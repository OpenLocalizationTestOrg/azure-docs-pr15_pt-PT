<properties
   pageTitle="Gerir os volumes de StorSimple (U2) | Microsoft Azure"
   description="Explica como adicionar, modificar, monitorizar e eliminar volumes de StorSimple e como colocar offline se for necessário."
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
   ms.date="10/28/2016"
   ms.author="alkohli" />

# <a name="use-the-storsimple-manager-service-to-manage-volumes-update-2"></a>Utilizar o serviço do Gestor de StorSimple para gerir volumes (Update 2)

[AZURE.INCLUDE [storsimple-version-selector-manage-volumes](../../includes/storsimple-version-selector-manage-volumes.md)]

## <a name="overview"></a>Descrição geral

Neste tutorial explica como utilizar o serviço do Gestor de StorSimple para criar e gerir volumes no dispositivo StorSimple e dispositivo virtual StorSimple com Update 2 instalado.

O serviço do Gestor de StorSimple é uma extensão no portal de clássica do Azure que permite-lhe gerir a sua solução StorSimple a partir de uma interface única web. Para além de gestão de volumes, pode utilizar o serviço do Gestor de StorSimple para criar e gerir serviços StorSimple, ver e gerir dispositivos, ver os alertas e ver e gerir políticas de cópia de segurança e o catálogo de cópia de segurança.

## <a name="volume-types"></a>Tipos de volume

Podem ser StorSimple volumes:

- **Localmente afixados volumes**: dados estes volumes permanecem no local StorSimple dispositivo sempre.
- **Tiered volumes**: dados estes volumes podem ajustar na nuvem.

Um arquivo volume é um tipo de volume em camadas. O tamanho do segmento eliminação de duplicados maior utilizado para volumes arquivo permite que o dispositivo para o qual transferir maiores segmentos de dados para a nuvem. 

Se for necessário, pode alterar o volume tipo a partir do local para camadas ou a partir de camadas como local. Para mais informações, consulte [alterar o tipo de volume](#change-the-volume-type).

### <a name="locally-pinned-volumes"></a>Volumes localmente afixados

Os volumes localmente afixados são totalmente aprovisionados volumes que efetuar e não camada dados na nuvem, assegurando local garantias para dados principais, independentemente da conectividade de nuvem. Em volumes localmente afixados não deduplicated e dados comprimidos; No entanto, são deduplicated instantâneos dos volumes localmente afixados. 

Volumes localmente afixados configurações totalmente; por isso, tem de ter espaço suficiente no seu dispositivo quando de criá-las. Pode aprovisionar o volumes localmente afixados por excesso para um tamanho máximo de 8 TB no dispositivo StorSimple 8100 e de 20 TB no dispositivo 8600. StorSimple reserva o restante espaço local no dispositivo para instantâneos, metadados e processamento de dados. Pode aumentar o tamanho de um volume afixado localmente para o espaço máximo disponível, mas não é possível diminuir o tamanho de um volume uma vez criado.

Quando cria um volume localmente afixado, o espaço disponível para a criação de volumes em camadas é reduzido. O inverso também é verdadeiro: Se tiver volumes em camadas existentes, o espaço disponível para criar localmente afixados volumes será inferior os limites máximos indicados acima. Para mais informações sobre volumes locais, consulte as [Perguntas mais frequentes de volumes de localmente afixados](storsimple-local-volume-faq.md).   

### <a name="tiered-volumes"></a>Volumes em camadas

Em camadas volumes são pequenos aprovisionados volumes na qual os dados acedidos frequentemente permanecem locais no dispositivo e menos dados utilizados frequentemente são automaticamente camados para a nuvem. Aprovisionamento fino é uma tecnologia de Virtualização na qual armazenamento disponível aparece exceda físicos recursos. Em vez de reserva de armazenamento suficiente com antecedência, StorSimple utiliza aprovisionamento fina para alocar espaço apenas suficiente para cumprir requisitos atuais. A natureza flexível de armazenamento na nuvem facilita a esta abordagem porque StorSimple pode aumentar ou diminuir o armazenamento em nuvem para satisfazer necessidades alteração.

Se estiver a utilizar o volume em camadas para os dados de arquivo, selecionar a caixa de verificação **utilizar este volume para menos dados arquivo acedidos frequentemente** altera o tamanho do segmento eliminação de duplicados para o volume para 512 KB. Se não selecionar esta opção, o volume em camadas correspondente irá utilizar um tamanho de fragmento de 64 KB. Um tamanho de fragmento de eliminação de duplicados maior permite que o dispositivo acelerar a transferência de dados de arquivo de grandes dimensões na nuvem.

>[AZURE.NOTE] Arquivo volumes criados com uma versão anterior à atualização 2 StorSimple serão importados como em camadas com a caixa de verificação arquivo selecionada.

### <a name="provisioned-capacity"></a>Capacidade aprovisionada

Consulte a tabela seguinte para capacidade aprovisionada máxima para cada tipo de dispositivo e o volume. (Tenha em atenção que volumes localmente afixados não estão disponíveis num dispositivo virtual.)

|             | Tamanho máximo do volume em camadas | Máximo localmente afixados o tamanho de volume |
|-------------|----------------------------|------------------------------------|
| **Dispositivos físicos** |       |       |
| 8100                 | 64 TB | 8 TB |
| 8600                 | 64 TB | 20 TB |
| **Dispositivos virtuais**  |       |       |
| 8010                | 30 TB | N/D   |
| 8020               | 64 TB | N/D   |

## <a name="the-volumes-page"></a>A página de Volumes

A página de **Volumes** permite-lhe gerir os volumes de armazenamento configurações no dispositivo Microsoft Azure StorSimple para sua iniciadores (servidores). Apresenta a lista de volumes no seu dispositivo StorSimple.

 ![Página de volumes](./media/storsimple-manage-volumes-u2/VolumePage.png)

Um volume é constituído por uma série de atributos:

- **Nome do volume** – um nome descritivo que têm de ser exclusivos e ajuda a identificar o volume. Este nome também é utilizado em relatórios de monitorização quando aplica um filtro num volume específico.

- **Estado** – pode ser online ou offline. Se um volume está offline, não está visível para os iniciadores (servidores) que são permitidos acesso ao utilizar o volume.

- **Capacidade** – Especifica a quantidade total de dados que podem ser armazenados pelo iniciador (server). Os volumes localmente afixados totalmente configurações e armazenadas no dispositivo StorSimple. Configurações pequenos volumes em camadas e os dados são deduplicated. Com pouco aprovisionados volumes, dispositivo previamente não atribuir capacidade de armazenamento física interna ou na nuvem, de acordo com capacidade de volume configurado. A capacidade de volume é atribuída e consumida a pedido.

- **Tipo de** – indica se o volume é **Tiered** (predefinido) ou **localmente afixados**.

- **Cópia de segurança** – indica se existe uma política de cópia de segurança de predefinida para o volume.

- **Acesso** – Especifica os iniciadores (servidores) que são permitidos acesso a este volume. Iniciadores que não sejam membros de registo de controlo de acesso (ACR) que está associado com o volume não verá o volume.

- **Monitorização** – Especifica a permissão ou não está a ser monitorizado um volume. Um volume terá monitorização ativada por predefinição, quando é criado. Monitorização irá, no entanto, desativada para um clonar volume. Para ativar a monitorização de um volume, siga as instruções no [Monitor de um volume](#monitor-a-volume). 

Utilize as instruções neste tutorial, para efetuar as seguintes tarefas:

- Adicionar um volume 
- Modificar um volume 
- Alterar o tipo de volume
- Eliminar um volume 
- Colocar um volume offline 
- Monitorizar um volume 

## <a name="add-a-volume"></a>Adicionar um volume

[Criou um volume](storsimple-deployment-walkthrough-u2.md#step-6-create-a-volume) durante a implementação da sua solução StorSimple. Adicionar um volume é um procedimento semelhante.

#### <a name="to-add-a-volume"></a>Para adicionar um volume

1. Na página de **dispositivos** , selecione o dispositivo, faça duplo clique nele e, em seguida, clique no separador de **Contentores de Volume** .

2. Selecione um contentor de volume a partir da lista e faça duplo clique para aceder a volumes associados com o contentor.

3. Clique em **Adicionar** na parte inferior da página. Adicionar um Assistente de volume é iniciado.

     ![Assistente de volume definições básicas para adicionar](./media/storsimple-manage-volumes-u2/TieredVolEx.png)

4. No Assistente adicionar um volume, em **Definições básicas**, faça o seguinte:

  1. Fornece um **nome** para o volume.
  2. Selecione um **Tipo de utilização** a partir da lista pendente. Das cargas de trabalho que necessitam de dados para ser localmente no dispositivo sempre disponível, selecione **Localmente afixados**. Para todos os outros tipos de dados, selecione **Tiered**. (**Tiered** é a predefinição).
  3. Se tiver seleccionado **Tiered** no passo 2, pode selecionar a caixa de verificação **utilizar este volume para menos dados arquivo acedidos frequentemente** para configurar um arquivo volume.
  4. Introduza a **Capacidade de aprovisionada** para o volume GB ou TB. Consulte o artigo [Provisioned capacidade](#provisioned-capacity) para os tamanhos máximos para cada tipo de dispositivo e o volume. Observe a **Capacidade disponível** para determinar a quantidade de armazenamento é realmente disponível no seu dispositivo.

5. Clique no ícone de seta![Ícone de seta](./media/storsimple-manage-volumes-u2/HCS_ArrowIcon.png). Se estiver a configurar um volume localmente afixado, irá ver a seguinte mensagem.

    ![Alterar a mensagem de tipo de Volume](./media/storsimple-manage-volumes-u2/LocalVolEx.png)
   
5. Clique no ícone de seta ![ícone de seta](./media/storsimple-manage-volumes-u2/HCS_ArrowIcon.png)novamente para ir para a página de **Definições adicionais** .

    ![Adicionar definições adicionais do Assistente de Volume](./media/storsimple-manage-volumes-u2/AddVolume2.png)<br>

6. Em **Definições adicionais**, adicione um novo registo de controlo de acesso (ACR):
  
  1. Selecione um registo de controlo de acesso (ACR) a partir da lista pendente. Em alternativa, pode adicionar uma nova ACR. ACRs determinar quais anfitriões podem aceder a sua volumes através da correspondência de anfitrião do IQN com que listado no registo. Se não especificar um ACR, irá ver a seguinte mensagem.

        ![Especificar ACR](./media/storsimple-manage-volumes-u2/SpecifyACR.png)

  2. Recomendamos que seleciona a caixa de verificação **Ativar uma cópia de segurança predefinidos para este volume** .
  3. Clique no ícone de verificação ![Ícone de verificação](./media/storsimple-manage-volumes-u2/HCS_CheckIcon.png) Para criar o volume com as definições de especificado.

O volume do novo está agora pronto para utilizar.

>[AZURE.NOTE] Se criar um localmente afixado volume e, em seguida, criar outro volume localmente afixado imediatamente posteriormente, a criação de volume tarefas são executadas sequencialmente. A tarefa de criação de volume primeira tem de concluir antes de poderem começar a tarefa de criação de volume seguinte.

## <a name="modify-a-volume"></a>Modificar um volume

Modifica um volume quando precisar de para expandi-lo ou alterar os anfitriões que o volume de acesso.

> [AZURE.IMPORTANT] 
>
> - Se modificar o tamanho do volume no dispositivo, o tamanho do volume tem de ser alteradas no anfitrião do também. 
> - Os passos do lado do anfitrião descritos aqui são para o Windows Server 2012 (2012R2). Procedimentos para Linux ou outros sistemas operativos de anfitrião serão diferentes. Consulte as instruções do sistema operativo anfitrião quando modificar o volume num sistema anfitrião com outro sistema operativo. 

#### <a name="to-modify-a-volume"></a>Para modificar um volume

1. Na página de **dispositivos** , selecione o dispositivo, faça duplo clique nele e, em seguida, clique no separador de **Contentores de Volume** .

2. Selecione um contentor de volume a partir da lista e faça duplo clique para visualizar os volumes associados com o contentor.

3. Selecione um volume e, na parte inferior da página, clique em **Modificar**. Inicia o Assistente de volume modificar.

4. No Assistente de volume de modificar, em **Definições básicas**, pode fazer o seguinte:

  - Edite o **nome**.
  - Converter o **Tipo de utilização** de localmente afixados para camadas ou a partir de camadas para localmente afixados (consulte [alterar o tipo de volume](#change-the-volume-type) para obter mais informações).
  - Aumente a **capacidade de aprovisionado**. Só pode ser aumentado a **Capacidade de aprovisionado** . Não é possível diminuir um volume após ter sido criada.

5. Em **Definições adicionais**, pode modificar ACR, desde que o volume está offline. Se o volume está online, terá de colocá-lo offline pela primeira vez. Consulte os passos na [demorar um volume offline](#take-a-volume-offline) antes de modificar o ACR.

    > [AZURE.NOTE] Não é possível alterar a opção **Ativar uma cópia de segurança predefinidos** para o volume.

6. Guardar as alterações ao clicar no ícone de verificação ![ícone de verificação](./media/storsimple-manage-volumes-u2/HCS_CheckIcon.png). Portal clássico do Azure será apresentada uma mensagem de volume atualizar. Esta será apresentada uma mensagem de êxito quando o volume foi atualizado com êxito.

7. Se são expandir um volume, execute os seguintes passos no seu computador anfitrião do Windows:

   1. Aceda a **Gestão de computador** ->**Gestão do disco**.
   2. Botão direito do rato **Gestão do disco** e selecione **Voltar a analisar discos**.
   3. Na lista de discos, selecione o volume que atualizou, botão direito do rato e, em seguida, selecione **Expandir Volume**. O Assistente de expandir Volume é iniciado. Clique em **seguinte**.
   4. Conclua o assistente, aceitar os valores predefinidos. Depois do assistente estiver concluído, o volume deve mostrar o aumento do tamanho.

    >[AZURE.NOTE] Se expandir um localmente afixado volume e, em seguida, expanda outro localmente afixada volume imediatamente posteriormente, as tarefas de expansão de volume sequencialmente executar. A tarefa de expansão de volume primeira tem de concluir antes de poderem começar a tarefa de expansão de volume seguinte.

![Vídeo disponível](./media/storsimple-manage-volumes-u2/Video_icon.png) **vídeo disponível**

Para ver um vídeo que demonstra como expandir um volume, clique em [aqui](https://azure.microsoft.com/documentation/videos/expand-a-storsimple-volume/).

## <a name="change-the-volume-type"></a>Alterar o tipo de volume

Pode alterar o tipo de volume a partir de camadas para localmente afixados ou a partir do localmente afixados para em camadas. No entanto, esta conversão não deve ser uma frequente ocorrência. Alguns dos motivos para converter um volume a partir de camadas para localmente afixados são:

- Locais garantias relativamente ao desempenho e disponibilidade de dados
- Eliminação de nuvem latências e problemas de conectividade de nuvem.

Normalmente, estes são pequenas volumes existentes ao qual pretende aceder a frequentemente. Um volume localmente afixado totalmente está aprovisionado quando é criado. Se estiver a converter um volume em camadas para um volume localmente afixado, StorSimple verifica que tem espaço suficiente no seu dispositivo antes de iniciar a conversão. Se tem espaço suficiente, receberá um erro e a operação será cancelada. 

> [AZURE.NOTE] Antes de começar uma conversão de camadas para localmente afixados, certifique-se de que considere os requisitos de espaço da sua outras das cargas de trabalho. 

Poderá pretender alterar um volume localmente afixado para um volume em camadas se precisar de espaço adicional para aprovisionar outros volumes. Quando converte o volume localmente afixado para camadas, a capacidade disponível na dispositivo aumenta o tamanho da capacidade lançamento. Se os problemas de conectividade impedirem a conversão de um volume do tipo de local com o tipo de em camadas, o volume local irá apresentar propriedades de um volume em camadas até que seja concluída a conversão. Isto acontece porque alguns dados poderão ter passado para a nuvem. Estes dados espalhados irão continuar a ocupar espaço no local no dispositivo que não pode ser disponibilizado até que a operação seja reiniciada e concluída.

>[AZURE.NOTE] Converter um volume pode demorar algum tempo e não pode cancelar uma conversão depois de ser iniciado. O volume permanece online durante a conversão e que pode tomar cópias de segurança, mas não é possível expandir ou restaurar o volume enquanto a conversão.  

Conversão de uma em camadas para um volume localmente afixado afetam o desempenho de dispositivo. Além disso, os seguintes fatores poderão aumentar o tempo necessário para concluir a conversão:

- Existe largura de banda insuficiente.

- Não existe nenhuma cópia de segurança atual.

Para minimizar os efeitos que poderão ter seguintes fatores:

- Reveja a largura de banda limitação políticas e certifique-se de que está disponível uma largura de banda Mbps dedicada 40.
- Agende a conversão para das horas de expediente.
- Tire um instantâneo de nuvem antes de começar a conversão.

Se estiver a converter vários volumes (suporte diferentes cargas de trabalho), deve prioridade a conversão de volume para que mais elevados volumes de prioridade são convertidos em primeiro lugar. Por exemplo, deverá converter volumes que alojam máquinas virtuais (VMs) ou volumes com cargas de trabalho de SQL antes de o converter volumes com cargas de trabalho de partilha de ficheiro.

#### <a name="to-change-the-volume-type"></a>Para alterar o tipo de volume

1. Na página de **dispositivos** , selecione o dispositivo, faça duplo clique nele e, em seguida, clique no separador de **Contentores de Volume** .

2. Selecione um contentor de volume a partir da lista e faça duplo clique para visualizar os volumes associados com o contentor.

3. Selecione um volume e, na parte inferior da página, clique em **Modificar**. Inicia o Assistente de volume modificar.

4. Na página **Definições básicas** , altere o tipo de utilização selecionando o novo tipo da lista pendente **Tipo de utilização** .

    - Se está a alterar o tipo de para **localmente afixados**, StorSimple irá Verifique se existe uma capacidade suficiente.
    - Se está a alterar o tipo de **Tiered** e este volume será utilizado para os dados de arquivo, selecione a caixa de verificação **utilizar este volume para menos dados arquivo acedidos frequentemente** .

        ![Caixa de verificação do arquivo](./media/storsimple-manage-volumes-u2/ModifyTieredVolEx.png)

5. Clique no ícone de seta ![ícone de seta](./media/storsimple-manage-volumes-u2/HCS_ArrowIcon.png) para ir para a página de **Definições adicionais** . Se estiver a configurar um volume localmente afixado, é apresentada a seguinte mensagem.

    ![Alterar a mensagem de tipo de Volume](./media/storsimple-manage-volumes-u2/ModifyLocalVolEx.png)

6. Clique no ícone de seta ![ícone de seta](./media/storsimple-manage-volumes-u2/HCS_ArrowIcon.png) novamente para continuar.

7. Clique no ícone de verificação ![Ícone de verificação](./media/storsimple-manage-volumes-u2/HCS_CheckIcon.png) para iniciar o processo de conversão. Portal do Azure será apresentada uma mensagem de volume atualizar. Esta será apresentada uma mensagem de êxito quando o volume foi atualizado com êxito.

## <a name="take-a-volume-offline"></a>Colocar um volume offline

Poderá ter de colocar um volume offline quando está a planear modificá-lo ou eliminá-la. Quando um volume está offline, não está disponível para o acesso de leitura e escrita. Terá de ter o volume offline no anfitrião do e no dispositivo. 

#### <a name="to-take-a-volume-offline"></a>Para colocar um volume offline

1. Certifique-se de que o volume em questão não é utilizado antes de efetuá-lo offline.

2. Tomar o volume offline no anfitrião do primeiro. Isto elimina quaisquer potencial risco de danos nos dados no volume. Para obter passos específicos, consulte as instruções do seu sistema operativo do anfitrião.

3. Depois do anfitrião está offline, siga o volume no dispositivo offline executando os seguintes passos:

  1. Na página de **dispositivos** , selecione o dispositivo, faça duplo clique nele e, em seguida, clique no separador de **Contentores de Volume** . No separador **Contentores de Volume** lista num formato tabular todos os contentores de volume que estão associados com o dispositivo.
  2. Selecione um contentor de volume e clique na mesma para apresentar a lista de todos os volumes dentro do contentor.
  3. Selecione um volume e clique em **colocar offline**.
  4. Quando lhe for pedido de confirmação, clique em **Sim**. O volume deverá agora estar offline.

    Depois de um volume está offline, a opção **Colocar Online** fica disponível.

> [AZURE.NOTE] O comando **Colocar Offline** envia um pedido para o dispositivo para colocar o volume offline. Se anfitriões ainda estiver a utilizar o volume, isto resulta em ligações quebradas, mas demorar o volume offline não irá falhar. 

## <a name="delete-a-volume"></a>Eliminar um volume

> [AZURE.IMPORTANT] Pode eliminar um volume apenas se estiver offline.

Conclua os passos seguintes para eliminar um volume.

#### <a name="to-delete-a-volume"></a>Para eliminar um volume

1. Na página de **dispositivos** , selecione o dispositivo, faça duplo clique nele e, em seguida, clique no separador de **Contentores de Volume** .

2. Selecione o contentor de volume que tem o volume que pretende eliminar. Clique no contentor de volume para aceder à página de **Volumes** .

3. Todos os volumes associados este contentor são apresentados num formato tabular. Verifique o estado do volume que pretende eliminar. Se o volume que pretende eliminar não está offline, colocá-lo offline em primeiro lugar, seguir os passos no [tomar um volume offline](#take-a-volume-offline).

4. Depois do volume está offline, clique em **Eliminar** na parte inferior da página.

5. Quando lhe for pedido de confirmação, clique em **Sim**. O volume agora será eliminado e não a página de **Volumes** irá mostrar a lista actualizada de volumes dentro do contentor.

    >[AZURE.NOTE]Se eliminar um volume localmente afixado, o espaço disponível para novos volumes pode não ser atualizado imediatamente. O serviço do Gestor de StorSimple atualiza o local espaço disponível periodicamente. Sugerimos que aguarde alguns minutos antes de tentar criar o novo volume.<br> Para além disso, se eliminar um localmente afixado volume e, em seguida, eliminar outro localmente afixada volume imediatamente posteriormente, a eliminação de volume tarefas são executadas sequencialmente. A tarefa de eliminação de volume primeira tem de concluir antes de poderem começar a tarefa de eliminação de volume seguinte.
 
## <a name="monitor-a-volume"></a>Monitorizar um volume

Monitorização do volume permite-lhe recolher posso/the-as estatísticas relacionadas com para um volume. Monitorização está ativada por predefinição para os 32 pela primeira vez volumes que criar. Monitorização de volumes adicionais é desativada por predefinição. Monitorização de volumes clonados também é desativada por predefinição.

Execute os passos seguintes para ativar ou desativar a monitorização para um volume.

#### <a name="to-enable-or-disable-volume-monitoring"></a>Para ativar ou desativar a monitorização de volume

1. Na página de **dispositivos** , selecione o dispositivo, faça duplo clique nele e, em seguida, clique no separador de **Contentores de Volume** .

2. Selecione o contentor de volume em que reside o volume e, em seguida, clique no contentor de volume para aceder à página de **Volumes** .

3. Todos os volumes associados este contentor estão listados na apresentação em tabela. Clique em e selecione o volume ou clonar volume.

4. Na parte inferior da página, clique em **Modificar**.

5. No Assistente de Volume modificar, em **Definições básicas**, selecione **Ativar** ou **desativar** a partir da lista pendente de **monitorização** .

## <a name="next-steps"></a>Próximos passos

- Saiba como [clonar um volume StorSimple](storsimple-clone-volume.md).

- Saiba como [utilizar o serviço do Gestor de StorSimple para administrar o seu dispositivo StorSimple](storsimple-manager-service-administration.md).

 
