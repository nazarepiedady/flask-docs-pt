# Flask
> desenvolvimento web, uma gota de cada vez

Bem-vindo a documentação do Flask. Começe com a [Instalação](#instalação) e depois tenha uma visão geral com o [Começo Rápido](#começo-rápido).
Existe também um [Tutorial](#tutorial) mais detalhado que lhe mostra como construir uma pequena contudo completa aplicação web usando o Flask.
Na seção [Padrões do Flask](#padrões-do-flask) se oferece descrições a cerca dos padrões de desenvolvimentos mais comuns em aplicações Flask. E o resto da documentações procura descrever com mais propriedade cada componente dentro Flask, tendo uma refêrencia completa na seção da [API](#api).

O Flask possui como dependência, [Jinja](https://www.palletsprojects.com/p/jinja/) como motor de interpretação de documento HTML, e o [Werkzeug](https://www.palletsprojects.com/p/werkzeug/) que é o conjunto de ferramentas da interface de entrada do servidor web, o WSGI toolkit. A documentação para essas duas bibliotecas podem ser achadas em:

* [Documentação do Jinja](http://jinja.pocoo.org/docs)
* [Documentação do Werkzeug](https://werkzeug.palletsprojects.com/)

## Guia do Utilização
Esta parte da documentação, começa por conceder algumas informações fundamentais sobre o Flask, e depois busca focar-se em dar instruções passo a passo de como seria o desenvolvimento web com o uso do Flask.

## Prefácio
Considere ler as informações antes de começar a usar o Flask. Estas informções buscam de maneira simpática responder algumas questões sobre o proposito e objetivos do projeto, e sobre quando você deve ou não usar ele.

## O que "micro" quer realmente dizer?
Com "micro" não se quer dizer que toda sua aplicação comportará apenas um único arquivo Python (algo que não é impossível), muito menos significa que o Flask carece de funcionalidades. O "micro" em microframework significa que o Flask pretende manter o core da aplicação simples, contudo extensível. O Flask não irá tomar as decisões por você, decisões do tipo, qual banco de dados usar. Aquelas decisões que ele faz, tal como qual motor de inpretação de documento HTML (templating engine) usar, podem ser facilmente alteradas. Todo o resto é deixado por tua conta, de maneira que Flask pode ser tudo que você precisa e nada daquelo que você não precisa.

Por padrão, Flask não possui uma camada de abstração para banco de dados, validação de formulário ou qualquer coisa onde já existe diferentes bibliotecas capazes de lidar com essas responsabilidades. Em vez disso, Flask suporta extensões para adicionar tais funcionalidades a sua aplicação como se estás por si mesmas tivessem sido implementadas em Flask. Numerosas extensões provê integração com banco de dados, validação de formulário, upload de arquivos, varias tecnologias de autenticação aberta e muito mais. Flask pode até ser "micro", mas ele está pronto para uso em produção em uma variedade de necessidades.

## Configuração e Convenções
Flask possui varios valores de configuração, com padrões sensíveis, e algumas convenções ao se dar inicio. Por convenção, os templates (\*.html, \*.jinja2, etc) e arquivos estáticos (\*.jpg, \*.png, \*.svg, \*.css, \*.js, etc) são armazenados em subdiretórios dentro da arvore da aplicação Python, com os respectivos nomes `templates` e `static`. Embora essa ordem possa ser mudada, você normalmente não precisa o fazer, especialmente quanto está dando seus primeiros passos na ferramenta.

## Crescendo com Flask
Uma vez tendo o Flask instalado e funcionando, você irá achar uma variedade de extensões disponíveis dentro da comunidade, para integrar em seus projetos em produção. Os mantenedores do Flask revisam as extensões desenvolvidas e garantes que as extensões aprovadas irão quebrar com versões futuras do Flask.

A medida que sua base de código cresce, você está livre para fazer as decisões de design que achar mais apropriado para o seu projeto. Flask irá continuar a prover uma camada mais simples a fim de tirar proveito do melhor que o Python tem a oferecer. Você pode implementar padrões mais avançados em SQLAlchemy ou em outra ferramenta de banco de dados, introduzir persistência de dados não-relacional se for o mais apropriado, e tirar vantagem de framework agnósticos construidos para trabalhar com o WSGI, a interface do Python para a web.

O Flask possui varios ciclos (hooks) para personalizar seu comportamentos. É possível que você precise de mais personalizações extras, A classe do Flask está pronta para ser herdada por subclasses. Se você está interessado isso, verife o assunto no capitulo, [Tornando-se Grande](#tornando-se-grande). Se você tem curiosidade em saber mais sobre os principios de design do Flask, siga para a seção sobre [Decisões de Design dentro Flask](#decisões-de-design-no-flask).

Siga para [Instalação](#instalação), o [Começo Rápido](#começo-rápido), ou o [Prefácio para Programadores Experientes](#prefácio-para-programadores-experientes).

# Thread-Locals dentro do Flask

Uma das decisões de design tomadas no Flask foi que a tarefa que é símples deveria ser símples; elas não deveria conter muitos códigos e nem devem limitar você. Por esta razão, o Flask toma poucas decisões de design o que levaria algumas pessoas a concidera-lo pouco ortodoxo ou mesmo surpreendente. Por exemplo, o Flask usa objetos thread-local internamente então você não precisa passar objetos de função em função dentro de uma requisição para se manter a salvo de thread. Esta melhoria é conveniente, mas requer um contexto de requisição valido para injeção de dependência ou quando tentar reusar o código que usa um valor indexado para a requesição. O projeto Flask é honesto quando se trata de thread-locals, não esconde eles, e chama-os dentro do código e documentação, onde eles são usados.

# Desenvolve para Web com Cautela

Sempre mantenha a segurança em mente quando for construir aplicações web.

Se você escrever uma aplicação web, você está provalvemente permitindo que usuários se registem e deixem seus dados no seu servidor. Os usuários estão confiando em você seus dados. E mesmo se você for o único usuário que deixará seus dados em sua aplicação, você continuaria a querer que seus dados fossem armazenados com segurança.

Infelizmente, existem muitas maneiras de comprometer a segurança de uma aplicação web. O Flask proteje você contra um dos problemas de segurança mais comuns em aplicações web modernas: cross-site scripting (XSS). A menos que você deliberadamente marque código HTML inseguro como seguro, Flask e o gerador de template Jinj2 debaixo dos panos tem você protegido. Porém existem muitas mais maneiras de causar problemas de segurança.

A documentação irá avisar a você sobre os aspectos do desenvolvimento web que requerem segurança. Algumas dessas questões de segurança são muito mais complexas do que se possa pensar, e todos nós algumas vezes subestimamos a probabilidade de que uma vulnerabilidade seja explorada - até que um espertinho descobri uma maneira de explorar nossa aplicação. E nem adianta pensar que sua aplicação não é importante o suficiente para atrair um espertinho. Depedendo do tipo de ataque as chances são que robós automatizados estajam procurando maneiras de preencher seu banco de dados com spam, links para software maliciosos ou algo do tipo.

O Flask não é diferente de nenhum outro framework em que você o desenvolvedor deve construir com cuidado, estando atento a vulnerabilidades exploráveis quando estiver construindo de acordo com os seus requisitos.

# Instalação

## Versão do Python

Nós recomendamos o uso da última versão do Python 3. O Flask suporta o Python 3.5 até as mais recentes, Python 2.7, e o PyPy.

## Depedências

Estas distribuições irão ser instaladas automaticamente quando estiver instalando o Flask.

- [Werkzeug](https://palletsprojects.com/p/werkzeug/) que implementa o WSGI, a interface padrão entre aplicações e servidores.
- [Jinja](https://palletsprojects.com/p/jinja/) é a linguagem de template que renderiza as páginas que sua aplicação serve.
- [MarkupSafe](https://palletsprojects.com/p/markupsafe/) vem com o Jinja. Ele escapa entradas duvidosas quando estiver renderizando os templates, para evitar ataques de injeção.
- [ItsDangerous](https://palletsprojects.com/p/itsdangerous/) seguramente assina os dados para guarantir sua integridade. Isso é usado para proteger as sessões do cookie usados pelo Flask.
- [Click](https://palletsprojects.com/p/click/) é um framework para escrita de aplicações de linha de comando. Ele provê ao Flask ações de linha de comando e permite adicionar novos comandos personalizados.

#### Dependências Opcionais

Estas distribuições não serão automaticamente instaladas. O Flask irá detectar e usa-los se você instala-los.

- [Blinker](https://pythonhosted.org/blinker/) provê suporte para [Signals](#signals)
- [SimpleJSON](https://simplejson.readthedocs.io/) é uma implementação rápida compatível com o módulo `json` do Python. Se estiver instalada, é a preferida para operações evolvendo JSON.
- [python-dotenv](https://github.com/theskumar/python-dotenv#readme) habilita o suporte a [variáveis de ambientes vindas do arquivo dotenv](#dotenv) quando estiver executando o comandos do `Flask`.
- [Watchdog](https://pythonhosted.org/watchdog/) provê um recarregador mais rápido e eficiente ao servidor de desenvolvimento.

## Ambiente Virtual

Use um ambiente virtual para gerenciar as dependências para o seu projeto, tanto as de desenvolvimento quanto as de produção.

Quais problemas um ambiente virtual resolve? Ajuda a manter os vários projetos Python que você tem, aos quais possivelmente estão usando versões de bibliotecas Python diferentes, ou até mesmo versões diferentes do próprio Python. Novas versões de bibliotecas para um projeto podem quebrar a compatibilidade em outro projeto.

Ambientes virtuais são grupos independente de bibliotecas Python, uma para cada projeto. Pacotes instalados a um projeto não irão afetar outros projetos, nem interferir com o funcionamento dos pacotes do sistema operacional.

Python 3 vem embalado com o módulo `venv` para criar ambientes virtuais. Se você estiver usando uma versão moderna do Python, você pode continuar para a seção seguinte.

Se você estiver usando Python 2, consulte primeiro [Instalar o virualenv](#instalar-o-virtualenv).

### Criar um Ambiente

Cria uma pasta para o projeto e uma pasta `venv` dentro:

```sh
$ mkdir myproject
$ cd myproject
$ python3 -m venv venv
```

No Windows:

```sh
$ py -3 -m venv venv
```

Se você precisar instalar o módulo virtualvenv porque está usando o Python 2, use o comando a seguir na vez do de acima:

```sh
$ python2 -m virtualvenv venv
```

No Windows:

```sh
> \Python27\Scripts\virtualvenv.exe venv
```

### Ative o Ambiente

Antes de você trabalhar em seu projeto, ative o ambiente correspondente:

```sh
$ . venv/bin/activate
```

No Windows:

```sh
> venv venv/Scripts/activate
```

A sua interface de linha de comando irá mudar para exibir o nome do ambiente virtual em uso.

## Instale o Flask

Dentro do ambiente virtual em uso, use o seguinte comando para instalar o Flask:

```sh
$ pip install Flask
```

O Flask está agora instalado. Consulte o [Começo Rápido](#começo-rápido) ou [Resumo da Documentação](#resumo-da-documentação).

#### Vivendo no Limite

Se você quiser trabalhar com a última versão do código do Flask mesmo antes deste ser lançado, instale ou atualize o código a partir da ramo principal (master):

```sh
$ pip install -U https://github.com/pallets/flask/archive/master.tar.gz
```

## Instale o virtualenv

Se você estiver usando o Python 2, o módulo `venv` não está disponível para você usar. Ao invés disso, instale o [virtualenv](https://virtualenv.pypa.io/).

No Linux, virtualenv é provido pelo seu gestor de pacotes:

```sh
# Debian, Ubuntu
$ sudo apt-get install python-virtualenv

# CentOS, Fedora
$ sudo yum install python-virtualenv

# Arch
$ sudo pacman -S python-virtualenv
```

Se você estiver no Mac OS X ou Windows, baixe o [get-pip.py](https://bootstrap.pypa.io/get-pip.py), depois:

```sh
$ sudo python2 Download/get-pip.py
$ sudo python2 -m pip install virtualenv
```

No Windows, como usuário administrador:

```sh
> \Python27\python.exe Downloads\get-pip.py
> \Python27\python.exe -m pip install virtualenv
```

Agora você pode voltar para a seção acima e [criar um ambiente](#criar-um-ambiente).

# Começo Rápido

Pronto para começar? Esta página dá um boa introdução ao Flask. Ela assume que você já tem o Flask instalado. Se você ainda não o tiver instalado, siga para a seção [Instalação](#instalação).

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

1. Nós importamos a classe Flask. Uma instãncia dessa classe será nossa aplicação WSGI.
2. Depos nós criamos uma instância da classe. O primeiro argumento é o nome do módulo ou pacote da aplicação. Se você estiver usando um módulo solitário (como neste exemplo), você deveria usar `__name__` porque depedendo se for iniciado como aplicação ou importado como um módulo o nome será diferente ('`__main__`' versos o atual nome importado). Isto é necessário para que então o Flask saiba onde procurar pelos templates, arquivos estáticos, e por aí vai. Para mais informações de uma olhada na documentação do Flask.
3. Nós em seguida usamos o decorator `route()` que diz ao Flask qual URL deve acionar nossa função.
4. A função é dada um nome que é também usado para gerar URLs para aquela função em particular, e retorna a messagem que queremos mostrar no browser do usuário.

Apenas guarde-a como `hello.py` ou algo similar. Certifique-se de não chamar a sua aplicação de Flask.py porque isso conflitaria com Flask em si.

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

Isto lança servidor embutido muito símples, que é bom o suficiente para testes mas provavelmente não aquele que você quer usar em produção. Para opções de
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
> Isso diz ao sistema operacional para ouvir todos os IPs publicos.

## O que fazer se o servidor não iniciar

No caso de **python -m flask** falhar ou **flask** não existir, há varias razões pelas quais este pode ser o caso. Antes de tudo você precisa verificar a messagem de erro.

### Versão Antiga do Flask

Versões do Flask aboixo da versão 0.11 tem maneiras diferente de iniciar a aplicação. Resumindo, o comando **flask** não existia, e nem fazia **python -m flask**. Neste caso você tem duas opções: tanto atualizar para versões recente do Flask ou ter que olhar na documentação do [Servidor de Desenvolvimento](#servidor-de-desenvolvimento) para achar um método alternativo para executar um servidor.

### Nome Importado Inválido

A variável de ambiente `FLASK_APP` é o nome do módulo importado durante a execução de `flask run`. Neste caso em que o módulo está nomeado incorretamente você receberá um erro de importação, ao iniciar (ou se a depuração estiver ativada quando você navegar até a aplicação). Ele dirá a você o que ele tentou importar e porquê falhou.

A razão mais comum é um typo ou porque você não criou um objeto `app`.

## Modo de Depuração

(Quer apenas imprir os erros e rastrear vestigios? veja [Erros da Aplicação](#erros-da-aplicação))

O roteiro (script) do **flask** é bom para começar um servidor de desenvolvimento local, mas você teria de reiniciar manualmente depois de cada mudança no seu código. Que não muito bom e o Flask pode fazer melhor. Se você ativar o suporte a depuração, o servidor reiniciar-se-á a si mesmo a cada mundaça de código, e ele também proverá para você um depurador muito útil se as coisas correrem mal.

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

Você pode adicionar seções variáveis à uma URL pela marcação de seções com `<nome_da_variável>`. Sua função depois recebe o `<nome_da_variável>` como um argumento de palavra-chave. Opcionalmente, você pode usar um conversor para especificar o tipo do argumento como `<conversor:nome_da_variável>`.

```py
from markupsafe import escape

@app.route('/user/<username>')
def show_user_profile(username):
    # exiba o perfil do usuário para esse usuário
    return 'User %s' % escape(username)

@app.route('/post/<int:post_id>')
def show_post(post_id):
    # exiba a postagem com o dado id, o id é um inteiro
    return 'Post %d' % post_id

@app.route('/path/<path:subpath>')
def show_subpath(subpath):
    # exiba a subpasta depois de /path/
    return 'Subpath %s' % escape(subpath)
```

Tipos de conversores:


 Tipo     | Descrição
 -------- | --------------------------------------------------
 `string` | (padrão) aceita qualquer texto sem barra inclinada
 `int`    | aceita número posítivo inteiro
 `float`  | aceita número posítivo com ponto flutuante
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

A URL canonica para o ponto final de `projects` tem uma barra final. É similar a uma pasta dentro de um sistema de arquivos. Se você acessar a URL sem a barra final, Flask leva você para a URL canonica com a barra final.

A URL canonica para o ponto final `about` não tem uma barra final. É similar ao nome do caminho de um arquivo. Acessar a URL com uma barra final produz um erro 404 de "página não encontrada". Isto ajuda a manter as URLs para esses recursos únicas, que ajuda os mecanismos de busca a evitar a indexação da mesma página duas vezes.


### Construindo uma URL

Para construir uma URL para uma função especifica, use a função **`url_for()`**. Ela aceita o nome da função como seu primeiro argumento e qualquer número de argumentos de palavra-chave, cada correspondendo a uma parte variável da regra da URL. Partes variáveis desconhecidas são anexadas a URL como parâmetros de consulta.

Porquê você quereria construir URLs usando a função de reversão de URL **`url_for()`** ao invés de codifica-los de forma manual dentro dos seus templates?

1. A reversão é algumas vezes mais descritiva do que a códificação manual de URLs.
2. Você pode mudar suas URLs de uma só vez ao invés recordar para manualmente modificar as URLs.
3. A construção de URL trata do escapamento de caracteres especiais e converte para Unicode os dados transparentemente.
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

As aplicações web usam métodos HTTP diferentes quando estão prestes a acessar URLs. Você deveria se familiarizar com os métodos HTTP ao trabalhar com Flask. Por padrão, uma rota responde somente a requesições `GET`. Você pode usar o argumento `methods` do decorador **`route()`** para manipular métodos HTTP diferentes.

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


## Arquivos Estáticos

Aplicações web dinâmicas também precisam de arquivos estáticos. Que usualmente é de onde os arquivos CSS e JavaScript estão vindo. Idealmente seu servidor web é configurado para servir-los para você, porém durante o desenvolvimento o Flask pode fazer isso também. Apenas crie um pasta com o nome `static` dentro do seu pacote ou proximo ao seu módulo e ele estará disponível na aplicação em `/static`.

Para gerar URLs para os arquivos estáticos, use o nome de ponto final especial `'static'`:

```py
url_for('static', filename='style.css')
```

O arquivo tem de estar armazenado no sistema de arquivos como `static/style.css`.

## Renderizando Templates

Gerar o HTML a partir do Python não é divertido, e atualmente um pouco embaraçoso porque você tem de fazer o escapamento do HTML manualmente para manter a segurança da aplicação. Por causa disto o Flask configura o gerenciador de template [Jinja2](http://jinja.pocoo.org/) automaticamente para você.

Você pode usar o método **`render_template()`** para renderizar um template. Tudo que você tem de fazer é prover o nome do template e as variáveis que você quer passar para o gerenciador de template como argumentos de palavra-chave. Aqui está um exemplo símples de como renderizar um template:

```py
from flask import render_template

@app.route('/hello/')
@app.route('/hello/<name>')
def hello(name=None):
    return render_template('hello.html', name=name)
```

O Flask irá procurar pelos templates dentro da pasta `templates`. Então se sua aplicação é um módulo, esta pasta está proxima a esse módulo, se é um pacote está atualmente dentro do seu pacote:

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

Você pode usar todo o poder dos templates Jinja2 nos seus templates. Siga em frente para a [Documentação de Template Jinja2](http://jinja.pocoo.org/docs/templates/) oficial para mais informações.

Aqui está um exemplo de template:

```jinja
<!doctype html>
<title>Hello from Flask</title>
{% if name %}
  <h1>Hello {{ name }}!</h1>
{% else %}
  <h1>Hello, World!</h1>
{% endif %}
```

Dentro dos templates você também tem acesso aos  objetos **request**, **session** e **g** como também a função **`get_flashed_messages()`**.

Os templates são especialmente úteis quando a herança é usada. Se você quiser saber como isso funciona, siga para documentação do padrão de [Herança de Template](#herança-de-template). Basicamente a herança de template torna possível manter certos elementos em cada página (como, cabeçalho, navegação e o rodapé).

O escapamento automático está ativado, então se a variável `name` conter código HTML ele será escapado automaticamente. Se você poder confiar uma variável e você sabe que será um código HTML seguro (por exemplo porque veio de um módulo que converte a marcação do wiki em HTML) você pode marcar ele como sendo seguro pelo uso da classe **Markup** ou pelo uso do filtro `|safe` dentro do template. Siga para a documentação do Jinja2 para obter mais exemplos de uso.

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

Para aplicações web é crucial reagir aos dados que um cliente envia para o servidor. No Flask esta informação é fornecida através do objeto global **request**. Se você tem alguma experiência com o Python, você ficaria maravilhado de como esse objeto pode ser global e como o Flask gerencia-o para o manter seguro (threadsafe). A resposta é locais de contexto:

### Locais de Contexto

> ## Informação de Mantenedor
> Se você quiser entender como isso funciona e como você pode implementar testes com locais de contexto, leia esta seção, ou do contrário você pode apenas salta-la.

Certos objetos no Flask são globais, mas não do tipo comum. Esses objetos são intermédiarios(proxies) de objetos que estão locais para um contexto especifico. Que legal. Porém é um pouco mais fácil de entender.

Imagina o contexto sendo o manipulador de thread. Uma requesição chega e o servidor web decide gerar uma nova thread (ou outra coisa, o objeto subjacente é capaz de lidar com sistemas de concorrência diferentes de threads). Quando o Flask inicia seu manipulador de requisição interno ele percebe que a thread atual é o contexto ativo e vincula a aplicação atual e o ambiente WSGI ao contexto (thread). Ele faz isso de uma maneira inteligente para que uma aplicação possa invocar outra aplicação sem quebrar.

Então o que isso significa para você? Basicamente você pode ignorar completamente que esso é o caso a menos que você esteja fazendo algo como um teste unitário. Você perceberá que o código que depende de um objeto de requisição será interrompido repentinamente porque não há objeto de requisição. A solução é você mesmo criar um objeto de requisição e vincula-lo ao contexto. A solução mais fácil para teste unitário é usar o gerenciador de contexto **`test_request_context()`**. Em combinação com a instrução `with` ele vinculará uma requisição de teste para que você possa interagir com ela. Aqui está um exemplo:

```py
from flask import request

with app.test_request_context('/hello', method='POST'):
    # agora você pode fazer algo com a requisiçã até o
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

O objeto de requesição é documentado na seção da API e não vamos cobri-lo aqui em detalhes (veja [Request](#request)). Aqui está um grande resumo de algumas das operações mais comuns. Antes de tudo você tem que importa-lo a partir do módulo `flask`:

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

Para acessar parametros enviados em uma URL (`?key=value`) você pode usar o atributo **`args`**:

```py
searchword = request.args.get('key', '')
```

Recomendamos o acesso aos parametros da URL com *get* ou pela captura do **`KeyError`** porque os usuários podem mudar a URL e apresenta-los uma página de erro HTTP 400 Requisição Mal-feita nesse caso não seria amigável com o usuário.

Para uma lista de métodos completa e atributos do objeto da requisição, siga para a documentação do **`Request`**.


### Carregamento de Arquivo

Você pode manipular arquivos carregados facilmente com o Flask. Apenas certifique-se de não esquecer the configurar o atributo `enctype="multipart/form-data"` em seu formulário HTML, senão o browser não enviará de todo seus arquivos.

Arquivos carregados são armazenados na memória ou em um local temporário no sistema de arquivos. Você pode acessar esses arquivos buscando por eles no atributo **`files`** do objeto da requisição. Cada arquivo carregado é armazenado dentro daquele dicionário. Ele se comporta como o objeto **`file`** padrão do Python, porém ele tem um método **`save()`** que permite você guardar aquele arquivo no sistema de arquivos do servidor. Aqui está um exemplo símples que mostra como isso funciona:

```py
from flask import request

@app.route('/upload', methods=['GET', 'POST'])
def upload_file():
    if request.method == 'POST':
        f = request.files['the_file']
        f.save('/var/www/uploads/uploaded_file.txt')
    ...
```

Se você quiser saber como o arquivo foi nomeado no cliente antes de ele ser carregado para a sua aplicação, você pode acessar o atributo **`filename`**. Sempre, por favor tenha em mente que esse valor pode ser esquecido então nunca, mas nunca confie naquele valor. Se você quiser usar o nome do arquivo do cliente para guardar o arquivo no servidor, passe-o através da função **`secure_filename()`** que o Werkzeug oference a você:

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

Para exemplos melhores de como fazer carregamento de arquivos, consulte o padrão de [Carregamento de Arquivos](#carregando-arquivos).

### Cookies

Para acessar os cookies você pode usar o atributo **`cookies`**. Para configurar os cookies você pode usar o método **`set_cookie`** do objeto da resposta. O atributo **`cookies`** do objeto da requisição é um dicionário com todos os cookies enviados pelo cliente. Se você quiser usar sessions(seções), não use os cookies diretamente mas ao invés disso use [Seções](#Seções) no Flask que adiciona alguma segurança em cima dos cookies por você.

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

Para redicionar um usuário para outro ponto final (endpoint), use a função **`redirect()`**; Para abortar a requisição anticipadamente com um código de erro, use a função **`abort()`**:

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
2. Se é uma string, um objeto de resposta é criado com aquele dado e os parametros padrão.
3. Se é um dicionario, um objeto de resposta é criado usando `jsonify`.
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

Um formato de resposta comum quando se está escrenvendo uma API é o JSON. É fácil começar a escrever tal API com o Flask. Se você retornar um `dict` de uma view, ele será convertido em uma resposta JSON.

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

Depedendo do design da sua API, você pode querer criar respostas JSON para outros além do `dict`. Neste caso, use a função **`jsonify()`**, que serializará qualquer tipo de dados JSON suportado. Ou busque dentro da comunidade Flask, extensões que suportam aplicações mais complexas.

```py
@app.route("/users")
def users_api():
    users = get_all_users()
    return jsonify([user.to_json() for user in users])
```

## Sessões

Em adição ao objeto da requisição existem também um segundo objeto chamado **`session`** que permite você armazenar informações especificas para um usuário de uma requisição para a próxima. Isso é implementado em cima dos cookies para você e os simbolos dos cookies criptograficamente. O que isso quer dizer é que o usuários poderiam ver os conteudos de seu cookies porém não modifica-los, a menos que eles conheçam a chave secreta usada para acessar.

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
    # remova o nome do usuário se tiver, da seção
    session.pop('username', None)
    return redirect(url_for('index'))
```

O método **`escape()`** mencionado aqui faz o escapamento por você se você não estiver usando o gerador de template (como neste exemplo).

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

Boas interfaces de usuário e aplicações tem tudo haver com feedback(resposta). Se os usuários não receberem respostas suficientes eles irão provavelmente acabar odeiando a aplicação. O Flask oferece uma maneira realmente símples de dar resposta a um usuário com o sistema de alerta. O sistema de alerta basicamente tornam possível armazenar uma mensagem no fim de uma requisição e acessa-la na proxima (e somente na proxima) requisição. Isso é usualmente combinado com um layout de template para expor a mensagem.

Para alertar uma mensagem use o método **`flash()`**, para ter acesso a mensagem você pode usar **`get_flashed_messages()`** que está também disponível nos templates. Consulte a [Mensagem de Alerta](#mensagem-de-alerta) para ter um exemplo completo.

## Logging

* **Relatório de Mudança**:
    * Novo desde a versão 0.3.

Algumas vezes você poderia estar em uma situação onde você lida com dados que deveriam estar corretos, mas não estão. Por exemplo você pode ter algum código no lado do cliente que envia uma requisição HTTP para o servidor mas está obviamente mal-formatado. Isso pode ser causado por um usuário mexendo com os dados, ou o código do lado do cliente falhando. A maioria das vezes está bem responder com `400 Bad Request (Requisição Mal Feita)` nesta situações, mas algumas vezes não ira querer fazer e o código tem de continuar funcionando.

Você pode continuar querer registrar que alguma coisa de errado aconteceu. É aqui onde loggers vem para dar uma mãozinha. Como no Flask 0.3 um logger é preconfigurado para você usar.

Aqui está alguns exemplos de chamadas registradoras:

```py
app.logger.debug('A value for debugging')
app.logger.warning('A warning occurred (%d apples)', 42)
app.logger.error('An error occurred')
```

O **`logger`** atrelado é um **`Logger`** padrão de logging, então siga para documentação oficial de **`logging`** para mais informações.

Leia mais em [Erros da Aplicação](#erros-da-aplicação).

## Interceptando em WSGI Middleware

Para adicionar um WSGI Middleware a sua aplicação Flask, envolva o atributo `wsgi_app` da aplicação. Por exemplo, para aplicar **`ProxyFix`** middleware do Werkzeug para executar por trás do Nginx:

```py
from werkzeug.middleware.proxy_fix import ProxyFix
app.wsgi_app = ProxyFix(app.wsgi_app)
```

Envolvendo o `app.wsgi_app` ao invés de `app` significa que `app` continua apontando para a sua aplicação Flask, e não para o middleware, então você pode continuar usar e configurar `app` diretamente.

## Usando Extensões do Flask

Extensões são pacotes que ajudam você a concluir tarefas comuns. Por example, Flask-SQLAlchemy fornece suporte ao SQLAlchemy que torna símples e fácil o seu uso com Flask.

Para saber mais sobre extensões no Flask, dê uma olhada em [Extensões](#extensões).

## Instalar em um Servidor Web

Pronto para instalar sua nova aplicação Flask? Vá para [Opções de Instalação](#opções-de-instalação).

# Tutorial

## Estrutura do Projeto

Crie uma pasta para o projeto e entre nela:

```sh
$ mkdir flask-tutorial
$ cd flask-tutorial
```

Depois siga as [instruções de instalação](#instalação) para configurar um ambiente virtual em Python e instalar o Flask para o seu projeto.

O tutorial assumirá que você está trabalhando a partir da pasta `flask-tutorial` a partir de agora. O nome de arquivo em cima de cada bloco de código são relativos a essa pasta.

---

Uma aplicação Flask que pode ser tão símples quanto um único arquivo.

`hello.py`
```py
from flask import Flask

app = Flask(__name__)


@app.route('/')
def hello():
    return 'Hello, World!'
```

Todavia, como um projeto vai ficando maior, se torna insustentável, manter todo código em um único arquivo. Projetos em Python usam *pacotes* para organizar código dentro de vários módulos que pode ser importados onde é necessitado e o tutorial fará isso também.

A pasta do projeto conterá:

* `flaskr/`, um pacote Python contendo o código e arquivos da sua aplicação.
* `tests/`, uma pasta contendo módulos de teste.
* `venv/`, um ambiente virtual em Python onde o Flask e outras dependências está instaladas.
* Arquivos de instalação dizem ao Python como instalar o seu projeto.
* Configuração para controle de versão, tal como [git](https://git-scm.com/). Você deveria tornar um habito o uso algum tipo de controle de versão para todos os seus projetos, indepedente do seu tamanho.
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

Uma aplicação Flask é uma instância da classe **`Flask`**. Tudo que diz respeito a aplicação, tal como a configuração e URLs, serão registradas com essa classe.

A mais simples de criar uma aplicação Flask é criar uma instância global diretamente no topo do seu código, tipo como é feito no exemplo "Hello, World!" da seção anterior. Enquanto isso é simples e útil em alguns casos, ele pode causar alguns problemas dificeis a medida que o projeto vai crescendo.

Ao invés de criar uma instância do **`Flask`** globalmente, você irá cria-lo dentro de uma função. Esta função é conhecida como a fabrica da aplicação (application factory). Qualquer configuração, registro, ou outra configuração que a aplicação precise irá acontecer dentro da função, depois a aplicação será retornada.

### A Fabrica da Aplicação

É hora de começar a codificar! Crie a pasta `flaskr` e adiciona o arquivo `__init__.py`. O `__init__.py` serve a duas responsabilidades: ela irá conter a fabrica da aplicação, e ele dirá ao Python que a pasta `flaskr` deve ser tratada como um pacote.

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

`create_app` é a função de fabrico da aplicação. Você irá adicionar mas coisas depois no tutorial, mas por agora ele já faz muita coisa.

1. `app = Flask(__name__, instance_relative_config=True)` cria a instância **`Flask`**.
    * `__name__` é o nome do módulo atual do Python. O app precisa saber onde está localizado para configurar alguns caminhos, e `__name__` é uma maneira conveniente de dizer isso a ele.
    * `instance_relative_config=True` diz a app que as arquivos de configuração são relativo a [pasta da instância](#pasta-da-instância). A pasta da instância se encontra fora do pacote `flaskr` e pode segurar dados locais que não deveriam ser consolidados pelo controlo de versão, tal como configurações secretas e o arquivo do banco de dados.

2. **`app.config.from_mapping()`** configura alguma configuração padrão que a app usará:
    * **`SECRET_KEY`** é usado pelo Flask e suas extensões para manter os dados em segurança. Está configurado para `dev` para fornecer um valor conveniente durante o desenvolvimento, mas ele deve ser sobrescrito com um valor aleatório quando estiver instalando.
    * `DATABASE` é o caminho onde o arquivo do banco de dados SQLite será salvo. Está embaixo de `app.instance_path`, que é o caminho que o Flask escolheu para a pasta da instância. Você aprenderá mais sobre o banco de dados na prôxima seção.
3. **`app.config.from_pyfile()`** sobrescreve as configurações padrão com valores dadas pelo arquivo `config.py` na pasta da instância se ela existir. Por exemplo, quando instalando, isso pode ser usado para configurar uma `SECRET_KEY` real.
    * `test_config` pode também ser passado para a fabrica, e será usado ao invés da instância de configuração. Isto é, os testes que você escreverá depois no tutorial podem ser configurado independentemente de quaisquer valores de desenvolvimento que você tem configurado.
4. **`os.makedirs()`** garante que **`app.instance_path`** exista. O Flask não cria a pasta da instância automaticamente, mas ela precisa ser criada porque seu projeto irá criar o arquivo do banco de dados SQLite lá.
5. **`@app.route()`** cria uma simples rotas então você pode ver a aplicação trabalhando antes de ter contato com o resto do tutorial. Ele cria uma conexão entre a URL `/hello` e uma função que retorna uma resposta, neste caso a string(texto) `Hello, World!`.


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

Visite http://127.0.0.1:5000/hello em um browser e você deve visualizar a mensagem "Hello, World!". Parabéns, você está agora executando a sua aplicação web em Flask.

Siga para [Definir e Acessar o Banco de Dados](#definir-e-acessar-o-banco-de-dados).

## Definir e Acessar o Banco de Dados

A aplicação usará o banco de dados [SQLite](https://sqlite.org/about.html) para armazenar os dados dos usuários e suas publicações. O Python vem com suporte ao SQLite no módulo **`sqlite3`**.

O SQLite é conveniente porque não requer configuração de um servidor de banco de dados separados e está imbutido no Python. De qualquer forma, se as requisições concorrentes tentarem escrever no banco de dados ao mesmo tempo, elas diminuirão a velocidade, pois cada escrita acontece sequencialmente. Pequenas aplicações não notar isso. Depois de se tornar grande você, você pode querer mudar para um banco de dados diferente.

O tutorial não irá entrar em detalhes sobre o SQL. Se você não estiver familiarizado com ela, a documentação do SQLite descreve a [linguagem](https://sqlite.org/lang.html).

### Conectar ao Banco de Dados

A primeira coisa a fazer quando estiver trabalhando com o banco de dados SQLite (e com a maioria das bibliotecas de banco de dados do Python) é criar uma conexão com ela. Quaisqueres consultas e operações são performadas usando a conexão, que é fechada depois do trabalho estiver terminado.

Em aplicações web essa conexão geralmente está ligada à requisição. Ela é criada em algum momento ao lidar com uma requisição e fechada antes que a resposta seja envianda.

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

**`g`** é um objeto especial que é único para cada requisição. Ele é usado para armazenar os dados que podem vir ser acessados por várias funções durante a requisição. A conexão é armazenada e reusada para não ter que criar uma conexão nova se **`get_db`** for chamado uma segunda vez na mesma requisição.

**`current_app`** é outro objeto especial que aponta para manipulador de requisição da aplicação Flask. Desde que você usou uma fábrica de aplicação, não há um objeto de aplicação ao escrever o resto do seu código. `get_db` será chamado quando a aplicação tiver sido criada e estiver manipulando uma requisição, então **`current_app`** pode ser usado.

**`sqlite3.Row`** diz a conexão para retornar linhas que se comportem como dicionários. Isso permite acessar as colunas pelo nome.

`close_db` verifica se uma conexão foi criada, verificando se `g.db` foi configurado. Se a conexão existir, ela é fechada. Além disso você informará a sua aplicação que sobre a função `close_db` na fábrica da aplicação para que seja ela executada a cada requisição.

### Criar as Tabelas

No SQLite, os dados são armazenados em *tabelas* e *colunas*. Estes precisam ser criados antes de você poder guardar e recuperar dados. Flaskr armazenará os usuários na tabela `user`, e as publicações na tabela `post`. Crie um arquivo com os comandos SQL necessários para criar tabelas vazias:

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

Adicione uma função Python que executará esses comandos SQL ao arquivo `db.py`:

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

**`open_resource()`** abre um arquivo relativo ao pacote `flaskr`, o que é útil, pois você não necessariamente saberá onde esse local é ao implementar a sua aplicação depois. `get_db` retorna uma conexão com o banco de dados, que é usada para executar os comandos lidos a partir do arquivo.

**`click.command()`** define um comando de linha de comando com o nome `init-db` que chama a função `init_db` e mostra uma messagem de sucesso para o usuário. Você pode ler [Interface de Linha de Comando](#interface-de-linha-de-comando) para aprender mais sobre a escrita de comandos.


### Registar com a Aplicação

As funções `close_db` e `init_db_command` precisam ser registadas com a instância da aplicação; senão, eles não serão usados pela aplicação. No entanto, já que você está usando uma função de fabricação, essa instância não está disponível quando estiver escrevendo as funções. Em vez disso, escreva uma função que receba uma aplicação e faça o registro.

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

Agora que `init-db` já foi registrado com a aplicação, ele pode ser chamado usando o comando `flask`, similatar ao comando `run` das seções anteriores.

> ## Nota:
> Se continua executando o servidor a partir das seções anteriores, você pode parar o servidor, ou executar esse comando em um novo terminal. Se você usar um novo terminal, lembresse de mudar para a pasta do seu projeto e ativar o ambiente como descrito em [Ativar o Ambiente](#ativar-o-ambiente). Você também precisará configurar o `FLASK_APP`e o `FLASK_ENV` como mostrado nas seções anteriores.

Execute o comando `init-db`

```sh
$ flask init-db
Initialized the database.
```

Agora passará a existir um arquivo com o nome `flaskr.sqlite` dentro da pasta `instance` no seu projeto.

Continue para [Blueprints(modelos) e views(visão)](#blueprints-e-views).

## Blueprints e Views

Uma função de view é o código que você escreve para responder a requisições para sua aplicação. O Flask usa um padrão para corresponder a URL da requisição de entrada para uma view que deve lidar com isso. A view retorna dados que o Flask transforma em uma resposta de saída. O Flask também pode ir em outra direção e gerar uma URL para uma view baseada em seu nome e argumentos.

### Criar um Blueprint

Um **`Blueprint`** é uma maneira de organizar um grupo de views relacionadas e outros códigos. Em vez de registrar views e outros códigos diretamente com uma aplicação, eles são registrados com um blueprint. Em seguida, o blueprint é registrado com a aplicação quando estiver disponível na função de fabricação.


O Flaskr terá dois blueprints, uma para funções de autenticação e uma para as funções de publições do blogue. O código de cada blueprint irá em módulo separado. Já que o bloque precisa conhecer a autenticidade, você escreverá a autenticação primeiro.

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

Isso cria um **`Blueprint`** com nome `'auth'`. Tal como o objeto da aplicação, o blueprint precisa saber onde está definida, então `__name__` é passado como segundo arguemento. O `url_prefix` estará preparado para todas URL associadas com a blueprint.

Importa e regista o blueprint da fábrica usando **`app.register_blueprint()`**. Coloque o código novo no final da função de fabricação antes de retornar a aplicação(app).

`flaskr/__init__.py`

```py
def create_app():
    app = ...
    # código existente omitido

    from . import auth
    app.register_blueprint(auth.bp)

    return app
```

O blueprint de autenticação terá views para registar novos usuários e para entrar e para sair.

### A Primeira View: Register(registrar)

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

Aqui está o que a função da view `register` está fazendo:

1. **`@bp.route`** associa a URL `/register` a função da view `register`. Quando o Flask recebe uma requisição para `/auth/register`, ele chamará a view `register` e usa o valor retornado como resposta.

2. Se o usuário submeteu o formulário, **`request.method`** será `POST`. Neste caso, começa validando a entrada.

3. **`request.form`** é um tipo de mapeamento de dicionário especial de chaves e valores do formulário submetido. Os usuários entrarão com seus `username` e `password`.

4. Faz a validação para ter a certeza de que o `username` e `password` não estão vazios.

5. Faz a validação para saber se o `username` já não está registrado, consultando o banco de dados e verificando se um resultado é retornado. **`db.execute`** pega uma consulta SQL com espaços reservados por `?` para qualquer entradada do usuário, e uma tupla de valores para substituir os espaços reservados. A biblioteca do banco de dados cuidará do escapamento dos valores, então você não está vulnerável a ataques de injeção de código SQL (SQL injection attack).

    [**`fetchone()`**](https://docs.python.org/3/library/sqlite3.html#sqlite3.Cursor.fetchone) retorna um linha da consulta. Se a consulta não retornou nenhum resultado, ela retorna `None`.
    Depois, [**`fetchall()`**](https://docs.python.org/3/library/sqlite3.html#sqlite3.Cursor.fetchall) é usado, que retorna uma lista de todos os resultados.

6. Se a validação for bem-sucedida, insere os dados do novo usuário dentro do banco de dados. Para segurança, senhas nunca devem ser armazenadas diretamente em um banco de dados. Ao invés disso, [**`generate_password_hash()`**](https://werkzeug.palletsprojects.com/en/1.0.x/utils/#werkzeug.security.generate_password_hash) é usado para seguramente criptografar a senha, e esse valor criptografado é armazenado. Como, essa consulta modifica dados, o [**`db.commit()`**](https://docs.python.org/3/library/sqlite3.html#sqlite3.Connection.commit) precisa ser chamado logo em seguida para salvar as alterações.

7. Depois de armazenar os usuários, eles são redirecionados para a página de entrada (login). [**`url_for()`**](#flask.url_for) gera a URL para a view de entrada (login) baseado no seu nome.  Isso é preferível a escrever a URL diretamente, pois permite você alterar a URL mais tarde, sem alterar todo código que se vincula a ele. [**`redirect()`**](#flask.redirect) gera uma resposta de redirecionamento para a URL gerada.

8. Se a validação falhar, o erro é exibido aos usuários. [**`flash()`**](#flask.flash) armazena mensagens que pode ser recuperadas ao renderizar o template.

9. Quando os usuários inicialmente navegarem para `auth/register`, ou houve um erro de validação, um página HTML com o formulário de registro deve ser exibido. [**`render_template()`**] renderizará um template contendo o HTML, que você escreverá no próximo passo do tutorial.


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

2. [**`check_password_hash()`**](#https://werkzeug.palletsprojects.com/en/1.0.x/utils/#werkzeug.security.check_password_hash) mistura a senha enviada da mesma maneira que o senha misturada guardada e seguramente compara elas. Se eles se corresponderem, a senha é valida.

3. [**`session`**](#flask.session) é um [dicionário](https://docs.python.org/3/library/stdtypes.html#dict) que armazena dado enviado junto da requisição. Quando a validação for bem-sucedida, o `id` do usuário é armazenado em uma nova seção. O dado é armazenado em um *cookie* que é enviado para o browser, e o browser depois envia ele de volta já na proxima requisição. O Flask *assina* com segurança o dado para que não possa ser adulterado.


Agora que o `id` do usuário está guardado em [**`session`**](#flask.session), estará disponível nas proximas requisições. Ao iniciar cada requisição, se um usuário estiver logado, suas informações devem ser carregadas e disponibilizadas para outras views.

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

[**`bp.before_app_request()`**](#flask.Blueprint.before_app_request) registra uma função que executa antes da função da view, não importa qual URL foi requesitada. `load_logged_in_user` verifica se um id de usuário está armazenado na [**`session`**](#flask.session) e pega esse dado do usuário do banco de dados, guarda-o em [**`g.user`**](#flask.g), que dura para o comprimento da requisição. Se não houver um id de usuário, ou se o id não existe, `g.user` será `None`.


#### Logout

Para terminar a seção, você precisa remover o id do usuário da [**`session`**](#flask.session). Assim `load_logged_in_user` não carregará um usuário na proxima requisição.

`flaskr/auth.py`

```py
@bp.route('/logout')
def logout():
    session.clear()
    return redirect(url_for('index'))
```

#### Exigir Autenticação em Outras Views

Criar, editar, e eliminar publicações do blogue exigirá que o usuário esteja logado. Um *decorador* pode ser usado para verificar isso para cada view ao qual for aplicado.

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

Esse decorador retorna uma nova função view que envolve a view original ao qual é aplicada. A nova função verifica se um usuário está carregado e ou ao contrário redireciona para a página de entrada (login). Se um usuário estiver carregado a view original é chamada e continua normalmente. Você usará esse decorador ao escrever as views do blogue.

### Endpoints e URLs

A função [**`url_for()`**](#flask.url_for) gera a URL para uma view com base em um nome e argumentos. O nome associado a uma view é também chamado de *endpoint*, e por padrão é o mesmo nome da função view.

Por exemplo, a view `hello()` que foi adicionada a fábrica de aplicação mais cedo durante o tutorial tem o nome `'hello'` e pode ser ligado a `url_for('hello')`. Se ela receber um argumento, o qual você verá mais tarde, seria ligado ao uso de `url_for('hello', who='World')`.

Ao usar um blueprint, o nome do blueprint é predefinido para o nome da função, então o endpoint para a função `login` que você escreveu acima é `auth.login` porque você adicionou ele ao blueprint `auth`.

Siga para [Templates](#templates).

## Templates

Você escreveu a view de autenticação para sua aplicação, mas se está executando o servidor e tentar ir para quaisqueres URLs, verá um erro `TemplateNotFound`. Isso porque as views estão chamando [**`render_template()`**](#flask.render_template), porém você ainda não escreveu nenhum template. Os arquivos de templates serão guardados na pasta `templates` dentro do pacote `flaskr`.

Templates são arquivos que contêm dados estáticos como també espaços reservados para dados dinâmicos. Um template é renderizado com dado especifico para produzir um documento final. O Flask usa a biblioteca de templates [Jinja](#http://jinja.pocoo.org/docs/templates/) para renderizar templates.

Em sua aplicação, você usará templates para renderizar o [HTML](https://developer.mozilla.org/docs/Web/HTML) que será exibido no browser do usuário. No Flask, o Jinja é configurado para auto-escapar qualquer dado que for renderizado nos templates HTML. Isso significa que é seguro renderizar a entrada do usuário; quaisquer caracteres que eles entrarem que possa mexer com o HTML, tal como `<` e `>` serão escapados com valores *seguros* que se parecem com os mesmos no browser mas sem causar efeitos colaterais.

O Jinja parece e se comporta principalmente como Python. Delimitadores especiais são usados para diferenciar a sintáxe do Jinja do conteúdo estático no template. Qualquer coisa entre `{{` e `}}` é uma expessão que será emitida para o documento final. `{%` e `%}` denota uma declaração de controle de fluxo como `if` e `for`. Ao contrário do Python, blocos são denotados por tags iniciais e finais, em vez de indentação, já que o texto estático dentro de um bloco pode alterar a indentação.


### O Layout Básico

Cada página na aplicação terá o mesmo layout básica em torno de um corpo diferente. Ao invés de escrever toda a estrutura HTML em cada template, cada template extenderá um template básico e sobrescrever seções específicas.

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

[**`g`**](#flask.g) está automaticamente disponível nos  templates. Baseado em que se `g.user` estiver definido (a partir de `load_logged_in_user`), tanto o nome do usuário e um link de saída (logout) são exibidos, ou links para registar e um para entrar (login) são exibidos. [**`url_for()`**](#flask.url_for) está também automaticamente disponível, e é usado para gerar URLs para as views ao invés de escreve-los manualmente.

Depois do título da página, e antes do conteúdo, o template faz um loop em cada mensagem retornada por [**`get_flashed_messages()`**](#flask.get_flashed_messages). Você usou [**`flash()`**](#flask.flash) nas views para mostrar mensagens de erros, e este é o código que irá exibi-los.

Existem três blocos definidos aqui que serão sobrescritos em outros templates:

1. `{% block title %}` mudará o título exibido na aba do browser e título da janela.

2. `{% block header %}` é similar ao `title` porém irá mudar o título exibido na página.

3. `{% block content %}` é onde o conteúdo de cada página irá, tal como formulário de entrada (login) ou uma publicação do blogue.

O template básico está diretamente dentro da pasta `templates`. Para manter os outros organizados, os templates para um blueprint serão postos dentro de uma pasta com o mesmo nome que o blueprint.

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

`{% extends 'base.html' %}` diz ao Jinja que este template deve substituir os blocos do template básico. Todo o conteúdo renderizado deve aparecer dentro das tags `{% block %}` que sobreescreve os blocos do template básico.

Um padrão útil usado aqui é pôr `{% block title %}` dentro de `{% block header %}`. Isto definirá o bloco de título e em seguida produzir o valor dele dentro do bloco de cabeçalho, assim ambos a janela e a página partilham o mesmo título sem ter escreve duas vezes.

A tag `input` está usando aqui o atributo `required`. Isso diz ao browser para não submeter o formulário até que aqueles campos estejam preenchidos. Se os usuários estiverem usando um browser antigo que não suporta aquele atributo, ou se eles estiverem usando algo além do browser para fazer requisições, você ainda deseja validar os daods na view do Flask. É importante sempre validar completamente os daods no servidor, mesmo se o cliente fazer alguma validação também.

### Log In

Este é identico ao template register excepto para o título e botão de submissão.

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

Agora que os templates de autenticação estão escritos, você pode registar um usuário. Certifique-se de que o servidor ainda está executando (`flask run` se não estiver), depois siga para http://127.0.0.1:5000/auth/register.

Tente clicar no botão "Register" sem preencher o formulário e veja o que o browser mostra uma mensagem de erro. Tente remover os atributos `required` do template `register.html` e clique de novo em "Register". Ao invés de o browser mostrar um erro, a página recarregará e o erro do [**`flash()`**](#flask.flash) na view será exibido.

Preencha o nome de usuário e senha e você será redirecionado para uma página de entrada (login). Tente inserir um nome de usuário incorreto, ou o nome de usuário correto e senha incorreta. Se você entrar, você receberá um erro porque ainda não existe uma view `index` para qual redirecionar.

Continue para [Arquivos Estáticos](#arquivos-estáticos).

## Arquivos Estáticos

As views de autenticação e templates funcionam, porém eles parecem muito plano por agora. Algum [CSS](https://developer.mozilla.org/docs/Web/CSS) pode ser adicionado para adicionar estilo ao lagout HTML que você construiu. O estilo não irá mudar, assim é um arquivo *estático* em vez de um template.

O Flask automaticamente adiciona uma view `static` que pega um caminho relativo para pasta `flaskr/static` e servi-o. O template `base.html` já tem um link para o arquivo `style.css`:

```jinja
{{ url_for('static', filename='style.css') }}
```
Fora o CSS, outros tipos de arquivos estáticos pode ser arquivos com funções JavaScript, ou uma imagem com logotipo. Eles são todos colocados na pasta `flaskr/static` e referenciados com `url_for('static', filaname='...')`.

Este tutorial não é focado em como escrever CSS, então pode simplesmente copiar o seguinte para o arquivo `flaskr/static/style.css`:

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

Você pode ler mais sobre CSS na [documentação da Mozilla](https://developer.mozilla.org/docs/Web/CSS). Se você mudar um arquivo estático, recarrege a página do browser. Se a mudança não for exibida, tente limpar o cache do seu browser.

Continue em [Blueprint do Blogue](#blueprint-do-blogue).

## Blueprint do Blogue

Você usará a mesma tecnica que você aprendeu quando escrever a blueprint de autenticação para escrever o blueprint do blogue. O blogue deve listar todos as publicações, permitir usuários logados criar publicações, e permiter o autor da publicação editar ou elimina-lo.

Ao implementar cada view, mantenha o servidor de desenvolvidos em execução. Ao você guardar suas mudanças, tente ir para o URL em seu browser e testa-los.

### O Blueprint

Defina o blueprint e regista-o na fabrica da aplicação.

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

Importa e regista o blueprint da fabrica usando [**`app.register_blueprint()`**](#flask.register_blueprint). Coloque o novo código no final da função de fabricação antes de retornar o app.

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

Ao contrário do blueprint de autenticação, o blueprint do blogue não tem um `url_prefix`. Então a view `index` estará em `/`, a view `create` em `/create`, e por aí vai. O blogue é funcionalidade principal do Flaskr, então faz sentido que o index do blogue será o index principal.

No entanto, o endpoint para a view `index` definida aboixo será `blog.index`. Alguns das views de autenticação referem-se a um endpoint do index plano. [**`app.add_url_rule()`**](#flask.add_url_rule) associa o endpoint com o nome `'index'` com a url `/` de modo que `url_for('index')` ou `url_for('blog.index')` ambos funcionarão, gerando a mesma URL `/` de qualquer maneira.

Em outra aplicação você pode dar ao blueprint do blogue uma `url_prefix` e definir uma view `index` separada na fábrica da aplicação, semelhante a view `hello`. Então os endpoints `index` e `blog.index` e as URLs seriam diferentes.

### Index

O index exibirá todas as publicações, com as mais recentes em primeiro. Um `JOIN` é usado para que as informações do autor na tabela do usuário estejam disponível no resultado.

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

Quando um usuário estiver logado, o bloco do `header` adiciona um link para a view `create`. Quando os usuários forem os autores de uma publicação, eles verão um link "Edit" para a view `update` para aquela pulicação. `loop.last` é uma variável disponível dentro do [Jinja para loops](http://jinja.pocoo.org/docs/templates/#for). É usado para exibir uma linha depois de cada publicação excepto a última, para separa-los visualmente.

### Create

A view `create` funciona da mesma forma que a view de autenticação `register`. Ou formulário é exibido, ou os dados publicados são validados e a publicação é adicionada ao banco de dados ou um erro é mostrado.

O decorador `login_required` que você escreveu anteriormente é usado nas views do blogue. Um usuário deve estar logado para visitar essas views, caso contrário, eles serão redirecionados para a página de entrada(login).

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
### Update

Ambos a view `update` e `delete` precisarão buscar um `post` por `id` e verificar se o autor corresponde ao usuário logado. Evite código duplicado, você pode escrever uma função para receber o `post` e chame-o em cada view.

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

[**`abort()`**](#flask.abort) irá levantar uma exceção especial que retorna um código de estado HTTP. Ele leva uma mensagem opcianal para mostrar com um erro, caso contrário uma mensagem padrão é usada. `404` significa "Não Encontrado", e `403` significa "Proibido". (`401` significa "Não Autorizado", porém você redireciona para a página de entrada (login) ao invés de retornar aquele estado.)

O argumento `check_author` é definido para que a função possa ser usado para obter uma publicação sem verificar o autor. Isso seria útil se você escreveu uma view para mostrar uma publicação individual em uma página, onde o usuário não importa porque eles não modificam a publicação.

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

Ao contrário das views que você escreveu até agora, a função `update` leva um argumento, `id`. Que corresponde ao `<int:id>` na rota. Uma URL real será parecerido com `/1/update`. O Flask capturará o `1`, garante que seja um [**`int`**](https://docs.python.org/3/library/functions.html#int), e passa-lo como o argumento `id`. Se você não especificar `int:` e ao invés disso fizer `<id>`, ele será uma string. Para gerar um URL para a página de atualização, [**`url_for()`**](#flask.url_for) precisa ser passado o `id` assim ele sabe o que preencher: `url_for('blog.update', id=post['id'])`. Isto está também no arquivo `index.html` acima.

As views `create` e `update` parecem muito similares. A princípal diferença é que a view `update` usa um objeto `post` e uma consulta de tipo `UPDATE` ao invés de um `INSERT`. Com alguma refatoração inteligente, você poderia usar uma view e template para ambas ações, porém para o tutorial é mais claro mante-los separados.

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

Este template tem dois formulários. O primeiro publica os dados editados para a página atual (`/<id>/update`). O outro formulário somente contém um botão e especifica um atributo `action` que publica para a view delete. O botão usa algum JavaScript para mostrar uma dialogo de confirmação antes do envio.

O padrão `{{ request.form['title'] or post['title'] }}` é usado para escolher quais dados aparecem no formulário. Quando o formulário não tiver sido enviado, os dados `post` originais aparecem, mas se um formulário com dados invalidos foram publicados, você deseja exibir-lo então o usuário pode corrigir o erro, assim `request.form` é usado em vez disso. [**`request`**](#flask.request)  é outra variável que está automaticamente disponível nos templates.

### Delete

A view delete não tem seu próprio template, o botão delete é parte do `update.html` e publica para a URL `/<id>/delete`. Como não há template, ele irá somente lidar com o método `POST` e depois redirecionar para view `index`.

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

Parabens, você acabou de escrever usa aplicação! Tire algum templo para testar tudo no browser. De qualquer maneira, ainda há mais por se fazer antes do projeto estar completo.

Continue para [Tornar o Projeto Instalável](#tornar-o-projeto-instalável).

## Tornar o Projeto Instalável

Tornar seu projeto instalável significa que você pode construir um arquivo de distribuição e instala-lo em outros ambientes, do mesmo jeito que você instalou o Flask no ambiente do seu projeto. Isto faz com que implementação do seu projeto seja o mesmo que instalar qualquer outra biblioteca, assim você está usando todas as ferramentas padrão do Python para gerenciar tudo.

A instalação também vem com outros beneficios que podem não estar obvios no tutorial ou para usuário de Python inexperientes, incluindo:

* Atualmente, o Python e o Flask entendem como usar o pacote `flaskr` somente porque você está executando a partir da pasta da sua aplicação. A instalação significa que você pode importa-lo não importa onde é executado.

* Você pode gerenciar as dependências da sua aplicação tal como fazes com outros pacotes, então `pip install yourproject.whl` instala-os.

* Ferramentas de testes podem insolar seu ambiente de testes do seu ambiente de desenvolvimento.


> ## Nota:
> Isto está sendo introduzido no final do tutorial, mas em seus futuros projetos você deve sempre começar com ele.

### Descreva o Projeto

O arquivo `setup.py` descreve seu projeto e os arquivos que pertencem a ele.


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

`packages` informa ao Python quais pastas de pacotes (e os arquivos Python que eles contêm) incluir. `find_packages()` encontra essas pastas automaticamente assim você não tem que informa-los. Para incluir outros arquivos, tais como a pastas de arquivos estáticos e templates, `include_package_data` é configurado. O Python precisa de outro arquivo chamado `MANIFEST.in` para informar quais são esses outros dados.

`MANIFEST.in`

```
include flaskr/schema.sql
graft flaskr/static
graft flaskr/templates
global-exclude *.pyc
```

Isso diz ao Python para copiar tudo dentro das pastas `static` e `templates`, e o arquivo `schema.sql`, contudo exclui todos arquivos de bytecode.

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

O código de teste está localizado na pasta `tests`. Esta pasta está *próxima* ao `flaskr`, não dentro dele. O arquivo `tests/conftest.py` contém funções de configuração chamadas *fixtures* que cada teste usará. Em Python, testes são módulos que começam com `test_`, e cada função de teste nesses módulos também começam com `test_`.

Cada teste criará um novo arquivo de banco de dados temporário e popular com alguns dados que serão usados nos testes. Escreva um arquivo SQL para inserir aqueles dados.

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

[**tempfile.mkstemp()**](https://docs.python.org/3/library/tempfile.html#tempfile.mkstemp) cria e abri um arquivo temporário, retornando o objeto do arquivo e o caminho para ele. O caminho `DATABASE` é substituido, então ele aponta para este caminho temporário ao invés da pasta da instância. Depois de configurar o caminho, as tabelas do banco de dados são criados e os dados de teste são inseridos. Depois o teste estiver terminado, o arquivo temporário é fechado e removido.

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

Este teste usa o fixture `monkeypatch` do Pytest para substituir a função `init_db` com um daqueles registros que estão sendo chamados. O fixture `runner` que você escreveu acima é usado para chamar o comando `init-db` pelo nome.


### Autenticação

Para a maioria das views, um usuário precisa estar logado. A maneira mais fácil de fazer isso nos testes é fazer uma requisição `POST` para a view `login` com o cliente. Em vez de escrever isso todas as vezes, você pode escrever um classe com metódos para fazer isso, e usar uma fixture para passa-lo para o cliente em cada teste.

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

Com a fixture `auth`, você pode chamar `auth.login()` em um teste para logar como usuário `test`, que foi inserido como parte dos dados do teste na fixture `app`.

A view `register` deve renderizar com sucesso em `GET`. No `POST` com dados de formulário validos, ele deve redirecionar para a URL de login e o dados do usuário devem estar dentro do banco de dados. Dados invalidos devem exibir mensagens de erro.

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

[**client.get()**](https://werkzeug.palletsprojects.com/en/1.0.x/test/#werkzeug.test.Client.get) faz uma requisição `GET` e retorna o objeto [**Response**](#flask.response) retornado pelo Flask. Semelhantemente, [**client.post()**](https://werkzeug.palletsprojects.com/en/1.0.x/test/#werkzeug.test.Client.post) faz uma requisição `POST`, convertendo o dicionário `data` em dado de formulário.

Para testar que a página renderiza com sucesso, uma simples requisição é feita e confirmada por um [**status_code**](#flask.response.status_code) `200 OK`. Se a renderização falhar, o Flask retornaria um código `500 Internal Server Error`.

[**headers**](#flask.response.headers) terá um cabeçalho `Location` com a URL do login quando a view register redireciona para a view de login.

[**data**](#flask.response.data) contém o corpo da resposta como bytes. Se você espera um certo valor para renderizar na página, verifique se está dentro do `data`. Bytes devem ser comparados a bytes. Se você quiser comparar texto Unicode, use [**get_data(as_text=True)**](https://werkzeug.palletsprojects.com/en/1.0.x/wrappers/#werkzeug.wrappers.BaseResponse.get_data).

`pytest.mark.parametrize` diz ao Pytest para executar a mesma função de teste com diferentes argumentos. Você pode usa-lo aqui para testar diferentes entradas invalidas e mensagens de erro sem escrever a mesma código três vezes.

Os testes para a view `login` são muito similar a aqueles para o `register`. Ao invês de testar os dados dentro do banco de dados, [**session**](#flask.session) deve ter o `user_id` configurado depois de logado.

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

Testar o `logout` é o oposto de `login`. [**session**](#flask.session) não deve conter o `user_id` depois de deslogar.

`tests/test_auth.py`

```py
def test_logout(client, auth):
    auth.login()

    with client:
        auth.logout()
        assert 'user_id' not in session
```

### Blog

Todas as views do blog usam a fixture `auth` que você escreveu mais cedo. Chama `auth.login()` e as requisições subsequentes do cliente irão ser logadas como usuário `test`.

A view `index` deve exibir informação sobre a publicação que foi adicionada com o dados do teste. Quando logado como o autor, deve haver um link para editar a publicação.

Você pode também testar mais algum comportamento de autenticação enquanto testa a view `index`. Quando não estiver logado, cada página exibe links para logar ou registar. Quando logado, há um link para deslogar.

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

Um usuário deve estar logado para acessar as views `create`, `update`, e `delete`. O usuário logado deve ser o autor da publicação para acessar o `update` e o `delete`, do contrário um estado `403 Forbidden` é retornado. Se um `post` com o `id` dado não existir, `update` e `delete` devem retornar `404 Not Found`.

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
    with app.app_context():
        db = get_db()
        db.execute('UPDATE post SET author_id = 2 WHERE id = 1')
        db.commit()

    auth.login()
    # current user can't modify other user's post
    assert client.post('/1/update').status_code == 403
    assert client.post('/1/delete').status_code == 403
    # current user doesn't see edit link
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

Alguma configuração extra, que não é necessária mas que torna a execução dos testes com o coverage menos verbosa, pode ser adicionado ao arquivo `setup.cfg` do projeto.

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

Um relatório em HTML permite você ver quais linhas foram cobertas em cada arquivo:

```sh
$ coverage html
```

Isto gera arquivos dentro da pasta `htmlcov`. Abra o `htmlcov/index.html` em seu browser para ver o relatório.

Continue para  [Instalar em Produção](#instalar-em-produção).

## Instalar em Produção

Esta parte do tutorial assume que você tenha um servidor onde você quer fazer o deploy da sua aplicação. Ele concede uma visão geral de como criar um arquivo de distribuição e instala-lo, mas não entrará em assuntos especificos sobre servidor ou software usar. Você pode configurar um novo ambiente em seu computador de desenvolvimento para tentar aplicar as instruções abaixo, mas provavelmente não deveria usa-lo para hospedar uma aplicação pública real. Veja [Opções de Instalação](#opções-de-instalação) para uma listagem de varias diferentes maneiras de hospedar sua aplicação.


### Construir e Instalar

Quando você quiser instalar sua aplicação em outro lugar, você constróis um arquivo de distribuição. O atual padrão para distruibuição Python é o formato *wheel* (roda), com a extensão `.whl`. Certifique-se de que a biblioteca wheel seja instalada primeiro:

```sh
$ pip install wheel
```

Executar `setup.py` com Python dá para você uma ferramenta de linha de comando para emitir um comandos relacionados a construção. O comando `bdist_wheel` irá construir um arquivo de distribuição wheel.

```sh
$ python setup.py bdist_wheel
```

Você pode encontrar o arquivo em `dist/flaskr-1.0.0-py3-none-any.whl`. O nome do arquivo é o nome do projeto, a versão, e algumas tags (etiquetas) que o arquivo pode instalar.

Copie este aquivo para outra maquina, [configure um novo ambiente virtual](#criar-um-ambiente), depois instale o arquivo com `pip`.

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

Crie o arquivo `config.py` na pasta da instância, que a fábrica irá ler se ele existir. Copie o valor gerado para dentro dele.

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

Este tutorial encaminhará você através da criação de uma aplicação básica do tipo blogue chamada Flaskr. Os usuários será capazes de se registar, logar, criar publicações, e editar ou eliminar suas próprias publicações. Você será capaz de empacotar e instalar a aplicação em outros computadores.

![página inicial do flaskr](./static_files/flaskr_index.png)

É assumido que você já está familiarizado com o Python. O [tutotial oficial](https://docs.python.org/3/tutorial/) na documentação do Python é excelente maneira de aprender e revisar a linguagem.

Enquanto é desenhado para fornecer um bom ponto de partida, o tutorial não cobri todas funcionalidades do Flask. Consulte o [Começo Rápido](#começo-rápido) para uma revisão daquilo que o Flask pode fazer, depois mergulhe dentro da documentação para encontrar mais. O tutorial somente usa o que é fornecido pelo Flask e Python. Em outro projeto, você pode decidir usar [Extensões](#extensões) ou outras bibliotecas para realizar algumas tarefas de maneira mais símples.

![página de login do flaskr](./static_files/flaskr_login.png)

O Flask é flexível. Ele não exige que você use qualquer projeto ou estutura de código em particular. No entanto, ao começar pela primeira vez, é útil use a solução mais estruturada. Isto signica que o tutorial exigirá um pouco de boilerplate no início, mas é feito para evitar muitos erros comuns com os quais desenvolvedores inexperientes se deparam, e ele cria um projeto que é fácil de expandir. Uma vez que você se sinta mais confortável com Flask, você pode abandonar esta estrutura e tirar vantagem da flexibilidade do Flask.

![página de editar do flaskr](./static_files/flaskr_edit.png)

[O projeto do tutorial está disponível como um exemplo no repositório do Flask](https://github.com/pallets/flask/tree/1.1.2/examples/tutorial), se você quiser comparar seu projeto com o produto final conforme você segue o tutorial.

Continue para [Estrutura do Projeto](#estrutura-do-projeto).

# Templates

O Flask usa o Jinja2 como seu gerador de templates. Obviamente você está livre para usar um gerador de template diferente, mas você ainda terá de instalar o Jinja2 para executar o Flask. Este requesito é necessário para ativar ricas extensões. Uma extensão pode depender da presença do Jinja2.

Esta seção somente lhe proverá uma introdução rápida sobre como o Jinja2 é integrado ao Flask. Se você quiser informação sobre a sintáxi do gerador de template, se diriga a [Documentação Oficial de Template do Jinja2](http://jinja.pocoo.org/docs/templates/) para obter mais detalhes.

## Configuração do Jinja2

A menos que seja modificada, o Jinja2 é configurado pelo Flask da seguinte maneira:

* autoescapamento está ativado para todos os templates com as extensões, `.html`, `.htm`, `.xml` como também `.xhtml` quando estiver usando a função **`render_template()`**.

* autoescapamento está ativado para todas as strings quando estiver usando a função **`render_template_string()`**.

* um template tem a habilidade de ativar/desativar o autoescapamento com a tag `{% autoescape %}`.

* o Flask injeta algumas funções globais e auxiliadores dentro do contexto do Jinja2, além dos valores que estão presentes por padrão.

## Contexto Padrão

O variávies globais a seguir estão disponíveis dentro dos templates Jinja2 por padrão:

* **`config`**: O objeto da configuração atual (**`flask.config`**)
  * Relatório de mudança, mudança introduzida na versão 0.10: este agora está disponível, até dentro de templates importados. Novo dentro da versão 0.6.

* **`request`**: o objeto da requisição atual (**`flask.request`**). Esta variável não estará disponível se o template for renderizado sem um contexto de requisição ativo.

* **`session`**: o objeto da seção atual (**`flask.session`**). Esta variável não estará disponível se o template for renderizado sem um contexto de requisição ativo.

* **`g`**: o objeto vinculado a requisição para variávies globais (**`flask.g`**). Esta variável não estará disponível se o template for renderizado sem um contexto de requisição ativo.

* **`url_for()`**: a função **`flask.url_for()`**.

* **`get_flashed_messages()`**: a função **`flask.get_flashed_messages()`**.


> ## O Comportamento de Contexto do Jinja
> Estas variáveis são adicionadas ao contexto de variáveis, elas não são variávies globais. A diferença é que por padrão estes não serão exibidos no contexto dos templates importados. Isto é parcialmante causado por questões performance, parcialmente por manter as coisas explicitas.
>
> O que isto significa para você? Se você tem um macro que deseja importar, que precisa acessar o objeto da requisição, você tem duas possibilidades:
>
> 1. Você explicitamente passa a requisição para o macro como paramêtro, ou o atributo do objeto da requisição que você está interessado.
>
> 2. Você importa o macro "com contexto (with context)".
>
> Importação com contexto se parece com isto:
>
> ```jinja2
> {% from '_helpers.html' import my_macro with context %}
>```

## Filtros Padrão

O Flask oferece as seguintes filtros para o Jinja2 além dos filtros oferecidos pelo próprio Jinja2:

**`tojson()`**: Esta função converte o objeto dado em uma representação JSON. Isto é, por exemplo, muito útil se você tentar gerar JavaScript dinamicamente.

```jinja2
<script type=text/javascript>
    doSomethingWith({{ user.username|tojson }});
</script>
```

Também é seguro usar a saída de |tojson em um atributo HTML entre aspas simples.

```jinja2
<button onclick='doSomethingWith({{ user.username|tojson }})'>
    Click me
</button>
```

Note que em versões do Flask anteriores a 0.10, se estiver usando a saída de `|tojson` dentro de `script`, deves se certificar de desativar o escapamento com `|safe`. No Flask 0.10 e adiante, isto acontece automaticamente.


## Controlando Autoescapamento

O autoescapamento é o conceito de automaticamente escapar caracteres especiais por você. Caracteres especiais no sentido de HTML (ou XML, e assim XHTML) são `&`, `>`, `<`, `"` como também `'`. Por esses caracteres carregarem significados especiais nos documentos por conta própria, você tem de substituir-los pelos chamadas "entidades" caso você quiser usá-los para compôr o texto. Não fazer isso podera causar não só frustrações ao usuário por não poder usar tais caracteres no texto, mas também pode conduzir a problemas de segurança. (Consulte [Cross-Site Scripting](#cross-site-scripting))

No entanto algumas vezes você precisará desativar o autoescapamento em templates. Este pode ser o caso se você quiser explicitamente injetar código HTML dentro das páginas, por exemplo se eles vierem de um sistema que gera código HTML seguro tal como um conversor de código Markdown para HTML.

Existem três maneiras de alcançar isso:

* No código Python, envolva a string de código HTML dentro de um objeto Markup passa-lo para o template. Esta é em geral a maneira recomendada.
* Dentro do template, use o filtro `!safe` para explicitamente marcar uma string como código HTML seguro (`{{ myvariable|safe }}`)
* Desativar totalmente por tempo indeterminado o sistema de autoescapamento.

Para desativar o sistema de autoescapemento em templates, você pode usar o bloco `{% autoescape %}`:

```jinja2
{% autoescape false %}
    <p>autoescaping is disabled here
    <p>{{ will_not_be_escaped }}
{% endautoescape %}
```

Sempre que fizer isso, por favor seja muito cauteloso em relação as variáveis que você estiver usando neste bloco.


## Registando Filtros

Se você quiser registar seus próprios filtros no Jinja2, você tem duas maneiras de fazer isso. Você pode tanto pôr eles dentro da váriavel **`jinja_env`** ou usar o decorador **`template_filter()`**.

Ambos os dois exemplos seguintes funcionam e ambos invertem um objeto:

```py
@app.template_filter('reverse')
def reverse_filter(s):
    return s[::-1]

def reverse_filter(s):
    return s[::-1]
app.jinja_env.filters['reverse'] = reverse_filter
```

No caso de decorador, o argumento é opcional se você quiser usar o nome da função como nome do filtro. Uma vez registada, você pode usar o filtro dentro do seu template da mesma maneira que se usa os filtros que vêm imbutido dentro do Jinja2, por exemplo se você tem no contexto uma lista Python chamada *mylist*:

```jinja2
{% for x in mylist | reverse %}
{% endfor %}
```

## Processadores de Contexto

Existe dentro do Flask processadores de contexto, que lhe permitem injetar automaticamente novas variáveis dentro do contexto de um template. Processadores de contexto executam antes do template ser renderizado e possuem a habilidade de injetar novos valores dentro do contexto do template. Um processador de contexto é uma função que retorna um dicionário. As chaves e valores do dicionário são depois combinadas com o template, para todos os templates na aplicação:

```py
@app.context_processor
def inject_user():
    return dict(user=g.user)
```

O processador de contexto descrito acima torna uma variável chamada *user* com o valor de *g.user* disponível dentro do template. Este exemplo não é de todo muito interessante visto que *g* já está disponível dentro do template de qualquer maneira, mas dá uma ideia de como a coisa funciona.

Variáveis não estão limitadas aos valores; um processador de contexto podem também tornar funções disponíveis para o template (desde que o Python permita passar funções):

```py
@app.context_processor
def utility_processor():
    def format_price(amount, currency='€'):
        return f'{amount:.2f}{currency}'
    return dict(format_price=format_price)
```

O processador de contexto acima torna a função *format_price* disponível em todos os templates.


# Testando Aplicações Flask

**O que não estiver testado, está quebrado**

A origem desta citação é desconhecida e enquanto que não é inteiramente verdadeiro, também não está longe da verdade. Aplicações que não são testadas fazem com que seja difícil de melhorar o código existente e desenvolvedores de aplicações que não usam testes tendem a se tornar muito paranoicos. Se uma aplicação faz uso de testes automatizados, você pode seguramente fazer mudanças e instantaneamente saber se alguma coisa quebrou.

Flask fornece uma maneira de testar sua aplicação através da exposição do [cliente de teste do Werkzeug](https://werkzeug.palletsprojects.com/en/2.0.x/test/#werkzeug.test.Client) e manipula os contexto locais por você. Você pode então usa-lo com a sua solução de teste favorita.

Nesta documentação usaremos o pacote [pytest](https://docs.pytest.org/) como framework base para os nossos testes. Você pode instala-lo com o comando `pip`, como em:

```sh
$ pip install pytest
```


## A Aplicação

Primeiro, precisaremos de uma aplicação para testar; usaremos a aplicação construida na seção do [Tutorial](#tutorial). Se você ainda não tiver a aplicação, pegue o código-fonte dos [exemplos](https://github.com/pallets/flask/tree/2.0.1/examples/tutorial).

Só então é que podemos importar o módulo `flaskr` correctamente, precisamos executar `pip install -e .` dentro da pasta tutorial.


## O Esqueleto dos Testes

Nós começaremos por adicionar uma pasta de testes com o nome `tests` dentro da raíz da aplicação. Depois criar um ficheiro em Python com o nome `test_flaskr.py` para guardar os nossos testes. Quando formatamos o nome do ficheiro como `test_*.py`, ele será automaticamente alcançado pelo pytest.

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

Esta fixture do cliente será chamada em cada teste individual. Isto dá-nos uma interface símples para aplicação, onde poderemos realizar requisições de teste para aplicação. O cliente também rasteará os cookies por nós.

Durante a configuração, a bandeira (flag) **`TESTING`** é ativada. O que isto faz é desactivar a captura de erros durante a realização de uma requisição, assim você pode receber relatório de erros melhores quando estiver realizando requisições de teste contra a aplicação.

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

Apesar de não ter executado nenhum teste atual, já sabemos que nossa aplicação **`flaskr`** é sintaticamente valída, ou do contrário a importação teria morrido com uma exceção.


## O Primeiro Teste

Agora é o momento de começar a testar a funcionalidade da aplicação. Vamos verificar se a aplicação exibe o texto "No entries here so far" quando acessamos a raíz da aplicação (/). Para fazer isso, adicionamos uma nova função de teste como esta ao **`test_flaskr.py`**:

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


## Outras Tecnicas de Testes

Além de usar o cliente de teste como é exibido acima, há támbém o método **`test_request_context()`** que pode ser usado combinado com o declaração `with` para ativar uma contexto de requisição temporariamente. Com isto você pode acessar os objetos **`request`**, **`g`** e **`session`** como nas funções de apresentação (views). Aqui está um exemplo completo que demostra o uso desta técnica:

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

* Relatório de Mundaça
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

* Relatório de Mundaça
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

* Relatório de Mundaça
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
