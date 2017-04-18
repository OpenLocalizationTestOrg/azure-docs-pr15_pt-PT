<properties
    pageTitle="Serviços de domínio do Azure AD: Definições de DNS de atualização para a rede virtual Azure | Microsoft Azure"
    description="Introdução ao Azure Active Directory Domain Services"
    services="active-directory-ds"
    documentationCenter=""
    authors="mahesh-unnikrishnan"
    manager="stevenpo"
    editor="curtand"/>

<tags
    ms.service="active-directory-ds"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/21/2016"
    ms.author="maheshu"/>

# <a name="azure-ad-domain-services---update-dns-settings-for-the-azure-virtual-network"></a>Serviços de domínio Azure AD - definições de DNS de atualização para a rede virtual Azure

## <a name="task-4-update-dns-settings-for-the-azure-virtual-network"></a>Tarefa 4: Atualizar as definições de DNS para a rede virtual Azure
Em que antecede tarefas de configuração, activou com êxito serviços de domínio do Azure AD para o seu diretório. A tarefa seguinte é garantir que os computadores numa rede virtual podem ligar-se e consumir estes serviços. Atualize as definições do servidor DNS para a sua rede virtual para apontarem para os dois endereços IP no qual dos serviços de domínio do Azure AD está disponível na rede virtual.

> [AZURE.NOTE] Anote os endereços IP para serviços de domínio do Azure AD apresentados no separador **Configurar** do seu diretório, depois de ter ativado o serviços de domínio do Azure AD para o diretório.

Execute os seguintes passos de configuração para atualizar a definição do servidor DNS para a rede virtual na qual ativou dos serviços de domínio do Azure AD.

1. Navegue para o **portal clássica Azure** ([https://manage.windowsazure.com](https://manage.windowsazure.com)).

2. Selecione o nó de **redes** no painel da esquerda.

    ![Nó de redes virtuais](./media/active-directory-domain-services-getting-started/virtual-network-select.png)

3. No separador **Redes virtuais** , selecione a rede virtual nas quais é activado serviços de domínio do Azure AD ver as respetivas propriedades.

4. Clique no separador **Configurar** .

    ![Nó de redes virtuais](./media/active-directory-domain-services-getting-started/virtual-network-configure-tab.png)

5. Na secção **DNS servers** , introduza os endereços IP dos serviços de domínio do Azure AD.

6. Certifique-se de que introduz os endereços IP que foram apresentados na secção **Serviços de domínio** no separador **Configurar** do seu diretório.

7. Para guardar as definições do servidor DNS para esta rede virtual, clique em **Guardar** no painel de tarefas na parte inferior da página.

   ![Atualize as definições de servidor DNS para a rede virtual.](./media/active-directory-domain-services-getting-started/update-dns.png)

> [AZURE.NOTE] Depois de atualizar as definições de servidor DNS para a rede virtual, poderá demorar algum tempo para máquinas virtuais da rede para obter a configuração de DNS atualizada. Se não for uma máquina virtual não é possível ligar para o domínio, pode limpar a cache DNS (por ex. ' ipconfig/flushdns') na máquina virtual. Este comando força uma atualização das definições de DNS na máquina virtual.


## <a name="task-5---enable-password-synchronization-to-azure-ad-domain-services"></a>Tarefa 5 - Ativar sincronização de palavras-passe para serviços de domínio do Azure AD
A tarefa de configuração seguinte é para [Ativar a sincronização de palavras-passe para serviços de domínio do Azure AD](active-directory-ds-getting-started-password-sync.md).
