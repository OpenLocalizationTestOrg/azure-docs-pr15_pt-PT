<properties 
    pageTitle="Atualizar o agente de PhoneFactor para o servidor de autenticação Multifator Azure"
    description="Este documento descreve como introdução ao Azure MFA servidor e como atualizar a partir do agente phonefactor mais antigo."
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="curtland"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/04/2016"
    ms.author="kgremban"/>

# <a name="upgrading-the-phonefactor-agent-to-azure-multi-factor-authentication-server"></a>Atualizar o agente de PhoneFactor para o servidor de autenticação Multifator Azure

Atualizar a partir do V5 PhoneFactor agente ou mais antigo para o servidor de autenticação Multifator Azure requer o agente de PhoneFactor e componentes interligadas desinstalado antes do servidor de autenticação Multifator e respectivos componentes interligados podem ser instalados.

## <a name="to-upgrade-the-phonefactor-agent-to-azure-multi-factor-authentication-server"></a>Atualizar o agente de PhoneFactor para o servidor de autenticação Multifator Azure
<ol>
<li>Em primeiro lugar, criar cópias de segurança do ficheiro de dados PhoneFactor. A localização de instalação predefinida é c:\Programas\Microsoft Files\PhoneFactor\Data\Phonefactor.pfdata.


<li>Se o Portal do utilizador estiver instalado:</li>
<ol>
<li>Navegue para a pasta de instalação e agregar o ficheiro da Web. config. A localização de instalação predefinida é C:\inetpub\wwwroot\PhoneFactor.</li>


<li>Se tiver adicionado temas personalizados para o portal, criar cópias de segurança a pasta personalizada abaixo do directório de C:\inetpub\wwwroot\PhoneFactor\App_Themes.</li>


<li>Desinstale o Portal do utilizador através do agente de PhoneFactor (disponível apenas se instalado no mesmo servidor como agente de PhoneFactor) ou através do Windows programas e funcionalidades.</li></ol>




<li>Se o serviço móvel do Web App estiver instalado:
<ol>
<li>Vá para a pasta de instalação e agregar o ficheiro da Web. config. A localização de instalação predefinida é C:\inetpub\wwwroot\PhoneFactorPhoneAppWebService.</li>
<li>Desinstalar o serviço móvel Web App através do Windows programas e funcionalidades.</li></ol>

<li>Se estiver instalado o SDK do serviço Web, desinstale o mesmo através do agente de PhoneFactor ou através do Windows programas e funcionalidades.

<li>Desinstalar o agente de PhoneFactor através do Windows programas e funcionalidades.

<li>Instale o servidor de autenticação Multifator. Note que o caminho de instalação é recolhido a partir do registo de instalação anterior do agente de PhoneFactor, de modo que deve instalar na mesma localização (por exemplo, C:\Program Files\PhoneFactor). Novas instalações terá uma predefinição de diferentes instalar o caminho (por exemplo, C:\Program Files\Multi fator autenticação Server). O ficheiro de dados para a esquerda pelo agente PhoneFactor anterior deve ser actualizado durante a instalação, para que os seus utilizadores e definições, devem continuar a não existem depois de instalar o servidor de autenticação Multifator novo.

<li>Se lhe for solicitado, ativar o servidor de autenticação Multifator e certifique-se de que é atribuída ao grupo de replicação correto.

<li>Se o SDK do serviço Web foi instalado anteriormente, instale o novo serviço Web SDK através da Interface de utilizador do servidor de autenticação Multifator. Note que o nome de directório virtual predefinido é agora "MultiFactorAuthWebServiceSdk" em vez de "PhoneFactorWebServiceSdk". Se pretender utilizar o nome do anterior, tem de alterar o nome do directório virtual durante a instalação. Caso contrário, se permitir a instalação utilizar o novo nome predefinido, tem de alterar o URL na todas as aplicações que fazem referência ao SDK do serviço Web como o Portal de utilizador e o serviço móvel do Web App para aponte para a localização correta.

<li>Se o Portal do utilizador foi instalado anteriormente no servidor de agente PhoneFactor, instale o Portal de utilizador de autenticação Multifator novo através da Interface de utilizador do servidor de autenticação Multifator. Note que o nome de directório virtual predefinido é agora "MultiFactorAuth" em vez de "PhoneFactor". Se pretender utilizar o nome do anterior, tem de alterar o nome do directório virtual durante a instalação. Caso contrário, se permitir a instalação utilizar o novo nome predefinido, deve clique no ícone do Portal de utilizador no servidor de autenticação Multifator e atualizar o URL do Portal de utilizador no separador Definições.

<li>Se o Portal de utilizador e/ou serviço móvel do Web App anteriormente estava instalado num servidor diferente do agente de PhoneFactor de:
<ol>
<li>Vá para a localização de instalação (por exemplo, C:\Program Files\PhoneFactor) e copie o installer(s) adequado para o outro servidor. Existem programas de instalação de 32 bits e 64 bits para o Portal de utilizador e o serviço móvel do Web App. Estes são chamados MultiFactorAuthenticationUserPortalSetupXX.msi e MultiFactorAuthenticationMobileAppWebServiceSetupXX.msi respetivamente.</li>
<li>Para instalar o Portal de utilizador no servidor web, abra uma linha de comandos como administrador e execute o MultiFactorAuthenticationUserPortalSetupXX.msi. Note que o nome de directório virtual predefinido é agora "MultiFactorAuth" em vez de "PhoneFactor". Se pretender utilizar o nome do anterior, tem de alterar o nome do directório virtual durante a instalação. Caso contrário, se permitir a instalação utilizar o novo nome predefinido, deve clique no ícone do Portal de utilizador no servidor de autenticação Multifator e atualizar o URL do Portal de utilizador no separador Definições. Os utilizadores existentes terá de ser informados sobre o novo URL.</li>
<li>Aceda ao Portal do utilizador instalar localização (por exemplo, C:\inetpub\wwwroot\MultiFactorAuth) e editar o ficheiro da Web. config. Copie os valores nas secções appSettings e applicationSettings a partir do seu ficheiro Web. config original que foi cópia de segurança antes da atualização para o novo ficheiro na Web. config. Se o novo nome de directório virtual predefinida foi constantemente ao instalar o SDK do serviço Web, altere o URL na secção applicationSettings para apontar para a localização correta. Se quaisquer outras predefinições foram alteradas no ficheiro Web. config anterior, aplica essas alterações mesmo para o novo ficheiro na Web. config.</li>
<li>Para instalar o serviço móvel do Web App no servidor web, abra uma linha de comandos como administrador e execute o MultiFactorAuthenticationMobileAppWebServiceSetupXX.msi. Note que o nome de directório virtual predefinido é agora "MultiFactorAuthMobileAppWebService" em vez de "PhoneFactorPhoneAppWebService". Se pretender utilizar o nome do anterior, tem de alterar o nome do directório virtual durante a instalação. Pretende escolher o nome de um endereço mais curto para tornam mais fácil para os utilizadores finais escrever nos seus dispositivos móveis. Caso contrário, se permitir a instalação utilizar o novo nome predefinido, deve clique no ícone de aplicação Mobile no servidor de autenticação Multifator e atualizar o URL do serviço móvel aplicação Web.</li>
<li>Aceda ao serviço Web App Mobile instalar localização (por exemplo, C:\inetpub\wwwroot\MultiFactorAuthMobileAppWebService) e editar o ficheiro da Web. config. Copie os valores nas secções appSettings e applicationSettings a partir do seu ficheiro Web. config original que foi cópia de segurança antes da atualização para o novo ficheiro na Web. config. Se o novo nome de directório virtual predefinida foi constantemente ao instalar o SDK do serviço Web, altere o URL na secção applicationSettings para apontar para a localização correta. Se quaisquer outras predefinições foram alteradas no ficheiro Web. config anterior, aplica essas alterações mesmo para o novo ficheiro na Web. config.</li></ol>
