<properties 
    pageTitle="Configurar o dashboard, Monitor, escala, e ligações de híbrido nos serviços do BizTalk | Microsoft Azure" 
    description="Saiba mais sobre os controlos e monitorizar o desempenho nos separadores clássicos portais para serviços de BizTalk: Dashboard, Monitor, escala, configurar e híbrido ligações. MAK, MAB, WABS" 
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
    ms.topic="article" 
    ms.date="08/23/2016" 
    ms.author="mandia"/>




# <a name="review-the-dashboard-monitor-scale-configure-and-hybrid-connection-tabs"></a>Rever os separadores de Dashboard, Monitor, escala, configurar e ligação híbrido

Depois de criar o seu serviço de BizTalk e implementar a aplicação, pode alterar algumas das definições do serviço de BizTalk e monitorizar o desempenho da aplicação. 

Quando abre o portal clássico Azure, são colocados automaticamente no separador **Todos os itens** . Para ver o seu serviço de BizTalk, selecione o seu serviço de BizTalk no separador **Todos os itens** ou selecione o separador de **BIZTALK serviços** ; e, em seguida, selecione o seu nome de serviço de BizTalk.

É aberta numa nova janela com separadores que se seguem. Este tópico descreve destes separadores.

## <a name="quick-start-quick-startquickstart"></a>Guia de introdução (![Guia de introdução][QuickStart])
Dependendo da edição de serviços de BizTalk, todas as opções listadas poderão não estar disponíveis. 
<table border="1">
    <tr>
        <td><strong>Obter as ferramentas de</strong></td>
        <td>Transferir o SDK de serviços de BizTalk para instalar os modelos de projeto do Visual Studio no seu computador de desenvolvimento no local. Estes modelos de criar os <strong>Serviços de BizTalk</strong> (ponte) e os projetos <strong>BizTalk serviço artefactos</strong> (transformação) Visual Studio que são implementados no seu serviço de BizTalk.
        <br/><br/>
        <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302335">Como é que começar a utilizar o SDK do Azure BizTalk Services</a> e <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=241589">instalar o SDK do Azure BizTalk Services</a> lista os passos para começar a utilizar.
        </td>
    </tr>
    <tr>
        <td><strong>Criar parceiro acordos</strong></td>
        <td>Abre o Portal de serviços de BizTalk Azure alojado no Azure onde adiciona parceiros e criar X12, AS2 e os acordos EDIFACT editar.
        <br/><br/>
        <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=303653">Configurar componentes para editar Messaging no Portal de serviços de BizTalk</a> lista os passos para começar a utilizar.
        </td>
    </tr>

<tr>
        <td><strong>Saiba mais sobre os serviços de BizTalk</strong></td>
        <td>Vá para o <a HREF="http://azure.microsoft.com/documentation/services/biztalk-services/">Centro de formação</a> para obter mais informações sobre os serviços de BizTalk Azure.</td>
</tr>
</table>


Na barra de tarefas na parte inferior, pode:

<table border="1">

<tr>
<td><strong>Gerir</strong> sua implementação da aplicação</td>
<td>Abre-se o portal do Azure BizTalk serviços. O Portal de serviços de BizTalk é entrada para a configuração de editar, incluindo adicionar parceiros e criar X12, AS2 e os acordos EDIFACT.
<br/><br/>
Este é o mesmo que <strong>criar acordos de parceiro</strong> no separador <strong>Quick Start</strong> .
<br/><br/>
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=303653">Configurar componentes para editar Messaging no Portal de serviços de BizTalk</a> fornece mais informações sobre o Portal de serviços de BizTalk.</td>
</tr>

<tr>
<td><strong>Informações de ligação</strong> do espaço de nomes de controlo de acesso</td>
<td>Quando selecionar informações de ligação, em seguida, o espaço de nomes de controlo de acesso, o emissor predefinida e a chave predefinido são apresentados. Pode copiar estes valores.
<br/><br/>
Também pode abrir o Portal de controlo de acesso. <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=285670">Criar um espaço de nomes do controlo de acesso</a> fornece mais informações sobre o Portal de controlo de acesso.</td>
</tr>

<tr>
<td><strong>Sincronizar teclas</strong> na conta de armazenamento</td>
<td>Quando cria uma conta de armazenamento, uma chave primária e chave secundário são criados automaticamente. Estas teclas de encriptação controlam o acesso à sua conta de armazenamento. O serviço de BizTalk utiliza automaticamente a chave primária. <strong>Teclas de sincronização</strong> permitem aos utilizadores alternar entre a chave primária e a chave secundária sem interromper o serviço de BizTalk.
<br/><br/>
Por exemplo, pretende que o serviço de BizTalk para utilizar uma nova chave primária da conta de armazenamento. Para fazer isto:
<br/><br/>
<ol>
<li>Selecione o seu serviço de BizTalk e selecione <strong>Teclas de sincronização</strong>. Selecione a chave secundária. Após fazer isto, o serviço de BizTalk começa a utilizar a chave secundário.</li>
<li>No portal do Azure clássico, selecione a sua conta de armazenamento e gerar a chave primária. Lembre-se de que o serviço de BizTalk está a utilizar a chave secundária.</li>
<li>Selecione o seu serviço de BizTalk e selecione <strong>Teclas de sincronização</strong>. Agora, selecione a chave primária. Este é a chave primária nova que geradas novamente.</li>
<li>No portal do Azure clássico, selecione a sua conta de armazenamento e gerar a chave secundária.</li>
</ol>
<br/>
Este processo é denominado ""passagem com o rato teclas. O objetivo é permitir que utilizadores alternar entre a chave primária e a chave secundária sem interromper o serviço de BizTalk.</td>
</tr>

<tr>
<td><strong>Eliminar</strong> a aplicação</td>
<td>Ao selecionar eliminar, o seu serviço de BizTalk e todos os itens implementados-são removidos.</td>
</tr>
</table>


## <a name="dashboard"></a>Dashboard
Dependendo da edição de serviços de BizTalk, todas as opções listadas poderão não estar disponíveis. 

Quando selecionar o nome do serviço de BizTalk, o separador Dashboard é apresentado. No Dashboard, pode:

##### <a name="usage-overview-shows-the-number-of-used-hybrid-connections"></a>Descrição geral de utilização: Mostra o número de ligações utilizadas de híbrido
Utilização de dados apresenta também GB. 

##### <a name="metric-graph-shows-a-fixed-list-of-performance-metrics"></a>Gráfico métrico: Mostra uma lista de métricas de desempenho fixa
Estes métricas fornecem valores em tempo real sobre o estado de funcionamento do serviço BizTalk. Também pode escolher os valores **relativo** ou **absoluto** e o intervalo de tempo de **intervalo** de métricas que são apresentados no gráfico. 

Para obter uma descrição destes métricas de desempenho, aceda a [Métricas disponíveis](#Metrics) neste tópico.


##### <a name="quick-glance-lists-your-biztalk-service-properties"></a>Vista rápida: Listas as propriedades do serviço de BizTalk

<table border="1">

<tr>
<td><strong>Atualizar as credenciais de base de dados de controlo</strong></td>
<td>Altera o nome de utilizador e palavra-passe utilizadas para iniciar sessão para a base de dados de controlo.</td>
</tr>
<tr>
<td><strong>Atualizar um certificado SSL</strong></td>
<td>Pode atualizar o serviço de BizTalk para utilizar um certificado SSL diferente. Um certificado SSL autoassinado é criado automaticamente quando <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302280">criar o serviço de BizTalk</a>.</td>
</tr>
<tr>
<td><strong>Transferir o certificado</strong></td>
<td>Pode transferir o certificado SSL utilizado pelo seu serviço de BizTalk para uma máquina local.</td>
</tr>
<tr>
<td><strong>Estado</strong></td>
<td>Apresenta o estado atual do seu serviço de BizTalk. Consulte o artigo <a HREF="http://go.microsoft.com/fwlink/p/?LinkID=329870">BizTalk serviços: gráfico de estado do serviço</a>. </td>
</tr>
<tr>
<td><strong>URL do serviço</strong></td>
<td>O URL do seu serviço de BizTalk. Isto é o mesmo, tal como o <strong>URL do domínio</strong> introduzido quando é criado o seu serviço de BizTalk.</td>
</tr>
<tr>
<td><strong>Endereço IP público de Virtual (VIP)</strong></td>
<td>O endereço IP associado ao seu serviço de BizTalk. É utilizado para todos os pontos finais de entrada e é o endereço de origem para o tráfego de saída. Este endereço IP pertencer no seu serviço de BizTalk desde que é criado. Se eliminar o serviço de BizTalk, o endereço IP é atribuído a outro serviço de BizTalk.</td>
</tr>
<tr>
<td><strong>ACS espaço de nomes</strong></td>
<td>Autentica com o serviço de BizTalk.</td>
</tr>
<tr>
<td><strong>Edition</strong></td>
<td>Listas a edição introduzidas quando o serviço de BizTalk é criado.</td>
</tr>
<tr>
<td><strong>Localização</strong></td>
<td>Apresenta a região geográfica que aloja o seu serviço de BizTalk.</td>
</tr>
<tr>
<td><strong>Criado</strong></td>
<td>Apresenta a data e hora, que o serviço de BizTalk foi criado.</td>
</tr>
<tr>
<td><strong>Controlo da base de dados</strong></td>
<td>O nome da base de dados do Azure SQL que armazena as tabelas de controlo utilizadas pelo seu serviço de BizTalk. 
<br/><br/>
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302280">Explicação das requisitos</a> fornece detalhes sobre a base de dados de controlo.</td>
</tr>
<tr>
<td><strong>Armazenamento de monitorização/arquivo</strong></td>
<td>O nome de conta de armazenamento do Windows Azure que armazena a saída de monitorização do seu serviço de BizTalk.
<br/><br/>
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=302280">Explicação das requisitos</a> fornece detalhes sobre a conta de armazenamento.</td>
</tr>
<tr>
<td><strong>Nome da subscrição</strong></td>
<td>Apresenta a subscrição que aloja o seu serviço de BizTalk. A subscrição controla aceder ao portal clássico Azure.</td>
</tr>
<tr>
<td><strong>ID da subscrição</strong></td>
<td>Quando é criada uma subscrição, é gerado automaticamente um ID da subscrição. Ao utilizar os REST APIs, que poderá necessitar de introduzir o ID da subscrição.</td>
</tr>
</table>

[BizTalk serviços: portal clássico de utilizar o Azure aprovisionamento](http://go.microsoft.com/fwlink/p/?LinkID=302280) lista os passos para criar um serviço de BizTalk.


##### <a name="manage-connection-information-sync-keys-and-delete-in-the-task-bar"></a>Gerir informações de ligação, as teclas de sincronização e eliminar na barra de tarefas:

<table border="1">

<tr>
<td><strong>Gerir</strong> sua implementação da aplicação</td>
<td>Abre-se o Portal de serviços de Azure BizTalk. O Portal de serviços de BizTalk é entrada para a configuração de editar, incluindo adicionar parceiros e criar X12, AS2 e os acordos EDIFACT.
<br/><br/>
Este é o mesmo que <strong>criar acordos de parceiro</strong> no separador <strong>Quick Start</strong> .
<br/><br/>
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=303653">Configurar componentes para editar Messaging no Portal de serviços de BizTalk</a> fornece mais informações sobre o Portal de serviços de BizTalk.</td>
</tr>
<tr>
<td><strong>Informações de ligação</strong> do espaço de nomes de controlo de acesso</td>
<td>Apresenta o espaço de nomes de controlo de acesso, emissor predefinida e valores de chave predefinida; Pode copiado.
<br/><br/>
Também pode abrir o Portal de controlo de acesso. Este Portal de controlo de acesso é igual a através da opção do Active Directory no painel de navegação à esquerda.
<br/><br/>
<a HREF="http://go.microsoft.com/fwlink/p/?LinkID=285670">Gerir o seu ACS espaço de nomes de</a> fornece mais informações sobre o Portal de controlo de acesso.</td>
</tr>
<tr>
<td><strong>Sincronizar teclas</strong> na conta de armazenamento</td>
<td>Quando cria uma conta de armazenamento, uma chave primária e chave secundário são criados automaticamente. Estas teclas de encriptação controlam o acesso à sua conta de armazenamento. O serviço de BizTalk utiliza automaticamente a chave primária. <strong>Teclas de sincronização</strong> permitem aos utilizadores alternar entre a chave primária e a chave secundária sem interromper o serviço de BizTalk.
<br/><br/>
Por exemplo, pretende que o serviço de BizTalk para utilizar uma nova chave primária da conta de armazenamento. Para fazer isto:
<br/><br/>
<ol>
<li>Selecione o seu serviço de BizTalk e selecione <strong>Teclas de sincronização</strong>. Selecione a chave secundária. Após fazer isto, o serviço de BizTalk começa a utilizar a chave secundário.</li>
<li>No portal do Azure clássico, selecione a sua conta de armazenamento e gerar a chave primária. Lembre-se de que o serviço de BizTalk está a utilizar a chave secundária.</li>
<li>Selecione o seu serviço de BizTalk e selecione <strong>Teclas de sincronização</strong>. Agora, selecione a chave primária. Este é a chave primária nova que geradas novamente.</li>
<li>No portal do Azure clássico, selecione a sua conta de armazenamento e gerar a chave secundária.</li>
</ol>
<br/>
Este processo é denominado ""passagem com o rato teclas. O objetivo é permitir que utilizadores alternar entre a chave primária e a chave secundária sem interromper o serviço de BizTalk.</td>
</tr>

<tr>
<td><strong>Eliminar</strong> a aplicação</td>
<td>O serviço de BizTalk e todos os itens implementados-são removidos.</td>
</tr>
</table>


## <a name="monitor"></a>Monitor
Não se aplica à edição gratuito.

Quando selecionar o nome do serviço de BizTalk, no separador Monitor está disponível e apresenta o seguinte:

##### <a name="metric-graph-displays-the-selected-performance-metrics"></a>Métrico gráfico: Apresenta as métricas de desempenho seleccionados
Estes métricas fornecem valores em tempo real sobre o estado de funcionamento do serviço BizTalk. Escolher quais métricas de desempenho são apresentadas. Um máximo de seis métricas de desempenho pode ser apresentado em simultâneo. 

Também pode escolher os valores **relativo** ou **absoluto** e o intervalo de tempo de **intervalo** de métricas que são apresentadas. 

##### <a name="to-remove-or-display-metrics-in-the-graph"></a>Para remover ou apresentar métricas no gráfico:
1. Selecione o separador **Monitor** .
2. Selecione **Adicionar métricas** na barra de tarefas:  
![Selecione adicionar métricas][AddMetrics]
3. Verifique as métricas de desempenho que pretende apresentar.
4. Selecione a marca de verificação para regressar ao separador **Monitor** .
5. Selecione o círculo junto a métrica para apresentar o valor desse métrica no gráfico.  

    Por exemplo, a **Utilização da CPU** métrica estiver a cinzento; o resultado não é apresentado no gráfico:  
![Métrica de utilização da CPU está desativada][GrayedMetric]  

    Selecione a cinzento saída círculo para ativar a **Utilização da CPU** métrica apresentar o resultado no gráfico:  
![Métrica de utilização da CPU é activada][EnabledMetric]

6. Para remover uma métrica do gráfico de visualização e a lista, selecione **Eliminar métrica** na barra de tarefas. Para adicionar o Retroceder métrica à lista, selecione **Adicionar métricas** na barra de tarefas, verifique a métrica do e selecione a marca de verificação para regressar ao separador **Monitor** . Selecione a cinzento saída círculo para ativar a métrica do.

## <a name="Metrics"></a>Métricas disponíveis
Estão disponíveis as seguintes contadores/métricas de desempenho:

<table border="1">

<tr>
<td><strong>Latência RountdTrip</strong></td>
<td>Mostra o tempo médio despendido em milissegundos (ms) para processar uma mensagem a partir do momento que a mensagem é recebida até que a mensagem é completamente processada pelo serviço BizTalk através de todas as pontes. Apenas as mensagens com êxito processadas são contadas.<br/><br/>
Quando ocorrem os seguintes eventos, é criado um carimbo de data/hora:
<ul>
<li>Mensagem introduz o gateway</li>
<li>Mensagem é encaminhada para o destino</li>
<li>É recebida uma resposta de destino</li>
<li>Resposta de confirmação de destino enviada para o gateway</li>
</ul>
<br/>
Esta métrica mostra o resultado do cálculo seguinte:
<br/><br/>
[Destino confirmação resposta enviada para o gateway] - [mensagem introduz o gateway]</td>
</tr>
<tr>
<td><strong>Falhas na origem</strong></td>
<td>Apresenta o número total de mensagens que falhou pelo serviço BizTalk quando no mensagens dos pontos finais de origem.</td>
</tr>
<tr>
<td><strong>Utilização da CPU</strong></td>
<td>Lista % de média tempo de processador de todas as instâncias de função.</td>
</tr>
<tr>
<td><strong>Latência de processamento</strong></td>
<td>Apresenta o tempo médio despendido em milissegundos (ms) para processar uma mensagem pelo serviço BizTalk através de todas as pontes, excluindo o tempo gasto em destinos. Apenas as mensagens com êxito processadas são contadas.<br/><br/>
Quando ocorrem cada um dos seguintes eventos, é criado um carimbo de data/hora:

<ul>
<li>Mensagem introduz o gateway</li>
<li>Mensagem é encaminhada para o destino</li>
<li>É recebida uma resposta de destino</li>
<li>Resposta de confirmação de destino enviada para o gateway</li>
</ul>
<br/>Esta métrica mostra o resultado do cálculo seguinte:<br/><br/>
[Destino confirmação resposta enviada para o gateway] - [mensagem introduz o gateway] - [é recebida uma resposta de destino] + [mensagem é encaminhada para o destino]</td>
</tr>
<tr>
<td><strong>Falhas de processo</strong></td>
<td>Apresenta o número total de mensagens que falhou durante o processamento pelo serviço BizTalk através de todas as pontes dentro de um intervalo de tempo.</td>
</tr>
<tr>
<td><strong>Mensagens enviadas</strong></td>
<td>Apresenta o número total de mensagens enviadas pelo serviço BizTalk através de todas as pontes dentro de um intervalo de tempo. Esta métrica é incrementada quando uma mensagem enviada a partir de uma tubagem atinge o destino da rota. Esta métrica não indicar que uma mensagem é processada com êxito.<br/><br/>
Num cenário de pedido de resposta, a métrica é incrementada quando o destino da rota envia uma confirmação de recibo de volta para a tubagem.</td>
</tr>
<tr>
<td><strong>Mensagens recebidas</strong></td>
<td>Apresenta o número total de mensagens recebida pelo serviço BizTalk através de todas as pontes dentro de um intervalo de tempo. Esta métrica é incrementada quando uma nova mensagem é recebida pelas em curso.</td>
</tr>
<tr>
<td><strong>Mensagens de processo</strong></td>
<td>Apresenta o número total de mensagens atualmente a ser processadas pelo serviço BizTalk dentro de um intervalo de tempo.</td>
</tr>
<tr>
<td><strong>Mensagens processadas</strong></td>
<td>Apresenta o número total de mensagens com êxito processados pelo serviço BizTalk através de todas as pontes dentro de um intervalo de tempo. Esta métrica é incrementada quando uma mensagem é recebida com êxito pela tubagem e encaminhada com êxito ao destino.</td>
</tr>
</table>


## <a name="scale"></a>Escala
No separador escala, pode adicionar ou subtrair o número de unidades utilizado pelo seu serviço de BizTalk. Por predefinição, existe uma unidade configurada. Podem ser adicionadas unidades adicionais para dimensionar o seu serviço de BizTalk. Quando aumentar a escala, são aumentando débito. A quantidade de recursos também aumenta, incluindo implementados pontes, acordos, LOB ligações e power de processamento. Por exemplo, pode aumenta a escala de unidade de 1 a 2 unidades. Esta situação, pode implementar o dobro do número de pontes, faça duplo os acordos, faça duplo as ligações de LOB e faça duplo da capacidade de processamento.

Algumas edições BizTalk não fornecem uma opção de escala. Neste caso, uma unidade é permitida. Para determinar quantas unidades que pode ser dimensionada a sua edição, consulte [BizTalk serviços: edições gráfico](biztalk-editions-feature-chart.md).

Aumentar o número de unidades pode ter um impacto preços. Se aumentar as unidades de, selecionar **Guardar** apresenta uma mensagem que indica-lhe se faturação é afetada. Em seguida, escolha continuar. Quando o que aumenta o número de unidades, as alterações de estado de serviço de BizTalk a partir do Active para atualizar. No estado de actualização, o seu serviço de BizTalk continua a ser executado.

[BizTalk serviços: edições gráfico](biztalk-editions-feature-chart.md) define uma "unidade".


## <a name="configure"></a>Configurar
Não se aplica às ligações híbrido.

Define o estado de cópia de segurança para nenhum ou automático. Quando definido como nenhum, sem cópias de segurança são criadas automaticamente. Quando é definida como automático, configure a localização da cópia de segurança, a frequência da cópia de segurança e durante quanto tempo para manter os ficheiros de cópia de segurança. 

[BizTalk serviços: cópia de segurança e restaurar](biztalk-backup-restore.md) fornece os detalhes. 


## <a name="HybridConnections"></a>Ligações de híbrido
Ligações de híbrido ligam uma aplicação do Azure, como Web Apps ou aplicações móveis no serviço de aplicação do Azure, a um recurso de no local que utiliza uma porta TCP estática, tal como SQL Server, MySQL, HTTP Web API e a maioria dos serviços Web personalizados. Híbrido ligações são geridas nos serviços do BizTalk no portal do Azure clássico.

Para criar ligações de híbrido na aplicação de serviço de Azure, consulte [Access no local recursos utilizando ligações de híbrido no Azure aplicação de serviço](../app-service-web/web-sites-hybrid-connection-get-started.md).

Para criar ou gerir ligações de híbrido no Azure BizTalk Services, consulte o artigo [Híbrido ligações](integration-hybrid-connection-overview.md).



## <a name="next"></a>Seguinte
Agora que já está familiarizado com os separadores de diferentes, pode obter mais informações sobre as funcionalidades dos serviços do Azure BizTalk:

- [Serviços de BizTalk: limitação](biztalk-throttling-thresholds.md)  
- [BizTalk serviços: Nome do emissor e chave emissor](biztalk-issuer-name-issuer-key.md)  
- [BizTalk serviços: Cópia de segurança e restauro](biztalk-backup-restore.md)

## <a name="see-also"></a>Consulte também
- [Ligações de híbrido](integration-hybrid-connection-overview.md)  
- [Serviços de BizTalk: Programador, Basic, padrão e Premium edições gráfico](biztalk-editions-feature-chart.md)  
- [BizTalk serviços: Portal clássico Azure utilizando o aprovisionamento](biztalk-provision-services.md)  
- [BizTalk serviços: Gráfico de estado do serviço BizTalk](biztalk-service-state-chart.md)  
- [Como é que começar a utilizar o SDK do Azure BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=302335)

[QuickStart]: ./media/biztalk-dashboard-monitor-scale-tabs/QuickStartIcon.png
[AddMetrics]: ./media/biztalk-dashboard-monitor-scale-tabs/WABS_AddMetrics.png
[GrayedMetric]: ./media/biztalk-dashboard-monitor-scale-tabs/WABS_GrayedMetric.png
[EnabledMetric]: ./media/biztalk-dashboard-monitor-scale-tabs/WABS_EnabledMetric.png
 
