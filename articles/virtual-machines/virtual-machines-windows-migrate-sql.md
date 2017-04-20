<properties
    pageTitle="Migrar uma base de dados do SQL Server para o SQL Server numa VM | Microsoft Azure"
    description="Obter informações sobre como migrar uma base de dados de utilizador local para o SQL Server numa máquina virtual Azure."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="sabotta"
    manager="jhubbard"
    editor=""
    tags="azure-service-management" />
<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/26/2016"
    ms.author="carlasab"/>


# <a name="migrate-a-sql-server-database-to-sql-server-in-an-azure-vm"></a>Migrar uma base de dados do SQL Server para o SQL Server numa VM Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]Modelo do Gestor de recursos.


Existem vários métodos para migrar uma base de dados de utilizador de SQL Server no local para o SQL Server numa VM Azure. Este artigo por breves instantes debater vários métodos, recomenda o melhor método para vários cenários e incluir uma [Iniciação](#azure-vm-deployment-wizard-tutorial) para o guiar através da utilização do Assistente de **Implementar uma base de dados do SQL Server para um Microsoft Azure VM** . 

O método utilizando o Assistente de **Implementar uma base de dados do SQL Server para um Microsoft Azure VM** descrito na [Iniciação](#azure-vm-deployment-wizard-tutorial) é apenas do modelo de implementação clássico. 

## <a name="what-are-the-primary-migration-methods"></a>Quais são os métodos de migração primária?

Os métodos de migração primárias são:

- Utilizar a implementar uma base de dados do SQL Server a um assistente do Microsoft Azure VM
- Efectuar cópia de segurança no local utilizando a compressão e copie manualmente o ficheiro de cópia de segurança para a máquina virtual Azure
- Efectuar uma cópia de segurança e restauro na máquina virtual Azure de URL do URL
- Desanexar e, em seguida, copie os ficheiros de dados e de registo para armazenamento de Azure blob e, em seguida, anexar ao SQL Server na Azure VM de URL
- Converter o computador físico no local para Hyper-V VHD, carregar para armazenamento de Azure Blob e, em seguida, implementar como nova VM utilizando carregá-las VHD
- Unidade de disco rígido de envio a utilizar o serviço de importação/exportação do Windows
- Se tiver uma implementação AlwaysOn no local, utilize o [Assistente de réplica Azure adicionar](virtual-machines-windows-classic-sql-onprem-availability.md) para criar uma réplica no Azure e, em seguida, a activação pós-falha, os utilizadores a apontar para a instância de base de dados Azure
- Utilizar [replicação transaccional](https://msdn.microsoft.com/library/ms151176.aspx) do SQL Server para configurar a instância de Azure SQL Server como um subscritor e, em seguida, desactivar a replicação, os utilizadores a apontar para a instância de base de dados Azure



## <a name="choosing-your-migration-method"></a>Escolher o método de migração

Para dados óptimo desempenho de transferência, migração de ficheiros de base de dados para a VM Azure utilizando um ficheiro de cópia de segurança comprimido é geralmente o melhor método. Este é o método que utiliza a [Implementar uma base de dados do SQL Server a um assistente do Microsoft Azure VM](#azure-vm-deployment-wizard-tutorial) . Este assistente é o método recomendado para migrar uma execução de base de dados de utilizador no local no SQL Server 2005 ou superior para o SQL Server 2014 ou superior, quando o ficheiro de cópia de segurança da base de dados comprimidos for inferior a 1 TB.

Para minimizar o tempo de inactividade durante o processo de migração da base de dados, utilize a opção AlwaysOn ou a opção de replicação transaccional.

Se não for possível utilizar métodos acima mencionados, migre manualmente a base de dados. Utilizando este método, será iniciado normalmente com uma cópia de segurança de base de dados, seguida de uma cópia da base de dados de cópia de segurança em Azure e, em seguida, efectuar o restauro da base de dados. Pode também copiar os próprios ficheiros de base de dados para Azure e, em seguida, anexá-las. Existem vários métodos que pode efectuar este processo manual de migração de uma base de dados para uma VM Azure.

> [AZURE.NOTE] Quando actualiza para o SQL Server 2014 ou SQL Server 2016 de versões anteriores do SQL Server, deverá considerar se forem necessárias alterações. Recomendamos que endereço dependências todas as funcionalidades não suportadas pela nova versão do SQL Server como parte do seu projecto de migração. Para mais informações sobre as edições suportadas e cenários, consulte [actualizar para o SQL Server](https://msdn.microsoft.com/library/bb677622.aspx).

A tabela seguinte lista cada um dos métodos principais de migração e descreve quando a utilização de cada método é mais adequada.

| Método  | Versão da base de dados de origem  |  Versão de base de dados de destino | Restrição de tamanho de cópia de segurança de base de dados de origem  | Notas  |
|---|---|---|---|---|
| [Utilizar a implementar uma base de dados do SQL Server a um assistente do Microsoft Azure VM](#azure-vm-deployment-wizard-tutorial) | SQL Server 2005 ou superior | SQL Server 2014 ou superior | < 1 TB  | Método mais rápido e mais simples, utilização sempre que possível migrar para uma instância de SQL Server nova ou existente numa máquina virtual Azure | 
| [Utilizar o Assistente de réplica Azure](virtual-machines-windows-classic-sql-onprem-availability.md) | SQL Server 2012 ou superior | SQL Server 2012 ou superior | [Limite de armazenamento VM Azure](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) | Minimiza o período de indisponibilidade, utilizar quando tem uma implementação de local AlwaysOn |
| [Utilizar a replicação transaccional do SQL Server](https://msdn.microsoft.com/library/ms151176.aspx) | SQL Server 2005 ou superior | SQL Server 2005 ou superior | [Limite de armazenamento VM Azure](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) | Utilize quando for necessário minimizar o tempo de inactividade e não têm uma implementação de local AlwaysOn |
| [Efectuar cópia de segurança no local utilizando a compressão e copie manualmente o ficheiro de cópia de segurança para a máquina virtual Azure](#backup-to-file-and-copy-to-vm-and-restore) | SQL Server 2005 ou superior | SQL Server 2005 ou superior | [Limite de armazenamento VM Azure](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) | Utilizar apenas quando não é possível utilizar o assistente, por exemplo, quando a versão da base de dados de destino for inferior a CU2 do SQL Server 2012 SP1 ou o tamanho de cópia de segurança da base de dados é maior do que 1 TB (12.8 TB com SQL Server 2016) |
| [Efectuar uma cópia de segurança e restauro na máquina virtual Azure de URL do URL](#backup-to-url-and-restore) | CU2 do SQL Server 2012 SP1 ou superior | CU2 do SQL Server 2012 SP1 ou superior | < 12.8 TB de 2016 como servidor de SQL, caso contrário < 1 TB | Geralmente a utilização de [cópia de segurança para o URL](https://msdn.microsoft.com/library/dn435916.aspx) é equivalente no desempenho ao utilizar o assistente e não é bem isso tão fácil |
| [Desanexar e, em seguida, copie os ficheiros de dados e de registo para armazenamento de Azure blob e, em seguida, anexar ao SQL Server na máquina virtual Azure de URL](#detach-and-copy-to-url-and-attach-from-url) | SQL Server 2005 ou superior | SQL Server 2014 ou superior | [Limite de armazenamento VM Azure](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) | Utilize este método quando pretender [armazenar estes ficheiros utilizando o serviço de armazenamento Azure Blob](https://msdn.microsoft.com/library/dn385720.aspx) e anexá-las ao SQL Server executado na VM Azure, especialmente com bases de dados muito grandes |
| [Converter a máquina local VHDs Hyper-V, carregar para armazenamento de Azure Blob e, em seguida, implementar uma nova máquina virtual utilizando o VHD carregado](#convert-to-vm-and-upload-to-url-and-deploy-as-new-vm) | SQL Server 2005 ou superior | SQL Server 2005 ou superior | [Limite de armazenamento VM Azure](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) | Utilize quando [colocar a sua própria licença de SQL Server](../sql-database/sql-database-paas-vs-sql-server-iaas.md), quando migrar uma base de dados que vai executar uma versão mais antiga do SQL Server, ou durante a migração de utilizador e sistema de bases de dados em conjunto como parte da migração da base de dados dependente de outras bases de dados de utilizador e/ou bases de dados do sistema. |
| [Unidade de disco rígido de envio a utilizar o serviço de importação/exportação do Windows](#ship-hard-drive) | SQL Server 2005 ou superior | SQL Server 2005 ou superior | [Limite de armazenamento VM Azure](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/) | Utilizar o [Serviço de importação/exportação do Windows](../storage/storage-import-export-service.md) quando o método manual copy é demasiado lento, tal como com bases de dados muito grandes |

## <a name="azure-vm-deployment-wizard-tutorial"></a>Tutorial do Assistente de implementação de VM Azure

Utilizar o Assistente de **Implementar uma base de dados do SQL Server para um Microsoft Azure VM** no Microsoft SQL Server Management Studio para migrar um SQL Server 2005, SQL Server 2008, SQL Server 2008 R2, 2012 do SQL Server, SQL Server 2014 ou SQL Server 2016 utilizador no local da base de dados (até 1 TB) para SQL Server 2014 ou SQL Server 2016 numa máquina virtual Azure. Utilize este assistente para migrar uma base de dados do utilizador para uma máquina virtual Azure existente ou para uma VM Azure com o SQL Server criadas pelo assistente durante o processo de migração. Quando migrar uma base de dados para uma versão mais recente do SQL Server, a base de dados é automaticamente actualizado durante o processo.

O método é apenas do modelo de implementação clássico. 

### <a name="get-latest-version-of-the-deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard"></a>Obter uma versão mais recente da implementar uma base de dados do SQL Server a um assistente do Microsoft Azure VM

Utilize a versão mais recente do Microsoft SQL Server Management Studio para o SQL Server para garantir que tem a versão mais recente do Assistente de **Implementar uma base de dados do SQL Server para um Microsoft Azure VM** . A versão mais recente deste assistente incorpora as actualizações mais recentes para o portal clássico Azure e suporta as imagens Azure VM mais recentes na galeria (versões mais antigas do assistente poderão não funcionar). Para obter a versão mais recente do Microsoft SQL Server Management Studio para o SQL Server, [transferi-lo](http://go.microsoft.com/fwlink/?LinkId=616025) e instalá-lo num computador cliente com a conectividade à base de dados que pretende migrar e à internet.

### <a name="configure-the-existing-azure-virtual-machine-and-sql-server-instance-if-applicable"></a>Configurar o existente máquina virtual Azure e a instância do SQL Server (se aplicável)

Se estiver a migrar uma VM Azure existentes, são necessários os seguintes passos de configuração:

- Configure a VM Azure e a instância do SQL Server para activar a conectividade de outro computador, seguindo os passos para [ligar à instância do SQL Server VM de SSMS noutro computador](virtual-machines-windows-sql-connect.md). Apenas os SQL Server 2014 e SQL Server 2016 imagens na Galeria são suportadas se estiver a migrar utilizando o assistente.
- Configure um ponto final aberto para o serviço SQL Server nuvem placa o gateway de Microsoft Azure com porta privada do 11435. Esta porta é criada como parte do SQL Server 2014 ou SQL Server 2016 aprovisionamento um Azure de Microsoft VM. A placa de nuvem também cria uma regra de Firewall do Windows para permitir a suas receber ligações de TCP na porta de predefinição 11435. Este ponto final permite que o Assistente para utilizar o serviço de adaptador de nuvem para copiar os ficheiros de cópia de segurança da instância de local para a VM Azure. Para mais informações, consulte o [Adaptador de nuvem para o SQL Server](https://msdn.microsoft.com/library/dn169301.aspx).

    ![Criar o ponto final de placa de nuvem](./media/virtual-machines-windows-migrate-sql/cloud-adapter-endpoint.png)

### <a name="run-the-use-the-deploy-a-sql-server-database-to-a-microsoft-azure-vm-wizard"></a>Executar a utilização a implementar uma base de dados do SQL Server a um assistente do Microsoft Azure VM

1. Abrir o Microsoft SQL Server Management Studio para Microsoft SQL Server 2016 e ligue à instância do SQL Server que contém a base de dados do utilizador que migrar uma VM Azure.
2. Com o botão direito na base de dados que está a migrar, aponte para tarefas e, em seguida, clique em implementar um Azure de Microsoft VM.

    ![Iniciar Assistente](./media/virtual-machines-windows-migrate-sql/start-wizard.png)

3. Na página Introdução, clique em seguinte.
4. Na página Definições de origem, ligar à instância do SQL Server que contém a base de dados que deseja migrar para um VM Azure.
5. Especifique uma localização temporária para os ficheiros de cópia de segurança. Se ligar a um servidor remoto, tem de especificar uma unidade de rede.

    ![Definições de origem](./media/virtual-machines-windows-migrate-sql/source-settings.png)

6. Clique em seguinte.
7. Na página Microsoft Azure Sign-In, clique em iniciar sessão e início de sessão na sua conta Azure.
8. Seleccione a subscrição que pretende utilizar e clique em seguinte.

    ![Azure início de sessão no](./media/virtual-machines-windows-migrate-sql/azure-signin.png)

9. Na página Definições de implementação, pode especificar um novo ou um serviço de nuvem existente nome e o nome da Máquina Virtual:

 - Especifique um novo nome nome de serviço de nuvem e a Máquina Virtual para criar um novo serviço de nuvem com uma nova máquina virtual Azure utilizar uma imagem do SQL Server 2014 ou Galeria de 2016 do SQL Server.

     - Se especificar um novo nome de serviço em nuvem, especifique a conta de armazenamento que irá utilizar.

     - Se especificar um nome de serviço em nuvem existente, a conta de armazenamento será obtida e introduzida automaticamente.

 - Especifique um nome de serviço em nuvem existente e o novo nome da Máquina Virtual para criar uma nova máquina virtual Azure um serviço de nuvem existente. Especifique apenas um 2014 do SQL Server ou SQL Server 2016 a imagem na galeria.
 - Especifique um nome de serviço em nuvem existente e o nome da Máquina Virtual para utilizar uma máquina virtual Azure existente. Isto tem de ser uma imagem criada utilizando um 2014 do SQL Server ou SQL Server 2016 a imagem na galeria.

        ![Deployment Settings](./media/virtual-machines-windows-migrate-sql/deployment-settings.png)

10. Clique em definições
  - Se tiver especificado um nome de serviço em nuvem existente e o nome da Máquina Virtual, ser-lhe-à pedido para fornecer o nome de utilizador e palavra-passe.

        ![Definições da máquina Azure](./media/virtual-machines-windows-migrate-sql/azure-machine-settings.png)

    - Se tiver especificado um novo nome de Máquina Virtual, será solicitado para seleccionar uma imagem da lista de imagens da Galeria e forneça as seguintes informações:
      - Imagem – seleccione apenas 2014 do SQL Server ou SQL Server 2016
        - Nome de utilizador
        - Nova palavra-passe
        - Confirmar palavra-passe
        - Localização
        - Tamanho.
    - Além disso, clique para aceitar o certificado automática gerado para este novo Azure Máquina Virtual da Microsoft e, em seguida, clique em OK.

    ![Azure novas definições de máquina](./media/virtual-machines-windows-migrate-sql/azure-new-machine-settings.png)

11. Especifique o nome de base de dados de destino, se diferente do nome de base de dados de origem. Se a base de dados de destino já existir, o sistema irá automaticamente incrementar o nome de base de dados em vez de substituir a base de dados existente.
12. Clique em seguinte e, em seguida, clique em ' Concluir '.

    ![Resultados](./media/virtual-machines-windows-migrate-sql/results.png)

13. Quando concluir o assistente, ligue à máquina virtual e certifique-se de que a base de dados foram migrado.
14. Se tiver criado uma nova máquina virtual, configure a máquina virtual Azure e instância do SQL Server seguindo os passos para [ligar à instância do SQL Server VM de SSMS noutro computador](virtual-machines-windows-sql-connect.md).

## <a name="backup-to-file-and-copy-to-vm-and-restore"></a>Cópia de segurança para o ficheiro e copiar para a VM e restauro

Utilize este método quando não é possível utilizar a implementar uma base de dados do SQL Server a um assistente do Microsoft Azure VM porque está a migrar para uma versão do SQL Server antes de 2014 do SQL Server ou o ficheiro de cópia de segurança é maior do que 1 TB. Se o ficheiro de cópia de segurança for superior a 1 TB, tem de o repartições porque o tamanho máximo de um disco VM é 1 TB. Utilize os seguintes passos gerais para migrar uma base de dados de utilizador utilizando este método manual:

1.  Efectue uma cópia de segurança da base de dados para uma localização no local.
2.  Criar ou carregar uma máquina virtual com a versão do SQL Server pretendido.
3.  O programa de configuração com base nos seus requisitos de conectividade. Consulte a [ligar a uma máquina de Virtual do servidor SQL no Azure (Gestor de recursos)](virtual-machines-windows-sql-connect.md).
4.  Copie os ficheiros de cópia de segurança para a VM utilizando o ambiente de trabalho remoto, o Explorador do Windows ou o comando de cópia de uma linha de comandos.

## <a name="backup-to-url-and-restore"></a>Cópia de segurança e restauro de URL

Utilize o método de [cópia de segurança para o URL](https://msdn.microsoft.com/library/dn435916.aspx) quando não é possível utilizar a implementar uma base de dados do SQL Server a um assistente do Microsoft Azure VM porque o ficheiro de cópia de segurança é maior do que 1 TB e estiver a migrar de e para o SQL Server 2016. Para bases de dados mais pequenas do que 1 TB ou executar uma versão do SQL Server antes de 2016 do SQL Server, recomenda-se a utilização do assistente. Com SQL Server de 2016, conjuntos de cópia de segurança repartidos (striped) são suportados, são recomendados para desempenho e necessárias para ultrapassar os limites de tamanho por blob. Para bases de dados muito grandes, recomenda-se a utilização do [Serviço de importação/exportação do Windows](../storage/storage-import-export-service.md) .

## <a name="detach-and-copy-to-url-and-attach-from-url"></a>Desanexar e copiar para o URL e anexar de URL

Utilize este método quando pretender [armazenar estes ficheiros utilizando o serviço de armazenamento Azure Blob](https://msdn.microsoft.com/library/dn385720.aspx) e anexá-las ao SQL Server executado na VM Azure, especialmente com bases de dados muito grandes. Utilize os seguintes passos gerais para migrar uma base de dados de utilizador utilizando este método manual:

1.  Separar os ficheiros de base de dados da instância de base de dados local.
2.  Copie os ficheiros de base de dados separada para armazenamento de Azure blob utilizando o [utilitário da linha de comandos AZCopy](../storage/storage-use-azcopy.md).
3.  Anexe os ficheiros de base de dados do Azure URL a instância do SQL Server na Azure VM.

## <a name="convert-to-vm-and-upload-to-url-and-deploy-as-new-vm"></a>Converter para VM e carregar para o URL e implementar como novo VM

Utilize este método para migrar todos os sistema e de utilizador bases de dados de uma instância de SQL Server no local a máquina virtual Azure. Utilize os seguintes passos gerais para migrar uma instância de SQL Server toda utilizando este método manual:

1.  Converta física ou máquinas virtuais para Hyper-V VHDs utilizando o [Conversor de Máquina Virtual da Microsoft](http://technet.microsoft.com/library/dn873998.aspx).
2.  Carregar ficheiros VHD para armazenamento de Azure utilizando o [cmdlet Add-AzureVHD](https://msdn.microsoft.com/library/windowsazure/dn495173.aspx).
3.  Implemente uma nova máquina virtual utilizando o VHD carregado.

> [AZURE.NOTE] Para migrar uma aplicação completa, considere utilizar [Recuperação de Site Azure](../site-recovery/site-recovery-overview.md)].

## <a name="ship-hard-drive"></a>Unidade de disco rígido do navio

Utilize o [método do serviço de importação/exportação do Windows](../storage/storage-import-export-service.md) para transferir grandes quantidades de dados de ficheiros para armazenamento de Azure Blob em situações em que a enviar através da rede não viáveis ou proibitivamente dispendioso. Com este serviço, envia um ou mais unidades de disco rígido que contém os dados para um centro de dados Azure, onde os dados serão carregados para a conta de armazenamento.

## <a name="next-steps"></a>Passos seguintes

Para mais informações sobre como executar o SQL Server no Azure Virtual máquinas, consulte [SQL Server em máquinas virtuais de Azure panorâmica](virtual-machines-windows-sql-server-iaas-overview.md).

Para obter instruções sobre como criar uma Máquina Virtual do Azure SQL Server a partir de uma imagem capturada, consulte [Sugestões & truques no 'clonagem «máquinas virtuais de Azure SQL de imagens capturadas](https://blogs.msdn.microsoft.com/psssql/2016/07/06/tips-tricks-on-cloning-azure-sql-virtual-machines-from-captured-images/) no blogue engenheiros de servidor de SQL de CSS.
