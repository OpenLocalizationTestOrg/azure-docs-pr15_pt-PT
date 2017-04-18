<properties 
   pageTitle="Ligar remotamente ao seu dispositivo StorSimple | Microsoft Azure"
   description="Explica como configurar o seu dispositivo, para gestão remota e como ligar ao Windows PowerShell para StorSimple através de HTTP ou HTTPS."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="06/21/2016"
   ms.author="alkohli" />

# <a name="connect-remotely-to-your-storsimple-device"></a>Ligar remotamente ao seu dispositivo StorSimple

## <a name="overview"></a>Descrição geral

Pode utilizar o Windows PowerShell remoto para ligar para o seu dispositivo StorSimple. Ao ligar desta forma, não verá um menu. (Pode ver um menu apenas se a série consola no dispositivo que utilizar para ligar.) Com o Windows PowerShell remoto, pode ligar para um espaço de execução específico. Também pode especificar o idioma de apresentação. 

Para obter mais informações sobre como utilizar o Windows PowerShell remoto para gerir o seu dispositivo, aceda a [Utilizar o Windows PowerShell para StorSimple administrar o seu dispositivo StorSimple](storsimple-windows-powershell-administration.md).

Neste tutorial explica como configurar o seu dispositivo, para gestão remota e, em seguida, como ligar ao Windows PowerShell para StorSimple. Pode utilizar HTTP ou HTTPS para ligar através do Windows PowerShell remoto. No entanto, quando estiver a decidir como ligar ao Windows PowerShell para StorSimple, considere o seguinte: 

- Ligar-se diretamente ao consola de série do dispositivo é segura, mas ligar a série consola através de parâmetros de rede não está. Tenha cuidado com o risco de segurança quando se liga ao consola de série do dispositivo através de parâmetros de rede. 

- Ligar-se através de uma sessão de HTTP poderá oferecem mais segurança do que a ligar-se através da consola de série através da rede. Embora não seja o método mais seguro, é aceitável em redes fidedignas. 

- Através de uma sessão de HTTPS com um certificado autoassinado é o mais seguro e ligar a opção recomendada.

Pode ligar remotamente para a interface do Windows PowerShell. No entanto, acesso remoto para o seu dispositivo StorSimple através da interface do Windows PowerShell não está ativado por predefinição. Pode ter de ativar primeiro gestão remota no dispositivo e, em seguida, no cliente que é utilizada para aceder ao seu dispositivo.

Os passos descritos neste artigo foram efectuados num sistema anfitrião a executar o Windows Server 2012 R2.

## <a name="connect-through-http"></a>Ligar-se através de HTTP

Ligar ao Windows PowerShell para StorSimple através de uma sessão de HTTP oferece mais segurança do que a ligar através da consola do seu dispositivo StorSimple série. Embora não seja o método mais seguro, é aceitável em redes fidedignas.

Pode utilizar o portal clássico Azure ou a série consola para configurar a gestão remota. Selecione os seguintes procedimentos:

- [Utilizar o portal do clássico Azure para ativar a gestão remota através de HTTP](#use-the-azure-classic-portal-to-enable-remote-management-over-http)

- [Utilizar a série consola para activar a gestão remota através de HTTP](#use-the-serial-console-to-enable-remote-management-over-http)

Depois de ativar a gestão remota, utilize o seguinte procedimento para preparar o cliente para uma ligação remota.

- [Preparar o cliente para a ligação remoto](#prepare-the-client-for-remote-connection)

### <a name="use-the-azure-classic-portal-to-enable-remote-management-over-http"></a>Utilizar o portal do clássico Azure para ativar a gestão remota através de HTTP 

Execute os seguintes passos no portal do Azure clássico para ativar a gestão remota através de HTTP.

#### <a name="to-enable-remote-management-through-the-azure-classic-portal"></a>Para ativar a gestão remota através do portal clássico do Azure

1. Aceder a **dispositivos** > **Configurar** para o seu dispositivo.

2. Desloque-se até à secção **Gestão remota** .

3. Defina o **Activar a gestão remota** para **Sim**.

4. Agora pode optar por ligar através de HTTP. (A predefinição é ligar ao longo do HTTPS). Certifique-se de que HTTP está selecionada.

    >[AZURE.NOTE] Ligar-se através de HTTP é aceitável apenas em redes fidedignas.

6. Clique em **Guardar** na parte inferior da página.

### <a name="use-the-serial-console-to-enable-remote-management-over-http"></a>Utilizar a série consola para activar a gestão remota através de HTTP

Execute os passos seguintes na consola de série do dispositivo para ativar a gestão remota.

#### <a name="to-enable-remote-management-through-the-device-serial-console"></a>Para ativar a gestão remota através da consola de série do dispositivo

1. No menu de consola série, selecione a opção 1. Para obter mais informações sobre a utilização da consola de revisão em série no dispositivo, aceda a [ligar ao Windows PowerShell para StorSimple através da consola de série do dispositivo](storsimple-windows-powershell-administration.md#connect-to-windows-powershell-for-storsimple-via-device-serial-console).

2. Na linha de comandos, escreva:`Enable-HcsRemoteManagement –AllowHttp`

3. Será notificado sobre Vulnerabilidade de segurança de através de HTTP para ligar para o dispositivo. Quando lhe for pedido, confirme ao escrever **Y**.

4. Certifique-se de HTTP está ativada ao escrever:`Get-HcsSystem`

5. Certifique-se de que o campo de **RemoteManagementMode** mostra **HttpsAndHttpEnabled**. A ilustração seguinte mostra estas definições no betumes.

     ![Serial HTTPS e HTTP activado](./media/storsimple-remote-connect/HCS_SerialHttpsAndHttpEnabled.png)

### <a name="prepare-the-client-for-remote-connection"></a>Preparar o cliente para a ligação remoto

Execute os seguintes passos no cliente para activar a gestão remota.

#### <a name="to-prepare-the-client-for-remote-connection"></a>Para preparar o cliente para ligação remota

1. Inicie uma sessão do Windows PowerShell como administrador.

2. Escreva o seguinte comando para adicionar o endereço IP do dispositivo StorSimple à lista de anfitriões fidedignos do cliente: 

     `Set-Item wsman:\localhost\Client\TrustedHosts <device_ip> -Concatenate -Force`

     Substituir <*device_ip*> com o endereço IP do seu dispositivo; Por exemplo: 

     `Set-Item wsman:\localhost\Client\TrustedHosts 10.126.173.90 -Concatenate -Force`

3. Escreva o seguinte comando para guardar as credenciais de dispositivo numa variável: 

     *$cred = get-credenciais*

4. Na caixa de diálogo que é apresentado:

    1. Escreva o nome de utilizador no seguinte formato: *device_ip\SSAdmin*.
    2. Escreva a palavra-passe de administrador dispositivo que foi definida quando o dispositivo foi configurado com o Assistente de configuração. A palavra-passe predefinida é a *Palavra-passe1*.

7. Inicie uma sessão do Windows PowerShell no dispositivo escrevendo este comando:

     `Enter-PSSession -Credential $cred -ConfigurationName SSAdminConsole -ComputerName <device_ip>`

     >[AZURE.NOTE] Para criar uma sessão do Windows PowerShell para utilização com o dispositivo virtual StorSimple, acrescentar a `–Port` parâmetro e especifique a porta pública que configurou no remoto para aparelho Virtual StorSimple.

     Neste momento, o que deve ter uma sessão remota do Windows PowerShell ativa para o dispositivo.

    ![Comunicação remota do PowerShell através de HTTP](./media/storsimple-remote-connect/HCS_PSRemotingUsingHTTP.png)

## <a name="connect-through-https"></a>Ligue-se através de HTTPS

Ligar ao Windows PowerShell para StorSimple através de uma sessão de HTTPS é o método mais seguro e recomendado da ligação remotamente ao seu dispositivo do Microsoft Azure StorSimple. Os procedimentos seguintes explicam como configurar os computadores cliente e consola serial para que possa utilizar HTTPS para ligar ao Windows PowerShell para StorSimple.

Pode utilizar o portal clássico Azure ou a série consola para configurar a gestão remota. Selecione os seguintes procedimentos:

- [Utilizar o portal do clássico Azure para activar a gestão remota HTTPS](#use-the-azure-classic-portal-to-enable-remote-management-over-https)

- [Utilizar a série consola para activar a gestão remota HTTPS](#use-the-serial-console-to-enable-remote-management-over-https)

Depois de ativar a gestão remota, utilize os seguintes procedimentos para preparar o anfitrião do para uma gestão remota e ligar ao dispositivo anfitrião do remoto.

- [Preparar o anfitrião do para a gestão remota](#prepare-the-host-for-remote-management)

- [Ligar para o dispositivo de anfitrião do remoto](#connect-to-the-device-from-the-remote-host)

### <a name="use-the-azure-classic-portal-to-enable-remote-management-over-https"></a>Utilizar o portal do clássico Azure para activar a gestão remota HTTPS

Execute os seguintes passos no portal do Azure clássico para ativar a gestão remota por HTTPS.

#### <a name="to-enable-remote-management-over-https-from-the-azure-classic-portal"></a>Para ativar a gestão remota por HTTPS a partir do Azure portal clássico

1. Aceder a **dispositivos** > **Configurar** para o seu dispositivo.

2. Desloque-se até à secção **Gestão remota** .

3. Defina o **Activar a gestão remota** para **Sim**.

4. Agora pode optar por ligar através do HTTPS. (A predefinição é ligar ao longo do HTTPS). Certifique-se de que HTTPS está selecionada. 

5. Clique em **Transferir o certificado de gestão remota**. Especifique uma localização para guardar este ficheiro. Terá de instalar este certificado no computador cliente ou anfitrião que irá utilizar para ligar para o dispositivo.

6. Clique em **Guardar** na parte inferior da página.

### <a name="use-the-serial-console-to-enable-remote-management-over-https"></a>Utilizar a série consola para activar a gestão remota HTTPS

Execute os passos seguintes na consola de série do dispositivo para ativar a gestão remota.

#### <a name="to-enable-remote-management-through-the-device-serial-console"></a>Para ativar a gestão remota através da consola de série do dispositivo

1. No menu de consola série, selecione a opção 1. Para obter mais informações sobre a utilização da consola de revisão em série no dispositivo, aceda a [ligar ao Windows PowerShell para StorSimple através da consola de série do dispositivo](storsimple-windows-powershell-administration.md#connect-to-windows-powershell-for-storsimple-via-device-serial-console).

2. Na linha de comandos, escreva: 

     `Enable-HcsRemoteManagement`

    Isto deve ativar HTTPS no seu dispositivo.

3. Certifique-se de que HTTPS ter sido ativada ao escrever: 

     `Get-HcsSystem`

    Certifique-se de que o campo de **RemoteManagementMode** mostra **HttpsEnabled**. A ilustração seguinte mostra estas definições no betumes.

     ![HTTPS Serial activado](./media/storsimple-remote-connect/HCS_SerialHttpsEnabled.png)

4. A partir do resultado de `Get-HcsSystem`, copie o número de série do dispositivo e guardá-lo para utilizar posteriormente.

    >[AZURE.NOTE] O número de série de mapas para o nome CN no certificado.

5. Obter um certificado de gestão remota ao escrever: 
 
     `Get-HcsRemoteManagementCert`

    Um certificado semelhante ao seguinte irá aparecer.

    ![Obter o certificado de gestão remota](./media/storsimple-remote-connect/HCS_GetRemoteManagementCertificate.png)

5. Copie as informações no certificado da **-início certificado-** para **-Terminar certificado-** num editor de texto, como bloco de notas e guardá-lo como um ficheiro. cer. (Irá copiar este ficheiro para o anfitrião remoto quando se preparar o anfitrião.)

    >[AZURE.NOTE] Para gerar um certificado novo, utilize o `Set-HcsRemoteManagementCert` cmdlet.

### <a name="prepare-the-host-for-remote-management"></a>Preparar o anfitrião do para a gestão remota

Para preparar o computador anfitrião para uma ligação remota que utiliza uma sessão de HTTPS, execute os seguintes procedimentos:

- [Importar o ficheiro. cer para o arquivo de raiz do cliente ou anfitrião remoto](#to-import-the-certificate-on-the-remote-host).

- [Adicionar números de série de dispositivo para o ficheiro de anfitriões no seu anfitrião remoto](#to-add-device-serial-numbers-to-the-remote-host).

Cada um dos seguintes procedimentos é descrita abaixo.

#### <a name="to-import-the-certificate-on-the-remote-host"></a>Para importar o certificado no anfitrião do remoto

1. O ficheiro. cer com o botão direito e selecione **Instalar certificado**. Isto irá iniciar o Assistente de importação de certificado.

    ![Assistente de importação de certificado 1](./media/storsimple-remote-connect/HCS_CertificateImportWizard1.png)

2. Para a **localização do arquivo**, selecione o **Computador Local**e, em seguida, clique em **seguinte**.

3. Selecione **colocar todos os certificados na loja seguinte**e, em seguida, clique em **Procurar**. Navegue para o arquivo de raiz do seu anfitrião remoto e, em seguida, clique em **seguinte**.

    ![Assistente de importação de certificado 2](./media/storsimple-remote-connect/HCS_CertificateImportWizard2.png)

4. Clique em **Concluir**. Aparece uma mensagem que indica que a importação foi efetuada com êxito.

    ![Assistente de importação de certificado 3](./media/storsimple-remote-connect/HCS_CertificateImportWizard3.png)

#### <a name="to-add-device-serial-numbers-to-the-remote-host"></a>Para adicionar números de série do dispositivo ao anfitrião do remoto

1. Iniciar o bloco de notas como um administrador e, em seguida, abra o ficheiro de anfitriões localizado em \Windows\System32\Drivers\etc.

2. Adicione as seguintes entradas de três para o seu ficheiro de anfitriões: **endereço IP de dados 0**, o **endereço IP fixo controlador de 0**e o **endereço IP fixo do controlador de 1**.

3. Introduza o número de série do dispositivo que guardou anteriormente. Mapear este para o endereço IP conforme apresentado na seguinte imagem. Controlador de 0 e 1 controlador, acrescente **Controller0** e **controlador_1** no final do número de série (nome NC).

    ![Adicionar o nome de CN ao ficheiro hosts](./media/storsimple-remote-connect/HCS_AddingCNNameToHostsFile.png)

4. Guarde o ficheiro de anfitriões.

### <a name="connect-to-the-device-from-the-remote-host"></a>Ligar para o dispositivo de anfitrião do remoto

Utilizar o Windows PowerShell e SSL para introduzir uma sessão de ' s no seu dispositivo a partir de um cliente ou anfitrião remoto. A sessão de ' s liga-se a opção 1 no menu [acções em série consola](storsimple-windows-powershell-administration.md#connect-to-windows-powershell-for-storsimple-via-device-serial-console) do seu dispositivo.

Efetue o seguinte procedimento no computador a partir do qual pretende efectuar a ligação do Windows PowerShell remota.

#### <a name="to-enter-an-ssadmin-session-on-the-device-by-using-windows-powershell-and-ssl"></a>Para introduzir uma sessão de ' s no dispositivo com Windows PowerShell e SSL

1. Inicie uma sessão do Windows PowerShell como administrador.

2. Adicione o endereço IP do dispositivo para anfitriões fidedignos do cliente escrevendo:

     `Set-Item wsman:\localhost\Client\TrustedHosts <device_ip> -Concatenate -Force`

    Onde <*device_ip*> é o endereço IP do seu dispositivo; Por exemplo: 

     `Set-Item wsman:\localhost\Client\TrustedHosts 10.126.173.90 -Concatenate -Force`

3. Crie uma nova credencial ao escrever: 

     `$cred = New-Object pscredential @("<IP of target device>\SSAdmin", (ConvertTo-SecureString -Force -AsPlainText "<Device Administrator Password>"))`

    Onde <*IP do dispositivo de destino*> é o endereço IP de dados 0 para o seu dispositivo; Por exemplo, **10.126.173.90** conforme mostrado na imagem anterior do ficheiro anfitriões. Além disso, fornece a palavra-passe de administrador para o seu dispositivo.

4. Crie uma sessão ao escrever:

     `$session = New-PSSession -UseSSL -ComputerName <Serial number of target device> -Credential $cred -ConfigurationName "SSAdminConsole"`

    Para o parâmetro - nome do computador no cmdlet, fornece o <*número de série de dispositivo de destino*>. Este número de série foi mapeado para o endereço IP do dados 0 no ficheiro de anfitriões no seu anfitrião remoto; Por exemplo, **SHX0991003G44MT** conforme apresentado na seguinte imagem.

5. Tipo: 

     `Enter-PSSession $session`

6. Terá de esperar alguns minutos e, em seguida, será ligado ao seu dispositivo através de HTTPS através de SSL. Verá uma mensagem que indica que está ligado ao seu dispositivo.

    ![Comunicação remota do PowerShell com o HTTPS e SSL](./media/storsimple-remote-connect/HCS_PSRemotingUsingHTTPSAndSSL.png)

## <a name="next-steps"></a>Próximos passos

- Saiba mais sobre como [utilizar o Windows PowerShell para administrar o seu dispositivo StorSimple](storsimple-windows-powershell-administration.md).

- Saiba mais sobre como [utilizar o serviço do Gestor de StorSimple para administrar o seu dispositivo StorSimple](storsimple-manager-service-administration.md).
