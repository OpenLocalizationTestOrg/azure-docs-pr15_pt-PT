<properties 
    pageTitle="Começar a trabalhar com o servidor de autenticação Multifator do Azure"
    description="Esta é a página de autenticação multifator Azure que descreve como começar com o Azure MFA Server."
    services="multi-factor-authentication"
    keywords="Autenticação servidor, azure múltiplos fator autenticação ativação page da aplicação, transferência de servidor de autenticação"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="curtand"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/15/2016"
    ms.author="kgremban"/>

# <a name="getting-started-with-the-azure-multi-factor-authentication-server"></a>Começar a trabalhar com o servidor de autenticação Multifator do Azure




<center>![Nuvem](./media/multi-factor-authentication-get-started-server/server2.png)</center>

Agora que foi determinado se utilizar no local autenticação multifator, vamos introdução. Esta página abrange uma nova instalação do servidor e obter-configuração com o Active Directory no local. Se já tem o servidor de PhoneFactor instalada e está à procura de atualização, consulte o artigo [actualizar para o servidor de Multifator Azure](multi-factor-authentication-get-started-server-upgrade.md) ou se está a procurar informações sobre como instalar apenas o serviço web consulte o artigo [Implementar o Azure Multifator autenticação de servidor de serviço móvel do aplicação Web](multi-factor-authentication-get-started-server-webservice.md).


## <a name="download-the-azure-multi-factor-authentication-server"></a>Transferir o Server Azure autenticação Multifator



Existem duas formas diferentes que pode transferir o servidor de autenticação Multifator Azure. Ambos são feitos através do portal do Azure. O primeiro é gerindo o fornecedor de autenticação Multifator diretamente. O segundo é através das definições do serviço. A segunda opção requer um fornecedor de autenticação Multifator ou uma licença Azure MFA, Azure AD Premium ou do conjunto de aplicações do Enterprise mobilidade.


### <a name="to-download-the-azure-multi-factor-authentication-server-from-the-azure-portal"></a>Para transferir o servidor de autenticação Multifator de Azure a partir do portal do Azure
--------------------------------------------------------------------------------

1. Inicie sessão Portal do Azure como administrador.
2. No lado esquerdo, selecione do Active Directory.
3. Na página do Active Directory, na parte superior, clique **Fornecedores de autenticação Multifator**
4. Na parte inferior, clique em **Gerir**
5. Isto irá abrir uma nova página.  Clique em **transferências.** 
 ![Transferir](./media/multi-factor-authentication-sdk/download.png)
6. Acima **Gerar credenciais de ativação**, clique em **Transferir.** 
 ![Transferir](./media/multi-factor-authentication-get-started-server/download4.png)
7. Guarde a transferência.



### <a name="to-download-the-azure-multi-factor-authentication-server-via-the-service-settings"></a>Para transferir o servidor de autenticação Multifator Azure através das definições do serviço


1. Inicie sessão Portal do Azure como administrador.
2. No lado esquerdo, selecione do Active Directory.
3. Faça duplo clique na sua instância do Azure AD.
4. Na parte superior, clique em **Configurar**
![transferir](./media/multi-factor-authentication-sdk/download2.png)
5. Em autenticação multifator selecione **Gerir definições de serviço**
6. Na página de definições dos serviços, na parte inferior do ecrã, clique em **Ir para o portal**.
![Transferir](./media/multi-factor-authentication-get-started-server/servicesettings.png)
7. Isto irá abrir uma nova página. Clique em **transferências.**
8. Acima **Gerar credenciais de ativação**, clique em **Transferir.**
9. Guarde a transferência.




## <a name="install-and-configure-the-azure-multi-factor-authentication-server"></a>Instalar e configurar o servidor de autenticação Multifator Azure
Agora que tenha transferido do servidor pode instalar e configure-o.  Certifique-se de que o servidor que estiver a instalá-lo num cumpre os seguintes requisitos:



Requisitos do servidor de autenticação Multifator Azure|Descrição|
:------------- | :------------- |
Hardware|<li>200 MB de espaço no disco rígido</li><li>processador com capacidade de x32 ou x64</li><li>1 GB de RAM maior</li>
Software|<li>Windows Server 2008 ou maior se o anfitrião é um servidor de sistema operativo</li><li>Windows 7 ou posterior, se o anfitrião é um cliente SO</li><li>4.0 do Microsoft .NET Framework</li><li>SDK IIS 7.0 ou superior se instalar o utilizador portal ou serviço web</li>

### <a name="azure-multi-factor-authentication-server-firewall-requirements"></a>Requisitos de firewall do servidor de autenticação Multifator Azure
--------------------------------------------------------------------------------
Cada servidor MFA tem de conseguir comunicar na porta 443 saída ao seguinte:

- https://PFD.phonefactor.NET
- https://pfd2.phonefactor.NET
- https://CSS.phonefactor.NET

Se estiverem restringidos firewalls saídos na porta 443, os seguintes intervalos de endereços IP terá de ser aberto:

Sub-rede IP|Máscara de rede|Intervalo de IP
:------------- | :------------- | :------------- |
134.170.116.0/25|255.255.255.128|134.170.116.1 – 134.170.116.126
134.170.165.0/25|255.255.255.128|134.170.165.1 – 134.170.165.126
70.37.154.128/25|255.255.255.128|70.37.154.129 – 70.37.154.254

Se não estiver a utilizar funcionalidades de confirmação de eventos do Azure Multifator autenticação e se os utilizadores não estão autenticar com as aplicações móveis Multifator Auth a partir de dispositivos na rede da empresa o IP intervalos podem ser reduzidos ao seguinte:


Sub-rede IP|Máscara de rede|Intervalo de IP
:------------- | :------------- | :------------- |
134.170.116.72/29|255.255.255.248|134.170.116.72 – 134.170.116.79
134.170.165.72/29|255.255.255.248|134.170.165.72 – 134.170.165.79
70.37.154.200/29|255.255.255.248|70.37.154.201 – 70.37.154.206


### <a name="to-install-and-configure-the-azure-multi-factor-authentication-server"></a>Instalar e configurar o servidor de autenticação Multifator de Azure
--------------------------------------------------------------------------------


1. Faça duplo clique sobre o ficheiro executável. Isto vai começar a instalação.
2. No ecrã de selecionar a pasta de instalação, certifique-se de que a pasta está correta e clique em seguinte.
3. Depois de concluir a instalação, clique em Concluir.  Este procedimento inicia o Assistente de configuração.
4. No Assistente de configuração do ecrã de boas-vindas, coloque uma marca de verificação **Ignorar** ao utilizar o Assistente de configuração de autenticação e clique em **seguinte**.  Isto irá fechar o assistente e iniciar o servidor.
    ![Nuvem](./media/multi-factor-authentication-get-started-server/skip2.png)
5. Novamente na página que recomendamos transferido o servidor a partir de, clique no botão de **Credenciais de ativação gerar** .  Copie estas informações para o servidor de MFA Azure nas caixas fornecidas e clique em **Ativar**.


Os passos acima mostram uma configuração express com o Assistente de configuração.  Pode voltar a executar o Assistente de autenticação ao selecioná-lo a partir do menu Ferramentas no servidor.



##<a name="import-users-from-active-directory"></a>Importar os utilizadores a partir do Active Directory

Agora que o servidor é instalado e configurado rapidamente pode importar os utilizadores para o servidor de MFA Azure.

### <a name="to-import-users-from-active-directory"></a>Para importar os utilizadores do Active Directory
--------------------------------------------------------------------------------


1. Em servidor de MFA Azure, à esquerda, selecione **utilizadores**.
2. Na parte inferior, selecione **Importar a partir do Active Directory**.
3. Agora pode procurar utilizadores individuais ou procurar o diretório de AD ou com utilizadores do-los.  Neste caso, podemos irá especificar os utilizadores or.
4. Realce todos os utilizadores no lado direito e clique em **Importar**.  Deverá receber um pop-up informar que lhe foram efetuada com êxito.  Feche a janela de importação.

![Nuvem](./media/multi-factor-authentication-get-started-server/import2.png)

## <a name="send-users-an-email"></a>Enviar aos utilizadores uma mensagem de e-mail
Agora que importou os seus utilizadores para o servidor de autenticação Multifator de Azure, é aconselhável que enviar os seus utilizadores uma mensagem de e-mail informá-los de que foi registados na autenticação multifator.

Com o servidor de autenticação Multifator Azure, existem várias formas para configurar os seus utilizadores para utilizando a autenticação multifator.  Por exemplo, se souber números de telefone dos utilizadores ou foram poder importar os números de telefone para o servidor de autenticação Multifator Azure a partir do diretório da sua empresa, o e-mail será informar os utilizadores de configurado para utilizar a autenticação Multifator do Azure, fornecer algumas instruções utilizando a autenticação Multifator do Azure e informar o utilizador do número de telefone, que estes irão receber os respetivos autenticação no.  

O conteúdo do correio eletrónico variam consoante o método de autenticação que tenha sido definida para o utilizador (por exemplo, telefonema, SMS, a aplicação móvel).  Por exemplo, se o utilizador for necessária para utilizar um PIN quando autenticam, o e-mail irá indicar-lhes o que foi definida os respetivos PIN inicial como.  Os utilizadores normalmente são necessários para alterar o seu PIN durante a sua primeira autenticação.

Se os números de telefone dos utilizadores não tenham sido configurados ou importados para o servidor de autenticação Multifator Azure ou os utilizadores estão pré-configurada para utilizar a aplicação móvel para autenticação, pode enviar-lhes uma mensagem de e-mail que permite que as pessoas que tenham sido configurados para utilizar a autenticação Multifator do Azure e -vai encaminhá-lo para concluir a sua inscrição conta através do Portal de utilizador do Azure Multifator autenticação.  Uma hiperligação será incluída-se de que o utilizador clica para aceder ao Portal de utilizador. Quando o utilizador clica na ligação, o respetivo browser web abra e tire da sua empresa Azure Multifator autenticação de utilizador portal.   


### <a name="configuring-email-and-email-templates"></a>Configurar o e-mail e modelos de e-mail

Ao clicar no ícone de correio eletrónico à esquerda pode configurar as definições para estas mensagens de correio eletrónico a enviar.  Este é onde pode introduzir as informações de SMTP do seu servidor de correio e permite-lhe enviar um e-mail e uma altura aberta ao adicionar uma marca de verificação para enviar mensagens para a caixa de verificação utilizadores.

![Definições de e-mail](./media/multi-factor-authentication-get-started-server/email1.png)

No separador conteúdo do E-Mail, irá ver todos os vários modelos de correio eletrónico que estão disponíveis para escolha.  Pelo dependendo de como tiver configurado os utilizadores utilizem a autenticação multifator, pode escolher o modelo que melhor se adequa lhe.

![Modelos de e-mail](./media/multi-factor-authentication-get-started-server/email2.png)

## <a name="how-the-azure-multi-factor-authentication-server-handles-user-data"></a>Como o servidor de autenticação Multifator Azure trata os dados de utilizador

Quando utiliza a autenticação Multifator (MFA) servidor no local, dados de um utilizador são armazenados nos servidores no local. Sem dados de utilizador persistentes são armazenados na nuvem. Quando o utilizador efetua a autenticação de dois fatores, o servidor de MFA envia dados para o serviço de nuvem do Azure MFA para efetuar a autenticação. Quando estes pedidos de autenticação são enviados para o serviço de nuvem, os campos seguintes são enviados no pedido e registos de início para que fiquem disponíveis em relatórios de autenticação/a utilização do cliente. Alguns dos campos são opcionais para que pode ser activadas ou desactivadas dentro do servidor de autenticação Multifator. Comunicação do servidor MFA para o serviço de nuvem MFA utiliza SSL/TLS através da porta 443 saída. Estes campos são:

- ID exclusivo - nome de utilizador ou ID de servidor interno MFA
- Primeiro e último nome - opcional
- Endereço de e-mail - opcional
- Número de telefone - quando efetuar uma chamada de voz ou a autenticação do SMS
- Token de dispositivo - quando efetuar a autenticação de aplicação móvel
- Modo de autenticação
- Resultado de autenticação
- Nome do servidor MFA
- Servidor MFA IP
- Cliente IP – se disponível



Além dos campos acima, o resultado de autenticação (sucesso/negação) e a razão para qualquer recusas também está disponível através de relatórios de utilização/autenticação e armazenadas com os dados de autenticação.


## <a name="advanced-azure-multi-factor-authentication-server-configurations"></a>Configurações de servidor de autenticação Multifator Azure avançadas
Para obter informações adicionais sobre a configuração avançada e informações de configuração, utilize a tabela abaixo.

Método|Descrição
:------------- | :------------- |
[Portal de utilizador](multi-factor-authentication-get-started-portal.md)|  Informações sobre a configuração e configurar o portal de utilizador, incluindo implementação e gestão personalizada do utilizador.
[Serviço de Federação do Active Directory](multi-factor-authentication-get-started-adfs.md)|Informações sobre como configurar a autenticação Multifator de Azure com o AD FS.
[Autenticação RADIUS](multi-factor-authentication-get-started-server-radius.md)|  Informações sobre como configurar e configurar o servidor de MFA Azure com raio.
[Autenticação do IIS](multi-factor-authentication-get-started-server-iis.md)|Informações sobre a instalação e configuração do servidor de MFA do Azure com o IIS.
[Autenticação do Windows](multi-factor-authentication-get-started-server-windows.md)|  Informações sobre a instalação e configuração do servidor de MFA do Azure com autenticação do Windows.
[Autenticação LDAP](multi-factor-authentication-get-started-server-ldap.md)|Informações sobre como configurar e configurar o servidor de MFA Azure com autenticação LDAP.
[Remoto Gateway de ambiente de trabalho e servidor de autenticação Multifator Azure utilizar RADIUS](multi-factor-authentication-get-started-server-rdg.md)|  Informações sobre o programa de configuração e configurar o servidor de MFA Azure com Gateway de ambiente de trabalho remoto utilizar RADIUS.
[Sincronizar com o Active Directory do Windows Server](multi-factor-authentication-get-started-server-dirint.md)|Informações sobre a instalação e configuração de sincronização entre o Active Directory e o servidor de MFA Azure.
[Implementar o serviço do Azure autenticação Multifator servidor Web da aplicação móvel](multi-factor-authentication-get-started-server-webservice.md)|Informações sobre como configurar e configurar o serviço do Azure MFA servidor web.
