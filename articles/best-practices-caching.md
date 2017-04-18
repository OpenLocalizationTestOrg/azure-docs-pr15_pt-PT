<properties
   pageTitle="Colocação em cache orientações | Microsoft Azure"
   description="Orientações sobre colocação em cache para melhorar o desempenho e escalabilidade."
   services=""
   documentationCenter="na"
   authors="dragon119"
   manager="christb"
   editor=""
   tags=""/>

<tags
   ms.service="best-practice"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="07/14/2016"
   ms.author="masashin"/>


# <a name="caching-guidance"></a>Colocação em cache de orientação

[AZURE.INCLUDE [pnp-header](../includes/guidance-pnp-header-include.md)]

Colocação em cache é uma técnica comuns que destina-se para melhorar o desempenho e escalabilidade de um sistema. Faz isto copiando temporariamente dados acedidos frequentemente ao armazenamento rápido que se encontra fechar para a aplicação. Se este armazenamento de dados rápida se encontra mais perto da aplicação que a origem original, em seguida, colocação em cache pode melhorar significativamente tempos de resposta para aplicações cliente por funcionar mais rapidamente dados.

Colocação em cache é mais eficaz quando uma instância de cliente repetidamente lê os mesmos dados, especialmente se todas as condições seguintes aplicam-se para o arquivo de dados original:
- Continua a ser relativamente estático.
- É lenta em comparação com a velocidade da cache.
- É sujeito a um nível de contenção elevado.
- É longe quando latência da rede pode fazer com que o access ser lenta.

## <a name="caching-in-distributed-applications"></a>Colocação em cache distribuída aplicações

Aplicações distribuídas normalmente implementam uma ou ambas as seguintes estratégias quando colocação em cache de dados:

- Utilizar uma cache privada, onde dados são mantidos localmente no computador que esteja a executar uma instância de uma aplicação ou serviço.
- Utilizar uma cache partilhada, a funcionar como uma origem de comuns que pode ser acedida por vários processos e/ou máquinas.

Em ambos os casos, colocação em cache pode ser efetuada lado do cliente e/ou do lado do servidor. Colocação em cache do lado do cliente é feita através do processo que fornece a interface de utilizador para um sistema, tal como um web browser ou a aplicação de ambiente de trabalho.
Colocação em cache do lado do servidor é feita através do processo que fornece os serviços de empresas que estão a executar o remotamente.

### <a name="private-caching"></a>A colocação em cache privada

O tipo de cache mais básico é uma loja na memória. Tem contidos no espaço de endereços de um processo simples e pode ser consultada diretamente pelo código que é executado no processo. É muito rápido aceder a este tipo de cache. Também pode conferir um meio extremamente eficaz para armazenar conhecimentos básicos quantidades de dados estáticos, uma vez que o tamanho de uma cache normalmente é limitado pelo volume de memória que está disponível no computador que aloja o processo.

Se precisar de mais informações do que é possível física na memória em cache, pode escrever dados em cache para o sistema de ficheiros local. Será mais lenta aceder a mais dados que são mantidos na memória, mas deve ser ainda mais rápida e fiável que obtenção de dados através de uma rede.

Se tiver várias instâncias de uma aplicação que utilize este modelo executado em simultâneo, cada instância da aplicação tem a própria cache independente mantém a sua própria cópia dos dados.

Pense uma cache como um instantâneo dos dados originais em algumas ponto no passado. Se estes dados não forem estáticos, é provável que instâncias da aplicação diferentes mantenha a tecla versões diferentes dos dados nas suas caches. Por conseguinte, a mesma consulta realizada por estas instâncias pode devolver resultados diferentes, conforme apresentado na figura 1.

![Utilizar uma cache na memória no diferentes instâncias de uma aplicação](media/best-practices-caching/Figure1.png)

_Figura 1: Utilizar uma cache na memória no diferentes instâncias de uma aplicação_

### <a name="shared-caching"></a>Partilhada a colocação em cache

Utilizar uma cache partilhada pode ajudar a atenuar preocupações que dados podem ser diferentes no cada cache, que pode ocorrer com na memória colocação em cache. A colocação em cache partilhada assegura que instâncias da aplicação diferente de ver a mesma vista dos dados em cache. Faz isto por localizar a cache numa localização diferente, normalmente alojado como parte de um serviço em separado, conforme apresentado na figura 2.

![Utilizar uma cache partilhada](media/best-practices-caching/Figure2.png)

_Figura 2: Utilizar uma cache partilhada_

Uma vantagem importante da abordagem de colocação em cache partilhada é escalabilidade fornece. Vários serviços de cache partilhados são implementados utilizando um cluster de servidores e utilizam software distribui os dados ao longo do cluster de forma transparente. Uma instância da aplicação simplesmente envia um pedido para o serviço de cache.
A infraestrutura de subjacente é responsável por para determinar a localização dos dados em cache no cluster. Pode facilmente dimensionar a cache ao adicionar mais servidores.

Existem duas desvantagens principais da colocação em cache abordagem partilhada:
- A cache é mais lenta aceder a vez que é já não são mantida localmente para cada instância da aplicação.
- O requisito de registo para implementar um serviço de cache separada poderá adicionar complexidade a solução.

## <a name="considerations-for-using-caching"></a>Considerações para utilizar a colocação em cache

As seguintes secções descrevem as considerações para criar e utilizar uma cache mais detalhadamente.

### <a name="decide-when-to-cache-data"></a>Decidir quando colocar dados em cache

Colocação em cache pode melhorar significativamente o desempenho, escalabilidade e disponibilidade. Os dados mais que tem e maior o número de utilizadores que precisam de aceder a estes dados, maior será os benefícios da colocação em cache tornam-se. Se ao facto de colocação em cache reduz a latência e contenção associada processar grandes volumes de pedidos em simultâneo no arquivo de dados original.

Por exemplo, uma base de dados pode suportar um número limitado de ligações em simultâneo. Obter dados a partir de uma cache partilhada, no entanto, em vez de base de dados subjacente, torna possíveis para uma aplicação de cliente aceder a estes dados mesmo se o número de ligações disponíveis é esgotado atualmente. Para além disso, se a base de dados ficar disponível, aplicações de cliente poderão ser possível continuar a utilizando os dados que são mantidos na cache.

Considere a colocação em cache de dados que são frequentemente a ler mas modificados com pouca frequência (por exemplo, dados que tenham uma proporção superior das operações de leitura de operações de escrita). Não no entanto, recomendamos que utilize a cache de como o arquivo de informações críticas autoritativos. Em vez disso, certifique-se de que todas as alterações que a aplicação não pode perder de forma alguma perder são guardadas sempre um arquivo de dados persistentes. Isto significa que, se a cache não está disponível, a aplicação ainda pode continuar a trabalhar com utilizando o arquivo de dados e que não perca informações importantes.

### <a name="determine-how-to-cache-data-effectively"></a>Determinar como colocar em cache dados de forma eficaz

A chave para utilizar uma cache de forma eficaz reside no determinar os dados mais adequados a cache e a colocação em cache-lo ao tempo adequado. Os dados podem ser adicionados à cache a pedido pela primeira vez que é obtida por uma aplicação. Isto significa que a aplicação que necessita para obter os dados de uma só vez a partir da loja de dados e que o access subsequente pode ser cumprido utilizando a cache.

Em alternativa, uma cache pode ser total ou parcialmente preenchida com dados com antecedência, normalmente quando a aplicação é iniciada (uma abordagem de conhecido como propagar). No entanto, poderá não ser aconselhável para implementar o propagar para uma grande cache porque esta abordagem pode impor uma carga repentina sobre, alta no arquivo de dados original quando a aplicação é iniciada a executar.

Muitas vezes uma análise de padrões de utilização pode ajudar a decidir se pretende total ou parcialmente preencher uma cache de e para escolher os dados a cache. Por exemplo, pode ser útil propagar a cache com os dados de perfil de utilizador estático para clientes que utilizam a aplicação regularmente (talvez diariamente), mas não para clientes que utilizam a aplicação de uma só vez, uma semana.

A colocação em cache normalmente funciona bem com os dados que é imutáveis ou que as alterações com pouca frequência. Alguns exemplos incluem informações de referência como produto e informações sobre preços uma aplicação do comércio electrónico ou recursos partilhados estáticos que estão dispendiosos construir. Alguns ou todos estes dados podem ser carregados para a cache no arranque da aplicação para minimizar o pedido de recursos e para melhorar o desempenho. Também poderá adequado para ter um processo de fundo que actualiza periodicamente referência dados na cache para garantir que estão atualizados ou que atualiza a cache de dados de referência quando as alterações.

Colocação em cache é menos útil para dados dinâmicos, apesar de existirem algumas exceções a esta análise (consulte a secção Cache altamente dinâmicos dados mais adiante neste artigo para obter mais informações). Quando os dados originais altera regularmente, as informações em cache torna-se obsoletos muito rapidamente ou o overhead da cache de a sincronizar com o arquivo de dados original reduz a eficácia da colocação em cache.

Note que uma cache de não ter que incluir os dados para uma entidade concluídos. Por exemplo, se um item de dados representa um objeto de valores múltiplo tal como um cliente de identificação bancária com um nome, endereço e saldo da conta, algumas destes elementos poderão permanecem estáticas (como o nome e endereço), enquanto outras pessoas (como o saldo da conta) poderão ser mais dinâmicas. Nas seguintes situações, pode ser útil para as partes estáticas dos dados em cache e obter (ou calcular) apenas as informações restantes quando for necessário.

Recomendamos que realizar análise de teste e a utilização de desempenho para determinar se o carregamento de população pré-lançamento ou a pedido da cache ou uma combinação de ambos, é adequado. A decisão deve ser baseada no volatilidade e padrão a utilização dos dados. Análise de utilização e o desempenho da cache é particularmente importante aplicações que encontrar cargas frequente e tem de ser altamente dimensionáveis. Por exemplo, em cenários altamente dimensionáveis-poderá fazer sentido propagar a cache para reduzir a carregar no arquivo de dados em alturas de pico.

Colocação em cache pode também ser utilizada para evitar a repetição cálculos enquanto a aplicação estiver em execução. Se uma operação transforma dados ou executa um cálculo complicado,-pode guardar os resultados da operação na cache de. Se o mesmo cálculo for necessário mais tarde, a aplicação simplesmente obtém os resultados da cache.

Uma aplicação pode modificar os dados que são mantidos numa cache. No entanto, recomendamos a pensar em cache como um arquivo de dados breves que poderia desaparecer em qualquer altura. Não armazenar dados importantes na cache apenas; Certifique-se de que mantém as informações no arquivo de dados original. Isto significa que se a cache de ficar disponível, minimizar dar a oportunidade de perda de dados.

### <a name="cache-highly-dynamic-data"></a>Altamente dinâmico de dados em cache

Quando alterar rapidamente informações são armazenadas num arquivo de dados persistente, pode impor uma sobrecarga no sistema. Por exemplo, considere um dispositivo que continuamente relatórios de estado ou algumas outras medida. Se uma aplicação optar por não estes dados com base no que as informações em cache será quase sempre Desatualizadas em cache, em seguida, a mesma consideração pode dever devolve true quando armazenar e obter esta informação a partir da loja de dados. No tempo que demora a guardar e obter estes dados, pode ter alterado.

Numa situação como estas, considere os benefícios da armazenar as informações dinâmicas diretamente na cache em vez de no arquivo de dados persistentes. Se os dados são não críticos e não requer auditoria, em seguida, não interessa se a alteração ocasional é perdida.

### <a name="manage-data-expiration-in-a-cache"></a>Gerir a expiração de dados numa cache

Na maioria dos casos, os dados que são mantidos numa cache são uma cópia de dados que são mantidas no arquivo de dados original. Os dados no arquivo de dados original poderão alterar depois-foi colocada em cache, a causar os dados em cache para se tornar obsoletos. Muitos sistemas de colocação em cache permitem-lhe configurar a cache de dados de expirar e reduzir o período de tempo para os quais os dados podem ser desatualizados.

Quando os dados em cache expirarem, é removida da cache e a aplicação terá de obter os dados a partir da loja de dados original (-pode colocar as informações obtidas recentemente novamente na cache). Pode definir uma política de expiração predefinida quando configurar a cache. Em vários serviços de cache, também pode prever o período de expiração para objectos individuais quando armazená-los através de programação na cache.
Algumas caches permitem-lhe especificar o período de expiração como um valor absoluto ou como um valor deslizante que faz com que o item ser removido da cache de se não é acedida no período de tempo especificado. Esta definição substitui qualquer política de expiração de toda a cache, mas apenas para os objectos especificados.

> [AZURE.NOTE] Considere o período de expiração para a cache e os objetos que contém cuidadosamente. Se efetuar-demasiado curta, objetos expirará demasiado rapidamente e irá reduzir os benefícios da utilização da cache. Se efetuar o período demasiado tempo, corre o risco dos dados se tornam obsoletos.

Também é possível que poderá preencher a cache para cima, se for permitidos dados permanecem residência durante muito tempo. Neste caso, todos os pedidos para adicionar novos itens para a cache podem causar alguns itens ser removido forçar num processo conhecido como eviction. Serviços de cache expulsar normalmente dados numa base de (LRU) menos recentemente utilizado, mas pode substituir esta política e impedir que os itens que está a ser retirado normalmente. No entanto, se adotar esta abordagem, corre o risco excedem a memória que está disponível na cache. Uma aplicação que tentativas para adicionar um item para a cache irá falhar com uma exceção.

Algumas implementações colocação em cache, poderão fornecer políticas eviction adicionais. Existem vários tipos de políticas de eviction. Estes incluem:
- Uma política recentemente utilizados (na expetativa que os dados não será necessários novamente).
- Uma política de first no first-out (dados mais antigo são retirados pela primeira vez).
- Uma política de remoção explícitas com base num evento acionado (tais como os dados modificados).

### <a name="invalidate-data-in-a-client-side-cache"></a>Invalidar dados na cache do lado do cliente

Dados que são mantidos em cache uma lado do cliente são geralmente considerados fora junto do serviço que fornece os dados para o cliente. Um serviço não é possível diretamente força um cliente para adicionar ou remover informações a partir de uma cache do lado do cliente.

Isto significa que é possível para um cliente que utiliza uma cache mal configurada para continuar a utilizar informações Desatualizadas. Por exemplo, se as políticas de expiração da cache de não são implementadas corretamente, um cliente poderá utilizar informações Desatualizadas que são colocada em cache localmente quando as informações na origem de dados original foi alterado.

Se estiver a criar uma aplicação web que serve de dados através de uma ligação de HTTP, pode forçar implicitamente um cliente da web (como um browser ou web proxy) para obter as informações mais recentes. Pode fazê-lo se um recurso é atualizado por uma alteração no URI do recurso. Os clientes Web utilizam normalmente o URI de um recurso como chave na cache do lado do cliente, para que se o URI forem alterados, o cliente web ignora qualquer, anteriormente, em cache versões de um recurso e obtém em vez disso, a nova versão.

## <a name="managing-concurrency-in-a-cache"></a>Gerir simultaneidade numa cache

Caches frequentemente foram concebidos para ser partilhado por várias instâncias de uma aplicação. Cada instância da aplicação pode ler e modificar os dados na cache. Por conseguinte, as mesmas questões simultaneidade que surja com qualquer arquivo de dados partilhada também se aplicam a uma cache. Numa situação onde precisa de uma aplicação para modificar os dados que são mantidos na cache, poderá ter de se certificar de que as atualizações feitas por uma instância da aplicação não substituir as alterações efetuadas por outra instância.

Dependendo da natureza dos dados e a probabilidade de conflitos, pode tomar uma das duas abordagens para simultaneidade:

- __Optimista.__ Imediatamente antes de atualizar os dados, a aplicação verifica se os dados na cache foi alterado desde que foi lido. Se os dados ainda o mesmo, pode ser efetuada a alteração. Caso contrário, a aplicação tem de decidir se pretende atualizar. (A lógica empresarial que unidades esta decisão será específicos da aplicação.) Esta abordagem é adequado para situações onde as atualizações são pouco frequentes ou onde conflitos estão provável a ocorrência.
- __Pessimista.__ Quando obtém os dados, a aplicação bloqueia-lo na cache para impedir que outra instância alterá-lo. Este processo garante que não poderão ocorrer conflitos, mas estes também podem bloquear outras instâncias que precisa para processar os mesmos dados. Simultaneidade pessimista é recomendada apenas para operações curto e pode afetar a escalabilidade de uma solução. Esta abordagem poderá ser adequada para situações onde os conflitos estão mais predispostos, especialmente se uma aplicação de actualizações de vários itens na cache e tem de garantir que estas alterações sejam aplicadas de forma consistente.

### <a name="implement-high-availability-and-scalability-and-improve-performance"></a>Implementar elevada disponibilidade e escalabilidade e melhorar o desempenho

Evitar utilizar uma cache de como o repositório principal de dados; Esta é a função do arquivo de dados original a partir do qual é povoada a cache. Arquivo de dados original é responsável por assegurar persistente dos dados.

Tenha cuidado para não apresentar dependências críticas sobre a disponibilidade de um serviço de cache partilhada para as soluções. Uma aplicação deverá conseguir continuar a funcionar se o serviço que fornece a cache partilhada não estiver disponível. A aplicação não deve deixar de responder ou falhar ao aguardar que o serviço de cache continuar.

Por conseguinte, a aplicação deve estar preparada para detetar a disponibilidade do serviço de cache e reverter para o arquivo de dados original se a cache está inacessível. O [separador de palavras circuito padrão](http://msdn.microsoft.com/library/dn589784.aspx) é útil para processamento este cenário. O serviço que fornece a cache pode ser recuperado e assim que torna-se disponível, pode ser preenchida a cache de dados são ser lido o arquivo de dados original, seguindo uma estratégia de como o [padrão de anulação da Cache](http://msdn.microsoft.com/library/dn589799.aspx)de formulário.

No entanto, poderá haver um impacto escalabilidade no sistema de se a aplicação reverte para o arquivo de dados original quando a cache está temporariamente indisponível.
Enquanto está a ser recuperado o arquivo de dados, o arquivo de dados original foi inundado com pedidos de dados, que resulta em tempos limite e falhou ligações.

Considere a implementação de uma cache local, privada em cada ocorrência de uma aplicação, juntamente com a cache partilhada que aceder a todas as instâncias da aplicação. Quando a aplicação obtém um item, pode verificar pela primeira vez na sua cache local, em seguida, na partilhada em cache e, por fim dos dados originais armazenar. A cache local pode ser preenchida a utilizar os dados na cache partilhada ou na base de dados se a cache partilhada não estiver disponível.

Esta abordagem exige configuração cuidadosa para impedir que a cache local se tornam obsoletos demasiado relativamente à cache partilhada. No entanto, a cache local age como um intervalo de tempo se a cache partilhada não é possível alcançar. Figura 3 mostra esta estrutura.

![Utilizar uma cache local, privada com uma cache partilhada](media/best-practices-caching/Caching3.png)
_figura 3: utilizar uma cache local, privada com uma cache partilhada_

Para suportar caches grandes que detêm dados relativamente longo, alguns serviços de cache fornecem uma opção de alta disponibilidade que implementa automática activação pós-falha se a cache de ficar indisponível. Esta abordagem normalmente envolve replicação os dados em cache que estão armazenados num servidor cache principal para um servidor de cache secundário e mudar para o servidor secundário se o servidor primário falhar ou conectividade é perdida.

Para reduzir a latência que está associada a escrever para vários destinos, a replicação do servidor secundário poderão ocorrer modo assíncrono quando dados destina-se para a cache no servidor principal. Esta abordagem orienta a possibilidade de que podem perder algumas informações colocadas em cache em caso de falha, mas a proporção destes dados deve ser pequenas em comparação com o tamanho geral da cache.

Se uma cache partilhada for grande, poderá vantajoso partição os dados em cache em nós para reduzir as hipóteses de contenção e melhorar escalabilidade. Muitos caches partilhadas suportam a capacidade de adicionar dinamicamente (e remover) nós e redistribuir os dados ao longo a partições. Esta abordagem poderá implicar clusters, na qual a coleção de nós é apresentada a aplicações de cliente como uma cache de forma totalmente integrada, única. Internamente, no entanto, os dados são dispersos entre nós de seguir uma estratégia de distribuição predefinido que equilibra a carga uniformemente. O [documento de orientação da criação de partições dados](http://msdn.microsoft.com/library/dn589795.aspx) no Web site da Microsoft fornece mais informações sobre estratégias de criação de partições possíveis.

Clusters também podem aumentar a disponibilidade da cache. Se um nó falhar, o resto da cache é continuam a estar acessível.
Clusters são frequentemente utilizado em conjunto com replicação e activação pós-falha. Cada nó pode ser replicada e a réplica pode ser rapidamente colocada online se o nó falhar.

Muitas leia e operações de escrita provavelmente envolvam valores de dados única ou objetos. No entanto, por vezes, poderá ser necessário armazenar ou obter rapidamente grandes volumes de dados.
Por exemplo, propagar uma cache poderia envolvam escrever centenas ou milhares de itens para a cache. Também poderá ter uma aplicação obter um grande número de itens relacionados a partir da cache como parte do mesmo pedido.

Muitos caches em grande escala fornecem operações batch para estes efeitos. Isto permite uma aplicação de cliente Compactar para cima um grande volume de itens para um único pedido e reduz o overhead que está associado ao efetuar um grande número de pedidos de pequenas.

## <a name="caching-and-eventual-consistency"></a>Colocação em cache e eventual consistência

Para o padrão de cache anulação trabalhar, a instância da aplicação que preenche a cache tem de ter acesso a versão mais recente e consistente dos dados. Num sistema que implementa eventual consistência (tal como um arquivo de dados replicada) este não poderá ser as maiúsculas/minúsculas.

Uma instância de uma aplicação do poderia modificar um item de dados e invalidar a versão em cache desse item. Outra instância da aplicação poderá tentar ler este item a partir de uma cache, o que faz com que uma falha de cache, para que lê os dados a partir da loja de dados e adiciona-a para a cache. No entanto, se o arquivo de dados não foi totalmente sincronizado com as outras réplicas, a instância da aplicação pode ler e preencher a cache com o valor antigo.

Para mais informações sobre como lidar com consistência dos dados, consulte a página de [introdução de consistência de dados](http://msdn.microsoft.com/library/dn589800.aspx) no Web site da Microsoft.

### <a name="protect-cached-data"></a>Proteger os dados em cache

Independentemente o serviço de cache utilizar, considere como pode proteger os dados que são mantidos em cache contra acesso não autorizado. Existem duas preocupações principais:

- Privacidade dos dados na cache
- A privacidade dos dados tal como é flua entre a cache e a aplicação que está a utilizar a cache

Para proteger os dados na cache, o serviço de cache poderá implementar um dispositivo de autenticação que requer que aplicações especificam o seguinte:
- Identidades do quais podem aceder aos dados na cache.
- Quais as operações (leitura e escrita) que estas identidades são permitidas para efetuar.

Para reduzir a sobrecarga que está associada leitura e escrita dados, depois de uma identidade tenha sido concedida escrita e/ou acesso de leitura para a cache, que identidade pode utilizar quaisquer dados na cache.

Se for necessário restringir o acesso a subconjuntos de dados em cache, pode efetuar um dos seguintes procedimentos:

- Dividir a cache a partições (ao utilizar os servidores de cache diferentes) e apenas conceder acesso a identidades para as partições que devem ser autorizados a utilizar.
- Encriptar os dados em cada subconjunto utilizando as teclas diferentes e fornecer as teclas de encriptação apenas para identidades que devem ter acesso a cada subconjunto. Ainda poderá conseguir obter todos os dados na cache de uma aplicação de cliente, mas só será possível desencriptar os dados para o qual este tenha as teclas.

Também tem de proteger os dados como flua e terminar a cache. Para fazer isto, dependem das funcionalidades de segurança fornecidas pela infraestrutura de rede que aplicações de cliente utilizam para ligar à cache. Se a cache for implementada utilizando um servidor no local dentro da mesma organização que aloja as aplicações de cliente, em seguida, isolamento da rede propriamente dito poderá não necessitam que efetuar passos adicionais. Se a cache está localizada remotamente e exige uma ligação TCP ou HTTP através de uma rede pública (tal como a Internet), considere a implementação SSL.

## <a name="considerations-for-implementing-caching-with-microsoft-azure"></a>Considerações para implementar a colocação em cache no Microsoft Azure

Azure fornece a Cache do Azure Redis. Esta é uma implementação da origem de abrir cache Redis que funciona como um serviço num centro de dados Azure. Fornece um serviço de cache que pode ser acedido a partir de qualquer aplicação do Azure, se a aplicação é implementada como um serviço na nuvem, num Web site ou dentro de uma máquina virtual Azure. Caches podem ser partilhados por aplicações de cliente que tem a chave de acesso adequado.

Azure Redis Cache é uma solução de colocação em cache de alto desempenho que fornece disponibilidade, escalabilidade e segurança. Normalmente, é executado como um serviço distribuir por uma ou mais máquinas dedicadas. Tenta armazenar as informações como pessoais à medida que pode fazer na memória para se certificar de acesso rápido. Esta arquitetura destina-se para fornecer latência baixa e débito alto, reduzindo a necessidade de executar operações e/s lentas.

 Azure Redis Cache é compatível com muitas das APIs vários que são utilizadas por aplicações de cliente. Se tiver aplicações existentes que já estiver a utilizar na Cache Redis Azure em execução no local, a Cache do Azure Redis fornece um caminho de migração rápida a colocação em cache na nuvem.

> [AZURE.NOTE] Azure também fornece o serviço de Cache gerido. Este serviço baseia o motor Azure Service ferro Cache. Permite-lhe criar uma cache distribuída que pode ser partilhada por aplicações vagamente ao forma. A cache está alojada em servidores de alto desempenho em execução num centro de dados do Azure.
No entanto, esta opção já não é recomendada e apenas é fornecida para suportar aplicações existentes que foram criadas para utilizá-la. Para todos os desenvolvimento novo, utilize a Cache de Redis Azure.
>
> Para além disso, o Azure suporta na função colocação em cache. Esta funcionalidade permite-lhe criar uma cache específica num serviço na nuvem.
A cache está alojada pelo instâncias de uma função web ou de trabalho e só pode ser acedida pelas funções que estão a funcionar como parte da mesma unidade de implementação de serviço de nuvem. (Uma unidade de implementação é o conjunto de instâncias de funções que são implementadas como um serviço na nuvem para uma região específica.) A cache é agrupada e todas as instâncias da função dentro da mesma unidade de implementação que aloja a cache de tornam-se parte do mesmo cluster de cache. No entanto, esta opção já não é recomendada e apenas é fornecida para suportar aplicações existentes que foram criadas para utilizá-la. Para todos os desenvolvimento novo, utilize a Cache de Redis Azure.
>
> Serviço de Cache gerido Azure e Azure na função Cache estão atualmente agendado para a reforma no 16 de Novembro de 2016.
É recomendado que migrar para o Azure Redis Cache de preparação para a este reforma. Para obter mais informações, visite a página   [o que é a oferta de Cache Redis do Azure e qual o tamanho devo utilizar?](redis-cache/cache-faq.md#what-redis-cache-offering-and-size-should-i-use) no Web site da Microsoft.


### <a name="features-of-redis"></a>Funcionalidades do Redis

 Redis é mais do que um servidor de cache simples. Fornece uma base de dados na memória distribuído com um conjunto amplo comando que suporta vários cenários comuns. Estas são descritas mais adiante neste documento, na secção utilizar Redis colocação em cache. Esta secção resume algumas das principais funcionalidades que fornece Redis.

### <a name="redis-as-an-in-memory-database"></a>Redis como uma base de dados na memória

Redis suporta ambos os métodos de leitura e escrita operações. No Redis, podem ser protegidas escritas de falha de sistema ao serem armazenados periodicamente num ficheiro instantâneo local ou num ficheiro de registo só de acréscimo. Não é as maiúsculas/minúsculas caches muitos (que devem ser consideradas arquivos de dados transitória).

 Todas as gravações são assíncronas e fazer bloquear clientes de leitura e escrita dados. Quando Redis inícios em execução, lê os dados a partir do ficheiro de registo ou instantâneo e utiliza-os para construir a cache na memória. Para mais informações, consulte o artigo [Redis persistente](http://redis.io/topics/persistence) no Web site da Redis.

> [AZURE.NOTE] Redis não garante que todos os escritas serão guardadas trabalho uma falha grave, mas pelo piores podem perder apenas alguns segundos vale de dados. Lembre-se de que uma cache não se destina a funcionar como uma origem de dados autoritativas e cabe as aplicações que utilizam a cache para garantir que dados críticos são guardados com êxito a um arquivo de dados adequado. Para mais informações, consulte o artigo [anulação da cache do padrão](http://msdn.microsoft.com/library/dn589799.aspx).

#### <a name="redis-data-types"></a>Redis tipos de dados

Redis é um valor de chave de arquivo, onde podem conter os valores tipos simples ou estruturas de dados complexos, tais como hashes, listas e define. Suporta um conjunto de operações Atómica sobre estes tipos de dados. Teclas podem ser permanente ou marcados com uma limitada time-para-live, altura em que a tecla e o seu valor correspondente são removidos automaticamente da cache de. Para mais informações sobre chaves Redis e valores, visite a página [uma introdução à Redis tipos de dados e captações](http://redis.io/topics/data-types-intro) no Web site da Redis.

#### <a name="redis-replication-and-clustering"></a>Redis replicação e clusters

Redis suporta a replicação de principal/subordinado para ajudar a garantir a disponibilidade e manter débito. Escreva operações para um nó mestra Redis são replicadas para um ou mais nós subordinados. Operações de leitura podem ser fornecidas pelo modelo global ou de qualquer um dos subordinados.

Em caso de uma partição de rede, os subordinados podem continuar a servir de dados e, em seguida, transparente voltar a sincronizar com o modelo global de quando a ligação é restabelecida. Para obter mais detalhes, visite a página de [replicação](http://redis.io/topics/replication) no Web site da Redis.

Redis também fornece clusters, que lhe permite partição dados para shards por servidores e distribuir a carga de forma transparente. Esta funcionalidade aumenta a escalabilidade, uma vez que podem ser adicionados novos Redis servidores e os dados como o tamanho da cache de voltar a criar particionados aumentam.

Além disso, cada servidor no cluster pode ser replicada utilizando a replicação principal/subordinado. Este procedimento garante disponibilidade através de cada nó no cluster. Para mais informações sobre clusters e sharding, visite a [página de cluster tutorial de Redis](http://redis.io/topics/cluster-tutorial) no Web site da Redis.

### <a name="redis-memory-use"></a>Redis utilização de memória

Uma cache Redis tem um tamanho de finito depende dos recursos disponíveis no computador anfitrião. Quando configura um servidor de Redis, pode especificar a quantidade máxima de memória, pode utilizar. Também pode configurar uma chave numa cache Redis ter um tempo de expiração, após o qual é automaticamente removido da cache. Esta funcionalidade pode ajudar a impedir a cache na memória enchimento com dados antigos ou obsoletos.

À medida que memória preenchida, Redis pode automaticamente expulsar chaves e os respetivos valores, seguindo um número de políticas de. A predefinição é LRU (utilizada recentemente menos), mas também pode selecionar outras políticas como expulsar teclas aleatoriamente ou desativá-lo completamente a eviction (nas quais, tentativas de maiúsculas/minúsculas para adicionar itens à falhas de cache se estiver cheia). A página [Utilizando Redis como uma cache LRU](http://redis.io/topics/lru-cache) fornece mais informações.

### <a name="redis-transactions-and-batches"></a>Redis transações e secções

Redis permite uma aplicação de cliente submeter uma série de operações que ler e escrever dados em cache como uma transação atómica. Todos os comandos a transação são garante para executar sequencialmente e não comandos emitidos por outros clientes em simultâneo vai ser intercalados entre elas.

No entanto, estes não são verdadeiras transações como uma base de dados relacional seria efectuá-los. Processamento de transações consiste em duas fases – o primeiro é quando são colocadas na fila os comandos e o segundo é quando os comandos são executados. Durante a fase de colocação de comando, são apresentados os comandos que compõem a transação pelo cliente. Se algum tipo de erro ocorre neste momento (como um erro de sintaxe, ou o número de parâmetros errado) Redis, em seguida, recusa a processar a transação inteira e rejeita-lo.

Durante a fase executar, Redis executa cada comando em fila numa sequência. Se um comando falhar nesta fase, Redis continua com o seguinte comando em fila de espera e não recuperar os efeitos de todos os comandos que já tem sido executados. Este formulário simplificado da transação ajuda a manter o desempenho e evitar problemas de desempenho que problemas são causados pela contenção.

Redis implementar um formulário de bloqueio optimista para ajudar a manter a consistência. Para obter informações detalhadas sobre as transações e bloquear com Redis, visite a [página de transações](http://redis.io/topics/transactions) no Web site da Redis.

Redis também suporta não transaccional lotes de pedidos de. O protocolo Redis que os clientes utilizar para enviar comandos para um servidor de Redis permite que um cliente enviar uma série de operações como parte do mesmo pedido. Isto pode ajudar a reduzir fragmentação pacote na rede. Quando o lote é processado, cada comando é executado. Se qualquer um destes comandos forem mal, estes serão rejeitados (que não acontece com uma transação), mas os restantes comandos serão executados. Também não existe nenhuma garantia sobre a ordem na qual os comandos no lote de serão processados.

### <a name="redis-security"></a>Redis segurança

Redis foca puramente fornecer acesso rápido aos dados e foi concebido para executados no interior de um ambiente fidedigno que possa ser acedido apenas pelos clientes fidedignos. Redis suporta um modelo de segurança limitada com base em autenticação de palavra-passe. (É possível remover autenticação completamente, apesar de não recomendamos isto.)

Todos os clientes autenticados partilham a mesma palavra-passe global e têm acesso aos mesmos recursos. Se precisar de mais abrangente sessão de segurança, tem de implementar o seu próprio camada de segurança à frente do servidor de Redis e todos os pedidos de cliente devem passar através desta camada adicional. Não devem ser expostas diretamente redis para clientes ou não fidedignos ou não autenticados.

Pode restringir o acesso aos comandos por desativá-los ou mudar o nome-las (e ao fornecer apenas os clientes privilegiados com os novos nomes).

Redis não suporta diretamente qualquer outra forma de encriptação de dados, para que todos os codificação tem de ser executado por aplicações de cliente. Para além disso, Redis não fornece qualquer outra forma de segurança de transporte. Se precisar de proteger os dados como flua através da rede, recomendamos que a execução de um proxy SSL.

Para mais informações, visite a página [Redis segurança](http://redis.io/topics/security) no Web site da Redis.

> [AZURE.NOTE] Azure Redis Cache fornece as suas próprias camadas de segurança através das quais os clientes se ligam. Os servidores de Redis subjacentes não são expostos à rede pública.

### <a name="using-the-azure-redis-cache"></a>Utilizar a cache Redis do Azure

A Cache do Azure Redis fornece acesso a Redis servidores a executar o em servidores alojados um centro de dados do Azure; funciona como uma fachada que fornece o controlo de acesso e segurança. Pode aprovisionar o uma cache utilizando o portal de gestão do Azure. O portal fornece um número de configurações predefinidas, que se situa entre uma cache 53GB em execução como um serviço dedicado que suporta comunicações de SSL (para privacidade) e a replicação de principal/subordinado com um SLA de 99,9% de disponibilidade, para baixo até uma 250 MB em cache sem replicação (sem garantias de disponibilidade) em execução no hardware partilhada.

Utilizar o portal de gestão do Azure, também pode configurar a política de eviction da cache e controlar o acesso à cache ao adicionar utilizadores às funções fornecidas; Proprietário, contribuinte, leitor. Estas funções definem as operações que os membros podem executar. Por exemplo, os membros da função proprietário têm controlo total sobre a cache (incluindo segurança) e os seus conteúdos, membros da função Contribuinte podem ler e escrever informações em cache e membros da função leitor apenas podem obter dados a partir da cache.

A maioria das tarefas administrativas são executadas através do portal de gestão do Azure e, por este motivo muitos dos comandos administrativos disponíveis na versão padrão do Redis não estão disponíveis, incluindo a capacidade para modificar a configuração através de programação, encerramento servidor Redis, configure slaves adicionais ou forçar guardar dados para o disco.

O portal de gestão Azure inclui uma apresentação de gráfica conveniente que permite-lhe monitorizar o desempenho da cache. Por exemplo, pode ver o número de ligações a ser efectuadas, o número de pedidos executada, o volume de operações de leitura e escrita, e o número de cache de acertos versus falhas na cache. Utilizar estas informações, pode determinar a eficácia da cache e, se necessário muda para uma configuração diferente ou altere a política de eviction. Para além disso, pode criar alertas que enviam mensagens de correio eletrónico a um administrador se uma ou mais métricas críticas estão fora um intervalo esperado. Por exemplo, se o número de falhas de cache exceder um valor especificado na última hora, um administrador poderia recebê-las à medida que a cache, poderá ser demasiado pequena ou dados poderão estar a ser retirados demasiado rapidamente.

Também pode monitorizar a utilização de rede para a cache, memória e CPU.

Para obter mais informações e exemplos que mostra como criar e configurar uma Cache de Redis Azure, visite a página de [volta à volta do Azure Redis Cache](https://azure.microsoft.com/blog/2014/06/04/lap-around-azure-redis-cache-preview/) no blogue do Azure.

## <a name="caching-session-state-and-html-output"></a>Estado da sessão colocação em cache e a saída de HTML

Se lhe construir ASP.NET aplicações que executar utilizando funções Azure web, pode guardar informações de estado de sessão e de saída em HTML numa Azure Redis Cache de web. O fornecedor de estado de sessão para Azure Redis Cache permite-lhe partilhar informações sobre a sessão entre diferentes instâncias de uma aplicação web do ASP.NET e é muito útil em situações de farm web onde afinidade de cliente / servidor não está disponível e colocação em cache sessão dados na memória não seria adequada.

Utilizar o fornecedor de estado de sessão com a Cache de Redis Azure participem várias vantagens, incluindo:

- Pode partilhar o estado da sessão se um grande número de ocorrências de uma aplicação web do ASP.NET, fornecendo escalabilidade melhorada,
- Suporta controlado, em simultâneo acesso a dados de estado do mesmo sessão para vários leitores e uma única sénior, e
- Pode utilizar compressão para guardar memória e melhorar o desempenho de rede.

Para obter mais informações visite a página de [Fornecedor de estado do ASP.NET sessão para Azure Redis Cache](redis-cache/cache-aspnet-session-state-provider.md) no Web site da Microsoft.

> [AZURE.NOTE] Não utilize o fornecedor de estado de sessão para Azure Redis Cache para aplicações do ASP.NET que são executados fora o ambiente do Azure. A latência da aceder a cache de fora do Azure pode eliminar as vantagens de desempenho de colocação em cache de dados.

Do mesmo modo, o fornecedor de Cache de saída para Azure Redis Cache permite-lhe guardar as respostas HTTP geradas por uma aplicação web do ASP.NET. Utilizando o fornecedor de Cache de saída com a Cache de Redis Azure pode melhorar os tempos de resposta de aplicações que compor a saída de HTML complexa; instâncias da aplicação de respostas semelhantes gerar podem tornar a utilização de fragmentos saída partilhado na cache em vez de gerar este HTML novo a partir de saída.  Para obter mais informações visite a página de [ASP.NET fornecedor de Cache de saída para Azure Redis Cache](redis-cache/cache-aspnet-output-cache-provider.md) no Web site da Microsoft.

### <a name="azure-redis-cache"></a>Azure Redis cache

Azure Redis Cache oferece um acesso aos servidores Redis que está alojado um centro de dados do Azure. Funciona como uma fachada que fornece o controlo de acesso e segurança. Pode aprovisionar o uma cache utilizando o portal do Azure.

O portal fornece um número de configurações predefinidas. Estes intervalo a partir de uma cache 53 GB em execução como um serviço dedicado que suporta comunicações de SSL (para privacidade) e principal/subordinado a replicação com um SLA de 99,9% de disponibilidade, para baixo até uma cache de 0 MB 25 sem replicação (sem garantias de disponibilidade) em execução no hardware partilhada.

Utilizar o portal do Azure, pode também configurar a política de eviction da cache e controlar o acesso à cache ao adicionar utilizadores às funções fornecidas.  Estas funções, que definem as operações que os membros podem executar, incluam proprietário, contribuinte e leitor. Por exemplo, os membros da função proprietário têm controlo total sobre a cache (incluindo segurança) e os seus conteúdos, membros da função Contribuinte podem ler e escrever informações em cache e membros da função leitor apenas podem obter dados a partir da cache.

A maioria das tarefas administrativas são executadas através do portal do Azure. Por este motivo, muitos dos comandos administrativos que estão disponíveis na versão padrão do Redis não estão disponíveis, incluindo a capacidade de modificar a configuração através de programação, encerrar o servidor de Redis, configurar subordinados adicionais ou forçar guardar dados no disco.

O portal do Azure inclui uma apresentação de gráfica conveniente que permite-lhe monitorizar o desempenho da cache. Por exemplo, pode ver o número de ligações a ser feita, o número de pedidos de que está a ser executado, o volume de operações de leitura e escrita e o número de acertos versus falhas na cache. Com esta informação, pode determinar a eficácia da cache e se for necessário, mudar para uma configuração diferente ou altere a política de eviction.

Para além disso, pode criar alertas que enviam mensagens de correio eletrónico a um administrador se uma ou mais métricas críticas estão fora um intervalo esperado. Por exemplo, poderá pretender alertar um administrador se o número de falhas de cache exceder um valor especificado na última hora, uma vez que significa a cache poderão ser demasiado pequena ou dados poderão estar a ser retirados demasiado rapidamente.

Também pode monitorizar o CPU, memória e a utilização de rede para a cache.

Para obter mais informações e exemplos que mostra como criar e configurar uma Cache de Redis Azure, visite a página de [volta à volta do Azure Redis Cache](https://azure.microsoft.com/blog/2014/06/04/lap-around-azure-redis-cache-preview/) no blogue do Azure.

## <a name="caching-session-state-and-html-output"></a>Estado da sessão colocação em cache e a saída de HTML

Se estiver a compilar que executar utilizando funções Azure web, pode guardar sessão de aplicações web do ASP.NET indique informações e saída em HTML numa Azure Redis Cache. O fornecedor de estado de sessão para Azure Redis Cache permite-lhe partilhar informações sobre a sessão entre diferentes instâncias de uma aplicação web do ASP.NET e é muito útil em situações de farm web onde afinidade de cliente / servidor não está disponível e colocação em cache sessão dados na memória não seria adequada.

Utilizar o fornecedor de estado de sessão com a Cache de Redis Azure participem várias vantagens, incluindo:

- Partilhar o estado da sessão com um grande número de ocorrências de aplicações web do ASP.NET.
- Fornecer escalabilidade melhorada.
- Suporte controlado, em simultâneo acesso a dados de estado do mesmo sessão para vários leitores e uma única sénior.
- Utilizar a compressão para guardar memória e melhorar o desempenho de rede.

Para mais informações, visite a página de [fornecedor de estado do ASP.NET sessão para Azure Redis Cache](redis-cache/cache-aspnet-session-state-provider.md) no Web site da Microsoft.

> [AZURE.NOTE] Não utilize o fornecedor de estado de sessão para Azure Redis Cache com aplicações do ASP.NET que executar fora o ambiente do Azure. A latência da aceder a cache de fora do Azure pode eliminar as vantagens de desempenho de colocação em cache de dados.

Do mesmo modo, o fornecedor de cache de saída para Azure Redis Cache permite-lhe guardar as respostas HTTP geradas por uma aplicação web do ASP.NET. Utilizando o fornecedor de cache de saída com a Cache de Redis Azure pode melhorar os tempos de resposta de aplicações que compor complexa saída em HTML. Podem tornar instâncias da aplicação que geram respostas semelhantes utilização dos fragmentos saída partilhado na cache em vez de gerar este HTML novo a partir de saída. Para mais informações, visite a página [ASP.NET fornecedor de cache de saída para Azure Redis Cache](redis-cache/cache-aspnet-output-cache-provider.md) no Web site da Microsoft.

## <a name="building-a-custom-redis-cache"></a>Construir uma cache Redis personalizada

Azure Redis Cache age como uma fachada para os servidores de Redis subjacentes. Atualmente suporta um conjunto de configurações fixos mas não fornece para Redis clusters. Se necessitar de uma configuração avançada que não é abrangida pela cache Azure Redis (tal como uma cache de maior que 53 GB) pode criar e alojar o seus próprio servidores Redis utilizando Azure máquinas virtuais.

Este é um processo potencialmente complexo porque poderá ter de criar várias VMs para funcionar como nós mestras e subordinados se pretende implementar a replicação. Além disso, se pretender criar um cluster, em seguida, terá de vários modelos globais e servidores subordinados. Uma topologia de replicação agrupadas mínimas que fornece um elevado grau de disponibilidade e escalabilidade compreende VMs, pelo menos, seis organizados como três pares dos servidores principal/subordinado (um cluster tem de conter pelo menos três nós principais).

Cada par de principal/subordinado deverá estar localizado juntos para minimizar a latência. No entanto, cada conjunto de pares pode estar em execução diferentes centros de dados Azure localizados na regiões diferentes, se pretender localizar dados em cache fechar para as aplicações que são mais provável utilizá-la. A página [a executar o Redis numa VM de Linux CentOS no Azure](http://blogs.msdn.com/b/tconte/archive/2012/06/08/running-redis-on-a-centos-linux-vm-in-windows-azure.aspx) no Web site da Microsoft explica um exemplo que mostra como criar e configurar um nó Redis em execução como um VM Azure.

[AZURE.NOTE] Tenha em atenção que se implementar o seu próprio cache Redis desta forma, for o responsável da monitorização, gerir e proteger o serviço.

## <a name="partitioning-a-redis-cache"></a>Criar a partições uma cache Redis

Criar a partições a cache envolve dividir a cache em vários computadores. Esta estrutura oferece-lhe várias vantagens ao longo com um servidor de cache única, incluindo:

- Criação de uma cache que é muito maior do que podem ser armazenados num único servidor.
- Distribuir dados nos servidores, melhorar a disponibilidade. Se um servidor falha ou torna-se inacessível, os dados que contém não estiver disponível, mas os dados nos servidores restantes ainda podem ser acedido. Para uma cache, esta não é crucial porque os dados em cache estão apenas uma cópia breves dos dados que são mantidas numa base de dados. Dados em cache num servidor que fica inacessível podem ser colocadas em cache num servidor diferente em vez disso.
- Distribuir a carga por servidores, melhorando desempenho e escalabilidade.
- Feche Geolocating dados aos utilizadores acesso, que reduz latência.

Para uma cache, a forma mais comuns de criação de partições é sharding. Nesta estratégia, cada partição (ou shard) é uma cache de Redis na sua própria direita. Dados são direcionados para uma partição específica ao utilizar a lógica sharding, que pode utilizar uma variedade de abordagens para distribuir os dados. O [padrão de Sharding](http://msdn.microsoft.com/library/dn589797.aspx) fornece mais informações sobre como implementar sharding.

Para implementar a partições na cache de Redis, pode execute uma das seguintes abordagens:

- _Encaminhamento da consulta do lado do servidor._ Nesta técnica, uma aplicação de cliente envia um pedido para qualquer um dos servidores Redis que compõem a cache (provavelmente o servidor mais próximo). Cada servidor Redis armazena os metadados que descreva a partição que detém e também contém informações sobre o qual a partições estão localizadas noutros servidores. O servidor de Redis examina o pedido do cliente. Se pode ser resolvido localmente, fará a operação pedida. Caso contrário reencaminhará o pedido para o servidor adequado. Este modelo é implementado por Redis clusters e é descrito detalhadamente mais na página [Redis cluster tutorial](http://redis.io/topics/cluster-tutorial) no Web site da Redis. Redis clusters são transparente para aplicações de cliente e servidores Redis adicionais podem ser adicionados a cluster (e os dados divididos novamente) sem necessidade reconfigure o os clientes.

- _Criação de partições lado do cliente._ Neste modelo, a aplicação de cliente contém lógica (possivelmente sob a forma de uma biblioteca) que encaminha os pedidos para o servidor de Redis adequado. Esta abordagem pode ser utilizada com Azure Redis Cache. Criar múltiplas Azure Redis Caches (um para cada partição de dados) e implementar a lógica do lado do cliente que encaminha os pedidos para a cache correta. Se alterar o esquema de partições (se adicionais Azure Redis Caches são criadas, por exemplo), aplicações de cliente poderão ter de ser reconfigurar.

- _Assistido proxy partições._ Neste esquema, o cliente aplicações enviar pedidos para um serviço de proxy intermédio que compreende como os dados são divididos e, em seguida, encaminha o pedido para o adequado Redis servidor. Esta abordagem pode também ser utilizada com Azure Redis Cache; o serviço de proxy pode ser implementado como um serviço de nuvem Azure. Esta abordagem requer um nível adicional de complexidade para implementar o serviço e os pedidos de podem demorar mais tempo para realizar que com a partições do lado do cliente.

A página [divisão: como dividir dados entre várias instâncias de Redis](http://redis.io/topics/partitioning) na Redis o Web site fornece mais informações sobre como implementar a partições com Redis.

### <a name="implement-redis-cache-client-applications"></a>Implementar Redis aplicações de cliente de cache

Redis aplicações de cliente suporta escritas em vários linguagens de programação. Se estiver a criar novas aplicações utilizando o .NET Framework, é a abordagem recomendada utilizar a biblioteca de cliente StackExchange.Redis. Esta biblioteca fornece um modelo de objeto do .NET Framework que abstracts os detalhes para ligar a um servidor de Redis, comandos de enviar e receber as respostas. Está disponível no Visual Studio como um pacote de NuGet. Pode utilizar esta biblioteca mesmo para ligar a uma Cache do Azure Redis ou uma cache Redis personalizada alojado numa VM.

Para ligar a um servidor de Redis utilize estática `Connect` método a `ConnectionMultiplexer` escolares. A ligação que este método cria foi concebida para ser utilizado em toda a duração da aplicação cliente e, a mesma ligação pode ser utilizada por vários threads em simultâneo. Não voltar a ligar e desligar sempre que executar uma operação de Redis, uma vez que isto pode diminuir o desempenho.

Pode especificar os parâmetros de ligação, tais como o endereço do anfitrião do Redis e a palavra-passe. Se estiver a utilizar o Azure Redis Cache, a palavra-passe é um da chave primária ou secundária que é gerada para a Cache de Redis Azure utilizando o portal de gestão do Azure.

Depois de ter ligado ao servidor Redis, pode obter uma alça da base de dados Redis que age como a cache. A ligação Redis fornece o `GetDatabase` método para o fazer. Em seguida, pode obter itens a partir da cache e armazenar dados na cache utilizando o `StringGet` e `StringSet` métodos. Estes métodos esperar uma chave como um parâmetro e devolver o item na cache de que tem um valor correspondente (`StringGet`) ou adicionar o item para a cache com esta chave (`StringSet`).

Dependendo da localização do servidor Redis, muitas operações poderão assumir algumas latência enquanto um pedido é transmitido no servidor e é devolvida uma resposta para o cliente. A biblioteca de StackExchange fornece versões assíncronas de muitos dos métodos que expõe-lo para o ajudar a aplicações de cliente permanecem responder. Estes métodos suportam o [Padrão de assíncrona baseado em tarefas](http://msdn.microsoft.com/library/hh873175.aspx) no .NET Framework.

O fragmento de código seguinte mostra um método denominado `RetrieveItem`. Ilustra a uma implementação do padrão com base em Redis e a biblioteca de StackExchange anulação da cache. O método leva-o até um valor de chave de cadeia e tentará obter o item correspondente da cache de Redis ao contactar o suporte de `StringGetAsync` método (a versão do assíncrona `StringGet`).

Se não for encontrado o item, são obtido a partir da utilizando de origem de dados subjacentes a `GetItemFromDataSourceAsync` método (que é um método local e que não faça parte da biblioteca de StackExchange). Em seguida, é adicionada à cache utilizando o `StringSetAsync` método para que pode possível recuperá-lo mais rapidamente próxima vez.

```csharp
// Connect to the Azure Redis cache
ConfigurationOptions config = new ConfigurationOptions();
config.EndPoints.Add("<your DNS name>.redis.cache.windows.net");
config.Password = "<Redis cache key from management portal>";
ConnectionMultiplexer redisHostConnection = ConnectionMultiplexer.Connect(config);
IDatabase cache = redisHostConnection.GetDatabase();
...
private async Task<string> RetrieveItem(string itemKey)
{
    // Attempt to retrieve the item from the Redis cache
    string itemValue = await cache.StringGetAsync(itemKey);

    // If the value returned is null, the item was not found in the cache
    // So retrieve the item from the data source and add it to the cache
    if (itemValue == null)
    {
        itemValue = await GetItemFromDataSourceAsync(itemKey);
        await cache.StringSetAsync(itemKey, itemValue);
    }

    // Return the item
    return itemValue;
}
```

O `StringGet` e `StringSet` métodos não estão limitados a obter ou armazenar valores de cadeia. Podem pegar em qualquer item que está serializado como uma matriz de bytes. Se precisar de guardar um objecto .NET, pode serializá-la como uma sequência de bytes e utilizar o `StringSet` método para escrevê-lo a cache.

Da mesma forma, pode ler um objeto da cache de utilizando o `StringGet` método e anular a serialização como um objeto de .NET. O código seguinte mostra um conjunto de extensão métodos para a interface de IDatabase (o `GetDatabase` método de uma ligação de Redis devolve um `IDatabase` objeto) e algum código de exemplo que utiliza estes métodos para ler e escrever uma `BlogPost` objeto para a cache:

```csharp
public static class RedisCacheExtensions
{
    public static async Task<T> GetAsync<T>(this IDatabase cache, string key)
    {
        return Deserialize<T>(await cache.StringGetAsync(key));
    }

    public static async Task<object> GetAsync(this IDatabase cache, string key)
    {
        return Deserialize<object>(await cache.StringGetAsync(key));
    }

    public static async Task SetAsync(this IDatabase cache, string key, object value)
    {
        await cache.StringSetAsync(key, Serialize(value));
    }

    static byte[] Serialize(object o)
    {
        byte[] objectDataAsStream = null;

        if (o != null)
        {
            BinaryFormatter binaryFormatter = new BinaryFormatter();
            using (MemoryStream memoryStream = new MemoryStream())
            {
                binaryFormatter.Serialize(memoryStream, o);
                objectDataAsStream = memoryStream.ToArray();
            }
        }

        return objectDataAsStream;
    }

    static T Deserialize<T>(byte[] stream)
    {
        T result = default(T);

        if (stream != null)
        {
            BinaryFormatter binaryFormatter = new BinaryFormatter();
            using (MemoryStream memoryStream = new MemoryStream(stream))
            {
                result = (T)binaryFormatter.Deserialize(memoryStream);
            }
        }

        return result;
    }
}
```

O código seguinte ilustra um método denominado `RetrieveBlogPost` que utiliza estes métodos de extensão para ler e escrever uma serializável `BlogPost` objeto para a cache de seguir o padrão de cache anulação:

```csharp
// The BlogPost type
[Serializable]
private class BlogPost
{
    private HashSet<string> tags = new HashSet<string>();

    public BlogPost(int id, string title, int score, IEnumerable<string> tags)
    {
        this.Id = id;
        this.Title = title;
        this.Score = score;
        this.tags = new HashSet<string>(tags);
    }

    public int Id { get; set; }
    public string Title { get; set; }
    public int Score { get; set; }
    public ICollection<string> Tags { get { return this.tags; } }
}
...
private async Task<BlogPost> RetrieveBlogPost(string blogPostKey)
{
    BlogPost blogPost = await cache.GetAsync<BlogPost>(blogPostKey);
    if (blogPost == null)
    {
        blogPost = await GetBlogPostFromDataSourceAsync(blogPostKey);
        await cache.SetAsync(blogPostKey, blogPost);
    }

    return blogPost;
}
```

Redis suporta comando efectuar o pipeline se uma aplicação cliente envia vários pedidos assíncronos. Redis pode multiplex os pedidos de utilizar a mesma ligação em vez de a receber e a responder aos comandos numa sequência estrito.

Esta abordagem ajuda a reduzir a latência, tornando mais eficaz utilização da rede. O fragmento de código seguinte mostra um exemplo que obtém os detalhes de duas clientes em simultâneo. O código submete dois pedidos e, em seguida, executa algumas outras processamento (não apresentado) antes de à espera de receber os resultados. O `Wait` método do objeto cache é semelhante a .NET Framework `Task.Wait` método:

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
var task1 = cache.StringGetAsync("customer:1");
var task2 = cache.StringGetAsync("customer:2");
...
var customer1 = cache.Wait(task1);
var customer2 = cache.Wait(task2);
```

A [documentação do Azure Redis Cache](https://azure.microsoft.com/documentation/services/cache/) de página no Web site da Microsoft fornece mais informações sobre como escrever aplicações de cliente que podem utilizar a Cache do Azure Redis. Informações adicionais estão disponíveis na [página utilização básica](https://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/Basics.md) no Web site da StackExchange.Redis.

A página [tubagens e multiplexers](https://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/PipelinesMultiplexers.md) no Web site da mesma fornece mais informações sobre as operações assíncronas e efectuar o pipeline com Redis e a biblioteca de StackExchange.  A secção seguinte neste artigo, utilizar Redis colocação em cache, oferece exemplos de algumas das técnicas mais avançadas que pode aplicar aos dados que são mantidos em cache um Redis.

## <a name="using-redis-caching"></a>Utilizar Redis colocação em cache

A utilização mais simples de Redis colocação em cache preocupações é pares chave-valor quando o valor for uma cadeia de comprimento arbitrário que pode conter quaisquer dados binários não interpretada. (É, essencialmente, uma matriz de bytes que pode ser tratada como uma cadeia). Este cenário foi ilustrado em aplicações de cliente de implementar Redis Cache secção neste artigo.

Tenha em atenção que as chaves também contêm dados não interpretados, para que possa utilizar qualquer informação binária como chave. Mais tempo a chave é, no entanto, mais espaço demorará para armazenar e mais tempo vai demorar para efetuar operações de pesquisa. Para textuais e facilidade de manutenção, estruturar o seu keyspace cuidadosamente e utilize as teclas significativas (mas não verboso).

Por exemplo, utilize teclas estruturadas tal como "cliente: 100" para representar a chave para o cliente com ID 100 em vez de simplesmente "100". Este esquema permite-lhe facilmente distinguir entre os valores que armazenam diferentes tipos de dados. Por exemplo, também poderá utilize a tecla "encomendas: 100" para representar a chave para a encomenda com ID 100.

Para além das constantes unidimensionais cadeias binárias, um valor num par de valor de chave Redis também pode conter mais estruturado informações, incluindo listas, define (ordenados e não ordenado) e efectuar o hash. Redis fornece um conjunto de comandos abrangente que pode manipular estes tipos e muitos destes comandos estão disponíveis para aplicações do .NET Framework através de uma biblioteca de cliente como StackExchange. A página [uma introdução à Redis tipos de dados e captações](http://redis.io/topics/data-types-intro) no Web site da Redis fornece uma descrição mais detalhada dos seguintes tipos e os comandos que pode utilizar para manipulá-los.

Esta secção resume alguns casos de utilização comum para estes tipos de dados e comandos.

### <a name="perform-atomic-and-batch-operations"></a>Executar Atómica e operações batch

Redis suporta uma série de atómica obter e definir operações em valores de cadeia. Estas operações remover os riscos de corrida possíveis que poderão ocorrer ao utilizar separada `GET` e `SET` comandos. As operações que estão disponíveis incluem:

- `INCR`, `INCRBY`, `DECR`, e `DECRBY`, que efetuar operações atómica de incremento e diminuir nos valores de dados numéricos inteiros. A biblioteca de StackExchange fornece versões sobrecarregados da `IDatabase.StringIncrementAsync` e `IDatabase.StringDecrementAsync` métodos para efetuar estas operações e devolver o valor resultante armazenado na cache. O fragmento de código seguinte ilustra como utilizar estes métodos:

  ```csharp
  ConnectionMultiplexer redisHostConnection = ...;
  IDatabase cache = redisHostConnection.GetDatabase();
  ...
  await cache.StringSetAsync("data:counter", 99);
  ...
  long oldValue = await cache.StringIncrementAsync("data:counter");
  // Increment by 1 (the default)
  // oldValue should be 100

  long newValue = await cache.StringDecrementAsync("data:counter", 50);
  // Decrement by 50
  // newValue should be 50
  ```

- `GETSET`, que obtém o valor que está associada uma tecla e esta transforma-se para um novo valor. A biblioteca StackExchange disponibiliza esta operação através de `IDatabase.StringGetSetAsync` método. O fragmento de código abaixo mostra um exemplo deste método. Este código devolve o valor atual que está associada a tecla "dados: contador" do exemplo anterior. Em seguida, repõe o valor para esta chave voltar a zero, todos os como parte da operação de mesmo:

  ```csharp
  ConnectionMultiplexer redisHostConnection = ...;
  IDatabase cache = redisHostConnection.GetDatabase();
  ...
  string oldValue = await cache.StringGetSetAsync("data:counter", 0);
  ```

- `MGET`e `MSET`, que pode devolver ou alterar um conjunto de valores de cadeia como uma única operação. O `IDatabase.StringGetAsync` e `IDatabase.StringSetAsync` métodos são sobrecarregados para suportar esta funcionalidade, conforme mostrado no exemplo seguinte:

  ```csharp
  ConnectionMultiplexer redisHostConnection = ...;
  IDatabase cache = redisHostConnection.GetDatabase();
  ...
  // Create a list of key-value pairs
  var keysAndValues =
      new List<KeyValuePair<RedisKey, RedisValue>>()
      {
          new KeyValuePair<RedisKey, RedisValue>("data:key1", "value1"),
          new KeyValuePair<RedisKey, RedisValue>("data:key99", "value2"),
          new KeyValuePair<RedisKey, RedisValue>("data:key322", "value3")
      };

  // Store the list of key-value pairs in the cache
  cache.StringSet(keysAndValues.ToArray());
  ...
  // Find all values that match a list of keys
  RedisKey[] keys = { "data:key1", "data:key99", "data:key322"};
  RedisValue[] values = null;
  values = cache.StringGet(keys);
  // values should contain { "value1", "value2", "value3" }
  ```

Também pode combinar várias operações para uma única transação Redis conforme descrito na secção transações e lotes Redis neste artigo. A biblioteca de StackExchange fornece suporte para transações através de `ITransaction` interface.

Criar um `ITransaction` objecto, utilizando o `IDatabase.CreateTransaction` método. Invocar comandos para a transação utilizando os métodos fornecidos pela `ITransaction` objeto.

O `ITransaction` interface fornece acesso a um conjunto de métodos que é semelhante das pode ser consultada pela `IDatabase` interface, exceto que todos os métodos de são assíncronos. Isto significa que só são executadas quando o `ITransaction.Execute` método é chamado. O valor que é devolvido pela `ITransaction.Execute` método indica se a transação foi criada com êxito (verdadeiro) ou se tiver falhado (false).

O fragmento de código seguinte mostra um exemplo esse incrementos e diminui dois contadores como parte da transação mesmo:

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
ITransaction transaction = cache.CreateTransaction();
var tx1 = transaction.StringIncrementAsync("data:counter1");
var tx2 = transaction.StringDecrementAsync("data:counter2");
bool result = transaction.Execute();
Console.WriteLine("Transaction {0}", result ? "succeeded" : "failed");
Console.WriteLine("Result of increment: {0}", tx1.Result);
Console.WriteLine("Result of decrement: {0}", tx2.Result);
```

Lembre-se de que as transações de Redis estão ao contrário de transações bases de dados relacionais. O `Execute` método simplesmente filas de todos os comandos que compõem a transação a ser executado e se qualquer uma delas está mal a transação está parada. Se todos os comandos tem sido na fila com êxito, cada comando é executado modo assíncrono.

Se a qualquer comando falhar, as outras continuar o processamento. Se precisar de verificar que um comando foi concluída com êxito, deve obter os resultados do comando, utilizando a propriedade do **resultado** da tarefa correspondente, conforme mostrado no exemplo acima. Ao ler a propriedade de **resultado** irá bloquear o tópico de chamada até a tarefa foi concluída.

Para mais informações, consulte a página de [transações na Redis](https://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/Transactions.md) no Web site da StackExchange.Redis.

Quando executa operações batch, pode utilizar o `IBatch` interface da biblioteca de StackExchange. Esta interface fornece acesso a um conjunto de métodos semelhantes das pode ser consultada pela `IDatabase` interface, exceto que todos os métodos de são assíncronos.

Criar um `IBatch` objecto, utilizando o `IDatabase.CreateBatch` método e, em seguida, execute o lote utilizando o `IBatch.Execute` método, conforme mostrado no seguinte exemplo. Este código simplesmente define um valor de cadeia, incrementos e diminui os mesmos contadores utilizados no exemplo anterior e apresenta os resultados:

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
IBatch batch = cache.CreateBatch();
batch.StringSetAsync("data:key1", 11);
var t1 = batch.StringIncrementAsync("data:counter1");
var t2 = batch.StringDecrementAsync("data:counter2");
batch.Execute();
Console.WriteLine("{0}", t1.Result);
Console.WriteLine("{0}", t2.Result);
```

É importante compreender que ao contrário uma transação da, se um comando num lote falhar porque está mal formação, os outros comandos poderão continuar a executar. O `IBatch.Execute` método não devolver qualquer indicação de sucesso ou falha.

### <a name="perform-fire-and-forget-cache-operations"></a>Executar fire e esquecer operações de cache

Redis suporta fire e esquecer operações utilizando comando sinalizadores. Esta situação, o cliente simplesmente inicia uma operação, mas tenha sem interesse no resultado e não aguardar que o comando para ser concluída. O exemplo abaixo mostra como executar o comando INCR como um fire e esquecer operação:

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
await cache.StringSetAsync("data:key1", 99);
...
cache.StringIncrement("data:key1", flags: CommandFlags.FireAndForget);
```

### <a name="specify-automatically-expiring-keys"></a>Especificar chaves automaticamente prestes a expirar

Quando guarda um item numa Redis cache, pode especificar um limite de tempo após o qual o item será automaticamente removido da cache. Também pode consultar mais quanto tempo uma chave tem antes que expire utilizando o `TTL` comando. Este comando está disponível para aplicações StackExchange utilizando o `IDatabase.KeyTimeToLive` método.

O fragmento de código seguinte mostra como configurar um tempo de expiração de 20 segundos numa chave e à duração restante da chave de consulta:

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
// Add a key with an expiration time of 20 seconds
await cache.StringSetAsync("data:key1", 99, TimeSpan.FromSeconds(20));
...
// Query how much time a key has left to live
// If the key has already expired, the KeyTimeToLive function returns a null
TimeSpan? expiry = cache.KeyTimeToLive("data:key1");
```

Também pode configurar o tempo de expiração para uma determinada data e hora utilizando o comando de EXPIRAÇÃO, o que está disponível na biblioteca StackExchange, tal como o `KeyExpireAsync` método:

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
// Add a key with an expiration date of midnight on 1st January 2015
await cache.StringSetAsync("data:key1", 99);
await cache.KeyExpireAsync("data:key1",
    new DateTime(2015, 1, 1, 0, 0, 0, DateTimeKind.Utc));
...
```

> _Sugestão:_ Pode remover manualmente um item da cache de utilizando o comando DEL, que se encontra disponível através da biblioteca de StackExchange como o `IDatabase.KeyDeleteAsync` método.

### <a name="use-tags-to-cross-correlate-cached-items"></a>Utilizar etiquetas para publicação em-estão relacionados itens em cache

Um conjunto de Redis é uma coleção de vários itens que partilha uma chave única. Pode criar um conjunto utilizando o comando SADD. Pode obter os itens num conjunto utilizando o comando SMEMBERS. A biblioteca StackExchange implementa o comando SADD com o `IDatabase.SetAddAsync` método e o SMEMBERS comando com o `IDatabase.SetMembersAsync` método.

Também pode combinar conjuntos existentes para criar novos conjuntos utilizando o SDIFF (conjunto diferença), POROSO (conjunto interseção) e comandos SUNION (conjunto União). A biblioteca StackExchange une estas operações na `IDatabase.SetCombineAsync` método. O primeiro parâmetro para este método Especifica a operação de conjunto a executar.

Os seguinte fragmentos de código mostram como conjuntos podem ser úteis para rapidamente armazenar e obter coleções de itens relacionados. Este código utiliza o `BlogPost` tipo que foi descrito na secção aplicações de cliente da Cache de Redis implementar neste artigo.

A `BlogPost` objeto contiver quatro campos — um ID, um título, uma classificação de pontuação e um conjunto de etiquetas. O primeiro fragmento de código abaixo mostra os dados de exemplo são utilizados para preencher uma lista c# de `BlogPost` objetos:

```csharp
List<string[]> tags = new List<string[]>()
{
    new string[] { "iot","csharp" },
    new string[] { "iot","azure","csharp" },
    new string[] { "csharp","git","big data" },
    new string[] { "iot","git","database" },
    new string[] { "database","git" },
    new string[] { "csharp","database" },
    new string[] { "iot" },
    new string[] { "iot","database","git" },
    new string[] { "azure","database","big data","git","csharp" },
    new string[] { "azure" }
};

List<BlogPost> posts = new List<BlogPost>();
int blogKey = 0;
int blogPostId = 0;
int numberOfPosts = 20;
Random random = new Random();
for (int i = 0; i < numberOfPosts; i++)
{
    blogPostId = blogKey++;
    posts.Add(new BlogPost(
        blogPostId,               // Blog post ID
        string.Format(CultureInfo.InvariantCulture, "Blog Post #{0}",
            blogPostId),          // Blog post title
        random.Next(100, 10000),  // Ranking score
        tags[i % tags.Count]));   // Tags--assigned from a collection
                                  // in the tags list
}
```

Pode armazenar os códigos para cada `BlogPost` objeto como um conjunto na cache de Redis e associá-cada conjunto com o ID da `BlogPost`. Permite que uma aplicação localizar rapidamente todas as etiquetas que pertencem a uma mensagem do blogue específica. Para ativar a procurar na direção oposta e encontrar todas as mensagens no blogue que partilham uma etiqueta específica, pode criar outro conjunto que detém o blogue novas publicações referenciar o ID de etiqueta na chave:

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
// Tags are easily represented as Redis Sets
foreach (BlogPost post in posts)
{
    string redisKey = string.Format(CultureInfo.InvariantCulture,
        "blog:posts:{0}:tags", post.Id);
    // Add tags to the blog post in Redis
    await cache.SetAddAsync(
        redisKey, post.Tags.Select(s => (RedisValue)s).ToArray());

    // Now do the inverse so we can figure how which blog posts have a given tag
    foreach (var tag in post.Tags)
    {
        await cache.SetAddAsync(string.Format(CultureInfo.InvariantCulture,
            "tag:{0}:blog:posts", tag), post.Id);
    }
}
```

Estas estruturas permitem-lhe executar várias consultas comuns muito eficazmente. Por exemplo, pode localizar e apresentar todas as etiquetas de mensagem do blogue 1 da seguinte forma:

```csharp
// Show the tags for blog post #1
foreach (var value in await cache.SetMembersAsync("blog:posts:1:tags"))
{
    Console.WriteLine(value);
}
```

Pode encontrar todas as etiquetas que são comuns no blogue, para publicar a mensagem 1 e blogue 2, efetuar uma operação de interseção conjunto, da seguinte forma:

```csharp
// Show the tags in common for blog posts #1 and #2
foreach (var value in await cache.SetCombineAsync(SetOperation.Intersect, new RedisKey[]
    { "blog:posts:1:tags", "blog:posts:2:tags" }))
{
    Console.WriteLine(value);
}
```

E que pode encontrar todas as mensagens no blogue que contêm uma etiqueta específica:

```csharp
// Show the ids of the blog posts that have the tag "iot".
foreach (var value in await cache.SetMembersAsync("tag:iot:blog:posts"))
{
    Console.WriteLine(value);
}
```

### <a name="find-recently-accessed-items"></a>Localizar acedidas recentemente itens

É uma tarefa comum obrigatório de muitas aplicações encontrar mais recentemente acedidos itens. Por exemplo, um site de blogues poderá pretender apresentar informações sobre as mensagens do blogue lidas mais recentemente.

Pode implementar esta funcionalidade utilizando uma lista de Redis. Uma lista de Redis contém vários itens que partilham a mesma chave. A lista age como uma fila terminada duplo. Pode emissão itens para o fim da lista, utilizando o LPUSH (push esquerdo) e comandos do RPUSH (push direita). Pode obter os itens a partir de um dos final da lista através dos comandos LPOP e RPOP. Também pode devolver um conjunto de elementos, utilizando os comandos LRANGE e DISPOR.

Os fragmentos de código abaixo mostram como pode realizar estas operações utilizando a biblioteca de StackExchange. Este código utiliza o `BlogPost` tipo dos exemplos anteriores. Uma mensagem no blogue está a ser lido por um utilizador, o `IDatabase.ListLeftPushAsync` método realiza o título da mensagem no blogue, para uma lista que está associada a tecla "blog:recent_posts" na Redis cache.

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
string redisKey = "blog:recent_posts";
BlogPost blogPost = ...; // Reference to the blog post that has just been read
await cache.ListLeftPushAsync(
    redisKey, blogPost.Title); // Push the blog post onto the list
```

Como são lidos mais mensagens no blogue, seus títulos são enviados para a mesma lista. A lista é ordenada por sequência na qual foram adicionados os títulos. As mensagens lidas mais recentemente do blogue estão no lado esquerdo da lista. (Se a mesma mensagem de blogue é lido mais de uma vez, terá várias entradas na lista.)

Pode apresentar os títulos das mensagens lidas mais recentemente utilizando o `IDatabase.ListRange` método. Este método leva a chave que contém a lista, um ponto de partida e um ponto final. O código seguinte obtém os títulos de 10 mensagens de blogue (itens de 0 a 9) no final mais à esquerda da lista:

```csharp
// Show latest ten posts
foreach (string postTitle in await cache.ListRangeAsync(redisKey, 0, 9))
{
    Console.WriteLine(postTitle);
}
```

Tenha em atenção que a `ListRangeAsync` método não remover itens da lista. Para executar esta tarefa, pode utilizar o `IDatabase.ListLeftPopAsync` e `IDatabase.ListRightPopAsync` métodos.

Para impedir que a lista em crescimento indefinidamente, pode de reforma periodicamente itens por limitar a lista. O fragmento de código abaixo mostra como remover tudo, mas as cinco mais à esquerda itens a partir da lista:

```csharp
await cache.ListTrimAsync(redisKey, 0, 5);
```

### <a name="implement-a-leader-board"></a>Implementar um quadro de caráter de preenchimento

Por predefinição, os itens num conjunto não são mantidos numa ordem específica. Pode criar um conjunto ordenado utilizando o comando ZADD (o `IDatabase.SortedSetAdd` método na biblioteca de StackExchange). Os itens são ordenados utilizando um valor numérico denominado uma pontuação, que é fornecida como um parâmetro para o comando.

O fragmento de código seguinte adiciona o título de uma mensagem no blogue, para uma lista ordenada. Neste exemplo, cada mensagem no blogue também tem um campo de pontuação, que contém a classificação da mensagem no blogue.

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
string redisKey = "blog:post_rankings";
BlogPost blogPost = ...; // Reference to a blog post that has just been rated
await cache.SortedSetAddAsync(redisKey, blogPost.Title, blogpost.Score);
```

Pode obter os títulos de mensagem de blogue e pontuações por pontuação ordem ascendente por utilizando o `IDatabase.SortedSetRangeByRankWithScores` método:

```csharp
foreach (var post in await cache.SortedSetRangeByRankWithScoresAsync(redisKey))
{
    Console.WriteLine(post);
}
```

> [AZURE.NOTE] A biblioteca de StackExchange também fornece o `IDatabase.SortedSetRangeByRankAsync` método, que devolve os dados na ordem de pontuação, mas não devolver os resultados.

Também pode obter itens por ordem descendente de pontuações e limitar o número de itens que são devolvidos ao fornecer parâmetros adicionais para o `IDatabase.SortedSetRangeByRankWithScoresAsync` método. O exemplo seguinte mostra os títulos e pontuações das superior 10 classificados mensagens no blogue:

```csharp
foreach (var post in await cache.SortedSetRangeByRankWithScoresAsync(
                               redisKey, 0, 9, Order.Descending))
{
    Console.WriteLine(post);
}
```

O exemplo seguinte utiliza a `IDatabase.SortedSetRangeByScoreWithScoresAsync` método, que pode utilizar para limitar os itens que são devolvidos para aqueles que se situar dentro de uma pontuação determinada intervalo:

```csharp
// Blog posts with scores between 5000 and 100000
foreach (var post in await cache.SortedSetRangeByScoreWithScoresAsync(
                               redisKey, 5000, 100000))
{
    Console.WriteLine(post);
}
```

### <a name="message-by-using-channels"></a>Mensagem por através de canais

Para além das que serve como uma cache de dados, um servidor de Redis fornece mensagens através de um mecanismo de alto desempenho publisher/subscritor. Aplicações de cliente podem subscrever um canal e outras aplicações ou serviços podem publicar mensagens para o canal. Subscrever aplicações, em seguida, irá receber estas mensagens e pode processá-las.

Redis fornece o comando de subscrever para aplicações de cliente utilizar para subscrever canais. Este comando espera o nome de uma ou mais canais no qual a aplicação irá aceitar mensagens. A biblioteca StackExchange inclui o `ISubscription` interface, que permite a uma aplicação do .NET Framework subscrever e publicar para os canais.

Criar um `ISubscription` objecto, utilizando o `GetSubscriber` método de ligação ao servidor Redis. Em seguida, ouvir para mensagens num canal, utilizando o `SubscribeAsync` método deste objeto. Exemplo de código que se segue mostra como subscrever a um canal com o nome "mensagens: blogPosts":

```csharp
ConnectionMultiplexer redisHostConnection = ...;
ISubscriber subscriber = redisHostConnection.GetSubscriber();
...
await subscriber.SubscribeAsync("messages:blogPosts", (channel, message) =>
{
    Console.WriteLine("Title is: {0}", message);
});
```

O primeiro parâmetro para a `Subscribe` método é o nome do canal. Este nome segue as mesmas convenções que são utilizadas pelo teclas na cache. O nome pode conter quaisquer dados binários, embora é aconselhável utilizar cadeias relativamente curtas, significativas para ajudar a garantir bom desempenho e manutenção.

Tenha também em atenção que o espaço utilizado pelos canais está separado do que é utilizado por chaves. Isto significa que pode ter canais e teclas que têm o mesmo nome, apesar de Isto pode tornar o código da aplicação mais difícil manter.

O segundo parâmetro é um delegado de ação. Este delegado é executado modo assíncrono sempre que aparece uma nova mensagem no canal. Este exemplo mostra simplesmente a mensagem na consola (a mensagem irá conter o título de uma mensagem no blogue).

Para publicar um canal, uma aplicação pode utilizar o comando Redis publicar. A biblioteca de StackExchange fornece o `IServer.PublishAsync` método para executar esta operação. O fragmento de código seguinte mostra como publicar uma mensagem para o canal "mensagens: blogPosts":

```csharp
ConnectionMultiplexer redisHostConnection = ...;
ISubscriber subscriber = redisHostConnection.GetSubscriber();
...
BlogPost blogpost = ...;
subscriber.PublishAsync("messages:blogPosts", blogPost.Title);
```

Existem vários pontos que deve compreender sobre o mecanismo publicar/subscrever:

- Podem subscrever múltiplos subscritores do mesmo canal e estes todos irão receber as mensagens que são publicadas desse canal.
- Subscritores só recebem mensagens que tenham sido publicadas depois de estes subscreveu. Canais não estão na memória intermédia e depois de uma mensagem é publicada, a infraestrutura de Redis envia a mensagem para cada subscritor e, em seguida, remove.
- Por predefinição, as mensagens são recebidas por subscritores pela ordem pela qual são enviadas. Num sistema altamente ativo com um grande número de mensagens e muitos subscritores e fabricantes, garantia sequencial a entrega de mensagens pode diminuir o desempenho do sistema. Se cada mensagem é independente e não é importante a ordem, pode ativar a transformação em simultâneo pelo sistema Redis, que pode ajudar a melhorar a capacidade de resposta. É possível alcançar isto num cliente StackExchange definindo PreserveAsyncOrder da ligação utilizada pelo subscritor falso:

```csharp
ConnectionMultiplexer redisHostConnection = ...;
redisHostConnection.PreserveAsyncOrder = false;
ISubscriber subscriber = redisHostConnection.GetSubscriber();
```

## <a name="related-patterns-and-guidance"></a>Orientação e padrões relacionados

O seguinte padrão também poderá ser relevante para o seu cenário quando implementar a colocação em cache nas suas aplicações:

- [Cache anulação padrão](http://msdn.microsoft.com/library/dn589799.aspx): este padrão descreve como carregar os dados a pedido para uma cache a partir de um arquivo de dados. Este padrão também ajuda a manter a consistência entre os dados que são mantidos na cache e os dados no arquivo de dados original.
- O [padrão de Sharding](http://msdn.microsoft.com/library/dn589797.aspx) fornece informações sobre como implementar a partições horizontal para ajudar a melhorar escalabilidade quando armazenar e aceder a grandes volumes de dados.

## <a name="more-information"></a>Obter mais informações

- A página de [classe MemoryCache](http://msdn.microsoft.com/library/system.runtime.caching.memorycache.aspx) no Web site da Microsoft
- A página de [documentação do Azure Redis Cache](https://azure.microsoft.com/documentation/services/cache/) no Web site da Microsoft
- A página [Azure Redis Cache perguntas mais frequentes sobre](redis-cache/cache-faq.md) no Web site da Microsoft
- A página do [modelo de configuração](http://msdn.microsoft.com/library/windowsazure/hh914149.aspx) no Web site da Microsoft
- A página de [Padrão de assíncrona baseado em tarefas](http://msdn.microsoft.com/library/hh873175.aspx) no Web site da Microsoft
- A página [tubagens e multiplexers](https://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/PipelinesMultiplexers.md) no repo StackExchange.Redis GitHub
- A página no Web site da Redis [Redis persistente](http://redis.io/topics/persistence)
- A [página de replicação](http://redis.io/topics/replication) no Web site da Redis
- A página [Redis cluster tutorial](http://redis.io/topics/cluster-tutorial) no Web site da Redis
- O [divisão: como dividir dados entre várias instâncias de Redis](http://redis.io/topics/partitioning) página no Web site da Redis
- A página [Utilizando Redis como uma Cache LRU](http://redis.io/topics/lru-cache) no Web site da Redis
- A página de [transações](http://redis.io/topics/transactions) no Web site da Redis
- A página [Redis segurança](http://redis.io/topics/security) no Web site da Redis
- A página de [volta à volta do Azure Redis Cache](https://azure.microsoft.com/blog/2014/06/04/lap-around-azure-redis-cache-preview/) no blogue do Azure
- A página [a executar o Redis numa VM de Linux CentOS no Azure](http://blogs.msdn.com/b/tconte/archive/2012/06/08/running-redis-on-a-centos-linux-vm-in-windows-azure.aspx) no Web site da Microsoft
- A página de [fornecedores de estado do ASP.NET sessão para Azure Redis Cache](redis-cache/cache-aspnet-session-state-provider.md) no Web site da Microsoft
- A página [ASP.NET fornecedor de cache de saída para Azure Redis Cache](redis-cache/cache-aspnet-output-cache-provider.md) no Web site da Microsoft
- A página de [Uma introdução à Redis tipos de dados e captações](http://redis.io/topics/data-types-intro) no Web site da Redis
- A página de [utilização básica](https://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/Basics.md) no Web site da StackExchange.Redis
- A página de [transações na Redis](https://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/Transactions.md) na repo StackExchange.Redis
- O [Guia de criação de partições de dados](http://msdn.microsoft.com/library/dn589795.aspx) no Web site da Microsoft
