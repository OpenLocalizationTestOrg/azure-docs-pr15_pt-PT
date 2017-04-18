<properties 
   pageTitle="Configurar CHAP para o seu dispositivo StorSimple | Microsoft Azure"
   description="Descreve como configurar o desafio autenticação CHAP (Handshake Protocol) num dispositivo StorSimple."
   services="storsimple"
   documentationCenter=""
   authors="alkohli"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="TBD"
   ms.date="08/17/2016"
   ms.author="alkohli" />

# <a name="configure-chap-for-your-storsimple-device"></a>Configurar CHAP para o seu dispositivo StorSimple

Neste tutorial explica como configurar CHAP para o seu dispositivo StorSimple. O procedimento detalhado neste artigo aplica-se ao StorSimple 8000 série, bem como StorSimple 1200 dispositivos.

CHAP designa protocolo de autenticação de Handshake HIP. É um esquema de autenticação utilizado por servidores para validar a identidade de clientes remotos. A verificação baseia-se uma palavra-passe partilhada ou palavra-passe. CHAP pode ser unidirecional (unidireccional) ou participações (bidireccional). CHAP unidirecional é quando o destino autentica um iniciador. CHAP inversa ou comum, outro lado, requer que o destino autenticar o iniciador e, em seguida, o iniciador autenticar o destino. Autenticação do iniciador pode ser implementada sem autenticação de destino. No entanto, a autenticação de destino pode ser implementada apenas se a autenticação do iniciador também é implementada. 

Como prática recomendada, recomendamos que utilize CHAP para melhorar a segurança iSCSI.

>[AZURE.NOTE] Tenha em atenção que IPSEC não é suportada atualmente em StorSimple dispositivos.

As definições de CHAP no dispositivo StorSimple podem ser configuradas das seguintes formas:

- Autenticação unidireccional ou sentida

- Bidireccional ou a autenticação do inversa ou comum

Em cada uma nestes casos, o portal para o dispositivo e do software de iniciador iSCSI do servidor tem de ser configurado. Os passos detalhados para esta configuração são descritos no tutorial seguinte.

## <a name="unidirectional-or-one-way-authentication"></a>Autenticação unidireccional ou sentida

Em autenticação, o destino autentica o iniciador. Esta autenticação requer que configure as definições de iniciador CHAP no dispositivo de StorSimple e o software de iniciador no anfitrião do iSCSI. As modalidades para o seu dispositivo StorSimple e o anfitrião do Windows são descritas seguinte.

#### <a name="to-configure-your-device-for-one-way-authentication"></a>Para configurar o seu dispositivo, para autenticação sentido

1. No portal clássico Azure, na página de **dispositivos** , clique no separador **Configurar** .

    ![Iniciador CHAP](./media/storsimple-configure-chap/IC740943.png)

2. Desloque-se para baixo nesta página e, na secção **CHAP iniciador** :
                                                    
    1. Forneça um nome de utilizador para o seu iniciador CHAP.

    2. Fornece uma palavra-passe para o seu iniciador CHAP.

         > [AZURE.IMPORTANT] O nome de utilizador CHAP tem de conter menos de 233 carateres. A palavra-passe CHAP tem de estar entre 12 e 16 carateres. Um nome de utilizador ou palavra-passe mais longa irá resultar numa falha de autenticação no anfitrião do Windows.
    
    3. Confirme a palavra-passe.

4. Clique em **Guardar**. Será apresentada uma mensagem de confirmação. Clique em **OK** para guardar as alterações.

#### <a name="to-configure-one-way-authentication-on-the-windows-host-server"></a>Para configurar a autenticação unidirecional no servidor de anfitrião do Windows

1. No servidor de anfitrião do Windows, inicie o iniciador iSCSI.

2. Na janela do **iSCSI iniciador propriedades** , execute os seguintes passos:
                                                    
    1. Clique no separador de **deteção** .

        ![Propriedades de iniciador iSCSI](./media/storsimple-configure-chap/IC740944.png)

    2. Clique em **descobrir o Portal**.

3. Na caixa de diálogo **Descobrir o Portal de destino** :
                                                    
    1. Especifique o endereço IP do seu dispositivo.

    3. Clique em **Avançadas**.

        ![Descobrir o portal de destino](./media/storsimple-configure-chap/IC740945.png)

4. Na caixa de diálogo **Definições avançadas** :
                                                    
    1. Selecione a caixa de verificação **Ativar CHAP iniciar sessão** .

    2. No campo **nome** , fornece o nome de utilizador que especificou para o iniciador CHAP no portal do clássico.

    3. No campo **o segredo de destino** , fornece a palavra-passe que especificou para o iniciador CHAP no portal do clássico.

    4. Clique em **OK**.

        ![Definições avançadas gerais](./media/storsimple-configure-chap/IC740946.png)

5. No separador **destinos** da janela do **iSCSI iniciador propriedades** , o estado do dispositivo deve aparecer como **ligado**. Se estiver a utilizar um dispositivo StorSimple 1200, em seguida, cada volume vai ser instalado como um destino iSCSI conforme apresentado abaixo. Por conseguinte, os passos 3-4 terão de ser repetida para cada volume.

    ![Volumes instalados como destinos separados](./media/storsimple-configure-chap/chap4.png)

    > [AZURE.IMPORTANT] Se mudar o nome iSCSI, o novo nome será utilizado para sessões de iSCSI novo. Novas definições não são utilizadas para existentes sessões até termine a sessão e inicie sessão novamente.

Para obter mais informações sobre como configurar CHAP no servidor de anfitrião do Windows, vá para [Considerações adicionais](#additional-considerations).


## <a name="bidirectional-or-mutual-authentication"></a>Autenticação bidireccional ou participações

Na autenticação bidirecional, o destino autentica o iniciador e, em seguida, o iniciador autentica de destino. Isto requer que o utilizador configurar as definições de iniciador CHAP, bem como as definições de CHAP inversa no dispositivo e iSCSI iniciador o software no anfitrião do. Os procedimentos seguintes descrevem os passos para configurar a autenticação comum no dispositivo e no anfitrião do Windows.

#### <a name="to-configure-your-device-for-mutual-authentication"></a>Para configurar o seu dispositivo, para autenticação comum

1. No portal clássico Azure, na página de **dispositivos** , clique no separador **Configurar** .

    ![Destino CHAP](./media/storsimple-configure-chap/IC740948.png)

2. Desloque-se para baixo nesta página e, na secção de **Destino CHAP** :
                                                    
    1. Forneça um **nome de utilizador inverter CHAP** para o seu dispositivo.

    2. Fornece uma **palavra-passe inverter CHAP** para o seu dispositivo.

    3. Confirme a palavra-passe.

3. Na secção **CHAP iniciador** :
                                                
    1. Forneça um **nome de utilizador** para o seu dispositivo.

    1. Fornece uma **palavra-passe** para o seu dispositivo.

    3. Confirme a palavra-passe.

4. Clique em **Guardar**. Será apresentada uma mensagem de confirmação. Clique em **OK** para guardar as alterações.

#### <a name="to-configure-bidirectional-authentication-on-the-windows-host-server"></a>Para configurar a autenticação de bidirecional no servidor de anfitrião do Windows

1. No servidor de anfitrião do Windows, inicie o iniciador iSCSI.

2. Na janela do **iSCSI iniciador propriedades** , clique no separador de **configuração** .

3. Clique em **CHAP**.

4. Na caixa de diálogo **iSCSI comum CHAP o segredo do iniciador** :
                                                    
    1. Digite a **Inverter palavra-passe de CHAP** que configurou no portal do Azure clássico.

    2. Clique em **OK**.

        ![iSCSI comum CHAP o segredo do iniciador](./media/storsimple-configure-chap/IC740949.png)

5. Clique no separador de **alvos** .

6. Clique no botão de **Ligar** . 

7. Na caixa de diálogo **Ligar para o destino** , clique em **Avançadas**.

8. Na caixa de diálogo **Propriedades avançadas** :
                                                    
    1. Selecione a caixa de verificação **Ativar CHAP iniciar sessão** .

    2. No campo **nome** , fornece o nome de utilizador que especificou para o iniciador CHAP no portal do clássico.

    3. No campo **o segredo de destino** , fornece a palavra-passe que especificou para o iniciador CHAP no portal do clássico.

    4. Selecione a caixa de verificação **Executar comum autenticação** .

        ![Autenticação comum de definições avançadas](./media/storsimple-configure-chap/IC740950.png)

    5. Clique em **OK** para concluir a configuração de CHAP
     
Para obter mais informações sobre como configurar CHAP no servidor de anfitrião do Windows, vá para [Considerações adicionais](#additional-considerations).

## <a name="additional-considerations"></a>Considerações adicionais

A funcionalidade **Ligação rápida** não suporta as ligações que têm CHAP ativado. Quando é activado CHAP, certifique-se de que utilize o botão de **Ligar** está disponível no separador **destinos** para ligar a um destino.

![Ligar ao destino](./media/storsimple-configure-chap/IC740947.png)

Na caixa de diálogo **ligar ao destino** que é apresentada, selecione a caixa de verificação **Adicionar esta ligação para a lista de alvos favorito** . Este procedimento garante que sempre que reiniciar o computador, uma tentativa para restaurar a ligação para os destinos favoritas iSCSI.

## <a name="errors-during-configuration"></a>Erros durante a configuração

Se a configuração de CHAP estiver incorreta, significa que é provável que seja apresentada uma mensagem de erro de **Falha de autenticação** .

## <a name="verification-of-chap-configuration"></a>Verificação de configuração de CHAP

Pode verificar que está a ser utilizado CHAP completando os passos seguintes.

#### <a name="to-verify-your-chap-configuration"></a>Para verificar a sua configuração CHAP

1. Clique em **destinos favoritos**.

2. Selecione o destino para as quais é activado autenticação.

3. Clique em **Detalhes**.

    ![destinos favoritas do iSCSI iniciador propriedades](./media/storsimple-configure-chap/IC740951.png)

4. Na caixa de diálogo **Favoritas detalhes de destino** , tenha em atenção a entrada no campo **autenticação** . Se a configuração foi efetuada com êxito, deverá aparecer **CHAP**.

    ![Detalhes de destino favorito](./media/storsimple-configure-chap/IC740952.png)

## <a name="next-steps"></a>Próximos passos

- Saiba mais sobre [segurança StorSimple](storsimple-security.md).

- Saiba mais sobre como [utilizar o serviço do Gestor de StorSimple para administrar o seu dispositivo StorSimple](storsimple-manager-service-administration.md).
