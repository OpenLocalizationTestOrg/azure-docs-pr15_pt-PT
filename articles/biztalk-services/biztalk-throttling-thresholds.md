<properties 
    pageTitle="Saiba mais sobre limitação nos serviços do BizTalk | Microsoft Azure" 
    description="Saiba mais sobre limitação limiares e resultante runtime comportamentos para serviços de BizTalk. Limitação baseia utilização de memória e o número de mensagens. MAK, MAB, WABS" 
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





# <a name="biztalk-services-throttling"></a>Serviços de BizTalk: limitação

Serviços de BizTalk Azure implementa o serviço limitação com base em duas condições: a utilização de memória e o número de mensagens simultâneas de processamento. Este tópico apresenta os limiares optimização e descreve o comportamento de Runtime quando ocorre uma condição optimização.

## <a name="throttling-thresholds"></a>Limitação limiares

A tabela seguinte lista de origem optimização e limiares:

||Descrição|Limiar mínimo|Limite superior|
|---|---|---|---|
|Memória|% do total sistema memória disponível/PageFileBytes. <p><p>Total PageFileBytes disponíveis é aproximadamente 2 vezes a quantidade de RAM do sistema.|60%|70%|
|Processamento de mensagens|Número de mensagens em simultâneo de processamento|40 * número de núcleos|100 * número de núcleos|

Quando um limiar alto for atingido, é iniciado Azure BizTalk serviços optimizar. Limitação marcas quando o limiar mínimo for atingido. Por exemplo, o serviço está a utilizar 65% memória do sistema. Neste caso, não optimizar o serviço. O serviço é iniciado com 70% memória do sistema. Esta situação, o serviço acelera e continua a aceleração até que o serviço utiliza memória do sistema 60% (baixa limiar).

Azure BizTalk serviços controla o optimização Estado (normal vs. Estado desacelerado) e a duração optimização.


## <a name="runtime-behavior"></a>Tempo de execução do comportamento

Quando Azure BizTalk serviços introduz um Estado optimização, ocorre o seguinte:

- A aceleração é por instância de função. Por exemplo:<br/>
RoleInstanceA é limitação. Não é limitação RoleInstanceB. Esta situação, mensagens em RoleInstanceB são processadas conforme esperado. Mensagens em RoleInstanceA são eliminadas e falharem com o seguinte erro:<br/><br/>
**O servidor está ocupado. Volte a tentar.**<br/><br/>
- Quaisquer origens solicitação não as de inquérito ou transferir uma mensagem. Por exemplo:<br/>
Uma tubagem obtém mensagens de uma origem FTP externa. Obtém a instância de função efetuar a solicitação para uma fase de optimização. Esta situação, pipeline de deixa de transferir mensagens adicionais até que a instância de função pare limitação.
- É enviada uma resposta ao cliente para que o cliente pode submeter a mensagem.
- Tem de esperar até que a limitação seja resolvido. Especificamente, tem de esperar até que o limiar mínimo for atingido.

## <a name="important-notes"></a>Notas importantes
- Limitação não pode ser desativada.
- Limitação limiares seja modificado.
- Limitação é implementada todo o sistema.
- O servidor de base de dados do SQL Azure também tem limitação incorporados.

## <a name="additional-azure-biztalk-services-topics"></a>Tópicos de Azure BizTalk serviços adicionais

-  [Instalar os serviços de Azure BizTalk SDK](http://go.microsoft.com/fwlink/p/?LinkID=241589)<br/>
-  [Tutoriais: Serviços de Azure BizTalk](http://go.microsoft.com/fwlink/p/?LinkID=236944)<br/>
-  [Como é que começar a utilizar o SDK do Azure BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=302335)<br/>
-  [Serviços de Azure BizTalk](http://go.microsoft.com/fwlink/p/?LinkID=303664)<br/>

## <a name="see-also"></a>Consulte também
- [Serviços de BizTalk: Programador, Basic, padrão e Premium edições gráfico](http://go.microsoft.com/fwlink/p/?LinkID=302279)<br/>
- [BizTalk serviços: Portal clássico Azure utilizando o aprovisionamento](http://go.microsoft.com/fwlink/p/?LinkID=302280)<br/>
- [Serviços de BizTalk: Gráfico de estado de aprovisionamento](http://go.microsoft.com/fwlink/p/?LinkID=329870)<br/>
- [BizTalk serviços: Separadores de Dashboard, Monitor e escala](http://go.microsoft.com/fwlink/p/?LinkID=302281)<br/>
- [BizTalk serviços: Cópia de segurança e restauro](http://go.microsoft.com/fwlink/p/?LinkID=329873)<br/>
- [BizTalk serviços: Nome do emissor e chave emissor](http://go.microsoft.com/fwlink/p/?LinkID=303941)<br/>
 
