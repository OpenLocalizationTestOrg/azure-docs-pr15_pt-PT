<properties
    pageTitle="Adicionar o Microsoft Translator nas aplicações de lógica | Microsoft Azure"
    description="Descrição geral da conexão Microsoft Translator com parâmetros REST API"
    services=""
    suite=""
    documentationCenter="" 
    authors="MandiOhlinger"
    manager="erikre"
    editor=""
    tags="connectors"/>

<tags
   ms.service="multiple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="08/18/2016"
   ms.author="mandia"/>

# <a name="get-started-with-the-microsoft-translator-connector"></a>Começar a trabalhar com o conector de Microsoft Translator
Ligar ao Microsoft Translator para traduzir texto, detetar um idioma e muito mais. Com o Microsoft Translator, pode: 

- Crie o seu fluxo de negócio com base em dados que receber do Microsoft Translator. 
- Utilize as ações para traduzir texto, detetar um idioma e muito mais. Estas ações obtém uma resposta e, em seguida, disponibilizam o resultado para outras ações. Por exemplo, quando é criado um novo ficheiro no Dropbox, pode traduzir o texto no ficheiro para outro idioma com o Microsoft Translator.

Para adicionar uma operação nas aplicações de lógica, consulte o artigo [criar uma aplicação de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Accionadores e acções
Microsoft Translator inclui as seguintes ações. Não existem sem accionadores.

Accionadores | Ações
--- | ---
Nenhum | <ul><li>Detetar o idioma</li><li>Texto em voz</li><li>Traduzir texto</li><li>Obter idiomas</li><li>Obter idiomas de voz</li></ul>

Todas as conexões suportam dados nos formatos JSON e XML.


## <a name="create-a-connection-to-microsoft-translator"></a>Criar uma ligação ao Microsoft Translator

>[AZURE.INCLUDE [Steps to create a connection to Microsoft Translator](../../includes/connectors-create-api-microsofttranslator.md)]


## <a name="swagger-rest-api-reference"></a>Referência de REST API swagger
Aplica-se para a versão: 1.0.

### <a name="detect-language"></a>Detetar o idioma    
Detetar o idioma de origem do determinado texto.  
```GET: /Detect```

| Nome| Tipo de dados|Obrigatório|Localizado na|Valor predefinido|Descrição|
| ---|---|---|---|---|---|
|consulta|cadeia|Sim|consulta|nenhum |Texto cujo idioma será identificado|

#### <a name="response"></a>Resposta
|Nome|Descrição|
|---|---|
|200|OK|
|predefinido|A operação falhou.|


### <a name="text-to-speech"></a>Texto em voz    
Converte um determinado texto em voz como uma sequência de áudio no formato de vaga.  
```GET: /Speak```

| Nome| Tipo de dados|Obrigatório|Localizado na|Valor predefinido|Descrição|
| ---|---|---|---|---|---|
|consulta|cadeia|Sim|consulta|nenhum |Texto para converter em|
|idioma|cadeia|Sim|consulta|nenhum |Código de idioma para gerar voz (exemplo: ' en-dos us)|

#### <a name="response"></a>Resposta
|Nome|Descrição|
|---|---|
|200|OK|
|predefinido|A operação falhou.|


### <a name="translate-text"></a>Traduzir texto    
Converte o texto para um idioma especificado utilizando o Microsoft Translator.  
```GET: /Translate```

| Nome| Tipo de dados|Obrigatório|Localizado na|Valor predefinido|Descrição|
| ---|---|---|---|---|---|
|consulta|cadeia|Sim|consulta|nenhum |Texto para traduzir|
|languageTo|cadeia|Sim|consulta| nenhum|Código de idioma de destino (exemplo: 'fr')|
|languageFrom|cadeia|nenhum|consulta|nenhum |Origem de idioma; Se não for fornecido, o Microsoft Translator tentará deteção automática. (exemplo: pt-pt)|
|categoria|cadeia|nenhum|consulta|geral |Categoria de tradução (predefinido: 'Geral')|

#### <a name="response"></a>Resposta
|Nome|Descrição|
|---|---|
|200|OK|
|predefinido|A operação falhou.|


### <a name="get-languages"></a>Obter idiomas    
Obtém todos os idiomas que suporte da Microsoft Translator.  
```GET: /TranslatableLanguages```

Não existem parâmetros para esta chamada. 

#### <a name="response"></a>Resposta
|Nome|Descrição|
|---|---|
|200|OK|
|predefinido|A operação falhou.|


### <a name="get-speech-languages"></a>Obter idiomas de voz    
Obtém os idiomas disponíveis para sintetizador de voz.  
```GET: /SpeakLanguages``` 

Não existem parâmetros para esta chamada.

#### <a name="response"></a>Resposta
|Nome|Descrição|
|---|---|
|200|OK|
|predefinido|A operação falhou.|

## <a name="object-definitions"></a>Definições de objecto

#### <a name="language-language-model-for-microsoft-translator-translatable-languages"></a>Idioma: modelo de idioma para idiomas converter Microsoft Translator

|Nome da propriedade | Tipo de dados | Obrigatório|
|---|---|---|
|Código|cadeia|nenhum|
|Nome|cadeia|nenhum|


## <a name="next-steps"></a>Próximos passos

[Criar uma aplicação de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md).

Regresse à [lista APIs](apis-list.md).


<!--References-->
[5]: https://datamarket.azure.com/developer/applications/
[6]: ./media/connectors-create-api-microsofttranslator/register-your-application.png
