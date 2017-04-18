<properties
    pageTitle="Permitir a aplicação segredos Cofre de chave do Azure pilha de revtrieve | Microsoft Azure"
    description="Utilizar uma aplicação de exemplo para trabalhar com Azure pilha chave Cofre"
    services="azure-stack"
    documentationCenter=""
    authors="rlfmendes"
    manager="natmack"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/26/2016"
    ms.author="ricardom"/>

# <a name="run-the-sample-application-for-key-vault"></a>Executar a aplicação de exemplo para cofre chave 

Neste guia, irá utilizar uma aplicação de exemplo para obter segredos e palavras-passe do cofre chave.

## <a name="download-the-samples-and-prepare"></a>Transferir os exemplos e preparar

Transfira os exemplos de cliente do Cofre de chave do Azure a partir da [página de amostras de cliente do Azure chave cofre](https://www.microsoft.com/en-us/download/details.aspx?id=45343).

Extrai o conteúdo do ficheiro. zip o computador local.

Ler o ficheiro de **README.md** (isto é um ficheiro de texto) e, em seguida, siga as instruções.

## <a name="run-sample-1--hellokeyvault"></a>Executar exemplo #1 – HelloKeyVault
HelloKeyVault é uma aplicação de consola que explica os cenários chaves que são suportados pelo Cofre de chave:

  1. Criar/importar uma chave (HSM ou software de chave)
  2. Encriptar uma palavra-passe com uma chave de conteúdo
  3. Moldar a chave de conteúdo com uma chave de chave Cofre
  4. Moldagem a chave de conteúdo
  5. Desencriptar o segredo
  6. Definir uma palavra-passe

Aplicação consola deverá ser possível executar com sem alterações, exceto a que as definições de configuração adequada no App serão atualizadas de acordo com os seguintes passos:

1. Atualize as definições de configuração da aplicação no HelloKeyVault\App.config com o seu URL do cofre, ID da aplicação de principal e palavra-passe. As informações, opcionalmente, podem ser geradas utilizando **scripts\GetAppConfigSettings.ps1**.
2. Atualize os valores das variáveis obrigatórias na GetAppConfigSettings.ps1.
3. Inicie a janela do Windows PowerShell.
4. Execute o script GetAppConfigSettings.ps1 dentro da janela do PowerShell.
5. Copie os resultados do script para o ficheiro HelloKeyVault\App.config.


## <a name="next-steps"></a>Próximos passos

[Implementar uma VM com uma palavra-passe do Cofre de tecla](azure-stack-kv-deploy-vm-with-secret.md)

[Implementar uma VM com um certificado de chave Cofre](azure-stack-kv-push-secret-into-vm.md)