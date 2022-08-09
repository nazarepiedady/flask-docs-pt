# Modelos de Marcação de Hipertexto

O Flask usa o Jinja2 como seu gerador de modelos de marcação de hipertexto. Obviamente você está livre para usar um gerador de modelo de marcação de hipertexto diferente, mas você ainda terá de instalar o Jinja2 para executar o Flask. Este requisito é necessário para ativar ricas extensões. Uma extensão pode depender da presença do Jinja2.

Esta secção somente lhe proverá uma introdução rápida sobre como o Jinja2 é integrado ao Flask. Se você quiser informação sobre a sintaxe do gerador de modelo de marcação de hipertexto, consulte a [Documentação Oficial de Modelo de Marcação de Hipertexto do Jinja2](http://jinja.pocoo.org/docs/templates/) para obter mais detalhes.

## Configuração da Jinja2

A menos que seja modificada, o Jinja2 é configurado pelo Flask da seguinte maneira:

* autoescapamento está ativado para todos os modelos de marcação de hipertexto com as extensões, `.html`, `.htm`, `.xml` como também `.xhtml` quando estiver usando a função **`render_template()`**.

* autoescapamento está ativado para todas as strings quando estiver usando a função **`render_template_string()`**.

* um modelo de marcação de hipertexto tem a habilidade de ativar/desativar o autoescapamento com a tag `{% autoescape %}`.

* o Flask injeta algumas funções globais e auxiliadores dentro do contexto do Jinja2, além dos valores que estão presentes por padrão.

## Contexto Padrão

O variáveis globais a seguir estão disponíveis dentro dos modelos de marcação de hipertexto Jinja2 por padrão:

* **`config`**: O objeto da configuração atual (**`flask.config`**)
  * Relatório de mudança, mudança introduzida na versão 0.10: este agora está disponível, até dentro de modelos de marcação de hipertexto importados. Novo dentro da versão 0.6.

* **`request`**: o objeto da requisição atual (**`flask.request`**). Esta variável não estará disponível se o modelo de marcação de hipertexto for renderizado sem um contexto de requisição ativo.

* **`session`**: o objeto da secção atual (**`flask.session`**). Esta variável não estará disponível se o modelo de marcação de hipertexto for renderizado sem um contexto de requisição ativo.

* **`g`**: o objeto vinculado a requisição para variáveis globais (**`flask.g`**). Esta variável não estará disponível se o modelo de marcação de hipertexto for renderizado sem um contexto de requisição ativo.

* **`url_for()`**: a função **`flask.url_for()`**.

* **`get_flashed_messages()`**: a função **`flask.get_flashed_messages()`**.

> ## O Comportamento de Contexto do Jinja
> Estas variáveis são adicionadas ao contexto de variáveis, elas não são variáveis globais. A diferença é que por padrão estes não serão exibidos no contexto dos modelos de marcação de hipertexto importados. Isto é parcialmente causado por questões performance, parcialmente por manter as coisas explicitas.
>
> O que isto significa para você? Se você tem um macro que deseja importar, que precisa acessar o objeto da requisição, você tem duas possibilidades:
>
> 1. Você explicitamente passa a requisição para o macro como parâmetro, ou o atributo do objeto da requisição que você está interessado.
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

O autoescapamento é o conceito de automaticamente escapar caracteres especiais por você. Caracteres especiais no sentido de HTML (ou XML, e assim XHTML) são `&`, `>`, `<`, `"` como também `'`. Por esses caracteres carregarem significados especiais nos documentos por conta própria, você tem de substituir-los pelos chamadas "entidades" caso você quiser usá-los para compor o texto. Não fazer isso poderá causar não só frustrações ao usuário por não poder usar tais caracteres no texto, mas também pode conduzir a problemas de segurança. (Consulte [Cross-Site Scripting](#cross-site-scripting))

No entanto algumas vezes você precisará desativar o autoescapamento em modelos de marcação de hipertexto. Este pode ser o caso se você quiser explicitamente injetar código HTML dentro das páginas, por exemplo se eles vierem de um sistema que gera código HTML seguro tal como um conversor de código Markdown para HTML.


Existem três maneiras de alcançar isso:

* No código Python, envolva a string de código HTML dentro de um objeto Markup passa-lo para o modelo de marcação de hipertexto. Esta é em geral a maneira recomendada.
* Dentro do modelo de marcação de hipertexto, use o filtro `!safe` para explicitamente marcar uma string como código HTML seguro (`{{ myvariable|safe }}`)
* Desativar totalmente por tempo indeterminado o sistema de autoescapamento.

Para desativar o sistema de autoescapemento em modelos de marcação de hipertexto, você pode usar o bloco `{% autoescape %}`:

```jinja2
{% autoescape false %}
    <p>autoescaping is disabled here
    <p>{{ will_not_be_escaped }}
{% endautoescape %}
```

Sempre que fizer isso, por favor seja muito cauteloso em relação as variáveis que você estiver usando neste bloco.


## Registando Filtros

Se você quiser registar seus próprios filtros no Jinja2, você tem duas maneiras de fazer isso. Você pode tanto pôr eles dentro da variável **`jinja_env`** ou usar o decorador **`template_filter()`**.

Ambos os dois exemplos seguintes funcionam e ambos invertem um objeto:

```py
@app.template_filter('reverse')
def reverse_filter(s):
    return s[::-1]

def reverse_filter(s):
    return s[::-1]
app.jinja_env.filters['reverse'] = reverse_filter
```

No caso de decorador, o argumento é opcional se você quiser usar o nome da função como nome do filtro. Uma vez registada, você pode usar o filtro dentro do seu modelo de marcação de hipertexto da mesma maneira que se usa os filtros que vêm embutido dentro do Jinja2, por exemplo se você tem no contexto uma lista Python chamada *mylist*:

```jinja2
{% for x in mylist | reverse %}
{% endfor %}
```


## Processadores de Contexto

Existe dentro do Flask processadores de contexto, que lhe permitem injetar automaticamente novas variáveis dentro do contexto de um modelo de marcação de hipertexto. Processadores de contexto executam antes do modelo de marcação de hipertexto ser renderizado e possuem a habilidade de injetar novos valores dentro do contexto do modelo de marcação de hipertexto. Um processador de contexto é uma função que retorna um dicionário. As chaves e valores do dicionário são depois combinadas com o modelo de marcação de hipertexto, para todos os modelos de marcação de hipertexto na aplicação:

```py
@app.context_processor
def inject_user():
    return dict(user=g.user)
```

O processador de contexto descrito acima torna uma variável chamada *user* com o valor de *g.user* disponível dentro do modelo de marcação de hipertexto. Este exemplo não é de todo muito interessante visto que *g* já está disponível dentro do modelo de marcação de hipertexto de qualquer maneira, mas dá uma ideia de como a coisa funciona.

Variáveis não estão limitadas aos valores; um processador de contexto podem também tornar funções disponíveis para o modelo de marcação de hipertexto (desde que o Python permita passar funções):

```py
@app.context_processor
def utility_processor():
    def format_price(amount, currency='€'):
        return f'{amount:.2f}{currency}'
    return dict(format_price=format_price)
```

O processador de contexto acima torna a função *format_price* disponível em todos os modelos de marcação de hipertexto.