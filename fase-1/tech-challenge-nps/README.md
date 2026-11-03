# Tech Challenge Fase 1: Case NPS Preditivo

Projeto da pós-graduação PÓS TECH AI Scientist (FIAP).
Aluna: Viviane Neris Campos

## Objetivo

Descobrir **quais fatores operacionais influenciam a satisfação (NPS) do cliente** em um e-commerce e mostrar como a empresa pode agir **antes** da pesquisa de NPS, que hoje só é coletada depois do fim da jornada de compra.

Pergunta orientadora: *o que, na operação, faz um cliente dar nota baixa?*

## Descrição da base de dados

- Arquivo: `data/desafio_nps_fase_1.csv`
- 2.500 pedidos (uma linha por pedido) e 19 colunas, sem valores ausentes e sem duplicatas.
- Grupos de colunas:
  - **Cliente:** idade, região e tempo de relacionamento.
  - **Pedido:** valor, itens, desconto, parcelas e frete.
  - **Logística:** tempo de entrega, dias de atraso e tentativas de entrega.
  - **Atendimento:** contatos, tempo de resolução e reclamações.
  - **Satisfação:** `nps_score` (target, de 0 a 10), `csat_internal_score` e `repeat_purchase_30d`.
- A base é sintética (de uso didático).

## Metodologia

1. **Carga e checagem** da base.
2. **Target:** `nps_score`, coletado depois da experiência de compra. Para análise, as notas foram agrupadas em detrator (< 7), neutro (7 até < 9) e promotor (≥ 9).
3. **Leakage:** `repeat_purchase_30d` (recompra, que acontece depois) e `csat_internal_score` (outra medida de satisfação, parecida com o target) ficam fora do modelo. `customer_id` e `order_id` são apenas identificadores.
4. **EDA com foco em negócio:** fatores críticos, detratores, ponto de ruptura e perfis de cliente.
5. **Modelo (opcional):** regressão linear (baseline) e árvore de decisão, com separação estratificada em 80% treino e 20% teste. Avaliação por MAE, RMSE e R², comparada a um modelo ingênuo que prevê a nota média.

## Principais resultados

- Nota média de 4,4; 84% dos clientes são detratores e só 4% são promotores (NPS clássico de −80).
- **Atraso na entrega é o principal fator:** cada dia de atraso custa cerca de 1 ponto de nota. Com 3 dias ou mais, de 97% a 100% dos clientes viram detratores.
- O tempo total de entrega não tem relação com a nota. O que pesa é **descumprir o prazo prometido**.
- Reclamações, contatos com o atendimento e tempo de resolução também reduzem a nota.
- O perfil do cliente (região, idade, tempo de casa e valor do pedido) quase não muda a nota.
- Regressão linear: erro médio de 1,4 ponto (contra 2,1 do modelo ingênuo) e R² de 0,51. Ao sinalizar pedidos com nota prevista abaixo de 3, 80% dos sinalizados realmente dão nota baixa.

## Estrutura do repositório

├── data/        # base de dados (CSV)
├── notebooks/   # notebook com tratamento, EDA e modelo
├── reports/     # gráficos (figuras/) e slides
└── README.md


## Como reproduzir

1. Clone o repositório.
2. Instale as bibliotecas: `pip install pandas numpy matplotlib scikit-learn jupyter`.
3. Abra `notebooks/01_nps_eda_e_modelo.ipynb` (Jupyter ou Google Colab) e execute todas as células em ordem. O notebook lê o CSV em `../data/desafio_nps_fase_1.csv`, então mantenha a estrutura de pastas.
   - No Colab, envie a pasta `data/` e ajuste o caminho do `pd.read_csv` se necessário.

## Limitações

- Base sintética: os resultados mostram o raciocínio, não metas reais.
- Correlação não é causa.
- Assumimos que o atendimento acontece antes da pesquisa e que o `csat_internal_score` só fica disponível depois.
- Sem coluna de data, não foi possível validar no tempo.
