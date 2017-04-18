<properties
    pageTitle="Como utilizar a API (Python) - guia de funcionalidade de gestão de serviço"
    description="Saiba como através de programação efetuar tarefas de gestão comuns do serviço a partir de Python."
    services="cloud-services"
    documentationCenter="python"
    authors="lmazuel"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="cloud-services"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="python"
    ms.topic="article"
    ms.date="09/06/2016"
    ms.author="lmazuel"/>

# <a name="how-to-use-service-management-from-python"></a>Como utilizar o serviço de gestão de Python

> [AZURE.NOTE] API do serviço de gestão é a ser substituído por novo recurso gestão API, encontra-se disponível uma versão de pré-visualização.  Consulte a [documentação de gestão de recursos do Azure](http://azure-sdk-for-python.readthedocs.org/) para obter detalhes sobre como utilizar a nova API de gestão de recursos a partir do Python.

Este guia mostra-lhe como através de programação efetuar tarefas de gestão comuns do serviço a partir de Python. A classe de **ServiceManagementService** no [Azure SDK para Python](https://github.com/Azure/azure-sdk-for-python) suporta acesso de programação ao muita a funcionalidade relacionados com a gestão do serviço que está disponível no [portal clássica Azure] [ management-portal] (como **criar, atualizar e eliminar serviços em nuvem, híbridas, serviços de gestão de dados e máquinas virtuais**). Esta funcionalidade pode ser útil na criação de aplicações que precisam de acesso de programação para gestão de serviços.

## <a name="WhatIs"> </a>o que é o serviço de gestão
A API do serviço de gestão de fornece acesso de programação para a maior parte das funcionalidades de gestão do serviço disponível através do [portal clássica Azure][management-portal]. O SDK do Azure para Python permite-lhe gerir os seus serviços em nuvem e contas de armazenamento.

Para utilizar a API de gestão do serviço, necessita de [criar uma conta Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="Concepts"> </a>Conceitos
O SDK do Azure para Python é moldado [API de gestão do serviço de Azure][svc-mgmt-rest-api], que é um REST API. Todas as operações de API são executadas através de SSL e mutuamente autenticados utilizando x. 509 v3 certificados. Serviço de gestão de pode ser acedido a partir de dentro de um serviço em execução no Azure, ou diretamente através da Internet a partir de qualquer aplicação que pode enviar um pedido de HTTPS e recebe uma resposta HTTPS.

## <a name="Installation"> </a>Instalação

Todas as funcionalidades descritas neste artigo estão disponíveis na `azure-servicemanagement-legacy` compactar, que pode instalar utilizando pip. Para obter mais detalhes sobre a instalação (por exemplo, se estiver familiarizado com o Python), consulte este artigo: [instalar Python e o SDK do Azure](../python-how-to-install.md)

## <a name="Connect"> </a>Como: ligar a gestão de serviços
Para ligar para o ponto final de gestão de serviços, é necessário o ID da subscrição Azure e um certificado de gestão válida. Pode obter o seu ID da subscrição através do [portal clássica Azure][management-portal].

> [AZURE.NOTE] Desde Azure SDK para Python v0.8.0, agora é possível utilizar certificados criados com OpenSSL quando a ser executado no Windows.  Requer Python ponto 2.7.4 ou posterior. Recomendamos que os utilizadores utilizem OpenSSL em vez de. pfx, desde o suporte para. pfx certificados provavelmente serão removidos no futuro.

### <a name="management-certificates-on-windowsmaclinux-openssl"></a>Gestão certificados presentes no Windows/Mac/Linux (OpenSSL)
Pode utilizar [OpenSSL](http://www.openssl.org/) para criar o seu certificado de gestão.  Realmente necessárias para criar duas certificados, uma para o servidor (um `.cer` ficheiro) e outra para o cliente (um `.pem` ficheiro). Para criar o `.pem` de ficheiros, executar:

    openssl req -x509 -nodes -days 365 -newkey rsa:1024 -keyout mycert.pem -out mycert.pem

Para criar o `.cer` certificado e executar:

    openssl x509 -inform pem -in mycert.pem -outform der -out mycert.cer

Para mais informações sobre os certificados Azure, consulte o artigo [Descrição geral de certificados para serviços em nuvem Azure](./cloud-services-certs-create.md). Para obter uma descrição completa dos parâmetros OpenSSL, consulte a documentação sobre em [http://www.openssl.org/docs/apps/openssl.html](http://www.openssl.org/docs/apps/openssl.html).

Depois de ter criado estes ficheiros, precisar de carregar a `.cer` ficheiro para Azure através da ação "Carregamento" do separador "as definições" do [portal clássica Azure][management-portal], e necessita de tome nota do onde o guardou o `.pem` ficheiro.

Depois de ter obtido o seu ID da subscrição, criado um certificado e carregados o `.cer` ficheiro para Azure, pode ligar para o ponto final gestão Azure, passando o id da subscrição e o caminho para o `.pem` ficheiro para **ServiceManagementService**:

    from azure import *
    from azure.servicemanagement import *

    subscription_id = '<your_subscription_id>'
    certificate_path = '<path_to_.pem_certificate>'

    sms = ServiceManagementService(subscription_id, certificate_path)

No exemplo anterior, `sms` é um objeto de **ServiceManagementService** . A classe de **ServiceManagementService** é a classe primária utilizada para gerir serviços Azure.

### <a name="management-certificates-on-windows-makecert"></a>Certificados de gestão no Windows (MakeCert)

Pode criar um certificado autoassinado gestão no seu computador utilizar `makecert.exe`.  Abra um **Visual Studio linha de comandos** como **administrador** e utilize o seguinte comando, substituindo *AzureCertificate* com o nome do certificado que pretende utilizar.

    makecert -sky exchange -r -n "CN=AzureCertificate" -pe -a sha1 -len 2048 -ss My "AzureCertificate.cer"

O comando cria o `.cer` ficheiro e instala-lo no arquivo de certificados **pessoais** . Para obter mais detalhes, consulte o artigo [Descrição geral de certificados para serviços em nuvem Azure](./cloud-services-certs-create.md).

Depois de ter criado o certificado, precisar de carregar a `.cer` ficheiro para Azure através da ação "Carregamento" do separador "as definições" do [portal clássica Azure][management-portal].

Depois de ter obtido o seu ID da subscrição, criado um certificado e carregado o `.cer` ficheiro para Azure, pode ligar para o ponto final gestão Azure, passando o id da subscrição e a localização do certificado no arquivo de certificados **pessoais** para **ServiceManagementService** (novamente, substitua *AzureCertificate* com o nome do seu certificado):

    from azure import *
    from azure.servicemanagement import *

    subscription_id = '<your_subscription_id>'
    certificate_path = 'CURRENT_USER\\my\\AzureCertificate'

    sms = ServiceManagementService(subscription_id, certificate_path)

No exemplo anterior, `sms` é um objeto de **ServiceManagementService** . A classe de **ServiceManagementService** é a classe primária utilizada para gerir serviços Azure.

## <a name="ListAvailableLocations"> </a>Como: lista de localizações disponíveis

Para listar as localizações que estão disponíveis para serviços de alojamento, utilize o **lista\_localizações** método:

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.list_locations()
    for location in result:
        print(location.name)

Quando cria um serviço na nuvem ou serviço de armazenamento tem de fornecer uma localização válida. O **lista\_localizações** método devolve sempre uma lista actualizada das localizações disponíveis neste momento. Partir este escrita, localizações disponíveis são:

- Europa Ocidental
- Europa Norte
- Sudeste asiático
- Este asiático
- Central (EUA)
- América do Norte Central (EUA)
- Sul Central (EUA)
- Ocidental dos e.u.a.
- Leste dos EUA
- Japão leste
- Japão oeste
- Sul do Brasil
- Leste Austrália
- Sudeste Austrália

## <a name="CreateCloudService"> </a>Como: criar um serviço na nuvem

Quando criar uma aplicação e executá-la no Azure, o código e configuração em conjunto são denominados um Azure [serviço na nuvem][] (conhecido como um *alojado serviço* lançamentos Azure anteriores). O **criar\_alojado\_serviço** método permite-lhe criar um novo serviço alojado ao fornecer um nome de serviço alojado (que tem de ser exclusivo no Azure), uma etiqueta (automaticamente codificado em base64), uma descrição e uma localização.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    name = 'myhostedservice'
    label = 'myhostedservice'
    desc = 'my hosted service'
    location = 'West US'

    sms.create_hosted_service(name, label, desc, location)

Lista de todos os serviços alojados para a sua subscrição com a **lista\_alojado\_serviços** método:

    result = sms.list_hosted_services()

    for hosted_service in result:
        print('Service name: ' + hosted_service.service_name)
        print('Management URL: ' + hosted_service.url)
        print('Location: ' + hosted_service.hosted_service_properties.location)
        print('')

Se pretender obter informações sobre um determinado serviço alojado, pode fazê-lo transferindo o nome do serviço alojado para o **obter\_alojado\_serviço\_propriedades** método:

    hosted_service = sms.get_hosted_service_properties('myhostedservice')

    print('Service name: ' + hosted_service.service_name)
    print('Management URL: ' + hosted_service.url)
    print('Location: ' + hosted_service.hosted_service_properties.location)

Depois de ter criado um serviço na nuvem, pode implementar o seu código para o serviço com o **criar\_implementação** método.

## <a name="DeleteCloudService"> </a>Como: eliminar um serviço na nuvem

Pode eliminar um serviço na nuvem ao passar o nome do serviço para o **Eliminar\_alojado\_serviço** método:

    sms.delete_hosted_service('myhostedservice')

Antes de poder eliminar um serviço, todas as implementações para o serviço pela primeira vez devem ser eliminadas. (Consulte o artigo [como: eliminar uma implementação](#DeleteDeployment) para obter detalhes.)

## <a name="DeleteDeployment"> </a>Como: eliminar uma implementação

Para eliminar uma implementação, utilize o **Eliminar\_implementação** método. O exemplo seguinte mostra como eliminar uma implementação denominada `v1`.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    sms.delete_deployment('myhostedservice', 'v1')

## <a name="CreateStorageService"> </a>Como: criar um serviço de armazenamento

Um [serviço de armazenamento](../storage/storage-create-storage-account.md) dá-lhe acesso a [Blobs](../storage/storage-python-how-to-use-blob-storage.md)do Azure, [tabelas](../storage/storage-python-how-to-use-table-storage.md)e [filas](../storage/storage-python-how-to-use-queue-storage.md). Para criar um serviço de armazenamento, é necessário um nome para o serviço (entre os carateres em minúsculas 3 e 24 e exclusivo Azure), uma descrição, uma etiqueta (até 100 carateres, automaticamente codificados em base64) e uma localização. O exemplo seguinte mostra como criar um serviço de armazenamento ao especificar uma localização.

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    name = 'mystorageaccount'
    label = 'mystorageaccount'
    location = 'West US'
    desc = 'My storage account description.'

    result = sms.create_storage_account(name, desc, label, location=location)

    operation_result = sms.get_operation_status(result.request_id)
    print('Operation status: ' + operation_result.status)

Nota no exemplo anterior que o estado do **criar\_armazenamento\_conta** operação pode ser recuperada passando o resultado devolvido por **criar\_armazenamento\_conta** para o **obter\_operação\_Estado** método.  

Pode listar suas contas de armazenamento e respetivas propriedades com o **lista\_armazenamento\_contas** método:

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.list_storage_accounts()
    for account in result:
        print('Service name: ' + account.service_name)
        print('Location: ' + account.storage_service_properties.location)
        print('')

## <a name="DeleteStorageService"> </a>Como: eliminar um serviço de armazenamento

Pode eliminar um serviço de armazenamento ao passar o nome do serviço de armazenamento para as **Eliminar\_armazenamento\_conta** método. Eliminar um serviço de armazenamento elimina todos os dados armazenados no serviço (blobs, tabelas e filas).

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    sms.delete_storage_account('mystorageaccount')

## <a name="ListOperatingSystems"> </a>Como: sistemas operativos disponíveis de lista

Para listar os sistemas operativos que estão disponíveis para serviços de alojamento, utilize o **lista\_operativo\_sistemas** método:

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.list_operating_systems()

    for os in result:
        print('OS: ' + os.label)
        print('Family: ' + os.family_label)
        print('Active: ' + str(os.is_active))

Em alternativa, pode utilizar o **lista\_operativo\_sistema\_famílias de tipos de** método, que agrupa os sistemas operativos pela família:

    result = sms.list_operating_system_families()

    for family in result:
        print('Family: ' + family.label)
        for os in family.operating_systems:
            if os.is_active:
                print('OS: ' + os.label)
                print('Version: ' + os.version)
        print('')

## <a name="CreateVMImage"> </a>Como: criar uma imagem de sistema operativo

Para adicionar uma imagem do sistema operativo para o repositório de imagem, utilize o **Adicionar\_SO\_imagem** método:

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    name = 'mycentos'
    label = 'mycentos'
    os = 'Linux' # Linux or Windows
    media_link = 'url_to_storage_blob_for_source_image_vhd'

    result = sms.add_os_image(label, media_link, name, os)

    operation_result = sms.get_operation_status(result.request_id)
    print('Operation status: ' + operation_result.status)

Para listar as imagens de sistema operativo que estão disponíveis, utilize o **lista\_SO\_imagens** método. Inclui todas as imagens de plataforma e imagens de utilizador:

    result = sms.list_os_images()

    for image in result:
        print('Name: ' + image.name)
        print('Label: ' + image.label)
        print('OS: ' + image.os)
        print('Category: ' + image.category)
        print('Description: ' + image.description)
        print('Location: ' + image.location)
        print('Media link: ' + image.media_link)
        print('')

## <a name="DeleteVMImage"> </a>Como: eliminar uma imagem de sistema operativo

Para eliminar uma imagem de utilizador, utilize o **Eliminar\_SO\_imagem** método:

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    result = sms.delete_os_image('mycentos')

    operation_result = sms.get_operation_status(result.request_id)
    print('Operation status: ' + operation_result.status)

## <a name="CreateVM"> </a>Como: criar uma máquina virtual

Para criar uma máquina virtual, tem primeiro criar um [serviço na nuvem](#CreateCloudService).  Em seguida, crie a implementação de máquina virtual utilizando o **criar\_virtual\_máquina\_implementação** método:

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    name = 'myvm'
    location = 'West US'

    #Set the location
    sms.create_hosted_service(service_name=name,
        label=name,
        location=location)

    # Name of an os image as returned by list_os_images
    image_name = 'OpenLogic__OpenLogic-CentOS-62-20120531-en-us-30GB.vhd'

    # Destination storage account container/blob where the VM disk
    # will be created
    media_link = 'url_to_target_storage_blob_for_vm_hd'

    # Linux VM configuration, you can use WindowsConfigurationSet
    # for a Windows VM instead
    linux_config = LinuxConfigurationSet('myhostname', 'myuser', 'mypassword', True)

    os_hd = OSVirtualHardDisk(image_name, media_link)

    sms.create_virtual_machine_deployment(service_name=name,
        deployment_name=name,
        deployment_slot='production',
        label=name,
        role_name=name,
        system_config=linux_config,
        os_virtual_hard_disk=os_hd,
        role_size='Small')

## <a name="DeleteVM"> </a>Como: eliminar uma máquina virtual

Para eliminar uma máquina virtual, elimina a implementação do utilizando o **Eliminar\_implementação** método:

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    sms.delete_deployment(service_name='myvm',
        deployment_name='myvm')

O serviço de nuvem, em seguida, pode ser eliminado utilizando a **Eliminar\_alojado\_serviço** método:

    sms.delete_hosted_service(service_name='myvm')

##<a name="how-to-create-a-virtual-machine-from-a-captured-virtual-machine-image"></a>Como: Criar uma Máquina Virtual a partir de uma imagem capturada Máquina Virtual

Para capturar uma imagem VM, pela primeira vez ligar para o **capturar\_vm\_imagem** método:

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    # replace the below three parameters with actual values
    hosted_service_name = 'hs1'
    deployment_name = 'dep1'
    vm_name = 'vm1'

    image_name = vm_name + 'image'
    image = CaptureRoleAsVMImage    ('Specialized',
        image_name,
        image_name + 'label',
        image_name + 'description',
        'english',
        'mygroup')

    result = sms.capture_vm_image(
            hosted_service_name,
            deployment_name,
            vm_name,
            image
        )

Em seguida, para se certificar de que tenha capturado com êxito a imagem, utilize o **lista\_vm\_imagens** api e certifique-se a sua imagem é apresentada nos resultados de:

    images = sms.list_vm_images()

Para criar finalmente máquina virtual utilizando a imagem capturada, utilize o **criar\_virtual\_máquina\_implementação** método conforme antes, mas desta vez passar a vm_image_name em vez disso

    from azure import *
    from azure.servicemanagement import *

    sms = ServiceManagementService(subscription_id, certificate_path)

    name = 'myvm'
    location = 'West US'

    #Set the location
    sms.create_hosted_service(service_name=name,
        label=name,
        location=location)

    sms.create_virtual_machine_deployment(service_name=name,
        deployment_name=name,
        deployment_slot='production',
        label=name,
        role_name=name,
        system_config=linux_config,
        os_virtual_hard_disk=None,
        role_size='Small',
        vm_image_name = image_name)

Para saber mais sobre como capturar uma máquina de Virtual Linux, consulte o artigo [como capturar uma máquina de Virtual Linux.](../virtual-machines/virtual-machines-linux-classic-capture-image.md)

Para saber mais sobre como capturar uma máquina de Virtual do Windows, consulte o artigo [como capturar uma máquina de Virtual do Windows.](../virtual-machines/virtual-machines-windows-classic-capture-image.md)

## <a name="What's Next"> </a>Passos seguintes

Agora que aprendeu as noções básicas de gestão do serviço, pode aceder a [documentação de referência de API completa para o SDK do Python Azure](http://azure-sdk-for-python.readthedocs.org/) e executar tarefas complexas facilmente para gerir a aplicação python.

Para mais informações, consulte o [Centro de programadores do Python](/develop/python/).

[What is Service Management]: #WhatIs
[Concepts]: #Concepts
[How to: Connect to service management]: #Connect
[How to: List available locations]: #ListAvailableLocations
[How to: Create a cloud service]: #CreateCloudService
[How to: Delete a cloud service]: #DeleteCloudService
[How to: Create a deployment]: #CreateDeployment
[How to: Update a deployment]: #UpdateDeployment
[How to: Move deployments between staging and production]: #MoveDeployments
[How to: Delete a deployment]: #DeleteDeployment
[How to: Create a storage service]: #CreateStorageService
[How to: Delete a storage service]: #DeleteStorageService
[How to: List available operating systems]: #ListOperatingSystems
[How to: Create an operating system image]: #CreateVMImage
[How to: Delete an operating system image]: #DeleteVMImage
[How to: Create a virtual machine]: #CreateVM
[How to: Delete a virtual machine]: #DeleteVM
[Next Steps]: #NextSteps
[management-portal]: https://manage.windowsazure.com/
[svc-mgmt-rest-api]: http://msdn.microsoft.com/library/windowsazure/ee460799.aspx


[serviço na nuvem]:/services/cloud-services/

