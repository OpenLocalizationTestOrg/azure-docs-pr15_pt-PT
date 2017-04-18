<properties
    pageTitle="Gestor de tráfego - tráfego métodos de encaminhamento | Microsoft Azure"
    description="Este artigo irá ajudá-lo a compreender os métodos de encaminhamento de tráfego diferentes utilizados pelo Gestor de tráfego"
    services="traffic-manager"
    documentationCenter=""
    authors="sdwheeler"
    manager="carmonm"
    editor=""
/>
<tags
    ms.service="traffic-manager"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
    ms.date="10/11/2016"
    ms.author="sewhee"
/>

# <a name="traffic-manager-traffic-routing-methods"></a>Métodos de encaminhamento de tráfego de Gestor de tráfego

Gestor de tráfego Azure suporta três métodos encaminhamento de tráfego para determinar como deve encaminhar o tráfego de rede para os vários pontos finais de serviço. Gestor de tráfego aplica-se o método de encaminhamento de tráfego a cada consulta de DNS que recebe. O método de encaminhamento de tráfego determina o ponto final devolvido na resposta DNS.

O suporte técnico do Gestor de recursos do Azure para o Gestor de tráfego utiliza terminologia diferente que o modelo de implementação clássica. A tabela seguinte mostra as diferenças entre os termos de Gestor de recursos e clássico:

| Termos de Gestor de recursos | Termos clássico |
|-----------------------|--------------|
| Método de encaminhamento de tráfego | Método de balanceamento de carga |
| Método de prioridade | Método de activação pós-falha |
| Método de ponderada | Método de round robin |
| Método de desempenho | Método de desempenho |

Com base nos comentários, foi alterado a terminologia para melhorar a clareza e reduzir equívocos comuns. Não existe nenhuma diferença de funcionalidade.

Existem três métodos de encaminhamento de tráfego disponíveis no Gestor de tráfego:

- **Prioridade:** Selecione 'Priority' quando pretender utilizar um ponto final de serviço principal para todo o tráfego e fornecer cópias de segurança no caso do principal ou os pontos finais da cópia de segurança não estão disponíveis.
- **Ponderada:** Selecionar 'ponderada' quando pretender distribuir o tráfego através de um conjunto de pontos finais, quer uniformemente ou de acordo com espessuras e que pode definir.
- **Desempenho:** Selecione 'Desempenho' quando tiver os pontos finais em diferentes localizações geográficas e pretende que os utilizadores finais para utilizar o ponto final "mais próximo" em termos de latência da rede mais baixo.

Todos os perfis do Gestor de tráfego incluem a monitorização de estado de funcionamento do ponto final e o ponto final automática activação pós-falha. Para obter mais informações, consulte [Gestor de tráfego de ponto final de monitorização](traffic-manager-monitoring.md). Um perfil do Gestor de tráfego única pode utilizar apenas um método de encaminhamento de tráfego. Pode selecionar um método de encaminhamento de tráfego diferente para o seu perfil em qualquer altura. As alterações são aplicadas dentro de um minuto e, sem tempo de inatividade é suportado. Métodos de encaminhamento de tráfego podem ser combinados utilizando perfis do Gestor de tráfego aninhadas. Aninhamento permite sofisticadas e flexíveis configurações de encaminhamento de tráfego que satisfaça as necessidades das aplicações maiores e complexas. Para mais informações, consulte o artigo [perfis do Gestor de tráfego aninhadas](traffic-manager-nested-profiles.md).

## <a name="priority-traffic-routing-method"></a>Método de encaminhamento de tráfego de prioridade

Muitas vezes uma organização pretende fornecer fiabilidade para os seus serviços ao implementar um ou mais serviços de cópia de segurança no caso dos seus serviços primário correr para baixo. O método de encaminhamento de tráfego de 'Priority' permite aos clientes Azure facilmente implementar este padrão activação pós-falha.

![Gestor de tráfego Azure 'Priority' Encaminhamento de tráfego método][1]

Perfil do Gestor de tráfego de contém uma lista com prioridade alta de pontos finais de serviço. Por predefinição, o Gestor de tráfego envia todo o tráfego para o ponto final de (prioridade mais alta) principal. Se o ponto final principal não estiver disponível, o Gestor de tráfego encaminha o tráfego para o ponto final do segundo. Se os pontos de finais principais e secundários não estão disponíveis, o tráfego vai para o terceiro e assim sucessivamente. Disponibilidade do ponto final se baseia o estado configurado (ativado ou desativado) e a monitorização de ponto final em curso.

### <a name="configuring-endpoints"></a>Configurar os pontos finais

Com o Gestor de recursos do Azure, configurá-lo a prioridade de ponto final explicitamente utilizando a propriedade 'priority' para cada ponto final. Esta propriedade é um valor entre 1 e 1000. Os valores mais baixos representam uma prioridade mais alta. Os pontos finais não podem partilhar os valores da prioridade. Definir a propriedade é opcional. Quando omitido, é utilizada uma prioridade predefinida baseada na ordem de ponto final.

Com a interface clássica, a prioridade de ponto final está configurada implicitamente. A prioridade baseia-se a ordem pela qual os pontos finais são listados na definição do perfil.

## <a name="weighted-traffic-routing-method"></a>Método de encaminhamento de tráfego ponderado

O método de encaminhamento de tráfego 'Ponderado' permite-lhe para distribuir uniformemente tráfego ou para utilizar uma ponderação predefinida.

![Azure tráfego 'Ponderada' Encaminhamento de tráfego método Gestor][2]

Método de encaminhamento de tráfego ponderada, pode atribuir uma espessura de cada ponto final na configuração de perfil do Gestor de tráfego. O peso é um número inteiro de 1 a 1000. Este parâmetro é opcional. Se for omitido, o tráfego gestores utiliza uma espessura de predefinido de '1'.

Para cada consulta DNS recebida, o Gestor de tráfego escolhe aleatoriamente um extremo disponível. A probabilidade de escolher um ponto final é baseada espessuras atribuídas a todos os pontos finais disponíveis. Usar o mesmo peso em todos os resultados de pontos finais de uma distribuição de tráfego par. Utilizar espessuras superiores ou inferiores num pontos finais específicos faz com que essas pontos finais para ser devolvido mais ou menos frequentemente nas respostas DNS.

O método ponderado permite alguns cenários de útil:

- Atualização da aplicação gradual: atribuir uma percentagem de tráfego para encaminhar para um novo ponto final e aumente o tráfego gradualmente ao longo do tempo para 100%.
- Migração de aplicação para o Azure: criar um perfil com os pontos finais Azure e externos. Ajuste a espessura dos pontos finais para preferir os pontos finais de novos.
- Segurança na nuvem para capacidade adicional: Expandir rapidamente uma implementação no local para a nuvem, colocando-o por trás de um perfil do Gestor de tráfego. Quando precisar de capacidade extra na nuvem, pode adicionar ou permitir mais pontos finais e especificar qual a parte da tráfego vai para cada ponto final.

O novo portal Azure suporta a configuração de encaminhamento de tráfego ponderado. Espessura não pode ser configurada no portal do clássico. Também pode configurar espessuras utilizando o Gestor de recursos e versões clássicas do Azure PowerShell, clip e os REST APIs.

É importante compreender que respostas DNS são armazenadas em cache por clientes e por servidores DNS recursiva que os clientes utilizar para resolver nomes de DNS. Este colocação em cache, pode ter um impacto no distribuições tráfego ponderada. Quando o número de clientes e os servidores DNS recursiva for grande, distribuição de tráfego funciona conforme esperado. No entanto, quando o número de clientes ou recursiva os servidores DNS é pequeno, colocação em cache pode significativamente prejudicar a distribuição de tráfego.

Casos de utilização comum incluem:

- Ambientes de testes e desenvolvimento
- Comunicações de aplicação a aplicação
- Aplicações destinadas a uma utilizador-base estreita partilhar uma infraestrutura DNS comuns recursiva (por exemplo, empregados da empresa a ligar através de um proxy)

Estes efeitos de colocação em cache DNS são comuns a todos os tráfego com base no DNS encaminhamento sistemas, não apenas o Gestor de tráfego Azure. Em alguns casos, explicitamente limpar a cache DNS pode fornecer uma solução. Em outros casos, um método de encaminhamento de tráfego alternativo poderá ser mais adequado.

## <a name="performance-traffic-routing-method"></a>Método de encaminhamento de tráfego de desempenho

Implementar os pontos finais em duas ou mais localizações em todo o mundo pode melhorar a capacidade de resposta de muitas aplicações pelo encaminhar tráfego para a localização que esteja 'mais' para si. O método de encaminhamento de tráfego de 'Desempenho' fornece esta funcionalidade.

![Gestor de tráfego Azure 'Desempenho' Encaminhamento de tráfego método][3]

O ponto final 'mais' não é necessariamente mais próximo medido por distância geográfica. Em vez disso, o método de encaminhamento de tráfego de 'Desempenho' determina o ponto final mais próximo através da medição latência da rede. Gestor de tráfego mantém uma tabela de latência da Internet para controlar o tempo de transmissão entre intervalos de endereços IP e cada Azure Centro de dados.

Gestor de tráfego de procura o endereço IP de origem do pedido DNS a receber na tabela de latência da Internet. Gestor de tráfego escolhe um extremo disponível no Centro de dados Azure que tem a latência mais baixa nesse intervalo de endereços IP, em seguida, devolve a resposta DNS desse ponto final.

Como é explicado em [Como funciona o tráfego de Gestor](traffic-manager-how-traffic-manager-works.md), Gestor de tráfego não receber consultas de DNS diretamente a partir dos clientes. Em vez disso, as consultas DNS originam o serviço DNS recursiva que os clientes estão configurados para utilizar. Por conseguinte, o endereço IP utilizada para determinar o ponto final 'mais' não é o endereço IP do cliente, mas é o endereço IP do serviço DNS recursiva. Na prática, este endereço IP é um bom proxy para o cliente.

Gestor de tráfego actualiza regularmente a tabela de latência da Internet para ter em conta as alterações na global Internet e novas regiões Azure. No entanto, desempenho da aplicação varia com base nos variações em tempo real no caso de carga pela Internet. Encaminhamento de tráfego de desempenho não monitoriza carga um ponto final de serviço determinado. No entanto, se um ponto final ficar disponível, Gestor de tráfego não não-o na respostas a consulta de DNS.

Pontos a tenha em atenção:

- Se o seu perfil contém vários pontos finais na mesma Azure região, em seguida, Gestor de tráfego distribui tráfego uniformemente nos pontos finais disponíveis na região. Se preferir uma distribuição de tráfego diferentes dentro de uma região, pode utilizar [perfis do Gestor de tráfego aninhadas](traffic-manager-nested-profiles.md).

- Se todos os pontos finais activados numa região Azure determinado são degradados, Gestor de tráfego distribui tráfego por todos os outros pontos finais disponíveis em vez do ponto final mais próximo seguinte. Esta lógica impede que ocorram por sobrecarga não o ponto final mais próximo seguinte uma falha em cascata. Se pretender definir uma sequência de activação pós-falha preferido, utilize [aninhadas perfis do Gestor de tráfego](traffic-manager-nested-profiles.md).

- Quando utilizar o método de encaminhamento de tráfego de desempenho com os pontos finais externos ou os pontos finais aninhados, é necessário especificar a localização desses pontos finais. Selecione a região Azure mais próxima da sua implementação. Essas localizações são os valores suportados pela tabela de latência da Internet.

- O algoritmo de que seleciona o ponto final é determinista. São dirigidas repetidas consultas de DNS do mesmo cliente para o ponto final do mesmo. Normalmente, os clientes utilizam os servidores DNS diferentes recursiva quando em viagem. O cliente pode ser encaminhado para um ponto final de diferentes. Encaminhamento de também pode ser afetado pelas actualizações para a tabela de latência da Internet. Por conseguinte, o método de encaminhamento de tráfego de desempenho não garante que um cliente é sempre encaminhado para o ponto final do mesmo.

- Quando altera a tabela de latência da Internet, poderá reparar que alguns clientes são direcionados para um ponto final de diferentes. Esta alteração encaminhamento é mais precisa com base em dados de latência atual. Estas atualizações são essenciais para manter a precisão do encaminhamento de tráfego desempenho medida na Internet continuamente evolução deste.

## <a name="next-steps"></a>Próximos passos

Obter informações sobre como desenvolver aplicações de alta disponibilidade utilizando o [Gestor de tráfego de ponto final de monitorização](traffic-manager-monitoring.md)

Saiba como [criar um perfil do Gestor de tráfego](traffic-manager-manage-profiles.md)

<!--Image references-->
[1]: ./media/traffic-manager-routing-methods/priority.png
[2]: ./media/traffic-manager-routing-methods/weighted.png
[3]: ./media/traffic-manager-routing-methods/performance.png
