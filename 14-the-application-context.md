# O Contexto da Aplicação


O contexto da aplicação preserva o rasto de dados do nível de aplicação durante uma requisição, comando de interface de linha de comando, ou outra atividade. No lugar da passagem da aplicação em volta de cada função, as delegações [**`current_app`**](#) e [**`g`**](#) são acessadas.

Isto é semelhante ao [O Contexto da Requisição](#), a qual preserva o rasto de dados do nível da requisição durante uma requisição. O contexto da aplicação correspondente é empurrado quando um contexto de requisição é empurrado.