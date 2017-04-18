<properties
    pageTitle="Referência para programadores do Azure funções | Microsoft Azure"
    description="Compreenda os conceitos de funções do Azure e componentes são comuns a todos os idiomas e enlaces."
    services="functions"
    documentationCenter="na"
    authors="christopheranderson"
    manager="erikre"
    editor=""
    tags=""
    keywords="Azure funções, funções, processamento de eventos, webhooks, cluster dinâmico, sem servidor arquitetura"/>

<tags
    ms.service="functions"
    ms.devlang="multiple"
    ms.topic="reference"
    ms.tgt_pltfrm="multiple"
    ms.workload="na"
    ms.date="05/13/2016"
    ms.author="chrande"/>

# <a name="azure-functions-developer-reference"></a>Referência para programadores do Azure funções

Funções Azure partilham alguns conceitos técnicos principais e os componentes, independentemente do idioma ou encadernação que deve utilizar. Antes de passar para detalhes específicos a um determinado idioma ou encadernação de formação, certifique-se de que leia esta descrição geral que se aplica às mesmas.

Este artigo assume que já leu um [Descrição geral das funções do Azure](functions-overview.md) e estiver familiarizado com [WebJobs SDK conceitos como accionadores, enlaces e o tempo de execução de JobHost](../app-service-web/websites-dotnet-webjobs-sdk.md). Funções Azure baseia o SDK WebJobs. 


## <a name="function-code"></a>Código de função

*Função* é o conceito principal nas funções Azure. Escrever código para uma função num idioma da sua preferência e guardar os ficheiros de código e um ficheiro de configuração na mesma pasta. A configuração é em JSON e o ficheiro é denominado `function.json`. Uma variedade de idiomas são suportados e, cada uma delas tem uma experiência ligeiramente diferente otimizada para funcionar melhor para esse idioma. 

O `function.json` ficheiro contém configuração específica para uma função, incluindo respectivos enlaces. O tempo de execução lê este ficheiro para determinar quais os eventos acionado desativar de quais os dados a incluir quando chamar a função e onde enviar dados passou ao longo da função própria. 

```json
{
    "disabled":false,
    "bindings":[
        // ... bindings here
        {
            "type": "bindingType",
            "direction": "in",
            "name": "myParamName",
            // ... more depending on binding
        }
    ]
}
```

Pode impedir que o tempo de execução de executarem a função definindo a `disabled` propriedade para `true`.

O `bindings` propriedade é onde configurar accionadores e enlaces. Cada ligação de partilha algumas definições comuns e algumas definições que são específicas para um tipo específico de encadernação. Cada enlace requer as seguintes definições:

|Propriedade|Tipos de valores /|Comentários|
|---|-----|------|
|`type`|cadeia|Tipo de ligação. Por exemplo, `queueTrigger`.
|`direction`|'in', 'saída'| Indica se a ligação para receber dados para a função ou enviar dados a partir a função.
| `name` | cadeia | O nome que será utilizado para os dados dependentes na função. Para c# será assim nome de um argumento; para JavaScript estarão na chave de uma lista de chave/valores.

## <a name="function-app"></a>Aplicação de função

Uma aplicação de função é composto por uma ou mais funções individuais que são geridas em conjunto pelo serviço de aplicação do Azure. Todas as funções numa aplicação de função partilhar o mesmo plano da definição de preços, contínua implementação e versão runtime. Funções de escrita em vários idiomas podem partilhar a aplicação de função mesmo. Pense de uma aplicação de função como uma forma de organizar e gerir constituem as funções. 

## <a name="runtime-script-host-and-web-host"></a>Runtime (script anfitrião e anfitrião na web)

O tempo de execução ou script anfitrião, é o anfitrião de WebJobs SDK subjacente que recebe para eventos, reúne e envia dados e executa, finalmente, o seu código. 

Para facilitar a accionadores HTTP, também existe um anfitrião na web que foi concebido para sentar à frente do anfitrião do script em cenários de produção. Isto ajuda a isolar o script anfitrião do tráfego de front-end gerido pelo anfitrião da web.

## <a name="folder-structure"></a>Estrutura de pastas

[AZURE.INCLUDE [functions-folder-structure](../../includes/functions-folder-structure.md)]

Quando cima de definição de um projeto para implementar funções para uma aplicação de função na aplicação de serviço de Azure, pode são processados os esta estrutura de pastas como código de site. Pode utilizar ferramentas existentes como integração contínua e implementação ou implementação personalizada de scripts para fazê-lo implementar a instalação do pacote de tempo ou o código de transpilation.

>[AZURE.NOTE] O `wwwroot` pasta aqui é onde os ficheiros serão implementados para. No entanto, não pode incluir nessa pasta na ficheiros implementar, que seriam conclui-lo com `wwwroot\wwwroot`. Em vez disso, o `host.json` pastas de ficheiros e função devem ser diretamente na raiz da implementa.

## <a id="fileupdate"></a>Como atualizar os ficheiros de aplicação de função

O editor de função incorporado no portal do Azure permite-lhe atualizar o ficheiro de *function.json* e o ficheiro de código para uma função. Para carregar ou actualizar outros ficheiros como *package.json* ou *project.json* ou dependências, tem de utilizar outros métodos de implementação.

Função aplicações foram criadas no serviço de aplicação, para que todas as [Opções de implementação disponíveis para aplicações web padrão](../app-service-web/web-sites-deploy.md) estão disponíveis para as aplicações de função também. Eis alguns métodos que pode utilizar para carregar ou actualizar ficheiros de aplicação de função. 

#### <a name="to-use-app-service-editor"></a>Para utilizar o Editor de serviço de aplicação

1. No portal do Azure funções, clique em **definições de aplicação de função**.

2. Na secção **Definições avançadas** , clique em **Ir para definições do serviço de aplicação**.

3. Em **Ferramentas de desenvolvimento**, clique em **Editor de serviço de aplicação** no Menu da aplicação de navegação.

4.  Clique em **Ir**.

    Depois de carrega o Editor de serviço de aplicação, verá as pastas do ficheiro e função *host.json* em *wwwroot*. 

5. Abrir ficheiros para editá-los, ou arraste e largue a partir do seu computador de desenvolvimento para carregar ficheiros.

#### <a name="to-use-the-function-apps-scm-kudu-endpoint"></a>Utilizar o ponto final de SMS (Kudu) a aplicação de função

1. Navegue para: `https://<function_app_name>.scm.azurewebsites.net`.

2. Clique em **Consola de depuração > CMD**.

3. Navegue para `D:\home\site\wwwroot\` para atualizar *host.json* ou `D:\home\site\wwwroot\<function_name>` para actualizar ficheiros de uma função.

4. Arrastar e largar um ficheiro que pretende carregar para a pasta na grelha de ficheiro adequada. Existem duas áreas na grelha de ficheiro onde pode colocar um ficheiro. Para os ficheiros *. zip* , é apresentada uma caixa com a etiqueta "Arraste aqui para carregar e descomprimir o ficheiro.." Para outros tipos de ficheiro, largue na grelha de ficheiro, mas fora da caixa "descomprimir o ficheiro.".

#### <a name="to-use-ftp"></a>Para utilizar o FTP

1. Siga as instruções [aqui](../app-service-web/web-sites-deploy.md#ftp) para obter FTP configurado.

2. Quando estiver ligado ao site de aplicação de função, copiar um ficheiro atualizado *host.json* para `/site/wwwroot` ou copiar ficheiros de função para `/site/wwwroot/<function_name>`.

#### <a name="to-use-continuous-deployment"></a>Para utilizar implementação contínua

Siga as instruções no tópico de [implementação do Azure funções como contínua](functions-continuous-deployment.md).

## <a name="parallel-execution"></a>Execução paralela

Quando ocorre um evento acionador vários mais depressa do que um tempo de execução de função por tópicos único pode processá-las, o tempo de execução pode invocar a função várias vezes em paralelo.  Se uma aplicação de função estiver a utilizar o [Plano de serviço dinâmicos](functions-scale.md#dynamic-service-plan), a aplicação de função poderia Dimensionar automaticamente saída.  Cada instância da aplicação função, se a aplicação é executada no plano de serviço dinâmicos ou um normal [Plano de serviço de aplicação](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md), poderá processar exe função em simultâneo, paralelas utilizando vários threads.  O número máximo de exe função em simultâneo em cada ocorrência de aplicação de função varia consoante o tamanho da memória da aplicação de função. 

## <a name="azure-functions-pulse"></a>Funções Azure Pulse  

Pulse é um fluxo de evento direto que apresenta o número de vezes que a função é executado, assim como êxitos e falhas. Também pode monitorizar o seu tempo de execução média. Vamos irá adicionar mais funcionalidades e personalização ao mesmo ao longo do tempo. Pode aceder à página de **Pulse** a partir do separador de **monitorização** .

## <a name="repositories"></a>Repositórios

O código para as funções de Azure é abrir origem e armazenadas no GitHub repositórios:

* [Azure runtime de funções](https://github.com/Azure/azure-webjobs-sdk-script/)
* [Azure portal de funções](https://github.com/projectkudu/AzureFunctionsPortal)
* [Modelos de funções Azure](https://github.com/Azure/azure-webjobs-sdk-templates/)
* [Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk/)
* [Extensões Azure WebJobs SDK](https://github.com/Azure/azure-webjobs-sdk-extensions/)

## <a name="bindings"></a>Enlaces

Eis uma tabela de todos os enlaces suportados.

[AZURE.INCLUDE [dynamic compute](../../includes/functions-bindings.md)]

## <a name="reporting-issues"></a>Relatar problemas

[AZURE.INCLUDE [Reporting Issues](../../includes/functions-reporting-issues.md)] 

## <a name="next-steps"></a>Próximos passos

Para obter mais informações, consulte os seguintes recursos:

* [Azure funções c# de referência para programadores](functions-reference-csharp.md)
* [Azure funções F # de referência para programadores](functions-reference-fsharp.md)
* [Referência para programadores do Azure NodeJS de funções](functions-reference-node.md)
* [Azure accionadores de funções e enlaces](functions-triggers-bindings.md)
* [Azure funções: viagem de](https://blogs.msdn.microsoft.com/appserviceteam/2016/04/27/azure-functions-the-journey/) no blogue de equipa do serviço de aplicação do Azure. Um histórico das como foi desenvolvida Azure funções.





