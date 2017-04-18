<properties
    pageTitle="Pedido avançado limitação com a gestão de API do Azure"
    description="Saiba como criar e aplicar quota flexível e taxa limitar as políticas de gestão de API do Azure."
    services="api-management"
    documentationCenter=""
    authors="darrelmiller"
    manager="erikre"
    editor=""/>

<tags
    ms.service="api-management"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="10/25/2016"
    ms.author="darrmi"/>


# <a name="advanced-request-throttling-with-azure-api-management"></a>Pedido avançado limitação com a gestão de API do Azure

Ser capaz de optimizar pedidos recebidos é uma função de chave de gestão de API do Azure. Quer controlando a taxa de pedidos de ou os dados/pedidos total transferidos, a gestão de API permite fornecedores de API para proteger a suas APIs de abuse e criar valor para diferentes camadas de produto API.

## <a name="product-based-throttling"></a>Limitação de produto com base
Até à data, a taxa limitação capacidades tem sido limitadas ao livro a ser confinado a uma subscrição em particular produto (essencialmente uma chave), definido no portal do publisher de gestão de API. Isto é útil para o fornecedor de API aplicar limites sobre os programadores de que se inscreveram para utilizar os respetivos API, no entanto,-não ajuda, por exemplo, no limitação utilizadores finais individuais, de API. É possível que se refere a um utilizador da aplicação o Programador de consumir a quota de toda e, em seguida, impedir que outros clientes do programador ser capaz de utilizar a aplicação. Além disso, vários clientes que podem gerar um grande volume de pedidos de poderão limitar o acesso aos utilizadores ocasionais.

## <a name="custom-key-based-throttling"></a>Chave personalizado baseada limitação
As políticas de [taxa de limite por chave](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRateByKey) e [quota por chave](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuotaByKey) novas fornecem uma solução significativamente mais flexível para controlo de tráfego. Estas novas políticas permitem-lhe definir expressões para identificar as teclas que serão utilizadas para controlar a utilização de tráfego. A forma como isto funciona easiest é ilustrado com um exemplo. 

## <a name="ip-address-throttling"></a>Limitação do endereço IP
As seguintes políticas restringir um endereço IP do cliente único que apenas 10 chamadas minuto, com um total de 1.000.000 chamadas e 10.000 quilobytes da largura de banda por mês. 

    <rate-limit-by-key  calls="10"
              renewal-period="60"
              counter-key="@(context.Request.IpAddress)" />

    <quota-by-key calls="1000000"
              bandwidth="10000"
              renewal-period="2629800"
              counter-key="@(context.Request.IpAddress)" />

Se todos os clientes de na Internet utilizado um endereço IP exclusivo, poderá uma forma eficaz de limitar a utilização pelo utilizador. No entanto, é muito provável que vários utilizadores irão partilha um único endereço IP público devido ao-los ao aceder à Internet através de um dispositivo NAT. Apesar desta ação, para APIs permitir o acesso não autenticado a `IpAddress` poderá estar a melhor opção.

## <a name="user-identity-throttling"></a>Limitação de identidade do utilizador
Se um utilizador final é autenticado, em seguida, uma chave de optimização pode ser gerada com base nas informações que identifica exclusivamente que um utilizador.

    <rate-limit-by-key calls="10"
        renewal-period="60"
        counter-key="@(context.Request.Headers.GetValueOrDefault("Authorization","").AsJwt()?.Subject)" />

Neste exemplo estamos extrair cabeçalho autorização, convertê-la para `JWT` do objeto e utilize o assunto do token para identificar o utilizador e utilizar como a taxa de limitar chave. Se a identidade do utilizador está armazenada na `JWT` como um do outro afirmações, em seguida, que pode ser utilizado valor no seu lugar.

## <a name="combined-policies"></a>Políticas de combinado
Apesar das novas políticas optimização fornecem mais controlo que as políticas de optimização existentes, ainda existe valor combinação de ambas as capacidades. Limitação por chave de subscrição do produto ([taxas de chamada de limite através de uma subscrição](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRate) e [Definir quota de utilização através de uma subscrição](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuota)) é uma excelente forma de ativar monetizing de uma API ao carregar o dispositivo com base em níveis de utilização. O melhor controlo com de ser capaz de optimizar por utilizador é complementar e impede o comportamento de um utilizador de degradar a experiência de outra. 

## <a name="client-driven-throttling"></a>Condicionada pelo limitação de cliente
Quando a tecla optimização é definida utilizando uma [expressão de política](https://msdn.microsoft.com/library/azure/dn910913.aspx), em seguida, é o fornecedor de API que consiste em escolher como a limitação está confinado. No entanto, um programador poderá pretender controlar como eles classificar limite os seus próprios clientes. Isto pode ser ativado pelo fornecedor de API ao introduzir um cabeçalho personalizado para permitir que a aplicação de cliente o programador comunicar a chave à API.

    <rate-limit-by-key calls="100"
              renewal-period="60"
              counter-key="@(request.Headers.GetValueOrDefault("Rate-Key",""))"/>

Isto ativa a aplicação de cliente o programador escolher como pretende criar a taxa de limitar chave. Com um pouco de ingenuity um programador de cliente podia criar os seus próprios camadas taxa atribuindo conjuntos de teclas para os utilizadores e rodar a utilização da chave.

## <a name="summary"></a>Resumo
Gestão de API Azure fornece taxa e proposta limitação para proteger e adicione valor ao seu serviço de API. As novas políticas optimização com regras de âmbito personalizadas permitem-lhe melhor com controlo sobre essas exigências para ativar os seus clientes criar aplicações ainda melhores. Os exemplos neste artigo demonstram a utilização destas novas políticas pela taxa de fabrico limitar chaves com endereços IP do cliente, identidade do utilizador e os valores de cliente gerado. No entanto, existem muitas outras partes da mensagem que podem ser utilizadas como agente do utilizador, o caminho de URL fragmentos, o tamanho da mensagem.

## <a name="next-steps"></a>Próximos passos
Dê-nos seus comentários no tópico Disqus deste tópico. Seria ótima ouvir sobre outros valores chaves potenciais que foram uma escolha lógica no seu cenários.

## <a name="watch-a-video-overview-of-these-policies"></a>Ver uma descrição geral em vídeo estas políticas
Para mais informações sobre as políticas de [taxa de limite por chave](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRateByKey) e [quota por chave](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuotaByKey) abordados neste artigo, contacte o veja o vídeo seguinte.

> [AZURE.VIDEO advanced-request-throttling-with-azure-api-management]
