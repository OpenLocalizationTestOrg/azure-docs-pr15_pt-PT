<properties
   pageTitle="Migrar do Orchestrator para automatização Azure | Microsoft Azure"
   description="Descreve como migrar packs runbooks e integração de Orchestrator do Centro de sistema para automatização Azure."
   services="automation"
   documentationCenter=""
   authors="bwren"
   manager="stevenka"
   editor="tysonn" />
<tags
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/09/2016"
   ms.author="bwren" />


# <a name="migrating-from-orchestrator-to-azure-automation-beta"></a>Migrar do Orchestrator para automatização Azure (Beta)

Runbooks no [Sistema Centro Orchestrator](http://technet.microsoft.com/library/hh237242.aspx) são baseados em atividades de pacotes de integração escritas especificamente para Orchestrator enquanto runbooks no Azure automatização são baseados no Windows PowerShell.  [Runbooks gráficas](automation-runbook-types.md#graphical-runbooks) no Azure automatização ter um aspeto semelhante ao Orchestrator runbooks com as respetivas atividades que representa os cmdlets do PowerShell, subordinado runbooks e recursos.

O [Toolkit de migração do sistema Centro Orchestrator](http://www.microsoft.com/download/details.aspx?id=47323&WT.mc_id=rss_alldownloads_all) inclui ferramentas para ajudá-lo a converter runbooks Orchestrator Azure automatização.  Para além de converter o runbooks eles próprios, tem de converter os pacotes de integração com as atividades que utilizam os runbooks módulos de integração com os cmdlets do Windows PowerShell.  

Segue-se o processo básico para converter Orchestrator runbooks Azure automatização.  Cada um destes passos é descrita detalhadamente nas secções abaixo.

1.  Transferir o [Toolkit de migração do sistema Centro Orchestrator](http://www.microsoft.com/download/details.aspx?id=47323&WT.mc_id=rss_alldownloads_all) que contém as ferramentas e módulos de outros fabricantes referidos neste artigo.
2.  Importe [atividades normais módulo](#standard-activities-module) Azure automatização.  Isto inclui convertidos versões das atividades Orchestrator padrão que podem ser utilizadas pelo runbooks convertidos.
3.  Importe [Módulos de integração de Orchestrator de centro de sistema](#system-center-orchestrator-integration-modules) para o Azure automatização para esses packs integração utilizados pelo seu runbooks que aceder ao centro de sistema.
4.  Converter personalizada e de terceiros pacotes de integração utilizando o [Conversor do pacote de integração](#integration-pack-converter) e importar para o Azure automatização.
5.  Converter runbooks Orchestrator utilizando o [Conversor de livro execuções](#runbook-converter) e instale no Azure automatização.
6.  Crie manualmente elementos de Orchestrator necessários no Azure automatização uma vez que o conversor de livro execuções não converter estes recursos.
7.  Configure um [Híbrido livro execuções trabalho](#hybrid-runbook-worker) no Centro de dados locais para executar runbooks convertido que irá aceder a recursos locais.

## <a name="service-management-automation"></a>Automatização de gestão de serviços

[Automatização de gestão de serviços](http://technet.microsoft.com/library/dn469260.aspx) (SMA) armazena e executa runbooks no Centro de dados locais como Orchestrator e utiliza os módulos de integração com o mesmo como Azure automatização. O [Livro execuções conversor](#runbook-converter) converte Orchestrator runbooks runbooks gráfica apesar que não são suportadas no SMA.  Pode continuar a instalar o [Módulo padrão de atividades](#standard-activities-module) e [Módulos de integração do sistema Centro Orchestrator](#system-center-orchestrator-integration-modules) para SMA, mas tem de manualmente [reescrever a sua runbooks](http://technet.microsoft.com/library/dn469262.aspx).

## <a name="hybrid-runbook-worker"></a>Trabalhador de livro execuções híbrido

Runbooks no Orchestrator estão armazenados num servidor de base de dados e ser executados em servidores de livro execuções, tanto no Centro de dados locais.  Runbooks no Azure automatização são armazenados na nuvem Azure e pode executar no Centro de dados locais utilizando um [Híbrido livro execuções trabalhador](automation-hybrid-runbook-worker.md).  Este é o modo como normalmente será executada runbooks convertido a partir de Orchestrator uma vez que são concebidos para ser executados em servidores locais.

## <a name="integration-pack-converter"></a>Integração Pack conversor

O conversor do pacote de integração converte os pacotes de integração que foram criados utilizando o [Toolkit de integração de Orchestrator (OIT)](http://technet.microsoft.com/library/hh855853.aspx) para módulos de integração com base no Windows PowerShell, que podem ser importados para o Azure automatização ou serviço de gestão de automatização.  

Quando executa o conversor do pacote de integração, são apresentadas com um assistente que permitirá selecionar um ficheiro de pacote (.oip) integração.  O assistente, em seguida, apresenta as actividades incluídas no pacote que integração e permite-lhe selecionar que será migrado.  Quando concluir o assistente, cria um módulo de integração que inclui um cmdlet correspondente para cada uma das atividades no pacote integração original.


### <a name="parameters"></a>Parâmetros

As propriedades de uma atividade no pacote integração são convertidas em parâmetros do cmdlet correspondente no módulo integração.  Cmdlets do Windows PowerShell ter um conjunto de [parâmetros comuns](http://technet.microsoft.com/library/hh847884.aspx) que pode ser utilizada com todos os cmdlets.  Por exemplo, o verboso parâmetro - faz com que um cmdlet para informações detalhadas sobre o funcionamento de saída.  Sem cmdlet poderá ter um parâmetro com o mesmo nome como um parâmetro comuns.  Se uma actividade tem uma propriedade com o mesmo nome como um parâmetro comuns, o assistente irá pedir para fornecer o outro nome para o parâmetro.

### <a name="monitor-activities"></a>Atividades do monitor

Monitor runbooks no Orchestrator comece com um [monitor de atividade](http://technet.microsoft.com/library/hh403827.aspx) e executar continuamente a aguardar chamado por um evento específico.  Automatização Azure não suporta monitor runbooks, para que outras atividades do monitor no pacote integração não serão convertidas.  Em vez disso, é criado um cmdlet do marcador de posição no módulo integração da atividade de monitor de.  Este cmdlet tem sem a funcionalidade, mas permitir qualquer livro de execuções convertido que utiliza-la para ser instalado.  Este livro de execuções não conseguir executar no Azure automatização, mas pode ser instalado para que pode modificá-lo.

### <a name="integration-packs-that-cannot-be-converted"></a>Pacotes de integração que não podem ser convertidos

Pacotes de integração que não foram criados com OIT não podem ser convertidos com o conversor do pacote de integração. Também existem algumas pacotes de integração fornecidas pela Microsoft atualmente não pode ser convertido com esta ferramenta.  Convertidos versões destes pacotes de integração de tem sido [fornecido para transferência](#system-center-orchestrator-integration-modules) para que possa ser instaladas no Azure automatização ou serviço de gestão de automatização.


## <a name="standard-activities-module"></a>Módulo padrão de atividades

Orchestrator inclui um conjunto de [atividades normais](http://technet.microsoft.com/library/hh403832.aspx) que não são incluídas um pacote de integração, mas são utilizadas pelo runbooks muitos.  O módulo atividades normais é um módulo de integração que inclui um cmdlet equivalente para cada uma das seguintes atividades.  Tem de instalar este módulo integração no Azure automatização antes de importar quaisquer runbooks convertido que utilize uma atividade de padrão.

Para além de suporte runbooks convertido, os cmdlets no módulo atividades normais podem ser utilizados por alguém que esteja familiarizado com Orchestrator para criar novas runbooks no Azure automatização.  Enquanto a funcionalidade de todas as atividades padrão pode ser executada com os cmdlets do, estes podem funcionam de forma diferente.  Os cmdlets no módulo convertido atividades normais irá funcionam da mesma as respetivas atividades correspondentes e utilize os mesmos parâmetros.  Isto pode ajudar o autor do livro execuções Orchestrator existente no respetivo transição para runbooks de automatização do Azure.

## <a name="system-center-orchestrator-integration-modules"></a>Módulos de integração de Orchestrator do Centro de sistema

A Microsoft fornece [os pacotes de integração](http://technet.microsoft.com/library/hh295851.aspx) para criar runbooks para automatizar componentes do Centro de sistema e outros produtos.  Alguns destes packs integração atualmente são baseados no OIT mas atualmente não podem ser convertidos para módulos de integração devido a problemas conhecidos.  [Módulos de integração do sistema Centro Orchestrator](https://www.microsoft.com/download/details.aspx?id=49555) inclui convertidos versões destes packs integração que podem ser importados de automatização do Azure e automatização de gestão de serviço.  

Pela versão RTM desta ferramenta, serão publicadas atualizadas versões dos packs integração com base em OIT que pode ser convertido com o conversor do pacote de integração.  Também serão fornecidas para ajudá-lo a converter runbooks utilizando as atividades dos pacotes de integração não baseados no OIT orientações.

## <a name="runbook-converter"></a>Livro execuções conversor

O livro execuções conversor converte Orchestrator runbooks [runbooks gráficos](automation-runbook-types.md#graph-runbooks) que pode ser importado Azure automatização.  

Conversor de livro execuções é implementada como um módulo do PowerShell com um cmdlet denominado **ConvertFrom SCORunbook** que efetua a conversão.  Quando instalar a ferramenta, irá criar um atalho de uma sessão de PowerShell que carrega o cmdlet.   

Segue-se o processo básico para converter um livro de execuções Orchestrator e importe-o para automatização Azure.  As secções seguintes fornecem mais detalhes sobre como utilizar a ferramenta e trabalhar com runbooks convertidos.

1. Exporte um ou mais runbooks do Orchestrator.
2. Obter módulos de integração para todas as atividades no livro de execuções.
3. Converta o runbooks Orchestrator no ficheiro exportado.
4. Reveja as informações nos registos para validar a conversão e para determinar as tarefas manuais necessárias.
4. Importe runbooks convertido para automatização Azure.
5. Crie qualquer elementos necessários no Azure automatização.
6. Edite o livro de execuções no Azure automatização para modificar quaisquer actividades necessárias.

### <a name="using-runbook-converter"></a>Utilizar o livro execuções conversor

A sintaxe para **ConvertFrom SCORunbook** é da seguinte forma:

    ConvertFrom-SCORunbook -RunbookPath <string> -Module <string[]> -OutputFolder <string> 

- RunbookPath - caminho para o ficheiro de exportação que contém o runbooks para converter.
- Módulo - delimitado por vírgulas lista de módulos de integração que contém as atividades a runbooks.
- OutputFolder - o caminho para a pasta para criar convertidas em runbooks gráfica. 


O comando de exemplo seguinte converte runbooks num ficheiro de exportação denominado **MyRunbooks.ois_export**.  Estes runbooks Utilize pacotes de integração com o Active Directory e o Gestor de proteção de dados.

    ConvertFrom-SCORunbook -RunbookPath "c:\runbooks\MyRunbooks.ois_export" -Module c:\ip\SystemCenter_IntegrationModule_ActiveDirectory.zip,c:\ip\SystemCenter_IntegrationModule_DPM.zip -OutputFolder "c:\runbooks" 


### <a name="log-files"></a>Ficheiros de registo

O livro execuções conversor irá criar os ficheiros de registo seguinte na mesma localização como livro de execuções convertido.  Se os ficheiros já existirem, em seguida, estas serão substituídas com informações da última conversão.

| Ficheiro | Conteúdo |
|:---|:---|
| Conversor do livro execuções - Progress | Passos detalhados da conversão dos incluindo as informações para cada actividade convertida com êxito e aviso para cada actividade não convertidas em. |
| Conversor do livro execuções - Summary.log  | Resumo da conversão dos última, incluindo todos os avisos e dar seguimento tarefas que precisa para executar como criar uma variável de necessários para o livro de execuções convertido.  |

### <a name="exporting-runbooks-from-orchestrator"></a>Exportar runbooks Orchestrator

O livro execuções conversor funciona com um ficheiro de exportação de Orchestrator que contém uma ou mais runbooks.  -Lo irá criar um livro de execuções do Azure automatização correspondente para cada livro de execuções Orchestrator no ficheiro de exportação.  

Para exportar um livro de execuções do Orchestrator, com o botão direito no nome do livro de execuções no Designer de livro execuções e selecione **Exportar**.  Para exportar todos os runbooks numa pasta, com o botão direito no nome da pasta e selecione **Exportar**.


### <a name="runbook-activities"></a>Livro execuções atividades

O livro execuções conversor converte cada atividade no livro de execuções Orchestrator uma atividade correspondente no Azure automatização.  As atividades que não podem ser convertidas, é criada uma atividade de marcador de posição no livro de execuções com texto de aviso.  Depois de importar livro de execuções convertido para automatização Azure, tem de substituir qualquer uma das seguintes atividades com as atividades válidas que executar a funcionalidade necessária.

Serão convertidas em outras atividades Orchestrator no [Módulo padrão de atividades](#standard-activities-module) .  Existem algumas atividades Orchestrator padrão que não estão neste módulo apesar e não são convertidas.  Por exemplo, **Enviar evento da plataforma** tem sem automatização Azure equivalente, uma vez que o evento é específico de Orchestrator.

[Atividades do monitor](https://technet.microsoft.com/library/hh403827.aspx) não são convertidos uma vez que não haja nenhuma equivalente aos mesmos em automatização Azure.  A exceção são monitor atividades no [convertidos pacotes de integração](#integration-pack-converter) que será convertido na atividade de marcador de posição.

Será convertido em qualquer atividade a partir de um [convertidos pack integração](#integration-pack-converter) se fornecer o caminho para o módulo de integração com o parâmetro **módulos** .  Para pacotes de integração com o Centro de sistema, pode utilizar os [Módulos de integração do sistema Centro Orchestrator](#system-center-orchestrator-integration-modules).


### <a name="orchestrator-resources"></a>Recursos Orchestrator

O livro execuções conversor converte apenas runbooks, não outros recursos de Orchestrator como contadores, variáveis ou ligações.  Contadores não são suportadas no Azure automatização.  Variáveis e ligações são suportadas, mas terá de as criar manualmente.  Os ficheiros de registo irão informá-lo se necessitar de livro de execuções desses recursos e especificar recursos correspondentes que precisa para criar no Azure automatização para o livro de execuções convertido funcionar corretamente.

Por exemplo, um livro de execuções pode utilizar uma variável para preencher um determinado valor uma actividade.  Livro de execuções convertido irá converter a atividade e especificar um activo variável no Azure automatização com o mesmo nome que a variável de Orchestrator.  Será indicado no ficheiro de **Livro execuções conversor - Summary.log** que é criado após a conversão.  Terá de criar manualmente esta variável ativo no Azure automatização antes de utilizar o livro de execuções. 

 
### <a name="input-parameters"></a>Parâmetros de entrada

Runbooks no Orchestrator aceitar parâmetros de entrada com a atividade de **Inicialização dados** .  Se o livro de execuções a ser convertidas em incluir este atividade, é criado um [parâmetro de entrada](automation-graphical-authoring-intro.md#runbook-input-and-output) no livro de execuções Azure automatização para cada parâmetro na atividade de.  Uma atividade de [controlo de Script de fluxo de trabalho](automation-graphical-authoring-intro.md#activities) é criada no livro de execuções convertido que obtém e devolve cada parâmetro.  Qualquer atividades no livro de execuções que utilizar um parâmetro de entrada referir-se para o resultado desta atividade.

O motivo que é utilizada esta estratégia é melhor aproximadamente a funcionalidade do livro de execuções Orchestrator.  Atividades no novo runbooks gráfica devem se referem diretamente à utilizar uma origem de dados de entrada do livro execuções de parâmetros de entrada.


### <a name="invoke-runbook-activity"></a>Invocar livro execuções atividade

Runbooks no Orchestrator comece outras runbooks com a atividade de **Livro de execuções invocar** . Se o livro de execuções a ser convertidas em incluir esta actividade e a opção **aguardar pela conclusão** estiver definida, uma atividade de livro execuções é criada para o mesmo no livro de execuções convertido.  Se a opção **aguardar pela conclusão** não estiver definida, em seguida, uma atividade de Script de fluxo de trabalho é criada que utiliza **AzureAutomationRunbook iniciar** para iniciar o livro de execuções.  Depois de importar livro de execuções convertido para automatização Azure, tem de modificar esta actividade com as informações especificadas na atividade de.




## <a name="related-articles"></a>Artigos relacionados

- [Sistema Centro 2012 - Orchestrator](http://technet.microsoft.com/library/hh237242.aspx)
- [Automatização de gestão de serviços](https://technet.microsoft.com/library/dn469260.aspx)
- [Trabalhador de livro execuções híbrido](automation-hybrid-runbook-worker.md)
- [Atividades normais Orchestrator](http://technet.microsoft.com/library/hh403832.aspx)
- [Toolkit de migração do Centro de sistema transferências Orchestrator](https://www.microsoft.com/en-us/download/details.aspx?id=47323)
 
