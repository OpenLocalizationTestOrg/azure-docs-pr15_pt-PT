<properties
    pageTitle="Introdução a cópia de segurança do Azure DPM | Microsoft Azure"
    description="Uma introdução à cópias de segurança servidores DPM utilizando o serviço de cópia de segurança do Azure"
    services="backup"
    documentationCenter=""
    authors="Nkolli1"
    manager="shreeshd"
    editor=""
    keywords="Sistema Centro de dados proteção Gestor, Gestor de proteção de dados, dpm cópia de segurança"/>

<tags
    ms.service="backup"
    ms.workload="storage-backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/21/2016"
    ms.author="trinadhk;giridham;jimpark;markgal"/>

# <a name="preparing-to-back-up-workloads-to-azure-with-dpm"></a>Preparar a cópia de segurança das cargas de trabalho para Azure com DPM

> [AZURE.SELECTOR]
- [Servidor de cópia de segurança Azure](backup-azure-microsoft-azure-backup.md)
- [SCDPM](backup-azure-dpm-introduction.md)
- [Servidor de cópia de segurança Azure (clássico)](backup-azure-microsoft-azure-backup-classic.md)
- [SCDPM (clássico)](backup-azure-dpm-introduction-classic.md)


Este artigo fornece uma introdução à utilização de cópia de segurança do Microsoft Azure para proteger os servidores de sistema Centro dados Protection Gestor (DPM) e das cargas de trabalho. Ao ler o artigo-lo, irá compreender:

- Como funciona a cópia de segurança do Azure DPM server
- As pré-requisitos para alcançar uma experiência de cópia de segurança suave
- Foram encontrados típicos erros e como lidar com os mesmos
- Cenários suportados

Centro de sistema DPM cópias de dados de ficheiros e aplicações. Dados de cópia de segurança para DPM podem ser armazenados no fita, no disco, ou cópias de segurança Azure com cópia de segurança do Microsoft Azure. DPM interage com Azure cópias de segurança da seguinte forma:

- **DPM implementado como uma máquina de virtual server ou no local física** — se DPM é implementado como um servidor físico ou como uma máquina de virtual no local Hyper-V pode agregar dados para uma cópia de segurança do Azure cofre para além do disco e banda cópia de segurança.
- **DPM implementado como uma máquina virtual Azure** — a partir do sistema Centro 2012 R2 com 3 de atualização, DPM pode ser implementado como uma máquina virtual Azure. Se for implementado DPM como uma máquina virtual Azure que pode agregar dados a discos Azure anexados à máquina de virtual DPM Azure ou pode descarregar o armazenamento de dados por efetuando até cofre uma cópia de segurança do Azure.

## <a name="why-backup-your-dpm-servers"></a>Por que motivo fazer cópia de segurança seus servidores de DPM?

Os benefícios de negócio de utilização de cópia de segurança do Azure para cópias de segurança servidores DPM incluem:

- Para a implementação de DPM no local, pode utilizar a cópia de segurança do Azure como alternativa à implementação a longo prazo em banda.
- Para implementações DPM no Azure, cópia de segurança do Azure permite-lhe descarregar o armazenamento do disco Azure, permitindo-lhe dimensionar ao armazenar os dados mais antigos no Azure cópia de segurança e os dados novos no disco.

## <a name="how-does-dpm-server-backup-work"></a>Como funciona a cópia de segurança do DPM server?
Em primeiro lugar para agregar uma máquina virtual, é necessário um instantâneo do ponto de tempo dos dados. O serviço de cópia de segurança do Azure inicia a tarefa de cópia de segurança na hora agendada e a extensão de cópia de segurança para tirar um instantâneo de accionadores. A extensão de cópia de segurança coordenadas com o serviço VSS no convidado para alcançar consistência e invoca blob instantâneo API do serviço de armazenamento do Windows Azure quando tiver sido atingida consistência. Isto é feito para obter um instantâneo consistente dos discos de máquina virtual, sem ter de encerrá-lo.

Após ter sido tomado o instantâneo, os dados são transferidos pelo serviço de cópia de segurança do Azure para o cópia de segurança cofre. O serviço demora cuidado de identificação e transferir apenas os blocos que foram alterados da cópia de segurança última tornar o armazenamento de cópias de segurança e de rede eficiente. Quando a transferência de dados estiver concluída, o instantâneo é removido e é criado um ponto de recuperação. Este ponto de recuperação pode ser visto no portal do Azure clássico.

>[AZURE.NOTE] Para máquinas virtuais de Linux, é possível cópia de segurança apenas ficheiro consistentes.

## <a name="prerequisites"></a>Pré-requisitos
Prepare a cópia de segurança do Azure para agregar dados DPM da seguinte forma:

1. **Criar um cofre de cópia de segurança** — criar cofre na consola do Azure cópia de segurança.
2. **Transferência de credenciais de cofre** — na cópia de segurança Azure, carregar o certificado de gestão que criou para o cofre.
3. **Instalar o agente de cópia de segurança do Azure e registar o servidor** — a partir do Azure cópia de segurança, instale o agente em cada servidor DPM e registar o servidor DPM no cofre cópia de segurança.

[AZURE.INCLUDE [backup-create-vault](../../includes/backup-create-vault.md)]

[AZURE.INCLUDE [backup-download-credentials](../../includes/backup-download-credentials.md)]

[AZURE.INCLUDE [backup-install-agent](../../includes/backup-install-agent.md)]


## <a name="requirements-and-limitations"></a>Requisitos de (e limitações)

- DPM pode estar em execução como um servidor físico ou uma máquina de virtual Hyper-V instalado no sistema de centro de 2012 SP1 ou sistema Centro 2012 R2. Também pode ser executada como uma máquina virtual Azure em execução no sistema de centro de 2012 R2 com, pelo menos, DPM 2012 R2 Update Rollup 3 ou uma máquina de virtual do Windows no VMWare em execução no sistema de centro de 2012 R2 com, pelo menos, Update Rollup 5.
- Se estiver a executar DPM com sistema Centro 2012 SP1, deve instalar Update Rollup 2 para SP1 de Gestor de proteção de dados do Centro de sistema. O que é necessário antes de poder instalar o agente de cópia de segurança do Azure.
- O servidor DPM deve ter Windows PowerShell e o .net Framework 4,5 instalado.
- DPM pode cópias das cargas de trabalho a maior parte dos Azure cópia de segurança. Para uma lista completa do que tem suportado consulte a cópia de segurança do Azure suporte itens abaixo.
- Não não possível recuperar dados armazenados num Azure cópia de segurança com a opção "copiar para a banda".
- Terá de uma conta Azure com a funcionalidade de cópia de segurança do Azure ativada. Se não tiver uma conta, pode criar uma conta de avaliação gratuita apenas de duas minutos. Leia sobre [preços de cópia de segurança do Azure](https://azure.microsoft.com/pricing/details/backup/).
- Utilização de cópia de segurança do Azure requer o agente de cópia de segurança do Azure ser instalado em servidores que pretende fazer cópia de segurança. Cada servidor tem de ter, pelo menos, 10% do tamanho dos dados que estão a ser cópia de segurança, disponíveis como armazenamento gratuito no local. Por exemplo, criar cópias de segurança de 100 GB de dados requer um mínimo de 10 GB de espaço livre na localização rascunho. Enquanto o mínimo é 10%, é recomendado 15% de espaço de armazenamento gratuito no local para ser utilizado para a localização da cache.
- Os dados serão armazenados no armazenamento Azure cofre. Não existe limite para a quantidade de dados que pode até uma cópia de segurança do Azure do Cofre de palavras, mas o tamanho de uma origem de dados (por exemplo uma máquina virtual ou base de dados) não deve exceder 54,400 GB.

Estes tipos de ficheiro são suportados para até Azure:

- Encriptada (cópias de segurança completas apenas)
- Comprimidos (utilizarão cópias de segurança suportadas)
- Dispersos (utilizarão cópias de segurança suportados)
- Comprimido e de dispersão (tratada como Sparse)

E estes não são suportadas:

- Os servidores de sistemas de ficheiros entre maiúsculas e minúsculas não são suportados.
- Ligações de disco rígidas (ignorado)
- Nova análise pontos (ignorado)
- Encriptadas e comprimidos (ignorado)
- Encriptadas e dispersos (ignorado)
- Sequência comprimida
- Sequência de dispersa

>[AZURE.NOTE] A partir no sistema de centro de 2012 DPM com SP1 diante, pode fazer cópia de segurança para cima das cargas de trabalho protegidas por DPM para Azure utilizando a cópia do Microsoft Azure.
