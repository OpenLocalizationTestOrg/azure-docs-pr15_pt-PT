<properties 
    pageTitle="Como configurar um serviço na nuvem (portal clássico) | Microsoft Azure" 
    description="Saiba como configurar os serviços em nuvem no Azure. Saiba como atualizar a configuração do serviço na nuvem e configurar o acesso remoto instâncias de função." 
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




# <a name="how-to-configure-cloud-services"></a>Como configurar os serviços em nuvem

> [AZURE.SELECTOR]
- [Portal do Azure](cloud-services-how-to-configure-portal.md)
- [Azure portal clássico](cloud-services-how-to-configure.md)

Pode configurar as definições utilizadas mais frequentemente para um serviço na nuvem no portal do Azure clássico. Em alternativa, se o modo atualizar os seus ficheiros de configuração diretamente, transferir um ficheiro de configuração do serviço para atualizar e, em seguida, carregar o ficheiro atualizado e actualizar o serviço de nuvem com as alterações à configuração. Qualquer forma, as atualizações de configuração são eliminadas para todas as instâncias de função.

Portal do Azure clássico também permite-lhe para [Ativar a ligação ao ambiente de trabalho remoto para uma função no Azure serviços em nuvem](cloud-services-role-enable-remote-desktop.md)

Azure apenas pode assegurar a disponibilidade de serviço de percentagem 99.95 durante as atualizações de configuração se tiver, pelo menos, duas instâncias de função para cada função. Permite que uma máquina de virtual processar pedidos de cliente, enquanto o outro está a ser atualizado. Para mais informações, consulte o artigo [Acordos do nível de serviço](https://azure.microsoft.com/support/legal/sla/).

## <a name="change-a-cloud-service"></a>Alterar um serviço na nuvem

1. No [portal clássica Azure](http://manage.windowsazure.com/), clique em **Serviços em nuvem**, clique no nome do serviço em nuvem e, em seguida, clique em **Configurar**.

    ![Página de configuração](./media/cloud-services-how-to-configure/CloudServices_ConfigurePage1.png)
    
    Na página **Configurar** , pode configurar a monitorização, atualizar as definições de função e escolha o sistema operativo convidado e família para instâncias de função. 

2. Na **monitorização**, defina o nível de monitorização para verboso ou mínimas e configurar as cadeias de ligação de diagnóstico que são necessárias para verboso monitorização.

3. Para funções de serviço (agrupadas por função), pode atualizar as seguintes definições:
    
    >**Definições**  
    >Modifique os valores das definições de configuração de diversas que estão especificados nos elementos *ConfigurationSettings* do ficheiro de configuração (.cscfg) serviço.
    >
    >**Certificados**  
    >Altere a impressão digital do certificado que está a ser utilizada em encriptação SSL para uma função. Para alterar um certificado, primeiro terá de carregar o novo certificado (na página de **certificados** ). Em seguida, atualize a impressão digital na cadeia de certificado apresentada nas definições de função.

4. Num **sistema operativo**, pode alterar o sistema operativo família ou a versão de instâncias de uma função ou selecione **automática** para ativar as atualizações automáticas a versão atual do sistema operativo. As definições do sistema operativo aplicam a web funções e funções de trabalho, mas não afetam máquinas virtuais.

    Durante a implementação, a versão mais recente do sistema operativo está instalada em todas as instâncias de função e os sistemas operativos são atualizados automaticamente por predefinição. 
    
    Se precisar do seu serviço de nuvem executar uma versão do sistema operativo diferente devido a requisitos de compatibilidade no seu código, pode escolher um sistema operativo família e versão. Quando escolher uma versão específica do sistema operativo, automática do sistema operativo atualizações para o serviço de nuvem são suspensa. Terá de Certifique-se de que os sistemas operativos receber atualizações.
    
    Se a resolver problemas de compatibilidade de todos os que têm as suas aplicações com a versão mais recente do sistema operativo, pode ativar atualizações automáticas sistema operativo definindo a versão do sistema operativo para **Automático**. 
    
    ![Definições do sistema operativo](./media/cloud-services-how-to-configure/CloudServices_ConfigurePage_OSSettings.png)

5. Para guardar as suas definições de configuração e transmiti-las para as instâncias de função, clique em **Guardar**. (Clique em **Rejeitar** para cancelar as alterações). **Guardar** e **Rejeitar** são adicionados à barra de comandos depois de alterar uma definição de.

## <a name="update-a-cloud-service-configuration-file"></a>Atualizar um ficheiro de configuração do serviço de nuvem

1. Transferir um ficheiro de configuração de serviço (.cscfg) de nuvem com a configuração atual. Na página **Configurar** para o serviço de nuvem, clique em **Transferir**. Em seguida, clique em **Guardar**ou clique em **Guardar como** para guardar o ficheiro.

2. Depois de atualizar o ficheiro de configuração do serviço, carregar e aplicar as atualizações de configuração:

    1. Na página **Configurar** , clique em **carregar**.
    
        ![Carregar configuração](./media/cloud-services-how-to-configure/CloudServices_UploadConfigFile.png)
    
    2. No **ficheiro de configuração**, utilize **Procurar** para selecionar o ficheiro .cscfg atualizado.
    
    3. Se o seu serviço de nuvem contiver qualquer funções que têm apenas uma instância, selecione a caixa de verificação **Aplicar configuração, mesmo se uma ou mais funções contêm uma única ocorrência** para ativar as atualizações de configuração para as funções continuar.
    
        A menos que definir, pelo menos, duas instâncias de cada função, Azure não garante, pelo menos, 99.95 percentagem disponibilidade do seu serviço de nuvem durante atualizações de configuração de serviço. Para mais informações, consulte o artigo [Acordos do nível de serviço](https://azure.microsoft.com/support/legal/sla/).
    
    4. Clique em **OK** (marca de verificação). 


## <a name="next-steps"></a>Próximos passos

* Saiba como [Implementar um serviço na nuvem](cloud-services-how-to-create-deploy.md).
* Configure um [nome de domínio personalizado](cloud-services-custom-domain-name.md).
* [Gerir o serviço na nuvem](cloud-services-how-to-manage.md).
* [Ativar a ligação ao ambiente de trabalho remoto para uma função de serviços em nuvem Azure](cloud-services-role-enable-remote-desktop.md)
* Configure [os certificados ssl](cloud-services-configure-ssl-certificate.md).
