<properties
    pageTitle="Publicar uma aplicação a um cluster remoto com o Visual Studio | Microsoft Azure"
    description="Saiba como publicar uma aplicação a um cluster de ferro serviço remoto utilizando o Visual Studio."
    services="service-fabric"
    documentationCenter="na"
    authors="cawams"
    manager="timlt"
    editor="" />

<tags
    ms.service="multiple"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="multiple"
    ms.date="07/29/2016"
    ms.author="cawa" />

# <a name="publish-an-application-to-a-remote-cluster-by-using-visual-studio"></a>Publicar uma aplicação a um cluster remota utilizando o Visual Studio

> [AZURE.SELECTOR]
- [PowerShell](service-fabric-deploy-remove-applications.md)
- [Visual Studio](service-fabric-publish-app-remote-cluster.md)

<br/>

A extensão do Azure Service ferro para Visual Studio fornece uma forma fácil, de forma repetida e script para publicar uma aplicação a um cluster de serviço ferro.

## <a name="the-artifacts-required-for-publishing"></a>Erros necessários para publicação

### <a name="deploy-fabricapplicationps1"></a>FabricApplication.ps1 implementar

Este é um script PowerShell que utiliza um caminho de perfil de publicar como um parâmetro para as aplicações de serviço ferro publicação. Uma vez que este script parte da sua aplicação, está bem-vindo ao modificá-lo conforme necessário para a sua aplicação.

### <a name="publish-profiles"></a>Publicar perfis

Uma pasta no projeto de aplicação de serviço ferro denominado **PublishProfiles** contém ficheiros XML que armazenam informações essenciais de publicação de uma aplicação, tais como:

- Parâmetros de ligação de cluster ferro de serviço
- Caminho para um ficheiro de parâmetro de aplicação
- Definições de atualização

Por predefinição, a aplicação irá incluir dois publicar perfis: Local.xml e Cloud.xml. Pode adicionar mais perfis ao copiar e colar um dos ficheiros predefinido.

### <a name="application-parameter-files"></a>Ficheiros de parâmetro da aplicação

Uma pasta no projeto de aplicação de serviço ferro denominado **ApplicationParameters** contém ficheiros XML para os valores de parâmetros manifesto de aplicação especificado pelo utilizador. Podem ser parametrizados ficheiros manifesto da aplicação para que possa utilizar diferentes valores para as definições de implementação. Para saber mais sobre parametrizar a aplicação, consulte o artigo [Gerir vários ambientes no serviço ferro](service-fabric-manage-multiple-environment-app-configuration.md).

>[AZURE.NOTE] Para os serviços do ator, deverá criar projeto pela primeira vez antes de tentar editar o ficheiro num editor de ou através de caixa de diálogo Publicar. Isto acontece porque a parte dos ficheiros manifesto será gerada durante a compilação.

## <a name="to-publish-an-application-by-using-the-publish-service-fabric-application-dialog-box"></a>Para publicar uma aplicação utilizando a caixa de diálogo Publicar a aplicação de serviço ferro

Os passos seguintes demonstram como publicar uma aplicação utilizando a caixa de diálogo **Publicar a aplicação de serviço ferro** fornecida pelo Visual Studio serviço ferro Tools.

1. No menu de atalho do projeto ferro a aplicação de serviço, selecione **publicar...** Para ver a caixa de diálogo **Publicar a aplicação de serviço ferro** .

    ![O * * caixa de diálogo Publicar serviço ferro aplicação * *][0]

    O ficheiro selecionado na caixa de lista pendente do **perfil de destino** é onde todas as definições, exceto **manifesto versões**, são guardadas. Pode reutilizar um perfil existente ou crie um novo ao selecionar **< gerir perfis de... >** na caixa de listagem pendente **perfil de destino** . Quando escolher um perfil de publicar, os seus conteúdos aparecem nos campos correspondentes da caixa de diálogo. Para guardar as alterações em qualquer altura, selecione a ligação de **Guardar o perfil** .    

2. Na secção de **ponto final de ligação** , especifique o ponto final de publicação de um local ou remota ferro de serviço do cluster. Para adicionar ou alterar o ponto final de ligação, clique na lista pendente de **Ponto final de ligação** . A lista apresenta o cluster de serviço ferro disponível ligação os pontos finais para o qual pode publicar com base nas suas subscrições Azure. Tenha em atenção que se que não é já tiver sessão iniciada Visual Studio, será pedido para fazê-lo.

    Utilize a caixa de diálogo de seleção de cluster para escolher a partir do conjunto de subscrições disponíveis e clusters de.

    ![O * * caixa de diálogo Selecionar serviço ferro Cluster * *][1]

    >[AZURE.NOTE] Se gostaria publicar um ponto final arbitrário (tal como um cluster de festa), consulte a secção de **publicação a um ponto final cluster arbitrário** abaixo.

    Assim que escolher um ponto final, o Visual Studio valida a ligação ao cluster de serviço ferro selecionado. Se o cluster não estiver seguro, Visual Studio pode ligar para o mesmo imediatamente. No entanto, se o cluster estiver protegido, terá de instalar um certificado no seu computador local antes de continuar. Consulte o artigo [como configurar ligações seguras](service-fabric-visualstudio-configure-secure-connections.md) para obter mais informações. Quando terminar, selecione o botão **OK** . Cluster seleccionado é apresentada na caixa de diálogo **Publicar a aplicação de serviço ferro** .

3. Na caixa de lista pendente de **Ficheiro de parâmetro da aplicação** , navegue para um ficheiro de parâmetro de aplicação. Um ficheiro de parâmetro de aplicação especificado pelo utilizador valores para os parâmetros de suspensões no ficheiro manifesto da aplicação. Para adicionar ou alterar um parâmetro, escolha o botão **Editar** . Introduzir ou alterar o valor do parâmetro na grelha de **parâmetros** . Quando tiver terminado, selecione o botão **Guardar** .

    ![O * * caixa de diálogo Editar parâmetros * *][2]

4. Utilizar a caixa de verificação **atualizar a aplicação** para especificar se publicar esta ação é uma atualização. Atualização publicar ações diferem normal publicar ações. Consulte o artigo [Atualizar a aplicação de serviço ferro](service-fabric-application-upgrade.md) para obter uma lista das diferenças. Para configurar as definições de atualização, escolha a ligação de **Configurar definições de atualização** . É apresentado o editor de parâmetro de atualização. Consulte o artigo [Configurar a atualização de uma aplicação de serviço ferro](service-fabric-visualstudio-configure-upgrade.md) para saber mais sobre a atualização parâmetros.

5. Selecione **manifesto versões...** botão para ver a caixa de diálogo **Editar versões** . Tem de atualizar a aplicação de serviço versões e para uma atualização substituir. Consulte o artigo [Atualizar tutorial de aplicação de serviço ferro](service-fabric-application-upgrade-tutorial.md) para saber como aplicação e serviço manifesto versões impacto um processo de atualização.

    ![O * * caixa de diálogo Editar versões * *][3]

    Se a aplicação e versões do serviço utilizam o controlo de versões semântico como 1.0.0 ou valores numéricos no formato de 1.0.0.0, selecione a opção **atualizar automaticamente as versões de serviço e de aplicação** . Quando escolher esta opção, o serviço e números de versão da aplicação são atualizados automaticamente sempre que um código, config ou os dados do feed de dados versão são atualizados. Se preferir editar as versões manualmente, desmarque a caixa de verificação desativar esta funcionalidade.

    >[AZURE.NOTE] Para todas as entradas de pacote que seja apresentado para um projeto do ator, pela primeira vez a criar o projeto para gerar as entradas na ficheiros manifesto do serviço.

6. Quando tiver terminado especificando todas as definições necessárias, selecione o botão **Publicar** para publicar a sua aplicação para o cluster de serviço ferro selecionado. As definições que especificou são aplicadas ao processo de publicar.

## <a name="publish-to-an-arbitrary-cluster-endpoint-including-party-clusters"></a>Publicar um ponto final cluster arbitrário (incluindo clusters de festa)

O Visual Studio experiência de publicação está optimizado para publicação a clusters remotos associados a uma das suas subscrições Azure. No entanto, é possível publicar no arbitrários pontos finais (por exemplo, o serviço ferro festa clusters) ao editar diretamente no perfil de publicar XML. Conforme descrito acima, dois publicar perfis são fornecidos por predefinição –**Local.xml** e **Cloud.xml**– mas está bem-vindo ao criar perfis adicionais para diferentes ambientes. Por exemplo, poderá pretender criar um perfil de publicação para clusters de festa, talvez denominada **Party.xml**.

Se estiver a ligar a um cluster não seguro, só necessário de ponto final de ligação cluster, tais como `partycluster1.eastus.cloudapp.azure.com:19000`. Nesse caso, o ponto final de ligação no perfil de publicar seria algo parecido com:

```XML
<ClusterConnectionParameters ConnectionEndpoint="partycluster1.eastus.cloudapp.azure.com:19000" />
```

  Se estiver a ligar a um cluster seguro, também terá de fornecer detalhes do certificado de cliente a partir da loja local para ser utilizado para autenticação. Para obter mais detalhes, consulte o artigo [Configurar ligações seguras a um cluster de serviço ferro](service-fabric-visualstudio-configure-secure-connections.md).

  Assim que o seu perfil de publicar está configurado, pode referenciá-la na caixa de diálogo Publicar conforme apresentado abaixo.

  ![Publicar novo perfil na caixa de diálogo Publicar][4]

  Tenha em atenção que neste caso, o novo perfil de publicar aponta para um dos ficheiros de parâmetro da aplicação predefinida. Isto é adequado se pretende publicar a mesma configuração da aplicação para um número de ambientes. Por outro lado, nos casos em que pretende ter configurações diferentes para cada ambiente que pretende publicar, faria sentido para criar um ficheiro de parâmetro de aplicação correspondente.

## <a name="next-steps"></a>Próximos passos

Para saber como pode automatizar o processo de publicação num ambiente integração contínuo, consulte o artigo [configurar o serviço ferro contínua integração](service-fabric-set-up-continuous-integration.md).


[0]: ./media/service-fabric-publish-app-remote-cluster/PublishDialog.png
[1]: ./media/service-fabric-publish-app-remote-cluster/SelectCluster.png
[2]: ./media/service-fabric-publish-app-remote-cluster/EditParams.png
[3]: ./media/service-fabric-publish-app-remote-cluster/EditVersions.png
[4]: ./media/service-fabric-publish-app-remote-cluster/publish-to-party-cluster.png
