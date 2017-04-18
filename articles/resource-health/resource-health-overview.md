<properties
   pageTitle="Descrição geral de estado de funcionamento do Azure recursos | Microsoft Azure"
   description="Descrição geral do Estado de funcionamento do Azure recurso"
   services="Resource health"
   documentationCenter="dev-center-name"
   authors="BernardoAMunoz"
   manager=""
   editor=""/>

<tags
   ms.service="resource-health"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="Supportability"
   ms.date="06/01/2016"
   ms.author="BernardoAMunoz"/>

# <a name="azure-resource-health-overview"></a>Descrição geral de estado de funcionamento do Azure recursos

Azure estado de funcionamento do recurso é um serviço que expõe o estado de funcionamento de recursos Azure individuais e fornece orientações acionáveis para resolução de problemas. Num ambiente do nuvem onde, não é possível aceder diretamente ao servidores ou elementos de infraestrutura, o objetivo do Estado de funcionamento do recurso é para reduzir o tempo que os clientes gastam em resolução de problemas, nomeadamente reduzir o tempo gasto para determinar se a raiz do problema estabelece dentro da aplicação ou se é causado por um evento no interior da plataforma Azure.

## <a name="what-is-considered-a-resource-and-how-does-resource-health-decides-if-the-resource-is-healthy-or-not"></a>O que é considerado um recurso e como é que o estado de funcionamento do recurso decide se o recurso está em bom estado ou não? 
Um recurso é uma instância de criado pelo utilizador de um tipo de recurso fornecido por um serviço, por exemplo: uma máquina virtual, uma aplicação Web ou uma base de dados do SQL. 

Estado de funcionamento do recurso depende de sinais emitidos por recurso e/ou o serviço para determinar se um recurso está em bom estado ou não. É importante para Repare que neste momento contas apenas do Estado de funcionamento de recursos para o estado de funcionamento de um recurso específico escreva não considerar que outros elementos que podem contribuir para o estado de funcionamento geral. Por exemplo, ao reportar que o estado de uma máquina virtual, apenas a parte de cluster infraestrutura do é considerado, ou seja, problemas na rede não serão apresentados no estado de funcionamento do recurso, a menos que existe uma indisponibilidade do serviço declarados, neste caso,-vai ser apresentada através da faixa no topo da pá. Mais informações sobre a indisponibilidade do serviço são disponibilizadas mais adiante. 

## <a name="how-is-resource-health-different-from-service-health-dashboard"></a>Estado de funcionamento do recurso é diferente do Dashboard do Estado de funcionamento do serviço?

As informações fornecidas pelo Estado de funcionamento do recurso são mais granulares que o que é fornecido pelo Dashboard do Estado de funcionamento do serviço. Enquanto SHD comunica eventos que causam impacto na disponibilidade de um serviço numa região, estado de funcionamento do recurso expõe informações relevantes para um recurso específico, por exemplo,-irão expor eventos que causam impacto na disponibilidade de uma máquina virtual, uma aplicação web ou uma base de dados do SQL. Por exemplo, se um nó for reiniciado inesperadamente, clientes cujas máquinas virtuais foram em execução nesse nó poderão obter o motivo por que motivo os respetivos VM foi indisponível para um período de tempo.   

## <a name="how-to-access-resource-health"></a>Como aceder ao estado de funcionamento do recurso
Para os serviços disponíveis através do Estado de funcionamento do recurso, existem 2 formas de aceder ao estado de funcionamento do recurso.

### <a name="azure-portal"></a>Portal do Azure
O pá de estado de funcionamento do recurso no Portal do Azure, fornece informações detalhadas sobre o estado de funcionamento do recurso, bem como recomendado ações que variam consoante o estado de funcionamento atual do recurso. Este pá fornece a melhor experiência ao consultar o estado de funcionamento do recurso, como-facilita o acesso a outros recursos dentro do portal. Tal como mencionado antes, o conjunto de ações recomendadas na pá de estado de funcionamento do recurso variam consoante o estado de funcionamento atual:

* Recursos saudáveis: uma vez que não foi detectado nenhum problema que pode afetar o estado de funcionamento do recurso, as ações são com o foco para ajudar o processo de resolução de problemas. Por exemplo, fornece acesso direto aos pá a resolução de problemas, que oferece orientação sobre como resolver a face de clientes problemas mais comuns.
* Recurso danificado: problemas causados pela Azure, o pá irá apresentar ações Microsoft está a demorar (ou tiver tomado) para recuperar o recurso. Para problemas causados pela utilizador ações iniciadas, o irá pá numa lista de clientes ações que pode tomar para isso abordarem o problema e recuperar o recurso.  

Assim que iniciou sessão no Portal do Azure, existem duas formas de aceder a pá de estado de funcionamento do recurso: 

###<a name="open-the-resource-blade"></a>Abra o pá de recursos
Abra o pá recurso para um dado recurso. No pá definições que é apresentada junto a pá recurso, clique em estado de funcionamento do recurso para abrir o pá de estado de funcionamento do recurso. 

![Pá de estado de funcionamento do recurso](./media/resource-health-overview/resourceBladeAndResourceHealth.png)

### <a name="help-and-support-blade"></a>Ajuda e pá de suporte
Abra a pá ajuda e suporte ao clicar no ponto de interrogação no canto superior direito, em seguida, selecionar ajuda + suporte. 

**A partir da barra de navegação superior**

![Ajuda + suporte](./media/resource-health-overview/HelpAndSupport.png)

Clicar no mosaico do abre pá de subscrição de estado de funcionamento recurso que irá listar todos os recursos na sua subscrição. Junto a cada recurso, existe um ícone a indicar que o seu estado de funcionamento. Clicar em cada recurso é aberto o pá de estado de funcionamento do recurso.

**Mosaico de estado de funcionamento do recurso**

![Mosaico de estado de funcionamento do recurso](./media/resource-health-overview/resourceHealthTile.png)

## <a name="what-does-my-resource-health-status-mean"></a>O que significa o meu estado de funcionamento do recurso?
Existem 4 Estados de estado de funcionamento diferente que poderá ver para o seu recurso.

### <a name="available"></a>Disponível
O serviço não detetou algum problema da plataforma que poderia que afetam a disponibilidade do recurso. Isto é indicado por um ícone de marca de verificação verde. 

![O recurso está disponível](./media/resource-health-overview/Available.png)

### <a name="unavailable"></a>Indisponível

Neste caso o serviço foi detetado um problema em curso na plataforma na qual é que afetam a disponibilidade deste recurso, por exemplo, o nó onde a VM estava a ser executado inesperadamente reiniciado. Isto é indicado por um ícone de aviso vermelho. Informações adicionais sobre o problema for fornecidas na secção de meio da pá, incluindo: 

1.  Que ações Microsoft está a demorar a recuperar o recurso 
2.  Uma linha cronológica detalhada do problema, incluindo a resolução esperado tempo
3.  Uma lista de acções para utilizadores recomendada 

![Recurso não está disponível](./media/resource-health-overview/Unavailable.png)

### <a name="unavailable--customer-initiated"></a>Indisponível – cliente iniciados por
O recurso está disponível devido a um pedido de cliente como parar a um recurso ou pedir um reinício. Isto é indicado por um ícone de informativo azul. 

![Recurso não está disponível devido ao utilizador uma ação iniciada](./media/resource-health-overview/userInitiated.png)

### <a name="unknown"></a>Desconhecido
O serviço não recebeu informações sobre este recurso para mais de 5 minutos. Isto é indicado por um ícone de cinzento de ponto de interrogação. 

É importante ter em atenção que esta não é uma indicação definitiva que existe um problema com um recurso, para que os clientes que devem seguir estas recomendações:

* Se o recurso está em execução como é esperado mas o seu estado de funcionamento está definido para desconhecido no estado de funcionamento do recurso, não existem problemas e que pode esperar o estado do recurso que Atualize para Saudável depois de alguns minutos.
* Se existirem problemas ao aceder a do recurso e o seu estado de funcionamento estiver definidos para desconhecido no estado de funcionamento do recurso, este pode ser uma indicação antecipada podem existir um problema e investigações adicionais devem ser feitas até que o estado de funcionamento é atualizado para saudável ou danificado

![Estado de funcionamento do recurso é desconhecido](./media/resource-health-overview/unknown.png)

## <a name="service-impacting-events"></a>Eventos que afetam do serviço
Se o recurso pode ser afectado por um serviço que afetam evento em curso, será apresentada uma faixa no topo da pá de estado de funcionamento do recurso. Clicar na faixa será aberto o pá eventos de auditoria, que irá apresentar informações adicionais sobre a indisponibilidade.

![Estado de funcionamento do recurso pode ser afectado por um SIE](./media/resource-health-overview/serviceImpactingEvent.png)

## <a name="what-else-do-i-need-to-know-about-resource-health"></a>O que mais preciso de saber sobre o estado de funcionamento do recurso?

### <a name="signal-latency"></a>Latência sinal
Sinais que feed estado de funcionamento do recurso, poderá ser até 15 mínimo atrasado, que podem causar discrepâncias entre o estado de funcionamento atual do recurso e a respectiva disponibilidade real. É importante que tenha isto em conta, tal como ajudarão eliminar desnecessários tempo gasto investigar possíveis problemas. 

### <a name="special-case-for-sql"></a>Caso especial para SQL 
Estado de funcionamento do recurso comunica o estado da base de dados SQL, não o SQL server. Enquanto aceder esta rota fornece uma imagem de estado de funcionamento mais real, requer que vários componentes e serviços tomar em consideração para determinar o estado de funcionamento da base de dados. O sinal atual baseia-se em inícios de sessão para a base de dados, o que significa que para bases de dados que recebem normais os inícios de sessão (que inclui entre outras coisas, pedidos de execução de consulta a receber) o estado de funcionamento estado será regularmente apresentado. Se a base de dados não foi acedido por um período de 10 minutos ou mais, serão movido para o estado de membro desconhecido. Isto significa que a base de dados está indisponível, basta que sem sinal tenha sido emitida porque foram executados sem inícios de sessão. Ligar à base de dados e executar uma consulta irão emitir sinais necessários para determinar e actualizar o estado de funcionamento da base de dados.

## <a name="feedback"></a>Comentários
Estamos sempre abrir para comentários e sugestões! Envie-nos seus [sugestões](https://feedback.azure.com/forums/266794-support-feedback). Para além disso, pode participar com-nos através do [Twitter](https://twitter.com/azuresupport) ou os [fóruns do MSDN](https://social.msdn.microsoft.com/Forums/azure).
