# Bitácora de carrera

Dashboard estático (HTML + JS, sin backend) que lee tus actividades exportadas de Garmin Connect y muestra volumen semanal, ritmo, frecuencia cardíaca y tu registro de actividades reciente.

## Estructura

```
├── index.html              ← el dashboard (no necesitas tocarlo)
├── calculadora.html        ← calculadora de ritmos (independiente, no usa datos)
└── data/
    ├── activities.csv      ← tus datos. Esto es lo que actualizas cada semana
    └── events.csv          ← tus carreras-evento (opcional, ver más abajo)
```

## 1. Publicarlo en GitHub Pages

1. Crea un repositorio nuevo en GitHub (puede ser privado o público).
2. Sube estos dos elementos (`index.html` y la carpeta `data/`) a la raíz del repositorio.
3. Ve a **Settings → Pages**.
4. En "Source" elige **Deploy from a branch**, rama `main`, carpeta `/ (root)`, y guarda.
5. En 1-2 minutos GitHub te dará una URL tipo `https://tu-usuario.github.io/tu-repo/`. Ábrela — ahí está tu dashboard.

## 2. Actualizarlo cada semana

1. En Garmin Connect, ve a **Actividades → (menú de exportar) → Exportar CSV**. Puedes exportar todo tu historial o solo el rango que quieras — el dashboard simplemente lee lo que haya en el archivo.
2. Reemplaza el archivo `data/activities.csv` del repositorio por el nuevo export (mismo nombre, misma carpeta).
3. Sube el cambio (commit + push), o si editas directo en la web de GitHub, usa "Upload files" y confirma el reemplazo.
4. GitHub Pages se actualiza solo, normalmente en menos de un minuto. Refresca la página.

No necesitas tocar `index.html` nunca — todo el cálculo (volumen por semana, ritmo, promedios, la tabla) se hace en el navegador a partir del CSV.

## Qué muestra

- **Número principal**: kilómetros corridos en el rango de semanas (4, 8, 12, 26 o 52) y tipo de sesión seleccionados.
- **Volumen semanal**: barras con los km por semana.
- **Dinámica de carrera**: cadencia, longitud de zancada, oscilación vertical y equilibrio de apoyo (izq/der), promedio del período.
- **Ritmo y FC por carrera**: evolución del ritmo (min/km) y frecuencia cardíaca media, carrera por carrera.
- **Superposición de métricas por carrera**: la FC media queda fija y puedes superponer ritmo, cadencia, longitud de zancada, oscilación vertical y desnivel con botones. Cada métrica tiene su propio eje (mismo color); con "Escala relativa" todas se llevan a 0–100 % para comparar la forma de las curvas, y "Suavizado" promedia las últimas 3, 5 o 10 carreras para que los rangos largos se lean mejor.
- **Vista anual**: gráfico por mes con una serie por año (kilómetros, sesiones, ritmo medio o FC media) y tabla resumen por año, desde `CONFIG.startYear` (2022) hasta el año actual. Usa todo el historial del CSV.
- **Zonas de frecuencia cardíaca**: distribución de tus sesiones en las 5 zonas clásicas (% de tu FC máx.), configurable en `CONFIG.maxHr`.
- **Comparar carreras por distancia estándar**: agrupa automáticamente cualquier carrera de 5K, 10K, media maratón o maratón (según los rangos de `CONFIG.distanceBands`) y compara ritmo, FC, cadencia, zancada, oscilación vertical o desnivel entre intentos — incluyendo una tabla de promedios que separa entrenamientos de carreras oficiales.
- **Tabla de actividades recientes**: las últimas 15 actividades de cualquier tipo (carrera, fuerza, caminata).

## Eventos: la estrella de Garmin

**Regla principal:** en Garmin Connect, una actividad con **estrella naranja = evento**; sin estrella = entrenamiento. El export CSV trae esa marca en la columna `Favorito` y el dashboard la usa directamente. Importante: el CSV refleja las estrellas *al momento de exportar*; si marcas una carrera después, vuelve a exportar.

## Eventos (`data/events.csv`)

Garmin llama "Carrera" a todo trote, así que el dashboard no puede saber solo cuáles son eventos. `data/events.csv` es la lista: un export de Garmin Connect (mismo formato que `activities.csv`) que contiene solo tus carreras-evento.

- Al cargar, el dashboard cruza cada fila con `activities.csv` por fecha y hora exacta. Si coincide, esa actividad queda marcada como **evento**; si no está en `activities.csv` (por ejemplo, carreras de años anteriores), la suma igual.
- Cuando corras un evento nuevo, agrega su fila a `events.csv`. Para un evento puntual también sirve agregar su fecha a `CONFIG.raceDates`.
- Los años que solo tienen eventos (sin tus entrenamientos) se marcan en la vista anual como "solo eventos": sus totales no son tu volumen real.
- Si `events.csv` no existe, el dashboard sigue funcionando y lo avisa en la sección de comparación.

## Calculadora de ritmos (`calculadora.html`)

Página aparte, enlazada desde el encabezado del dashboard (`https://tu-usuario.github.io/tu-repo/calculadora.html`). No lee ningún CSV: funciona sola.

- Elige la distancia (5K, 10K, 15K, media maratón, maratón u otra en km) y completa **uno** de tres datos: tiempo, ritmo (min/km) o velocidad (km/h). Los otros dos se calculan al instante. Si cambias la distancia, se mantiene el ritmo.
- Muestra además el ritmo por milla y los tiempos de pista (100 m a 1.600 m).
- **Plan de carrera:** parciales por km con ritmo, tiempo parcial y acumulado, con estrategia pareja, negativa o positiva (diferencia entre mitades de 1 a 5 %). Con una hora de salida opcional agrega la hora de paso por cada km. También hay una vista de "km cada 5 minutos".
- **Tabla de equivalencias** de ritmo, km/h y marcas para 5K, 10K, 21K y 42K; al tocar una fila se carga ese ritmo.
- "Copiar plan" deja los parciales en el portapapeles para pegarlos donde quieras.

## Configuración editable (dentro de `index.html`, bloque `CONFIG`)

- `maxHr`: tu FC máxima, usada para las zonas.
- `startYear`: primer año que muestra la vista anual (por defecto 2022). Si el CSV trae años anteriores, se agregan solos.
- `raceDates`: fechas (`YYYY-MM-DD`) de eventos que quieras marcar sin agregarlos a `events.csv`.
- `raceKeywords`: palabras que, si aparecen en el título de la actividad, la marcan sola como evento (cuidado con palabras que también uses en entrenamientos normales, como nombres de comunas).
- `distanceBands`: los rangos de km de cada distancia: 5K, 10K, media maratón, largo (22–41,99 km) y maratón. La banda "Otras distancias" recoge todo lo demás (desde 2 km) para que ninguna carrera quede invisible.

## Notas técnicas

- El dashboard usa las columnas del export estándar de Garmin Connect en español: `Tipo de actividad`, `Fecha`, `Título`, `Distancia`, `Frecuencia cardiaca media`, `FC máxima`, `Ritmo medio`, `Ascenso total`, `Pasos`, `Calorías`, `Tiempo`, `Cadencia de carrera media`, `Longitud media de zancada`, `Oscilación vertical media`, `Equilibrio medio de tiempo de contacto con el suelo`. Si Garmin cambia el nombre de alguna columna en el futuro, esa métrica puntual mostrará "--" pero el resto del dashboard sigue funcionando.
- Solo las filas de tipo **Carrera** entran en los cálculos de ritmo, volumen, zonas y desnivel. Fuerza y caminata aparecen solo en la tabla de actividades recientes.
- Para ver 2022–2025 en la vista anual, exporta desde Garmin Connect **todo el historial** (no solo el último año) y reemplaza `data/activities.csv`.
- La sección de comparación por distancia usa **todo tu historial**, no el rango de semanas de arriba (las carreras estándar son eventos espaciados en meses).
- Para previsualizarlo en tu computador antes de subirlo (sin GitHub Pages), no lo abras con doble clic — los navegadores bloquean la lectura del CSV por seguridad al abrir un archivo local así. En vez de eso, desde la carpeta del proyecto corre:
  ```
  python3 -m http.server 8000
  ```
  y abre `http://localhost:8000` en el navegador.
