<properties
   pageTitle="Técnicas pré-requisitos para a criação de um serviço de dados do Marketplace | Microsoft Azure"
   description="Compreender os requisitos para a criação de um serviço de dados para implementar e vender no Azure Marketplace"
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager="hascipio"
   editor=""/>

<tags
   ms.service="marketplace"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/26/2016"
   ms.author="hascipio; avikova" />

# <a name="technical-pre-requisites-for-creating-a-data-service-offer-for-the-azure-marketplace"></a>Técnicas pré-requisitos para a criação de um serviço de dados da oferta para Azure Marketplace

>[AZURE.IMPORTANT] **Neste momento podemos já não são ativação qualquer novos fabricantes de serviço de dados. Novo dataservices não irá obter aprovado para obter uma lista.** Se tiver uma aplicação de empresas SaaS que gostaria de publicar no AppSource pode encontrar mais informações [aqui](https://appsource.microsoft.com/partners). Se tiver um aplicações IaaS ou serviço para programadores que gostaria de publicar no Azure Marketplace pode encontrar mais informações [aqui](https://azure.microsoft.com/marketplace/programs/certified/).

Leia o processo de cuidadosamente antes de começar e compreender onde e por que motivo é executado cada passo. Quanto possível, qual deve preparar a informação da sua empresa e outros dados, transferir ferramentas necessárias e/ou criar componentes técnicos antes de iniciar o processo de criação de oferta.

Que deve ter os seguintes itens preparados antes de iniciar o processo:

## <a name="make-a-decision-on-what-technology-will-be-used-to-publish-your-data-service-offer"></a>Tomar uma decisão no que tecnologia será utilizada para publicar a sua oferta de serviço de dados

Um fabricante pode decidir entre vários tecnologias quando o serviço de dados de publicação no Azure Marketplace. As principais technologies, que são suportadas descritos abaixo. Independentemente que a tecnologia de apoio é utilizada para publicar o serviço de dados, o utilizador final consome os dados através do **OData feed** exposto pela Azure Marketplace serviço. Obter informações completas sobre o serviço de OData que pode encontrar na [http://www.odata.org/](http://www.odata.org/)

## <a name="sql-azure-database"></a>SQL Azure base de dados

Está com dificuldades conjunto de dados pronto no SQL Azure cabe do Publisher. Terá de subscrever Azure, aprovisionar o tamanho adequado da base de dados e carregue os seus dados na base de dados do SQL Azure. Publisher também é responsável para manter o seus dados sempre atualizados. Obter mais informações sobre ao subscrever a serviços de Azure que pode encontrar na [https://azure.microsoft.com/services/sql-database/](https://azure.microsoft.com/services/sql-database/)


Quando mover os dados no SQL Azure, Azure Marketplace pode expor tabelas e vistas. O Publisher pode especificar quais/vistas de tabelas e colunas são expostas para o utilizador final. Ainda mais o fornecedor de conteúdos também pode especificar quais as colunas que podem ser consultadas pelo utilizador final e os que só são devolvidos na carga de útil. Isto dá um nível de flexibilidade sobre quais os dados na base de dados devem ser expostos elevado. Colunas que podem ser consultadas tem de ser cópias por uma ou mais índices de base de dados.

## <a name="rest-based-web-service"></a>Serviço web REST com base

Protocolos suportados: **apenas HTTPS**

Serviços de resto com base existentes podem ser expostos através do Azure Marketplace. Uma vez que o conjunto de dados é sempre exposto para o utilizador final como um feed de OData, as necessidades de serviço do Azure Marketplace possam mapear o serviço para um OData com base no serviço. Para fazê-lo os restantes com os pontos finais base tem de expor todos os parâmetros como parâmetros HTTP.

A carga de útil tem de estar num formulário que pode ser mapeada para uma resposta ATOM. Por conseguinte, a resposta de serviços precisa de estar no formato XML e só pode conter um elemento de repetição que contém os valores de carga útil (como o conjunto de registos). O serviço do Azure Marketplace irá mapear o nó repetição a nó entrada ATOM e os valores de carga útil para os nós de propriedade no nó de entrada.

Informações de autorização (por exemplo, de API chave, autenticação token, etc.) tem de estar fornecidos como um parâmetro HTTP ou no cabeçalho de HTTP (par de valor de chave) – autenticação básica também é suportada. Uma chave de válida tem de ser fornecido e todos os pedidos de através do Azure Marketplace estão a ser efectuados através dessa tecla. Utilizador monitorização e faturação acontece na camada do Azure Marketplace.

Erros devolvidos pelo serviço tem de ser mapeados para códigos de estado HTTP. Caso o serviço devolve um XML que contém o erro que estas irão ser mapeado pelo serviço Azure Marketplace para códigos de estado HTTP.

## <a name="soap-based-web-services"></a>Serviços de web SOAP com base

Protocolo: **apenas HTTPS**

Os requisitos são com o mesmo, tal como o resto base secção serviço. A única diferença é que os parâmetros também podem ser fornecidos no corpo XML a ser publicada ao serviço do Publisher com cada pedido feito através do Azure Marketplace. Isto significa que os parâmetros HTTP que fornece o utilizador na front-end estão a ser traduzidos elementos XML de um documento XML publicada com o pedido de serviço web do fornecedor de conteúdo.

## <a name="odata-based-web-services"></a>Serviços de web OData com base

Protocolo: **apenas HTTPS**

Dados podem ser expostos como um serviço de OData ao Azure Marketplace. O sistema vai ser efetuada com o serviço através de e substitui a raiz do serviço de raiz de serviço do Azure Marketplace – para garantir que todas as chamadas subsequentes analisá Azure Marketplace.

Serviços de OData não só é necessário aceda relativamente a uma base de dados back-end. OData suporta qualquer tipo de armazenamento ou empresas lógica para orientar o serviço.


## <a name="next-steps"></a>Próximos passos
Agora que revisto pré-requisitos e concluídas as tarefas necessárias, pode avançar com a sua oferta de serviço de dados como detalhadas no [Guia de publicação de serviço de dados](marketplace-publishing-data-service-creation.md)de criação.

Ou, se pretender para rever o processo global e os respetivos artigos para cada uma das fases publicação, visite o artigo [Introdução: como publicar uma oferta ao Azure Marketplace](marketplace-publishing-getting-started.md).

[link-acct]:marketplace-publishing-accounts-creation-registration.md
