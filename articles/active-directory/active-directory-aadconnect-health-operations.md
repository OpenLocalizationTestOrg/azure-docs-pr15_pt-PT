<properties
    pageTitle="Ligação do Azure AD operações de estado de funcionamento."
    description="Este artigo descreve operações adicionais que podem ser executadas depois de ter implementado Azure AD ligar-se do Estado de funcionamento."
    services="active-directory"
    documentationCenter=""
    authors="karavar"
    manager="samueld"
    editor="curtand"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="vakarand"/>

# <a name="azure-ad-connect-health-operations"></a>Operações de estado de funcionamento de ligação do Azure AD

O tópico seguinte descreve várias operações que podem ser executadas com o Azure AD ligar-se do Estado de funcionamento.

## <a name="enable-email-notifications"></a>Ativar notificações de correio eletrónico
Pode configurar o Azure AD ligar estado de funcionamento do serviço para enviar notificações de correio eletrónico quando alertas são gerados a indicar que a sua infraestrutura de identidade não está em bom estada. Isto ocorrerá quando é gerado um alerta, bem como quando está marcado como resolvido. Siga as instruções abaixo para configurar notificações de correio eletrónico.

![Mensagem de correio electrónico do Estado de funcionamento de ligação do Azure AD descobrir de notificação](./media/active-directory-aadconnect-health/email_noti_discover.png)

>[AZURE.NOTE] Notificações de correio eletrónico estão desativadas por predefinição.


### <a name="to-enable-azure-ad-connect-health-email-notifications"></a>Para ativar o Azure AD ligar-se do Estado de funcionamento notificações de correio eletrónico

1. Abra o pá alertas para o serviço para o qual pretende receber uma notificação de e-mail.
2. Clique no botão "Definições de notificação" a partir da barra de ação.
3. Ative o parâmetro de notificação de E-Mail para Ativado.
4. Selecione a caixa de verificação para configurar todos os administradores globais para receber notificações de correio eletrónico.
5. Se pretender receber notificações de e-mail em outros endereços de correio eletrónico, pode especificá-las na caixa destinatário de correio eletrónico adicional. Para remover um endereço de e-mail a partir desta lista, clique com o botão direito do rato sobre a entrada e selecione eliminar.
6. Para finalizar as alterações clique em "Guardar". Todas as alterações irão demorar efeitos apenas depois de clicar em "Guardar".

## <a name="delete-a-server-or-service-instance"></a>Eliminar uma instância de servidor ou serviço

### <a name="delete-a-server-from-azure-ad-connect-health-service"></a>Eliminar um servidor de serviço de estado de funcionamento do ligar Azure AD
Em alguns casos, poderá pretender remover um servidor de que está a ser controlado. Siga as instruções abaixo para remover um servidor partir Azure AD ligar-se do Estado de funcionamento do serviço.

Quando eliminar um servidor, tenha em atenção o seguinte procedimento:

- Esta ação irá INTERROMPER recolher quaisquer dados mais do que o servidor. Este servidor será removido do serviço de monitorização. Após esta ação, não será capaz de ver o novas alertas, monitorização ou utilização de dados de analytics para este servidor.
- Esta ação não irá desinstalar ou remover o agente de estado de funcionamento do seu servidor. Se não tiver desinstalado o estado de funcionamento do agente antes de efetuar este passo, poderá ver eventos de erro no servidor relacionadas com o agente de estado de funcionamento.
- Esta ação não irá eliminar os dados recolhidos já deste servidor. Os dados serão eliminados de acordo com a política de retenção de dados do Microsoft Azure.
- Após efetuar esta ação, se pretender iniciar a monitorização de mesmo servidor novamente, terá de desinstalar e voltar a instalar o agente de estado de funcionamento neste servidor.


#### <a name="to-delete-a-server-from-azure-ad-connect-health-service"></a>Para eliminar um servidor de serviço de estado de funcionamento do ligar Azure AD

Ligação do Azure AD estado de funcionamento para o AD FS e Azure AD (sincronização) da ligação:

1. Abra a pá servidor a partir do pá da lista de servidor selecionando o nome do servidor para ser removido.
2. No pá Server, clique no botão "Delete" a partir da barra de ação.
3. Confirme a ação para eliminar o servidor ao escrever o nome do servidor na caixa de confirmação.
4. Clique no botão "Delete".

Ligação do Estado de funcionamento do Azure AD para o AD DS:

1. Abra o dashboard controladores de domínio.
2. Selecione o controlador de domínio para ser removido.
3. Clique no botão "Eliminar seleccionada" a partir da barra de ação.
4. Confirme a ação para eliminar o servidor.
5. Clique no botão "Delete".

### <a name="delete-a-service-instance-from-azure-ad-connect-health-service"></a>Eliminar uma instância do serviço do serviço de estado de funcionamento do ligar Azure AD

Em alguns casos, poderá pretender remover uma instância do serviço. Siga as instruções abaixo para remover uma instância do serviço a partir Azure AD ligar-se do Estado de funcionamento do serviço.

Ao eliminar uma instância do serviço, tenha em atenção o seguinte procedimento:

- Esta ação irá remover a instância atual do serviço do serviço de monitorização.
- Esta ação não irá desinstalar ou remover o estado de funcionamento do agente a partir de qualquer um dos servidores que foram monitorizados como parte desta instância de serviço. Se não tiver desinstalado o estado de funcionamento do agente antes de efetuar este passo, poderá ver eventos de erro no servidor (es) relacionados com o agente de estado de funcionamento.
- Todos os dados desta instância de serviço serão eliminados de acordo com a política de retenção de dados do Microsoft Azure.
- Após efetuar esta ação, se pretender começar a monitorizar o serviço, desinstale e voltar a instalar o agente de estado de funcionamento em todos os servidores que será monitorizada. Após efetuar esta ação, se pretender iniciar a monitorização de mesmo servidor novamente, terá de desinstalar e voltar a instalar o agente de estado de funcionamento neste servidor.


#### <a name="to-delete-a-service-instance-from-azure-ad-connect-health-service"></a>Para eliminar uma instância do serviço do serviço de estado de funcionamento do ligar Azure AD

1. Abra a pá de serviço a partir do pá da lista de serviço ao selecionar o identificador de serviço (nome do farm de servidores) que pretende remover.
2. No pá Server, clique no botão "Delete" a partir da barra de ação.
3. Confirme o nome do serviço ao escrevê-la na caixa de confirmação. (por exemplo: sts.contoso.com)
4. Clique no botão "Delete".
<br><br>


[//]: # (Start of RBAC section)
## <a name="manage-access-with-role-based-access-control"></a>Gerir o acesso com baseado em funções de controlo de acesso
### <a name="overview"></a>Descrição geral
[Função de controlo de acesso com base](role-based-access-control-configure.md) para Azure AD ligar-se do Estado de funcionamento oferece um acesso serviço do Azure AD ligar-se do Estado de funcionamento para utilizadores e/ou grupos fora administradores globais. Isto é realizado pelos atribuir funções de aos grupos de e\ou utilizadores pretendidos e fornece um mecanismos para limitar os administradores globais no seu diretório.

#### <a name="roles"></a>Funções
Azure AD ligar estado de funcionamento do suporta as seguintes funções incorporadas.

| Função | Permissões |
| ----------- | ---------- |
| Proprietário | Os proprietários de podem ***Gerir o acesso*** (por exemplo, função atribuir a um utilizador/grupo), ***Ver todas as informações*** (por exemplo, os alertas de vista) a partir do portal e ***alterar as definições*** (por exemplo, notificações de correio eletrónico) dentro do Azure AD ligar-se do Estado de funcionamento. <br>Por predefinição, os administradores globais do Azure AD são atribuídos esta função e esta não pode ser alterada.  |
|Contribuinte|  Os contribuintes podem ***Ver todas as informações*** (por exemplo, os alertas de vista) a partir do portal e ***alterar as definições*** (por exemplo, notificações de correio eletrónico) dentro das Azure AD ligar-se do Estado de funcionamento.|
|Leitor| Leitores podem ***Ver todas as informações*** (por exemplo, os alertas de vista) a partir do portal do Azure AD ligar-se do Estado de funcionamento.|

Todas as outras funções (como 'Administradores de acesso do utilizador' ou 'DevTest Labs utilizadores'), mesmo se estiver disponível na experiência portal, têm sem impacto para aceder aos Azure AD ligar-se do Estado de funcionamento.

#### <a name="access-scope"></a>Âmbito do Access

Ligação do Azure AD suporta gerir acesso em dois níveis:

- ***Todas as instâncias de serviço***: Este é o caminho recomendado para a maioria dos clientes e controla o acesso de todas as instâncias de serviço (por exemplo, um farm ADFS) ao longo de todos os tipos de funções que estão a ser monitorizados pelo Azure AD ligar-se do Estado de funcionamento.

- ***A instância do serviço***: em alguns casos, poderá ter de segregar acesso com base em tipos de funções ou através de uma instância do serviço. Neste caso, pode gerir o acesso ao nível da instância do serviço.  

É concedida permissão se um utilizador final tem acesso quer ao nível ou instância do serviço de diretório.


### <a name="how-to-allow-users-or-groups-access-to-azure-ad-connect-health"></a>Como permitir o acesso de utilizadores ou grupos para Azure AD ligar-se do Estado de funcionamento
#### <a name="steps-1-select-the-appropriate-access-scope"></a>Os passos de 1: Selecionar o âmbito de acesso adequado
Para permitir que um utilizador acesso ao nível de *todas as instâncias de serviço* do Azure AD ligar-se do Estado de funcionamento, abra o pá principal no Azure AD ligar-se do Estado de funcionamento.<br>
#### <a name="step-2-add-users-groups-and-assign-roles"></a>Passo 2: Adicionar utilizadores, grupos e atribuir funções
1. Clique na peça "Utilizadores" a partir da secção de configurar.<br>
![Estado de funcionamento RBAC principal pá ligação do Azure AD](./media/active-directory-aadconnect-health/RBAC_main_blade.png)
2. Selecione "Adicionar"
3. Selecione a "função" tal como "Proprietário"<br>
![Estado de funcionamento de ligação do Azure AD RBAC adicionar utilizador](./media/active-directory-aadconnect-health/RBAC_add.png)
4. Escreva o nome ou o identificador do alvo de utilizador ou grupo. Pode selecionar uma ou mais utilizadores ou grupos ao mesmo tempo. Clique em "select".
![Ligação do Azure AD do Estado de funcionamento RBAC Seleccionar utilizador](./media/active-directory-aadconnect-health/RBAC_select_users.png)
5. Selecione "Ok".<br>

6. Uma vez concluída a atribuição de funções, a utilizadores e grupos ou irão aparecer na lista.<br>
![Lista de utilizadores do Estado de funcionamento RBAC ligação do Azure AD](./media/active-directory-aadconnect-health/RBAC_user_list.png)

Estes passos permite que os utilizadores listados e o acesso do grupo de acordo com as respectivas funções atribuídas.
>[AZURE.NOTE]
- Os administradores globais tem sempre acesso total a todas as operações mas contas de administrador global não será apresentadas na lista acima.
- Funcionalidade "Convidar utilizadores" não é suportada no Azure AD ligar-se do Estado de funcionamento.

#### <a name="step-3-share-the-blade-location-with-users-or-groups"></a>Passo 3: Partilhar a localização de pá com utilizadores ou grupos
1. Depois de atribuir permissões, um utilizador pode aceder Azure AD ligar-se do Estado de funcionamento ao aceder a [http://aka.ms/aadconnecthealth](http://aka.ms/aadconnecthealth).
2. Uma vez no pá, o utilizador pode afixar a pá ou diferentes partes ao dashboard ao basta clicar em "Afixar ao dashboard"<br>
![Pá de pin do Azure AD ligar-se do Estado de funcionamento RBAC](./media/active-directory-aadconnect-health/RBAC_pin_blade.png)


>[AZURE.NOTE] Um utilizador com a função de "Leitor" não conseguir executar a operação "Criar" para obter a extensão do Azure AD ligar-se do Estado de funcionamento do Azure Marketplace. Este utilizador conseguem aceder à pá ao aceder à ligação acima. Para uma utilização subsequente, o utilizador pode afixar pá ao dashboard.

### <a name="remove-users-andor-groups"></a>Remover utilizadores e/ou grupos
Pode remover um utilizador ou grupo adicionado à parte do Azure AD ligar-se do Estado de funcionamento função com base no controlo de acesso à direita clicando em e selecionar remover.<br>
![Estado de funcionamento RBAC remover utilizador ligação do Azure AD](./media/active-directory-aadconnect-health/RBAC_remove.png)

[//]: # (End of RBAC section)

## <a name="related-links"></a>Ligações relacionadas

* [Estado de funcionamento de ligação do Azure AD](active-directory-aadconnect-health.md)
* [Ligação do Azure AD instalação do agente de estado de funcionamento](active-directory-aadconnect-health-agent-install.md)
* [Utilizar o Azure AD ligar-se do Estado de funcionamento com o AD FS](active-directory-aadconnect-health-adfs.md)
* [Utilizar o Azure AD ligar-se do Estado de funcionamento para a sincronização](active-directory-aadconnect-health-sync.md)
* [Utilizar o Azure AD ligar-se do Estado de funcionamento com o AD DS](active-directory-aadconnect-health-adds.md)
* [Perguntas mais frequentes do Estado de funcionamento de ligação do Azure AD](active-directory-aadconnect-health-faq.md)
* [Histórico de versões do Estado de funcionamento de ligação do Azure AD](active-directory-aadconnect-health-version-history.md)
