<properties
   pageTitle="Introdução ao Microsoft Power BI incorporado"
   description="Power BI incorporado, adicionar relatórios interativos do Power BI para a sua aplicação de business intelligence"
   services="power-bi-embedded"
   documentationCenter=""
   authors="guyinacube"
   manager="erikre"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="hero-article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="10/04/2016"
   ms.author="asaxton"/>

# <a name="get-started-with-microsoft-power-bi-embedded"></a>Introdução ao Microsoft Power BI incorporado

**Power BI incorporado** é um serviço Azure que permite que os programadores de aplicação adicionar relatórios interativos do Power BI para os seus próprios aplicações. **Power BI incorporado** funciona com aplicações existentes sem necessitar de redesenhar ou alterar os modo como os utilizadores iniciar sessão.

Recursos para o **Microsoft Power BI incorporado** configurações através de do [Azure processador APIs](https://msdn.microsoft.com/library/mt712306.aspx). Neste caso, o recurso que aprovisionar o que é uma **Coleção de área de trabalho do Power BI**.

![](media\power-bi-embedded-get-started\introduction.png)

## <a name="create-a-workspace-collection"></a>Criar uma coleção de área de trabalho
Uma **Coleção de área de trabalho** é o recurso Azure nível superior e um contentor para o conteúdo que será incorporado na sua aplicação. Uma **Coleção de área de trabalho** podem ser criados de duas maneiras:

   -    Manualmente utilizando o Portal do Azure
   -    Utilizar através de programação APIs Manager(ARM) de recursos do Azure

Vamos percorrer os passos para criar uma **Coleção de área de trabalho** utilizando o Portal do Azure.

   1.   Abra e iniciar sessão no **Portal do Azure**: [http://portal.azure.com](http://portal.azure.com).

   2.   Clique em **+ Novo** no painel superior.

       ![](media\power-bi-embedded-get-started\create-workspace-1.png)

   3.   Em **dados + Analytics** clique em **Incorporado do Power BI**.
   4.   Em **Criação pá**, introduza as informações necessárias. Para **preços**, consulte o artigo [preços incorporado do Power BI](http://go.microsoft.com/fwlink/?LinkID=760527).

       ![](media\power-bi-embedded-get-started\create-workspace-2.png)

   5. Clique em **Criar**.

A **Coleção de área de trabalho** irá demorar alguns minutos para aprovisionar. Quando concluído, que vai ser feito para a **Área de trabalho recolha pá**.

   ![](media\power-bi-embedded-get-started\create-workspace-3.png)

A **Criação de pá** contém as informações necessárias ligar as APIs que criar áreas de trabalho e implementar conteúdo aos mesmos.

<a name="view-access-keys"/>
## <a name="view-power-bi-api-access-keys"></a>Teclas de acesso à API do Vista Power BI

Uma das partes mais importantes de informações necessárias para ligar para o Power BI REST APIs são as **Teclas de acesso**. Estas são utilizadas para gerar **tokens de aplicação** que são utilizadas para autenticar pedidos de API. Para ver as **Teclas de acesso**, clique em **Teclas de acesso** no **Pá definições**. Para mais informações sobre **tokens de aplicação**, consulte o artigo [Authenticating e autorizar com o Power BI incorporado](power-bi-embedded-app-token-flow.md).

   ![](media\power-bi-embedded-get-started\access-keys.png)

Verá a ' aviso de que tem duas chaves.

   ![](media\power-bi-embedded-get-started\access-keys-2.png)

Copie estas teclas e armazená-las de forma segura na sua aplicação. É muito importante que são processados estas teclas tal como faria com uma palavra-passe, uma vez que estes irá fornecer acesso a todo o conteúdo na sua **Coleção de área de trabalho**.

Enquanto duas chaves estiverem listadas, é necessária, apenas uma chave num momento específico. Na segunda chave é fornecida para periodicamente pode gerar chaves sem interromper o acesso ao serviço.

Agora que tem uma instância do Power BI para a sua aplicação e **As teclas de acesso**, pode importar um relatório para a sua própria aplicação. Antes de saber como importar um relatório, a secção seguinte descreve criar conjuntos de dados do Power BI e relatórios para incorporar uma aplicação.

## <a name="create-power-bi-datasets-and-reports-to-embed-into-an-app"></a>Criar conjuntos de dados do Power BI e relatórios para incorporar uma aplicação

Agora que criou uma instância do Power BI para a sua aplicação e ter **As teclas de acesso**, terá de criar os relatórios que pretende incorporar e conjuntos de dados do Power BI. Conjuntos de dados e relatórios podem ser criados utilizando o **Power BI Desktop**. Pode transferir o [Power BI Desktop para livre](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/). Em alternativa, para começar a rapidamente, pode transferir o [revenda análise exemplo PBIX] (http://go.microsoft.com/fwlink/?LinkID=780547). Para saber mais sobre como utilizar o **Power BI Desktop**, consulte o artigo [Introdução ao Power BI Desktop](https://powerbi.microsoft.com/en-us/guided-learning/powerbi-learning-0-2-get-started-power-bi-desktop).

Com o **Power BI Desktop**, ligue à sua origem de dados ao importar uma cópia dos dados para o **Power BI Desktop** e ligar diretamente a origem de dados utilizando **DirectQuery**.

Estas são as diferenças entre utilizar de **importação** e **DirectQuery**.

|Importar | DirectQuery
|---|---
|As tabelas, colunas *e os dados* são importadas ou copiadas para **o ambiente de trabalho do Power BI**. À medida que trabalha com visualizações, o **Power BI Desktop** consultas uma cópia dos dados. Para ver as alterações que ocorreram os dados subjacentes, tem de atualizar ou importar um concluída, atual conjunto de dados novamente.|Apenas a *tabelas e colunas* são importados ou copiado para **o ambiente de trabalho do Power BI**. À medida que trabalha com visualizações, o **Power BI Desktop** consulta da origem de dados subjacentes, o que significa que sempre que está a ver dados atuais.

Para obter mais informações sobre a ligação a uma origem de dados, consulte o artigo [ligar a uma origem de dados](power-bi-embedded-connect-datasource.md).

Depois de guardar o seu trabalho no **Ambiente de trabalho do Power BI**, é criado um ficheiro PBIX. Este ficheiro contém o relatório. Além disso, se importar dados a PBIX contém o conjunto de dados completo, ou se utilizar **DirectQuery**, o PBIX contém apenas um esquema de conjunto de dados. Implementar a PBIX para a área de trabalho utilizando a [API de importação do Power BI](https://msdn.microsoft.com/library/mt711504.aspx)através de programação.

> [AZURE.NOTE] **Power BI incorporado** tem APIs adicionais para alterar o servidor e a base de dados que está a apontar para o conjunto de dados e definir credenciais de conta de serviço que irá utilizar o conjunto de dados para ligar à sua base de dados. Consulte o artigo [mensagem SetAllConnections](https://msdn.microsoft.com/library/mt711505.aspx) e [origem de dados de patches Gateway](https://msdn.microsoft.com/library/mt711498.aspx).

## <a name="next-steps"></a>Próximos passos
Nos passos anteriores, criou uma coleção de área de trabalho e o primeiro relatório e conjunto de dados. Está na altura para saber como escrever código **Incorporado do Power BI**para. Para ajudar a começar a utilizar, criámos uma aplicação web do exemplo: [introdução da amostra](power-bi-embedded-get-started-sample.md). O exemplo mostra-lhe como para:

  - Aprovisionar conteúdo
      - Criar uma área de trabalho
      - Importar um ficheiro PBIX
      - Atualize as cadeias de ligação e definir credenciais para a sua conjuntos de dados.

  - Em segurança incorporar um relatório

## <a name="see-also"></a>Consulte também
- [Introdução ao exemplo](power-bi-embedded-get-started-sample.md)
- [Autenticação e autorização com o Power BI incorporado](power-bi-embedded-app-token-flow.md)
- [Ambiente de trabalho do Power BI](https://powerbi.microsoft.com/documentation/powerbi-desktop-get-the-desktop/)
