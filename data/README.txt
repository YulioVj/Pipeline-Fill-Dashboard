CARPETA DE DATOS
================

Esta carpeta es informativa. El dashboard NO lee archivos desde esta
carpeta automáticamente — los navegadores no permiten que una página web
local lea el disco por sí sola sin que el usuario elija el archivo (ver
"Cómo actualizar la base" en el README.md principal).

La base de datos que el dashboard usa al abrir dashboard.html está
EMBEBIDA dentro de js/data.js (variable DEFAULT_CSV_TEXT), generada a
partir de tu base.csv más reciente.

Para actualizar los datos tienes dos caminos:

1) ACTUALIZACIÓN TEMPORAL (sin tocar archivos):
   En el dashboard, botón "CARGAR NUEVA BASE" → contraseña (ver
   CONFIG.UPDATE_PASSWORD en js/calculations.js) → seleccionar tu .csv
   actualizado. El dashboard se recalcula al instante. Este cambio NO se
   guarda: si recargas la página, vuelve a usar la base embebida.

2) ACTUALIZACIÓN PERMANENTE (para que cargue sola la próxima vez):
   Reemplaza el contenido de la constante DEFAULT_CSV_TEXT en js/data.js
   por el contenido de tu nuevo .csv. Puedes guardar una copia de
   referencia de ese .csv en esta carpeta (data/) para tenerlo a la mano.

ESTRUCTURA ESPERADA DEL CSV
----------------------------
Ver la lista completa de columnas reconocidas en EXPECTED_COLUMNS dentro
de js/data.js. Si faltan columnas, el dashboard sigue funcionando con lo
disponible y lo indica en pantalla (aviso amarillo). Si sobran columnas,
se ignoran sin romper nada.

Las columnas de semana (37, 38, 39, 40, 41, 42...) se detectan
automáticamente por ser encabezados puramente numéricos: no es necesario
modificar el código para agregar semanas nuevas.

La columna "Inventario Proyectado Por Semana" (por ahora no presente en
la base real) se detecta si aparece con el nombre exacto seguido del
número de semana, ej. "Inventario Proyectado Por Semana 41", o variantes
como "Proyectado 41".
