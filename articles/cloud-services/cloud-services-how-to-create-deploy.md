<properties
    pageTitle="Como criar e implementar um serviço na nuvem | Microsoft Azure"
    description="Saiba como criar e implementar um serviço na nuvem utilizando o método de criação rápida no Azure."
    services="cloud-services"
    documentationCenter=""
    authors="Thraka"
    manager="timlt"
    editor=""/>

<tags
    ms.service="cloud-services"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/06/2016"
    ms.author="adegeo"/>




# <a name="how-to-create-and-deploy-a-cloud-service"></a>Como criar e implementar um serviço na nuvem

> [AZURE.SELECTOR]
- [Portal do Azure](cloud-services-how-to-create-deploy-portal.md)
- [Azure portal clássico](cloud-services-how-to-create-deploy.md)

Portal clássico do Azure fornece duas formas de criar e implementar um serviço na nuvem: **Criar rápida** e **Criar personalizado**.

Este tópico explica como utilizar o método rápido criar para criar um novo serviço de nuvem e, em seguida, utilize a **carregar** para carregar e implementar um pacote de serviço de nuvem no Azure. Quando utilizar este método, o portal clássico do Azure torna disponíveis ligações convenientes para concluir todos os requisitos à medida que avança. Se estiver pronto para implementar o seu serviço de nuvem quando o criar, que pode fazer ambos ao mesmo tempo utilizando **Criar personalizado**.

> [AZURE.NOTE] Se planeia publicar o seu serviço de nuvem a partir de serviços de equipa de Studio Visual (VSTS), utilize a criação rápida e, em seguida, configurar a publicação de VSTS a partir do **Início rápido** ou o dashboard. Para obter mais informações, consulte o artigo [Entrega contínua para Azure utilizando Visual Studio equipa os serviços][TFSTutorialForCloudService], ou consulte o artigo ajuda para a página de **Início rápido** .

## <a name="concepts"></a>Conceitos
Três componentes são necessários para implementar uma aplicação como um serviço na nuvem no Azure:

- **Definição de serviço**  
  O ficheiro de definição de serviço de nuvem (.csdef) define o modelo de serviço, incluindo o número de funções.

- **Configuração do serviço**  
  O ficheiro de configuração de serviço de nuvem (.cscfg) fornece as definições de configuração para o cloud funções de serviços e individuais, incluindo o número de ocorrências de função.

- **Pacote de serviço**  
  O pacote de serviço (.cspkg) contém o código da aplicação e configurações e o ficheiro de definição de serviço.
  
Pode saber mais sobre estas e como criar um pacote [aqui](cloud-services-model-and-package.md).

## <a name="prepare-your-app"></a>Preparar a sua aplicação
Pode implementar um serviço na nuvem, tem de criar o pacote de serviço de nuvem (.cspkg) a partir do seu código da aplicação e um ficheiro de configuração de serviço de nuvem (.cscfg). O SDK do Azure fornece ferramentas para preparar estes ficheiros de implementação necessários. Pode instalar o SDK a partir da página [Azure transferências](https://azure.microsoft.com/downloads/) no idioma que preferir desenvolver código da aplicação.

Funcionalidades de serviço de nuvem três exigem configurações especiais antes de exportar um pacote de serviço:

- Se pretende implementar um serviço na nuvem que utiliza Secure Sockets Layer (SSL) para encriptação de dados, a [Configurar a aplicação](cloud-services-configure-ssl-certificate.md#step-2-modify-the-service-definition-and-configuration-files) para o SSL.

- Se pretender configurar ligações de ambiente de trabalho remoto para instâncias de função, [configure as funções](cloud-services-role-enable-remote-desktop.md) para ambiente de trabalho remoto.

- Se pretende configurar verboso monitorização do seu serviço de nuvem, ative o Azure diagnósticos para o serviço de nuvem. *Monitorização mínimas* (a predefinição do nível de monitorização) utiliza contadores de desempenho reunidos dos sistemas operativos de anfitrião de instâncias de função (máquinas virtuais). "Monitorização verboso * reúne métricas adicionais com base em dados de desempenho as instâncias de função para activar a análise mais perto dos problemas que ocorram durante o processamento de aplicação. Para saber como ativar o Azure diagnósticos, consulte o artigo [Ativar diagnósticos no Azure](cloud-services-dotnet-diagnostics.md).

Para criar um serviço na nuvem com implementações de funções da web ou funções de trabalho, tem de [criar o pacote do serviço](cloud-services-model-and-package.md#servicepackagecspkg).

## <a name="before-you-begin"></a>Antes de começar

- Se ainda não tiver instalado o SDK do Azure, clique em **Instalar o SDK do Azure** para abrir a [página de transferências do Azure](https://azure.microsoft.com/downloads/)e, em seguida, transfira o SDK para o idioma que preferir desenvolver o seu código. (Terá a oportunidade de fazê-lo mais tarde.)

- Se as ocorrências de função necessitam de um certificado, crie os certificados. Serviços em nuvem necessitam de um ficheiro. pfx com uma chave privada. Pode [carregar os certificados para Azure](cloud-services-configure-ssl-certificate.md#step-3-upload-a-certificate) à medida que cria e implementar o serviço de nuvem.

- Se planeia implementar o serviço de nuvem a um grupo de afinidade, crie o grupo afinidade. Pode utilizar um grupo de afinidade para implementar o seu serviço em nuvem e outros serviços do Azure na mesma localização numa região. Pode criar o grupo afinidade na área de **redes** do Azure clássica portal, na página **afinidade grupos** .


## <a name="how-to-create-a-cloud-service-using-quick-create"></a>Como: criar um serviço de nuvem utilizando a criação rápida

1. No [portal clássica Azure](http://manage.windowsazure.com/), clique em **Novo**>**Calcular**>**Serviço na nuvem**>**Criação rápida**.

    ![CloudServices_QuickCreate](./media/cloud-services-how-to-create-deploy/CloudServices_QuickCreate.png)

2. No **URL**, introduza um nome de subdomínio para utilizar no URL público para aceder ao seu serviço de nuvem nas implementações de produção. É o formato de URL para implementações de produção: http://*myURL*. cloudapp.net.

3. Na **região ou afinidade do grupo**, selecione a região geográfica ou grupo de afinidade para implementar o serviço de nuvem. Selecione um grupo de afinidade se pretende implementar o seu serviço de nuvem para a mesma localização como outros serviços do Azure dentro de uma região.

4. Clique em **criar serviço na nuvem**.

    ![CloudServices_Region](./media/cloud-services-how-to-create-deploy/CloudServices_Regionlist.png)

    Pode monitorizar o estado do processo na área de mensagem na parte inferior da janela.

    É aberta a área de **Serviços em nuvem** , com o novo serviço de nuvem apresentado. Quando o estado muda para criado, a criação de serviço de nuvem foi concluída com êxito.

    ![CloudServices_CloudServicesPage](./media/cloud-services-how-to-create-deploy/CloudServices_CloudServicesPage.png)


## <a name="how-to-upload-a-certificate-for-a-cloud-service"></a>Como: carregue um certificado para um serviço na nuvem

1. No [portal clássica Azure](http://manage.windowsazure.com/), clique em **Serviços em nuvem**, clique no nome do serviço em nuvem e, em seguida, clique em **certificados**.

    ![CloudServices_QuickCreate](./media/cloud-services-how-to-create-deploy/CloudServices_EmptyDashboard.png)


2. Clique em **carregar um certificado** ou **carregar**.

3. No **ficheiro**, utilize **Procurar** para selecionar o certificado (ficheiro. pfx).

4. Em **palavra-passe**, introduza a chave privada para o certificado.

5. Clique em **OK** (marca de verificação).

    ![CloudServices_AddaCertificate](./media/cloud-services-how-to-create-deploy/CloudServices_AddaCertificate.png)

    Pode ver o progresso do carregamento na área de mensagem, apresentado abaixo. Quando o carregamento for concluída, o certificado é adicionado à tabela. Na área de mensagem, clique em OK para fechar a mensagem.

    ![CloudServices_CertificateProgress](./media/cloud-services-how-to-create-deploy/CloudServices_CertificateProgress.png)

## <a name="how-to-deploy-a-cloud-service"></a>Como: implementar um serviço na nuvem

1. No [portal clássica Azure](http://manage.windowsazure.com/), clique em **Serviços em nuvem**, clique no nome do serviço em nuvem e, em seguida, clique em **Dashboard**.

2. Clique em **carregar uma nova implementação de produção** ou **carregar**.

3. Na **etiqueta de implementação**, introduza um nome para a nova implementação - por exemplo, MyCloudServicev4.

3. No **pacote**, utilize **Procurar** para selecionar o ficheiro de pacote de serviço (.cspkg) a utilizar.

4. **Configuração**, utilize **Procurar** para selecionar o ficheiro de configurar de serviço (.cscfg) a utilizar.

5. Se o serviço de nuvem irá incluir qualquer funções com apenas uma instância, selecione a caixa de verificação **Implementar o mesmo se uma ou mais funções contêm uma única ocorrência** para ativar a implementação para continuar.

    Azure só pode garantir a percentagem de 99.95 acesso ao serviço de nuvem durante as atualizações de serviço e manutenção se a cada função tiver, pelo menos, duas instâncias. Se for necessário, pode adicionar instâncias de função adicionais na página de **escala** depois de implementar o serviço de nuvem. Para mais informações, consulte o artigo [Acordos do nível de serviço](https://azure.microsoft.com/support/legal/sla/).

6. Clique em **OK** (marca de verificação) para iniciar a implementação do serviço na nuvem.

    ![CloudServices_UploadaPackage](./media/cloud-services-how-to-create-deploy/CloudServices_UploadaPackage.png)

    Pode monitorizar o estado de implementação na área de mensagem. Clique em OK para ocultar a mensagem.

    ![CloudServices_UploadProgress](./media/cloud-services-how-to-create-deploy/CloudServices_UploadProgress.png)

## <a name="verify-your-deployment-completed-successfully"></a>Verifique se a sua implementação foi concluída com êxito

1. Clique em **Dashboard**.

    O estado deve mostrar que o serviço está **em execução**.

2. Em **vista rápida**, clique no URL do site para abrir o seu serviço de nuvem num browser.

    ![CloudServices_QuickGlance](./media/cloud-services-how-to-create-deploy/CloudServices_QuickGlance.png)


[TFSTutorialForCloudService]: cloud-services-continuous-delivery-use-vso.md
 
## <a name="next-steps"></a>Próximos passos

* [Configuração geral do seu serviço de nuvem](cloud-services-how-to-configure.md).
* Configure um [nome de domínio personalizado](cloud-services-custom-domain-name.md).
* [Gerir o serviço na nuvem](cloud-services-how-to-manage.md).
* Configure [os certificados ssl](cloud-services-configure-ssl-certificate.md).