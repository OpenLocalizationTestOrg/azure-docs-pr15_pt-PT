<properties
    pageTitle="Ligar à base de dados SQL utilizando o SQL Server Management Studio no Azure RemoteApp | Microsoft Azure"
    description="Utilize este tutorial para saber como utilizar o SQL Server Management Studio no Azure RemoteApp para maior segurança e desempenho quando ligar à base de dados SQL"
    services="sql-database"
    documentationCenter=""
    authors="adhurwit"
    manager="jhubbard"/>

<tags
    ms.service="sql-database"
    ms.workload="data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/05/2016"
    ms.author="adhurwit"/>

# <a name="use-sql-server-management-studio-in-azure-remoteapp-to-connect-to-sql-database"></a>Utilizar o SQL Server Management Studio no Azure RemoteApp para ligar à base de dados SQL

## <a name="introduction"></a>Introdução  
Este tutorial mostra-lhe como utilizar o SQL Server Management Studio (SSMS) no Azure RemoteApp para ligar à base de dados SQL. Orienta-pelo processo de configuração do SQL Server Management Studio no Azure RemoteApp, explica as vantagens e mostra as funcionalidades de segurança que pode utilizar no Azure Active Directory.

**Estimativa tempo a concluir:** 45 minutos

## <a name="ssms-in-azure-remoteapp"></a>SSMS no Azure RemoteApp

Azure RemoteApp é um serviço RDS no Azure que proporciona aplicações. Pode obter mais informações sobre o mesmo aqui: [o que é o RemoteApp?](../remoteapp/remoteapp-whatis.md)

SSMS em execução no Azure RemoteApp dá-lhe a mesma experiência como SSMS a ser executado localmente.

![Captura de ecrã a mostrar SSMS a ser executada em RemoteApp do Azure][1]



## <a name="benefits"></a>Benefícios

Existem várias vantagens em utilizar SSMS no RemoteApp do Azure, incluindo:

- Porta 1433 no Azure SQL Server não tem de estar expostos externamente (fora do Azure).
- Não é necessário para manter adicionar e remover endereços IP na firewall do Azure SQL Server.
- Todas as ligações de RemoteApp do Azure ocorrem ao longo do HTTPS sobre a utilização da porta 443 encriptados protocolo de ambiente de trabalho remoto
- É vários utilizador e pode dimensionar.
- Existe um ganho de desempenho a partir tendo SSMS na mesma região como a base de dados do SQL.
- É possível auditar utilização do Azure RemoteApp com a edição de Premium do Azure Active Directory que tenha relatórios de atividade de utilizador.
- Pode ativar a autenticação multifator (MFA).
- Acesso SSMS em qualquer lugar quando utilizar qualquer um dos clientes RemoteApp do Azure suportados que inclui iOS, Android, Mac, Windows Phone e Windows PC.


## <a name="create-the-azure-remoteapp-collection"></a>Criar a coleção de RemoteApp do Azure

Eis os passos para criar a sua coleção de RemoteApp do Azure com SSMS:


### <a name="1-create-a-new-windows-vm-from-image"></a>1. Crie um novo VM do Windows da imagem
Utilize a imagem de "Windows servidor remoto ambiente de trabalho sessão anfitrião Windows Server 2012 R2" a partir da Galeria para tornar o seu novo VM.


### <a name="2-install-ssms-from-sql-express"></a>2. instalar SSMS SQL Express

Ir para a nova VM e navegue para esta página de transferência: [Microsoft® SQL Server® 2014 Express](https://www.microsoft.com/en-us/download/details.aspx?id=42299)

Existe uma opção para transferir apenas SSMS. Depois de transferência, ir para o diretório de instalar e executar a configuração para instalar SSMS.

Também precisa de instalar o SQL Server 2014 Service Pack 1. Pode transferi-lo aqui: [Microsoft SQL Server 2014 Service Pack 1 (SP1)](https://www.microsoft.com/en-us/download/details.aspx?id=46694)

SQL Server 2014 Service Pack 1 inclui uma funcionalidade essencial para trabalhar com a base de dados do SQL Azure.


### <a name="3-run-validate-script-and-sysprep"></a>3. executar scripts de validar e Sysprep

No ambiente de trabalho da VM um script PowerShell denomina-se para validar. Execute este fazendo duplo clique. Vai verificar que a VM está pronta a ser utilizado para alojar remoto de aplicações. Quando verificação estiver concluída, irá pedir para executar o sysprep - selecionar executá-la.

Quando concluir sysprep, irá encerrar a VM.

Para saber mais sobre a criação de uma imagem de RemoteApp do Azure, consulte o artigo: [como criar uma imagem do modelo RemoteApp no Azure](http://blogs.msdn.com/b/rds/archive/2015/03/17/how-to-create-a-remoteapp-template-image-in-azure.aspx)


### <a name="4-capture-image"></a>4. capturar a imagem

Quando a VM porque deixou de funcionar, encontrá-la no portal do atual e capturá-lo.

Para saber mais sobre capturar uma imagem, consulte o artigo [capturar uma imagem de uma máquina de virtual do Azure Windows criada com o modelo clássico de implementação](../virtual-machines/virtual-machines-windows-classic-capture-image.md)


### <a name="5-add-to-azure-remoteapp-template-images"></a>5. Adicionar imagens de modelos de RemoteApp do Azure

Na secção Azure RemoteApp do portal atual, aceda ao separador imagens de modelos e clique em Adicionar. Na caixa de pop-up, selecione "Importar uma imagem a partir da sua biblioteca máquinas virtuais" e, em seguida, selecione a imagem que acabou de criar.



### <a name="6-create-cloud-collection"></a>6. criar coleção de nuvem

No portal do atual, crie uma nova coleção de nuvem do RemoteApp do Azure. Selecione a imagem de modelo que acabou de importar com SSMS instalado.

![Criar nova coleção de nuvem][2]


### <a name="7-publish-ssms"></a>7. publicar SSMS

Na publicação separador da sua coleção de nuvem novo, selecione publicar uma aplicação a partir do Menu Iniciar e, em seguida, selecione SSMS a partir da lista.

![Publicar a aplicação][5]

### <a name="8-add-users"></a>8. Adicionar utilizadores

No separador de acesso do utilizador pode selecionar os utilizadores que terão acesso a esta coleção de RemoteApp do Azure que inclui apenas SSMS.

![Adicionar utilizador][6]


### <a name="9-install-the-azure-remoteapp-client-application"></a>9. instalar a aplicação de cliente RemoteApp do Azure

Pode transferir e instalar um cliente RemoteApp do Azure aqui: [Transferir | Azure RemoteApp](https://www.remoteapp.windowsazure.com/en/clients.aspx)



## <a name="configure-azure-sql-server"></a>Configurar o Azure SQL Server

É a única configuração necessário para se certificar de que dos serviços do Azure estiver ativado para a firewall. Se utilizar esta solução, não é necessário adicionar quaisquer endereços IP para abrir a firewall. É o tráfego de rede que é permitido para o SQL Server a partir de outros serviços do Azure.


![Permitir do Azure][4]



## <a name="multi-factor-authentication-mfa"></a>Autenticação Multifator (MFA)

MFA pode ser ativado especificamente para esta aplicação. Aceda ao separador aplicações do Azure Active Directory. Encontrará uma entrada para RemoteApp do Microsoft Azure. Se clicar nessa aplicação e, em seguida, configurar, irá ver a página abaixo, onde pode ativar MFA para esta aplicação.

![Ativar MFA][3]



## <a name="audit-user-activity-with-azure-active-directory-premium"></a>Atividade de utilizador de auditoria com o Azure Active Directory Premium

Se não tiver Azure AD Premium, em seguida, tem de ativá-la na secção licenças do seu diretório. Com o Premium ativado, pode atribuir aos utilizadores para o nível de Premium.

Ao aceder a um utilizador no seu Azure Active Directory, em seguida, pode aceder ao separador atividade para ver informações de início de sessão para RemoteApp do Azure.



## <a name="next-steps"></a>Próximos passos

Depois de concluir os passos acima, será conseguir executar o cliente RemoteApp do Azure e início de sessão, com um utilizador atribuído. Será apresentada com SSMS como uma das suas aplicações e pode executá-la como faria se estivesse instalado no seu computador com acesso ao Azure SQL Server.

Para obter mais informações sobre como efetuar a ligação à base de dados do SQL, consulte o artigo [ligar à base de dados SQL com SQL Server Management Studio e executar uma consulta de T-SQL exemplo](sql-database-connect-query-ssms.md).


Tudo o que é por agora. Desfrute!



<!--Image references-->
[1]: ./media/sql-database-ssms-remoteapp/ssms.png
[2]: ./media/sql-database-ssms-remoteapp/newcloudcollection.png
[3]: ./media/sql-database-ssms-remoteapp/mfa.png
[4]: ./media/sql-database-ssms-remoteapp/allowazure.png
[5]: ./media/sql-database-ssms-remoteapp/publish.png
[6]: ./media/sql-database-ssms-remoteapp/user.png
