<properties 
    pageTitle="Erro durante a deteção de autenticação" 
    description="O Assistente de ligação do active directory detetado um tipo de autenticação incompatível" 
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

# <a name="error-during-authentication-detection"></a>Erro durante a deteção de autenticação

Enquanto detetar o código de autenticação de anterior, o assistente detetado um tipo de autenticação incompatíveis estão incluídos.   

##<a name="what-is-being-checked"></a>O que está a ser selecionado?

**Nota:** Para poder detetar corretamente o código de autenticação de anterior num projeto, o projeto têm de ser formado.  Caso se tenha deparado ou este erro e não tiver um código de autenticação anterior no seu projeto, reconstruir e tente novamente.

###<a name="project-types"></a>Tipos de projecto

O assistente verifica que tipo de projeto que está a desenvolver, de modo que pode inserção a lógica de autenticação à direita no projeto.  Se existir qualquer controlador que deriva da `ApiController` no project, o projeto será considerado um projeto WebAPI.  Se existirem apenas os controladores derivam `MVC.Controller` no project, o projeto será considerado um projeto MVC.  Mais nada não é suportado pelo assistente.  Projetos de Web Forms atualmente não são suportados.

###<a name="compatible-authentication-code"></a>Código de autenticação compatíveis

O assistente também verifica se existem definições de autenticação que tem sido previamente configuradas com o assistente ou são compatíveis com o assistente.  Se todas as definições estão presentes, considera-se um caso reentrante e o assistente irá abrir e apresentar as definições.  Se apenas algumas das definições estão presentes, é considerada um caso de erro.

Num projecto MVC, o assistente verifica para qualquer uma das seguintes definições, resultam da utilização anterior do assistente:

    <add key="ida:ClientId" value="" />
    <add key="ida:Tenant" value="" />
    <add key="ida:AADInstance" value="" />
    <add key="ida:PostLogoutRedirectUri" value="" />

Além disso, o assistente verifica para qualquer uma das seguintes definições num projeto Web API, resultam da utilização anterior do assistente:

    <add key="ida:ClientId" value="" />
    <add key="ida:Tenant" value="" />
    <add key="ida:Audience" value="" />

###<a name="incompatible-authentication-code"></a>Código de autenticação incompatível

Por fim, o Assistente de tentativas de detetar versões do código de autenticação que tenham sido configuradas com versões anteriores do Visual Studio. Se tiver recebido este erro, significa que o projeto contém um tipo de autenticação incompatíveis estão incluídos. O assistente Deteta os seguintes tipos de autenticação a partir de versões anteriores do Visual Studio:

* Autenticação do Windows 
* Contas de utilizador individual 
* Contas organizacionais 
 

Para detetar autenticação do Windows num projeto MVC, o Assistente de procura, o `authentication` elemento do seu ficheiro de **Web. config** .

<pre>
    &lt;configuração&gt;
        &lt;System. Web&gt;
            <span style="background-color: yellow">&lt;modo de autenticação = "Windows" /&gt;</span>
        &lt;/system.web&gt;
    &lt;/configuration&gt;
</pre>

Para detetar autenticação do Windows num projeto Web API, o Assistente de procura, o `IISExpressWindowsAuthentication` elemento a partir de ficheiro do seu projeto **csproj** :

<pre>
    &lt;Project&gt;
&lt;PropertyGroup&gt;
            <span style="background-color: yellow">&lt;IISExpressWindowsAuthentication&gt;ativado&lt;/IISExpressWindowsAuthentication&gt;</span>
        &lt;/PropertyGroup > &lt;/do Project        &gt;
</pre>

Para detetar autenticação de contas de utilizador Individual, o Assistente de procura no elemento de pacote a partir do seu ficheiro **Packages.config** .

<pre>
    &lt;pacotes&gt;
        <span style="background-color: yellow">&lt;compactar id="Microsoft.AspNet.Identity.EntityFramework" versão = "2.1.0" targetFramework = "net45" /&gt;</span>
    &lt;/pacotes&gt;
</pre>

Para detetar um formulário de autenticação de conta institucional antigo, o Assistente de procura o elemento seguinte a partir da **Web. config**:

<pre>
    &lt;configuração&gt;
        &lt;appSettings&gt;
            <span style="background-color: yellow">&lt;Adicionar chave = valor de "ida: Realm" = "* * *" /&gt;</span>
        &lt;/appSettings&gt;
    &lt;/configuration&gt;
</pre>

Para alterar o tipo de autenticação, remova o tipo de autenticação incompatível e execute novamente o assistente.

Para mais informações, consulte o artigo [Cenários de autenticação de Azure AD](active-directory-authentication-scenarios.md).