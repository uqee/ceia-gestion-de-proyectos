---
description: Extrae los comentarios (anotaciones PDF) de uno o varios PDFs de la carpeta context y los vuelca en archivos markdown con el sufijo "Comments (extracted)".
agent: build
---

Sos un agente técnico. Tu única tarea es extraer los comentarios embebidos como anotaciones PDF en uno o varios archivos de la carpeta `./context` y escribir el resultado en archivos `.md` junto a cada PDF de origen, usando el patrón de nombre:

```
<nombre-original-sin-extension> - Comments (extracted).md
```

Por ejemplo: `2026-07-03 - Comments - Planificación v1.pdf` → `2026-07-03 - Comments (extracted) - Planificación v1.md`.

## Argumentos

- `$ARGUMENTS` (opcional): ruta a un PDF específico. Si está vacía, se procesan todos los PDFs dentro de `./context` cuyo nombre contenga `Comments` y termine en `.pdf` (excluyendo los `.md` ya generados).
- Si el usuario pasa múltiples rutas separadas por espacios, procesá cada una.

## Procedimiento

1. Confirmá que `python3` está disponible y que la dependencia `fitz` (PyMuPDF) está instalada (`python3 -c "import fitz"`). Si no lo está, **abortá** con un mensaje claro y no intentes instalarla salvo que el usuario lo pida.
2. Escribí el siguiente script Python en un archivo temporal (usá `/var/folders/2j/cdg6gp1x375gs3v6hgh8gtwr0000gn/T/opencode/extract_comments.py` o equivalente) y ejecútalo con `python3`. El script imprime la lista de archivos `.md` creados:

```python
#!/usr/bin/env python3
"""Extrae anotaciones de comentarios de PDFs y las vuelca a markdown."""
from __future__ import annotations

import datetime as _dt
import re
import sys
from pathlib import Path

import fitz


def _parse_pdf_date(value: str) -> str:
    if not value:
        return ""
    m = re.match(r"D:(\d{4})(\d{2})(\d{2})", value)
    if m:
        return f"{m.group(1)}-{m.group(2)}-{m.group(3)}"
    return value


def _build_xref_to_page(doc: fitz.Document) -> dict[int, int]:
    mapping: dict[int, int] = {}
    for p in range(doc.page_count):
        page = doc[p]
        try:
            info = doc.xref_object(page.xref)
        except Exception:
            continue
        for refs_str in re.findall(r"/Annots\s*\[([^\]]+)\]", info):
            for r in re.findall(r"(\d+)\s+\d+\s+R", refs_str):
                mapping[int(r)] = p + 1
    return mapping


ACUTE_MAP = {
    "a": "á", "e": "é", "i": "í", "o": "ó", "u": "ú",
    "A": "Á", "E": "É", "I": "Í", "O": "Ó", "U": "Ú",
    "\u0131": "í",  # dotless i -> í
}
TILDE_MAP = {
    "a": "ã", "n": "ñ", "o": "õ", "A": "Ã", "N": "Ñ", "O": "Õ",
}
DIAERESIS_MAP = {
    "a": "ä", "e": "ë", "i": "ï", "o": "ö", "u": "ü",
    "A": "Ä", "E": "Ë", "I": "Ï", "O": "Ö", "U": "Ü",
}


def _recombine_accents(text: str) -> str:
    """Re-join T1-font decomposition artifacts left by PyMuPDF.

    Examples:
        "Verificaci´on" -> "Verificación"
        "t´ecnica"      -> "técnica"
        "l´ıder"        -> "líder"
        "´Epica"        -> "Épica"
        "Dise˜no"       -> "Diseño"
    """
    if not text:
        return text
    text = re.sub(
        r"([A-Za-z\u00C0-\u017F])\u00b4([A-Za-z\u00C0-\u017F\u0131])",
        lambda m: m.group(1) + ACUTE_MAP.get(m.group(2), m.group(2)),
        text,
    )
    text = re.sub(
        r"(^|\W)\u00b4([A-Za-z\u00C0-\u017F])",
        lambda m: m.group(1) + ACUTE_MAP.get(m.group(2), m.group(2)),
        text,
    )
    text = re.sub(
        r"([A-Za-z\u00C0-\u017F])\u02dc([A-Za-z\u00C0-\u017F])",
        lambda m: m.group(1) + TILDE_MAP.get(m.group(2), m.group(2)),
        text,
    )
    text = re.sub(
        r"(^|\W)\u02dc([A-Za-z\u00C0-\u017F])",
        lambda m: m.group(1) + TILDE_MAP.get(m.group(2), m.group(2)),
        text,
    )
    text = re.sub(
        r"([A-Za-z\u00C0-\u017F])\u00a8([A-Za-z\u00C0-\u017F])",
        lambda m: m.group(1) + DIAERESIS_MAP.get(m.group(2), m.group(2)),
        text,
    )
    return text


def _extract_highlighted_text(page: fitz.Page, rect) -> str:
    if rect is None:
        return ""
    try:
        words = page.get_text("words", clip=rect) or []
        words.sort(key=lambda w: (round(w[1], 1), w[0]))
        lines: list[str] = []
        current_y: float | None = None
        current_line: list[str] = []
        for w in words:
            _x0, y0, _x1, _y1, text = w[0], w[1], w[2], w[3], w[4]
            if current_y is None or abs(y0 - current_y) > 2:
                if current_line:
                    lines.append(" ".join(current_line))
                current_line = [text]
                current_y = y0
            else:
                current_line.append(text)
        if current_line:
            lines.append(" ".join(current_line))
        joined = " ".join(lines).strip()
        return _recombine_accents(joined)
    except Exception:
        return ""


def _annot_rect(annot):
    try:
        return annot.rect
    except Exception:
        return None


def _gather_annotations(pdf_path: Path) -> list[dict]:
    doc = fitz.open(pdf_path)
    out: list[dict] = []
    for p in range(doc.page_count):
        page = doc[p]
        try:
            annots = list(page.annots() or [])
        except Exception:
            annots = []
        for a in annots:
            try:
                info = a.info or {}
            except Exception:
                info = {}
            subtype = ""
            try:
                t = a.type
                if isinstance(t, tuple):
                    subtype = str(t[1]) if len(t) > 1 else str(t[0])
                else:
                    subtype = str(t)
            except Exception:
                subtype = ""
            author = info.get("title", "")
            content = (info.get("content", "") or "").replace("\ufeff", "").strip()
            creation = info.get("creationDate", "")
            creation_date = _parse_pdf_date(creation)
            irt_xref = None
            try:
                irt_xref = a.irt_xref
            except Exception:
                pass
            rect = _annot_rect(a)
            highlighted = ""
            if subtype == "Highlight":
                highlighted = _extract_highlighted_text(page, rect)
            out.append(
                {
                    "xref": a.xref,
                    "subtype": subtype,
                    "author": author,
                    "content": content,
                    "date": creation_date,
                    "page": p + 1,
                    "rect": rect,
                    "irt": irt_xref,
                    "highlighted": highlighted,
                }
            )
    doc.close()
    return out


def _group_comments(annots: list[dict]) -> tuple[list[dict], list[dict]]:
    parents: list[dict] = []
    replies: list[dict] = []
    for a in annots:
        if a["subtype"] == "Highlight":
            parents.append(a)
        elif a["subtype"] == "Text" and a["irt"] is None and a["content"]:
            parents.append(a)
        else:
            replies.append(a)
    return parents, replies


def _render_markdown(pdf_path: Path, parents: list[dict], replies: list[dict]) -> str:
    today = _dt.date.today().isoformat()
    total_replies = len(replies)
    by_parent: dict[int, list[dict]] = {}
    for r in replies:
        if r["irt"] is not None:
            by_parent.setdefault(r["irt"], []).append(r)

    def _fmt_block(parent: dict, idx: int) -> str:
        lines: list[str] = [f"## Comentario {idx} - p. {parent['page']}", ""]
        lines.append(f"- **Autor**: {parent['author'] or '(sin autor)'}")
        lines.append(f"- **Fecha**: {parent['date'] or '(sin fecha)'}")
        if parent["subtype"] == "Highlight" and parent["highlighted"]:
            snippet = parent["highlighted"].replace("\n", " ")
            if len(snippet) > 300:
                snippet = snippet[:297] + "..."
            lines.append(f'- **Texto resaltado en el plan**: "{snippet}"')
        body = parent["content"]
        if body:
            quoted = "\n".join([f"  > {ln}" if ln else "  >" for ln in body.split("\n")])
            lines.append("- **Comentario**:")
            lines.append("")
            lines.append(quoted)
        elif parent["subtype"] == "Highlight":
            lines.append("- **Comentario**: _(solo texto resaltado, sin observacion)_")
        else:
            lines.append("- **Comentario**: _(sin texto)_")
        thread = by_parent.get(parent["xref"], [])
        if thread:
            lines.append("")
            lines.append("- **Replicas**:")
            for r in thread:
                author = r["author"] or "(sin autor)"
                date = r["date"] or "(sin fecha)"
                if r["content"]:
                    quoted = "\n".join([f"    > {ln}" if ln else "    >" for ln in r["content"].split("\n")])
                    lines.append(f"  - **{author}** - {date}:")
                    lines.append("")
                    lines.append(quoted)
                else:
                    lines.append(f"  - :white_check_mark: **{author}** - {date} (resuelto)")
        lines.append("")
        return "\n".join(lines)

    parents.sort(key=lambda a: (a["page"] or 0, a["xref"]))
    blocks = [_fmt_block(p, i + 1) for i, p in enumerate(parents)]

    try:
        rel = pdf_path.relative_to(Path.cwd())
    except ValueError:
        rel = pdf_path
    header = [
        f"# Comentarios extraidos: {pdf_path.name}",
        "",
        f"- **PDF de origen**: `{rel}`",
        f"- **Total**: {len(parents)} comentarios - {total_replies} replicas",
        f"- **Extraido el**: {today}",
        "",
        "---",
        "",
    ]
    return "\n".join(header) + "\n".join(blocks)


def _output_path_for(pdf_path: Path) -> Path:
    stem = pdf_path.stem
    suffix = " - Comments (extracted)"
    if " - Comments" in stem:
        new_stem = stem.replace(" - Comments", suffix, 1)
    else:
        new_stem = f"{stem}{suffix}"
    return pdf_path.with_name(new_stem + ".md")


def process(pdf_path: Path) -> Path | None:
    annots = _gather_annotations(pdf_path)
    if not annots:
        return None
    parents, replies = _group_comments(annots)
    md = _render_markdown(pdf_path, parents, replies)
    out = _output_path_for(pdf_path)
    out.write_text(md, encoding="utf-8")
    return out


def main() -> int:
    if len(sys.argv) > 1:
        inputs = [Path(p).expanduser().resolve() for p in sys.argv[1:]]
    else:
        context_dir = Path("./context").resolve()
        inputs = sorted(
            p
            for p in context_dir.glob("*.pdf")
            if "Comments" in p.name and "extracted" not in p.name
        )

    if not inputs:
        print("No se encontraron PDFs de comentarios para procesar.", file=sys.stderr)
        return 1

    created: list[Path] = []
    for pdf in inputs:
        if not pdf.exists():
            print(f"  X No existe: {pdf}", file=sys.stderr)
            continue
        try:
            out = process(pdf)
        except Exception as exc:
            print(f"  X Error procesando {pdf.name}: {exc}", file=sys.stderr)
            continue
        if out:
            created.append(out)
            print(f"  + {pdf.name} -> {out.name}")
    print(f"\nArchivos generados: {len(created)}")
    for c in created:
        print(f"  - {c}")
    return 0


if __name__ == "__main__":
    raise SystemExit(main())
```

3. Verificá que los archivos se crearon correctamente. Leé el primer y el último archivo generado para confirmar que:
   - El título es correcto.
   - Los comentarios decodifican bien (tildes, mayúsculas, saltos de línea, comillas tipográficas).
   - El campo "Texto resaltado en el plan" muestra el fragmento correcto.
   - Las réplicas se listan debajo del comentario padre.
4. Reportá al usuario la lista de archivos `.md` creados con la ruta absoluta de cada uno.

## Reglas de formato del markdown de salida

- Codificación UTF-8.
- Saltos de línea suaves: un comentario por bloque, separados por una línea en blanco.
- Blockquote con prefijo `> ` para el cuerpo del comentario y `    > ` para réplicas con texto.
- Réplicas vacías (checkmarks) en una sola línea: `- :white_check_mark: **Autor** — fecha (resuelto)`.
- Si el comentario es solo un resaltado sin texto (`/Contents` vacío), mostrar `_solo texto resaltado, sin observación_`.
- El campo "Texto resaltado en el plan" se trunca a 300 caracteres con elipsis si excede.
- Reconstruir acentos rotos por la codificación T1: `Verificaci´on` → `Verificación`, `Dise˜no` → `Diseño`, `l´ıder` → `líder`.

## No hacer

- No modifiques `charter.tex`, `AGENTS.md` ni ningún otro archivo del proyecto.
- No instales dependencias nuevas; si `fitz` no está disponible, abortá.
- No borres los PDFs originales.
- No hagas commit ni push.
