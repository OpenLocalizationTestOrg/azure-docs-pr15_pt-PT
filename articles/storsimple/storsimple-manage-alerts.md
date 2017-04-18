<properties 
   pageTitle="Ver e gerir alertas StorSimple | Microsoft Azure"
   description="Descreve StorSimple alertas condições e gravidade, como configurar as notificações de alerta e como utilizar o serviço do Gestor de StorSimple para gerir os alertas."
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="10/18/2016"
   ms.author="anbacker" />

# <a name="use-the-storsimple-manager-service-to-view-and-manage-storsimple-alerts"></a>Utilizar o serviço do Gestor de StorSimple para ver e gerir StorSimple alertas

## <a name="overview"></a>Descrição geral

No separador **alertas** no serviço do Gestor de StorSimple fornece uma forma para rever e desmarque StorSimple relacionadas com o dispositivo alertas numa base em tempo real. A partir deste separador centralmente que pode monitorizar os problemas de estado de funcionamento dos seus dispositivos de StorSimple e a solução do Microsoft Azure StorSimple geral.

Neste tutorial descreve condições comuns de alertas, níveis de gravidade dos alertas e como configurar as notificações de alerta. Para além disso, inclui tabelas de referência rápida alerta, que permitem-lhe localizar um alerta específico e responder adequadamente rapidamente.

![Página de alertas](./media/storsimple-manage-alerts/HCS_AlertsPage.png)

## <a name="common-alert-conditions"></a>Condições de alertas comuns

Dispositivo StorSimple gera alertas em resposta a uma variedade de condições. Seguem-se os tipos de condições de alertas mais comuns:

- **Problemas de hardware** – estes alertas informá-lo sobre o estado de funcionamento do hardware. Estes permitem-lhe saber se forem necessárias actualizações firmware, se uma interface de rede tem problemas ou, se existir um problema com uma das suas unidades de dados.

- **Problemas de conectividade** – estas alertas ocorrem quando existe dificuldade em transferir os dados. Problemas de comunicação podem ocorrer durante a transferência de dados para e da conta de armazenamento Azure ou devido a falta de conectividade entre os dispositivos e o serviço do Gestor de StorSimple. Problemas de comunicação são algumas das corrigir uma vez que existem tantos pontos de falha. Deve verificar sempre pela primeira vez que a conectividade da rede e acesso à Internet estão disponíveis antes de continuar para resolução de problemas mais avançadas. Para obter ajuda com resolução de problemas, consulte [resolução de problemas com o cmdlet testar a ligação](storsimple-troubleshoot-deployment.md).

- **Problemas de desempenho** – estes alertas problemas são causados ao seu sistema não estiver melhores, tal como quando está numa carga elevada.

Além disso, poderá ver alertas relacionados com a segurança, atualizações ou falhas de tarefa.

## <a name="alert-severity-levels"></a>Níveis de gravidade dos alertas

Alertas tiverem níveis de gravidade diferentes, consoante o impacto que terá a situação alerta e a necessidade de uma resposta ao alerta. Os níveis de gravidade são:

- **Crítica** – este alerta está a resposta a uma condição de que está a afetar o desempenho do seu sistema de efetuada com êxito. É necessária uma acção para se certificar de que StorSimple serviço não é interrompido.

- **Aviso** -esta condição pode tornar crítica se não ficar resolvido. Deve investigar a situação e efetuar qualquer ação necessária para limpar o problema.

- **Informações** – este alerta contém informações que podem ser úteis no controlo e gerir o seu sistema.

## <a name="configure-alert-settings"></a>Configurar definições de alerta

Pode escolher se pretende ser notificado por correio eletrónico de condições de alertas para cada um dos seus dispositivos de StorSimple. Para além disso, pode identificar outros destinatários de notificação de alerta introduzindo os respetivos endereços de correio eletrónico na caixa **outros destinatários de correio eletrónico** , separados por ponto e vírgula.

>[AZURE.NOTE] Pode introduzir um máximo de 20 endereços de e-mail do dispositivo.

Depois de ativar a notificação de e-mail para um dispositivo, os membros da lista de notificação irão receber uma mensagem de correio eletrónico sempre que ocorre um alerta crítico. As mensagens serão enviadas a partir do *storsimple-alerts-noreply@mail.windowsazure.com* e irá descrevem a condição de alerta. Os destinatários podem clicar em **Anular a subscrição** para remover eles próprios a partir da lista de notificação de e-mail.

#### <a name="to-enable-email-notification-of-alerts-for-a-device"></a>Para ativar a notificação de e-mail de alertas para um dispositivo

1. Aceda a **dispositivos** > **Configurar** para o dispositivo.

2. Em **Definições de alerta**, defina o seguinte:

    1. No campo **Enviar notificação de e-mail** , selecione **Sim**.

    2. No campo **administradores do serviço de E-Mail** , selecione **Sim** se pretender que o administrador do serviço e todos os administradores a recebem as notificações de alerta.

    3. No campo de **outros destinatários de correio eletrónico** , introduza os endereços de e-mail de todos os outros destinatários que devem receber as notificações de alerta. Introduza os nomes no formato *someone@somewhere.com*. Utilize o ponto e vírgula para separar os endereços de e-mail. Pode configurar um máximo de 20 endereços de e-mail por dispositivo. 

        ![Configuração da notificação de alertas](./media/storsimple-manage-alerts/AlertNotify.png)

3. Para enviar uma notificação de correio electrónico de teste, clique no ícone de seta ao lado de **teste de enviar correio eletrónico**. O serviço do Gestor de StorSimple irá apresentar as mensagens de estado como reencaminha a notificação de teste. 

4. Quando for apresentada a seguinte mensagem, clique em **OK**. 

    ![Notificação enviado por correio electrónico de teste de alertas](./media/storsimple-manage-alerts/HCS_AlertNotificationConfig3.png)

    >[AZURE.NOTE] Se não é possível enviar a mensagem de notificação de teste, o serviço do Gestor de StorSimple irá exibir uma mensagem adequada. Clique em **OK**, aguarde alguns minutos e, em seguida, tente enviar a mensagem de notificação de teste novamente. 

## <a name="view-and-track-alerts"></a>Ver e controlar alertas

Dashboard de serviço de Gestor de StorSimple fornece-lhe rapidamente o número de alertas nos seus dispositivos, dispostas por nível de gravidade.

![Dashboard de alertas](./media/storsimple-manage-alerts/admin_alerts_dashboard.png)

Clicar no nível de gravidade abre-se no separador **alertas** . Os resultados incluem apenas os que correspondem a esse nível gravidade alertas.

![Relatório de alertas confinado ao tipo de alerta](./media/storsimple-manage-alerts/admin_alerts_scoped.png)

Clicar num alerta na lista fornece detalhes adicionais para o alerta, incluindo a hora da última foi comunicado o alerta, o número de ocorrências de alerta no dispositivo, como uma acção recomendada para resolver o alerta. Se for um alerta de hardware, também será identificar o componente de hardware.

![Exemplo de alerta de hardware](./media/storsimple-manage-alerts/admin_alerts_hardware.png)

Pode copiar os detalhes do alerta para um ficheiro de texto, se precisar de enviar as informações para o Microsoft Support. Depois de ter seguido recomendação e resolvido o alerta condição no local, deve desmarcar o alerta dispositivo seleccionando o alerta no separador **alertas** e clicando em **Limpar**. Para limpar múltiplos alertas, selecione cada alerta, clique em qualquer coluna exceto a coluna de **alerta** e, em seguida, clique em **Limpar** depois de selecionar todos os alertas para ser desmarcada. Tenha em atenção que algumas alertas automaticamente estão desmarcadas quando o problema for resolvido ou quando o sistema atualiza o alerta com novas informações.

Quando clica em **Limpar**, terá a oportunidade de fornecer comentários sobre o alerta e os passos que seguiu para resolver o problema. Alguns eventos serão limpa pelo sistema se outro evento é acionado com novas informações. Nesse caso, verá a seguinte mensagem.

![Mensagem de alerta limpar](./media/storsimple-manage-alerts/admin_alerts_system_clear.png)

## <a name="sort-and-review-alerts"></a>Alertas de ordenar e rever

Poderá encontrar mais eficientes para executar relatórios sobre alertas, para que possa rever e desmarcá-las em grupos. Para além disso, no separador **alertas** pode apresentar até 250 alertas. Se tiver excedido esse número de alertas, nem todos os alertas serão apresentados na vista predefinida. Pode combinar os seguintes campos para personalizar as alertas são apresentadas:

- **Estado** – pode apresentar **ativos** ou **desmarcado** alertas. Ainda estão a ser activadas alertas ativas no seu sistema, enquanto alertas desmarcadas foram manualmente desmarcadas por um administrador ou através de programação limpas porque o sistema atualizadas a condição alerta com novas informações.

- **Gravidade** – pode apresentar alertas de todos os níveis de gravidade (informações críticas, aviso,) ou apenas uma determinada gravidade, tal como alertas apenas críticos.

- **Origem** – pode mostrar alertas de todas as fontes ou limitar os alertas para aqueles que pertencem ao serviço ou um ou todos os dispositivos.

- **Intervalo de tempo** – ao especificar as datas **de** e **para** e carimbos de data / hora, pode procurar alertas durante o período de tempo que lhe interessam.

## <a name="alerts-quick-reference"></a>Referência rápida de alertas

As tabelas seguintes listam alguns dos alertas Microsoft Azure StorSimple que poderá encontrar, bem como informações adicionais e recomendações sempre que disponíveis. Alertas de dispositivo StorSimple abrange uma das categorias seguintes:

- [Alertas de conectividade de nuvem](#cloud-connectivity-alerts)

- [Cluster alertas](#cluster-alerts)

- [Alertas de recuperação de falhas](#disaster-recovery-alerts)

- [Alertas de hardware](#hardware-alerts)

- [Alertas de falha de tarefa](#job-failure-alerts)

- [Alertas de volume localmente afixada](#locally-pinned-volume-alerts)

- [Alertas de funcionamento em rede](#networking-alerts)

- [Alertas de desempenho](#performance-alerts)

- [Alertas de segurança](#security-alerts)

- [Alertas do pacote de suporte](#support-package-alerts)

- [Alertas de actualização](#update-alerts)

### <a name="cloud-connectivity-alerts"></a>Alertas de conectividade de nuvem

|Texto do alerta|Evento|Obter mais informações / acções recomendadas|
|:---|:---|:---|
|Não é possível estabelecer conectividade ao <*nome de credenciais da nuvem*>.|Não consegue ligar à conta de armazenamento.|Parece poderá haver um problema de conectividade com o seu dispositivo. Volte a executar o `Test-HcsmConnection` cmdlet a partir da Interface do Windows PowerShell para StorSimple no seu dispositivo para identificar e corrigir o problema. Se as definições estão corretas, o problema poderá ser com as credenciais da conta de armazenamento para a qual foi elevado o alerta. Neste caso, utilize o `Test-HcsStorageAccountCredential` cmdlet para determinar se existem problemas que pode resolver.<ul><li>Verificar as suas definições de rede.</li><li>Verifique as credenciais da conta de armazenamento.</li></ul>|
|Não recebemos um heartbeat a partir do seu dispositivo para os últimos <*número*> minutos.|Não é possível ligar ao dispositivo.|Parece existe um problema de conectividade com o seu dispositivo. Utilize o `Test-HcsmConnection` cmdlet a partir da Interface do Windows PowerShell para StorSimple no seu dispositivo para identificar e corrigir o problema ou contacte o administrador de rede.|

### <a name="storsimple-behavior-when-cloud-connectivity-fails"></a>Comportamento do StorSimple quando ocorre uma falha na nuvem conectividade

O que acontece se ocorre uma falha na nuvem conectividade para o meu dispositivo StorSimple a ser executada em produção?

Se na nuvem conectividade falhar no seu dispositivo de produção StorSimple, em seguida, dependendo o estado do seu dispositivo, a seguinte pode ocorrer: 

- **Para os dados locais no seu dispositivo**: há algum tempo, haverá sem interrupção e lê irão continuar a ser fornecido. No entanto, como o número de IOs pendentes aumenta e excede um limite, o lê pode começar a falhar. 

    Dependendo da quantidade de dados no seu dispositivo, o escreve também irão continuar a ocorrer por primeiros de poucas horas depois de interrupção de conectividade a nuvem. O escreve irá desacelerar, em seguida e são eventualmente começar a falhar se a conectividade de nuvem for interrompida para várias horas. (Existe armazenamento temporário no dispositivo para os dados que está a ser enviados para a nuvem. Esta área é esvaziada quando os dados são enviados. Se a conectividade falhar, não serão enviados dados nesta área de armazenamento na nuvem e IO irá falhar.)   

 
- **Para os dados na nuvem**: para a maioria dos erros de conectividade de nuvem, é devolvido um erro. Assim que a conectividade for restaurada, os IOs são retomadas sem que o utilizador está com dificuldades que trazer o volume online. Em casos raros, intervenção do utilizador que devem ser seguida para trazer novamente o volume online a partir do Azure portal clássico. 
 
- **Para instantâneos nuvem em curso**: A operação é repetida algumas vezes no prazo de 5 de 4 horas e se a ligação não está a ser restaurada, os instantâneos nuvem irão falhar.


### <a name="cluster-alerts"></a>Cluster alertas

|Texto do alerta|Evento|Obter mais informações / acções recomendadas|
|:---|:---|:---|
|Dispositivo Ocorreu uma falha ao longo do <*nome do dispositivo*>.|Dispositivo está no modo de manutenção.|Dispositivo Ocorreu uma falha ao longo do devido a entrar ou sair do modo de manutenção. Este é normal e não é necessária nenhuma ação. Depois de ter confirmado este alerta, desmarque-a partir da página de alertas.|
|Dispositivo Ocorreu uma falha ao longo do <*nome do dispositivo*>.|Firmware do dispositivo ou software apenas foi atualizado.|Ocorreu um cluster activação pós-falha devido a uma atualização. Este é normal e não é necessária nenhuma ação. Depois de ter confirmado este alerta, desmarque-a partir da página de alertas.|
|Dispositivo Ocorreu uma falha ao longo do <*nome do dispositivo*>.|Controlador de foi encerrar ou reiniciado.|Dispositivo Ocorreu uma falha ao longo do porque o controlador de ativo foi encerrar ou reiniciado por um administrador. Não é necessária nenhuma ação. Depois de ter confirmado este alerta, desmarque-a partir da página de alertas.|
|Dispositivo Ocorreu uma falha ao longo do <*nome do dispositivo*>.|Activação planeada pós-falha.|Certifique-se de que se trata de uma activação planeada pós-falha. Depois de reencaminhado ação adequada, desmarque este alerta a partir da página de alertas.|
|Dispositivo Ocorreu uma falha ao longo do <*nome do dispositivo*>.|Não planeado activação pós-falha.|StorSimple baseia-se para recuperar automaticamente activações pós-falha não planeada. Se vir um grande número destes alertas, contacte o Microsoft Support.|
|Dispositivo Ocorreu uma falha ao longo do <*nome do dispositivo*>.|Causa outras/desconhecida.|Se vir um grande número destes alertas, contacte o Microsoft Support. Depois do problema for resolvido, desmarque este alerta a partir da página de alertas.|
|Um serviço de dispositivo críticos relatórios de estado como falhou.|Falha do serviço de DataPath. |Contacte o Microsoft Support para obter assistência.|
|Endereço IP virtual de interface de rede <*dados #*> relatórios de estado como falhou. |Causa outras/desconhecida. |Por vezes, temporárias condições podem causar estes alertas. Se este for o caso, em seguida, este alerta vai ser automaticamente limpa após algum tempo. Se o problema persistir, contacte o Microsoft Support.|
|Endereço IP virtual de interface de rede <*dados #*> relatórios de estado como falhou.|Nome da interface: <*dados #*> endereço IP <IP address> não pode ficar online porque um endereço IP duplicado foi detetado na rede. |Garantir que os endereços IP duplicados são removido da rede ou reconfigure interface com um endereço IP diferente.|


### <a name="disaster-recovery-alerts"></a>Alertas de recuperação de falhas

|Texto do alerta|Evento|Obter mais informações / acções recomendadas|
|:---|:---|:---|
|Operações de recuperação não conseguiu restaurar todas as definições para este serviço. Dados de configuração do dispositivo são num estado inconsistente para alguns dispositivos.|Detetado após a recuperação de falhas inconsistência dos dados.|Dados encriptados o serviço não estão sincronizados com que no dispositivo. Autorize o dispositivo <*nome do dispositivo*> a partir do Gestor de StorSimple para iniciar o processo de sincronização. Utilizar a Interface do Windows PowerShell para StorSimple para executar o `Restore-HcsmEncryptedServiceData` no dispositivo <*nome do dispositivo*> cmdlet, fornecer a palavra-passe antiga como uma entrada para este cmdlet para restaurar o perfil de segurança. Em seguida, execute o `Invoke-HcsmServiceDataEncryptionKeyChange` cmdlet para atualizar a chave de encriptação de dados do serviço. Depois de reencaminhado ação adequada, desmarque este alerta a partir da página de alertas.|


### <a name="hardware-alerts"></a>Alertas de hardware

|Texto do alerta|Evento|Obter mais informações / acções recomendadas|
|:---|:---|:---|
|Componente de hardware <*ID do componente*> relatórios de estado como <*Estado*>.||Por vezes, temporárias condições podem causar estes alertas. Se Sim, este alerta será automaticamente desmarcada após algum tempo. Se o problema persistir, contacte o Microsoft Support.|
|Controlador passiva disfunção.|Controlador de passivo (secundário) não está a funcionar.|O dispositivo está operacional, mas um dos seus controladores está danificada. Experimente reiniciar que o controlador de. Se não for resolvido o problema, contacte o Microsoft Support.|

### <a name="job-failure-alerts"></a>Alertas de falha de tarefa

|Texto do alerta|Evento|Obter mais informações / acções recomendadas|
|:---|:---|:---|
|Cópia de segurança do <*ID de grupo de volume de origem*> falhou.|Falha na tarefa de cópia de segurança.|Problemas de conectividade podem estar a impedir a operação de cópia de segurança a conclusão com êxito. Se não existirem sem problemas de conectividade, poderá atingiu o número máximo de cópias de segurança. Elimine qualquer cópias de segurança que já não forem necessárias e repita a operação. Depois de reencaminhado ação adequada, desmarque este alerta a partir da página de alertas.|
|Clonar de <*elemento de cópia de segurança de origem IDs*> <*números de série de volume de destino*> Ocorreu uma falha.|Falha ao clonar tarefa.|Atualize a lista de cópia de segurança para confirmar que a cópia de segurança ainda é válida. Se a cópia de segurança for válida, é possível que problemas de conectividade de nuvem estejam a impedir a operação de clonar a conclusão com êxito. Se não existirem sem problemas de conectividade, poderá atingiu o limite de armazenamento. Elimine qualquer cópias de segurança que já não forem necessárias e repita a operação. Depois de reencaminhado ação adequada para resolver o problema, desmarque este alerta a partir da página de alertas.|
|Restauro de <*elemento de cópia de segurança de origem IDs*> falhou.|Restaure falha na tarefa.|Atualize a lista de cópia de segurança para confirmar que a cópia de segurança ainda é válida. Se a cópia de segurança for válida, é possível que problemas de conectividade de nuvem estejam a impedir a operação de restaurar a conclusão com êxito. Se não existirem sem problemas de conectividade, poderá atingiu o limite de armazenamento. Elimine qualquer cópias de segurança que já não forem necessárias e repita a operação. Depois de reencaminhado ação adequada para resolver o problema, desmarque este alerta a partir da página de alertas.|

### <a name="locally-pinned-volume-alerts"></a>Alertas de volume localmente afixada

|Texto do alerta|Evento|Obter mais informações / acções recomendadas|
|:---|:---|:---|
|Criação de volume local <*nome do volume*> falhou.| A tarefa da criação de volume falhou. <*Mensagem de erro correspondente ao código de erro de falha*>.|Problemas de conectividade podem estar a impedir a operação de criação de espaço de completar com êxito. Volumes localmente afixados configurações thickly e o processo de criação espaço envolve capaz volumes em camadas para a nuvem. Se não existirem sem problemas de conectividade, poderá ter esgotar o espaço local no dispositivo. Determine se existe espaço no dispositivo antes de repetir esta operação.|
|Falha de expansão do volume local <*nome do volume*>.|A tarefa de alteração de volume falhou devido a < a*mensagem de erro correspondente ao código de erro de falha*>.|Problemas de conectividade podem estar a impedir a operação de expansão de volume a partir de completar com êxito. Volumes localmente afixados configurações thickly e o processo de expandir o espaço existente envolve capaz volumes em camadas para a nuvem. Se não existirem sem problemas de conectividade, poderá ter esgotar o espaço local no dispositivo. Determine se existe espaço no dispositivo antes de repetir esta operação.|
|Falha na conversão de volume <*nome do volume*>.|A tarefa de conversão de volume para converter o tipo de volume a partir do localmente afixada à camadas falha.|A conversão do volume do tipo localmente afixada à camadas não pôde ser concluída. Certifique-se de que não existem sem problemas de conectividade a impedir a operação a conclusão com êxito. Para a resolução de problemas de conectividade aceda a [resolução de problemas com o cmdlet HcsmConnection de teste](storsimple-troubleshoot-deployment.md#troubleshoot-with-the-test-hcsmconnection-cmdlet).<br>O volume localmente afixado original agora foi marcado como um volume em camadas uma vez que alguns dos dados a partir do volume localmente afixado tem espalhado na nuvem durante a conversão. O volume em camadas resultante ainda é que ocupa espaço local no dispositivo não pode ser recuperado para futuros volumes locais.<br>Resolver quaisquer problemas de conectividade, desmarque o alerta e converter este volume o tipo de volume localmente afixada original para garantir que todos os dados são disponibilizados localmente novamente.|
|Falha na conversão de volume <*nome do volume*>.|A tarefa de conversão de volume para converter o tipo de volume a partir de camadas para localmente afixados falhou.|Conversão do volume do tipo de camadas para localmente afixados não podem ser concluído. Certifique-se de que não existem sem problemas de conectividade a impedir a operação a conclusão com êxito. Para a resolução de problemas de conectividade aceda a [resolução de problemas com o cmdlet HcsmConnection de teste](storsimple-troubleshoot-deployment.md#troubleshoot-with-the-test-hcsmconnection-cmdlet).<br>O volume em camadas original marcado como um volume localmente afixado como parte do processo de conversão continua a ter dados que residem na nuvem, enquanto o espaço thickly aprovisionado no dispositivo para este volume já não pode recuperado para futuros volumes locais.<br>Resolver quaisquer problemas de conectividade, desmarque o alerta e converter este volume o tipo de volume em camadas original para garantir que pode ser recuperado espaço local thickly aprovisionado no dispositivo.|
|Consumo de espaço local para o locais instantâneos da chegar <*nome do grupo de volume*>|Instantâneos locais para a política de cópia de segurança mais cedo poderão ser executado espaço e invalidados para evitar falhas de escrita de anfitrião.|Frequentes instantâneos locais juntamente com um vasilha alta dados em volumes associada este grupo para a política de cópia de segurança estão a causar espaço local no dispositivo para ser consumida rapidamente. Elimine qualquer locais instantâneos que já não forem necessárias. Para além disso, atualize o seu agendas instantâneo local para esta política de cópia de segurança tirar instantâneos locais menos frequentes e certifique-se de que nuvem instantâneos sejam tomados regularmente. Se estas ações não são tomadas, mais cedo poderá ser esgotado espaço local para estes instantâneos e o sistema irá eliminar automaticamente-los para se certificar de que escreve anfitrião continua a ser processadas com êxito.|
|Tem sido invalidados instantâneos locais para <*nome do grupo volume*>.|Os instantâneos locais para <*nome do grupo volume*> foram invalidados e, em seguida, eliminados porque foram superior ao espaço local no dispositivo.|Para assegurar que esta não voltar a ocorrer no futuro, reveja as agendas de instantâneo local para esta política de cópia de segurança e eliminar qualquer locais instantâneos que já não forem necessárias. Frequentes instantâneos locais juntamente com um vasilha alta dados em volumes associada este grupo para a política de cópia de segurança poderão causar espaço local no dispositivo para ser consumida rapidamente.|
|Restauro de <*elemento de cópia de segurança de origem IDs*> falhou.|A tarefa de restauro falhou.|Se que tem localmente afixados ou uma mistura de volumes localmente afixados e em camadas nesta política de cópia de segurança, atualize a lista de cópia de segurança para confirmar que a cópia de segurança ainda estiver válida. Se a cópia de segurança for válida, é possível que problemas de conectividade de nuvem estejam a impedir a operação de restaurar a conclusão com êxito. Os volumes localmente afixados que foram a ser restaurados como parte deste grupo instantâneo não possui todos os seus dados são transferidos para o dispositivo e, se tiver uma mistura de volumes em camadas e localmente afixados neste grupo instantâneo, não serão sincronizados entre si. Para concluir com êxito a operação de restauro, levar os volumes neste grupo offline no anfitrião do e repita a operação de restaurar. Tenha em atenção que todas as modificações para os dados de volume que foram executadas durante o processo de restaurar serão perdidas.|

### <a name="networking-alerts"></a>Alertas de funcionamento em rede

|Texto do alerta|Evento|Obter mais informações / acções recomendadas|
|:---|:---|:---|
|Não foi possível iniciar o serviço de StorSimple (s).|Erro de DataPath |Se o problema persistir, contacte o Microsoft Support.|
|Endereços IP duplicados detetado para 'Data0'.| |O sistema tiver detectado um conflito de endereço IP '10.0.0.1'. O recurso de rede 'Data0' no dispositivo *<device1>* está offline. Certifique-se de que este endereço IP não é utilizado por qualquer outra entidade na rede. Para resolver problemas de rede, aceda a [resolução de problemas com o cmdlet Get-NetAdapter](storsimple-troubleshoot-deployment.md#troubleshoot-with-the-get-netadapter-cmdlet). Contacte o administrador de rede para ajudar a resolver este problema. Se o problema persistir, contacte o Microsoft Support. |
|Endereços IPv4 do (ou IPv6) para 'Data0' está offline.| |O recurso de rede 'Data0' com o endereço IP '10.0.0.1'. e comprimento «22» no dispositivo do prefixo *<device1>* está offline. Certifique-se de que as portas mudar às quais está ligada esta interface estão operacionais. Para resolver problemas de rede, aceda a [resolução de problemas com o cmdlet Get-NetAdapter](storsimple-troubleshoot-deployment.md#troubleshoot-with-the-get-netadapter-cmdlet). |
 

### <a name="performance-alerts"></a>Alertas de desempenho

|Texto do alerta|Evento|Obter mais informações / acções recomendadas|
|:---|:---|:---|
|A carga de dispositivo excedeu <*limiar*>.|Mais lento do que esperado tempos de resposta.|A utilização de relatórios do dispositivo numa carga elevada de entrada/saída. Isto pode provocar o dispositivo não funcione, bem como deverá. Reveja as cargas de trabalho que tiver ligado ao dispositivo e determinar se existirem qualquer que podem ser movidas para outro dispositivo ou que já não são necessárias.<br>Para compreender o estado atual, aceda a [utilizar o serviço do Gestor de StorSimple para monitorizar o seu dispositivo](storsimple-monitor-device.md)|

### <a name="security-alerts"></a>Alertas de segurança

|Texto do alerta|Evento|Obter mais informações / acções recomendadas|
|:---|:---|:---|
|Começou a sessão do Microsoft Support.|Terceiros acedido sessão de suporte.|Confirme que este acesso está autorizado. Depois de reencaminhado ação adequada, desmarque este alerta a partir da página de alertas.|
|Palavra-passe para <*elemento*> irá expirar em <*comprimento do tempo*>.|A aproximação expiração de palavra-passe.|Altere a palavra-passe antes que expire.|
|Informações de configuração de segurança em falta de <*ID de elemento*>.||Os volumes associados este contentor de volume não podem ser utilizados para criar uma réplica da sua configuração StorSimple. Para se certificar de que os seus dados em segurança estão armazenados, recomendamos que elimine o contentor de volume e quaisquer volumes associadas com o contentor de volume. Depois de reencaminhado ação adequada, desmarque este alerta a partir da página de alertas.|
|<*número*> tentativas de início de sessão Ocorreu uma falha de <*ID de elemento*>.|Tentarem falhadas múltiplos.|O dispositivo pode estar em ataque ou um utilizador autorizado está a tentar estabelecer ligação com uma palavra-passe incorretas.<ul><li>Contacte os utilizadores autorizados e certifique-se de que estas tentativas terem de uma origem de legítima. Se continuar a ver grandes quantidades de tentativas falhadas de início de sessão, considere desativar gestão remota e contacte o administrador de rede. Depois de reencaminhado ação adequada, desmarque este alerta a partir da página de alertas.</li><li>Verifique que o seu Gestor de instantâneo instâncias estão configuradas com a palavra-passe correta. Depois de reencaminhado ação adequada, desmarque este alerta a partir da página de alertas.</li></ul>Para mais informações, consulte [alterar uma palavra-passe do dispositivo expirados](storsimple-snapshot-manager-manage-devices.md#change-an-expired-device-password).|
|As falhas de uma ou mais ocorreram ao alterar a chave de encriptação de dados do serviço.||Ocorreram erros encontrados ao alterar a chave de encriptação de dados do serviço. Depois de ter endereçadas as condições de erro, execute o `Invoke-HcsmServiceDataEncryptionKeyChange` cmdlet a partir da Interface do Windows PowerShell para StorSimple no seu dispositivo para actualizar o serviço. Se este problema persistir, contacte o suporte da Microsoft. Depois de resolver o problema, desmarque este alerta a partir da página de alertas.|

### <a name="support-package-alerts"></a>Alertas do pacote de suporte

|Texto do alerta|Evento|Obter mais informações / acções recomendadas|
|:---|:---|:---|
|Falha na criação do pacote de suporte.|StorSimple não foi possível gerar o pacote.|Repita esta operação. Se o problema persistir, contacte o Microsoft Support. Depois de ter resolvido o problema, desmarque este alerta a partir da página de alertas.|

### <a name="update-alerts"></a>Alertas de actualização

|Texto do alerta|Evento|Obter mais informações / acções recomendadas|
|:---|:---|:---|
|Correcção instalada.|Atualização de software/firmware concluída.|A correcção ter sido instalada com êxito no seu dispositivo.|
|Manuais atualizações disponíveis.|Notificação de atualizações disponíveis.|Utilize a Interface do Windows PowerShell para StorSimple no seu dispositivo para instalar estas atualizações. <br>Para obter mais informações, aceda ao [atualizar o seu dispositivo StorSimple 8000 série](storsimple-update-device.md).|
|Novas atualizações disponíveis.|Notificação de atualizações disponíveis.|Pode instalar estas atualizações a partir da página de **Manutenção** ou ao utilizar a Interface do Windows PowerShell para StorSimple no seu dispositivo. <br>Para obter mais informações, aceda ao [atualizar o seu dispositivo StorSimple 8000 série](storsimple-update-device.md).|
|Ocorreu uma falha ao instalar atualizações.|As atualizações não foram instaladas com êxito.|O sistema não foi possível instalar as atualizações. Pode instalar estas atualizações a partir da página de **Manutenção** ou ao utilizar a Interface do Windows PowerShell para StorSimple no seu dispositivo. Se o problema persistir, contacte o Microsoft Support. <br>Para obter mais informações, aceda ao [atualizar o seu dispositivo StorSimple 8000 série](storsimple-update-device.md).|
|Não é possível procurar novas atualizações automaticamente.|Ocorreu uma falha com a verificação automática.|Pode verificar manualmente as novas atualizações a partir da página de **Manutenção** .|
|Novo agente WUA disponível.|Notificação de atualização disponível.|Transferir o Windows Update Agent mais recente e instalá-lo a partir da interface do Windows PowerShell.|
|Versão do componente de firmware <*ID do componente*> não corresponder a com hardware.|Actualizações firmware não foram instaladas com êxito.|Contacte o suporte da Microsoft.|

## <a name="next-steps"></a>Próximos passos

Saiba mais sobre [StorSimple erros e resolução de problemas de um dispositivo operacional](storsimple-troubleshoot-operational-device.md).

