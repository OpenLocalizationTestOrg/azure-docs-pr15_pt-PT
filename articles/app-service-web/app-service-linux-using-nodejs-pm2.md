<properties 
    pageTitle="Utilizar PM2 configuração para NodeJS no Web Apps no Linux | Microsoft Azure" 
    description="Utilizar PM2 configuração para NodeJS no Web Apps no Linux" 
    keywords="Azure aplicação de serviço, do web app, nodejs, pm2, linux, oss"
    services="app-service" 
    documentationCenter="" 
    authors="naziml" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/10/2016" 
    ms.author="naziml"/>

# <a name="using-pm2-configuration-for-nodejs-in-web-apps-on-linux"></a>Utilizar PM2 configuração para Node.js no Web Apps no Linux

Se definiu na pilha de aplicação para Node.js Web Apps no Linux, obterá a opção de configurar um ficheiro de arranque Node.js conforme mostrado na imagem abaixo.

![][1]

Pode utilizar esta opção para quer

-   Especifique o script de arranque para a sua aplicação Node.js (por exemplo: /bin/server.js)
-   Especificar o ficheiro de configuração de PM2 a utilizar para a sua aplicação Node.js (por exemplo: /foo/process.json)

 >[AZURE.NOTE] Se pretender que os processos de nó para reiniciar automaticamente quando determinados ficheiros forem modificados, terá de utilizar a configuração PM2. Caso contrário, a aplicação não irá reiniciar quando recebe as notificações de alteração de coisas como implementação contínua, quando altera o código da aplicação.

Pode verificar a [documentação de ficheiro do processo](http://pm2.keymetrics.io/docs/usage/application-declaration/) de Node.js para todas as opções, mas abaixo encontra-se um exemplo do que utilizaria como o seu ficheiro process.json

        {
          "name"        : "worker",
          "script"      : "/bin/server.js",
          "instances"   : 1,
          "merge_logs"  : true,
          "log_date_format" : "YYYY-MM-DD HH:mm Z",
          "watch": ["/bin/server.js", "foo.txt"],
          "watch_options": {
            "followSymlinks": true,
            "usePolling"   : true,
            "interval"    : 5
          }
        }

Factos importantes a nota nesta configuração são 

-   A propriedade "script" Especifica script de iniciar a aplicação.
-   A propriedade "instâncias" Especifica quantas instâncias do processo de nó à iniciação. Se estiver a utilizar a aplicação no tamanhos VM maiores que têm múltiplas tarolos, que pretende maximizar os recursos ao definir um valor maior aqui.
-   A matriz "ver" Especifica todos os ficheiros para cujas alterações pretende reiniciar os processos de nó.
-   Para "watch_options" atualmente tem de especificar "usePolling" como verdadeiro devido a forma como o seu conteúdo de aplicação é instalado.


## <a name="next-steps"></a>Próximos passos ##

* [O que é o serviço de aplicação no Linux?](./app-service-linux-intro.md)

<!--Image references-->
[1]: ./media/app-service-linux-using-nodejs-pm2/nodejs-startup-file.png