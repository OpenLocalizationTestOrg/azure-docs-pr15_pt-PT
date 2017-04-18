<properties
   pageTitle="Conexão do SQL genérico | Microsoft Azure"
   description="Este artigo descreve como configurar o conector de SQL genérico da Microsoft."
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

# <a name="generic-sql-connector-technical-reference"></a>Genérica referência técnica do conector de SQL

Este artigo descreve o conector de SQL genérico. O artigo aplica-se aos seguintes produtos:

- Gestor de identidades do Microsoft 2016 (MIM2016)
- Gestor de identidades do Forefront 2010 R2 (FIM2010R2)
    -   Tem de utilizar correcção 4.1.3671.0 ou posterior [KB3092178](https://support.microsoft.com/kb/3092178).

Para MIM2016 e FIM2010R2, o conector está disponível como uma transferência a partir do [Centro de transferências da Microsoft](http://go.microsoft.com/fwlink/?LinkId=717495).

Para ver este conector em ação, consulte o artigo [Genérico de SQL de conexão, passo a passo](active-directory-aadconnectsync-connector-genericsql-step-by-step.md) .

## <a name="overview-of-the-generic-sql-connector"></a>Descrição geral da conexão genérico de SQL

O conector de SQL genérico permite-lhe integrar o serviço de sincronização com um sistema de base de dados que oferece conectividade ODBC.  

A partir de uma perspetiva de alto nível, as seguintes funcionalidades são suportadas pelo versão atual do conector:

Funcionalidade | Suporte
--- | ---
Origem de dados ligada | A conexão é suportada com todos os controladores ODBC de 64 bits. Foi testado com o seguinte: <li>Microsoft SQL Server e do SQL Azure</li><li>IBM DB2 10. x</li><li>IBM DB2 9</li><li>G Oracle 10 e 11</li><li>MySQL 5. x</li>
Cenários   | <li>Gestão de ciclo de vida do objeto</li><li>Gestão de palavra-passe</li>
Operações | <li>Importar completo e Delta importar, exportar</li><li>Para exportar: Adicionar, eliminar, atualização e substituir</li><li>Definir palavra-passe, alterar palavra-passe</li>
Esquema | <li>Deteção dinâmica dos atributos e objetos</li>

### <a name="prerequisites"></a>Pré-requisitos
Antes de utilizar a conexão, certifique-se de que tem o seguinte procedimento no servidor de sincronização:

- 4.5.2 do Microsoft .NET Framework ou posterior
- controladores de cliente do ODBC de 64 bits

### <a name="permissions-in-connected-data-source"></a>Permissões na origem de dados ligada
Para criar ou executar qualquer uma das tarefas suportadas numa conexão genérico de SQL, tem de ter:

- db_datareader
- db_datawriter

### <a name="ports-and-protocols"></a>Portas e protocolos
Para as portas necessárias para o controlador de ODBC trabalhar, consulte a documentação do fornecedor de base de dados.

## <a name="create-a-new-connector"></a>Criar uma nova conexão
Para criar uma conexão genérico de SQL, num **Serviço de sincronização** , selecione **Agente de gestão** e de **Criar**. Selecione a conexão **SQL genérico (Microsoft)** .

![CreateConnector](./media/active-directory-aadconnectsync-connector-genericsql/createconnector.png)

### <a name="connectivity"></a>Conectividade
A conexão utiliza um ficheiro de ODBC DSN de conectividade. Crie o ficheiro DSN utilizando **Origens de dados ODBC** , que se encontram no menu Iniciar, em **Ferramentas administrativas**. Na ferramenta de administrativa, crie um **Ficheiro DSN** para que pode ser fornecido ao conector.

![CreateConnector](./media/active-directory-aadconnectsync-connector-genericsql/connectivity.png)

O ecrã de conectividade é o primeiro quando cria uma nova genérico conexão de SQL. Primeiro terá de fornecer as seguintes informações:

- Caminho do ficheiro DSN
- Autenticação
    - Nome de utilizador
    - Palavra-passe

A base de dados deve suportar um dos seguintes métodos de autenticação:

- **Autenticação do Windows**: A base de dados de autenticação utiliza as credenciais do Windows para verificar o utilizador. O utilizador nome/palavra-passe especificada é utilizada para autenticar com a base de dados. Esta conta tem permissões para a base de dados.
- **Autenticação do SQL**: A autenticação utiliza de base de dados o nome de utilizador/palavra-passe definidos um ecrã conectividade para ligar à base de dados. Se armazenar o nome de utilizador/palavra-passe no ficheiro DSN, as credenciais fornecidas no ecrã de conectividade têm prioridade.
- **Autenticação de base de dados do SQL Azure**: para obter mais informações, consulte o artigo [ligar ao SQL da base de dados ao utilizar o Azure autenticação do Active Directory](..\sql-database\sql-database-aad-authentication.md).

**DN é âncora**: Se selecionar esta opção, o DN também é utilizado como o atributo âncora. -Pode ser utilizada para uma implementação simple, mas também tem as seguintes limitações:

-   Conector suporta apenas um tipo de objecto. Por conseguinte, quaisquer atributos de referência só podem referenciar o mesmo tipo de objeto.

**Tipo de exportação: objeto substituir**: durante a exportação, quando tem alterado apenas alguns atributos, o objeto inteiro com todos os atributos é exportado e substitui o objeto existente.

### <a name="schema-1-detect-object-types"></a>Esquema 1 (tipos de objeto de detetar)
Nesta página, passar para configurar o modo como o conector vai encontrar os tipos de outro objeto da base de dados.

Cada tipo de objeto é apresentado como uma partição e configurado mais sobre **Configurar a partições e hierarquias**.

![schema1a](./media/active-directory-aadconnectsync-connector-genericsql/schema1a.png)

**Método de detecção do tipo de objeto**: O conector suporta estes métodos de deteção de tipo de objeto.

- **Valor fixo**: forneça a lista de tipos de objecto com uma lista separados por vírgulas. Por exemplo: `User,Group,Department`.  
![schema1b](./media/active-directory-aadconnectsync-connector-genericsql/schema1b.png)
- **Procedimento vista/Tabela/armazenado**: fornecer o nome do procedimento tabela/vista/armazenada e, em seguida, o nome da coluna que fornece a lista de tipos de objeto. Se utilizar um procedimento armazenado, em seguida, também fornecem parâmetros para o mesmo no formato **[nome]: [direção]: [valor]**. Fornece cada parâmetro numa linha separada (utilize Ctrl + Enter para começar uma nova linha).  
![schema1c](./media/active-directory-aadconnectsync-connector-genericsql/schema1c.png)
- **Consulta SQL**: esta opção permite-lhe fornecer uma consulta SQL que devolve uma única coluna com os tipos de objeto, por exemplo `SELECT [Column Name] FROM TABLENAME`. A coluna devolvida tem de ser do tipo de cadeia (varchar).

### <a name="schema-2-detect-attribute-types"></a>Esquema 2 (tipos de atributo detetar)
Nesta página, passar para configurar a forma como os nomes dos atributos e tipos de irão ser detetados. As opções de configuração são listadas para cada tipo de objeto detetado na página anterior.

![schema2a](./media/active-directory-aadconnectsync-connector-genericsql/schema2a.png)

**Método de detecção do tipo de atributo**: O conector suporta estes métodos de deteção de tipo de atributo com cada tipo de objecto detectado no ecrã 1 de esquema.

- **Procedimento vista/Tabela/armazenado**: fornecer o nome do procedimento tabela/vista/armazenada que deve ser utilizado para localizar os nomes de atributo. Se utilizar um procedimento armazenado, em seguida, também fornecem parâmetros para o mesmo no formato **[nome]: [direção]: [valor]**. Fornece cada parâmetro numa linha separada (utilize Ctrl + Enter para começar uma nova linha). Para detetar os nomes de atributo num atributo de múltiplos valor, forneça uma lista separados por vírgulas das tabelas ou vistas. Cenários de valores múltiplos não são suportados quando o tabela principal e subordinado têm os mesmos nomes de coluna.
- **Consulta SQL**: esta opção permite-lhe fornecer uma consulta SQL que devolve uma única coluna com nomes de atributo, por exemplo `SELECT [Column Name] FROM TABLENAME`. A coluna devolvida tem de ser do tipo de cadeia (varchar).

### <a name="schema-3-define-anchor-and-dn"></a>Esquema 3 (definir âncora e DN)
Esta página permite-lhe configurar âncora e atributo DN para cada tipo de objeto detectado. Pode selecionar vários atributo para tornar a âncora exclusivo.

![schema3a](./media/active-directory-aadconnectsync-connector-genericsql/schema3a.png)

- Atributos de múltiplos valores e booleanos não estão listados.
- Mesmo atributo não pode utilizar para DN e âncora, a menos que **DN é âncora** está selecionada na página conectividade.
- Se **DN é âncora** estiver selecionada na página conectividade, esta página requer apenas o atributo DN. Este atributo seria também ser utilizado como o atributo âncora.
![schema3b](./media/active-directory-aadconnectsync-connector-genericsql/schema3b.png)

### <a name="schema-4-define-attribute-type-reference-and-direction"></a>Esquema de 4 (Definir tipo de atributo, referência e direção)
Esta página permite-lhe configurar o tipo de atributo, como o número inteiro, binário, ou Booleano e direção para cada atributo. Todos os atributos de página **esquema 2** estão listados incluindo atributos de múltiplos valores.

![schema4a](./media/active-directory-aadconnectsync-connector-genericsql/schema4a.png)

- **Tipo de dados**: utilizado para mapear o tipo de atributo aos tipos de conhecido pelo motor de sincronização. A predefinição é utilizar o mesmo tipo como detetado no esquema de SQL, mas não estão facilmente detectado DateTime e de referência. Para aqueles, tem de especificar **DateTime** ou **referência**.
- **Direcção**: pode definir a direção de atributo para importar, exportar ou ImportExport. ImportExport é predefinição.
![schema4b](./media/active-directory-aadconnectsync-connector-genericsql/schema4b.png)

Notas:

- Se não for detectável pelo conector de um tipo de atributo, utiliza o tipo de dados cadeia.
- **Tabelas aninhadas** pode ser considerado tabelas de base de dados de uma coluna. Oracle armazena as linhas de uma tabela aninhada nenhuma ordem particular. No entanto, quando obtém a tabela aninhada para uma variável de PL/SQL, as linhas são dadas índices consecutivas, começando na 1. Que dá-lhe acesso matriz semelhante a linhas individuais.
- **VARRYS** não são suportadas no connector.

### <a name="schema-5-define-partition-for-reference-attributes"></a>Esquema de 5 (Definir partição para os atributos de referência)
Nesta página, configure para todos os atributos de referência que partição (tipo de objeto) um atributo é que fazem referência a.

![schema5](./media/active-directory-aadconnectsync-connector-genericsql/schema5.png)

Se utiliza o **DN é âncora**, em seguida, tem de utilizar o mesmo tipo de objeto como a que se estiver a referir. Não pode referenciar outro tipo de objeto.

### <a name="global-parameters"></a>Parâmetros globais
A página de parâmetros globais é utilizada para configurar o método de palavra-passe, formato de data/hora e Delta importar.

![globalparameters1](./media/active-directory-aadconnectsync-connector-genericsql/globalparameters1.png)

O conector de SQL genérico suporta os seguintes métodos para importação Delta:

- **Acionar**: consulte o artigo [gerar vistas Delta utilizar accionadores](https://technet.microsoft.com/library/cc708665.aspx).
- **Marca d'água**: uma abordagem genérica que pode ser utilizada com quaisquer bases de dados. A consulta de marca d'água é previamente povoada com base no fornecedor de base de dados. Uma coluna de marca d'água deve estar presente no cada tabela/vista utilizada. Esta coluna tem de registar inserções e atualizações para as tabelas como e respetivo dependentes (múltiplos valores ou subordinado) tabelas. Relógio entre o serviço de sincronização e o servidor de base de dados têm de ser sincronizado. Caso contrário, poderão ser omitidas algumas entradas na importação de variação.  
Limitação:
    - Estratégia de marca d'água não não suporte eliminado objetos.
- **Instantâneo**: (funciona apenas com o Microsoft SQL Server) [Gerar Delta vistas Utilização de instantâneos](https://technet.microsoft.com/library/cc720640.aspx)
- **Registo de alterações**: (funciona apenas com o Microsoft SQL Server) [sobre o registo de alterações](https://msdn.microsoft.com/library/bb933875.aspx)  
Limitações:
    - Âncora & atributo DN tem de ser parte da chave primária para o objeto selecionado na tabela.
    - Consulta SQL não é suportada durante a importação e exportação com registo de alterações.

**Parâmetros adicionais**: especificar o fuso horário do servidor de base de dados que indica onde se encontra o seu servidor de base de dados. Este valor é utilizada para os vários formatos de data e hora atributos de suporte.

A conexão sempre armazena a data e hora de data no formato de UTC. Possam corretamente converter a data e horas, o fuso horário do servidor da base de dados e o formato utilizado tem de ser especificado. O formato deve ser expressa em formato .net.

Durante a exportação todos os atributos de tempo de data tem de ser fornecido para a conexão no formato de hora UTC.

![globalparameters2](./media/active-directory-aadconnectsync-connector-genericsql/globalparameters2.png)

**Configuração de palavra-passe**: O conector fornece capacidades de sincronização de palavra-passe e suporta defina e alterar palavra-passe.

A conexão fornece dois métodos para suportar a sincronização de palavra-passe:

- **Procedimento armazenado**: Este método requer dois procedimentos armazenados para suportar Definir & Alterar palavra-passe. Escreva todos os parâmetros para adicionar e alterar a palavra-passe operação em **Definir SP de palavra-passe** e os parâmetros de **Alterar SP de palavra-passe** respetivamente como por exemplo abaixo.
![globalparameters3](./media/active-directory-aadconnectsync-connector-genericsql/globalparameters3.png)
- **Extensão de palavra-passe**: Este método requer a extensão de palavra-passe DLL (ter para fornecer o nome do DLL de extensão que está a implementar a interface de [IMAExtensible2Password](https://msdn.microsoft.com/library/microsoft.metadirectoryservices.imaextensible2password.aspx) ). Assemblagem de extensão de palavra-passe deve ser colocada na pasta de extensão, para que a conexão pode carregar a DLL o tempo de execução.
![globalparameters4](./media/active-directory-aadconnectsync-connector-genericsql/globalparameters4.png)

Também tem de ativar a gestão de palavra-passe na página **Configurar extensão** .
![globalparameters5](./media/active-directory-aadconnectsync-connector-genericsql/globalparameters5.png)

### <a name="configure-partitions-and-hierarchies"></a>Configurar a partições e hierarquias
Na página a partições e hierarquias, selecione todos os tipos de objeto. Cada tipo de objecto é a sua própria partição.

![partitions1](./media/active-directory-aadconnectsync-connector-genericsql/partitions1.png)

Também pode substituir os valores definidos na página da **conectividade** ou **Parâmetros globais** .

![partitions2](./media/active-directory-aadconnectsync-connector-genericsql/partitions2.png)

### <a name="configure-anchors"></a>Configurar as âncoras
Esta página é só de leitura, uma vez que já tenha sido definida a âncora. O atributo âncora selecionado é anexado sempre com o tipo de objeto para assegurar permanece exclusivo através de tipos de objeto.

![âncoras](./media/active-directory-aadconnectsync-connector-genericsql/anchors.png)

## <a name="configure-run-step-parameter"></a>Configurar passo executar parâmetro
Estes passos são configurados nos perfis de executar o Connector. Estas configurações faça o trabalho real de importação e exportação de dados.

### <a name="full-and-delta-import"></a>Completo e importar Delta
Genérico suporte de conector de SQL completo e importar Delta através destes métodos:

- Tabela
- Vista
- Procedimento armazenado
- Consulta SQL

![runstep1](./media/active-directory-aadconnectsync-connector-genericsql/runstep1.png)

**Vista de tabela /**  
Para importar os atributos de múltiplos valores para um objeto, tem de fornecer o nome da tabela/vista separados por vírgulas no **nome do múltiplo/vistas de tabela** e condições de associação respetivos na **condição de associação** com a tabela principal.

Exemplo: Que pretende importar o objeto do funcionário e todos os atributos múltiplos valores. Existem duas tabelas, com o nome colaborador (tabela principal) e departamento (múltiplos valor).
Faça o seguinte:

- Tipo **Empregados** na **Vista/Tabela/SP**.
- Escreva departamento no **nome do múltiplo/vistas de tabela**.
- Escreva a condição da associação entre funcionários & departamento na **Condição de associação**, por exemplo `Employee.DEPTID=Department.DepartmentID`.
![runstep2](./media/active-directory-aadconnectsync-connector-genericsql/runstep2.png)

**Procedimentos armazenados**  
![runstep3](./media/active-directory-aadconnectsync-connector-genericsql/runstep3.png)

- Se tiver uma quantidade de dados, recomenda-se para implementar a paginação com os procedimentos armazenados.
- Para obter o procedimento armazenado suportar a paginação, tem de fornecer iniciar o índice e índice de fim. Consulte o artigo: De [forma eficaz de paginação através de grandes quantidades de dados](https://msdn.microsoft.com/library/bb445504.aspx).
- @StartIndexe @EndIndex são substituídos ao tempo de execução com o valor de tamanho de página respetivos configurado no **Passo de configurar** página. Por exemplo, quando o conector obtém a primeira página e o tamanho da página está definido 500, dessa situação @StartIndex seria 1 e @EndIndex 500. Estes valores aumentam quando obtém conexão páginas subsequentes e altere o @StartIndex & @EndIndex valor.
- Para executar o procedimento armazenado parametrizadas, fornecer os parâmetros na `[Name]:[Direction]:[Value]` formatar. Introduza cada parâmetro numa linha separada (utilizar Ctrl + Enter para obter uma nova linha).
- Conexão do SQL genérico também suporta a operação de importação de servidores ligados no Microsoft SQL Server. Se informações devem ser recuperadas de uma tabela no servidor ligados, tabela deve ser apresentada no formato:`[ServerName].[Database].[Schema].[TableName]`
- Conexão do SQL genérico suporta apenas esses objetos que tenham semelhante estrutura (tanto alias nome e tipo de dados) entre executar os passos de deteção de informações e o esquema. Se o objeto selecionado a partir do esquema e informações fornecidas no passo executar for diferente, conexão de SQL não consegue suportar este tipo de cenários.

**Consulta SQL**  
![runstep4](./media/active-directory-aadconnectsync-connector-genericsql/runstep4.png)

![runstep5](./media/active-directory-aadconnectsync-connector-genericsql/runstep5.png)

- Os conjunto de resultados de várias consultas não suportadas.
- Consulta SQL suporta a paginação e forneça iniciar índice e índice de fim como uma variável para suportar a paginação.

### <a name="delta-import"></a>Importar delta
![runstep6](./media/active-directory-aadconnectsync-connector-genericsql/runstep6.png)

Importar a configuração delta requer alguns mais configuração em comparação com importação completa.

- Se escolher a abordagem accionador ou instantâneo para registar alterações delta, em seguida, forneça base de dados de tabela histórico ou instantâneo na caixa **nome da base de dados de instantâneo ou tabela de histórico** .
- Também precisa fornecer a condição de associação entre tabela Histórico e tabela principal, por exemplo`Employee.ID=History.EmployeeID`
- Para controlar a transação na tabela principal a partir da tabela Histórico, tem de fornecer o nome da coluna que contém as informações de operação (adicionar/Update/Delete).
- Se optar por marca d'água para registar alterações delta, em seguida, fornece o nome da coluna que contém as informações de operação no **Nome da coluna de marca de água**.
- A coluna **Alterar tipo atributo** é necessária para o tipo de alteração. Esta coluna mapas uma alteração que ocorre na tabela primária ou tabela de valor múltiplos a um tipo de alteração na vista de variação. Esta coluna pode conter o tipo de alteração de Modify_Attribute para alterar nível do atributo ou uma adicionar, modificar ou eliminar alterar tipo para um tipo de alteração de nível do objeto. Se for algo que não seja o valor predefinido adicionar, modificar ou eliminar, em seguida, que pode definir esses valores ao utilizar esta opção.

### <a name="export"></a>Exportar
![runstep7](./media/active-directory-aadconnectsync-connector-genericsql/runstep7.png)

Suporte de conector de SQL genérico exportar com quatro métodos suportados tal como:

- Tabela
- Vista
- Procedimento armazenado
- Consulta SQL

**Vista de tabela /**  
Se escolher a opção/vista de tabela, em seguida, o conector gera as respetivas consultas para a exportação.

**Procedimentos armazenados**  
![runstep8](./media/active-directory-aadconnectsync-connector-genericsql/runstep8.png)

Se escolher a opção de procedimento armazenado, exportar requer que três procedimentos armazenados diferentes para efetuar operações de inserir/actualizar/eliminar.

- **Adicionar nome SP**: SP esta é executada se qualquer objeto chega à connector para inserção na tabela respetivas.
- **Atualizar o nome do SP**: SP esta é executada se qualquer objeto chega à connector para atualizar a tabela respetivos.
- **Eliminar nome SP**: SP esta é executada se qualquer objeto chega à connector para eliminação na tabela respetivas.
- Atributo selecionado a partir do esquema de utilizado como um valor de parâmetro para o procedimento armazenado. Por exemplo, `EmployeeName: INPUT: @EmployeeName` (EmployeeName está selecionada no esquema de conexão e a conexão substitui o respectivo valor enquanto segue exportar)
- Para executar o procedimento armazenado parametrizado, fornecer parâmetros no `[Name]:[Direction]:[Value]` formatar. Introduza cada parâmetro numa linha separada (utilizar Ctrl + Enter para obter uma nova linha).

**Consulta SQL**  
![runstep9](./media/active-directory-aadconnectsync-connector-genericsql/runstep9.png)

Se escolher a opção de consulta SQL, exportar requer que três consultas diferentes para efetuar operações de inserir/actualizar/eliminar.

- **Inserir consulta**: esta consulta é executada se qualquer objeto chega à connector para inserção na tabela respetivas.
- **Consulta atualizar**: esta consulta é executada se qualquer objeto chega à connector para update na tabela respetivas.
- **Consulta Eliminar**: esta consulta é executada se qualquer objeto chega à connector para eliminação na tabela respetivas.
- Atributo selecionado a partir do esquema de utilizado como um valor de parâmetro à consulta, por exemplo`Insert into Employee (ID, Name) Values (@ID, @EmployeeName)`

## <a name="troubleshooting"></a>Resolução de problemas

-   Para obter informações sobre como ativar o registo resolver a conexão, consulte o artigo [como ativar ETW Tracing para os conectores](http://go.microsoft.com/fwlink/?LinkId=335731).
