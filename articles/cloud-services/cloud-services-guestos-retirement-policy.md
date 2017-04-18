<properties 
   pageTitle="Compatibilidade e a política de reforma guia para Azure convidado SO | Microsoft Azure" 
   description="Fornece informações sobre o que irá suporte Microsoft no que diz respeito ao Azure convidado SO utilizado pelos serviços em nuvem." 
   services="cloud-services" 
   documentationCenter="na" 
   authors="raiye" 
   manager="timlt" 
   editor=""/>

<tags
   ms.service="cloud-services"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="tbd" 
   ms.date="10/24/2016"
   ms.author="raiye"/>

# <a name="azure-guest-os-supportability-and-retirement-policy"></a>Política Azure de compatibilidade e extinção de SO convidado
As informações nesta página está relacionada com o sistema operativo da Azure convidado ([SO convidado](cloud-services-guestos-update-matrix.md)) para o trabalho de serviços em nuvem e funções web (PaaS). Não se aplica a máquinas virtuais (IaaS). 

A Microsoft tem um publicados [suportar a política para o SO convidado](http://support.microsoft.com/gp/azure-cloud-lifecycle-faq). A página que está a ler agora descreve como a política é implementada.

A política é 

1. Microsoft irá suportar **, pelo menos, as famílias de tipos de duas mais recentes do SO convidado**. Quando uma família é foi removida, clientes tem doze meses a partir da data de reforma oficial que Atualize para uma família de SO convidado suportada mais recente.
2. Microsoft irá suportar a **, pelo menos, duas versões mais recentes do famílias de tipos de SO convidado suportadas**. 
3. Microsoft irá suportar na **, pelo menos, as duas versões mais recentes do Azure SDK**. Quando uma versão do SDK é foi removida, clientes terão de 12 meses a partir da data de reforma oficial que Atualize para uma versão mais recente. 

Por vezes mais de duas versões ou famílias de tipos de poderão ser suportadas. Informações de suporte do oficiais SO convidado irão aparecer no [Azure convidado SO lançamentos e da matriz de compatibilidade de SDK](cloud-services-guestos-update-matrix.md).


## <a name="when-a-guest-os-family-or-version-is-retired"></a>Quando é foi removida uma família de SO convidado ou uma versão 


Uma nova do SO convidado **família** é introduzido algures após o lançamento de uma nova versão oficial do sistema operativo Windows Server. Sempre que for introduzida uma família SO convidado nova, Microsoft irá retirar da família mais antiga do SO convidado. 

Novas SO convidado **versões** são introduzidos cerca de cada mês para incorporar as atualizações mais recentes do MSRC. Devido as atualizações mensais normais, uma versão do SO convidado é normalmente desativados 60 dias após o lançamento. Isto mantém, pelo menos, duas versões de SO convidado para cada família disponível para utilização. 

### <a name="process-during-a-guest-os-family-retirement"></a>Processo de durante um extinção da família do SO convidado 


Assim que a extinção é anunciada, os clientes têm um período de 12 meses "transição" antes da família mais antiga oficialmente é removida do serviço. Desta vez transição pode ser prolongada ao critério da Microsoft. Atualizações serão registadas no [Azure convidado SO lançamentos e da matriz de compatibilidade de SDK](cloud-services-guestos-update-matrix.md).

Um processo de reforma gradual começará 6 meses para o período de transição. Durante este período de tempo:

1. Microsoft irá notificá-os clientes para a reforma. 
2. A versão mais recente do Azure SDK não suporta a família SO convidado retirada.
3. Novas implementações e redeployments dos serviços em nuvem não são permitidos da família retirada

Microsoft irão continuar a apresentar a nova versão do SO convidado incorporar as atualizações mais recentes do MSRC até ao último dia do período de transição, conhecido como "data de expiração". Nesse momento, quaisquer serviços na nuvem ainda em execução será não ser suportados em SLA o Azure. A Microsoft tem o critério para forçar atualização, eliminar ou parar desses serviços após essa data.



### <a name="process-during-a-guest-os-version-retirement"></a>Processo de durante um extinção de versão do SO convidado 
Se os clientes definir os respetivos SO convidado para atualizar automaticamente, nunca têm de se preocupar lidar com as versões do SO convidado. Estes sempre utilizará a versão mais recente do SO convidado.

Versões de SO convidado são lançadas todos os meses. Devido a taxa de lançamentos normais, cada versão tem uma fixa esperança de vida.

Em 60 dias para o esperança de vida está uma versão "*desativado*". "Desativado" significa que a versão é removida do portal do Azure clássico. Também pode já não ser definido a partir do ficheiro de configuração de CSCFG. Implementações existentes são deixadas em execução, mas implementações novas e atualizações de código e configuração para implementações existentes não são permitidas. 

Posteriormente, o SO convidado versão "*expira*" e quaisquer instalações versão ainda é forçar atualizado e configurar para atualizar automaticamente o SO convidado no futuro. Expiração é feita em lotes para que o período de tempo do grandes inválidos ao expiração pode variar. 

Estes períodos podem ser feitos mais longo do critério da Microsoft para facilitar a transições de cliente. Quaisquer alterações serão comunicadas no [Azure convidado SO lançamentos e da matriz de compatibilidade de SDK](cloud-services-guestos-update-matrix.md).



### <a name="notifications-during-retirement"></a>Notificações durante a extinção 

* **Extinção da família** <br>Serão utilizadas pela Microsoft mensagens no blogue e notificação de portal clássica Azure. Clientes que ainda estão a utilizar uma família SO convidado retirada serão notificados através de comunicação direta (correio eletrónico, mensagens portais, chamada telefónica) para os administradores de serviço atribuído. Todas as alterações serão registadas a esta página e o feed RSS listado no início desta página. 


* **Versão extinção** <br>Todas as alterações serão registadas a esta página e o feed RSS listado no início desta página, incluindo a versão, desativada e datas de expiração. Administradores de serviços irão receber mensagens de correio eletrónico, caso tenham implementações em execução numa versão do SO de convidado desativado ou família. A temporização destes e-mails pode variar. Geralmente são, pelo menos, um mês antes grandes inválidos, apesar desta temporização não é uma SLA oficial. 


## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

**Como posso mitigar como as consequências decorrentes da migração?**

Deve utilizar família mais recente do SO convidado para estruturar os seus serviços na nuvem. 

1. Começar a planear a sua migração mais cedo para uma família mais recente. 
2. Configure implementações temporário teste para testar o seu serviço na nuvem na família do nova. 
3. Definir a sua versão do SO convidado para **Automático** (osVersion = * no ficheiro [.cscfg](cloud-services-model-and-package.md#cscfg) ) para que a migração para novas versões de SO convidado ocorre automaticamente.

**O que acontece se a minha aplicação web requer mais aprofundada integração com o SO?**

Se a sua arquitetura da aplicação web exigir mais aprofundada dependência de sistema operativo subjacente, utilizar plataforma suportadas capacidades, tais como [tarefas de arranque](cloud-services-startup-tasks.md) ou outros mecanismos de expansão que possam existir no futuro. Em alternativa, também pode utilizar [máquinas virtuais do Azure](https://azure.microsoft.com/documentation/scenarios/virtual-machines/) (IaaS – infraestrutura como um serviço), onde for o responsável da manutenção de sistema operativo subjacente.
 
## <a name="next-steps"></a>Próximos passos
Reveja o mais recente [disponibilização de SO convidado](cloud-services-guestos-update-matrix.md).
