<properties 
    pageTitle="Políticas de gestão de Azure API no | Microsoft Azure" 
    description="Saiba como criar, editar e configurar as políticas de gestão de API." 
    services="api-management" 
    documentationCenter="" 
    authors="steved0x" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="api-management" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/25/2016" 
    ms.author="sdanie"/>


#<a name="policies-in-azure-api-management"></a>Políticas de gestão de Azure API

Em gestão de API do Azure, as políticas são uma funcionalidade avançada do sistema que permitir que o publisher alterar o comportamento da API do através da configuração. As políticas são uma coleção de instruções que são executadas sequencialmente no pedido de ou resposta de uma API. Declarações populares incluem a conversão do formato de XML para JSON e taxa limitar para restringir a quantidade de chamadas a partir de um programador de chamadas. Muitas mais políticas estão disponíveis terminar a caixa.

Consulte a [Referência da política][] para uma lista completa de declarações de política e as suas definições.

As políticas são aplicadas dentro do gateway que encontra-se entre o consumidor API e a API gerida. O gateway recebe todos os pedidos e normalmente encaminha-los inalterada à API subjacente. No entanto uma política pode aplicar alterações para o pedido de entrada e de resposta de saída.

Expressões de política podem ser utilizados como valores de atributo ou valores de texto em qualquer uma das políticas de gestão de API, a menos que a política caso contrário, especifica. Algumas políticas, tais como as políticas de [fluxo de controlo][] e [Definir variável][] são baseadas em expressões de política. Para mais informações, consulte o artigo [Avançadas políticas][] e [expressões de política][].

## <a name="scopes"> </a>Como configurar políticas
Políticas podem ser configuradas globalmente ou o âmbito de um [produto][], [API][] ou [operação][]. Para configurar uma política, navegue para o editor de políticas no portal do publisher.

![Menu de políticas][policies-menu]

O editor de políticas é constituído por três secções principais: o âmbito de política (parte superior), a definição de política onde políticas forem editadas (esquerda) e as declarações de lista (direita):

![Editor de políticas][policies-editor]

Para começar a configurar uma política de primeiro tem de selecionar o âmbito no qual deve ser aplicada a política. Na captura de ecrã abaixo o **Starter** produto está selecionado. Repare que o símbolo quadrado junto ao nome da política indica que já é aplicada uma política a este nível.

![Âmbito][policies-scope]

Uma vez que já foi aplicada uma política, a configuração é apresentada na vista de definição.

![Configurar][policies-configure]

A política é apresentada só de leitura na primeira. Para editar a definição, clique na **Configurar a política de** ação.

![Editar][policies-edit]

A definição de política é um documento XML simple que descreve uma sequência de declarações de entrada e saídas. Pode ser editado o XML diretamente na janela da definição. Uma lista de declarações é fornecida para a direita e declarações aplicáveis ao âmbito actual estão ativadas e realçadas; conforme demonstrado pela declaração de **Taxas de chamada limite** na captura de ecrã acima.

Clicar numa declaração ativada irá adicionar XML adequado na localização do cursor na vista de definição. 

>[AZURE.NOTE] Se a política ao qual pretende adicionar não estiver ativada, certifique-se de que se encontra no âmbito correto para essa política. Cada declaração da política foi concebida para utilização em determinados âmbitos e as secções de política. Para rever as secções de política e âmbitos para uma política, consulte a secção de **utilização** para esse política na [Referência da política de][].

Uma lista completa das declarações de política e as definições estão disponíveis na [Referência da política][].

Por exemplo, para adicionar uma nova instrução para restringir pedidos recebidos a endereços IP especificados, coloque o cursor dentro do conteúdo da `inbound` elemento XML e clique na declaração de **restringir autor IPs** .

![Políticas de restrição][policies-restrict]

Este procedimento irá adicionar um fragmento XML para a `inbound` elemento que fornece orientações sobre como configurar a declaração de.

    <ip-filter action="allow | forbid">
        <address>address</address>
        <address-range from="address" to="address"/>
    </ip-filter>

Para limitar os pedidos de entrada e aceitar apenas as partir de um endereço IP de 1.2.3.4 modificar XML da seguinte forma:

    <ip-filter action="allow">
        <address>1.2.3.4</address>
    </ip-filter>

![Guardar][policies-save]

Quando concluir a configurar as instruções para a política, clique em **Guardar** e as alterações serão automaticamente propagadas para o API o Data Management gateway.

##<a name="sections"> </a>Configuração da política de noções básicas sobre

Uma política é uma série de declarações executar para que um pedido e uma resposta. A configuração está corretamente dividida em `inbound`, `backend`, `outbound`, e `on-error` secções conforme apresentado na configuração que se segue.

    <policies>
      <inbound>
        <!-- statements to be applied to the request go here -->
      </inbound>
      <backend>
        <!-- statements to be applied before the request is forwarded to 
             the backend service go here -->
      </backend>
      <outbound>
        <!-- statements to be applied to the response go here -->
      </outbound>
      <on-error>
        <!-- statements to be applied if there is an error condition go here -->
      </on-error>
    </policies> 

Se existir um erro durante o processamento de um pedido, os restantes passos a `inbound`, `backend`, ou `outbound` secções são ignoradas e execução saltos para as instruções no `on-error` secção. Ao colocar declarações de política na `on-error` secção pode rever o erro utilizando o `context.LastError` propriedade, inspecionar e personalizar a resposta do erro utilizando o `set-body` política e configurar o que acontece se ocorre um erro. Existem códigos de erro para obter os passos incorporados e erros que podem ocorrer durante o processamento de declarações de política. Para mais informações, consulte o artigo [processamento de erros no políticas de gestão de API](https://msdn.microsoft.com/library/azure/mt629506.aspx).

Uma vez que podem ser especificadas políticas de níveis diferentes (global, produto, api e operação) a configuração fornece uma forma para especificar a ordem pela qual declarações a definição de política executar relativamente à política principal. 

Âmbitos de política são avaliados pela seguinte ordem.

1. Âmbito global
2. Âmbito do produto
3. Âmbito de API
4. Âmbito de operação

Declarações nelas são avaliadas de acordo com o posicionamento da `base` elemento, caso esteja presente.

Por exemplo, se tiver uma política em nível global e de uma política configurada para uma API, em seguida, sempre que esse API particular é utilizada ambas as políticas irão serão aplicadas. Gestão de API permite para encomendar determinista das declarações de política combinado através do elemento de base. 

    <policies>
        <inbound>
            <cross-domain />
            <base />
            <find-and-replace from="xyz" to="abc" />
        </inbound>
    </policies>

Na definição de política do exemplo acima, a `cross-domain` declaração seria executado antes de ser seguido de quaisquer políticas mais elevadas que por sua vez, o `find-and-replace` política.

Se for apresentada a mesma política duas vezes na declaração de política, é aplicada a política avaliada mais recentemente. Pode utilizar esta opção de substituir as políticas que são definidas num âmbito superior. Para ver as políticas que foram no âmbito actual no editor de política, clique em **voltar a calcular política efectiva para âmbito selecionado**.

Tenha em atenção que política global não tem nenhuma política de principal e utilizar o `<base>` elemento-não tem efeito. 

## <a name="next-steps"></a>Próximos passos

Consulte o artigo seguir vídeo expressões de política.

> [AZURE.VIDEO policy-expressions-in-azure-api-management]

[Referência da política]: api-management-policy-reference.md
[Produto]: api-management-howto-add-products.md
[API]: api-management-howto-add-products.md#add-apis 
[Operação]: api-management-howto-add-operations.md

[Políticas de avançadas]: https://msdn.microsoft.com/library/azure/dn894085.aspx
[Fluxo de controlo]: https://msdn.microsoft.com/library/azure/dn894085.aspx#choose
[Definir a variável de]: https://msdn.microsoft.com/library/azure/dn894085.aspx#set_variable
[Expressões de política]: https://msdn.microsoft.com/library/azure/dn910913.aspx

[policies-menu]: ./media/api-management-howto-policies/api-management-policies-menu.png
[policies-editor]: ./media/api-management-howto-policies/api-management-policies-editor.png
[policies-scope]: ./media/api-management-howto-policies/api-management-policies-scope.png
[policies-configure]: ./media/api-management-howto-policies/api-management-policies-configure.png
[policies-edit]: ./media/api-management-howto-policies/api-management-policies-edit.png
[policies-restrict]: ./media/api-management-howto-policies/api-management-policies-restrict.png
[policies-save]: ./media/api-management-howto-policies/api-management-policies-save.png
