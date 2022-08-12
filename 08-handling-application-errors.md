# Manipulando Erros na Aplicação

Aplicações falham, servidores falham. Mais cedo ou mais tarde você verá uma exceção em produção. Mesmo se seu código estiver 100% correto, você continuará vendo exceções de tempos em tempos. Por quê? Porque tudo que estiver envolvido falhará. Aqui estão algumas situações onde códigos perfeitamente bem podem guiar a erros no servidor:

* o cliente terminou a requisição antes da aplicação terminar de fazer a leitura dos sendo enviados.
* o servidor de banco de dados foi sobrecarregado e não pode realizar a consulta
* um sistema de ficheiro cheio
* um servidor backend sobrecarregado
* um erro na programação de uma biblioteca que estiver usando
* a conexão da rede de um servidor para outro falhou

E aqueles são apenas um pequeno exemplo de problemas que poderiam ser achados. Então como lidamos com aquela serie de problemas? Por padrão se sua aplicação estiver sendo executada em modo de produção, e uma exceção for levantada, O Flask exibirá uma página muito simples para você e registará a exceção ao **`logger`**.

Porém há muito que você pode fazer, e nós cobriremos algumas das melhores configurações para lidar com erros incluindo exceções personalizadas e ferramentas de terceiros.

## Ferramentas de Registo de Erros

Enviar mensagens de erro, mesmo que seja apenas para aqueles que são críticos, pode tornar-se desgastante se muitos usuários sendo atingidos pelo erro e os ficheiros de registo de atividades normalmente nunca são vistos. É por isso que recomendamos o uso do [Sentry](https://sentry.io/) para lidar com os erros na aplicação. Está disponível como projeto de código-aberto no [GitHub](https://github.com/getsentry/sentry) e está também disponível como como uma [versão hospedada](https://sentry.io/signup/) que você pode testar gratuitamente. Sentry agrega erros duplicados, captura o rastreador de vestígios completo e variáveis locais para depuração (debugging), e envia-te mensagens baseados nos novos erros ou frequência das margens.

Para usar o Sentry você precisa instalar o cliente `sentry-sdk` como dependência extra do `flask`.

```sh
$ pip install sentry-sdk[flask]
```

E depois adiciona-lo a sua aplicação Flask:

```py
import sentry_sdk
from sentry_sdk.integrations.flask import FlaskIntegration

sentry_sdk.init('YOUR_DSN_HERE', integrations=[FlaskIntegration()])
```

O valor `YOUR_DSN_HERE` precisa ser substituído pelo valor DSN que você recebeu da instalação do Sentry.

Depois da instalação, falhas provocam Erros Internos do Servidor são automaticamente reportadas ao Sentry e de lá você recebe notificações de erro.

Veja também:

* Sentry também suporta captura de erros a partir da fila de trabalho (RQ, Celery, etc.) de maneira elegante. Veja a documentação do [Python SDK](https://docs.sentry.io/platforms/python/) para obter mais informações.
* [Iniciando com o Sentry](https://docs.sentry.io/quickstart/?platform=python)
* [Documentação do Flask-specific](https://docs.sentry.io/platforms/python/guides/flask/)


## Manipuladores de Erro

Sempre que um erro ocorrer dentro do Flask, um código de estado HTTP apropriado será retornado. 400-499 indicam erros na requisição de dados feitas pelo cliente, ou sobre os dados requisitados. 500-599 indicam erros com o servidor ou a aplicação em si.

Você pode querer mostrar páginas de erros personalizadas para o usuário sempre que um erro ocorrer. Isto pode ser feito pelo registo de manipuladores de erros.

Um manipulador de erro é uma função que retorna uma resposta sempre que um tipo de erro for acionado, similar a uma view que é uma função que retorna uma resposta sempre que uma URL requisitada é correspondida. É passada a instância do erro a ser manipulado, que se parece muito com uma [**`HTTPException`**](https://werkzeug.palletsprojects.com/en/2.0.x/exceptions/#werkzeug.exceptions.HTTPException).

O código de estado da resposta não será defino no código de manipulador. Se certifique de prover o código de estado do HTTP apropriado sempre que estiver retornando uma resposta de um manipulador.


### Registando

Registe manipuladores pela decoração de uma função com **`errorhandler()`**. Ou use **`register_error_handler()`** para registar a função mais tarde. Lembre de definer o código do erro sempre que estiver retornando uma resposta.

```py
@app.errorhandler(werkzeug.exceptions.BadRequest)
def handle_bad_request(e):
    return 'bad request!', 400

# ou, sem o decorador
app.register_error_handler(400, handle_bad_request)
```

Subclasses [**`werkzeug.exceptions.HTTPException`**](https://werkzeug.palletsprojects.com/en/2.0.x/exceptions/#werkzeug.exceptions.HTTPException) tais como [**`BadRequest`**](https://werkzeug.palletsprojects.com/en/2.0.x/exceptions/#werkzeug.exceptions.BadRequest) e seus códigos HTTP são intermutáveis sempre que estiver registando um manipulador. (`BadRequest.code == 400`)

Códigos HTTP não padronizados não podem ser registados pelo código porque eles não são conhecidos pelo Werkzeug. Ao invés disso, defina uma subclasse de [**`HTTPException`**](https://werkzeug.palletsprojects.com/en/2.0.x/exceptions/#werkzeug.exceptions.HTTPException) com o código apropriado e regista, e depois levante aquela classe de exceção.

```py
class InsufficientStorage(werkzeug.exceptions.HTTPException):
    code = 507
    description = 'Not enough storage space.'

app.register_error_handler(InsufficientStorage, handle_507)

raise InsufficientStorage()
```

Manipuladores podem ser registado para qualquer classe de exceção, não apenas para as subclasses de [**`HTTPException`**](https://werkzeug.palletsprojects.com/en/2.0.x/exceptions/#werkzeug.exceptions.HTTPException) ou códigos dos estados do HTTP. Manipuladores podem ser registados em uma classe específica, ou em todas subclasses de uma classe pai.


### Manipulando

Sempre estiver construindo uma aplicação em Flask você correrá dentro de exceções. Se alguma parte da seu código quebrar enquanto manipulando uma requisição (e você não tiver um manipuladores de erro registado), um "500 Internal Server Error (Erro Interno do Servidor)" ([**`InternalServerError`**](https://werkzeug.palletsprojects.com/en/2.0.x/exceptions/#werkzeug.exceptions.InternalServerError)) será retornado por padrão. De maneira similar, um erro "400 Not Found (Não Encontrado)" ([**`NotFound`**](https://werkzeug.palletsprojects.com/en/2.0.x/exceptions/#werkzeug.exceptions.NotFound)) ocorrerá se uma requisição for enviada para uma rota não registada. Se uma rota receber um método de uma requisição não permita, um erro "405 Method Not Allowed (Método Não Permitido)" ([**`MethodNotAllowed`**](https://werkzeug.palletsprojects.com/en/2.0.x/exceptions/#werkzeug.exceptions.MethodNotAllowed)) será levantado. Todos esses são subclasses do [+*`HTTPException`**](https://werkzeug.palletsprojects.com/en/2.0.x/exceptions/#werkzeug.exceptions.HTTPException) e oferecidas por padrão dentro do Flask.

O Flask dá para você a habilidade de levantar qualquer exceção HTTP registada pelo Werkzeug. Contudo, por padrão as exceções HTTP retorna páginas símples de exceção. Você pode quer exibir páginas de erros personalizadas para o usuário sempre que um erro ocorrer. Isto pode ser feito registando manipuladores de erros.

Sempre que o Flask capturar uma exceção enquanto estiver manipulando uma requisição, ele é primeiro buscado pelo código. Se nenhum manipulador é registado para o código, o Flask busca o erro pela sua hierarquia de classe; o manipulador mais específico é escolhido. Se nenhum manipulador estiver registado, as subclasses de [**`HTTPException`**](https://werkzeug.palletsprojects.com/en/2.0.x/exceptions/#werkzeug.exceptions.HTTPException) exibem uma mensagem genérica sobre o código delas, enquanto outras exceções são convertidas para um genérico "500 Internal Server Error".

Por exemplo, se uma instância de [*+`ConnectionRefusedError`**](https://docs.python.org/3/library/exceptions.html#ConnectionRefusedError) é levantada, e um manipulador é registado para [**`ConnectionError`**](https://docs.python.org/3/library/exceptions.html#ConnectionError) e [**`ConnectionRefusedError`**](https://docs.python.org/3/library/exceptions.html#ConnectionRefusedError), o manipulador mais específico [**`ConnectionRefusedError`**](https://docs.python.org/3/library/exceptions.html#ConnectionRefusedError) é chamado com uma instância de exceção para gerar uma resposta.

Manipuladores registadas no esquema têm precedência sobre aquelas registadas globalmente na aplicação, assumindo que um esquema está manipulando a requisição que levanta uma exceção. Todavia, o esquema não pode manipular o roteamento de erro 404 porque o erro 404 ocorre no nível de roteamento antes do esquema puder sequer ser determinado.


### Manipuladores de Exceções Genéricas

É possível registar manipuladores de erro para classes de bases mais genéricas tais como `HTTPException` ou até mesmo `Exception`. Todavia, esteja ciente de que estes irão capturar mais do que você realmente espera.

Por exemplo, um manipulador de erro para `HTTPException` pode ser útil para transform as páginas de erros em HTML padrão em JSON. No entanto, este manipulador acionará coisas que você não causou diretamente, tais como erros 404 e 405 durante o roteamento. Certifique-se de criar o seu manipulador com cuidado, assim você não perde informações a respeito do erro HTTP.

```py
from flask import json
from werkzeug.exceptions import HTTPException

@app.errorhandler(HTTPException)
def handle_exception(e):
    """Retorna JSON no lugar de HTML para erros HTTP"""
    # inicia com o código do estado do erro e com os cabeçalhos corretos
    response = e.get_response()
    # substitua o corpo (body) com JSON
    response.data = json.dumps({
        "code": e.code,
        "name": e.name,
        "description": e.description,
    })
    response.content_type = "application/json"
    return response
```

Um manipulador de erro para `Exception` poder parecer útil para mudar como são apresentados ao usuário, todos erros, incluindo aqueles não manipulados. Todavia, isto é similar a fazer `exception Exception`: em Python, isto capturará todos erros diferentes não manipulados, incluindo todos códigos dos estados do HTTP.

Na maioria dos casos será mais seguro registar manipuladores de erros para exceções mais específicas. Desde que as instâncias de `HTTPException` sejam respostas WSGI válidas, você poderia também passa-las diretamente.

```py
from werkzeug.exceptions import HTTPException

@app.errorhandler(Exception)
def handle_exception(e):
    # passa atráves de erros HTTP
    if isinstance(e, HTTPException):
        return e


    # agora está manipulando exceções que não são apenas HTTP
    return render_template("500_generic.html", e=e), 500
```

Os manipuladores de erros continuam a respeitar a hierarquia da classe de exceção. Se você registar manipuladores para ambos `HTTPException` e `Exception`, o manipulador de `Exception` não manipulará as subclasses de `HTTPException` porque ele o manipulador de `HTTPException` é mais específico.


### Exceções Não Manipuladas

Quando não houver nenhum manipulador de erro registado para uma exceção, um erro de código 500 Erro Interno do Servidor será retornado. Consulte **`flask.Flask.handle_exception()`** para obter mais informações sobre este comportamento.

Se houver um manipulador de erro registado para o `InternalServerError` (Erro Interno do Servidor), isto será invocado. Desde a versão do Flask 1.1.0, este manipulador de erro sempre será passado como uma instância de `InternalServerError`, não o erro não manipulado original.

O erro original está disponível como `e.original_exception`.

Um manipulador de erro para "500 Internal Server Error" será passado com exceções não capturadas adicionais para explicitar erros 500. No modo de depuração, um manipulador para "500 Internal Server Error" não será usado. Ao invés disso, o depurador interativo será exibido.


## Páginas de Erro Personalizada

Algumas vezes, quando estiver construindo uma aplicação em Flask, você desejará levantar uma [**`HTTPException`**](https://werkzeug.palletsprojects.com/en/2.0.x/exceptions/#werkzeug.exceptions.HTTPException) para avisar ao usuário que algo está errado com a requisição. Felizmente, O Flask vem com a útil função [**`abort()`**]() que como desejado, aborta uma requisição com um erro HTTP a partir do Werkzeug. Ele também fornecerá para você uma página de erro simples em preto e branco com uma descrição básica, mas nada agradável.

Dependendo do tipo do código do erro é mais ou menos provável que o usuário realmente veja tal erro.

Considere o código abaixo, onde podemos ter uma rota para o perfil do usuário, e se o usuário falhar em passar um nome de usuário (username) nós podemos levantar um "400 Má Requisição (Bad Request)". Se o usuário passar um nome de usuário e nós não conseguirmos achar o usuário passado, nós levantamos um "404 Não Encontrado (Not Found)".

```py
from flask import abort, render_template, request

# o username precisa ser fornecido nos argumentos (args) da consulta (query)
# uma requisição bem sucedida seria parecida com /profile?username=jack
@app.route("/profile")
def user_profile():
    username = request.arg.get("username")
    # se o username não for fornecido na requisição, retorna erro 400 má requisição
    if username is None:
        abort(400)

    user = get_user(username=username)
    # se o usuário não for achado pelo seu username, retorna erro 404 não encontrado
    if user is None:
        abort(404)

    return render_template("profile.html", user=user)
```

Aqui está um outro exemplo de implementação de uma exceção "404 Página Não Encontrada (Page Not Found)":

```py
from flask import render_template

@app.errorhandler(404)
def page_not_found(e):
    # repare que definimos o código de estado 404 explicitamente
    return render_template('404.html'), 404
```

Quando estiver usando [Fábricas de Aplicações]():

```py
from flask import Flask, render_template

def page_not_found(e):
  return render_template('404.html'), 404

def create_app(config_filename):
    app = Flask(__name__)
    app.register_error_handler(404, page_not_found)
    return app
```

Um exemplo de template poderia ser assim:

```jinja2
{% extends "layout.html" %}
{% block title %}Page Not Found{% endblock %}
{% block body %}
  <h1>Page Not Found</h1>
  <p>What you were looking for is just not there.
  <p><a href="{{ url_for('index') }}">go somewhere nice</a>
{% endblock %}
```

### Exemplos Adicionais

Os exemplos acima realmente não seriam uma implementação sobre páginas de exceções padrões. Podemos criar um template personalizado para 500.html como algo parecido com isto:

```jinja2
{% extends "layout.html" %}
{% block title %}Internal Server Error{% endblock %}
{% block body %}
  <h1>Internal Server Error</h1>
  <p>Oops... we seem to have made a mistake, sorry!</p>
  <p><a href="{{ url_for('index') }}">Go somewhere nice instead</a>
{% endblock %}
```

Pode ser implementada ao renderizar o modelo de marcação sobre o "500 Erro Interno do Servidor (Internal Server Error)":

```py
from flask import render_template

@app.errorhandler(500)
def internal_server_error(e):
    # repare que definimos o código de estado 500 explicitamente
    return render_template('500.html'), 500
```

Quando estiver usando [Fábricas de Aplicações]():

```py
from flask import Flask, render_template

def internal_server_error(e):
  return render_template('500.html'), 500

def create_app():
    app = Flask(__name__)
    app.register_error_handler(500, internal_server_error)
    return app
```

Quando estiver usando [Aplicações Modulares com Esquemas (Blueprints)]():

```py
from flask import Blueprint

blog = Blueprint('blog', __name__)

# como um decorador
@blog.errorhandler(500)
def internal_server_error(e):
    return render_template('500.html'), 500

# ou com o register_error_handler
blog.register_error_handler(500, internal_server_error)
```

## Estrutura dos Manipuladores de Erro

Em [Aplicações Modulares com Estruturas](#), a maioria dos manipuladores de erros funcionarão como esperado. Todavia, há um canivete no que respeita a manipuladores para exceções 404 e 405. Estes manipuladores de erros são unicamente invocadas a partir de uma declaração `raise` apropriada ou uma chamada para `abort` em outras funções de visualização (view) do esquema; eles não são invocados por, exemplo, acesso a uma URL inválida.

Isto é porque o esquema não "possui" um espaço de URL certo, assim a instância da aplicação não tem maneira de saber qual esquema de manipulador de erro deveria executar se receber uma URL inválida. Se você gostaria de executar uma estratégia diferente de manipulação para estes erros baseado nos prefixos da URL, eles podem ser definidos no nível da aplicação usando o objeto procurador (proxy) `request`.

```py
from flask import jsonify, render_template

# no nível da aplicação
# e não no nível do esquema
@app.errorhandler(404)
def page_not_found(e):
    # se uma requisição está dentro do espaço da URL do nosso blogue
    if request.path.startswith('/blog/'):
        # retornamos uma página do blogue personalizada para o erro 404
        return render_template("blog/404.html"), 404
    else:
        # caso contrário retornamos nossa página genérica para o erro 404
        return render_template("404.html"), 404

@app.errorhandler(405)
def method_not_allowed(e):
    # se uma requisição tiver o método errado para nossa API
    if request.path.startswith('/api/'):
        # então retornamos um json dizendo
        return jsonify(message="Method Not Allowed"), 405
    else:
        # caso contrário retornamos uma página genérica para o erro 405
        return render_template("405.html"), 405
```


## Retornando Erros de API como JSON

Ao desenvolver APIS em Flask, alguns desenvolvedores perceberam que as exceções internas do Flask não são expressivas o suficiente para serem usadas em APIs e que o tipo de conteúdo (content type) do *text/html* que eles emitem não é muito útil para consumidores de API.

Usando a mesma técnica usada acima e o método [**`jsonify()`**](#) podemos retornar uma resposta JSON para os erros da API. O método [**`abort()`**](#) é chamado com um parâmetro `description`. O manipulador de erro usará aquilo como a mensagem de erro em JSON, e definir o código do estado para 404.

```py
from flask import abort, jsonify

@app.errorhandler(404)
def resource_not_found(e):
    return jsonify(error=str(e)), 404

@app.route("/cheese")
def get_one_cheese():
    resource = get_resource()

    if resource is None:
        abort(404, description="Resource not found")

    return jsonify(resource)
```

Também podemos criar classes de exceções personalizadas. Por exemplo, podemos introduzir uma nova exceção personalizada para uma API que pode carregar uma mensagem adequada humanamente legível, um código de estado para o erro e mais algumas opções adicionais (payload) para dar mais contexto ao erro.

Esse é um exemplo simples:

```py
from flask import jsonify, request

class InvalidAPIUsage(Exception):
    status_code = 400

    def __init__(self, message, status_code=None, payload=None):
        super().__init__()
        self.message = message
        if status_code is not None:
            self.status_code = status_code
        self.payload = payload

    def to_dict(self):
        rv = dict(self.payload or ())
        rv['message'] = self.message
        return rv

@app.errorhandler(InvalidAPIUsage)
def invalid_api_usage(e):
    return jsonify(e.to_dict())

# uma API com a rota da aplicação para receber informações do usuário
# uma requisição correta pode ser /api/user?user_id=420
@app.route("/api/user")
def user_api(user_id):
    user_id = request.arg.get("user_id")
    if not user_id:
        raise InvalidAPIUsage("No user id provided!")

    user = get_user(user_id=user_id)
    if not user:
        raise InvalidAPIUsage("No such user!", status_code=404)

    return jsonify(user.to_dict())
```

Agora uma apresentação pode levantar aquela exceção com uma mensagem de erro. Adicionalmente algumas informações adicionais (payload) podem ser fornecidos como um dicionário através do parâmetro *payload*.


## Registando (Logging)

Consulte a secção de [Registando](#) para obter mais informações sobre como fazer registo das exceções, tais como enviar-las por email para os administradores.


## Depurando (Debugging)

Consulte a secção de [Depurando Erros da Aplicação](#) para obter mais informações sobre como depurar erros em desenvolvimento e em produção.