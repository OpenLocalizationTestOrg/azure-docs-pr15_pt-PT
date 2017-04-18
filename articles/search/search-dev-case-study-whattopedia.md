<properties 
    pageTitle="Azure pesquisa programador caso prático da Microsoft: Como WhatToPedia criado um portal de infomedia no Microsoft Azure | Microsoft Azure | Serviço de pesquisa alojado na nuvem" 
    description="Saiba como criar um informações portal e meta motor de busca utilizando a pesquisa do Azure, um serviço de pesquisa na nuvem alojado para programadores." 
    services="search, sql-database,  storage, web-sites" 
    documentationCenter="" 
    authors="HeidiSteen" 
    manager="jhubbard"/>

<tags 
    ms.service="search" 
    ms.devlang="NA" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="search" 
    ms.date="08/29/2016" 
    ms.author="heidist"/>

# <a name="azure-search-developer-case-study"></a>Pesquisa Azure programador caso prático da Microsoft

## <a name="how-whattopediacomhttpwhattopediacom-built-an-infomedia-portal-on-microsoft-azure"></a>Como [WhatToPedia.com](http://whattopedia.com/) compilado um portal de infomedia no Microsoft Azure

 ![][6]  &nbsp;&nbsp;&nbsp;  <font size="9">A ideia</font> 


Os nossos ideia é criar um portal de informações que o ajuda a compradores ligar com revendedores através de uma experiência muito relevantes, pesquisa confinados, semelhante a como viajar portais CORRESP turistas o com o alojamento, restaurantes e entretenimento quando está no uncharted território. 

O portal que podemos pretende obter irá fornecer uma experiência de pesquisa excepcionalmente alta qualidade dados revendedor num determinado mercado, ajudar compradores localizar armazena com base na localização e outras amenidades o revendedor fornece. Vamos irá propagar motor de busca com um conjunto de dados inicial, mas o valor mais aprofundada será construída ao longo do tempo, com a ajuda de subscritores do revendedor que regista informações sobre o seu negócio. Promoções, mercadorias nova, marcas mais populares, serviços de especialidade sessões-– todos alguns exemplos de dados que adiciona um valor ao nosso site. Estes dados são personalizada comunicados e integrados no corpo de pesquisa, assim que o revendedor se inscreve como um subscritor. Publicidade, juntamente com o modelo de subscrição, forneça a sequência de receitas para o nosso novo negócio.

Procura será o modelo de interação predominante do utilizador, numa plataforma puro na nuvem. Para fins de escala e custos de baixa, quase tudo o que recomendamos, a partir da experiência de portal para controlo de origem, será através de um serviço online. Utilizar o motor de busca que fornece as funcionalidades que precisamos fora da caixa de, podemos criar uma aplicação de pesquisa rapidamente, sem ter de criar e gerir uma pesquisa do motor de estabelecidos.

## <a name="what-we-built"></a>O que criámos

WhatToPedia é uma empresa de infomedia de arranque. Criámos [WhatToPedia.com](http://whattopedia.com/) – - atualmente no teste de mercado na Europa Norte com uma data de ativação de 2 de Fevereiro de 2015. Os nossos base cliente é principalmente os centros de fixa e tradicionais que precisam de uma presença online acessível que seja fácil de gerir e manter.

Os nossos tarefa é apelar compradores através de uma experiência de pesquisa online excelente, aumentar resultados com base na cidade ou vizinhança, marcas, armazenar nomes ou armazenar tipos. Chamar compradores tem um efeito de ondulação, revendedores principal para subscrever o nosso site de portal. As subscrições estão pagas, com uma taxa acessível.

 ![][7] 

Após a inscrição para uma subscrição, num revendedor leva-o até ao longo do respetivo perfil existente (criado inicialmente-na partir de dados comprados), atualizá-lo com dados adicionais sobre promoções, marcas em destaque ou anúncios. Capacidades de sessões, tais como os idiomas falados, moedas aceitaram, compras isentas de imposto, pode ser personalizada denunciado para melhor apelar compradores quem estão à procura esses amenidades.

## <a name="who-we-are"></a>Quem estamos

O meu nome estiver Segato Ferreira (Microsoft Consulting) e posso trabalhou com Julião Boelling, conduzir programador na WhatToPedia, para estruturar a solução. 

WhatToPedia é um arranque, teste suas empresas nova portal na Suécia, onde a maioria das 60.000 revendedores são PME fixa e tradicionais (pequenas e médias empresas) de marketing. Uma vez que recomendamos souber que uma pessoa compras na Europa speaks vários idiomas e executa várias moedas, podemos criar soluções que acomodar uma forma multilingue. Estamos conforme necessário e a que se encontram, motor de busca que suporta o nossos requisitos multilingue na pesquisa Azure.

Pesquisa Azure era um conversor de jogo para os nossos projeto. Antes da disponibilidade de pesquisa do Azure, podemos gasto energia considerável trabalhar através de pessoal construir nossa própria motor de busca. Ter Azure pesquisa como um serviço online removido o poderá técnico e administrativo maior de nossa solução, que se destinam introdução no mercado mais depressa e com uma experiência de pesquisa mais eficaz.  

## <a name="how-we-did-it"></a>Como fizemos

A nossa visão foi criar uma infraestrutura completa com base em serviços em nuvem apenas. Microsoft foi escolhido como a plataforma estratégica porque era o fornecedor que oferecidas ao necessário dimensionar serviços (para colaboração tanto desenvolvimento), no pedido e preços acessível.
 
### <a name="high-level-components"></a>Componentes de alto nível

Criámos um negócio, não apenas um site. Suporte do esforço inteiro necessária uma gama completa de ferramentas e aplicações. Vamos aprovadas Visual Studio e do Visual Studio Team Services para o desenvolvimento, Team Foundation serviço (TFS) Online para o controlo de origem e gestão de scrum, Office 365 para comunicação e colaboração e obviamente Microsoft Azure para todas as operações relacionadas com o site e armazenamento. Com o Visual Studio, IDE fornecido direta do aprovisionamento Azure, com a integração TFS fornecer uma intensidade adicionais produtividade online.

O diagrama abaixo ilustra os componentes de alto nível utilizados no infraestrutura do WhatToPedia.

   ![][8]

### <a name="how-we-use-microsoft-azure"></a>Como utilizamos Microsoft Azure

Consultar as caixas verdes no diagrama anterior, verá que a solução WhatToPedia está incorporada sobre estes serviços:

- [Pesquisa Azure](https://azure.microsoft.com/services/search/)
- [Azure Web sites utilizando o MVC 4](https://azure.microsoft.com/services/websites/)
- [Azure WebJobs para tarefas agendadas](../app-service-web/websites-webjobs-resources.md)
- [Base de dados Azure SQL](https://azure.microsoft.com/services/sql-database/)
- [Armazenamento de BLOBS do Azure](https://azure.microsoft.com/services/storage/)
- [Entrega de E-Mail SendGrid](https://azure.microsoft.com/marketplace/partners/sendgrid/sendgrid-azure/)

O coração muito da solução é dados e de pesquisa. O fluxo de dados a partir do fornecedor do revendedor para o cliente de fim é ilustrado abaixo:

  ![][9]

Armazenamento de dados principal é o revendedor e contabilidade e dados de base de dados do SQL Azure. Isto é composta pelo conjunto de dados inicial, assim como dados específicos do revendedor adicionados ao longo do tempo. Estamos a utilizar um WebJob Azure publicar atualizações a partir da base de dados SQL o corpo de pesquisa na pesquisa Azure.

### <a name="presentation-layer"></a>Camadas de apresentação

O portal é um site do Azure, implementada no MVC 4 e [Twitter arranque](http://en.wikipedia.org/wiki/Bootstrap_%28front-end_framework%29). Optamos por MVC, pois oferece uma abordagem muito mais limpeza para HTML que desenvolvimento baseada em formulários do ASP.NET. Para evitar ter de criar aplicações para vários dispositivos e manter múltiplas plataformas de dispositivos móveis, foi escolhido Arranque Twitter para todos os dispositivos e plataformas de suporte.

### <a name="authentication-permissions-and-sensitive-data"></a>Autenticação, permissões e dados confidenciais

Compradores navegar no site de forma anónima. Como tal, não existem requisitos início de sessão para compradores, nem fazer podemos armazenar quaisquer dados consumidor. 

Revendedores são uma história diferente. Aqui, podemos armazenam informações de perfil público, as informações de faturação e conteúdo multimédia que pretendem expor no site. Cada revendedor que subscrevem para o site para obter um início de sessão do utilizador, utilizado para autenticar o utilizador antes de efetuar atualizações ao perfil do assinante.  Vamos armazena todos os dados de subscritor no armazenamento de base de dados do SQL Azure e BLOBS do Azure.
Vamos optado por participar para um modelo de autenticação com base em autenticação baseada em formulários do .NET. Optamos por esta abordagem para sua simplificar; não precisamos de funções, suporte de IU e outras funcionalidades estranhas que estão incluídos outras abordagens. 

Para garantir que revendedores vejam apenas os dados que pertencem aos mesmos, criámos num revendedor ID de cada revendedor subsequentemente utilizado em todas as operações leitura e escrita que envolvam dados específicos do revendedor. Com esta abordagem, encontrámos que não precisamos conceder permissões de base de dados aos revendedores individuais. Todos os revendedores interagem com o sistema em função uma única base de dados, com o ID do revendedor como nosso técnica de isolamento de dados.

Porque o nossa empresas são tudo sobre os efeitos descendentes (condução empresas mais para revendedores, criar incentivos para anunciar e subscrever), podemos pode desenhar a linha de cada processamento compras através da web. Como tal, não encontrará um carrinho de compras no nosso site, o que simplifica o nossos os requisitos de segurança. 

Outra simplificação que podemos ocupados foi deixem nossas operações faturação e de contas a pagar à. Por informações de pagamento do cliente encaminhamento diretamente para de terceiros ([SveaWebPay](http://www.sveawebpay.se/)), podemos reduzir os riscos associando armazenar e proteger dados confidenciais no nossos arquivos de dados. 

### <a name="search-engine"></a>Motor de busca

O essencial a nossa solução é motor de busca criada com base em serviço de pesquisa do Azure. Inicialmente, criámos um motor de procura personalizada, mas durante este processo, podemos realizados a complexidade e esforço foi muito alto facto e que lhe for pedido-na ter em consideração outras alternativas. 

Funcionalidades básicas que foram mais importantes-nos incluído:

- Filtros
- Navegação por facetas
- Aumentar os resultados
- Paginação através de AJAX

Uma pesquisa de internet-nos importados para o vídeo seguinte, o qual inspirar-se-nos Experimente o Azure pesquisa: [Vôo picado abrangente na Europa TechEd](http://channel9.msdn.com/events/TechEd/Europe/2014/DBI-B410) 

Depois de ver o vídeo, iremos foram prontos para criar um protótipo com base em podemos viu. Uma vez que recomendamos já tinha um modelo de dados no MVC, criar o protótipo foi simples porque os dados continham termos pesquisáveis e podemos já tinha trabalhado saída os requisitos para como queremos para Faceta, ordenar e filtrar os dados. 

Este é como criámos o protótipo.

**Configurar o serviço de pesquisa do Azure**

1. Iniciar sessão no Portal clássica do Azure e adicionado o serviço de pesquisa a nossa subscrição. É utilizada a versão partilhada (gratuita com os nossos subscrição).
2. Crie um índice remissivo. Para o protótipo, utilizámos o portal da IU para definir os campos de procura e criar os perfis de pontuação. Os nossos perfil pontuação baseia-se a dados de localização: país | Localidade | endereço (consulte o artigo: adicionar perfis de pontuação).
3. Copiar o URL do serviço e o administrador api-chave para os nossos ficheiros de configuração. Esta chave está na página de serviço de pesquisa no portal do e são utilizada para autenticar o serviço.
    
**Desenvolver uma tarefa de indexador pesquisa – consola do Windows**

1. Ler todas as revendedores de base de dados.
2. Ligar a API do serviço de pesquisa Azure para carregar revendedores um a um (consulte o artigo: http://msdn.microsoft.com/library/azure/dn798930.aspx).
3. Definir uma propriedade na base de dados que revendedor é indexado utilizarão a indexação. Vamos fez ao adicionar um campo de 'indexador' que armazena o estado da indexação de cada perfil (indexado ou não). 

Consulte o artigo anexo para o fragmento de código que constrói a tarefa de indexador.

**Desenvolver um Portal de Web de pesquisa – MVC**

1. Chamar o serviço de pesquisa do Azure para obter todos os documentos a partir da pesquisa (consulte o artigo: http://msdn.microsoft.com/library/azure/dn798927.aspx)
2. Extrair a seguir a partir da resposta do serviço de pesquisa (ao utilizar json.net http://james.newtonking.com/json)
   - Resultados
   - Facetas
   - Contagem de resultados
   - Desenvolva uma interface de utilizador para apresentar os resultados da pesquisa, facetas e as contagens (já tivemos isto).

Este é o código que é utilizada para obter os resultados da pesquisa do Azure:

    string requestUrl = 
    string.Format("https://{0}.search.windows.net/indexes/profiles/docs?searchMode=all&$count=true&search={1}&facet=city,count:20&facet=category&$top=10&$skip={2}&api-version=2014-07-31-Preview{3}", Config.SearchServiceName, EscapeODataString(q), skip, filter);
      var response = client.GetAsync(requestUrl).Result; //  + Uri.EscapeDataString("hennes")
      response.EnsureSuccessStatusCode();
     dynamic json = JsonConvert.DeserializeObject(response.Content.ReadAsStringAsync().Result);

**Aumentar a localização**

Provavelmente o mais importante requisito de registo para verificar no protótipo incluído adicionar uma palavra-chave de pesquisa de localização à consulta. É crucial nosso portal que se um utilizador introduz um nome de localidade na pesquisa de consulta, que revendedores na cidade determinado seriam classificar superiores aos revendedores que tenham a palavra-chave cidade na descrição. Para este requisito, utilizámos um perfil de pontuação classificar um campo Cidade mais elevado que os outros campos.

**Suporte de múltiplos idiomas**

Vamos necessários para apresentar os resultados da pesquisa correto nos idiomas corretos e forneça uma opção para encontrar os mesmos resultados em idiomas diferentes. Foram dois lados para este problema: 

- Procurar palavras em vários idiomas
- Apresentar os resultados da pesquisa no idioma correto

Vamos resolvido a parte da apresentação ao adicionar um documento para cada idioma com texto localizado e uma propriedade com o idioma. Quando um utilizador introduz um termo de pesquisa, podemos utilizador `$filter` expressões para filtrar o idioma do utilizador tiver optado por.

Cada um desses documentos tem uma propriedade oculta denominada "cidades" criada com base no tipo de coleções de sites. Esta propriedade armazena nomes de cidades em todos os idiomas, permitindo que o utilizador procurar em vários idiomas.

###<a name="data-storage"></a>Armazenamento de dados

Todos os dados (perfil, subscrição e contabilidade) são armazenados numa base de dados SQL. Todos os ficheiros multimédia são armazenados no armazenamento de BLOBS do Azure, incluindo imagens e vídeos fornecidos pelo revendedor. Utilizar o armazenamento de BLOBS separado isola os efeitos de carregamento dos ficheiros; ficheiros nunca são cocriação mingled com o Web site, por isso não precisamos de recriar o site sempre que recomendamos adicionar ficheiros.

Uma vantagem importante da nossa estrutura de armazenamento é os múltiplos programadores podem partilhar um armazenamento de desenvolvimento único. Um dos requisitos para o projeto WhatToPedia foi poderá criar um ambiente de desenvolvimento dentro de 15 minutos, incluindo dados revendedor, imagens e vídeos. Ao obter os dados mais recentes a partir de TFS Online, a executar um script SQL e executar a tarefa de importação, um ambiente completo pode ser passou de sem perder tempo de todo. Esta prática também melhora o processo de teste.

###<a name="webjobs"></a>WebJobs

Utilizamos Azure WebJobs para atualizar os dados para o índice. Ao criar uma tarefa do indexador de pesquisa, peça de indexação foi muito fácil de integrar a nossa solução. A alteração de código apenas foram efetuadas foi acomodar o trabalho indexador foi adicionar um `Indexed` campo ao nosso modelo de dados para indicar o estado de índice remissivo. Sempre que um novo perfil é adicionado ou atualizado, o `Indexed` campo esteja definido como falso. O mesmo se aplica se o revendedor altera dele dados de perfil através do portal.  

A tarefa de procura todas as linhas que está a ter `Indexed` definido como falso. Quando encontrar a linha, o documento é publicado no Azure pesquisa e, em seguida, o `Indexed` campo esteja definido como verdadeiro. Não temos que planear para adicionar versus actualização de dados, porque o serviço de pesquisa do Azure realmente tratará de isto. Se adicionar um documento que já se encontra presente, o serviço irá fazer uma atualização automaticamente.

Todas as tarefas de web foram desenvolvidas como aplicações da consola que podem ser carregadas Azure web sites como ficheiros ZIP, descompactadas e, em seguida, agendadas.

A tarefa está agendada para ser executado em 5 minutos como uma tarefa agendada web. Vamos calculados que o serviço demora cerca de três minutos para carregar 3.000 documentos, que foi dentro de nossos requisitos. 

> [AZURE.NOTE] Existe uma funcionalidade de indexador protótipo que foi introduzida recentemente na pesquisa Azure. Esta funcionalidade veio demasiado tarde para-nos para utilizá-lo no nosso lançamento inicial, mas parece resolver o problema mesmo que utilizámos o nossa tarefa indexador, ou seja, a automatizar carregar operações de dados.


###<a name="backup-strategy"></a>Estratégia de cópia de segurança

Vamos concebido uma estratégia de cópia de segurança várias camadas para recuperar a partir de um intervalo de cenários, a partir de falha grave, para baixo até recuperação de uma transação individual. Os ativos para proteger a incluem três tipos de dados (web site, dados de subscritor e ficheiros de multimédia). 

Em primeiro lugar, mantendo o código de origem do web site TFS Online, podemos saber que se vai o site para baixo, podemos pode reconstruir por voltar a publicar a partir do TFS. 

Dados de subscritor base de dados do SQL Azure são os elementos mais importantes. Vamos novamente isto através da incorporada funcionalidade (consulte [o cópia de segurança da base de dados do SQL Azure e restaurar](http://msdn.microsoft.com/library/azure/jj650016.aspx)). A agenda de cópia de segurança é a cópia de segurança da base de dados completa vez por semana, cópias de segurança da base de dados diferença uma vez por dia e cópias de segurança do registo da transação em 5 minutos.  Esta solução tendo em conta o tamanho dos dados, é mais adequada para a nossa volumes de dados de imediato e previsto.

Em terceiro lugar, podemos armazenar ficheiros de imagem e de vídeo no armazenamento de BLOBS do Azure. Ainda estamos são avaliar o plano de cópia de segurança ultimate para estes dados considerar Explorer de Cloudberry para Azure como uma possível solução. Por agora, utilizamos uma WebJob para copiar imagens e vídeos para outra localização.

##<a name="what-we-learned"></a>O podemos aprendeu

Uma vez que recomendamos já possui dados, foi fácil estabelecer prova de conceito. Dentro de horas, tivemos um protótipo com facetas contadores, paginação, classificados perfis e de resultados de pesquisa. Os resultados da pesquisa foram tão exatos, que podemos decidido remover algumas dos filtros apresentados para o cliente de fim. 

A inesperada maior para-nos foi velocidade podemos poderia saiba pesquisa Azure e quanto podemos tem novamente. Literalmente, Estabelecemos prova de conceito em algumas horas (ver nota abaixo), substituir 500 linhas de código com 3 linhas de código a aplicação de front-end (mais de uma nova WebJob) e obter melhores resultados. 

Anteriormente, o nosso código implementada paginação, contagens e outros comportamentos que estão padrão para procurar. Utilizando a pesquisa de Azure, os resultados que recomendamos voltar incluem os acertos de pesquisa, facetas, paginar dados contagens – todos os conteúdos que conforme necessário e foram ter de fornecer estabelecidos. Este procedimento também incluído aumentar e navegação por facetas incorporada, não temos no nossa solução original.

O desafio maior durante a execução foi que estava uma versão de pré-visualizar e encontrar informações e experiências partilhadas era difícil. Quando é estabelecida alguns pontos, encontrámos que utilizar o serviço de pesquisa do Azure foi bastante simple devido ao respetivo formato de dados REST API e JSON. Foi chamamos a arquitetura diretamente a partir do aberta mais origem plug-ins como JQuery JSON.Net e poderia utilizamos ferramentas como Fiddler para pioneira rápida e depuração. 

> [AZURE.NOTE] Para além de ter os dados preparados, contribuiu aqueles dos casos construir o protótipo já entendido como funciona a tecnologia de apoio, tornando-nos mais produtivo e mais appreciative das funcionalidades incorporadas de pesquisa. Se precisar de começar construção de consulta de pesquisa, navegação por facetas, filtros, etc. que deve esperar protótipos de demorar mais tempo. 

###<a name="controlling-facets-in-the-search-presentation-page"></a>Controlar facetas na página pesquisa da apresentação

Um dos nossos learnings durante o teste de conceito foi planear facetas cuidadosamente upfront. Após carregar muitos dados para a solução, podemos viu que o volume de facetas e-mails foi demasiado elevado para apresentar aos utilizadores. 

Vamos resolvido ao restringir o parâmetro de contagem faceta. O parâmetro contagem impõe um limite de disco rígido no número de facetas devolvida ao utilizador. Uma ligação que inclui um debate do parâmetro contar pode ser encontrada [aqui](search-faceted-navigation.md).

###<a name="webjobs-for-scheduling-tasks"></a>WebJobs para agendar tarefas

Pesquisa Azure não foi a apenas agradável inesperada para-nos. Vamos descobriu que WebJobs a utilizar para automatizar a nossa carregar operações de dados para pesquisa Azure foi largamente superior ao nossa abordagem anterior, que provocado utilizando uma VM dedicada a executar o Scheduler do Windows, com as tarefas agendadas para atualizar o índice de pesquisa. WebJobs foi mais simples configurar e fácil de depuração e obviamente muito menos dispendioso que ter de pagar uma VM dedicada.

###<a name="azure-blob-storage-explorer-for-updating-images"></a>Azure Explorador de armazenamento de BLOBS para actualizar as imagens

Encontrámos que através do [Explorador de armazenamento de BLOBS do Azure](https://azurestorageexplorer.codeplex.com/) (disponível no codeplex) para ser muito útil para gerir as atualizações de imagem e de vídeo para o site. Vamos utilizá-lo como uma ferramenta de programador para atualizar manualmente a imagens e vídeos que fazem parte do nosso site principal. Estamos encontrados que seja mais flexíveis do que implementar alterações para o portal e elimina uma iteração teste completa sempre que precisamos de actualizar uma imagem. 

##<a name="a-few-final-words"></a>Algumas palavras finais

Obrigado para as pessoas excelentes na WhatToPedia para permitir que-nos partilhar a sua história!  

Espero que encontrados este caso prático úteis. Se aceder utilizar a pesquisa do Azure, posso que alguns recursos para aumentar a velocidade que ao longo:

- [Fórum MSDN dedicado à procura do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=azuresearch)
- [StackOverflow também tem uma etiqueta](http://stackoverflow.com/questions/tagged/azure-search)
- [Página de documentação no Azure.com](https://azure.microsoft.com/documentation/services/search/)
- [Azure documentação de pesquisa no MSDN](http://msdn.microsoft.com/library/azure/dn798933.aspx)


##<a name="appendix-search-indexer-webjob"></a>Anexo: Pesquisa indexador WebJob

O código seguinte cria o indexador mencionado na secção sobre a criação de protótipo.

        static void Main(string[] args)
        {
            int success = 0;
            int errors = 0;

            Log.Write("Starting job","", System.Diagnostics.TraceLevel.Info);

            var serviceName = ConfigurationManager.AppSettings["SearchServiceName"];
            var serviceKey = ConfigurationManager.AppSettings["SearchServiceKey"];

            HttpClient client = new HttpClient();
            client.DefaultRequestHeaders.Add("api-key", serviceKey);

            var db = new DB(Config.ConectionString);

            var recreateIndex = false;
            Boolean.TryParse(ConfigurationManager.AppSettings["RecreateIndex"], out recreateIndex);

            if(recreateIndex)
            {
                Log.Write("Recreating index and set all to no index", "", System.Diagnostics.TraceLevel.Info);
                db.SetAllToNotIndexed();
                RecreateIndex(serviceName, client);
            }            
            
            var profiles = db.Profiles.Where(p=>!p.Indexed).ToList();

            Log.Write(string.Format("Indexing {0} profiles",profiles.Count),"", System.Diagnostics.TraceLevel.Info);

            var cities = db.Cities.ToList();
            var categories = db.Tags.Where(p=>p.ParentId==null).ToList();            

            foreach (var profile in profiles)
            {
                Log.Write(string.Format("Indexing profile {0}", profile.Name),"",profile.ProfileId,0,System.Diagnostics.TraceLevel.Verbose);

                try
                {
                    var city = cities.Where(p => p.CityId == profile.CityId);
                    var category = categories.Where(p => p.TagId == profile.CategoryId);

                    var cityse = city.Where(p => p.Lang == "se").FirstOrDefault();
                    var cityen = city.Where(p => p.Lang == "en").FirstOrDefault();
                    var categoryse = category.Where(p => p.Lang == "se").FirstOrDefault();
                    var categoryen = category.Where(p => p.Lang == "en").FirstOrDefault();

                    var citysename = cityse == null ? "" : cityse.Name;
                    var cityenname = cityen == null ? "" : cityen.Name;
                    var categorysename = categoryse == null ? "" : categoryse.Name;
                    var categoryenname = categoryen == null ? "" : categoryen.Name;

                    var tags = db.GetTagsFromProfile(profile.ProfileId);

                    var batch = new
                    {
                        value = new[] 
                    { 
                        new 
                        { 
                            id = profile.ProfileId.ToString()+"_en",
                            profileid = profile.ProfileId.ToString(),
                            city = cityenname,
                            category = categoryenname,
                            address = profile.Adress1,
                            email = profile.Email,
                            name = profile.Name,
                            lang = "en",
                            brands = profile.Brands,
                            descen=profile.DescEn,
                            descse=profile.DescSe,
                            orgnumber=profile.OrgNumber,
                            phone=profile.Phone,
                            zip=profile.Zip,
                            cities = city.Select(p=>p.Name).ToArray(),
                            categories = category.Select(p=>p.Name).ToArray(),
                            cityid = profile.CityId.ToString(),
                            tags=tags.ToArray()
                        },
                        new 
                        { 
                            id = profile.ProfileId.ToString()+"_se",
                            profileid = profile.ProfileId.ToString(),
                            city = citysename,
                            category = categorysename,
                            address = profile.Adress1,
                            email = profile.Email,
                            name = profile.Name,
                            lang = "se",
                            brands = profile.Brands,
                            descen=profile.DescEn,
                            descse=profile.DescSe,
                            orgnumber=profile.OrgNumber,
                            phone=profile.Phone,
                            zip=profile.Zip,
                            cities = city.Select(p=>p.Name).ToArray(),
                            categories = category.Select(p=>p.Name).ToArray(),
                            cityid = profile.CityId.ToString(),
                            tags=tags.ToArray()
                        }
                    },
                    };

                    var response = client.PostAsync("https://" + serviceName + ".search.windows.net/indexes/profiles/docs/index?api-version=2014-10-20-Preview", new StringContent(JsonConvert.SerializeObject(batch), Encoding.UTF8, "application/json")).Result;
                    response.EnsureSuccessStatusCode();

                    db.Entry(profile).State = System.Data.Entity.EntityState.Modified;
                    profile.Indexed = true;
                    db.SaveChanges();
                    success++;
                }
                catch(Exception ex)
                {
                    Log.Write("Error indexing profile", ex.Message, profile.ProfileId, 0, System.Diagnostics.TraceLevel.Verbose);
                    errors++;
                }
            }
            if(errors > 0)
            {
                Log.Write(string.Format("Job ended success ({0}), errors ({1})", success, errors), "", System.Diagnostics.TraceLevel.Error);
            }
            else
            {
                Log.Write(string.Format("Job ended success ({0}), errors ({1})", success, errors), "", System.Diagnostics.TraceLevel.Info);
            }
            
        }

        static void RecreateIndex( string ServiceName, HttpClient client)
        {
            var index = new
            {
                name = "profiles",
                fields = new[] 
                { 
                    new { name = "id",              type = "Edm.String",         key = true,  searchable = false, filterable = false, sortable = false, facetable = false, retrievable = true,  suggestions = false },
                    new { name = "profileid",       type = "Edm.String",         key = false,  searchable = false, filterable = false, sortable = false, facetable = false, retrievable = true,  suggestions = false },
                    new { name = "cityid",          type = "Edm.String",         key = false,  searchable = false, filterable = false, sortable = false, facetable = false, retrievable = true,  suggestions = false },
                    new { name = "city",            type = "Edm.String",         key = false, searchable = true,  filterable = true, sortable = true,  facetable = true, retrievable = true,  suggestions = true  },
                    new { name = "category",        type = "Edm.String",         key = false, searchable = true,  filterable = true, sortable = false, facetable = true, retrievable = true,  suggestions = false  },
                    new { name = "address",         type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true,  suggestions = false },
                    new { name = "email",           type = "Edm.String",         key = false, searchable = true,  filterable = false, sortable = true, facetable = false, retrievable = true,  suggestions = false },
                    new { name = "name",            type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true, suggestions = true },
                    new { name = "lang",            type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = false,  facetable = false,  retrievable = true, suggestions = false },
                    new { name = "brands",          type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true,  suggestions = false },
                    new { name = "descen",          type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true,  suggestions = false },
                    new { name = "descse",          type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true,  suggestions = false },
                    new { name = "orgnumber",       type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true,  suggestions = false },
                    new { name = "phone",           type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true,  suggestions = false },
                    new { name = "zip",             type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true,  suggestions = false },
                    new { name = "cities",          type = "Collection(Edm.String)",         key = false, searchable = true,  filterable = false,  sortable = false,  facetable = false,  retrievable = false, suggestions = false },
                   new { name = "categories",      type = "Collection(Edm.String)",         key = false, searchable = true,  filterable = false,  sortable = false,  facetable = false,  retrievable = false, suggestions = false },
                    new { name = "tags",            type = "Collection(Edm.String)",         key = false, searchable = true,  filterable = false,  sortable = false,  facetable = false,  retrievable = false, suggestions = false }
                    
                }
            };

            var url = "https://" + ServiceName + ".search.windows.net/indexes/?api-version=2014-10-20-Preview";

            var deleteUrl = "https://" + ServiceName + ".search.windows.net/indexes/profiles?api-version=2014-10-20-Preview";

            try
            {
                var deleteResponseIndex = client.DeleteAsync(deleteUrl).Result;
                deleteResponseIndex.EnsureSuccessStatusCode();
            }
            catch (Exception ex)
            {

            }

            var responseIndex = client.PostAsync(url, new StringContent(JsonConvert.SerializeObject(index), Encoding.UTF8, "application/json")).Result;
            responseIndex.EnsureSuccessStatusCode();            
          


<!--Anchors-->
[Subheading 1]: #subheading-1
[Subheading 2]: #subheading-2
[Subheading 3]: #subheading-3
[Subheading 4]: #subheading-4
[Subheading 5]: #subheading-5
[Next steps]: #next-steps

<!--Image references-->
[6]: ./media/search-dev-case-study-whattopedia/lightbulb.png
[7]: ./media/search-dev-case-study-whattopedia/WhatToPedia-Search-Bageri.png
[8]: ./media/search-dev-case-study-whattopedia/WhatToPedia-Stack.png
[9]: ./media/search-dev-case-study-whattopedia/WhatToPedia-Archicture.png


<!--Link references-->
[Link 1 to another azure.microsoft.com documentation topic]: ../virtual-machines-windows-hero-tutorial.md
[Link 2 to another azure.microsoft.com documentation topic]: ../web-sites-custom-domain-name.md
[Link 3 to another azure.microsoft.com documentation topic]: ../storage-whatis-account.md
 
