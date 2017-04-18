<properties
 pageTitle="Configurar o seu dispositivo | Microsoft Azure"
 description="Configurar o seu 3 de Pi framboesa para a primeira utilização de tempo e instale o SO Raspbian, um sistema operativo gratuito que está optimizado para o hardware framboesa Pi."
 services="iot-hub"
 documentationCenter=""
 authors="shizn"
 manager="timlt"
 tags=""
 keywords=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="multiple"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="10/21/2016"
 ms.author="xshi"/>

# <a name="11-configure-your-device"></a>1.1 configurar o seu dispositivo

## <a name="111-what-you-will-do"></a>1.1.1 o que vai fazer

Configurar o seu Pi para a primeira utilização de tempo e instalar o sistema operativo do Raspbian, um sistema operativo gratuito que está optimizado para o hardware framboesa Pi. Se cumprir algum problema, atingir soluções na [página de resolução de problemas](iot-hub-raspberry-pi-kit-node-troubleshooting.md).

## <a name="112-what-you-will-learn"></a>1.1.2 o que vai aprender

Nesta secção, vai aprender:

- Como instalar Raspbian no seu Pi
- Como power o seu Pi com um cabo USB
- Como ligar o seu Pi à rede utilizando um cabo Ethernet ou Wi-Fi
- Como adicionar um LED para o breadboard e ligá-lo à sua Pi

## <a name="113-what-you-need"></a>1.1.3 o que precisa

Para concluir esta secção, tem as seguintes partes do seu framboesa Pi 3 Starter Kit:

- A área de framboesa Pi 3
- O cartão de MicroSD 16GB
- O 5V 2º de alimentação com o cabo USB micro pé seis
- O breadboard
- Cabos de conexão
- Uma resistência 560 Ohm
- Uma arcos 10mm LED
- O cabo Ethernet

![Elementos no seu Kit de iniciação](media/iot-hub-raspberry-pi-lessons/lesson1/starter_kit.jpg)

Também precisa de:

- Uma ligação com ou sem fios para sua Pi ligar à
- Um USB SD placa ou Minibarra SD cartão para gravar a imagem do SO para o cartão MicroSD.
- Um computador a executar o Windows, Mac ou Linux. O computador é utilizado para instalar Raspbian no cartão de MicroSD.
- Uma ligação à Internet para transferir as ferramentas e necessárias software

## <a name="114-install-raspbian-on-the-microsd-card"></a>1.1.4 instalar Raspbian no cartão de MicroSD

Prepare o cartão de MicroSD para escrever a imagem Raspbian.

1. Transferir Raspbian.
  1. [Transferir](https://www.raspberrypi.org/downloads/raspbian/) o ficheiro de postal para Raspbian Jessie com Pixel.
  2. Extrai a imagem Raspbian para uma pasta no seu computador.
2. Instale Raspbian até ao cartão MicroSD.
  1. [Transferir](https://www.etcher.io) e instalar o utilitário de gravador de DVDs Etcher SD cartão.
  2. Executar Etcher e selecione a imagem de Raspbian extraídas no passo 1.
  3. Selecione a unidade de cartão MicroSD.
    Nota: Etcher pode ter já seleccionado na unidade correta.
  4. Clique em Flash para instalar Raspbian até ao cartão MicroSD.
  5. Remova o cartão MicroSD do seu computador, uma vez concluída.
    Nota: É seguro remover o cartão MicroSD diretamente porque Etcher automaticamente ejecta ou desmonta o cartão MicroSD após a conclusão.
  6. Inserir o cartão MicroSD no seu Pi.

![Inserir o cartão SD](media/iot-hub-raspberry-pi-lessons/lesson1/insert_sdcard.jpg)

## <a name="115-power-on-your-pi"></a>1.1.5 power no seu Pi

Power no seu Pi utilizando o cabo USB micro e alimentação.

![Power no](media/iot-hub-raspberry-pi-lessons/lesson1/micro_usb_power_on.jpg)

> [AZURE.NOTE] É importante utilizar a fonte de alimentação no kit que é, pelo menos, 2º para se certificar de que o seu framboesa é Moro com potência suficiente para funcionar corretamente.

## <a name="116-connect-your-raspberry-pi-3-to-the-network"></a>1.1.6 ligar o seu framboesa Pi 3 à rede

Pode ligar a sua Pi a uma rede com fios ou a uma rede sem fios. Certifique-se de que o seu Pi está ligado à mesma rede que o seu computador. Por exemplo, pode ligar o seu Pi para o parâmetro mesmo que o computador está ligado à.

### <a name="1161-connect-to-a-wired-network"></a>1.1.6.1 ligar a uma rede com fios

Utilize o cabo Ethernet para ligar o seu Pi à sua rede com fios. Os dois LEDs no seu Pi ative se a ligação é estabelecida.

![Ligar utilizando o cabo Ethernet](media/iot-hub-raspberry-pi-lessons/lesson1/connect_ethernet.jpg)

### <a name="1162-connect-to-a-wireless-network"></a>1.1.6.2 ligar a uma rede sem fios

Siga as [instruções](https://www.raspberrypi.org/learning/software-guide/wifi/) da Fundação de Pi framboesa para ligar a sua Pi à sua rede sem fios. Estas instruções para necessitam de ligar primeiro um monitor e um teclado a sua Pi.

## <a name="117-connect-the-led-to-your-pi"></a>1.1.7 ligar o LED ao seu Pi

Para concluir esta tarefa, utilize o [breadboard](https://learn.sparkfun.com/tutorials/how-to-use-a-breadboard), os cabos de conexão, o LED e a resistência. Ligue-las para as portas de [uso geral entrada/saída](https://www.raspberrypi.org/documentation/usage/gpio/) (GPIO) do seu Pi. 

![Breadboard, LED e resistência](media/iot-hub-raspberry-pi-lessons/lesson1/breadboard_led_resistor.jpg)

1. Ligue a endereço mais curta ramificação do LED ao **GPIO terra (Pin 6)**.
2. Ligar a ramificação mais longa do LED a uma ramificação da resistência.
3. Ligar a outras ramificação da resistência a **GPIO 4 (afixar 7)**.

Tenha em atenção que a polaridade LED é importante. Esta definição polaridade frequentemente é conhecida como baixa ativo.

![Saída](media/iot-hub-raspberry-pi-lessons/lesson1/pinout_breadboard.png)

Congratulation! Com êxito configurou o seu Pi.

## <a name="118-summary"></a>1.1.8 resumo

Nesta secção, aprendeu como configurar o seu Pi ao instalar Raspbian, ligar-se a sua Pi a uma rede e ligar-se um LED ao seu Pi. Tenha em atenção o LED ainda não ilumine. Na secção seguinte, instale as ferramentas e necessárias software de preparação para a execução de uma aplicação de amostra no seu Pi.

![Hardware está pronta](media/iot-hub-raspberry-pi-lessons/lesson1/hardware_ready.jpg)

## <a name="next-steps"></a>Próximos passos

[1.2 obter as ferramentas de](iot-hub-raspberry-pi-kit-node-lesson1-get-the-tools-win32.md)
