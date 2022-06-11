# Instalação

## Versão do Python

Nós recomendamos o uso da última versão do Python 3. O Flask suporta o Python 3.5 até as mais recentes, Python 2.7, e o PyPy.

## Dependências

Estas distribuições irão ser instaladas automaticamente quando estiver instalando o Flask.

- [Werkzeug](https://palletsprojects.com/p/werkzeug/) que implementa o WSGI, a interface padrão entre aplicações e servidores.
- [Jinja](https://palletsprojects.com/p/jinja/) é a linguagem de template que renderiza as páginas que sua aplicação serve.
- [MarkupSafe](https://palletsprojects.com/p/markupsafe/) vem com o Jinja. Ele escapa entradas duvidosas quando estiver renderizando os templates, para evitar ataques de injeção.
- [ItsDangerous](https://palletsprojects.com/p/itsdangerous/) seguramente assina os dados para garantir sua integridade. Isso é usado para proteger as sessões de cookie usados pelo Flask.
- [Click](https://palletsprojects.com/p/click/) é um framework para escrita de aplicações de linha de comando. Ele provê ao Flask ações de linha de comando e permite adicionar novos comandos personalizados.

#### Dependências Opcionais

Estas distribuições não serão automaticamente instaladas. O Flask irá detetar e usar eles se você instalar os mesmos.

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

Agora você pode voltar para a secção acima e [criar um ambiente](#criar-um-ambiente).
