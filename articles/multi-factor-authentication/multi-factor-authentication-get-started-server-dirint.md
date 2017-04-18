<properties 
    pageTitle="Integração de diretórios entre o Azure a autenticação Multifator e do Active Directory"
    description="Esta é a página de autenticação multifator Azure que descreve como integrar o servidor de autenticação Multifator do Azure Active Directory para poder sincronizar os directórios."
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="curtand"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/04/2016"
    ms.author="kgremban"/>

# <a name="directory-integration-between-azure-mfa-server-and-active-directory"></a>Integração de diretórios entre o Azure MFA Server e o Active Directory

A secção de integração de diretórios permite-lhe configurar o servidor para integrar com o Active Directory ou outro directório LDAP.  Permite-lhe configurar atributos para corresponder ao esquema de diretório e configurar a sincronização automática de utilizadores.

## <a name="settings"></a>Definições
Por predefinição, o servidor de autenticação Multifator Azure está configurado para importar ou sincronizar os utilizadores a partir do Active Directory.  No separador permite-lhe para substituir o comportamento predefinido e para vincular a um directório LDAP diferente, um diretório de AFONSO ou controlador de domínio do Active Directory específico.  Também fornece para a utilização da autenticação de LDAP para proxy LDAP ou para vincular LDAP como um destino RADIUS, a pré-autenticação para autenticação do IIS ou autenticação principal para o Portal de utilizador.  A tabela seguinte descreve as definições individuais.

![Definições](./media/multi-factor-authentication-get-started-server-dirint/dirint.png)

| Funcionalidade | Descrição |
| ------- | ----------- |
| Utilizar o Active Directory | Selecione a opção utilizar Active Directory para utilizar o Active Directory para importar e a sincronização.  Esta é a predefinição. <br>Nota: O computador tem de ser associado a um domínio e tem de ter sessão iniciada com uma conta de domínio para integração do Active Directory funcione corretamente. |
| Incluir domínios fidedignos | A caixa de verificação Incluir domínios fidedignos para que a tentativa de agente ao ligar aos domínios fidedignos pelo domínio atual, outro domínio na floresta ou domínios de verificação, normalmente uma fidedignidade de floresta.  Quando não importar ou sincronizar os utilizadores a partir de qualquer um dos domínios fidedignos, desmarque a caixa de verificação para melhorar o desempenho.  A predefinição é verificada. |
| Utilizar a configuração LDAP específica | Selecione a opção Utilizar LDAP para utilizar as definições de LDAP especificadas para importar e sincronização. Nota: Quando utilizar LDAP está selecionada, a interface de utilizador altera referências a partir do Active Directory para LDAP. |
| Botão Editar | O botão Editar permite que as definições de configuração de LDAP atuais alterada. |
| Utilizar consultas de âmbito de atributo | Indica se as consultas de âmbito atributo devem ser utilizadas.  Consultas de âmbito atributo permitem para pesquisas de diretório eficiente qualificar registos com base em entradas no atributo outro registo.  O servidor de autenticação Multifator Azure utiliza consultas de âmbito de atributo para utilizadores que forem membro de um grupo de segurança de consulta eficazmente.   <br>Nota: Não existem alguns casos onde consultas de âmbito atributo são suportadas, mas não deveriam ser utilizadas.  Por exemplo, do Active Directory pode ter problemas com as consultas de âmbito atributo quando um grupo de segurança contém os membros a partir de mais do que um domínio.  Neste caso, a caixa de verificação deverá ser desmarcada. |

A tabela seguinte descreve as definições de configuração de LDAP.

| Funcionalidade | Descrição |
| ------- | ----------- |
| Servidor | Introduza o nome do anfitrião ou o endereço IP do servidor a executar o directório LDAP.  Um servidor de cópia de segurança também pode ser especificado separados por um ponto e vírgula. <br>Nota: Quando vincular tipo é SSL, um nome de anfitrião completamente qualificado é geralmente necessário. |
| Base DN | Introduza o nome exclusivo do objeto base diretório a partir do qual serão iniciado todas as consultas de diretório.  Por exemplo, Cc = abc, CC = com. |
| Vincular tipo - consultas | Selecione o tipo de vincular adequado para utilizar quando se liga para procurar no directório LDAP.  Isto é utilizado para importações, sincronização e resolução do nome de utilizador. <br><br>  Anónima - será efetuada uma ligação anónima.  Vincular DN e vincular palavra-passe não serão utilizadas.  Isto apenas irá funcionar se o directório LDAP permite anónima encadernação e as permissões permitem consultar os registos apropriados e atributos.  <br><br> Como texto simples para vincular a directório LDAP serão transmitida simples - vincular DN e vincular palavra-passe.  Isto deve apenas ser utilizado para fins de teste para verificar que é possível alcançar o servidor e que a conta de vincular tem acesso adequado.  É recomendado que SSL ser utilizado em vez disso, após ter sido instalada a orientação do diapositivo notas adequada.  <br><br> SSL - vincular DN e palavra-passe vincular será encriptado utilizar SSL para ligar para o directório LDAP.  Isto requer que uma orientação do diapositivo notas ser instalado localmente que confia directório LDAP.  <br><br> Windows - vincular nome de utilizador e palavra-passe vincular será utilizado para segura ligar a um controlador de domínio do Active Directory ou do diretório AFONSO.  Se vincular o nome de utilizador for deixado em branco, será utilizada para vincular a sessão iniciada numa conta de utilizador. |
| Vincular - tipo de autenticação | Selecione o tipo de vincular adequado para utilização ao executar a autenticação de vincular LDAP.  Consulte o artigo vincular descrições em tipo de vincular - consultas tipo.  Por exemplo, isto permite anónimo vincular a ser utilizado para consultas enquanto vincular SSL é utilizada para proteger a autenticação de vincular LDAP. |
| Vincular DN ou vincular nome de utilizador | Introduza o nome distinto do registo de utilizador para a conta a utilizar quando se liga ao directório LDAP.<br><br>O nome distinto vincular só é utilizado quando vincular tipo é simples ou SSL.  <br><br>Introduza o nome de utilizador da conta do Windows para utilizar quando se liga ao directório LDAP quando vincular tipo é Windows.  Se deixado em branco, será utilizada para vincular a sessão iniciada numa conta de utilizador. |
| Vincular a palavra-passe | Introduza a palavra-passe vincular para o vincular DN ou o nome de utilizador que está a ser utilizado para ligar para o directório LDAP.  Para configurar a palavra-passe para o serviço de AdSync do servidor de autenticação Multifator, a sincronização tem de estar ativada e o serviço tem de estar em execução no computador local.  A palavra-passe será guardada no Windows armazenados nomes de utilizador e palavras-passe em conta que o serviço de AdSync do servidor de autenticação Multifator está a funcionar como.  A palavra-passe também será guardada em conta que a interface de utilizador do servidor de autenticação Multifator está a funcionar como e, em conta que o serviço de servidor de autenticação Multifator está a funcionar como.  <br><br> Nota: Dado que a palavra-passe só é armazenada no Windows armazenados liste e palavras-passe do servidor local, este passo terão de ser executadas em cada servidor de autenticação Multifator que precisa de aceder à palavra-passe. |
| Limite de tamanho de consulta | Especifique o tamanho limite para o número máximo de utilizadores que irá devolver uma pesquisa do diretório.  Este limite deve corresponder à configuração no directório LDAP.  Para grandes pesquisas onde não é suportada paginação, importar e sincronização irão tentar obter utilizadores em lotes.  Se o limite de tamanho especificado aqui for maior que o limite que configurou no directório LDAP, poderão ser perdidas alguns utilizadores. |
| Botão Testar | Clique no botão de teste para testar a ligação para o servidor LDAP.  <br><br> Nota: A opção Utilizar LDAP não necessitam de estar selecionada para testar a ligação.  Isto permite que o enlace para ensaiar antes de utilizar a configuração de LDAP. |

## <a name="filters"></a>Filtros
Filtros permitem-lhe definir critérios para qualificar registos quando executa uma pesquisa do diretório.  Ao definir o filtro pode reduzir os objetos que pretende sincronizar.  

![Filtros](./media/multi-factor-authentication-get-started-server-dirint/dirint2.png)

Autenticação Multifator Azure tem as seguintes 3 opções.

- **Filtro de contentor** - especifique os critérios de filtro utilizados para qualificar registos contentor quando executa uma pesquisa do diretório.  Para o Active Directory e AFONSO, (| ( objectClass=organizationalUnit)(objectClass=container)) é normalmente utilizado.  Para outros directórios LDAP, os critérios de filtragem são elegíveis cada tipo de objecto contentor deverão ser utilizados consoante o esquema de diretório.  <br>Nota: Se estiver em branco, para a esquerda ((objectClass=organizationalUnit)(objectClass=container)) será utilizada por predefinição.

- **Filtro de grupo de segurança** - especifique os critérios de filtro utilizados para qualificar registos de grupo de segurança quando executa uma pesquisa do diretório.  Para o Active Directory e AFONSO, (&(objectCategory=group) (groupType:1.2.840.113556.1.4.804:=-2147483648)) é geralmente utilizada.  Para outros directórios LDAP, os critérios de filtragem são elegíveis cada tipo de objeto do grupo de segurança deverão ser utilizados consoante o esquema de diretório.  <br>Nota: Se deixado em branco, (&(objectCategory=group) (groupType:1.2.840.113556.1.4.804:=-2147483648)) será utilizada por predefinição.

- **Filtro do utilizador** - especifique os critérios de filtro utilizados para qualificar registos de utilizador quando executa uma pesquisa do diretório.  Para o Active Directory e AFONSO, (e (objectClass=user)(objectCategory=person)) é geralmente utilizada.  Para outros directórios LDAP (classe de objecto = inetOrgPerson) ou algo semelhante deve ser utilizado consoante o esquema de diretório. <br>Nota: Se deixado em branco, (e (objectCategory=person)(objectClass=user)) ser utilizado por predefinição.

## <a name="attributes"></a>Atributos
Atributos podem ser personalizados conforme necessário para um diretório específico.  Esta opção permite-lhe adicionar atributos personalizados e ajustar a sincronização de diretórios com apenas os atributos que necessita.  O valor para cada campo atributo deve ser o nome do atributo, tal como foi definido no esquema de diretório.  Utilize a tabela abaixo para obter informações adicionais.

![Atributos](./media/multi-factor-authentication-get-started-server-dirint/dirint3.png)

| Funcionalidade | Descrição |
| ------- | ----------- |
| Identificador exclusivo | Introduza o nome do atributo do atributo que serve o identificador exclusivo do contentor, grupo de segurança e registos de utilizador.  No Active Directory, este nome costuma GUID de objecto.  Em outras implementações LDAP, pode ser entryUUID ou algo semelhante.  A predefinição é GUID de objecto. |
| ... Botões (atributo select) | Cada campo atributo tem um botão "… junto ao mesmo que irá apresentar a caixa de diálogo Seleccionar atributo permitindo um atributo ser selecionados a partir de uma lista". <br><br>Selecione a caixa de diálogo do atributo.<br><br>Nota: Atributos podem ser introduzidos manualmente e não são necessárias para que correspondam a um atributo na lista de atributo. |
| Tipo de identificador exclusivo | Selecione o tipo do atributo Identificador exclusivo.  No Active Directory, o atributo GUID de objecto é GUID.  Em outras implementações LDAP, pode ser do tipo matriz de bytes ASCII ou cadeia.  A predefinição é GUID. <br><br>Nota: É importante isto corretamente definida uma vez que itens de sincronização são referenciadas pela sua Identificador exclusivo e o tipo de identificador exclusivo é utilizado para localizar diretamente no objeto no diretório.  Esta definição para cadeia quando o directório realmente armazena o valor, tal como uma matriz de bytes de caracteres ASCII irá impedir a sincronização de funcionar corretamente. |
| Nome distinto | Introduza o nome do atributo do atributo que contém o nome exclusivo para cada registo.  No Active Directory, este nome costuma distinguishedName.  Em outras implementações LDAP, pode ser entryDN ou algo semelhante.  A predefinição é distinguishedName. <br><br>Nota: Se não existir um atributo que contém apenas o nome exclusivo, o atributo adspath pode ser utilizado.  O "LDAP: / /<server>/" parte do caminho da irão ser removido automaticamente desativar deixando apenas o nome exclusivo do objeto. |
| Nome do contentor | Introduza o nome do atributo do atributo que contém o nome num registo de contentor.  O valor deste atributo é apresentado na hierarquia de contentor ao importar a partir do Active Directory ou a adição de itens de sincronização.  A predefinição é o nome. <br><br>Nota: Se contentores diferentes utilizam atributos diferentes para os seus nomes, vários atributos de nome de contentor podem ser especificados no separados por ponto e vírgula.  O primeiro contentor foi encontrado atributo name num objeto contentor será utilizado para apresentar o respetivo nome. |
| Nome do grupo de segurança | Introduza o nome do atributo do atributo que contém o nome de um registo de grupo de segurança.  O valor deste atributo será apresentado na lista de grupo de segurança ao importar a partir do Active Directory ou a adição de itens de sincronização.  A predefinição é o nome. |
| Utilizadores | Os seguintes atributos são utilizados para procurar, apresentar, importar e sincronizar informações de utilizador do diretório. |
| Nome de utilizador | Introduza o nome do atributo do atributo que contém o nome de utilizador num registo de utilizador.  O valor deste atributo será utilizado como o nome de utilizador do servidor de autenticação Multifator.  Um segundo atributo pode ser especificado como uma cópia de segurança para o primeiro.  O atributo segundo só será utilizado se o atributo primeiro não contiver um valor para o utilizador.  As predefinições são userPrincipalName e sAMAccountName. |
| Nome próprio | Introduza o nome do atributo do atributo que contém o primeiro nome num registo de utilizador.  A predefinição é givenName. |
| Apelido | Introduza o nome do atributo do atributo que contém o último nome num registo de utilizador.  A predefinição é sn. |
| Endereço de e-mail | Introduza o nome do atributo do atributo que contém o endereço de correio eletrónico num registo de utilizador.  Endereço de e-mail será utilizado para enviar bem-vindo ao e atualize os e-mails para o utilizador.  A predefinição é correio. |
| Grupo de utilizadores | Introduza o nome do atributo do atributo que contém o grupo de utilizador num registo de utilizador.  Grupo de utilizador pode ser utilizado para filtrar os utilizadores no agente e nos mapas no Portal de gestão de servidor do Multifator Auth. |
| Descrição | Introduza o nome do atributo do atributo que contém a descrição num registo de utilizador.  Descrição só é utilizada para procurar.  A predefinição é descrição. |
| Idioma de chamada de voz | Introduza o nome do atributo do atributo que contém o nome abreviado do idioma a utilizar para chamadas de voz do utilizador. |
| Idioma do texto SMS | Introduza o nome do atributo do atributo que contém o nome abreviado do idioma a utilizar para as mensagens de texto SMS para o utilizador. |
| Idioma da aplicação de telefone | Introduza o nome do atributo do atributo que contém o nome abreviado do idioma a utilizar para mensagens de texto de aplicação de telefone para o utilizador. |
| Idioma token JURAMENTO | Introduza o nome do atributo do atributo que contém o nome abreviado do idioma a utilizar para mensagens de texto token de JURAMENTO para o utilizador. |
| Telemóveis | Os seguintes atributos são utilizados para importar ou sincronizar números de telefone do utilizador.  Se não for especificado um nome de atributo do tipo de telefone, o tipo de telefone não estará disponível quando a importação a partir do Active Directory ou ao adicionar itens de sincronização. |
| Empresas | Introduza o nome do atributo do atributo que contém o número de telefone de negócio num registo de utilizador.  A predefinição é telephoneNumber. |
| Home page | Introduza o nome do atributo do atributo que contém o número de telefone de casa num registo de utilizador.  A predefinição é telefone. |
| Pager | Introduza o nome do atributo do atributo que contém o número de pager num registo de utilizador.  A predefinição é pager. |
| Mobile | Introduza o nome do atributo do atributo que contém o número de telemóvel num registo de utilizador.  A predefinição é móvel. |
| Fax | Introduza o nome do atributo do atributo que contém o número de fax num registo de utilizador.  A predefinição é facsimileTelephoneNumber. |
| Telefone de IP | Introduza o nome do atributo do atributo que contém o número de telefone IP num registo de utilizador.  A predefinição é ipPhone. |
| Personalizada | Introduza o nome do atributo do atributo que contém um número de telefone personalizado |
|  | um registo de utilizador.  A predefinição é em branco. |
| Extensão | Introduza o nome do atributo do atributo que contém a extensão do número de telefone num registo de utilizador.  O valor do campo extensão será utilizado como a extensão para apenas o número de telefone principal.  A predefinição é em branco. <br><br>Nota: Se o atributo extensão não for especificado, extensões podem ser incluídas como parte do atributo telefone.  A extensão deve ser precedida com um x para poder ser analisada.  Por exemplo 555-123-4567 x890 resultaria 555-123-4567 como o número de telefone e 890 como a extensão. |
| Restaurar predefinições de botão | Clique no botão Restaurar predefinições para voltar a todos os atributos ao respetivo valor predefinido.  As predefinições deverão funcionar corretamente com o esquema do Active Directory ou AFONSO normal. |

Para editar atributos, basta clicar no botão Editar no separador atributos.  Isto irá trazer o windows que lhe permite editar os atributos.

![Editar atributos](./media/multi-factor-authentication-get-started-server-dirint/dirint4.png)

## <a name="synchronization"></a>Sincronização de diretórios
A sincronização mantém a base de dados de utilizador do Azure Multifator sincronizado com os utilizadores no Active Directory ou outro directório Lightweight Directory Access Protocol (LDAP) Lightweight Directory Access Protocol.  O processo é semelhante à importação utilizadores manualmente a partir do Active Directory, mas periodicamente consulta de utilizador do Active Directory e alterações de grupo de segurança processar.  Também fornece para desativar ou remover utilizadores removido de um grupo de segurança ou contentor e remover utilizadores eliminados do Active Directory.

O serviço de Multifator Auth ADSync é um serviço Windows que executa o inquéritos periódicos do Active Directory.  Este não é devem ser confundidas com Azure AD Sync ou ligação do Azure AD.  ADSync de autenticação Multifator, embora incorporadas num código base semelhantes, é específico ao servidor de autenticação Multifator Azure.  Mesmo está instalado num estado parado e é iniciado pelo serviço do servidor de autenticação Multifator quando configurado para executar.  Se tiver uma configuração de servidor de autenticação Multifator de servidor com várias, o Auth ADSync Multifator só pode ser executado num servidor único.

O serviço de Multifator Auth ADSync utiliza a extensão de servidor do DirSync LDAP fornecida pela Microsoft eficazmente Verifique a existência de alterações.  Este autor de controlo do DirSync tem de ter "directório obter alterações" para a direita e DS replicação-obter alterações expandido controlar o acesso à direita.  Por predefinição, estes direitos são atribuídos às contas de administrador e sistema local em controladores de domínio.  O serviço de Multifator Auth AdSync está configurado para executar o sistema local por predefinição.  Por conseguinte, é mais simples executar o serviço num controlador de domínio.  O serviço pode executar como uma conta com permissões menor se configurar que seja executada sempre uma sincronização completa.  Esta é menos eficiente, mas requer menos privilégios de conta.

Se configurado para utilizar LDAP e o directório LDAP suporta o controlo de DirSync, em seguida, de consulta de utilizador e alterações de grupo de segurança irá funcionam da mesma forma que faz com o Active Directory.  Se o directório LDAP não suporta o controlo de DirSync, uma sincronização completa ser executada durante o ciclo de cada.

![Sincronização de diretórios](./media/multi-factor-authentication-get-started-server-dirint/dirint5.png)

Utilize a tabela abaixo para obter informações adicionais sobre cada uma das definições no separador Sincronização individuais.

| Funcionalidade | Descrição |
| ------- | ----------- |
| Ativar a sincronização com o Active Directory | Quando selecionada, o serviço do servidor de autenticação Multifator será iniciado para periodicamente as inquérito do Active Directory para as alterações. <br><br>Nota: tem de ser adicionado, pelo menos, um Item de sincronização e um sincronizar agora têm de ser executada antes do servidor de autenticação Multifator serviço será iniciado a processar alterações. |
| Sincronizar todas as | Especifique o intervalo de tempo que o serviço do servidor de autenticação Multifator irá aguardar entre de consulta e a processar alterações. <br><br> Nota: O intervalo especificado é o tempo entre o início de cada ciclo.  Se o tempo a processar alterações excede o intervalo, o serviço será consultar retomada de imediato. |
| Remover utilizadores já não no Active Directory | Quando selecionada, o serviço do servidor de autenticação Multifator serão tombstones de utilizador do Active Directory eliminados do processo e remover o utilizador Multifator Auth servidor relacionado. |
| Executar sempre uma sincronização completa | Quando selecionada, o serviço do servidor de autenticação Multifator sempre irá efetuar uma sincronização completa.  Quando estiver desmarcado, o serviço do servidor de autenticação Multifator irá efetuar uma sincronização utilizarão consultando apenas os utilizadores que foram alteradas.  A predefinição está desmarcada. <br><br> Nota: Quando desmarcado, uma sincronização utilizarão só pode ser executada quando o diretório suporta o controlo de DirSync e a conta que está a ser utilizada para ligar ao diretório tem as permissões adequadas para executar consultas de utilizarão DirSync.  Se a conta não possui as permissões adequadas ou vários domínios estão, normalmente a sincronização, execute um inteiro a sincronização é recomendada. |
| Exigir a aprovação do administrador quando mais do que X os utilizadores irão ser desactivada ou removida | Itens de sincronização podem ser configurados para desativar ou remover os utilizadores que já não são um membro do grupo de segurança ou contentor do item.  Como salvaguarda, aprovação do administrador pode ser necessária quando o número de utilizadores para desativar ou remover excede um determinado limiar.  Quando selecionada, aprovação será necessária para o limiar especificado.  A predefinição é de 5 e o intervalo é 1 e 999. <br><br> Aprovação é facilitada ao primeiro enviar uma notificação de e-mail para os administradores. A notificação de correio eletrónico dá-instruções para a revisão e aprovação a desativação e a remoção de utilizadores.  Quando a interface de utilizador do servidor de autenticação Multifator é iniciada, irá pedir para aprovação. |

O botão **Sincronizar agora** permite-lhe executar uma sincronização completa para os itens de sincronização especificado.  É necessária uma sincronização completa sempre que os itens de sincronização são adicionadas, modificadas, removidos ou reordenados.  Também é necessário antes do serviço de Multifator Auth AdSync será operacionais avançado, uma vez que define o ponto de partida a partir do qual o serviço serão as inquérito para utilizarão alterações.  Se foram efetuadas alterações para itens de sincronização e uma sincronização completa não foi executada, vai ser-lhe para sincronizar agora ao navegar para outra secção ou durante o fecho da interface de utilizador.

Botão **Remover** permite ao administrador eliminar um ou mais itens de sincronização a partir da lista de item de sincronização do servidor de autenticação Multifator.

>[AZURE.WARNING]Assim que um registo de item de sincronização ter sido removido, não é possível recuperá-la. Terá de voltar a adicionar o registo de item de sincronização, caso tenha eliminado por engano.

O item de sincronização ou itens de sincronização foram removidas do servidor de autenticação Multifator.  O serviço do servidor de autenticação Multifator já não processará os itens de sincronização.

Os botões Mover para cima e mover para baixo permitir que o administrador alterar a ordem dos itens de sincronização.  A ordem é importante uma vez que o mesmo utilizador pode ser um membro de mais do que um item de sincronização (por exemplo, um contentor e um grupo de segurança).  As definições de aplicado ao utilizador durante a sincronização serão proveniente do primeiro item de sincronização na lista à qual o utilizador está associado.  Por isso, os itens de sincronização devem ser colocados em ordem de prioridade.

>[AZURE.TIP]Uma sincronização completa deve ser executada depois de remover itens de sincronização.  Uma sincronização completa deve ser executada depois de ordenação de itens de sincronização.  Clique no botão Sincronizar agora para efetuar uma sincronização completa.

## <a name="multi-factor-auth-servers"></a>Servidores de autenticação Multifator
Servidores de autenticação Multifator adicionais podem ser configurados para servir como um proxy RADIUS cópia de segurança, o proxy LDAP, ou para a autenticação do IIS. A configuração da sincronização será partilhada entre todos os agentes. No entanto, apenas um dos seguintes agentes poderá ter o funcionamento de serviço do servidor de autenticação Multifator. Este separador permite-lhe selecionar o servidor de autenticação Multifator que deve ser ativado para a sincronização.

![Servidores de multi Multifator Auth](./media/multi-factor-authentication-get-started-server-dirint/dirint6.png)
