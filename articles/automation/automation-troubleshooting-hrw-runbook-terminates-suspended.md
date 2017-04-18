<properties
   pageTitle="Híbrido livro execuções trabalhador: Uma tarefa de livro execuções termina com o estado suspenso | Microsoft Azure"
   description="Causas sintomas e resoluções para erro de taxas de cessação híbrido livro execuções trabalhador tarefa."
   services="automation"
   documentationCenter=""
   authors="mgoedtel"
   manager="jwhit"
   editor="tysonn" />
<tags
   ms.service="automation"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/17/2016"
   ms.author="magoedte" />

# <a name="hybrid-runbook-worker-a-runbook-job-terminates-with-a-status-of-suspended"></a>Híbrido livro execuções trabalhador: Uma tarefa de livro execuções termina com o estado suspenso

## <a name="summary"></a>Resumo

O livro de execuções será suspensa pouco depois de tentar executá-la três vezes. Existem condições que podem interromper o livro de execuções a conclusão com êxito e a mensagem de erro relacionados não inclui quaisquer informações adicionais que indica qual a razão. Este artigo fornece os passos de resolução de problemas para problemas relacionados com as falhas de execução de livro execuções híbrido livro execuções trabalhador.

Se o seu problema Azure não endereçado neste artigo, visite os fóruns Azure no [MSDN e a pilha de conteúdo adicional](https://azure.microsoft.com/support/forums/). Pode publicar o seu problema destes fóruns ou ao [ @AzureSupport no Twitter](https://twitter.com/AzureSupport). Além disso, pode preencher um pedido de suporte Azure ao selecionar a **obter suporte** no site de [suporte do Azure](https://azure.microsoft.com/support/options/) .

## <a name="symptom"></a>Sintoma

Falha de execução de livro execuções e o erro devolvido é, "a ação de tarefa não é possível executar 'Ativar', porque o processo de parado inesperadamente. A ação de tarefa foi efetuada a tentativa 3 vezes."


## <a name="cause"></a>Causa

Existem várias causas possíveis para o erro: 

  1. Trabalhador híbrida está atrás de um firewall ou proxy
  2. O computador a que executar o trabalhador híbrido no tem menos os mínimos hardware [requisitos](automation-hybrid-runbook-worker.md#hybrid-runbook-worker-requirements) 
  3. Não é possível autenticar os runbooks com recursos locais


## <a name="cause-1-hybrid-runbook-worker-is-behind-proxy-or-firewall"></a>Causa 1: Híbrido livro execuções trabalhador está atrás da firewall ou proxy

O computador a que executar o trabalho de livro execuções híbrido no é atrás de um firewall ou servidor proxy e aceder à rede de saída não pode ser permitido ou corretamente configurado.

### <a name="solution"></a>Solução

Verifique se o computador tem acesso de saída para *. cloudapp.net em portas 443, 9354 e 30000 30199. 

## <a name="cause-2-computer-has-less-than-minimum-hardware-requirements"></a>Causa 2: O computador tem requisitos mínimos menos de hardware

Computadores que executam o trabalho de livro execuções híbrido devem cumprir os requisitos mínimos de hardware antes de designá-lo para alojar esta funcionalidade. Caso contrário, dependendo da utilização de outros processos em segundo plano e contenção causados pela runbooks durante a execução do recurso, o computador irá tornar-se mais utilizadas e provocar atrasos de tarefa de livro execuções ou tempos limite. 

### <a name="solution"></a>Solução 

Em primeiro lugar, confirme o computador designado para executar a funcionalidade de trabalho de livro execuções híbrido cumpra os requisitos mínimos de hardware.  Caso isso aconteça, monitorize a utilização de memória e CPU para determinar qualquer correlação entre o desempenho dos processos de trabalho de livro execuções híbrido e Windows.  Se existir memória ou pressão CPU, isto pode indicar a necessidade de atualizar ou adicionar processadores adicionais ou aumentar a memória para endereçar o congestionamento do recurso e resolver o erro. Em alternativa, selecione um recurso de cluster diferentes que pode suportar a requisitos mínimos e a escala quando pedidos carga de trabalho a indicar que um aumento é necessário.         

## <a name="cause-3-runbooks-cannot-authenticate-with-local-resources"></a>Causa 3: Runbooks não é possível autenticar com recursos locais

### <a name="solution"></a>Solução

Verifique o registo de eventos do **Microsoft SMA** para um evento correspondente com descrição *Win32 processo terminou com o código [4294967295]*.  A causa deste erro é ainda não o tiver configurado autenticação no seu runbooks ou especificado as credenciais executar como para o grupo de trabalho híbrido.  Reveja [as permissões do livro execuções](automation-hybrid-runbook-worker.md#runbook-permissions) para confirmar a que tiver configurado corretamente autenticação para sua runbooks.  


 

