<properties
    pageTitle="Adicionar um fornecedor de recursos de aplicações Web a pilha Azure | Microsoft Azure"
    description="Orientação detalhada para a Web Apps no Azure pilha de implementação"
    services="azure-stack"
    documentationCenter=""
    authors="ccompy, apwestgarth"
    manager="stefsch"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="app-service"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/26/2016"
    ms.author="anwestg"/>

# <a name="add-a-web-apps-resource-provider-to-azure-stack"></a>Adicionar um fornecedor de recursos do Web Apps a pilha de Azure

> [AZURE.NOTE] As seguintes informações só se aplica a implementações do Azure pilha TP1.

Adicionar um fornecedor de recursos de aplicações Web a pilha de Azure tem sete passos:

1.  Transferir componentes necessários.
2.  Crie certificados para ser utilizado por aplicações Web do Azure pilha.
3.  Utilize o installer para fase, transferir e instalar o Azure pilha Web Apps. 
4.  Valide instalação de aplicações Web.
5.  Crie registos DNS para o Front-End e Balanceadores de carga Management Server.
6.  Registe o fornecedor de recurso Web Apps recentemente implementado processador.
7.  Teste de unidade o fornecedor de recursos de aplicações Web.

## <a name="download-required-components"></a>Transferir componentes necessários

1.  Transferir o [instalador do Azure pilha de aplicação de serviço de pré-visualização](http://aka.ms/azasinstaller). 
2.  Transfira os [scripts de helper de implementação do Azure pilha de aplicação de serviço](http://aka.ms/azashelper). 
3.  Extrair os ficheiros de ficheiro zip de scripts de helper, deverá haver três scripts:
    - AppServiceCerts.ps1 criar
    - AppServiceDnsRecords.ps1 criar
    - Register-AppServiceResourceProvider.ps1 

## <a name="create-certificates-to-be-used-by-azure-stack-web-apps"></a>Criar certificados para ser utilizado por Azure pilha Web Apps

Este script primeiro funciona com a autoridade de certificação Azure pilha para criar 3 certificados que são necessários por aplicações Web. Execute o script no ClientVM garantindo que estiver a utilizar o PowerShell como azurestack\administrator:
1.  Numa sessão de PowerShell em execução como **azurestack\administrator**, execute o script **AppServiceCerts.ps1 de criar** .  Esta ação cria, três certificados na mesma pasta como o script, que são necessários por aplicações Web.
2.  Introduza uma palavra-passe para proteger os ficheiros de pfx e tome nota do mesmo à medida que vai necessitar de introduzi-lo no instalador de aplicações Web do Azure pilha.

## <a name="use-the-installer-to-download-and-install-azure-stack-web-apps"></a>Utilizar o installer para transferir e instalar o Azure pilha Web Apps

O instalador do appservice.exe irá:
1.  Perguntar ao utilizador para aceitar o Microsoft e terceiros EULA.
2.  Recolha informações de implementação do Azure pilha.
3.  Crie um contentor blob na conta Azure pilha de armazenamento especificada.
4.  Transferir os ficheiros necessários para instalar o fornecedor de recursos do Azure pilha Web App.
5.  Prepare a instalação para implementar o fornecedor de recursos do Web App no ambiente do Azure pilha.
6.  Carregue os ficheiros para a conta de armazenamento de aplicação de serviço.
7.  Apresentar as informações necessárias para iniciar o modelo de Gestor de recursos do Azure.

Os passos seguintes orientá-lo através das fases de instalação:

>[AZURE.NOTE] Tem de utilizar uma conta elevada (administrador local ou de domínio) para executar o instalador. Se iniciar sessão como azurestack\azurestackuser, será pedido para introduzir as credenciais elevadas. 

1.  Execute appservice.exe como **azurestack\administrator**. 
2.  Clique em **implementar utilizando o Gestor de recursos do Azure**.

![Installer de pré-visualização técnica 1 de aplicação de serviço de pilha Azure][1]

3.  Rever e aceitar os termos de licenciamento do pré-lançamento de Software Microsoft e, em seguida, clique em **seguinte**.
4.  Reveja e aceite os termos de partylicense terceiros e, em seguida, clique em **seguinte**.
5.  Reveja as informações de configuração da aplicação de serviço de nuvem e clique em **seguinte**.

![Configuração da nuvem de pilha Azure aplicação de serviço pré-visualização técnica 1 aplicação de serviço][2]

6. Clique em **Ligar** (junto à caixa Azure pilha subscrições).

![Pilha Azure aplicação de serviço pré-visualização técnica 1 aplicação de serviço na nuvem ecrã de configuração duas][3]

7.  Na janela do Azure pilha autenticação fornecer a sua **conta de administrador de serviços do Azure Active Directory** e a **palavra-passe**e, em seguida, clique em **Iniciar sessão**.
**Nota:** Esta é a conta do Azure Active Directory que forneceu quando implementado o Azure pilha.
    - Clique na **Seta para baixo** no lado direito da caixa ao lado de **Subscrições de pilha Azure** e, em seguida, selecione a sua subscrição.

![Seleção de subscrição de pré-visualização técnica 1 de aplicação de serviço de pilha Azure][5]

8.  Clique na **Seta para baixo** no lado direito da caixa ao lado do **Azure pilha localizações**.
    - Selecione o **Local**.
9. Introduza o **nome** para o seu administrador.
10. Introduza uma **palavra-passe** para o administrador.
11. Reveja os **Detalhes do SQL Server** e efetuar alterações, se necessário.
12. Reveja a **Conta de início de sessão SysAdmin** e efetuar alterações, se necessário.
13. Introduza a **Palavra-passe de SysAdmin**.
14. Clique em **seguinte**.  Neste momento o instalador do agora Verifique os detalhes de ligação para o SQL Server fornecidos.

![Seleção de subscrição de pré-visualização técnica 1 de aplicação de serviço de pilha Azure][4]    

15. Clique em **Procurar** junto do **Ficheiro de certificado de SSL de predefinido aplicações Web** e navegue para a webapps **. AzureStack.Local** certificado que [criou anteriormente](#Create-Certificates-To-Be-Used-By-Azure-Stack-Web-Apps).
16. Introduza a **palavra-passe de certificado** definida por si quando criou os certificados.
17. Clique em **Procurar** junto do **Ficheiro de certificado SSL do recurso fornecedor** e navegue para a gestão de **. AzureStack.Local** certificado que [criou anteriormente](#Create-Certificates-To-Be-Used-By-Azure-Stack-Web-Apps).
18. Introduza a **palavra-passe de certificado** definida por si quando criou os certificados.
19. Clique em **Procurar** junto do **Ficheiro de certificado de raiz do recurso fornecedor** e navegue para a gestão de **. AzureStack.Local** certificado que [criou anteriormente](#Create-Certificates-To-Be-Used-By-Azure-Stack-Web-Apps).
20. Clique em **seguinte** o instalador verificará a palavra-passe de certificado fornecida.

![Detalhes do certificado de pré-visualização técnica 1 de aplicação de serviço de pilha Azure][6]

A implementação irá demorar cerca de 45-60 minutos a concluir.

![Progresso da instalação do Azure pilha aplicação de serviço pré-visualização técnica 1][7]

21. Quando o programa de instalação for concluída com êxito, clique em **Sair**.

## <a name="validate-web-apps-installation"></a>Validar instalação de aplicações Web

1.  No seu **Computador de anfitrião do Azure pilha de** abrir o **Gestor de Hyper-V**.
2.  Localize os **CN0 VM** e **ligar-se** para a VM.
![Aplicação de serviço de pilha Azure pré-visualização técnica 1 Gestor de Hyper-V][8]
3.  No ambiente de trabalho deste VM, faça duplo clique a **Web consola de gestão da nuvem**.
![Consola de gestão de pré-visualização técnica 1 de aplicação de serviço de pilha Azure][9]
4.  Navegue para **servidores geridos**.
5.  Quando todas as máquinas **pronto** avançar para o próximo passo. 
![Estado de servidores geridos 1 de pré-visualização técnica de aplicação de serviço de pilha Azure][10]

## <a name="create-dns-records-for-the-management-server-and-front-end-load-balancers"></a>Criar registos DNS para o servidor de gestão e Balanceadores de carga de Front-End
1.  Abra uma instância do PowerShell como **azurestack\administrator**.
2.  Navegue para a localização dos scripts transferida e extraída no [passo de pré-requisito](#Download-Required-Components).
3.  Executar o script **Criar AppServiceDnsRecords.ps1** , esta ação cria entradas de DNS para permitir os pedidos de aplicação portal e web a ser encaminhado para a servidores front-End.  Durante a implementação de processador de aplicações Web, dois balanceadores de carga de Software (SLBs) criados no fornecedor de recursos de rede. Estes apontem para os servidores de gestão e de servidores front-End. O portal e os pedidos de processador com base para o fornecedor de serviços de aplicação do Azure pilha de recurso Ir para o servidor de gestão.

## <a name="register-the-newly-deployed-azure-stack-web-apps-provider-with-arm"></a>Registar o fornecedor de Azure pilha Web Apps recentemente implementado com braço
1.  Abra uma instância do PowerShell como **azurestack\administrator**.
2.  Navegue para a localização dos scripts transferida e extraída no [passo de pré-requisito](#Download-Required-Components).
3.  Execute o script **Register AppServiceResourceProvider.ps1** . 

>[AZURE.NOTE] Escreva o nome de utilizador e palavra-passe **EXATAMENTE (incluindo maiúsculas e minúsculas)** à medida que foi introduzida para o **Administrador de Virtual Machine(s)** e os campos de **palavra-passe** na instalação ou o registo de fornecedor do recurso irá falhar.

## <a name="test-drive-azure-stack-web-apps"></a>Teste unidade pilha Azure Web Apps

Agora que tiver implementado e registou o fornecedor de recursos do Web Apps, pode testá-la para se certificar de que os inquilinos podem implementar web apps.

1.  No portal do Azure pilha, clique em novo, clique em Web + Mobile e clique em aplicação Web.
2.  Na pá Web App, escreva um nome na caixa de aplicação Web.
3.  Em grupo de recursos, clique em novo e, em seguida, escreva um nome na caixa de grupo de recursos. 
4.  Clique em plano/localização do serviço de aplicação e clique em criar novo.
5.  Na pá de plano de serviço de aplicação, escreva um nome na caixa de plano de serviço de aplicação.
6.  Clique em preços de camadas, clique em Free partilhado ou partilhado partilhado, clique em selecionar, clique em OK e, em seguida, clique em criar.
7.  Num minuto um mosaico para a nova aplicação web será apresentado no Dashboard. Clique no mosaico.
8.  Na pá de aplicação web, clique em Procurar para ver o Web site predefinido para esta aplicação.


**Implementar um WordPress, DNN ou Django Web site (opcional)**

1. No portal do Azure pilha, clique em "+", aceda ao Azure Marketplace, implementar um Web site Django e aguarde pela conclusão com êxito. A plataforma de web Django utiliza um ficheiro com base no sistema base de dados e não necessita de todos os fornecedores de recursos adicionais, como SQL ou MySQL.  

2. Se também implementado um fornecedor de recursos do MySQL, pode implementar um Web site WordPress de mercado. Quando lhe for pedido para os parâmetros de base de dados, introduzir o nome de utilizador como *User1@Server1* (com o nome de utilizador e o nome do servidor da sua preferência).

3. Se também implementado um fornecedor de recursos do SQL Server, pode implementar um Web site DNN de mercado. Quando lhe for pedido para os parâmetros de base de dados, escolha uma base de dados no computador com o SQL Server que está ligado ao seu fornecedor de recursos.

## <a name="next-steps"></a>Próximos passos

Também pode experimentar outros [plataforma como um serviço (PaaS) serviços](azure-stack-tools-paas-services.md), como o [fornecedor de recursos do SQL Server](azure-stack-sql-rp-deploy-short.md) ou o [fornecedor de recursos do MySQL](azure-stack-mysql-rp-deploy-short.md).

<!--Image references-->
[1]: ./media/azure-stack-webapps-deploy/AppService_exe_Start.png
[2]: ./media/azure-stack-webapps-deploy/AppService_exe_DefaultEntriesStep1.png
[3]: ./media/azure-stack-webapps-deploy/AppService_exe_DefaultEntriesStep2.png
[4]: ./media/azure-stack-webapps-deploy/AppService_exe_DefaultEntriesStep2_populated.png
[5]: ./media/azure-stack-webapps-deploy/AppService_exe_DefaultEntriesStep2_SubscriptionSelection.png
[6]: ./media/azure-stack-webapps-deploy/AppService_exe_DefaultEntriesStep3_Certificates.png
[7]: ./media/azure-stack-webapps-deploy/AppService_exe_InstallationProgress.png
[8]: ./media/azure-stack-webapps-deploy/HyperV.png
[9]: ./media/azure-stack-webapps-deploy/MMC.png
[10]: ./media/azure-stack-webapps-deploy/ManagedServers.png


<!--Links-->
[Azure_Stack_App_Service_preview_installer]: http://go.microsoft.com/fwlink/?LinkID=717531
[WebAppsDeployment]: http://go.microsoft.com/fwlink/?LinkId=723982
[AppServiceHelperScripts]: http://go.microsoft.com/fwlink/?LinkId=733525
