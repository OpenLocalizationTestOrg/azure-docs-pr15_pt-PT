<properties
   pageTitle="Interagir com relatórios de utilizar a API JavaScript | Microsoft Azure"
   description="Power BI incorporado, interagir com relatórios de utilizar a API JavaScript"
   services="power-bi-embedded"
   documentationCenter=""
   authors="guyinacube"
   manager="erikre"
   editor=""
   tags=""/>
<tags
   ms.service="power-bi-embedded"
   ms.devlang="NA"
   ms.topic="hero-article"
   ms.tgt_pltfrm="NA"
   ms.workload="powerbi"
   ms.date="10/04/2016"
   ms.author="asaxton"/>

# <a name="interact-with-power-bi-reports-using-the-javascript-api"></a>Interagir com relatórios de Power BI, utilizar a API JavaScript

A API do Power BI JavaScript permite-lhe incorporar facilmente relatórios do Power BI na suas aplicações. Com a API, as suas aplicações através de programação podem interagir com elementos de relatório diferentes como filtros e páginas. Este interatividade torna a uma parte mais integrada da sua aplicação de relatórios do Power BI.

Incorporar um relatório do Power BI na sua aplicação ao utilizar iframe que está alojado como parte da aplicação. A iframe age como um limite entre as aplicações e um relatório, como pode ver na seguinte imagem. 

![Power BI incorporado iframe sem Javascript API](media\powerbi-embedded-interact-with-reports\powerbi-embedded-interact-report-1.png)

A iframe facilita o processo de incorporação muitas, mas sem a API JavaScript o relatório e a sua aplicação não é possível interagir uns com os outros. Pode tornar esta falta de interação acha que o relatório não é realmente parte da aplicação. O relatório e a aplicação necessitam realmente comunicar e para trás, tal como a imagem seguinte.

![Power BI incorporado iframe com a Javascript API](media\powerbi-embedded-interact-with-reports\powerbi-embedded-interact-report-2.png)

A API do Power BI JavaScript permite-lhe escrever código que pode segura passar o limite de iframe. Permite que as aplicações do através de programação, efetue uma ação num relatório de e para ouvir para eventos de ações que os utilizadores fazer dentro do relatório.

## <a name="what-can-you-do-with-the-power-bi-javascript-api"></a>O que pode fazer com a API do Power BI JavaScript?
Com a API JavaScript pode gerir relatórios, navegue para páginas de um relatório, filtrar um relatório e processar incorporação eventos. O diagrama seguinte mostra a estrutura da API do.

![Diagrama do Power BI JavaScript API](media\powerbi-embedded-interact-with-reports\powerbi-embedded-interact-report-3.png)


### <a name="manage-reports"></a>Gerir relatórios
A API Javascript permite-lhe gerir o comportamento de nível do relatório e página:

- Incorporar um relatório de BI Power específico segura na sua aplicação - tente [incorporar a aplicação de demonstração](http://azure-samples.github.io/powerbi-angular-client/#/scenario1)
  - Conjunto token de acesso
- Configurar o relatório
  - Activar e desactivar o painel de filtro e o painel de navegação - tente [atualizar a aplicação de demonstração de definições](http://azure-samples.github.io/powerbi-angular-client/#/scenario6)
  - Definir predefinições para páginas e filtros - tentar [Configurar demonstração predefinições](http://azure-samples.github.io/powerbi-angular-client/#/scenario5)
- Introduza e sair do modo de ecrã inteiro

[Saiba mais acerca de como incorporar um relatório](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Embedding-Basics)


### <a name="navigate-to-pages-in-a-report"></a>Navegue para páginas de um relatório
A JavaScript API enbales que para descobrir todas as páginas num relatório de e para definir a página atual. Experimente a [aplicação de demonstração de navegação](http://azure-samples.github.io/powerbi-angular-client/#/scenario3).

[Saiba mais sobre navegação entre páginas](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Page-Navigation)

### <a name="filter-a-report"></a>Filtrar um relatório
A API JavaScript fornece básicos e avançados capacidades para relatórios incorporados e páginas do relatório de filtragem. Experimente a [aplicação de demonstração de filtragem](http://azure-samples.github.io/powerbi-angular-client/#/scenario4)e reveja algum código introdutório aqui.  


#### <a name="basic-filters"></a>Filtros básicos
Um filtro básico é colocado num nível de hierarquia ou coluna e contém uma lista de valores para incluir ou excluir.

```
const basicFilter: pbi.models.IBasicFilter = {
  $schema: "http://powerbi.com/product/schema#basic",
  target: {
    table: "Store",
    column: "Count"
  },
  operator: "In",
  values: [1,2,3,4]
}
```


#### <a name="advanced-filters"></a>Filtros avançados
Filtros avançados utilizam o operador lógico e ou ou e aceitar uma ou duas condições, cada uma com os seus próprios operador e um valor. Condições suportadas são:

- Nenhum
- LessThan
- LessThanOrEqual
- GreaterThan
- GreaterThanOrEqual
- Contém
- DoesNotContain
- StartsWith
- DoesNotStartWith
- É
- IsNot
- É. CÉL.
- IsNotBlank

```
const advancedFilter: pbi.models.IAdvancedFilter = {
  $schema: "http://powerbi.com/product/schema#advanced",
  target: {
    table: "Store",
    column: "Name"
  },
  logicalOperator: "Or",
  conditions: [
    {
      operator: "Contains",
      value: "Wash"
    },
    {
      operator: "Contains",
      value: "Park"
    }
  ]
}
```
[Saiba mais sobre filtrar](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Filters)


### <a name="handling-events"></a>Processamento de eventos
Para além de informações para a iframe a enviar, a aplicação também poderá receber informações sobre os seguintes eventos provenientes do iframe:

- Incorporar
  - carregado
  - erro
- Relatórios
  - pageChanged
  - dataSelected (brevemente)

[Mais informações sobre o processamento de eventos](https://github.com/Microsoft/PowerBI-JavaScript/wiki/Handling-Events)


## <a name="next-steps"></a>Próximos passos
Para obter mais informações sobre a API do Power BI JavaScript, consulte as ligações seguintes:

- [Wiki de API JavaScript](https://github.com/Microsoft/PowerBI-JavaScript/wiki)
- [Referência do modelo de objeto](https://microsoft.github.io/powerbi-models/modules/_models_.html)
- Amostras
  - [Angular](http://azure-samples.github.io/powerbi-angular-client)
  - [Ember](https://github.com/Microsoft/powerbi-ember)
- [Demonstração direto](https://microsoft.github.io/PowerBI-JavaScript/demo/)
