# Publicar datos del monitor al dashboard (PUBLISHING)

Este documento describe cómo el repositorio **privado** `dbarrantes200/flight-monitor-pro`
publica el historial de precios en este repositorio **público** en cada corrida del workflow.

## Por qué un PAT

El `GITHUB_TOKEN` del workflow solo tiene permiso dentro de su propio repo.
Para hacer push al repo público del dashboard se necesita un
**Personal Access Token (fine-grained)** con permiso de escritura solo sobre este repo.

### 1. Crear el token (Danny, una sola vez)

1. En github.com, clic en tu avatar → **Settings** → **Developer settings** (abajo, izquierda) →
   **Personal access tokens** → **Fine-grained tokens** → **Generate new token**.
2. **Token name:** `flight-monitor-dashboard-publish`
3. **Expiration:** 1 year (o "No expiration" si prefieres no renovarlo).
4. **Repository access:** *Only select repositories* → selecciona **`flight-monitor-dashboard`**.
5. **Permissions** → **Repository permissions** → **Contents** → **Read and write**.
6. **Generate token** y cópialo (solo se muestra una vez).

### 2. Guardarlo como Secret en el repo PRIVADO (Danny, una sola vez)

1. Ve a `dbarrantes200/flight-monitor-pro` → **Settings** → **Secrets and variables** → **Actions**.
2. **New repository secret** → Name: `DASHBOARD_REPO_TOKEN` → pega el token → **Add secret**.

### 3. Agregar el paso al workflow (Danny, en el editor web del repo privado)

En `dbarrantes200/flight-monitor-pro` → `.github/workflows/flight-monitor.yml`, **después**
del paso `💾 Guardar historial de precios`, pega este bloque (respetando la indentación de 6 espacios):

```yaml
      - name: 📥 Checkout dashboard repo
        uses: actions/checkout@v4
        with:
          repository: dbarrantes200/flight-monitor-dashboard
          token: ${{ secrets.DASHBOARD_REPO_TOKEN }}
          path: dashboard-site

      - name: 🌐 Publicar datos al dashboard
        continue-on-error: true
        run: |
          cp data/price_history.json dashboard-site/data.json
          cd dashboard-site
          git config --local user.email "flight-monitor@github.com"
          git config --local user.name "Flight Monitor Bot"
          git add data.json
          git diff --quiet && git diff --staged --quiet || git commit -m "📊 Update price data [skip ci]"
          git push
```

Notas:
- `continue-on-error: true` hace que un fallo al publicar en el dashboard (p. ej. token expirado)
  **no** marque en rojo la corrida del monitor principal.
- Solo publica `data.json` (precios). No sale código, ni secretos, ni tokens.

### 4. Activar GitHub Pages (Danny, una sola vez)

1. En este repo → **Settings** → **Pages**.
2. **Build and deployment** → Source: **Deploy from a branch** → Branch: **main**, carpeta **/(root)** → **Save**.
3. En **Custom domain** escribe `vuelos.learnandbuildit.com` → **Save**.
   (El archivo `CNAME` del repo hace lo mismo; el campo de la UI lo confirma.)
4. Espera el check **Enforce HTTPS** y actívalo cuando esté disponible.

### 5. DNS en Hostinger (Danny, una sola vez)

En el panel de Hostinger, zona DNS de `learnandbuildit.com`, agrega:

- Tipo: **CNAME** · Host/Nombre: **`vuelos`** · Apunta a: **`dbarrantes200.github.io`** · TTL: 14400 (o el valor por defecto)

La propagación puede tardar de minutos a unas horas.
