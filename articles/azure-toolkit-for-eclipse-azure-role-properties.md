<properties
    pageTitle="Propriedades da função Azure"
    description="Saiba como utilizar o Toolkit de Azure para Eclipse para configurar as definições da função Azure."
    services=""
    documentationCenter="java"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="multiple"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/11/2016" 
    ms.author="robmcm"/>

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh690945.aspx -->

# <a name="azure-role-properties"></a>Propriedades da função Azure #

Definições de configuração de vários para o seu perfil Azure podem ser definidas dentro o Toolkit de Azure para Eclipse.

## <a name="configuring-azure-role-properties"></a>Configurar propriedades de função Azure ##

Configurar as propriedades da função Azure é feito através das caixas de diálogo de propriedades para o seu perfil de trabalho. Abra o menu de contexto para a função no painel de Project Explorer de Eclipse e selecione o sub-menu de **Azure** . (Se não vir a função no Explorador de projeto Eclipse, expanda o seu projeto no Project Explorer Azure.)

![][ic789599]

As várias propriedades que podem ser definidas a partir das caixas de diálogo de **Propriedades** são descritas neste tópico. Tenha em atenção que muitas propriedades são automaticamente preenchidas quando cria um novo projeto de implementação do Azure.

As seguintes páginas de propriedade estão disponíveis para funções de Azure.

* [Propriedades de máquina virtual](#virtual_machine_properties)
* [Propriedades de colocação em cache](#caching_properties)
* [Propriedades de certificados](#certificates_properties)
* [Propriedades de componentes](#components_properties)
* [Depuração de propriedades](#debugging_properties)
* [Propriedades de pontos finais](#endpoints_properties)
* [Propriedades de variáveis de ambiente](#environment_variables_properties)
* [Balanceamento de carga / propriedades da sessão afinidade (a.k.a "sessões autocolantes")](#session_affinity_properties)
* [Propriedades de armazenamento local](#local_storage_properties)
* [Propriedades do servidor de configuração](#server_configuration_properties)
* [SSL descarregar propriedades](#ssl_offloading_properties)
    
<a name="virtual_machine_properties"></a>
### <a name="virtual-machine-properties"></a>Propriedades de máquina virtual ###

Abra o menu de contexto para a função no painel do Project Explorer de Eclipse, clique em **Azure**e, em seguida, clique em **Propriedades**e terá a capacidade para alterar o tamanho de máquina virtual e também alterar o número de instâncias, conforme apresentado na seguinte imagem.

![][ic719499]

>[AZURE.NOTE] Apenas para o Windows: quando que defina o número de instâncias um valor maior do que 1 e também de configurar um servidor de aplicações, o toolkit irá permitir apenas 1 função instância para ser executada no emulador, independentemente desta definição. Este é para evitar conflitos de encadernação de porta entre as instâncias de servidor diferente (por exemplo, todas as tentar ligar à porta 8080) quando são executados no mesmo computador. A definição de contagem de instância pretendida é preservada, mas compõem efeito apenas quando implementar na nuvem.

<a name="caching_properties"></a> 
### <a name="caching-properties"></a>Propriedades de colocação em cache ###

Abrir o menu de contexto para a função no painel de Project Explorer de Eclipse, clique em **Azure**e, em seguida, clique em **cache**. Dentro desta caixa de diálogo, pode ativar a caches compatível com o memcache cocriação localizados com nome, permitindo-lhe para o ajudar a acelerar a suas aplicações web.

![][ic719483]

Dentro de uma página de propriedade **Colocação em cache** , pode especificar as definições globais para as seguintes opções:

* Se o cocriação localizado a colocação em cache está ativada.
* o tamanho da cache como uma percentagem de memória.
* o nome de conta de armazenamento para guardar o estado da cache quando a aplicação é executado como um serviço na nuvem ou nenhum se não pretender guardar o estado da cache. (O nome da conta de armazenamento não é utilizado quando executar a sua aplicação no emulador de cluster.) Se definir o nome da conta de armazenamento para **(automático)** (que é a predefinição), a configuração de colocação em cache automaticamente irá utilizar a mesma conta de armazenamento como a que selecionar na caixa de diálogo **publicar no Azure** .

>[AZURE.NOTE] A definição de **(automático)** terá o efeito pretendido apenas se publicar a sua implementação utilizando o toolkit de Eclipse Assistente de publicação. Se em vez disso, publicar o ficheiro de .cspkg manualmente utilizando um mecanismo externo, como o [Portal de gestão do Azure][], a implementação não irá funcionar corretamente.

A caixa de diálogo seguinte mostra as propriedades de uma cache.

![][ic719501]

* **Nome:** O nome da cache de cocriação localizado.
* **Número de porta:** O número de porta a utilizar para a cache.
* **Política de expiração:** Um dos seguintes valores que especifica quando uma chave na cache expira.
    * **Absoluta:** A tecla expira quando o tempo especificado pelo **minutos para live** é atingido.
    * **NeverExpires:** A tecla não tem uma hora de expiração.
    * **SlidingWindow:** A tecla expira se não foi acedido durante o período de tempo especificado pelo **minutos para live**; Sempre que é acedida, é repor o relógio de expiração.
* **Minutos para live:** Número máximo de minutos para uma chave de memcached para live sujeito a política de expiração.
* **Elevada disponibilidade com cópias de segurança replicadas no instâncias de outra função:** Se ativada, a ajuda a fornece recorrendo elevada disponibilidade de replicadas cópias de segurança no instâncias de função diferente. Tenha em atenção que, pelo menos, duas instâncias de função tem de ser em efeito para a sua implementação para esta funcionalidade trabalhar.

Para adicionar uma nova cache, clique no botão **Adicionar** na página de propriedades de **Colocação em cache** e será aberta uma caixa de diálogo **Configurar Cache com o nome** . Forneça os valores corretos para as propriedades que são descritas acima.

Para modificar uma cache com nome, selecione a cache e clique no botão **Editar** na página de propriedades de **Colocação em cache** . Será aberta uma caixa de diálogo, permitindo-lhe modificar as propriedades de cache. Prima **OK** para guardar os valores de cache.

Para eliminar uma cache, selecione a cache e clique no botão **Remover** na página de propriedades de **Colocação em cache** e, em seguida, clique em **Sim** para confirmar a eliminação.

Para mais informações sobre como utilizar a colocação em cache, veja [como utilizar Co-located colocação em cache][].

<a name="certificates_properties"></a> 
### <a name="certificates-properties"></a>Propriedades de certificados ###

Abrir o menu de contexto para a função no painel de Project Explorer de Eclipse, clique em **Azure**e, em seguida, clique em **certificados**.

![][ic710964]

Dentro desta caixa de diálogo, pode adicionar ou remover certificados referenciados por projeto Eclipse. Note que os certificados listados aqui não são automaticamente guardados dentro de qualquer keystore Java e, consequentemente, não estão automaticamente disponíveis para qualquer utilizar a partir de uma aplicação Java. Apenas são registadas com Azure para que estes podem ser carregados previamente para o Windows certificado armazenar em máquinas virtuais a executar a sua implementação e subsequentemente utilizado por outro software do Windows. Atualmente, a única funcionalidade do toolkit que utiliza os certificados referenciados desta forma na caixa de diálogo **certificados** é [SSL descarregar][], devido a sua dependência de serviços de informação Internet (IIS) e aplicação pedir encaminhamento (ARR), que requerem o certificado adequado para ser disponibilizados desta forma.

Quando implementar o projeto Azure utilizando o Assistente de publicação, vai ser-lhe para apontar para os ficheiros de intercâmbio de informações pessoais (PFX) correspondentes a estes certificados, juntamente com as suas palavras-passe, para poder carregar automaticamente para o serviço Azure, mas apenas se de que não foram carregadas aí anteriormente.

<a name="components_properties"></a> 
### <a name="components-properties"></a>Propriedades de componentes ###

Abrir o menu de contexto para a função no painel de Project Explorer de Eclipse, clique em **Azure**e, em seguida, clique em **componentes**. Dentro desta caixa de diálogo, tem a capacidade de adicionar, modificar, ou remover os componentes ao seu cargo, bem como alterar a ordem pela qual são processadas.

![][ic719502]

A funcionalidade de componentes permite-lhe adicionar dependências ao seu projeto de implementação do Azure, como Java projectos de aplicações, ficheiros especiais e declarações de linha de comandos executável que são necessários a sua implementação.

Para cada componente, pode especificar:

* Passo a tomar quando importar o componente para o seu projeto de implementação do Azure quando é criado.
* Passo a tomar quando implementar o componente na nuvem Azure.

>[AZURE.NOTE] Quando especificar ficheiros componente ou linhas de comandos, tenha em atenção que a implementação será publicada para uma máquina de virtual do Windows, para que os seus passos personalizados tem de ser válidos para um sistema operativo de baseados no Windows. 

Componentes têm as seguintes propriedades:

* **Importar:** Método de que indica como o componente será importado para o projeto quando o projeto é criado. Isto pode ser um dos seguintes valores:
    * **cópia:** O componente é copiado a partir do caminho local especificado pela propriedade **a partir do** directório de **approot** a função.
    * **AURICULAR:** O componente é um arquivo de empresa Java (AURICULAR) importado a partir de um projeto de aplicação empresarial no caminho local especificado pela propriedade **a partir de** . (Isto é detetado automaticamente pelo toolkit com base em da natureza do projeto nessa localização).
    * **JAR:** O componente é um arquivo de Java (para caixa) e é importado a partir de um projeto Java no caminho local especificado pela propriedade **a partir de** . (Isto é detetado automaticamente pelo toolkit com base em da natureza do projeto nessa localização).
    * **Nenhum:** Não tomadas para importar o componente. Este é aplicável quando o componente é considerado para já estar presentes no diretório de **approot** a função ou quando o componente é simplesmente uma declaração executável linha de comandos, conforme especificado na propriedade **como** quando o método de **Implementar** é **execução**.
    * **Guerra:** O componente é um arquivo de aplicação web do Java (guerra) e é importado a partir de um projecto dinâmica da Web no caminho local especificado pela propriedade **a partir de** . (Isto é detetado automaticamente pelo toolkit com base em da natureza do projeto nessa localização).
    * **zip:** O componente é um ficheiro zip e é importado por comprimir o directório ou o ficheiro especificado pela propriedade **a partir de** .
* **a partir do:** Caminho de origem no seu computador local para a pasta ou ficheiro que representa o item (NS) para importar para a sua implementação. Variáveis do ambiente Windows podem ser utilizadas nesta propriedade. Todos os componentes importáveis serão importados para o diretório de **approot** a função quando o projeto é criado.
    
    Tenha em atenção que tem a capacidade de implementar um componente de transferência de uma quando implementá-lo na nuvem (não o emulador cluster). Ver informação relacionada abaixo sobre a adição de um componente.    
    
* **As:** Nome do ficheiro em qual o componente será importado para o diretório de **approot** a função e finalmente implementado na nuvem Azure. Deixe esta propriedade em branco para manter o nome da mesma tal como está no computador local. (Para os componentes executáveis, isto é, aqueles cujo método **Implementar** está definido para **execução**, este pode ser uma declaração de linha de comandos do Windows arbitrária.)

    >[AZURE.IMPORTANT] Se utilizar carateres de espaço para este valor, estes irão ser processados de forma diferente consoante o método de implementar. Se o método de implementar for **execução**, espaços serão interpretados como separadores do argumento de linha de comandos e não como parte do nome do ficheiro. Para todos os outros implementar métodos, espaços serão interpretados como parte do nome do ficheiro.
    
* **Implementar:** Método de que indica a ação aplicada ao componente quando a implementação for iniciada. Isto pode ser um dos seguintes valores:
    * **cópia:** O componente é copiado para o caminho de destino especificado pela **** propriedade.
    * **execução:** O componente é uma declaração de linha de comandos Windows executável executada no contexto do caminho especificado por propriedade **para** , ao tempo que a implementação é iniciado.
    * **Nenhum:** Nenhuma ação é aplicada ao componente quando inicia a implementação.
    * **zip:** O componente é deszipado para o caminho de destino especificado pela **** propriedade. Este método só está disponível quando a propriedade de **Importar** é **postal**.
* **To:** Caminho de destino na máquina virtual onde o componente será implementado. Variáveis do ambiente Windows podem ser utilizadas nesta propriedade e caminhos de ficheiro são relativamente às **approot**.
    
Para adicionar um novo componente, clique no botão **Adicionar** na página de propriedades **componentes** e será aberta uma caixa de diálogo do **Azure componente de função** . Forneça os valores corretos para as propriedades que são descritas acima. 

A imagem seguinte mostra um exemplo para adicionar um novo componente de guerra.

![][ic719503]

Quando implementar na nuvem (não o emulador cluster), se pretender implementar o componente de transferência de uma, certifique-se de que **Quando estiver na nuvem, em vez de incluindo no pacote, implementar a partir de** está selecionada. Se pretende transferir a partir da sua conta de armazenamento Azure, selecione o armazenamento de conta a partir da lista pendente de **conta de armazenamento** (pode clicar na ligação de **contas** para modificar o que se encontra na lista), que irão parcialmente encontrar no campo **URL** e, em seguida, preencha a restante parte do URL. Se não pretende utilizar armazenamento Azure, selecione **(nenhum)** na lista pendente de **conta de armazenamento** e introduza o URL para o seu componente no campo **URL** . Especificar um dos seguintes métodos:

* **cópia:** O componente de transferência é copiado para o caminho de destino especificado pelo caminho do **Diretório para** .
* **mesmo:** O mesmo método utilizado para **Implementar a partir de transferência** , tal como para **Implementar a partir do pacote**.
* **zip:** O componente de transferência é deszipado para o caminho de destino especificado pelo caminho do **Diretório para** .

Para modificar um componente de, selecione o componente e clique no botão **Editar** na página de propriedades **componentes** . Será aberta uma caixa de diálogo, permitindo-lhe modificar as propriedades do componente. Prima **OK** para guardar os valores de componente.

Para eliminar um componente de, selecione o componente e clique no botão **Remover** na página de propriedades **componentes** e, em seguida, clique em **Sim** para confirmar a eliminação.

Componentes são processados na ordem listada. Utilize os botões **Mover para cima** e **Mover para baixo** para dispor a ordem.

>[AZURE.NOTE] A funcionalidade de configuração do servidor depende de componentes também. Os componentes não podem ser removidos ou editados sem remover a configuração do servidor correspondente. Será pedido sobre que ao tentar efetuar alterações a esses componentes.

<a name="debugging_properties"></a> 
### <a name="debugging-properties"></a>Depuração de propriedades ###

Abrir o menu de contexto para a função no painel de Project Explorer de Eclipse, clique em **Azure**e, em seguida, clique na **depuração**. Dentro desta caixa de diálogo, pode ter a capacidade de ativar ou desativar a depuração remota, bem como criar configurações depurar, conforme apresentado na seguinte imagem.

![][ic719504]

Para obter informações relacionadas sobre depuração, consulte o artigo [Depuração de aplicações do Azure no Eclipse][].

<a name="endpoints_properties"></a> 
### <a name="endpoints-properties"></a>Propriedades de pontos finais ###

Abrir o menu de contexto para a função no painel de Project Explorer de Eclipse, clique em **Azure**e, em seguida, clique em **pontos finais**. Dentro desta caixa de diálogo, tem a capacidade de criar um ponto final, bem como editar ou remover um ponto final, conforme apresentado na seguinte imagem.

![][ic719505]

Para adicionar um ponto final, clique no botão **Adicionar** na página **pontos finais** de propriedade e será aberta uma caixa de diálogo **Adicionar ponto final** .

![][ic710897]

Introduza um nome para o ponto final, selecione o tipo ( **Input**, **interna**ou **InstanceInput**) e especifique a porta pública e privada. Prima **OK** para guardar os novos valores de ponto final.

Dependendo do tipo de ponto final, poderá utilizar intervalos de portas da seguinte forma:

* Para um ponto final instância de entrada, a porta pública pode ser um intervalo de portas (por exemplo **2000-2010**) e a porta privada é um valor fixo.
* Para um ponto final interno, não é utilizada a porta pública e a porta privada pode ser um intervalo ou deixado em branco ou definido como um asterisco para indicá-lo automaticamente está definido por Azure.
* Para os pontos finais entrados, a porta pública só pode ser um valor fixo, e a porta privada pode ser um valor fixo ou deixado em branco ou definido como um asterisco para indicar a configurar automaticamente ao Azure.

Se pretender utilizar um número de porta única em vez de um intervalo, deixe a caixa de texto para o fim do intervalo branco.

Para portas que estão definidas para se automático, o precisa determinar qual a porta realmente é utilizada durante a runtime, a aplicação pode utilizar a API do tempo de execução do serviço Azure, que é documentado no [pacote com.microsoft.windowsazure.serviceruntime resumo][].

Para ver como os pontos finais de entrada instância podem ser utilizados para o ajudar com a depuração de uma implementação com várias instâncias, consulte o artigo [depuração de uma instância de função específica numa implementação com várias instâncias][].

Para modificar um ponto final, selecione o ponto final e clique no botão **Editar** na página **pontos finais** de propriedade. Será aberta uma caixa de diálogo, permitindo-lhe modificar o nome do ponto final, tipo e portas públicas e privadas. Prima **OK** para guardar os valores de ponto final modificados.

Para eliminar um ponto final, selecione o ponto final e clique no botão **Remover** na página **pontos finais** de propriedade e, em seguida, clique em **Sim** para confirmar a eliminação.

Para configurar algumas das funcionalidades (tal como a colocação em cache, depuração remota, afinidade sessão ou SSL descarregar) ativadas pelo utilizador numa função correctamente, o toolkit pode configurar automaticamente os pontos finais especiais que serão listados juntamente com os pontos finais definidos pelo utilizador. O toolkit impede que o utilizador editar ou eliminar tal automaticamente gerado pontos finais desde que a funcionalidade associada está ativada.

<a name="environment_variables_properties"></a> 
### <a name="environment-variables-properties"></a>Propriedades de variáveis de ambiente ###

Abrir o menu de contexto para a função no painel de Project Explorer de Eclipse, clique em **Azure**e, em seguida, clique em **Ambiente de variáveis**. Dentro esta caixa de diálogo, tem a capacidade de criar uma variável de ambiente, bem como modificar ou remover uma variável de ambiente, conforme apresentado na seguinte imagem.

![][ic719506]

Variáveis de ambiente estão disponíveis para o script de arranque quando a função é iniciado.

>[AZURE.NOTE] Quando especificar variáveis de ambiente, tenha em atenção que a implementação será publicada para uma máquina de virtual do Windows, pelo que variáveis de ambiente devem ser válidas para um sistema operativo de baseados no Windows.

Como um exemplo de uma variável de ambiente a ser disponível quando a função é iniciado, crie uma nova variável de ambiente clicando no botão **Adicionar** . A imagem seguinte mostra uma variável de ambiente denominada **MyRoleVersion** a ser criada e atribuída ao valor **1.0**.

![][ic659268]

No seu código jsp, pode apresentar o valor a utilizar o `System.getenv` método:

    <body>
      <b> Hello World!</b>
      <p>Running role version: <%= System.getenv("MyRoleVersion") %></p>
    </body>

Que resulta neste resultado quando a aplicação é executado:

![][ic552233]

Para modificar uma variável de ambiente, selecione a variável de ambiente e clique no botão **Editar** na página de propriedades de **Variáveis de ambiente** . Será aberta uma caixa de diálogo, permitindo-lhe modificar as propriedades de variável de ambiente. Prima **OK** para guardar o ambiente de valores de variáveis.

Para eliminar uma variável de ambiente, selecione a variável de ambiente e clique no botão **Remover** na página de propriedades de **Variáveis de ambiente** e, em seguida, clique em **Sim** para confirmar a eliminação.

Para configurar algumas das funcionalidades (como a configuração do servidor, depuração remota ou armazenamento Local) ativado pelo utilizador numa função correctamente, o toolkit pode configurar automaticamente variáveis de ambiente especial que estará listadas juntamente com as variáveis de ambiente definidas pelo utilizador. O toolkit impede que o utilizador editar ou eliminar tal automaticamente gerado variáveis de ambiente desde que a funcionalidade associada está ativada.

<a name="session_affinity_properties"></a> 
### <a name="load-balancing--session-affinity-aka-sticky-sessions-properties"></a>Balanceamento de carga / propriedades da sessão afinidade (a.k.a "sessões autocolantes") ###

Abrir o menu de contexto para a função no painel de Project Explorer de Eclipse, clique em **Azure**e, em seguida, clique em **Balanceamento de carga**. Dentro desta caixa de diálogo, tem a capacidade de ativar ou desativar afinidade sessão, conforme apresentado na seguinte imagem.

![][ic719492]

Para obter informações relacionadas, consulte o artigo [Afinidade sessão][]. Além disso, tenha em atenção comportamento esta funcionalidade no contexto de SSL descarregar, tal como descrito em [Descarregar SSL][].

<a name="local_storage_properties"></a> 
### <a name="local-storage-properties"></a>Propriedades de armazenamento local ###

Abrir o menu de contexto para a função no painel de Project Explorer de Eclipse, clique em **Azure**e, em seguida, clique em **Armazenamento Local**. Dentro desta caixa de diálogo, tem a capacidade de criar, modificar ou remover temporário armazenamento local para a máquina virtual que está a ser executado a aplicação. Valores específicos podem ser definidos para o tamanho do armazenamento local, bem como se os conteúdos são preservados quando a função é reciclada, conforme apresentado na seguinte imagem.

![][ic719508]

Opcionalmente, também pode especificar uma variável de ambiente que corresponde ao armazenamento local.

Por predefinição, tudo o que implementar para Azure é colocado (e descompactado) na pasta **approot** da instância função. Enquanto implementações mais simples irão ajustar ao documento mesmo depois unzipping, o espaço atribuído para o diretório **approot** é limitado e bem definidos (menos de 1 GB é uma razoável regra útil). Por conseguinte, para assegurar que Azure atribui espaço em disco suficiente para implementações maiores que não poderão ajustá-las na pasta **approot** , deve configurar um recurso de armazenamento local utilizando a caixa de diálogo **Armazenamento Local** . Para uma forma fácil de fazer isto, consulte o artigo [Implementar grandes implementações][].

Pode referenciar facilmente o recurso de armazenamento de scripts de arranque (por exemplo, o **startup.cmd**) utilizando a variável de ambiente automaticamente associada ao toolkit de Eclipse ao recurso, conforme apresentado na caixa de diálogo **Armazenamento Local** . Essa variável de ambiente irá conter o caminho completo da recursos locais que configurou no momento que é executado o script de arranque. 

Para modificar um recurso de armazenamento local, selecione o recurso de armazenamento local e clique no botão **Editar** na página de propriedades de **Armazenamento Local** . Será aberta uma caixa de diálogo, permitindo-lhe modificar as propriedades do recurso armazenamento local. Prima **OK** para guardar os valores do recurso armazenamento local.

Para eliminar um recurso de armazenamento local, selecione o recurso de armazenamento local e clique no botão **Remover** na página de propriedades de **Armazenamento Local** e, em seguida, clique em **Sim** para confirmar a eliminação.

<a name="server_configuration_properties"></a> 
### <a name="server-configuration-properties"></a>Propriedades do servidor de configuração ###

Abrir o menu de contexto para a função no painel de Project Explorer de Eclipse, clique em **Azure**e, em seguida, clique em **Configuração do servidor**. Dentro esta caixa de diálogo, têm a capacidade de adicionar, remover e modificar o servidor de aplicação JDK e Java utilizado pela sua implementação, bem como adicionar ou remover as aplicações (como ficheiros guerra, para caixa ou AURICULAR) utilizadas pelo sua implementação.

### <a name="jdk-configuration"></a>Configuração de JDK ###

Esta caixa de diálogo permite-lhe especificar o pacote JDK a utilizar para a sua implementação. Se estiver a utilizar Eclipse no Windows, pode especificar o pacote JDK utilizar localmente quando a ser executada no Azure emulador e tem a opção para implementar a instalação local Azure. Em sistemas operativos não Windows, a definição de JDK emulador não é aplicável e não é possível implementar a JDK localmente instalada uma vez que não é compatível com o Windows. No entanto, independentemente do sistema operativo que está a utilizar, pode sempre escolher entre os pacotes JDK festa 3º para implementar o Azure ou aponte para sua própria pacote JDK compatível com o Windows a partir de uma localização de transferência alternativo.

Segue-se um exemplo de como pode especificar um JDK no Windows:

![][ic780647]

Se estiver a utilizar Eclipse no Windows, pode especificar um JDK para utilizar com o emulador cluster; Para fazê-lo, certifique-se de **que utilização JDK a partir deste caminho de ficheiro para testar a ligação localmente** está selecionada na secção **emulador implementação** . Em seguida, especifique o caminho local para o seu JDK; Pode navegar para diferentes JDK se aquele que pretende utilizar não estiver selecionada automaticamente. Também tem a opção para implementar o seu JDK no seu serviço de nuvem Azure; Para fazê-lo, selecione a opção de **Implementar meu JDK local (carregar automaticamente para o armazenamento em nuvem)** na secção de **implementação de nuvem** .

Nota: Em sistemas operativos não Windows, as definições de **implementação de emulador** e a opção de **Implementar meu JDK local** não estão disponíveis. O exemplo seguinte ilustra especificando uma JDK num Mac ou de outro sistema de operativo não Windows suportado:

![][ic789643]

Independentemente do sistema operativo que estiver, tem as seguintes duas opções de **implementação de nuvem** para a origem e o tipo de seu pacote JDK:

* **Implementar um pacote JDK de festa 3º disponível no Azure** 
* **Implementar a partir de uma transferência personalizada** 

Se estiver a utilizar a opção de **Implementar uma 3 festa pacote JDK disponível a partir do Azure** :

1. Selecione a caixa de verificação denominada **Implementar um 3 festa pacote JDK disponível a partir do Azure**.
1. A partir da lista pendente, selecione o pacote JDK festa 3º que está disponível no Azure.
1. O separador **JDK** terá uma aspeto semelhante ao seguinte no Windows:  ![][ic780648] 
    e terá aspeto semelhante ao seguinte no Mac OS ou suportadas outros sistemas operativos não Windows: ![][ic789643]
1. Clique em **OK** para guardar as alterações.
1. Quando lhe for pedido para aceitar o contrato de licença do fornecedor de pacote festa JDK 3º, reveja os termos de licenciamento. Assumindo que aceite os termos, clique em **Sim** para fechar a caixa de diálogo do **contrato de licença aceitar** .
    Tenha em atenção que a lógica subjacente para a qual os itens são apresentados na lista pendente para a opção de **Implementar uma 3 festa pacote JDK disponível a partir do Azure** pode ser personalizada. Para personalizar os itens, na caixa de diálogo **JDK** , clique na ligação **Personalizar** . Isto irá fechar a página de propriedade **JDK** e abra o ficheiro **componentsets.xml** Eclipse, que pode modificar, em seguida, conforme necessário. Documentação para **componentsets.xml** é incluída no próprio ficheiro **componentsets.xml** .

Se estiver a utilizar a opção de **Implementar uma JDK a partir de uma transferência personalizada** :

1. Crie um postal do seu diretório de instalação JDK, garantir que o próprio nó de diretório é subordinado da estrutura ZIP e não os seus conteúdos. Tome nota do nome do diretório, à medida que vai precisar de mais tarde e tenha em atenção que esta instalação JDK será implementada para uma máquina de virtual do Windows.
1. Carregar postal para a sua conta de armazenamento Azure como um blob. Pode fazer esta utilizando uma ferramenta externamente disponível para o carregamento dos blobs ao armazenamento Azure. Recomenda-se para utilizar um blob privado. Tome nota do URL BLOBs do conteúdo postal.
1. Selecione a caixa de verificação denominada **Implementar um JDK a partir de uma transferência personalizada**.
    Se pretende transferir a partir da sua conta de armazenamento Azure, selecione o armazenamento de conta a partir da lista pendente de **conta de armazenamento** (pode clicar na ligação de **contas** para modificar o que se encontra na lista), que irão parcialmente encontrar no campo **URL** e, em seguida, preencha a restante parte do URL. Se não pretende utilizar armazenamento Azure, selecione **(nenhum)** na lista pendente de **conta de armazenamento** e introduza o URL para o transferir JDK no campo **URL** . Se utilizar o armazenamento Azure, tem de ser minúsculas blob nomes no URL.
1. Certifique-se de que a caixa de texto **JAVA_HOME** refere-se para o nome do diretório correto. Por predefinição, referenciam o mesmo nome de diretório JDK como o valor que escolher para a sua utilização local. No entanto, se o diretório contido a ZIP tem um nome diferente (por exemplo, devido ao utilizar uma versão diferente), atualize o nome do directório na caixa de texto **JAVA_HOME** , por conseguinte, uma vez que esta definição será utilizada na nuvem (não está na emulador cluster).
1. Clique em **OK** para guardar as alterações.

Já está. Agora, quando constrói para o cloud, irá reparar o tamanho de pacote será muito mais pequeno, o processo de criação, normalmente, deverá demorar menos tempo e a implementação propriamente dito durante a publicação para a nuvem também deverá demorar menos tempo. Tenha em atenção que as opções de **Implementar meu JDK local (carregar automaticamente para o armazenamento em nuvem)** ou **Implementar uma JDK a partir de uma transferência personalizada** estão em efeito apenas quando a aplicação é implementada na nuvem. Se não tem qualquer efeito na sua experiência de emulador cluster; a versão local dos componentes continuará a ser utilizada quando implementar o emulador cluster. 

### <a name="server-configuration"></a>Configuração do servidor ###

Segue-se um exemplo de como pode especificar um servidor de aplicações.

![][ic796926]

Certifique-se de que a caixa de verificação **Implementar um servidor deste tipo** está selecionada e, em seguida, selecione o tipo de servidor de aplicação que pretende utilizar.

Para especificar um servidor para utilizar para implementação na nuvem, pode tirar partido das seguintes opções:

1. **Implementar um 3 festa o servidor está disponível no Azure** - isto é especialmente aplicável em Dev Center/testar cenários onde eficiência de implementação e simplificar é uma prioridade e o servidor não requer uma configuração personalizada. Ou quando que pretende utilizar um desses servidores como ponto de partida, mas inclui passos de personalização de servidor adequado no lógica de arranque da sua implementação.
1. **Implementar a partir de uma transferência personalizada** - este é especialmente aplicável em cenários de produção quando tiver um servidor especialmente preparado e configurado que pretende utilizar na nuvem.
1. **Implementar minha instalação do servidor local** - isto é especialmente aplicável no se a instalação do servidor local já está a ser configuradas personalizada para a sua utilização. Se selecionar esta opção, tem de especificar caminho do seu servidor local no **caminho servidor Local da** caixa de texto abaixo.

Se estiver a utilizar a opção de **Implementar uma 3 festa o servidor está disponível no Azure** :

1. Selecione a caixa de verificação denominada **Implementar um 3 festa o servidor está disponível no Azure**.
1. A partir do menu pendente, selecione o software de servidor pretendido para utilizar com a sua implementação na nuvem. Nota, se já tiver especificado um tipo de servidor para utilizar versões anteriores, será limitado a escolher apenas um servidor de nuvem que está na mesma família como esse tipo de servidor. Mas, se não tiver escolhido um tipo de servidor, pode escolher a partir de qualquer um dos servidores que estão atualmente disponíveis no Azure e o tipo de servidor será seleccionado automaticamente para si.
1. Clique em **OK** para guardar as alterações.

Se utilizar a opção de **Implementar a partir de uma transferência personalizada** :

1. Certifique-se de que selecionou já um tipo de servidor de acordo com os passos anteriores. Isto indica o plug-in como implementar o servidor a partir do seu transferir personalizado, tal como tem de ser a partir da mesma família de como o seu tipo de servidor seleccionado.
1. Selecione a caixa de verificação denominada **Implementar a partir de uma transferência personalizada**.
    Se pretende transferir da sua conta de armazenamento Azure, selecione o armazenamento de conta a partir da lista pendente de **conta de armazenamento** (pode clicar na ligação de **contas** para modificar o que se encontra na lista), que irão parcialmente encontrar no campo **URL** e, em seguida, preencha a restante parte do URL para o seu servidor transferir ZIP (quando utilizar o armazenamento Azure, nomes de BLOBs no URL tem de ser em minúsculas). Se não pretende utilizar armazenamento Azure, selecione **(nenhum)** na lista pendente de **conta de armazenamento** e introduza o URL para o seu servidor ZIP no campo **URL** . O ZIP iria conter uma pasta de subordinados que representa o seu diretório de instalação do servidor de aplicação. Por exemplo, se estiver a utilizar um postal para Apache Tomcat 7.0.35, dentro de postal seria a pasta de subordinados que representa o diretório de instalação, tal como **apache-tomcat-7.0.35**. 
1. Especifique o valor para a variável de ambiente de diretório. Predefinição será o valor utilizado para o seu servidor de aplicação local, se existirem, mas pode especificar um valor diferente se o servidor de aplicações na nuvem é diferente do seu servidor de aplicação local. No entanto, é necessário para se certificar de que o servidor de aplicações na nuvem é da mesma família como o servidor de tipo que selecionou anteriormente.
    Se atualizar o seu zip de servidor de aplicação na nuvem no futuro, pode alterar manualmente a definição de diretório, ou, para que seja corresponder novamente a sua definição local (se tiver alterado o seu servidor de aplicação local demasiado).
1. Clique em **OK** para guardar as alterações.

A lógica subjacente para a qual os itens são apresentados no separador **servidor** da página de propriedades de **Configuração do servidor** pode ser personalizada. Este é uma funcionalidade avançada que poderá ter se as suas necessidades expandem para além dos valores predefinidos ou se pretender adicionar outros servidores. Para personalizar a lógica, na caixa de diálogo **servidor** , clique na ligação **Personalizar** . Isto irá fechar a página de propriedades de **Configuração do servidor** e abra o ficheiro **componentsets.xml** Eclipse, que, em seguida, pode modificar, conforme necessário para expandir o modelo de configuração do servidor. Documentação para **componentsets.xml** é incluída no próprio ficheiro **componentsets.xml** .

Se estiver a utilizar a opção de **Implementar o meu servidor local (carregar automaticamente para o armazenamento em nuvem)** :

1. Selecione a caixa de verificação denominada **Implementar o meu servidor local (carregar automaticamente para o armazenamento em nuvem)**.
1. Utilizando a lista pendente de **conta de armazenamento** , selecione **(automático)**. Se especificar **(automático)** aqui, o toolkit de Eclipse irá utilizar a mesma conta de armazenamento para o seu servidor como aquele de que selecionar para a sua implementação na caixa de diálogo **publicar no Azure** .
1. Clique em **OK** para guardar as alterações.

Selecione um caminho de instalação do servidor no seu computador na caixa de texto **caminho de servidor Local** se qualquer uma das condições seguintes forem verdadeiras:

* Que pretende testar a sua implementação no emulador (aplicável apenas ao Windows).
* Pretende implementar o seu servidor instalado localmente para a nuvem.
* Que pretende utilizar a transferência de servidor personalizado do seu próprio na nuvem, na qual caso, também certificar-se que a opção de **Implementar o meu servidor local (carregar automaticamente para o armazenamento em nuvem)** está selecionada acima.

Se nenhuma das opções anteriores se aplicar à sua situação, a definição de servidor local é opcional.

### <a name="applications-configuration"></a>Configuração de aplicações ###

Segue-se um exemplo de como pode especificar uma aplicação.

![][ic719512]

Clique em **Adicionar** para adicionar outra aplicação ou **Remover** para remover uma aplicação. Para fins de eficiência, se pretender utilizar uma transferência para a origem de uma aplicação do quando implementar na nuvem, utilize as [Propriedades de componentes](#components_properties) para especificar um URL, conta de armazenamento, etc. 

Começando com a versão de Abril de 2014, suas aplicações são automaticamente carregadas para a mesma conta de armazenamento (em contentor **eclipsedeploy** ) como um selecionado para a sua implementação. A lógica de arranque do sua implementação contém um passo que pela primeira vez transfere nessas aplicações a partir dessa conta de armazenamento. Isto significa que pode atualizar as aplicações na sua implementação sem necessitar da reconstruir e implementar o pacote completo, ao carregá manualmente as versões mais recentes da aplicação diretamente para essa conta de armazenamento (utilizando o portal do Azure por exemplo), substituir os ficheiros de guerra originalmente carregados aí pelo toolkit. Em seguida, basta inicie a Reciclagem de todas as instâncias essas funções com o portal de gestão do Azure novamente ou através de utilitários de linha de comandos. (Acionar função Reciclagem diretamente a partir de dentro o toolkit de Eclipse não é suportada.)

### <a name="notes-about-server-configuration"></a>Notas sobre a configuração do servidor ###

Alterações que efetuou através da página de propriedades de **configuração do servidor** serão refletidas na `<component>` elementos do ficheiro Package.

Quando utilizar a **carregar automaticamente …** ou **Implementar a partir de transferência …** opções para o JDK ou servidor de aplicações e estão a criar para o cloud (não o emulador cluster) e está ligado à rede, poderá notar a criar mensagens como as seguintes na saída do consola, tal como o construtor de conselhos verifica a disponibilidade da transferência:

`[windowsazurepackage] Verifying blob availability (https://example.blob.core.windows.net/temp/tomcat6.zip)...` 

Se selecionou a opção de **Implementar a partir de transferência...** , o seguinte aviso pode ser apresentado, mas continuará a compilação:

`[windowsazurepackage] warning: Failed to confirm blob availability! Make sure the URL and/or the access key is correct (https://example.blob.core.windows.net/temp/tomcat6.zip).` 

Este aviso é a única indicação de que não tiver sido verificada disponibilidade a transferência. Por isso, se uma implementação falhar na nuvem, por alguma razão, verifique se recebeu este aviso.

Se pretende desativar a verificação de transferência (por exemplo, se se sentir-desnecessariamente mais lento a compilação), defina o `verifydownloads` atributo para `false` na `<windowsazurepackage>` elemento do Package: 

`<windowsazurepackage verifydownloads="false" ...>` 

Se tiver selecionado a opção **carregar automaticamente...** , em seguida, na janela da consola irá ver mensagens de compilação comunicar o progresso do carregamento 5 segundos, sempre que um carregamento é necessário.

<a name="ssl_offloading_properties"></a> 
### <a name="ssl-offloading-properties"></a>SSL descarregar propriedades ###

Abrir o menu de contexto para a função no painel de Project Explorer de Eclipse, clique em **Azure**e, em seguida, clique em **SSL descarregar**. 

![][ic719481]

Dentro desta caixa de diálogo, pode ativar a SSL descarregar, permitindo-lhe facilmente activar o suporte de HTTPS Hypertext Transfer Protocol seguro () na sua implementação Java no Azure, sem que seja necessário configurar SSL no seu servidor de aplicação Java. Para mais informações, consulte o artigo [SSL descarregar][] e [como utilizar SSL descarregar][].

## <a name="see-also"></a>Consulte também ##

[Toolkit Azure para Eclipse][]

[Instalar o Toolkit Azure para Eclipse][]

[Criar uma aplicação do mundo Olá Azure no Eclipse][]

[Propriedades do projeto Azure][]

[Lista de conta de armazenamento Azure][]

Para mais informações sobre como utilizar o Azure com Java, consulte o [Centro de programadores do Azure Java][].

<!-- URL List -->

[Centro de programadores do Azure Java]: http://go.microsoft.com/fwlink/?LinkID=699547
[Portal de gestão do Azure]: http://go.microsoft.com/fwlink/?LinkID=512959
[Toolkit Azure para Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Propriedades do projeto Azure]: http://go.microsoft.com/fwlink/?LinkID=699524
[Lista de conta de armazenamento Azure]: http://go.microsoft.com/fwlink/?LinkID=699528
[pacote com.microsoft.windowsazure.serviceruntime resumo]: http://azure.github.io/azure-sdk-for-java/com/microsoft/windowsazure/serviceruntime/package-summary.html
[Criar uma aplicação do mundo Olá Azure no Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699533
[Depuração de uma instância de função específica numa implementação com várias instâncias]: http://go.microsoft.com/fwlink/?LinkID=699535#debugging_specific_role_instance
[Depuração de aplicações do Azure no Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699535
[Implementar grandes implementações]: http://go.microsoft.com/fwlink/?LinkID=699536
[Como utilizar a colocação em cache cocriação localizado]: http://go.microsoft.com/fwlink/?LinkID=699542
[Como utilizar SSL descarregar]: http://go.microsoft.com/fwlink/?LinkID=699545
[Instalar o Toolkit Azure para Eclipse]: http://go.microsoft.com/fwlink/?LinkId=699546
[Afinidade sessão]: http://go.microsoft.com/fwlink/?LinkID=699548
[Descarregar SSL]: http://go.microsoft.com/fwlink/?LinkID=699549

<!-- IMG List -->

[ic789599]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic789599.png
[ic719499]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719499.png
[ic719483]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719483.png
[ic719501]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719501.png
[ic710964]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic710964.png
[ic719502]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719502.png
[ic719503]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719503.png
[ic719504]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719504.png
[ic719505]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719505.png
[ic710897]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic710897.png
[ic719506]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719506.png
[ic659268]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic659268.png
[ic552233]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic552233.png
[ic719492]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719492.png
[ic719508]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719508.png
[ic780647]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic780647.png
[ic789643]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic789643.png
[ic780648]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic780648.png
[ic789643]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic789643.png
[ic796926]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic796926.png
[ic719512]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719512.png
[ic719481]: ./media/azure-toolkit-for-eclipse-azure-role-properties/ic719481.png
