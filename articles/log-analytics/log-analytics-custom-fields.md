<properties
   pageTitle="Campos personalizados no registo de análise | Microsoft Azure"
   description="A funcionalidade de campos personalizados do registo de análise permite-lhe criar os seus próprios campos pesquisáveis a partir de dados OMS que adicionar às propriedades de um registo recolhido.  Este artigo descreve o processo para criar um campo personalizado e fornece instruções detalhadas sobre com um evento de exemplo."
   services="log-analytics"
   documentationCenter=""
   authors="bwren"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="log-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/18/2016"
   ms.author="bwren" />

# <a name="custom-fields-in-log-analytics"></a>Campos personalizados no registo de análise

A funcionalidade de **Campos personalizados** do registo de análise permite-lhe expandir registos existentes no repositório de OMS ao adicionar os próprios campos pesquisáveis.  Campos personalizados são preenchidos automaticamente a partir de dados extraídos de outras propriedades no mesmo registo.

![Descrição geral dos campos personalizados](media/log-analytics-custom-fields/overview.png)

Por exemplo, o registo de exemplo abaixo apresenta dados úteis outra forma soterrados na descrição do evento.  Extrair estes dados para propriedades separadas, este fica disponível para acções tais como a ordenação e filtragem.

![Botão de pesquisa de registo](media/log-analytics-custom-fields/sample-extract.png)

>[AZURE.NOTE] Na pré-visualização, está limitado a 100 campos personalizados na área de trabalho.  Este limite será expandida quando esta funcionalidade atinge disponibilidade geral.

## <a name="creating-a-custom-field"></a>Criar um campo personalizado

Quando cria um campo personalizado, tem de registo de análise compreender quais os dados a utilizar para preencher o valor.  Utiliza uma tecnologia pela Microsoft Research denominado FlashExtract identificar rapidamente estes dados.  Em vez de que seja necessário fornecer instruções explícitas, análises de registo aprende sobre os dados que pretende extrair de exemplos que indicar.

As secções seguintes fornecem o procedimento para criar um campo personalizado.  Na parte inferior deste artigo é instruções sobre extração de exemplo.

> [!NOTE] O campo personalizado é povoado medida que os registos que correspondam aos critérios especificados são adicionados ao arquivo de dados OMS, para que só será apresentado nos registos recolhidos depois do campo personalizado é criado.  O campo personalizado não será adicionado aos registos que já estão no arquivo de dados quando é criado.

### <a name="step-1--identify-records-that-will-have-the-custom-field"></a>Passo 1 – identificar os registos que terão o campo personalizado
O primeiro passo é identificar os registos que irão obter o campo personalizado.  Começar com uma [pesquisa de registo padrão](log-analytics-log-searches.md) e, em seguida, selecione um registo para funcionar como o modelo que vai aprender a análise de registo a partir de.  Quando especificar que vai para extrair dados para um campo personalizado, é aberto o **Assistente de extração de campo** onde validar e otimizar os critérios.

2. Vá para o **Registo de pesquisa** e utilizar uma [consulta para obter os registos](log-analytics-log-searches.md) que terão o campo personalizado.
2. Selecione um registo que irá utilizar a análise de registo para funcionar como um modelo para extrair dados para preencher o campo personalizado.  Irá identificar os dados que pretende extrair deste registo e registo de análise utilizará estas informações para determinar a lógica para preencher o campo personalizado para todos os registos semelhantes.
3. Clique no botão para a esquerda de qualquer propriedade texto do registo e selecione **extrair campos a partir de**.
4. O **campo extração de assistente é aberto**e o registo que selecionou é apresentado na coluna **Principal exemplo** .  O campo personalizado será definido para esses mesmos registos com os mesmos valores nas propriedades que estão selecionados.  
5. Se a seleção não exatamente o que pretende, selecione campos adicionais para limitar os critérios.  Para alterar os valores de campo para os critérios, tem de cancelar e selecionar um registo diferente que correspondam aos critérios que pretende.

### <a name="step-2---perform-initial-extract"></a>Passo 2 – executar extrair inicial.
Uma vez que identificou os registos que terão o campo personalizado, identifique os dados que pretende extrair.  Registo a análise utilizará estas informações para identificar padrões semelhantes em registos semelhantes.  No passo após estes será capaz de validar os resultados e fornecer ainda mais detalhes para análise de registo utilizar na sua análise.

1. Realce o texto no registo de exemplo que pretende preencher o campo personalizado.  Será apresentada, em seguida, com uma caixa de diálogo para fornecer um nome para o campo e para executar o extrair inicial.  Os carateres ** \_CF** será automaticamente anexada.
2. Clique em **extrair** para efetuar uma análise de registos recolhidos.  
3. As secções de **Resumo** e **Os resultados da pesquisa** apresentarem os resultados da extrair, de modo que pode inspecionar sua precisão.  **Resumo** apresenta os critérios utilizados para identificar os registos e uma contagem para cada um dos valores dos dados identificados.  **Resultados de pesquisa** fornece uma lista detalhada dos registos que correspondam a critérios.


### <a name="step-3--verify-accuracy-of-the-extract-and-create-custom-field"></a>Passo 3 – verificar a precisão da extrair e criar campo personalizado

Assim que tiver executado a extrair inicial, a análise de registo irá apresentar os seus resultados com base em dados que já foram recolhidos.  Se os resultados parece estar correta, em seguida, pode criar o campo personalizado com o trabalho sem mais.  Caso contrário, em seguida, pode otimizar os resultados para que o registo de análise pode melhorar a respectiva lógica.

2.  Se quaisquer valores na extrair inicial não estiverem corretas, em seguida, clique no ícone **Editar** junto a um registo impreciso e selecione **modificar este realce** para poder modificar a seleção.
3.  A entrada é copiada para a secção **exemplos adicionais** sob o **Exemplo de principal**.  Pode ajustar o realce aqui para ajudar a compreender a seleção que deve efetuadas a análise de registo.
4.  Clique em **extrair** para utilizar estas informações novas para avaliar todos os registos existentes.  Os resultados podem ser modificados para os registos que não seja aquele que modificou apenas com base neste novas informações da empresa.
5.  Continue a adicionar correções até que todos os registos a extrair corretamente identificam os dados para preencher o novo campo personalizado.
6. Clique em **Guardar extrair** quando estiver satisfeito com os resultados.  O campo personalizado agora é definido, mas não possível adicioná-lo para quaisquer registos ainda.
7.  Aguarde para novos registos que correspondam a critérios especificados a recolher e, em seguida, execute novamente a pesquisa de registo. Novos registos deverão ter o campo personalizado.
8.  Utilize o campo personalizado, como qualquer outra propriedade registo.  Pode utilizá-la para agregados e agrupar dados e até mesmo utilizá-lo para produzir novas informações.


## <a name="viewing-custom-fields"></a>Ver os campos personalizados
Pode ver uma lista de todos os campos personalizados no seu grupo de gestão do mosaico **Definições** do OMS dashboard.  Selecione os **dados** e, em seguida, **campos personalizados** para uma lista de todos os campos personalizados na área de trabalho.  

![Campos personalizados](media/log-analytics-custom-fields/list.png)

## <a name="removing-a-custom-field"></a>Remover um campo personalizado
Existem duas formas para remover um campo personalizado.  O primeiro é a opção **Remover** para cada campo ao visualizar a lista completa, tal como descrito acima.  É o outro método obter um registo e clique no botão à esquerda do campo.  No menu terá uma opção para remover o campo personalizado.

## <a name="sample-walkthrough"></a>Tutorial de exemplo

A secção seguinte explica um exemplo completo da criação de um campo personalizado.  Este exemplo extrai o nome do serviço de eventos do Windows que indicam um serviço de alterar o estado.  Baseia-se eventos criado pelo Gestor de controlo do serviço no registo do sistema em computadores com Windows.  Se pretender seguir neste exemplo, tem de ser [recolher eventos de informações para o registo do sistema](log-analytics-data-sources-windows-events.md).

Vamos introduza a seguinte consulta para devolver todos os eventos do serviço de Gestor de controlo que tenham um ID do 7036 evento que é o evento que indica um serviço iniciar ou parar.

![Consulta](media/log-analytics-custom-fields/query.png)

Vamos, em seguida, selecione qualquer registo com 7036 de ID do evento.

![Registo de origem](media/log-analytics-custom-fields/source-record.png)

Pretendemos o nome do serviço que é apresentada na propriedade **RenderedDescription** e selecione o botão junto a esta propriedade.

![Extrair campos](media/log-analytics-custom-fields/extract-fields.png)

É aberto o **Assistente de extração de campo** e os campos de **registo de eventos** e **IDdoevento** são seleccionados na coluna **Principal exemplo** .  Isto indica que o campo personalizado será definido para eventos de registo do sistema com um ID do 7036 evento.  Isto é suficiente para que não precisamos selecionar os campos.

![Exemplo de principal](media/log-analytics-custom-fields/main-example.png)

Estamos a realçar o nome do serviço na propriedade **RenderedDescription** e utilizar o **serviço** para identificar o nome do serviço.  O campo personalizado será chamado **Service_CF**.

![Título do campo](media/log-analytics-custom-fields/field-title.png)

Vemos que o nome do serviço é identificado corretamente para alguns registos mas não para outras pessoas.   Mostrar **Os resultados da pesquisa** que parte do nome para o **Adaptador de desempenho WMI** não foi selecionado.  O **Resumo** mostra que quatro registos com o serviço **DPRMA** incorretamente incluída uma palavra extra e dois registos identificaram **Módulos Installer** em vez do **Windows Installer módulos**.  

![Resultados da pesquisa](media/log-analytics-custom-fields/search-results-01.png)

Vamos começar com o registo de **Adaptador de desempenho WMI** .  Vamos clique em respetivo ícone de edição e, em seguida, **modificar este realce**.  

![Modificar o realce](media/log-analytics-custom-fields/modify-highlight.png)

Vamos aumentar o realce para incluir a palavra **WMI** e, em seguida, volte a executar o extrair.  

![Exemplo adicional](media/log-analytics-custom-fields/additional-example-01.png)

Pode Vemos que as entradas da **Adaptador de desempenho WMI** tem sido corrigidas e a análise de registo também utilizada essas informações para corrigir os registos para o **Windows Installer do módulo**.  É possível ver na secção de **Resumo** apesar esse **DPMRA** ainda não é a ser identificado corretamente.

![Resultados da pesquisa](media/log-analytics-custom-fields/search-results-02.png)

Estamos desloque-se para um registo com o serviço DPMRA e utilize o mesmo processo para corrigir esse registo.

![Exemplo adicional](media/log-analytics-custom-fields/additional-example-02.png)

 Quando é executada a extração de, podemos ver que todos os nossos resultados estão corretas.

![Resultados da pesquisa](media/log-analytics-custom-fields/search-results-03.png)

É possível ver que **Service_CF** é criado mas ainda não é adicionado a todos os registos.

![Contagem inicial](media/log-analytics-custom-fields/initial-count.png)

Após algum tempo ter passado para novos eventos que são recolhidos, podemos ver que que o campo **Service_CF** está agora a ser adicionado os registos que correspondam aos nossos os critérios.

![Resultados finais](media/log-analytics-custom-fields/final-results.png)

Vamos agora pode utilizar o campo personalizado, como qualquer outra propriedade de registo.  Para ilustrar isto, podemos criar uma consulta que agrupa por novo **Service_CF** campo para inspecionar os serviços que são mais ativos.

![Agrupar por uma consulta](media/log-analytics-custom-fields/query-group.png)

## <a name="next-steps"></a>Próximos passos

- Saiba [as pesquisas de registo](log-analytics-log-searches.md) construir consultas através de campos personalizados no critério.
- Monitorizar os [ficheiros de registo personalizados](log-analytics-data-sources-custom-logs.md) que analisar a utilizando campos personalizados.