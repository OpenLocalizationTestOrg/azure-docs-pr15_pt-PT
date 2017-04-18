<properties 
    pageTitle="Notas sobre a versão 0.1 StorSimple 8000 atualizar | Microsoft Azure"
    description="Descreve as novas funcionalidades e correções, problemas em aberto e soluções disponíveis para Outubro de 2014 lançamento do Microsoft Azure StorSimple (atualização de 0,1)."
    services="storsimple"
    documentationCenter="NA"
    authors="alkohli"
    manager="carmonm"
    editor="" />
 <tags 
    ms.service="storsimple"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="NA"
    ms.workload="TBD"
    ms.date="09/21/2016"
    ms.author="alkohli" />

# <a name="storsimple-8000-series-update-01-release-notes--october-2014"></a>Notas sobre a versão 0.1 StorSimple 8000 série atualização – de Outubro de 2014  

## <a name="overview"></a>Descrição geral

As seguintes notas de lançamento identificam os problemas críticos abrir para StorSimple 8000 série Actualização 0,1 disponibilizada em Outubro de 2014. Também contêm uma lista das atualizações de software e firmware de StorSimple incluídas nesta versão. Este é o lançamento inicial após a StorSimple 8000 série versão é efetuada ficará disponível de Julho de 2014 e corresponde à versão do software 6.3.9600.17312.  

Recomendamos que pesquisar para e aplicar atualizações disponíveis para imediatamente depois de instalar o dispositivo. Também pode ativar as atualizações automáticas para transferir e instalar atualizações de alta prioridade da Microsoft, assim que são lançadas. Para obter mais informações, consulte o artigo como [atualizar o seu dispositivo StorSimple](storsimple-update-device.md).  

Reveja as informações contidas nas notas de lançamento antes de implementar as atualizações na sua solução StorSimple.  

>[AZURE.IMPORTANT]
> 
-   Utilize o serviço do Gestor de StorSimple e não do Windows PowerShell para StorSimple para instalar as atualizações de Outubro.  
-   As atualizações normalmente demorar cerca de 3 horas para concluir.  
-   A versão de Outubro de StorSimple não contém atualizações para o dispositivo virtual StorSimple. Ainda pode aplicar atualizações disponíveis para Windows, incluindo correções de segurança recentes, mas não verá uma alteração na versão para o dispositivo virtual.  

Certifique-se dos que pré-requisitos seguintes forem cumpridos antes de atualizar o seu dispositivo StorSimple.  

- Certifique-se de que ambos os controladores do dispositivo estiver a executar o antes de verificar existência de atualizações. Se um dos controlador não está em execução, a digitalização irá falhar. Para verificar se os controladores são num estado Saudável, navegue até ao **Estado de Hardware** na página de **Manutenção** . Se existirem componentes de que **precisa de atenção**, contacte o Microsoft Support antes de continuar.  
- Garantir que IPs fixo para ambas as controlador de 0 e 1 de controlador encaminháveis e podem ligar-se à Internet como estas são utilizadas para servir as atualizações para o dispositivo. Pode utilizar o [cmdlet Testar ligação](https://technet.microsoft.com/library/hh849808.aspx) para executar o ping um endereço conhecido fora da rede, tais como o outlook.com, para confirmar que o controlador de tem conectividade de rede externa.  
- Certifique-se de que as portas saídas necessárias estão disponíveis no seu dispositivo StorSimple para comunicação de saída. Para mais informações, consulte o artigo [requisitos para o seu dispositivo StorSimple de rede](storsimple-system-requirements.md#networking-requirements-for-your-storsimple-device).  
- Se a versão do software dispositivo for anterior ao 6.3.9600.17312 (actualização de Outubro de 2014), desative as portas dados 2 e 3 de dados, caso ativada, antes de iniciar a atualização. Se deixar a 2 de dados ou dados 3 portas activadas quando aplicar a atualização, poderá fazer com que o controlador de dispositivo iniciar o modo de recuperação. Tenha em atenção que, quando desativar as interfaces de rede, todos os volumes associados se-á offline e a e/s vai ser interrompidas para a duração da atualização.  

## <a name="whats-new-in-the-october-release"></a>Quais são as novidades na versão de Outubro

Esta atualização inclui as seguintes melhorias:

- Agora pode utilizar o serviço do Gestor de StorSimple da IU para gerir os controladores do dispositivo. A gestão de acções incluem reiniciar, encerramento, ou ativar um controlador. Para obter mais informações, aceda a [Gerir StorSimple controladores de dispositivo](storsimple-manage-device-controller.md).  
- Pode agendar a atribuição de largura de banda WAN de acordo com as combinações de dia-da-semana e hora do dia. Esta opção permite-lhe utilizar de largura de banda WAN horas de expediente. Modelos de largura de banda diferentes são permitidos de membros em contentores de volume diferente. Para obter mais informações, aceda a [Gerir os seus modelos de largura de banda StorSimple](storsimple-manage-bandwidth-templates.md).  
- Pode configurar notificações de correio eletrónico para importante notificar o administrador (es) e outros problemas existentes ou possivelmente futuros. Para obter mais informações, aceda a [Configurar definições de alerta](storsimple-manage-alerts.md#configure-alert-settings).  

## <a name="issues-fixed-in-the-october-release"></a>Fixada na versão de Outubro de problemas


A tabela seguinte fornece um resumo dos problemas que foram fixo nesta atualização.  

| Não. | Funcionalidade | Problema | Aplica-se ao dispositivo físico | Aplica-se ao dispositivo virtual |
|-----|---------|-------|---------------------------------|--------------------------------|
| 1 | Interfaces de rede | A versão anterior, as interfaces de rede dados 2 e 3 de dados foram trocadas no software. Este erro foi corrigido nesta atualização. Liberte as definições e desativar estas interfaces de rede antes de instalar a atualização. Depois de instalar a atualização, terá de reconfigurar estas interfaces. | Sim | N |
| 2 | Pacote de suporte | Na versão anterior, se executou o cmdlet do Windows PowerShell **HcsSupportPackage de exportação** para obter os registos de controlador de gestão de em (BMC), a operação falhou com o seguinte aviso: "a operação foi concluída com êxito neste controlador, mas ocorreu uma falha do controlador de peer devido aos seguintes erros. Verifique se o peer está em bom estado e se o nó actual pode ligar-se para o peer." Agora fica fixo este problema. | Sim | N |
| 3 | Dispositivo activação pós-falha | Na versão anterior, havido a oportunidade de inconsistência dados se uma tarefa de **cópia de segurança de descobrir** falhou durante um dispositivo activação pós-falha. Agora fica fixo este problema. | Sim | N |
| 4 | Dispositivo activação pós-falha | Na versão anterior, após uma falha na ligação dispositivo, cópias de segurança estavam visíveis mas o contentor de volume associado não era presente no dispositivo de destino. Agora fica fixo este problema. | Sim | N |
| 5 | Dispositivo activação pós-falha | A versão anterior, Ocorreu um erro na enumeração de cópias de segurança na nuvem durante a operação de restauro de registo que poderia potencialmente conduzir à inconsistência dos dados se havia problemas de conectividade de nuvem. | Sim | N |
| 6 | Actualização de firmware | Na versão anterior, a tarefa de atualização do dispositivo firmware falhou e um erro que indicar que os cmdlets não eram reconhecíveis e que a atualização falhou como resultado. O controlador de correu, em seguida, no modo de recuperação. Agora fica fixo este problema. | Sim | N |
| 7 | Instalação | Agora foram corrigidos erros causados pelo dispositivo não a ser criada uma imagem corretamente durante a instalação. | Sim | N |
| 8 | Repor fábrica | Opcionalmente, agora pode ignorar a verificação de firmware de reposição de fábrica do mesmo. Esta é uma alteração a partir da versão anterior. | Sim | N |
| 9 | Repor fábrica | A versão anterior, quando tiver sido executado um cmdlet de reposição de fábrica do mesmo, firmware versão verificações foram efetuadas apenas para alguns componentes de hardware. Verificações de firmware adicionais foram efetuadas após o primeiro reinício durante o processo, que pode causar a reposição falhar. Este fix assegura que todas as verificações firmware são efetuadas quando a fábrica de repor o cmdlet é executado e reinicie o antes do primeiro sistema. | Sim | N |
| 10 | Rotação de chave de conta de armazenamento | O cmdlet **Invocar HcsmServiceDataEncryptionKeyChange** utilizado para rodar as teclas de conta de armazenamento agora pede ao utilizador para introduzir a chave de encriptação de dados do serviço. Esta é uma alteração a partir da versão anterior na qual a chave de encriptação de dados do serviço foi passada como um parâmetro inline. | Sim | N |
| 11 | Reposição de recurso dentro de 24 horas | Durante a recuperação de falhas, a limpeza do dispositivo de origem não ocorrer simplesmente, que causem reposição de recurso a falha. Este erro foi corrigido nesta versão. | Sim | N |

## <a name="known-issues-in-the-october-release"></a>Problemas conhecidos na versão de Outubro

A tabela seguinte fornece um resumo dos problemas conhecidos nesta versão.

| Não. | Funcionalidade | Problema | Solução/comentários | Aplica-se ao dispositivo físico | Aplica-se ao dispositivo virtual |
|-----|---------|-------|----------------------------|----------------------------|---------------------------|
| 1 | Repor fábrica | Em alguns casos, ao efetuar a reposição de fábrica do mesmo, o dispositivo StorSimple poderá estar bloqueado e mostrar esta mensagem: **Repor o fábrica está em curso (fase 8)**. Isto acontece se premir CTRL + C enquanto o cmdlet está em curso. | Prima CTRL + C depois de iniciar a reposição de fábrica do mesmo. Se já estiver a trabalhar neste estado, contacte o Microsoft Support para os passos seguintes. | Sim | N |
| 2 | Repor fábrica | Efetue não fábrica repor um dispositivo de StorSimple é atualizado a partir das versões DG para Outubro de 2014 liberte. | Esta operação só irá funcionar se um patches estiver instalado. Contacte o Microsoft Support para obter esta correção necessária. | Sim | N | 
| 3 | Quórum disco | Em casos raros, se a maioria dos discos na inclusão EBOD de um dispositivo 8600 é desligada que resulta em quórum sem disco, em seguida, o agrupamento de armazenamento será offline. Irá permanecer offline, mesmo se os discos são ligados. | Terá de reinicie o dispositivo. Se o problema persistir, contacte o Microsoft Support para os passos seguintes. | Sim | N |
| 4 | Nuvem instantâneo falhas | Em casos raros, um instantâneo de nuvem pode falhar com o erro de **limite de cópia de segurança máximo instalações atingido**. Isto ocorre se exceder 255 clones onlinehttps no mesmo dispositivo, a partir do mesmo volume original que foi eliminado. | | Sim | Sim |
| 5 | ID de controlador incorretos | Quando a substituição do controlador é executada, controlador de 0 poderá aparecer como controlador de 1. Durante a substituição do controlador, quando a imagem é carregada a partir do nó de ponto a ponto, o ID do controlador pode ser apresentada inicialmente como ID. o controlador de ponto a ponto Em casos raros, este comportamento também pode ser visto após reiniciar o sistema. |Não é necessária nenhuma ação do utilizador. Esta situação resolverá propriamente dito após a substituição de controlador está concluída. | Sim | N |
| 6 | Monitorizar os gráficos de dispositivo | No serviço StorSimple Gestor, os gráficos de monitorização do dispositivo não funciona quando estiver básicas ou a autenticação NTLM está ativada na configuração do servidor proxy para o dispositivo. | Modificar a configuração do proxy web para o dispositivo registado com o seu serviço de Gestor de StorSimple, por isso que a autenticação está definida para nenhum. Para fazer isto, execute o o Windows PowerShell para cmdlet StorSimple Set-HcsWebProxy. | Sim | Sim |
| 7 | Contas de armazenamento | Utilizar o serviço de armazenamento para eliminar a conta de armazenamento é um cenário não suportado. Isto vai originar uma situação na qual não não possível obter dados de utilizador. | | Sim | Sim |
| 8 | Dispositivo activação pós-falha | Várias activações pós-falha de um contentor de volume a partir do mesmo dispositivo de origem para dispositivos diferentes alvo não é suportada. | Activação em pós-falha a partir de um único dispositivo inactivo para vários dispositivos irá tornar os contentores de volume na primeira Ocorreu uma falha ao longo do dispositivo perder a propriedade dos dados. Após essa uma falha na ligação, estes contentores de volume irão aparecer ou comportam-se de forma diferente quando vê-los no portal do Azure clássico. | Sim | N |
| 9 | Instalação | Durante a StorSimple placa para a instalação do SharePoint, tem de fornecer um IP de dispositivo para que a instalação concluir com êxito.    | | Sim | N |
| 10 | Proxy da Web | Se a sua configuração do proxy web tiver HTTPS como o protocolo especificado, em seguida, a serviço de dispositivos de comunicação, será afectada e o dispositivo irão offline. Pacotes de suporte também serão geradas no processo de consumir recursos significativos no seu dispositivo. | Certifique-se de que o URL do web proxy HTTP, como o protocolo especificado. Mais informações sobre como [configurar o proxy web para o seu dispositivo](storsimple-configure-web-proxy.md). | Sim | N |
| 11 | Proxy da Web | Se configurar e activar o proxy web num dispositivo registado, terá de reiniciar o controlador de ativo no seu dispositivo. | | Sim | N |
| 12 | Latência alta na nuvem e elevada carga de trabalho e/s | Quando o seu dispositivo StorSimple encontra uma combinação de latências nuvem muito alta (ordem de segundos) e a carga de trabalho e/s alta, os volumes de dispositivo vão para o estado degradado e o/s pode falhar com um erro "dispositivo não está pronto". | É necessário reiniciar o computador os controladores do dispositivo ou executar um dispositivo activação pós-falha para recuperar desta situação manualmente. | Sim | N |

## <a name="physical-device-updates-in-the-october-release"></a>Atualizações de dispositivo físico na Outubro solte

Quando estão aplicadas estas atualizações para um dispositivo físico, a versão do software será alterado para 6.3.9600.17312. Salvo caso contrário, estas notas de lançamento aplicar a todos os modelos do dispositivo StorSimple. Para mais informações sobre estas atualizações, consulte o artigo [software de aparelho físico de Outubro de 2014 atualização para o Microsoft Azure StorSimple aparelho](http://support.microsoft.com/kb/2986997).  

## <a name="serial-attached-scsi-sas-controller-and-firmware-updates-in-the-october-release"></a>Controlador de SCSI (SA) anexado série e atualizações de firmware na Outubro solte

Nesta versão atualiza o controlador e o firmware no controlador de SA do seu dispositivo físico. Para obter mais informações sobre o controlador de SA atualizar, consulte o artigo [Atualizar para LSI SA controladores no Microsoft Azure StorSimple aparelho de Outubro de 2014](http://support.microsoft.com/kb/2987020).   

Nesta versão também se aplica uma atualização cumulativa firmware que resolva os problemas de fiabilidade com os componentes de hardware do dispositivo. Para mais informações sobre a atualização de firmware, consulte o artigo [firmware de Outubro de 2014 atualização para o Microsoft Azure StorSimple aparelho](http://support.microsoft.com/kb/2987015).  

## <a name="virtual-device-updates-in-the-october-release"></a>Atualizações de dispositivo virtual na Outubro solte

Nesta versão não contém todas as atualizações para o dispositivo virtual. Aplicar esta atualização não será alterada a versão do software de um dispositivo virtual.
 
