<properties
   pageTitle="Expirar dados DocumentDB com a hora à live | Microsoft Azure"
   description="Com o TTL, Microsoft Azure DocumentDB fornece a capacidade de que os documentos automaticamente eliminados do sistema após um período de tempo."
   services="documentdb"
   documentationCenter=""
   keywords="TTL"
   authors="kiratp"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="documentdb"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/28/2016"
   ms.author="kipandya"/>

# <a name="expire-data-in-documentdb-collections-automatically-with-time-to-live"></a>Expirar dados coleções de DocumentDB automaticamente com a hora à live

Aplicações podem produzir e armazenar grandes quantidades de dados. Algumas destes dados, como o computador gerado dados, registos de início e utilizador sessão eventos informações só sejam torna útil para um período de tempo finito. Assim que os dados ficam excesso às necessidades da aplicação que é seguro limpar estes dados e reduzir as necessidades de armazenamento de uma aplicação.

Com "hora para live" ou TTL, Microsoft Azure DocumentDB fornece a capacidade de que os documentos automaticamente eliminados da base de dados após um período de tempo. A hora predefinida para live pode definir nível da coleção de e substituída com base por documento. Assim que TTL estiver definido, como uma predefinição de coleções de sites ou a um nível de documento, DocumentDB removerá automaticamente os documentos que existem após esse período de tempo, em segundos, uma vez que a última modificação.

Tempo para encontram-se na DocumentDB utiliza um desvio contra quando o documento foi modificado pela última vez. Para fazê-lo-utiliza o campo de _ts que existe em cada documento. O campo _ts é uma estilo de unix época carimbo data/hora que representa a data e hora. O campo de _ts é atualizado, sempre que um documento é modificado. 

## <a name="ttl-behavior"></a>Comportamento TTL

A funcionalidade TTL é controlada pelo propriedades TTL em dois níveis - nível da coleção de e o nível de documento. Os valores são definidos em segundos e são tratados como uma delta a partir do _ts que o documento foi última modificação em.

 1.  DefaultTTL para a coleção de
  * Se faltar (ou definido como nulo), os documentos não são eliminados automaticamente.
  
  * Se a apresentar e o valor for "-1" = infinito – documentos não expirem por predefinição
  
  * Se a apresentar e o valor for algum número ("n") – documentos expirarem "n" segundos após da última modificação

 2.  TTL para os documentos: 
  * A propriedade é aplicável apenas se DefaultTTL estiver presente para a coleção de elemento principal.
  
  * Substitui o valor de DefaultTTL para a coleção de elemento principal.

Assim que o documento tiver expirado (ttl + _ts > = hora atual do servidor), o documento está marcado como "expiradas". Operação não é permitida nestes documentos depois deste período de tempo e serão excluídos partir dos resultados de quaisquer consultas executadas. Os documentos são eliminados física no sistema e são eliminados em segundo plano forma oportunista tarde. Isto não consumir qualquer [Pedido de unidades (RUs)](documentdb-request-units.md) a partir do orçamento de coleções de sites.

A lógica acima pode ser apresentada na seguinte matriz:

|       | DefaultTTL em falta/não definir na coleção de | DefaultTTL = -1 coleção | DefaultTTL = "n" coleção|
| ------------- |:-------------|:-------------|:-------------|
| TTL em falta no documento| Nada para substituir ao nível do documento, uma vez que o documento e de coleções de sites não tenham nenhum conceito de TTL. | Não existem documentos nesta coleção de irão expirar. | Os documentos nesta coleção de irão expirar quando decorrida n de intervalo. |
| TTL = -1 no documento | Nada para substituir ao nível de documentos desde a coleção de não define a propriedade de DefaultTTL pode substituir um documento. TTL num documento é interpretada anulado pelo sistema. | Não existem documentos nesta coleção de irão expirar. | O documento com TTL =-1 nesta coleção de nunca irá expirar. Todos os outros documentos irão expirar após intervalo "n". |
|  TTL = n no documento | Nada para substituir ao nível do documento. TTL num documento no anulado interpretada pelo sistema. | O documento com TTL = n expirará após n de intervalo, em segundos. Outros documentos irão herdar o intervalo de -1 e nunca expirar. | O documento com TTL = n expirará após n de intervalo, em segundos. Outros documentos irão herdam a coleção de intervalo "n". |


## <a name="configuring-ttl"></a>Configurar o TTL

Por predefinição, o TTL é desativada por predefinição, todas as coleções de DocumentDB e em todos os documentos.

## <a name="enabling-ttl"></a>Ativar o TTL

Para ativar o TTL uma coleção de ou os documentos numa coleção, tem de definir a propriedade DefaultTTL de uma coleção de -1 ou um número positivo diferente de zero. A definição de DefaultTTL como-1 significa que por predefinição, todos os documentos na coleção de residirão uma eternidade, mas o DocumentDB serviço deve monitorizar nesta coleção de documentos que tenham substituído esta predefinição.

## <a name="configuring-default-ttl-on-a-collection"></a>Configurar o TTL predefinido numa coleção de

É possível configurar uma hora predefinida para live num nível de coleções de sites. 

Para definir o valor TTL numa coleção, tem de fornecer um número positivo diferente de zero, que indica o período, em segundos, a expirar todos os documentos na coleção de após o carimbo de hora última modificação do documento (_ts).

Em alternativa, pode configurar a predefinição para -1, implica que inseriu para a coleção de todos os documentos residirão indefinidamente por predefinição.

## <a name="setting-ttl-on-a-document"></a>Definição TTL num documento

Para além de definir uma predefinição TTL numa coleção de pode definir TTL específico num nível de documento. Este procedimento irá substituir a predefinição da coleção de.

Para definir o valor TTL num documento, tem de fornecer um número positivo diferente de zero, que indica o período, em segundos, a expirar o documento após o último carimbo de hora de modificação do documento (_ts).

Para definir o deslocamento este termo, defina o campo TTL no documento.

Se um documento não tem nenhum campo TTL, em seguida, a predefinição da coleção de será aplicada.

Se TTL estiver desativado nível da coleção de, o campo TTL no documento será ignorado até TTL é activado novamente na coleção de.


## <a name="extending-ttl-on-an-existing-document"></a>Expandir TTL no documento existente

Pode repor o TTL num documento ao efetuar qualquer operação de escrita no documento. Este procedimento irá definir o _ts como a hora atual e a contagem decrescente para o termo de documento, tal como definido pelo ttl, vai começar novamente.

Se pretender alterar o valor ttl de um documento, pode atualizar o campo à medida que pode fazer com qualquer outro campo pode ser definido.


## <a name="removing-ttl-from-a-document"></a>Remover TTL a partir de um documento

Se tiver sido definido um TTL num documento e que já não pretende esse documento a expirar, em seguida, pode obter o documento, remova o campo TTL e substitui o documento no servidor.

Quando o campo TTL é removido do documento, a predefinição da coleção de será aplicada.

Para parar de um documento a partir de expirar e não herdam a partir da coleção de, em seguida, é necessário definir o valor TTL como -1.


## <a name="disabling-ttl"></a>Desativar o TTL

Para desativar TTL totalmente numa coleção e parar o processo de fundo a partir do está à procura de documentos expirados que a propriedade DefaultTTL na coleção de deve ser eliminada.

Eliminar esta propriedade é diferente do defini-la para -1. Definição para novos documentos do meio-1 adicionado à coleção de residirão uma eternidade mas pode substituí-lo em específicos documentos na coleção de.

Remover esta propriedade totalmente de coleção de significa que que não existem documentos irão expirar, mesmo se existem documentos que tenham explicitamente substituídos por defeito anterior.


## <a name="faq"></a>PERGUNTAS MAIS FREQUENTES

**O que irá TTL custar-me?**

Não há custo adicional para definir um TTL num documento.

**Quanto tempo vai demorar para eliminar o meu documento assim que estiver o TTL o?**

Os documentos são marcados como indisponíveis assim que o documento tiver expirado (ttl + _ts > = hora atual do servidor). Operação não é permitida nestes documentos depois deste período de tempo e serão excluídos partir dos resultados de quaisquer consultas executadas. Os documentos são eliminados física pelo sistema em segundo plano. Isto não irá consumir qualquer RUs a partir do orçamento a coleção.

**Se demora um período de tempo para eliminar os documentos, contam para minha quota (e fatura) até que tenham obtenham eliminado?**

Não, assim que o documento tiver expirado não será efetuada para o armazenamento destes documentos e o tamanho dos documentos não contará para a quota de armazenamento para uma coleção de.

**TTL num documento terão qualquer impacto na taxas RU?**

Não, não irá existir qualquer impacto no taxas RU para operações realizadas em qualquer documento dentro DocumentDB.

**Irá eliminar de impacto débito que posso ter aprovisionado minha coleção de documentos?**

Não, servir pedidos contra a sua coleção de receberá prioridade a executar o processo de fundo para eliminar os seus documentos. Adicionar TTL a qualquer documento não irá afetar esta.

**Quando um documento expira, quanto tempo-permanecerá na minha coleção até ser eliminada?**

Assim que o documento expirar já não estar acessível. A hora exata um documento irá permanecer na sua coleção de antes de realmente eliminado é que não sejam determinista e será baseado nos quando o processo de fundo é possível eliminar o documento.

**Documentos expirados são eliminados em todos os nós ou é "eventualmente consistent?"**

O documento ficará indisponível em simultâneo em todos os nós e em todas as regiões.

**Existe um custo RU para monitorizar os TTL tarefas em segundo plano?**

Não, não há custo RU para esta situação.

**Com que frequência é expiração de TTL com dada?**

Verificação de expiração dos TTL não ocorre como um processo de fundo. Quando responder a um pedido de serviço back-end será efetuada inline as verificações e excluir todos os documentos que expiraram. A eliminação do documento física é o único processo que modo assíncrono é executado em segundo plano. A frequência deste processo é determinada pelas RUs disponíveis na coleção de.

**É que a funcionalidade TTL apenas serem aplicadas a documentos inteiros ou pode posso expirar valores de propriedade do documento individuais?**

TTL aplica-se para todo o documento. Se gostaria de expirar apenas uma parte de um documento, em seguida, recomendamos que extrair a parte do documento principal na um documento separado "ligado" e, em seguida, utilizar o TTL nesse documento extraído.

**A funcionalidade o TTL tem quaisquer requisitos de indexação específicos?**

Sim. A coleção de tem de ter a [indexação política definida](documentdb-indexing-policies.md) para lenta ou consistente. Tentar definir DefaultTTL numa colecção de com a indexação conjunto para nenhum irá resultar num erro, tal como desativar a indexação numa coleção de que tem um DefaultTTL já configurou a tentar.


## <a name="next-steps"></a>Próximos passos

Para saber mais sobre Azure DocumentDB, consulte a página de [*documentação*](https://azure.microsoft.com/documentation/services/documentdb/) do serviço.




