# Case prático: Churn de clientes em uma Telecom

Este repositório contém os datasets usados no case prático de **churn de clientes em uma empresa de telecomunicações**.

O objetivo do case é investigar quais fatores podem estar relacionados ao cancelamento de clientes nos primeiros meses de relacionamento com a empresa, com foco em **churn em até 6 meses após a contratação**.

A proposta é simular um problema comum em empresas de assinatura: clientes entram na base, contratam produtos como internet básica, internet fibra ou streaming, interagem com o atendimento, recebem cobranças mensais e, em alguns casos, cancelam o contrato pouco tempo depois.

## Contexto do problema

Uma empresa de telecom percebeu que uma parte dos clientes cancela o contrato em até 6 meses após a entrada na base. Esse comportamento é preocupante porque clientes que saem cedo podem reduzir o retorno sobre o investimento de aquisição, aumentar o custo comercial e prejudicar o crescimento sustentável da empresa.

Antes de sair criando modelos ou dashboards, precisamos estruturar o problema de negócio. Uma forma de fazer isso é montar uma **issue tree** com possíveis causas para o churn.

Neste case, podemos separar o problema em duas grandes frentes:

1. **Experiência do cliente**: problemas no atendimento, baixa satisfação, muitos contatos, canais pouco eficientes, dificuldades com o serviço contratado ou percepção negativa da empresa.
2. **Competição**: ofertas melhores de concorrentes, preço mais baixo, pacotes mais atrativos ou melhor cobertura em determinadas regiões.

Como os datasets disponíveis trazem informações cadastrais, dados de atendimento e faturamento mensal, o foco da análise será a frente de **experiência do cliente**. A competição pode ser uma hipótese relevante, mas não temos dados diretos sobre concorrentes neste material.

## Objetivo da análise

O objetivo principal é investigar o churn em até 6 meses e identificar padrões que possam ajudar a empresa a tomar decisões práticas para reduzir cancelamentos.

Algumas perguntas que podem guiar a análise:

- Qual é a taxa de churn geral da base?
- Qual é a taxa de churn em até 6 meses após a contratação?
- Clientes que entram em contato com o atendimento cancelam mais?
- A nota do atendimento está relacionada ao churn?
- O canal de atendimento influencia a chance de cancelamento?
- Clientes com maior duração de atendimento têm maior risco de churn?
- Determinados produtos estão associados a maior ou menor churn?
- Existe diferença de churn por idade ou região aproximada pelo CEP?
- O valor de faturamento muda antes do cancelamento?
- Quais sinais poderiam ser usados para priorizar ações de retenção?

## Datasets disponíveis

O case utiliza três arquivos principais:

1. `cadastro_clientes(1).csv`
2. `atendimento.csv`
3. `faturamento_mensal(1).csv`

Cada arquivo representa uma dimensão diferente da jornada do cliente.

---

## 1. Cadastro de clientes

**Arquivo:** `cadastro_clientes(1).csv`

Esta base contém uma linha por cliente e traz informações cadastrais, produtos contratados e dados de churn.

**Dimensões da base:**

- 5.000 linhas
- 11 colunas
- 5.000 clientes únicos

### Dicionário de dados

| Coluna | Descrição |
|---|---|
| `id_cliente` | Identificador único do cliente. Pode ser usado para cruzar as bases. |
| `data_entrada` | Data em que o cliente entrou na base ou assinou contrato com a empresa. |
| `data_saida` | Data de saída/cancelamento do cliente. Quando está vazia, indica que o cliente não cancelou. |
| `reativacao` | Indica se o cliente passou por reativação. |
| `data_reativacao` | Data em que o cliente foi reativado, quando aplicável. |
| `idade` | Idade do cliente. |
| `cep` | CEP do cliente. Pode ser usado como proxy simples de região. |
| `possui_internet_basica` | Indica se o cliente possui o produto de internet básica. |
| `possui_internet_fibra` | Indica se o cliente possui o produto de internet fibra. |
| `possui_streaming` | Indica se o cliente possui o produto de streaming. |
| `churn` | Indica se o cliente cancelou o contrato. |

### Observações importantes

- A base possui 5.000 clientes.
- A coluna `churn` indica se o cliente cancelou em algum momento.
- Para analisar **churn em até 6 meses**, é necessário comparar `data_saida` com `data_entrada`.
- Clientes sem `data_saida` podem ser tratados como clientes ativos.
- A variável `cep` pode ser usada para análises regionais, mas deve ser interpretada com cuidado, pois não traz diretamente cidade, estado ou bairro.

---

## 2. Atendimento

**Arquivo:** `atendimento.csv`

Esta base contém registros de atendimento realizados pelos clientes. Um mesmo cliente pode aparecer várias vezes, pois pode ter feito mais de um contato com a empresa.

**Dimensões da base:**

- 18.324 linhas
- 5 colunas
- 2.785 clientes únicos com pelo menos um atendimento registrado

### Dicionário de dados

| Coluna | Descrição |
|---|---|
| `id_cliente` | Identificador do cliente atendido. Pode ser usado para cruzar com a base cadastral. |
| `data_atendimento` | Data em que o atendimento aconteceu. |
| `canal` | Canal utilizado no atendimento. Os canais disponíveis são `telefone`, `whatsapp` e `chat_site`. |
| `duracao_segundos` | Duração do atendimento em segundos. |
| `nota` | Nota atribuída ao atendimento, variando de 1,0 a 5,0. |

### Observações importantes

- Nem todos os clientes possuem atendimento registrado.
- A base pode ser agregada por cliente para criar variáveis como:
  - quantidade de atendimentos;
  - nota média do atendimento;
  - menor nota recebida;
  - maior duração de atendimento;
  - canal mais utilizado;
  - quantidade de atendimentos antes do churn;
  - quantidade de atendimentos nos primeiros 30, 60, 90 ou 180 dias.
- É importante tomar cuidado com vazamento de informação. Para analisar churn em até 6 meses, devem ser usadas apenas informações disponíveis antes do evento de churn ou dentro da janela definida para análise.

---

## 3. Faturamento mensal

**Arquivo:** `faturamento_mensal(1).csv`

Esta base contém o faturamento mensal dos clientes. Cada linha representa o faturamento de um cliente em determinado mês.

**Dimensões da base:**

- 66.056 linhas
- 3 colunas
- 5.000 clientes únicos
- 29 meses disponíveis, de `2024-01` a `2026-05`

### Dicionário de dados

| Coluna | Descrição |
|---|---|
| `id_cliente` | Identificador do cliente. Pode ser usado para cruzar com a base cadastral. |
| `ano_mes` | Ano e mês de referência do faturamento. |
| `receita_bruta` | Valor de receita bruta gerado pelo cliente naquele mês. |

### Observações importantes

- A base permite analisar a evolução mensal da receita por cliente.
- A partir dela, é possível criar variáveis como:
  - receita média mensal;
  - receita total nos primeiros 6 meses;
  - variação da receita ao longo do tempo;
  - queda de faturamento antes do churn;
  - número de meses faturados;
  - receita no mês de entrada;
  - receita nos meses anteriores ao cancelamento.
- Assim como na base de atendimento, é necessário cuidado para não usar informações que só aconteceram depois do churn ou depois da janela de análise.

---

## Possíveis cruzamentos entre as bases

As três bases podem ser cruzadas pela coluna `id_cliente`.

Exemplo de estrutura esperada para análise:

1. Começar pela base `cadastro_clientes(1).csv`, pois ela contém uma linha por cliente e a variável de churn.
2. Criar a variável-alvo de churn em até 6 meses usando `data_entrada` e `data_saida`.
3. Agregar a base de atendimento por cliente.
4. Agregar a base de faturamento por cliente e por janelas temporais.
5. Unir as variáveis agregadas à base cadastral.
6. Analisar padrões de churn por perfil, produto, atendimento e faturamento.

## Definição sugerida da variável-alvo

Uma definição possível para a variável-alvo do case é:

> Cliente teve churn em até 6 meses se possui `data_saida` preenchida e se a diferença entre `data_saida` e `data_entrada` é menor ou igual a 6 meses.

Em termos práticos:

- `churn_6m = True`: cliente cancelou em até 6 meses após a entrada.
- `churn_6m = False`: cliente não cancelou ou cancelou após mais de 6 meses.

Essa definição pode ser ajustada dependendo do objetivo didático da aula.

## Cuidados analíticos

Alguns cuidados importantes para os alunos:

- Não confundir churn geral com churn em até 6 meses.
- Evitar usar dados posteriores ao cancelamento para explicar o próprio cancelamento.
- Tratar clientes sem atendimento como um grupo relevante, e não apenas como dado ausente.
- Agregar corretamente bases transacionais antes de cruzar com a base de clientes.
- Verificar se há múltiplas linhas por cliente nas bases de atendimento e faturamento.
- Analisar não apenas correlação, mas também possíveis explicações de negócio.
- Separar métricas descritivas de variáveis que poderiam ser usadas em um modelo preditivo.

## Exemplos de análises possíveis

Algumas análises que podem ser feitas durante o case:

- Taxa de churn geral e churn em até 6 meses.
- Churn por produto contratado.
- Churn por faixa etária.
- Churn por CEP ou região aproximada.
- Churn por quantidade de atendimentos.
- Churn por canal de atendimento.
- Churn por nota média de atendimento.
- Churn por duração média dos atendimentos.
- Receita média mensal de clientes churn e não churn.
- Evolução da receita antes do cancelamento.

## Entregável esperado

Ao final do case, espera-se que os alunos sejam capazes de:

- estruturar um problema de negócio antes de partir para a análise;
- entender quais dados estão disponíveis e quais hipóteses podem ser testadas;
- construir uma variável de churn em até 6 meses;
- cruzar bases cadastrais, transacionais e de atendimento;
- criar métricas acionáveis para investigar churn;
- gerar insights úteis para uma área de negócio;
- propor ações de retenção com base nos dados analisados.
