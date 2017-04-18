<properties 
   pageTitle="Notas de lançamento 0,2 StorSimple 8000 atualizar | Microsoft Azure"
   description="Descreve as novas funcionalidades e correções, problemas em aberto e soluções disponíveis para Janeiro de 2015 lançamento do Microsoft Azure StorSimple (atualização de 0,2)."
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="carmonm"
   editor="" />
 <tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="05/16/2016"
   ms.author="v-sharos" />


# <a name="storsimple-8000-series-update-02-release-notes---january-2015"></a>Notas de lançamento 0,2 de actualização de série de 8000 StorSimple - Janeiro de 2015

## <a name="overview"></a>Descrição geral

As seguintes notas de lançamento identificam os problemas críticos abrir para a versão de Janeiro de 2015 do Microsoft Azure StorSimple. Também contêm uma lista das atualizações de software e firmware de StorSimple incluídas nesta versão. Este é o segundo lançamento depois da StorSimple 8000 série versão é efetuada ficará disponível de Julho de 2014.
  
Esta atualização não altere a versão do software dispositivo físico da actualização de Outubro. Este continua a ser versão 6.3.9600.17312. A imagem utilizada pela imagem do dispositivo virtual foi alterado nesta versão. Por conseguinte, todos os dispositivos virtuais novos criados após 20/1/2015 irão mostrar a versão como 6.3.9600.17361.  

Reveja as seguintes informações contidas nas notas de lançamento para a atualização de Janeiro de 2015.

> [AZURE.IMPORTANT]  
>
>- Esta atualização não está disponível através do Windows Update e não pode ser instalada como outras atualizações. Dispositivo não vai receber esta atualização, mesmo se tiver aplicado as actualizações utilizando o portal clássico Azure. Esta atualização só serão aplicadas a dispositivos virtuais criados após 20 de Janeiro de 2015. 
> 
>- A versão de Janeiro de StorSimple não contém atualizações para o dispositivo físico StorSimple. Ainda pode aplicar atualizações disponíveis para Windows para o dispositivo virtual, incluindo correções de segurança recentes, mas não verá uma alteração na versão para o dispositivo físico StorSimple.

## <a name="whats-new-in-the-january-release"></a>Quais são as novidades na versão de Janeiro

Esta atualização contém uma correcção relacionadas com os volumes de ficar offline no dispositivo virtual. (Consulte o artigo [problemas corrigidos neste lançamento](#issues-fixed-in-the-january-release).)  

A atualização não contém novas funcionalidades ou funcionalidade.  

## <a name="issues-fixed-in-the-january-release"></a>Fixada na versão de Janeiro de problemas

A tabela seguinte descreve o problema que foi fixo nesta atualização.

|Não.|Funcionalidade|Problema|Aplica-se ao dispositivo físico|Aplica-se ao dispositivo virtual 
|---|-------|-----|--------------------------|-------------------------
|1|Volumes de ficar offline|Quando latências nuvem alta persistirem durante vários minutos, os volumes de dispositivo virtual StorSimple aceda offline nos anfitriões. Este fix aumenta o limiar de para latências na nuvem, portanto minimizar as situações que pode causar os volumes de ficar offline em anfitriões.|N|Sim  

## <a name="known-issues-in-the-january-release"></a>Problemas conhecidos na versão de Janeiro

A tabela seguinte fornece um resumo dos problemas conhecidos nesta versão.
 
|Não.|Funcionalidade|Problema|Solução/comentários|Aplica-se ao dispositivo físico|Aplica-se ao dispositivo virtual 
|---|-------|-----|-------------------|--------------------------|-------------------------
|1| Repor fábrica|  Em alguns casos, ao efetuar a reposição de fábrica do mesmo, o dispositivo StorSimple poderá estar bloqueado e mostrar esta mensagem: **Repor o fábrica está em curso (fase 8).** Isto acontece se premir CTRL + C enquanto o cmdlet está em curso.| Prima CTRL + C depois de iniciar a reposição de fábrica do mesmo. Se já estiver a trabalhar neste estado, contacte o Microsoft Support para os passos seguintes.|Sim|N|
|2|Quórum disco| Em casos raros, se a maioria dos discos na inclusão EBOD de um dispositivo 8600 é desligada que resulta em quórum sem disco, em seguida, o agrupamento de armazenamento será offline. Irá permanecer offline, mesmo se os discos são ligados.|Terá de reinicie o dispositivo. Se o problema persistir, contacte o Microsoft Support para os passos seguintes.|Sim |N
|3|Nuvem instantâneo falhas|Em casos raros, um instantâneo de nuvem pode falhar com o erro de **limite de cópia de segurança máximo instalações atingido**. Isto ocorre se exceder 255 clones onlinehttps no mesmo dispositivo, a partir do mesmo volume original que foi eliminado.||Sim|Sim
|4|ID de controlador incorretos|Quando a substituição do controlador é executada, controlador de 0 poderá aparecer como controlador de 1. Durante a substituição do controlador, quando a imagem é carregada a partir do nó de ponto a ponto, o ID do controlador pode ser apresentada inicialmente como ID. o controlador de ponto a ponto  Em casos raros, este comportamento também pode ser visto após reiniciar o sistema.|Não é necessária nenhuma ação do utilizador. Esta situação resolverá propriamente dito após a substituição de controlador está concluída.|Sim|N 
|5| Monitorizar os gráficos de dispositivo|No serviço StorSimple Gestor, os gráficos de monitorização do dispositivo não funciona quando estiver básicas ou a autenticação NTLM está ativada na configuração do servidor proxy para o dispositivo.|Modificar a configuração do proxy web para o dispositivo registado com o seu serviço de Gestor de StorSimple, por isso que a autenticação está definida para nenhum. Para fazer isto, execute o o Windows PowerShell para cmdlet StorSimple Set-HcsWebProxy.|Sim|Sim
|6| Contas de armazenamento|Utilizar o serviço de armazenamento para eliminar a conta de armazenamento é um cenário não suportado. Isto vai originar uma situação na qual não não possível obter dados de utilizador.|| Sim |  Sim
|7|Dispositivo activação pós-falha| Várias activações pós-falha de um contentor de volume a partir do mesmo dispositivo de origem para dispositivos diferentes alvo não é suportada.| Activação em pós-falha a partir de um único dispositivo inactivo para vários dispositivos irá tornar os contentores de volume na primeira Ocorreu uma falha ao longo do dispositivo perder a propriedade dos dados. Após essa uma falha na ligação, nestes contentores de volume serão apresentada ou comportam-se de forma diferente quando visualizá-los no portal do Azure clássico.|Sim|N
|8| Instalação|Durante a StorSimple placa para a instalação do SharePoint, tem de fornecer um IP de dispositivo para que a instalação concluir com êxito.||Sim|N
|9| Proxy da Web|Se a sua configuração do proxy web tiver HTTPS como o protocolo especificado, em seguida, a serviço de dispositivos de comunicação, será afectada e o dispositivo irão offline. Pacotes de suporte também serão geradas no processo de consumir recursos significativos no seu dispositivo.|Certifique-se de que o URL do web proxy HTTP, como o protocolo especificado. Consulte o artigo obter mais informações sobre como [configurar o proxy web para o seu dispositivo](storsimple-configure-web-proxy.md).|Sim |N
|10|Proxy da Web|  Se configurar e activar o proxy web num dispositivo registado, terá de reiniciar o controlador de ativo no seu dispositivo.|| Sim |N
|11|Latência alta na nuvem e elevada carga de trabalho e/s|Quando o seu dispositivo StorSimple encontra uma combinação de latências nuvem muito alta (ordem de segundos) e a carga de trabalho e/s alta, os volumes de dispositivo vão para o estado degradado e o/s pode falhar com um erro "dispositivo não está pronto".|É necessário reiniciar o computador os controladores do dispositivo ou executar um dispositivo activação pós-falha para recuperar desta situação manualmente.|Sim|N

## <a name="physical-device-updates-in-the-january-release"></a>Atualizações de dispositivo físico nas Janeiro solte

Esta atualização não contém quaisquer outras alterações ao dispositivo StorSimple.

## <a name="serial-attached-scsi-sas-controller-and-firmware-updates-in-the-january-release"></a>Controlador de SCSI (SA) anexado série e atualizações de firmware nas Janeiro solte

Nesta versão não contém o controlador de SCSI (SA) anexado série ou o firmware quaisquer atualizações. A actualização do controlador estava a Outubro de 2014 lançamento. 

## <a name="virtual-device-updates-in-the-january-release"></a>Atualizações de dispositivo virtual nas Janeiro solte

Nesta versão contém uma imagem atualizada para o dispositivo virtual. Todos os dispositivos virtuais criados após 20 de Janeiro de 2015 irão mostrar a versão do software como 6.3.9600.17361.

 
