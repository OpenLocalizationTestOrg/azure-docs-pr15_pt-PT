<properties
   pageTitle="Criar um gateway de aplicação através do portal | Microsoft Azure"
   description="Saiba como criar um Gateway aplicação utilizando o portal"
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

# <a name="create-an-application-gateway-by-using-the-portal"></a>Criar um gateway de aplicação, utilizando o portal

> [AZURE.SELECTOR]
- [Portal do Azure](application-gateway-create-gateway-portal.md)
- [Azure PowerShell do Gestor de recursos](application-gateway-create-gateway-arm.md)
- [Azure PowerShell clássico](application-gateway-create-gateway.md)
- [Modelo do Gestor de recursos Azure](application-gateway-create-gateway-arm-template.md)
- [Clip Azure](application-gateway-create-gateway-cli.md)

Azure de aplicação de Gateway é um balanceador de carga de camada-7. Fornece falha na ligação, pedidos HTTP encaminhamento de desempenho entre servidores diferentes, quer estejam na nuvem ou no local. Aplicação Gateway fornece muitas funcionalidades do controlador de entrega de aplicação (ADC) incluindo balanceamento de carga HTTP afinidade sessão baseada em cookies, descarregar o Secure Sockets Layer (SSL), sondas de estado de funcionamento personalizado, o suporte para múltiplos sites e muitas outras pessoas. Para localizar uma lista completa das funcionalidades suportadas, visite [Descrição geral de Gateway de aplicação](application-gateway-introduction.md)

## <a name="scenario"></a>Cenário

Neste cenário, saiba como criar um gateway de aplicação através do portal Azure.

Este cenário irá:

- Crie um gateway aplicação média com duas instâncias.
- Crie uma rede virtual denominada AdatumAppGatewayVNET com um bloco CIDR reservado de 10.0.0.0/16.
- Crie uma sub-rede denominada Appgatewaysubnet que utiliza 10.0.0.0/28 como o seu bloco CIDR.
- Configure um certificado para a passagem para da SSL.

![Exemplo de cenário][scenario]

>[AZURE.IMPORTANT] Sondas adicionais de configuração do gateway aplicação, incluindo o estado de funcionamento personalizado, endereços de conjunto de dados back-end e regras adicionais estão configurados depois do gateway aplicação está configurado e não durante a implementação inicial.

## <a name="before-you-begin"></a>Antes de começar

Azure Gateway de aplicação requer a sua própria sub-rede. Ao criar uma rede virtual, certifique-se de que deixar espaço suficiente endereço ter várias sub-redes. Assim que implementar um gateway de aplicação a uma sub-rede, gateways aplicação apenas adicional que conseguem ser adicionada à sub-rede.

## <a name="create-the-application-gateway"></a>Criar um Gateway de aplicação

### <a name="step-1"></a>Passo 1

Navegue até ao portal do Azure, clique em **Novo** > **funcionamento em rede** > **Gateway de aplicação**

![Criar o Gateway de aplicação][1]

### <a name="step-2"></a>Passo 2

Preencha as informações básicas sobre o gateway aplicação seguinte. Quando estiver concluído, clique em **OK**

As informações necessárias para as definições básicas são:

- **Nome** - o nome para o gateway de aplicação.
- **Camada** - esta é a camada do gateway aplicação. Duas camadas estão disponíveis, **WAF** e **padrão**. WAF ativa a funcionalidade firewall da aplicação web.
- **Tamanho SKU** - esta definição é o tamanho do gateway aplicação, opções disponíveis são (**pequeno**, **médio**e **grande**). *Pequena não está disponível quando é escolhida camada WAF*
- **Contagem de instâncias** - o número de instâncias, este valor deve ser um número entre 2 e 10.
- **Grupo de recursos** - grupo de recursos para colocar em espera o gateway de aplicação, pode ser um grupo de recursos existente ou um novo.
- **Localização** - a região para o gateway de aplicação, é a mesma localização o grupo de recursos. *A localização é importante que a rede virtual e endereço IP público tem de ser na mesma localização, como o gateway*.

![definições básicas da Mostrar pá][2]

>[AZURE.NOTE] Pode ser escolhida uma contagem de instância de 1 para fins de teste. É importante saber que qualquer contagem de instâncias em duas instâncias não é abrangido pela SLA e, por conseguinte, que não são recomendadas. Gateways pequenas estão a ser utilizado para Dev Center teste e não para fins de produção.

### <a name="step-3"></a>Passo 3

Assim que são definidas as definições básicas, o passo seguinte é definir a rede virtual para ser utilizado. A rede virtual aloja à aplicação que o gateway aplicação balanceamento de carga para.

Clique em **Escolher uma rede virtual** para configurar a rede virtual.

![Pá que mostra as definições de gateway de aplicação][3]

### <a name="step-4"></a>Passo 4

No pá *Rede Virtual escolher* , clique em **Criar novo**

Enquanto não explicado neste cenário, uma rede existente Virtual poderá estar selecionada neste momento.  Se for utilizada uma rede virtual existente, é importante saber que a rede virtual necessita de uma sub-rede vazia ou uma sub-rede de recursos para gateway de aplicação apenas para ser utilizado.

![Selecione pá de rede virtual][4]

### <a name="step-5"></a>Passo 5

Preencha as informações da rede no pá a **Criar rede Virtual** , conforme descrito na descrição do [cenário](#scenario) anterior.

![Criar pá de rede virtual com informações introduzidas][5]

### <a name="step-6"></a>Passo 6

Quando a rede virtual estiver criada, o passo seguinte é definir o front-end IP para o gateway de aplicação. Neste momento, a opção é entre um público ou um endereço IP privado para front-end. A escolha depende de se a aplicação de internet opostas ou internos apenas. Este cenário assume utilizando um endereço IP público. Para escolher um endereço IP privado, pode clicar no botão **privado** . É escolhido de um endereço IP atribuído automaticamente ou pode clicar na caixa de verificação de **Escolher um endereço IP privado específico** de introduzir um manualmente.

### <a name="step-7"></a>Passo 7

Clique em **Escolher um endereço IP público**. Se houver um endereço IP público existente pode ser escolhido neste momento, este cenário pode criar um novo endereço IP público. Clique em **Criar novo**

![Selecione pá de endereço IP público][6]

### <a name="step-8"></a>Passo 8

Em seguida, atribua um nome amigável ao endereço IP público e clique em **OK**

![Criar pá do endereço IP público][7]

### <a name="step-9"></a>Passo 9

A última definição para configurar ao criar um gateway aplicação é a configuração de escuta.  Se for utilizado **http** , não resta nada para configurar e pode ser clicado em **OK** . Para utilizar **https** ainda mais configuração é necessária.

Para utilizar **https**, é necessário um certificado. A chave privada do certificado de é necessária, por isso uma exportação. pfx do certificado e a palavra-passe tem de ser fornecido.

### <a name="step-10"></a>Passo 10

Clique em **HTTPS**, clique no ícone de **pasta** junto a caixa de texto **PFX carregar certificado** .
Navegue para o ficheiro de certificado. pfx no sistema de ficheiros. Assim que estiver selecionado, dê um nome amigável ao certificado e escreva a palavra-passe para o ficheiro. pfx.

Depois de concluída clique em **OK** para rever as definições para o Gateway de aplicação.

![Secção de configuração de escuta no pá definições][9]

### <a name="step-11"></a>Passo 11

Reveja a página de resumo e clique em **OK**.  Agora o gateway aplicação em fila de espera para cima e criado.

### <a name="step-12"></a>Passo 12

Assim que o gateway aplicação ter sido criado, navegue até ao mesmo no portal para continuar a configuração do gateway aplicação.

![Vista de recursos do Gateway de aplicação][10]

Estes passos criam um gateway básico de aplicações com as definições predefinidas para o escuta, conjunto de back-end, as definições de http back-end e regras. Pode modificar estas definições de acordo com a sua implementação assim que o aprovisionamento de ser efetuada com êxito.

## <a name="next-steps"></a>Próximos passos

Este cenário cria um gateway de aplicação predefinido. São os passos configurar o gateway de aplicação ao adicionar membros do conjunto de dados, modificar as definições e, ajustando regras no gateway para ela funcionar corretamente.

Saiba como criar personalizado do Estado de funcionamento sondas acedendo a [criar uma sonda de estado de funcionamento personalizado](application-gateway-create-probe-portal.md)

Saiba como configurar SSL descarregar e tomar a desencriptação SSL dispendiosa desativar os servidores web acedendo a [Configurar SSL descarregar](application-gateway-ssl-portal.md)

Saiba como proteger as suas aplicações com [Firewall de aplicação Web](application-gateway-webapplicationfirewall-overview.md) uma funcionalidade de gateway de aplicação.

<!--Image references-->
[1]: ./media/application-gateway-create-gateway-portal/figure1.png
[2]: ./media/application-gateway-create-gateway-portal/figure2.png
[3]: ./media/application-gateway-create-gateway-portal/figure3.png
[4]: ./media/application-gateway-create-gateway-portal/figure4.png
[5]: ./media/application-gateway-create-gateway-portal/figure5.png
[6]: ./media/application-gateway-create-gateway-portal/figure6.png
[7]: ./media/application-gateway-create-gateway-portal/figure7.png
[8]: ./media/application-gateway-create-gateway-portal/figure8.png
[9]: ./media/application-gateway-create-gateway-portal/figure9.png
[10]: ./media/application-gateway-create-gateway-portal/figure10.png
[scenario]: ./media/application-gateway-create-gateway-portal/scenario.png
