
<properties 
   pageTitle="Azure SDK .NET 2,8 notas de lançamento" 
   description="Azure SDK .NET 2,8 notas de lançamento" 
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
 
# <a name="azure-sdk-for-net-28-281-and-282"></a>Azure SDK para .NET 2,8, 2.8.1 e 2.8.2

##<a name="overview"></a>Descrição geral
 
Este artigo contém as notas de lançamento (que inclui problemas conhecidos e alterações de última hora) para o SDK do Azure para .NET 2,8, 2.8.1 e. 2.8.2 liberta. 

Para a lista completa das novas funcionalidades e feitas nesta versão, consulte o anúncio [Azure SDK 2,8 para Visual Studio 2013 e para Visual Studio 2015](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/) . 

##  <a name="azure-sdk-for-net-28"></a>Azure SDK para .NET 2,8

### <a name="download-azure-sdk-for-net-28"></a>Transferir o Azure SDK para .NET 2,8

[Azure SDK para .NET 2,8 para Visual Studio 2015](http://go.microsoft.com/fwlink/?LinkId=699285) 

[Azure SDK para .NET 2,8 para Visual Studio 2013](http://go.microsoft.com/fwlink/?LinkId=699287)
 
### <a name="net-452-support"></a>Suporte do .NET 4.5.2 

####<a name="known-issues"></a>Problemas conhecidos

Azure .NET SDK 2,8 permite-lhe criar .NET 4.5.2 pacotes de serviço na nuvem. No entanto 4.5.2 do .NET framework não será instalada na predefinição solte imagens SO convidado até Janeiro de 2016 SO convidado. Antes desse, 4.5.2 .NET framework estará disponível através de uma versão de lançamento SO convidado separada – Novembro 02 de 2015. Ver a página [Azure convidado SO lançamentos e da matriz de compatibilidade de SDK](../cloud-services/cloud-services-guestos-update-matrix.md) para controlar quando a imagem será disponibilizada.  Assim que a imagem de 2015 02 Novembro é disponibilizada pode optar por utilizar essa imagem ao atualizar o seu ficheiro de ficheiro (.cscfg) de configuração do serviço na nuvem. Na configuração do serviço de ficheiro defina o atributo osVersion do elemento ServiceConfiguration com a cadeia "WA-convidado-SO-4.26_201511-02". Se optar por optar ativamente por participar no utilizar esta imagem, em seguida, já não irá obter as atualizações automáticas do SO convidado. Para obter as atualizações automáticas a osVersion deve ser definido como "*" e .NET 4.5.2 só estará disponível através de atualizações automáticas no Janeiro de 2016.

###<a name="azure-data-factory"></a>Dados Azure fábrica

####<a name="known-issues"></a>Problemas conhecidos 

Durante a criação de projeto de **Modelo de fábrica do mesmo de dados** que envolvam dados de exemplo, o script de shell de azure power poderá falhar se a versão de shell de azure power instalado no computador for após 0.9.8.

Para poder criar com êxito este tipo de projeto, tem de instalar a [versão da shell de azure power 0.9.8](https://github.com/Azure/azure-powershell/releases/download/v0.9.8-September2015/azure-powershell.0.9.8.msi).


### <a name="azure-resource-manager-tools"></a>Ferramentas de Gestor de recursos do Azure 

####<a name="breaking-changes"></a>Força de alterações

O script do PowerShell fornecido pelo projeto de grupo de recursos do Azure foi atualizado neste lançamento para trabalhar com os cmdlets do Azure PowerShell novos versão 1.0.  Este novo script não irá funcionar com Visual Studio quando utilizar uma versão do SDK antes de 2,8.  

Scripts de projetos criados em versões anteriores do SDK não serão executada a partir do Visual Studio ao utilizar o SDK 2,8.  Todos os scripts irão continuar a trabalhar fora do Visual Studio com a versão adequada dos cmdlets do Azure PowerShell.  

O SDK 2,8 requer versão 1.0 dos cmdlets do Azure PowerShell.  Todas as outras versões do SDK necessitam do Microsoft 0.9.8 os cmdlets do Azure PowerShell.  Para obter mais informações consulte o artigo [neste](http://go.microsoft.com/fwlink/?LinkID=623011) blogue.

###<a name="web-tools-extensions"></a>Extensões nas ferramentas da Web

####<a name="known-issues"></a>Problemas conhecidos

Os seguintes problemas conhecidos serão endereçados na versão seguinte.

- Aplicação relacionados com o serviço na nuvem e Server Explorer gesto para não produção ambientes (como Azure China ou os clientes de pilha de Azure) não funcionam. Para clientes nestas áreas afetada, transferir o perfil de publicar a partir do portal Azure permitirá a capacidade de publicação. Um lançamento futuro irá reparar gestos como "Anexar depurador" e "Ver registos transmissão" Azure China e clientes de pilha. 
- Os clientes podem ver erros durante a aplicação de serviço de criação quando a aplicação de informações da instância que são implementar está numa região diferente de Leste dos EUA. Nestes cenários, criar uma aplicação de serviço no portal do e transferir o perfil de publicar permitirão cenários de publicação. 

###<a name="azure-hdinsight-tools"></a>Ferramentas de Azure HDInsight

####<a name="new-updates"></a>Novas atualizações

- Pode executar a consulta de ramo no cluster através do HiveServer2 com quase nenhum sobrecarga e ver que a tarefa de registos em tempo real.
- Utilizar a nova Hive execução vista de tarefa pode abordar o seu trabalho mais aprofundado, encontrar mais detalhes e identificar possíveis problemas.

Para obter informações, consulte o artigo [Azure SDK 2,8 para Visual Studio 2013 e para Visual Studio 2015](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/). 

## <a name="azure-sdk-for-net-281"></a>Azure SDK para .NET 2.8.1

### <a name="known-issues-for-visual-studio-2013-and-visual-studio-2015"></a>Problemas conhecidos para Visual Studio 2013 e para Visual Studio 2015
 
1. WebJob acionada publica faixas irá mostrar e erro e não definir uma agenda, mas irá transmitir a WebJob para Azure. Os clientes que estiverem que precisam de uma tarefa agendada, em seguida, podem utilizar o Portal do Azure para configurar a agenda para o WebJob. 
2. Clientes Python podem ocorrer problemas de depurador. Equipa de suporte é gradual saída um fix para este mas se são afetados clientes, informe Microsoft souber nos fóruns do ou no blogue do anúncio ou libertar a secção de comentários de notas. 
3. Os clientes em certas regiões (por exemplo, Sul Índia) serão experimentar a aplicação de serviço de erros de aprovisionamento. Este é compatível com o portal e clientes que experiência este problema podem utilizar o portal do Azure para pedir acesso a publicar estas regiões geo. Uma vez que pedem acesso a estas regiões utilizando o Azure portal de aprovisionamento deve funcionam. 

##<a name="azure-sdk-for-net-282"></a>Azure SDK para .NET 2.8.2

Após a instalação do 2.8.2 ferramentas, clientes podem experimentar o problema que se segue.         

- Se estiver a utilizar o Windows 10 e não tiver instalado o Internet Explorer, obterá um erro "Não foi possível encontrar Internet Explorer".
Para resolver o problema, instale o Internet Explorer utilizando a caixa de diálogo Adicionar/remover componentes do Windows.

Se observar este problema, utilize a funcionalidade de enviar um sorriso para comunicar a situação.

Para mais informações, consulte o artigo [Este](https://azure.microsoft.com/blog/announcing-azure-sdk-2-8-2-for-net/) publicar.
##<a name="other-updates"></a>Outras atualizações

Para outras atualizações, consulte o artigo [anúncio Azure SDK 2,8 publicar](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/).

##<a name="also-see"></a>Consulte também

[Mensagem de anúncio SDK 2,8 Azure](https://azure.microsoft.com/blog/announcing-the-azure-sdk-2-8-for-net/)

[Suporte e informações de reforma para o SDK do Azure para APIs e .NET](https://msdn.microsoft.com/library/azure/dn479282.aspx)

