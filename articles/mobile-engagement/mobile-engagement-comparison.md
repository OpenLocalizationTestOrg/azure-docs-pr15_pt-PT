<properties
    pageTitle="Comparar o Azure Mobile Cativação com outros serviços do Azure semelhantes"
    description="Comparar o Azure Mobile Cativação com outros serviços do Azure semelhantes - HockeyApp, AppInsights, concentradores de notificação"
    services="mobile-engagement"
    documentationCenter="mobile" 
    authors="piyushjo" 
    manager="erikre" 
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/19/2016"
    ms.author="piyushjo" />

# <a name="comparing-azure-mobile-engagement-with-other-similar-azure-services"></a>Comparar o Azure Mobile Cativação com outros serviços do Azure semelhantes

Alguma vez é expandir a lista de serviços oferecidos pelo Microsoft Azure e por vezes poderá dúvidas como é o Azure Mobile Cativação diferente este serviço que acabou de ler ou ouvir sobre. Este artigo tentativas para limpar a ambiguidade e direciona para selecionar o Azure Mobile Cativação quando é mais adequado para a sua utilização. 
 
Azure Mobile Cativação é um serviço atribuído especificamente **para os comerciantes/CMOs digitais** mas pode ser utilizado por qualquer **proprietário da aplicação móvel ou publisher** que pretenda aumentar a utilização, retenção e monetização das suas aplicações móveis. 

*É uma plataforma Cativação de utilizador do software-como-a-service (SaaS) que fornece informações baseadas em dados para a utilização de aplicação, segmentação de utilizador em tempo real e permite que as notificações push contextually ciente e mensagens de na aplicação.* 

Dividir as esta definição, temos as seguintes características principais que também realça os seu proposta de valor exclusivo:

1.  **Notificações push contextually ciente e mensagens de na aplicação**
        
    Este é o foco core do produto - executar as notificações push alvo e personalizada. E para que isto acontecer, vamos recolher dados de análise comportamento avançada. 

2.  **Informações baseadas em dados para a utilização de aplicação**

    Fornecemos plataforma cruzada SDK para recolher as análises de comportamentais sobre os utilizadores da aplicação. Tenha em atenção as análises de comportamento de termo (contra a análise de desempenho) porque vamos concentrar-nos como os utilizadores da aplicação estão a utilizar a aplicação. Vamos recolher dados de análise de desempenho básicas sobre erros, falhas etc no entanto, ou seja não o foco core do produto. 

3.  **Segmentação de utilizador em tempo real**

    Depois de ter reunido dados de análise comportamento dos utilizadores de aplicação, podemos permitem-lhe para segmento a audiência com base em vários parâmetros e recolhidas dados para permitem-lhe executar campanhas de push direccionado. 

4.  **Software-como-a-service (SaaS):**

    Temos um portal de separada a partir do portal de gestão Azure que está optimizado para interagir e ver análises de comportamentais ricos sobre os utilizadores da aplicação e executar push campanhas de marketing. O produto destina-se para o ajudar a correr de sem tempo!   
 
Com este conjunto de diferenciação mão, eis como podemos comparar com outros ofertas Azure aparentemente semelhantes - nota que o artigo não executa uma comparação de nível de funcionalidade, mas leva-o até um cenário mais com base abordagem para definir qual o produto que melhor se adequa:
 
1.  [HockeyApp](https://azure.microsoft.com/services/hockeyapp/) é uma solução de DevOps móvel da Microsoft. Com o mesmo, pode distribuir compilações para testes beta, recolher dados de falha de sistema e obter comentários de utilizador. Também integra com o Visual Studio Team Services ativação de compilação fácil implementações e integração de item de trabalho. 
    
    É aqui o foco no DevOps e recolher dados de análise de desempenho sobre as aplicações móveis. Poderá conclui-lo com integração de ambas as HockeyApps e Mobile Cativação na sua aplicação e que não será invulgar porque, apesar de não existem algumas sobreposição nos dados recolhidos, o foco core dos produtos é diferente e ajudar a alcançar objetivos diferentes para si.  

2.  [Informações de aplicação](../application-insights/app-insights-overview.md) Se a sua aplicação móvel tiver do lado do servidor, em seguida, que irá utilizar a aplicação informações para monitorizar lado do servidor web da sua aplicação mas para as cliente lado aplicações móveis, deve utilizar HockeyApp. 

3.  [Notificação concentradores](https://azure.microsoft.com/services/notification-hubs/) fornece um serviço lightweight no sentido em que não precisa de um SDK integrada na aplicação móvel e pode ter controlo total da sua aplicação móvel e -permite o envio de notificações push com as funcionalidades básicas de empresa. Este é ótimo para qualquer pelo proprietário aplicação respeita menor sobre filtragem e mais informações de envio/comunicar instantaneamente aos seus utilizadores da aplicação (difusão para uma grande população). 

    Tenha em atenção o foco sobre o envio de blazing notificações rápidas com capacidade de segmentação de base. 

Vamos alguns cenários:

1.  Miguel faz parte da equipa de marketing digital na loja Adventure Works que publica aplicações móveis para os utilizadores. Objetivo de Miguel é para se certificar de que os utilizadores a quem transferir as suas aplicações móveis continuem a utilizá-la e frequentemente. Este aumenta não apenas uma marca corporativa anexar com os utilizadores da aplicação, mas também os aumentos monetização quando os utilizadores da aplicação efectuam compras com a aplicação móvel. Para este Miguel será necessário enviar notificações orientadas para os utilizadores da aplicação, que são úteis encorajá-los para abrir a aplicação e regresse ao mesmo frequentemente. Este é onde Miguel irá encontrar Mobile Cativação úteis. 

2.  Joann faz parte da equipa de desenvolvimento das aplicações móveis do Adventure Works e está à procura de um produto registar os detalhes sobre a causa uma falha, exceções e obter telemetria de desempenho a partir de uma aplicação. Este é onde Joann irá encontrar HockeyApp úteis. Joann poderia integrar o ambos HockeyApp para respetivos cenários de programador com foco nos e Azure Mobile Cativação para Miguel na mesma aplicação para tirar o rendimento de ambos. 

3.  Robin faz parte da equipa de desenvolvimento de aplicações móveis na rede da Contoso notícias e todos os que ela pretende consiste em enviar saída força de alertas de notícias para todos os utilizadores sem muito segmentação assim que o alerta de notícias é acionado. Este é onde Robin irá encontrar notificação concentradores úteis. Neste cenário, é possível no entanto que tal como a aplicação móvel casos, existe um requisito para fazer muito mais rica segmentação e obter detalhes sobre o comportamento do utilizador de aplicação. Neste momento, Robin terá de avaliar Azure Mobile Cativação. 
 
Para recap, a finalidade de Cativação Mobile não está apenas para recolher analytics - não é "ainda outro produto de análise da Microsoft". É sobre o envio de notificações push alvo e para este filtragem, vamos recolher dados de análise comportamento mas o foco permanece no envio de notificações push qual mais adequadas para os utilizadores da aplicação para que não deparar como spam. 

Para obter mais detalhes - veja este [vídeo rápido](mobile-engagement-overview.md) sobre Mobile Cativação muito simplesmente. 

