# Prefácio

Considere ler as informações antes de começar a usar o Flask. Estas informações buscam de maneira simpática responder algumas questões sobre o propósito e objetivos do projeto, e sobre quando você deve ou não usar ele.


## O que "micro" quer realmente dizer?

Com "micro" não se quer dizer que toda sua aplicação comportará apenas um único arquivo Python (algo que não é impossível), muito menos significa que o Flask carece de funcionalidades. O "micro" em micro-abstração significa que a Flask pretende manter o core da aplicação simples, contudo extensível. O Flask não irá tomar as decisões por você, decisões do tipo, qual banco de dados usar. Aquelas decisões que ele faz, tal como qual motor de interpretação de modelo de marcação (templating engine) usar, podem ser facilmente alteradas. Todo o resto é deixado por tua conta, de maneira que Flask pode ser tudo que você precisa e nada daquilo que você não precisa.

Por padrão, Flask não possui uma camada de abstração para banco de dados, validação de formulário ou qualquer coisa onde já existe diferentes bibliotecas capazes de lidar com essas responsabilidades. Em vez disso, Flask suporta extensões para adicionar tais funcionalidades a sua aplicação como se estás por si mesmas tivessem sido implementadas em Flask. Numerosas extensões provê integração com banco de dados, validação de formulário, upload de arquivos, varias tecnologias de autenticação aberta e muito mais. Flask pode até ser "micro", mas ele está pronto para uso em produção em uma variedade de necessidades.


## Configuração e Convenções

Flask possui vários valores de configuração, com padrões sensíveis, e algumas convenções ao se dar inicio. Por convenção, os templates (\*.html, \*.jinja2, etc) e arquivos estáticos (\*.jpg, \*.png, \*.svg, \*.css, \*.js, etc) são armazenados em subdiretórios dentro da árvore da aplicação Python, com os respetivos nomes `templates` e `static`. Embora essa ordem possa ser mudada, você normalmente não precisa o fazer, especialmente quanto está dando seus primeiros passos na ferramenta.


## Crescendo com Flask

Uma vez tendo o Flask instalado e funcionando, você irá achar uma variedade de extensões disponíveis dentro da comunidade, para integrar em seus projetos em produção. Os mantenedores do Flask revisam as extensões desenvolvidas e garantes que as extensões aprovadas irão quebrar com versões futuras do Flask.

A medida que sua base de código cresce, você está livre para fazer as decisões de design que achar mais apropriado para o seu projeto. Flask irá continuar a prover uma camada mais simples a fim de tirar proveito do melhor que o Python tem a oferecer. Você pode implementar padrões mais avançados em SQLAlchemy ou em outra ferramenta de banco de dados, introduzir persistência de dados não-relacional se for o mais apropriado, e tirar vantagem de framework agnósticos construidos para trabalhar com o WSGI, a interface do Python para a web.

O Flask possui vários ciclos (hooks) para personalizar seu comportamentos. É possível que você precise de mais personalizações extras, A classe do Flask está pronta para ser herdada por subclasses. Se você está interessado isso, consulte o assunto no capitulo, [Tornando-se Grande](#tornando-se-grande). Se você tem curiosidade em saber mais sobre os princípios de design do Flask, siga para a secção sobre [Decisões de Design dentro Flask](#decisões-de-design-no-flask).

Siga para [Instalação](#instalação), o [Começo Rápido](#começo-rápido), ou o [Prefácio para Programadores Experientes](#prefácio-para-programadores-experientes).