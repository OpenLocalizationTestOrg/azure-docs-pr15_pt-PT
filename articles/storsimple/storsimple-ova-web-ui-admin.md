<properties 
   pageTitle="Web matriz Virtual StorSimple administração IU | Microsoft Azure"
   description="Descreve como efetuar tarefas de administração do dispositivo básicos através da web matriz Virtual StorSimple IU."
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
   ms.date="04/07/2016"
   ms.author="alkohli" />

# <a name="use-the-web-ui-to-administer-your-storsimple-virtual-array"></a>Utilize a IU da Web para administrar a sua matriz de Virtual StorSimple

![fluxo de processo de configuração](./media/storsimple-ova-web-ui-admin/manage4.png)

## <a name="overview"></a>Descrição geral

Os tutoriais neste artigo aplicam-se para a Microsoft Azure StorSimple Virtual matriz (também conhecido como o StorSimple no local virtual dispositivo) a executar o lançamento de disponibilidade geral (das versões DG) Março de 2016. Este artigo descreve alguns dos fluxos de trabalho complexos e tarefas de gestão de que podem ser executadas na matriz StorSimple Virtual. Pode gerir a matriz de Virtual StorSimple utilizando o Gestor de StorSimple IU (designado pelo portal de IU) e a web local da IU para o dispositivo de serviço. Este artigo foca-se as tarefas que pode efetuar através da IU da web.

Este artigo inclui os seguintes tutoriais:

- Obter a chave de encriptação de dados do serviço
- Resolver problemas de erros de configuração de IU web
- Gerar um pacote de registo
- Encerrar ou reiniciar o seu dispositivo

## <a name="get-the-service-data-encryption-key"></a>Obter a chave de encriptação de dados do serviço

Uma chave de encriptação de dados do serviço é gerada quando registar o seu primeiro dispositivo com o serviço do Gestor de StorSimple. Esta chave, em seguida, é necessário com a chave de registo do serviço para se registar dispositivos adicionais com o serviço do Gestor de StorSimple.

Se tiver perdeu a sua chave de encriptação de dados do serviço e necessidade de recuperá-la, execute os seguintes passos no local web IU do dispositivo registarem com o seu serviço.

#### <a name="to-get-the-service-data-encryption-key"></a>Para obter a chave de encriptação de dados do serviço

1. Ligar para o local web IU. Aceda a **configuração** > **definições na nuvem**.
  

2. Na parte inferior da página, clique em **obter a chave de encriptação de dados de serviço**. Uma chave de irão aparecer. Copiar e guarde esta chave.
    
    ![obter a chave de encriptação de dados do serviço 1](./media/storsimple-ova-web-ui-admin/image27.png)
   


## <a name="troubleshoot-web-ui-setup-errors"></a>Resolver problemas de erros de configuração de IU web

Em algumas instâncias quando configurar o dispositivo através da web local IU, que podem ocorrer erros. Para diagnosticar e resolver estes erros, pode executar os testes de diagnóstico.

#### <a name="to-run-the-diagnostic-tests"></a>Para executar os testes de diagnóstico

1. No local IU da web, aceda a **resolução de problemas** > **testes de diagnóstico**.

    ![Execute os diagnósticos 1](./media/storsimple-ova-web-ui-admin/image29.png)

2. Na parte inferior da página, clique em **Executar testes de diagnóstico**. Este iniciará testes para diagnosticar qualquer possíveis problemas com a sua rede, o dispositivo, o proxy da web, hora ou definições da nuvem. Será notificado do que o dispositivo está em execução testes.

3. Após tem concluído os testes, serão apresentados os resultados. O exemplo seguinte mostra os resultados de testes de diagnóstico. Tenha em atenção que as definições de proxy web não foram configuradas neste dispositivo e, consequentemente, não foi executado o teste de proxy web. Todos os outros testes para as definições de rede, servidor DNS e definições de hora foram efetuada com êxito.

    ![Execute os diagnósticos 2](./media/storsimple-ova-web-ui-admin/image30.png)

## <a name="generate-a-log-package"></a>Gerar um pacote de registo

Um pacote de registo é composto por todos os registos relevantes que o podem ajudar a Microsoft Support com a resolução de problemas do dispositivo. Neste lançamento, um pacote de registo pode ser gerado através da IU da web local.

#### <a name="to-generate-the-log-package"></a>Para gerar o pacote de registo

1. No local IU da web, aceda a **resolução de problemas** > **registos do sistema**.

    ![gerar o pacote de registo 1](./media/storsimple-ova-web-ui-admin/image31.png)

2. Na parte inferior da página, clique em **Criar pacote de registo**. Será criado um pacote de registos do sistema. Isto vai demorar alguns minutos.

    ![gerar o pacote de registo 2](./media/storsimple-ova-web-ui-admin/image32.png)

    Será notificado depois do pacote for criado com êxito e a página será atualizada para indicar a hora e data quando o pacote foi criado.

    ![gerar o pacote de registo 3](./media/storsimple-ova-web-ui-admin/image33.png)

3. Clique em **Transferir o pacote de registo**. Um pacote ZIP será transferido no sistema de.

    ![gerar registo pacote 4](./media/storsimple-ova-web-ui-admin/image34.png)

4. Pode deszipar o pacote de registo transferido e ver os ficheiros de registo do sistema.

## <a name="shut-down-and-restart-your-device"></a>Encerrar e reinicie o seu dispositivo

Pode encerrar ou reinicie o seu dispositivo virtual através da IU da web local. Vamos recomendar antes de reiniciar o, siga os volumes ou partilhas offline no anfitrião do e, em seguida, o dispositivo. Isto irá minimizar qualquer possibilidade de danos nos dados. 

#### <a name="to-shut-down-your-virtual-device"></a>Para encerrar o seu dispositivo virtual

1. No local IU da web, aceda a **Manutenção** > **as definições de energia**.

2. Na parte inferior da página, clique em **encerramento**.

    ![encerramento dispositivo 1](./media/storsimple-ova-web-ui-admin/image36.png)

3. Será apresentado um aviso a indicar que um encerramento do dispositivo irá interromper qualquer IO que estavam em curso, que resulta num tempo de inatividade. Clique no ícone de verificação ![Selecione o ícone](./media/storsimple-ova-web-ui-admin/image3.png).

    ![Aviso de encerramento do dispositivo](./media/storsimple-ova-web-ui-admin/image37.png)

    Será notificado do que o encerramento foi iniciado.

    ![encerramento dispositivo iniciado](./media/storsimple-ova-web-ui-admin/image38.png)

    O dispositivo agora irá encerrar. Se pretender iniciar o seu dispositivo, terá de fazê-lo através do Gestor de Hyper-V.

#### <a name="to-restart-your-virtual-device"></a>Para reiniciar o seu dispositivo virtual

1. No local IU da web, aceda a **Manutenção** > **as definições de energia**.

2. Na parte inferior da página, clique em **reiniciar**.

    ![Reiniciar dispositivo](./media/storsimple-ova-web-ui-admin/image36.png)

3. Será apresentado um aviso a indicar que reiniciar o dispositivo irá interromper qualquer IOs que estavam em curso, que resulta num tempo de inatividade. Clique no ícone de verificação ![Selecione o ícone](./media/storsimple-ova-web-ui-admin/image3.png).

    ![Reinicie o aviso](./media/storsimple-ova-web-ui-admin/image37.png)

    Será notificado do que o reinício foi iniciado.

    ![reiniciar iniciados por](./media/storsimple-ova-web-ui-admin/image39.png)

    Enquanto o reinício está em curso, irá perder a ligação para a interface de utilizador. Pode monitorizar o reinício através da atualização dos IU periodicamente. Em alternativa, pode monitorizar o estado do dispositivo reiniciar através do Gestor de Hyper-V.

## <a name="next-steps"></a>Próximos passos

Saiba como [utilizar o serviço do Gestor de StorSimple para gerir o seu dispositivo](storsimple-manager-service-administration.md).
