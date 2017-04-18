<properties
    pageTitle="Utilizar bases de dados do MySQL como PaaS na pilha de Azure | Microsoft Azure"
    description="Compreenda os passos rápidos para implementar o fornecedor de recursos do MySQL e fornecer MySQL como um serviço na pilha de Azure."
    services="azure-stack"
    documentationCenter=""
    authors="Dumagar"
    manager="bradleyb"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/26/2016"
    ms.author="dumagar"/>

# <a name="use-mysql-databases-as-paas-on-azure-stack"></a>Utilizar bases de dados do MySQL como PaaS na pilha de Azure

> [AZURE.NOTE] As seguintes informações só se aplica a implementações do Azure pilha TP1.

Pode implementar um fornecedor de recursos do MySQL na pilha de Azure. Depois de implementar o fornecedor de recursos, pode criar os servidores de MySQL e bases de dados através de modelos de implementação do Gestor de recursos do Azure e fornecer bases de dados do MySQL como um serviço. Bases de dados do MySQL, que são comuns em web sites, suportam muitas plataformas de Web site. Depois de implementar o fornecedor de recursos, pode criar Web sites de WordPress da plataforma do Azure Web Apps como um suplemento do serviço (PaaS) para Azure pilha.

## <a name="quick-steps-to-deploy-the-resource-provider"></a>Passos rápidos para implementar o fornecedor de recursos
Utilize estes passos se ainda estiver familiarizado com o Azure pilha. Se pretender obter mais detalhes, siga as hiperligações em cada secção ou ir diretamente para [Implementar a MySQL da base de dados recursos fornecedor placa de Azure pilha conceito](azure-stack-mysql-rp-deploy-long.md).

1.  Certifique-se de que não [concluir todos os passos de configuração](azure-stack-mysql-rp-deploy-long.md#set-up-steps-before-you-deploy) antes de implementar o fornecedor de recurso:

    - 3,5 .NET framework já está configurado na imagem do Windows Server base. (Se tiver transferido os bits Azure pilha depois 23 de Fevereiro de de 2016, pode ignorar este passo.)
    - [Uma versão do Azure PowerShell, que é compatível com o Azure pilha está instalada](http://aka.ms/azStackPsh).
    - Nas definições de segurança do Internet Explorer no ClientVM, [o Internet Explorer segurança melhorada está desativada e os cookies estão activados](azure-stack-mysql-rp-deploy-long.md#Turn-off-IE-enhanced-security-and-enable-cookies).

2. [Transferir o ficheiro de binários do MySQL recurso fornecedor](http://aka.ms/masmysqlrp) e extrai-lo no ClientVM na pilha Azure prova do conceito.

3. [Executar bootstrap.cmd e os scripts](azure-stack-mysql-rp-deploy-long.md#Bootstrap-the-resource-provider-deployment-PowerShell-and-Prepare-for-deployment).

    Um conjunto de scripts que é agrupado por dois separadores principais abre-se na integrada Scripting ambiente (ISe do PowerShell). Executar os scripts carregados sequência da esquerda para a direita em cada separador.

    1. Execute scripts no separador **preparar** da esquerda para a direita para:

        - Crie um certificado de caracteres universais para assegurar a comunicação entre o fornecedor de recursos e o Gestor de recursos do Azure.
        - Aceite os termos do MySQL EULA e transferir os binários do MySQL.
        - Carregue os certificados e todos os outros artefactos para uma conta de armazenamento do Azure pilha.
        - Publica pacotes de Galeria de modo a que pode implementar MySQL recursos através da galeria.

        > [AZURE.IMPORTANT] Se qualquer um dos scripts deixar de responder por nenhum motivo evidente após submeter o seu inquilino do Azure Active Directory, as definições de segurança poderão estar a bloquear uma DLL necessário para a implementação para executar. Para resolver este problema, procure Microsoft.AzureStack.Deployment.Telemetry.Dll na sua pasta do fornecedor de recursos, botão direito do rato clique na mesma, clique em **Propriedades**e, em seguida, selecione **Desbloquear** no separador **Geral** .

    2. Execute scripts no separador **Implementar** da esquerda para a direita para:

        - [Implementar uma máquina de virtual (VM)](azure-stack-mysql-rp-deploy-long.md#Deploy-the-MySQLResource-Provider-VM) que irá alojar o seu fornecedor de recursos, MySQL servidores e bases de dados que irá criar uma instância. Este script faz referência a um ficheiro de parâmetro JSON, que necessita de atualizar com alguns valores antes de executar o script.
        - [Registe-se um registo DNS local](azure-stack-mysql-rp-deploy-long.md#Update-the-local-DNS) que serão mapeadas para o seu fornecedor de recurso VM.
        - [Registe-se o seu fornecedor de recurso](azure-stack-mysql-rp-deploy-long.md#Register-the-MySQL-RP-Resource-Provider) com o Gestor de recursos de Azure local.

        > [AZURE.IMPORTANT] Todos os scripts partem do pressuposto de que a imagem do sistema operativo base cumpra os pré-requisitos (.NET 3.5 instalado, JavaScript e cookies activados na ClientVM e a versão mais recente do PowerShell Azure instalado). Se obtiver erros ao executar os scripts, verifique que preenchidas os pré-requisitos.

5. Para [testar o seu fornecedor de recursos do MySQL novo](/azure-stack-MySql-rp-deploy-long.md#create-your-first-mysql-database-to=test-your-deployment), implemente uma base de dados do MySQL a partir do portal do Azure pilha. Clique em **Criar** &gt; **personalizada** &gt; **servidor do MySQL precisar e base de dados**.

Isto deve obter o seu fornecedor de recursos do MySQL para cima e a ser executada em cerca de 25 minutos.
