<properties
    pageTitle="Solução VMware monitorização no registo de análise | Microsoft Azure"
    description="Saiba mais sobre como a solução VMware monitorização pode ajudar a gerir os registos e monitorizar ESXi anfitriões."
    services="log-analytics"
    documentationCenter=""
    authors="bandersmsft"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/28/2016"
    ms.author="banders"/>

# <a name="vmware-monitoring-preview-solution-in-log-analytics"></a>Solução VMware monitorização (pré-visualização) no registo de análise

A solução VMware monitorização na análise de registo é uma solução que o ajuda a criar um registo centralizado e monitorização abordagem para registos de VMware grandes. Este artigo descreve como pode resolver problemas, capturar e gerir os anfitriões ESXi numa única localização utilizando a solução. Com a solução, pode ver dados detalhados para todos os seus anfitriões ESXi num único local. Pode ver tendências de anfitriões VM e ESXi fornecidos através de registos do anfitrião do ESXi, estado e contagens de evento superior. Pode resolver problemas ao visualizar e procurar registos de anfitrião ESXi centralizados. E, pode criar alertas com base em consultas de pesquisa de registo.

A solução utiliza a funcionalidade de nativo do sistema do anfitrião da ESXi ao push dados para uma VM, que possui OMS agente de destino. No entanto, a solução não escrever ficheiros do sistema dentro de destino VM. O agente OMS abre a porta 1514 e recebe a esta. Assim que recebe os dados, o agente OMS emite os dados para OMS.

## <a name="installing-and-configuring-the-solution"></a>Instalar e configurar a solução

Utilize as seguintes informações para instalar e configurar a solução.

- Adicione a solução VMware monitorização à área de trabalho OMS utilizando o processo descrito na [soluções de adicionar o registo de análise a partir da Galeria de soluções](log-analytics-add-solutions.md).

#### <a name="supported-vmware-esxi-hosts"></a>Anfitriões de VMware ESXi suportados
vSphere ESXi anfitrião 5.5 e 6.0

#### <a name="prepare-a-linux-server"></a>Preparar um servidor de Linux
Crie um sistema operativo do Linux VM para receber todos os dados do sistema de anfitriões ESXi. O [OMS Linux agente](log-analytics-linux-agents.md) é o ponto de coleções de sites para todos os dados do sistema de anfitrião ESXi. Pode utilizar vários anfitriões ESXi para reencaminhar os registos para um único servidor Linux, tal como no exemplo seguinte.  

   ![fluxo do sistema](./media/log-analytics-vmware/diagram.png)

### <a name="configure-syslog-collection"></a>Configurar a recolha do sistema

1. Configure o reencaminhamento de chamadas do sistema para VSphere. Para obter informações detalhadas para o ajudar a configurar o reencaminhamento de chamadas do sistema, consulte o artigo [configuração do sistema no ESXi 5. x e 6.0 (2003322)](https://kb.vmware.com/selfservice/microsites/search.do?language=en_US&cmd=displayKC&externalId=2003322). Aceda a **configuração do anfitrião ESXi** > **Software** > **Definições avançadas** > **Syslog**.
  ![vsphereconfig](./media/log-analytics-vmware/vsphere1.png)  

2. No campo *Syslog.global.logHost* , adicione o seu servidor de Linux e o número de porta *1514*. Por exemplo, `tcp://hostname:1514` ou`tcp://123.456.789.101:1514`

3. Abra a firewall do anfitrião ESXi do sistema. **Configuração do anfitrião ESXi** > **Software** > **Perfil de segurança** > **Firewall** e abrir as **Propriedades**.  

    ![vspherefw](./media/log-analytics-vmware/vsphere2.png)  

    ![vspherefwproperties](./media/log-analytics-vmware/vsphere3.png)  

4. Verifique o vSphere consola para verificar que esse syslog está corretamente configurado. Confirmar no anfitrião do ESXI essa porta **1514** está configurado.

5. Testar a ligação entre o servidor de Linux e anfitrião do ESXi utilizando o `nc` comando no anfitrião do ESXi. Por exemplo:

    ```
    [root@ESXiHost:~] nc -z 123.456.789.101 1514
    Connection to 123.456.789.101 1514 port [tcp/*] succeeded!
    ```

6. Transferir e instalar o agente OMS para Linux no servidor Linux. Para mais informações, consulte a [documentação do agente de OMS para Linux](https://github.com/Microsoft/OMS-Agent-for-Linux).

7. Após a instalação do agente do OMS para Linux, vá para o directório /etc/opt/microsoft/omsagent/sysconf/omsagent.d e copie o ficheiro de vmware_esxi.conf ao directório /etc/opt/microsoft/omsagent/conf/omsagent.d e alterar o proprietário/grupo e as permissões do ficheiro. Por exemplo:

    ```
    sudo cp /etc/opt/microsoft/omsagent/sysconf/omsagent.d/vmware_esxi.conf /etc/opt/microsoft/omsagent/conf/omsagent.d
sudo chown omsagent:omiusers /etc/opt/microsoft/omsagent/conf/omsagent.d/vmware_esxi.conf
    ```

8.  Reinicie o agente OMS para Linux executando `sudo /opt/microsoft/omsagent/bin/service_control restart`.

9. No Portal do OMS, pesquise registo `Type=VMware_CL`. Quando OMS recolhe os dados do sistema, mantém o formato do sistema. No portal do, são capturados alguns campos específicos, como o *nome do anfitrião* e *NomeProcesso*.  

    ![tipo](./media/log-analytics-vmware/type.png)  

    Se os resultados da pesquisa do registo de vista são semelhantes na imagem acima, estiver pronto para utilizar o dashboard de solução OMS VMware monitorização.  

## <a name="vmware-data-collection-details"></a>VMware detalhes da coleção de dados

A solução VMware monitorização recolhe várias métricas e registo de dados de desempenho ESXi anfitriões com os agentes OMS para Linux que ativou.

A tabela seguinte mostra os métodos de recolha de dados e outros detalhes sobre como os dados recolhidos.

| plataforma | Agente OMS para Linux | Agente SCOM | Armazenamento Azure | SCOM necessário? | Dados de agente SCOM enviados por grupo de gestão | frequência de coleções de sites |
|---|---|---|---|---|---|---|
|Linux|![Sim](./media/log-analytics-vmware/oms-bullet-green.png)|![N](./media/log-analytics-vmware/oms-bullet-red.png)|![N](./media/log-analytics-vmware/oms-bullet-red.png)|            ![N](./media/log-analytics-containers/oms-bullet-red.png)|![N](./media/log-analytics-vmware/oms-bullet-red.png)| cada 3 minutos|


A tabela que se segue mostram exemplos de campos de dados recolhidos pela solução VMware monitorização:

| nome do campo | Descrição |
| --- | --- |
| Device_s| Dispositivos de armazenamento VMware |
| ESXIFailure_s | tipos de falha |
| EventTime_t | hora em que ocorreu quando de evento |
| HostName_s | Nome do anfitrião ESXi |
| Operation_s | Criar VM ou eliminar VM |
| ProcessName_s | nome do evento |
| ResourceId_s | nome do anfitrião VMware |
| ResourceLocation_s | VMware |
| ResourceName_s | VMware |
| ResourceType_s | Hyper-V |
| SCSIStatus_s | Estado de VMware SCSI |
| SyslogMessage_s | Dados do sistema |
| UserName_s | utilizador que criou ou eliminados VM |
| VMName_s | Nome VM |
| Computador | computador anfitrião |
| TimeGenerated | foram gerados os dados de tempo |
| DataCenter_s | VMware Centro de dados |
| StorageLatency_s | Latência de armazenamento (ms) |

## <a name="vmware-monitoring-solution-overview"></a>Descrição geral da solução VMware de monitorização

O mosaico de VMware aparece no portal do OMS. Fornece uma vista de alto nível de quaisquer falhas. Quando clica no mosaico, vá para a vista de dashboard.

![dispor em mosaico](./media/log-analytics-vmware/tile.png)

#### <a name="navigate-the-dashboard-view"></a>Navegar na vista de dashboard

Na vista de dashboard **VMware** , pás estão organizados por:

- Contagem de estado de falhas
- Conta do anfitrião superior ao evento
- Contagens de evento superior
- Atividades de máquina virtual
- Eventos de disco ESXi anfitrião


![solution1](./media/log-analytics-vmware/solutionview1-1.png)

![solution2](./media/log-analytics-vmware/solutionview1-2.png)

Clique em qualquer pá para abrir o painel de pesquisa de análise de registo que mostra informações detalhadas específicas para o pá.

A partir daqui, pode editar a consulta de pesquisa para modificá-lo para algo específico. Para obter um tutorial sobre os princípios básicos da pesquisa OMS, consulte o artigo o [tutorial de pesquisa de registo OMS.](log-analytics-log-searches.md)

#### <a name="find-esxi-host-events"></a>Localizar ESXi anfitrião eventos

Um único anfitrião ESXi gera múltiplos registos, com base no seus processos. A solução VMware monitorização centraliza-los e resume as contagens de evento. Esta vista centralizada ajuda-o a compreender que anfitrião ESXi tem um grande volume de eventos e que eventos ocorrerem com mais frequência no seu ambiente.

![evento](./media/log-analytics-vmware/events.png)

Pode agregar mais ao clicar num anfitrião ESXi ou um tipo de evento.

Quando clica num nome de anfitrião ESXi, pode ver informações a partir do anfitrião ESXi. Se pretende limitar resultados com o tipo de evento, adicione `“ProcessName_s=EVENT TYPE”` na sua consulta de pesquisa. Pode selecionar **NomeProcesso** no filtro de pesquisa. Que limita a informação por si.

![o nível de detalhe](./media/log-analytics-vmware/eventhostdrilldown.png)

#### <a name="find-high-vm-activities"></a>Localizar alta VM actividades

Uma máquina virtual podem ser criada e eliminada no qualquer anfitrião ESXi. É útil para que um administrador identificar VMs quantos cria um anfitrião ESXi. No-ativar, ajuda a compreender o desempenho e capacidade de planeamento. Manter o registo de eventos de atividade VM é crucial ao gerir o seu ambiente.

![o nível de detalhe](./media/log-analytics-vmware/vmactivities1.png)

Se pretender ver adicionais ESXi anfitrião VM criação dados, clique num nome de anfitrião ESXi.

![o nível de detalhe](./media/log-analytics-vmware/createvm.png)

#### <a name="common-search-queries"></a>Consultas de pesquisa comuns

A solução inclui outras consultas úteis que o podem ajudar a gerir o seu anfitriões ESXi, como o espaço de armazenamento de alta, latência de armazenamento e falha de caminho.

![consultas](./media/log-analytics-vmware/queries.png)

#### <a name="save-queries"></a>Guardar consultas

Guardar consultas de pesquisa é uma funcionalidade no OMS padrão e pode ajudá-lo a manter quaisquer consultas que ter encontrado úteis. Depois de criar uma consulta que achar útil, guarde-o ao clicar em **Favoritos**. Uma consulta guardada permite-lhe facilmente reutilizá-la mais tarde a partir da página de [Dashboard meus](log-analytics-dashboards.md) onde pode criar os seus próprios dashboards personalizados.

![DockerDashboardView](./media/log-analytics-vmware/dockerdashboardview.png)

#### <a name="create-alerts-from-queries"></a>Criar alertas de consultas

Após ter criado as suas consultas, poderá pretender utilizar as consultas para alertam-no quando ocorrem eventos específicos. Consulte o artigo [alertas no registo Analytics](log-analytics-alerts.md) para obter informações sobre como criar alertas. Para obter exemplos dos alertar consultas e outros exemplos de consulta, consulte a mensagem do blogue [Monitor VMware utilizando a análise de registo OMS](https://blogs.technet.microsoft.com/msoms/2016/06/15/monitor-vmware-using-oms-log-analytics) .

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

### <a name="what-do-i-need-to-do-on-the-esxi-host-setting-what-impact-will-it-have-on-my-current-environment"></a>O que preciso para sobre o ESXi alojar definição? Que impacto vai tê-lo no meu ambiente atual?
A solução utiliza o nativo ESXi anfitrião do sistema mecanismo de reencaminhamento de chamadas. Não tem nenhum software Microsoft adicional no anfitrião do ESXi para capturar os registos. Deverá ter um impacto baixo ao ambiente existente. No entanto, tem de definir o reencaminhamento do sistema, que é a funcionalidade ESXI.

### <a name="do-i-need-to-restart-my-esxi-host"></a>É necessário reiniciar o anfitrião do meu ESXi?
Não. Este processo não requer um reinício. Por vezes, vSphere não corretamente atualiza do sistema. Neste caso, inicie sessão ao anfitrião do ESXi e recarregar do sistema. Novamente, não tem de reiniciar o anfitrião, para que este processo não está desorganização para o seu ambiente.

### <a name="can-i-increase-or-decrease-the-volume-of-log-data-sent-to-oms"></a>Pode aumentar ou diminuir o volume dos dados de registo enviados para OMS?
Sim, pode. Pode utilizar as definições do nível de registo do sistema anfitrião ESXi no vSphere. Colecção de registos baseia o nível de *informações* . Por isso, se pretender VM criação ou eliminação de auditoria, tem de manter o nível de *informações* no Hostd. Para mais informações, consulte o artigo da [Base de dados de conhecimento VMware](https://kb.vmware.com/selfservice/microsites/search.do?&cmd=displayKC&externalId=1017658).

### <a name="why-is-hostd-not-providing-data-to-oms-my-log-setting-is-set-to-info"></a>Por que motivo Hostd não está a fornecer dados a OMS? A definição de registo está definida para informações.
Ocorreu um erro de anfitrião ESXi para o carimbo de hora do sistema. Para mais informações, consulte o artigo da [Base de dados de conhecimento VMware](https://kb.vmware.com/selfservice/microsites/search.do?language=en_US&cmd=displayKC&externalId=2111202). Depois de aplicar a solução, Hostd deve funcionar normalmente.

### <a name="can-i-have-multiple-esxi-hosts-forwarding-syslog-data-to-a-single-vm-with-omsagent"></a>Pode ter vários anfitriões de ESXi reencaminhamento de chamadas do sistema dados para uma única VM com omsagent?
Sim. Pode ter vários anfitriões de ESXi reencaminhamento para uma única VM com omsagent.

### <a name="why-dont-i-see-data-flowing-into-oms"></a>Por que motivo não consigo ver os dados a fluir em OMS?

Podem existir vários motivos:

- Anfitrião do ESXi corretamente não é conduza dados para a VM executar omsagent. Para testar, execute os seguintes passos:
    1. Para confirmar, inicie sessão no anfitrião do ESXi utilizando ssh e execute o seguinte comando:`nc -z ipaddressofVM 1514`

        Se não tiver êxito, provavelmente vSphere definições da configuração avançada não corrigir. Consulte o artigo [Configurar do sistema coleção](#configure-syslog-collection) para obter informações sobre como configurar o anfitrião do ESXi para reencaminhamento de chamadas do sistema.

    2. Se for bem sucedida conectividade de porta do sistema, mas ainda não vir quaisquer dados, em seguida, voltar a carregá do sistema no anfitrião do ESXi utilizando ssh para executar o seguinte comando:` esxcli system syslog reload`

- A VM com OMS agente não está definida corretamente. Para testar esta situação, execute os seguintes passos:
    1. OMS recebe à porta 1514 e envia dados para OMS. Para verificar que é aberta, execute o seguinte comando:`netstat -a | grep 1514`
    2. Deverá visualizar porta `1514/tcp` abrir. Se não o fizer, certifique-se de que o omsagent está corretamente instalada. Se não vir as informações da porta, em seguida, a porta do sistema não está aberta na VM.
        1. Certifique-se de que o agente OMS está em execução utilizando `ps -ef | grep oms`. Se não estiver em execução, iniciar o processo ao executar o comando` sudo /opt/microsoft/omsagent/bin/service_control start`
        2. Abrir o `/etc/opt/microsoft/omsagent/conf/omsagent.d/vmware_esxi.conf` ficheiro.

            Verifique se o utilizador inicial e definição de grupo válido, semelhante a:`-rw-r--r-- 1 omsagent omiusers 677 Sep 20 16:46 vmware_esxi.conf`

            Se o ficheiro não existir ou o utilizador e a definição de grupo é o problema, agir lentes por [preparar um servidor de Linux](#prepare-a-linux-server).

## <a name="next-steps"></a>Próximos passos

- Utilize [As pesquisas de registo de](log-analytics-log-searches.md) análise de registo para ver dados detalhados de anfitrião de VMware.
- [Criar os seus próprios dashboards](log-analytics-dashboards.md) a mostrar dados de anfitrião VMware.
- [Criar alertas](log-analytics-alerts.md) quando ocorrem eventos de anfitrião VMware específicos.
