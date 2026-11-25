- **Historia:** HU-1.2 · Registrar un riego. **Deuda:** D-1 (`docs/deuda-tecnica.md`).
- **Objetivo:** el formato de un identificador de MongoDB se define en un solo sitio de `src/`: `validarIdPlanta` usa `esIdValido` en lugar de su propia expresión regular.

### Reglas

- **DEBE** hacer que `validarIdPlanta`, en `src/dominio/validaciones.js`, use `esIdValido` para decidir si el identificador tiene un formato válido.
- **DEBE** dejar una sola expresión regular para los identificadores en `src/`: la que usa `esIdValido`. `ID_PLANTA` desaparece.
- **DEBE** mantener los mensajes tal como están: «La planta es obligatoria» para un valor que falta, `null`, vacío o con solo espacios, y «La planta no tiene un identificador válido» para cualquier otro valor no válido.
- **DEBE** añadir a `test/dominio/validaciones.test.js` un test nuevo que, para una lista de valores, compruebe que `validarRiego` devuelve un detalle en `planta` exactamente cuando `esIdValido` devuelve falso.
- **NO DEBE** cambiar los mensajes de error ni las respuestas de la API.
- **NO DEBE** modificar ni borrar ningún test existente: todos tienen que pasar tal como están.
- **NO DEBE** tocar `src/routes/` ni `client/`.

### Criterios de aceptación

- **CA-08.1:** DADO el código de `src/`, CUANDO se buscan expresiones regulares de identificador, ENTONCES solo hay una, la que usa `esIdValido`.
- **CA-08.2:** DADO `planta: "665f1c2a9b1e8a0012345678"` y 500 ml, CUANDO se valida el riego, ENTONCES no hay ningún detalle en `planta`.
- **CA-08.3:** DADO cada uno de estos valores de `planta`: `undefined`, `null`, `""`, `"   "`, `"123"`, `"665f1c2a9b1e8a001234567z"`, `"665f1c2a9b1e8a00123456789"`, `" 665f1c2a9b1e8a0012345678"`, `123`, `{ $gt: "" }`, `"665F1C2A9B1E8A0012345678"` y `"665f1c2a9b1e8a0012345678"`, CUANDO se valida el riego, ENTONCES hay un detalle en `planta` si y solo si `esIdValido` devuelve falso para ese valor.
- **CA-08.4:** DADO `planta: "123"`, CUANDO se valida el riego, ENTONCES el mensaje es «La planta no tiene un identificador válido»; y DADO `planta: "   "`, ENTONCES el mensaje es «La planta es obligatoria».
- **CA-08.5:** DADO la batería de tests actual, CUANDO se ejecuta `npm test`, ENTONCES todos los tests que ya existían pasan sin haberlos modificado.

### Verificación

| Criterio | Cómo se comprueba |
| --- | --- |
| CA-08.1 | `grep -rnE "\[0-9a-f\]\{24\}" src` devuelve una sola línea |
| CA-08.2 a CA-08.4 | Tests en `test/dominio/validaciones.test.js` |
| CA-08.5 | `npm test` pasa, y `git diff --numstat main -- test/` muestra 0 líneas borradas |
| Alcance | Revisión del diff: no toca `src/routes/` ni `client/` |

### Archivos

`src/dominio/validaciones.js` · `test/dominio/validaciones.test.js`

### Pendientes

- **Tests que no pudieron fallar primero:** CA-08.2 a CA-08.4 pasan antes y después del cambio, porque es un refactor: el comportamiento ya era equivalente. Lo único que falló antes del cambio fue CA-08.1 (`grep` devolvía 2 líneas: `ID_PLANTA` e `ID_VALIDO`).
- **Registro de deuda:** al fusionar el PR, marcar D-1 como pagado en `docs/deuda-tecnica.md`. Ese archivo no está entre los de esta spec.
