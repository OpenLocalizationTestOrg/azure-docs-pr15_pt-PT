<properties
    pageTitle="Como criar e implementar um serviço na nuvem | Microsoft Azure"
    description="Saiba como criar e implementar um serviço na nuvem através do portal Azure."
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
    ms.date="10/11/2016"
    ms.author="adegeo"/>




# <a name="how-to-create-and-deploy-a-cloud-service"></a>Como criar e implementar um serviço na nuvem

> [AZURE.SELECTOR]
- [Portal do Azure](cloud-services-how-to-create-deploy-portal.md)
- [Azure portal clássico](cloud-services-how-to-create-deploy.md)

Portal do Azure fornece duas formas de criar e implementar um serviço na nuvem: *Criar rápida* e *Criar personalizado*.

Este artigo explica como utilizar o método rápido criar para criar um novo serviço de nuvem e, em seguida, utilize a **carregar** para carregar e implementar um pacote de serviço de nuvem no Azure. Quando utilizar este método, o portal do Azure torna disponíveis ligações convenientes para concluir todos os requisitos à medida que avança. Se estiver pronto para implementar o seu serviço de nuvem quando o criar, que pode fazer ambos ao mesmo tempo utilizando criar personalizado.

> [AZURE.NOTE] Se planeia publicar o seu serviço de nuvem a partir de serviços de equipa de Studio Visual (VSTS), utilize criação rápida e, em seguida, configure o publicação VSTS o guia de introdução do Azure ou o dashboard. Para obter mais informações, consulte o artigo [Entrega contínua para Azure utilizando Visual Studio equipa os serviços][TFSTutorialForCloudService], ou consulte o artigo ajuda para a página de **Início rápido** .

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

- Se pretende implementar um serviço na nuvem que utiliza Secure Sockets Layer (SSL) para encriptação de dados, a [Configurar a aplicação](cloud-services-configure-ssl-certificate-portal.md#modify) para o SSL.

- Se pretender configurar ligações de ambiente de trabalho remoto para instâncias de função, [configure as funções](cloud-services-role-enable-remote-desktop.md) para ambiente de trabalho remoto. Só pode ser feito no portal do clássico.

- Se pretende configurar verboso monitorização do seu serviço de nuvem, ative o Azure diagnósticos para o serviço de nuvem. *Monitorização mínimas* (a predefinição do nível de monitorização) utiliza contadores de desempenho reunidos dos sistemas operativos de anfitrião de instâncias de função (máquinas virtuais). *Monitorização verboso* reúne métricas adicionais com base em dados de desempenho as instâncias de função para activar a análise mais perto dos problemas que ocorram durante o processamento de aplicação. Para saber como ativar o Azure diagnósticos, consulte o artigo [Ativando diagnósticos no Azure](cloud-services-dotnet-diagnostics.md).

Para criar um serviço na nuvem com implementações de funções da web ou funções de trabalho, tem de [criar o pacote do serviço](cloud-services-model-and-package.md#servicepackagecspkg).

## <a name="before-you-begin"></a>Antes de começar

- Se ainda não tiver instalado o SDK do Azure, clique em **Instalar o SDK do Azure** para abrir a [página de transferências do Azure](https://azure.microsoft.com/downloads/)e, em seguida, transfira o SDK para o idioma que preferir desenvolver o seu código. (Terá a oportunidade de fazê-lo mais tarde.)

- Se as ocorrências de função necessitam de um certificado, crie os certificados. Serviços em nuvem necessitam de um ficheiro. pfx com uma chave privada. [Que pode carregar os certificados para Azure]() à medida que criar e implementar o serviço de nuvem.

- Se planeia implementar o serviço de nuvem a um grupo de afinidade, crie o grupo afinidade. Pode utilizar um grupo de afinidade para implementar o seu serviço em nuvem e outros serviços do Azure na mesma localização numa região. Pode criar o grupo afinidade na área de **redes** do Azure clássica portal, na página **afinidade grupos** .


## <a name="create-and-deploy"></a>Crie e implemente

1. Inicie a sessão [portal do Azure](https://portal.azure.com/).
2. Clique em **Novo > máquinas virtuais**e, em seguida, desloque-se para baixo e clique em **Serviço na nuvem**.

    ![Publicar o seu serviço de nuvem](media/cloud-services-how-to-create-deploy-portal/create-cloud-service.png)

3. Na parte inferior da página de informações que é apresentada, clique em **Criar**. 
4. Na nova pá de **Serviço na nuvem** , introduza um valor para o **nome de DNS**.
5. Criar um novo **Grupo de recursos** ou selecione uma existente.
6. Selecione uma **localização**.
7. Clique em **Compactar**. Isto vai abrir o pá **carregar um pacote** . Preencha os campos obrigatórios.  

     Se qualquer um dos seus direitos contiverem uma única ocorrência, certifique-se **que implementar o mesmo se uma ou mais funções contêm uma única instância** está selecionada.

8. Certifique-se de que a **implementação de início** está selecionada.
9. Clique em **OK** irá fechar o pá **carregar um pacote** .
10. Se não tiver quaisquer certificados para adicionar, clique em **Criar**.

    ![Publicar o seu serviço de nuvem](media/cloud-services-how-to-create-deploy-portal/select-package.png)

## <a name="upload-a-certificate"></a>Carregar um certificado

Se o pacote de implementação do tiver sido [configurado para utilizar certificados](cloud-services-configure-ssl-certificate-portal.md#modify), pode carregar o certificado agora.

1. Seleccionar **certificados**, e no pá **certificados de adicionar** , selecione o ficheiro. pfx do SSL certificado e, em seguida, forneça a **palavra-passe** para o certificado,
2. Clique em **anexar certificado**e, em seguida, clique em **OK** no pá **certificados de adicionar** .
3. Clique em **Criar** no pá **Serviço na nuvem** . Quando a implementação atingiu o estado **pronto** , pode avançar para os passos.

    ![Publicar o seu serviço de nuvem](media/cloud-services-how-to-create-deploy-portal/attach-cert.png)


## <a name="verify-your-deployment-completed-successfully"></a>Verifique se a sua implementação foi concluída com êxito

1. Clique na instância do serviço na nuvem.

    O estado deve mostrar que o serviço está **em execução**.

2. Em **Essentials**, clique no **URL do Site** para abrir o seu serviço de nuvem num browser.

    ![CloudServices_QuickGlance](./media/cloud-services-how-to-create-deploy-portal/running.png)


[TFSTutorialForCloudService]: http://go.microsoft.com/fwlink/?LinkID=251796

## <a name="next-steps"></a>Próximos passos

* [Configuração geral do seu serviço de nuvem](cloud-services-how-to-configure-portal.md).
* Configure um [nome de domínio personalizado](cloud-services-custom-domain-name-portal.md).
* [Gerir o serviço na nuvem](cloud-services-how-to-manage-portal.md).
* Configure [os certificados ssl](cloud-services-configure-ssl-certificate-portal.md).