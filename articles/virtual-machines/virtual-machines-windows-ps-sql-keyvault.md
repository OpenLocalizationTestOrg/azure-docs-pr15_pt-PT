<properties
    pageTitle="Configurar integração do Azure cofre chave para o SQL Server no Azure VMs (Gestor de recurso)"
    description="Saiba como para automatizar a configuração de encriptação do SQL Server para utilização com o Azure chave cofre. Este tópico explica como utilizar o Azure chave cofre integração com máquinas virtuais de SQL Server criadas com o Gestor de recursos."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="rothja"
    manager="jhubbard"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="10/25/2016"
    ms.author="jroth"/>

# <a name="configure-azure-key-vault-integration-for-sql-server-on-azure-vms-resource-manager"></a>Configurar integração do Azure cofre chave para o SQL Server no Azure VMs (Gestor de recurso)

> [AZURE.SELECTOR]
- [Gestor de recursos](virtual-machines-windows-ps-sql-keyvault.md)
- [Clássico](virtual-machines-windows-classic-ps-sql-keyvault.md)

## <a name="overview"></a>Descrição geral
Existem várias funcionalidades de encriptação de SQL Server, como a [encriptação de dados transparente (TDE)](https://msdn.microsoft.com/library/bb934049.aspx), [coluna encriptação nível (limpar)](https://msdn.microsoft.com/library/ms173744.aspx)e [encriptação de cópia de segurança](https://msdn.microsoft.com/library/dn449489.aspx). Estes formulários de encriptação necessitam que gerir e armazenar as chaves de criptografia que utiliza para encriptação. O serviço do Azure chave cofre (AKV) foi concebido para melhorar a segurança e gestão destas teclas numa localização segura e altamente disponível. O [SQL Server Connector](http://www.microsoft.com/download/details.aspx?id=45344) permite SQL Server para utilizar estas teclas do Azure chave cofre.

Se de que está a executar do SQL Server com no local máquinas, existem passos [que pode seguir para aceder ao Azure chave cofre a partir do seu computador do SQL Server no local](https://msdn.microsoft.com/library/dn198405.aspx). Mas, para o SQL Server no Azure VMs, pode poupar tempo ao utilizar a funcionalidade de *Integração do Azure chave cofre* .

Quando esta funcionalidade está ativada,-lo automaticamente instala o SQL Server Connector, configura o fornecedor EKM para aceder ao Cofre de chave do Azure e cria a credencial a permitem-lhe aceder ao seu cofre. Se os passos na documentação anteriormente mencionadas no local que visualizou, pode ver que esta funcionalidade automatiza os passos 2 e 3. É a única coisa que ainda precisa para fazer manualmente criar as teclas e chave cofre. A partir daí, o todo o programa de configuração da sua VM SQL é automática. Assim que esta funcionalidade terminar este programa de configuração, pode executar declarações de T-SQL para começar a encriptar o seu bases de dados ou cópias de segurança como faria normalmente.

[AZURE.INCLUDE [AKV Integration Prepare](../../includes/virtual-machines-sql-server-akv-prepare.md)]

## <a name="enabling-and-configuring-akv-integration"></a>Activar e configurar integração de AKV
Pode ativar a integração do AKV durante o aprovisionamento ou configurá-lo para VMs existentes.

### <a name="new-vms"></a>Novos VMs
Se o aprovisionamento de uma nova máquina de virtual do SQL Server com o Gestor de recursos, o portal do Azure fornece um passo para ativar a integração do Azure chave cofre. A funcionalidade de Azure chave Cofre está disponível apenas para o Enterprise, programador e avaliação edições do SQL Server.

![Integração do cofre chave Azure SQL](./media/virtual-machines-windows-ps-sql-keyvault/azure-sql-arm-akv.png)

Para obter instruções detalhadas sobre aprovisionamento, consulte o artigo [aprovisionar uma máquina de virtual do SQL Server no Portal do Azure](virtual-machines-windows-portal-sql-server-provision.md).

### <a name="existing-vms"></a>VMs existentes
Para máquinas virtuais do SQL Server existentes, selecione o seu máquina de virtual do SQL Server. Em seguida, selecione a secção pá as **definições de** **configuração do SQL Server** .

![Integração de AKV SQL para VMs existentes](./media/virtual-machines-windows-ps-sql-keyvault/azure-sql-rm-akv-existing-vms.png)

No pá **configuração do SQL Server** , clique no botão **Editar** na secção Integração da automatizado cofre chave.

![Configurar integração de AKV SQL para VMs existentes](./media/virtual-machines-windows-ps-sql-keyvault/azure-sql-rm-akv-configuration.png)

Quando tiver terminado, clique no botão **OK** na parte inferior da pá a **configuração do SQL Server** para guardar as alterações.

>[AZURE.NOTE] Também pode configurar integração de AKV através de um modelo. Para mais informações, consulte o artigo [Guia de introdução Azure modelo para a integração do Azure chave cofre](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vm-sql-existing-keyvault-update).

[AZURE.INCLUDE [AKV Integration Next Steps](../../includes/virtual-machines-sql-server-akv-next-steps.md)]
