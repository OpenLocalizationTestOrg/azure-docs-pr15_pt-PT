<properties 
   pageTitle="O que é o Gestor de instantâneo StorSimple? | Microsoft Azure"
   description="Descreve o Gestor de instantâneo StorSimple, sua arquitetura e suas funcionalidades."
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
   ms.date="05/24/2016"
   ms.author="v-sharos" />

# <a name="what-is-storsimple-snapshot-manager"></a>O que é o Gestor de instantâneo StorSimple?

## <a name="overview"></a>Descrição geral

Gestor de instantâneo StorSimple é um snap-in Consola de gestão da Microsoft (MMC) que simplifica proteção de dados e gestão de cópia de segurança num ambiente do Microsoft Azure StorSimple. Com o StorSimple instantâneo Gestor, pode gerir dados do Microsoft Azure StorSimple no Centro de dados e na nuvem como uma solução de armazenamento integrada única assim simplificar os processos de cópia de segurança e reduzir os custos.

Esta descrição geral apresenta o Gestor de instantâneo StorSimple, descreve as suas funcionalidades e explica o seu papel no Microsoft Azure StorSimple. 

Para obter uma descrição geral do sistema Microsoft Azure StorSimple todo, incluindo o dispositivo StorSimple, serviço StorSimple Gestor, StorSimple instantâneo gestor e StorSimple adaptador para o SharePoint, consulte o artigo [StorSimple 8000 série: uma solução de armazenamento na nuvem híbrido](storsimple-overview.md). 
 
>[AZURE.NOTE] 
>
>- Não pode utilizar o Gestor de instantâneo StorSimple para gerir matrizes virtuais do Microsoft Azure StorSimple (também conhecido como StorSimple no local dispositivos virtuais).
>
>- Se planear instalar StorSimple Update 2 no seu dispositivo StorSimple, certifique-se de que transferir a versão mais recente do Gestor de instantâneo StorSimple e instalá-lo **antes de instalar StorSimple Update 2**. A versão mais recente do StorSimple instantâneo gestor é compatível com versões anteriores e funciona com todas as versões de lançamento do Microsoft Azure StorSimple. Se estiver a utilizar a versão anterior do StorSimple instantâneo Gestor, terá de atualizar (não ter de desinstalar a versão anterior antes de instalar a nova versão).

## <a name="storsimple-snapshot-manager-purpose-and-architecture"></a>Objectivo de StorSimple instantâneo gestor e de arquitetura

Gestor de instantâneo StorSimple fornece uma consola de gestão central que pode utilizar para criar consistente e cópias de segurança de ponto no tempo do local e na nuvem dados. Por exemplo, pode utilizar a consola para:

- Configurar, criar cópias de segurança e eliminar volumes.
- Configurar os grupos de volume para se certificar de que criar cópias de segurança dos dados sejam consistentes para a aplicação.
- Gerir políticas de cópia de segurança para que os dados cópias de uma agenda predeterminada.
- Criar local e na nuvem instantâneos, que podem ser armazenados na nuvem e utilizados para recuperação de falhas.

O Gestor de instantâneo StorSimple obtém na lista de aplicações registado com o fornecedor VSS no anfitrião do. Em seguida, para criar cópias de segurança aplicação consistente, verifica os volumes utilizados por uma aplicação e sugere grupos de volume para configurar. Gestor de instantâneo StorSimple utiliza estes grupos de volume para gerar cópias de segurança que sejam consistentes de aplicação. (As consistência da aplicação existe quando todos os ficheiros relacionados e bases de dados são sincronizados e representam o verdadeiro estado da aplicação num ponto específico no tempo). 

Gestor de instantâneo StorSimple cópias de segurança assumem a forma de instantâneos elementares, que apenas as alterações de captura desde a última cópia de segurança. Como resultado, cópias de segurança necessitam de menos armazenamento e podem ser criadas e restauradas rapidamente. Gestor de instantâneo StorSimple utiliza o serviço cópia do Windows Volume sombra (VSS) para se certificar de que instantâneos capturar dados consistentes de aplicação. (Para obter mais informações, aceda a integração com secção Windows serviço de cópia sombra.) Com o StorSimple instantâneo Gestor, pode criar agendas de cópia de segurança ou tirar cópias de segurança de imediatas, conforme necessário. Se precisar de restaurar dados a partir de um permite StorSimple instantâneo Gestor cópia de segurança, selecione a partir de um catálogo de local ou na nuvem instantâneos. Azure StorSimple restaura apenas os dados que são necessária, conforme for necessário, que impede atrasos na disponibilidade de dados durante as operações de restaurar.)

![Arquitetura de StorSimple instantâneo Gestor](./media/storsimple-what-is-snapshot-manager/HCS_SSM_Overview.png)

**Arquitetura de StorSimple instantâneo Gestor** 

## <a name="support-for-multiple-volume-types"></a>Suporte para múltiplos tipos de volume

Pode utilizar o Gestor de instantâneo StorSimple para configurar e agregar os seguintes tipos de volumes: 

- **Volumes básicos** – um volume básico é uma única partição num disco básico. 

- **Volumes simples** – um volume simples é um volume dinâmico que contém o espaço em disco a partir de um único disco dinâmico. Um simple volume é composta por uma única região de um disco ou em várias regiões que estão ligadas em conjunto no mesmo disco. (Pode criar volumes simples apenas em discos dinâmicos.) Volumes simples não são tolerância a falhas.

- **Volumes dinâmicos** – um volume dinâmico é um volume criado num disco dinâmico. Discos dinâmicos utilizam uma base de dados para controlar as informações sobre os volumes contidos em discos dinâmicos no computador. 

- **Volumes dinâmicos com espelhando as** – volumes dinâmicos com espelhando as foram criadas na arquitetura de RAID 1. Com RAID 1, são escritos dados idênticos no disco de duas ou mais, produzir um conjunto espelhado. Um pedido de leitura, em seguida, poderá ser resolvido por qualquer disco que contém os dados pedidos.

- **Volumes partilhados de cluster** – com volumes partilhados de cluster (CSVs), vários nós num cluster de activação pós-falha em simultâneo podem leitura ou escrita no mesmo disco. Activação em pós-falha a partir de um nó para outro nó pode ocorrer rapidamente, sem necessidade de uma alteração na propriedade de unidade ou montagem, desmontar e remover um volume. 

>[AZURE.IMPORTANT] Não misture CSVs e não CSVs no limite da mesma. Misturar CSVs e não CSVs num instantâneo não é suportada. 
 
Pode utilizar o Gestor de instantâneo StorSimple para restaurar os grupos de todo o volume ou clonar volumes individuais e recuperar ficheiros individuais.

- [Volumes e grupos de volume](#volumes-and-volume-groups) 
- [Tipos de cópia de segurança e políticas de cópia de segurança](#backup-types-and-backup-policies) 

Para mais informações sobre funcionalidades do Gestor de instantâneo StorSimple e como utilizá-las, consulte o artigo [interface de utilizador StorSimple instantâneo Gestor](storsimple-use-snapshot-manager.md).

## <a name="volumes-and-volume-groups"></a>Volumes e grupos de volume

Com o StorSimple instantâneo Gestor, pode criar volumes e, em seguida, configurá-los em grupos de volume. 

Gestor de instantâneo StorSimple utiliza grupos de volume para criar cópias de segurança que sejam consistentes de aplicação. Consistência da aplicação existe quando todos os ficheiros relacionados e bases de dados são sincronizados e representam o verdadeiro estado de uma aplicação num ponto específico no tempo. Grupos de volume (que também são conhecidos como *grupos de consistência*) formam a base de uma cópia de segurança ou restaurar a tarefa.

Grupos de volume não são a mesma contentores de volume. Um contentor de volume contém um ou mais volumes partilham uma conta de armazenamento na nuvem e outros atributos, como o consumo de encriptação e a largura de banda. Um contentor único volume pode conter até 256 volumes de StorSimple pequenos aprovisionados. Para mais informações sobre contentores de volume, aceda a [Gerir os contentores de volume](storsimple-manage-volume-containers.md). Grupos de volume são coleções de volumes que configurar para facilitar operações de cópia de segurança. Se selecionar dois volumes que pertencem aos contentores de volume diferente, colocá-los num grupo único volume e, em seguida, criar uma política de cópia de segurança para esse grupo volume, cada volume vai ser cópia de segurança no contentor de volume adequado, utilizando a conta de armazenamento adequado.

>[AZURE.NOTE] Todos os volumes num grupo de volume tem de vir de um fornecedor de serviço de nuvem único.

## <a name="integration-with-windows-volume-shadow-copy-service"></a>Integração com o serviço de cópia sombra Windows

Gestor de instantâneo StorSimple utiliza o serviço cópia do Windows Volume sombra (VSS) para capturar dados consistentes de aplicação. VSS facilita a consistência da aplicação ao comunicar com aplicações deverá ter em consideração VSS para coordenar a criação de instantâneos utilizarão. VSS assegura que as aplicações são inactivo temporariamente ou modo silencioso, quando são disponibilizados instantâneos. 

A aplicação StorSimple instantâneo Gestor da VSS funciona com o SQL Server e volumes NTFS genéricos. O processo é da seguinte forma: 

1. Um autor do pedido, que é normalmente uma gestão de dados e solução protection (tal como StorSimple instantâneo Gestor) ou uma aplicação de cópia de segurança, invoca VSS e pede-la para reunir informações a partir do software de sénior na aplicação de destino.

2. VSS contacta o componente de sénior para obter uma descrição dos dados. O gravador de devolve a descrição dos dados a ser cópias de segurança. 

3. VSS sinais sénior para preparar a aplicação para cópia de segurança. O gravador de prepara os dados de cópia de segurança ao completar transações abrir, atualizar registos de transações e assim sucessivamente e, em seguida, notifica o VSS.

4. VSS instrui o gravador de temporariamente parar de arquivos de dados da aplicação e certifique-se de que não existem dados destina-se para o volume enquanto a cópia sombra é criada. Este passo garante consistência dos dados e demora mais do que 60 segundos.

5. VSS instrui o fornecedor para criar a cópia sombra. Fornecedores, que podem ser software ou hardware são baseados, gerir os volumes que estão atualmente em execução e criar cópias de sombra-las a pedido. O fornecedor cria a cópia de sombra e notifica VSS quando estiver concluída.

6. VSS contacta o gravador de para notificar a aplicação que pode retomar a e/s e também para confirmar que e/s foi colocada em pausa com êxito durante a criação de cópia de sombra. 

7. Se a cópia foi efetuada com êxito, VSS devolve localização a cópia para o autor do pedido. 

8. Se tiver sido escritos dados enquanto a cópia sombra foi criada, a cópia de segurança serão inconsistente. VSS elimina a cópia sombra e notifica o autor do pedido. O autor do pedido pode repetir o processo de cópia de segurança automaticamente ou notificar o administrador para repeti-la posteriormente.

Consulte a seguinte ilustração.

![Processo VSS](./media/storsimple-what-is-snapshot-manager/HCS_SSM_VSS_process.png)

**Processo de serviço de cópia sombra Windows** 

## <a name="backup-types-and-backup-policies"></a>Tipos de cópia de segurança e políticas de cópia de segurança

Com o StorSimple instantâneo Gestor, pode agregar dados e armazená-lo localmente e na nuvem. Pode utilizar o Gestor de instantâneo StorSimple para agregar dados imediatamente ou pode utilizar uma política de cópia de segurança para criar uma agenda para tirar cópias de segurança automaticamente. Políticas de cópia de segurança também permitem-lhe especificar quantos instantâneos serão retidos. 

### <a name="backup-types"></a>Tipos de cópia de segurança

Pode utilizar o Gestor de instantâneo StorSimple para criar os seguintes tipos de cópias de segurança:

- **Instantâneos locais** – instantâneos Local são em qualquer altura cópias dos dados de volume que estão armazenados no dispositivo StorSimple. Normalmente, este tipo de cópia de segurança pode ser criado e restaurado rapidamente. Pode utilizar um instantâneo local, tal como faria com uma cópia de segurança local.

- **Instantâneos na nuvem** – nuvem instantâneos estão no momento cópias dos dados de volume que estão armazenados na nuvem. Um instantâneo de nuvem é equivalente a um instantâneo de replicadas num sistema armazenamento diferentes, externo. Nuvem instantâneos são particularmente úteis em cenários de recuperação de falhas.

### <a name="on-demand-and-scheduled-backups"></a>Cópias de segurança a pedido e agendadas

Com o StorSimple instantâneo Gestor, pode iniciar uma cópia de segurança única seja criado imediatamente ou pode utilizar uma política de cópia de segurança para agendar operações de cópia de segurança periódicas.

Uma política de cópia de segurança é um conjunto de regras automatizados que pode utilizar para agendar cópias de segurança regulares. Uma cópia de segurança política permite-lhe definir a frequência e os parâmetros para tomar instantâneos de um grupo de volume específico. Pode utilizar políticas para especificar datas de início e de expiração, horas, frequência e requisitos de retenção, para ambas as local e na nuvem instantâneos. Uma política é aplicada imediatamente depois de defini-lo. 

Pode utilizar o Gestor de instantâneo StorSimple para configurar ou reconfigure as políticas de cópia de segurança sempre que necessário. 

Configurar as seguintes informações para cada política de cópia de segurança que criar:

- **Nome** – o nome exclusivo da política de cópia de segurança seleccionada.

- **Tipo de** – o tipo de política de cópia de segurança; instantâneo local ou instantâneo na nuvem.

- **Grupo de volume** – o grupo de volume que é atribuída a política de cópia de segurança seleccionada.

- **Retenção** – o número de cópias de segurança a manter. Se selecionar a caixa de **Tudo** , todas as cópias de segurança são mantidas até que o número máximo de cópias de segurança por volume é atingido, altura em que a política irá falhar e gerar uma mensagem de erro. Em alternativa, pode especificar um número de cópias de segurança para reter (entre 1 e 64).

- **Data** – a data quando a política de cópia de segurança foi criada.

Para obter informações sobre como configurar políticas de cópia de segurança, aceda ao [Utilizar o Gestor de instantâneo StorSimple para criar e gerir políticas de cópia de segurança](storsimple-snapshot-manager-manage-backup-policies.md).

### <a name="backup-job-monitoring-and-management"></a>Tarefa de cópia monitorização e gestão

Pode utilizar o Gestor de instantâneo StorSimple para monitorizar e gerir tarefas de cópia de segurança futuras, agendadas e concluídas. Para além disso, o Gestor de instantâneo de StorSimple fornece um catálogo de cópias de segurança concluídas até 64. Pode utilizar o catálogo para localizar e restaurar volumes ou ficheiros individuais. 

Para obter informações sobre a monitorização de tarefas de cópia de segurança, aceda ao [Utilizar o Gestor de instantâneo StorSimple para ver e gerir tarefas de cópia de segurança](storsimple-snapshot-manager-manage-backup-jobs.md).


## <a name="next-steps"></a>Próximos passos

- Saiba mais sobre como [utilizar o Gestor de instantâneo StorSimple para administrar a sua solução StorSimple](storsimple-snapshot-manager-admin.md).

- [Instantâneo de StorSimple Gestor](https://www.microsoft.com/download/details.aspx?id=44220)de transferências.
