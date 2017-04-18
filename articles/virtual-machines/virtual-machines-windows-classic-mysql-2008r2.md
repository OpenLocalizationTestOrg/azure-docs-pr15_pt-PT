<properties
    pageTitle="Criar uma VM executar MySQL | Microsoft Azure"
    description="Crie uma máquina de virtual Azure que executem o Windows Server 2012 R2 e a base de dados do MySQL utilizando o modelo de implementação clássica."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="cynthn"
    manager="timlt"
    editor="tysonn"
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/25/2016"
    ms.author="cynthn"/>


# <a name="install-mysql-on-a-virtual-machine-created-with-the-classic-deployment-model-running-windows-server-2012-r2"></a>Instalar o MySQL num computador virtual criado com o modelo de implementação clássica a executar o Windows Server 2012 R2

[MySQL](http://www.mysql.com) é um popular abrir origem, base de dados SQL. Este tutorial mostra-lhe como instalar e executar a versão da Comunidade do MySQL 5.6.23 como um servidor do MySQL num computador a executar o Windows Server 2012 R2 virtual. Para obter instruções sobre como instalar MySQL Linux, consulte: [como instalar MySQL no Azure](virtual-machines-linux-mysql-install.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]

## <a name="create-a-virtual-machine-running-windows-server-2012-r2"></a>Criar uma máquina de virtual com o Windows Server 2012 R2

Se ainda não tiver uma VM a executar o Windows Server 2012 R2, pode utilizar este [tutorial](virtual-machines-windows-classic-tutorial.md) para criar a máquina virtual. 

## <a name="attach-a-data-disk"></a>Anexar um disco de dados

Quando a máquina virtual estiver criada, opcionalmente, pode anexar um disco de dados adicionais. Isto é recomendado para cargas de trabalho de produção e para evitar a falta de espaço na unidade de sistema operativo (c), que inclui o sistema operativo.

Consulte o artigo [como anexar um disco de dados para uma máquina de virtual do Windows](virtual-machines-windows-classic-attach-disk.md) e siga as instruções para anexar um disco vazio. Definir a definição de cache anfitrião para **nenhum** ou **só de leitura**.

## <a name="log-on-to-the-virtual-machine"></a>Inicie sessão no computador virtual

Em seguida, irá [Iniciar sessão para a máquina virtual](virtual-machines-windows-classic-connect-logon.md) para que possa instalá MySQL.

##<a name="install-and-run-mysql-community-server-on-the-virtual-machine"></a>Instalar e executar o servidor do MySQL precisar da Comunidade na máquina virtual

Siga estes passos para instalar, configurar e executar a versão da Comunidade do servidor do MySQL precisar:

> [AZURE.NOTE] Estes passos para o 5.6.23.0 não forem versão de Comunidade do MySQL e Windows Server 2012 R2. A experiência do poderão ser diferente para diferentes versões do MySQL ou o Windows Server.

1.  Depois de se ter ligado à máquina virtual utilizando o ambiente de trabalho remoto, clique em **Do Internet Explorer** do ecrã de início.
2.  Selecione o botão de **Ferramentas** no canto superior direito (o ícone de roda cogged) e, em seguida, clique em **Opções da Internet**. Clique no separador **segurança** , clique no ícone de **Sites fidedignos** e, em seguida, clique no botão de **Sites** . Adicionar http://*. mysql.com à lista de sites fidedignos. Clique em * *Fechar**e, em seguida, clique em * *OK**.
3.  Na barra endereço do Internet Explorer, escreva http://dev.mysql.com/downloads/mysql/.
4.  Utilize o site do MySQL para localizar e transferir a versão mais recente do instalador do MySQL para Windows. Quando escolher o instalador do MySQL, transfira a versão que tem a completa (por exemplo, o mysql-installer-Comunidade-5.6.23.0.msi com um tamanho de ficheiro de 282.4 MB) do conjunto de ficheiros e guarde o programa de instalação.
5.  Quando o instalador do concluiu a transferência, clique em **Executar** para iniciar a configuração.
6.  Na página **Contrato de licença** , aceite o contrato de licença e clique em **seguinte**.
7.  Na página **Escolher um tipo de configuração** , clique no tipo de configuração que pretende e, em seguida, clique em **seguinte**. Os seguintes passos partem do pressuposto a seleção do tipo de configuração de **apenas o servidor** .
8.  Na página de **instalação** , clique em **Executar**. Quando a instalação estiver concluída, clique em **seguinte**.
9.  Na página **Configuração de produto** , clique em **seguinte**.
10. Na página **tipo e redes** , especifique as configuração pretendida tipo e conectividade opções, incluindo a porta TCP caso seja necessário. Selecione **Mostrar opções avançadas**e, em seguida, clique em **seguinte**.

    ![](./media/virtual-machines-windows-classic-mysql-2008r2/MySQL_TypeNetworking.png)

11. Na página **contas e funções** , especifique uma palavra-passe da raiz de MySQL segura. Adicione contas de utilizador do MySQL adicionais, conforme necessário e, em seguida, clique em **seguinte**.

    ![](./media/virtual-machines-windows-classic-mysql-2008r2/MySQL_AccountsRoles_Filled.png)

12. Na página do **Serviço do Windows** , especificar alterações para as predefinições para executar o servidor do MySQL como um serviço do Windows, conforme necessário e, em seguida, clique em **seguinte**.

    ![](./media/virtual-machines-windows-classic-mysql-2008r2/MySQL_WindowsService.png)

13. Na página **Opções avançadas** , especifique as alterações às opções de registo, conforme necessário e, em seguida, clique em **seguinte**.

    ![](./media/virtual-machines-windows-classic-mysql-2008r2/MySQL_AdvOptions.png)

14. Na página **Configuração do servidor de aplicar** , clique em **Executar**. Quando estiverem concluídos, os passos de configuração, clique em **Concluir**.
15. Na página **Configuração de produto** , clique em **seguinte**.
16. Na página **Concluir a instalação** , clique em **Registo de copiar para área de transferência** se pretender examiná-la mais tarde e, em seguida, clique em **Concluir**.
17. A partir do ecrã Iniciar, escreva **mysql**e, em seguida, clique em **Cliente de linha de comandos do MySQL 5.6**.
18. Introduza a palavra-passe de raiz que especificou no passo 11 e lhe-á ser apresentado com um pedido onde pode emitir comandos para configurar MySQL. Para obter detalhes de comandos e sintaxe, consulte o artigo [Manuais de referência do MySQL](http://dev.mysql.com/doc/refman/5.6/en/server-configuration-defaults.html).

    ![](./media/virtual-machines-windows-classic-mysql-2008r2/MySQL_CommandPrompt.png)

19. Também pode configurar configuração predefinições do servidor, tais como a directórios base e dados e as unidades, com entradas no ficheiro de 5.6\my-default.ini de servidor de ficheiros c:\Programas\Microsoft (x86) \MySQL\MySQL. Para obter mais informações, consulte o artigo [5.1.2 predefinições de configuração do servidor](http://dev.mysql.com/doc/refman/5.6/en/server-configuration-defaults.html).

## <a name="configure-endpoints"></a>Configurar os pontos finais

Se pretender que o serviço do servidor do MySQL esteja disponível para computadores de cliente do MySQL na Internet, tem de configurar um ponto final para a porta TCP no qual o serviço de servidor do MySQL está a escutar e crie uma regra adicional da Firewall do Windows. Esta é a porta TCP 3306, a menos que especificou uma porta diferente na página **tipo e redes** (passo 10 do procedimento anterior).


> [AZURE.NOTE] Considere cuidadosamente as implicações de segurança de efetuar esta ação, uma vez que este procedimento irá tornar o serviço de servidor do MySQL disponíveis para todos os computadores na Internet. Pode definir o conjunto de endereços IP de origem que são permitidos para utilizar o ponto final de com uma lista de controlo de acesso (ACL). Para mais informações, consulte o artigo [como definir o pontos finais para uma Máquina Virtual](virtual-machines-windows-classic-setup-endpoints.md).


Para configurar um ponto final do serviço de servidor do MySQL:

1.  No portal do Azure clássico, clique em **máquinas virtuais**, clique no nome do seu máquina de virtual MySQL e, em seguida, clique em **pontos finais**.
2.  Na barra de comandos, clique em **Adicionar**.
3.  Na página **Adicionar um ponto final para uma máquina virtual** , clique na seta à direita.
4.  Se estiver a utilizar a predefinição porta MySQL TCP de 3306, clique em **MySQL** no **nome**e, em seguida, clique na marca de verificação.
5.  Se estiver a utilizar uma porta TCP diferente, escreva um nome exclusivo no **nome**. Selecione **TCP** no protocolo, escreva o número da porta **Porta públicos** e **Privados porta**e, em seguida, clique na marca de verificação.

## <a name="add-a-windows-firewall-rule-to-allow-mysql-traffic"></a>Adicionar uma regra de Firewall do Windows para permitir o tráfego do MySQL

Para adicionar uma regra de Firewall do Windows que permita MySQL tráfego da Internet, execute o seguinte comando numa linha de comandos do Windows PowerShell elevada na máquina MySQL servidor virtual.

    New-NetFirewallRule -DisplayName "MySQL56" -Direction Inbound –Protocol TCP –LocalPort 3306 -Action Allow -Profile Public


    
## <a name="test-your-remote-connection"></a>Testar a ligação remota


Para testar a ligação ao serviço do MySQL servidor a executar na máquina virtual Azure remota, primeiro tem de determinar o nome do DNS correspondente para o serviço de nuvem que contém a máquina virtual a executar o servidor do MySQL precisar.

1.  No portal do Azure clássico, clique em **máquinas virtuais**, clique no nome do seu máquina de virtual de servidor do MySQL e, em seguida, clique em **Dashboard**.
2.  A partir do dashboard de máquina virtual, tenha em atenção o valor de **Nome de DNS** , na secção **Rapidamente rápida** . Eis um exemplo:

    ![](./media/virtual-machines-windows-classic-mysql-2008r2/MySQL_DNSName.png)

3.  A partir de um computador local com MySQL ou com o cliente do MySQL, execute o seguinte comando para iniciar sessão como um utilizador do MySQL.

        mysql -u <yourMysqlUsername> -p -h <yourDNSname>

    Por exemplo, para o dbadmin3 de nome de utilizador do MySQL e o nome de DNS testmysql.cloudapp.net para a máquina virtual, utilize o seguinte comando.

        mysql -u dbadmin3 -p -h testmysql.cloudapp.net


## <a name="next-steps"></a>Próximos passos

Para saber mais sobre como executar MySQL, consulte a [Documentação do MySQL](http://dev.mysql.com/doc/).
