<properties
   pageTitle="Guia para a criação de um serviço de dados do Marketplace | Microsoft Azure"
   description="Obter instruções detalhadas sobre como criar, certificar e implementar um serviço de dados para comprar no Azure Marketplace."
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager="hascipio"
   editor=""/>

   <tags
      ms.service="marketplace"
      ms.devlang="na"
      ms.topic="article"
      ms.tgt_pltfrm="na"
      ms.workload="na"
      ms.date="08/26/2016"
      ms.author="hascipio; avikova" />

# <a name="mapping-an-existing-web-service-to-odata-through-csdl"></a>Mapeamento de um serviço web existente para OData através de CSDL

>[AZURE.IMPORTANT] **Neste momento podemos já não são ativação qualquer novos fabricantes de serviço de dados. Novo dataservices não irá obter aprovado para obter uma lista.** Se tiver uma aplicação de empresas SaaS que gostaria de publicar no AppSource pode encontrar mais informações [aqui](https://appsource.microsoft.com/partners). Se tiver um aplicações IaaS ou serviço para programadores que gostaria de publicar no Azure Marketplace pode encontrar mais informações [aqui](https://azure.microsoft.com/marketplace/programs/certified/).

Este artigo fornece uma descrição geral sobre como utilizar uma CSDL para mapear um serviço existente para um serviço compatível de OData. Explica como para criar o documento de mapeamento (CSDL) que transformações de pedido de entrada a partir do cliente através de uma chamada de serviço e o resultado (dados) novamente para o cliente através de um OData compatível feed. Azure Marketplace do Microsoft expõe serviços para os utilizadores finais ao utilizar o protocolo de OData. Serviços que são expostos pelos fornecedores de conteúdos (dados proprietários) são expostos numa variedade de formulários, tal como o resto, SOAP, etc.

## <a name="what-is-a-csdl-and-its-structure"></a>O que é um CSDL e da sua estrutura?
Um CSDL (Conceptual esquema Definition Language) é uma especificação que define como descrever o serviço web ou serviço de base de dados no comuns oferta XML ao Azure Marketplace.

Descrição geral Simple do **Pedir fluxo:**

  `Client -> Azure Marketplace -> Content Provider’s Web Service (Get, Post, Delete, Put)`

O **fluxo de dados** é na direção oposta:

  `Client <- Azure Marketplace <- Content Provider’s WebService`

**Figura 1** diagramas como um cliente obteria dados a partir de um fornecedor de conteúdos (o seu serviço) ao aceder através do Azure Marketplace.  O CSDL é utilizado pelo componente de mapeamento/transformação para processar o pedido e transferem dados entre serviço (s) o fornecedor de conteúdos e o cliente pedido.

*Figura 1: Fluxo detalhado a partir do cliente pedir para o fornecedor de conteúdo através do Azure Marketplace*

  ![desenho](media/marketplace-publishing-data-service-creation-odata-mapping/figure-1.png)

Para informações gerais sobre Atom, Atom Pub e o protocolo de OData no qual as extensões de Azure Marketplace construir, por favor rever: [http://msdn.microsoft.com/library/ff478141.aspx](http://msdn.microsoft.com/library/ff478141.aspx)

Excerto pontos acima de ligação:     *"o objetivo do protocolo de dados abertas (adiante como OData) é fornecem um protocolo com base no resto para operações CRUD estilo (criar, ler, atualizar e eliminar) relativamente a recursos expostas como serviços de dados. Um "serviço de dados" é um ponto final nas quais existem dados expostos a partir de uma ou mais "colecções" cada com zero ou mais "entradas", que são constituídos escreveu pares valor com o nome. OData é publicado pela Microsoft em normas de organização de normalização (organização para as progredir de normas estruturadas de informação) para que qualquer pessoa que pretenda criar servidores, clientes ou ferramentas sem royalties ou restrições."*

### <a name="three-critical-pieces-that-have-to-be-defined-by-the-csdl-are"></a>Três partes crítico que têm de ser definidas pela CSDL são:

- O **ponto final** do serviço de fornecedor a Web endereço URI () do serviço
- Os **parâmetros de dados** a ser transmitido como entrada ao fornecedor de serviços as definições dos parâmetros a ser enviada para o serviço do fornecedor de conteúdos para baixo para o tipo de dados.
- **Esquema** de dados a ser devolvidos para o serviço de pedir o esquema dos dados a ser entregues pelo serviço o fornecedor de conteúdo, incluindo o contentor, coleções/tabelas, variáveis/colunas e tipos de dados.

O diagrama seguinte mostra uma descrição geral do fluxo de onde o cliente introduz a declaração de OData (chamada para o serviço web do fornecedor de conteúdos) para obter os resultados/dados novamente.

  ![desenho](media/marketplace-publishing-data-service-creation-odata-mapping/figure-2.png)

### <a name="steps"></a>Passos:

1. Cliente envia o pedido através de chamada de serviço completa com parâmetros de entrada definidos em XML ao Azure Marketplace
2. CSDL é utilizado para validar a chamada de serviço.
    - Formatado serviço de chamadas é, em seguida, enviada para o serviço de fornecedores de conteúdo ao Azure Marketplace
3. A função Serviçoweb executa e pré a ação do verbo de Http (ou seja, obter) os dados são devolvidos ao Azure Marketplace, onde os dados pedidos (se existir) estão expõe em formato XML para o cliente utilizando o mapeamento definido na CSDL.
4. O cliente é enviado os dados (se existir) em formato XML ou JSON

## <a name="definitions"></a>Definições

### <a name="odata-atom-pub"></a>Pub OData ATOM

Definir uma extensão de pub ATOM em que cada entrada representa uma linha de um resultado. A peça de conteúdo da entrada é melhorada para contêm os valores da linha – como pares valor de chave. Obter mais informações for encontradas aqui: [https://www.odata.org/documentation/odata-version-3-0/atom-format/](https://www.odata.org/documentation/odata-version-3-0/atom-format/)

### <a name="csdl---conceptual-schema-definition-language"></a>CSDL - linguagem de definição de esquema Conceptual

Permite que define funções (SPROCs) e entidades são expostas através de uma base de dados. Obter mais informações encontradas aqui: [http://msdn.microsoft.com/library/bb399292.aspx](http://msdn.microsoft.com/library/bb399292.aspx)  

> [AZURE.TIP] Clique no menu pendente **outras versões** e selecione uma versão se não vir o artigo.

### <a name="edm---entry-data-model"></a>EDM - modelo de dados de entrada
- Descrição geral: [http://msdn.microsoft.com/library/vstudio/ee382825 (v=vs.100).aspx][OverviewLink] [OverviewLink]: http://msdn.microsoft.com/library/vstudio/ee382825 (v=vs.100).aspx
- Pré-visualização: [http://msdn.microsoft.com/library/aa697428 (v=vs.80).aspx][PreviewLink] [PreviewLink]: http://msdn.microsoft.com/library/aa697428 (v=vs.80).aspx
- Tipos de dados: [http://msdn.microsoft.com/library/bb399548 (v=VS.100).aspx][DataTypesLink] [DataTypesLink]: http://msdn.microsoft.com/library/bb399548 (v=VS.100).aspx

A seguinte mostra o fluxo de detalhadas esquerda para a direita de onde o cliente introduz a declaração de OData (chamada para o serviço web do fornecedor de conteúdos) para obter os resultados/dados trás:

  ![desenho](media/marketplace-publishing-data-service-creation-odata-mapping/figure-3.png)


## <a name="csdl-basics"></a>Noções básicas CSDL

Um CSDL (Conceptual esquema Definition Language) é uma especificação que define como descrever o serviço web ou serviço de base de dados no comuns oferta XML ao Azure Marketplace. Descreve o crítico CSDL peças que **permite a passagem de dados da origem de dados ao Azure Marketplace.** As peças principais estão descritas aqui:

- Informações da interface descrever todas as funções publicamente disponíveis (FunctionImport nó)
- Informações do tipo de dados para todas as mensagens requests(input) e responses(outputs) mensagem (EntitySet/EntityContainer/EntityType nós)
- Encadernação informações sobre o protocolo de transporte para ser utilizado (cabeçalho nó)
- Informações de endereço para localizar o serviço especificado (BaseURI atributo)

Em Eis um resumo dos, o CSDL representa um contrato de plataforma e idioma independente entre o autor do pedido serviço e o fornecedor de serviços. Utilizar o CSDL, um cliente, pode localizar um serviço de base de dados/serviços web e invocar qualquer uma das suas funções disponíveis ao público.

### <a name="relating-a-csdl-to-a-database-or-a-collection"></a>Relacionados com um CSDL para uma base de dados ou de uma coleção de
**A especificação de CSDL**

CSDL é Gramática XML para descrever um serviço web. A especificação de propriamente dito estiver dividida em 4 elementos principais: EntitySet, FunctionImport; Espaço de nomes e EntityType.

Para tornar este produção mais fácil de compreender permite-lhe se relacionam com um CSDL a uma tabela.

Lembre-se;

  CSDL representa um contrato de plataforma e idioma independente entre **solicitação de serviço** e o **fornecedor de serviços**. Utilizar CSDL, um **cliente** pode localizar um **serviço de base de dados/serviços web** e invocar qualquer um dos seus publicamente disponível **funções.**

Para um serviço de dados podem ser consideradas as quatro partes de um CSDL em termos de uma base de dados, tabela, coluna e arquivo de termos.

Relacionados com estes da seguinte forma para um serviço de dados:

- EntityContainer ~ = base de dados
- EntitySet ~ = tabela
- EntityType ~ = colunas
- FunctionImport ~ = procedimento armazenado

**Verbos HTTP permitidos**
- OBTER – devolve valores da db (devolve uma coleção de)
- MENSAGEM – utilizada para transmitir dados para e os valores devolvidos opcionais de base de dados (criar uma nova entrada na coleção de retorno id/URI)
- ELIMINAR – elimina dados a partir do DB (elimina uma coleção de)
- COLOCAR – atualizar os dados para um DB (substituir uma coleção de ou criar um)

## <a name="metadatamapping-document"></a>Documento/mapeamento de metadados

O documento de mapeamento de metadados/é utilizado para mapear os serviços web existentes um fornecedor de conteúdos para que podem ser exposto como um serviço web de OData pelo sistema Azure Marketplace. Baseia-se CSDL e implementa extensões de alguns CSDL para acomodar as necessidades dos restantes serviços web expostos através do Azure Marketplace com base. As extensões de encontram-se no espaço de nomes [http://schemas.microsoft.com/dallas/2010/04](http://schemas.microsoft.com/dallas/2010/04) .

Segue-se um exemplo do CSDL: (copiar e colar o exemplo CSDL numa editor de XML e alterar para corresponder ao seu serviço abaixo.  Em seguida, cole CSDL mapeamento em separador de serviço de dados quando criar o seu serviço no [Portal de publicação do Azure Marketplace](https://publish.windowsazure.com)).

**Termos:** Relacionadas com os termos CSDL os termos de IU (PPUI) do [Portal de publicação](https://publish.windowsazure.com) .
- Oferecer "Título" a PPUI se relaciona com MyWebOffer
- Minhaempresa na PPUI se relaciona com **Nome a apresentar o Publisher** no [Centro de programadores do Microsoft](http://dev.windows.com/registration?accountprogram=azure) IU
- API do seu se refere a um Web ou serviço de dados (um plano de PPUI)

**Hierarquia:** 
 Offer(s) que tenham Plan(s) ao proprietário de uma empresa (fornecedor conteúdo), nomeadamente service(s), qual a linha para cima com uma API.

### <a name="webservice-csdl-example"></a>Exemplo de Serviçoweb CSDL

Liga a um serviço que está a expor um ponto final da aplicação web (por exemplo, uma aplicação do c#)

        <?xml version="1.0" encoding="utf-8"?>
        <!-- The namespace attribute below is used by our system to generate C#. You can change “MyCompany.MyOffer” to something that makes sense for you, but change “MyOffer” consistently throughout the document. -->
        <Schema Namespace="MyCompany.MyWebOffer" Alias="MyOffer" xmlns="http://schemas.microsoft.com/ado/2009/08/edm" xmlns:d="http://schemas.microsoft.com/dallas/2010/04" >
        <!-- EntityContainer groups all the web service calls together into a single offering. Every web service call has a FunctionImport definition. -->
          <EntityContainer Name="MyOffer">
        <!-- EntitySet is defined for CSDL compatibility reasons, not required for ReturnType=”Raw”
        @Name is used as reference by FunctionImport @EntitySet. And is used in the customer facing UI as name of the Service.
        @EntityType is used to point at the type definition near the bottom of this file. -->
            <EntitySet Name="MyEntities" EntityType="MyOffer.MyEntityType" />
        <!-- Add a FunctionImport for every service method. Multiple FunctionImports can share a single return type (EntityType). -->
        <!-- ReturnType is either Raw() for a stream or Collection() for an Atom feed. Ex. of Raw: ReturnType=”Raw(text/plain)” -->
        <!—EntitySet is the entityset defined above, and is needed if ReturnType is not Raw -->
        <!-- BaseURI attribute defines the service call, replace & with the encode value (&amp;).
        In the input name value pairs {param} represents passed in value.
        Or the value can be hard coded as with AccountKey. -->
        <!-- AllowedHttpMethods optional (default = “GET”), allows the CSDL to specifically specify the verb of the service, “Get”, “Post”, “Put”, or “Delete”. -->
        <!-- EncodeParameterValues, True encodes the parameter values, false does not. -->
        <!-- BaseURI is translated into an URITemplate which defines how the web service call is exposed to marketplace customers.
        Ex. https://api.datamarket.azure.com/mycompany/MyOfferPlan?name={name}
        BaseURI is XML encoded, the {...} point to the parameters defined below.
        Marketplace will read the parameters from this URITemplate and fill the values into the corresponding parameters of the BaseUri or RequestBody (below) during calls to your service.  
        It is okay for @d:BaseUri to include information only for Marketplace consumption, it will not be exposed to end users. i.e. the hardcoded AccountKey in the above BaseURI does not show up in the client facing URITemplate. -->
            <FunctionImport Name="MyWebServiceMethod"
                            EntitySet="MyEntities"
                            ReturnType="Collection(MyOffer.MyEntityType)"
        d:AllowedHttpMethods="GET"
        d:EncodeParameterValues="true"
        d:BaseUri="http://services.organization.net/MyServicePath?name={name}&amp;AccountKey=22AC643">
        <!-- Definition of the RequestBody is only required for HTTP POST requests and is optional for HTTP GET requests. -->
        <d:RequestBody d:httpMethod="POST">
                <!-- Use {} for placeholders to insert parameters. -->
                <!-- This example uses SOAP formatting, but any POST body can be used. -->
            <!-- This example shows how to pass userid and password via the header -->
                <![CDATA[<soapenv:Envelope xmlns:soapenv="http://schemas.xmlsoap.org/soap/envelope/" xmlns:MyOffer="http://services.organization.net/MyServicePath">
                  <soapenv:Header/>
                  <soapenv:Body>
                    <MyOffer:ws_MyWebServiceMethod>
                      <myWebServiceMethodRequest>
                        <!--This information is not exposed to end users. -->
                        <UserId>userid</UserId>
                        <Password>password</Password>
                        <!-- {name} is replaced with the value read from @d:UriTemplate above -->
                        <Name>{name}</Name>
                        <!-- Parameters can be used more than once and are not limited to appearing as the value of an element -->
                        <CustomField Name="{name}" />
                        <MyField>Static content</MyField>
                      </myWebServiceMethodRequest>
                    </MyOffer:ws_MyWebServiceMethod>
                  </soapenv:Body>
                </soapenv:Envelope>      
              ]]>
        </d:RequestBody>
        <!-- Title, Rights and Description are optional and used to specify values to insert into the ATOM feed returned to the end user.  You can specify the element to contain a fixed message by providing a value for the element (this is the default value).  @d:Map is an XPath expression that points into the response returned by your service and is optional.  -->
        <d:Title d:Map="/MyResponse/Title">Default title.</d:Title>
        <d:Rights>© My copyright. This is a fixed response. It is okay to also add a d:Map attribute to override this text.</d:Rights>
        <d:Description d:Map="/MyResponse/Description"></d:Description>
        <d:Namespaces>
        <d:Namespace d:Prefix="p"  d:Uri="http://schemas.organization.net/2010/04/myNamespace" />
        <d:Namespace d:Prefix="p2" d:Uri="http://schemas.organization.net/2010/04/MyNamespace2" />
        </d:Namespaces>
        <!-- Parameters of the web service call:
        @Name should match exactly (case sensitive) the {…} placeholders in the @d:BaseUri, @d:UriTemplate, and d:RequestBody, i.e. “name” parameter in above BaseURI.
        @Mode is always "In", compatibility with CSDL
        @Type is the EDM.SimpleType of the parameter, see http://msdn.microsoft.com/library/bb399548(v=VS.100).aspx
        @d:Nullable indicates whether the parameter is required.
        @d:Regex - optional, attribute to describe the string, limiting unwanted input at the entry of the system
        @d:Description - optional, is used by Service Explorer as help information
        @d:SampleValues - optional, is used by Service Explorer as help information. Multiple Sample values are separated by '|', e.g. "804735132|234534224|23409823234"
        @d:Enum - optional for string type. Contains an enumeration of possible values separated by a '|', e.g. d:enum="english|metric|raw". Will be converted in a dropdown list in the Service Explorer.
        -->
        <Parameter name="name" Mode="In" Type="String" d:Nullable="false" d:Regex="^[a-zA-Z]*$" d:Description="A name that cannot contain any spaces or non-alpha non-English characters"
        d:Enum="George|John|Thomas|James"
        d:SampleValues="George"/>
        <Parameter Name=" AccountKey" Mode="In" Type="String" d:Nullable="false" />

        <!-- d:ErrorHandling is an optional element. Use it define standardized errors by evaluating the service response. -->
        <d:ErrorHandling>
        <!-- Any number of d:Condition elements are allowed, they are evaluated in the order listed.
        @d:Match is an Xpath query on the service response, it should return true or false where true indicates an error.
        @d:httpStatusCode is the error code to return if an response matches the error.
        @d:errorMessage is the user friendly message to return when an error occurs.
        -->
        <d:Condition d:Match="/Result/ErrorMessage[text()='Invalid token']" d:HttpStatusCode="403" d:ErrorMessage="User cannot connect to the service." />
        </d:ErrorHandling>
           </FunctionImport>

            <!-- The EntityContainer defines the output data schema -->
        </EntityContainer>
        <!-- The EntityType @d:Map defines the repeating node (an XPath query) in the response (output data schema). -->
        <!-- If these nodes are outside a namespace, add the prefix in the xpath. -->
        <!--
        @Name - define your user readable name, will become an XML element in the ATOM feed, so comply with the XML element naming restrictions (no spaces or other illegal characters).
        @Type is the EDM.SimpleType of the parameter, see http://msdn.microsoft.com/library/bb399548(v=VS.100).aspx.
        @d:Map uses an Xpath query to point at the location to extract the content from your services response.
        The "." is relative to the repeating node in the EntityType @d:Map Xpath expression.
        -->
            <EntityType Name="MyEntityType" d:Map="/MyResponse/MyEntities">
        <Property Name="ID" d:IsPrimaryKey="True" Type="Int32"  Nullable="false" d:Map="./Remaining[@Amount]"/>
        <Property Name="Amount" Type="Double"   Nullable="false" d:Map="./Remaining[@Amount]"/>
        <Property Name="City"   Type="String"   Nullable="false" d:Map="./City"/>
        <Property Name="State"  Type="String"   Nullable="false" d:Map="./State"/>
        <Property Name="Zip"    Type="Int32"    Nullable="false" d:Map="./Zip"/>
        <Property Name="Updated"    Type="DateTime" Nullable="false" d:Map="./Updated"/>
        <Property Name="AdditionalInfo" Type="String" Nullable="true"
        d:Map="./Info/More[1]"/>
            </EntityType>
        </Schema>

> [AZURE.TIP] Ver mais exemplos de serviço Web de CSDL no artigo [exemplos de mapeamento de um serviço web existente a OData através de CSDLs](marketplace-publishing-data-service-creation-odata-mapping-examples.md)

###<a name="dataservice-csdl-example"></a>Exemplo de CSDL de serviço de dados

Liga a um serviço que está a expor numa tabela de base de dados ou vista como um ponto final exemplo abaixo mostra que duas APIs para base de dados com base CSDL API (pode utilizar vistas em vez de tabelas).

        <?xml version="1.0"?>
        <!-- The namespace attribute below is used by our system to generate C#. You can change “MyCompany.MyOffer” to something that makes sense for you, but change “MyOffer” consistently throughout the document. -->
        <Schema Namespace="MyCompany.MyDataOffer" Alias="MyOffer" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/ado/2009/08/edm">
        <!-- EntityContainer groups all the data service calls together into a single offering. Every web service call has a FunctionImport definition. -->
        <EntityContainer Name="MyOfferContainer">
        <!-- EntitySet is defined for CSDL compatibility reasons, not required for ReturnType=”Raw”
            Think of the EntitySet as a Service
        @Name is used in the customer facing UI as name of the Service.
        @EntityType is used to point at the type definition (returned set of table columns). -->
        <EntitySet Name="CompanyInfoEntitySet" EntityType="MyOffer.CompanyInfo" />
        <EntitySet Name="ProductInfoEntitySet" EntityType="MyOffer.ProductInfo" />
        </EntityContainer>
        <!-- EntityType defines result (output); the table (or view) and columns to be returned by the data service.)
            Map is the schema.tabel or schema.view
            dals.TableName is the table Name
            Name is the name identifier for the EntityType and the Name of the service exposed to the client via the UI.
            dals:IsExposed determines if the table schema is exposed (generally true).
            dals:IsView (optional) true if this is based on a view rather than a table
            dals:TableSchema is the schema name of the table/view
        -->
        <EntityType
        Map="[dbo].[CompanyInfo]"
        dals:TableName="CompanyInfo"
        Name="CompanyInfo"
        dals:IsExposed="true"
        dals:IsView="false"
        dals:TableSchema="dbo"
        xmlns:dals="http://schemas.microsoft.com/dallas/2010/04">
        <!-- Property defines the column properties and the output of the service.
            dals:ColumnName is the name of the column in the table /view.
            Type is the emd.SimpleType
            Nullable determines if NULL is a valid output value
            dals.CharMaxLenght is the maximum length of the output value
            Name is the name of the Property and is exposed to the client facing UI
            dals:IsReturned is the Boolean that determines if the Service exposes this value to the client.
            IsQueryable is the Boolean that determines if the column can be used in a database query
            (For data Services: To improve Performance make sure that columns marked ISQueryable=”true” are in an index.)
            dals:OrdinalPosition is the numerical position x in the table or the View, where x is from 1 to the number of columns in the table.
            dals:DatabaseDataType is the data type of the column in the database, i.e. SQL data type dals:IsPrimaryKey indicates if the column is the Primary key in the table/view.  (The columns marked ISPrimaryKey are used in the Order by clause when returning data.)
        -->
        <Property dals:ColumnName="data" Type="String" Nullable="true" dals:CharMaxLength="-1" Name="data" dals:IsReturned="true" dals:IsQueryable="false" dals:IsPrimaryKey="false" dals:OrdinalPosition="3" dals:DatabaseDataType="nvarchar" />
        <Property dals:ColumnName="id" Type="Int32" Nullable="false" Name="id" dals:IsReturned="true" dals:IsQueryable="true" dals:IsPrimaryKey="true" dals:OrdinalPosition="1" dals:NumericPrecision="10" dals:DatabaseDataType="int" />
        <Property dals:ColumnName="ticker" Type="String" Nullable="true" dals:CharMaxLength="10" Name="ticker" dals:IsReturned="true" dals:IsQueryable="true" dals:IsPrimaryKey="false" dals:OrdinalPosition="2" dals:DatabaseDataType="nvarchar" />
        </EntityType>
        <EntityType Map="[dbo].[ProductInfo]" dals:TableName="ProductInfo" Name="ProductInfo" dals:IsExposed="true" dals:IsView="false" dals:TableSchema="dbo" xmlns:dals="http://schemas.microsoft.com/dallas/2010/04">
        <Property dals:ColumnName="companyid" Type="Int32" Nullable="true" Name="companyid" dals:IsReturned="true" dals:IsQueryable="true" dals:IsPrimaryKey="false" dals:OrdinalPosition="2" dals:NumericPrecision="10" dals:DatabaseDataType="int" />
        <Property dals:ColumnName="id" Type="Int32" Nullable="false" Name="id" dals:IsReturned="true" dals:IsQueryable="true" dals:IsPrimaryKey="true" dals:OrdinalPosition="1" dals:NumericPrecision="10" dals:DatabaseDataType="int" />
        <Property dals:ColumnName="product" Type="String" Nullable="true" dals:CharMaxLength="50" Name="product" dals:IsReturned="true" dals:IsQueryable="true" dals:IsPrimaryKey="false" dals:OrdinalPosition="3" dals:DatabaseDataType="nvarchar" />
        </EntityType>
        </Schema>

## <a name="see-also"></a>Consulte também
- Se estiver interessado em aprendizagem e compreender os nós específicos e respectivos parâmetros, leia este artigo [OData mapeamento nós do serviço de dados](marketplace-publishing-data-service-creation-odata-mapping-nodes.md) para definições e explicações, exemplos e utilizar contexto de maiúsculas/minúsculas.
- Se estiver interessado em exemplos de revisão, leia este artigo [OData mapeamento de exemplos de serviço de dados](marketplace-publishing-data-service-creation-odata-mapping-examples.md) para ver o código de exemplo e compreender sintaxe do código e contexto.
- Para voltar para o caminho prescrito publicação de um serviço de dados ao Azure Marketplace, leia este artigo [Guia de publicação de serviço de dados](marketplace-publishing-data-service-creation.md).
