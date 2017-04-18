<properties
    pageTitle="Módulo do PowerShell para máquina aprendizagem | Microsoft Azure"
    description="O módulo do PowerShell para Azure máquina aprendizagem está disponível no modo de pré-visualização público. Utilize o PowerShell para criar e gerir áreas de trabalho, experiências, serviços web e mais."
    keywords="experimentar, regressão linear, algoritmos de formação de máquina, tutorial de formação de máquina, técnicas de modelação de aspeto do Office, experiência de ciências de dados"
    services="machine-learning"
    documentationCenter=""
    authors="hning86"
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.workload="data-services"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/05/2016"
    ms.author="garye;haining"/>

# <a name="powershell-module-for-microsoft-azure-machine-learning"></a>Módulo do PowerShell para Microsoft Azure máquina Learning

O módulo do PowerShell para Azure máquina aprendizagem é uma poderosa ferramenta que permite-lhe utilizar o Windows PowerShell para gerir áreas de trabalho, experiências, conjuntos de dados, web serivces e muito mais.

Pode ver a documentação e transferir o módulo, juntamente com o código de origem completo, [https://aka.ms/amlps](https://aka.ms/amlps). 

## <a name="what-is-the-machine-learning-powershell-module"></a>O que é o módulo máquina aprendizagem PowerShell?

O módulo máquina aprendizagem PowerShell é um. Com base em líquido DLL módulo que permite-lhe gerir totalmente Azure máquina aprendizagem áreas de trabalho, experiências, conjuntos de dados, serviços web e pontos finais de serviço web a partir do Windows PowerShell. Juntamente com o módulo, pode transferir o código de origem completo que inclua uma [camada c# API](https://github.com/hning86/azuremlps/blob/master/code/AzureMLSDK.cs)simplesmente separados por vírgulas. Isto significa que pode referenciar esta DLL a partir do seu próprio projecto .NET e gerir Azure máquina formação através de código .NET. Além disso, a DLL depende subjacente REST APIs que pode tirar partido diretamente a partir do seu cliente de favorito.

## <a name="what-can-i-do-with-the-powershell-module"></a>O que posso fazer com o módulo PowerShell?

Aqui estão algumas das tarefas que pode ser executadas com este módulo do PowerShell. Consulte a [documentação completa](https://aka.ms/amlps) para estes e muitas outras funções.

- Aprovisionar uma nova área de trabalho para um certificado de gestão ([Novo AmlWorkspace](https://github.com/hning86/azuremlps#new-amlworkspace))
- Exportar e importar um ficheiro JSON que representa um gráfico da experiência ([AmlExperimentGraph exportar](https://github.com/hning86/azuremlps#export-amlexperimentgraph) e [Importar AmlExperimentGraph](https://github.com/hning86/azuremlps#import-amlexperimentgraph))
- Executar uma experiência ([AmlExperiment início](https://github.com/hning86/azuremlps#start-amlexperiment))
- Criar um serviço web terminar uma experiência de aspeto do Office ([Novo AmlWebService](https://github.com/hning86/azuremlps#new-amlwebservice))
- Criar um novo ponto final de um serviço web publicada ([Adicionar AmlWebServiceEndpoint](https://github.com/hning86/azuremlps#add-amlwebserviceendpoint))
- Invocar um RRS e/ou BES web ponto final de serviço ([Invocar AmlWebServiceRRSEndpoint](https://github.com/hning86/azuremlps#invoke-amlwebservicerrsendpoint) e [Invocar AmlWebServicBESEndpoint](https://github.com/hning86/azuremlps#invoke-amlwebservicebesendpoint))

Eis um exemplo rápido de utilizar o PowerShell para executar uma experiência existente:

        #Find the first Experiment named “xyz”
        $exp = (Get-AmlExperiment | where Description -eq ‘xyz’)[0]
        #Run the Experiment
        Start-AmlExperiment -ExperimentId $exp.ExperimentId 

Para um caso de utilização de forma mais aprofundado, consulte este artigo sobre como utilizar o módulo PowerShell para automatizar uma tarefa muito frequentemente pedidas: [criar muitos modelos de aprendizagem automática e web pontos finais de serviço a partir de uma experiência através do PowerShell](machine-learning-create-models-and-endpoints-with-powershell.md).

## <a name="how-do-i-get-started"></a>Como posso começar a?

Para começar a utilizar com o PowerShell de formação de máquina, transfira o [solte pacote](https://github.com/hning86/azuremlps/releases) a partir de GitHub e siga as [instruções de instalação](https://github.com/hning86/azuremlps/blob/master/README.md). Terá de desbloquear a DLL transferido/descompactados e, em seguida, importá-lo para o ambiente do PowerShell. A maior parte dos cmdlets requerem que forneceu o ID da área de trabalho, o token de autorização de área de trabalho e o Azure região que está a área de trabalho. A forma mais simples para fornecer estes é através de um ficheiro de config.json predefinido, que é abrangido detalhe as instruções de instalação. Obviamente, pode também clonar a árvore de git e modificar/compilar o código localmente utilizando o Visual Studio.

## <a name="next-steps"></a>Próximos passos

O módulo PowerShell irão continuar a ser melhoradas e expandido durante este período de pré-visualização. Controlar a [Cortana Intelligence e máquina aprendizagem blogue](https://blogs.technet.microsoft.com/machinelearning/) mais notícias e informações.
