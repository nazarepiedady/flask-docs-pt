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


## Armazenando de Dados

O contexto de aplicação é um bom lugar para guardar dados comuns durante uma requisição ou comando de interface de linha de comando. A Flask fornece o objeto [**`g`**](#) para este propósito. É um simples objeto de nome reservado que tem o mesmo tempo de vida de um contexto de aplicação.

---

### Note:

O nome `g` significa “global”, mas esta está referindo-se aos dados que são globais *dentro de um contexto*. Os dados em `g` são perdidos depois do contexto terminar, e não é um lugar apropriado para guardar dados entre requisições. Utilize a [**`session`**](#) ou uma base de dados para guardar os dados através das requisições.

---

Um uso comum para [**`g`**](#) é de gerir recursos durante uma requisição.

1. `get_X()` cria o recurso `X` se ele não existir, cacheando ele como `g.X`.
2. `teardown_X()` fecha ou de outro modo desloca o recurso se ele existir. Está registado como um manipulador de [**`teardown_appcontext()`**](#).

Por exemplo, tu podes gerir uma conexão de base de dado utilizando este padrão:

```py
from flask import g

def get_db():
    if 'db' not in g:
      g.db = connect_to_database()

    return g.db

@app.teardown_appcontext
def teardown_db(exception):
    db = g.pop('db', None)

    if db is not None:
        db.close()
```

Durante uma requisição, todas chamadas para `get_db()` retornará a mesma conexão, será automaticamente fechada no final da requisição.

Tu podes utilizar [**`LocalProxy`**] para produzir um novo contexto local a partir de `get_db()`:

```py
from werkzeug.local import LocalProxy
db = LocalProxy(get_db)
```

O acesso ao `db` chamará `get_db` internamente, da mesma maneira que [**`current_app`**](#) funciona.

---

Se estiveres escrevendo uma extensão, [**`g`**](#) deve ser reservado para o código do utilizador. Tu podes guardar dados internos no próprio contexto, mas certifique-se de utilizar um nome suficientemente único. O contexto atual é acessado com [**`_app_ctx_stack.top`**](#). Para mais informações consulte [Desenvolvimento de Extensão de Flask](#).