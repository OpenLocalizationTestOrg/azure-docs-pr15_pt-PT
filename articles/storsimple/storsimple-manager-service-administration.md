<properties 
   pageTitle="Administração de serviços de Gestor de StorSimple | Microsoft Azure"
   description="Saiba como gerir o seu dispositivo de StorSimple utilizando o serviço do Gestor de StorSimple no portal do Azure clássico."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/21/2016"
   ms.author="alkohli" />

# <a name="use-the-storsimple-manager-service-to-administer-your-storsimple-device"></a>Utilizar o serviço do Gestor de StorSimple para administrar o seu dispositivo StorSimple

## <a name="overview"></a>Descrição geral

Este artigo descreve a interface de serviço do Gestor de StorSimple, incluindo como ligar aos, as várias opções disponíveis e ligações saída para específicos dos fluxos de trabalho que podem ser executadas por esta UI. Estas orientações são aplicável ao ambos; física StorSimple e o dispositivo virtual.

Depois de ler este artigo, irá aprender a:

- Ligar ao serviço do Gestor de StorSimple
- Navegar IU do Gestor de StorSimple
- Administrar o seu dispositivo StorSimple através do serviço de Gestor de StorSimple


## <a name="connect-to-storsimple-manager-service"></a>Ligar ao serviço do Gestor de StorSimple

O serviço do Gestor de StorSimple é executado no Microsoft Azure e liga à múltiplos StorSimple dispositivos. Utilize um portal de clássico Microsoft Azure central em execução num browser para gerir nestes dispositivos. Para ligar para o serviço do Gestor de StorSimple, faça o seguinte.

#### <a name="to-connect-to-the-service"></a>Para ligar ao serviço

1. Navegue até ao [https://manage.windowsazure.com/](https://manage.windowsazure.com/).

1. Utilizar as credenciais da conta Microsoft, inicie sessão no portal clássico do Microsoft Azure (localizado no canto superior direito do painel).

1. Desloque-se para baixo no painel de navegação à esquerda para aceder ao serviço do Gestor de StorSimple.


## <a name="navigate-storsimple-manager-service-ui"></a>Navegue até o serviço do Gestor de StorSimple IU

A hierarquia de navegação para o serviço do Gestor de StorSimple IU é apresentada na seguinte tabela.

- Página de destino do **Gestor de StorSimple** leva-o para as páginas de nível de serviço de IU aplicáveis a todos os dispositivos dentro de um serviço.

- Página de **dispositivos** leva-o para as páginas da IU do nível de dispositivo aplicáveis a um dispositivo específico.

- Página de **Volume contentores** leva-o para a página de volume que mostra todos os volumes associados com um dispositivo.


#### <a name="storsimple-manager-service-navigational-hierarchy"></a>Hierarquia de navegação de serviço do Gestor de StorSimple

|Página de destino|Páginas de nível de serviço|Páginas de nível do dispositivo|Páginas de nível do dispositivo|
|---|---|---|---|
|Serviço do Gestor de StorSimple|Dashboard de serviço|Dashboard de dispositivo||
||Dispositivos →|Monitor|
||Catálogo de cópia de segurança|Containers→ de volume|Volumes|
||Configurar (serviço)|Políticas de cópia de segurança||
||Tarefas|Configurar (dispositivo)|
||Alertas|Manutenção|

![Vídeo disponível](./media/storsimple-manager-service-administration/Video_icon.png) **vídeo disponível**

Para ver um vídeo que orienta-o através da interface de utilizador do serviço de Gestor de StorSimple, clique em [aqui](https://azure.microsoft.com/documentation/videos/storsimple-manager-service-overview/).

## <a name="administer-storsimple-device-using-storsimple-manager-service"></a>Administrar o dispositivo de StorSimple utilizando o serviço do Gestor de StorSimple

A tabela seguinte mostra um resumo de todas as tarefas de gestão comuns e fluxos de trabalho complexos que podem ser executados dentro do serviço de Gestor de StorSimple IU. Estas tarefas estão organizadas com base nas páginas de IU na qual são iniciadas.

Para mais informações sobre cada fluxo de trabalho, clique no procedimento apropriado na tabela.

#### <a name="storsimple-manager-workflows"></a>Gestor de StorSimple fluxos de trabalho

|Se pretender fazer isto...|Aceda a esta página IU...|Utilize este procedimento.|
|---|---|---|
|Criar um serviço</br>Eliminar um serviço</br>Obter a chave de registo de serviço</br>Gerar chave de registo do serviço|Serviço do Gestor de StorSimple|[Implementar um serviço de Gestor de StorSimple](storsimple-manage-service.md)
|Alterar a chave de encriptação de dados de serviço</br>Ver os registos de operação|Dashboard de StorSimple → de serviço do Gestor|[Utilize o dashboard de serviço do Gestor de StorSimple](storsimple-service-dashboard.md)|
|Desativar um dispositivo</br>Eliminar um dispositivo|Gestor de StorSimple serviço → dispositivos|[Desativar ou eliminar um dispositivo](storsimple-deactivate-and-delete-device.md)|
|Saiba mais sobre falhas recuperação e dispositivo activação pós-falha</br>Activação pós-falha para um dispositivo físico</br>Activação pós-falha para um dispositivo virtual</br>Recuperação de falhas de continuidade do negócio (BCDR)|Gestor de StorSimple serviço → dispositivos|[Recuperação activação e falhas para o seu dispositivo StorSimple](storsimple-device-failover-disaster-recovery.md)|
|Lista cópias de segurança para um volume</br>Selecione um conjunto de cópia de segurança</br>Eliminar um conjunto de cópia de segurança|Catálogo de cópia de segurança StorSimple → de serviço do Gestor|[Gerir cópias de segurança](storsimple-manage-backup-catalog.md)|
|Clonar um volume|Catálogo de cópia de segurança StorSimple → de serviço do Gestor|[Clonar um volume](storsimple-clone-volume.md)|
|Restaurar um conjunto de cópia de segurança|Catálogo de cópia de segurança StorSimple → de serviço do Gestor|[Restaurar um conjunto de cópia de segurança](storsimple-restore-from-backup-set.md)|
|Sobre contas de armazenamento</br>Adicionar uma conta de armazenamento</br>Editar uma conta de armazenamento</br>Eliminar uma conta de armazenamento</br>Rotação da chave de contas de armazenamento|Configurar o Gestor de StorSimple serviço →|[Gerir contas de armazenamento](storsimple-manage-storage-accounts.md)|
|Acerca dos modelos de largura de banda</br>Adicionar um modelo de largura de banda</br>Editar um modelo de largura de banda</br>Eliminar um modelo de largura de banda</br>Utilizar um modelo de largura de banda predefinido</br>Criar um modelo de largura de banda para todo o dia que inicia uma vez especificado|Configurar o Gestor de StorSimple serviço →|[Gerir modelos de largura de banda](storsimple-manage-bandwidth-templates.md)|
|Acerca de registos de controlo de acesso</br>Criar um registo de controlo de acesso</br>Editar um registo de controlo de acesso</br>Eliminar um registo de controlo de acesso|Configurar o Gestor de StorSimple serviço →|[Gerir os registos de controlo de acesso](storsimple-manage-acrs.md)|
|Ver detalhes do projecto</br>Cancelar uma tarefa|Tarefas de serviço → do Gestor de StorSimple|[Gerir tarefas](storsimple-manage-jobs.md)
|Receber notificações de alerta</br>Gerir alertas</br>Reveja os alertas|Gestor de StorSimple serviço → Alertas|[Ver e gerir StorSimple alertas](storsimple-manage-alerts.md)
|Ver iniciadores ligadas</br>Encontrar o número de série do dispositivo</br>Encontrar o destino IQN|Gestor de StorSimple serviço → dispositivos → Dashboard|[Utilize o dashboard de dispositivo StorSimple](storsimple-device-dashboard.md)|
|Criar gráficos de monitorização|Gestor de StorSimple serviço → dispositivos → monitorizar|[Monitorizar o seu dispositivo StorSimple](storsimple-monitor-device.md)|
|Adicionar um contentor de volume</br>Modificar um contentor de volume</br>Eliminar um contentor de volume|StorSimple Gestor serviço → dispositivos → Volume contentores|[Gerir contentores de volume](storsimple-manage-volume-containers.md)|
|Adicionar um volume</br>Modificar um volume</br>Colocar um volume offline</br>Eliminar um volume</br>Monitorizar um volume|Volumes de StorSimple Gestor serviço → dispositivos → contentores de Volume →|[Gerir volumes](storsimple-manage-volumes.md)|
|Modificar as definições de dispositivo</br>Modificar as definições de tempo</br>Modificar as definições de DNS.md</br>Configurar as interfaces de rede|Gestor de StorSimple serviço → dispositivos → configurar|[Modificar a configuração do dispositivo para o seu dispositivo StorSimple](storsimple-modify-device-config.md)|
|Definições de proxy de web de vista|Gestor de StorSimple serviço → dispositivos → configurar|[Configurar o web proxy para o seu dispositivo](storsimple-configure-web-proxy.md)|
|Modificar a palavra-passe de administrador do dispositivo</br>Modificar a palavra-passe StorSimple instantâneo Manager|Gestor de StorSimple serviço → dispositivos → configurar|[Alterar palavras-passe de StorSimple](storsimple-change-passwords.md)|
|Configurar a gestão remota|Gestor de StorSimple serviço → dispositivos → configurar|[Ligar remotamente ao seu dispositivo StorSimple](storsimple-remote-connect.md)|
|Configurar definições de alerta|Gestor de StorSimple serviço → dispositivos → configurar|[Ver e gerir StorSimple alertas](storsimple-manage-alerts.md)|
|Configurar CHAP para o seu dispositivo StorSimple|Gestor de StorSimple serviço → dispositivos → configurar|[Configurar CHAP para o seu dispositivo StorSimple](storsimple-configure-chap.md)|
|Adicionar uma política de cópia de segurança</br>Adicionar ou modificar uma agenda</br>Eliminar uma política de cópia de segurança</br>Tirar uma cópia de segurança manual</br>Criar uma política de cópia de segurança personalizada com vários volumes e agendas|Políticas de cópia de segurança do Gestor de StorSimple serviço → dispositivos →|[Gerir políticas de cópia de segurança](storsimple-manage-backup-policies.md)|
|Parar de controladores do dispositivo</br>Reinicie controladores do dispositivo</br>Encerrar controladores do dispositivo</br>Repor o seu dispositivo para as predefinições de fábrica</br>(Acima destinam-se apenas a dispositivo no local)|Gestor de StorSimple serviço → dispositivos → manutenção|[Gerir StorSimple controlador de dispositivo](storsimple-manage-device-controller.md)|
|Saiba mais sobre StorSimple componentes de hardware</br>Estado de hardware do monitor</br>(Acima destinam-se apenas a dispositivo no local)|Gestor de StorSimple serviço → dispositivos → manutenção|[Componentes de hardware do monitor](storsimple-monitor-hardware-status.md)|
|Criar um pacote de suporte|Gestor de StorSimple serviço → dispositivos → manutenção|[Criar e gerir um pacote de suporte](storsimple-create-manage-support-package.md)|
|Instalar atualizações de software|Gestor de StorSimple serviço → dispositivos → manutenção|[Atualizar o seu dispositivo](storsimple-update-device.md)|


##<a name="next-steps"></a>Próximos passos
Se tiver quaisquer problemas com a operação no dia a dia do seu dispositivo StorSimple ou com qualquer um dos seus componentes de hardware, consulte:

- [Resolver problemas de um dispositivo operacionais avançado](storsimple-troubleshoot-operational-device.md)
- [Utilizar StorSimple LEDs indicador de monitorização](storsimple-monitoring-indicators.md)

Se não conseguir resolver os problemas e tem de criar um pedido de serviço, consulte [Contactar o suporte do Microsoft](storsimple-contact-microsoft-support.md).
