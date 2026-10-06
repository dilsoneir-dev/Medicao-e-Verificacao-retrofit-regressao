# M&V de Retrofit com Modelagem Multivariável

Avaliação prática de Medição e Verificação (M&V) de um projeto de eficiência energética (retrofit), conforme **PIMVP/IPMVP – Opção C** (instalação completa, linha de base por regressão), **PROPEE/ANEEL – Módulo 8** e **ASHRAE Guideline 14**.

> Trabalho individual de Mestrado Profissional em Sistemas de Energia Elétrica (MPSEE), IFSC – Câmpus Florianópolis.
> Prof. Ricardo Luiz Alves.
> Autor: Dilsonei Rigotti.

## Objetivo

1. Ajustar um modelo de **Regressão Linear Múltipla (RLM)** do consumo semanal de energia elétrica na linha de base (Ano 0, 52 semanas) em função de produção de peças (P1–P5), temperatura e umidade relativa.
2. Filtrar as variáveis por **eliminação regressiva** (p < 0,05) e justificar o modelo final (parcimônia, colinearidade Temp–UR).
3. Verificar os critérios de aceitação: **R² > 0,75** e **CV(RMSE) < 15 %**.
4. Calcular a **economia evitada** no Ano 1 (pós-AEE) com a linha de base ajustada e tratar um **Ajuste Não-Rotineiro (ANR)** — extrusora adicional de +15 MWh/semana a partir da semana 30.

## Resultados principais

| Item | Resultado |
|---|---|
| Variável mais correlacionada com o consumo | Temperatura (r = 0,859) |
| Variáveis mantidas (p < 0,05) | P1, P2, Temp |
| Modelo final | `EE = 111,0055 + 2,6083·P1 + 1,4110·P2 + 1,7384·Temp` (MWh/semana) |
| R² / R² ajustado | 0,9513 / 0,9483 |
| CV(RMSE) | 1,33 % |
| Atende R² > 0,75 e CV(RMSE) < 15 %? | Sim |
| Economia evitada no Ano 1 | **2.883,4 MWh** (21,17 % da linha de base ajustada) |
| Cenário da extrusora sem ANR (subestima a economia) | 2.538,4 MWh |
| Cenário da extrusora com ANR | 2.883,4 MWh (20,65 % sobre a linha de base com ANR) |

## Estrutura do repositório

```
MV-retrofit-regressao-ipmvp/
├── README.md
├── LICENSE
├── CITATION.cff
├── requirements.txt
├── memoria_de_calculo/
│   └── ThermoPlex_MV_Memoria_de_Calculo.xlsx   # dados + cálculos (fonte principal)
├── notebook/
│   └── 01_verificacao_mv.ipynb                  # validação cruzada independente em Python
├── relatorio/
│   └── Relatorio_IEEE_ThermoPlex_MV.pdf         # relatório técnico (formato IEEE)
└── figuras/                                      # figuras do relatório (geradas pelo notebook)
```

### Dados versionados no Excel

Não há CSV separado: os dados de entrada estão **dentro da planilha**, abas 01 (Ano 0 - Linha de Base) e 02 (Ano 1 - Pós-AEE).

| Aba | Conteúdo |
|---|---|
| `Ano 0 - Linha de Base` | Dados de entrada: 52 semanas de consumo (MWh), P1–P5 (mil unid.), Temp (°C) e UR |
| `Ano 1 - Pós-AEE` | Dados de entrada: 52 semanas pós-projeto; calcula LB ajustada e economia por semana |
| `Resumo` | Resultados-chave, todos como fórmulas ligadas às abas de cálculo; α editável |
| `4.1a Correlação` · `4.1b Seleção` · `4.1c Parsimônia` · `4.1d Dependência Temp-UR` | Parte I: correlação, seleção de variáveis, parcimônia e dependência entre Temp e UR |
| `4.2a Regressão Completa` · `4.2b Filtragem` · `4.2c Modelo Final` | Parte II: ajuste da RLM (`PROJ.LIN`), eliminação regressiva e modelo final com validação |
| `4.3 Economia Evitada` | Parte III: linha de base ajustada e economia evitada |
| `4.4 ANR` | Parte IV: ajuste não-rotineiro (extrusora) |
| `Aux_X` | Matriz auxiliar de apoio aos cálculos |

Os valores são fórmulas nativas; o α (0,05) e as premissas do ANR são editáveis. Ao mudar um dado, todo o restante se recalcula.

## Metodologia (resumo)

- **Ajuste principal:** Excel, função `PROJ.LIN` (LINEST), conforme a memória de cálculo.
- **Validação cruzada:** o notebook reproduz com `statsmodels` (OLS), lendo os dados diretamente das abas `Ano 0 - Linha de Base` e `Ano 1 - Pós-AEE` do Excel, e compara os resultados com a planilha. A célula de conferência (seção 8) usa `assert` e falha se algum valor divergir. A partir do Notebook, os gráficos são gerados.
- **Diagnósticos:** R², R² ajustado, RMSE (n−k−1), CV(RMSE), estatística F, VIF, Durbin-Watson e Shapiro-Wilk.
- **Parcimônia:** com n = 52, o modelo completo (7 variáveis) deixa 44 graus de liberdade e 6,5 observações por parâmetro; o final (3 variáveis) tem 48 gl e 13 observações por parâmetro (referência: ≥ 10).

## Como reproduzir

Requer Python 3.10+.

```bash
git clone https://github.com/dilsoneir-dev/MV-retrofit-regressao-ipmvp.git
cd MV-retrofit-regressao-ipmvp
python -m venv .venv
source .venv/bin/activate        # Windows: .venv\Scripts\activate
pip install -r requirements.txt
jupyter lab notebook/01_verificacao_mv.ipynb
```

Execute *Run All*. O notebook localiza o Excel em `../memoria_de_calculo/` e grava as figuras em `figuras/`. Para só conferir os números, abra a planilha e veja a aba `Resumo`.

## Relatório

O relatório completo (PDF, formato IEEE) está em [`relatorio/`](relatorio/Relatorio_IEEE_ThermoPlex_MV.pdf).

## Licença e citação

Código e planilha sob licença MIT (veja `LICENSE`). Para citar, use o `CITATION.cff`.
