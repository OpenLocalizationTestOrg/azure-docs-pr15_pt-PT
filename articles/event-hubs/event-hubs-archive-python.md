<properties
    pageTitle="Tutorial do Azure evento concentradores arquivo | Microsoft Azure"
    description="Exemplo que utiliza o SDK do Azure Python para demonstrar a utilizar a funcionalidade de evento concentradores arquivo."
    services="event-hubs"
    documentationCenter=""
    authors="djrosanova"
    manager="timlt"
    editor=""/>

<tags
    ms.service="event-hubs"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/13/2016"
    ms.author="darosa;sethm"/>

# <a name="event-hubs-archive-walkthrough-python"></a>Evento concentradores arquivo instruções passo a passo: Python

Arquivo de concentradores evento é uma nova funcionalidade de concentradores de evento que permite-lhe entregar automaticamente os dados em sequência no seu centro de evento para uma conta de armazenamento de Blobs do Azure da sua escolha. Isto torna mais fácil executar o lote de processamento de transmissão de dados em tempo real. Este artigo descreve como utilizar o arquivo de concentradores evento com Python. Para mais informações sobre o evento concentradores arquivo, consulte o [artigo Descrição geral](event-hubs-archive-overview.md).

Este exemplo utiliza o SDK do Azure Python para demonstrar a utilizar a funcionalidade de arquivo. O sender.py envia simulada telemetria ambiental para evento concentradores no formato JSON. Centro do evento está configurado para utilizar a funcionalidade de arquivo para escrever estes dados ao armazenamento em lotes de blob. O archivereader.py, em seguida, lê estes blobs e cria um ficheiro de acréscimo por dispositivo e escreve os dados em ficheiros. csv.

O que vai ser feito

1.  Criar uma conta de armazenamento de Blobs do Azure e um contentor BLOBs dentro da mesma, utilizando o portal Azure

2.  Criar um espaço de nomes do concentrador de evento, utilizando o portal Azure

3.  Criar um concentrador de evento com a funcionalidade de arquivo ativada, utilizando o portal Azure

4.  Enviar dados para o concentrador de evento com um script Python

5.  Ler os ficheiros a partir do arquivo e processá-las com outro script Python

Pré-requisitos

1.  Python 2.7.x

2.  Uma subscrição do Azure

[AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

## <a name="create-an-azure-storage-account"></a>Criar uma conta de armazenamento do Windows Azure

1.  Inicie sessão no [portal do Azure][].

2.  No painel de navegação do lado esquerdo do portal, clique em novo, em seguida, clique em dados + armazenamento e, em seguida, clique em conta de armazenamento.

3.  Preencha os campos a pá de conta de armazenamento e clique em **Criar**.

    ![][1]

4.  Quando vir a mensagem **Foi concluída com êxito híbridas** , clique em sobre a nova conta de armazenamento e na pá **Essentials** clique em **Blobs**. Quando abre o **serviço de BLOBs** pá, clique em **+ Container** na parte superior. O contentor **arquivo**atribuir um nome e, em seguida, feche o pá **Blob serviço** .

5.  Clique em **teclas de acesso** na pá para a esquerda e copie o nome da conta de armazenamento e o valor de **chave1**. Guarde estes valores no bloco de notas ou noutro local temporário.

[AZURE.INCLUDE [event-hubs-create-event-hub](../../includes/event-hubs-create-event-hub.md)]

## <a name="create-a-python-script-to-send-events-to-your-event-hub"></a>Criar um script Python para enviar eventos para o seu centro de evento

1.  Abra o editor de Python favorito, tal como [Código do Visual Studio][].

2.  Crie um script denominado **sender.py**. Este script enviará 200 eventos ao seu centro de evento. São as medições ambientais simples enviadas JSON.

3.  Cole o código seguinte sender.py:

    ```
    import uuid
    import datetime
    import random
    import json
    from azure.servicebus import ServiceBusService
    
    sbs = ServiceBusService(service_namespace='INSERT YOUR NAMESPACE NAME', shared_access_key_name='RootManageSharedAccessKey', shared_access_key_value='INSERT YOUR KEY')
    devices = []
    for x in range(0, 10):
        devices.append(str(uuid.uuid4()))
    
    for y in range(0,20):
        for dev in devices:
            reading = {'id': dev, 'timestamp': str(datetime.datetime.utcnow()), 'uv': random.random(), 'temperature': random.randint(70, 100), 'humidity': random.randint(70, 100)}
            s = json.dumps(reading)
            sbs.send\_event('myhub', s)
        print y
    ```
4.  Atualize o código para utilizar o seu nome de espaço de nomes e os valores de chave que obteve quando criou o espaço de nomes do evento concentradores precedente.

## <a name="create-a-python-script-to-read-your-archive-files"></a>Criar um script Python para ler os seus ficheiros de arquivo

1.  Preencha a pá e clique em **Criar**.

2.  Crie um script denominado **archivereader.py**. Este script vai ler os ficheiros de arquivo e cria um ficheiro por dispositivo para escrever os dados apenas para que o dispositivo.

3.  Cole o código seguinte archivereader.py:

    ```
    import os
    import string
    import json
    import avro.schema
    from avro.datafile import DataFileReader, DataFileWriter
    from avro.io import DatumReader, DatumWriter
    from azure.storage.blob import BlockBlobService
    
    def processBlob(filename):
        reader = DataFileReader(open(filename, 'rb'), DatumReader())
        dict = {}
        for reading in reader:
            parsed\_json = json.loads(reading["Body"])
            if not 'id' in parsed\_json:
                return
            if not dict.has\_key(parsed\_json['id']):
            list = []
            dict[parsed\_json['id']] = list
        else:
            list = dict[parsed\_json['id']]
            list.append(parsed\_json)
        reader.close()
        for device in dict.keys():
            deviceFile = open(device + '.csv', "a")
            for r in dict[device]:
                deviceFile.write(", ".join([str(r[x]) for x in r.keys()])+'\\n')

    def startProcessing(accountName, key, container):
        print 'Processor started using path: ' + os.getcwd()
        block\_blob\_service = BlockBlobService(account\_name=accountName, account\_key=key)
        generator = block\_blob\_service.list\_blobs(container)
        for blob in generator:
            if blob.properties.content\_length != 0:
                print('Downloaded a non empty blob: ' + blob.name)
                cleanName = string.replace(blob.name, '/', '\_')
                block\_blob\_service.get\_blob\_to\_path(container, blob.name, cleanName)
                processBlob(cleanName)
                os.remove(cleanName)
            block\_blob\_service.delete\_blob(container, blob.name)
    startProcessing('YOUR STORAGE ACCOUNT NAME', 'YOUR KEY', 'archive')
    ```

4.  Certifique-se colar os valores adequados para o seu nome de conta de armazenamento e chave na chamada para `startProcessing`.

## <a name="run-the-scripts"></a>Executar os scripts

1.  Abra uma linha de comandos que tenha Python no seu caminho e, em seguida, execute estes comandos para instalar pacotes pré-requisito Python:

    ```
    pip install azure-storage
    pip install azure-servicebus
    pip install avro
    ```
  
    Se tiver uma versão anterior de um dos armazenamento azure ou azure poderá ter de utilizar o **– Atualizar** opção

    Também poderá ter de execute o seguinte (não é necessária na maioria dos sistemas):

    ```
    pip install cryptography
    ```

2.  Alterar o seu diretório para onde quer que guardou sender.py e archivereader.py e executa este comando:

    ```
    start python sender.py
    ```
    
    Esta ação inicia um novo processo Python para executar o remetente.

3. Agora, aguarde alguns minutos para o arquivo executar. Em seguida, escreva o seguinte comando na janela do comando original:

    ```
    python archivereader.py
    ```

Este processador de arquivo utiliza o directório local para transferir todos os de blobs do armazenamento conta/contentor. Irá processar qualquer que não estão vazias e escrever os resultados como ficheiros. csv com o directory local.

## <a name="next-steps"></a>Próximos passos

Pode obter mais informações sobre o evento concentradores visitando as ligações seguintes:

- [Descrição geral de evento concentradores arquivar][]
- Uma [aplicação de exemplo que utiliza o evento concentradores][]concluída.
- Exemplo de [escala de saída evento processamento com concentradores do evento][] .
- [Descrição geral de concentradores do evento][]
 

[Portal do Azure]: https://portal.azure.com/
[Descrição geral de evento concentradores arquivar]: event-hubs-archive-overview.md
[1]: ./media/event-hubs-archive-python/event-hubs-python1.png
[About Azure storage accounts]: https://azure.microsoft.com/en-us/documentation/articles/storage-create-storage-account/
[Código do Visual Studio]: https://code.visualstudio.com/
[Descrição geral de concentradores do evento]: event-hubs-overview.md
[aplicação de exemplo que utiliza concentradores de evento]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-286fd097
[Dimensionar saída evento processamento com concentradores de evento]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3
