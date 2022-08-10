# Documentação da Flask

> desenvolvimento web, em um passo de cada vez

Bem-vindo a documentação da Flask. Começa com a [Instalação](./03-installation.md) e a seguir adquira uma perspetiva geral com a [Introdução Rápida](./04-quickstart.md). Também há uma [Lição](./05-tutorial.md) mais detalhada que mostra como criar uma pequena mas completa aplicação com a Flask. Os padrões comuns estão descritos na secção [Padrões para a Flask](). O resto da documentação descreve cada componente da Flask em detalhes, com uma referência completa na secção [API]().

A Flask depende do gerador de modelos de marcação [Jinja](https://www.palletsprojects.com/p/jinja/) e o jogo de ferramentas de WSGI [Werkzeug](https://www.palletsprojects.com/p/werkzeug/). As documentação destas bibliotecas podem ser encontradas em:

* [Documentação da Jinja](https://jinja.palletsprojects.com/)
* [Documentação da Werkzeug](https://werkzeug.palletsprojects.com/)

## Guia de Utilizador

Esta parte da documentação, a qual é na sua maioria prosa, começa com algumas informações de fundo sobre a Flask, depois foca-se nas instruções passo a passo para o desenvolvimento web com a Flask.

* [Prefácio](01-foreword.md)
    * [O que "micro" significa?](01-foreword.md#o-que-\"micro\"-quer-realmente-dizer)
    * [Configuração e Convenções](01-foreword.md#configuração-e-convenções)
    * [Crescendo com a Flask](01-foreword.md#crescendo-com-flask)
* [Prefácio para Programadores Experientes](02-forword-for-experienced-programmers.md)
    * [Fio Condutor Local dentro da Flask](02-forword-for-experienced-programmers.md#fio-condutor-local-dentro-da-flask)
    * [Programe para Web com Cautela](02-forword-for-experienced-programmers.md#programa-para-web-com-cautela)
* [Instalação](03-installation.md)
    * [Versão da Python](03-installation.md#versão-da-python)
    * [Dependências](03-installation.md#dependências)
    * [Ambientes Virtuais](03-installation.md#ambiente-virtual)
    * [Instalar a Flask](03-installation.md#instalar-a-flask)
* [Introdução Rápida](04-quickstart.md)
    * [Uma Aplicação Minima](04-quickstart.md#uma-aplicação-minima)
    * [O Que Fazer Se O Servidor Não Iniciar](04-quickstart.md#o-que-fazer-caso-o-servidor-não-iniciar)
    * [Mode de Depuração](04-quickstart.md#modo-de-depuração)
    * [Tratando o HTML](04-quickstart.md#tratando-o-html)
    * [Roteamento](04-quickstart.md#roteamento)
    * [Ficheiros Estáticos](04-quickstart.md#ficheiros-estáticos)
    * [Interpretando os Modelos de Marcação](04-quickstart.md#interpretando-os-modelos-de-marcação)
    * [Acessando os Dados da Requisição](04-quickstart.md#acessando-os-dados-da-requisição)
    * [Redirecionamentos e Erros](04-quickstart.md#redirecionamentos-e-erros)
    * [Sobre Respostas](04-quickstart.md#sobre-respostas)
    * [Sessões](04-quickstart.md#sessões)
    * [Mensagem de Alerta](04-quickstart.md#mensagem-de-alerta)
    * [Registo](04-quickstart.md#registo)
    + [Intercetando no Intermediário de WSGI](04-quickstart.md#intercetando-no-intermediário-de-wsgi)
    * [Utilizando Extensões de Flask](04-quickstart.md#utilizando-extensões-de-flask)
    * [Instalando em um Servidor Web](04-quickstart.md#instalando-em-um-servidor-web)
* [Lição](05-tutorial.md)
    * [Estrutura de Projeto](05-tutorial.md#estrutura-de-projeto)
    * [Configuração da Aplicação](05-tutorial.md#configuração-da-aplicação)
    * [Definir e Acessar a Base de Dado](05-tutorial.md#definir-e-acessar-o-banco-de-dados)
    * [Estruturas e Apresentações](05-tutorial.md#estruturas-e-apresentações)
    * [Modelos de Marcação de Hipertexto](05-tutorial.md#modelos-de-marcação)
    * [Ficheiros Estáticos](05-tutorial.md#ficheiros-estáticos)
    * [Estrutura do Blogue](05-tutorial.md#estrutura-de-blogue)
    * [Tornar o Projeto Instalável](05-tutorial.md#tornar-o-projeto-instalável)
    * [Cobertura de Teste](05-tutorial.md#cobertura-de-teste)
    * [Executar no Servidor em Produção](05-tutorial.md#executar-no-servidor-em-produção)
* [Modelos de Marcação de Hipertexto](06-templates.md)
    * [Configuração da Jinja2](06-templates.md#configuração-do-jinja2)
    * [Contexto Padrão](06-templates.md#contexto-padrão)
    * [Controlando o Autoescapamento](06-templates.md#controlando-autoescapamento)
    * [Registando Filtros](06-templates.md#registando-filtros)
    * [Processadores de Contexto](06-templates.md#processadores-de-contexto)
* [Testando Aplicações em Flask](07-testing-flask-applications.md)