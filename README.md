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