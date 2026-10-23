# Mini-Projeto Avaliativo - Análise de Dados com Python

**Aluna:** Suelen Maiara Heemann
**Turma:** T8
**Módulo 1 - Semana 07**

## Objetivo
Fazer uma análise exploratória de dados (AED) de uma base de varejo, transformando
dados brutos em informações compreensíveis: diagnóstico de qualidade, limpeza
justificada, funções reutilizáveis, estatísticas, agrupamentos e exportação de uma
base tratada.

## Origem da base
- **Fonte:** Kaggle - https://www.kaggle.com/datasets/namespaiva/base-varejo/data
- **Arquivo usado:** `Base Varejo.csv` (separador `;`)
- **Versão:** baixada em 22/10/2026
- O arquivo original **não está neste repositório** (está no `.gitignore`). Para
  reproduzir, baixe-o no link acima.

## Unidade da análise
Cada linha da base representa **um item de uma compra**, e não um cliente nem uma
compra inteira. Por isso:
- contar linhas = contar **itens**;
- contar `CO_ID` diferentes = contar **compras**;
- contar `CL_ID` diferentes = contar **clientes**.

A base tem 830.000 itens, 18.471 compras e 1.000 clientes.

## Colunas utilizadas
| Coluna | Significado |
|---|---|
| `DATA` | Data da compra |
| `CO_ID` | Identificador da compra |
| `CL_ID` | Identificador do cliente |
| `CL_GENERO` | Gênero do cliente (F ou M) |
| `CL_EC` | Código de 1 a 5 (provavelmente estado civil) |
| `CL_FHL` | Número de filhos do cliente (0 a 4) |
| `CL_SEG` | Segmento do cliente (A, B ou C) |
| `PR_ID` | Identificador do produto |
| `PR_CAT` | Categoria do produto |
| `PR_NOME` | Nome do produto |

> Os significados de `CL_EC`, `CL_FHL` e `CL_SEG` foram deduzidos pelos valores
> encontrados, pois a base não traz dicionário de dados.

## Diagnóstico de qualidade
- 830.000 linhas e 14 colunas; as colunas `Unnamed: 10` a `Unnamed: 13` estão 100% vazias.
- Valores ausentes nas outras 10 colunas: **0**.
- Textos vazios ou só espaços: **0**.
- Linhas duplicadas: **96.553**.
- `PR_CAT = #N/D`: **3.650** linhas, todas do produto 107.
- `REFRIGERANTE LIMaO` (letra minúscula): **7.320** linhas.

## Decisões de limpeza
| Problema | Decisão | Motivo |
|---|---|---|
| 4 colunas vazias | Removidas | Não têm informação |
| Ausentes e textos vazios | Nada a tratar | O diagnóstico encontrou 0 |
| 96.553 linhas duplicadas | **Mantidas** | A base não tem coluna de quantidade; linhas iguais na mesma compra provavelmente são o mesmo produto levado mais de uma vez, e não dá para confirmar que sejam erro |
| `#N/D` (3.650 linhas) | Trocado por `NAO INFORMADO` | São compras reais; não inventei categoria nem apaguei as linhas |
| `REFRIGERANTE LIMaO` | Padronizado para maiúsculas | Corrige o texto sem mudar o significado |
| `DATA` como texto | Convertida para data | Permite ordenar e filtrar por período |

Resultado: 830.000 registros antes e 830.000 depois (nenhuma linha apagada);
colunas de 14 para 10.

## Funções criadas
- `tratar_dados(tabela)`: recebe a tabela bruta e devolve a tabela tratada.
- `resumo_numerico(serie)`: recebe uma coluna numérica e devolve contagem, média,
  mínimo e máximo.

## Análises
- **Estatísticas de `CL_FHL`** (1 linha por cliente, 1.000 clientes): média 1,14,
  mínimo 0, máximo 4.
- **Filtro:** clientes do segmento A (84 clientes, 1.492 compras, 67.736 itens).
- **Agrupamento 1:** itens por categoria (`groupby("PR_CAT").size()`).
- **Agrupamento 2:** compras por gênero (`groupby("CL_GENERO")["CO_ID"].nunique()`).

## Conclusões
1. A base tem 830.000 itens, em 18.471 compras feitas por 1.000 clientes.
2. ALIMENTOS concentra 434.767 itens (52,4%), seguido de HIGIENE (18,7%) e LIMPEZA (17,6%).
3.Mulheres fizeram 9.615 compras (52,1%) e homens 8.856 (47,9%).
4. A média é de 1,14 filho por cliente, e 527 dos 1.000 clientes não têm filhos.
5. O segmento A tem 84 clientes, que geraram 1.492 compras (8,1% do total).

## Recomendação
Como ALIMENTOS responde por mais da metade dos itens, vale investigar quais produtos
dessa categoria mais saem (PRESUNTO COZIDO, com 14.381 itens, é o que mais aparece)
para planejar estoque e ofertas.

## Limitação
A base não tem valores em dinheiro nem quantidade comprada, então a análise mede itens
e compras, mas não permite calcular faturamento, ticket médio ou margem. As 96.553
linhas repetidas foram mantidas por não ser possível confirmar que são erro.

## Explicação do processo
A **leitura** dos dados ocorre no início do notebook, com `pd.read_csv(..., sep=";")`.
A **transformação** acontece na função `tratar_dados()`, que remove as colunas vazias,
padroniza o texto, troca `#N/D` por `NAO INFORMADO` e converte a data. A **gravação**
é feita com `df_limpo.to_csv("varejo_limpo.csv", index=False)`. A decisão de manter
as duplicatas e de não apagar as linhas com `#N/D` preservou todas as compras
registradas, o que mantém as contagens por categoria e por compra fiéis ao que de fato
aconteceu na loja; padronizar o texto evita contar o mesmo produto como dois itens.

## Como executar
1. Baixe o CSV no Kaggle (link acima) e salve na mesma pasta do notebook com o nome
   `Base Varejo.csv`.
2. Instale as dependências: `pip install pandas ipykernel`.
3. Abra `Miniprojeto_Suelen_T8.ipynb` no VS Code (ou Jupyter) e rode todas as células.
4. O arquivo `varejo_limpo.csv` (base tratada) será gerado na mesma pasta.