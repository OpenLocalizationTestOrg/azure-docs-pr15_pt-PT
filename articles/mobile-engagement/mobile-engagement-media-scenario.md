<properties 
    pageTitle="Azure Mobile Cativação pós-implementação para a aplicação de multimédia"
    description="Cenário de aplicação de multimédia para implementar o Azure Mobile Cativação" 
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

#<a name="implement-mobile-engagement-with-media-app"></a>Implementar Cativação móvel com a aplicação de multimédia

## <a name="overview"></a>Descrição geral

João é um Gestor de projeto móvel numa empresa de multimédia grande. Ele recentemente iniciada uma nova aplicação que tem uma contagem de transferência muito alta. Ele atingiu dele objetivos para transferência, mas ainda dele devolvido no Investment(ROI) por utilizador não cumprir os requisitos. 

João identificou já a razão pela qual o seu ROI está demasiado baixo. Os utilizadores deixar de utilizar a sua aplicação após apenas 2 semanas frequentemente e a maior parte dos mesmos nunca voltar atrás. Ele pretende aumentar a retenção da sua aplicação.

Depois de alguns inicial testes, ele tem aprendeu quando ele realiza dele utilizadores com as notificações push, tendência para continuar a utilizar a sua aplicação. Também utilizadores que tenham sido Inativos frequentemente irão regressar à aplicação dependendo notificações que ele envia-los. João decide investir em qualquer tipo de Cativação programa para a sua aplicação que utiliza a filtragem avançadas com as notificações push.

João recentemente tem leia o [Azure Mobile Cativação - guia de introdução com as melhores práticas](mobile-engagement-getting-started-best-practices.md) e tiver decidido implementar as recomendações entre a guia da.

##<a name="objectives-and-kpis"></a>Objetivos e KPIs

Se reunirem intervenientes-chave para a aplicação de João. Empresas são gerada a partir de anúncios como os utilizadores consumam dele multimédia. Aumentando conteúdo consumido por utilizador, João aumenta dele receitas. Todos os concordem sobre um objectivo principal: para aumentar as vendas de anúncios por 25%. Criam empresas indicadores chave de desempenho (KPIs) para medida e unidade este objectivo

* Número de anúncios clicado por utilizador
* Quantos páginas de artigo visitadas (por utilizador / por sessão / por semana / por mês...)
* O que são categorias favoritas

Com base na reunião João com chaves intervenientes ele definiu dele KPIs de empresas. Ele segue parte 1 do [Azure Mobile Cativação - guia de introdução com as melhores práticas](mobile-engagement-getting-started-best-practices.md). 

Em seguida, ele cria os seguintes KPIs de Cativação para se certificar de que são atingidos objetivos:

* Monitorizar retenção entre os seguintes intervalos: diária, semanal, bi semanal e mensal.
* Contagens de utilizadores ativos
* A classificação de aplicação na aplicação armazena

Com base nas recomendações da equipa do IT, os KPIs técnico seguintes foram adicionados para atender as seguintes questões:

* O que é o meu caminho do utilizador (qual a página for visitada, quantos utilizadores de tempo gastam em-lo)
* Número de falhas ou erros encontrados por sessão?
* Versões de SO quais os meus utilizadores a executar o?
* O que é o tamanho médio do ecrã para os meus utilizadores?
* Que tipo de ligações à internet possui os meus utilizadores?

Para cada KPI, ele classifica os dados necessários e ele grava-o na localização da sua playbook inicial maiúscula.

## <a name="engagement-program-and-integration"></a>Programa de Cativação e integração

Agora que João já terminou definir dele KPIs, ele começa dele fase de estratégia de Cativação através da definição de programas de Cativação 4 e aos seus objectivos:    ![][1]

Em seguida, João vai mais aprofundado por com detalhes sobre as notificações push para cada programa. Notificações push são definidos pelas cinco elementos:

1. Objectivo: o que é o objectivo da notificação de
2. Como o objectivo será atingido
3. Destino: quem vai receber a notificação?
4. Content: O que é o texto e o formato da notificação de (no App/Out da aplicação)
5. Quando: o que é o momento em que melhor para enviar esta notificação de emissão

    ![][2]

Para obter mais informações consulte os [guias de planeamento](https://github.com/Azure/azure-mobile-engagement-samples/tree/master/Playbooks).

De acordo com a peça 2 do papel em branco João utiliza a secção de destino para definir os dados que ele tem para recolher e escreve o seu plano de etiqueta em parceria com departamento de TI para implementar a solução. Depois de 1 semana de implementação e testes de aceitação de utilizador, João finalmente pode iniciar o seus programas.

##<a name="program-results"></a>Resultados de programa

4 meses mais tarde, João revê espetáculos dos programas. O programa de boas-vindas e o programa semanal são reunião dele objetivos. Reduz o número de utilizador com apenas uma sessão, mais funcionalidades da aplicação estão a ser utilizadas e o número de ligações por semana tem duplicado.

O **Programa inativa** está a ajudar João compreender gregária de utilizador. Parece que 15% dos utilizadores Inativos regresse à aplicação. No entanto a maior parte dos mesmos não manter-se ativo mais do que 1 mês. João prevê uma potencial otimização desta sequência com notificações adicionais e expandir as suas escolhas de conteúdos.

**Descobrir o programa** não funcionar corretamente. Aumenta cruzada vender mas não suficiente para alcançar dele objetivos. João identifica que ele não tem dados suficientes para tornar relevantes a filtrar e propor conteúdo apropriado. Ele deixa deste programa e foca-se em "editorial as notificações push" com Azure Mobile Cativação a enviar. Dele jornalistas já tem uma solução CMS para enviar notificações push e não pretende alterar.

João decide utilizar a API atinja que é um HTTP REST API que lhe permite gerir campanhas de atingir sem ter de utilizar a interface AZME Web. Com esta abordagem João pode recolher os dados que ele precisa e permitir que os seus escritores continuar a utilizar a solução CMS.

Para assegurar que essa funcionalidade está a funcionar corretamente, João pede departamento de TI para ser vigilant sobre os seguintes pontos:

1. **Sistemas de operação** : têm os seus próprios regras para administrar as notificações push, para que o João decide a lista todos os casos e verifica se as APIs processá-lo.
Por exemplo: O sistema de Android push permite conceito não for o caso com iOS.

2. **Período de tempo**: João pretende uma API que defina o período de tempo e defina um fim para campanhas. Ele pretende manter os utilizadores a partir de qualquer notificação desorganização bombing.

3. **Categorias**: equipa de Marketing prepara modelo para cada tipo de alertas. João pergunta departamento de TI para definir categorias dentro da API.

Depois de alguns testes João é satisfeito. Graças a esta API jornalistas podem continuar a enviar notificações push com as respetivas CMS e Azure Mobile Cativação recolhe todos os dados comportamentais das mesmas

Depois destes 4 pela primeira vez meses, resultados refletem um bom geral desempenho e oferece-lhe nível de confiança para o João e o seu quadro, ROI por utilizador aumentos por 15% e vendas móveis representam 17.5% do total de vendas, um aumento de 7.5% apenas quatro meses.

<!--Image references-->
[1]: ./media/mobile-engagement-media-scenario/engagement-strategy.png
[2]: ./media/mobile-engagement-media-scenario/push-scenarios.png

<!--Link references-->
[Media Playbook link]: https://github.com/Azure/azure-mobile-engagement-samples/tree/master/Playbooks
