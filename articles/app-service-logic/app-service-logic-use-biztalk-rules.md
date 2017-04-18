<properties
   pageTitle="Saiba mais sobre e criar uma aplicação BizTalk regras API na sua aplicação de lógica | Microsoft Azure"
   description="Este tópico abrange as funcionalidades de conexão BizTalk regras e fornece instruções sobre a sua utilização"
   services="logic-apps"
   documentationCenter=".net,nodejs,java"
   authors="anuragdalmia"
   manager="erikre"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="04/20/2016"
   ms.author="andalmia"/>

#<a name="biztalk-rules"></a>Regras de BizTalk

[AZURE.INCLUDE [app-service-logic-version-message](../../includes/app-service-logic-version-message.md)]

Regras de negócio contém as políticas e as decisões que controlam processos de negócio. Estas políticas podem ser definidas formal na manuais de procedimento, há contratos que ou acordos ou poderão existir como dados de conhecimento ou conhecimentos incorporados num empregados. Estas políticas são dinâmicas e sujeitos a alteração ao longo do tempo devidas a alterações no planos de negócio, regulamentos ou outras razões.

Estas políticas de execução em linguagens de programação tradicionais requer tempo substancial e coordená-lo e não ativar os não programadores participar em criação e manutenção de políticas de negócio. Regras de negócio BizTalk fornece uma maneira de rapidamente implementar estas políticas e separar o resto do processo de negócio. Esta opção permite-para efetuar as alterações necessárias para políticas de negócio sem que afetam o resto do processo de negócio.

##<a name="why-rules"></a>Porque é que as regras

Existem 3 principais razões para utilizar as regras de negócio BizTalk no processo de negócio:

* Separar lógica de negócio a partir do código da aplicação
- Permitir analistas de negócio ter mais controlo sobre a gestão de lógica de negócio
+ Alterações a lógica empresarial Ir para produção mais rápido

##<a name="rules-concepts"></a>Conceitos de regras

##<a name="vocabulary"></a>Vocabulário

Um _vocabulário_ é uma coleção de definições que consiste nomes amigáveis para os objetos computação utilizados em condições da regra e ações. Definições de vocabulário facilite as regras de leitura, entender e partilhar por pessoas num domínio empresarial específico.

Vocabularies foram concebidos para una o intervalo entre semântica de negócio e pós-implementação. Por exemplo, uma ligação de dados para um Estado de aprovação pode apontar para uma determinada coluna numa linha numa determinada base de dados, representado como uma consulta SQL. Em vez de inserir deste tipo de representação complexo numa regra, em vez disso, poderá criar uma definição de vocabulário, associada a essa ligação de dados, com um nome amigável da "Estado". Subsequentemente pode incluir "Estado" em qualquer número de regras e o motor de regra a poderá obter os dados correspondentes da tabela.

##<a name="rule"></a>Regra

Regras de negócio são declarações declarativas que governem a realização de processos de negócio. Uma regra é composta por uma condição e ações. A condição é avaliada, e se é avaliado como true, o motor de regra inicia uma ou mais ações.
Regras na arquitetura de regras de negócio são definidas pelas utilizando o seguinte formato:

_IF_ _condição_ _Em seguida_ _ação_

Considere o seguinte exemplo:

*Quantidade se é menor ou igual a fundos disponíveis*  
*Em seguida, realizar transação e recibo de impressão*

Esta regra determina se será ser realizada uma transação da aplicando lógica empresarial, sob a forma de uma comparação de dois valores monetários, a forma de um montante da transação e fundos disponíveis.
Pode utilizar a regra de negócio para criar, modificar e implementar regras de negócio. Em alternativa, pode executar as tarefas anteriores através de programação.

###<a name="conditions"></a>Condições

Uma condição é um expressão (booleana) que consiste numa ou mais predicados verdadeiro/falso.
No nosso exemplo, o predicado menor ou igual a é aplicado à quantia e fundos disponíveis. Esta condição sempre irá avaliar como true ou false.
Predicados podem ser combinados com os operadores lógicos AND, OR e não para formar uma expressão lógica que seja potencialmente bastante grande, mas irão sempre avaliar como true ou false.

###<a name="actions"></a>Ações

Ações são as consequências funcionais de avaliação de condição. Se uma condição regra é cumprida, são iniciadas uma ação correspondente ou ações.
No nosso exemplo, "realizar transação" e "Imprimir recibo" é ações que são realizadas quando e apenas quando, a condição (neste caso, "se valor for menor ou igual a fundos disponíveis") é verdadeira.
Ações são representadas no quadro de regras de empresas através da execução de operações de conjunto em documentos XML.

##<a name="policy"></a>Política

Uma política é um agrupamento lógico de regras. Componha uma política, guardá-lo, testá-la e, quando estiver satisfeito com os resultados, utilizá-lo num ambiente de produção.

###<a name="policy-composition"></a>Composição da política

Pode compor políticas no portal do regras. Uma política pode conter um conjunto de regras arbitrariamente grande, mas normalmente ao compor uma política de regras que pertencem aos domínio empresarial específicos dentro do contexto da aplicação que irá utilizar a política.

###<a name="policy-testing"></a>Testes de política
Pode executar executar um teste da política de forma eficaz antes de ser utilizado num ambiente de produção. O Portal de regras permite-lhe fornecer entradas para uma política, execute a política e ver os resultados. O resultado inclui registos, a execução da regra, a avaliação de condição e saídas resultantes.

##<a name="sample-scenario---insurance-claims"></a>Cenário - créditos de seguros de exemplo
Vamos demorar um cenário de exemplo e percorrer podemos compor a lógica empresarial para a mesma.

![Texto alternativo][1]

Num cenário de créditos de seguros realmente simple, coabitem submete dele afirmação seguro (através do qualquer cliente como o Web site, telefone aplicação, etc). Este pedido de afirmação é enviada para afirmação processamento unidade o negócio e com base no resultado da transformação, a afirmação pode ser qualquer um dos aprovado, rejeitado ou enviadas ao longo para transformação subsequente manual.
A unidade de processamento de afirmação nosso cenário seria aquele que abrange a lógica empresarial para o sistema. A demorar mais detalhada esta unidade, é possível ver o seguinte procedimento:

![Texto alternativo][2]

Diga-nos utilize agora regras de negócio para implementar o este lógica empresarial.


##<a name="creation-of-rules-api-app"></a>Criação de regras Api aplicação


1. Inicie sessão no Portal do Azure
2. Selecione novo -> Marketplace, em seguida, procure *BizTalk regras*
3. Selecione BizTalk regras a partir da lista de resultados. Abre o pá BizTalk regras
4. Selecione o botão *Criar* ![texto alternativo][3]
1. Na pá nova que se abre, introduza as seguintes informações:  
    1. Nome – atribua um nome para a sua aplicação de API de regras
    1. Plano de serviço de aplicação – selecione ou crie um novo plano de serviço de aplicação
    1. Preços camada – escolha a comparar camada que pretende esta aplicação às residem na
    1. Grupo de recursos – selecione ou criar grupo de recursos sempre que a aplicação deverá residem na
    2. Subscrição - selecione a subscrição que pretende utilizar
    1. Localização – escolha a localização geográfica onde pretende que a aplicação para ser implementada.
4.  Selecione *Criar*. Dentro de alguns minutos a sua aplicação de API de regras de BizTalk deverá ser criada.

##<a name="vocabulary-creation"></a>Criação de vocabulário
Depois de criar uma aplicação de API BizTalk regras, o próximo passo seria criar vocabularies. A expetativa é que o programador é o persona mais comuns para ser efetuar este exercício. Eis como obter este concluído:


1. Iniciação seu BizTalk regras API aplicação a partir do portal ao aceder a procurar -> API aplicações - ><Your Rules API App>. Obterá ao dashboard de aplicação de API regras semelhante ao abaixo:

   ![Texto alternativo][4]

2. Selecione "Definições de vocabulário". Isto mostram-lhe a 3. vocabulário criação ecrã selecione "Adicionar" para começar a adicionar novas definições de vocabulário.
Existem 2 tipos de definições vocabulário atualmente suportados – Literal e XML.

##<a name="literal-definition"></a>Definição de literal
1.  Depois de clicar em "Adicionar", um pá "Adicionar definição" nova abre-se para cima. Introduza os seguinte valores
  1.    Nome – apenas carateres alfanuméricos são esperados sem quaisquer caracteres especiais. Isto também deve ser exclusivo à sua lista de definição de vocabulário existente.
  2.    Descrição – campo opcional.
  3.    Tipo de definição – existem 2 tipos suportados. Selecione Literal neste exemplo
  4.    Tipo de dados – Isto permite aos utilizadores selecionar o tipo de dados da definição. Atualmente são suportados tipos de dados de 4: Posso.  Cadeia – estes valores têm de ser introduzidas aspas ("exemplo de uma cadeia")  
    II. Booleano – este pode ser VERDADEIRO ou FALSO  
    III.    Número – pode ser qualquer número decimal  
    IV. DateTime – Isto significa que a definição é do tipo de data do tipo. Dados têm de ser introduzidos utilizar este formato – dd/mm/aaaa ss AM\PM  
  5. Introdução – este é onde pode introduzir o valor da sua definição. Os valores introduzidos aqui tem de estar em conformidade com o tipo de dados que selecionou. Pode introduzir um valor único, um conjunto de valores separados por vírgulas ou um intervalo de valores a utilizar a palavra-chave *para*. Por exemplo, pode introduzir valor exclusivo 1; um conjunto de 1, 2, 3; ou um intervalo de 1 a 5. Tenha em atenção que intervalo só é suportado para números.
  6. Clique *em OK*.

![Texto alternativo][5]
##<a name="xml-definition"></a>Definição de XML
Se o tipo de vocabulário é escolhido como XML, as seguintes entradas tem de ser especificado  
  um.    Esquema-clique nesta será aberto um novo utilizador permitindo pá para escolher a partir de uma lista de esquemas já carregados ou permissão para carregar um novo.
b.    XPATH – neste entrada obtém desbloqueados apenas depois de escolher um esquema no passo anterior. Ao clicar nisto irá apresentar o esquema que foi selecionado e permite ao utilizador selecionar o nó para o qual uma definição de vocabulário tem de ser criado.  
  c.    FACTORIAL – esta entrada identifica que objeto de dados deverá ser alimentado ao motor de regras para processamento. Esta é uma propriedade avançada e por predefinição está definida para o elemento principal do XPATH selecionado. FACTORIAL torna-se particularmente importante para recolha e a interligação de cenários.

![Texto alternativo][6]

### <a name="add-bulk"></a>Adicionar em volume
Os passos acima capturadas a experiência de criação de definições de vocabulário. Depois de criada, as definições de vocabulário criado aparecerá no formulário de lista. Existem requisitos possam gerar várias definições a partir do mesmo esquema em vez de repetir os passos acima sempre único. Este é onde fica muito útil capacidade de adicionar em volume.
Selecionar "Adicionar em volume" será direcionado para uma nova pá. O primeiro passo é selecionar o esquema para o qual várias definições estão a ser criada. Selecionar esta será aberto um novo pá tem a permissão que quer escolher de uma lista de esquemas já carregados ou permitir para carregar um novo.
Agora a propriedade XPath obtém desbloqueada. Selecionar esta será aberto o Visualizador de esquema onde vários nós podem ser selecionados.
Os nomes de várias definições criados serão predefinida para o nome do nó selecionado. Estes sempre podem ser modificadas após a criação.

![Texto alternativo][7]

##<a name="policy-creation"></a>Criação de política
Assim que o programador tiver criado vocabularies necessários, a expetativa é que o analista de negócio seria as políticas de empresas criar um através do Portal do Azure.  
    1. a aplicação de regras criadas, há uma lente de política clicando em que o utilizador será Ir para a página de criação de política.  
    2. Esta página irá mostrar a lista de políticas que tem esta aplicação regras específico. O analista pode adicionar uma nova política, basta escrever um nome de política e atingir o separador duas vezes. Várias políticas podem residem numa única aplicação API regras.  
    3. selecionando a política criada entrarão em ao utilizador para a página de detalhes de política onde um pode ver as regras que estão na política.  
    ![Texto alternativo][8]
 4.  Selecione "Adicionar" para adicionar uma nova regra. Esta será direcionado para uma nova pá.

##<a name="rule-creation"></a>Criação de regras
Uma regra é uma coleção de declarações de condição e acção. As ações são executadas se a condição devolver o valor verdadeiro. Em pá criar regra, dar um nome exclusivo regra (para essa política) e uma descrição (opcional).
A caixa de condição (se) pode ser utilizada para criar instruções condicionais complexas. Seguem-se as palavras-chave suportadas:  
1.  E – operador condicional  
2.  Ou – operador condicional  
3.  faz\_não\_existe  
4.  existe  
5.  FALSO  
6.  é\_igual\_para  
7.  é\_maior\_que  
8.  é\_maior\_que\_igual\_para  
9.  é\_no  
10. é\_menos\_que  
11. é\_menos\_que\_igual\_para  
12. é\_não\_no  
13. é\_não\_igual\_para  
14. resto  
15. VERDADEIRO  

A caixa de Action(THEN) pode conter várias declarações um por linha, para criar as ações que estão a ser executado. Seguem-se as palavras-chave suportadas:  
1.  é igual a  
2.  FALSO  
3.  VERDADEIRO  
4.  parar  
5.  resto  
6.  nulo  
7.  Atualizar  

As caixas de condição e acção fornecem Intellisense para o ajudar a criar rapidamente uma regra. Isto pode acionado ao atingir ctrl + barra de espaço ou ao basta começar a escrever. Palavras-chave correspondência de caracteres escritos serão automaticamente filtrado para baixo e apresentado. A janela de intellisense irá mostrar todas as palavras-chave e vocabulário definições.
![Texto alternativo][9]

##<a name="explicit-forward-chaining"></a>Reencaminhar explícita interligação
Suporta de regras de BizTalk explícitas reencaminhar interligação Sim, se optar por utilizadores voltar a avaliar regras em resposta ao determinadas ações, podem accionar isto utilizando determinadas palavras-chave. Seguem-se as palavras-chave suportadas:  
   1.   Atualizar <vocabulary definition> – esta palavra-chave avalia novamente todas as regras que utilizam a definição de vocabulário especificado na sua condição.  
   2.   Paragem – esta palavra-chave deixa de todas as execuções de regra

##<a name="enabledisable-rules"></a>Regras de Enable\Disable
Cada regra na política pode ser ativada ou desactivada. Por predefinição, todas as regras de estão ativadas. Regras desativadas impossível ser executada durante a execução de política. Regras de Enable\Disable podem ser feitas quer a partir do pá regra diretamente – os comandos estão disponíveis na barra de comandos na parte superior da pá ou da política, o menu de contexto (botão direito do rato numa regra) tem a opção de enable\disable.

##<a name="rule-priority"></a>Regra prioridade
Todas as regras de uma política de são executadas por ordem. A prioridade da execução é determinada pela ordem em que ocorrem na política. Esta prioridade pode ser alterada ao basta arrastar e largar a regra.

##<a name="test-policy"></a>Política de teste
Pode testar o seu políticas utilizando o comando "Testar política" no pá a política de teste. Neste pá pode ver uma lista das definições de vocabulário que são utilizadas na política que requerem uma entrada do utilizador. Os utilizadores podem adicionar manualmente valores para estas entradas para os respetivos cenário de teste. Em alternativa, os utilizadores também podem optar por importar testar XMLs para entradas do tipo. Depois de todas as entradas estiverem a ser, o teste pode ser executado e resultados para cada definição do vocabulário são apresentados na coluna Exportar para uma fácil comparação. Para ver registos amigáveis analista de negócio, clique no "Ver registos" para ver os registos de execução. Para guardar os registos de início, a opção de "Guardar saída" está disponível para armazenar testar todos os dados relacionados para uma análise independente.

## <a name="using-rules-in-logic-apps"></a>Utilizar as regras no aplicações de lógica
Assim que a política foi criada e testada, está agora pronto para consumo. Pode criar uma nova aplicação de lógica selecionando lógica aplicações a partir do lado esquerdo da home page do portal. Assim que a sua aplicação de lógica ter sido criada, iniciá-la, em seguida, selecione *Accionadores e ações*. Em seguida, pode selecionar o modelo *criar de raiz* . Siga os passos para adicionar a sua aplicação de API de regras de BizTalk à aplicação lógica. Quando este estiver concluída, vai ser uma opção para escolher qual as regras a aplicação API (acção) para destino. Acções incluem a lista de políticas que estão a ser executado. Selecione uma política específica após o qual as entradas necessário para a política precisa de. Os utilizadores podem utilizar a saída a partir da aplicação de API regras a jusante para outra tomada de decisões.

## <a name="using-rules-via-apis"></a>Utilizar as regras através do APIs
Também pode ser chamada a aplicação de API regras utilizando um conjunto avançado de APIs. Neste modo como os utilizadores não estão restringido a utilizar apenas o lógica de aplicações e podem utilizar as regras em qualquer aplicação ao efetuar chamadas de descanso. Podem ser visualizadas APIs do resto exata disponíveis ao clicar na lente de "API definição" no dashboard de regras.

![Texto alternativo][10]

Segue-se um exemplo de como um poderá utilizar esta API numa C#

            // Constructing the JSON message to use in API call to Rules API App

            // xmlInstance is the XML message instance to be passed as input to our Policy
            string xmlInstance = "<ns0:Patient xmlns:ns0=\"http://tempuri.org/XMLSchema.xsd\">  <ns0:Name>Name_0</ns0:Name>  <ns0:Email>Email_0</ns0:Email>  <ns0:PatientID>PatientID_0</ns0:PatientID>  <ns0:Age>10.4</ns0:Age>  <ns0:Claim>    <ns0:ClaimDate>2012-05-31T13:20:00.000-05:00</ns0:ClaimDate>    <ns0:ClaimID>10</ns0:ClaimID>    <ns0:TreatmentID>12</ns0:TreatmentID>    <ns0:ClaimAmount>10000.0</ns0:ClaimAmount>    <ns0:ClaimStatus>ClaimStatus_0</ns0:ClaimStatus>    <ns0:ClaimStatusReason>ClaimStatusReason_0</ns0:ClaimStatusReason>  </ns0:Claim></ns0:Patient>";

            JObject input = new JObject();

            // The JSON object is to be of form {"<XMLSchemName>_<RootNodeName>":"<XML Instance String>".
            // In the below case, we are using XML Schema - "insruanceclaimsschema" and the root node is "Patient".
            // This is CASE SENSITIVE.
            input.Add("insuranceclaimschema_Patient", xmlInstance);
            string stringContent = JsonConvert.SerializeObject(input);


            // Making REST call to Rules API App
            HttpClient httpClient = new HttpClient();

            // The url is the Host URL of the Rules API App
            httpClient.BaseAddress = new Uri("https://rulesservice77492755b7b54c3f9e1df8ba0b065dc6.azurewebsites.net/");
            HttpContent httpContent = new StringContent(stringContent);
            httpContent.Headers.ContentType = MediaTypeHeaderValue.Parse("application/json");

            // Invoking API "Execute" on policy "InsruranceClaimPolicy" and getting response JSON object. The url can be gotten from the API Definition Lens
            var postReponse = httpClient.PostAsync("api/Policies/InsuranceClaimPolicy?comp=Execute", httpContent).Result;

Tenha em atenção que a aplicação de API regras acima tem as suas definições de segurança definidas como "Disponível Anon público". Isto pode ser definido a partir da aplicação de API utilizando - todas as definições -> definições da aplicação -> nível de acesso.

![Texto alternativo][11]

## <a name="editing-vocabulary-and-policy"></a>Editar política e vocabulário
Uma das principais vantagens do utilizando regras de negócio é que as alterações a lógica empresarial podem seguia muitas mais rápida de produção. Qualquer alteração efetuada e políticas de vocabulário é aplicada imediatamente de produção. Utilizador simplesmente precisa navegue para a definição de vocabulário respetivos ou política e altere para a tenha entrar em efeito.

<!--Image references-->
[1]: ./media/app-service-logic-use-biztalk-rules/InsuranceScenario.PNG
[2]: ./media/app-service-logic-use-biztalk-rules/InsuranceBusinessLogic.png
[3]: ./media/app-service-logic-use-biztalk-rules/CreateRuleApiApp.png
[4]: ./media/app-service-logic-use-biztalk-rules/RulesDashboard.png
[5]: ./media/app-service-logic-use-biztalk-rules/LiteralVocab.PNG
[6]: ./media/app-service-logic-use-biztalk-rules/XMLVocab.PNG
[7]: ./media/app-service-logic-use-biztalk-rules/BulkAdd.PNG
[8]: ./media/app-service-logic-use-biztalk-rules/PolicyList.PNG
[9]: ./media/app-service-logic-use-biztalk-rules/RuleCreate.PNG
[10]: ./media/app-service-logic-use-biztalk-rules/APIDef.PNG
[11]: ./media/app-service-logic-use-biztalk-rules/PublicAnon.PNG
