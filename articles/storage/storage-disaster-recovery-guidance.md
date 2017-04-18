<properties
    pageTitle="O que fazer em caso de uma falha de armazenamento do Windows Azure | Microsoft Azure"
    description="O que fazer em caso de uma falha de armazenamento do Windows Azure"
    services="storage"
    documentationCenter=".net"
    authors="robinsh"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="08/03/2016"
    ms.author="robinsh"/>


# <a name="what-to-do-if-an-azure-storage-outage-occurs"></a>O que fazer se ocorre uma falha de armazenamento do Windows Azure

A Microsoft, podemos trabalhar definitivamente para se certificar de que os nossos serviços estão sempre disponíveis. Por vezes, força para além dos nossos impacto controlo-nos de formas que causam falhas de serviço não planeado em regiões de uma ou mais. Para ajudar a lidar com estes ocorrências raras, fornecemos-as seguintes orientações de alto nível para serviços de armazenamento do Windows Azure.

## <a name="how-to-prepare"></a>Como preparar o 

É fundamental para cada cliente preparar os seus próprios plano de recuperação de falhas. O esforço para recuperar a partir de uma falha de armazenamento normalmente envolve pessoal operações e procedimentos automatizados para reativar a sua aplicações num Estado de funcionamento. Consulte a documentação do Azure abaixo para criar o seu próprio plano de recuperação de falhas:

-   [Recuperação de falhas e elevada disponibilidade para aplicações do Azure](../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md)

-   [Orientação técnica do Azure RDP](../resiliency/resiliency-technical-guidance.md)

-   [Serviço de recuperação de Site Azure](https://azure.microsoft.com/services/site-recovery/)

-   [Azure replicação de armazenamento](storage-redundancy.md)

-   [Serviço de cópia de segurança Azure](https://azure.microsoft.com/services/backup/)

## <a name="how-to-detect"></a>Como detetar 

O caminho recomendado para determinar o estado do serviço Azure é subscrever ao [Dashboard de estado de funcionamento do serviço do Azure](https://azure.microsoft.com/status/).

## <a name="what-to-do-if-a-storage-outage-occurs"></a>O que fazer se ocorre uma falha de armazenamento

Se uma ou mais técnico de armazenamento está temporariamente indisponível regiões de uma ou mais, existem duas opções para a ter em conta. Se pretende acesso imediato para os seus dados, considere a opção 2.

### <a name="option-1-wait-for-recovery"></a>Opção 1: Aguardar recuperação

Neste caso, não é necessária nenhuma ação da sua parte. Estamos a trabalhar diligentemente para restaurar a disponibilidade de serviço Azure. Pode monitorizar o estado do serviço no [Dashboard de estado de funcionamento do serviço do Azure](https://azure.microsoft.com/status/).

### <a name="option-2-copy-data-from-secondary"></a>Opção 2: Copiar dados de secundário

Se tiver optado por [armazenamento geo redundantes acesso de leitura (GRS RT)](storage-redundancy.md#read-access-geo-redundant-storage) (recomendado) para as suas contas de armazenamento, terá acesso de leitura para os seus dados a partir da região secundária. Pode utilizar ferramentas, tal como [AzCopy](storage-use-azcopy.md), [PowerShell do Azure](storage-powershell-guide-full.md)e a [biblioteca de movimento de dados do Azure](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/) para copie os dados da região de secundário para outra conta de armazenamento numa região unimpacted e, em seguida, aponte suas aplicações para essa conta de armazenamento para ambas as ler e escrever disponibilidade.

## <a name="what-to-expect-if-a-storage-failover-occurs"></a>O que esperar se ocorre um armazenamento activação pós-falha

Se optar por [armazenamento Geo redundantes (GRS)](storage-redundancy.md#geo-redundant-storage) ou [armazenamento geo redundantes acesso de leitura (GRS RT)](storage-redundancy.md#read-access-geo-redundant-storage) (recomendado), armazenamento do Windows Azure irão manter os seus dados resistentes nas duas regiões (principais e secundários). Em ambas as regiões, o armazenamento do Windows Azure mantém constantemente várias réplicas dos seus dados.

Quando uma falhas regionais afeta a sua região principal, podemos pela primeira vez Experimente restaurar o serviço nesse região. Depende da natureza da falhas e respetivos impactos, em algumas ocasiões raros podemos poderá não conseguir restaurar a região principal. Nesse momento, podemos fará uma geo com falha. A replicação de dados da publicação em região é um processo assíncrono que pode implicar um atraso, por isso, é possível que as alterações que ainda não foi replicadas à região de secundário poderão serão perdidas. Pode consultar a ["hora da última sincronização" da sua conta de armazenamento](https://blogs.msdn.microsoft.com/windowsazurestorage/2013/12/11/windows-azure-storage-redundancy-options-and-read-access-geo-redundant-storage/) para obter detalhes sobre o estado de replicação.

Algumas pontos em relação à experiência de geo com falha de armazenamento:

-   Armazenamento geo com falha só será acionada pela equipa de armazenamento do Windows Azure – não haja nenhuma ação de cliente necessária.

-   Os existente armazenamento pontos finais de serviço para blobs, tabelas, filas e ficheiros permanecerá igual após a mudança de recurso; a entrada DNS terá de ser atualizados para mudar da região do principal para a região secundária.

-   Antes e durante a mudança de geo, não terá acesso de escrita à sua conta de armazenamento devido o impacto da falhas mas ainda pode ler a partir do secundária se tiver sido configurada a sua conta de armazenamento como GRS RT.

-   Quando tiver sido concluída geo com falha e as alterações DNS foram propagados, o acesso de escrita à sua conta de armazenamento de leitura e serão retomadas. Pode consultar ["Geo activação pós-falha hora da última" da sua conta de armazenamento](https://msdn.microsoft.com/library/azure/ee460802.aspx) para obter mais detalhes.

-   Após a falha na ligação, sua conta de armazenamento irão ser funcionar na totalidade, mas num estado "degradado", tal como está realmente alojada numa região autónomo com possível sem replicação geo. Para mitigar este risco, iremos restaurar a região primária original e, em seguida, efetue uma geo-reposição de recurso para restaurar o estado original. Se a região primária original for irrecuperável, podemos atribuirá outro região secundário.
Para obter mais detalhes sobre a infraestrutura de replicação de geo de armazenamento do Windows Azure, consulte o artigo sobre o blogue da equipa armazenamento sobre as [Opções de redundância e GRS RT](https://blogs.msdn.microsoft.com/windowsazurestorage/2013/12/11/windows-azure-storage-redundancy-options-and-read-access-geo-redundant-storage/).

##<a name="best-practices-for-protecting-your-data"></a>Práticas recomendadas para proteger os seus dados

Existem algumas abordagens recomendadas para criar cópia de segurança dos seus dados de armazenamento regularmente.

-   VM discos – utilize o [serviço de cópia de segurança do Azure](https://azure.microsoft.com/services/backup/) para agregar discos VM utilizados pelo seu máquinas virtuais Azure.

-   Bloquear blobs – criar um [instantâneo](https://msdn.microsoft.com/library/azure/hh488361.aspx) de cada blob bloco ou copiar blobs ao armazenamento de outra conta no outro região utilizando [AzCopy](storage-use-azcopy.md), [Azure PowerShell](storage-powershell-guide-full.md)ou a [biblioteca de movimento de dados do Azure](https://azure.microsoft.com/blog/introducing-azure-storage-data-movement-library-preview-2/).

-   Tabelas – utilize [AzCopy](storage-use-azcopy.md) para exportar os dados da tabela para outra conta de armazenamento no outro região.

-   Ficheiros – utilize [AzCopy](storage-use-azcopy.md) ou [Azure PowerShell](storage-powershell-guide-full.md) para copiar os seus ficheiros para outra conta de armazenamento no outro região.
