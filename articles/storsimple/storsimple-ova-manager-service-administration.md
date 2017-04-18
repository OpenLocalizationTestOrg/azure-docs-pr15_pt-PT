<properties 
   pageTitle="Administração de Gestor de StorSimple Virtual matriz | Microsoft Azure"
   description="Saiba como gerir a sua matriz Virtual do StorSimple no local utilizando o serviço do Gestor de StorSimple no portal do Azure clássico."
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
   ms.date="10/11/2016"
   ms.author="alkohli" />

# <a name="use-the-storsimple-manager-service-to-administer-your-storsimple-virtual-array"></a>Utilize o serviço do Gestor de StorSimple para administrar a sua matriz de Virtual StorSimple

![fluxo de processo de configuração](./media/storsimple-ova-manager-service-administration/manage4.png)

## <a name="overview"></a>Descrição geral

Este artigo descreve a interface de serviço do Gestor de StorSimple, incluindo como ligar-se-lo e as várias opções disponíveis e fornece ligações para os fluxos de trabalho específicos que podem ser executadas por esta UI. 

Depois de ler este artigo, saberá como:

- Ligar ao serviço de Gestor de StorSimple
- Navegar IU do Gestor de StorSimple
- Administrar a sua matriz Virtual StorSimple através do serviço de Gestor de StorSimple

> [AZURE.NOTE] Para ver as opções de gestão disponíveis para o dispositivo de série StorSimple 8000, aceda a [utilizar o serviço do Gestor de StorSimple para administrar o seu dispositivo StorSimple](storsimple-manager-service-administration.md).

## <a name="connect-to-the-storsimple-manager-service"></a>Ligar ao serviço de Gestor de StorSimple

O serviço do Gestor de StorSimple é executado no Microsoft Azure e liga à vários StorSimple matrizes de Virtual. Utilize um portal de clássico Microsoft Azure central em execução num browser para gerir nestes dispositivos. Para ligar para o serviço do Gestor de StorSimple, faça o seguinte.

#### <a name="to-connect-to-the-service"></a>Para ligar ao serviço

1. Aceda a [https://manage.windowsazure.com/](https://manage.windowsazure.com/).

2. Utilizar as credenciais da conta Microsoft, inicie sessão no portal clássico do Microsoft Azure (localizado no canto superior direito do painel).

3. Desloque-se para baixo no painel de navegação à esquerda para aceder ao serviço do Gestor de StorSimple.

    ![Desloque-se ao serviço](./media/storsimple-ova-manager-service-administration/admin-scroll.png)

## <a name="navigate-the-storsimple-manager-service-ui"></a>Navegue até o serviço do Gestor de StorSimple IU

A hierarquia de navegação para o serviço do Gestor de StorSimple IU é apresentada na seguinte tabela.

- A página de destino do **Gestor de StorSimple** leva-o para as páginas de nível de serviço de IU aplicáveis a todas as matrizes Virtual dentro de um serviço.

- A página de **dispositivos** leva-o para as páginas de IU ao nível do dispositivo – aplicáveis para uma matriz Virtual específico.

#### <a name="storsimple-manager-service-navigational-hierarchy"></a>Hierarquia de navegação de serviço do Gestor de StorSimple

|Página de destino|Páginas de nível de serviço|Páginas de nível do dispositivo|
|---|---|---|
|Serviço do Gestor de StorSimple|Dashboard (serviço)|Dashboard (dispositivo)|
||Dispositivos →|Monitor|
||Catálogo de cópia de segurança|Partilhas (servidor de ficheiros) ou </br>Volumes (iSCSI server)|
||Configurar (serviço)|Configurar (dispositivo)|
||Tarefas|Manutenção|
||Alertas|

## <a name="use-the-storsimple-manager-service-to-perform-management-tasks"></a>Utilizar o serviço do Gestor de StorSimple para efetuar tarefas de gestão

A tabela seguinte mostra um resumo de todas as tarefas de gestão comuns e fluxos de trabalho complexos que podem ser executados dentro do serviço de Gestor de StorSimple IU. Estas tarefas estão organizadas com base nas páginas de IU na qual são iniciadas.

Para mais informações sobre cada fluxo de trabalho, clique no procedimento apropriado na tabela.

#### <a name="storsimple-manager-workflows"></a>Gestor de StorSimple fluxos de trabalho

|Se pretender fazer isto...|Aceda a esta página IU...|Utilize este procedimento|
|---|---|---|
|Criar um serviço</br>Eliminar um serviço</br>Obter a chave de registo do serviço</br>Gerar a chave de registo do serviço|Serviço do Gestor de StorSimple|[Implementar o serviço do Gestor de StorSimple](storsimple-ova-manage-service.md)|
|Alterar a chave de encriptação de dados de serviço</br>Ver os registos de operações|Dashboard de StorSimple → de serviço do Gestor|[Utilize o dashboard do serviço de StorSimple](storsimple-ova-service-dashboard.md)|
|Desativar uma matriz Virtual</br>Eliminar uma matriz Virtual|Gestor de StorSimple serviço → dispositivos|[Desativar ou eliminar uma matriz Virtual](storsimple-ova-deactivate-and-delete-device.md)|
|Falhas recuperação e dispositivo activação pós-falha</br>Pré-requisitos de activação pós-falha</br>Activação pós-falha para um dispositivo virtual</br>Recuperação de falhas de continuidade do negócio (BCDR)</br>Erros durante a recuperação de falhas|Gestor de StorSimple serviço → dispositivos|[Falhas recuperação e dispositivo activação pós-falha para a sua matriz de Virtual StorSimple](storsimple-ova-failover-dr.md)|
|Criar cópias partilhas e volumes de segurança</br>Tirar uma cópia de segurança manual</br>Alterar a agenda da cópia de segurança</br>Ver cópias de segurança existentes|Catálogo de cópia de segurança do Gestor de StorSimple serviço →|[Criar uma cópia de segurança a matriz de Virtual StorSimple](storsimple-ova-backup.md)|
|Restaurar partilhas a partir de um conjunto de cópia de segurança</br>Restaurar volumes de um conjunto de cópia de segurança</br>Recuperação de ao nível do item (apenas servidor do ficheiro)|Catálogo de cópia de segurança StorSimple → de serviço do Gestor|[Restaurar a partir de uma cópia de segurança da sua matriz de Virtual StorSimple](storsimple-ova-restore.md)|
|Sobre contas de armazenamento</br>Adicionar uma conta de armazenamento</br>Editar uma conta de armazenamento</br>Eliminar uma conta de armazenamento|Configurar o Gestor de StorSimple serviço →|[Gerir contas de armazenamento para a matriz Virtual StorSimple](storsimple-ova-manage-storage-accounts.md)|
|Acerca de registos de controlo de acesso</br>Adicionar ou modificar um registo de controlo de acesso </br>Eliminar um registo de controlo de acesso|Configurar o Gestor de StorSimple serviço →|[Gerir os registos de controlo de acesso para a matriz Virtual StorSimple](storsimple-ova-manage-acrs.md)|
|Ver detalhes do projecto|Tarefas de serviço → do Gestor de StorSimple| [Gerir tarefas de StorSimple Virtual matriz](storsimple-ova-manage-jobs.md)|
|Configurar definições de alerta</br>Receber notificações de alerta</br>Gerir alertas</br>Reveja os alertas|Gestor de StorSimple serviço → Alertas|[Ver e gerir alertas para a matriz Virtual StorSimple](storsimple-ova-manage-alerts.md)|
|Modificar a palavra-passe de administrador do dispositivo|Gestor de StorSimple serviço → dispositivos → configurar|[Alterar a palavra-passe de administrador do dispositivo de matriz Virtual StorSimple](storsimple-ova-change-device-admin-password.md)|
|Instalar atualizações de software|Gestor de StorSimple serviço → dispositivos → manutenção|[Atualizar a sua matriz Virtual](storsimple-ova-install-update-01.md)|

>[AZURE.NOTE] Tem de utilizar a [web local da IU](storsimple-ova-web-ui-admin.md) para as seguintes tarefas:
>
>- [Obter a chave de encriptação de dados de serviço](storsimple-ova-web-ui-admin.md#get-the-service-data-encryption-key)
>- [Criar um pacote de suporte](storsimple-ova-web-ui-admin.md#generate-a-log-package)
>- [Parar e recomeçar uma matriz Virtual](storsimple-ova-web-ui-admin.md#shut-down-and-restart-your-device)

##<a name="next-steps"></a>Próximos passos
Para informações acerca da IU da web e como utilizá-la, aceda ao [utilizar a web StorSimple da IU para administrar a sua matriz de Virtual StorSimple](storsimple-ova-web-ui-admin.md).
