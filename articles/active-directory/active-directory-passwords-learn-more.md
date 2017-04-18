<properties
    pageTitle="Saiba mais: Gestão de palavra-passe AD Azure | Microsoft Azure"
    description="Advanced tópicos em gestão de palavra-passe do Azure AD, incluindo a palavra-passe escrita não consolidada funcionamento, segurança de repetição de escrita de palavra-passe, como a palavra-passe repor funciona portal e dados que são utilizados pelo reposição de palavra-passe."
    services="active-directory"
    documentationCenter=""
    authors="asteen"
    manager="femila"
    editor="curtand"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/12/2016"
    ms.author="asteen"/>

# <a name="learn-more-about-password-management"></a>Saiba mais sobre a gestão de palavra-passe

> [AZURE.IMPORTANT] **Está aqui porque está a ter problemas ao iniciar sessão?** Se Sim, [Eis como pode alterar e repor a sua própria palavra-passe](active-directory-passwords-update-your-own-password.md).

Se já tiver implementado a gestão de palavra-passe ou são apenas está à procura para saber mais sobre técnicas nitty que abrasivo de como funciona antes de implementar, esta secção irá dar-lhe uma boa visão geral dos conceitos técnicos atrás o serviço. Vamos abordar o seguinte procedimento:

* [**Descrição geral de repetição de escrita de palavra-passe**](#password-writeback-overview)
  - [Como funciona a palavra-passe repetição de escrita](#how-password-writeback-works)
  - [Cenários de palavra-passe de escrita não consolidada suportados](#scenarios-supported-for-password-writeback)
  - [Modelo de segurança de repetição de escrita de palavra-passe](#password-writeback-security-model)
* [**Como a palavra-passe é que reponho a portal trabalho?**](#how-does-the-password-reset-portal-work)
  - [Quais os dados que são utilizados pelo reposição de palavra-passe?](#what-data-is-used-by-password-reset)
  - [Como aceder a palavra-passe repor os dados para os seus utilizadores](#how-to-access-password-reset-data-for-your-users)

## <a name="password-writeback-overview"></a>Descrição geral de repetição de escrita de palavra-passe
Repetição de escrita de palavra-passe é um componente do [Azure Active Directory ligar-se](active-directory-aadconnect.md) que pode ser ativado e utilizado pelo subscritores do Azure Active Directory Premium. Para mais informações, consulte o artigo [Azure Active Directory edições](active-directory-editions.md).

Palavra-passe escrita não consolidada permite-lhe configurar o inquilino na nuvem para escrever palavras-passe novamente para si no local do Active Directory.  -Torna desnecessário um tenha de configurar e gerir uma solução de reposição personalizada de palavra-complicada no local e fornece uma maneira conveniente para os seus utilizadores para repor a palavra-passe no local onde quer que estiverem baseado na nuvem.  Continue a ler para algumas das principais funcionalidades do palavra-passe de escrita não consolidada:

- **Comentários zero atraso.**  Repetição de escrita de palavra-passe é uma operação síncrona.  Os utilizadores serão imediatamente notificados se a palavra-passe não satisfazer política ou não pôde ser repor ou alteradas por qualquer motivo.
- **Suporta a reposição de palavras-passe para os utilizadores que utilizem ou outras tecnologias de Federação do AD FS.**  Com palavra-passe de escrita não consolidada, desde que as contas de utilizador federado são sincronizadas no seu inquilino do Azure AD, eles poderão gerir as suas instalações AD as palavras-passe a partir da nuvem.
- **Suporta repor palavras-passe para utilizadores com a sincronização de hash de palavra-passe.** Quando o serviço de reposição de palavra-passe Deteta que uma conta de utilizador sincronizada está ativada para a sincronização de hash de palavra-passe, for reposta no local esta conta e palavra-passe de nuvem em simultâneo.
- **Suporta alterar palavras-passe a partir do painel de acesso e o Office 365.**  Quando federado ou sincronização de palavra-passe seria utilizadores vêm para alterar a palavra-passe expirada ou não expirou, podemos irá escrever essas palavras-passe novamente para o seu ambiente de AD local.
- **Suporta a ser escrito novamente as palavras-passe quando um administrador repô-los a partir do** [**Portal de gestão azure**](https://manage.windowsazure.com).  Sempre que um repõe administração seria de palavra-passe de um utilizador no [Portal de gestão do Azure](https://manage.windowsazure.com), se esse utilizador for federado ou sincronização de palavra-passe, podemos irá configurar a palavra-passe, que o administrador seleciona no seu AD local, também.  Isto atualmente não é suportado no Portal de administração do Office.
- **Impõe suas no local políticas de palavra-passe de AD.**  Quando um utilizador repõe essa palavra-passe, podemos Certifique-se de que satisfaz no local política AD antes de fazê-lo a esse diretório.  Isto inclui histórico, complexidade, idade, filtros de palavra-passe e outras restrições de palavra-passe que definiu no seu AD local.
- **Não necessita de todas as regras de firewall de entrada.**  Palavra-passe escrita não consolidada utiliza uma retransmissão Azure Service Bus como um canal de comunicação subjacente, que significa que não possui abrir quaisquer entradas portas na firewall para que esta funcionalidade trabalhar.
- **Não é suportada para contas de utilizador que existam dentro dos grupos protegidos no seu no local Active Directory.** Para mais informações sobre os grupos protegidas, consulte o artigo [contas protegida e grupos do Active Directory](https://technet.microsoft.com/library/dn535499.aspx).

### <a name="how-password-writeback-works"></a>Como funciona a palavra-passe repetição de escrita
Palavra-passe escrita não consolidada tem três componentes principais:

- Serviço de nuvem de reposição de palavra-passe (isto é também integrado nas páginas de alteração de palavra-passe do Azure AD)
- Reencaminhamento de Azure Service Bus inquilino específicas
- Ponto final de reposição de palavra-passe de no local

Estas encaixam conforme descrito a abaixo diagrama:

  ![][001]

Quando sincronizar uma palavra-passe ou federado hash seria utilizador é recebida para repor ou alterar dele palavra-passe na nuvem, ocorre o seguinte:

1.  Vamos verifique qual o tipo de palavra-passe que o utilizador tem.  Se o podemos ver que a palavra-passe é gerida no local, em seguida, podemos Certifique-se que o serviço de escrita não consolidada está a trabalhar.  Se for, podemos permitir que o utilizador continuar, se não, é podemos indicar ao utilizador que respetiva palavra-passe não é possível repor aqui.
2.  Em seguida, o utilizador transmite as portas de autenticação adequado e atinge ecrã Repor palavra-passe.
3.  O utilizador seleciona uma nova palavra-passe e confirma-lo.
4.  Após clicar em submeter, podemos encriptar a palavra-passe de texto simples com uma chave simétrica que foi criada durante o processo de configuração de escrita não consolidada.
5.  Após a encriptação a palavra-passe, podemos inclui-los numa carga de útil enviada através de um canal HTTPS para o seu reencaminhamento bus serviço específico de inquilino (que Recomendamos também configurados para durante o processo de configuração de escrita não consolidada).  Este reencaminhamento está protegido por uma palavra-passe gerada aleatoriamente que sabe que apenas a instalação no local.
6.  Assim que a mensagem atinge bus de serviço, o ponto final de reposição de palavra-passe é automaticamente sai para cima e vê que tem um pedido de reposição pendentes.
7.  O serviço, em seguida, procura o utilizador em questão, utilizando o atributo de âncora na nuvem.  Para esta pesquisa ser concluída com êxito, o objeto do utilizador tem de existir no espaço de conexão AD, deve estar ligada ao objeto MV correspondente e deve estar ligada para o objeto de conexão AAD correspondente. Por fim, por ordem para a sincronização encontrar esta conta de utilizador, a ligação de objeto do conector de AD para MV tem de ter a regra de sincronização `Microsoft.InfromADUserAccountEnabled.xxx` na ligação.  É necessária porque quando a chamada a partir da nuvem, o motor de sincronização utiliza o atributo cloudAnchor para procurar o objeto de espaço de conexão AAD, em seguida, segue a ligação novamente para o objeto MV e, em seguida, seguir a ligação novamente para o objeto de AD. Uma vez que podem existir vários objetos AD (floresta com várias) para o mesmo utilizador, o motor de sincronização depende de `Microsoft.InfromADUserAccountEnabled.xxx` ligação para escolher o correto.
8.  Assim que a conta de utilizador for encontrada, vamos tentar repor a palavra-passe diretamente na floresta AD adequada.
9.  Se a operação de conjunto de palavra-passe for bem sucedida, vamos indicar ao utilizador terem sido modificada a respetiva palavra-passe e que podem ir na sua forma feliz.
10. Se a palavra-passe definida falha de operação, estamos devolver o erro ao utilizador e a informá-los, tente novamente.  A operação pode falhar porque o serviço foi para baixo, uma vez que a palavra-passe estas seleccionadas satisfaz as políticas da organização, porque não podemos não foi possível localizar o utilizador de anúncios local, ou qualquer número das razões.  Vamos tem uma mensagem específica para muitos nestes casos e indicar ao utilizador, o que ele podem fazer para resolver o problema.

### <a name="scenarios-supported-for-password-writeback"></a>Cenários de palavra-passe de escrita não consolidada suportados
A tabela abaixo descreve quais os cenários são suportados para as versões do nossas capacidades de sincronização.  Em geral, recomenda-se vivamente que instalar a versão mais recente da [ligação do Azure AD](active-directory-aadconnect.md#install-azure-ad-connect) se pretender utilizar a palavra-passe de escrita não consolidada.

  ![][002]

### <a name="password-writeback-security-model"></a>Modelo de segurança de repetição de escrita de palavra-passe
Repetição de escrita de palavra-passe é um serviço altamente seguro e robusto.  Para certificar-se de que as suas informações estiver protegidas, podemos ativar um modelo de segurança camadas 4 que é descrito abaixo.

- **Reencaminhamento bus serviço específico do inquilino** – ao configurar o serviço, vamos configurar o reencaminhamento de bus um específicas do inquilino serviço que está protegido por uma gerado aleatoriamente palavra-passe segura que Microsoft nunca tenha acesso ao.
- **Locked para baixo com encriptação forte chave de encriptação de palavra-passe** – depois do reencaminhamento de bus do serviço é criado, criamos uma chave simétrica forte que utilizamos para encriptar a palavra-passe, tal como chegar sobre o fio.  Esta chave encontra-se apenas na loja secreta da sua empresa na nuvem, é fortemente bloqueada e auditada, tal como faria com qualquer palavra-passe no diretório.
- **TLS norma da indústria** – quando uma palavra-passe repor ou alterar a operação ocorre na nuvem, podemos tirar a palavra-passe de texto simples e encriptá-la com a sua chave pública.  Vamos, em seguida, plop que numa mensagem HTTPS que é enviada através de um canal encriptado utilizar certificados SSL da Microsoft para o reencaminhamento de bus de serviço.  Depois de receber essa mensagem para Bus de serviço, o agente no local sai para cima, autentica bus serviço utilizando a palavra-passe segura que tivesse sido gerado anteriormente, escolhe a mensagem encriptada, desencripta-utilizar a chave privada que podemos gerados e, em seguida tentativas para definir a palavra-passe através da API do AD DS SetPassword.  Este passo é o que permite-nos para impor a política de palavra-passe AD no local (complexidade, idade, histórico, filtros, etc.) na nuvem.
- **Políticas de expiração da mensagem** – por fim, se, por alguma razão, a mensagem encontra-se no serviço Bus uma vez que é o serviço no local para baixo, será excedeu e removida depois de alguns minutos para aumentar a segurança ainda mais.

## <a name="how-does-the-password-reset-portal-work"></a>Como a palavra-passe é que reponho a portal trabalho?
Quando um utilizador navega para o portal de repor a palavra-passe, um fluxo de trabalho é pontapés para determinar se essa conta de utilizador é válida, que organização a que pertence utilizadores, sempre que palavra-passe esse utilizador é gerida e, se ou não o utilizador está licenciado para utilizar a funcionalidade.  Leia os passos abaixo para saber mais sobre a lógica atrás a página de reposição de palavra-passe.

1.  Utilizador clica no não consegue aceder à sua ligação de conta ou vai diretamente para [https://passwordreset.microsoftonline.com](https://passwordreset.microsoftonline.com).
2.  O utilizador introduz um id de utilizador e transmite um captcha.
3.  Azure AD verifica se o utilizador é possível utilizar esta funcionalidade ao efetuar o seguinte:
    - Verifica se o utilizador tem esta funcionalidade ativado e uma licença do Azure AD atribuídas.
        - Se o utilizador não tem esta funcionalidade activada ou uma licença atribuída, é pedido ao utilizador para contactar dele administrador para repor a palavra-passe dele.
    - Verificações de que o utilizador tem direita desafio dados definidos no conta em conformidade com a política de administrador.
        - Se a política requer apenas um desafio, é garantir que o utilizador tem os dados adequados definidos para pelo menos um dos desafios ativados pela política de administrador.
          - Se o utilizador não estiver configurado, em seguida, o utilizador é aconselhável notificar administrador para repor a palavra-passe de contacto.
        - Se a política requer dois desafios, é garantir que o utilizador tem os dados adequados definidos para, pelo menos, duas dos desafios ativados pela política de administrador.
          - Se o utilizador não está configurado, em seguida, podemos o utilizador é aconselhável contactar dele administrador para repor a palavra-passe dele.
    - Verifica se ou não a palavra-passe de utilizador é gerido no local (federado ou tinha sincronização hash de palavra-passe).
       - Se for implementada escrita não consolidada e palavra-passe de utilizador é gerido no local, o utilizador é permitido para avançar para autenticar e repor a palavra-passe dele.
       - Se não está implementada escrita não consolidada e palavra-passe de utilizador é gerido no local, em seguida, é pedido ao utilizador para contactar dele administrador para repor a palavra-passe dele.
4.  Se for determinado que o utilizador é possível para repor a palavra-passe com êxito, em seguida, o utilizador é orientado pelo processo de repor.

Saiba mais sobre como implementar a palavra-passe de escrita não consolidada na [Introdução: gestão de palavra-passe do Azure AD](active-directory-passwords-getting-started.md).

### <a name="what-data-is-used-by-password-reset"></a>Quais os dados que são utilizados pelo reposição de palavra-passe?
A seguinte tabela descreve onde e como estes dados são utilizados durante a reposição de palavra-passe e foi concebidos para ajudar a decidir quais as opções de autenticação são adequadas para a sua organização. Esta tabela mostra também quaisquer requisitos de formatação para os casos onde está a fornecer dados em nome de utilizadores a partir de caminhos de entrada que não validar estes dados.

> [AZURE.NOTE] Telefone do escritório não é apresentado no portal do registo, uma vez que os utilizadores não estão atualmente possam editar esta propriedade no diretório.

<table>
          <tbody><tr>
            <td>
              <p>
                <strong>Nome do método de contacto</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Elemento de dados do Azure Active Directory</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Pode ser definida / utilizados onde?</strong>
              </p>
            </td>
            <td>
              <p>
                <strong>Requisitos de formato</strong>
              </p>
            </td>
          </tr>
          <tr>
            <td>
              <p>Telefone do escritório</p>
            </td>
            <td>
              <p>NúmeroTelefone</p>
              <p>Por exemplo, conjunto-MsolUser - UserPrincipalName JWarner@contoso.com - NúmeroTelefone ",1 1234567890 x 1234"</p>
            </td>
            <td>
              <p>Utilizados em:</p>
              <p>Portal de reposição de palavra-passe</p>
              <p>Definível a partir de:</p>
              <p>NúmeroTelefone é pode ser definida do PowerShell, o DirSync, o Portal de gestão do Azure e o Portal de administração do Office</p>
            </td>
            <td>
              <p>+ NIB xxxyyyzzzz (por exemplo, 1234567890,1)</p>
              <ul>
                <li class="unordered">
Tem de fornecer um código de país/região<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
Tem de fornecer um código de área (onde aplicável)<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
Tem de ter fornecer um código de um + à frente do país<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
Tem de ter um espaço entre indicativo do país e o resto do número<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
Extensões não são suportadas, se tiver qualquer extensões especificada, podemos irá eliminá-lo a partir do número antes de distribuir a chamada telefónica.<br><br></li>
              </ul>
            </td>
          </tr>
          <tr>
            <td>
              <p>Número de telemóvel</p>
            </td>
            <td>
              <p>AuthenticationPhone</p>
              <p>OU</p>
              <p>MobilePhone</p>
              <p>(Autenticação de que telefone é utilizado caso haja dados apresentados, caso contrário, isto reverte para o campo de número de telemóvel).</p>
              <p>Por exemplo, conjunto-MsolUser - UserPrincipalName JWarner@contoso.com - MobilePhone ",1 1234567890 x 1234"</p>
            </td>
            <td>
              <p>Utilizados em:</p>
              <p>Portal de reposição de palavra-passe</p>
              <p>Portal de registo</p>
              <p>Definível a partir de: </p>
              <p>AuthenticationPhone é pode ser definida a partir do portal de registo de reposição de palavra-passe ou o portal de registo MFA.</p>
              <p>MobilePhone é pode ser definida do PowerShell, o DirSync, o Portal de gestão do Azure e o Portal de administração do Office</p>
            </td>
            <td>
              <p>+ NIB xxxyyyzzzz (por exemplo, 1234567890,1)</p>
              <ul>
                <li class="unordered">
Tem de fornecer um código de país/região.<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
Tem de fornecer um código de área (onde aplicável).<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
Tem de ter fornecer código um + à frente do país.<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
Tem de ter um espaço entre indicativo do país e o resto do número.<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
Extensões não são suportadas, se tiver qualquer extensões especificada, podemos ignorá-la quando despacho a chamada telefónica.<br><br></li>
              </ul>
            </td>
          </tr>
          <tr>
            <td>
              <p>E-mail alternativo</p>
            </td>
            <td>
              <p>AuthenticationEmail</p>
              <p>OU</p>
              <p>AlternateEmailAddresses [0] </p>
              <p>(Autenticação de que correio eletrónico é utilizado caso haja dados apresentados, caso contrário, isto reverte para o campo de E-mail alternativo).</p>
              <p>Nota: o campo de e-mail alternativo está especificado como uma matriz de cadeias no diretório.  Utilizamos a primeira entrada na seguinte matriz.</p>
              <p>Por exemplo, conjunto-MsolUser - UserPrincipalName JWarner@contoso.com - AlternateEmailAddresses"email@live.com"</p>
            </td>
            <td>
              <p>Utilizados em:</p>
              <p>Portal de reposição de palavra-passe</p>
              <p>Portal de registo</p>
              <p>Definível a partir de: </p>
              <p>AuthenticationEmail é pode ser definida a partir do portal de registo de reposição de palavra-passe ou o portal de registo MFA.</p>
              <p>AlternateEmail é pode ser definida do PowerShell, o Portal de gestão do Azure e Portal de administração do Office</p>
            </td>
            <td>
              <p>
                <a href="mailto:user@domain.com">user@domain.com</a>ou甲斐@黒川.日本</p>
              <ul>
                <li class="unordered">
Mensagens de correio eletrónico devem seguir formatação padrão, como por.<br><br></li>
              </ul>
              <ul>
                <li class="unordered">
Mensagens de correio eletrónico Unicode são suportadas.<br><br></li>
              </ul>
            </td>
          </tr>
          <tr>
            <td>
              <p>Segurança perguntas e respostas</p>
            </td>
            <td>
              <p>Não está disponível para modificar diretamente no diretório.</p>
            </td>
            <td>
              <p>Utilizados em:</p>
              <p>Portal de reposição de palavra-passe</p>
              <p>Portal de registo </p>
              <p>Definível a partir de: </p>
              <p>A única forma para definir perguntas de segurança é através do Portal de gestão do Azure.</p>
              <p>A única forma para definir as respostas a perguntas de segurança para um determinado utilizador é através do Portal do registo.</p>
            </td>
            <td>
              <p>Perguntas de segurança tem um máximo de 200 carateres e um mínimo de 3 caracteres</p>
              <p>Respostas tem um máximo de 40 carateres e um mínimo de 3 caracteres</p>
            </td>
          </tr>
        </tbody></table>

###<a name="how-to-access-password-reset-data-for-your-users"></a>Como aceder a palavra-passe repor os dados para os seus utilizadores
####<a name="data-settable-via-synchronization"></a>Pode ser definidos através da sincronização de dados
Os campos seguintes podem ser sincronizados a partir no local:

* Número de telemóvel
* Telefone do escritório

####<a name="data-settable-with-azure-ad-powershell"></a>Dados pode ser definidos com PowerShell do Azure AD
Os campos seguintes são acessíveis com PowerShell do Azure AD & a API do gráfico:

* E-mail alternativo
* Número de telemóvel
* Telefone do escritório
* Telefone de autenticação
* E-Mail de autenticação

####<a name="data-settable-with-registration-ui-only"></a>Pode ser definidos com o registo IU apenas de dados
Os seguintes campos só são acessíveis através do registo de personalizada SSPR IU (https://aka.ms/ssprsetup):

* Segurança perguntas e respostas

####<a name="what-happens-when-a-user-registers"></a>O que acontece quando um utilizador regista?
Quando um utilizador regista, a página de registo será **sempre** definir os seguintes campos:

* Telefone de autenticação
* E-Mail de autenticação
* Segurança perguntas e respostas

Se tiver fornecido um valor para o **número de telemóvel** ou **E-mail alternativo**, os utilizadores podem utilizar aqueles imediatamente para repor a palavra-passe, mesmo ainda não registraram no serviço.  Além disso, os utilizadores irão ver esses valores quando registar pela primeira vez e modificá-las se pretendem que.  No entanto, após com êxito registam, estes valores serão mantidas nos campos de **Telefone de autenticação** e **E-Mail de autenticação** , respetivamente.

Isto pode ser uma forma útil para desbloquear grandes quantidades de utilizadores utilizem o personalizada SSPR enquanto ainda permitir que os utilizadores validar estas informações durante o processo de registo.

####<a name="setting-password-reset-data-with-powershell"></a>Definir palavra-passe repor os dados com o PowerShell
Pode definir valores para os seguintes campos com o Azure AD PowerShell.

* E-mail alternativo
* Número de telemóvel
* Telefone do escritório

Para começar, primeiro terá de [Transferir](https://msdn.microsoft.com/library/azure/jj151815.aspx#bkmk_installmodule)e instalar o módulo Azure AD PowerShell.  Assim que tiver instalada, pode seguir os passos abaixo para configurar a cada campo.

#####<a name="alternate-email"></a>E-mail alternativo
```
Connect-MsolService
Set-MsolUser -UserPrincipalName user@domain.com -AlternateEmailAddresses @("email@domain.com")
```

#####<a name="mobile-phone"></a>Número de telemóvel
```
Connect-MsolService
Set-MsolUser -UserPrincipalName user@domain.com -MobilePhone "+1 1234567890"
```

#####<a name="office-phone"></a>Telefone do escritório
```
Connect-MsolService
Set-MsolUser -UserPrincipalName user@domain.com -PhoneNumber "+1 1234567890"
```

####<a name="reading-password-reset-data-with-powershell"></a>Dados com o PowerShell repor a palavra-passe de leitura
Pode obter valores para os seguintes campos com o Azure AD PowerShell.

* E-mail alternativo
* Número de telemóvel
* Telefone do escritório
* Telefone de autenticação
* E-Mail de autenticação

Para começar, primeiro terá de [Transferir](https://msdn.microsoft.com/library/azure/jj151815.aspx#bkmk_installmodule)e instalar o módulo Azure AD PowerShell.  Assim que tiver instalada, pode seguir os passos abaixo para configurar a cada campo.

#####<a name="alternate-email"></a>E-mail alternativo
```
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select AlternateEmailAddresses
```

#####<a name="mobile-phone"></a>Número de telemóvel
```
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select MobilePhone
```

#####<a name="office-phone"></a>Telefone do escritório
```
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select PhoneNumber
```

#####<a name="authentication-phone"></a>Telefone de autenticação
```
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select PhoneNumber
```

#####<a name="authentication-email"></a>E-Mail de autenticação
```
Connect-MsolService
Get-MsolUser -UserPrincipalName user@domain.com | select -Expand StrongAuthenticationUserDetails | select Email
```

## <a name="links-to-password-reset-documentation"></a>Ligações para a palavra-passe repor documentação
Abaixo estão ligações para todas as páginas de documentação de reposição de palavra-passe do Azure AD:

* **Está aqui porque está a ter problemas ao iniciar sessão?** Se Sim, [Eis como pode alterar e repor a sua própria palavra-passe](active-directory-passwords-update-your-own-password.md).
* [**Como funciona**](active-directory-passwords-how-it-works.md) - Saiba mais sobre os seis diferentes componentes do seu serviço e o que faz cada
* [**Introdução ao**](active-directory-passwords-getting-started.md) - Saiba como permitir os utilizadores para repor e alterar a palavra-passe na nuvem ou no local
* [**Personalizar**](active-directory-passwords-customize.md) - Saiba como personalizar o aspeto e funcionamento e comportamento do serviço para necessidades da sua organização
* [**Práticas recomendadas**](active-directory-passwords-best-practices.md) - Saiba como implementar rapidamente e eficazmente gerir palavras-passe na sua organização
* [**Obter informações**](active-directory-passwords-get-insights.md) - Saiba mais sobre os nossos integradas capacidades de relatórios
* [**Perguntas mais frequentes**](active-directory-passwords-faq.md) : Obtenha respostas às perguntas mais frequentes
* [**Resolução de problemas**](active-directory-passwords-troubleshoot.md) - aprender rapidamente resolver problemas relacionados com o serviço



[001]: ./media/active-directory-passwords-learn-more/001.jpg "Image_001.jpg"
[002]: ./media/active-directory-passwords-learn-more/002.jpg "Image_002.jpg"
