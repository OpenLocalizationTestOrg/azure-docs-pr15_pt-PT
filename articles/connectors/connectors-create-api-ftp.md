<properties
pageTitle="Saiba como utilizar o conector FTP nas aplicações de lógica | Microsoft Azure"
description="Crie aplicações de lógica com Azure aplicação de serviço. Ligar ao servidor de FTP para gerir os seus ficheiros. Pode executar várias ações, como carregar, atualizar, obtenha e elimine ficheiros no servidor de FTP."
services="logic-apps"   
documentationCenter=".net,nodejs,java"  
authors="msftman"   
manager="erikre"    
editor=""
tags="connectors" />

<tags
ms.service="logic-apps"
ms.devlang="multiple"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="integration"
ms.date="07/22/2016"
ms.author="deonhe"/>

# <a name="get-started-with-the-ftp-connector"></a>Começar a trabalhar com o conector FTP

Utilize o conector FTP para monitorizar, gerir e criar ficheiros num servidor FTP. 

Para utilizar [qualquer conexão](./apis-list.md), tem primeiro criar uma aplicação de lógica. Pode começar por [criar uma aplicação de lógica agora](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="connect-to-ftp"></a>Ligar a FTP

Antes de pode aceder a sua aplicação de lógica qualquer serviço, primeiro tem de criar uma *ligação* ao serviço. Uma [ligação](./connectors-overview.md) fornece conectividade entre uma aplicação de lógica e outro serviço.  

### <a name="create-a-connection-to-ftp"></a>Criar uma ligação a FTP

>[AZURE.INCLUDE [Steps to create a connection to FTP](../../includes/connectors-create-api-ftp.md)]

## <a name="use-a-ftp-trigger"></a>Utilize um acionador de FTP

Um accionador é um evento que pode ser utilizado para iniciar o fluxo de trabalho definido numa aplicação de lógica. [Saiba mais sobre os accionadores](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts).  

>[AZURE.IMPORTANT]O conector FTP requer um servidor FTP que está acessível a partir da Internet e estiver configurado para funcionar com o modo de passivo. Além disso, a ligação FTP é **não é compatível com FTPS implícitos (FTP através de SSL)**. O conector FTP suporta apenas FTPS explícita (FTP através de SSL).  

Neste exemplo, posso irá mostrar-lhe como utilizar o accionador **FTP - quando um ficheiro é adicionado ou modificado** para iniciar um fluxo de trabalho de aplicação de lógica quando um ficheiro é adicionado ao ou modificado num servidor FTP. Um exemplo de empresa, pode utilizar este accionador para monitorizar a uma pasta de FTP para novos ficheiros que representam encomendas de clientes.  Em seguida, pode utilizar uma ação de conexão FTP como **obter o conteúdo do ficheiro** para obter os conteúdos da ordem de processamento suplementar e de armazenamento na sua base de dados de encomendas.

1. Introduza *ftp* na caixa de pesquisa no estruturador de aplicações de lógica, em seguida, selecione o accionador **FTP - quando um ficheiro é adicionado ou modificado**   
![Imagem de accionador FTP 1](./media/connectors-create-api-ftp/ftp-trigger-1.png)  
O controlo **Quando um ficheiro é adicionado ou modificado** abre-se para cima  
![Imagem de accionador FTP 2](./media/connectors-create-api-ftp/ftp-trigger-2.png)  
- Selecione **…** localizada no lado direito do controlo. Esta ação abre o controlo de selecionador de pasta  
![Imagem de accionador FTP 3](./media/connectors-create-api-ftp/ftp-trigger-3.png)  
- Selecione o **>** (seta para a direita) e procurar para localizar a pasta que pretende para monitorizar para ficheiros novos ou modificados. Selecione a pasta e repare que a pasta agora é apresentada no controlo de **pasta** .  
![Imagem de accionador FTP 4](./media/connectors-create-api-ftp/ftp-trigger-4.png)   


Neste momento, a sua aplicação de lógica ter sido configurada com um accionador que vai começar uma execução dos outros accionadores e ações no fluxo de trabalho quando um ficheiro é modificado ou criado na pasta FTP específica. 

>[AZURE.NOTE]Para uma aplicação de lógica para serão funcionais, tem de conter pelo menos um accionador e uma ação. Siga os passos na secção seguinte para adicionar uma ação.  



## <a name="use-a-ftp-action"></a>Utilizar uma ação de FTP

Uma ação é uma operação realizada pelo fluxo de trabalho definido numa aplicação de lógica. [Saber mais sobre ações](../app-service-logic/app-service-logic-what-are-logic-apps.md#logic-app-concepts).  

Agora que adicionou um accionador, siga estes passos para adicionar uma ação que irá obter o conteúdo do ficheiro novo ou modificado encontrados pelo accionador.    

1. Selecione **+ novo passo** para adicionar a ação para obter o conteúdo do ficheiro no servidor de FTP  
- Selecione a ligação **Adicionar uma ação** .  
![Imagem de acção FTP 1](./media/connectors-create-api-ftp/ftp-action-1.png)  
- Introduza *FTP* para procurar todas as ações relacionados com FTP.
- Selecione **FTP - obter o conteúdo do ficheiro** como a ação a tomar quando um ficheiro novo ou modificado está localizado na pasta FTP.      
![Imagem de acção FTP 2](./media/connectors-create-api-ftp/ftp-action-2.png)  
O controlo de **obter o conteúdo do ficheiro** é aberto. **Nota**: serão ser-lhe autorizar a sua aplicação de lógica para aceder à sua conta de servidor FTP se ainda não feito anteriormente.  
![Imagem de acção FTP 3](./media/connectors-create-api-ftp/ftp-action-3.png)   
- Selecione o controlo de **ficheiro** (o espaço em branco localizada abaixo **ficheiro***). Aqui, pode utilizar qualquer uma das várias propriedades do ficheiro novo ou modificado no servidor de FTP.  
- Selecione a opção de **conteúdo do ficheiro** .  
![Imagem de acção FTP 4](./media/connectors-create-api-ftp/ftp-action-4.png)   
-  O controlo é atualizado, o que indica que a ação de **FTP - obter o conteúdo do ficheiro** irá obter o *conteúdo de ficheiro* do ficheiro novo ou modificado no servidor de FTP.      
![Imagem de acção FTP 5](./media/connectors-create-api-ftp/ftp-action-5.png)     
- Guardar o seu trabalho, em seguida, adicionar um ficheiro para a pasta FTP para testar o seu fluxo de trabalho.    

Neste momento, a aplicação de lógica ter sido configurada com um accionador para monitorizar a uma pasta num servidor FTP e iniciar o fluxo de trabalho quando encontrar um novo ficheiro ou um ficheiro modificado no servidor de FTP. 

A aplicação de lógica também ter sido configurada com uma ação para obter o conteúdo do ficheiro novo ou modificado.

Agora pode adicionar outra ação tal como a ação de [SQL Server - Inserir linha](./connectors-create-api-sqlazure.md#insert-row) para inserir o conteúdo do ficheiro novo ou modificado para uma tabela de base de dados SQL.  

## <a name="technical-details"></a>Detalhes técnicos

Aqui estão os detalhes sobre accionadores, ações e as respostas que suporta esta ligação:

## <a name="ftp-triggers"></a>FTP accionadores

FTP tem os seguintes accionadores:  

|Accionador | Descrição|
|--- | ---|
|[Quando um ficheiro é adicionado ou modificado](connectors-create-api-ftp.md#when-a-file-is-added-or-modified)|Esta operação de um fluxo de accionadores quando um ficheiro é adicionado ou modificado numa pasta.|


## <a name="ftp-actions"></a>Ações de FTP

FTP tem as seguintes ações:


|Ação|Descrição|
|--- | ---|
|[Obter metadados de ficheiro](connectors-create-api-ftp.md#get-file-metadata)|Esta operação obtém os metadados para um ficheiro.|
|[Ficheiro de actualização](connectors-create-api-ftp.md#update-file)|Esta operação atualiza um ficheiro.|
|[Eliminar o ficheiro](connectors-create-api-ftp.md#delete-file)|Esta operação elimina um ficheiro.|
|[Obter metadados do ficheiro utilizando caminho](connectors-create-api-ftp.md#get-file-metadata-using-path)|Esta operação obtém os metadados de um ficheiro com o caminho.|
|[Aceder a conteúdo através de caminho do ficheiro](connectors-create-api-ftp.md#get-file-content-using-path)|Esta operação obtém o conteúdo de um ficheiro com o caminho.|
|[Obter o conteúdo do ficheiro](connectors-create-api-ftp.md#get-file-content)|Esta operação obtém o conteúdo de um ficheiro.|
|[Criar o ficheiro](connectors-create-api-ftp.md#create-file)|Esta operação cria um ficheiro.|
|[Ficheiro de cópia](connectors-create-api-ftp.md#copy-file)|Esta operação copia um ficheiro para um servidor de FTP.|
|[Lista de ficheiros na pasta](connectors-create-api-ftp.md#list-files-in-folder)|Esta operação obtém a lista de ficheiros e subpastas numa pasta.|
|[Lista de ficheiros na pasta raiz](connectors-create-api-ftp.md#list-files-in-root-folder)|Esta operação obtém a lista de ficheiros e subpastas na pasta raiz.|
|[Extrair pasta](connectors-create-api-ftp.md#extract-folder)|Esta operação extrai um ficheiro de arquivo para uma pasta (exemplo:. zip).|
### <a name="action-details"></a>Detalhes de ação

Aqui estão os detalhes das ações e accionadores para este conexão, juntamente com as respectivas respostas:



### <a name="get-file-metadata"></a>Obter metadados de ficheiro
Esta operação obtém os metadados para um ficheiro. 


|Nome da propriedade| Nome a apresentar|Descrição|
| ---|---|---|
|ID de *|Ficheiro|Selecionar um ficheiro|

Um * indica que não é necessária uma propriedade

#### <a name="output-details"></a>Detalhes de saída

BlobMetadata


| Nome da propriedade | Tipo de dados |
|---|---|---|
|ID|cadeia|
|Nome|cadeia|
|DisplayName em grupos|cadeia|
|Caminho|cadeia|
|Última modificação|cadeia|
|Tamanho|número inteiro|
|MediaType|cadeia|
|IsFolder|Booleano|
|ETag|cadeia|
|FileLocator|cadeia|




### <a name="update-file"></a>Ficheiro de actualização
Esta operação atualiza um ficheiro. 


|Nome da propriedade| Nome a apresentar|Descrição|
| ---|---|---|
|ID de *|Ficheiro|Selecionar um ficheiro|
|corpo *|Conteúdo do ficheiro|Conteúdo do ficheiro|

Um * indica que não é necessária uma propriedade

#### <a name="output-details"></a>Detalhes de saída

BlobMetadata


| Nome da propriedade | Tipo de dados |
|---|---|---|
|ID|cadeia|
|Nome|cadeia|
|DisplayName em grupos|cadeia|
|Caminho|cadeia|
|Última modificação|cadeia|
|Tamanho|número inteiro|
|MediaType|cadeia|
|IsFolder|Booleano|
|ETag|cadeia|
|FileLocator|cadeia|




### <a name="delete-file"></a>Eliminar o ficheiro
Esta operação elimina um ficheiro. 


|Nome da propriedade| Nome a apresentar|Descrição|
| ---|---|---|
|ID de *|Ficheiro|Selecionar um ficheiro|

Um * indica que não é necessária uma propriedade




### <a name="get-file-metadata-using-path"></a>Obter metadados do ficheiro utilizando caminho
Esta operação obtém os metadados de um ficheiro com o caminho. 


|Nome da propriedade| Nome a apresentar|Descrição|
| ---|---|---|
|caminho *|Caminho do ficheiro|Selecionar um ficheiro|

Um * indica que não é necessária uma propriedade

#### <a name="output-details"></a>Detalhes de saída

BlobMetadata


| Nome da propriedade | Tipo de dados |
|---|---|---|
|ID|cadeia|
|Nome|cadeia|
|DisplayName em grupos|cadeia|
|Caminho|cadeia|
|Última modificação|cadeia|
|Tamanho|número inteiro|
|MediaType|cadeia|
|IsFolder|Booleano|
|ETag|cadeia|
|FileLocator|cadeia|




### <a name="get-file-content-using-path"></a>Aceder a conteúdo através de caminho do ficheiro
Esta operação obtém o conteúdo de um ficheiro com o caminho. 


|Nome da propriedade| Nome a apresentar|Descrição|
| ---|---|---|
|caminho *|Caminho do ficheiro|Selecionar um ficheiro|

Um * indica que não é necessária uma propriedade




### <a name="get-file-content"></a>Obter o conteúdo do ficheiro
Esta operação obtém o conteúdo de um ficheiro. 


|Nome da propriedade| Nome a apresentar|Descrição|
| ---|---|---|
|ID de *|Ficheiro|Selecionar um ficheiro|

Um * indica que não é necessária uma propriedade




### <a name="create-file"></a>Criar o ficheiro
Esta operação cria um ficheiro. 


|Nome da propriedade| Nome a apresentar|Descrição|
| ---|---|---|
|caminhopasta *|Caminho da pasta|Selecione uma pasta|
|nome *|Nome do ficheiro|Nome do ficheiro|
|corpo *|Conteúdo do ficheiro|Conteúdo do ficheiro|

Um * indica que não é necessária uma propriedade

#### <a name="output-details"></a>Detalhes de saída

BlobMetadata


| Nome da propriedade | Tipo de dados |
|---|---|---|
|ID|cadeia|
|Nome|cadeia|
|DisplayName em grupos|cadeia|
|Caminho|cadeia|
|Última modificação|cadeia|
|Tamanho|número inteiro|
|MediaType|cadeia|
|IsFolder|Booleano|
|ETag|cadeia|
|FileLocator|cadeia|




### <a name="copy-file"></a>Ficheiro de cópia
Esta operação copia um ficheiro para um servidor de FTP. 


|Nome da propriedade| Nome a apresentar|Descrição|
| ---|---|---|
|origem *|Url de origem|URL para o ficheiro de origem|
|destino *|Caminho do ficheiro de destino|Caminho do ficheiro de destino, incluindo o nome de ficheiro de destino|
|substituir|Substituir?|Substitui o ficheiro de destino, se definida como 'true'|

Um * indica que não é necessária uma propriedade

#### <a name="output-details"></a>Detalhes de saída

BlobMetadata


| Nome da propriedade | Tipo de dados |
|---|---|---|
|ID|cadeia|
|Nome|cadeia|
|DisplayName em grupos|cadeia|
|Caminho|cadeia|
|Última modificação|cadeia|
|Tamanho|número inteiro|
|MediaType|cadeia|
|IsFolder|Booleano|
|ETag|cadeia|
|FileLocator|cadeia|




### <a name="when-a-file-is-added-or-modified"></a>Quando um ficheiro é adicionado ou modificado
Esta operação de um fluxo de accionadores quando um ficheiro é adicionado ou modificado numa pasta. 


|Nome da propriedade| Nome a apresentar|Descrição|
| ---|---|---|
|ID pasta *|Pasta|Selecione uma pasta|

Um * indica que não é necessária uma propriedade




### <a name="list-files-in-folder"></a>Lista de ficheiros na pasta
Esta operação obtém a lista de ficheiros e subpastas numa pasta. 


|Nome da propriedade| Nome a apresentar|Descrição|
| ---|---|---|
|ID de *|Pasta|Selecione uma pasta|

Um * indica que não é necessária uma propriedade



#### <a name="output-details"></a>Detalhes de saída

BlobMetadata


| Nome da propriedade | Tipo de dados |
|---|---|---|
|ID|cadeia|
|Nome|cadeia|
|DisplayName em grupos|cadeia|
|Caminho|cadeia|
|Última modificação|cadeia|
|Tamanho|número inteiro|
|MediaType|cadeia|
|IsFolder|Booleano|
|ETag|cadeia|
|FileLocator|cadeia|




### <a name="list-files-in-root-folder"></a>Lista de ficheiros na pasta raiz
Esta operação obtém a lista de ficheiros e subpastas na pasta raiz. 


Não existem parâmetros para esta chamada

#### <a name="output-details"></a>Detalhes de saída

BlobMetadata


| Nome da propriedade | Tipo de dados |
|---|---|---|
|ID|cadeia|
|Nome|cadeia|
|DisplayName em grupos|cadeia|
|Caminho|cadeia|
|Última modificação|cadeia|
|Tamanho|número inteiro|
|MediaType|cadeia|
|IsFolder|Booleano|
|ETag|cadeia|
|FileLocator|cadeia|




### <a name="extract-folder"></a>Extrair pasta
Esta operação extrai um ficheiro de arquivo para uma pasta (exemplo:. zip). 


|Nome da propriedade| Nome a apresentar|Descrição|
| ---|---|---|
|origem *|Caminho do ficheiro de arquivo de origem|Caminho para o ficheiro de arquivo|
|destino *|Caminho da pasta de destino|Caminho para a pasta de destino|
|substituir|Substituir?|Substitui os ficheiros de destino, se definida como 'true'|

Um * indica que não é necessária uma propriedade



#### <a name="output-details"></a>Detalhes de saída

BlobMetadata


| Nome da propriedade | Tipo de dados |
|---|---|---|
|ID|cadeia|
|Nome|cadeia|
|DisplayName em grupos|cadeia|
|Caminho|cadeia|
|Última modificação|cadeia|
|Tamanho|número inteiro|
|MediaType|cadeia|
|IsFolder|Booleano|
|ETag|cadeia|
|FileLocator|cadeia|



## <a name="http-responses"></a>Respostas HTTP

Ações e accionadores acima, poderá regressar um ou mais dos seguintes códigos de estado HTTP: 

|Nome|Descrição|
|---|---|
|200|OK|
|202|Aceites|
|400|Pedido incorrecto|
|401|Não autorizado|
|403|Proibido|
|404|Não foi encontrado|
|500|Erro de servidor interno. Ocorreu um erro desconhecido.|
|predefinido|A operação falhou.|







## <a name="next-steps"></a>Próximos passos
[Criar uma aplicação de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md)