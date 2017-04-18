<properties
    pageTitle="Saiba mais sobre funcionalidades nas edições de serviços de BizTalk | Microsoft Azure"
    description="Comparar as funcionalidades das edições de serviços de BizTalk: livre, programador, Basic, padrão e Premium. MAK, MAB, WABS."
    services="biztalk-services"
    documentationCenter=""
    authors="MandiOhlinger"
    manager="erikre"
    editor=""/>

<tags
    ms.service="biztalk-services"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/15/2016"
    ms.author="mandia"/>


# <a name="biztalk-services-editions-chart"></a>BizTalk serviços: Gráfico de edições

Serviços de BizTalk Azure oferece várias edições. Utilize este artigo para determinar qual a edição se adequa às suas necessidades cenário e empresas.


## <a name="compare-the-editions"></a>Comparar as edições

**Libertar (pré-visualização)**

Pode criar e gerir ligações de híbrido. Uma ligação de híbrido é uma forma fácil de ligar um Web site Azure a um sistema no local, como o SQL Server.

**Para programadores**

Inclui ligações híbrido, endereços EAI e editar processamento de mensagens com um portal de gestão de parceiro negociação fáceis de utilizar e suporte para esquemas de editar comuns e processamento de editar rich sobre X12 e AS2. Pode criar cenários comuns de endereços EAI ligar serviços na nuvem com quaisquer protocolos HTTP/S, resto, FTP, WCF e SFTP para ler e escrever mensagens.  Utilizam conectividade para sistemas LOB no local com adaptadores SAP, Oracle eComércio, Oracle DB, Siebel e o SQL Server prontos a utilizar. Utilize um ambiente do programador centrados em com ferramentas do Visual Studio para o desenvolvimento fácil e implementação. Limitado para fins de desenvolvimento e teste apenas com sem nível contrato serviço (SLA).

**Básicas**

Inclui a maior parte das capacidades de programador com aumentos nas ligações híbrido ligações, endereços EAI pontes, editar acordos e BizTalk adaptador Pack. Também oferece elevada disponibilidade e a opção para dimensionar com um contrato de nível de serviço (SLA).

**Padrão**

Inclui todas as capacidades básicas com aumentos nas ligações híbrido ligações, endereços EAI pontes, editar acordos e BizTalk adaptador Pack. Também oferece elevada disponibilidade e a opção para dimensionar com um contrato de nível de serviço (SLA).

**Premium**

Inclui todas as funcionalidades de padrão com aumentos nas ligações híbrido ligações, endereços EAI pontes, editar acordos e BizTalk placa Pack. Também inclui arquivo, elevada disponibilidade e a opção para dimensionar com um contrato de nível de serviço (SLA).


## <a name="editions-chart"></a>Gráfico de edições
A tabela seguinte apresenta as diferenças.

<table border="1">
<tr bgcolor="FAF9F9">
        <th></th>
        <th>Libertar (pré-visualização)</th>
        <th>Para programadores</th>
        <th>Básicas</th>
        <th>Padrão</th>
        <th>Premium</th>
</tr>

<tr>
<td><strong>Preço de partida</strong></td>
<td colspan="5"><a HREF="http://go.microsoft.com/fwlink/p/?LinkID=304011"> Serviços de Azure BizTalk preços</a> <br/><br/> <a HREF="http://azure.microsoft.com/pricing/calculator/?scenario=full">Calculadora de preços do Azure</a></td>
</tr>
<tr>
<td><strong>Configuração mínima predefinido</strong></td>
<td>1 unidade gratuita</td>
<td>Unidade 1 para programadores</td>
<td>1 unidade básica</td>
<td>1 unidade padrão</td>
<td>Unidade de 1 prémio</td>
</tr>
<tr>
<td><strong>Escala</strong></td>
<td>Sem escala</td>
<td>Sem escala</td>
<td>Sim, em incrementos de 1 unidade básica</td>
<td>Sim, em incrementos de 1 unidade padrão</td>
<td>Sim, em incrementos de 1 unidade Premium</td>
</tr>
<tr>
<td><strong>Escala saída máxima permitida</strong></td>
<td>Sem escala</td>
<td>Sem escala</td>
<td>Até 8 unidades</td>
<td>Até 8 unidades</td>
<td>Até 8 unidades</td>
</tr>
<tr>
<td><strong>Endereços EAI pontes por unidade</strong></td>
<td>Não incluído</td>
<td>25</td>
<td>25</td>
<td>125</td>
<td>500</td>
</tr>
<tr>
<td><strong>EDITAR, AS2</strong>
<br/><br/>
Inclui TPM acordos</td>
<td>Não incluído</td>
<td>Incluído. 10 acordos por unidade.</td>
<td>Incluído. 50 acordos por unidade.</td>
<td>Incluído. 250 acordos por unidade.</td>
<td>Incluído. acordos de 1000 por unidade.</td>
</tr>
<tr>
<td><strong>Ligações de híbrido por unidade</strong></td>
<td>5</td>
<td>5</td>
<td>10</td>
<td>50</td>
<td>100</td>
</tr>
<tr>
<td><strong>Híbrido ligação de transferência de dados (GB) por unidade</strong></td>
<td>5</td>
<td>5</td>
<td>50</td>
<td>250</td>
<td>500</td>
</tr>
<tr>
<td><strong>Ligações de serviço de placa BizTalk para sistemas LOB no local</strong></td>
<td>Não incluído</td>
<td>1 ligação</td>
<td>2 ligações</td>
<td>5 ligações</td>
<td>25 ligações</td>
</tr>
<tr>
<td align="left"><strong>Sistemas/protocolos suportados:</strong>
<ul>
<li>HTTP</li>
<li>HTTPS</li>
<li>FTP</li>
<li>SFTP</li>
<li>WCF</li>
<li>Serviço Bus (SB)</li>
<li>BLOBs do Azure</li>
<li>REST APIs</li>
</ul>
</td>
<td>Não incluído</td>
<td>Incluído</td>
<td>Incluído</td>
<td>Incluído</td>
<td>Incluído</td>
</tr>
<tr>
<td><strong>Disponibilidade de alta</strong>
<br/><br/>
Para o contrato de nível de serviço (SLA), consulte o artigo <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=304011">Preços do Azure BizTalk serviços</a>.
</td>
<td>Não incluído</td>
<td>Não incluído</td>
<td>Incluído</td>
<td>Incluído</td>
<td>Incluído</td>
</tr>
<tr>
<td><strong>Cópia de segurança e restauro</strong></td>
<td>Não incluído</td>
<td>Incluído</td>
<td>Incluído</td>
<td>Incluído</td>
<td>Incluído</td>
</tr>
<tr>
<td><strong>Controlo</strong></td>
<td>Não incluído</td>
<td>Incluído</td>
<td>Incluído</td>
<td>Incluído</td>
<td>Incluído</td>
</tr>
<tr>
<td><strong>Arquivo</strong><br/><br/>
Inclui não repudiação de recibo (NRR) e transferir as mensagens registadas</td>
<td>Não incluído</td>
<td>Incluído</td>
<td>Não incluído</td>
<td>Não incluído</td>
<td>Incluído</td>
</tr>
<tr>
<td><strong>Utilização de código personalizado</strong></td>
<td>Não incluído</td>
<td>Incluído</td>
<td>Incluído</td>
<td>Incluído</td>
<td>Incluído</td>
</tr>
<tr>
<td><strong>Utilização das transformações, incluindo XSLT personalizado</strong></td>
<td>Não incluído</td>
<td>Incluído</td>
<td>Incluído</td>
<td>Incluído</td>
<td>Incluído</td>
</tr>
</table>

> [AZURE.NOTE] Para RDP contra falhas de hardware, elevada disponibilidade implica ter várias VMs dentro de uma única unidade BizTalk.


## <a name="faqs"></a>Perguntas mais frequentes

#### <a name="what-is-a-biztalk-unit"></a>O que é uma unidade de BizTalk?
"Unidade" é o nível atómico de uma implementação do Azure BizTalk serviços. Cada edição vem com uma unidade que tenha de memória e capacidade de cluster diferente. Por exemplo, uma unidade básica tem mais cluster de programador, padrão tem mais cluster que básicas etc. Quando dimensionar um serviço de BizTalk, dimensionar em termos de unidades.

#### <a name="what-is-the-difference-between-biztalk-services-and-azure-biztalk-vm"></a>O que é a diferença entre os serviços de BizTalk e Azure BizTalk VM?
Serviços de BizTalk fornece uma arquitetura de plataforma-como-a-Service (PaaS) verdadeira para construção de soluções de integração na nuvem. Com o modelo de PaaS, focar-se completamente em lógica da aplicação e deixar todos a gestão de infraestrutura para a Microsoft, incluindo:

- Não é necessário para gerir ou correcção máquinas virtuais.
- Microsoft assegura disponibilidade.
- Controlar escala a pedido solicitando simplesmente mais ou menos capacidade através do portal do Azure.

BizTalk Server em máquinas virtuais do Azure fornece uma arquitetura de infraestrutura-como-a-Service (IaaS). Criar máquinas virtuais e configurá-los exatamente como o seu ambiente no local, tornando mais fácil executar as aplicações existentes na nuvem com sem alterações de código. Com IaaS, é responsável ainda para configurar as máquinas virtuais, gerir as máquinas virtuais (por exemplo, instalar software e sistema operativo patches) e criação de aplicação para elevada disponibilidade.

Se está a observar construção de soluções de integração com o novo que minimizar o esforço de gestão de infraestrutura, utilize os serviços de BizTalk. Se estiver à procura de migrar rapidamente as soluções BizTalk existentes ou está à procura de um ambiente a pedido desenvolver e testar aplicações BizTalk Server, utilize uma Máquina Virtual no Azure BizTalk Server.

#### <a name="what-is-the-difference-between-biztalk-adapter-service-and-hybrid-connections"></a>O que é a diferença entre o serviço de placa de BizTalk e as ligações de híbrido?
O serviço de placa BizTalk é utilizado por um serviço de BizTalk Azure. O serviço de placa de BizTalk utiliza o pacote de placa BizTalk para ligar a um sistema de linha de negócio (LOB) no local. Uma ligação de híbrido fornecem uma forma fácil e conveniente para ligar a aplicações do Azure, como a funcionalidade de Web Apps no serviço de aplicação do Azure e Azure Mobile serviços, a um recurso no local.

#### <a name="what-does-hybrid-connection-data-transfer-gb-per-unit-mean-is-this-per-minutehourdayweekmonth-what-happens-when-the-limit-is-reached"></a>O que significa "Híbrido ligação transferência de dados (GB) por unidade"? Este é por minuto/hora/dia/semana/mês? O que acontece quando o limite é atingido?

O custo da ligação de híbrido por unidade depende da edição de serviços de BizTalk. Resumindo, dependem de custos quantidade de dados transferir. Por exemplo, transferir dados de 10 GB diariamente custos de menor do que a transferência de 100 GB diariamente. Utilize a [Preços Calculadora](https://azure.microsoft.com/pricing/calculator/?scenario=full) para serviços de BizTalk para determinar os custos específicos. Normalmente, os limites são impostos diariamente. Se exceder o limite, qualquer eventual hiperdosagem é cobrada à taxa de $1 por GB.

#### <a name="when-i-create-an-agreement-in-biztalk-services-why-does-the-number-of-bridges-go-up-by-two-instead-of-just-one"></a>Quando posso criar um contrato de serviços de BizTalk, por que motivo é que o número de pontes subir por dois em vez de apenas um?

Cada contrato compreende de duas pontes diferentes: uma pontes de comunicação de lado de envio e uma bridge de comunicação de lado receção.

####  <a name="what-happens-when-i-hit-the-quota-limit-on-the-number-of-bridges-or-agreements"></a>O que acontece quando posso visitas o limite de quota no número de pontes ou acordos?

Não é possível implementar qualquer pontes novos ou criar novos qualquer acordos. Para implementar mais, tem de dimensionado de forma a mais unidades do serviço BizTalk ou atualizar para uma edição superior.

#### <a name="how-do-i-migrate-from-one-tier-of-biztalk-services-to-another"></a>Como posso migrar a partir de uma camada dos serviços de BizTalk para outro?

A edição gratuita não pode ser migrada ou 'aumentada' para outra camada e não pode ser cópia de segurança e restaurados para outra camada. Se precisar de outra camada, crie um novo serviço BizTalk utilizando a nova camada. Qualquer artefactos criados com a edição livre, incluindo ligações híbrido, tem de ser recriadas no novo serviço BizTalk. 

Para as edições restantes, utilize a cópia de segurança e restaurar para migrar o seu artefactos de uma camada para outro. Por exemplo, os artefactos na camada padrão de cópia de segurança e, em seguida, restaurá-las na camada Premium. [BizTalk serviços: cópia de segurança e restaurar](biztalk-backup-restore.md) descreve os caminhos de migração suportados e listas que artefactos de cópia de segurança abrange. Tenha em atenção que híbrido ligações não cópia de segurança abrange. Depois de cópias de segurança e restaurar para uma nova camada, em seguida, recriar as ligações de híbrido.  


#### <a name="is-the-biztalk-adapter-service-included-in-the-service-how-do-i-receive-the-software"></a>É o serviço de placa BizTalk incluído no serviço? Como posso receber o software?

Sim, o serviço de placa BizTalk com o pacote de placa BizTalk incluídos o Azure BizTalk Services SDK [Transferir](http://www.microsoft.com/download/details.aspx?id=39087).

## <a name="next-steps"></a>Próximos passos

Para criar Azure BizTalk serviços no portal do Azure, aceda a [BizTalk serviços: aprovisionamento através do portal Azure](biztalk-provision-services.md). Para começar a criação de aplicações, aceda a [Azure BizTalk serviços](http://go.microsoft.com/fwlink/p/?LinkID=235197).

## <a name="additional-resources"></a>Recursos adicionais
- [Serviços de BizTalk: Aprovisionamento através do portal Azure](biztalk-provision-services.md)<br/>
- [Serviços de BizTalk: Gráfico de estado de aprovisionamento](biztalk-service-state-chart.md)<br/>
- [BizTalk serviços: Separadores de Dashboard, Monitor e escala](biztalk-dashboard-monitor-scale-tabs.md)<br/>
- [BizTalk serviços: Cópia de segurança e restauro](biztalk-backup-restore.md)<br/>
- [Serviços de BizTalk: limitação](biztalk-throttling-thresholds.md)<br/>
- [BizTalk serviços: Nome do emissor e chave emissor](biztalk-issuer-name-issuer-key.md)<br/>
- [Como é que começar a utilizar o SDK do Azure BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=302335)<br/>
