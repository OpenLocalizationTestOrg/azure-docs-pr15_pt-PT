<properties 
    pageTitle="Azure Mobile Cativação pós-implementação para a aplicação de jogos"
    description="Cenário de aplicação de jogos para implementar o Azure Mobile Cativação" 
    services="mobile-engagement" 
    documentationCenter="mobile" 
    authors="piyushjo"
    manager="dwrede"
    editor=""/>

<tags
    ms.service="mobile-engagement"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="mobile-multiple"
    ms.workload="mobile" 
    ms.date="08/19/2016"
    ms.author="piyushjo"/>

#<a name="implement-mobile-engagement-with-gaming-app"></a>Implementar Cativação móvel com a aplicação de jogos

## <a name="overview"></a>Descrição geral

Um arranque jogos foi lançada uma nova aplicação jogo de role play/estratégia de sugestivos com base. O jogo foi a trabalhar para 6 meses. Este jogo é um sucesso enorme e tiver milhões de transferências e a retenção é muito alta em comparação com outras aplicações de jogos de arranque. Na reunião rever trimestrais, os intervenientes concorda com que necessitam para aumentar a receita média por utilizador (ARPU). Pacotes de no jogo Premium estão disponíveis como ofertas especiais. Estes packs jogos permitem aos utilizadores atualizar o aspecto e o desempenho das suas linhas sugestivos e lures ou tackles no jogo. No entanto, as vendas de pacote são muito baixas. Assim decidam pela primeira vez analisar a experiência do cliente com uma ferramenta de análise e, em seguida, desenvolver uma Cativação programa para aumentar a utilizar o vendas avançadas segmentação.

Com base no [Azure Mobile Cativação - guia de introdução com as melhores práticas](mobile-engagement-getting-started-best-practices.md) que criam uma estratégia de Cativação.

##<a name="objectives-and-kpis"></a>Objetivos e KPIs

Se reunirem intervenientes-chave para o jogo. Todos os concordem sobre objectivo principal um - para aumentar as vendas do pacote de premium por 15%. Criam empresas indicadores chave de desempenho (KPIs) para medida e unidade este objectivo

* No qual o nível do jogo são estes pacotes comprados?
* O que é a receita por utilizador, por sessão, por semana e por mês?
* Quais são os tipos de compra favoritas?

Parte 1 do [Guia de introdução](mobile-engagement-getting-started-best-practices.md) explica como definir os objetivos e KPIs. 

Com os KPIs de negócio definido agora, o Gestor de produto Mobile cria Cativação KPIs para determinar o novo tendências de utilizador e retenção.

* Monitorizar retenção e utilizar nos seguintes intervalos: diariamente, cada 2 dias, semanalmente, mensalmente e todas as 3 meses
* Conta de utilizador ativo
* A classificação de aplicação na loja

Com base nas recomendações da equipa do IT, os KPIs técnicos seguintes foram adicionados para atender as seguintes questões:

* O que é o meu caminho do utilizador (qual a página for visitada, quanto vez que os utilizadores passam no mesmo)
* Número de falhas ou erros encontrados por sessão
* Versões de SO quais os meus utilizadores a executar o?
* O que é o tamanho médio do ecrã para os meus utilizadores?
* Que tipo de ligação à internet possui os meus utilizadores?

Para cada KPI o Gestor de produto Mobile Especifica os dados ela necessita e onde se encontra na sua playbook.

## <a name="engagement-program-and-integration"></a>Programa de Cativação e integração

Antes de criar um programa de Cativação avançadas, o Director de projeto Mobile responsável pelo projeto deve ter uma compreensão abrangente de como e quando os produtos estão consumidos pelos utilizadores.

Depois de 3 meses, o Director de projeto Mobile tenha recolhido dados suficientes para melhorar a suas na aplicação push notificação de vendas. Ele aprende que:

* Na primeira compra geralmente acontece ao nível de 14. Para 90% nestes casos, a compra é novas armas lendários para $3.
* No 80% nestes casos, os utilizadores que tenham efetuado de uma compra única, continue com o produto e fazer mais compras.
* Os utilizadores que passaram o nível de 20, começar a passam a mais do que $10/ semana.
* Os utilizadores tendem para comprar pacotes de premium ao nível 16, 24 e 32.

Graças a esta análise Mobile projeto Director decide criar push específico sequências de notificação para aumentar nas vendas de aplicação. Ele cria três sequências de push que ele chama: bem-vindo ao programa, o programa de vendas e o programa inativa. Para obter mais informações, consulte os [guias de planeamento](https://github.com/Azure/azure-mobile-engagement-samples/tree/master/Playbooks)
    ![][1]

<!--Image references-->

[1]: ./media/mobile-engagement-game-scenario/notification-scenario.png

<!--Link references-->
