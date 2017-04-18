<properties 
    pageTitle="Como configurar um serviço na nuvem (portal) | Microsoft Azure" 
    description="Saiba como configurar os serviços em nuvem no Azure. Saiba como atualizar a configuração do serviço na nuvem e configurar o acesso remoto instâncias de função. Estes exemplos utilizam o portal do Azure." 
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

Pode configurar as definições utilizadas mais frequentemente para um serviço na nuvem no portal do Azure. Em alternativa, se o modo atualizar os seus ficheiros de configuração diretamente, transferir um ficheiro de configuração do serviço para atualizar e, em seguida, carregar o ficheiro atualizado e actualizar o serviço de nuvem com as alterações à configuração. Qualquer forma, as atualizações de configuração são eliminadas para todas as instâncias de função.

Também pode gerir as instâncias das funções de serviço de nuvem ou ambiente de trabalho remoto nas mesmas.

Azure apenas pode assegurar a disponibilidade de serviço de percentagem 99.95 durante as atualizações de configuração se tiver, pelo menos, duas instâncias de função para cada função. Permite que uma máquina de virtual processar pedidos de cliente, enquanto o outro está a ser atualizado. Para mais informações, consulte o artigo [Acordos do nível de serviço](https://azure.microsoft.com/support/legal/sla/).

## <a name="change-a-cloud-service"></a>Alterar um serviço na nuvem

Depois de abrir o [Azure portal](https://portal.azure.com/), navegue até ao seu serviço de nuvem. A partir daqui gerir muitos aspetos do mesmo. 

![Página de definições](./media/cloud-services-how-to-configure-portal/cloud-service.png)

As ligações de **Definições** ou **todas as definições de** serão aberto o pá de **Definições** onde pode alterar as **Propriedades**, altere a **configuração**, gerir os **certificados**, o programa de configuração de **regras de alertas**,-las e gerir os **utilizadores** que têm acesso a este serviço na nuvem.

![Pá de definições de serviço de nuvem Azure](./media/cloud-services-how-to-configure-portal/cs-settings-blade.png)

>[AZURE.NOTE]
>O sistema operativo utilizado para o serviço de nuvem não pode ser alterado utilizando o **Azure portal**, só pode alterar esta definição através do [portal clássica Azure](http://manage.windowsazure.com/). Isto é detalhado [aqui](cloud-services-how-to-configure.md#update-a-cloud-service-configuration-file).

## <a name="monitoring"></a>Monitorização

Pode adicionar alertas no seu serviço de nuvem. Clique em **Definições** > **Regras de alerta** > **Adicionar alerta**. 

![](./media/cloud-services-how-to-configure-portal/cs-alerts.png)

A partir daqui pode configurar um alerta. Com a caixa pendente **Mertic** , pode configurar um alerta para os seguintes tipos de dados.

- Leitura de disco
- Escrita no disco
- De rede
- Saída de rede
- Percentagem de CPU 

![](./media/cloud-services-how-to-configure-portal/cs-alert-item.png)

### <a name="configure-monitoring-from-a-metric-tile"></a>Configurar a monitorização a partir de um mosaico métrico

Em vez de utilizar **as definições de** > **Regras de alerta**, pode clicar num dos mosaicos métricos na secção de **monitorização** da pá **serviço na nuvem** .

![Monitorização do serviço na nuvem](./media/cloud-services-how-to-configure-portal/cs-monitoring.png)

A partir daqui pode personalizar o gráfico utilizado com o mosaico ou adicionar uma regra de alerta.


## <a name="reboot-reimage-or-remote-desktop"></a>Reinicie, reimage ou ambiente de trabalho remoto

Neste momento não é possível configurar o ambiente de trabalho remoto utilizando o **Azure portal**. No entanto, pode configurar-o através de do [portal clássica Azure](cloud-services-role-enable-remote-desktop.md), [PowerShell](cloud-services-role-enable-remote-desktop-powershell.md), ou [Visual Studio](../vs-azure-tools-remote-desktop-roles.md). 

Em primeiro lugar, clique na instância do serviço na nuvem.

![Instância do serviço na nuvem](./media/cloud-services-how-to-configure-portal/cs-instance.png)

A partir do pá que abre, pode iniciar uma ligação de ambiente de trabalho remoto, remotamente reinicie a instância ou remotamente reimage uou (começa com uma imagem fresca) a instância.

![Botões de instância do serviço na nuvem](./media/cloud-services-how-to-configure-portal/cs-instance-buttons.png)



## <a name="reconfigure-your-cscfg"></a>Reconfigurar o seu .cscfg

Poderá ter de informática reconfigure o serviço de nuvem através do ficheiro de [configuração de serviço (cscfg)](cloud-services-model-and-package.md#cscfg) . Primeiro tem de transferir o seu ficheiro .cscfg, modificá-lo, em seguida, carregue-o.

1. Clique no ícone de **Definições** ou a ligação de **todas as definições** para abrir o pá as **Definições** .

    ![Página de definições](./media/cloud-services-how-to-configure-portal/cloud-service.png)

2. Clique no item de **configuração** .

    ![Configuração pá](./media/cloud-services-how-to-configure-portal/cs-settings-config.png)

3. Clique no botão **Transferir** .

    ![Transferir](./media/cloud-services-how-to-configure-portal/cs-settings-config-panel-download.png)

4. Depois de atualizar o ficheiro de configuração do serviço, carregar e aplicar as atualizações de configuração:

    ![Carregar](./media/cloud-services-how-to-configure-portal/cs-settings-config-panel-upload.png) 
    
5. Selecione o ficheiro .cscfg e clique em **OK**.

            
## <a name="next-steps"></a>Próximos passos

* Saiba como [Implementar um serviço na nuvem](cloud-services-how-to-create-deploy-portal.md).
* Configure um [nome de domínio personalizado](cloud-services-custom-domain-name-portal.md).
* [Gerir o serviço na nuvem](cloud-services-how-to-manage-portal.md).
* Configure [os certificados ssl](cloud-services-configure-ssl-certificate-portal.md).