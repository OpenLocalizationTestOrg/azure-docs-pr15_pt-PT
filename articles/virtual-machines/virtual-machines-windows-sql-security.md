<properties
    pageTitle="Considerações de segurança para SQL Server no Azure | Microsoft Azure"
    description="Este tópico que se refere a recursos criados com o modelo clássico de implementação e fornece informações gerais para proteger o SQL Server em execução numa máquina de Virtual do Azure."
    services="virtual-machines-windows"
    documentationCenter="na"
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
    ms.date="06/24/2016"
    ms.author="jroth" />

# <a name="security-considerations-for-sql-server-in-azure-virtual-machines"></a>Considerações de segurança para SQL Server em máquinas virtuais do Azure
 
Este tópico inclui geral diretrizes de segurança que ajudam a estabelecer uma VM Azure acesso seguro a instâncias do SQL Server. No entanto, para garantir melhor protecção a sua instâncias de base de dados do SQL Server no Azure, recomendamos que implementar as práticas de segurança tradicional no local para além das melhores práticas de segurança para Azure.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]


Para mais informações sobre as práticas de segurança do SQL Server, consulte o artigo [SQL Server 2008 R2 melhores práticas de segurança - operacionais e tarefas administrativas](http://download.microsoft.com/download/1/2/A/12ABE102-4427-4335-B989-5DA579A4D29D/SQL_Server_2008_R2_Security_Best_Practice_Whitepaper.docx)

Azure está em conformidade com várias normas do setor e padrões que podem permitem-lhe criar uma solução em conformidade com o SQL Server em execução numa máquina Virtual. Para obter informações sobre a conformidade de regulamentação com Azure, consulte o artigo [Centro de fidedignidade do Azure](https://azure.microsoft.com/support/trust-center/).

Segue-se uma lista de recomendações de segurança que devem ser consideradas quando configurar e ligar-se para a instância do SQL Server em máquinas uma VM Azure.

## <a name="considerations-for-managing-accounts"></a>Considerações para gerir contas de:

- Crie uma conta de administrador local exclusivo que não chama-se **administrador**.

- Utilize complexas palavras-passe fortes para todas as suas contas. Para obter mais informações sobre como criar uma palavra-passe da segura, consulte o artigo [sugestões para criar um palavras-passe fortes](http://windows.microsoft.com/en-us/windows-vista/Tips-for-creating-a-strong-password) artigo.

- Por predefinição, o Azure seleciona a autenticação do Windows durante a configuração do SQL Server Virtual Machine. Assim, o início de sessão **SA** é desactivado e uma palavra-passe é atribuída pelo programa de configuração. Vamos recomendar que deve ser **SA** login não ser utilizada ou ativada. Seguem-se estratégias alternativas se um início de sessão do SQL for pretendido:
    - Crie uma conta SQL que tenha sysadmin associação.
    - Se tem de utilizar um início de sessão **SA** , ativar o início de sessão e mude o nome e atribuir uma palavra-passe nova.
    - Ambas as opções que foram mencionadas anteriormente requerem uma alteração o modo de autenticação **do SQL Server e o modo de autenticação do Windows**. Para mais informações, consulte o artigo [Alterar modo de autenticação de servidor](https://msdn.microsoft.com/library/ms188670.aspx).

## <a name="considerations-for-securing-connections-to-azure-virtual-machine"></a>Considerações para proteger ligações a Máquina Virtual Azure:

- Considere utilizar [Rede Virtual Azure](../virtual-network/virtual-networks-overview.md) para administrar as máquinas virtuais em vez de portas RDP públicas.

- Utilize um [Grupo de segurança de rede](../virtual-network/virtual-networks-nsg.md) (NSG) para permitir ou negar o tráfego de rede para o seu computador virtual. Se pretender utilizar um NSG e ter um ponto final ACL já no local, remova primeiro o ponto final de ACL. Para obter informações sobre como fazer isto, consulte o artigo [Gerir o acesso listas de controlo (ACL) para os pontos finais ao utilizar o PowerShell](../virtual-network/virtual-networks-acl-powershell.md).

- Se estiver a utilizar os pontos finais, remova qualquer os pontos finais na máquina virtual se não utilizá-los. Para obter instruções sobre como utilizar juntamente com os pontos finais, consulte o artigo [Gerir a ACL num ponto final](../virtual-network/virtual-machines-windows-classic-setup-endpoints.md#manage-the-acl-on-an-endpoint).

- Ative uma opção de ligação encriptada para uma instância do motor de base de dados do SQL Server em máquinas virtuais do Azure. Configure a instância do SQL server com um certificado com a sessão iniciada. Para mais informações, consulte o artigo [Ativar as ligações encriptados ao motor de base de dados](https://msdn.microsoft.com/library/ms191192.aspx) e [Sintaxe da cadeia de ligação](https://msdn.microsoft.com/library/ms254500.aspx).

- Se o seu máquinas virtuais deve ser acedidas apenas a partir de uma rede específica, utilize a Firewall do Windows para restringir o acesso a determinadas endereços IP ou sub-redes de rede.

## <a name="next-steps"></a>Próximos passos

Se também estiver interessado em práticas recomendadas para desempenho, consulte o artigo [Desempenho melhores práticas para SQL Server em máquinas virtuais do Azure](virtual-machines-windows-sql-performance.md).

Para obter outros tópicos relacionados com a execução do SQL Server no Azure VMs, consulte o artigo [Do SQL Server em máquinas virtuais do Azure panorâmica](virtual-machines-windows-sql-server-iaas-overview.md).
