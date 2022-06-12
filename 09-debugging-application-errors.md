# Depurando Erros de Aplicação


## Em Produção

**Não execute o servidor de desenvolvimento, ou ative o depurador embutido, em um ambiente de produção.** O depurador permite executar código Python de maneira arbitrária a partir do browser. É protegido por um fixador, mas que não deve ser confiado por questões de segurança.

Use uma ferramenta registador de erro, tal como Sentry, como descrito em [Ferramentas de Registo de Erros](), ou ative o registo e notificações como descrito em [Registando (Logging)]().

Se você tiver acesso ao servidor, você poderia adicionar algum código capaz de iniciar um depurador externo se `request.remote_addr` corresponder ao seu IP. Algumas depuradores de IDEs também tem um modo remoto assim é possível interagir localmente com os pontos de paradas do servidor. Apenas ative temporariamente o depurador.


## O Depurador Embutido

O servidor de desenvolvimento embutido do Werzeug oferece um depurador que exibe rastreador interativo dentro do browser sempre que um erro não manipulado ocorrer durante uma requisição. Este depurador deve ser apenas usado durante o desenvolvimento.

![imagem do rastreador no browser exibindo erro de tipagem](./static_files/debugger.png)

---


### Aviso:

O depurador permite executar arbitrariamente código Python a partir do browser. É protegido por um fixador, mas continua a representar a maioria dos riscos de segurança. Não execute o servidor de desenvolvimento ou depurador em ambiente de produção.

---

Para ativar o depurador, execute o servidor de desenvolvimento com a variável de ambiente `FLASK_ENV` definida para `development`. Isto poem o Flask em modo de depuração, que muda a maneira de como ele manipula certos erros, e ativa o depurador junto com o recarregador.

Bash
```sh
$ export FLASK_ENV=development
$ flask run
```

CMD
```cmd
> set FLASK_ENV=development
> flask run
```

Powershell
```ps
> $env:FLASK_ENV = "development"
> flask run
```

`FLASK_ENV` apenas pode ser definido como uma variável de ambiente. Sempre que estiver executando a partir do código Python, passar `debug=True` ativa o modo de depuração, que é na maioria dos casos equivalente. O modo de depuração pode ser também controlado separadamente do `FLASK_ENV` com a variável de ambiente `FLASK_DEBUG`.

```py
app.run(debug=True)
```

O [Servidor de Desenvolvimento]() e a [Interface de Linha de Comando]() possuem mais informação sobre como executar o depurador, modo de depuração, e modo de desenvolvimento. Informações mais detalhadas sobre o depurador pode ser achada na documentação do [Werkzeug](https://werkzeug.palletsprojects.com/debug/).


## Depuradores Externos

Depuradores externos, tais como aqueles oferecidos pelas IDEs, podem oferecer uma experiência de depuração mais rica do que os depuradores embutidos. Eles também podem ser usados para caminhar pelo código durante uma requisição antes de um erro ser levantado, ou se nenhum erro for de todo acionado. Alguns até têm um modo remoto assim você pode depurar o código enquanto estiver ainda executando, isso a partir de uma outra máquina.

Sempre que estiver usando um depurador externo, a aplicação deve estar no modo de depuração, mas pode ser útil desativar o depurador e recarregador embutido, em caso de interferência.

Quando estiver executando a partir da linha de comando:

Bash
```sh
$ export FLASK_ENV=development
$ flask run --no-debugger --no-reload
```

CMD
```cmd
> set FLASK_ENV=development
> flask run --no-debugger --no-reload
```

Powershell
```ps
> $env:FLASK_ENV = "development"
> flask run --no-debugger --no-reload
```

Quando estiver executando a partir do código Python:

```py
app.run(debug=True, use_debugger=False, use_reloader=False)
```

Desativação destes não é obrigatória, um depurador externo continuará a funcionar com os seguintes avisos. Se o depurador embutido não estiver desativado, ele capturará exceções não manipuladas antes do depurador externo poder faze-lo. Se o recarregador não estiver desativado, ele pode causar um recarregamento inesperado se o código mudar durante a depuração.