<properties
    pageTitle="Azure Cativação móvel Web SDK APIs | Microsoft Azure"
    description="As atualizações mais recentes e os procedimentos para o Web SDK para Azure Mobile Cativação"
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="piyushjo"
    manager="erikre"
    editor="" />

<tags
    ms.service="mobile-engagement"
    ms.workload="mobile"
    ms.tgt_pltfrm="web"
    ms.devlang="js"
    ms.topic="article"
    ms.date="06/07/2016"
    ms.author="piyushjo" />

# <a name="use-the-azure-mobile-engagement-api-in-a-web-application"></a>Utilizar a API do Azure Mobile Cativação numa aplicação web

Este documento é um suplemento para o documento que mostra-lhe como [integrar o Mobile Cativação numa aplicação web](mobile-engagement-web-integrate-engagement.md). Fornece aprofundadas detalhes sobre como utilizar a API do Azure Mobile Cativação para comunicar as estatísticas de aplicação.

API de Cativação Mobile é fornecido pela `engagement.agent` objeto. A predefinição Azure Mobile Cativação Web SDK alias está `engagement`. Pode redefinir este alias da configuração SDK.

## <a name="mobile-engagement-concepts"></a>Conceitos de Cativação Mobile

As seguintes partes refinar comuns [conceitos Mobile Cativação](mobile-engagement-concepts.md) para a plataforma de web.

### <a name="session-and-activity"></a>`Session`e`Activity`

Se o utilizador permanece inactivo para mais do que de aguardar alguns segundos entre duas atividades, a sequência de utilizador das atividades é dividida em duas sessões distintos. Estes alguns segundos chamam o limite de sessão.

Se a sua aplicação web não declara o fim de atividades de utilizador por si (ao contactar o `engagement.agent.endActivity` função), o servidor de Cativação Mobile expira automaticamente a sessão do utilizador dentro de três minutos depois da página de aplicações estiver fechada. Esta opção é denominada o limite de sessão do servidor.

### `Crash`

Relatórios de automatizado do não identificadas JavaScript exceções não são criados por predefinição. No entanto, pode comunicar causa uma falha manualmente, utilizando o `sendCrash` funcionar (consulte a secção sobre relatórios causa uma falha).

## <a name="reporting-activities"></a>Atividades de elaboração de relatórios

Relatório na atividade de utilizador inclui quando um utilizador inicia uma nova actividade e, quando o utilizador termina a atividade atual.

### <a name="user-starts-a-new-activity"></a>Utilizador inicia uma nova actividade

    engagement.agent.startActivity("MyUserActivity");

Precisa de telefonar `startActivity()` alterações de cada actividade de utilizador do tempo. A primeira chamada para esta função inicia uma nova sessão do utilizador.

### <a name="user-ends-the-current-activity"></a>Utilizador termina a atividade atual

    engagement.agent.endActivity();

Precisa de telefonar `endActivity()` pelo menos uma vez quando o utilizador concluir a sua última actividade. Isto informa o Mobile Cativação Web SDK, que o utilizador está atualmente inactivo e que a sessão do utilizador tem de ser fechados após o limite de sessão expira. Se chamar `startActivity()` antes que expire o limite de sessão, a sessão é simplesmente retomada.

Porque não existe nenhuma chamada fiável para quando a janela do navegador é fechada, é frequentemente dificulta ou impossibilita chamar o fim de atividades de utilizador no interior de um ambiente web. Razão pela qual o servidor de Cativação Mobile expira automaticamente a sessão do utilizador dentro de três minutos depois da página de aplicações estiver fechada.

## <a name="reporting-events"></a>Eventos de elaboração de relatórios

Criar relatórios sobre eventos abrangem eventos de sessão e eventos de programa autónomo.

### <a name="session-events"></a>Eventos de sessão

Eventos de sessão são normalmente utilizados para comunicar as ações realizadas por um utilizador durante a sessão do utilizador.

**Exemplo sem dados adicionais:**

    loginButton.onclick = function() {
      engagement.agent.sendSessionEvent('login');
      // [...]
    }

**Exemplo com os dados adicionais:**

    loginButton.onclick = function() {
      engagement.agent.sendSessionEvent('login', {user: 'alice'});
      // [...]
    }

### <a name="standalone-events"></a>Eventos de autónomo

Ao contrário dos eventos de sessão, podem ocorrer autónomo eventos fora do contexto de uma sessão.

Para que, utilize ``engagement.agent.sendEvent`` em vez de ``engagement.agent.sendSessionEvent``.

## <a name="reporting-errors"></a>Relatório de erros

Relatório de erros abrange os erros de sessão e autónomo erros.

### <a name="session-errors"></a>Erros de sessão

Erros de sessão são normalmente utilizados para comunicar os erros que tenham um impacto no utilizador durante a sessão do utilizador.

**Exemplo sem dados adicionais:**

    var validateForm = function() {
      // [...]
      if (password.length < 6) {
        engagement.agent.sendSessionError('password_too_short');
      }
      // [...]
    }

**Exemplo com os dados adicionais:**

    var validateForm = function() {
      // [...]
      if (password.length < 6) {
        engagement.agent.sendSessionError('password_too_short', {length: 4});
      }
      // [...]
    }

### <a name="standalone-errors"></a>Erros de autónomo

Ao contrário dos erros de sessão, podem ocorrer erros de autónomo fora do contexto de uma sessão.

Para que, utilize `engagement.agent.sendError` em vez de `engagement.agent.sendSessionError`.

## <a name="reporting-jobs"></a>Tarefas de elaboração de relatórios

Mapas no trabalhos de folhas de rosto causa uma falha de elaboração de relatórios e elaboração de relatórios de erros e eventos que ocorram durante uma tarefa.

**Exemplo:**

Se pretender monitorizar a um pedido de AJAX, utilizaria o seguinte procedimento:

    // [...]
    xhr.onreadystatechange = function() {
      if (xhr.readyState == 4) {
      // [...]
        engagement.agent.endJob('publish');
      }
    }
    engagement.agent.startJob('publish');
    xhr.send();
    // [...]

### <a name="reporting-errors-during-a-job"></a>Relatório de erros durante a uma tarefa

Erros podem estar relacionado com uma tarefa em execução em vez de para a sessão do utilizador actual.

**Exemplo:**

Se pretender comunicar um erro se um pedido de AJAX falhar:

    // [...]
    xhr.onreadystatechange = function() {
      if (xhr.readyState == 4) {
        // [...]
        if (xhr.status == 0 || xhr.status >= 400) {
          engagement.agent.sendJobError('publish_xhr', 'publish', {status: xhr.status, statusText: xhr.statusText});
        }
        engagement.agent.endJob('publish');
      }
    }
    engagement.agent.startJob('publish');
    xhr.send();
    // [...]

### <a name="reporting-events-during-a-job"></a>Eventos de elaboração de relatórios durante uma tarefa

Eventos podem estar relacionado com a uma tarefa em execução em vez de para a sessão do utilizador actual, agradecimentos a `engagement.agent.sendJobEvent` função.

Esta função, exatamente como funciona `engagement.agent.sendJobError`.

### <a name="reporting-crashes"></a>Causa uma falha de elaboração de relatórios

Utilizar o `sendCrash` função ao relatório falha manualmente.

O `crashid` argumento é uma cadeia que identifica o tipo de falha de sistema.
O `crash` argumento é normalmente o rastreio da pilha de falha de sistema como uma cadeia.

    engagement.agent.sendCrash(crashid, crash);

## <a name="extra-parameters"></a>Parâmetros adicionais

Pode anexar dados arbitrários a um evento, erro, atividade ou tarefa.

Os dados podem ser qualquer objeto JSON (mas não numa matriz ou tipo primitivo).

**Exemplo:**

    var extras = {"video_id": 123, "ref_click": "http://foobar.com/blog"};
    engagement.agent.sendEvent("video_clicked", extras);

### <a name="limits"></a>Limites

Limites que se aplicam aos parâmetros adicionais são nas áreas de expressões regulares para chaves, tipos de valor e tamanho.

#### <a name="keys"></a>Teclas

Cada chave no objeto tem de corresponder a seguinte expressão normal:

    ^[a-zA-Z][a-zA-Z_0-9]*

Isto significa que tem de iniciar a teclas com, pelo menos, uma letra, seguido de letras, dígitos ou sublinhado (\_).

#### <a name="values"></a>Valores

Valores estão limitados a cadeia, número e tipos de booleanos.

#### <a name="size"></a>Tamanho

Extras são limitadas ao 1.024 carateres por chamada (depois do Mobile Cativação Web SDK codifica-JSON).

## <a name="reporting-application-information"></a>Informações sobre a aplicação de elaboração de relatórios

Manualmente pode comunicar rastreio informações (ou outras informações específicas da aplicação) utilizando o `sendAppInfo()` função.

Tenha em atenção que estas informações podem ser enviadas incrementada. O valor mais recente para uma chave específica será mantido para um dispositivo específico.

Como extras do evento, pode utilizar qualquer objeto JSON para abstract informações sobre a aplicação. Tenha em atenção que matrizes ou sub-objetos são tratados como cadeias simples (utilizando serialização JSON).

**Exemplo:**

Eis uma amostra de código para enviar género do utilizador e a data de nascimento:

    var appInfos = {"birthdate":"1983-12-07","gender":"female"};
    engagement.agent.sendAppInfo(appInfos);

### <a name="limits"></a>Limites

Limites que se aplicam a informações sobre a aplicação são nas áreas de expressões regulares para chaves e tamanho.

#### <a name="keys"></a>Teclas

Cada chave no objeto tem de corresponder a seguinte expressão normal:

    ^[a-zA-Z][a-zA-Z_0-9]*

Isto significa que tem de iniciar a teclas com, pelo menos, uma letra, seguido de letras, dígitos ou sublinhado (\_).

#### <a name="size"></a>Tamanho

Informações sobre a aplicação está limitado a 1.024 carateres por chamada (depois do Mobile Cativação Web SDK codifica-JSON).

No exemplo anterior, JSON enviado para o servidor é 44 carateres de comprimento:

    {"birthdate":"1983-12-07","gender":"female"}
