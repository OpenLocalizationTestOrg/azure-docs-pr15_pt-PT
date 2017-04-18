<properties
    pageTitle="Resolução de problemas dos diagnósticos do Azure"
    description="Resolução de problemas quando utilizar diagnósticos do Azure Azure serviços em nuvem, máquinas virtuais e "
    services="multiple"
    documentationCenter=".net"
    authors="rboucher"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="10/04/2016"
    ms.author="robb"/>


# <a name="azure-diagnostics-troubleshooting"></a>Resolução de problemas dos diagnósticos do Azure
Resolução de problemas informações relevantes para utilizar o Azure diagnóstico. Para mais informações sobre diagnósticos do Azure, consulte o artigo [Descrição geral dos diagnósticos do Azure](azure-diagnostics.md#cloud-services).

## <a name="azure-diagnostics-is-not-starting"></a>Não está a iniciar o diagnósticos do Azure

Diagnósticos são composto por dois componentes: um plug-in de agente de convidado e o agente de monitorização. Pode verificar os ficheiros de registo **DiagnosticsPluginLauncher.log** e **DiagnosticsPlugin.log** para obter informações sobre por que motivo não inicia diagnósticos.  
  
Função um serviço na nuvem, ficheiros de registo para o plug-in de agente do convidado estiver localizados na: 

``` 
C:\Logs\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\1.6.3.0\ 
``` 

Na máquina de Virtual uma Azure, ficheiros de registo para o plug-in de agente do convidado estiver localizados na: 

``` 
C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\1.6.3.0\Logs\ 
``` 
 
A última linha dos ficheiros de registo irá conter o código de saída.  

``` 
DiagnosticsPluginLauncher.exe Information: 0 : [4/16/2016 6:24:15 AM] DiagnosticPlugin exited with code 0 
``` 

O plug-in devolve os seguintes códigos de saída:

Código de saída|Descrição
---|---
0|Sucesso.
-1|Erro genérico.
-2|Não é possível carregar o ficheiro rcf.<p>Este é um erro interno que deverá ocorrer apenas se o iniciador de plug-in do agente de convidado é manualmente invocar, incorretamente, a VM.
-3|Não é possível carregar o ficheiro de configuração de diagnóstico.<p><p>Solução: Este é o resultado de um ficheiro de configuração não prisma validação de esquema. A solução é fornecer um ficheiro de configuração está em conformidade com o esquema.
-4|Outra instância do agente de monitorização diagnóstico já está a utilizar o diretório de recursos locais.<p><p>Solução: Especifique um valor diferente para **LocalResourceDirectory**.
-6|No iniciador de plug-in do agente de convidado tentou iniciação diagnóstico com uma linha de comandos inválido.<p><p>Este é um erro interno que deverá ocorrer apenas se o iniciador de plug-in do agente de convidado é manualmente invocar, incorretamente, a VM.
-10|O plug-in diagnóstico terminou com uma exceção não processada.
-11|O agente de convidado não foi possível criar o processo responsável pela iniciando e monitorizar o agente de monitorização.<p><p>Solução: Verifique que suficientes sistema recursos estão disponíveis para novos processos de iniciação.<p>
-101|Argumentos inválidos ao chamar o plug-in diagnóstico.<p><p>Este é um erro interno que deverá ocorrer apenas se o iniciador de plug-in do agente de convidado é manualmente invocar, incorretamente, a VM.
-102|O processo de plug-in é não é possível iniciar própria.<p><p>Solução: Verifique que suficientes sistema recursos estão disponíveis para novos processos de iniciação.
-103|O processo de plug-in é não é possível iniciar própria. Especificamente é não é possível criar o objeto do registo.<p><p>Solução: Verifique que suficientes sistema recursos estão disponíveis para novos processos de iniciação.
-104|Não é possível carregar o ficheiro de rcf fornecido pelo agente de convidado.<p><p>Este é um erro interno que deverá ocorrer apenas se o iniciador de plug-in do agente de convidado é manualmente invocar, incorretamente, a VM.
-105|O plug-in de diagnóstico não é possível abrir o ficheiro de configuração de diagnóstico.<p><p>Este é um erro interno que deverá ocorrer apenas se o plug-in de diagnóstico é manualmente invocar, incorretamente, a VM.
-106|Não consegue ler o ficheiro de configuração diagnóstico.<p><p>Solução: Este é o resultado de um ficheiro de configuração não prisma validação de esquema. Por isso, a solução é fornecer um ficheiro de configuração está em conformidade com o esquema. Pode encontrar o XML que seja entregue a extensão de diagnóstico na pasta *%SystemDrive%\WindowsAzure\Config* na VM. Abra o ficheiro XML e pesquisa para **Microsoft.Azure.Diagnostics**, em seguida, para o campo **xmlCfg** adequado. Os dados são base64 codificado pelo que terá [codificá-lo](http://www.bing.com/search?q=base64+decoder) ver o XML que foi carregado por diagnóstico.<p>
-107|A fase de diretório de recursos para o agente de monitorização não é válida.<p><p>Este é um erro interno que deverá ocorrer apenas se o agente de monitorização é manualmente invocar, incorretamente, a VM.</p>
-108    |Não é possível converter o ficheiro de configuração de diagnóstico para o ficheiro de configuração do agente monitorização.<p><p>Este é um erro interno que deverá ocorrer apenas se o plug-in de diagnóstico manualmente é chamado com um ficheiro de configuração inválida.
-110|Erro de configuração de diagnósticos geral.<p><p>Este é um erro interno que deverá ocorrer apenas se o plug-in de diagnóstico manualmente é chamado com um ficheiro de configuração inválida.
-111|Não é possível iniciar o agente de monitorização.<p><p>Solução: Verifique que estão disponíveis suficientes recursos de sistema.
-112|Erro geral


## <a name="diagnostics-data-is-not-logged-to-azure-storage"></a>Dados diagnóstico não são tiver sessão iniciada armazenamento do Windows Azure
Diagnósticos do Azure armazenam todos os dados no armazenamento do Windows Azure.

A causa mais comuns do evento dados em falta é informações da conta de armazenamento incorretamente definida.

Solução: Corrija o ficheiro de configuração de diagnóstico e voltar a instalar diagnósticos.
Se o problema persistir após a instalação novamente a extensão de diagnóstico, em seguida, poderá ter de depurar mais ao consultar os monitorização agente erros. Antes de são carregados dados eventos à sua conta de armazenamento é armazenado na LocalResourceDirectory.

Para a função de serviço de nuvem a LocalResourceDirectory é:

    C:\Resources\Directory\<CloudServiceDeploymentID>.<RoleName>.DiagnosticStore\WAD<DiagnosticsMajorandMinorVersion>\Tables

Para máquinas virtuais a LocalResourceDirectory é:

    C:\WindowsAzure\Logs\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>\WAD<DiagnosticsMajorandMinorVersion>\Tables

Se não existem ficheiros na pasta LocalResourceDirectory, o agente de monitorização não consegue iniciar. Normalmente, isto é causado por um ficheiro de configuração inválida, um evento que deve ser dadas as CommandExecution.log.

Se o agente de monitorização com êxito está a recolher dados de evento irá ver ficheiros .tsf para cada evento definidos no ficheiro de configuração. Agente de monitorização regista as suas erros no ficheiro MaEventTable.tsf. Para verificar se o conteúdo deste ficheiro pode utilizar a aplicação tabel2csv para converter o ficheiro de .tsf para um ficheiro de values(.csv) separados por vírgulas:

Numa função de serviço de nuvem:

    %SystemDrive%\Packages\Plugins\Microsoft.Azure.Diagnostics.PaaSDiagnostics\<DiagnosticsVersion>\Monitor\x64\table2csv maeventtable.tsf

*% SystemDrive %* numa função de serviço de nuvem é normalmente d:.

Num computador Virtual:

    C:\Packages\Plugins\Microsoft.Azure.Diagnostics.IaaSDiagnostics\<DiagnosticsVersion>\Monitor\x64\table2csv maeventtable.tsf

Os comandos acima gera o registo ficheiro *maeventtable.csv*, que pode abrir e inspecionar para mensagens de falha.    


## <a name="diagnostics-data-tables-not-found"></a>Dados de diagnóstico tabelas não encontrados
As tabelas no Azure armazenamento exploração diagnósticos Azure dados são com o nome utilizando o código abaixo:

        if (String.IsNullOrEmpty(eventDestination)) {
            if (e == "DefaultEvents")
                tableName = "WADDefault" + MD5(provider);
            else
                tableName = "WADEvent" + MD5(provider) + eventId;
        }
        else
            tableName = "WAD" + eventDestination;

Eis um exemplo:

        <EtwEventSourceProviderConfiguration provider=”prov1”>
          <Event id=”1” />
          <Event id=”2” eventDestination=”dest1” />
          <DefaultEvents />
        </EtwEventSourceProviderConfiguration>
        <EtwEventSourceProviderConfiguration provider=”prov2”>
          <DefaultEvents eventDestination=”dest2” />
        </EtwEventSourceProviderConfiguration>

Que irá gerar 4 tabelas:

Evento|Nome da tabela
---|---
fornecedor de = "prov1" &lt;id do evento = "1" /&gt;|WADEvent + MD5("prov1") + "1"
fornecedor de = "prov1" &lt;id do evento = eventDestination "2" = "dest1" /&gt;|WADdest1
fornecedor de = "prov1" &lt;DefaultEvents /&gt;|WADDefault+MD5("prov1")
fornecedor de = "prov2" &lt;DefaultEvents eventDestination = "dest2" /&gt;|WADdest2
