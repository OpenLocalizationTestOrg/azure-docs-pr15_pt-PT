<properties 
    pageTitle="Monitorizar os indicadores de StorSimple | Microsoft Azure" 
    description="Descreve a versão simplificada – blasting díodos (LEDs) e alarmes sonoros utilizados para monitorizar o estado do dispositivo StorSimple."
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
    ms.workload="TBD"
    ms.date="08/18/2016"
    ms.author="alkohli" />

# <a name="use-storsimple-monitoring-indicators-to-manage-your-device"></a>Utilizar StorSimple indicadores de monitorização para gerir o seu dispositivo   

## <a name="overview"></a>Descrição geral

Dispositivo StorSimple inclui light – blasting díodos (LEDs) e alarme que pode utilizar para monitorizar os módulos e estado geral do dispositivo StorSimple. Os indicadores de monitorização podem ser encontrados sobre os componentes de hardware de inclusão primária o dispositivo e a inclusão EBOD. Os indicadores de monitorização podem ser LEDs ou alarmes audíveis.

Existem três Estados de LED utilizados para indicar o estado de um módulo: verde, piscar verde a vermelho laranja ou laranja vermelho.  

- Verde LEDs representam um Estado de funcionamento saudável.  
- Piscar verde para representar de LEDs vermelho laranja a presença de condições não críticos que poderá ser necessário intervenção do utilizador.  
- LED de laranja vermelho indicam que existe uma falha crítica presente no módulo.  

O resto deste artigo descreve os vários LEDs de indicador de monitorização, os seus locais no dispositivo StorSimple, o estado do dispositivo baseado nos Estados LED e qualquer alarmes sonoros associados.

## <a name="front-panel-indicator-leds"></a>Indicador de painel frontal LEDs

Painel frontal, também conhecido como o *Painel de operações* ou o *Painel de ops*, apresenta o estado de agregação de todos os módulos no sistema. Painel frontal é idêntico a StorSimple principal e a inclusão EBOD e é ilustrado abaixo.  

   ![Painel frontal do dispositivo][1]
 
Painel frontal contém os seguintes indicadores:  

1. Botão Desativar som
2. Indicador de alimentação LED (vermelho/verde-laranja)
3. Indicador de falhas de módulo por um INSTRUTOR (no vermelho-laranja/desligado)
4. Indicador de falhas lógicos por um INSTRUTOR (no vermelho-laranja/desligado
5. Visualização de ID de unidade  

A diferença entre o painel frontal principal LEDs para o dispositivo e as que se destinam a inclusão EBOD é o **Número de identificação de unidade de sistema** apresentadas no ecrã de apresentação LED. O ID de unidade predefinido apresentado no dispositivo é **00**, Considerando que o ID de unidade predefinido apresentado na inclusão EBOD é **01**. Esta opção permite-lhe rapidamente diferenciar entre o dispositivo e a inclusão EBOD quando o dispositivo está ativado. Se o seu dispositivo estiver desativado, utilize as informações fornecidas no [Ativar um novo dispositivo](storsimple-turn-device-on-or-off.md#turn-on-a-new-device) para diferenciar o dispositivo a inclusão EBOD.  

## <a name="front-panel-led-status"></a>Painel frontal LED Estado  

Utilize a tabela seguinte para identificar o Estado indicado por LEDs no painel frontal para o dispositivo ou a inclusão EBOD.  

|Power de sistema | Falhas de módulo | Falhas de lógica | Alarme | Estado|
|-------------|---------------|-----------------|-------|-------|
|Vermelho laranja | DESATIVAR     | DESATIVAR | N/D | CA perdida, a funcionar no power cópia de segurança ou CA com e o controlador de módulos foram removidos.|
|Verde | ATIVADO | ATIVADO | N/D | Estado de teste do power do painel OPS no (5s)|
|Verde | DESATIVAR | DESATIVAR | N/D | Power em todas as funções boas|
|Verde | ATIVADO |N/D | Falhas PCM LEDs, falhas de ventoinha LEDs | Qualquer falha PCM, falhas de ventoinha, acima ou abaixo do temperatura|
| Verde | ATIVADO | N/D | Módulo e/s LEDs  | Qualquer falha do controlador módulo|
| Verde | ATIVADO | N/D | N/D | Falhas de lógica de inclusão|
| Verde | Flash | N/D | Estado do módulo por um INSTRUTOR no módulo do controlador. Falhas PCM LEDs, falhas de ventoinha LEDs | Tipo de módulo do controlador desconhecido instalado, falha de bus I2C, o erro de configuração do controlador módulo fundamentais do produto dados (VPD) |

## <a name="power-cooling-module-pcm-indicator-leds"></a>Indicador de módulo (PCM) refrigeração LEDs do Power   

Indicador de módulo (PCM) LEDs refrigeração potência pode encontrar na parte detrás da inclusão principal ou a inclusão EBOD em cada módulo PCM. Este tópico explica como utilizar os seguintes LEDs para monitorizar o estado do seu dispositivo StorSimple.  

- PCM LEDs para a inclusão principal
- PCM LEDs para a inclusão EBOD

## <a name="pcm-leds-for-the-primary-enclosure"></a>PCM LEDs para a inclusão principal  

O dispositivo StorSimple tem um módulo PCM 764W com um baterias adicionais. A ilustração seguinte mostra o painel de LED do dispositivo.  

   ![PCM LEDs na inclusão principal][2]

Legenda de LED:

1. Falha de energia AC
2. Falha de ventoinha
3. Falhas de baterias
4. PCM OK
5. Falha do Centro de dados
6. Carregador boa  

O estado do PCM é indicado no painel de LED. O painel de PCM LED do dispositivo tem de seis LED. Quatro estes LEDs apresentar o estado de alimentação e a ventoinha. O restante LED de dois indicam o estado do módulo, carregador de cópia de segurança no PCM. Pode utilizar as tabelas seguintes para determinar o estado do PCM.  

### <a name="pcm-indicator-leds-for-power-supply-and-fan"></a>Indicador de PCM LEDs para alimentação e ventoinha
| Estado | PCM OK (verde) | Falhas de AC (laranja) | Falhas de ventoinha (laranja) | Falhas de centro de dados (laranja) |
|--------|----------------|-----------------------|------------------|----------------------|
| Sem CA (para inclusão) | DESATIVAR | DESATIVAR | DESATIVAR | DESATIVAR|
| Sem CA (apenas para este PCM) | DESATIVAR | ATIVADO | DESATIVAR | ATIVADO |
| AC apresentar PCM Diante - OK     | DIANTE | DESATIVAR | DESATIVAR | DESATIVAR |
| Falhas PCM (ventoinha falhas) | DESATIVAR | DESATIVAR | ATIVADO | N/D |
| Falhas PCM (por cima do amp sobre tensão, ao longo do atual) | DESATIVAR | ATIVADO | ATIVADO | ATIVADO |
| PCM (ventoinha terminar tolerância) | ATIVADO | DESATIVAR | DESATIVAR | ATIVADO |
| Modo de espera | Piscar | DESATIVAR | DESATIVAR | DESATIVAR |
| Transferência do firmware PCM | DESATIVAR | Piscar | Piscar | Piscar |

### <a name="pcm-indicator-leds-for-the-backup-battery"></a>Indicador PCM LEDs para a cópia de segurança baterias  

| Estado | Carregador boa (verde) | Falhas de baterias (laranja) |
|--------|----------------------|-----------------------|
| Não se encontra presente baterias | DESATIVAR | DESATIVAR |
| Carregador presente e carregada | ATIVADO | DESATIVAR |
| Apuramento de manutenção ou carregar o dispositivo de baterias | Piscar | DESATIVAR |
| Falhas "contornos" baterias (recuperáveis) | DESATIVAR | Piscar |
| Falhas "disco rígida" baterias (que não sejam recuperáveis) | DESATIVAR | ATIVADO |
| Carregador desactivado | Piscar | DESATIVAR |

## <a name="pcm-leds-for-the-ebod-enclosure"></a>PCM LEDs para a inclusão EBOD  

A inclusão EBOD tem um PCM 580W e sem baterias adicionais. O painel de PCM para a inclusão EBOD tem indicador LEDs apenas para as fontes de alimentação e a ventoinha. A ilustração seguinte mostra estes LEDs.

   ![PCM LEDs na inclusão EBOD][3] 
 
Pode utilizar a tabela seguinte para determinar o estado do PCM.  

| Estado | PCM OK (verde) | Falhas de AC (laranja) | Falhas de ventoinha (laranja) | Falhas de centro de dados (laranja) |
|--------|---------------|------------------------|------------------|----------------------|
| Sem CA (para inclusão) | DESATIVAR | DESATIVAR | DESATIVAR | DESATIVAR |
| Sem CA (apenas para este PCM) | DESATIVAR | ATIVADO | DESATIVAR | ATIVADO |
| AC apresentar PCM ON – OK | ATIVADO | DESATIVAR | DESATIVAR | DESATIVAR |
| Falhas PCM (ventoinha falhas) | DESATIVAR | DESATIVAR | ATIVADO | X |
| Falhas PCM (por cima do amp, ao longo do tensão, ao longo do atual | DESATIVAR | ATIVADO | ATIVADO | ATIVADO |
| PCM (ventoinha terminar tolerância) | ATIVADO | DESATIVAR | DESATIVAR | ATIVADO |
| Modelo de espera | Piscar | DESATIVAR | DESATIVAR | DESATIVAR |
| Transferência do firmware PCM | DESATIVAR | Piscar | Piscar | Piscar |

## <a name="controller-module-indicator-leds"></a>Indicador de módulo do controlador LEDs  

O dispositivo StorSimple contém LEDs para o controlador de principal e os módulos de controlador EBOD.   

### <a name="monitoring-leds-for-the-primary-controller"></a>Monitorização LEDs para o controlador de principal
A ilustração seguinte ajuda-o a identificar os LEDs no controlador principal. (Todos os componentes são listados para ajudar a orientação.)  

   ![Monitorização LEDs - controlador principal][4]
 
Utilize a tabela seguinte para determinar se o módulo do controlador está a funcionar corretamente.  

### <a name="controller-indicator-leds"></a>Indicador de controlador LEDs  

| LED | Descrição                                                                            
|---- | ----------- |
| ID de LED (azul) | Indica que está a ser identificado o módulo. Se o LED azul está a piscar num controlador em execução, em seguida, o controlador é o controlador ativo e outra é o controlador de espera. Para mais informações, consulte o artigo [identificar o controlador de ativo no seu dispositivo](storsimple-controller-replacement.md#identify-the-active-controller-on-your-device). |
| Falhas LED (laranja) | Indica uma falha de controlador de.        
| LED OK (verde) | Constante verde indica que o controlador é OK. Piscar verde indica um controlador de erro de configuração de VPD. |
| Atividade de SA LEDs (verde) | Constante verde indica uma ligação sem actividade atual. Piscar verde indica que a ligação tem atividade em curso. |
| Estado de Ethernet LEDs | Lado direito indica actividade de ligação/rede: ligação (verde estável) ativo, (piscar verde) actividade de rede. Do lado esquerdo indica a velocidade da rede: Mb/s 1000 (amarelo), 100 (verde) Mb s e (desligado) 10 Mb/s. Consoante o modelo de componente, este light poderá intermitência do mesmo se a interface de rede não estiver ativada. |
| MENSAGEM LEDs | Indica o progresso de arranque quando o controlador de está ativado. Se o dispositivo StorSimple falha de arranque, este LED irá ajudá-Microsoft Support identificar o ponto no processo de arranque no qual ocorreu a falha. |

>[AZURE.IMPORTANT] 
Se a falha LED é feixe, existe um problema com o módulo do controlador que pode ser resolvido pelo reiniciar o controlador de. Contacte o Microsoft Support se reiniciar o controlador de não resolver este problema.  


### <a name="monitoring-leds-for-the-ebod-ebod-enclosure"></a>Monitorização LEDs para o EBOD (inclusão EBOD)  

Cada um dos controladores Gb/s SA EBOD 6 tem LEDs indicam o respetivo estado conforme apresentado na seguinte ilustração.  

  ![Monitorização LEDs - inclusão EBOD][5]

Utilize a tabela seguinte para determinar se o módulo do controlador EBOD está a funcionar normalmente.  

### <a name="ebod-controller-module-indicator-leds"></a>Indicador de módulo EBOD controlador LEDs  

|Estado | Módulo e/s OK (verde) | Falhas de módulo e/s (laranja) | Atividade de porta anfitrião (verde) |
|-------|----------------------|-------------------------------|----------------------------|
| Módulo de controlador OK | ATIVADO | DESATIVAR | - |
| Falha do controlador módulo | DESATIVAR | ATIVADO | - |
| Sem ligação à porta de anfitrião externo | - | - | DESATIVAR |
| Ligação à porta de anfitrião externo – sem actividade | - | - | ATIVADO |
| Ligação à porta de anfitrião externo - atividade | - | - | Piscar |
| Erro de metadados do controlador módulo | Piscar | - | - |

## <a name="disk-drive-indicator-leds-for-the-primary-enclosure-and-ebod-enclosure"></a>Indicador de unidade de disco LEDs para a inclusão de principal e a inclusão EBOD

O dispositivo StorSimple tem unidades de disco localizadas na inclusão de principal e a inclusão EBOD. Cada unidade de disco contém monitorização indicador LEDs, conforme descrito nesta secção. 

Para as unidades de disco, o estado de unidade é indicado por uma verde LED e um LED vermelho laranja instalados na parte da frente de cada módulo do unidade carrier. A ilustração seguinte mostra estes LEDs.

  ![Unidade de disco LEDs][6]
 
Utilize a tabela seguinte para determinar o estado de cada unidade de disco, por sua vez afeta o painel global frontal LED estado.  

### <a name="disk-drive-indicator-leds-for-the-ebod-enclosure"></a>Indicador de unidade de disco LEDs para a inclusão EBOD  

| Estado | Atividade OK LED (verde) | Falhas LED (vermelho laranja) | Associadas painel ops LED |
|-------|--------------------------|----------------------|-------------------------|
| Não existe nenhuma unidade instalada | DESATIVAR | DESATIVAR | Nenhum |
| Unidade instalada e operacional | Piscar no desativar com atividade | X | Nenhum |
| Conjunto de identidade do dispositivo de serviços de inclusão de SCSI (seus) | ATIVADO | Piscar 1 segundo no 1/segundo desativar | Nenhum |
| Conjunto de bit o seus dispositivo de falhas | ATIVADO | ATIVADO | Falhas lógica (vermelho) |
| Falha do Power controlo circuito | DESATIVAR | ATIVADO | Falhas de módulo (vermelho) |

## <a name="audible-alarms"></a>Alarmes sonoros  

Um dispositivo StorSimple contém alarmes sonoros associadas a inclusão de principal e a inclusão EBOD. Encontra-se um alarme sonoro no painel frontal (também conhecido como o painel de ops) de ambos os anexos. Alarme sonoro indica quando existe uma condição falhas. As seguintes condições ativará alarme:  

- Falhas de ventoinha ou falha
- Tensão fora do intervalo
- Sobre ou em condição de temperatura
- Térmica sobrecarga
- Falha de sistema
- Falhas de lógica
- Falha de aprovisionamento do Power
- Remoção de uma potência refrigeração módulo (PCM)  

A tabela seguinte descreve os vários Estados de alarme.  

### <a name="alarm-states"></a>Alarme Unidos  

| Estado de alarme | Ação | Ação com o botão Desativar som premido |
|------------|---------|---------------------------------|
| S0 | Modo normal: silenciosa | Duas vezes AvisoSonoro |
| S1 | Modo de falhas: 1 segundo no 1/segundo desativar | Transição para S2 ou S3 (consulte notas) |
| S2 | Modo de lembrar: intermitente AvisoSonoro | Nenhum |
| S3 | Modo sem som: silenciosa | Nenhum |
| S4 | Modo de falhas crítico: alarme contínuo | Não está disponível: desativar som não activa |

> [AZURE.NOTE] 

>  - Estado de alarme S1, se não premir desativar som dentro de minutos de 2, o estado automaticamente transições para S2 ou S3.  
>  - Alarme Estados-membros S1 S4 regressar ao S0 depois da condição de falhas está desmarcada.  
>  - Estado de falhas críticas S4 pode ser introduzido a partir de qualquer outro Estado.  

Pode desativar o som alarme sonoro premindo no botão Desativar som no painel de ops. Desativar o som dos automática irá ocorrer depois de dois minutos se o parâmetro de desativar som não é operado manualmente. Quando o alarme é desativado, este continuará a som com breves alarmes intermitentes sonoros para indicar que ainda existe um problema. Alarme serão silencioso quando todos os problemas estão desmarcados.  

A tabela seguinte descreve as várias condições de alarme.  

### <a name="alarm-conditions"></a>Condições de alarme  

| Estado | Gravidade | Alarme | OPS painel LED |
|--------|---------|--------|----------------|
| Alerta PCM – perda de alimentação CC a partir de uma única PCM | Falhas – sem perda de redundância | S1 | Falhas de módulo|
|Alerta PCM – perda de alimentação CC a partir de uma única PCM | Falhas – perda de redundância | S1 | Falhas de módulo |
| Falhas de ventoinha PCM | Falhas – perda de redundância | S1 | Falhas de módulo |
| Módulo SB detetado falhas PCM | Falhas | S1 | Falhas de módulo |
| PCM removido | Erro de configuração | Nenhum | Falhas de módulo |
| Erro de configuração de inclusão | Falhas – crítica | S1 | Falhas de módulo |
| Alerta de temperatura de aviso baixa | Aviso | S1 | Falhas de módulo |
| Alerta de temperatura de aviso alta | Aviso | S1 | Falhas de módulo |
| Sobre alarme de temperatura | Falhas – crítica | S1 | Falhas de módulo |
| Falha de bus I2C | Falhas – perda de redundância | S1 | Falhas de módulo |
| OPS painel erro de comunicação (I2C) | Falhas – crítica     | S1 | Falhas de módulo |
| Erro de controlador | Falhas – crítica | S1 | Falhas de módulo |
| Falhas de módulo SB interface | Falhas – crítica | S1 | Falhas de módulo |
| Falhas de módulo de interface SB – não funcionais módulos restante | Falhas – crítica | S4 | Falhas de módulo |
| Módulo de interface de SB removido | Aviso | Nenhum | Falhas de módulo |
| Unidade power controlo de falhas | Aviso – sem perda de energia de unidade | S1 | Falhas de módulo |
| Unidade power controlo de falhas | Falhas – crítica; perda de energia de unidade | S1 | Falhas de módulo |
| Unidade removida | Aviso | Nenhum | Falhas de módulo |
| Power insuficiente disponível | Aviso | nenhum | Falhas de módulo |

## <a name="next-steps"></a>Próximos passos

Saiba mais sobre os [componentes de hardware StorSimple e de estado](storsimple-monitor-hardware-status.md).

[1]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE01.png
[2]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE02.png
[3]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE03.png
[4]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE04.png
[5]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE05.png
[6]: ./media/storsimple-monitoring-indicators/storsimple-monitoring-indicators-IMAGE06.png

 
