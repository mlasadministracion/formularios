# Formularios Institucionales — My Little Artists School (MLAS)

Documentación técnica y de usuario para los formularios web interactivos e imprimibles del colegio.

---

## 📂 Estructura de Formularios

1. **Formulario de Admisiones / Inscripción:**
   * Archivo: `formularios/inscripcion/formulario.html`
   * Extensión: **3 páginas A4 exactas** para impresión o guardado en PDF.
   * Contenido: Nivel y Grado, Datos del Niño o la Niña (9 renglones dedicados), Información de Padres y Acudiente, Contactos de Emergencia, Antecedentes de Embarazo y Parto, Hospitalarios, Medicamentos y Alergias, Control de Crecimiento, Seguimiento Odontológico, Carné de Vacunas, Requisitos de Admisión y 3 bloques de firmas.

2. **Hoja de Matrícula:**
   * Archivo: `formularios/matricula/matricula.html`
   * Extensión: **1 página estricta** (A4 / Carta).
   * Contenido: Folio, Matrícula Nº, Datos del Estudiante, Datos de Padres y Acudiente, Escolaridad y 4 bloques de firmas (Madre, Padre, Directora y Coordinadora).

---

## 🛠️ Modo de Pruebas (Botón "Rellenar Datos de Prueba")

Para proteger la integridad de los formularios cuando son diligenciados por usuarios finales (padres de familia), el botón **`📝 Rellenar datos de prueba`** se encuentra **oculto por defecto**.

Para habilitarlo y realizar pruebas o demostraciones, dispones de **3 métodos**:

### 1. Parámetro en la URL (Recomendado)
Abre el formulario agregando cualquiera de los siguientes parámetros al final de la URL:
* `?test=1`
* `?demo=1`
* `?modo=prueba`

**Ejemplos:**
```text
file:///d:/Proyectos/MLAS/formularios/inscripcion/formulario.html?test=1
file:///d:/Proyectos/MLAS/formularios/matricula/matricula.html?demo=1
```

*Si abres el archivo localmente como `file:///` y el navegador descarta los parámetros de consulta, puedes usar el identificador hash:*
```text
file:///d:/Proyectos/MLAS/formularios/inscripcion/formulario.html#test
file:///d:/Proyectos/MLAS/formularios/matricula/matricula.html#demo
```

### 2. Atajo de Teclado Secreto
En cualquier momento con el formulario abierto en pantalla, pulsa:
```text
Ctrl + Shift + D    (D de Demo)
```
* Esto alternará de forma inmediata la visibilidad del botón entre visible y oculto.

### 3. Desde la Consola del Navegador (`F12`)
Abre las herramientas de desarrollador (`F12` o `Ctrl + Shift + I`) y en la pestaña **Console** ejecuta:
* `activarModoPrueba()` — Muestra el botón.
* `desactivarModoPrueba()` — Oculta el botón.

> **Comportamiento al recargar:** El botón **siempre se oculta automáticamente al recargar la página** a menos que la URL incluya explícitamente los parámetros de prueba (`?test=1`, `?demo=1`, `#test`, etc.). No se almacena en memoria persistente (`localStorage`) para garantizar que ningún usuario final pueda verlo al ingresar a la página limpia.

---

## ✨ Funcionalidades Destacadas

* **Control de Zoom en Pantalla (`➖ 115% ➕`):** Barra superior flotante que permite ampliar o reducir la visualización en pantalla sin alterar la escala al imprimir.
* **Formato de Fechas Unificado:** Estricto `DD/MM/AAAA` con máscara de auto-completado de barras (`/`) y calendario emergente `📅`.
* **Firmas Digitales Híbridas:**
  * Dibujo a mano alzada o con lápiz óptico sobre el recuadro.
  * Botón **`📁 Subir firma`** para cargar archivos de imagen (PNG, JPG) con ajuste proporcional automático.
  * Botón **`✕ Borrar`** para reiniciar la firma.
* **Borrador Local:** Botones para `💾 Guardar borrador` y `📂 Cargar borrador` que almacenan campos, fotos y firmas en `localStorage`.
* **Listas Desplegables con Estilo Ayuda (`.select-as-help`):**
  * Estado vacío: tipografía gris atenuada en cursiva con línea inferior punteada (*dashed*).
  * Estado seleccionado: azul institucional en negrita con línea inferior sólida.
* **Impresión Limpia:** Al imprimir en blanco, los textos de ayuda (`placeholder`) se tornan transparentes para garantizar líneas de diligenciamiento manual totalmente despejadas.
