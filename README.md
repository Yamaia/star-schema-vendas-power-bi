# ⭐ Star Schema para Cenários de Vendas com Power BI

Modelagem dimensional em **Star Schema** para análise de vendas no Power BI: uma tabela fato central ligada a tabelas dimensão, com relacionamentos 1:N e medidas DAX.

Desenvolvido como desafio de projeto da [DIO](https://www.dio.me/).

## 🎯 Objetivo

Transformar uma base de vendas "plana" (uma única tabela) em um modelo estrela, para ganhar desempenho, clareza e facilidade de análise.

## 🗂️ Estrutura do repositório

```
├── dados/
│   └── base-vendas.xlsx         # Base original (tabela plana)
├── relatorio/
│   └── star-schema-vendas.pbix
├── imagens/
│   ├── modelo-star-schema.png   # Diagrama do modelo
│   └── relatorio.png
└── README.md
```

## 🧱 Modelo (Star Schema)

```
                 dim_Calendario
                       │
dim_Cliente ───── fato_Vendas ───── dim_Produto
                       │
              dim_Localidade / dim_Vendedor
```

### Tabela fato
| Tabela | Granularidade | Colunas |
|---|---|---|
| `fato_Vendas` | 1 linha por item vendido | `ID_Venda`, `ID_Cliente`, `ID_Produto`, `ID_Localidade`, `ID_Vendedor`, `Data`, `Quantidade`, `Valor_Unitario`, `Valor_Total`, `Custo` |

### Tabelas dimensão
| Tabela | Chave | Atributos |
|---|---|---|
| `dim_Cliente` | `ID_Cliente` | Nome, Segmento, Cidade |
| `dim_Produto` | `ID_Produto` | Nome, Categoria, Subcategoria |
| `dim_Localidade` | `ID_Localidade` | País, Estado, Cidade |
| `dim_Vendedor` | `ID_Vendedor` | Nome, Equipe |
| `dim_Calendario` | `Data` | Ano, Trimestre, Mês, Nome do Mês, Dia da Semana |

> Ajuste colunas e dimensões conforme a base usada no desafio.

## 🛠️ Como construir

### 1. Importar a base
**Obter dados → Excel/CSV** e carregar a tabela plana no Power Query.

### 2. Criar as dimensões no Power Query
Para cada dimensão:
1. Clicar com o botão direito na consulta original → **Referência**
2. Manter só as colunas da dimensão (**Escolher Colunas**)
3. **Remover Duplicatas** pela chave
4. Renomear a consulta (ex.: `dim_Produto`)

```m
let
    Origem = base_vendas,
    Colunas = Table.SelectColumns(Origem, {"ID_Produto", "Produto", "Categoria", "Subcategoria"}),
    Unicos = Table.Distinct(Colunas, {"ID_Produto"})
in
    Unicos
```

### 3. Criar a tabela fato
Referência da base original, mantendo só as **chaves** e as **métricas** (sem atributos descritivos), e ajustando os tipos de dados.

### 4. Criar a tabela Calendário
```DAX
dim_Calendario =
ADDCOLUMNS(
    CALENDAR(DATE(2020,1,1), DATE(2025,12,31)),
    "Ano", YEAR([Date]),
    "Trimestre", "T" & QUARTER([Date]),
    "Mês", MONTH([Date]),
    "Nome do Mês", FORMAT([Date], "MMMM"),
    "Dia da Semana", FORMAT([Date], "dddd")
)
```

### 5. Relacionamentos
Na **Exibição de Modelo**, ligar cada dimensão à fato:

| Dimensão | Fato | Cardinalidade | Direção do filtro |
|---|---|---|---|
| `dim_Cliente[ID_Cliente]` | `fato_Vendas[ID_Cliente]` | 1:N | Única |
| `dim_Produto[ID_Produto]` | `fato_Vendas[ID_Produto]` | 1:N | Única |
| `dim_Localidade[ID_Localidade]` | `fato_Vendas[ID_Localidade]` | 1:N | Única |
| `dim_Vendedor[ID_Vendedor]` | `fato_Vendas[ID_Vendedor]` | 1:N | Única |
| `dim_Calendario[Date]` | `fato_Vendas[Data]` | 1:N | Única |

Depois, ocultar as colunas de chave da fato e organizar o diagrama com a fato ao centro.

## 🧮 Medidas DAX

```DAX
Total Vendas = SUM(fato_Vendas[Valor_Total])

Quantidade Vendida = SUM(fato_Vendas[Quantidade])

Lucro = SUM(fato_Vendas[Valor_Total]) - SUM(fato_Vendas[Custo])

Margem % = DIVIDE([Lucro], [Total Vendas])

Ticket Médio = DIVIDE([Total Vendas], DISTINCTCOUNT(fato_Vendas[ID_Venda]))

Vendas Ano Anterior =
CALCULATE([Total Vendas], SAMEPERIODLASTYEAR(dim_Calendario[Date]))
```

## 📊 Relatório

- Cartões: Total de Vendas, Lucro, Margem %
- Vendas por mês e por categoria
- Vendas por localidade
- Segmentadores: Ano, Categoria, Cliente

## 🖼️ Prints

![Modelo Star Schema](imagens/modelo-star-schema.png)
![Relatório](imagens/relatorio.png)

## 🌐 Relatório publicado

🔗 [Acessar no Power BI Service](COLE_AQUI_O_LINK)

## 💡 Aprendizados

- Diferença entre tabela fato e tabelas dimensão
- Criação de dimensões a partir de uma base plana no Power Query
- Relacionamentos 1:N e direção do filtro
- Tabela Calendário e inteligência de tempo com DAX
- Ganho de desempenho e clareza com o modelo estrela

## 👤 Autor

**Yamaia** — desenvolvedor focado em backend, cloud e arquitetura de soluções.

[GitHub](https://github.com/SEU_USUARIO) · [LinkedIn](https://linkedin.com/in/SEU_PERFIL)
