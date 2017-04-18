<properties
   pageTitle="O gateway de dados no local | Microsoft Azure"
   description="Um gateway no local é necessário caso o seu servidor do Analysis Services no Azure irá ligar a origens de dados no local."
   services="analysis-services"
   documentationCenter=""
   authors="minewiskan"
   manager="erikre"
   editor=""
   tags=""/>
<tags
   ms.service="analysis-services"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="na"
   ms.date="10/24/2016"
   ms.author="owend"/>

# <a name="on-premises-data-gateway"></a>Gateway de dados no local

O gateway de dados no local age como um bridge, fornecer dados seguro transferência entre as origens de dados no local e o seu servidor de Azure Analysis Services na nuvem.

Um gateway está instalado no computador na sua rede. Um gateway tem de estar instalado para cada server Azure Analysis Services que tem na sua subscrição do Azure. Por exemplo, se tiver dois servidores na sua subscrição do Azure ligar a origens de dados no local, um gateway tem de estar instalado em dois computadores em separado na sua rede.

## <a name="requirements"></a>Requisitos de

**Requisitos mínimos:**

- 4,5 do .NET framework
- versão de 64 bits do Windows 7 / Windows Server 2008 R2 (ou posterior)

**Recomendada:**

- 8 core CPU
- 8 GB de memória
- versão de 64 bits do Windows 2012 R2 (ou posterior)

**Considerações importantes:**

- O gateway não pode ser instalado num controlador de domínio.

- Apenas um gateway pode ser instalado num único computador.

- Instale o gateway no computador que permanece no e não aceda a suspensão. Se o computador não estiver no, o seu servidor Azure Analysis Services não consegue ligar a sua origens de dados no local para atualizar os dados.

- Não instale o gateway num computador sem fios ligado à rede. Desempenho pode ser diminuído.

- Para alterar o nome do servidor de um gateway que já foi configurado, é necessário reinstalar e configurar um novo gateway.

- Em alguns casos, modelos em tabela ligar a origens de dados com fornecedores nativos como SQL Server Native Client (SQLNCLI11) podem devolver um erro. Para saber mais, consulte [as ligações de origem de dados](analysis-services-datasource.md).

## <a name="supported-on-premises-data-sources"></a>Origens de dados suportadas no local
Pré-visualização, do gateway suporta ligações entre o seu servidor Azure Analysis Services e seguintes origens de dados no local:

- Do SQL Server
- Armazém de dados SQL
- APPS
- Oracle
- Teradata


## <a name="download"></a>Transferir
 [Transferir o gateway](https://aka.ms/azureasgateway)


## <a name="install-and-configure"></a>Instalar e configurar

1. Execute a configuração.

2. Escolher uma localização de instalação e aceite os termos de licenciamento.

3. Inicie sessão no Azure.

4. Especifique o nome do servidor de análise do Azure. Só pode especificar um servidor por gateway. Clique em **Configurar** e estiver pronto.

    ![Inicie sessão no azure](./media\analysis-services-gateway\aas-gateway-configure-server.png)


## <a name="how-it-works"></a>Como funciona
O gateway é executado como um serviço do Windows, **gateway de dados no local**, num computador na rede da sua organização. O gateway que instalar para utilização com o Azure Analysis Services baseia-se no mesmo gateway utilizado para outros serviços como o Power BI, mas com algumas diferenças na forma como está configurado.

![Como funciona](./media/analysis-services-gateway/aas-gateway-how-it-works.png)

Trabalho fluxo consultas e dados da seguinte forma:

1.  Uma consulta é criada pelo serviço de nuvem com as credenciais encriptadas para a origem de dados no local. Em seguida, ter sido enviada para uma fila para o gateway processar.

2.  O serviço de nuvem gateway analisa a consulta e envia o pedido para o [Azure Service Bus](https://azure.microsoft.com/documentation/services/service-bus/).

3.  O gateway de dados no local consulta o Azure Service Bus para pedidos de pendentes.

4.  O gateway recebe a consulta, desencripta as credenciais e liga-se às origens de dados com essas credenciais.

5.  O gateway envia a consulta a origem de dados de execução.

6.  Os resultados são enviados a partir da origem de dados, novamente para o gateway e, em seguida, para o serviço de nuvem.

## <a name="windows-service-account"></a>Conta de serviço do Windows

O gateway de dados no local está configurado para utilizar *NT SERVICE\PBIEgwService* para a credencial de início de sessão de serviço do Windows. Por predefinição, tem à direita do início de sessão como um serviço; no contexto do computador que estiver a instalar o gateway. Esta credencial não é a mesma conta utilizado para ligar a origens de dados no local ou a sua conta Azure.  

Se encontrar problemas com o seu servidor de proxy devido a autenticação, poderá querer alterar a conta de serviço do Windows para um utilizador do domínio ou gerido conta de serviço.

## <a name="ports"></a>Portas

O gateway cria uma ligação de saída para Azure Service Bus. Comunica portas saídas: TCP 443 (predefinição), 5671, 5672, 9350 através de 9354.  O gateway não requer portas de entrada.

É recomendado lhe lista branca os endereços IP para a sua região de dados na sua firewall. Pode transferir a [lista de IP de centro de dados do Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653). Esta lista é atualizada semanais.

> [AZURE.NOTE]  Endereços IP listados na lista de IP do Centro de dados de Azure estão notação CIDR. Por exemplo, 10.0.0.0/24 não significa 10.0.0.0 através de 10.0.0.24. Saiba mais sobre a [notação CIDR](http://whatismyipaddress.com/cidr).

Seguem-se os nomes de domínio completamente qualificado utilizados pelo gateway.

|Nomes de domínio|Portas de saída|Descrição|
|---|---|---|
|*. powerbi.com|80|HTTP utilizado para transferir o programa de instalação.|
|*. powerbi.com|443|HTTPS|
|*. analysis.windows.net|443|HTTPS|
|*. login.windows.net|443|HTTPS|
|*. servicebus.windows.net|5671 5672|Avançadas colocação de protocolo (AMQP)|
|*. servicebus.windows.net|443, 9350 9354|Escutas no serviço Bus Relay ao longo do TCP (requer 443 para aquisição token de controlo de acesso)|
|*. frontend.clouddatahub.net|443|HTTPS|
|*. core.windows.net|443|HTTPS|
|login.microsoftonline.com|443|HTTPS|
|*. msftncsi.com|443|Utilizado para testar a ligação à internet, se o gateway está inacessível pelo serviço do Power BI.|
|*.microsoftonline p.com|443|Utilizado para autenticação, dependendo da configuração.|


### <a name="forcing-https-communication-with-azure-service-bus"></a>Forçar comunicação de HTTPS com Bus de serviço do Azure

Pode forçar o gateway para comunicar com o Azure Service Bus utilizando HTTPS em vez de TCP direta; No entanto, isto pode reduzir substancialmente desempenho. Tem de modificar o ficheiro *Microsoft.PowerBI.DataMovement.Pipeline.GatewayCore.dll.config* . Altere o valor de `AutoDetect` para `Https`. Este ficheiro está localizado, por predefinição, ao *local c:\Programas\Microsoft Files\On data gateway*.

```
<setting name="ServiceBusSystemConnectivityModeString" serializeAs="String">
    <value>Https</value>
</setting>
```


## <a name="troubleshooting"></a>Resolução de problemas
Em definições avançadas, o gateway de dados no local utilizado para ligar o Azure Analysis Services a sua origens de dados no local é o mesmo gateway utilizado com o Power BI.

Se estiver a ter problemas ao instalar e configurar um gateway, certifique-se de que consulte o artigo [resolução de problemas do Gateway do Power BI](https://powerbi.microsoft.com/documentation/powerbi-gateway-onprem-tshoot/). Se achar que está a ter um problema com a firewall, consulte as secções de firewall ou proxy.

Se achar que está a encontrar problemas de proxy, com o gateway, consulte o artigo [Configurar definições de proxy para os Gateways do Power BI](https://powerbi.microsoft.com/documentation/powerbi-gateway-proxy.md).

## <a name="next-steps"></a>Próximos passos
- [Gerir do Analysis Services](analysis-services-manage.md)
- [Obter dados a partir do Azure Analysis Services](analysis-services-connect.md)
