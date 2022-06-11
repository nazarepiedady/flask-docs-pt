# Thread-Locals dentro do Flask

Uma das decisões de design tomadas no Flask foi que a tarefa que é simples deveria ser simples; elas não deveria conter muitos códigos e nem devem limitar você. Por esta razão, o Flask toma poucas decisões de design o que levaria algumas pessoas a considera ele pouco ortodoxo ou mesmo surpreendente. Por exemplo, o Flask usa objetos thread-local internamente então você não precisa passar objetos de função em função dentro de uma requisição para se manter a salvo de thread. Esta melhoria é conveniente, mas requer um contexto de requisição valido para injeção de dependência ou quando tentar re-usar o código que usa um valor indexado para a requisição. O projeto Flask é honesto quando se trata de thread-locals, não esconde eles, e chama-os dentro do código e documentação, onde eles são usados.


# Desenvolve para Web com Cautela

Sempre mantenha a segurança em mente quando for construir aplicações web.

Se você escrever uma aplicação web, você está provavelmente permitindo que usuários se registem e deixem seus dados no seu servidor. Os usuários estão confiando em você seus dados. E mesmo se você for o único usuário que deixará seus dados em sua aplicação, você continuaria a querer que seus dados fossem armazenados com segurança.

Infelizmente, existem muitas maneiras de comprometer a segurança de uma aplicação web. O Flask proteje você contra um dos problemas de segurança mais comuns em aplicações web modernas: cross-site scripting (XSS). A menos que você deliberadamente marque código HTML inseguro como seguro, Flask e o gerador de modelo de marcação Jinja2 debaixo dos panos tem você protegido. Porém existem muitas mais maneiras de causar problemas de segurança.

A documentação irá avisar a você sobre os aspetos do desenvolvimento web que requerem segurança. Algumas dessas questões de segurança são muito mais complexas do que se possa pensar, e todos nós algumas vezes subestimamos a probabilidade de que uma vulnerabilidade seja explorada - até que um espertinho descobrir uma maneira de explorar nossa aplicação. E nem adianta pensar que sua aplicação não é importante o suficiente para atrair um espertinho. Dependendo do tipo de ataque as chances são que robós automatizados estejam procurando maneiras de preencher seu banco de dados com spam, links para software maliciosos ou algo do tipo.

O Flask não é diferente de nenhum outro framework em que você o desenvolvedor deve construir com cuidado, estando atento a vulnerabilidades exploráveis quando estiver construindo de acordo com os seus requisitos.