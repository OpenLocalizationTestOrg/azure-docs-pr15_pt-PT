<properties 
    pageTitle="Introdução à aplicação de serviço no Linux | Microsoft Azure" 
    description="Saiba mais sobre o serviço de aplicação no Linux." 
    keywords="Azure aplicação de serviço, linux, oss"
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

# <a name="introduction-to-app-service-on-linux"></a>Introdução à aplicação de serviço no Linux
Serviço de aplicação no Linux é atualmente na pré-visualização público e suporte nativo aplicações web em execução no Linux. 

## <a name="overview"></a>Descrição geral ##
Clientes podem utilizar o serviço de aplicação no Linux às aplicações web do anfitrião vierem no Linux para pilhas de aplicação suportada. A secção de funcionalidades seguinte lista as pilhas de aplicação atualmente suportados.

## <a name="features"></a>Funcionalidades ##
Serviço de aplicação no Linux atualmente suporta as seguintes pilhas de aplicação

- NODE.js
- PHP

Os clientes podem implementar os respetivos aplicações utilizando

- FTP.
- Local Git.
- GitHub ou BitBucket.

Para a aplicação de dimensionamento


- Clientes podem dimensionar os respetivos do web app para cima e para baixo ao alterar a camada no seu plano de serviço de aplicação. 
- Clientes podem Dimensionar terminar as suas aplicações saída e executar a sua aplicação em várias instâncias de vários dentro dos limites da sua SKU.

Para Kudu algumas das funcionalidades básicas irão funcionar

- Ambiente.
- Implementações.
- Consola de básica.

## <a name="limitations"></a>Limitações ##

O portal de gestão Azure só irá mostrar funcionalidades atualmente suportadas para o serviço de aplicação no Linux e esconder os restantes. Como ativar mais funcionalidades a nossa equipa podemos irá manter refletir isto no portal de gestão. Algumas funcionalidades, como a integração de VNET e AAD / autenticação de terceiros ou extensões de site Kudu actualmente não funciona. Mas recebemos estes trabalhar podemos será atualizado nossa documentação e blogue sobre as alterações.

Esta pré-visualização do público atualmente só está disponível nas seguintes regiões

-   US Ocidental.
-   Europa Ocidental.
-   Sudeste asiático.

Web app no Linux só é suportado no dedicado planos do serviço da aplicação e não tem uma camada Free ou partilhado. Além disso, planos de serviço de aplicação para normal e Linux web apps são mutuamente exclusivos, para que não é possível criar uma aplicação web do Linux num plano de serviço de aplicação que não sejam Linux.

Web app num Linux têm de ser criado num grupo de recursos que não contenha não Linux web apps da mesma região.

Devido à falta de uma reciclagem sobreposta das aplicações web, clientes devem esperar que um tempo de inatividade pequenas em caso de uma aplicação web tem reiniciado. 

## <a name="next-steps"></a>Próximos passos ##

Siga as ligações seguintes para começar a utilizar o serviço de aplicação no Linux. Publique perguntas e preocupações no [nosso fórum](https://social.msdn.microsoft.com/forums/azure/home?forum=windowsazurewebsitespreview).

* [Criar aplicações Web na aplicação de serviço no Linux](./app-service-linux-how-to-create-a-web-app.md)
* [Utilizar PM2 configuração para Node.js no Web Apps no Linux](./app-service-linux-using-nodejs-pm2.md)

