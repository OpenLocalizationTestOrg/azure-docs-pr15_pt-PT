<properties
    pageTitle="Active Directory gestão de serviços de Federação e personalização com ligação do Azure AD | Microsoft Azure"
    description="Gestão de AD FS com ligação do Azure AD e a personalização do AD FS início de sessão no experiência do utilizador com ligação do Azure AD e PowerShell."
    keywords="O AD FS, ADFS, AD FS gestão, AAD ligar, ligar, iniciar sessão, o AD FS personalização, reparar Federação de fidedignidade, Office 365, parte dependente"
    services="active-directory"
    documentationCenter=""
    authors="anandyadavmsft"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/01/2016"
    ms.author="anandy"/>

# <a name="active-directory-federation-services-management-and-customization-with-azure-ad-connect"></a>Gestão do Active Directory os serviços de Federação e personalização com ligação do Azure AD

Tarefas de detalhes neste artigo relacionado com serviços de Federação do Active Directory (AD FS) que podem ser executadas utilizando o Microsoft Azure Active Directory ligar, assim como outras tarefas comuns do AD FS que poderão ser necessárias para concluir a configuração de um farm de servidores do AD FS.

| Tópico | O que abrange |
|:------|:-------------|
|**Gestão do AD FS**|
|[Reparar a fidedignidade](#repairthetrust)| Reparar a fidedignidade da federação com o Office 365 |
|[Adicionar um servidor do AD FS](#addadfsserver) | Expandir o farm do AD FS com um servidor do AD FS adicional|
|[Adicionar um servidor de proxy do AD FS web aplicações](#addwapserver) | Expandir o farm do AD FS com um servidor WAP adicional|
|[Adicionar um domínio federado](#addfeddomain)| Adicionar um domínio federado|
| **Personalização do AD FS**|
|[Adicionar um logótipo da empresa personalizada ou uma ilustração](#customlogo)| Personalizar uma página de início de sessão do AD FS com um logótipo da empresa e uma ilustração |
|[Adicionar uma descrição de início de sessão](#addsignindescription) | Adicionar uma descrição de início de sessão na página |
|[Modificar AD FS afirmação regras](#modclaims) | Modificar o AD FS afirmações para vários cenários de Federação |

## <a name="ad-fs-management"></a>Gestão do AD FS

Ligação do Azure AD fornece várias tarefas relacionadas com o AD FS que podem ser executado utilizando o Assistente de ligação do Azure AD com intervenção mínima do utilizador. Depois de instalar a ligação do Azure AD executando o assistente, pode executar novamente o Assistente para executar tarefas adicionais.

### Reparar a fidedignidade<a name=repairthetrust></a>

Ligação do Azure AD pode verificar o estado de funcionamento atual da fidedignidade AD FS e Azure Active Directory e efetuar ações adequadas para reparar a fidedignidade. Siga estes passos para reparar o Azure AD e fidedignidade AD FS.

1. Selecione **Reparar AAD e ADFS confiar** na lista de tarefas adicionais.
![Reparar AAD e ADFS confiar](media\active-directory-aadconnect-federation-management\RepairADTrust1.PNG)

2. Na página **ligar ao Azure AD** , fornecer as suas credenciais de administrador global para Azure AD e clique em **seguinte**.
![Ligar ao Azure AD](media\active-directory-aadconnect-federation-management\RepairADTrust2.PNG)

3. Na página de **credenciais de acesso remoto** , introduza as credenciais para o administrador do domínio.
![Credenciais de acesso remoto](media\active-directory-aadconnect-federation-management\RepairADTrust3.PNG)

    Depois de clicar em **seguinte**, o ligação do Azure AD verificar a existência de estado de funcionamento do certificado e mostrar quaisquer problemas.

    ![Estado de certificados](media\active-directory-aadconnect-federation-management\RepairADTrust4.PNG)

    A página **pronto para configurar** irá mostrar a lista de ações que será executada para reparar a fidedignidade.

    ![Pronto para configurar](media\active-directory-aadconnect-federation-management\RepairADTrust5.PNG)

4. Clique em **instalar** para reparar a fidedignidade.

>[AZURE.NOTE] Azure AD Connect pode apenas reparação ou agir em certificados que estão autoassinado. Ligação do Azure AD não consegue reparar certificados de terceiros.

### Adicionar um servidor do AD FS<a name=addadfsserver></a>

> [AZURE.NOTE] Ligação do Azure AD requer o ficheiro de certificado PFX para adicionar um servidor do AD FS. Por conseguinte, pode executar esta operação apenas se tiver configurado o farm do AD FS utilizando a ligação do Azure AD.

1. Selecione **Implementar um servidor de Federação adicionais** e clique em **seguinte**.
![Servidor de Federação adicionais](media\active-directory-aadconnect-federation-management\AddNewADFSServer1.PNG)

2. Na página **ligar ao Azure AD** , introduza as suas credenciais de administrador global do Azure AD e clique em **seguinte**.
![Ligar ao Azure AD](media\active-directory-aadconnect-federation-management\AddNewADFSServer2.PNG)

3. Forneça o domínio de credenciais de administrador.
![Credenciais de administrador do domínio](media\active-directory-aadconnect-federation-management\AddNewADFSServer3.PNG)

4. Ligação do Azure AD irá pedir a palavra-passe do ficheiro PFX que forneceu ao configurar a sua nova farm do AD FS com ligação do Azure AD. Clique em **Introduzir palavra-passe** para fornecer a palavra-passe para o ficheiro PFX.
![Palavra-passe de certificado](media\active-directory-aadconnect-federation-management\AddNewADFSServer4.PNG)

    ![Especificar um certificado SSL](media\active-directory-aadconnect-federation-management\AddNewADFSServer5.PNG)

5. Na página de **Servidores do AD FS** , introduza o nome do servidor ou o endereço IP a serem adicionados ao farm do AD FS.
![Servidores do AD FS](media\active-directory-aadconnect-federation-management\AddNewADFSServer6.PNG)

6. Clique em **seguinte** e percorrer a página **Configurar** final. Depois de ligação do Azure AD tiver terminado de adicionar os servidores para o farm do AD FS, será dada a opção para verificar a conectividade.
![Pronto para configurar](media\active-directory-aadconnect-federation-management\AddNewADFSServer7.PNG)

    ![Instalação concluída](media\active-directory-aadconnect-federation-management\AddNewADFSServer8.PNG)

### Adicionar um servidor de proxy do AD FS web aplicações<a name=addwapserver></a>

> [AZURE.NOTE] Ligação do Azure AD requer o ficheiro de certificado PFX para adicionar um servidor de proxy de aplicação web. Por conseguinte, irá conseguirá efetuar esta operação apenas se tiver configurado o farm do AD FS utilizando a ligação do Azure AD.

1. Selecione **Implementar o Proxy de aplicação Web** a partir da lista de tarefas disponíveis.
![Implementar o proxy de aplicação web](media\active-directory-aadconnect-federation-management\WapServer1.PNG)

2. Forneça as credenciais de administrador global Azure.
![Ligar ao Azure AD](media\active-directory-aadconnect-federation-management\wapserver2.PNG)

3. Na página de **certificado SSL especificar** , forneça a palavra-passe para o ficheiro PFX que forneceu quando configurar o farm do AD FS com a ligação do Azure AD.
![Palavra-passe de certificado](media\active-directory-aadconnect-federation-management\WapServer3.PNG)

    ![Especificar um certificado SSL](media\active-directory-aadconnect-federation-management\WapServer4.PNG)

4. Adicione o servidor para ser adicionado como um proxy de aplicação web. Porque o servidor de proxy de aplicação web poderão não ser associado ao domínio, o assistente irá pedir para introduzir as credenciais administrativas no servidor a ser adicionado.
![Credenciais do servidor administrativo](media\active-directory-aadconnect-federation-management\WapServer5.PNG)

5. Na página de **credenciais de fidedignidade do Proxy** , forneça credenciais administrativas para configurar a fidedignidade de proxy e aceder ao servidor principal no farm do AD FS.
![Credenciais de fidedignidade do proxy](media\active-directory-aadconnect-federation-management\WapServer6.PNG)

6. Na página **pronto para configurar** , o Assistente de mostra a lista de ações que será executada.
![Pronto para configurar](media\active-directory-aadconnect-federation-management\WapServer7.PNG)

7. Clique em **instalar** para concluir a configuração. Após a configuração está concluída, o assistente dá-lhe a opção para verificar a conectividade para os servidores. Clique em **Verificar** para verificar conectividade.
![Instalação concluída](media\active-directory-aadconnect-federation-management\WapServer8.PNG)

### Adicionar um domínio federado<a name=addfeddomain></a>

É fácil de adicionar um domínio para ser Federado com Azure AD utilizando a ligação do Azure AD. Ligação do Azure AD adiciona o domínio para a Federação e modifica as regras de afirmação para refletir corretamente o emissor quando tiver múltiplos domínios federados com Azure AD.

1. Para adicionar um domínio federado, selecione a tarefa **Adicionar uma adicional domínio do Azure AD**.
![Adicionais domínio do Azure AD](media\active-directory-aadconnect-federation-management\AdditionalDomain1.PNG)

2. Na página seguinte do assistente, fornece as credenciais de administrador global para Azure AD.
![Ligar ao Azure AD](media\active-directory-aadconnect-federation-management\AdditionalDomain2.PNG)

3. Na página de **credenciais de acesso remoto** , forneça o domínio de credenciais de administrador.
![Credenciais de acesso remoto](media\active-directory-aadconnect-federation-management\additionaldomain3.PNG)

4. Na página seguinte, o assistente irá fornecer uma lista de domínios do Azure AD com os quais pode federar seu diretório no local. Selecione o domínio a partir da lista.
![Domínio do Azure AD](media\active-directory-aadconnect-federation-management\AdditionalDomain4.PNG)

    Depois de escolher o domínio, o assistente irá fornecer-lhe com as informações adequadas em relação à ainda mais ações que o assistente irá demorar e o impacto da configuração. Em alguns casos, se selecionar um domínio que ainda não é verificado no Azure AD, o assistente irá fornecer-lhe com informações para ajudá-lo a verificar o domínio. Consulte o artigo [Adicionar o nome de domínio personalizado para o Azure Active Directory](active-directory-add-domain.md) para obter mais detalhes.

5. Clique em **seguinte**e página **pronto para configurar** irá apresentar a lista de ações que irá efetuar a ligação do Azure AD. Clique em **instalar** para concluir a configuração.
![Pronto para configurar](media\active-directory-aadconnect-federation-management\AdditionalDomain5.PNG)

## <a name="ad-fs-customization"></a>Personalização do AD FS

As secções seguintes fornecem detalhes sobre algumas das tarefas comuns que poderá ter de desempenhar quando personaliza a sua página de início de sessão no AD FS.

### Adicionar um logótipo da empresa personalizada ou uma ilustração<a name=customlogo></a>

Para alterar o logótipo da empresa que é apresentada na página **Iniciar sessão** , utilize o seguinte cmdlet do Windows PowerShell e sintaxe.

> [AZURE.NOTE] As dimensões recomendadas para o logótipo são 260 x 35 @ 96 PPP com um tamanho de ficheiro não maior 10 KB.

    Set-AdfsWebTheme -TargetName default -Logo @{path="c:\Contoso\logo.PNG"}

> [AZURE.NOTE] O parâmetro *NomeDestino* é necessário. O tema predefinido que é disponibilizado com o AD FS é com o nome predefinido.


### Adicionar uma descrição de início de sessão<a name=addsignindescription></a>

Para adicionar uma descrição de início de sessão na página para a **página de início de sessão**, utilize o seguinte cmdlet do Windows PowerShell e sintaxe.

    Set-AdfsGlobalWebContent -SignInPageDescriptionText "<p>Sign-in to Contoso requires device registration. Click <A href='http://fs1.contoso.com/deviceregistration/'>here</A> for more information.</p>"

### Modificar AD FS afirmação regras<a name=modclaims></a>

AD FS suporta um idioma afirmação avançada que pode utilizar para criar regras de afirmações personalizadas. Para mais informações, consulte [A função da linguagem afirmação regra](https://technet.microsoft.com/library/dd807118.aspx).

As secções seguintes descrevem como pode escrever regras personalizadas para alguns cenários de sobre a Azure AD e Federação do AD FS.

#### <a name="immutable-id-conditional-on-a-value-being-present-in-the-attribute"></a>ID imutáveis condicional relativa a um valor a ser presentes no atributo

Ligação do Azure AD permite-lhe especificar um atributo que será utilizado como uma âncora de origem quando objetos são sincronizados para Azure AD. Se o valor no atributo personalizado não estiver vazio, poderá pretender uma imutáveis afirmação de ID de problema. Por exemplo, poderá selecionar **ms-ds-consistencyguid** como o atributo para a âncora de origem e pretende emitir **ImmutableID** como se fosse **ms-ds-consistencyguid** caso o atributo tem um valor contra-lo. Se não existirem nenhum valor contra o atributo, emitir **GUID de objecto** como se fosse o ID da imutáveis.  Pode construir o conjunto de regras de afirmação personalizada, tal como descrito na secção seguinte.

**Regra 1: Atributos da consulta**

    c:[Type == "http://schemas.microsoft.com/ws/2008/06/identity/claims/windowsaccountname"]
    => add(store = "Active Directory", types = ("http://contoso.com/ws/2016/02/identity/claims/objectguid", "http://contoso.com/ws/2016/02/identity/claims/msdsconcistencyguid"), query = "; objectGuid,ms-ds-consistencyguid;{0}", param = c.Value);

Esta regra, que está a consultar os valores de **ms-ds-consistencyguid** e **GUID de objecto** para o utilizador a partir do Active Directory. Altere o nome do arquivo para nome de um arquivo adequado na sua implementação do AD FS. Também altere o tipo de afirmações a um tipo de afirmações adequada para a sua Federação tal como foi definido para **GUID de objecto** e **ms-ds-consistencyguid**.

Além disso, ao utilizar a **Adicionar** e não o **problema**, que evite adicionar um problema de saída para a entidade e pode utilizar os valores como valores intermédios. Irá emitir afirmação numa regra posterior depois de estabelecer qual o valor para utilizar como o ID da imutáveis.

**Regra 2: Verificar se ms-ds-consistencyguid existe para o utilizador**

    NOT EXISTS([Type == "http://contoso.com/ws/2016/02/identity/claims/msdsconcistencyguid"])
    => add(Type = "urn:anandmsft:tmp/idflag", Value = "useguid");

Esta regra define um sinalizador temporário denominado **idflag** que está definido para **useguid** caso não haja nenhuma **ms-ds-concistencyguid** povoada para o utilizador. A lógica atrás este é o facto de que o AD FS não permite em afirmações vazias. Por isso, ao adicionar em afirmações http://contoso.com/ws/2016/02/identity/claims/objectguid e http://contoso.com/ws/2016/02/identity/claims/msdsconcistencyguid na regra 1, terminem em cima uma **msdsconsistencyguid** reclamar apenas se o valor é povoado para o utilizador. Se não for preenchido, vê o AD FS que terá um valor vazio e coloca-o imediatamente. Todos os objetos terá **GUID de objecto**, por isso essa afirmação sempre lá estejam após 1 de regra é executado.

**Regra 3: Emitir ms-ds-consistencyguid como se fosse imutáveis ID, se existir**

    c:[Type == "http://contoso.com/ws/2016/02/identity/claims/msdsconcistencyguid"]
    => issue(Type = "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier", Value = c.Value);

Esta é uma verificação **existentes** implícita. Se o valor para a afirmação não existir, em seguida, emitir que como se fosse o ID da imutáveis. O exemplo anterior utiliza a afirmação **nameidentifier** . Terá de alterar esta para o tipo de afirmação adequado para o ID imutáveis no seu ambiente.

**Regra de 4: Emitir GUID de objecto como se fosse imutáveis ID, se não existir ms-ds-consistencyGuid**

    c1:[Type == "urn:anandmsft:tmp/idflag", Value =~ "useguid"]
    && c2:[Type == "http://contoso.com/ws/2016/02/identity/claims/objectguid"]
    => issue(Type = "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier", Value = c2.Value);

Esta regra, que está a simplesmente dar o sinalizador temporário **idflag**. Decida se pretende emitir afirmação com base no seu valor.

> [AZURE.NOTE] A sequência destas regras é importante.

#### <a name="sso-with-a-subdomain-upn"></a>SSO com um subdomínio UPN

Pode adicionar mais do que um domínio federado, utilizando a ligação do Azure AD, tal como descrito em [Adicionar um novo domínio federado](active-directory-aadconnect-federation-management.md#add-a-new-federated-domain). Tem de modificar a afirmação UPN para que o ID de emissor corresponde ao domínio de raiz e não o subdomínio, porque o domínio de raiz federados abrange também o filho.

Por predefinição, a regra de afirmação para ID emissor está definida como:

    c:[Type
    == “http://schemas.xmlsoap.org/claims/UPN“]

    => issue(Type = “http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid“, Value = regexreplace(c.Value, “.+@(?<domain>.+)“, “http://${domain}/adfs/services/trust/“));

![Predefinição emissor ID afirmação](media\active-directory-aadconnect-federation-management\issuer_id_default.png)

A regra predefinida simplesmente leva-o até o sufixo UPN e utiliza-a na afirmação de ID de emissor. Por exemplo, João é um utilizador no sub.contoso.com e contoso.com for Federado com Azure AD. João introduz john@sub.contoso.com como o nome de utilizador ao iniciar sessão no Azure AD e o ID de emissor predefinido reclamar regra no AD FS trata-lo da seguinte forma.

    c:[Type
    == “http://schemas.xmlsoap.org/claims/UPN“]

    => issue(Type = “http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid“, Value = regexreplace(john@sub.contoso.com, “.+@(?<domain>.+)“, “http://${domain}/adfs/services/trust/“));

**Reclamar valor:** http://sub.contoso.com/adfs/services/trust/

Para que apenas o domínio de raiz o valor de afirmação emissor, altere a regra afirmação para fazer corresponder o seguinte.

    c:[Type == “http://schemas.xmlsoap.org/claims/UPN“]

    => issue(Type = “http://schemas.microsoft.com/ws/2008/06/identity/claims/issuerid“, Value = regexreplace(c.Value, “^((.*)([.|@]))?(?<domain>[^.]*[.].*)$”, “http://${domain}/adfs/services/trust/“));

## <a name="next-steps"></a>Próximos passos

Saiba mais sobre as [Opções de início de sessão no utilizador](active-directory-aadconnect-user-signin.md).
