<properties 
    pageTitle="Introdução com o Azure Active Directory e serviços de Visual Studio ligado (projectos MVC) | Microsoft Azure" 
    description="Como começar a utilizar o Azure Active Directory em projetos MVC depois liga à ou criar um Azure AD utilizando o Visual Studio ligado serviços" 
    services="active-directory" 
    documentationCenter="" 
    authors="TomArcher" 
    manager="douge" 
    editor=""/>
  
<tags 
    ms.service="active-directory" 
    ms.workload="web" 
    ms.tgt_pltfrm="vs-getting-started" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="08/15/2016" 
    ms.author="tarcher"/>

# <a name="getting-started-with-azure-active-directory-and-visual-studio-connected-services-mvc-projects"></a>Introdução ao Azure Active Directory e do Visual Studio ligado serviços (MVC projetos)

> [AZURE.SELECTOR]
> - [Introdução](vs-active-directory-dotnet-getting-started.md)
> - [O que aconteceu](vs-active-directory-dotnet-what-happened.md)
 
##<a name="requiring-authentication-to-access-controllers"></a>Exigir autenticação para controladores do access 

Todos os controladores do seu projeto foram adorned com o atributo **autorizar** . Este atributo vai requerer que o utilizador autenticado antes de aceder a estes controladores. Para permitir que o controlador de ser acedido de forma anónima, remova este atributo controlador de. Se pretende definir as permissões num nível mais granular, aplique o atributo para cada método que requer a autorização em vez de aplicá-lo para a classe de controlador.
 
##<a name="adding-signin--signout-controls"></a>Adicionar o início de sessão do / encerrar controlos 

Para adicionar um controlos de início de sessão/encerrar à sua vista, pode utilizar a vista parcial **_LoginPartial.cshtml** para adicionar a funcionalidade para uma das suas vistas. Eis um exemplo de funcionalidade adicionada à vista padrão **_Layout.cshtml** . (Tenha em atenção o último elemento de div com fechar barra de navegação de classe):

<pre>
    &lt;! DOCTYPE html&gt; 
&lt;html&gt; 
&lt;head&gt; 
&lt;meta charset="utf-8" /&gt; 
&lt;meta name="viewport" content="width=device-width, initial-scale=1.0"&gt; 
&lt;title&gt;@ViewBag.Title - My ASP.NET Application&lt;/title&gt; 
@Styles.Render("~/Content/css") @Scripts.Render("~/bundles/modernizr") &lt;/head&gt; 
&lt;body&gt; 
&lt;div class="navbar navbar-inverse navbar-fixed-top"&gt; 
&lt;div class="container"&gt; 
&lt;div class="navbar-header"&gt; 
&lt;button type="button" class="navbar-toggle" data-toggle="collapse" data-target=".navbar-collapse"&gt; 
&lt;span class="icon-bar"&gt;&lt;/span&gt; 
&lt;span class="icon-bar"&gt;&lt;/span&gt; 
&lt;span class="icon-bar"&gt;&lt;/span&gt; 
&lt;/button&gt; 
@Html.ActionLink("Application name", "Index", "Home", new { area = "" }, new { @class = "navbar-brand" }) &lt;/div&gt; 
&lt;div class="navbar-collapse collapse"&gt; 
&lt;ul class="nav navbar-nav"&gt; 
&lt;li&gt;@Html.ActionLink("Home", "Index", "Home")&lt;/li&gt; 
&lt;li&gt;@Html.ActionLink("About", "About", "Home")&lt;/li&gt; 
&lt;li&gt;@Html.ActionLink("Contact", "Contact", "Home")&lt;/li&gt; 
&lt;/ul&gt; 
                    <span style="background-color:yellow">@Html.Partial("_LoginPartial")</span> 
                &lt;/ div&gt; 
&lt;/div&gt; 
&lt;/div&gt; 
&lt;div class="container body-content"&gt; 
@RenderBody() &lt;hr /&gt; 
&lt;footer&gt; 
&lt;p&gt;&amp;copy; @DateTime.Now.Year - My ASP.NET Application&lt;/p&gt; 
&lt;/footer&gt; 
&lt;/div&gt; 
@Scripts.Render("~/bundles/jquery") @Scripts.Render("~/bundles/bootstrap") @RenderSection("scripts", required: false) &lt;/body&gt; 
&lt;/html                                                                                                                                                                                                                                                                                                                                                                                                                                                           &gt;
</pre>

[Saiba mais sobre o Azure Active Directory](https://azure.microsoft.com/services/active-directory/) 
