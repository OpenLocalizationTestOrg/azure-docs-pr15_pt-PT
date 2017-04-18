<properties
   pageTitle="Descrição geral do controlo de acesso no arquivo de dados de Lake | Microsoft Azure"
   description="Compreender como aceder ao controlo na loja do Azure dados Lake"
   services="data-lake-store"
   documentationCenter=""
   authors="nitinme"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="09/06/2016"
   ms.author="nitinme"/>

# <a name="access-control-in-azure-data-lake-store"></a>Controlo de acesso no arquivo de Lake de dados do Azure

Arquivo de dados de Lake implementa o modelo de controlo do access que deriva da HDFS e, por sua vez, a partir do modelo de controlo de acesso de POSIX. Este artigo resume os princípios básicos para o modelo de controlo de acesso para dados Lake arquivo. Para saber mais sobre o HDFS controlo de acesso modelo consulte o artigo [Guia de permissões HDFS](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html).

## <a name="access-control-lists-on-files-and-folders"></a>Listas de controlo de acesso de ficheiros e pastas

Existem dois tipos de acesso listas de controlo (ACL) - **ACL de acesso** e **ACL predefinido**.

* **Acesso ACL** – estas controlo de acesso a um objeto. Ficheiros e pastas tem ACL de acesso.

* **Predefinido ACL** – um "modelo" de ACL associados a uma pasta que determinam o ACL de acesso para quaisquer itens subordinados criados nessa pasta. Ficheiros não têm ACL predefinido.

![ACL de arquivo de Lake de dados](./media/data-lake-store-access-control/data-lake-store-acls-1.png)

O Access ACL e ACL predefinido de ter a mesma estrutura.

![ACL de arquivo de Lake de dados](./media/data-lake-store-access-control/data-lake-store-acls-2.png)

>[AZURE.NOTE] Alterar a ACL predefinido num elemento principal não afeta o acesso ACL ou predefinido ACL de itens subordinados já existentes.

## <a name="users-and-identities"></a>Utilizadores e identidades

Todos os ficheiros e pastas tem permissões distintas para estas identidades:

* O utilizador proprietário do ficheiro
* O grupo proprietário
* Utilizadores com nome
* Grupos com nome
* Todos os outros utilizadores

As identidades dos utilizadores e grupos são identidades do Azure Active Directory (AAD) por isso, salvo indicação um "utilizador", no contexto Lake de arquivo de dados, poderia quer dizer um utilizador do AAD ou um grupo de segurança AAD.

## <a name="permissions"></a>Permissões

As permissões de um objecto de sistema de ficheiros são **Ler**, **escrever**, e **Executar** e estes podem ser utilizados em ficheiros e pastas, conforme apresentado na tabela abaixo.

|            |    Ficheiro     |   Pasta |
|------------|-------------|----------|
| **Read (R)** | Pode ler os conteúdos de um ficheiro | Necessita de **Ler** e **Executar** listar o conteúdo da pasta.|
| **Escrever (E)** | Pode escrever ou acrescentar a um ficheiro | Necessita de **escrever e executar** criar subordinado itens numa pasta. |
| **Executar (X)** | Não significa nada no contexto Lake de arquivo de dados | Necessário para percorrer os itens de subordinado de uma pasta. |

### <a name="short-forms-for-permissions"></a>Breves formulários de permissões

**RWX**é utilizado para indicar **Ler + escrever + executar**. Existe um formulário de numérico mais comprimido na qual **leitura = 4**, **escrever = 2**, e **executar = 1** e os respetivos soma representa as permissões. Abaixo estão alguns exemplos.

| Formulário numérico | Formulário breve |      O que significa:     |
|--------------|------------|------------------------|
| 7            | RWX        | Ler + escrever + executar |
| 5            | R-X        | Leitura + executar         |
| 4            | R:        | Leitura                   |
| 0            | ---        | Não existem permissões         |


### <a name="permissions-do-not-inherit"></a>Não herdam permissões

No modelo POSIX estilo utilizado pelo arquivo de dados de Lake, as permissões de um item são armazenadas no próprio item. Por outras palavras, permissões de um item não podem ser herdadas dos itens de elemento principal.

## <a name="common-scenarios-related-to-permissions"></a>Cenários comuns relacionados com permissões

Eis alguns cenários comuns para compreender que permissões necessárias para executar determinadas operações de uma conta do arquivo de Lake de dados.

### <a name="permissions-needed-to-read-a-file"></a>Permissões necessárias para ler um ficheiro

![ACL de arquivo de Lake de dados](./media/data-lake-store-access-control/data-lake-store-acls-3.png)

* Para o ficheiro a ser lidos - o autor da chamada necessita de permissões de **leitura**
* Para todas as pastas na estrutura de pastas que contenham o ficheiro - o autor da chamada requer permissões de **execução**

### <a name="permissions-needed-to-append-to-a-file"></a>Permissões necessárias para acrescentar a um ficheiro

![ACL de arquivo de Lake de dados](./media/data-lake-store-access-control/data-lake-store-acls-4.png)

* Para o ficheiro a ser anexado ao - o autor da chamada requer permissões de **escrita**
* Para todas as pastas que contenham o ficheiro - o autor da chamada requer permissões de **execução**

### <a name="permissions-needed-to-delete-a-file"></a>Permissões necessárias para eliminar um ficheiro

![ACL de arquivo de Lake de dados](./media/data-lake-store-access-control/data-lake-store-acls-5.png)

* Necessita de permissões de **escrita + executar** para a pasta principal - o autor da chamada
* Para todas as outras pastas no caminho do ficheiro - o autor da chamada requer permissões de **execução**

>[AZURE.NOTE] Escreva as permissões do ficheiro não é necessária para eliminar o ficheiro desde que as acima duas condições são verdadeiras.

### <a name="permissions-needed-to-enumerate-a-folder"></a>Permissões necessárias para enumerar uma pasta

![ACL de arquivo de Lake de dados](./media/data-lake-store-access-control/data-lake-store-acls-6.png)

* Necessita de permissões de **leitura + executar** para a pasta para enumerar - o autor da chamada
* Para todas as pastas de predecessor - o autor da chamada requer permissões de **execução**

## <a name="viewing-permissions-in-the-azure-portal"></a>Permissões de visualização no portal do Azure

Pá do **Explorador de dados** a conta de arquivo de Lake de dados, clique em **Access** para ver as ACL para um ficheiro ou uma pasta. Na captura de ecrã abaixo, clique em Access para ver as ACL para a pasta de **catálogo** sob a conta **mydatastore** .

![ACL de arquivo de Lake de dados](./media/data-lake-store-access-control/data-lake-store-show-acls-1.png)

Após esta ação, pá **acesso** , clique em **Vista simples** para ver a vista mais simples.

![ACL de arquivo de Lake de dados](./media/data-lake-store-access-control/data-lake-store-show-acls-simple-view.png)

Clique em **Vista avançada** para ver a vista mais avançada.

![ACL de arquivo de Lake de dados](./media/data-lake-store-access-control/data-lake-store-show-acls-advance-view.png)

## <a name="the-super-user"></a>O utilizador super

Um utilizador super tem mais de direitos de todos os utilizadores do arquivo de dados Lake. Um utilizador super:

* tenha permissões de RWX para **todos os** ficheiros e pastas
* Pode alterar as permissões no qualquer ficheiro ou pasta.
* Pode alterar o proprietário de utilizador ou grupo proprietário de qualquer ficheiro ou pasta.

No Azure, uma conta do arquivo de Lake dados tem várias funções Azure:

* Proprietários de
* Contribuintes
* Leitores
* Etc.

Todos os utilizadores na função **proprietários** de uma conta do arquivo de Lake dados são automaticamente um utilizador Super dessa conta. Para saber mais sobre o Azure função com base no Access controlo RBCA () consulte [controlo de acesso baseado em funções](../active-directory/role-based-access-control-configure.md).

## <a name="the-owning-user"></a>O utilizador proprietário

O utilizador que criou o item é automaticamente o utilizador proprietário do item. Um utilizador do proprietário pode:

* Alterar as permissões de um ficheiro que é propriedade
* Altere o grupo proprietário de um ficheiro que pertence, desde que o utilizador proprietário também é um membro do grupo de destino.

>[AZURE.NOTE] O utilizador proprietária **não pode** alterar o proprietário do utilizador de outro ficheiro de propriedade. Apenas os utilizadores Super podem alterar o utilizador proprietário de um ficheiro ou pasta.

## <a name="the-owning-group"></a>O grupo proprietário

Nas ACL POSIX, cada utilizador está associado um grupo"principal". Por exemplo, o utilizador "alice" pode pertencer ao grupo "Finanças". Alice pode pertencer a vários grupos, mas um grupo sempre está designado como o grupo principal. No POSIX, quando Alice cria um ficheiro, o grupo proprietário desse ficheiro está definido para o grupo principal, o que é neste caso "Finanças".
 
Quando é criado um novo item de sistema de ficheiros, o arquivo de dados de Lake atribui um valor para o grupo proprietário. 

* **Caso 1** - a pasta de raiz "/". Esta pasta é criada quando é criada uma conta do arquivo de Lake de dados. Neste caso, o grupo proprietário está definido para o utilizador que criou a conta.
* **Caso 2** (todas as outras caso) - quando é criado um novo item, grupo proprietário é copiado a partir da pasta principal.

O grupo proprietário pode ser alterado pelos:
* Quaisquer utilizadores Super
* O utilizador proprietário, se o utilizador proprietário também é um membro do grupo de destino.

## <a name="access-check-algorithm"></a>Algoritmo de verificação do Access

A ilustração seguinte representa o algoritmo de verificação do access para contas do arquivo de Lake de dados.

![Algoritmo de ACL de arquivo de Lake de dados](./media/data-lake-store-access-control/data-lake-store-acls-algorithm.png)


## <a name="the-mask-and-effective-permissions"></a>A máscara e "permissões"

A **máscara** é um valor RWX que é utilizado para limitar o acesso para **os utilizadores com o nome**, o **proprietário do grupo**e **grupos com o nome** quando efetuar o algoritmo de verificação de acesso. Aqui estão os conceitos chave para a máscara. 

* A máscara cria "permissões", ou seja, modifica as permissões no momento da verificação de acesso.
* A máscara pode ser editada diretamente ao proprietário do ficheiro e quaisquer utilizadores super.
* A máscara tem a capacidade de remover permissões para criar a permissão efectiva. As máscaras de introdução, **não pode** adicionar permissões à permissão efectiva. 

Observe alguns exemplos diga-nos. Abaixo, a máscara está definida para **RWX**, que significa que a máscara não remove todas as permissões. Repare que as permissões eficazes para com nome de utilizador, grupo proprietário e grupo com nome não são alteradas durante a verificação de acesso.

![ACL de arquivo de Lake de dados](./media/data-lake-store-access-control/data-lake-store-acls-mask-1.png)

No exemplo abaixo, a máscara está definida para **R-X**. Por isso,- **desativa a permissão de escrita** para **com nome de utilizador**, **se o grupo**e **denominada grupo** no momento da acesso verificar.

![ACL de arquivo de Lake de dados](./media/data-lake-store-access-control/data-lake-store-acls-mask-2.png)

Para sua referência, eis onde a máscara para um ficheiro ou pasta é apresentada no Portal do Azure.

![ACL de arquivo de Lake de dados](./media/data-lake-store-access-control/data-lake-store-show-acls-mask-view.png)

>[AZURE.NOTE] Para uma nova conta do arquivo de Lake de dados, máscaras de introdução para o Access ACL e ACL predefinido da pasta raiz ("/") são predefinir para RWX.

## <a name="permissions-on-new-files-and-folders"></a>Permissões em novos ficheiros e pastas

Quando um novo ficheiro ou pasta é criada numa pasta existente, determina a ACL predefinido na pasta principal:

* Uma pasta subordinado ACL predefinida e acesso ACL
* Acesso ACL um ficheiro de subordinados (ficheiros não têm uma ACL predefinida)

### <a name="a-child-file-or-folders-access-acl"></a>Um ficheiro de subordinado ou ACL de acesso da pasta

Quando é criada um subordinado ficheiro ou pasta, predefinido ACL o principal é copiada como o ficheiro de subordinado ou ACL de acesso da pasta. Além disso, se a **outro** utilizador tiver permissões de RWX na pasta predefinida o principal ACL, é completamente removida do ACL de acesso do item subordinado.

![ACL de arquivo de Lake de dados](./media/data-lake-store-access-control/data-lake-store-acls-child-items-1.png)

Na maioria dos cenários, as informações acima são tudo o que deve precisa de saber sobre como um item subordinado acesso ACL é determinado. No entanto, se estiver familiarizado com sistemas POSIX e pretender compreender aprofundadas como este transformação é alcançar, consulte a secção [funções da Umask na criação de ACL de acesso para novos ficheiros e pastas](#umasks-role-in-creating-the-access-acl-for-new-files-and-folders) neste artigo.
 

### <a name="a-child-folders-default-acl"></a>ACL predefinido de uma pasta de subordinados

Quando é criada uma pasta de subordinadas sob uma pasta principal, predefinido ACL a pasta principal é copiada sobre, tal como está, para predefinido ACL a pasta subordinado.

![ACL de arquivo de Lake de dados](./media/data-lake-store-access-control/data-lake-store-acls-child-items-2.png)

## <a name="advanced-topics-for-understanding-acls-in-data-lake-store"></a>Tópicos avançados para compreender ACL no arquivo de dados de Lake

Seguem-se apenas alguns tópicos avançados para ajudar a compreender como ACL são determinadas dados Lake armazenar ficheiros ou pastas.

### <a name="umasks-role-in-creating-the-access-acl-for-new-files-and-folders"></a>Função do Umask na criação de ACL de acesso para novos ficheiros e pastas

Num sistema compatíveis com POSIX, o conceito geral é que essa umask for um valor de 9-bit na pasta principal utilizado para transformar a permissão para o **utilizador proprietário**, **se o grupo**e **outros** num novo ficheiro subordinado ou ACL de acesso da pasta. Os bits de uma umask identificam quais os bits para desativar o ACL de acesso do item subordinado. Assim é utilizado para seletivamente impedir a propagação das permissões de utilizador, grupo, que detém proprietário e outros.
  
Num sistema HDFS, o umask é normalmente uma opção de configuração de todo o site que é controlada por administradores. Arquivo de dados de Lake utiliza uma **umask toda a conta** que não pode ser alterado. A tabela seguinte mostra umask do arquivo de dados Lake.

| Grupo de utilizadores  | Definição | Efeito no novo item de subordinados acesso ACL |
|------------ |---------|---------------------------------------|
| Se o utilizador | ---     | Não terá efeito                             |
| Proprietário de grupo| ---     | Não terá efeito                             |
| Outros       | RWX     | Remover leitura + escrever + executar         | 

A ilustração seguinte mostra este umask em ação. O efeito líquido consiste em remover **Ler + escrever + executar** para **outro** utilizador. Uma vez que o umask não especificado bits para **se o utilizador** e o **proprietário do grupo**, essas permissões não são transformados.

![ACL de arquivo de Lake de dados](./media/data-lake-store-access-control/data-lake-store-acls-umask.png) 

### <a name="the-sticky-bit"></a>O bit autocolante

O bit autocolante é uma funcionalidade de um sistema de ficheiros POSIX mais avançada. O contexto for Lake de arquivo de dados, não é provável que será necessário, o bit autocolante.

A tabela abaixo mostra como o bit autocolante funciona no arquivo de dados de Lake.

| Grupo de utilizadores         | Ficheiro    | Pasta |
|--------------------|---------|-------------------------|
| Autocolante bit **desligado** | Não terá efeito   | Não terá efeito           |
| Autocolante bit **ON**  | Não terá efeito   | Impede que qualquer pessoa exceto **os utilizadores Super** e o **utilizador proprietário** de um item de subordinados a partir de eliminar ou mudar o nome desse item subordinado.               |

O bit autocolante não é apresentado no Portal do Azure.

## <a name="common-questions-for-acls-in-data-lake-store"></a>Perguntas comuns para ACL no arquivo de dados de Lake

Aqui estão algumas questões que surgem com frequência relativamente às ACL no arquivo de dados de Lake.

### <a name="do-i-have-to-enable-support-for-acls"></a>Tenho de ter que activar o suporte para ACL?

Não. Controlo de acesso através de ACL sempre está ligada para uma conta do arquivo de Lake de dados.

### <a name="what-permissions-are-required-to-recursively-delete-a-folder-and-its-contents"></a>Que permissões são necessários para o modo recursivo eliminar uma pasta e os seus conteúdos?

* Na pasta de origem tem de ter **escrita + executar**.
* A pasta a ser eliminada e todas as pastas dentro da mesma, necessita de **Ler + escrever + executar**.
>[AZURE.NOTE] Eliminar os ficheiros em pastas não requer escrita nesses ficheiros. Além disso, a pasta de raiz "/" **nunca** podem ser eliminados.

### <a name="who-is-set-as-the-owner-of-a-file-or-folder"></a>Quem está definido como proprietário de um ficheiro ou pasta?

O criador de blocos de um ficheiro ou pasta torna-se o proprietário.

### <a name="who-is-set-as-the-owning-group-of-a-file-or-folder-at-creation"></a>Quem é definida como grupo proprietário de um ficheiro ou pasta criação?

É copiada a partir do grupo proprietário da pasta principal sob o qual o novo ficheiro ou pasta é criada.

### <a name="i-am-the-owning-user-of-a-file-but-i-dont-have-the-rwx-permissions-i-need-what-do-i-do"></a>Sou o proprietário de utilizador de um ficheiro mas eu não possuir as permissões de RWX que é necessário. O que posso fazer?

O utilizador proprietário simplesmente pode alterar as permissões do ficheiro para atribuir a próprios qualquer permissões de RWX que necessitam.

### <a name="does-data-lake-store-support-inheritance-of-acls"></a>Arquivo de dados de Lake suporta a herança de ACL?

Não.

### <a name="what-is-the-difference-between-mask-and-umask"></a>O que é a diferença entre máscaras de introdução e umask?

| máscaras de introdução | umask|
|------|------|
| A propriedade de **máscaras de introdução** está disponível em todos os ficheiros e pastas. | O **umask** é uma propriedade da conta de arquivo de Lake de dados. Sim, não existe apenas uma única umask do arquivo de dados Lake.    |
| A propriedade de máscaras de introdução de um ficheiro ou pasta pode ser alterada pelo proprietário do utilizador ou grupo proprietário de um ficheiro ou um utilizador super. | Não é possível modificar a propriedade umask por qualquer utilizador, mesmo um utilizador super. É um valor imutável, constante.|
| A propriedade de máscaras de introdução é utilizada para durante o algoritmo de verificação de acesso de tempo de execução para determinar se um utilizador tem à direita para desempenhar em operação um ficheiro ou pasta. A função da máscara é criar "permissões" no momento da verificação de acesso. | O umask não é utilizado durante a verificação de acesso de todo. O umask é utilizada para determinar a acesso ACL de itens subordinados novo de uma pasta. |
| A máscara é um valor RWX de 3-bit que aplica-se com nome de utilizador, grupo com nome e utilizador proprietária no momento da verificação de acesso.| O umask é um valor 9 bit que se aplica ao proprietário de utilizador, grupo proprietário e outros subordinado novo.| 

### <a name="where-can-i-learn-more-about-posix-access-control-model"></a>Onde posso saber mais sobre o modelo de controlo de acesso POSIX?

* [http://www.vanemery.com/Linux/ACL/POSIX_ACL_on_Linux.HTML](http://www.vanemery.com/Linux/ACL/POSIX_ACL_on_Linux.html)

* [Guia de permissão HDFS](http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html) 

* [PERGUNTAS MAIS FREQUENTES DO POSIX](http://www.opengroup.org/austin/papers/posix_faq.html)

* [POSIX 1003.1 2008](http://standards.ieee.org/findstds/standard/1003.1-2008.html)

* [POSIX 1003.1e 1997](http://users.suse.com/~agruen/acl/posix/Posix_1003.1e-990310.pdf)

* [ACL de POSIX no Linux](http://users.suse.com/~agruen/acl/linux-acls/online/)

* [Utilizar listas de controlo de acesso no Linux ACL](http://bencane.com/2012/05/27/acl-using-access-control-lists-on-linux/)

## <a name="see-also"></a>Consulte também

* [Descrição geral do Azure Lake de arquivo de dados](data-lake-store-overview.md)

* [Introdução ao Azure dados Lake Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md)





