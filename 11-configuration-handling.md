# Manipulação da Configuração

Aplicações precisam algum tipo de configuração. Existem diferentes definições que você pode quer mudar dependendo do ambiente da aplicação como alternando o modo de depuração, definindo a chave secreta, ou outras coisas especificas do ambiente.

O maneira que o Flask está desenhado geralmente requer que a configuração esteja disponível quando a aplicação iniciar. Você pode escrever manualmente a configuração dentro do código, o que para muitas aplicações pequenas não é assim tão mau, mas existem maneiras melhores.

Independentemente de como você carregar a sua configuração, há um objeto de configuração disponível o que segura os valores de configuração carregados: O atributo **`config`** do objeto **`Flask`**. Este é o lugar onde o próprio Flask coloca certos valores de configuração e também onde as extensões podem colocar seus valores de configuração. Porém este é também onde você pode ter sua própria configuração.


## Configurações Básicas

O atributo **`config`** é efetivamente uma subclasse de um dicionário e pode ser modificado como qualquer dicionário:

```py
app = Flask(__name__)
app.config['TESTING'] = True
```

Certos valores de configuração são também avançado para o objeto **`Flask`** assim você pode ler e escrever eles por aí:

```py
app.testing = True
```

Para atualizar várias chaves de uma só vez você pode usar o método **`dict.update()`**:

```py
app.config.update(
  TESTING=True,
  SECRET_KEY=b'_5#y2L"F4Q8z\n\xec]/'
)
```

## Ambiente e Funcionalidades de Depuração

Os valores de configuração [**`ENV`**](#env) e [**`DEBUG`**](#debug) são especiais porque eles podem agir inconsistentemente se forem mudados depois da aplicação ter começado a composição. No sentido de definir o ambiente e modo de depuração com segurança, O Flask usa variáveis de ambientes.

O ambiente é usado para indicar ao Flask, as extensões, e outros programas, como Sentry, qual contexto o Flask está executando. Ele é controlado com o a variável de ambiente **`FLASK_ENV`** e padrões para `production` (produção).

Definir **`FLASK_ENV`** para `development` (desenvolvimento) ativará o modo de depuração. O comando `flask run` usará o depurador interativo e o recarregador por padrão dentro do modo de depuração. Para controlar isto separadamente do ambiente, use a bandeira **`FLASK_DEBUG`**.

* Relatório da Mudança: Mudado na versão 1.0: **`FLASK_ENV`** adicionado para controlar o ambiente separadamente a partir do modo de depuração. O ambiente de desenvolvimento ativa o modo de depuração.

Para mudar o Flask para o ambiente de de desenvolvimento e ativar o modo de depuração, defina **`FLASK_ENV`**:

Bash
```sh
$ export FLASK_ENV=development
$ flask run
```

CMD
```cmd
> set FLASK_ENV=development
> flask run
```

Powershell
```powershell
> $env:FLASK_ENV = "development"
> flask run
```

É recomendado usar as variáveis de ambiente como foi descrito acima. Enquanto é possível definir o [**`ENV`**](#env) e o [**`DEBUG`**](#debug) dentro da sua configuração ou código, isto é fortemente desencorajado. Eles não podem ser lidos antecipadamente pelo comando `flask`, e alguns sistemas ou extensões podem ter já configurada elas mesmas baseadas em valor anterior.


## Valores de Configuração Embutidos

Os seguintes valores de configuração são usados internamente pelo Flask:

**`ENV`**: Qual ambiente a aplicação está executando. O Flask e as extensões podem ativar comportamentos baseados no ambiente, tal como ativar o modo de depuração. O atributo **`env`** mapeia até esta chave de configuração. Este é definido pela variável de ambiente **`FLASK_ENV`** e pode não agir como esperado se definida no código.

**Não ative o desenvolvimento quando estiver desdobrando na produção.**

Valor padrão: `production`

* Relatório de Mudança: Novo desde a versão `1.0`.


**`DEBUG`**: Se o modo de depuração estiver ativado. Quando estiver usando o comando `flask run` para iniciar o servidor de desenvolvimento, um depurador interativo será exibido para exceções não manipuladas, e o servidor será recarregado sempre que houver mudanças no código. O atributo **`debug`** mapeia para esta chave de configuração. Esta é ativada quando o [**`ENV`**](#env) está em `development` e é sobrescrita pela variável de ambiente `FLASK_DEBUG`. Ela pode não agir como esperado se definida no código.

**Não ative o modo de depuração quando estiver desdobrando na produção.**

Valor padrão: `True` se [**`ENV`**] está como `development`, ou do contrário é `False`.


**`TESTING`**: Ativa o modo de testes. As exceções são propagadas em vez de serem manipuladas pelos manipuladores de erros da aplicação. As exceções podem também mudar o seu comportamento para tornar os testes mais fáceis. Você deve ativar esta dentro dos seus próprios testes.

Valor padrão: `False`


**`PROPAGATE_EXCEPTIONS`**: Exceções são levantadas novamente ao invés de serem manipuladas pelos manipuladores de erros da aplicação. Se não for definida, ele é implicitamente `true` se o **`TESTING`** ou **`DEBUG`** estiver ativado.

Valor padrão: `None`


**`PRESERVE_CONTEXT_ON_EXCEPTION`**: Não estoura o contexto da requisição quando uma exceção ocorrer. Se não for definida, ele é `true` se o **`DEBUG`** for `true`. Isto permite os depuradores analisar o interior dos dados da requisição em erros, e normalmente não precisa ser definida diretamente.

Valor padrão: `None`


**`TRAP_HTTP_EXCEPTIONS`**: Se não houver manipulador para uma exceção do tipo `HTTPException`, levanta novamente ele para ser manipulado pelo depurador interativo ao invés de retornar ele como uma resposta de erro simples.

Valor padrão: `False`


**`TRAP_BAD_REQUEST_ERRORS`**: Ao tentar acessar uma chave que não existe a partir dos dicionários da requisição como `args` e `form` retornarão uma página de erro 400 Má Requisição. Ativa isto para tratar o erro como uma exceção não manipulada assim você recebe o depurador interativo. Isto é uma versão mais específica de **`TRAP_HTTP_EXCEPTIONS`**. Se não definida, ela é ativada em modo de depuração.

Valor padrão: `None`


**`SECRET_KEY`**: Um chave secreta que será usada para seguramente assinar a cookie da sessão e pode ser usada para qualquer outra necessidade relacionada a segurança pelas extensões ou pela sua aplicação. Ela deve ser um `bytes` ou `str` longo e aleatório. Por exemplo, copie a saída disto para a sua configuração:

```sh
$ python -c 'import os; print(os.urandom(16))'
b'_5#y2L"F4Q8z\n\xec]/'
```

**Não revele a chave secreta quando estiver publicando questões ou consolidando o código**.

Valor padrão: `None`


**`SESSION_COOKIE_NAME`**: O nome do cookie da sessão. Pode ser mudado em caso de você já tiver um cookie com o mesmo nome.

Valor padrão: `session`


**`SESSION_COOKIE_DOMAIN`**: A regra de correspondência de domínio para qual o cookie da sessão será valida. Se não for definido, o cookie será valido para todos subdomínios do **`SERVER_NAME`**. Se for `False`, o domínio do cookie não será definido.

Valor padrão: `None`


**`SESSION_COOKIE_PATH`**: O caminho para qual o cookie da sessão será valido. Se não for definida, o cookie será valido por baixo da **`APPLICATION_ROOT`** ou **`/`** se ela não for definida.

Valor padrão: `None`


**`SESSION_COOKIE_HTTPONLY`**: Os navegadores não permitirão o JavaScript acessar os cookies marcados como “HTTP only (Apenas HTTP)” por segurança.

Valor padrão: `True`


**`SESSION_COOKIE_SECURE`**: Os navegadores apenas enviarão cookies com as requisições sobre o HTTPS se o cookie for marcado como “secure (seguro)”. A aplicação deve ser servida sobre o HTTPS para isto fazer sentido.

Valor padrão: `False`


**`SESSION_COOKIE_SAMESITE`**: Restringe como os cookies são enviados com as requisições a partir de sítios externos. Pode ser definido para '`Lax`' (recomendado) ou '`Strict`'. Consulte as [opções Set-Cookie]().

Valor padrão: `None`

* Registo de Mudança: Novo desde a versão 1.0


**`PERMANENT_SESSION_LIFETIME`**: Se o `session.permanent` for `true`, a expiração do cookie será definir este número de segundos no futuro. Pode ser tanto um `datetime.timedelta` ou um `int`.

A implementação de cookie padrão do Flask valida que a assinatura criptográfica não é mais antiga que este valor.

Valor padrão: `timedelta(days=31) (2678400 segundos)`


**`SESSION_REFRESH_EACH_REQUEST`**: Controla se o cookie foi enviado com toda resposta quando o `session.permanent` for `true`. Enviar o cookie a todo momento (o padrão) pode mais confiavelmente guardar a sessão da expiração, porém usa mais banda larga. Sessões não permanentes não são afetadas.

Valor padrão: `True`


**`USE_X_SENDFILE`**: Quando estiver servindo ficheiros, define o cabeçalho `X-Sendfile` ao invés de servir os dados com o Flask. Alguns servidores da web, tais como o Apache, reconhecem isso e servem os dados mais eficientemente. Isso só faz sentido quando estiver usando esse servidor. 

Valor padrão: `False`


**`SEND_FILE_MAX_AGE_DEFAULT`**: Quando estiver servindo os ficheiros, define a idade máxima do control de cache para este número de segundos. Pode ser um `datetime.timedelta` ou um `int`. Sobrescreve este valor em uma base por ficheiro usando o `get_send_file_max_age()` na aplicação ou na estrutura.

Se for `None`, o `send_file` fiz ao navegador para usar requisições condicionais que serão usadas ao invés de um cache cronometrado, que é normalmente preferível.

Valor padrão: `None`


**`SERVER_NAME`**: Informa a aplicação qual hospedeiro e porta ela está ligada. Exigido para o suporte de correspondência de rota de subdomínio.

Se for definida, será usada para o domínio do cookie da sessão se o **`SESSION_COOKIE_DOMAIN`** não estiver definido. Os navegadores da web modernos não permitirão a definição de cookies para domínios sem um ponto. Par usar um domínio localmente, adicione quaisquer nomes que devem encaminhar para aplicação ao seu ficheiro `hosts`.

```
127.0.0.1 localhost.dev
```

Se for definido, `url_for` pode gerar URLs externas com apenas um contexto de aplicação ao invés de um contexto de requisição.

Valor padrão: `None`


**`APPLICATION_ROOT`**: Informa a aplicação a qual caminho ela está mondada por baixo da aplicação / servidor da web. Isto é usado para geração de URLs fora do contexto de uma requisição (dentro de uma requisição, os despachador é responsável pela definição do **`SCRIPT_NAME`**; consulte o [Despacho da Aplicação]() para exemplos de configuração do despacho).

Será usado para o caminho do cookie da sessão se o **`SESSION_COOKIE_PATH`** não for definido.

Valor padrão: `/`


**`PREFERRED_URL_SCHEME`**: Usa este esquema para geração de URLs externas quando não estiver em um contexto de requisição.

Valor padrão: `http`


**`MAX_CONTENT_LENGTH`**: Não leia mais do que estes muitos bytes de dados da requisição a caminho. Se não for definido a requisição não especifica um `CONTENT_LENGTH`, nenhum dado será lido por segurança.

Valor padrão: `None`


**`JSON_AS_ASCII`**: Serializa os objetos para JSON de ASCII encriptado. Se isto estiver desativado, o JSON retornado de `jsonify` conterá caracteres de Unicode. Isto tem implicações de segurança quando estiver renderizando o JSON dentro de JavaScript em modelos de marcação, e normalmente deve continuar ativado.

Valor padrão: `True`


**`JSON_SORT_KEYS`**: Organiza alfabeticamente as chaves de objetos JSON. Isto é útil para o cacheamento porque ele garante que o dado está serializado da mesma maneira não importa qual seja a semente de hash do Python. Embora não recomendado, você pode desativar isto para uma possível melhora de desempenho as custas do cacheamento.

Valor padrão: `True`


**`JSONIFY_PRETTYPRINT_REGULAR`**: As respostas de `jsonify` serão emitidas com novas linhas, espaços e indentações para mais fácil leitura por humanos. Sempre ativada no modo de depuração.

Valor padrão: `False`


**`JSONIFY_MIMETYPE`**: O mimetype das respostas de `jsonify`.

Valor padrão: `application/json`


**`TEMPLATES_AUTO_RELOAD`**: Recarrega os modelos de marcação quando eles forem alterados. Se for defino, ele será ativado no modo de depuração.

Valor padrão: `None`


**`EXPLAIN_TEMPLATE_LOADING`**: Regista a informação de depuração rastreando como um ficheiro de modelo de marcação foi carregado. Isto pode ser útil para descobrir o porque que um modelo de marcação não foi carregado ou o porque que ficheiro errado parece ser carregado.

Valor padrão: `False`


**`MAX_COOKIE_SIZE`**: Avisa se os cabeçalhos de cookie forem maiores do que estes muitos bytes. Padronizado para `4093`. Cookies maiores podem ser silenciosamente ignorados pelos navegadores. Defina para `0` para desativar o aviso.

Relatório de Mudança:
  * Mudando na versão 1.0: `LOGGER_NAME` e `LOGGER_HANDLER_POLICY` foram removidos. Consulte a secção [Registo de Atividade]() para informações sobre a configuração.

  * [**`ENV`**]() adicionado para refletir a variável de ambiente **`FLASK_ENV`**.

  * [**`SESSION_COOKIE_SAMESITE`**]() adicionado para controlar a opção **`SameSite`** do cookie da sessão.

  * [**`MAX_COOKIE_SIZE`**]() adicionado para controlar um aviso de Werkzeug.

  * Novo desde a versão 0.11: **`SESSION_REFRESH_EACH_REQUEST`**, **`TEMPLATES_AUTO_RELOAD`**, **`LOGGER_HANDLER_POLICY`**, **`EXPLAIN_TEMPLATE_LOADING`**.

  * Novo desde a versão 0.10: **`JSON_AS_ASCII`**, **`JSON_SORT_KEYS`**, **`JSONIFY_PRETTYPRINT_REGULAR`**.

  * Novo desde a versão 0.9: **`PREFERRED_URL_SCHEME`**.

  * Novo desde a versão 0.8: **`TRAP_BAD_REQUEST_ERRORS`**, **`TRAP_HTTP_EXCEPTIONS`**, **`APPLICATION_ROOT`**, **`SESSION_COOKIE_DOMAIN`**, **`SESSION_COOKIE_PATH`**, **`SESSION_COOKIE_HTTPONLY`**, **`SESSION_COOKIE_SECURE`**.

  * Novo desde a versão 0.7: **`PROPAGATE_EXCEPTIONS`**, **`PRESERVE_CONTEXT_ON_EXCEPTION`**

  * Novo desde a versão 0.6: **`MAX_CONTENT_LENGTH`**

  * Novo desde a versão 0.5: **`SERVER_NAME`**

  * Novo desde a versão 0.4: **`LOGGER_NAME`**


## Configurando a partir de Ficheiros de Python

A configuração se torna mais útil se você puder guardar ela em um ficheiro separado, idealmente localizado fora do pacote da aplicação atual. Isto torna o empacotamento e a distribuição de sua aplicação possível através de várias ferramentas de manipulação de pacote ([Desdobrando com o Setuptools]()) e finalmente modificar o ficheiro de configuração mais adiante.

Assim um padrão comum é:

```py
app = Flask(__name__)
app.config.from_object('yourapplication.default_settings')
app.config.from_envvar('YOURAPPLICATION_SETTINGS')
```

Isto primeiro carrega a configuração a partir do módulo *`yourapplication.default_settings`* e depois sobrescreve os valores com os conteúdos do ficheiro que a variável de ambiente **`YOURAPPLICATION_SETTINGS`** aponta. Esta variável de ambiente pode ser definida dentro do shell antes de inicializar o servidor:

Bash
```sh
$ export YOURAPPLICATION_SETTINGS=/path/to/settings.cfg
$ flask run
 * Running on http://127.0.0.1:5000/
```

CMD
```cmd
> set YOURAPPLICATION_SETTINGS=\path\to\settings.cfg
> flask run
 * Running on http://127.0.0.1:5000/
```

Powershell
```ps
> $env:YOURAPPLICATION_SETTINGS = "\path\to\settings.cfg"
> flask run
 * Running on http://127.0.0.1:5000/
```

Os ficheiros de configuração eles mesmos são de fato ficheiros de Python. Somente os valores em maiúsculo são de fato guardados dentro do objeto de configuração mais tarde. Então certifique-se de usar letras maiúsculas para suas chaves de configuração.

Aqui está um exemplo de um ficheiro de configuração:

```py
# Example configuration
SECRET_KEY = b'_5#y2L"F4Q8z\n\xec]/'
```

Certifique-se de carregar a configuração mais cedo, pare que as extensões tenham a habilidade de acessar a configuração quando estiverem iniciando. Existem também outros métodos no objeto de configuração para carregar a partir de ficheiros individuais. Para um referência completa, leia a documentação do objeto [**`Config`**]().


## Configurando a partir de Ficheiros de Dados

É também possível carregar a configuração a partir de um ficheiro em um formato de sua escolha usando o método [**`from_file()`**](). Por exemplo carregar a partir de um ficheiro TOML:

```py
import toml
app.config.from_file("config.toml", load=toml.load)
```

Ou a partir de um ficheiro JSON:

```py
import json
app.config.from_file("config.json", load=json.load)
```

## Configurando a partir de Variáveis de Ambiente

Além de apontar para ficheiros de configuração usando variáveis de ambiente, você pode achar ele útil (ou necessário) para controlar seus valores de configuração diretamente do ambiente.

Variáveis de ambiente podem ser definidas na shell antes de iniciar o servidor:

Bash
```sh
$ export SECRET_KEY="5f352379324c22463451387a0aec5d2f"
$ export MAIL_ENABLED=false
$ flask run
 * Running on http://127.0.0.1:5000/
```

CMD
```cmd
> set SECRET_KEY="5f352379324c22463451387a0aec5d2f"
> set MAIL_ENABLED=false
> flask run
 * Running on http://127.0.0.1:5000/
```

Powershell
```ps
> $env:SECRET_KEY = "5f352379324c22463451387a0aec5d2f"
> $env:MAIL_ENABLED = "false"
> flask run
 * Running on http://127.0.0.1:5000/
```

Enquanto esta abordagem é de uso direto, é importante lembrar que as variáveis de ambiente são sequências de caracteres – eles não são automaticamente desserializados em tipos de Python.

Aqui está um exemplo de um ficheiro de configuração que usa variáveis de ambiente:

```py
import os

_mail_enabled = os.environ.get("MAIL_ENABLED", default="true")
MAIL_ENABLED = _mail_enabled.lower() in {"1", "t", "true"}

SECRET_KEY = os.environ.get("SECRET_KEY")

if not SECRET_KEY:
    raise ValueError("No SECRET_KEY set for Flask application")
```

Repare que qualquer valor além de uma sequência de caracteres vazia será interpretado como um valor booleano `True` em Python, o que requer cuidado se um ambiente explicitamente define valores destinados a serem `False`.

Certifique-se de carregar a configuração mais cedo, assim que as extensões terão a habilidade de acessar a configuração quando iniciar. Existem outros métodos no objeto de configuração bem como carregar a partir de ficheiros individuais. Para uma referência completa, leia a documentação da classe **`Config`**.


## Boas Práticas de Configuração

O lado negativo com a abordagem mencionada mais cedo é que esta torna os testes um pouco mais difícil. Em geral não há um única solução a 100% para este problema, mas há coisas que você pode manter em mente para melhor essa experiência:

1. Crie a sua aplicação dentro de uma função e regista as estruturas nela. Assim você pode criar várias instâncias de sua aplicação com diferentes configurações anexadas o que torna os testes unitários muito mais fácil. Você pode usar isto para passar a configuração quando necessário.

2. Não escreva código que precisa da configuração no momento da importação. Se você se limitar a acessos de somente requisição para configuração você pode reconfigurar o objeto mais tarde quando necessário.


## Desenvolvimento / Produção

A maioria das aplicações precisam mais de uma configuração. Deve haver pelo menos configurações separadas para o servidor de produção e aquela usada durante o desenvolvimento. A maneira mais fácil de lidar com isso é usar uma configuração padrão que sempre será carregada e parte do controle de versão, e uma configuração separada que sobrescreve os valores quando necessário como mencionado no exemplo abaixo:

```py
app = Flask(__name__)
app.config.from_object('yourapplication.default_settings')
app.config.from_envvar('YOURAPPLICATION_SETTINGS')
```

Depois você só precisa de um ficheiro `config.py` separado e exportar `YOURAPPLICATION_SETTINGS=/path/to/config.py` e você terminou. No entanto também existem caminhos alternativos. Por exemplo você poderia usar importações e subclassificações.

O que é muito popular dentro do mundo de Django é tornar a importação explicita dentro do ficheiro de configuração pela adição de `from yourapplication.default_settings import *` no princípio do ficheiro e depois e depois sobrescrever as mudanças de forma manual. Você poderia também inspecionar uma variável de ambiente como `YOURAPPLICATION_MODE` e definir ela para *production*, *development* etc e com base nisso importar diferentes ficheiros codificados.

Um padrão também interessante é usar classes e herança para a configuração:

```py
class Config(object):
    TESTING = False

class ProductionConfig(Config):
    DATABASE_URI = 'mysql://user@localhost/foo'

class DevelopmentConfig(Config):
    DATABASE_URI = "sqlite:////tmp/foo.db"

class TestingConfig(Config):
    DATABASE_URI = 'sqlite:///:memory:'
    TESTING = True
```

Para ativar tal configuração você apenas precisa chamar ela dentro de **`from_object()`**:

```py
app.config.from_object('configmodule.ProductionConfig')
```

Repare que **`from_object()`** não instância a classe do objeto. Se você precisar instanciar a classe, tal como para acessar uma propriedade, então você deve fazer isto antes de chamar o **`from_object()`**:

```py
from configmodule import ProductionConfig
app.config.from_object(ProductionConfig())

# Alternatively, import via string:
from werkzeug.utils import import_string
cfg = import_string('configmodule.ProductionConfig')()
app.config.from_object(cfg)
```

A instanciação do objeto de configuração permite você usar o decorador `@property` dentro de suas classes de configuração:

```py
class Config(object):
    """Base config, uses staging database server."""
    TESTING = False
    DB_SERVER = '192.168.1.56'

    @property
    def DATABASE_URI(self):  # Note: todos letras maiúsculas
        return f"mysql://user@{self.DB_SERVER}/foo"

class ProductionConfig(Config):
    """Uses production database server."""
    DB_SERVER = '192.168.19.32'

class DevelopmentConfig(Config):
    DB_SERVER = 'localhost'

class TestingConfig(Config):
    DB_SERVER = 'localhost'
    DATABASE_URI = 'sqlite:///:memory:'
```

Existem várias maneiras diferentes e está sobre você como você deseja gerir os seus ficheiros de configuração. Contudo aqui está uma lista de boas recomendações:

* Mantenha uma configuração padrão no controle de versão. Seja para popular a configuração com esta configuração padrão ou importar ela de dentro de seus próprios ficheiros de configuração antes de sobrescrever os valores.

* Use uma variável de ambiente para mudar entre as configurações. Isto pode ser feito a partir de fora do interpretador do Python e torna o desenvolvimento e desdobramento muito mais fácil porque você pode rapidamente e facilmente mudar entre diferentes configurações sem ter de tocar no código. Se você estiver trabalhando com frequência em diferentes projetos você pode até mesmo criar seu próprio script para terceirização que ativa um ambiente virtual e exporta a configuração de desenvolvimento por você.

* Use uma ferramenta como a [fabric](https://www.fabfile.org/) em produção para empurrar o código e as configurações separadamente para o servidor de produção. Para mais detalhes sobre como fazer isso, consulte o padrão de [Desdobrando com a Fabric]().


## Pastas da Instância

* Relatório de mudança: Novo desde a versão 0.8

A versão 8.0 da Flask introduziu as pastas de instância. A Flask por um longo período tornou possível referir-se aos cominhos relativos para a pasta da aplicação diretamente (através de **`Flask.root_path`**). Isto também foi como muitos desenvolvedores carregavam as configurações guardadas próximas à aplicação. Infelizmente no entanto isto apenas funciona bem se as aplicações não forem pacotes, em cujo caso o caminho da raiz refere-se aos conteúdos do pacote.

Com a Flask 0.8 um novo atributo foi introduzido: **`Flask.instance_path`**. Ele refere-se a um novo conceito chamado de “pasta da instância”. A pasta da instância foi desenhada para não estar sob controle de versão e ser especifica do desdobramento. É o lugar perfeito para deixar coisas que ou mudam em tempo de execução ou são ficheiros de configuração.

Você pode ou explicitamente fornecer o caminho da pasta da instância quando estiver criando a aplicação Flask ou pode deixar a Flask detetar automaticamente a pasta da instância. Para configuração explicita use o parâmetro *instance_path*:

```py
app = Flask(__name__, instance_path='/path/to/instance/folder')
```

Tenha em mente que este cominho *deve* ser absoluto quando fornecido.

Se o parâmetro *instance_path* não for fornecido as seguintes localizações padrão são usadas:

* Módulo desinstalado:

```
/myapp.py
/instance
```

* Pacote desinstalado:

```
/myapp
    /__init__.py
/instance
```

* Módulo ou pacote instalado:

```
$PREFIX/lib/pythonX.Y/site-packages/myapp
$PREFIX/var/myapp-instance
```

O `$PREDIX` é o prefixo da sua instalação do Python. Este pode ser `/usr` ou o caminho para o seu ambiente virtual. Você pode imprimir o valor de `sys.prefix` para ver qual prefixo está definido.

Visto que o objeto de configuração fornecido, carregando ficheiros de configuração a partir de nomes de ficheiros relativos, nós tornamos possível mudar o carregamento através de nomes de ficheiros para serem relativos ao cominho da instância se desejado. O comportamento dos caminhos relativos nos ficheiros de configuração pode ser mudado entre “relativo a raiz da aplicação” (o padrão) para “relative a pasta da instância” através da opção *instance_relative_config* para o construtor da aplicação.

```py
app = Flask(__name__, instance_relative_config=True)
```

Aqui está um exemplo completo de como configurar a Flask para pré-carregar a configuração a partir de um módulo e depois sobrescreve a configuração a partir de um ficheiro dentro da pasta da instância se ele existir:

```py
app = Flask(__name__, instance_relative_config=True)
app.config.from_object('yourapplication.default_settings')
app.config.from_pyfile('application.cfg', silent=True)
```

O caminho para a pasta da instância pode ser encontrado por intermédio de **`Flask.instance_path`**. A Flask também fornece um atalho para abrir um ficheiro a partir da pasta da instância com **`Flask.open_instance_resource()`**.

Exemplo de uso para ambos:

```py
filename = os.path.join(app.instance_path, 'application.cfg')
with open(filename) as f:
    config = f.read()

# ou por intermédio de open_instance_resource:
with app.open_instance_resource('application.cfg') as f:
    config = f.read()
```