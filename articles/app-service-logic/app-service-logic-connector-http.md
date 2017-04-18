<properties
   pageTitle="Utilizando o escuta HTTP e o conector nas aplicações de lógica | Aplicação de serviço do Microsoft Azure "
   description="Como criar e configurar o escuta HTTP e a aplicação de conexão ou API de ação de HTTP e utilizá-la numa aplicação lógica na aplicação de serviço do Azure"
   services="logic-apps"
   documentationCenter=".net,nodejs,java"
   authors="anuragdalmia"
   manager="erikre"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="08/31/2016"
   ms.author="prkumar"/>


# <a name="get-started-with-the-http-listener-and-http-action-and-add-it-to-your-logic-app"></a>Começar a trabalhar com a escuta HTTP e ação de HTTP e adicioná-lo para a sua aplicação de lógica

> [AZURE.NOTE]Vamos estejam terminando suporte para este conector porque a funcionalidade está agora incluída por predefinição como o **manual accionador** quando criar novas aplicações de lógica.  Recomendamos que atualizar todas as suas aplicações de lógica que estão a utilizar este conector.  
> Esta versão do artigo aplica-se a versão do esquema de 2014-12-01-pré-visualização de aplicações de lógica.

Ligar diretamente para recursos HTTP para ouvir para pedidos de HTTP e configurar os pedidos de web HTTP. Existem alguns cenários onde poderá ter de trabalhar com ligações de HTTP diretas, incluindo:

1.  Desenvolver uma aplicação que suporta uma web ou utilizador móvel interativos front-end lógica.
2.  Para obter e processar dados a partir de um serviço web que não tem uma fora do conector de caixa.
3.  Para executar ações que já estão exposta como um serviço web, mas não está disponível como uma aplicação de API.

Para estes cenários, existem duas opções:

1. **Escuta HTTP**: este conector age como um accionador e recebe pedidos de HTTP de um ponto final de configurado. Quando é recebida uma chamada no ponto final configurado, accionadores uma nova instância do fluxo de e transmite os recebidos no pedido de para o fluxo de transformação de dados. Também pode ser configurado para responder automaticamente ao pedido de recebidas, quando o fluxo tiver iniciado ou permitem-lhe construir uma resposta com base na execução do fluxo e enviar uma resposta para o autor da chamada.
2. **Ação de HTTP**: Isto permite-lhe configurar um pedido de web para qualquer ponto final disponível na internet, obtém novamente uma resposta e disponibiliza-o para ações adicionais no fluxo de consumir.

Podem acionar lógica aplicações com base numa variedade de origens de dados e conectores de oferta para obter e processar dados como uma parte do fluxo da. Pode adicionar o conector HTTP aos seus dados de fluxo de trabalho e o processo de negócio como parte de uma aplicação de lógica este fluxo de trabalho. 

## <a name="creating-an-http-listener-for-your-logic-app"></a>Criar uma escuta HTTP para a sua aplicação de lógica
Uma conexão pode ser criada dentro de uma aplicação de lógica ou ser criada diretamente a partir do Azure Marketplace. Para criar uma conexão a partir de mercado:  

1. Na startboard Azure, selecione **Marketplace**.
2. Procurar "HTTP", selecione escuta HTTP e selecione **Criar**.
3.  Configure a escuta HTTP da seguinte forma:  
![][1]

4.  Quando configurar as definições do pacote, verá a opção seguinte na se a escuta deverá responder automaticamente ou exigir que para enviar uma resposta explícita. Defina esta para **Falso** para enviar a sua própria resposta:  
![][2]

5.  Clique em **OK** para criar.
6.  Assim que a instância de aplicação API é criada, abra as definições para configurar a segurança. A escuta HTTP atualmente suporta autenticação básica. Pode configurar esta através da opção de segurança quando abre a escuta HTTP:  
![][3]
  
    **Problema conhecido** Definições de *segurança a mostram "Nenhum", como o valor predefinido, no entanto, é indefinido. Tem de alterar a definição para Basic e voltar a nenhuma antes de guardá-la para se certificar de que o HTTP está configurado corretamente.*  

7. Por fim, defina as definições de segurança da aplicação API para público (acesso anónimo) para permitir que os clientes externos aceder ao ponto final. Esta definição está disponível em "todas as definições > definições da aplicação" da aplicação HTTP escuta API:![][10]

Uma vez que tenha feito, agora pode criar uma aplicação de lógica para utilizar a escuta HTTP.

## <a name="using-the-http-listener-in-your-logic-app"></a>Utilizar a escuta HTTP na sua aplicação de lógica
Assim que a sua aplicação API for criada, agora pode utilizar a escuta HTTP como um accionador para a sua aplicação de lógica. Para executar esta tarefa, tem de:

4.  Crie uma nova aplicação de lógica.
5.  Abra "Accionadores e ações" para abrir o estruturador de aplicações lógica e configurar o seu fluxo. A escuta HTTP está listada na Galeria de. Selecione-o.
6.  Agora pode definir o método de HTTP e o URL relativo no qual que requerem a escuta acionar o fluxo:  
![][4]  
![][5]

7.  Para obter o URI concluído, faça duplo clique no ponto de HTTP escuta para ver as suas definições de configuração e copie o URL para o "anfitrião" da sua aplicação API:  
![][6]
8.  Agora pode utilizar os dados recebidos no pedido de HTTP na outras ações no fluxo da seguinte forma:  
![][7]  
![][8]
9.  Por fim, para enviar uma resposta, adicione outro escuta HTTP e selecione a ação de enviar a resposta de HTTP. Defina o ID de pedir para RequestID obtido a escuta HTTP e preencher o corpo de resposta e o estado HTTP que pretender regressar novamente:  
![][9]

## <a name="using-the-http-action"></a>Utilizar a ação de HTTP
A ação de HTTP vierem é suportada pelo lógica de aplicações e não necessita de uma aplicação de API para ser criado primeiro possam utilizá-la. Pode inserir uma ação de HTTP em qualquer ponto na sua aplicação de lógica e escolha o URI, cabeçalhos e corpo para a chamada.
A ação de HTTP suporta várias opções para a segurança de lado do cliente. Ver as [Opções de segurança do lado do cliente](../scheduler/scheduler-outbound-authentication.md).

O resultado da ação HTTP é cabeçalhos e corpo, que pode ser utilizado ainda mais a jusante no fluxo de semelhante a como resultado de outras ações e conectores é consumido.

## <a name="do-more-with-your-connector"></a>Fazer mais com a conexão
Agora que a conexão for criada, pode adicioná-la para um fluxo de trabalho de empresas com uma aplicação de lógica. Consulte o artigo [quais são as aplicações de lógica?](app-service-logic-what-are-logic-apps.md).

Ver a referência Swagger REST API [API aplicações referência e conectores](http://go.microsoft.com/fwlink/p/?LinkId=529766).

Também pode rever a segurança estatísticas e controlo de desempenho ao conector. Consulte o artigo [Gerir e monitorizar o seu incorporada aplicações API e conectores](app-service-logic-monitor-your-connectors.md).

> [AZURE.NOTE] Se pretender começar com aplicações do Azure lógica antes de inscrever-se para uma conta do Azure, aceda à [Aplicação de lógica tente](https://tryappservice.azure.com/?appservice=logic). Imediatamente pode criar uma aplicação de lógica starter curto na aplicação de serviço. Sem cartões de crédito necessários; sem compromissos.

<!--Image references-->
[1]: ./media/app-service-logic-connector-http/1.png
[2]: ./media/app-service-logic-connector-http/2.png
[3]: ./media/app-service-logic-connector-http/3.png
[4]: ./media/app-service-logic-connector-http/4.png
[5]: ./media/app-service-logic-connector-http/5.png
[6]: ./media/app-service-logic-connector-http/6.png
[7]: ./media/app-service-logic-connector-http/7.png
[8]: ./media/app-service-logic-connector-http/8.png
[9]: ./media/app-service-logic-connector-http/9.png
[10]: ./media/app-service-logic-connector-http/10.png
