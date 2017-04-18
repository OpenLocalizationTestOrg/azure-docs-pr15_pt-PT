<properties 
    pageTitle="Descrição geral de X12 e o pacote de integração de Enterprise | Aplicação de serviço do Microsoft Azure | Microsoft Azure" 
    description="Saiba como utilizar X12 acordos para criar aplicações de lógica" 
    services="logic-apps" 
    documentationCenter=".net,nodejs,java"
    authors="msftman" 
    manager="erikre" 
    editor="cgronlun"/>

<tags 
    ms.service="app-service-logic" 
    ms.workload="integration" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="07/08/2016" 
    ms.author="deonhe"/>

# <a name="enterprise-integration-with-x12"></a>Integração da empresa com X12 

>[AZURE.NOTE]Funcionalidades de folhas de rosto a X12 esta página de aplicações de lógica. Para obter informações sobre EDIFACT clique [aqui](app-service-logic-enterprise-integration-edifact.md).

## <a name="create-an-x12-agreement"></a>Criar um X12 contrato 
Antes de poder trocar X12 mensagens, tem de criar um X12 contrato e armazená-lo na sua conta de integração. Os passos seguintes irão guiá-lo durante o processo de criação de um X12 contrato.

### <a name="heres-what-you-need-before-you-get-started"></a>Eis o que precisa antes de começar
- Uma [conta de integração](./app-service-logic-enterprise-integration-accounts.md) definida na sua subscrição do Azure  
- Pelo menos mais duas [parceiros](./app-service-logic-enterprise-integration-partners.md) já definidos na sua conta de integração  

>[AZURE.NOTE]Ao criar um contrato, o conteúdo no ficheiro contrato tem de corresponder o tipo de acordo.    


Depois de ter [criado uma conta de integração](./app-service-logic-enterprise-integration-accounts.md) e [adicionado parceiros](./app-service-logic-enterprise-integration-partners.md), pode criar um X12 contrato seguindo estes passos:  

### <a name="from-the-azure-portal-home-page"></a>Na home page de portal Azure

Depois de registo para o [Azure portal](http://portal.azure.com "Azure portal"):  
1. Selecione **Procurar** a partir do menu à esquerda.  

>[AZURE.TIP]Se não vir a ligação **Procurar** , poderá ter de expandir o menu pela primeira vez. Faça o seguinte ao selecionar a ligação **Mostrar o menu de** que está localizada na parte superior esquerda do menu fechado.  

![](./media/app-service-logic-enterprise-integration-overview/overview-1.png)    
2. Escreva *integração* na caixa de pesquisa de filtro, em seguida, selecione **Contas de integração** a partir da lista de resultados.       
![](./media/app-service-logic-enterprise-integration-x12/x12-1-3.png)    
3. No pá de **Contas de integração** aberto, selecione a conta de integração no qual irá criar o contrato. Se não vir qualquer integração contas listas, [criar um primeiro](./app-service-logic-enterprise-integration-accounts.md "All about integration accounts").  
![](./media/app-service-logic-enterprise-integration-x12/x12-1-4.png)  
4.  Selecione o mosaico **acordos** . Se não vir os acordos dispor em mosaico, adicioná-la pela primeira vez.   
![](./media/app-service-logic-enterprise-integration-x12/x12-1-5.png)     
5. Selecione o botão **Adicionar** na pá acordos que é aberta.  
![](./media/app-service-logic-enterprise-integration-agreements/agreement-2.png)  
6. Introduza um **nome** para o contrato, em seguida, selecione o **tipo de acordo**, **Parceiro de anfitrião**, **Identidade de anfitrião**, **Parceiro de convidado**, **Identidade de convidado**, na pá acordos que é aberta.  
![](./media/app-service-logic-enterprise-integration-x12/x12-1.png)  
7. Depois de ter definido a receber propriedades de definições, selecione o botão **OK**  
Vamos continuar:  
8. Selecione **Definições de recepção** para configurar como mensagens recebidas através deste contrato estão a ser processada.  
9. O controlo de definições de recepção estiver dividido em secções seguintes, incluindo identificadores, confirmação, esquemas, Envelopes, números de controlo, validações e definições interno. Configure estas propriedades com base no seu contrato com o parceiro que irá ser trocar mensagens com. Eis uma vista destes controlos, configurá-los com base em como pretende que o presente contrato para identificar e processar as mensagens a receber:  
![](./media/app-service-logic-enterprise-integration-x12/x12-2.png)  

![](./media/app-service-logic-enterprise-integration-x12/x12-3.png)  
10. Selecione o botão **OK** para guardar as definições.  

### <a name="identifiers"></a>Identificadores

|Propriedade|Descrição |
|---|---|
|ISA1 (autorização qualificador)|Selecione o valor de qualificador autorização a partir da lista pendente.|
|ISA2|Opcional. Introduza o valor de autorização de informações. Se o valor introduzido para ISA1 for diferente do 00, introduza um mínimo de um caráter alfanumérico e um máximo de 10.|
|ISA3 (segurança qualificador)|Selecione o valor de qualificador de segurança a partir da lista pendente.|
|ISA4|Opcional. Introduza o valor de informações de segurança. Se o valor introduzido para ISA3 for diferente do 00, introduza um mínimo de um caráter alfanumérico e um máximo de 10.|

### <a name="acknowledgments"></a>Confirmações 

|Propriedade|Descrição |
|----|----|
|TA1 esperado|Selecione esta caixa de verificação para devolver uma confirmação (TA1) técnica ao remetente intercâmbio. Estes confirmações são enviadas para o remetente de intercâmbio com base nas definições de enviar para o contrato.|
|FA esperado|Selecione esta caixa de verificação para devolver uma confirmação (FA) funcional para o remetente de intercâmbio. Em seguida, selecione se pretende que as confirmações 997 ou 999, com base nas versões de esquema que estiver a trabalhar com. Estes confirmações são enviadas para o remetente de intercâmbio com base nas definições de enviar para o contrato.|
|Incluir AK2/IK2 ciclo|Selecione esta caixa de verificação para ativar geração de AK2 ciclos no confirmações funcionais para conjuntos de transação aceite. Nota: Esta caixa de verificação só é activada se tiver selecionado a caixa de verificação FA esperado.|

### <a name="schemas"></a>Esquemas

Escolha um esquema para cada tipo da transação (ST1) e a aplicação de remetente (GS2). Pipeline de receção desassembla a mensagem a receber por correspondência de valores para ST1 e GS2 na mensagem a receber com os valores que definir aqui e o esquema da mensagem a receber com o esquema que definir aqui.

|Propriedade|Descrição |
|----|----|
|Versão|Selecione o X12 versão|
|Tipo da transação (ST01)|Selecione o tipo da transação|
|Aplicação do remetente (GS02)|Selecione a aplicação de remetente|
|Esquema|Selecione o ficheiro de esquema que pretende-nos. Ficheiros de esquema estão localizados na sua conta de integração.|

### <a name="envelopes"></a>Envelopes

|Propriedade|Descrição |
|----|----|
|Utilização de ISA11|Utilize este campo para especificar o separador num conjunto da transação:</br></br>Selecione o identificador padrão para utilizar a notação decimal de "." em vez da notação decimal do documento recebida na editar receba em curso.</br></br>Selecione o separador de repetição para especificar o separador das repetidos ocorrências de um elemento de dados simples ou uma estrutura de dados repetidos. Por exemplo, (^) é normalmente utilizado como separador de repetição. Para esquemas HIPAA, só pode utilizar (^).|

### <a name="control-numbers"></a>Números de controlo

|Propriedade|Descrição |
|----|----|
|Não permitir o número de controlo Interchange duplicados|Selecione esta opção para bloquear intercâmbio duplicados. Se estiver selecionada, o Portal de serviços de BizTalk verifica que o número de controlo interchange (ISA13) para o intercâmbio recebido não corresponder o número de intercâmbio de controlo. Se uma correspondência for detectada, pipeline de receção não processar o intercâmbio.<br/>Caso tenha optado por não permitir intercâmbio duplicados números de controlo, em seguida, pode especificar o número de dias em que a verificação é executada atribuindo o valor adequado para verificar a existência de duplicados ISA13 x dias.|
|Não permitir duplicados de números de controlo do grupo|Selecione esta opção para bloquear intercâmbio com números de controlo do grupo duplicados.|
|Não permitir da transação conjunto controlo números duplicados|Selecione esta opção para bloquear intercâmbio com números de controlo do conjunto de duplicados da transação.|

### <a name="validations"></a>Validações

|Propriedade|Descrição |
|----|----|
|Tipo de mensagem|Tipo de mensagem de editar, como encomenda de compra 850 ou implementação 999 confirmação.|
|Editar validação|Executa a validação de editar em tipos de dados, tal como foi definido pelas propriedades de editar do esquema, restrições de comprimento, elementos de dados vazia e separadores de à direita.|
|Validação expandida|Se o tipo de dados não estiver a editar, validação está no requisito de elemento de dados e permitido repetição, enumerações e validação de comprimento de elemento de dados (mínimo/max).|
|Permitir que os zeros finais/espaços à esquerda|São mantidas qualquer espaço adicional e zero caracteres que são à esquerda ou à direita. Não são removidos.|
|Política de separação à direita|Gera os separadores no intercâmbio recebida. Opções incluem obrigatório, opcional e NotAllowed.|

### <a name="internal-settings"></a>Definições internas

|Propriedade|Descrição |
|----|----|
|Converter em formato decimal implícito Nn de base 10 de valor numérico|Converte um número de editar especificada com o formato Nn para um valor numérico de base 10 no XML intermédio no Portal de serviços do BizTalk.|
|Criar etiquetas XML vazias se os separadores são permitidos|Selecione esta caixa de verificação para que o remetente de intercâmbio incluir vazias etiquetas XML para os separadores de à direita.|
|Processamento de lotes de entrada|Dividir intercâmbio como da transação conjuntos - suspender da transação conjuntos no erro: analisa cada transação definir num intercâmbio num documento XML separado ao aplicar o envelope adequado para o conjunto da transação. Com esta opção, se uma ou mais transação conjuntos no intercâmbio validação falhe, em seguida, serviços BizTalk suspender apenas esses conjuntos da transação. </br></br>Intercâmbio dividido como da transação conjuntos - suspender intercâmbio no erro: analisa cada transação definir num intercâmbio num documento XML separado ao aplicar o envelope adequado. Com esta opção, se uma ou mais transação conjuntos no intercâmbio validação falhe, em seguida, serviços BizTalk suspender o intercâmbio inteiro.</br></br>Preservar intercâmbio - suspender da transação conjuntos no erro: deixa o intercâmbio intactos, criar um documento XML para o intercâmbio por lotes inteiro. Com esta opção, se onAe ou transação mais conjuntos no intercâmbio validação falhe, em seguida, serviços BizTalk suspender apenas esses conjuntos da transação, enquanto continuar a processar todos os conjuntos da transação.</br></br>Preservar intercâmbio - suspender intercâmbio no erro: deixa o intercâmbio intactos, criar um documento XML para o intercâmbio por lotes inteiro. Com esta opção, se uma ou mais transação conjuntos no intercâmbio validação falhe, em seguida, serviços BizTalk suspender o intercâmbio inteiro.</br></br>|

O contrato está pronto para processar mensagens a receber que está em conformidade com o esquema que selecionou.

Para configurar as definições que lidar com mensagens enviadas para parceiros:  
11. Selecione **Definições de envio** para configurar a forma como as mensagens enviadas através deste contrato não para ser resolvido.  

O controlo de definições de envio estiver dividido em secções seguintes, incluindo identificadores, confirmação, esquemas, Envelopes, números de controlo, conjuntos de caracteres e separadores e validação. 

Eis uma vista destes controlos. Efetue as seleções com base em como pretende processar as mensagens enviadas para parceiros através deste contrato:   
![](./media/app-service-logic-enterprise-integration-x12/x12-4.png)  

![](./media/app-service-logic-enterprise-integration-x12/x12-5.png)  

![](./media/app-service-logic-enterprise-integration-x12/x12-6.png)  
12. Selecione o botão **OK** para guardar as definições.  

### <a name="identifiers"></a>Identificadores
|Propriedade|Descrição |
|----|----|
|Qualificador autorização (ISA1)|Selecione o valor de qualificador autorização a partir da lista pendente.|
|ISA2|Introduza o valor de autorização de informações. Se este valor é diferente de 00, em seguida, introduza um mínimo de um caráter alfanumérico e um máximo de 10.|
|Qualificador de segurança (ISA3)|Selecione o valor de qualificador de segurança a partir da lista pendente.|
|ISA4|Introduza o valor de informações de segurança. Se este valor é diferente de 00, para a caixa de texto valor (ISA4), em seguida, introduza um mínimo de um valor alfanumérico e um máximo de 10.|

### <a name="acknowledgment"></a>Confirmação
|Propriedade|Descrição |
|----|----|
|TA1 esperado|Selecione esta caixa de verificação para devolver uma confirmação (TA1) técnica ao remetente intercâmbio. Esta definição especifica que o parceiro de anfitrião quem está a enviar a mensagem pede uma confirmação do parceiro de convidado no contrato do. Estes confirmações são esperadas pelo parceiro de anfitrião, com base nas definições de receber o contrato.|
|FA esperado|Selecione esta caixa de verificação para devolver uma confirmação (FA) funcional para o remetente de intercâmbio e, em seguida, selecione se pretende que as confirmações 997 ou 999, com base nas versões de esquema que estiver a trabalhar com. Estes confirmações são esperadas pelo parceiro de anfitrião, com base nas definições de receber o contrato.|
|Versão FA|Selecione a versão de FA|

### <a name="schemas"></a>Esquemas
|Propriedade|Descrição |
|----|----|
|Versão|Selecione o X12 versão|
|Tipo da transação (ST01)|Selecione o tipo da transação|
|ESQUEMA|Selecione o esquema para utilizar. Esquemas encontram-se na sua conta de integração. Para aceder ao seu esquemas, ligação sua conta de integração para a sua aplicação de lógica.|

### <a name="envelopes"></a>Envelopes
|Propriedade|Descrição |
|----|----|
|Utilização de ISA11|Utilize este campo para especificar o separador num conjunto da transação:</br></br>Selecione o identificador padrão para utilizar a notação decimal de "." em vez da notação decimal do documento recebida na editar receba em curso.</br></br>Selecione o separador de repetição para especificar o separador das repetidos ocorrências de um elemento de dados simples ou uma estrutura de dados repetidos. Por exemplo, (^) é normalmente utilizado como separador de repetição. Para esquemas HIPAA, só pode utilizar (^).</br>|
|Separador de repetição|Introduza o separador de repetição|
|Número da versão controlo (ISA12)|Selecione a versão da X12 padrão que é utilizada pelo Portal de serviços BizTalk para gerar um intercâmbio de saída.|
|Indicador de utilização (ISA15)|Introduza se contexto de um intercâmbio é informações (I), dados de produção (P), ou testar dados (T). Receber a editar em curso promove esta propriedade para o contexto.|
|Esquema|Pode introduzir a forma como o Portal de serviços de BizTalk gera os segmentos s e ST para um intercâmbio codificada X12 que envia para Pipeline de enviar.</br></br>Pode associar os valores da GS1, GS2, GS3, GS4, GS5, GS7 e GS8 elementos de dados com valores do tipo da transação e elementos de dados de versão/lançamento. Quando o Portal de serviços de BizTalk determina que uma mensagem de XML tem os valores definidos para o tipo da transação e elementos de versão/numa linha de grelha, em seguida, é preenchida os elementos de dados GS1, GS2, GS3, GS4, GS5, GS7 e GS8 no envelope do envio intercâmbio utilizando os valores da mesma linha da grelha. Os valores da transação tipo e elementos de versão/tem de ser exclusivos.</br></br>Opcional. Para GS1, selecione um valor para o código funcional a partir da lista pendente.</br></br>Obrigatório. Para GS2, introduza um valor alfanumérico ao remetente de aplicação com um mínimo de dois carateres e um máximo de 15 carateres.</br></br>Obrigatório. Para GS3, introduza um valor alfanumérico para no auscultador de aplicação com um mínimo de dois carateres e um máximo de 15 carateres.</br></br>Opcional. Para GS4, selecione CCYYMMDD ou AAMMDD.</br></br>Opcional. Para GS5, selecione HHMM, HHMMSS ou HHMMSSdd.</br></br>Opcional. Para GS7, selecione um valor para a agência responsável a partir da lista pendente.</br></br>Opcional. Para GS8, introduza um valor alfanumérico para o documento identificado com um mínimo de um caráter e um máximo de 12 caracteres.</br></br>**Nota**: estes são os valores que introduz o Portal de serviços de BizTalk nos campos de intercâmbio está a criar se escrever a transação s e elementos de versão/na mesma linha são uma correspondência para aqueles associados ao intercâmbio.|

### <a name="control-numbers"></a>Números de controlo
|Propriedade|Descrição |
|----|----|
|Formatos Graphics Interchange número de controlo (ISA13)|Obrigatório. Introduza um intervalo de valores para o número de controlo de intercâmbio utilizado pelo Portal de serviços do BizTalk gerar um intercâmbio de saída. Introduza um valor numérico com um mínimo de 1 e um máximo de 999999999.|
|Número de controlo de grupo (GS06)|Obrigatório. Introduza o intervalo de números que deve utilizar o Portal de serviços de BizTalk para o número de controlo do grupo. Introduza um valor numérico com um mínimo de um caráter e um máximo de onze carateres.|
|Da transação definir o número de controlo (ST02)|Para o número da transação Definir controlo (ST02), introduza um intervalo de valores numéricos para os campos obrigatórios do meio e valores alfanuméricos para opcional prefixo e sufixo. O comprimento máximo de todos os quatro campos é onze carateres.|
|Prefixo|Para designar o intervalo de números de controlo da transação conjunto utilizados numa confirmação, introduza os valores nos campos número (ST02) ACK controlo. Introduza um valor numérico para os dois campos nome do meio e um valor alfanumérico (se desejar) para os campos prefixo e sufixo. Os campos nome do meio são necessários e contêm os valores mínimos e máximos para o número de controlo o prefixo e sufixo são opcionais. O comprimento máximo de todos os três campos é onze carateres.|
|Sufixo|Para designar o intervalo de números de controlo da transação conjunto utilizados numa confirmação, introduza os valores nos campos número (ST02) ACK controlo. Introduza um valor numérico para os dois campos nome do meio e um valor alfanumérico (se desejar) para os campos prefixo e sufixo. Os campos nome do meio são necessários e contêm os valores mínimos e máximos para o número de controlo o prefixo e sufixo são opcionais. O comprimento máximo de todos os três campos é onze carateres.|

### <a name="character-sets-and-separators"></a>Conjuntos de carateres e separadores
Outros que o conjunto de caracteres, pode introduzir um conjunto diferente de delimitadores para ser utilizado para cada tipo de mensagem. Se não for especificado um conjunto de carateres para esquema de uma determinada mensagem, em seguida, é utilizado o conjunto de carateres predefinido.

|Propriedade|Descrição |
|----|----|
|Conjunto de carateres para ser utilizado|Selecione o X12 conjunto de carateres para validar as propriedades que introduzir para o contrato.</br></br>**Nota**: O Portal de serviços de BizTalk só utiliza esta definição para validar os valores introduzidos para as propriedades de contrato relacionados. A receber pipeline ou enviar pipeline ignora esta propriedade de conjunto de carateres quando efetuar o processamento de tempo de execução.|
|Esquema|Símbolo de selecionar (+) e selecione um esquema a partir da lista pendente. Para o esquema selecionado, selecione os separadores de definir para ser utilizado:</br></br>Separador de elemento componente – Enter um caráter para separar os elementos de dados composto.</br></br>Separador de elemento de dados – Enter um caráter para separar os elementos de dados simples dentro de elementos de dados composto.</br></br></br></br>Carácter de substituição – Selecione esta caixa de verificação se a carga útil dados contém carateres que também são utilizados como dados, segmento ou os separadores de componente. Em seguida, pode introduzir um caráter de substituição. Quando a gerar de saída X12 mensagem, todas as ocorrências dos carateres de separação no payload dados são substituídos pelo caráter especificado.</br></br>Segmento terminador – introduzir um caráter individual para indicar o fim de um segmento de editar.</br></br>Sufixo – selecione o caráter que é utilizado com o identificador de segmento. Se designar um sufixo, o elemento de dados do segmento terminador pode estar vazio. Se o terminador segmento for deixado em branco, tem de designar um sufixo.|

### <a name="validation"></a>Validação
|Propriedade|Descrição |
|----|----|
|Tipo de mensagem|Se selecionar esta opção permite validação no auscultador de intercâmbio. Esta validação executa a validação de editar em elementos de dados do conjunto da transação, extra separadores e validar tipos de dados, restrições de comprimento e elementos de dados vazia.|
|Editar validação||
|Validação expandida|Se selecionar esta opção permite validação adicional de intercâmbio recebido do remetente intercâmbio. Isto inclui a validação de comprimento do campo, opcionalidade e contagem de repetição para além de validação de tipo de dados XSD. Pode ativar a validação de extensão sem activar a validação de editar, ou vice versa.|
|Permitir que os zeros à esquerda / à direita|Se selecionar esta opção especifica que um intercâmbio de editar recebido a partir da parte não falhar validação se um elemento de dados de intercâmbio de editar não estar em conformidade com o requisito de comprimento devido a um ou os espaços à direita, mas se ajustarem à sua requisito de comprimento quando estes são removidos.|
|Separador decimal|Se selecionar esta opção especifica um intercâmbio de editar recebido a partir da parte não validação falhe se um elemento de dados de intercâmbio de editar não está em conformidade com a sua requisito de comprimento devido a zeros à esquerda (ou à direita) ou os espaços à direita, mas se ajustarem à sua requisito de comprimento quando estes são removidos.</br></br>Selecione não permitido se não pretender permitir que os delimitadores e separadores num intercâmbio recebido do remetente intercâmbio. Se o intercâmbio contiver delimitadores e separadores de à direita, for declarado inválidos.</br></br>Selecione opcional para aceitar intercâmbio com ou sem delimitadores e separadores de à direita.</br></br>Selecione obrigatório se o intercâmbio recebido tem de conter delimitadores e separadores de à direita.|

Após selecionar **OK** nas lâminas abertas:  
13. Selecione o mosaico **acordos** no pá a conta de integração e irá ver o contrato recentemente adicionado listado.  
![](./media/app-service-logic-enterprise-integration-x12/x12-7.png)   

## <a name="learn-more"></a>Saiba mais
- [Saber mais sobre o pacote de integração de empresa] (./app-service-logic-enterprise-integration-overview.md "Saiba mais sobre o pacote de integração de empresa")  
