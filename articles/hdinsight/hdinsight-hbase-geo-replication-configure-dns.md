<properties 
   pageTitle="Configurar o DNS entre duas redes virtuais Azure | Microsoft Azure" 
   description="Saiba como configurar ligações de VPN e resolução do nome de domínio entre duas redes virtuais e como configurar a replicação de geo HBase." 
   services="hdinsight,virtual-network" 
   documentationCenter="" 
   authors="mumian" 
   manager="jhubbard" 
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="06/28/2016"
   ms.author="jgao"/>

# <a name="configure-dns-between-two-azure-virtual-networks"></a>Configurar o DNS entre duas redes virtuais Azure

> [AZURE.SELECTOR]
- [Configurar a conectividade VPN](../hdinsight-hbase-geo-replication-configure-vnets.md)
- [Configurar o DNS](hdinsight-hbase-geo-replication-configure-dns.md)
- [Configurar a replicação HBase](hdinsight-hbase-geo-replication.md) 


Saiba como adicionar e configurar servidores DNS para o Azure redes virtuais para processar resolução do nome no interior e através das redes virtuais.

Neste tutorial é a segunda parte da [série] [ hdinsight-hbase-geo-replication] sobre como criar HBase geo-replicação:

- [Configurar uma ligação VPN entre duas redes virtuais][hdinsight-hbase-geo-replication-vnet]
- Configurar o DNS para as redes virtuais (Este tutorial)
- [Configurar a HBase geo replicação][hdinsight-hbase-geo-replication]


O diagrama seguinte ilustra as duas redes virtuais que criou no [Configurar uma ligação VPN entre duas redes virtuais][hdinsight-hbase-geo-replication-vnet]:

![Diagrama de rede virtual HDInsight HBase replicação][img-vnet-diagram]

##<a name="prerequisites"></a>Pré-requisitos
Antes de começar este tutorial, tem de ter o seguinte procedimento:

- **Azure uma subscrição**. Consulte o artigo [obter Azure versão de avaliação gratuita](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- **Uma estação de trabalho com o Azure PowerShell**.

    Antes de executar scripts de PowerShell, certifique-se de que está ligado à sua subscrição do Azure utilizando o cmdlet seguinte:

        Add-AzureAccount

    Se tiver múltiplas subscrições Azure, utilize o cmdlet seguinte para definir a subscrição atual:

        Select-AzureSubscription <AzureSubscriptionName>
        
    [AZURE.INCLUDE [upgrade-powershell](../../includes/hdinsight-use-latest-powershell.md)]

- **Azure duas rede virtual com a conectividade VPN**.  Para obter instruções, consulte o artigo [Configurar uma ligação VPN entre duas redes virtuais Azure][hdinsight-hbase-geo-replication-vnet].

>[AZURE.NOTE] Nomes de serviço Azure e nomes de máquina virtual tem de ser exclusivos. O nome utilizado neste tutorial é Contoso-[nome do Azure Service/VM]-[UE / EUA]. Por exemplo, Contoso-VNet-UE é a rede virtual Azure no Centro de dados do Norte Europa; Contoso-DNS-US são o servidor DNS VM no Centro de dados do Leste dos EUA. Tem de vir com o seus próprio nomes.
 
 
##<a name="create-azure-virtual-machines-to-be-used-as-dns-servers"></a>Criar Azure máquinas virtuais para ser utilizado como servidores DNS

**Para criar uma máquina de virtual dentro da Contoso-VNet-UE, chamado Contoso-DNS-UE**

1.  Clique em **Novo**, **Calcular**, **máquina VIRTUAL**, **De GALERIA**.
2.  Selecione **Centro de R2 do Windows Server 2012**.
3.  Introduza:
    - **Nome da máquina VIRTUAL**: Contoso-DNS-UE
    - **Novo nome de utilizador**: 
    - **Nova palavra-passe**: 
4.  Introduza:
    - **Serviço em NUVEM**: criar um novo serviço na nuvem
    - **Região/grupo AFINIDADE/VIRTUAL de rede**: (selecione Contoso-VNet-UE)
    - **VIRTUAL sub-redes de rede**: sub-rede-1
    - **Conta de armazenamento**: utilizar uma conta de armazenamento gerados automaticamente
    
        O nome do serviço de nuvem serão os mesmos como o nome de máquina virtual. Neste caso, que é Contoso-DNS-UE. Para máquinas virtuais subsequentes, posso pode optar por utilizar o serviço de nuvem mesmo.  Todas as máquinas virtuais em serviço em nuvem mesmo partilhar a mesma rede virtual e sufixo do domínio.

        A conta de armazenamento é utilizada para armazenar o ficheiro de imagem máquina virtual. 
    - **Os pontos finais**: (deslocar para baixo e selecione **DNS**) 

Quando a máquina virtual estiver criada, descubra interna IP e IP externo.

1.  Clique no nome de máquina virtual, **Contoso-DNS-UE**.
2.  Clique em **DashBoard**.
3.  Tome nota:
    - ENDEREÇO IP PÚBLICO DE VIRTUAL
    - ENDEREÇO IP INTERNO


**Para criar uma máquina de virtual dentro da Contoso-VNet-US, chamado Contoso-DNS-pt** 

- Repita o mesmo procedimento para criar uma máquina virtual com os seguintes valores:
    - NOME da máquina VIRTUAL: Contoso-DNS-pt
    - REGIÃO/grupo AFINIDADE/da rede VIRTUAL: selecione Contoso-VNET-pt
    - REDE VIRTUAL sub-redes: Sub-rede-1
    - CONTA de armazenamento: Utilizar uma conta de armazenamento gerados automaticamente
    - Os pontos finais: (selecione DNS)

##<a name="set-static-ip-addresses-for-the-two-virtual-machines"></a>Definir os endereços IP estáticos para as duas máquinas virtuais

Os servidores DNS requer endereços IP estáticos.  Este passo não pode ser feito Portal clássica do Azure. Irá utilizar o Azure PowerShell.

**Para configurar o endereço IP estático para as duas máquinas virtuais**

1. Abra o Windows PowerShell ISE.
2. Execute os seguintes cmdlets.  

        Add-AzureAccount
        Select-AzureSubscription [YourAzureSubscriptionName]
        
        Get-AzureVM -ServiceName Contoso-DNS-EU -Name Contoso-DNS-EU | Set-AzureStaticVNetIP -IPAddress 10.1.0.4 | Update-AzureVM
        Get-AzureVM -ServiceName Contoso-DNS-US -Name Contoso-DNS-US | Set-AzureStaticVNetIP -IPAddress 10.2.0.4 | Update-AzureVM 

    NomeServiço é o nome do serviço na nuvem. Porque o servidor DNS for a primeira máquina virtual do serviço de nuvem, o nome de serviço de nuvem é o mesmo que o nome de máquina virtual.

    Poderá ter de atualizar ServiceName e o nome correspondem aos nomes que possui.


##<a name="add-the-dns-server-role-the-two-virtual-machines"></a>Adicionar máquinas virtuais função as duas servidor DNS

**Para adicionar a função de servidor de DNS para Contoso-DNS-UE**

1.  A partir do Portal do Azure clássico, clique em **máquinas virtuais** à esquerda. 
2.  Clique em **Contoso-DNS-UE**.
3.  Clique em **DASHBOARD** situado na parte superior.
4.  Clique em **Ligar** a partir da parte inferior e siga as instruções para ligar à máquina virtual através do RDP.
2.  Dentro da sessão RDP, clique no botão do Windows no canto inferior esquerdo para abrir o ecrã de início.
3.  Clique no mosaico do **Gestor de servidor** .
4.  Clique em **Adicionar funções e funcionalidades**.
5.  Clique em **seguinte**
6.  Selecione **instalação baseado em funções ou funcionalidade baseada**e, em seguida, clique em **seguinte**.
7.  Selecione máquina virtual DNS (devem ser realçada já) e, em seguida, clique em **seguinte**.
8.  Verifique o **servidor de DNS**.
9.  Clique em **Adicionar funcionalidades**e, em seguida, clique em **continuar**.
10. Clique três vezes em **seguinte** e, em seguida, clique em **instalar**. 

**Para adicionar a função de servidor de DNS para Contoso-DNS-pt**

- Repita os passos para adicionar a função de DNS para **Contoso-DNS-US**.

##<a name="assign-dns-servers-to-the-virtual-networks"></a>Atribuir servidores DNS para as redes virtuais

**Para registar os dois servidores DNS**

1.  A partir do Portal do Azure clássico, clique em **Novo**, **Serviços de rede**, **Rede VIRTUAL**, **Registe-se o servidor de DNS**.
2.  Introduza:
    - **Nome**: Contoso-DNS-UE
    - **Endereço de IP do servidor DNS**: 10.1.0.4 – o endereço IP tem correspondência o endereço IP de máquina virtual de servidor DNS.
     
3.  Repita o processo para registar Contoso-DNS-US com as seguintes definições:
    - **Nome**: Contoso-DNS-pt
    - **Endereço de IP do servidor DNS**: 10.2.0.4

**Para atribuir os dois servidores DNS para as duas redes virtuais**

1.  Clique em **redes** a partir do painel esquerdo no Portal do clássico.
2.  Clique em **Contoso-VNet-UE**.
3.  Clique em **Configurar**.
4.  Selecione **Contoso-DNS-UE** na secção **dns servers** .
5.  Clique em **Guardar** na parte inferior da página e clique em **Sim** para confirmar.
6.  Repita o processo de atribuir o servidor de DNS **Contoso-DNS-US** para a rede virtual **Contoso-VNet-US** .

Todas as máquinas virtuais que foram implementadas para as redes virtuais tem de ser reiniciadas para atualizar a configuração do servidor DNS.

**Para reiniciar as máquinas virtuais**

1. A partir do Portal do Azure clássico, clique em **máquinas virtuais** à esquerda.
2. Clique em **Contoso-DNS-UE**.
3. Clique em **Dashboard** situado na parte superior.
4. Clique em **REINICIAR** na parte inferior.
5. Repita os mesmos passos para reiniciar o computador **Contoso-DNS-US**.


##<a name="configure-dns-conditional-forwarders"></a>Configure os reencaminhadores condicionais DNS

O servidor de DNS em cada virtual rede só pode resolver nomes DNS dentro desse rede virtual. Tem de configurar um reencaminhador condicional para apontarem para o servidor DNS de ponto a ponto para resoluções de nome na rede virtual ponto a ponto.

Para configurar reencaminhador condicional, tem de conhecer os sufixos de domínio dos servidores DNS da duas. Os sufixos DNS podem ser diferentes dependendo a configuração de serviços em nuvem que utilizou quando criou as máquinas virtuais. Para cada sufixo DNS utilizado na rede virtual, tem de adicionar um reencaminhador condicional. 

**Para localizar os sufixos de domínio dos servidores DNS da duas**

1. RDP para **Contoso-DNS-UE**.
2. Abra o Windows PowerShell consola ou linha de comandos.
3. Executar o **ipconfig**e anote **sufixo DNS específico da ligação**.
4. Não fechar a sessão RDP, irá necessitar de-lo. 
5. Repita os mesmos passos para saber o **sufixo DNS específico da ligação** de **Contoso-DNS-US**.


**Para configurar reencaminhadores de DNS**
 
1.  A sessão RDP **Contoso-DNS-UE**, clique na tecla do Windows no canto inferior esquerdo.
2.  Clique em **Ferramentas administrativas**.
3.  Clique em **DNS**.
4.  No painel esquerdo, expanda **DSN**, **Contoso-DNS-UE**.
5.  Introduza as seguintes informações:
    - **Domínio de DNS**: introduza o sufixo DNS da Contoso-DNS-US. Por exemplo: Contoso-DNS-US.b5.internal.cloudapp.net.
    - **Endereços IP dos servidores principais**: introduza 10.2.0.4, que é o endereço IP do Contoso-DNS-e.u.a..
6.  Prima **ENTER**e, em seguida, clique em **OK**.  Agora pode poderão resolver endereço IP do Contoso-DNS-e.u.a. da Contoso-DNS-UE.
7.  Repita os passos para adicionar um reencaminhador de DNS para o serviço DNS no computador, virtual Contoso-DNS-US com os seguintes valores:
    - **Domínio de DNS**: introduza o sufixo da UE de DNS de Contoso. 
    - **Endereços IP dos servidores principais**: introduza 10.2.0.4, que é o endereço IP Contoso-DNS-da UE.

##<a name="test-the-name-resolution-across-the-virtual-networks"></a>Testar a resolução de nomes através das redes virtuais

Agora pode testar resolução do nome de anfitrião através das redes virtuais. Ping foi bloqueada pelo firewall por predefinição.  Pode utilizar o nslookup para resolver as máquinas virtuais do servidor DNS (tem de utilizar FQDN) nas redes ponto a ponto.  


##<a name="next-steps"></a>Próximos passos

Neste tutorial, aprendeu como configurar a resolução do nome através de redes virtuais com ligações VPN. Outros duas artigos na série abrangem:

- [Configurar uma ligação VPN entre duas redes virtuais Azure][hdinsight-hbase-geo-replication-vnet]
- [Configurar a HBase geo replicação][hdinsight-hbase-geo-replication]



[hdinsight-hbase-geo-replication]: hdinsight-hbase-geo-replication.md
[hdinsight-hbase-geo-replication-vnet]: hdinsight-hbase-geo-replication-configure-vnets.md
[powershell-install]: powershell-install-configure.md

[img-vnet-diagram]: ./media/hdinsight-hbase-geo-replication-configure-DNS/HDInsight.HBase.VPN.diagram.png 
