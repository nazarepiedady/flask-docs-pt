# Transmissores (Signals)

* Relatório de mudança: Novo desde a versão 0.6

Desde a versão 0.6 do Flask, existe o suporte integrado para transmissões em Flask. Este suporte é fornecido pela excelente biblioteca [blinker](https://pypi.org/project/blinker/) e retrocederá graciosamente se não estiver disponível.

O que são transmissões? Transmissores ajudam você a dissociar aplicações ao enviar notificações quando ações ocorrem em algum lugar no núcleo da abstração ou uma outra extensão da Flask. Resumindo, transmissões permitem certos emissores para notificar aos subscritores de que alguma coisa aconteceu.

A Flask vem com alguns transmissores e outras extensões podem fornecer mais. Também tenha em mente que os transmissores destinam-se a notificar os subscritores e não deveria encorajar subscritores a modificar os dados. Você reparará que existem transmissores que parecem fazer a mesma coisa tal qual alguns dos decoradores embutidos fazem (exemplo: [**`request_started`**]() é muito similar ao [**`before_request()`**]()). No entanto, existem diferenças em como elas funcionam. O manipulador [**`before_request()`**]() do núcleo, por exemplo, é executado em uma ordem específica e é capaz de abortar a requisição com antecedência ao retornar uma resposta. Em contraste todos manipuladores de transmissão são executado em uma ordem indefinida e não modificam nenhum dado.

A grande vantagem dos transmissores sobre os manipuladores é que você pode seguramente subscrever-se nelas apenas por uma fração de segundo. Estas subscrições temporárias são úteis para testes unitários por exemplo. Digamos que você deseja saber qual modelo de marcação foi renderizado como parte de uma requisição: transmissores permitem-te fazer exatamente isso.

## Subscrevendo-se aos Transmissores

Para subscrever-se a uma transmissão, você pode usar o método [**`connect()`**](https://pythonhosted.org/blinker/index.html#blinker.base.Signal.connect) de uma transmissão. O primeiro argumento é a função que deve ser chamada quando a transmissão é emitida, o segundo valor opcional especifica um emissor. Para cancelar a subscrição de uma transmissão, você pode usar o método [**`disconnect()`**](https://pythonhosted.org/blinker/index.html#blinker.base.Signal.disconnect).

Para todos os transmissores do núcleo da Flask, o emissor é a aplicação que emitiu a transmissão. Quando você subscrever-se à uma transmissão, certifique-se de também fornecer um emissor a menos que você na realidade deseje ouvir as transmissões em toda aplicação. Isto é especialmente verdadeiro se você estiver desenvolvendo uma extensão.

Por exemplo, aqui está um auxiliar de gestor de contexto que pode ser usado em um teste unitário para determinar quais modelos de marcação foram renderizados e quais variáveis foram passadas ao modelo de marcação:

```py
from flask import template_rendered
from contextlib import contextmanager

@contextmanager
def captured_templates(app):
    recorded = []
    def record(sender, template, context, **extra):
        recorded.append((template, context))
    template_rendered.connect(record, app)
    try:
        yield recorded
    finally:
        template_rendered.disconnect(record, app)
```

Isto pode agora ser facilmente combinado com um cliente de teste:

```py
with captured_templates(app) as templates:
    rv = app.test_client().get('/')
    assert rv.status_code == 200
    assert len(templates) == 1
    template, context = templates[0]
    assert template.name == 'index.html'
    assert len(context['items']) == 10
```

Certifique-se de se subscrever com um argumento extra `**extra`, de modo que as suas chamadas não falhem se a Flask introduzir novos argumentos aos transmissores.

Toda renderização de modelo de marcação no código emitida por uma aplicação *app* no corpo do bloco `with` será agora gravada em uma variável de modelos de marcação (*templates*). Sempre que um modelo de marcação é renderizado, o objeto do modelo de marcação bem como o contexto são anexados a ele.

Além isso existe um método auxiliar conveniente ([**`connected_to()`**](https://pythonhosted.org/blinker/index.html#blinker.base.Signal.connected_to)) que permite-te temporariamente subscrever uma função a uma transmissão com um gestor de contexto por conta própria. Como o retorno de valor do gestor de contexto não pode ser especificado desta maneira, você tem que passar a lista como um argumento:

```py
from flask import template_rendered

def captured_templates(app, recorded, **extra):
    def record(sender, template, context):
        recorded.append((template, context))
    return template_rendered.connected_to(record, app)
```

O exemplo acima pareceria-se então com algo neste sentido:

```py
templates = []
with captured_templates(app, templates, **extra):
    ...
    template, context = templates[0]
```

---

## Mudanças na API do Blinker:

O método [**`connected_to()`**](https://pythonhosted.org/blinker/index.html#blinker.base.Signal.connected_to) chegou ao Blinker na sua versão 1.1.

---

## Criando Transmissões

Se você quiser usar transmissões em sua própria aplicação, você pode usar diretamente a biblioteca **blinker**. O casos de uso mais comuns são transmissões em um [**`Namespace()`**](https://pythonhosted.org/blinker/index.html#blinker.base.Namespace) personalizado. Isto é o que é recomendado na maior parte das vezes:

```py
from blinker import Namespace
my_signals = Namespace()
```

Agora você pode criar novas transmissões deste jeito:

```py
model_saved = my_signals.signal('model-saved')
```

O nome para a transmissão aqui faz dela única e também simplifica a depuração. Você pode acessar o nome da transmissão com o atributo [**`name`**](https://pythonhosted.org/blinker/index.html#blinker.base.NamedSignal.name).

---

## Para Desenvolvedores de Extensões:

Se você estiver escrevendo uma extensão de Flask e você deseja degradação graciosa para instalações de blinker em falta, você pode fazer isso pelo uso da classe [**`flask.signals.Namespace`**]().

---

## Enviando Transmissões

Se você quiser emitir um sinal, você pode fazer isso chamando o método [**`send()`**](https://pythonhosted.org/blinker/index.html#blinker.base.Signal.send). Ele aceita um emissor como primeiro argumento e opcionalmente algumas palavras-chaves que são avençadas para os registadores de sinal:

```py
class Model(object):
    ...

    def save(self):
        model_saved.send(self)
```

Tente pegar sempre um bom emissor. Se você tiver uma classe que está emitindo um sinal, passe o `self` como emissor. Se você estiver emitindo um sinal a partir de uma função aleatória, você pode passar o `current_app._get_current_object()` como emissor.

---

### Passando Proxies como Emissores:

Nunca passe o **`current_app`** como emissor para um sinal. Ao invés disso use `current_app._get_current_object()`. A razão para isso é que **`current_app`** é uma proxy e não um objeto real da aplicação.

---

## Sinais e o Contexto da Requisição da Flask

Os sinais suportam completamente [O Contexto da Requisição]() quando estiver recebendo as sinais. As variáveis de contexto local estão constantemente disponíveis entre o **`request_started`** e o **`request_finished`**, assim você pode depender do **`flask.g`** e outros quando necessário. Note as limitações descritas em [Enviando Sinais]() e o sinal **`request_tearing_down`**.

## Decorador Baseado em Subscrições de Sinal

Com o Blinker 1.1 você também pode facilmente subscrever-se aos sinais usando o novo decorador **`connect_via()`**:

```py
from flask import template_rendered

@template_rendered.connect_via(app)
def when_template_rendered(sender, template, context, **extra):
    print(f'Template {template.name} is rendered with {context}')
```

## Sinais Principais

Dê uma olhada em [Sinais]() para uma lista de todos sinais embutidos.
