<properties 
    pageTitle="Nova versão do esquema 2016-06-01 | Microsoft Azure" 
    description="Saiba como escrever a definição de JSON para a versão mais recente das aplicações de lógica" 
    authors="jeffhollan" 
    manager="dwrede" 
    editor="" 
    services="logic-apps" 
    documentationCenter=""/>

<tags
    ms.service="logic-apps"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/25/2016"
    ms.author="jehollan"/>
    
# <a name="new-schema-version-2016-06-01"></a>Nova versão do esquema 2016-06-01

O nova esquema e a API versão para as aplicações de lógica tem um número de melhoramentos de melhorar a fiabilidade e facilidade de utilização de aplicações de lógica. Existem diferenças fundamentais 3:

1. Adição de âmbitos, que são as ações que contêm uma colecção de ações.
1. Condições e ciclos são ações primeira classe
1. Ordenação mais verboso através da execução `runAfter` propriedade (que substitui `dependsOn`)

Para obter informações sobre como atualizar as suas aplicações de lógica a partir do esquema de 2015-08-01-pré-visualização para o esquema de 2016-06-01, [dar saída a secção atualização abaixo.](#upgrading-to-2016-06-01-schema)


## <a name="1-scopes"></a>1. âmbitos de

Uma das maiores mudanças neste esquema é a adição de âmbitos e a capacidade de aninhar ações entre si.  Isto é útil quando agrupar um conjunto de ações ou quando necessário especificá-las aninhar ações dentro de outro (por exemplo uma condição pode conter outra condição).  Obter mais detalhes sobre a sintaxe de âmbito podem ser encontrado [aqui](app-service-logic-loops-and-scopes.md), mas um exemplo simples âmbito pode ser encontrado abaixo:


```
{
    "actions": {
        "My_Scope": {
            "type": "scope",
            "actions": {                
                "Http": {
                    "inputs": {
                        "method": "GET",
                        "uri": "http://www.bing.com"
                    },
                    "runAfter": {},
                    "type": "Http"
                }
            }
        }
    }
}
```

## <a name="2-conditions-and-loops-changes"></a>2. condições e ciclos de alterações

Em versões anteriores do esquema, condições e ciclos foram associados a uma única acção de parâmetros.  Esta limitação tenha sido levantada neste esquema e agora condições e ciclos aparecem como um tipo de ação.  Podem encontrar mais informações [neste artigo](app-service-logic-loops-and-scopes.md)e um exemplo simples de uma ação de condição é mostrado abaixo:

```
{
    "If_trigger_is_foo": {
        "type": "If",
        "expression": "@equals(triggerBody(), 'foo')",
        "runAfter": { },
        "actions": {
            "Http_2": {
                "inputs": {
                    "method": "GET",
                    "uri": "http://www.bing.com"
                },
                "runAfter": {},
                "type": "Http"
            }
        },
        "else": 
        {
            "if_trigger_is_bar": "..."
        }      
    }
}
```

## <a name="3-runafter-property"></a>3. propriedade RunAfter

O novo `runAfter` propriedade está a substituir `dependsOn` para o ajudar a permitir mais precisão na ordenação executar.  `dependsOn`foi sinónima de "a ação executou e foi efetuada com êxito," no entanto, muitas vezes precisar de executar uma ação se a acção anterior ser efetuada com êxito, falha ou ignorada.  `runAfter`permite que flexibilidade.  É um objeto que especifica todos os nomes de ação que é executada após e define uma matriz de estado que são aceites accionador a partir de.  Por exemplo, se pretender executar após passo A teve êxito e o passo B foi foi concluída com êxito ou Ocorreu uma falha, teria de construir o seguinte `runAfter` propriedade:

```
{
    "...",
    "runAfter": {
        "A": ["Succeeded"],
        "B": ["Succeeded", "Failed"]
    }
}
```

## <a name="upgrading-to-2016-06-01-schema"></a>Atualizar para o esquema de 2016-06-01

Atualizar para o novo esquema de 2016-06-01 bastam alguns passos.  Para obter detalhes sobre as alterações do esquema podem ser encontrados [neste artigo](app-service-logic-schema-2016-04-01.md).  O processo de atualização inclui a executar o script atualização, guardar como uma nova aplicação de lógica e potencialmente substituir a aplicação antiga do lógica caso seja necessário.

1. Abra a sua aplicação de lógica atual.
1. Clique no botão de **Atualização esquema** na barra de ferramentas
   
    ![][1]
   
    A definição atualizada será devolvida.  Que poderia copiar e colar este uma definição de recursos se precisar, mas **Recomendamos vivamente** que utilize o botão **Guardar como** para se certificar de ligação de todos os referências são válidas na aplicação lógica atualizado.
1. Clique no botão **Guardar como** na barra de ferramentas do pá a atualização.
1. Preencha o estado da aplicação nome e lógica e clique em **Criar** para implementar a aplicação de lógica de atualização.
1. Verificar a que sua aplicação de lógica atualizado está a funcionar como esperado.

    >[AZURE.NOTE] Se estiver a utilizar um accionador manual ou o pedido, o URL de chamada de retorno irá foram alterados na sua nova aplicação de lógica.  Utilize o novo URL para verificar funciona ponto a ponto e pode clonar ao longo da sua aplicação de lógica existente preservar URLs anterior.

1. *Opcional* Utilize o botão de **clonar** na barra de ferramentas (adjacente para o ícone de **Actualização de esquema** na imagem acima) para substituir a sua aplicação de lógica anterior com a nova versão do esquema.  Isto é necessário apenas se pretender manter o ID do recurso mesmo ou pedir accionador URL da sua aplicação de lógica.

### <a name="upgrade-tool-notes"></a>Ferramenta de atualização notas

#### <a name="condition-mapping"></a>Mapeamento de condição

A ferramenta irá tornar um esforço de utilizações para agrupar as ações de ramo true e false em conjunto num âmbito na definição do atualizado.  Especificamente, o estruturador padrão de `@equals(actions('a').status, 'Skipped')` deve ser apresentada como um `else` ação.  No entanto se a ferramenta detectar padrões não reconhece potencialmente criará condições em separado para o verdadeiro e FALSO ramo.  Acções podem ser novamente mapeadas publicar atualização, se necessário.

#### <a name="foreach-with-condition"></a>ForEach com a condição
  
O padrão de anterior de um ciclo foreach com uma condição por produto pode ser replicado no esquema de novo com a ação de filtro.  Isto deve ocorrer automaticamente no atualização.  A condição torna-se uma acção de filtro antes do ciclo de foreach (para devolver apenas uma matriz de itens que correspondam a condição), e essa matriz lhe é transmitida para a ação foreach.  Pode ver um exemplo deste [neste artigo](app-service-logic-loops-and-scopes.md)

#### <a name="resource-tags"></a>Etiquetas de recursos

Etiquetas de recursos serão removidas no atualização e terá de defini-las novamente para o fluxo de trabalho atualizado.

## <a name="other-changes"></a>Outras alterações

### <a name="manual-trigger-renamed-to-request-trigger"></a>Manual accionador cujo nome foi mudado ao pedido de accionador

O tipo de `manual` foi preterida e o nome alterado para `request` com o tipo de `http`.  Este é mais consistente com o tipo de padrão que ao accionador é utilizado para construir.

### <a name="new-filter-action"></a>Nova ação 'Filtro'

Se estiver a trabalhar com uma matriz de grandes dimensões e preciso de filtrá-lo para baixo para um conjunto mais pequeno de itens, pode utilizar o novo tipo de 'Filtro'.  Aceita uma matriz e uma condição e irá avaliar a condição para cada item e devolver uma matriz de itens que satisfaçam a condição.

### <a name="foreach-and-until-action-restrictions"></a>ForEach e até restrições de ação

O foreach e até ciclo restringido a uma única ação.

### <a name="trackedproperties-on-actions"></a>TrackedProperties no ações

Ações agora podem ter numa propriedade adicional (colaterais para `runAfter` e `type`) denominado `trackedProperties`.  É um objeto que especifica determinadas entradas de ação ou resultados devem ser incluídas de telemetria Azure diagnóstico que é emitida como parte de um fluxo de trabalho.  Por exemplo:

```
{                
    "Http": {
        "inputs": {
            "method": "GET",
            "uri": "http://www.bing.com"
        },
        "runAfter": {},
        "type": "Http",
        "trackedProperties": {
            "responseCode": "@action().outputs.statusCode",
            "uri": "@action().inputs.uri"
        }
    }
}
```

## <a name="next-steps"></a>Próximos passos
- [Utilizar a definição de fluxo de trabalho da aplicação de lógica](app-service-logic-author-definitions.md)
- [Criar um modelo de implementação de aplicação de lógica](app-service-logic-create-deploy-template.md)


<!-- Image references -->
[1]: ./media/app-service-logic-schema-2016-04-01/upgradeButton.png
