<properties
   pageTitle="Exemplo de DMZ – construir uma DMZ para proteger aplicações com uma Firewall e NSGs | Microsoft Azure"
   description="Criar uma DMZ com uma Firewall e grupos de segurança de rede (NSG)"
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

# <a name="example-2--build-a-dmz-to-protect-applications-with-a-firewall-and-nsgs"></a>Exemplo 2 – construir uma DMZ para proteger aplicações com uma Firewall e NSGs

[Regressar à página segurança limite melhor práticas][HOME]

Este exemplo irá criar uma DMZ com uma firewall, servidores do quatro windows e grupos de segurança de rede. Também irá orientá-através de cada um dos comandos relevantes para fornecer uma compreensão mais aprofundada de cada passo. Também existe uma secção de cenário de tráfego para fornecer uma aprofundadas passo a passo como tráfego avança através de camadas de defesa a DMZ. Por fim, nas referências de secção é o código completo e instruções para criar este ambiente de teste e experimentar vários cenários. 

![DMZ com NVA e NSG de entrada][1]

## <a name="environment-description"></a>Descrição do ambiente
Neste exemplo, não existe uma subscrição que contém o seguinte:

- Dois serviços em nuvem: "FrontEnd001" e "BackEnd001"
- Uma rede Virtual "CorpNetwork", com duas sub-redes: "FrontEnd" e "Back-end"
- Um único grupo de segurança de rede que foi aplicada aos ambas as sub-redes
- Um rede virtual aparelho neste exemplo, uma Firewall de NextGen Barracuda, ligado à sub-rede front-end
- Um servidor do Windows que representa um servidor da aplicação web ("IIS01")
- Os servidores de duas janelas que representam novamente a aplicação terminam servidores ("AppVM01", "AppVM02")
- Um servidor do Windows que representa um servidor de DNS ("DNS01")

>[AZURE.NOTE] Apesar deste exemplo utiliza uma Firewall de NextGen Barracuda, muitos da eletrodomésticos Virtual diferente da rede podem ser utilizados para este exemplo.

Na secção referências abaixo existe um script do PowerShell que irá criar a maior parte do ambiente descrito acima. Construir a VMs e redes virtuais, embora são efetuadas por script de exemplo, não se descritas detalhadamente neste documento.
 
Para criar o ambiente:

  1.    Guardar o ficheiro de xml de configuração de rede incluído na secção referências (atualizado com nomes, localização e IP endereços para fazer corresponder o cenário determinado)
  2.    Atualizar as variáveis de utilizador no script para corresponder ao ambiente que o script está a ser executado contra (subscrições, nomes de serviço, etc)
  3.    Executar o script PowerShell

**Nota**: A região representada no script do PowerShell tem de corresponder à região representada no ficheiro xml de configuração da rede.

Assim que o script executada com sucesso os seguintes passos de scripts pós podem ser tidos:

1.  Configurar regras de firewall, isto é abrangido na secção abaixo intitulada: as regras de Firewall.
2.  Opcionalmente, na secção referências são duas scripts para configurar o servidor web e o servidor de aplicação com uma aplicação web simples para permitir a testar com esta configuração DMZ.

A secção seguinte explica a maior parte das declarações scripts em relação a grupos de segurança de rede.

## <a name="network-security-groups-nsg"></a>Grupos de segurança de rede (NSG)
Neste exemplo, um grupo NSG é criado e, em seguida, carregado com seis regras. 

>[AZURE.TIP] Regra geral, deverá criar as suas regras "Permitir" específicas pela primeira vez e, em seguida, as regras do "Negar" mais genéricas última. A dita prioridade atribuída que as regras são avaliadas primeiro. Depois de tráfego é encontrado para aplicar a uma regra específica, são avaliadas sem novas regras. Regras NSG podem aplicar uma na direção recebidas (da perspetiva de sub-rede).

Forma declarativa, as seguintes regras foram a ser criadas para o tráfego de entrada:

1.  Tráfego DNS interno (porta 53) é permitido
2.  Tráfego RDP (porta 3389) da Internet para qualquer VM é permitido
3.  Tráfego HTTP (porta 80) da Internet para o NVA (firewall) é permitido
4.  Qualquer tráfego (todas as portas) a partir do IIS01 para AppVM1 é permitido
5.  Qualquer tráfego (todas as portas) da Internet para todo o VNet (ambos os sub-redes) é negado
6.  Qualquer tráfego (todas as portas) da sub-rede Frontend à sub-rede back-end é negado

Com estas regras vinculado ao cada sub-rede, se um pedido de HTTP foi entrado da Internet para o servidor web, ambas as regras 3 (permitir) e 5 (negar) seria aplicável, mas uma vez que a regra 3 tem uma prioridade mais alta apenas quiser aplicar e regra de 5 não seria entrar em reproduzir. Assim, o pedido de HTTP seria permitido à firewall. Se esse mesmo tráfego estava a tentar contactar o servidor de DNS01, regra 5 (negar) seria o primeiro para aplicar e o tráfego não seria permitido a passar para o servidor. Regra de 6 (negar) bloqueia a sub-rede front-end a partir de falar à sub-rede back-end (exceto o tráfego permitido em regras de 1 e 4), isto Protege a rede de back-end caso um compromete intruso a aplicação web no front-end, o intruso seria ter acesso limitado à rede back-end "protegida" (apenas para recursos apresentado quando se clica no servidor AppVM01).

Existe uma regra de saída predefinido que permite o tráfego de saída à internet. Neste exemplo, vamos está permitir o tráfego de saída e não modificar todas as regras de saída. Para bloquear o tráfego em ambas as direções, definidos encaminhamento de utilizador é necessária, isto é abordado num exemplo de diferentes que pode encontrar no [documento de limite de segurança principal][HOME].

As regras NSG analisadas acima são muito semelhantes às regras NSG no [exemplo 1 - construir uma DMZ simples com NSGs][Example1]. Reveja a descrição da NSG nesse documento para um olhar detalhado sobre cada regra NSG e os atributos do mesmo.

## <a name="firewall-rules"></a>Regras da firewall
Um cliente de gestão de terá de estar instalado num PC para gerir a firewall e criar as configurações conforme necessário. Veja o fornecedor de documentação da sua firewall (ou outros NVA) sobre como gerir o dispositivo. O resto nesta secção irá descrevem a configuração do firewall propriamente dito, através do cliente de gestão de fornecedores (ou seja, não o Azure portal ou PowerShell).

Instruções para transferência do cliente e estabelecer ligação ao Barracuda utilizado neste exemplo podem ser encontradas aqui: [Barracuda GN Admin](https://techlib.barracuda.com/NG61/NGAdmin)

A firewall, regras de reencaminhamento de chamadas terão de ser criado. Uma vez que este exemplo apenas encaminha o tráfego da internet no vinculadas para a firewall e, em seguida, para o servidor web, é necessário o reencaminhamento de chamadas apenas uma regra NAT. Na NextGen Firewall Barracuda utilizados neste exemplo, a regra seria uma regra de destino NAT ("Hora de Verão NAT") para transmitir este tráfego.

Para criar a regra seguinte (ou verificar regras predefinidas existente), a partir do dashboard de cliente Barracuda GN Admin, navegue para o separador de configuração, na configuração operacionais secção, clique em Ruleset. Uma grelha denominada, "Principais regras" irá apresentar as regras de ativas e desativadas existentes na firewall. No canto superior direito da grelha deste é uma pequena, verde "+" botão, clique em esta opção para criar uma nova regra (Nota: a firewall do pode ser "bloqueada" para as alterações, se vir um botão marcado como "Bloquear" e consoante esteja ou não é possível criar ou editar regras, clique neste botão para "desbloquear" a ruleset e permitir ações de edição). Se pretender editar uma regra existente, selecione nessa regra, botão direito do rato e selecione Editar regra.

Criar uma nova regra e forneça um nome, tal como "WebTraffic". 

O ícone de regra de destino NAT tem o seguinte aspeto: ![Destino NAT ícone][2]

A regra propriamente dito seria algo parecido com:

![Regra de firewall][3]

Aqui qualquer endereço acertos a Firewall de entrada está a tentar entrar HTTP (portas 80 e 443 para HTTPS) será enviada fora interface de "DHCP1 Local IP" a Firewall e redireccionada para o servidor Web com o endereço IP do 10.0.1.5. Uma vez que o tráfego está a chegar na porta 80 e ir para o servidor web na porta 80 não era necessária nenhuma alteração de porta. No entanto, a lista de alvos poderia ter sido 10.0.1.5:8080 se nosso servidor Web ouvidos na porta 8080 traduzir, por conseguinte, a entrada porta 80 na firewall porta de entrada 8080 no servidor web.

Um método de ligação devem também ser representado, para a regra de destino a partir da Internet, "SNAT dinâmico" é o mais adequado. 

Apesar de ter sido criada apenas uma regra é importante que o seu prioridade está definida corretamente. Se na grelha de todas as regras no firewall que esta nova regra está na parte inferior (abaixo a regra "BLOCKALL")-nunca entrarão em reproduzir. Certifique-se de que a regra recentemente criada para o tráfego da web é acima a regra BLOCKALL.

Assim que a regra é criada, devem ser enviado para a firewall e, em seguida, ativado, se isto não é feito a alteração de regra só terão efeito. O processo de push e ativação é descrito na secção seguinte.

## <a name="rule-activation"></a>Ativação de regra
Com o ruleset modificada para adicionar esta regra, a ruleset tem de ser carregado para a firewall e ativado.

![Ativação de regra de firewall][4]

No canto superior direito do cliente de gestão do são um cluster dos botões. Clique no botão de "Enviar alterações" para enviar as regras modificadas para a firewall e, em seguida, clique no botão "Ativar".

Com a ativação de ruleset a firewall esta compilação de ambiente de exemplo está concluída. Opcionalmente, podem ser executados os scripts de compilação mensagem na secção referências para adicionar uma aplicação para este ambiente para testar os cenários de tráfego abaixo.

>[AZURE.IMPORTANT] É fundamental para perceber o que irá não clicar no servidor web diretamente. Quando um browser pede uma página HTTP a partir do FrontEnd001.CloudApp.Net, o ponto final de HTTP (porta 80) transmite este tráfego para o firewall não o servidor web. A firewall, em seguida, de acordo com a regra criado anteriormente, NAT pedir para o servidor Web.

## <a name="traffic-scenarios"></a>Cenários de tráfego

#### <a name="allowed-web-to-web-server-through-firewall"></a>(Permitidos) Servidor Web para Web através da Firewall
1.  Página HTTP de pedidos de utilizador do Internet a partir do FrontEnd001.CloudApp.Net (Internet serviço de nuvem opostas)
2.  Tráfego através de ponto final abrir na porta 80 interface local firewall no 10.0.1.4:80 fases do serviço de nuvem
3.  Frontend sub-rede começa processamento de regra de entrada:
  1.    NSG regra 1 (DNS) não aplicar, mover para a regra seguinte
  2.    NSG regra 2 (RDP) não aplicar, mover para a regra seguinte
  3.    Aplicar NSG regra 3 (Internet firewall), o tráfego é o processamento das regras de permitidos, parar
4.  O tráfego de acertos endereço IP interno da firewall (10.0.1.4)
5.  Regra de reencaminhamento de chamadas firewall consulte este é o tráfego de porta 80, redireciona-lo para o servidor web IIS01
6.  IIS01 está a escutar tráfego da web, receber este pedido e inicia o processamento do pedido
7.  IIS01 pede-lhe o SQL Server no AppVM01 informações
8.  Sem regras de saída numa sub-rede front-end, o tráfego é permitido
9.  A sub-rede back-end começa processamento de regra de entrada:
  1.    NSG regra 1 (DNS) não aplicar, mover para a regra seguinte
  2.    NSG regra 2 (RDP) não aplicar, mover para a regra seguinte
  3.    NSG regra 3 (Internet firewall) não aplicar, mover para a regra seguinte
  4.    NSG regra 4 aplicar (IIS01 para AppVM01), o tráfego é permitido, parar processamento de regra
10. AppVM01 recebe a consulta SQL e responde
11. Uma vez que não existem regras saídas na sub-rede back-end a resposta é permitida
12. Frontend sub-rede começa processamento de regra de entrada:
  1.    Não existe nenhuma regra NSG que se aplica a entrada tráfego da sub-rede back-end à sub-rede front-end, para que nenhuma da NSG regras aplicar
  2.    A regra de sistema predefinida permitir o tráfego entre sub-redes permitirá este tráfego para que o tráfego seja permitido.
13. O servidor do IIS recebe a resposta SQL e conclui a resposta de HTTP e envia para o autor do pedido
14. Dado que se trata de uma sessão NAT a partir da firewall, o destino de resposta for (inicialmente) para a Firewall
15. A firewall recebe a resposta do servidor Web e reencaminha novamente para o utilizador de Internet
16. Uma vez que existem sem regras de saída na sub-rede front-end a resposta é permitido e o utilizador Internet recebe a página web solicitada.

#### <a name="allowed-rdp-to-backend"></a>(Permitidos) RDP para back-end
1.  Administrador do servidor na internet os pedidos de sessão RDP para AppVM01 no BackEnd001.CloudApp.Net:xxxxx onde xxxxx é o número de porta atribuído aleatoriamente para RDP para AppVM01 (a porta atribuída, pode encontrar no Portal do Azure ou através do PowerShell)
2.  Uma vez que a Firewall apenas está a escutar o endereço de FrontEnd001.CloudApp.Net, não esteja envolvido com este fluxo de tráfego
3.  Back-end sub-rede começa processamento de regra de entrada:
  1.    NSG regra 1 (DNS) não aplicar, mover para a regra seguinte
  2.    Aplicar NSG regra 2 RDP (), o tráfego é o processamento das regras de permitidos, parar
4.  Com sem regras de saída, aplicam regras predefinidas e tráfego de retorno é permitido
5.  Sessão RDP é activado
6.  AppVM01 pede palavra-passe de nome de utilizador

#### <a name="allowed-web-server-dns-lookup-on-dns-server"></a>(Permitidos) Pesquisa de servidor DNS da Web no servidor DNS
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

#### <a name="allowed-web-server-access-file-on-appvm01"></a>(Permitidos) Ficheiro de acesso de servidor Web no AppVM01
1.  IIS01 pedir um ficheiro no AppVM01
2.  Sem regras de saída numa sub-rede front-end, o tráfego é permitido
3.  A sub-rede back-end começa processamento de regra de entrada:
  1.    NSG regra 1 (DNS) não aplicar, mover para a regra seguinte
  2.    NSG regra 2 (RDP) não aplicar, mover para a regra seguinte
  3.    NSG regra 3 (Internet firewall) não aplicar, mover para a regra seguinte
  4.    NSG regra 4 aplicar (IIS01 para AppVM01), o tráfego é permitido, parar processamento de regra
4.  AppVM01 recebe o pedido e responder com o ficheiro (partindo do princípio de acesso está autorizado)
5.  Uma vez que não existem regras saídas na sub-rede back-end a resposta é permitida
6.  Frontend sub-rede começa processamento de regra de entrada:
  1.    Não existe nenhuma regra NSG que se aplica a entrada tráfego da sub-rede back-end à sub-rede front-end, para que nenhuma da NSG regras aplicar
  2.    A regra de sistema predefinida permitir o tráfego entre sub-redes permitirá este tráfego para que o tráfego seja permitido.
7.  O servidor do IIS recebe o ficheiro

#### <a name="denied-web-direct-to-web-server"></a>(Negado) Web direta para o servidor Web
Uma vez que o servidor Web, IIS01 e a Firewall são no serviço na nuvem mesmo que partilham o mesmo endereço IP público oposto. Portanto, qualquer tráfego HTTP seria ser direcionado para a firewall. Enquanto o pedido seria servido com êxito,-não é possível ir diretamente para o servidor Web, esta passou, como planeado, através da Firewall pela primeira vez. Consulte o artigo o primeiro cenário nesta secção para o fluxo de tráfego.

#### <a name="denied-web-to-backend-server"></a>(Negado) Web ao servidor de back-end
1.  Utilizador da Internet tenta aceder a um ficheiro no AppVM01 através do serviço de BackEnd001.CloudApp.Net
2.  Uma vez que não existem pontos finais aberto para partilha de ficheiros, esta não seria passar o serviço de nuvem e não seria contactar o servidor
3.  Se os pontos finais estiverem abertos por algum motivo, regra NSG 5 (Internet para VNet) seria bloquear esse tráfego

#### <a name="denied-web-dns-lookup-on-dns-server"></a>(Negado) Pesquisa de DNS Web no servidor DNS
1.  Tenta de utilizador da Internet pesquisar um registo DNS interno no DNS01 através do serviço de BackEnd001.CloudApp.Net
2.  Uma vez que não existem pontos finais aberto para DNS, esta não seria passar o serviço de nuvem e não seria contactar o servidor
3.  Se os pontos finais estiverem abertos por algum motivo, regra NSG 5 (Internet para VNet) seria bloquear esse tráfego (Nota: nessa regra 1 (DNS) do não se aplica por dois motivos, pela primeira vez o endereço de origem é a internet, só se aplica esta regra para o local VNet como a origem, também esta é uma regra de permitir, para que nunca-seria negar tráfego)

#### <a name="denied-web-to-sql-access-through-firewall"></a>(Negado) Web para o access SQL através da Firewall
1.  Utilizador da Internet os pedidos de dados SQL do FrontEnd001.CloudApp.Net (Internet serviço de nuvem opostas)
2.  Uma vez que não existem pontos finais aberto para SQL, esta não seria passar o serviço de nuvem e não seria chegar a firewall
3.  Se os pontos finais estiverem abertos por algum motivo, sub-rede Frontend começa processamento de regra de entrada:
  1.    NSG regra 1 (DNS) não aplicar, mover para a regra seguinte
  2.    NSG regra 2 (RDP) não aplicar, mover para a regra seguinte
  3.    Aplicar NSG regra 2 (Internet firewall), o tráfego é o processamento das regras de permitidos, parar
4.  O tráfego de acertos endereço IP interno da firewall (10.0.1.4)
5.  Firewall tem sem as regras de reencaminhamento para SQL e coloca o tráfego de

## <a name="conclusion"></a>Conclusão
Esta é uma forma relativamente diretamente reencaminhar de proteger a sua aplicação com uma firewall e isolar sub-rede back-end a partir de tráfego de entrada.

Obter mais exemplos e uma descrição geral dos limites de segurança de rede podem ser encontradas [aqui][HOME].

## <a name="references"></a>Referências
### <a name="main-script-and-network-config"></a>Script principal e configuração de rede
Guarde o Script completo num ficheiro de script PowerShell. Guarde a configuração de rede para um ficheiro com o nome "NetworkConf2.xml".
Modificar as variáveis definidas pelo utilizador, conforme necessário. Executar o script, em seguida, siga as instruções do programa de configuração de regra de Firewall acima.

#### <a name="full-script"></a>Script completo
Este script irá, com base nas variáveis de definidas pelo utilizador:

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
      Example of DMZ and Network Security Groups in an isolated network (Azure only, no hybrid connections)
    
     .DESCRIPTION
      This script will build out a sample DMZ setup containing:
       - A default storage account for VM disks
       - Two new cloud services
       - Two Subnets (FrontEnd and BackEnd subnets)
       - A Network Virtual Appliance (NVA), in this case a Barracuda NextGen Firewall
       - One server on the FrontEnd Subnet (plus the NVA on the FrontEnd subnet)
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
       myFirewall - 10.0.1.4
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
        $NetworkConfigFile = "C:\Scripts\NetworkConf2.xml"
    
      # VM Base Disk Image Details
        $SrvImg = Get-AzureVMImage | Where {$_.ImageFamily -match 'Windows Server 2012 R2 Datacenter'} | sort PublishedDate -Descending | Select ImageName -First 1 | ForEach {$_.ImageName}
        $FWImg = Get-AzureVMImage | Where {$_.ImageFamily -match 'Barracuda NextGen Firewall'} | sort PublishedDate -Descending | Select ImageName -First 1 | ForEach {$_.ImageName}
    
      # NSG Details
        $NSGName = "MyVNetSG"
    
    # User Defined VM Specific Config
        # Note: To ensure proper NSG Rule creation later in this script:
        #       - The Web Server must be VM 1
        #       - The AppVM1 Server must be VM 2
        #       - The DNS server must be VM 4
        #
        #       Otherwise the NSG rules in the last section of this
        #       script will need to be changed to match the modified
        #       VM array numbers ($i) so the NSG Rule IP addresses
        #       are aligned to the associated VM IP addresses.
    
        # VM 0 - The Network Virtual Appliance (NVA)
          $VMName += "myFirewall"
          $ServiceName += $FrontEndService
          $VMFamily += "Firewall"
          $img += $FWImg
          $size += "Small"
          $SubnetName += $FESubnet
          $VMIP += "10.0.1.4"
    
        # VM 1 - The Web Server
          $VMName += "IIS01"
          $ServiceName += $FrontEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $FESubnet
          $VMIP += "10.0.1.5"
    
        # VM 2 - The First Appliaction Server
          $VMName += "AppVM01"
          $ServiceName += $BackEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $BESubnet
          $VMIP += "10.0.2.5"
    
        # VM 3 - The Second Appliaction Server
          $VMName += "AppVM02"
          $ServiceName += $BackEndService
          $VMFamily += "Windows"
          $img += $SrvImg
          $size += "Standard_D3"
          $SubnetName += $BESubnet
          $VMIP += "10.0.2.6"
    
        # VM 4 - The DNS Server
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
            If ($VMFamily[$i] -eq "Firewall") 
                { 
                New-AzureVMConfig -Name $VMName[$i] -ImageName $img[$i] –InstanceSize $size[$i] | `
                    Add-AzureProvisioningConfig -Linux -LinuxUser $LocalAdmin -Password $LocalAdminPwd  | `
                    Set-AzureSubnet  –SubnetNames $SubnetName[$i] | `
                    Set-AzureStaticVNetIP -IPAddress $VMIP[$i] | `
                    New-AzureVM –ServiceName $ServiceName[$i] -VNetName $VNetName -Location $DeploymentLocation
                # Set up all the EndPoints we'll need once we're up and running
                # Note: Web traffic goes through the firewall, so we'll need to set up a HTTP endpoint.
                #       Also, the firewall will be redirecting web traffic to a new IP and Port in a
                #       forwarding rule, so the HTTP endpoint here will have the same public and local
                #       port and the firewall will do the NATing and redirection as declared in the
                #       firewall rule.
                Add-AzureEndpoint -Name "MgmtPort1" -Protocol tcp -PublicPort 801  -LocalPort 801  -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "MgmtPort2" -Protocol tcp -PublicPort 807  -LocalPort 807  -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                Add-AzureEndpoint -Name "HTTP"      -Protocol tcp -PublicPort 80   -LocalPort 80   -VM (Get-AzureVM -ServiceName $ServiceName[$i] -Name $VMName[$i]) | Update-AzureVM
                # Note: A SSH endpoint is automatically created on port 22 when the appliance is created.
                }
            Else
                {
                New-AzureVMConfig -Name $VMName[$i] -ImageName $img[$i] –InstanceSize $size[$i] | `
                    Add-AzureProvisioningConfig -Windows -AdminUsername $LocalAdmin -Password $LocalAdminPwd  | `
                    Set-AzureSubnet  –SubnetNames $SubnetName[$i] | `
                    Set-AzureStaticVNetIP -IPAddress $VMIP[$i] | `
                    Set-AzureVMMicrosoftAntimalwareExtension -AntimalwareConfiguration '{"AntimalwareEnabled" : true}' | `
                    Remove-AzureEndpoint -Name "PowerShell" | `
                    New-AzureVM –ServiceName $ServiceName[$i] -VNetName $VNetName -Location $DeploymentLocation
                    # Note: A Remote Desktop endpoint is automatically created when each VM is created.
                }
            $i++
        }
    
    # Configure NSG
        Write-Host "Configuring the Network Security Group (NSG)" -ForegroundColor Cyan
        
      # Build the NSG
        Write-Host "Building the NSG" -ForegroundColor Cyan
        New-AzureNetworkSecurityGroup -Name $NSGName -Location $DeploymentLocation -Label "Security group for $VNetName subnets in $DeploymentLocation"
    
      # Add NSG Rules
        Write-Host "Writing rules into the NSG" -ForegroundColor Cyan
        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable Internal DNS" -Type Inbound -Priority 100 -Action Allow `
            -SourceAddressPrefix VIRTUAL_NETWORK -SourcePortRange '*' `
            -DestinationAddressPrefix $VMIP[4] -DestinationPortRange '53' `
            -Protocol *
    
        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable RDP to $VNetName VNet" -Type Inbound -Priority 110 -Action Allow `
            -SourceAddressPrefix INTERNET -SourcePortRange '*' `
            -DestinationAddressPrefix VIRTUAL_NETWORK -DestinationPortRange '3389' `
            -Protocol *
    
        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable Internet to $($VMName[0])" -Type Inbound -Priority 120 -Action Allow `
            -SourceAddressPrefix Internet -SourcePortRange '*' `
            -DestinationAddressPrefix $VMIP[0] -DestinationPortRange '*' `
            -Protocol *
    
        Get-AzureNetworkSecurityGroup -Name $NSGName | Set-AzureNetworkSecurityRule -Name "Enable $($VMName[1]) to $($VMName[2])" -Type Inbound -Priority 130 -Action Allow `
            -SourceAddressPrefix $VMIP[1] -SourcePortRange '*' `
            -DestinationAddressPrefix $VMIP[2] -DestinationPortRange '*' `
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
      # Configure Firewall
      # Install Test Web App (Run Post-Build Script on the IIS Server)
      # Install Backend resource (Run Post-Build Script on the AppVM01)
      Write-Host
      Write-Host "Build Complete!" -ForegroundColor Green
      Write-Host
      Write-Host "Optional Post-script Manual Configuration Steps" -ForegroundColor Gray
      Write-Host " - Configure Firewall" -ForegroundColor Gray
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
[1]: ./media/virtual-networks-dmz-nsg-fw-asm/example2design.png "DMZ com NSG de entrada"
[2]: ./media/virtual-networks-dmz-nsg-fw-asm/dstnaticon.png "Ícone NAT de destino"
[3]: ./media/virtual-networks-dmz-nsg-fw-asm/firewallrule.png "Regra de firewall"
[4]: ./media/virtual-networks-dmz-nsg-fw-asm/firewallruleactivate.png "Ativação de regra de firewall"

<!--Link References-->
[HOME]: ../best-practices-network-security.md
[SampleApp]: ./virtual-networks-sample-app.md
[Example1]: ./virtual-networks-dmz-nsg-asm.md
