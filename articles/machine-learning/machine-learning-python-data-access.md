<properties 
    pageTitle="Aceder aos conjuntos de dados com a biblioteca do cliente máquina aprendizagem Python | Microsoft Azure" 
    description="Instalar e utilizar a biblioteca do cliente Python para aceder e gerir dados Azure máquina formação em segurança a partir de um ambiente Python local." 
    services="machine-learning" 
    documentationCenter="python" 
    authors="bradsev" 
    manager="jhubbard" 
    editor="cgronlun"/>

<tags 
    ms.service="machine-learning" 
    ms.workload="data-services" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/12/2016" 
    ms.author="huvalo;bradsev" />


#<a name="access-datasets-with-python-using-the-azure-machine-learning-python-client-library"></a>Conjuntos de dados do Access com Python utilizando a biblioteca de cliente do Azure máquina aprendizagem Python 

A pré-visualização da biblioteca de cliente do Microsoft Azure máquina aprendizagem Python pode ativar o acesso seguro para sua conjuntos de dados do Azure máquina formação a partir de um ambiente Python local e permite a criação e gestão de conjuntos de dados numa área de trabalho.

Este tópico fornece instruções sobre como:

* instalar a biblioteca Python de aprendizagem automática do cliente 
* aceder e carregue os conjuntos de dados, incluindo as instruções sobre como obter autorização para aceder ao Azure máquina aprendizagem conjuntos de dados a partir do seu ambiente Python local
*  aceder a intermédios conjuntos de dados a partir experiências
*  utilizar a biblioteca do cliente Python enumerar conjuntos de dados, aceder aos metadados, leia o conteúdo de um conjunto de dados, criar novos conjuntos de dados e atualizar conjuntos de dados existentes

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

 
##<a name="prerequisites"></a>Pré-requisitos

A biblioteca do cliente Python foi testada em ambientes os seguintes:

 - Windows, Mac e Linux
 - Python 2.7, 3.3 e 3.4

Dependência de-tem nos pacotes do seguintes:

 - pedidos de
 - Python dateutil
 - pandas

Recomendamos que utilize uma distribuição Python, tais como [Anaconda](http://continuum.io/downloads#all) ou [cúpula](https://store.enthought.com/downloads/), que vêm com Python, IPython e os pacotes de três listado acima instalada. Apesar de IPython não é necessária estritamente, é um excelente ambiente para manipular e visualizar dados de forma interativa.


###<a name="installation"></a>Como instalar a biblioteca do cliente Azure máquina aprendizagem Python

A biblioteca do cliente Azure máquina aprendizagem Python também tem de estar instalada para concluir as tarefas descritas neste tópico. Está disponível a partir do [Índice de pacote Python](https://pypi.python.org/pypi/azureml). Para instalá-lo no seu ambiente Python, execute o seguinte comando a partir do seu ambiente Python local:

    pip install azureml

Em alternativa, pode transferir e instalar a partir de origens no [github](https://github.com/Azure/Azure-MachineLearning-ClientLibrary-Python).

    python setup.py install

Se tiver git instalado no seu computador, pode utilizar pip para instalar diretamente a partir do repositório de git:

    pip install git+https://github.com/Azure/Azure-MachineLearning-ClientLibrary-Python.git


##<a name="datasetAccess"></a>Utilizar fragmentos de código Studio para aceder a conjuntos de dados

A biblioteca do cliente Python dá-lhe o acesso de programação aos seus conjuntos de dados existentes a partir do experiências tem sido executadas.

A partir da interface de web Studio, pode gerar fragmentos de código que incluam todas as informações necessárias para transferir e serialização conjuntos de dados como Pandas DataFrame objetos no seu computador de localização.

### <a name="security"></a>Segurança para o acesso a dados

Fragmentos de código fornecidos pela Studio para utilização com a biblioteca do cliente Python inclui o seu id de área de trabalho e autorização token. Estes fornecem acesso total a área de trabalho e devem ser protegidos, como uma palavra-passe.

Por motivos de segurança, a funcionalidade de fragmento de código só está disponível para utilizadores que tenham o seu papel definido como **proprietário** para a área de trabalho. Ao seu cargo é apresentado no Azure máquina aprendizagem Studio na página **utilizadores** em **Definições**.

![Segurança][security]

Se a sua função não estiver definida como **proprietário**, pode qualquer pedido para ser novamente convidado como um proprietário ou pergunte ao proprietário da área de trabalho para fornecer-lhe o fragmento de código.

Para obter o token de autorização, pode efetuar um dos seguintes procedimentos:



- Peça para obter um token a partir de um proprietário. Os proprietários podem aceder a respectivos tokens de autorização a partir da página de definições da sua área de trabalho no Studio. Selecione **Definições** a partir do painel esquerdo e clique em **TOKENS de autorização** para ver os tokens principais e secundários.  Apesar da página principal ou os tokens de autorização secundário podem ser utilizados no fragmento de código, é recomendável que os proprietários de partilham apenas os tokens de autorização secundário.

![](./media/machine-learning-python-data-access/ml-python-access-settings-tokens.png)

- Pedir para ser promovido a função do proprietário.  Para executar esta tarefa, um proprietário atual da área de trabalho necessita pela primeira vez remove a área de trabalho, em seguida, voltar convidá-lo para o mesmo como um proprietário.

Assim que os programadores obtiveram o id de área de trabalho e a autorização token, são possível aceder à área de trabalho utilizando o fragmento de código, independentemente da respetiva função.

Tokens de autorização são geridas na página de **Autorização de TOKENS** em **Definições**. Pode gerá-los, mas este procedimento revoga acesso ao token de anterior.

### <a name="accessingDatasets"></a>Conjuntos de dados do Access a partir de uma aplicação Python local

1. No computador aprendizagem Studio, clique em **conjuntos de dados** na barra de navegação à esquerda.

2. Selecione o conjunto de dados que pretende aceder. Pode selecionar qualquer uma dos conjuntos de dados a partir da lista de **Conjuntos de dados meus** ou a partir da lista de **amostras** .

3. Barra de ferramentas inferior, clique em **gerar código de acesso de dados**. Se os dados num formato compatível com a biblioteca do cliente Python, este botão é desativado.

    ![Conjuntos de dados][datasets]

4. Selecione o fragmento de código a partir da janela que aparece e copiá-lo para a sua área de transferência.

    ![Código de acesso][dataset-access-code]

5. Cole o código para o bloco de notas da sua aplicação Python local.

    ![Bloco de notas][ipython-dataset]

## <a name="accessingIntermediateDatasets"></a>Conjuntos de dados intermédios do Access a partir de aprendizagem máquina experiências

Depois de uma experiência é executada no Studio de aprendizagem máquina, é possível aceder os intermédios conjuntos de dados a partir de nós de saída dos módulos. Conjuntos de dados intermédios são os dados que foram criados e utilizados para obter os passos intermédios quando uma ferramenta de modelo foi executada.

Podem ser acedidos intermédios conjuntos de dados, como o formato de dados é compatível com a biblioteca do cliente Python.

São suportados os seguintes formatos (constantes para estes estão a `azureml.DataTypeIds` escolares):

 - Texto simples
 - GenericCSV
 - GenericTSV
 - GenericCSVNoHeader
 - GenericTSVNoHeader

Pode determinar o formato ao pairar o rato sobre um nó de saída do módulo. É apresentada juntamente com o nome do nó, uma descrição.

Alguns dos módulos, tal como a [divisão] [ split] módulo, exportar para um formato com nome `Dataset`, que não é suportado pela biblioteca do cliente Python.

![Formato de conjunto de dados][dataset-format]

Tem de utilizar um módulo de conversão, tal como [Converter CSV][convert-to-csv], para obter um resultado para um formato suportado.

![Formato de GenericCSV][csv-format]

Os seguintes passos mostram um exemplo que cria uma experiência, executa-lo e acede o conjunto de dados intermédio.

1. Crie uma nova experiência.

2. Inserir um módulo de **classificação de binário adultos contagem rendimento conjunto de dados** .

3. Inserir uma [divisão] [ split] módulo e ligue a respectiva entrada para a saída de módulo do conjunto de dados.

4. Inserir um [Converter CSV] [ convert-to-csv] módulo e ligue a respectiva entrada a um da [divisão] [ split] exporta módulo.

5. Guardar a experiência, executá-la e aguarde que ele concluir a trabalhar rapidamente.

6. Clique no nó de saída no [Converter CSV] [ convert-to-csv] módulo.

7. Quando for apresentado o menu de contexto, selecione **gerar código de acesso de dados**.

    ![Menu de contexto][experiment]

8. Selecione o fragmento de código e copiá-lo para sua área de transferência a partir da janela que é apresentada.

    ![Código de acesso][intermediate-dataset-access-code]

9. Cole o código no seu bloco de notas.

    ![Bloco de notas][ipython-intermediate-dataset]

10. Pode visualizar os dados utilizando matplotlib. Esta ação apresentará um histograma para a coluna de idade:

    ![Histograma][ipython-histogram]


##<a name="clientApis"></a>Utilizar a biblioteca do cliente máquina aprendizagem Python acesso, ler, criar e gerir conjuntos de dados

### <a name="workspace"></a>Área de trabalho

A área de trabalho é o ponto de entrada para a biblioteca de cliente Python. Fornecer a `Workspace` token de classe com o seu id de área de trabalho e autorização para criar uma instância:

    ws = Workspace(workspace_id='4c29e1adeba2e5a7cbeb0e4f4adfb4df',
                   authorization_token='f4f3ade2c6aefdb1afb043cd8bcf3daf')


### <a name="enumerate-datasets"></a>Enumerar conjuntos de dados

Enumerar todos os conjuntos de dados numa área de trabalho determinado:

    for ds in ws.datasets:
        print(ds.name)

Enumerar apenas os utilizador criado conjuntos de dados:

    for ds in ws.user_datasets:
        print(ds.name)

Enumerar apenas os conjuntos de dados de exemplo:

    for ds in ws.example_datasets:
        print(ds.name)

Pode aceder a um conjunto de dados pelo nome (que é entre maiúsculas e minúsculas):

    ds = ws.datasets['my dataset name']

Ou pode ser acedido por índice:

    ds = ws.datasets[0]


### <a name="metadata"></a>Metadados

Conjuntos de dados tem metadados, para além do conteúdo. (Intermédios conjuntos de dados são uma exceção para esta regra e não tem qualquer metadados.)

Alguns valores de metadados são atribuídos pelo utilizador na hora de criação:

    print(ds.name)
    print(ds.description)
    print(ds.family_id)
    print(ds.data_type_id)

As outras pessoas estão valores atribuídos pelo Azure ML:

    print(ds.id)
    print(ds.created_date)
    print(ds.size)

Consulte o artigo o `SourceDataset` classe para obter mais informações sobre os metadados disponíveis.


### <a name="read-contents"></a>Ler conteúdos

Fragmentos de código fornecidos automaticamente pelo máquina aprendizagem Studio transfira e serialização o conjunto de dados para um objeto de Pandas DataFrame. Esta é executada com o `to_dataframe` método:

    frame = ds.to_dataframe()

Se preferir transferir os dados não processados e executar a desserialização si próprio, que é uma opção. Neste momento, este é a única opção para formatos de como 'ARFF', a biblioteca do cliente Python não é possível serialização.

Para ler o conteúdo como texto:

    text_data = ds.read_as_text()

Para ler os conteúdos como binários:

    binary_data = ds.read_as_binary()

Pode abrir também tem apenas uma sequência aos conteúdos:

    with ds.open() as file:
        binary_data_chunk = file.read(1000)


### <a name="create-a-new-dataset"></a>Criar um novo conjunto de dados

A biblioteca do cliente Python permite-lhe carregar conjuntos de dados a partir do seu programa de Python. Estes conjuntos de dados, em seguida, estão disponíveis para utilização na área de trabalho.

Se os dados que tiver uma DataFrame Pandas, utilize o seguinte código:

    from azureml import DataTypeIds

    dataset = ws.datasets.add_from_dataframe(
        dataframe=frame,
        data_type_id=DataTypeIds.GenericCSV,
        name='my new dataset',
        description='my description'
    )

Se já for serializados os seus dados, pode utilizar:

    from azureml import DataTypeIds

    dataset = ws.datasets.add_from_raw_data(
        raw_data=raw_data,
        data_type_id=DataTypeIds.GenericCSV,
        name='my new dataset',
        description='my description'
    )

A biblioteca do cliente Python é possível serializar um DataFrame Pandas para os seguintes formatos (constantes para estes estão a `azureml.DataTypeIds` escolares):

 - Texto simples
 - GenericCSV
 - GenericTSV
 - GenericCSVNoHeader
 - GenericTSVNoHeader


### <a name="update-an-existing-dataset"></a>Atualizar um conjunto de dados existente

Se tentar carregar um novo conjunto de dados com um nome que corresponde a um conjunto de dados existente, deve obter um erro de conflito.

Para atualizar um conjunto de dados existente, tem primeiro de obter uma referência para o conjunto de dados existente:

    dataset = ws.datasets['existing dataset']

    print(dataset.data_type_id) # 'GenericCSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to jan 2015'

Em seguida, utilize `update_from_dataframe` para serializar e substituir o conteúdo do conjunto de dados no Azure:

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(frame2)

    print(dataset.data_type_id) # 'GenericCSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to jan 2015'

Se pretender serializar os dados para um formato diferente, especificar um valor para opcional `data_type_id` parâmetro.

    from azureml import DataTypeIds

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(
        dataframe=frame2,
        data_type_id=DataTypeIds.GenericTSV,
    )

    print(dataset.data_type_id) # 'GenericTSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to jan 2015'

Opcionalmente, pode definir uma nova descrição ao especificar um valor para o `description` parâmetro.

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(
        dataframe=frame2,
        description='data up to feb 2015',
    )

    print(dataset.data_type_id) # 'GenericCSV'
    print(dataset.name)         # 'existing dataset'
    print(dataset.description)  # 'data up to feb 2015'

Opcionalmente, pode definir um novo nome ao especificar um valor para o `name` parâmetro. De agora em, irá obter o conjunto de dados utilizando o novo nome. O código seguinte atualiza os dados, nome e descrição.

    dataset = ws.datasets['existing dataset']

    dataset.update_from_dataframe(
        dataframe=frame2,
        name='existing dataset v2',
        description='data up to feb 2015',
    )

    print(dataset.data_type_id)                    # 'GenericCSV'
    print(dataset.name)                            # 'existing dataset v2'
    print(dataset.description)                     # 'data up to feb 2015'

    print(ws.datasets['existing dataset v2'].name) # 'existing dataset v2'
    print(ws.datasets['existing dataset'].name)    # IndexError

O `data_type_id`, `name` e `description` parâmetros são opcionais e predefinido para os valores anteriores. O `dataframe` parâmetro é sempre necessário.

Se já for serializados os seus dados, utilizar `update_from_raw_data` em vez de `update_from_dataframe`. Se passar no `raw_data` em vez de `dataframe`, funciona de forma semelhante.



<!-- Images -->
[security]:./media/machine-learning-python-data-access/security.png
[dataset-format]:./media/machine-learning-python-data-access/dataset-format.png
[csv-format]:./media/machine-learning-python-data-access/csv-format.png
[datasets]:./media/machine-learning-python-data-access/datasets.png
[dataset-access-code]:./media/machine-learning-python-data-access/dataset-access-code.png
[ipython-dataset]:./media/machine-learning-python-data-access/ipython-dataset.png
[experiment]:./media/machine-learning-python-data-access/experiment.png
[intermediate-dataset-access-code]:./media/machine-learning-python-data-access/intermediate-dataset-access-code.png
[ipython-intermediate-dataset]:./media/machine-learning-python-data-access/ipython-intermediate-dataset.png
[ipython-histogram]:./media/machine-learning-python-data-access/ipython-histogram.png


<!-- Module References -->
[convert-to-csv]: https://msdn.microsoft.com/library/azure/faa6ba63-383c-4086-ba58-7abf26b85814/
[split]: https://msdn.microsoft.com/library/azure/70530644-c97a-4ab6-85f7-88bf30a8be5f/
 
