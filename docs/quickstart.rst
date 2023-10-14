Introdução Rápida
=================

Podemos começar? Esta página dar-nos-á uma boa introdução à Flask. Primeiramente siga a :doc:`installation` para configurar um projeto e instalar a Flask.


Um Aplicação Mínima
---------------------

Uma aplicação de Flask mínima parece-se com isto:

.. code-block:: python

    from flask import Flask

    app = Flask(__name__)

    @app.route("/")
    def hello_world():
        return "<p>Hello, World!</p>"

Então, o que é que este código faz?

1.  Primeiro, importamos a classe :class:`~flask.Flask`. Uma instância desta classe será a nossa aplicação de WSGI.
2.  Depois criamos uma instância desta classe. O primeiro argumento é o nome do módulo ou pacote da aplicação. ``__name__`` é um atalho conveniente para isto que é apropriado para a maioria dos casos. Isto é necessário para que a Flask saiba onde procurar por recursos tais como modelos de marcação e ficheiros estáticos.
3.  Em seguida usamos o decorador :meth:`~flask.Flask.route` para dizer a Flask qual URL deveria acionar a nossa função.
4.  A função retorna a mensagem que queremos exibir no navegador do utilizador. O tipo de conteúdo padrão é HTML, então o HTML na sequência de caracteres será desenhado pelo navegador.

Guarde-o como :file:`hello.py` ou algo semelhante. Certifica-te de não chamar a tua aplicação de :file:`flask.py` porque isto entraria em conflito com a própria Flask.

Para executar a aplicação, usemos o comando ``flask`` ou ``python -m flask``. Nós precisamos dizer a Flask onde a nossa aplicação está com a opção ``--app``.

.. code-block:: text

    $ flask --app hello run
     * Serving Flask app 'hello'
     * Running on http://127.0.0.1:5000 (Press CTRL+C to quit)

.. admonition:: Comportamento de Descoberta de Aplicação

    Como um atalho, se o ficheiro for nomeado ``app.py`` ou ``wsgi.py``, não temos de usar ``--app``. Consulte a :doc:`/cli` por mais detalhes.

Isto lança um servidor embutido muito simples, que é bom o suficiente para testes mas provavelmente não o que queremos usar em produção. Para opções de implementação em produção consulte a :doc:`deploying/index`.

Agora podemos acessar a http://127.0.0.1:5000/, e deveríamos ver a nossa saudação de olá mundo.

Se um outro programa estiver a usar a porta 5000, veremos ``OSError: [Errno 98]`` ou ``OSError: [WinError 10013]`` quando o servidor tentar iniciar. Consulte a :ref:`address-already-in-use` por como lidar com isto.

.. _public-server:

.. admonition:: Servidor Visível Externamente

   Se executarmos o servidor notaremos que o servidor apenas está acessível a partir no nosso próprio computador, não a partir de qualquer outro na rede. Isto é o padrão porque no modo de depuração um utilizador da aplicação pode executar código de Python arbitrário no nosso computador.

   Se tivermos o depurador desativado ou confiarmos nos utilizadores na nossa rede, podemos tornar o servidor disponível publicamente simplesmente adicionado ``--host=0.0.0.0`` à linha de comando::

       $ flask run --host=0.0.0.0

   Isto diz ao nosso sistema operativo para ouvir todos os IPs públicos.


Modo de Depuração
-----------------

O comando ``flask run`` pode fazer mais do que apenas iniciar o servidor de desenvolvimento. Ao ativar o modo de depuração, o servidor recarregará automaticamente se o código mudar, e mostrará um depurador interativo no navegador se um erro ocorrer durante uma requisição.

.. image:: _static/debugger.png
    :align: center
    :class: screenshot
    :alt: The interactive debugger in action.

.. warning::

    O depurador permite executar código de Python arbitrário a partir do navegador. É protegido por um pino, mas ainda representa um grande risco de segurança. Não execute o servidor de desenvolvimento ou depurador num ambiente de produção.

Para ativar o modo de depuração, usamos a opção ``--debug``.

.. code-block:: text

    $ flask --app hello run --debug
     * Serving Flask app 'hello'
     * Debug mode: on
     * Running on http://127.0.0.1:5000 (Press CTRL+C to quit)
     * Restarting with stat
     * Debugger is active!
     * Debugger PIN: nnn-nnn-nnn

Consulte também:

-   :doc:`/server` e :doc:`/cli` por informações sobre a execução no modo de depuração.
-   :doc:`/debugging` por informações sobre o uso do depurador embutido e outros depuradores.
-   :doc:`/logging` e :doc:`/errorhandling` para registar erros e exibir páginas de erro bonitas.


Escapes de HTML
---------------

Quando retornamos HTML (o tipo de resposta padrão na Flask), quaisquer valores fornecidos pelo utilizador desenhado na saída deve ser escapado para proteger de ataques de injeção. Os modelos de marcação de HTML desenhados com a Jinja, introduzida depois, farão isto automaticamente.

:func:`~markupsafe.escape`, mostrada neste exemplo, pode ser usada manualmente. É omitida na maioria dos exemplos por questões de brevidade, mas devemos sempre estar cientes de como estamos a usar dados não confiáveis.

.. code-block:: python

    from markupsafe import escape

    @app.route("/<name>")
    def hello(name):
        return f"Hello, {escape(name)}!"

Se um utilizador conseguiu submeter o ``<script>alert("bad")</script>``, o escapamento faz isto ser desenhado como texto, ao invés de executar o programa no navegador do utilizador.

``<name>`` na rota captura um valor a partir da URL e passa-o à função de visão. Estas regras de variável são explicadas abaixo.


Roteamento
----------

As aplicações de Web modernas usam URLs significativas para ajudar os utilizadores. Os utilizadores estão mais propensos a gostarem duma página e voltar se a página usar uma URL significativa que eles podem lembrar e usar para visitar diretamente uma página.

Usamos o decorador :meth:`~flask.Flask.route` para vincular uma função à uma URL. ::

    @app.route('/')
    def index():
        return 'Index Page'

    @app.route('/hello')
    def hello():
        return 'Hello, World'

Nós podemos fazer mais! Nós podemos tornar as partes da URL dinâmicas e anexar várias regras à uma função.

Regras de Variáveis
```````````````````

Nós podemos adicionar seções variáveis à uma URL marcando as seções com ``<variable_name>``. Nossa função depois recebe a ``<variable_name>`` como argumento chave. Opcionalmente, podemos usar um conversor para especificar o tipo do argumento como ``<converter:variable_name>``. ::

    from markupsafe import escape

    @app.route('/user/<username>')
    def show_user_profile(username):
        # mostrar o perfil do utilizador para o utilizador
        return f'User {escape(username)}'

    @app.route('/post/<int:post_id>')
    def show_post(post_id):
        # mostrar a publicação com o dado id, o id é um inteiro
        return f'Post {post_id}'

    @app.route('/path/<path:subpath>')
    def show_subpath(subpath):
        # mostrar o sub-caminho depois de /path/
        return f'Subpath {escape(subpath)}'

Tipos de conversor:

========== ==========================================
``string`` (padrão) aceita qualquer texto sem uma barra
``int``    aceita inteiros positivos
``float``  aceita valores de ponto flutuante positivos
``path``   como uma ``string`` mas também aceita barras
``uuid``   aceita sequências de caracteres de UUID
========== ==========================================


URL Únicas / Comportamento de Redirecionamento
``````````````````````````````````````````````

As duas regras seguintes diferente em seu uso duma barra à direita. ::

    @app.route('/projects/')
    def projects():
        return 'The project page'

    @app.route('/about')
    def about():
        return 'The about page'

A URL canónica para o destino ``projects`` tem uma barra à direita. É semelhante à uma pasta num sistema de ficheiro. Se quisermos acessar a URL sem uma barra à direita (``/projects``), a Flask redireciona-nos à uma URL canónica com a barra à direita (``/projects/``).

A URL canónica para o destino ``about`` não tem uma barra à direita. É semelhante ao nome do caminho dum ficheiro. Acessar a URL com uma barra à direita (``/about/``) produz um erro "Não Encontrado" 404. Isto ajuda a manter as URLs únicas para estes recursos, o que ajuda os motores de pesquisa a evitarem indexarem a mesma página duas vezes.


.. _url-building:

Construção duma URL
```````````````````

Para construir uma URL para uma função específica, usamos a função :func:`~flask.url_for`. Ela aceita o nome da função como seu primeiro argumento e qualquer número de argumentos de chaves, cada um correspondendo à uma parte variável da regra da URL. As partes variáveis desconhecidas são anexadas às URLs como parâmetros de consulta.

Porquê quereríamos construir URLs usando a função de reversão de URL :func:`~flask.url_for` ao invés de escrevê-las manualmente nos nossos modelos de marcação?

1. Inverter é muitas vezes mais descritivo do que escrever manualmente as URLs.
2. Nós podemos mudar as nossas URLs duma só vez ao invés de precisar de lembrar de manualmente mudar as URLs escritas manualmente.
3. A construção da URL lida com escapamento dos caracteres especiais de maneira transparente.
4. Os caminhos gerados são sempre absolutos, evitando comportamentos inesperados dos caminhos relativos nos navegadores.
5. Se a nossa aplicação for colocada fora da raiz da URL, por exemplo, em ``/myapplication`` ao invés de ``/``, :func:`~flask.url_for` lida com isto apropriadamente para nós.

Por exemplo, eis como usamos o método :meth:`~flask.Flask.test_request_context` para testar a :func:`~flask.url_for`. :meth:`~flask.Flask.test_request_context` diz à Flask para comportar-se como se estivesse a lidar com uma requisição mesmo enquanto usamos uma concha da Python. Consulte a :ref:`context-locals`.

.. code-block:: python

    from flask import url_for

    @app.route('/')
    def index():
        return 'index'

    @app.route('/login')
    def login():
        return 'login'

    @app.route('/user/<username>')
    def profile(username):
        return f'{username}\'s profile'

    with app.test_request_context():
        print(url_for('index'))
        print(url_for('login'))
        print(url_for('login', next='/'))
        print(url_for('profile', username='John Doe'))

.. code-block:: text

    /
    /login
    /login?next=/
    /user/John%20Doe


Métodos de HTTP
```````````````

As aplicações de Web usam diferentes métodos de HTTP quando acessamos as URLs. Nós mesmos devemos familiarizar-nos com os métodos de HTTP conforme trabalhamos com a Flask. Por padrão, uma rota apenas responde à requisições de ``GET``. Nós podemos usar o argumento ``methods`` do decorador :meth:`~flask.Flask.route` para lidar com diferentes métodos de HTTP. ::

    from flask import request

    @app.route('/login', methods=['GET', 'POST'])
    def login():
        if request.method == 'POST':
            return do_the_login()
        else:
            return show_the_login_form()

O exemplo acima mantém todos os métodos para rota dentro duma função, o que pode ser útil se cada parte usar algum dado em comum.

Nós também podemos separar as visões para diferentes métodos em funções diferentes. A Flask fornece um atalho para decorar tais rotas com :meth:`~flask.Flask.get`, :meth:`~flask.Flask.post`, etc. para cada método de HTTP comum:

.. code-block:: python

    @app.get('/login')
    def login_get():
        return show_the_login_form()

    @app.post('/login')
    def login_post():
        return do_the_login()

Se ``GET`` estiver presente, a Flask adiciona automaticamente suporte para o método ``HEAD`` e manipula requisições de ``HEAD`` de acordo com a `HTTP RFC`_. Da mesma maneira, ``OPTIONS`` é implementada automaticamente para nós.

.. _HTTP RFC: https://www.ietf.org/rfc/rfc2068.txt

Ficheiros Estáticos
-------------------

As aplicações de Web dinâmicas também precisam de ficheiros estáticos. É normalmente de onde os ficheiros de CSS e JavaScript estão a vir. Idealmente o nosso servidor de Web é configurado para servi-los para nós, mas durante o desenvolvimento a Flask também pode fazer isto. Só precisamos criar uma pasta chamada :file:`static` no nosso pacote ou próximo ao nosso módulo e estará disponível em ``/static`` na aplicação.

Para gerar as URLs para os ficheiros estáticos, usamos nome de destino ``'static'`` especial::

    url_for('static', filename='style.css')

O ficheiro tem de ser armazenado no sistema de ficheiro como :file:`static/style.css`.

Interpretação dos Modelos de Marcação
-------------------------------------

Gerar o HTML a partir de dentro da Python não é divertido, e de fato é muito desconfortável porque temos de fazer o escapamento do HTML por conta própria para manter a aplicação segura. Por causa disto a Flask configura o motor de modelo de marcação `Jinja2 <https://palletsprojects.com/p/jinja/>`_ para nós automaticamente.

Os modelos de marcação podem ser usados para gerar qualquer tipo de ficheiro de texto. Para aplicações de Web, estaremos essencialmente a gerar páginas de HTML, mas também podemos gerar markdown, textos simples para correios-eletrónicos, e mais alguma coisa.

Para uma referência ao HTML, CSS, e outras APIs da Web, usamos a `MDN Web Docs`_.

.. _MDN Web Docs: https://developer.mozilla.org/

Para interpretar um modelo de marcação, podemos usar o método :func:`~flask.render_template`. Tudo o que temos de fazer é fornecer o nome do modelo de marcação e as variáveis que queremos passar ao motor de modelo de marcação como argumentos de chaves. Eis um exemplo simples de como interpretar um modelo de marcação: ::

    from flask import render_template

    @app.route('/hello/')
    @app.route('/hello/<name>')
    def hello(name=None):
        return render_template('hello.html', name=name)

A Flask procurará pelos modelos de marcação na pasta :file:`templates`. Então se a nossa aplicação for um módulo, esta pasta está próxima deste módulo, se for um pacote está de fato dentro do nosso pacote:

**Caso 1**: um módulo::

    /application.py
    /templates
        /hello.html

**Caso 2**: um pacote::

    /application
        /__init__.py
        /templates
            /hello.html

Para os modelos de marcação podemos usar o poder total dos modelos de marcação da Jinja2. Siga para a `Documentação do Modelo de Marcação da Jinja2 <https://jinja.palletsprojects.com/templates/>`_ oficial para mais informação.

Eis um modelo de marcação de exemplo:

.. sourcecode:: html+jinja

    <!doctype html>
    <title>Hello from Flask</title>
    {% if name %}
      <h1>Hello {{ name }}!</h1>
    {% else %}
      <h1>Hello, World!</h1>
    {% endif %}

Dentro dos modelos de marcação também podemos acessar os objetos :data:`~flask.Flask.config`, :class:`~flask.request`, :class:`~flask.session` e :class:`~flask.g` [#]_, bem como as funções :func:`~flask.url_for` e :func:`~flask.get_flashed_messages`.

Os modelos de marcação são especialmente úteis se herança for usada. Se quisermos saber como isto funciona, devemos consultar a :doc:`patterns/templateinheritance`. Basicamente a herança de modelo de marcação possibilita manter certos elementos em cada página (como cabeçalho, navegação e rodapé).

O escapamento automático está ativado, então se ``name`` contiver HTML será escapado automaticamente. Se podemos confiar numa variável e sabemos que será HTML seguro (por exemplo, porque vem a partir dum módulo que converte a marcação da wiki para HTML) podemos marcá-la como segura usando a classe :class:`~markupsafe.Markup` ou usando o filtro ``|safe`` no modelo de marcação.

Eis uma introdução básica à como a classe :class:`~markupsafe.Markup` funciona::

    >>> from markupsafe import Markup
    >>> Markup('<strong>Hello %s!</strong>') % '<blink>hacker</blink>'
    Markup('<strong>Hello &lt;blink&gt;hacker&lt;/blink&gt;!</strong>')
    >>> Markup.escape('<blink>hacker</blink>')
    Markup('&lt;blink&gt;hacker&lt;/blink&gt;')
    >>> Markup('<em>Marked up</em> &raquo; HTML').striptags()
    'Marked up » HTML'

.. versionchanged:: 0.5

   O auto-escapamento já não está ativado para todos os modelos de marcação. As seguintes extensões para os modelos de marcação acionam o auto-escapamento: ``.html``, ``.htm``, ``.xml``, ``.xhtml``. Os modelos de marcação carregados a partir duma sequência de caracteres terão o auto-escapamento desativado.

.. [#] Não tens a certeza do que o objeto :class:`~flask.g` é? É algo no qual podemos armazenar informação para as nossas próprias necessidades. Consulte a documentação para :class:`flask.g` e :doc:`patterns/sqlite3`.


Acessando os Dados da Requisição
--------------------------------

Para as aplicações da Web é crucial reagir aos dados que um cliente envia ao servidor. Na Flask esta informação é fornecida através do objeto :class:`~flask.request` global. Se tivermos alguma experiência com a Python podemos estar a perguntar para nós mesmos como este objeto pode ser global e como a Flask ainda consegue manter-se segura (threadsafe). Uma resposta é locais de contexto:


.. _context-locals:

Locais de Contexto
``````````````````

.. admonition:: Informação de Alguém de Dentro

   Se quisermos entender como isto funciona e como podemos implementar testes com os locais de contexto, devemos ler esta seção, de outro modo podemos saltá-la.

Certos objetos na Flask são objetos globais, mas não da maneira habitual. Estes objetos são de fato delegações aos objetos que são locais à um contexto específico. Que garfada. Mas isto é de fato muito fácil de entender.

Imagina o contexto sendo a linha de manipulação. Uma requisição chega e o servidor da Web decide gerar uma nova linha (ou outra coisa, o objeto adjacente é capaz de lidar com os sistema de concorrência para além das linhas). Quando a Flask começar a sua manipulação de requisição interna compreende que a linha atual é o contexto ativo e vincula a aplicação atual e os ambientes de WSGI àquele contexto (linha). Ela faz isto duma maneira inteligente para que uma aplicação possa invocar uma outra aplicação sem rutura.

E o que isto significa para nós? Basicamente podemos ignorar completamente que isto é o caso a menos que estejamos a fazer algo como testes unitários. Nós notaremos que o código que depende dum objeto de requisição quebrará de repente porque não existe nenhum objeto de requisição. A solução é nós mesmos criarmos um objeto de requisição e vinculá-lo ao contexto. A solução mais fácil para testes unitários é usar o gestor de contexto :meth:`~flask.Flask.test_request_context`. Em conjunto com a declaração ``with`` vinculará uma requisição de teste para que possamos interagir com o mesmo. Eis um exemplo::

    from flask import request

    with app.test_request_context('/hello', method='POST'):
        # agora podemos fazer algo com a requisição até o
        # final do bloco `with`, tais como asserções básicas:
        assert request.path == '/hello'
        assert request.method == 'POST'

A outra possibilidade é passar um ambiente de WSGI inteiro ao método :meth:`~flask.Flask.request_context`::

    with app.request_context(environ):
        assert request.method == 'POST'

O Objeto de Requisição
``````````````````````

O objeto de requisição está documentado na seção da API e não o cobriremos em detalhes (consulte :class:`~flask.Request`). Eis uma visão geral aberta dalgumas das operações mais comuns. Em primeiro lugar temos de importá-lo a partir do módulo ``flask``::

    from flask import request

O método da requisição atual está disponível usando o atributo :attr:`~flask.Request.method`. Para acessar os dados do formulário (os dados transmitidos numa requisição ``POST`` ou ``PUT``) podemos usar o atributo :attr:`~flask.Request.form`. Eis um exemplo completo dos dois atributos mencionados acima::

    @app.route('/login', methods=['POST', 'GET'])
    def login():
        error = None
        if request.method == 'POST':
            if valid_login(request.form['username'],
                           request.form['password']):
                return log_the_user_in(request.form['username'])
            else:
                error = 'Invalid username/password'
        # o código abaixo é executado se o método da requisição
        # era GET ou as credenciais eram inválidas
        return render_template('login.html', error=error)

O que acontece se a chave não existir no atributo ``form``? Neste caso uma :exc:`KeyError` especial é levantada. Nós podemos capturá-lo como uma :exc:`KeyError` padrão mas se não o fizermos, uma página de Má Requisição 400 de HTTP é exibida. Então para muitas situações não temos de lidar com este problema.

Para acessar os parâmetros submetidos na URL (``?key=value``) podemos usar o atributo :attr:`~flask.Request.args`::

    searchword = request.args.get('key', '')

Nós recomendados acessar os parâmetros da URL com `get` ou com a captura da :exc:`KeyError` porque os utilizadores podem mudar a URL e apresentá-los uma página de má requisição 400 neste caso não é agradável.

Para uma lista completa de métodos e atributos do objeto de requisição, siga para a documentação da :class:`~Flask.Request`.


Carregamentos de Ficheiro
`````````````````````````

Nós podemos manipular os ficheiros carregados com a Flask facilmente. Só temos de certificar-nos de não esquecer de definir o atributo ``enctype="multipart/form-data"`` no formulário do nosso HTML, de outro modo o navegador não transmitirá os nossos ficheiros.

Os ficheiros carregados são armazenados na memória ou numa localização temporária no sistema de ficheiro. Nós podemos acessar estes ficheiros olhando no atributo :attr:`~flask.request.files` no objeto de requisição. Cada ficheiro carregado é armazenado neste dicionário. Ele comporta-se tal como um objeto :class:`file` da Python padrão, mas também tem um método :meth:`~werkzeug.datastructures.FileStorage.save` que permite-nos armazenar este ficheiro no sistema de ficheiro do servidor. Eis um simples exemplo mostrando como isto funciona::

    from flask import request

    @app.route('/upload', methods=['GET', 'POST'])
    def upload_file():
        if request.method == 'POST':
            f = request.files['the_file']
            f.save('/var/www/uploads/uploaded_file.txt')
        ...


Se quisermos saber como o ficheiro foi nomeado no cliente antes dele ser carregado para nossa aplicação, podemos acessar o atributo :attr:`~werkzeug.datastructures.FileStorage.filename`. No entanto, devemos manter em mente que este valor pode ser esquecido então nunca devemos confiar neste valor. Se quisermos usar o nome de ficheiro do cliente para armazenar o ficheiro no servidor, o passamos através da função :func:`~werkzeug.utils.secure_filename` que a Werkzeug fornece para nós::

    from werkzeug.utils import secure_filename

    @app.route('/upload', methods=['GET', 'POST'])
    def upload_file():
        if request.method == 'POST':
            file = request.files['the_file']
            file.save(f"/var/www/uploads/{secure_filename(file.filename)}")
        ...

Para melhores exemplos, consulte :doc:`patterns/fileuploads`.

Cookies
```````

Para acessar os cookies podemos usar o atributo :attr:`~flask.Request.cookies`. Para definir os cookies podemos usar o método :attr:`~flask.Response.set_cookie` dos objetos de resposta. O atributo :attr:`~flask.Request.cookies` dos objetos da requisição é um dicionário com todos os cookies que o cliente transmite. Se quisermos usar as sessões, não usamos os cookies diretamente mas ao invés deste usamos as :ref:`sessions` na Flask que adiciona alguma segurança sobre os cookies para nós.

Lendo os cookies::

    from flask import request

    @app.route('/')
    def index():
        username = request.cookies.get('username')
        # usar `cookies.get(key)` ao invés de cookies[key] para
        # não receber uma `KeyError` se o cookie estiver em falta

Armazenando os cookies::

    from flask import make_response

    @app.route('/')
    def index():
        resp = make_response(render_template(...))
        resp.set_cookie('username', 'the username')
        return resp

Nota que os cookies são definidos sobre os objetos de resposta. Uma vez que normalmente apenas retornamos sequências de caracteres a partir das funções de visão, a Flask os converterá em objetos de resposta por nós. Se explicitamente quisermos fazer isto podemos usar a função :meth:`~flask.make_response` e modificá-la.

Algumas vezes podemos querer definir um cookie num ponto onde o objeto de resposta ainda não existe. Isto é possível usando o padrão :doc:`patterns/deferredcallbacks`.

Para isto consulte também :ref:`about-responses`.

Redirecionamentos e Erros
-------------------------

Para redirecionar um utilizador para um outro destino, usamos a função :func:`~flask.redirect`; para abortar uma requisição prematuramente com um código de erro, usamos a função :func:`~flask.abort`::

    from flask import abort, redirect, url_for

    @app.route('/')
    def index():
        return redirect(url_for('login'))

    @app.route('/login')
    def login():
        abort(401)
        this_is_never_executed()

Isto é um exemplo muito inútil porque um utilizador será redirecionado do índice para uma página que não pode acessar (401 significa acesso negado), mas mostra como isto funciona.

Por padrão uma página de erro em preto e branco é exibida para cada código de erro. Se quisermos personalizar a página de erro, podemos usar o decorador :meth:`~flask.Flask.errorhandler`::

    from flask import render_template

    @app.errorhandler(404)
    def page_not_found(error):
        return render_template('page_not_found.html'), 404

Nota o ``404`` depois da chamada de :func:`~flask.render_template`. Isto diz a Flask que o código do estado desta página deve ser 404 o que significa não encontrado. Por padrão, 200 é assumido o que traduz-se para: tudo correu bem.

Consulte :doc:`errorhandling` por mais detalhes.

.. _about-responses:

Sobre Respostas
---------------

O valor de retorno duma função de visão é automaticamente convertido num objeto de resposta para nós. Se o valor de retorno for uma sequência de caracteres é convertido num objeto de resposta com a sequência de caracteres como corpo da resposta, um código de estado ``202 OK`` e um tipo de ficheiro :mimetype:`text/html`. Se o valor de retorno for um dicionário ou lista, :func:`jsonify` é chamada para produzir uma resposta. A lógica que a Flask aplica para conversão de valores de retorno em objetos de resposta é da seguinte maneira:

1.  Se um objeto de resposta do tipo correto for retornado é diretamente retornado a partir duma visão.
2.  Se for uma sequência de caracteres, um objeto de resposta é criado com estes dados e os parâmetros padrão.
3.  Se for um iterador ou gerador retornando sequências de caracteres ou bytes, é tratado como uma resposta de fluxo contínuo.
4.  Se for um dicionário ou lista, um objeto de resposta é criado usando :func:`~flask.json.jsonify`.
5. Se uma tupla for retornada, os itens na tupla podem fornecer informação adicional. Tais tuplas têm de estar no formato ``(response, status)``, ``(response, headers)``, ou ``(response, status, headers)``. O valor de ``status`` sobreporá o código de estado e ``headers`` pode ser uma lista ou dicionário de valores de cabeçalho adicionais.
6.  Se nada disto funcionar, a Flask assumirá que o valor de retorno é uma aplicação de WSGI válida e converterá esta num objeto de resposta.

Se quisermos obter o objeto da resposta resultante dentro da visão, podemos usar a função :func:`~flask.make_response`.

Imaginemos que temos uma visão como esta::

    from flask import render_template

    @app.errorhandler(404)
    def not_found(error):
        return render_template('error.html'), 404

Nós apenas precisamos de envolver a expressão de retorna com a :func:`~flask.make_response` e receber o objeto de resposta para modificá-lo, depois retorná-lo::

    from flask import make_response

    @app.errorhandler(404)
    def not_found(error):
        resp = make_response(render_template('error.html'), 404)
        resp.headers['X-Something'] = 'A value'
        return resp


APIs com JSON
``````````````

Um formato de resposta comum quando escrevemos uma API é o JSON. É fácil começar a escrever tal API com a Flask. Se retornarmos um ``dict`` ou ``list`` a partir duma visão, este será convertido à uma resposta de JSON.

.. code-block:: python

    @app.route("/me")
    def me_api():
        user = get_current_user()
        return {
            "username": user.username,
            "theme": user.theme,
            "image": url_for("user_image", filename=user.image),
        }

    @app.route("/users")
    def users_api():
        users = get_all_users()
        return [user.to_json() for user in users]

Isto é um atalho para passagem de dados à função :func:`~flask.json.jsonify`, a qual serializará qualquer tipo de dado de JSON. Isto significa que todos os dados no dicionário ou lista deve ser JSON serializável.

Para tipos complexos tais como modelos de base de dados, vamos querer usar uma biblioteca de serialização para converter os dados para tipos de JSON válidos primeiro. Existem muitas bibliotecas de serialização e extensões da API da Flask mantidas pela comunidade que suportam aplicações mais complexas.


.. _sessions:

Sessões
--------

Além do objeto da requisição também existe um segundo objeto chamado :class:`~flask.session` que permite-nos armazenar informação específica à um utilizador a partir duma requisição à próxima. Isto é implementado sobre os biscoitos (cookies) para nós e assina os biscoitos de de maneira criptográfica. O que isto significa é que o utilizador poderia olhar o conteúdo do nosso biscoito mas não modificá-lo, a menos que saiba a chave secreta usada para a assinatura.

No sentido de usar as sessões temos de definir uma chave secreta. Eis como as sessões funcionam::

    from flask import session

    # Definir a chave secreta à alguns bytes aleatórios.
    # Mantém isto realmente secreto!
    app.secret_key = b'_5#y2L"F4Q8z\n\xec]/'

    @app.route('/')
    def index():
        if 'username' in session:
            return f'Logged in as {session["username"]}'
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
        # remover o nome do utilizador da sessão se existir
        session.pop('username', None)
        return redirect(url_for('index'))

.. admonition:: Como gerar boas chaves secretas

    Uma chave secreta deveria ser o mais aleatória possível. O nosso sistema operacional tem maneiras de gerar dados muito aleatórios baseado num gerador aleatório criptográfico. Usamos o seguinte comando para gerar rapidamente um valor para o :attr:`Flask.secret_key` (ou :data:`SECRET_KEY`)::

        $ python -c 'import secrets; print(secrets.token_hex())'
        '192b9bdd22ab9ed4d12e236c78afcb9a393ec15f71bbf5dc987d54727823bcbf'

Uma nota sobre as sessões baseadas no biscoito: a Flask receberá os valores que colocarmos num objeto de sessão e os serializará num biscoito. Se verificarmos que alguns valores não persistem entre as requisições, os biscoitos estão efetivamente ativados, e que não estamos a receber uma mensagem de erro clara, temos que verificar o tamanho do biscoito nas respostas da nossa página comparado ao tamanho suportado pelos navegadores da Web.

Para além das sessões baseadas no lado do cliente padrão, se quisermos manipular as sessões no lado do servidor, existem muitas extensões de Flask que suportam isto.

Mensagem Intermitente
----------------------

As boas aplicações e interfaces de utilizador têm tudo a ver com reações. Se o utilizador não receber reações suficientes é provável que acabe por odiar a aplicação. A Flask fornece uma maneira muito simples de reagir à um utilizador com o sistema de intermitência. O sistema de intermitência basicamente torna possível gravar uma mensagem no final duma requisição e acessá-la na próxima (e apenas na próxima) requisição. Isto é normalmente combinado com um modelo de marcação de disposição para expor a mensagem.

Para fazer piscar uma mensagem usamos o método :func:`~flask.flash`, para obter as mensagens podemos usar :func:`~flask.get_flashed_messages` que também está disponível nos modelos de marcação. Consulte a :doc:`patters/flashing` por um exemplo completo.

Registos
--------

.. versionadded:: 0.3

Algumas vezes podemos estar numa situação onde lidamos com dados que deveriam estar corretos, mas na realidade não estão. Por exemplo podemos ter algum código do lado do cliente que envia uma requisição de HTTP ao servidor mas está obviamente malformado. Isto pode ser causado por um utilizador que adultera os dados ou por falha do código do cliente. Na maior parte das vezes não existe problema algum em responder com ``400 Bad Request`` neste situação, mas algumas vezes não será o suficiente e o código tem de continuar a funcionar.

Nós ainda podemos querer registar que algo de estranho aconteceu. Eis onde os registadores tornam-se úteis. Na Flask 3.0 um registador está pré-configurado para nós usarmos.

Eis algumas chamas de registo de exemplo::

    app.logger.debug('A value for debugging')
    app.logger.warning('A warning occurred (%d apples)', 42)
    app.logger.error('An error occurred')

O :attr:`~flask.Flask.logger` anexado é uma :class:`~logging.Logger` de registo padrão, então siga para documentação do :mod:`logging` oficial para mais informações.

Consulte a :doc:`errorhandling`.


Ligações do Intermediário da WSGI
----------------------------------

Para adicionar um intermediário de WSGI à nossa aplicação de Flask, envolvemos o atributo ``wsgi_app`` da aplicação. Por exemplo, para aplicar o intermediário :class:`~werkzeug.middleware.proxy_fix.ProxyFix` da Werkzeug para executar por trás do NGINX:

.. code-block:: python

    from werkzeug.middleware.proxy_fix import ProxyFix
    app.wsgi_app = ProxyFix(app.wsgi_app)

Envolver a ``app.wsgi_app`` ao invés da ``app`` significa que ``app`` ainda aponta para a nossa aplicação de Flask, não para o intermediário, então podemos continuar a usar e configurar a ``app`` diretamente.

Usando Extensões de Flask
-------------------------

As extensões são pacotes que ajudam-nos a realizar tarefas comuns. Por exemplo, a Flask-SQLAlchemy fornece suporte à SQLAlchemy que torna simples e fácil usar com a Flask.

Para mais sobre extensões de Flask, consulte :doc:`extensions`.

Implementação num Servidor da Web
----------------------------------

Pronto para implementar a nossa nova aplicação de Flask em produção? Consulte :doc:`deploying/index`.
