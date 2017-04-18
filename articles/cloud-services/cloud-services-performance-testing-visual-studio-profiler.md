<properties 
    pageTitle="Criação de perfis de um serviço na nuvem localmente no emulador de cluster | Microsoft Azure" 
    services="cloud-services"
    description="Investigar problemas de desempenho em serviços em nuvem com o Gestor de perfis do Visual Studio" 
    documentationCenter=""
    authors="TomArcher" 
    manager="douge" 
    editor=""
    tags="" 
    />

<tags 
    ms.service="cloud-services" 
    ms.workload="na" 
    ms.tgt_pltfrm="na" 
    ms.devlang="multiple" 
    ms.topic="article" 
    ms.date="07/30/2016" 
    ms.author="tarcher"/>

# <a name="testing-the-performance-of-a-cloud-service-locally-in-the-azure-compute-emulator-using-the-visual-studio-profiler"></a>Testar o desempenho de um serviço na nuvem localmente no emulador de cluster Azure utilizando o Gestor de perfis do Visual Studio

Uma variedade de ferramentas e técnicas estão disponíveis para testar o desempenho dos serviços em nuvem.
Durante a publicação de um serviço na nuvem para Azure, pode ter Visual Studio recolher dados de perfis e, em seguida, analisá-los a localmente, como descrito na [criação de perfis de uma aplicação do Azure][1].
Pode também utilizar diagnósticos para controlar uma variedade de contadores de desempenho, conforme descrito em [utilizar contadores de desempenho no Azure][2].
Também poderá querer criar um perfil a aplicação localmente no emulador de cluster antes de implementá-lo na nuvem.

Este artigo abrange o método de CPU amostragem de criação de perfis, que pode ser feito localmente o emulador. Recolha de CPU é um método de criação de perfis que não é muito intrusivo. Um intervalo de amostragem designada, o Gestor de perfis tira um instantâneo da pilha de chamada. Os dados são recolhidos durante um período de tempo e apresentados num relatório. Este método de criação de perfis tem tendência para indicar onde numa aplicação inviabiliza intensa a maior parte do trabalho CPU está a ser feita.  Isto dá-lhe a oportunidade de concentrar-se no "caminho quente" onde está a gastar mais tempo a aplicação.



## <a name="1-configure-visual-studio-for-profiling"></a>1: configurar o Visual Studio para criação de perfis

Em primeiro lugar, existem algumas opções de configuração de Visual Studio que poderão ser útil quando perfis. Para fazer sentido dos relatórios perfis, terá de símbolos (ficheiros. pdb) para a aplicação e também os símbolos de bibliotecas do sistema. Irá querer para se certificar de a referência dos servidores de símbolo disponíveis. Para efetuar esta ação, no menu **Ferramentas** no Visual Studio, selecione **Opções**e, em seguida, selecione a **depuração**, em seguida, **símbolos**. Certifique-se de que os servidores de símbolo de Microsoft está listado em **localizações do ficheiro (. pdb) símbolo**.  Também pode fazer referência http://referencesource.microsoft.com/symbols, que poderá ter de ficheiros de símbolos adicionais.

![Opções de símbolo][4]

Se pretender, pode simplificar os relatórios de que o Gestor de perfis gera definindo apenas meus código. Com apenas meus código ativado, pilhas de chamada de função são simplificadas para que as chamadas totalmente internas para bibliotecas e o .NET Framework estão ocultos dos relatórios. No menu **Ferramentas** , selecione **Opções**. Em seguida, expanda o nó de **Ferramentas de desempenho** e selecione **Geral**. Selecione a caixa de verificação para **Ativar apenas meus código para relatórios de gerador de perfis**.

![Apenas as opções do meu código][17]

Pode utilizar estas instruções com um projeto existente ou com um novo projeto.  Se criar um novo projeto para experimentar as técnicas descritas abaixo, escolha um projecto de c# **Serviço em nuvem Azure** e selecione uma **Função de Web** e uma **Função de trabalho**.

![Funções de projeto do Azure serviço na nuvem][5]

Por exemplo efeitos, adicione algum código para o projeto que retira muito do tempo e demonstra alguns problema de desempenho óbvios. Por exemplo, adicione o código seguinte a um projeto de função de trabalho:

    public class Concatenator
    {
        public static string Concatenate(int number)
        {
            int count;
            string s = "";
            for (count = 0; count < number; count++)
            {
                s += "\n" + count.ToString();
            }
            return s;
        }
    }

Chamar este código a partir do método de RunAsync aula de derivado de RoleEntryPoint a função de trabalho. (Ignorar o aviso sobre o método a executar o modo síncrono).

        private async Task RunAsync(CancellationToken cancellationToken)
        {
            // TODO: Replace the following with your own logic.
            while (!cancellationToken.IsCancellationRequested)
            {
                Trace.TraceInformation("Working");
                Concatenator.Concatenate(10000);
            }
        }

Criar e executar o serviço de nuvem localmente sem depuração (Ctrl + F5), com a configuração da solução definida para **edição**. Isto garante que todos os ficheiros e pastas são criadas para executar a aplicação localmente e assegura que todas as emuladores são iniciados. Inicie a IU de emulador calcular a partir da barra de tarefas para verificar que a sua função de trabalho está em execução.

## <a name="2-attach-to-a-process"></a>2: anexar a um processo

Em vez de criação de perfis a aplicação por iniciá-lo a partir do IDE do Visual Studio 2010, tem de anexar o Gestor de perfis um processo em execução. 

Para anexar o Gestor de perfis a um processo, no menu **Analisar** , escolha **gerador de perfis** e **Anexar/desanexar**.

![Anexar a opção de perfil][6]

Para uma função de trabalho, localize o processo de WaWorkerHost.exe.

![Processo de WaWorkerHost][7]

Se a sua pasta de projeto está numa unidade de rede, o Gestor de perfis irá pedir para outra localização para guardar os relatórios de perfis de fornecer.

 Também pode anexar a uma função de web ao anexar a WaIISHost.exe.
Se existirem vários processos de função de trabalho na sua aplicação, terá de utilizar o ID de processo para distingui-los. Pode consultar o ID de processo através de programação acedendo o objeto do processo. Por exemplo, se adicionar este código para o método de executar a classe derivado de RoleEntryPoint numa função de, pode procurar na IU do emulador calcular saber quais os processos para ligar de início de sessão.

    var process = System.Diagnostics.Process.GetCurrentProcess();
    var message = String.Format("Process ID: {0}", process.Id);
    Trace.WriteLine(message, "Information");

Para ver o registo, comece a IU de emulador calcular.

![Iniciar o emulador cluster IU][8]

Abra a janela de consola do trabalho função registo na IU do emulador calcular ao clicar na barra de título da janela da consola. Pode ver o ID do processo no registo de.

![ID do processo de vista][9]

Um que anexou, execute os passos na IU sua aplicação (se necessário) para reproduzir o cenário.

Quando pretende parar a criação de perfis, selecione a ligação **Parar a criação de perfis** .

![Parar a opção de criação de perfis][10]

## <a name="3-view-performance-reports"></a>3: ver relatórios de desempenho

O relatório de desempenho para a aplicação é apresentado.

Neste momento, o Gestor de perfis deixa de executar, guarda os dados num ficheiro .vsp e apresenta um relatório que mostra uma análise de dados.

![Relatório de gerador de perfis][11]


Se vir String.wstrcpy no caminho quente, clique no meu código de para alterar a vista para mostrar apenas código do utilizador.  Se vir String.Concat, experimente premir o botão Mostrar todo o código.

Deverá ver o método concatenar e String.Concat ocupar uma grande parte do tempo de execução.

![Análise de relatório][12]

Se tiver adicionado o código de concatenação de cadeia neste artigo, deverá ver um aviso na lista de tarefas para esta situação. Também poderá ver um aviso de que existe um montante em excesso da coleção de lixo, o que é devido o número de cadeias que são criados e eliminados.

![Avisos de desempenho][14]

## <a name="4-make-changes-and-compare-performance"></a>4: Efetue alterações e comparar o desempenho

Pode também comparar o desempenho antes e depois de uma alteração de código.  Parar o processo em execução e editar o código para substituir a operação de concatenação de cadeia com a utilização de StringBuilder:

    public static string Concatenate(int number)
    {
        int count;
        System.Text.StringBuilder builder = new System.Text.StringBuilder("");
        for (count = 0; count < number; count++)
        {
             builder.Append("\n" + count.ToString());
        }
        return builder.ToString();
    }

Faça outra de desempenho de executar e, em seguida, comparar o desempenho. No Explorador do desempenho, se o será executado estiverem na mesma sessão, pode basta selecionar ambos os relatórios, abra o menu de atalho e selecione **Comparar relatórios de desempenho**. Se pretender comparar com um use noutra sessão de desempenho, abra o menu **Analisar** e selecione **Comparar relatórios de desempenho**. Especifique ambos os ficheiros na caixa de diálogo que aparece.

![Comparar a opção de relatórios de desempenho][15]

Os relatórios realçar as diferenças entre as duas execuções.

![Relatório de comparação][16]

Parabéns! Tenha obteve começou com o Gestor de perfis.

## <a name="troubleshooting"></a>Resolução de problemas

- Certifique-se de que é uma compilação de lançamento de criação de perfis e iniciar o sem depuração.

- Se a opção de anexar/desanexar não estiver ativada no menu gerador de perfis, execute o Assistente de desempenho.

- Utilize a IU de emulador calcular para ver o estado da sua aplicação. 

- Se ocorrerem problemas de início de aplicações no emulador ou anexar o Gestor de perfis, encerrar para baixo o emulador cluster e reinicie-o. Se isso não resolver o problema, experimente reiniciar o computador. Este problema pode ocorrer se utilizar o emulador calcular para suspender e remover implementações em execução.

- Se tiver utilizado qualquer um dos comandos perfis da linha de comandos, especialmente as definições globais, certifique-se de que tenha sido chamado VSPerfClrEnv /globaloff e que foi encerrar VsPerfMon.exe.

- Se quando amostragem, verá a mensagem "PRF0025: não foram recolhidos dados," Verifique se o processo de anexados a tem atividade CPU. As aplicações que não estão a fazer qualquer trabalho utilizaria não poderão produzir quaisquer dados amostragem.  Também é possível que o processo de saído antes de qualquer amostragem sido concluída. Verifique que o método de executar para uma função que estão a perfis não terminar.

## <a name="next-steps"></a>Próximos passos

Instrumentalizar binários Azure no emulador não é suportada no Gestor de perfis do Visual Studio, mas se pretende testar alocação de memória, pode escolher essa opção quando perfis. Também pode escolher simultaneidade criação de perfis, que ajuda a determinar se threads são perder tempo competir para bloqueios ou camada interação de criação de perfis, que o ajuda a encontrar problemas de desempenho quando interagir entre camadas de uma aplicação, com mais frequência entre a camada de dados e uma função de trabalho.  Pode ver as consultas de base de dados que gera da sua aplicação e utilização de dados de perfis para melhorar a utilização da base de dados. Para obter informações sobre a criação de interação perfis de camadas, consulte a mensagem do blogue [instruções passo a passo: utilizar o Gestor de perfis de interação camadas no Visual Studio 2010http de sistema de equipa][3].



[1]: http://msdn.microsoft.com/library/azure/hh369930.aspx
[2]: http://msdn.microsoft.com/library/azure/hh411542.aspx
[3]: http://blogs.msdn.com/b/habibh/archive/2009/06/30/walkthrough-using-the-tier-interaction-profiler-in-visual-studio-team-system-2010.aspx
[4]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally09.png
[5]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally10.png
[6]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally02.png
[7]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally05.png
[8]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally010.png
[9]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally07.png
[10]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally06.png
[11]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally03.png
[12]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally011.png
[14]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally04.png 
[15]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally013.png
[16]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally012.png
[17]: ./media/cloud-services-performance-testing-visual-studio-profiler/ProfilingLocally08.png
 