Fábrica de dados é um serviço de inquilino com várias que tem os seguintes limites de predefinido no local para se certificar de que as subscrições do cliente são protegidas a partir de outras das cargas de trabalho. Muitos dos limites podem ser facilmente aumentados para a sua subscrição por excesso para o limite máximo ao contactar o suporte. 

**Recurso** | **Limite predefinido** | **Limite máximo**
-------- | ------------- | -------------
fábricas de dados de uma subscrição do Azure | 50 | [Contactar o suporte](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)
tubagens dentro de uma fábrica de dados | 2500 | [Contactar o suporte](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)
conjuntos de dados dentro de uma fábrica de dados | 5000 | [Contactar o suporte](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)
setores em simultâneo por conjunto de dados | 10 | 10
bytes por objecto para em curso objetos <sup>1</sup> | 200 KB | 2000 KB
bytes por objeto para o conjunto de dados e de objetos do serviço ligadas <sup>1</sup> | 100 KB | 2000 KB
HDInsight núcleos de cluster a pedido dentro de uma subscrição <sup>2</sup> | 48 | [Contactar o suporte](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)
Nuvem dados movimento unidade <sup>3</sup> | 8 | [Contactar o suporte](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)
Repetir a contagem de tubagem atividade execuções | 1000 | MaxInt (32 bits)

<sup>1</sup> em curso, conjunto de dados e objetos de serviço ligadas representam um agrupamento lógico da sua carga de trabalho. Limites para estes objectos não se relacionam com a quantidade de dados, pode mover e processar com o serviço do Azure fábrica de dados. Fábrica de dados foi concebida para dimensionar para processar petabytes de dados.

<sup>2</sup> a pedido HDInsight núcleos são atribuídos a terminar a subscrição que contém a fábrica de dados. Como resultado, o limite de acima é a fábrica de dados impostas limite de core para núcleos de HDInsight a pedido e é a diferença entre o limite de core associado à sua subscrição Azure.

<sup>3</sup> unidade de movimento de dados na nuvem (DMU) está a ser utilizada numa operação de cópia na nuvem para nuvem. É uma medida que representa a potência (uma combinação de alocação de recursos de rede, memória e CPU) de uma única unidade na fábrica de dados. É possível alcançar mais elevado débito de cópia, tirando partido mais DMUs para alguns cenários. Consulte a secção de [unidades de movimento de dados de nuvem](../../articles/data-factory/data-factory-copy-activity-performance.md#cloud-data-movement-units) em detalhes.

**Recurso** | **Limite inferior predefinido** | **Limite mínimo**
-------- | ------------------- | -------------
Intervalo de agendamento | 15 minutos | 15 minutos
Intervalo entre tentativas de repetição | 1 segundo | 1 segundo
Repetir o valor de tempo limite | 1 segundo | 1 segundo


### <a name="web-service-call-limits"></a>Limites de chamada de serviço Web

Gestor de recursos do Azure tem limites para chamadas de API. Pode fazer chamadas à API taxa de juro dentro dos [limites de API do Gestor de recursos do Azure](../azure-subscription-service-limits.md#resource-group-limits). 


