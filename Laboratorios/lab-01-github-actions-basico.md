# 🧪 Lab 01 — Implement GitHub Actions (Fundamentos)

## 🎯 Objetivo
Criar e executar um workflow básico no GitHub Actions para entender a estrutura fundamental de um pipeline CI/CD.

---

## 📘 1. Introdução

O GitHub Actions permite automatizar tarefas como build, testes, deploy e validações usando arquivos YAML dentro do repositório.

Neste laboratório, você aprenderá:

- Como estruturar um workflow  
- Como utilizar triggers  
- Como executar passos (steps) simples  
- Como observar os logs de execução  

---

## 🛠️ 2. Criar o arquivo de workflow

Crie a seguinte pasta no repositório:

```
.github/workflows/
```

Dentro dela, crie o arquivo:

```
build-basico.yml
```

Adicione o conteúdo abaixo:

```yaml
name: Build Básico

on:
  push:
    branches:
      - main
  pull_request:

jobs:
  run-basic-script:
    runs-on: ubuntu-latest

    steps:
      - name: Checkout do repositório
        uses: actions/checkout@v4

      - name: Exibir mensagem no console
        run: echo "Workflow executado com sucesso! 🎉"

      - name: Listar arquivos do diretório
        run: ls -la
```

---

## 📌 3. Estrutura explicada

### `name:`  
Define o nome amigável do workflow.

### `on:`  
Gatilhos que disparam o workflow:  
- `push` para commits na branch *main*  
- `pull_request` para PRs abertos  

### `jobs:`  
Conjunto de tarefas executadas no pipeline.

### `runs-on:`  
Sistema operacional do runner (máquina virtual).  
Usaremos **ubuntu-latest** por ser mais rápido e gratuito.

### `steps:`  
Cada etapa do pipeline.  
Inclui comandos `run:` e ações prontas via `uses:`.

---

## ▶️ 4. Executar o workflow

Após criar o arquivo:

1. Faça commit e push  
2. Acesse a aba **Actions** no GitHub  
3. Escolha o workflow **Build Básico**  
4. Observe a execução em tempo real  

---

## 🔍 5. Validar os logs

Verifique:

- Se o repositório foi feito checkout corretamente  
- Se a mensagem “Workflow executado com sucesso!” apareceu  
- Se a listagem de arquivos foi exibida  

---

## 🧩 6. Exercícios adicionais

Realize pequenos desafios:

### ✔️ Desafio 1  
Adicionar um step que exibe o nome da branch atual:

```yaml
run: echo "Branch atual: ${{ github.ref }}"
```

### ✔️ Desafio 2  
Adicionar um step que falha propositalmente:

```yaml
run: exit 1
```

### ✔️ Desafio 3  
Adicionar um step condicional:

```yaml
if: github.event_name == 'push'
run: echo "Isso só aparece em push!"
```

---

## 🎉 7. Conclusão

Você aprendeu:

- Criar workflows  
- Usar triggers  
- Executar scripts simples  
- Ler logs de execução  

Este é o primeiro passo para avançar para os laboratórios seguintes, onde você realizará deploy para Azure e automação de testes de carga.

Continue para o **Lab 02 — GitHub Actions + Azure (Deploy)**.
