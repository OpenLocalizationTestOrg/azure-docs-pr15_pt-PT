<properties 
    pageTitle="Resolver problemas de serviços de BizTalk utilizando registos de operação | Microsoft Azure" 
    description="Resolver problemas de serviços de BizTalk utilizando os registos de operação. MAK, MAB, WABS" 
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
    ms.date="08/15/2016" 
    ms.author="mandia"/>


# <a name="biztalk-services-troubleshoot-using-operation-logs"></a>Serviços de BizTalk: Resolução de problemas com os registos de operação

## <a name="what-are-the-operation-logs"></a>Quais são os registos de operação
Registos de operação é uma funcionalidade de serviços de gestão disponível no portal de clássico do Azure que permite-lhe ver os registos de históricos de operações efetuadas no seus serviços Azure, incluindo os serviços de BizTalk. Permite-lhe ver dados históricos relacionados com operações de gestão de na sua subscrição do serviço de BizTalk até ao 180 dias.

> [AZURE.NOTE]Esta funcionalidade só para capturar a registos para operações de gestão nos serviços de BizTalk, por exemplo, quando o serviço foi iniciado, cópias para cima, e assim sucessivamente. Estas operações são registadas independentemente se são executadas a partir do Azure portal clássico ou ao utilizar as [BizTalk serviço REST APIs](http://msdn.microsoft.com/library/azure/dn232347.aspx). Para obter uma lista completa de operações que são registadas utilizando os serviços de gestão, consulte [Operações registadas utilizando Azure Management Services](#bizops).<br/><br/>
Isto não capturar os registos para actividades relacionadas com o tempo de execução do serviço de BizTalk (como mensagem processada pelos pontes etc.). Para ver estes registos, utilize a vista de controlo a partir do portal de serviços de BizTalk. Para mais informações, consulte o artigo [De rastreio de mensagens](http://msdn.microsoft.com/library/azure/hh949805.aspx).

## <a name="view-biztalk-services-operation-logs"></a>Ver registos de funcionamento de serviços de BizTalk
1. No portal do Azure clássico, selecione **Os serviços de gestão**e, em seguida, selecione o separador de **Registos de operação** .
2. Pode filtrar os registos com base nos diferentes parâmetros como subscrição, intervalo de datas, tipo de serviço (por exemplo, BizTalk serviços), nome do serviço ou o estado da operação de (com êxito, falha).
3. Selecione a marca de verificação para ver a lista filtrada. A imagem seguinte mostra atividades relacionadas com testbiztalkservice:  ![ver registos de operação][ViewLogs] 
4. Para ver mais informações sobre uma operação específica, selecione a linha e clique em **Detalhes** na barra de tarefas na parte inferior.


## <a name="bizops"></a>Operações registadas utilizando os serviços de gestão do Azure
A tabela seguinte apresenta as operações que são registadas ao utilizar os serviços de gestão do Azure:

Nome da operação | Tarefa
--- | ---
CreateBizTalkService | Operação para criar um novo serviço BizTalk
DeleteBizTalkService | Operação para eliminar um serviço de BizTalk
RestartBizTalkService | Operação para reiniciar a um serviço de BizTalk
StartBizTalkService | Operação para iniciar um serviço de BizTalk
StopBizTalkService | Operação para parar de um serviço de BizTalk
DisableBizTalkService | Operação para desativar um serviço de BizTalk
EnableBizTalkService | Operação para ativar um serviço de BizTalk
BackupBizTalkService | Operação para criar cópias de um serviço de BizTalk
RestoreBizTalkService | Operação para restaurar um serviço de BizTalk da cópia de segurança especificada
SuspendBizTalkService | Operação para suspender um serviço de BizTalk
ResumeBizTalkService | Operação para retomar a um serviço de BizTalk
ScaleBizTalkService | Operação para dimensionar um serviço de BizTalk para cima ou para baixo
ConfigUpdateBizTalkService | Operação para atualizar a configuração de um serviço de BizTalk
ServiceUpdateBizTalkService | Operação para atualizar ou alterar um serviço de BizTalk para uma versão diferente
PurgeBackupBizTalkService | Operação para limpar cópias de segurança do serviço BizTalk fora do período de retenção


## <a name="see-also"></a>Consulte também
- [Serviço de cópia de segurança BizTalk](http://go.microsoft.com/fwlink/p/?LinkID=325584)
- [Restaurar o serviço de BizTalk da cópia de segurança](http://go.microsoft.com/fwlink/p/?LinkID=325582)
- [Serviços de BizTalk: Programador, Basic, padrão e Premium edições gráfico](http://go.microsoft.com/fwlink/p/?LinkID=302279)
- [BizTalk serviços: Portal clássico Azure utilizando o aprovisionamento](http://go.microsoft.com/fwlink/p/?LinkID=302280)
- [Serviços de BizTalk: Gráfico de estado de aprovisionamento](http://go.microsoft.com/fwlink/p/?LinkID=329870)
- [BizTalk serviços: Separadores de Dashboard, Monitor e escala](http://go.microsoft.com/fwlink/p/?LinkID=302281)
- [Serviços de BizTalk: limitação](http://go.microsoft.com/fwlink/p/?LinkID=302282)
- [BizTalk serviços: Nome do emissor e chave emissor](http://go.microsoft.com/fwlink/p/?LinkID=303941)
- [Como é que começar a utilizar o SDK do Azure BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=302335)

[ViewLogs]: ./media/biztalk-troubleshoot-using-ops-logs/Operation-Logs.png
 
