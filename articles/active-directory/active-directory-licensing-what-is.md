<properties
    pageTitle="O que é de licenciamento do Microsoft Azure Active Directory? | Microsoft Azure"
    description="Descrição do licenciamento do Microsoft Azure Active Directory do, como funciona, como começar a e as melhores práticas, incluindo o Office 365, Microsoft Intune e Azure Active Directory Premium e edições básicas"
    services="active-directory"
      keywords="Licenciamento do Azure AD"
    documentationCenter=""
    authors="curtand"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="08/23/2016"
    ms.author="curtand"/>

# <a name="what-is-microsoft-azure-active-directory-licensing"></a>O que é de licenciamento do Microsoft Azure Active Directory?

##<a name="description"></a>Descrição
Azure Active Directory (Azure AD) está a identidade da Microsoft como uma solução de serviço (IDaaS) e a plataforma. Azure AD-lhe fornecido um número de versões funcionais e técnicas que vão do Azure AD livres, o que está disponível com qualquer serviço da Microsoft como o Office 365, Dynamics, Microsoft Intune e Azure (Azure AD não gerar quaisquer encargos consumo neste modo), para Azure AD paga versões tal como do conjunto de aplicações de mobilidade da empresa (EMS), Azure AD Premium e Basic, bem como Azure Multifator autenticação (MFA). Como muitos dos serviços online da Microsoft, mais Azure AD pago versões são entregues através de direitos de por utilizador, como se encontram no Office 365, Microsoft Intune e Azure AD. Nestes casos, a compra de serviço é representada com um ou mais subscrições e cada subscrição inclui um número de pré-lançamento compra de licenças no inquilino do. Direitos de por utilizador são obtidos através de atribuição de licenças, criar uma ligação entre o utilizador e o produto, ativar os componentes de serviço para o utilizador e consumir uma das licenças pré-paga.

[Experimente premium do Azure AD agora.](https://portal.office.com/Signup/Signup.aspx?OfferId=01824d11-5ad8-447f-8523-666b0848b381&ali=1#0)

> [AZURE.NOTE] Portal de administração do Azure AD é uma parte do portal clássica Azure. Enquanto utilizar Azure AD não requer qualquer compras Azure, aceder a este portal requer uma subscrição do Azure active ou uma [subscrição de avaliação do Azure](https://azure.microsoft.com/pricing/free-trial/).

Para obter uma descrição geral abrangente das capacidades de serviço do Azure AD, consulte o artigo [o que é o Azure AD](active-directory-whatis.md).
[Saber mais sobre níveis de serviço do Azure AD](https://azure.microsoft.com/support/legal/sla/)

> [AZURE.NOTE]  Subscrições do Azure como pague são diferentes: enquanto também representada no seu diretório, destas subscrições ativar a criação de recursos Azure e mapeá-los para o seu método de pagamento. Neste caso, não existem contagens sem licença associadas à subscrição. Associação de utilizadores com a sua subscrição, o acesso dos utilizadores a gerir os recursos de subscrição, é obtida através de lhes conceder permissões para trabalhar com recursos Azure mapeados para a subscrição.


##<a name="how-does-azure-ad-licensing-work"></a>Como funciona o licenciamento do Azure AD?

Baseada na licença (baseado em direito) Azure AD de serviços de trabalho ao ativar uma subscrição do seu inquilino do Azure AD/serviço de diretório. Assim que a subscrição estiver ativa as capacidades de serviço podem ser geridas por administradores de serviço de diretório/e utilizadas por utilizadores licenciados.

Quando comprar ou ativar o conjunto de aplicações do Enterprise mobilidade, Azure AD Premium ou do Azure AD básicas, diretório da sua será atualizado com a subscrição, incluindo as suas período de validade e licenças pré-paga. As informações da subscrição, incluindo o estado, próximo evento de ciclo de vida e o número de licenças atribuídas ou disponíveis está disponível através do portal do Azure clássico no separador de licenças para a pasta específica. Isto é também melhor local para gerir as atribuições de licença.

Cada subscrição é constituído por uma ou mais planos do serviço, cada um mapeamento o nível de funcionalidade incluído ao tipo de serviço; Por exemplo, Azure AD Azure MFA, Microsoft Intune, Exchange Online ou SharePoint Online. Gestão de licenças do Azure AD não requer a gestão de nível de plano de serviço. Este é diferente do Office 365 qual se baseia deste modo configuração avançada para gerir o acesso a serviços incluídos. Azure AD depende na configuração do serviço, para activar funcionalidades e gerir permissões individuais.

Em geral, informações de subscrição do Azure AD são geridas através do portal clássico Azure, no separador de licenças para a pasta específica. Subscrições de AD Azure, com a exceção das Azure AD Premium, não mostram no portal do Office.

> [AZURE.IMPORTANT] Azure AD Premium e Basic, assim como subscrições do conjunto de aplicações do Enterprise mobilidade, estão limitados à sua aprovisionado diretório/inquilino. Subscrições não podem ser divididas entre directórios ou utilizadas para dão utilizadores em outros directórios. Mover uma subscrição entre directórios é possível mas requer submeter um bilhetes de suporte ou de cancelamento e compra novamente no caso de compras diretas.

> Ao comprar o Azure AD ou conjunto de aplicações do Enterprise mobilidade através de licenciamento em Volume activação de subscrição acontecerá automaticamente quando o contrato inclui outros serviços do Microsoft Online, por exemplo, Office 365.

Pagas Azure boca do diretório do intervalo de funcionalidades de AD. Alguns exemplos incluem:
- Com base no grupo atribuição até à aplicações, que é activada em aplicação específica que está a gerir.
- Avançadas e as capacidades da gestão personalizada grupo estão disponíveis na configuração do diretório ou dentro do grupo específico.
- Relatórios de segurança Premium estão no separador de criação de relatórios
- Deteção de aplicação de nuvem apresentado no portal do Azure em identidade.

###<a name="assigning-licenses"></a>Atribuir licenças
Enquanto a obtenção de uma subscrição é tudo o que precisa para configurar capacidades pagas, utilizar o Azure AD pago funcionalidades requer distribuir licenças aos indivíduos à direita. Em geral, qualquer utilizador que devem ter acesso a ou quem é gerido através de um Azure AD paga funcionalidade tem de ser atribuída uma licença. Uma atribuição de licenças é um mapeamento entre um utilizador e um serviço comprado, como o Azure AD Premium, Basic ou conjunto de aplicações do Enterprise mobilidade.

Gerir os utilizadores no seu diretório devem ter uma licença é simple. Pode ser feito ao atribuir a um grupo para criar regras de atribuição através do portal de administração do Azure AD ou ao atribuir licenças diretamente para a direita indivíduos através de um portal, PowerShell ou APIs. Ao atribuir licenças a um grupo, todos os membros do grupo forem atribuídos uma licença. Se os utilizadores estão adicionados ou removidos do grupo serem serão atribuídas ou removidos a licença adequada. Atribuição de grupo pode utilizar qualquer gestão de grupos disponível para si e é consistente com atribuição com base no grupo às aplicações. Utilizar esta abordagem, pode configurar regras de modo a que todos os utilizadores no seu diretório são automaticamente atribuídos, certifique-se de que todas as pessoas com o cargo adequado tem uma licença ou até mesmo delegar a decisão de outros gestores na organização.

Com a atribuição de licenças com base no grupo, qualquer utilizador em falta de uma localização de utilização serão herdam a localização do directório durante a atribuição. Esta localização pode ser alterada pelo administrador em qualquer altura. Em casos onde a atribuição automatizada falhou devido a um erro, as informações de utilizador nesse tipo de licença vão refletir as desse Estado.

##<a name="getting-started-with-azure-ad-licensing"></a>Introdução com o licenciamento do Azure AD

Introdução ao Azure AD é fácil; Pode sempre criar no diretório da sua como parte da inscrever-se para um avaliação de Azure gratuito. [Saiba mais sobre inscrever-se como uma organização](sign-up-organization.md). A seguinte pode ajudá-lo certifique-se de que o seu diretório melhor está alinhado com outros serviços da Microsoft que pode ser utilizado por outros ou está a planear consumir e os seus objetivos em obter o serviço.

Aqui estão algumas das melhores práticas:
- Se já estiver a utilizar qualquer um dos serviços organizacionais da Microsoft, que já tem de um diretório do Azure AD. Neste caso, deve continuar a utilizar o mesmo directório para outros serviços, para que a gestão de identidades core, incluindo aprovisionamento e híbrido, SSO, pode ser utilizada através dos serviços. Os utilizadores terão uma experiência de início de sessão único e terão a vantagem de funcionalidades mais ricas ao longo dos serviços. Como resultado, se decidir comprar uma Azure AD pago serviço para sua força de trabalho, recomendamos que utilize o mesmo directório para o fazer.
- Se está a planear utilizar Azure AD para um conjunto diferente de utilizadores (parceiros, clientes e assim sucessivamente), ou se optar por avaliar o Azure AD services e gostaria de fazê-lo na isolamento do seu serviço de produção ou se estiver à procura para configurar um ambiente do sandbox para os seus serviços, recomendamos que irá pela primeira vez que criar um novo directório através do portal clássico do Azure Azure. [Saiba mais sobre como criar um novo diretório do Azure AD no portal do Azure clássico](active-directory-licensing-directory-independence.md). O novo directório será criado com a sua conta, como um utilizador externo com permissões de administrador global. Quando iniciar sessão no portal do Azure clássico com esta conta, irá conseguirá ver este diretório e aceder a todas as tarefas de administração do diretório. Recomendamos que cria uma conta local com privilégios adequados para gerir outros serviços da Microsoft (que não está acessível através do portal do Azure clássico). [Saiba mais sobre como criar contas de utilizador no Azure AD](active-directory-create-users.md).

> [AZURE.NOTE] Azure AD suporta externos "utilizadores", que são contas de utilizador numa instância do Azure AD que foram criadas utilizando uma conta Microsoft (MSA) ou uma identidade do Azure AD a partir do diretório outra. Enquanto estamos ocupados prolongamento esta capacidade para todos os serviços organizacionais da Microsoft, do momento estas contas não são suportadas em alguns dos experiências dos serviços; Por exemplo, o portal de administração do Office 365 não suporta atualmente estes utilizadores. Como resultado, os utilizadores externos com contas do Microsoft não conseguir aceder portal de administração do Office 365 em todos os durante a utilizadores externos a partir de outros directórios Azure AD serão ignorados. Neste caso, apenas a conta de utilizador local, o Azure AD ou diretório do Office 365 onde o utilizador foi originalmente criado, que seria acessível através destes experiências.

Como indicado, Azure AD tem diferentes versões pagas. Estas versões têm algumas diferenças secundárias na sua disponibilidade de compra:


| Produto   | EA/VL     | Abrir  |   CSP |   Direitos de utilização do MPN  |   Comprar pacote de direta | Versão de avaliação |
|---|---|---|---|---|---|---|
| Conjunto de aplicações do Enterprise mobilidade |   X | X | X | X |  |      X |
| Azure AD Premium  | X | X | X |   | X | X |
| Azure AD Basic    | X | X | X | X |  <br /> |  <br />  |

###<a name="select-one-or-more-license-trials"></a>Selecione um ou mais tentativas de licença
 Em todos os casos, pode ativar uma subscrição de avaliação do Azure AD Premium ou para o conjunto de aplicações do Enterprise mobilidade selecionando a versão de avaliação específico que pretende no separador licenças no seu diretório. Qualquer versão de avaliação contém uma subscrição de 30 dias com 100 licenças.

![Planos de licença de avaliação do Azure Active Directory](./media/active-directory-licensing-what-is/trial_plans.png)

![Planos de licença de avaliação do conjunto de aplicações do Enterprise mobilidade](./media/active-directory-licensing-what-is/EMS_trial_plan.png)

![Planos de licença de avaliação ativa](./media/active-directory-licensing-what-is/active_license_trials.png)

###<a name="assign-licenses"></a>Atribuir licenças
Assim que a subscrição estiver ativa, deve atribuir uma licença a próprio e atualize o browser para se certificar de que está a ver todas as funcionalidades. O passo seguinte é atribuir licenças a utilizadores que terão de aceder ou serão incluídas no pago funcionalidades do Azure AD. Tal como mencionado acima em "Atribuir licenças", a melhor forma para fazer isto é identificar o grupo que representa a audiência pretendida e atribuí-lo a licença; desta forma, os utilizadores que são adicionados ou removidos do grupo sobre o ciclo de vida irão ser atribuídos a ou removidos de licença.

Para atribuir uma licença a um grupo ou que os utilizadores individuais, selecione o plano de licenças que pretende atribuir e clique em **atribuir** na barra de comandos.

![Planos de licença de avaliação ativa](./media/active-directory-licensing-what-is/assign_licenses.png)

Uma vez na caixa de diálogo atribuição para o plano selecionada, pode selecionar utilizadores e adicioná-los para a coluna **atribuir** à direita. Pode percorrer a lista de utilizadores ou procurar em vez de indivíduos específicos utilizando a está à procura de vidro no canto superior direito da grelha de utilizador. Para atribuir grupos, selecione "Grupos de" a partir do menu **Mostrar** e, em seguida, clique no botão de verificação à direita para atualizar as atribuições que são apresentadas.

![Atribuir licenças a grupos](./media/active-directory-licensing-what-is/assign_licenses_to_groups.png)

Agora pode procurar ou página através dos grupos e adicioná-los para a coluna **atribuir** da mesma forma. Pode utilizar estes para atribuir uma combinação de utilizadores e grupos numa única operação. Para concluir o processo de atribuição, clique no botão de verificação no canto inferior direito da página.

![Mensagem de progresso de atribuição de licenças](./media/active-directory-licensing-what-is/license_assignment_progress_message.png)

Quando um grupo é atribuído, os membros herdam as licenças de 30 minutos, mas normalmente dentro de minutos de 1-2.

Erros de atribuição podem ocorrer durante a atribuição de licenças do Azure AD, mas são relativamente raros. Os possíveis erros de atribuição são limitados ao livro:
- Atribuição um conflito de-quando um utilizador anteriormente tiver sido atribuído uma licença que é incompatível com a licença atual. Neste caso, a atribuir a licença nova irão necessitar remover anterior.
- Licenças disponíveis excedidas - quando o número de utilizadores no grupos atribuídos excede licenças disponíveis, estado da atribuição dos utilizadores vai refletir as uma falha de atribuir devido licenças em falta.

###<a name="view-assigned-licenses"></a>Ver atribuído licenças

Uma vista de resumo de licenças atribuídas, incluindo o evento de ciclo de vida de subscrição disponíveis, atribuídos e seguinte são apresentadas no separador **licenças** .

![Ver o número de licenças atribuídas](./media/active-directory-licensing-what-is/view_assigned_licenses.png)

Uma lista detalhada dos grupos e utilizadores atribuídos, incluindo o estado da atribuição e o caminho (direto ou herdado a partir de um ou mais grupos) está disponível ao navegar para um plano de licença.

![Visualização de detalhe de licenças atribuídas para um plano de licença](./media/active-directory-licensing-what-is/assigned_licenses_detail.png)

É tão fácil como atribuindo-lhes remover licenças. Se o utilizador está atribuído diretamente ou de um grupo do atribuídas, pode remover a licença ao selecionar o tipo de licença, selecionar **Remover**, adicionar à lista de remover o utilizador ou grupo e confirmar a ação. Em alternativa, pode abrir um tipo de licença, selecione o utilizador ou grupo específico e toque em **Remover** na barra de comandos. Para terminar a herança de um utilizador de uma licença de um grupo, basta remova o utilizador do grupo.

###<a name="extending-trials"></a>Expandir as versões de avaliação

Extensões de avaliação para clientes estão disponíveis como self-service através do portal do Office 365. Um administrador de cliente pode navegar para o [Portal do Office](https://portal.office.com/#Billing) (acesso depende permissões para o portal do Office) e selecione a sua versão de avaliação do Azure AD Premium. Clique na ligação **Expandir avaliação** e siga as instruções. É necessário introduzir um cartão de crédito, mas não será cobrado.

![Expandir uma versão de avaliação de licença no portal do Office](./media/active-directory-licensing-what-is/extend_license_trial.png)

Os clientes também podem pedir uma avaliação extensão ao submeter um pedido de suporte. Um administrador de cliente pode navegar para o portal do Office 365 [suportar a página](http://aka.ms/extendAADtrial) (acesso depende permissões para a página de suporte do Office). Nesta página, selecione "Subscrições e tentativas" em funcionalidades e "Versão de avaliação perguntas" em sintoma. Por fim, introduza informações sobre as circunstâncias

![Expandir uma versão de avaliação de licença utilizando um pedido de suporte](./media/active-directory-licensing-what-is/alternate_office_aad_trial_extension.png)

## <a name="next-steps"></a>Próximos passos

Agora poderá ser pronto para configurar e utilizar algumas funcionalidades Premium do Azure AD.

- [Reposição personalizada de palavra-passe](active-directory-manage-passwords.md)
- [Gestão de grupos de gestão personalizada](active-directory-accessmanagement-self-service-group-management.md)
- [Densidade do Azure AD Connect](active-directory-aadconnect-health.md)
- [Atribuição de grupo para aplicações](active-directory-manage-groups.md)
- [Autenticação Multifator Azure](../multi-factor-authentication/multi-factor-authentication.md)
- [Direta compra de licenças do Azure AD Premium](http://aka.ms/buyaadp)
