<properties
   pageTitle="Lógica de aplicações no local ligação de dados do gateway | Microsoft Azure"
   description="Obter informações sobre como criar uma ligação para o gateway de dados no local a partir de uma aplicação de lógica."
   services="logic-apps"
   documentationCenter=".net,nodejs,java"
   authors="jeffhollan"
   manager="erikre"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="07/05/2016"
   ms.author="jehollan"/>

# <a name="connect-to-the-on-premises-data-gateway-for-logic-apps"></a>Ligar para o gateway de dados no local para as aplicações de lógica

Conectores de aplicações suportados lógica permitem-lhe configurar a ligação aos dados do access no local através do gateway de dados no local.  Os passos seguintes irão guiá-lo através de como instalar e configurar o gateway de dados no local para trabalhar com uma aplicação de lógica.

## <a name="prerequisites"></a>Pré-requisitos

* Tem de ser a utilizar uma trabalho ou escola endereço de e-mail no Azure para associar o gateway de dados no local com a sua conta (conta Azure Active Directory com base)
    * Se estiver a utilizar uma Account Microsoft (por exemplo, @outlook.com, @live.com) pode utilizar a sua conta Azure para criar uma trabalho ou escola endereço de e-mail ao [seguir os passos aqui](../virtual-machines/virtual-machines-windows-create-aad-work-id.md#locate-your-default-directory-in-the-azure-classic-portal)

> [AZURE.WARNING] Existe uma limitação atualmente que no local gateway instalar só estará concluída ao utilizar uma conta que foi registada com o Power BI.  Entretanto, registe qualquer conta com "Power BI gratuito" para concluir a instalação com êxito.

* Tem de ter no local os dados de gateway [instalado num computador local](app-service-logic-gateway-install.md).
* Gateway tem não ter sido pedido por outro gateway de dados do Azure no local ([reclamar acontece com a criação do passo 2 abaixo](#2-create-an-azure-on-premises-data-gateway-resource)) - uma instalação só pode ser associada ao recurso de um gateway.

## <a name="installing-and-configuring-the-connection"></a>Instalar e configurar a ligação

### <a name="1-install-the-on-premises-data-gateway"></a>1. a instalar o gateway de dados no local

Pode encontrar informações sobre como instalar o gateway de dados no local [neste artigo](app-service-logic-gateway-install.md).  O gateway tem de estar instalado num computador no local antes de poder continuar com o resto dos passos.

### <a name="2-create-an-azure-on-premises-data-gateway-resource"></a>2. Crie um recurso de gateway de dados do Azure no local

Depois de instalado, tem de associar a sua subscrição Azure com o gateway de dados no local.

1. Iniciar sessão no Azure utilizando o trabalho mesmo ou o endereço de e-mail escolares que foi utilizado durante a instalação do gateway
1. Clique em **Novo** botão de recurso
1. Procurar e selecione o **gateway de dados no local**
1. Preencha as informações para associar o gateway a sua conta – incluindo selecionando o **Nome de instalação**

    ![Ligação de dados no local do Gateway][1]
1. Clique no botão **Criar** para criar o recurso

### <a name="3-create-a-logic-app-connection-in-the-designer"></a>3. criar uma ligação de aplicação de lógica no estruturador de

Agora que a sua subscrição do Azure está associada uma instância do gateway de dados no local, pode criar uma ligação à mesma a partir de dentro de uma aplicação de lógica.

1. Abra uma aplicação de lógica e selecione uma conexão que suporta a conectividade no local (a partir deste escrita, SQL Server)
1. Selecione a caixa de verificação para **ligar através do gateway de dados no local**

    ![Criação de Gateway de Designer da aplicação de lógica][2]
1. Selecione o **Gateway** para ligar e concluir quaisquer outras informações de ligação necessárias
1. Clique em **Criar** para criar a ligação

Agora deverá estar configurada com êxito a ligação para utilização na sua aplicação de lógica.  

## <a name="next-steps"></a>Próximos passos
- [Exemplos de comuns e cenários para as aplicações de lógica](app-service-logic-examples-and-scenarios.md)
- [Funcionalidades de integração de empresa](app-service-logic-enterprise-integration-overview.md)

<!-- Image references -->
[1]: ./media/app-service-logic-gateway-connection/createblade.PNG
[2]: ./media/app-service-logic-gateway-connection/blankconnection.PNG
[3]: ./media/app-service-logic-gateway-connection/checkbox.PNG