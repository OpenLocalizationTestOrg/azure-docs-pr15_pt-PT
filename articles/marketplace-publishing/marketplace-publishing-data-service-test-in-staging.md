<properties
   pageTitle="Testar a sua oferta de serviço de dados do Marketplace | Microsoft Azure"
   description="Compreenda como testar a sua oferta de serviço de dados do Azure Marketplace."
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

# <a name="testing-your-data-service-offer-in-staging"></a>Testar a sua oferta de serviço de dados em transição

>[AZURE.IMPORTANT] **Neste momento podemos já não são ativação qualquer novos fabricantes de serviço de dados. Novo dataservices não irá obter aprovado para obter uma lista.** Se tiver uma aplicação de empresas SaaS que gostaria de publicar no AppSource pode encontrar mais informações [aqui](https://appsource.microsoft.com/partners). Se tiver um aplicações IaaS ou serviço para programadores que gostaria de publicar no Azure Marketplace pode encontrar mais informações [aqui](https://azure.microsoft.com/marketplace/programs/certified/).

Depois de concluir os dois primeiros passos de [criar a sua conta Microsoft Developer](marketplace-publishing-accounts-creation-registration.md) e [criar a sua oferta de serviço de dados no Portal de publicação](marketplace-publishing-data-service-creation.md) , está pronto para fazer a sua oferta disponíveis no Azure Marketplace. Este tópico irá guiá-lo o primeiro, intermédio, passo denominado "Transição"

Teste significa implementar a sua oferta em privado "sandbox" onde pode testar e verificar a sua funcionalidade antes da instalação push-produção. A oferta serão apresentados na transição apenas ao que teria para um cliente que tenha implementado-lo.

## <a name="step-1-pushing-your-offer-to-staging"></a>Passo 1. Direccionar a oferta de transição
Direccionar a oferta de transição permite-lhe testar a oferta antes de as torna-se disponível para subscritores futuros.  Pode ver como a sua oferta irá aparecer e funcionar para aqueles subscrever para os seus dados.  

  ![desenho](media/marketplace-publishing-data-service-test-in-staging/step-1.1.png)

1.  Início de sessão no [Portal de publicação](https://publish.windowsazure.com)
2.  Selecione **Serviços de dados** na janela de navegação à esquerda
3.  Selecione a sua oferta que pretende para transmitir para transição. Irá ver o ecrã acima.
4.  Clique em botão de **Notificações Push para transição** .  
5.  Se existirem problemas com a oferta de que se necessário, para ser concluída antes da instalação push transição, irá ver uma lista apresentada.  Corrigi estes itens, clicando em cada item na lista. Quando todas as correções efetuadas, clique novamente no botão de **notificações Push para transição** .

Se não existem problemas com a sua oferta irá ver a janela de pop-up abaixo.  

Se que está a planear não/não aprovado para revelar a sua oferta no Portal do Azure (atualmente tem limitado capacidade), em seguida, feche simplesmente a janela de pop-up.

Para testar o seu serviço de dados no Portal do Azure (para além de DataMarket portal), será necessário um ID da subscrição Azure para testar com.  Este ID da subscrição irá identificar a conta que é permitida para testar a sua oferta.  

Cortar e colar o seu ID da subscrição e clique em marca de verificação para continuar.

  ![desenho](media/marketplace-publishing-data-service-test-in-staging/step-1.2.png)

> [AZURE.NOTE] Destas subscrições Azure IDs só são necessários para testar e de transição no [Portal de gestão do Azure](https://manage.windowsazure.com). Que não são necessárias para testar no Azure Marketplace.

O ecrã seguinte que aparece mostra a publicação está a demorar local ao apresentar o ícone "em curso" realçada amarelo abaixo. A instalação push transição leva entre 10 a 15 minutos.  Se demora mais tempo, pela primeira vez Atualize o seu browser (prima F5 no IE).  Nos casos raros onde a sua oferta é ainda a instalação push transição após uma hora, clique no contacto-na ligação para saber diga-no que existe um problema.

  ![desenho](media/marketplace-publishing-data-service-test-in-staging/step-1.3.png)

Quando Push para transição concluir o ícone "em curso" irão mover parar e o estado serão atualizadas para "Faseada".  Agora está pronto para testar a sua oferta.  

## <a name="step-2-test-your-staged-offer-in-datamarket"></a>Passo 2. Testar a sua oferta faseada na DataMarket

Clique na ligação seguir o texto **"Vejam o seu serviço oferecer a..."** para apresentar o ecrã que subscritor verão quando a sua oferta acede à produção e aparecerá no DataMarket.

  ![desenho](media/marketplace-publishing-data-service-test-in-staging/step-2.2.png)

Testar ou verificar cada um dos 12 itens marcadas acima para garantir que todos os logótipos preços/transações, texto, imagens, documentação e as ligações estão corretos e a funcionar corretamente.  Esta é uma boa altura para garantir que quaisquer valores de teste que introduziu quando criar a sua oferta foram substituídas pelos valores reais.

1. Logótipo de oferta
2. Nome da oferta
3. Publisher nome/ligação para o Web site da sua empresa
4. Categorias de pesquisa para a sua oferta
5. Ligação para a sua oferta o suporte para o ajudar a subscritores
6. Contextual descrição para a sua oferta
7. Plano de oferta ilustrando detalhes da faturação
8. Ligação para o código de implementação
9. Utilizam imagens de exemplo que ilustram o modo de dados de oferta
10. Entrada/saída metadados para cada serviço dentro da oferta
11. Termos da oferta de utilização
12. Pré-visualização de dados a oferta


Por fim, selecione que o serviço irá funcionar através de Datamarket ao clicar na ligação "EXPLORAR este conjunto de dados".  Será aberta uma nova janela na ferramenta de chamamos de "Serviço o Explorador" para que possa pré-visualizar os resultados de uma consulta contra seu serviço.  Nesta janela, pode introduzir os parâmetros conforme necessário e ver os resultados apresentados a partir de uma consulta contra seu serviço.   Além disso, é apresentada o URL para a sua consulta.  

> [AZURE.NOTE] Certifique-se de que reveja a descrição do serviço apresentada na parte superior de textual.  E se a sua oferta consiste em mais de um serviço de chamadas, clique nos separadores na parte inferior para mudar para o serviço seguinte para rever e testar.



## <a name="next-step"></a>Passo seguinte
Se estiver a ter problemas e precisa de ajuda para resolver contacte o [Suporte do Azure Publisher]( http://go.microsoft.com/fwlink/?LinkId=272975).

Se estiver satisfeito e pronto para publicar a sua oferta leia a documentação de [Pedir aprovação para notificações Push para produção](marketplace-publishing-push-to-production.md) .

## <a name="see-also"></a>Consulte também
- [Introdução: Como publicar uma oferta ao Azure Marketplace](marketplace-publishing-getting-started.md)
