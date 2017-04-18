<properties
    pageTitle="Descrição geral aprofundada de planos do Azure de aplicação de serviço | Microsoft Azure"
    description="Saiba como a aplicação de serviço de planos para o trabalho de aplicação de serviço do Azure e como estas beneficiam a sua experiência de gestão."
    keywords="aplicação, azure aplicação do serviço, plano de serviço de aplicação dimensionáveis, do escala, custo do serviço de aplicação"
    services="app-service"
    documentationCenter=""
    authors="btardif"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/13/2016"
    ms.author="byvinyal"/>

# <a name="azure-app-service-plans-in-depth-overview"></a>Descrição geral aprofundada de planos do Azure de aplicação de serviço#

Um plano de serviço de aplicação representa um conjunto de funcionalidades e capacidades que possa partilhar entre várias aplicações. Aplicações Web, aplicações Mobile, função aplicações ou aplicações de API, na [Aplicação de serviço de Azure](http://go.microsoft.com/fwlink/?LinkId=529714) todas as executar num plano de serviço de aplicação. Estes planos cinco camadas preços de suporte: *livre*, *partilhado*, *básica*, *padrão*e *Premium*. Cada camada tem as suas próprias capacidades e a capacidade. Aplicações na mesma subscrição e localização geográfica podem partilhar um plano. Podem utilizar todas as aplicações que partilham um plano de todas as funcionalidades e funcionalidades que são definidas pelas camada do plano. Executar todas as aplicações que estão associadas um plano nos recursos que define o plano.

Por exemplo, se o seu plano está configurado para utilizar duas instâncias de "pequenas" na camada serviço padrão, todas as aplicações que estão associadas esse plano executar em ambas as instâncias e têm acesso à funcionalidade camada serviço padrão. Plano de instâncias em que estiver a executar o aplicações estão totalmente geridas e altamente disponíveis.

Este artigo explora as características-chave, tais como a camada e escala, de um plano de serviço de aplicação e como entrem em reprodução durante a gerir as suas aplicações.

## <a name="apps-and-app-service-plans"></a>Aplicações e planos do serviço de aplicação

Uma aplicação na aplicação de serviço pode ser associada a apenas um plano de aplicação de serviço a qualquer momento.

Aplicações e planos estão contidos num grupo de recursos. Um grupo de recursos serve o limite do ciclo de vida para cada recurso que está a dentro da mesma. Pode utilizar grupos de recursos para gerir todas as partes de uma aplicação em conjunto.

Uma vez que um grupo de recursos única pode ter vários planos de serviço de aplicação, pode atribuir diferentes aplicações para recursos físicas diferentes. Por exemplo, pode separar recursos entre ambientes Dev Center, teste e produção. Está com dificuldades em separado ambientes de produção e Dev Center/testar permite-lhe identificar recursos. Desta forma, carga testes contra uma nova versão das suas aplicações não competir para os mesmos recursos como as aplicações de produção, que estão a servir clientes reais.

Quando tiver vários planos num grupo de recursos única, também pode definir uma aplicação que abrange regiões geográficas. Por exemplo, uma aplicação altamente disponível a ser executada em duas áreas inclui planos, pelo menos, dois, uma para cada região e uma aplicação associada a cada plano. Situação, todas as cópias da aplicação, em seguida, estão contidas num grupo de recursos único. Ter um grupo de recursos com vários planos e apps vários torna mais fácil gerir, controlar e ver o estado de funcionamento da aplicação.

## <a name="create-an-app-service-plan-or-use-existing-one"></a>Criar um plano de serviço de aplicação ou utilize um já existente

Quando cria uma aplicação, deverá tomar em consideração de criação de um grupo de recursos. Por outro lado, se a aplicação que está prestes a criar um componente de uma aplicação de maior, esta aplicação deverá ser criada dentro do grupo de recursos que está atribuído para essa aplicação maior.

Se a nova aplicação é um completamente nova aplicação ou a parte de um maior, pode optar por utilizar um plano de serviço de aplicação existente para alojá-la ou criar um novo. Esta decisão está mais uma pergunta da capacidade de e carga esperada.

Se esta nova aplicação vai utilizar muitos recursos e tem diferentes factores a partir de outras aplicações de escala alojado numa planta existente, recomendamos que isolá-las no seu próprio plano.

Quando cria um plano, pode atribuir um novo conjunto de recursos para a sua aplicação e obter maior controlo sobre alocação de recursos, uma vez que cada um dos planos obtém o seu próprio conjunto de instâncias.

Uma vez que pode mover aplicações nos planos, pode alterar a forma como os recursos são atribuídos ao longo da aplicação maior.

Por fim, se pretende criar uma aplicação numa região diferente e esse região não tem um plano existente, crie um plano nesse região possam-se de que não existem a sua aplicação do anfitrião.

## <a name="create-an-app-service-plan"></a>Criar um plano de serviço de aplicação

>[AZURE.TIP] Se tiver um ambiente do serviço de aplicação pode rever a documentação específica a aplicação de serviço ambientes aqui: [criar um plano de serviço de aplicação num ambiente do serviço de aplicação](../app-service-web/app-service-web-how-to-create-a-web-app-in-an-ase.md#createplan)

Pode criar um plano de serviço de aplicação vazio a experiência de procurar do plano de serviço de aplicação ou como parte de criação de aplicação.

No [portal do Azure](https://portal.azure.com), clique em **Novo** > **Web + mobile**e, em seguida, selecione **Web App** ou outro tipo de aplicação de serviço de aplicação.
![Crie uma aplicação no portal do Azure.][createWebApp]

Pode, em seguida, selecione ou criar o plano de serviço de aplicação para a nova aplicação.

 ![Crie um plano de serviço de aplicação.][createASP]

Para criar um novo plano de serviço de aplicação, clique em **[+] Criar nova**, escreva o nome do **plano de serviço de aplicação** e, em seguida, selecione uma **localização**de apropriada. Clique em **níveis de preços**e, em seguida, selecione uma camada comparar adequada para o serviço. Selecione **Ver tudo** para ver mais opções de preços, tal como **gratuitos** e **partilhado**. Depois de selecionar a definição de preços camada, clique no botão **Selecionar** .

## <a name="move-an-app-to-a-different-app-service-plan"></a>Deslocar-se uma aplicação para um plano diferente do serviço de aplicação

Pode mover uma aplicação para um plano de serviço de aplicação diferente no [portal do Azure](https://portal.azure.com). Pode mover aplicações entre planos, desde que os planos são no mesmo grupo de recursos e à região geográfica errada.

Para mover uma aplicação para outro plano, vá para a aplicação que pretende mover. No menu **Definições** , procure o **Plano de serviço de aplicação de alteração**.

**Plano de serviço de aplicação alterar** abre-se no Seletor de **plano de serviço de aplicação** . Neste momento, pode escolher um esquema existente ou crie um novo. Apenas válidos planos (no mesmo grupo de recursos e localização geográfica) são apresentados.

![Selector de plano de serviço de aplicação.][change]

Cada um dos planos tem as suas próprias de preços de camadas. Por exemplo, quando move um site a partir de uma camada gratuita para uma camada padrão, a aplicação agora pode utilizar todas as funcionalidades e recursos da camada padrão.

## <a name="clone-an-app-to-a-different-app-service-plan"></a>Clonar uma aplicação para um plano diferente do serviço de aplicação
Se quiser mover a aplicação para uma região diferente, uma alternativa é aplicação clonar. Clonar faz uma cópia da sua aplicação no plano de aplicação de serviço de novo ou existente ou num ambiente de aplicação de serviço em qualquer região.

 ![Clonar uma aplicação.][appclone]

Pode encontrar **Clonar aplicação** no menu **Ferramentas** .

Clonar tem algumas limitações que pode ler sobre na [aplicação de serviço de aplicação do Azure clonar através do portal Azure](../app-service-web/app-service-web-app-cloning-portal.md).

## <a name="scale-an-app-service-plan"></a>Dimensionar um plano de serviço de aplicação

Existem três formas de dimensionar um plano:

- **Alterar o plano preços de camadas**. Por exemplo, um plano na camada básica pode ser convertido para uma camada padrão ou Premium e todas as aplicações que estão associadas esse plano agora podem utilizar as funcionalidades que a nova camada de serviço oferece.
- **Alterar o tamanho da instância do plano**. Por exemplo, um plano na camada básica que utiliza instâncias pequenas pode ser alterado para utilizar instâncias de grandes dimensões. Todas as aplicações que estão associadas esse plano agora podem utilizar a memória adicional e recursos de CPU que o tamanho da instância maior oferece.
- **Alterar a contagem de instâncias do plano**. Por exemplo, um plano padrão que é dimensionado saída para três instâncias pode ser dimensionado para 10 instâncias. Um plano Premium pode ser dimensionado saída 20 instâncias (sujeito a disponibilidade). Todas as aplicações que estão associadas esse plano agora podem utilizar a memória adicional e recursos de CPU que oferece a contagem de instância maior.

Pode alterar o tamanho da camada e instância comparar ao clicar em **Escala o** item em definições para a aplicação ou o plano de serviço de aplicação. Alterações aplicam-se para o plano de serviço de aplicação e afetam todas as aplicações que aloja a mesma.

 ![Definir valores escalar para cima uma aplicação.][pricingtier]

## <a name="app-service-plan-cleanup"></a>Limpeza de plano de serviço de aplicação
**Planos do serviço de aplicação** que tenham sem aplicações associadas às mesmas assumir ainda taxas de uma vez que continuem a reserva a capacidade de cluster configurada nas propriedades de escala de plano do serviço de aplicação.
Para evitar taxas inesperadas, quando a aplicação última alojada no plano de uma aplicação de serviço é eliminada, o plano de serviço de aplicação vazio resultante também é eliminado.


## <a name="summary"></a>Resumo

Planos do serviço de aplicação representam um conjunto de funcionalidades e capacidades que pode partilhar nas suas aplicações. Planos do serviço de aplicação dar-lhe a flexibilidade de poder alocar aplicações específicas para um conjunto de recursos e otimizar ainda mais a utilização do recurso Azure. Desta forma, se pretender guardar dinheiro no seu ambiente de teste, pode partilhar um plano de entre vários aplicações. Também pode maximizar o débito para o seu ambiente de produção por dimensionamento-lo em múltiplas regiões e planos.

## <a name="whats-changed"></a>O que é alterado

* Para um guia para a alteração de Web sites para a aplicação de serviço, consulte o artigo: [aplicação de serviço de Azure e respectivo impacto na existente dos serviços do Azure](http://go.microsoft.com/fwlink/?LinkId=529714)

[pricingtier]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/appserviceplan-pricingtier.png
[assign]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/assing-appserviceplan.png
[change]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/change-appserviceplan.png
[createASP]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-appserviceplan.png
[createWebApp]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/create-web-app.png
[appclone]: ./media/azure-web-sites-web-hosting-plans-in-depth-overview/app-clone.png
