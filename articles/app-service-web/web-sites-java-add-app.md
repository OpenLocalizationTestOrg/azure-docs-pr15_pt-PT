<properties 
    pageTitle="Adicionar uma aplicação Java Azure aplicação de serviço Web Apps" 
    description="Este tutorial mostra-lhe como adicionar uma página ou a aplicação para a instância do Azure aplicação serviço Web Apps que já esteja configurado para utilizar Java." 
    services="app-service\web" 
    documentationCenter="java" 
    authors="rmcmurray" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="app-service-web" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="Java" 
    ms.topic="article" 
    ms.date="08/11/2016" 
    ms.author="robmcm"/>

# <a name="add-a-java-application-to-azure-app-service-web-apps"></a>Adicionar uma aplicação Java Azure aplicação de serviço Web Apps

Assim que tiver inicializado a aplicação web do Java na [Aplicação de serviço de Azure][] conforme é documentado ao [criar uma aplicação web do Java na aplicação de serviço de Azure](web-sites-java-get-started.md), pode carregar a aplicação ao colocar o seu guerra na pasta **webapps** .

O caminho de navegação para a pasta **webapps** difere como configurar a sua instância do Web Apps.

- Se configurar a sua aplicação web utilizando o Azure Marketplace, o caminho para a pasta **webapps** é no formulário de **d:\home\site\wwwroot\bin\application\_server\webapps**, onde **aplicação\_server** é o nome do servidor da aplicação em vigor para a instância do Web Apps. 
- Se configurar a sua aplicação web utilizando a configuração do Azure IU, o caminho para a pasta **webapps** é no formulário **d:\home\site\wwwroot\webapps**. 

Tenha em atenção que pode utilizar o controlo de origem para carregar a aplicação ou páginas web, incluindo [cenários de integração contínua](app-service-continuous-deployment.md). FTP também é uma opção para carregar a aplicação ou páginas web.

Nota para Tomcat web apps: uma vez que carregou o seu ficheiro guerra para a pasta **webapps** , o servidor da aplicação Tomcat irá detectar que tenha adicionado-lo e irá automaticamente carregá-lo. Tenha em atenção que se copiar ficheiros (que não ficheiros guerra) para o diretório de raiz, o servidor da aplicação terá de ser reiniciado antes dos ficheiros são utilizados. A funcionalidade de autoload para as aplicações web Tomcat Java em execução no Azure baseia-se um novo ficheiro de guerra a ser adicionado, ou novos ficheiros ou directórios adicionados à pasta **webapps** . 

## <a name="next-steps"></a>Próximos passos

Para mais informações, consulte o [Centro de programadores do Java](/develop/java/).

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!-- External Links -->
[Azure de aplicação de serviço]: http://go.microsoft.com/fwlink/?LinkId=529714
