<properties
pageTitle="Como atualizar um serviço na nuvem | Microsoft Azure"
description="Saiba como atualizar a serviços em nuvem no Azure. Saiba como uma atualização num serviço na nuvem avança para garantir a disponibilidade."
services="cloud-services"
documentationCenter=""
authors="Thraka"
manager="timlt"
editor=""/>
<tags
ms.service="cloud-services"
ms.workload="tbd"
ms.tgt_pltfrm="na"
ms.devlang="na"
ms.topic="article"
ms.date="08/10/2016"
ms.author="adegeo"/>

# <a name="how-to-update-a-cloud-service"></a>Como atualizar um serviço na nuvem

## <a name="overview"></a>Descrição geral
Em 10.000 pés, a atualizar um serviço na nuvem, incluindo as suas funções e o convidado sistema operativo, é um processo três passos. Em primeiro lugar, os binários e ficheiros de configuração para o novo serviço na nuvem ou a versão do SO devem ser carregados. Em seguida, Azure reserva de recursos cluster e de rede para o serviço de nuvem com base nos requisitos da nova versão do serviço na nuvem. Por fim, Azure executa uma utilização gradual incrementada atualizar o inquilino para a nova versão ou convidado sistema operativo, preservando a sua disponibilidade. Este artigo aborda os detalhes de neste último passo-a atualização de ficha técnica em.

## <a name="update-an-azure-service"></a>Atualizar um serviço Azure

Azure organiza as instâncias de funções em agrupamentos lógicos denominados domínios atualização (UD). Domínios atualização (UD) são conjuntos de lógicos de instâncias de funções que são atualizadas como um grupo.  Atualizações Azure a nuvem do serviço uma UD cada vez, que permite instâncias na outras UDs para continuar a servir o tráfego.

O número predefinido de domínios para a atualização for a 5. Pode especificar um número diferente de domínios para a atualização ao incluir o atributo upgradeDomainCount no ficheiro de definição do serviço (.csdef). Para mais informações sobre o atributo upgradeDomainCount, consulte o artigo [WebRole esquema](https://msdn.microsoft.com/library/azure/gg557553.aspx) ou [Esquema de WorkerRole](https://msdn.microsoft.com/library/azure/gg557552.aspx).

Ao efetuar uma atualização no local de uma ou mais funções no seu serviço, o Azure atualiza conjuntos de instâncias de função de acordo com o domínio de atualização aos quais pertencem. Atualizações Azure todas as ocorrências de um determinado domínio atualização – pará-los, atualizar, colocá-las trás online –, em seguida, move o cursor para o domínio seguinte. Por parar apenas as instâncias em execução no domínio de atualização atual, Azure torna-se de que uma atualização ocorre com o impacto menos possível para o serviço em execução. Para mais informações, consulte o artigo [como funciona a atualização](#howanupgradeproceeds) mais adiante.

> [AZURE.NOTE] Enquanto o termos **Atualizar** e **Atualizar** tem significado ligeiramente diferente no contexto Azure, podem ser utilizadas alternadamente para os processos e as descrições das funcionalidades neste documento.

Tem de definir o seu serviço, pelo menos, duas instâncias de uma função para essa função a ser atualizados no local sem tempo de inatividade. Se o serviço é constituída por apenas uma instância de uma função, o serviço vai estar disponível até que tenha terminado a atualização no local.

Este tópico abrange as seguintes informações sobre as atualizações Azure:

-   [Permitida alterações do serviço durante uma atualização](#AllowedChanges)
-   [Como avança uma atualização](#howanupgradeproceeds)
-   [Recuperação de uma atualização](#RollbackofanUpdate)
-   [Iniciar várias operações mutating uma implementação em curso](#multiplemutatingoperations)
-   [Distribuição de funções em vários domínios atualização](#distributiondfroles)

<a name="AllowedChanges"></a>
## <a name="allowed-service-changes-during-an-update"></a>Permitida alterações do serviço durante uma atualização
A tabela seguinte mostra as alterações permitidas para um serviço durante uma atualização:

|Alterações permitido alojamento, serviços e funções|Atualização no local|Faseada (trocar VIP)|Eliminar e implemente novamente|
|---|---|---|---|
|Versão do sistema operativo|Sim|Sim|Sim
|Nível de fidedignidade .NET|Sim|Sim|Sim|
|Tamanho de máquina virtual<sup>1</sup>|Sim<sup>2</sup>|Sim|Sim|
|Definições do armazenamento local|Aumentar apenas<sup>2</sup>|Sim|Sim|
|Adicionar ou remover funções num serviço|Sim|Sim|Sim|
|Número de ocorrências de uma determinada função|Sim|Sim|Sim|
|Número ou tipo de pontos finais para um serviço|Sim<sup>2</sup>|N|Sim|
|Nomes e os valores das definições de configuração|Sim|Sim|Sim|
|Valores (mas não nomes) das definições de configuração|Sim|Sim|Sim|
|Adicionar novos certificados|Sim|Sim|Sim|
|Alterar certificados existentes|Sim|Sim|Sim|
|Implementar o novo código|Sim|Sim|Sim|
<sup>1</sup> Alterar tamanho limitada ao subconjunto de tamanhos de disponíveis para o serviço de nuvem.

<sup>2</sup> Requer o Azure SDK 1,5 ou versões posteriores.

> [AZURE.WARNING] Alterar o tamanho de máquina virtual irá destroy dados locais.


Os itens seguintes não são suportados durante uma atualização:

-   Mudar o nome de uma função. Remover e, em seguida, adicione a função com o novo nome.
-   A alteração da contagem actualização do domínio.
-   Diminuir o tamanho dos recursos locais.

Se estiver a fazer outras atualizações à definição do seu serviço, tal como diminuir o tamanho das recursos locais, tem de executar uma atualização de trocar VIP em vez disso. Para mais informações, consulte o artigo [Trocar implementação](https://msdn.microsoft.com/library/azure/ee460814.aspx).

<a name="howanupgradeproceeds"></a>
## <a name="how-an-upgrade-proceeds"></a>Como avança uma atualização
Pode decidir se pretende atualizar todas as funções no seu serviço ou uma única função no serviço. Em ambos os casos, todas as instâncias de cada função que está a ser actualizado e que pertençam para o primeiro domínio atualização são paradas, atualizadas e colocadas novamente online. Assim que estiverem novamente online, as instâncias no domínio de segundo atualização são paradas, atualizadas e colocadas novamente online. Um serviço na nuvem pode conter no máximo de uma atualização de ativa cada vez. A atualização é executada sempre contra a versão mais recente do serviço na nuvem.

O diagrama seguinte ilustra como a atualização funciona se estiver a atualizar todas as funções no serviço:

![Serviço de atualização] (media/cloud-services-update-azure-service/IC345879.png "Serviço de atualização")

Este diagrama seguinte ilustra como a atualização funciona se estiver a atualizar apenas uma única função:

![Função de atualização] (media/cloud-services-update-azure-service/IC345880.png "Função de atualização")  

> [AZURE.NOTE] Ao atualizar um serviço a partir de uma única ocorrência para várias instâncias seu serviço será importado para baixo enquanto a atualização é executada, devido a forma Azure atualiza os serviços. A disponibilidade de serviço do responsável contrato de nível do serviço só se aplica aos serviços que são implementados com mais de uma instância. A lista seguinte descreve como os dados em cada unidade são afetados pela cada cenário de atualização de serviço Azure:
>
>Reinicie VM:
>
-   C: preservados
-   D: preservados
-   E: preservados
>
>Reinicie portal:
>
-   C: preservados
-   D: preservados
-   E: destruídos
>
>Portal Reimage:
>
-   C: preservados
-   D: destruídas
-   E: destruídos

>Atualização no local:
>
-   C: preservados
-   D: preservados
-   E: destruídos
>
>Migração de nó:
>
-   C: destruídos
-   D: destruídas
-   E: destruídos

>Tenha em atenção que, na lista acima, a unidade e: representa a unidade de raiz a função e não deve ser codificada. Em alternativa, utilize a variável de ambiente % RoleRoot % para representar a unidade.

>Para minimizar o tempo de inatividade ao atualizar um serviço de ocorrência única, implementar um novo serviço com várias instâncias para o servidor de teste e efetue uma trocar VIP.

Durante uma atualização automática, o controlador de ferro Azure periodicamente avalia o estado de funcionamento do serviço na nuvem para determinar quando for seguro para guiá-lo a UD seguinte. Este estado de funcionamento a avaliação será executada numa base por função e considera apenas as instâncias a versão mais recente (ou seja, instâncias de UDs que já foram efectuei). Verifica se que um número mínimo de instâncias de função, para cada função, atingiram terminal bom estado.

### <a name="role-instance-start-timeout"></a>Tempo limite de início de instância de função
O controlador de ferro irá esperar 30 minutos para cada instância de função alcançar um Estado de introdução. Se a duração de tempo limite decorrida, o controlador de ferro irão continuar a andar para a próxima instância de função.

<a name="RollbackofanUpdate"></a>
## <a name="rollback-of-an-update"></a>Recuperação de uma atualização
Azure dá flexibilidade na gestão de serviços durante uma atualização, permitindo-lhe iniciar operações adicionais de um serviço, depois do pedido de actualização inicial for aceite pelo controlador de ferro Azure. Só pode ser executada uma reversão quando uma atualização (alteração da configuração) ou atualização está no estado de **em curso** no processo de implementação. Uma atualização ou atualização é considerada em curso desde há pelo menos uma instância do serviço que não tenha sido atualizada para a nova versão. Para testar se uma reversão é permitida, verifique o valor do sinalizador RollbackAllowed, devolvido pela operações [Obter implementação](https://msdn.microsoft.com/library/azure/ee460804.aspx) e [Obter propriedades do serviço de nuvem](https://msdn.microsoft.com/library/azure/ee460806.aspx) , está definido como verdadeiro.

> [AZURE.NOTE] Apenas faz sentido para telefonar a anular uma atualização **no local** ou atualizar uma vez que VIP trocar actualizações envolvem substituição de uma instância em execução inteira do seu serviço com o outro.

Recuperação de uma atualização em curso tem os seguintes efeitos no processo de implementação:

-   As ocorrências de funções que não tivessem sido atualizadas ou atualizadas para a nova versão não são atualizadas ou atualizadas, uma vez que essas instâncias ainda estão a ser executado a versão de destino do serviço de.
-   As ocorrências de funções que já tinham sido atualizadas ou atualizadas para a nova versão do pacote do serviço (\*.cspkg) ficheiro ou a configuração do serviço (\*.cscfg) ficheiro (ou ambos os ficheiros) são revertidos para a versão anterior à atualização destes ficheiros.

Esta é a funcionalidade fornecida pelas seguintes funcionalidades:

-   A [Reposição atualizar ou atualizar a](https://msdn.microsoft.com/library/azure/hh403977.aspx) operação, que pode ser designado por uma atualização de configuração (acionado ao [Alterar a configuração de implementação](https://msdn.microsoft.com/library/azure/ee460809.aspx)de chamada) ou uma atualização (acionado ao [Atualizar a implementação](https://msdn.microsoft.com/library/azure/ee460793.aspx)de chamada) desde há pelo menos uma instância no serviço que não tenha sido atualizado para a nova versão.
-   O elemento bloqueado e o elemento RollbackAllowed, que são devolvidos como parte do corpo da resposta das operações de [Obter implementação](https://msdn.microsoft.com/library/azure/ee460804.aspx) e [Obter propriedades do serviço de nuvem](https://msdn.microsoft.com/library/azure/ee460806.aspx) :
    1.  O elemento bloqueado permite-lhe detetar quando uma operação de mutating pode ser chamada sobre uma determinado implementação.
    2.  O elemento RollbackAllowed permite-lhe detetar quando a operação [Anulação atualizar ou atualizar](https://msdn.microsoft.com/library/azure/hh403977.aspx) pode ser designado por uma determinado implementação.

    Para realizar uma reversão, não tem de verificar o bloqueado e os elementos de RollbackAllowed. Basta para confirmar que RollbackAllowed está definido como verdadeiro. Estes elementos são devolvidos apenas se estes métodos são invocar utilizando o cabeçalho do pedido definido como "x-ms-versão: 2011-10-01" ou uma versão posterior. Para mais informações acerca dos cabeçalhos de controlo de versões, consulte o artigo [o controlo de versões do serviço de gestão](https://msdn.microsoft.com/library/azure/gg592580.aspx).

Existem algumas situações onde uma reversão de uma atualização ou atualização não é suportada, estes são da seguinte forma:

-   Redução de recursos locais - se a atualização aumentar os recursos locais para uma função de plataforma do Azure não permite a reverter. 
-   Limitações de quota - se a atualização foi uma escala de cores para baixo operação que já não poderão ter quota de cluster suficientes para concluir a operação de reposição da versão anterior. Cada subscrição Azure tem uma quota associada que especifica o número máximo de núcleos que pode ser consumida por todos os serviços alojados que pertencem a esse subscrição. Se efetuar uma reversão de uma determinada actualização seria a sua subscrição através de quota, em seguida, que uma reversão não irão estar ativada.
-   Condição de corrida - se tiver concluído a atualização inicial, uma reversão não é possível.

Um exemplo de quando a recuperação de uma atualização poderá ser útil é se estiver a utilizar a operação de [Implementação de atualização](https://msdn.microsoft.com/library/azure/ee460793.aspx) no modo manual para controlar a velocidade com que uma atualização no local principal para o Azure alojado serviço está a ser lançada.

Durante a implementação de actualização [Atualizar implementação](https://msdn.microsoft.com/library/azure/ee460793.aspx) de chamadas no modo manual e começar a guiá-lo para a atualização de domínios. Se a determinada altura, à medida que monitorizar a atualização, tenha em atenção algumas instâncias de função nos domínios atualização primeiros que examinar tem deixar de responder, pode ligar a operação [Anulação atualizar ou atualizar](https://msdn.microsoft.com/library/azure/hh403977.aspx) no processo de implementação que saiam inalterada as instâncias que não tivessem sido atualizadas e instâncias de anulação que tivessem sido atualizadas para o pacote de serviço anterior e a configuração.

<a name="multiplemutatingoperations"></a>
## <a name="initiating-multiple-mutating-operations-on-an-ongoing-deployment"></a>Iniciar várias operações mutating uma implementação em curso
Em alguns casos pretende iniciar várias operações mutating simultâneas uma implementação em curso. Por exemplo, pode efetuar uma atualização de serviço e, enquanto está a ser lançada essa actualização ao longo do seu serviço, que pretende fazer algumas alterações, por exemplo, a filmar voltar a atualização, aplicar uma atualização diferente ou mesmo eliminar a implementação. Um caso em que esta poderá ser necessária é se uma atualização do serviço contém buggy código que faz com que uma instância de função atualizado para repetidamente uma falha de sistema. Neste caso, o controlador de ferro Azure não conseguir progredir na aplicação do que uma vez que um número de instâncias do domínio actualizado insuficiente é Saudável de atualização. Este estado é referido como um *preso implementação*. Pode descolar de implementação reverter a atualização ou aplicando uma atualização fresca parte superior da uma falha um.

Assim que o pedido inicial para atualizar ou atualizar o serviço foi recebido pelo controlador de ferro Azure, pode começar a operações mutação subsequentes. Isto é, não tem de aguardar a operação inicial concluir antes de poder iniciar outra operação mutating.

Iniciar uma segunda operação de atualização enquanto está a decorrer a primeira actualização fará semelhante a operação de reposição da versão anterior. Se a segunda actualização estiver no modo automático, o primeiro domínio atualização será atualizado imediatamente, possivelmente levando a ocorrências de vários domínios atualização a ser offline ao mesmo ponto no tempo.

As operações mutating são os seguintes: [Alterar a configuração de implementação](https://msdn.microsoft.com/library/azure/ee460809.aspx), [Atualizar implementação](https://msdn.microsoft.com/library/azure/ee460793.aspx), [Estado de implementação de atualização](https://msdn.microsoft.com/library/azure/ee460808.aspx), [Eliminar implementação](https://msdn.microsoft.com/library/azure/ee460815.aspx)e [Anulação atualizar ou atualizar](https://msdn.microsoft.com/library/azure/hh403977.aspx).

Duas operações [Obter implementação](https://msdn.microsoft.com/library/azure/ee460804.aspx) e [Obter propriedades do serviço de nuvem](https://msdn.microsoft.com/library/azure/ee460806.aspx), devolvem o sinalizador de Locked que pode ser analisado para determinar se é possível invocar uma operação de mutating na implementação determinado.

Para ligar para a versão dos seguintes métodos que devolve o sinalizador de Locked, tem de definir o cabeçalho do pedido para "x-ms-versão: 2011-10-01" ou uma versão posterior. Para mais informações acerca dos cabeçalhos de controlo de versões, consulte o artigo [o controlo de versões do serviço de gestão](https://msdn.microsoft.com/library/azure/gg592580.aspx).

<a name="distributiondfroles"></a>
## <a name="distribution-of-roles-across-upgrade-domains"></a>Distribuição de funções em vários domínios atualização
Azure distribui instâncias de uma função uniformemente por um determinado número de domínios para a atualização, que pode ser configurado como parte do ficheiro de definição (.csdef) do serviço. O número máximo de domínios para a atualização é 20 e a predefinição é 5. Para mais informações sobre como modificar o ficheiro de definição de serviço, consulte o [Esquema de definição de serviço do Azure (.csdef ficheiro)](cloud-services-model-and-package.md#csdef).

Por exemplo, se a sua função tiver dez instâncias, por predefinição cada domínio atualização contém duas instâncias. Se a sua função tiver 14 instâncias, em seguida, quatro dos domínios para a atualização contêm três instâncias e um domínio quinto contém dois.

Atualização domínios estão identificados com um índice baseado em zero: o primeiro domínio atualização têm um ID de 0 e o segundo domínio atualização apresenta um ID de 1 e assim sucessivamente.

O diagrama seguinte ilustra como um serviço que contém duas funções são distribuídos quando o serviço define dois domínios de atualização. O serviço está em execução oito instâncias da função web e nove instâncias da função de trabalho.

![Distribuição dos domínios para a atualização] (media/cloud-services-update-azure-service/IC345533.png "Distribuição dos domínios para a atualização")

> [AZURE.NOTE] Tenha em atenção que Azure controla a forma como as instâncias são atribuídas em vários domínios atualização. Não é possível especificar quais instâncias estão alocadas à qual o domínio.

## <a name="next-steps"></a>Próximos passos
[Como gerir serviços em nuvem](cloud-services-how-to-manage.md)  
[Como monitorizar a serviços em nuvem](cloud-services-how-to-monitor.md)  
[Como configurar os serviços em nuvem](cloud-services-how-to-configure.md)  
