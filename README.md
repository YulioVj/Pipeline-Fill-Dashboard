# VENTO Supply Chain Dashboard

Dashboard ejecutivo de Supply Chain para **Vento Motorcycles U.S.A.**: inventario, forecast, cumplimiento semanal, DOH y capacidad de almacenamiento por unidad de negocio (E-COMMERCE / DISTRIBUIDORES).

## 1. Qué hace el dashboard

- KPIs de Forecast, Pedido Revisado, Inventario Cumplido y Pendiente por Entregar.
- Comparación **Inventario Proyectado vs Cumplido por semana**, con acumulado y "ritmo" (atrasado / en línea / adelantado) calculado dinámicamente según el número de semanas disponibles.
- **Capacidad de inventario** por canal con gauges tipo velocímetro (E-COMMERCE, DISTRIBUIDORES, TOTAL), sin doble conteo de almacenes.
- **DOH (Días de Inventario)** — únicamente valor actual, sin histórico ni tendencias.
- Modelos pendientes por entregar (gráfica + tabla).
- Matriz ejecutiva ordenable y filtrable.
- Alertas automáticas y resumen ejecutivo en texto, ambos recalculados con los filtros.
- Actualización de la base protegida por contraseña.

## 2. Cómo abrirlo

Abre `dashboard.html` con doble clic (Chrome, Edge o Firefox). Necesita conexión a internet únicamente para cargar Chart.js desde su CDN oficial (`cdn.jsdelivr.net`) y el logo de Vento desde `vento.com`; si no hay internet, el logo cae automáticamente a un respaldo de texto y el resto del dashboard funciona igual, pero las gráficas no se dibujarán sin Chart.js.

## 3. Estructura del proyecto

```
VENTO_DASHBOARD/
├── dashboard.html          Estructura HTML, referencias a CSS/JS
├── README.md                Este archivo
├── CHANGELOG.md              Historial de versiones
├── assets/
│   ├── logo/LEEME.txt         Cómo está resuelto el logo (ver sección 9)
│   └── icons/LEEME.txt        Carpeta reservada para iconos futuros
├── css/
│   └── dashboard.css          Todos los estilos (variables de color, layout, componentes)
├── js/
│   ├── data.js                 Base embebida (base.csv) + parser CSV + normalización
│   ├── calculations.js         CONFIGURACIÓN + todas las fórmulas de negocio
│   ├── filters.js              Estado de filtros y listas dinámicas
│   ├── security.js             Flujo de contraseña para actualizar la base
│   ├── charts.js                Gráficas (Chart.js) y gauges de capacidad
│   ├── dashboard.js             Renderizado de tarjetas, tablas y secciones
│   └── app.js                   Inicialización y wiring de eventos
└── data/
    └── README.txt               Cómo actualizar los datos
```

## 4. Cómo actualizar la base

Dos formas (ver también `data/README.txt`):

- **Temporal**: botón "CARGAR NUEVA BASE" en el header → contraseña → seleccionar `.csv`. Se recalcula todo al instante, pero se pierde al recargar la página.
- **Permanente**: reemplaza el contenido de `DEFAULT_CSV_TEXT` en `js/data.js` por el nuevo CSV.

El sistema detecta automáticamente qué columnas trae el archivo. Si falta alguna columna esperada, el dashboard sigue funcionando y muestra un aviso indicando cuáles faltan — nunca se rompe.

## 5. Contraseña inicial

```
123
```

Definida en `js/calculations.js`:

```js
const CONFIG = {
  ...
  UPDATE_PASSWORD: "123",
  ...
};
```

**Aclaración de seguridad**: esta contraseña vive en JavaScript del lado del cliente — es una barrera de interfaz, no una medida de seguridad real. Cualquiera con acceso al código puede leerla. Para control de acceso real se necesita un backend con autenticación.

## 6. Cómo cambiar la contraseña

Edita `UPDATE_PASSWORD` en `js/calculations.js` (dentro del objeto `CONFIG`, al principio del archivo) y guarda.

## 7. Cómo cambiar las capacidades

También en `CONFIG` (`js/calculations.js`):

```js
CAPACITY_ECOMMERCE: 1300,
CAPACITY_DISTRIBUIDORES: 3000,
// CAPACITY_TOTAL se calcula solo (suma de las dos anteriores)
```

## 8. Cómo cambiar las reglas de DOH

En el mismo objeto `CONFIG`:

```js
DOH_NORMAL_MAX: 45,   // 1–45 días = NORMAL
DOH_ALERT_MAX: 59,    // 46–59 días = ALERTA; más de 59 = CRÍTICO; 0 = SIN COBERTURA
RHYTHM_TOLERANCE: 5,  // tolerancia en puntos % para el ritmo semanal
```

## 9. Cómo cambiar el logo

El logo se referencia por variable en `js/calculations.js`:

```js
VENTO_LOGO_URL: "https://www.vento.com/wp-content/uploads/vento-logo.svg",
```

Es el logotipo oficial de Vento (obtenido directamente de vento.com). Para usar un archivo local en vez del enlace, ver `assets/logo/LEEME.txt`.

## 10. Cómo cambiar los colores

Todos los colores están centralizados como variables CSS al inicio de `css/dashboard.css`:

```css
--color-primary: #1B3A6B;
--color-success: #1E8E5A;
--color-warning: #B9820A;
--color-danger: #C0392B;
--color-background: #F4F6F9;
...
```

Cambia el valor hex y se propaga a todo el dashboard (tarjetas, badges, barras de progreso, gauges).

## 11. Cómo agregar semanas

No hace falta tocar el código. La detección de columnas de semana en `js/data.js` (función `detectWeekColumns`) reconoce automáticamente cualquier encabezado numérico (`37`, `38`, `41`, `42`, `100`...) como una semana de cumplido real. Basta con agregar la columna a tu CSV y volver a cargarlo.

Cuando exista la columna "Inventario Proyectado Por Semana", agrégala con el nombre exacto seguido del número de semana (ej. `Inventario Proyectado Por Semana 41` o `Proyectado 41`) y el dashboard la detectará igual de forma automática.

## 12. Cómo agregar indicadores

1. Agrega el cálculo correspondiente en `js/calculations.js` (sigue el patrón de las funciones existentes: recibir `rows`, devolver un objeto con los valores calculados, usar `safeDiv` para evitar división entre cero).
2. Agrega el contenedor HTML en `dashboard.html` (una tarjeta `.card` o fila de tabla).
3. Píntalo desde `js/dashboard.js`, dentro de `updateDashboard()` o una función nueva llamada desde ahí.

## 13. Cómo modificar gráficas

Todas las gráficas están en `js/charts.js`, cada una en su propia función (`renderWeeklyChart`, `renderPendientesChart`, `renderGauge`...). Usan Chart.js estándar — consulta la documentación en https://www.chartjs.org/docs/latest/ para cambiar tipos de gráfica, colores o escalas.

## 14. Cómo modificar cálculos

Todas las fórmulas de negocio están en `js/calculations.js`, documentadas con comentarios junto a cada función. Evita duplicar lógica: si necesitas un cálculo parecido a uno existente, reutiliza o extiende la función correspondiente.

## 15. Dependencias

- **Chart.js 4.4.0** vía CDN (`https://cdn.jsdelivr.net/npm/chart.js@4.4.0/dist/chart.umd.min.js`) — única dependencia externa.
- Sin frameworks, sin Node.js, sin build step. Todo es HTML/CSS/JS plano.

## 16. Cómo generar el ZIP

Desde la carpeta que contiene `VENTO_DASHBOARD/`:

```bash
zip -r VENTO_DASHBOARD.zip VENTO_DASHBOARD
```

O selecciona la carpeta `VENTO_DASHBOARD` completa y comprímela con tu herramienta habitual (botón derecho → Enviar a → Carpeta comprimida, en Windows; o Comprimir, en macOS).
