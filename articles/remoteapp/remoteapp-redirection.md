<properties
    pageTitle="Utilização do redirecionamento no Azure RemoteApp | Microsoft Azure"
    description="Saiba como configurar e utilizar redirecionamento no RemoteApp"
    services="remoteapp"
    documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="elizapo" />

# <a name="using-redirection-in-azure-remoteapp"></a>Utilização do redirecionamento no RemoteApp do Azure

> [AZURE.IMPORTANT]
> Está a ser descontinuada Azure RemoteApp. Leia o [anúncio](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.

Redirecionamento do dispositivo permite aos seus utilizadores interagir com aplicações remotos utilizando os dispositivos ligados ao seu computador local, telemóvel ou tablet. Por exemplo, se tiver fornecido Skype através de Azure RemoteApp, o utilizador precisa a câmara instalada no seu PC para trabalhar com o Skype. Também é verdadeiro para uma periféricos ligado por intervalo de USB, monitores, altifalantes e impressoras.

RemoteApp tira partido de protocolo de ambiente de trabalho remoto (RDP) e RemoteFX para fornecer redireccionamento.

## <a name="what-redirection-is-enabled-by-default"></a>O que redirecionamento está ativado por predefinição?
Quando utiliza RemoteApp, o seguinte redireccionamento está ativado por predefinição. As informações entre parênteses Mostrar a definição de RDP.

- Reproduza sons no computador local (**Reproduzir neste computador**). (audiomode:i:0)
- Capturar áudio a partir do computador local e enviar para o computador remoto (**registo deste computador**). (audiocapturemode:i:1)
- Imprimir a impressoras locais (redirectprinters:i:1)
- Portas (redirectcomports:i:1)
- Cartão inteligente dispositivo (redirectsmartcards:i:1)
- Área de transferência (capacidade para copiar e colar) (redirectclipboard:i:1)
- Desmarque suavização de tipo de letra tipo (permitir suavização de tipo de letra: i:1)
- Redirecione Plug and Play suportados todos os dispositivos. (devicestoredirect:s: *)

## <a name="what-other-redirection-is-available"></a>O que outras redirecionamento está disponível?
Duas opções de redirecionamento estão desativadas por predefinição:

- Redirecionamento de unidade (mapeamento de unidade): unidades do seu computador local tornam-se as unidades mapeadas na sessão remota. Permite-lhe guardar ou abrir ficheiros a partir do seu unidades locais enquanto trabalha na sessão remota.
- Redirecionamento USB: pode utilizar os dispositivos USB ligados ao seu computador local dentro da sessão remota.

## <a name="change-your-redirection-settings-in-remoteapp"></a>Alterar as definições de redirecionamento no RemoteApp
Pode alterar as definições de redirecionamento de dispositivo para uma coleção de utilizando o PowerShell do Microsoft Azure com SDK. Depois de instalar o novo PowerShell e SDK, pela primeira vez configurá-lo para gerir a sua subscrição conforme descrito no [artigo como instalar e configurar o Azure PowerShell](../powershell-install-configure.md).

Em seguida, utilize um comando semelhante ao seguinte para definir as propriedades RDP personalizadas:

    Set-AzureRemoteAppCollection -CollectionName <collection name>  -CustomRdpProperty "drivestoredirect:s:*`nusbdevicestoredirect:s:*"

(Tenha em atenção que *N'* são utilizados como delimitador entre propriedades individuais.)

Para obter uma lista das quais as propriedades RDP personalizadas estão configuradas, execute o cmdlet seguinte. Tenha em atenção que as propriedades personalizadas só são apresentadas como resultados de saída e não as propriedades predefinidas do:  

    Get-AzureRemoteAppCollection -CollectionName <collection name>

Quando definir propriedades personalizadas tem de especificar todas as propriedades personalizadas sempre; caso contrário, a definição reverte para desativado.   

### <a name="common-examples"></a>Exemplos comuns
Utilize o cmdlet seguinte para ativar o redirecionamento de unidade:  

    Set-AzureRemoteAppCollection -CollectionName <collection name>  -CustomRdpProperty "drivestoredirect:s:*”

Utilize este cmdlet para activar USB e unidade redirecionamento:

    Set-AzureRemoteAppCollection -CollectionName <collection name>  -CustomRdpProperty "drivestoredirect:s:*`nusbdevicestoredirect:s:*"

Utilize este cmdlet para desativar a partilha de área de transferência:  

    Set-AzureRemoteAppCollection -CollectionName <collection name>  -CustomRdpProperty "redirectclipboard:i:0”

> [AZURE.IMPORTANT] Certifique-se de que completamente terminar a sessão todos os utilizadores na coleção de (e não apenas desligá-los) antes de testar a alteração. Para certificar-se de que os utilizadores têm completamente terminadas, aceda ao separador **sessões** na coleção de no portal do Azure e terminar a sessão quaisquer utilizadores que estão perder a ligação ou tiver sessão iniciadas. Por vezes, pode demorar alguns segundos para as unidades locais para mostrar no Explorer dentro da sessão.

## <a name="change-usb-redirection-settings-on-your-windows-client"></a>Altere as definições de redirecionamento de USB no seu cliente do Windows

Se pretender utilizar redirecionamento USB num computador que se liga aos RemoteApp, existem 2 ações que precisem de ocorrer. 1 - o seu administrador tem de ativar o redirecionamento de USB nível da coleção de utilizando o Azure PowerShell. 2 - em cada dispositivo em que pretende utilizar redirecionamento USB, tem de ativar uma política de grupo que lhe permita. Este passo terão de ser executadas para cada utilizador que quer utilizar redirecionamento USB.

> [AZURE.NOTE] Redirecionamento USB com RemoteApp do Azure só é suportado para computadores com o Windows.

### <a name="enable-usb-redirection-for-the-remoteapp-collection"></a>Ativar o redirecionamento de USB para a coleção de RemoteApp
Utilize o cmdlet seguinte para activar redirecionamento USB nível da coleção de:

    Set-AzureRemoteAppCollection -CollectionName <collection_name> -CustomRdpProperty "nusbdevicestoredirect:s:*"

### <a name="enable-usb-redirection-for-the-client-computer"></a>Ativar o redirecionamento de USB para o computador cliente

Para configurar as definições de redireccionamento USB no seu computador:

1. Abra o Editor de política de Grupo Local (GPEDIT. MSC). (Executar gpedit a partir de uma linha de comandos.)
2. Abra o **computador configuração administrativos\Componentes Windows \ Serviços de ambiente de trabalho ligação ambiente de trabalho Client\RemoteFX USB dispositivo redirecionamento**.
3. Faça duplo clique em **Permitir RDP redirecionamento de outros dispositivos RemoteFX USB suportados deste computador**.
4. Selecione **ativada**e, em seguida, selecione **administradores e utilizadores nos direitos de acesso de redirecionamento do RemoteFX USB**.
5. Abra uma linha de comandos com permissões administrativas e execute o seguinte comando:

        gpupdate /force
6. Reinicie o computador.

Também pode utilizar a ferramenta de gestão de política de grupo para criar e aplicar a política de redireccionamento USB para todos os computadores no seu domínio:

1. Inicie sessão no controlador de domínio como o administrador do domínio.
2. Abra consola de gestão de políticas de grupo. (Clique em **Iniciar > Ferramentas administrativas > Gestão de política de grupo**.)
3. Navegue para o domínio ou unidade organizacional para o qual pretende criar a política.
4. Botão direito do rato **Política predefinida de domínio**e, em seguida, clique em **Editar**.
5. Abra o **computador configuração administrativos\Componentes Windows \ Serviços de ambiente de trabalho ligação ambiente de trabalho Client\RemoteFX USB dispositivo redirecionamento**.
6. Faça duplo clique em **Permitir RDP redirecionamento de outros dispositivos RemoteFX USB suportados deste computador**.
7. Selecione **ativada**e, em seguida, selecione **administradores e utilizadores nos direitos de acesso de redirecionamento do RemoteFX USB**.
8. Clique em **OK**.  
