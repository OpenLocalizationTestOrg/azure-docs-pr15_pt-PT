<properties
    pageTitle="SQL Server em máquinas virtuais Azure FAQ | Microsoft Azure"
    description="Este artigo fornece respostas às perguntas mais frequentes sobre a execução do SQL Server no Azure VMs."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="v-shysun"
    manager="felixwu"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="09/13/2016"
    ms.author="v-shysun"/>

# <a name="sql-server-on-azure-virtual-machines-faq"></a>SQL Server em máquinas virtuais Azure perguntas mais frequentes

Este tópico fornece respostas a algumas perguntas mais comuns sobre a execução do [SQL Server em máquinas virtuais do Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/).

[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

1. **Como posso criar uma máquina virtual Azure com o SQL Server?**

    Existem duas formas para o fazer. É a solução mais fácil criar uma máquina de Virtual que inclui o SQL Server. Para obter um tutorial sobre a inscrição no Azure e criar uma VM SQL a partir do portal, consulte o artigo [aprovisionar uma máquina de virtual do SQL Server no Portal do Azure](virtual-machines-windows-portal-sql-server-provision.md). Também tem a opção de instalar o SQL Server uma VM e reutilizar uma licença no local com [Licença mobilidade através do Software Assurance no Azure](https://azure.microsoft.com/pricing/license-mobility/)manualmente.

1. **O que é a diferença entre SQL VMs e o serviço de base de dados SQL?**

    Em termos conceptuais, com o SQL Server num computador virtual Azure não que difere da execução do SQL Server num centro de dados remoto. Em contrapartida, [Base de dados SQL](../sql-database/sql-database-technical-overview.md) oferece da base de dados-como-a-service. Base de dados SQL, não possui acesso às máquinas que aloja as bases de dados. Para uma comparação completa, consulte o artigo [Escolha uma opção do SQL Server nuvem: base de dados SQL Azure (PaaS) ou do SQL Server no Azure VMs (IaaS)](../sql-database/sql-database-paas-vs-sql-server-iaas.md).

1. **Como migrar minha base de dados do SQL Server no local para a nuvem?**

    Primeiro, crie uma máquina virtual Azure com uma instância do SQL Server. Em seguida, migre as bases de dados no local para essa instância. Para estratégias de migração de dados, consulte o artigo [migrar uma base de dados do SQL Server para SQL Server em máquinas uma VM Azure](virtual-machines-windows-migrate-sql.md).

2. **Pode alterar as funcionalidades instaladas ou instalar uma segunda instância do SQL Server na mesma VM?**

    Sim. O suporte de instalação do SQL Server encontra-se numa pasta na unidade **C** . Execute **Setup.exe** a partir dessa localização para adicionar novas instâncias do SQL Server ou para alterar outras funcionalidades instaladas do SQL Server no computador.

3. **Como atualizar para uma nova versão/edição do SQL Server numa VM Azure?**

    Atualmente, não existe nenhuma atualização no local para o SQL Server em execução numa VM Azure. Criar uma nova máquina virtual Azure com a versão/edição pretendida do SQL Server e, em seguida, migra as bases de dados para o novo servidor utilizando o padrão [técnicas de migração de dados](virtual-machines-windows-migrate-sql.md).

4. **Como instalar a minha cópia licenciada do SQL Server numa VM Azure?**

    Copiar o suporte de instalação do SQL Server para a VM de servidor do Windows e, em seguida, instalar o SQL Server na VM. Para o licenciamento motivos, tem de ter [Licença mobilidade através do Software Assurance no Azure](https://azure.microsoft.com/pricing/license-mobility/).

5. **Dispõe de pagar os custos SQL de uma VM se apenas está a ser utilizado para o modo de espera/activação pós-falha?**

    Se estiver a criar a VM SQL através da galeria, em seguida, tem de ter uma licença separada para a suspensão VM de SQL e o preço é o mesmo. Se instalar o SQL Server manualmente numa máquina virtual com [Mobilidade licença](https://azure.microsoft.com/pricing/license-mobility/), terá a opção para ter uma gratuita passiva SQL instância para activação pós-falha. Reveja as restrições e requisitos.

6. **Como são actualizações e service packs aplicados numa VM do SQL Server?**

    Máquinas virtuais dão-lhe controlo sobre o computador anfitrião, incluindo quando e como aplicar atualizações. Sistema operativo, pode aplicar manualmente atualizações do windows ou pode ativar a um serviço de agendamento denominado de [Correcção automática](virtual-machines-windows-classic-sql-automated-patching.md). Automatizado Patching instalações atualizações marcadas como importantes, incluindo actualizações do SQL Server nessa categoria. Outras atualizações opcionais para o SQL Server tem de estar instaladas manualmente.

7. **É possível configurar configurações não mostradas na Galeria de máquina virtual (por exemplo Windows 2008 R2 + SQL Server 2012)?**

    Não. Para imagens da Galeria de máquina virtual que incluem o SQL Server, tem de selecionar uma das imagens do fornecido.

9. **Como posso instalar ferramentas de dados do SQL no meu VM Azure?**

    Transfira e instale as ferramentas de dados do SQL de [Ferramentas de dados do Microsoft SQL Server - Business Intelligence para Visual Studio 2013 ](https://www.microsoft.com/en-us/download/details.aspx?id=42313).

## <a name="resources"></a>Recursos

Para obter uma descrição geral do SQL Server em máquinas virtuais do Azure, veja o vídeo [Azure VM é a melhor plataforma para o SQL Server 2016](https://channel9.msdn.com/Events/DataDriven/SQLServer2016/Azure-VM-is-the-best-platform-for-SQL-Server-2016). Também pode obter um bom de introdução no tópico, [SQL Server em máquinas virtuais do Azure panorâmica](virtual-machines-windows-sql-server-iaas-overview.md).

Outros recursos incluem:

- [Aprovisionar uma máquina de virtual do SQL Server no Portal do Azure](virtual-machines-windows-portal-sql-server-provision.md)
- [Migrar uma base de dados para o SQL Server numa VM Azure](virtual-machines-windows-migrate-sql.md)
- [Disponibilidade de alta e recuperação de falhas para SQL Server em máquinas virtuais do Azure](virtual-machines-windows-sql-high-availability-dr.md)
- [Práticas recomendadas do desempenho para SQL Server em máquinas virtuais do Azure](virtual-machines-windows-sql-performance.md)
- [Padrões de aplicação e estratégias de desenvolvimento para SQL Server em máquinas virtuais do Azure](virtual-machines-windows-sql-server-app-patterns-dev-strategies.md)
