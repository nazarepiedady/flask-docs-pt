Instalação
============


Versão da Python
------------------

Nós recomendamos usar a versão mais recente da Python. A Flask suporta a Python 3.8 e as mais recentes.


Dependências
--------------

Estas distribuições serão instaladas automaticamente quando instalares a Flask.

* `Werkzeug`_ implementa a WSGI, a interface da Python padrão entre as aplicações e servidores.
* `Jinja`_ é uma linguagem de modelo de marcação que desenha as páginas que a tua aplicação serve.
* `MarkupSafe`_ vem com a Jinja. Ela escapa entradas duvidosas quando geramos os modelos de marcação para evitar ataques de injeção.
* `ItsDangerous`_ assina com segurança o dado para garantir sua integridade. Isto é usado para proteger o cookie da sessão da Flask.
* `Click`_ é uma abstração para escrita de aplicações de linha de comando. Ela fornece o comando ``flask`` e permite adicionar comandos de gestão personalizados.
* `Blinker`_ fornece suporte para os :doc:`signals`.

.. _Werkzeug: https://palletsprojects.com/p/werkzeug/
.. _Jinja: https://palletsprojects.com/p/jinja/
.. _MarkupSafe: https://palletsprojects.com/p/markupsafe/
.. _ItsDangerous: https://palletsprojects.com/p/itsdangerous/
.. _Click: https://palletsprojects.com/p/click/
.. _Blinker: https://blinker.readthedocs.io/


Dependências Opcionais
~~~~~~~~~~~~~~~~~~~~~~~~

Estas distribuições não serão instaladas automaticamente. A Flask detetará e as usará se as instalarmos.

* `python-dotenv`_ ativa suporte para a :ref:`dotenv` quando executamos os comandos da ``flask``.
* `Watchdog`_ fornece um recarregador mais rápido e mais eficiente para o servidor de desenvolvimento.

.. _python-dotenv: https://github.com/theskumar/python-dotenv#readme
.. _watchdog: https://pythonhosted.org/watchdog/


greenlet
~~~~~~~~~~~

Nós podemos escolher usar ``gevent`` ou ``eventlet`` com a nossa aplicação. Neste caso, ``greenlet>=1.0`` é obrigatório. Quando usamos PyPY, ``PyPy>=7.3.7`` é obrigatório.

Estas não sãos as versões mínimas suportadas, apenas indicam as primeiras versões que adicionaram funcionalidades necessárias. Nós deveríamos usar as versões mais recentes de cada.


Ambientes Virtuais
--------------------

Nós devemos sempre usar um ambiente virtual para gerir as dependências para o nosso projeto, tanto em desenvolvimento como em produção.

Qual problema um ambiente virtual soluciona? Quanto mais projetos de Python temos, mais provavelmente é que precisaremos de trabalhar com diferentes versões de bibliotecas de Python, ou mesmo a própria Python. As versões mais novas de bibliotecas para um projeto pode quebrar a compatibilidade em um outro projeto.

Os ambientes virtuais são grupos independentes de bibliotecas da Python, um para cada projeto. Os pacotes instalados para um projeto não afetarão outros projetos ou os pacotes do sistema operativo.

A Python vem empacotada com o módulo :mod:`venv` para criar ambientes virtuais:


.. _install-create-env:

Criar um Ambiente
~~~~~~~~~~~~~~~~~~~~~

Criar uma pasta de projeto e um pasta :file:`.venv` dentro dela:

.. tabs::

   .. group-tab:: macOS/Linux

      .. code-block:: text

         $ mkdir myproject
         $ cd myproject
         $ python3 -m venv .venv

   .. group-tab:: Windows

      .. code-block:: text

         > mkdir myproject
         > cd myproject
         > py -3 -m venv .venv


.. _install-activate-env:

Ativar o ambiente
~~~~~~~~~~~~~~~~~~~~~~~~

Antes de trabalharmos no nosso projeto, vamos ativar o ambiente correspondente:

.. tabs::

   .. group-tab:: macOS/Linux

      .. code-block:: text

         $ . .venv/bin/activate

   .. group-tab:: Windows

      .. code-block:: text

         > .venv\Scripts\activate

O nosso pronto da shell mudará para mostrar o nome do ambiente ativado.


Instalação da Flask
----------------------

Dentro do ambiente ativado, usamos o seguinte comando para instalar a Flask:

.. code-block:: sh

    $ pip install Flask

A Flask agora está instalada. Consulte a :doc:`/quickstart` ou siga para a :doc:`Perspetiva geral da documentação </index>`.
