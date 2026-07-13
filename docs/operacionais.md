# Notas operacionais

## 2026-07-13
Fonte dados.mj.gov.br apresentou instabilidade durante testes de
ingestao: primeiro erro HTTP 500 (confirmado tambem direto no
navegador), depois ConnectTimeout na tentativa seguinte. Confirmado
que o problema e da fonte, nao do ambiente local (script, rede e
dependencias funcionando normalmente). Decisao: aguardar e retomar
os testes no dia seguinte. Motivou a adicao de tratamento de erro
especifico (RuntimeError com mensagem traduzida) na funcao
listar_recursos, distinguindo falha de servidor (5xx) de timeout
de conexao.