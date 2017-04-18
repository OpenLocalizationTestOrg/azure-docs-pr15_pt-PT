<properties 
   pageTitle="Notas de lançamento 0,3 StorSimple 8000 atualizar | Microsoft Azure"
   description="Descreve as novas funcionalidades e correções, problemas em aberto e soluções disponíveis para o de Fevereiro de 2015 lançamento do Microsoft Azure StorSimple (atualização de 0,3)."
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
   ms.date="04/18/2016"
   ms.author="v-sharos" />

# <a name="storsimple-8000-series-update-03-release-notes---february-2015"></a>Notas de lançamento 0,3 de actualização de série de 8000 StorSimple - de Fevereiro de 2015

## <a name="overview"></a>Descrição geral

As seguintes notas de lançamento identificam os problemas críticos abrir para StorSimple 8000 série Actualização 0,3 disponibilizada em Fevereiro de 2015. Também contêm uma lista das atualizações de software e firmware de StorSimple incluídas nesta versão. Este é o terceiro lançamento depois da StorSimple 8000 série versão é efetuada ficará disponível de Julho de 2014.
  
Esta atualização não altere a versão do software de dispositivo da atualização de Janeiro. Este continua a ser versão 6.3.9600.17312. Pode confirmar que a atualização ter sido instalada, verificando a data da **Última atualização** . Se a data de 2/10/2015 ou posterior, em seguida, a atualização foi instalada com êxito.  

Recomendamos que pesquisar para e aplicar atualizações disponíveis para imediatamente depois de instalar o seu dispositivo StorSimple. Também pode ativar as atualizações automáticas para transferir e instalar atualizações de alta prioridade da Microsoft, assim que são lançadas. Para mais informações, consulte o artigo [atualizar o seu dispositivo StorSimple](storsimple-update-device.md).  

Reveja as informações contidas nas notas de lançamento antes de implementar a atualização na sua solução StorSimple.  

>[AZURE.IMPORTANT]   
>
> - Utilize o serviço do Gestor de StorSimple e não do Windows PowerShell para StorSimple para instalar a atualização de Fevereiro.   
> - Demora cerca uma hora instala esta atualização. No entanto, se estiver a instalar atualizações acumulativas, o processo poderá demorar cerca de 3 horas para concluir.  
> - A versão de Fevereiro de StorSimple não contém atualizações para o dispositivo virtual StorSimple. Ainda pode aplicar atualizações disponíveis para Windows para o dispositivo virtual, incluindo correções de segurança recentes, mas não verá uma alteração na versão para o dispositivo virtual.  

Certifique-se de que os pré-requisitos seguintes forem cumpridos antes de atualizar o seu dispositivo StorSimple.  

- Certifique-se de que ambos os controladores do dispositivo estiver a executar o antes de verificar existência de atualizações. Se um dos controlador não está em execução, a digitalização irá falhar. Para verificar se os controladores são num estado Saudável, navegue até ao **Estado de Hardware** na página de **Manutenção** . Se existirem componentes de que **precisa de atenção**, contacte o Microsoft Support antes de continuar.
- Garantir que IPs fixo para controlador de 0 e controlador de 1 encaminháveis e pode ligar-se à Internet como estas são utilizadas para servir as atualizações para o dispositivo. Pode utilizar o [cmdlet Testar ligação](https://technet.microsoft.com/library/hh849808.aspx) para executar o ping um endereço conhecido fora da rede, tais como o outlook.com, para confirmar que o controlador de tem conectividade de rede externa.
- Certifique-se de que as portas 80 e 443 estão disponíveis no seu dispositivo StorSimple para comunicação de saída. Para mais informações, consulte o artigo [requisitos para o seu dispositivo StorSimple de rede](storsimple-system-requirements.md#networking-requirements-for-your-storsimple-device).
- Se a versão do software dispositivo for anterior ao 6.3.9600.17312 (actualização de Outubro de 2014), desative as portas dados 2 e 3 de dados, caso ativada, antes de iniciar a atualização. Sair de dados 2 ou 3 dados portas ativadas ao aplicar a atualização pode causar o controlador de dispositivo iniciar o modo de recuperação. Tenha em atenção que, quando desativar as interfaces de rede, todos os volumes associados se-á offline e a/s vai ser interrompidas para a duração da atualização.  
  
## <a name="whats-new-in-the-february-release"></a>Quais são as novidades na versão de Fevereiro

Esta atualização contém um fix para a fábrica de repor o problema que ocorreram no dispositivos que tivessem sido atualizados a partir da das versões DG solte para a versão de Outubro de 2014. Para mais informações, consulte o artigo [problemas corrigidos nesta versão](#issues-fixed-in-the-february-release).   

Esta atualização não contém novas funcionalidades ou funcionalidade.  

## <a name="issues-fixed-in-the-february-release"></a>Problemas corrigidos na versão de Fevereiro

A tabela seguinte descreve o problema que foi fixo nesta atualização.  
 
| Não. | Funcionalidade | Problema | Aplica-se ao dispositivo físico | Aplica-se ao dispositivo virtual |
|-----|---------|-------|---------------------------------|-------------------------------|
| 1 | Repor fábrica | Tentar executar uma fábrica repor num dispositivo que originalmente tinha a versão das versões DG (versão 6.3.9600.17215) instalada, mas foi atualizado para a Outubro de lançamento (versão 6.3.9600.17312). A fábrica de repor falha e o dispositivo torna-se instável. | Sim | N |


## <a name="known-issues-in-the-february-release"></a>Problemas conhecidos na versão de Fevereiro

A tabela seguinte fornece um resumo dos problemas conhecidos nesta versão.
 
| Não. | Funcionalidade | Problema | Solução/comentários | Aplica-se ao dispositivo físico  | Aplica-se ao dispositivo virtual |
|-----|---------|-------|----------------------------|-----------------------------|--------------------------|
| 1 | Repor fábrica | Em alguns casos, ao efetuar a reposição de fábrica do mesmo, o dispositivo StorSimple poderá estar bloqueado e mostrar esta mensagem: **Repor o fábrica está em curso (fase 8)**. Isto acontece se premir CTRL + C enquanto o cmdlet está em curso. | Prima CTRL + C depois de iniciar a reposição de fábrica do mesmo. Se já estiver a trabalhar neste estado, contacte o Microsoft Support para os passos seguintes. | Sim | N |
| 2 | Quórum disco | Em casos raros, se a maioria dos discos na inclusão EBOD de um 8600device é desligada que resulta em quórum sem disco, em seguida, o agrupamento de armazenamento será offline. Irá permanecer offline, mesmo se os discos são ligados. | Terá de reinicie o dispositivo. Se o problema persistir, contacte o Microsoft Support para os passos seguintes. | Sim | N |
| 3 | Nuvem instantâneo falhas | Em casos raros, um instantâneo de nuvem pode falhar com o erro de **limite de cópia de segurança máximo instalações atingido**. Isto ocorre se exceder 255 clones onlinehttps no mesmo dispositivo, a partir do mesmo volume original que foi eliminado. |  | Sim | Sim |
| 4 | ID de controlador incorretos | Quando a substituição do controlador é executada, controlador de 0 poderá aparecer como controlador de 1. Durante a substituição do controlador, quando a imagem é carregada a partir do nó de ponto a ponto, o ID do controlador pode ser apresentada inicialmente como ID. o controlador de ponto a ponto Em casos raros, este comportamento também pode ser visto após reiniciar o sistema. | Não é necessária nenhuma ação do utilizador. Esta situação resolverá propriamente dito após a substituição de controlador está concluída. | Sim | N |
| 5 | Monitorizar os gráficos de dispositivo | No serviço StorSimple Gestor, os gráficos de monitorização do dispositivo não funciona quando estiver básicas ou a autenticação NTLM está ativada na configuração do servidor proxy para o dispositivo. | Modificar a configuração do proxy web para o dispositivo registado com o seu serviço de Gestor de StorSimple, por isso que a autenticação está definida para nenhum. Para fazer isto, execute o o Windows PowerShell para cmdlet StorSimple Set-HcsWebProxy. | Sim | Sim |
| 6 | Contas de armazenamento | Utilizar o serviço de armazenamento para eliminar a conta de armazenamento é um cenário não suportado. Isto vai originar uma situação na qual não não possível obter dados de utilizador. |  | Sim | Sim |
| 7 | Dispositivo activação pós-falha | Várias activações pós-falha de um contentor de volume a partir do mesmo dispositivo de origem para dispositivos diferentes alvo não é suportada.  Activação em pós-falha a partir de um único dispositivo inactivo para vários dispositivos irá tornar os contentores de volume na primeira Ocorreu uma falha ao longo do dispositivo perder a propriedade dos dados. Após essa uma falha na ligação, nestes contentores de volume serão apresentada ou comportam-se de forma diferente quando visualizá-los no portal do Azure clássico. |   | Sim | N |
| 8 | Instalação | Durante a StorSimple placa para a instalação do SharePoint, tem de fornecer um IP de dispositivo para que a instalação concluir com êxito. |  | Sim | N |
| 9 | Proxy da Web | Se a sua configuração do proxy web tiver HTTPS como o protocolo especificado, em seguida, a serviço de dispositivos de comunicação, será afectada e o dispositivo irão offline. Pacotes de suporte também serão geradas no processo de consumir recursos significativos no seu dispositivo. | Certifique-se de que o URL do web proxy HTTP, como o protocolo especificado. Mais informações sobre como [configurar o proxy web para o seu dispositivo](storsimple-configure-web-proxy.md). | Sim | N |
| 10 | Proxy da Web | Se configurar e activar o proxy web num dispositivo registado, terá de reiniciar o controlador de ativo no seu dispositivo. |  | Sim | N |
| 11 | Latência alta na nuvem e elevada carga de trabalho e/s | Quando o seu dispositivo StorSimple encontra uma combinação de latências nuvem muito alta (ordem de segundos) e a carga de trabalho e/s alta, os volumes de dispositivo vão para o estado degradado e o/s pode falhar com um erro "dispositivo não está pronto". | É necessário reiniciar o computador os controladores do dispositivo ou executar um dispositivo activação pós-falha para recuperar desta situação manualmente. | Sim | N |

## <a name="physical-device-updates-in-the-february-release"></a>Atualizações de dispositivo físico no Fevereiro solte

Esta atualização resolve o problema de reposição de fábrica do mesmo que ocorreram no dispositivos que tivessem sido atualizados a partir da das versões DG solte para a versão de Outubro de 2014. Não contém quaisquer outras actualizações para o dispositivo StorSimple.  

## <a name="serial-attached-scsi-sas-controller-and-firmware-updates-in-the-february-release"></a>Controlador de SCSI (SA) anexado série e atualizações de firmware no Fevereiro solte

Nesta versão não contém o controlador de SCSI (SA) anexado série ou o firmware quaisquer atualizações. A actualização do controlador estava a Outubro de 2014 lançamento.  

## <a name="virtual-device-updates-in-the-february-release"></a>Atualizações de dispositivo virtual no Fevereiro solte

Nesta versão não contém todas as atualizações para o dispositivo virtual. Aplicar esta atualização não será alterada a versão do software de um dispositivo virtual.
 
