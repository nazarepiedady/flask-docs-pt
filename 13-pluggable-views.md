# Apresentações Conectáveis

Relatório de Mudança: introduzida na versão 0.7

A versão 0.7 da Flask introduziu as apresentações conectáveis inspiradas pelas apresentações genéricas da Django as quais são baseadas nas classes no lugar de funções. A principal intenção é que você possa substituir partes das implementações e desta maneira ter apresentações conectáveis personalizáveis.


## Princípio Básico

Considere que tens uma função que carrega uma lista de objetos da base de dados e interpreta dentro de um modelo de marcação:

```py
@app.route('/users/')
def show_users(page):
    users = Users.query.all()
    return render_template('users.html', users=users)
```

Isto é simples e flexível, mas se quiseres fornecer esta apresentação de uma maneira genérica que possa também ser adaptada à outros modelos de base de dados e modelos de marcação você talvez queira mais flexibilidade. Isto é onde as apresentações baseadas em classes conectáveis tomam o lugar. Como primeiro passo para converter isto para uma apresentação baseada em classe você faria isto:

```py
from flask.views import View

class ShowUsers(View):

    def dispatch_request(self):
        users = Users.query.all()
        return render_template('users.html', objects=users)

app.add_url_rule('/users/', view_func=ShowUsers.as_view('show_users'))
```

Como podes ver o que tens que fazer é criar uma subclasse de [`flask.views.View`](#) e implementar o [`dispatch_request`](#). Depois temos que converter aquela classe para uma função de apresentação atual pelo método de classe [`as_view`](#). A sequência de caracteres que passares para aquela função é o nome do destino que apresentação depois terá. Porém isto por si mesmo não é útil, então vamos refazer um pouco o código:

```py
from flask.views import View

class ListView(View):
    
    def get_template_name(self):
        raise NotImplementedError()
    
    def render_template(self, context):
        return render_template(self.get_template_name(), **context)
    
    def dispatch_request(self):
        context = {'objects': self.get_objects()}
        return self.render_template(context)

class UserView(ListView):
    
    def get_template_name(self):
        return 'users.html'
    
    def get_objects(self):
        return User.query.all()
```

Isto de certeza não é tão útil para um exemplo tão pequeno, mas é bom o suficiente para explicar o princípio básico. Quando tiveres uma apresentação baseada em classe a questão surgi para o que `self` aponta. A maneira que isto funciona é que sempre que a requisição é despachada uma nova instância da classe é criada e o método [`dispatch_request()`](#) é chamado com os parâmetros da regra de URL. A classe em si mesma é instanciada com os parâmetros passados para a função [`as_view()`](#). Por exemplo você pode escrever uma classe tipo esta:

```py
class RenderTemplateView(View):
    def __init__(self, template_name):
        self.template_name = template_name
    def dispatch_request(self):
        return render_template(self.template_name)
```

E depois podes registá-la deste jeito:

```py
app.add_url_rule('/about', view_func=RenderTemplateView.as_view(
    'about_page', template_name='about.html'))
```

## Sugestões de Método

As apresentações conectáveis estão presas a aplicação como uma função regular com o uso tanto de [`route()`](#) ou de [`add_url_rule()`](#). Isto no entanto significa que terias que fornecer os nomes dos métodos HTTP que a apresentação suporta quando atribuires esta. No sentido de mover aquela informação para a classe você pode fornecer um atributo [`methods`](#) que tem esta informação:

```py
class MyView(View):
    methods = ['GET', 'POST']

    def dispatch_request(self):
        if request.method == 'POST':
            ...
        ...

app.add_url_rule('/myview', view_func=MyView.as_view('myview'))
```

## Comunicação Baseada em Método

Para APIs em RESTful é especialmente útil executar uma função diferente para cada método de HTTP. Com a [`flask.views.MethodView`](#) podes facilmente fazer isto. Cada método de HTTP aponta para um método da classe com o mesmo nome (só em minúsculas):

```py
from flask.views import MethodView

class UserAPI(MethodView):

    def get(self):
        users = User.query.all()
        ...
    
    def post(self):
        user = User.from_form_data(request.form)
        ...

app.add_url_rule('/users/', view_func=UserAPI.as_view('users'))
```

Desta maneira você também não precisa fornecer o atributo [`methods`](#). É automaticamente definido baseada nos métodos definidos na classe.