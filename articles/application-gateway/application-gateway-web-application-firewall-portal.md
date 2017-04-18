<properties
   pageTitle="Criar um gateway aplicação com firewall de aplicação web utilizando o portal | Microsoft Azure"
   description="Saiba como criar um Gateway aplicação com firewall de aplicação web utilizando o portal"
   services="application-gateway"
   documentationCenter="na"
   authors="georgewallace"
   manager="carmonm"
   editor="tysonn"
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

# <a name="create-an-application-gateway-with-web-application-firewall-by-using-the-portal"></a>Criar um gateway aplicação com firewall de aplicação web utilizando o portal

> [AZURE.SELECTOR]
- [Portal do Azure](application-gateway-web-application-firewall-portal.md)
- [Azure PowerShell do Gestor de recursos](application-gateway-web-application-firewall-powershell.md)

A firewall de aplicação web (WAF) no Azure aplicação Gateway protege aplicações web do comuns ataques baseada na web, como a introdução de SQL, ataques de scripts de publicação em vários sites e contra manipulação de sessão. Aplicação Web protege contra muitas das OWASP superiores 10 comuns web vulnerabilidades.

Azure de aplicação de Gateway é um balanceador de carga de camada-7. Fornece falha na ligação, pedidos HTTP encaminhamento de desempenho entre servidores diferentes, quer estejam na nuvem ou no local.
Aplicação fornece muitas funcionalidades do controlador de entrega de aplicação (ADC) incluindo balanceamento de carga HTTP afinidade sessão baseada em cookies, Secure Sockets Layer (SSL) descarregar, o estado de funcionamento personalizado sondas, suporte para múltiplos sites e muitas outras.
Para localizar uma lista completa das funcionalidades suportadas, visite [Descrição geral de Gateway de aplicação](application-gateway-introduction.md)

## <a name="scenarios"></a>Cenários

Neste artigo, existem dois cenários:

O primeiro cenário, aprendeu para [Adicionar firewall de aplicação web para um gateway de aplicação existente](#add-web-application-firewall-to-an-existing-application-gateway).

Segundo cenário, aprendeu para [criar um gateway de aplicação com firewall de aplicação web](#create-an-application-gateway-with-web-application-firewall)

![Exemplo de cenário][scenario]

>[AZURE.NOTE] Sondas adicionais de configuração do gateway aplicação, incluindo o estado de funcionamento personalizado, endereços de conjunto de dados back-end e regras adicionais estão configurados depois do gateway aplicação está configurado e não durante a implementação inicial.

## <a name="before-you-begin"></a>Antes de começar

Azure Gateway de aplicação requer a sua própria sub-rede. Ao criar uma rede virtual, certifique-se de que deixar espaço suficiente endereço ter várias sub-redes. Assim que implementar um gateway de aplicação a uma sub-rede, gateways aplicação apenas adicional que conseguem ser adicionada à sub-rede.

## <a name="add-web-application-firewall-to-an-existing-application-gateway"></a>Adicionar firewall de aplicação web para um gateway de aplicação existente

Este cenário atualiza um gateway de aplicação existente para suportar a firewall de aplicação web no modo de prevenção.

### <a name="step-1"></a>Passo 1

Navegue até ao portal do Azure, selecione um Gateway de aplicação existente.

![Criar o Gateway de aplicação][1]

### <a name="step-2"></a>Passo 2

Clique em **configuração** e atualizar as definições de gateway da aplicação. Quando terminar clique em **Guardar**

As definições para atualizar um gateway de aplicação existente para suportar a firewall de aplicação web são:

- **Camada** - a camada selecionada tem de ser **WAF** para suportar a firewall de aplicação web
- **Tamanho SKU** - esta definição é o tamanho do gateway aplicação com firewall de aplicação web, opções disponíveis são (**média** e **grande**).
- **Estado da firewall** - esta definição desativa ou permite firewall de aplicação web.
- O **modo de firewall** - esta definição está como firewall de aplicação web lida com tráfego malicioso. Modo de **detecção** apenas os registos de eventos, onde o modo de **prevenção** regista os eventos e deixa o tráfego malicioso.

![definições básicas da Mostrar pá][2]

>[AZURE.NOTE] Para ver registos de firewall da aplicação web, tem de estar ativados diagnósticos e ApplicationGatewayFirewallLog selecionada. Pode ser escolhida uma contagem de instância de 1 para fins de teste. É importante saber que qualquer contagem de instâncias em duas instâncias não é abrangido pela SLA e, por conseguinte, que não são recomendadas. Pequenas gateways não estão disponíveis quando utilizar firewall de aplicação web.

## <a name="create-an-application-gateway-with-web-application-firewall"></a>Criar um gateway aplicação com firewall de aplicação web

Este cenário irá:

- Crie um gateway de aplicação de firewall de aplicação web média com duas instâncias.
- Crie uma rede virtual denominada AdatumAppGatewayVNET com um bloco CIDR reservado de 10.0.0.0/16.
- Crie uma sub-rede denominada Appgatewaysubnet que utiliza 10.0.0.0/28 como o seu bloco CIDR.
- Configure um certificado para a passagem para da SSL.

### <a name="step-1"></a>Passo 1

Navegue até ao portal do Azure, clique em **Novo** > **funcionamento em rede** > **Gateway de aplicação**

![Criar o Gateway de aplicação][1-1]

### <a name="step-2"></a>Passo 2

Preencha as informações básicas sobre o gateway aplicação seguinte. Certifique-se de que escolher **WAF** como a camada. Quando estiver concluído, clique em **OK**

As informações necessárias para as definições básicas são:

- **Nome** - o nome para o gateway de aplicação.
- **Camada** - a camada do gateway aplicação, opções disponíveis são (**padrão** e **WAF**). Firewall de aplicação Web só está disponível na camada WAF.
- **Tamanho SKU** - esta definição é o tamanho do gateway aplicação, opções disponíveis são (**média** e **grande**).
- **Contagem de instâncias** - o número de instâncias, este valor deve ser um número entre **2** e **10**.
- **Grupo de recursos** - grupo de recursos para colocar em espera o gateway de aplicação, pode ser um grupo de recursos existente ou um novo.
- **Localização** - a região para o gateway de aplicação, é a mesma localização o grupo de recursos. *A localização é importante que a rede virtual e endereço IP público tem de ser na mesma localização, como o gateway*.

![definições básicas da Mostrar pá][2-2]

>[AZURE.NOTE] Pode ser escolhida uma contagem de instância de 1 para fins de teste. É importante saber que qualquer contagem de instâncias em duas instâncias não é abrangido pela SLA e, por conseguinte, que não são recomendadas. Pequenas gateways não são suportadas para cenários de firewall de aplicação web.

### <a name="step-3"></a>Passo 3

Assim que são definidas as definições básicas, o passo seguinte é definir a rede virtual para ser utilizado. A rede virtual aloja à aplicação que o gateway aplicação balanceamento de carga para.

Clique em **Escolher uma rede virtual** para configurar a rede virtual.

![Pá que mostra as definições de gateway de aplicação][3]

### <a name="step-4"></a>Passo 4

No pá **Rede Virtual escolher** , clique em **Criar novo**

Enquanto não explicado neste cenário, uma rede existente Virtual poderá estar selecionada neste momento.  Se for utilizada uma rede virtual existente, é importante saber que a rede virtual necessita de uma sub-rede vazia ou uma sub-rede de recursos para gateway de aplicação apenas para ser utilizado.

![Selecione pá de rede virtual][4]

### <a name="step-5"></a>Passo 5

Preencha as informações de rede pá a **Criar rede Virtual** conforme descrito na descrição do [cenário](#scenario) anterior.

![Criar pá de rede virtual com informações introduzidas][5]

### <a name="step-6"></a>Passo 6

Quando a rede virtual estiver criada, o passo seguinte é definir o front-end IP para o gateway de aplicação. Neste momento, a opção é entre um público ou um endereço IP privado para front-end. A escolha depende de se a aplicação de internet opostas ou internos apenas. Este cenário assume utilizando um endereço IP público. Para escolher um endereço IP privado, pode clicar no botão **privado** . É escolhido de um endereço IP atribuído automaticamente ou pode clicar na caixa de verificação de **Escolher um endereço IP privado específico** de introduzir um manualmente.

Clique em **Escolher um endereço IP público**. Se houver um endereço IP público existente pode ser escolhido neste momento, este cenário pode criar um novo endereço IP público. Clique em **Criar novo**

![Selecione pá de endereço IP público][6]

### <a name="step-7"></a>Passo 7

Em seguida, atribua um nome amigável ao endereço IP público e clique em **OK**

![Criar pá do endereço IP público][7]

### <a name="step-8"></a>Passo 8

Em seguida, pode configurar a configuração de escuta.  Se for utilizado **http** , não resta nada para configurar e pode ser clicado em **OK** . Para utilizar **https** ainda mais configuração é necessária.

Para utilizar **https**, é necessário um certificado. A chave privada do certificado de é necessária para exportar um PFX das necessidades de certificado para ser fornecido e a palavra-passe para o ficheiro.

Clique em **HTTPS**, clique no ícone de **pasta** junto a caixa de texto **PFX carregar certificado** .
Navegue para o ficheiro de certificado. pfx no sistema de ficheiros. Assim que estiver selecionado, dê um nome amigável ao certificado e escreva a palavra-passe para o ficheiro. pfx.

Depois de concluída clique em **OK** para rever as definições para o Gateway de aplicação.

![Secção de configuração de escuta no pá definições][8]

### <a name="step-9"></a>Passo 9

Configure as definições específicas do **WAF** .

- **Estado da firewall** - esta definição, WAF ativa ou desativar.
- **Modo de firewall** - esta definição determina que o WAF ações assume tráfego malicioso. Se for escolhido **deteção** , o tráfego só é registado.  Se for escolhido **prevenção** , o tráfego é tem sessão iniciado e parado com um não autorizado 403.

![definições da firewall aplicação Web][9]

### <a name="step-10"></a>Passo 10

Reveja a página de resumo e clique em **OK**.  Agora o gateway aplicação em fila de espera para cima e criado.

### <a name="step-12"></a>Passo 12

Assim que o gateway aplicação ter sido criado, navegue até ao mesmo no portal para continuar a configuração do gateway aplicação.

![Vista de recursos do Gateway de aplicação][10]

Estes passos criam um gateway básico de aplicações com as definições predefinidas para o escuta, conjunto de back-end, as definições de http back-end e regras. Pode modificar estas definições de acordo com a sua implementação assim que o aprovisionamento de ser efetuada com êxito

## <a name="next-steps"></a>Próximos passos

Saiba como configurar os registos de diagnóstico, para iniciar sessão os eventos que são detetados ou impedidos com Firewall de aplicação Web visitando [Diagnósticos do Gateway de aplicação](application-gateway-diagnostics.md)

Saiba como criar personalizado do Estado de funcionamento sondas acedendo a [criar uma sonda de estado de funcionamento personalizado](application-gateway-create-probe-portal.md)

Saiba como configurar SSL descarregar e tomar a desencriptação SSL dispendiosa desativar os servidores web acedendo a [Configurar SSL descarregar](application-gateway-ssl-portal.md)

<!--Image references-->
[1]: ./media/application-gateway-web-application-firewall-portal/figure1.png
[2]: ./media/application-gateway-web-application-firewall-portal/figure2.png
[1-1]: ./media/application-gateway-web-application-firewall-portal/figure1-1.png
[2-2]: ./media/application-gateway-web-application-firewall-portal/figure2-2.png
[3]: ./media/application-gateway-web-application-firewall-portal/figure3.png
[4]: ./media/application-gateway-web-application-firewall-portal/figure4.png
[5]: ./media/application-gateway-web-application-firewall-portal/figure5.png
[6]: ./media/application-gateway-web-application-firewall-portal/figure6.png
[7]: ./media/application-gateway-web-application-firewall-portal/figure7.png
[8]: ./media/application-gateway-web-application-firewall-portal/figure8.png
[9]: ./media/application-gateway-web-application-firewall-portal/figure9.png
[10]: ./media/application-gateway-web-application-firewall-portal/figure10.png
[scenario]: ./media/application-gateway-web-application-firewall-portal/scenario.png