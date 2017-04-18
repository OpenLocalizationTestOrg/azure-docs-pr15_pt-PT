<properties 
   pageTitle="Notas de lançamento do Azure SDK para .NET 2.5.1" 
   description="Notas de lançamento do Azure SDK para .NET 2.5.1" 
   services="app-service" 
   documentationCenter=".net,nodejs,java" 
   authors="Juliako" 
   manager="erikre" 
   editor=""/>

<tags
   ms.service="app-service"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="10/10/2016"
   ms.author="juliako"/>


# <a name="azure-sdk-for-net-251-release-notes"></a>Notas de lançamento do Azure SDK para .NET 2.5.1

Este documento contém as notas de lançamento para o SDK do Azure para .NET 2.5.1 liberte. 

##<a name="azure-sdk-for-net-251-release-notes"></a>Notas de lançamento do Azure SDK para .NET 2.5.1

Seguem-se as novas funcionalidades e atualizações no SDK Azure para .NET 2.5.1.

- Novo features\scenarios relacionados com **As extensões de ferramentas Web**. 

    - Sites públicos do Azure nome foram mudado a aplicação de serviço de Azure. Para obter mais informações, consulte [serviço de aplicação do Azure e dos serviços do Azure existente](app-service-changes-existing-services.md).
    - Foi adicionado Azure API aplicações (pré-visualização) suporte para que os clientes possam publicar ASP.NET projectos como API aplicações e, em seguida, utilize o adicionar > gesto de aplicação de cliente do Azure API no c# projectos para gerar código baseado na estrutura da aplicação API implementada. 
    - O nó de Web sites no Explorador do servidor foi preterido em vez do nó do Azure aplicação de serviço, que contém o suporte para o recurso de agrupamento de aplicações do Azure API, aplicações Mobile e aplicações Web baseadas em grupos.
    - Foi adicionado Azure suporte de aplicações do Mobile (pré-visualização) para que os clientes podem criar novos projetos aplicações Mobile, adicionar controladores de aplicações Mobile, publicar os projectos e remotamente depurar aplicações.
    - Adicionar > gesto de aplicação de cliente do Azure API suporta agora ficheiros Swagger JSON locais, os programadores Web API podem utilizar NuGets de terceiros, como Swashbuckle para gerar Swagger ou criá-lo manualmente. Desta forma, os programadores de cliente podem utilizar as funcionalidades de geração de código para consumir qualquer ponto final Swagger c# projetos. 
    - Foram melhoradas Web App e caixas de diálogo de publicação de aplicação de API para suportar o conceito de Azure Portal de agrupamento de recursos e seleção/criação de grupos de recursos do Azure e a aplicação de serviço planos são representados na nova Web App e API App aprovisionamento caixa de diálogo. 
    - Azure nós de API aplicação Server Explorer fornecem ligações à ligação abrangente API aplicações no Portal do Azure, bem como outras funcionalidades tal como a transmissão de registo e depuração remota.

    Para problemas conhecidos e limitações atuais Azure SDK .NET 2.5.1 [nesta](app-service-release-notes.md#known_issues_2_5_1) secção abaixo.


- Novo features\scenarios relacionados com a **HDInsight ferramentas** no Visual Studio estão ativados nesta versão. 
    - Validação local de scripts ramo. Clique no botão de script de validar na barra de ferramentas para ver se existem erros no seu script. 
    - Melhorado depuração de ramo de tarefas. Agora pode depurar ramo de tarefas ao aceder a registos de fio no Visual Studio. Se a sua aplicação tiver problemas de desempenho, a investigar FIO registos irá fornecer informações úteis...
    - (Público pré-visualização) Palavra-chave a conclusão automática e IntelliSense suportam para ramo. Para o ajudar a ramo de scripts do autor, HDInsight ferramentas para o Visual Studio adicionados suporte de palavra-chave a conclusão automática e o IntelliSense para ramo.
    - Suporte de tempestade. Agora pode utilizar ferramentas de HDInsight para Visual Studio para desenvolver tempestade topologias/Spouts/parafusos no c#. Em seguida, pode submeter a topologia desenvolvida para um cluster de tempestade e ver o estado de topologia/raio/vareta. Pode utilizar para resolver o seu tempestade parafusos/topologias/Spouts registos do sistema e os registos de cliente. Também pode utilizar activos JAVA existentes no tempestade no HDInsight.
    
    Para mais informações, consulte o artigo [começar a utilizar o HDInsight Hadoop ferramentas para o Visual Studio](hdinsight-hadoop-visual-studio-tools-get-started.md).



##<a id="known_issues_2_5_1"></a>Azure SDK para .NET 2.5.1 problemas e limitações conhecidos

- Azure API aplicações está visível como um alvo de implementação para aplicações Mobile. Web Apps deve ser o destino apenas para aplicações Mobile até uma edição subsequente. 
- Aprovisionamento de aplicação API Azure pode resultar em sucesso mas intermitentemente falhar atualizar o progresso na janela do Azure actividade de serviço de aplicação. Solução é verificar o estado da nova aplicação de API do Azure no Portal do Azure. 
- Ficheiro > novo projeto > API aplicação > experiência F5 resulta num erro HTTP porque não existe nenhuma default/index.html. Solução é manualmente navegue para o URL de /api/values. 
- Intermitentemente, ícones de Server Explorer aparecem planos. Reiniciar VS resolve este problema. 
- Se uma exceção é devolvida durante o aprovisionamento de aplicação Web ou a aplicação de API (como os erros de quota excedida ou nome do gateway Azure API aplicação duplicado), os erros mostram algum texto não processado do JSON. 
- Problemas de criação de projetos intermitentes quando informações de aplicação estiver selecionada na hora de criação de projeto.
- Por vezes, o código de aplicação de cliente do Azure API gerado falta espaços de nomes, precisam de estar incluído manualmente (ou automaticamente importados através de pistas Visual Studio) para o código compilar. 
- Projetos de aplicação Mobile devem ser publicados às aplicações web, mas tem de escolher um site criado como uma aplicação móvel no Portal do Azure uma vez que a aplicação Mobile projectos requerem uma base de dados. 
- A página de início para aplicações Mobile utiliza o termo "serviço móvel" em vez de "aplicações móveis" 
- Criação de projetos de aplicação Mobile poderá demorar um minuto para criar. 
- Durante a aplicação de API aprovisionamento (em alguns casos) um erro provêm de voltar a API do Azure refletir que as permissões não foi possível definir corretamente, enquanto a aplicação de API ter sido aprovisionada corretamente e está pronta para ser utilizado. Pode definir manualmente as permissões ao utilizar o Portal do Azure.
- Informações de aplicação não é suportada no modelos da aplicação de API e modelos de aplicação Mobile.
- Projetos de aplicação API não podem ser utilizados em conjunto com projetos de serviço na nuvem.
- Modelos de projecto de aplicação API só estão disponíveis no c#.
- Consumo de aplicação API através do menu de contexto "Adicionar Azure API aplicação cliente" só é suportado no c#.

 
