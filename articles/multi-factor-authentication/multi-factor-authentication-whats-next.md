<properties
    pageTitle="Autenticação Multifator Azure - o que se segue"
    description="Esta é a página de autenticação multifator Azure que descreve o que fazer a seguir com MFA.  Isto inclui relatórios, alerta de fraude, ignorar única, mensagens de voz personalizado, colocação em cache, fidedignas ips e app palavras-passe."
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="curtand"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/23/2016"
    ms.author="kgremban"/>

# <a name="configuring-azure-multi-factor-authentication"></a>Configurar a autenticação Multifator Azure

Este artigo ajuda-o a gerir a autenticação Multifator de Azure agora que estão a trabalhar.  Cobrir uma variedade de tópicos que ajudá-lo a tirar o máximo partido do Azure a autenticação Multifator.  Nem todas estas funcionalidades estão disponíveis em todas as versões do Azure a autenticação Multifator.

A configuração para algumas das funcionalidades abaixo encontra-se no Portal de gestão de autenticação do Azure Multifator. Existem duas maneiras diferentes que podem aceder ao portal de gestão de MFA, que são ambos feitas através do portal do Azure. O primeiro é ao gerir um fornecedor de autenticação Multifator se utilizar com base em consumo MFA. O segundo é através das definições do serviço MFA. A segunda opção requer um fornecedor de autenticação Multifator ou uma licença Azure MFA, Azure AD Premium ou do conjunto de aplicações do Enterprise mobilidade.

Para aceder ao Portal de gestão de MFA através de um fornecedor de autenticação Multifator Azure, inicie sessão no portal do Azure como administrador e selecione a opção do Active Directory. Clique no separador de **Fornecedores de autenticação Multifator** , em seguida, selecione o seu diretório e clique no botão **Gerir** na parte inferior.

Para aceder ao Portal de gestão de MFA através da página de definições do serviço MFA, inicie sessão no portal do Azure como administrador e selecione a opção do Active Directory. Clique no seu diretório e, em seguida, clique no separador de **Configurar** . Na secção autenticação multifator, selecione **Gerir definições de serviço**. Na parte inferior da página Definições do serviço MFA, clique na ligação **Ir para o portal** .


Funcionalidade| Descrição| O que é abrangido
:------------- | :------------- | :------------- |
[Alerta de fraude](#fraud-alert)|Alerta de fraude pode ser configurada e configurar o para que os utilizadores podem comunicar fraudulentas tentativas para aceder aos seus recursos.|Como configurar, configurar e fraude um relatório
[Ignorar único](#one-time-bypass) |Ignorar uma única permite que um utilizador autenticar uma única vez ignorando"" autenticação multifator.|Como instalar e configurar uma única ignorar
[Mensagens de voz personalizado](#custom-voice-messages) |Mensagens de voz personalizadas permitem-lhe utilizar o seu próprio gravações ou saudações com autenticação multifator. |Como instalar e configurar saudações personalizadas e mensagens
[Colocação em cache](#caching-in-azure-multi-factor-authentication)|Colocação em cache permite-lhe definir uma hora específica período, de modo a que tentativas subsequentes de autenticação teve êxito automaticamente. |Como instalar e configurar a autenticação colocação em cache.
[IPs de confiança](#trusted-ips)|Fidedignos que IPs é uma funcionalidade de autenticação multifator que permite aos administradores de um inquilino gerido ou federado a capacidade de ignorar a autenticação multifator para utilizadores que está a iniciar sessão a partir de intranet local da empresa.|Configurar e configurar o endereços IP que estão isentos para autenticação multifator
[Palavras-passe de aplicação](#app-passwords)|Uma palavra-passe de aplicação permite que uma aplicação que não está MFA ciente para ignorar a autenticação multifator e continuar a trabalhar.|Informações sobre palavras-passe de aplicação.
[Não se esqueça de autenticação Multifator para passada dispositivos e browsers](#remember-multi-factor-authentication-for-devices-users-trust)|Permite-lhe lembrar-se dispositivos para um determinado número de dias após um utilizador com êxito tem iniciar sessão com MFA.|Informações sobre como ativar esta funcionalidade e configurar o número de dias.
[Métodos de verificação selecionável](#selectable-verification-methods)|Permite-lhe escolher os métodos de autenticação que estão disponíveis para os utilizadores utilizem.|Informações sobre como ativar ou desativar métodos de autenticação específicos, tais como mensagens de texto ou de chamada.



## <a name="fraud-alert"></a>Alerta de fraude
Alerta de fraude pode ser configurada e configurar o para que os utilizadores podem comunicar fraudulentas tentativas para aceder aos seus recursos.  Os utilizadores podem relatar fraude com a aplicação móvel ou através do seu telefone.

### <a name="to-set-up-and-configure-fraud-alert"></a>Para definir e configurar o alerta de fraude

1.  Inicie sessão no http://azure.microsoft.com
2.  Navegue até ao Portal de gestão de MFA, de acordo com as instruções na parte superior desta página.
3.  No Portal de gestão do Azure Multifator autenticação, clique em definições, na secção Configurar.
4.  Na secção da página de definições de alerta de fraude, verifique os permitir que os utilizadores submeter alertas de fraude caixa de verificação.
5.  Se pretender que os utilizadores a ser bloqueadas quando é comunicado fraude, coloque uma verificação de utilizador de bloco quando fraude é comunicado.
6.  Na caixa de texto **Código para relatório fraude durante inicial de saudação** , introduza um código de número que pode ser utilizado durante a verificação de chamada. Se um utilizador introduz este código plus # em vez de apenas o sinal de #, em seguida, um alerta de fraude vai ser comunicado.
7.  Na parte inferior, clique em Guardar.

>[AZURE.NOTE]
>Saudações de voz da Microsoft predefinido indicam aos utilizadores a premir # 0 para submeter um alerta de fraude. Se quiser utilizar um código diferente de 0, deve gravar e carregue o seus próprio saudações de voice personalizado com instruções adequadas.


![Nuvem](./media/multi-factor-authentication-whats-next/fraud.png)

### <a name="to-report-fraud-alert"></a>Para o alerta de fraude de relatório
Alerta de fraude pode ser comunicada duas formas.  Quer através da aplicação móvel ou através do telefone.  

### <a name="to-report-fraud-alert-with-the-mobile-app"></a>Para o alerta de fraude de relatório com a aplicação móvel



1. Quando uma verificação é enviada para o seu telefone, selecione-o para iniciar a aplicação Microsoft Authenticator.
2. Para denunciar fraude, clique em Cancelar e fraude de relatório. Isto reúne uma caixa que diz que será notificada de equipa de suporte de TI da sua organização.
3. Clique em fraude de relatório.
4. A aplicação, clique em Fechar.

![Nuvem](./media/multi-factor-authentication-whats-next/report1.png)


![Nuvem](./media/multi-factor-authentication-whats-next/fraud2.png)

### <a name="to-report-fraud-alert-with-the-phone"></a>Para o alerta de fraude de relatório com o telefone

1. Quando uma verificação chamada com o seu telemóvel, atendê-lo.  
2. Para denunciar fraude, introduza o código que tenha sido configurado para corresponder com relatórios fraude através do telefone e, em seguida, o sinal #. Será notificado que foi apresentado um alerta de fraude.
3. Termine a chamada.

### <a name="to-view-the-fraud-report"></a>Para ver o relatório de fraude

1. Inicie sessão no [http://azure.microsoft.com](https://azure.microsoft.com/)
2. No lado esquerdo, selecione do Active Directory.
3. Na parte superior, selecione fornecedores de autenticação Multifator. Isto reúne uma lista dos fornecedores de autenticação Multifator.
4. Se tiver mais do que um fornecedor de autenticação Multifator, selecione a que pretender ver o relatório de alerta de fraude e clique em Gerir na parte inferior da página. Se tiver apenas uma, clique em gerir. Esta ação abre o Portal de gestão de autenticação do Azure Multifator.
5. No Portal de gestão de autenticação do Multifator de Azure, à esquerda, em vista de um relatório, clique em fraude alerta.
6. Especifique o intervalo de datas que pretende visualizar no relatório. Também pode especificar quaisquer liste específico, números de telefone e estado de um utilizador.
7. Clique em executar. Isto reúne um relatório semelhante ao abaixo. Também pode clicar em exportar para CSV se pretender exportar o relatório.

## <a name="one-time-bypass"></a>Ignorar único

Ignorar uma única permite que um utilizador autenticar uma única vez ignorando"" autenticação multifator. O fluxo direto é temporário e expira após o número de segundos especificado.  Por isso, em situações onde a aplicação móvel ou o telefone não está a receber uma notificação ou chamada telefónica, pode ativar a ignorar um único para o utilizador possa aceder ao recurso pretendido.

### <a name="to-create-a-one-time-bypass"></a>Para criar uma única ignorar

1.  Inicie sessão no http://azure.microsoft.com
2.  Navegue até ao Portal de gestão de MFA, de acordo com as instruções na parte superior desta página.
3.  No Azure Multifator autenticação Portal de gestão, se vir o nome do seu inquilino ou o fornecedor do Azure MFA à esquerda com um + junto ao mesmo, clique na + ver diferentes grupos de replicação MFA Server e o grupo predefinido do Azure. Clique no grupo apropriado.
4.  Em administração de utilizador, clique em **Ignorar One-Time**.
![Nuvem](./media/multi-factor-authentication-whats-next/create1.png)
5.  Na página One-Time ignorar, clique em **Novo One-Time ignorar**.
6.  Introduza o nome de utilizador do utilizador, o número de segundos que o fluxo direto existirão, a razão para o fluxo direto e clique em **Ignorar**.
![Nuvem](./media/multi-factor-authentication-whats-next/create2.png)
7.  Neste momento, o utilizador tem de iniciar sessão antes do fluxo direto único expira.



### <a name="to-view-the-one-time-bypass-report"></a>Para ver o relatório de fluxo direto únicos

1. Inicie sessão no [http://azure.microsoft.com](https://azure.microsoft.com/)
2. No lado esquerdo, selecione do Active Directory.
3. Na parte superior, selecione fornecedores de autenticação Multifator. Isto reúne uma lista dos fornecedores de autenticação Multifator.
4. Se tiver mais do que um fornecedor de autenticação Multifator, selecione a que pretender ver o relatório de alerta de fraude e clique em Gerir na parte inferior da página. Se tiver apenas uma, clique em gerir. Esta ação abre o Portal de gestão de autenticação do Azure Multifator.
5. No Portal de gestão de autenticação do Multifator de Azure, à esquerda, em vista de um relatório, clique em Ignorar One-Time.
6. Especifique o intervalo de datas que pretende visualizar no relatório. Também pode especificar quaisquer liste específico, números de telefone e estado de um utilizador.
7. Clique em executar. Isto reúne um relatório semelhante ao abaixo. Também pode clicar em exportar para CSV se pretender exportar o relatório.

<center>![Nuvem](./media/multi-factor-authentication-whats-next/report.png)</center>

## <a name="custom-voice-messages"></a>Mensagens de voz personalizado

Mensagens de voz personalizadas permitem-lhe utilizar o seu próprio gravações ou saudações com autenticação multifator.  Estes podem ser utilizados para além registos a ou substituir a Microsoft.

Antes de começar, tenha em atenção o seguinte procedimento:

- Os formatos de ficheiro suportados atual são. wav e. mp3.
- O limite de tamanho de ficheiro é 5 MB.
- É recomendado que para mensagens de autenticação-não ter mais de 20 segundos. Nada maior do que esta poderá causar a verificação falhar porque o utilizador poderá não responder antes da mensagem é concluída e a verificação tempo limite.



### <a name="to-set-up-custom-voice-messages-in-azure-multi-factor-authentication"></a>Para configurar a mensagens de voz personalizado no Azure a autenticação Multifator
1.  Crie uma mensagem de voz personalizado utilizando um dos formatos de ficheiro suportados.
2.  Inicie sessão no http://azure.microsoft.com
3.  Navegue até ao Portal de gestão de MFA, de acordo com as instruções na parte superior desta página.
4.  No Portal de gestão do Azure Multifator autenticação, clique em mensagens de voz, na secção Configurar.
5.  Na secção de mensagens de voz, clique em **Nova mensagem de voz**.
![Nuvem](./media/multi-factor-authentication-whats-next/custom1.png)
6.  Para configurar: mensagens de voz nova página, clique em **Gerir ficheiros de som**.
![Nuvem](./media/multi-factor-authentication-whats-next/custom2.png)
7.  Para configurar: página ficheiros de som, clique em **Carregar ficheiro de som**.
![Nuvem](./media/multi-factor-authentication-whats-next/custom3.png)
8.  Para configurar: carregue o ficheiro de som, clique em **Procurar** e navegue para a sua mensagem de voz, clique em **Abrir**.
![Nuvem](./media/multi-factor-authentication-whats-next/custom4.png)
9.  Adicionar uma descrição e clique em carregar.
10. Assim que for concluída, uma mensagem confirma que carregou com êxito o ficheiro.
11. No lado esquerdo, clique em mensagens de voz.
12. Na secção de mensagens de voz, clique em nova mensagem de voz.
13. A partir do menu pendente idioma, selecione um idioma.
14. Se esta mensagem é para uma aplicação específica, especifique-o na caixa de aplicação.
15. O tipo de mensagem, selecione o tipo de mensagem a ser substituído com os nossos nova mensagem personalizada.
16. A partir do menu pendente ficheiro de som, selecione o ficheiro de som.
17. Clique em **Criar**. Uma mensagem confirma que criou com êxito uma mensagem de voz.
![Nuvem](./media/multi-factor-authentication-whats-next/custom5.png)</center>



## <a name="caching-in-azure-multi-factor-authentication"></a>Colocação em cache no Azure autenticação Multifator

Colocação em cache permite-lhe definir uma hora específica período, de modo a que tentativas subsequentes de autenticação teve êxito automaticamente. É principalmente utilizado quando sistemas no local, tal como VPN enviarem pedidos de verificação vários enquanto o primeiro pedido ainda estiver em curso. Isto permite que os pedidos subsequentes para ser concluída com êxito automaticamente após o utilizador ser bem sucedida a verificação em curso. Tenha em atenção que a colocação em cache não se destina a ser utilizado para suplementos de início de sessão para Azure AD.


### <a name="to-set-up-caching-in-azure-multi-factor-authentication"></a>Para configurar a colocação em cache no Azure a autenticação Multifator

1.  Inicie sessão no http://azure.microsoft.com
2.  Navegue até ao Portal de gestão de MFA, de acordo com as instruções na parte superior desta página.
3.  No Portal de gestão do Azure Multifator autenticação, clique em cache, na secção Configurar.
4.  Na página de colocação em cache para configurar, clique em nova Cache
5.  Selecione o tipo de Cache e os segundos de cache. Clique em criar.

<center>![Nuvem](./media/multi-factor-authentication-whats-next/cache.png)</center>

## <a name="trusted-ips"></a>IPs de confiança

Fidedignos que IPs é uma funcionalidade de autenticação multifator que permite aos administradores de um inquilino gerido ou federado a capacidade de ignorar a autenticação multifator para utilizadores que está a iniciar sessão a partir de intranet local da empresa. As funcionalidades estão disponíveis para inquilinos do Azure AD que tenham licenças Azure AD Premium, o conjunto de aplicações do Enterprise mobilidade ou Azure a autenticação Multifator.


Tipo de inquilino do Azure AD| Opções de IP de confiança disponíveis
:------------- | :------------- |
Geridos|Intervalos de endereços IP específicos – os administradores podem especificar um intervalo de endereços IP que pode ignorar a autenticação multifator para utilizadores que está a iniciar sessão a partir da intranet da empresa.
Federado|<li>Todos os utilizadores federados - todos os utilizadores federados que estão a iniciar sessão a partir de dentro da organização aguardam autenticação multifator utilizando uma afirmação emitida pelo AD FS.</li><li>Intervalos de endereços IP específicos – os administradores podem especificar um intervalo de endereços IP que pode ignorar a autenticação multifator para utilizadores que está a iniciar sessão a partir da intranet da empresa.

O fluxo direto só funciona a partir de dentro da intranet da empresa. Por exemplo, se que todos os utilizadores federados apenas seleccionada e um utilizador inicia sessão a partir de fora da intranet da empresa, que o utilizador tem que autenticar utilizando a autenticação multifator mesmo se o utilizador apresenta uma afirmação AD FS. A tabela seguinte descreve quando multifator autenticação e de aplicação as palavras-passe são necessárias dentro da sua corpnet e fora da sua corpnet quando IPs de confiança está ativado.


|IPs com capacidade de confiança| IPs desativado de confiança
:------------- | :------------- | :------------- |
Corpnet interno|Para fluxos de browser, autenticação multifator não é necessário.|Para os fluxos de browser, exige autenticação multifator
|Para as aplicações de cliente avançada, palavras-passe normal funcionam se o utilizador não criou quaisquer palavras-passe de aplicação. Depois de ter sido criada uma palavra-passe de aplicação, palavras-passe de aplicação são necessárias.|Para as aplicações de cliente avançada, obrigatório palavras-passe de aplicação
Corpnet exterior|Para fluxos de browser, autenticação multifator necessário.|Para fluxos de browser, autenticação multifator necessário.
|Para as aplicações de cliente avançada, palavras-passe de aplicação necessário.|Para as aplicações de cliente avançada, palavras-passe de aplicação necessário.

### <a name="to-enable-trusted-ips"></a>Para ativar IPs de confiança

1. Início de sessão no portal do Azure clássica.
2. No lado esquerdo, clique em do Active Directory.
3. Em, clique em diretório no diretório que pretende configurar IPsing fidedignos no.
4. No directório de que selecionou, clique em configurar.
5. Na secção autenticação multifator, clique em Gerir definições de serviço.
6. Na página Definições de serviço, em IPs de confiança, selecione:

    - Para pedidos de federado utilizadores com origem a partir do meu intranet – federado todos os utilizadores que estão a iniciar sessão a partir à rede empresarial aguardam autenticação multifator utilizando uma afirmação emitida pelo AD FS.
    - Para pedidos a partir de um intervalo de IPs público – específico de introduza os endereços IP nas caixas fornecidas utilizando a notação CIDR. Por exemplo: xxx.xxx.xxx.0/24 para os endereços IP no xxx.xxx.xxx.1 intervalo – xxx.xxx.xxx.254 ou xxx.xxx.xxx.xxx/32 para um único endereço IP. Pode introduzir até 50 intervalos de endereços IP.

7. Clique em Guardar.
8. Depois de terem sido aplicadas as atualizações, clique em Fechar.



![IPs de confiança](./media/multi-factor-authentication-whats-next/trustedips3.png)




## <a name="app-passwords"></a>Palavras-passe de aplicação

Em algumas aplicações, como o Office 2010 ou mais antigo e o Apple Mail não pode utilizar a autenticação multifator.  Para utilizar estas aplicações, terá de utilizar "palavras-passe de aplicação" em vez da sua palavra-passe tradicional.  A palavra-passe de aplicação permite que a aplicação ignorar a autenticação multifator e continuar a trabalhar.

>[AZURE.NOTE] Autenticação moderna para os clientes do Office 2013
>
> Clientes do Office 2013 (incluindo o Outlook) agora suportam novo protocolos de autenticação e podem ser ativados para suportar uma autenticação Multifator.  Isto significa que uma vez activada, palavras-passe de aplicação não são necessárias para utilizar com clientes do Office 2013.  Para mais informações, consulte o artigo [Office autenticação moderna público pré-visualização 2013 anunciada](https://blogs.office.com/2015/03/23/office-2013-modern-authentication-public-preview-announced/).



### <a name="important-things-to-know-about-app-passwords"></a>Factos importantes a saber sobre palavras-passe de aplicação

Segue-se uma lista das coisas que deve saber sobre palavras-passe de aplicação importante.

- Os utilizadores podem ter várias aplicação palavras-passe, que aumenta a área da superfície para roubo. Uma vez que as palavras-passe de aplicação são difícil memorizar, poderão encorajar a que as pessoas Anote esta. Esta não é recomendada e não deve fazer uma vez que apenas um fator é necessária para iniciar sessão com palavra-passe de aplicação.
- As aplicações que as palavras-passe em cache e utilizá-lo em cenários no local, podem começar a falhar uma vez que a palavra-passe de aplicação não é conhecida fora o id da organização. Um exemplo é e-mails do Exchange que estão no local, mas o correio arquivado é na nuvem. A mesma palavra-passe não funciona.
- A palavra-passe real é gerada automaticamente e não for fornecida pelo utilizador. Esta é uma vez que a palavra-passe gerada automaticamente, é mais difícil intruso adivinhar e é mais segura.
- Neste momento não existe um limite de 40 palavras-passe por utilizador. Vai ser-lhe para eliminar uma das suas palavras-passe de aplicação existente para criar um novo.
- Assim que a autenticação multifator está ativada numa conta de utilizador, palavras-passe de aplicação podem ser utilizadas com maior parte dos clientes não baseadas no browser como o Outlook e o Lync, mas não podem ser executadas ações administrativas utilizar palavras-passe de aplicação através de aplicações não baseadas no browser, como o Windows PowerShell, mesmo que o utilizador tem uma conta administrativa.  Certifique-se de que criar uma conta de serviço com uma palavra-passe segura para executar scripts de PowerShell e não ativar dessa conta para autenticação multifator.

>[AZURE.WARNING]  As palavras-passe de aplicação não funcionam em ambientes híbridos, onde os clientes de comunicam com ambas no local e na nuvem pontos finais de deteção automática. Isto acontece porque palavras-passe domínio são necessários para autenticar no local e palavras-passe de aplicação são necessários para autenticar com a nuvem.


### <a name="naming-guidance-for-app-passwords"></a>Atribuir nomes a seta de quatro pontas palavras-passe de aplicação
Recomenda-se que os nomes de palavra-passe de aplicação deverão refletir o dispositivo no qual são utilizadas. Por exemplo, se tiver um computador portátil com aplicações não baseadas no browser, como o Outlook, Word e Excel, só tem de criar uma aplicação palavra-passe com o nome portátil e utilizar essa palavra-passe de aplicação em todos os estas aplicações. Apesar de poder criar palavras-passe separadas para todas estas aplicações, não é recomendado. O recomendar forma consiste em utilizar uma aplicação palavra-passe por dispositivo.


<center>![Nuvem](./media/multi-factor-authentication-whats-next/naming.png)</center>


### <a name="federated-sso-app-passwords"></a>Palavras-passe de aplicação de federada (SSO)
Azure AD suporta a Federação com no local Windows Server Active Directory Domain Services (AD DS). Se a sua organização está federated(SSO) com Azure AD e que vai estar a utilizar a autenticação Multifator Azure, em seguida, apresentamos informações importantes que deve ter em atenção quando utiliza palavras-passe de aplicação. Aplica-se apenas a clientes do federated(SSO).

- A palavra-passe de aplicação é verificada por Azure AD e, consequentemente, ignora Federação. Federação ativamente só é utilizada quando configurar a palavra-passe de aplicação.
- Para utilizadores federated(SSO), podemos nunca vá para o fornecedor de identidade (IdP) ao contrário do fluxo passivo. As palavras-passe são armazenadas no id de organização. Se o utilizador sai da empresa, essas informações tem de fluxo para id organizacional utilizando DirSync em tempo real. Conta desativar/eliminação poderá demorar até três horas para sincronizar, atrasar desativar/eliminação da palavra-passe de aplicação no Azure AD.
- Definições de controlo de acesso de cliente no local não são respeitadas por palavra-passe de aplicação
- No local a autenticação registo / capacidade de auditoria está disponível para a palavra-passe de aplicação
- Mais educação do utilizador final é necessária para o cliente do Microsoft Lync 2013. Para obter os passos necessários, consulte o artigo como alterar a palavra-passe no seu correio eletrónico para a palavra-passe de aplicação.
- Determinadas estruturas de arquitecturais avançadas poderão necessitar de utilizar uma combinação de organizacional nome de utilizador e palavras-passe e palavras-passe de aplicação ao utilizar a autenticação multifator com os clientes, dependendo de onde autenticar. Para clientes que autenticar uma infraestrutura no local, utilizaria uma organizacional nome de utilizador e palavra-passe. Para clientes que autenticar Azure AD, teria de utilizar a palavra-passe de aplicação.

Por exemplo, suponha que tem uma arquitectura que consiste em dos seguintes procedimentos:

- São federar sua instância no local do Active Directory com o Azure AD
- Está a utilizar o Exchange online
- Está a utilizar o Lync que esteja especificamente no local
- Estiver a utilizar o Azure a autenticação Multifator


![Proofup](./media/multi-factor-authentication-whats-next/federated.png)

 Nestes casos, terá de efetuar o seguinte procedimento:

- Quando iniciar sessão no Lync, utilize o nome de utilizador e palavra-passe dos seus organizações.
- Quando tentar aceder ao livro de endereços através de um cliente do Outlook que se liga para o Exchange online, utilize uma palavra-passe de aplicação.

### <a name="allowing-app-password-creation"></a>Permitir a criação de palavra-passe de aplicação
Por predefinição, os utilizadores não é possível criar palavras-passe de aplicação.  Esta funcionalidade tem de estar ativada.  Para permitir que os utilizadores a capacidade de criar palavras-passe de aplicação, utilize o procedimento seguinte.

#### <a name="to-enable-users-to-create-app-passwords"></a>Para permitir aos utilizadores criar palavras-passe de aplicação



1. Inicie sessão no portal do Azure clássico.
2. No lado esquerdo, clique em do Active Directory.
3. Em, clique em diretório no diretório para o utilizador que pretende ativar.
4. Na parte superior, clique em utilizadores.
5. Na parte inferior da página, clique em Gerir autenticação Multifator  
6. Na parte superior da página autenticação multifator, clique em definições de serviço.
7. Certifique-se de que o botão de opção ao lado de permitir que os utilizadores para criar palavras-passe de aplicação para iniciar sessão no aplicações baseadas no browser que não está seleccionado.


![Criar palavras-passe de aplicação](./media/multi-factor-authentication-whats-next/trustedips3.png)

### <a name="creating-app-passwords"></a>Criar palavras-passe de aplicação
Os utilizadores podem criar palavras-passe de aplicação durante o respetivo registo inicial.  Estes são dadas uma opção no final do processo de registo que lhe permite-lhe que crie-los.

Para além dos utilizadores também podem criar palavras-passe de aplicação posteriormente ao alterar as definições no portal do Azure, o portal do Office 365 ou ao

### <a name="to-create-app-passwords-in-the-office-365-portal"></a>Para criar palavras-passe de aplicação no portal do Office 365
--------------------------------------------------------------------------------


1. Inicie sessão no portal do Office 365
2. No canto superior direito, selecione o widget definições
3. No lado esquerdo, selecione verificação segurança adicional
4. À direita, selecione **atualizar os meus números de telefone utilizados para segurança da conta**
5. Na página proofup, na parte superior, selecione as palavras-passe de aplicação
6. Clique em **Criar**
7. Introduza um nome para a palavra-passe de aplicação e clique em **seguinte**
8. Copiar a palavra-passe de aplicação para a área de transferência e colá-la a sua aplicação.

<center>![Nuvem](./media/multi-factor-authentication-whats-next/security.png)</center>


### <a name="to-create-app-passwords-in-the-azure-portal"></a>Para criar palavras-passe de aplicação no portal do Azure
--------------------------------------------------------------------------------
1. Inicie sessão no portal do Azure clássico.
3. Na parte superior, com o botão direito no seu nome de utilizador e selecione a verificação de segurança adicional.
5. Na página proofup, na parte superior, selecione as palavras-passe de aplicação
6. Clique em **Criar**
7. Introduza um nome para a palavra-passe de aplicação e clique em **seguinte**
8. Copiar a palavra-passe de aplicação para a área de transferência e colá-la a sua aplicação.


![Palavras-passe de aplicação](./media/multi-factor-authentication-whats-next/app2.png)

### <a name="to-create-app-passwords-if-you-do-not-have-an-office-365-or-azure-subscription"></a>Para criar palavras-passe de aplicação, se não tiver uma subscrição do Office 365 ou do Azure
--------------------------------------------------------------------------------
1. Inicie sessão no [https://myapps.microsoft.com](https://myapps.microsoft.com)
2. Na parte superior, selecione perfil.
3. Clique no seu nome de utilizador e selecione verificação segurança adicional.
5. Na página proofup, na parte superior, selecione as palavras-passe de aplicação
6. Clique em **Criar**
7. Introduza um nome para a palavra-passe de aplicação e clique em **seguinte**
8. Copiar a palavra-passe de aplicação para a área de transferência e colá-la a sua aplicação.

![Palavras-passe de aplicação](./media/multi-factor-authentication-whats-next/myapp.png)

## <a name="remember-multi-factor-authentication-for-devices-users-trust"></a>Não se esqueça de autenticação Multifator para dispositivos utilizadores fidedignidade

Renovados autenticação Multifator para dispositivos e browsers que os utilizadores fidedignidade é uma funcionalidade gratuita para todos os utilizadores MFA.  Permite-lhe dar aos utilizadores a opção para MFA by-pass para um determinado número de dias após executar uma bem sucedida iniciar sessão utilizando MFA. Isto pode melhorar a facilidade de utilização para os seus utilizadores.

No entanto, uma vez que os utilizadores estão autorizados a lembrar-se MFA para dispositivos fidedignos, esta funcionalidade pode reduzir a segurança da conta. Para se certificar de segurança da conta, deverá restaurar a autenticação Multifator para os seus dispositivos para um dos seguintes cenários:

- Se a respetiva conta empresarial ficar comprometida
- Se um dispositivo passado for perdido ou roubado

> [AZURE.NOTE] Esta funcionalidade é implementada como uma cache de cookies do browser. -Não funciona se os cookies do browser não estão ativados.

### <a name="how-to-enabledisable-remember-multi-factor-authentication"></a>Como ativar/desativar lembrar-se a autenticação multifator

1. Inicie sessão no portal do Azure clássico.
2. No lado esquerdo, clique em do Active Directory.
3. Active Directory, clique no diretório que pretende configurar o não se esqueça de autenticação Multifator para dispositivos.
4. No directório de que selecionou, clique em configurar.
5. Na secção autenticação multifator, clique em Gerir definições de serviço.
6. Na página Definições de serviço, em Gerir definições de dispositivo de utilizador, selecione/anular a seleção de **Permitir que os utilizadores não se esqueça de autenticação multifator em dispositivos que ou fidedignos**.
![Não se esqueça de dispositivos](./media/multi-factor-authentication-whats-next/remember.png)
8. Defina o número de dias que pretende permitir a suspensão. A predefinição é 14 dias.
9. Clique em Guardar.
10. Clique em Fechar.


## <a name="selectable-verification-methods"></a>Métodos de verificação selecionável
Nas versões nuvem tanto no local, pode escolher quais os métodos de verificação estão disponíveis para os seus utilizadores. A tabela abaixo fornece uma breve descrição geral de cada método.

Quando os seus utilizadores inscrever-se as respetivas contas para MFA, eles escolher o método de confirmação preferido terminar as opções que activou. As orientações para o processo de inscrição está incluída num [conjunto de configurar a minha conta para verificação de dois passos](multi-factor-authentication-end-user-first-time.md)

Método|Descrição
:------------- | :------------- |
Ligar para o telemóvel |  Coloca uma chamada de voz automático para o telefone de autenticação. O utilizador atende a chamada e premir # no teclado de telefone para autenticar. Este número de telefone não está a ser sincronizado com o Active Directory no local.
Mensagem de texto para telefone | Envia uma mensagem de texto que contém um código de verificação ao utilizador. É pedido ao utilizador que quer responder à mensagem de texto com o código de verificação ou para introduzir o código de verificação para a interface de início de sessão.
Notificação através da aplicação móvel | Neste modo, a aplicação Microsoft Authenticator impede o acesso não autorizado às contas e deixa de transações fraudulentas. Isto é feito utilizando uma notificação de emissão para o seu telemóvel ou dispositivo registado. Simplesmente o visualize a notificação e se for legítima toque em verificar. Caso contrário, pode escolher negar ou optar por negar e comunicar a notificação fraudulenta. Para obter informações sobre relatórios notificações fraudulentas veja como utilizar a funcionalidade de fraude de relatório e negar para autenticação Multifator.</br></br>A aplicação Microsoft Authenticator está disponível para [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072)e [IOS](http://go.microsoft.com/fwlink/?Linkid=825073).|
Código de verificação a partir da aplicação móvel | Neste modo, a aplicação Microsoft Authenticator pode ser utilizada como um token de software para gerar um código de verificação JURAMENTO. Este código de verificação, em seguida, pode ser introduzido juntamente com o nome de utilizador e palavra-passe para fornecer a segunda forma de autenticação.</li><br><p> A aplicação Microsoft Authenticator está disponível para [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072)e [IOS](http://go.microsoft.com/fwlink/?Linkid=825073).

### <a name="how-to-enabledisable-authentication-methods"></a>Como ativar/desativar métodos de autenticação

1. Inicie sessão no portal do Azure clássico.
2. No lado esquerdo, clique em do Active Directory.
3. Active Directory, clique no diretório que pretende ativar ou desativar os métodos de autenticação.
4. No directório de que selecionou, clique em configurar.
5. Na secção autenticação multifator, clique em Gerir definições de serviço.
6. Na página Definições de serviço, em Opções de verificação, selecione/anular a seleção das opções que pretende utilizar.</br></br>
![Opções de verificação](./media/multi-factor-authentication-whats-next/authmethods.png)
9. Clique em Guardar.
10. Clique em Fechar.
