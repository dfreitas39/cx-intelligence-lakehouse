# cx-intelligence-lakehouse

Lakehouse de inteligência de reclamações de consumo (Consumidor.gov.br)
no Databricks Free Edition. Arquitetura Medallion (Bronze, Silver, Gold),
enriquecimento com GenAI, ML com MLflow e dashboard AI/BI.

## Contexto de aprendizado
Este é um projeto de portfólio e de estudo. O dono do repositório está
construindo base de engenharia de dados. Portanto:
- Sempre explique O QUE vai fazer e POR QUE antes de propor código
- Prefira soluções simples e legíveis a soluções "espertas"
- Aponte trade-offs e alternativas quando existirem
- Nunca use bibliotecas novas sem justificar a escolha

## Convenções técnicas
- Python com type hints; PySpark para processamento; SQL para consultas
- Ingestão idempotente: rodar duas vezes nunca pode duplicar dados
- Toda tabela Bronze carrega metadados: arquivo_origem, data_ingestao
- Decisões de arquitetura viram ADR em docs/adr/
- Docstrings e comentários em português do Brasil
- Não usar travessões nem meia-risca em textos; usar vírgulas ou pontos

## Fatos confirmados na Fase 0 (spikes)
- Rede do Free Edition ALCANÇA dados.mj.gov.br (rede aberta)
- AI Functions disponíveis na conta (ai_analyze_sentiment funciona)
- CSV da fonte: encoding utf-8-sig (tem BOM), separador ponto e vírgula
- Schema 2026 tem 30 colunas; última coluna "Análise" pode conter texto livre

## Ambiente
- Databricks Free Edition: serverless apenas, cotas diárias de compute,
  rate limits no GenAI. Código deve processar incrementalmente (mês a mês)
- Desenvolvimento local: Windows, VS Code, Python 3.13, Git
- GitHub é a fonte de verdade; Databricks puxa via Git folder

## Estrutura
- src/ingestao/    scripts de download e upload para Volumes
- notebooks/       notebooks Databricks
- docs/adr/        Architecture Decision Records
- docs/            dicionário de dados, diagramas