<properties
   pageTitle="Aplicações de lógica instalar o gateway de dados no local | Microsoft Azure"
   description="Informações sobre como instalar o gateway de dados no local para utilização numa aplicação lógica."
   services="logic-apps"
   documentationCenter=".net,nodejs,java"
   authors="jeffhollan"
   manager="erikre"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="07/05/2016"
   ms.author="jehollan"/>

# <a name="install-the-on-premises-data-gateway-for-logic-apps"></a>Instalar o gateway de dados no local para as aplicações de lógica

## <a name="installation-and-configuration"></a>Instalação e configuração

### <a name="prerequisites"></a>Pré-requisitos

Mínimo:

* 4,5 do .NET framework
* versão de 64 bits do Windows 7 ou Windows Server 2008 R2 (ou posterior)

Recomendada:

* 8 core CPU
* 8 GB de memória
* versão de 64 bits do Windows 2012 R2 (ou posterior)

Considerações relacionadas:

* Não é possível instalar um gateway num controlador de domínio.
* Não devem instalar um gateway num computador, como um computador portátil, que pode ser desativado, asleep, ou não ligado à Internet, uma vez que o gateway não é possível executar em qualquer uma dessas circunstâncias. Além disso, poderá sofrem desempenho do gateway através de uma rede sem fios.

### <a name="install-a-gateway"></a>Instalar um gateway

Pode obter o [instalador para o gateway de dados no local](http://go.microsoft.com/fwlink/?LinkID=820931&clcid=0x409).

Especificar **gateway de dados no local** , como o modo, inicie sessão com o seu trabalho ou escola conta e, em seguida, quer configurar um novo gateway ou migrar, restaurar ou assumir o controlo de um gateway existente.

* Para configurar um gateway, escreva um **nome** e uma **chave de recuperação**e, em seguida, clique ou toque em **Configurar**.

    Especifique uma chave de recuperação que contém pelo menos de oito carateres e mantê-la num local seguro. Irá precisar desta chave se pretender migrar, restaurar ou assumir o controlo do seu gateway.

* Para migrar, restaurar ou assumir o controlo de um gateway existente, forneça a chave de recuperação que foi especificada quando o gateway foi criado.

### <a name="restart-the-gateway"></a>Reinicie o gateway

O gateway funciona como um serviço do Windows e, tal como acontece com qualquer outro serviço do Windows, pode iniciar e pará-lo de várias formas. Por exemplo, pode abrir uma linha de comandos com permissões elevadas no computador onde o gateway está em execução e, em seguida, execute um dos seguintes comandos:

* Para parar o serviço, execute este comando:

    `net stop PBIEgwService`

* Para iniciar o serviço, execute este comando:

    `net start PBIEgwService`

### <a name="configure-a-firewall-or-proxy"></a>Configurar uma firewall ou proxy

Para obter informações sobre como fornecem informações de proxy do seu gateway, consulte o artigo [Configurar definições de proxy](https://powerbi.microsoft.com/en-us/documentation/powerbi-gateway-proxy/).

Pode verificar se a firewall ou proxy, poderá estar a bloquear ligações executando o seguinte comando a partir de uma linha de comandos do PowerShell. Isto irá testar a conectividade ao Bus de serviço do Azure. Neste apenas os testes de conectividade de rede e não tem a ver com o serviço de nuvem do servidor ou o gateway. É útil para determinar se o seu computador realmente possam entrar à internet.

`Test-NetConnection -ComputerName watchdog.servicebus.windows.net -Port 9350`

Os resultados deverão ter um aspeto semelhantes a este exemplo. Se **TcpTestSucceeded** não for cumprida, pode estar bloqueado por uma firewall.

```
ComputerName           : watchdog.servicebus.windows.net
RemoteAddress          : 70.37.104.240
RemotePort             : 5672
InterfaceAlias         : vEthernet (Broadcom NetXtreme Gigabit Ethernet - Virtual Switch)
SourceAddress          : 10.120.60.105
PingSucceeded          : False
PingReplyDetails (RTT) : 0 ms
TcpTestSucceeded       : True
```

Se quiser ser exaustiva, substitua os valores do **nome do computador** e **uma porta** com os listados em [Configurar portas](#configure-ports) mais adiante.

A firewall também pode bloquear as ligações que o Bus de serviço do Azure torna-se aos centros de dados Azure. Se for esse o caso, irá querer à lista branca (desbloquear) todos os endereços IP para a sua região para esses centros de dados. Pode obter uma lista de [endereços de Azure IP aqui](https://www.microsoft.com/download/details.aspx?id=41653).

### <a name="configure-ports"></a>Configurar as portas

O gateway cria uma ligação de saída para Azure Service Bus. Comunica portas saídas: TCP 443 (predefinição), 5671, 5672, 9350 guiada 9354. O gateway não necessita de portas de entrada.

Saiba mais sobre [soluções híbrido](../service-bus-messaging/service-bus-fundamentals-hybrid-solutions.md).

| NOMES DE DOMÍNIO | PORTAS DE SAÍDA | DESCRIÇÃO |
| ----- | ------ | ------ |
| *. analysis.windows.net | 443 | HTTPS |
| *. login.windows.net | 443 | HTTPS |
| *. servicebus.windows.net |5671 5672 | Avançadas colocação de protocolo (AMQP) |
| *. servicebus.windows.net | 443, 9350 9354 | Escutas no serviço Bus Relay ao longo do TCP (requer 443 para aquisição token de controlo de acesso) |
| *. frontend.clouddatahub.net | 443 | HTTPS |
| *. core.windows.net | 443 | HTTPS |
| login.microsoftonline.com | 443 | HTTPS |
| *. msftncsi.com | 443 | Utilizado para testar a ligação à internet, se o gateway está inacessível pelo serviço do Power BI. |

Se precisar de branco lista endereços IP em alternativa aos domínios, pode transferir e utilizar a [lista de intervalos IP de centro de dados do Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653). Em alguns casos, as ligações de Azure Service Bus serão efectuadas com o endereço IP em vez dos nomes de domínio completamente qualificado.

### <a name="sign-in-account"></a>Conta de início de sessão

Os utilizadores irão inicie sessão com um uma trabalho conta ou profissional. Esta é a sua conta de organização. Se inscreveu numa oferta do Office 365 e não fornecer o seu e-mail do trabalho real, pode ser semelhante jeff@contoso.onmicrosoft.com. Sua conta, dentro de um serviço na nuvem, é armazenada dentro de um inquilino no Azure Active Directory (AAD). Na maioria dos casos, a UPN sua conta AAD devolvida correspondem o endereço de e-mail.

### <a name="windows-service-account"></a>Conta de serviço do Windows

O gateway de dados no local está configurado para utilizar NT SERVICE\PBIEgwService para a credencial de início de sessão de serviço do Windows. Por predefinição, tem à direita do registo como um serviço. Este é no contexto do computador no qual está a instalar o gateway.

Esta não é a conta utilizada para ligar a origens de dados no local ou com a conta escolar ou profissional com as quais ao iniciar sessão no serviços em nuvem.

##<a name="frequently-asked-questions"></a>Perguntas mais frequentes

### <a name="general"></a>Geral

**Pergunta**: origens de dados que o gateway suporta?<br/>
**Resposta**: partir ao escrever, SQL Server.

**Pergunta**: é necessário um gateway para origens de dados na nuvem, como SQL Azure? <br/>
**Resposta**: não. Um gateway se liga ao apenas as origens de dados no local.

**Pergunta**: o que é o serviço Windows real designado por?<br/>
**Resposta**: serviços, o gateway chama-se o serviço de Gateway empresarial do Power BI.

**Pergunta**: existem quaisquer ligações de entrada para o gateway a partir da nuvem? <br/>
**Resposta**: não. O gateway utiliza ligações de saída para Azure Service Bus.

**Pergunta**: o que acontece se para bloquear pessoas que as ligações de saída? O que preciso abrir? <br/>
**Resposta**: Consulte portas e anfitriões que utiliza o gateway.


**Pergunta**: O gateway tem de ser instalada no mesmo computador como origem de dados? <br/>
**Resposta**: não. O gateway irá ligar à origem de dados utilizando as informações de ligação que lhe foram fornecidas. Pense do gateway como uma aplicação de cliente neste sentido. Apenas será necessário poderá ligar-se para o nome do servidor que foi fornecido.


**Pergunta**: o que é a latência para executar consultas para uma origem de dados a partir do gateway? O que é a melhor arquitetura? <br/>
**Resposta**: para reduzir a latência da rede, instale o gateway perto da origem de dados possível. Se, pode instalar o gateway na origem de dados reais, irá minimizar a latência introduzida. Considere centros de dados. Por exemplo, se o serviço está a utilizar o Centro de dados dos e.u.a. Ocidental e tiver o SQL Server alojado numa VM Azure, deverá ter também a VM Azure nos EUA Ocidental. Isto irá minimizar latência e evitar taxas de saída na VM Azure.


**Pergunta**: existem quaisquer requisitos de largura de banda de rede? <br/>
**Resposta**: é recomendável que tenham bom débito para a sua ligação de rede. Cada ambiente é diferente e a quantidade de dados a ser enviadas irá afetar os resultados. Utilizar ExpressRoute pode ajudar a garantir um nível de débito entre no local e os centros de dados Azure.

Pode utilizar a aplicação de teste de velocidade Azure da ferramenta de terceiros para ajudar a avaliar o que é o seu débito.


**Pergunta**: pode executar ao serviço Windows do gateway com uma conta do Azure Active Directory? <br/>
**Resposta**: não. O serviço Windows tem de ter uma conta Windows válida. Por predefinição, é executada com o serviço SID, NT SERVICE\PBIEgwService.


**Pergunta**: como são resultados enviados novamente para a nuvem? <br/>
**Resposta**: Isto é feito no Bus de serviço do Azure. Para mais informações, consulte o artigo como funciona.


**Pergunta**: onde estão as minhas credenciais armazenadas? <br/>
**Resposta**: as credenciais que introduzir uma origem de dados são armazenadas encriptados no serviço de nuvem de gateway. As credenciais são desencriptar no gateway no local.

### <a name="high-availabilitydisaster-recovery"></a>Recuperação de alta/falhas de disponibilidade

**Pergunta**: existem qualquer planos para ativar a cenários de elevada disponibilidade com o gateway? <br/>
**Resposta**: Esta é o guia, mas ainda não tem uma linha cronológica.


**Pergunta**: que opções tem disponíveis para recuperação de falhas? <br/>
**Resposta**: pode utilizar a chave de recuperação para restaurar ou mover um gateway. Quando instala o gateway, especifique a chave de recuperação.


**Pergunta**: o que é o benefício a chave de recuperação? <br/>
**Resposta**: fornece uma maneira para migrar ou recuperar as definições de gateway após uma falhas.

### <a name="troubleshooting"></a>Resolução de problemas

**Pergunta**: onde estão os registos de gateway? <br/>
**Resposta**: Consulte ferramentas posteriormente neste tópico.


**Pergunta**: como posso ver quais os consultas que estão a ser enviado para a origem de dados no local? <br/>
**Resposta**: pode ativar rastreio de consulta, que irá incluir as consultas a ser enviadas. Lembre-se para alterá-la novamente para o valor original quando concluído a resolução de problemas. Sair do consulta rastreio activado fazer com que os registos ser maior.

Também pode consultar ferramentas que tem a origem de dados para as consultas de rastreio. Por exemplo, pode utilizar eventos expandido ou gerador de perfis de SQL para SQL Server e Analysis Services.

## <a name="how-the-gateway-works"></a>Como funciona o gateway

Quando um utilizador interage com um elemento que está ligado a uma origem de dados no local:

1. O serviço de nuvem cria uma consulta, juntamente com as credenciais da origem de dados, encriptadas e envia a consulta para a fila para o gateway processar.
1. O serviço analisa a consulta e envia o pedido ao Bus de serviço do Azure.
1. O gateway de dados no local consulta o Azure Service Bus para pedidos de pendentes.
1. O gateway recebe a consulta, desencripta as credenciais e liga-se para as origens de dados com essas credenciais.
1. O gateway envia a consulta a origem de dados de execução.
1. Os resultados são enviados a partir da origem de dados novamente para o gateway e, em seguida, para o serviço de nuvem. O serviço utiliza, em seguida, os resultados.

## <a name="troubleshooting"></a>Resolução de problemas

### <a name="update-to-the-latest-version"></a>Atualize para a versão mais recente

Podem revelar muitas problemas quando a versão do gateway está desatualizada.  É aconselhável geral para se certificar de que tem a versão mais recente.  Se ainda não o tiver atualizado o gateway para um mês ou aumentar, poderá querer para pondere instalar a versão mais recente do gateway e ver se consegue reproduzir o problema.

### <a name="error-failed-to-add-user-to-group--2147463168-pbiegwservice-performance-log-users-"></a>Erro: Falha ao adicionar utilizador ao grupo. (Utilizadores do registo de desempenho de PBIEgwService-2147463168)

Poderá receber este erro se está a tentar instalar o gateway no controlador de domínio, que não é suportado. Terá de implementar o gateway num computador que não seja um controlador de domínio.

## <a name="tools"></a>Ferramentas

### <a name="collecting-logs-from-the-gateway-configurator"></a>Recolher registos a partir do configurador do gateway

É possível recolher vários registos para o gateway. Começam sempre com os registos!

#### <a name="installer-logs"></a>Registos de Installer

`%localappdata%\Temp\Power_BI_Gateway_–Enterprise.log`

#### <a name="configuration-logs"></a>Registos de configuração

`%localappdata%\Microsoft\Power BI Enterprise Gateway\GatewayConfigurator.log`

#### <a name="enterprise-gateway-service-logs"></a>Registos do serviço de gateway Enterprise

`C:\Users\PBIEgwService\AppData\Local\Microsoft\Power BI Enterprise Gateway\EnterpriseGateway.log`

#### <a name="event-logs"></a>Registos de eventos

Os registos do Data Management Gateway e PowerBIGateway estão presentes em **registos de serviços e de aplicação**.

### <a name="fiddler-trace"></a>Rastreio fiddler

[Fiddler](http://www.telerik.com/fiddler) é uma ferramenta gratuita da Telerik monitoriza o tráfego HTTP.  Pode ver o anterior e quarta com o Power BI do serviço a partir do computador do cliente. Isto pode mostrar erros e outras informações relacionadas.

## <a name="next-steps"></a>Próximos passos
- [Criar uma ligação no local às aplicações de lógica](app-service-logic-gateway-connection.md)
- [Funcionalidades de integração de empresa](app-service-logic-enterprise-integration-overview.md)
- [Conectores de aplicações de lógica](../connectors/apis-list.md)