<properties 
   pageTitle="Configurar o proxy de web para um dispositivo StorSimple | Microsoft Azure"
   description="Saiba como utilizar o Windows PowerShell para StorSimple para configurar definições de proxy web para o seu dispositivo StorSimple."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/17/2016"
   ms.author="alkohli" />

# <a name="configure-web-proxy-for-your-storsimple-device"></a>Configurar o web proxy para o seu dispositivo StorSimple

## <a name="overview"></a>Descrição geral

Neste tutorial descreve como utilizar o Windows PowerShell para StorSimple para configurar e ver as definições de proxy web para o seu dispositivo StorSimple. As definições de proxy web são utilizadas pelo dispositivo StorSimple quando comunicar com a nuvem. Um servidor de proxy web é utilizado para adicionar outra camada de segurança, filtro de conteúdo, cache para requisitos de largura de banda facilidade ou até mesmo ajuda com a análise.

Web proxy é uma configuração opcional para o seu dispositivo StorSimple. Pode configurar o proxy do web apenas através do Windows PowerShell para StorSimple. A configuração é um processo de dois passos da seguinte forma:

1. Configurar definições de proxy web através do Assistente de configuração ou o Windows PowerShell para StorSimple cmdlets pela primeira vez.

2. Em seguida, a ativar as definições de proxy web configurado através do Windows PowerShell para StorSimple cmdlets.

Após a configuração do proxy web está concluída, pode ver as definições de proxy web configurado no serviço do Microsoft Azure StorSimple gestor e o Windows PowerShell para StorSimple. 

Depois de ler neste tutorial, irá poderá:

- Configurar o proxy do web utilizando o Assistente de configuração e os cmdlets do
- Activar o proxy web utilizando os cmdlets do
- Ver definições de proxy da web no portal do Azure clássico
- Resolver problemas de erros durante a configuração do proxy web


## <a name="configure-web-proxy-via-windows-powershell-for-storsimple"></a>Configurar o proxy de web através do Windows PowerShell para StorSimple

Utilize um dos seguintes procedimentos para configurar definições de proxy da web:

- Assistente de configuração para orientá-lo através dos passos de configuração.

- Cmdlets do Windows PowerShell para StorSimple.

Cada um dos seguintes métodos é abordada nas secções seguintes.

## <a name="configure-web-proxy-via-the-setup-wizard"></a>Configurar o proxy de web através do Assistente de configuração

Pode utilizar o Assistente de configuração para orientá-lo através dos passos de configuração do proxy web. Execute os seguintes passos para configurar o proxy da web no seu dispositivo.

#### <a name="to-configure-web-proxy-via-the-setup-wizard"></a>Para configurar o proxy do web através do Assistente de configuração

1. No menu de consola série, selecione **Iniciar sessão com acesso total** a opção 1 e forneça a **palavra-passe de administrador do dispositivo**. Escreva o seguinte comando para iniciar uma sessão de Assistente de configuração:

    `Invoke-HcsSetupWizard`

2. Se esta for a primeira vez que utilizou o Assistente de configuração para o registo de dispositivo, terá de configurar todas as definições de rede necessários até chegar a configuração do proxy web. Se já está registado o seu dispositivo, pode aceitar todas as definições de rede configuradas até chegar a configuração do proxy web. No Assistente de configuração, quando lhe for pedido para configurar definições de proxy da web, escreva **Sim**.

3. Para o **URL da Web Proxy**, especifique o endereço IP ou o nome de domínio completamente qualificado (FQDN) do seu servidor de proxy de web e o número da porta TCP que pretende que o seu dispositivo para utilizar quando comunicar com a nuvem. Utilize o seguinte formato:

    `http://<IP address or FQDN of the web proxy server>:<TCP port number>`

    Por predefinição, o número da porta TCP 8080 é especificado.

4. Escolha o tipo de autenticação como **NTLM**, **básica**ou **nenhum**. Básica é a autenticação menos segura para a configuração do servidor proxy. Gestor de LAN NT (NTLM) é um protocolo de autenticação altamente seguro e complexa que utiliza um sistema de mensagens de três vias (por vezes, existem quatro se for necessária integridade adicional) para autenticar um utilizador. A autenticação de predefinido é NTLM. Para mais informações, consulte o artigo [básicos](http://hc.apache.org/httpclient-3.x/authentication.html) e [a autenticação NTLM](http://hc.apache.org/httpclient-3.x/authentication.html). 

    > [AZURE.IMPORTANT] **No serviço StorSimple Gestor, os gráficos de monitorização do dispositivo não funciona quando estiver básicas ou a autenticação NTLM está ativada na configuração do servidor proxy para o dispositivo. Para os gráficos de monitorização trabalhar, terá de se certificar de que a autenticação está definida para nenhum.**

5. Se estiver a utilizar autenticação, fornece um **Nome de utilizador do Web Proxy** e uma **Palavra-passe do Web Proxy**. Também terá de confirmar a palavra-passe.

    ![Configurar o Proxy da Web no StorSimple Device1](./media/storsimple-configure-web-proxy/IC751830.png)

Se está a registar o seu dispositivo pela primeira vez, continue com o registo. Se já foi registado o seu dispositivo, o assistente irá sair. As definições configuradas serão guardadas.

Web proxy também irão estar ativado agora. Pode ignorar o passo [Activar o proxy web](#enable-web-proxy) e ir diretamente para [definições de proxy de web de vista no portal do Azure clássica](#view-web-proxy-settings-in-the-azure-classic-portal).


## <a name="configure-web-proxy-via-windows-powershell-for-storsimple-cmdlets"></a>Configurar o proxy de web através do Windows PowerShell para StorSimple cmdlets

Uma forma alternativa para configurar definições de proxy da web é através o Windows PowerShell para StorSimple cmdlets. Execute os seguintes passos para configurar o proxy web.

#### <a name="to-configure-web-proxy-via-cmdlets"></a>Para configurar o proxy do web através de cmdlets

1. No menu de consola série, selecione a opção 1, **Iniciar sessão com acesso total**. Quando lhe for pedido, forneça a **palavra-passe de administrador do dispositivo**. A palavra-passe predefinida é `Password1`.

2. Na linha de comandos, escreva:

    `Set-HcsWebProxy -Authentication NTLM -ConnectionURI "<http://<IP address or FQDN of web proxy server>:<TCP port number>" -Username "<Username for web proxy server>"`

    Fornecer e confirme a palavra-passe quando lhe for pedido, conforme apresentado abaixo.

    ![Configurar o Proxy da Web no StorSimple Device3](./media/storsimple-configure-web-proxy/IC751831.png)

O proxy web agora está configurado e tem de estar ativada.

## <a name="enable-web-proxy"></a>Activar o proxy web

Web proxy é desativado por predefinição. Depois de configurar as definições de proxy web no seu dispositivo StorSimple, tem de utilizar o Windows PowerShell para StorSimple para ativar as definições de proxy web.

> [AZURE.NOTE] **Este passo não será necessário se tiver utilizado o Assistente de configuração para configurar o proxy web. Web proxy automaticamente está ativado por predefinição após uma sessão de Assistente de configuração.**

Execute os seguintes passos no Windows PowerShell para StorSimple activar o proxy web no seu dispositivo:

#### <a name="to-enable-web-proxy"></a>Para ativar o proxy web

1. No menu de consola série, selecione a opção 1, **Iniciar sessão com acesso total**. Quando lhe for pedido, forneça a **palavra-passe de administrador do dispositivo**. A palavra-passe predefinida é `Password1`.

2. Na linha de comandos, escreva:

    `Enable-HcsWebProxy`

    Ativou agora a configuração do proxy web no seu dispositivo StorSimple.

    ![Configurar o Proxy da Web no StorSimple Device4](./media/storsimple-configure-web-proxy/IC751832.png)

## <a name="view-web-proxy-settings-in-the-azure-classic-portal"></a>Ver definições de proxy da web no portal do Azure clássico

As definições de proxy web estão configuradas através da interface do Windows PowerShell e não podem ser alteradas a partir no interior do portal clássico. No entanto, pode, ver estas definições configuradas no portal do clássico. Execute os passos seguintes para ver o proxy web.

#### <a name="to-view-web-proxy-settings"></a>Para ver as definições de proxy web
1. Navegue para **serviço do Gestor de StorSimple > dispositivos**. Selecione e clique num dispositivo e, em seguida, aceda a **Configurar**.
1. Desloque-se para baixo na página **Configurar** **definições de proxy Web** secção. Pode ver as definições de proxy web configurado no seu dispositivo StorSimple conforme apresentado abaixo.

    ![Proxy de Web de vista no Portal de gestão](./media/storsimple-configure-web-proxy/ViewWebProxyPortal_M.png)
 
## <a name="errors-during-web-proxy-configuration"></a>Erros durante a configuração do proxy web

Se as definições de proxy web tiverem sido configuradas incorretamente, mensagens de erro serão apresentadas ao utilizador no Windows PowerShell para StorSimple. A seguinte tabela explica algumas destas mensagens de erro, os respetivos causas prováveis e acções recomendadas.

|Acções em série n.|Código de erro HRESULT|Causa possível raiz|Acção recomendada|
|:---|:---|:---|:---|
|1.|0x80070001|Comando é executado do controlador de passivo e não é possível comunicar com o controlador ativo.|Execute o comando no controlador ativo. Para executar o comando do controlador de passivo, terá de resolver a conectividade de passivo para controlador ativo. Terá de contrate Microsoft Support se este conectividade é interrompida.|
|2.|0x800710dd - o identificador de operação não é válido|Definições de proxy não são suportadas no dispositivo virtual StorSimple.|Definições de proxy não são suportadas no dispositivo virtual StorSimple. Estes apenas podem ser configuradas num dispositivo físico StorSimple.|
|3.|0x80070057 - parâmetro inválido|Um dos parâmetros fornecidos para as definições de proxy não é válido.|O URI não é fornecido no formato correto. Utilize o seguinte formato:`http://<IP address or FQDN of the web proxy server>:<TCP port number>`|
|4.|0x800706BA - servidor RPC não disponível|A causa de raiz é uma das seguintes opções:</br></br>Cluster não está para cima.</br></br>Serviço de DataPath não está em execução.</br></br>O comando é executado a partir do controlador de passiva e não é possível comunicar com o controlador ativo.|Fórum realizam Support da Microsoft para garantir que o cluster estiver para cima e datapath serviço está em execução.</br></br>Execute o comando do controlador de ativo. Se pretender executar o comando do controlador de passivo, terá de Certifique-se de que o controlador de passivo possam comunicar com o controlador de ativo. Terá de contrate Microsoft Support se este conectividade é interrompida.|
|5.|0x800706be - chamada RPC falhou|Cluster é premida.|Fórum realizam Support da Microsoft para garantir que o cluster está para cima.|
|6.|0x8007138f - recurso cluster não encontrado|Recurso de cluster de serviço da plataforma não se encontra. Isto pode acontecer quando a instalação não foi inicial maiúscula.|Poderá ter de efetuar uma fábrica Repor no seu dispositivo. Poderá ter criar um recurso de plataforma. Contacte o Microsoft Support para os passos seguintes.|
|7.|0x8007138c - recurso de cluster não online|Recursos de cluster de plataforma ou datapath não estão online.|Contacte o suportehttp Support da Microsoft para ajudar a garantir que estão online o recurso de serviço datapath e a plataforma.|

> [AZURE.NOTE] 
> 
> -  Lista de acima das mensagens de erro não é exaustiva. 
> - Não serão apresentados no portal do Azure clássico no seu serviço de Gestor de StorSimple erros relacionados com definições de proxy da web. Se existir um problema com o proxy web após a configuração está concluída, o estado do dispositivo será alterada para **Offline** no portal do clássico. |

## <a name="next-steps"></a>Próximos passos

- Se quaisquer problemas com que se deparar enquanto a implementar o seu dispositivo ou configurar as definições de proxy da web, consulte a [resolução de problemas na sua implementação de dispositivo StorSimple](storsimple-troubleshoot-deployment.md).

- Para saber como utilizar o serviço do Gestor de StorSimple, aceda a [utilizar o serviço do Gestor de StorSimple para administrar o seu dispositivo StorSimple](storsimple-manager-service-administration.md).
