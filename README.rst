Flask
=====

Flask é uma leve abstração de aplicação de Web de `WSGI`_. Ela está desenhada para tornar o começo rápido e fácil, com a habilidade de escalar para aplicações complexas. Ela começou como um simples capa em torno da `Werkzeug`_ e `Jinja`_ e tornou-se uma das mais populares abstrações de aplicações de Web da Python.

Flask oferece sugestões, mas não impõe quaisquer dependências ou disposição de projeto. Está sobre os programadores escolher as ferramentas e bibliotecas que quiserem usar. Existem muitas extensões fornecidas pela comunidade que facilitam a adição de nova funcionalidade.

.. _WSGI: https://wsgi.readthedocs.io/
.. _Werkzeug: https://werkzeug.palletsprojects.com/
.. _Jinja: https://jinja.palletsprojects.com/


Instalação
----------

Instalar e atualizar usando `pip`_:

.. code-block:: text

    $ pip install -U Flask

.. _pip: https://pip.pypa.io/en/stable/getting-started/


Um Exemplo Simples
----------------

.. code-block:: python

    # guardar isto como `app.py`
    from flask import Flask

    app = Flask(__name__)

    @app.route("/")
    def hello():
        return "Hello, World!"

.. code-block:: text

    $ flask run
      * Running on http://127.0.0.1:5000/ (Press CTRL+C to quit)


Contribuir
------------

Para orientação sobre configurar um ambiente de desenvolvimento e como fazer uma contribuição à Flask, consulte as `contributing guidelines`_.

.. _contributing guidelines: https://github.com/pallets/flask/blob/main/CONTRIBUTING.rst


Doar
------

A organização Pallets desenvolve e suporta a Flask e as bibliotecas que ela usa. No sentido aumentar a comunidade de colaboradores e utilizadores, e permitir os responsáveis de dedicar mais tempo aos projetos, `please donate today`_.

.. _please donate today: https://palletsprojects.com/donate


Ligações
-----

-   Documentação: https://flask.palletsprojects.com/
-   Mudanças: https://flask.palletsprojects.com/changes/
-   Lançamentos da PyPI: https://pypi.org/project/Flask/
-   Código-fonte: https://github.com/pallets/flask/
-   Monitor de Problema: https://github.com/pallets/flask/issues/
-   Conversas: https://discord.gg/pallets
