<properties
   pageTitle="Resolver problemas de funções de que a começar a falharem | Microsoft Azure"
   description="Aqui estão algumas razões comuns por que motivo uma função de serviço em nuvem poderá falhar para começar. Soluções para estes problemas também são fornecidas."
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
   ms.date="09/02/2016"
   ms.author="v-six" />

# <a name="troubleshoot-cloud-service-roles-that-fail-to-start"></a>Resolver problemas de funções de serviço na nuvem que a começar a falhar

Aqui estão alguns problemas comuns e soluções relacionados com serviços em nuvem Azure funções de que a começar a falharem.

[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="missing-dlls-or-dependencies"></a>DLLs em falta ou dependências

Responder funções e funções que são ciclo entre **a inicializar**, **ocupado**e **Parar a** Estados-membros podem ser causadas por DLLs em falta ou conjuntos.

Podem ser os sintomas mais de DLLs ou assemblagem em falta:

- A instância da função é percorrer **a inicializar**, **ocupado**e **Parar a** Estados-membros.
- A instância de função foi movida para **pronto** mas se navegar para a aplicação web, a página não será apresentada.

Existem vários métodos recomendados para investigar estes problemas.

## <a name="diagnose-missing-dll-issues-in-a-web-role"></a>Diagnosticar problemas DLL em falta numa função de web

Quando navegar para um Web site que é implementado numa web função e o browser apresenta um erro no servidor semelhante ao seguinte, pode indicar que uma DLL está em falta.

![Erro de servidor numa aplicação '/'.](./media/cloud-services-troubleshoot-roles-that-fail-start/ic503388.png)

## <a name="diagnose-issues-by-turning-off-custom-errors"></a>Diagnosticar problemas de desativando a erros personalizados

Informações de erro mais completas podem ser visualizadas configurando a Web. config para a função web definir o modo de erro personalizadas para desativado e Reimplementar o serviço.

Para ver mais completas erros sem utilizar o ambiente de trabalho remoto:

1. Abra a solução no Microsoft Visual Studio.

2. No **Explorador de soluções**, localize o ficheiro da Web. config e abra-o.

3. Na Web. config ficheiro, localize a secção System. Web e adicione a linha seguinte:

    ```xml
    <customErrors mode="Off" />
    ```

4. Guarde o ficheiro.

5. Compactar e implementar novamente o serviço.

Assim que o serviço é novamente implementado, verá uma mensagem de erro com o nome da assemblagem em falta ou DLL.

## <a name="diagnose-issues-by-viewing-the-error-remotely"></a>Diagnosticar problemas, veja o erro remotamente

Pode utilizar o ambiente de trabalho remoto para aceder a função e ver as informações de erro mais completas remotamente. Utilize os passos seguintes para ver os erros utilizando o ambiente de trabalho remoto:

1. Certifique-se de que o Azure SDK 1.3 ou posterior está instalado.

2. Durante a implementação da solução utilizando o Visual Studio, opte por "Configurar ambiente de trabalho ligações remotas...". Para mais informações sobre como configurar a ligação de ambiente de trabalho remoto, consulte o artigo [Utilizar o ambiente de trabalho remoto com o Azure funções](../vs-azure-tools-remote-desktop-roles.md).

3. No portal do Microsoft Azure clássico, assim que a instância mostra um Estado de **pronto**, clique das instâncias de função.

4. Clique no ícone de **Ligar** na área de **Acesso remoto** do Friso.

5. Inicie sessão no computador virtual utilizando as credenciais especificada durante a configuração de ambiente de trabalho remoto.

6. Abra uma janela de comando.

7. Tipo de `IPconfig`.

8. Tenha em atenção o valor endereço IPV4.

9. Abra o Internet Explorer.

10. Escreva o endereço e o nome da aplicação web. Por exemplo, `http://<IPV4 Address>/default.aspx`.

Navegar para o Web site agora irá devolver mais explícitas mensagens de erro:

* Erro de servidor numa aplicação '/'.

* Descrição: Ocorreu uma exceção não processada durante a execução do pedido web atual. Reveja o rastreio da pilha para obter mais informações sobre o erro e onde é originária o código.

* Detalhes de exceção: System.IO.FIleNotFoundException: não é possível carregar ficheiro ou assemblagem ' Microsoft.WindowsAzure.StorageClient, versão = 1.1.0.0, idioma = neutro, TokenDeChavePública = 31bf856ad364e35' ou das suas dependências. O sistema não consegue localizar o ficheiro especificado.

Por exemplo:

![Erro no servidor explícitas numa aplicação '/'](./media/cloud-services-troubleshoot-roles-that-fail-start/ic503389.png)

## <a name="diagnose-issues-by-using-the-compute-emulator"></a>Diagnosticar problemas de utilizando o emulador cluster

Pode utilizar o emulador do Microsoft Azure cluster para diagnosticar e resolução de problemas de em falta dependências e erros da Web. config.

Para obter melhores resultados na utilização deste método de diagnóstico, deve utilizar um computador ou máquina virtual que tem uma instalação de raiz do Windows. Para melhor simular o ambiente do Azure, utilize o Windows Server 2008 R2 x64.

1. Instale a versão autónoma do [Azure SDK](https://azure.microsoft.com/downloads/).

2. No computador do desenvolvimento, construa o projeto de serviço de nuvem.

3. No Explorador do Windows, navegue para a pasta bin\debug do projeto de serviço na nuvem.

4. Copie o ficheiro de pasta e .cscfg .csx para o computador que está a utilizar para depurar os problemas.

5. No computador LIMPARB, abra uma janela de linha de comandos do Azure SDK e escreva `csrun.exe /devstore:start`.

6. Na linha de comandos, escreva `run csrun <path to .csx folder> <path to .cscfg file> /launchBrowser`.

7. Quando a função é iniciado, verá informações detalhadas de erro no Internet Explorer. Também pode utilizar ferramentas de resolução de problemas padrão do Windows para diagnosticar ainda mais o problema.

## <a name="diagnose-issues-by-using-intellitrace"></a>Diagnosticar problemas de utilizando IntelliTrace

Para trabalho e funções web que utilizam o .NET Framework 4, pode utilizar [IntelliTrace](https://msdn.microsoft.com/library/dd264915.aspx), que se encontra disponível no [Microsoft Visual Studio Ultimate](https://www.visualstudio.com/products/visual-studio-ultimate-with-MSDN-vs).

Siga estes passos para implementar o serviço com IntelliTrace ativada:

1. Confirme que o Azure SDK 1.3 ou posterior está instalado.

2. Implemente a solução utilizando o Visual Studio. Durante a implementação, selecione a caixa de verificação **Ativar IntelliTrace para funções de .NET 4** .

3. Assim que a instância é iniciado, abra o **Explorador do servidor**.

4. Expandir a **Azure\\serviços em nuvem** nó e localize a implementação.

5. Expanda a implementação até ver as instâncias de função. Botão direito do rato sobre uma das instâncias.

6. Selecione a **vista IntelliTrace registos**. O **Resumo IntelliTrace** será aberto.

7. Localize a secção exceções do resumo. Se existirem exceções, a secção será identificada **Exceção dados**.

8. Expanda os **Dados de exceção** e procurar erros **System.IO.FileNotFoundException** semelhantes ao seguinte:

![Os dados de excepção, ficheiro em falta ou assemblagem](./media/cloud-services-troubleshoot-roles-that-fail-start/ic503390.png)

## <a name="address-missing-dlls-and-assemblies"></a>Em falta DLLs e montagens de endereços

Para resolver erros de assemblagem e DLL em falta, siga estes passos:

1. Abra a solução no Visual Studio.

2. No **Explorador de soluções**, abra a pasta de **referências** .

3. Clique em assemblagem identificada no erro.

4. No painel de **Propriedades** , localize a **propriedade Cópia Local** e defina o valor **Verdadeiro**.

5. Implementar o serviço de nuvem.

Assim que tiver confirmado que foram corrigidos todos os erros, pode implementar o serviço sem consultar a caixa de verificação **Ativar IntelliTrace para funções de .NET 4** .

## <a name="next-steps"></a>Próximos passos

Ver mais [artigos de resolução de problemas](https://azure.microsoft.com/documentation/articles/?tag=top-support-issue&product=cloud-services) para a serviços em nuvem.

Para saber como resolver problemas de função de serviço de nuvem, utilizando dados de diagnósticos do Azure PaaS computador, consulte o artigo [série de blogues do Kevin Williamson](http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).
