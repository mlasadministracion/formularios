# Generador de Recibos — MY LITTLE ARTISTS SCHOOL S.A.S.

Script en Python que genera recibos de cobro en PDF para cada estudiante a
partir de una hoja de cálculo de Google Sheets, y opcionalmente los envía por
correo electrónico.

## Archivos del proyecto

| Archivo | Rol |
|---|---|
| `generar_recibos.py` | Script principal: lee el Excel, genera los PDF y envía los correos. |
| `recibo_template.html` | Plantilla del recibo (HTML + CSS) con placeholders `{campo}`. |
| `config.py` | Credenciales de Gmail y textos (asunto/cuerpo) del correo. |
| `logo.png` | Logo del colegio, usado en el encabezado del recibo. |
| `fonts/Play-Regular.ttf`, `fonts/Play-Bold.ttf` | Fuente "Play" (la misma del recibo original en Word), incluida localmente para que el PDF se vea igual en cualquier PC. |
| `recibos/<MES>/` | Carpeta de salida generada automáticamente con un PDF por estudiante. |

## Requisitos

```bash
pip3 install --break-system-packages openpyxl weasyprint requests
```

`weasyprint` necesita la librería de sistema `pango` para renderizar el PDF:

```bash
brew install pango
```

## Uso

```bash
python3 generar_recibos.py <NOMBRE_HOJA> [--enviar]
```

- `<NOMBRE_HOJA>`: nombre exacto de la pestaña del Excel a procesar (ej. `AGOSTO`).
- `--enviar` (opcional): si se incluye, además de generar los PDF, envía cada
  recibo por correo a las direcciones de la columna `correo` de esa fila.
  Si se omite, el script solo genera los archivos localmente.

Ejemplos:

```bash
# Solo generar los PDF, sin enviar correos
python3 generar_recibos.py AGOSTO

# Generar y enviar por correo
python3 generar_recibos.py AGOSTO --enviar
```

Los PDF quedan en `recibos/AGOSTO/<NOMBRE ESTUDIANTE>.pdf`.

## De dónde viene la información

El script **no usa un archivo local**: en cada ejecución descarga la versión
más reciente de la hoja de cálculo publicada en Google Sheets, mediante la URL
de exportación:

```
https://docs.google.com/spreadsheets/d/<ID>/export?format=xlsx
```

Esto requiere que el documento esté compartido como "cualquiera con el enlace
puede ver". La descarga se hace en memoria (no se guarda ningún `.xlsx` en
disco) y se abre con `openpyxl`.

## Estructura esperada de cada hoja del Excel

- **Fila 3**: etiquetas de cada columna (nombres en minúscula que se usan como
  claves para reemplazar los placeholders de la plantilla). Ejemplo de
  columnas usadas: `codigo`, `estudiante`, `curso`, `referencia de pago`,
  `cobro`, `recibo`, `pension`, `dias almuerzo`, `almuerzo`, `total`,
  `pension2`, `pension3`, `genera factura`, `correo`, `acudiente`.
- **A partir de la fila 4**: una fila por estudiante. La primera fila con un
  número en la columna A marca el inicio de los datos; el script se detiene al
  encontrar una fila sin número en A.
- **Columna `genera factura`** (`si`/`no`): controla si se genera o no el
  recibo de esa fila. Si la hoja no tiene esta columna (hojas antiguas), se
  asume `si` para todos los estudiantes.
- **Filas de totales**: se detectan y se ignoran automáticamente porque no
  tienen nombre de estudiante en la columna B.

Como las etiquetas se leen dinámicamente de la fila 3, hojas con distinto
orden de columnas (por ejemplo meses antiguos vs. recientes) funcionan sin
cambios en el código, siempre que las etiquetas de texto sean las mismas.

## Cómo se arma cada recibo (PDF)

1. Se lee la fila del estudiante y se arma un diccionario `{etiqueta: valor}`.
2. Cada valor se formatea con `formatear_valor`:
   - vacío o `"NA"` → `"-"`
   - números → separador de miles con punto (`.`)
   - si la etiqueta es un campo monetario (`pension`, `almuerzo`, `total`,
     `pago 2`, `pago 3`) se antepone `"$"`.
3. Se toma el HTML de `recibo_template.html` y se reemplaza cada
   `{etiqueta}` por su valor. También se reemplazan `{mes_upper}` y
   `{mes_title}` por el nombre de la hoja en mayúsculas y en formato título.
4. Si el almuerzo queda en `"-"` o `"$0"`, se elimina por completo la fila de
   "Almuerzo" de la tabla de conceptos (no se cobra almuerzo ese mes). El
   mecanismo exacto (`generar_recibos.py`, dentro de `generar_pdf`):

   ```python
   almuerzo_val = reemplazos.get("almuerzo", "-")
   if almuerzo_val in ("-", "$0"):
       html = re.sub(r'<tr>\s*<td>Almuerzo.*?</tr>', '', html, flags=re.DOTALL)
   ```

   - `reemplazos["almuerzo"]` es el valor ya formateado que se insertó en el
     HTML para ese estudiante (por ejemplo `"$150.000"`, `"-"` o `"$0"`).
   - Si ese valor es `"-"` (celda vacía o "NA") o `"$0"` (monto cero), se
     considera que el estudiante no paga almuerzo ese mes.
   - En ese caso se usa una expresión regular para borrar del HTML la fila
     `<tr>` que empieza en `<td>Almuerzo` hasta su `</tr>` de cierre
     (`re.DOTALL` permite que el patrón cruce saltos de línea, y el `.*?`
     no-codicioso evita que se coma filas siguientes).
   - Esto ocurre después de reemplazar los placeholders pero antes de
     convertir el HTML a PDF, así que el recibo final simplemente no incluye
     esa fila cuando no aplica.
5. El HTML resultante se convierte a PDF con `weasyprint`, usando como
   `base_url` la carpeta del proyecto para que las rutas relativas del
   template (`logo.png`, `fonts/...`) se resuelvan correctamente.
6. El PDF se guarda como `recibos/<MES>/<NOMBRE ESTUDIANTE>.pdf`.

## Plantilla (`recibo_template.html`)

- Página en tamaño carta horizontal (`landscape`).
- Fuente `Play` (regular y bold) cargada desde `fonts/`, igual a la del
  recibo original en Word.
- Encabezado dividido en 3 columnas: la primera con el logo centrado, las
  otras dos con el nombre y datos del colegio centrados.
- Tabla de datos del estudiante (cobro, recibo, mes, año, código, nombre,
  curso, referencia de pago, acudiente).
- Tabla de conceptos (Pensión, Almuerzo, Total a pagar) con encabezados y fila
  de total en rojo (`#ff1919`).
- Tabla de pagos parciales (fechas de pago del mes).
- Bloque de instrucciones de pago en azul (`#215e99`), centrado.

Los textos de fechas de pago están fijos en la plantilla (`Pago del 1 al 5
de...`, `Pago del 6 al 10 de...`, `Pago a partir del 11 de...`) y los días de
almuerzo se toman dinámicamente de la columna `dias almuerzo` del Excel
(`{dias almuerzo}`).

## Envío de correos (`--enviar`)

- Usa Gmail vía SMTP SSL (`smtp.gmail.com:465`) con las credenciales de
  `config.py`.
- `EMAIL_REMITENTE` debe ser el correo puro (sin nombre) porque es el que se
  usa para autenticar (`smtp.login`); `EMAIL_NOMBRE` es solo el nombre que se
  muestra en el campo "De".
- `EMAIL_PASSWORD` es una **contraseña de aplicación** de Gmail (no la
  contraseña normal de la cuenta), necesaria porque Gmail bloquea el login
  SMTP con la contraseña real cuando hay verificación en dos pasos.
- El asunto sale de `EMAIL_ASUNTO.format(mes=..., estudiante=...)`, siempre en
  mayúsculas.
- El cuerpo sale de `EMAIL_CUERPO.format(mes=...)`.
- Los destinatarios de cada estudiante se toman de la columna `correo`,
  separando por comas y validando que cada dirección contenga `@`.
- Si un estudiante no tiene correo registrado, simplemente se omite el envío
  para esa fila (el PDF sí se genera).
- Si `--enviar` no se pasa, no se abre ninguna conexión SMTP: solo se generan
  los PDF.

## Seguridad de las credenciales

`config.py` contiene la contraseña de aplicación de Gmail en texto plano. Si
este proyecto se sube a un repositorio (GitHub, etc.), ese archivo **no debe
subirse** — hay que agregarlo a `.gitignore` y cargar las credenciales desde
variables de entorno o un secreto externo en su lugar.

## Resumen del flujo completo

```
Google Sheets (hoja del mes)
        │  descarga en memoria (requests + openpyxl)
        ▼
leer_hoja()  →  lista de estudiantes a facturar
        │
        ▼
generar_pdf()  →  recibo_template.html + datos → PDF (weasyprint)
        │
        ▼ (si --enviar)
enviar_email()  →  PDF adjunto por Gmail SMTP a la columna "correo"
```
