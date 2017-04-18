
<properties
    pageTitle="Requisitos de imagem do Azure RemoteApp | Microsoft Azure"
    description="Saiba mais sobre os requisitos para a criação de imagens para ser utilizado com RemoteApp do Azure"
    services="remoteapp"
    documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016"
    ms.author="elizapo" />



# <a name="requirements-for-azure-remoteapp-images"></a>Requisitos para imagens RemoteApp do Azure

> [AZURE.IMPORTANT]
> Está a ser descontinuada Azure RemoteApp. Leia o [anúncio](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.

Azure RemoteApp utiliza uma imagem do Windows Server 2012 R2 para todos os programas que pretende partilhar com os utilizadores do anfitrião. Para criar uma imagem personalizada, pode começar com uma imagem existente ou [crie um novo](remoteapp-create-custom-image.md).

> [AZURE.TIP] Sabia que a sua subscrição do Azure RemoteApp dá-lhe acesso a uma imagem do Windows Server 2012 R2 na Galeria de Azure VM que pode utilizar para criar a sua própria imagem de modelo? [Dê saída](remoteapp-image-on-azurevm.md).  


Os requisitos para a imagem que podem ser carregados para utilização com o Azure RemoteApp são:


- Aplicações personalizadas não armazenam dados localmente na imagem. Estas imagens são sem estado e deverão conter apenas aplicações.
- A imagem não contiver dados que podem ser perdidos.
- O tamanho da imagem deve ser um múltiplo de MB. Se tentar carregar uma imagem que não é um múltiplo exato, o carregamento irá falhar.
- O tamanho da imagem tem de ser 127 GB ou mais pequeno.
- Tem de ser num ficheiro VHD (VHDX ficheiros não são atualmente suportados).
- O VHD não tem de ser uma máquina de virtual geração 2.
- O VHD pode ser tamanho fixo ou expansão dinâmica. Um VHD expansão dinâmica é recomendado porque demora menos tempo a carregar para o Azure que um ficheiro VHD tamanho fixo.
- O disco tem de ser inicializado utilizando o registo de arranque do modelo global (MBR) estilo de partição. O estilo de partições GUID partição (GPT tabela) não é suportado.
- O VHD tem de conter uma instalação do Windows Server 2012 R2 única. Pode conter vários volumes, mas apenas uma que contém uma instalação do Windows.
- A função de anfitrião de sessão de ambiente de trabalho remoto (RDSH) e a funcionalidade Experiência de ambiente de trabalho tem de estar instalados.
- A função corretor de ligação de ambiente de trabalho remoto tem *não* ser instalado.
- Tem de ser desactivado sistema de encriptação de ficheiro (EFS).
- A imagem tem de ser processada por Sysprep utilizando os parâmetros **/oobe / generalizar /shutdown** (não utilizar o parâmetro **/mode:vm** ).
- Carregar o VHD a partir de uma cadeia de instantâneo não é suportada.

Consulte o artigo [criar uma imagem de RemoteApp do Azure](remoteapp-imageoptions.md) para obter mais informações sobre a criação de imagens para RemoteApp do Azure.
