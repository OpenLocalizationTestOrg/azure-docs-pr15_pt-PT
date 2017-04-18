<properties
    pageTitle="Serviços de domínio do Azure AD: Activar os serviços de domínio do Azure AD | Microsoft Azure"
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
    ms.date="10/19/2016"
    ms.author="maheshu"/>

# <a name="enable-azure-ad-domain-services"></a>Activar os serviços de domínio do Azure AD

## <a name="task-3-enable-azure-ad-domain-services"></a>Tarefa 3: Activar os serviços de domínio do Azure AD
Nesta tarefa, ativar serviços de domínio do Azure AD para o seu diretório. Execute os seguintes passos de configuração para activar os serviços de domínio do Azure AD para o seu diretório.

1. Navegue para o **portal clássica Azure** ([https://manage.windowsazure.com](https://manage.windowsazure.com)).

2. Selecione o nó **Do Active Directory** no painel da esquerda.

3. Selecione o inquilino do Azure AD (directório) para o qual pretende ativar serviços de domínio do Azure AD.

    ![Selecione diretório do Azure AD](./media/active-directory-domain-services-getting-started/select-aad-directory.png)

4. Clique no separador **Configurar** .

    ![Configurar o separador de diretório](./media/active-directory-domain-services-getting-started/configure-tab.png)

5. Desloque-se para baixo até uma secção intitulado **dos serviços de domínio**.

    ![Secção de configuração dos serviços de domínio](./media/active-directory-domain-services-getting-started/domain-services-configuration.png)

6. Ativar/desativar a opção intitulada **Ativar serviços de domínio para este diretório** como **Sim**. Que existam alguns mais opções de configuração para serviços de domínio do Azure AD aparecem na página.

    ![Ativar serviços de domínio](./media/active-directory-domain-services-getting-started/enable-domain-services.png)

    > [AZURE.NOTE] Quando ativa a serviços de domínio do Azure AD do seu inquilino, Azure AD gera e armazena os hashes credencial Kerberos e NTLM que são necessários para autenticar utilizadores.

7. Especifique o **nome de domínio DNS dos serviços de domínio**.

   - O nome de domínio predefinido do directório (ou seja, terminando a **. onmicrosoft.com** sufixo de domínio) está selecionada por predefinição.

   - A lista contém todos os domínios que tenham sido configurados para o seu diretório do Azure AD – incluindo verificado, bem como não verificados domínios que configurar no separador 'Domains'.

   - Para além disso, também pode escrever um nome de domínio personalizado. Neste exemplo, vamos ter escrito num nome de domínio personalizado 'contoso100.com'.

     > [AZURE.WARNING] Certifique-se de que o prefixo de domínio do nome do domínio que especificar (por exemplo, contoso100 no nome de domínio 'contoso100.com') é menos de 15 carateres. Não é possível criar um domínio de serviços de domínio do Azure AD com um prefixo de domínio mais de 15 carateres.

8. Certifique-se de que o nome de domínio DNS que escolheu para o domínio gerido não já existe na rede virtual. Especificamente, verifique se:

   - já tem um domínio com o mesmo nome de domínio DNS da rede virtual.

   - a rede virtual que selecionou tem uma ligação VPN com a sua rede no local e tiver um domínio com o mesmo nome de domínio DNS na sua rede no local.

   - tem um serviço de nuvem existente com o mesmo nome da rede virtual.

9. O passo seguinte é selecionar uma rede virtual na qual gostaria serviços de domínio do Azure AD para estar disponível. Selecione a rede virtual e sub-rede dedicada que criou no menu pendente intitulado **Serviços de domínio de ligar para esta rede virtual**.

   - Certifique-se de que a rede virtual que especificou pertence para uma região Azure suportada pelos serviços de domínio do Azure AD. Referem-se até à página de [Azure serviços por região](https://azure.microsoft.com/regions/#services/) saber as regiões Azure na qual dos serviços de domínio do Azure AD está disponível.

   - Redes virtuais que pertencem a uma região onde não é suportado dos serviços de domínio do Azure AD não aparecer na lista pendente.
   
   - Utilize uma sub-rede dedicada no interior da rede virtual para serviços de domínio do Azure AD. Certifique-se de que não selecione a sub-rede de gateway. Consulte o artigo [Considerações de redes](active-directory-ds-networking.md). 

   - Da mesma forma, redes virtuais que foram criadas utilizando o Gestor de recursos do Azure não aparecem na lista pendente. Redes virtuais com base no Gestor de recursos não são atualmente suportadas pelos serviços de domínio do Azure AD.

10. Para ativar os serviços de domínio do Azure AD, clique em **Guardar** a partir do painel de tarefas na parte inferior da página.

11. A página apresenta um ' pendentes... ' Estado, enquanto estiver a ser activado serviços de domínio do Azure AD para o seu diretório.

    ![Activar os serviços de domínio - pendentes Estado](./media/active-directory-domain-services-getting-started/enable-domain-services-pendingstate.png)

    > [AZURE.NOTE] Serviços de domínio do AD Azure fornece elevada disponibilidade para o seu domínio gerido. Após ativar serviços de domínio do Azure AD, repare que os endereços IP que estão disponíveis na apresentação de rede virtual para cima um a um dos serviços de domínio. O endereço IP segundo é apresentado brevemente, como o mais cedo o serviço permite elevada disponibilidade para o seu domínio. Quando elevada disponibilidade está configurada e ativa para o seu domínio, deverá ver dois endereços IP na secção do separador **Configurar** **Serviços de domínio** .

12. Depois de cerca de 20-30 minutos, consulte o primeiro endereço IP no qual está disponível na sua rede virtual no campo **endereço IP** na página **Configurar** serviços de domínio.

    ![Serviços de domínio activados - IP primeiro aprovisionado](./media/active-directory-domain-services-getting-started/domain-services-enabled-firstdc-available.png)

13. Quando elevada disponibilidade está operacional para o seu domínio, verá duas endereços IP apresentados na página. O domínio gerido está disponível na sua rede virtual selecionado nestes dois endereços IP. Anote os endereços IP para que pode atualizar as definições de DNS para a sua rede virtual. Este passo permite máquinas virtuais da rede virtual para ligar para o domínio para operações, tais como a associação de domínio.

    ![Serviços de domínio activados - ambos os IPs aprovisionado](./media/active-directory-domain-services-getting-started/domain-services-enabled-bothdcs-available.png)

> [AZURE.NOTE] Dependendo do tamanho do seu inquilino do Azure AD (número de utilizadores, grupos, etc.), sincronização para o seu domínio gerido demora tempo. Este processo de sincronização acontece em segundo plano. Para grandes inquilinos com dezenas de milhares de objetos, poderá demorar um ou dois para todos os utilizadores, os membros do grupo e as credenciais a ser sincronizada dias.

<br>

## <a name="task-4---update-dns-settings-for-the-azure-virtual-network"></a>Tarefa 4 - atualizar definições de DNS para a rede virtual Azure
É a próxima tarefa de configuração de [atualizar as definições de DNS para a rede virtual Azure](active-directory-ds-getting-started-dns.md).
