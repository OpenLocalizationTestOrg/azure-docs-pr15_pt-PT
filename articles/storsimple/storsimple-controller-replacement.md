<properties 
   pageTitle="Substituir um controlador de dispositivo StorSimple | Microsoft Azure"
   description="Explica como remover e substituir ou em ambos os módulos controlador no seu dispositivo StorSimple."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="08/17/2016"
   ms.author="alkohli" />

# <a name="replace-a-controller-module-on-your-storsimple-device"></a>Substituir um módulo de controlador no seu dispositivo StorSimple

## <a name="overview"></a>Descrição geral

Neste tutorial explica como remover e substituir ou em ambos os módulos controlador num dispositivo StorSimple. Também fala sobre a lógica subjacente para os cenários de substituição do controlador único e duplo.

>[AZURE.NOTE] Antes de efetuar substituição controlador, recomendamos que atualize sempre o firmware controlador para a versão mais recente.
>
>Para impedir que danos ao seu dispositivo StorSimple, não ejecte o controlador de até que os LEDs são apresentadas como um dos seguintes procedimentos:
>
>- Todas as luzes estiverem desligado.
>- LED 3, ![ícone de marca de verificação verde](./media/storsimple-controller-replacement/HCS_GreenCheckIcon.png), e ![ícone de cruz vermelha](./media/storsimple-controller-replacement/HCS_RedCrossIcon.png) são piscar, e LED 0 e LED 7 são **Sucessivamente**.

A tabela seguinte mostra os cenários de substituição do controlador suportado.

|Maiúsculas/minúsculas|Cenário de substituição|Procedimento aplicável|
|:---|:-------------------|:-------------------|
|1|Um controlador é num estado falhado, o outro controlador é estável e ativa.|[Substituição único controlador](#replace-a-single-controller), que descreva a [lógica atrás de substituição único controlador](#single-controller-replacement-logic), bem como os [passos de substituição](#single-controller-replacement-steps).|
|2|Ambos os controladores de tem ocorrido uma falha e necessitam de substituição. Quadro, discos, and.disk inclusão está em bom estado.|[Substituição de controlador duplo](#replace-both-controllers), que descreva a [lógica atrás de substituição controlador duplo](#dual-controller-replacement-logic), bem como os [passos de substituição](#dual-controller-replacement-steps). |
|3|Controladores do mesmo dispositivo ou a partir de diferentes dispositivos são trocadas. O quadro, discos e anexos de disco são saudáveis.|Será apresentada uma mensagem de alerta de erro de correspondência ranhura.|
|4|Um controlador está em falta e o outro controlador de falha.|[Substituição de controlador duplo](#replace-both-controllers), que descreva a [lógica atrás de substituição controlador duplo](#dual-controller-replacement-logic), bem como os [passos de substituição](#dual-controller-replacement-steps).|
|5|Um ou ambos os controladores de tem falhado. Não é possível aceder ao dispositivo através da consola série ou o Windows PowerShell remoto.|[Contactar o suporte do Microsoft](storsimple-contact-microsoft-support.md) para obter um procedimento de substituição do controlador manual.|
|6|Os controladores de tem uma versão de compilação diferentes, que pode ser devido a:<ul><li>Controladores de tem uma versão diferente do software.</li><li>Controladores de tem uma versão de firmware diferente.</li></ul>|Se as versões de software do controlador forem diferentes, a lógica de substituição Deteta que e atualiza a versão do software no controlador de substituição.<br><br>Se as versões de firmware controlador são diferentes e a versão antiga de firmware é **não** automaticamente upgradeable, uma mensagem de alerta irão aparecer no portal do Azure clássico. Deve procurar atualizações e instale as atualizações de firmware.</br></br>Se as versões de firmware controlador são diferentes e a versão de firmware antigo é automaticamente upgradeable, a lógica de substituição do controlador detetar isto e, depois do controlador é iniciado, o firmware será automaticamente atualizado.|

Tem de remover um módulo de controlador se tiver falhado. Podem falhar ou em ambos os controlador módulos, que pode resultar numa substituição de controlador de única ou substituição de controlador duplo. Para obter os procedimentos de substituição e a lógica atrás-las, consulte o seguinte:

- [Substituir um único controlador](#replace-a-single-controller)
- [Substituir ambos os controladores](#replace-both-controllers)
- [Remover um controlador de](#remove-a-controller)
- [Inserir um controlador](#insert-a-controller)
- [Identificar o controlador de ativo no seu dispositivo](#identify-the-active-controller-on-your-device)

>[AZURE.IMPORTANT] Antes de remover e substituir um controlador, reveja as informações de segurança em [substituição de componente de hardware StorSimple](storsimple-hardware-component-replacement.md).

## <a name="replace-a-single-controller"></a>Substituir um único controlador

Quando um dos dois controladores no dispositivo Microsoft Azure StorSimple falhou, está danificada ou em falta, tem de substituir um único controlador. 

### <a name="single-controller-replacement-logic"></a>Lógica de substituição do controlador de única

Substituição único controlador, o utilizador deve pela primeira vez remove o controlador de falhado. (O controlador de restante no dispositivo é o controlador de active.) Quando insere o controlador de substituição, ocorrem as seguintes ações:

1. O controlador de substituição inicia imediatamente comunicar com o dispositivo StorSimple.

2. Um instantâneo do disco rígido virtual (VHD) para o controlador de ativo é copiado no controlador de substituição.

3. O instantâneo é modificado para que quando inicia o controlador de substituição a partir deste VHD, será reconhecido como um controlador de espera.

4. Quando as modificações estiverem concluídas, o controlador de substituição será iniciado como o controlador de espera.

5. Quando os controladores de ambas estiverem em execução, cluster é recebida online.

### <a name="single-controller-replacement-steps"></a>Passos de substituição do controlador de única

Conclua os passos seguintes se um dos controladores no seu dispositivo do Microsoft Azure StorSimple falhar. (O controlador de outros tem de ser ativa e a ser executado. Se ambos os controladores falharem ou mau funcionamento, avance para [os passos de substituição do controlador duplo](#dual-controller-replacement-steps).)

>[AZURE.NOTE] Pode demorar 30 – 45 minutos para o controlador de reiniciar e completamente recuperar o procedimento de substituição do controlador de única. O tempo total para todo o processo, incluindo como anexar os cabos é aproximadamente a 2 horas.

#### <a name="to-remove-a-single-failed-controller-module"></a>Para remover um módulo de falha de controlador única

1. No portal do Azure clássico, vá para o serviço do Gestor de StorSimple, clique no separador **dispositivos** e, em seguida, clique no nome do dispositivo que pretende monitorizar.

2. Aceda a **manutenção > Estado de Hardware**. O estado do controlador de 0 ou 1 controlador deve ser vermelho, que indica uma falha.

    >[AZURE.NOTE] O controlador de falhado de substituição único controlador é sempre um controlador de espera.

3. Utilize figura 1 e a tabela seguinte para localizar o módulo do controlador falhar.  

    ![Inserção de módulos de inclusão principal de dispositivo](./media/storsimple-controller-replacement/IC740994.png)

    **Figura 1** Verso StorSimple dispositivo

  	|Etiqueta|Descrição|
  	|:----|:----------|
  	|1|PCM 0|
  	|2|PCM 1|
  	|3|Controlador de 0|
  	|4|Controlador de 1|

4. No controlador de falhado, remova todos os cabos de rede ligada as portas de dados. Se estiver a utilizar um modelo de 8600, também remova os cabos de SA que ligue o controlador de para o controlador de EBOD.

5. Siga os passos em [Remover um controlador](#remove-a-controller) para remover o controlador de falhado. 

6. Instale a substituição de fábrica do mesmo na ranhura mesmo a partir do qual foi removido o controlador de falhado. Isto accionadores a lógica de substituição do controlador de única. Para mais informações, consulte o artigo [lógica de substituição do controlador de única](#single-controller-replacement-logic).

7. Enquanto a lógica de substituição único controlador que progride em segundo plano, voltar a ligar os cabos de. Tomar cuidado para ligar todos os cabos exatamente da mesma forma que estava ligados antes da substituição.

8. Depois do controlador de ser reiniciado, verifique o **Estado do controlador** e o **Estado de Cluster** no portal do Azure clássico para confirmar que o controlador de seja novamente para um estado Saudável e em modo de espera.

>[AZURE.NOTE] Se estiver a monitorizar o dispositivo através da consola de série, poderá ver várias reinicia enquanto está a recuperar o controlador do processo de substituição. Quando é apresentado no menu de consola série, em seguida, sabe que a substituição está concluída. Se o menu não aparecer nas duas horas do início a substituição de controlador, utilize a [contactar o suporte da Microsoft](storsimple-contact-microsoft-support.md).

## <a name="replace-both-controllers"></a>Substituir ambos os controladores

Quando ambos os controladores no dispositivo Microsoft Azure StorSimple tem ocorrido uma falha, são disfunção ou estão em falta, tem de substituir ambos os controladores. 

### <a name="dual-controller-replacement-logic"></a>Lógica de substituição do controlador duplo

Em substituição de controlador duplo, primeiro, remova ambos os controladores de falhadas e, em seguida, inserir substituições. Quando são inseridos os controladores de duas substituição, ocorrem as seguintes ações:

1. O controlador de substituição ranhura 0 verifica o seguinte procedimento:
 
   1. É-lo utilizando as versões atuais dos firmware e software?

   2. Trata-se de uma parte do cluster?

   3. É o controlador de ponto a ponto em execução e é agrupadas?
                            
    Se nenhuma destas condições for verdadeira, o controlador de procura a cópia de segurança mais recente diária (localizada na **nonDOMstorage** na unidade S). O controlador de copia o instantâneo mais recente do VHD a cópia de segurança.

2. O controlador de ranhura 0 utiliza o instantâneo para si próprio de imagem.

3. Entretanto, o controlador de ranhura 1 espera para controlador de 0 para concluir o processamento de imagens e iniciar.

4. Depois de inicia o controlador de 0, o controlador de 1 Deteta o cluster criado por controlador de 0, accionadores a lógica de substituição do controlador de única. Para mais informações, consulte o artigo [lógica de substituição do controlador de única](#single-controller-replacement-logic).

5. Posteriormente, ambos os controladores irão estar a executar e cluster ficará online.

>[AZURE.IMPORTANT] Após a substituição controlador duplo, depois do dispositivo StorSimple está configurado, é essencial que tirar um manual de cópia de segurança do dispositivo. Cópias de segurança de configuração do dispositivo diárias não são acionou até ter decorrido 24 horas. Trabalhar com o [Suporte da Microsoft](storsimple-contact-microsoft-support.md) para criar um manual de cópia de segurança do seu dispositivo.

### <a name="dual-controller-replacement-steps"></a>Passos de substituição do controlador duplo

Este fluxo de trabalho é necessário quando ambos os controladores no seu dispositivo do Microsoft Azure StorSimple tem ocorrido uma falha. Isto pode acontecer no Centro de dados nos quais o sistema de refrigeração deixa de funcionar e como resultado, ambos os controladores falharem dentro de um breve período de tempo. Dependendo se o dispositivo StorSimple está ligado ou desligado e, se estiver a utilizar um 8600 ou um modelo de 8100, um conjunto diferente de passos é necessário.

>[AZURE.IMPORTANT] Pode demorar 45 minutos para 1 hora para o controlador reiniciar e completamente recuperar a partir de um procedimento de substituição do controlador duplo. O tempo total para todo o processo, incluindo como anexar os cabos é de cerca 2.5 horas.

#### <a name="to-replace-both-controller-modules"></a>Para substituir ambos os módulos controlador

1. Se o dispositivo estiver desativado, ignorar este passo e prossiga para o passo seguinte. Se o dispositivo está ativado, desative o dispositivo.
                                        
    1. Se estiver a utilizar um modelo de 8600, desative a inclusão principal primeiro e, em seguida, desativar a inclusão EBOD.

    2. Aguarde até que o dispositivo tem encerrar completamente. Todos os LEDs no fundo do dispositivo será desativada.

2. Remova todos os cabos de rede que estão ligados às portas de dados. Se estiver a utilizar um modelo de 8600, também remova os cabos de SA que se ligam a inclusão principal para a inclusão EBOD.

3. Remova ambos os controladores do dispositivo StorSimple. Para mais informações, consulte o artigo [Remover um controlador de](#remove-a-controller).

4. Insira a substituição de fábrica do controlador de 0 pela primeira vez e, em seguida, insira controlador 1. Para mais informações, consulte o artigo [Inserir um controlador](#insert-a-controller). Isto accionadores a lógica de substituição do controlador duplo. Para mais informações, consulte o artigo [lógica de substituição do controlador duplo](#dual-controller-replacement-logic).

5. Enquanto a lógica de substituição do controlador que progride em segundo plano, voltar a ligar os cabos de. Tomar cuidado para ligar todos os cabos exatamente da mesma forma que estava ligados antes da substituição. Consulte as instruções detalhadas para o seu modelo o cabo secção do seu dispositivo de [instalar o seu dispositivo StorSimple 8100](storsimple-8100-hardware-installation.md) ou [instalar o seu dispositivo StorSimple 8600](storsimple-8600-hardware-installation.md).

6. Ative o dispositivo StorSimple. Se estiver a utilizar um modelo de 8600:

    1. Certifique-se de que a inclusão EBOD está ativado primeiro.

    2. Aguarde até que a inclusão EBOD está em execução.

    3. Ative a inclusão principal.

    4. Depois do primeiro controlador de ser reiniciado e está em bom estado Estado, o sistema irá estar em execução.

    >[AZURE.NOTE] Se estiver a monitorizar o dispositivo através da consola de série, poderá ver várias reinicia enquanto está a recuperar o controlador do processo de substituição. Quando aparece o menu série console, em seguida, sabe que a substituição está concluída. Se o menu não aparecer no prazo de iniciar a substituição do controlador de 2,5 horas, utilize a [contactar o suporte da Microsoft](storsimple-contact-microsoft-support.md).

## <a name="remove-a-controller"></a>Remover um controlador de

Utilize o seguinte procedimento para remover um módulo de cablagem controlador do seu dispositivo StorSimple.

>[AZURE.NOTE] São as seguintes ilustrações para controlador de 0. Para controlador de 1, estes seriam ser revertidas.

#### <a name="to-remove-a-controller-module"></a>Para remover um módulo de controlador

1. Perceber bloqueios o módulo entre o seu miniatura e indicador.

2. Encaixar levemente a miniatura do indicador em conjunto para libertar bloqueios de controlador andar.

    ![Libertar bloqueios de controlador](./media/storsimple-controller-replacement/IC741047.png)

    **Figura 2** Libertar bloqueios de controlador

2. Utilize o fecho como uma alça de para o diapositivo o controlador de fora do quadro.

    ![Controlador de deslizante fora do quadro](./media/storsimple-controller-replacement/IC741048.png)

    **Figura 3** Deslizante o controlador de fora do quadro

## <a name="insert-a-controller"></a>Inserir um controlador

Utilize o seguinte procedimento para instalar um módulo de controlador fornecido de fábrica do mesmo depois de remover um módulo cablagem a partir do seu dispositivo StorSimple.

#### <a name="to-install-a-controller-module"></a>Para instalar um módulo de controlador

1. Verifique se existir dano qualquer para os conectores de interface. Não instale o módulo se qualquer um do PIN de conexão são danificados ou falsificados.

2. Deslize o módulo do controlador para o quadro enquanto o fecho totalmente é disponibilizado. 

    ![Controlador de deslizante para o quadro](./media/storsimple-controller-replacement/IC741053.png)

    **Figura 4** Controlador de deslizante para o quadro

3. Com o módulo controlador inserido, comece a fechar o bloqueios enquanto continua ao módulo do controlador de emissão no quadro. Os bloqueios irão realizam para orientar o controlador de para um local.

    ![Fecho do controlador de fecho](./media/storsimple-controller-replacement/IC741054.png)

    **Figura 5** Os bloqueios de controlador de fecho

4. Já está quando o fecho ajusta-se para o respetivo local. O LED **OK** deverá agora estar no.  

    >[AZURE.NOTE] Pode demorar até 5 minutos para o controlador de e o LED para ativar.

5. Para verificar que a substituição êxito, é apresentado no portal do Azure clássico, aceda a **dispositivos** > **Manutenção** > **Estado do Hardware**e certifique-se de que estão saudáveis controlador de 0 e controlador de 1 (estado é verde).

## <a name="identify-the-active-controller-on-your-device"></a>Identificar o controlador de ativo no seu dispositivo

Existem muitas situações, tal como substituição de registo ou controlador de dispositivo pela primeira vez, que exigem a localizar o controlador de ativo num dispositivo StorSimple. O controlador de ativo processa todas as disco firmware e redes operações. Pode utilizar qualquer um dos seguintes métodos para identificar o controlador de ativo:

- [Utilizar o portal do clássico Azure para identificar o controlador de ativo](#use-the-azure-classic-portal-to-identify-the-active-controller)

- [Utilizar o Windows PowerShell para StorSimple para identificar o controlador de ativo](#use-windows-powershell-for-storsimple-to-identify-the-active-controller)

- [Verifique o dispositivo para identificar o controlador de ativo físico](#check-the-physical-device-to-identify-the-active-controller)

Cada um dos seguintes procedimentos é descrita em seguinte.

### <a name="use-the-azure-classic-portal-to-identify-the-active-controller"></a>Utilizar o portal do clássico Azure para identificar o controlador de ativo

No portal do Azure clássico, navegue para **dispositivos** > **Manutenção**e desloque-se para a secção **controladores** . Aqui pode verificar qual o controlador de está ativo.

![Identificar controlador ativo no portal clássico Azure](./media/storsimple-controller-replacement/IC752072.png)

**Figura 6** Portal clássica Azure a mostrar o controlador de ativo

### <a name="use-windows-powershell-for-storsimple-to-identify-the-active-controller"></a>Utilizar o Windows PowerShell para StorSimple para identificar o controlador de ativo

Quando aceder ao seu dispositivo através da consola de série, é apresentada uma mensagem de faixa. A mensagem de faixa contém informações de dispositivo básicos como o modelo, nome, versão do software instalado e o estado do controlador de que está a aceder. A imagem seguinte mostra um exemplo de uma mensagem de faixa:

![Mensagem de faixa série](./media/storsimple-controller-replacement/IC741098.png)

**Figura 7** Faixa controlador da Mostrar mensagem 0 como ativo

Pode utilizar a mensagem de faixa para determinar se o controlador de que estiver ligado a está ativo nem passiva.

### <a name="check-the-physical-device-to-identify-the-active-controller"></a>Verifique o dispositivo para identificar o controlador de ativo físico

Para identificar o controlador de ativo no seu dispositivo, localize a azul por um INSTRUTOR acima dos dados 5 portas na parte detrás da inclusão principal.

Se este LED está a piscar, o controlador de está ativo e o controlador de outros está em modo de espera. Utilize o seguinte diagrama e tabela como uma ajuda.

![Dispositivo primário inclusão inserção com dataports](./media/storsimple-controller-replacement/IC741055.png)

**Figura 8** Verso inclusão primária com dados portas e protocolos LEDs de monitorização

|Etiqueta|Descrição|
|:----|:----------|
|1-6|DADOS 0 – 5 portas de rede|
|7|LED azul|


## <a name="next-steps"></a>Próximos passos

Saiba mais sobre a [substituição de componente de hardware StorSimple](storsimple-hardware-component-replacement.md).
