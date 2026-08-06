# Biometric Identity Verification — Project Summary
*Final project (CEIA, FIUBA) — for review by Ksenija Blažević, Director*

## What we're building

A biometric verification module for Leroy Merlin that uses facial recognition to check, at the start of each shift, that the worker present matches the person on file, and, at hiring, that the candidate matches the photo on their ID document. Out of scope: document authenticity checks, production-grade liveness, and any deployment against real company data — this is an academic prototype validated entirely on public datasets.

## Technical approach

- Pipeline: face detection (MTCNN / RetinaFace) → landmark alignment → embedding generation (ArcFace / AdaFace) → cosine-distance comparison → thresholded accept/reject decision → liveness check (baseline classifier).
- Core experimental question: does domain-adaptation fine-tuning of a pretrained model (positive, neutral, or negative effect) improve verification performance under degraded, real-world-like conditions (blur, compression, low resolution, cross-age), and what's the compute-cost trade-off?
- Fine-tuning subset: 5,000–10,000 identities / 100,000–300,000 images sampled from Glint360K (or WebFace4M), augmented with synthetic degradations and age-diverse sampling.
- Delivery: a reproducible CLI harness (metrics + figures) and an interactive Gradio/Streamlit demo. No production deployment.

## Models & datasets

- **Models:** ArcFace and AdaFace (main, one fine-tuned — ArcFace required, AdaFace optional). TransFace (ICCV 2023) as an optional third model if time allows.
- **Fine-tuning source:** Glint360K (primary) or WebFace4M (alternative).
- **Evaluation benchmarks:** LFW (baseline, expected saturated ~99%+, used mainly as a saturation check), AgeDB (cross-age — primary success benchmark), RFW (demographic-bias reporting), plus synthetically degraded variants of LFW/AgeDB. Liveness evaluated on OULU-NPU or CASIA-FASD.
- **Excluded:** MS-Celeb-1M and CASIA-WebFace (retracted / ambiguous license).

## Evaluation & success criteria

- **Primary metric:** Equal Error Rate (EER) on AgeDB.
- **Secondary metrics:** FAR, FRR, AUC, TAR at fixed FAR (e.g. FAR=10⁻³), ROC curves, mean inference time & memory.
- **Statistical rigor:** fine-tuning effect reported as absolute % EER difference with 95% CI via bootstrap (N=1000); non-significant results will be documented and analyzed, not hidden.
- **Reference hardware:** 2019 16" MacBook Pro for timing figures; fine-tuning/eval runs on GPU (Colab/Kaggle) where needed.

## Main deliverables (MVP)

- Pretrained ArcFace + AdaFace verification pipeline with configurable threshold.
- ArcFace fine-tuned on the domain-adapted subset, evaluated against the pretrained baseline on all benchmarks.
- Liveness baseline (MiniFASNet or FeatherNet) integrated into the pipeline, reported with its known limitations.
- Reproducible CLI harness + interactive demo + full documentation.
- **Optional (only if schedule allows):** TransFace evaluation (8h), AdaFace fine-tuning (12h).

## Backlog & time estimate (600 h total, ~8 months, 12 sprints)

| Epic | Covers | Est. |
|---|---|---|
| Epic 1 — Document–face registration & verification | HU1–HU4: face capture, ID-photo extraction, live-vs-document comparison, invalid-capture handling | ~85 h |
| Epic 2 — Shift verification | HU5–HU6: face verification at shift start vs stored reference; retry/rejection handling | ~50 h |
| Epic 3 — Experimental evaluation & fine-tuning (core) | HU7–HU14: threshold config, baseline eval (ArcFace/AdaFace), optional TransFace, fine-tuning subset prep, ArcFace fine-tuning, optional AdaFace fine-tuning, re-evaluation & comparison, liveness baseline | ~155 h (+33 h optional) |
| Epic 4 — Harness, demo & delivery | HU15–HU16: CLI evaluation harness, interactive Gradio/Streamlit demo, documentation | ~44 h |

**Breakdown:** 388 h technical + 212 h non-technical (planning, reports, defense prep) = 600 h. Experimental core (fine-tuning + evaluation) concentrated in sprints 5–7 (120 h). Public defense projected for April 23, 2027.

## Key acceptance criteria (representative)

- Capture/verification pipeline runs end-to-end in <5s on reference hardware; single-face detection confidence >80%.
- Baseline evaluation (HU8): ArcFace & AdaFace scored on LFW/AgeDB/RFW + degraded variants, with EER/FAR/FRR/AUC/TAR/ROC, fully reproducible (documented seeds, splits, model versions).
- Fine-tuning (HU11): documented hyperparameters, preserved checkpoints, effect on EER reported with 95% CI; non-significant results explicitly analyzed rather than omitted.
- Liveness (HU14): ACER/HTER reported on a public spoofing dataset, compared to literature baselines, limitations documented.
- Harness (HU15) and demo (HU16): one-command reproducibility, documented CLI arguments, exact commands to regenerate all figures in the report.

## Governance & risk (brief)

- No real worker data used anywhere — public/synthetic datasets only; consent/DPIA/access-control requirements documented as future-deployment work, not built now.
- Top risks: insufficient free-tier GPU compute (mitigated via smaller subset + layer freezing + Colab Pro budget), burnout/schedule slip (mitigated via prioritizing the experimental core), and LFW saturation blocking model differentiation (addressed by shifting differentiation to AgeDB/RFW).
