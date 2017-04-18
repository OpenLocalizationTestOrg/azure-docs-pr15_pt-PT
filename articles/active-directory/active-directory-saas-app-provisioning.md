<properties
    pageTitle="Automatizado SaaS aplicação utilizador aprovisionamento no Azure AD | Microsoft Azure"
    description="Uma introdução à como pode utilizar o Azure AD para aprovisionar automaticamente, retirar aprovisionar e atualizar continuamente contas de utilizador entre várias aplicações de SaaS de terceiros."
    services="active-directory"
    documentationCenter=""
    authors="asmalser-msft"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="02/09/2016"
    ms.author="asmalser-msft"/>

#<a name="automate-user-provisioning-and-deprovisioning-to-saas-applications-with-azure-active-directory"></a>Automatizar utilizador aprovisionamento e Deprovisioning SaaS aplicações com o Azure Active Directory

##<a name="what-is-automated-user-provisioning-for-saas-apps"></a>O que é automatizado aprovisionamento de utilizador para as aplicações de SaaS?

Azure Active Directory (Azure AD) permite-lhe automatizar a criação, manutenção e remoção da identidades de utilizador em aplicações de nuvem ([SaaS](https://azure.microsoft.com/overview/what-is-saas/)) como Dropbox, Salesforce, ServiceNow e muito mais.

**Abaixo estão alguns exemplos do que esta funcionalidade permite-lhe fazer:**

- Crie automaticamente novas contas nas aplicações SaaS direita para novas pessoas quando aderir a sua equipa.
- Desative automaticamente contas a partir das aplicações SaaS quando as pessoas inevitavelmente deixam a equipa.
- Certifique-se de que as identidades nas suas aplicações SaaS são mantidas atualizadas com base nas alterações no diretório.
- Aprovisionar objetos de não utilizadores, tais como grupos, para aplicações SaaS que suportam-los.

**Também aprovisionamento automatizado utilizador inclui as seguintes funcionalidades:**

- A capacidade para corresponder ao identidades existentes entre Azure AD e SaaS apps.
- Opções de personalização a ajuda do Azure AD ajustar as configurações atuais das aplicações de SaaS a sua organização está atualmente a utilizar.
- Alertas de e-mail opcional para erros de aprovisionamento.
- Registos de elaboração de relatórios e atividade para o ajudar a monitorização e resolução de problemas.

##<a name="why-use-automated-provisioning"></a>Porquê Utilizar Aprovisionamento automatizado?

Algumas motivações comuns para utilizar esta funcionalidade incluem:

- Para evitar os custos, ineficiências e erro humano associado manual processos de aprovisionamento.
- Para proteger a sua organização removendo instantaneamente identidades dos utilizadores a partir das aplicações de SaaS chaves quando deixarem a organização.
- Para importar facilmente um número em massa de utilizadores para uma aplicação SaaS específica.
- Para aproveitar a conveniência da tendo a solução de aprovisionamento executar fora as políticas de acesso app mesmo que definiu para Azure AD Single Sign-On.

##<a name="frequently-asked-questions"></a>Perguntas mais frequentes

**Frequência é que Azure AD escrever diretório alterações à aplicação SaaS?**

Azure AD verifica a existência de alterações todas as cinco a dez minutos. Se a aplicação SaaS está a devolver vários erros (tal como em maiúsculas/minúsculas credenciais de administrador inválidos), em seguida, Azure AD diminuir gradualmente sua frequência para até uma vez por dia, até que sejam resolvidos os erros.

**Quanto tempo vai demorar aprovisionamento dos meus utilizadores?**

Alterações utilizarão ocorrem quase instantaneamente mas se estiver a tentar aprovisionar a maior parte do seu diretório, em seguida, depende o número de utilizadores e grupos que possui. Diretórios pequenas demoram apenas alguns minutos, médias directórios poderão demorar vários minutos e poderão demorar algumas horas, directórios muito grandes.

**Como posso registar o progresso da tarefa de aprovisionamento atual?**

Pode rever o relatório de aprovisionamento de conta na secção de relatórios do seu diretório. Outra opção é a visitar o separador Dashboard para a aplicação de SaaS que são aprovisionamento para e, procure a secção "Integração com o estado" perto da parte inferior da página.

**Como posso saber se os utilizadores a obter aprovisionado corretamente falharem?**

No final da configuração do aprovisionamento o assistente não existem é uma opção para subscrever notificações de correio eletrónico para aprovisionar falhas. Também pode verificar o relatório de erros de aprovisionamento para ver quais os utilizadores não conseguiu ser aprovisionado e por que motivo.

**Pode Azure AD escrever alterações a partir da aplicação SaaS novamente ao diretório?**

Para a maioria das aplicações de SaaS, aprovisionamento é apenas de saída, que significa que os utilizadores estão escritos do diretório da aplicação e alterações a partir da aplicação não podem ser escritas novamente ao diretório. Para [DIATRABALHO](https://msdn.microsoft.com/library/azure/dn762434.aspx), no entanto, aprovisionamento é entrada só, que significa que que os utilizadores são importados para o diretório a partir do dia de trabalho e da mesma forma, as alterações no diretório não obter escritas novamente para o dia de trabalho.

**Como podem submeter os seus comentários para a equipa de engenharia?**

Contacte-nos através de da [Fórum de comentários do Azure Active Directory](https://feedback.azure.com/forums/169401-azure-active-directory/).

##<a name="how-does-automated-provisioning-work"></a>Como é que o trabalho de aprovisionamento automatizado?

Azure AD Aprovisiona utilizadores às aplicações SaaS ligando para os pontos finais fornecidos pelo fornecedor cada aplicação de aprovisionamento. Estes pontos finais permitem Azure AD criar, atualizar e remover utilizadores através de programação. Segue-se uma breve descrição geral dos passos diferentes que entra em Azure AD para automatizar aprovisionamento.

1. Quando ativa aprovisionamento para uma aplicação pela primeira vez, são executadas as seguintes ações:
 - Azure AD tentará corresponder ao quaisquer utilizadores existentes na aplicação SaaS com suas identidades correspondentes no diretório. Quando um utilizador é correspondido, estes são *não* ativada automaticamente para o início de sessão único. Ordem de um utilizador tenha acesso à aplicação, eles tem de ser explicitamente atribuídos à aplicação no Azure AD, diretamente ou através de membros do grupo.
 - Se já tiver especificado os utilizadores que devem ser atribuídos à aplicação e se Azure AD falhar encontrar as contas existentes para esses utilizadores, Azure AD aprovisionar novas contas para os mesmos na aplicação.
2. Assim que tiver sido concluída a sincronização inicial, como descrito acima, Azure AD irá Verifique todas as 10 minutos para as seguintes alterações:
 - Se tem sido atribuídos a novos utilizadores à aplicação (diretamente ou através da associação a grupos), em seguida, vai ser aprovisionado uma nova conta na aplicação SaaS.
 - Se foi removido o acesso de um utilizador, em seguida, respetiva conta na aplicação SaaS será marcada como desativada (utilizadores totalmente nunca são eliminados, que protege-o de perda de dados em caso de uma configuração errada).
 - Se um utilizador recentemente tiver sido atribuído à aplicação e já possui uma conta na aplicação SaaS, essa conta será marcada como estando ativados e podem ser atualizadas determinadas propriedades de utilizador se estiverem Desatualizadas comparadas com o diretório.
 - Se tem sido alteradas informações de um utilizador (como o número de telefone, localização do escritório, etc) no diretório, essas informações também a ser atualizadas na aplicação SaaS.

Para mais informações sobre como os atributos são mapeados entre Azure AD e a aplicação SaaS, consulte o artigo sobre [Personalizar mapeamentos de atributo](active-directory-saas-customizing-attribute-mappings.md).

##<a name="list-of-apps-that-support-automated-user-provisioning"></a>Lista de aplicações que suportam aprovisionamento automatizado do utilizador

Clique numa aplicação para ver um tutorial sobre como configurar aprovisionamento automatizado para o mesmo:

- [Caixa](http://go.microsoft.com/fwlink/?LinkId=286016)
- [Citrix GoToMeeting](http://go.microsoft.com/fwlink/?LinkId=309580)
- [Coincidirem](http://go.microsoft.com/fwlink/?LinkId=309575)
- [Docusign](http://go.microsoft.com/fwlink/?LinkId=403254)
- [Dropbox para empresas](http://go.microsoft.com/fwlink/?LinkId=309581)
- [Google Apps](http://go.microsoft.com/fwlink/?LinkId=309577)
- [Jive](http://go.microsoft.com/fwlink/?LinkId=309591)
- [Equipa de vendas](http://go.microsoft.com/fwlink/?LinkId=286017)
- [Salesforce Sandbox](http://go.microsoft.com/fwlink/?LinkId=327869)
- [ServiceNow](http://go.microsoft.com/fwlink/?LinkId=309587)
- [DIATRABALHO](http://go.microsoft.com/fwlink/?LinkId=690250) (entrada de aprovisionamento)

Ordem de uma aplicação para o suporte de aprovisionamento automatizado do utilizador,-lo primeiro tem de fornecer os pontos finais necessários que permitem para programas externos automatizar a criação, a manutenção e a remoção de utilizadores. Por conseguinte, não todas as aplicações do SaaS são compatíveis com esta funcionalidade. Para as aplicações que suportam este, a equipa de engenharia do Azure AD, em seguida, poderá criar um conector de aprovisionamento para nessas aplicações e este trabalho tem prioridade pelas necessidades dos atuais e potenciais clientes.

Para contactar a equipa de engenharia do Azure AD para solicitar aprovisionamento suporte para as aplicações adicionais, submeta uma mensagem através de da [Fórum de comentários do Azure Active Directory](https://feedback.azure.com/forums/169401-azure-active-directory/).

##<a name="related-articles"></a>Artigos relacionados

- [Índice de artigo para gestão de aplicações no Azure Active Directory](active-directory-apps-index.md)
- [Personalizar os mapeamentos de atributo para aprovisionamento de utilizador](active-directory-saas-customizing-attribute-mappings.md)
- [Escrever expressões para mapeamentos de atributo](active-directory-saas-writing-expressions-for-attribute-mappings.md)
- [Controlo do âmbito filtros para aprovisionamento de utilizador](active-directory-saas-scoping-filters.md)
- [Utilizar SCIM para activar automático de aprovisionamento de utilizadores e grupos a partir do Azure Active Directory para aplicações](active-directory-scim-provisioning.md)
- [Notificações de aprovisionamento de conta](active-directory-saas-account-provisioning-notifications.md)
- [Lista de tutoriais sobre como integrar SaaS aplicações](active-directory-saas-tutorial-list.md)
