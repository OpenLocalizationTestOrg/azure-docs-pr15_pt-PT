<properties 
   pageTitle="O que é StorSimple? | Microsoft Azure" 
   description="Descreve StorSimple tiering, o dispositivo, dispositivo virtual, serviços e gestão de armazenamento e apresenta a termos chave utilizados nas StorSimple." 
   services="storsimple" 
   documentationCenter="NA" 
   authors="SharS" 
   manager="carmonm" 
   editor=""/>

<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD" 
   ms.date="10/05/2016"
   ms.author="v-sharos@microsoft.com"/>

# <a name="storsimple-8000-series-a-hybrid-cloud-storage-solution"></a>Série de StorSimple 8000: uma solução de armazenamento na nuvem híbrido

## <a name="overview"></a>Descrição geral

Bem-vindo ao Microsoft Azure StorSimple, uma solução de armazenamento integrada gere armazenamento tarefas entre dispositivos no local e armazenamento em nuvem Microsoft Azure. StorSimple é uma solução de rede (SAN) de área de armazenamento eficaz, rentável e fácil de gerir facilmente que elimina muitos problemas e despesas associadas a proteção de armazenamento e dados da empresa. Utiliza o dispositivo de série StorSimple 8000 especialidades, integra-se com serviços em nuvem e fornece um conjunto de ferramentas de gestão para uma vista de forma totalmente integrada de todo o armazenamento de empresa, incluindo armazenamento em nuvem. (As informações de implementação de StorSimple publicadas no Web site da Microsoft Azure aplica-se a dispositivos de série de StorSimple 8000 apenas. Se estiver a utilizar um dispositivo de série StorSimple 5000/7000, aceda a [Ajuda de StorSimple](http://onlinehelp.storsimple.com/).)

StorSimple utiliza [armazenamento tiering](#automatic-storage-tiering) para gerir os dados armazenados através de vários suportes de armazenamento. O conjunto de trabalho atual é armazenados no local em unidades de estado sólido (SSDs), os dados que são utilizados com menos frequência são armazenados em unidades de disco rígido (HDDs) e arquivo dados são enviados para a nuvem. Além disso, StorSimple utiliza compressão e eliminação de duplicados para reduzir a quantidade de armazenamento consome os dados. Para obter mais informações, aceda a [eliminação de duplicados e a compressão](#deduplication-and-compression). Para obter definições de outros termos chaves e conceitos utilizados na documentação StorSimple 8000 série, aceda a [terminologia StorSimple](#storsimple-terminology) no final deste artigo.

Com StorSimple Update 2, é possível identificar volumes adequados como *localmente afixados* para garantir que os dados principal permanecem locais para o dispositivo e não camada para a nuvem. Permite-lhe executar cargas de trabalho que são sensíveis a latência na nuvem, como SQL e máquina virtual das cargas de trabalho, em localmente afixados volumes, enquanto continua a utilizar a nuvem para cópia de segurança. Para mais informações sobre os volumes localmente afixados, consulte o artigo [utilizar o serviço do Gestor de StorSimple para gerir volumes](storsimple-manage-volumes-u2.md). 

Actualização 2 também permite-lhe criar dispositivos virtuais StorSimple que tirem partido da latências baixos e alto desempenho fornecida pela armazenamento Azure premium. Para obter mais informações sobre os dispositivos virtuais do StorSimple premium, consulte o artigo [Implementar e gerir um dispositivo virtual StorSimple no Azure](storsimple-virtual-device-u2.md). Para obter mais informações sobre o armazenamento do Azure premium, aceda a [armazenamento Premium: armazenamento de alto desempenho para cargas de trabalho do Azure Máquina Virtual](../storage/storage-premium-storage.md).

Para além de gestão de armazenamento, funcionalidades de proteção de dados StorSimple permitem-lhe criar a pedido e agendado cópias de segurança e, em seguida, armazenar localmente ou na nuvem. Cópias de segurança são tidos em forma de instantâneos elementares, que significa que podem ser criados e restaurados rapidamente. Nuvem instantâneos podem ser muito importantes em cenários de recuperação de falhas porque substituir sistemas de armazenamento secundário (como cópia de segurança de banda) e permitem-lhe restaurar os dados para o Centro de dados ou para sites alternativos, se necessário.

![ícone de vídeo](./media/storsimple-overview/video_icon.png) Veja o vídeo para uma breve introdução ao Microsoft Azure StorSimple.

> [AZURE.VIDEO storsimple-hybrid-cloud-storage-solution]

## <a name="why-use-storsimple"></a>Porquê utilizar StorSimple?

A tabela seguinte descreve alguns dos benefícios chaves que fornece do Microsoft Azure StorSimple.

| Funcionalidade | Benefício |
|---------|---------|
|Integração transparente | Microsoft Azure StorSimple utiliza o protocolo de iSCSI para ligar em diante instalações de armazenamento de dados. Isto assegura que os dados armazenados na nuvem, o Centro de dados, ou em servidores remotos parece estar armazenados num único local.|
|Custos de armazenamento reduzidas|Microsoft Azure StorSimple atribui suficiente local ou de armazenamento em nuvem para satisfazer necessidades atuais e expande a armazenamento em nuvem apenas quando for necessário. Ainda mais reduz requisitos de armazenamento e despesas eliminando redundantes versões dos mesmos dados (eliminação de duplicados) e ao utilizar a compressão.|
|Gestão de armazenamento simplificado|Microsoft Azure StorSimple fornece o sistema de ferramentas de administração que pode utilizar para configurar e gerir dados armazenados no local, num servidor remoto e na nuvem. Para além disso, pode gerir a cópia de segurança e restaurar funções a partir de um snap-in Consola de gestão da Microsoft (MMC). StorSimple fornece uma interface opcional, separada que pode utilizar para alargar a serviços de proteção de dados e gestão StorSimple para conteúdo armazenado nos servidores do SharePoint. |
|Recuperação de falhas melhorada e conformidade|Microsoft Azure StorSimple não requer o tempo de recuperação expandida. Em vez disso, restaura dados conforme for necessário. Isto significa que operações normais podem continuar com uma interrupção mínima. Para além disso, pode configurar políticas para especificar agendas de cópia de segurança e retenção de dados.
|Mobilidade de dados|Dados carregados para serviços em nuvem da Microsoft Azure podem ser acedidos a partir de outros sites para fins de recuperação e migração. Para além disso, pode utilizar StorSimple para configurar os dispositivos virtuais StorSimple em máquinas virtuais (VMs) em execução no Microsoft Azure. Os VMs, em seguida, podem utilizar os dispositivos virtuais para aceder a dados armazenados para fins de teste ou recuperação.|
|Suporte para outros fornecedores de serviço de nuvem |A série de StorSimple 8000 com um software de atualização 1 ou posterior suporta Amazon S3 com RRS, HP e OpenStack cloud services, bem como Microsoft Azure. (Irá necessitar de uma conta de armazenamento do Microsoft Azure para fins de gestão de dispositivos.) Para obter mais informações, vá para [o que é novo no 1.2 de atualização](storsimple-update1-release-notes.md#whats-new-in-update-12).|
|Continuidade de negócios | Actualização de 1 ou posterior fornece uma nova funcionalidade de migração que permite aos utilizadores de série de 5000 7000 migrar os respetivos dados para um dispositivo de série StorSimple 8000 de StorSimple.|
|Disponibilidade no Portal do Azure administração pública | Está disponível no Portal de administração pública do Azure StorSimple actualização 1 ou posterior. Para obter mais informações, consulte [Implementar o seu dispositivo de StorSimple no local no Portal de administração pública](storsimple-deployment-walkthrough-gov.md).|
|Proteção de dados e disponibilidade | A série de StorSimple 8000 com 1 de atualização ou posterior suporta zona redundantes armazenamento (ZRS), para além de armazenamento localmente redundantes armazenamento (LRS) e Geo redundantes (GRS). Consulte [Este artigo sobre as opções de redundância de armazenamento do Windows Azure](https://azure.microsoft.com/documentation/articles/storage-redundancy/) para obter detalhes ZRS.|
|Suporte para as aplicações críticas | Com StorSimple Update 2, é possível identificar volumes adequados como localmente afixados. Esta funcionalidade assegura que os dados necessários por aplicações críticas não são camados para a nuvem. Volumes localmente afixados não estão sujeitos a nuvem latências ou problemas de conectividade. Para mais informações sobre os volumes localmente afixados, consulte o artigo [utilizar o serviço do Gestor de StorSimple para gerir volumes](storsimple-manage-volumes-u2.md).|
|Latência baixa e alto desempenho | StorSimple Update 2 permite-lhe criar virtuais dispositivos que tirar partido de alto desempenho, funcionalidades de latência baixa de armazenamento do Azure premium. Para obter mais informações sobre os dispositivos virtuais do StorSimple premium, consulte o artigo [Implementar e gerir um dispositivo virtual StorSimple no Azure](storsimple-virtual-device-u2.md).|

![ícone de vídeo](./media/storsimple-overview/video_icon.png) veja [Este vídeo](https://www.youtube.com/watch?v=4MhJT5xrvQw&feature=youtu.be) para obter uma descrição geral das StorSimple 8000 série funcionalidades e benefícios.

## <a name="storsimple-components"></a>Componentes de StorSimple

A solução do Microsoft Azure StorSimple inclui os seguintes componentes:

- **Dispositivo do Microsoft Azure StorSimple** – uma matriz de armazenamento de híbrido no local que contenha SSDs e HDDs, juntamente com os controladores redundantes e capacidades de activação pós-falha automática. Os controladores de gerir o armazenamento de tiering, colocação atualmente dados utilizados (ou quentes) no armazenamento local (no dispositivo ou no local servidores), enquanto move menos dados utilizados com frequência para a nuvem.
- **Dispositivo virtual StorSimple** – também conhecido como aparelho Virtual StorSimple, esta é uma versão de software do dispositivo StorSimple que replica a arquitetura e a maioria das capacidades do dispositivo de armazenamento híbrido física. O dispositivo virtual StorSimple é executado num único nó numa máquina virtual Azure. Dispositivos virtuais Premium, que tirar partido de armazenamento do Azure premium, estão disponíveis no Update 2 e versões posteriores.
- **Serviço do Gestor de StorSimple** – uma extensão do Azure clássica portal que permite-lhe gerir um StorSimple dispositivo ou StorSimple virtual a partir de uma interface única web. Pode utilizar o serviço do Gestor de StorSimple para criar e gerir serviços, ver e gerir dispositivos, ver os alertas, gerir volumes e ver e gerir políticas de cópia de segurança e do catálogo de cópia de segurança.
- **Windows PowerShell para StorSimple** – uma interface de comandos que pode utilizar para gerir o dispositivo StorSimple. Windows PowerShell para StorSimple tem funcionalidades que permitem-lhe registar o seu dispositivo StorSimple, configure a interface de rede no seu dispositivo, instalar determinados tipos de atualizações, resolver o seu dispositivo acedendo a sessão de suporte e altere o estado do dispositivo. Pode aceder a Windows PowerShell para StorSimple ao ligar à consola série ou ao utilizar o Windows PowerShell remoto.
- **Cmdlets de azure PowerShell StorSimple** – uma coleção de cmdlets do Windows PowerShell que permitem-lhe automatizar tarefas de nível de serviço e migração da linha de comandos. Para obter mais informações sobre os cmdlets do Azure PowerShell para StorSimple, vá para a [referência do cmdlet](https://msdn.microsoft.com/library/dn920427.aspx).
- **Gestor de instantâneo StorSimple** – um snap-in MMC que utiliza grupos de volume e o serviço de cópia de sombra de Volume do Windows para gerar a aplicação consistente cópias de segurança. Além disso, pode utilizar o Gestor de instantâneo StorSimple criar cópia de segurança agendas e clonar ou restaurar volumes. 
- **Placa StorSimple para o SharePoint** – uma ferramenta de que forma transparente expande a Microsoft Azure StorSimple armazenamento e dados protecção em farms do SharePoint Server, ao efetuar o armazenamento de StorSimple visualizável e fácil de gerir a partir do portal de Administração Central do SharePoint.

O diagrama abaixo fornece uma vista de alto nível do Microsoft Azure StorSimple arquitetura e componentes.

![Arquitetura de StorSimple](./media/storsimple-overview/overview-big-picture.png)

As secções seguintes descrevem cada um dos seguintes componentes em maior detalhe e explicam como a solução organiza os dados, atribui o armazenamento e facilita a gestão de armazenamento e proteção de dados. A última secção fornece definições para alguns dos termos importantes e conceitos relacionados com a StorSimple componentes e da sua gestão.

## <a name="storsimple-device"></a>Dispositivo StorSimple

O dispositivo do Microsoft Azure StorSimple é uma matriz de armazenamento de híbrido no local que fornece armazenamento principal e iSCSI acesso aos dados armazenados no mesmo. Gere a comunicação com armazenamento em nuvem e ajuda a garantir a segurança e confidencialidade de todos os dados que está armazenada na solução do Microsoft Azure StorSimple.

O dispositivo StorSimple inclui SSDs e HDDs unidades de disco rígido, bem como suporte para clusters e automático activação pós-falha. Contém um processador partilhado, armazenamento partilhado e dois controladores espelhadas. Controlador de cada fornece o seguinte:

- Ligação para um computador anfitrião
- Até seis portas de rede para ligar à rede local (LAN)
- Monitorização de hardware
- Acesso aleatório não voláteis a memória (NVRAM), que mantém informações mesmo se power é interrompido
- Clusters atualizar para gerir as atualizações de software em servidores um cluster de activação pós-falha para que as atualizações tenham mínimas ou sem efeito sobre a disponibilidade de serviço
- Serviço de cluster, que funciona como um cluster de back-end, fornecendo elevada disponibilidade e minimizar quaisquer efeitos adversos que poderão ocorrer se um disco ou SSD falha ou é colocado offline

Controlador de apenas uma está ativo em qualquer ponto no tempo. Se o controlador de ativo falhar, o segundo controlador fica ativo automaticamente. 

Para obter mais informações, aceda ao [StorSimple componentes de hardware e de estado](storsimple-monitor-hardware-status.md).

## <a name="storsimple-virtual-device"></a>Dispositivo virtual StorSimple

Pode utilizar StorSimple para criar um dispositivo de virtual replica a arquitetura e capacidades do dispositivo de armazenamento híbrido física. O dispositivo virtual StorSimple (também conhecido como o StorSimple Virtual aparelho) é executado num único nó numa máquina virtual Azure. (Um dispositivo virtual só pode ser criado numa máquina virtual Azure. Não pode criar um num dispositivo de StorSimple ou um servidor no local.) 

O dispositivo virtual tem as seguintes funcionalidades:

- -Comporta-se como um aparelho físico e pode oferecem uma interface iSCSI para máquinas virtuais na nuvem. 
- Pode criar um número ilimitado de dispositivos virtuais na nuvem e ativá-los e desativar conforme necessário. 
- Pode ajudar a simular ambientes no local no recuperação de falhas, desenvolvimento e cenários de teste e ajudá-lo ao nível do item de obtenção de cópias de segurança. 

Com Update 2 e posterior, o dispositivo virtual StorSimple está disponível em dois modelos: o dispositivo de 8010 (anteriormente conhecido como o modelo de 1100) e o dispositivo 8020. O dispositivo 8010 tem uma capacidade máxima de 30 TB. O dispositivo de 8020, tira partido de armazenamento de Azure premium, tem uma capacidade máxima de 64 TB. (No locais camadas, armazenamento Azure premium armazena dados no SSDs Considerando que armazenamento padrão armazena os dados no HDDs.) Tenha em atenção que tem de ter uma conta de armazenamento Azure premium ao utilizar o armazenamento de premium. Para obter mais informações sobre o armazenamento de premium, aceda a [armazenamento Premium: armazenamento de alto desempenho para cargas de trabalho do Azure Máquina Virtual](../storage/storage-premium-storage.md).

Para mais informações sobre o dispositivo virtual StorSimple, aceda a [Implementar e gerir um dispositivo virtual StorSimple no Azure](storsimple-virtual-device-u2.md).

## <a name="storsimple-manager-service"></a>Serviço do Gestor de StorSimple

Microsoft Azure StorSimple fornece uma interface de utilizador baseada na web (o serviço do Gestor de StorSimple) que permite-lhe gerir o Centro de dados e armazenamento em nuvem centralmente. Pode utilizar o serviço do Gestor de StorSimple para efetuar as seguintes tarefas:

- Configure definições do sistema para StorSimple dispositivos.
- Configurar e gerir as definições de segurança para StorSimple dispositivos.
- Configure credenciais da nuvem e propriedades.
- Configurar e gerir volumes num servidor.
- Configure os grupos de volume.
- Criar cópias de segurança e restaurar os dados.
- Monitorizar o desempenho.
- Reveja as definições do sistema e identificar possíveis problemas.

Pode utilizar o serviço do Gestor de StorSimple para executar todas as tarefas de administração, exceto aqueles que necessitam de sistema para baixo de tempo, tal como a configuração inicial e a instalação das atualizações.

Para mais informações, aceda a [utilizar o serviço do Gestor de StorSimple para administrar o seu dispositivo StorSimple](storsimple-manager-service-administration.md).

## <a name="windows-powershell-for-storsimple"></a>Windows PowerShell para StorSimple

Windows PowerShell para StorSimple fornece uma interface de comandos que pode utilizar para criar e gerir o serviço do Microsoft Azure StorSimple e configurar o e monitorizar StorSimple dispositivos. É uma interface de comandos baseada em Windows PowerShell, que inclui dedicados cmdlets para gerir o seu dispositivo StorSimple. Windows PowerShell para StorSimple tem funcionalidades que permitem-lhe:

- Registe-se um dispositivo.
- Configure a interface de rede num dispositivo.
- Instale determinados tipos de atualizações.
- Resolver problemas de dispositivo acedendo a sessão de suporte.
- Altere o estado do dispositivo.

Pode aceder Windows PowerShell para StorSimple a partir de uma série consola (num computador anfitrião ligado diretamente ao dispositivo) ou remotamente ao utilizar o Windows PowerShell remoto. Tenha em atenção que algumas Windows PowerShell para tarefas de StorSimple, tais como registo dispositivo inicial, só pode ser efetuado num consola do série. 

Para obter mais informações, aceda ao [Utilizar o Windows PowerShell para StorSimple administrar o seu dispositivo](storsimple-windows-powershell-administration.md).

## <a name="azure-powershell-storsimple-cmdlets"></a>Cmdlets do PowerShell StorSimple Azure

Os cmdlets do Azure PowerShell StorSimple são uma colecção de cmdlets do Windows PowerShell que permitem-lhe automatizar tarefas de nível de serviço e migração da linha de comandos. Para obter mais informações sobre os cmdlets do Azure PowerShell para StorSimple, vá para a [referência do cmdlet](https://msdn.microsoft.com/library/dn920427.aspx).

## <a name="storsimple-snapshot-manager"></a>Gestor de instantâneo StorSimple

É o Gestor de instantâneo StorSimple um snap-in Consola de gestão da Microsoft (MMC) que pode utilizar para criar consistente e cópias de segurança de ponto no tempo do local e na nuvem dados. O snap-in é executado num sistema anfitrião com base no Windows Server. Pode utilizar o Gestor de instantâneo StorSimple para:

- Configurar, criar cópias de segurança e eliminar volumes.
- Configurar os grupos de volume para se certificar de que criar cópias de segurança dos dados sejam consistentes para a aplicação.
- Gerir políticas de cópia de segurança para que os dados são cópias de uma agenda predeterminada e armazenados numa localização designada (localmente ou na nuvem).
- Restaure os volumes e ficheiros individuais.

Cópias de segurança são captadas como instantâneo, que apenas as alterações de registo, desde que foi colocada em limite da última e exigir extremidade menos espaço de armazenamento de cópias de segurança completas. Pode criar agendas de cópia de segurança ou tirar cópias de segurança de imediatas, conforme necessário. Para além disso, pode utilizar StorSimple instantâneo gestor para estabelecer políticas de retenção que controlam quantos instantâneos serão guardados. Se tiver subsequentemente de restaurar dados a partir de uma cópia de segurança, permite-lhe do Gestor de instantâneo StorSimple seleciona o catálogo de local ou na nuvem instantâneos. 

Se ocorre uma falhas ou se precisar de restaurar os dados por qualquer outra razão, o Gestor de instantâneo StorSimple restaura-incrementada conforme for necessário. Restauro de dados não requer que encerrar a totalidade do sistema enquanto restaurar um ficheiro, substituir equipamento ou mover operações para outro local.

Para obter mais informações, aceda a [o que é o Gestor de instantâneo StorSimple?](storsimple-what-is-snapshot-manager.md)

## <a name="storsimple-adapter-for-sharepoint"></a>Placa StorSimple para o SharePoint

Microsoft Azure StorSimple inclui a placa StorSimple para o SharePoint, um componente opcional que transparente expande a funcionalidades de proteção de armazenamento e dados StorSimple em farms do SharePoint server. A placa funciona com um fornecedor de armazenamento de BLOBs remoto (RBS) e a funcionalidade de RBS do SQL Server, permitindo-lhe mover BLOBs para um servidor de cópia de segurança pelo sistema Microsoft Azure StorSimple. Microsoft Azure StorSimple, em seguida, armazena os BLOB dados localmente ou na nuvem, com base em utilização.

O adaptador StorSimple para o SharePoint é gerido a partir do portal de Administração Central do SharePoint. Consequentemente, gestão do SharePoint permanece centralizada e todo o armazenamento parece estar no farm do SharePoint.

Para obter mais informações, aceda ao [Adaptador StorSimple para o SharePoint](storsimple-adapter-for-sharepoint.md). 

## <a name="storage-management-technologies"></a>Tecnologias de gestão de armazenamento
 
Para além do dispositivo StorSimple dedicado, dispositivo virtual e outros componentes, Microsoft Azure StorSimple utiliza as seguintes tecnologias de software para fornecer acesso aos dados e para reduzir o consumo de armazenamento:

- [Armazenamento automático tiering](#automatic-storage-tiering) 
- [Aprovisionamento fina](#thin-provisioning) 
- [Eliminação de duplicados e compressão](#deduplication-and-compression) 

### <a name="automatic-storage-tiering"></a>Armazenamento automático tiering

Microsoft Azure StorSimple organiza automaticamente os dados em camadas lógicos com base em relação a outros dados, idade e utilização atual. Dados que é mais ativos são armazenados localmente, enquanto menos dados ativos e inativos automaticamente são migrados para a nuvem. O diagrama seguinte ilustra esta abordagem de armazenamento.
 
![Camadas de armazenamento StorSimple](./media/storsimple-overview/hcs-data-services-storsimple-components-tiers.png)

Para ativar o acesso rápido, StorSimple armazena dados muito ativos (dados quentes) no SSDs no dispositivo StorSimple. Armazena dados que são utilizados ocasionalmente (interessado dados) no HDDs no dispositivo ou nos servidores no Centro de dados. Move-la Inativos dados de cópia de segurança, e dados retidos para arquivo ou para fins de conformidade na nuvem. 

>[AZURE.NOTE] No Update 2 ou posterior, pode especificar um volume como localmente afixado, caso em que os dados permanecem no dispositivo local e não não camadas para a nuvem. 

StorSimple ajusta e reorganiza os dados e alterar atribuições de armazenamento como padrões de utilização. Por exemplo, algumas informações poderão ficar ativas menos ao longo do tempo. À medida que se torne gradualmente menos ativo, é migrado a partir do SSDs para HDDs e, em seguida, na nuvem. Se esses mesmos dados fica ativos novamente, é migrado para o dispositivo de armazenamento.

O processo de tiering de armazenamento ocorre da seguinte forma:

1. Um administrador de sistema configura uma conta de armazenamento do Microsoft Azure na nuvem.
2. O administrador utiliza a consola série e o serviço do Gestor de StorSimple (em execução no portal do Azure clássico) para configurar o dispositivo e ficheiro de servidor, criar políticas de proteção de volumes e dados. Máquinas no local (como servidores do ficheiro) utilizam a Interface de sistema de computador pequenas Internet (iSCSI) para aceder ao dispositivo StorSimple.
3. Inicialmente, StorSimple armazena os dados na camada SSD rápida do dispositivo.
4. Tal como a camada SSD abordagens capacidade, StorSimple deduplicates comprime os blocos de dados mais antigo e move-os para a camada de disco.
5. Como a capacidade de abordagens do disco camada, StorSimple encripta os blocos de dados mais antigo e envia-os em segurança para a conta de armazenamento do Microsoft Azure através do HTTPS.
6. Microsoft Azure cria várias réplicas dos dados no seu centro de dados e num centro de dados remoto, garantindo que podem recuperar os dados caso ocorra uma falhas. 
7. Quando o servidor de ficheiros solicita dados armazenados na nuvem, StorSimple devolve-la de forma totalmente integrada e armazena uma cópia na camada SSD do dispositivo StorSimple.

### <a name="thin-provisioning"></a>Aprovisionamento fina

Aprovisionamento fino é uma tecnologia de Virtualização na qual armazenamento disponível aparece exceda físicos recursos. Em vez de reserva de armazenamento suficiente com antecedência, StorSimple utiliza aprovisionamento fina para alocar espaço apenas suficiente para cumprir requisitos atuais. A natureza flexível de armazenamento na nuvem facilita a esta abordagem porque StorSimple pode aumentar ou diminuir o armazenamento em nuvem para satisfazer necessidades alteração. 

>[AZURE.NOTE] Volumes localmente afixados não são pequenos aprovisionados. Armazenamento atribuído para um só local volume está aprovisionado na sua totalidade quando é criado o volume.

### <a name="deduplication-and-compression"></a>Eliminação de duplicados e compressão

Microsoft Azure StorSimple utiliza a compressão de eliminação de duplicados e os dados para reduzir ainda mais requisitos de armazenamento.

Eliminação de duplicados reduz o montante global da dados armazenados eliminando redundância no conjunto de dados armazenado. À medida que informações são alteradas, StorSimple ignora os dados inalterados e captura apenas as alterações. Além disso, StorSimple reduz a quantidade de dados armazenados, identificar e remover informações desnecessárias. 

>[AZURE.NOTE] Dados de volumes de localmente afixados não são deduplicated ou comprimidos. No entanto, cópias de segurança de volumes localmente afixados são deduplicated e comprimidas.

## <a name="storsimple-workload-summary"></a>StorSimple carga de trabalho Resumo

Um resumo das cargas de trabalho de StorSimple suportados é tabulado abaixo.

| Cenário                  | Carga de trabalho                | Suportada |  Restrições                                  | Versão              |
|---------------------------|-------------------------|-----------|------------------------------------------------|----------------------|
| Colaboração             | Partilha de ficheiros            | Sim       |                                                | Todas as versões         |
| Colaboração             | Partilha de ficheiros distribuído| Sim       |                                                | Todas as versões         |
| Colaboração             | SharePoint              | Sim *      |Suportado apenas com volumes localmente afixados      | Update 2 e posterior   |
| Arquivo                  | Arquivo de ficheiros simples   | Sim       |                                                | Todas as versões         |
| Virtualização            | Máquinas virtuais        | Sim *      |Suportado apenas com volumes localmente afixados      | Update 2 e posterior   |
| Base de dados                  | SQL                     | Sim *      |Suportado apenas com volumes localmente afixados      | Update 2 e posterior   |
| Controlo de vídeo        | Controlo de vídeo      | Sim *       |Funcionalidades suportadas quando o dispositivo de StorSimple é dedicado apenas este carga de trabalho| Update 2 e posterior   |
| Cópia de segurança                    | Cópia de segurança de destino principal | Sim *      |Funcionalidades suportadas quando o dispositivo de StorSimple é dedicado apenas este carga de trabalho| Atualização 3 e posterior |
| Cópia de segurança                    | Cópia de segurança de destino secundário | Sim *      |Funcionalidades suportadas quando o dispositivo de StorSimple é dedicado apenas este carga de trabalho| Atualização 3 e posterior |

*Sim & #42; -Diretrizes de solução e restrições devem ser aplicadas.*

As seguintes cargas de trabalho não são suportadas pelo StorSimple 8000 dispositivos de série. Se implementada num StorSimple, estas cargas de trabalho irão resultar numa configuração não suportada.

-  Processamento de imagens médico
-  Exchange
-  VDI
-  Oracle
-  SAP
-  Dados grandes
-  Distribuição de conteúdo
-  Arranque a partir do SCSI

Segue-se uma lista dos componentes de infraestrutura StorSimple suportado. 

| Cenário | Carga de trabalho      | Suportada |  Restrições                                 | Versão      |
|----------|---------------|-----------|-----------------------------------------------|--------------|
| Geral  | Encaminhar Express | Sim       |                                               |Todas as versões |
| Geral  | DataCore FC   | Sim *       |Suportado com DataCore SANsymphony            | Todas as versões |
| Geral  | DFSR          | Sim *      |Suportado apenas com volumes localmente afixados     | Todas as versões |
| Geral  | Indexação      | Sim *       |Em camadas volumes, indexação metadados só é suportada (sem dados).<br>Para volumes localmente afixados, concluir a indexação é suportada.| Todas as versões |
| Geral  | Antivírus    | Sim *       |Para volumes em camadas, é suportado apenas pesquisa ao abrir e fechar.<br> Para volumes localmente afixados, pesquisa completa é suportada.| Todas as versões |

*Sim & #42; -Diretrizes de solução e restrições devem ser aplicadas.*

## <a name="storsimple-terminology"></a>Terminologia StorSimple 

Antes de implementar a solução do Microsoft Azure StorSimple, recomendamos que reveja os seguintes termos e definições.

### <a name="key-terms-and-definitions"></a>Chaves termos e definições

| Termo (acrónimo ou abreviatura) | Descrição |
| ------------------------------ | ---------------- |
| registo de controlo de acesso (ACR)    | Um registo associado um volume no seu dispositivo do Microsoft Azure StorSimple que determina quais anfitriões podem ligar-se à mesma. Determinar se baseia o iSCSI qualificado nome (IQN) de anfitriões (contidos a ACR) que estejam a ligar ao seu dispositivo StorSimple.|
| AES 256                        | Um algoritmo de encriptação AES (Advanced Standard) 256 bits para encriptar dados tal como é move o cursor para e a partir da nuvem. |
| tamanho da unidade de alocação (Austrália)     | A menor quantidade de espaço de disco que pode ser atribuída para suportar um ficheiro no seu Windows sistemas de ficheiros. Se um tamanho de ficheiro não for um múltiplo par do tamanho cluster, espaço extra deve ser utilizado para sem soltar o ficheiro (por excesso para o múltiplo seguinte do tamanho do cluster) que resulta no espaço perdido e fragmentação do disco rígido. <br>A Austrália recomendada para Azure StorSimple volumes é 64 KB porque funciona bem com os algoritmos de eliminação de duplicados.|
| armazenamento automatizado tiering      | Mover automaticamente os dados menos ativos da SSDs para HDDs e, em seguida, para uma camada na nuvem e, em seguida, permitindo-gestão do armazenamento de todas as partir de uma interface de utilizador central.|
| catálogo de cópia de segurança | Uma coleção de cópias de segurança, normalmente, relacionados com, o tipo de aplicação que foi utilizado. Nesta coleção de é apresentada na página de catálogo de cópia de segurança do serviço do Gestor de StorSimple IU.|
| ficheiro de cópia de segurança de catálogo             | Um ficheiro que contenha uma lista de instantâneos disponíveis atualmente armazenados na base de dados cópia de segurança do StorSimple instantâneo gestor. |
| política de cópia de segurança                   | Uma seleção de volumes, tipo de cópia de segurança e um calendário que permite-lhe criar cópias de segurança com base numa agenda predefinido.|
| objectos de grandes dimensões binários (BLOBs)    | Uma coleção de dados binários armazenados como uma única entidade num sistema de gestão da base de dados. BLOBs são normalmente imagens, áudio ou outros objetos de multimédia, embora código executável, por vezes, binário está armazenado como um BLOB.|
| Protocolo de autenticação desafio Handshake (CHAP) | Protocolo utilizado para autenticar o ponto de uma ligação, com base em peer partilha de uma palavra-passe ou palavra-passe. CHAP pode ser unidirecional ou comum. Com o CHAP unidirecional, o destino autentica um iniciador. CHAP comum requer que o destino autenticar o iniciador e que o iniciador autentica de destino. | 
| clonar                          | Uma cópia de um volume duplicada. |
|Nuvem, como uma camada (CaaT)          | Armazenamento integrado como uma camada dentro a arquitetura de armazenamento para que seja apresentada todo o armazenamento façam parte da rede de uma empresa de armazenamento em nuvem.|
| fornecedor de serviços na nuvem (CSP)   | Um fornecedor de serviços de computação de nuvem.|
| instantâneo de nuvem                 | Uma cópia em qualquer altura de dados de volume que estão armazenados na nuvem. Um instantâneo de nuvem é equivalente a um instantâneo de replicadas num sistema armazenamento diferentes, externo. Nuvem instantâneos são particularmente úteis em cenários de recuperação de falhas.|
| chave de encriptação de armazenamento na nuvem   | Uma palavra-passe ou uma chave de utilizado pelo seu dispositivo StorSimple para aceder aos dados encriptados enviados pelo seu dispositivo para a nuvem.|
| Atualizar clusters         | Gerir as atualizações de software em servidores um cluster de activação pós-falha para que as atualizações tenham mínimas ou sem efeito sobre a disponibilidade de serviço.|
| DataPath                       | Uma coleção de unidades funcionais que executar operações de transformação de dados interligadas.|
| desativar                     | Uma acção permanente quebra a ligação entre o dispositivo StorSimple e o serviço de nuvem associado. Nuvem instantâneos do dispositivo permanecem após este processo e podem ser clonar ou utilizados para recuperação de falhas.|
| reflexão de disco                 | Unidades de replicação de volumes de disco lógico em separado disco rígido em tempo real para garantir a disponibilidade contínua.|
| disco dinâmico espelhando as         | Replicação de volumes de disco lógico de discos dinâmicos.|
| discos dinâmicos                  | Formato de volume de disco que utiliza o Gestor de disco lógicos (LDM) para armazenar e gerir dados em múltiplos discos físicos. Discos dinâmicos podem ser do Friso do Excel para fornecer mais espaço livre.|
| Adicional inclusão de conjunto de discos (EBOD) | Uma inclusão secundário do seu dispositivo do Microsoft Azure StorSimple que contém discos extra disco rígido para armazenamento adicional.|
| Aprovisionamento FAT               | Um armazenamento convencional aprovisionamento no qual armazenamento espaço que é atribuído com base em antecipada necessidades (e é normalmente para além da necessidade de atual). Consulte também *aprovisionamento fina*.|
| unidade de disco rígido (disco)          | Uma unidade de que utiliza pratos um rotação para armazenar dados.|
| armazenamento em nuvem híbrido           | Arquitetura de armazenamento que utiliza recursos locais e externo, incluindo o armazenamento em nuvem.|
| Pequenas computador sistema Interface da Internet (iSCSI) | Um padrão de funcionamento em rede armazenamento com base em IP protocolo Internet para ligar equipamento de armazenamento de dados ou de produção.|
| iniciador iSCSI                 | Um componente de software que permite que o computador anfitrião com o Windows para ligar a uma rede externa armazenamento baseado em iSCSI.|
| iSCSI qualificado nome (IQN)      | Um nome exclusivo que identifica um destino ou iniciador iSCSI.|
| destino iSCSI                    | Um componente de software que fornece iSCSI centralizada subsistemas de disco em redes de área de armazenamento.|
| Live arquivo                  | Uma abordagem de armazenamento na qual dados arquivo estão acessíveis sempre (mesmo não está armazenado noutro local numa banda, por exemplo). Microsoft Azure StorSimple utiliza arquivo direto.|
|volume localmente afixada | um volume que reside no dispositivo e nunca é camadas para a nuvem. |
| instantâneo local                  | Uma cópia em qualquer altura de dados de volume que estão armazenados no dispositivo Microsoft Azure StorSimple.|
| StorSimple do Microsoft Azure      | Uma poderosa solução que consiste num aparelho de armazenamento do Centro de dados e software que permitem às organizações IT tirar partido armazenamento em nuvem, como se estivesse armazenamento do Centro de dados. StorSimple simplifica a proteção de dados e gestão de dados enquanto estiver reduzindo os custos. A solução consolida armazenamento, arquivar, cópia de segurança e falhas recuperação primária (DR) através da integração de forma totalmente integrada com a nuvem. Através da combinação de gestão de dados de armazenamento e nuvem SAN numa plataforma classe empresarial, StorSimple dispositivos activar a velocidade, simplificar e fiabilidade para todas as necessidades relacionados com armazenamento.|
| Power e de refrigeração módulo (PCM)  | Componentes de hardware do seu dispositivo StorSimple que consiste as fontes de alimentação e a ventoinha, por conseguinte, o nome do Power e de refrigeração módulo. A inclusão principal do dispositivo tem dois 764W PCMs enquanto a inclusão EBOD tem dois 580W PCMs.|
| inclusão principal               | Inclusão principal do seu dispositivo StorSimple que contém os controladores de plataforma de aplicação.|
| objectivo de tempo de recuperação (RTO)   | A quantidade máxima de tempo que deve ser gasto antes de um processo de negócio ou sistema totalmente é restaurada após uma falhas.| 
|série anexado SCSI (SA)       | Um tipo de unidade de disco rígido (disco).|
| chave de encriptação de dados de serviço     | Uma chave de disponibilizados para qualquer novo dispositivo StorSimple que regista com o serviço do Gestor de StorSimple. Os dados de configuração transferidos entre o serviço do Gestor de StorSimple e o dispositivo estão encriptados a utilizar uma chave pública e, em seguida, podem ser desencriptar apenas no dispositivo com uma chave privada. Chave de encriptação de dados de serviço permite que o serviço obter esta chave privada para desencriptação.|
| chave de registo do serviço        | Uma chave que ajuda a registar o dispositivo de StorSimple com o serviço do Gestor de StorSimple para que seja apresentada no portal do Azure clássico para ainda mais ações de gestão.|
| Interface de sistema do computador pequenas (SCSI) | Um conjunto de padrões para física ligar computadores e transmitir dados entre elas.|
| unidade de estado sólido (SSD)         | Um disco que contenha sem mover peças; Por exemplo, uma pen USB.|
| conta de armazenamento                 | Um conjunto de credenciais de acesso ligados à sua conta de armazenamento para um fornecedor de serviço de nuvem determinado.| 
| Placa StorSimple para o SharePoint| Um componente do Microsoft Azure StorSimple transparente expande a proteção de armazenamento e dados de StorSimple em farms do SharePoint server.|
| Serviço do Gestor de StorSimple      | Extensão do Azure clássica portal que permite-lhe a gestão do Azure StorSimple no local e dispositivos virtuais.|
| Gestor de instantâneo StorSimple     | Um consola de gestão da Microsoft (MMC) snap-in para gestão de operações de cópia de segurança e restauro no Microsoft Azure StorSimple.|
| tirar cópias de segurança                     | Uma funcionalidade que permite ao utilizador tirar uma cópia de segurança interativa de um volume. É uma forma alternativa de efetuar uma cópia de segurança manual de um volume por oposição a efetuar uma cópia de segurança automatizada através de uma política definida.|
| Aprovisionamento fina               | Método de otimizar a eficiência com o qual o espaço de armazenamento disponível é utilizado na sistemas de armazenamento. No aprovisionamento fina, o armazenamento de que é atribuído entre vários utilizadores com base no espaço mínimo necessário por cada utilizador a qualquer momento. Consulte também *fat aprovisionamento*.|
| tiering | Dispor dados agrupamentos lógicos com base em relação a outros dados, idade e utilização atual. StorSimple organiza automaticamente os dados em camadas.   |
| volume                          | Áreas de armazenamento lógicas apresentadas sob a forma de unidades. StorSimple volumes correspondem aos volumes instalados pelo anfitrião, incluindo as descobertas através da utilização de iSCSI e um dispositivo StorSimple.|
 | contentor de volume                | Um agrupamento de volumes e as definições que se aplicam aos mesmos. Todos os volumes no seu dispositivo StorSimple são agrupados em contentores de volume. Definições de contentor de volume incluem contas de armazenamento, definições de encriptação para dados enviados para a cloud com as teclas de encriptação associada e a largura de banda consumida para operações que envolvam na nuvem.|
| grupo de volume                    | No Gestor de StorSimple instantâneo, um grupo de volume é uma coleção de volumes configurado para facilitar o processamento de cópia de segurança.|
| Serviço de cópia sombra (VSS)| Um serviço de sistema operativo Windows Server, que facilita a consistência da aplicação ao comunicar com aplicações deverá ter em consideração VSS para coordenar a criação de instantâneos utilizarão. VSS assegura que as aplicações são inactivas temporariamente quando são disponibilizados instantâneos.|
| Windows PowerShell para StorSimple | Uma interface de linha de comandos com base no Windows PowerShell utilizado operar e gerir o seu dispositivo StorSimple. Mantendo algumas das funcionalidades básicas de empresa do Windows PowerShell, esta interface tem cmdlets dedicados adicionais que são orientados para gerir um dispositivo StorSimple.|


## <a name="next-steps"></a>Próximos passos

Saiba mais sobre [segurança StorSimple](storsimple-security.md).




 

 
