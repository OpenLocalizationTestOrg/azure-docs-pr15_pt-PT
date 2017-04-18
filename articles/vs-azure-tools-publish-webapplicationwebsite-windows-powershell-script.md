<properties
   pageTitle="Publicar WebApplicationWebSite (script do Windows PowerShell) | Microsoft Azure"
   description="Saiba como publicar um projeto de web para um Web site Azure. Este script cria os recursos necessários na sua subscrição do Azure caso não existam."
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# <a name="publish-webapplicationwebsite-windows-powershell-script"></a>Publicar WebApplicationWebSite (script do Windows PowerShell)

##<a name="syntax"></a>Sintaxe

Publica um projecto da web para um Web site Azure. O script cria os recursos necessários na sua subscrição do Azure caso não existam.

    Publish-WebApplicationWebSite
    –Configuration <configuration>
    -SubscriptionName <subscriptionName>
    -WebDeployPackage <packageName>
    -DatabaseServerPassword @{Name = "name"; Password = "password"}
    -SendHostMessagesToOutput
    -Verbose


## <a name="configuration"></a>Configuração

O caminho para o ficheiro de configuração de JSON que descreva os detalhes da implementação.

|Parâmetro|Valor predefinido|
|---|---|
|Aliases|nenhum|
|Obrigatório?|VERDADEIRO|
|Posição|com o nome|
|Valor predefinido|nenhum|
|Aceitar a entrada de tubagem?|FALSO|
|Aceitar carateres universais?|FALSO|

## <a name="subscriptionname"></a>SubscriptionName

O nome da subscrição do Azure ao qual pretende criar o Web site no.

|Parâmetro|Valor predefinido|
|---|---|
|Aliases|nenhum|
|Obrigatório?|FALSO|
|Posição|com o nome|
|Valor predefinido|nenhum|
|Aceitar a entrada de tubagem?|FALSO|
|Aceitar carateres universais?|FALSO|

## <a name="webdeploypackage"></a>WebDeployPackage

O caminho para o pacote de implementação de web para publicar o Web site. Pode criar este pacote utilizando o Assistente de Web publicar no Visual Studio. Para mais informações, consulte o artigo [Introdução aos serviços em nuvem Azure e ASP.NET](http://go.microsoft.com/fwlink/p/?LinkID=623089).

|Parâmetro|Valor predefinido|
|---|---|
|Aliases|nenhum|
|Obrigatório?|FALSO|
|Posição|com o nome|
|Valor predefinido|nenhum|
|Aceitar a entrada de tubagem?|FALSO|
|Aceitar carateres universais?|FALSO|

## <a name="databaseserverpassword"></a>DatabaseServerPassword

O nome de utilizador e palavra-passe para a base de dados SQL Azure.

|Parâmetro|Valor predefinido|
|---|---|
|Aliases|nenhum|
|Obrigatório?|FALSO|
|Posição|com o nome|
|Valor predefinido|nenhum|
|Aceitar a entrada de tubagem?|FALSO|
|Aceitar carateres universais?|FALSO|

## <a name="sendhostmessagestooutput"></a>SendHostMessagesToOutput

Se for VERDADEIRO, imprimir mensagens são o script na sequência de saída.

|Parâmetro|Valor predefinido|
|---|---|
|Aliases|nenhum|
|Obrigatório?|FALSO|
|Posição|com o nome|
|Valor predefinido|FALSO|
|Aceitar a entrada de tubagem?|FALSO|
|Aceitar carateres universais?|FALSO|

## <a name="remarks"></a>Observações

Para obter uma explicação completa de como utilizar o script para criar ambientes de teste de verificação e Dev Center, consulte o artigo [Utilizar o Windows PowerShell Scripts para publicar para Dev Center e ambientes de teste](vs-azure-tools-publishing-using-powershell-scripts.md).

O ficheiro de configuração de JSON Especifica os detalhes do que está a ser implementada. Inclui as informações que especificou quando criou o projeto, tal como o nome e o nome de utilizador para o Web site. Também inclui a base de dados a aprovisionar, se existirem. O código seguinte mostra um ficheiro de configuração de JSON exemplo:

    {
        "environmentSettings": {
            "webSite": {
                "name": "WebApplication10554",
                "location": "West US"
            },
            "databases": [
                {
                    "connectionStringName": "DefaultConnection",
                    "databaseName": "WebApplication10554_db",
                    "serverName": "iss00brc88",
                    "user": "sqluser2",
                    "password": "",
                    "edition": "",
                    "size": "",
                    "collation": "",
                    "location": "West US"
                }
            ]
        }
    }

Pode editar o ficheiro de configuração de JSON para alterar o que é implementado. É necessária uma secção do Web site, mas a secção de base de dados é opcional.

## <a name="next-steps"></a>Próximos passos

Para obter mais informações, consulte [Publicar-WebApplicationVM (script do Windows PowerShell)](vs-azure-tools-publish-webapplicationvm.md)
