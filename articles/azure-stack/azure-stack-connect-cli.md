<properties
    pageTitle="Ligar a pilha Azure com clip | Microsoft Azure"
    description="Saiba como utilizar a interface de comandos em diferentes plataformas (CLI) para gerir e implementar recursos na pilha de Azure"
    services="azure-stack"
    documentationCenter=""
    authors="HeathL17"
    manager="byronr"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/19/2016"
    ms.author="helaw"/>

# <a name="install-and-configure-azure-stack-cli"></a>Instalar e configurar o clip de pilha do Azure

Neste documento, iremos guiá-lo durante o processo de utilizar o Azure Interface da linha de comandos (CLI) para gerir os recursos de pilha de Azure Linux e Mac plataformas de cliente.  

## <a name="install-azure-stack-cli"></a>Instalar pilha clip do Azure

Se estiver num Mac ou Linux, pode obter o clip utilizando o seguinte comando:
  
    `npm install -g azure-cli@0.10.4`.


## <a name="connect-to-azure-stack"></a>Ligar a pilha Azure
Os passos seguintes, irá configurar Azure clip para ligar a pilha de Azure. Em seguida, inicie sessão e obter informações sobre a subscrição.

1.  Obter o valor de active directory-recurso id através da execução este PowerShell:
        
          (Invoke-RestMethod -Uri https://api.azurestack.local/metadata/endpoints?api-version=1.0 -Method Get).authentication.audiences[0]

2.  Utilize o seguinte comando clip para adicionar o ambiente do Azure pilha, certificando-se atualizar *– ativa-diretório-recurso-id* com os dados de URL obtidos no passo anterior:

           azure account env add AzureStack --resource-manager-endpoint-url "https://api.azurestack.local" --management-endpoint-url "https://api.azurestack.local" --active-directory-endpoint-url  "https://login.windows.net" --portal-url "https://portal.azurestack.local" --gallery-endpoint-url "https://portal.azurestack.local" --active-directory-resource-id "https://azurestack.local-api/" --active-directory-graph-resource-id "https://graph.windows.net/"

3.  Inicie sessão utilizando o comando seguinte (substituir *nome de utilizador* com o seu nome de utilizador):

        azure login -e AzureStack -u “<username>”

    >[AZURE.NOTE]Se estiver a obter problemas de validação de certificado, desativar validação do certificado ao executar o comando `set        NODE_TLS_REJECT_UNAUTHORIZED=0`.

4.  Defina o modo de configuração Azure para o Gestor de recursos do Azure utilizando o seguinte comando:

        azure config mode arm

5.  Obter uma lista de subscrições.

        azure account list     

## <a name="next-steps"></a>Próximos passos

[Implementar modelos com clip do Azure](azure-stack-deploy-template-command-line.md)

[Ligar-se com o PowerShell](azure-stack-connect-powershell.md)

[Gerir permissões de utilizador](azure-stack-manage-permissions.md)
