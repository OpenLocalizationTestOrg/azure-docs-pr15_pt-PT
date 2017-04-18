<properties
    pageTitle="Aprovisionar uma máquina de Virtual do SQL Server | Microsoft Azure"
    description="Criar e ligue a uma máquina de virtual do SQL Server no Azure através do Portal. Neste tutorial, utiliza o modo de Gestor de recursos."
    services="virtual-machines-windows"
    documentationCenter="na"
    authors="rothja"
    editor=""
    manager="jhubbard"
    tags="azure-resource-manager" />
<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="09/21/2016"
    ms.author="jroth" />

# <a name="provision-a-sql-server-virtual-machine-in-the-azure-portal"></a>Aprovisionar uma máquina de virtual do SQL Server no Portal do Azure

> [AZURE.SELECTOR]
- [Portal](virtual-machines-windows-portal-sql-server-provision.md)
- [PowerShell](virtual-machines-windows-ps-sql-create.md)

Neste tutorial de ponto a ponto mostra-lhe como utilizar o Portal do Azure para Aprovisionar uma máquina de virtual com o SQL Server.

O gallery Azure máquina virtual (VM) inclui várias imagens que contêm Microsoft SQL Server. Com poucos cliques, pode selecionar uma das imagens do SQL VM a partir da Galeria e aprovisioná-lo no seu ambiente do Azure.

Neste tutorial, irá:

- [Seleccionar uma imagem de SQL VM a partir da Galeria](#select-a-sql-vm-image-from-the-gallery)
- [Configurar e criar a VM](#configure-the-vm)
- [Abra a VM com o ambiente de trabalho remoto](#open-the-vm-with-remote-desktop)
- [Ligar para o SQL Server remotamente](#connect-to-sql-server-remotely)

## <a name="select-a-sql-vm-image-from-the-gallery"></a>Seleccionar uma imagem de SQL VM a partir da Galeria

1. Inicie sessão no [portal do Azure](https://portal.azure.com) utilizando a sua conta.

    >[AZURE.NOTE] Se não possui uma conta Azure, visite o [Azure versão de avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/).

1. No portal do Azure, clique em **Novo**. O portal abre-se a pá de **Novo** . Os recursos do SQL Server VM estão no grupo **máquinas virtuais** do mercado.

1. Na pá **Novo** , clique em **máquinas virtuais**.

1. Para ver todas as imagens disponíveis, clique em **Ver todos os** a pá **máquinas virtuais** .

    ![Azure máquinas virtuais pá](./media/virtual-machines-windows-portal-sql-server-provision/azure-compute-blade.png)

1. Em **servidores de base de dados**, clique em **Do SQL Server**. Poderá ter de deslocar para baixo para localizar **os servidores de base de dados**. Rever os modelos disponíveis do SQL Server.

    ![Galeria de máquina virtual SQL imagens](./media/virtual-machines-windows-portal-sql-server-provision/virtual-machine-gallery-sql-server.png)

1. Identifica cada modelo de uma versão do SQL Server e num sistema operativo. Selecione uma das seguintes imagens a partir da lista. Em seguida, reveja a pá detalhes que fornece uma descrição da imagem máquina virtual.

    >[AZURE.NOTE] Imagens de SQL VM incluem os custos de licenciamento para o SQL Server para os preços por minuto da VM que cria. Existe outra opção para trazer o-proprietário-licenças (BYOL) e pagar apenas para a VM. Os nomes de imagem são o prefixo {BYOL}. Para mais informações sobre esta opção, consulte o artigo [Introdução ao SQL Server em máquinas virtuais do Azure](virtual-machines-windows-sql-server-iaas-overview.md).

1. Em **Selecione um modelo de implementação**, verifique que o **Gestor de recursos** está selecionada. Gestor de recursos é o modelo de implementação recomendado para máquinas virtuais novos. Clique em **Criar**.

    ![Criar SQL VM com o Gestor de recursos](./media/virtual-machines-windows-portal-sql-server-provision/azure-compute-sql-deployment-model.png)

## <a name="configure-the-vm"></a>Configurar a VM
Existem cinco pás para configurar uma máquina de virtual do SQL Server.

| Passo               | Descrição                          |
|---------------------|-------------------------------|
| **Noções básicas**              | [Configurar as definições básicas](#1-configure-basic-settings)      |
| **Tamanho**                | [Escolher o tamanho de máquina virtual](#2-choose-virtual-machine-size)   |
| **Definições**            | [Configurar funcionalidades opcionais](#3-configure-optional-features)   |
| **Definições do SQL Server** | [Configurar definições de servidor SQL](#4-configure-sql-server-settings) |
| **Resumo**             | [Reveja o resumo](#5-review-the-summary)            |

## <a name="1-configure-basic-settings"></a>1. a configurar as definições básicas
Na pá **Noções básicas** , forneça as seguintes informações:

* Introduza uma **nome**de máquina virtual exclusiva.
* Especifique um **nome de utilizador** para a conta de administrador local na VM. Esta conta também é adicionada para **o SQL Server fixo sysadmin** .
* Fornece uma forte **palavra-passe**.
* Se tiver múltiplas subscrições, verifique se a subscrição está correta para a nova VM.
* Na caixa de **grupo de recursos** , escreva um nome para um novo grupo de recursos. Em alternativa, para utilizar um recurso existente grupo clique em **Selecionar existente**. Um grupo de recursos é um conjunto de recursos relacionados no Azure (máquinas virtuais, contas de armazenamento, redes virtuais, etc.).

    >[AZURE.NOTE] Utilizar um novo grupo de recursos é útil se estiver apenas testes ou saber mais sobre implementações do SQL Server no Azure. Depois de terminar com o teste, elimine o grupo de recursos para eliminar automaticamente a VM e todos os recursos associados esse grupo de recursos. Para mais informações sobre os grupos de recursos, consulte o artigo [Descrição geral do Gestor de recursos do Azure](../azure-resource-manager/resource-group-overview.md).

* Selecione uma **localização** para esta implementação.
* Clique em **OK** para guardar as definições.

    ![Pá de noções básicas SQL](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-basic.png)

## <a name="2-choose-virtual-machine-size"></a>2. Escolha o tamanho de máquina virtual
No passo **tamanho** , escolha um tamanho de máquina virtual no pá a **Escolher um tamanho** . O pá é apresentada inicialmente tamanhos de máquina recomendadas baseados no modelo que selecionou. Prevê também ao custo mensal para executar a VM.

![Opções de tamanho VM de SQL](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-vm-choose-a-size.png)

Para cargas de trabalho de produção, recomendamos que selecione um tamanho de máquina virtual que suporta o [Armazenamento de Premium](../storage/storage-premium-storage.md). Se não forem desse nível de desempenho, utilize o botão de **Ver tudo** , que mostra todas as opções de tamanho de máquina. Por exemplo, poderá utilizar um tamanho mais pequeno do computador para o desenvolvimento ou num ambiente de teste.

>[AZURE.NOTE] Para obter mais informações sobre máquina virtual tamanhos consulte o artigo [tamanhos para máquinas virtuais](virtual-machines-windows-sizes.md). Considerações sobre os tamanhos VM do SQL Server, consulte o artigo [melhores práticas de desempenho para SQL Server em máquinas virtuais do Azure](virtual-machines-windows-sql-performance.md).

Selecione o tamanho do seu computador e, em seguida, clique em **Selecionar**.

## <a name="3-configure-optional-features"></a>3. configurar funcionalidades opcionais
Em pá **Definições** , configure o Azure armazenamento, funcionamento em rede e monitorização para a máquina virtual.

- Em **armazenamento**, especifique um **disco escreva** de padrão ou Premium (SSD). Armazenamento de Premium é recomendado para cargas de trabalho de produção.

>[AZURE.NOTE] Se selecionar Premium (SSD) para um tamanho de máquina que não suporta o armazenamento de Premium, o tamanho de máquina alterações automaticamente.  

- Em **conta de armazenamento**, pode aceitar o nome da conta de armazenamento aprovisionada automaticamente. Também pode clicar numa **conta de armazenamento** para escolher uma conta existente e configure o tipo de conta de armazenamento. Por predefinição, o Azure cria uma nova conta de armazenamento com armazenamento localmente redundante. Para mais informações sobre as opções de armazenamento, consulte o artigo [replicação de armazenamento do Windows Azure](../storage/storage-redundancy.md).

- Em **rede**, pode aceitar os valores automaticamente previamente. Também pode clicar em cada funcionalidade para configurar manualmente a **Rede Virtual**, **sub-rede**, **endereço IP público**e **Grupo de segurança de rede**. Para efeitos neste tutorial, manter os valores predefinidos.

- Azure permite **monitorização** por predefinição, com a mesma conta de armazenamento designada para a VM. Pode alterar estas definições aqui.

- Em **definir de disponibilidade**, especifique um conjunto de disponibilidade. Para efeitos neste tutorial, pode selecionar **nenhuma**. Se planeia configurar a grupos de Disponibilidade AlwaysOn SQL, configure a disponibilidade para evitar recriar a máquina virtual.  Para mais informações, consulte o artigo [Gerir a disponibilidade de máquinas virtuais](virtual-machines-windows-manage-availability.md).

Quando terminar configurar estas definições, clique em **OK**.

## <a name="4-configure-sql-server-settings"></a>4. configurar definições de servidor SQL
No pá **definições do SQL Server** , configure as definições específicas e otimizações para SQL Server. As definições que pode configurar para SQL Server incluem o seguinte.

| Definição               |
|---------------------|
| [Conectividade](#connectivity)              |
| [Autenticação](#authentication)                |
| [Configuração de armazenamento](#storage-configuration)            |
| [Automatizado a aplicação de patches](#automated-patching) |
| [Cópia de segurança automatizada](#automated-backup)             |
| [Integração do cofre chave Azure](#azure-key-vault-integration)             |
| [Serviços de R](#r-services) |

### <a name="connectivity"></a>Conectividade
Em **conetividade SQL**, especifique o tipo de acesso que pretende instância do SQL Server neste VM. Efeitos neste tutorial, selecione **pública (internet)** para permitir ligações para o SQL Server a partir do máquinas ou serviços na internet. Com esta opção selecionada, Azure configura automaticamente a firewall e o grupo de segurança de rede para permitir o tráfego no porta 1433.  

![Opções de conectividade SQL](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-arm-connectivity-alt.png)

Para ligar para o SQL Server através da internet, também tem de ativar autenticação do SQL Server, que é descrito na secção seguinte.

>[AZURE.NOTE] É possível adicionar mais restrições para comunicações de rede para a VM do SQL Server. Pode fazê-lo ao editar o grupo de segurança de rede depois da VM é criada. Para obter mais informações, consulte o artigo [o que é um grupo de segurança de rede (NSG)?](../virtual-network/virtual-networks-nsg.md)

Se preferir não ativar as ligações ao motor de base de dados através da internet, selecione uma das seguintes opções:

- **Local (no interior VM)** para permitir ligações para o SQL Server, apenas a partir de dentro da VM.
- **Privado (dentro do rede Virtual)** para permitir ligações para o SQL Server a partir do máquinas ou serviços na mesma rede virtual.

>[AZURE.NOTE] A imagem de máquina virtual do SQL Server Express edition não ativar o protocolo TCP/IP automaticamente. Esta situação for verdadeira mesmo para as opções de conectividade públicos e privados. Para Express edition, tem de utilizar o Gestor de configuração do SQL Server para [Ativar manualmente o protocolo TCP/IP](#configure-sql-server-to-listen-on-the-tcp-protocol) depois de criar a VM.

Em geral, melhore a segurança ao selecionar a ligação mais restritiva que permite do seu cenário. Mas todas as opções são passíveis de serem protegidas através de regras de grupo de segurança de rede e a autenticação do Windows/SQL.

**Porta** assume a predefinição 1433. Pode especificar um número de porta diferente.
Para obter mais informações, consulte o artigo [ligar para uma SQL Server Máquina Virtual (Gestor de recurso) | Microsoft Azure](virtual-machines-windows-sql-connect.md).

### <a name="authentication"></a>Autenticação
Se necessitar de autenticação do SQL Server, clique em **Ativar** em **autenticação de SQL**.

![Autenticação do SQL Server](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-arm-authentication.png)

>[AZURE.NOTE] Se planeia aceder ao SQL Server através da internet (ou seja, a opção de conectividade pública), tem de ativar a autenticação de SQL aqui. Acesso do público para o SQL Server necessita da utilização de autenticação de SQL.

Se ativar a autenticação do SQL Server, especifique um **nome de início de sessão** e a **palavra-passe**. Este nome de utilizador está configurado como um início de sessão de autenticação do SQL Server e o membro do **sysadmin** fixada função de servidor. Consulte o artigo [Escolher um modo de autenticação](http://msdn.microsoft.com/library/ms144284.aspx) para obter mais informações sobre os modos de autenticação.

Se não ativar autenticação do SQL Server, pode utilizar a conta de administrador local na VM para ligar a instância do SQL Server.

### <a name="storage-configuration"></a>Configuração de armazenamento
Clique em **configuração de armazenamento** para especificar os requisitos de armazenamento.

![Configuração do armazenamento de SQL](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-arm-storage.png)

>[AZURE.NOTE] Se selecionar armazenamento padrão, esta opção não está disponível. Otimização de armazenamento automático só está disponível para armazenamento Premium.

Pode especificar requisitos como operações de entrada/saída por segundo (IOPs), débito em MB/s e o tamanho do armazenamento total. Configure estes valores utilizando as escalas de correr. O portal automaticamente calcula o número de discos com base nestes requisitos.

Por predefinição, o Azure optimiza o armazenamento de 5000 IOPs, 200 MB e 1 TB de espaço de armazenamento. Pode alterar estas definições de armazenamento com base em carga de trabalho. Em **armazenamento otimizada para**, selecione uma das seguintes opções:

- **Geral** é a predefinição e suporta a maioria das cargas de trabalho.
- Processamento de **transaccionais** optimiza o armazenamento de base de dados tradicional OLTP das cargas de trabalho.
- **Armazenamento de dados** optimiza o armazenamento de elaboração de relatórios e analíticos das cargas de trabalho.

>[AZURE.NOTE] Os limites superiores os controlos de deslize variam consoante o tamanho de máquina virtual seleccionada.

### <a name="automated-patching"></a>Automatizado a aplicação de patches
**Automatizado a aplicação de patches** está ativada por predefinição. Aplicação de patches automatizado permite Azure para automaticamente correcção do SQL Server e o sistema operativo. Especificar um dia da semana, hora e duração de uma janela de manutenção. Azure executa a aplicação de patches nesta janela de manutenção. A agenda da janela de manutenção utiliza a região VM para a hora. Se não pretender Azure para automaticamente correcção do SQL Server e o sistema operativo, clique em **desativar**.  

![SQL automatizado a aplicação de patches](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-arm-patching.png)

Para mais informações, consulte o artigo [Automatizado corrigir para SQL Server em máquinas virtuais do Azure](virtual-machines-windows-sql-automated-patching.md).

### <a name="automated-backup"></a>Cópia de segurança automatizada
Ative cópias de segurança da base de dados automática para todas as bases de dados em **cópia de segurança automática**. Cópia de segurança automática é desativada por predefinição.

Quando ativa a cópia de segurança automatizada SQL, pode configurar o seguinte procedimento:

- Período de retenção (dias) para cópias de segurança
- Conta de armazenamento para utilizar para cópias de segurança
- Opção de encriptação e palavra-passe para cópias de segurança

Para encriptar a cópia de segurança, clique em **Ativar**. Em seguida, especifique a **palavra-passe**. Azure cria um certificado para encriptar as cópias de segurança e utiliza a especificado palavra-passe para proteger esse certificado.

![Cópia de segurança automática de SQL](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-arm-autobackup.png)

 Para mais informações, consulte o artigo [Cópia de segurança automatizado para SQL Server em máquinas virtuais do Azure](virtual-machines-windows-sql-automated-backup.md).

### <a name="azure-key-vault-integration"></a>Integração do cofre chave Azure
Para armazenar segredos de segurança no Azure para encriptação, clique em **integração do cofre chave Azure** e clique em **Ativar**.

![Integração do cofre chave Azure SQL](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-arm-akv.png)

A tabela seguinte lista os parâmetros necessários para configurar a integração do Azure chave cofre.

|PARÂMETRO|DESCRIÇÃO|EXEMPLO|
|----------|----------|-------|
|**URL do cofre chave** |A localização da chave cofre.|https://contosokeyvault.Vault.Azure.NET/ |
|**Nome principal** |Azure Active Directory nome principal do serviço. Este nome é também conhecida como o ID de cliente.  |5-4e11-af04eb07b669ccf2 de fde2b411 33 + d|
| **Secreta principal**|Azure Active Directory serviço principal secreta. Este segredo é também conhecido como o segredo de cliente. | 9VTJSQwzlFepD8XODnzy8n2V01Jd8dAjwm/azF1XDKM =|
|**Nome de credenciais**|**Nome de credenciais**: integração AKV cria uma credencial dentro do SQL Server, permitindo a VM ter acesso ao Cofre de palavras chave. Selecione um nome para esta credencial.| mycred1|

Para mais informações, consulte o artigo [Configurar Azure chave cofre integração para SQL Server no Azure VMs](virtual-machines-windows-ps-sql-keyvault.md).

Quando terminar de configurar as definições do SQL Server, clique em **OK**.

### <a name="r-services"></a>Serviços de R
Para SQL Server 2016 Enterprise edition, tem a opção para ativar os [Serviços do SQL Server R](https://msdn.microsoft.com/library/mt604845.aspx). Permite-lhe utilizar análise avançadas com o SQL Server 2016. Clique em **Ativar** pá as **Definições de servidor de SQL** .

![Activar os serviços do SQL Server R](./media/virtual-machines-windows-portal-sql-server-provision/azure-vm-sql-server-r-services.png)

>[AZURE.NOTE] Para imagens do SQL Server que não estão 2016 Enterprise edition, a opção ativar serviços de R é desativada.

## <a name="5-review-the-summary"></a>5. Reveja o resumo
O **Resumo** pá, reveja o resumo e clique em **OK** para criar o SQL Server, grupo de recursos e recursos especificados para este VM.

Pode monitorizar a implementação a partir do portal azure. O botão de **notificações** na parte superior do ecrã mostra o estado básico da implementação.

>[AZURE.NOTE] Para fornecer-lhe uma ideia no vezes de implementação, posso implementada uma VM SQL à região de Leste dos EUA com as predefinições. Esta implementação de teste demorou um total de 26 minutos a concluir. Mas que se poderá deparar uma mais rápido ou mais lento tempo de implementação com base na sua região e selecionado definições.

## <a name="open-the-vm-with-remote-desktop"></a>Abra a VM com o ambiente de trabalho remoto

Utilize os seguintes passos para ligar à máquina virtual com o ambiente de trabalho remoto:

1. Depois da VM Azure é criada, o ícone para a VM aparece no seu dashboard Azure. Também pode encontrar mediante navegação máquinas virtuais existentes. Clique na sua nova máquina virtual SQL. Uma **Máquina Virtual** pá apresenta os detalhes do seu máquina virtual.
1. Na parte superior da pá **Máquina Virtual** , clique em **Ligar**.
1. O browser transfere um ficheiro RDP para a VM. Abra o ficheiro RDP.
    ![Ambiente de trabalho remoto para VM de SQL](./media/virtual-machines-windows-portal-sql-server-provision/azure-sql-vm-remote-desktop.png)
1. A ligação de ambiente de trabalho remoto notifica-o de que não pode ser identificado o publicador desta ligação remota. Clique em **Ligar** para continuar.
1. Na caixa de diálogo **Segurança do Windows** , clique em **utilizar outra conta**.
1. Para tipo de **nome de utilizador** ** \<nome de utilizador >**, onde <user name> é o nome de utilizador que especificou quando configurou a VM. Tem de adicionar uma barra invertida inicial antes do nome.
1. Escreva a **palavra-passe** que configurou anteriormente para este VM e, em seguida, clique em **OK** para ligar.
1. Se outra caixa de diálogo **Ligação de ambiente de trabalho remoto** lhe perguntar se pretende ligar, clique em **Sim**.

Depois de se ligar à máquina de virtual do SQL Server, pode iniciar o SQL Server Management Studio e ligar-se a autenticação do Windows com as suas credenciais de administrador local. Se ativou autenticação do SQL Server, também pode ligar com autenticação de SQL utilizando o início de sessão SQL e configurado durante o aprovisionamento de palavra-passe.

Acesso ao computador permite-lhe alterar diretamente máquina e definições do SQL Server com base nos seus requisitos. Por exemplo, pode configurar as definições da firewall ou alterar as definições de configuração do SQL Server.

## <a name="connect-to-sql-server-remotely"></a>Ligar para o SQL Server remotamente

Neste tutorial, vamos selecionada acesso **público** para a máquina virtual e a **Autenticação do SQL Server**. Estas definições configuradas automaticamente a máquina virtual para permitir ligações de SQL Server a partir de qualquer cliente através da internet (partindo do princípio de que tenham o início de sessão do SQL correto).

>[AZURE.NOTE] Se não tiver selecionado público durante o aprovisionamento, em seguida, passos adicionais são necessários para aceder ao seu instância do SQL Server através da internet. Para mais informações, consulte o artigo [ligar para uma máquina de Virtual do SQL Server](virtual-machines-windows-sql-connect.md).

As secções seguintes mostram como se ligar à sua instância do SQL Server no seu VM a partir de um computador diferente através da internet.

> [AZURE.INCLUDE [Connect to SQL Server in a VM Resource Manager](../../includes/virtual-machines-sql-server-connection-steps-resource-manager.md)]

## <a name="next-steps"></a>Próximos passos
Para obter outras informações sobre como utilizar o SQL Server no Azure, consulte o artigo o [SQL Server em máquinas virtuais do Azure](virtual-machines-windows-sql-server-iaas-overview.md) e as [Perguntas mais frequentes](virtual-machines-windows-sql-server-iaas-faq.md).

Para obter uma descrição geral do vídeo do SQL Server em máquinas virtuais do Azure, veja [Azure VM é a melhor plataforma para o SQL Server 2016](https://channel9.msdn.com/Events/DataDriven/SQLServer2016/Azure-VM-is-the-best-platform-for-SQL-Server-2016).

[Explorar o caminho de formação](https://azure.microsoft.com/documentation/learning-paths/sql-azure-vm/) para o SQL Server em máquinas virtuais Azure.
