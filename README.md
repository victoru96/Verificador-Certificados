# 🎟️ Verificador de Certificados

Sistema de verificación para eventos y conferencias. Un solo QR fijo lleva al verificador, y el asistente ingresa su código manualmente. Los datos viven en Google Sheets — cualquier persona con acceso de edición puede agregar, modificar o revocar certificados sin tocar el código.

---

## 📁 Archivos

| Archivo | Descripción |
|---|---|
| `index.html` | Verificador principal — consulta Google Sheets en tiempo real |
| `plan-b.html` | Plan B — funciona sin Google Sheets, lee `certificates.json` |
| `certificates.json` | Base de datos local para el Plan B |
| `README.md` | Este archivo |

---

## 🚀 Configuración en 4 pasos

### Paso 1 — Crear el Google Sheet

Crea una hoja nueva con esta estructura (la primera fila son encabezados):

| A | B | C | D | E |
|---|---|---|---|---|
| **ID** | **Nombre** | **Evento** | **Fecha** | **Estado** |
| EVT-2024-001 | Ana García | Congreso 2024 | 1 de marzo de 2024 | Válido |
| EVT-2024-002 | Carlos López | Congreso 2024 | 1 de marzo de 2024 | REVOCADO |

- La columna **Estado** acepta: `Válido` (o cualquier texto) → ✅ válido / `REVOCADO`, `ANULADO`, `INVALIDO` → ⛔ revocado
- El **ID** debe ser único por certificado. Puedes usar cualquier formato: `EVT-001`, `CONF2024-0023`, etc.

### Paso 2 — Hacer el Sheet público (solo lectura)

1. Abre el Sheet → clic en **Compartir**
2. En "Acceso general" → **Cualquier persona con el enlace**
3. Rol: **Lector**
4. Guardar

> El Sheet sigue siendo de solo lectura para el público. Solo tú (y quien tú invites como editor) puede modificarlo.

### Paso 3 — Copiar el Sheet ID

La URL de tu Sheet tiene esta forma:
```
https://docs.google.com/spreadsheets/d/  AQUÍ_ESTÁ_EL_ID  /edit
```
Copia esa parte larga entre `/d/` y `/edit`.

### Paso 4 — Configurar `index.html`

Abre `index.html` y edita el bloque `CONFIG` al inicio del script:

```js
const CONFIG = {
  SHEET_ID:   'PEGA_AQUÍ_EL_ID',   // ← el ID copiado
  SHEET_NAME: 'Hoja 1',            // ← nombre exacto de la pestaña

  COL_ID:     0,   // columna A — código único
  COL_NAME:   1,   // columna B — nombre
  COL_EVENT:  2,   // columna C — evento
  COL_DATE:   3,   // columna D — fecha
  COL_STATUS: 4,   // columna E — estado
};
```

Si tus columnas están en un orden diferente, solo cambia los números (0 = A, 1 = B, etc.).

---

## 🌐 Publicar en GitHub Pages

1. Sube todos los archivos al repositorio
2. Ve a **Settings → Pages**
3. Source: rama `main`, carpeta `/ (root)`
4. GitHub te dará una URL tipo: `https://tuusuario.github.io/tu-repo/`

El verificador queda en:
```
https://tuusuario.github.io/tu-repo/
```

---

## 📱 El QR único

Genera **un solo QR** que apunte al verificador:
```
https://tuusuario.github.io/tu-repo/
```

Ese QR va impreso en todos los certificados. Los asistentes lo escanean, llegan al sitio e ingresan el código que aparece en su certificado.

Puedes generar el QR en: [qr-code-generator.com](https://www.qr-code-generator.com) o cualquier generador de QR.

---

## 👥 Dar acceso a otra persona para editar

Solo comparte el Google Sheet con esa persona como **Editor**:
1. Abre el Sheet → Compartir
2. Ingresa su correo → rol **Editor**
3. Listo — puede agregar, editar y revocar certificados desde Google Sheets sin tocar el código

---

## 🛡️ Plan B (sin Google Sheets)

Usa `plan-b.html` + `certificates.json` cuando:
- No haya conexión a internet en el evento
- El Sheet tenga algún problema de acceso
- Quieras tener un respaldo offline

Para actualizar los datos del Plan B, edita `certificates.json`:

```json
{
  "lastUpdated": "2024-03-01",
  "certificates": [
    {
      "id": "EVT-2024-001",
      "name": "Nombre del Participante",
      "event": "Nombre del Evento",
      "date": "1 de marzo de 2024",
      "status": "Válido"
    }
  ]
}
```

Sube el archivo actualizado a GitHub y el Plan B queda actualizado en:
```
https://tuusuario.github.io/tu-repo/plan-b.html
```

---

## 🔧 Personalización

| Qué | Dónde |
|---|---|
| Título y subtítulo del sitio | Etiqueta `<h1>` y `.subtitle` en el HTML |
| Nombre que aparece en el footer | Texto en `.footer` |
| Colores | Variables CSS en `:root` |
| Columnas del Sheet | Objeto `CONFIG` en el `<script>` |

---

## ❓ FAQ

**¿Se actualiza en tiempo real?**
Sí. Cada consulta va directo al Sheet en ese momento. Si agregas un certificado al Sheet, al segundo siguiente ya es verificable.

**¿Qué pasa si el Sheet no es público?**
La consulta falla con error de conexión. Asegúrate de que el acceso esté en "Cualquier persona con el enlace".

**¿Funciona en celular?**
Sí, el diseño es completamente responsive.

**¿El código distingue mayúsculas/minúsculas?**
No. El verificador convierte todo a mayúsculas antes de comparar, así que `evt-2024-001`, `EVT-2024-001` y `Evt-2024-001` son equivalentes.
