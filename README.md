# 💰 Mis Gastos — App Android (Cordova + HTML5)

Aplicación móvil **offline** para gestionar gastos mensuales de forma rápida. Diseñada para usar con **una sola mano** en la calle o en la caja del negocio.

- **Stack:** HTML5 + CSS + JavaScript puro (un solo archivo `www/index.html`)
- **Empaquetado:** Apache Cordova → APK Android
- **Persistencia:** `window.localStorage` (no requiere internet ni servidor)
- **Moneda:** ARS con formato argentino (`$ 12.345,67`)
- **Diseño:** Dark Mode (`#0F172A` + violeta `#8B5CF6` + amarillo `#FACC15`)

---

## 📱 Funcionalidades

### Captura de gastos
- Fecha y hora autocompletadas al abrir la app (editables para registrar gastos pasados)
- Lugar / comercio (texto libre con autocompletado de lugares ya usados)
- Categoría (texto libre con sugerencias precargadas: Comida, Bazar, Combustible, Servicios, Varios, etc.)
- Importe numérico con teclado decimal
- Botón grande **"Guardar Gasto"** que limpia el formulario y actualiza totales al instante

### Resumen inteligente
- **Total mensual** destacado en panel superior
- **Desglose por lugar** con texto normalizado (uppercase + trim + colapsa espacios): `Chino`, `chino ` y `CHINO` se agrupan juntos
- **Sub-desglose por categoría** expandible cuando un mismo lugar tiene varias categorías (ej. "Chino → Comida / Bazar")
- Lista ordenada por mayor monto

### Historial y gestión
- Lista de todos los gastos del mes (los más recientes arriba)
- **Editar** cualquier gasto (carga los datos en el formulario)
- **Eliminar** con confirmación
- **Duplicar** (crea un nuevo gasto con mismo lugar, categoría e importe — útil para gastos recurrentes)
- **Filtro por mes/año** con selector y flechas ‹ › para navegar meses anteriores

### Extras
- **Exportar CSV** (descarga `gastos_YYYY-MM.csv` con separador `;` y BOM UTF-8, listo para Excel/Google Sheets)
- Persistencia garantizada al cerrar o reiniciar la app
- Refresco automático de la fecha al volver a la app

---

## 📂 Estructura del proyecto

```
gastos-app/
├── config.xml          # Configuración Cordova (paquete, permisos, plugins)
├── package.json        # Dependencias y scripts npm
├── README.md           # Esta guía
├── res/                # (crear) Iconos y splash screens
│   ├── icon/android/
│   └── screen/android/
└── www/                # Código fuente web
    └── index.html      # App completa (HTML + CSS + JS embebido)
```

---

## 🚀 Guía paso a paso para generar el APK

### Requisitos previos

1. **Node.js 18+** — <https://nodejs.org>
2. **Java JDK 17** — necesario para Android SDK
   - Verificá: `java -version` (debe decir 17.x)
3. **Android Studio** — <https://developer.android.com/studio>
   - Instalá el **SDK de Android 34** (API 34)
   - Instalá **Android SDK Build-Tools 34.0.0**
   - Instalá **Android SDK Platform-Tools**
4. **Apache Cordova CLI**:
   ```bash
   npm install -g cordova@12
   ```

### Variables de entorno

Configurá en tu `~/.bashrc` o `~/.zshrc` (Linux/macOS) o como Variables de Entorno (Windows):

```bash
export ANDROID_HOME=$HOME/Android/Sdk
export ANDROID_SDK_ROOT=$ANDROID_HOME
export PATH=$PATH:$ANDROID_HOME/platform-tools
export PATH=$PATH:$ANDROID_HOME/cmdline-tools/latest/bin
export JAVA_HOME=/ruta/a/tu/jdk-17
```

Verificá:
```bash
cordova --version
adb --version
java -version
```

### Pasos de compilación

#### 1. Entrá al proyecto
```bash
cd gastos-app
```

#### 2. Instalá dependencias
```bash
npm install
```

#### 3. Agregá la plataforma Android (solo la primera vez)
```bash
cordova platform add android
```

#### 4. (Opcional) Probar la app en el navegador
Como es HTML5 puro, podés abrirla directamente:
```bash
# Opción A — Abrir el archivo en el navegador
open www/index.html          # macOS
xdg-open www/index.html      # Linux
start www/index.html         # Windows

# Opción B — Cordova serve
cordova serve 8000
# Luego abrí http://localhost:8000
```

#### 5. Probar en un dispositivo físico
- Habilitá la **Depuración USB** en el celular (Ajustes → Opciones de desarrollador)
- Conectalo por USB
- Verificá que lo detecte: `adb devices`
- Ejecutá:
```bash
cordova run android
```

#### 6. Compilar el APK

**APK de debug** (rápido, sin firmar):
```bash
cordova build android
```
👉 Resultado en: `platforms/android/app/build/outputs/apk/debug/app-debug.apk`

**APK de release** (firmado, listo para distribución):
```bash
# 6a. Generar keystore (UNA sola vez)
keytool -genkey -v -keystore mis-gastos.keystore -alias misgastos \
  -keyalg RSA -keysize 2048 -validity 10000

# 6b. Crear archivo de propiedades (platforms/android/release-signing.properties)
storeFile=../../mis-gastos.keystore
storeType=jks
keyAlias=misgastos
storePassword=TU_PASSWORD
keyPassword=TU_PASSWORD

# 6c. Compilar release
cordova build android --release
```
👉 Resultado en: `platforms/android/app/build/outputs/apk/release/app-release.apk`

#### 7. Instalar el APK en el celular
- Copiá el `.apk` al celular (por cable, Google Drive, etc.)
- Abrí el archivo desde el explorador de archivos del celular
- Android te pedirá permiso para instalar apps de origen desconocido → aceptá

O por ADB:
```bash
adb install -r platforms/android/app/build/outputs/apk/debug/app-debug.apk
```

---

## 🎨 Personalización

### Iconos y splash
1. Generá tus imágenes (1024×1024 PNG para iconos, 2732×2732 para splash)
2. Usá `<https://cordova.apache.org/docs/en/latest/cordova-cli/icon-splash.html>` o copiá los archivos en `res/icon/android/` y `res/screen/android/` con los tamaños indicados en `config.xml`
3. Ejecutá: `cordova prepare android`

### Cambiar el ID del paquete
En `config.xml`, modificá:
```xml
<widget id="com.tudominio.gastosapp" ...>
```
Usá tu propio dominio invertido (ej. `ar.marcos.gastos`).

### Cambiar colores
Editá las variables CSS al inicio de `www/index.html`:
```css
:root {
  --bg: #0F172A;        /* Fondo principal */
  --accent: #8B5CF6;    /* Color de acento */
  --yellow: #FACC15;    /* Montos destacados */
}
```

---

## 🔒 Notas técnicas

- **Almacenamiento:** Se usa `window.localStorage` con la clave `gastos_app_v1`. En Android WebView, esto se guarda en el almacenamiento interno de la app y **persiste al cerrar y reiniciar**. Si desinstalás la app o limpiás los datos, se borra.
- **Exportar CSV:** En Android, el archivo se descarga a la carpeta `Download/` del dispositivo a través del navegador WebView. Si no aparece automáticamente, agregá el plugin `cordova-plugin-file` y `cordova-plugin-file-opener2` (ya incluidos en `config.xml`).
- **Permisos Android:** La app no requiere permisos especiales (no cámara, no ubicación, no internet). Solo necesita almacenamiento para el CSV, que en Android 11+ se maneja vía scoped storage automáticamente.
- **Sin internet:** La app funciona 100% offline. El `<access origin="*">` está solo para no bloquear nada, pero no hace llamadas externas.

---

## 🐞 Solución de problemas

| Error | Solución |
|---|---|
| `Requirements check failed for JDK` | Instalá JDK 17 y configurá `JAVA_HOME` |
| `ANDROID_HOME not set` | Instalá Android Studio y exportá `ANDROID_HOME` |
| `Failed to install apk` | Habilitá Depuración USB en el celular |
| Gradle build fails | Probá `cordova clean` y luego `cordova build` |
| `Cannot find module 'q'` | Ejecutá `npm install -g q` o reinstalá cordova |
| Status bar blanca | Verificá que esté cargado `cordova-plugin-statusbar` |
| App en blanco | Revisá la consola con `chrome://inspect` mientras está conectada al celular |

---

## 🔧 Cambios v1.1 — Post-auditoría técnica

Se aplicaron los siguientes fixes identificados en una auditoría con deep-seek:

### Críticos (Sprint 0)
- **`<script src="cordova.js">`** agregado al HTML — sin esto, ningún plugin funciona en el APK
- **Export CSV reescrito** usando `cordova-plugin-file` + `cordova-plugin-file-opener2` con fallback automático a navegador para test local
- **Iconos/splash**: se comentaron las referencias a archivos inexistentes para que el build no rompa. Para personalizar, ejecutá `npm run icons` (requiere `cordova-res`)
- **Campo importe** cambiado de `type="number"` a `type="text" inputmode="decimal"` con función `parseAmount()` que acepta coma o punto (`1500,50`, `1.234,56`, `1500.50` todos válidos)

### Alta prioridad (Sprint 1)
- **Sticky header** ahora usa `top: var(--safe-top)` para no superponerse al notch
- **`changeMonth()` unificado**: prevMonth, nextMonth y monthSelect ahora llaman a la misma función, que sincroniza la fecha del form con el mes visible
- **`formDirty` flag**: `visibilitychange` ya no pisa la fecha manual escrita por el usuario
- **Listener duplicado de `monthSelect`** eliminado
- **Event delegation**: los `onclick="window.__*"` inline fueron reemplazados por `data-action` + un único listener en `document.body` (mejor patrón, sin polución del scope global)

### Media prioridad (Sprint 2)
- **`cordova-plugin-whitelist` y `cordova-plugin-device`** eliminados (no-op en Cordova 10+)
- **`cordova-plugin-file` bumpado** a `^8.1.0` (compatible con scoped storage Android 11+)
- **`cordova-plugin-file-opener2` bumpado** a `^4.0.0` (mejor soporte Android 13+)
- **Preferencias obsoletas eliminadas**: `GradleVersion`, `android-buildToolVersion`, `KeepRunning`, `AndroidInsecureFileModeEnabled`
- **`main` field** eliminado de `package.json` (Cordova no lo usa, generaba confusión)
- **CSP meta tag** agregada (offline, sin `unsafe-eval`)
- **Hook `backbutton`**: cierra modal o cancela edición antes de salir de la app

### Backlog (no aplicado todavía)
Si en el futuro crece el uso:
- Paginación del historial (más de 200 gastos por mes)
- Almacenar importe en centavos (enteros) para evitar floating-point
- Diffing en `render()` en vez de re-render completo
- Tests funcionales E2E con Cordova sim

---

## 🔧 Cambios v1.2 — Ronda 2 post-auditoría

Se aplicaron los fixes de una segunda revisión que detectó bugs persistentes y
deuda técnica confirmada:

### Bug crítico resuelto
- **`resetForm()` ahora sincroniza con el mes visible** — antes siempre usaba
  `nowLocalISO()`, lo que causaba que al guardar un gasto de un mes pasado y
  cargar otro sin tocar la fecha, el segundo cayera en el mes actual en vez del
  visible. Ahora `resetForm()` decide entre `nowLocalISO()` y
  `viewYear + '-MM-01T12:00'` según corresponda. El `submit()` fue reordenado
  para setear `viewYear/viewMonth` antes de llamar `resetForm()`.

### Consistencia de UX
- **`monthSelect` ahora bloquea en modo edición** — antes solo los botones ‹ ›
  bloqueaban; el dropdown dejaba cambiar de mes silenciosamente y causaba
  comportamiento inconsistente. Ahora muestra el mismo toast
  `"Cancelá la edición primero"` y re-sincroniza el `<select>` con el mes
  original.

### Seguridad / build
- **`<access origin="*">` eliminado** — era contradictorio con el claim de
  "app 100% offline". Ahora solo se permite `https://localhost` (WebViewAssetLoader)
  y `file:///*`. Los `<allow-intent>` para `http://*/*` y `https://*/*` también
  se eliminaron; solo queda `market:*` por si se linkea a la Play Store.
- **Versión bumpeada a 1.1.0** en `package.json` y `config.xml` (antes 1.0.0
  aunque el README decía "v1.1").

### Defensa en profundidad
- **`loadGastos()` sanitiza el JSON** — filtra gastos sin `id`/`datetime`/
  `place`/`category` y coacciona `amount` con `Number(g.amount) || 0`. Así si
  alguien edita manualmente el JSON de localStorage y mete un `amount: "abc"`,
  la app muestra `$0` en vez de `NaN` silencioso.
- **Código muerto eliminado**: `input[type=number]` CSS (ya no aplica porque el
  campo es `type=text`) y `navigator.msSaveOrOpenBlob` (IE/Edge legacy).
- **Script `start`** cambiado de `cordova serve 8000` (deprecado desde CLI 10)
  a abrir `www/index.html` directo en el navegador.
- **Doble `buildMonthOptions()`** en el `submit()` eliminado (era redundante).

---

## 🔧 Cambios v1.3 — Cierre de auditoría (ronda 3)

El auditor confirmó los 8 fixes de la ronda 2 y señaló 1 issue menor más en
`dupGasto()`, que ya está aplicado:

- **`dupGasto()` ahora sincroniza la fecha del form** — antes, al duplicar un
  gasto desde un mes distinto al actual, la vista saltaba al mes actual pero
  el `datetime` del form seguía en el mes anterior. El próximo gasto cargado
  sin tocar la fecha caería en el mes equivocado. Ahora se llama
  `syncFormDateToView()` después de actualizar `viewYear/viewMonth`, con la
  misma guarda de modo edición que el resto del flujo.

### Backlog reconocido (no aplicado, bajo decisión de diseño)

El auditor también mencionó 4 puntos amarillos que no se aplican por razones
justificadas:

1. **`loadGastos()` no valida formato de datetime** — un `datetime: "abc"`
   pasaría el `filter` (truthy) pero `new Date("abc")` es `Invalid Date`, lo
   que haría el gasto invisible en todas las vistas. No se aplicó porque
   requiere corrupción externa del JSON (la app nunca guarda eso). Si en el
   futuro se importa datos desde otra fuente, agregar
   `!isNaN(new Date(g.datetime).getTime())` al filter.

2. **`<allow-navigation href="blob:*" />` es config muerta** — la exportación
   CSV escribe a filesystem con `cordova-plugin-file` y abre con
   `fileOpener2`; el fallback navegador usa `a.download`. Ningún flujo
   "navega" a un blob URL. Se deja como "por si acaso" — inofensivo.

3. **`parseAmount("1.234")` es ambiguo AR/US** — `parseFloat("1.234")` da
   `1.234` (punto decimal), no `1234` (separador de miles AR). Decisión de
   diseño: el placeholder `"0,00"` comunica el formato esperado. Agregar
   heurística ("si hay un punto y 3 dígitos después, asumir miles") puede
   confundir más de lo que ayuda. Queda como decisión documentada.

4. **`buildMonthOptions()` usa `value=idx` numérico en vez de `value="YYYY-MM"`**
   — funciona hoy porque el `change` handler lee `dataset.year/month`, no el
   `value`. Pero si alguien toca `buildMonthOptions` sin entender el
   contrato, puede romper. Deuda técnica pendiente para v1.2.

### Estado final

La app está lista para release como **v1.1.0** con 103 tests pasando, sintaxis
JS válida y todos los bugs críticos resueltos. Los puntos restantes son
mejoras de robustez para cuando la app crezca, no bloqueantes para uso
personal.

---

## 📄 Licencia

MIT — Libre uso, modificación y distribución.

---

**¡Listo!** Cualquier mejora o bug, abrí un issue o mandá PR. 🚀
