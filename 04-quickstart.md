# Começar

Pronto para começar? Esta página dá um boa introdução ao Flask. Ela assume que você já tem o Flask instalado. Se você ainda não o tiver instalado, siga para a secção [Instalação](#instalação).

## Uma Aplicação Minima

Uma aplicação minima em Flask parece com algo do tipo:

```py
from flask import Flask
app = Flask(__name__)

@app.route('/')
def hello_world():
    return 'Hello, World'
```

O que aquele código faz?

1. Nós importamos a classe Flask. Uma instância dessa classe será nossa aplicação WSGI.
2. Depois nós criamos uma instância da classe. O primeiro argumento é o nome do módulo ou pacote da aplicação. Se você estiver usando um módulo solitário (como neste exemplo), você deveria usar `__name__` porque dependendo se for iniciado como aplicação ou importado como um módulo o nome será diferente ('`__main__`' versos o atual nome importado). Isto é necessário para que então o Flask saiba onde procurar pelos modelos de marcação, ficheiros estáticos, e por aí vai. Para mais informações de uma olhada na documentação do Flask.
3. Nós em seguida usamos o decorator `route()` que diz ao Flask qual URL deve acionar nossa função.
4. A função é dada um nome que é também usado para gerar URLs para aquela função em particular, e retorna a mensagem que queremos mostrar no browser do usuário.

Apenas guarde ele como `hello.py` ou algo similar. Certifique-se de não chamar a sua aplicação de `Flask.py` porque isso iria entrar em conflito com Flask em si.

Para executar a aplicação você pode tanto usar comando Flask ou o modificador `-m` do Python com o Flask.

Antes de você poder fazer isso, você precisa dizer ao seu terminal qual aplicação usar através da exportação da variável de ambiente `FLASK_APP`:

```sh
$ export FLASK_APP=hello.py
$ flask run
 * Running on http://127.0.0.1:5000/
```

Se você estiver no Windows, a sintaxe de variável de ambiente depende do interpretador de linha de comando. No prompt de comando:

```sh
C:\path\to\app>set FLASK_APP=hello.py
```

E no PowerShell:

```ps
PS C:\path\to\app> $env:FLASK_APP = "hello.py"
```

Outra alternativa é você poder usar **python -m flask**:

```sh
$ export FLASK_APP=hello.py
$ python -m flask run
 * Running on http://127.0.0.1:5000/
```

Isto lança servidor embutido muito simples, que é bom o suficiente para testes mas provavelmente não aquele que você quer usar em produção. Para opções de
instalação no servidor veja [Opções de Instalação](opções-de-instalação).

Agora siga para https://127.0.0.1:5000, e você verá sua saudação Olá Mundo.

> ## Servidor Externamente Visível:
>
> Se você executar o servidor notará que o servidor só está acessível a partir de seu próprio computador, e não para outro na rede. Isto é o padrão porque em modo de depuração o usuário da aplicação pode executar código Python arbitrário no seu computador.
>
> Se você tiver o depurador desabilitado ou confiar nos usuários da sua rede, você pode deixar o servidor publicamente disponível simplesmente adicionando `--host=0.0.0.0` na linha de comando:
>
> `$ flask run --host=0.0.0.0`
>
> Isso diz ao sistema operacional para ouvir todos os IPs públicos.

## O que fazer caso o servidor não iniciar

No caso de **python -m flask** falhar ou **flask** não existir, há varias razões pelas quais este pode ser o caso. Antes de tudo você precisa verificar a mensagem de erro.

### Versão Antiga do Flask

Versões do Flask abaixo da versão 0.11 tem maneiras diferente de iniciar a aplicação. Resumindo, o comando **flask** não existia, e nem fazia **python -m flask**. Neste caso você tem duas opções: tanto atualizar para versões recente do Flask ou ter que olhar na documentação do [Servidor de Desenvolvimento](#servidor-de-desenvolvimento) para achar um método alternativo para executar um servidor.

### Nome Importado Inválido

A variável de ambiente `FLASK_APP` é o nome do módulo importado durante a execução de `flask run`. Neste caso em que o módulo está nomeado incorretamente você receberá um erro de importação, ao iniciar (ou se a depuração estiver ativada quando você navegar até a aplicação). Ele dirá a você o que ele tentou importar e porquê falhou.

A razão mais comum é um typo ou porque você não criou um objeto `app`.

## Modo de Depuração

(Quer apenas imprimir os erros e rastrear vestígios? veja [Erros da Aplicação](#erros-da-aplicação))

O roteiro (script) do **flask** é bom para começar um servidor de desenvolvimento local, mas você teria de reiniciar manualmente depois de cada mudança no seu código. Que não muito bom e o Flask pode fazer melhor. Se você ativar o suporte a depuração, o servidor reiniciar-se-á a si mesmo a cada mudança de código, e ele também proverá para você um depurador muito útil se as coisas correrem mal.

Para ativar todas funcionalidades de desenvolvimento (incluindo o modo de depuração) você pode exportar a variável de ambiente `FLASK_ENV` e configura-lo para `development` antes de executar o servidor:

```sh
$ export FLASK_ENV=development
$ flask run
```

(No Windows você precisa usar o `set` no lugar de `export`.)

Isto faz as seguintes coisas:

1. Ativa o depurador
2. Ativa a reiniciação automática
3. Ativa o modo de depuração na aplicação Flask.

Você pode também controlar o modo de depuração separadamente a partir do ambiente pela exportação `FLASK_DEBUG=1`.

Existem mais parâmetros que são explicados na documentação do [Servidor de Desenvolvimento](#servidor-de-desenvolvimento).

> ## Atenção:
>
> Apesar do depurador interativo não funcionar em ambiente de bifurcação (que o torna quase impossível de o usar em servidores de produção), ele ainda permite a execução de código arbitrário. Isto o torna um risco de segurança maior e por conseguinte ele nunca deve ser usado em maquinas de produção.

Captura de tela do depurador em ação:

![captura-de-tela-do-depurador-em-ação](./static_files/debugger.png)

Mais informações sobre o uso do depurador pode ser encontrado na [documentação do Werkzeug](https://werkzeug.palletsprojects.com/debug/#using-the-debugger).

Tens outro depurador em mente? Veja [Trabalhando com Depuradores](#trabalhando-com-depuradores).

## Tratando o HTML

Quando estiver retornando o HTML (o tipo de resposta padrão em Flask), quaisquer valores fornecidos pelo utilizador traduzidos na saída devem ser tratados para proteger de ataques de injeção. Os modelos de marcação de HTML interpretados com a Jinja, introduzida depois, fará isto automaticamente.

`escape()`, mostrado aqui, pode ser usado manualmente. É omitido na maioria dos exemplos por questões de brevidade, mas você sempre deve estar ciente de como está utilizando dados não confiáveis.

```py
from markupsafe import escape

@app.route("/<name>")
def hello(name):
    return f"Hello, {escape(name)}!"
```

Se um utilizador conseguiu submeter o nome `<script>alert("bad")</script>`, o tratamento faz com que ele seja transformado em texto, ao invés de executar o `script` no navegador do utilizador.

O `<name>` na rota captura um valor a partir da URL e passa-o para uma função de apresentação. Estas variáveis são explicadas abaixo.

## Roteamento

Aplicações web modernas usam URLs semânticas para ajudar os usuários. Usuários são mais propensos a gostarem de uma página e voltar a ela se a página usar uma URL semântica que eles possam lembrar e usar para visitar diretamente uma página.

Use o decorador **`route()`** para vincular uma função à uma URL.

```py
@app.route('/')
def index():
    return 'Index Page'

@app.route('/hello')
def hello():
    return 'Hello, World'
```

Você pode fazer mais! Você pode tornar partes de uma URL dinâmica e anexar várias regras à uma função.

### Regras Variáveis

Você pode adicionar secções variáveis à uma URL pela marcação de secções com `<nome_da_variável>`. Sua função depois recebe o `<nome_da_variável>` como um argumento de palavra-chave. Opcionalmente, você pode usar um conversor para especificar o tipo do argumento como `<conversor:nome_da_variável>`.

```py
from markupsafe import escape

@app.route('/user/<username>')
def show_user_profile(username):
    # exiba o perfil do utilizador para esse utilizador
    return f'User {escape(username)}'

@app.route('/post/<int:post_id>')
def show_post(post_id):
    # exiba a publicação com o dado id, o id é um inteiro
    return f'Post {post_id}'

@app.route('/path/<path:subpath>')
def show_subpath(subpath):
    # exiba o caminho depois de /path/
    return f'Subpath {escape(subpath)}'
```

Tipos de conversores:


 Tipo     | Descrição 
 -------- | -------------------------------------------------- 
 `string` | (padrão) aceita qualquer texto sem barra inclinada
 `int`    | aceita número positivo inteiro                  
 `float`  | aceita número positivo com ponto flutuante       
 `path`   | como `string` mas também aceita barra inclinada    


### URLs Únicas / Comportamento de Redirecionamento

As duas regras a seguir diferem em seu uso de uma barra final.

```py
@app.route('/projects/')
def projects():
    return 'The project page'

@app.route('/about')
def about():
    return 'The about page'
```

A URL canónica para o ponto final de `projects` tem uma barra final. É similar a uma pasta dentro de um sistema de ficheiros. Se você acessar a URL sem a barra final, Flask leva você para a URL canónica com a barra final.

A URL canónica para o ponto final `about` não tem uma barra final. É similar ao nome do caminho de um ficheiro. Acessar a URL com uma barra final produz um erro 404 de "página não encontrada". Isto ajuda a manter as URLs para esses recursos únicas, que ajuda os mecanismos de busca a evitar a indexação da mesma página duas vezes.


### Construindo uma URL

Para construir uma URL para uma função especifica, use a função **`url_for()`**. Ela aceita o nome da função como seu primeiro argumento e qualquer número de argumentos de palavra-chave, cada correspondendo a uma parte variável da regra da URL. Partes variáveis desconhecidas são anexadas a URL como parâmetros de consulta.

Porquê você quereria construir URLs usando a função de reversão de URL **`url_for()`** ao invés de codifica-los de forma manual dentro dos seus templates?

1. A reversão é algumas vezes mais descritiva do que a codificação manual de URLs.
2. Você pode mudar suas URLs de uma só vez ao invés recordar para manualmente modificar as URLs.
3. A construção de URL trata do escapamento de caracteres especiais e converte para Unicode os dados de forma transparente.
4. Os caminhos gerados são sempre absolutos, evitando os comportamentos inesperados de caminhos relativos nos browsers.
5. Se sua aplicação está do lado de fora da URL raiz, por exemplo, em `/myapplication` ao invés de `/`, a **`url_for()`** trata disso apropriadamente por você.

Para exemplo, aqui usamos o método **`test_request_context()`** para testar a função **`url_for()`**.
**`test_request_context()`** diz ao Flask para agir como se estivesse a tratar de uma requisição mesmo enquanto usamos uma shell do Python. Veja [Locais de Contexto](#locais-de-contexto).

```py
from flask import Flask, url_for
from markupsafe import escape

app = Flask(__name__)

@app.route('/')
def index():
    return 'index'

@app.route('/login')
def login():
    return 'login'

@app.route('/user/<username>')
def profile(username):
    return '{}\'s profile'.format(escape(username))

with app.test_request_context():
    print(url_for('index'))
    print(url_for('login'))
    print(url_for('login', next='/'))
    print(url_for('profile', username='John Doe'))
```

```sh
/
/login
/login?next=/
/user/John%20Doe
```

### Métodos HTTP

As aplicações web usam métodos HTTP diferentes quando estão prestes a acessar URLs. Você deveria se familiarizar com os métodos HTTP ao trabalhar com Flask. Por padrão, uma rota responde somente a requisições `GET`. Você pode usar o argumento `methods` do decorador **`route()`** para manipular métodos HTTP diferentes.

```py
from flask import request

@app.route('/login', methods=['GET', 'POST'])
def login():
    if request.method == 'POST':
        return do_the_login()
    else:
        return show_the_login_form()
```

Se o `GET` estiver presente, o Flask adiciona automaticamente o suporte para o método `HEAD` e manipula as requisições `HEAD` de acordo com o [HTTP RFC](https://www.ietf.org/rfc/rfc2068.txt). Igualmente, `OPTIONS` é automaticamente implementado para você.


## Ficheiros Estáticos

Aplicações web dinâmicas também precisam de ficheiros estáticos. Que usualmente é de onde os ficheiros de CSS e de JavaScript estão vindo. Idealmente seu servidor web é configurado para servir-los para você, porém durante o desenvolvimento o Flask pode fazer isso também. Apenas crie um pasta com o nome `static` dentro do seu pacote ou proximo ao seu módulo e ele estará disponível na aplicação em `/static`.

Para gerar URLs para os ficheiros estáticos, use o nome de ponto final especial `'static'`:

```py
url_for('static', filename='style.css')
```

O ficheiro tem de estar armazenado no sistema de ficheiros como `static/style.css`.

## Interpretando os Modelos de Marcação

Gerar o HTML a partir do Python não é divertido, e atualmente um pouco embaraçoso porque você tem de fazer o tratamento da HTML manualmente para manter a segurança da aplicação. Por causa disto o Flask configura o gestor de modelo de marcação [Jinja2](http://jinja.pocoo.org/) automaticamente para você.

Você pode usar o método **`render_template()`** para interpretar um modelo de marcação. Tudo que você tem de fazer é prover o nome do modelo de marcação e as variáveis que você quer passar para o gestor de modelo de marcação como argumentos de palavra-chave. Aqui está um exemplo simples de como renderizar um modelo de marcação:

```py
from flask import render_template

@app.route('/hello/')
@app.route('/hello/<name>')
def hello(name=None):
    return render_template('hello.html', name=name)
```

O Flask irá procurar pelos modelos de marcação dentro da pasta `templates`. Então se sua aplicação é um módulo, esta pasta está proxima a esse módulo, se é um pacote está atualmente dentro do seu pacote:

1º caso: um módulo:

```
/application.py
/templates
    /hello.html
```

2º caso: um pacote:

```
/application
    /__init__.py
    /templates
        /hello.html
```

Você pode usar todo o poder dos modelos de marcação Jinja2 nos seus modelos de marcação. Siga em frente para a [Documentação do Modelo de Marcação Jinja2](http://jinja.pocoo.org/docs/templates/) oficial para mais informações.

Aqui está um exemplo de modelo de marcação:

```jinja
<!doctype html>
<title>Hello from Flask</title>
{% if name %}
  <h1>Hello {{ name }}!</h1>
{% else %}
  <h1>Hello, World!</h1>
{% endif %}
```

Dentro dos modelos de marcação você também tem acesso aos objetos **request**, **session** e **g** como também a função **`get_flashed_messages()`**.

Os modelos de marcação são especialmente úteis quando a herança é usada. Se você quiser saber como isso funciona, siga para documentação do padrão de [Herança de Modelo de Marcação](#herança-de-modelo-de-marcação). Basicamente a herança de modelo de marcação torna possível manter certos elementos em cada página (como, cabeçalho, navegação e o rodapé).

O escapamento automático está ativado, então se a variável `name` conter código HTML ele será escapado automaticamente. Se você poder confiar uma variável e você sabe que será um código HTML seguro (por exemplo porque veio de um módulo que converte a marcação do wiki em HTML) você pode marcar ele como sendo seguro pelo uso da classe **Markup** ou pelo uso do filtro `|safe` dentro do modelo de marcação. Siga para a documentação do Jinja2 para obter mais exemplos de uso.

Aqui está uma introdução básica de como a classe **Markup** funciona:

```py
>>> from markupsafe import Markup
>>> Markup('<strong>Hello %s!</strong>') % '<blink>hacker</blink>'
Markup(u'<strong>Hello &lt;blink&gt;hacker&lt;/blink&gt;!</strong>')
>>> Markup.escape('<blink>hacker</blink>')
Markup(u'&lt;blink&gt;hacker&lt;/blink&gt;')
>>> Markup('<em>Marked up</em> &raquo; HTML').striptags()
u'Marked up \xbb HTML'
```

* **Relatório de Mudança**
    * Mudado na versão 0.5: O auto-escapamento já não está disponível para todos os templates. As seguintes extensões para templates ativam o auto-escapamento: `.html`, `.htm`, `.xml`, `.xhtml`. Templates carregados a partir de strings terão o auto-escapamento desativado.

Não tem certeza do que o objeto **g** é? É algo em que você pode armazenar informações para suas próprias necessidades, consulte a documentação deste objeto (**g**) e a [Usando o SQLite3 com Flask](#sqlite3) para obter mais informações.


## Acessando os Dados da Requisição

Para aplicações web é crucial reagir aos dados que um cliente envia para o servidor. No Flask esta informação é fornecida através do objeto global **request**. Se você tem alguma experiência com o Python, você ficaria maravilhado de como esse objeto pode ser global e como o Flask gerência-o para o manter seguro (threadsafe). A resposta é locais de contexto:

### Locais de Contexto

> ## Informação de Mantenedor
> Se você quiser entender como isso funciona e como você pode implementar testes com locais de contexto, leia esta secção, ou do contrário você pode apenas salta-la.

Certos objetos no Flask são globais, mas não do tipo comum. Esses objetos são intermediários(proxies) de objetos que estão locais para um contexto especifico. Que legal. Porém é um pouco mais fácil de entender.

Imagina o contexto sendo o manipulador de thread. Uma requisição chega e o servidor web decide gerar uma nova thread (ou outra coisa, o objeto subjacente é capaz de lidar com sistemas de concorrência diferentes de threads). Quando o Flask inicia seu manipulador de requisição interno ele percebe que a thread atual é o contexto ativo e vincula a aplicação atual e o ambiente WSGI ao contexto (thread). Ele faz isso de uma maneira inteligente para que uma aplicação possa invocar outra aplicação sem quebrar.

Então o que isso significa para você? Basicamente você pode ignorar completamente que esse é o caso a menos que você esteja fazendo algo como um teste unitário. Você perceberá que o código que depende de um objeto de requisição será interrompido repentinamente porque não há objeto de requisição. A solução é você mesmo criar um objeto de requisição e vincula-lo ao contexto. A solução mais fácil para teste unitário é usar o gestor de contexto **`test_request_context()`**. Em combinação com a instrução `with` ele vinculará uma requisição de teste para que você possa interagir com ela. Aqui está um exemplo:

```py
from flask import request

with app.test_request_context('/hello', method='POST'):
    # agora você pode fazer algo com a requisição até o
    # fim do bloco with, tal como asserções básicas:
    assert request.path == '/hello'
    assert request.method == 'POST'
```

Outra possibilidade é passando um ambiente WSGI inteiro para o método **`request_context()`**:

```py
from flask import request

with app.request_context(environ):
    assert request.method == 'POST'
```


### O Objeto de Requisição

O objeto de requisição é documentado na secção da API e não vamos cobri-lo aqui em detalhes (veja [Request](#request)). Aqui está um grande resumo de algumas das operações mais comuns. Antes de tudo você tem que importa-lo a partir do módulo `flask`:

```py
from flask import request
```

O método de requisição atual está disponível através do atributo `method`. Para acessar dos dados do formulário (dados enviados através de uma requisição `POST` ou `PUT`) você pode usar o atributo `form`. Aqui está um exemplo completo dos dois atributos mencionados acima:

```py
@app.route('/login', methods=['POST', 'GET'])
def login():
    error = None
    if request.method == 'POST':
        if valid_login(request.form['username'],
                       request.form['password']):
            return log_the_user_in(request.form['username'])
        else:
            error = 'Invalid username/password'
    # o código abaixo será executado se o método da requisição
    # for GET ou as credenciais forem inválidas
    return render_template('login.html', error=error)
```

O que acontece se a chave não existir dentro do atributo `form`? Neste caso um **`KeyError`** especial levantado. Você pode captura-lo igual a um **`KeyError`** padrão, mas se você não fizer isso, um erro HTTP 400 de Requisição Mal-feita será exibido na página. Mas para a maioria das situações você não tem que lidar com esse problema.

Para acessar parâmetros enviados em uma URL (`?key=value`) você pode usar o atributo **`args`**:

```py
searchword = request.args.get('key', '')
```

Recomendamos o acesso aos parâmetros da URL com *get* ou pela captura do **`KeyError`** porque os usuários podem mudar a URL e apresenta-los uma página de erro HTTP 400 Requisição Mal-feita nesse caso não seria amigável com o usuário.

Para uma lista de métodos completa e atributos do objeto da requisição, siga para a documentação do **`Request`**.


### Carregamento de Ficheiro

Você pode manipular ficheiros carregados facilmente com o Flask. Apenas certifique-se de não esquecer the configurar o atributo `enctype="multipart/form-data"` em seu formulário HTML, senão o browser não enviará de todo seus ficheiros.

Ficheiros carregados são armazenados na memória ou em um local temporário no sistema de ficheiros. Você pode acessar esses ficheiros buscando por eles no atributo **`files`** do objeto da requisição. Cada ficheiro carregado é armazenado dentro daquele dicionário. Ele se comporta como o objeto **`file`** padrão do Python, porém ele tem um método **`save()`** que permite você guardar aquele ficheiro no sistema de ficheiros do servidor. Aqui está um exemplo simples que mostra como isso funciona:

```py
from flask import request

@app.route('/upload', methods=['GET', 'POST'])
def upload_file():
    if request.method == 'POST':
        f = request.files['the_file']
        f.save('/var/www/uploads/uploaded_file.txt')
    ...
```

Se você quiser saber como o ficheiro foi nomeado no cliente antes de ele ser carregado para a sua aplicação, você pode acessar o atributo **`filename`**. Sempre, por favor tenha em mente que esse valor pode ser esquecido então nunca, mas nunca confie naquele valor. Se você quiser usar o nome do ficheiro do cliente para guardar o ficheiro no servidor, passe-o através da função **`secure_filename()`** que o Werkzeug oferece a você:

```py
from flask import request
from werkzeug.utils import secure_filename

@app.route('/upload', methods=['GET', 'POST'])
def upload_file():
    if request.method == 'POST':
        f = request.files['the_file']
        f.save('/var/www/uploads/' + secure_filename(f.filename))
    ...
```

Para exemplos melhores de como fazer carregamento de ficheiros, consulte o padrão de [Carregamento de Ficheiros](#carregando-ficheiros).

### Cookies

Para acessar os cookies você pode usar o atributo **`cookies`**. Para configurar os cookies você pode usar o método **`set_cookie`** do objeto da resposta. O atributo **`cookies`** do objeto da requisição é um dicionário com todos os cookies enviados pelo cliente. Se você quiser usar sessions(sessões), não use os cookies diretamente mas ao invés disso use [Sessões](#Sessões) no Flask que adiciona alguma segurança em cima dos cookies por você.

Lendo os cookies:

```py
from flask import request

@app.route('/')
def index():
    username = request.cookies.get('username')
    # use cookies.get(key) ao invés de cookies[key] para não
    # receber um KeyError se o cookie estiver em falta
    # ou não existir.
```

Armazenando os cookies:

```py
from flask import make_response

@app.route('/')
def index():
    resp = make_response(render_template(...))
    resp.set_cookie('username', 'the username')
    return resp
```

Note que os cookies são configurados no objeto da resposta. Já que normalmente você apenas retorna strings a partir funções da view(visualização) o Flask converterá eles para objeto da resposta por você. Se você explicitamente quiser fazer isso você pode usar a função **`make_response()`** e então modifica-los.


Algumas vezes você quererá configurar um cookie em um ponto onde o objeto da resposta ainda não exista. Isso é possível através do uso do padrão de [Resposta para Requisições Adiadas](#resposta-para-requisições-adiadas).

Para isso veja também [Sobre Respostas](#sobre-respostas).

## Redireção e Erros

Para redirecionar um usuário para outro ponto final (endpoint), use a função **`redirect()`**; Para abortar a requisição antecipadamente com um código de erro, use a função **`abort()`**:

```py
from flask import abort, redirect, url_for

@app.route('/')
def index():
    return redirect(url_for('login'))

@app.route('/login')
def login():
    abort(401)
    this_is_never_executed()
```

Este exemplo é meio sem sentido visto que os usuários serão redirecionados do index para uma página que eles não podem acessar (401 significa "acesso negado") mas ele mostra como a coisa funciona.

Por padrão uma página de erro a preto e branco é exibida para cada erro no código. Se você quiser personalizar a página de erro, você pode usar o decorador **`errorhandler()`**:

```py
from flask import render_template

@app.errorhandler(404)
def page_not_found(error):
    return render_template('page_not_found.html'), 404
```

Note que o `404` é depois da chamada da função **`render_template()`**. Isso diz ao Flask que o código do estado (status code) daquela página seria `404` que significa "não encontrado". Por padrão 200 se traduz para: "tudo correu bem".

Veja [Manipuladores de Erro](#manipuladores-de-erros) para mais detalhes.


## Sobre Respostas

O valor retornado da função de visualização é automaticamente convertida em um objeto de resposta por você. Se o valor retornado é uma string é convertida em um objeto de resposta com a string como corpo da resposta, um código de estado `200 OK` e um mimetype *text/html*. Se valor retornado é um dicionário (dict), **`jsonify()`** é chamado para produzir uma resposta. A lógica que o Flask aplica para converter valores retornados em um objetos de resposta é como os seguintes:

1. Se um objeto de resposta do tipo correto é retornado é diretamente da view.
2. Se é uma string, um objeto de resposta é criado com aquele dado e os parâmetros padrão.
3. Se é um dicionário, um objeto de resposta é criado usando `jsonify`.
4. Se uma tupla for retornada os itens na tupla podem oferecer informações extra. Tais tuplas tem de ser neste formato `(response, status)`, `(response, headers)`, ou `(response, status, headers)`. O valor de `status` sobrescreverá o código do estado e `headers` pode ser uma lista ou dicionário de valores adicionais do cabeçalho.
5. Se nada disso funcionar, o Flask assumirá o valor retornado é uma aplicação WSGI e converte-o em um objeto de resposta.

Se você quiser obter o objeto de resposta resultante dentro da view, você pode usar a função **`make_response()`**.

Imagine que você tem uma view como esta:

```py
@app.errorhandler(404)
def not_found(error):
    return render_template('error.html'), 404
```

Você apenas precisa envolver a expressão retornada por **`make_response()`** e receber o objeto de resposta para modifica-lo, e então retorna-lo:

```py
@app.errorhandler(404)
def not_found(error):
    resp = make_response(render_template('error.html'), 404)
    resp.headers['X-Something'] = 'A value'
    return resp
```

### APIs com JSON

Um formato de resposta comum quando se está escrevendo uma API é o JSON. É fácil começar a escrever tal API com o Flask. Se você retornar um `dict` de uma view, ele será convertido em uma resposta JSON.

```py
@app.route("/me")
def me_api():
    user = get_current_user()
    return {
        "username": user.username,
        "theme": user.theme,
        "image": url_for("user_image", filename=user.image),
    }
```

Dependendo do desenho da sua API, você pode querer criar respostas JSON para outros além do `dict`. Neste caso, use a função **`jsonify()`**, que serializará qualquer tipo de dados JSON suportado. Ou busque dentro da comunidade Flask, extensões que suportam aplicações mais complexas.

```py
@app.route("/users")
def users_api():
    users = get_all_users()
    return jsonify([user.to_json() for user in users])
```

## Sessões

Em adição ao objeto da requisição existem também um segundo objeto chamado **`session`** que permite você armazenar informações especificas para um usuário de uma requisição para a próxima. Isso é implementado em cima dos cookies para você e os símbolos dos cookies criptograficamente. O que isso quer dizer é que o usuários poderiam ver os conteúdos de seu cookies porém não modifica-los, a menos que eles conheçam a chave secreta usada para acessar.

Em regra para usar sessões você tem que configurar um chava secreta, Aqui está um exemplo de como as sessões funcionam:

```py
from flask import Flask, session, redirect, url_for, request
from markupsafe import escape

app = Flask(__name__)

# Configure a chave secreta para algo número de bytes aleatórios. Mantenha isso realmente em segredo.
app.secret_key = b'_5#y2L"F4Q8z\n\xec]/'

@app.route('/')
def index():
    if 'username' in session:
        return 'Logged in as %s' % escape(session['username'])
    return 'You are not logged in'

@app.route('/login', methods=['GET', 'POST'])
def login():
    if request.method == 'POST':
        session['username'] = request.form['username']
        return redirect(url_for('index'))
    return '''
        <form method="post">
            <p><input type=text name=username>
            <p><input type=submit value=Login>
        </form>
    '''

@app.route('/logout')
def logout():
    # remova o nome do usuário se tiver, da sessão
    session.pop('username', None)
    return redirect(url_for('index'))
```

O método **`escape()`** mencionado aqui faz o escapamento por você se você não estiver usando o gerador de modelo de marcação (como neste exemplo).

> ## Como gerar um boas chaves secretas:
>
> Uma chave secreta deveria ser a mais aleatória possível. Seu sistema operacional tem maneiras de gerar dados bons e aleatórias baseados em um gerador aleatório criptográfico. Use os seguintes comandos para rapidamente gerar um valor para **`Flask.secret_key`** (ou **`SECRET_KEY`**):
> 
> $ `python -c 'import os; print(os.urandom(16))'`
>
> b'_5#y2L"F4Q8z\n\xec]/'

Uma nota em sessões baseadas em cookies: O Flask levará os valores que você pôs dentro do objeto da sessão e serializa-lo-as dentro de m cookie. Se você encontrando alguns valores que não persistem ao longo da requisição, os cookies estão realmente ativados, e você não está recebendo um mensagem de erro clara, verifique o tamanho do cookie em suas respostas da página comparadas ao tamanho suportado pelos web browsers.

Ao contrário do padrão de sessões do lado do cliente, se você quiser manipular sessões no lado do servidor, existem várias extensões do Flask que a suportam.


## Mensagem de Alerta

Boas interfaces de usuário e aplicações tem tudo haver com feedback(resposta). Se os usuários não receberem respostas suficientes eles irão provavelmente acabar odiando a aplicação. O Flask oferece uma maneira realmente simples de dar resposta a um usuário com o sistema de alerta. O sistema de alerta basicamente tornam possível armazenar uma mensagem no fim de uma requisição e acessa-la na proxima (e somente na proxima) requisição. Isso é usualmente combinado com um layout de modelo de marcação para expor a mensagem.

Para alertar uma mensagem use o método **`flash()`**, para ter acesso a mensagem você pode usar **`get_flashed_messages()`** que está também disponível nos templates. Consulte a [Mensagem de Alerta](#mensagem-de-alerta) para ter um exemplo completo.

## Logging

* **Relatório de Mudança**:
    * Novo desde a versão 0.3.

Algumas vezes você poderia estar em uma situação onde você lida com dados que deveriam estar corretos, mas não estão. Por exemplo você pode ter algum código no lado do cliente que envia uma requisição HTTP para o servidor mas está obviamente mal-formatado. Isso pode ser causado por um usuário mexendo com os dados, ou o código do lado do cliente falhando. A maioria das vezes está bem responder com `400 Bad Request (Requisição Mal Feita)` nesta situações, mas algumas vezes não ira querer fazer e o código tem de continuar funcionando.

Você pode continuar querer registrar que alguma coisa de errado aconteceu. É aqui onde loggers vem para dar uma mãozinha. Como no Flask 0.3 um logger é pré-configurado para você usar.

Aqui está alguns exemplos de chamadas registadoras:

```py
app.logger.debug('A value for debugging')
app.logger.warning('A warning occurred (%d apples)', 42)
app.logger.error('An error occurred')
```

O **`logger`** atrelado é um **`Logger`** padrão de logging, então siga para documentação oficial de **`logging`** para mais informações.

Leia mais em [Erros da Aplicação](#erros-da-aplicação).

## Intercetando em WSGI Middleware

Para adicionar um WSGI Middleware a sua aplicação Flask, envolva o atributo `wsgi_app` da aplicação. Por exemplo, para aplicar **`ProxyFix`** middleware do Werkzeug para executar por trás do Nginx:

```py
from werkzeug.middleware.proxy_fix import ProxyFix
app.wsgi_app = ProxyFix(app.wsgi_app)
```

Envolvendo o `app.wsgi_app` ao invés de `app` significa que `app` continua apontando para a sua aplicação Flask, e não para o middleware, então você pode continuar usar e configurar `app` diretamente.

## Usando Extensões do Flask

Extensões são pacotes que ajudam você a concluir tarefas comuns. Por example, Flask-SQLAlchemy fornece suporte ao SQLAlchemy que torna simples e fácil o seu uso com Flask.

Para saber mais sobre extensões no Flask, dê uma olhada em [Extensões](#extensões).

## Instalar em um Servidor Web

Pronto para instalar sua nova aplicação Flask? Vá para [Opções de Instalação](#opções-de-instalação).