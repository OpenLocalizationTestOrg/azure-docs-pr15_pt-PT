<properties 
    pageTitle="Gerir contas de serviços de multimédia do Microsoft Azure com PowerShell" 
    description="Saiba como gerir contas dos serviços de multimédia do Azure com os cmdlets do PowerShell." 
    authors="Juliako" 
    manager="erikre" 
    editor="" 
    services="media-services" 
    documentationCenter=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/03/2016"
    ms.author="juliako"/>


#<a name="manage-azure-media-services-accounts-with-powershell"></a>Gerir contas de serviços de multimédia do Microsoft Azure com PowerShell

> [AZURE.SELECTOR]
- [Portal](media-services-portal-create-account.md)
- [PowerShell](media-services-manage-with-powershell.md)
- [RESTO](http://msdn.microsoft.com/library/azure/dn194267.aspx)

> [AZURE.NOTE] Para poder criar uma conta de serviços de multimédia do Azure, tem de ter uma conta Azure. Se não tiver uma conta, pode criar uma conta de avaliação gratuita apenas de duas minutos. Para obter detalhes, consulte o artigo <a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A8A8397B5" target="_blank">Versão de avaliação gratuita do Azure</a>.

##<a name="overview"></a>Descrição geral 

Este artigo lista os cmdlets do Azure PowerShell para serviços de multimédia do Azure (MGA) no quadro Azure o Gestor de recursos. Os cmdlets existe no espaço de nomes **Microsoft.Azure.Commands.Media** .

## <a name="versions"></a>Versões

**ApiVersion**: "2015-10-01"
               

## <a name="new-azurermmediaservice"></a>Novo AzureRmMediaService

Cria um serviço de multimédia.

### <a name="syntax"></a>Sintaxe

Parâmetro definido: StorageAccountIdParamSet

    New-AzureRmMediaService [-ResourceGroupName] <string> [-AccountName] <string> [-Location] <string> [-StorageAccountId] <string> [-Tags <hashtable>]  [<CommonParameters>]

Parâmetro definido: StorageAccountsParamSet

    New-AzureRmMediaService [-ResourceGroupName] <string> [-AccountName] <string> [-Location] <string> [-StorageAccounts] <PSStorageAccount[]> [-Tags <hashtable>]  [<CommonParameters>]

### <a name="parameters"></a>Parâmetros

**-ResourceGroupName &lt;cadeia&gt;**

Especifica o nome do grupo de recursos ao qual pertence este serviço de multimédia.

Aliases | nenhum
---|---
Obrigatório?   |  VERDADEIRO
Posição?   |  0
Valor predefinido |nenhum
Aceitar a entrada de tubagem? |TRUE(ByPropertyName)
Aceitar carateres universais?  |FALSO

**-AccountName &lt;cadeia&gt;**

Especifica o nome do serviço de multimédia.

Aliases |Nome
---|---
Obrigatório? |VERDADEIRO
Posição? |1
Valor predefinido |nenhum
Aceitar a entrada de tubagem? |FALSO
Aceitar carateres universais? |FALSO

**-Localização &lt;cadeia&gt;**

Especifica a localização de recursos do serviço de multimédia.

Aliases |nenhum
---|---
Obrigatório? |VERDADEIRO
Posição? |2
Valor predefinido  |nenhum
Aceitar a entrada de tubagem? |TRUE(ByPropertyName)
Aceitar carateres universais? |FALSO

**-StorageAccountId &lt;cadeia&gt;**

Especifica uma conta de armazenamento primário associado ao serviço de multimédia.

- Nova armazenamento conta (criada com a API do Gestor de recursos) suportada apenas.

- A conta de armazenamento tem de existir previamente e tem a mesma localização com o serviço de multimédia.

Aliases |nenhum
---|---
Obrigatório? |VERDADEIRO
Posição? |3
Valor predefinido  |nenhum
Aceitar a entrada de tubagem? |TRUE(ByPropertyName)
Nome do conjunto de parâmetro |StorageAccountIdParamSet
Aceitar carateres universais?|FALSO

**-StorageAccounts &lt;PSStorageAccount\[\]&gt;**

Especifica contas de armazenamento que associado ao serviço de multimédia.

- Nova armazenamento conta (criada com a API do Gestor de recursos) suportada apenas.

- A conta de armazenamento tem de existir previamente e tem a mesma localização com o serviço de multimédia.

- Apenas a uma conta de armazenamento pode ser especificada como principal.

Aliases |nenhum
---|---
Obrigatório?  |VERDADEIRO
Posição?  |3
Valor predefinido |nenhum
Aceitar a entrada de tubagem? |TRUE(ByPropertyName)
Nome do conjunto de parâmetro |StorageAccountsParamSet
Aceitar carateres universais? |FALSO

**-Etiquetas &lt;Hashtable&gt;**

Especifica uma tabela de hash as etiquetas que estão associadas com o serviço de multimédia.

- Exemplo:@{"tag1"="value1";"tag2"=:value2"}

Aliases |nenhum
---|---
Obrigatório?  |FALSO
Posição?  |com o nome
Valor predefinido |nenhum
Aceitar a entrada de tubagem? |FALSO
Aceitar carateres universais? |FALSO

**&lt;Parâmetrosdocomando&gt;**

Este cmdlet suporta os parâmetros comuns:-depurar, - ErrorAction, - ErrorVariable, - InformationAction, - InformationVariable, - OutVariable,-OutBuffer, - PipelineVariable, - verboso, - WarningAction e - WarningVariable.

### <a name="inputs"></a>Entradas do tipo

O tipo de entrada é o tipo dos objetos que pode encaminhar ao cmdlet.

### <a name="outputs"></a>Resultados de

O tipo de saída é o tipo dos objetos que o cmdlet emite.

## <a name="set-azurermmediaservice"></a>Definir AzureRmMediaService

Atualiza um serviço de multimédia.

### <a name="syntax"></a>Sintaxe

    Set-AzureRmMediaService [-ResourceGroupName] <string> [-AccountName] <string> [-Tags <hashtable>] [-StorageAccounts <PSStorageAccount[]>]  [<CommonParameters>]

### <a name="parameters"></a>Parâmetros

**-ResourceGroupName &lt;cadeia&gt;**

Especifica o nome do grupo de recursos ao qual pertence este serviço de multimédia.

Aliases |nenhum
---|---
Obrigatório?  |VERDADEIRO
Posição?  |0
Valor predefinido |nenhum
Aceitar a entrada de tubagem? |TRUE(ByPropertyName)
Aceitar carateres universais? |FALSO

**-AccountName &lt;cadeia&gt;**

Especifica o nome do serviço de multimédia.

Aliases |Nome
---|---
Obrigatório? |VERDADEIRO
Posição? |1
Valor predefinido |Nenhum
Aceitar a entrada de tubagem? |TRUE(ByPropertyName)
Aceitar carateres universais? |FALSO

**-StorageAccounts &lt;PSStorageAccount\[\]&gt;**

Especifica contas de armazenamento que associado ao serviço de multimédia.

- Nova armazenamento conta (criada com a API do Gestor de recursos) suportada apenas.

- A conta de armazenamento tem de existir previamente e tem a mesma localização com o serviço de multimédia.

- Apenas a uma conta de armazenamento pode ser especificada como principal.

Aliases |nenhum
---|---
Obrigatório? |FALSO
Posição? |Com o nome
Valor predefinido |nenhum
Aceitar a entrada de tubagem? |TRUE(ByPropertyName)
Nome do conjunto de parâmetro |StorageAccountsParamSet
Aceitar carateres universais? |FALSO

**-Etiquetas &lt;Hashtable&gt;**

Especifica uma tabela de hash de etiquetas de dados que estão associadas este serviço de multimédia.

- As etiquetas de dados que estão associadas com o serviço de multimédia são substituídas por valor especificado pelo cliente.

Aliases |nenhum
---|---
Obrigatório? |FALSO
Posição?  |Com o nome
Valor predefinido |Nenhum
Aceitar a entrada de tubagem? |TRUE(ByPropertyName)
Aceitar carateres universais? |FALSO

**&lt;Parâmetrosdocomando&gt;**

Este cmdlet suporta os parâmetros comuns:-depurar, - ErrorAction, - ErrorVariable, - InformationAction, - InformationVariable, - OutVariable,-OutBuffer, - PipelineVariable, - verboso, - WarningAction e - WarningVariable.

### <a name="inputs"></a>Entradas do tipo

O tipo de entrada é o tipo dos objetos que pode encaminhar ao cmdlet.

### <a name="outputs"></a>Resultados de

O tipo de saída é o tipo dos objetos que o cmdlet emite.

## <a name="remove-azurermmediaservice"></a>Remover AzureRmMediaService

Remove um serviço de multimédia.

### <a name="syntax"></a>Sintaxe

    Remove-AzureRmMediaService [-ResourceGroupName] <string> [-AccountName] <string>  [<CommonParameters>]

### <a name="parameters"></a>Parâmetros

**-ResourceGroupName &lt;cadeia&gt;**

Especifica o nome do grupo de recursos ao qual pertence este serviço de multimédia.

Aliases |nenhum
---|---
Obrigatório? |VERDADEIRO
Posição? |0
Valor predefinido |nenhum
Aceitar a entrada de tubagem? |TRUE(ByPropertyName)
Aceitar carateres universais? |FALSO

**-AccountName &lt;cadeia&gt;**

Especifica o nome do serviço de multimédia.

Aliases |nenhum
---|---
Obrigatório? |VERDADEIRO
Posição? |2
Valor predefinido |Nenhum
Aceitar a entrada de tubagem?  |TRUE(ByPropertyName)
Aceitar carateres universais? |FALSO

**&lt;Parâmetrosdocomando&gt;**

Este cmdlet suporta os parâmetros comuns:-depurar, - ErrorAction, - ErrorVariable, - InformationAction, - InformationVariable, - OutVariable,-OutBuffer, - PipelineVariable, - verboso, - WarningAction e - WarningVariable.

### <a name="inputs"></a>Entradas do tipo

O tipo de entrada é o tipo dos objetos que pode encaminhar ao cmdlet.

### <a name="outputs"></a>Resultados de

O tipo de saída é o tipo dos objetos que o cmdlet emite.

## <a name="get-azurermmediaservice"></a>Get-AzureRmMediaService

Obtém todos os serviços de multimédia num grupo de recursos ou num serviço de multimédia com um determinado nome.

### <a name="syntax"></a>Sintaxe

ParameterSet: ResourceGroupParameterSet

    Get-AzureRmMediaService [-ResourceGroupName] <string>  [<CommonParameters>] 

ParameterSet: AccountNameParameterSet

    Get-AzureRmMediaService [-ResourceGroupName] <string> [-AccountName] <string>  [<CommonParameters>]

### <a name="parameters"></a>Parâmetros

**-ResourceGroupName &lt;cadeia&gt;**

Especifica o nome do grupo de recursos ao qual pertence este serviço de multimédia.

Aliases |nenhum
---|---
Obrigatório? |VERDADEIRO
Posição?  |0
Valor predefinido |nenhum
Aceitar a entrada de tubagem? |TRUE(ByPropertyName)
Nome do conjunto de parâmetro |ResourceGroupParameterSet, AccountNameParameterSet
Aceitar carateres universais?   FALSO

**-AccountName &lt;cadeia&gt;**

Especifica o nome do serviço de multimédia.

Aliases |nenhum
---|---
Obrigatório? |VERDADEIRO
Posição?  |1
Valor predefinido |nenhum
Aceitar a entrada de tubagem? |TRUE(ByPropertyName)
Nome do conjunto de parâmetro  |AccountNameParameterSet
Aceitar carateres universais? |FALSO

**&lt;Parâmetrosdocomando&gt;**

Este cmdlet suporta os parâmetros comuns:-depurar, - ErrorAction, - ErrorVariable, - InformationAction, - InformationVariable, - OutVariable,-OutBuffer, - PipelineVariable, - verboso, - WarningAction e - WarningVariable.

### <a name="inputs"></a>Entradas do tipo

O tipo de entrada é o tipo dos objetos que pode encaminhar ao cmdlet.

### <a name="outputs"></a>Resultados de

O tipo de saída é o tipo dos objetos que o cmdlet emite.

## <a name="get-azurermmediaservicekeys"></a>Get-AzureRmMediaServiceKeys

Obtém chaves de um serviço de multimédia.

### <a name="syntax"></a>Sintaxe

    Get-AzureRmMediaServiceKeys [-ResourceGroupName] <string> [-AccountName] <string>  [<CommonParameters>]

### <a name="parameters"></a>Parâmetros

**-ResourceGroupName &lt;cadeia&gt;**

Especifica o nome do grupo de recursos ao qual pertence este serviço de multimédia.

Aliases |nenhum
---|---
Obrigatório? |VERDADEIRO
Posição?  |0
Valor predefinido |nenhum
Aceitar a entrada de tubagem? |TRUE(ByPropertyName)
Aceitar carateres universais? |FALSO

**-AccountName &lt;cadeia&gt;**

Especifica o nome do serviço de multimédia.

Aliases |nenhum
---|---
Obrigatório? |VERDADEIRO
Posição? |1
Valor predefinido |nenhum
Aceitar a entrada de tubagem? |TRUE(ByPropertyName)
Aceitar carateres universais? |FALSO

**&lt;Parâmetrosdocomando&gt;**

Este cmdlet suporta os parâmetros comuns:-depurar, - ErrorAction, - ErrorVariable, - InformationAction, - InformationVariable, - OutVariable,-OutBuffer, - PipelineVariable, - verboso, - WarningAction e - WarningVariable.

### <a name="inputs"></a>Entradas do tipo

O tipo de entrada é o tipo dos objetos que pode encaminhar ao cmdlet.

### <a name="outputs"></a>Resultados de

O tipo de saída é o tipo dos objetos que o cmdlet emite.

## <a name="set-azurermmediaservicekey"></a>Definir AzureRmMediaServiceKey

Gera novamente uma chave primária ou secundária de um serviço de multimédia.

### <a name="syntax"></a>Sintaxe

    Set-AzureRmMediaServiceKey [-ResourceGroupName] <string> [-AccountName] <string> [-KeyType] <KeyType> {Primary | Secondary}  [<CommonParameters>]

### <a name="parameters"></a>Parâmetros

**-ResourceGroupName &lt;cadeia&gt;**

Especifica o nome do grupo de recursos ao qual pertence este serviço de multimédia.

Aliases |nenhum
---|---
Obrigatório?  |VERDADEIRO
Posição?  |0
Valor predefinido |nenhum
Aceitar a entrada de tubagem?  |TRUE(ByPropertyName)
Aceitar carateres universais? |FALSO

**-AccountName &lt;cadeia&gt;**

Especifica o nome do serviço de multimédia.

Aliases |nenhum
---|---
Obrigatório? |VERDADEIRO
Posição?  |1
Valor predefinido |nenhum
Aceitar a entrada de tubagem?   |TRUE(ByPropertyName)
Aceitar carateres universais? |FALSO

**-KeyType &lt;KeyType&gt;**

Especifica o tipo de chave do serviço de multimédia.

- Principal ou secundário

Aliases |nenhum
---|---
Obrigatório?  |VERDADEIRO
Posição?  |2
Valor predefinido |nenhum
Aceitar a entrada de tubagem? |FALSO
Aceitar carateres universais? |FALSO

**&lt;Parâmetrosdocomando&gt;**

Este cmdlet suporta os parâmetros comuns:-depurar, - ErrorAction, - ErrorVariable, - InformationAction, - InformationVariable, - OutVariable,-OutBuffer, - PipelineVariable, - verboso, - WarningAction e - WarningVariable.

### <a name="inputs"></a>Entradas do tipo

O tipo de entrada é o tipo dos objetos que pode encaminhar ao cmdlet.

### <a name="outputs"></a>Resultados de

O tipo de saída é o tipo dos objetos que o cmdlet emite.

## <a name="sync-azurermmediaservicestoragekeys"></a>Sincronização AzureRmMediaServiceStorageKeys

Sincroniza as teclas de conta de armazenamento para uma conta de armazenamento associado ao serviço de multimédia.

### <a name="syntax"></a>Sintaxe

    Sync-AzureRmMediaServiceStorageKeys [-ResourceGroupName] <string> [-MediaServiceAccountName] <string>    [-StorageAccountId] <string>  [<CommonParameters>]

### <a name="parameters"></a>Parâmetros

**-ResourceGroupName &lt;cadeia&gt;**

Especifica o nome do grupo de recursos ao qual pertence este serviço de multimédia.

Aliases |nenhum
---|---
Obrigatório? |VERDADEIRO
Posição? |0
Valor predefinido |nenhum
Aceitar a entrada de tubagem? |TRUE(ByPropertyName)
Aceitar carateres universais? |FALSO

**-AccountName &lt;cadeia&gt;**

Especifica o nome do serviço de multimédia.

Aliases |nenhum
---|---
Obrigatório? |VERDADEIRO
Posição? |1
Valor predefinido |nenhum
Aceitar a entrada de tubagem? |TRUE(ByPropertyName)
Aceitar carateres universais? |FALSO

**-StorageAccountId &lt;cadeia&gt;**

Especifica a conta de armazenamento associada ao serviço de multimédia.

Aliases |ID
---|---
Obrigatório? |VERDADEIRO
Posição?  |2
Valor predefinido |nenhum
Aceitar a entrada de tubagem? |      TRUE(ByPropertyName)
Aceitar carateres universais? |FALSO

**&lt;Parâmetrosdocomando&gt;**

Este cmdlet suporta os parâmetros comuns:-depurar, - ErrorAction, - ErrorVariable, - InformationAction, - InformationVariable, - OutVariable,-OutBuffer, - PipelineVariable, - verboso, - WarningAction e - WarningVariable.

### <a name="inputs"></a>Entradas do tipo

O tipo de entrada é o tipo dos objetos que pode encaminhar ao cmdlet.

### <a name="outputs"></a>Resultados de

O tipo de saída é o tipo dos objetos que o cmdlet emite.

## <a name="next-step"></a>Passo seguinte 

Consulte o artigo dos serviços de multimédia caminhos de aprendizagem.

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

 
