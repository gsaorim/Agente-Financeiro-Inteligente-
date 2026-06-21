# Passo a Passo de Execução

## Setup da API da Groq

Este projeto utiliza a API da **Groq** com o modelo `llama3-8b-8192`, rodando diretamente na nuvem de forma leve e rápida, eliminando a necessidade de instalar ou rodar modelos pesados localmente.

1. A chave de API (GROQ_API_KEY) já está configurada dentro do arquivo src/app.py
2. Certifique-se de estar conectado à internet para que o agente possa responder


## Código Completo

Todo o código-fonte está no arquivo `app.py`.

## Como Rodar

```bash
# 1. Instalar dependências
pip install streamlit pandas groq

# 2. Navegar até a pasta raiz em que o projeto foi salvo
```bash 
cd "Caminho/Ate/A/Pasta/Projeto"
```

# 3. Rodar o app
```bash
python -m streamlit run src/app.py
```

## Evidência de Execução

<img width="1857" height="1512" alt="Image" src="https://github.com/user-attachments/assets/c315dcc6-607a-4cee-b54d-a0b1db0bc6fe" />