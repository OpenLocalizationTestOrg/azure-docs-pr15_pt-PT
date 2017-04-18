<properties
    pageTitle="Descrição geral de lógica aplicações conectores | Microsoft Azure"
    description="Descrição geral de conectores que podem ser utilizadas numa aplicação de lógica"
    services=""
    documentationCenter="" 
    authors="jeffhollan"
    manager="erikre"
    editor=""
    tags="connectors"/>

<tags
   ms.service="logic-apps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="07/15/2016"
   ms.author="jehollan"/>

# <a name="using-connectors-in-a-logic-app"></a>Utilização de conectores numa aplicação de lógica

Conectores fornecem acesso rápido para eventos, dados e ações nos serviços, protocolos e plataformas.  A lista completa de conectores compatível com aplicações de lógica podem [ser encontradas aqui](apis-list.md).  Conectores podem ser utilizados como um accionador ou uma ação numa aplicação lógica e precisem de uma *ligação* de configurado para utilizar (por exemplo: autorização de uma conta do Twitter para aceder a ou publique em seu nome).

## <a name="basics"></a>Noções básicas

As conexões são serviços alojados pode aceder ao como parte de uma aplicação de lógica para integrar com outros serviços como Dynamics, Azure, Salesforce, [e mais](apis-list.md).  São implementados e geridas pelo Microsoft, para que possa criar os seus fluxos de trabalho de integração com escala, débito e segurança tratados.  Pode adicionar uma conexão a uma aplicação de lógica ao procurar e selecionar uma ação de conexão ou accionador em **Mostrar Microsoft gerido APIs**.

![Menu de ações para selecionar accionador][1]

Cada ação de conexão ou accionador terá o seu conjunto de propriedades para configurar.  Pode clique nos botões informações para saber mais sobre a ação ou a referência sua documentação [para obter mais informações](apis-list.md).

Se pretende integrar com um serviço ou API que não esteja ainda uma conexão, também pode expandir aplicações lógica através de uma [conexão personalizada](../app-service-logic/app-service-logic-create-api-app.md) ou apenas a chamada diretamente para o serviço através de um protocolo como HTTP.

## <a name="triggers"></a>Accionadores

Algumas conexões tem um accionador, o que significa que um evento a partir desse conexão serão fire de uma aplicação de lógica e transmitir em quaisquer dados como parte do accionador.  Um accionador é sempre o primeiro passo numa aplicação lógica.  Accionadores populares incluem operações como:
 
 * Periodicidade - executar cada hora
 * Quando é recebido um pedido de HTTP
 * Quando um item é adicionado a uma fila
 * Quando é recebida uma mensagem de e-mail
 
Alguns Accionadores irão fogo instantâneas que um evento acontece através de uma notificação para a aplicação de lógica e outras pessoas irão precisar de um intervalo de periodicidade configurado no número de vezes que a aplicação de lógica vai verificar o serviço para que um evento (até cada 15 segundos).  

Assim que um evento for recebido, a aplicação de lógica executar irá fogo e as ações no fluxo de trabalho serão iniciado.  Também poderá aceder a quaisquer dados a partir do accionador em todo o fluxo de trabalho (por exemplo o accionador 'num tweet novo' irá passar a tweet para executar).

## <a name="actions"></a>Ações

A maior parte dos conexões têm uma ou várias ações que podem ser executadas como parte do fluxo de trabalho.  Ações estão todos os passos que ocorrem depois de executar tem chama a partir de um accionador.  Para adicionar um ação, clique o botão de **Novo passo** e procurar a conexão que pretende utilizar.  Depois de selecionado (e configurar após todas as [ligações](#connections) que podem ser necessários) irá ver o cartão de ação, que pode configurar.  Pode selecionar dados de passos anteriores ao clicar em qualquer um dos tokens para saídas ou introduza na qualquer outra configuração conforme necessário.

![Configurar uma ação de conexão][2]

## <a name="connections"></a>Ligações

A maior parte dos conectores necessitam que configurar uma *ligação* antes de poder utilizar o conector.  Uma *ligação* é qualquer configuração ligação ou de início de sessão necessária para aceder a conexão.  Para os conectores que utilizam OAuth, criar uma ligação significa que iniciar sessão no serviço (como o Office 365, Salesforce ou GitHub) onde pode ser encriptado e armazenado de forma segura num arquivo secreta Azure token de acesso.  Outros conectores (como FTP e SQL) requerem uma ligação que contém a configuração como endereço do servidor, nome de utilizador e palavra-passe.  Estes detalhes de configuração de ligação são também encriptados e armazenados em segurança.  Ligações poderão aceder ao serviço para, desde que permite que o serviço.  Para ligações do Azure Active Directory OAuth (como o Office 365 e Dynamics) podemos pode continuar a atualizar o token de acesso indefinidamente.  Outros serviços poderão colocar limites no quanto tempo pode Utilizamos um token sem-lo a ser atualizados.  Em geral determinadas ações, tais como alterar uma palavra-passe invalidará todos os tokens de acesso.  

Ligações possam ser visualizadas e geridas no Azure clicando em **Procurar** e selecionando **API ligações**.  O recurso de ligações de API pode visualizar, editar, atualizar ou autorizar novamente todas as ligações que criou.

## <a name="next-steps"></a>Próximos passos

- [Criar a sua primeira aplicação de lógica](../app-service-logic/app-service-logic-create-a-logic-app.md)
- [Saiba mais exemplos de aplicações de lógica e utilizações comuns](../app-service-logic/app-service-logic-examples-and-scenarios.md)
- [Começar a trabalhar com accionadores de integração de empresa e acções](../app-service-logic/app-service-logic-enterprise-integration-overview.md)

<!--Image References -->
[1]: ./media/connectors-overview/addAction.png
[2]: ./media/connectors-overview/configureAction.png