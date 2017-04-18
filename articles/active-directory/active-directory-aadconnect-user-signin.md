<properties
    pageTitle="Ligação do Azure AD: Utilizador início de sessão | Microsoft Azure"
    description="Azure AD Connect utilizador iniciar sessão para as definições personalizadas."
    services="active-directory"
    documentationCenter=""
    authors="billmath"
    manager="femila"
    editor="curtand"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/16/2016"
    ms.author="billmath"/>



# <a name="azure-ad-connect-user-sign-on-options"></a>Azure AD ligar utilizador Sign-on opções

Ligação do Azure AD permite que os utilizadores iniciar sessão nuvem e no local recursos utilizando as mesmas palavras-passe. Este artigo descreve os conceitos chave para cada modelo de identidade para ajudar a escolher a identidade que pretende utilizar para o início de sessão no Azure AD.

Se já está familiarizado com o modelo de identidade do Azure AD e quer saber mais sobre um método específico, basta clicar por baixo no tópico adequado.

* [Sincronização de palavra-passe](#password-synchronization)
* [SSO federada (com ADFS)](#federation-using-a-new-or-existing-ad-fs-in-windows-server-2012-r2-farm)


## <a name="choosing-a-user-sign-in-method"></a>Escolher um método de utilizador de início de sessão
Para a maioria das organizações que pretendem apenas permitir que o utilizador iniciar sessão Office 365, SaaS aplicações e outros Azure AD baseados recursos, a opção de sincronização de palavra-passe predefinida é recomendada.
Algumas organizações, no entanto, tem razões específicas para utilizar o início de sessão federado opção tal como o AD FS.  Estes incluem:

- Sua organização já tiver o AD FS ou uma festa de fornecedor de Federação implementado de 3
- A política de segurança impede a sincronizar hashes de palavra-passe para a nuvem
- Exigir que os utilizadores experiência totalmente integrada SSO (sem a palavra-passe adicional pede-lhe) quando aceder a recursos de nuvem a partir do domínio associado máquinas na rede da empresa
- Exigir a algumas funcionalidades específicas de que AD FS tem
    - Autenticação multifator de no local com um fornecedor de terceiros ou smart cards (Saiba mais sobre outros fornecedores MFA do AD FS no Windows Server 2012 R2)
    - Funcionalidades de integração do Active Directory como o bloqueio de conta contornos ou política de horas de palavra-passe e trabalho do AD
    - Condicional acesso a ambos no local e na nuvem recursos utilizando o registo de dispositivo, associação do Azure AD ou as políticas da Intune MDM

### <a name="password-synchronization"></a>Sincronização de palavra-passe
Com a sincronização de palavra-passe, hashes de palavras-passe de utilizador são sincronizados a partir do seu Active Directory no local para Azure AD.  Quando as palavras-passe são alteradas ou repor no local, as palavras-passe novas são sincronizadas imediatamente à Azure AD para que os utilizadores podem utilizar sempre a mesma palavra-passe para recursos de nuvem à medida que são feitas no local.  As palavras-passe nunca são enviadas para o Azure AD nem armazenadas no Azure AD em texto simples.
Sincronização de palavra-passe pode ser utilizada juntamente com palavra-passe de escrita de retorno para ativar self repor a palavra-passe serviço no Azure AD.

<center>![Nuvem](./media/active-directory-aadconnect-user-signin/passwordhash.png)</center>

[Obter mais informações sobre a sincronização de palavra-passe](active-directory-aadconnectsync-implement-password-synchronization.md)


### <a name="federation-using-a-new-or-existing-ad-fs-in-windows-server-2012-r2-farm"></a>Utilizar o AD FS novo ou existente no Windows Server 2012 R2 farm de servidores de Federação
Com federados início de sessão, os utilizadores podem iniciar sessão Azure AD com base serviços com palavra-passe no local e contendo, enquanto na rede da empresa, sem ser necessário introduzir novamente a palavra-passe.  A opção de federação com o AD FS permite-lhe implementar uma nova ou especificar um AD FS existente do Windows Server 2012 R2 farm.  Se optar por especificar um farm existente, a ligação do Azure AD irá configurar a fidedignidade entre o seu farm e Azure AD para que os utilizadores possam iniciar.

<center>![Nuvem](./media/active-directory-aadconnect-user-signin/federatedsignin.png)</center>

#### <a name="to-deploy-federation-with-ad-fs-in-windows-server-2012-r2-you-will-need-the-following"></a>Para implementar a Federação com o AD FS no Windows Server 2012 R2, terá o seguinte

Se estiver a implementar uma nova farm:

- Um servidor do Windows Server 2012 R2 para o servidor de Federação.
- Um servidor do Windows Server 2012 R2 para o Proxy de aplicação Web.
- um ficheiro. pfx com um certificado SSL para o seu nome de serviço de Federação pretendido, tal como fs.contoso.com.

Se estiver a implementar uma nova farm ou utilizar um farm existente:

- Credenciais de administrador local nos seus servidores de Federação.
- Credenciais de administrador local no qualquer grupo de trabalho (sem domínio associado) servidores no qual pretende implementar a função de Proxy de aplicação Web.
- O computador no qual pode executa o assistente tem de ser possível ligar a quaisquer outros computadores nos quais pretende instalar o AD FS ou Proxy de aplicação Web através de gestão remota do Windows.

[Configurar o SSO com o AD FS](./aad-connect/active-directory-aadconnect-get-started-custom.md#configuring-federation-with-ad-fs) 

#### <a name="sign-on-using-an-earlier-version-of-ad-fs-or-a-third-party-solution"></a>Iniciar sessão com uma versão anterior do AD FS ou uma terceira parte solução

Se já tiver configurado o início de sessão da nuvem sobre como utilizar uma versão anterior do AD FS (como o AD FS 2.0) ou um fornecedor de Federação de terceiros, pode optar por ignorar o início de sessão do utilizador na configuração através da ligação do Azure AD.  Isto permite-lhe aceder a mais recente sincronização e outras capacidades da ligação do Azure AD enquanto ainda utilizar a sua solução existente para iniciar sessão no.

[Lista de compatibilidade de Federação de terceiros do Azure AD](./active-directory-aadconnect-federation-compatibility.md)

## <a name="user-sign-in-and-user-principal-name-upn"></a>Utilizador com sessão e nome principal de utilizador (UPN)

### <a name="understanding-user-principal-name"></a>Nome principal de utilizador Noções sobre

No Active Directory, o sufixo UPN predefinido é o nome DNS do domínio no qual a conta de utilizador criada. Na maioria dos casos, este é o nome de domínio registado como o domínio de empresa na Internet. No entanto, pode adicionar mais sufixos UPN utilizando e fidedignidades do Active Directory Domains.

O UPN do utilizador é o formato username@domai. Por exemplo, para um active directory contoso.com utilizador João pode ter UPN john@contoso.com. O UPN do utilizador é baseado no RFC 822. Apesar de correio eletrónico e UPN partilharem o mesmo formato, o valor de UPN para um utilizador pode ou não pode ser igual ao endereço de e-mail do utilizador.

### <a name="user-principal-name-in-azure-ad"></a>Nome principal de utilizador no Azure AD

Assistente do Azure AD Connect irá utilizar o atributo userPrincipalName ou permitem-lhe especificar o atributo (na instalação personalizada) para ser utilizado nos locais como nome de utilizador principal no Azure AD. Este é o valor que será utilizado para iniciar sessão no Azure AD. Se o valor do atributo de nome principal de utilizador não correspondem a um domínio verificado no Azure AD, em seguida, Azure AD irá substituí-la por defeito. onmicrosoft.com valor.

Cada directório no Azure Active Directory é fornecido com um nome de domínio incorporado no contoso.onmicrosoft.com formulário que lhe permite começar a utilizar o Azure ou outros serviços da Microsoft. Pode melhorar e simplificar a experiência de início de sessão utilizar domínios personalizados. Para obter informações sobre o domínio personalizado nomes no Azure AD e como verificar um domínio, leia [Adicionar o seu nome de domínio personalizado para o Azure Active Directory](active-directory-add-domain.md#add-your-custom-domain-name-to-azure-active-directory)

## <a name="azure-ad-sign-in-configuration"></a>Azure AD início de sessão no configuração

### <a name="azure-ad-sign-in-configuration-with-azure-ad-connect"></a>Azure AD início de sessão no configuração com ligação do Azure AD
Azure AD início de sessão no experiência é dependente se é possível fazer corresponder o sufixo de nome principal de utilizador de um utilizador que está a ser sincronizado com um dos domínios personalizados verificados no diretório Azure AD Azure AD. Ligação do Azure AD fornece ajuda durante a configuração do Azure AD início de sessão no definições, para que a experiência de início de sessão do utilizador nuvem é semelhante à experiência no local. Ligação do Azure AD lista os sufixos UPN definidos para o domínio (s) e tenta fazer corresponder um domínio personalizado no Azure AD e ajuda-o com a ação adequada que necessita para ser encaminhado.
A página de início de sessão no Azure AD lista saída os sufixos UPN definidos para o Active Directory no local e apresenta o estado correspondente contra cada sufixo. Os valores de estado podem ser um da abaixo:

| Estado | Descrição | É necessária qualquer ação |
|:----|:----|:----|
|Verificado| Ligação do Azure AD encontrou que uma correspondência verificado o domínio no Azure AD. Todos os utilizadores para este domínio possam iniciar sessão com as suas credenciais no local| Não é necessária nenhuma ação |
|Não verificados| Ligação do Azure AD foi possível encontrar um domínio personalizado correspondente no Azure AD mas não é verificado. Será alterado sufixo UPN dos utilizadores deste domínio predefinidos. onmicrosoft.com sufixo após a sincronização caso não é verificado o domínio. | Verifique o domínio personalizado no Azure AD. [Saiba mais](./active-directory-add-domain.md#verify-the-domain-name-with-azure-ad) |  
|Não adicionado| Ligação do Azure AD não foi possível encontrar um domínio personalizado correspondente ao sufixo UPN. Será alterado sufixo UPN de utilizadores a partir deste domínio predefinidos. onmicrosoft.com se o domínio não for adicionado e verifeid no Azure.| Adicionar e verificar um domínio personalizado correspondente ao sufixo UPN [Obter mais informações](./active-directory-add-domain.md)|

Azure AD início de sessão na página lista suffix(s) o UPN definida para o Active Directory no local e o correspondente domínio personalizado no Azure AD com o estado de verificação atual. Na instalação personalizada, agora pode selecionar o atributo nome principal de utilizador na página de **início de sessão no Azure AD** .

![Azure AD início de sessão na página](./media/active-directory-aadconnect-user-signin/custom_azure_sign_in.png)

Pode clicar no botão Atualizar novamente obter o estado mais recente dos domínios personalizados a partir do Azure AD.

###<a name="selecting-attribute-for-user-principal-name-in-azure-ad"></a>Selecionar o atributo nome principal de utilizador no Azure AD

UserPrincipalName - o atributo userPrincipalName é o atributo que os utilizadores vão usar quando estes início de sessão no Azure AD e no Office 365. Os domínios utilizados, também conhecido como o-sufixo UPN, deverão ser verificados no Azure AD antes dos utilizadores estão sincronizados. Recomendamos vivamente para manter o userPrincipalName atributo predefinido. Se este atributo é não encaminháveis e não pode ser verificado, em seguida, é possível selecionar outro atributo, por exemplo de correio eletrónico, como o atributo mantém o ID de início de sessão no. Isto é conhecido como ID alternativo. O valor do atributo ID alternativo deve segui-lo a norma RFC822. Um ID alternativo pode ser utilizado com palavra-passe único início de sessão (SSO) e Federação SSO como a solução de início de sessão.

>[AZURE.NOTE] Utilizar um ID alternativo não é compatível com todas as cargas de trabalho do Office 365. Para mais informações, consulte [Configurar o ID de início de sessão alternativo](https://technet.microsoft.com/library/dn659436.aspx).

#### <a name="different-custom-domain-states-and-effect-on-azure-sign-in-experience"></a>Estados de domínio personalizado diferente e efeito sobre a experiência de início de sessão no Azure
É muito importante compreender a relação entre os Estados de domínio personalizado no seu diretório do Azure AD e os sufixos UPN definida no local. Diga-de percorrer as diferentes possíveis Azure início de sessão no experiências quando estiver a configurar sycnhronization utilizando o construtor do Azure AD.

Para obter as informações abaixo, diga-nos partem do princípio de que temos em questão com o contoso.com de sufixo UPN utilizada no diretório no local como parte do UPN, por exemplo user@contoso.com.

###### <a name="express-settings--password-synchronization"></a>Definições de Express / sincronização de palavra-passe
| Estado         | Efeito na experiência de utilizador Azure iniciar sessão |
|:-------------:|:----------------------------------------|
| Não adicionado | Neste caso não domínio personalizado para contoso.com foi adicionado no diretório do Azure AD. Os utilizadores que têm o UPN no local com sufixo @contoso.com, não poderão utilizar o UPN no local para iniciar sessão no Azure. Em vez disso, terá a utilizar um novo UPN fornecido-los por Azure AD adicionando o sufixo para directório do Azure AD predefinido. Por exemplo, se estiver a sincronizar os utilizadores Azure AD diretório azurecontoso.onmicrosoft.com, em seguida, o utilizador no local user@contoso.com será dada um UPN douser@azurecontoso.onmicrosoft.com|
| Não verificados | Neste caso temos contoso.com um domínio personalizado adicionado no diretório do Azure AD, no entanto, não tenha verificado. Se prosseguir com a sincronização de utilizadores sem verificar o domínio, em seguida, os utilizadores serão atribuídos um novo UPN por Azure AD tal como o que tinha num cenário de 'Não adicionados'.|
| Verificado | Neste caso temos contoso.com um domínio personalizado, já adicionado e verificada no Azure AD para o sufixo UPN. Os utilizadores poderão utilizar o local principal nome de utilizador, por exemplo, user@contoso.com, para o início de sessão no Azure depois de estes são sincronizadas com o Azure AD|

###### <a name="ad-fs-federation"></a>Federação do AD FS
Não é possível criar uma federação com as predefinições. onmicrosoft.com no Azure AD ou um domínio personalizado não verificado no Azure AD. Quando estiver a executar o Assistente de ligação do Azure AD, se selecionar um domínio não verificado para criar uma federação com, em seguida, ligação do Azure AD irá pedir-lhe com os registos necessários para ser criado onde o seu DNS está alojado do domínio. Para mais informações consulte [aqui](active-directory-aadconnect-get-started-custom.md#verify-the-azure-ad-domain-selected-for-federation).

Se tiver selecionado a opção início de sessão do utilizador como "Federação com o AD FS", em seguida, tem de ter um domínio personalizado para continuar com a criação de uma federação no Azure AD. Para os nossos debate, isto significa que temos contoso.com um domínio personalizado adicionado no diretório do Azure AD.

| Estado         | Efeito na experiência de utilizador Azure iniciar sessão |
|:-------------:|:----------------------------------------|
| Não adicionado | Neste caso ligação do Azure AD não conseguiu encontrar um domínio personalizado correspondente para o contoso.com de sufixo UPN no diretório do Azure AD. Tem de adicionar um domínio personalizado contoso.com se precisar de utilizadores para iniciar sessão utilizando o AD FS com o respectivo UPN no local, como user@contoso.com.|
| Não verificados | Neste caso ligação do Azure AD irá pedir-lhe com adequado para obter detalhes sobre como verificar o seu domínio numa fase posterior|
| Verificado | Neste caso, pode prosseguir com a configuração sem qualquer acção|  

## <a name="changing-user-sign-in-method"></a>Alterar o método de sessão do utilizador

Pode alterar o método de utilizador de início de sessão a partir de Federação para utilizar a está disponível de tarefas na ligação do Azure AD após a configuração inicial do utilizando o Assistente de ligação do Azure AD de sincronização de palavra-passe. Execute novamente o Assistente de ligação do Azure AD e será apresentada com uma lista de tarefas que pode efetuar. Selecione **alterar utilizador iniciar sessão** a partir da lista de tarefas. 

![Alterar a sessão do utilizador"](./media/active-directory-aadconnect-user-signin/changeusersignin.png)

Na página seguinte, será pedido para fornecer as credenciais para Azure AD.

![Ligar ao Azure AD](./media/active-directory-aadconnect-user-signin/changeusersignin2.png)

Na página de **início de sessão no utilizador** , selecione a **Sincronização de palavra-passe**. Isto irá alterar o diretório de federado para uma gerida.

![Ligar ao Azure AD](./media/active-directory-aadconnect-user-signin/changeusersignin3.png)

>[AZURE.NOTE] Se estiver a fazer apenas um parâmetro temporário à sincronização de palavra-passe, em seguida, selecione **não converta contas de utilizador**. Não verificar a opção, será direcionado para a conversão de cada utilizador federado e pode demorar algumas horas.
  
## <a name="next-steps"></a>Próximos passos
Saiba mais sobre como [integrar a suas identidades no local com o Azure Active Directory](active-directory-aadconnect.md).

Saber mais sobre [ligação do Azure AD: estruturar conceitos](active-directory-aadconnect-design-concepts.md)


