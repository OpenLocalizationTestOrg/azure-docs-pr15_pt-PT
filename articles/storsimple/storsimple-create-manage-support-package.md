<properties
   pageTitle="Criar um pacote de suporte StorSimple | Microsoft Azure"
   description="Saiba como criar, desencriptar e editar um pacote de suporte para o seu dispositivo StorSimple."
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


# <a name="create-and-manage-a-storsimple-support-package"></a>Criar e gerir um pacote de suporte StorSimple

## <a name="overview"></a>Descrição geral

Um pacote de suporte StorSimple é um dispositivo de fácil utilização recolhe todos os registos relevantes para o ajudar a Microsoft Support com a resolução de problemas do dispositivo de StorSimple. Os registos recolhidos são encriptados e comprimidos.

Neste tutorial inclui instruções passo a passo para criar e gerir o pacote de suporte.

## <a name="create-and-upload-a-support-package-in-the-azure-classic-portal"></a>Criar e carregar um pacote de suporte no portal do Azure clássico

Pode criar e carregar um pacote de suporte para o site Microsoft Support através da página de **Manutenção** do serviço no portal do Azure clássico.

> [AZURE.NOTE] O carregamento requer uma chave de suporte. A suporte de engenharia deve fornecer isto para si numa mensagem de e-mail.

Um pacote de suporte encriptada e comprimido (ficheiro. cab) é criado e carregado para o site de suporte. O suporte técnico, em seguida, pode obter este pacote a partir do site de suporte para o problema de resolução de problemas.

Execute os seguintes passos no portal do clássico para criar um pacote de suporte.

#### <a name="to-create-a-support-package-in-the-azure-classic-portal"></a>Para criar um pacote de suporte no portal do Azure clássico

1. Selecione **dispositivos** > **Manutenção**.

2. Na secção de **pacote de suporte** , selecione **criar e carregar o pacote de suporte**.

3. Na caixa de diálogo **criar e carregar o pacote de suporte** , faça o seguinte:

    ![Criar pacote de suporte](./media/storsimple-create-manage-support-package/IC740923.png)

    - Na caixa de texto **Chave de suporte** , introduza a chave. A engenharia de suporte da Microsoft deve enviam esta chave no e-mail.

    - Selecione a caixa de verificação para fornecer o seu consentimento para carregar automaticamente o pacote de suporte para o site Microsoft Support.

    - Clique no ícone de verificação ![Ícone de verificação](./media/storsimple-create-manage-support-package/IC740895.png).


## <a name="manually-create-a-support-package"></a>Criar manualmente um pacote de suporte

Em alguns casos, terá de criar manualmente o pacote de suporte através do Windows PowerShell para StorSimple. Por exemplo:

- Se precisar de remover informações confidenciais dos seus ficheiros de registo antes de partilhar com Microsoft Support.

- Se estiver a ter dificuldades em carregar o pacote devido a problemas de conectividade.

Pode partilhar o seu pacote de suporte manualmente gerado com o Microsoft Support através de e-mail. Execute os passos seguintes para criar um pacote de suporte no Windows PowerShell para StorSimple.

#### <a name="to-create-a-support-package-in-windows-powershell-for-storsimple"></a>Para criar um pacote de suporte no Windows PowerShell para StorSimple

1. Para iniciar uma sessão do Windows PowerShell como administrador no computador remoto que é utilizado para ligar para o seu dispositivo StorSimple, introduza o seguinte comando:

    `Start PowerShell`

2. Na sessão do Windows PowerShell, ligar à consola ' s do seu dispositivo:

    - Na linha de comandos, introduza:

        `$MS = New-PSSession -ComputerName <IP address for DATA 0> -Credential SSAdmin -ConfigurationName "SSAdminConsole"`

    1. Na caixa de diálogo que é apresentada, introduza a palavra-passe de administrador do dispositivo. A palavra-passe predefinida é:

        `Password1`

        ![Caixa de diálogo de credenciais do PowerShell](./media/storsimple-create-manage-support-package/IC740962.png)

    2. Clique **em OK**.
    1. Na linha de comandos, introduza:

        `Enter-PSSession $MS`

3. Na sessão que se abre, introduza o comando adequado.

    - Para partilhas de rede que são protegido palavra-passe, introduza:

        `Export-HcsSupportPackage –PackageTag "MySupportPackage" –Credential "Username" -Force`

        Vai ser-lhe uma palavra-passe, um caminho para a pasta de rede partilhada e uma frase de encriptação (porque o pacote de suporte está encriptado). Um pacote de suporte, em seguida, é criado na pasta especificada.

    - Para partilhas de que não estão protegido palavra-passe, não necessita de `-Credential` parâmetro. Introduza o seguinte:

        `Export-HcsSupportPackage –PackageTag "MySupportPackage" -Force`

        O pacote de suporte é criado para ambos os controladores na pasta partilhada rede especificado. É um ficheiro de encriptadas, comprimido pode ser enviado para a Microsoft Support para resolução de problemas. Para mais informações, consulte o artigo [Contactar o suporte do Microsoft](storsimple-contact-microsoft-support.md).


### <a name="the-export-hcssupportpackage-cmdlet-parameters"></a>Os parâmetros de cmdlet HcsSupportPackage de exportação
Pode utilizar os seguintes parâmetros com o cmdlet HcsSupportPackage de exportação.

| Parâmetro            | Obrigatório/opcional | Descrição                                                                                                                                                             |
|----------------------|-------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| `-Path`                 | Obrigatório          | Utilize para fornecer a localização da pasta partilhada rede em que é colocado o pacote de suporte.                                                                 |
| `-EncryptionPassphrase` | Obrigatório          | Utilize para fornecer uma frase de acesso para o ajudar a encriptar o pacote de suporte.                                                                                                        |
| `-Credential`           | Opcional          | Utilize para fornecer credenciais de acesso para a pasta de rede partilhada.                                                                                        |
| `-Force`                | Opcional          | Utilize para ignorar o passo de confirmação da frase de acesso de encriptação.                                                                                                                |
| `-PackageTag`           | Opcional          | Utilize para especificar um diretório em *caminho* no qual está colocado o pacote de suporte. A predefinição é [nome do dispositivo]-[data atual e time:yyyy-MM-dd-HH-mm-ss].       |
| `-Scope`                | Opcional          | Especifique como **Cluster** (predefinição) para criar um pacote de suporte para ambos os controladores. Se pretender criar um pacote apenas para o controlador de actual, especifique o **controlador de**. |


## <a name="edit-a-support-package"></a>Editar um pacote de suporte

Depois de ter criado um pacote de suporte, poderá ter de editar o pacote para remover informações confidenciais. Isto pode incluir volume nomes, endereços IP do dispositivo e nomes de cópia de segurança dos ficheiros de registo.

> [AZURE.IMPORTANT] Apenas pode editar um pacote de suporte que foi criado através do Windows PowerShell para StorSimple. Não pode editar um pacote que criou no portal do Azure clássico com o serviço do Gestor de StorSimple.

Para editar um pacote de apoio antes de carregá-lo no site Microsoft Support, desencriptar pela primeira vez o pacote de suporte, edite os ficheiros e, em seguida, voltar a encriptá-la. Execute os passos seguintes.

#### <a name="to-edit-a-support-package-in-windows-powershell-for-storsimple"></a>Para editar um pacote de suporte no Windows PowerShell para StorSimple

1. Gera um pacote de suporte conforme descrito anteriormente, [para criar um pacote de suporte no Windows PowerShell para StorSimple](#to-create-a-support-package-in-windows-powershell-for-storsimple).

2. [Transferir o script](http://gallery.technet.microsoft.com/scriptcenter/Script-to-decrypt-a-a8d1ed65) localmente no seu cliente.

3. Importe o módulo Windows PowerShell. Especifique o caminho para a pasta local em que transferiu o script. Para importar o módulo, introduza:

    `Import-module <Path to the folder that contains the Windows PowerShell script>`

4. Todos os ficheiros são ficheiros de *.aes* que são comprimidos e encriptados. Para descomprimir e desencriptar ficheiros, introduza:

    `Open-HcsSupportPackage <Path to the folder that contains support package files>`

    Tenha em atenção que as extensões de ficheiro real agora são apresentadas para todos os ficheiros.

    ![Editar o pacote de suporte](./media/storsimple-create-manage-support-package/IC750706.png)

5. Quando lhe for pedido para a frase de acesso de encriptação, introduza a frase de acesso que utilizou quando o pacote de suporte foi criado.

        cmdlet Open-HcsSupportPackage at command pipeline position 1

        Supply values for the following parameters:EncryptionPassphrase: ****

6. Navegue para a pasta que contém os ficheiros de registo. Porque os ficheiros de registo estão agora descomprimir e desencriptar, estes serão têm extensões de ficheiro original. Modificar estes ficheiros para remover quaisquer informações específicas do cliente, como nomes de volume e endereços IP do dispositivo e guarde os ficheiros.

7. Feche os ficheiros ao comprimi-las com gzip e encriptá-las com AES 256. Destina-se para a velocidade e segurança no transferir o pacote de suporte através de uma rede. Para comprimir e encriptar ficheiros, introduza o seguinte:

    `Close-HcsSupportPackage <Path to the folder that contains support package files>`

    ![Editar o pacote de suporte](./media/storsimple-create-manage-support-package/IC750707.png)

8. Quando lhe for pedido, forneça uma frase de encriptação para o pacote de suporte modificados.

        cmdlet Close-HcsSupportPackage at command pipeline position 1
        Supply values for the following parameters:EncryptionPassphrase: ****

9. Escreva a nova frase de acesso, para que pode partilhá-lo com o Microsoft Support quando solicitado.


### <a name="example-editing-files-in-a-support-package-on-a-password-protected-share"></a>Exemplo: Editar ficheiros num pacote de suporte numa partilha de protegido por palavra-passe

O exemplo seguinte mostra como desencriptar, editar e voltar a encriptar um pacote de suporte.

        PS C:\WINDOWS\system32> Import-module C:\Users\Default\StorSimple\SupportPackage\HCSSupportPackageTools.psm1

        PS C:\WINDOWS\system32> Open-HcsSupportPackage \\hcsfs\Logs\TD48\TD48Logs\C0-A\etw

        cmdlet Open-HcsSupportPackage at command pipeline position 1

        Supply values for the following parameters:

        EncryptionPassphrase: ****

        PS C:\WINDOWS\system32> Close-HcsSupportPackage \\hcsfs\Logs\TD48\TD48Logs\C0-A\etw

        cmdlet Close-HcsSupportPackage at command pipeline position 1

        Supply values for the following parameters:

        EncryptionPassphrase: ****

        PS C:\WINDOWS\system32>

## <a name="next-steps"></a>Próximos passos

- Saiba como [utilizar pacotes de suporte e registos de dispositivo para resolver a sua implementação do dispositivo](storsimple-troubleshoot-deployment.md#support-packages-and-device-logs-available-for-troubleshooting).

- Saiba como [utilizar o serviço do Gestor de StorSimple para administrar o seu dispositivo StorSimple](storsimple-manager-service-administration.md).
