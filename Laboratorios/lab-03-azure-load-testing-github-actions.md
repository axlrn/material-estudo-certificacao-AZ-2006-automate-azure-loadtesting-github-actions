# 🧪 Lab 03 — Azure Load Testing + GitHub Actions (Testes de Carga)

## 🎯 Objetivo
Configurar o **Azure Load Testing** e integrar a execução de testes de carga em um workflow do **GitHub Actions**, fazendo com que o pipeline falhe automaticamente quando os critérios de desempenho não forem atendidos.

---

## 📘 1. Introdução

Neste laboratório você vai:

- Criar um recurso de **Azure Load Testing**
- Definir um arquivo de configuração de teste de carga (YAML)
- Integrar o teste de carga em um workflow existente de CI/CD
- Configurar **Fail Criteria** para reprovar o pipeline em caso de problema

---

## 🏗️ 2. Preparar o recurso Azure Load Testing

No **Portal Azure**:

1. Pesquise por **Load Testing**
2. Clique em **Criar**
3. Preencha:
   - Assinatura
   - Resource group
   - Nome do recurso (ex.: `my-loadtesting-rg`)
   - Região
4. Conclua a criação

Anote o **nome do recurso de Load Testing**, pois será usado no workflow.

---

## 🧾 3. Criar arquivo de configuração do teste (YAML)

Na raiz do repositório, crie uma pasta:

```
loadtest/
```

Dentro dela, crie o arquivo:

```
loadtest-config.yaml
```

Exemplo de conteúdo:

```yaml
version: 1.0
testName: basic-load-test
displayName: Basic Load Test
testDescription: Teste de carga simples executado via GitHub Actions
engineInstances: 1

testPlan:
  testType: URL
  testUrl: "https://SEU_APP.azurewebsites.net/"
  virtualUsers: 50
  duration: "00:05:00"

failureCriteria:
  - avg(response_time_ms) > 1000
  - percentage(error) > 1
```

> 💡 Ajuste a URL do teste (`testUrl`) para apontar para a sua aplicação no Azure App Service.

---

## 🔐 4. Secrets necessários (revisão)

Certifique-se de que os seguintes secrets já existem no repositório (herdados do Lab 02):

- `AZURE_CLIENT_ID`
- `AZURE_TENANT_ID`
- `AZURE_CLIENT_SECRET`
- `AZURE_SUBSCRIPTION_ID`

Adicione também:

- `AZURE_LOADTEST_RESOURCE` → nome do recurso de Azure Load Testing

---

## ⚙️ 5. Atualizar o workflow para incluir teste de carga

Se você já tem o workflow de deploy (`deploy-app.yml`), pode:

- Adicionar um **job** novo para o teste de carga, ou
- Incluir os steps de teste no mesmo job, após o deploy

Aqui vamos usar um job separado chamado `load-test`.

Crie ou edite o arquivo:

```
.github/workflows/deploy-and-loadtest.yml
```

Exemplo de workflow:

```yaml
name: Deploy + Load Test

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

      - name: Deploy para App Service
        uses: azure/webapps-deploy@v3
        with:
          app-name: ${{ secrets.AZURE_WEBAPP_NAME }}
          package: .

  load-test:
    needs: build-and-deploy
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

      - name: Executar teste de carga
        uses: azure/load-testing@v1
        with:
          loadtest-resource: ${{ secrets.AZURE_LOADTEST_RESOURCE }}
          loadtest-config-file: "loadtest/loadtest-config.yaml"
```

> 📌 A Action `azure/load-testing@v1` irá:
> - Ler o arquivo de configuração
> - Executar o teste de carga
> - Aplicar os critérios de falha definidos em `failureCriteria`

Se qualquer critério for violado, o step (e o job) será marcado como **failed**, fazendo o workflow inteiro falhar.

---

## ▶️ 6. Executar e analisar o workflow

1. Faça commit e push das alterações
2. Acesse a aba **Actions** no GitHub
3. Acompanhe:
   - `build-and-deploy`
   - `load-test`
4. Verifique:
   - Tempo de resposta médio
   - Percentual de erros
   - Status final do job

---

## 📊 7. Validar resultados no Portal Azure

No portal:

1. Acesse o recurso de **Azure Load Testing**
2. Vá até **Tests** → selecione o teste
3. Analise:
   - Gráficos de resposta
   - Erros
   - Métricas agregadas

Compare com os critérios definidos no YAML.

---

## 🧩 8. Exercícios adicionais

### ✔️ Desafio 1 — Aumentar a carga
- Aumente `virtualUsers` para 200
- Observe impacto no tempo de resposta

### ✔️ Desafio 2 — Restrições mais rígidas
- Reduza o limite de `avg(response_time_ms)` para 500
- Verifique se o pipeline começa a falhar

### ✔️ Desafio 3 — Gate de qualidade
Integre o job `load-test` como gate para deploy em produção (ex.: só prosseguir se o teste passar).

---

## 🎉 9. Conclusão

Você aprendeu a:

- Configurar o Azure Load Testing
- Criar arquivo YAML de configuração de teste
- Integrar Load Testing a um workflow GitHub Actions
- Reprovar o pipeline automaticamente quando a performance não atende os critérios

Com isso, você fecha o ciclo:

1. **Lab 01** → Fundamentos de GitHub Actions  
2. **Lab 02** → Deploy para Azure App Service  
3. **Lab 03** → Teste de carga automatizado com Azure Load Testing  

Este fluxo representa exatamente o tipo de habilidade prática cobrada na credencial AZ-2006.
