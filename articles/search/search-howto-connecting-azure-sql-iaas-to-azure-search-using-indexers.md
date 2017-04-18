<properties 
    pageTitle="Configurar uma ligação a partir de um indexador Azure pesquisa para o SQL Server numa máquina virtual Azure | Microsoft Azure | Indexadores" 
    description="Ativar ligações encriptadas e configurar a firewall para permitir ligações para o SQL Server num computador virtual Azure (VM) a partir de um indexador no Azure pesquisa." 
    services="search" 
    documentationCenter="" 
    authors="jack4it" 
    manager="pablocas" 
    editor=""/>

<tags 
    ms.service="search" 
    ms.devlang="rest-api" 
    ms.workload="search" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.date="09/26/2016" 
    ms.author="jackma"/>

# <a name="configure-a-connection-from-an-azure-search-indexer-to-sql-server-on-an-azure-vm"></a>Configurar uma ligação a partir de um indexador Azure pesquisa para o SQL Server numa VM Azure

Conforme indicado na [Ligação Azure SQL base de dados para pesquisa Azure utilizando indexadores](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md#frequently-asked-questions), criar indexadores contra **Do SQL Server no Azure VMs** (ou **SQL Azure VMs** para pequena) é suportado pela pesquisa Azure, mas existem algumas relacionadas com segurança pré-requisitos para encarregam-se da primeira. 

**Duração da tarefa:** Cerca de 30 minutos, partindo do princípio de já instalado um certificado na VM.

## <a name="enable-encrypted-connections"></a>Ativar as ligações encriptadas

Pesquisa Azure requer um canal encriptado para todos os pedidos de indexador através de uma ligação à internet público. Esta secção apresenta os passos para que isto funcione.

1. Verifique as propriedades do certificado para verificar que o nome de assunto é o nome de domínio completamente qualificado (FQDN) da Azure VM. Pode utilizar uma ferramenta como CertUtils ou snap-in certificados para ver as propriedades. Pode obter o FQDN Essentials secção do pá de serviço VM de, no campo **nome do endereço de IP público/DNS etiqueta** , no [portal do Azure](https://portal.azure.com/).

    - Para VMs criadas utilizando o modelo mais recente do **Gestor de recursos** , o FQDN está formatado como `<your-VM-name>.<region>.cloudapp.azure.com`. 

    - Para mais antigos VMs criados como um **clássica** VM, o FQDN está formatado como `<your-cloud-service-name.cloudapp.net>`. 

2. Configure o SQL Server para utilizar o certificado utilizando o Editor de registo (regedit). 

    Apesar de Gestor de configuração do SQL Server é normalmente utilizado para esta tarefa, não pode utilizá-lo para este cenário. -Não localiza o certificado importado porque o FQDN do VM no Azure não correspondem ao FQDN como determinado pelo VM (identifique o domínio como o computador local ou o domínio de rede ao qual está associado). Quando os nomes não corresponderem, utilize regedit para especificar o certificado.

    - Na regedit, navegue para esta chave de registo: `HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft SQL Server\[MSSQL13.MSSQLSERVER]\MSSQLServer\SuperSocketNetLib\Certificate`.
     
    O `[MSSQL13.MSSQLSERVER]` peça varia com base no nome da versão e instância. 

    - Defina o valor da chave de **certificado** para a **impressão digital** do certificado SSL que importou para a VM.

    Existem várias formas de começar a impressão digital, alguns melhor do que outras pessoas. Se copiar a partir do snap-in **certificados** na MMC, provavelmente vai selecionar para cima uma invisíveis levando caráter [conforme descrito neste artigo de suporte](https://support.microsoft.com/kb/2023869/), que resulta num erro quando tenta uma ligação. Existem várias formas de contornar para corrigir este problema. O mais fácil é RETROCESSO sobre e, em seguida, escreva novamente o primeiro caráter da impressão digital para remover o caráter à esquerda do campo de valor de chave na regedit. Em alternativa, pode utilizar uma ferramenta diferente para copiar a impressão digital.

3. Conceder permissões para a conta de serviço. 

    Certifique-se que a conta de serviço do SQL Server é concedida a permissão adequada na chave privada do certificado SSL. Se ignorar este passo, SQL Server não será iniciado. Pode utilizar o snap-in **certificados** ou **CertUtils** para esta tarefa.

4. Reinicie o serviço do SQL Server.

## <a name="configure-sql-server-connectivity-in-the-vm"></a>Configurar a conetividade do SQL Server na VM

Depois de configurar a ligação encriptada, uma ferramenta necessária ao Azure pesquisa, existem passos de configuração adicionais intrínsecos ao SQL Server no Azure VMs. Se ainda não o tiver feito, o passo seguinte é concluir a configuração utilizar qualquer um dos seguintes artigos:

- Para um **Gestor de recursos** VM, consulte o artigo [ligar a uma SQL Server Máquina Virtual no Azure utilizando o Gestor de recursos](../virtual-machines/virtual-machines-windows-sql-connect.md). 

- Para um **clássica** VM, consulte o artigo [ligar a uma SQL Server Máquina Virtual no Azure clássica](../virtual-machines/virtual-machines-windows-classic-sql-connect.md).

Em particular, reveja a secção cada artigo para "ligar através da internet".

## <a name="configure-the-network-security-group-nsg"></a>Configurar o grupo de segurança de rede (NSG)

Não é invulgar configurar o NSG e ponto final Azure correspondente ou lista de controlo de acesso (ACL) para tornar o seu VM Azure acessível para as outras partes. Provavelmente que tenha feito antes de permitir que o seu próprio lógica da aplicação ligar à sua VM de Azure SQL. É não diferente para uma ligação do Azure pesquisa ao seu VM de Azure SQL. 

As ligações em baixo fornecem instruções sobre a configuração do NSG para implementações VM. Utilize estas instruções para ACL um ponto final Azure pesquisa com base no seu endereço IP.

> [AZURE.NOTE] Para segundo plano, consulte o artigo [o que é um grupo de segurança da rede?](../virtual-network/virtual-networks-nsg.md)

- Para um **Gestor de recursos** VM, consulte o artigo [como criar NSGs para implementações processador](../virtual-network/virtual-networks-create-nsg-arm-pportal.md). 

- Para um **clássica** VM, consulte o artigo [como criar NSGs para implementações clássica](../virtual-network/virtual-networks-create-nsg-classic-ps.md).

Endereçamento IP pode representar alguns desafios que são ultrapassar facilmente se esteja ciente do problema e soluções para os mesmos. As secções restantes fornecem recomendações para problemas relacionados com endereços IP na ACL de processamento.

#### <a name="restrict-access-to-the-search-service-ip-address"></a>Restringir o acesso ao endereço IP do serviço de pesquisa

Recomendamos vivamente que restringir o acesso ao endereço IP do seu serviço de pesquisa na ACL em vez de tornar o seu VMs do Azure SQL e larga abrir a qualquer pedidos de ligação. Pode saber facilmente o endereço IP executando o ping o FQDN (por exemplo, `<your-search-service-name>.search.windows.net`) do seu serviço de pesquisa.

#### <a name="managing-ip-address-fluctuations"></a>Gerir os variações de endereço IP

Se o seu serviço de pesquisa tiver apenas uma unidade de pesquisa (ou seja, uma réplica e uma partição), irá alterar o endereço IP durante o serviço de manutenção de rotina reinício, invalidem a uma ACL existente com o endereço IP do seu serviço de pesquisa.

Uma forma de evitar o erro de conectividade subsequentes é utilizar mais do que uma réplica e uma partição no Azure pesquisa. Fazê-lo aumenta o custo, mas também é resolvido o problema de endereço IP. Na pesquisa Azure, endereços IP não são alterados quando tiver mais do que uma unidade de pesquisa.

Uma segunda abordagem é permite a ligação a falhar e, em seguida, reconfigurar ACL na NSG. Em média, que pode esperar endereços IP para alterar em alguns semanas. Para clientes que faça a indexação controlada numa base pouco frequente, esta abordagem poderá viável.

É uma terceira abordagem viável (mas não particularmente segura) especificar o intervalo de endereços IP da região Azure onde o seu serviço de pesquisa está aprovisionado. A lista de intervalos IP a partir do qual endereços IP públicos são atribuídos para recursos Azure é publicada no [intervalos IP do Centro de dados de Azure](https://www.microsoft.com/download/details.aspx?id=41653). 

#### <a name="include-the-azure-search-portal-ip-addresses"></a>Incluir os endereços IP portais de pesquisa do Azure

Se estiver a utilizar o portal do Azure para criar um indexador, lógica portal de pesquisa do Azure também precisa de acesso ao seu VM de Azure SQL durante a hora de criação. Endereços IP portais do Azure pesquisa podem ser encontrados executando o ping `stamp2.search.ext.azure.com`.

## <a name="next-steps"></a>Próximos passos

Com a configuração para fora da área de trabalho, pode agora especificar SQL Server no Azure VM como origem de dados de um indexador Azure pesquisa. Para mais informações, consulte [Ligar Azure SQL base de dados para pesquisa Azure utilizando indexadores](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md) .
