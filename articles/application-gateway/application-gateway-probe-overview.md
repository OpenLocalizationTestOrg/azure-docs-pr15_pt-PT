

<properties
   pageTitle="Estado de funcionamento a monitorização de descrição geral de Gateway de aplicação do Azure | Microsoft Azure"
   description="Saiba mais sobre as capacidades de monitorização Gateway de aplicação do Azure"
   services="application-gateway"
   documentationCenter="na"
   authors="georgewallace"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/25/2016"
   ms.author="gwallace" />

# <a name="application-gateway-health-monitoring-overview"></a>Aplicação Gateway do Estado de funcionamento descrição geral da monitorização

Azure Gateway de aplicação por predefinição monitoriza o funcionamento de todos os recursos no seu conjunto de back-end e remove automaticamente qualquer recurso considerado danificado do conjunto. Aplicação Gateway continua monitorizar as instâncias danificadas e adiciona-os novamente para o conjunto de back-end Saudável assim que ficam disponíveis e respondem a Estado de funcionamento sondas. Gateway aplicação envia que o estado de funcionamento sondas com a mesma porta que é definida nas definições de HTTP back-end. Este procedimento garante que está a sonda de a testar a mesma porta que os clientes seriam estar a utilizar para ligar para o back-end.

![exemplo de sonda de gateway de aplicação][1]

Além de utilizar a monitorização de sonda de estado de funcionamento do predefinido, também pode personalizar a sonda de estado de funcionamento de acordo com os requisitos da sua aplicação. Neste artigo, estejam tapadas sondas de estado de funcionamento personalizados e predefinidos.

## <a name="default-health-probe"></a>Sonda de estado de funcionamento predefinido

Um gateway aplicação configura automaticamente uma sonda de estado de funcionamento predefinido quando não configurou a qualquer configuração sonda personalizado. O comportamento de monitorização funciona ao efetuar um pedido de HTTP para os endereços IP configurados para o conjunto de back-end. Por predefinição sondas se as definições de http back-end estiverem configuradas para HTTPS, a sonda irá utilizar https também para testar o estado de funcionamento da back-ends.

Por exemplo: configurar o seu gateway de aplicação para utilizar os servidores de back-end A, B e C para receber o tráfego de rede HTTP na porta 80. A monitorização de estado de funcionamento de predefinido os testes de três servidores cada 30 segundos para obter uma resposta HTTP saudável. Uma resposta HTTP Saudável tem um [código de estado](https://msdn.microsoft.com/library/aa287675.aspx) entre 200 e 399.

Se a verificação de sonda predefinido falhar para que o servidor, o gateway aplicação remove-a partir do seu conjunto de back-end e deixa de tráfego de rede a fluir para este servidor. Ainda continua a sonda de predefinido para verificar a existência servidor um cada 30 segundos. Quando o servidor A com êxito responde a um pedido a partir de uma sonda de estado de funcionamento do predefinido, é adicionado novamente como Saudável para o conjunto de dados back-end e tráfego começa a fluir novamente para o servidor.

### <a name="default-health-probe-settings"></a>Predefinições de sonda de estado de funcionamento

|Propriedade sonda | Valor | Descrição|
|---|---|---|
| Sonda de URL| http://127.0.0.1:\<porta\>/ | Caminho de URL |
| Intervalo | 30 | Sonda intervalo em segundos |
| Limite de tempo  | 30 | Tempo limite de sonda em segundos |
| Limiar danificado | 3 | Sonda Repetir contagem. O servidor de back-end está marcado para baixo após a contagem de falhas consecutivas sonda atinge o limiar de danificado. |

> [AZURE.NOTE] A porta será sempre a mesma porta como as definições de HTTP back-end.

A sonda de predefinido analisa apenas http://127.0.0.1:\<porta\> para determinar o estado de funcionamento. Se precisar de configurar a sonda de estado de funcionamento para ir para um URL personalizado ou modificar quaisquer outras definições, tem de utilizar sondas personalizadas, tal como descrito nos passos seguintes.

## <a name="custom-health-probe"></a>Sonda de estado de funcionamento personalizado

Sondas personalizadas permitem-lhe tenham um controlo mais granular sobre o estado de funcionamento de monitorização. Ao utilizar sondas personalizadas, pode configurar o intervalo de sonda, o URL e o caminho para testar e quantas respostas falhadas para aceitar antes de marcar a instância do conjunto de dados back-end como danificado.

### <a name="custom-health-probe-settings"></a>Definições de sonda de estado de funcionamento personalizado

A tabela seguinte fornece definições para as propriedades de uma sonda de estado de funcionamento personalizado.

|Propriedade sonda| Descrição|
|---|---|
| Nome | Nome da sonda. Este nome é utilizado para fazer referência a sonda nas definições de HTTP back-end. |
| Protocolo | Protocolo utilizado para enviar a sonda. A sonda irá utilizar o protocolo definido nas definições de HTTP back-end |
| Anfitrião |  Para enviar a sonda de nome do anfitrião. Aplicável apenas quando o site com várias está configurado no Gateway de aplicação, caso contrário, utiliza '127.0.0.1'. Este é diferente do nome do anfitrião VM. |
| Caminho | Caminho relativo da sonda. O caminho válido começa a partir de '/'. |
| Intervalo | Sonda intervalo em segundos. Este é o intervalo de tempo entre duas sondas consecutivas.|
| Limite de tempo | Sonda de limite de tempo em segundos. A sonda está marcada como falhou se não for recebida uma resposta válida durante este período de tempo limite. |
| Limiar danificado | Sonda Repetir contagem. O servidor de back-end está marcado para baixo após a contagem de falhas consecutivas sonda atinge o limiar de danificado. |

> [AZURE.IMPORTANT] Se estiver configurado Gateway de aplicação para um único local, por predefinição anfitrião do nome deverá ser especificado como '127.0.0.1', a menos que caso contrário, é configurado no sonda personalizada.
Para referência uma sonda personalizada é enviada para \<protocolo\>://\<anfitrião\>:\<porta\>\<caminho\>. A porta utilizada será a mesma porta conforme definido nas definições de HTTP de back-end.

## <a name="next-steps"></a>Próximos passos

Depois de formação sobre como monitorizar o estado de funcionamento Gateway de aplicação, pode configurar uma [sonda de estado de funcionamento personalizado](application-gateway-create-probe-portal.md) no portal do Azure ou uma [sonda de estado de funcionamento personalizado](application-gateway-create-probe-ps.md) utilizando o PowerShell e o modelo de implementação do Azure o Gestor de recursos.

[1]: ./media/application-gateway-probe-overview/appgatewayprobe.png