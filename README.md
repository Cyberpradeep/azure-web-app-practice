# Flask Practice App for Azure App Service

Simple Flask web app (no database) for practicing Azure App Service deployment.

## Project structure

- `app.py` - Flask application entrypoint
- `templates/index.html` - simple UI page
- `requirements.txt` - Python dependencies
- `runtime.txt` - Python runtime for App Service Linux build

## Run locally

1. Create and activate a virtual environment.
2. Install packages:

   ```bash
   pip install -r requirements.txt
   ```

3. Start the app:

   ```bash
   python app.py
   ```

4. Open `http://localhost:8000`.

## Deploy to Azure App Service (quick practice)

Prerequisites:
- Azure CLI installed
- Logged in with `az login`

Commands:

```bash
# 1) Variables
RESOURCE_GROUP=rg-flask-practice
APP_NAME=testing-prap
LOCATION=northuae
RUNTIME="PYTHON|3.12"

# 2) Create resource group
az group create --name $RESOURCE_GROUP --location $LOCATION

# 3) Create Linux App Service plan (B1 for practice)
az appservice plan create \
  --name plan-flask-practice \
  --resource-group $RESOURCE_GROUP \
  --sku B1 \
  --is-linux

# 4) Create web app
az webapp create \
  --name $APP_NAME \
  --resource-group $RESOURCE_GROUP \
  --plan plan-flask-practice \
  --runtime $RUNTIME

# 5) Configure startup command for production
az webapp config set \
  --name $APP_NAME \
  --resource-group $RESOURCE_GROUP \
  --startup-file "gunicorn --bind=0.0.0.0 --timeout 600 app:app"

# 6) Deploy source from current folder (zip deploy)
az webapp up \
  --name $APP_NAME \
  --resource-group $RESOURCE_GROUP \
  --runtime $RUNTIME

# 7) Browse app
az webapp browse --name $APP_NAME --resource-group $RESOURCE_GROUP
```

## Health endpoint

- `/health` returns JSON status.
