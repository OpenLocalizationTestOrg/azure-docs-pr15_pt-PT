<properties
   pageTitle="Configurar o denominada as credenciais de autenticação | Microsoft Azure"
   description="Saiba como para fornecer credenciais esse Visual Studio pode utilizar para autenticar pedidos para Azure para publicar uma aplicação para Azure a partir do Visual Studio ou para monitorizar a um serviço de nuvem existente... "
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# <a name="setting-up-named-authentication-credentials"></a>Configurar as credenciais de autenticação com nome

Para publicar uma aplicação para Azure a partir do Visual Studio ou para monitorizar a um serviço de nuvem existente, tem de fornecer credenciais que pode utilizar o Visual Studio para autenticar pedidos para Azure. Existem vários locais no Visual Studio, onde pode iniciar sessão para fornecer essas credenciais. Por exemplo, a partir do Explorador do servidor, pode abrir o menu de atalho para o nó do **Azure** e selecione **ligar-se ao Azure**. Quando iniciar sessão, as informações da subscrição associadas à sua conta Azure estão disponíveis no Visual Studio e não há mais de que tem de fazer nada.

Ferramentas Azure também suporta uma forma mais antiga fornecer as credenciais, utilizando o subscrição (ficheiro .publishsettings). Este tópico descreve este método, que ainda é suportado no Azure SDK 2.2.

Os seguintes itens são necessários para a autenticação Azure.

- O ID da subscrição

- Um certificado de v3 x. 509 válido

>[AZURE.NOTE] O comprimento da chave do certificado de v3 x. 509 tem de ser, pelo menos, 2048 bits. Azure irá rejeitar qualquer certificado que não correspondam a este requisito ou que não é válido.

Visual Studio utiliza o seu ID da subscrição juntamente com os dados de certificados como credenciais. As credenciais adequadas são referenciadas no ficheiro de subscrição (.publishsettings ficheiro), que contém uma chave pública para o certificado. O ficheiro de subscrição pode conter as credenciais para a mais do que uma subscrição.

Pode editar as informações da subscrição a partir da caixa de diálogo **Novo/Edit subscrição** , tal como é explicado posteriormente neste tópico.

Se pretender criar um certificado de si, pode referir-se para as instruções em [criar e carregar um certificado de gestão do Azure](https://msdn.microsoft.com/library/windowsazure/gg551722.aspx) e, em seguida, carregar o certificado manualmente para o [portal clássica Azure](http://go.microsoft.com/fwlink/?LinkID=213885).

>[AZURE.NOTE] Estas credenciais que requer o Visual Studio para gerir os seus serviços na nuvem não são as mesmas credenciais que são necessários para autenticar um pedido dos serviços de armazenamento Azure.

## <a name="modify-or-export-authentication-credentials-in-visual-studio"></a>Modificar ou exportar as credenciais de autenticação no Visual Studio

Também pode configurar, modificar ou exportar as suas credenciais autenticação na caixa de diálogo **Nova subscrição** , que é apresentada se efetuar qualquer uma das seguintes ações:

- No **Explorador do servidor**, abra o menu de atalho para o nó do **Azure** , selecione **Gerir subscrições**, selecione o separador **certificados** e selecione o botão **Novo** ou **Editar** .

- Durante a publicação de um serviço em nuvem Azure a partir do Assistente da **Aplicação de Azure publicar** , selecione **Gerir** na lista **Escolher a sua subscrição** , em seguida, selecione o separador certificados e, em seguida, selecione o botão **Novo** ou **Editar** .

O seguinte procedimento assume que a caixa de diálogo **Nova subscrição** é aberta.

### <a name="to-set-up-authentication-credentials-in-visual-studio"></a>Para configurar as credenciais de autenticação no Visual Studio

1. Na **Selecione um certificado existente** para a lista de autenticação, selecione um certificado.

1. Selecione o botão **copiar o caminho completo** . O caminho para o certificado (ficheiro. cer) é copiado para a área de transferência.

    >[AZURE.IMPORTANT] Para publicar a aplicação Azure a partir do Visual Studio, tem de carregar este certificado para o [portal clássica Azure](http://go.microsoft.com/fwlink/?LinkID=213885).

1. Para carregar o certificado do [Azure portal clássico](http://go.microsoft.com/fwlink/?LinkID=213885):

    1. Selecione a ligação do Azure Portal.

         O [portal clássica Azure](http://go.microsoft.com/fwlink/?LinkID=213885) abre.

    1. Inicie sessão no [portal clássica Azure](http://go.microsoft.com/fwlink/?LinkID=213885)e, em seguida, selecione o botão de **Serviços em nuvem** .

    1. Selecione o serviço de nuvem que lhe interessa.

        A página para o serviço abre.

    1. No separador **certificados** , selecione o botão **carregar** .

    1. Cole o caminho completo do ficheiro. cer que acabou de criar e, em seguida, introduza a palavra-passe que especificou.
