# ADR-001: Plataforma e estratégia de ingestão

## Status

Aceito em 2026-07-12.

## Contexto

O projeto cx-intelligence-lakehouse precisa ingerir a base pública de
reclamações do Consumidor.gov.br, disponibilizada pela Senacon como
arquivos CSV mensais no portal dados.mj.gov.br, cobrindo de 2014 a 2026,
com atualização mensal contínua. O objetivo é construir um lakehouse com
arquitetura Medallion, enriquecimento com GenAI, um modelo de ML e um
dashboard analítico.

Duas escolhas precisavam ser definidas antes de construir: em qual
plataforma o projeto rodaria e como o dado entraria nela.

Para embasar essa decisão, quatro spikes foram executados na fase 0:

1. Rede: verificou se o Databricks Free Edition alcança dados.mj.gov.br.
   Resultado: rede aberta. A API CKAN do portal respondeu com status 200
   e retornou a lista de recursos do dataset.
2. AI Functions: verificou se o GenAI está disponível na conta.
   Resultado: a função ai_analyze_sentiment respondeu corretamente,
   confirmando a viabilidade da fase de enriquecimento.
3. Encoding e separador: inspecionou os bytes crus de um arquivo mensal.
   Resultado: encoding UTF-8 com BOM (exige leitura com utf-8-sig),
   separador ponto e vírgula, 30 colunas no schema de 2026.
4. Ponte com o repositório: funciona. O Git folder no Databricks clonou
   o repositório do GitHub com sucesso, confirmando o ciclo de trabalho
   VS Code, GitHub, Databricks.

Restrições conhecidas do Databricks Free Edition: ambiente apenas
serverless, cotas diárias de compute que, se excedidas, desligam o
workspace pelo resto do dia, e limites de taxa nas AI Functions.

## Decisão

O projeto será desenvolvido no Databricks Free Edition, escolhido para
desenvolver domínio prático em Spark, Delta Lake, Unity Catalog e nas
AI Functions nativas da plataforma, complementando um projeto anterior
já construído em GCP e BigQuery.

A ingestão será híbrida: o download dos arquivos CSV da fonte roda fora
do Databricks (em ambiente local ou Colab), e o upload do arquivo bruto
para um Volume do Unity Catalog alimenta a camada Bronze. O
processamento a partir da Bronze roda dentro do Databricks.

## Consequências

Positivas:

- Separação entre ingestão e processamento. Trazer o dado para perto
  (download) fica desacoplado de transformar o dado (Spark). Esse é um
  princípio de arquitetura que vale além deste projeto e em qualquer
  modelo de cobrança.
- Eficiência de custo. O modelo de cobrança do Databricks é por tempo de
  compute consumido (DBU por segundo), não por dado processado. Baixar
  arquivo de dentro de um compute pago significaria pagar processamento
  por uma tarefa de espera de rede. A ingestão híbrida evita isso, e o
  mesmo raciocínio protege a cota diária no Free Edition.
- Portabilidade. O passo de download funciona em qualquer ambiente
  (Colab hoje, um orquestrador amanhã), sem acoplamento à plataforma.

Negativas ou custos assumidos:

- A ingestão passa a ter duas etapas em vez de uma, e exige um ponto de
  execução externo ao Databricks para o download.
- Como o download não roda no notebook, a linhagem completa do dado
  precisa ser registrada com cuidado na Bronze (arquivo de origem e data
  de ingestão) para não perder rastreabilidade.

## Alternativas consideradas

- Ingestão inteiramente dentro do Databricks. Descartada como padrão
  apesar de a rede estar aberta (Spike 1). É mais simples por ter um
  ambiente só, mas gasta compute pago com espera de rede e acopla a
  ingestão à plataforma. A simplicidade não compensou a perda de
  portabilidade e a ineficiência de custo.
- Manter o projeto no GCP e BigQuery. Descartada porque já existe um
  projeto de portfólio nessa stack. Escolher o Databricks amplia a
  cobertura de ferramentas do portfólio e desenvolve competência em
  Spark e nas AI Functions, que são requisitos frequentes de mercado.