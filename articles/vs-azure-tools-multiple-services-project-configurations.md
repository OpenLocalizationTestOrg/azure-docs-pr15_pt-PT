<properties
   pageTitle="Configurar o seu projeto Azure com configurações dos serviços de vários | Microsoft Azure"
   description="Saiba como configurar um projeto de serviço de nuvem Azure alterando os ficheiros ServiceDefinition.csdef e ServiceConfiguration.cscfg."
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# <a name="configuring-your-azure-project-using-multiple-service-configurations"></a>Configurar o seu projeto Azure com várias configurações de serviço

Um projeto de serviço de nuvem Azure inclui dois ficheiros de configuração: ServiceDefinition.csdef e ServiceConfiguration.cscfg. Estes ficheiros são compactados com a aplicação de serviço de nuvem Azure e implementados Azure.

- O ficheiro **ServiceDefinition.csdef** contém os metadados que são necessário o ambiente do Azure para as necessidades da sua aplicação de serviço de nuvem, incluindo que funções contém. Este ficheiro também contém definições de configuração de que se aplicam a todas as instâncias. Estas definições de configuração podem ser lidos o tempo de execução com a API de tempo de execução de alojamento do Azure serviço. Este ficheiro não pode ser atualizado enquanto o seu serviço está em execução no Azure.

- O ficheiro **ServiceConfiguration.cscfg** conjuntos de valores para as definições de configuração no ficheiro de definição de serviço e especifica o número de instâncias para executar para cada função. Pode ser atualizado este ficheiro enquanto o serviço de nuvem estiver em execução no Azure.

As ferramentas de Azure para o Microsoft Visual Studio fornecem páginas de propriedades que pode utilizar para configurar definições de configuração de armazenados nestes ficheiros. Para aceder às páginas de propriedade, faça duplo clique sobre a referência de função sob o projeto de serviço de nuvem Azure no Explorador de solução, ou a referência de função com o botão direito e selecione **Propriedades**, conforme apresentado na figura seguinte.

![VS_Solution_Explorer_Roles_Properties](./media/vs-azure-tools-multiple-services-project-configurations/IC784076.png)

Para obter informações sobre os esquemas subjacentes para a definição de serviço e os ficheiros de configuração do serviço, consulte a [Referência de esquema](https://msdn.microsoft.com/library/azure/dd179398.aspx). Para mais informações sobre a configuração do serviço, consulte o artigo [como configurar os serviços na nuvem](./cloud-services/cloud-services-how-to-configure.md).

## <a name="configuring-role-properties"></a>Configurar propriedades de função

As páginas de propriedade para uma função de web e uma função de trabalho são semelhantes, apesar de não existem algumas diferenças, indicadas nas secções seguintes.

Na página de **Colocação em cache** , pode configurar o Azure colocação em cache dos serviços.

### <a name="configuration-page"></a>Página de configuração

Na página **configuração** , pode definir estas propriedades:

**Instâncias**

Defina a propriedade de contagem de **instância** para o número de instâncias que para esta função deverá ser possível executar o serviço.

Defina a propriedade **tamanho da memória virtual** para **Extra pequenas**, **pequeno**, **médio**, **grande**ou **Extra grande**.  Para mais informações, consulte o artigo [tamanhos dos serviços em nuvem](./cloud-services/cloud-services-sizes-specs.md).

**Acção de arranque** (Apenas para o web função)

Defina esta propriedade para especificar que o Visual Studio deve iniciação num browser para os pontos finais HTTP ou os pontos finais HTTPS ou ambos quando iniciar a depuração.

A opção de ponto final HTTPS está disponível apenas se já tiver definido um ponto final HTTPS para o seu perfil. Pode definir um ponto final HTTPS na página **pontos finais de** propriedade.

Se já tiver adicionado um ponto final HTTPS, a opção de ponto final HTTPS está ativada por predefinição e Visual Studio será executado num browser para este ponto final quando iniciar a depuração, para além de um browser para o ponto final de HTTP. Este comando assume que estão activadas ambas as opções de arranque.

**Diagnósticos**

Por predefinição, os diagnósticos está ativado para a função da Web. A nuvem Azure project e armazenamento de conta do serviço estão definidas para utilizar o emulador armazenamento local. Quando estiver pronto para implementar Azure, pode selecionar o botão de construtor (**…**) para atualizar a conta de armazenamento a utilizar o Azure armazenamento na nuvem. Pode transferir os dados de diagnóstico para a conta de armazenamento a pedido ou em intervalos agendados automaticamente. Para mais informações sobre diagnósticos do Azure, consulte o artigo [Ativar diagnósticos no Azure serviços em nuvem e máquinas virtuais](./cloud-services/cloud-services-dotnet-diagnostics.md).

## <a name="settings-page"></a>Página de definições

Na página **Definições** , pode adicionar as definições de configuração do seu serviço. Definições de configuração são pares valor do nome. A função a execução de código pode ler os valores das suas definições de configuração o tempo de execução utilizar classes fornecidas pela [Azure gerido biblioteca](http://go.microsoft.com/fwlink?LinkID=171026). Especificamente, o método de [GetConfigurationSettingValue](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.serviceruntime.roleenvironment.getconfigurationsettingvalue.aspx) devolve o valor de uma definição de configuração nomeado o tempo de execução.

### <a name="configuring-a-connection-string-to-a-storage-account"></a>Configurar uma cadeia de ligação a uma conta de armazenamento

Uma cadeia de ligação é uma definição de configuração que fornece informações de autenticação e ligação para o emulador armazenamento ou para uma conta de armazenamento Azure. Sempre que o seu código tem aceder a dados de serviços de armazenamento Azure – ou seja, blob, fila ou dados da tabela – a partir de uma função a execução de código, terá de definir uma cadeia de ligação para essa conta de armazenamento.

Uma cadeia de ligação que aponta para uma conta de armazenamento Azure tem de utilizar um formato definido. Para obter informações sobre como criar cadeias de ligação, consulte o artigo [Configurar cadeias de ligação do Azure armazenamento](./storage/storage-configure-connection-string.md).

Quando estiver pronto para testar o seu serviço dos serviços de armazenamento Azure ou quando estiver pronto para implementar o seu serviço de nuvem para Azure, pode alterar o valor de quaisquer cadeias de ligação para apontar para a sua conta de armazenamento Azure. Selecionar (**…**), selecione **as credenciais de conta de armazenamento Enter**. Introduza as informações da conta que inclui o seu nome de conta e chave da conta. Na caixa de diálogo **Armazenamento cadeia de ligação de conta** , também pode indicar se pretende utilizar pontos finais HTTPS predefinidos (a opção predefinida), os pontos finais HTTP predefinidos ou os pontos finais personalizados. Pode decidir utilizar os pontos finais personalizados se ter registado um nome de domínio personalizado para o serviço, tal como descrito em [configurar um nome de domínio personalizado para os dados de BLOBs numa conta de armazenamento Azure](./storage/storage-custom-domain-name.md).

>[AZURE.IMPORTANT] Tem de modificar as cadeias de ligação para apontar para uma conta de armazenamento Azure antes de implementar o seu serviço. A falhar fazer isto pode causar a sua função não para iniciar, ou para percorrer os Estados a inicialização, ocupados e parar.

## <a name="endpoints-page"></a>Página pontos finais

Uma função de trabalho pode ter qualquer número de pontos finais HTTP, HTTPS ou TCP. Os pontos finais podem ser os pontos finais introdução, estão disponíveis para os clientes externos, ou os pontos finais internos, estão disponíveis para outras funções que são executadas no serviço.

- Para tornar um ponto final HTTP disponível para clientes externos e browsers, alterar o tipo de ponto final para a entrada e especificar um nome e um número de porta público.

- Para disponibilizar um ponto final HTTPS para clientes externos e browsers, altere o tipo de ponto final para **entrada**e especificar um nome, um número de porta público e um nome de gestão de certificado.

    Tenha em atenção que antes de poder especificar um certificado de gestão, tem de definir o certificado na página de propriedade **certificados** .

- Para disponibilizar um ponto final para acesso interno por outras funções no serviço de nuvem, altere o tipo de ponto final para interno e especificar um nome e possíveis portas privadas para este ponto final.

## <a name="local-storage-page"></a>Página de armazenamento local

Pode utilizar a página de propriedades de **Armazenamento Local** para reservar um ou mais recursos de armazenamento local para uma função. Um recurso de armazenamento local é um diretório reservado no sistema de ficheiros da máquina virtual Azure na qual está em execução de uma instância de uma função.

## <a name="certificates-page"></a>Página de certificados

Na página de **certificados** , pode associar certificados a sua função. Os certificados que adicionar podem ser utilizados para configurar o seu pontos finais HTTPS na página **pontos finais de** propriedade.

A página de propriedades de **certificados** adiciona informações sobre os seus certificados para a configuração do serviço. Tenha em atenção que os seus certificados não são fornecidos com o seu serviço; tem carregue os seus certificados separadamente para Azure através do [portal clássica Azure](http://go.microsoft.com/fwlink/?LinkID=213885).

Para associar um certificado ao seu cargo, forneça um nome para o certificado. Utilizar este nome para consultar o certificado quando configura um ponto final HTTPS na página **pontos finais de** propriedade. Em seguida, especifique se o arquivo de certificados é **Computador Local** ou **Utilizador atual** e o nome do arquivo de. Por fim, introduza impressão digital do certificado. Se o certificado pertencer a User\Personal atual (o meu) loja, pode introduzir impressão digital do certificado ao selecionar o certificado de uma lista povoada. Se está inserido em qualquer outra localização, introduza o valor de impressão digital desenhada à mão.

Quando adiciona um certificado do arquivo de certificados, quaisquer certificados intermédios são automaticamente adicionados às definições de configuração para si. Estes certificados intermédios também tem de ser carregados para Azure para configurar o seu serviço de SSL corretamente.

Qualquer certificados de gestão associar o seu serviço só se aplicam ao seu serviço quando está a ser executado na nuvem. Quando o seu serviço está em execução no ambiente de desenvolvimento local, utiliza um certificado padrão que é gerido pelo emulador cluster.

## <a name="configuring-the-azure-cloud-service-project"></a>Configurar o projeto de serviço de nuvem Azure

Para configurar as definições que se aplicam a um projeto de serviço de nuvem Azure inteira, pela primeira vez de abrir o menu de atalho para esse nó de projeto e, em seguida, escolher propriedades para abrir as suas páginas de propriedades. A tabela seguinte mostra essas páginas de propriedade.

|Página de propriedades|Descrição|
|---|---|
|Aplicação|A partir desta página, pode apresentar informações sobre a versão do Azure ferramentas que utiliza este projeto de serviço de nuvem, e pode atualizar para a versão atual das ferramentas.|
|Criar eventos|A partir desta página, pode definir eventos de compilação pré e pós compilação.|
|Desenvolvimento|A partir desta página, pode especificar as instruções de configuração da compilação e as condições em qual quaisquer eventos de compilação pós são executados.|
|Web|A partir desta página, pode configurar as definições que se relacionam com o servidor web.|
