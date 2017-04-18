<properties
    pageTitle="Fazer cópia de segurança e restaurar para SQL Server | Microsoft Azure"
    description="Descreve considerações de cópia de segurança e restauro para bases de dados do SQL Server em máquinas virtuais do Azure de executar."
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="rothja"
    manager="jhubbard"
    editor=""
    tags="azure-resource-management" />

<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="08/19/2016"
    ms.author="jroth" />

# <a name="backup-and-restore-for-sql-server-in-azure-virtual-machines"></a>Cópia de segurança e restauro para SQL Server em máquinas virtuais do Azure

## <a name="overview"></a>Descrição geral

Criar cópias de segurança dos dados nas bases de dados do SQL Server são uma parte importante a estratégia no proteger contra a perda de dados devido a erros de aplicação ou utilizador. Isto é igualmente verdadeiro para SQL Server em execução no Azure máquinas virtuais (VMs).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Para o SQL Server em execução no Azure VMs, pode utilizar nativa cópia de segurança e restaurar técnicas com discos ligados para o destino dos ficheiros de cópia de segurança. No entanto, existe um limite para o número de discos que pode anexar a uma máquina de virtual Azure, com base no [tamanho da máquina virtual](virtual-machines-linux-sizes.md). Também existe gerais da gestão de discos a ter em conta.

A partir do SQL Server 2014, pode criar cópias de segurança e restaurar ao armazenamento Blob do Microsoft Azure. SQL Server 2016 também fornece melhoramentos para esta opção. Além disso, para os ficheiros de base de dados armazenados no armazenamento Blob do Microsoft Azure, SQL Server 2016 fornece uma opção para quase instantâneas cópias de segurança e para restaura rápida utilização de instantâneos Azure. Este artigo fornece uma descrição geral das seguintes opções e obter informações adicionais podem ser encontradas em [cópia de segurança do SQL Server e restaurar com o serviço de armazenamento de Blobs do Microsoft Azure](https://msdn.microsoft.com/library/jj919148.aspx).

>[AZURE.NOTE] Para um debate das opções para cópias de segurança muito grandes bases de dados, consulte o artigo [Multi terabytes SQL Server base de dados de cópia de segurança estratégias para máquinas virtuais do Azure](http://blogs.msdn.com/b/igorpag/archive/2015/07/28/multi-terabyte-sql-server-database-backup-strategies-for-azure-virtual-machines.aspx).

As secções abaixo incluem informações específicas sobre as diferentes versões do SQL Server suportada numa máquina virtual Azure.

## <a name="sql-server-virtual-machines"></a>SQL Server Virtual máquinas

Quando a instância do SQL Server está em execução numa máquina de Virtual do Azure, os ficheiros de base de dados já residem nos dados discos no Azure. Estes discos encontram-se na armazenamento de Blobs do Azure. Por isso, os motivos para cópias de segurança da base de dados e as abordagens demorar um pouco alterar. Considere o seguinte: 

- Já não necessitar de efetuar cópias de segurança da base de dados para fornecer protecção contra falhas de hardware ou multimédia porque o Microsoft Azure fornece esta protecção como parte do serviço do Microsoft Azure.

- Ainda precisa de efetuar cópias de segurança da base de dados para fornecer proteção contra erros de utilizador ou, para fins de arquivo, motivos regulamentação ou efeitos administrativos.

- Pode armazenar o ficheiro de cópia de segurança diretamente no Azure. Para obter mais informações, consulte as seguintes secções fornecem orientação para as diferentes versões do SQL Server.

## <a name="sql-server-2016"></a>SQL Server 2016

Microsoft SQL Server 2016 suporta a [cópia de segurança e restauro com blobs do Azure](https://msdn.microsoft.com/library/jj919148.aspx) das funcionalidades encontradas no SQL Server 2014. Mas também inclui as seguintes melhorias:

| Melhoramento de 2016               | Detalhes                          |
|---------------------|-------------------------------|
| **Repartição**              | Quando cópias de segurança ao armazenamento blob do Microsoft Azure, SQL Server 2016 suporta a cópia de segurança para vários blobs para ativar cópias de segurança grandes bases de dados, até um máximo de 12.8 TB.      |
| **Instantâneo cópia de segurança**                | Através da utilização de instantâneos Azure, o ficheiro SQL Server instantâneo cópia de segurança fornece quase instantâneas cópias de segurança e restaura rápida para ficheiros de base de dados armazenados utilizando o serviço de armazenamento de Blobs do Azure. Esta funcionalidade permite-lhe simplificar a cópia de segurança e restaurar políticas. Instantâneo de ficheiro de cópia de segurança também suporta ponto restauro de tempo. Para mais informações, consulte o artigo [Instantâneo cópias de segurança para os ficheiros de base de dados no Azure](https://msdn.microsoft.com/library/mt169363%28v=sql.130%29.aspx).   |
| **Gerido agendamento de cópia de segurança**            | SQL Server gerido cópia de segurança para Azure suporta agora agendas personalizadas. Para mais informações, consulte o artigo [SQL Server gerido cópia de segurança do Microsoft Azure](https://msdn.microsoft.com/library/dn449496.aspx).   |

Para obter um tutorial das capacidades do SQL Server 2016 quando utilizar o armazenamento de Blobs do Azure, consulte o artigo [Tutorial: utilizar o serviço de armazenamento de Blobs do Microsoft Azure com bases de dados do SQL Server 2016](https://msdn.microsoft.com/library/dn466438.aspx).

## <a name="sql-server-2014"></a>SQL Server 2014

SQL Server 2014 inclui os seguintes melhoramentos:

1. **Cópia de segurança e restauro para Azure**:

 - *Cópia de segurança do SQL Server para URL* tem agora suporte no SQL Server Management Studio. A opção para criar cópias de segurança Azure está agora disponível quando utiliza a tarefa de cópia de segurança ou restaurar ou o Assistente de plano de manutenção no SQL Server Management Studio. Para mais informações, consulte o artigo [Cópia de segurança do SQL Server para URL](https://msdn.microsoft.com/library/jj919148%28v=sql.120%29.aspx).
 - *SQL Server gerido cópia de segurança para Azure* tem nova funcionalidade que permite a gestão de cópia de segurança automatizado. Este é especialmente útil para automatizar a gestão de cópia de segurança para instâncias do SQL Server 2014 em execução numa máquina de Azure. Para mais informações, consulte o artigo [SQL Server gerido cópia de segurança do Microsoft Azure](https://msdn.microsoft.com/library/dn449496%28v=sql.120%29.aspx).
 - *Cópia de segurança automatizado* fornece automatização adicional para activar automaticamente o *SQL Server gerido cópia de segurança para Azure* em todas as bases de dados novos e existentes para uma VM de servidor do SQL no Azure. Para mais informações, consulte o artigo [Cópia de segurança automatizado para SQL Server em máquinas virtuais do Azure](virtual-machines-windows-sql-automated-backup.md).
 - Para obter uma descrição geral de todas as opções para cópia de segurança do SQL Server 2014 para Azure, consulte o artigo [cópia de segurança do SQL Server e restaurar com o serviço de armazenamento de Blobs do Microsoft Azure](https://msdn.microsoft.com/library/jj919148%28v=sql.120%29.aspx).

1. **Encriptação**: SQL Server 2014 suporta a encriptação de dados ao criar uma cópia de segurança. Suporta vários algoritmos de encriptação e a utilização osf um certificado ou chave assimétrico. Para mais informações, consulte o artigo [Encriptação de cópia de segurança](https://msdn.microsoft.com/library/dn449489%28v=sql.120%29.aspx).

## <a name="sql-server-2012"></a>SQL Server 2012

Para obter informações detalhadas sobre a cópia de segurança do SQL Server e restaurar no SQL Server 2012, consulte o artigo [cópia de segurança e restauro de servidor de bases de dados SQL (SQL Server 2012)](https://msdn.microsoft.com/library/ms187048%28v=sql.110%29.aspx).

Iniciar no SQL Server 2012 SP1 Cumulative Update 2, pode criar até de segurança e restaurar a partir do serviço de armazenamento de Blobs do Azure. Este melhoramento pode ser utilizado para agregar bases de dados do SQL Server em SQL Server em execução numa máquina de Virtual Azure ou uma instância no local. Para mais informações, consulte o artigo [cópia de segurança do SQL Server e restaurar com o serviço de armazenamento de Blobs do Azure](https://msdn.microsoft.com/library/jj919148%28v=sql.110%29.aspx).

Algumas das vantagens de utilizar o serviço de armazenamento de Blobs do Azure incluem a capacidade para ignorar o limite de 16 disco para discos ligados, facilidade de gestão, a disponibilidade direta do ficheiro de cópia de segurança para outra instância da instância do SQL Server em execução numa máquina virtual Azure ou uma instância no local para fins de recuperação de falhas ou de migração. Para obter uma lista completa dos benefícios para utilizar um serviço de armazenamento de Blobs do Azure para cópias de segurança do SQL Server, consulte a secção de *benefícios* em [cópia de segurança do SQL Server e restaurar com o serviço de armazenamento de Blobs do Azure](https://msdn.microsoft.com/library/jj919148%28v=sql.110%29.aspx).

Para recomendações de melhores práticas e informações de resolução de problemas, consulte o artigo [cópia de segurança e restaurar melhores práticas (serviço de armazenamento de Blobs do Azure)](https://msdn.microsoft.com/library/jj919149%28v=sql.110%29.aspx).

## <a name="sql-server-2008"></a>SQL Server 2008

Para cópia de segurança do SQL Server e restaurar no SQL Server 2008 R2, consulte o artigo [fazer cópias de segurança e restaurar bases de dados do SQL Server (SQL Server 2008 R2)](https://msdn.microsoft.com/library/ms187048%28v=sql.105%29.aspx).

Para cópia de segurança do SQL Server e restaurar no SQL Server 2008, consulte o artigo [fazer cópias de segurança e restaurar bases de dados do SQL Server (SQL Server 2008)](https://msdn.microsoft.com/library/ms187048%28v=sql.100%29.aspx).

## <a name="next-steps"></a>Próximos passos

Se estiver a planear a implementação do SQL Server em máquinas uma VM Azure, pode encontrar orientações aprovisionamento no tutorial seguinte: [aprovisionamento uma máquina de Virtual do SQL Server no Azure com o Gestor de recursos do Azure](virtual-machines-windows-portal-sql-server-provision.md).

Apesar de cópia de segurança e restauro podem ser utilizados para migrar os seus dados, existem potencialmente mais fácil caminhos de migração de dados para o SQL Server numa VM Azure. Para um debate cheio de opções de migração e recomendações, consulte o artigo [migrar uma base de dados para o SQL Server numa VM Azure](virtual-machines-windows-migrate-sql.md).

Reveja outros [recursos para executar o SQL Server em máquinas virtuais do Azure](virtual-machines-windows-sql-server-iaas-overview.md).
