<properties
    pageTitle="Serviços de domínio do Azure AD: Criar o grupo de administradores de CC AAD | Microsoft Azure"
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
    ms.topic="article"
    ms.date="10/03/2016"
    ms.author="maheshu"/>

# <a name="get-started-with-azure-ad-domain-services"></a>Começar a trabalhar com serviços de domínio do Azure AD

Este artigo explica as tarefas de configuração necessárias para activar os serviços de domínio do Azure AD do seu inquilino do Azure AD.

## <a name="task-1-create-the-aad-dc-administrators-group"></a>Tarefa 1: Criar o grupo 'AAD CC administradores'
A primeira tarefa é criar um grupo administrativo no seu inquilino do Azure Active Directory. Este grupo administrativo especial chama **AAD CC administradores**. Os membros deste grupo são concedidos privilégios administrativos no máquinas que estão associadas domínio para o domínio gerido de serviços de domínio do Azure AD. Em máquinas façam parte de um domínio, este grupo é adicionado ao grupo 'Administradores'. Para além disso, os membros deste grupo podem utilizar o ambiente de trabalho remoto para ligar remotamente a máquinas façam parte de um domínio.  

> [AZURE.NOTE] Não tiver privilégios de administrador de domínio ou o administrador da empresa no domínio gerido criado utilizando os serviços de domínio do Azure AD. No domínios geridos, estes privilégios são reservados pelo serviço em não ficam disponíveis para utilizadores do inquilino. No entanto, pode utilizar o grupo de administradores especial criado nesta tarefa de configuração, para efetuar algumas operações privilegiadas. Estas operações incluem participar computadores para o domínio, pertencente ao grupo Administradores no máquinas façam parte de um domínio, configurar etc de política de grupo.

Nesta tarefa de configuração, crie o grupo administrativo e adicionar um ou mais utilizadores no seu diretório ao grupo. Execute os seguintes passos para criar o grupo administrativo para serviços de domínio do Azure AD:

1. Navegue até ao **portal clássica Azure** ([https://manage.windowsazure.com](https://manage.windowsazure.com))

2. Selecione o nó **Do Active Directory** no painel da esquerda.

3. Selecione o inquilino do Azure AD (directório) para o qual pretende ativar serviços de domínio do Azure AD. Só pode criar um domínio para cada directório Azure AD.

    ![Selecione diretório do Azure AD](./media/active-directory-domain-services-getting-started/select-aad-directory.png)

4. Clique no separador de **grupos** .

5. Para adicionar um grupo ao seu inquilino do Azure AD, clique em **Adicionar grupo** a partir do painel de tarefas na parte inferior da página.

    ![Adicionar botão Agrupar](./media/active-directory-domain-services-getting-started/add-group-button.png)

6. Crie um grupo com o nome **AAD CC administradores**. Defina o **tipo de grupo** para a **segurança**.

    > [AZURE.WARNING] Para permitir o acesso dentro de seus serviços de domínio do Azure AD gerido domínio, crie um grupo com este nome exata.

    ![Criar o grupo de administradores](./media/active-directory-domain-services-getting-started/create-admin-group.png)

7. Adicione uma descrição para este grupo para que outras pessoas compreender que este grupo é utilizado para conceder privilégios administrativos dentro dos serviços de domínio do Azure AD.

8. Depois de ter sido criado o grupo, clique no nome do grupo para ver as propriedades deste grupo. Para adicionar utilizadores como os membros deste grupo, clique no botão **Adicionar membros** no painel inferior.

    ![Adicionar botão de membros do grupo](./media/active-directory-domain-services-getting-started/add-group-members-button.png)

9. Na caixa de diálogo **Adicionar membros** , selecione os utilizadores que devem ser os membros deste grupo e selecione a caixa de verificação quando tiver terminado.

    ![Adicionar utilizadores ao grupo de administradores](./media/active-directory-domain-services-getting-started/add-group-members.png)

<br>

## <a name="task-2-create-or-select-an-azure-virtual-network"></a>Tarefa 2: Criar ou selecionar uma rede virtual Azure
A tarefa de configuração seguinte é [criar ou selecionar uma rede virtual Azure](active-directory-ds-getting-started-vnet.md).
