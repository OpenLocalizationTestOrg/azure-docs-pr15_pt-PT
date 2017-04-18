<properties 
    pageTitle="Como configurar as notificações e modelos de correio eletrónico na gestão de API do Azure" 
    description="Saiba como configurar as notificações e modelos no Azure API gestão de e-mail." 
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

# <a name="how-to-configure-notifications-and-email-templates-in-azure-api-management"></a>Como configurar as notificações e modelos de correio eletrónico na gestão de API do Azure

Gestão de API fornece a capacidade para configurar as notificações de eventos específicos e para configurar os modelos de correio eletrónico que são utilizados para comunicar com os administradores e os programadores de uma instância de gestão de API. Este tópico mostra como configurar as notificações para os eventos disponíveis e fornece uma descrição geral de configurar os modelos de correio eletrónico utilizados para estes eventos.

## <a name="publisher-notifications"> </a>Configurar as notificações do publisher

Para configurar as notificações, clique em **Gerir** no Portal clássica do seu serviço de gestão de API do Azure. Isto leva-o para o portal do publisher de gestão de API.

![Portal do Publisher][api-management-management-console]

>Se ainda não criou uma instância do serviço de gestão de API, consulte o artigo [criar uma instância do serviço de gestão de API][] no tutorial [Introdução ao Azure API gestão][] .

Clique em **notificações** a partir do menu de **Gestão de API** à esquerda para ver as notificações disponíveis.

![Notificações do Publisher][api-management-publisher-notifications]

A seguinte lista de eventos pode ser configurada para notificações.

-   **Pedidos de subscrição (exigir aprovação)** - os destinatários de e-mail especificado e os utilizadores irão receber notificações de correio eletrónico acerca dos pedidos de subscrição para produtos API exigir aprovação.
-   **Novas subscrições** - os destinatários de e-mail especificado e os utilizadores irão receber notificações de correio eletrónico sobre as subscrições de produto novas API.
-   **Pedidos de Galeria de aplicação** - os destinatários de e-mail especificado e os utilizadores irão receber notificações de correio eletrónico quando novas aplicações são submetidas para a Galeria de aplicação.
-   **BCC** - os destinatários de e-mail especificado e os utilizadores irão receber e-mail cópias ocultas de todos os e-mails enviados para programadores.
-   **Novo problema ou comentário** - os destinatários de e-mail especificado e os utilizadores irão receber notificações de correio eletrónico quando um novo problema ou submeter comentário no portal do programador.
-   **Mensagem de fechar conta** - os destinatários de e-mail especificado e os utilizadores irão receber notificações de correio eletrónico quando uma conta estiver fechada.
-   **Limite de quota de subscrição Approaching** - os seguintes destinatários de e-mail e os utilizadores irão receber notificações de correio eletrónico quando obtém a utilização de subscrição perto quota de utilização.

Para cada evento, pode especificar os destinatários de correio eletrónico utilizando a caixa de texto do endereço de e-mail ou pode selecionar utilizadores de uma lista.

Para especificar os endereços de e-mail para ser notificado, introduza-las na caixa de texto do endereço de e-mail. Se tiver vários endereços de e-mail, separá-las utilizando vírgulas.

![Destinatários de notificação][api-management-email-addresses]

Para especificar os utilizadores para ser notificado, clique em **Adicionar destinatário**, verifique a caixa junto aos utilizadores para ser notificado e clique em **OK**.

>Tenha em atenção que apenas os administradores são apresentados na lista.

Depois de configurar os destinatários de notificação, clique em **Guardar** para aplicar os destinatários de notificação atualizado.

>Se navegar fora do separador de **Notificações do Publisher** portal do publisher o alerta se existem alterações não guardadas.

## <a name="email-templates"> </a>Configurar modelos de e-mail

Gestão de API fornece modelos de correio eletrónico para as mensagens de e-mail que são enviadas durante a administrar e a utilização do serviço. Os seguintes modelos de correio eletrónico são fornecidos.

-   Submissão da Galeria de aplicação aprovado
-   Carta de fecho de programador
-   Notificação a aproximação de limite de quota de programador
-   Convidar utilizador
-   Novo comentário adicionado a um problema
-   Novo problema recebido
-   Nova subscrição ativada
-   Confirmação da subscrição renovada
-   Pedido de subscrição recusa
-   Pedido de subscrição recebido

Estes modelos podem ser modificados como pretendido.

Para ver e configurar os modelos de correio eletrónico para a instância de API gestão, clique em **notificações** a partir do menu de **Gestão de API** no lado esquerdo e selecione o separador de **Modelos de E-Mail** .

![Modelos de e-mail][api-management-email-templates]

Para ver ou modificar um modelo específico, selecione-a partir da lista pendente de **modelos** .

![Lista de modelos de correio eletrónico][api-management-email-templates-list]

Cada modelo de e-mail tem um assunto em texto simples e uma definição de corpo em formato HTML. Cada item pode ser personalizada como pretendido.

![Editor de modelo de e-mail][api-management-email-template]

A lista de **parâmetros** contém uma lista dos parâmetros, que, quando inserido no assunto ou no corpo da mensagem, será substituído o valor designado quando o correio eletrónico é enviado. Para inserir um parâmetro, coloque o cursor onde pretende que o parâmetro para aceder e clique na seta para a esquerda do nome do parâmetro.

Clique em **Pré-visualizar** ou **Enviar um teste** para ver como o e-mail será procure ou enviar mensagens de e-mail teste.

>Tenha em atenção que os parâmetros não são substituídos por valores reais quando pré-visualizar ou enviar um teste.
>
>Para guardar as alterações ao modelo de e-mail, clique em **Guardar**ou para cancelar as alterações clique em **Cancelar**.



[api-management-management-console]: ./media/api-management-howto-configure-notifications/api-management-management-console.png
[api-management-publisher-notifications]: ./media/api-management-howto-configure-notifications/api-management-publisher-notifications.png
[api-management-email-addresses]: ./media/api-management-howto-configure-notifications/api-management-email-addresses.png


[api-management-email-templates]: ./media/api-management-howto-configure-notifications/api-management-email-templates.png
[api-management-email-templates-list]: ./media/api-management-howto-configure-notifications/api-management-email-templates-list.png
[api-management-email-template]: ./media/api-management-howto-configure-notifications/api-management-email-template.png


[Configure publisher notifications]: #publisher-notifications
[Configure email templates]: #email-templates

[How to create and use groups]: api-management-howto-create-groups.md
[How to associate groups with developers]: api-management-howto-create-groups.md#associate-group-developer

[Introdução ao Azure API gestão]: api-management-get-started.md
[Criar uma instância do serviço de gestão de API]: api-management-get-started.md#create-service-instance