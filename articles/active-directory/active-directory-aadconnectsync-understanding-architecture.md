<properties
   pageTitle="Sincronização do Azure AD Connect: Noções sobre a arquitetura | Microsoft Azure"
   description="Este tópico descreve a arquitetura do ligação do Azure AD sync e explica os termos utilizados."
   services="active-directory"
   documentationCenter=""
   authors="andkjell"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.workload="identity"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="08/31/2016"
   ms.author="billmath"/>

# <a name="azure-ad-connect-sync-understanding-the-architecture"></a>Sincronização do Azure AD Connect: Noções sobre a arquitetura
Este tópico abrange a arquitetura básica para a ligação do Azure AD sincronização. Em muitos aspectos, é semelhante ao seus predecessoras MIIS 2003, ILM 2007 e 2010 de FIM. Sincronização do Azure AD Connect é a evolução destas tecnologias. Se estiver familiarizado com qualquer uma destas tecnologias anteriores, o conteúdo deste tópico irá estar familiarizado para si, assim. Se estiver familiarizado com a sincronização, em seguida, este tópico é para si. No entanto não é um requisito para saber os detalhes neste tópico para ser efetuada com êxito no efetuar personalizações para a sincronização de ligação do Azure AD (denominado motor de sincronização neste tópico).

## <a name="architecture"></a>Arquitetura
O motor de sincronização cria uma vista de objetos que estão armazenados em várias origens de dados ligada integrada e gere informações de identidade nessas origens de dados. Esta vista integrada é determinada pelas informações de identidade obtidas a partir de origens de dados ligada e um conjunto de regras que determinam como estas informações do processo.

### <a name="connected-data-sources-and-connectors"></a>Origens de dados ligada e conectores
O motor de sincronização processa informações de identidade do repositórios de dados diferentes, tal como o Active Directory ou uma base de dados do SQL Server. Cada repositório de dados que organiza os seus dados num formato semelhante a base de dados e que fornece métodos de acesso a dados padrão é um potencial candidato de origem de dados para o motor de sincronização. Repositórios de dados que estão sincronizados pelo motor de sincronização chamam-se **ligado à rede de origens de dados** ou **ligado directórios** (CD).

O motor de sincronização contém interação com a origem de dados ligados num módulo chamado uma **conexão**. Cada tipo de origem de dados ligada tem um conector específico. A conexão converte uma operação necessária para o formato que compreende pode utilizar para a origem de dados ligados.

Conectores efetuar chamadas de API para informações sobre a identidade (ler e escrever) do exchange com a origem de dados ligados. Também é possível adicionar uma conexão personalizada utilizando a arquitetura de conectividade extensible. A ilustração seguinte mostra como uma conexão liga-se uma origem de dados ligada ao motor de sincronização.

![Arq1](./media/active-directory-aadconnectsync-understanding-architecture/arch1.png)

Podem fluxo de dados em qualquer direção, mas não é possível um fluxo de em ambos os sentidos em simultâneo. Por outras palavras, uma conexão pode ser configurada para permitir que dados fluxo da origem de dados ligada ao motor de sincronização ou a partir do motor de sincronização à origem de dados ligados, mas apenas uma dessas operações pode ocorrer em qualquer momento para um objeto e atributo. A direção pode ser diferente para objetos diferentes e para os atributos diferentes.

Para configurar uma conexão, especifique os tipos de objeto que pretende sincronizar. Especificar os tipos de objeto define o âmbito de objetos que estão incluídos no processo de sincronização. O passo seguinte é selecionar os atributos para sincronizar, que é conhecido como uma lista de inclusão de atributo. Estas definições podem ser alteradas qualquer altura em resposta a alterações para regras de negócio. Quando utiliza o Assistente de instalação de ligação do Azure AD, estas definições estão configuradas para si.

Para exportar objetos a uma origem de dados ligados, a lista de inclusão de atributo tem de incluir pelo menos os atributos mínimos necessários para criar um tipo de objecto específico numa origem de dados ligados. Por exemplo, o atributo **sAMAccountName** deve ser incluído na lista de inclusão de atributo para exportar um objecto de utilizador no Active Directory porque todos os objetos de utilizador no Active Directory têm de ter um atributo **sAMAccountName** definido. Novamente, o Assistente de instalação é que esta configuração para si.

Se a origem de dados ligada utiliza elementos estruturais, tal como a partições ou contentores para organizar os objetos, pode limitar as áreas na origem de dados ligada que são utilizadas para obter uma solução determinada.

### <a name="internal-structure-of-the-sync-engine-namespace"></a>Estrutura interna do espaço de nomes de motor de sincronização
O espaço de nomes do motor de sincronização inteira consiste em dois espaços de nomes que armazenam as informações de identidade. Os dois espaços são:

- O espaço de conexão (CS)
- O metaverse (MV)

O **espaço de conexão** é uma área de transição que contém as representações dos objetos designadas a partir de uma origem de dados ligada e os atributos especificados na lista de inclusão de atributo. O motor de sincronização utiliza o espaço de conexão para determinar o que mudou na origem de dados ligada e a fase alterações recebidas. O motor de sincronização também utiliza o espaço de conexão para fase alterações enviadas para exportar à origem de dados ligados. O motor de sincronização mantém um espaço de conexão distintos como uma área de teste para cada conector.

Ao utilizar uma área de teste, o motor de sincronização permanece independente das origens de dados ligada e não é afetado pela sua disponibilidade e acessibilidade. Como resultado, pode processar informações de identidade em qualquer altura ao utilizar os dados na área de teste. O motor de sincronização pode pedir apenas as alterações efetuadas no interior da origem de dados ligada desde o último sessão de comunicação terminadas ou push saída apenas as alterações às informações de identidade que a origem de dados ligada ainda não tenha recebido, que reduz o tráfego de rede entre o motor de sincronização e a origem de dados ligados.

Além disso, o motor de sincronização armazena informações de estado sobre todos os objetos que-fases no espaço de conexão. Quando são recebidos novos dados, o motor de sincronização avalia sempre se já tem sido sincronizados os dados.

O **metaverse** é uma área de armazenamento que contém as informações de identidade agregados de várias origens de dados ligados, fornecer uma vista individual global, integrada de todos os objetos combinados. Objetos de Metaverse são criados com baseados nas informações de identidade que são obtidas a partir de origens de dados ligada e um conjunto de regras que permitam que pode personalizar o processo de sincronização.

A ilustração seguinte mostra o espaço de nomes do espaço de conexão e o espaço de nomes de metaverse dentro do motor de sincronização.

![Arch2](./media/active-directory-aadconnectsync-understanding-architecture/arch2.png)

## <a name="sync-engine-identity-objects"></a>Objetos de identidade do motor de sincronização
Os objetos no motor de sincronização são representações de um dos objetos na origem de dados ligada ou na vista integrada que sincronizar motor tem desses objetos. Cada objeto do motor de sincronização tem de ter um identificador exclusivo global (GUID). GUID fornecem integridade dos dados e express relações entre objetos.

### <a name="connector-space-objects"></a>Objetos de espaço de conexão
Quando o motor de sincronização comunica com uma origem de dados ligados, lê as informações de identidade na origem de dados ligada e utiliza essas informações para criar uma representação do objeto identidade no espaço de conexão. Não pode criar ou eliminar estes objectos individualmente. No entanto, pode eliminar manualmente todos os objetos num espaço de conexão.

Todos os objetos no espaço conexão têm dois atributos:

- Um identificador exclusivo global (GUID)
- Um nome distinto (também conhecido como DN)

Se a origem de dados ligada atribui um atributo exclusivo para o objeto, em seguida, objetos no espaço connector também podem ter um atributo âncora. O atributo âncora identifica um objeto na origem de dados ligados. O motor de sincronização utiliza a âncora para localizar a representação deste objeto correspondente na origem de dados ligados. Motor de sincronização parte do princípio de que a âncora de um objeto nunca as alterações ao longo do tempo de vida do objeto.

Muitos dos conectores utilizam um identificador exclusivo conhecido para gerar uma âncora automaticamente para cada objeto quando são importado. Por exemplo, o conector do Active Directory utiliza o atributo **GUID de objecto** para uma âncora. Para origens de dados ligados não fornecem um identificador exclusivo claramente definido, pode especificar geração âncora como parte da configuração do conector.

Nesse caso, a âncora é criada a partir de uma ou mais atributos exclusivos de um objeto escrever, nem de alterações e que identifica exclusivamente identifica o objeto no espaço de conexão (por exemplo, um número de empregado ou um ID de utilizador).

Um objeto de espaço de conexão pode ser um dos seguintes procedimentos:

- Um objeto de transição
- Um marcador de posição

### <a name="staging-objects"></a>Objetos de transição
Um objeto de transição representa uma instância dos tipos de objecto designada da origem de dados ligados. Além do GUID e o nome exclusivo, um objeto de transição tenha sempre um valor que indica o tipo de objeto.

Os objetos de transição que foram importados sempre têm um valor para o atributo âncora. Objetos de transição que tenham sido recentemente já estão aprovisionados pelo motor de sincronização e estão no processo de criação da origem de dados ligada não tem um valor para o atributo âncora.

Objetos de transição também conter valores atuais de atributos de negócio e informações operacionais necessário pelo motor de sincronização para executar o processo de sincronização. Obter informações operacionais incluem sinalizadores que indicam o tipo de atualizações que são colocados no objeto transição. Se um objeto de transição tiver recebido novas informações de identidade da origem de dados ligada que ainda não foi processada, o objeto é assinalado como **Importar pendente**. Se a um objeto de transição tiver informações da identidade nova que não tem sido exportadas à origem de dados ligados, é sinalizada como **Exportar pendente**.

Um objeto de transição pode ser um objeto de importação ou de um objeto de exportação. O motor de sincronização cria um objeto de importação utilizando as informações de objeto de receber da origem de dados ligados. Quando o motor de sincronização recebe informações sobre a existência de um novo objeto que corresponde a um dos tipos de objeto selecionados no Connector, que cria um objeto de importar no espaço conexão como uma representação do objeto na origem de dados ligados.

A ilustração seguinte mostra um objeto de importação que representa um objeto na origem de dados ligados.

![Arch3](./media/active-directory-aadconnectsync-understanding-architecture/arch3.png)

O motor de sincronização cria um objeto de exportação utilizando as informações de objeto na metaverse. Objetos de exportação são exportados à origem de dados ligada durante a sessão de comunicação seguinte. Da perspetiva do motor de sincronização, objetos de exportação não existam na origem de dados ligada ainda. Por conseguinte, o atributo âncora para um objeto de exportação não está disponível. Depois de receber o objeto a partir do motor de sincronização, a origem de dados ligada cria um valor exclusivo para o atributo âncora do objeto.

A ilustração seguinte mostra como um objeto de exportação é criado utilizando informação de identidade da metaverse.

![Arch4](./media/active-directory-aadconnectsync-understanding-architecture/arch4.png)

O motor de sincronização confirma a exportação do objeto por voltar a importar o objeto da origem de dados ligados. Objetos de exportação ficam importar objectos quando o motor de sincronização recebe-las durante a importação seguinte dessa origem de dados ligados.

### <a name="placeholders"></a>Marcadores de posição
O motor de sincronização utiliza um espaço de nomes simples para armazenar os objetos. No entanto, algumas origens de dados ligada como do Active Directory utilizam um espaço de nomes hierárquico. Para informações a partir de um espaço de nomes hierárquica transformam-se um espaço de nomes simples, o motor de sincronização utiliza os marcadores de posição para manter a hierarquia.

Cada marcador de posição representa um componente (por exemplo, uma unidade organizacional) do nome de hierárquica de um objeto que não tenha sido importado motor de sincronização, mas é necessária para construir o nome hierárquico. Estão a preencher lacunas criadas por referências na origem de dados ligados a objetos que não são de transição objetos no espaço de conexão.

O motor de sincronização também utiliza os marcadores de posição para armazenar referenciados objetos que ainda não foram importados. Por exemplo, se a sincronização está configurada para incluir o atributo gestor para o objeto *Abbie Spencer* e o valor recebido é um objeto que não tenha sido importado ainda, tal como *CN = Lima Sperry, CN = utilizadores, Cc = fabrikam, CC = com*, as informações do Gestor são armazenadas como marcadores de posição no espaço de conexão. Se o objeto de gestor mais tarde é importado, o objeto de marcador de posição é substituído pelo objecto transição que representa o gestor.

### <a name="metaverse-objects"></a>Objetos de Metaverse
Um objeto de metaverse contém a vista agregada esse motor de sincronização tem os objetos de transição no espaço de conexão. Motor de sincronização cria metaverse objectos utilizando as informações no importar objectos. Vários objetos de espaço de conexão podem ser associados a um objeto de metaverse único, mas não pode ser ligado um objeto de espaço de conexão a mais do que um objeto de metaverse.

Objetos de Metaverse não podem manualmente criados ou eliminados. O motor de sincronização elimina automaticamente metaverse objetos que não possui uma ligação para qualquer objeto de espaço de conexão no espaço de conexão.

Para mapear objetos dentro de uma origem de dados ligados a um tipo de objeto correspondente dentro de metaverse, o motor de sincronização fornece um esquema extensible com um conjunto de tipos de objeto e atributos associados predefinido. Pode criar novos tipos de objeto e atributos de metaverse objetos. Atributos podem ser valor único ou de valores múltiplos, e os tipos de atributo podem ser cadeias, referências, números e valores lógicos.

### <a name="relationships-between-staging-objects-and-metaverse-objects"></a>Relações entre objectos transição e metaverse
Dentro do espaço de nomes do motor de sincronização, o fluxo de dados está ativado por relação a ligação entre objectos transição e metaverse. Um transição objeto que está associado a um objeto de metaverse chama um **associarem ao objeto** (ou um **objeto de conexão**). Um transição objeto que não está ligado a um objeto de metaverse chama um **desagregado objeto** (ou um **objeto de disconnector**). Os termos participar e desagregado são preferidos para não confunda com os conectores responsáveis por importar e exportar dados de um diretório ligado.

Marcadores de posição nunca estão ligadas a um objeto de metaverse

Um objeto associado inclui um objeto de transição e a sua relação ligada a um objeto de metaverse único. Objetos associados são utilizados para sincronizar o atributo os valores entre um objeto de espaço de conexão e um objeto de metaverse.

Quando um objeto de transição torna-se um objeto associado durante a sincronização, atributos podem fluir entre a transição e objecto o objeto metaverse. Fluxo de atributo é bidireccional e é configurado utilizando regras de atributo de importar e exportar regras de atributo.

Um objeto de espaço de conexão única pode ser ligado a apenas um objeto de metaverse. No entanto, cada objeto metaverse pode ser associado a vários objetos de espaço de conexão na mesma ou nos espaços de conexão diferente, conforme apresentado na seguinte ilustração.

![Arch5](./media/active-directory-aadconnectsync-understanding-architecture/arch5.png)

A relação entre a transição e objecto um objeto de metaverse ligada é persistente e pode ser removida apenas por regras que especificar.

Um desagregadas é um objeto de transição que não está associado a qualquer objeto de metaverse. O atributo valores de um objecto desagregado não são processadas qualquer ainda mais dentro de metaverse. Os valores do atributo do objeto correspondente na origem de dados ligados não são atualizados pelo motor de sincronização.

Ao utilizar objetos desagregados, pode armazenar informações de identidade do motor de sincronização e processá-la mais tarde. Manter um objeto de transição como um objeto desagregado no espaço conexão tem muitas vantagens. Uma vez que o sistema já tem pré-configuradas a informação necessária sobre este objecto, não é necessário criar uma representação deste objeto novamente durante a importação seguinte da origem de dados ligados. Desta forma, motor de sincronização sempre tem um instantâneo completo da origem de dados ligados, mesmo que não existe atual ligação à origem de dados ligados. Objetos desagregados podem ser convertidos em objetos associados e vice-versa, consoante as regras que especificar.

Um objeto de importar é criado como um objeto desagregado. Um objeto de exportação tem de ser um objeto associado. A lógica do sistema impõe esta regra e elimina todos os objectos de exportação que não seja um objeto associado.

## <a name="sync-engine-identity-management-process"></a>Processo de gestão de identidade de motor de sincronização
O processo de gestão de identidades controla como informações de identidade são atualizadas entre diferentes ligada origens de dados. Gestão de identidades ocorre nas três processos:

- Importar
- Sincronização de diretórios
- Exportar

Durante o processo de importação, o motor de sincronização avalia as informações de identidade recebidas de uma origem de dados ligados. Quando as alterações são detectadas,-cria novos objetos de transição ou atualiza os objectos transição existentes no espaço conexão para a sincronização.

Durante o processo de sincronização, o motor de sincronização atualiza metaverse para refletir as alterações que ocorreram no espaço conexão e atualiza o espaço de conexão para refletir as alterações que ocorreram na metaverse.

Durante o processo de exportação, o motor de sincronização emite terminar as alterações que são colocados em objetos de transição e os que são sinalizadas como pendentes exportar.

A seguinte ilustração mostra onde cada um dos processos ocorre como identidade fluxos de informações a partir de uma origem de dados ligados para outra.

![Arch6](./media/active-directory-aadconnectsync-understanding-architecture/arch6.png)

### <a name="import-process"></a>Processo de importação
Durante o processo de importação, o motor de sincronização avalia as atualizações às informações de identidade. Motor de Sincronização compara as informações de identidade recebidas da origem de dados ligada com as informações de identidade sobre um objeto de transição e determina se o objeto transição requer atualizações. Se for necessário atualizar o objeto transição por novos dados, o objeto transição é sinalizado como pendentes importar.

Por objetos no espaço conexão antes da sincronização de transição, motor de sincronização pode processar apenas as informações de identidade que foi alterado. Este processo fornece os seguintes benefícios:

- **Sincronização eficiente**. A quantidade de dados processadas durante a sincronização é minimizada.
- **Nova sincronização eficiente**. Pode alterar a forma como o motor de sincronização processa informações de identidade sem a reestabelecer ligação o motor de sincronização à origem de dados.
- **Oportunidade de pré-visualizar sincronização**. Pode pré-visualizar a sincronização de diretórios com Verifique se os pressupostos do informações sobre o processo de gestão de identidades estão corretos.

Para cada objeto especificado no Connector, o motor de sincronização pela primeira vez tenta localizar uma representação do objeto no espaço conexão de linhas com marcadores. Motor de sincronização examina todos os objetos de transição no espaço conexão e tenta localizar um objeto de transição correspondente que tiver um atributo âncora correspondente. Se nenhum objeto transição existente tiver um atributo âncora correspondente, o motor de sincronização tenta localizar um objeto de transição correspondente com o mesmo nome distinto.

Quando o motor de sincronização localiza um objeto de transição que corresponde pelo nome distinto, mas não por âncora, ocorre o seguinte comportamento especial:

- Se o objeto localizado no espaço conexão tiver sem âncora, o motor de sincronização remove este objeto do espaço de conexão e assinala o objeto metaverse que está ligada a como **Repetir aprovisionamento na próxima sincronização executar**. Em seguida, que cria o novo objecto de importação.
- Se o objeto localizado no espaço conexão tiver uma âncora, em seguida, motor de sincronização assume que este objeto foi mudado o nome ou eliminado no diretório ligado. Atribui um nome distinto temporário, as novo para o objeto de espaço de conexão para que-pode testar o objeto recebido. O objecto antigo torna-se, em seguida, **breves**, a aguardar a conexão para importar a mudar o nome ou eliminação para resolver a situação.

Se o motor de sincronização localizar um objeto de transição que corresponde ao objecto especificado no Connector, determina a forma que tipo de alterações sejam aplicadas. Por exemplo, o motor de sincronização poderá mudar o nome ou eliminar o objeto na origem de dados ligada ou -poderá atualizar apenas os valores dos atributos do objeto.

Objetos de transição com dados atualizados são marcados como pendentes importar. Diferentes tipos de pendentes importações estão disponíveis. Dependendo do resultado do processo de importação, um objeto de transição no espaço conexão tem uma das seguintes pendentes tipos de importação:

- **Nenhum**. Sem alterações para qualquer um dos atributos do objeto transição estão disponíveis. Motor de sincronização não sinalize deste tipo como pendentes importar.
- **Adicionar**. O objeto transição é um novo objeto de importar no espaço de conexão. Motor de sincronização assinala deste tipo como pendentes importar para processamento adicional na metaverse.
- **Atualização**. Motor de sincronização localiza um objeto de transição correspondente no espaço conexão e sinalizadores este tipo como pendentes importar para que podem ser processadas atualizações para os atributos de metaverse. As atualizações incluem mudar o nome do objeto.
- **Eliminar**. Motor de sincronização localiza um objeto de transição correspondente no espaço conexão e sinalizadores este tipo como pendentes importar para que o objeto associado pode ser eliminado.
- **Adicionar/eliminar**. Motor de sincronização localiza um objeto de transição correspondente no espaço de conexão, mas não correspondem os tipos de objeto. Neste caso, uma adicionar eliminar está testada modificação. A eliminar adicionar modificação indica ao motor de sincronização que uma nova sincronização completa deste objecto deve ocorrer devido ao facto diferentes conjuntos de regras aplicam para este objecto quando o objeto escreva alterações.

Ao definir o estado pendente importação de um objeto de transição, é possível significativamente reduzir a quantidade de dados processados durante a sincronização porque ao fazê-lo por isso, permite que o sistema processar apenas os objectos que tenham actualizados dados.

### <a name="synchronization-process"></a>Processo de sincronização
A sincronização é constituído por dois processos relacionados:

- Entrada sincronização, quando o conteúdo da metaverse é atualizado utilizando os dados no espaço de conexão.
- Sincronização saída, quando o conteúdo do espaço de conexão é atualizado utilizando dados de metaverse.

Ao utilizar as informações testadas no espaço de conexão, o processo de sincronização entrada cria na metaverse, a vista integrada dos dados armazenados nas origens de dados ligados. Todos os objetos de transição ou apenas aqueles com um pendente importar informações são agregados consoante a forma como as regras são configuradas.

As atualizações do processo de sincronização saída exportar objetos quando alterar metaverse objetos.

Sincronização de entrada cria a vista integrada no metaverse as informações de identidade é recebido a partir de origens de dados ligados. Motor de sincronização pode processar informações de identidade em qualquer altura, utilizando as informações de identidade mais recentes que este tenha da origem de dados ligados.

**Sincronização de entrada**

Sincronização de entrada inclui os seguintes processos:

- **Aprovisionar** (também designado por **projecções** se é importante distinguir este processo de aprovisionamento de sincronização saída). O motor de sincronização cria um novo objeto metaverse com base num objeto transição e ligações-los. Aprovisionar é uma operação de nível do objeto.
- **Participar**. O motor de sincronização ligações um objeto de transição para um objecto metaverse existente. Uma associação é uma operação de nível do objeto.
- **Fluxo de atributo importar**. Motor de sincronização atualiza os valores de atributo, denominados caudal de atributo, do objeto na metaverse. Importar Fluxo de atributo é uma operação de nível do atributo que requer uma ligação entre um objecto transição e um objeto de metaverse.

Aprovisionar é o único processo que cria objectos na metaverse. Aprovisionar afeta apenas os objetos de importação que estão desagregados objetos. Durante a disposição, o motor de sincronização cria um objeto de metaverse que corresponde ao tipo de objeto do objeto importar e estabelece uma ligação entre ambos os objetos, portanto, criando um objeto associado.

O processo de associação também estabelece uma ligação entre importar objectos e um objeto de metaverse. A diferença entre associação e disposição é que o processo de associação requer que o objeto de importar estão ligadas a um objeto de metaverse existente, onde o processo de aprovisionar cria um novo objeto metaverse.

Motor de sincronização tentará aderir a um objeto de importação para um objeto de metaverse ao utilizar critérios especificado na configuração de regra de sincronização.

Durante os processos de aprovisionar e participar, o motor de sincronização ligações um objeto desagregado para um objeto de metaverse, tornando-as a participar. Depois destas operações de nível do objeto estiverem concluídas, o motor de sincronização pode atualizar os valores do atributo do objeto associado metaverse. Este processo é denominado importar fluxo de atributo.

Importar Fluxo de atributo ocorre em todos os objetos de importação que executar novos dados e não estão ligados a um objeto de metaverse.

**Sincronização de saída**

As atualizações de sincronização saída exportar objectos quando um objeto de metaverse alterar mas não é eliminado. Sobre o objetivo da sincronização de saída é avaliar se necessitam de alterações a objetos metaverse actualizações a transição objetos nos espaços conexão. Em alguns casos, as alterações podem exigir que transição objetos em todos os espaços de conexão ser atualizados. Objetos de transição que são alterados são sinalizados como pendentes exportar, tornando-as exportar objetos. Estes objetos são mais tarde seguia à origem de dados ligada durante o processo de exportação de exportação.

Sincronização de saída tem três processos:

- **Aprovisionamento**
- **Deprovisioning**
- **Exportar Fluxo de atributo**

Aprovisionamento e deprovisioning estão ambas as operações de nível do objeto. Deprovisioning depende do aprovisionamento porque aprovisionamento só pode iniciá-lo. Deprovisioning é acionada quando Aprovisiona o remove a ligação entre um objeto de metaverse e um objeto de exportação.

Aprovisionamento sempre é acionado quando as alterações são aplicadas a objetos a metaverse. Quando forem efetuadas alterações a objetos metaverse, o motor de sincronização pode executar qualquer uma das seguintes tarefas como parte do processo de aprovisionamento:

- Crie objetos associados, onde um objeto de metaverse está ligado a um objeto de exportação recentemente criado.
- Mudar o nome de um objeto associado.
- Terminar ligações entre um objeto de metaverse e objetos, criação de um objecto desagregado de teste.

Se aprovisionamento requer o motor de sincronização para criar um novo objeto de conexão, o objeto de transição aos quais está ligado o objeto metaverse é sempre um objeto de exportação, uma vez que o objeto não existir na origem de dados ligados.

Se necessitar de aprovisionamento do motor de sincronização para terminar um objeto associado, criação de um objecto desagregado, deprovisioning é acionada. O processo de deprovisioning elimina o objeto.

Durante a deprovisioning, eliminar um objeto de exportação não física elimina o objeto. O objeto é assinalado como **eliminada**, o que significa que a operação de eliminação está testada no objeto.

Exportar atributo fluxo também ocorre durante o processo de sincronização de saída, da mesma forma que o fluxo de atributo importar ocorre durante a sincronização de entrada. Exportar atributo fluxo ocorre apenas entre metaverse e exportar objetos que são associados.

### <a name="export-process"></a>Processo de exportação
Durante o processo de exportação, o motor de sincronização examina todos os objetos de exportação que são sinalizados como pendentes exportar no espaço conexão e, em seguida, envia actualizações para a origem de dados ligados.

O motor de sincronização, pode determinar o sucesso de uma exportação mas não é possível suficientemente determinar que o processo de gestão de identidades está concluído. Objetos na origem de dados ligada podem sempre ser alterados por outros processos. Uma vez que o motor de sincronização não tiver uma ligação persistente à origem de dados ligados, não é suficiente para fazer suposições sobre as propriedades de um objeto na origem de dados ligada baseada apenas numa notificação de exportação com êxito.

Por exemplo, um processo na origem de dados ligada alterar atributos do objeto novamente para os respetivos valores originais (ou seja, a origem de dados ligada pode substituir os valores imediatamente depois dos dados são seguia pelo motor de sincronização e aplicados com êxito na origem de dados ligada).

Arquivos do motor de sincronização exportar e importar informações de estado sobre cada objeto transição. Se os valores dos atributos que estão especificados na lista de inclusão de atributo foram alterados desde a última exportação, o armazenamento de importar e exportar motor de sincronização do estado permite reagir corretamente. Motor de sincronização utiliza o processo de importação para confirmar os valores de atributo que tenham sido exportados à origem de dados ligados. Uma comparação entre as informações importadas e exportadas, conforme mostrado na ilustração seguinte, ativa o motor de sincronização determinar se a exportação foi efetuada com êxito ou se precisa de ser repetida.

![Arch7](./media/active-directory-aadconnectsync-understanding-architecture/arch7.png)

Por exemplo, se o motor de sincronização exporta o atributo C, o qual tem um valor de 5, a uma origem de dados ligados, armazena C = 5 na sua memória de estado de exportação. Cada exportação adicional neste resultados objeto uma tentativa para exportar C = 5 à origem de dados ligada novamente porque motor de sincronização assume que este valor não ser forma persistente aplicado ao objeto (ou seja, a menos que um valor diferente foi importado recentemente a partir da origem de dados ligados). A memória exportação está desmarcada quando for recebida C = 5 durante uma operação de importação no objeto.

## <a name="next-steps"></a>Próximos passos
Saiba mais sobre a configuração de [sincronizar a ligação do Azure AD](active-directory-aadconnectsync-whatis.md) .

Saiba mais sobre como [integrar a suas identidades no local com o Azure Active Directory](active-directory-aadconnect.md).
