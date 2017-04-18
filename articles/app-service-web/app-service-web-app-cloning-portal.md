<properties
    pageTitle="Web App clonar através do Portal do Azure"
    description="Saiba como clonar suas aplicações Web do novo Web Apps através do Portal do Azure."
    services="app-service\web"
    documentationCenter=""
    authors="ahmedelnably"
    manager="stefsch"
    editor=""/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="03/08/2016"
    ms.author="ahmedelnably"/>

# <a name="azure-app-service-app-cloning-using-azure-portal"></a>Aplicação Azure de aplicação de serviço clonar utilizar o Portal do Azure#

A funcionalidade clonagem no [Azure aplicação de serviço Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714) permite-lhe facilmente clonar aplicações web existentes para uma aplicação recentemente criada numa região diferente ou na mesma região. Isto irá permitir que os clientes implementar um número de aplicações através de diferentes regiões de forma rápida e fácil.

Aplicação clonar está atualmente suportados apenas para planos de serviço de aplicação de camada premium. A nova funcionalidade utiliza as mesmas limitações como a funcionalidade de cópia de segurança do Web Apps, consulte o artigo [Criar cópia de segurança de uma aplicação web na aplicação de serviço de Azure](web-sites-backup.md).

[AZURE.INCLUDE [app-service-web-to-api-and-mobile](../../includes/app-service-web-to-api-and-mobile.md)] 


## <a name="cloning-an-existing-app"></a>Clonar uma aplicação existente ##

A aplicação web tem de executar no modo de **Premium** que criar um Clonar para a aplicação web.

1. No [Portal do Azure](https://portal.azure.com/), abra pá da sua aplicação web.
2. Clique em **Ferramentas**. Em seguida, na pá **Ferramentas** , clique em **Aplicação clonar**.

    ![][1]

    > [AZURE.NOTE]
    > Se a aplicação web ainda não estiver no modo de **Premium** , receberá uma mensagem a indicar os modos de suportados para clonar de aplicação. Neste momento, tem a opção para selecionar a **Atualizar**.
    
3. No pá a **Aplicação clonar** fornece um nome da nova aplicação web, grupo de recursos e plano de serviço de aplicação. Também o utilizador poderá escolher se pretende clonar um número de definições da origem de aplicação web ou não.

    ![][2]

4. Depois de clicar em **Criar** a plataforma irá começar a trabalhar em criar um clonar da aplicação web origem.

## <a name="cloning-an-existing-app-to-an-app-service-environment"></a>Clonar uma aplicação existente para um ambiente do serviço de aplicação##

Na pá **Clonar aplicação** cliente terá a opção para escolher um conjunto de aplicação num ambiente existente do serviço de aplicação.

## <a name="current-restrictions"></a>Restrições atuais ##

Esta funcionalidade está atualmente na pré-visualização, estamos a trabalhar para adicionar novas capacidades ao longo do tempo, a lista seguinte são as restrições conhecidas sobre o suporte técnico da atual da aplicação clonar no Portal do Azure:

- Definições do Gestor de tráfego Azure não são clonar
- As definições de escala automática não estão clonar
- Definições da agenda de cópia de segurança não são clonar
- Definições de VNET não são clonar
- Informações de aplicação não são automaticamente configuradas da aplicação web do destino
- Fácil Auth definições não são clonar
- Extensão de kudu não são clonar
- Sugestão regras não são clonar
- Conteúdo da base de dados não são clonar


### <a name="references"></a>Referências ###
- [Web App clonar através do PowerShell](app-service-web-app-cloning.md)
- [Agregar uma aplicação web na aplicação de serviço do Azure](web-sites-backup.md)
- [Como criar um ambiente de aplicação de serviço](app-service-web-how-to-create-an-app-service-environment.md)
- [Criar uma aplicação web num ambiente do serviço de aplicação](app-service-web-how-to-create-a-web-app-in-an-ase.md)
- [Introdução à aplicação de serviço ambiente](app-service-app-service-environment-intro.md)

<!--Image references-->
[1]: ./media/app-service-web-app-cloning-portal/CloningBlade.png
[2]: ./media/app-service-web-app-cloning-portal/CloneSettings.png