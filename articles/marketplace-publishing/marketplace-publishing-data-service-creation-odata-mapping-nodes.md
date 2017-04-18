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

# <a name="understanding-the-nodes-schema-for-mapping-an-existing-web-service-to-odata-through-csdl"></a>Noções sobre o esquema de nós de mapeamento de um serviço web existente para OData através de CSDL

>[AZURE.IMPORTANT] **Neste momento podemos já não são ativação qualquer novos fabricantes de serviço de dados. Novo dataservices não irá obter aprovado para obter uma lista.** Se tiver uma aplicação de empresas SaaS que gostaria de publicar no AppSource pode encontrar mais informações [aqui](https://appsource.microsoft.com/partners). Se tiver um aplicações IaaS ou serviço para programadores que gostaria de publicar no Azure Marketplace pode encontrar mais informações [aqui](https://azure.microsoft.com/marketplace/programs/certified/).

Este documento irá ajudar a clarificar a estrutura de nó para mapear um protocolo de OData para CSDL. É importante ter em atenção que a estrutura de nó está bem formatado XML. Por isso, esquema de raiz, principal e subordinado é aplicável ao estruturar o seu mapeamento de OData.

## <a name="ignored-elements"></a>Elementos ignorados
Seguem-se os elementos CSDL alto níveis (nós XML) não estão a passar para ser utilizado por back-end Azure Marketplace durante a importação de metadados o serviço web. Podem estar presentes, mas serão ignoradas.

| Elemento | Âmbito |
|----|----|
| Utilizar o elemento | O nó, sub nós e todos os atributos |
| Elemento de documentação | O nó, sub nós e todos os atributos |
| ComplexType | O nó, sub nós e todos os atributos |
| Elemento de associação | O nó, sub nós e todos os atributos |
| Propriedade expandida | O nó, sub nós e todos os atributos |
| EntityContainer | Os seguintes atributos são ignorados: *expande* e *AssociationSet* |
| Esquema | Os seguintes atributos são ignorados: *espaço de nomes* |
| FunctionImport | Os seguintes atributos são ignorados: *modo* (valor predefinido de ln é considerada) |
| EntityType | Os seguintes nós sub são ignorados: *chave* e *PropertyRef* |

A seguinte tabela descreve as alterações (adicionada e ignorados elementos) para os vários nós de CSDL XML detalhadamente.

## <a name="functionimport-node"></a>Nó FunctionImport
Um nó FunctionImport representa um URL (ponto de entrada) que expõe um serviço para o utilizador final. O nó permite que descreve como o URL é dirigido, estão disponíveis para o utilizador final que parâmetros e como estes parâmetros são fornecidos.

Obter mais detalhes sobre este nó encontram-se em [aqui] [MSDNFunctionImportLink]

[MSDNFunctionImportLink]: (https://msdn.microsoft.com/library/cc716710 (v=vs.100).aspx)

Seguem-se os atributos adicionais (ou adições à atributos) que são expostos pelo nó FunctionImport:

**d:BaseUri** -
modelo URI de para o recurso de resto que é apresentado quando se clica Marketplace. Marketplace utiliza o modelo para construir consultas contra o serviço web REST. O modelo de URI contém marcadores de posição de parâmetros sob a forma de {parameterName}, onde parameterName é o nome do parâmetro. Ex. apiVersion = {apiVersion}.
Parâmetros são permitidos seja apresentada como parâmetros URI ou como parte do caminho URI. No caso do aspeto no caminho fiquem sempre obrigatórios (não podem ser marcadas como anuláveis). *Exemplo:*`d:BaseUri="http://api.MyWeb.com/Site/{url}/v1/visits?start={start}&amp;end={end}&amp;ApiKey=3fadcaa&amp;Format=XML"`

**Nome** - o nome da função importado.  Não podem ser iguais a outros nomes definidos na CSDL.  Ex. Nome = "GetModelUsageFile"

**EntitySet** *(opcional)* - se a função devolve um conjunto de tipos de entidade, o valor da **EntitySet** tem de ser a entidade definida ao qual pertence a coleção. Caso contrário, o atributo **EntitySet** não deve ser utilizado. *Exemplo:*`EntitySet="GetUsageStatisticsEntitySet"`

**Tiporetorno** *(Opcional)* - Especifica o tipo de elementos devolvido pela URI.  Não utilize este atributo se a função não devolve um valor. Seguem-se os tipos suportados:

 - **Coleções de sites (<Entity type name>)**: Especifica um conjunto de tipos de entidade definido. O nome é presente no atributo nome do nó EntityType. Um exemplo é coleção (WXC. HourlyResult).
 - **Matéria-prima (<mime type>)**: Especifica um documento/blob bruto que é devolvido ao utilizador. Um exemplo é Raw(image/jpeg) outros exemplos:

  - ReturnType="Raw(text/plain)"
  - Tiporetorno = "coleção de (o sage. DeleteAllUsageFilesEntity) "*

**d:Paging** - Especifica processada paginação pelo recurso restantes. Os valores de parâmetros são utilizados dentro de aspas curvos ramos, por exemplo, página = {$page} & itemsperpage = {$size} as opções disponíveis são:

- **Nenhum:** sem paginação está disponível
- **Ignorar:** paginação é expressa através de um lógico "Ignorar" e "assumir" (parte superior). Ignorar xyz áàãâ ç elementos de M e tomar, em seguida, devolve os elementos de N seguintes. Valor do parâmetro: $skip
- **Tomar:** Tomar devolve os elementos de N seguintes. Valor do parâmetro: $take
- **PageSize:** paginação é expressa através de um página lógica e tamanho (itens por página). Página representa a página atual, que é devolvida. Valor do parâmetro: $page
- **Tamanho:** tamanho representa o número de itens devolvidos para cada página. Valor do parâmetro: $size

**d:AllowedHttpMethods** *(Opcional)* - Especifica que verbais são processada por recurso restantes. Além disso, restringe verbais aceite para o valor especificado.  Predefinição = mensagem.  *Exemplo:* `d:AllowedHttpMethods="GET"` As opções disponíveis são:

- **Obter:** normalmente utilizado para devolver dados
- **Mensagem:** normalmente utilizado para inserir novos dados
- **Colocar:** normalmente utilizado para atualizar os dados
- **Eliminar:** utilizado para eliminar dados

Nós subordinados adicionais (não cobertos pela documentação CSDL) dentro o nó FunctionImport são:

**d:RequestBody** *(Opcional)* - corpo do pedido é utilizado para indicar que o pedido de espera um corpo sejam enviadas. Parâmetros podem ser fornecidos no corpo do pedido. Estes são expresso dentro de chavetas, por exemplo, {parameterName}. Estes parâmetros são mapeados da entrada do utilizador para o corpo que é transferida para o serviço do fornecedor de conteúdos. O elemento requestBody tem um atributo com nome httpMethod. O atributo permite que os dois valores:

- **Mensagem:** Utilizado se o pedido for uma mensagem de HTTP
- **Obter:** Utilizado se o pedido for uma HTTP GET

    Exemplo:

        `<d:RequestBody d:httpMethod="POST">
        <![CDATA[
        <req1:Request xmlns:r1="http://schemas.mysite.com//generic/requests/1" Version="1.0">
        <req1:Query>{Query}</req1:Query><req1:AppId>D453474</req1:AppId>
        <req:DestinationSchemas><req1:Schema>Generic.RequestResponse[1.0]</req1:Schema>
        </req1:DestinationSchemas>
        </req1: Request>
        ]]>
        </d:RequestBody>`

**d:Namespaces** e **d:Namespace** - este nó descreve os espaços de nomes que são definidos em XML que é devolvido pela importação de função (ponto final URI). O XML que é devolvido pelo serviço back-end pode conter qualquer número de espaços de nomes para diferenciar o conteúdo que é devolvido. **Todos estes espaços de nomes, se for utilizado nas consultas de XPath d:Map ou d:Match tem de estar listada.** O nó d:Namespaces contém uma conjunto de lista de nós do d:Namespace. Cada um deles lista um espaço de nomes utilizado na resposta do serviço de back-end. Seguem-se o atributo do nó d:Namespace:

-   **d:Prefix:** O prefixo do espaço de nomes, conforme visto no XML resultados devolvidos pelo serviço, por exemplo, f:FirstName, f:LastName, onde f é o prefixo.
- **d:Uri:** O URI completo do espaço de nomes utilizado no documento resultado. Representa o valor que o prefixo é resolvido para o tempo de execução.

**d:ErrorHandling** *(Opcional)* - este nó contém condições para processamento de erros. Cada uma das condições é validada contra o resultado que é devolvido pelo serviço o fornecedor de conteúdo. Se uma condição corresponde ao código de erro HTTP proposto uma mensagem de erro é devolvida para o utilizador final.

**d:ErrorHandling** *(Opcional)* e **d:Condition** *(opcional)* - um nó condição mantém uma condição que está selecionada no resultado devolvido pelo serviço o fornecedor de conteúdo. Seguem-se os atributos **necessários** :

- **d:Match:** Uma expressão XPath que valida se um determinado valor/nó é presente na saída do fornecedor do conteúdo XML. O XPath é executado contra a saída e deve devolver VERDADEIRO se a condição é uma correspondência ou FALSO, caso contrário.
- **d:HttpStatusCode:** O código de estado HTTP que deve ser devolvido por Marketplace no caso a condição corresponde. Marketplace signalizes erros ao utilizador através de códigos de estado HTTP. Uma lista de códigos de estado HTTP estão disponíveis no http://en.wikipedia.org/wiki/HTTP_status_code
- **d:ErrorMessage:** A mensagem de erro que é devolvida – com o código de estado HTTP – para o utilizador final. Isto deve ser uma mensagem de erro amigáveis que não contêm qualquer segredos.

**d:Title** *(Opcional)* - permite que descreve o título da função. O valor para o título está a chegar a partir do

- O mapa opcional atributo (um xpath) que indica onde encontrar o título na resposta devolvida a partir do pedido de serviço.
- - Ou - o título especificado como valor do nó.

**d:Rights** *(Opcional)* - os direitos (por exemplo, direitos de autor) associados a função. O valor para os direitos está a chegar a partir de:

- O mapa opcional atributo (um xpath) que indica onde encontrar os direitos na resposta devolvida a partir do pedido de serviço.
-   - Ou - os direitos especificados como valor do nó.

**d:Description** *(Opcional)* - uma breve descrição para a função. Está a chegar a partir do valor para a descrição

- O mapa opcional atributo (um xpath) que especifica onde localizar a descrição na resposta devolvida a partir do pedido de serviço.
- - Ou -a descrição da especificado como valor do nó.

**d:EmitSelfLink** - *consulte acima exemplo "FunctionImport paginação através de dados devolvidos"*

**d:EncodeParameterValue** - extensão opcional a OData

**d:QueryResourceCost** - extensão opcional a OData

**d:Map** - extensão opcional a OData

**d:Headers** - extensão opcional a OData

**d:Headers** - extensão opcional a OData

**d:Value** - extensão opcional a OData

**d:HttpStatusCode** - extensão opcional a OData

**d:ErrorMessage** - extensão opcional a OData

## <a name="parameter-node"></a>Nó de parâmetro

Este nó representa um parâmetro que está exposto como parte do modelo de URI / pedir corpo que tenha sido especificado no nó FunctionImport.

Uma página de documento muito útil detalhes sobre o nó "Elemento do parâmetro" é encontrada em [aqui](http://msdn.microsoft.com/library/ee473431.aspx) (utilize no menu pendente **Versão outras** para selecionar uma versão diferente, se for necessário para visualizar a documentação). *Exemplo:*`<Parameter Name="Query" Nullable="false" Mode="In" Type="String" d:Description="Query" d:SampleValues="Rudy Duck" d:EncodeParameterValue="true" MaxLength="255" FixedLength="false" Unicode="false" annotation:StoreGeneratedPattern="Identity"/>`

| Atributo de parâmetro | É necessário | Valor |
|----|----|----|
| Nome | Sim | O nome do parâmetro. Sensível às maiúsculas e!  Corresponde as maiúsculas/minúsculas BaseUri. **Exemplo:**`<Property Name="IsDormant" Type="Byte" />` |
| Tipo | Sim | O tipo de parâmetro. O valor tem de ser um **EDMSimpleType** ou um tipo de complexo que esteja no âmbito do modelo. Para mais informações, consulte "6 suportados parâmetro/propriedade tipos".  (Sensível a maiúsculas e minúsculas! Primeira CARÁCT está em maiúscula, resto minúsculas.)  Consulte também, [Conceptual modelo tipos (CSDL)] [MSDNParameterLink]. **Exemplo:**`<Property Name="LimitedPartnershipID " Type="Int32" />` |
| Modo | N | **Em**, ou InOut dependendo se o parâmetro é uma entrada, saída ou parâmetro de entrada/saída. ("Em" só está disponível no Azure Marketplace.) **Exemplo:**`<Parameter Name="StudentID" Mode="In" Type="Int32" />` |
| Compr. máximo | N | Máximo permitido comprimento do parâmetro. **Exemplo:**`<Property Name="URI" Type="String" MaxLength="100" FixedLength="false" Unicode="false" />` |
| Precisão | N | A precisão do parâmetro. **Exemplo:**`<Property Name="PreviousDate" Type="DateTime" Precision="0" />` |
| Escala | N | A escala do parâmetro. **Exemplo:**`<Property Name="SICCode" Type="Decimal" Precision="10" Scale="0" />` |

[MSDNParameterLink]: (http://msdn.microsoft.com/library/bb399548 (v=VS.100).aspx)

Seguem-se os atributos que tenham sido adicionados a especificação de CSDL:

| Atributo de parâmetro | Descrição |
|----|----|
| **d:RegEx** *(Opcional)* | Uma instrução regex utilizada para validar o valor para o parâmetro de entrada. Se o valor de entrada não corresponder a declaração do valor é rejeitado. Esta opção permite-também especificar um conjunto de valores possíveis, por exemplo, ^ [0-9] +? $ para permitir apenas números. **Exemplo:** ' < o nome do parâmetro = "nome" modo = "Em" tipo = "Cadeia" d: anuláveis = d:Regex "false" = "^ [a-zA-Z] * $" d:Description = d:SampleValues "um nome que não pode conter espaços ou carateres de não ingleses não alfa" = "Jorge|João|Ferreira|Fernando "/ >' |
| **d:Enum** *(Opcional)* | Lista de valores válidos para o parâmetro separados por um pipe. O tipo dos valores tem de corresponder ao tipo do parâmetro definido. Exemplo: ' inglês|métrica|Observou`. Enum will display as a selectable dropdown list of parameters in the UI (service explorer). **Example:** `< o nome do parâmetro = "Duração" tipo = "Cadeia" modo = "Em" Nullable = d:Enum "true" = "1 ano|5years|10years "/ >' |
| **d: anuláveis** *(Opcional)* | Permite que define se um parâmetro pode ser nulo. A predefinição é: VERDADEIRO. No entanto, parâmetros são expostos como parte do caminho no modelo de URI não podem ser nulos. Quando o atributo está definido como falso para estes parâmetros – a entrada do utilizador é substituída. **Exemplo:**`<Parameter Name="BikeType" Type="String" Mode="In" Nullable="false"/>` |
| **d:SampleValue** *(Opcional)* | Um valor de exemplo para apresentar como uma nota para o cliente na interface de utilizador.  É possível adicionar vários valores utilizando uma lista de pipe separado, ou seja, ' um|b|c` **Example:** `< o nome do parâmetro = "BikeOwner" tipo = "Cadeia" modo = "Em" d:SampleValues = "Jorge|João|Ferreira|Fernando "/ >' |

## <a name="entitytype-node"></a>Nó EntityType

Este nó representa um dos tipos de que são devolvidos do Marketplace para o utilizador final. Também contém o mapeamento do resultado que é devolvido pelo serviço o fornecedor de conteúdos para os valores que são devolvidos ao utilizador final.

Obter mais detalhes sobre este nó encontram-se em [aqui](http://msdn.microsoft.com/library/bb399206.aspx) (utilize no menu pendente **Versão outras** para selecionar uma versão diferente, se for necessário para visualizar a documentação.)

| Nome do atributo | É necessário | Valor |
|----|----|----|
| Nome | Sim | O nome do tipo de entidade. **Exemplo:**`<EntityType Name="ListOfAllEntities" d:Map="//EntityModel">` |
| Base | N | O nome de outro tipo de entidade que é o tipo de base do tipo de entidade que está a ser definido. **Exemplo:**`<EntityType Name="PhoneRecord" BaseType="dqs:RequestRecord">` |

Seguem-se os atributos que tenham sido adicionados a especificação de CSDL:

**d:Map** - expressão An XPath executada em relação a saída de serviço. Partem do princípio de aqui é que a saída de serviço contém um conjunto de elementos que se repetem, como um ATOM feed onde existe um conjunto de nós de entrada que se repetem. Cada um de nós de repetição contém um registo. O XPath, em seguida, é especificado para aponte para o nó repetição individual no resultado de serviço do fornecedor do conteúdo que contém os valores de um registo individual. Exemplo de saída de serviço:

        `<foo>
          <bar> … content … </bar>
          <bar> … content … </bar>
          <bar> … content … </bar>
        </foo>`

Expressão XPath seria ser /foo/barra uma vez que cada da barra de nó for a repetição a saída e contém o conteúdo em si que é devolvido para o utilizador final.

**Chave** - deste atributo é ignorado pelo Marketplace. RESTO baseados serviços web, em geral, não expõem uma chave primária.


## <a name="property-node"></a>Nó de propriedade

Este nó contém uma propriedade do registo.

Obter mais detalhes sobre este nó encontram-se em [http://msdn.microsoft.com/library/bb399546.aspx](http://msdn.microsoft.com/library/bb399546.aspx) (utilize no menu pendente **Versão outras** para selecionar uma versão diferente, se for necessário para visualizar a documentação.) *Exemplo:*
        `<EntityType Name="MetaDataEntityType" d:Map="/MyXMLPath">
        <Property Name="Name"   Type="String" Nullable="true" d:Map="./Service/Name" d:IsPrimaryKey="true" DefaultValue=”Joe Doh” MaxLength="25" FixedLength="true" />
        ...
        </EntityType>`

| NomeAtributo | Obrigatório | Valor |
|----|----|----|
| Nome | Sim | O nome da propriedade. |
| Tipo | Sim | O tipo do valor da propriedade. O tipo de valor de propriedade tem de ser um **EDMSimpleType** ou um tipo de complexo (identificadas por um nome completamente qualificado) que é dentro do âmbito do modelo. Para mais informações, consulte o artigo tipos de modelo Conceptual (CSDL). |
| Anuláveis | N | **Verdadeiro** (o valor predefinido) ou **False** dependendo se a propriedade pode ter um valor nulo. Nota: A versão do CSDL indicado por [http://schemas.microsoft.com/ado/2006/04/edm](http://schemas.microsoft.com/ado/2006/04/edm) espaço de nomes, uma propriedade tipo complexo tem de ter Nullable = "False". |
| ValorPredefinido | N | O valor predefinido da propriedade. |
|Compr. máximo | N | O comprimento máximo do valor da propriedade. |
| FixedLength | N | **True** ou **False** dependendo se o valor da propriedade será armazenado como uma cadeia de comprimento fiexed. |
| Precisão | N | Refere-se para o número máximo de dígitos para manter o valor numérico. |
| Escala | N | Número máximo de casas decimais para manter o valor numérico. |
| Unicode | N | **True** ou **False** dependendo se o valor da propriedade ser armazenado como uma cadeia de Unicode. |
| Agrupamento | N | Uma cadeia que especifica a sequência de ordenação para ser utilizado na origem de dados. |
| ConcurrencyMode | N | **Nenhum** (o valor predefinido) ou **fixa**. Se o valor estiver definido para **fixa**, o valor da propriedade será utilizado nos controlos de simultaneidade optimista. |

Seguem-se os atributos adicionais que tenham sido adicionados a especificação de CSDL:

**d:Map** - expressão XPath executada em relação ao serviço de saída e extrai de uma propriedade dos resultados. O XPath especificado é relativo o nó repetição que foi seleccionado no XPath o nó EntityType. Também é possível especificar um XPath absoluto para permitir que inclua um recurso estático em cada uma dos nós de saída, como por exemplo uma instrução de direitos de autor apenas encontra-se uma vez no serviço original de saída, mas deve ser apresentado em cada uma das linhas na saída de OData. Exemplo de serviço:

        `<foo>
          <bar>
           <baz0>… value …</baz0>
           <baz1>… value …</baz1>
           <baz2>… value …</baz2>
          </bar>
        </foo>`

Expressão XPath aqui seria ./bar/baz0 para obter o nó baz0 de serviço do fornecedor de conteúdos.

**d:CharMaxLength** - para tipo de cadeia, pode especificar o comprimento máximo. Consulte o artigo CSDL de serviço de dados de exemplo

**d:IsPrimaryKey** - indica se a coluna é a chave primária na vista de tabela /. Consulte o artigo CSDL de serviço de dados de exemplo.

**d:isExposed** - determina se o esquema da tabela é exposto (normalmente, VERDADEIRO). Consulte o artigo CSDL de serviço de dados de exemplo

**d:IsView** *(Opcional)* - true se este for baseada numa vista em vez de uma tabela.  Consulte o artigo CSDL de serviço de dados de exemplo

**d:Tableschema** - veja o exemplo CSDL de serviço de dados

**d:ColumnName** - é o nome da coluna na vista de tabela /.  Consulte o artigo CSDL de serviço de dados de exemplo

**d:IsReturned** - é o valor booleano que determina se o serviço expõe este valor para o cliente.  Consulte o artigo CSDL de serviço de dados de exemplo

**d:IsQueryable** - é o valor booleano que determina se a coluna pode ser utilizada numa consulta de base de dados.   Consulte o artigo CSDL de serviço de dados de exemplo

**d:OrdinalPosition** - é numérica posição a coluna do aspeto, x, na tabela ou a vista, onde x é 1 para o número de colunas na tabela.  Consulte o artigo CSDL de serviço de dados de exemplo

**d:DatabaseDataType** - é o tipo de dados da coluna na base de dados, ou seja, de tipo de dados SQL. Consulte o artigo CSDL de serviço de dados de exemplo

## <a name="supported-parametersproperty-types"></a>Tipos de parâmetros/propriedade suportados
Seguem-se os tipos de suportados para parâmetros e propriedades. (Sensível a maiúsculas e minúsculas)

| Tipos primitivos | Descrição |
|----|----|
| Nulo | Representa a ausência de um valor |
| Booleano | Representa o conceito de matemático de valor binário lógica|
| Byte | Valor inteiro de 8 bits assinado|
|Data/hora| Representa a data e hora com valores que se situa entre 12:00:00 à meia-noite, 1 de Janeiro de 1753 D.C. através de 11:59:59 p. m, Dezembro de 9999 D.C.|
|Decimal | Representa valores numéricos com precisão fixa e a escala. Este tipo pode descrever um valor numérico que se situa entre 10 negativo ^ 255 + 1 para 10 positivo ^ 255 -1|
| Dupla | Representa uma número com uma precisão de 15 dígitos que pode representar valores com intervalo aproximado de +-2, 23E-308 através de + 1, 79E de vírgula flutuante +308. **Utilize Casa Decimal devido Exel exportar problema**|
| Única | Representa uma número com uma precisão de 7 dígitos que pode representar valores com intervalo aproximado de +, 1, 18E-38 através de + 3, 40E de vírgula flutuante + 38|
|GUID |Representa um valor de identificador exclusivo do 16 byte (128-bit) |
|Int16|Representa um valor de número inteiro de 16 bits assinado |
|Int32|Representa um valor de número inteiro de 32 bits assinado |
|Int64|Representa um valor de número inteiro de 64 bits assinado |
|Cadeia | Representa ou variável-caráter dados de comprimento fixo |


## <a name="see-also"></a>Consulte também
- Se estiver interessado em Noções sobre o processo de mapeamento de OData e a finalidade geral, leia este artigo [Mapeamento de OData do serviço de dados](marketplace-publishing-data-service-creation-odata-mapping.md) para rever as definições, estruturas e instruções.
- Se estiver interessado em exemplos de revisão, leia este artigo [OData mapeamento de exemplos de serviço de dados](marketplace-publishing-data-service-creation-odata-mapping-examples.md) para ver o código de exemplo e compreender sintaxe do código e contexto.
- Para voltar para o caminho prescrito publicação de um serviço de dados ao Azure Marketplace, leia este artigo [Guia de publicação de serviço de dados](marketplace-publishing-data-service-creation.md).
