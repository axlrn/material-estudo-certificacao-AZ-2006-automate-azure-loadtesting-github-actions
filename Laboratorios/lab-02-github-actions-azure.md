# 🧪 Lab 02 — GitHub Actions + Azure (Deploy)

## 🎯 Objetivo
Publicar uma aplicação no Azure App Service usando GitHub Actions, criando a integração segura entre GitHub e Azure por meio de um Service Principal.

---

## 📘 1. Introdução

Este laboratório mostra como:

- Criar um **Service Principal** para autenticação
- Configurar **GitHub Secrets**
- Criar um workflow YAML para deploy automatizado
- Validar o deploy no Azure

---

## 🛠️ 2. Criar o Service Principal no Azure

Execute no Azure CLI:

```bash
az ad sp create-for-rbac --name github-sp --role contributor --scopes /subscriptions/<SUBSCRIPTION_ID>
```

A saída conterá:

- **appId** → Client ID  
- **password** → Client Secret  
- **tenant** → Tenant ID  

Guarde-as, você usará no GitHub.

---

## 🔐 3. Registrar Secrets no GitHub

No repositório:

**Settings → Secrets and variables → Actions → New repository secret**

Adicione:

- `AZURE_CLIENT_ID`
- `AZURE_TENANT_ID`
- `AZURE_CLIENT_SECRET`
- `AZURE_SUBSCRIPTION_ID`
- `AZURE_WEBAPP_NAME`

Esses valores serão usados no workflow.

---

## 🧩 4. Criar o workflow de deploy

Crie o arquivo:

```
.github/workflows/deploy-app.yml
```

Com o conteúdo:

```yaml
name: Deploy para Azure App Service

on:
  push:
    branches:
      - main

jobs:
  build-and-deploy:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout do repositório
        uses: actions/checkout@v4

      - name: Login no Azure
        uses: azure/login@v2
        with:
          client-id: ${{ secrets.AZURE_CLIENT_ID }}
          tenant-id: ${{ secrets.AZURE_TENANT_ID }}
          client-secret: ${{ secrets.AZURE_CLIENT_SECRET }}

      - name: Deploy para o App Service
        uses: azure/webapps-deploy@v3
        with:
          app-name: ${{ secrets.AZURE_WEBAPP_NAME }}
          package: .
```

---

## ▶️ 5. Executar o workflow

1. Faça push para a branch **main**  
2. Acesse **Actions**  
3. Verifique o workflow **Deploy para Azure App Service**  
4. Confirme que o deploy foi realizado

---

## 🔍 6. Validar o Deploy no Azure

No portal:

1. Acesse **App Services**
2. Selecione seu WebApp
3. Verifique o último deploy em **Deployment Center**
4. Abra a URL da aplicação

Se tudo estiver correto, sua aplicação estará publicada.

---

## 🧩 7. Exercícios adicionais

### ✔️ Desafio 1  
Adicionar um build (ex.: .NET, Node, Python) antes do deploy.

### ✔️ Desafio 2  
Publicar apenas quando um pull request for aprovado.

### ✔️ Desafio 3  
Adicionar controle de ambiente:

```yaml
environment: production
```

---

## 🎉 8. Conclusão

Você aprendeu a:

- Criar um Service Principal
- Integrar GitHub ↔ Azure com autenticação segura
- Criar workflow de deploy
- Validar publicação no Azure

Continue agora para o **Lab 03 — Azure Load Testing + GitHub Actions (Testes de Carga)**.
