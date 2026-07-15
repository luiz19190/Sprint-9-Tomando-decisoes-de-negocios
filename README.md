# Sprint 9 — Tomando Decisões de Negócios

Projeto do curso de Data Analytics da [TripleTen](https://tripleten.com/), por **Luiz Trajano**.

Atuando como analista de uma grande loja online, o objetivo do projeto é apoiar uma decisão de
negócio em duas frentes: **priorizar hipóteses** de crescimento de receita e **analisar os
resultados de um teste A/B** já em andamento, para decidir se ele deve continuar, ser encerrado
com um vencedor, ou encerrado sem diferença entre os grupos.

## Estrutura do projeto

```
├── Sprint 9 - Tomando Decisoes de Negocios.ipynb   # notebook principal (Parte 1, Parte 2 e conclusão geral)
├── Data/
│   ├── hypotheses_us.csv   # hipóteses de crescimento (Reach, Impact, Confidence, Effort)
│   ├── orders_us.csv       # pedidos do teste A/B (data, grupo, receita, visitante)
│   └── visits_us.csv       # visitas diárias por grupo do teste A/B
├── LICENSE
└── README.md
```

## Parte 1 — Priorização de hipóteses (ICE x RICE)

Nove hipóteses de aumento de receita foram pontuadas com dois frameworks:

- **ICE** = `Impact × Confidence / Effort`
- **RICE** = `Reach × Impact × Confidence / Effort`

| Framework | Hipótese líder | Pontuação |
|---|---|---|
| ICE | Lançar uma promoção com descontos de aniversário | 16,2 |
| RICE | Adicionar formulário de assinatura em todas as páginas | 112,0 |

A troca de ICE para RICE reordena o ranking quase por completo, porque o **Reach** (alcance) só
entra na segunda fórmula. A hipótese "formulário de assinatura" salta para o topo no RICE por
atingir muito mais usuários, enquanto a "promoção de aniversário", líder no ICE, cai para a 5ª
posição — ela tem alto impacto e confiança, mas alcança pouca gente (`Reach = 1`). A decisão final
usa o **RICE** como critério, por penalizar ideias que soam boas isoladamente mas têm baixo
alcance real.

## Parte 2 — Análise do teste A/B

O teste rodou de **01/08/2019 a 31/08/2019** (31 dias corridos, sem lacunas). Antes de analisar,
os dados passaram por um pré-processamento que identificou e removeu usuários "contaminados"
(presentes nos grupos A e B ao mesmo tempo), garantindo que os grupos fossem mutuamente exclusivos.

### O outlier que distorce receita e ticket médio

Um único pedido de **US$ 19.920,40** — o maior de toda a base — é responsável por boa parte da
vantagem aparente do grupo B em receita acumulada e ticket médio:

- Com esse pedido, o ticket médio de B fecha o mês em **+27,8%** acima de A.
- Sem ele, o ticket médio de B cai para **4% abaixo** de A — a vantagem desaparece por completo.

### Testes de significância (Mann-Whitney U, α = 0,05)

| Métrica | Dados | p-valor | Rejeita H₀? | Diferença relativa (B vs A) |
|---|---|---|---|---|
| Conversão | Bruto | 0,0110 | Sim | +16,0% |
| Conversão | Filtrado (sem anomalias) | 0,0070 | Sim | +18,9% |
| Ticket médio | Bruto | 0,8622 | Não | +27,8% |
| Ticket médio | Filtrado (sem anomalias) | 0,8220 | Não | -3,2% |

As anomalias foram definidas com base nos percentis 95/99: usuários com mais de 2 pedidos e
pedidos com valor acima de US$ 830,30 (percentil 99).

O padrão que emerge é claro: a diferença de **conversão** entre os grupos se mantém — e até se
fortalece — depois de remover as anomalias, o que é o oposto do comportamento esperado de um
efeito artificial. Já a diferença de **ticket médio** nunca chega a ser estatisticamente
significativa, e a vantagem nominal de B desaparece (e inverte de sinal) assim que os outliers
são retirados.

## Decisão final

**Parar o teste e declarar o grupo B como líder**, com base exclusivamente na conversão — a única
métrica que se sustenta estatisticamente nos dados brutos e filtrados. A vantagem de receita e
ticket médio observada nos gráficos acumulados não resiste ao teste de significância nem à remoção
do outlier de US$ 19.920,40, e por isso não é usada como argumento da decisão.

## Tecnologias utilizadas

- Python (pandas, numpy, matplotlib, scipy.stats)
- Jupyter Notebook

## Como executar

1. Clone o repositório.
2. Coloque os arquivos `hypotheses_us.csv`, `orders_us.csv` e `visits_us.csv` na pasta `Data/`
   (ou ajuste os caminhos de leitura no notebook, se estiver rodando em outro ambiente).
3. Abra `Sprint 9 - Tomando Decisoes de Negocios.ipynb` e execute as células em ordem.
