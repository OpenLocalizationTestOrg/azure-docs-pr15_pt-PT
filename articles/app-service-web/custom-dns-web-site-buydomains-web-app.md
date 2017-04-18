<properties
    pageTitle="Como adquirir um nome de domínio personalizado no Azure aplicação de serviço Web Apps"
    description="Saiba como adquirir um nome de domínio personalizado com uma aplicação web na aplicação de serviço de Azure."
    services="app-service\web"
    documentationCenter=""
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service-web"
    ms.workload="web"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="robmcm"/>

# <a name="buy-and-configure-a-custom-domain-name-in-azure-app-service"></a>Comprar e configurar um nome de domínio personalizado no serviço de aplicação do Azure

[AZURE.INCLUDE [web-selector](../../includes/websites-custom-domain-selector.md)]

Quando cria uma aplicação web, o Azure atribui-lhe para um subdomínio do azurewebsites.net. Por exemplo, se a sua aplicação web é com o nome **contoso**, o URL é **contoso.azurewebsites.net**. Azure também atribui um endereço IP virtual.

Para uma aplicação web de produção, provavelmente pretende que os utilizadores para ver um nome de domínio personalizado. Este artigo explica como comprar e configurar um domínio personalizado com a [Aplicação de serviço Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714). 

[AZURE.INCLUDE [introfooter](../../includes/custom-dns-web-site-intro-notes.md)]


## <a name="overview"></a>Descrição geral

Se não tiver um nome de domínio para a sua aplicação web, pode facilmente comprar um no [Portal do Azure](https://portal.azure.com/). Durante o processo de compra pode optar por ter registos DNS do WWW e raiz do domínio mapeados para a sua aplicação web automaticamente. Também pode gerir o seu direito de domínio no interior do Azure Portal.


Utilize os passos seguintes para comprar nomes de domínio e atribuir a sua aplicação web.

1. No seu browser, abra o [Portal do Azure](https://portal.azure.com/).

2. No separador **Web Apps** , clique no nome da sua aplicação web, selecione **Definições**e, em seguida, selecione **domínios personalizados**

    ![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-6.png)

3. Na pá **domínios personalizados** , clique em **comprar domínios**.

    ![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-1.png)

4. No pá **Comprar domínios** , utilize a caixa de texto para escrever o nome de domínio que pretende comprar e prima Enter. Os domínios disponíveis sugeridos serão apresentados imediatamente abaixo da caixa de texto. Selecione o domínio que pretende comprar. Pode escolher comprar vários domínios ao mesmo tempo. 

  ![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-2.png)

5. Clique nas **Informações de contacto** e preencha o formulário de informações de contacto do domínio.

  ![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-3.png)

    > [AZURE.NOTE] É muito importante que preencha todos os campos obrigatórios com uma precisão quanto possível, especialmente o endereço de e-mail. Em caso de adquirir o domínio sem o "Proteção de privacidade", poderá ser-lhe pedido para confirmar o seu e-mail antes do domínio fica ativo. Em alguns casos, dados incorreta para informações de contacto irão resultar numa falha para comprar domínios. 

6. Agora pode escolher,

    um) "renovação" o domínio todos os anos
    
    b) optar ativamente para "Proteção de privacidade" que está incluída no preço de compra gratuitamente (exceto TLDs quem registo fazer não suporta a privacidade. Por exemplo:. co.in,. co.uk, etc.)  
    
    c) "atribuir nomes de anfitriões predefinido" do WWW e domínio para a aplicação Web atual de raiz. 

  ![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-2.5.png)
  
    > [AZURE.NOTE] Opção C configura enlaces de DNS e Hostname enlaces automaticamente para si.  Desta forma, a aplicação Web pode ser acedida utilizar o domínio personalizado, logo a compra estiver concluída (baring atrasos de propagação de DNS em alguns casos). Caso, a aplicação Web do for atrás Azure tráfego Gestor, não verá uma opção para atribuir o domínio de raiz, tal como A registos não funcionam com o Gestor de tráfego. Pode sempre atribuir o domínios/suboperações-domains comprados através de um Web App para outra Web App e vice versa. Consulte o artigo passo 8 para obter mais detalhes. 
    
7. Clique na **Selecionar** no pá **Domínios comprar** e, em seguida, irá ver as informações de aquisição no pá **confirmação de compra** . Se aceitar os termos legais e clique em **comprar**, será apresentada sua encomenda e que pode monitorizar o processo de compra **notificação**. Comprar pacote de domínio pode demorar alguns minutos a concluir. 

  ![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-4.png)

  ![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-5.png)

8. Se encomendou com êxito um domínio, pode gerir o domínio e atribuir a sua aplicação web. Clique na **"…"** no lado direito do seu domínio. Em seguida, pode **Cancelar a compra** ou **domínio de gerir**. Clique em **Gerir domínio**, em seguida, podemos vincular **subdomínio** ao nosso web app no **domínio de gerir** pá. Se pretende vincular um **subdomínio** para uma aplicação Web diferentes, em seguida, execute este passo de dentro do contexto do respetivos Web App. Sobre aqui pode selecionar para o domínio para o ponto final do Gestor de tráfego (se atribuir Web App está atrás TM) por Gestor de tráfego simplesmente seleccionando o nome a partir do menu pendente. Ao fazê-lo, domínio/subdomínio será atribuído automaticamente para todas as aplicações de Web atrás esse ponto final do Gestor de tráfego. 

    ![](./media/custom-dns-web-site-buydomains-web-app/dncmntask-cname-buydomains-6.png)

    > [AZURE.NOTE] Pode "Cancelar compra" dentro de 5 dias para reembolso completo. Depois de 5 dias que irá não conseguir "Cancelar compra", em vez disso, verá uma opção para "Eliminar" o domínio. Eliminar o domínio irá resultar em libertá-lo da sua subscrição sem reembolso e irão tornar-se o domínio disponíveis. 

Assim que tiver concluído a configuração, o nome de domínio personalizado será listado na secção de **Hostname enlaces** da sua aplicação web.

Neste momento, deverá conseguir introduza o nome de domínio personalizado no seu browser e ver que-lo com êxito leva-o para a sua aplicação web.
 
## <a name="what-happens-to-the-custom-domain-you-bought"></a>O que acontece ao domínio personalizado que comprou

O domínio personalizado que comprou no pá de **domínios personalizados e SSL** está associado à subscrição Azure. Como um recurso Azure, este domínio personalizado é separados e independentes a partir da aplicação de serviço de aplicação que comprou o domínio para pela primeira vez. Isto significa que:

- No portal do Azure, pode utilizar o domínio personalizado que comprou de mais do que uma aplicação de serviço de aplicação e não apenas para a aplicação que comprou o domínio personalizado para o primeiro. 
- Pode gerir todos os domínios personalizados que comprou a subscrição Azure acedendo a pá **domínios personalizados e SSL** de *qualquer* aplicação de serviço de aplicação que subscrição.
- Pode atribuir qualquer aplicação do serviço de aplicação a partir da mesma subscrição do Azure a um subdomínio dentro desse domínio personalizado.
- Se decidir eliminar uma aplicação de serviço de aplicação, pode escolher não eliminar o domínio personalizado que está vinculado ao se pretender manter utilizá-lo para outras aplicações.

## <a name="if-you-cant-see-the-custom-domain-you-bought"></a>Se não conseguir ver o domínio personalizado que comprou

Se tiver comprado o domínio personalizado a partir do pá **domínios personalizados e SSL** , mas não consigo ver o domínio personalizado em **domínios gerido**, verifique se as seguintes coisas:

- Poderá não concluiu a criação de domínio personalizado. Verifique a campainha de notificação na parte superior do Azure portal para o progresso.
- A criação de domínio personalizado, pode ter falhado por algum motivo. Verifique a campainha de notificação na parte superior do Azure portal para o progresso.
- O domínio personalizado pode ter foi concluída com êxito mas o pá pode não ser atualizado ainda. Experimente voltar a abrir o pá **domínios personalizados e SSL** .
- Poderá ter eliminado o domínio personalizado em algumas ponto. Verificar os registos de auditoria clicando em **Definições** > **Registos de auditoria** de pá principal da sua aplicação. 
- Pode pertencer a pá **domínios personalizados e SSL** que está à procura de uma aplicação que é criada uma subscrição do Azure diferente. Mudar para outra aplicação numa subscrição diferente e verifique a sua pá **domínios personalizados e SSL** .  
  No interior do portal, não será possível ver ou gerir domínios personalizados que criou numa subscrição do Azure diferente que a aplicação. No entanto, se clicar em **Gestão avançada** pá de **domínio de gerir** o domínio, será redirecionado para o site do fornecedor de domínio onde poderá configurar   [manualmente](web-sites-custom-domain-name.md) o seu domínio personalizado como qualquer domínio personalizado externo 
   para as aplicações que criou numa subscrição do Azure diferente. 


