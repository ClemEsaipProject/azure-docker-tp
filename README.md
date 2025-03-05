

# TP : Déploiement d'une application Flask conteneurisée sur Azure

## Objectif
Déployer une application web Python (Flask) dans un conteneur Docker sur Azure Web App (PaaS) en utilisant VS Code et Azure CLI.

## Prérequis
- [Visual Studio Code](https://code.visualstudio.com/) avec extensions :
  - Docker
  - Azure Tools
- [Docker Desktop](https://www.docker.com/products/docker-desktop)
- [Azure CLI](https://docs.microsoft.com/fr-fr/cli/azure/install-azure-cli)
- Compte Azure actif

## Étapes réalisées

### 1. Création de l'application Flask
```markdown
Fichier `app.py` :
```python
from flask import Flask
app = Flask(__name__)

@app.route('/')
def hello_world():
    return 'Hello, Azure from a Docker Container!'

if __name__ == '__main__':
    app.run(host='0.0.0.0', port=5000)
```

### 2. Configuration Docker
- `Dockerfile` :
```bash
FROM python:3.9-slim
WORKDIR /app
COPY . /app
RUN pip install -r requirements.txt
EXPOSE 5000
CMD ["python", "app.py"]
```

- Fichier `requirements.txt` :
```
Flask
```

### 3. Construction et test local
Commandes exécutées :
```bash
docker build -t flask-app .
docker run -p 5000:5000 flask-app
```

### 4. Déploiement sur Azure
**a. Création du registre de conteneurs (ACR)**
```bash
az acr create --name  --resource-group  --sku Standard --location westeurope
```

**b. Authentification et push de l'image**
```bash
az acr login --name 
docker tag flask-app .azurecr.io/flask-app:v1
docker push .azurecr.io/flask-app:v1
```

**c. Déploiement sur Azure Web App**
Via l'interface Azure dans VS Code :
1. Création d'une Web App Linux
2. Sélection de l'image Docker depuis l'ACR
3. Configuration du port 5000

## Validation
Accéder à l'URL de votre Web App pour voir le message :
`Hello, Azure from a Docker Container!`

## Pipeline CI/CD (Bonus)
Exemple de configuration Azure Pipelines dans `azure-pipelines.yml` :
```yml
trigger:
- main

pool:
  vmImage: 'ubuntu-latest'

steps:
- task: UsePythonVersion@0
  inputs:
    versionSpec: '3.x'

- script: |
    python -m venv venv
    source venv/bin/activate
    pip install -r requirements.txt
    pytest tests/
  displayName: 'Exécution des tests'
```

**Auteur** : WAHAGA Clément
**Date** : 05 mars 2025


