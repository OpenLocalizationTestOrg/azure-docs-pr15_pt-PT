<properties
   pageTitle="Conector do Lotus Domino | Microsoft Azure"
   description="Este artigo descreve como configurar o conector de Domino Lotus da Microsoft."
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.workload="identity"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="08/30/2016"
   ms.author="billmath"/>

# <a name="lotus-domino-connector-technical-reference"></a>Referência técnica do Lotus Domino conexão
Este artigo descreve o Lotus Domino Connector. O artigo aplica-se aos seguintes produtos:

- Gestor de identidades do Microsoft 2016 (MIM2016)
- Gestor de identidades do Forefront 2010 R2 (FIM2010R2)
    -   Tem de utilizar correcção 4.1.3671.0 ou posterior [KB3092178](https://support.microsoft.com/kb/3092178).

Para MIM2016 e FIM2010R2, o conector está disponível como uma transferência a partir do [Centro de transferências da Microsoft](http://go.microsoft.com/fwlink/?LinkId=717495).

## <a name="overview-of-the-lotus-domino-connector"></a>Descrição geral do Lotus Domino conector
O Lotus Domino Connector permite-lhe integrar o serviço de sincronização com o servidor do IBM Lotus Domino.

A partir de uma perspetiva de alto nível, as seguintes funcionalidades são suportadas pelo versão atual do conector:

Funcionalidade | Suporte
--- | ---
Origem de dados ligada | Servidor: <li>Do Lotus Domino 8.5. x</li><li>Do Lotus Domino 9</li>Cliente:<li>Do Lotus Notes 9</li>
Cenários | <li>Gestão de ciclo de vida do objeto</li><li>Gestão de grupos</li><li>Gestão de palavra-passe</li>
Operações | <li>Completo e importar Delta</li><li>Exportar</li><li>Definir e alterar a palavra-passe em palavra-passe HTTP</li>
Esquema | <li>Pessoa (guardado no servidor do utilizador, contacto (pessoas com nenhum certificado))</li><li>Grupo</li><li>Recurso (sala, recurso Online da reunião)</li><li>Correio na base de dados</li><li>Deteção dinâmica dos atributos para objetos suportados</li>

O conector do Lotus Domino utiliza o cliente do Lotus Notes para comunicar com o servidor do Lotus Domino. Em consequência esta dependência, um cliente de notas do Lotus suportados tem de estar instalado no servidor de sincronização. A comunicação entre o cliente e o servidor é implementada através da interface do Lotus Notes .NET interoperabilidade (Interop.domino.dll). Esta interface facilita a comunicação entre o Microsoft.NET plataforma e o cliente do Lotus Notes e suporta o acesso a documentos do Lotus Domino e vistas. Para importar delta, também é possível que é utilizada a interface nativa C++ (consoante o método de importação de delta selecionado).

### <a name="prerequisites"></a>Pré-requisitos
Antes de utilizar a conexão, certifique-se de que tem o seguinte procedimento no servidor de sincronização:

- 4.5.2 do Microsoft .NET Framework ou posterior
- O cliente do Lotus Notes tem de estar instalado no seu servidor de sincronização
- O conector do Lotus Domino requer a predefinição do Lotus Domino LDAP esquema base de dados (schema.nsf) estar presentes no servidor Domino diretório. Se não estiver presente, pode instalá-lo ao executar o ou reiniciar o serviço LDAP no servidor Domino.

### <a name="connected-data-source-permissions"></a>Permissões de origem de dados ligadas
Para efetuar qualquer uma das tarefas suportadas no conector do Lotus Domino, tem de ser um membro dos grupos seguintes:

- Administradores de acesso completos
- Administradores
- Administradores de base de dados

A tabela seguinte apresenta as permissões que são necessárias para cada operação:

Operação | Direitos de acesso
--- | ---
Importar | <li>Ler documentos públicos</li><li> Total de administrador do Access (quando estiver membro do grupo de administradores de acesso total, têm automaticamente um acesso eficaz na ACL.)</li>
Exportar e definir palavra-passe | Acesso eficaz: <li>Criar documentos</li><li>Eliminar documentos</li><li>Ler documentos públicos</li><li>Escrever documentos públicos</li><li>Replicação ou copiar documentos</li>Operações de exportação, também terá das seguintes funções: <li>CreateResource</li><li>GroupCreator</li><li>GroupModifier</li><li>UserCreator</li><li>UserModifier</li>

### <a name="direct-operations-and-adminp"></a>Operações diretas e AdminP
Operações ou aceda diretamente ao diretório Domino ou durante o processo de AdminP. Objetos de lista de todos os suportadas, operações de tabelas a seguinte e, se aplicável, o método de implementação relacionadas:

**Livro de endereços principal**

Objecto | Criar | Actualização | Eliminar
--- | --- | --- | ---
Pessoa | AdminP | Direta | AdminP
Grupo | AdminP | Direta | AdminP
MailInDB | Direta | Direta | Direta
Recurso | AdminP | Direta | AdminP

**Livro de endereços secundário**

Objecto | Criar | Actualização | Eliminar
--- | --- | --- | ---
Pessoa | N/D | Direta | Direta
Grupo | Direta | Direta | Direta
MailInDB | Direta | Direta | Direta
Recurso | N/D | N/D | N/D

Quando é criado um recurso, é criado um documento de notas. Da mesma forma, quando é eliminado um recurso, o documento de notas é eliminado.

### <a name="ports-and-protocols"></a>Portas e protocolos
Cliente do IBM Lotus Notes e servidores de Domino comunicam através de notas remoto procedimento de chamadas (NRPC) onde NRPC deve utilizar TCP/IP. O número da porta predefinida é 1352, mas pode ser alterado pelo administrador do Domino.

### <a name="not-supported"></a>Não é suportado
As seguintes operações não são suportadas pelo versão atual do conector do Lotus Domino:

- Deslocar-se a caixa de correio entre servidores.

## <a name="create-a-new-connector"></a>Criar uma nova conexão

### <a name="client-software-installation-and-configuration"></a>Configuração e instalação de Software de cliente
Do Lotus Notes tem de estar instalado no servidor **antes do** que Connector está instalado.

Quando instala, certifique-se de que fazer um **Único utilizador instalar**. A predefinição **Vários utilizadores instalar** não funciona.  
![Notes1](./media/active-directory-aadconnectsync-connector-domino/notes1.png)

Na página funcionalidades, instale apenas as funcionalidades do Lotus Notes requeridas e **Início de sessão único do cliente**. Início de sessão único é necessário para a conexão conseguir iniciar sessão servidor Domino.  
![Notes2](./media/active-directory-aadconnectsync-connector-domino/notes2.png)

**Nota:** Inicie uma vez do Lotus Notes com um utilizador que se encontra no mesmo servidor como a conta que utiliza como conta de serviço a conexão. Certifique-se também fechar o cliente do Lotus Notes no servidor. Não pode estar em execução ao mesmo tempo que a conexão tenta ligar ao servidor Domino.

### <a name="create-connector"></a>Criar conector
Para criar um conector do Lotus Domino, num **Serviço de sincronização** , selecione **Agente de gestão** e **Criar**. Selecione a conexão **do Lotus Domino (Microsoft)** .  
![CreateConnector](./media/active-directory-aadconnectsync-connector-domino/createconnector.png)

Se a sua versão do serviço de sincronização oferece a capacidade de configurar a **arquitetura**, certifique-se da que conexão está definida para o valor predefinido para executar o **processo**.

### <a name="connectivity"></a>Conectividade
Na página Conetividade, tem de especificar o nome do servidor do Lotus Domino e introduza as credenciais de início de sessão.  
![Conectividade](./media/active-directory-aadconnectsync-connector-domino/connectivity.png)

A propriedade de servidor Domino suporta dois formatos para o nome do servidor:

- Nomedoservidor
- Nomedoservidor/Nome_do_directório

O formato de **Nomedoservidor/Nome_do_directório** é o formato preferido para este atributo, uma vez que fornece uma resposta mais rápida quando o conector de contacta o servidor Domino.

O ficheiro de ID de utilizador fornecido está armazenado na base de dados de configuração do serviço de sincronização.

Para **Importar Delta** têm estas opções:

- **Nenhum**. A conexão não suporta qualquer importações delta.
- **Adicionar/atualização**. A importação do conector faz delta adiciona e atualiza operações. Para eliminar, é necessária uma operação de **Importação completa** . Esta operação está a utilizar a interoperabilidade .net.
- **Adicionar/actualizar/eliminar**. A importação do conector faz delta adicionar, atualizar e eliminar operações. Esta operação está a utilizar as interfaces C++ nativas.

Nas **Opções de esquema** tem as seguintes opções:

- **Predefinição de esquema**. A conexão Deteta o esquema do servidor Domino. Esta é a opção de predefinido.
- **Esquema DSML**. Utilizado apenas se o servidor Domino não expõe o esquema. Em seguida, pode criar um ficheiro DSML com o esquema e importe-o em vez disso. Para mais informações sobre DSML, consulte o artigo [organização de normalização](https://www.oasis-open.org/committees/tc_home.php?wg_abbrev=dsml).

Quando clicar em seguinte, os parâmetros de configuração de ID de utilizador e palavra-passe são verificados.

### <a name="global-parameters"></a>Parâmetros globais
Na página parâmetros globais, configurar o fuso horário e importar e exportar a opção de operação.  
![Parâmetros globais](./media/active-directory-aadconnectsync-connector-domino/globalparameters.png)

O parâmetro de **Fuso horário do Domino servidor** define a localização do seu servidor Domino.

Esta opção de configuração é necessária para suportar operações de **importação de variação de** uma vez que permite a sincronização de serviço determinar alterações entre as dois últimos importações.

#### <a name="import-settings-method"></a>Definições de importação, método
**Executar a importação completo por** tem estas opções:

- Pesquisa
- Vista (recomendada)

**Procura** é utilizando a indexação na Domino mas é comuns que os índices não são atualizados em tempo real e os dados devolvidos do servidor sempre não estão corretos. Para um sistema com muitas alterações, esta opção normalmente não funciona bem e fornece falso elimina em algumas situações. No entanto, **pesquisa** é mais rápido que **vista**.

**A vista** é a opção recomendada, uma vez que fornece o estado correcto de dados. É ligeiramente mais lento do que procura.

#### <a name="creation-of-virtual-contact-objects"></a>Criação de objetos de contactos Virtual
O **Permitir criação de \_objecto contacto** tem estas opções:

- Nenhum
- Valores de que não sejam referência
- Valores de referência e não referência

No Domino, atributos de referência podem conter vários formatos diferentes para fazer referência a outros objetos. Possam representar diferentes variações, implementa o conector \_contactar objetos, também conhecido como **Contactos Virtual** (VC). Estes objetos são criados para que estes possam juntar para objectos MV existentes ou projectados como novos objetos. Desta forma, referências de atributo podem ser preservadas.

Ao ativar esta definição e, se o conteúdo de um atributo de referência não é um formato de DN, um \_objecto contacto é criado. Por exemplo, um atributo membro de um grupo pode conter endereços SMTP. Também é possível de ter o nome abreviado e outros atributos presentes no atributos de referência. Neste cenário, selecione **Valores não-referência**. Esta configuração é a definição mais comuns para implementações Domino.

Quando Lotus Domino está configurado para ter livros de endereços separada com nomes distintos diferentes que representa o mesmo objeto, é possível criar também \_contacte objetos para todos os valores de referência que sejam encontram num livro de endereços. Neste cenário, selecione a opção de **referência e valores de referência não** .

Se tiver múltiplos valores no atributo **FullName** no Domino, em seguida, também pretende ativar a criação de contactos Virtual para que podem ser resolvidas referências. Por exemplo, este atributo pode ter vários valores após uma casamento ou divórcio. Selecione a caixa de verificação **Activar... FullName tem vários valores** para este cenário.

Ao associar os atributos de correto, o \_objetos de contacto seriam ser associados ao objeto MV.

Estes objectos têm VC =\_contacto adicionado à sua DN.

#### <a name="import-settings-conflict-object"></a>Importar definições, estão em conflito objeto
**Excluir objeto de conflito**

Numa grande implementação Domino, é possível que vários objetos têm o mesmo DN devido a problemas de replicação. Nestes casos, o conector iria visualizar dois objetos com UniversalIDs diferente mas DN mesmo. Este conflito fará um objecto breves a ser criado no espaço de conexão. A conexão pode ignorar os objetos que tiverem sido seleccionados Domino como vítimas replicação. A recomendação é manter esta caixa de verificação selecionada.

#### <a name="export-settings"></a>Exportar definições de
Se a opção **Utilizar AdminP para atualizar referências** estiver desmarcada, exportação de atributos de referência, tais como membro, for uma chamada direta e não utiliza o processo de AdminP. Utilize esta opção apenas quando AdminP não tiver sido configurado para manter a integridade referencial.

#### <a name="routing-information"></a>Informações de encaminhamento
No Domino, é possível que um atributo referência tem informações de encaminhamento incorporadas como um sufixo para o DN. Por exemplo, o atributo membro num grupo poderia conter **CN=example/organization@ABC**. O sufixo @ABC é as informações de encaminhamento. As informações de encaminhamento são utilizadas por Domino para enviar e-mails para o sistema de Domino correto, o que pode ser um sistema de uma organização diferente. No campo de informações de encaminhamento, pode especificar encaminhar sufixos utilizados dentro da empresa no âmbito da conexão. Se um destes valores for encontrado como um sufixo no atributo referência, as informações de encaminhamento são removidas da referência. Se o encaminhamento sufixo relativa a um valor de referência não é possível que deverá ser correspondido para um desses valores especificados, um \_objecto contacto é criado. Estes \_objetos de contacto são criados com ** RO=@ ** inseridos o DN. Para estas \_contacto objectos também são adicionados os seguintes atributos para permitir que aderir a um objeto real se for necessário: \_routingName, \_contactName, \_displayName e UniversalID.

#### <a name="additional-address-books"></a>Livros de endereços adicionais
Se não tiver **Assistência diretório** instalado, que fornece o nome de livros de endereços secundário, em seguida, pode introduzir manualmente estes livros de endereços.

#### <a name="multivalued-transformation"></a>Transformação de valores múltiplo
Muitos atributos no Lotus Domino são múltiplos valores. Os atributos de metaverse correspondentes são normalmente único avaliada. Ao configurar a importação e a opção de operação de exportação, ativar o connector para o ajudar com a tradução necessária dos atributos afetados.

**Exportar**  
A opção de operação de exportação suporta dois modos:

- Anexar item
- Substituir o item

**Substituir Item** – quando selecionar esta opção, o conector sempre remover os atuais valores do atributo no Domino e substitua-os com os valores fornecidos. O fornecido avaliada pode ser um valor único ou múltiplos valores.

Exemplo: O atributo do Assistente de início de um objeto de pessoa tem os seguintes valores:

- NC = Greg Winston/OU=Contoso/O=Americas,NAB=names.nsf
- NC = Smith/OU=Contoso/O=Americas,NAB=names.nsf João

Se um Assistente novo com o nome **David Alexander** é atribuído a este objeto da pessoa, o resultado é:

- NC = David Alexander/OU=Contoso/O=Americas,NAB=names.nsf

**Anexar Item** – quando selecionar esta opção, o conector mantém os valores existentes no atributo no Domino e inserir novos valores na parte superior da lista de dados.

Exemplo: O atributo do Assistente de início de um objeto de pessoa tem os seguintes valores:

- NC = Greg Winston/OU=Contoso/O=Americas,NAB=names.nsf
- NC = Smith/OU=Contoso/O=Americas,NAB=names.nsf João

Se um Assistente novo com o nome **David Alexander** é atribuído a este objeto da pessoa, o resultado é:

- NC = David Alexander/OU=Contoso/O=Americas,NAB=names.nsf
- NC = Greg Winston/OU=Contoso/O=Americas,NAB=names.nsf
- NC = Smith/OU=Contoso/O=Americas,NAB=names.nsf João

**Importar**  
A opção de operação de importação suporta dois modos:

- Predefinido
- Valor único valor múltiplo

Por **predefinição** – ao selecionar a opção predefinida, todos os valores de todos os atributos são importados.

Atributo de valor único é convertido **Multivalued único valor** – quando selecionar esta opção, um atributo múltiplos valor. Se existir mais do que um valor, é utilizado o valor no canto superior (este valor é normalmente também a mais recente).

Exemplo: O atributo do Assistente de início de um objeto de pessoa tem os seguintes valores:

- NC = David Alexander/OU=Contoso/O=Americas,NAB=names.nsf
- NC = Greg Winston/OU=Contoso/O=Americas,NAB=names.nsf
- NC = Smith/OU=Contoso/O=Americas,NAB=names.nsf João

A atualização mais recente para este atributo está **David Alexander**. Uma vez que a opção de operação de importação estiver definida para Multivalued único valor, conexão importa apenas **David Alexander** para o espaço de conexão.

A lógica para converter os atributos de múltiplos valores em atributos de valor único não se aplica ao atributo de membro do grupo e para o atributo de fullname pessoa.

-Lo também é possível configurar importar e exportar regras de transformação de valores múltiplos atributos por atributo, como uma exceção à regra global. Para configurar esta opção, introduza [objecttype]. [NomeAtributo] nas caixas de texto **Importar a lista de exclusão de atributo** e **exportar a lista de exclusão de atributo** . Por exemplo, se introduzir Person.Assistant e o sinalizador global está definido para importar todos os valores, apenas o primeiro valor é importado para o assistente.

#### <a name="certifiers"></a>Certificadores
Todas as unidades de organização/organizacional são listadas pela conexão. Para poder exportar objectos de pessoa para o livro de endereços principal, é necessário um certifier com a palavra-passe.

Se todos os certificadores tem a mesma palavra-passe, a **palavra-passe para todos os Certifers** pode ser utilizada. Em seguida, pode introduzir a palavra-passe aqui e especifique apenas o ficheiro certifier.

Se apenas importar, em seguida, não terá de especificar quaisquer certificadores.

### <a name="configure-provisioning-hierarchy"></a>Configurar o aprovisionamento de hierarquia
Quando configura o conector do Lotus Domino, ignore esta página de diálogo. O conector do Lotus Domino não suporta a hierarquia de aprovisionamento.  
![Hierarquia de aprovisionamento](./media/active-directory-aadconnectsync-connector-domino/provisioninghierarchy.png)

### <a name="configure-partitions-and-hierarchies"></a>Configurar a partições e hierarquias
Quando configurar a partições e hierarquias, tem de selecionar o livro de endereços primária denominado NAB=names.nsf. Para além do livro de endereços principal, pode selecionar livros de endereços secundário que existam.  
![Partições](./media/active-directory-aadconnectsync-connector-domino/partitions.png)

### <a name="select-attributes"></a>Selecione atributos
Quando configurar o seu atributos, tem de selecionar todos os atributos que são o prefixo ** \_MMS\_**. Estes são os atributos necessários de aprovisionar o novos objetos para Lotus Domino

![Atributos](./media/active-directory-aadconnectsync-connector-domino/attributes.png)

## <a name="object-lifecycle-management"></a>Gestão de ciclo de vida do objeto
Esta secção fornece uma descrição geral dos diferentes objectos na Domino.

### <a name="person-objects"></a>Objetos de pessoa
O objeto de pessoa representa os utilizadores na organização e unidades de organização. Além dos atributos predefinido, o administrador Domino pode adicionar os atributos personalizados para um objeto de pessoa. No mínimo, um objeto de pessoa tem de incluir todos os atributos obrigatórios. Para uma lista completa dos atributos obrigatórios, consulte o artigo [Do Lotus Notes propriedades](#lotus-notes-properties). Para registar um objeto da pessoa, devem ser cumpridos os pré-requisitos seguintes:

- O livro de endereços (names.nsf) tem de ter sido definido e deverá ser o livro de endereços principal.
- Tem de ter o certifier your/or Id e a palavra-passe para registar um determinado utilizador na organização / unidade da organização.
- Tem de definir um conjunto específico de propriedades do Lotus Notes de um objeto de pessoa. Estas propriedades são utilizadas para aprovisionar o objeto de pessoa. Para obter mais detalhes, consulte a secção designada [Do Lotus Notes propriedades](#lotus-notes-properties) mais tarde deste documento.
- A HTTP palavra-passe inicial para uma pessoa é um atributo e conjunto durante o aprovisionamento.
- O objeto de pessoa tem de ser um dos seguintes três tipos suportados:
    1. Utilizador normal com um ficheiro de impressão e de um ficheiro de id de utilizador
    2. Roaming utilizador (um utilizador Normal que inclui todos os ficheiros guardados no servidor de base de dados)
    3. Contactos (com ficheiro sem id de utilizador)

As pessoas (exceto os contactos) ainda mais podem ser agrupadas em utilizadores-nos e internacional conforme definido pelo valor da \_MMS\_IDRegType propriedade. Estes pessoa: Utilize o cliente de notas para aceder aos servidores do Lotus Domino, ter um Id de notas e um documento da pessoa. Se estiverem a utilizar impressão de notas, em seguida, também possuem um ficheiro de correio. O utilizador tem de estar registado para se tornar ativa. Para obter mais informações, consulte:

- [Configurar utilizadores de notas](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_SETTING_UP_NOTES_USERS.html)
- [Registo de utilizador](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_REGISTERING_USERS.html)
- [Gerir utilizadores](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_MANAGING_USERS_5151.html)
- [Mudar o nome de utilizadores](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_RENAMING_A_USER_AUTOMATICALLY.html)

Todas estas operações são executadas no Lotus Domino e, em seguida, importadas para o serviço de sincronização.

### <a name="resources-and-rooms"></a>Recursos e salas
Um recurso é outro tipo de uma base de dados do Lotus Domino. Recursos podem ser salas de conferência com vários tipos de equipamento, tal como projectores. Existem subtipos de recursos suportados pelo conector do Lotus Domino que são definidos pelo atributo de tipo de recurso:

Tipo de recurso | Atributo de tipo de recurso
--- | ---
Sala | 1
Recurso (outro) | 2
Reunião online | 3

Para o tipo de objeto de recursos trabalhar, é necessário o seguinte procedimento:

- Base de dados de reserva de recursos deve já existam no servidor Domino ligado
- O site já está definido para o recurso

A base de dados de reserva de recursos contém três tipos de documentos:

- Perfil de site
- Recurso
- Reserva

Para obter mais detalhes sobre como configurar da base de dados de reserva de recursos, consulte o artigo [Configurar a base de dados reservas de recursos](https://www-01.ibm.com/support/knowledgecenter/SSKTMJ_8.0.1/com.ibm.help.domino.admin.doc/DOC/H_SETTING_UP_THE_RESOURCE_RESERVATIONS_DATABASE.html).

**Criar, atualizar e eliminar recursos**  
As operações criar, atualizar e eliminar são executadas pelo conector do Lotus Domino na base de dados reserva de recursos. Recursos são criados como documentos no Names.nsf (ou seja, o livro de endereços principal). Para obter mais detalhes sobre como editar e eliminar recursos, consulte o artigo [Editar e eliminar documentos de recursos](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_EDITING_AND_DELETING_RESOURCE_DOCUMENTS.html).

**Importar e exportar operação para recursos**  
Os recursos podem ser importados para e exportados a partir do serviço de sincronização, como qualquer outro tipo de objeto. Selecione o tipo de objecto como recursos durante a configuração. Bem sucedida operação de exportação, que deve ter detalhes do tipo de recurso, base de dados de conferência e nome do Site.

### <a name="mail-in-databases"></a>Correio nas bases de dados
Uma base de dados Mail-In é uma base de dados que foi concebido para receber e-mails. É uma caixa de correio do Lotus Domino que não está associada a qualquer conta de utilizador do Lotus Domino específica (ou seja, não ter as suas próprias ficheiro de ID e palavra-passe). Uma base de dados no correio tem um ID exclusivo ("nome curto") associado-lo e o seu próprio endereço de correio electrónico.

Se existir uma necessidade de uma caixa de correio separada com o seu próprio endereço de e-mail que pode ser partilhado entre os diferentes utilizadores (por exemplo, group@contoso.com), é criada uma base de dados mail-in. O acesso a esta caixa de correio é controlado através do seu controlo de acesso lista (ACL), que contém os nomes dos utilizadores de notas que são permitidos para abrir a caixa de correio.

Para obter uma lista dos atributos necessários, consulte a secção denominada [Atributos obrigatórios](#mandatory-attributes) neste artigo.

Quando uma base de dados foi concebido para receber uma mensagem de correio, um documento de impressão na base de dados é criado no Lotus Domino. Este documento tem de existir no diretório de Domino de todos os servidores que armazena uma cópia da base de dados. Para obter uma descrição detalhada sobre a criação de um documento de impressão na base de dados, consulte o artigo [criar um documento de impressão na base de dados](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_CREATING_A_MAILIN_DATABASE_DOCUMENT_FOR_A_NEW_DATABASE_OVERVIEW.html).

Antes de criar uma base de dados no correio, a base de dados deverá já existentes (deveria ter sido criada pelo administrador do Lotus) no servidor Domino.

### <a name="group-management"></a>Gestão de grupos
Pode obter uma descrição geral detalhada o Lotus Domino da gestão de grupo a partir dos seguintes recursos:

- [Utilizar grupos](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_USING_GROUPS_OVER.html)
- [Criar um grupo](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_CREATING_AND_MODIFYING_GROUPS_STEPS_MIDTOPIC_55038956829238418.html)
- [Criar e modificar os grupos](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_CREATING_AND_MODIFYING_GROUPS_STEPS.html)
- [Gerir grupos](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_MANAGING_GROUPS_1804.html)
- [Mudar o nome de um grupo](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_RENAMING_A_GROUP_STEPS.html)

### <a name="password-management"></a>Gestão de palavra-passe
Para um utilizador do Lotus Domino registado, existem dois tipos de palavras-passe:

1. Palavra-passe de utilizador (armazenado no ficheiro User.id)
2. Internet / palavra-passe HTTP

O conector do Lotus Domino suporta apenas operações com palavra-passe HTTP.

Para efetuar a gestão de palavra-passe, deverá ativar a gestão de palavras-passe para o conector no estruturador de agente de gestão. Para ativar a gestão de palavra-passe, selecione **Ativar a gestão de palavra-passe** na página de diálogo **Configurar extensões** .  
![Configurar as extensões de](./media/active-directory-aadconnectsync-connector-domino/configureextensions.png)

O suporte de conector do Lotus Domino seguir operações em palavra-passe Internet:

- Definir palavra-passe: Definir palavra-passe define uma nova palavra-passe do HTTP/Internet o utilizador na Domino. Por predefinição, a conta também é desbloqueada. O sinalizador de desbloquear é exposto na interface de WMI do motor de sincronização.
- Alterar palavra-passe: Neste cenário, um utilizador poderá querer alterar a palavra-passe ou é pedido para alterar a palavra-passe após um tempo especificado. Para esta operação tirar coloque, tanto (antigo e a palavra-passe nova) são obrigatórios. Uma vez alterado, a nova palavra-passe é atualizado no Lotus Domino.

Para obter mais informações, consulte:

- [Utilizar a funcionalidade de bloqueio de Internet](http://www.ibm.com/developerworks/lotus/library/domino8-lockout/)
- [Gerir palavras-passe de Internet](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=/com.ibm.help.domino.admin85.doc/H_NOTES_AND_INTERNET_PASSWORD_SYNCHRONIZATION_7570_OVER.html)

## <a name="reference-information"></a>Informações de referência
Esta secção apresenta como as descrições de atributo e requisitos de atributo para o conector do Lotus Domino.

### <a name="lotus-notes-properties"></a>Propriedades do Lotus Notes
Quando aprovisionar objetos de pessoa para o seu diretório do Lotus Domino, os objetos tem de ter um conjunto específico de propriedades com valores específicos povoadas. Estes valores apenas são necessários para criar operações.

A tabela seguinte lista estas propriedades e fornece uma descrição dos mesmos.

Propriedade | Descrição
--- | ---
\_MMS_AltFullName | O nome completo alternativo do utilizador.
\_MMS_AltFullNameLanguage | O idioma para ser utilizado para especificar o nome completo alternativo do utilizador.
\_MMS_CertDaysToExpire | O número de dias desde a data atual antes do certificado expira. Se não for especificado, a data de predefinido é de dois anos a partir da data atual.
\_MMS_Certifier | Propriedade que contém o nome de hierarquia organizacional da certifier. Por exemplo: Or = OrganizationUnit, the = Org, C = país.
\_MMS_IDPath | Se a propriedade estiver vazia, nenhum ficheiro de identificação de utilizador é criado localmente no servidor de sincronização. Se a propriedade contiver um nome de ficheiro, um ficheiro de ID de utilizador é criado na pasta madata. A propriedade também pode conter um caminho completo.
\_MMS_IDRegType | As pessoas podem ser classificadas como os utilizadores internacionais, os utilizadores-nos e contactos. A tabela seguinte lista os valores possíveis: <li>0 - contacto</li><li>1 - utilizador e.u.a.</li><li>2 - utilizador internacional</li>
\_MMS_IDStoreType | Propriedade obrigatório dos Estados Unidos e utilizadores internacionais. A propriedade contém um valor de número inteiro que especifica se a identificação do utilizador está armazenada como um anexo no livro de endereços de notas ou num ficheiro de correio da pessoa. Se o ficheiro de ID de utilizador é um anexo no livro de endereços,-, opcionalmente, pode ser criada como um ficheiro com \_MMS_IDPath. <li>Esvazie - ficheiro de ID de arquivo no ID cofre, nenhum ficheiro de identificação (utilizado para contactos).</li><li> 1 - anexo no livro de endereços de notas. O \_MMS_Password propriedade tem de estar definida para ficheiros de identificação do utilizador que são anexos</li><li>2 - ID de arquivo no ficheiro de correio da pessoa. O \_MMS_UseAdminP tem de estar definida como false para permitir que o ficheiro de correio criado durante o registo de pessoa. O \_MMS_Password propriedade tem de estar definida para ficheiros de identificação do utilizador.</li>
\_MMS_MailQuotaSizeLimit | O número de megabytes são permitidos para a base de dados do ficheiro de correio electrónico.
\_MMS_MailQuotaWarningThreshold | O número de megabytes são permitidos para a base de dados do ficheiro de correio electrónico antes de é emitido um aviso.
\_MMS_MailTemplateName | O ficheiro de modelo de correio electrónico é utilizado para criar o ficheiro de correio electrónico do utilizador. Se não for especificado um modelo, o ficheiro de correio é criado utilizando o modelo especificado. Se não for especificado nenhum modelo, o ficheiro de modelo predefinido é utilizado para criar o ficheiro.
\_MMS_OU | Propriedade opcional que é o nome OU no certifier. Esta propriedade deve estar vazia para os contactos.
\_MMS_Password | Propriedade necessária para os utilizadores. A propriedade contém a palavra-passe para o ficheiro de identificação do objeto.
\_MMS_UseAdminP | Propriedade deverá ser definida como VERDADEIRO se o ficheiro de correio deve ser criado pelo processo de AdminP no servidor Domino (assíncrono para o processo de exportação). Se a propriedade estiver definida para FALSO, o ficheiro de correio é criado com o utilizador Domino (síncrono no processo de exportação).

Para um utilizador com um ficheiro de identificação associado a \_MMS_Password propriedade tem de conter um valor. Acesso ao correio electrónico através do cliente do Lotus Notes, as propriedades de MailServer e MailFile de um utilizador tem de conter um valor.

Para aceder ao e-mail através de um browser, as seguintes propriedades tem de conter valores:

- MailFile - propriedade obrigatório que contém o caminho no servidor do Lotus Domino localização onde armazenou o ficheiro de correio.
- MailServer - propriedade obrigatório que contém o nome do servidor do Lotus Domino. Este valor é o nome a utilizar quando criou o ficheiro do Lotus de correio no servidor Domino.
- HTTPPassword - propriedade opcional que contém a palavra-passe do Web access para o objeto.

Para aceder ao servidor de Domino sem capacidade de correio, a propriedade HTTPPassword tem de conter um valor. A propriedade MailFile e a propriedade MailServer podem estar vazias.

Com \_MMS_ IDStoreType = 2 (id de arquivo no ficheiro de impressão), a propriedade MailSystem de NotesRegistrationclass está definida para REG_MAILSYSTEM_INOTES (3).

### <a name="mandatory-attributes"></a>Atributos obrigatórios
O conector do Lotus Domino principalmente suporta os seguintes tipos de objetos (tipos de documento):

- Grupo
- Correio numa base de dados
- Pessoa
- Contacto (pessoa com sem certifier)
- Recurso

Esta secção apresenta os atributos que são obrigatórios para cada objeto suportado exportar para um servidor Domino.

Tipo de objeto | Atributos obrigatórios
--- | ---
Grupo | <li>NomedaLista</li>
Principais na base de dados | <li>FullName</li><li>MailFile</li><li>MailServer</li><li>MailDomain</li>
Pessoa | <li>Apelido</li><li>MailFile</li><li>Nome curto</li><li>\_MMS_Password</li><li>\_MMS_IDStoreType</li><li>\_MMS_Certifier</li><li>\_MMS_IDRegType</li><li>\_MMS_UseAdminP</li>
Contacto (pessoa com sem certifier) | <li>\_MMS_IDRegType</li>
Recurso | <li>FullName</li><li>Tipo de recurso</li><li>ConfDB</li><li>CapacidadeRecurso</li><li>Site</li><li>DisplayName em grupos</li><li>MailFile</li><li>MailServer</li><li>MailDomain</li>

## <a name="common-issues-and-questions"></a>Problemas e perguntas comuns

### <a name="schema-detection-does-not-work"></a>Deteção de esquema não funciona
Para conseguir detetar o esquema, é necessário que o ficheiro de schema.nsf é presente no servidor Domino. Este ficheiro só aparece se LDAP estiver instalado no servidor. Se não for detectável o esquema, verifique o seguinte:

- Schema.nsf o ficheiro estiver presente na pasta raiz do servidor Domino
- O utilizador tem permissões para ver o ficheiro schema.nsf.
- Requer um reinício do servidor LDAP. Abrir **Lotus Domino consola** e utilize o comando de **Indicar ao ReloadSchema LDAP** para recarregar o esquema.

### <a name="not-all-secondary-address-books-are-visible"></a>Nem todos os livros de endereços secundário estão visíveis
A conexão Domino baseia-se a funcionalidade **Assistência diretório** possam encontrar os livros de endereços secundário. Se os livros de endereços secundário estiverem em falta, verifique se foi ativado e configurado no servidor de Domino [Diretório assistência](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_ABOUT_DIRECTORY_ASSISTANCE.html) .

### <a name="custom-attributes-in-domino"></a>Atributos personalizados no Domino
Existem várias formas Domino para expandir o esquema para que seja apresentada como um atributo personalizado consumíveis pelo conector.

**Abordagem 1: Expandir Lotus Domino esquema**

1. Criar uma cópia do modelo de diretório Domino {PUBNAMES. NTF} pelos seguintes [estes passos](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_CREATING_A_COPY_OF_THE_DEFAULT_PUBIC_ADDRESS_BOOK_TEMPLATE.html) (não deve personalizar o directório de IBM Lotus Domino modelo predefinido):
2. Abra o modelo de diretório de cópia de Domino {CONTOSO. Modelo NTF} que foi criado no Designer de Domino e siga estes passos:
    - Clique em elementos do partilhado e expanda subformulários
    - Faça duplo clique no subformulário de InheritableSchema ${nome de objecto} (onde {nome de objecto} é o nome da categoria de objeto estruturais predefinido, por exemplo: pessoa).
    - Nome o atributo que pretende adicionar em esquema {MyPersonAtrribute} e correspondente a esse atributo. Criar um campo selecione Menu **Criar** e, em seguida, selecione o **campo** a partir do menu.
    - No campo adicionado, defina as respetivas propriedades ao selecionar o tipo, estilo, tamanho, tipo de letra e outros parâmetros relacionados na janela de propriedades do campo.
    - Manter o atributo valor predefinido mesmo como o nome atribuído para esse atributo (por exemplo, se o nome do atributo MyPersonAttribute, manter o valor predefinido com o mesmo nome).
    - Guarde o subformulário de InheritableSchema ${nome de objecto} com valores actualizados.
3. Substituir o modelo de diretório Domino {PUBNAMES. NTF} com o novo modelo personalizado {CONTOSO. NTF} pelos seguintes [estes passos](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_ABOUT_RULES_FOR_CUSTOMIZING_THE_PUBLIC_ADDRESS_BOOK.html).
4. Fechar Domino Admin e abra Domino consola para reiniciar o serviço LDAP e para recarregar o esquema de LDAP:
    - Na consola do Domino, inserir o comando em texto **Domino comando** arquivado para reiniciar o serviço LDAP - [Reinicie o LDAP de tarefa]( http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_STARTING_AND_STOPPING_THE_LDAP_SERVER_OVER.html).
    - Para recarregar LDAP esquema utilize LDAP indicar ao comando - saber ReloadSchema LDAP
5. Abrir Domino administrador e selecione o separador pessoas e grupos para ver o atributo adicionado é refletido na domino adicionam pessoa.
6. Abrir Schema.nsf a partir do separador **ficheiros** e ver atributo adicionado é refletido para a classe de objectos LDAP dominoPerson.

**Abordagem 2: Criar um auxClass com o atributo personalizado e associar a classe de objecto**

1. Criar uma cópia do modelo de diretório Domino {PUBNAMES. NTF} pelos seguintes [estes passos](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_CREATING_A_COPY_OF_THE_DEFAULT_PUBIC_ADDRESS_BOOK_TEMPLATE.html) (nunca personalizar o directório de IBM Lotus Domino modelo predefinido):
2. Abra o modelo de diretório de cópia de Domino {CONTOSO. Modelo NTF} que foi criado, no Designer de Domino.
3. No painel esquerdo, selecione partilhado código e, em seguida, subformulários.
4. Clique em novo subformulário
5. Efetue o seguinte procedimento para especificar as propriedades para o novo subformulário:
    - Com o novo subformulário aberta, selecione estrutura - subformulário propriedades
    - Junto a propriedade nome, introduza um nome para a classe de objecto auxiliar – por exemplo, TestSubform.
    - Manter a propriedade opções de "Incluir no subformulário inserir … diálogo" seleccionada
    - Desmarque a opções propriedade "composição de passagem HTML nas notas."
    - Deixe as outras propriedades da mesma e feche a caixa propriedades do subformulário.
    - Guarde e feche o subformulário novo.
6. Faça o seguinte para adicionar um campo para definir a classe de objecto auxiliar:
    - Abra o subformulário que criou.
    - Selecione criar - de campo.
    - Junto ao nome no separador Noções básicas da caixa de diálogo campo, especificar um nome qualquer, por exemplo: {MyPersonTestAttribute}.
    - No campo adicionado, defina as respetivas propriedades ao selecionar o tipo, estilo, tamanho, tipo de letra e propriedades relacionadas.
    - Manter o atributo valor predefinido mesmo como o nome atribuído para esse atributo (por exemplo, se o nome do atributo MyPersonTestAttribute, manter o valor predefinido com o mesmo nome).
    - Guarde o subformulário com valores actualizados e efetue o seguinte procedimento:
        - No painel esquerdo, selecione partilhado código e, em seguida, subformulários
        - Selecione novo subformulário e, selecione o Design - propriedades de estrutura.
        - Clique no separador terceiro a partir da esquerda e, selecione **Alterar propagar esta proibição de estrutura**.
7. Abrir ${nome de objecto} ExtensibleSchema subformulário, (onde {nome de objecto} é o nome da categoria de objeto estruturais predefinido, por exemplo – pessoa).
8. Inserir recurso e selecione o subformulário (que criou, por exemplo – TestSubform) e guardar o subformulário de ExtensibleSchema ${nome de objecto}.
9. Substituir o modelo de diretório Domino {PUBNAMES. NTF} com o novo modelo personalizado {CONTOSO. NTF} pelos seguintes [estes passos](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_ABOUT_RULES_FOR_CUSTOMIZING_THE_PUBLIC_ADDRESS_BOOK.html).
10. Fechar Domino Admin e abra Domino consola para reiniciar o serviço LDAP e para recarregar o esquema de LDAP:
    - Na consola do Domino, inserir o comando em texto **Domino comando** arquivado para reiniciar o serviço LDAP - [Reinicie o LDAP de tarefa](http://publib.boulder.ibm.com/infocenter/domhelp/v8r0/index.jsp?topic=%2Fcom.ibm.help.domino.admin85.doc%2FH_STARTING_AND_STOPPING_THE_LDAP_SERVER_OVER.html).
    - Para recarregar LDAP esquema utilize o comando LDAP saber **Onde está ReloadSchema LDAP**.
11. Abra Domino administrador e selecione o separador de pessoas e grupos para ver o atributo adicionado é refletido na domino Adicionar pessoa (em outras pessoas tecla de tabulação).
12. Abrir Schema.nsf a partir do separador **ficheiros** e ver atributo adicionado é refletido em TestSubform LDAP auxiliar classe de objeto.

**Abordagem 3: Adicionar o atributo personalizado para a classe de ExtensibleObject**

1. Abrir um ficheiro de {Schema.nsf} colocado no diretório de raiz
2. Selecione Classes de objectos LDAP no menu à esquerda em **Todos os documentos de esquema** e clique em botão de **classe de objecto adicionar** :
3. Fornece nome LDAP sob a forma de {zzzExtensibleSchema} (onde zzz é o nome da categoria de objeto estruturais predefinido, por exemplo, pessoa). Por exemplo, para expandir o esquema para pessoa classe de objecto, fornece o nome LDAP {PersonExtensibleSchema}.
4. Fornece o nome de classe de objeto Superior, para o qual pretende expandir o esquema. Por exemplo, para expandir o esquema para a classe de objeto da pessoa, fornece o nome de classe de objeto Superior {dominoPerson}:
5. Fornece um OID válido correspondente para a classe de objeto.
6. Selecione os atributos de expandidos/personalizados em campos obrigatórios ou tipos de atributo opcional de acordo com o requisito de registo:
7. Depois de adicionar os atributos necessários para o ExtensibleObjectClass, clique em **Guardar e fechar**.
8. É criada uma ExtensibleObjectClass para a classe de objeto predefinidos respetivos com os atributos expandidos.

## <a name="troubleshooting"></a>Resolução de problemas

-   Para obter informações sobre como ativar o registo resolver a conexão, consulte o artigo [como ativar ETW Tracing para os conectores](http://go.microsoft.com/fwlink/?LinkId=335731).
