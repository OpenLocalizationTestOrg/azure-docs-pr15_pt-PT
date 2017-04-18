<properties
 pageTitle="Resolução de problemas de implementação do serviço de nuvem | Microsoft Azure"
 description="Existem alguns problemas comuns que poderão ocorrer quando implementar o Azure um serviço na nuvem. Este artigo fornece soluções para alguns dos mesmos."
   services="cloud-services"
   documentationCenter=""
   authors="simonxjx"
   manager="felixwu"
   editor=""
   tags="top-support-issue"/>
<tags
   ms.service="cloud-services"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="tbd"
   ms.date="09/02/2016"
   ms.author="v-six" />

# <a name="troubleshoot-cloud-service-deployment-problems"></a>Resolução de problemas de implementação do serviço na nuvem

Quando implementar um pacote de aplicação de serviço de nuvem Azure, pode obter informações sobre a implementação a partir de **Propriedades** no portal do Azure. Pode utilizar os detalhes neste painel para ajudá-lo a resolver problemas relacionados com o serviço de nuvem, e pode fornecer estas informações para suportar o Azure ao abrir um novo pedido de suporte.

Pode localizar o painel de **Propriedades** da seguinte forma:

* No portal do Azure, clique em implementação do seu serviço de nuvem, clique em **todas as definições**e, em seguida, clique em **Propriedades**.
* No portal do Azure clássico, clique na implementação do seu serviço de nuvem, clique em **DASHBOARD**, localizada no canto inferior direito da página (na **vista rápida**). Tenha em atenção que não existe nenhuma etiqueta "Propriedades" neste painel.

> [AZURE.NOTE] Pode copiar o conteúdo do painel de **Propriedades** para a área de transferência ao clicar no ícone no canto superior direito do painel.

[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="problem-i-cannot-access-my-website-but-my-deployment-is-started-and-all-role-instances-are-ready"></a>Problema: Posso não é possível aceder ao meu Web site, mas minha implementação é iniciada e todas as instâncias de função estão prontas

A ligação de URL do Web site apresentada no portal do não inclui a porta. A porta predefinida para Web sites é 80. Se a aplicação estiver configurada para executar uma porta diferente, tem de adicionar o número da porta correto para o URL ao aceder ao Web site.

1. No portal do Azure, clique na implementação do seu serviço de nuvem.
2. No painel de **Propriedades** do Azure portal, verifique as portas para as instâncias de função (em **Pontos finais de entrada**).
3. Se a porta não for 80, adicione o valor da porta correto para o URL quando aceder à aplicação. Para especificar uma porta não predefinida, escreva o URL, seguido por dois pontos (:) seguido do número de porta, sem espaços.

## <a name="problem-my-role-instances-recycled-without-me-doing-anything"></a>Problema: O meu instâncias de função Reciclagem sem-me a fazer nada

Serviço de reparação ocorre automaticamente quando Azure Deteta nós de problema e, por conseguinte, move o cursor instâncias de papel para novos nós. Quando isto acontece, poderá ver o seu instâncias de função Reciclagem automaticamente. Para saber se a reparação serviço ocorreu:

1. No portal do Azure, clique na implementação do seu serviço de nuvem.
2. No painel de **Propriedades** do Azure portal, reveja as informações e determinar se a reparação serviço ocorreu durante o período de tempo observados as funções de reciclagem.

Funções também serão Reciclagem aproximadamente uma vez por mês durante o anfitrião SO e atualizações do SO convidado.  
Para mais informações, consulte a [Função instância reinicia vencimento a SO actualizações](http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx) de mensagem do blogue

## <a name="problem-i-cannot-do-a-vip-swap-and-receive-an-error"></a>Problema: Posso não é possível fazer uma trocar VIP e receber uma mensagem de erro

Não é permitido um trocar VIP se uma atualização de implementação estiver em curso. Atualizações de implementação podem ocorrer automaticamente quando:

* Um novo sistema operativo de convidado está disponível e está configuradas para as atualizações automáticas.
* A reparação serviço ocorre.

Para saber se uma atualização automática está a impedir efetuar uma trocar VIP:

1. No portal do Azure, clique na implementação do seu serviço de nuvem.
2. No painel de **Propriedades** do Azure portal, observe o valor de **Estado**. Se estiver **pronto**, em seguida, selecione a **última operação** para ver se um recentemente aconteceu que possam estar a impedir a trocar VIP.
3. Repita os passos 1 e 2 para a implementação de produção.
4. Se for uma atualização automática no processo, aguarde para o mesmo concluir antes de tentar fazer a trocar VIP.

## <a name="problem-a-role-instance-is-looping-between-started-initializing-busy-and-stopped"></a>Problema: Uma instância da função é ciclo entre os iniciado, a inicializar, ocupado e parado

Esta condição pode indicar um problema com o seu ficheiro de código de configuração pacote de aplicação. Nesse caso, deverá conseguir ver o estado alterar em poucos minutos e o portal do Azure poderá dizer algo como **Reciclagem**, **ocupado**ou **a inicializar**. Isto indica que não existe algum problema com a aplicação que está a manter a instância de função a execução.

Para mais informações sobre como resolver problemas de para este problema, consulte a mensagem [Azure PaaS calcular diagnósticos de dados](http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx) e [problemas comuns que causam funções de administrador a Reciclagem](cloud-services-troubleshoot-common-issues-which-cause-roles-recycle.md)do blogue.

## <a name="problem-my-application-stopped-working"></a>Problema: Minha aplicação deixado de funcionar

1. No portal do Azure, clique na instância de função.
2. No painel de **Propriedades** do Azure portal, considere as seguintes condições para resolver o problema:
   * Se a instância de função recentemente deixou (pode verificar o valor de **interrupção contar**), poderá ser atualizar a implementação. Aguarde para ver se a instância de função continua a funcionar no seu próprio.
   * Se a instância de função está **ocupado**, verifique o seu código de aplicação para ver se o evento [StatusCheck](https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleenvironment.statuscheck) é processado. Poderá ter de adicionar ou corrigir algum código que processa este evento.
   * Percorra os dados de diagnóstico e resolução de problemas de cenários na mensagem no blogue [Azure PaaS calcular diagnósticos de dados](http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).

>[AZURE.WARNING] Se Reciclagem seu serviço de nuvem, reponha as propriedades para a implementação, eficazmente apagar as informações para o problema original.

## <a name="next-steps"></a>Próximos passos

Ver mais [artigos de resolução de problemas](https://azure.microsoft.com/documentation/articles/?tag=top-support-issue&product=cloud-services) para a serviços em nuvem.

Para saber como resolver problemas de função de serviço de nuvem, utilizando dados de diagnósticos do Azure PaaS computador, consulte o artigo [série de blogues do Kevin Williamson](http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).
