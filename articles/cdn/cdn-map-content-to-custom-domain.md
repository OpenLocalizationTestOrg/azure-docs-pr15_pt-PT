<properties
     pageTitle="Como mapear o conteúdo da rede (CDN) do Azure entrega de conteúdos para um domínio personalizado | Microsoft Azure"
     description="Este tópico demonstram como mapear uma conteúdo CDN para um domínio personalizado."
     services="cdn"
     documentationCenter=""
     authors="camsoper"
     manager="erikre"
     editor=""/>
<tags
     ms.service="cdn"
     ms.workload="media"
     ms.tgt_pltfrm="na"
     ms.devlang="na"
     ms.topic="article"
    ms.date="07/28/2016"
     ms.author="casoper"/>

# <a name="how-to-map-custom-domain-to-content-delivery-network-cdn-endpoint"></a>Como mapear domínio personalizado para o ponto final de rede de entrega de conteúdos (CDN)
Pode mapear um domínio personalizado para um ponto final de CDN para poder utilizar o seu próprio nome de domínio no URLs para o conteúdo em cache em vez de utilizar um subdomínio do azureedge.net.

Existem duas formas de mapear o seu domínio personalizado para um ponto final de CDN:

1. [Criar um registo CNAME com a sua entidade de registo de domínio e mapear o seu domínio personalizado e subdomínio para o ponto final de CDN](#register-a-custom-domain-for-an-azure-cdn-endpoint)

    Um registo CNAME é uma funcionalidade DNS que mapas, como um domínio de origem, `www.contosocdn.com` ou `cdn.contoso.com`, para um domínio de destino. Neste caso, o domínio de origem é o seu domínio personalizado e subdomínio (um subdomínio, como **www** ou **cdn** sempre é necessário). O domínio de destino é o ponto final de CDN.  

    O processo de mapeamento do seu domínio personalizado para o ponto final de CDN, no entanto, resultar num breve período de tempo de inatividade do domínio enquanto está a registar o domínio no Portal do Azure.

2. [Adicionar um passo intermédio registo com **cdnverify**](#register-a-custom-domain-for-an-azure-cdn-endpoint-using-the-intermediary-cdnverify-subdomain)

    Se o seu domínio personalizado está atualmente a suportar uma aplicação com um contrato de nível de serviço (SLA) que requer que não existem ser sem tempo de inatividade, pode utilizar o subdomínio Azure **cdnverify** para fornecer um passo intermédio registo para que os utilizadores poderão aceder ao seu domínio enquanto o DNS mapeamento demora local.  

Após registar o seu domínio personalizado utilizando um dos procedimentos acima, irá pretende [Certifique-se de que o subdomínio personalizado referencia o ponto final de CDN](#verify-that-the-custom-subdomain-references-your-cdn-endpoint).

> [AZURE.NOTE] Tem de criar um registo CNAME com a sua entidade de registo de domínio para mapear o seu domínio para o ponto final de CDN. Registos CNAME mapeiam subdomínios específicos, tais como `www.contoso.com` ou `cdn.contoso.com`. Não é possível mapear um registo CNAME para um domínio de raiz, tais como `contoso.com`.
>    
> Um subdomínio apenas pode ser associado um ponto final de CDN. O registo CNAME que criar serão encaminha todo o tráfego destinado para o subdomínio para o ponto final especificado.  Por exemplo, se associar `www.contoso.com` com o seu ponto final de CDN, em seguida, não pode associá-lo com outros pontos finais Azure, tal como um ponto final de conta de armazenamento ou um ponto final de serviço de nuvem. No entanto, pode utilizar subdomínios diferentes a partir do mesmo domínio para os pontos finais de serviços diferente. Também pode mapear subdomínios diferentes para o mesmo ponto final de CDN.
>
> Para os pontos finais de **CDN Azure a partir do Verizon** (padrão e Premium), tenha em atenção que ocupa a **90 minutos** para as alterações de domínio personalizado a serem propagadas para nós de limite CDN.

## <a name="register-a-custom-domain-for-an-azure-cdn-endpoint"></a>Registe-se um domínio personalizado para um ponto final Azure CDN

1.  Iniciar sessão no [Portal do Azure](https://portal.azure.com/).
2.  Clique em **Procurar**, em seguida, **CDN perfis**, em seguida, os perfis CDN com o ponto final que pretende mapear para um domínio personalizado.  
3.  Na pá **CDN perfil** , clique no ponto final de CDN com o qual pretende associar o subdomínio.
4.  Na parte superior da pá ponto final, clique no botão **Adicionar domínio personalizado** .  Pá **Adicionar um domínio personalizado** , verá o nome do anfitrião ponto final, derivado a partir do seu ponto final de CDN, para utilizar na criação de um novo registo CNAME. O formato do endereço do nome do anfitrião irá aparecer como ** &lt;EndpointName >. azureedge.net**.  Pode copiar este nome de anfitrião para utilizar em criar o registo CNAME.  
5.  Navegue até ao web site da sua entidade e localize a secção para criar registos DNS. Poderá encontrar esta numa secção como o **Nome de domínio**, **DNS**ou **Gestão de servidor de nomes**.
6.  Localize a secção para gerir CNAMEs. Poderá ter de aceder a uma página de definições avançadas e procure as palavras, CNAME, Alias ou subdomínios.
7.  Crie um novo registo CNAME que mapeie o subdomínio que selecionou (por exemplo, **www** ou **cdn**) para o nome do anfitrião fornecido no pá a **Adicionar um domínio personalizado** .
8.  Regressar ao pá a **Adicionar um domínio personalizado** e introduza o seu domínio personalizado, incluindo o subdomínio, na caixa de diálogo. Por exemplo, introduza o nome de domínio no formato `www.contoso.com` ou `cdn.contoso.com`.   

    Azure verificará que o registo CNAME existe para o nome do domínio que introduziu. Se o CNAME está correto, o seu domínio personalizado é validado.  Para os pontos finais de **CDN Azure a partir do Verizon** (padrão e Premium), poderá demorar até 90 minutos para as definições de domínio personalizado a serem propagadas para todos os nós de limite CDN, no entanto.  

    Tenha em atenção que em alguns casos, pode demorar algum tempo para o registo CNAME a serem propagadas para os servidores de nomes na Internet. Se o seu domínio não está a validar imediatamente e pensa que o registo CNAME está correto, em seguida, aguarde alguns minutos e tente novamente.


## <a name="register-a-custom-domain-for-an-azure-cdn-endpoint-using-the-intermediary-cdnverify-subdomain"></a>Registe-se um domínio personalizado para um ponto final de Azure CDN utilizando o subdomínio cdnverify intermédio  

1. Iniciar sessão no [Portal do Azure](https://portal.azure.com/).
2. Clique em **Procurar**, em seguida, **CDN perfis**, em seguida, os perfis CDN com o ponto final que pretende mapear para um domínio personalizado.  
3. Na pá **CDN perfil** , clique no ponto final de CDN com o qual pretende associar o subdomínio.
4. Na parte superior da pá ponto final, clique no botão **Adicionar domínio personalizado** .  Pá **Adicionar um domínio personalizado** , verá o nome do anfitrião ponto final, derivado a partir do seu ponto final de CDN, para utilizar na criação de um novo registo CNAME. O formato do endereço do nome do anfitrião irá aparecer como ** &lt;EndpointName >. azureedge.net**.  Pode copiar este nome de anfitrião para utilizar em criar o registo CNAME.
5. Navegue até ao web site da sua entidade e localize a secção para criar registos DNS. Poderá encontrar esta numa secção como o **Nome de domínio**, **DNS**ou **Gestão de servidor de nomes**.
6. Localize a secção para gerir CNAMEs. Poderá ter de aceder a uma página de definições avançadas e procure as palavras **CNAME**, **Alias**ou **subdomínios**.
7. Crie um novo registo CNAME e fornecer um alias de subdomínio que inclui o subdomínio **cdnverify** . Por exemplo, o subdomínio que especificar será no formato **cdnverify.www** ou **cdnverify.cdn**. Em seguida, fornecer o nome de anfitrião, que é o ponto final de CDN, no formato de **cdnverify.&lt; EndpointName >. azureedge.net**.
8. Regressar ao pá a **Adicionar um domínio personalizado** e introduza o seu domínio personalizado, incluindo o subdomínio, na caixa de diálogo. Por exemplo, introduza o nome de domínio no formato `www.contoso.com` ou `cdn.contoso.com`. Tenha em atenção que neste passo, não necessita preceda o subdomínio com **cdnverify**.  

    Azure verificará que o registo CNAME existe para o nome de domínio cdnverify que introduziu.
9. Neste momento, o seu domínio personalizado, tenha sido verificado pela Azure, mas o tráfego para o seu domínio ainda não está a ser encaminhado para o ponto final CDN. Depois da aguardar tempo suficiente para permitir que as definições de domínio personalizado a serem propagadas para os nós de limite CDN (90 minutos para **CDN Azure a partir do Verizon**, 1-2 minutos para que **CDN Azure a partir do Akamai**), regresse ao web site do seu DNS entidade de registo e criar outro registo CNAME que mapas do seu subdomínio para o ponto final CDN. Por exemplo, especificar o subdomínio como **www** ou **cdn**e o nome do anfitrião como ** &lt;EndpointName >. azureedge.net**. Este passo, o registo do seu domínio personalizado está concluído.
10. Por fim, pode eliminar o registo CNAME criada utilizando **cdnverify**, tal como estava necessária apenas como um passo intermédio.  


## <a name="verify-that-the-custom-subdomain-references-your-cdn-endpoint"></a>Certifique-se de que o subdomínio personalizado referencia o ponto final de CDN

- Depois de concluir o registo do seu domínio personalizado, pode aceder a conteúdo que é colocada em cache no seu ponto final CDN utilizando o domínio personalizado.
Em primeiro lugar, certifique-se de que tem público conteúdo que é colocada em cache no ponto final. Por exemplo, se o seu ponto final de CDN está associado uma conta de armazenamento, a CDN coloca em cache conteúdo em contentores de BLOBs público. Para testar o domínio personalizado, certifique-se de que o contentor está definido para permitir o acesso de público e que contém pelo menos uma blob.
- No seu browser, navegue para o endereço de BLOBs utilizando o domínio personalizado. Por exemplo, se for o seu domínio personalizado `cdn.contoso.com`, o URL para um blob em cache será semelhante ao seguinte URL: http://cdn.contoso.com/mypubliccontainer/acachedblob.jpg

## <a name="see-also"></a>Consulte também

[Como pode ativar a rede de entrega de conteúdos (CDN) para Azure](./cdn-create-new-endpoint.md)  
