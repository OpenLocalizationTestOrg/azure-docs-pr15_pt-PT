<properties
   pageTitle="Gerir os seus modelos de largura de banda StorSimple | Microsoft Azure"
   description="Descreve como gerir modelos de largura de banda de StorSimple, que lhe permitem controlar consumo de largura de banda."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags
   ms.service="storsimple"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/16/2016"
   ms.author="alkohli" />

# <a name="use-the-storsimple-manager-service-to-manage-storsimple-bandwidth-templates"></a>Utilizar o serviço do Gestor de StorSimple para gerir modelos de largura de banda StorSimple

## <a name="overview"></a>Descrição geral

Modelos de largura de banda permitem-lhe configurar a utilização da largura de banda de rede entre várias agendas de hora do dia a camada dados a partir do dispositivo StorSimple para a nuvem.

Com largura de banda limitação agendas, pode:

- Especifique a largura de banda personalizada agendas consoante as utilizações de rede de carga de trabalho.

- Centralizar a gestão e reutilizar as agendas através de vários dispositivos de forma totalmente integrada e fácil.

> [AZURE.NOTE] Esta funcionalidade está disponível apenas para dispositivos físicos StorSimple e não para dispositivos virtuais.

Todos os modelos de largura de banda do seu serviço são apresentados num formato tabular e contêm as seguintes informações:

- **Nome** – um nome exclusivo atribuído para o modelo de largura de banda quando foi criada.

- **Agenda** – o número de agendas contidos num modelo de largura de banda determinado.

- **Utilizado por** – o número de volumes utilizando os modelos de largura de banda.

Utilize StorSimple serviço **Configurar** página do Gestor de no portal do Azure clássico para gerir modelos de largura de banda.

Também pode encontrar informações adicionais para o ajudar a configurar modelos de largura de banda no:

- Perguntas e respostas acerca dos modelos de largura de banda
- Práticas recomendadas para os modelos de largura de banda

## <a name="add-a-bandwidth-template"></a>Adicionar um modelo de largura de banda

Execute os passos seguintes para criar um novo modelo de largura de banda.

#### <a name="to-add-a-bandwidth-template"></a>Para adicionar um modelo de largura de banda

1. Na página Gestor de StorSimple serviço **Configurar** , clique em **modelo de largura de banda de adicionar/editar**.

2. Na caixa de diálogo **Modelo de largura de banda Add/Edit** :

   1. A partir da lista pendente de **modelo** , selecione **Criar novo** para adicionar um novo modelo de largura de banda.
   2. Especifique um nome exclusivo para o modelo de largura de banda.

3. Defina uma **agenda de largura de banda**. Para criar uma agenda:

   1. A partir da lista pendente, selecione os dias da semana que a agenda está configurada para. Pode selecionar vários dias ao selecionar as caixas de verificação localizadas antes dos respetivos dias na lista.
   2. Selecione a opção de **Todo o dia** se é aplicada a agenda para todo o dia. Quando esta opção estiver selecionada, já não pode especificar uma **Hora de início** ou uma **Hora de fim**. Executa a agenda a partir de 12:00 AM para 11:59 PM.
   3. A partir da lista pendente, selecione uma **Hora de início**. Este é quando começará a agenda.
   4. A partir da lista pendente, selecione uma **Hora de fim**. Este é quando deixarão da agenda.

         > [AZURE.NOTE] Agendas sobrepostas não são permitidas. Se as horas de início e de fim irão resultar numa agenda sobreposta, verá uma mensagem de erro para esse efeito.

   5. Especifique a **taxa de largura de banda**. Esta é a largura de banda em Megabits por segundo (Mbps) utilizado pelo dispositivo StorSimple operações que envolvam na nuvem (os carregamentos e transferências). Fornece um número entre 1 e 1.000 para este campo.

   6. Clique no ícone de verificação ![ícone verificação](./media/storsimple-manage-bandwidth-templates/HCS_CheckIcon.png). O modelo que criou será adicionado à lista de modelos de largura de banda na página **Configurar** serviço.

    ![Criar novo modelo de largura de banda](./media/storsimple-manage-bandwidth-templates/HCS_CreateNewBT1.png)

4. Clique em **Guardar** na parte inferior da página e, em seguida, clique em **Sim** quando lhe for pedido para confirmar. Isto irá guardar as alterações de configuração que tenha efetuado.

## <a name="edit-a-bandwidth-template"></a>Editar um modelo de largura de banda

Execute os passos seguintes para editar um modelo de largura de banda.

### <a name="to-edit-a-bandwidth-template"></a>Para editar um modelo de largura de banda

1. Clique em **Adicionar/Editar lista de largura de banda modelo**.

2. Na caixa de diálogo **Modelo de largura de banda Add/Edit** :

   1. A partir da lista pendente de **modelo** , selecione um modelo de largura de banda existente que pretende modificar.
   2. Faça as alterações. (Pode modificar quaisquer definições existentes.)
   3. Clique no ícone de verificação ![Ícone de verificação](./media/storsimple-manage-bandwidth-templates/HCS_CheckIcon.png). Irá ver o modelo modificado na lista de modelos de largura de banda na página configurar serviço.

3. Para guardar as alterações, clique em **Guardar** na parte inferior da página. Clique em **Sim** quando lhe for pedido para confirmar.

> [AZURE.NOTE] Não lhe ser permitido para guardar as alterações se a agenda editada sobrepõe-se uma agenda existente no modelo de largura de banda que está a modificar.

## <a name="delete-a-bandwidth-template"></a>Eliminar um modelo de largura de banda

Execute os seguintes passos para eliminar um modelo de largura de banda.

#### <a name="to-delete-a-bandwidth-template"></a>Para eliminar um modelo de largura de banda

1. Na lista dos modelos de largura de banda do seu serviço de tabela, selecione o modelo que pretende eliminar. Será apresentado um ícone de eliminar (**x**) para a direita representarem do modelo selecionado. Clique no ícone de **x** para eliminar o modelo.

2. Será pedido de confirmação. Clique em **OK** para continuar.

Se o modelo é utilizado por qualquer volume (s), não lhe ser permitido para eliminá-la. Verá uma mensagem de erro indicando que o modelo está em utilização. Caixa de diálogo de mensagem de erro aparecerá aconselhar que devem ser removidas todas as referências para o modelo.

Pode eliminar todas as referências para o modelo ao aceder à página de **Contentores de Volume** e modificar os contentores de volume que utilizam este modelo para que estes utilizam outro modelo ou utilizam uma definição de largura de banda ilimitado ou personalizada. Quando todas as referências foram removidas, pode eliminar o modelo.

## <a name="use-a-default-bandwidth-template"></a>Utilizar um modelo de largura de banda predefinido

Um modelo de largura de banda predefinido é fornecido e é utilizado pelo contentores de volume por predefinição para impor controlos de largura de banda ao aceder na nuvem. O modelo predefinido também serve como uma referência pronta para os utilizadores que criar os seus próprios modelos. Sejam os detalhes deste modelo predefinido:

- **Nome** – noites ilimitados e os fins de semana

- **Agenda** – uma agenda única a partir de segunda a sexta-feira, que se aplica uma taxa de largura de banda de 1 Mbps entre 8: 00 e a hora de dispositivo de 5 PM. A largura de banda está definida para ilimitado para o resto da semana.

O modelo predefinido pode ser editado. A utilização deste modelo (incluindo versões editadas) é registada.

## <a name="create-an-all-day-bandwidth-template-that-starts-at-a-specified-time"></a>Criar um modelo de largura de banda para todo o dia que inicia uma vez especificado

Siga este procedimento para criar uma agenda que inicia uma vez especificado e executa todo o dia. No exemplo, a agenda começa em 9 AM durante a manhã e executa até 9 AM durante a manhã seguinte. É importante ter em atenção que as horas de início e de fim de uma agenda determinada têm ambos de estar contidas na mesma agenda de 24 horas e não é possível abranger vários dias. Se precisar de configurar modelos de largura de banda que abrangem vários dias, terá de utilizar várias agendas (conforme mostrado no exemplo).

#### <a name="to-create-an-all-day-bandwidth-template"></a>Para criar um modelo de largura de banda para todo o dia

1. Crie uma agenda que começa no 9 AM durante a manhã e é executado até à meia-noite.

2. Adicione outra agenda. Configure a segunda agenda para executar a partir de meia-noite até 9 AM durante a manhã.

3. Guarde o modelo de largura de banda.

A agenda composta, em seguida, será iniciado uma vez à sua escolha e executar para todo o dia.

## <a name="questions-and-answers-about-bandwidth-templates"></a>Perguntas e respostas acerca dos modelos de largura de banda

**Q**. O que acontece aos controlos de largura de banda quando se encontra entre as agendas? (Foi terminado uma agenda e outro ainda não tiver iniciado).

**A**. Nestes casos, vai ser ocupados sem controlos de largura de banda. Isto significa que o dispositivo pode utilizar a largura de banda ilimitada quando tiering dados para a nuvem.

**Q**. Pode modificar modelos de largura de banda num dispositivo offline?

**A**. Não conseguir modificar modelos de largura de banda no contentores de volumes se o dispositivo correspondente está offline.

**Q**. Pode editar um modelo de largura de banda associado a um contentor de volume quando os volumes associados estiverem offline?

**A**. Pode modificar um modelo de largura de banda associado um contentor de volume cujos volumes estão offline. Tenha em atenção que quando estão offline volumes, sem dados vai ser camados a partir do dispositivo na nuvem.

**Q**. Pode eliminar um modelo predefinido?

**A**. Embora possa eliminar um modelo predefinido, não é uma boa ideia fazê-lo. A utilização de um modelo predefinido, incluindo versões editadas, é registada. Os dados de controlo estão analisados e ao longo do tempo, são utilizados para melhorar o modelo predefinido.

**Q**. Como pode determinar que os modelos de largura de banda precisa de ser alterada?

**A**. Um dos sinais que precisa de modificar os modelos de largura de banda é quando começar a ver a rede desacelerar ou esbatimento várias vezes num dia. Se tal acontecer, monitorize a rede de armazenamento e a utilização verificando os gráficos de desempenho e/s e débito da rede.

A partir de dados de débito rede, identifique a hora do dia e os contentores de volume em que ocorre o congestionamento de rede. Se isto acontece quando estão a ser camados dados na nuvem (receber estas informações do desempenho e/s para todos os contentores de volume para o dispositivo para o cloud), em seguida, terá de modificar os modelos de largura de banda associados com os contentores de volume.

Depois dos modelos modificados estiverem em utilização, terá de monitorizar a rede novamente para latências significativas. Se estes ainda existirem, em seguida, terá para voltar a visitar os modelos de largura de banda.

**Q**. O que acontece se tem vários contentores de volume no meu dispositivo agenda desse sobreposição mas diferentes limites aplicam-se a cada?

**A**. Imaginemos que tem um dispositivo com 3 contentores de volume. As agendas associadas nestes contentores completamente sobrepõem-se. Para cada um destes contentores, os limites de largura de banda utilizados são 5, 10 e 15 Mbps respetivamente. Quando ocorrem em todas as destes contentores/s ao mesmo tempo, pode ser aplicado ao mínimo dos limites de largura de banda 3: neste caso, como estes pedidos e/s de envio de 5 Mbps partilhar a mesma fila.

## <a name="best-practices-for-bandwidth-templates"></a>Práticas recomendadas para os modelos de largura de banda

Siga estas melhores práticas para o seu dispositivo StorSimple:

- Configure modelos de largura de banda no seu dispositivo para ativar a variável limitação do débito da rede pelo dispositivo em alturas diferentes do dia. Estes modelos de largura de banda quando utilizado com cópia de segurança agendas de forma eficaz podem tirar partido da largura de banda de rede adicionais para operações de nuvem horas de expediente.

- Calcule a largura de banda real necessária para uma implementação determinada com base no tamanho de implementação e o objectivo de tempo de recuperação necessárias (RTO).

## <a name="next-steps"></a>Próximos passos

Saiba mais sobre como [utilizar o serviço do Gestor de StorSimple para administrar o seu dispositivo StorSimple](storsimple-manager-service-administration.md).
