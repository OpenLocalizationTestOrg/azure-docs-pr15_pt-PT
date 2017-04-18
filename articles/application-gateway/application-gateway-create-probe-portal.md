<properties
   pageTitle="Criar uma sonda personalizada para um gateway de aplicação, utilizando o portal | Microsoft Azure"
   description="Saiba como criar uma sonda personalizada para o Gateway de aplicação utilizando o portal"
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

# <a name="create-a-custom-probe-for-application-gateway-by-using-the-portal"></a>Criar uma sonda personalizada para o Gateway aplicação utilizando o portal

> [AZURE.SELECTOR]
- [Portal do Azure](application-gateway-create-probe-portal.md)
- [Azure PowerShell do Gestor de recursos](application-gateway-create-probe-ps.md)
- [Azure PowerShell clássico](application-gateway-create-probe-classic-ps.md)

[AZURE.INCLUDE [azure-probe-intro-include](../../includes/application-gateway-create-probe-intro-include.md)]

## <a name="scenario"></a>Cenário

Cenário que se segue vai pela criação de uma sonda de estado de funcionamento personalizado num gateway de aplicação existente.
O cenário assume que já tenha seguido os passos para [criar um Gateway de aplicação](application-gateway-create-gateway-portal.md).

## <a name="createprobe"></a>Criar a sonda

Sondas estão configuradas de um processo de dois passos através do portal. O primeiro passo é criar a sonda de, em seguida, adicionar a sonda para as definições de http back-end do gateway aplicação.

### <a name="step-1"></a>Passo 1

Navegue para http://portal.azure.com e selecione um gateway de aplicação existente.

![Descrição geral de Gateway de aplicação][1]

### <a name="step-2"></a>Passo 2

Clique em **sondas** e clique no botão **Adicionar** para adicionar uma nova sonda.

![Adicionar sonda pá com informações preenchidas][2]

### <a name="step-3"></a>Passo 3

Preencha as informações necessárias para a sonda e quando estiver concluído, clique em **OK**.

- **Nome** - este é um nome amigável para a sonda acessível no portal.
- **Anfitrião** - este é o nome de anfitrião é utilizado para a sonda. Aplicável apenas quando o site com várias está configurado no Gateway de aplicação, caso contrário, utiliza '127.0.0.1'. Este é diferente do nome do anfitrião VM.
- **Caminho** - o resto do url completo para a sonda personalizado. Um caminho válido começa com '/'
- **Intervalo (seg.)** - com que frequência a sonda é executada para verificar a existência de estado de funcionamento. Não é recomendado para definir o canto inferior a 30 segundos.
- **Limite de tempo (seg.)** - a quantidade de tempo que a sonda de espera antes de temporização saída. Intervalo de tempo limite tem de estar suficientemente alta para que uma chamada de http pode ser efetuada para garantir que a página de estado de funcionamento de back-end está disponível.
- **Limiar danificado** - número de tentativas falhadas sejam considerados danificado. Um limite de 0 significa que, se uma falha de verificação do Estado de funcionamento back-end será determinado danificado imediatamente.

> [AZURE.IMPORTANT] o nome do anfitrião não é o nome do servidor. Este é o nome do anfitrião do virtual em execução no servidor de aplicações. A sonda de é enviada para http://(host name):(port from httpsetting)/urlPath

![definições de configuração de sonda][3]

## <a name="add-probe-to-the-gateway"></a>Adicionar sonda para o gateway

Agora que a sonda de ter sido criada, é hora para adicioná-lo para o gateway. Definições de sonda estão definidas nas definições de http back-end do gateway aplicação.

### <a name="step-1"></a>Passo 1

Clique nas **definições de HTTP** do gateway aplicação e, em seguida, clique nas definições de http de back-end atual, na janela para ativar a pá de configuração.

![janela de definições de HTTPS][4]

### <a name="step-2"></a>Passo 2

No pá de definições **appGatewayBackEndHttp** , clique em **sonda personalizada de utilização** e selecione a sonda criada na secção [criar a sonda de](#createprobe) .
Quando tiver terminado, clique em **OK** e as definições serão aplicadas.

![appgatewaybackend pá de definições][5]

A sonda de predefinido verifica a predefinição de acesso para a aplicação web. Agora que uma sonda personalizada ter sido criada, o gateway aplicação utiliza o caminho personalizado definido para monitorizar o estado de funcionamento para o back-end seleccionado. Com base nos critérios que tenha sido definida, o gateway aplicação verifica o ficheiro especificado no sonda. Se a chamada para o anfitrião: porta / caminho não devolver uma resposta de estado de Http 200 OK, o servidor é disponibilizado fora rotação, depois do limiar danificado for atingido. A pesquisar continua na instância danificada para determinar quando é que se torne Saudável novamente. Assim que a instância for adicionada novamente ao agrupamento de servidor saudável tráfego começa a fluir à mesma novamente e a pesquisar na instância continua no intervalo de tempo especificado do utilizador como habitualmente.


## <a name="next-steps"></a>Próximos passos

Para saber como configurar SSL descarregar com Azure aplicação Gateway consulte o artigo [Configurar SSL descarregar](application-gateway-ssl-portal.md)

[1]: ./media/application-gateway-create-probe-portal/figure1.png
[2]: ./media/application-gateway-create-probe-portal/figure2.png
[3]: ./media/application-gateway-create-probe-portal/figure3.png
[4]: ./media/application-gateway-create-probe-portal/figure4.png
[5]: ./media/application-gateway-create-probe-portal/figure5.png