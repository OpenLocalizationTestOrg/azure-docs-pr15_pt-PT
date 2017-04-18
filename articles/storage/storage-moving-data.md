<properties
    pageTitle="Mover dados de e a partir do armazenamento Azure | Microsoft Azure"
    description="Este artigo fornece uma descrição geral dos diferentes métodos para mover dados de armazenamento do Windows Azure e para."
    services="storage"
    documentationCenter=""
    authors="micurd"
    manager="jahogg"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/21/2016"
    ms.author="micurd"/>

# <a name="moving-data-to-and-from-azure-storage"></a>Mover dados de armazenamento do Windows Azure e para

Se pretender mover os dados no local ao armazenamento do Azure (ou vice versa), existem várias formas para o fazer. A abordagem que funciona melhor por si serão variam consoante o seu cenário. Este artigo irá fornecer uma descrição geral do cenários diferentes e ofertas adequadas para cada um deles.

## <a name="building-applications"></a>Criar aplicações

Se estiver a compilar uma aplicação, desenvolver contra REST API ou um dos nossos muitos bibliotecas do cliente é uma excelente forma de mover os dados de armazenamento do Windows Azure e para.

Armazenamento Azure fornece bibliotecas do cliente formatado para .NET, iOS, Java, Android, Universal Windows plataforma (UWP), Xamarin, C++, Node.JS, PHP, Rubi e Python. As bibliotecas do cliente fornecem funcionalidades avançadas como lógica de repetição, registo e carregamentos paralelos. Também pode desenvolver diretamente contra REST API, que podem ser designadas por qualquer idioma que faz com que os pedidos de HTTP/HTTPS.

Consulte o artigo [Introdução ao armazenamento de Blobs do Azure](storage-dotnet-how-to-use-blobs.md) para obter mais informações.

Além disso, também Oferecemos a [Biblioteca de movimento do Azure armazenamento de dados](https://www.nuget.org/packages/Microsoft.Azure.Storage.DataMovement) que é uma biblioteca de concebido para copiar de alto desempenho de dados para e a partir do Azure. Consulte a nossa [documentação](https://github.com/Azure/azure-storage-net-data-movement) de biblioteca de movimento de dados para obter mais informações. 

## <a name="quickly-viewinginteracting-with-your-data"></a>Rapidamente visualização/interagir com os seus dados

Se pretender que uma forma fácil de ver os seus dados de armazenamento do Windows Azure enquanto está a ter também a capacidade de carregar e transferir os seus dados, em seguida, considere utilizar o Explorador de armazenamento de Azure.

Consulte o artigo nossa lista de [Explorador de armazenamento do Azure](storage-explorers.md) para obter mais informações.

## <a name="system-administration"></a>Administração do sistema

Se exigir ou são mais à vontade fica com um utilitário da linha de comandos (por exemplo, os administradores de sistema), aqui estão algumas opções para a ter em conta:

### <a name="azcopy"></a>AzCopy

AzCopy é um utilitário da linha de comandos do Windows concebido para copiar de alto desempenho de dados para e a partir do armazenamento do Windows Azure. Também pode copiar dados dentro de uma conta de armazenamento ou entre contas de armazenamento diferente.

Para obter mais informações, consulte [Transferir dados com o utilitário AzCopy da linha de comandos](storage-use-azcopy.md) .

### <a name="azure-powershell"></a>Azure PowerShell

Azure PowerShell é um módulo que fornece cmdlets de gestão de serviços no Azure. É uma baseado em tarefas da linha de comandos shell e linguagem concebido especialmente para a administração do sistema.

Consulte o artigo [Utilizar o PowerShell Azure com armazenamento do Windows Azure](storage-powershell-guide-full.md) para obter mais informações.

### <a name="azure-cli"></a>Clip Azure

Clip Azure fornece um conjunto de abrir origem, em diferentes plataformas comandos para trabalhar com os serviços do Azure. Clip Azure está disponível no Windows, os x e Linux.

Consulte o artigo [utilizar o clip do Azure com armazenamento do Windows Azure](storage-azure-cli.md) para obter mais informações.

## <a name="moving-large-amounts-of-data-with-a-slow-network"></a>Mover grandes quantidades de dados com uma rede lenta

Uma das maiores desafios associados mover grandes quantidades de dados é o tempo de transferência. Se pretender obter dados para a armazenamento do Windows Azure sem preocupar com custos de redes ou escrever o código, Azure importar/exportar é uma solução adequada.

Consulte o artigo [Azure importar/exportar](storage-import-export-service.md) para obter mais informações.

## <a name="backing-up-your-data"></a>Criar cópias de segurança dos seus dados

Se precisar simplesmente a cópia de segurança dos seus dados para o armazenamento do Windows Azure, cópia de segurança do Azure é a forma para ir. Esta é uma solução poderosa para criar cópias de segurança de dados no local e Azure VMs.

Consulte [Azure cópia de segurança](../backup/backup-introduction-to-azure-backup.md) para obter mais informações.

## <a name="accessing-your-data-on-premises-and-from-the-cloud"></a>Aceder aos seus dados no local e a partir da nuvem

Se precisar de uma solução para aceder aos seus dados no local e a partir da nuvem, deverá considere utilizar a solução de armazenamento de nuvem de implementações do Azure, StorSimple. Esta solução é composta por um dispositivo StorSimple físico que inteligente frequentemente armazena dados utilizados no SSDs, ocasionalmente de dados utilizados no HDDs e dados inativa/cópia de segurança/arquivo no armazenamento do Windows Azure.

Consulte o artigo [StorSimple](../storsimple/storsimple-overview.md) para obter mais informações.

## <a name="recovering-your-data"></a>Recuperar os seus dados

Quando tiver das cargas de trabalho no local e aplicações, terá uma solução que permite a sua empresa continuar a trabalhar rapidamente um eventualidade. Recuperação de Site Azure trata replicação, activação pós-falha e recuperação de máquinas virtuais e servidores físicos. Dados replicados são armazenados no armazenamento do Windows Azure, permitindo-lhe eliminar a necessidade de um centro de dados secundária no local.

Consulte o artigo [Azure recuperação de Site](../site-recovery/site-recovery-overview.md) para obter mais informações.
