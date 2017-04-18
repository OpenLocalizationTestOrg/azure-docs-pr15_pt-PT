<properties
 pageTitle="Submeter tarefas para um pacote de aos cluster no Azure | Microsoft Azure"
 description="Saiba como configurar um computador local para submeter tarefas a um cluster de aos Pack no Azure"
 services="virtual-machines-windows"
 documentationCenter=""
 authors="dlepow"
 manager="timlt"
 editor=""
 tags="azure-resource-manager,azure-service-management,hpc-pack"/>
<tags
ms.service="virtual-machines-windows"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="vm-multiple"
 ms.workload="big-compute"
 ms.date="10/14/2016"
 ms.author="danlep"/>

# <a name="submit-hpc-jobs-from-an-on-premises-computer-to-an-hpc-pack-cluster-deployed-in-azure"></a>Submeter HPC tarefas a partir de um computador no local a um cluster de pacote aos implementado no Azure

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]

Configure um computador do cliente no local para submeter tarefas a um cluster de [Pacote de do Microsoft](https://technet.microsoft.com/library/cc514029) Azure. Este artigo mostra-lhe como configurar o num computador local com ferramentas de cliente para submeter tarefa através de HTTPS ao cluster no Azure. Desta forma, vários utilizadores de cluster podem submeter tarefas a um cluster de pacote aos baseado na nuvem, mas sem ligar diretamente para o nó cabeça VM ou aceder a uma subscrição do Azure.

![Submeter uma tarefa para um cluster no Azure][jobsubmit]

## <a name="prerequisites"></a>Pré-requisitos

* **Nó de cabeça aos Pack implementado numa VM Azure** - Recomendamos que utilize ferramentas automatizadas, como um [modelo de guia de introdução Azure](https://azure.microsoft.com/documentation/templates/) ou um [script PowerShell do Azure](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md) para implementar o nó cabeça e cluster. Precisa do nome de DNS do nó cabeça e as credenciais de administrador um cluster de concluir os passos neste artigo.

* **Computador cliente** - precisa de um computador cliente Windows ou Windows Server, que pode executar pacote aos utilitários de cliente (consulte o artigo [requisitos de sistema](https://technet.microsoft.com/library/dn535781.aspx)). Se apenas pretender utilizar o portal do pacote aos web ou REST API para submeter tarefas, pode utilizar qualquer computador cliente da sua escolha.

* **Suporte de instalação do pacote de aos** - para instalar os utilitários de cliente aos Pack, o pacote de instalação gratuita para a versão mais recente do pacote de aos (aos Pack 2012 R2) está disponível a partir do [Centro de transferências da Microsoft](http://go.microsoft.com/fwlink/?LinkId=328024). Certifique-se de que a mesma versão do pacote de aos está instalado o nó cabeça VM transferir.

## <a name="step-1-install-and-configure-the-web-components-on-the-head-node"></a>Passo 1: Instalar e configurar os componentes web no nó cabeça

Para permitir que uma interface REST submeter tarefas para cluster por HTTPS, certifique-se de que os componentes web do pacote aos estão configurados no nó cabeça aos Pack. Se já não são instalados, instale primeiro os componentes web ao executar o ficheiro de instalação HpcWebComponents.msi. Em seguida, configure os componentes ao executar o script PowerShell aos **HPCWebComponents.ps1 conjunto**.

Para obter procedimentos detalhados, consulte o artigo [instalar os componentes Web do Microsoft aos Pack](http://technet.microsoft.com/library/hh314627.aspx).

>[AZURE.TIP] Alguns modelos do Azure guia de introdução para o pacote de de instalar e configurar os componentes web do automaticamente. Se utilizar o [script de implementação aos Pack IaaS](virtual-machines-windows-classic-hpcpack-cluster-powershell-script.md) para criar o cluster, opcionalmente, pode instalar e configurar os componentes web do como parte da implementação.

**Para instalar os componentes web**

1. Ligar para o nó cabeça VM com as credenciais de administrador um cluster.

2. A partir da pasta de configuração do pacote aos, execute a HpcWebComponents.msi no nó cabeça.

3. Siga os passos no Assistente para instalar os componentes web

**Para configurar os componentes web**

1. No nó cabeça, comece aos PowerShell como administrador.

2. Para alterar o diretório para a localização do script de configuração, escreva o seguinte comando:

    ```powershell
    cd $env:CCP_HOME\bin
    ```
3. Para configurar a interface de descanso e iniciar o serviço Web de aos, escreva o seguinte comando:

    ```powershell
    .\Set-HPCWebComponents.ps1 –Service REST –enable
    ```

4. Quando lhe for pedido para selecionar um certificado, selecione o certificado que corresponde ao nome do nó cabeça DNS público. Por exemplo, se implementar o nó cabeça VM utilizando o modelo clássico de implementação, o nome do certificado aspeto CN =&lt;*HeadNodeDnsName*&gt;. cloudapp.net. Se utilizar o modelo de implementação do Gestor de recursos, o nome do certificado aspeto CN =&lt;*HeadNodeDnsName*&gt;. &lt; *região*&gt;. cloudapp.azure.com.

    >[AZURE.NOTE] Selecione este certificado mais tarde quando submeter as tarefas para o nó cabeça a partir de um computador no local. Não selecione ou configurar um certificado que corresponde ao nome do computador do nó cabeça no domínio do Active Directory (por exemplo, CN =*MyHPCHeadNode.HpcAzure.local*).

5. Para configurar o portal de web para submissão de tarefa, escreva o seguinte comando:

    ```powershell
    .\Set-HPCWebComponents.ps1 –Service Portal -enable
    ```
6. Depois de concluído o script, parar e reinicie o serviço de Programador de tarefas ao escrever os seguintes comandos:

    ```powershell
    net stop hpcscheduler
    net start hpcscheduler
    ```

## <a name="step-2-install-the-hpc-pack-client-utilities-on-an-on-premises-computer"></a>Passo 2: Instalar os utilitários de cliente aos Pack num computador no local

Se pretender instalar os utilitários de cliente aos Pack no seu computador, transferir os ficheiros de configuração do pacote aos (instalação completa) a partir do [Centro de transferências da Microsoft](http://go.microsoft.com/fwlink/?LinkId=328024). Quando iniciar a instalação, selecione a opção de configuração para o **pacote de aos utilitários de cliente**.

Para utilizar as ferramentas de cliente aos Pack submeter tarefas para o nó cabeça VM, também precisa de exportar um certificado do nó cabeça de e instalá-lo no computador cliente. O certificado tem de estar no. Formato de CER.

**Para exportar o certificado do nó cabeça**

1. No nó cabeça, adicione snap-in certificados para uma consola de gestão da Microsoft para a conta de computador Local. Para obter os passos adicionar o snap-in, consulte o artigo [Adicionar Snap-in Certificados a uma MMC](https://technet.microsoft.com/library/cc754431.aspx).

2. Na árvore de consola, expanda **certificados – Computador Local** > **pessoal**e, em seguida, clique em **certificados**.

3. Localize o certificado que configurou para os componentes web do pacote no [passo 1: instalar e configurar os componentes web no nó cabeça](#step-1:-install-and-configure-the-web-components-on-the-head-node) (por exemplo, CN =&lt;*HeadNodeDnsName*&gt;. cloudapp.net).

4. Botão direito do rato no certificado e clique em **Todas as tarefas** > **Exportar**.

5. No Assistente de exportação de certificado, clique em **seguinte**e certifique-se de que **não exportar a chave privada** está selecionada.

6. Siga os restantes passos do Assistente para exportar o certificado X509 codificado x. 509 binário (. Formato de CER).


**Para importar o certificado no computador cliente**


1. Copie o certificado que exportou a partir do nó de cabeça para uma pasta no computador cliente.

2. No computador cliente, execute certmgr.

3. No Gestor de certificado, expanda **Certificados – utilizador actual** > **Fidedignos autoridades de certificação de raiz**, com o botão direito **certificados**e, em seguida, clique em **Todas as tarefas** > **Importar**.

4. No Assistente de importação de certificado, clique em **seguinte** e siga os passos para importar o certificado que exportou a partir do nó de cabeça para o arquivo de autoridades de certificação de raiz fidedigna.



>[AZURE.TIP] Poderá ver um aviso de segurança, porque a autoridade de certificação no nó cabeça não é reconhecida pelo computador cliente. Para fins de teste, pode ignorar este aviso e concluir a importação de certificado.

## <a name="step-3-run-test-jobs-on-the-cluster"></a>Passo 3: Executar teste tarefas no cluster

Para verificar a sua configuração, tente executar tarefas num cluster no Azure do computador no local. Por exemplo, pode utilizar ferramentas de interface gráfica de pacote aos ou linha de comandos para submeter tarefas ao cluster. Também pode utilizar um portal de baseada na web para submeter tarefas.


**Para executar comandos de submissão da tarefa no computador cliente**


1. Num computador cliente onde os utilitários de cliente aos Pack estão instalados, inicie uma linha de comandos.

2. Comando escreva uma amostra. Por exemplo, para listar todas as tarefas no cluster, escreva um comando semelhante a um dos seguintes procedimentos, dependendo o nome completo do DNS do nó cabeça:

    ```command
    job list /scheduler:https://<HeadNodeDnsName>.cloudapp.net /all
    ```
    
    ou
    
    ```command
    job list /scheduler:https://<HeadNodeDnsName>.<region>.cloudapp.azure.com /all
    ```

    >[AZURE.TIP] Utilize o nome completo do DNS do nó cabeça, não é o endereço IP, no URL programador. Se especificar o endereço IP, aparece um erro semelhante a "o certificado do servidor precisa para significa tem uma cadeia de fidedignidade válida, ou seja colocado na loja raiz confiável."

3. Quando lhe for pedido, escreva o nome de utilizador (no formulário de &lt;NomeDomínio&gt;\\&lt;nome de utilizador&gt;) e palavra-passe de administrador de cluster de HPC ou outro utilizador cluster que configurou. Pode escolher armazenar as credenciais localmente para obter mais operações de tarefa.

    É apresentada uma lista de tarefas.


**Utilizar o Gestor de tarefa de HPC no computador cliente**

1. Se não tiver anteriormente armazenar credenciais de domínio para um utilizador de cluster ao submeter uma tarefa, pode adicionar as credenciais no Gestor de credenciais.

    um. No painel de controlo no computador cliente, inicie o Gestor de credenciais.

    b. Clique em **Credenciais do Windows** > **Adicionar credenciais genérica**.

    c. Especificar o endereço da Internet (por exemplo, https://&lt;HeadNodeDnsName&gt;.cloudapp.net/HpcScheduler ou https://&lt;HeadNodeDnsName&gt;.&lt; região&gt;.cloudapp.azure.com/HpcScheduler) e o nome de utilizador (&lt;NomeDomínio&gt;\\&lt;nome de utilizador&gt;) e palavra-passe de administrador de cluster ou de outro utilizador cluster que configurou.

2. No computador cliente, inicie o Gestor de tarefa HPC.

3. Na caixa de diálogo **Selecione nó de cabeça** , escreva o URL para o nó cabeça no Azure (por exemplo, https://&lt;HeadNodeDnsName&gt;. cloudapp.net ou https://&lt;HeadNodeDnsName&gt;.&lt; região&gt;. cloudapp.azure.com).

    Gestor de tarefa HPC abre e apresenta uma lista de tarefas no nó cabeça.

**Utilizar o portal de web em execução no nó cabeça**

1. Iniciar um browser no computador cliente e introduza um dos seguintes endereços, consoante o nome completo do DNS do nó cabeça:

    ```
    https://<HeadNodeDnsName>.cloudapp.net/HpcPortal
    ```
    
    ou
    
    ```
    https://<HeadNodeDnsName>.<region>.cloudapp.azure.com/HpcPortal
    ```
2. Na caixa de diálogo segurança que aparece, escreva as credenciais de domínio do administrador de cluster HPC. (Também pode adicionar outros utilizadores de cluster no diferentes funções. Consulte o artigo [Gerir os utilizadores de Cluster](https://technet.microsoft.com/library/ff919335.aspx).)

    O portal de web abre-se para a vista de lista de tarefa.

3. Para submeter uma tarefa de exemplo que devolve a cadeia "Olá mundo" da cluster, clique em **nova tarefa** na navegação à esquerda.

4. Na página **Nova tarefa** , em **partir de páginas de submissão**, clique em **HelloWorld**. É apresentada a página de submissão da tarefa.

5. Clique em **Submeter**. Se lhe for pedido, fornece as credenciais de domínio do administrador de cluster HPC. A tarefa é submetida e o ID da tarefa de aparecer na página **Minhas tarefas** .

6. Para ver os resultados da tarefa que que utilizou ao submeter, clique no ID da tarefa e, em seguida, clique em **Vista de tarefas** para ver os resultados de comando (em **saída**).

## <a name="next-steps"></a>Próximos passos

* Também pode submeter a cluster Azure com a [Aos Pack REST API](http://social.technet.microsoft.com/wiki/contents/articles/7737.creating-and-submitting-jobs-by-using-the-rest-api-in-microsoft-hpc-pack-windows-hpc-server.aspx)de tarefas.

* Se pretende submeter cluster de tarefas a partir de um cliente Linux, consulte o artigo da amostra Python na [aos Pack 2012 R2 SDK e o código de exemplo](https://www.microsoft.com/download/details.aspx?id=41633).


<!--Image references-->
[jobsubmit]: ./media/virtual-machines-windows-hpcpack-cluster-submit-jobs/jobsubmit.png
