<properties
    pageTitle="Descrição geral de API de exportação de Cativação móvel"
    description="Aprenda as noções básicas sobre como exportar os seus dados não processados gerados pelo dispositivos do utilizador para tirar partido-lo no seus próprio ferramentas"
    services="mobile-engagement"
    documentationCenter="mobile"
    authors="kpiteira"
    manager="erikre"
    editor=""/>

<tags
    ms.service="mobile-engagement"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="mobile-multiple"
    ms.workload="mobile"
    ms.date="04/26/2016"
    ms.author="kpiteira"/>

# <a name="mobile-engagement-export-api-overview"></a>Descrição geral de API de exportação de Cativação móvel

## <a name="introduction"></a>Introdução

Neste documento, irá obter informações sobre as noções básicas sobre como exportar os seus dados não processados gerados pelo dispositivos do utilizador para tirar partido-lo no seus próprio ferramentas.

## <a name="pre-requisites"></a>Pré-requisitos

Necessita de exportar os dados não processados de Cativação Mobile:

- Programa de configuração de autenticação de API para poderá utilizar as API (consulte [configuração manual de autenticação](mobile-engagement-api-authentication-manual.md)),
- Utilizar as API REST ou [.net SDK](mobile-engagement-dotnet-sdk-service-api.md),
- Uma conta de armazenamento do Windows Azure.

>[AZURE.NOTE] Recomendamos também aconselhar excelente [Explorador de armazenamento do Microsoft Azure](http://storageexplorer.com/), pelo menos durante a fase de desenvolvimento à medida que fornece uma IU fáceis de utilizar para interagir com armazenamento do Windows Azure.

## <a name="what-can-be-exported"></a>O que pode ser exportado?

Cativação Mobile permite que os utilizadores para recolher vários tipos de dados e, consequentemente, tem vários tipos de exportação adequados para estes tipos de dados diferente.
Existem 2 tipos essenciais de exportação:

- Instantâneo: utilizado, normalmente, a exportação de dados que representa um Estado e para a qual Mobile Cativação não tenha um histórico. Isto inclui etiquetas (informações app), tokens ou respostas de campanha push, por exemplo. Como consequência estes tipos de exportação não estão relacionadas com uma data.
- históricas: este tipo de exportação é utilizado para dados que é acumulado ao longo do tempo, tais como eventos ou atividades, por exemplo.

A tabela abaixo descreve exaustiva todos os possíveis exportações:

| Exportar o tipo | Tipo de dados | Descrição                                                                                                                                 |
|-------------|-----------|---------------------------------------------------------------------------------------------------------------------------------------------|
| Instantâneo    | Notificações Push      | Gera uma exportação de respostas campanhas de emissão numa base por deviceid/ID de utilizador                                                              |
| Instantâneo    | Etiqueta       | Gera uma exportação de etiquetas (informações app) associados a cada dispositivos                                                                       |
| Instantâneo    | Dispositivo    | Gera uma exportação da maior parte dos dados dispersos em dispositivos, como os technicals (modelo, região, fuso horário,...), a etiquetas, a primeira vez viu,... |
| Instantâneo    | Token     | Gera uma exportação de todos os tokens válidas                                                                                                 |
| Histórico  | Atividade  | Gera uma exportação de todas as atividades para cada dispositivos num determinado período de tempo                                                           |
| Histórico  | Evento     | Gera uma exportação de todas as atividades para cada dispositivos num determinado período de tempo                                                           |
| Histórico  | Tarefa       | Gera uma exportação de todas as tarefas para cada dispositivos num determinado período de tempo                                                                 |
| Histórico  | Erro     | Gera uma exportação de todos os erros para cada dispositivos num determinado período de tempo                                                               |

## <a name="how-does-it-work"></a>Como funciona?

Exportações são longas a executar tarefas que pode produzir ficheiros de dados grandes. Por que motivo, não é possível invocar para devolver imediatamente um ficheiro para o transferir.
Para exportar dados de Cativação Mobile, terá que criar uma **Tarefa de exportação** através da API onde pode especificar geralmente:

- O tipo de exportação (instantâneo ou histórica),
- O tipo de dados
- O **Contentor de armazenamento do Azure** (incluindo uma SA válida com acesso de escrita) onde o resultado da exportação será escrito.

Tenha em atenção que poderá demorar alguns minutos para o trabalho ser iniciado e, em seguida, poderá executar a partir de aguardar alguns segundos para as aplicações Sparkline a várias horas para as aplicações com muitas utilizadores ou atividade.

Quando a tarefa estiver criada, é possível verificar o respetivo estado para ver se ainda estiver em execução ou se tiver concluído.

Assim que a tarefa é foi concluída com êxito, o ficheiro de dados resultante está disponível no contentor de armazenamento fornecido.
