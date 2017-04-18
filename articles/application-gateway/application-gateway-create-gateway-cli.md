<properties
   pageTitle="Criar um gateway de aplicação utilizando o clip do Azure no Gestor de recursos | Microsoft Azure"
   description="Saiba como criar um Gateway aplicação utilizando o clip do Azure no Gestor de recursos"
   services="application-gateway"
   documentationCenter="na"
   authors="georgewallace"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/25/2016"
   ms.author="gwallace" />

# <a name="create-an-application-gateway-by-using-the-azure-cli"></a>Criar um gateway de aplicação, utilizando o clip do Azure

> [AZURE.SELECTOR]
- [Portal do Azure](application-gateway-create-gateway-portal.md)
- [Azure PowerShell do Gestor de recursos](application-gateway-create-gateway-arm.md)
- [Azure PowerShell clássico](application-gateway-create-gateway.md)
- [Modelo do Gestor de recursos Azure](application-gateway-create-gateway-arm-template.md)
- [Clip Azure](application-gateway-create-gateway-cli.md)

Azure de aplicação de Gateway é um balanceador de carga de camada-7. Fornece falha na ligação, pedidos HTTP encaminhamento de desempenho entre servidores diferentes, quer estejam na nuvem ou no local. Gateway aplicação tem as seguintes funcionalidades de entrega de aplicação: HTTP carregar balanceamento, afinidade sessão baseada em cookies e Secure Sockets Layer (SSL) a passagem para da, sondas de estado de funcionamento personalizado e o suporte para múltiplos sites.

## <a name="prerequisite-install-the-azure-cli"></a>Pré-requisito: Instalar o clip Azure

Para realizar os passos neste artigo, tem de [instalar a Interface de comandos do Azure para Mac, Linux e o Windows (Azure CLI)](../xplat-cli-install.md) e é necessário para [Iniciar sessão no Azure](../xplat-cli-connect.md). 

> [AZURE.NOTE] Se não tiver uma conta Azure, necessita de um. Subir o início de sessão para uma [avaliação gratuita aqui](../active-directory/sign-up-organization.md).

## <a name="scenario"></a>Cenário

Neste cenário, saiba como criar um gateway de aplicação através do portal Azure.

Este cenário irá:

- Crie um gateway aplicação média com duas instâncias.
- Crie uma rede virtual denominada AdatumAppGatewayVNET com um bloco CIDR reservado de 10.0.0.0/16.
- Crie uma sub-rede denominada Appgatewaysubnet que utiliza 10.0.0.0/28 como o seu bloco CIDR.
- Configure um certificado para a passagem para da SSL.

![Exemplo de cenário][scenario]

>[AZURE.NOTE] Sondas adicionais de configuração do gateway aplicação, incluindo o estado de funcionamento personalizado, endereços de conjunto de dados back-end e regras adicionais estão configurados depois do gateway aplicação está configurado e não durante a implementação inicial.

## <a name="before-you-begin"></a>Antes de começar

Azure Gateway de aplicação requer a sua própria sub-rede. Ao criar uma rede virtual, certifique-se de que deixar espaço suficiente endereço ter várias sub-redes. Assim que implementar um gateway de aplicação a uma sub-rede, gateways aplicação apenas adicional que conseguem ser adicionada à sub-rede.

## <a name="log-in-to-azure"></a>Iniciar sessão no Azure

Abra a **linha de comandos do Microsoft Azure**e inicie sessão. 

    azure login

Depois de escrever o exemplo anterior, é fornecido um código. Navegue até ao https://aka.ms/devicelogin num browser para continuar o processo de início de sessão.

![início de sessão do cmd a mostrar dispositivo][1]

No browser, introduza o código que recebeu. Ser redirecionado para uma página de início de sessão.

![browser para introduzir o código][2]

Assim que tiver sido introduzido o código tiver sessão iniciada no, feche o browser para continuar com o cenário.

![com êxito sessão iniciada no][3]

## <a name="switch-to-resource-manager-mode"></a>Mudar para o modo de Gestor de recursos

    azure config mode arm

## <a name="create-the-resource-group"></a>Criar o grupo de recursos

Antes de criar o gateway de aplicação, é criado um grupo de recursos para conter o gateway de aplicação. A imagem seguinte mostra o comando.

    azure group create -n AdatumAppGatewayRG -l eastus

## <a name="create-a-virtual-network"></a>Criar uma rede virtual

Assim que o grupo de recursos é criado, uma rede virtual é criada para o gateway de aplicação.  No exemplo seguinte, o espaço de endereços era como 10.0.0.0/16 conforme definido nas notas cenário anterior.

    azure network vnet create -n AdatumAppGatewayVNET -a 10.0.0.0/16 -g AdatumAppGatewayRG -l eastus

## <a name="create-a-subnet"></a>Criar uma sub-rede

Quando a rede virtual estiver criada, é adicionada uma sub-rede para o gateway de aplicação.  Se planeia utilizar gateway aplicação com uma aplicação web alojada na mesma rede virtual como gateway para a aplicação, certifique-se de que deixar espaço suficiente para outra sub-rede.

    azure network vnet subnet create -g AdatumAppGatewayRG -n Appgatewaysubnet -v AdatumAppGatewayVNET -a 10.0.0.0/28 

## <a name="create-the-application-gateway"></a>Criar um gateway de aplicação

Assim que a rede virtual e sub-rede são criadas, pré-requisitos para o gateway de aplicação estão concluídas. Para além de um certificado. pfx exportado anteriormente e a palavra-passe para o certificado são necessários para o passo seguinte: os endereços IP utilizados para o back-end são os endereços IP para o seu servidor de back-end. Estes valores podem ser privados IPs na rede virtual, ips público ou nomes de domínio completamente qualificado para os servidores de back-end.

    azure network application-gateway create -n AdatumAppGateway -l eastus -g AdatumAppGatewayRG -e AdatumAppGatewayVNET -m Appgatewaysubnet -r 134.170.185.46,134.170.188.221,134.170.185.50 -y c:\AdatumAppGateway\adatumcert.pfx -x P@ssw0rd -z 2 -a Standard_Medium -w Basic -j 443 -f Enabled -o 80 -i http -b https -u Standard

> [AZURE.NOTE] Para obter uma lista de parâmetros que pode ser fornecido durante a criação, execute o seguinte comando: **gateway de aplicação de rede azure criar – ajuda**.

Este exemplo cria um gateway básico de aplicações com as definições predefinidas para o escuta, conjunto de back-end, as definições de http back-end e regras. Configura também a passagem para da SSL. Pode modificar estas definições de acordo com a sua implementação assim que o aprovisionamento de ser efetuada com êxito.
Se já tiver a aplicação web definida com o conjunto de back-end nos passos anteriores, depois de criada, balanceamento de carga começa.

## <a name="next-steps"></a>Próximos passos

Saiba como criar personalizado do Estado de funcionamento sondas acedendo a [criar uma sonda de estado de funcionamento personalizado](application-gateway-create-probe-portal.md)

Saiba como configurar SSL descarregar e tomar a desencriptação SSL dispendiosa desativar os servidores web acedendo a [Configurar SSL descarregar](application-gateway-ssl-arm.md)

<!--Image references-->

[scenario]: ./media/application-gateway-create-gateway-cli/scenario.png
[1]: ./media/application-gateway-create-gateway-cli/figure1.png
[2]: ./media/application-gateway-create-gateway-cli/figure2.png
[3]: ./media/application-gateway-create-gateway-cli/figure3.png