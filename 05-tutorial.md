# Tutorial

## Estrutura de Projeto

Crie uma pasta para o projeto e entre nela:

```sh
$ mkdir flask-tutorial
$ cd flask-tutorial
```

Depois siga as [instruções de instalação](#instalação) para configurar um ambiente virtual em Python e instalar o Flask para o seu projeto.

O tutorial assumirá que você está trabalhando a partir da pasta `flask-tutorial` a partir de agora. O nome de ficheiro em cima de cada bloco de código são relativos a essa pasta.

---

Uma aplicação Flask que pode ser tão simples quanto um único ficheiro.

`hello.py`
```py
from flask import Flask

app = Flask(__name__)


@app.route('/')
def hello():
    return 'Hello, World!'
```

Todavia, como um projeto vai ficando maior, se torna insustentável, manter todo código em um único ficheiro. Projetos em Python usam *pacotes* para organizar código dentro de vários módulos que pode ser importados onde é necessitado e o tutorial fará isso também.

A pasta do projeto conterá:

* `flaskr/`, um pacote Python contendo o código e arquivos da sua aplicação.
* `tests/`, uma pasta contendo módulos de testes.
* `venv/`, um ambiente virtual em Python onde o Flask e outras dependências está instaladas.
* Ficheiros de instalação dizem ao Python como instalar o seu projeto.
* Configuração para controle de versão, tal como [git](https://git-scm.com/). Você deveria tornar um habito o uso algum tipo de controle de versão para todos os seus projetos, independente do seu tamanho.
* Quaisquer outros arquivos do projeto que você poderá adicionar no futuro.

Por fim, a estrutura do seu projeto parecerá com isso:

```
/home/user/Projects/flask-tutorial
├── flaskr/
│   ├── __init__.py
│   ├── db.py
│   ├── schema.sql
│   ├── auth.py
│   ├── blog.py
│   ├── templates/
│   │   ├── base.html
│   │   ├── auth/
│   │   │   ├── login.html
│   │   │   └── register.html
│   │   └── blog/
│   │       ├── create.html
│   │       ├── index.html
│   │       └── update.html
│   └── static/
│       └── style.css
├── tests/
│   ├── conftest.py
│   ├── data.sql
│   ├── test_factory.py
│   ├── test_db.py
│   ├── test_auth.py
│   └── test_blog.py
├── venv/
├── setup.py
└── MANIFEST.in
```

Se você estiver usando um controle de versão, os arquivos a seguir que são gerados durante a execução do seu projeto deveriam ser ignorados. Deve haver outros arquivos baseados no editor que estiver a usar. Em geral, ignore arquivos que você não escreveu. Por exemplo, com o git:

`.gitignore`
```
venv/

*.pyc
__pycache__/

instance/

.pytest_cache/
.coverage
htmlcov/

dist/
build/
*.egg-info/
```

Siga para [Configuração da Aplicação](#configuração-da-aplicação).


## Configuração da Aplicação

Uma aplicação Flask é uma instância da classe **`Flask`**. Tudo que diz respeito a aplicação, tal como a configuração e URLs, serão registadas com essa classe.

A mais simples de criar uma aplicação Flask é criar uma instância global diretamente no topo do seu código, tipo como é feito no exemplo "Hello, World!" da secção anterior. Enquanto isso é simples e útil em alguns casos, ele pode causar alguns problemas difíceis a medida que o projeto vai crescendo.

Ao invés de criar uma instância do **`Flask`** globalmente, você irá cria-lo dentro de uma função. Esta função é conhecida como a fabrica da aplicação (application factory). Qualquer configuração, registo, ou outra configuração que a aplicação precise irá acontecer dentro da função, depois a aplicação será retornada.

### A Fabrica da Aplicação

É hora de começar a codificar! Crie a pasta `flaskr` e adiciona o ficheiro `__init__.py`. O `__init__.py` serve a duas responsabilidades: ela irá conter a fabrica da aplicação, e ele dirá ao Python que a pasta `flaskr` deve ser tratada como um pacote.

```sh
$ mkdir flaskr
```

`flaskr/__init__.py`

```py
import os

from flask import Flask


def create_app(test_config=None):
    # cria e configura a aplicação
    app = Flask(__name__, instance_relative_config=True)
    app.config.from_mapping(
        SECRET_KEY='dev',
        DATABASE=os.path.join(app.instance_path, 'flaskr.sqlite'),
    )

    if test_config is None:
        # carrega a configuração da instância, se ela existir, quando não hover testes
        app.config.from_pyfile('config.py', silent=True)
    else:
        # carrega a configuração do teste se ele for passado
        app.config.from_mapping(test_config)

    # garantir a existência da pasta da instância
    try:
        os.makedirs(app.instance_path)
    except OSError:
        pass

    # um página simples que diz 'hello'
    @app.route('/hello')
    def hello():
        return 'Hello, World!'

    return app
```

`create_app` é a função de fabrico de aplicação. Você irá adicionar mas coisas depois no tutorial, mas por agora ele já faz muita coisa.

1. `app = Flask(__name__, instance_relative_config=True)` cria a instância **`Flask`**.
    * `__name__` é o nome do módulo atual do Python. O app precisa saber onde está localizado para configurar alguns caminhos, e `__name__` é uma maneira conveniente de dizer isso a ele.

    * `instance_relative_config=True` diz a app que as arquivos de configuração são relativo a [pasta da instância](#pasta-da-instância). A pasta da instância se encontra fora do pacote `flaskr` e pode segurar dados locais que não deveriam ser consolidados pelo controlo de versão, tal como configurações secretas e o ficheiro do banco de dados.

2. **`app.config.from_mapping()`** configura alguma configuração padrão que a app usará:
    * **`SECRET_KEY`** é usado pelo Flask e suas extensões para manter os dados em segurança. Está configurado para `dev` para fornecer um valor conveniente durante o desenvolvimento, mas ele deve ser sobrescrito com um valor aleatório quando estiver instalando.

    * `DATABASE` é o caminho onde o ficheiro do banco de dados SQLite será salvo. Está de baixo de `app.instance_path`, que é o caminho que o Flask escolheu para a pasta da instância. Você aprenderá mais sobre o banco de dados na próxima secção.

3. **`app.config.from_pyfile()`** sobrescreve as configurações padrão com valores dadas pelo ficheiro `config.py` na pasta da instância se ela existir. Por exemplo, quando instalando, isso pode ser usado para configurar uma `SECRET_KEY` real.
    * `test_config` pode também ser passado para a fabrica, e será usado ao invés da instância de configuração. Isto é, os testes que você escreverá depois no tutorial podem ser configurado independentemente de quaisquer valores de desenvolvimento que você tem configurado.

4. **`os.makedirs()`** garante que **`app.instance_path`** exista. O Flask não cria a pasta da instância automaticamente, mas ela precisa ser criada porque seu projeto irá criar o ficheiro do banco de dados SQLite lá.

5. **`@app.route()`** cria uma simples rotas então você pode ver a aplicação trabalhando antes de ter contacto com o resto do tutorial. Ele cria uma conexão entre a URL `/hello` e uma função que retorna uma resposta, neste caso a string(texto) `Hello, World!`.


### Executar a Aplicação

Agora você pode executar a sua aplicação usando o comando `flask`. A partir do terminal, diz ao Flask onde encontrar a sua aplicação, depois executa ela no modo de desenvolvimento. Lembre, você deve estar no nível superior da pasta `flask-tutorial`, não no pacote `flaskr`.

O modo de desenvolvimento exibe um depurador interativo sempre que a página levantar uma exceção, e reinicia o servidor sempre que você fizer uma mudança no código. Você pode deixar ele executando e apenas recarregar a página do browser enquanto você segue o tutorial:

No Linux e Mac:

```sh
$ export FLASK_APP=flaskr
$ export FLASK_ENV=development
$ flask run
```

No linha de comando do Windows, usa `set` ao invés de `export`:

```sh
> set FLASK_APP=flaskr
> set FLASK_ENV=development
> flask run
```

No PowerSheell do Windows, use `$env:` ao invés de `export`:

```sh
> $env:FLASK_APP = "flaskr"
> $env:FLASK_ENV = "development"
> flask run
```

Você verá um saída parecida a esta:

```sh
* Serving Flask app "flaskr"
* Environment: development
* Debug mode: on
* Running on http://127.0.0.1:5000/ (Press CTRL+C to quit)
* Restarting with stat
* Debugger is active!
* Debugger PIN: 855-212-761
```

Visite http://127.0.0.1:5000/hello em um navegador e você deve visualizar a mensagem "Hello, World!". Parabéns, você está agora executando a sua aplicação web em Flask.

Siga para [Definir e Acessar o Banco de Dados](#definir-e-acessar-o-banco-de-dados).

## Definir e Acessar o Banco de Dados

A aplicação usará o banco de dados [SQLite](https://sqlite.org/about.html) para armazenar os dados dos usuários e suas publicações. O Python vem com suporte ao SQLite no módulo **`sqlite3`**.

O SQLite é conveniente porque não requer configuração de um servidor de banco de dados separados e está embutido no Python. De qualquer forma, se as requisições concorrentes tentarem escrever no banco de dados ao mesmo tempo, elas diminuirão a velocidade, pois cada escrita acontece sequencialmente. Pequenas aplicações não notar isso. Depois de se tornar grande você, você pode querer mudar para um banco de dados diferente.

O tutorial não irá entrar em detalhes sobre o SQL. Se você não estiver familiarizado com ela, a documentação do SQLite descreve a [linguagem](https://sqlite.org/lang.html).

### Conectar ao Banco de Dados

A primeira coisa a fazer quando estiver trabalhando com o banco de dados SQLite (e com a maioria das bibliotecas de banco de dados do Python) é criar uma conexão com ela. Quaisquer consultas e operações são desempenhadas usando a conexão, que é fechada depois do trabalho estiver terminado.

Em aplicações web essa conexão geralmente está ligada à requisição. Ela é criada em algum momento ao lidar com uma requisição e fechada antes que a resposta seja enviada.

`flaskr/db.py`
```py
import sqlite3

import click
from flask import current_app, g
from flask.cli import with_appcontext


def get_db():
    if 'db' not in g:
        g.db = sqlite3.connect(
            current_app.config['DATABASE'],
            detect_types=sqlite3.PARSE_DECLTYPES
        )
        g.db.row_factory = sqlite3.Row

    return g.db


def close_db(e=None):
    db = g.pop('db', None)

    if db is not None:
        db.close()
```

**`g`** é um objeto especial que é único para cada requisição. Ele é usado para armazenar os dados que podem vir ser acessados por várias funções durante a requisição. A conexão é armazenada e re-usada para não ter que criar uma conexão nova se **`get_db`** for chamado uma segunda vez na mesma requisição.

**`current_app`** é outro objeto especial que aponta para manipulador de requisição da aplicação Flask. Desde que você usou uma fábrica de aplicação, não há um objeto de aplicação ao escrever o resto do seu código. `get_db` será chamado quando a aplicação tiver sido criada e estiver manipulando uma requisição, então **`current_app`** pode ser usado.

**`sqlite3.Row`** diz a conexão para retornar linhas que se comportem como dicionários. Isso permite acessar as colunas pelo nome.

`close_db` verifica se uma conexão foi criada, verificando se `g.db` foi configurado. Se a conexão existir, ela é fechada. Além disso você informará a sua aplicação que sobre a função `close_db` na fábrica da aplicação para que seja ela executada a cada requisição.

### Criar as Tabelas

No SQLite, os dados são armazenados em *tabelas* e *colunas*. Estes precisam ser criados antes de você poder guardar e recuperar dados. Flaskr armazenará os usuários na tabela `user`, e as publicações na tabela `post`. Crie um ficheiro com os comandos SQL necessários para criar tabelas vazias:

`flaskr/schema.sql`
```sql
DROP TABLE IF EXISTS user;
DROP TABLE IF EXISTS post;

CREATE TABLE user (
  id INTEGER PRIMARY KEY AUTOINCREMENT,
  username TEXT UNIQUE NOT NULL,
  password TEXT NOT NULL
);

CREATE TABLE post (
  id INTEGER PRIMARY KEY AUTOINCREMENT,
  author_id INTEGER NOT NULL,
  created TIMESTAMP NOT NULL DEFAULT CURRENT_TIMESTAMP,
  title TEXT NOT NULL,
  body TEXT NOT NULL,
  FOREIGN KEY (author_id) REFERENCES user (id)
);
```

Adicione uma função Python que executará esses comandos SQL ao ficheiro `db.py`:

`flaskr/db.py`
```py
def init_db():
    db = get_db()

    with current_app.open_resource('schema.sql') as f:
        db.executescript(f.read().decode('utf8'))


@click.command('init-db')
@with_appcontext
def init_db_command():
    """Eliminar os dados existentes e crie novas tabelas."""
    init_db()
    click.echo('Initialized the database.')
```

**`open_resource()`** abre um ficheiro relativo ao pacote `flaskr`, o que é útil, pois você não necessariamente saberá onde esse local é ao implementar a sua aplicação depois. `get_db` retorna uma conexão com o banco de dados, que é usada para executar os comandos lidos a partir do ficheiro.

**`click.command()`** define um comando de linha de comando com o nome `init-db` que chama a função `init_db` e mostra uma mensagem de sucesso para o usuário. Você pode ler [Interface de Linha de Comando](#interface-de-linha-de-comando) para aprender mais sobre a escrita de comandos.


### Registar com a Aplicação

As funções `close_db` e `init_db_command` precisam ser registadas com a instância da aplicação; senão, eles não serão usados pela aplicação. No entanto, já que você está usando uma função de fabricação, essa instância não está disponível quando estiver escrevendo as funções. Em vez disso, escreva uma função que receba uma aplicação e faça o registo.

`flaskr/db.py`

```py
def init_app(app):
    app.teardown_appcontext(close_db)
    app.cli.add_command(init_db_command)
```

**`app.teardown_appcontext()`** diz ao Flask para chamar aquela função quando estiver limpo depois de retornar a resposta.

**`app.cli.add_command()`** adiciona um novo comando que pode ser chamado com o comando `flask`.

Importa e chame essa função da fábrica. Coloque o novo código no final da função de fabricação antes de retornar a aplicação (o `app`).

`flaskr/__init__.py`

```py
def create_app():
    app = ...
    # código existente omitido

    from . import db
    db.init_app(app)

    return app

```

### Inicializar o Arquivo de Banco de Dados

Agora que `init-db` já foi registado com a aplicação, ele pode ser chamado usando o comando `flask`, similar ao comando `run` das secções anteriores.

> ## Nota:
> Se continua executando o servidor a partir das secções anteriores, você pode parar o servidor, ou executar esse comando em um novo terminal. Se você usar um novo terminal, lembrasse de mudar para a pasta do seu projeto e ativar o ambiente como descrito em [Ativar o Ambiente](#ativar-o-ambiente). Você também precisará configurar o `FLASK_APP`e o `FLASK_ENV` como mostrado nas secções anteriores.

Execute o comando `init-db`

```sh
$ flask init-db
Initialized the database.
```

Agora passará a existir um ficheiro com o nome `flaskr.sqlite` dentro da pasta `instance` no seu projeto.

Continue para [Estruturas (Blueprints) e Apresentações (Views)](#estruturas-(blueprints)-e-apresentações-(views)).


## Estruturas (Blueprints) e Apresentações (Views)

Uma função de apresentação é o código que você escreve para responder a requisições para sua aplicação. O Flask usa um padrão para corresponder a URL da requisição de entrada para uma apresentação que deve lidar com isso. A apresentação retorna dados que o Flask transforma em uma resposta de saída. O Flask também pode ir em outra direção e gerar uma URL para uma apresentação baseada em seu nome e argumentos.

### Criar uma Estrutura (Blueprint)

Uma **`Estrutura (Blueprint)`** é uma maneira de organizar um grupo de apresentações relacionadas e outros códigos. Em vez de registrar as apresentações e outros códigos diretamente com uma aplicação, eles são registados com uma estrutura. Em seguida, a estrutura é registada com a aplicação quando estiver disponível na função de fabricação.

O Flaskr terá duas estruturas, uma para funções de autenticação e uma para as funções de publicações do blogue. O código de cada estrutura irá em um módulo separado. Já que o blogue precisa conhecer a autenticidade, você escreverá a autenticação primeiro.

`flaskr/auth.py`

```py
import functools

from flask import (
    Blueprint, flash, g, redirect, render_template, request, session, url_for
)
from werkzeug.security import check_password_hash, generate_password_hash

from flaskr.db import get_db

bp = Blueprint('auth', __name__, url_prefix='/auth')
```

Isso cria uma **`Estrutura (Blueprint)`** com nome `'auth'`. Tal como o objeto da aplicação, a estrutura precisa saber onde está definida, então `__name__` é passado como segundo argumento. O `url_prefix` estará preparado para todas URL associadas com a estrutura.

Importa e regista a estrutura da fábrica usando **`app.register_blueprint()`**. Coloque o código novo no final da função de fabricação antes de retornar a aplicação(app).

`flaskr/__init__.py`

```py
def create_app():
    app = ...
    # código existente omitido

    from . import auth
    app.register_blueprint(auth.bp)

    return app
```

A estrutura de autenticação terá apresentações para registar novos usuários e para entrar e para sair.

### A Primeira Apresentação: Register (registar)

Quando os usuários visitarem a URL `/auth/register`, a view `register` retornará o HTML com um formulário para eles preencherem. Quando eles submeterem o formulário, ele validará sua entrada e ou mostra formulário com uma mensagem de erro ou cria um novo usuário e segue para a página de entrada (login).

Por agora você irá apenas escrever o código da view. Na proxima página, você escreverá templates para gerar o formulário em HTML.

`flaskr/auth.py`

```py
@bp.route('/register', methods=('GET', 'POST'))
def register():
    if request.method == 'POST':
        username = request.form['username']
        password = request.form['password']
        db = get_db()
        error = None

        if not username:
            error = 'Username is required.'
        elif not password:
            error = 'Password is required.'
        elif db.execute(
            'SELECT id FROM user WHERE username = ?', (username,)
        ).fetchone() is not None:
            error = 'User {} is already registered.'.format(username)

        if error is None:
            db.execute(
                'INSERT INTO user (username, password) VALUES (?, ?)',
                (username, generate_password_hash(password))
            )
            db.commit()
            return redirect(url_for('auth.login'))

        flash(error)

    return render_template('auth/register.html')
```

Aqui está o que a função da apresentação `register` está fazendo:

1. **`@bp.route`** associa a URL `/register` a função da view `register`. Quando o Flask recebe uma requisição para `/auth/register`, ele chamará a view `register` e usa o valor retornado como resposta.

2. Se o usuário submeteu o formulário, **`request.method`** será `POST`. Neste caso, começa validando a entrada.

3. **`request.form`** é um tipo de mapeamento de dicionário especial de chaves e valores do formulário submetido. Os usuários entrarão com seus `username` e `password`.

4. Faz a validação para ter a certeza de que o `username` e `password` não estão vazios.

5. Faz a validação para saber se o `username` já não está registado, consultando o banco de dados e verificando se um resultado é retornado. **`db.execute`** pega uma consulta SQL com espaços reservados por `?` para qualquer entrada do usuário, e uma tupla de valores para substituir os espaços reservados. A biblioteca do banco de dados cuidará do escapamento dos valores, então você não está vulnerável a ataques de injeção de código SQL (SQL injection attack).

6. Se a validação for bem-sucedida, insere os dados do novo usuário dentro do banco de dados. Para segurança, senhas nunca devem ser armazenadas diretamente em um banco de dados. Ao invés disso, [**`generate_password_hash()`**](https://werkzeug.palletsprojects.com/en/1.0.x/utils/#werkzeug.security.generate_password_hash) é usado para seguramente criptografar a senha, e esse valor criptografado é armazenado. Como, essa consulta modifica dados, o [**`db.commit()`**](https://docs.python.org/3/library/sqlite3.html#sqlite3.Connection.commit) precisa ser chamado logo em seguida para salvar as alterações.

7. Depois de armazenar os usuários, eles são redirecionados para a página de entrada (login). [**`url_for()`**](#flask.url_for) gera a URL para a apresentação de entrada (login) baseado no seu nome.  Isso é preferível a escrever a URL diretamente, pois permite você alterar a URL mais tarde, sem alterar todo código que se vincula a ele. [**`redirect()`**](#flask.redirect) gera uma resposta de redirecionamento para a URL gerada.

8. Se a validação falhar, o erro é exibido aos usuários. [**`flash()`**](#flask.flash) armazena mensagens que pode ser recuperadas ao renderizar o modelo.

9. Quando os usuários inicialmente navegarem para `auth/register`, ou houve um erro de validação, um página HTML com o formulário de registo deve ser exibido. [**`render_template()`**] renderizará um template contendo o HTML, que você escreverá no próximo passo do tutorial.


#### Login

Essa views segue o mesmo padrão qua a view `register` acima.

`flaskr/auth.py`

```py
@bp.route('/login', methods=('GET', 'POST'))
def login():
    if request.method == 'POST':
        username = request.form['username']
        password = request.form['password']
        db = get_db()
        error = None
        user = db.execute(
            'SELECT * FROM user WHERE username = ?', (username,)
        ).fetchone()

        if user is None:
            error = 'Incorrect username.'
        elif not check_password_hash(user['password'], password):
            error = 'Incorrect password.'

        if error is None:
            session.clear()
            session['user_id'] = user['id']
            return redirect(url_for('index'))

        flash(error)

    return render_template('auth/login.html')
```

Existem algumas diferenças em relação a view `register`:

1. O usuário é consultado primeiro e armazenado em uma variável para usar mais tarde.

    - [**`fetchone()`**](https://docs.python.org/3/library/sqlite3.html#sqlite3.Cursor.fetchone) retorna um linha da consulta. Se a consulta não retornou nenhum resultado, ela retorna `None`.
    Depois, [**`fetchall()`**](https://docs.python.org/3/library/sqlite3.html#sqlite3.Cursor.fetchall) é usado, que retorna uma lista de todos os resultados.

2. [**`check_password_hash()`**](#https://werkzeug.palletsprojects.com/en/1.0.x/utils/#werkzeug.security.check_password_hash) mistura a senha enviada da mesma maneira que o senha misturada guardada e seguramente compara elas. Se eles se corresponderem, a senha é valida.

3. [**`session`**](#flask.session) é um [dicionário](https://docs.python.org/3/library/stdtypes.html#dict) que armazena dado enviado junto da requisição. Quando a validação for bem-sucedida, o `id` do usuário é armazenado em uma nova secção. O dado é armazenado em um *cookie* que é enviado para o navegador, e o navegador depois envia ele de volta já na proxima requisição. O Flask *assina* com segurança o dado para que não possa ser adulterado.


Agora que o `id` do usuário está guardado em [**`session`**](#flask.session), estará disponível nas próximas requisições. Ao iniciar cada requisição, se um usuário estiver iniciado, suas informações devem ser carregadas e disponibilizadas para outras apresentações.

`flaskr/auth.py`

```py
@bp.before_app_request
def load_logged_in_user():
    user_id = session.get('user_id')

    if user_id is None:
        g.user = None
    else:
        g.user = get_db().execute(
            'SELECT * FROM user WHERE id = ?', (user_id,)
        ).fetchone()
```

[**`bp.before_app_request()`**](#flask.Blueprint.before_app_request) regista uma função que executa antes da função de apresentação, não importa qual URL foi requisitada. `load_logged_in_user` verifica se um id de usuário está armazenado na [**`session`**](#flask.session) e pega esse dado do usuário do banco de dados, guarda-o em [**`g.user`**](#flask.g), que dura para o comprimento da requisição. Se não houver um `id` de usuário, ou se o `id` não existe, `g.user` será `None`.


#### Logout

Para terminar a secção, você precisa remover o id do usuário da [**`session`**](#flask.session). Assim `load_logged_in_user` não carregará um usuário na proxima requisição.

`flaskr/auth.py`

```py
@bp.route('/logout')
def logout():
    session.clear()
    return redirect(url_for('index'))
```

#### Exigir Autenticação em Outras Apresentações

Criar, editar, e eliminar publicações do blogue exigirá que o usuário esteja iniciado. Um *decorador* pode ser usado para verificar isso para cada apresentação ao qual for aplicado.

`flaskr/auth.py`

```py
def login_required(view):
    @functools.wraps(view)
    def wrapped_view(**kwargs):
        if g.user is None:
            return redirect(url_for('auth.login'))

        return view(**kwargs)

    return wrapped_view
```

Esse decorador retorna uma nova função de apresentação que envolve a apresentação original ao qual é aplicada. A nova função verifica se um usuário está carregado e ou ao contrário redireciona para a página de entrada (login). Se um usuário estiver carregado a apresentação original é chamada e continua normalmente. Você usará esse decorador ao escrever as apresentações do blogue.

### Endpoints e URLs

A função [**`url_for()`**](#flask.url_for) gera a URL para uma apresentação com base em um nome e argumentos. O nome associado a uma apresentação é também chamada de *endpoint (destino)*, e por padrão é o mesmo nome da função de apresentação.

Por exemplo, a apresentação `hello()` que foi adicionada a fábrica de aplicação mais cedo durante a aula prática tem o nome `'hello'` e pode ser ligado a `url_for('hello')`. Se ela receber um argumento, o qual você verá mais tarde, seria ligado ao uso de `url_for('hello', who='World')`.

Ao usar uma estrutura, o nome da estrutura é predefinido para o nome da função, então o destino para a função `login` que você escreveu acima é `auth.login` porque você adicionou ele a estrutura `auth`.

Siga para [Templates](#modelos-de-marcação).


## Modelos de Marcação

Você escreveu a apresentação de autenticação para sua aplicação, mas se está executando o servidor e tentar ir para quaisquer URLs, verá um erro `TemplateNotFound`. Isso porque as apresentações estão chamando [**`render_template()`**](#flask.render_template), porém você ainda não escreveu nenhum modelo de marcação. Os ficheiros de modelos de marcação serão guardados na pasta `templates` dentro do pacote `flaskr`.

Modelos de Marcação são ficheiros que contêm dados estáticos como também espaços reservados para dados dinâmicos. Um modelo de marcação é renderizado com dado especifico para produzir um documento final. O Flask usa a biblioteca de modelos de marcação [Jinja](#http://jinja.pocoo.org/docs/templates/) para renderizar modelos de marcação.

Em sua aplicação, você usará modelos de marcação para renderizar o [HTML](https://developer.mozilla.org/docs/Web/HTML) que será exibido no navegador do usuário. No Flask, o Jinja é configurado para auto-escapar qualquer dado que for renderizado nos modelos de marcação HTML. Isso significa que é seguro renderizar a entrada do usuário; quaisquer caracteres que eles entrarem que possa mexer com o HTML, tal como `<` e `>` serão escapados com valores *seguros* que se parecem com os mesmos no navegador mas sem causar efeitos colaterais.

O Jinja se parece e se comporta principalmente como Python. Delimitadores especiais são usados para diferenciar a sintaxe do Jinja do conteúdo estático no template. Qualquer coisa entre `{{` e `}}` é uma expressão que será emitida para o documento final. `{%` e `%}` denota uma declaração de controle de fluxo como `if` e `for`. Ao contrário do Python, blocos são denotados por tags iniciais e finais, em vez de indentação, já que o texto estático dentro de um bloco pode alterar a indentação.


### O Esquema Base

Cada página na aplicação terá o mesmo esquema básico em torno de um corpo diferente. Ao invés de escrever toda a estrutura de HTML em cada modelo de marcação, cada modelo de marcação estenderá um modelo de marcação básico e sobrescrever secções específicas.

`flaskr/templates/base.html`

```jinja
<!doctype html>
<title>{% block title %}{% endblock %} - Flaskr</title>
<link rel="stylesheet" href="{{ url_for('static', filename='style.css') }}">
<nav>
  <h1>Flaskr</h1>
  <ul>
    {% if g.user %}
      <li><span>{{ g.user['username'] }}</span>
      <li><a href="{{ url_for('auth.logout') }}">Log Out</a>
    {% else %}
      <li><a href="{{ url_for('auth.register') }}">Register</a>
      <li><a href="{{ url_for('auth.login') }}">Log In</a>
    {% endif %}
  </ul>
</nav>
<section class="content">
  <header>
    {% block header %}{% endblock %}
  </header>
  {% for message in get_flashed_messages() %}
    <div class="flash">{{ message }}</div>
  {% endfor %}
  {% block content %}{% endblock %}
</section>
```

[**`g`**](#flask.g) está automaticamente disponível nos  templates. Baseado em que se `g.user` estiver definido (a partir de `load_logged_in_user`), tanto o nome do usuário e uma ligação de saída (logout) são exibidos, ou ligações para registar e um para entrar (login) são exibidos. [**`url_for()`**](#flask.url_for) está também automaticamente disponível, e é usado para gerar URLs para as apresentações ao invés de escrever eles manualmente.

Depois do título da página, e antes do conteúdo, o modelo de marcação faz um loop em cada mensagem retornada por [**`get_flashed_messages()`**](#flask.get_flashed_messages). Você usou [**`flash()`**](#flask.flash) nas views para mostrar mensagens de erros, e este é o código que irá exibir eles.

Existem três blocos definidos aqui que serão sobrescritos em outros modelos de marcação:

1. `{% block title %}` mudará o título exibido na aba do navegador e título da janela.

2. `{% block header %}` é similar ao `title` porém irá mudar o título exibido na página.

3. `{% block content %}` é onde o conteúdo de cada página irá, tal como formulário de entrada (login) ou uma publicação do blogue.

O modelo de marcação básico está diretamente dentro da pasta `templates`. Para manter os outros organizados, os modelos de marcação para uma estrutura serão postos dentro de uma pasta com o mesmo nome que a estrutura.

### Register

`flaskr/templates/auth/register.html`

```jinja
{% extends 'base.html' %}

{% block header %}
  <h1>{% block title %}Register{% endblock %}</h1>
{% endblock %}

{% block content %}
  <form method="post">
    <label for="username">Username</label>
    <input name="username" id="username" required>
    <label for="password">Password</label>
    <input type="password" name="password" id="password" required>
    <input type="submit" value="Register">
  </form>
{% endblock %}
```

`{% extends 'base.html' %}` diz ao Jinja que este modelo de marcação deve substituir os blocos do modelo de marcação básico. Todo o conteúdo renderizado deve aparecer dentro das tags `{% block %}` que sobrescreve os blocos do modelo de marcação básico.

Um padrão útil usado aqui é pôr `{% block title %}` dentro de `{% block header %}`. Isto definirá o bloco de título e em seguida produzir o valor dele dentro do bloco de cabeçalho, assim ambos a janela e a página partilham o mesmo título sem ter escreve duas vezes.

O marcador `input` está usando aqui o atributo `required`. Isso diz ao navegador para não submeter o formulário até que aqueles campos estejam preenchidos. Se os usuários estiverem usando um navegador antigo que não suporta aquele atributo, ou se eles estiverem usando algo além do navegador para fazer requisições, você ainda deseja validar os dados na apresentação do Flask. É importante sempre validar completamente os dados no servidor, mesmo se o cliente fazer alguma validação também.

### Log In

Este é idêntico ao modelo de marcação `register` exceto para o título e botão de submissão.

`flaskr/templates/auth/login.html`

```jinja
{% extends 'base.html' %}

{% block header %}
  <h1>{% block title %}Log In{% endblock %}</h1>
{% endblock %}

{% block content %}
  <form method="post">
    <label for="username">Username</label>
    <input name="username" id="username" required>
    <label for="password">Password</label>
    <input type="password" name="password" id="password" required>
    <input type="submit" value="Log In">
  </form>
{% endblock %}
```

### Registar um Usuário

Agora que os modelos de marcação de autenticação estão escritos, você pode registar um usuário. Certifique-se de que o servidor ainda está executando (`flask run` se não estiver), depois siga para http://127.0.0.1:5000/auth/register.

Tente clicar no botão "Register" sem preencher o formulário e veja o que o navegador mostra uma mensagem de erro. Tente remover os atributos `required` do modelo de marcação `register.html` e clique de novo em "Register". Ao invés de o navegador mostrar um erro, a página recarregará e o erro do [**`flash()`**](#flask.flash) na apresentação será exibido.

Preencha o nome de usuário e senha e você será redirecionado para uma página de entrada (login). Tente inserir um nome de usuário incorreto, ou o nome de usuário correto e senha incorreta. Se você entrar, você receberá um erro porque ainda não existe uma apresentação `index` para qual redirecionar.

Continue para [Ficheiros Estáticos](#ficheiros-estáticos).

## Ficheiros Estáticos

As apresentações de autenticação e modelos de marcação funcionam, porém eles parecem muito plano por agora. Algum [CSS](https://developer.mozilla.org/docs/Web/CSS) pode ser adicionado para adicionar estilo ao esquema de HTML que você construiu. O estilo não irá mudar, assim é um ficheiro *estático* em vez de um template.

O Flask automaticamente adiciona uma apresentação `static` que pega um caminho relativo para pasta `flaskr/static` e servir ele. O modelo de marcação `base.html` já tem uma ligação para o ficheiro `style.css`:

```jinja
{{ url_for('static', filename='style.css') }}
```
Fora o CSS, outros tipos de ficheiros estáticos podem ser ficheiros com funções JavaScript, ou uma imagem com logótipo. Eles são todos colocados na pasta `flaskr/static` e referenciados com `url_for('static', filaname='...')`.

Esta aula prática não é focada em como escrever CSS, então pode simplesmente copiar o seguinte para o ficheiro `flaskr/static/style.css`:

`flaskr/static/style.css`

```css
html { font-family: sans-serif; background: #eee; padding: 1rem; }
body { max-width: 960px; margin: 0 auto; background: white; }
h1 { font-family: serif; color: #377ba8; margin: 1rem 0; }
a { color: #377ba8; }
hr { border: none; border-top: 1px solid lightgray; }
nav { background: lightgray; display: flex; align-items: center; padding: 0 0.5rem; }
nav h1 { flex: auto; margin: 0; }
nav h1 a { text-decoration: none; padding: 0.25rem 0.5rem; }
nav ul  { display: flex; list-style: none; margin: 0; padding: 0; }
nav ul li a, nav ul li span, header .action { display: block; padding: 0.5rem; }
.content { padding: 0 1rem 1rem; }
.content > header { border-bottom: 1px solid lightgray; display: flex; align-items: flex-end; }
.content > header h1 { flex: auto; margin: 1rem 0 0.25rem 0; }
.flash { margin: 1em 0; padding: 1em; background: #cae6f6; border: 1px solid #377ba8; }
.post > header { display: flex; align-items: flex-end; font-size: 0.85em; }
.post > header > div:first-of-type { flex: auto; }
.post > header h1 { font-size: 1.5em; margin-bottom: 0; }
.post .about { color: slategray; font-style: italic; }
.post .body { white-space: pre-line; }
.content:last-child { margin-bottom: 0; }
.content form { margin: 1em 0; display: flex; flex-direction: column; }
.content label { font-weight: bold; margin-bottom: 0.5em; }
.content input, .content textarea { margin-bottom: 1em; }
.content textarea { min-height: 12em; resize: vertical; }
input.danger { color: #cc2f2e; }
input[type=submit] { align-self: start; min-width: 10em; }
```

Você pode encontrar uma versão mais compacta do `style.css` em [código de exemplo](https://github.com/pallets/flask/tree/1.1.2/examples/tutorial/flaskr/static/style.css).

Vá para http://127.0.0.1:5000/auth/login e a página deve se parecer com captura de tela abaixo.

![página inicial](./static_files/flaskr_login.png)

Você pode ler mais sobre CSS na [documentação da Mozilla](https://developer.mozilla.org/docs/Web/CSS). Se você mudar um ficheiro estático, recarregue a página do navegador. Se a mudança não for exibida, tente limpar o cache do seu navegador.

Continue em [Estrutura de Blogue](#blueprint-do-blogue).


## Estrutura de Blogue

Você usará a mesma técnica que você aprendeu quando escrever a estrutura de autenticação para escrever a estrutura de blogue. O blogue deve listar todos as publicações, permitir usuários iniciados criar publicações, e permitir o autor da publicação editar ou eliminar ele.

Ao implementar cada apresentação, mantenha o servidor de desenvolvidos em execução. Ao você guardar suas mudanças, tente ir para o URL em seu navegador e testa-los.

### A Estrutura

Definir a estrutura e registar ela na fabrica de aplicação.

`flaskr/blog.py`

```py
from flask import (
    Blueprint, flash, g, redirect, render_template, request, url_for
)
from werkzeug.exceptions import abort

from flaskr.auth import login_required
from flaskr.db import get_db

bp = Blueprint('blog', __name__)
```

Importar e registar a estrutura da fabrica usando [**`app.register_blueprint()`**](#flask.register_blueprint). Coloque o novo código no final da função de fabricação antes de retornar a aplicação.

`flaskr/__init__.py`

```py
def create_app():
    app = ...
    # código existente omitido

    from . import blog
    app.register_blueprint(blog.bp)
    app.add_url_rule('/', endpoint='index')

    return app
```

Ao contrário da estrutura de autenticação, a estrutura de blogue não tem um `url_prefix`. Então a apresentação `index` estará em `/`, a view `create` em `/create`, e por aí vai. O blogue é funcionalidade principal do Flaskr, então faz sentido que o `index` do blogue será o `index` principal.

No entanto, o destino para a apresentação `index` definida abaixo será `blog.index`. Alguns das apresentações de autenticação referem-se a um destino de `index` plano. [**`app.add_url_rule()`**](#flask.add_url_rule) associa o destino com o nome `'index'` com a url `/` de modo que `url_for('index')` ou `url_for('blog.index')` ambos funcionarão, gerando a mesma URL `/` de qualquer maneira.

Em outra aplicação você pode dar a estrutura do blogue uma `url_prefix` e definir uma apresentação `index` separada na fábrica da aplicação, semelhante a apresentação `hello`. Então os destinos `index` e `blog.index` e as URLs seriam diferentes.

### A Apresentação Index

A index exibirá todas as publicações, com as mais recentes em primeiro. Um `JOIN` é usado para que as informações do autor na tabela do usuário estejam disponível no resultado.

`flaskr/blog.py`

```py
@bp.route('/')
def index():
    db = get_db()
    posts = db.execute(
        'SELECT p.id, title, body, created, author_id, username'
        ' FROM post p JOIN user u ON p.author_id = u.id'
        ' ORDER BY created DESC'
    ).fetchall()
    return render_template('blog/index.html', posts=posts)
```

`flaskr/templates/blog/index.html`

```jinja
{% extends 'base.html' %}

{% block header %}
  <h1>{% block title %}Posts{% endblock %}</h1>
  {% if g.user %}
    <a class="action" href="{{ url_for('blog.create') }}">New</a>
  {% endif %}
{% endblock %}

{% block content %}
  {% for post in posts %}
    <article class="post">
      <header>
        <div>
          <h1>{{ post['title'] }}</h1>
          <div class="about">by {{ post['username'] }} on {{ post['created'].strftime('%Y-%m-%d') }}</div>
        </div>
        {% if g.user['id'] == post['author_id'] %}
          <a class="action" href="{{ url_for('blog.update', id=post['id']) }}">Edit</a>
        {% endif %}
      </header>
      <p class="body">{{ post['body'] }}</p>
    </article>
    {% if not loop.last %}
      <hr>
    {% endif %}
  {% endfor %}
{% endblock %}
```

Quando um usuário estiver iniciado, o bloco de `header` adiciona uma ligação para a apresentação `create`. Quando os usuários forem os autores de uma publicação, eles verão uma ligação "Edit" para a apresentação `update` para aquela publicação. `loop.last` é uma variável disponível dentro do [Jinja para loops](http://jinja.pocoo.org/docs/templates/#for). É usado para exibir uma linha depois de cada publicação exceto a última, para separa-los visualmente.

### A Apresentação Create

A apresentação `create` funciona da mesma forma que a apresentação de autenticação `register`. Ou formulário é exibido, ou os dados publicados são validados e a publicação é adicionada ao banco de dados ou um erro é mostrado.

O decorador `login_required` que você escreveu anteriormente é usado nas apresentações do blogue. Um usuário deve estar iniciado para visitar essas apresentações, caso contrário, eles serão redirecionados para a página de entrada(login).

`flaskr/blog.py`

```py
@bp.route('/create', methods=('GET', 'POST'))
@login_required
def create():
    if request.method == 'POST':
        title = request.form['title']
        body = request.form['body']
        error = None

        if not title:
            error = 'Title is required.'

        if error is not None:
            flash(error)
        else:
            db = get_db()
            db.execute(
                'INSERT INTO post (title, body, author_id)'
                ' VALUES (?, ?, ?)',
                (title, body, g.user['id'])
            )
            db.commit()
            return redirect(url_for('blog.index'))

    return render_template('blog/create.html')
```

`flaskr/templates/blog/create.html`

```jinja
{% extends 'base.html' %}

{% block header %}
  <h1>{% block title %}New Post{% endblock %}</h1>
{% endblock %}

{% block content %}
  <form method="post">
    <label for="title">Title</label>
    <input name="title" id="title" value="{{ request.form['title'] }}" required>
    <label for="body">Body</label>
    <textarea name="body" id="body">{{ request.form['body'] }}</textarea>
    <input type="submit" value="Save">
  </form>
{% endblock %}
```
### A Apresentação Update

Ambas as apresentação `update` e `delete` precisarão buscar um `post` por `id` e verificar se o autor corresponde ao usuário iniciado. Evite código duplicado, você pode escrever uma função para receber o `post` e chamar ele em cada apresentação.

`flaskr/blog.py`

```py
def get_post(id, check_author=True):
    post = get_db().execute(
        'SELECT p.id, title, body, created, author_id, username'
        ' FROM post p JOIN user u ON p.author_id = u.id'
        ' WHERE p.id = ?',
        (id,)
    ).fetchone()

    if post is None:
        abort(404, "Post id {0} doesn't exist.".format(id))

    if check_author and post['author_id'] != g.user['id']:
        abort(403)

    return post
```

[**`abort()`**](#flask.abort) irá levantar uma exceção especial que retorna um código de estado HTTP. Ele leva uma mensagem opcional para mostrar com um erro, caso contrário uma mensagem padrão é usada. `404` significa "Não Encontrado", e `403` significa "Proibido". (`401` significa "Não Autorizado", porém você redireciona para a página de entrada (login) ao invés de retornar aquele estado.)

O argumento `check_author` é definido para que a função possa ser usado para obter uma publicação sem verificar o autor. Isso seria útil se você escreveu uma apresentação para mostrar uma publicação individual em uma página, onde o usuário não importa porque eles não modificam a publicação.

`flaskr/blog.py`

```py
@bp.route('/<int:id>/update', methods=('GET', 'POST'))
@login_required
def update(id):
    post = get_post(id)

    if request.method == 'POST':
        title = request.form['title']
        body = request.form['body']
        error = None

        if not title:
            error = 'Title is required.'

        if error is not None:
            flash(error)
        else:
            db = get_db()
            db.execute(
                'UPDATE post SET title = ?, body = ?'
                ' WHERE id = ?',
                (title, body, id)
            )
            db.commit()
            return redirect(url_for('blog.index'))

    return render_template('blog/update.html', post=post)
```

Ao contrário das apresentações que você escreveu até agora, a função `update` leva um argumento, `id`. Que corresponde ao `<int:id>` na rota. Uma URL real será parecido com `/1/update`. O Flask capturará o `1`, garante que seja um [**`int`**](https://docs.python.org/3/library/functions.html#int), e passa-lo como o argumento `id`. Se você não especificar `int:` e ao invés disso fizer `<id>`, ele será uma string. Para gerar um URL para a página de atualização, [**`url_for()`**](#flask.url_for) precisa ser passado o `id` assim ele sabe o que preencher: `url_for('blog.update', id=post['id'])`. Isto está também no ficheiro `index.html` acima.

As apresentações `create` e `update` parecem muito similares. A principal diferença é que a apresentação `update` usa um objeto `post` e uma consulta de tipo `UPDATE` ao invés de um `INSERT`. Com alguma refatoração inteligente, você poderia usar uma apresentação e um modelo de marcação para ambas ações, porém para o tutorial é mais claro manter eles separados.

`flaskr/templates/blog/update.html`

```jinja
{% extends 'base.html' %}

{% block header %}
  <h1>{% block title %}Edit "{{ post['title'] }}"{% endblock %}</h1>
{% endblock %}

{% block content %}
  <form method="post">
    <label for="title">Title</label>
    <input name="title" id="title"
      value="{{ request.form['title'] or post['title'] }}" required>
    <label for="body">Body</label>
    <textarea name="body" id="body">{{ request.form['body'] or post['body'] }}</textarea>
    <input type="submit" value="Save">
  </form>
  <hr>
  <form action="{{ url_for('blog.delete', id=post['id']) }}" method="post">
    <input class="danger" type="submit" value="Delete" onclick="return confirm('Are you sure?');">
  </form>
{% endblock %}
```

Este modelo de marcação tem dois formulários. O primeiro publica os dados editados para a página atual (`/<id>/update`). O outro formulário somente contém um botão e especifica um atributo `action` que publica para a apresentação `delete`. O botão usa algum JavaScript para mostrar uma dialogo de confirmação antes do envio.

O padrão `{{ request.form['title'] or post['title'] }}` é usado para escolher quais dados aparecem no formulário. Quando o formulário não tiver sido enviado, os dados `post` originais aparecem, mas se um formulário com dados inválidos foram publicados, você deseja exibir ele então o usuário pode corrigir o erro, assim `request.form` é usado em vez disso. [**`request`**](#flask.request)  é outra variável que está automaticamente disponível nos modelos de marcação.

### Delete

A apresentação `delete` não tem seu próprio template, o botão `delete` é parte do `update.html` e publica para a URL `/<id>/delete`. Como não há modelo de marcação, ele irá somente lidar com o método `POST` e depois redirecionar para view `index`.

`flaskr/blog.py`

```py
@bp.route('/<int:id>/delete', methods=('POST',))
@login_required
def delete(id):
    get_post(id)
    db = get_db()
    db.execute('DELETE FROM post WHERE id = ?', (id,))
    db.commit()
    return redirect(url_for('blog.index'))
```

Parabens, você acabou de escrever usa aplicação! Tire algum templo para testar tudo no navegador. De qualquer maneira, ainda há mais por se fazer antes do projeto estar completo.

Continue em [Tornar o Projeto Instalável](#tornar-o-projeto-instalável).


## Tornar o Projeto Instalável

Tornar seu projeto instalável significa que você pode construir um ficheiro de distribuição e instala-lo em outros ambientes, do mesmo jeito que você instalou o Flask no ambiente do seu projeto. Isto faz com que implementação do seu projeto seja o mesmo que instalar qualquer outra biblioteca, assim você está usando todas as ferramentas padrão do Python para gerir tudo.

A instalação também vem com outros benefícios que podem não estar óbvios no tutorial ou para usuário de Python inexperientes, incluindo:

* Atualmente, o Python e o Flask entendem como usar o pacote `flaskr` somente porque você está executando a partir da pasta da sua aplicação. A instalação significa que você pode importar ele não importa onde é executado.

* Você pode gerir as dependências da sua aplicação tal como fazes com outros pacotes, então `pip install yourproject.whl` instala-os.

* Ferramentas de testes podem insolar seu ambiente de testes do seu ambiente de desenvolvimento.


> ## Nota:
> Isto está sendo introduzido no final do tutorial, mas em seus futuros projetos você deve sempre começar com ele.

### Descreva o Projeto

O ficheiro `setup.py` descreve seu projeto e os arquivos que pertencem a ele.


`setup.py`

```py
from setuptools import find_packages, setup

setup(
    name='flaskr',
    version='1.0.0',
    packages=find_packages(),
    include_package_data=True,
    zip_safe=False,
    install_requires=[
        'flask',
    ],
)
```

`packages` informa ao Python quais pastas de pacotes (e os arquivos Python que eles contêm) incluir. `find_packages()` encontra essas pastas automaticamente assim você não tem que informa-los. Para incluir outros arquivos, tais como a pastas de arquivos estáticos e templates, `include_package_data` é configurado. O Python precisa de outro ficheiro chamado `MANIFEST.in` para informar quais são esses outros dados.

`MANIFEST.in`

```
include flaskr/schema.sql
graft flaskr/static
graft flaskr/templates
global-exclude *.pyc
```

Isso diz ao Python para copiar tudo dentro das pastas `static` e `templates`, e o ficheiro `schema.sql`, contudo exclui todos arquivos de bytecode.

Veja o [guia oficial de empacotamento](https://packaging.python.org/tutorials/packaging-projects/) para obter outras explicações sobre os arquivos e opções usadas.

### Instale o Projeto

Use `pip` para instalar seu projeto em seu ambiente virtual.

```sh
$ pip install -e .
```

Isto informa ao pip para achar o `setup.py` na pasta atual e instala-lo no modo *editable* (editável) ou *development* (desenvolvimento). Modo editável significa que assim que você fizer mudanças em seu código local, você só precisará instalar se você mudar os metadados sobre o projeto, tal como suas dependências.

Você pode observar com `pip list` que o projeto agora está instalado.

```sh
$ pip list

Package        Version   Location
-------------- --------- ----------------------------------
click          6.7
Flask          1.0
flaskr         1.0.0     /home/user/Projects/flask-tutorial
itsdangerous   0.24
Jinja2         2.10
MarkupSafe     1.0
pip            9.0.3
setuptools     39.0.1
Werkzeug       0.14.1
wheel          0.30.0
```

Nada muda no que diz respeito a como você tem estado a executar seu projeto. `FLASK_APP` continua a estar configurado para o `flaskr` e `flask run` continua a executar a aplicação, mas agora você pode chama-lo de qualquer, não apenas na dentro da pasta `flask-tutorial`.

Continue para [Cobertura de Testes](#cobertura-de-testes).


## Cobertura de Testes

Escrever testes unitários para sua aplicação permite você verificar se o código que você escreveu funciona da maneira esperada por você. O Flask fornece um cliente de teste que simula requisições para a aplicação e retorna os dados da resposta.

Você deve testar o máximo possível do seu código. O códigos dentro de funções só executam quando a função é chamada, e o códigos em ramos, tais como blocos `if`, somente executam quando a condição é atendida. Você quer ter a certeza de que cada função é testada com os dados que cobrem cada ramificação.

Quanto mais próximo conseguir chegar de 100% de cobertura, mais confiante você pode estar de que fazer uma mudança não irá inesperadamente mudar outros comportamentos. De qualquer maneira 100% de cobertura não garante que sua aplicação não tenha bugs. Em particular, ele não testa como o usuário interage com a aplicação no browser. A despeito disso, cobertura de teste é uma ferramenta importante de se usar durante o desenvolvimento.

> ## Nota:
> Isto está sendo introduzindo tarde no tutorial, mas em seus projetos futuros você deve testar durante o desenvolvimento.

Você usará [pytest](https://pytest.readthedocs.io/) e [coverage](https://coverage.readthedocs.io/) para testar e medir seu código. Instale-os ambos:

```sh
$ pip install pytest coverage
```

### Setup and Fixtures

O código de teste está localizado na pasta `tests`. Esta pasta está *próxima* ao `flaskr`, não dentro dele. O ficheiro `tests/conftest.py` contém funções de configuração chamadas *fixtures* que cada teste usará. Em Python, testes são módulos que começam com `test_`, e cada função de teste nesses módulos também começam com `test_`.

Cada teste criará um novo ficheiro de banco de dados temporário e popular com alguns dados que serão usados nos testes. Escreva um ficheiro SQL para inserir aqueles dados.

`tests/data.sql`

```sql
INSERT INTO user (username, password)
VALUES
  ('test', 'pbkdf2:sha256:50000$TCI4GzcX$0de171a4f4dac32e3364c7ddc7c14f3e2fa61f2d17574483f7ffbb431b4acb2f'),
  ('other', 'pbkdf2:sha256:50000$kJPKsz6N$d2d4784f1b030a9761f5ccaeeaca413f27f2ecb76d6168407af962ddce849f79');

INSERT INTO post (title, body, author_id, created)
VALUES
  ('test title', 'test' || x'0a' || 'body', 1, '2018-01-01 00:00:00');
```

O `app` fixture chamará a fábrica (factory) e passar `test_config` para configurar a aplicação e banco de dados para testar ao invés de usar sua configuração de desenvolvimento local.

`tests/conftest.py`

```py
import os
import tempfile

import pytest
from flaskr import create_app
from flaskr.db import get_db, init_db

with open(os.path.join(os.path.dirname(__file__), 'data.sql'), 'rb') as f:
    _data_sql = f.read().decode('utf8')


@pytest.fixture
def app():
    db_fd, db_path = tempfile.mkstemp()

    app = create_app({
        'TESTING': True,
        'DATABASE': db_path,
    })

    with app.app_context():
        init_db()
        get_db().executescript(_data_sql)

    yield app

    os.close(db_fd)
    os.unlink(db_path)


@pytest.fixture
def client(app):
    return app.test_client()


@pytest.fixture
def runner(app):
    return app.test_cli_runner()
```

[**tempfile.mkstemp()**](https://docs.python.org/3/library/tempfile.html#tempfile.mkstemp) cria e abri um ficheiro temporário, retornando o objeto do ficheiro e o caminho para ele. O caminho `DATABASE` é substituido, então ele aponta para este caminho temporário ao invés da pasta da instância. Depois de configurar o caminho, as tabelas do banco de dados são criados e os dados de teste são inseridos. Depois o teste estiver terminado, o ficheiro temporário é fechado e removido.

[**TESTING**](#testing) diz ao Flask que a aplicação está no modo de este. O Flask muda alguns comportamentos internos, então é mais fácil testar, e outras extensões podem também usar a flag (bandeira) para fazer teste neles facilmente.

O `client` fixture chama [**app.test_client()**](#flask.test_client) com o objeto da aplicação criada pelo `app` fixture. Os testes usarão o cliente para fazer requisições para a aplicação sem executar o servidor.

O `runner` fixture é similar ao `client`. [**app.test_cli_runner**](#flask.test_cli_runner) cria um corredor que pode chamar os comandos do Click registado com a aplicação.

O Pytest usa fixtures, combinando seus nomes de funções com nomes de argumentos nas funções de teste. Por exemplo, a função `test_hello` que você escreverá a seguir recebe um argumento `client`. O Pytest corresponde com a função `client`, chama-a, e passa o valor retornado para a função de teste.

### Fábrica (Factory)

Não há muito que testar na própria fábrica. A maioria do código será executado para cada teste pronto, so se algo falhar os outros testes notarão.

O único comportamento que pode alterar é passar a configuração de teste. Se a configuração não estiver passada, deve haver alguma configuração padrão, ao contrário da configuração que seria substituida.

`tests/test_factory.py`

```py
from flaskr import create_app


def test_config():
    assert not create_app().testing
    assert create_app({'TESTING': True}).testing


def test_hello(client):
    response = client.get('/hello')
    assert response.data == b'Hello, World!'
```

Você adicionou a rota `hello` como um exempo quando escrever a fábrica desde o início do tutorial. Ele retorna "Hello, World!", então o teste verifica se os dados da resposta combinam.

### Banco de Dados

Dentro do contexto de uma aplicação, `get_db` deve retornar a mesma conexão toda vez que é chamado. Depois do contexto, a conexão deve ser terminada.

`tests/test_db.py`

```py
import sqlite3

import pytest
from flaskr.db import get_db


def test_get_close_db(app):
    with app.app_context():
        db = get_db()
        assert db is get_db()

    with pytest.raises(sqlite3.ProgrammingError) as e:
        db.execute('SELECT 1')

    assert 'closed' in str(e.value)
```

O comando `init-db` deve chamar a função `init_db` e exibir uma mensagem.

`tests/test_db.py`

```py
def test_init_db_command(runner, monkeypatch):
    class Recorder(object):
        called = False

    def fake_init_db():
        Recorder.called = True

    monkeypatch.setattr('flaskr.db.init_db', fake_init_db)
    result = runner.invoke(args=['init-db'])
    assert 'Initialized' in result.output
    assert Recorder.called
```

Este teste usa o fixture `monkeypatch` do Pytest para substituir a função `init_db` com um daqueles registos que estão sendo chamados. O fixture `runner` que você escreveu acima é usado para chamar o comando `init-db` pelo nome.


### Autenticação

Para a maioria das views, um usuário precisa estar iniciado. A maneira mais fácil de fazer isso nos testes é fazer uma requisição `POST` para a view `login` com o cliente. Em vez de escrever isso todas as vezes, você pode escrever um classe com métodos para fazer isso, e usar uma fixture para passa-lo para o cliente em cada teste.

`tests/conftest.py`

```py
class AuthActions(object):
    def __init__(self, client):
        self._client = client

    def login(self, username='test', password='test'):
        return self._client.post(
            '/auth/login',
            data={'username': username, 'password': password}
        )

    def logout(self):
        return self._client.get('/auth/logout')


@pytest.fixture
def auth(client):
    return AuthActions(client)
```

Com a fixture `auth`, você pode chamar `auth.login()` em um teste para iniciar como usuário `test`, que foi inserido como parte dos dados do teste na fixture `app`.

A apresentação `register` deve renderizar com sucesso em `GET`. No `POST` com dados de formulário validos, ele deve redirecionar para a URL de login e o dados do usuário devem estar dentro do banco de dados. Dados inválidos devem exibir mensagens de erro.

`tests/test_auth.py`

```py
import pytest
from flask import g, session
from flaskr.db import get_db


def test_register(client, app):
    assert client.get('/auth/register').status_code == 200
    response = client.post(
        '/auth/register', data={'username': 'a', 'password': 'a'}
    )
    assert 'http://localhost/auth/login' == response.headers['Location']

    with app.app_context():
        assert get_db().execute(
            "select * from user where username = 'a'",
        ).fetchone() is not None


@pytest.mark.parametrize(('username', 'password', 'message'), (
    ('', '', b'Username is required.'),
    ('a', '', b'Password is required.'),
    ('test', 'test', b'already registered'),
))
def test_register_validate_input(client, username, password, message):
    response = client.post(
        '/auth/register',
        data={'username': username, 'password': password}
    )
    assert message in response.data
```

[**client.get()**](https://werkzeug.palletsprojects.com/en/1.0.x/test/#werkzeug.test.Client.get) faz uma requisição `GET` e retorna o objeto [**Response**](#flask.response) retornado pelo Flask. Igualmente, [**client.post()**](https://werkzeug.palletsprojects.com/en/1.0.x/test/#werkzeug.test.Client.post) faz uma requisição `POST`, convertendo o dicionário `data` em dado de formulário.

Para testar que a página renderiza com sucesso, uma simples requisição é feita e confirmada por um [**status_code**](#flask.response.status_code) `200 OK`. Se a renderização falhar, o Flask retornaria um código `500 Internal Server Error`.

[**headers**](#flask.response.headers) terá um cabeçalho `Location` com a URL do login quando a view register redireciona para a view de login.

[**data**](#flask.response.data) contém o corpo da resposta como bytes. Se você espera um certo valor para renderizar na página, verifique se está dentro do `data`. Bytes devem ser comparados a bytes. Se você quiser comparar texto Unicode, use [**get_data(as_text=True)**](https://werkzeug.palletsprojects.com/en/1.0.x/wrappers/#werkzeug.wrappers.BaseResponse.get_data).

`pytest.mark.parametrize` diz ao Pytest para executar a mesma função de teste com diferentes argumentos. Você pode usa-lo aqui para testar diferentes entradas invalidas e mensagens de erro sem escrever a mesma código três vezes.

Os testes para a view `login` são muito similar a aqueles para o `register`. Ao invés de testar os dados dentro do banco de dados, [**session**](#flask.session) deve ter o `user_id` configurado depois de iniciado.

`tests/test_auth.py`

```py
def test_login(client, auth):
    assert client.get('/auth/login').status_code == 200
    response = auth.login()
    assert response.headers['Location'] == 'http://localhost/'

    with client:
        client.get('/')
        assert session['user_id'] == 1
        assert g.user['username'] == 'test'


@pytest.mark.parametrize(('username', 'password', 'message'), (
    ('a', 'test', b'Incorrect username.'),
    ('test', 'a', b'Incorrect password.'),
))
def test_login_validate_input(auth, username, password, message):
    response = auth.login(username, password)
    assert message in response.data
```

Usando o `client` em um bloco `with` permite acessar variáveis de contexto tais como [**session**](#flask.session) depois da resposta retornada. Normalmente, acessar a `session` fora da requisição levantaria um erro.

Testar o `logout` é o oposto de `login`. [**session**](#flask.session) não deve conter o `user_id` depois de sair.

`tests/test_auth.py`

```py
def test_logout(client, auth):
    auth.login()

    with client:
        auth.logout()
        assert 'user_id' not in session
```

### Blog

Todas as views do blog usam a fixture `auth` que você escreveu mais cedo. Chama `auth.login()` e as requisições subsequentes do cliente irão ser iniciadas como usuário `test`.

A view `index` deve exibir informação sobre a publicação que foi adicionada com o dados do teste. Quando iniciado como o autor, deve haver um link para editar a publicação.

Você pode também testar mais algum comportamento de autenticação enquanto testa a view `index`. Quando não estiver iniciado, cada página exibe links para iniciar ou registar. Quando iniciado, há um link para sair.

`tests/test_blog.py`

```py
import pytest
from flaskr.db import get_db


def test_index(client, auth):
    response = client.get('/')
    assert b"Log In" in response.data
    assert b"Register" in response.data

    auth.login()
    response = client.get('/')
    assert b'Log Out' in response.data
    assert b'test title' in response.data
    assert b'by test on 2018-01-01' in response.data
    assert b'test\nbody' in response.data
    assert b'href="/1/update"' in response.data
```

Um usuário deve estar iniciado para acessar as views `create`, `update`, e `delete`. O usuário iniciado deve ser o autor da publicação para acessar o `update` e o `delete`, do contrário um estado `403 Forbidden` é retornado. Se um `post` com o `id` dado não existir, `update` e `delete` devem retornar `404 Not Found`.

`tests/test_blog.py`

```py
@pytest.mark.parametrize('path', (
    '/create',
    '/1/update',
    '/1/delete',
))
def test_login_required(client, path):
    response = client.post(path)
    assert response.headers['Location'] == 'http://localhost/auth/login'


def test_author_required(app, client, auth):
    # change the post author to another user
    # mudar o autor da publicação para um outro usuário
    with app.app_context():
        db = get_db()
        db.execute('UPDATE post SET author_id = 2 WHERE id = 1')
        db.commit()

    auth.login()
    # current user can't modify other user's post
    # o utilizador atual não pode modificar a publicação de outro utilizador
    assert client.post('/1/update').status_code == 403
    assert client.post('/1/delete').status_code == 403
    # current user doesn't see edit link
    # o utilizador atual não vê a ligação `edit`
    assert b'href="/1/update"' not in client.get('/').data


@pytest.mark.parametrize('path', (
    '/2/update',
    '/2/delete',
))
def test_exists_required(client, auth, path):
    auth.login()
    assert client.post(path).status_code == 404
```

As views `create` e o `update` devem renderizar e retornar um estado `200 OK` para a requisição `GET`. Quando um dado valido é enviado em uma requisição `POST`, `create` deve inserir um novo dado de publicação dentro do banco de dados, e `update` deve modificar o dado existe. Ambas as páginas devem exibir uma mensagem de erro em um dado invalido.

`tests/test_blog.py`

```py
def test_create(client, auth, app):
    auth.login()
    assert client.get('/create').status_code == 200
    client.post('/create', data={'title': 'created', 'body': ''})

    with app.app_context():
        db = get_db()
        count = db.execute('SELECT COUNT(id) FROM post').fetchone()[0]
        assert count == 2


def test_update(client, auth, app):
    auth.login()
    assert client.get('/1/update').status_code == 200
    client.post('/1/update', data={'title': 'updated', 'body': ''})

    with app.app_context():
        db = get_db()
        post = db.execute('SELECT * FROM post WHERE id = 1').fetchone()
        assert post['title'] == 'updated'


@pytest.mark.parametrize('path', (
    '/create',
    '/1/update',
))
def test_create_update_validate(client, auth, path):
    auth.login()
    response = client.post(path, data={'title': '', 'body': ''})
    assert b'Title is required.' in response.data
```

A view `delete` deve redirecionar a URL do index e a publicação não deve mais existir dentro do banco de dados.

`tests/test_blog.py`

```py
def test_delete(client, auth, app):
    auth.login()
    response = client.post('/1/delete')
    assert response.headers['Location'] == 'http://localhost/'

    with app.app_context():
        db = get_db()
        post = db.execute('SELECT * FROM post WHERE id = 1').fetchone()
        assert post is None
```

### Executando os Testes

Alguma configuração extra, que não é necessária mas que torna a execução dos testes com o coverage menos verbosa, pode ser adicionado ao ficheiro `setup.cfg` do projeto.

`setup.cfg`

```cfg
[tool:pytest]
testpaths = tests

[coverage:run]
branch = True
source =
    flaskr
```

Para executar os testes, use o comando `pytest`. Ele encontrará e executará todas as funções de teste que você escreveu.

```sh
$ pytest

========================= test session starts ==========================
platform linux -- Python 3.6.4, pytest-3.5.0, py-1.5.3, pluggy-0.6.0
rootdir: /home/user/Projects/flask-tutorial, inifile: setup.cfg
collected 23 items

tests/test_auth.py ........                                      [ 34%]
tests/test_blog.py ............                                  [ 86%]
tests/test_db.py ..                                              [ 95%]
tests/test_factory.py ..                                         [100%]

====================== 24 passed in 0.64 seconds =======================
```

Se qualquer teste falhar, o pytest exibirá o erro que foi levantado. Você pode executar `pytest -v` para receber uma lista de cada função de teste ao invês de pontos.

Para medir a cobertura do código dos seus testes, use o comando `coverage` para chamar o pytest ao invês de executa-lo diretamente.

```sh
$ coverage run -m pytest
```

Você pode visualizar um relatório de cobertura simples no terminal:

```sh
$ coverage report

Name                 Stmts   Miss Branch BrPart  Cover
------------------------------------------------------
flaskr/__init__.py      21      0      2      0   100%
flaskr/auth.py          54      0     22      0   100%
flaskr/blog.py          54      0     16      0   100%
flaskr/db.py            24      0      4      0   100%
------------------------------------------------------
TOTAL                  153      0     44      0   100%
```

Um relatório em HTML permite você ver quais linhas foram cobertas em cada ficheiro:

```sh
$ coverage html
```

Isto gera arquivos dentro da pasta `htmlcov`. Abra o `htmlcov/index.html` em seu browser para ver o relatório.

Continue para  [Instalar em Produção](#instalar-em-produção).


## Instalar em Produção

Esta parte do tutorial assume que você tenha um servidor onde você quer fazer o deploy da sua aplicação. Ele concede uma visão geral de como criar um ficheiro de distribuição e instala-lo, mas não entrará em assuntos específicos sobre servidor ou software usar. Você pode configurar um novo ambiente em seu computador de desenvolvimento para tentar aplicar as instruções abaixo, mas provavelmente não deveria usa-lo para hospedar uma aplicação pública real. Veja [Opções de Instalação](#opções-de-instalação) para uma listagem de varias diferentes maneiras de hospedar sua aplicação.


### Construir e Instalar

Quando você quiser instalar sua aplicação em outro lugar, você constróis um ficheiro de distribuição. O atual padrão para distribuição Python é o formato *wheel* (roda), com a extensão `.whl`. Certifique-se de que a biblioteca wheel seja instalada primeiro:

```sh
$ pip install wheel
```

Executar `setup.py` com Python dá para você uma ferramenta de linha de comando para emitir um comandos relacionados a construção. O comando `bdist_wheel` irá construir um ficheiro de distribuição wheel.

```sh
$ python setup.py bdist_wheel
```

Você pode encontrar o ficheiro em `dist/flaskr-1.0.0-py3-none-any.whl`. O nome do ficheiro é o nome do projeto, a versão, e algumas tags (etiquetas) que o ficheiro pode instalar.

Copie este ficheiro para outra maquina, [configure um novo ambiente virtual](#criar-um-ambiente), depois instale o ficheiro com `pip`.

```sh
$ pip install flaskr-1.0.0-py3-none-any.whl
```

O pip instalará seu projeto junto com suas dependências.

Desde que este seja uma maquina diferente, você precisa executar `init-db` novamente para criar o banco de dados na pasta da instância.

```sh
$ export FLASK_APP=flaskr
$ flask init-db
```

Quando o Flask detecta que está instalado (não em modo editável), ele usa um diretório diferente para a pasta da instância. Você pode encontra-lo em `venv/var/flaskr-instance`.

### Configurar a Chave Secreta (Secret Key)

No início do tutorial que você deu um valor padrão para [**`SECRET_KEY`**](#secret_key). Este deve ser mudado para algum número de bytes aleatório em produção. Senão, invasores poderiam usar a chave pública `'dev'` para modificar o cookie da sessão, ou qualquer coisa que use a chave secreta.

Você pode usar o seguinte comando para gerar uma chave secreta com valores aleatórios:

```sh
$ python -c 'import os; print(os.urandom(16))'

b'_5#y2L"F4Q8z\n\xec]/'
```

Crie o ficheiro `config.py` na pasta da instância, que a fábrica irá ler se ele existir. Copie o valor gerado para dentro dele.

`venv/var/flask-instance/config.py`

```py
SECRET_KEY = b'_5#y2L"F4Q8z\n\xec]/'
```

Você pode também aplicar qualquer outra configuração necessária aqui, contudo `SECRET_KEY` é a unica necessária para o Flaskr.

### Executar com um Servidor em Produção

Quando estiver executando publicamente ao invês de em desenvolvimento, você não deve usar o servidor de desenvolvimento incluso (`flask run`). O servidor de desenvolvimento é fornecido pelo Werkzeug por conveniência, porém não está desenhado para ser particularmente eficiente, estável ou seguro.

Ao invês disso, use um servidor WSGI de produção. Por exemplo, use o [Waitress](https://docs.pylonsproject.org/projects/waitress/en/stable/), primeiro instale-o em ambiente virtual:

```sh
$ pip install waitress
```

Você precisa informar ao Waitress sobre sua aplicação, porém ele não usa `FLASK_APP` como `flask run` faz. Você precisa dizer para ele importar e chamar a fábrica da aplicação para receber o objeto da aplicação.

```sh
$ waitress-serve --call 'flaskr:create_app'

Serving on http://0.0.0.0:8080
```

Veja [Opções de Instalação](opções-de-instalação) para uma listagem de diferrentes maneiras de hospedar sua aplicação. Waitress é apenas um exemplo, escolhido para o tutorial porque ele é suportado em ambos Windows e Linux. Existem muitos servidores WSGI e opções de instalação que você pode escolher para seu projeto.

Continue para [Continue Desenvolvendo!](#continue-desenvolvendo).

## Continue Desenvolvendo

Você aprendeu sobre alguns conceitos de Flask e Python ao longo do tutorial. Volte e revise o tutorial e compare o seu código com os passos que você tomou para chegar lá. Compare seu projeto ao [projeto de exemplo](https://github.com/pallets/flask/tree/1.1.2/examples/tutorial), que pode parecer um pouco diferente devido a natureza do passo a passo do tutorial.

Há muito mais no Flask do que você viu até agora. Mesmo assim, agora você está equipado para iniciar desenvolvendo suas próprias aplicações web. Consulte o [Começo Rápido](#começo-rápido) para uma revisão daquilo que Flask é capaz de fazer, depois mergulhe para dentro da documentação para continuar aprendendo. O Flask usa [Jinja](https://palletsprojects.com/p/jinja/), [Click](https://palletsprojects.com/p/click/), [Werkzeug](https://palletsprojects.com/p/werkzeug/), e o [ItsDangerous](https://palletsprojects.com/p/itsdangerous/) por trás dos panos, e todos eles tem sua própria documentação também. Você também se interessará em [Extensões](#extensões) que realiza as tarefas como trabalhar com o banco de dados ou validação dos dados enviados pelo formulário do jeito fácil e poderoso.

Se você quiser continuar desenvolvendo seu projeto Flaskr, aqui estão algumas ideias que você poderia tentar a seguir:

* Um view detalhada para mostrar uma única publicação. Clique no título da publicação para ir para sua página.

* Gostar / Desgostar uma publicação.

* Comentários

* Tags (etiquetas). Clicando em uma tag e mostrar todas publicações que têm a mesma tag.

* A caixa de busca que filtra a página index pelo nome.

* Exibir paginação. Mostre somente 5 publicações por página.

* Carregar (upload) uma imagem para acompanhar uma publicação.

* Formate as publicações usando o Markdown.

* Um feed RSS de novas publicações.

Divirta-se e contrua aplicações incríveis!

Este tutorial encaminhará você através da criação de uma aplicação básica do tipo blogue chamada Flaskr. Os usuários será capazes de se registar, iniciar, criar publicações, e editar ou eliminar suas próprias publicações. Você será capaz de empacotar e instalar a aplicação em outros computadores.

![Página Inicial do flaskr](./static_files/flaskr_index.png)

É assumido que você já está familiarizado com o Python. O [tutotial oficial](https://docs.python.org/3/tutorial/) na documentação do Python é excelente maneira de aprender e revisar a linguagem.

Enquanto é desenhado para fornecer um bom ponto de partida, o tutorial não cobri todas funcionalidades do Flask. Consulte o [Começo Rápido](#começo-rápido) para uma revisão daquilo que o Flask pode fazer, depois mergulhe dentro da documentação para encontrar mais. O tutorial somente usa o que é fornecido pelo Flask e Python. Em outro projeto, você pode decidir usar [Extensões](#extensões) ou outras bibliotecas para realizar algumas tarefas de maneira mais símples.

![Página Iniciar do flaskr](./static_files/flaskr_login.png)

O Flask é flexível. Ele não exige que você use qualquer projeto ou estutura de código em particular. No entanto, ao começar pela primeira vez, é útil use a solução mais estruturada. Isto signica que o tutorial exigirá um pouco de boilerplate no início, mas é feito para evitar muitos erros comuns com os quais desenvolvedores inexperientes se deparam, e ele cria um projeto que é fácil de expandir. Uma vez que você se sinta mais confortável com Flask, você pode abandonar esta estrutura e tirar vantagem da flexibilidade do Flask.

![Página Editar do flaskr](./static_files/flaskr_edit.png)

[O projeto do tutorial está disponível como um exemplo no repositório do Flask](https://github.com/pallets/flask/tree/1.1.2/examples/tutorial), se você quiser comparar seu projeto com o produto final conforme você segue o tutorial.

Continue para [Estrutura de Projeto](#estrutura-do-projeto).