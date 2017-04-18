<properties
   pageTitle="O que é o Azure Analysis Services | Microsoft Azure"
   description="Visão geral do Analysis Services no Azure."
   services="analysis-services"
   documentationCenter=""
   authors="minewiskan"
   manager="erikre"
   editor=""
   tags=""/>
<tags
   ms.service="analysis-services"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="na"
   ms.date="10/25/2016"
   ms.author="owend"/>

# <a name="what-is-azure-analysis-services"></a>O que é o Azure Analysis Services?
![Azure Analysis Services](./media/analysis-services-overview/aas-overview-aas-icon.png)

Criado no motor de análise provas dadas em Microsoft SQL Server Analysis Services, Azure Analysis Services fornece dados empresarial modelação na nuvem.

> [AZURE.IMPORTANT] Azure Analysis Services está na **pré-visualização**. Existem algumas coisas que apenas não estão a funcionar ainda. Certifique-se de que dar saída [expectativas de pré-visualização](#preview-expectations) neste artigo. E, certifique-se de que atento nosso [blogue Azure Analysis Services](https://go.microsoft.com/fwlink/?linkid=830920) para as informações mais recentes.

## <a name="built-on-sql-server-analysis-services"></a>Criada com base em SQL Server Analysis Services
Azure Analysis Services é compatível com o mesmo SQL Server 2016 Analysis Services Enterprise Edition que já conhece. Azure Analysis Services suporta modelos em tabela ao nível de compatibilidade de 1200. DirectQuery, partições, segurança de nível de linha, bidirecional relações e traduções são suportadas.

## <a name="use-the-tools-you-already-know"></a>Utilize as ferramentas que já conhece
![Ferramentas de BI de programador](./media/analysis-services-overview/aas-overview-dev-tools.png)

Quando criar modelos de dados para o Azure Analysis Services, utilizar as mesmas ferramentas que para SQL Server Analysis Services. Criar e implementar modelos de tabela utilizando as versões mais recentes do [SQL Server dados Tools (SSDT)](https://msdn.microsoft.com/library/mt204009.aspx) ou ao utilizar modelos [Powershell do Azure](../powershell-install-configure.md) e [Gestor de recursos do Azure](../azure-resource-manager/resource-group-overview.md) do [SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx).

## <a name="connect-to-data-sources"></a>Ligar a origens de dados
Modelos de dados implementado servidores no suporte Azure ligar a dados origens no local na sua organização ou na nuvem. Combinar dados a partir de ambas no local e na nuvem origens de dados para um híbrido solução de BI.

![Origens de dados](./media/analysis-services-overview/aas-overview-data-sources.png)

Porque o servidor está na nuvem, é totalmente integrada ligar a origens de dados na nuvem. Ao ligar a origens de dados no local, o [gateway de dados no local](analysis-services-gateway.md) garante rapidamente, ligações seguras com o seu servidor de Analysis Services na nuvem.  

 \*Algumas origens de dados não são suportadas ainda na pré-visualização. Para saber mais, consulte o artigo [expectativas de pré-visualização](#preview-expectations) neste artigo.

## <a name="explore-your-data-from-anywhere"></a>Explorar os seus dados a partir de qualquer lugar
Ligar e [obter dados](analysis-services-connect.md) a partir dos servidores do sobre qualquer lugar. Azure Analysis Services suporta a ligação a partir do Power BI Desktop, Excel, aplicações personalizadas e ferramentas com base no browser.

![Visualizações de dados](./media/analysis-services-overview/aas-overview-visualization.png)

 \*Power BI incorporado ainda não é suportado na pré-visualização.

## <a name="secure"></a>Seguro

#### <a name="user-authentication"></a>Autenticação de utilizador
Autenticação de utilizador para o Azure Analysis services é processada pelo [Azure Active Directory (AAD)](../active-directory/active-directory-whatis.md). Ao tentar iniciar sessão numa base de dados do Azure Analysis Services, os utilizadores utilizar uma identidade de conta de organização com acesso à base de dados que estão a tentar aceder. Estas identidades do utilizador tem de ser membros do Azure Active Directory predefinido para a subscrição onde reside o servidor do Azure Analysis Services. [Integração de diretórios](https://technet.microsoft.com/library/jj573653.aspx) entre AAD e no local Active Directory é uma excelente forma de obter suas no local acesso de utilizadores a uma base de dados do Azure Analysis Services, mas não é necessário para todos os cenários.

Os utilizadores iniciar sessão com o nome principal de utilizador (UPN) da sua conta e respetiva palavra-passe. Quando sincronizadas com um Active Directory no local, UPN o utilizador é frequentemente respetivo endereço de e-mail organizacional.

Permissões para gerir o recurso de servidor do Azure Analysis Services são processadas pelo atribuir utilizadores a funções de dentro da sua subscrição do Azure. Por predefinição, os administradores de subscrição tem permissões de proprietário para o recurso de servidor no Azure. Utilizadores adicionais podem ser adicionados ao utilizar o Gestor de recursos do Azure.

#### <a name="data-security"></a>Segurança dos dados
Azure Analysis Services utiliza armazenamento de Blobs do Azure persistir armazenamento e de metadados para bases de dados do Analysis Services. Ficheiros de dados numa Blob são encriptados utilizando Azure Blob Server lado encriptação (SSE). Quando utilizar o modo de consulta direta, apenas os metadados está armazenado; os dados reais são acedidos a partir da origem de dados no momento da consulta.

#### <a name="on-premises-data-sources"></a>Origens de dados no local
Proteger o acesso aos dados que residem no local na sua organização pode ser realizado pelos instalar e configurar um [gateway de dados no local](analysis-services-gateway.md). Gateways fornecem acesso aos dados para consulta direta e modos de na memória. Quando um modelo de Azure Analysis Services se liga a uma origem de dados no local, é criada uma consulta juntamente com as credenciais encriptadas para a origem de dados no local. O serviço de nuvem gateway analisa a consulta e envia o pedido para um Bus de serviço do Azure. O gateway no local consulta o Azure Service Bus para pedidos de pendentes. O gateway, em seguida, obtém a consulta, desencripta as credenciais e liga à origem de dados de execução. Os resultados, em seguida, são enviados a partir da origem de dados, novamente para o gateway e, em seguida, para a base de dados do Azure Analysis Services.

Azure Analysis Services é regido pelos [Termos do Microsoft Online Services](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31) e a [Declaração de privacidade do Microsoft Online Services](https://www.microsoft.com/privacystatement/OnlineServices/Default.aspx).
Para saber mais sobre a segurança do Azure, consulte o [Centro de fidedignidade do Microsoft](https://www.microsoft.com/trustcenter/Security/AzureSecurity).

## <a name="get-help"></a>Obter ajuda
Azure Analysis Services é simple para configurar e gerir. Pode encontrar todas as informações que necessita para criar e gerir um servidor aqui. Ao criar um modelo de dados para implementar ao seu servidor, é muito a mesma tal como está para criar um modelo de dados que implementar a um servidor no local. Existe uma biblioteca de conceptuais, procedimentos, tutoriais e artigos de referência no [Analysis Services no MSDN](https://msdn.microsoft.com/library/bb522607.aspx)extensa.

Recomendamos também têm um número de vídeos úteis no [Azure Analysis Services no canal 9](https://channel9.msdn.com/series/Azure-Analysis-Services).

Coisas estão a alterar rapidamente. Pode sempre obter as informações mais recentes no [blogue do Azure Analysis Services](https://go.microsoft.com/fwlink/?linkid=830920).

## <a name="community"></a>Comunidade
Do Analysis Services tem uma fascinantes Comunidade de utilizadores. Participar na conversação no [Fórum do Azure Analysis Services](https://aka.ms/azureanalysisservicesforum).

## <a name="feedback"></a>Comentários
Ter sugestões ou pedidos de funcionalidades? Certifique-se de que deixe os seus comentários no [Azure Analysis Services comentários](https://aka.ms/azureanalysisservicesfeedback).

Sugestões sobre a documentação de ter? Pode adicionar comentários utilizando Disqus na parte inferior de cada artigo.

## <a name="preview-expectations"></a>Pré-visualização expectativas
Azure Analysis Services atualmente está na pré-visualização. Existem algumas coisas que deve ter em mente.

##### <a name="server-modes"></a>Modos de servidor
Azure Analysis Services suporta atualmente modo Tabular para modelos em tabela ao nível de compatibilidade de 1200. Modo multidimensional e Data Mining e Power Pivot para o modo do SharePoint não são suportadas.

##### <a name="data-sources"></a>Origens de dados
Para pré-visualizar, as seguintes origens de dados são suportadas modelos em tabela 1200 implementados um servidor do Azure Analysis Services.

| **Nuvem** | **No local** |
|--------------|------------|
| Base de dados SQL | Do SQL Server |
| Armazém de dados SQL | APPS |
|      | Oracle |
|       | Teradata |


### <a name="data-source-providers"></a>Fornecedores de origem de dados
Modelos de dados no Azure Analysis Services precisem de fornecedores de dados diferente ligar a origens de dados que modelos de dados SQL Server Analysis Services. Requisitos de fornecedor de dados dependem a origem de dados ser na nuvem ou no local e o tipo de modelo de dados Consulta na memória ou direta. Para saber mais, consulte [as ligações de origem de dados](analysis-services-datasource.md).


### <a name="client-connections"></a>Ligações de cliente
Power BI incorporado ainda não é suportado na pré-visualização.

Livros do Excel com ligações direto para um servidor do Azure Analysis Services e guardado no OneDrive ou SharePoint Online não são suportados.



## <a name="next-steps"></a>Próximos passos
Agora que sabe mais sobre o Azure Analysis Services, é hora para começar a utilizar. Saiba como [criar um servidor](analysis-services-create-server.md) no Azure e [Implementar um modelo de tabela](analysis-services-deploy.md) à mesma.
