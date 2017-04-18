<properties
    pageTitle="Como utilizar o armazenamento de tabela do Azure a partir do Rubi | Microsoft Azure"
    description="Armazene dados estruturados na nuvem através do armazenamento de tabela do Azure, um arquivo de dados NoSQL."
    services="storage"
    documentationCenter="ruby"
    authors="tamram"
    manager="carmonm"
    editor=""/>
<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="ruby"
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="tamram"/>


# <a name="how-to-use-azure-table-storage-from-ruby"></a>Como utilizar o armazenamento de tabela do Azure a partir do Rubi

[AZURE.INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
<br/>
[AZURE.INCLUDE [storage-try-azure-tools-queues](../../includes/storage-try-azure-tools-tables.md)]

## <a name="overview"></a>Descrição geral

Este guia mostra-lhe como efetuar cenários comuns utilizando o serviço de tabela do Azure. Os exemplos estão escritos com a API rubi. Os cenários abrangidos incluem **criar e eliminar uma tabela, inserir e consultar entidades numa tabela**.

[AZURE.INCLUDE [storage-table-concepts-include](../../includes/storage-table-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## <a name="create-a-ruby-application"></a>Criar uma aplicação Rubi

Para obter instruções sobre como criar uma aplicação Rubi, consulte o artigo [Rubi na aplicação Web do carris numa VM Azure](../virtual-machines/linux/classic/virtual-machines-linux-classic-ruby-rails-web-app.md).


## <a name="configure-your-application-to-access-storage"></a>Configurar a aplicação para aceder ao armazenamento

Para utilizar o armazenamento do Windows Azure, é necessário transferir e utilizar o pacote de azure Rubi que inclui um conjunto de bibliotecas de conveniência que comunicar com os serviços de armazenamento RESTANTE.

### <a name="use-rubygems-to-obtain-the-package"></a>Utilizar RubyGems para obter o pacote

1. Utilize uma interface da linha de comandos como **PowerShell** (Windows), **Terminal** (Mac) ou **festa** (Unix).

2. Na janela de comandos para instalar o gem e dependências, escreva a **gem instalar azure** .

### <a name="import-the-package"></a>Importar o pacote

Utilizar o editor de texto Favoritos, adicione o seguinte para a parte superior do ficheiro Rubi onde tenciona utilizar o armazenamento:

    require "azure"

## <a name="set-up-an-azure-storage-connection"></a>Configurar uma ligação de armazenamento do Windows Azure

O módulo azure vai ler as variáveis de ambiente **AZURE\_armazenamento\_conta** e **AZURE\_armazenamento\_acesso\_chave** para as informações necessárias para ligar à sua conta de armazenamento do Windows Azure. Se não estiver definidos estas variáveis de ambiente, tem de especificar as informações da conta antes de utilizar **Azure::TableService** com o seguinte código:

    Azure.config.storage_account_name = "<your azure storage account>"
    Azure.config.storage_access_key = "<your azure storage access key>"

Para obter estes valores a partir de um classic ou conta de armazenamento do Gestor de recursos no Portal do Azure:

1. Inicie a sessão [Portal do Azure](https://portal.azure.com).
2. Navegue para a conta de armazenamento que pretende utilizar.
3. Na pá definições no lado direito, clique em **Teclas de acesso**.
4. O pá de teclas de acesso que é apresentada, verá a tecla de acesso 1 e 2 da chave de acesso. Pode utilizar qualquer um destes. 
5. Clique no ícone de copiar para copiar a chave para a área de transferência. 

Para obter estes valores a partir de uma conta de armazenamento clássica no portal do Azure clássico:

1. Inicie a sessão [portal do Azure clássica](https://manage.windowsazure.com).
2. Navegue para a conta de armazenamento que pretende utilizar.
3. Clique em **GERIR as teclas de acesso** na parte inferior do painel de navegação.
4. De pop-up a caixa de diálogo, verá o nome da conta de armazenamento, a tecla de acesso principal e a chave de acesso secundário. Chave do access, pode utilizar principal ou no gráfico secundário. 
5. Clique no ícone de copiar para copiar a chave para a área de transferência.

## <a name="create-a-table"></a>Criar uma tabela

O objeto **Azure::TableService** permite-lhe trabalhar com tabelas e entidades. Para criar uma tabela, utilize o **criar\_table()** método. O exemplo seguinte cria uma tabela ou imprimir saída o erro se existir um.

    azure_table_service = Azure::TableService.new
    begin
      azure_table_service.create_table("testtable")
    rescue
      puts $!
    end

## <a name="add-an-entity-to-a-table"></a>Adicionar uma entidade a uma tabela

Para adicionar uma entidade, crie primeiro um objecto hash que define as propriedades de entidade. Tenha em atenção que para cada entidade tem de especificar uma **PartitionKey** e **RowKey**. Estes são os identificadores exclusivos das entidades e são valores que podem ser consultados muito for mais rápido outras propriedades do seu. Armazenamento Azure utiliza **PartitionKey** para distribuir automaticamente entidades a tabela ao longo do demasiados nós de armazenamento. Entidades com o mesmo **PartitionKey** são armazenadas no mesmo nó. O **RowKey** é o ID exclusivo da entidade dentro da partição que pertence.

    entity = { "content" => "test entity",
      :PartitionKey => "test-partition-key", :RowKey => "1" }
    azure_table_service.insert_entity("testtable", entity)

## <a name="update-an-entity"></a>Atualizar uma entidade

Existem vários métodos disponíveis para atualizar uma entidade existente:

* **Atualizar\_entity():** Atualize uma entidade existente ao substituí-la.
* **em série\_entity():** Atualiza uma entidade existente ao intercalar novos valores de propriedade na entidade existente.
* **Inserir\_ou\_em série\_entity():** Atualiza uma entidade existente ao substituí-la. Se não existe uma entidade não existir, será inserida uma nova:
* **Inserir\_ou\_substituir\_entity():** Atualiza uma entidade existente ao intercalar novos valores de propriedade na entidade existente. Se não existe uma entidade não existir, será inserida uma nova.

O exemplo seguinte demonstra atualizar uma entidade utilizando **Atualizar\_entity()**:

    entity = { "content" => "test entity with updated content",
      :PartitionKey => "test-partition-key", :RowKey => "1" }
    azure_table_service.update_entity("testtable", entity)

Com **Atualizar\_entity()** e **em série\_entity()**, se a entidade que está a atualizar não existe, em seguida, a operação de atualização falhará. Por isso, se pretender armazenar uma entidade, independentemente de se já existe, deve em vez disso, utilizar **Inserir\_ou\_substituir\_entity()** ou **Inserir\_ou\_em série\_entity()**.

## <a name="work-with-groups-of-entities"></a>Trabalhar com grupos de entidades

Por vezes, faz sentido para submeter várias operações em conjunto num lote para se certificar de atómica processamento pelo servidor. Para realizar que, primeiro, crie um **lote de** objeto e, em seguida, utilize o **executar\_batch()** método no **TableService**. O exemplo seguinte demonstra submeter duas entidades com RowKey 2 e 3 numa secção. Repare que apenas funciona para entidades com o mesmo PartitionKey.

    azure_table_service = Azure::TableService.new
    batch = Azure::Storage::Table::Batch.new("testtable",
      "test-partition-key") do
      insert "2", { "content" => "new content 2" }
      insert "3", { "content" => "new content 3" }
    end
    results = azure_table_service.execute_batch(batch)

## <a name="query-for-an-entity"></a>Consulta para uma entidade

Para consultar uma entidade numa tabela, utilize o **obter\_entity()** método, passando o nome da tabela, **PartitionKey** e **RowKey**.

    result = azure_table_service.get_entity("testtable", "test-partition-key",
      "1")

## <a name="query-a-set-of-entities"></a>Um conjunto de entidades de consulta

Para consultar um conjunto de entidades numa tabela, crie um objeto de hash de consulta e utilize o **consulta\_entities()** método. O exemplo seguinte demonstra a introdução de todas as entidades com o mesmo **PartitionKey**:

    query = { :filter => "PartitionKey eq 'test-partition-key'" }
    result, token = azure_table_service.query_entities("testtable", query)

> [AZURE.NOTE] Se o conjunto de resultados é demasiado grande para uma única consulta devolver, um token de continuação de notas será devolvido que pode utilizar para recuperar páginas subsequentes.

## <a name="query-a-subset-of-entity-properties"></a>Um subconjunto de propriedades entidade de consulta

Uma consulta a uma tabela pode obter propriedades apenas alguns a partir de uma entidade. Esta técnica, denominada "projecções", reduz largura de banda e pode melhorar o desempenho da consulta, especialmente para entidades muito grandes. Utilize a cláusula select e passam os nomes das propriedades que gostaria de trazer ao longo para o cliente.

    query = { :filter => "PartitionKey eq 'test-partition-key'",
      :select => ["content"] }
    result, token = azure_table_service.query_entities("testtable", query)

## <a name="delete-an-entity"></a>Eliminar uma entidade

Para eliminar uma entidade, utilize o **Eliminar\_entity()** método. Tem de ser efetuada com na nome da tabela que contém a entidade, a PartitionKey e RowKey da entidade.

        azure_table_service.delete_entity("testtable", "test-partition-key", "1")

## <a name="delete-a-table"></a>Eliminar uma tabela

Para eliminar uma tabela, utilize o **Eliminar\_table()** método e passar na nome da tabela que pretende eliminar.

        azure_table_service.delete_table("testtable")

## <a name="next-steps"></a>Próximos passos

Para saber mais sobre as tarefas mais complexas do armazenamento, siga estas ligações:

- [Blogue da equipa do armazenamento Azure](http://blogs.msdn.com/b/windowsazurestorage/)
- Repositório de [Azure SDK para Rubi](http://github.com/WindowsAzure/azure-sdk-for-ruby) no GitHub
