A tabela seguinte indica os limites associados com as camadas de serviços diferente (S1, S2, S3, F1). Para informações sobre o custo de cada *unidade* de cada camada, consulte o artigo [IoT concentrador preços](https://azure.microsoft.com/pricing/details/iot-hub/).

| Recurso | S1 padrão | S2 padrão | S3 padrão | F1 gratuito |
| -------- | ----------- | ----------- | ----------- | ------- |
| Mensagens/dia | 400.000 | 6,000,000   | 300,000,000 | 8.000   |
| Unidades máximas | 200    | 200         | 200         | 1       |

> [AZURE.NOTE] Se antecipar a utilizar mais de 200 unidades com um concentrador de camada S1 ou S2 ou S3, contacte o suporte da Microsoft.

A tabela seguinte lista os limites que se aplicam a recursos IoT concentrador:

| Recurso | Limite |
| -------- | ----- |
| Máximo pago IoT concentradores por subscrição Azure | 10 |
| Máximos concentradores IoT gratuitos por subscrição Azure | 1 |
| Número máximo de identidades do dispositivo<br/>  devolvido numa única chamada | 1000 |
| Retenção de máxima de mensagem de IoT concentrador para mensagens de nuvem de dispositivo | 7 dias |
| Tamanho máximo da mensagem de nuvem de dispositivo | 256 KB |
| Tamanho máximo do lote nuvem de dispositivo | 256 KB |
| Mensagens máximas no lote de nuvem de dispositivo | 500 |
| Tamanho máximo da mensagem de dispositivo de nuvem | 64 KB |
| TTL máximo para mensagens de dispositivo de nuvem | 2 dias |
| Contagem de entrega máximo para o dispositivo de nuvem <br/> mensagens | 100 |
| Contagem de entrega máximo para mensagens de comentários <br/> em resposta a uma mensagem de dispositivo de nuvem | 100 |
| TTL máximo para mensagens de comentários na <br/> resposta a uma mensagem de dispositivo de nuvem | 2 dias |

> [AZURE.NOTE] Se precisar de mais de 10 concentradores IoT pagos numa subscrição do Azure, contacte o suporte da Microsoft.

O serviço de IoT concentrador acelera os pedidos de quando são excedidas as quotas seguintes:

| Optimizar | Valor por concentrador |
| -------- | ------------- |
| Operações de registo de identidade <br/> (criar, obter, lista, atualizar, eliminar), <br/> Importar/exportar individual ou em massa | mínimo/5000/unidade (para S3) <br/> 100/mínimo/unidade de (para S1 e S2). |
| Ligações de dispositivos | 6000/seg/unidade (para S3), 120/seg/unidade (para S2), sec/12/unidade (para S1). <br/>Mínimo de 100/seg. |
| Nuvem de dispositivo envia | 6000/seg/unidade (para S3), 120/seg/unidade (para S2), sec/12/unidade (para S1). <br/>Mínimo de 100/seg. |
| Nuvem-a-dispositivo envia | mínimo/5000/unidade (para S3), 100/mínimo/unidade (para S1 e S2). |
| Nuvem para dispositivo recebe | mínimo/50000/unidade (para S3), 1000/mínimo/unidade (para S1 e S2). |
| Operações de carregamento de ficheiros | ficheiro de 5000 carregar notificações/mínimo/unidade (para S3), carregamento de ficheiro 100 notificações/mínimo/unidade (para S1 e S2). <br/> 10000 SA URIs pode ser saída para uma conta de armazenamento do Windows Azure ao mesmo tempo.<br/> 10 SA URIs/dispositivo pode ser saída de uma só vez. |
