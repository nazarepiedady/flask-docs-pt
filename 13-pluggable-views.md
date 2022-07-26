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