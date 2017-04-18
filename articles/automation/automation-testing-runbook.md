<properties 
    pageTitle="Testar um livro de execuções no Azure automatização | Microsoft Azure"
    description="Antes de publicar um livro de execuções no Azure automatização, pode testar-o para se certificar de que funciona como esperado.  Este artigo descreve como testar um livro de execuções e visualizar a respectiva saída."
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
    ms.date="09/12/2016"
    ms.author="magoedte;bwren" />

# <a name="testing-a-runbook-in-azure-automation"></a>Testar um livro de execuções em automatização do Azure
Quando testar um livro de execuções, a [versão de rascunho](automation-creating-importing-runbook.md#publishing-a-runbook) é executado e as ações que realiza são concluídas. É criado sem histórico da tarefa, mas as sequências de [saída](automation-runbook-output-and-messages.md#output-stream) e [aviso e erro](automation-runbook-output-and-messages.md#message-streams) são apresentadas no teste painel de saída. Mensagens de [sequência verbosa](automation-runbook-output-and-messages.md#message-streams) só são apresentadas no painel de saída se a [variável de $VerbosePreference](automation-runbook-output-and-messages.md#preference-variables) está definida para continuar.

Apesar da versão de rascunho está a ser executada, livro de execuções ainda executa normalmente o fluxo de trabalho e executa as ações relativamente a recursos no ambiente. Por este motivo, só deverá testar runbooks na recursos de que não sejam produção.

O procedimento para cada [tipo de livro execuções](automation-runbook-types.md) de teste é a mesma e, não existe diferença entre o editor textual e o editor de gráfico no portal do Azure ao testar.  


## <a name="to-test-a-runbook-in-the-azure-portal"></a>Para testar a um livro de execuções no portal do Azure

Pode trabalhar com qualquer [tipo de livro execuções](automation-runbook-types.md) no portal do Azure.

1. Abra a versão de rascunho do livro de execuções no [textual editor](automation-editing-a-runbook.md#Portal) ou [editor de gráfico](automation-graphical-authoring-intro.md).
2. Clique no botão de **teste** para abrir o pá de teste.
3. Se o livro de execuções tiver parâmetros, serão apresentados no painel esquerdo, onde pode fornecer valores para ser utilizado para o teste.
4. Se pretender executar o teste de um [Híbrido livro execuções trabalho](automation-hybrid-runbook-worker.md), em seguida, alterar **Definições de executar** o trabalhador **Híbrido** e selecione o nome do grupo de destino.  Caso contrário, mantenha a predefinição **Azure** para executar o teste na nuvem.
5. Clique no botão **começar** para iniciar o teste.
6. Se o livro de execuções for [PowerShell fluxo de trabalho](automation-runbook-types.md#powershell-workflow-runbooks) ou [gráficos](automation-runbook-types.md#graphical-runbooks), em seguida, pode parar ou suspender-enquanto está a ser testado com os botões por baixo do painel de saída. Quando suspender livro de execuções, conclui a atividade atual antes de a ser suspensa. Assim que o livro de execuções está suspensa, pode pará-la ou reinicie-o.
7. Verificar se o resultado do livro de execuções no painel de saída.


## <a name="next-steps"></a>Próximos passos

- Para saber como criar ou importar um livro de execuções, consulte o artigo [criar ou importar um livro de execuções na automatização do Azure](automation-creating-importing-runbook.md)
- Para saber mais sobre a criação de gráficos, consulte o artigo [gráficos cocriação na automatização do Azure](automation-graphical-authoring-intro.md)
- Para começar a trabalhar com runbooks de fluxo de trabalho do PowerShell, consulte o artigo [meu livro de execuções de fluxo de trabalho de PowerShell primeiro](automation-first-runbook-textual.md)
- Para saber mais sobre como configurar runboks para devolver mensagens de estado e os erros, incluindo as práticas recomendadas, consulte o artigo [saída do livro execuções e mensagens de automatização do Azure](automation-runbook-output-and-messages.md)