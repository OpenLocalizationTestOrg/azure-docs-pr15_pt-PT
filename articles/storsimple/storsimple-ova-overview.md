<properties
   pageTitle="Descrição geral de matriz Virtual StorSimple | Microsoft Azure"
   description="Descreve a matriz Virtual StorSimple, uma solução de armazenamento integrada gere armazenamento tarefas entre um dispositivo virtual no local e o armazenamento em nuvem Microsoft Azure."
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
   ms.date="10/06/2016"
   ms.author="alkohli" />

# <a name="introduction-to-the-storsimple-virtual-array"></a>Introdução à matriz StorSimple Virtual

## <a name="overview"></a>Descrição geral

Bem-vindo ao Microsoft Azure StorSimple Virtual matriz, uma solução de armazenamento integrada gere armazenamento tarefas entre um dispositivo de virtual no local a executar numa hipervisor e armazenamento em nuvem Microsoft Azure. A matriz Virtual (também conhecido como o StorSimple no local virtual dispositivo) é um servidor de ficheiros eficaz, rentável e fácil de gerir facilmente ou solução de servidor de iSCSI elimina muitos problemas e despesas associadas a proteção de armazenamento e dados da empresa. A matriz Virtual é especialmente bem adaptada aos cenários do office (ROBO) office/ramo remoto.

Esta descrição geral foca-se a matriz Virtual. 

- Para obter uma descrição geral da série de StorSimple 8000, aceda a [StorSimple 8000 série: uma solução de nuvem híbrido](storsimple-overview.md). 

- Para obter informações sobre o dispositivo de série StorSimple 5000/7000, vá para o [Ajudar a StorSimple Online](http://onlinehelp.storsimple.com/).

A matriz Virtual suporta a iSCSI ou protocolo de mensagem bloco SMB (Server). -É executada no seu infraestrutura hipervisor existente e fornece tiering à nuvem, cópia de segurança da nuvem, restaurar rápida, recuperação ao nível do item e funcionalidades de recuperação de falhas.

A tabela seguinte resume as funcionalidades importantes da matriz Virtual.

| Funcionalidade | Matriz virtual |
| ------- | ------------- |
|Requisitos de instalação | Utiliza a infraestrutura de Virtualização (Hyper-V ou VMware)|
| Disponibilidade | Nó único |
| Capacidade total (incluindo na nuvem) |Até 64 capacidade utilizáveis TB por dispositivo virtual |
| Capacidade local | GB 390 a capacidade de utilizáveis 6.4 TB por dispositivo virtual (é aprovisionar 500 GB para 8 TB de espaço em disco)|
| Protocolos nativos | iSCSI ou SMB |
| Objectivo de tempo de recuperação (RTO) | iSCSI: menos de 2 minutos, independentemente de tamanho |
| Objectivo de ponto de recuperação (RPO) | Cópias de segurança diárias e cópias de segurança a pedido |
| Armazenamento tiering | Utilizações térmico mapeamento para determinar quais os dados que devem ser camados ou reduzir |
| Suporte | Infraestrutura da Virtualização suportada pelo fornecedor |
| Desempenho | Varia dependendo infraestrutura subjacente |
| Mobilidade de dados | Pode restaurar no mesmo dispositivo ou ao nível do item recuperação (servidor de ficheiros) |
| Camadas de armazenamento | Armazenamento de hipervisor local e na nuvem |
| Tamanho de partilhar |Camadas: de cima para 20 TB; localmente afixados: até 2 TB |
| Tamanho de volume |Camadas: até 5 TB; localmente afixados: até 500 GB |
| Instantâneos | Falha de sistema consistente |
| Recuperação de ao nível do item | Sim; os utilizadores possam restaurar a partir de acções |

## <a name="why-use-storsimple"></a>Porquê utilizar StorSimple?

StorSimple liga-se de utilizadores e servidores ao armazenamento Azure em minutos, com sem modificação de aplicação.

A tabela seguinte descreve alguns dos benefícios chaves que fornece a solução matriz Virtual.

| Funcionalidade | Benefício |
|---------|---------|
| Integração transparente | A matriz Virtual suporta o iSCSI ou o protocolo SMB. A circulação dados entre a camada local e a camada de nuvem está totalmente integrada e transparente para o utilizador.|
| Custos de armazenamento reduzidas | Com StorSimple, aprovisionar armazenamento local suficiente para satisfazer necessidades atuais para os dados quentes utilizadas mais. Que armazenamento necessidades crescem em sequência, StorSimple camadas frios dados rentável nuvem armazenamento. Os dados são deduplicated e comprimidos antes de enviar para a nuvem para reduzir ainda mais requisitos de armazenamento e despesas.|
| Gestão de armazenamento simplificado | StorSimple fornece gestão centralizada na nuvem através do Gestor de StorSimple para gerir vários dispositivos.| 
| Recuperação de falhas melhorada e conformidade | StorSimple facilita a recuperação de falhas mais rápida restaurar os metadados imediatamente e restaurar os dados conforme necessário. Isto significa que operações normais podem continuar com uma interrupção mínima.|
| Mobilidade de dados | Camadas de dados para a nuvem podem ser acedidas a partir de outros sites para fins de recuperação e migração. Tenha em atenção que pode restaurar dados apenas a matriz Virtual original. No entanto, pode utiliza funcionalidades de recuperação de falhas para restaurar a matriz inteira Virtual para outra matriz Virtual.|

## <a name="storsimple-workload-summary"></a>StorSimple carga de trabalho Resumo

Um resumo das cargas de trabalho de StorSimple suportados é tabulado abaixo.

| Cenário                | Carga de trabalho              | Suportada |  Restrições                                  | Versão              |
|-------------------------|-----------------------|-----------|------------------------------------------------|----------------------|
| Colaboração ROBO      | Partilha de ficheiros          | Sim       | Consulte o artigo [limites máximos de servidor de ficheiros](storsimple-ova-limits.md). <br>Consulte o artigo [requisitos de sistema para versões SMB suportadas](storsimple-ova-system-requirements.md).   | Todas as versões      |


## <a name="workflows"></a>Fluxos de trabalho

A matriz Virtual StorSimple é particularmente adequada para os seguintes fluxos de trabalho:

- [Gestão de armazenamento baseado na nuvem](#cloud-based-storage-management)
- [Localização independente cópia de segurança](#location-independent-backup)
- [Recuperação de proteção e falhas de dados](#data-protection-and-disaster-recovery)

### <a name="cloud-based-storage-management"></a>Gestão de armazenamento baseado na nuvem

Pode utilizar o serviço de Gestor de StorSimple em execução no portal do Azure clássico para gerir os dados armazenados em múltiplos dispositivos e em várias localizações. Isto é particularmente útil em cenários ramo distribuído. Tenha em atenção que, tem de criar instâncias separadas do Gestor de StorSimple service to manage Virtual matrizes e dispositivos StorSimple físicos. 

![gestão de armazenamento baseado na nuvem](./media/storsimple-ova-overview/cloud-based-storage-management.png)

### <a name="location-independent-backup"></a>Localização independente cópia de segurança

Com a matriz Virtual, nuvem instantâneos fornecem uma localização independente no momento cópia de um volume ou partilhar. Nuvem instantâneos estiverem ativados por predefinição e não podem ser desativados. Todos os volumes e partilhas são cópia de segurança ao mesmo tempo através de uma política de cópia de segurança diária única, e que pode tomar cópias de segurança do ad hoc adicionais sempre que necessário.

### <a name="data-protection-and-disaster-recovery"></a>Recuperação de proteção e falhas de dados

A matriz Virtual suporta os seguintes dados proteção e falhas recuperação cenários:

- **Volume ou partilhar restaurar** – o restauro de utilização, como novo fluxo de trabalho para recuperar um volume ou partilhar. Utilize esta abordagem para recuperar a partilha ou todo o volume.
- **Recuperação de ao nível do item** – partilhas permitem acesso simplificado aos cópias de segurança recentes. Pode facilmente recuperar ficheiros individuais de uma pasta de .backup especial disponível na nuvem. Esta capacidade de restaurar é orientadas por utilizador e não intervenção administrativa é necessária.
- **Recuperação de falhas** – utilizar a funcionalidade de activação pós-falha para recuperar todos os volumes ou partilhas para uma nova matriz Virtual. Criar nova matriz Virtual e registá-lo com o serviço do Gestor de StorSimple, então falhar sobre a matriz Virtual original. A matriz Virtual nova, em seguida, irá assumir os recursos aprovisionados. 

## <a name="virtual-array-components"></a>Componentes de matrizes virtuais

A matriz Virtual inclui os seguintes componentes:

- [Matriz virtual](#virtual-array) – um dispositivo de armazenamento na nuvem híbrido com base numa máquina virtual aprovisionada no seu ambiente virtualizado ou hipervisor.  
- [Serviço do Gestor de StorSimple](#storsimple-manager-service) – uma extensão do Azure clássica portal que permite-lhe gerir dispositivos de StorSimple de uma ou mais a partir de uma interface de web único que pode aceder a partir de localizações geográficas diferentes. Pode utilizar o serviço do Gestor de StorSimple para criar e gerir serviços, ver e gerir os dispositivos e alertas e gerir os volumes, partilhas e instantâneos existentes.
- [Interface de utilizador local web](#local-web-user-interface) – uma IU baseada na web que é utilizada para configurar o dispositivo para que possa ligar à rede local e, em seguida, registe o dispositivo com o serviço do Gestor de StorSimple. 
- [Interface da linha de comandos](#command-line-interface) – uma interface do Windows PowerShell, que pode utilizar para iniciar uma sessão de suporte na matriz Virtual.
As secções seguintes descrevem cada um dos seguintes componentes em maior detalhe e explicam como a solução organiza os dados, atribui o armazenamento e facilita a gestão de armazenamento e proteção de dados.

### <a name="virtual-array"></a>Matriz virtual

A matriz Virtual é uma solução de armazenamento de nó único que fornece armazenamento principal, gere a comunicação com armazenamento em nuvem e ajuda a garantir a segurança e confidencialidade de todos os dados que está armazenada no dispositivo.

A matriz Virtual está disponível num modelo que está disponível para transferência. A matriz de armazenamento tem uma capacidade máxima de 6.4 TB do dispositivo (com um requisito de armazenamento subjacente de 8 TB) e, em seguida, incluindo 64 TB de armazenamento em nuvem. 

A matriz Virtual tem as seguintes funcionalidades:

- É rentável. -Torna a utilização da sua infraestrutura de Virtualização existente e pode ser implementada no seu hipervisor Hyper-V ou VMware existente.
- -Encontra-se no Centro de dados e pode ser configurado como um iSCSI ou um servidor do ficheiro. 
- Está integrado a com a nuvem.
- Cópias de segurança são armazenadas na nuvem, pode facilitar a recuperação de falhas e simplificar a recuperação ao nível do item (ILR). 
- Pode aplicar atualizações para a matriz Virtual, tal como faria aplicá-los para um dispositivo físico.

>[AZURE.NOTE] Não é possível expandir uma matriz Virtual. Por isso, é importante aprovisionar o armazenamento adequado quando cria o dispositivo virtual. 

### <a name="storsimple-manager-service"></a>Serviço do Gestor de StorSimple

Microsoft Azure StorSimple fornece uma interface de utilizador baseada na web (o serviço do Gestor de StorSimple) que permite-lhe gerir o Centro de dados e armazenamento em nuvem centralmente. Pode utilizar o serviço do Gestor de StorSimple para efetuar as seguintes tarefas:

- Gerir vários matrizes de Virtual StorSimple a partir de um único serviço. 
- Configurar e gerir as definições de segurança para StorSimple dispositivos. (Encriptação na nuvem é dependente Microsoft Azure APIs.)
- Configure propriedades e as credenciais de conta de armazenamento.
- Configurar e gerir volumes ou partilhas.
- Criar cópias de segurança e restauro dos dados num volumes ou partilhas.
- Monitorizar o desempenho.
- Reveja as definições do sistema e identificar possíveis problemas.

Utilizar o serviço do Gestor de StorSimple para executar a administração diária da sua matriz Virtual.

Para mais informações, aceda a [utilizar o serviço do Gestor de StorSimple para administrar o seu dispositivo StorSimple](storsimple-manager-service-administration.md).

### <a name="local-web-user-interface"></a>Interface de utilizador local web

A matriz Virtual inclui uma IU baseada na web que é utilizada para configuração única e registo do dispositivo com o serviço do Gestor de StorSimple. Pode utilizá-lo para encerrar e reinicie a matriz Virtual, executar testes de diagnóstico, atualizar o software, alterar a palavra-passe de administrador do dispositivo, ver registos do sistema e contacte o Microsoft Support de enviar um pedido de serviço. 

Para obter informações sobre como utilizar a IU baseada na web, aceda a [utilizar a IU baseada na web para administrar a sua matriz de Virtual StorSimple](storsimple-ova-web-ui-admin.md).

### <a name="command-line-interface"></a>Interface da linha de comandos

A interface do Windows PowerShell incluída permite-lhe iniciar uma sessão de suporte com Microsoft Support para que podem ajudá-lo a resolver problemas e resolver problemas que poderá encontrar no seu dispositivo virtual.

## <a name="storage-management-technologies"></a>Tecnologias de gestão de armazenamento

Além da matriz Virtual e outros componentes, a solução StorSimple utiliza as seguintes tecnologias de software para fornecer acesso rápido aos dados importantes, reduzir o consumo de armazenamento e proteger dados armazenados no seu Virtual matriz:

- [Armazenamento automático tiering](#automatic-storage-tiering) 
- [Acções localmente afixadas e volumes](#locally-pinned-shares-and-volumes)
- [Eliminação de duplicados e a compressão de dados camadas ou cópias de segurança na nuvem](#deduplication-and-compression-for-data-tiered/backed-up-to-the-cloud) 
- [Cópias de segurança agendadas e a pedido](#scheduled-and-on-demand-backups)

### <a name="automatic-storage-tiering"></a>Armazenamento automático tiering

A matriz Virtual utiliza um novo mecanismo tiering para gerir os dados armazenados a matriz Virtual e na nuvem. Existem apenas dois camadas: o local matriz Virtual e Azure armazenamento em nuvem. A matriz Virtual StorSimple organiza dados automaticamente as camadas com base num mapa térmico, que controla a utilização atual, idade e relações de outros dados. Dados que são mais ativo (fale) está armazenado localmente, enquanto menos dados ativos e inativos automaticamente são migrados para a nuvem. (Todas as cópias de segurança são armazenadas na nuvem.) StorSimple ajusta e reorganiza os dados e alterar atribuições de armazenamento como padrões de utilização. Por exemplo, algumas informações poderão ficar ativas menos ao longo do tempo. À medida que se torne gradualmente menos ativo, é camada saída na nuvem. Se esses mesmos dados fica ativos novamente, é camada para a matriz de armazenamento.

Dados para um determinado partilhar em camadas ou volume são garantir espaço as suas próprias camadas local. (aproximadamente 10% do total espaço aprovisionado para essa partilhar ou volume). Enquanto Isto reduz o armazenamento disponível no dispositivo virtual para essa partilhar ou volume, assegura que tiering para uma partilha ou volume não irão ser afetado pelas necessidades tiering dos outros volumes ou partilhas. Assim uma carga de trabalho muito ocupada num partilhar ou volume não pode forçar todos os outros das cargas de trabalho para a nuvem. 

![armazenamento automático tiering](./media/storsimple-ova-overview/automatic-storage-tiering.png)

>[AZURE.NOTE] Pode especificar um volume como localmente afixado, caso em que os dados permanecem na matriz Virtual e nunca não camadas para a nuvem. Para obter mais informações, aceda ao [localmente afixados partilhas e volumes](#locally-pinned-shares-and-volumes).

### <a name="locally-pinned-shares-and-volumes"></a>Acções localmente afixadas e volumes

Pode criar volumes como localmente afixados e partilhas adequadas. Esta funcionalidade assegura que os dados necessários por aplicações críticas permanecem na matriz Virtual e nunca são camados para a nuvem. Acções localmente afixadas e volumes têm as seguintes funcionalidades: 

- Não estão sujeitos a nuvem latências ou problemas de conectividade.
- Ainda beneficiar do StorSimple nuvem cópia de segurança e falhas funcionalidades de recuperação.

Pode restaurar uma partilha localmente afixada ou volume, como camadas ou uma partilha em camadas ou volume como localmente afixados. 

Para mais informações sobre os volumes localmente afixados, aceda a [utilizar o serviço do Gestor de StorSimple para gerir volumes](storsimple-manage-volumes-u2.md).

### <a name="deduplication-and-compression-for-data-tiered-or-backed-up-to-the-cloud"></a>Eliminação de duplicados e a compressão de dados camadas ou cópias de segurança na nuvem

StorSimple utiliza a compressão de eliminação de duplicados e os dados para reduzir ainda mais requisitos de armazenamento na nuvem. Eliminação de duplicados reduz o montante global da dados armazenados eliminando redundância no conjunto de dados armazenado. À medida que informações são alteradas, StorSimple ignora os dados inalterados e captura apenas as alterações. Além disso, StorSimple reduz a quantidade de dados armazenados, identificar e remover duplicadas informações. 

>[AZURE.NOTE] Dados armazenados na matriz Virtual não são deduplicated ou comprimidos. Todos os deduplication e compressão ocorre antes dos dados são enviados para a nuvem.

### <a name="scheduled-and-on-demand-backups"></a>Cópias de segurança agendadas e a pedido

Funcionalidades de proteção de dados StorSimple permitem-lhe criar cópias de segurança a pedido. Para além disso, uma agenda de cópia de segurança predefinida assegura que dados cópias de segurança diariamente. Cópias de segurança são tidos em forma de instantâneos elementares, que são armazenados na nuvem. Instantâneos, que apenas as alterações de registo desde a última cópia de segurança, podem ser criados e restaurados rapidamente. Estes instantâneos podem ser muito importantes em cenários de recuperação de falhas porque substituir sistemas de armazenamento secundário (como cópia de segurança de banda) e permitem-lhe restaurar os dados para o Centro de dados ou para sites alternativos, se necessário.

## <a name="next-steps"></a>Próximos passos

Saiba como [preparar o portal de matriz Virtual](storsimple-ova-deploy1-portal-prep.md).


