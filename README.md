# Flight Monitor Dashboard (público)

Dashboard público de solo lectura para **Flight Monitor PRO** (el sistema privado de monitoreo de precios de vuelos).

- **Sitio:** https://vuelos.learnandbuildit.com (GitHub Pages + dominio personalizado vía `CNAME`)
- **Datos:** `data.json` es una copia saneada de `data/price_history.json` del repositorio **privado** `dbarrantes200/flight-monitor-pro`. Se regenera en cada corrida del workflow del monitor (cada 6 h).
- **Este repo NO contiene código del monitor, secretos, tokens ni claves.** Solo la página del dashboard y los datos de precios.
- La página (`index.html`) es bilingüe ES/EN, responsive, y se actualiza sola cuando `data.json` cambia.

## Cómo se publica (automatizado)

Ver `PUBLISHING.md` para el paso del workflow que copia el historial al publicar en este repo.
