<properties
   pageTitle="Conector LDAP genérico | Microsoft Azure"
   description="Este artigo descreve como configurar o conector de LDAP genérico da Microsoft."
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

# <a name="generic-ldap-connector-technical-reference"></a>Referência de técnica de conexão LDAP genérica
Este artigo descreve a conexão LDAP genérico. O artigo aplica-se aos seguintes produtos:

- Gestor de identidades do Microsoft 2016 (MIM2016)
- Gestor de identidades do Forefront 2010 R2 (FIM2010R2)
    -   Tem de utilizar correcção 4.1.3671.0 ou posterior [KB3092178](https://support.microsoft.com/kb/3092178).

Para MIM2016 e FIM2010R2, o conector está disponível como uma transferência a partir do [Centro de transferências da Microsoft](http://go.microsoft.com/fwlink/?LinkId=717495).

Quando se referir à IETF RFCs, este documento está a utilizar o formato (RFC [número de RFC] / [secção no documento RFC]), por exemplo (RFC 4512/4.3).
Pode encontrar mais informações em http://tools.ietf.org/html/rfc4500 (ter para substituir 4500 com o número RFC correcto).

## <a name="overview-of-the-generic-ldap-connector"></a>Descrição geral da conexão LDAP genérico
A conexão LDAP genérico permite-lhe integrar o serviço de sincronização com o servidor LDAP v3.

Certas operações e elementos de esquema, como aqueles necessário para executar importação delta, não estão especificados nos IETF RFCs. Para estas operações apenas directórios LDAP explicitamente especificados são suportados.

A partir de uma perspetiva de alto nível, as seguintes funcionalidades são suportadas pelo versão atual do conector:

Funcionalidade | Suporte
--- | --- |
Origem de dados ligada | A conexão é suportada com todos os servidores de v3 LDAP (RFC 4510 em conformidade). Foi testado com o seguinte: <li>Serviços de Lightweight Directory do Microsoft Active Directory (AD LDS)</li><li>Catálogo Global Microsoft Active Directory (AD GC)</li><li>Servidor de directório 389</li><li>Servidor de diretório Apache</li><li>Tivoli da IBM DS</li><li>Diretório Isode</li><li>NetIQ eDirectory</li><li>Novell eDirectory</li><li>Abrir DJ</li><li>Abrir DS</li><li>Abrir LDAP (openldap.org)</li><li>Oracle (anteriormente DOM) diretório Server Enterprise Edition</li><li>Servidor de directório Virtual RadiantOne (VDS)</li><li>DOM um servidor de directório</li>**Diretórios dos não suportados:** <li>Microsoft Active Directory Domain Services (AD DS) [o conector do Active Directory incorporados como alternativa, utilize]</li><li>Diretório da Internet do Oracle (OID)</li>
Cenários   | <li>Gestão de ciclo de vida do objeto</li><li>Gestão de grupos</li><li>Gestão de palavra-passe</li>
Operações |As seguintes operações são suportadas em todos os directórios LDAP: <li>Importação completa</li><li>Exportar</li>As seguintes operações só são suportadas em directórios especificados:<li>Importar delta</li><li>Definir palavra-passe, alterar palavra-passe</li>
Esquema | <li>Esquema é detectada do esquema LDAP (RFC3673 e RFC4512/4.2)</li><li>Suporta classes estruturais, aux aulas e classe de objecto extensibleObject (RFC4512/4.3)</li>

### <a name="delta-import-and-password-management-support"></a>Delta suporte a gestão de importação e palavra-passe
Suportado nos diretórios para importar Delta e gestão de palavra-passe:

- Serviços de Lightweight Directory do Microsoft Active Directory (AD LDS)
    - Suporta todas as operações de importação de variação
    - Suporta definir palavra-passe
- Catálogo Global Microsoft Active Directory (AD GC)
    - Suporta todas as operações de importação de variação
    - Suporta definir palavra-passe
- Servidor de directório 389
    - Suporta todas as operações de importação de variação
    - Suporta definir palavra-passe e alterar palavra-passe
- Servidor de diretório Apache
    - Não suporta a importação de delta uma vez que este diretório não têm um registo de alterações persistente
    - Suporta definir palavra-passe
- Tivoli da IBM DS
    - Suporta todas as operações de importação de variação
    - Suporta definir palavra-passe e alterar palavra-passe
- Diretório Isode
    - Suporta todas as operações de importação de variação
    - Suporta definir palavra-passe e alterar palavra-passe
- Novell eDirectory e NetIQ eDirectory
    - Suporta operações de adicionar, atualizar e mudar o nome para a importação de variação
    - Não suporta operações de eliminação para importação delta
    - Suporta definir palavra-passe e alterar palavra-passe
- Abrir DJ
    - Suporta todas as operações de importação de variação
    - Suporta definir palavra-passe e alterar palavra-passe
- Abrir DS
    - Suporta todas as operações de importação de variação
    - Suporta definir palavra-passe e alterar palavra-passe
- Abrir LDAP (openldap.org)
    - Suporta todas as operações de importação de variação
    - Suporta definir palavra-passe
    - Não suporta alterar palavra-passe
- Oracle (anteriormente DOM) diretório Server Enterprise Edition
    - Suporta todas as operações de importação de variação
    - Suporta definir palavra-passe e alterar palavra-passe
- Servidor de directório Virtual RadiantOne (VDS)
    - Tem de utilizar a versão 7.1.1 ou superior
    - Suporta todas as operações de importação de variação
    - Suporta definir palavra-passe e alterar palavra-passe
-  DOM um servidor de directório
    - Suporta todas as operações de importação de variação
    - Suporta definir palavra-passe e alterar palavra-passe

### <a name="prerequisites"></a>Pré-requisitos
Antes de utilizar a conexão, certifique-se de que tem o seguinte procedimento no servidor de sincronização:

- 4.5.2 do Microsoft .NET Framework ou posterior

### <a name="detecting-the-ldap-server"></a>Detetar o servidor LDAP
A conexão baseia-se na várias técnicas para detetar e identificar o servidor LDAP. A conexão utiliza na raiz DSE, nome/versão do fabricante e -inspeciona o esquema para localizar objetos exclusivos e atributos conhecidos existir na determinados servidores LDAP. Estes dados, se encontrar, é utilizado para preencher as opções de configuração no Connector previamente.

### <a name="connected-data-source-permissions"></a>Permissões de origem de dados ligadas
Para executar a importar e exportar operações sobre os objectos no diretório ligado, a conta do connector tem de ter permissões suficientes. A conexão tem permissões para conseguir exportar de escrita e permissões de leitura para consiga importar. Configuração de permissões é realizada dentro das experiências de gestão do próprio directório de destino.

### <a name="ports-and-protocols"></a>Portas e protocolos
A conexão utiliza o número de porta especificado na configuração, que, por predefinição, é 389 para LDAP e 636 para LDAPS.

Para LDAPS, tem de utilizar SSL 3.0 ou TLS. SSL 2.0 não é suportada e não pode ser ativado.

### <a name="required-controls-and-features"></a>Controlos necessários e funcionalidades
As seguintes controlos LDAP/funcionalidades tem de estar disponíveis no servidor LDAP para o conector funcione corretamente:  
`1.3.6.1.4.1.4203.1.5.3`Filtros de verdadeiro/falso

O filtro de True/False não é frequentemente comunicado como suportados pelo directórios LDAP e poderá aparecer na **Página Global** em **Obrigatório funcionalidades não encontrado**. É utilizado para criar **ou** filtros em consultas LDAP, por exemplo, ao importar vários tipos de objeto. Se pode importar mais do que um tipo de objeto, o servidor LDAP suporta esta funcionalidade.

Se utilizar um diretório onde um identificador exclusivo está a âncora o seguinte procedimento também têm de ser disponível (consulte a secção [Configurar as âncoras](#configure-anchors) mais adiante neste artigo para obter mais informações):  
`1.3.6.1.4.1.4203.1.5.1`Todos os atributos operacionais avançados

Se o diretório tiver mais objectos que o que pode ajustar numa chamada ao diretório, em seguida, é recomendado utilizar paginação. Para a paginação trabalhar, precisa de uma das seguintes opções:

**Opção 1:**  
`1.2.840.113556.1.4.319`pagedResultsControl

**Opção 2:**  
`2.16.840.1.113730.3.4.9`VLVControl  
`1.2.840.113556.1.4.473`SortControl

Se ambas as opções estiverem ativadas na configuração de conexão, é utilizado pagedResultsControl.

`1.2.840.113556.1.4.417`ShowDeletedControl

ShowDeletedControl só é utilizado com o método de importação de delta USNChanged para conseguirá ver os objetos eliminados.

A conexão tentará detetar as opções presentes no servidor. Se não podem ser detectadas as opções, um aviso é presente na página Global nas propriedades do conector. Nem todos os servidores LDAP apresentar todos os controlos/funcionalidades suporte e mesmo se este aviso estiver presente, o conector pode funcionar sem problemas.

### <a name="delta-import"></a>Importar delta
Importar delta só está disponível quando foi detectado um diretório de suporte. Atualmente são utilizados os seguintes métodos:

- LDAP Accesslog. Consulte o artigo [http://www.openldap.org/doc/admin24/overlays.html#Access registo](http://www.openldap.org/doc/admin24/overlays.html#Access Logging)
- LDAP Changelog. Consulte o artigo [http://tools.ietf.org/html/draft-good-ldap-changelog-04](http://tools.ietf.org/html/draft-good-ldap-changelog-04)
- Carimbo de data/hora. Para Novell/NetIQ eDirectory, o conector utiliza data/hora da última para obter criado e actualizado objetos. Novell/NetIQ eDirectory não fornece que uma equivalente significa para obter os objetos eliminados. Também pode ser utilizada esta opção se não existe outro método de importação de delta está ativo no servidor LDAP. Esta opção não é possível importar eliminado objectos.
- USNChanged. Consulte o artigo: [https://msdn.microsoft.com/library/ms677627.aspx](https://msdn.microsoft.com/library/ms677627.aspx)

### <a name="not-supported"></a>Não é suportado
As seguintes funcionalidades LDAP não são suportadas:

- Referências de LDAP entre servidores (RFC 4511/4.1.10)

## <a name="create-a-new-connector"></a>Criar uma nova conexão
Para criar um conector de LDAP genérico, no **Serviço de sincronização** , selecione **Agente de gestão** e **Criar**. Selecione a conexão **LDAP genérico (Microsoft)** .

![CreateConnector](./media/active-directory-aadconnectsync-connector-genericldap/createconnector.png)

### <a name="connectivity"></a>Conectividade
Na página Conetividade, tem de especificar as informações de anfitrião, porta e enlace. Dependendo de qual enlace é selecionado, adicionais poderão fornecer as informações nas secções seguintes.

![Conectividade](./media/active-directory-aadconnectsync-connector-genericldap/connectivity.png)

- A definição de tempo limite da ligação só é utilizada para a primeira ligação ao servidor quando detetar o esquema.
- Se a associação é acesso anónimo, em seguida, nenhuma nome de utilizador / palavra-passe nem certificado são utilizadas.
- Para outras enlaces, introduza informações ou em nome de utilizador / palavra-passe ou selecione um certificado.
- Se estiver a utilizar Kerberos para autenticar, em seguida, também fornecem o Realm domínio do utilizador.

A caixa de texto **aliases de atributo** é utilizada para atributos definidos no esquema de com RFC4522 sintaxe. Estes atributos não podem ser detetados durante a deteção de esquema e a conexão precisa de ajuda para identificar esses atributos. Por exemplo o seguinte é necessário para ser introduzida na caixa de aliases de atributo para identificar corretamente o atributo userCertificate como um atributo binário:

`userCertificate;binary`

Segue-se um exemplo de como esta configuração pode ter aspeto:

![Conectividade](./media/active-directory-aadconnectsync-connector-genericldap/connectivityattributes.png)

Selecione a caixa de verificação **incluir operacionais atributos no esquema** para incluir também atributos criados pelo servidor. Estes incluir atributos tal como quando o objeto foi criado e actualizar pela última vez.

Selecione **incluir atributos extensible no esquema** se extensible objectos (RFC4512/4.3) são utilizados e ativar esta opção permite que cada atributo a ser utilizado em todos os objetos. Se selecionar esta opção faz com que o esquema muito grandes, pelo que, a menos que o diretório ligado está a utilizar esta funcionalidade recomendação manter a opção desmarcada.

### <a name="global-parameters"></a>Parâmetros globais
Na página parâmetros globais, configure o DN para o registo de alterações dos delta e funcionalidades LDAP adicionais. A página é previamente povoada com as informações fornecidas pelo servidor LDAP.

![Conectividade](./media/active-directory-aadconnectsync-connector-genericldap/globalparameters.png)

A secção superior mostra informações fornecidas pelo servidor de si só, tal como o nome do servidor. A conexão também verifica que os controlos obrigatórios estão presentes na na raiz DSE. Se estes controlos não estiverem listados, é apresentado um aviso. Alguns directórios LDAP não liste a todas as funcionalidades da na raiz DSE e é possível que o conector funciona sem problemas, mesmo se existir um aviso.

As caixas de verificação **suportadas controlos** controlam o comportamento de determinadas operações:

- Com a opção Eliminar árvore selecionada, é eliminada uma hierarquia com uma chamada LDAP. Com a opção Eliminar árvore desmarcada, o conector faz um eliminar recursiva caso seja necessário.
- Com os resultados paginados selecionados, a conexão faz uma paginada importação com o tamanho especificado sobre os passos executar.
- A VLVControl e SortControl é uma alternativa a pagedResultsControl ler dados a partir do directório LDAP.
- Se todas as três opções (pagedResultsControl VLVControl e SortControl) não estão selecionadas a conexão importa todos os objeto numa única operação, poderá falhar se for um diretório de grandes dimensões.
- ShowDeletedControl só é utilizado quando o método de importar Delta é USNChanged.

O registo de alterações DN é o contexto de nomenclatura utilizado pelo registo de alterações delta, por exemplo **cn = changelog**. Este valor tem de ser especificado para possam fazer importar delta.

Segue-se uma lista de registo de alterações dos predefinido DNs:

Diretório | Registo de alterações dos delta
--- | ---
Microsoft AD LDS e AD GC | Detetado automaticamente. USNChanged.
Servidor de diretório Apache | Não está disponível.
Diretório 389 | Registo de alterações. Valor a utilizar predefinido: **cn = changelog**
Tivoli da IBM DS | Registo de alterações. Valor a utilizar predefinido: **cn = changelog**
Diretório Isode | Registo de alterações. Valor a utilizar predefinido: **cn = changelog**
Novell/NetIQ eDirectory | Não está disponível. Carimbo de data/hora. As utilizações de conexão última atualização data/hora para obter adicionada e atualizar registos.
Abrir DJ/DS | Registo de alterações.  Valor a utilizar predefinido: **cn = changelog**
Abrir LDAP | Registo de acesso. Valor a utilizar predefinido: **cn = accesslog**
Oracle DSEE | Registo de alterações. Valor a utilizar predefinido: **cn = changelog**
RadiantOne VDS | Directório virtual. Depende do diretório ligado ao VDS.
DOM um servidor de directório | Registo de alterações. Valor a utilizar predefinido: **cn = changelog**

O atributo de palavra-passe é o nome do atributo que a conexão deve utilizar para definir a palavra-passe em Alterar palavra-passe e operações de definir palavra-passe.
Este valor por predefinição está definido para **userPassword** mas pode ser alterada quando necessário para um sistema LDAP específico.

Na lista de partições adicionais, é possível adicionar espaços adicionais de nomes não são automaticamente detetados. Por exemplo, esta definição pode ser utilizada se vários servidores compõem um cluster de lógico, deverá todos ser importado ao mesmo tempo. Tal como do Active Directory pode ter vários domínios numa floresta mas todos os domínios de partilham um esquema, o mesmo pode simulado introduzindo os espaços de nomes adicionais nesta caixa. Cada espaço de nomes pode importar a partir de diferentes servidores e ainda mais está configurado na página configurar a partições e hierarquias. Utilize Ctrl + Enter para obter uma nova linha.

### <a name="configure-provisioning-hierarchy"></a>Configurar o aprovisionamento de hierarquia
Esta página é utilizada para mapear o componente de DN, por exemplo OU, para o tipo de objecto que deve ser aprovisionado, por exemplo organizationalUnit.

![Hierarquia de aprovisionamento](./media/active-directory-aadconnectsync-connector-genericldap/provisioninghierarchy.png)

Ao configurar a hierarquia de aprovisionamento, pode configurar a conexão para criar automaticamente uma estrutura quando for necessário. Por exemplo, se existir um centro de dados do espaço de nomes = contoso, CC = com e novo objeto cn = João, or = Seattle, c = US, Cc = contoso, Cc = está aprovisionado com, em seguida, a conexão pode criar um objeto de país tipo para EUA e um organizationalUnit para Seattle se aqueles ainda não existirem no diretório.

### <a name="configure-partitions-and-hierarchies"></a>Configurar a partições e hierarquias
Na página a partições e hierarquias, selecione todos os espaços de nomes com objetos que pretenda importar e exportar.

![Partições](./media/active-directory-aadconnectsync-connector-genericldap/partitions.png)

Para cada espaço de nomes, também é possível configurar as definições de conectividade que iria substituam os valores especificados no ecrã de conectividade. Se estes valores são esquerdas para a sua valor em branco do predefinido, são utilizadas as informações no ecrã de conectividade.

Também é possível selecionar quais contentores e ou a conexão deve importação e exportação para.

### <a name="configure-anchors"></a>Configurar as âncoras
Esta página tem sempre um valor pré-configurado e não pode ser alterada. Se tiver sido identificado o fabricante do servidor, em seguida, a âncora poderá conter de um atributo imutáveis, por exemplo o GUID para um objeto. Se este não foi detectado ou conhecido para que não seja um atributo imutáveis, em seguida, o conector utiliza dn (nome distinto) como a âncora.

![âncoras](./media/active-directory-aadconnectsync-connector-genericldap/anchors.png)

Segue-se uma lista de servidores LDAP e âncora a ser utilizado:

Diretório | Atributo de âncora
--- | ---
Microsoft AD LDS e AD GC | GUID de objecto
Servidor de directório 389 | DN
Apache diretório | DN
Tivoli da IBM DS | DN
Diretório Isode | DN
Novell/NetIQ eDirectory | GUID
Abrir DJ/DS | DN
Abrir LDAP | DN
Oracle ODSEE | DN
RadiantOne VDS | DN
DOM um servidor de directório | DN

## <a name="other-notes"></a>Outras notas
Esta secção fornece informações de aspetos que são específicos para este conector ou por outros motivos é importante que conheça.

### <a name="delta-import"></a>Importar delta
A marca d'água delta abrir LDAP é UTC data/hora. Por esta razão, o relógio entre o serviço de sincronização numa e o protocolo LDAP abrir têm de ser sincronizado. Caso contrário, poderão ser omitidas algumas entradas no registo de alterações de variação.

Para Novell eDirectory, a importação de delta não está a detectar elimina qualquer objeto. Por este motivo, é necessário executar uma importação completa periodicamente para encontrar todos os objetos eliminados.

Em directórios com um registo de alterações delta que é baseada no data/hora, recomenda-se vivamente para executar uma importação completa alturas periódicos. Este processo permite que o motor de sincronização para encontrar e dissemelhanças com entre o servidor LDAP e o que é atualmente no espaço de conexão.

## <a name="troubleshooting"></a>Resolução de problemas

-   Para obter informações sobre como ativar o registo resolver a conexão, consulte o artigo [como ativar ETW Tracing para os conectores](http://go.microsoft.com/fwlink/?LinkId=335731).
