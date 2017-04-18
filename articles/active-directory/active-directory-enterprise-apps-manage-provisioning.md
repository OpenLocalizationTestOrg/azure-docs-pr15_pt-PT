<properties
    pageTitle="Aprovisionamento de gestão de aplicações empresariais na pré-visualização do Azure Active Directory do utilizador | Microsoft Azure"
    description="Saiba como gerir o aprovisionamento de conta de utilizador para aplicações empresariais com a pré-visualização do Azure Active Directory"
    services="active-directory"
    documentationCenter=""
    authors="asmalser"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="09/12/2016"
    ms.author="asmalser"/>

#<a name="preview-managing-user-account-provisioning-for-enterprise-apps-in-the-new-azure-portal"></a>Pré-visualização: Gerir a conta de utilizador de aprovisionamento para aplicações empresariais no novo portal do Azure

Este artigo descreve como utilizar o [Azure portal](https://portal.azure.com) para gerir a conta de utilizador automáticas de aprovisionamento e anular aprovisionamento para aplicações que suportem-la e, sobretudo, fases, que foram adicionadas na categoria "em destaque" da [Galeria de aplicação do Azure Active Directory](active-directory-appssoaccess-whatis.md#get-started-with-the-azure-ad-application-gallery). Esta experiência de gestão no novo portal do Azure está atualmente na pré-visualização público e este artigo descreve as novas funcionalidades, bem como algumas limitações temporárias que estão no local durante o período de pré-visualização. [Qual é a pré-visualização?](active-directory-preview-explainer.md)

Para saber mais sobre o aprovisionamento de conta de utilizador automática e como funciona, consulte o artigo [automatizar aprovisionamento de utilizador e Deprovisioning SaaS aplicações com o Azure Active Directory](active-directory-saas-app-provisioning.md).

##<a name="finding-your-apps-in-the-new-portal"></a>Localizar as aplicações no novo portal

Partir Setembro de 2016, todas as aplicações que tenham sido configuradas para single sign-on num diretório, por um administrador do diretório através da [Galeria de aplicação do Azure Active Directory](active-directory-appssoaccess-whatis.md#get-started-with-the-azure-ad-application-gallery) no interior do [Azure portal clássico](https://manage.windowsazure.com), podem agora ser visualizadas e geridas no novo portal do Azure.

Podem encontrar estas aplicações na secção **Aplicações empresariais** do novo portal Azure, que pode ser acedido através do menu **Mais serviços** na área de navegação à esquerda. Aplicações empresariais são as aplicações que foram implementadas e estão a ser utilizadas pelos utilizadores na sua organização.

![Pá aplicações empresariais][0]

Selecionar a ligação de **todas as aplicações** à esquerda mostra uma lista de todas as aplicações que tenham sido configurados, incluindo as aplicações que tivessem sido adicionadas a partir da galeria. Selecionar uma aplicação carrega o pá recurso para essa aplicação, onde relatórios podem ser visualizados nessa aplicação e pode ser gerida uma variedade de definições.

Definições de aprovisionamento de conta de utilizador pode ser gerida selecionando **aprovisionar** à esquerda.

![Pá de recursos de aplicação][1]


##<a name="provisioning-modes"></a>Modos de aprovisionamento

O pá **aprovisionar** começa com um menu de **modo** que mostra que modos de aprovisionamento são suportados para uma aplicação empresarial e permite-los de ser configurado. As opções disponíveis incluem:

* **Automática** - esta opção é apresentada se Azure AD suporta baseadas em API aprovisionamento automático e/ou anular aprovisionamento de contas de utilizador para esta aplicação. Selecionar deste modo apresenta uma interface que os administradores através de configuração do Azure AD para ligar a gestão de utilizadores a aplicação API, criar mapeamentos de conta e fluxos de trabalho que definem como devem fluxo de dados de conta de utilizador entre Azure AD de guias de introdução e a aplicação e gerir o Azure AD aprovisionar serviço.

* **Manual** - esta opção é apresentada se Azure AD não suporta aprovisionamento automático de contas de utilizador para esta aplicação. Esta opção significa que os registos de conta de utilizador armazenados na aplicação tem de ser geridos utilizando um processo externo, com base nas capacidades de gestão e de aprovisionamento de utilizador fornecidas pela aplicação (que pode incluir SAML JIT aprovisionamento).


##<a name="configuring-automatic-user-account-provisioning"></a>Configurar o aprovisionamento de conta de utilizador automática

Selecionar a opção **automática** apresenta um ecrã que é dividido em quatro secções:

###<a name="admin-credentials"></a>Credenciais de administrador
Este é onde as credenciais necessárias para Azure AD ligar a gestão de utilizadores a aplicação API são introduzidos. A entrada obrigatória varia consoante a aplicação. Para saber mais sobre os tipos de credenciais e requisitos para aplicações específicas, consulte o [tutorial de configuração para essa aplicação específica](active-directory-saas-app-provisioning.md#list-of-apps-that-support-automated-user-provisioning).

Selecionar o botão **Testar ligação** permite-lhe testar as credenciais por ter Azure AD tentar ligar à aplicação do aprovisionamento aplicação com as credenciais fornecidas.

###<a name="mappings"></a>Mapeamentos
Este é onde os administradores podem ver e editar qual o fluxo de atributos de utilizador entre Azure AD e a aplicação de destino, quando contas de utilizador são aprovisionadas ou atualizadas.

Existe um conjunto de mapeamentos entre os objetos de utilizador do Azure AD e objetos de utilizador de cada aplicação SaaS pré-configurado. Algumas aplicações gerir outros tipos de objetos, tais como a grupos ou contactos. Selecionar uma destes mapeamentos na tabela mostra o editor de mapeamento para a direita, onde podem ser visualizados e personalizados.

![Pá de recursos de aplicação][2]

Personalizações suportadas durante a pré-visualização do primeiro incluem:

* Activar e desactivar os mapeamentos de objetos específicos, tais como o objeto de utilizador do Azure AD para objeto de utilizador a aplicação SaaS.

* Editar os atributos que fluam do objeto do utilizador Azure AD para objeto de utilizador a aplicação. Para mais informações sobre mapeamento de atributo, consulte [Noções sobre tipos de mapeamento de atributo](active-directory-saas-customizing-attribute-mappings.md#understanding-attribute-mapping-types).

* Filtre que ações de aprovisionamento Azure AD deve efetuar na aplicação de destino, que é uma nova funcionalidade no portal do Azure. Em vez de ter Azure AD totalmente sincronizar objetos, pode limitar as acções executadas. Por exemplo, apenas por selecionar de **atualização**, Azure AD apenas as atualizações utilizador existente contas numa aplicação e não criar novos. Ao selecionar apenas **Criar**, Azure apenas cria novas contas de utilizador, mas não atualiza o já existentes. Esta funcionalidade permite aos administradores criar mapeamentos diferentes para a criação de conta e actualizar fluxos de trabalho. A capacidade para criar múltiplos mapeamentos por aplicação completa é planeada para mais tarde durante o período de pré-visualização.

###<a name="settings"></a>Definições
Esta secção permite aos administradores iniciar e parar o Azure AD aprovisionamento do serviço para a aplicação seleccionada, bem como opcionalmente limpar a cache de aprovisionamento e reinicie o serviço.

Se estiver a ser activado aprovisionamento pela primeira vez para uma aplicação, ative o serviço ao alterar o **Estado de aprovisionamento** para **no**. Isto faz com que o Azure AD aprovisionamento do serviço efetuar uma sincronização inicial, onde lê os utilizadores atribuídos na secção **utilizadores e grupos** , consultas a aplicação de destino para as mesmas e, em seguida, executa acções aprovisionamento definidas na secção Azure AD **mapeamentos** . Durante este processo, o serviço de aprovisionamento armazena dados em cache sobre contas de utilizador que está a gerir a, para que com gestão de contas dentro as aplicações de destino que foram nunca no âmbito da atribuição não são afetadas por retirar aprovisionamento operações. Após a sincronização inicial, o serviço de aprovisionamento sincroniza automaticamente utilizador e agrupar objetos num intervalo de dez minutos.

Basta alterar o **Estado de aprovisionamento** para **desativar** interrompe o serviço de aprovisionamento. Neste estado, Azure não criar, atualizar ou remover qualquer utilizador ou agrupar objetos na aplicação. Alterar o estado para ativado faz com que o serviço continuar onde parou.

Selecionar a caixa de verificação **desmarcar o estado atual e reinicie a sincronização** e guardar deixa o serviço de aprovisionamento, copia os dados em cache sobre que contas do Azure AD está a gerir, reinicia os serviços e executa a sincronização inicial novamente. Esta opção permite aos administradores iniciar o processo de implementação de aprovisionamento sobre novamente.

###<a name="synchronization-details"></a>Detalhes de sincronização
Esta secção fornece adição obter mais detalhes sobre o funcionamento do serviço de aprovisionamento, incluindo as horas primeiros e últimos que executou o serviço de aprovisionamento contra a aplicação e quantas utilizador e agrupar objetos estão a ser geridos.

Ligações são fornecidos para o **relatório de atividade aprovisionar**, que proporciona um registo de todos os utilizadores e grupos criado, actualizado e removida entre Azure AD e o destino da aplicação e para o **relatório de erros aprovisionar** que fornece mais detalhada mensagens de erro de utilizador e agrupar objetos que não foi possível ler, criado, actualizados ou removidos. 

[0]: ./media/active-directory-enterprise-apps-manage-provisioning/enterprise-apps-blade.PNG
[1]: ./media/active-directory-enterprise-apps-manage-provisioning/enterprise-apps-provisioning.PNG
[2]: ./media/active-directory-enterprise-apps-manage-provisioning/enterprise-apps-provisioning-mapping.PNG
