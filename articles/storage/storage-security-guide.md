<properties
    pageTitle="Guia de segurança do Azure armazenamento | Microsoft Azure"
    description="Detalhes de vários métodos de proteger o armazenamento do Windows Azure, incluindo, mas não limitado a RBAC, encriptação do serviço de armazenamento, encriptação do lado do cliente, SMB 3.0 e encriptação do Azure do disco."
    services="storage"
    documentationCenter=".net"
    authors="robinsh"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="09/08/2016"
    ms.author="robinsh"/>

#<a name="azure-storage-security-guide"></a>Guia de segurança de armazenamento Azure

##<a name="overview"></a>Descrição geral

Armazenamento Azure fornece um conjunto abrangente de capacidades de segurança em conjunto permitir que os programadores criem aplicações seguras. A conta de armazenamento propriamente dita pode ser protegida utilizando o controlo de acesso baseado em funções e o Azure Active Directory. Dados podem ser protegidos em trânsito entre uma aplicação e Azure ao utilizar a [Encriptação do lado do cliente](storage-client-side-encryption.md), HTTPS ou SMB 3.0. Dados podem ser definidos encriptados automaticamente quando escritas armazenamento do Windows Azure utilizando o [Serviço de armazenamento encriptação (SSE)](storage-service-encryption.md). Sistema operativo e dados discos utilizados por máquinas virtuais podem ser definidos para ser encriptado através de [Encriptação do Azure do disco](../security/azure-security-disk-encryption.md). Pode ser concedido acesso delegado para os objetos de dados no armazenamento Azure utilizar [Assinaturas de acesso partilhado](storage-dotnet-shared-access-signature-part-1.md).

Este artigo irá fornecer uma descrição geral de cada uma destas funcionalidades de segurança podem ser utilizadas com armazenamento do Windows Azure. Ligações são fornecidos para artigos que irão dar-detalhes de cada funcionalidade-o que pode fazer facilmente inquérito sobre cada tópico posterior.

Aqui estão os tópicos para ser abordados neste artigo:

-   [Segurança do plano de gestão](#management-plane-security) – proteger a sua conta de armazenamento

    O plano de gestão é composta pelos recursos utilizados para gerir a sua conta de armazenamento. Nesta secção, irá falarmos sobre o modelo de implementação do Gestor de recursos do Azure e como utilizar o controlo de acesso baseado em funções RBCA () para controlar o acesso às suas contas de armazenamento. Recomendamos também serão falar sobre como gerir as suas chaves de conta de armazenamento e como a gerá-los.

-   [Segurança de plano de dados](#data-plane-security) – proteger o acesso aos seus dados

    Nesta secção, abordaremos permissão do acesso aos objetos de dados reais na sua conta de armazenamento, como blobs, ficheiros, filas e tabelas, utilizar assinaturas de acesso partilhado e armazenados políticas de acesso. Abordaremos SA de nível de serviço e SA ao nível da conta. Recomendamos também verá como limitar o acesso a um endereço IP específico (ou intervalo de endereços IP), como limitar o protocolo utilizado para HTTPS e como revogar uma assinatura de acesso partilhado sem aguardá-lo a expirar.

-   [Encriptação em trânsito](#encryption-in-transit)

    Esta secção explica como proteger dados quando são transferidas ou desaparecer armazenamento do Windows Azure. Iremos falar sobre a utilização recomendada de HTTPS e a encriptação utilizado pelo SMB 3.0 para Azure partilhas de ficheiros. Recomendamos também irá demorar um olhar sobre a encriptação do lado do cliente, que permite-lhe para encriptar os dados antes de serem transferidas para o armazenamento numa aplicação cliente e desencriptar os dados após a transferência armazenamento.

-   [Encriptação em repouso](#encryption-at-rest)

    Irá falarmos sobre encriptação de serviço de armazenamento (SSE) e como pode ativá-la para uma conta de armazenamento, que resulta no seu bloco de blobs, blobs de página e acrescentar blobs automaticamente a ser encriptados quando escritas armazenamento do Windows Azure. Vamos também abordar como pode utilizar a encriptação de disco do Azure e explore as diferenças básicas e casos de encriptação do disco versus SSE versus encriptação do lado do cliente. Vamos abordar conformidade FIPS para computadores de Governo dos Estados Unidos brevemente.

-   Utilizar [A análise de armazenamento](#storage-analytics) para auditar o acesso de armazenamento do Windows Azure

    Esta secção explica como localizar informações nos registos de análise de armazenamento para um pedido. Vamos veja a análise de armazenamento real os dados do registo e veja como a identificação se um pedido de for feito com a chave de conta de armazenamento, com uma assinatura de acesso partilhado ou forma anónima e se teve êxito ou falhou.

-   [Activar baseada no Browser e clientes utilizando CORS](#Cross-Origin-Resource-Sharing-CORS)

    Esta secção fala sobre como permitir que o recurso de origem cruz partilha (CORS). Iremos falar sobre acesso entre domínios e como geri-la com as capacidades CORS incorporadas no armazenamento do Windows Azure.

##<a name="management-plane-security"></a>Segurança do plano de gestão

O plano de gestão é composta por operações que afetam a conta de armazenamento propriamente dita. Por exemplo, pode criar ou eliminar uma conta de armazenamento, obter uma lista de contas de armazenamento numa subscrição, obter as chaves de conta de armazenamento ou as teclas de conta de armazenamento a gerar.

Quando cria uma nova conta de armazenamento, selecionar um modelo de implementação de clássico ou o Gestor de recursos. O modelo clássico de criação de recursos no Azure só permite o acesso tipo tudo ou nada para a subscrição e, por sua vez, a conta de armazenamento.

Este guia foca-se em Gestor de recursos modelo que está o meio recomendado para criar contas do armazenamento. Com as contas de armazenamento do Gestor de recursos, em vez de com acesso à toda a subscrição, pode controlar o acesso num nível mais finito para o plano de gestão utilizando o controlo de acesso baseado em funções RBCA ().

###<a name="how-to-secure-your-storage-account-with-role-based-access-control-rbac"></a>Como proteger a sua conta de armazenamento com o controlo de acesso baseado em funções RBCA)

Vamos falar sobre as RBAC e como pode utilizá-lo. Cada subscrição Azure tem um Azure Active Directory. Os utilizadores, grupos e aplicações a partir desse directório a podem ser concedidas acesso a gerir os recursos na subscrição do Azure que utilizam o modelo de implementação do Gestor de recursos. Isto é referido como controlo de acesso baseado em funções RBCA (). Para gerir o acesso a este, pode utilizar o [Azure portal](https://portal.azure.com/), as [Ferramentas do Azure clip](../xplat-cli-install.md), [PowerShell](../powershell-install-configure.md)ou do [Azure armazenamento recurso fornecedor REST APIs](https://msdn.microsoft.com/library/azure/mt163683.aspx).

Com o modelo de Gestor de recursos, coloque a conta de armazenamento no access de grupo e controlo de recursos para o plano de gestão dessa conta de armazenamento específicas utilizando o Azure Active Directory. Por exemplo, pode dar utilizadores específicos a capacidade de aceder às teclas de conta de armazenamento, enquanto que outros utilizadores podem ver informações acerca da conta de armazenamento, mas não é possível aceder as teclas de conta de armazenamento.

####<a name="granting-access"></a>Conceder acesso

Acesso é concedido através da atribuição o papel RBAC adequado para os utilizadores, grupos e aplicações, no âmbito à direita. Para conceder acesso a toda a subscrição, atribuir uma função ao nível da subscrição. Pode conceder acesso a todos os recursos num grupo de recursos ao conceder permissões ao grupo de recursos própria. Também pode atribuir funções específicas para recursos específicos, como contas de armazenamento.

Eis os principais elementos que precisa de saber sobre a utilização RBAC para aceder a operações de gestão de uma conta de armazenamento do Windows Azure:

-   Ao atribuir acesso, que mostra basicamente atribuir uma função para a conta que pretende para ter acesso. Pode controlar o acesso às operações utilizadas para gerir essa conta de armazenamento, mas não para os objetos de dados na conta. Por exemplo, pode conceder permissão para obter as propriedades da conta de armazenamento (como redundância), mas não para um contentor ou de dados de um contentor dentro de armazenamento de Blobs.

-   Para uma pessoa de ter permissão para aceder aos dados de objectos na conta de armazenamento, pode dar permissão para ler as teclas de conta de armazenamento e que o utilizador, em seguida, pode utilizar essas chaves para aceder a blobs, filas, tabelas e ficheiros.

-   Funções podem ser atribuídas a uma conta de utilizador específico, um grupo de utilizadores, ou para uma aplicação específica.

-   Cada função tem uma lista de ações e não ações. Por exemplo, a função Contribuinte Máquina Virtual tem uma ação de "listKeys" que permite que as teclas de conta de armazenamento para ser lidos. O contribuinte tem "Não ações" como atualizar o acesso de utilizadores no Active Directory.

-   Funções de armazenamento incluir (mas não estão limitadas a) o seguinte:

    -   Proprietário – que possam gerir tudo, incluindo o acesso.

    -   Contribuinte – ele pode fazer nada o proprietário pode exceto atribuir acesso. Alguém com esta função pode ver e gerar as teclas de conta de armazenamento. Com as teclas de conta de armazenamento, podem aceder a objetos de dados.

    -   Leitor – poderem ver informações sobre a conta de armazenamento, exceto segredos. Por exemplo, se atribuir uma função com permissões de leitor da conta de armazenamento a alguém, poderem ver as propriedades da conta de armazenamento, mas não podem Efetue alterações às propriedades ou visualizar as teclas de conta de armazenamento.

    -   Armazenamento conta contribuinte – pode gerir a conta de armazenamento – poderem lidos grupos de recursos e recursos, a subscrição e criar e gerir implementações de grupo de recursos de subscrição. Também pode aceder às teclas de conta de armazenamento, que por sua vez, significa que estes possam aceder ao plano de dados.

    -   Administrador de acesso do utilizador – que possam gerir acesso do utilizador para a conta de armazenamento. Por exemplo, eles podem conceder acesso de leitor a um utilizador específico.

    -   Máquina virtual contribuinte – que possam gerir máquinas virtuais mas não a conta de armazenamento aos quais estão ligados. Esta função pode listam as teclas de conta de armazenamento, o que significa que o utilizador a quem atribuir esta função pode atualizar o plano de dados.

        Para um utilizador criar uma máquina virtual, têm de ser possível criar o ficheiro VHD correspondente numa conta de armazenamento. Para o fazer, precisam de ser possível obter a chave de conta de armazenamento e passam-lo à API criar a VM. Por conseguinte, têm de ter esta permissão, de modo que podem listam as teclas de conta de armazenamento.

- A capacidade de definir funções personalizadas é uma funcionalidade que permite-lhe redigir um conjunto de ações a partir de uma lista de ações disponíveis que podem ser executadas no Azure recursos.

- O utilizador tem de ser configuradas no Azure Active Directory antes de poder atribuir uma função aos mesmos.

- Pode criar um relatório de quem concedido/revogado que tipo de acesso para a quem e, no qual o âmbito utilizando o PowerShell ou o clip do Azure.

####<a name="resources"></a>Recursos

-   [Controlo de acesso baseado em funções de Azure Active Directory](../active-directory/role-based-access-control-configure.md)

    Este artigo explica o controlo de acesso baseado em Azure Active Directory função e como funciona.

-   [RBAC: Criadas em funções](../active-directory/role-based-access-built-in-roles.md)

    Este artigo fornece detalhes sobre todas as funções incorporadas disponíveis no RBAC.

-   [Noções sobre a implementação do Gestor de recursos e implementação clássica](../resource-manager-deployment-model.md)

    Este artigo explica a implementação do Gestor de recursos e modelos de implementação clássica e explica as vantagens de utilizar os grupos de Gestor de recursos e de recursos

-   [Cluster Azure, rede e os fornecedores de armazenamento em Gestor de recursos Azure](../virtual-machines/virtual-machines-windows-compare-deployment-models.md)

    Este artigo explica como calcular Azure, rede e fornecedores de armazenamento de trabalhar em modelo de Gestor de recursos.

-   [Gerir o controlo de acesso baseado em funções com a API REST](../active-directory/role-based-access-control-manage-access-rest.md)

    Este artigo mostra como utilizar a API REST para gerir RBAC.

-   [Referência da API do resto do armazenamento Azure recurso fornecedor](https://msdn.microsoft.com/library/azure/mt163683.aspx)

    Esta é a referência para as APIs pode utilizar para gerir a sua conta de armazenamento através de programação.

-   [Guia do programador auth com a API do Gestor de recursos do Azure](http://www.dushyantgill.com/blog/2015/05/23/developers-guide-to-auth-with-azure-resource-manager-api/)

    Este artigo mostra como autenticar utilizando APIs do Gestor de recursos.

-   [Controlo de acesso baseado em funções para Microsoft Azure a partir do Ignite](https://channel9.msdn.com/events/Ignite/2015/BRK2707)

    Esta é uma hiperligação a um vídeo do canal 9 da conferência Ignite 2015 MS. Nesta sessão, falar sobre aceder a gestão e as capacidades de relatórios no Azure e explorar práticas recomendadas para proteger o acesso às subscrições do Azure utilizando o Azure Active Directory.

###<a name="managing-your-storage-account-keys"></a>Gerir as suas chaves de conta de armazenamento

Teclas de conta de armazenamento são cadeias de 512 bits criadas por Azure que, juntamente com o nome da conta de armazenamento, pode ser utilizada para aceder os objetos de dados armazenados na conta de armazenamento, por exemplo, blobs, entidades dentro de uma tabela, mensagens em fila e ficheiros numa partilha de ficheiros do Azure. Controlar o acesso ao armazenamento conta teclas controlos para o plano de dados para essa conta de armazenamento.

Cada conta de armazenamento tem duas chaves referidas como "Chave 1" e "2" no [portal do Azure](http://portal.azure.com/) e chave nos cmdlets do PowerShell. Estes podem voltar a ser gerados manualmente utilizando um dos vários métodos, incluindo, mas não limitado a através de do [Azure portal](https://portal.azure.com/), PowerShell, o clip Azure ou através de programação a utilizar a biblioteca de cliente do armazenamento .NET ou Azure armazenamento serviços REST API.

Existem várias razões para gerar chaves de conta de armazenamento.

-   Poderá gerá-los regularmente por motivos de segurança.

-   Seria gerar chaves de conta de armazenamento se alguém geridos para ataque para uma aplicação e obter a chave que foi codificado ou guardada num ficheiro de configuração, conceder-lhes acesso completo à sua conta de armazenamento.

-   Caso outra para nova chave geração é se a equipa está a utilizar uma aplicação do Explorador de armazenamento que mantém a chave de conta de armazenamento de um dos membros da equipa deixa. A aplicação seria continuar a trabalhar, conceder-lhes acesso à sua conta de armazenamento depois de que está ausente. Esta é realmente a razão primária criaram nível conta partilhado acesso assinaturas – pode utilizar uma nível de conta SA em vez de armazenar as teclas de acesso num ficheiro de configuração.

####<a name="key-regeneration-plan"></a>Plano de chave nova geração

Não pretende gerar apenas a chave que está a utilizar sem algum planeamento. Se fazê-lo, poderia cortada todo o acesso a essa conta de armazenamento, que pode fazer com que o interrupção principal. Este é o motivo pelo qual existem duas chaves. Deve gerar uma chave de cada vez.

Antes de gerar suas chaves, certifique-se de que tem uma lista de todas as aplicações que são dependentes a conta de armazenamento, bem como quaisquer outros serviços que estiver a utilizar no Azure. Por exemplo, se estiver a utilizar serviços de multimédia do Azure que são dependentes na sua conta de armazenamento, tem novamente sincronizar as teclas de acesso com o seu serviço de multimédia depois de voltar a gerar a chave. Se estiver a utilizar qualquer aplicações como o Explorador de armazenamento, terá de fornecer as teclas de novas para também nessas aplicações. Tenha em atenção que se tiver VMs ficheiros cujo VHD são armazenados na conta de armazenamento, eles, não serão afectados gerando as teclas de conta de armazenamento.

Pode voltar a gerar suas chaves no portal do Azure. Assim que as chaves serão recriadas eles podem demorar até 10 minutos a ser sincronizada em serviços de armazenamento.

Quando estiver pronto, eis o processo de geral com detalhes sobre como deverá alterar a sua chave. Neste caso, partem do princípio de é que estiver a utilizar chave 1 e passar para alterar tudo para como alternativa, utilize a chave de 2.

1.  Voltar a gerar chave 2 para se certificar de que é seguro. Pode fazê-lo no portal do Azure.

2.  Em todas as aplicações a localização onde armazenou a chave de armazenamento, altere a chave de armazenamento para utilizar novo valor da chave 2. Teste e publicar a aplicação.

3.  Depois de todos os de aplicações e serviços estão para cima e executar com êxito, a gerar chave 1. Isto assegura que qualquer pessoa a quem não expressamente têm a nova chave já não terá acesso à conta de armazenamento.

Se estiver a utilizar a chave de 2, pode utilizar o mesmo processo, mas inverter os nomes de chaves.

Pode migrar sobre algumas de dias, alterar cada aplicação para utilizar a nova chave e publicá-lo. Depois de todos eles são feitos, deve, em seguida, regresse e gerar a chave antiga para que esta já não funciona.

Outra opção é colocar a chave de conta de armazenamento num [Azure chave cofre](https://azure.microsoft.com/services/key-vault/) como uma palavra-passe e ter as suas aplicações de obter a chave a partir daí. Em seguida, quando gerar a tecla e atualizar cofre chave Azure, as aplicações não terão de ser novamente implementada porque irá atende a nova chave do Cofre de palavras chave Azure automaticamente. Tenha em atenção que pode ter o pedido ler a chave sempre que precisar dele ou pode-lo em cache na memória e se falhar quando utilizá-lo, obter a chave novamente a partir do Azure chave cofre.

Também utilizar o Azure chave cofre adiciona outro nível de segurança para as suas chaves de armazenamento. Se utilizar este método, nunca terá não chave armazenamento num ficheiro de configuração, que remove nessa linha de alguém obter acesso às chaves sem permissão específicos.

Outra vantagem de utilizar Azure chave Cofre é que também pode controlar o acesso a suas chaves utilizando o Azure Active Directory. Isto significa que pode conceder acesso a inúmeras das aplicações de que necessita para obter as teclas do Cofre de chave do Azure e saber a não ser possível aceder às teclas sem lhes conceder permissão especificamente outras aplicações.

Nota: é recomendado para utilizar apenas uma das teclas em todas as suas aplicações ao mesmo tempo. Se utilizar chave 1 em algumas locais e 2 de tecla no outras pessoas, não conseguir rodar suas chaves sem algumas aplicações perder o acesso.

####<a name="resources"></a>Recursos

-   [Sobre contas de armazenamento Azure](storage-create-storage-account.md#regenerate-storage-access-keys)

    Este artigo fornece uma descrição geral das contas de armazenamento e aborda visualizar, copiar e voltar a gerar teclas de acesso de armazenamento.

-   [Referência da API do resto do armazenamento Azure recurso fornecedor](https://msdn.microsoft.com/library/mt163683.aspx)

    Este artigo contém ligações para artigos específicas sobre a obter as teclas de conta de armazenamento e voltar a gerar as teclas de conta de armazenamento para uma conta do Azure utilizar a API REST. Nota: Este é para contas de armazenamento do Gestor de recursos.

-   [Operações de contas de armazenamento](https://msdn.microsoft.com/library/ee460790.aspx)

    Este artigo na referência Gestor do serviço de armazenamento REST API contém ligações para artigos específicos no retrieving e voltar a gerar as teclas de conta de armazenamento utilizar a API REST. Nota: Este é para as contas de armazenamento clássica.

-   [Diga final para gestão de chave – gerir o acesso aos dados de armazenamento do Windows Azure através do Azure AD](http://www.dushyantgill.com/blog/2015/04/26/say-goodbye-to-key-management-manage-access-to-azure-storage-data-using-azure-ad/)

    Este artigo mostra como utilizar o Active Directory para controlar o acesso às suas chaves de armazenamento do Windows Azure no Azure chave cofre. Também mostra como utilizar uma tarefa de automatização do Azure para gerar chaves numa base de hora a hora.

##<a name="data-plane-security"></a>Plano de segurança dos dados

Plano de segurança dos dados que se refere a métodos utilizados para proteger os objetos de dados armazenados no armazenamento Azure – o blobs, filas, tabelas e ficheiros. Vamos visualizou métodos para encriptar de dados e de segurança durante o trânsito dos dados, mas como que tomaria permissão do acesso aos objectos?

Basicamente, existem dois métodos para controlar o acesso aos próprios objectos de dados. O primeiro é ao controlo de acesso às teclas de conta de armazenamento e a segunda está a utilizar assinaturas de acesso partilhado para conceder acesso a objetos de dados específicos durante um período de tempo específico.

Uma exceção ao nota é que pode permitir o público acesso ao seu blobs através da definição do nível de acesso para o contentor que detém os blobs em conformidade. Se definir access para um contentor para Blob ou um contentor, permitirá público acesso de leitura para blobs desse contentor. Isto significa que qualquer pessoa com um URL que aponta para um blob no contentor pode abri-lo num browser sem utilizar uma assinatura de acesso partilhado ou está a ter as teclas de conta de armazenamento.

###<a name="storage-account-keys"></a>Teclas de conta de armazenamento

Teclas de conta de armazenamento são cadeias de 512 bits criadas por Azure que, juntamente com o nome da conta de armazenamento, pode ser utilizado para aceder aos objectos dados armazenados na conta de armazenamento.

Por exemplo, pode ler blobs, escrever para filas, criar tabelas e modificar os ficheiros. Muitos destas ações podem ser executados através do portal do Azure, ou utilizar uma das várias aplicações do Explorador de armazenamento. Também pode escrever código para utilizar a API REST ou uma das bibliotecas do cliente de armazenamento para efetuar estas operações.

Tal como é abordado na secção sobre a [Segurança de plano de gestão](#management-plane-security), é possível conceder acesso para as teclas de armazenamento para uma conta de armazenamento clássica atribuindo acesso total para a subscrição Azure. Acesso às teclas de armazenamento para uma conta de armazenamento utilizando o modelo de Gestor de recursos do Azure pode ser controlado através do controlo de acesso baseado em funções RBCA ().

###<a name="how-to-delegate-access-to-objects-in-your-account-using-shared-access-signatures-and-stored-access-policies"></a>Como acesso a objetos na sua conta utilizando partilhado assinaturas de acesso e armazenados políticas de acesso delegado

Uma assinatura de acesso partilhado é uma cadeia que contém um token de segurança que pode ser anexado um URI que permite-lhe acesso de delegado para objetos de armazenamento e especificar restrições, tais como as permissões e o intervalo de data/hora de acesso.

Pode conceder acesso a blobs, contentores, mensagens em fila, ficheiros e tabelas. Com tabelas, na realidade pode conceder permissão para aceder a um intervalo de entidades na tabela ao especificar os intervalos de chaves partição e linha ao qual pretende que o utilizador tenha acesso. Por exemplo, se tiver dados armazenados com uma chave de partição de estado geográfica, pode conceder a alguém acesso ao apenas os dados para Califórnia.

Outro exemplo, poderá dar um token de SA que lhe permite escrever entradas para uma fila de uma aplicação web e atribuir um trabalhador a aplicação de função um token de SA obtenha mensagens de fila de espera e processá-las. Ou pode conceder a um cliente um token de SA que podem utilizar para carregar imagens para um contentor do armazenamento de BLOBs e dar uma permissão de aplicação web para ler essas imagens. Em ambos os casos, existe uma separação de preocupações – cada aplicação pode ser fornecida apenas o acesso de que necessitam para realizar a sua tarefa. Isto é possível através da utilização de assinaturas de acesso partilhado.

####<a name="why-you-want-to-use-shared-access-signatures"></a>Por que motivo que pretende utilizar assinaturas de acesso partilhado

Porque é que motivos pode ter para utilizar uma SA em vez de apenas dar saída a sua chave de conta de armazenamento, que é muito mais fácil? Dar saída a sua chave da conta de armazenamento é como as teclas do seu Reino de armazenamento de partilha. Concede a acesso completo. Alguém poderia utilize as teclas e carregar a sua biblioteca de música inteira à sua conta de armazenamento. Estes podem substituir os seus ficheiros com versões vírus ou roubar os seus dados. Para dar acesso ilimitado à sua conta de armazenamento é algo que não devem ser tidos ligeiramente.

Com assinaturas de acesso partilhados, pode dar um cliente apenas as permissões necessárias para um período de tempo limitado. Por exemplo, se alguém está a carregar um blob à sua conta, pode concedê-los acesso de escrita para tempo apenas suficiente carregar o blob (dependendo o tamanho de BLOBs, claro). E se mudar de ideias, pode revogar o que o access.

Para além disso, pode especificar que os pedidos de efetuada utilizando uma SA são restringidos a determinadas endereço IP ou intervalo de endereços IP externo ao Azure. Também pode pedir que são efetuadas as solicitações através de um determinado protocolo (HTTPS ou HTTP/HTTPS). Isto significa que se pretender apenas permitir o tráfego HTTPS, pode configurar o protocolo necessário para HTTPS apenas e o tráfego HTTP será bloqueado.

####<a name="definition-of-a-shared-access-signature"></a>Definição de uma assinatura de acesso partilhado

Uma assinatura de acesso partilhado é um conjunto de parâmetros de consulta anexada ao URL apontar para o recurso

que fornece informações sobre o acesso permitido e o comprimento do tempo para o qual o acesso é permitido. Eis um exemplo; Este URI fornece acesso de leitura para um blob para cinco minutos. Nota SA parâmetros de consulta tem de ser carateres codificação de URL, como % 3A para dois pontos (:) ou % 20 para um espaço.

    http://mystorage.blob.core.windows.net/mycontainer/myblob.txt (URL to the blob)
    ?sv=2015-04-05 (storage service version)
    &st=2015-12-10T22%3A18%3A26Z (start time, in UTC time and URL encoded)
    &se=2015-12-10T22%3A23%3A26Z (end time, in UTC time and URL encoded)
    &sr=b (resource is a blob)
    &sp=r (read access)
    &sip=168.1.5.60-168.1.5.70 (requests can only come from this range of IP addresses)
    &spr=https (only allow HTTPS requests)
    &sig=Z%2FRHIX5Xcg0Mq2rqI3OlWTjEg2tYkboXr1P9ZUXDtkk%3D (signature used for the authentication of the SAS)

####<a name="how-the-shared-access-signature-is-authenticated-by-the-azure-storage-service"></a>Como a assinatura de acesso partilhado é autenticada pelo serviço de armazenamento do Azure

Quando o serviço de armazenamento recebe o pedido, leva os parâmetros de consulta de entrada e cria uma assinatura utilizando o método mesmo como o programa de chamada. Em seguida, se compara duas assinaturas. Se concordam, o serviço de armazenamento pode verificar a versão do serviço de armazenamento para certificar-se de que é válida, verifique se a data e hora atuais estão dentro da janela especificada, certifique-se de aceder a pedida corresponde ao pedido, etc.

Por exemplo, com o nosso URL acima, se o URL foi a apontar para um ficheiro em vez de um blob, este pedido seria falhar porque a mesma Especifica que a assinatura de acesso partilhado é para um blob. Se o comando REST a ser denominado tiver sido atualizar um blob, teria falhar porque a assinatura de acesso partilhado Especifica que é permitido acesso só de leitura.

####<a name="types-of-shared-access-signatures"></a>Tipos de assinaturas acesso partilhado

-   Uma nível de serviço SA podem ser utilizada para aceder a recursos específicos numa conta de armazenamento. Alguns exemplos de este estiver a obter uma lista de blobs num contentor, transferir um blob, atualizar uma entidade numa tabela, adicionar mensagens a uma fila ou carregar um ficheiro para uma partilha de ficheiros.

-   SA uma nível de conta podem ser utilizada para aceder ao tudo o que podem ser utilizada SA uma nível de serviço para. Para além disso, pode dar-opções para recursos de que não são permitidos com SA uma nível de serviço, tal como a capacidade de criar contentores, tabelas, filas e partilhas de ficheiros. Também pode especificar acesso a vários serviços ao mesmo tempo. Por exemplo, pode conceder a alguém acesso aos blobs e ficheiros na sua conta de armazenamento.

####<a name="creating-an-sas-uri"></a>Criar um URI SA

1.  Pode criar um URI ad hoc a pedido, que define todos os parâmetros de consulta de cada vez.

    Esta é realmente flexível mas, se tiver um conjunto lógico de parâmetros são semelhantes cada vez, utilizar uma política de acesso armazenados é uma ideia geral.

2.  Pode criar uma política de acesso armazenados para um contentor inteira, partilha de ficheiros, tabela ou fila de espera. Em seguida, pode utilizar este como a base para o URIs SA que cria. Podem ser facilmente revogadas permissões com base em armazenados políticas de acesso. Pode ter até 5 políticas de definidas cada contentor, fila de espera, tabela ou partilha de ficheiros.

    Por exemplo, se foram vai ter várias pessoas ler blobs num contentor específico, podia criar uma política de acesso armazenados que diz "dar acesso de leitura" e outras definições que serão os mesmos cada vez. Em seguida, pode criar um URI SA utilizando as definições da política de acesso armazenados e especificando a data/hora de expiração. A vantagem desta é que não tem de especificar todos os parâmetros de consulta de cada vez.

####<a name="revocation"></a>Revogação de certificados

Suponha que seu SA está comprometida, ou se pretender alterá-lo devido a segurança empresarial ou de requisitos de conformidade de regulamentação. Como pode revogar o acesso a um recurso utilizar esse SA? Depende como criou o URI SA.

Se estiver a utilizar o ad hoc URIs, tem três opções. Pode emitir tokens de SA com as políticas de expiração curtas e aguardar simplesmente as associações de segurança a expirar. Pode mudar o nome ou eliminar o recurso (partindo do princípio que o token foi confinado a um único objeto). Pode alterar as teclas de conta de armazenamento. Esta opção última pode ter um impacto grande, dependendo de como muitos serviços estiver a utilizar essa conta de armazenamento e provavelmente não está algo que pretende fazer sem algum planeamento.

Se estiver a utilizar uma SA derivada de uma política de acesso armazenadas, pode remover o acesso ao revogar a política de acesso armazenados – basta pode alterá-la para que se já tiver expirado ou pode removê-lo completamente. Entra em vigor imediatamente e invalida cada SA criadas utilizando essa política de acesso armazenados. Atualizar ou remover a política de acesso armazenado poderão pessoas impacto aceder a esse contentor específico, partilha de ficheiros, tabela ou fila através do SA, mas se os clientes são escritos para que estes pedem novas associações de segurança quando antigo fica inválido, isto irá funcionar ajustar.

Uma vez que utilizar uma SA derivada de uma política de acesso armazenados fornece-lhe a capacidade de revogar esse SA imediatamente, é a melhor prática recomendada para utilizar sempre as políticas de acesso armazenados sempre que possível.

####<a name="resources"></a>Recursos

Para obter informações mais detalhadas sobre como utilizar assinaturas de acesso partilhado e armazenados políticas de acesso, completa com exemplos, consulte os artigos seguintes:

-   Estes são os artigos de referência.

    -   [Serviço SA](https://msdn.microsoft.com/library/dn140256.aspx)

        Este artigo fornece exemplos de utilização de uma nível de serviço SA com blobs, mensagens em fila, intervalos de tabela e ficheiros.

    -   [Construir um serviço SA](https://msdn.microsoft.com/library/dn140255.aspx)

    -   [Construir uma conta SA](https://msdn.microsoft.com/library/mt584140.aspx)

-   Estes são a tutoriais para utilizar a biblioteca do cliente .NET para criar assinaturas de acesso partilhado e armazenados políticas de acesso.

    -   [Utilizar assinaturas de acesso partilhado (SA)](storage-dotnet-shared-access-signature-part-1.md)

    -   [Partilhadas assinaturas do Access, parte 2: Criar e utilizar uma SA com o serviço de BLOBs](storage-dotnet-shared-access-signature-part-2.md)

        Este artigo inclui uma explicação do modelo SA, partilhados assinaturas de acesso, e recomendações para a melhor prática utilizam de SA. Também é explicado a revogação da permissão concedida.

-   Limitação de acesso através do endereço IP (ACL de IP)

    -   [O que é um ponto final (ACL) na lista de controlo de acesso?](../virtual-network/virtual-networks-acl.md)

    -   [Construir um serviço SA](https://msdn.microsoft.com/library/azure/dn140255.aspx)

        Este é o artigo de referência para as SA de nível de serviço inclui um exemplo de IP ACLing.

    -   [Construir uma conta SA](https://msdn.microsoft.com/library/azure/mt584140.aspx)

        Este é o artigo de referência para as SA de nível de conta inclui um exemplo de IP ACLing.

-   Autenticação

    -    [Autenticação para os serviços de armazenamento Azure](https://msdn.microsoft.com/library/azure/dd179428.aspx)

-   Partilhado assinaturas acesso introdução Tutorial

    -   [Introdução ao Tutorial de SA](https://github.com/Azure-Samples/storage-dotnet-sas-getting-started)

##<a name="encryption-in-transit"></a>Encriptação em trânsito

###<a name="transport-level-encryption--using-https"></a>Encriptação de nível de transporte – utilizando HTTPS

Outro passo que deve seguir para garantir a segurança dos seus dados de armazenamento do Windows Azure é encriptar os dados entre o cliente e o armazenamento do Windows Azure. A primeira recomendação é utilizar sempre o protocolo [HTTPS](https://en.wikipedia.org/wiki/HTTPS) , que garantem comunicação segura através da Internet pública.

Deve utilizar sempre HTTPS quando chamar os REST APIs ou aceder a objectos no armazenamento. Além disso, **Assinaturas de acesso partilhados**, que podem ser utilizados para acesso de delegado para objetos de armazenamento do Windows Azure, incluir uma opção para especificar que apenas o protocolo HTTPS pode ser utilizado quando utilizar assinaturas acesso de partilhados, garantindo que qualquer pessoa enviar links com tokens SA utilizará o protocolo inicial maiúscula.

####<a name="resources"></a>Recursos

-   [Ativar HTTPS para uma aplicação no Azure de aplicação de serviço](../app-service-web/web-sites-configure-ssl-certificate.md)

    Este artigo mostra-lhe como pode ativar HTTPS para uma aplicação Web do Azure.

###<a name="using-encryption-during-transit-with-azure-file-shares"></a>Utilizar a encriptação durante o trânsito com partilhas de ficheiros do Azure

Armazenamento de ficheiros Azure suporta HTTPS quando utilizar a API REST, mas são mais frequentemente utilizado como uma partilha de ficheiros SMB anexado a uma VM. SMB 2.1 não suporta a encriptação, para que as ligações apenas são permitidas dentro da mesma região no Azure. No entanto, SMB 3.0 suporta a encriptação e podem ser utilizados com o Windows Server 2012 R2, Windows 8, Windows 8.1 e Windows 10, permitindo a publicação em região aceder e até mesmo acesso no ambiente de trabalho.

Tenha em atenção que enquanto partilhas de ficheiros do Azure podem ser utilizadas com Unix, o cliente Linux SMB ainda não suporta a encriptação, para que apenas é permitido o acesso dentro de uma região Azure. Suporte de encriptação para Linux está ativada as informações gerais de programadores Linux responsáveis pela funcionalidade SMB. Quando adicionam encriptação, terá a mesma capacidade para aceder a uma partilha de ficheiros do Azure no Linux, tal como o faria para Windows.

####<a name="resources"></a>Recursos

-   [Como utilizar o armazenamento de ficheiros do Azure com Linux](storage-how-to-use-files-linux.md)

    Este artigo mostra como montagem uma partilha de ficheiros do Azure num sistema Linux e carregar/transferência de ficheiros.

-   [Introdução ao armazenamento de ficheiros do Azure no Windows](storage-dotnet-how-to-use-files.md)

    Este artigo fornece uma descrição geral de partilhas de ficheiros do Azure e como montagem e utilizá-los de utilizar o PowerShell e o .NET.

-   [Armazenamento de ficheiros Azure internos](https://azure.microsoft.com/blog/inside-azure-file-storage/)

    Este artigo anuncia a disponibilidade geral de armazenamento de ficheiros do Azure e fornece detalhes técnicos sobre a encriptação SMB 3.0.

###<a name="using-client-side-encryption-to-secure-data-that-you-send-to-storage"></a>Utilizar a encriptação do lado do cliente para proteger os dados que enviar armazenamento

Outra opção que o ajuda a garantir que os seus dados são seguros enquanto a ser transferidos entre uma aplicação de cliente e armazenamento é de encriptação do lado do cliente. Os dados são encriptados antes de a ser transferidas para o armazenamento do Windows Azure. Quando a obter os dados a partir do armazenamento do Windows Azure, os dados são desencriptar após for recebida do lado do cliente. Apesar dos dados são encriptados aceder ao longo o fio, recomendamos que utilize também HTTPS, porque não tem verificações de integridade de dados criadas no qual a ajuda mitigar afetar a integridade dos dados de erros de rede.

Encriptação do lado do cliente também é um método de encriptação os seus dados em repouso, tal como os dados são armazenados na sua forma encriptada. Iremos falar sobre isto mais detalhadamente na secção sobre a [encriptação em repouso](#encryption-at-rest).

##<a name="encryption-at-rest"></a>Encriptação em repouso

Existem três funcionalidades Azure que fornecem encriptação em repouso. Encriptação do disco Azure é utilizada para encriptar o sistema operativo e dados de discos no IaaS Virtual máquinas. As outras duas – encriptação do lado do cliente e SSE – são ambos utilizados para encriptar dados no armazenamento do Windows Azure. Vamos observar cada um dos seguintes procedimentos e, em seguida, efetue uma comparação e ver quando cada uma delas pode ser utilizada.

Apesar de poder utilizar encriptação do lado do cliente para encriptar os dados estão em trânsito (que também é armazenado na sua forma encriptada no armazenamento), poderá preferir simplesmente utilizar HTTPS durante a transferência e tem algumas forma para os dados sejam automaticamente encriptados quando está armazenado. Existem duas formas para fazer isto – encriptação de disco do Azure e SSE. Um é utilizado para encriptar diretamente os dados no sistema operativo e dados discos utilizados por VMs e o outro é utilizado para encriptar dados escritos ao armazenamento de Blobs do Azure.

###<a name="storage-service-encryption-sse"></a>Encriptação do serviço de armazenamento (SSE)

SSE permite-lhe pedir que o serviço de armazenamento encriptar os dados automaticamente ao escrevê-lo ao armazenamento do Azure. Quando a ler os dados de armazenamento do Windows Azure, será possível desencriptar pelo serviço de armazenamento antes de serem devolvidos. Isto permite-lhe proteger os seus dados sem ter de modificar o código ou adicionar código a todas as aplicações.

Esta é uma definição de que aplica-se para a conta de armazenamento de todo. Pode ativar e desativar esta funcionalidade ao alterar o valor da definição. Para executar esta tarefa, pode utilizar o portal do Azure, PowerShell, o clip do Azure, armazenamento recurso fornecedor REST API ou a biblioteca de cliente do armazenamento .NET. Por predefinição, o SSE está desativada.

Neste momento, as teclas utilizadas para encriptação são geridas pelo Microsoft. Vamos gerar originalmente as teclas e gerir o armazenamento seguro das teclas, bem como a rotação normal conforme definido pelo interna política da Microsoft. No futuro, irá obter a capacidade de gerir o seus próprio chaves de encriptação e forneça um caminho de migração de teclas geridas pelo Microsoft para teclas gerido de cliente.

Esta funcionalidade está disponível para contas padrão e Premium armazenamento criadas utilizando o modelo de implementação do Gestor de recursos. SSE só se aplica a bloquear blobs, blobs de página e acrescentar blobs. Os outros tipos de dados, incluindo tabelas, filas e ficheiros, não serão encriptados.

Dados só são encriptados quando SSE está ativado e os dados destina-se ao armazenamento Blob. Ativar ou desativar SSE não causam impacto na dados existentes. Por outras palavras, quando ativar esta encriptação, este será não voltar atrás e encriptar dados que já existe nem irá-desencriptar os dados que já existem ao desativar a SSE.

Se pretender utilizar esta funcionalidade com uma conta de armazenamento clássico, pode criar uma nova conta de armazenamento do Gestor de recursos e utilizar AzCopy para copiar os dados para a nova conta. 

###<a name="client-side-encryption"></a>Encriptação do lado do cliente

Mencionado encriptação do lado do cliente quando debater a encriptação dos dados estão em trânsito. Esta funcionalidade permite-lhe encriptar através de programação os seus dados numa aplicação cliente antes de enviar-no fio sejam escritos nos armazenamento do Windows Azure e para através de programação desencriptar os seus dados depois de obtê-lo a partir do armazenamento do Windows Azure.

Isto fornecer encriptação em trânsito, mas também fornece a funcionalidade de encriptação em repouso. Tenha em atenção que apesar dos dados são encriptados quando estão em trânsito, ainda assim, recomendamos a utilização de HTTPS para tirar partido das verificações de integridade dos dados incorporados ajudar a atenuar afetar a integridade dos dados de erros de rede.

Um exemplo de onde pode utilizar este é se tiver uma aplicação web que armazena blobs e obtém blobs e pretende que a aplicação e os dados para ser tão seguro como possíveis. Nesse caso, utilizaria encriptação do lado do cliente. O tráfego entre o cliente e o serviço de Blobs do Azure contém o recurso encriptado e ninguém pode interpretar os dados estão em trânsito e reconstitui-lo para o seu blobs privados.

Encriptação do lado do cliente está incorporada no Java e as bibliotecas do cliente armazenamento .NET, que por sua vez utilizam Azure chave cofre APIs, tornando muito fácil que pode implementar. O processo de encriptação e desencriptar os dados utiliza a técnica de envelope e armazena metadados utilizado pela encriptação em cada objeto de armazenamento. Por exemplo, para blobs, armazena-lo num metadados blob, enquanto para filas, adiciona-lo para cada mensagem de fila de espera.

Para obter a encriptação propriamente dito, pode gerar e gerir o seus próprio chaves de encriptação. Também pode utilizar teclas geradas pela biblioteca de cliente do Azure armazenamento ou pode fazer com que o Cofre de chave do Azure gerar as teclas de. Pode armazenar das chaves de encriptação no seu armazenamento chave no local ou pode armazená-los num cofre de chave do Azure. Azure chave cofre permite-lhe conceder acesso ao segredos no Azure chave cofre a utilizadores específicos utilizando o Azure Active Directory. Isto significa que não apenas a qualquer pessoa pode ler o Cofre de chave do Azure e obter as teclas que está a utilizar para encriptação do lado do cliente.

####<a name="resources"></a>Recursos

-   [Encriptar e desencriptar blobs no armazenamento do Windows Azure utilizando Cofre de chave do Azure](storage-encrypt-decrypt-blobs-key-vault.md)

    Este artigo mostra como utilizar a encriptação do lado do cliente com Cofre de chave do Azure, incluindo como criar o KEK e armazená-lo no cofre através do PowerShell.

-   [Encriptação do lado do cliente e Azure cofre chave para o armazenamento do Microsoft Azure](storage-client-side-encryption.md)

    Este artigo fornece uma explicação de encriptação do lado do cliente e fornece exemplos de como utilizar a biblioteca do cliente de armazenamento para encriptar e desencriptar recursos dos serviços de armazenamento quatro. Também fala sobre Azure chave cofre.

###<a name="using-azure-disk-encryption-to-encrypt-disks-used-by-your-virtual-machines"></a>Utilizar a encriptação de disco do Azure para encriptar discos utilizados pelo seu máquinas virtuais

Encriptação do disco Azure é uma nova funcionalidade que esteja atualmente na pré-visualização. Esta funcionalidade permite-lhe encriptar a SO discos e dados utilizados por uma máquina de Virtual IaaS. Para o Windows, as unidades são encriptadas com a tecnologia de encriptação do BitLocker norma da indústria. Para Linux, os discos são encriptados com a tecnologia de encriptação DM. Isto está integrado com o Azure chave cofre para permitir a controlar e gerir as chaves de encriptação do disco.

A solução de encriptação de disco do Azure suporta os seguinte cenários de encriptação de três cliente:

-   Active a encriptação no novo IaaS VMs criada a partir de ficheiros VHD encriptados de cliente e chaves de encriptação fornecidos pelo cliente, o que são armazenadas no Azure chave cofre.

-   Active a encriptação no novo IaaS VMs criados a partir do Azure Marketplace.

-   Ative a encriptação existente IaaS VMs já em execução no Azure.

>[AZURE.NOTE] Para VMs Linux já em execução no Azure ou novo Linux VMs criada a partir de imagens no Azure Marketplace, encriptação do disco SO não é suportada. A encriptação do Volume SO para Linux VMs só é suportada para VMs que foram encriptado no local e carregado para Azure. Esta restrição só se aplica a disco SO; encriptação de volumes de dados para um VM Linux é suportada.

A solução suporta o seguinte procedimento para IaaS VMs versão de pré-visualização público quando ativado no Microsoft Azure:

-   Integração com o Azure cofre chave

-   Padrão [A, D e G série IaaS VMs](https://azure.microsoft.com/pricing/details/virtual-machines/)

-   Activar a encriptação no VMs IaaS criada utilizando o modelo de [Gestor de recursos do Azure](../azure-resource-manager/resource-group-overview.md)

-   Público Azure todas as [regiões](https://azure.microsoft.com/regions/)

Esta funcionalidade garante que todos os dados nos seus discos máquina virtual está encriptado em repouso no armazenamento do Windows Azure.

####<a name="resources"></a>Recursos

-   [Encriptação de Azure do disco para Windows e máquinas virtuais de Linux IaaS](https://gallery.technet.microsoft.com/Azure-Disk-Encryption-for-a0018eb0)

    Este artigo aborda a versão de pré-visualização do Azure disco encriptação e fornece uma ligação para transferir o papel em branco.

###<a name="comparison-of-azure-disk-encryption-sse-and-client-side-encryption"></a>Comparação de encriptação Azure do disco, SSE e encriptação do lado do cliente

####<a name="iaas-vms-and-their-vhd-files"></a>IaaS VMs e os respetivos ficheiros VHD

Para discos utilizados pelo IaaS VMs, recomendamos que utilize encriptação do Azure do disco. Pode ativar SSE para encriptar os ficheiros VHD que são utilizados para trás esses discos no Azure armazenamento, mas apenas encripta os dados recentemente escritos. Isto significa que se criar uma VM e, em seguida, ativar SSE na conta de armazenamento que detém o ficheiro VHD, apenas as alterações serão encriptadas, não VHD ficheiro original.

Se criar uma VM com uma imagem a partir do Azure Marketplace, Azure efetua uma [cópia pouco fundo](https://en.wikipedia.org/wiki/Object_copying) da imagem à sua conta de armazenamento de armazenamento do Windows Azure e não está encriptado, mesmo se tiver SSE ativado. Depois de cria a VM e inicia a atualizar a imagem, SSE começará a encriptação dos dados. Por este motivo, é melhor utilizar a encriptação de disco do Azure na VMs criadas a partir de imagens no Azure Marketplace caso pretenda totalmente encriptado.

Se trazer uma VM previamente encriptada para o Azure a partir no local, será capaz de carregar as chaves de encriptação para Cofre de chave do Azure e continuar a utilizar a encriptação para esse VM que estava a utilizar no local. Azure disco encriptação está ativada para processar este cenário.

Se tiver VHD não encriptado a partir no local, pode carregá-lo para o gallery como uma imagem personalizada e aprovisionar uma VM a partir dos mesmos. Se efetuar o seguinte utilizando os modelos de Gestor de recursos, pode pedi-la para ativar o Azure disco encriptação quando a VM-arranca.

Quando adicionar um disco de dados e montagem-na VM, pode ativar o Azure disco encriptação nesse disco de dados. Irá encriptar localmente nesse disco de dados pela primeira vez e, em seguida, a camada de gestão de serviço irá fazer uma escrita lenta contra armazenamento para que o conteúdo do armazenamento está encriptado.

####<a name="client-side-encryption"></a>Encriptação do lado do cliente####

Encriptação do lado do cliente é o método mais seguro encriptar os seus dados, porque encripta-lo antes de trânsito e encripta os dados no resto. No entanto, requerem que adicionar código às suas aplicações do armazenamento, o que é que não pretende fazer a utilizar. Nestes casos, pode utilizar HTTPs para os seus dados em trânsito, SSE para encriptar os dados no resto.

Com a encriptação do lado do cliente, pode encriptar entidades de tabela, mensagens em fila e blobs. Com SSE, apenas pode encriptar blobs. Se precisar de dados de tabela e fila de espera para ser encriptados, deve utilizar encriptação do lado do cliente.

Encriptação do lado do cliente é gerida totalmente pela aplicação. Esta é a abordagem mais segura, mas necessitam que fazer alterações de programação para a sua aplicação e colocar processos de gestão de chave no local. Utilizaria isto quando pretende que a segurança extra durante o trânsito, e pretende que os dados armazenados para ser encriptados.

Encriptação do lado do cliente é mais carga no cliente e tem de ter isso em consideração nos seus planos escalabilidade, especialmente se estiver a encriptação e transferir os muitos dados.

####<a name="storage-service-encryption-sse"></a>Encriptação do serviço de armazenamento (SSE)

SSE é gerido pelo armazenamento do Windows Azure. Utilizar SSE não fornece para a segurança dos dados estão em trânsito, mas -encriptar os dados tal como destina-se ao armazenamento do Azure. Não existe nenhuma impacto no desempenho ao utilizar esta funcionalidade.

Apenas pode encriptar blobs bloco, acrescentar blobs e blobs utilizando SSE da página. Se precisar de encriptar dados de tabela ou os dados de fila de espera, deverá tomar em consideração de utilizar a encriptação do lado do cliente.

Se tiver um arquivo ou de biblioteca de ficheiros VHD que utilizar como base para a criação de novas máquinas virtuais, pode criar uma nova conta de armazenamento, ativar SSE e, em seguida, carregue os ficheiros VHD para essa conta. Os ficheiros VHD serão encriptados ao armazenamento do Windows Azure.

Se tiver Azure disco encriptação ativadas para os discos numa VM e SSE activadas na conta do armazenamento mantém os ficheiros VHD, irá funcionar ajustar; resultará em quaisquer dados recentemente escrito a ser encriptados duas vezes.

##<a name="storage-analytics"></a>Análise de armazenamento

###<a name="using-storage-analytics-to-monitor-authorization-type"></a>Utilizar a análise de armazenamento para monitorizar o tipo de autorização

Para cada conta de armazenamento, pode ativar a análise de armazenamento do Azure executar o registo e armazenar dados métricas. Este é uma ótima ferramenta para utilizar quando pretende verificar as métricas de desempenho de uma conta de armazenamento ou necessitar de resolver uma conta de armazenamento de uma vez que está a ter problemas de desempenho.

Outro conjunto de dados, que pode ver nos registos de análise do armazenamento é o método de autenticação utilizado por outra pessoa ao aceder aos armazenamento. Por exemplo, com o armazenamento de BLOBs, pode ver se utilizaram uma assinatura de acesso partilhadas ou as teclas de conta de armazenamento, ou se o blob acedido foi público.

Isto pode ser muito útil se intimamente são a proteger o acesso ao armazenamento. Por exemplo, no armazenamento Blob do pode definir todos os contentores para privado e implementar a utilização de um serviço de SA em toda a suas aplicações. Em seguida, pode verificar os registos regularmente para ver se o seu blobs são acedidos utilizando as teclas de conta de armazenamento, que podem indicar uma violação de segurança, ou se a blobs são públicos mas não devem ser.

####<a name="what-do-the-logs-look-like"></a>Os registos são como?

Depois de ativar as métricas de conta de armazenamento e registo através do portal do Azure, dados de analytics serão iniciado acumular rapidamente. A funcionalidade de registo e métricas para cada serviço está separado; início de sessão escrito apenas quando existe atividade nessa conta de armazenamento, enquanto as métricas serão registadas minuto, cada hora ou diariamente, dependendo de como o configurar.

Os registos são armazenados na blobs bloco num contentor denominada $logs na conta de armazenamento. Neste contentor é criado automaticamente quando a análise de armazenamento está ativada. Depois de criada neste contentor, não é possível eliminá-lo, embora possa eliminar os seus conteúdos.

Em contentor $logs, existe uma pasta para cada serviço e, em seguida, existem subpastas para a ano/mês/dia/hora. Em hora, os registos são simplesmente numerados. Este é o que a estrutura do directório terá o seguinte aspeto:

![Vista de ficheiros de registo](./media/storage-security-guide/image1.png)

Todos os pedidos de armazenamento do Windows Azure tem sessão iniciado. Eis um instantâneo de um ficheiro de registo, que mostra os primeiros alguns campos.

![Instantâneo de um ficheiro de registo](./media/storage-security-guide/image2.png)

Pode ver que pode utilizar os registos para controlar qualquer tipo de chamadas para uma conta de armazenamento.

####<a name="what-are-all-of-those-fields-for"></a>O que são todos os campos para?

Existe um artigo listado nos recursos abaixo que fornece a lista de campos muitos os registos de início e o que são utilizadas para. Eis a lista de campos na ordem:

![Instantâneo de campos num ficheiro de registo](./media/storage-security-guide/image3.png)

Estamos interessados as entradas da GetBlob e como são autenticados, para que precisamos de procure as entradas com o tipo de operação "Get-Blob" e verificar o estado de pedido (4<sup>ésima</sup> coluna) e o tipo de autorização (8<sup>ésima</sup> coluna).

Por exemplo, as primeiras linhas na listagem acima, o estado do pedido é "Êxito" e o tipo de autorização é "autenticado". Isto significa que o pedido foi validado utilizando a chave de conta de armazenamento.

####<a name="how-are-my-blobs-being-authenticated"></a>Como são o meu blobs a ser autenticados?

Temos três casos podemos estiver interessados em.

1.  O blob é público e aceder às mesmas através de um URL sem uma assinatura de acesso partilhado. Neste caso, o estado do pedido é "AnonymousSuccess" e o tipo de autorização é "anónimo".

    1.0; 2015-11-17T02:01:29.0488963Z; GetBlob; **AnonymousSuccess**200 124; 37; **anónima**; mystorage...

2.  O blob for privado e foi utilizado com uma assinatura de acesso partilhado. Neste caso, o estado do pedido é "SASSuccess" e o tipo de autorização é "SA".

    1.0; 2015-11-16T18:30:05.6556115Z; GetBlob; **SASSuccess**200 416; 64; **SA**; mystorage...

3.  O blob for privado e a chave de armazenamento foi utilizada para aceder à mesma. Neste caso, o estado do pedido é "**êxito**" e o tipo de autorização é "**autenticados**".

    1.0; 2015-11-16T18:32:24.3174537Z; GetBlob; **Sucesso**206 59; 22; **autenticados**; mystorage...

Pode utilizar a análise de mensagem da Microsoft para ver e analisar estes registos. Inclui as funcionalidades de pesquisa e filtragem. Por exemplo, poderá pretender procurar instâncias de GetBlob para ver se a utilização de é o que esperar, ou seja, para se certificar de que alguém está não a aceder a sua conta de armazenamento tenha.

####<a name="resources"></a>Recursos

-   [Análise de armazenamento](storage-analytics.md)

    Este artigo é uma descrição geral de como ativá-las e de análise de armazenamento.

-   [Formato de registo de análise de armazenamento](https://msdn.microsoft.com/library/azure/hh343259.aspx)

    Este artigo ilustra do formato de registo de análise de armazenamento e detalhes de campos disponíveis nele, incluindo tipo de autenticação, que indica o tipo de autenticação utilizado para o pedido.

-   [Monitorizar a uma conta de armazenamento no portal do Azure](storage-monitor-storage-account.md)

    Este artigo mostra como configurar a monitorização de métricas e o registo de uma conta de armazenamento.

-   [Resolução de problemas de ponto a ponto com métricas de armazenamento do Azure e registo, AzCopy e analisador de mensagem](storage-e2e-troubleshooting.md)

    Este artigo fala sobre resolução de problemas utilizando a análise de armazenamento e mostra como utilizar o Microsoft mensagem Analyzer.

-   [Guia de operativo analisador de mensagens da Microsoft](https://technet.microsoft.com/library/jj649776.aspx)

    Este artigo é a referência para a Microsoft mensagem Analyzer e inclui ligações para um tutorial, o guia de introdução e a funcionalidade de resumo.

##<a name="cross-origin-resource-sharing-cors"></a>Recurso de origem cruz partilha (CORS)

###<a name="cross-domain-access-of-resources"></a>Acesso de domínios de recursos

Quando um browser web a ser executada num domínio faz com que um pedido de HTTP para um recurso a partir de um domínio diferente, esta opção é denominada um pedido de HTTP cruzada origem. Por exemplo, uma página HTML servida de contoso.com torna um pedido para um jpeg alojado no fabrikam.blob.core.windows.net. Por motivos de segurança, browsers restringem os pedidos de HTTP cruzada origem iniciados a partir de scripts, tal como JavaScript. Isto significa que quando algum código JavaScript numa página web em contoso.com pede esse jpeg no fabrikam.blob.core.windows.net, browser não permitir o pedido.

O que a este tem de fazer com o armazenamento do Windows Azure? Bem, se estiver a armazenar elementos estáticos como ficheiros de dados JSON ou XML no armazenamento Blob do utilizando uma conta de armazenamento denominado Fabrikam, o domínio para os ativos será fabrikam.blob.core.windows.net e a aplicação web do contoso.com não conseguir aceder aos mesmos utilizando JavaScript porque os domínios são diferentes. Também é verdadeiro se o que está a tentar ligar para um dos serviços de armazenamento Azure, como o armazenamento de tabela que devolvem dados JSON para serem processados pelo cliente JavaScript.

####<a name="possible-solutions"></a>Possíveis soluções

Uma forma para resolver este problema é atribuir um domínio personalizado como "storage.contoso.com" a fabrikam.blob.core.windows.net. É o problema que só pode atribuir esse domínio personalizado a conta de um armazenamento. O que acontece se os ativos são armazenados nas várias contas de armazenamento?

Outra forma para resolver este problema é tem a aplicação web funcionar como um proxy para as chamadas de armazenamento. Isto significa que se carregamento de um ficheiro ao armazenamento Blob, a aplicação web seria escrevê-la localmente e, em seguida, copie-o ao armazenamento Blob ou -seria ler-o para a memória e, em seguida, escreva-o ao armazenamento Blob. Em alternativa, poderia escrever uma aplicação web dedicado (tal como uma Web API) que carrega localmente os ficheiros e escreve-los ao armazenamento Blob do. Qualquer forma, tem de conta para essa função quando necessita de determinar a escalabilidade.

####<a name="how-can-cors-help"></a>Como pode ajudar CORS?

Armazenamento Azure permite-lhe ativar a CORS – cruzada a partilha de recursos de origem. Para cada conta de armazenamento, pode especificar domínios que podem aceder aos recursos nessa conta de armazenamento. Por exemplo, no nosso caso descrito acima, podemos pode ativar o CORS a conta de armazenamento fabrikam.blob.core.windows.net e configure-o para permitir o acesso a contoso.com. Em seguida, o contoso.com de aplicação web pode aceder diretamente aos recursos no fabrikam.blob.core.windows.net.

É uma coisa para tenha em atenção que CORS permite o acesso, mas não fornece autenticação, que é necessária para todos os que não público o access de recursos de armazenamento. Isto significa que só pode aceder a blobs se forem públicas ou incluir uma assinatura de acesso partilhado que lhe dá a permissão adequada. Tabelas, filas e ficheiros não têm público de acesso e exigem uma SA.

Por predefinição, CORS é desativado num todos os serviços. Pode ativar a CORS ao utilizar a API REST ou a biblioteca do cliente de armazenamento para ligar a um dos métodos para definir as políticas de serviço. Ao fazê-lo, incluir uma regra CORS, que se encontra no XML. Eis um exemplo de uma regra CORS que tenha sido definida utilizando a operação de definir propriedades do serviço para o serviço de BLOBs para uma conta de armazenamento. Pode executar essa operação com a biblioteca de cliente do armazenamento ou os REST APIs para armazenamento do Windows Azure.

    <Cors>    
        <CorsRule>
            <AllowedOrigins>http://www.contoso.com, http://www.fabrikam.com</AllowedOrigins>
            <AllowedMethods>PUT,GET</AllowedMethods>
            <AllowedHeaders>x-ms-meta-data*,x-ms-meta-target*,x-ms-meta-abc</AllowedHeaders>
            <ExposedHeaders>x-ms-meta-*</ExposedHeaders>
            <MaxAgeInSeconds>200</MaxAgeInSeconds>
        </CorsRule>
    <Cors>

Eis o que significa que cada linha:

-   **AllowedOrigins** Isto indica que domínios que não sejam correspondência podem pedir e receber dados a partir do serviço de armazenamento. Isto indica que contoso.com e fabrikam.com podem de pedir dados a partir do armazenamento de BLOBs para uma conta de armazenamento específico. Pode também defini-lo para um caráter universal (\*) para permitir que todos os domínios para pedidos de acesso.

-   **AllowedMethods** Esta é a lista dos métodos (verbos de pedido HTTP) que podem ser utilizadas quando efetuar o pedido. Neste exemplo, apenas colocar e obter são permitidos. Pode defini-lo para um caráter universal (\*) para permitir que todos os métodos ser utilizado.

-   **AllowedHeaders** Esta é os cabeçalhos de pedido que o domínio de origem pode especificar quando efetuar o pedido. Neste exemplo, são permitidos começando x-ms-metadado de todos os cabeçalhos de metadados, x-ms meta destino e x-ms meta abc. O caráter universal (\*) indica que é permitido qualquer início cabeçalho com o prefixo especificado.

-   **ExposedHeaders** Isto indica quais os cabeçalhos de resposta devem ser expostos pelo browser para o emissor pedido. Neste exemplo, qualquer cabeçalho de começar com "x-ms - meta-" será exposto.

-   **MaxAgeInSeconds** Este é a quantidade máxima de tempo que um browser será na cache o pedido de opções prévia. (Para obter mais informações sobre o pedido prévia, consulte o artigo primeiro abaixo.)

####<a name="resources"></a>Recursos

Para mais informações sobre CORS e como ativá-lo, consulte consulte o artigo estes recursos.

-   [Suporte de (CORS) para os serviços de armazenamento Azure no Azure.com de partilha de recursos de origem cruz](storage-cors-support.md)

    Este artigo fornece uma descrição geral do CORS e como definir regras para os serviços de armazenamento diferente.

-   [Recurso de origem da publicação em partilha (CORS) suporte para os serviços de armazenamento Azure no MSDN](https://msdn.microsoft.com/library/azure/dn535601.aspx)

    Esta é a documentação de referência para obter suporte para os serviços de armazenamento do Azure CORS. Tem ligações para artigos aplicar a cada serviço de armazenamento e apresenta um exemplo e explica cada elemento no ficheiro CORS.

-   [Armazenamento do Microsoft Azure: Introdução ao CORS](http://blogs.msdn.com/b/windowsazurestorage/archive/2014/02/03/windows-azure-storage-introducing-cors.aspx)

    Este é uma ligação para o artigo de blogue inicial anunciar o CORS e que mostra como utilizá-la.

##<a name="frequently-asked-questions-about-azure-storage-security"></a>Perguntas mais frequentes sobre a segurança de armazenamento do Windows Azure

1.  **Como posso verificar a integridade dos blobs que estou a transferir ou desaparecer armazenamento do Windows Azure se não é possível utilizar o protocolo HTTPS?**

    Se por qualquer motivo que tem de utilizar HTTP em vez de HTTPS e estão a trabalhar com blobs bloco, pode utilizar a verificação MD5 para ajudar a verificar a integridade dos blobs a serem transferidos. Isto irá ajudar com a proteção contra erros de camada de transporte/de rede, mas não necessariamente com ataques intermédios.

    Se pode utilizar HTTPS, que fornece a segurança de nível de transporte, em seguida, utilizar a verificação de MD5 é redundantes e desnecessários.
    
    Para mais informações, consulte consulte o artigo [Descrição geral de MD5 de Blobs do Azure](http://blogs.msdn.com/b/windowsazurestorage/archive/2011/02/18/windows-azure-blob-md5-overview.aspx).

2.  **E em relação a conformidade com FIPS para Governo dos E.U.A?**

    Dos Estados Unidos Federal Information Processing padrão (FIPS) define algoritmos aprovados para utilização por Federal dos e.u.a. sistemas informáticos, administração pública para a proteção de dados confidenciais. Ativar FIPS modo num ambiente de trabalho ou Windows server indica o SO que apenas validado FIPS algoritmos devem ser utilizados. Se uma aplicação utiliza não é compatível com algoritmos, irão interromper as aplicações. Versões de With.NET Framework 4.5.2 ou superior, a aplicação muda automaticamente os algoritmos de criptografia para utilizar algoritmos compatível com FIPS quando o computador está no modo FIPS.

    Microsoft irá deixá-la por excesso para cada cliente para decidir se ativar o modo de FIPS. Vamos considerar que não existe motivo apelativa para clientes que não estão sujeitas regulamentos de administração pública para ativar o modo de FIPS por predefinição.

    **Recursos**

-   [Por que motivo podemos estiver não recomendar "Modo FIPS" já](http://blogs.technet.com/b/secguide/archive/2014/04/07/why-we-re-not-recommending-fips-mode-anymore.aspx)

    Este artigo de blogue fornece uma descrição geral das FIPS e explica por que motivo não activar modo FIPS por predefinição.

-   [FIPS 140 validação](https://technet.microsoft.com/library/cc750357.aspx)

    Este artigo fornece informações sobre como produtos Microsoft e módulos criptografia está em conformidade com a norma FIPS para o Governo dos EUA Federal.

-   ["Criptografia do sistema: utilizar FIPS compatível com algoritmos para encriptação, hashing e assinatura" efeitos de definições de segurança no Windows XP e em versões posteriores do Windows](https://support.microsoft.com/kb/811833)

    Este artigo fala sobre a utilização do modo de FIPS no computadores mais antigos do Windows.
