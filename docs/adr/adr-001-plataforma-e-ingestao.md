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

O projeto será desenvolvido no Databricks