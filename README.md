# EGMS Viewer

Visualizzatore web interattivo dei dati di spostamento del suolo del **European Ground Motion Service (EGMS)**, il servizio europeo di Copernicus basato sull'interferometria radar satellitare (Sentinel-1).

**Prova l'applicazione:** https://egmsviewer.onrender.com

![Mappa dei punti colorati per velocità media di spostamento](docs/img/mappa.png)

> **English summary.** EGMS Viewer is a single-file, dependency-free web app (HTML + Canvas + vanilla JavaScript) that loads an EGMS L2b CSV, maps measurement points coloured by mean velocity, shows the displacement time series with a linear regression for each point, and exports KML and Shapefile. Everything runs in the browser: no file is uploaded. It is a faithful port of a Python/Tkinter desktop tool and is deployed as a static site on Render. See [`docs/DEPLOY_RENDER.md`](docs/DEPLOY_RENDER.md) for the deployment procedure.

## Cosa fa

- **Carica un CSV EGMS** (livello L2b) dal computer, con selezione file o trascinandolo sulla mappa.
- **Mappa dei punti** colorati per velocità media di spostamento (`mean_velocity`), su scala fissa da -10 mm/anno (rosso) a +10 mm/anno (blu), con barra dei colori.
- **Satellite sotto ai punti** (Esri World Imagery), attivabile e disattivabile.
- **Zoom con la rotella** centrato sul cursore, pan trascinando, zoom su un'area, cronologia delle viste e salvataggio della mappa come PNG.
- **Dettaglio del punto**: un clic apre una finestra con le informazioni tecniche (PID, coerenza temporale, RMSE, ecc.) e il grafico della serie temporale di spostamento cumulato, con regressione lineare, velocità stimata e R².
- **Valori puntuali**: un clic su un dato del grafico apre il popup con data, spostamento, variazione dal punto precedente e spostamento totale dall'inizio della serie.
- **Esporta KML** per Google Earth, insieme a una pagina HTML con i grafici interattivi a cui rimandano i link dei punti.
- **Esporta Shapefile** (ZIP con `.shp`, `.shx`, `.dbf`, `.prj`, `.cpg`) pronto per QGIS.

![Finestra di dettaglio con serie temporale e regressione lineare](docs/img/dettaglio.png)

## Provalo in un minuto

1. Apri https://egmsviewer.onrender.com.
2. Scarica [`data/esempio_sintetico.csv`](data/esempio_sintetico.csv) (pulsante *Download raw file* su GitHub).
3. Premi **Carica CSV EGMS…** e scegli il file.
4. Clicca un punto rosso al centro dell'area per vedere la serie temporale in discesa.

> **Il dataset di esempio è sintetico.** L'ho generato con [`data/genera_esempio.py`](data/genera_esempio.py) per simulare una conca di subsidenza attorno a un centro, più rumore e qualche dato mancante. Non contiene misure reali. Per usare dati reali, scaricali dal portale EGMS di Copernicus nel formato CSV L2b.

## Formato del CSV

| Colonne | Obbligatorie | Note |
|---|---|---|
| `latitude`, `longitude`, `mean_velocity` | sì | Coordinate in gradi decimali (WGS84), velocità in mm/anno. |
| Colonne data `AAAAMMGG` (es. `20190105`) | no | Spostamento cumulato in mm. Senza queste colonne resta solo la mappa. |
| `pid`, `mp_type`, `height`, `mean_velocity_std`, `acceleration`, `acceleration_std`, `seasonality`, `seasonality_std`, `temporal_coherence`, `amplitude_dispersion`, `rmse`, `incidence_angle`, `track_angle` | no | Mostrate nel pannello informativo se presenti. |

## Struttura della repository

```
.
├── index.html                  # L'applicazione completa (HTML + CSS + JavaScript)
├── render.yaml                 # Configurazione di deploy su Render (infrastructure as code)
├── data/
│   ├── esempio_sintetico.csv   # Dataset di prova (sintetico)
│   └── genera_esempio.py       # Script che lo genera
├── docs/
│   ├── ARCHITETTURA.md         # Come è fatto il codice e come corrisponde all'originale Python
│   ├── DEPLOY_RENDER.md        # Procedura di pubblicazione su Render
│   └── img/                    # Screenshot usati in questo README
├── python-desktop/
│   ├── egms_viewer.py          # Versione desktop originale (Tkinter + matplotlib)
│   ├── requirements.txt
│   └── README.md
└── LICENSE
```

## Tecnologie

- **HTML, CSS e JavaScript** senza librerie esterne né fase di build: un solo file, che funziona anche offline (il satellite richiede internet).
- **Canvas 2D** per mappa e grafici, scritti da zero.
- **Web Mercator (EPSG:3857)** per allineare i punti ai tile satellitari.
- **Render Static Site** per la pubblicazione, con deploy automatico a ogni push su GitHub.

## Privacy

Il CSV viene letto ed elaborato **solo nel browser**: non viene caricato su nessun server. L'unica comunicazione con l'esterno è la richiesta dei tile satellitari a Esri, che per forza di cose rivela l'area visualizzata. Con il satellite disattivato l'applicazione non effettua nessuna richiesta di rete.

## Da Python al web

L'applicazione nasce come programma desktop in Python (Tkinter, matplotlib, pandas, scipy, geopandas). Questa versione ne riproduce tutte le funzioni nel browser. I risultati sono stati confrontati con quelli del programma originale: il KML esportato è identico riga per riga, i dati dei grafici coincidono e regressione lineare e R² corrispondono a quelli di scipy. I dettagli sono in [`docs/ARCHITETTURA.md`](docs/ARCHITETTURA.md).

## Pubblicazione su Render

La procedura completa, dal repository GitHub al sito online, è descritta in [`docs/DEPLOY_RENDER.md`](docs/DEPLOY_RENDER.md).

## Licenza

Rilasciato con licenza [MIT](LICENSE).

## Autrice

Tania Gianturco
