<properties
    pageTitle="Ativar o Proxy de aplicação do Azure AD | Microsoft Azure"
    description="Ative o Proxy de aplicação no portal clássico do Azure e instalar as conexões para o proxy inverso."
    services="active-directory"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="07/19/2016"
    ms.author="kgremban"/>

# <a name="enable-application-proxy-in-the-azure-portal"></a>Ativar o Proxy de aplicação no portal do Azure

Este artigo explica os passos para ativar o Proxy de aplicação do Microsoft Azure AD para o seu diretório da nuvem no Azure AD.

Se não estiver familiarizado com o Proxy de aplicação que pode ajudar a fazer, saiba mais sobre [como fornecer acesso seguro remoto a no local aplicações](active-directory-application-proxy-get-started.md).

## <a name="application-proxy-prerequisites"></a>Pré-requisitos de Proxy de aplicação
Antes de poder ativar e utilizar os serviços de Proxy de aplicação, tem de ter:

- Uma [subscrição do Microsoft Azure AD básica ou premium](active-directory-editions.md) e um diretório do Azure AD para a qual for um administrador global.
- Um servidor a executar o Windows Server 2012 R2 ou Windows 8.1 ou posterior, no qual pode instalar o conector de Proxy de aplicação. O servidor envia pedidos para os serviços de Proxy da aplicação na nuvem e necessita de uma ligação de HTTP ou HTTPS para as aplicações que esteja a publicar.

    - Para single sign-on para as suas aplicações publicadas, nesta máquina deve ser associados ao domínio no mesmo domínio AD como as aplicações que esteja a publicar.

- Se existir uma firewall no caminho, certifique-se de que está aberto, para que a conexão pode fazer pedidos de HTTPS (TCP) para o Proxy de aplicação. A conexão utiliza estas portas juntamente com subdomínios que fazem parte da msappproxy.net domínios de alto nível e servicebus.windows.net. Certifique-se abrir as portas seguintes para o tráfego de **saída** :

  	| Número de porta | Descrição |
  	| --- | --- |
  	| 80 | Ative o tráfego de saída de HTTP para a validação de segurança. |
  	| 443 | Ativar a autenticação de utilizador contra Azure AD (obrigatório apenas para o processo de registo de conexão) |
  	| 10100 – 10120 | Ativar as respostas de LOB HTTP enviadas novamente para o proxy |
  	| 9352, 5671 | Permitir a comunicação entre o conector por defeito até o serviço Azure para pedidos recebidos. |
  	| 9350 | Opcional, para ativar um melhor desempenho para pedidos recebidos |
  	| 8080 | Ativar a sequência de arranque de conexão e a atualização automática da conexão |
  	| 9090 | Activar o registo de conexão (obrigatório apenas para o processo de registo de conexão) |
  	| 9091 | Ativar a renovação automática do certificado de fidedignidade conexão |

    Se a firewall do impõe tráfego de acordo com os utilizadores de origem, abra as seguintes portas para o tráfego provenientes do Windows services em execução como um serviço de rede. Além disso, certifique-se activar porta 8080 para NT\Sistema.

- Se a sua organização utiliza servidores proxy para ligar à internet, demorar um olhar sobre a mensagem de blogue [trabalhar com os servidores proxy existentes no local](https://blogs.technet.microsoft.com/applicationproxyblog/2016/03/07/working-with-existing-on-prem-proxy-servers-configuration-considerations-for-your-connectors/) para obter detalhes sobre como configurá-los.

## <a name="step-1-enable-application-proxy-in-azure-ad"></a>Passo 1: Ativar o Proxy de aplicação no Azure AD
1. Inicie sessão como administrador no [portal clássica Azure](https://manage.windowsazure.com/).
2. Aceda ao Active Directory e selecione a pasta na qual pretende ativar o Proxy de aplicação.

    ![Do Active Directory - ícone](./media/active-directory-application-proxy-enable/ad_icon.png)

3. Selecione **Configurar** a partir da página de diretório e desloque-se para **O Proxy de aplicação**.
4. Botão de alternar **Ativar serviços de Proxy de aplicação para este directório** para **ativado**.

    ![Ativar o Proxy de aplicação](./media/active-directory-application-proxy-enable/app_proxy_enable.png)

5. Selecione **Transferir agora**. Isto leva-o para o **Azure AD aplicação Proxy conexão transferir**. Leia e aceite os termos de licenciamento e clique em **Transferir** para guardar o ficheiro do Windows Installer (.exe) para o conector.

## <a name="step-2-install-and-register-the-connector"></a>Passo 2: Instalar e registar o conector
1. Execute **AADApplicationProxyConnectorInstaller.exe** no servidor preparada de acordo com os pré-requisitos.
2. Siga as instruções no Assistente para instalar.
3. Durante a instalação, irá for pedido para registar o conector com o Proxy de aplicação do seu inquilino do Azure AD.

  - Forneça as credenciais de administrador global do Azure AD. O inquilino do administrador global pode ser diferente das suas credenciais do Microsoft Azure.
  - Certifique-se o administrador quem regista a conexão no mesmo directório ter ativado o serviço de Proxy de aplicação. Por exemplo, se o domínio de inquilino contoso.com, o administrador deve ser admin@contoso.com ou qualquer outro alias nesse domínio.
  - Se **A configuração de segurança avançada IE** estiver definido para **** no servidor sempre que estiver a instalar a conexão, poderá ser bloqueado o ecrã de registo. Siga as instruções na mensagem de erro para permitir o acesso. Certifique-se de que as funcionalidades de segurança do Internet Explorer está desativada.
  - Se não tiver êxito registo de conexão, consulte o artigo [Resolver problemas de Proxy de aplicação](active-directory-application-proxy-troubleshoot.md).  

4. Quando a instalação estiver concluída, dois novos serviços são adicionados ao seu servidor:

    - **Microsoft AAD aplicação Proxy Connector** permite conectividade
    - **Microsoft AAD aplicação Proxy conexão QuickBooks** é um serviço de atualização automatizado, que periodicamente verifica a existência de novas versões da conexão e atualiza o conector conforme necessário.

    ![Serviços de conector de Proxy de aplicação - captura de ecrã](./media/active-directory-application-proxy-enable/app_proxy_services.png)

5. Clique em **Concluir** na janela de instalação.

Para fins de elevada disponibilidade, deve implementar, pelo menos, dois conectores. Para implementar os conectores mais, repita os passos 2 e 3 acima. Cada conector deverá estar registado junto separadamente.

Se quer desinstalar a conexão, desinstale o serviço de conexão e o serviço do QuickBooks. Reinicie o computador para remover completamente o serviço.


## <a name="next-steps"></a>Próximos passos

Agora está pronto para [publicar aplicações com o Proxy de aplicação](active-directory-application-proxy-publish.md).

Se tiver aplicações que estejam em redes separadas ou localizações diferentes, pode utilizar grupos de conexão para organizar os conectores diferentes em unidades lógicas. Saiba mais sobre como [trabalhar com conexões de Proxy de aplicação](active-directory-application-proxy-connectors.md).
