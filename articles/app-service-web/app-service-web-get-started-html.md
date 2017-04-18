<properties 
    pageTitle="Implementar a aplicação web do primeira Azure dentro de cinco minutos | Microsoft Azure" 
    description="Saiba como é fácil executar aplicações web na aplicação de serviço ao implementar uma aplicação de exemplo. Iniciar a efetuar rapidamente desenvolvimento real e ver resultados imediatamente." 
    services="app-service\web"
    documentationCenter=""
    authors="cephalin"
    manager="wpickett"
    editor=""
/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="10/13/2016" 
    ms.author="cephalin"
/>
    
# <a name="deploy-your-first-web-app-to-azure-in-five-minutes"></a>Implementar a aplicação web do primeira Azure dentro de cinco minutos

Neste tutorial ajuda-o a implementar uma aplicação de web HTML + CSS simple para a [Aplicação de serviço de Azure](../app-service/app-service-value-prop-what-is.md).
Pode utilizar a aplicação de serviço para criar aplicações web, [aplicação móvel trás extremidades](/documentation/learning-paths/appservice-mobileapps/)e [API apps](../app-service-api/app-service-api-apps-why-best-platform.md).

Irá: 

- Crie uma aplicação web na aplicação de serviço de Azure.
- Implemente HTML e CSS-lo.
- Consulte as páginas a executar o live de produção.
- Atualização do conteúdo da mesma forma que faria [push que Git consolida](https://git-scm.com/docs/git-push).

## <a name="prerequisites"></a>Pré-requisitos

- [Git](http://www.git-scm.com/downloads).
- [Clip azure](../xplat-cli-install.md).
- Uma conta do Microsoft Azure. Se não tiver uma conta, pode [Inscrever-se para uma avaliação gratuita](/pricing/free-trial/?WT.mc_id=A261C142F) ou [Ativar os benefícios da sua Visual Studio subscritor](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F).

>[AZURE.NOTE] Pode [Tentar aplicação de serviço de](http://go.microsoft.com/fwlink/?LinkId=523751) sem uma conta Azure. Criar uma aplicação starter e reproduzir com o mesmo para até uma hora – sem cartão de crédito obrigatório, sem compromissos.

## <a name="deploy-a-simple-html-site"></a>Implementar um site HTML simples

1. Abra uma nova linha de comandos do Windows, janela PowerShell, Linux shell ou terminal dos X. Executar `git --version` e `azure --version` para verificar que Git e Azure clip estão instalados no seu computador.

    ![Testar a instalação das ferramentas de clip para a sua aplicação web primeira no Azure](./media/app-service-web-get-started/1-test-tools.png)

    Se ainda não tiver instalado as ferramentas, consulte [Pré-requisitos](#Prerequisites) para ligações de transferência.

3. Iniciar sessão no Azure da seguinte forma:

        azure login

    Seguir a mensagem de ajuda para continuar o processo de início de sessão.

    ![Iniciar sessão no Azure para criar a aplicação web do primeira](./media/app-service-web-get-started/3-azure-login.png)

4. Alterar o Azure clip no modo ASM, em seguida, definir o utilizador de implementação para a aplicação de serviço. Irá implementar código utilizando as credenciais mais tarde.

        azure config mode asm
        azure site deployment user set --username <username> --pass <password>

1. Alterar para um diretório de trabalho (`CD`) e clonar a aplicação de exemplo da seguinte forma:

        git clone https://github.com/Azure-Samples/app-service-web-html-get-started.git

2. Alterar para o repositório da sua aplicação de exemplo. 

        cd app-service-web-html-get-started

4. Crie o recurso de aplicação de serviço de aplicação no Azure com um nome de aplicação exclusiva e o utilizador de implementação que configurou anteriormente. Quando lhe for pedido, especifique o número da região pretendida.

        azure site create <app_name> --git --gitusername <username>

    ![Criar o recurso Azure para a sua aplicação web primeiro no Azure](./media/app-service-web-get-started/4-create-site.png)

    A aplicação é criada no Azure agora. Além disso, o seu diretório atual é inicializado Git e ligada para a nova aplicação de serviço de aplicação como um Git remoto.
    Pode navegar para o URL da aplicação (http://&lt;nome_apl >. azurewebsites.net) para ver a página HTML maravilhosas predefinida, mas vamos realmente obter o código existem agora.

4. Implemente o código de exemplo para a sua aplicação Azure, como seria de emissão qualquer código com Git. Quando lhe for pedido, utilize a palavra-passe que configurou anteriormente.

        git push azure master

    ![Código de emissão para a sua aplicação web primeira no Azure](./media/app-service-web-get-started/5-push-code.png)

    Se utilizou os quadros de idioma, verá saída diferente. Isto acontece porque `git push` não só coloca código no Azure, mas também accionadores tarefas de implementação do motor de implementação. Se tiver qualquer package.json (Node.js) ou requirements.txt (Python) ficheiros no seu raiz de projeto (repositório) ou se tiver um ficheiro de packages.config no projeto ASP.NET, o script de implementação restaura os pacotes necessários para si. Também pode [Ativar a extensão de compositor](web-sites-php-mysql-deploy-use-git.md#composer) para processar automaticamente os ficheiros de composer.json na sua aplicação PHP.

Parabéns, ter implementado a aplicação para a aplicação de serviço de Azure.

## <a name="see-your-app-running-live"></a>Ver a sua aplicação a executar o live

Para ver a sua aplicação a executar o live no Azure, execute este comando a partir de qualquer directório no seu repositório:

    azure site browse

## <a name="make-updates-to-your-app"></a>Fazer atualizações para a sua aplicação

Agora pode usar Git para transmitir a partir do seu projeto (repositório) de raiz qualquer momento para efetuar uma atualização para o site direto. Pode fazê-lo da mesma forma que quando implementado o seu código pela primeira vez. Por exemplo, sempre que pretender emissão uma alteração de nova testámos localmente, basta execute os seguintes comandos a partir do seu raiz de projeto (repositório):

    git add .
    git commit -m "<your_message>"
    git push azure master

## <a name="next-steps"></a>Próximos passos

Localize os passos de desenvolvimento e implementação preferidos para o seu framework idioma:

> [AZURE.SELECTOR]
- [.NET](web-sites-dotnet-get-started.md)
- [PHP](app-service-web-php-get-started.md)
- [NODE.js](app-service-web-nodejs-get-started.md)
- [Python](web-sites-python-ptvs-django-mysql.md)
- [Java](web-sites-java-get-started.md)

Em alternativa, largas à imaginação com a aplicação web do primeira. Por exemplo:

- Experimente [outras formas para implementar o seu código para Azure](../app-service-web/web-sites-deploy.md). Por exemplo, para implementar a partir de um dos seus repositórios GitHub, basta selecione **GitHub** em vez de **Local Git repositório** nas **Opções de implementação**.
- Tomar a sua aplicação Azure para o próximo nível. Autenticar os seus utilizadores. Escala com base na pedido. Configure o alguns alertas de desempenho. Tudo com alguns cliques. Consulte o artigo [Adicionar funcionalidade para a sua aplicação web primeira](app-service-web-get-started-2.md).

