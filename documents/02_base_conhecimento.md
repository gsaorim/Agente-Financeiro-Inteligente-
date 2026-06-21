# Base de Conhecimento

## Dados Utilizados

| Arquivo | Formato | Para que serve no Nico? |
|---------|---------|---------------------|
| `historico_atendimento.csv` | CSV | Contextualizar interações anteriores, ou seja, dar continuidade ao atendimento de forma mais eficiente. |
| `perfil_investidor.json` | JSON | Personalizar as explicações sobre as dúvidas e necessidades de aprendizado do cliente. |
| `produtos_financeiros.json` | JSON | Conhecer os produtos disponíveis para que eles possam ser ensinados ao cliente. |
| `transacoes.csv` | CSV | Analisar padrão de gastos do cliente e usar essas informações de forma didática. |
| `dicionario_analogias.json` *(Novo)* | JSON | Fornecer metáforas prontas e validadas (ex: "CDI é a temperatura do mercado") para evitar que o Nico invente explicações confusas. |

---

## Adaptações nos Dados

> Você modificou ou expandiu os dados mockados? Descreva aqui.

- **Foco Absoluto em Renda Fixa e Começo Seguro:** Removi fundos de ações e fundos multimercados da base de ensino de investimentos, mantendo estritamente produtos de volatilidade ultrabaixa e fáceis de explicar (Tesouro Selic, CDB e LCI/LCA). O Fundo Imobiliário (FII) foi mantido como o limite máximo de risco médio (renda variável controlada) para fins puramente comparativos.
- **Inclusão do Dicionário de Analogias:** Um arquivo de mapeamento foi adicionado para guiar a didática informal e visual do Nico.

---

## Estratégia de Integração

### Como os dados são carregados?
> Descreva como seu agente acessa a base de conhecimento.

Existem duas possibilidades, injetar os dados diretamente no prompt (Ctrl + C, Ctrl + V) ou carregar os arquivos via código, como no exemplo abaixo:

```python
import pandas as pd
import json

perfil = json.load(open('./data/perfil_investidor.json'))
transacoes = pd.read_csv('./data/transacoes.csv')
historico = pd.read_csv('./data/historico_atendimento.csv')
produtos = json.load(open('./data/produtos_financeiros.json'))
analogias = json.load(open('./data/dicionario_analogias.json')) # Arquivo extra do Nico
```

### Como os dados são usados no prompt?
> Os dados vão no system prompt? São consultados dinamicamente?

Para simplificar, podemos simplesmente "injetar" os dados em nosso prompt, agarntindo que o Agente tenha o melhor contexto possível. Lembrando que, em soluções mais robustas, o ideal é que essas informaçoes sejam carregadas dinamicamente para que possamos ganhar flexibilidade.

```text
DADOS DO CLIENTE E PERFIL (data/perfil_investidor.json):
{
  "nome": "João Silva",
  "idade": 32,
  "profissao": "Analista de Sistemas",
  "renda_mensal": 5000.00,
  "perfil_investidor": "iniciante_conservador",
  "objetivo_principal": "Construir reserva de emergência e organizar gastos",
  "patrimonio_total": 15000.00,
  "reserva_emergencia_atual": 10000.00,
  "aceita_risco": false,
  "metas": [
    {
      "meta": "Completar reserva de emergência",
      "valor_necessario": 15000.00,
      "prazo": "2026-06"
    },
    {
      "meta": "Entrada do apartamento",
      "valor_necessario": 50000.00,
      "prazo": "2027-12"
    }
  ]
}

TRANSAÇÕES DO CLIENTE (data/transacoes.csv):
data,descricao,categoria,valor,tipo
2026-05-01,Salário,receita,5000.00,entrada
2026-05-02,Aluguel,moradia,1200.00,saida
2026-05-03,Supermercado,alimentacao,450.00,saida
2026-05-05,Netflix,lazer,55.90,saida
2026-05-07,Farmácia,saude,89.00,saida
2026-05-10,Restaurante,lazer,120.00,saida
2026-05-12,Uber,transporte,45.00,saida
2026-05-15,Conta de Luz,moradia,180.00,saida
2026-05-20,Academia,saude,99.00,saida
2026-05-25,Combustível,transporte,250.00,saida

HISTÓRICO DE APRENDIZADO DO CLIENTE (data/historico_atendimento.csv):
data,canal,tema,resumo,resolvido
2026-04-15,chat,Poupança,Entendeu por que a poupança rende pouco,sim
2026-04-22,chat,Organização,Aprendeu sobre a regra dos potes (50/30/20),sim
2026-05-01,chat,Tesouro Selic,Tirou dúvidas sobre o que é o Tesouro Direto,sim
2026-05-12,chat,Metas,Calculou quanto falta para terminar a reserva de emergência,sim

PRODUTOS DISPONÍVEIS PARA ENSINO (data/produtos_financeiros.json):
[
  {
    "nome": "Tesouro Selic",
    "categoria": "renda_fixa",
    "risco": "muito baixo",
    "rentabilidade": "100% da Selic",
    "aporte_minimo": 30.00,
    "indicado_para": "Reserva de emergência e iniciantes absolutos"
  },
  {
    "nome": "CDB Liquidez Diária",
    "categoria": "renda_fixa",
    "risco": "baixo",
    "rentabilidade": "102% do CDI",
    "aporte_minimo": 100.00,
    "indicado_para": "Quem busca segurança com o dinheiro rendendo livre todo dia"
  },
  {
    "nome": "LCI/LCA",
    "categoria": "renda_fixa",
    "risco": "baixo",
    "rentabilidade": "95% do CDI",
    "aporte_minimo": 1000.00,
    "indicado_para": "Quem pode deixar o dinheiro parado por pelo menos 90 dias (isento de Imposto de Renda)"
  },
  {
    "nome": "Fundo Imobiliário (FII)",
    "categoria": "fundo",
    "risco": "medio",
    "rentabilidade": "Dividendos mensais (entre 0,5% a 1% ao mês)",
    "aporte_minimo": 100.00,
    "indicado_para": "Aprender como funciona a renda variável através de aluguéis, sem correr riscos extremos"
  }
]

DICIONÁRIO DE ANALOGIAS PERMITIDAS (data/dicionario_analogias.json):
{
  "reserva_de_emergencia": "O seu estepe do carro. Você não usa todo dia, mas se o pneu furar, ele te salva de ficar parado na estrada.",
  "inflacao": "O monstrinho do supermercado que faz o seu dinheiro valer menos com o tempo.",
  "liquidez": "A velocidade do resgate. Alta liquidez significa que o dinheiro volta para sua mão voando.",
  "cdi": "O termômetro dos juros dos bancos. Se ele sobe, a renda fixa rende mais.",
  "metodo_50_30_20": "A regra dos três potes: 50% para Necessidades, 30% para Desejos e 20% para o Futuro."
}
```

---

## Exemplo de Contexto Montado

> Mostre um exemplo de como os dados são formatados para o agente.

O exemplo de contexto montado abaixo, se baiseia nos dados originais da base de conhecimento, mas os sintetiza deixando apenas as informações mais relevantes, otimizando assim o consumo de tokens. Entretanto, vale lembrar que mais importante do que economizar tokens, é ter todas as informações relevantes disponíveis em seu contexto.

```
DADOS DO CLIENTE:
- Nome: João Silva (32 anos)
- Foco atual: Organização e Montar Reserva de Emergência.
- Nível de tolerância a risco: Zero (Conservador/Medroso).

[RAIO-X DAS FINANÇAS DELE (MÉTODO 50/30/20)]
* Calculado automaticamente via script com base no arquivo 'transacoes.csv':
- Renda Mensal Total: R$ 5.000,00
- Pote 50% (Necessidades - Aluguel, Luz, Mercado, Saúde): R$ 2.018,00 (Ocupando 40% da renda - Ótimo!)
- Pote 30% (Estilo de Vida - Lazer, Restaurante, Uber): R$ 470,90 (Ocupando 9.4% da renda - Controlado)
- Pote 20% (Guardado/Sobrou para o Futuro): R$ 2.511,10 (Disponível para aportar na reserva)

[STATUS DA RESERVA]
- Meta Final: R$ 15.000,00
- Guardado hoje: R$ 10.000,00
- Falta para a meta: R$ 5.000,00 (Prazo final: Junho de 2026)

[DIRETRIZES DE RESPOSTA DO NICO]
1. Se o João perguntar sobre onde colocar os R$ 2.511,10 que sobraram, você NÃO PODE dizer "coloque no banco X". Você deve explicar que, como o foco dele é Reserva de Emergência, ele precisa procurar opções com "Liquidez Diária" (use a analogia do estepe do carro).
2. Se ele perguntar sobre o histórico, lembre-o de que ele já entendeu sobre a poupança em abril, e que agora o passo é fazê-la crescer no Tesouro Selic ou CDB.
3. Use sempre o tom informal, chamando-o pelo nome (João) de forma amigável.
```