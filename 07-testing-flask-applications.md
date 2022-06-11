# Testando Aplicações Flask

**O que não estiver testado, está quebrado**

A origem desta citação é desconhecida e enquanto que não é inteiramente verdadeiro, também não está longe da verdade. Aplicações que não são testadas fazem com que seja difícil de melhorar o código existente e desenvolvedores de aplicações que não usam testes tendem a se tornar muito paranoicos. Se uma aplicação faz uso de testes automatizados, você pode seguramente fazer mudanças e instantaneamente saber se alguma coisa quebrou.

Flask fornece uma maneira de testar sua aplicação através da exposição do [cliente de teste do Werkzeug](https://werkzeug.palletsprojects.com/en/2.0.x/test/#werkzeug.test.Client) e manipula os contexto locais por você. Você pode então usa-lo com a sua solução de teste favorita.

Nesta documentação usaremos o pacote [pytest](https://docs.pytest.org/) como framework base para os nossos testes. Você pode instala-lo com o comando `pip`, como em:

```sh
$ pip install pytest
```


## A Aplicação

Primeiro, precisaremos de uma aplicação para testar; usaremos a aplicação construida na secção do [Tutorial](#tutorial). Se você ainda não tiver a aplicação, pegue o código-fonte dos [exemplos](https://github.com/pallets/flask/tree/2.0.1/examples/tutorial).

Só então é que podemos importar o módulo `flaskr` corretamente, precisamos executar `pip install -e .` dentro da pasta tutorial.


## O Esqueleto dos Testes

Nós começaremos por adicionar uma pasta de testes com o nome `tests` dentro da raiz da aplicação. Depois criar um ficheiro em Python com o nome `test_flaskr.py` para guardar os nossos testes. Quando formatamos o nome do ficheiro como `test_*.py`, ele será automaticamente alcançado pelo pytest.

A seguir, criamos um [pytest fixture](https://docs.pytest.org/en/latest/fixture.html) com nome de **`client`** que configura a aplicação para os testes e inicializa um novo banco de dados:

```py
import os
import tempfile

import pytest

from flaskr import create_app


@pytest.fixture
def client():
    db_fd, flaskr.app.config['DATABASE'] = tempfile.mkstemp()
    flaskr.app.config['TESTING'] = True

    with flaskr.app.test_client() as client:
        with flaskr.app.app_context():
            flaskr.init_db()
        yield client

    os.close(db_fd)
    os.unlink(flaskr.app.config['DATABASE'])
```

Esta fixture do cliente será chamada em cada teste individual. Isto dá-nos uma interface simples para aplicação, onde poderemos realizar requisições de teste para aplicação. O cliente também rasteará os cookies por nós.

Durante a configuração, a bandeira (flag) **`TESTING`** é ativada. O que isto faz é desativar a captura de erros durante a realização de uma requisição, assim você pode receber relatório de erros melhores quando estiver realizando requisições de teste contra a aplicação.

Por SQLite3 ser baseado no sistema de ficheiros, podemos facilmente usar o módulo [`tempfile`](https://docs.python.org/3/library/tempfile.html#module-tempfile) para criar um banco de dados temporário e inicializa-lo. A função [`mkstemp()`](https://docs.python.org/3/library/tempfile.html#tempfile.mkstemp) faz duas coisas por nós: ela retorna um manipulador de ficheiro de baixo nível e um nome de ficheiro aleatório, que por fim usamos como nome de banco de dados. Apenas temos que deixar o *db_fd* por perto, assim podemos usar a função [`os.close()`](https://docs.python.org/3/library/os.html#os.close) para fechar o ficheiro.

Para deletar o banco de dados depois do teste, o fixture fecha o ficheiro e remove-o do sistema de ficheiro.

Se agora executarmos a sequência de teste (test suite), veremos uma saída semelhante a que está abaixo:

```sh
$ pytest

================ test session starts ================
rootdir: ./flask/examples/flaskr, inifile: setup.cfg
collected 0 items

=========== no tests ran in 0.07 seconds ============
```

Apesar de não ter executado nenhum teste atual, já sabemos que nossa aplicação **`flaskr`** é sintaticamente valida, ou do contrário a importação teria morrido com uma exceção.


## O Primeiro Teste

Agora é o momento de começar a testar a funcionalidade da aplicação. Vamos verificar se a aplicação exibe o texto "No entries here so far" quando acessamos a raiz da aplicação (/). Para fazer isso, adicionamos uma nova função de teste como esta ao **`test_flaskr.py`**:

```py
def test_empty_db(client):
    """Start with a blank database."""

    rv = client.get('/')
    assert b'No entries here so far' in rv.data
```

Repare que nossas funções de testes começam com a palavra *test*; isto permite que o [pytest](https://docs.pytest.org/) identifique automaticamente a função como um teste a ser executado.

Ao usar o `client.get` podemos enviar uma requisição HTTP do tipo `GET` para aplicação no caminho dado. O valor retornado será um objeto **`response_class`**. Podemos agora usar o atributo [**`data`**](https://werkzeug.palletsprojects.com/en/1.0.x/wrappers/#werkzeug.wrappers.BaseResponse.data) para inspecionar o valor retornado (como string) da aplicação. Neste caso, nós garantimos que `'No entries here so far'` seja parte da saída.

Execute-o novamente e verás um teste passando:

```sh
$ pytest -v

================ test session starts ================
rootdir: ./flask/examples/flaskr, inifile: setup.cfg
collected 1 items

tests/test_flaskr.py::test_empty_db PASSED

============= 1 passed in 0.10 seconds ==============
```


## Iniciando e Terminando Sessão

A maioria das funcionalidades de nossa aplicação está somente disponível para o usuário administrador, sendo assim precisamos de uma maneira de permitir com que o nosso cliente de teste inicie e termine sessão em nossa aplicação. Para fazer isso, disparamos algumas requisições para as páginas login e logout com os dados obrigatórios do formulário (username e password). E pelas páginas de login e logout redirecionarem, dizemos ao cliente para prosseguir o redirecionamento configurando o atributo *follow_redirects* com valor verdadeiro.

Adiciona as seguintes funções dentro do seu ficheiro `test_flaskr.py`:

```py
def login(client, username, password):
    return client.post('/login', data=dict(
        username=username,
        password=password
    ), follow_redirects=True)


def logout(client):
    return client.get('/logout', follow_redirects=True)
```

Agora podemos testar facilmente que o inicio e termino de sessão funcionam e que falham com credenciais inválidas. Adiciona esta nova função de teste:

```py
def test_login_logout(client):
    """Make sure login and logout works."""

    username = flaskr.app.config["USERNAME"]
    password = flaskr.app.config["PASSWORD"]

    rv = login(client, username, password)
    assert b'You were logged in' in rv.data

    rv = logout(client)
    assert b'You were logged out' in rv.data

    rv = login(client, f"{username}x", password)
    assert b'Invalid username' in rv.data

    rv = login(client, username, f'{password}x')
    assert b'Invalid password' in rv.data
```


## Testar com Adição de Mensagens

Devemos também testar que a adição de mensagem funciona. Adiciona uma função de teste como está:

```py
def test_messages(client):
    """Test that messages work."""

    login(client, flaskr.app.config['USERNAME'], flaskr.app.config['PASSWORD'])
    rv = client.post('/add', data=dict(
        title='<Hello>',
        text='<strong>HTML</strong> allowed here'
    ), follow_redirects=True)
    assert b'No entries here so far' not in rv.data
    assert b'&lt;Hello&gt;' in rv.data
    assert b'<strong>HTML</strong> allowed here' in rv.data
```

Aqui verificamos que o código HTML é permitido no texto, mas não no título, o que é o comportamento esperado.

Agora ao executar deve devolver-nos três testes passando:

```sh
$ pytest -v

================ test session starts ================
rootdir: ./flask/examples/flaskr, inifile: setup.cfg
collected 3 items

tests/test_flaskr.py::test_empty_db PASSED
tests/test_flaskr.py::test_login_logout PASSED
tests/test_flaskr.py::test_messages PASSED

============= 3 passed in 0.23 seconds ==============
```


## Outras Técnicas de Testes

Além de usar o cliente de teste como é exibido acima, há também o método **`test_request_context()`** que pode ser usado combinado com o declaração `with` para ativar uma contexto de requisição temporariamente. Com isto você pode acessar os objetos **`request`**, **`g`** e **`session`** como nas funções de apresentação (views). Aqui está um exemplo completo que demonstra o uso desta técnica:

```py
import flask

app = flask.Flask(__name__)

with app.test_request_context('/?name=Peter'):
    assert flask.request.path == '/'
    assert flask.request.args['name'] == 'Peter'
```

Todos os outros objetos que estão relacionados ao contexto podem ser usados da mesma maneira.

Se você quiser testar sua aplicação usando configurações diferentes e parecer não haver uma boa maneira de faze-lo, considere a possibilidade de mudar a fábricas de aplicação (veja [Fábricas de Aplicação](#fábricas-de-aplicação)).

Perceba, todavia se você estiver usando um contexto de requisição de teste, as funções **`before_request()`** e **`after_request()`** não são chamados automaticamente. Contudo as funções **`teardown_request()`** são de fato executadas quando o contexto de requisição de teste deixa o bloco `with`. Se você quiser que as funções **`before_request()`** sejam executadas também, você precisa executar você mesmo o **`preprocess_request()`**:

```py
app = flask.Flask(__name__)

with app.test_request_context('/?name=Peter'):
    app.preprocess_request()
    ...
```

Pode ser necessário para isto, abrir conexões com banco de dados ou algo similar dependendo de como a sua aplicação foi desenhada.

Se você quiser executar as funções **`after_request()`** você precisa chama-las dentro do **`process_response()`** que todavia requer que você passe-o um objeto de resposta:

```py
app = flask.Flask(__name__)

with app.test_request_context('/?name=Peter'):
    resp = Response('...')
    resp = app.process_response(resp)
    ...
```

Em geral isto é menos útil porque até aquele momento você pode de maneira direta começar a usar o cliente de teste.


## Contexto e Recursos Fantasmas

* Relatório de Mudança
    * Novo a partir da versão 0.10

Um padrão muito comum é guardar informações de autorização do usuário e conexões com o banco de dados no contexto da aplicação ou no objeto **`flask.g`**. O principal padrão para se alcançar isso é pôr lá o objeto em primeiro momento de uso e depois remove-lo numa teardown. Imagine por algo momento este código para pegar o usuário atual:

```py
def get_user():
    user = getattr(g, 'user', None)
    if user is None:
        user = fetch_current_user_from_database()
        g.user = user
    return user
```

Para um teste seria bom sobrescrever este usuário a partir de fora sem ter que mudar código algum. Isto pode ser alcançado encaixando o sinal **`flask.appcontext_pushed`**:

```py
from contextlib import contextmanager
from flask import appcontext_pushed, g

@contextmanager
def user_set(app, user):
    def handler(sender, **kwargs):
        g.user = user
    with appcontext_pushed.connected_to(handler, app):
        yield
```

E depois usa-lo:

```py
from flask import json, jsonify

@app.route('/users/me')
def users_me():
    return jsonify(username=g.user.username)

with user_set(app, my_user):
    with app.test_client() as c:
        resp = c.get('/users/me')
        data = json.loads(resp.data)
        assert data['username'] == my_user.username
```


## Mantendo o Contexto Por Perto

* Relatório de Mudança
    * Novo a partir da versão 0.4

Algumas vezes é útil acionar uma requisição comum porém todavia manter o contexto por perto por mais tempo para que uma introspeção adicional possa acontecer. Com o Flask 0.4 isso é possível através do método **`test_client()`** com um bloco `with`:

```py
app = flask.Flask(__name__)

with app.test_client() as c:
    rv = c.get('/?tequila=42')
    assert request.args['tequila'] == '42'
```

Se você estivesse usando apenas o **`test_client`** sem o bloco `with`, o `assert` falharia com um erro porque *request* não se encontra mais disponível (porque você está tentando usa-lo fora da requisição atual).


## Acessando e Modificando Sessões

* Relatório de Mudança
    * Novo a partir da versão 0.8

Algumas vezes pode ser muito útil acessar ou modificar as sessões a partir do cliente de teste. Geralmente existem duas maneiras de fazer isso. Se você quiser apenas garantir que a sessão tem certas chaves para certos valores, você pode apenas manter o contexto por perto e acessar **`flask.session`**:

```py
with app.test_client() as c:
    rv = c.get('/')
    assert flask.session['foo'] == 42
```

Isto contudo não torna-o possível modificar ou acessar a sessão antes que uma requisição seja acionada. Desde a versão 0.8 do Flask passamos a fornecer o que é chamado de "transação de sessão" que simula as chamadas apropriadas para abertura de uma sessão dentro do contexto do cliente de teste e modifica-lo. Ao final da transação a sessão está guardada e pronta para ser usada pelo cliente de teste. Isto funciona independentemente da sessão usada no backend:

```py
with app.test_client() as c:
    with c.session_transaction() as sess:
        sess['a_key'] = 'a value'

    # uma vez que isto estiver alcançado a sessão foi guardada e está pronta para ser usada pelo cliente
    c.get(...)
```

Repare que neste caso, você tem de usar o objeto `sess` ao invés do proxy **`flask.session`**. O objeto contudo ele mesmo fornecerá a mesma interface.


## Testando APIS em JSON

* Relatório de Mudança
    * Novo a partir da versão 1.0

O Flask tem grande suporte para JSON, que é uma escolha popular para construção de APIS em JSON. Fazer requisições em JSON e examinar o JSON retornado pela resposta é algo muito conveniente:

```py
from flask import request, jsonify

@app.route('/api/auth')
def auth():
    json_data = request.get_json()
    email = json_data['email']
    password = json_data['password']
    return jsonify(token=generate_token(email, password))

with app.test_client() as c:
    rv = c.post('/api/auth', json={
        'email': 'flask@example.com', 'password': 'secret'
    })
    json_data = rv.get_json()
    assert verify_token(email, json_data['token'])
```

Passando o argumento `json` dentro do método do cliente de teste configura o dado requisitado para objeto JSON serializado e configura o tipo de conteúdo para `application/json`. Você recebe JSON a partir da requisição ou resposta através do método `get_json`.


## Testando Comandos da CLI

O Click vem com utilitários para testar seus comandos da CLI. Um **`CliRunner`** executa os comandos de forma insolada e captura suas saídas dentro de um objeto **`Result`**.

O Flask fornece o **`test_cli_runner()`** para criar um **`FlaskCliRunner`** que passa a aplicação Flask para a CLI automaticamente. Use seu método **`invoke()`** para chamar comandos da mesma maneira que seriam chamados a partir da linha de comando.

```py
import click

@app.cli.command('hello')
@click.option('--name', default='World')
def hello_command(name):
    click.echo(f'Hello, {name}!')

def test_hello():
    runner = app.test_cli_runner()

    # invoca o comando diretamente
    result = runner.invoke(hello_command, ['--name', 'Flask'])
    assert 'Hello, Flask' in result.output

    # ou pelo seu nome
    result = runner.invoke(args=['hello'])
    assert 'World' in result.output
```

No exemplo acima, é útil invocar o comando pelo nome porque ele verifica que o comando foi corretamente registado com a aplicação.

Se você quiser testar como o seu comando parsea os parâmetros, sem executar o comando, use seu método **`make_context()`**. Isto é útil para testes de validações complexas e tipos personalizados.

```py
def upper(ctx, param, value):
    if value is not None:
        return value.upper()

@app.cli.command('hello')
@click.option('--name', default='World', callback=upper)
def hello_command(name):
    click.echo(f'Hello, {name}!')

def test_hello_params():
    context = hello_command.make_context('hello', ['--name', 'flask'])
    assert context.params['name'] == 'FLASK'
```