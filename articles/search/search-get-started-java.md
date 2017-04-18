<properties
    pageTitle="Introdução ao Azure pesquisa em Java | Microsoft Azure | Serviço de pesquisa alojado na nuvem"
    description="Como criar uma aplicação de pesquisa na nuvem alojado no Azure utilizando Java como linguagem de programação."
    services="search"
    documentationCenter=""
    authors="EvanBoyle"
    manager="pablocas"
    editor="v-lincan"/>

<tags
    ms.service="search"
    ms.devlang="na"
    ms.workload="search"
    ms.topic="hero-article"
    ms.tgt_pltfrm="na"
    ms.date="07/14/2016"
    ms.author="evboyle"/>

# <a name="get-started-with-azure-search-in-java"></a>Introdução ao Azure pesquisa em Java
> [AZURE.SELECTOR]
- [Portal](search-get-started-portal.md)
- [.NET](search-howto-dotnet-sdk.md)

Saiba como criar uma aplicação de pesquisa Java personalizada que utiliza Azure a pesquisa para a sua experiência de pesquisa. Neste tutorial utiliza a [REST API do serviço de pesquisa do Azure](https://msdn.microsoft.com/library/dn798935.aspx) para construir os objetos e operações utilizadas neste exercício.

Para executar este exemplo, tem de ter um serviço de pesquisa do Azure, pode inscrever no [Portal do Azure](https://portal.azure.com). Consulte o artigo [criar um serviço de pesquisa do Azure no portal](search-create-service-portal.md) para obter instruções passo a passo.

O seguinte software é utilizada para criar e testar este exemplo:

- [Eclipse IDE para programadores de EE Java](https://eclipse.org/downloads/packages/eclipse-ide-java-ee-developers/lunar). Não se esqueça de transferir a versão EE. Um destes passos verificação requer uma funcionalidade que se encontram apenas nesta edição.

- [JDK 8u40](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)

- [Apache Tomcat 8.0](http://tomcat.apache.org/download-80.cgi)

## <a name="about-the-data"></a>Acerca dos dados

Esta aplicação exemplo utiliza os dados a partir de [Estados Unidos geológicas serviços (USG)](http://geonames.usgs.gov/domestic/download_data.htm), filtrado no estado da ilha Rhode para reduzir o tamanho do conjunto de dados. Vamos utilizar estes dados para criar uma aplicação de pesquisa devolve edifícios marco como hospitais e escolas, bem como funcionalidades geológicas como sequências, lagos e cimeiras.

Esta aplicação, o programa **SearchServlet.java** cria e carrega o índice utilizando uma construção de [indexador](https://msdn.microsoft.com/library/azure/dn798918.aspx) obter o conjunto de dados filtrado USG a partir de uma base de dados do SQL Azure público. Credenciais predefinidas e informações de ligação à origem de dados online são fornecidas no código de programa. Em termos de acesso a dados, não é necessária nenhuma configuração mais.

> [AZURE.NOTE] Vamos aplicado um filtro neste conjunto de dados para se manter abaixo do limite de 10.000 documento da camada comparar gratuito. Se utilizar a camada padrão, este limite não se aplica e pode modificar este código para utilizar um conjunto de dados maior. Para obter detalhes sobre capacidade para cada camada comparar, consulte o artigo [limites e restrições](search-limits-quotas-capacity.md).

## <a name="about-the-program-files"></a>Sobre os ficheiros de programa

A lista seguinte descreve os ficheiros que relevantes para este exemplo.

- Search.jsp: Fornece a interface de utilizador
- SearchServlet.java: Fornece métodos (semelhantes a um controlador no MVC)
- SearchServiceClient.java: Pedidos de alças HTTP
- SearchServiceHelper.java: Uma helper classe que fornece métodos estáticos
- Document.Java: Fornece o modelo de dados
- Config.Properties: define o URL do serviço de pesquisa e a chave de api
- Pom.XML: Uma dependência Maven

<a id="sub-2"></a>
## <a name="find-the-service-name-and-api-key-of-your-azure-search-service"></a>Localizar o nome de serviço e chave de api do seu serviço de pesquisa do Azure

Todas as chamadas de REST API Azure procurar requerem que fornece o URL do serviço e uma tecla de api. 

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Na barra de atalhos, clique em **serviço de pesquisa** para listar todos os serviços de pesquisa do Azure aprovisionados para a sua subscrição.
3. Selecione o serviço que pretende utilizar.
4. No dashboard de serviço, verá os mosaicos para as informações essenciais e o ícone de chave para aceder às teclas de administrador.

    ![][3]

5. Copie o URL do serviço e uma tecla de administrador. Terá-las mais tarde, quando adicioná-los para o ficheiro **config.properties** .

## <a name="download-the-sample-files"></a>Transferir os ficheiros de exemplo

1. Aceda à [AzureSearchJavaDemo](https://github.com/AzureSearch/AzureSearchJavaIndexerDemo) no Github.

2. Clique em **Transferir ZIP**, guardar o ficheiro. zip no disco e, em seguida, extraia todos os ficheiros que contém. Considere extrair os ficheiros para a área de trabalho Java para facilitar a localização do projeto mais tarde.

3. Os ficheiros de amostra são só de leitura. Propriedades da pasta com o botão direito e desmarque o atributo só de leitura.

Todas as modificações ficheiro subsequentes e executar declarações serão feitas contra ficheiros nesta pasta.  

## <a name="import-project"></a>Importar projecto

1. No Eclipse, selecione **ficheiro** > **Importar** > **gerais** > **Projectos existentes na área de trabalho**.

    ![][4]

2. Em **Selecione o diretório de raiz**, navegue para a pasta que contém os ficheiros de exemplo. Selecione a pasta que contém a pasta .project. O projeto deve aparecer na lista de **projetos** , como um item selecionado.

    ![][12]

3. Clique em **Concluir**.

4. Utilize o **Project Explorer** para ver e editar os ficheiros. Se ainda não estiver aberta, clique em **janela** > **Mostrar vista do** > **Project Explorer** ou utilize o atalho para o abrir.

## <a name="configure-the-service-url-and-api-key"></a>Configurar o URL do serviço e a chave de api

1. No **Project Explorer**, faça duplo clique **config.properties** para editar as definições de configuração que contém o nome de servidor e a chave de api.

2. Consulte os passos neste artigo, onde encontrado o URL do serviço e a chave de api no [Portal do Azure](https://portal.azure.com), obtenha os valores que agora irá introduzir **config.properties**.

3. Na **config.properties**, substitua "Api chave" com a tecla api do seu serviço. Em seguida, o nome do serviço (o primeiro componente do http://servicename.search.windows.net URL) substitui "nome do serviço" no mesmo ficheiro.

    ![][5]

## <a name="configure-the-project-build-and-runtime-environments"></a>Configurar os ambientes de projeto, criar e runtime

1. No Eclipse, no Explorador de projeto, com o botão direito do projeto > **Propriedades** > **Facetas do projeto**.

2. Selecione **Web dinâmico módulo**, **Java**e **JavaScript**.

    ![][6]

3. Clique em **Aplicar**.

4. **Janela**selecionar > **Preferências** > **Server** > **Runtime ambientes** > **divisa.**.

5. Expanda Apache e selecione a versão do servidor Apache Tomcat instalado anteriormente. No nosso sistema, podemos versão 8 instalada.

    ![][7]

6. Na página seguinte, especifique o diretório de instalação Tomcat. Num computador Windows, provavelmente, será c:\Programas\Microsoft Files\Apache Software Foundation\Tomcat *versão*.

6. Clique em **Concluir**.

7. **Janela**selecionar > **Preferências** > **Java** > **instalado JREs** > **Adicionar**.

8. Na **JRE adicionar**, selecione **VM padrão**.

10. Clique em **seguinte**.

11. Na definição de JRE, no JRE principal, clique em **diretório**.

12. Navegue para **Ficheiros de programas** > **Java** e selecione o JDK tiver instalado anteriormente. É importante selecionar o JDK como JRE.

13. Na JREs instalado, escolha o **JDK**. As definições de deverão ter um aspeto semelhantes ao seguinte captura de ecrã.

    ![][9]

14. Em alternativa, selecione **janela** > **Web Browser** > **Internet Explorer** para abrir a aplicação numa janela do browser externos. Utilizar um browser externo dá-lhe uma melhor experiência de aplicação Web.

    ![][8]

Tem agora concluir as tarefas de configuração. Em seguida, irá criar e executar o projeto.

## <a name="build-the-project"></a>Criar o projecto

1. No Explorador de projeto, o nome do projeto com o botão direito e selecione **Executar como** > **Maven construir …** para configurar o projeto.

    ![][10]

8. Editar configuração, em objetivos, escreva "limpo instalar" e, em seguida, clique em **Executar**.

Mensagens de estado são o resultado na janela da consola. Deverá ver SUCESSO CONSTRUIR a indicar que o projeto compilado sem erros.

## <a name="run-the-app"></a>Executar a aplicação

Neste último passo será executar a aplicação num ambiente do runtime local server.

Se ainda ainda não tiver indicado um ambiente do tempo de execução de servidor no Eclipse, terá de fazer em primeiro lugar.

1. No Project Explorer, expanda **ConteúdoWeb**.

5. Botão direito do rato **Search.jsp** > **Executar como** > **executar no servidor**. Selecione o servidor Apache Tomcat e, em seguida, clique em **Executar**.

> [AZURE.TIP] Se utilizou uma área de trabalho não predefinida para armazenar o seu projeto, terá de modificar **Executar configuração** para apontar para a localização do projecto para evitar um erro de arranque do servidor. No Explorador do Project, botão direito do rato **Search.jsp** > **Executar como** > **Configurações executar**. Selecione o servidor Apache Tomcat. Clique em **argumentos**. Clique em **área de trabalho** ou **Sistema de ficheiros** para definir a pasta que contém o projeto.

Quando executar a aplicação, deverá ver uma janela do browser, fornecer uma caixa de pesquisa para introduzir termos.

Aguarde cerca de um minuto antes de clicar em **Procurar** para dar o tempo de serviço para criar e carregar o índice. Se obtiver um erro HTTP 404, só precisa de esperar ligeiramente já antes de tentar novamente.

## <a name="search-on-usgs-data"></a>Procurar dados USGS

O conjunto de dados USG inclui registos que são importantes para o estado da Rhode ilha. Se clicar em **pesquisa** com uma caixa de pesquisa vazia, obterá as entradas de 50 superiores, que é a predefinição.

Introduzir um termo de pesquisa irá dar-motor de busca algo para ir para no. Tente introduzir um nome regional. "Roger Williams" foi a primeira Governador de Rhode ilha. Vários parques, edifícios e escolas são o nome da contactá-la.

![][11]

Também pode tentar qualquer um destes termos:

- Pawtucket
- Pembroke
- Ganso + cabo

## <a name="next-steps"></a>Próximos passos

Este é o primeiro tutorial do Azure pesquisa com base em Java e o conjunto de dados USG. Ao longo do tempo, irá Expandimos este tutorial para ficar demonstram funcionalidades de pesquisa adicionais que poderá querer utilizar no seu soluções personalizadas.

Se já tiver alguns fundo na pesquisa Azure, pode utilizar este exemplo como um springboard para ainda mais pioneira, talvez aumentando a [página de pesquisa](search-pagination-page-layout.md)ou implementar a [Navegação por facetas](search-faceted-navigation.md). Também pode melhorar relativamente a página de resultados de pesquisa ao adicionar contagens e documentos de lotes para que os utilizadores podem percorrer os resultados.

Novo no Azure pesquisa? Recomendamos que está a tentar outros tutoriais desenvolver compreender o que pode criar. Visite a nossa [página de documentação](https://azure.microsoft.com/documentation/services/search/) para obter mais recursos. Também pode ver as ligações no nosso [vídeo e lista de iniciação](search-video-demo-tutorial-list.md) para aceder a mais informações.

<!--Image references-->
[1]: ./media/search-get-started-java/create-search-portal-1.PNG
[2]: ./media/search-get-started-java/create-search-portal-21.PNG
[3]: ./media/search-get-started-java/create-search-portal-31.PNG
[4]: ./media/search-get-started-java/AzSearch-Java-Import1.PNG
[5]: ./media/search-get-started-java/AzSearch-Java-config1.PNG
[6]: ./media/search-get-started-java/AzSearch-Java-ProjectFacets1.PNG
[7]: ./media/search-get-started-java/AzSearch-Java-runtime1.PNG
[8]: ./media/search-get-started-java/AzSearch-Java-Browser1.PNG
[9]: ./media/search-get-started-java/AzSearch-Java-JREPref1.PNG
[10]: ./media/search-get-started-java/AzSearch-Java-BuildProject1.PNG
[11]: ./media/search-get-started-java/rogerwilliamsschool1.PNG
[12]: ./media/search-get-started-java/AzSearch-Java-SelectProject.png
