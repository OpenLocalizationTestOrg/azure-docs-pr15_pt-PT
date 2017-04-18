
<properties 
   pageTitle="Notas de lançamento do Azure SDK para .NET 2.7 e .NET 2.7.1" 
   description="Notas de lançamento do Azure SDK para .NET 2.7 e .NET 2.7.1" 
   services="app-service\web" 
   documentationCenter=".net" 
   authors="Juliako" 
   manager="erikre" 
   editor=""/>

<tags
   ms.service="app-service"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="10/17/2016"
   ms.author="juliako"/>

# <a name="azure-sdk-for-net-27-and-net-271-release-notes"></a>Notas de lançamento do Azure SDK para .NET 2.7 e .NET 2.7.1

##<a name="overview"></a>Descrição geral

Este documento contém as notas de lançamento para o SDK do Azure para .NET 2.7 lançamento. 

O documento contém também as notas de lançamento para o SDK do Azure para .NET 2.7.1 liberte.

Azure SDK 2.7 só é suportada no Visual Studio 2015 e no Visual Studio 2013. [Azure SDK 2.6](https://azure.microsoft.com/downloads/) é que a última suportada SDK para Visual Studio 2012.

Para obter informações detalhadas sobre esta versão, consulte o artigo [anúncio Azure SDK 2.7 publicar](https://azure.microsoft.com/blog/2015/07/20/announcing-the-azure-sdk-2-7-for-net/) e [Registar do Azure SDK 2.7.1 anúncio](http://go.microsoft.com/fwlink/?LinkId=623850).

##<a name="azure-sdk-for-net-27"></a>Azure SDK para .NET 2.7

###<a name="sign-in-improvements-for-visual-studio-2015"></a>Inicie sessão no melhoramentos para Visual Studio 2015

Azure SDK 2.7 para Visual Studio 2015 suporta as novas funcionalidades de gestão de identidades no Visual Studio 2015.  Isto inclui suporte para contas de aceder ao Azure através do controlo de acesso com base da função, fornecedores de solução na nuvem, DreamSpark e outros tipos de conta e de subscrição.

As melhorias de início de sessão no incluído com o Azure SDK 2.7 só estão disponíveis no Visual Studio 2015. Suporte para Visual Studio 2013 é incluído no Azure SDK 2.7.1.


###<a name="mobile-sdk"></a>SDK móvel

Atualizado modelos de **Aplicações móveis** para refletir o processo e configuração do [pacote NuGet](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) mais recente.

###<a name="service-bus"></a>Serviço Bus 

Correções de erros gerais e melhorias. Para obter informações detalhadas sobre atualizações e funcionalidades, consulte as notas de lançamento do [Serviço Bus NuGet](http://www.nuget.org/packages/WindowsAzure.ServiceBus/)mais recente.

###<a name="hdinsight-tools"></a>Ferramentas de HDInsight 

Nesta versão, as seguintes atualizações foram feitas. Estas atualizações são na pré-visualização. Para mais informações, consulte o artigo [neste blogue](http://go.microsoft.com/fwlink/?LinkId=619108).

- Ramo de gráficos para ramo de trabalhos Tez
- Suporte de Hive IntelliSense LMG completo
- Suporte de modelo porco
- Modelos de tempestade para serviços do Azure

####<a name="breaking-changes"></a>Força de alterações

- Projecto **tempestade** antigo tem de ser actualizado quando utilizar esta versão das ferramentas. Para mais informações, consulte o artigo [neste blogue](http://go.microsoft.com/fwlink/?LinkId=619108).
- Visual Studio Web Express já não é suportada. Para mais informações, consulte o artigo [neste blogue](http://go.microsoft.com/fwlink/?LinkId=619108).

###<a name="azure-app-service-tools"></a>Ferramentas do Azure de aplicação de serviço

Nesta versão as seguintes atualizações foram feitas extensões de ferramentas da Web. Para obter mais informações consulte o artigo [neste](https://azure.microsoft.com/blog/2015/07/20/announcing-the-azure-sdk-2-7-for-net/) blogue. 

- Suporte para contas DreamSpark adicionadas
- Alteração completa para ferramentas do Azure feita para suportar o novo APIs de gestão de recursos Azure
- Suporte para a aplicação de serviço de Azure adicionados à [Nuvem Explorer](#cloud_explorer)

####<a name="known-issues"></a>Problemas conhecidos

Nós de ranhura de implementação do Web App não forem apresentadas sob o nó faixas no Explorador do servidor e não carregam nós subordinados do Web App implementação ranhura em nuvem Explorer. Este problema foi resolvido e preparado para a próxima versão SDK. 


###<a name="cloud_explorer"></a>Nuvem Explorer para Visual Studio 2015

Azure SDK 2.7 inclui Explorer na nuvem para de 2015 Visual Studio que permite-lhe ver os recursos do Azure, inspecionar as respectivas propriedades e executar ações de programador chave a partir do Visual Studio. 

Explorador de nuvem suporta o seguinte procedimento:

- Grupo de recursos e tipo de recurso vistas dos seus recursos Azure 
- Procurar recursos pelo nome (disponível na vista de tipo de recurso)
- Suporte para subscrições e recursos que têm a função com base no Access controlo RBCA () aplicada 
- Painel ação integrada que mostra com foco nos programador ações específicas para recursos selecionados. Por exemplo: Anexar Depurador remoto para máquinas virtuais criada utilizando a pilha de Gestor de recursos, ver dados de diagnóstico de máquinas virtuais etc.
- Painel de propriedades integrado que mostra as propriedades com foco nos programador frequentemente necessárias durante o ensaio/Dev Center 
- Mudar rápida da conta a utilizar quando enumerar recursos (utilize o comando definições na barra de ferramentas) 
- A filtragem de subscrições para utilizar quando enumerar recursos (utilize o comando definições na barra de ferramentas) 
- Ligações abrangente portal de gestão de recursos e grupos de recursos do Azure 
 
 
###<a name="azure-resource-manager-tools"></a>Ferramentas de Gestor de recursos do Azure 

As ferramentas de Gestor de recursos do Azure foram actualizadas para trabalhar com a função com base no Access controlo RBCA () e os novos tipos de subscrição.  A capacidade de utilizar novas contas de armazenamento, para além de armazenamento clássico, para armazenar artefactos durante a implementação está incluída com estas alterações.  

Se estiver a utilizar um projeto de grupo de recursos do Azure a partir de uma versão anterior do SDK com a 2.7 SDK, é necessário um novo script de implementação para implementar a utilizar uma nova conta de armazenamento em vez de armazenamento clássico.  Será pedido antes de forem efetuadas alterações ao seu projeto para adicionar o novo script.  Vai ser mudado o script antigo e terá efetue quaisquer modificações para o novo script manualmente.
 
 
###<a name="storage-explorer-tools"></a>Ferramentas do Explorador de armazenamento 

- Suporte para a visualização Blobs acrescentar. Mais informações nesta [mensagem no blogue](http://blogs.msdn.com/b/windowsazurestorage/archive/2015/04/13/introducing-azure-storage-append-blob.aspx). 
- Suporte para a visualização de contas de armazenamento de Premium através do Explorador do servidor. Servidor Explorer irá apresentar apenas as blobs de página para contas de armazenamento premium como estão o único tipo suportado para contas de armazenamento premium.

###<a name="azure-data-factory-tools-for-visual-studio"></a>Ferramentas de fábrica do Azure dados para o Visual Studio 

Introdução ao **Azure dados fábrica ferramentas** para o Visual Studio. Seguem-se as funcionalidades ativadas. Consulte o artigo [neste blogue](http://go.microsoft.com/fwlink/?LinkId=617530) para obter mais informações.

- **Criação de modelo com base**: com base selecione caso utilize modelos, os modelos de dados movimento ou modelos de processamento de dados para implementar uma solução de integração de dados de ponto a ponto e comece a trabalhar práticos rapidamente com a fábrica de dados. 
- **Integração com o Explorador de solução para a criação e implementar entidades fábrica de dados**: criar e implementar o tubagens e entidades relacionadas, como projetos do Visual Studio. 
- **Ver a integração com o diagrama para interação visual enquanto criação**: visualmente autor tubagens e conjuntos de dados com o auxiliar a partir da vista de diagrama. 
- **Integração com o Explorador de servidor para navegação e de interação com entidades já implementadas**: tirar partido de Explorador do servidor para procurar já implementada dados fábricas e entidades correspondentes. Importe uma fábrica dados implementado ou qualquer entidade (em curso, serviço ligadas, conjuntos de dados) para o seu projeto. 
- **JSON de edição com a validação de esquema e rich intellisense**: eficazmente configurar e editar documentos do JSON de entidades fábrica de dados com a validação de esquemas e intellisense avançada 
- **Publicação com várias ambiente**: publicar criados tubagens Dev Center, teste ou ambiente ordem de produção por criar ficheiros config separada para cada ambiente.
- **Porco, ramo e .net com base no processamento de dados de suporte**: suporte para porco e Hive Scripts no project fábrica de dados. Suporte para referenciar c# Project para gerir .net atividade.

##<a name="azure-sdk-for-net-271"></a>Azure SDK para .NET 2.7.1

A secção seguinte contém as atualizações que foram introduzidas com o SDK do Azure para .NET 2.7.1 liberte.
###<a name="hdinsight-tools"></a>Ferramentas de HDInsight 

Para obter mais detalhada explicação sobre as atualizações de ferramentas do HDInsight, consulte o artigo [neste blogue](http://go.microsoft.com/fwlink/?LinkId=623831).

- Ramo de tarefa operador vista (uma nova funcionalidade)

    Para ajudar a compreender a sua consulta ramo melhor, a vista de operador Hive funcionalidade foi adicionada. Para ver todos os operadores dentro de um vértice, faça duplo clique no vértices do gráfico, tarefa. Para ver os detalhes mais de um operador específico, paire sobre esse operador.
- Ramo de marcador de erro (uma nova funcionalidade)

    Instantaneamente, para que possa ver os erros de gramática foi adicionada a funcionalidade de ramo de marcador de erro. Além disso, mensagens de erro foram avançadas e agora, pode ver erros de gramática detalhadas instantaneamente (até neste lançamento, tinha que submeter um script Hive para cluster e aguarde algum tempo até que a obter detalhes sobre a mensagem de erro).  
- Gráfico de topologia tempestade (uma nova funcionalidade)

    Visualizar é muito importante quando quiser ver se a topologia de está a funcionar como esperado. Nesta versão adicionámos de visualização para gráficos de tempestade. Pode visualizar as métricas importantes para a sua topologia (por exemplo, uma cor indica Meteorologia um determinado raio é "Estado ocupada" ou não). Pode também faça duplo clique a raio vareta para ver mais detalhes.

- Suporte para clusters HDInsight que foram criadas no Portal do Azure (corrigir um erro)

    Agora pode utilizar o Visual Studio para ver e submeter as tarefas para todos os seus clusters HDInsight independentemente de onde o cluster foram criadas.

- Mais suporte IntelliSense & carregamento mais rápido Hive metadados (melhoramento da)

    Vamos foram melhorados o IntelliSense ao adicionar mais sugestões de utilizador amigável. Por exemplo, alias de tabela pode agora também serão sugeridos em IntelliSense para que a sua consulta pode escrever mais facilmente. Além disso, podemos foram melhorados os metadados Hive para que apenas irá demorar vários segundos para listar todas as bases de dados, tabelas e colunas do seu metastore ramo a ser carregados.

Para obter mais detalhada explicação sobre as atualizações de ferramentas do HDInsight, consulte o artigo [neste blogue](http://go.microsoft.com/fwlink/?LinkId=623831).

###<a name="improvements-in-visual-studio-2013"></a>Melhorias no Visual Studio 2013

- Azure SDK 2.7.1 permite Visual Studio 2013 aceder a contas do Azure e subscrições através de controlo de acesso com base da função, fornecedores de solução na nuvem e Dreamspark.
- Com o Azure SDK 2.7.1, a nova janela de ferramenta nuvem Explorer agora também está disponível no Visual Studio 2013.

###<a name="known-issues"></a>Problemas conhecidos

Instalar o 2.6 SDK Azure ou 2.7.1 para Visual Studio Comunidade 2013 num não - inglês SO irá apresentar um aviso que podem ser incompatíveis os recursos de inglês e não inglesa do Visual Studio. Este aviso pode ser rejeitado em segurança. Só ocorrerá se o computador não continha uma instalação anterior do Visual Studio Comunidade 2013 e estiver a instalar o SDK num não - inglês SO. O aviso é apresentado depois do pacote de idiomas aplica-se os recursos RTM para Visual Studio, mas antes de aplica-se Update 4. Dispensar o aviso de permitir que o pacote de idiomas continuar e concluir a aplicar a versão de 4 de atualização do conteúdo do pacote de idiomas.

Projetos LightSwitch não estão compatibile com esta versão. Este problema vai ser resolvido com a versão SDK seguinte.

##<a name="also-see"></a>Consulte também
[Azure SDK 2.7.1 post anúncio de nascimento](http://go.microsoft.com/fwlink/?LinkId=623850)

[Mensagem de anúncio SDK 2.7 Azure](https://azure.microsoft.com/blog/2015/07/20/announcing-the-azure-sdk-2-7-for-net/)

[Suporte e informações de reforma para o SDK do Azure para APIs e .NET](https://msdn.microsoft.com/library/azure/dn479282.aspx/)
