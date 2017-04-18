<properties
   pageTitle="Exemplo de DMZ – construir uma DMZ Simple com NSGs | Microsoft Azure"
   description="Criar uma DMZ com grupos de segurança de rede (NSG)"
   services="virtual-network"
   documentationCenter="na"
   authors="tracsman"
   manager="rossort"
   editor=""/>

<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/01/2016"
   ms.author="jonor;sivae"/>

# <a name="example-1--build-a-simple-dmz-with-nsgs"></a>Exemplo 1 – construir uma DMZ Simple com NSGs

[Regressar à página segurança limite melhor práticas][HOME]

Este exemplo criará uma DMZ simple com quatro servidores do windows e grupos de segurança de rede. Também irá orientá-através de cada um dos comandos relevantes para fornecer uma compreensão mais aprofundada de cada passo. Também existe uma secção de cenário de tráfego para fornecer uma aprofundada passo a passo como tráfego avança através de camadas de defesa a DMZ. Por fim, nas referências de secção é o código completo e instruções para criar este ambiente de teste e experimentar vários cenários. 

![DMZ com NSG de entrada][1]

## <a name="environment-description"></a>Descrição do ambiente
Neste exemplo, não existe uma subscrição que contém o seguinte:

- Dois serviços em nuvem: "FrontEnd001" e "BackEnd001"
- Uma rede Virtual, "CorpNetwork", com duas sub-redes; "FrontEnd" e "Back-end"
- Um grupo de segurança de rede que foi aplicada aos ambas as sub-redes
- Um servidor do Windows que representa um servidor da aplicação web ("IIS01")
- Os servidores de duas janelas que representam novamente a aplicação terminam servidores ("AppVM01", "AppVM02")
- Um servidor do Windows que representa um servidor de DNS ("DNS01")

Na secção referências abaixo existe um script do PowerShell que irá criar a maior parte do ambiente descrito acima. Construir a VMs e redes Virtual, embora são efetuadas por script de exemplo, não se descritas detalhadamente neste documento. 

Para criar o ambiente;

  1.    Guardar o ficheiro de xml de configuração de rede incluído na secção referências (atualizado com nomes, localização e IP endereços para fazer corresponder o cenário determinado)
  2.    Atualizar as variáveis de utilizador no script para corresponder ao ambiente que o script está a ser executado contra (subscrições, nomes de serviço, etc)
  3.    Executar o script PowerShell

**Nota**: A região representada no script do PowerShell tem de corresponder à região representada no ficheiro xml de configuração da rede.

Assim que o script é executado com êxito adicional passos opcionais podem ser tomadas, na secção referências dois scripts para configurar o servidor web e o servidor de aplicação com uma aplicação web simples para permitir a testar com esta configuração DMZ.

As secções seguintes irão fornecer uma descrição detalhada dos grupos de segurança de rede e sobre como funcionam para este exemplo por observar linhas de chave do PowerShell script.

## <a name="network-security-groups-nsg"></a>Grupos de segurança de rede (NSG)
Neste exemplo, um grupo NSG é criado e, em seguida, carregado com seis regras. 

>[AZURE.TIP] Regra geral, deverá criar as suas regras "Permitir" específicas pela primeira vez e, em seguida, as regras do "Negar" mais genéricas última. A dita prioridade atribuída que as regras são avaliadas primeiro. Depois de tráfego é encontrado para aplicar a uma regra específica, são avaliadas sem novas regras. Regras NSG podem aplicar uma na direção recebidas (da perspetiva de sub-rede).

Forma declarativa, as seguintes regras foram a ser criadas para o tráfego de entrada:

1.  Tráfego DNS interno (porta 53) é permitido
2.  Tráfego RDP (porta 3389) da Internet para qualquer VM é permitido
3.  HTTP o tráfego (porta 80) da Internet para o servidor web (IIS01) é permitido
4.  Qualquer tráfego (todas as portas) a partir do IIS01 para AppVM1 é permitido
5.  Qualquer tráfego (todas as portas) da Internet para todo o VNet (ambos os sub-redes) é negado
6.  Qualquer tráfego (todas as portas) da sub-rede Frontend à sub-rede back-end é negado

Com estas regras vinculado ao cada sub-rede, se um pedido de HTTP foi entrado da Internet para o servidor web, ambas as regras 3 (permitir) e 5 (negar) seria aplicável, mas uma vez que a regra 3 tem uma prioridade mais alta apenas quiser aplicar e regra de 5 não seria entrar em reproduzir. Assim, o pedido de HTTP seria permitido para o servidor web. Se esse mesmo tráfego estava a tentar contactar o servidor de DNS01, regra 5 (negar) seria o primeiro para aplicar e o tráfego não seria permitido a passar para o servidor. Regra de 6 (negar) bloqueia a sub-rede front-end a partir de falar à sub-rede back-end (exceto o tráfego permitido em regras de 1 e 4), isto Protege a rede de back-end caso um compromete intruso a aplicação web no front-end, o intruso seria ter acesso limitado à rede back-end "protegida" (apenas para recursos apresentado quando se clica no servidor AppVM01).

Existe uma regra de saída predefinido que permite o tráfego de saída à internet. Neste exemplo, vamos está permitir o tráfego de saída e não modificar todas as regras de saída. Para bloquear o tráfego em ambos os sentidos, definidos encaminhamento de utilizador é necessária, isto é abordado no "Exemplo 3" abaixo.

Cada regra é abordada mais detalhadamente da seguinte forma (**Nota**: qualquer item na lista que começa com um sinal de dólar abaixo (por exemplo,: $NSGName) é uma variável de definidas pelo utilizador a partir do script na secção referência deste documento):

1. Em primeiro lugar um grupo de segurança de rede têm de ser formado para colocar em espera as regras:

        New-AzureNetworkSecurityGroup -Name $NSGName `
            -Location $DeploymentLocation `
            -Label "Security group for $VNetName subnets in $DeploymentLocation"
 
2.  Neste exemplo, a primeira regra irá permitir o tráfego DNS entre todas as redes internas para o servidor DNS na sub-rede back-end. A regra tem algumas parâmetros importantes:
  - "Escrever" significa que se trata em que direcção de tráfego esta regra será aplicada; Este é da perspetiva de sub-rede ou Máquina Virtual (consoante onde está vinculado este NSG). Assim, se tipo for "Entrada" e tráfego é introduzir sub-rede, quiser aplicar a regra e sair da sub-rede o tráfego não seja afectado por esta regra.
  - "Prioridade" define a ordem em que será avaliado um fluxo de tráfego. Quanto mais baixa o número maior é a prioridade. Assim que uma regra aplica-se para um fluxo de tráfego específico, sem as outras regras são processadas. Assim, se uma regra com prioridade 1 permite que o tráfego e uma regra com prioridade 2 impede o tráfego e ambas as regras aplicam-se para o tráfego de, em seguida, o tráfego seria permitido fluxo (uma vez que a regra 1 tinha uma prioridade mais alta demorou efeito e não mais foram aplicadas regras).
  - "Ação" se trata de se o tráfego afectado por esta regra for bloqueado ou permitido.

            Get-AzureNetworkSecurityGroup -Name $NSGName | `
                Set-AzureNetworkSecurityRule -Name "Enable Internal DNS" `
                -Type Inbound -Priority 100 -Action Allow `
                -SourceAddressPrefix VIRTUAL_NETWORK -SourcePortRange '*' `
                -DestinationAddressPrefix $VMIP[4] `
                -DestinationPortRange '53' `
                -Protocol *

3.  Esta regra irá permitir o tráfego do RDP fluxo da internet para a porta RDP em qualquer servidor em qualquer um dos sub-rede a VNET. Esta regra utiliza dois tipos especiais de prefixos de endereço "VIRTUAL_NETWORK" e "INTERNET". Este é uma forma fácil de endereço categorias maiores de prefixos de endereço.

        Get-AzureNetworkSecurityGroup -Name $NSGName | `
            Set-AzureNetworkSecurityRule -Name "Enable RDP to $VNetName VNet" `
            -Type Inbound -Priority 110 -Action Allow `
            -SourceAddressPrefix INTERNET -SourcePortRange '*' `
            -DestinationAddressPrefix VIRTUAL_NETWORK `
            -DestinationPortRange '3389' `
            -Protocol *

4.  Esta regra permite que o tráfego de internet entrada acertar o servidor web. Isto não altera o comportamento de encaminhamento; apenas permitir o tráfego destinado IIS01 passar. Assim, se o tráfego da Internet tivesse o servidor web como o respectivo destino esta regra seria permiti-lo e parar de processar mais regras. (Na regra na prioridade está bloqueado 140 todos os outros entrada tráfego da internet). Se apenas está a processar tráfego HTTP, esta regra poderia ainda mais restrito para permitir apenas 80 de porta de destino.

        Get-AzureNetworkSecurityGroup -Name $NSGName | `
            Set-AzureNetworkSecurityRule -Name "Enable Internet to $VMName[0]" `
            -Type Inbound -Priority 120 -Action Allow `
            -SourceAddressPrefix Internet -SourcePortRange '*' `
            -DestinationAddressPrefix $VMIP[0] `
            -DestinationPortRange '*' `
            -Protocol *

5.  Esta regra permite o tráfego transmitir do servidor IIS01 para o servidor de AppVM01, um bloqueia regra posterior todos os outros Frontend para o tráfego de back-end. Para melhorar a esta regra, se a porta for conhecida que deve ser adicionado. Por exemplo, se o servidor do IIS está de atingir apenas do SQL Server no AppVM01, o intervalo de porta de destino deve ser alterado de "*" (qualquer) para 1433 (a porta SQL), permitindo assim que uma superfície de entrada ataque mais pequena no AppVM01 deve a aplicação web nunca ser comprometida.

        Get-AzureNetworkSecurityGroup -Name $NSGName | `
            Set-AzureNetworkSecurityRule -Name "Enable $VMName[1] to $VMName[2]" `
            -Type Inbound -Priority 130 -Action Allow `
        -SourceAddressPrefix $VMIP[1] -SourcePortRange '*' `
        -DestinationAddressPrefix $VMIP[2] `
        -DestinationPortRange '*' `
        -Protocol *
 
6.  Esta regra impede o tráfego da internet para todos os servidores de rede. Em combinação com a regra de prioridade 110 e 120, permite que apenas entrada tráfego da internet para a firewall e portas RDP para outros servidores e blocos de tudo. 

        Get-AzureNetworkSecurityGroup -Name $NSGName | `
            Set-AzureNetworkSecurityRule `
            -Name "Isolate the $VNetName VNet from the Internet" `
            -Type Inbound -Priority 140 -Action Deny `
            -SourceAddressPrefix INTERNET -SourcePortRange '*' `
            -DestinationAddressPrefix VIRTUAL_NETWORK `
            -DestinationPortRange '*' `
            -Protocol *
 
7.  A regra final impede o tráfego da sub-rede Frontend à sub-rede back-end. Dado que se trata de uma regra só de entrada, é permitido tráfego inverso (a partir de back-end para o front-end).

        Get-AzureNetworkSecurityGroup -Name $NSGName | `
            Set-AzureNetworkSecurityRule `
            -Name "Isolate the $FESubnet subnet from the $BESubnet subnet" `
            -Type Inbound -Priority 150 -Action Deny `
            -SourceAddressPrefix $FEPrefix -SourcePortRange '*' `
            -DestinationAddressPrefix $BEPrefix `
            -DestinationPortRange '*' `
            -Protocol * 

## <a name="traffic-scenarios"></a>Cenários de tráfego
#### <a name="allowed-web-to-web-server"></a>(*Permitidos*) Servidor Web para Web
1.  Página HTTP de pedidos de utilizador do Internet a partir do FrontEnd001.CloudApp.Net (Internet serviço de nuvem opostas)
2.  Nuvem de tráfego através de ponto final abrir na porta 80 no sentido IIS01 fases do serviço (o servidor web)
3.  Frontend sub-rede começa processamento de regra de entrada:
  1.    NSG regra 1 (DNS) não aplicar, mover para a regra seguinte
  2.    NSG regra 2 (RDP) não aplicar, mover para a regra seguinte
  3.    Aplicar NSG regra 3 (Internet para IIS01), o tráfego é o processamento das regras de permitidos, parar
4.  O tráfego de acertos interno endereço IP do servidor web IIS01 (10.0.1.5)
5.  IIS01 está a escutar tráfego da web, receber este pedido e inicia o processamento do pedido
6.  IIS01 pede-lhe o SQL Server no AppVM01 informações
7.  Sem regras de saída numa sub-rede front-end, o tráfego é permitido
8.  A sub-rede back-end começa processamento de regra de entrada:
  1.    NSG regra 1 (DNS) não aplicar, mover para a regra seguinte
  2.    NSG regra 2 (RDP) não aplicar, mover para a regra seguinte
  3.    NSG regra 3 (Internet firewall) não aplicar, mover para a regra seguinte
  4.    NSG regra 4 aplicar (IIS01 para AppVM01), o tráfego é permitido, parar processamento de regra
9.  AppVM01 recebe a consulta SQL e responde
10. Uma vez que não existem regras saídas na sub-rede back-end a resposta é permitida
11. Frontend sub-rede começa processamento de regra de entrada:
  1.    Não existe nenhuma regra NSG que se aplica a entrada tráfego da sub-rede back-end à sub-rede front-end, para que nenhuma da NSG regras aplicar
  2.    A regra de sistema predefinida permitir o tráfego entre sub-redes permitirá este tráfego para que o tráfego seja permitido.
12. O servidor do IIS recebe a resposta SQL e conclui a resposta de HTTP e envia para o autor do pedido
13. Uma vez que existem sem regras de saída na sub-rede front-end a resposta é permitido e o utilizador Internet recebe a página web solicitada.

#### <a name="allowed-rdp-to-backend"></a>(*Permitidos*) RDP para back-end
1.  Administrador do servidor na internet os pedidos de sessão RDP para AppVM01 no BackEnd001.CloudApp.Net:xxxxx onde xxxxx é o número de porta atribuído aleatoriamente para RDP para AppVM01 (a porta atribuída, pode encontrar no Portal do Azure ou através do PowerShell)
2.  Back-end sub-rede começa processamento de regra de entrada:
  1.    NSG regra 1 (DNS) não aplicar, mover para a regra seguinte
  2.    Aplicar NSG regra 2 RDP (), o tráfego é o processamento das regras de permitidos, parar
3.  Com sem regras de saída, aplicam regras predefinidas e tráfego de retorno é permitido
4.  Sessão RDP é activado
5.  AppVM01 pede palavra-passe de nome de utilizador

#### <a name="allowed-web-server-dns-lookup-on-dns-server"></a>(*Permitidos*) Pesquisa de servidor DNS da Web no servidor DNS
1.  Servidor, IIS01, necessidades em termos de feed de dados na www.data.gov, mas as necessidades para resolver o endereço da Web.
2.  A configuração de rede para as listas de VNet DNS01 (10.0.2.4 na sub-rede back-end) como o servidor DNS principal, IIS01 envia o pedido DNS para DNS01
3.  Sem regras de saída numa sub-rede front-end, o tráfego é permitido
4.  Back-end sub-rede começa processamento de regra de entrada:
  1.     Aplicar NSG regra 1 (DNS), o tráfego é o processamento das regras de permitidos, parar
5.  Servidor DNS recebe o pedido
6.  Servidor de DNS não tem o endereço em cache e pede um servidor DNS de raiz na internet
7.  Sem regras de saída numa sub-rede back-end, o tráfego é permitido
8.  Responde servidor DNS da Internet, uma vez que esta sessão foi iniciada internamente, a resposta é permitida
9.  Coloca a resposta do servidor DNS e responde ao pedido inicial novamente para IIS01
10. Sem regras de saída numa sub-rede back-end, o tráfego é permitido
11. Frontend sub-rede começa processamento de regra de entrada:
  1.    Não existe nenhuma regra NSG que se aplica a entrada tráfego da sub-rede back-end à sub-rede front-end, para que nenhuma da NSG regras aplicar
  2.    A regra de sistema predefinida permitir o tráfego entre sub-redes permitirá este tráfego para que o tráfego é permitido
12. IIS01 recebe a resposta de DNS01

#### <a name="allowed-web-server-access-file-on-appvm01"></a>(*Permitidos*) Ficheiro de acesso de servidor Web no AppVM01
1.  IIS01 pedir um ficheiro no AppVM01
2.  Sem regras de saída numa sub-rede front-end, o tráfego é permitido
3.  A sub-rede back-end começa processamento de regra de entrada:
  1.    NSG regra 1 (DNS) não aplicar, mover para a regra seguinte
  2.    NSG regra 2 (RDP) não aplicar, mover para a regra seguinte
  3.    NSG regra 3 (Internet para IIS01) não aplicar, mover para a regra seguinte
  4.    NSG regra 4 aplicar (IIS01 para AppVM01), o tráfego é permitido, parar processamento de regra
4.  AppVM01 recebe o pedido e responder com o ficheiro (partindo do princípio de acesso está autorizado)
5.  Uma vez que não existem regras saídas na sub-rede back-end a resposta é permitida
6.  Frontend sub-rede começa processamento de regra de entrada:
  1.    Não existe nenhuma regra NSG que se aplica a entrada tráfego da sub-rede back-end à sub-rede front-end, para que nenhuma da NSG regras aplicar
  2.    A regra de sistema predefinida permitir o tráfego entre sub-redes permitirá este tráfego para que o tráfego seja permitido.
7.  O servidor do IIS recebe o ficheiro

#### <a name="denied-web-to-backend-server"></a>(*Negado*) Web ao servidor de back-end
1.  Utilizador da Internet tenta aceder a um ficheiro no AppVM01 através do serviço de BackEnd001.CloudApp.Net
2.  Uma vez que não existem pontos finais aberto para partilha de ficheiros, esta não seria passar o serviço de nuvem e não seria contactar o servidor
3.  Se os pontos finais estiverem abertos por algum motivo, regra NSG 5 (Internet para VNet) seria bloquear esse tráfego

#### <a name="denied-web-dns-lookup-on-dns-server"></a>(*Negado*) Pesquisa de DNS Web no servidor DNS
1.  Tenta de utilizador da Internet pesquisar um registo DNS interno no DNS01 através do serviço de BackEnd001.CloudApp.Net
2.  Uma vez que não existem pontos finais aberto para DNS, esta não seria passar o serviço de nuvem e não seria contactar o servidor
3.  Se os pontos finais estiverem abertos por algum motivo, regra NSG 5 (Internet para VNet) seria bloquear esse tráfego (Nota: nessa regra 1 (DNS) do não se aplica por dois motivos, pela primeira vez o endereço de origem é a internet, só se aplica esta regra para o local VNet como a origem, também esta é uma regra de permitir, para que nunca-seria negar tráfego)

#### <a name="denied-web-to-sql-access-through-firewall"></a>(*Negado*) Web para o access SQL através da Firewall
1.  Utilizador da Internet os pedidos de dados SQL do FrontEnd001.CloudApp.Net (Internet serviço de nuvem opostas)
2.  Uma vez que não existem pontos finais aberto para SQL, esta não seria passar o serviço de nuvem e não seria chegar a firewall
3.  Se os pontos finais estiverem abertos por algum motivo, sub-rede Frontend começa processamento de regra de entrada:
  1.    NSG regra 1 (DNS) não aplicar, mover para a regra seguinte
  2.    NSG regra 2 (RDP) não aplicar, mover para a regra seguinte
  3.    Aplicar NSG regra 3 (Internet para IIS01), o tráfego é o processamento das regras de permitidos, parar
4.  O tráfego de acertos endereço IP interno da IIS01 (10.0.1.5)
5.  IIS01 não está a escutar porta 1433, por isso, não resposta ao pedido

## <a name="conclusion"></a>Conclusão
Esta é uma forma relativamente simple e reencaminhar diretamente de isolar a sub-rede back-end a partir de tráfego de entrada.

Obter mais exemplos e uma descrição geral dos limites de segurança de rede podem ser encontradas [aqui][HOME].

## <a name="references"></a>Referências
### <a name="main-script-and-network-config"></a>Script principal e configuração de rede
Guarde o Script completo num ficheiro de script PowerShell. Guarde a configuração de rede para um ficheiro com o nome "NetworkConf1.xml".
Modificar as variáveis definidas pelo utilizador, conforme necessário. Executar o script, em seguida, siga as instruções de configuração de Firewall regra contidas na secção 1 de exemplo acima.

#### <a name="full-script"></a>Script completo
Este script irá, com base em variáveis definidas pelo utilizador;

1.  Ligar a uma subscrição do Azure
2.  Criar uma nova conta de armazenamento
3.  Criar um novo VNet e duas sub-redes tal como foi definido no ficheiro de configuração de rede
4.  Construir 4 windows server VMs
5.  Configure NSG incluindo:
  - Criar um NSG
  - Preenchê-lo com regras
  - Encadernação a NSG para as sub-redes adequadas

Este script do PowerShell localmente no deve ser executado que um internet ligado PC ou de servidor.

>[AZURE.IMPORTANT] Quando este script é executado, poderão existir avisos ou outras mensagens informativas que pop no PowerShell. Mensagens de erro apenas vermelho são motivos para preocupações.


    <# 
     .SYNOPSIS
      Example of Network Security Groups in an isolated network (Azure only, no hybrid connections)
    
     .DESCRIPTION
      This script will build out a sample DMZ setup containing:
       - A default storage account for VM disks
       - Two new cloud services
       - Two Subnets (FrontEnd and BackEnd subnets)
       - One server on the FrontEnd Subnet
       - Three Servers on the BackEnd Subnet
       - Network Security Groups to allow/deny traffic patterns as declared
      
      Before running script, ensure the network configuration file is created in
      the directory referenced by $NetworkConfigFile variable (or update the
      variable to reflect the path and file name of the config file being used).
    
     .Notes
      Security requirements are different for each use case and can be addressed in a
      myriad of ways. Please be sure that any sensitive data or applications are behind
      the appropriate layer(s) of protection. This script serves as an example of some
      of the techniques that can be used, but should not be used for all scenarios. You
      are responsible to assess your security needs and the appropriate protections
      needed, and then effectively implement those protections.
    
      FrontEnd Service (FrontEnd subnet 10.0.1.0/24)
       IIS01      - 10.0.1.5
     
      BackEnd Service (BackEnd subnet 10.0.2.0/24)
       DNS01      - 10.0.2.4
       AppVM01    - 10.0.2.5
       AppVM02    - 10.0.2.6
    
    #>
    
    # Fixed Variables
        $LocalAdminPwd = Read-Host -Prompt "Enter Local Admin Password to be used for all VMs"
        $VMName = @()
        $ServiceName = @()
        $VMFamily = @()
        $img = @()
        $size = @()
        $SubnetName = @()
        $VMIP = @()
    
    # User Defined Global Variables
      # These should be changes to reflect your subscription and services
      # Invalid options will fail in the validation section
    
      # Subscription Access Details
        $subID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
    
      # VM Account, Location, and Storage Details
        $LocalAdmin = "theAdmin"
        $DeploymentLocation = "Central US"
        $StorageAccountName = "vmstore02"
    
      # Service Details
        $FrontEndService = "FrontEnd001"
        $BackEndService = "BackEnd001"
    
      # Network Details
        $VNetName = "CorpNetwork"
        $FESubnet = "FrontEnd"
        $FEPrefix = "10.0.1.0/24"
        $BESubnet = "BackEnd"
        $BEPrefix = "10.0.2.0/24"
        $NetworkConfigFile = "C:\Scripts\NetworkConf1.xml"
    
      # VM Base Disk Image Details
        $SrvImg = Get-AzureVMImage | Where {$_.ImageFamily -match 'Windows Server 2012 R2 Datacenter'} | sort PublishedDate -Descending | Select ImageName -First 1 | ForEach {$_.ImageName}
      
      # NSG Details
        $NSGName = "MyVNetSG"
    
    # User Defined VM Specific Config
        # Note: To ensure proper NSG Rule creation later in this script:
        #       - The Web Server must be VM 0
        #       - The AppVM1 Server must be VM 1
        #       - The DNS server must be VM 3
        #
        #       Otherwise the NSG rules in the last section of this
        #       script will need to be changed to match the modified
        #       VM array numbers ($i) so the NSG Rule IP addresses
        #       are aligned to the associated VM IP addresses.
    
        # VM 0 - The Web Server
          $VMName += "IIS01"
          $ServiceName += $FrontEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $FESubnet
          $VMIP += "10.0.1.5"
    
        # VM 1 - The First Application Server
          $VMName += "AppVM01"
          $ServiceName += $BackEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $BESubnet
          $VMIP += "10.0.2.5"
    
        # VM 2 - The Second Application Server
          $VMName += "AppVM02"
          $ServiceName += $BackEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $BESubnet
          $VMIP += "10.0.2.6"
    
        # VM 3 - The DNS Server
          $VMName += "DNS01"
          $ServiceName += $BackEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $BESubnet
          $VMIP += "10.0.2.4"

    # ----------------------------- #
    # No User Defined Varibles or   #
    # Configuration past this point #
    # ----------------------------- #   

      # Get your Azure accounts
        Add-AzureAccount
        Set-AzureSubscription –SubscriptionId $subID -ErrorAction Stop
        Select-AzureSubscription -SubscriptionId $subID -Current -ErrorAction Stop
    
      # Create Storage Account
        If (Test-AzureName -Storage -Name $StorageAccountName) { 
            Write-Host "Fatal Error: This storage account name is already in use, please pick a diffrent name." -ForegroundColor Red
            Return}
        Else {Write-Host "Creating Storage Account" -ForegroundColor Cyan 
              New-AzureStorageAccount -Location $DeploymentLocation -StorageAccountName $StorageAccountName}
    
      # Update Subscription Pointer to New Storage Account
        Write-Host "Updating Subscription Pointer to New Storage Account" -ForegroundColor Cyan 
        Set-AzureSubscription –SubscriptionId $subID -CurrentStorageAccountName $StorageAccountName -ErrorAction Stop
    
    # Validation
    $FatalError = $false
    
    If (-Not (Get-AzureLocation | Where {$_.DisplayName -eq $DeploymentLocation})) {
         Write-Host "This Azure Location was not found or available for use" -ForegroundColor Yellow
         $FatalError = $true}
    
    If (Test-AzureName -Service -Name $FrontEndService) { 
        Write-Host "The FrontEndService service name is already in use, please pick a different service name." -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The FrontEndService service name is valid for use." -ForegroundColor Green}
    
    If (Test-AzureName -Service -Name $BackEndService) { 
        Write-Host "The BackEndService service name is already in use, please pick a different service name." -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The BackEndService service name is valid for use." -ForegroundColor Green}
    
    If (-Not (Test-Path $NetworkConfigFile)) { 
        Write-Host 'The network config file was not found, please update the $NetworkConfigFile variable to point to the network config xml file.' -ForegroundColor Yellow
        $FatalError = $true}
    Else { Write-Host "The network config file was found" -ForegroundColor Green
            If (-Not (Select-String -Pattern $DeploymentLocation -Path $NetworkConfigFile)) {
                Write-Host 'The deployment location was not found in the network config file, please check the network config file to ensure the $DeploymentLocation varible is correct and the netowrk config file matches.' -ForegroundColor Yellow
                $FatalError = $true}
            Else { Write-Host "The deployment location was found in the network config file." -ForegroundColor Green}}
    
    If ($FatalError) {
        Write-Host "A fatal error has occured, please see the above messages for more information." -ForegroundColor Red
        Return}
    Else { Write-Host "Validation passed, now building the environment." -ForegroundColor Green}
    
    # Create VNET
        Write-Host "Creating VNET" -ForegroundColor Cyan 
        Set-AzureVNetConfig -ConfigurationPath $NetworkConfigFile -ErrorAction Stop
    
    # Create Services
        Write-Host "Creating Services" -ForegroundColor Cyan
        New-AzureService -Location $DeploymentLocation -ServiceName $FrontEndService -ErrorAction Stop
        New-AzureService -Location $DeploymentLocation -ServiceName $BackEndService -ErrorAction Stop
    
    # Build VMs
        $i=0
        $VMName | Foreach {
            Write-Host "Building $($VMName[$i])" -ForegroundColor Cyan
            New-AzureVMConfig -Name $VMName[$i] -ImageName $img[$i] –InstanceSize $size[$i] | `
                Add-AzureProvisioningConfig -Windows -AdminUsername $LocalAdmin -Password $LocalAdminPwd  | `
                Set-AzureSubnet  –SubnetNames $SubnetName[$i] | `
                Set-AzureStaticVNetIP -IPAddress $VMIP[$i] | `
                Set-AzureVMMicrosoftAntimalwareExtension -AntimalwareConfiguration '{"AntimalwareEnabled" : true}' | `
                Remove-AzureEndpoint -Name "PowerShell" | `
                New-AzureVM –ServiceName $ServiceName[$i] -VNetName $VNetName -Location $DeploymentLocation
                # Note: A Remote Desktop endpoint is automatically created when each VM is created.
            $i++
        }
        # Add HTTP Endpoint for IIS01
        Get-AzureVM -ServiceName $ServiceName[0] -Name $VMName[0] | Add-AzureEndpoint -Name HTTP -Protocol tcp -LocalPort 80 -PublicPort 80 | Update-AzureVM
    
    # Configure NSG
        Write-Host "Configuring the Network Security Group (NSG)" -ForegroundColor Cyan
        
      # Build the NSG
        Write-Host "Building the NSG" -ForegroundColor Cyan
        New-AzureNetworkSecurityGroup -Name $NSGName -Location $DeploymentLocation -Label "Security group for $VNetName subnets in $DeploymentLocation"
    
      # Add NSG Rules
        Write-Host "Writing rules into the NSG" -ForegroundColor Cyan
        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable Internal DNS" -Type Inbound -Priority 100 -Action Allow `
            -SourceAddressPrefix VIRTUAL_NETWORK -SourcePortRange '*' `
            -DestinationAddressPrefix $VMIP[3] -DestinationPortRange '53' `
            -Protocol *
    
        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable RDP to $VNetName VNet" -Type Inbound -Priority 110 -Action Allow `
            -SourceAddressPrefix INTERNET -SourcePortRange '*' `
            -DestinationAddressPrefix VIRTUAL_NETWORK -DestinationPortRange '3389' `
            -Protocol *
    
        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable Internet to $($VMName[0])" -Type Inbound -Priority 120 -Action Allow `
            -SourceAddressPrefix Internet -SourcePortRange '*' `
            -DestinationAddressPrefix $VMIP[0] -DestinationPortRange '*' `
            -Protocol *
    
        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable $($VMName[0]) to $($VMName[1])" -Type Inbound -Priority 130 -Action Allow `
            -SourceAddressPrefix $VMIP[0] -SourcePortRange '*' `
            -DestinationAddressPrefix $VMIP[1] -DestinationPortRange '*' `
            -Protocol *
        
        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Isolate the $VNetName VNet from the Internet" -Type Inbound -Priority 140 -Action Deny `
            -SourceAddressPrefix INTERNET -SourcePortRange '*' `
            -DestinationAddressPrefix VIRTUAL_NETWORK -DestinationPortRange '*' `
            -Protocol *
    
        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Isolate the $FESubnet subnet from the $BESubnet subnet" -Type Inbound -Priority 150 -Action Deny `
            -SourceAddressPrefix $FEPrefix -SourcePortRange '*' `
            -DestinationAddressPrefix $BEPrefix -DestinationPortRange '*' `
            -Protocol *
    
        # Assign the NSG to the Subnets
            Write-Host "Binding the NSG to both subnets" -ForegroundColor Cyan
            Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName -SubnetName $FESubnet -VirtualNetworkName $VNetName
            Set-AzureNetworkSecurityGroupToSubnet -Name $NSGName -SubnetName $BESubnet -VirtualNetworkName $VNetName
    
    # Optional Post-script Manual Configuration
      # Install Test Web App (Run Post-Build Script on the IIS Server)
      # Install Backend resource (Run Post-Build Script on the AppVM01)
      Write-Host
      Write-Host "Build Complete!" -ForegroundColor Green
      Write-Host
      Write-Host "Optional Post-script Manual Configuration Steps" -ForegroundColor Gray
      Write-Host " - Install Test Web App (Run Post-Build Script on the IIS Server)" -ForegroundColor Gray
      Write-Host " - Install Backend resource (Run Post-Build Script on the AppVM01)" -ForegroundColor Gray
      Write-Host
      

#### <a name="network-config-file"></a>Ficheiro de configuração de rede
Guardar este ficheiro xml com localização actualizada e adicionar a ligação a este ficheiro para a variável de $NetworkConfigFile no script acima.
    
    <NetworkConfiguration xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ServiceHosting/2011/07/NetworkConfiguration">
      <VirtualNetworkConfiguration>
        <Dns>
          <DnsServers>
            <DnsServer name="DNS01" IPAddress="10.0.2.4" />
            <DnsServer name="Level3" IPAddress="209.244.0.3" />
          </DnsServers>
        </Dns>
        <VirtualNetworkSites>
          <VirtualNetworkSite name="CorpNetwork" Location="Central US">
            <AddressSpace>
              <AddressPrefix>10.0.0.0/16</AddressPrefix>
            </AddressSpace>
            <Subnets>
              <Subnet name="FrontEnd">
                <AddressPrefix>10.0.1.0/24</AddressPrefix>
              </Subnet>
              <Subnet name="BackEnd">
                <AddressPrefix>10.0.2.0/24</AddressPrefix>
              </Subnet>
            </Subnets>
            <DnsServersRef>
              <DnsServerRef name="DNS01" />
              <DnsServerRef name="Level3" />
            </DnsServersRef>
          </VirtualNetworkSite>
        </VirtualNetworkSites>
      </VirtualNetworkConfiguration>
    </NetworkConfiguration>

#### <a name="sample-application-scripts"></a>Scripts de exemplo de aplicações
Se pretender instalar uma aplicação de exemplo para esta e outros exemplos DMZ, um foi fornecido a seguinte ligação: [Exemplo de Script de aplicação][SampleApp]

<!--Image References-->
[1]: ./media/virtual-networks-dmz-nsg-asm/example1design.png "DMZ com NSG de entrada"

<!--Link References-->
[HOME]: ../best-practices-network-security.md
[SampleApp]: ./virtual-networks-sample-app.md

