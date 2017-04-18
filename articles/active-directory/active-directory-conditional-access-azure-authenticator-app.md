
<properties
    pageTitle="Azure autenticador para Android | Microsoft Azure"
    description="Aplicação do Microsoft Azure autenticador pode ser utilizada para iniciar sessão para aceder a recursos de trabalho. A aplicação do Azure autenticador notifica-o de um pedido de verificação de dois fatores pendente apresentando um alerta para o dispositivo móvel."
    services="active-directory"
    documentationCenter=""
    authors="femila"
    manager="swadhwa"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="femila"/>

# <a name="azure-authenticator-for-android"></a>Azure autenticador para Android

Pode ter recomendado o administrador de TI para utilizar o autenticador do Microsoft Azure para iniciar sessão para aceder aos seus recursos de trabalho. Esta aplicação fornece estas duas opções de início de sessão no:

* Autenticação Multifator permite-lhe proteger as suas contas escolar ou profissional com a verificação de dois passos. Pode iniciar sessão utilizando mais do que conhece (por exemplo, a palavra-passe) e proteger a conta ainda mais com algo com (uma chave de segurança a partir desta aplicação). A aplicação do Azure autenticador notifica-o de um pedido de verificação de dois fatores pendente apresentando um alerta para o dispositivo móvel. Tem de simplesmente ver o pedido na aplicação e toque em verificar ou Cancelar. Em alternativa, poderá ser-lhe pedido para introduzir o código de apresentada na aplicação.

* Conta profissional permite-lhe ativar o seu telemóvel ou tablet Android para um dispositivo fidedigno e fornecer único início de sessão (SSO) aos aplicações da empresa. O administrador de TI pode pedir-lhe adicionar uma conta de trabalho para poder aceder a recursos da empresa. SSO permite-lhe iniciar sessão, uma vez e automaticamente recorrer de iniciar sessão ao longo da sua empresa tem à disposição de todas as aplicações.

## <a name="installing-the-azure-authenticator-app"></a>Instalar a aplicação autenticador Azure

Pode instalar a aplicação autenticador Azure a partir da Google Play Store.
As instruções para adicionar a conta de trabalho a partir do dispositivo de vs um Samsung Android do dispositivo Samsung Android são um pouco diferentes. As instruções para ambos estão indicadas abaixo.

<a name="adding-the-work-account-from-samsung-android-device"></a>Adicionar a conta de trabalho a partir do dispositivo Samsung Android
----------------------------------------------------------------------------------------------------------------
###<a name="adding-the-work-account-through-the-app-home-screen"></a>Adicionar a conta de trabalho através do ecrã inicial da aplicação

As instruções seguintes são aplicáveis para dispositivos Samsung GS3 e acima telefones ou Note2 e acima tablets.

1. No ecrã principal da aplicação, aceite o contrato de licença do utilizador final (EULA).
2. No ecrã ativar conta, clique no menu de contexto no lado direito e selecione a **conta profissional**.
3. No ecrã Adicionar conta, selecione a** Conta profissional**.
4. No ecrã de administrador de dispositivo de ativar, clique em **Ativar**.
5. No ecrã de política de privacidade, selecione a caixa de verificação e clique em **Confirmar**.
6. No ecrã do local de trabalho participar, introduza o ID de utilizador fornecido pela sua organização e clique em **participar**.
7. Para iniciar sessão aplicação autenticador Azure, introduza o seu organizacional um * * * conta e palavra-passe e clique em **Iniciar sessão**.
8. O ecrã seguinte, que apresenta informações sobre a autenticação multifator (MFA) é para segurança adicional e é opcional. Verá este ecrã se o seu trabalho ou escola requer autenticação de segundo factor para criar a conta profissional. Fornece instruções para confirmar a sua conta.
9. O ecrã de participação do local de trabalho apresenta a mensagem, "**participar do seu local de trabalho**". A aplicação autenticador Azure está a tentar associar o seu dispositivo ao seu local de trabalho.
10. Deverá ver a mensagem local de trabalho associadas no ecrã seguinte.

>[AZURE.NOTE]
> São permitidos uma conta de trabalho única no seu dispositivo.

### <a name="adding-the-work-account-from-the-settings-menu"></a>Adicionar a conta de trabalho a partir do menu Definições
Depois de ter instalado a aplicação do Azure autenticador, também pode criar uma conta de trabalho a partir do Gestor de conta Android.

1. A partir do menu definições, navegue para **contas** e clique em **Adicionar conta**.
2. Siga os passos 3-10 no procedimento, adicionar a conta de trabalho através do ecrã principal do aplicação, para adicionar uma conta do trabalho.

<a name="adding-the-work-account-from-a-non-samsung-android-device"></a>Adicionar a conta de trabalho a partir de um dispositivo não Samsung Android
------------------------------------------------------------------------------------------------------------------
### <a name="adding-the-work-account-through-the-app-home-screen"></a>Adicionar a conta de trabalho através do ecrã inicial da aplicação

1. No ecrã principal da aplicação, aceite o contrato de licença do utilizador final (EULA).
2. No ecrã ativar conta, clique no menu de contexto no lado direito e selecione a **conta profissional**.
3. No ecrã contas, clique em **Adicionar conta**.
4. Se vir o ecrã de contas, clique em **Adicionar conta**. Se uma conta do trabalho já é criada anteriormente, irá ver um ecrã de sincronização que mostra os existentes conta. Pode manter a conta de trabalho ao basta tocar na seta para trás ao ecrã principal. Em alternativa, pode selecionar a conta a remover e recriar um novo trabalho no local de trabalho participar ecrã conta, introduza o ID de utilizador fornecido pela sua organização e clique em participar.
5. Para iniciar sessão aplicação autenticador Azure, introduza a sua conta institucional e a palavra-passe e clique em **Iniciar sessão**.
7. O ecrã seguinte, que apresenta informações sobre a autenticação multifator (MFA) é para segurança adicional e é opcional. Verá este ecrã se o seu trabalho ou escola requer autenticação de segundo factor para criar a conta profissional. Fornece instruções para confirmar a sua conta.
8. No ecrã seguinte, clique em **OK** . Não altere o nome do certificado.
a mensagem, "Aderir o seu local de trabalho". A aplicação autenticador Azure está a tentar associar o seu dispositivo ao seu local de trabalho.
Deverá ver a mensagem local de trabalho associadas no ecrã seguinte.

>[AZURE.NOTE]
> São permitidos uma conta de trabalho única no seu dispositivo.

Depois de ter instalado a aplicação do Azure autenticador, também pode criar uma conta de trabalho a partir do Gestor de conta Android.

1. A partir do menu **Definições** , navegue para contas e clique em **Adicionar conta**.
2. Siga os passos 2-7 no procedimento, adicionar a conta de trabalho através de aplicação casa ecrã * *, para adicionar uma conta do trabalho.

### <a name="how-to-find-out-which-version-is-installed"></a>Como saber qual a versão instalada

1. Pode saber qual a versão da aplicação autenticador Azure e versões do serviço associado estão instaladas no seu dispositivo.
2. A partir de pop-up menu, clique em **Acerca**.
3. O ecrã sobre apresenta os serviços da aplicação e as versões instaladas no seu dispositivo.
 
### <a name="sending-log-files-to-report-issues"></a>Enviar ficheiros de registo para problemas de relatório

1. Siga as orientações no Microsoft Support para comunicar um incidente com a aplicação do Azure autenticador, obter um número de incidente e enviar ficheiros de registo contra o número de incidente atribuído da seguinte forma:
2. A partir de pop-up menu, clique em **Iniciar sessão**.
3. Se tiver um incidente de abrir com o Microsoft Support, tome nota do incidente número (terá de-lo para um passo posterior). Se ainda não tiver criado um incidente de suporte e gostaria de assistência, siga as orientações de [Suporte da Microsoft](https://support.microsoft.com/en-us/contactus) para abrir um novo incidente.
4. No ecrã registo, clique em **Enviar agora**.
5. Selecione o fornecedor de e-mail que pretende utilizar.
7. Se já tiver um incidente de abrir Microsoft Support, contacte a engenharia de suporte atribuída ao seu problema para saber como enviar os dados do registo e tiver associadas com o seu incidente. O suporte técnico irá fornecer-lhe informações para a linha de endereço e o assunto da mensagem de correio electrónico. Se ainda não tiver um incidente de suporte, siga as orientações na Support da Microsoft para abrir um novo incidente.
9. Edite a linha **para** e a linha de **Assunto** com as informações que recebeu a partir do Microsoft Support.
10. A aplicação do Azure autenticador anexa o ficheiro de registo para o e-mail que estiver a enviar. Descreva o problema que está a ocorrer, atualize a lista de destinatários (opcional) e, envie o e-mail.

### <a name="deleting-the-work-account-and-leaving-your-workplace"></a>Eliminar a conta do trabalho e ao deixar o seu local de trabalho

Pode remover a conta de trabalho que criou em qualquer altura da seguinte forma:

**Para eliminar a conta de trabalho a partir do menu Definições**

1. A partir do Gestor de contas, selecione a **conta profissional**.
2. No ecrã conta profissional, nas **Definições gerais**, selecione **definições da conta – abandonar a sua rede local de trabalho**.
3. Selecione **Sair** no ecrã do **Local de trabalho participar** .
4. Quando a mensagem "tem a certeza de qual pretende sair local de trabalho" for apresentada, clique em **OK** .
5. Este procedimento garante que eliminou a conta de trabalho a partir do seu local de trabalho.

>[AZURE.NOTE]
>É recomendado que não utilize a opção Remover conta para eliminar uma conta de trabalho como esta opção pode não funcionar em algumas versões anteriores do Android.

##<a name="uninstalling-the-app"></a>A desinstalação da aplicação

Num dispositivo Samsung Android, privilégios de administrador do dispositivo tem de ser removidos da seguinte forma antes de desinstalar o 
1. Em **Definições**, em **sistema**, selecione **segurança**.
2. Dentro de D**evice administração**, clique em **administradores de dispositivo**. Certifique-se de que a caixa de verificação junto ao **Azure autenticador** está desmarcada.

##<a name="troubleshooting"></a>Resolução de problemas

Se vir o **Erro Keystore**, é possível que não tem o bloqueio de ecrã Definir o com um PIN. Para contornar este problema, desinstale a aplicação do Azure autenticador, configurar um PIN para o ecrã de bloqueio e reinstale a aplicação.
