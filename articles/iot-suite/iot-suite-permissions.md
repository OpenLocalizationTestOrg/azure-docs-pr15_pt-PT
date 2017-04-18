<properties
  pageTitle="Conjunto de aplicações do Azure IoT e Azure Active Directory | Microsoft Azure"
  description="Descreve como o conjunto de aplicações do Azure IoT utiliza o Azure Active Directory para gerir as permissões."
  services=""
  suite="iot-suite"
  documentationCenter=""
  authors="aguilaaj"
  manager="timlt"
  editor=""/>

<tags
  ms.service="iot-suite"
  ms.devlang="na"
  ms.topic="article"
  ms.tgt_pltfrm="na"
  ms.workload="na"
  ms.date="10/24/2016"
  ms.author="araguila"/>
  
# <a name="permissions-on-the-azureiotsuitecom-site"></a>Permissões no site de azureiotsuite.com

## <a name="what-happens-when-you-sign-in"></a>O que acontece quando iniciar sessão

Quando inicia sessão na [azureiotsuite.com][lnk-azureiotsuite], o site determina os níveis de permissões que tiver baseado no inquilino do Azure Active Directory (AAD) atualmente selecionada e subscrição Azure.

1.  O site pela primeira vez localiza do Azure que inquilinos AAD que pertence para preencher a lista de inquilinos visto junto ao seu nome de utilizador com sessão iniciada. Neste momento, o site só pode obter tokens de utilizador para um inquilino cada vez. Como resultado, ao mudar para um inquilino diferentes utilizando a lista pendente no canto superior direito, o site novamente os registos em que se encontra a esse inquilino para obter os tokens para esse inquilino.

2.  Em seguida, o site localiza do Azure quais as subscrições que tenha associado ao inquilino selecionado. Consulte as subscrições disponíveis quando cria uma nova solução pré-configurado.

3.  Por fim, o site obtém todos os recursos nos grupos do recurso e subscrições marcadas como soluções pré-configurado e preenche os mosaicos na home page.

As seguintes secções descrevem as funções que controlam o acesso para as soluções pré-configurado.

## <a name="aad-roles"></a>Funções AAD

As funções AAD controlam as soluções de aprovisionar pré-configurado capacidade e gerir utilizadores numa solução pré-configurado.

Pode encontrar mais informações sobre as funções de administrador no AAD no [artigo atribuir funções de administrador no Azure AD][lnk-aad-admin], mas este artigo foca principalmente o **Administrador Global** e as funções de **Membro e utilizadores domínio** como é utilizado pelas soluções pré-configurado.

**Administrador global:** Pode ser muitos administradores globais por inquilino AAD. Quando cria um inquilino do AAD, está por predefinição o administrador global desse inquilino. O administrador global pode aprovisionar o uma solução pré-configurado e é atribuído uma função de **administrador** para a aplicação de dentro do seu inquilino AAD. No entanto, se outro utilizador ao mesmo inquilino AAD cria uma aplicação, a função predefinida que o administrador global é concedido é **Só de leitura implícito**. Os administradores globais podem atribuir funções para aplicações utilizando o [portal clássica Azure][lnk-classic-portal].

**Membro e utilizadores domínio:** Pode haver vários utilizadores/membros do domínio por inquilino AAD. Um utilizador do domínio pode aprovisionar o uma solução pré-configurado através de [azureiotsuite.com] [ lnk-azureiotsuite] site. A função predefinida que lhes são concedidas para a aplicação de que aprovisionar o que é **administrador**. Que possam criar uma aplicação utilizando o script build.cmd na [monitorização de azure iot remote] [ lnk-rm-github-repo] ou [azure-iot-aspeto do Office-manutenção] [ lnk-pm-github-repo] repositório, mas a função predefinida que lhes são concedidas é **Só de leitura implícito**, tal como não possui permissão para atribuir funções. Se o outro utilizador no inquilino AAD cria uma aplicação, estão atribuídos a função **Só de leitura implícito** por predefinição para essa aplicação. Não possui a capacidade de atribuir funções para aplicações; Por conseguinte, não pode adicionar utilizadores ou funções de utilizadores para uma aplicação do mesmo se estes aprovisionado-lo.

**Convidado utilizador/convidado:** Pode ser convidado muitos utilizadores/convidados por inquilino AAD. Utilizadores convidados tem um conjunto de direitos limitado no inquilino AAD. Como resultado, os utilizadores convidados não é possível aprovisionar uma solução pré-configurado no inquilino AAD.

Para obter mais informações, consulte os seguintes recursos:

- [Criar ou editar utilizadores no Azure AD][lnk-create-edit-users]
- [Atribuir funções de aplicação no AAD][lnk-assign-app-roles]

## <a name="azure-subscription-administrator-roles"></a>Funções de administrador de subscrição Azure

As funções de administrador Azure controlam a capacidade de mapear uma subscrição do Azure para um inquilino do AD.

Pode saber mais sobre as funções de administrador cocriação Azure, o administrador de serviços e o administrador de conta no artigo [como adicionar ou alterar administrador cocriação Azure, o administrador de serviços e o administrador de conta][lnk-admin-roles].

## <a name="application-roles"></a>Funções da aplicação

As funções da aplicação controlam o acesso a dispositivos na sua solução pré-configurado.

Existem duas definidos e uma função implícita definida na aplicação que é criada quando aprovisionar o uma solução pré-configurado.

-   **Administrador:** Tem controlo total para adicionar, gerir e remova os dispositivos

-   **Só de leitura:** Tem a capacidade de ver dispositivos

-   **Implícito só de leitura:** Este é o mesmo como só de leitura, mas é concedida a todos os utilizadores do seu inquilino AAD. Foi feito para sua comodidade durante o desenvolvimento. Pode remover esta função, alterando o [RolePermissions.cs] [ lnk-resource-cs] ficheiro de origem.

### <a name="changing-application-roles-for-a-user"></a>Alterar funções de aplicação para um utilizador

Pode utilizar o procedimento seguinte para tornar um utilizador no Active Directory administrador da sua solução pré-configurado.

Tem de ser um administrador global AAD para alterar funções para um utilizador:

1. Aceda ao [portal clássica Azure][lnk-classic-portal].

2. Selecione **do Active Directory**.

3. Clique no nome do seu inquilino do AAD (este é o diretório que escolheu azureiotsuite.com quando a sua solução aprovisionado).

4. Clique em **aplicações**.

5. Clique no nome da aplicação que corresponde ao seu nome de solução pré-configurado. Se não vir a aplicação na lista, mude de lista pendente **Mostrar** para baixo para **as aplicações do proprietário da minha empresa** e clique na marca de verificação.

7. Clique em **utilizadores**.

8. Selecione o utilizador ao que qual pretende mudar funções.

9. Clique em **atribuir** e selecione a função (como **administrador**) que pretende atribuir ao utilizador, clique na marca de verificação.

## <a name="faq"></a>PERGUNTAS MAIS FREQUENTES

### <a name="im-a-service-administrator-and-id-like-to-change-the-directory-mapping-between-my-subscription-and-a-specific-aad-tenant-how-do-i-do-this"></a>Sou um administrador do serviço e gostaria de alterar o mapeamento de diretório entre a minha subscrição e um inquilino AAD específico. Como fazer isto?

1. Aceda ao [portal clássica Azure][lnk-classic-portal], clique em **Definições** , na lista de serviços no lado esquerdo.

2. Selecione a subscrição que gostaria de alterar o mapeamento de diretório para.

3. Clique em **Editar diretório**.

4. Selecione o **diretório** que pretende utilizar na lista pendente. Clique na seta reencaminhar.

5. Confirme o mapeamento de diretório e afetadas coadministradores. Tenha em atenção que, se está a mover a partir do diretório de outra, todos os administradores a partir do directório original são removidos.

### <a name="im-a-domain-usermember-on-the-aad-tenant-and-ive-created-a-preconfigured-solution-how-do-i-get-assigned-a-role-for-my-application"></a>Estou utilizador/membro de um domínio no inquilino de AAD e criou uma solução pré-configurado. Como posso obter foi atribuído uma função da minha aplicação?

Peça a um administrador global para lhe atribuir como um administrador global no inquilino de AAD para obter permissões para atribuir funções aos utilizadores a mesmo ou peça a um administrador global para lhe atribuir uma função. Se gostaria de alterar o inquilino AAD a solução pré-configurado foi implementada, consulte a pergunta seguinte.

### <a name="how-do-i-switch-the-aad-tenant-my-remote-monitoring-preconfigured-solution-and-application-are-assigned-to"></a>Como posso alternar entre o inquilino AAD que são atribuídos a minha remota solução pré-configurado monitorização e a aplicação para?

Pode executar uma implementação nuvem a partir do <https://github.com/Azure/azure-iot-remote-monitoring> e implementar novamente com um inquilino AAD recentemente criado. Dado que são por predefinição, um administrador global quando cria um novo inquilino AAD, terá acesso a adição de utilizadores e atribuir funções para esses utilizadores.

1. Criar um novo directório AAD no [portal de gestão do Azure][lnk-classic-portal].

2. Aceda a <https://github.com/Azure/azure-iot-remote-monitoring>.

3. Executar `build.cmd cloud [debug | release] {name of previously deployed remote monitoring solution}` (por exemplo, `build.cmd cloud debug myRMSolution`)

4. Quando lhe for pedido, defina o **tenantid** para ser o seu novo inquilino criado em vez do seu inquilino do anterior.


### <a name="i-want-to-change-a-service-administrator-or-co-administrator-when-logged-in-with-an-organisational-account"></a>Pretendo alterar um administrador de serviços ou um administrador de cocriação quando tem sessão iniciada com uma conta organizacional

Consulte o artigo de suporte [alterar administrador de serviços e cocriação de administrador quando tem sessão iniciada com uma conta organizacional][lnk-service-admins].

### <a name="why-am-i-seeing-this-error-your-account-does-not-have-the-proper-permissions-to-create-a-solution-please-check-with-your-account-administrator-or-try-with-a-different-account"></a>Por que razão estou a ver este erro? "A sua conta não possui as permissões adequadas para criar uma solução. Verifique com o seu administrador de conta ou tente com uma conta diferente."

Veja o diagrama abaixo:

![][img-flowchart]

> [AZURE.NOTE] Se continuar a ver o erro depois de validar são um administrador global no inquilino de AAD e um administrador de cocriação da subscrição, solicite ao administrador de conta, remova o utilizador e voltar a atribuir as permissões necessárias pela seguinte ordem: adicionar o utilizador como administrador global e, em seguida, adicione o utilizador como administrador de cocriação da subscrição Azure. Se os problemas persistirem, contacte o suportehttp [Ajuda e suporte][lnk-help-support].

**Por que razão estou a ver este erro quando tenho uma subscrição do Azure?** *Uma subscrição do Azure é necessária para criar soluções pré-configurada. Pode criar uma conta de avaliação gratuita apenas de duas minutos.*

Se tem a certeza de que já tenha uma subscrição Azure validar o inquilino em mapear para a sua subscrição e certifique-se de que o inquilino correto está seleccionado na lista pendente. Se tiver validado inquilino pretendido está correto, siga o diagrama acima e validar o mapeamento da sua subscrição e deste inquilino AAD.

## <a name="next-steps"></a>Próximos passos

Para continuar a obter informações sobre IoT conjunto de aplicações, consulte o artigo como pode [Personalizar uma solução pré-configurado][lnk-customize].

[img-flowchart]: media/iot-suite-permissions/flowchart.png

[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-rm-github-repo]: https://github.com/Azure/azure-iot-remote-monitoring
[lnk-pm-github-repo]: https://github.com/Azure/azure-iot-predictive-maintenance
[lnk-aad-admin]: https://azure.microsoft.com/documentation/articles/active-directory-assign-admin-roles/
[lnk-classic-portal]: https://manage.windowsazure.com/
[lnk-create-edit-users]: https://azure.microsoft.com/documentation/articles/active-directory-create-users/
[lnk-assign-app-roles]: https://azure.microsoft.com/documentation/articles/active-directory-application-manifest/
[lnk-service-admins]: https://azure.microsoft.com/support/changing-service-admin-and-co-admin/
[lnk-admin-roles]: https://azure.microsoft.com/documentation/articles/billing-add-change-azure-subscription-administrator/
[lnk-resource-cs]: https://github.com/Azure/azure-iot-remote-monitoring/blob/master/DeviceAdministration/Web/Security/RolePermissions.cs
[lnk-help-support]: https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade
[lnk-customize]: iot-suite-guidance-on-customizing-preconfigured-solutions.md
