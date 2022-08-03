# O Contexto da Aplicação


O contexto da aplicação preserva o rasto de dados do nível de aplicação durante uma requisição, comando de interface de linha de comando, ou outra atividade. No lugar da passagem da aplicação em volta de cada função, as delegações [**`current_app`**](#) e [**`g`**](#) são acessadas.

Isto é semelhante ao [O Contexto da Requisição](#), a qual preserva o rasto de dados do nível da requisição durante uma requisição. O contexto da aplicação correspondente é empurrado quando um contexto de requisição é empurrado.

## O Propósito do Contexto

O objeto de aplicação [**`Flask`**](#) possui atributos, tais como [**`config`**](#), que são úteis para acessar dentro de apresentações e [comandos de interface de linha de comando](#). No entanto, a importação da instância `app` dentro de módulos no teu projeto está propensa a problemas de importação circular. Quando estiver utilizando o [padrão de fábrica de aplicação](#) ou escrevendo [estruturas](#) ou [extensões](#) reutilizáveis não haverá nenhuma instância `app` para importar.

A Flask resolve este problema com o *contexto de aplicação*. No lugar de referir-se a uma `app` diretamente, tu utilizas a delegação [**`current_app`**](#), a qual aponta para a aplicação manipulando a atividade atual.

A Flask *empurra* automaticamente um contexto de aplicação quando estiveres manipulando uma requisição. As funções de apresentação, manipuladores de erro, outras funções que executam durante uma requisição terão acesso ao [**`current_app`**](#).

A Flask irá automaticamente empurrar um contexto de aplicação quando estiveres executando comandos de interface de linha de comando registados com a [**`Flask.cli`**] utilizando a `@app.cli.command()`.


## O Tempo de Vida do Contexto

O contexto de aplicação é criado e destruído quando necessário. Quando uma aplicação Flask começar a manipulação de uma requisição, ela empurra um contexto de aplicação e um [contexto de requisição](#). Quando a requisição terminar ela passa o contexto da requisição e depois o contexto da aplicação. Normalmente, um contexto de aplicação terá o mesmo tempo de vida de um uma requisição.

Consulte [O Contexto de Requisição](#) para mais informações sobre como os contextos funcionam e o ciclo de vida completo de uma requisição.


## Empurrar um Contexto Manualmente

Se tentares acessar a [**`current_app`**](#), ou qualquer coisa que a utilize, fora de um contexto de aplicação, receberás esta mensagem de erro:

```
RuntimeError: Working outside of application context.

This typically means that you attempted to use functionality that
needed to interface with the current application object in some way.
To solve this, set up an application context with app.app_context().
```

Se veres este erro enquanto estiveres configurando a tua aplicação, tal como quando estiveres inicializando uma extensão, tu podes empurrar um contexto manualmente desde tenhas acesso direto ao `app`. Utilize [**`app_context()`**](#) com um bloco `with`, e tudo que executares dentro do bloco terá acesso ao [**`current_app`**](#).

```py
def create_app():
    app = Flask(__name__)

    with app.app_context():
        init_db()

    return app
```

Se veres aquele erro em algum lugar no teu código que não está relacionado a configuração da aplicação, muito provavelmente indica que deves mover aquele código para dentro de uma função de apresentação ou comando de interface de linha de comando.