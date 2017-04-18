<properties 
    pageTitle="Azure RemoteApp FAQ | Microsoft Azure" 
    description="Obtenha respostas às mais perguntas mais frequentes sobre RemoteApp do Azure." 
    services="remoteapp" 
    documentationCenter="" 
    authors="lizap" 
    manager="swadhwa" 
    editor=""/>

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="get-started-article" 
    ms.date="08/15/2016" 
    ms.author="elizapo"/>

# <a name="azure-remoteapp-faq"></a>Perguntas mais frequentes do Azure RemoteApp

> [AZURE.IMPORTANT]
> Está a ser descontinuada Azure RemoteApp. Leia o [anúncio](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.

Seguem as seguintes questões sobre RemoteApp do Azure. Permitir que outras pessoas? Visite os [fóruns de RemoteApp](https://social.msdn.microsoft.com/Forums/azure/home?forum=AzureRemoteApp) e diga-no que precisa para conhecer ou pendente um comentário abaixo.

## <a name="cant-find-what-youre-looking-for-have-a-question-we-didnt-answer"></a>Não consegue encontrar o que está a procura? Tem uma pergunta que podemos não atender?
Se não conseguir encontrar as informações precisa ou tem uma pergunta adicional que recomendamos não está a abrangendo aqui, vá para o [Fórum RemoteApp do Azure](http://aka.ms/araforum) e faça a sua pergunta aí. Estamos sempre pode adicionar mais respostas aqui.

## <a name="what-is-azure-remoteapp"></a>O que é o Azure RemoteApp? ##


- **O que é o Azure RemoteApp?** RemoteApp é que um serviço Azure ajuda-o a fornecer acesso seguro e remoto às aplicações a partir de muitos dispositivos de utilizador diferentes. Leia mais sobre [RemoteApp do Azure](remoteapp-whatis.md).
- **Quais são as opções de implementação?** Existem dois tipos de colecções de RemoteApp: na nuvem e híbrido. Daquele que precisa depende de um número de fatores, como se precisar de associação de domínio. Falarmos sobre todas as decisões [aqui](remoteapp-collections.md).

## <a name="quick-tips-on-using-azure-remoteapp"></a>Sugestões rápidas sobre como utilizar RemoteApp do Azure ##
- **Quanto tempo até que estou a perder a ligação? Quanto tempo pode posso estar inactivo antes que também me o arranque?** 4 horas. Se que ou um dos seus utilizadores que idle 4 horas, será iniciada automaticamente terminar RemoteApp do Azure. Consulte o artigo as outras predefinições do [Azure subscrição e limites de serviço, Quotas e as restrições](../azure-subscription-service-limits.md).
- **Posso experimentar este serviço gratuito?** Sim. Existe uma versão de avaliação gratuita disponível para 30 dias. Depois das extremidades de avaliação, pode transição a uma conta paga (que pode utilizar em produção) ou deixar de utilizar o serviço. Iniciar a sua versão de avaliação gratuita ao aceder a [portal.azure.com](http://portal.azure.com) - criar uma nova instância do RemoteApp. Com a versão de avaliação gratuita, pode criar 2 instâncias do RemoteApp com 10 utilizadores por instância. Lembre-se de que esta versão de avaliação apenas encontra-se para 30 dias.
## <a name="azure-remoteapp-subscription-details"></a>Detalhes da subscrição RemoteApp Azure ##

- **Quais são os limites de serviço?** Pode obter informações sobre as predefinições e limites do serviço do Azure RemoteApp no [Azure subscrição e limites de serviço, Quotas e as restrições](../azure-subscription-service-limits.md). Diga-nos sabe se possui mais perguntas.
- **Quantos utilizadores têm de ter?** Existe um mínimo de 20 utilizadores. Deixar-me Repita que seja claro super - o mínimo é 20. Será efetuada para 20. 
- **Custo RemoteApp quanto o?** Consulte o artigo [Azure RemoteApp preços detalhes ](https://azure.microsoft.com/pricing/details/remoteapp/).
- **Um tipo de coleção de custo mais do que outra?** Sim, seja possível, dependendo do seu requisitos de coleções de sites. Uma coleção de híbrido requer uma ligação a partir do Azure RemoteApp à sua rede no local. Se utilizar uma rota VNET/Express existente, não existe nenhuma custo adicional. Mas, se utilizar um novo VNET do Azure e um gateway ou encaminhar Express, será cobrado para o [gateway VPN](https://azure.microsoft.com/pricing/details/vpn-gateway) ou [Encaminhar Express](https://azure.microsoft.com/pricing/details/expressroute/). Este custo (detalhado nas ligações de) está na parte superior de seu RemoteApp do Azure mensal de custo.

## <a name="collections---whats-supported-which-should-you-use-and-others"></a>Coleções - o que é suportado, qual deve utilizar e outras pessoas
- **Aplicações personalizadas linha de negócio (LOB) são suportadas?** Sim. Para utilizar uma aplicação personalizada RemoteApp do Azure, criar uma [imagem do modelo personalizado](remoteapp-create-custom-image.md)e, em seguida, carregue-o para a coleção de RemoteApp.
- **Minha aplicação LOB personalizada funcionam RemoteApp do Azure?** A melhor forma de figura que saída para testá-la. Consulte o artigo do [Centro de compatibilidade RD](http://www.rdcompatibility.com/compatibility/default.aspx).
- **Que método de implementação (na nuvem ou híbrido) é mais adequado para a minha organização?** Coleções de híbrido fornecem a experiência mais completa, se pretender que completo integração com o início de sessão único (SSO) e conectividade de rede seguro no local. Coleções de nuvem fornecem uma forma ágil e fácil de gerir para identificar a sua implementação utilizando vários métodos de autenticação. Leia mais sobre as [Opções de implementação](remoteapp-whatis.md).
- **Temos SQL ou outra base de dados quer no local ou no Azure. Que tipo de implementação devo utilizar?** Que depende onde está a sua base de dados SQL ou back-end. Se a base de dados estiver numa rede privada, use a coleção de híbrido. Se a base de dados está divulgada na Internet e permite que o cliente ligações para ligar à mesma, pode utilizar a coleção de nuvem.
- **O que sobre mapeamento de unidade, USB e porta série, partilha de área de transferência e redirecionamento de impressora?** Todas essas funcionalidades são suportadas no Azure RemoteApp. Redirecionamento de partilha e impressora da área de transferência está ativado por predefinição. Pode obter mais informações sobre o redirecionamento [aqui](remoteapp-redirection.md). 


## <a name="template-images"></a>Imagens de modelos
- **Pode utilizar uma nuvem ou máquina virtual existente como o modelo para minha coleção RemoteApp?** Sim! Pode criar uma imagem com base numa VM Azure, utilize uma das imagens incluídas com a sua subscrição ou criar uma imagem personalizada. Veja as [Opções de imagem RemoteApp](remoteapp-imageoptions.md).


## <a name="network-options"></a>Opções de rede
- **A coleção de híbrido requer um VNET. Vamos utilizar o nosso VNET existente?** Pode se o VNET existente é uma VNET Azure. Consulte o artigo "passo 1: configurar a rede virtual" nas [instruções de coleções de sites híbrido](remoteapp-create-hybrid-deployment.md) para obter mais informações.
- **Pode utilizar uma VNET uma coleção de nuvem?** Facto pode. Consulte o artigo [criar uma coleção de nuvem](remoteapp-create-cloud-deployment.md), especialmente passo 1, para obter mais informações.

## <a name="authentication-options"></a>Opções de autenticação



- **Sobre a autenticação? Quais os métodos são suportados?** A coleção de nuvem suporta contas do Microsoft e contas do Azure Active Directory, que são também contas do Office 365. A coleção de híbrido suporta apenas as contas do Azure Active Directory que foram sincronizadas (utilizar uma ferramenta de como o [Azure Active Directory Sync](http://blogs.technet.com/b/ad/archive/2014/09/16/azure-active-directory-sync-is-now-ga.aspx)) a partir de uma implementação do Active Directory do Windows Server; especificamente, seja sincronizado com a opção de sincronização de palavra-passe ou sincronizado com a Federação de serviços de Federação do Active Directory (AD FS) configurada. Também pode configurar [a autenticação Multifator (MFA)](https://azure.microsoft.com/services/multi-factor-authentication/).

>[AZURE.NOTE]Os utilizadores do Azure Active Directory tem de ser do inquilino de que está associada a sua subscrição. (Pode ver e modificar a sua subscrição no separador **Definições** no portal. Consulte o artigo [alterar o inquilino do Azure Active Directory utilizado pelo RemoteApp](remoteapp-changetenant.md) para obter mais informações.)

- **Por que motivo não consegue a fornecer acesso à minha conta Azure Active Directory?** Os utilizadores do Azure Active Directory tem de ser do diretório que está associada a sua subscrição. Pode ver ou modificar esse directório no separador definições no portal. Para mais informações, consulte [alterar o inquilino do Azure Active Directory utilizado pelo RemoteApp](remoteapp-changetenant.md) .

## <a name="clients---what-device-can-i-use-to-access-azure-remoteapp"></a>Os clientes - qual o dispositivo pode utilizar para aceder a RemoteApp do Azure?
Pode encontrar informações de cliente boa, incluindo os passos para instalar os clientes diferentes ao [aceder ao seu aplicações na RemoteApp do Azure](remoteapp-clients.md).

- **Os dispositivos e sistemas operativos as aplicações de cliente suportam?**
Primeiro, computadores e tablets: 
    - Windows 10 (pré-visualização de cliente)
    - Windows 8.1 e Windows 8
    - Windows 7 Service Pack 1
    - Mac OS X
    - Windows RT
    - Android tablets
    - para iPads e os telefones:
    - iPhone
    - Telemóvel Android
    - Windows Phone
 
    [Transferir](https://www.remoteapp.windowsazure.com/ClientDownload/AllClients.aspx) um cliente RemoteApp agora.
- **Azure RemoteApp suporta fina clientes?** Sim, são suportados os seguintes clientes finos incorporado do Windows:
    - Incorporados no Windows 7 padrão
    - Incorporados no Windows 8 padrão
    - Incorporados no Windows 8.1 indústria Pro
    - O Windows 10 IoT Enterprise

- **Qual a versão do Windows Server é suportada para ambiente de trabalho sessão anfitrião (RDSH) remoto?** Windows Server 2012 R2.

## <a name="support-and-feedback"></a>Suporte e comentários


- **O que é o plano de suporte para RemoteApp?** Suporte para a gestão de faturação e subscrição é fornecido nenhum custo. Suporte técnico está disponível através dos [planos de serviço Azure](https://azure.microsoft.com/support/plans/). Também pode obter suporte gratuito Comunidade através do nosso [Fórum de debate Azure](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=AzureRemoteApp). 
- **Como posso submeter comentários?** Visite o [Fórum de comentários](https://feedback.azure.com/forums/247748-azure-remoteapp/).
- **Quem pode falar Saiba mais sobre RemoteApp do Azure?** Para além do nosso [Fórum de debate](http://social.msdn.microsoft.com/Forums/windowsazure/home?forum=AzureRemoteApp), que é um ótimo local para publique perguntas, pode participar a semanal [Ask webinar de especialistas](https://azureinfo.microsoft.com/US-Azure-WBNR-FY15-11Nov-AzureRemoteAppAskTheExperts-Registration-Page.html), onde falarmos sobre todas as coisas RemoteApp.
- **E em relação a documentação de RemoteApp?** Estamos por isso, bem que perguntou. Para além dos conteúdos de ajuda a nível de ajuda do portal (basta clicar em **?** em qualquer página no portal do), os seguintes artigos estão disponíveis para ensinar tudo sobre RemoteApp:
    - **Introdução ao:**
        - [O que é RemoteApp?](remoteapp-whatis.md)
        - [Qual é das imagens dos modelos RemoteApp?](remoteapp-images.md)
        - [Como funciona o licenciamento?](remoteapp-licensing.md)
        - [Como RemoteApp e o Office funcionam em conjunto?](remoteapp-o365.md)
        - [Como redirecionamento funciona RemoteApp](remoteapp-redirection.md)?
    - **Implemente:**
        - [Criar uma imagem de modelo personalizado](remoteapp-create-custom-image.md)
        - [Criar uma coleção de híbrido](remoteapp-create-hybrid-deployment.md)
        - [Criar uma coleção de nuvem](remoteapp-create-cloud-deployment.md)
        - [Configure o Azure Active Directory para RemoteApp](remoteapp-ad.md)
        - [Publicar uma aplicação no RemoteApp](remoteapp-publish.md)
    - **Gerir:**
        - [Adicionar utilizadores](remoteapp-user.md)
        - [Práticas recomendadas para configurar e utilizar RemoteApp](remoteapp-bestpractices.md)  

    Vídeos! Recomendamos também têm um número de vídeos sobre o RemoteApp. Alguns fornecem introdução ([Introdução ao Azure RemoteApp](https://azure.microsoft.com/documentation/videos/cloud-cover-ep-150-azure-remote-app-with-thomas-willingham-and-nihar-namjoshi/)), enquanto outras pessoas guiá-lo implementação ([implementação na nuvem](https://www.youtube.com/watch?v=3NAv2iwZtGc&feature=youtu.be) e [implementação híbrida](https://www.youtube.com/watch?v=GCIMxPUvg0c&feature=youtu.be)). Veja-los!

 
### <a name="help-us-help-you"></a>Ajude-nos ajudá-lo 
Sabia que, para além de classificação deste artigo e fazer comentários para baixo abaixo, pode efetuar alterações ao artigo propriamente dito? Algo em falta? Algo errado? Escrever algo que é apenas confuso? Deslocar para cima e clique em **Editar no GitHub** para efetuar alterações - aqueles seja reencaminhado-para revisão e, em seguida, assim que iniciar a sessão nos mesmos, verá as alterações e melhorias para a direita aqui.
