<properties
    pageTitle="Como utilizar o armazenamento Azure para cópia de segurança do SQL Server e restaurar | Microsoft Azure"
    description="Aprenda a cópia de segurança do SQL Server ao armazenamento do Azure. Explica as vantagens de cópias de bases de dados SQL Azure armazenamento."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="MikeRayMSFT"
    manager="jhubbard"
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="07/22/2016"
    ms.author="mikeray"/>

# <a name="use-azure-storage-for-sql-server-backup-and-restore"></a>Utilizar o armazenamento Azure para cópia de segurança do SQL Server e restauro

## <a name="overview"></a>Descrição geral

A partir do SQL Server 2012 SP1 CU2, pode escrever agora cópias de segurança do SQL Server diretamente para o serviço de armazenamento de Blobs do Azure. Pode utilizar esta funcionalidade para criar até de segurança e restaurar a partir do serviço de Blobs do Azure com uma base de dados do SQL Server no local ou uma base de dados do SQL Server uma máquina virtual Azure. Cópia de segurança para o cloud oferece vantagens de disponibilidade, ilimitado replicadas geo externo de armazenamento e facilidade de migração de dados para e a partir da nuvem. Pode emitir declarações de cópia de segurança ou restaurar utilizando Transact-SQL ou SMO.

Novas capacidades; apresenta o SQL Server 2016 Pode utilizar [um instantâneo de ficheiro cópia de segurança](http://msdn.microsoft.com/library/mt169363.aspx) para efetuar cópias de segurança quase instantâneas e restaura incrivelmente rápida.

Este tópico explica por que razão poderá optar por utilizar armazenamento Azure para cópias de segurança SQL e, em seguida, descreve os componentes envolvidos. Pode utilizar os recursos fornecidos no final do artigo para aceder a tutoriais e informações adicionais para começar a utilizar este serviço com as cópias de segurança do SQL Server.

## <a name="benefits-of-using-the-azure-blob-service-for-sql-server-backups"></a>Vantagens de utilizar o serviço de Blobs do Azure para cópias de segurança do SQL Server

Existem várias desafios que cara quando cópias de segurança do SQL Server. Estes são os desafios mais incluem gestão de armazenamento, risco de falha de armazenamento, acesso ao armazenamento externo e configuração do hardware. Muitos destes desafios estão endereçados utilizando o serviço de arquivo de Blobs do Azure para cópias de segurança do SQL Server. Tenha em atenção os seguintes benefícios:

- **Facilidade de utilização**: armazenar as cópias de segurança no Azure blobs pode ser uma conveniente, flexível e fácil de aceder a opção externa. Criar externo armazenamento para as cópias de segurança do SQL Server pode ser tão fácil como modificar os scripts/trabalhos existentes para utilizar a sintaxe de **Cópia de segurança para URL** . Armazenamento externo normalmente deve ser suficiente extremidade desde a localização de base de dados de produção para impedir que um única falhas que poderão ter impacto ambas as localizações da base de dados externa e de produção. Ao selecionar a [replicação geo o Azure blobs](../storage/storage-redundancy.md), tem uma camada adicional de proteção eventualidade um pode afetar a região toda.
- **Arquivo de cópia de segurança**: o serviço de armazenamento de Blobs do Azure oferece uma melhor alternativa para a opção de banda frequentemente utilizados para arquivar cópias de segurança. Armazenamento de banda pode necessitar de transporte física para uma funcionalidade externa e medidas para proteger os elementos multimédia. Armazenar as cópias de segurança no armazenamento de Blobs do Azure fornece uma instantâneas, altamente disponível, e um duradouros arquivar opção.
- **Geridos hardware**: não existe nenhuma gerais de gestão do hardware com os serviços do Azure. Serviços de Azure gerir o hardware e fornecem geo replicação para protecção contra falhas de hardware e redundância.
- **Armazenamento ilimitado**: ao ativar uma cópia de segurança direta blobs do Azure, tem acesso ao armazenamento praticamente ilimitado. Em alternativa, efetuar cópias de segurança até um disco Azure máquina virtual tem com base no computador tamanho de limites. Existe um limite para o número de discos que pode anexar a uma máquina virtual Azure para cópias de segurança. Este limite é 16 discos para uma instância muito grande e menos de instâncias mais pequenas.
- **Disponibilidade de cópia de segurança**: cópias de segurança armazenadas no Azure blobs estão disponíveis a partir de qualquer lugar e em qualquer altura e facilmente pode ser acedidas por restaura uma SQL Server no local ou noutro SQL Server em execução num Azure Máquina Virtual, sem necessidade de base de dados anexar/desanexar ou transferir e como anexar o VHD.
- **Custo**: pagar apenas para o serviço utilizado. Pode ser rentável como uma opção de arquivo de cópia de segurança e externo. Consulte a [Calculadora comparar Azure](http://go.microsoft.com/fwlink/?LinkId=277060 "Calculadora preços")e o [artigo Azure preços](http://go.microsoft.com/fwlink/?LinkId=277059 "preços artigo") para obter mais informações.
- **Instantâneo de armazenamento**: quando os ficheiros de base de dados são armazenados numa BLOBs do Azure e estiver a utilizar o SQL Server 2016, pode utilizar [um instantâneo de ficheiro cópia de segurança](http://msdn.microsoft.com/library/mt169363.aspx) para efetuar cópias de segurança quase instantâneas e restaura incrivelmente rápida.

Para obter mais detalhes, consulte o artigo [cópia de segurança do SQL Server e restaurar com o serviço de armazenamento de Blobs do Azure](http://go.microsoft.com/fwlink/?LinkId=271617).

As duas secções seguintes apresentam o serviço de armazenamento de Blobs do Azure, incluindo os componentes do SQL Server necessários. É importante compreender os componentes e os respetivos interação para utilizar a cópia de segurança e restaurar a partir do serviço de armazenamento de Blobs do Azure com êxito.

## <a name="azure-blob-storage-service-components"></a>Componentes de serviço de armazenamento de Blobs do Azure

Os seguintes componentes Azure são utilizados quando cópias de segurança para o serviço de armazenamento de Blobs do Azure.

| Componente               | Descrição                          |
|---------------------|-------------------------------|
| **Conta de armazenamento** | A conta de armazenamento é o ponto de partida para todos os serviços de armazenamento. Para aceder a um serviço de armazenamento de Blobs do Azure, crie primeiro uma conta de armazenamento do Windows Azure. Para mais informações sobre o serviço de armazenamento de Blobs do Azure, consulte o artigo [como utilizar o serviço de armazenamento de Blobs do Azure](https://azure.microsoft.com/develop/net/how-to-guides/blob-storage/) |
| **Contentor** | Um contentor fornece um agrupamento de um conjunto de blobs e pode armazenar um número ilimitado de Blobs. Para escrever um SQL Server cópia de segurança para um serviço de Blobs do Azure, tem de ter, pelo menos, o contentor de raiz criado. |
| **Blob** | Um ficheiro de qualquer tipo e tamanho. BLOBs são endereçáveis utilizando o seguinte formato de URL: **https://[storage account].blob.core.windows.net/[container]/[blob]**. Para mais informações sobre Blobs de página, consulte [Noções sobre bloco e Blobs de página](http://msdn.microsoft.com/library/azure/ee691964.aspx) |

## <a name="sql-server-components"></a>Componentes do SQL Server

Os seguintes componentes do SQL Server são utilizados quando cópias de segurança para o serviço de armazenamento de Blobs do Azure.

| Componente               | Descrição                          |
|---------------------|-------------------------------|
| **URL** | Um URL especifica um recurso URI (Uniform Identifier) para um ficheiro de cópia de segurança exclusivo. O URL é utilizado para fornecer a localização e o nome do ficheiro de cópia de segurança do SQL Server. O URL têm de apontar para um blob real, não apenas um contentor. Se o blob não existir, é criada. Se um blob existente for especificado, falha de cópia de segurança, a menos que a > opção com um formato especificada. A seguinte é um exemplo do URL que especificou no comando de cópia de segurança: **http[s]://[storageaccount].blob.core.windows.net/[container]/[FILENAME.bak]**. HTTPS é recomendado, mas não é necessário. |
| **Credenciais** | As informações que são necessário para ligar e autenticar ao serviço de armazenamento de Blobs do Azure são armazenadas como uma credencial.  Ordem para SQL Server escrever cópias de segurança para um BLOBs do Azure ou restaurar a partir dos mesmos, tem de ser criada credenciais do SQL Server. Para mais informações, consulte o artigo [Credenciais do SQL Server](https://msdn.microsoft.com/library/ms189522.aspx). |

> [AZURE.NOTE] Se optar por copiar e carregar um ficheiro de cópia de segurança para o serviço de armazenamento de Blobs do Azure, tem de utilizar um tipo de BLOBs página como opção de armazenamento se está a planear utilizar este ficheiro para operações de restaurar. RESTAURAR a partir de um tipo de Blobs do bloco irá falhar com um erro.

## <a name="next-steps"></a>Próximos passos

1. Crie uma conta Azure se ainda não tiver uma. Se está a avaliar Azure, considere a [versão de avaliação gratuita](https://azure.microsoft.com/free/).

1. Em seguida, aceda através de um dos seguintes tutoriais que orientá-lo na criação de uma conta de armazenamento e executar uma operação de restauro.

    - **SQL Server 2014**: [Tutorial: cópia de segurança do SQL Server 2014 e restaurar a Microsoft Azure Blob serviço de armazenamento](https://msdn.microsoft.com/library/jj720558\(v=sql.120\).aspx).
    - **SQL Server 2016**: [Tutorial: utilizar o serviço de armazenamento de Blobs do Microsoft Azure com bases de dados do SQL Server 2016](https://msdn.microsoft.com/library/dn466438.aspx)

1. Reveja a documentação adicional, começando com [cópia de segurança do SQL Server e restaurar com o serviço de armazenamento de Blobs do Microsoft Azure](https://msdn.microsoft.com/library/jj919148.aspx).

Se tiver algum problema, reveja o tópico de [Cópia de segurança do SQL Server para URL melhores práticas e resolução de problemas](https://msdn.microsoft.com/library/jj919149.aspx).

Para outras SQL Server fazer cópia de segurança e restaurar as opções, consulte o artigo [cópia de segurança e restauro para SQL Server em máquinas virtuais do Azure](../virtual-machines/virtual-machines-windows-sql-backup-recovery.md).
