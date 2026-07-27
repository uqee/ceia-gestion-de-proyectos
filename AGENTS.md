# Reglas de estilo — charter.tex (Trabajo Final, Denis Zhbankov)

Reglas de redacción y estilo para `charter.tex`. Aplican a todo el documento salvo indicación contraria.

## Español

### Palabras en inglés dentro de texto en español

Toda palabra o expresión en inglés que aparezca dentro de texto en español debe:

- Escribirse en **minúsculas**, salvo cuando se trate de un **nombre propio** (persona, marca, acrónimo consolidado, nombre propio de métrica/modelo, etc.) o cuando inicien una oración, un elemento de lista, u otra posición que exija mayúscula inicial por la ortografía del español.
- Envolverse con la macro `\textit{...}` para cursivizarla.

Ejemplos:
- Correcto: `\textit{product backlog}`, `\textit{story points}`, `\textit{face verification}`, `\textit{mock}`, `\textit{product owner}`, `\textit{pipeline}`, `\textit{embedding}`, `\textit{deep learning}`, `\textit{outsourcing}`, `\textit{dataset}`.
- Correcto (nombre propio / acrónimo conservan mayúsculas): `FaceNet`, `ArcFace`, `MTCNN`, `RetinaFace`, `LFW`, `VGGFace2`, `CASIA-WebFace`, `API`, `REST`, `ROC`, `FAR`, `FRR`, `EER`, `GDPR`, `HIPAA`.
- Correcto (nombre propio de métrica, en cursiva): `\textit{False Acceptance Rate}`, `\textit{Equal Error Rate}`.
- Incorrecto: `Product Owner`, `Pipeline`, `Mock`, `Outsourcing` (mayúsculas injustificadas y/o sin cursiva).

### Gerundio de posterioridad

Evitar el gerundio que expresa una acción posterior a la principal (p. ej. «\textit{...capturando su rostro ... para almacenar ...}» si la captura y el almacenamiento no son simultáneos). Reescribir con oración coordinada o subordinada final.

### Mayúscula seguida de dos puntos

No se usa mayúscula después de dos puntos a menos que lo que siga sea un nombre propio (p. ej. evitar «\textit{El proyecto incluye: Implementación ...}»).

### Mayúsculas de expresiones del posgrado

Expresiones como «trabajo final», «plan de proyecto», «acta de constitución», etc. **no** son nombres propios. Dentro de una oración van en minúsculas, salvo cuando inicien la oración, un título de sección o una posición ortográfica que exija mayúscula inicial.

Ejemplos:
- Correcto: `la defensa del trabajo final`, `su trabajo final de la Carrera...`, `Director del trabajo final`.
- Incorrecto: `la defensa del Trabajo Final`, `su Trabajo Final de la...` (mayúsculas injustificadas a mitad de frase).

<!--
Plantilla para nuevas reglas:

### <Nombre corto de la regla>

<Descripción concisa.>

Ejemplos:
- Correcto: ...
- Incorrecto: ...
-->