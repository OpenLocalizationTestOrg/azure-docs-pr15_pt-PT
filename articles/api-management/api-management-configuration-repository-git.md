<properties 
    pageTitle="Como guardar e configurar a configuração do serviço de gestão de API utilizando Git" 
    description="Saiba como guardar e configurar a configuração do serviço de gestão de API utilizando Git." 
    services="api-management" 
    documentationCenter="" 
    authors="steved0x" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="api-management" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/25/2016" 
    ms.author="sdanie"/>


# <a name="how-to-save-and-configure-your-api-management-service-configuration-using-git"></a>Como guardar e configurar a configuração do serviço de gestão de API utilizando Git

>[AZURE.IMPORTANT] Configuração de Git para gestão de API atualmente está na pré-visualização. Está funcional concluída, mas é na pré-visualização porque podemos está à procura comentários sobre esta funcionalidade ativamente. É possível que poderá oferecemos uma quebra de alterar em resposta aos comentários dos clientes, para que recomendamos que não consoante a funcionalidade para utilização em ambientes de produção. Se tiver quaisquer comentários ou perguntas, consulte diga-nos Saiba `apimgmt@microsoft.com`.

Cada instância do serviço de gestão de API mantém uma base de dados de configuração que contenha informações sobre a configuração e os metadados para a instância do serviço. Podem ser efetuadas alterações para a instância do serviço, ao alterar uma definição no portal do publisher, utilizar um cmdlet do PowerShell ou fazer uma chamada de REST API. Para além destes métodos, também pode gerir a configuração de instância do serviço utilizando Git, tais como a ativação cenários de gestão de serviço:

-   Controlo de versões de configuração - transferir e armazenar diferentes versões da configuração do serviço
-   Em volume alterações à configuração - efetuar alterações a várias peças da sua configuração dos serviços no seu repositório local e integrar as alterações novamente para o servidor com uma única operação
-   Familiar Git toolchain e fluxo de trabalho - utilizar as ferramentas de Git e fluxos de trabalho que já estiver familiarizado com

O diagrama seguinte mostra uma descrição geral das várias formas para configurar a instância do serviço de gestão de API.

![Configurar o Git][api-management-git-configure]

Quando efetuar alterações no seu serviço utilizando o portal do publisher, os cmdlets do PowerShell ou REST API, está a gerir o serviço configuração da base de dados a utilizar o `https://{name}.management.azure-api.net` ponto final, conforme mostrado no lado direito do diagrama. O lado esquerdo do diagrama ilustra como pode gerir a configuração do serviço utilizando Git e Git repositório do seu serviço de que se encontra no `https://{name}.scm.azure-api.net`.

Os passos seguintes fornecem uma descrição geral de gerir o seu instância do serviço de gestão de API utilizando Git.

1.  Ativar o acesso de Git no seu serviço
2.  Guardar a sua base de dados de configuração do serviço para o seu repositório Git
3.  Clonar a repo Git para o seu computador local
4.  Separar o repo mais recente para baixo para o seu computador local e, as alterações de consolidação e push novamente para o seu repo
5.  Implementar as alterações a partir do seu repo na sua base de dados de configuração do serviço

Este artigo descreve como ativar e utilizar Git para gerir a configuração do serviço e fornece uma referência para os ficheiros e pastas no repositório de Git.

## <a name="to-enable-git-access"></a>Para permitir o acesso de Git

Pode ver rapidamente o estado da sua configuração Git visualizando o ícone de Git no canto superior direito do portal do publisher. Neste exemplo, Git access tem ainda não foi activado.

![Estado de Git][api-management-git-icon-enable]

Para ver e configurar as definições de configuração de Git, pode clicar no ícone de Git, ou clique no menu de **segurança** e navegue para o separador **repositório de configuração** .

![Ativar GIT][api-management-enable-git]

Para permitir o acesso de Git, selecione a caixa de verificação **Ativar Git acesso** .

Depois de um pouco a incorporá a alteração será guardada e é apresentada uma mensagem de confirmação. Tenha em atenção que Git ícone foi alterada para cor para indicar que o acesso de Git é activado e agora a mensagem de estado indica que não existem alterações não guardadas para o repositório. Isto acontece porque a base de dados de configuração de serviço de gestão de API ainda não foram guardada para o repositório.

![Git activado][api-management-git-enabled]

>[AZURE.IMPORTANT] Qualquer segredos que não estão definidos como propriedades serão armazenadas no repositório e irão permanecer no seu histórico até o utilizador desativem e voltar a ativar Git acesso. Propriedades de fornecem um local seguro para gerir valores de cadeia constante, incluindo segredos, através de todas as políticas e configuração de API para não ter armazená-los diretamente no seu declarações de política. Para mais informações, consulte o artigo [como utilizar as propriedades na políticas de gestão de API do Azure](api-management-howto-properties.md).

Para informações sobre como ativar ou desativar o acesso de Git utilizar a API REST, consulte [Ativar ou desativar o acesso de Git utilizar a API REST](https://msdn.microsoft.com/library/dn781420.aspx#EnableGit).

## <a name="to-save-the-service-configuration-to-the-git-repository"></a>Para guardar a configuração do serviço para o repositório de Git

É o primeiro passo antes de clonar do repositório guardar o estado atual da configuração do serviço para o repositório. Clique em **Guardar configuração para o repositório**.

![Guardar a configuração][api-management-save-configuration]

Faça as alterações desejadas no ecrã de confirmação e clique em **Ok** para guardar.

![Guardar a configuração][api-management-save-configuration-confirm]

Depois de alguns minutos a configuração é guardada, e o estado de configuração do repositório é apresentado, incluindo a data e hora da última alteração de configuração e a última sincronização entre a configuração do serviço e o repositório.

![Estado da configuração][api-management-configuration-status]

Assim que a configuração é guardada para o repositório, pode ser clonar.

Para obter informações sobre como efectuar esta operação a utilizar a API REST, consulte o artigo [consolidar instantâneo de configuração, utilizar a API REST](https://msdn.microsoft.com/library/dn781420.aspx#CommitSnapshot).

## <a name="to-clone-the-repository-to-your-local-machine"></a>Para clonar o repositório no computador local

Para clonar um repositório, é necessário o URL para o repositório, um nome de utilizador e uma palavra-passe. O nome de utilizador e o URL são apresentadas na parte superior do separador **repositório de configuração** .

![Git clonar][api-management-configuration-git-clone]

A palavra-passe é gerada na parte inferior do separador **repositório de configuração** .

![Gerar palavra-passe][api-management-generate-password]

Para gerar uma palavra-passe, primeiro se certificar de que o **termo** está definido para a data de expiração desejado e a hora e, em seguida, clique em **Gerar Token**.

![Palavra-passe][api-management-password]

>[AZURE.IMPORTANT] Anote esta palavra-passe. Depois de sair desta página a palavra-passe não será apresentada novamente.

Os exemplos seguintes utilizam a ferramenta de festa Git do [Git para Windows](http://www.git-scm.com/downloads) , mas pode utilizar qualquer ferramenta de Git estiver familiarizado com.

Abrir a ferramenta de Git a pasta pretendida e execute o seguinte comando para clonar do repositório de git para o seu computador local, utilizando o comando fornecido pelo portal de publisher.

    git clone https://bugbashdev4.scm.azure-api.net/ 

Fornece o nome de utilizador e palavra-passe quando lhe for pedido.

Se receber algum erro, tente modificar o `git clone` comando para incluir o nome de utilizador e palavra-passe, conforme mostrado no seguinte exemplo.

    git clone https://username:password@bugbashdev4.scm.azure-api.net/

Se este procedimento fornece um erro, tente parte da palavra-passe do comando de codificação de URL. Uma forma rápida de fazer isto é abrir o Visual Studio e emitir o seguinte comando na **Janela imediata**. Para abrir a **Janela imediata**, abrir qualquer solução ou projecto no Visual Studio (ou criar uma nova aplicação de consola vazia) e selecione **Windows**, **imediata** a partir do menu **Depurar** .

    ?System.NetWebUtility.UrlEncode("password from publisher portal")

Utilize a palavra-passe codificada juntamente com a sua localização de nome e repositório de utilizador para construir o comando git.

    git clone https://username:url encoded password@bugbashdev4.scm.azure-api.net/

Depois do repositório é clonar pode ver e trabalhar com o mesmo no sistema de ficheiros local. Para mais informações, consulte o artigo [referência de estrutura de ficheiros e pastas do repositório de Git local](#file-and-folder-structure-reference-of-local-git-repository).

## <a name="to-update-your-local-repository-with-the-most-current-service-instance-configuration"></a>Para atualizar o seu repositório local com a configuração de instância mais recente do serviço

Se efetuar alterações aos seus instância do serviço de gestão de API no portal do publisher ou utilizar a API REST, tem de guardar estas alterações para o repositório antes de que pode atualizar o repositório local com as alterações mais recentes. Para executar esta tarefa, clique em **Guardar configuração para o repositório** no separador **repositório de configuração** no portal do publisher e, em seguida, emitir o seguinte comando na sua repositório local.

    git pull

Antes de executar o `git pull` Certifique-se de que estão na pasta para o repositório local. Se apenas tiver concluído a `git clone` comando, em seguida, tem de alterar o diretório para sua repo ao executar um comando semelhante ao seguinte.

    cd bugbashdev4.scm.azure-api.net/

## <a name="to-push-changes-from-your-local-repo-to-the-server-repo"></a>Para transmitir alterações a partir do seu local repo para repo o servidor

Para transmitir alterações a partir do seu local repositório para o repositório de servidor, tem de confirmar as suas alterações e, em seguida, transmiti-los para o repositório de servidor. Para consolidar as suas alterações, abrir a ferramenta de comando Git, mude para o diretório do seu local repositório e emitir os seguintes comandos.

    git add --all
    git commit -m "Description of your changes"

Para transmitir todas as confirmações para o servidor, execute o seguinte comando.

    git push

## <a name="to-deploy-any-service-configuration-changes-to-the-api-management-service-instance"></a>Para implementar quaisquer alterações de configuração do serviço para a instância do serviço de gestão de API

Assim que as suas alterações locais são consolidadas seguia e para o repositório de servidor, pode implementá-las para a instância do serviço de gestão de API.

![Implementar][api-management-configuration-deploy]

Para obter informações sobre como efectuar esta operação a utilizar a API REST, consulte o artigo [alterações de Git implementar a base de dados de configuração utilizar a API REST](https://msdn.microsoft.com/library/dn781420.aspx#DeployChanges).

## <a name="file-and-folder-structure-reference-of-local-git-repository"></a>Referência de estrutura de ficheiros e pastas do repositório de Git local

Os ficheiros e pastas no repositório de local git contêm as informações de configuração sobre a instância do serviço.

| Item                       | Descrição                                                                                |
|-------------------------   |--------------------------------------------------------------------------------------------|
| pasta de gestão de api de raiz | Contém a configuração de nível superior para a instância do serviço                                  |
| pasta APIs                | Contém a configuração para apis na instância do serviço                            |
| pasta grupos              | Contém a configuração para os grupos na instância do serviço                          |
| pasta de políticas            | Contém as políticas que foram na instância do serviço                                              |
| pasta portalStyles        | Contém a configuração para as developer portal as personalizações a instância do serviço |
| pasta produtos            | Contém a configuração para os produtos da instância do serviço                        |
| pasta Modelos           | Contém a configuração para os modelos de correio eletrónico a instância do serviço                 |

Cada pasta pode conter um ou mais ficheiros e, em alguns casos uma ou mais pastas, por exemplo, uma pasta para cada API, produto ou grupo. Os ficheiros dentro de cada pasta são específicos para o tipo de entidade descrito pelo nome da pasta.

| Tipo de ficheiro | Objetivo                                                                |
|-----------|------------------------------------------------------------------------|
| JSON      | Informações de configuração sobre a entidade respetivas                  |
| HTML      | Descrições sobre a entidade, frequentemente apresentada no portal do Programador |
| XML       | Declarações de política                                                      |
| CSS       | Folhas de estilo para a personalização de portal de programador                        |

Estes ficheiros podem ser criados, eliminados, editados e geridos no sistema de ficheiro local e as alterações implementadas novamente para o seu instância do serviço de gestão de API.

>[AZURE.NOTE] As seguintes entidades não sejam encontram no repositório de Git e não podem ser configuradas utilizando Git.
>
>-    Utilizadores
>-    Subscrições
>-    Propriedades
>-    Entidades portais do programador diferente de estilos

### <a name="root-api-management-folder"></a>Pasta de gestão de api de raiz

Na raiz `api-management` pasta contém um `configuration.json` ficheiro que contenha informações de nível superiores sobre a instância do serviço no seguinte formato.

    {
      "settings": {
        "RegistrationEnabled": "True",
        "UserRegistrationTerms": null,
        "UserRegistrationTermsEnabled": "False",
        "UserRegistrationTermsConsentRequired": "False",
        "DelegationEnabled": "False",
        "DelegationUrl": "",
        "DelegatedSubscriptionEnabled": "False",
        "DelegationValidationKey": ""
      },
      "$ref-policy": "api-management/policies/global.xml"
    }

As primeiras quatro definições (`RegistrationEnabled`, `UserRegistrationTerms`, `UserRegistrationTermsEnabled`, e `UserRegistrationTermsConsentRequired`) mapa para as seguintes definições no separador **identidades** na secção **segurança** .

| Definição de identidade                     | Mapas para                                               |
|--------------------------------------|-------------------------------------------------------|
| RegistrationEnabled                  | Caixa de verificação **Redirecionar utilizadores anónimos à página de início de sessão** |
| UserRegistrationTerms                | **Termos de utilização no início de sessão no utilizador da** caixa de texto               |
| UserRegistrationTermsEnabled         | Caixa de verificação **Mostrar termos de utilização na página de inscrição**         |
| UserRegistrationTermsConsentRequired | Caixa de verificação **exigir consentimento**                          |

![Definições de identidade][api-management-identity-settings]

As definições de quatro seguinte (`DelegationEnabled`, `DelegationUrl`, `DelegatedSubscriptionEnabled`, e `DelegationValidationKey`) mapa para as seguintes definições no separador **delegação** na secção **segurança** .

| Definição de delegação           | Mapas para                                    |
|------------------------------|--------------------------------------------|
| DelegationEnabled            | Caixa de verificação de **delegado de início de sessão no & inscrição**    |
| DelegationUrl                | Caixa de texto do **URL de ponto final de delegação**        |
| DelegatedSubscriptionEnabled | Caixa de verificação de **subscrição do produto de delegado** |
| DelegationValidationKey      | Caixa de texto de **Chave de validação de delegado**        |

![Definições de delegação][api-management-delegation-settings]

A definição final, `$ref-policy`, o ficheiro de declarações de política global para a instância do serviço de mapas.

### <a name="apis-folder"></a>pasta APIs

O `apis` pasta contém uma pasta para cada API na instância do serviço que contém os seguintes itens.

-   `apis\<api name>\configuration.json`-Trata-se a configuração para a API e contém informações sobre o URL do serviço back-end e as operações. Esta é a mesma informação seria devolvida se fosse ligar a [obter uma API específica](https://msdn.microsoft.com/library/azure/dn781423.aspx#GetAPI) com `export=true` no `application/json` formatar.
-   `apis\<api name>\api.description.html`-Trata-se a descrição da API e corresponde à `description` propriedade da [entidade de API](https://msdn.microsoft.com/library/azure/dn781423.aspx#EntityProperties).
-   `apis\<api name>\operations\`-Esta pasta contém `<operation name>.description.html` ficheiros mapeiam para as operações da API do. Cada ficheiro contém a descrição de uma única operação na API do qual mapas para o `description` propriedade da [entidade de operação](https://msdn.microsoft.com/library/azure/dn781423.aspx#OperationProperties) no REST API.

### <a name="groups-folder"></a>pasta grupos

O `groups` pasta contém uma pasta para cada grupo definida na instância do serviço.

-   `groups\<group name>\configuration.json`-Esta é a configuração para o grupo. Este é as mesmas informações que deve ser devolvidas se fosse ligar a operação de [obter um grupo específico](https://msdn.microsoft.com/library/azure/dn776329.aspx#GetGroup) .
-   `groups\<group name>\description.html`-Trata-se a descrição do grupo e corresponde à `description` propriedade da [entidade de grupo](https://msdn.microsoft.com/library/azure/dn776329.aspx#EntityProperties).

### <a name="policies-folder"></a>pasta de políticas

O `policies` pasta contém as instruções de política para a instância do serviço.

-   `policies\global.xml`-contém políticas definidas no âmbito global para a instância do serviço.
-   `policies\apis\<api name>\`-Se tiver quaisquer políticas definidas no âmbito API, estão contidas nesta pasta.
-   `policies\apis\<api name>\<operation name>\`pasta - se tiver quaisquer políticas definidas no âmbito de operação, estão contidas nesta pasta no `<operation name>.xml` ficheiros mapeiam para as declarações de política para cada operação.
-   `policies\products\`-Se tiver quaisquer políticas definidas no âmbito do produto, estão contidas nesta pasta que contém `<product name>.xml` ficheiros mapeiam para as declarações de política para cada produto.

### <a name="portalstyles-folder"></a>pasta portalStyles

O `portalStyles` pasta contém folhas de estilo e configuração de personalizações portal do programador para a instância do serviço.

-   `portalStyles\configuration.json`-contém os nomes das folhas de estilo utilizados pelo portal de programador
-   `portalStyles\<style name>.css`-cada `<style name>.css` ficheiro contém estilos para o portal de programador (`Preview.css` e `Production.css` por predefinição).

### <a name="products-folder"></a>pasta produtos

O `products` pasta contém uma pasta para cada produto foi definido na instância do serviço.

-   `products\<product name>\configuration.json`-Esta é a configuração para o produto. Este é as mesmas informações que deve ser devolvidas se fosse ligar a operação de [obter um produto específico](https://msdn.microsoft.com/library/azure/dn776336.aspx#GetProduct) .
-   `products\<product name>\product.description.html`-Trata-se a descrição do produto e corresponde à `description` propriedade da [entidade de produto](https://msdn.microsoft.com/library/azure/dn776336.aspx#Product) no REST API.

### <a name="templates"></a>modelos

O `templates` pasta contém configuração para os [modelos de correio eletrónico](api-management-howto-configure-notifications.md) da instância do serviço.

-   `<template name>\configuration.json`-Esta é a configuração para o modelo de e-mail.
-   `<template name>\body.html`-Este é o corpo do modelo de e-mail.

## <a name="next-steps"></a>Próximos passos

Para obter informações sobre outras formas de gerir o seu instância do serviço, consulte:

-   Gerir a sua instância do serviço utilizando os seguintes cmdlets do PowerShell
    -   [Implementação de serviço referência do cmdlet do PowerShell](https://msdn.microsoft.com/library/azure/mt619282.aspx)
    -   [Gestão de serviços de referência do cmdlet do PowerShell](https://msdn.microsoft.com/library/azure/mt613507.aspx)
-   Gerir a sua instância do serviço no portal do publisher
    -   [Gerir a sua primeira API](api-management-get-started.md)
-   Gerir a sua instância do serviço de utilizar a API REST
    -   [Referência de gestão de API REST API](https://msdn.microsoft.com/library/azure/dn776326.aspx)

## <a name="watch-a-video-overview"></a>Ver uma descrição geral em vídeo

> [AZURE.VIDEO configuration-over-git]

[api-management-enable-git]: ./media/api-management-configuration-repository-git/api-management-enable-git.png
[api-management-git-enabled]: ./media/api-management-configuration-repository-git/api-management-git-enabled.png
[api-management-save-configuration]: ./media/api-management-configuration-repository-git/api-management-save-configuration.png
[api-management-save-configuration-confirm]: ./media/api-management-configuration-repository-git/api-management-save-configuration-confirm.png
[api-management-configuration-status]: ./media/api-management-configuration-repository-git/api-management-configuration-status.png
[api-management-configuration-git-clone]: ./media/api-management-configuration-repository-git/api-management-configuration-git-clone.png
[api-management-generate-password]: ./media/api-management-configuration-repository-git/api-management-generate-password.png
[api-management-password]: ./media/api-management-configuration-repository-git/api-management-password.png
[api-management-git-configure]: ./media/api-management-configuration-repository-git/api-management-git-configure.png
[api-management-configuration-deploy]: ./media/api-management-configuration-repository-git/api-management-configuration-deploy.png
[api-management-identity-settings]: ./media/api-management-configuration-repository-git/api-management-identity-settings.png
[api-management-delegation-settings]: ./media/api-management-configuration-repository-git/api-management-delegation-settings.png
[api-management-git-icon-enable]: ./media/api-management-configuration-repository-git/api-management-git-icon-enable.png




