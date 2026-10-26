# 💰 Mis Gastos

> App Android (offline-first) para registrar gastos mensuales en pesos argentinos. Construida con HTML5 puro y Apache Cordova, sin frameworks, sin backend, sin servidor.

[![License: MIT](https://img.shields.io/badge/License-MIT-yellow.svg)](LICENSE)
[![Platform: Android](https://img.shields.io/badge/Platform-Android-green.svg)](https://www.android.com/)
[![Cordova: 12](https://img.shields.io/badge/Cordova-12-blue.svg)](https://cordova.apache.org/)
[![Offline First](https://img.shields.io/badge/Offline-First-purple.svg)](#)

Mi primera app móvil. Construida de punta a punta: diseño, código, auditoría (5 rondas con IAs: Gemini, DeepSeek, Z.ai), build y empaquetado APK. Esta es la v1.1.0, lista para usar.

---

## 📱 ¿Qué hace?

Una app personal para anotar gastos en pesos argentinos. Sin login, sin nube, sin permisos raros. La abrís, cargás un gasto en 5 segundos, cerrás. Punto.

### Funcionalidades principales

- ✅ **Carga rápida**: fecha/hora autocompletadas, solo escribís lugar, categoría e importe
- ✅ **Importe con formato AR**: acepta coma decimal (`1500,50`), punto de miles (`1.234,56`) y combinaciones
- ✅ **Resumen por lugar**: agrupa automáticamente por comercio ("Chino", "YPF", "Farmacia")
- ✅ **Sub-desglose por categoría**: si un lugar tiene varias categorías, expandible
- ✅ **Historial mensual** con editar / eliminar / duplicar
- ✅ **Filtro por mes/año** con flechas ‹ ›
- ✅ **Exportar CSV** con BOM UTF-8 y separador `;` (abre directo en Excel argentino)
- ✅ **Persistencia local** con sanitización al cargar (defensa ante JSON corrupto)
- ✅ **100% offline**: no pide permiso de internet, no envía datos a ningún lado
- ✅ **Dark mode** con paleta Slate + Violet + Yellow
- ✅ **Safe-area** para notch y pantallas con cutout
- ✅ **Hook de back button** (cierra modal / cancela edición antes de salir)

---

## 🏗️ Arquitectura

### Stack técnico

| Capa | Tecnología | Razón |
|---|---|---|
| **UI** | HTML5 + CSS3 (custom properties, grid, flexbox) | Sin frameworks para mantener el bundle liviano (~3 MB APK) |
| **Lógica** | JavaScript vanilla (ES5-compatible) | Compatible con WebViews antiguos (Android 5.1+) |
| **Persistencia** | `window.localStorage` | Suficiente para uso personal (~5 MB límite, alcance del proyecto) |
| **Empaquetado** | Apache Cordova 12 + cordova-android 12 | Wrapper nativo para Android |
| **Plugins** | cordova-plugin-file, cordova-plugin-file-opener2, cordova-plugin-statusbar | Solo los esenciales |

### Estructura del proyecto

```
mis-gastos/
├── config.xml              # Configuración de Cordova (Android platform, preferencias, plugins)
├── package.json            # Dependencias y scripts npm
├── .gitignore              # Excluye node_modules, platforms, plugins, keystore
├── README.md               # Este archivo
└── www/                    # Código fuente web (Cordova lo empaqueta en el APK)
    └── index.html          # App completa: HTML + CSS + JS embebidos en un solo archivo (~1.4K líneas)
```

### Decisiones de diseño clave

- **HTML + CSS + JS en un solo archivo**: al no tener build step, simplifica la cadena de compilación. Para v1.2+ se separará cuando sea necesario por mantenimiento.
- **IIFE con `'use strict'`**: encapsulación del scope, sin variables globales.
- **Event delegation en `document.body`**: un único listener para todas las acciones (`data-action="edit"`, `data-action="del"`, etc.), performante y escalable.
- **`escapeHtml()` para todo input de usuario**: prevención de XSS antes de inyectar en `innerHTML`.
- **Sanitización de `localStorage` al cargar**: si el JSON está corrupto, filtra gastos inválidos y coacciona `amount` con `Number(g.amount) || 0`.
- **CSP estricta**: `default-src 'self' 'unsafe-inline' data: blob:;` (unsafe-inline está como deuda para v1.2, cuando separe el JS a archivo externo).

---

## 🚀 Cómo correrlo

### Opción A: Probar la app en el navegador (5 minutos)

No necesitás instalar nada para probar la UI. Abrí `www/index.html` directamente en Chrome.

```bash
# Desde la raíz del proyecto
start www/index.html  # Windows
xdg-open www/index.html  # Linux
open www/index.html  # macOS
```

⚠️ **Limitación**: en navegador, el export CSV usa `<a download>` (no `cordova-plugin-file`). Funciona pero descarga en la carpeta default del navegador, no en `Download/` del celular.

### Opción B: Compilar el APK (requisitos)

#### Requisitos previos

| Software | Versión | Cómo verificar |
|---|---|---|
| Node.js | 20 LTS | `node --version` |
| Java JDK | 17 | `java -version` |
| Android Studio | última | Con SDK Platform 33 + Build-Tools 33.0.2 |
| Apache Cordova | 12 | `cordova --version` |
| Gradle | 8.0+ | `gradle --version` |

#### Pasos de compilación

```bash
# 1. Clonar el repo
git clone https://github.com/abogadodottori-art/mis-gastos.git
cd mis-gastos

# 2. Instalar dependencias
npm install

# 3. Agregar plataforma Android
cordova platform add android

# 4. Verificar requisitos
cordova requirements

# 5. Compilar APK debug
cordova build android

# 6. Ubicación del APK generado
# platforms/android/app/build/outputs/apk/debug/app-debug.apk
```

### Opción C: Descargar el APK ya compilado

Cada release en [GitHub Releases](https://github.com/abogadodottori-art/mis-gastos/releases) incluye el APK debug listo para instalar. Solo necesitás habilitar "Instalar apps de origen desconocido" en tu celular.

---

## 📸 Capturas de pantalla

> TODO: Agregar capturas cuando el repo sea público. Sugerido: 3-5 screenshots en device frame mostrando:
> - Pantalla principal con total del mes
> - Carga de un gasto nuevo
> - Resumen agrupado por lugar
> - Historial con acciones
> - Export CSV funcionando

---

## 🛡️ Seguridad y privacidad

Esta app **no recopila, no transmite ni almacena datos personales** en servidores externos. Todo queda en tu dispositivo.

- **Sin permisos sensibles**: no pide cámara, ubicación, contactos ni almacenamiento masivo
- **Sin login / cuentas**: no pedimos email ni contraseña
- **Sin SDKs de terceros**: no analytics, no AdMob, no Firebase
- **Sin red**: la app funciona 100% offline (no hay llamadas a servidores)
- **Export CSV es local**: el archivo se genera en el dispositivo, vos decidís qué hacer con él

### Política de privacidad (resumen)

```
Mis Gastos no recopila, almacena ni transmite datos personales a servidores
externos. Toda la información se guarda localmente en el dispositivo mediante
localStorage. No se utilizan analytics, crash reporting ni SDKs de terceros.
Para borrar todos los datos: desinstalá la app o borrá los datos desde
Configuración de Android.
```

---

## 🔄 Historial de versiones

### v1.1.0 (octubre 2026) — Release público inicial

Aplicadas 4 rondas de auditoría con IAs (Gemini, DeepSeek, Z.ai) más 1 auditoría de la auditoría. Fixes principales:

- **CSV en APK**: implementado con `cordova-plugin-file` + `cordova-plugin-file-opener2` (fallback a `<a download>` en navegador)
- **Importe con coma decimal**: `input type="text" inputmode="decimal"` + función `parseAmount()` que acepta `1500,50`, `1.234,56`, etc.
- **Safe-area para notch**: `env(safe-area-inset-top)` en sticky header
- **Bug de mes visible**: `resetForm()` ahora sincroniza la fecha con el mes que estás viendo, no siempre con "ahora"
- **Bloqueo de navegación durante edición**: el dropdown de mes te avisa si estás editando
- **Sanitización de localStorage**: filtra gastos inválidos al cargar
- **Event delegation**: reemplazo de `onclick` inline con `data-action` + `closest()`
- **CSP**: política de seguridad restrictiva
- **Hook de back button**: cierra modal / cancela edición antes de salir de la app

### Backlog reconocido (v1.2+)

- Backup / restore JSON (importante para app de finanzas)
- Multi-moneda USD/ARS
- Presupuestos por categoría
- Gastos recurrentes
- Gráfico de torta por categoría
- Migración a SQLite (cuando los datasets superen los 500 gastos)

---

## 🧪 Testing

No tiene tests automáticos (deuda para v1.2). Para validar manualmente:

```bash
# Test de smoke (lógica de parseAmount en Node)
node -e "
function parseAmount(s) {
  if (s == null) return NaN;
  var str = String(s).replace(/[\s\$]/g, '');
  if (!/^\d[\d.,]*\$/.test(str)) return NaN;
  if (str.indexOf(',') > -1) {
    var i = str.lastIndexOf(',');
    var ent = str.slice(0, i).replace(/[.,]/g, '');
    var dec = str.slice(i + 1);
    if (/[.,]/.test(dec)) return NaN;
    return parseFloat(ent + '.' + (dec || '0'));
  }
  if (/^[1-9]\d{0,2}(\.\d{3})+\$/.test(str)) return parseFloat(str.replace(/\./g, ''));
  if ((str.match(/\./g) || []).length > 1) return NaN;
  return parseFloat(str);
}

const tests = [
  ['1500,50', 1500.5],
  ['1.234,56', 1234.56],
  ['1500', 1500],
  ['1.234', 1234],
  ['1e5', NaN],
  ['abc', NaN],
  ['', NaN],
  [null, NaN]
];

tests.forEach(([input, expected]) => {
  const result = parseAmount(input);
  const ok = (Number.isNaN(expected) && Number.isNaN(result)) || result === expected;
  console.log(ok ? '✓' : '✗', JSON.stringify(input), '→', result, ok ? '' : '(esperado: ' + expected + ')');
});
"
```

---

## 📚 Lo que aprendí construyendo esto

Mi primera app móvil. Resumen del proceso:

### Lo que no sabía antes y tuve que aprender

1. **Cadena de build Android**: Node.js → JDK → Android Studio → SDK Platform → Build-Tools → Gradle → Cordova → APK
2. **Diferencia entre Java 8 / 11 / 17 / 21** y por qué `JAVA_HOME` debe apuntar a la versión correcta
3. **Cordova plugin lifecycle**: cómo los plugins nativos se inyectan en el WebView vía `deviceready`
4. **Scoped storage Android 11+**: por qué `cordova.file.externalDataDirectory` es la carpeta correcta y no `externalRootDirectory`
5. **CSP (Content-Security-Policy)**: cómo restringir orígenes en un WebView
6. **Bug de Cordova 12 con Node 20**: `process.exitCode = err.code` falla cuando `err.code` es string (parcheado a mano)
7. **Bug de `--` en comentarios XML**: parser XML rechaza `--` dentro de comentarios
8. **`inputmode="decimal"` en WebView**: el `type="number"` no acepta coma en teclados es-AR
9. **Event delegation**: mejor patrón que 1000 `onclick` inline
10. **Backlog engineering**: cuándo aplicar un fix y cuándo dejarlo documentado

### Lo que mejoraría si arrancara de nuevo

- **Separar HTML / CSS / JS en archivos distintos** desde el día 1 (CSP más estricta)
- **Versionar el schema de datos** (`{v: 2, gastos: [...]}`) desde el primer commit
- **Implementar backup/restore JSON** antes de cualquier feature nueva
- **Usar TypeScript** para detectar errores en tiempo de compilación
- **Implementar tests unitarios** desde el día 1 (mínimo: `parseAmount`, `formatARS`, `normalize`)

### Proceso iterativo con IAs

La app pasó por **5 rondas de auditoría** con IAs distintas (Gemini, DeepSeek, Z.ai). Cada ronda encontró bugs que la anterior había pasado por alto. El resultado final es más sólido que cualquier primera versión que hubiera publicado solo.

El detalle completo de cada ronda está en el directorio `docs/auditorias/` (a subir próximamente).

---

## 🗺️ Roadmap

### v1.2 (Q1 2027)
- [ ] Backup / restore JSON
- [ ] Touch targets de 44-48px en historial
- [ ] Onboarding de 1 pantalla
- [ ] Refactor: separar `index.html` en HTML/CSS/JS
- [ ] Migración a cordova-android 15 + API 36

### v1.3 (Q2 2027)
- [ ] Presupuestos por categoría con barra de progreso
- [ ] Gráfico de torta (CSS conic-gradient)
- [ ] Multi-moneda USD/ARS (cotización manual)
- [ ] Gastos recurrentes

### v2.0 (2027-2028, si hay tracción)
- [ ] Migración a SQLite (cordova-sqlite-storage)
- [ ] Sync opcional en nube (con backend propio)
- [ ] Compartir gastos entre usuarios
- [ ] Versión PWA (Progressive Web App)
- [ ] Publicación en Play Store (con 12 testers)

---

## 📄 Licencia

MIT — Libre uso, modificación y distribución. Ver [LICENSE](LICENSE).

---

## 👤 Autor

**Fabricio Dottori**

- GitHub: [@abogadodottori-art](https://github.com/abogadodottori-art)
- Email: abogadodottori@gmail.com

---

## 🤝 Contribuir

Este es un proyecto personal de aprendizaje, pero si querés:

- Reportar bugs: abrí un [Issue](https://github.com/abogadodottori-art/mis-gastos/issues)
- Sugerir features: abrí un Issue con label `enhancement`
- Mandar PR: hacé fork, creá una rama `feature/tu-cambio`, mandá PR a `main`

**No acepto PRs que agreguen dependencias pesadas** (frameworks, build tools). La filosofía de esta app es mantenerla simple y offline-first.

---

## ⚠️ Disclaimer

Esta app es una herramienta personal de gestión de gastos. **No es un software contable ni financiero certificado**. No soy responsable por pérdida de datos, decisiones financieras tomadas en base a la app, ni problemas derivados de su uso.

Para uso crítico, implementá backups manuales frecuentes (exportá CSV después de cada carga importante).

---

## 🙏 Agradecimientos

- A las IAs que auditaron el código (Gemini, DeepSeek, Z.ai) — cada una encontró bugs que las demás pasaron por alto
- A la comunidad de Apache Cordova por mantener el framework vivo
- A mi yo de hace 2 meses, que no se rindió cuando Gradle no encontraba el SDK

---

**Hecho con ☕ y Code en Argentina 🇦🇷**
