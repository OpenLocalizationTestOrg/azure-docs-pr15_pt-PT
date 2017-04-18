<properties 
    pageTitle="Tarefas permitidas na diferentes Estados ou Estados nos serviços do BizTalk | Microsoft Azure" 
    description="As ações/operações permitidas na Estado MAK, MAB diferente: parar, iniciar, reinicie, suspender, retomar, eliminar, dimensionar, atualizar configuração e suporte para cima" 
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



# <a name="biztalk-services-service-state-chart"></a>Serviços de BizTalk: Gráfico de estado do serviço
Consoante o estado atual do serviço BizTalk, existem operações que pode ou não é possível executar o serviço de BizTalk.

Por exemplo, pode aprovisionar um novo serviço BizTalk no portal do Azure clássico. Quando terminar com êxito, o serviço de BizTalk está num Estado ativo. No Estado ativo, pode deixar do serviço de BizTalk. Se deixar de ser efetuada com êxito, o serviço de BizTalk vai a um Estado de parado. Se parar falhar, o serviço de BizTalk vai a um Estado de StopFailed. No Estado StopFailed, pode reiniciar o serviço de BizTalk. Se tentar uma operação que não é permitida, como o currículo serviço BizTalk, ocorre o seguinte erro:

**Operação não é permitida**

Aprovisionamento de um serviço de BizTalk, aceda a [BizTalk serviços: portal clássico de aprovisionamento Azure utilizando](http://go.microsoft.com/fwlink/p/?LinkID=302280).

As tabelas seguintes listam as operações ou ações que podem ser concluídas quando o serviço de BizTalk está num estado específico. Um ✔ significa que a operação é permitida enquanto estiver desse Estado. Uma entrada em branco significa que não pode ser efetuada a operação enquanto estiver desse Estado.

## <a name="start-stop-restart-suspend-resume-and-delete-operations"></a>Iniciar, parar, reinicie, suspender, currículo e eliminar operações
<table border="1">
<tr>
        <th colspan="15">Operação ou ação</th>
</tr>

<tr>
        <th rowspan="18">Estado do serviço BizTalk</th>
</tr>
<tr bgcolor="FAF9F9">
        <th> </th>
        <th>Iniciar</th>
        <th>Parar</th>
        <th>Reinicie o</th>
        <th>Suspender</th>
        <th>Currículo</th>
        <th>Eliminar</th>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Ativa</b></td>
<td> </td>
<td><center>✔</center></td>
<td><center>✔</center></td>
<td><center>✔</center></td>
<td> </td>
<td><center>✔</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Desativado</b></td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td><center>✔</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Suspensa</b></td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td><center>✔</center></td>
<td><center>✔</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Parado</b></td>
<td><center>✔</center></td>
<td> </td>
<td><center>✔</center></td>
<td> </td>
<td> </td>
<td><center>✔</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Ocorreu uma falha de atualização de serviço</b></td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td><center>✔</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>DisableFailed</b></td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td><center>✔</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>EnableFailed</b></td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td><center>✔</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>StartFailed<br/>
StopFailed<br/>
RestartFailed</b></td>
<td><center>✔</center></td>
<td><center>✔</center></td>
<td><center>✔</center></td>
<td> </td>
<td> </td>
<td><center>✔</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>SuspendedFailed<br/>
ResumeFailed</b></td>
<td> </td>
<td> </td>
<td> </td>
<td><center>✔</center></td>
<td><center>✔</center></td>
<td><center>✔</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>CreatedFailed<br/>
RestoreFailed<br/></b></td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td><center>✔</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>ConfigUpdateFailed</b></td>
<td> </td>
<td> </td>
<td><center>✔</center></td>
<td> </td>
<td> </td>
<td><center>✔</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>ScaleFailed</b></td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td> </td>
<td><center>✔</center></td>
</tr>
</table>
<br/>

## <a name="scale-update-configuration-and-backup-operations"></a>Escala, a configuração de atualização e operações de cópia de segurança
<table border="1">
<tr>
        <th colspan="15">Operação ou ação</th>
</tr>

<tr>
        <th rowspan="18">Estado do serviço BizTalk</th>
</tr>
<tr bgcolor="FAF9F9">
        <th> </th>
        <th>Escala</th>
        <th>Configuração de actualização</th>
        <th>Cópia de segurança</th>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Ativa</b></td>
<td><center>✔</center></td>
<td><center>✔</center></td>
<td><center>✔</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Desativado</b></td>
<td> </td>
<td> </td>
<td> </td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Suspensa</b></td>
<td> </td>
<td> </td>
<td><center>✔</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Parado</b></td>
<td> </td>
<td> </td>
<td><center>✔</center></td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>Ocorreu uma falha de atualização de serviço</b></td>
<td> </td>
<td> </td>
<td> </td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>DisableFailed</b></td>
<td> </td>
<td> </td>
<td> </td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>EnableFailed</b></td>
<td> </td>
<td> </td>
<td> </td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>StartFailed<br/>
StopFailed<br/>
RestartFailed</b></td>
<td> </td>
<td><center>✔</center></td>
<td> </td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>SuspendedFailed<br/>
ResumeFailed</b></td>
<td> </td>
<td> </td>
<td> </td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>CreatedFailed<br/>
RestoreFailed<br/></b></td>
<td> </td>
<td> </td>
<td> </td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>ConfigUpdateFailed</b></td>
<td> </td>
<td><center>✔</center></td>
<td> </td>
</tr>
<tr>
<td bgcolor="FAF9F9"><b>ScaleFailed</b></td>
<td><center>✔</center></td>
<td> </td>
<td> </td>
</tr>
</table>

## <a name="see-also"></a>Consulte também
- [BizTalk serviços: Portal clássico Azure utilizando o aprovisionamento](http://go.microsoft.com/fwlink/p/?LinkID=302280)<br/>
- [BizTalk serviços: Separadores de Dashboard, Monitor e escala](http://go.microsoft.com/fwlink/p/?LinkID=302281)<br/>
- [Serviços de BizTalk: Programador, Basic, padrão e Premium edições gráfico](http://go.microsoft.com/fwlink/p/?LinkID=302279)<br/>
- [BizTalk serviços: Cópia de segurança e restauro](http://go.microsoft.com/fwlink/p/?LinkID=329873)<br/>
- [Serviços de BizTalk: limitação](http://go.microsoft.com/fwlink/p/?LinkID=302282)<br/>
- [BizTalk serviços: Nome do emissor e chave emissor](http://go.microsoft.com/fwlink/p/?LinkID=303941)<br/>
- [Como é que começar a utilizar o SDK do Azure BizTalk Services](http://go.microsoft.com/fwlink/p/?LinkID=302335)


 
