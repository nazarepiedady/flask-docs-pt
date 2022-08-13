# Registando


O Flask faz uso do [**``logging**](https://docs.python.org/3/library/logging.html#module-logging), módulo padrão do Python. As mensagens sobre o estado da sua aplicação são registadas com o [**`app.logger`**](), que recebe o mesmo nome que [**`app.name`**](). Este registador também pode ser usado para registar suas próprias mensagens.

```py
@app.route('/login', methods=['POST'])
def login():
    user = get_user(request.form['username'])

    if user.check_password(request.form['password']):
        login_user(user)
        app.logger.info('%s logged in successfully', user.username)
        return redirect(url_for('index'))
    else:
        app.logger.info('%s failed to log in', user.username)
        abort(401)
```

Se você não configurar o registador de atividades, o nível de monitoramento padrão do Python normalmente é de 'aviso (warning)'. Nada que estiver abaixo do nível configurado estará visível.


## Configuração Básica

Sempre que quiser configurar o registador de atividades para seu projeto, você deve fazer isso o mais cedo possível logo quando o programa inicia. Se [**`app.logger`**]() for acessado antes do registador de atividades ser configurado, ele adicionará  um manipulador padrão. Se possível, configure o registador de atividades antes da criação do objeto da aplicação.

Este exemplo faz uso do [**`dictConfig`**](https://docs.python.org/3/library/logging.config.html#logging.config.dictConfig) para criar uma configuração para o registador de atividades, similar ao que é padrão do Flask, exceto para todos os registos:

```py
from logging.config import dictConfig

dictConfig({
    'version': 1,
    'formatters': {'default': {
        'format': '[%(asctime)s] %(levelname)s in %(module)s: %(message)s',
    }},
    'handlers': {'wsgi': {
        'class': 'logging.StreamHandler',
        'stream': 'ext://flask.logging.wsgi_errors_stream',
        'formatter': 'default'
    }},
    'root': {
        'level': 'INFO',
        'handlers': ['wsgi']
    }
})

app = Flask(__name__)
```


## Configuração Padrão

Se você mesmo não configurar o registador de atividades, o Flask adicionará um [**`StreamHandler`**](https://docs.python.org/3/library/logging.handlers.html#logging.StreamHandler) a [**`app.logger`**] automaticamente. Durante as requisições, ele escreverá para saída especificada pelo servidor WSGI no `environ['wsgi.errors']` (que normalmente é [**`sys.stderr`**](https://docs.python.org/3/library/sys.html#sys.stderr)). Fora de uma requisição, ele registará no [**`sys.stderr`**](https://docs.python.org/3/library/sys.html#sys.stderr).


## Removendo o Manipulador Padrão

Se você configurar o registador de atividades depois de acessar o [**`app.logger`**](), e precisar remover o manipulador padrão, você pode importa-lo e remove-lo:

```py
from flask.logging import default_handler

app.logger.removeHandler(default_handler)
```


## Email de Erros para os Administradores

No momento que aplicação estiver executando em um servidor remoto para produção, você provavelmente não estará consultando as mensagens no registo de atividades com frequência. O servidor WSGI provavelmente enviará mensagens sobre o atividade para um ficheiro, e você somente consultará aquele ficheiro se um usuário informar você que algo de errado aconteceu.

Para ser pró-ativo no que diz respeito a achar e corrigir problemas na aplicação (bugs), você pode configurar um [**`logging.handlers.SMTPHandler`**](https://docs.python.org/3/library/logging.handlers.html#logging.handlers.SMTPHandler) para enviar um email sempre que erros ou algo mais forem registados.

```py
import logging
from logging.handlers import SMTPHandler

mail_handler = SMTPHandler(
    mailhost='127.0.0.1',
    fromaddr='server-error@example.com',
    toaddrs=['admin@example.com'],
    subject='Application Error'
)
mail_handler.setLevel(logging.ERROR)
mail_handler.setFormatter(logging.Formatter(
    '[%(asctime)s] %(levelname)s in %(module)s: %(message)s'
))

if not app.debug:
    app.logger.addHandler(mail_handler)
```

Isto requer que você tenha um servidor SMTP configurado no mesmo servidor. Consulte a documentação do Python para obter mais informações sobre a configuração de um manipulador.


## Injetando Informações da Requisição

Ver mais informações sobre a requisição, tais como o endereço IP, pode ajudar na depuração de alguns erros. Você pode subclassificar o **`logging.Formatter`** para injetar seus próprios campos que podem ser usados dentro de mensagens. Você pode mudar o formatador para o manipulador padrão do Flask, o manipulador de correio definido acima, ou qualquer outro manipulador.

```py
from flask import has_request_context, request
from flask.logging import default_handler

class RequestFormatter(logging.Formatter):
    def format(self, record):
        if has_request_context():
            record.url = request.url
            record.remote_addr = request.remote_addr
        else:
            record.url = None
            record.remote_addr = None

        return super().format(record)

formatter = RequestFormatter(
    '[%(asctime)s] %(remote_addr)s requested %(url)s\n'
    '%(levelname)s in %(module)s: %(message)s'
)
default_handler.setFormatter(formatter)
mail_handler.setFormatter(formatter)
```

## Outras Bibliotecas

Outras bibliotecas podem usar extensivamente o logging, e você quer também ver mensagens relevantes daquele registo. A maneira mais simples de fazer isso é adicionar manipuladores ao registador raiz ao invés de somente o registador da aplicação.

```py
from flask.logging import default_handler

root = logging.getLogger()
root.addHandler(default_handler)
root.addHandler(mail_handler)
```

Dependendo do seu projeto, pode ser mais útil configurar cada registador que você cuida separadamente, ao invés de configurar apenas o registador raiz.

```py
for logger in (
    app.logger,
    logging.getLogger('sqlalchemy'),
    logging.getLogger('other_package'),
):
    logger.addHandler(default_handler)
    logger.addHandler(mail_handler)
```


## Werkzeug

O werkzeug regista informações básicas da requisição/resposta ao registador do `'werkzeug'`. Se o registador raiz não tiver manipuladores registados, o Werkzeug adiciona um **`StreamHandler`** para o seu registador.


## Extensões de Flask

Dependendo da situação, uma extensão pode escolher registar no **`app.logger`** ou em seu próprio registador nomeado. Consulte a documentação de cada extensão para obter informações mais detalhada.