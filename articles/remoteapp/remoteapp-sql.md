<properties
   pageTitle="SQL Azure com Azure RemoteApp | Microsoft Azure"
   description="Saiba como utilizar o SQL Azure com RemoteApp do Azure."
   services="remoteapp"
   documentationCenter=""
   authors="ericorman"
   manager="mbaldwin"
   editor=""/>

<tags
   ms.service="remoteapp"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="compute"
   ms.date="08/15/2016"
   ms.author="elizapo"/>

# <a name="sql-azure-with-azure-remoteapp"></a>SQL Azure com Azure RemoteApp

> [AZURE.IMPORTANT]
> Está a ser descontinuada Azure RemoteApp. Leia o [anúncio](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.

Muitas vezes, quando os clientes escolher alojar o seus aplicações do Windows na nuvem com RemoteApp do Azure que também pretendem migrar os respetivos dados tal como servidores SQL Server para a nuvem para uma implementação nuvem inteira. Isto permite uma solução de nuvem inteira alojada que possa ser acedida qualquer altura por qualquer dispositivo de qualquer lugar com o Azure RemoteApp. Abaixo estão ligações e referências juntamente com orientações para ajudá-lo com este processo.  

## <a name="migrate-your-sql-data"></a>Migrar os seus dados do SQL

Comece com [migrar uma base de dados do SQL Server para a base de dados do SQL Azure](../sql-database/sql-database-cloud-migrate.md). 

## <a name="configure-azure-remoteapp"></a>Configurar Azure RemoteApp
A aplicação do Windows no Azure RemoteApp do anfitrião. Segue-se um nível de muito alto passo a passo:

1.     Crie o [modelo de RemoteApp do Azure VM](remoteapp-imageoptions.md). 
2.     Instale a aplicação necessária na VM.
3.     Configurar a aplicação para que liga-se à base de dados SQL e confirme que funciona.
4.     Sysprep e encerramento a VM. Capture tal como uma imagem para utilização com o Azure. **Nota:** Será necessário para se certificar de que a aplicação é possível manter as informações de ligação DB durante o processo de sysprep. Se a aplicação não conseguir manter as informações de ligação DB, poderá querer participar o fornecedor da aplicação para verificar como podemos pode especificar a cadeia de ligação.
5.     Importe a imagem personalizada para a sua biblioteca do Azure RemoteApp selecionar a localização geográfica adequada que se encontra a sua implementação do SQL Azure. 
6.     Implementar uma coleção de RemoteApp no Centro de dados do mesmo como a sua implementação do SQL Azure utilizando o modelo acima e publicar a aplicação. Implementar o Azure RemoteApp no Centro de dados do mesmo como o SQL Azure implementação ajuda a garantir a velocidade de ligação mais rápida e reduzir a latência. 

## <a name="app-and-sql-configuration-considerations"></a>Considerações de configuração de aplicação e SQL:
Existem alguns pontos a ter em conta ao utilizar o Azure SQL com RemoteApp:

Saiba [como configurar uma firewall de base de dados do Azure SQL](../sql-database/sql-database-firewall-configure.md). Um excerto do artigo Estados-membros, "inicialmente, todo o acesso ao seu servidor de base de dados SQL Azure foi bloqueado pela firewall. Para poder começar a utilizar o seu servidor de base de dados do Azure SQL, tem de aceda ao Portal do clássica e especificar um ou mais regras de firewall de nível do servidor que permitem aceder ao seu servidor de base de dados do Azure SQL. Utilizar as regras de firewall para especificar quais intervalos de endereços IP da Internet são permitidos e permissão ou não aplicações Azure podem tentar ligar ao seu servidor de base de dados do Azure SQL."

Além disso, quando tenta um computador ligar ao seu servidor de base de dados da Internet, a firewall verifica se o endereço IP de origem do pedido de acordo com o conjunto completo de nível do servidor e (se necessário) nível de base de dados regras de firewall. "Se for o endereço IP do pedido de dentro de um dos intervalos especificados nas regras de firewall ao nível do servidor, a ligação é concedida ao seu servidor de base de dados do Azure SQL." Por conseguinte, podemos tornar utilização dos intervalos de IP e não apenas endereços IP de origem individuais.

Siga as instruções passo a passo no [como: Configurar definições da firewall na base de dados SQL utilizando o Portal do Azure](../sql-database/sql-database-configure-firewall-settings.md) para especificar o intervalo de IP. Quando estiver a configurar as regras de Firewall de SQL, forneça o intervalo de IP da sub-rede especificada para a coleção de RemoteApp do Azure. Isto deve permitir que os servidores de ARA ligar à base de dados SQL, apesar de tem dinamicamente-atribuídas endereços IP.

## <a name="troubleshooting"></a>Resolução de problemas
Se a experiência de utilização de uma aplicação de cliente alojado no Azure RemoteApp que se liga aos SQL de base de dados onde alojado no Azure ou no local está lenta podem existir algumas razões por que motivo.  

- Latência da rede do seu dispositivo para Azure é elevada. Mover para a ligação de rede melhor e mais rápida, pode para um melhor desempenho. Utilize [azurespeed.com](http://azurespeed.com/) como uma ferramenta de geral para testar a sua latência dispositivos ao centro de dados Azure.  
- Aplicação de cliente alojada no Azure RemoteApp está em limite. Selecionar um plano diferente do faturação como faturação Premium melhorar o desempenho. Outro truque consiste em monitorizar os recursos da aplicação está a consumir: durante uma sessão activa executar uma sequência de teclas ctrl-alt-end que iniciação ecrã SA, selecione o Gestor de tarefas e observe a utilização de recursos para a sua aplicação.
- Do SQL server está em limite ou não otimizados. Siga as orientações SQL para resolução de problemas. 

