<properties
    pageTitle="Como configurar um ambiente de aplicação de serviço | Microsoft Azure"
    description="Configuração, gestão e monitorização de ambientes de serviço de aplicação"
    services="app-service"
    documentationCenter=""
    authors="ccompy"
    manager="stefsch"
    editor=""/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/17/2016"
    ms.author="ccompy"/>


# <a name="configuring-an-app-service-environment"></a>Configurar um ambiente de aplicação de serviço #

## <a name="overview"></a>Descrição geral ##

De alto nível, um ambiente de serviço de aplicação do Azure consiste em vários componentes principais:

- Recursos de cluster que estiver a executar o serviço de ambiente de serviço de aplicação alojado
- Armazenamento
- Uma base de dados
- Classic(V1) ou recurso Manager(V2) Azure rede Virtual (VNet) 
- Uma sub-rede com o serviço de ambiente de serviço de aplicação alojados em execução no mesmo

### <a name="compute-resources"></a>Calcular a recursos

Utilize os recursos de cluster para sua agrupamentos de quatro recursos.  Cada ambiente de serviço de aplicação (Auxiliar) tem um conjunto de extremidades da frente e três agrupamentos de trabalho possíveis. Não é necessário utilizar todos os conjuntos de dados de trabalho de três – se pretender, só pode utilizar um ou dois.

Os anfitriões no agrupamentos de recursos (extremidades da frente e os trabalhadores) não são acessíveis diretamente a inquilinos. Não é possível utilizar o protocolo de ambiente de trabalho remoto (RDP) para ligar aos mesmos, alterar a sua aprovisionamento ou funcionar como um administrador na-los.

Pode definir a quantidade de agrupamento de recursos e tamanho. Num Auxiliar, tem quatro opções de tamanho, que estão identificados como P1 através de E4. Para obter detalhes sobre esses tamanhos e os respetivos preços, consulte o artigo [aplicação de serviço de preços](../app-service/app-service-value-prop-what-is.md).
Alterar o tamanho ou a quantidade é denominado uma operação de escala.  Operação de apenas uma escala pode estar em curso, uma vez.

**Parte da frente termina**: as extremidades da frente são os pontos finais HTTP/HTTPS para as aplicações que são mantidas no seu Auxiliar. Não se depare das cargas de trabalho no fim da parte da frente.

- Um Auxiliar começa com duas P2s, que é suficiente para Dev Center/testar cargas de trabalho e cargas de trabalho de produção feixe. Recomendamos vivamente P3s para definições de impressão para cargas de trabalho de produção muito carregado.
- Para definições de impressão para cargas de trabalho de produção muito carregado, recomendamos que tem pelo menos quatro P3s para garantir que existem suficientes extremidades frente executar quando ocorre a manutenção agendada. Atividades de manutenção agendada moldados para baixo uma front-end, uma vez. Isto reduz global capacidade front-end disponível durante atividades de manutenção.
- Instantaneamente não pode adicionar uma nova instância front-end. Podem pegar em entre 2 a 3 horas a aprovisionar.
- Para mais ajuste de escala, deverá monitorizar a percentagem de CPU, percentagem de memória e métricas de pedidos ativos para o conjunto de dados front-end. Se as percentagens CPU ou de memória acima 70 por cento quando a ser executado P3s, adicione mais extremidades frente. Se o valor de pedidos ativos calcula a média de para 15.000 a 20.000 pedidos por front-end, também deve adicionar mais extremidades frente. É o objetivo geral manter 70% de memória e CPU percentagens abaixo e pedidos ativos calcular a média de para abaixo 15.000 pedidos por parte da frente terminar se estiver a executar o P3s.  

**Os trabalhadores**: os trabalhadores estão onde as suas aplicações realmente executar. Quando colocar o seu plano de aplicação de serviço, que utiliza o trabalhadores no conjunto de trabalho associados.

- Não pode adicionar os trabalhadores instantaneamente. Estes pode tomar a partir de 2 a 3 horas a aprovisionar, independentemente de quantas estão a ser adicionados.
- Dimensionar o tamanho de um recurso de cluster para qualquer agrupamento entrarão em 2 a 3 horas por actualização de domínio. Existem 20 domínios de atualizar um Auxiliar. Se dimensionado o tamanho de cluster de um conjunto de trabalho com 10 instâncias, pode demorar entre 20 a 30 horas para concluir.
- Se alterar o tamanho dos recursos cluster que são utilizados num conjunto de trabalho, vai originar frios inícios das aplicações que estão a ser executada nesse agrupamento de trabalho.

A maneira mais para alterar o tamanho de recurso cluster de um conjunto de trabalho que não está em execução de aplicações é:

- Dimensione para baixo a contagem de instância 0. Irá demorar cerca de 30 minutos para retirar as instâncias.
- Selecione o novo tamanho de cluster e o número de instâncias. A partir daqui, irá demorar entre 2 a 3 horas para concluir.

Se as suas aplicações introduzir um tamanho de recurso cluster maior, não é possível tira partido das orientações anterior. Em vez de alterar o tamanho do agrupamento de trabalho que está a alojar nessas aplicações, pode preencher outro conjunto de trabalho com os trabalhadores do tamanho pretendido e mova as aplicações para esse agrupamento.

- Crie as ocorrências adicionais do tamanho necessário cluster no outro conjunto de trabalho. Isto irá demorar de 2 a 3 horas para concluir.
- Reatribua os planos de aplicação de serviço que estiver a alojar as aplicações que necessitam de um tamanho de maior para o conjunto de trabalho recentemente configurado. Esta é uma operação rápida que deverá ter menos de um minuto para concluir.  
- Dimensione para baixo o primeiro conjunto de trabalho se de que não são precisas às instâncias não utilizadas. Esta operação demora cerca de 30 minutos a concluir.

**Autoscaling**: uma das ferramentas de que o podem ajudar a gerir o seu cluster o consumo de recursos é autoscaling. Pode utilizar autoscaling para front-end ou conjuntos de dados de trabalho. Pode fazer coisas como aumentar as instâncias de qualquer tipo de agrupamento de manhã e reduzi-lo à noite. Ou talvez pode adicionar instâncias quando o número de trabalhadores estão disponíveis num conjunto de trabalho desce abaixo de um determinado limiar.

Se pretender definir regras de autoscale à volta de métricas de agrupamento de recursos de cluster, em seguida, tenha em consideração o tempo que necessita de aprovisionamento. Para obter mais detalhes sobre autoscaling ambientes de serviço de aplicação, consulte o artigo [como configurar autoscale num ambiente do serviço de aplicação][ASEAutoscale].

### <a name="storage"></a>Armazenamento

Cada Auxiliar está configurado com 500 GB de armazenamento. Este espaço é utilizado em todas as aplicações na Auxiliar. Este espaço de armazenamento é uma parte da Auxiliar e atualmente não pode ser mudado para utilizar o seu espaço de armazenamento. Se estiver a fazer ajustes à sua rede virtual encaminhamento ou segurança, necessita ainda permitir acesso ao armazenamento do Windows Azure – ou o Auxiliar não funciona.

### <a name="database"></a>Base de dados

A base de dados mantém as informações que define o ambiente, bem como os detalhes sobre as aplicações que estiver a executar o dentro da mesma. Este é demasiado uma parte da subscrição contidos Azure. Não é algo que tem uma capacidade direta para manipular. Se estiver a fazer ajustes à sua rede virtual encaminhamento ou segurança, necessita ainda permitir acesso ao SQL Azure – ou o Auxiliar não funciona.

### <a name="network"></a>Rede

O que é utilizado com o Auxiliar VNet pode ser um que efetuou quando criou o Auxiliar ou um que efetuou antecedência. Quando cria a sub-rede durante a criação de Auxiliar, força a Auxiliar pertençam ao mesmo grupo de recursos que a rede virtual. Se precisar de grupo de recursos utilizado pelo seu Auxiliar para ser diferente da sua VNet, em seguida, tem de criar o seu Auxiliar através de um modelo de Gestor de recursos.

Existem algumas restrições da rede virtual que é utilizado para um Auxiliar:
 
- A rede virtual tem de ser uma rede virtual regional.
- Não existem tem de estar numa sub-rede com 8 ou mais endereços onde o Auxiliar é implementada.
- Depois de uma sub-rede é utilizada para alojar uma Auxiliar, o intervalo de endereços da sub-rede não pode ser alterado. Por esta razão, recomendamos que a sub-rede contém, pelo menos, 64 endereços para acomodar qualquer crescimento Auxiliar futuro.
- Podem existir nada mais sub-rede mas o Auxiliar.

Ao contrário o serviço alojado que contém o Auxiliar, a [rede virtual] [ virtualnetwork] e de sub-rede estão sob o controlo de utilizador.  Pode administrar a sua rede virtual através da IU de rede Virtual ou PowerShell.  Um Auxiliar pode ser implementada num Classic ou VNet do Gestor de recursos.  O portal e API experiências são ligeiramente diferentes entre clássica e VNets do Gestor de recursos, mas a experiência de Auxiliar é a mesma.

O que é utilizado para alojar uma Auxiliar VNet pode utilizar qualquer um dos endereços de IP de RFC1918 privados ou pode utilizar endereços IP públicos.  Se pretender utilizar um intervalo IP que não é abrangido pelo RFC1918 (10.0.0.0/8, 172.16.0.0/12, 192.168.0.0/16), em seguida, tem de criar a sua sub-rede e VNet para ser utilizado por sua Auxiliar antes da data criação Auxiliar.

Uma vez que esta capacidade coloca o serviço de aplicação do Azure na sua rede virtual, significa que as aplicações que são alojadas no seu Auxiliar agora podem aceder a recursos que são disponibilizados através de ExpressRoute ou redes privadas virtuais (VPNs) site para o site diretamente. As aplicações que estão dentro do ambiente de serviço de aplicação não necessitam de funcionalidades adicionais de funcionamento em rede para aceder a recursos disponíveis para a rede virtual que está a alojar o seu ambiente de serviço de aplicação. Isto significa que não precisa de utilize VNET integração ou ligações de híbrido para ir para recursos em ou ligado à sua rede virtual. Ainda pode utilizar essas funcionalidades ambos apesar para aceder aos recursos em redes que não estão ligadas à sua rede virtual.

Por exemplo, pode utilizar VNET integração para integrar com uma rede virtual que está na sua subscrição, mas não está ligada à rede virtual que está a sua Auxiliar. Ainda também pode utilizar híbrido ligações para aceder aos recursos que estão no noutras redes, tal como pode normalmente.  

Se tiver a rede virtual configurada com uma VPN ExpressRoute, deverá conhecer de algumas das necessidades de encaminhamento que tem um Auxiliar. Existem algumas configurações de encaminhar definidos pelo utilizador (UDR) que não são compatíveis com um Auxiliar. Para obter mais detalhes sobre como executar uma Auxiliar numa rede virtual com ExpressRoute, consulte o artigo [executar um ambiente do serviço de aplicação numa rede virtual com ExpressRoute][ExpressRoute].

#### <a name="securing-inbound-traffic"></a>Proteger o tráfego de entrada

Existem dois métodos principais para controlar o tráfego de entrada para a sua Auxiliar.  Pode utilizar a rede Groups(NSGs) de segurança para controlar que IP endereços podem aceder ao seu Auxiliar conforme é aqui descrito [como controlar o tráfego de entrada num ambiente do serviço de aplicação](app-service-app-service-environment-control-inbound-traffic.md) e também pode configurar o seu Auxiliar com um Balancer(ILB) carga interno.  Estas funcionalidades podem também ser utilizadas em conjunto se quiser restringir o acesso com NSGs para sua Auxiliar ILB.

Quando cria uma Auxiliar, criará uma VIP na sua VNet.  Existem dois tipos de VIP, internos e externos.  Quando cria uma Auxiliar com um VIP externo as suas aplicações no seu Auxiliar será acessíveis através de um endereço IP encaminhável da internet. Quando seleciona interno seu Auxiliar será configurado com uma ILB e não serão diretamente internet acessíveis.  Um Auxiliar ILB ainda requer um VIP externo, mas só é utilizado para o acesso de gestão e manutenção Azure.  

Durante a criação de ILB Auxiliar fornecer o subdomínio utilizado pela Auxiliar ILB e terá que gerir os seus próprios DNS para o subdomínio que especificar.  Uma vez que defina o nome do subdomínio que também necessárias para gerir o certificado utilizado para acesso HTTPS.  Após a criação de Auxiliar lhe for pedido para fornecer o certificado.  Para saber mais sobre como criar e utilizar um Auxiliar ILB lidos [com um balanceador de carga interno com um ambiente do serviço de aplicação][ILBASE]. 


## <a name="portal"></a>Portal

Pode gerir e monitorizar o seu ambiente de serviço de aplicação ao utilizar a interface de utilizador no portal do Azure. Se tiver um Auxiliar, em seguida, está provável que veja o símbolo de aplicação de serviço na sua barra lateral. Este símbolo é utilizado para representar a aplicação ambientes de serviço no portal do Azure:

![Símbolo de ambiente de serviço de aplicação][1]

Para abrir a IU que lista todos os ambientes de serviço de aplicação, pode utilizar o ícone ou selecione as divisas (">" símbolo) na parte inferior da barra lateral para selecionar os ambientes de serviço de aplicação. Ao selecionar uma das ASEs listados, abra a IU que é utilizada para monitorizar e geri-lo.

![IU para monitorizar e gerir o seu ambiente de serviço de aplicação][2]

Este pá primeiro mostra algumas propriedades da sua Auxiliar, juntamente com um gráfico métrico por agrupamento de recursos. Também são algumas das propriedades que são apresentadas no bloco de **Essentials** hiperligações em que serão aberto o o pá que está associado. Por exemplo, pode selecionar o nome de **Rede Virtual** para abrir o IU associada à rede virtual que está a funcionar no seu Auxiliar. **Planos do serviço de aplicação** e **Apps** abrem o pás estes itens que estão no seu Auxiliar da lista.  

### <a name="monitoring"></a>Monitorização

Os gráficos permitem-lhe ver uma variedade de métricas de desempenho no cada grupo de recursos. Para o conjunto de dados front-end, pode monitorizar memória e CPU média. Para agrupamentos de trabalho, pode monitorizar a quantidade que é utilizada e a quantidade de que está disponível.

Vários aplicação de serviço de planos podem tornar a utilização de um conjunto de trabalho para os trabalhadores. A carga de trabalho não é distribuída da mesma forma, como, com os servidores front-end, por isso, utilização de memória e CPU não fornecer muito impedir informações úteis. É mais importante controlar quantas trabalhadores que utilizou e estiverem disponíveis – especialmente se faz a gestão este sistema para outras pessoas utilizarem.  

Também pode utilizar todas as métricas que podem ser controladas nos gráficos configurar alertas. Configurar alertas aqui funciona da mesma como qualquer outro local na aplicação de serviço. Pode definir um alerta a partir da peça de IU **alertas** ou de explorar em qualquer métricas IU e ao selecionar **Adicionar alerta**.

![Métricas IU][3]

As métricas abordados apenas são as métricas de ambiente de serviço de aplicação. Também existem métricas que estão disponíveis ao nível do plano de serviço de aplicação. Este é onde a monitorização de memória e CPU faz sentido muitas.

Num Auxiliar, todos os planos do serviço de aplicação estão dedicados de aplicação de serviço de planos. Isto significa que as aplicações apenas que estiverem em execução no anfitriões atribuídos a que o plano de serviço de aplicação estão as aplicações nesse plano de serviço de aplicação. Para ver detalhes no seu plano de serviço de aplicação, coloque o seu plano de serviço de aplicação a partir de qualquer uma das listas na IU Auxiliar ou de **planos do serviço de aplicação procure** (que apresenta todos-los).   

### <a name="settings"></a>Definições

Dentro de pá Auxiliar, existe uma secção de **Definições** que contenha várias funcionalidades importantes:

**Definições de** > **Propriedades**: A **definições de** pá aberto automaticamente quando trazer o seu pá Auxiliar. Na parte superior é **Propriedades**. Existem um número de itens aqui que são redundantes para ver no **Essentials**, mas o que é muito útil é **O endereço IP Virtual**, bem como **Saída endereços IP**.

![Pá definições e propriedades][4]

**Definições de** > **Endereços IP**: ao criar uma aplicação de IP Secure Sockets Layer (SSL) no seu Auxiliar, precisa de um endereço de IP SSL. Para obter um, o Auxiliar tem endereços de IP SSL que é proprietário de que podem ser atribuídos. Quando é criado um Auxiliar, este tenha um endereço de IP SSL para esse efeito, mas pode adicionar mais. Existe um encargo para endereços de SSL de IP adicionais, conforme apresentado na [Aplicação de serviço de preços] [ AppServicePricing] (na secção em ligações SSL). O preço adicional é o preço de IP SSL.

**Definições de** > **Front-End agrupamento** / **Agrupamentos de trabalho**: cada uma destes pás de agrupamento de recursos oferece a capacidade de ver as informações apenas nesse grupo de recursos, além de controlos para dimensionar totalmente nesse grupo de recursos.  

O separador base para cada grupo de recursos fornece um gráfico com métricas para esse grupo de recursos. Tal como faria com os gráficos de pá Auxiliar, pode ir para o gráfico e configurar alertas conforme pretender. Definir um alerta a partir de pá Auxiliar para um agrupamento de recursos específico é que a mesma coisa como fazê-lo a partir do grupo de recursos. A partir de pá de **definições do** conjunto de trabalho, tiver acesso a todas as aplicações ou planos de aplicação de serviço que estiver a executar este conjunto de trabalho.

![Definições do agrupamento de trabalho da IU][5]

### <a name="portal-scale-capabilities"></a>Capacidades de escala de portal  

Existem três operações de escala:

- Alterar o número de endereços IP a Auxiliar que estão disponíveis para utilização de IP SSL.
- Alterar o tamanho do recurso cluster que é utilizado num agrupamento de recursos.
- Alterar o número de recursos de cluster que são utilizados num agrupamento de recursos, manualmente ou através de autoscaling.

No portal do, existem três formas para controlar quantas servidores que tem no seu agrupamentos de recursos:

- Uma operação de escala a partir do principal pá Auxiliar na parte superior. Pode fazer escala múltiplos alterações à configuração para conjuntos de dados front-end e trabalhador. São todas aplicadas como uma única operação.
- Uma operação de escala manual a partir do pá de **escala** de conjunto de dados do recurso individual, que se encontra em **Definições**.
- Autoscaling, que definiu a partir do pá de **escala** de agrupamento de recurso individual.

Para utilizar a operação de escala no pá a Auxiliar, arraste o controlo de deslize para a quantidade pretendida e guarde. Esta UI também suporta a alterar o tamanho.  

![Escala da IU][6]

Para utilizar as capacidades de manual ou autoscale um agrupamento de recursos específico, aceda a **Definições** > **Front-End agrupamento** / **Agrupamentos de trabalho** , conforme adequado. Em seguida, abra o o agrupamento de que pretende alterar. Aceda a **Definições** > **escala saída** ou **definições da** > **Dimensionar para cima**. A **Escala de saída** pá permite-lhe controlar a quantidade de instância. **Escala de cima** permite-lhe controlar o tamanho do recurso.  

![Definições de escala da IU][7]

## <a name="fault-tolerance-considerations"></a>Considerações de tolerância a falhas

Pode configurar um ambiente do serviço de aplicação para utilizar até 55 cluster total recursos. Esses 55 cluster recursos de, 50 apenas podem ser utilizados para cargas de trabalho do anfitrião. O motivo para isto é uma tarefa dupla. Existe um mínimo de 2 recursos de cluster front-end.  Que deixa até 53 para suportar a alocação de conjunto de trabalho. Para fornecer tolerância a falhas, tem de ter um recurso de cluster adicionais que está atribuído de acordo com as seguintes regras:

- Cada conjunto de trabalho necessita de pelo menos 1 recurso de cluster adicionais que não está disponível para ser atribuídas uma carga de trabalho.
- Quando acede a quantidade de recursos de cluster num conjunto de dados trabalhador acima de um determinado valor, em seguida, outro recurso cluster é necessário para tolerância a falhas. Não é as maiúsculas/minúsculas no conjunto de front-end.

Dentro de qualquer conjunto de trabalho única, os requisitos de tolerância a falhas destinam-se que um determinado valor de X recursos atribuídos a um conjunto de trabalho:

- Se X for entre 2 e 20, a quantidade de recursos de cluster utilizáveis que pode utilizar das cargas de trabalho é X-1.
- Se X for entre 21 e 40, a quantidade de recursos de cluster utilizáveis que pode utilizar das cargas de trabalho é X-2.
- Se X for entre 41 e 53, a quantidade de recursos de cluster utilizáveis que pode utilizar das cargas de trabalho é X-3.

Os requisitos de espaço mínimo tem 2 servidores front-end e trabalhadores de 2.  Com as declarações acima, em seguida, aqui estão alguns exemplos para esclarecer:  

- Se tiver 30 trabalhadores num único conjunto de dados, em seguida, 28-las podem ser utilizados para cargas de trabalho do anfitrião.
- Se tiver 2 trabalhadores num único conjunto de dados, em seguida, 1 podem ser utilizados para cargas de trabalho do anfitrião.
- Se tiver 20 trabalhadores num único conjunto de dados, em seguida, 19 podem ser utilizados para das cargas de trabalho do anfitrião.  
- Se tiver 21 trabalhadores num único conjunto de dados, em seguida, continua a ser 19 apenas pode ser utilizados para das cargas de trabalho do anfitrião.  

O aspecto de tolerância a falhas é importante, mas terá de mantê-lo em mente à medida que dimensionar acima certos limiares. Se quiser adicionar mais capacidade passar da 20 instâncias, em seguida, aceda a 22 ou superior porque, 21 não adicionar qualquer mais capacidade. O mesmo se aplica aceder acima 40, onde o número seguinte que adiciona capacidade é 42.  

## <a name="deleting-an-app-service-environment"></a>Eliminar um ambiente de aplicação de serviço ##

Se pretende eliminar um ambiente do serviço de aplicação, em seguida, simplesmente utilize a ação de **Eliminar** no topo da pá ambiente de serviço de aplicação. Ao fazê-lo, será solicitado que introduza o nome do seu ambiente de serviço de aplicação para confirmar que realmente quer fazer. Tenha em atenção que quando elimina um ambiente do serviço de aplicação, elimine todo o conteúdo também dentro da mesma.  

![Eliminar um ambiente de aplicação de serviço da IU][9]  

## <a name="getting-started"></a>Introdução

Para começar com ambientes de serviço de aplicação, consulte o artigo [como criar um ambiente do serviço de aplicação](app-service-web-how-to-create-an-app-service-environment.md).

Para mais informações sobre a plataforma de aplicação de serviço do Azure, consulte o artigo [Aplicação de serviço de Azure](../app-service/app-service-value-prop-what-is.md).

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]

<!--Image references-->
[1]: ./media/app-service-web-configure-an-app-service-environment/ase-icon.png
[2]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-aseblade.png
[3]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-poolchart.png
[4]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-properties.png
[5]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-poolblade.png
[6]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-scalecommand.png
[7]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-poolscale.png
[8]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-pricingtiers.png
[9]: ./media/app-service-web-configure-an-app-service-environment/aseconfig-deletease.png

<!--Links-->
[WhatisASE]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-intro/
[Appserviceplans]: http://azure.microsoft.com/documentation/articles/azure-web-sites-web-hosting-plans-in-depth-overview/
[HowtoCreateASE]: http://azure.microsoft.com/documentation/articles/app-service-web-how-to-create-an-app-service-environment/
[HowtoScale]: http://azure.microsoft.com/documentation/articles/app-service-web-scale-a-web-app-in-an-app-service-environment/
[ControlInbound]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-control-inbound-traffic/
[virtualnetwork]: https://azure.microsoft.com/documentation/articles/virtual-networks-faq/
[AppServicePricing]: http://azure.microsoft.com/pricing/details/app-service/
[AzureAppService]: http://azure.microsoft.com/documentation/articles/app-service-value-prop-what-is/
[ASEAutoscale]: http://azure.microsoft.com/documentation/articles/app-service-environment-auto-scale/
[ExpressRoute]: http://azure.microsoft.com/documentation/articles/app-service-app-service-environment-network-configuration-expressroute/
[ILBASE]: http://azure.microsoft.com/documentation/articles/app-service-environment-with-internal-load-balancer/
