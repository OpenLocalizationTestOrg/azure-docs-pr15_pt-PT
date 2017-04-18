<properties
   pageTitle="Tamanho da pasta TEMP predefinido é demasiado pequeno para uma função | Microsoft Azure"
   description="Uma função de serviço de nuvem tem uma quantidade limitada de espaço para a pasta TEMP. Este artigo fornece algumas sugestões sobre como evitar espaço."
   services="cloud-services"
   documentationCenter=""
   authors="simonxjx"
   manager="felixwu"
   editor=""
   tags="top-support-issue"/>
<tags
   ms.service="cloud-services"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="tbd"
   ms.date="10/12/2016"
   ms.author="v-six" />

# <a name="default-temp-folder-size-is-too-small-on-a-cloud-service-webworker-role"></a>Tamanho da pasta TEMP predefinido é demasiado pequeno numa função de web/trabalhador serviço na nuvem

Directório temporário predefinido de uma função serviço na nuvem trabalhador ou web tem um tamanho máximo de 100 MB, pode tornar-se completo em algumas ponto. Este artigo descreve como evitar a falta de espaço para o directório temporário.

[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="why-do-i-run-out-of-space"></a>Por que motivo executar espaço?

As variáveis de ambiente de Windows padrão TEMP e TMP estão disponíveis para o código que está a ser executado na sua aplicação. TEMP e TMP aponte para um único directório que tenha um tamanho máximo de 100 MB. Quaisquer dados armazenados neste diretório não são mantidos ao longo do ciclo de vida do serviço na nuvem; Se forem recicladas as instâncias de função num serviço de nuvem, é limpo diretório.

## <a name="suggestion-to-fix-the-problem"></a>Sugestões para corrigir o problema

Implementa um dos seguintes alternativas:

- Configurar um recurso de armazenamento local e aceder à mesma diretamente em vez de utilizar TEMP ou TMP. Para aceder a um recurso de armazenamento local a partir de código que está a funcionar dentro da aplicação, contacte o método de [RoleEnvironment.GetLocalResource](https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleenvironment.getlocalresource.aspx) . 

- Configurar um recurso de armazenamento local e aponte os directórios TEMP e TMP para apontarem para o caminho do recurso armazenamento local. Esta modificação deve ser realizada dentro do método [RoleEntryPoint.OnStart](https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.onstart.aspx) .

Exemplo de código que se segue mostra como modificar os directórios de destino para TEMP e TMP a partir de dentro do método OnStart:


```csharp
using System;
using Microsoft.WindowsAzure.ServiceRuntime;

namespace WorkerRole1
{
    public class WorkerRole : RoleEntryPoint
    {
        public override bool OnStart()
        {
            // The local resource declaration must have been added to the
            // service definition file for the role named WorkerRole1:
            //
            // <LocalResources>
            //    <LocalStorage name="CustomTempLocalStore"
            //                  cleanOnRoleRecycle="false"
            //                  sizeInMB="1024" />
            // </LocalResources>

            string customTempLocalResourcePath =
            RoleEnvironment.GetLocalResource("CustomTempLocalStore").RootPath;
            Environment.SetEnvironmentVariable("TMP", customTempLocalResourcePath);
            Environment.SetEnvironmentVariable("TEMP", customTempLocalResourcePath);

            // The rest of your startup code goes here…

            return base.OnStart();
        }
    }
}
```

## <a name="next-steps"></a>Próximos passos

Ler um blogue que descreve [como aumentar o tamanho da Azure Web função ASP.NET pasta temporária](http://blogs.msdn.com/b/kwill/archive/2011/07/18/how-to-increase-the-size-of-the-windows-azure-web-role-asp-net-temporary-folder.aspx).

Ver mais [artigos de resolução de problemas](/?tag=top-support-issue&product=cloud-services) para a serviços em nuvem.

Para saber como resolver problemas de função de serviço de nuvem, utilizando dados de diagnósticos do Azure PaaS computador, ver [série de blogues do Kevin Williamson](http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).
