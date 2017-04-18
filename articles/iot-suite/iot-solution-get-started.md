<properties
    pageTitle="Exemplo de MyDriving Azure IoT: Guia de introdução do | Microsoft Azure"
    description="Começar a trabalhar com uma aplicação que é uma demonstração abrangente de como arquitectar um sistema de IoT utilizando o Microsoft Azure, incluindo a análise da cadeia, máquina aprendizagem e concentradores do evento."
    services=""
    documentationCenter=".net"
    suite=""
    authors="harikmenon"
    manager="douge"/>

<tags
    ms.service="multiple"
    ms.workload="tbd"
    ms.tgt_pltfrm="ibiza"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="03/25/2016"
    ms.author="harikm"/>

# <a name="mydriving-iot-system-quick-start"></a>Sistema de MyDriving IoT: Guia de introdução

MyDriving é um sistema que demonstra a estrutura e a execução de uma solução de [Internet de coisas](iot-suite-overview.md) (IoT) típica que reúne telemetria a partir de dispositivos, processa esses dados na nuvem e aplica-se fornecer uma resposta ajustável de aprendizagem automática. A sequência de demonstração registos de dados sobre o seu viagens de carro, utilizando os dados a partir do seu número de telemóvel e um adaptador que recolhe informações do sistema de controlo do seu carro. Utiliza estes dados para fornecer comentários sobre o seu estilo de condução em comparação com outros utilizadores.

É o objetivo real do MyDriving começar a criar a sua própria solução IoT. Mas antes desta, vamos começar a utilizar a MyDriving própria aplicação – como um membro da equipa do utilizador de teste. Isto dá-lhe uma experiência da aplicação e o sistema por trás-lo como um consumidor, antes de o delve para a arquitetura. -Lo também lhe apresentamos HockeyApp, uma forma modernos de gerir as distribuições, alfa e beta, das suas aplicações para testar os utilizadores.

## <a name="use-the-mobile-experience"></a>Utilizar a experiência móvel

Pode utilizar a aplicação MyDriving se tiver um dispositivo Android, iOS ou o Windows 10.

### <a name="android-and-windows-10-mobile-installation"></a>Android e a instalação do Windows 10 Mobile

No seu dispositivo:

1.  Permitir desenvolvimento de aplicações:

    -   Android: Nas **Definições** > **segurança**, permitir aplicações a partir de **origens desconhecidas**.

    -   Windows 10: Nas **Definições** > **atualizações** > **Para programadores**, defina o **modo de programador**.

2.  Aderir a nossa equipa de teste beta por inscrever-se com ou iniciar sessão no, [HockeyApp](https://rink.hockeyapp.net). HockeyApp torna mais fácil distribuir lançamentos antecipados da sua aplicação para testar os utilizadores.

    Se estiver a utilizar o Windows 10, utilize o browser do limite.

    Caso um participante de compilação 2016, inicie sessão com o e-mail de conta Microsoft mesmo que registado da conferência, através de um dos botões da Microsoft. Se estiver a já inscreveu com HockeyApp.

    ![Ecrã de início de sessão no HockeyApp](./media/iot-solution-get-started/image1.png)

3.  Transfira e instale a aplicação a partir daqui:

    -   [Android](http://rink.io/spMyDrivingAndroid)

    -   [Windows 10](http://rink.io/spMyDrivingUWP)

    Existem dois itens. Instale o certificado em **Pessoas fidedignos**. Em seguida, instale a aplicação.

*Problemas a iniciar a aplicação no Windows 10 Mobile?* Telemóvel poderá ser uma atualização ou dois atrás. Certifique-se de que tem as atualizações mais recentes ou instalar:

 - [Microsoft.NET.Native.Framework.1.2.appx](https://download.hockeyapp.net/packages/win10/Microsoft.NET.Native.Framework.1.2.appx) 

 - [Microsoft.NET.Native.Runtime.1.1.appx](https://download.hockeyapp.net/packages/win10/Microsoft.NET.Native.Runtime.1.1.appx) 

 - [Microsoft.VCLibs.ARM.14.00.appx](https://download.hockeyapp.net/packages/win10/Microsoft.VCLibs.ARM.14.00.appx)


### <a name="ios-installation"></a>instalação iOS

Se que foi compilação 2016, transfira a aplicação como um membro da nossa equipa de teste no HockeyApp:

1.  No seu dispositivo iOS, inicie sessão no [HockeyApp](https://rink.hockeyapp.net).
    Utilize um dos botões de início de sessão no Microsoft e inicie sessão com o e-mail de conta Microsoft mesmo que registado com a conferência. (Não utilizar os campos de e-mail e palavra-passe.)

    ![Ecrã de início de sessão no HockeyApp](./media/iot-solution-get-started/image1.png)

2.  No dashboard de HockeyApp, selecione MyDriving e transfira-o.

3.  Autorize a versão beta do HockeyApp:

    um. Aceda a **Definições** > **gerais** > **perfis e gestão de dispositivos.**

    b. Confie no certificado de **Bits do GmbH** .

Se não participar compilação 2016, pode criar e implementar a aplicação si:

1.   Transferir o código [de GitHub].

2.   Criar e implementar utilizando [Xamarin].

Localize mais detalhes no [Guia de referência MyDriving](http://aka.ms/mydrivingdocs).

## <a name="get-an-obd-adapter-optional"></a>Obter uma placa OBD (opcional)

Esta é a parte que o torna um sistema de Internet de coisas real! Pode utilizar a aplicação sem uma, mas é mais diversão com a coisa real e que não sejam dispendiosos.

Diagnóstico a bordo (OBD) é a funcionalidade do seu carro que utiliza a garagem para ajustar o seu carro e diagnosticar ruídos ímpares nem aos aviso. A menos que o seu carro é ótimo antiquity, encontrará um socket algures mão, normalmente atrás uma aba no dashboard. Com o conector à direita, pode obter métricas do desempenho do motor e efectuar alguns ajustes. Um conector OBD pode ser comprado efectuassem a partir de localizações habituais. Liga-se ao utilizar Bluetooth ou Wi-Fi para uma aplicação no seu telemóvel.

Neste caso, iremos ligar o seu carro na nuvem. A ligação direta do OBD é com o seu telemóvel, mas a nossa aplicação funciona como um reencaminhamento. Telemetria do seu carro é enviada diretamente no centro do MyDriving IoT, onde esta é processada do seu viagens de sessão e avalie o seu estilo de condução.

Para ligar um dispositivo OBD:

1.  Verifique se o seu carro tem um socket OBD.

2.  Obter uma placa OBD:

    -   Se estiver a utilizar um telemóvel Android ou Windows, é necessário uma placa de com capacidade de Bluetooth II OBD. É utilizada [A ferramenta de verificação BAFX produtos 34t5 Bluetooth OBDII].

    -   Se estiver a utilizar um telefone iOS, é necessário um adaptador OBD com capacidade de Wi-Fi. Utilizámos [ScanTool OBDLink MX Wi-Fi: OBD adaptador/diagnóstico Scanner].

3.  Siga as instruções fornecidas com placa OBD ligá-lo com o seu telemóvel. Tenha em atenção o seguinte:

    -   Tem de ser emparelhado um adaptador Bluetooth com o telefone, na página **Definições** de.

    -   Uma placa de Wi-Fi tem de ter um endereço 192.168 o intervalo.

4.  Se tiver várias carros, pode obter um adaptador separado para cada (máximo de três).

Se não tiver uma placa OBD, a aplicação ainda enviará localização e velocidade de dados a partir do aparelho GPS o telefone para o back-end e irá pedir-se que pretende simular uma OBD.

Pode saber mais sobre como a aplicação utiliza dados a partir da placa OBD e sobre as opções para criar o seu próprio dispositivo OBD na secção 2.1, "IoT dispositivos," no [Guia de referência MyDriving](http://aka.ms/mydrivingdocs).

## <a name="use-the-app"></a>Utilizar a aplicação

Inicie a aplicação. Existe um guia de introdução inicial para guiá-lo como funciona.

### <a name="track-your-trips"></a>Controlar o seu viagens

Toque no botão de registo (grande círculo vermelho na parte inferior do ecrã) para iniciar uma viagem e, toque novamente para o fim.

![Ilustração do botão Gravar para viagem de controlo](./media/iot-solution-get-started/image2.png)

Sempre que inicia uma viagem, se existir dispositivo sem OBD, é-lhe perguntado se pretende utilizar o simulator.

No final de uma viagem, toque no botão Parar e, receberá um resumo.

![Exemplo de uma viagem de resumo](./media/iot-solution-get-started/image3.png)

### <a name="review-your-trips"></a>Rever a sua viagens

![Exemplo de uma viagem passado](./media/iot-solution-get-started/image4.png)

### <a name="review-your-profile"></a>Rever o seu perfil

![Exemplo de um perfil de estilo de condução](./media/iot-solution-get-started/image5.png)

## <a name="send-us-your-test-feedback"></a>Envie-nos seus comentários de teste

Uma vez que criámos MyDriving para o ajudar a JumpStart da suas própria sistemas IoT, queremos certamente a sua opinião sobre bem como funciona. Diga-nos sabe se:

- Ocorrer dificuldades ou desafios.

- Existe um ponto de extensão que faria mais adequado para o seu cenário.

- Localizar uma forma mais eficiente de realizar determinadas necessidades.

- Tiver quaisquer outros sugestões para melhorar a MyDriving ou este documento.

Dentro de MyDriving própria aplicação, pode utilizar o mecanismo de comentários HockeyApp incorporado: no iOS e Android, basta dê ao seu telemóvel um agitar ou utilize o comando de menu de **comentários** . Isto automaticamente anexar uma captura de ecrã, para que recomendamos ficará a saber o que estiver a falar sobre. E se existirem quaisquer causa uma falha unfortunate, HockeyApp recolhe os registos de falha de sistema para informar-nos sobre-los. Também pode dar feedback através do [portal de HockeyApp].

Pode também um [problema no GitHub]de ficheiro, ou sair de um comentário abaixo (en-us edition).

Olharmos reencaminhar para audição a partir do!

## <a name="next-steps"></a>Próximos passos

-   Explore o [Guia de referência MyDriving](http://aka.ms/mydrivingdocs) para compreender como que tenha concebido e compilado todo o sistema MyDriving.

-   [Criar e implementar um sistema da sua própria](iot-solution-build-system.md) utilizando o nossos scripts do Gestor de recursos do Azure. O [Guia de referência MyDriving](http://aka.ms/mydrivingdocs) também orienta áreas onde vai introduzir a maior parte das personalizações.

  [a partir do GitHub]: https://github.com/Azure-Samples/MyDriving
  [utilizar Xamarin]: https://developer.xamarin.com/guides/ios/getting_started/installation/
  [Ferramenta de verificação para OBDII 34t5 Bluetooth BAFX produtos]: http://www.amazon.com/gp/product/B005NLQAHS
  [ScanTool OBDLink MX Wi-Fi: OBD adaptador/diagnóstico Scanner]: http://www.amazon.com/gp/product/B00OCYXTYY/ref=s9_simh_gw_g263_i1_r?pf_rd_m=ATVPDKIKX0DER&pf_rd_s=desktop-2&pf_rd_r=1MWRMKXK4KK9VYMJ44MP
  [HockeyApp portal]: https://rink.hockeyapp.org
  [GitHub de problema]: https://github.com/Azure-Samples/MyDriving/issues
