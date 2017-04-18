<properties
   pageTitle="Pré-requisitos não técnicas para criar uma oferta para Azure Marketplace | Microsoft Azure"
   description="Compreenda os requisitos para criar e implementar uma oferta para Azure Marketplace para outras pessoas para comprar."
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager="hascipio"
   editor=""/>

<tags
  ms.service="marketplace"
  ms.devlang="na"
  ms.topic="article"
  ms.tgt_pltfrm="Azure"
  ms.workload="na"
  ms.date="08/18/2016"
  ms.author="hascipio"/>

# <a name="general-prerequisites-for-creating-an-offer-for-the-azure-marketplace"></a>Pré-requisitos gerais para criar uma oferta para Azure Marketplace
Compreenda os pré-requisitos gerais, empresas processo-centrados em que são necessários para avançar com o processo de criação de oferta.

## <a name="ensure-that-you-are-registered-as-a-seller-with-microsoft"></a>Certifique-se de que estão registados como um vendedor com o Microsoft
Para obter instruções detalhadas sobre como registar uma conta de vendedor com a Microsoft, aceda a [criação de contas e registo](marketplace-publishing-accounts-creation-registration.md).

- **Se a sua empresa já está registada como um vendedor no Centro de Dev Center e que pretende criar uma nova oferta** , em seguida, inicie sessão de publicação portal com o mesmo id de e-mail com que Dev Center do registo é feito. Este passo é necessário para que o portal de publicação e Dev Center do estão ligadas com os outros.
- **Se a sua empresa já está registada como um vendedor no Centro de Dev Center e que pretende editar uma oferta existente,** em seguida, o início de sessão de publicação portal com a conta de administrador ou com uma conta que é adicionada como um administrador de co de publicação portal. Passos para adicionar uma conta de administrador co é dado abaixo.

## <a name="steps-to-add-a-co-admin-in-the-publishing-portal"></a>Passos para adicionar um administrador co no Portal de publicação
Os administradores de publicação portal pode adicionar outros membros da empresa, que estiver a trabalhar na aplicação, como um administrador de co de publicação portal. **Partindo do princípio que for o administrador,** tendo em conta abaixo são os passos para adicionar um co-administrador.

>[AZURE.NOTE] Para os novos utilizadores, antes de adicionar um administrador co de publicação portal, certifique-se de que ter criado pelo menos uma aplicação de publicação portal. Isto é necessário, tal como o separador **fabricantes de** ser apresentado apenas depois de criar pelo menos uma aplicação de publicação portal.

1. Certifique-se de que o id de correio eletrónico de administração co é uma account(MSA) da Microsoft. Caso contrário, registe-lo como um MSA utilizando esta [ligação](https://signup.live.com/signup?uaid=0089f09ccae94043a0f07c2aaf928831&lic=1).
2. Certifique-se de que existe pelo menos uma aplicação da conta de administrador antes de tentar adicionar um co-administrador.
3. Depois de concluir os passos acima, início de sessão de publicação portal com o id de correio eletrónico de co-admin e, em seguida, terminar sessão.
4. Agora início de sessão de publicação portal com o id de correio electrónico do administrador.
5. Navegue para fabricantes-selecione a sua conta -> > administradores -> Adicionar administrador co (captura de ecrã tendo em conta abaixo)

    ![desenho](media/marketplace-publishing-pre-requisites/imgAddAdmin_05.png)

6. Certifique-se de que os ids de e-mail fornecidos nas diferentes fases do processo de publicação (por exemplo, Dev Center do, portal de publicação) são monitorizados para qualquer comunicação da Microsoft.
7. Para o registo do Centro de Dev Center, evite utilizar uma conta associada a uma única pessoa. Isto é sugerido para remover dependência a partir de um indivíduo.
8. Se cara quaisquer problemas com o registo do Centro de Dev Center, em seguida, introduza aumente uma permissão de utilizar esta [ligação](https://developer.microsoft.com/en-us/windows/support).

## <a name="steps-to-delete-a-co-admin-in-the-publishing-portal"></a>Passos para eliminar um administrador de co de publicação portal
**Partindo do princípio que for o administrador,** tendo em conta abaixo são os passos para eliminar um co-administrador.

1. Início de sessão de publicação portal com o id de correio electrónico do administrador.
2. Navegue para **fabricantes** -> selecionar **administradores**-> a sua conta -> **Co-administradores**.
3. Clique no botão **X** junto ao administrador de co que pretende eliminar Total assegurado (captura de ecrã tendo em conta abaixo).

    ![desenho](media/marketplace-publishing-pre-requisites/imgDeleteAdmin_03.png)

> [AZURE.IMPORTANT] Não tem de concluir informações da empresa impostos e banco se está a planear a publicar ofertas apenas livres (ou trazer o seu próprio licença).

> O registo de empresa têm de ser concluído para começar a utilizar. No entanto, enquanto a sua empresa funciona nas informações na conta Microsoft Developer fiscal e banco, os programadores podem começar a trabalhar em criar a imagem de máquina virtual no [Portal de publicação](https://publish.windowsazure.com), mesmo certificadas, introdução e testá-la no ambiente de teste Azure. Terá a aprovação de conta vendedor concluída apenas para o passo final da sua oferta de publicação ao Azure Marketplace.

## <a name="acquire-an-azure-pay-as-you-go-subscription"></a>Adquirir uma subscrição "repartição" Azure
Esta é a subscrição que irá utilizar para criar as suas imagens VM distribuí sobre as imagens para o [Azure Marketplace](https://azure.microsoft.com/marketplace/). Se não tiver uma subscrição existente, em seguida, inscreva-se na https://account.windowsazure.com/signup?offer=ms-azr-0003p.

## <a name="sell-from-countries"></a>Países/regiões "Vender de"
> [AZURE.WARNING]
Para poder vender os seus serviços no Azure Marketplace, deve certificar-se de que a sua entidade registada é a partir de um dos países aprovado "vender-de". Esta restrição é por razões payout e fiscal. Estamos ativamente à procura para expandir esta lista de países/regiões no futuro próximo, pelo que esteja atento. Para a lista completa, consulte a secção 1b das [políticas de participação do Azure Marketplace](http://go.microsoft.com/fwlink/?LinkID=526833).

## <a name="next-steps"></a>Próximos passos
Depois das pré-requisitos não técnicas são preenchidas, seguinte está a oferta de pré-requisitos técnicos específicos. Clique na ligação para o artigo para o tipo de oferta respetivos que pretende criar para Azure Marketplace.

- [Pré-requisitos técnicos do VM](marketplace-publishing-vm-image-creation-prerequisites.md)
- [Solução modelo técnicos pré-requisitos](marketplace-publishing-solution-template-creation-prerequisites.md)

## <a name="see-also"></a>Consulte também
- [Introdução: como publicar uma oferta ao Azure Marketplace](marketplace-publishing-getting-started.md)
