<properties
    pageTitle="Trabalhar com Node.js módulos"
    description="Saiba como trabalhar com Node.js módulos ao utilizar a aplicação de serviço de Azure ou os serviços em nuvem."
    services=""
    documentationCenter="nodejs"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="nodejs"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="robmcm"/>


# <a name="using-nodejs-modules-with-azure-applications"></a>Utilização Node.js módulos com aplicações do Azure

Este artigo fornece orientações sobre como utilizar Node.js módulos com aplicações alojadas no Azure. Fornece orientações sobre garantir que a sua aplicação utiliza uma versão específica do módulo, bem como utilizar módulos nativos com o Azure.

Se já estiver familiarizado com utilizar Node.js módulos, ficheiros **package.json** e **npm shrinkwrap.json** , o seguinte é um resumo rápido do que é abordado neste artigo:

* Azure de aplicação de serviço compreende ficheiros **package.json** e **npm shrinkwrap.json** e pode instalar módulos com base nas entradas destes ficheiros.
* Azure serviços em nuvem esperar todos os módulos de ser instalada no ambiente de desenvolvimento bem como a **nó\_módulos** directory para ser incluídos como parte do pacote de implementação do. É possível activar o suporte para instalar o módulos que utilizem **package.json** ou **npm shrinkwrap.json** ficheiros em serviços em nuvem, no entanto, isto requer que personalização dos scripts predefinido utilizado pelo projetos de serviço na nuvem. Para um exemplo de como realizar esta tarefa, consulte o artigo [tarefa de arranque do Azure para ser executada npm instalar para evitar implementar módulos nó](https://github.com/woloski/nodeonazure-blog/blob/master/articles/startup-task-to-run-npm-in-azure.markdown)

> [AZURE.NOTE] Azure máquinas virtuais não são abordados neste artigo, como a experiência de implementação uma VM será dependente no sistema operativo alojado pela máquina Virtual.

##<a name="nodejs-modules"></a>Módulos node.js

Os módulos são deslastráveis pacotes JavaScript que fornecem funcionalidades específicas para a sua aplicação. Um módulo é normalmente instalado utilizando a ferramenta de linha de comandos **npm** , no entanto, algumas (como o módulo de http) são fornecidas como parte do pacote Node.js core.

Quando são instalados módulos, estes são armazenados na **nó\_módulos** directório na raiz da sua estrutura do directório de aplicação. Cada módulo dentro de **nó\_módulos** directory mantém as suas próprias **nó\_módulos** directório que contém todos os módulos que depende e este atalho repete novamente para cada módulo tudo para baixo da cadeia de dependência. Esta opção permite-cada módulo instalado para ter os suas próprias requisitos de versão para os módulos depende, no entanto pode resultar bastante numa estrutura de directório grande.

Quando implementar o **nó\_módulos** diretório como parte da sua aplicação,-aumenta o tamanho de implementação relativamente à utilização de um ficheiro **package.json** ou **npm shrinkwrap.json** ; No entanto, garante que a versão do módulos utilizados na produção são idênticos utilizadas no desenvolvimento de.

###<a name="native-modules"></a>Módulos nativos

Enquanto a maior parte dos módulos são ficheiros de JavaScript simplesmente de texto simples, alguns módulos são imagens binárias específicos da plataforma. Estes módulos são compilados em altura da instalação, normalmente utilizando Python e nó gyp. Uma vez que serviços em nuvem Azure depender de **nó\_módulos** pasta a ser implementada como parte do módulo liquidação, qualquer nativo incluído como parte dos módulos instalados deverá funcionar num serviço de nuvem, desde que foi instalado e compilado num sistema de desenvolvimento do Windows.

Azure de aplicação de serviço não suporta todos os módulos nativos e poderá falhar ao compilar aqueles com a pré-requisitos muito específicas. Enquanto alguns módulos populares como MongoDB têm dependências nativas opcionais e trabalho perfeitamente sem-las, duas soluções provar efetuada com êxito com quase todos os módulos nativos hoje disponíveis:

* Execute **npm instalar** num computador que tem instaladas pré-requisitos do todos os nativo módulo Windows. Em seguida, implementar a criada **nó\_módulos** pasta como parte da aplicação a aplicação de serviço de Azure.
* Azure de aplicação de serviço pode ser configurada para executar festa personalizada ou scripts da shell durante a implementação, fornecendo-a oportunidade de executar comandos personalizados e configurar de forma precisa a forma como **instalar npm** está a ser executada. Para um vídeo que mostra como fazer isto, consulte o artigo [Scripts de implementação do Web site personalizado com o Kudu].

###<a name="using-a-packagejson-file"></a>Com um ficheiro de package.json

O ficheiro **package.json** é uma forma para especificar as dependências de nível superior a sua aplicação requer para que a plataforma de alojamento possa instalar as dependências, em vez de que seja necessário incluir a **nó\_pacotes** pasta como parte da implementação. Depois de ter sido implementada a aplicação, o comando **npm instalar** é utilizado para analisar o ficheiro **package.json** e instale todas as dependências listadas.

Durante o desenvolvimento, pode utilizar o **– Guardar**, **– Guardar dev**, ou **– opcional de guardar** parâmetros ao instalar o módulos para adicionar uma entrada para o módulo ao seu ficheiro **package.json** automaticamente. Para mais informações, consulte o artigo [instalar npm](https://docs.npmjs.com/cli/install).

Um potencial problema com o ficheiro **package.json** é que apenas Especifica a versão para dependências de nível superior. Cada módulo instalado pode ou não pode especificar a versão do módulos depende e, por isso, é possível que poderá acabar com uma cadeia de dependência diferente daquele utilizadas no desenvolvimento de.

> [AZURE.NOTE]
> Quando implementar o serviço de aplicação do Azure, se o seu ficheiro <b>package.json</b> referencia um módulo nativo irá ver um erro semelhante ao seguinte quando a aplicação utilizando Git de publicação:

>       npm ERR! module-name@0.6.0 install: 'node-gyp configure build'

>       npm ERR! 'cmd "/c" "node-gyp configure build"' failed with 1


###<a name="using-a-npm-shrinkwrapjson-file"></a>Com um ficheiro de npm shrinkwrap.json

O ficheiro **npm shrinkwrap.json** é uma tentativa para responder às limitações de controlo de versões de módulo do ficheiro **package.json** . Enquanto o ficheiro **package.json** inclui apenas versões para os módulos de nível superior, o ficheiro de **npm shrinkwrap.json** contém os requisitos de versão para a cadeia de dependência de módulo completo.

Quando a aplicação estiver pronta para produção, pode bloquear-para baixo para requisitos de versão e criar um ficheiro de **npm shrinkwrap.json** utilizando o comando **npm shrinkwrap** . Isto irá utilizar as versões atualmente instaladas na **nó\_módulos** pasta e que grave os para o ficheiro **npm shrinkwrap.json** . Depois de ter sido implementada a aplicação para o ambiente de alojamento, o comando **npm instalar** é utilizado para analisar o ficheiro **npm shrinkwrap.json** e instale todas as dependências listadas. Para mais informações, consulte o artigo [npm shrinkwrap](https://docs.npmjs.com/cli/shrinkwrap).

> [AZURE.NOTE]
>Quando implementar o serviço de aplicação do Azure, se o seu ficheiro <b>npm shrinkwrap.json</b> referencia um módulo nativo irá ver um erro semelhante ao seguinte quando a aplicação utilizando Git de publicação:

>       npm ERR! module-name@0.6.0 install: 'node-gyp configure build'

>       npm ERR! 'cmd "/c" "node-gyp configure build"' failed with 1


##<a name="next-steps"></a>Próximos passos

Agora que a compreender como utilizar Node.js módulos com Azure, saiba como [especificar a versão de Node.js], [criar e implementar uma aplicação web do Node.js]e [como utilizar a Interface de comandos do Azure para Mac e o Linux].

Para mais informações, consulte o [Centro de programadores do Node.js](/develop/nodejs/).

[especificar a versão de Node.js]: nodejs-specify-node-version-azure-apps.md
[Como utilizar a Interface de comandos do Azure para Mac e o Linux]: xplat-cli-install.md
[criar e implementar uma aplicação web do Node.js]: web-sites-nodejs-develop-deploy-mac.md
[Node.js Web Application with Storage on MongoDB (MongoLab)]: store-mongolab-web-sites-nodejs-store-data-mongodb.md
[Build and deploy a Node.js application to an Azure Cloud Service]: cloud-services-nodejs-develop-deploy-app.md
[Scripts de implementação do Web site personalizado com Kudu]: /documentation/videos/custom-web-site-deployment-scripts-with-kudu/
