# O Contexto de Requisição


O contexto de requisição mantêm o rastreio de dados no nível da requisição durante uma requisição. No lugar da passagem do objeto de requisição para cada função que executa durante uma requisição, as delegações [**`request`**](#) e [**`session`**](#) são acessadas.

Isto é semelhante ao [O Contexto da Aplicação](14-the-application-context.md), o qual mantêm o rastreio de dados no nível de aplicação independente de uma requisição. Um contexto de aplicação correspondente é empurrado quando um contexto de requisição é empurrado.