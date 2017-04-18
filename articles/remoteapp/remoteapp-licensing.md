<properties
    pageTitle="Azure RemoteApp licenciamento | Microsoft Azure"
    description="Saiba como funciona o licenciamento no Azure RemoteApp."
    services="remoteapp"
    documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="08/15/2016"
    ms.author="elizapo" />


# <a name="how-does-licensing-work-in-azure-remoteapp"></a>Como licenciamento funciona RemoteApp do Azure?

> [AZURE.IMPORTANT]
> Está a ser descontinuada Azure RemoteApp. Leia o [anúncio](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.

Assim que tiver configurado o seu serviço de RemoteApp do Azure, criado os seus modelos e está pronto para publicar aplicações aos seus utilizadores. Mas ainda existe um critério último descobrir: licenciamento. Tal como é que o trabalho de licenciamento para RemoteApp e para as aplicações que partilha através de RemoteApp?

RemoteApp não requer qualquer licenças do Windows ou CAL de ambiente de trabalho remoto. A sua subscrição leva-o até cuidado da parte lateral RemoteApp própria. (Veja os detalhes do [preços planos](https://azure.microsoft.com/pricing/details/remoteapp)).

Se utilizar uma das imagens que está incluído na sua subscrição, pode partilhar das aplicações instaladas nessa imagem sem necessitar de uma licença em separado. Por exemplo, se utilizar a imagem do Windows Server 2012 R2 modelo para criar a sua coleção, pode partilhar sistema Centro Endpoint Protection com os seus utilizadores. As exceções apenas a esta regra são Office 365 ProPlus, solicitar uma subscrição separada, e Office 2013, que não podem ser partilhados numa coleção de produção.

Se pretender utilizar a imagem de modelo do Office 365 que vem com RemoteApp, tem de ter um plano do Office 365 ProPlus *existente* . O mesmo se VERDADEIRO para qualquer aplicação do Office 365 que publicar utilizando um modelo personalizado. Tem de ativar as aplicações com a sua própria subscrição. Esta situação for verdadeira para subscrições de avaliação ou pagas. Se pretender utilizar a imagem de modelo do Office 365 durante a versão de avaliação, *e ainda não tiver uma subscrição*, aceda à página do Office 365 para [se inscrever](https://go.microsoft.com/fwlink/p/?LinkID=403802) para uma subscrição de avaliação. Consulte o artigo [como RemoteApp e o Office funcionam em conjunto?](remoteapp-o365.md) para obter mais informações.

Se, durante o período de avaliação, que não pretende obter uma subscrição de avaliação do Office 365, utilize o Office 2013 Professional Plus modelo da imagem que vem com RemoteApp. Esta imagem de modelo só pode ser utilizada para 30 dias e não pode ser convertida numa coleção de paga.

Outras aplicações, terá para se certificar de que tem a licença para partilhar a aplicação.

Que faz sentido, à direita? Pode publicar qualquer aplicação que está por motivos legais elegíveis para partilhar. E precisa para se certificar de que realmente têm direito para partilhar os seus programas.

Tenha em atenção que não é possível utilizar um acordo CAL ou licença de Volume uma coleção de nuvem. *Pode* utilizar um contrato de licença de Volume para ativar o aplicações na sua coleção de híbrido (exceto o Office). Só precisa de instalá-los na sua imagem do modelo a partir do suporte de licenciamento em Volume. Siga as informações a partir do fornecedor da aplicação para instalar licenças num ambiente do ambiente de trabalho remoto.
