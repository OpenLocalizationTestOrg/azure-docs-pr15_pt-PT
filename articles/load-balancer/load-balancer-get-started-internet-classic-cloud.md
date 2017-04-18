<properties
   pageTitle="Começar a criar uma Internet opostas Balanceador de carga na implementação clássica utilizar o modelo para serviços em nuvem | Microsoft Azure"
   description="Saiba como criar um opostas Balanceador de carga no modelo clássico de implementação para serviços em nuvem da Internet"
   services="load-balancer"
   documentationCenter="na"
   authors="sdwheeler"
   manager="carmonm"
   editor=""
   tags="azure-service-management"
/>
<tags
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/17/2016"
   ms.author="sewhee" />

# <a name="get-started-creating-an-internet-facing-load-balancer-for-cloud-services"></a>Começar a criar um opostas Balanceador de carga para serviços em nuvem da Internet

[AZURE.INCLUDE [load-balancer-get-started-internet-classic-selectors-include.md](../../includes/load-balancer-get-started-internet-classic-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Este artigo abrange o modelo de implementação clássica. Pode também [saber como criar um opostas Balanceador de carga de utilizar o Gestor de recursos do Azure da Internet](load-balancer-get-started-internet-arm-cli.md).

Serviços em nuvem são automaticamente configurados com um balanceador de carga e podem ser personalizados através do modelo de serviço.

## <a name="create-a-load-balancer-using-the-service-definition-file"></a>Criar um balanceador de carga utilizando o ficheiro de definição de serviço

Pode tirar partido o SDK do Azure para .NET 2,5 atualizar o seu serviço de nuvem. Definições de ponto final para serviços em nuvem são efetuadas no ficheiro .csdef [definição de serviço](https://msdn.microsoft.com/library/azure/gg557553.aspx).

O exemplo seguinte mostra como um ficheiro de servicedefinition.csdef para uma implementação nuvem está configurado:

Verificar snipet para o ficheiro de .csdef gerado por uma implementação na nuvem, pode ver o ponto final de externo configurou para utilizar portas HTTP na porta 10000, 10001 e 10002.


    <ServiceDefinition name=“Tenant“>
    <WorkerRole name=“FERole” vmsize=“Small“>
    <Endpoints>
        <InputEndpoint name=“FE_External_Http” protocol=“http” port=“10000“ />
        <InputEndpoint name=“FE_External_Tcp“  protocol=“tcp“  port=“10001“ />
        <InputEndpoint name=“FE_External_Udp“  protocol=“udp“  port=“10002“ />

        <InputEndpointname=“HTTP_Probe” protocol=“http” port=“80” loadBalancerProbe=“MyProbe“ />

        <InstanceInputEndpoint name=“InstanceEP” protocol=“tcp” localPort=“80“>
           <AllocatePublicPortFrom>
              <FixedPortRange min=“10110” max=“10120“  />
           </AllocatePublicPortFrom>
        </InstanceInputEndpoint>
        <InternalEndpoint name=“FE_InternalEP_Tcp” protocol=“tcp“ />
    </Endpoints>
    </WorkerRole>
    </ServiceDefinition>




## <a name="check-load-balancer-health-status-for-cloud-services"></a>Verificar o estado de funcionamento do Balanceador de carga para serviços em nuvem


Segue-se um exemplo de uma sonda de estado de funcionamento:

        <LoadBalancerProbes>
        <LoadBalancerProbe name=“MyProbe” protocol=“http” path=“Probe.aspx” intervalInSeconds=“5” timeoutInSeconds=“100“ />
        </LoadBalancerProbes>

O Balanceador de carga combina as informações do ponto final e as informações da sonda para criar um URL sob a forma de http://{DIP de VM}:80/Probe.aspx que podem ser utilizados para consultar o estado de funcionamento do serviço.

O serviço de Deteta sondas periódicas a partir do mesmo endereço IP. Este é o pedido de sonda de estado de funcionamento provenientes do anfitrião do nó onde a máquina virtual está em execução.
O serviço tem que responder com um código de estado de HTTP 200 do Balanceador de carga para partem do princípio de que o serviço está em bom estado. Qualquer outro HTTP Estado código (por exemplo 503) diretamente leva-o até a máquina virtual terminar rotação.

A definição de sonda também controla a frequência da sonda. No nosso caso acima, o Balanceador de carga é a pesquisar o ponto final de cada seg. 5. Se for recebida sem resposta positiva para 10 segundos (dois intervalos de sonda), é considerada a sonda para baixo e, a máquina virtual é disponibilizada terminar rotação. Da mesma forma, se o serviço estiver fora do rotação e é recebida uma resposta positiva, o serviço de volta ao rotação imediatamente. Se o serviço é flutuante entre saudável e danificado, pode decidir o Balanceador de carga atrasar a reintrodução do serviço de voltar a rotação de até que tenha sido saudável de um número de sondas.

Verificar o esquema de definição de serviço para a [sonda de estado de funcionamento](https://msdn.microsoft.com/library/azure/jj151530.aspx) para obter mais informações.

## <a name="next-steps"></a>Próximos passos

[Começar a configurar uma Balanceador de carga interno](load-balancer-get-started-ilb-arm-ps.md)

[Configurar um modo de distribuição do Balanceador de carga](load-balancer-distribution-mode.md)

[Configurar definições de tempo limite do TCP idle para Balanceador de carga](load-balancer-tcp-idle-timeout.md)

