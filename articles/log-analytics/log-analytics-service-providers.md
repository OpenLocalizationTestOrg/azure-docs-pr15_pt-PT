<properties
    pageTitle="Iniciar sessão funcionalidades de análise para fornecedores de serviços | Microsoft Azure"
    description="Análise de registo pode ajudar gerido fornecedores de serviço (MSPs), grandes empresas, independentes Sofware MVPs e fornecedores de serviço de alojamento gerir e controlar os servidores no local do cliente ou infraestrutura da nuvem."
    services="log-analytics"
    documentationCenter=""
    authors="richrundmsft"
    manager="jochan"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/25/2016"
    ms.author="richrund"/>

# <a name="log-analytics-features-for-service-providers"></a>Funcionalidades de análise de registo para fornecedores de serviços

Pode ajudar a análise de registo fornecedores de serviço gerido (MSPs), grandes empresas, independentes de software MVPs e serviço fornecedores de alojamento de gerir e controlar os servidores no local do cliente ou infraestrutura da nuvem. 

Grandes empresas partilham muitas semelhanças com fornecedores de serviço, especialmente quando existe um departamento de TI centralizada que é o responsável pela gestão de IT para muitos diferentes unidades de negócio. Para simplificar, este documento utiliza o termo *fornecedor de serviços* mas a mesma funcionalidade também está disponível para empresas e outros clientes.

## <a name="cloud-solution-provider"></a>Fornecedor de solução da nuvem

Parceiros e fornecedores de serviço que fazem parte do programa do [Fornecedor de solução da nuvem (CSP)](https://partner.microsoft.com/Solutions/cloud-reseller-overview) , a análise de registo é um dos serviços Azure disponíveis numa subscrição CSP. 

Para análise de registo, as seguintes capacidades estão activadas no *Fornecedor de solução da nuvem* subscrições.

Como um *Fornecedor de solução na nuvem* , pode:

+ Crie áreas de trabalho de análise de registo na subscrição de um inquilino (cliente).
+ Áreas de trabalho do Access criadas por inquilinos. 
+ Adicionar e remover o acesso do utilizador para a área de trabalho com gestão de utilizadores Azure. Quando a página na área de trabalho de um inquilino no portal do OMS em gestão de utilizadores definições não está disponível
  - Análise de registo não suporta a acesso baseado em funções ainda - dando um utilizador `reader` permissão no portal do Azure permite-las fazer alterações de configuração no portal do OMS

Para iniciar sessão subscrição de um inquilino, é necessário especificar o identificador de inquilino. O identificador de inquilino é frequentemente essa última parte do endereço de e-mail utilizado para iniciar sessão.

+ No portal do OMS, adicionar `?tenant=contoso.com` no URL para o portal. Por exemplo,`mms.microsoft.com/?tenant=contoso.com`
+ No PowerShell, utilize o `-Tenant contoso.com` parâmetro ao utilizar `Add-AzureRmAccount` cmdlet
+ O identificador de inquilino é adicionado automaticamente ao utilizar o `OMS portal` ligação a partir do Azure portal para abrir e iniciar sessão no portal do OMS para a área de trabalho selecionada

Como um *cliente* de um fornecedor de solução na nuvem, pode:

+ Criar o registo de áreas de trabalho de análise numa subscrição CSP
+ Áreas de trabalho do Access criadas pelo CSP
  -  Utilizar o `OMS portal` ligação a partir do Azure portal para abrir e iniciar sessão no portal do OMS para a área de trabalho selecionada
+ Ver e utilizar a página de gestão de utilizador em definições no portal do OMS

>[AZURE.NOTE] As soluções de cópia de segurança e recuperação de sites para análise de registo não estão possível ligar a um cofre de serviços de recuperação e não podem ser configuradas de uma subscrição CSP.

## <a name="managing-multiple-customers-using-log-analytics"></a>Gerir vários clientes utilizando a análise de registo 

É recomendável que cria uma área de trabalho de análise de registo de cada cliente que gerir. Fornece uma área de trabalho de análise de registo:

+ Uma localização geográfica para dados sejam armazenados. 
+ Granularidade para faturação 
+ Isolamento de dados 
+ Configuração exclusiva

Ao criar uma área de trabalho por cliente, está conseguir manter os dados de cada cliente separados e também monitorizar a utilização de cada cliente.

Obter mais detalhes sobre quando e por que motivo criar várias áreas de trabalho é descrito [gerir o acesso para iniciar sessão analytics] (log-analytics-manage-access.md#determine-the-number-of-workspaces-you-need).

Criação e de configuração de áreas de trabalho do cliente podem ser automatizados através do [PowerShell](log-analytics-powershell-workspace-configuration.md), [modelos de Gestor de recursos](log-analytics-template-workspace-configuration.md), ou utilizar a [REST API](https://www.nuget.org/packages/Microsoft.Azure.Management.OperationalInsights/).

A utilização de modelos de Gestor de recursos para a configuração de área de trabalho permite-lhe ter uma configuração principal que pode ser utilizada para criar e configurar áreas de trabalho. Pode ter a certeza de que tal como são criadas as áreas de trabalho para clientes são automaticamente configurados com os seus requisitos. Quando atualiza os requisitos, o modelo é atualizado e, em seguida, voltar a aplicado as áreas de trabalho existentes. Este processo garante que as áreas de trabalho mesmo existentes cumprirem os padrões de novos.    

Quando a gerir a várias áreas de trabalho de análise de registo, recomendamos que integrar cada área de trabalho com o seu sistema de bilheteira existente consola de operações com a funcionalidade de [alertas](log-analytics-alerts.md) . Ao integrar com os sistemas de existentes, a equipa de suporte pode continuar a seguir seus processos familiares. Registo regularmente verifica cada área de trabalho contra os critérios de alerta que especificar e de análise gera um alerta quando é necessária uma ação.

Para relatórios de nível executivos que resumir dados ao longo de áreas de trabalho pode utilizar a integração entre [obter](log-analytics-powerbi.md)e de análise de registo. Se precisar de integrar com o outro sistema de elaboração de relatórios, pode utilizar a API de pesquisa (através do PowerShell ou [resto](log-analytics-log-search-api.md)) para executar consultas e exportar os resultados da pesquisa.

## <a name="next-steps"></a>Próximos passos

+ Automatizar a criação e de configuração de áreas de trabalho utilizar [modelos de Gestor de recursos](log-analytics-template-workspace-configuration.md)
+ Automatizar a criação de áreas de trabalho através do [PowerShell](log-analytics-powershell-workspace-configuration.md) 
+ Utilizar os [alertas](log-analytics-alerts.md) para integrar com os sistemas existentes
+ Gerar relatórios de resumo, utilizando o [obter](log-analytics-powerbi.md)
