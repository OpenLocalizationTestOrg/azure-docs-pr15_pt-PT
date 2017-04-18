<properties
    pageTitle="Integrar um serviço na nuvem Azure CDN | Microsoft Azure"
    description="Um tutorial que lhe ensina implementar um serviço na nuvem que serve de conteúdos a partir de um extremo Azure CDN integrado"
    services="cdn, cloud-services"
    documentationCenter=".net"
    authors="camsoper"
    manager="erikre"
    editor="tysonn"/>

<tags
    ms.service="cdn"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="07/28/2016"
    ms.author="casoper"/>


# <a name="intro"></a>Integrar um serviço na nuvem Azure CDN

Um serviço na nuvem pode ser integrado com o Azure CDN, funcionar qualquer tipo de conteúdo a partir da localização do serviço na nuvem. Esta abordagem dá-lhe as seguintes vantagens:

- Implementar e actualizar imagens, scripts e folhas de estilo em directórios de projeto do seu serviço de nuvem facilmente
- Atualizar facilmente os pacotes de NuGet no seu serviço de nuvem, como jQuery ou versões de arranque do sistema
- Gerir a aplicação Web e o seu servida CDN conteúda todas as a partir da mesma interface do Visual Studio
- Implementação unificado fluxo de trabalho para a aplicação Web e o seu conteúdo servida CDN
- Integrar ASP.NET Unir e minification Azure CDN

## <a name="what-you-will-learn"></a>O que vai aprender ##

Neste tutorial, irá aprender como:

-   [Integrar um ponto final Azure CDN com o seu serviço de nuvem e agir em conteúdo estático nas suas páginas Web a partir do Azure CDN](#deploy)
-   [Configurar definições de cache para em conteúdo estático no seu serviço de nuvem](#caching)
-   [Servir conteúdo a partir de ações de controlador através do Azure CDN](#controller)
-   [Servir agrupados e minified conteúdo através do Azure CDN preservando o script depuração experiência no Visual Studio](#bundling)
-   [Configurar contingência os scripts e CSS quando o seu CDN Azure está offline](#fallback)

## <a name="what-you-will-build"></a>O que irá criar ##

Será implementar uma função de Web do serviço de nuvem utilizando o modelo de ASP.NET MVC predefinido, adicionar código para servir o conteúdo a partir de uma CDN Azure integradas, tais como uma imagem, os resultados da ação de controlador e os ficheiros de JavaScript e CSS de predefinida e também escrever código para configurar o dispositivo contingência de conjuntos servida se que a CDN está offline.

## <a name="what-you-will-need"></a>O que irá precisar ##

Neste tutorial tem os pré-requisitos seguintes:

-   Uma [conta do Microsoft Azure](/account/) de ativos
-   Visual Studio 2015 com [Azure SDK](http://go.microsoft.com/fwlink/?linkid=518003&clcid=0x409)

> [AZURE.NOTE] Precisa de uma conta Azure para concluir este tutorial:
> + Pode [Abrir uma conta Azure gratuitamente](/pricing/free-trial/) - obtém créditos pode utilizar para experimentar o nosso pagos serviços Azure e mesmo depois de que estão habituados até pode manter a conta e utilização livre Azure serviços, como sites.
> + Pode [Ativar benefícios de subscritor MSDN](/pricing/member-offers/msdn-benefits-details/) - subscrição da sua MSDN fornece-lhe créditos todos os meses que pode utilizar para serviços Azure pagos.

<a name="deploy"></a>
## <a name="deploy-a-cloud-service"></a>Implementar um serviço na nuvem ##

Nesta secção, irá implementar o modelo de aplicação de ASP.NET MVC no Visual Studio 2015 predefinido para uma função de Web do serviço de nuvem e, em seguida, integrá-lo com um novo ponto final CDN. Siga as instruções abaixo:

1. No Visual Studio 2015, criar um novo serviço de nuvem Azure a partir da barra de menu ao aceder a **ficheiro > novo > projeto > nuvem > serviço em nuvem Azure**. Atribua um nome e clique em **OK**.

    ![](media/cdn-cloud-service-with-cdn/cdn-cs-1-new-project.PNG)

2. Selecione a **Função de Web do ASP.NET** e clique na **>** botão. Clique em OK.

    ![](media/cdn-cloud-service-with-cdn/cdn-cs-2-select-role.PNG)

3. Selecione **MVC** e clique em **OK**.

    ![](media/cdn-cloud-service-with-cdn/cdn-cs-3-mvc-template.PNG)

4. Agora, publica esta função Web para um serviço em nuvem Azure. Com o botão direito do projecto do serviço de nuvem e selecione **Publicar**.

    ![](media/cdn-cloud-service-with-cdn/cdn-cs-4-publish-a.png)

5. Se ainda não tiver subscrito no Microsoft Azure, clique no menu pendente de **Adicionar uma conta …** e clique no item de menu **Adicionar uma conta** .

    ![](media/cdn-cloud-service-with-cdn/cdn-cs-5-publish-signin.png)

6. Na página de início de sessão no, inicie sessão com a conta Microsoft que utilizou para ativar a sua conta Azure.
7. Assim que tem sessão iniciada, clique em **seguinte**.

    ![](media/cdn-cloud-service-with-cdn/cdn-cs-6-publish-signedin.png)

8. Partindo do princípio que ainda não criou uma conta de serviço ou de armazenamento na nuvem, Visual Studio irá ajudá-lo a criar ambos. Na caixa de diálogo **criar serviço em nuvem e conta** , escreva o nome do serviço pretendida e selecione a região pretendida. Em seguida, clique em **Criar**.

    ![](media/cdn-cloud-service-with-cdn/cdn-cs-7-publish-createserviceandstorage.png)

9. Na página de definições de publicar, verifique a configuração e clique em **Publicar**.

    ![](media/cdn-cloud-service-with-cdn/cdn-cs-8-publish-finalize.png)

    >[AZURE.NOTE] O processo de publicação de serviços em nuvem demora muito tempo. A ativar Web implementação para todas as opção de funções pode tornar a depuração seu serviço de nuvem muito mais rápido, fornecendo rápidas (mas temporárias) actualizações a sua funções da Web. Para mais informações sobre esta opção, consulte a [publicação de um serviço de nuvem utilizando as ferramentas do Azure](http://msdn.microsoft.com/library/ff683672.aspx).

    Quando o **Registo de atividade do Microsoft Azure** mostra que o estado de publicação está **concluída**, irá criar um ponto final de CDN está integrado com este serviço na nuvem.

    >[AZURE.WARNING] Se, após a publicação, o serviço de nuvem implementado é apresentado um ecrã de erro, é provável que porque o serviço de nuvem que tenha implementado está a utilizar o [sistema operativo que não inclui .NET 4.5.2 de convidado](../cloud-services/cloud-services-guestos-update-matrix.md#news-updates).  Pode contornar este problema ao [Implementar o .NET 4.5.2 como uma tarefa de arranque](../cloud-services/cloud-services-dotnet-install-dotnet.md).

## <a name="create-a-new-cdn-profile"></a>Criar um novo perfil CDN

Um perfil CDN é uma coleção de pontos finais de CDN.  Cada perfil contém um ou mais pontos finais CDN.  Pode optar por utilizar vários perfis para organizar os pontos finais de CDN ao domínio da internet, aplicação web ou por outros critérios algumas.

> [AZURE.TIP] Se já tiver um perfil CDN que pretende utilizar para este tutorial, avance para [criar um novo ponto final CDN](#create-a-new-cdn-endpoint).

[AZURE.INCLUDE [cdn-create-profile](../../includes/cdn-create-profile.md)]

## <a name="create-a-new-cdn-endpoint"></a>Criar um novo ponto final CDN

**Para criar um novo ponto final de CDN para a sua conta de armazenamento**

1. No [Portal de gestão do Azure](https://portal.azure.com), navegue até ao seu perfil CDN.  Pode pode ter afixada-lo para o dashboard no passo anterior.  Se não, pode encontrá-lo clicando em **Procurar**, em seguida, **perfis CDN**e clicar no perfil que pretende adicionar o seu ponto final para.

    É apresentada a pá de perfil CDN.

    ![Perfil CDN][cdn-profile-settings]

2. Clique no botão **Adicionar ponto final** .

    ![Adicionar botão de ponto final][cdn-new-endpoint-button]

    É apresentada a pá de **Adicionar um ponto final** .

    ![Adicionar pá de ponto final][cdn-add-endpoint]

3. Introduza um **nome** para este ponto final CDN.  Este nome será utilizada para aceder aos seus recursos em cache no domínio `<EndpointName>.azureedge.net`.

4. Na lista pendente **tipo de origem** , selecione o *serviço na nuvem*.  

5. Na lista pendente **origem hostname** , selecione o seu serviço de nuvem.

6. Deixe as predefinições para o **caminho de origem**, **cabeçalho de anfitrião de origem**e **porta de protocolo/origem**.  Tem de especificar pelo menos uma protocol (HTTP ou HTTPS).

7. Clique no botão **Adicionar** para criar o novo ponto final.

8. Quando o ponto final estiver criado, aparece uma lista de pontos finais para o perfil. A vista de lista mostra o URL para o utilizar para aceder a conteúdo em cache, bem como o domínio de origem.

    ![Ponto final CDN][cdn-endpoint-success]

    > [AZURE.NOTE] O ponto final não imediatamente estará disponível para utilização.  Pode demorar até 90 minutos para o registo a serem propagadas através da rede CDN. Os utilizadores que tentarem para utilizar o nome de domínio CDN imediatamente poderão receber o código de estado 404 até que o conteúdo está disponível através de CDN.

## <a name="test-the-cdn-endpoint"></a>Testar o ponto final de CDN

Quando o estado de publicação estiver **concluída**, abra uma janela do browser e navegue para * *http://<cdnName>*.azureedge.net/Content/bootstrap.css**. Na minha configuração, este URL é:

    http://camservice.azureedge.net/Content/bootstrap.css

Que corresponde ao seguinte URL de origem no ponto final CDN:

    http://camcdnservice.cloudapp.net/Content/bootstrap.css

Quando navega para * *http://*&lt;cdnName >*.azureedge.net/Content/bootstrap.css**, cálculo consoante a forma no seu browser, ser-lhe-á pedido para transferir ou abrir o bootstrap.css que veio incluída da sua aplicação Web publicada.

![](media/cdn-cloud-service-with-cdn/cdn-1-browser-access.PNG)

Pode aceder de forma semelhante a qualquer acessível publicamente URL na * *http://*&lt;serviceName >*.cloudapp.net/**, diretamente a partir do seu ponto final de CDN. Por exemplo:

-   Um ficheiro. js do caminho /Script
-   Qualquer ficheiro de conteúdo a partir de/o conteúdo caminho
-   Qualquer controlador/acção
-   Se a cadeia de consulta estiver ativada na sua endpoint CDN, qualquer URL com cadeias de consulta

Na verdade, com a configuração acima, pode alojar o serviço de nuvem inteira do * *http://*&lt;cdnName >*.azureedge.net/**. Se de que deixar de navegar para **http://camservice.azureedge.net/ * *, posso obter o resultado de ação de base/índice.

![](media/cdn-cloud-service-with-cdn/cdn-2-home-page.PNG)

Isto não significa, no entanto, que está sempre uma boa ideia (ou geralmente uma boa ideia) para servir um serviço em nuvem inteira através de Azure CDN. Alguns dos avisos são:

-   Esta abordagem requer todo o site para ser público, porque o Azure CDN não é possível servir qualquer conteúdo privado neste momento.
-   Se o ponto final de CDN ficar offline por qualquer motivo, se manutenção agendada ou erros do utilizador, o serviço de nuvem inteira fica offline, a menos que os clientes podem ser redireccionados para o URL de origem * *http://*&lt;serviceName >*.cloudapp.net/**.
-   Mesmo com as definições de Cache controlo personalizadas (consulte o artigo [Configurar opções de cache para ficheiros estáticos no seu serviço de nuvem](#caching)), um ponto final de CDN não melhorar o desempenho do conteúdo dinâmico altamente. Se tiver tentado carregar a home page a partir do seu ponto final de CDN como demonstrado acima, repare que demorou pelo menos 5 segundos a carregar a home page predefinida na primeira vez, é uma página bastante simple. Imagine que iria acontecer para a experiência do cliente se esta página contém conteúdo dinâmico que terá de actualizar minuto. Servir conteúdo dinâmico a partir de um ponto final de CDN necessita de expiração de cache curtas, que corresponde a cache frequente falhas no ponto final CDN função. Isto hurts o desempenho do seu serviço de nuvem e destrói a finalidade de uma CDN.

A alternativa é determinar que conteúdo para servir a partir do Azure CDN no caso a caso de no seu serviço de nuvem. Para o efeito, que já viu como aceder aos ficheiros individuais de conteúdo a partir do ponto final de CDN. Posso irá mostrar-lhe como servir uma ação de controlador de específico através do ponto final de CDN no [servir conteúdo a partir de ações de controlador através de Azure CDN](#controller).

<a name="caching"></a>
## <a name="configure-caching-options-for-static-files-in-your-cloud-service"></a>Configurar opções de colocação em cache para ficheiros estáticos no seu serviço de nuvem ##

Com a integração do Azure CDN no seu serviço de nuvem, pode especificar como pretende que sejam colocadas em cache no ponto final de CDN em conteúdo estático. Para executar esta tarefa, abra a *Web. config* a partir do seu projeto de funções Web (por exemplo, WebRole1) e adicione uma `<staticContent>` elemento a `<system.webServer>`. XML abaixo configura a cache de expirar em 3 dias.  

    <system.webServer>
      <staticContent>
        <clientCache cacheControlMode="UseMaxAge" cacheControlMaxAge="3.00:00:00"/>
      </staticContent>
      ...
    </system.webServer>

Depois de fazer isto, todos os ficheiros estáticos no seu serviço de nuvem irão observar a mesma regra na cache do CDN. Para um controlo mais granular das definições de cache, adicione um ficheiro de *Web. config* para uma pasta e adicione as definições de aí. Por exemplo, adicionar um ficheiro de *Web. config* para a pasta *\Content* e substitua o conteúdo XML seguinte:

    <?xml version="1.0"?>
    <configuration>
      <system.webServer>
        <staticContent>
          <clientCache cacheControlMode="UseMaxAge" cacheControlMaxAge="15.00:00:00"/>
        </staticContent>
      </system.webServer>
    </configuration>

Esta definição faz com que todos os ficheiros estáticos a partir da pasta *\Content* para ser colocadas em cache para 15 dias.

Para obter mais informações sobre como configurar o `<clientCache>` elemento, consulte o artigo [Cache de cliente &lt;clientCache >](http://www.iis.net/configreference/system.webserver/staticcontent/clientcache).

[Servir conteúdo a partir de ações de controlador através de Azure CDN](#controller), posso também mostrar-lhe como pode configurar definições de cache para os resultados de ação de controlador na cache de CDN.

<a name="controller"></a>
## <a name="serve-content-from-controller-actions-through-azure-cdn"></a>Servir conteúdo a partir de ações de controlador através do Azure CDN ##

Ao integrar uma função de Web do serviço de nuvem Azure CDN, é relativamente fácil servir o conteúdo de acções do controlador através de CDN Azure. Diferente de servir o seu serviço de nuvem diretamente através da CDN Azure (demonstrado acima), [Martinho Balliauw](https://twitter.com/maartenballiauw) mostra-lhe como fazê-lo com um controlador de MemeGenerator [reduzir latência na web com a CDN Azure](http://channel9.msdn.com/events/TechDays/Techdays-2014-the-Netherlands/Reducing-latency-on-the-web-with-the-Windows-Azure-CDN)divertido. Posso irá simplesmente reproduza-lo aqui.

Suponha que no seu serviço de nuvem que pretende gerar memes com base numa jovens Chuck Norris imagem (fotografia pela [Guilherme luz](http://www.flickr.com/photos/alan-light/218493788/)) da seguinte forma:

![](media/cdn-cloud-service-with-cdn/cdn-5-memegenerator.PNG)

Tiver uma alteração simples `Index` ação que permite que os clientes especificar os superlativos na imagem, em seguida, gera a meme depois de publicar a ação. Uma vez que é Chuck Norris, seria esperado esta página para se tornar aumentar ou diminuir significativamente populares globalmente. Este é um bom exemplo de servir conteúdo dinâmico semiestruturados com Azure CDN.

Siga os passos acima para esta ação do controlador de configuração:

1. Na pasta *\Controllers* , crie um novo ficheiro. cs denominado *MemeGeneratorController.cs* e substituir o conteúdo com o código seguinte. Certifique-se de que substituir a parte realçada com o seu nome CDN.  

        using System;
        using System.Collections.Generic;
        using System.Diagnostics;
        using System.Drawing;
        using System.IO;
        using System.Net;
        using System.Web.Hosting;
        using System.Web.Mvc;
        using System.Web.UI;

        namespace WebRole1.Controllers
        {
            public class MemeGeneratorController : Controller
            {
                static readonly Dictionary<string, Tuple<string ,string>> Memes = new Dictionary<string, Tuple<string, string>>();

                public ActionResult Index()
                {
                    return View();
                }

                [HttpPost, ActionName("Index")]
                public ActionResult Index_Post(string top, string bottom)
                {
                    var identifier = Guid.NewGuid().ToString();
                    if (!Memes.ContainsKey(identifier))
                    {
                        Memes.Add(identifier, new Tuple<string, string>(top, bottom));
                    }

                    return Content("<a href=\"" + Url.Action("Show", new {id = identifier}) + "\">here's your meme</a>");
                }

                [OutputCache(VaryByParam = "*", Duration = 1, Location = OutputCacheLocation.Downstream)]
                public ActionResult Show(string id)
                {
                    Tuple<string, string> data = null;
                    if (!Memes.TryGetValue(id, out data))
                    {
                        return new HttpStatusCodeResult(HttpStatusCode.NotFound);
                    }

                    if (Debugger.IsAttached) // Preserve the debug experience
                    {
                        return Redirect(string.Format("/MemeGenerator/Generate?top={0}&bottom={1}", data.Item1, data.Item2));
                    }
                    else // Get content from Azure CDN
                    {
                        return Redirect(string.Format("http://<yourCdnName>.azureedge.net/MemeGenerator/Generate?top={0}&bottom={1}", data.Item1, data.Item2));
                    }
                }

                [OutputCache(VaryByParam = "*", Duration = 3600, Location = OutputCacheLocation.Downstream)]
                public ActionResult Generate(string top, string bottom)
                {
                    string imageFilePath = HostingEnvironment.MapPath("~/Content/chuck.bmp");
                    Bitmap bitmap = (Bitmap)Image.FromFile(imageFilePath);

                    using (Graphics graphics = Graphics.FromImage(bitmap))
                    {
                        SizeF size = new SizeF();
                        using (Font arialFont = FindBestFitFont(bitmap, graphics, top.ToUpperInvariant(), new Font("Arial Narrow", 100), out size))
                        {
                            graphics.DrawString(top.ToUpperInvariant(), arialFont, Brushes.White, new PointF(((bitmap.Width - size.Width) / 2), 10f));
                        }
                        using (Font arialFont = FindBestFitFont(bitmap, graphics, bottom.ToUpperInvariant(), new Font("Arial Narrow", 100), out size))
                        {
                            graphics.DrawString(bottom.ToUpperInvariant(), arialFont, Brushes.White, new PointF(((bitmap.Width - size.Width) / 2), bitmap.Height - 10f - arialFont.Height));
                        }
                    }

                    MemoryStream ms = new MemoryStream();
                    bitmap.Save(ms, System.Drawing.Imaging.ImageFormat.Png);
                    return File(ms.ToArray(), "image/png");
                }

                private Font FindBestFitFont(Image i, Graphics g, String text, Font font, out SizeF size)
                {
                    // Compute actual size, shrink if needed
                    while (true)
                    {
                        size = g.MeasureString(text, font);

                        // It fits, back out
                        if (size.Height < i.Height &&
                             size.Width < i.Width) { return font; }

                        // Try a smaller font (90% of old size)
                        Font oldFont = font;
                        font = new Font(font.Name, (float)(font.Size * .9), font.Style);
                        oldFont.Dispose();
                    }
                }
            }
        }

2. Com o botão direito na predefinida `Index()` acção e selecione **Adicionar vista**.

    ![](media/cdn-cloud-service-with-cdn/cdn-6-addview.PNG)

3.  Aceite as definições abaixo e clique em **Adicionar**.

    ![](media/cdn-cloud-service-with-cdn/cdn-7-configureview.PNG)

4. Abra o novo *Views\MemeGenerator\Index.cshtml* e substitua o conteúdo HTML seguinte simple para submeter os superlativos:

        <h2>Meme Generator</h2>

        <form action="" method="post">
            <input type="text" name="top" placeholder="Enter top text here" />
            <br />
            <input type="text" name="bottom" placeholder="Enter bottom text here" />
            <br />
            <input class="btn" type="submit" value="Generate meme" />
        </form>

5. Volte a publicar o serviço de nuvem e navegue para * *http://*&lt;serviceName >*.cloudapp.net/MemeGenerator/Index** no seu browser.

Quando submeter os valores de formulário para `/MemeGenerator/Index`, o `Index_Post` ação método devolve uma ligação para o `Show` método de ação com o respetivos identificador de entrada. Quando clicar na ligação, chegar o seguinte código:  

    [OutputCache(VaryByParam = "*", Duration = 1, Location = OutputCacheLocation.Downstream)]
    public ActionResult Show(string id)
    {
        Tuple<string, string> data = null;
        if (!Memes.TryGetValue(id, out data))
        {
            return new HttpStatusCodeResult(HttpStatusCode.NotFound);
        }

        if (Debugger.IsAttached) // Preserve the debug experience
        {
            return Redirect(string.Format("/MemeGenerator/Generate?top={0}&bottom={1}", data.Item1, data.Item2));
        }
        else // Get content from Azure CDN
        {
            return Redirect(string.Format("http://<yourCDNName>.azureedge.net/MemeGenerator/Generate?top={0}&bottom={1}", data.Item1, data.Item2));
        }
    }

Se estiver ligado a sua depurador local, em seguida, obterá a experiência de depuração normal com um redirecionamento local. Se estiver em execução no serviço de nuvem, em seguida, irá redirecioná para:

    http://<yourCDNName>.azureedge.net/MemeGenerator/Generate?top=<formInput>&bottom=<formInput>

Que corresponde ao seguinte URL de origem no seu ponto final CDN:

    http://<youCloudServiceName>.cloudapp.net/MemeGenerator/Generate?top=<formInput>&bottom=<formInput>


Em seguida, pode utilizar o `OutputCacheAttribute` atributo na `Generate` método para especificar como o resultado de ação deve ser colocadas em cache, que irão respeitar as Azure CDN. O código abaixo especifique uma expiração da cache de 1 hora (3600 segundos).

    [OutputCache(VaryByParam = "*", Duration = 3600, Location = OutputCacheLocation.Downstream)]

Da mesma forma, pode servir o conteúdo a partir de qualquer ação controlador no seu serviço de nuvem através do seu CDN Azure, com a opção de colocação em cache pretendida.

Na secção seguinte, posso irá mostrar-lhe como servir os scripts integrados e minified e o CSS através de Azure CDN.

<a name="bundling"></a>
## <a name="integrate-aspnet-bundling-and-minification-with-azure-cdn"></a>Integrar ASP.NET Unir e minification Azure CDN ##

Folhas de estilo de scripts e CSS altere com pouca frequência e são prime candidatos para a cache de Azure CDN. Servir toda a função Web através do seu CDN Azure é a forma mais fácil de integrar Unir e minification Azure CDN. No entanto, tal como não que poderá querer fazer isto, posso irá mostrar-lhe como fazê-lo enquanto preservar a experiência de develper pretendida dos ASP.NET Unir e minification, tais como:

-   Depurar excelente experiência de modo
-   Implementação simplificada
-   Atualizações de imediatas a clientes para actualizações de versão script/CSS
-   Mecanismo contingência quando o seu ponto final de CDN falha
-   Minimizar a modificação de código

No projeto **WebRole1** que criou no [integrar um ponto final Azure CDN com o Azure Web site e servir em conteúdo estático nas suas páginas Web a partir do Azure CDN](#deploy), abra *App_Start\BundleConfig.cs* e veja o `bundles.Add()` método chamadas.

    public static void RegisterBundles(BundleCollection bundles)
    {
        bundles.Add(new ScriptBundle("~/bundles/jquery").Include(
                    "~/Scripts/jquery-{version}.js"));
        ...
    }

O primeiro `bundles.Add()` declaração adiciona um pacote de script no diretório virtual `~/bundles/jquery`. Em seguida, abra *Views\Shared\_Layout.cshtml* para ver como a etiqueta do pacote de script é composta. Deverá conseguir localizar a linha de código do Razor seguinte:

    @Scripts.Render("~/bundles/jquery")

Quando este código Razor é executado na função Azure Web, serão compostas um `<script>` etiqueta para o pacote de script semelhante ao seguinte:

    <script src="/bundles/jquery?v=FVs3ACwOLIVInrAl5sdzR2jrCDmVOWFbZMY6g6Q0ulE1"></script>

No entanto, quando esta é executada no Visual Studio escrevendo `F5`, serão compostas cada ficheiro de script no pacote individualmente (neste caso, apenas um ficheiro de script está no pacote):

    <script src="/Scripts/jquery-1.10.2.js"></script>

Permite-lhe o código JavaScript no seu ambiente de desenvolvimento de depuração enquanto estiver reduzindo ligações de cliente em simultâneo (unir) e melhorar o ficheiro de transferência de desempenho (minification) de produção. É uma excelente funcionalidade preservar com a integração do Azure CDN. Além disso, uma vez que o pacote composta já contenha uma cadeia de versão gerados automaticamente, que pretende criar uma réplica essa funcionalidade, para que o sempre que atualiza a sua versão de jQuery através de NuGet, pode ser atualizado no lado do cliente mais cedo possível.

Siga os passos abaixo para unir ASP.NET de integração e minification com o seu ponto final de CDN.

1. Novamente na *App_Start\BundleConfig.cs*, modificar o `bundles.Add()` métodos para utilizar um diferentes [construtor do pacote](http://msdn.microsoft.com/library/jj646464.aspx), que especifica um endereço CDN. Para executar esta tarefa, substitua a `RegisterBundles` definição método com o seguinte código:  

        public static void RegisterBundles(BundleCollection bundles)
        {
            bundles.UseCdn = true;
            var version = System.Reflection.Assembly.GetAssembly(typeof(Controllers.HomeController))
                .GetName().Version.ToString();
            var cdnUrl = "http://<yourCDNName>.azureedge.net/{0}?v=" + version;

            bundles.Add(new ScriptBundle("~/bundles/jquery", string.Format(cdnUrl, "bundles/jquery")).Include(
                        "~/Scripts/jquery-{version}.js"));

            bundles.Add(new ScriptBundle("~/bundles/jqueryval", string.Format(cdnUrl, "bundles/jqueryval")).Include(
                        "~/Scripts/jquery.validate*"));

            // Use the development version of Modernizr to develop with and learn from. Then, when you're
            // ready for production, use the build tool at http://modernizr.com to pick only the tests you need.
            bundles.Add(new ScriptBundle("~/bundles/modernizr", string.Format(cdnUrl, "bundles/modernizer")).Include(
                        "~/Scripts/modernizr-*"));

            bundles.Add(new ScriptBundle("~/bundles/bootstrap", string.Format(cdnUrl, "bundles/bootstrap")).Include(
                        "~/Scripts/bootstrap.js",
                        "~/Scripts/respond.js"));

            bundles.Add(new StyleBundle("~/Content/css", string.Format(cdnUrl, "Content/css")).Include(
                        "~/Content/bootstrap.css",
                        "~/Content/site.css"));
        }

    Certifique-se de que substituir `<yourCDNName>` com o nome do seu CDN Azure.

    Em palavras simples, esteja a definir `bundles.UseCdn = true` e adicionado um URL de CDN redigida para cada pacote. Por exemplo, o construtor primeiro o código:

        new ScriptBundle("~/bundles/jquery", string.Format(cdnUrl, "bundles/jquery"))

    é o mesmo que:

        new ScriptBundle("~/bundles/jquery", string.Format(cdnUrl, "http://<yourCDNName>.azureedge.net/bundles/jquery?v=<W.X.Y.Z>"))

    Este construtor indica-ASP.NET Unir e minification para compor ficheiros de script individuais quando depurado localmente, mas utilizar o endereço CDN especificado para aceder ao script em questão. No entanto, tenha em atenção duas características importantes com este URL CDN redigida:

    -   A origem para este URL CDN é `http://<yourCloudService>.cloudapp.net/bundles/jquery?v=<W.X.Y.Z>`, que é realmente o directório virtual o pacote de script no seu serviço de nuvem.
    -   Uma vez que está a utilizar o construtor CDN, a etiqueta de script CDN para o pacote já não contém a cadeia da versão gerados automaticamente no URL composta. Tem de gerar manualmente uma cadeia de versão exclusivo sempre que o pacote de script é modificado para forçar uma falha de cache no seu CDN Azure. Ao mesmo tempo, esta cadeia de versão exclusivo tem que permaneçam constante através de vida de implementação para maximizar acertos na sua CDN Azure depois do pacote é implementada.
    -   A cadeia de consulta v = puxa < w.x.y. z > a partir do *Properties\AssemblyInfo.cs* no seu projeto de funções da Web. Pode ter um fluxo de trabalho de implementação que inclui incrementar a versão de assemblagem sempre que publicar no Azure. Ou, apenas pode modificar *Properties\AssemblyInfo.cs* no seu projeto para incrementar automaticamente a cadeia de versão sempre que criar, utilizar o caráter universal ' *'. Por exemplo:

            [assembly: AssemblyVersion("1.0.0.*")]

        Qualquer outra estratégia para simplificar a gerar uma cadeia exclusiva para a duração de uma implementação irá funcionar aqui.

3. Voltar a publicar o serviço de nuvem e aceder à home page.

4. Visualiza o código HTML para a página. Deverá conseguir ver o URL de CDN composto por uma cadeia de versão exclusivo sempre que publicar novamente as alterações no seu serviço de nuvem. Por exemplo:  

        ...

        <link href="http://camservice.azureedge.net/Content/css?v=1.0.0.25449" rel="stylesheet"/>

        <script src="http://camservice.azureedge.net/bundles/modernizer?v=1.0.0.25449"></script>

        ...

        <script src="http://camservice.azureedge.net/bundles/jquery?v=1.0.0.25449"></script>

        <script src="http://camservice.azureedge.net/bundles/bootstrap?v=1.0.0.25449"></script>

        ...

5. No Visual Studio, depurar o serviço de nuvem no Visual Studio escrevendo `F5`.,

6. Visualiza o código HTML para a página. Utilizador continua a ver cada ficheiro de script individualmente processado para que possa ter uma experiência no Visual Studio de depuração consistente.  

        ...

            <link href="/Content/bootstrap.css" rel="stylesheet"/>
        <link href="/Content/site.css" rel="stylesheet"/>

            <script src="/Scripts/modernizr-2.6.2.js"></script>

        ...

            <script src="/Scripts/jquery-1.10.2.js"></script>

            <script src="/Scripts/bootstrap.js"></script>
        <script src="/Scripts/respond.js"></script>

        ...   

<a name="fallback"></a>
## <a name="fallback-mechanism-for-cdn-urls"></a>Mecanismo contingência para URLs de CDN ##

Quando o seu ponto final Azure CDN falha por qualquer motivo, pretender que a página Web para ser inteligentes o suficiente para aceder ao seu servidor de Web origem como opção de contingência para o carregamento de arranque ou JavaScript. É grave perder imagens no seu Web site devido a indisponibilidade CDN, mas muito mais extremas para perder funcionalidade de página cruciais fornecida pelo seu scripts e folhas de estilo.

A classe de [pacote](http://msdn.microsoft.com/library/system.web.optimization.bundle.aspx) contém uma propriedade denominada [CdnFallbackExpression](http://msdn.microsoft.com/library/system.web.optimization.bundle.cdnfallbackexpression.aspx) permite-lhe configurar dispositivo contingência de falha CDN. Para utilizar esta propriedade, siga os passos abaixo:

1. No seu projeto de funções da Web, abra *App_Start\BundleConfig.cs*, onde adicionou um URL de CDN no cada [construtor do pacote](http://msdn.microsoft.com/library/jj646464.aspx)e efetuar as seguintes alterações realçadas para adicionar mecanismo contingência para os conjuntos de predefinido:  

        public static void RegisterBundles(BundleCollection bundles)
        {
            var version = System.Reflection.Assembly.GetAssembly(typeof(BundleConfig))
                .GetName().Version.ToString();
            var cdnUrl = "http://cdnurl.azureedge.net/.../{0}?" + version;
            bundles.UseCdn = true;

            bundles.Add(new ScriptBundle("~/bundles/jquery", string.Format(cdnUrl, "bundles/jquery"))
                        { CdnFallbackExpression = "window.jquery" }
                        .Include("~/Scripts/jquery-{version}.js"));

            bundles.Add(new ScriptBundle("~/bundles/jqueryval", string.Format(cdnUrl, "bundles/jqueryval"))
                        { CdnFallbackExpression = "$.validator" }
                        .Include("~/Scripts/jquery.validate*"));

            // Use the development version of Modernizr to develop with and learn from. Then, when you&#39;re
            // ready for production, use the build tool at http://modernizr.com to pick only the tests you need.
            bundles.Add(new ScriptBundle("~/bundles/modernizr", string.Format(cdnUrl, "bundles/modernizer"))
                        { CdnFallbackExpression = "window.Modernizr" }
                        .Include("~/Scripts/modernizr-*"));

            bundles.Add(new ScriptBundle("~/bundles/bootstrap", string.Format(cdnUrl, "bundles/bootstrap"))     
                        { CdnFallbackExpression = "$.fn.modal" }
                        .Include(
                                "~/Scripts/bootstrap.js",
                                "~/Scripts/respond.js"));

            bundles.Add(new StyleBundle("~/Content/css", string.Format(cdnUrl, "Content/css")).Include(
                        "~/Content/bootstrap.css",
                        "~/Content/site.css"));
        }

    Quando `CdnFallbackExpression` é não é nulo, script é inserido para o código HTML para verificar se o pacote é carregado com êxito e, caso não esteja, aceder o pacote diretamente a partir do servidor Web de origem. Esta propriedade tem de ser definidas como uma expressão de JavaScript que testa se o pacote CDN respetivos é carregado corretamente. Expressão necessário para testar a cada pacote difere o conteúdo. Para conjuntos predefinidos acima:

    -   `window.jquery`é definida na. js jquery-{versão}
    -   `$.validator`é definida na jquery.validate.js
    -   `window.Modernizr`é definida na. js modernizer-{versão}
    -   `$.fn.modal`é definida na bootstrap.js

    Poderá provavelmente, reparou não tiver definido CdnFallbackExpression para o `~/Cointent/css` pacote. Este é porque existe um [erro no System.Web.Optimization](https://aspnetoptimization.codeplex.com/workitem/104) injete atualmente um `<script>` etiqueta para o CSS de contingência em vez do esperado `<link>` etiqueta.

    No entanto, existe uma boa [Estilo pacote contingência](https://github.com/EmberConsultingGroup/StyleBundleFallback) oferecidos pelo [Ember Consulting grupo](https://github.com/EmberConsultingGroup).

2. Para utilizar a solução CSS, criar um novo ficheiro. cs na pasta de *App_Start* Web função do seu projeto denominada *StyleBundleExtensions.cs*e substituir o respetivo conteúdo com o [código de GitHub](https://github.com/EmberConsultingGroup/StyleBundleFallback/blob/master/Website/App_Start/StyleBundleExtensions.cs).

4. Na *App_Start\StyleFundleExtensions.cs*, mudar o nome do espaço de nomes para o nome da sua função Web (por exemplo, **WebRole1**).

3. Voltar para a `App_Start\BundleConfig.cs` e modificar o último `bundles.Add` declaração com o seguinte código realçado:  

        bundles.Add(new StyleBundle("~/Content/css", string.Format(cdnUrl, "Content/css"))
            <mark>.IncludeFallback("~/Content/css", "sr-only", "width", "1px")</mark>
            .Include(
                  "~/Content/bootstrap.css",
                  "~/Content/site.css"));

    Este novo método de extensão utiliza a mesma ideia para introduzir script no HTML para verificar o DOM para a um nome de classe correspondentes, o nome da regra e o valor de regra definida no pacote CSS e classifica o servidor Web de origem, se não conseguir encontrar a correspondência.

4. Volte a publicar o serviço de nuvem e aceder à home page.

5. Visualiza o código HTML para a página. Deve encontrar scripts introduzidos semelhante ao seguinte:    

        ...

        <link href="http://az632148.azureedge.net/Content/css?v=1.0.0.25474" rel="stylesheet"/>
        <script>(function() {
                        var loadFallback,
                            len = document.styleSheets.length;
                        for (var i = 0; i < len; i++) {
                            var sheet = document.styleSheets[i];
                            if (sheet.href.indexOf('http://camservice.azureedge.net/Content/css?v=1.0.0.25474') !== -1) {
                                var meta = document.createElement('meta');
                                meta.className = 'sr-only';
                                document.head.appendChild(meta);
                                var value = window.getComputedStyle(meta).getPropertyValue('width');
                                document.head.removeChild(meta);
                                if (value !== '1px') {
                                    document.write('<link href="/Content/css" rel="stylesheet" type="text/css" />');
                                }
                            }
                        }
                        return true;
                    }())||document.write('<script src="/Content/css"><\/script>');</script>

            <script src="http://camservice.azureedge.net/bundles/modernizer?v=1.0.0.25474"></script>
        <script>(window.Modernizr)||document.write('<script src="/bundles/modernizr"><\/script>');</script>

        ...

            <script src="http://camservice.azureedge.net/bundles/jquery?v=1.0.0.25474"></script>
        <script>(window.jquery)||document.write('<script src="/bundles/jquery"><\/script>');</script>

            <script src="http://camservice.azureedge.net/bundles/bootstrap?v=1.0.0.25474"></script>
        <script>($.fn.modal)||document.write('<script src="/bundles/bootstrap"><\/script>');</script>

        ...


    Repare que o script introduzido para o pacote CSS contém ainda a remnant com falhas a partir do `CdnFallbackExpression` propriedade na linha:

        }())||document.write('<script src="/Content/css"><\/script>');</script>

    Mas desde a primeira parte da | | expressão sempre irá devolver verdadeira (na linha diretamente acima dessa), a função document.write() nunca será executado.

## <a name="more-information"></a>Obter mais informações ##
- [Descrição geral da rede de entrega de conteúdos Azure (CDN)](http://msdn.microsoft.com/library/azure/ff919703.aspx)
- [Utilizar o Azure CDN](cdn-create-new-endpoint.md)
- [Unir ASP.NET e Minification](http://www.asp.net/mvc/tutorials/mvc-4/bundling-and-minification)



[new-cdn-profile]: ./media/cdn-cloud-service-with-cdn/cdn-new-profile.png
[cdn-profile-settings]: ./media/cdn-cloud-service-with-cdn/cdn-profile-settings.png
[cdn-new-endpoint-button]: ./media/cdn-cloud-service-with-cdn/cdn-new-endpoint-button.png
[cdn-add-endpoint]: ./media/cdn-cloud-service-with-cdn/cdn-add-endpoint.png
[cdn-endpoint-success]: ./media/cdn-cloud-service-with-cdn/cdn-endpoint-success.png
