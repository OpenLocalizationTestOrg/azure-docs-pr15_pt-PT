<properties
    pageTitle="Serviços de domínio do Azure AD: Criar ou selecionar uma rede virtual | Microsoft Azure"
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
    ms.date="10/03/2016"
    ms.author="maheshu"/>

# <a name="create-or-select-a-virtual-network-for-azure-ad-domain-services"></a>Criar ou selecionar uma rede virtual para serviços de domínio do Azure AD

## <a name="guidelines-to-select-an-azure-virtual-network"></a>Diretrizes para selecionar uma rede virtual Azure
> [AZURE.NOTE] **Antes de começar**: referir-se a [Considerações para serviços de domínio do Azure AD de rede](active-directory-ds-networking.md).


## <a name="task-2-create-an-azure-virtual-network"></a>Tarefa 2: Criar uma rede virtual Azure
A tarefa de configuração seguinte é criar uma rede virtual Azure e uma sub-rede dentro da mesma. Ativar serviços de domínio do Azure AD nesta sub-rede na rede da sua virtual. Se já tiver uma rede virtual existente que prefere utilizar, pode ignorar este passo.

> [AZURE.NOTE] Certifique-se de que a rede virtual Azure criar ou optar por utilizar com os serviços de domínio do Azure AD pertence para uma região Azure que seja suportada dos serviços de domínio do Azure AD. Ver a página de [Serviços Azure por região](https://azure.microsoft.com/regions/#services/) saber as regiões Azure na qual dos serviços de domínio do Azure AD está disponível.

Tenha em atenção para baixo no nome da rede virtual para selecionar a rede para a direita virtual quando activar os serviços de domínio do Azure AD num passo de configuração subsequentes.

Execute os seguintes passos de configuração para criar uma rede virtual Azure na qual pretende ativar serviços de domínio do Azure AD.

1. Navegue para o **portal clássica Azure** ([https://manage.windowsazure.com](https://manage.windowsazure.com)).

2. Selecione o nó de **redes** no painel da esquerda.

    ![Nó de redes](./media/active-directory-domain-services-getting-started/networks-node.png)

3. No painel de tarefas na parte inferior da página, clique em **Novo** .

    ![Nó de redes virtuais](./media/active-directory-domain-services-getting-started/virtual-networks.png)

4. No nó **Serviços de rede** , selecione **Rede Virtual**.

5. Clique em **Criar rápidas** para criar uma rede virtual.

    ![Criar rede virtual - rápida](./media/active-directory-domain-services-getting-started/virtual-network-quickcreate.png)

6. Especifique um **nome** para a sua rede virtual. Também pode escolher configurar o **espaço de endereços** ou **contar VM máximo** para esta rede. Pode deixar a definição de **servidor DNS** definida como 'Nenhum' por agora. Pode atualizar o servidor de DNS definição após o seu domínio do Azure AD ativar serviços.

7. Certifique-se de que seleciona uma região suportada Azure na lista pendente de **localização** . Ver a página de [Serviços Azure por região](https://azure.microsoft.com/regions/#services/) saber as regiões Azure na qual dos serviços de domínio do Azure AD está disponível.

8. Para criar a sua rede virtual, clique no botão **criar uma rede Virtual** .

    ![Crie uma rede virtual para serviços de domínio do Azure AD.](./media/active-directory-domain-services-getting-started/create-vnet.png)

9. Quando a rede virtual estiver criada, selecione a rede virtual e clique no separador **Configurar** .

    ![Criar uma sub-rede](./media/active-directory-domain-services-getting-started/create-vnet-properties.png)

10. Navegue até à secção **espaços de endereços de rede virtual** . Clique em **Adicionar sub-rede** e especifique uma sub-rede com o nome **AaddsSubnet**. Clique em **Guardar** para criar a sub-rede.

    ![Crie uma sub-rede para serviços de domínio do Azure AD.](./media/active-directory-domain-services-getting-started/create-vnet-add-subnet.png)


<br>

## <a name="task-3---enable-azure-ad-domain-services"></a>Tarefa 3 - ativar serviços de domínio do Azure AD
A tarefa de configuração seguinte é para [Activar os serviços de domínio do Azure AD](active-directory-ds-getting-started-enableaadds.md).
