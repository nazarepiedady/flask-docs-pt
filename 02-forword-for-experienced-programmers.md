# Fio condutor local dentro da Flask

Uma das decisões de desenho tomadas na Flask foi que aquelas tarefas que são simples deveria ser simples; elas não deveria exigir muito código e ainda assim nem deveriam limitar você. Por esta razão, a Flask tem poucas decisões de desenho que algumas pessoas podem surpreender-se ou achar nada ortodoxas. Por exemplo, o Flask usa objetos de fio condutor local internamente, assim você não precisa passar objetos de função em função dentro de uma requisição para manter-se em linha segura. Esta abordagem é conveniente, mas precisa de um contexto de requisição válido para injeção de dependência ou para quando estiver tentando reutilizar código que utiliza um valor indexado para a requisição. O projeto da Flask é honesto quando se trata de fio condutor local, não esconde eles, e chama-os dentro do código e documentação, onde eles são utilizados.


# Programa para Web com Cautela

Sempre mantenha a segurança em mente quando for construir aplicações web.

Se você escrever uma aplicação web, você está provavelmente permitindo que usuários se registem e deixem seus dados no seu servidor. Os usuários estão confiando em você seus dados. E mesmo se você for o único usuário que deixará seus dados em sua aplicação, você continuaria a querer que seus dados fossem armazenados com segurança.

Infelizmente, existem muitas maneiras de comprometer a segurança de uma aplicação web. O Flask protege você contra um dos problemas de segurança mais comuns em aplicações web modernas: cross-site scripting (XSS). A menos que você deliberadamente marque código HTML inseguro como seguro, Flask e o gerador de modelo de marcação Jinja2 debaixo dos panos tem você protegido. Porém existem muitas mais maneiras de causar problemas de segurança.

A documentação irá avisar a você sobre os aspetos da programação web que requerem segurança. Algumas dessas questões de segurança são muito mais complexas do que se possa pensar, e todos nós algumas vezes subestimamos a probabilidade de que uma vulnerabilidade seja explorada - até que um espertinho descobrir uma maneira de explorar nossa aplicação. E nem adianta pensar que sua aplicação não é importante o suficiente para atrair um espertinho. Dependendo do tipo de ataque as chances são que robôs automatizados estejam procurando maneiras de preencher seu banco de dados com spam, ligações para software maliciosos ou algo do tipo.

A Flask não é diferente de nenhuma outra abstração em que você programador deve construir com cuidado, estando atento a vulnerabilidades exploráveis quando estiver construindo de acordo com os seus requisitos.