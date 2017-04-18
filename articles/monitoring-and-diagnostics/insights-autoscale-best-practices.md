<properties
    pageTitle="Práticas recomendadas para o Azure Monitor autoscaling. | Microsoft Azure"
    description="Saiba princípios para utilizar eficazmente autoscaling no Monitor do Azure."
    authors="kamathashwin"
    manager="carolz"
    editor=""
    services="monitoring-and-diagnostics"
    documentationCenter="monitoring-and-diagnostics"/>

<tags
    ms.service="monitoring-and-diagnostics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/20/2016"
    ms.author="ashwink"/>

# <a name="best-practices-for-azure-monitor-autoscaling"></a>Práticas recomendadas para autoscaling Monitor do Azure

As secções seguintes neste documento ajudá-lo a compreender as práticas recomendadas para autoscale no Azure. Após rever estas informações, poderá melhor utilizar eficazmente autoscale na sua infraestrutura Azure.

## <a name="autoscale-concepts"></a>Autoscale conceitos

- Um recurso pode ter apenas *uma* definição de autoscale
- Uma definição de autoscale pode ter um ou mais perfis e de cada perfil podem ter uma ou mais regras autoscale.
- Uma definição de autoscale escalas instâncias horizontalmente, que é *saída* , aumentando o *in* e instâncias diminuindo o número de instâncias.
 Uma definição de autoscale tem um máximo, mínimo e valor predefinido de instâncias.
- Uma tarefa autoscale sempre lê a métrica do associado para dimensionar por, verificar se tem-cruzados o limiar configurado para fora de escala ou escala-in. Pode ver uma lista de métricas esse autoscale pode dimensionar no [Monitor do Azure autoscaling comuns métricas](insights-autoscale-common-metrics.md).
- Limiares de todos os são calculados a um nível de instância. Por exemplo, "escala reduzir por 1 instância quando média CPU > 80% quando a contagem de instância é 2", significa que a escala de saída quando a CPU média através de todas as instâncias for superior a 80%.
- Sempre a receber notificações de falha por correio eletrónico. Especificamente, o proprietário, contribuintes e os leitores do recurso destino recebem correio eletrónico. Também sempre recebe uma mensagem de e-mail de *recuperação* quando autoscale recupera de uma falha de e começa a funcionar normalmente.
- Pode optar por não participar-in para receber uma notificação de ação de escala com êxito através de e-mail e webhooks.

## <a name="autoscale-best-practices"></a>Práticas recomendadas Autoscale

Utilize as seguintes melhores práticas ao utilizar autoscale.

### <a name="ensure-the-maximum-and-minimum-values-are-different-and-have-an-adequate-margin-between-them"></a>Certifique-se de que os valores máximos e mínimos são diferentes e tem uma margem adequada entre elas
Se tiver uma definição de que tem mínimo = 2, máximo = 2 e atuais contagem de instâncias for 2, não pode ocorrer nenhuma ação de escala. Manter uma margem adequada entre as contagens instância máximos e mínimos, o que são inclusive. Autoscale sempre escalas entre estes limites.

### <a name="manual-scaling-is-reset-by-autoscale-min-and-max"></a>Dimensionamento manual é reposta por autoscale mínimo e máximo
Se atualizar manualmente a contagem de instância para um valor acima ou abaixo do máximo, o motor autoscale escalas automaticamente novamente para o mínimo (se estiver abaixo) ou o número máximo (se estiver acima). Por exemplo, defina o intervalo entre 3 e 6. Se tiver uma instância em execução, o motor autoscale escalas 3 instâncias na sua próxima execução. Da mesma forma, seria escala no 8 instâncias de volta para o 6 na sua próxima execução.  Dimensionamento manual é muito temporário, a menos que reponha as regras de autoscale também.

### <a name="always-use-a-scale-out-and-scale-in-rule-combination-that-performs-an-increase-and-decrease"></a>Utilizar sempre uma combinação de regra de saída de escala e escala que efetua uma aumentar e diminuir
Se utilizar apenas uma parte da combinação, autoscale escala-in que simples saída ou, até o máximo ou mínimo, é atingido.

### <a name="do-not-switch-between-the-azure-portal-and-the-azure-classic-portal-when-managing-autoscale"></a>Não mudar entre o portal do Azure e o portal de clássico Azure ao gerir Autoscale
Para serviços em nuvem e serviços de aplicação (Web Apps), utilize o portal do Azure (portal.azure.com) para criar e gerir as definições de autoscale. Para conjuntos de escala de Máquina Virtual utilize PoSH, clip ou REST API para criar e gerir as definições de autoscale. Não alterne entre o Azure portal clássico (manage.windowsazure.com) e o portal do Azure (portal.azure.com) ao gerir autoscale configurações. O portal clássico Azure e as sua back-end subjacente tem limitações. Deslocar-se ao portal do Azure para gerir autoscale utilizando uma interface gráfica do utilizador. As opções estão a utilizar o autoscale PowerShell, clip ou REST API (através do Explorador de recursos do Azure).

### <a name="choose-the-appropriate-statistic-for-your-diagnostics-metric"></a>Selecione a estatística adequada para o seu métrica de diagnóstico
Para métricas de diagnóstico, pode optar por entre *média*, *mínimo*, *máximo* e *Total* como uma métrica Dimensionar por. A estatística mais comuns é *média*.

### <a name="choose-the-thresholds-carefully-for-all-metric-types"></a>Selecione os limiares cuidadosamente para todos os tipos de métricos
Recomendamos que selecione cuidadosamente limiares diferentes para fora de escala escala-in e com base em situações práticas.

Vamos *não é recomendado* definições autoscale como os exemplos abaixo com os valores do limiar mesmo ou muito semelhante para saída e em condições:

- Aumentar instâncias por 1 contar quando número de threads < = 600
- Diminuir instâncias por 1 contar quando número de threads > = 600

Vamos ver um exemplo do que pode levar a um comportamento que pode parecer confuso. Cosider sequência que se segue.

1. Suponhamos que não existem 2 instâncias para começar e, em seguida, o número médio de threads por instância cresce a 625.
2. Autoscale escalas terminar a adição de uma instância do 3º.
3. Em seguida, partem do princípio de que o número de threads média ao longo da instância baixar para 575.
4. Antes de dimensionamento para baixo, autoscale tentativas para estimar que o estado final serão se-dimensionada no. Por exemplo, 575 x 3 (contagem de instância atual) = 1,725 / 2 (número final de instâncias quando dimensionada para baixo) = 862.5 threads. Isto significa que autoscale teria imediatamente escala saída novamente mesmo depois de dimensionada sessão, se o número de threads média se mantém o mesmo ou até mesmo se situar apenas uma pequena quantidade. No entanto, se-dimensionada o novamente, todo o processo de seria repita, levando a um círculo infinito.
5. Para evitar esta situação (chamada "flapping"), autoscale não dimensionar para baixo de todo. Em vez disso, ignora e reevaluates a condição novamente da próxima vez que o serviço é executada. Isto poderia confunda muitas pessoas porque autoscale não seria aparecem trabalhar quando o número de threads média era 575.

Estimativa durante um escala-in destina-se para evitar situações "flappy". Devo manter este comportamento em mente quando escolher os mesmos limiares para escala de saída em.

Recomendamos que selecione uma margem adequada entre o limite de escala e em limiares. Por exemplo, considere a combinação de regra melhor seguinte.

- Aumentar instâncias por 1 contar quando CPU % > = 80
- Diminuir instâncias por 1 contar quando CPU % < = 60

Neste caso  

1. Partem do pressuposto existem 2 instâncias para começar.
2. Se a média % de CPU em várias instâncias vai para 80, autoscale escalas terminar a adição de uma instância de terceira.
3. Agora partem do princípio que ao longo do tempo a percentagem de CPU baixar para 60.
4. Regra de escala do Autoscale estimativas o estado final se fosse a escala-in. Por exemplo, 60 x 3 (contagem de instância atual) = 180 / 2 (número final de instâncias quando dimensionada para baixo) = 90. Por isso, autoscale não escala-in porque teria para escala-out retomada de imediato. Em vez disso, ignora dimensionamento para baixo.
5. Verifica a seguinte autoscale de tempo, a CPU continua a de se situar a 50. Novamente - estimativas instância de 50 x 3 = 150 / 2 instâncias = 75; que está abaixo do limiar de escala de saída de 80, por isso-escalas com êxito 2 instâncias.

### <a name="considerations-for-scaling-threshold-values-for-special-metrics"></a>Considerações para valores do limiar para métricas especiais de dimensionamento
 Para obter métricas especiais como o armazenamento ou serviço Bus fila métrica de comprimento, o limiar de é o número médio de disponíveis por atual número de ocorrências de mensagens. Selecione cuidadosamente escolher o valor de limiar para esta métrica.

Vamos ilustrar com um exemplo para se certificar de que compreende o comportamento melhor.

- Aumentar instâncias por 1 contar quando contagem de mensagem de fila de armazenamento > = 50
- Diminuir instâncias por 1 contar quando fila de armazenamento da mensagem contagem < = 10

Considere a seguinte sequência:

1. Existem 2 instâncias de fila de armazenamento.
2. Manterem a chegar mensagens e quando rever a fila de armazenamento, a contagem total lê 50. Poderá presumem que autoscale deverá começar uma ação de escala de saída. No entanto, tenha em atenção que ainda é 50/2 = 25 mensagens por instância. Por isso, escala de saída não ocorrer. Para a primeira escala saída deverá acontecer, a contagem total da mensagem na fila de armazenamento deve ser 100.
3. Em seguida, partem do princípio de que a contagem total da mensagem atinge 100.
4. É adicionada uma instância de fila de armazenamento 3º devido a uma ação de escala de saída.  A seguinte ação de escala de saída não irá acontecer até que a contagem total da mensagem na fila de espera atinja 150 porque 150/3 = 50.
5. Agora, o número de mensagens na fila de espera obtém mais pequeno. Com 3 instâncias, a ação de escala na primeira acontece quando o total de mensagens em todas as filas adicionar até 30 porque 30/3 = 10 mensagens por instância, que é o limiar de escala-in.

### <a name="considerations-for-scaling-when-multiple-profiles-are-configured-in-an-autoscale-setting"></a>Considerações para dimensionamento quando vários perfis estão configurados numa definição de autoscale

Na definição de autoscale, pode escolher um perfil predefinido, que é sempre aplicado sem qualquer dependência de agenda ou hora, ou pode escolher um perfil de periódico ou por um período fixo com um intervalo de data e hora.

Quando o serviço de autoscale processa-los, sempre verifica pela seguinte ordem:

1. Perfil de data fixa
2. Perfil periódico
3. ("Sempre") perfil predefinido

Se uma condição de perfil for cumprida, autoscale não verifica a seguinte condição de perfil abaixo do mesmo. Autoscale processa apenas um perfil de cada vez. Isto significa que se pretende incluir também uma condição de processamento de um perfil de camada inferior, tem de incluir estas regras, assim no perfil atual.

Analisemos isto utilizando um exemplo:

A imagem abaixo mostra uma definição de autoscale com um perfil predefinido de instâncias mínimos = 2 e máximas instâncias = 10. Neste exemplo, regras são configuradas para escala-out quando a contagem de mensagens na fila de espera for maior que 10 escala-in em quando a contagem de mensagens na fila de espera for inferior a 3. Agora o recurso pode dimensionar entre instâncias de 2 e 10.

Além disso, é um perfil periódico configurar para a segunda-feira. Está definido para instâncias mínimas = 2 e máximas instâncias = 12. Isto significa que na segunda-feira, verifica a primeira autoscale de tempo para esta condição, se a contagem de instância for 2,-escalas para o novo mínimo de 3. Desde que autoscale continua encontrar esta condição do perfil correspondido (segunda), apenas processa as CPU escala out/no regras baseadas configuradas para este perfil. Neste momento, este comando não verifica o comprimento da fila. No entanto, se também pretender que a condição de comprimento fila de ser verificado, deverá incluir as regras do perfil predefinido também no seu perfil segunda-feira.

Da mesma forma, quando autoscale muda novamente para o perfil predefinido, primeiro, verifica se as condições de mínimas e máximas forem cumpridas. Se o número de instâncias ao tempo 12, escalas para 10, o máximo permitido para o perfil predefinido.

![definições de autoscale](./media/insights-autoscale-best-practices/insights-autoscale-best-practices.png)

### <a name="considerations-for-scaling-when-multiple-rules-are-configured-in-a-profile"></a>Considerações para dimensionamento quando estiverem configuradas múltiplas regras de um perfil
Existem casos onde poderá ter de definir várias regras num perfil. O conjunto de regras de autoscale seguinte são utilizadas através da utilização de serviços quando estão definidas múltiplas regras.

Na *escala de saída*, autoscale é executada quando qualquer regra é cumprida.
*Escala-in*, autoscale necessitam de todas as regras de ser cumprida.

Para ilustrar, partem do princípio de que tem as seguintes regras autoscale 4:

- Se CPU < 30%, escala por 1
- Se memória < 50%, escala por 1
- Se CPU > 75%, escala de saída por 1
- Se memória > 75%, escala de saída por 1

Em seguida, ocorre a seguir:

- Se CPU é 76% e memória é 50%, podemos escala de saída.
- Se CPU é 50% e memória é 76% podemos escala de saída.

Por outro lado, se CPU é 25% e memória é 51% autoscale é que **não** escala-in. Na escala-in, CPU tem de ser 29% e da memória 49%.

### <a name="always-select-a-safe-default-instance-count"></a>Selecione sempre uma contagem de instância de seguros predefinido
A contagem de instância predefinida é importante autoscale escalas do seu serviço para esse contar quando métricas não estão disponíveis. Por conseguinte, selecione uma contagem de instância predefinido que é segura para sua das cargas de trabalho.

### <a name="configure-autoscale-notifications"></a>Configurar notificações de autoscale
Autoscale notifica-o a administradores e contribuintes do recurso por correio eletrónico se ocorrer qualquer uma das seguintes condições:

- serviço de autoscale falha efetuar uma ação.
- Métricas não estão disponíveis para o serviço de autoscale tomar uma decisão de escala.
- Métricas estão disponível (recuperação) novamente para tomar uma decisão de escala.
Para além de condições que se segue, pode configurar as notificações de e-mail ou webhook para receber uma notificação para ações de escala com êxito.
