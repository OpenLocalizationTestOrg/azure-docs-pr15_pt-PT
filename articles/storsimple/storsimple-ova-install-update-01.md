<properties 
   pageTitle="Instalar atualizações numa matriz Virtual StorSimple | Microsoft Azure"
   description="Descreve como utilizar web StorSimple Virtual matriz da IU para aplicar atualizações utilizando o método de portal e de correcção"
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
   ms.date="09/07/2016"
   ms.author="alkohli" />

# <a name="install-updates-on-your-storsimple-virtual-array"></a>Instalar atualizações no seu matriz de Virtual StorSimple

## <a name="overview"></a>Descrição geral

Este artigo descreve os passos necessários para instalar as atualizações no seu matriz Virtual StorSimple através da web local IU e através do portal do Azure clássico. Tem de aplicar atualizações de software ou correcções para manter a sua matriz de Virtual StorSimple atualizado. 

Tenha em atenção que instalar uma atualização ou correcção reinicia o seu dispositivo. Dado que a matriz Virtual StorSimple for um dispositivo de nó único, qualquer e/s em curso é interrompida e o seu dispositivo experiências tempo de inatividade. 

Antes de aplicar uma atualização, recomendamos que tome os volumes ou partilhas offline no anfitrião do primeiro e, em seguida, o dispositivo. Isto minimiza qualquer possibilidade de danos nos dados.

> [AZURE.IMPORTANT] Se estiver a executar a atualização 0,1 ou versões de software das versões DG, tem de utilizar o método de correcção através da web local da IU para instalar a atualização 0,3. Se estiver a executar a atualização 0,2, recomendamos que instale as atualizações através do portal do Azure clássica.

## <a name="use-the-local-web-ui"></a>Utilizar a web local da IU 
 
Existem dois passos ao utilizar da IU da web local:

- Transfira a atualização ou a correcção
- Instalar a atualização ou a correcção

### <a name="download-the-update-or-the-hotfix"></a>Transfira a atualização ou a correcção

Execute os passos seguintes para transferir a atualização de software do catálogo do Microsoft Update.

#### <a name="to-download-the-update-or-the-hotfix"></a>Para transferir a atualização ou a correcção

1. Inicie o Internet Explorer e navegue para [http://catalog.update.microsoft.com](http://catalog.update.microsoft.com).

2. Se esta for a primeira vez que utilizar o catálogo do Microsoft Update neste computador, clique em **instalar** quando lhe for pedido para instalar o suplemento do catálogo do Microsoft Update.
  
3. Na caixa de pesquisa do catálogo do Microsoft Update, introduza o número da Base de dados de conhecimento (KB) de correcção que pretende transferir. Introduza **3182061** para actualização 0,3 e, em seguida, clique em **Procurar**.

    Na lista correcção é apresentada, por exemplo, **StorSimple Virtual matriz atualização 0,3**.

    ![Catálogo de procura](./media/storsimple-ova-install-update-01/download1.png)

4. Clique em **Adicionar**. A atualização é adicionada ao cesto de.

5. Clique em **Ver cesto**.

6. Clique em **Transferir**. Especificar ou **Navegue** para uma localização local onde pretende que as transferências apareça. As atualizações são transferidas para a localização especificada e colocadas numa subpasta com o mesmo nome que a atualização. Também pode ser copiada a pasta para uma partilha de rede está acessível a partir do dispositivo.

7. Abra a pasta copiada, deverá ver um ficheiro de pacote do Microsoft Update autónomo `WindowsTH-KB3011067-x64`. Este ficheiro é utilizado para instalar a atualização ou correcção.


### <a name="install-the-update-or-the-hotfix"></a>Instalar a atualização ou a correcção

Antes da instalação de actualização ou correcção, certifique-se de que tem a atualização ou a correcção transferido quer localmente no seu anfitrião ou acessível através de uma partilha de rede. 

Utilize este método instalar atualizações num dispositivo em execução das versões DG ou atualizar 0.1 versões de software. Este procedimento irá guiá-menor que 2 minutos a concluir. Execute os passos seguintes para instalar a atualização ou correcção.


#### <a name="to-install-the-update-or-the-hotfix"></a>Para instalar a atualização ou a correcção

1. No local IU da web, aceda a **Manutenção** > **Atualização de Software**.

    ![dispositivo de actualização](./media/storsimple-ova-install-update-01/update1m.png)

2. Na **atualizar o caminho do ficheiro**, introduza o nome do ficheiro para a atualização ou a correcção. Também pode navegar para o ficheiro de instalação actualização ou correcção se colocado numa partilha de rede. Clique em **Aplicar**.

    ![dispositivo de actualização](./media/storsimple-ova-install-update-01/update2m.png)

3.  É apresentado um aviso. Fornecido isto é um dispositivo de nó único, após a atualização é aplicada, reinicia o dispositivo e existe tempo de inatividade. Clique no ícone de verificação.

    ![dispositivo de actualização](./media/storsimple-ova-install-update-01/update3m.png)

4. A atualização é iniciado. Depois do dispositivo é atualizado com êxito, reinicia. IU do local de não está acessível este duração.

    ![dispositivo de actualização](./media/storsimple-ova-install-update-01/update5m.png)

5. Depois do reinício estiver concluído, são redirecionado para a página de **início de sessão** . Para verificar se o software do dispositivo tem o actualizado, no local web IU, aceda a **Manutenção** > **Atualização de Software**. A versão do software apresentado deve ser **10.0.0.0.0.10288.0** para actualização 0,3.

    > [AZURE.NOTE] Vamos comunicar as versões de software numa forma ligeiramente diferente na web local IU e portal clássico do Azure. Por exemplo, a web local IU relatórios **10.0.0.0.0.10288** e o portal do clássico Azure relatórios **10.0.10288.0** para a mesma versão. 

    ![dispositivo de actualização](./media/storsimple-ova-install-update-01/update6m.png)





## <a name="use-the-azure-classic-portal"></a>Utilizar o portal clássico do Azure

Se executar a atualização 0,2, recomendamos que instale atualizações através do portal do Azure clássica. O procedimento portal requer que o utilizador digitalizar, transfira e, em seguida, instale as atualizações. Este procedimento demora cerca de 7 minutos a concluir. Execute os passos seguintes para instalar a atualização ou correcção.

[AZURE.INCLUDE [storsimple-ova-install-update-via-portal](../../includes/storsimple-ova-install-update-via-portal.md)]

Após a instalação estiver concluída (conforme indicado pelo Estado da tarefa a 100%), aceda a **dispositivos > manutenção > atualizações de Software**. A versão do software apresentado deve ser 10.0.10288.0.

![dispositivo de actualização](./media/storsimple-ova-install-update-01/azupdate12m.png)

## <a name="next-steps"></a>Próximos passos

Saiba mais sobre como [administrar a sua matriz de Virtual StorSimple](storsimple-ova-web-ui-admin.md).
