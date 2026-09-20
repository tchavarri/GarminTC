# Bitácora de carrera

Dashboard estático (HTML + JS, sin backend) que lee tus actividades exportadas de Garmin Connect y muestra volumen semanal, ritmo, frecuencia cardíaca y tu registro de actividades reciente.

## Estructura

```
├── index.html              ← el dashboard (no necesitas tocarlo)
└── data/
    └── activities.csv      ← tus datos. Esto es lo único que actualizas cada semana
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

- **Número principal**: kilómetros corridos en el rango de semanas seleccionado (4/8/12/26).
- **Volumen semanal**: barras con los km por semana.
- **Ritmo y FC por carrera**: evolución del ritmo (min/km) y frecuencia cardíaca media, carrera por carrera.
- **Tarjetas de resumen**: número de carreras, ritmo medio ponderado por distancia, FC media y desnivel acumulado, todo para el rango seleccionado.
- **Tabla de actividades recientes**: las últimas 15 actividades de cualquier tipo (carrera, fuerza, caminata).

## Notas técnicas

- El dashboard usa las columnas del export estándar de Garmin Connect en español: `Tipo de actividad`, `Fecha`, `Título`, `Distancia`, `Frecuencia cardiaca media`, `FC máxima`, `Ritmo medio`, `Ascenso total`, `Pasos`, `Calorías`, `Tiempo`, `Cadencia de carrera media`. Si Garmin cambia el nombre de alguna columna en el futuro, esa métrica puntual mostrará "--" pero el resto del dashboard sigue funcionando.
- Solo las filas de tipo **Carrera** entran en los cálculos de ritmo, volumen y desnivel. Fuerza y caminata aparecen solo en la tabla de actividades recientes.
- Para previsualizarlo en tu computador antes de subirlo (sin GitHub Pages), no lo abras con doble clic — los navegadores bloquean la lectura del CSV por seguridad al abrir un archivo local así. En vez de eso, desde la carpeta del proyecto corre:
  ```
  python3 -m http.server 8000
  ```
  y abre `http://localhost:8000` en el navegador.
