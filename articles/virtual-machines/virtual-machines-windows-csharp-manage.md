<properties
    pageTitle="Gerir VMs utilizando o Gestor de recursos do Azure e c# | Microsoft Azure"
    description="Gerir máquinas virtuais utilizando o Gestor de recursos do Azure e c#."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="davidmu1"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="na"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="davidmu"/>

# <a name="manage-azure-virtual-machines-using-azure-resource-manager-and-c"></a>Gerir máquinas virtuais do Azure utilizando o Gestor de recursos do Azure e C#  

As tarefas neste artigo mostram-lhe como gerir máquinas virtuais, tal como iniciar, parar e atualizar. Tem de existir uma máquina virtual num grupo de recursos para concluir as tarefas neste artigo.

Para concluir as tarefas neste artigo, é necessário:

- [Visual Studio](http://msdn.microsoft.com/library/dd831853.aspx)
- [Token de autenticação](../resource-group-authenticate-service-principal.md)

## <a name="create-a-visual-studio-project-and-install-packages"></a>Criar um projeto do Visual Studio e instalar pacotes

Pacotes de NuGet são as formas mais fácil de instalar as bibliotecas de que necessita para concluir as tarefas neste artigo. As bibliotecas de que instala neste artigo são o biblioteca de autenticação do Active Directory Azure e biblioteca para calcular fornecedor de recursos. Conclua estes passos para obter as bibliotecas no Visual Studio:

1. Clique em **ficheiro** > **Novo** > **projeto**.

2. Nos **modelos** > **Visual c#**, selecione a **Aplicação de consola**, introduza o nome e localização do projeto e, em seguida, clique em **OK**.

3. O nome do projeto no Explorador de solução com o botão direito e, em seguida, clique em **Gerir pacotes de NuGet**.

4. Tipo *Do Active Directory* na caixa de pesquisa, clique em **instalar** para o pacote de biblioteca de autenticação do Active Directory e, em seguida, siga as instruções para instalar o pacote.

5. Na parte superior da página, selecione a **Versão de pré-lançamento do incluir**. Tipo *Microsoft.Azure.Management.Compute* na caixa de pesquisa, clique em **instalar** para as bibliotecas para calcular .NET e, em seguida, siga as instruções para instalar o pacote.

Agora está pronto para começar a utilizar as bibliotecas para gerir o seu máquinas virtuais.

## <a name="set-up-the-project"></a>Configurar o projecto

Agora que a aplicação é criada e as bibliotecas estão instaladas, criar um token utilizando as informações de aplicação. Este token é utilizada para autenticar pedidos para o Gestor de recursos do Azure.

1. Abra o ficheiro de Program.cs para o projeto que criou e, em seguida, adicioná-los com instruções para a parte superior do ficheiro:

        using Microsoft.Azure;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using Microsoft.Azure.Management.Compute;
        using Microsoft.Azure.Management.Compute.Models;
        using Microsoft.Rest;
        
2. Adicione variáveis para o método principais a classe de programa para especificar o nome do grupo de recursos e o nome de máquina virtual e o identificador de subscrição de:

        var groupName = "resource group name";
        var vmName = "virtual machine name";  
        var subscriptionId = "subsciption id";

    Pode encontrar o identificador de subscrição ao executar Get-AzureRmSubscription.
    
3. Para obter o token de que é necessário para criar as credenciais, adicione este método para a classe de programa:

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
    
4. Para criar as credenciais, adicione este código para o método principais Program.cs:

        var token = GetAccessTokenAsync();
        var credential = new TokenCredentials(token.Result.AccessToken);

5. Guarde o ficheiro Program.cs.

## <a name="display-information-about-a-virtual-machine"></a>Apresentar informações sobre uma máquina virtual

1. Adicione este método para a classe de programa do projeto que criou anteriormente:

        public static async void GetVirtualMachineAsync(
          TokenCredentials credential, 
          string groupName, 
          string vmName, 
          string subscriptionId)
        {
          Console.WriteLine("Getting information about the virtual machine...");

          var computeManagementClient = new ComputeManagementClient(credential)
            { SubscriptionId = subscriptionId };
          var vmResult = await computeManagementClient.VirtualMachines.GetAsync(
            groupName, 
            vmName, 
            InstanceViewTypes.InstanceView);

          Console.WriteLine("hardwareProfile");
          Console.WriteLine("   vmSize: " + vmResult.HardwareProfile.VmSize);

          Console.WriteLine("\nstorageProfile");
          Console.WriteLine("  imageReference");
          Console.WriteLine("    publisher: " + vmResult.StorageProfile.ImageReference.Publisher);
          Console.WriteLine("    offer: " + vmResult.StorageProfile.ImageReference.Offer);
          Console.WriteLine("    sku: " + vmResult.StorageProfile.ImageReference.Sku);
          Console.WriteLine("    version: " + vmResult.StorageProfile.ImageReference.Version);
          Console.WriteLine("  osDisk");
          Console.WriteLine("    osType: " + vmResult.StorageProfile.OsDisk.OsType);
          Console.WriteLine("    name: " + vmResult.StorageProfile.OsDisk.Name);
          Console.WriteLine("    createOption: " + vmResult.StorageProfile.OsDisk.CreateOption);
          Console.WriteLine("    uri: " + vmResult.StorageProfile.OsDisk.Vhd.Uri);
          Console.WriteLine("    caching: " + vmResult.StorageProfile.OsDisk.Caching);

          Console.WriteLine("\nosProfile");
          Console.WriteLine("  computerName: " + vmResult.OsProfile.ComputerName);
          Console.WriteLine("  adminUsername: " + vmResult.OsProfile.AdminUsername);
          Console.WriteLine("  provisionVMAgent: " + vmResult.OsProfile.WindowsConfiguration.ProvisionVMAgent.Value);
          Console.WriteLine("  enableAutomaticUpdates: " + vmResult.OsProfile.WindowsConfiguration.EnableAutomaticUpdates.Value);

          Console.WriteLine("\nnetworkProfile");
          foreach (NetworkInterfaceReference nic in vmResult.NetworkProfile.NetworkInterfaces)
          {
            Console.WriteLine("  networkInterface id: " + nic.Id);
          }

          Console.WriteLine("\nvmAgent");
          Console.WriteLine("  vmAgentVersion" + vmResult.InstanceView.VmAgent.VmAgentVersion);
          Console.WriteLine("    statuses");
          foreach (InstanceViewStatus stat in vmResult.InstanceView.VmAgent.Statuses)
          {
            Console.WriteLine("    code: " + stat.Code);
            Console.WriteLine("    level: " + stat.Level);
            Console.WriteLine("    displayStatus: " + stat.DisplayStatus);
            Console.WriteLine("    message: " + stat.Message);
            Console.WriteLine("    time: " + stat.Time);
          }

          Console.WriteLine("\ndisks");
          foreach (DiskInstanceView idisk in vmResult.InstanceView.Disks)
          {
            Console.WriteLine("  name: " + idisk.Name);
            Console.WriteLine("  statuses");
            foreach (InstanceViewStatus istat in idisk.Statuses)
            {
              Console.WriteLine("    code: " + istat.Code);
              Console.WriteLine("    level: " + istat.Level);
              Console.WriteLine("    displayStatus: " + istat.DisplayStatus);
              Console.WriteLine("    time: " + istat.Time);
            }
          }

          Console.WriteLine("\nVM general status");
          Console.WriteLine("  provisioningStatus: " + vmResult.ProvisioningState);
          Console.WriteLine("  id: " + vmResult.Id);
          Console.WriteLine("  name: " + vmResult.Name);
          Console.WriteLine("  type: " + vmResult.Type);
          Console.WriteLine("  location: " + vmResult.Location);
          Console.WriteLine("\nVM instance status");
          foreach (InstanceViewStatus istat in vmResult.InstanceView.Statuses)
          {
            Console.WriteLine("\n  code: " + istat.Code);
            Console.WriteLine("  level: " + istat.Level);
            Console.WriteLine("  displayStatus: " + istat.DisplayStatus);
          }
          
        }

2. Para ligar para o método que acabou de adicionar, adicione este código para o método principais:

        GetVirtualMachineAsync(
          credential,
          groupName,
          vmName,
          subscriptionId);
        Console.WriteLine("\nPress enter to continue...");
        Console.ReadLine();
    
3. Guarde o ficheiro Program.cs.

4. Clique em **Iniciar** no Visual Studio e, em seguida, inicie sessão no Azure AD utilizando o mesmo nome de utilizador e palavra-passe que utiliza com a sua subscrição.

    Quando executa este método, deverá ver algo parecido com este exemplo:
    
        Getting information about the virtual machine...
        hardwareProfile
          vmSize: Standard_A0

        storageProfile
          imageReference
            publisher: MicrosoftWindowsServer
            offer: WindowsServer
            sku: 2012-R2-Datacenter
            version: latest
          osDisk
            osType: Windows
            name: myosdisk
            createOption: FromImage
            uri: http://store1.blob.core.windows.net/vhds/myosdisk.vhd
            caching: ReadWrite

          osProfile
            computerName: vm1
            adminUsername: account1
            provisionVMAgent: True
            enableAutomaticUpdates: True

          networkProfile
            networkInterface 
              id: /subscriptions/{subscription-id}
                /resourceGroups/rg1/providers/Microsoft.Network/networkInterfaces/nc1

          vmAgent
            vmAgentVersion2.7.1198.766
            statuses
            code: ProvisioningState/succeeded
            level: Info
            displayStatus: Ready
            message: GuestAgent is running and accepting new configurations.
            time: 4/13/2016 8:35:32 PM

          disks
            name: myosdisk
            statuses
              code: ProvisioningState/succeeded
              level: Info
              displayStatus: Provisioning succeeded
              time: 4/13/2016 8:04:36 PM

          VM general status
            provisioningStatus: Succeeded
            id: /subscriptions/{subscription-id}
              /resourceGroups/rg1/providers/Microsoft.Compute/virtualMachines/vm1
            name: vm1
            type: Microsoft.Compute/virtualMachines
            location: centralus

          VM instance status
            code: ProvisioningState/succeeded
              level: Info
              displayStatus: Provisioning succeeded
            code: PowerState/running
              level: Info
              displayStatus: VM running

## <a name="stop-a-virtual-machine"></a>Parar uma máquina virtual

Pode deixar uma máquina virtual de duas maneiras. Pode parar uma máquina virtual e manter todas as suas definições, mas continuar a Serei cobrado por-lo ou pode parar uma máquina virtual e retirá-lo. Quando uma máquina virtual é desatribuída, todos os recursos associados-lo também são extremidades desatribuídas e de faturação para o mesmo.

1. Comentar qualquer código que tiver adicionado anteriormente para o método principal, exceto o código para obter as credenciais.

2. Adicione este método para a classe de programa:

        public static async void StopVirtualMachineAsync(
          TokenCredentials credential, 
          string groupName, 
          string vmName, 
          string subscriptionId)
        {
          Console.WriteLine("Stopping the virtual machine...");
          var computeManagementClient = new ComputeManagementClient(credential)
            { SubscriptionId = subscriptionId };
          await computeManagementClient.VirtualMachines.PowerOffAsync(groupName, vmName);
        }

    Se pretender retirar a máquina virtual, altere a chamada desligar para este código:

        computeManagementClient.VirtualMachines.Deallocate(groupName, vmName);

3. Para ligar para o método que acabou de adicionar, adicione este código para o método principais:

        StopVirtualMachineAsync(
          credential,
          groupName,
          vmName,
          subscriptionId);
        Console.WriteLine("\nPress enter to continue...");
        Console.ReadLine();

4. Guarde o ficheiro Program.cs.

5. Clique em **Iniciar** no Visual Studio e, em seguida, inicie sessão no Azure AD utilizando o mesmo nome de utilizador e palavra-passe que utiliza com a sua subscrição.

    Deverá ver o estado da alteração de máquina virtual seja parado. Se executou o método que deallocate chamada, o estado está parado (desatribuído).

## <a name="start-a-virtual-machine"></a>Iniciar uma máquina virtual

1. Comentar qualquer código que tiver adicionado anteriormente para o método principal, exceto o código para obter as credenciais.

2. Adicione este método para a classe de programa:

        public static async void StartVirtualMachineAsync(
          TokenCredentials credential, 
          string groupName, 
          string vmName, 
          string subscriptionId)
        {
          Console.WriteLine("Starting the virtual machine...");
          var computeManagementClient = new ComputeManagementClient(credential)
            { SubscriptionId = subscriptionId };
          await computeManagementClient.VirtualMachines.StartAsync(groupName, vmName);
        }

3. Para ligar para o método que acabou de adicionar, adicione este código para o método principais:

        StartVirtualMachineAsync(
          credential,
          groupName,
          vmName,
          subscriptionId);
        Console.WriteLine("\nPress enter to continue...");
        Console.ReadLine();

4. Guarde o ficheiro Program.cs.

5. Clique em **Iniciar** no Visual Studio e, em seguida, inicie sessão no Azure AD utilizando o mesmo nome de utilizador e palavra-passe que utiliza com a sua subscrição.

    Deverá ver o estado da máquina virtual alterar para a trabalhar rapidamente.

## <a name="restart-a-running-virtual-machine"></a>Reiniciar uma máquina virtual em execução

1. Comentar qualquer código que tiver adicionado anteriormente para o método principal, exceto o código para obter as credenciais.

2. Adicione este método para a classe de programa:

        public static async void RestartVirtualMachineAsync(
          TokenCredentials credential,
          string groupName,
          string vmName,
          string subscriptionId)
        {
          Console.WriteLine("Restarting the virtual machine...");
          var computeManagementClient = new ComputeManagementClient(credential)
            { SubscriptionId = subscriptionId };
          await computeManagementClient.VirtualMachines.RestartAsync(groupName, vmName);
        }

3. Para ligar para o método que acabou de adicionar, adicione este código para o método principais:

        RestartVirtualMachineAsync(
          credential,
          groupName,
          vmName,
          subscriptionId);
        Console.WriteLine("\nPress enter to continue...");
        Console.ReadLine();

4. Guarde o ficheiro Program.cs.

5. Clique em **Iniciar** no Visual Studio e, em seguida, inicie sessão no Azure AD utilizando o mesmo nome de utilizador e palavra-passe que utiliza com a sua subscrição.

## <a name="resize-a-virtual-machine"></a>Redimensionar uma máquina virtual

Este exemplo mostra-lhe como alterar o tamanho de uma máquina virtual em execução.

1. Comentar qualquer código que tiver adicionado anteriormente para o método principal, exceto o código para obter as credenciais.

2. Adicione este método para a classe de programa:

        public static async void UpdateVirtualMachineAsync(
          TokenCredentials credential, 
          string groupName, 
          string vmName, 
          string subscriptionId)
        {
          Console.WriteLine("Updating the virtual machine...");
          var computeManagementClient = new ComputeManagementClient(credential)
            { SubscriptionId = subscriptionId };
          var vmResult = await computeManagementClient.VirtualMachines.GetAsync(groupName, vmName);
          vmResult.HardwareProfile.VmSize = "Standard_A1";
          await computeManagementClient.VirtualMachines.CreateOrUpdateAsync(groupName, vmName, vmResult);
        }

3. Para ligar para o método que acabou de adicionar, adicione este código para o método principais:

        UpdateVirtualMachineAsync(
          credential,
          groupName,
          vmName,
          subscriptionId);
        Console.WriteLine("\nPress enter to continue...");
        Console.ReadLine();

4. Guarde o ficheiro Program.cs.

5. Clique em **Iniciar** no Visual Studio e, em seguida, inicie sessão no Azure AD utilizando o mesmo nome de utilizador e palavra-passe que utiliza com a sua subscrição.

    Deverá visualizar o tamanho da alteração máquina virtual Standard_A1.

## <a name="add-a-data-disk-to-a-virtual-machine"></a>Adicionar um disco de dados para uma máquina virtual

Este exemplo mostra-lhe como adicionar um disco de dados para uma máquina virtual em execução.

1. Comentar qualquer código que tiver adicionado anteriormente para o método principal, exceto o código para obter as credenciais.

2. Adicione este método para a classe de programa:

        public static async void AddDataDiskAsync(
          TokenCredentials credential, 
          string groupName, 
          string vmName, 
          string subscriptionId)
        {
          Console.WriteLine("Adding the disk to the virtual machine...");
          var computeManagementClient = new ComputeManagementClient(credential)
            { SubscriptionId = subscriptionId };
          var vmResult = await computeManagementClient.VirtualMachines.GetAsync(groupName, vmName);
          vmResult.StorageProfile.DataDisks.Add(
            new DataDisk
              {
                Lun = 0,
                Name = "mydatadisk1",
                Vhd = new VirtualHardDisk
                  {
                    Uri = "https://mystorage1.blob.core.windows.net/vhds/mydatadisk1.vhd"
                  },
                CreateOption = DiskCreateOptionTypes.Empty,
                DiskSizeGB = 2,
                Caching = CachingTypes.ReadWrite
              });
          await computeManagementClient.VirtualMachines.CreateOrUpdateAsync(groupName, vmName, vmResult);
        }

3. Para ligar para o método que acabou de adicionar, adicione este código para o método principais:

        AddDataDiskAsync(
          credential,
          groupName,
          vmName,
          subscriptionId);
        Console.WriteLine("\nPress enter to continue...");
        Console.ReadLine();

4. Guarde o ficheiro Program.cs.

5. Clique em **Iniciar** no Visual Studio e, em seguida, inicie sessão no Azure AD utilizando o mesmo nome de utilizador e palavra-passe que utiliza com a sua subscrição.

## <a name="delete-a-virtual-machine"></a>Eliminar uma máquina virtual

1. Comentar qualquer código que tiver adicionado anteriormente para o método principal, exceto o código para obter as credenciais.

2. Adicione este método para a classe de programa:

        public static async void DeleteVirtualMachineAsync(
          TokenCredentials credential, 
          string groupName, 
          string vmName, 
          string subscriptionId)
        {
          Console.WriteLine("Deleting the virtual machine...");
          var computeManagementClient = new ComputeManagementClient(credential)
            { SubscriptionId = subscriptionId };
          await computeManagementClient.VirtualMachines.DeleteAsync(groupName, vmName);
        }

3. Para ligar para o método que acabou de adicionar, adicione este código para o método principais:

        DeleteVirtualMachineAsync(
          credential,
          groupName,
          vmName,
          subscriptionId);
        Console.WriteLine("\nPress enter to continue...");
        Console.ReadLine();

4. Guarde o ficheiro Program.cs.

5. Clique em **Iniciar** no Visual Studio e, em seguida, inicie sessão no Azure AD utilizando o mesmo nome de utilizador e palavra-passe que utiliza com a sua subscrição.

## <a name="next-steps"></a>Próximos passos

Se havia problemas com uma implementação, poderá observe [implementações de grupo de recursos de resolução de problemas com o Azure portal](../resource-manager-troubleshoot-deployments-portal.md)
