<properties 
    pageTitle="Como é redirecionar dispositivos USB no Azure RemoteApp? | Microsoft Azure" 
    description="Saiba como utilizar o redirecionamento para dispositivos USB no Azure RemoteApp." 
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



# <a name="how-do-you-redirect-usb-devices-in-azure-remoteapp"></a>Como é redirecionar dispositivos USB no Azure RemoteApp?

> [AZURE.IMPORTANT]
> Está a ser descontinuada Azure RemoteApp. Leia o [anúncio](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.

Redirecionamento do dispositivo permite aos utilizadores utilizar dispositivos de USB ligados ao seu computador ou tablet com aplicações na RemoteApp do Azure. Por exemplo, se partilhado Skype através de Azure RemoteApp, os utilizadores terão possam utilizar os respetivos câmaras de dispositivo.

Antes de continuar, certifique-se de que leia as informações de redirecionamento de USB na [utilizando redirecionamento no Azure RemoteApp](remoteapp-redirection.md). No entanto a recomendado nusbdevicestoredirect:s: * não funcionam para câmaras web USB e poderão não funcionar para algumas impressoras USB ou dispositivos multifuncionais USB. Por predefinição e por motivos de segurança, o administrador RemoteApp do Azure tem de ativar redirecionamento ao GUID de classe do dispositivo ou por ID da instância dispositivo antes dos utilizadores podem utilizar esses dispositivos.

Apesar deste artigo fala sobre redirecionamento de câmara web, pode utilizar uma abordagem semelhante para redirecionar impressoras USB e outros dispositivos multifuncionais USB que não são redireccionados pelo **nusbdevicestoredirect:s:*** comando.

## <a name="redirection-options-for-usb-devices"></a>Opções de redirecionamento para dispositivos USB
Azure RemoteApp utiliza mecanismos muito semelhantes para redirecionar dispositivos USB como aqueles disponíveis para serviços de ambiente de trabalho remoto. A tecnologia subjacente permite-lhe escolher o método de redirecionamento correta para um determinado dispositivo, para obter o melhor da ambos alto nível e um dispositivo RemoteFX USB redirecionamento utilizando o **usbdevicestoredirect:s:** comando. Existem quatro elementos a este comando:

| Ordem de processamento | Parâmetro           | Descrição                                                                                                                |
|------------------|---------------------|----------------------------------------------------------------------------------------------------------------------------|
| 1                | *                   | Seleciona todos os dispositivos que não estão recolhidos pelo redirecionamento de alto nível. Nota: por predefinição, * não funciona para câmaras web USB.  |
|                  | {Dispositivo GUID de classe} | Seleciona todos os dispositivos que correspondem a classe de configuração do dispositivo especificado.                                                           |
|                  | USB\InstanceID      | Seleciona um dispositivo USB especificado para o ID da instância determinado.                                                                  |
| 2                | -USB\Instance ID    | Remove as definições de redirecionamento para o dispositivo especificado.                                                                 |

## <a name="redirecting-a-usb-device-by-using-the-device-class-guid"></a>Redirecionar um dispositivo USB utilizando a classe de dispositivo GUID
Existem duas formas de localizar a classe de dispositivo GUID que pode ser utilizado para redirecionamento. 

A primeira opção é utilizar a [Definidas pelo sistema dispositivo configuração Classes disponíveis para fornecedores de etiquetas](https://msdn.microsoft.com/library/windows/hardware/ff553426.aspx). Escolha a classe que corresponda melhor o dispositivo ligado ao computador local. Para câmaras digitais tal poderá ser uma classe de dispositivo de imagens ou a classe de dispositivo de capturar vídeo. Terá de fazer algumas pioneira com as classes dispositivo para localizar a classe correta GUID que funciona com o dispositivo USB ligado localmente (no nosso caso a câmara web).

Uma melhor forma ou a segunda opção, é siga estes passos para localizar a classe de dispositivo específico GUID:

1. Abrir o Gestor de dispositivos, localize o dispositivo que será redirecionado e faça duplo clique nele e, em seguida, abra as propriedades.
![Abrir o Gestor de dispositivos](./media/remoteapp-usbredir/ra-devicemanager.png)
2. No separador **Detalhes** , selecione a propriedade **Guid de classe**. O valor que é apresentada é o GUID de classe para esse tipo de dispositivo.
![Propriedades da câmara](./media/remoteapp-usbredir/ra-classguid.png)
3. Utilize o valor de Guid de classe para redirecionar dispositivos que correspondam ao-lo.

Por exemplo:

        Set-AzureRemoteAppCollection -CollectionName <collection name> -CustomRdpProperty "nusbdevicestoredirect:s:<Class Guid value>"

Pode combinar várias redireccionamento dispositivo no cmdlet do mesmo. Por exemplo: para redirecionar armazenamento local e uma câmara web USB, cmdlet tem este aspeto:

        Set-AzureRemoteAppCollection -CollectionName <collection name> -CustomRdpProperty "drivestoredirect:s:*`nusbdevicestoredirect:s:<Class Guid value>"

Quando configurar o redirecionamento de dispositivo por GUID de classe são redirecionados todos os dispositivos que correspondem a esse GUID de classe na coleção de especificado. Por exemplo, se existirem vários computadores na rede local que tenham as mesmo câmaras de web USB, pode executar um único cmdlet para redirecionar todas as câmaras web.

## <a name="redirecting-a-usb-device-by-using-the-device-instance-id"></a>Redirecionar um dispositivo USB utilizando o ID da instância dispositivo

Se quer mais controlo extensivamente e pretende controlar o redirecionamento por dispositivo, pode utilizar o parâmetro de redirecionamento **USB\InstanceID** .

A peça as deste método está a localizar o ID de instância de dispositivo USB. Terá acesso ao computador e no dispositivo USB específico. Em seguida, siga estes passos:

1. Ativar o redirecionamento de dispositivo na sessão remota do ambiente de trabalho, conforme descrito no [como posso utilizar meus dispositivos e recursos numa sessão de ambiente de trabalho remoto?](http://windows.microsoft.com/en-us/windows7/How-can-I-use-my-devices-and-resources-in-a-Remote-Desktop-session)
2. Abra uma ligação de ambiente de trabalho remoto e clique em **Mostrar opções**.
3. Clique em **Guardar como** para guardar as definições de ligação atual para um ficheiro de RDP.  
    ![Guardar as definições como um ficheiro RDP](./media/remoteapp-usbredir/ra-saveasrdp.png)
4. Selecione um nome de ficheiro e uma localização, por exemplo, "MyConnection.rdp" e "Este PC\Documents" e guardar o ficheiro.
5. Abra o ficheiro de MyConnection.rdp utilizando um editor de texto e localize o ID da instância do dispositivo que pretende redirecionar.

Agora, utilize o ID da instância no cmdlet seguinte:

    Set-AzureRemoteAppCollection -CollectionName <collection name> -CustomRdpProperty "nusbdevicestoredirect:s: USB\<Device InstanceID value>"



### <a name="help-us-help-you"></a>Ajude-nos ajudá-lo 
Sabia que, para além de classificação deste artigo e fazer comentários para baixo abaixo, pode efetuar alterações ao artigo propriamente dito? Algo em falta? Algo errado? Escrever algo que é apenas confuso? Deslocar para cima e clique em **Editar no GitHub** para efetuar alterações - aqueles seja reencaminhado-para revisão e, em seguida, assim que iniciar a sessão nos mesmos, verá as alterações e melhorias para a direita aqui.