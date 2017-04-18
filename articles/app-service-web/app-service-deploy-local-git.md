<properties
    pageTitle="Implementação de local Git Azure de aplicação de serviço"
    description="Saiba como ativar local Git implementação para a aplicação de serviço de Azure."
    services="app-service"
    documentationCenter=""
    authors="dariagrigoriu"
    manager="wpickett"
    editor="mollybos"/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/13/2016"
    ms.author="dariagrigoriu"/>
    
# <a name="local-git-deployment-to-azure-app-service"></a>Implementação de local Git Azure de aplicação de serviço

Este tutorial mostra como implemente a sua aplicação para a [Aplicação de serviço de Azure] a partir de um repositório de Git no seu computador local. Aplicação de serviço suporta esta abordagem com a opção de implementação **Local Git** no [Portal do Azure].  
Muitos dos comandos Git descritos neste artigo são executados automaticamente quando criar uma aplicação de serviço de aplicação utilizando a [Interface de comandos do Azure] como descrito [aqui](app-service-web-get-started.md).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este tutorial, é necessário:

- Git. Pode transferir a instalação binário [aqui](http://www.git-scm.com/downloads).  
- Conhecimento básico de Git.
- Uma conta do Microsoft Azure. Se não tiver uma conta, pode [Inscrever-se para uma avaliação gratuita](https://azure.microsoft.com/pricing/free-trial) ou [Ativar os benefícios da sua Visual Studio subscritor](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details).

>[AZURE.NOTE] Se pretender começar a aplicação de serviço de Azure antes de inscrever-se para uma conta do Azure, aceda ao [Tentar aplicação de serviço](http://go.microsoft.com/fwlink/?LinkId=523751), onde imediatamente pode criar uma aplicação de starter curto na aplicação de serviço. Sem cartões de crédito necessários; sem compromissos.  

## <a name="Step1"></a>Passo 1: Criar um repositório local

Efetue as seguintes tarefas para criar um novo repositório de Git.

1. Inicie uma ferramenta de linha de comandos, tal como **GitBash** (Windows) ou **festa** (Shell de Unix). Em sistemas dos X pode aceder a linha de comandos através da aplicação de **Terminal** .

2. Navegue para o diretório onde o conteúdo para implementar esteja localizado.

3. Utilize o seguinte comando para iniciar um repositório de Git novo:

        git init

## <a name="Step2"></a>Passo 2: Consolidar o seu conteúdo

Aplicação de serviço suporta a aplicações criadas numa variedade de linguagens de programação. 

1. Se o seu repositório já inclui conteúdo ignorar esta aponte e deslocar-se ao ponto 2 abaixo. Se o seu repositório não já inclui conteúdo basta preencha com um ficheiro. HTML estático da seguinte forma: 

    - Utilizando um editor de texto, crie um novo ficheiro chamado **Index** na raiz do repositório Git
    - Adicionar o seguinte texto, como o conteúdo para o Index de ficheiros e guardá-la: *Olá Git!*
        
2. A partir da linha de comandos, certifique-se de que se encontra da raiz da sua repositório Git. Em seguida, utilize o seguinte comando para adicionar ficheiros ao seu repositório:

        git add -A 

4. Em seguida, consolide as alterações para o repositório utilizando o seguinte comando:

        git commit -m "Hello Azure App Service"

## <a name="Step3"></a>Passo 3: Ativar o repositório de aplicação de serviço de aplicação

Execute os passos seguintes para ativar um repositório de Git para a sua aplicação de serviço de aplicação.

1. Inicie a sessão [Portal do Azure].

2. No pá da sua aplicação de serviço de aplicação, clique em **Definições > origem de implementação**. Clique em **Escolher origem**, em seguida, clique em **Locais Git repositório**e, em seguida, clique em **OK**.  

    ![Repositório de Git local](./media/app-service-deploy-local-git/local_git_selection.png)

3. Se esta é a sua definição de horas primeira para cima um repositório no Azure, terá de criar credenciais de início de sessão para a mesma. Irá utilizá-los para iniciar sessão para as alterações de repositório e push Azure a partir do seu local repositório de Git. A partir de pá da sua aplicação, clique em **Definições > credenciais de implementação**, em seguida, configure a sua implementação nome de utilizador e palavra-passe. Quando tiver terminado, clique em **Guardar**.

    ![](./media/app-service-deploy-local-git/deployment_credentials.png)

## <a name="Step4"></a>Passo 4: Implementar o projeto

Utilize os passos seguintes para publicar a sua aplicação para a aplicação de serviço utilizando Local Git.

1. No pá da sua aplicação no Portal do Azure, clique em **Definições > propriedades** para o **URL de Git**.

    ![](./media/app-service-deploy-local-git/git_url.png)

    **URL de Git** é a referência para implementar a partir do seu local repositório remota. Irá utilizar este URL nos passos seguintes.

2. Com a linha de comandos, verifique que se encontra na raiz da sua repositório Git local.

3. Utilizar `git remote` adicionar a referência remota listado no **Git URL** a partir do passo 1. O seu comando terá uma aspeto semelhante ao seguinte:

        git remote add azure https://<username>@localgitdeployment.scm.azurewebsites.net:443/localgitdeployment.git         
    > [AZURE.NOTE] O comando **remoto** adiciona uma referência com nome a um repositório remoto. Neste exemplo, que cria uma referência com o nome 'azure' repositório do web app.

4. O conteúdo de emissão ao serviço de aplicação utilizando a nova **azure** remoto que acabou de criar.

        git push azure master

    Será pedido para a palavra-passe que criou anteriormente quando repor as suas credenciais implementação no Portal do Azure. Introduza a palavra-passe (tenha em atenção que Gitbash não eco asteriscos na consola à medida que escreve a palavra-passe). 
       
5. Volte para a sua aplicação no Portal do Azure. Uma entrada de registo do seu push mais recente deve ser apresentada na pá **híbridas** . 

    ![](./media/app-service-deploy-local-git/deployment_history.png)

6. Clique no botão **Procurar** na parte superior da pá a aplicação para verificar que o conteúdo foi implementado. 
    
## <a name="Step5"></a>Resolução de problemas

Seguem-se erros ou problemas frequentemente encontrados quando utilizar Git para publicar uma aplicação de serviço de aplicação no Azure:

****

**Sintoma**: não é possível acesso '[siteURL]': Falha ao ligar ao [scmAddress]

**Problema**: Este erro pode ocorrer se a aplicação não estiver a trabalhar.

**Resolução**: iniciar a aplicação no Portal do Azure. Implementação de Git não funcionar, a menos que a aplicação está a ser executado. 


****

**Sintoma**: não foi possível resolver anfitrião 'hostname'

**Problema**: Este erro pode ocorrer se as informações do endereço introduzidas quando criar o azure remoto estavam incorretas.

**Resolução**: Utilize o `git remote -v` comando para listar todos os controlos remotos, juntamente com o URL associado. Certifique-se de que o URL do 'azure' remoto está correto. Se for necessário, remova e recrie este remoto utilizando o URL correto.

****

**Sintoma**: sem refs em comum e nenhum especificada; fazer nada. Talvez deverá especificar um ramo como 'modelo global de'.

**Problema**: Este erro pode ocorrer se não especificar um ramo quando efetuar uma git operação de emissão e não tiver definido o valor de push.default utilizado pelo Git.

**Resolução**: executar a operação de push novamente, especificando o ramo principal. Por exemplo:

    git push azure master

****

**Sintoma**: src refspec [branchname] não corresponde a nenhum.

**Problema**: Este erro pode ocorrer se tentar notificações push para um ramo diferente de modelo global no azure remoto.

**Resolução**: executar a operação de push novamente, especificando o ramo principal. Por exemplo:

    git push azure master

****

**Sintoma**: erro - alterações consolidadas para o repositório remoto, mas a aplicação web do não atualizados.

**Problema**: Este erro pode ocorrer se estiver a implementar uma aplicação de Node.js que contém um ficheiro de package.json que especifica módulos necessários adicionais.

**Resolução**: adicionais mensagens que contenham 'npm erro'! deve ser arquivado antes deste erro e pode fornecer contexto adicional sobre a falha. Seguem-se conhecidos as causas deste erro e o correspondente 'npm erro!' mensagem:

* **Ficheiro de package.json mal**: npm erro! Não foi leia dependências.

* **Módulo nativo que não tem uma distribuição binária para o Windows**:

    * npm erro! \`cmd "/ c" "nó gyp reconstruir"\` Ocorreu uma falha com 1

        OU

    * npm erro! [modulename@version]pré-instalar: \`tornar | | gmake\`


## <a name="additional-resources"></a>Recursos adicionais

* [Documentação Git](http://git-scm.com/documentation)
* [Documentação Kudu do Project](https://github.com/projectkudu/kudu/wiki)
* [Implementação de contínuo Azure de aplicação de serviço](app-service-continuous-deployment.md)
* [Como utilizar o PowerShell para Azure](../powershell-install-configure.md)
* [Como utilizar a Interface de comandos do Azure](../xplat-cli-install.md)

[Azure de aplicação de serviço]: https://azure.microsoft.com/documentation/articles/app-service-changes-existing-services/
[Azure Developer Center]: http://www.windowsazure.com/en-us/develop/overview/
[Portal do Azure]: https://portal.azure.com
[Git website]: http://git-scm.com
[Installing Git]: http://git-scm.com/book/en/Getting-Started-Installing-Git
[Interface de comandos Azure]: https://azure.microsoft.com/en-us/documentation/articles/xplat-cli-azure-resource-manager/

[Using Git with CodePlex]: http://codeplex.codeplex.com/wikipage?title=Using%20Git%20with%20CodePlex&referringTitle=Source%20control%20clients&ProjectName=codeplex
[Quick Start - Mercurial]: http://mercurial.selenic.com/wiki/QuickStart
