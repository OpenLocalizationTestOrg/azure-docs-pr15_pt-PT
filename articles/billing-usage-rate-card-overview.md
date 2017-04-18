<properties
   pageTitle="Obtenha informações para o consumo de recursos do Microsoft Azure | Microsoft Azure"
   description="Fornece uma descrição geral conceptual da utilização de faturação do Azure e RateCard APIs, que são utilizadas para fornecer informações para o consumo de recursos Azure e tendências."
   services=""
   documentationCenter=""
   authors="BryanLa"
   manager="mbaldwin"
   editor=""
   tags="billing"/>

<tags
   ms.service="billing"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="billing"
   ms.date="08/16/2016"
   ms.author="mobandyo;bryanla"/>

# <a name="gain-insights-into-your-microsoft-azure-resource-consumption"></a>Informações de lucro para o consumo de recursos do Microsoft Azure

Clientes e parceiros requerem a capacidade de com exatidão prever e gerir os seus custos Azure.  À medida que desloca a partir de um Capex a um modelo de Opex, também precisa a capacidade de fazer showback vs. análise encargos, bem como fornecer fidelidade modo no estimativa e faturação, especialmente para implementações de grandes dimensões na nuvem.

A utilização de recursos do Azure e taxa APIs de cartão de outros fabricantes referidos este endereço artigo estas necessidades ao ativar novas informações para o seu consumo de recursos Azure.  

## <a name="introducing-the-azure-resource-usage-and-ratecard-apis"></a>Introdução ao Azure utilização do recurso e RateCard APIs

A utilização de recursos do Azure e RateCard APIs são implementadas como um fornecedor de recursos, como parte da família das APIs expostos pelo Gestor de recursos do Azure.  

### <a name="azure-resource-usage-api-preview"></a>Utilização de recursos Azure API (pré-visualização)
Clientes e parceiros podem utilizar a API da utilização de recursos do Azure para obter os respetivos dados consumo Azure estimado. As funcionalidades incluem:

- **Controlo de acesso baseado em funções azure** - clientes e parceiros pode configurar as políticas de acesso no [portal do Azure](https://portal.azure.com) ou através de [os cmdlets do Azure PowerShell](powershell-install-configure.md) para especificar quais os utilizadores ou aplicações podem obter acesso a dados de utilização da subscrição. Os autores das chamadas tem de utilizar tokens padrão do Azure Active Directory para autenticação. O autor da chamada também tem de ser adicionado à função o leitor, proprietário ou contribuinte para obter acesso a dados de utilização para uma subscrição Azure específica.

- **Hora a hora ou agregações diária** - os autores das chamadas podem especificar se pretendem os respetivos dados de utilização Azure horários grupos ou grupos diários. A predefinição é diárias.

- **Metadados instância fornecidos (inclui etiquetas de recurso)** – detalhes de nível da instância, como o uri resource completamente qualificado (/subscriptions/ {id da subscrição} /...), juntamente com o recurso de etiquetas de informações e recursos de grupo serão fornecidas na resposta. Isto irá ajudar os clientes deterministically e atribuir através de programação a utilização por etiquetas, para casos de utilização, como a carregar cruzada.

- **Metadados do recurso fornecidos** - detalhes do recurso como o nome de um indicador de apresentar, medidor categoria, medidor sub categoria, unidade e região também será transmitida na resposta, para dar o autores das chamadas uma melhor Noções sobre o que foi consumida. Estamos também a trabalhar para alinhar terminologia de metadados do recurso em portal do Azure, Azure a utilização CSV, EA faturação CSV e outros experiências público, para permitir que os clientes correlacionar dados ao longo experiências.

- **A utilização para todos os tipos de oferta** – dados de utilização serão ser acessíveis para todos os oferecem tipos incluindo repartição, MSDN, compromisso monetário, monetários créditos e EA entre outros.

### <a name="azure-resource-ratecard-api-preview"></a>Recurso Azure RateCard API (pré-visualização)
Clientes e parceiros podem utilizar a API do Azure recurso RateCard para obter a lista de recursos Azure disponíveis, juntamente com estimada estimados para cada um. As funcionalidades incluem:

- **Controlo de acesso baseado em funções azure** - clientes e parceiros pode configurar as políticas de acesso no [portal do Azure](https://portal.azure.com) ou através de [os cmdlets do Azure PowerShell](powershell-install-configure.md) para especificar quais os utilizadores ou aplicações podem obter um acesso aos dados RateCard. Os autores das chamadas tem de utilizar tokens padrão do Azure Active Directory para autenticação. O autor da chamada também tem de ser adicionado à função o leitor, proprietário ou contribuinte para obter acesso a dados de utilização para uma subscrição Azure específica.

- **Suporte para repartição, MSDN, compromisso monetário e crédito monetário oferece (EA não suportada)** - This API fornece informações de taxa de nível de oferta Azure, vs. ao nível da subscrição.  O autor da chamada desta API deve passar as informações da oferta para obter detalhes do recurso e taxas.  Como EA ofertas personalizou taxas por inscrição, podemos fornecer as taxas EA neste momento.

## <a name="scenarios"></a>Cenários

Aqui estão alguns dos cenários que são efetuados possíveis com a combinação de utilização de e as APIs RateCard:

- **Azure gastou durante o mês** - os clientes podem usar a utilização e APIs RateCard em conjunto para obter informações melhor para os respetivos nuvem passam durante o mês, através da análise os registos de hora a hora e diários das estimativas a utilização e os juros.

- **Configurar alertas** – clientes e parceiros pode configurar o recurso-monetários baseado ou alertas no seu consumo de nuvem ao obter o consumo estimado e estimativa encargo utilizando a utilização e a API RateCard.

- **Fatura Predict** – clientes e parceiros pode obter os respetivos consumo estimado e nuvem passam e aplicar algoritmos de aprendizagem automática para prever o que os respetivos fatura seria no final do ciclo de faturação.

- **Análise de custos de pré-lançamento consumo** – pode clientes também utilizar a API RateCard prever quanto sua fatura seria se terem sido mover as cargas de trabalho para Azure, por fornecendo pretendido números de utilização. Se clientes têm das cargas de trabalho existentes noutros nuvens ou nuvens privados, também pode mapear a sua utilização com o Azure passam a taxas para obter uma melhor estimativa da sua Azure estimado. Isto fornece uma vista avançada do que pode ser obtido através do [Azure Calculadora de preços](https://azure.microsoft.com/pricing/calculator/), como (por exemplo) nossos parceiros de faturação fornecem a capacidade de na oferta de tabela dinâmica e comparar/contraste entre os tipos de oferta diferentes para além das repartição, incluindo compromisso monetário e crédito monetário. As APIs também fornecem a capacidade de alterações de custo estimativa por região, ativar o tipo de análise de hipóteses necessário para tomar decisões de implementação, tal como implementar recursos no CDs diferentes em todo o mundo podem ter um impacto directo sobre o custo total.

- **Análise de hipóteses** -

    - Clientes e parceiros podem determinar se seria mais rentável para executar as cargas de trabalho no outro região, ou na outra configuração do recurso Azure. Recurso Azure custos podem ser diferentes com base na região do Azure na qual são executar e, isto permite que clientes e parceiros obter otimizações de custo.

    - Clientes e parceiros também podem determinar se outro tipo de oferta Azure fornece uma taxa melhor num recurso Azure.

## <a name="partner-solutions"></a>Soluções de parceiros

[A utilização do Microsoft Azure e RateCard APIs ativar Cloudyn para fornecer ITFM para clientes](billing-usage-rate-card-partner-solution-cloudyn.md) descreve a experiência de integração oferecida pelo parceiro Azure faturação API [Cloudyn](https://www.cloudyn.com/microsoft-azure/).  Este artigo fornece cobertura detalhada das suas experiências, incluindo um breve vídeo que mostra como um cliente do Azure pode utilizar Cloudyn e APIs de faturação do Azure para informações de ganhos a partir dos seus dados consumo Azure.

[Cruiser na nuvem e a integração de API faturação do Microsoft Azure](billing-usage-rate-card-partner-solution-cloudcruiser.md) descreve como [da nuvem Cruiser Express para o pacote do Azure de](http://www.cloudcruiser.com/partners/microsoft/) funciona diretamente a partir do portal WAP, permitindo-os clientes de forma totalmente integrada gerir aspectos operacionais e financeiros dos seu Microsoft Azure privado ou alojado público na nuvem a partir de uma única interface de utilizador.   

## <a name="next-steps"></a>Próximos passos
+ Dar saída a [Referência da Azure faturação REST API](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c) para obter mais detalhes sobre as duas APIs, que fazem parte de um conjunto de APIs fornecidos pelo Gestor de recursos do Azure.
+ Se pretender para ficar aceder à direita o código de exemplo, consulte o artigo nosso Microsoft Azure faturação API exemplos de código em [Amostras de código do Azure](https://azure.microsoft.com/documentation/samples/?term=billing).

## <a name="learn-more"></a>Saiba mais
+ Consulte o artigo [Descrição geral do Gestor de recursos do Azure](azure-resource-manager/resource-group-overview.md) para saber mais sobre o Gestor de recursos do Azure.
+ Para obter informações adicionais sobre o conjunto de ferramentas necessárias para ajudar a ganhar a compreensão de nuvem passam, consulte Gartner artigo [Guia de mercado para ferramentas de gestão financeira TI (ITFM)](http://www.gartner.com/technology/reprints.do?id=1-212F7AL&ct=140909&st=sb).
