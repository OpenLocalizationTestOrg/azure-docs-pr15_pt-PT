<properties 
   pageTitle="Ver e gerir alertas de matriz Virtual StorSimple | Microsoft Azure"
   description="Descreve as condições de alertas de matriz Virtual StorSimple e gravidade e como utilizar o serviço do Gestor de StorSimple para gerir os alertas."
   services="storsimple"
   documentationCenter="NA"
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="06/07/2016"
   ms.author="alkohli" />

# <a name="use-the-storsimple-manager-service-to-view-and-manage-alerts-for-the-storsimple-virtual-array"></a>Utilizar o serviço do Gestor de StorSimple para ver e gerir alertas para a matriz Virtual StorSimple

## <a name="overview"></a>Descrição geral

No separador **alertas** no serviço do Gestor de StorSimple fornece uma forma para rever e desmarque alertas relacionados com matriz Virtual StorSimple – numa base em tempo real. A partir deste separador centralmente que pode monitorizar os problemas de estado de funcionamento do seu matrizes de Virtual StorSimple (também conhecido como StorSimple no local dispositivos virtuais) e a solução do Microsoft Azure StorSimple geral.

Neste tutorial descreve como configurar as notificações de alerta, condições comuns de alertas, níveis de gravidade dos alertas e como ver e controlar alertas. Para além disso, inclui tabelas de referência rápida alerta, que permitem-lhe localizar um alerta específico e responder adequadamente rapidamente.

![Página de alertas](./media/storsimple-ova-manage-alerts/alerts1.png)

## <a name="configure-alert-settings"></a>Configurar definições de alerta

Pode escolher se pretende ser notificado por correio eletrónico de condições de alertas para cada um dos seus dispositivos virtuais StorSimple. Para além disso, pode identificar outros destinatários de notificação de alerta introduzindo os respetivos endereços de correio eletrónico na caixa **outros destinatários de correio eletrónico** , separados por ponto e vírgula.

>[AZURE.NOTE] Pode introduzir um máximo de 20 endereços de e-mail por dispositivo virtual.

Depois de ativar a notificação de e-mail para um dispositivo virtual, os membros da lista de notificação irão receber uma mensagem de correio eletrónico sempre que ocorre um alerta crítico. As mensagens serão enviadas a partir do *storsimple-alerts-noreply@mail.windowsazure.com* e irá descrevem a condição de alerta. Os destinatários podem clicar em **Anular a subscrição** para remover eles próprios a partir da lista de notificação de e-mail.

#### <a name="to-enable-email-notification-of-alerts-for-a-virtual-device"></a>Para ativar a notificação de e-mail de alertas para um dispositivo virtual

1. Aceda a **dispositivos** > **configuração** para o dispositivo virtual. Aceda à secção **definições de alerta** .

    ![definições de alerta](./media/storsimple-ova-manage-alerts/alerts2.png)

2. Em **definições de alerta**, defina o seguinte:

    1. No campo **Enviar notificação de e-mail** , selecione **Sim**.

    2. No campo **administradores do serviço de E-Mail** , selecione **Sim** se pretender que o administrador do serviço e todos os administradores a recebem as notificações de alerta.

    3. No campo de **outros destinatários de correio eletrónico** , introduza os endereços de e-mail de todos os outros destinatários que devem receber as notificações de alerta. Introduza os nomes no formato *someone@somewhere.com*. Utilize o ponto e vírgula para separar os endereços de e-mail. Pode configurar um máximo de 20 endereços de e-mail por dispositivo virtual. 

        ![configuração da notificação de alertas](./media/storsimple-ova-manage-alerts/alerts3.png)

3. Na parte inferior da página, clique em **Guardar** para guardar a configuração.

4. Para enviar uma notificação de correio electrónico de teste, clique no ícone de seta ao lado de **teste de enviar correio eletrónico**. O serviço do Gestor de StorSimple irá apresentar as mensagens de estado como reencaminha a notificação de teste. 

5. Quando for apresentada a seguinte mensagem, clique em **OK**. 

    ![Notificação enviado por correio electrónico de teste de alertas](./media/storsimple-ova-manage-alerts/alerts4.png)

    >[AZURE.NOTE] Se não é possível enviar a mensagem de notificação de teste, o serviço do Gestor de StorSimple irá exibir uma mensagem adequada. Clique em **OK**, aguarde alguns minutos e, em seguida, tente enviar a mensagem de notificação de teste novamente. 

    A mensagem de notificação de teste será semelhante ao seguinte.

    ![Exemplo de correio electrónico de teste de alertas](./media/storsimple-ova-manage-alerts/alerts5.png)

## <a name="common-alert-conditions"></a>Condições de alertas comuns

A matriz de Virtual StorSimple gera alertas em resposta a uma variedade de condições. Seguem-se os tipos de condições de alertas mais comuns:

- **Problemas de conectividade** – estas alertas ocorrem quando existe dificuldade em transferir os dados. Problemas de comunicação podem ocorrer durante a transferência de dados para e da conta de armazenamento Azure ou devido a falta de conectividade entre os dispositivos virtuais e o serviço do Gestor de StorSimple. Problemas de comunicação são algumas das corrigir uma vez que existem tantos pontos de falha. Deve verificar sempre pela primeira vez que a conectividade da rede e acesso à Internet estão disponíveis antes de continuar para resolução de problemas mais avançadas. Para obter informações sobre portas e as definições da firewall, aceda a [matriz Virtual StorSimple requisitos de sistema](storsimple-ova-system-requirements.md). Para obter ajuda com resolução de problemas, consulte [resolução de problemas com o cmdlet testar a ligação](storsimple-troubleshoot-deployment.md).

- **Problemas de desempenho** – estes alertas problemas são causados ao seu sistema não estiver melhores, tal como quando está numa carga elevada.

Além disso, poderá ver alertas relacionados com a segurança, atualizações ou falhas de tarefa.

## <a name="alert-severity-levels"></a>Níveis de gravidade dos alertas

Alertas tiverem níveis de gravidade diferentes, consoante o impacto que terá a situação alerta e a necessidade de uma resposta ao alerta. Os níveis de gravidade são:

- **Crítica** – este alerta está a resposta a uma condição de que está a afetar o desempenho do seu sistema de efetuada com êxito. É necessária uma acção para se certificar de que StorSimple serviço não é interrompido.

- **Aviso** -esta condição pode tornar crítica se não ficar resolvido. Deve investigar a situação e efetuar qualquer ação necessária para limpar o problema.

- **Informações** – este alerta contém informações que podem ser úteis no controlo e gerir o seu sistema.

## <a name="view-and-track-alerts"></a>Ver e controlar alertas

Dashboard de serviço de Gestor de StorSimple fornece-lhe rapidamente o número de alertas nos seus dispositivos virtuais, dispostas por nível de gravidade.

![Dashboard de alertas](./media/storsimple-ova-manage-alerts/alerts6.png)

Clicar no nível de gravidade abre-se no separador **alertas** . Os resultados incluem apenas os que correspondem a esse nível gravidade alertas.

![Relatório de alertas confinado ao tipo de alerta](./media/storsimple-ova-manage-alerts/alerts7.png)

Clicar num alerta na lista fornece detalhes adicionais para o alerta, incluindo a hora da última foi comunicado o alerta, o número de ocorrências de alerta no dispositivo, como uma acção recomendada para resolver o alerta.

Pode copiar os detalhes do alerta para um ficheiro de texto, se precisar de enviar as informações para o Microsoft Support. Depois de ter seguido recomendação e resolvido o alerta condição no local, deve desmarcar o alerta de selecionar o alerta no separador **alertas** e clicando em **Limpar**. Para limpar múltiplos alertas, selecione cada alerta, clique em qualquer coluna exceto a coluna de **alerta** e, em seguida, clique em **Limpar** depois de selecionar todos os alertas para ser desmarcada. Tenha em atenção que algumas alertas automaticamente estão desmarcadas quando o problema for resolvido ou quando o sistema atualiza o alerta com novas informações.

Quando clica em **Limpar**, terá a oportunidade de fornecer comentários sobre o alerta e os passos que seguiu para resolver o problema. 

![alertas de comentários](./media/storsimple-ova-manage-alerts/clear-alert.png)

Clique no ícone de verificação ![ícone de verificação](./media/storsimple-ova-manage-alerts/check-icon.png) Para guardar os seus comentários.

Alguns eventos serão limpa pelo sistema se outro evento é acionado com novas informações. Nesse caso, verá a seguinte mensagem.

![Mensagem de alerta limpar](./media/storsimple-ova-manage-alerts/alerts8.png)

## <a name="sort-and-review-alerts"></a>Alertas de ordenar e rever

No separador **alertas** pode apresentar até 250 alertas. Se tiver excedido esse número de alertas, nem todos os alertas serão apresentados na vista predefinida. Pode combinar os seguintes campos para personalizar as alertas são apresentadas:

- **Estado** – pode apresentar **ativos** ou **desmarcado** alertas. Ainda estão a ser activadas alertas ativas no seu sistema, enquanto alertas desmarcadas foram manualmente desmarcadas por um administrador ou através de programação limpas porque o sistema atualizadas a condição alerta com novas informações.

- **Gravidade** – pode apresentar alertas de todos os níveis de gravidade (informações críticas, aviso,) ou apenas uma determinada gravidade, tal como alertas apenas críticos.

- **Origem** – pode mostrar alertas de todas as fontes ou limitar os alertas para aqueles que pertencem ao serviço ou um ou todos os dispositivos virtuais.

- **Intervalo de tempo** – ao especificar as datas **de** e **para** e carimbos de data / hora, pode procurar alertas durante o período de tempo que lhe interessam.

## <a name="alerts-quick-reference"></a>Referência rápida de alertas

As tabelas seguintes listam alguns dos alertas Microsoft Azure StorSimple que poderá encontrar, bem como informações adicionais e recomendações sempre que disponíveis. Alertas de dispositivo virtual StorSimple abrange uma das categorias seguintes:

- [Alertas de conectividade de nuvem](#cloud-connectivity-alerts)

- [Alertas de configuração](#configuration-alerts)

- [Alertas de falha de tarefa](#job-failure-alerts)

- [Alertas de desempenho](#performance-alerts)

- [Alertas de segurança](#security-alerts)

- [Alertas de actualização](#update-alerts)

### <a name="cloud-connectivity-alerts"></a>Alertas de conectividade de nuvem

|Texto do alerta|Evento|Obter mais informações / acções recomendadas|
|:---|:---|:---|
|Dispositivo *<device name>* não está ligada para a nuvem.|Não consegue ligar o dispositivo com nome para a nuvem. |Não foi possível ligar para a nuvem. Isto pode ser devido a um dos seguintes procedimentos:<ul><li>Poderão existir um problema com as definições de rede no seu dispositivo.</li><li>Poderão existir um problema com as credenciais da conta de armazenamento.</li></ul>Para mais informações sobre como resolver problemas de conectividade, ir para a [web local da IU](storsimple-ova-web-ui-admin.md) do dispositivo.|


### <a name="configuration-alerts"></a>Alertas de configuração

|Texto do alerta|Evento|Obter mais informações / acções recomendadas|
|:---|:---|:---|
|No local a configuração de dispositivo virtual não suportada.|Desempenho lento.|A configuração actual pode resultar em degradação do desempenho. Certifique-se de que o seu servidor cumpre os requisitos de configuração mínima. Para obter mais informações, aceda ao [StorSimple Virtual requisitos de matriz](storsimple-ova-system-requirements.md). 
|Está a executar o espaço de disco aprovisionada no <*nome do dispositivo*>.|Aviso de espaço de disco.|Quando estiver a executar baixo espaço em disco aprovisionada. Para libertar espaço, considere mover das cargas de trabalho para outro volume ou partilhar ou eliminar dados.

### <a name="job-failure-alerts"></a>Alertas de falha de tarefa

|Texto do alerta|Evento|Obter mais informações / acções recomendadas|
|:---|:---|:---|
|Cópia de segurança do <*nome do dispositivo*> não foi possível ser concluída.|Falha da tarefa de cópia de segurança.|Não foi possível criar uma cópia de segurança. Considere o seguinte:<ul><li>Problemas de conectividade podem estar a impedir a operação de cópia de segurança a conclusão com êxito. Certifique-se de que não existem sem problemas de conectividade. Para mais informações sobre como resolver problemas de conectividade, ir para a [web local da IU](storsimple-ova-web-ui-admin.md) para o seu dispositivo virtual.</li><li>Atingiu o limite de armazenamento disponível. Para libertar espaço, considere eliminar quaisquer cópias de segurança que já não forem necessárias.</li></ul> Resolver os problemas, desmarque o alerta e repita a operação.|
|Restaurar do <*nome do dispositivo*> não foi possível ser concluída.|Restaure a falha da tarefa.|Não foi possível restaurar a partir de cópia de segurança. Considere o seguinte:<ul><li>Lista de cópia de segurança pode não ser válida. Atualize a lista para verificar a que mesma ainda estiver válida.</li><li>Problemas de conectividade podem estar a impedir a operação de restaurar a conclusão com êxito. Certifique-se de que não existem sem problemas de conectividade.</li><li>Atingiu o limite de armazenamento disponível. Para libertar espaço, considere eliminar quaisquer cópias de segurança que já não forem necessárias.</li></ul>Resolver os problemas, desmarque o alerta e repita a operação de restaurar.|
|Não foi possível concluir clonar do <*nome do dispositivo*>.|Clonar falha da tarefa.|Não foi possível criar um clonar. Considere o seguinte:<ul><li>Lista de cópia de segurança pode não ser válida. Atualize a lista para verificar a que mesma ainda estiver válida.</li><li>Problemas de conectividade podem estar a impedir a operação de clonar a conclusão com êxito. Certifique-se de que não existem sem problemas de conectividade.</li><li>Atingiu o limite de armazenamento disponível. Para libertar espaço, considere eliminar quaisquer cópias de segurança que já não forem necessárias.</li></ul>Resolver os problemas, desmarque o alerta e repita a operação.|

### <a name="performance-alerts"></a>Alertas de desempenho

|Texto do alerta|Evento|Obter mais informações / acções recomendadas|
|:---|:---|:---|
|Está a ocorrer atrasos inesperados na transferência de dados.|Transferência de dados lentos.|Optimização erros ocorrem quando exceder os destinos escalabilidade de um serviço de armazenamento. O serviço de armazenamento faz isto para se certificar de que nenhuma único cliente ou inquilino pode utilizar o serviço cargo outras pessoas. Para obter mais informações sobre como resolver problemas da sua conta de armazenamento Azure, vá para o [Monitor, diagnosticar e resolver problemas de armazenamento do Windows Azure](storage-monitoring-diagnosing-troubleshooting.md).
|Quando estiver a executar baixo reserva local espaço em disco no <*nome do dispositivo*>.|Tempo de resposta lenta.|10% do total tamanho aprovisionado para <*nome do dispositivo*> está reservado no dispositivo local e está agora a ficar com pouco o espaço reservado. A carga de trabalho no <*nome do dispositivo*> está a gerar uma taxa de vasilha superior ou poderá ter recentemente migrados uma grande quantidade de dados. Isto pode resultar em desempenho reduzido. Considere uma das seguintes ações para resolver este problema:<ul><li>Aumente a largura de banda da nuvem para este dispositivo.</li><li>Reduzir ou mova das cargas de trabalho para outro volume ou partilhar.</li></ul>

### <a name="security-alerts"></a>Alertas de segurança

|Texto do alerta|Evento|Obter mais informações / acções recomendadas|
|:---|:---|:---|
|Palavra-passe para <*nome do dispositivo*> irá expirar em <*número*> dias.|Aviso de palavra-passe.| A palavra-passe expirará no < número < dias. Considere alterar a palavra-passe. Para mais informações, consulte [alterar a palavra-passe de administrador do dispositivo de StorSimple Virtual matriz](storsimple-ova-change-device-admin-password.md).

### <a name="update-alerts"></a>Alertas de actualização

|Texto do alerta|Evento|Obter mais informações / acções recomendadas|
|:---|:---|:---|
|As novas atualizações estão disponíveis para o seu dispositivo.|Atualizações a matriz Virtual StorSimple estão disponíveis.|Pode instalar novas atualizações a partir da página de **Manutenção** .|
|Não foi possível pesquisar as novas atualizações no <*nome do dispositivo*>.|Atualize falha. |Ocorreu um erro durante a instalação das atualizações novas. Pode instalar as atualizações manualmente. Se o problema persistir, contacte o [Suporte da Microsoft](storsimple-contact-microsoft-support.md).|


## <a name="next-steps"></a>Próximos passos

- [Saiba mais sobre a matriz Virtual StorSimple](storsimple-ova-overview.md).


