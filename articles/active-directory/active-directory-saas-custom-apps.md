<properties 
    pageTitle="Configurar o serviço single sign-on para as aplicações que não estão na Galeria de aplicação do Azure Active Directory | Microsoft Azure" 
    description="Saiba como ao Self-Service ligar as aplicações para o Azure Active Directory utilizando SAML e o SSO com base em palavra-passe" 
    services="active-directory" 
    authors="asmalser-msft"  
    documentationCenter="na" manager="femila"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="02/09/2016" 
    ms.author="asmalser" />

#<a name="configuring-single-sign-on-to-applications-that-are-not-in-the-azure-active-directory-application-gallery"></a>Configurar o serviço single sign-on para as aplicações que não estão na Galeria de aplicação do Azure Active Directory

Este artigo é sobre uma funcionalidade que permite aos administradores configurar o serviço single sign-on para aplicações não se encontram no Azure Active Directory aplicação galeria *sem escrever código*. Esta funcionalidade foi lançada a partir da pré-visualização técnica 18 de Novembro de 2015 e está incluída no [Azure Active Directory Premium](active-directory-editions.md). Se está a procurar em vez disso, programador obter orientações sobre como integrar aplicações personalizadas com o Azure AD através de código, consulte o artigo [Cenários de autenticação de Azure AD](active-directory-authentication-scenarios.md).

A Galeria de aplicação do Azure Active Directory fornece uma lista de aplicações que são conhecidos para suportar um formulário de serviço single sign-on com o Azure Active Directory, conforme descrito [neste](active-directory-appssoaccess-whatis.md)artigo. Depois de (como um IT especialista ou sistema integrador na sua organização) ter encontrado na aplicação que pretende ligar, pode começar por siga as instruções passo a passo apresentadas no portal de gestão Azure para permitir o início de sessão único.

Os clientes com o [Azure Active Directory Premium](active-directory-editions.md) licenças também obter estas funcionalidades adicionais:

* Integração de gestão personalizada de qualquer aplicação que suporte fornecedores de identidade do SAML 2.0 (iniciados por SP ou iniciados por IdP)
* Integração de gestão personalizada de qualquer aplicação web que tenha uma baseada em HTML início de sessão na página utilizar o [SSO com base em palavra-passe](active-directory-appssoaccess-whatis.md#password-based-single-sign-on)
* Ligação de gestão personalizada de aplicações que utilizam o protocolo SCIM para utilizador aprovisionamento ([descritas aqui](active-directory-scim-provisioning.md))
* Capacidade de adicionar ligações para qualquer aplicação do [iniciador de aplicações do Office 365](https://blogs.office.com/2014/10/16/organize-office-365-new-app-launcher-2/) ou o [Painel de acesso do Azure AD](active-directory-appssoaccess-whatis.md#deploying-azure-ad-integrated-applications-to-users)

Isto pode incluir não só SaaS aplicações que utilizam mas tiver não ainda foi no-efectuar-se para a Galeria de aplicação do Azure AD, mas aplicações de terceiros web que a sua organização tenha implementado para os servidores de que controlar, tanto em nuvem ou no local.

Estas capacidades, também conhecido como *modelos da aplicação integração*, fornecerem pontos de ligação baseadas em padrões para as aplicações que suportam SAML, SCIM ou autenticação baseada em formulários em incluam flexíveis opções e definições para compatibilidade com um grande número de aplicações. 

##<a name="adding-an-unlisted-application"></a>Adicionar uma aplicação não listada

Para ligar uma aplicação utilizando um modelo de integração de aplicação, inicie sessão no portal de gestão Azure utilizando a sua conta de administrador do Azure Active Directory e navegue para a **do Active Directory > [directório] > aplicações** secção, selecione **Adicionar**e, em seguida, **Adicionar uma aplicação a partir da Galeria**. 

![][1]

Na Galeria de aplicação, pode adicionar uma aplicação não listada com a categoria **personalizada** no lado esquerdo ou ao selecionar a ligação **Adicionar uma aplicação não listada** que é apresentada nos resultados da pesquisa, se a sua aplicação pretendida não foi encontrada. Depois de introduzir um nome para a sua aplicação, pode configurar as opções de início de sessão únicos e o comportamento. 

**Sugestão rápida**: como prática recomendada, utilize a função Procurar para verificar se a aplicação já existe na Galeria de aplicação. Se a aplicação for encontrada e respetiva descrição menções "single sign-on", em seguida, a aplicação já são suportada para federados single sign-on. 

![][2]

Adicionar uma aplicação do segundo este método fornece uma experiência muito semelhante ao disponíveis para aplicações integradas previamente. Para começar, selecione **Configurar Single Sign-On**. O ecrã seguinte apresenta as seguintes três opções para configurar o início de sessão único, que são descrito nas secções seguintes.

![][3]


##<a name="azure-ad-single-sign-on"></a>Azure AD Single Sign-On

Selecione esta opção para configurar uma autenticação baseada em SAML para a aplicação. Isto requer que o suporte técnico da aplicação SAML 2.0 e, devem recolher informações sobre como utilizar as capacidades SAML da aplicação antes de continuar. Após selecionar **seguinte**, vai ser-lhe para introduzir o URL diferentes três correspondente para os pontos finais SAML para a aplicação. 

![][4]
 
Estas são:

* **URL no início de sessão (iniciados por SP apenas)** – onde o utilizador vai para o início de sessão para esta aplicação. Se a aplicação estiver configurada para executar o serviço iniciados por fornecedor início de sessão único no, em seguida, quando um utilizador navega para este URL, o fornecedor de serviços fará o redirecionamento necessário ao Azure AD para autenticar e o utilizador no início de sessão. Se este campo é preenchido, em seguida, Azure AD irá utilizar este URL para iniciar a aplicação a partir do Office 365 e o painel do Azure AD acesso. Se este campo é ommited, em seguida, Azure AD em vez disso, vai executar o fornecedor de identidades-início de sessão iniciada quando a aplicação é iniciada a partir do Office 365, o painel de acesso do Azure AD ou a partir do Azure AD único início de sessão URL (copiable a partir do separador Dashboard).

* **URL emissor** - o emissor URL deve identificar exclusivamente a aplicação para o qual início de sessão único no está a ser configurado. Este é o valor que Azure AD envia novamente a aplicação como o parâmetro de **audiência** do SAML token e a aplicação é esperada para validá-lo. Este valor também é apresentado como o **ID da entidade** de quaisquer metadados SAML fornecido pela aplicação. Verificar a documentação da aplicação SAML para obter detalhes sobre o que é o ID da entidade ou valor audiência for. Abaixo está um exemplo de como o URL de audiência aparece no token SAML devolvido para a aplicação:

```
    <Subject>
    <NameID Format="urn:oasis:names:tc:SAML:2.0:nameid-format:unspecificed">chad.smith@example.com</NameID>
        <SubjectConfirmation Method="urn:oasis:names:tc:SAML:2.0:cm:bearer" />
      </Subject>
      <Conditions NotBefore="2014-12-19T01:03:14.278Z" NotOnOrAfter="2014-12-19T02:03:14.278Z">
        <AudienceRestriction>
          <Audience>https://tenant.example.com</Audience>
        </AudienceRestriction>
      </Conditions>
```

* **URL de resposta** - o URL de resposta é onde a aplicação de espera receber o token SAML. Isto é também conhecido como o **URL do serviço de consumidor afirmação (ACS)**. Verificar a documentação da aplicação SAML para obter detalhes sobre o que é o seu resposta token SAML ou o URL ACS.
 Depois de estes foram introduzidas, clique em **seguinte** para avançar para o ecrã seguinte. Este ecrã fornece informações sobre o que tem de ser configurado no lado aplicação para ativá-la aceitar um token SAML a partir do Azure AD. 

![][5]

Quais os valores são necessários variam consoante a aplicação, pelo que deve verificar a documentação da aplicação SAML para obter detalhes. **Início de sessão** e **Terminar sessão** ambos ser resolvido para o ponto final mesmo, o que é o ponto final de processamento de pedidos SAML para sua instância do Azure AD do URL do serviço. O URL de emissor é o valor que aparece como "Emissor" dentro do token SAML emitido para a aplicação. 

Depois de ter sido configurada a sua aplicação, clique no botão **seguinte** e **Concluir** para fechar a caixa de diálogo. 

##<a name="assigning-users-and-groups-to-your-saml-application"></a>Atribuir utilizadores e grupos para a sua aplicação SAML 

Assim que a sua aplicação ter sido configurada para utilizar o Azure AD como um fornecedor de identidades baseadas em SAML, é quase pronto para testar. Como um controlo de segurança, Azure AD não irão emitir um token, permitindo-lhes iniciar sessão na aplicação, a menos que foi concedidos acesso utilizando Azure AD. Os utilizadores podem ser concedidos acesso diretamente ou através de um grupo de que são um membro da. 

Para atribuir um utilizador ou grupo para a sua aplicação, clique no botão **Atribuir utilizadores** . Selecione o utilizador ou grupo que pretende atribuir e, em seguida, selecione o botão **atribuir** . 

![][6]

Atribuir um utilizador permitirá Azure AD emitir o token de para o utilizador, assim como a causar um mosaico para esta aplicação seja apresentado no painel de acesso do utilizador. Um mosaico da aplicação também aparecerá no iniciador de aplicações do Office 365 se o utilizador estiver a utilizar o Office 365. 

Pode carregar um logótipo de mosaico para a aplicação com o botão **Carregar o logótipo** no separador **Configurar** para a aplicação. 

###<a name="customizing-the-claims-issued-in-the-saml-token"></a>Personalizar as afirmações emitidas no token de SAML 

Quando um utilizador autentica a aplicação, Azure AD emitir um token SAML para a aplicação que contém informações (ou em afirmações) sobre o utilizador que identifica exclusivamente. Por predefinição inclui o nome de utilizador do utilizador, endereço de e-mail, nome próprio e apelido. 

Pode ver ou editar as afirmações enviadas o token SAML para a aplicação no separador de **atributos** . 

![][7]

Existem dois motivos possíveis por que razão poderá ter de editar afirmações emitidas no token de SAML: •The aplicação foi escrita necessitam de um conjunto diferente de afirmação URIs ou valores de afirmação •Your aplicação foi implementada de uma forma que requer a NameIdentifier afirmação seja algo que não o nome de utilizador (nome da principal de utilizador AKA) armazenado no Azure Active Directory. 

Para obter informações sobre como adicionar e editar reclamações por estes cenários, consulte este [artigo sobre a personalização de afirmações](active-directory-saml-claims-customization.md). 

###<a name="testing-the-saml-application"></a>Testar a aplicação do SAML 

Depois de tem sido configurados a SAML URLs e o certificado no Azure AD e na aplicação, os utilizadores ou grupos foram atribuídos a aplicação no Azure e as afirmações foram revistas e editadas se for necessário, em seguida, o utilizador está pronto para iniciar sessão na aplicação. 

Para testar, basta iniciar-para o painel de acesso do Azure AD na https://myapps.microsoft.com utilizando uma conta de utilizador que atribuiu a aplicação, e, em seguida, clique no mosaico da aplicação iniciar o processo de início de sessão único. Em alternativa, pode procurar diretamente para o URL de início de sessão para a aplicação e iniciar sessão a partir daí. 

Para sugestões de depuração, consulte este [artigo sobre como depurar baseadas em SAML single sign-on para aplicações](active-directory-saml-debugging.md) 

##<a name="password-single-sign-on"></a>Palavra-passe Single Sign-On

Selecione esta opção para configurar o [baseado em palavra-passe serviço single sign-on](active-directory-appssoaccess-whatis.md) para uma aplicação web que tem uma página de início de sessão no HTML. Com base em palavra-passe SSO, também conhecida como a palavra-passe vaulting, permite-lhe gerir o acesso do utilizador e palavras-passe para aplicações web que não suportam a Federação de identidade. Também é útil para cenários onde precisa de vários utilizadores para partilhar uma única conta, tal como a contas de aplicação de redes sociais da sua organização. 

Após selecionar **seguinte**, vai ser-lhe para introduzir o URL da baseados na web e início de sessão na página a aplicação. Tenha em atenção que tem de ser a página que inclui os campos de entrada do nome de utilizador e palavra-passe. Assim que introduziu, Azure AD inicia um processo para analisar a página de início de sessão para um nome de utilizador de entrada e uma palavra-passe de entrada. Se o processo não é efetuada com êxito, em seguida, orienta-através de um processo de instalação de uma extensão de browser (requer o Internet Explorer, Chrome ou Firefox), que permitirá capturar manualmente os campos alternativo.

Depois da página de início de sessão é capturada, poderão ser atribuídos a utilizadores e grupos e políticas de credenciais podem ser definidas tal como normal [aplicações SSO de palavra-passe](active-directory-appssoaccess-whatis.md).

Nota: Pode carregar um logótipo de mosaico para a aplicação com o botão **Carregar o logótipo** no separador **Configurar** para a aplicação. 

##<a name="existing-single-sign-on"></a>Existente Single Sign-On

Selecione esta opção para adicionar uma ligação para uma aplicação para o portal de painel de acesso do Azure AD ou do Office 365 da sua organização. Pode utilizar este para adicionar ligações para aplicações web personalizada que atualmente utilizam serviços de Federação do Azure Active Directory (ou outro serviço de Federação) em vez do Azure AD para autenticação. Em alternativa, pode adicionar abrangente ligações para páginas específicas do SharePoint ou outras páginas web que pretende aparecer na painéis de acesso do utilizador. 

Após selecionar **seguinte**, vai ser-lhe para introduzir o URL da aplicação a ligação. Uma vez concluída, utilizadores e grupos, podem ser atribuídos a aplicação, o que faz com que a aplicação seja apresentado no [iniciador de aplicações do Office 365](https://blogs.office.com/2014/10/16/organize-office-365-new-app-launcher-2/) ou o [Painel de acesso do Azure AD](active-directory-appssoaccess-whatis.md#deploying-azure-ad-integrated-applications-to-users) para esses utilizadores.

Nota: Pode carregar um logótipo de mosaico para a aplicação com o botão **Carregar o logótipo** no separador **Configurar** para a aplicação.

## <a name="related-articles"></a>Artigos relacionados

- [Índice de artigo para gestão de aplicações no Azure Active Directory](active-directory-apps-index.md)
- [Como personalizar em afirmações emitidas no Token de SAML para aplicações integradas previamente](active-directory-saml-claims-customization.md)
- [Resolução de problemas baseadas em SAML Single Sign-On](active-directory-saml-debugging.md)

<!--Image references-->
[1]: ./media/active-directory-saas-custom-apps/customapp1.png
[2]: ./media/active-directory-saas-custom-apps/customapp2.png
[3]: ./media/active-directory-saas-custom-apps/customapp3.png
[4]: ./media/active-directory-saas-custom-apps/customapp4.png
[5]: ./media/active-directory-saas-custom-apps/customapp5.png
[6]: ./media/active-directory-saas-custom-apps/customapp6.png
[7]: ./media/active-directory-saas-custom-apps/customapp7.png
