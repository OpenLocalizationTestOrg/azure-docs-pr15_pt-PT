<properties 
    pageTitle="Como criar uma aplicação Web com a aplicação de serviço no Linux | Microsoft Azure" 
    description="Web app criação fluxo de trabalho para o serviço de aplicação no Linux." 
    keywords="Azure aplicação de serviço, do web app, linux, oss"
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

# <a name="create-a-web-app-with-app-service-on-linux"></a>Criar uma aplicação Web com a aplicação de serviço no Linux

## <a name="using-the-management-portal-to-create-your-web-app"></a>Utilizando o Portal de gestão para criar a sua aplicação web
Pode começar a criar a aplicação Web no Linux a partir do [portal de gestão](https://portal.azure.com) , conforme mostrado na imagem abaixo.

![][1]

Depois de selecionar a opção abaixo, pode serão apresentados a pá criar conforme mostrado na imagem abaixo. 

![][2]

-   Dê um nome ao seu do web app.
-   Selecione um grupo de recursos existente ou crie um novo. (Consulte regiões disponíveis na [secção limitações](./app-service-linux-intro.md)).
-   Selecione um plano de serviço de aplicação existente ou crie um novo um (consulte o artigo aplicação serviço plano notas na [secção limitações](./app-service-linux-intro.md)). 
-   Selecione a pilha de aplicação que pretende utilizar. Irá obter escolher entre várias versões do Node.js e PHP. 

Assim que tiver a aplicação que criou, pode alterar a pilha de aplicação a partir das definições da aplicação conforme mostrado na imagem abaixo.

![][3]

## <a name="deploying-your-web-app"></a>Implementar a aplicação web

Escolher "Opções de implementação" a partir do portal de gestão fornece-lhe a opção para utilizar um repositório de Git ou um repositório de GitHub o local para implementar a aplicação. As instruções, posteriormente, são da mesma forma para uma aplicação web do não Linux e pode seguir as instruções no nosso [local Git implementação](./app-service-deploy-local-git.md) ou nosso artigo de [implementação contínua](./app-service-continuous-deployment.md) para GitHub.

Também pode utilizar FTP para carregar a sua aplicação para o seu site. Pode obter o ponto final de FTP para a sua aplicação web a partir da secção de registos de diagnóstico, conforme mostrado na imagem abaixo.

![][4]


## <a name="next-steps"></a>Próximos passos ##

* [O que é o serviço de aplicação no Linux?](./app-service-linux-intro.md)
* [Utilizar PM2 configuração para Node.js no Web Apps no Linux](./app-service-linux-using-nodejs-pm2.md)

<!--Image references-->
[1]: ./media/app-service-linux-how-to-create-a-web-app/top-level-create.png
[2]: ./media/app-service-linux-how-to-create-a-web-app/create-blade.png
[3]: ./media/app-service-linux-how-to-create-a-web-app/application-settings-change-stack.png
[4]: ./media/app-service-linux-how-to-create-a-web-app/diagnostic-logs-ftp.png
