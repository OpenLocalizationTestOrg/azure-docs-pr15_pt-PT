<properties 
    pageTitle="Integração da empresa com EDIFACT | Microsoft Azure" 
    description="Saiba como utilizar os acordos do EDIFACT para criar aplicações de lógica" 
    services="logic-apps" 
    documentationCenter=".net,nodejs,java"
    authors="jeffhollan" 
    manager="erikre" 
    editor="cgronlun"/>

<tags 
    ms.service="app-service-logic" 
    ms.workload="integration" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="07/26/2016" 
    ms.author="jonfan"/>

# <a name="enterprise-integration-with-edifact"></a>Integração da empresa com EDIFACT 

> [AZURE.NOTE] Esta página abrange as funcionalidades EDIFACT de lógica de aplicações. Para obter informações sobre X12 clique [aqui](app-service-logic-enterprise-integration-x12.md).

## <a name="create-an-edifact-agreement"></a>Criar um contrato EDIFACT 
Antes de poderem trocar mensagens EDIFACT, tem de criar um contrato EDIFACT e armazená-lo na sua conta de integração. Os passos seguintes irão guiá-lo durante o processo de criação de um contrato EDIFACT.

### <a name="heres-what-you-need-before-you-get-started"></a>Eis o que precisa antes de começar
- Uma [conta de integração](./app-service-logic-enterprise-integration-accounts.md) definida na sua subscrição do Azure  
- Pelo menos mais duas [parceiros](./app-service-logic-enterprise-integration-partners.md) já definidos na sua conta de integração  

>[AZURE.NOTE]Ao criar um contrato, o conteúdo nas mensagens que irão que receber/enviar para e do parceiro tem de corresponder o tipo de acordo.    


Depois de ter [criado uma conta de integração](./app-service-logic-enterprise-integration-accounts.md) e [adicionado parceiros](./app-service-logic-enterprise-integration-partners.md), pode criar um contrato EDIFACT seguindo estes passos:  

### <a name="from-the-azure-portal-home-page"></a>Na home page de portal Azure

Depois de registo para o [Azure portal](http://portal.azure.com "Azure portal"):  
1. Selecione **Procurar** a partir do menu à esquerda.  

>[AZURE.TIP]Se não vir a ligação **Procurar** , poderá ter de expandir o menu pela primeira vez. Faça o seguinte ao selecionar a ligação **Mostrar o menu de** que está localizada na parte superior esquerda do menu fechado.  

![](./media/app-service-logic-enterprise-integration-edifact/EDIFACT-0.png)    
2. Escreva *integração* na caixa de pesquisa de filtro, em seguida, selecione **Contas de integração** a partir da lista de resultados.       
![](./media/app-service-logic-enterprise-integration-edifact/EDIFACT-1-3.png)    
3. No pá de **Contas de integração** aberto, selecione a conta de integração no qual irá criar o contrato. Se não vir qualquer integração contas listas, [criar um primeiro](./app-service-logic-enterprise-integration-accounts.md "All about integration accounts").  
![](./media/app-service-logic-enterprise-integration-edifact/EDIFACT-1-4.png)  
4.  Selecione o mosaico **acordos** . Se não vir os acordos dispor em mosaico, adicioná-la pela primeira vez.   
![](./media/app-service-logic-enterprise-integration-edifact/EDIFACT-1-5.png)     
5. Selecione o botão **Adicionar** na pá acordos que é aberta.  
![](./media/app-service-logic-enterprise-integration-edifact/EDIFACT-agreement-2.png)  
6. Introduza um **nome** para o contrato, em seguida, selecione o **tipo de acordo** para EDIFACT, **Parceiro de anfitrião**, **Identidade de anfitrião**, **Parceiro de convidado**, **Identidade de convidado**, na pá acordos que é aberta.  
![](./media/app-service-logic-enterprise-integration-edifact/EDIFACT-1.png)  
7. Depois de definir as propriedades de contrato, selecione **Definições de recepção** para configurar como mensagens recebidas através deste contrato estão a ser processada.  
8. O controlo de definições de recepção estiver dividido em secções seguintes, incluindo identificadores, confirmação, esquemas, números de controlo, validação, definições interno e processamento Batch. Configure estas propriedades com base no seu contrato com o parceiro que irá ser trocar mensagens com. Eis uma vista destes controlos, configurá-los com base em como pretende que o presente contrato para identificar e processar as mensagens a receber:  
![](./media/app-service-logic-enterprise-integration-edifact/EDIFACT-2.png)  
9. Selecione o botão **OK** para guardar as definições.  

### <a name="identifiers"></a>Identificadores

|Propriedade|Descrição |
|---|---|
|UNB6.1 (referência destinatários palavra-passe)|Introduza um valor alfanumérico que vão entre 1 e 14 caracteres.|
|UNB6.2 (referência destinatários qualificador)|Introduza um valor com um mínimo de um caráter e um máximo de dois carateres alfanumérico.|

### <a name="acknowledgments"></a>Confirmações 

|Propriedade|Descrição |
|----|----|
|Recibo de mensagem (CONTRL)|Selecione esta caixa de verificação para devolver uma confirmação (CONTRL) técnica ao remetente intercâmbio. O SACK é enviada para o remetente de intercâmbio com base nas definições de enviar para o contrato.|
|Confirmação (CONTRL)|Selecione esta caixa de verificação para devolver uma confirmação (CONTRL) funcional para o remetente de intercâmbio a confirmação é enviada para o remetente de intercâmbio com base nas definições de enviar para o contrato.|

### <a name="schemas"></a>Esquemas

|Propriedade|Descrição |
|----|----|
|UNH2.1 (TIPO)|Selecione um tipo de conjunto da transação.|
|UNH2.2 (VERSÃO)|Introduza o número de versão da mensagem. ((Mínimo), um caráter; máximo, três carateres).|
|UNH2.3 (LANÇAMENTO)|Introduza o número de lançamento de mensagem. ((Mínimo), um caráter; máximo, três carateres).|
|UNH2.5 (CÓDIGO ASSOCIADO ATRIBUÍDO)|Introduza o código atribuído. (Máximo, seis carateres. Tem de ser alfanuméricos).|
|UNG2.1 (APLICAÇÃO REMETENTE ID)|Introduza um valor com um mínimo de um caráter e um máximo de 35 caracteres alfanumérico.|
|UNG2.2 (APLICAÇÃO REMETENTE CÓDIGO QUALIFICADOR)|Introduza um valor alfanumérico, com um máximo de quatro carateres.|
|ESQUEMA|Selecione o esquema anteriormente carregado que pretende utilizar da sua conta de integração associado.|

### <a name="control-numbers"></a>Números de controlo

|Propriedade|Descrição |
|----|----|
|Não permitir o número de controlo Interchange duplicados|Selecione esta caixa de verificação para bloquear intercâmbio duplicados. Se estiver selecionada, a ação de codificar EDIFACT verifica que o número de controlo interchange (UNB5) para o intercâmbio recebido não corresponder a um número de controlo de intercâmbio processadas anteriormente. Se uma correspondência for detectada, então o o intercâmbio não está a ser processado.
|Verificar a existência de duplicados UNB5 cada (dias)|Caso tenha optado por não permitir números de controlo de intercâmbio de duplicados, pode especificar o número de dias em que a verificação é executada atribuindo o valor adequado para a opção **verificar a existência de duplicados UNB5 cada (dias)** .|
|Não permitir duplicados de números de controlo do grupo|Selecione esta caixa de verificação para bloquear intercâmbio com números de controlo do grupo duplicados (UNG5).|
|Não permitir da transação conjunto controlo números duplicados|Selecione esta caixa de verificação para bloquear intercâmbio com números de controlo da transação duplicados conjunto (UNH1).|
|Número de controlo de confirmação EDIFACT|Para designar os números das transações conjunto referência a ser utilizado numa confirmação, introduza um valor para o prefixo, um intervalo de números de referência e um sufixo.|

### <a name="validations"></a>Validações

|Propriedade|Descrição |
|----|----|
|Tipo de mensagem|Especifique o tipo de mensagem. Como é concluída cada linha de validação, outro serão automaticamente adicionado. Se forem especificadas sem regras, em seguida, a linha marcada como predefinido é utilizado para validação.|
|Editar validação|Selecione esta caixa de verificação para executar a validação de editar em tipos de dados, tal como foi definido pelas propriedades de editar do esquema, restrições de comprimento, elementos de dados vazia e separadores de à direita.|
|Validação expandida|Selecione esta caixa de verificação para ativar validação (XSD) expandida de intercâmbio recebido do remetente intercâmbio. Isto inclui a validação de comprimento do campo, opcionalidade e contagem de repetição para além de validação de tipo de dados XSD.|
|Permitir que os zeros finais/espaços à esquerda|Selecione **Permitir** para permitir que o líder/zeros à direita; Zeros **NotAllowed** para não permitir líder/à direita zeros **Cortar** cortar os espaços à esquerda e à direita.|
|Cortar os zeros finais/espaços à esquerda|Selecione esta caixa de verificação para cortar qualquer zeros à esquerda ou à direita|
|Política de separação à direita|Selecione **Não permitido** se não pretender permitir que os delimitadores e separadores num intercâmbio recebido do remetente intercâmbio. Se o intercâmbio contiver delimitadores e separadores de à direita, for declarado inválidos. Selecione **opcional** para aceitar intercâmbio com ou sem delimitadores e separadores de à direita. Selecione **obrigatório** se o intercâmbio recebido tem de conter delimitadores e separadores de à direita.|

### <a name="internal-settings"></a>Definições internas

|Propriedade|Descrição |
|----|----|
|Criar etiquetas XML vazias se os separadores são permitidos|Selecione esta caixa de verificação para que o remetente de intercâmbio incluir vazias etiquetas XML para os separadores de à direita.|
|Processamento de lotes de entrada|Opções de incluem:</br></br>**Dividir intercâmbio como da transação conjuntos - suspender da transação conjuntos erros**: analisa cada transação definir num intercâmbio num documento XML separado ao aplicar o envelope adequado para o conjunto da transação. Com esta opção, se um ou mais conjuntos da transação no intercâmbio falham de validação, em seguida, apenas esses conjuntos da transação são suspensa. Intercâmbio dividido como da transação conjuntos - suspender intercâmbio no erro: analisa cada transação definir num intercâmbio num documento XML separado ao aplicar o envelope adequado. Com esta opção, se uma ou mais transação conjuntos no intercâmbio validação falhe, em seguida, o intercâmbio inteiro será suspensa.</br></br>**Preservar intercâmbio - suspender da transação conjuntos erros**: deixa o intercâmbio intactos, criar um documento XML para o intercâmbio por lotes inteiro. Com esta opção, se um ou mais conjuntos da transação no intercâmbio falham de validação, em seguida, apenas esses conjuntos da transação são suspensa, enquanto todos os conjuntos da transação são processados.</br></br>**Preservar intercâmbio - suspender intercâmbio erros**: deixa o intercâmbio intactos, criar um documento XML para o intercâmbio por lotes inteiro. Com esta opção, se uma ou mais transação conjuntos no intercâmbio validação falhe, em seguida, o intercâmbio inteiro está suspensa.|

O contrato está pronto para processar mensagens a receber que está em conformidade com as definições que selecionou.

Para configurar as definições que lidar com mensagens enviadas para parceiros:  
10. Selecione **Definições de envio** para configurar a forma como as mensagens enviadas através deste contrato não para ser resolvido.  

O controlo de definições de envio estiver dividido em secções seguintes, incluindo identificadores, confirmação, esquemas, Envelopes, conjuntos de caracteres e separadores, números de controlo e validação. 

Eis uma vista destes controlos. Efetue as seleções com base em como pretende processar as mensagens enviadas para parceiros através deste contrato:   
![](./media/app-service-logic-enterprise-integration-edifact/EDIFACT-3.png)    
11. Selecione o botão **OK** para guardar as definições.  

### <a name="identifiers"></a>Identificadores
|Propriedade|Descrição |
|----|----|
|UNB1.2 (versão de sintaxe)|Selecione um valor entre **1** e **4**.|
|UNB2.3 (remetente inversa encaminhamento endereço)|Introduza um valor com um mínimo de um caráter e um máximo de 14 caracteres alfanumérico.|
|UNB3.3 (endereço encaminhamento inversa destinatários)|Introduza um valor com um mínimo de um caráter e um máximo de 14 caracteres alfanumérico.|
|UNB6.1 (referência destinatários palavra-passe)|Introduza um valor com um mínimo de um e um máximo de 14 caracteres alfanumérico.|
|UNB6.2 (referência destinatários qualificador)|Introduza um valor com um mínimo de um caráter e um máximo de dois carateres alfanumérico.|
|UNB7 (ID da aplicação de referência)|Introduzir um valor com um mínimo de um caráter e um máximo de 14 caracteres alfanumérico|

### <a name="acknowledgment"></a>Confirmação
|Propriedade|Descrição |
|----|----|
|Recibo de mensagem (CONTRL)|Selecione esta caixa de verificação se o parceiro alojado espera de receber para receber uma confirmação (CONTRL) técnica. Esta definição especifica que o parceiro alojado, quem está a enviar a mensagem, os pedidos de uma confirmação do parceiro de convidado.|
|Confirmação (CONTRL)|Selecione esta caixa de verificação se o parceiro alojado espera receber uma confirmação (CONTRL) funcional. Esta definição especifica que o parceiro alojado, quem está a enviar a mensagem, os pedidos de uma confirmação do parceiro de convidado.|
|Gerar SG1/SG4 ciclo para conjuntos de aceite da transação|Se tiver optado por pedir uma confirmação funcional, selecione esta caixa de verificação Forçar geração de SG1/SG4 ciclos no funcionais confirmações CONTRL para conjuntos de transação aceite.|

### <a name="schemas"></a>Esquemas
|Propriedade|Descrição |
|----|----|
|UNH2.1 (TIPO)|Selecione um tipo de conjunto da transação.|
|UNH2.2 (VERSÃO)|Introduza o número de versão da mensagem.|
|UNH2.3 (LANÇAMENTO)|Introduza o número de lançamento de mensagem.|
|ESQUEMA|Selecione o esquema para utilizar. Esquemas encontram-se na sua conta de integração. Para aceder ao seu esquemas, ligação sua conta de integração para a sua aplicação de lógica.|

### <a name="envelopes"></a>Envelopes
|Propriedade|Descrição |
|----|----|
|UNB8 (código de prioridade de processamento)|Introduza um valor por ordem alfabética que não seja mais de um caráter longo.|
|UNB10 (comunicação acordo)|Introduza um valor com um mínimo de um caráter e um máximo de 40 caracteres alfanumérico.|
|UNB11 (teste indicador)|Selecione esta caixa de verificação para indicar que o intercâmbio gerado é dados de teste|
|Aplicar UNA segmento (serviço cadeia conselhos)|Selecione esta caixa de verificação para gerar um segmento UNA para o intercâmbio sejam enviadas.|
|Aplicar UNG segmentos (cabeçalho do grupo de funções)|Selecione esta caixa de verificação para criar segmentos de agrupamento no cabeçalho do grupo de funcionais nas mensagens enviadas para o parceiro de convidado. Os seguintes valores são utilizados para criar os segmentos UNG:</br></br>Para **UNG1**, introduza um valor com um mínimo de um caráter e um máximo de seis carateres alfanumérico.</br></br>Para **UNG2.1**, introduza um valor com um mínimo de um caráter e um máximo de 35 caracteres alfanumérico.</br></br>Para **UNG2.2**, introduza um valor alfanumérico, com um máximo de quatro carateres.</br></br>Para **UNG3.1**, introduza um valor com um mínimo de um caráter e um máximo de 35 caracteres alfanumérico.</br></br>Para **UNG3.2**, introduza um valor alfanumérico, com um máximo de quatro carateres.</br></br>Para **UNG6**, introduza um valor com um mínimo de um e um máximo de três carateres alfanumérico.</br></br>Para **UNG7.1**, introduza um valor com um mínimo de um caráter e um máximo de três carateres alfanumérico.</br></br>Para **UNG7.2**, introduza um valor com um mínimo de um caráter e um máximo de três carateres alfanumérico.</br></br>Para **UNG7.3**, introduza um valor com um mínimo de 1 caráter e um máximo de 6 caracteres alfanumérico.</br></br>Para **UNG8**, introduza um valor com um mínimo de um caráter e um máximo de 14 caracteres alfanumérico.|

### <a name="character-sets-and-separators"></a>Conjuntos de carateres e separadores
Outros que o conjunto de caracteres, pode introduzir um conjunto diferente de delimitadores para ser utilizado para cada tipo de mensagem. Se não for especificado um conjunto de carateres para esquema de uma determinada mensagem, em seguida, é utilizado o conjunto de carateres predefinido.

|Propriedade|Descrição |
|----|----|
|UNB1.1 (identificador de sistema)|Selecione o caráter EDIFACT definido para serem aplicadas no intercâmbio de saída.|
|Esquema|Selecione um esquema a partir da lista pendente. Como cada linha é concluída é adicionada uma nova linha. Para o esquema selecionado, selecione os separadores de definir para ser utilizado:</br></br>**Separador de elemento componente** – Enter um caráter para separar os elementos de dados composto.</br></br>**Separador de elemento de dados** – Enter um caráter para separar os elementos de dados simples dentro de elementos de dados composto.</br></br></br></br>**Carácter de substituição** – Selecione esta caixa de verificação se a carga útil dados contém carateres que também são utilizados como dados, segmento ou os separadores de componente. Em seguida, pode introduzir um caráter de substituição. Quando a mensagem EDIFACT saída a gerar, todas as ocorrências dos carateres de separação dos dados de carga útil são substituídas com o caráter especificado.</br></br>**Segmento terminador** – Enter um caráter individual para indicar o fim de um segmento de editar.</br></br>**Sufixo** – selecione o caráter que é utilizado com o identificador de segmento. Se designar um sufixo, o elemento de dados do segmento terminador pode estar vazio. Se o terminador segmento for deixado em branco, tem de designar um sufixo.|

### <a name="control-numbers"></a>Números de controlo
|Propriedade|Descrição |
|----|----|
|UNB5 (número de controlo Interchange Format)|Introduza um prefixo, um intervalo de valores para o número de controlo de intercâmbio e um sufixo. Estes valores são utilizadas para gerar um intercâmbio de saída. O prefixo e sufixo são opcionais; é necessário o número de controlo. O número do controlo é incrementado para cada mensagem nova; o prefixo e sufixo permanecem inalteradas.|
|UNG5 (número de controlo de grupo)|Introduza um prefixo, um intervalo de valores para o número de controlo de intercâmbio e um sufixo. Estes valores são utilizadas para gerar o número de controlo do grupo. O prefixo e sufixo são opcionais; é necessário o número de controlo. O número do controlo é incrementado para cada mensagem nova, até que seja atingido o valor máximo; o prefixo e sufixo permanecem inalteradas.|
|UNH1 (número de referência do cabeçalho da mensagem)|Introduza um prefixo, um intervalo de valores para o número de controlo de intercâmbio e um sufixo. Estes valores são utilizadas para gerar o número de referência de cabeçalho da mensagem. O prefixo e sufixo são opcionais; o número de referência é necessário. O número de referência é incrementado para cada mensagem nova; o prefixo e sufixo permanecem inalteradas.|

### <a name="validations"></a>Validações
|Propriedade|Descrição |
|----|----|
|Tipo de mensagem|Se selecionar esta opção permite validação no auscultador de intercâmbio. Esta validação executa a validação de editar em elementos de dados do conjunto da transação, validar tipos de dados, restrições de comprimento e elementos de dados vazia e separadores de formação.|
|Editar validação|Selecione esta caixa de verificação para executar a validação de editar em tipos de dados, tal como foi definido pelas propriedades de editar do esquema, restrições de comprimento, elementos de dados vazia e separadores de à direita.|
|Validação expandida|Se selecionar esta opção permite validação adicional de intercâmbio recebido do remetente intercâmbio. Isto inclui a validação de comprimento do campo, opcionalidade e contagem de repetição para além de validação de tipo de dados XSD. Pode ativar a validação de extensão sem activar a validação de editar, ou vice versa.|
|Permitir que o líder/à direita zeros|Se selecionar esta opção especifica que um intercâmbio de editar recebido a partir da parte não falhar validação se um elemento de dados de intercâmbio de editar não estar em conformidade com o requisito de comprimento devido a um ou os espaços à direita, mas se ajustarem à sua requisito de comprimento quando estes são removidos.|
|Cortar os zeros finais/espaços à esquerda|Se selecionar esta opção, irá cortar os zeros à esquerda e à direita.|
|Separador decimal|Se selecionar esta opção especifica um intercâmbio de editar recebido a partir da parte não validação falhe se um elemento de dados de intercâmbio de editar não está em conformidade com a sua requisito de comprimento devido a zeros à esquerda (ou à direita) ou os espaços à direita, mas se ajustarem à sua requisito de comprimento quando estes são removidos.</br></br>Selecione **Não permitido** se não pretender permitir que os delimitadores e separadores num intercâmbio recebido do remetente intercâmbio. Se o intercâmbio contiver delimitadores e separadores de à direita, for declarado inválidos.</br></br>Selecione **opcional** para aceitar intercâmbio com ou sem delimitadores e separadores de à direita.</br></br>Selecione **obrigatório** se o intercâmbio recebido tem de conter delimitadores e separadores de à direita.|

Após selecionar **OK** na pá aberta:  
12. Selecione o mosaico **acordos** no pá a conta de integração e irá ver o contrato recentemente adicionado listado.  
![](./media/app-service-logic-enterprise-integration-edifact/EDIFACT-4.png)   

## <a name="learn-more"></a>Saiba mais
- [Saber mais sobre o pacote de integração de empresa] (./app-service-logic-enterprise-integration-overview.md "Saiba mais sobre o pacote de integração de empresa")  
