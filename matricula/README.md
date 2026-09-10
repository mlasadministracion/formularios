# Hoja de Matrícula — My Little Artists School (MLAS)

Documentación técnica y operativa para la **Hoja de Matrícula Institucional 2026** (`matricula.html`).

---

## 📂 Archivos de la Carpeta

* **`matricula.html`**: Formulario interactivo diseñado con precisión para **1 página estricta** (A4 / Carta) para visualización en navegador, diligenciamiento digital e impresión directa o guardado en PDF.
* **`README.md`**: Guía de uso, atajos, modo de pruebas y especificaciones técnicas.

---

## 🛠️ Modo de Pruebas (Botón "Rellenar datos de prueba")

Para garantizar una presentación pulcra y evitar confusiones en los usuarios finales (padres de familia), el botón **`📝 Rellenar datos de prueba`** se encuentra **oculto por defecto**.

Para habilitarlo y realizar pruebas o demostraciones completas, dispones de **3 métodos de acceso**:

### 1. Parámetro en la URL (Recomendado)
Abre el formulario añadiendo cualquiera de los siguientes parámetros al final de la URL:
* `?test=1`
* `?demo=1`
* `?modo=prueba`

**Ejemplo local:**
```text
file:///d:/Proyectos/MLAS/formularios/matricula/matricula.html?test=1
```

*Si abres el archivo localmente como `file:///` y el navegador no procesa query params, puedes usar el identificador hash:*
```text
file:///d:/Proyectos/MLAS/formularios/matricula/matricula.html#test
```

### 2. Atajo de Teclado Secreto
Con el formulario abierto en cualquier parte de la ventana, presiona:
```text
Ctrl + Shift + D    (D de Demo)
```
* Esto alternará instantáneamente la visibilidad del botón entre visible y oculto.

### 3. Desde la Consola del Navegador (`F12`)
Abre las herramientas de desarrollo con `F12` o `Ctrl + Shift + I` y en la pestaña **Console** ejecuta:
* `activarModoPrueba()` — Muestra el botón de prueba y guarda la preferencia.
* `desactivarModoPrueba()` — Oculta el botón.
* `fillDemoData()` (o `rellenarDatosPrueba()`) — Diligencia todos los campos con datos de ejemplo y calcula la edad automáticamente.

> **Comportamiento al recargar:** El botón **siempre se oculta automáticamente al recargar la página** a menos que la URL incluya explícitamente los parámetros de prueba (`?test=1`, `?demo=1`, `#test`, etc.). No se almacena en memoria persistente (`localStorage`) para garantizar que ningún usuario final pueda verlo al ingresar a la página limpia.

---

## ✨ Estructura del Formulario (1 Página Estricta)

1. **Cabecera Institucional y Folio:**
   * Logo del colegio MLAS.
   * Título principal institucional.
   * Recuadro de control administrativo: Folio y Matrícula Nº.

2. **Datos del Estudiante:**
   * Apellidos y Nombres completos.
   * Documento de Identidad con lista desplegable de tipos (RC, TI, CC, CE, PA, PPT, PEP, VISA, NUIP, NES).
   * EPS y Grupo Sanguíneo / RH con sugerencias automáticas (`datalist`).
   * Lugar de nacimiento, Fecha de nacimiento (`DD/MM/AAAA`) con selector de calendario `📅`.
   * **Cálculo automático de la edad:** Al seleccionar o ingresar la fecha de nacimiento, se computa y asigna de inmediato en años cumplidos.
   * Dirección de residencia, Ciudad, Barrio y Teléfono de contacto.
   * Género / Sexo con menú desplegable.

3. **Datos Familiares (Tabla Comparativa):**
   * Padre, Madre y Acudiente en columnas dedicadas.
   * Nombres completos, tipo y número de documento, lugar de expedición, fecha de nacimiento (`DD/MM/AAAA`) con calendario, correo electrónico y celular.
   * Fila para **Responsable Económico** y **Parentesco del Acudiente**.

4. **Escolaridad:**
   * Grado al que se matricula (con menú de sugerencias: Párvulos a Quinto).
   * Institución o colegio de procedencia.
   * Último grado cursado y aprobado.
   * Año lectivo.

5. **Cláusula de Aceptación y 4 Bloques de Firmas:**
   * Firma de la Madre (dibujo en canvas o carga de archivo).
   * Firma del Padre (dibujo en canvas o carga de archivo).
   * Firma de la Directora (nombre precargado: Gloria Amparo Gonzalez Castiblanco).
   * Firma de la Coordinadora (nombre precargado: Katherin Gabriela Acevedo González).

---

## 🎨 Características Interactivas y de Usabilidad

* **Ayudas Contextuales al 100%:** Cada campo cuenta con su atributo `title` explicativo (tooltip al posar el cursor) y `placeholder` descriptivo.
* **Desplegables con Estilo Ayuda (`.select-as-help`):**
  * Sin seleccionar: texto atenuado en cursiva (`#7a8a9c`) que se percibe como ayuda visual.
  * Seleccionado: azul institucional (`#0f2b5f`) en negrita con flecha SVG distintiva.
* **Control de Zoom en Pantalla (`➖ 115% ➕`):** Barra superior flotante con botones para ampliar o reducir la hoja según la resolución de la pantalla, sin afectar la escala de impresión.
* **Borrador Local:**
  * `💾 Guardar borrador`: Guarda todo el formulario y las 4 firmas en `localStorage`.
  * `📂 Cargar borrador`: Recupera el último borrador guardado en el equipo.
* **Firmas Híbridas:**
  * Dibujo manual alzado con ratón, pantalla táctil o stylus.
  * Botón `📁 Subir firma` para cargar imágenes PNG o JPG con ajuste automático centrado.
  * Botón `✕ Limpiar` para reiniciar el trazo.

---

## 🖨️ Recomendaciones de Impresión y PDF

* **Destino:** Guardar como PDF o impresora física en tamaño **A4** o **Carta (Letter)** con orientación **Vertical (Portrait)**.
* **Escala:** 100% (o predeterminada).
* **Opciones:** Activar la casilla **"Gráficos de fondo"** (*Background graphics*) para que las barras de sección en color azul y naranja se impriman nítidas.
* **Impresión en Blanco:** Si se imprime el formulario vacío para diligenciamiento a mano, los textos de ayuda (`placeholder`) se ocultan automáticamente, dejando líneas limpias y despejadas.
