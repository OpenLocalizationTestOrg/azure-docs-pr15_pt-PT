A tabela seguinte descreve cada um dos principais quotas, limites, predefinições e limitações no Azure Programador de tarefas.

|Recurso|Descrição do limite|
|---|---|
|**Tamanho da tarefa**|O tamanho máximo do trabalho é 16K. Se um local ou um PATCHES resultaram numa tarefa maior que estes limites, é devolvido um código de estado pedido incorrecto 400.|
|**Pedido de tamanho de URL**|Tamanho máximo do URL pedido é 2048 caracteres.|
|**Tamanho do cabeçalho de agregação**|Tamanho máximo de cabeçalho agregação é 4096 caracteres.|
|**Contagem de cabeçalho**|Contagem de cabeçalho máximo é 50 cabeçalhos.|
|**Tamanho do corpo**|Tamanho do corpo máximo é 8192 caracteres.|
|**Intervalo de periodicidade**|Intervalo de periodicidade máximo é 18 meses.|
|**Tempo a hora de início**|Máximo "hora a hora de início" é 18 meses.|
|**Histórico da tarefa**|Corpo máximo de resposta armazenado no histórico da tarefa é 2048 bytes.|
|**Frequência**|A quota de frequência max predefinido é 1 hora numa coleção de tarefa gratuito e 1 minuto numa coleção de tarefa padrão. A frequência max é configurável numa colecção de tarefa para ser inferior ao valor máximo. Todas as tarefas na coleção de tarefa estão limitadas o valor configurado na coleção de tarefa. Se tentar criar uma tarefa com uma frequência mais alta que a frequência máxima na coleção de tarefa pedido irá falhar com um código de estado de conflito 409.|
|**Tarefas**|A quota de tarefas max predefinida é 5 tarefas numa coleção de tarefa gratuito e 50 tarefas numa coleção de tarefa padrão. O número máximo de tarefas é configurável numa coleção de tarefa. Todas as tarefas na coleção de tarefa estão limitadas o valor configurado na coleção de tarefa. Se tentar criar mais tarefas que a quota de tarefas máximo, em seguida, o pedido falhar com um código de estado de conflito 409.|
|**Retenção de histórico de tarefa**|Histórico da tarefa é retido para até 2 meses ou por excesso para as último 1000 execuções.|
|**Retenção de tarefas concluídas e com falhas**|Tarefas concluídas e com falhas são mantidas 60 dias.|
|**Tempo limite**|Existe um estático (não configurável) pedir tempo limite de 60 segundos para ações de HTTP. Para mais longa operações em execução, siga protocolos assíncronos HTTP; Por exemplo, devolver uma 202 imediatamente mas continuar a trabalhar em segundo plano.|
