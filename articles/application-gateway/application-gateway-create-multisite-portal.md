<properties
   pageTitle="Configurar um gateway de aplicação existente para hospedagem de vários sites no portal do Azure | Microsoft Azure"
   description="Esta página fornece instruções sobre como configurar um gateway de aplicação Azure existente para várias aplicações web no mesmo gateway com o portal do Azure de alojamento."
   documentationCenter="na"
   services="application-gateway"
   authors="georgewallace"
   manager="carmonm"
   editor="tysonn"/>
<tags
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/25/2016"
   ms.author="gwallace"/>


# <a name="configure-an-existing-application-gateway-for-hosting-multiple-web-applications"></a>Configurar um gateway de aplicação existente para alojar várias aplicações web

> [AZURE.SELECTOR]
- [Portal do Azure](application-gateway-create-multisite-portal.md)
- [Azure PowerShell do Gestor de recursos](application-gateway-create-multisite-azureresourcemanager-powershell.md)

Vários sites alojamento permite-lhe implementar mais do que uma aplicação web no mesmo gateway de aplicação. Depende de presença de cabeçalho de anfitrião no pedido de HTTP recebido, para determinar qual escuta seria receber tráfego. A escuta direciona, em seguida, o tráfego para conjunto de dados back-end adequado, tal como está configurado na definição de regras do gateway. Nas aplicações de web SSL ativado, gateway aplicação baseia-se a extensão de nome de servidor indicação (SNI) para escolher a escuta correta para o tráfego da web. Uma utilização comum para alojamento de sites vários consiste em carregar saldo pedidos para domínios de web diferente para agrupamentos de servidor de back-end diferente. Da mesma forma vários subdomínios do domínio de raiz da mesma também podem ser alojados no mesmo gateway de aplicação.

## <a name="scenario"></a>Cenário

No exemplo seguinte, gateway aplicação é servir o tráfego para contoso.com e fabrikam.com com dois conjuntos de servidor back-end: contoso conjunto do servidor e fabrikam conjunto de servidor. Configuração semelhante pode ser utilizadas para subdomínios anfitrião como app.contoso.com e blog.contoso.com.

![cenário realizado][multisite]

## <a name="before-you-begin"></a>Antes de começar

Este cenário adiciona suporte de múltiplos site para um gateway de aplicação existente. Para poder concluir este cenário cenário um gateway de aplicação existente tem de estar disponíveis para configurar. Aceda a [criar um gateway utilizando o portal de aplicação](./application-gateway-create-gateway-portal.md) para saber como criar um gateway básico de aplicações no portal.

## <a name="requirements"></a>Requisitos de

- **Conjunto do servidor de back-end:** A lista de endereços IP dos servidores de back-end. Endereços IP listados quer devem pertencem à sub-rede rede virtual ou devem ser VIP/IP público. Também pode ser utilizado o FQDN.
- **Definições de conjunto de dados back-end servidor:** Cada conjunto tem definições como porta, protocolo e afinidade com base em cookies. Estas definições estão associadas a um agrupamento e são aplicadas a todos os servidores dentro do conjunto.
- **Porta front-end:** Esta porta é a pública porta que é aberta no gateway aplicação. Tráfego esta porta de acertos e, em seguida, redireccionado para um dos servidores de back-end.
- **Escuta:** A escuta tem uma porta front-end, um protocolo (Http ou Https, estes valores são entre maiúsculas e minúsculas) e o nome do certificado SSL (se configurar SSL descarregar). Para gateways aplicação ativada múltiplos sites, nome do anfitrião e indicadores SNI também são adicionados.
- **Regra:** A regra vincula automaticamente o serviço de escuta, o conjunto de servidor back-end e define qual conjunto de servidor back-end o tráfego deve ser direcionado para quando acertos de uma determinada escuta.
- **Certificados:** Cada escuta requer um certificado exclusivo, neste exemplo 2 listeners são criadas para múltiplos sites. Dois. pfx certificados e as palavras-passe para os mesmos têm de ser criado.

## <a name="create-an-application-gateway"></a>Criar um gateway de aplicação

Seguem-se os passos necessários para Atualize o gateway de aplicação:

1. Crie conjuntos de dados back-end a utilizar para cada site.
2. Crie uma nova escuta para cada gateway de aplicação do site irá de suporte.
3. Crie regras para mapear cada escuta com back-end adequado.

## <a name="create-back-end-pools-for-each-site"></a>Criar conjuntos de dados back-end para cada site

Um agrupamento de back-end para cada site gateway nessa aplicação irá suporte é necessária, neste caso 2 será criada, uma para contoso11.com e outra para fabrikam11.com.

### <a name="step-1"></a>Passo 1

Navegue para um gateway de aplicação existente no portal do Azure (https://portal.azure.com). Selecionar **conjuntos de dados back-end** e clique em **Adicionar**

![Adicionar conjuntos de dados back-end][7]

### <a name="step-2"></a>Passo 2

Preencha as informações para o conjunto de dados back-end **pool1**, adicionando os endereços ip ou FQDNs para os servidores de back-end e clique em **OK**

![definições de pool1 do conjunto de dados back-end][8]

### <a name="step-3"></a>Passo 3

Na pá conjuntos de dados back-end clique em **Adicionar** para adicionar um conjunto de dados back-end adicionais **pool2**, adicionando os endereços ip ou FQDNS para os servidores de back-end e clique em **OK**

![definições de pool2 do conjunto de dados back-end][9]

### <a name="create-listeners-for-each-back-end"></a>Criar listeners para cada back-end

Aplicação Gateway baseia-se nos cabeçalhos de anfitrião HTTP 1.1 para alojar o Web site mais do que um no mesmo endereço IP público e porta. A escuta básica criada no portal do não contém esta propriedade.

### <a name="step-1"></a>Passo 1

Clique em **Listeners** no gateway aplicação existente e clique em **múltiplos sites** para adicionar a primeira escuta.

![Pá de descrição geral de listeners][1]

### <a name="step-2"></a>Passo 2

Preencha as informações a escutar, neste exemplo terminação SSL está configurada, criar uma nova porta de front-end. Carregue o certificado. pfx a ser utilizado para cessação SSL. A única diferença neste pá em comparação comparada o pá escuta básicas padrão é o nome do anfitrião.

![Pá de propriedades de escuta][2]

### <a name="step-3"></a>Passo 3

Clique em **múltiplos sites** e crie outra escuta conforme descrito no passo anterior para o segundo site. Certifique-se utilizar um certificado diferente a segunda escutar. A única diferença neste pá em comparação comparada o pá escuta básicas padrão é o nome do anfitrião. Preencha as informações a escutar e clique em **OK**.

![Pá de propriedades de escuta][3]

> [AZURE.NOTE] Criação de listeners no portal do Azure para gateway aplicação for uma tarefa longa, poderá demorar algum tempo para criar os dois listeners neste cenário. Quando concluir a apresentação de listeners no portal do conforme visto na seguinte imagem.

![Descrição geral de escuta][4]

### <a name="create-rules-to-map-listeners-to-backend-pools"></a>Criar regras para mapear listeners para conjuntos de dados back-end

### <a name="step-1"></a>Passo 1

Navegue para um gateway de aplicação existente no portal do Azure (https://portal.azure.com). Selecione **regras** e selecione o de regra predefinido **regra1** existente e clique em **Editar**.

### <a name="step-2"></a>Passo 2

Preencha a pá regras conforme visto na seguinte imagem. Escolher a primeira escuta e o primeiro conjunto e clicar em **Guardar** quando terminar.

![Editar regra existente][6]

### <a name="step-3"></a>Passo 3

Clique em **regra básica** para criar a regra 2º. Preencha o formulário com a segunda escuta e o segundo conjunto de back-end e clique em **OK** para guardar.

![Adicionar pá regra básicas][10]

Este procedimento conclui configurar um gateway de aplicação existente com o suporte de múltiplos site através do portal do Azure.

## <a name="next-steps"></a>Próximos passos

Saiba como pode proteger os seus Web sites com a [Aplicação de Gateway - Firewall de aplicação Web](application-gateway-webapplicationfirewall-overview.md)

<!--Image references-->
[1]: ./media/application-gateway-create-multisite-portal/figure1.png
[2]: ./media/application-gateway-create-multisite-portal/figure2.png
[3]: ./media/application-gateway-create-multisite-portal/figure3.png
[4]: ./media/application-gateway-create-multisite-portal/figure4.png
[5]: ./media/application-gateway-create-multisite-portal/figure5.png
[6]: ./media/application-gateway-create-multisite-portal/figure6.png
[7]: ./media/application-gateway-create-multisite-portal/figure7.png
[8]: ./media/application-gateway-create-multisite-portal/figure8.png
[9]: ./media/application-gateway-create-multisite-portal/figure9.png
[10]: ./media/application-gateway-create-multisite-portal/figure10.png
[multisite]: ./media/application-gateway-create-multisite-portal/multisite.png