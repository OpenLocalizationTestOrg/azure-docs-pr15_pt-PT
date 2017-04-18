<properties
    pageTitle="O que é a inscrição personalizada no Azure? | Microsoft Azure"
    description="Uma descrição geral personalizada a inscrição no Azure, como gerir o processo de inscrição e como assumir o controlo um nome de domínio DNS."
    services="active-directory"
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


# <a name="what-is-self-service-signup-for-azure"></a>O que é a inscrição personalizada no Azure?

Este tópico explica o processo de inscrição personalizada e como assumir o controlo um nome de domínio DNS.  

## <a name="why-use-self-service-signup"></a>Porquê utilizar inscrição personalizada?

- Obtenha os clientes a serviços que pretendem mais rápido.
- Crie ofertas baseada em correio eletrónico para um serviço.
- Crie fluxos de inscrição no baseada em correio eletrónico que rapidamente permitir que os utilizadores criem identidades utilizando os respetivos aliases de e-mail de trabalho fácil de lembrar.
- Não geridos directórios Azure podem ser efectuados para directórios geridos mais tarde e ser reutilizados para outros serviços.

## <a name="terms-and-definitions"></a>Termos e definições

+ **Self-Service inscrever-se**: Este é o método pelo qual um utilizador se inscreve para um serviço na nuvem e tem uma identidade criada automaticamente-los no Azure Active Directory (Azure AD) com base no seu domínio de e-mail.
+ **Diretório do Azure que não é gerido**: Este é o directório onde essa identidade é criada. Num diretório que não é gerido é um diretório que não tem nenhum administrador global.
+ **Utilizador verificado por correio eletrónico**: Este é um tipo de conta de utilizador no Azure AD. Um utilizador que tem uma identidade criada automaticamente após a inscrição para uma oferta de gestão personalizada é conhecido como um utilizador verificado por e-mail. Um utilizador verificado por correio eletrónico é um membro de um diretório etiquetado com creationmethod normal = EmailVerified.

## <a name="user-experience"></a>Experiência do utilizador

Por exemplo, vamos supor um utilizador cujo o e-mail é Dan@BellowsCollege.com recebe sensíveis a maiúsculas e ficheiros por correio eletrónico. Os ficheiros tem sido protegidos pela gestão de direitos do Azure (Azure RMS). Mas organização de Duarte, fole universidade, não tem inscreveu no Azure RMS nem tem-implementado RMS do Active Directory. Neste caso, Duarte pode inscrever-se para uma subscrição gratuita do RMS para indivíduos para ler os ficheiros protegidos.

Se Duarte é o primeiro utilizador com um endereço de e-mail a partir do BellowsCollege.com para se inscrever este Self-Service perguntar se, em seguida, será criado um directory que não é gerido para BellowsCollege.com no Azure AD. Se outros utilizadores a partir do domínio BellowsCollege.com inscrever-se para esta oferta ou uma oferta de gestão personalizada semelhante, também terão contas de utilizador verificado por correio eletrónico criadas no diretório mesmo que não é gerido no Azure.

## <a name="admin-experience"></a>Experiência de administrador

Um administrador proprietária o nome de domínio DNS de um diretório Azure que não é gerido pode assumir o controlo ou intercalar diretório após que prova que é o proprietário. As secções seguintes explicam a experiência de administração mais detalhadamente, mas Eis um resumo:

- Quando der através de um diretório Azure que não é gerido, basta torna-se o administrador global do directório que não é gerido. Isto é, por vezes chamado uma aquisição interna.
- Quando a impressão em série num diretório Azure que não é gerido, adicionar o nome de domínio DNS do diretório não gerido ao diretório da sua Azure gerido e é criado um mapeamento de recursos de utilizadores por isso, os utilizadores podem continuar para aceder aos serviços sem interrupções. Isto é, por vezes chamado uma aquisição externa.

## <a name="what-gets-created-in-azure-active-directory"></a>O que é criado no Azure Active Directory?

#### <a name="directory"></a>diretório

- Um Azure Active directory do domínio é criado, um diretório por domínio.
- Diretório do Azure AD não tem nenhum administrador global.

#### <a name="users"></a>Utilizadores

- Para cada utilizador que se inscreve, um objeto de utilizador é criado no diretório do Azure AD.
- Cada objeto do utilizador está marcado como externo.
- Cada utilizador é dado acesso ao serviço que se inscreveu.

### <a name="how-do-i-claim-a-self-service-azure-ad-directory-for-a-domain-i-own"></a>Como requerer uma Azure AD personalizada para um domínio que sou o proprietário do directório?

Pode requerer uma Azure AD personalizada directório executando validação do domínio. Validação do domínio prova que é proprietário do domínio através da criação de registos DNS.

Existem duas formas de executar uma aquisição DNS de um diretório do Azure AD:

- aquisição interna (Admin detetar num diretório Azure guardem e pretende transformar um diretório gerido)
- aquisição externa (tentativas de administrador para adicionar um novo domínio à sua directório Azure gerido)

Poderá estar interessado validar o que é o proprietário um domínio porque está a efetuar através de um diretório que não é gerido depois de um utilizador executado inscrição personalizado no ou que poderá adicionar um novo domínio para um directório gerido existente. Por exemplo, tiver um domínio com o nome contoso.com e que pretende adicionar um novo domínio denominado contoso.co.uk ou contoso.uk.

## <a name="what-is-domain-takeover"></a>O que é aquisição de domínio?  

Esta secção aborda como validar o que é proprietário de um domínio

### <a name="what-is-domain-validation-and-why-is-it-used"></a>O que é a validação do domínio e por que motivo é utilizada?

Para executar operações de um diretório, Azure AD requer que valide o que é o proprietário do domínio DNS.  Validação do domínio permite-lhe requerer que o diretório e quer promover o diretório de gestão personalizada para um diretório gerido, ou intercalar o diretório de gestão personalizada num directório gerido existente.

## <a name="examples-of-domain-validation"></a>Exemplos de validação do domínio

Existem duas formas de executar uma aquisição DNS de um diretório:

+ aquisição interna (por exemplo, um administrador detetar um diretório de gestão personalizada, que não é gerido e pretende transformar directório gerido)
+ aquisição externa (por exemplo, um administrador tenta adicionar um novo domínio para um diretório gerido)

### <a name="internal-takeover---promote-a-self-service-unmanaged-directory-to-be-a-managed-directory"></a>Aquisição interna - promover um diretório de que não é gerido, personalizado para ser um diretório gerido

Quando fizer aquisição interna, diretório obtém convertido num diretório que não é gerido um diretório gerido. Tem de concluir a validação de nome de domínio DNS, onde criar um registo MX ou um registo TXT na zona de DNS. Essa ação:

+ Validar o que é o proprietário do domínio
+ Faz com que o diretório gerido
+ Faz com que o administrador global do directório

Digamos que um administrador de TI do fole universitário descobre que os utilizadores a escola se inscreveram ofertas de gestão personalizada. Como o proprietário do DNS registado nome BellowsCollege.com, o administrador de TI pode validar o que é o proprietário do nome de DNS no Azure e, em seguida, assumir o diretório que não é gerido. Diretório, se tornar um diretório gerido e o administrador de TI está atribuído a função de administrador global para o directório BellowsCollege.com.

### <a name="external-takeover---merge-a-self-service-directory-into-an-existing-managed-directory"></a>Aquisição externa - intercalar um diretório de gestão personalizada num directório gerido existente

Numa aquisição externa, já tem um diretório gerido e pretende que todos os utilizadores e grupos num diretório não gerida para aderir a esse directório gerido, em vez de duas próprias separam diretórios.

Como administrador de um diretório gerido, adicionar um domínio e esse domínio acontece ter num diretório que não é gerido associado.

Por exemplo, vamos supor for um administrador de TI e que já tem um diretório gerido para Contoso.com, um nome de domínio está registado para a sua organização. Descobrir a que os utilizadores da sua organização tiverem executado inscrição personalizada para cima para uma oferta utilizando o nome de domínio de e-mail user@contoso.co.uk, que é o outro nome do domínio que possui da sua organização. Esses utilizadores tem atualmente contas num diretório para contoso.co.uk que não é gerido.

Não pretende gerir dois directórios separados, para intercalar o diretório que não é gerido contoso.co.uk diretório da sua IT gerido existente para contoso.com.

Aquisição externa segue o mesmo processo de validação de DNS como aquisição interno.  Diferença estar: os utilizadores e serviços estão novamente mapeados para o diretório IT gerido.

#### <a name="whats-the-impact-of-performing-an-external-takeover"></a>O que é o impacto das efetuar uma aquisição externa?

Com uma aquisição externa, um mapeamento de recursos de utilizadores é criado para que os utilizadores podem continuar para aceder aos serviços sem interrupções. Muitas aplicações, incluindo o RMS para indivíduos, processam o mapeamento de recursos de utilizadores e os utilizadores podem continuar a aceder desses serviços sem alteração. Se uma aplicação não processar eficientemente o mapeamento de recursos de utilizadores, aquisição externa pode estar bloqueada explicitamente impedir que os utilizadores uma experiência de baixa.

#### <a name="directory-takeover-support-by-service"></a>suporte de públicas de aquisição Directory pelo serviço

Os seguintes serviços atualmente, aquisição:

- RMS


Os seguintes serviços mais cedo serão suporte aquisição:

- Obter

A seguinte não faça e necessitam de ação de administração adicionais de migrar dados de utilizador após uma aquisição externa.

- OneDrive do SharePoint


## <a name="how-to-perform-a-dns-domain-name-takeover"></a>Como efetuar uma aquisição de nome de domínio DNS

Tem algumas opções para saber como efetuar uma validação do domínio (e efetue uma aquisição, se pretender):

1.  Portal de gestão do Azure

    Uma aquisição é acionada ao efetuar uma adição do domínio.  Se já existe um diretório do domínio, terá a opção para executar uma aquisição externa.

    Inicie sessão no portal do Azure com as suas credenciais.  Navegue para o seu diretório existente e, em seguida, para **Adicionar um domínio**.

2.  Office 365

    Pode utilizar as opções na página [Gerir domínios](https://support.office.com/article/Navigate-to-the-Office-365-Manage-domains-page-026af1f2-0e6d-4f2d-9b33-fd147420fac2/) no Office 365 para trabalhar com o seu domínios e registos de DNS. Consulte o artigo [verificar o seu domínio no Office 365](https://support.office.com/article/Verify-your-domain-in-Office-365-6383f56d-3d09-4dcb-9b41-b5f5a5efd611/).

3.  Windows PowerShell

    Os passos seguintes são necessários para realizar uma validação através do Windows PowerShell.

    Passo    |   Cmdlet para utilizar
    ------- | -------------
    Criar um objeto de credenciais | Get-credenciais
    Ligar ao Azure AD | MsolService ligar
    obter uma lista de domínios   | Get-MsolDomain
    Criar um desafio  | Get-MsolDomainVerificationDns
    Criar o registo DNS   | Faça o seguinte no servidor DNS
    Verifique se o desafio    | MsolEmailVerifiedDomain confirmar

Por exemplo:

1. Ligar ao Azure AD utilizando as credenciais utilizadas para responder à oferta de gestão personalizada: Importar módulo MSOnline $msolcred = get-credencial ligar-msolservice-$msolcred de credenciais

2. Obter uma lista de domínios:

    Get-MsolDomain

3. Em seguida, execute o cmdlet Get-MsolDomainVerificationDns para criar um desafio:

    Get-MsolDomainVerificationDns – NomeDomínio *your_domain_name* – modo DnsTxtRecord

    Por exemplo:

    Get-MsolDomainVerificationDns – NomeDomínio contoso.com – modo DnsTxtRecord

4. Copie o valor (o desafio) que é devolvido a partir deste comando.

    Por exemplo:

    MS = 32DD01B82C05D27151EA9AE93C5890787F0E65D9

5. No espaço de nomes DNS público, crie um registo txt DNS que contém o valor que copiou no passo anterior.

    O nome para este registo é o nome do domínio principal, por isso, se criar este registo de recursos utilizando a função de DNS do Windows Server, deixe a colagem nome registo em branco e apenas o valor na caixa de texto

6. Execute o cmdlet confirmar MsolDomain para verificar o desafio:

    MsolEmailVerifiedDomain confirmar - DomainName *your_domain_name*

    Por exemplo:

    MsolEmailVerifiedDomain confirmar - DomainName contoso.com

Um desafio bem sucedido regressa à linha de comandos sem um erro.

## <a name="how-do-i-control-self-service-settings"></a>Como posso controlar as definições de gestão personalizada?

Têm os administradores dois controlos de gestão personalizada hoje. Podem controlar:

- Se é ou não podem aderir utilizadores diretório por correio eletrónico.
- Se é ou não utilizadores podem atribuir licenças a próprios para aplicações e serviços.


### <a name="how-can-i-control-these-capabilities"></a>Como posso controlar estas capacidades?

Um administrador pode configurar estas capacidades utilizando os parâmetros do cmdlet Set-MsolCompanySettings estes Azure AD:

+ **AllowEmailVerifiedUsers** controla se um utilizador pode criar ou participar num diretório que não é gerido. Se definir esse parâmetro para $false, os utilizadores não verificado por correio eletrónico podem participar diretório.
+ **AllowAdHocSubscriptions** controla a capacidade para os utilizadores efetuar a inscrição personalizada para cima. Se definir esse parâmetro para $false, os utilizadores não podem executar a inscrição personalizada.


### <a name="how-do-the-controls-work-together"></a>Como é que os controlos funcionam em conjunto?

Estes dois parâmetros podem ser utilizados em conjunto para definir mais controlos precisos sobre inscrição personalizada para cima. Por exemplo, o seguinte comando permitirá que os utilizadores executar a inscrição personalizada para cima, mas apenas se esses utilizadores já tem uma conta no Azure AD (por outras palavras, os utilizadores que seriam necessário uma conta verificado por e-mail seja criado não é possível executar inscrição personalizada para cima):

    Set-MsolCompanySettings -AllowEmailVerifiedUsers $false -AllowAdHocSubscriptions $true

O fluxograma seguinte explica todas as combinações diferentes para estes parâmetros e as condições resultantes do diretório e inscrição personalizada para cima.

![][1]

Para obter mais informações e exemplos de como utilizar estes parâmetros, consulte o artigo [Set-MsolCompanySettings](https://msdn.microsoft.com/library/azure/dn194127.aspx).

## <a name="see-also"></a>Consulte também

-  [Como instalar e configurar PowerShell do Azure](../powershell-install-configure.md)

-  [Azure PowerShell](https://msdn.microsoft.com/library/azure/jj156055.aspx)

-  [Referência do Cmdlet Azure](https://msdn.microsoft.com/library/azure/jj554330.aspx)

-  [Set-MsolCompanySettings](https://msdn.microsoft.com/library/azure/dn194127.aspx)

<!--Image references-->
[1]: ./media/active-directory-self-service-signup/SelfServiceSignUpControls.png
