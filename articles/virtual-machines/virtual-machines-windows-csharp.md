<properties
    pageTitle="Implementar recursos Azure utilizando c# | Microsoft Azure"
    description="Saiba como utilizar c# e Gestor de recursos do Azure para criar os recursos do Microsoft Azure."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="davidmu1"
    manager="timlt"
    editor="tysonn"
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="na"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/06/2016"
    ms.author="davidmu"/>

# <a name="deploy-azure-resources-using-c"></a>Implementar recursos de Azure utilizando C# 

Este artigo mostra-lhe como criar recursos Azure utilizando c#.

Tem primeiro para se certificar de que terminar as seguintes tarefas:

- Instalar o [Visual Studio](http://msdn.microsoft.com/library/dd831853.aspx)
- Verifique se a instalação do [Windows Management Framework 3.0](http://www.microsoft.com/download/details.aspx?id=34595) ou [o Windows Management Framework 4.0](http://www.microsoft.com/download/details.aspx?id=40855)
- Obter um [token de autenticação](../resource-group-authenticate-service-principal.md)

Demora cerca de 30 minutos para efetue estes passos.

## <a name="step-1-create-a-visual-studio-project-and-install-the-libraries"></a>Passo 1: Criar um projeto do Visual Studio e instale as bibliotecas

Pacotes de NuGet são a forma mais fácil de instalar as bibliotecas de que necessita para concluir este tutorial. Para obter as bibliotecas que precisa no Visual Studio, execute os seguintes passos:

1. Clique em **ficheiro** > **Novo** > **projeto**.

2. Nos **modelos** > **Visual c#**, selecione a **Aplicação de consola**, introduza o nome e localização do projeto e, em seguida, clique em **OK**.

3. O nome do projeto no Explorador de solução com o botão direito e, em seguida, clique em **Gerir pacotes de NuGet**.

4. Tipo *Do Active Directory* na caixa de pesquisa, clique em **instalar** para o pacote de biblioteca de autenticação do Active Directory e, em seguida, siga as instruções para instalar o pacote.

5. Na parte superior da página, selecione a **Versão de pré-lançamento do incluir**. Tipo *Microsoft.Azure.Management.Compute* na caixa de pesquisa, clique em **instalar** para as bibliotecas para calcular .NET e, em seguida, siga as instruções para instalar o pacote.

6. Tipo *Microsoft.Azure.Management.Network* na caixa de pesquisa, clique em **instalar** para as bibliotecas de .NET de rede e, em seguida, siga as instruções para instalar o pacote.

7. Tipo *Microsoft.Azure.Management.Storage* na caixa de pesquisa, clique em **instalar** para as bibliotecas de .NET de armazenamento e, em seguida, siga as instruções para instalar o pacote.

8. Escreva *Microsoft.Azure.Management.ResourceManager* na caixa de pesquisa, clique em **instalar** para as bibliotecas de gestão de recursos.

Agora está pronto para começar a utilizar as bibliotecas para criar uma aplicação.

## <a name="step-2-create-the-credentials-that-are-used-to-authenticate-requests"></a>Passo 2: Criar as credenciais que são utilizadas para autenticar pedidos

Agora pode formata as informações da aplicação que criou anteriormente para credenciais são utilizadas para autenticar pedidos para o Gestor de recursos do Azure.

1. Abra o ficheiro de Program.cs para o projeto que criou e, em seguida, adicioná-los com instruções para a parte superior do ficheiro:

        using Microsoft.Azure;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using Microsoft.Azure.Management.ResourceManager;
        using Microsoft.Azure.Management.ResourceManager.Models;
        using Microsoft.Azure.Management.Storage;
        using Microsoft.Azure.Management.Storage.Models;
        using Microsoft.Azure.Management.Network;
        using Microsoft.Azure.Management.Network.Models;
        using Microsoft.Azure.Management.Compute;
        using Microsoft.Azure.Management.Compute.Models;
        using Microsoft.Rest;

2. Para criar o token de que é necessário, adicione este método para a classe de programa:

        private static async Task<AuthenticationResult> GetAccessTokenAsync()
        {
          var cc = new ClientCredential("{client-id}", "{client-secret}");
          var context = new AuthenticationContext("https://login.windows.net/{tenant-id}");
          var token = await context.AcquireTokenAsync("https://management.azure.com/", cc);
          if (token == null)
          {
            throw new InvalidOperationException("Could not get the token");
          }
          return token;
        }

    Substitua {id de cliente} o identificador do Azure Active Directory aplicação, {-segredo cliente} com a tecla de acesso da aplicação de AD e {id de inquilino} com o identificador de inquilino para a sua subscrição. Pode localizar o id do inquilino através da execução Get-AzureRmSubscription. Pode encontrar a tecla de acesso utilizando o portal do Azure.

3. Para ligar para o método que tenha adicionado anteriormente, adicione este código para o método principal no ficheiro Program.cs:

        var token = GetAccessTokenAsync();
        var credential = new TokenCredentials(token.Result.AccessToken);

4. Guarde o ficheiro Program.cs.

## <a name="step-3-register-the-resource-providers-and-create-the-resources"></a>Passo 3: Registar os fornecedores de recursos e criar os recursos

### <a name="register-the-providers-and-create-a-resource-group"></a>Registe-se os fornecedores e criar um grupo de recursos

Todos os recursos tem de estar contidos num grupo de recursos. Antes de poder adicionar recursos a um grupo, a sua subscrição deve estar registada com os fornecedores de recursos.

1. Adicione variáveis para o método principais a classe de programa para especificar os nomes que pretende utilizar para os recursos de:

        var groupName = "resource group name";
        var subscriptionId = "subsciption id";
        var location = "location name";
        var storageName = "storage account name";
        var ipName = "public ip name";
        var subnetName = "subnet name";
        var vnetName = "virtual network name";
        var nicName = "network interface name";
        var avSetName = "availability set name";
        var vmName = "virtual machine name";  
        var adminName = "administrator account name";
        var adminPassword = "administrator account password";
        
    Substitua todos os valores de variáveis os nomes e identificador que pretende utilizar. Pode encontrar o identificador de subscrição ao executar Get-AzureRmSubscription.

2. Para criar o grupo de recursos e registar os fornecedores, adicione este método para a classe de programa:

        public static async Task<ResourceGroup> CreateResourceGroupAsync(
          TokenCredentials credential,
          string groupName,
          string subscriptionId,
          string location)
        {
          var resourceManagementClient = new ResourceManagementClient(credential)
            { SubscriptionId = subscriptionId };
            
          Console.WriteLine("Registering the providers...");
          var rpResult = resourceManagementClient.Providers.Register("Microsoft.Storage");
          Console.WriteLine(rpResult.RegistrationState);
          rpResult = resourceManagementClient.Providers.Register("Microsoft.Network");
          Console.WriteLine(rpResult.RegistrationState);
          rpResult = resourceManagementClient.Providers.Register("Microsoft.Compute");
          Console.WriteLine(rpResult.RegistrationState);
          
          Console.WriteLine("Creating the resource group...");
          var resourceGroup = new ResourceGroup { Location = location };
          return await resourceManagementClient.ResourceGroups.CreateOrUpdateAsync(groupName, resourceGroup);
        }

3. Para ligar para o método que tenha adicionado anteriormente, adicione este código para o método principais:

        var rgResult = CreateResourceGroupAsync(
          credential,
          groupName,
          subscriptionId,
          location);
        Console.WriteLine(rgResult.Result.Properties.ProvisioningState);
        Console.ReadLine();

### <a name="create-a-storage-account"></a>Criar uma conta de armazenamento

É necessária uma [conta de armazenamento](../storage/storage-create-storage-account.md) para armazenar o ficheiro de disco rígido virtual que é criado para a máquina virtual.

1. Para criar a conta de armazenamento, adicione este método para a classe de programa:

        public static async Task<StorageAccount> CreateStorageAccountAsync(
          TokenCredentials credential,       
          string groupName,
          string subscriptionId,
          string location,
          string storageName)
        {
          Console.WriteLine("Creating the storage account...");
          var storageManagementClient = new StorageManagementClient(credential)
            { SubscriptionId = subscriptionId };
          return await storageManagementClient.StorageAccounts.CreateAsync(
            groupName,
            storageName,
            new StorageAccountCreateParameters()
            {
              Sku = new Microsoft.Azure.Management.Storage.Models.Sku() 
                { Name = SkuName.StandardLRS},
              Kind = Kind.Storage,
              Location = location
            }
          );
        }

2. Para ligar para o método que tenha adicionado anteriormente, adicione este código para o método principais a classe de programa de:

        var stResult = CreateStorageAccountAsync(
          credential,
          groupName,
          subscriptionId,
          location,
          storageName);
        Console.WriteLine(stResult.Result.ProvisioningState);  
        Console.ReadLine();

### <a name="create-the-public-ip-address"></a>Criar o endereço IP público

Um endereço IP público é necessário para comunicar com a máquina virtual.

1. Para criar o endereço IP público da máquina virtual, adicione este método para a classe de programa:

        public static async Task<PublicIPAddress> CreatePublicIPAddressAsync(
          TokenCredentials credential,  
          string groupName,
          string subscriptionId,
          string location,
          string ipName)
        {
          Console.WriteLine("Creating the public ip...");
          var networkManagementClient = new NetworkManagementClient(credential)
            { SubscriptionId = subscriptionId };
          return await networkManagementClient.PublicIPAddresses.CreateOrUpdateAsync(
            groupName,
            ipName,
            new PublicIPAddress
            {
              Location = location,
              PublicIPAllocationMethod = "Dynamic"
            }
          );
        }

2. Para ligar para o método que tenha adicionado anteriormente, adicione este código para o método principais a classe de programa de:

        var ipResult = CreatePublicIPAddressAsync(
          credential,
          groupName,
          subscriptionId,
          location,
          ipName);
        Console.WriteLine(ipResult.Result.ProvisioningState);  
        Console.ReadLine();

### <a name="create-the-virtual-network"></a>Criar a rede virtual

Uma máquina de virtual que é criada com o modelo de implementação do Gestor de recursos tem de ser uma rede virtual.

1. Para criar uma sub-rede e uma rede virtual, adicione este método para a classe de programa:

        public static async Task<VirtualNetwork> CreateVirtualNetworkAsync(
          TokenCredentials credential,
          string groupName,
          string subscriptionId,
          string location,
          string vnetName,
          string subnetName)
        {
          Console.WriteLine("Creating the virtual network...");
          var networkManagementClient = new NetworkManagementClient(credential)
            { SubscriptionId = subscriptionId };
          
          var subnet = new Subnet
          {
            Name = subnetName,
            AddressPrefix = "10.0.0.0/24"
          };
          
          var address = new AddressSpace {
            AddressPrefixes = new List<string> { "10.0.0.0/16" }
          };
          
          return await networkManagementClient.VirtualNetworks.CreateOrUpdateAsync(
            groupName,
            vnetName,
            new VirtualNetwork
            {
              Location = location,
              AddressSpace = address,
              Subnets = new List<Subnet> { subnet }
            }
          );
        }
        
2. Para ligar para o método que tenha adicionado anteriormente, adicione este código para o método principais a classe de programa de:

        var vnResult = CreateVirtualNetworkAsync(
          credential,
          groupName,
          subscriptionId,
          location,
          vnetName,
          subnetName);
        Console.WriteLine(vnResult.Result.ProvisioningState);  
        Console.ReadLine();
        
### <a name="create-the-network-interface"></a>Criar a interface de rede

Uma máquina virtual necessita de uma interface de rede para comunicar na rede virtual.

1. Para criar uma interface de rede, adicione este método para a classe de programa:

        public static async Task<NetworkInterface> CreateNetworkInterfaceAsync(
          TokenCredentials credential,
          string groupName,
          string subscriptionId,
          string location,
          string subnetName,
          string vnetName,
          string ipName,
          string nicName)
        {
          Console.WriteLine("Creating the network interface...");
          var networkManagementClient = new NetworkManagementClient(credential)
            { SubscriptionId = subscriptionId };
          var subnetResponse = await networkManagementClient.Subnets.GetAsync(
            groupName,
            vnetName,
            subnetName
          );
          var pubipResponse = await networkManagementClient.PublicIPAddresses.GetAsync(groupName, ipName);

          return await networkManagementClient.NetworkInterfaces.CreateOrUpdateAsync(
            groupName,
            nicName,
            new NetworkInterface
            {
              Location = location,
              IpConfigurations = new List<NetworkInterfaceIPConfiguration>
              {
                new NetworkInterfaceIPConfiguration
                {
                  Name = nicName,
                  PublicIPAddress = pubipResponse,
                  Subnet = subnetResponse
                }
              }
            }
          );
        }

2. Para ligar para o método que tenha adicionado anteriormente, adicione este código para o método principais a classe de programa de:

        var ncResult = CreateNetworkInterfaceAsync(
          credential,
          groupName,
          subscriptionId,
          location,
          subnetName,
          vnetName,
          ipName,
          nicName);
        Console.WriteLine(ncResult.Result.ProvisioningState);  
        Console.ReadLine();

### <a name="create-an-availability-set"></a>Criar um conjunto de disponibilidade

Conjuntos de disponibilidade facilitam gerir a manutenção das máquinas virtuais utilizado pela sua aplicação.

1. Para criar um conjunto de disponibilidade, adicione este método para a classe de programa:

        public static async Task<AvailabilitySet> CreateAvailabilitySetAsync(
          TokenCredentials credential,
          string groupName,
          string subscriptionId,
          string location,
          string avsetName)
        {
          Console.WriteLine("Creating the availability set...");
          var computeManagementClient = new ComputeManagementClient(credential)
            { SubscriptionId = subscriptionId };
          return await computeManagementClient.AvailabilitySets.CreateOrUpdateAsync(
            groupName,
            avsetName,
            new AvailabilitySet()
            {
              Location = location
            }
          );
        }

2. Para ligar para o método que tenha adicionado anteriormente, adicione este código para o método principais a classe de programa de:

        var avResult = CreateAvailabilitySetAsync(
          credential,  
          groupName,
          subscriptionId,
          location,
          avSetName);
        Console.ReadLine();

### <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

Agora que criou todos os recursos de suporte, pode criar uma máquina virtual.

1. Para criar a máquina virtual, adicione este método para a classe de programa:

        public static async Task<VirtualMachine> CreateVirtualMachineAsync(
          TokenCredentials credential, 
          string groupName,
          string subscriptionId,
          string location,
          string nicName,
          string avsetName,
          string storageName,
          string adminName,
          string adminPassword,
          string vmName)
        {
          var networkManagementClient = new NetworkManagementClient(credential)
            { SubscriptionId = subscriptionId };
          var nic = networkManagementClient.NetworkInterfaces.Get(groupName, nicName);

          var computeManagementClient = new ComputeManagementClient(credential);
          computeManagementClient.SubscriptionId = subscriptionId;
          var avSet = computeManagementClient.AvailabilitySets.Get(groupName, avsetName);

          Console.WriteLine("Creating the virtual machine...");
          return await computeManagementClient.VirtualMachines.CreateOrUpdateAsync(
            groupName,
            vmName,
            new VirtualMachine
            {
              Location = location,
              AvailabilitySet = new Microsoft.Azure.Management.Compute.Models.SubResource
              {
                Id = avSet.Id
              },
              HardwareProfile = new HardwareProfile
              {
                VmSize = "Standard_A0"
              },
              OsProfile = new OSProfile
              {
                AdminUsername = adminName,
                AdminPassword = adminPassword,
                ComputerName = vmName,
                WindowsConfiguration = new WindowsConfiguration
                {
                  ProvisionVMAgent = true
                }
              },
              NetworkProfile = new NetworkProfile
              {
                NetworkInterfaces = new List<NetworkInterfaceReference>
                {
                  new NetworkInterfaceReference { Id = nic.Id }
                }
              },
              StorageProfile = new StorageProfile
              {
                ImageReference = new ImageReference
                {
                  Publisher = "MicrosoftWindowsServer",
                  Offer = "WindowsServer",
                  Sku = "2012-R2-Datacenter",
                  Version = "latest"
                },
                OsDisk = new OSDisk
                {
                  Name = "mytestod1",
                  CreateOption = DiskCreateOptionTypes.FromImage,
                  Vhd = new VirtualHardDisk
                  {
                    Uri = "http://" + storageName + ".blob.core.windows.net/vhds/mytestod1.vhd"
                  }
                }
              }
            }
          );
        }

    >[AZURE.NOTE] Neste tutorial cria uma máquina de virtual a executar uma versão do sistema operativo Windows Server. Para saber mais sobre como selecionar outras imagens, consulte o artigo [navegar e selecionar máquina virtual Azure imagens com o Windows PowerShell e o clip do Azure](virtual-machines-linux-cli-ps-findimage.md).

2. Para ligar para o método que tenha adicionado anteriormente, adicione este código para o método principais:

        var vmResult = CreateVirtualMachineAsync(
          credential,
          groupName,
          subscriptionId,
          location,
          nicName,
          avSetName,
          storageName,
          adminName,
          adminPassword,
          vmName);
        Console.WriteLine(vmResult.Result.ProvisioningState);
        Console.ReadLine();

##<a name="step-4-delete-the-resources"></a>Passo 4: Eliminar os recursos

Uma vez que são cobrados para recursos utilizados no Azure, sempre é aconselhável para eliminar os recursos que já não forem necessárias. Se quiser eliminar as máquinas virtuais e todos os recursos de suporte, só tem de fazer de eliminar o grupo de recursos.

1.  Para eliminar o grupo de recursos, adicione este método para a classe de programa:

        public static async void DeleteResourceGroupAsync(
          TokenCredentials credential,
          string groupName,
          string subscriptionId)
        {
          Console.WriteLine("Deleting resource group...");
          var resourceManagementClient = new ResourceManagementClient(credential)
            { SubscriptionId = subscriptionId };
          await resourceManagementClient.ResourceGroups.DeleteAsync(groupName);
        }

2.  Para ligar para o método que tenha adicionado anteriormente, adicione este código para o método principais:

        DeleteResourceGroupAsync(
          credential,
          groupName,
          subscriptionId);
        Console.ReadLine();

## <a name="step-5-run-the-console-application"></a>Passo 5: Executar a aplicação da consola

1. Para executar a aplicação da consola, clique em **Iniciar** no Visual Studio e, em seguida, inicie sessão no Azure AD utilizando o mesmo nome de utilizador e palavra-passe que utiliza com a sua subscrição.

2. Prima **Enter** após cada código de estado é devolvido para criar cada recurso. Depois da máquina virtual é criada, efetue o passo seguinte antes de premir Enter para eliminar todos os recursos.

    Deverá demorar cerca de cinco minutos para esta aplicação consola executar completamente a partir do início para conclusão. Antes de premir Enter para iniciar a remoção de recursos, poderá demorar alguns minutos para verificar a criação de recursos no portal do Azure antes de eliminá-los.

3. Para ver o estado dos recursos, navegue para os registos de auditoria no portal do Azure:

    ![Procurar registos de auditoria no portal do Azure](./media/virtual-machines-windows-csharp/crpportal.png)
    
## <a name="next-steps"></a>Próximos passos

- Tire partido da utilização de um modelo para criar uma máquina virtual utilizando as informações no [Implementar uma máquina de Virtual Azure utilizando c# e um modelo de Gestor de recursos](virtual-machines-windows-csharp-template.md).
- Saiba como gerir a máquina virtual que criou ao rever [máquinas virtuais de gerir utilizando o Gestor de recursos do Azure e PowerShell](virtual-machines-windows-csharp-manage.md).
