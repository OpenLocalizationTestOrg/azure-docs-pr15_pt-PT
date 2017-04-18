<properties
   pageTitle="Testar o desempenho da sua aplicação Azure web | Microsoft Azure"
   description="Execute testes de desempenho do Azure web app para verificar como a sua aplicação trata carga de utilizador. Medir o tempo de resposta e localize falhas que podem indicar problemas."
   services="app-service\web"
   documentationCenter=""
   authors="ecfan"
   manager="douge"
   editor="jimbe"/>

<tags
   ms.service="app-service-web"
   ms.workload="web"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="05/25/2016"
   ms.author="estfan; manasma; ahomer"/>

# <a name="performance-test-your-azure-web-app-under-load"></a>Desempenho testar a sua aplicação Azure web em caso de carga

Verifique o desempenho da sua aplicação web antes de iniciá-la ou implementar actualizações de produção. Desta forma, pode melhor avaliar se a sua aplicação está pronta para edição. Tiver dúvidas mais que a sua aplicação pode processar o tráfego durante a utilização de pico ou na sua próxima push de marketing.

Durante a pré-visualização pública, pode teste de desempenho a aplicação gratuitamente no Portal do Azure.
Estes testes simular carga de utilizador na sua aplicação durante um período de tempo específico e medem resposta da sua aplicação. Por exemplo, os resultados dos testes mostram como a mesma rapidez com a sua aplicação responde a um número específico de utilizadores. Que também sejam quantos pedidos falhou, que podem indicar problemas com a sua aplicação.      

![Encontrar problemas de desempenho na sua aplicação web](./media/app-service-web-app-performance-test/azure-np-perf-test-overview.png)

## <a name="before-you-start"></a>Antes de começar

* Tem uma [subscrição do Azure](https://account.windowsazure.com/subscriptions), se ainda não tiver um. Saiba como pode [Abrir uma conta Azure gratuitamente](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F).

* Terá de uma conta de [Serviços de equipa do Visual Studio](https://www.visualstudio.com/products/what-is-visual-studio-online-vs) para manter o histórico de teste de desempenho. Uma conta adequada será criada automaticamente quando configura o teste de desempenho. Ou pode criar uma nova conta ou utilizar uma conta existente se for o proprietário da conta. 

* Implemente a sua aplicação para testar a ligação num ambiente não sejam de produção. Tem a aplicação utilizar um plano de serviço de aplicação que não seja o plano utilizado na produção. Desta forma, não afeta clientes existentes ou diminuir a velocidade da sua aplicação de produção. 

## <a name="set-up-and-run-your-performance-test"></a>Configurar e executar o teste de desempenho

0.  Inicie sessão no [Portal do Azure](https://portal.azure.com). Para utilizar uma conta do Visual Studio Team Services que é o proprietário, inicie sessão no como o proprietário da conta.

0.  Aceda à sua aplicação web.

    ![Aceda ao procurar tudo, aplicações Web, a aplicação web](./media/app-service-web-app-performance-test/azure-np-web-apps.png)

0.  Vá para o **teste de desempenho**.

    ![Aceda a ferramentas, teste de desempenho](./media/app-service-web-app-performance-test/azure-np-web-app-details-tools-expanded.png)
 
0. Agora irá associar uma conta de [Serviços de equipa do Visual Studio](https://www.visualstudio.com/products/what-is-visual-studio-online-vs) para manter o histórico de teste de desempenho.

    Se tiver uma conta de serviços de equipa para utilizar, selecione essa conta. Se não, criar uma nova conta.

    ![Selecione a conta de serviços de equipa existente ou criar uma nova conta](./media/app-service-web-app-performance-test/azure-np-no-vso-account.png)

0.  Crie o teste de desempenho. Definir os detalhes e execute o teste. 

Pode ver os resultados em tempo real enquanto executa o teste.

Por exemplo, suponha que temos uma aplicação que deu saída cupões na venda de férias último ano. Este evento duração 15 minutos com uma carga de pico de 100 clientes em simultâneo. Queremos dobro do número de clientes este ano. Pretendemos também melhorar a satisfação do cliente reduzindo o tempo de carregamento de página a partir de 5 segundos para 2 segundos. Assim sendo, vamos testar desempenho nossa aplicação atualizada com 250 utilizadores para 15 minutos.

Vamos irá simular carga nossa aplicação gerando virtuais utilizadores (para clientes) quem visite o web site ao mesmo tempo. Isto vai mostrar-nos pedidos quantos estão a falhar ou responder lentamente.

  ![Criar, configurar e executar o teste de desempenho](./media/app-service-web-app-performance-test/azure-np-new-performance-test.png)

   *  URL da sua aplicação web predefinida é adicionado automaticamente. 
   Pode alterar o URL para testar a outras páginas (apenas para pedidos HTTP GET).

   *  Para simular condições locais e reduzir a latência, selecione uma localização mais próxima aos seus utilizadores para gerar a carga.

  Eis o teste em curso. Durante o primeiro minuto nossa página for carregada mais lenta do que pretendemos.

  ![Teste de desempenho em curso com dados em tempo real](./media/app-service-web-app-performance-test/azure-np-running-perf-test.png)

  Depois do teste estiver concluído, podemos saiba que a página for carregada muito mais rapidamente após o primeiro minuto. Isto ajuda a identificar onde poderá queremos começar a resolver o problema.

  ![Teste de desempenho concluída mostra os resultados, incluindo falhou pedidos](./media/app-service-web-app-performance-test/azure-np-perf-test-done.png)

## <a name="test-multiple-urls"></a>Testar vários URLs

Também pode executar testes de desempenho daquilo vários URLs que representam um cenário de utilizador do fim para fim ao carregar um ficheiro de teste de Web do Visual Studio. Algumas formas do pode criar um teste de Web Visual Studio ficheiro são:

* [Capturar tráfego utilizando Fiddler e exportar como um ficheiro de teste de Web do Visual Studio](http://docs.telerik.com/fiddler/Save-And-Load-Traffic/Tasks/VSWebTest)
* [Criar um ficheiro de teste de carga no Visual Studio](https://www.visualstudio.com/docs/test/performance-testing/run-performance-tests-app-before-release)

Para carregar e executar um ficheiro de teste de Web do Visual Studio:
 
0. Siga os passos acima para abrir o pá **testar o desempenho de novo** .
   Neste pá, selecione a opção CONFIGFURE testar utilizar para abrir o pá **Configurar teste utilizando** .  

    ![Abrir a carga de configurar testes pá](./media/app-service-web-app-performance-test/multiple-01-authoring-blade.png)

0. Verifique que o tipo de teste está definido para **Visual Studio Web teste** e selecione o ficheiro de arquivo de HTTP.
    Utilize o ícone de "pasta" para abrir a caixa de diálogo do Seletor de ficheiro.

    ![Carregar um ficheiro de teste do URL Visual Studio Web múltiplos](./media/app-service-web-app-performance-test/multiple-01-authoring-blade2.png)

    Depois do ficheiro foi carregado, verá a lista de URLs para ensaiar na secção de detalhes de URL.
 
0. Especificar a carga de utilizador e testar a duração e, em seguida, selecione **Executar teste**.

    ![Selecionar a carga de utilizador e a duração](./media/app-service-web-app-performance-test/multiple-01-authoring-blade3.png)

    Depois do teste for concluída, verá os resultados em dois painéis. Painel do lado esquerdo apresenta as informações de performnace como uma série de gráficos.

    ![Painel de resultados de desempenho](./media/app-service-web-app-performance-test/multiple-01a-results.png)

    Painel do lado direito mostra uma lista de pedidos de falhadas, com o tipo de erro e o número de vezes-ocorreu.

    ![O painel de falhas pedido](./media/app-service-web-app-performance-test/multiple-01b-results.png)

0. Volte a executar o teste ao selecionar o ícone de **voltar a executar** na parte superior do painel da direita.

    ![Voltar a executar o teste](./media/app-service-web-app-performance-test/multiple-rerun-test.png)

##  <a name="q--a"></a>As perguntas e respostas

#### <a name="q-is-there-a-limit-on-how-long-i-can-run-a-test"></a>P: existe um limite no quanto tempo pode executar um teste? 

**A**: Sim, pode executar o teste até uma hora no Portal do Azure.

#### <a name="q-how-much-time-do-i-get-to-run-performance-tests"></a>P: quanto tempo é recebo executar testes de desempenho? 

**A**: após a pré-visualizar pública, obtém 20.000 utilizador virtual minutos (VUMs) de livre cada mês com a sua conta de serviços de equipa do Visual Studio. Um VUM é o número de utilizadores virtuais multiplicados pelo número de minutos no seu teste. Se as suas necessidades excederem o limite gratuito, pode comprar mais tempo e pagar apenas para que utiliza.

#### <a name="q-where-can-i-check-how-many-vums-ive-used-so-far"></a>P: onde pode verificar os VUMs quantos que utilizou até ao momento?

**A**: pode verificar este valor no Portal do Azure.

![Aceda à sua conta de serviços de equipa](./media/app-service-web-app-performance-test/azure-np-vso-accounts.png)

![Verificar VUMs utilizados](./media/app-service-web-app-performance-test/azure-np-vso-accounts-vum-summary.png)

#### <a name="q-what-is-the-default-option-and-are-my-existing-tests-impacted"></a>P: qual é a opção predefinida e são afetados do meu testes existentes?

**A**: A opção predefinida para testes de carga de desempenho é um teste manual - o mesmo antes do URL de vários teste opção foi adicionado ao portal.
Testes existentes continuam a utilizar o URL configurado e irão funcionar como anteriormente.

#### <a name="q-what-features-not-supported-in-the-visual-studio-web-test-file"></a>P: o que funcionalidades não suportadas no ficheiro de teste de Web do Visual Studio?

**A**: momento esta funcionalidade não suporta os plug-ins, origens de dados e regras de extração de teste Web. Tem de editar o seu ficheiro de teste Web para as remover. Vamos Espero adicionar suporte para as seguintes funcionalidades no futuro atualizações de.

#### <a name="q-does-it-support-any-other-web-test-file-formats"></a>P:-suporta quaisquer outros formatos de ficheiro de teste Web?
  
**A**: A apresentar apenas Visual Studio teste Web ficheiros de formato são suportados.
Vamos seria satisfeitos a sua opinião se precisar de suporte para outros formatos de ficheiro. Enviar por correio eletrónico-na [vsoloadtest@microsoft.com](mailto:vsoloadtest@microsoft.com).

#### <a name="q-what-else-can-i-do-with-a-visual-studio-team-services-account"></a>P: que mais posso fazer com uma conta do Visual Studio Team Services?

**A**: para localizar a sua nova conta, aceda a ```https://{accountname}.visualstudio.com```. Partilhar o seu código, construir, teste, controlar o trabalho e software de envio – todas na nuvem utilizando qualquer ferramenta ou idioma. Saiba mais sobre como os [Serviços de equipa do Visual Studio](https://www.visualstudio.com/products/what-is-visual-studio-online-vs) funcionalidades e serviços ajudam a sua equipa colaborar mais facilmente e implementar continuamente.

## <a name="see-also"></a>Consulte também

* [Executar testes de desempenho da nuvem simples](https://www.visualstudio.com/docs/test/performance-testing/getting-started/get-started-simple-cloud-load-test)
* [Executar Apache Jmeter testes de desempenho](https://www.visualstudio.com/docs/test/performance-testing/getting-started/get-started-jmeter-test)
* [Gravar e reproduzir os testes de carga baseado na nuvem](https://www.visualstudio.com/docs/test/performance-testing/getting-started/record-and-replay-cloud-load-tests)
* [Desempenho testar a sua aplicação na nuvem](https://www.visualstudio.com/docs/test/performance-testing/getting-started/getting-started-with-performance-testing)
