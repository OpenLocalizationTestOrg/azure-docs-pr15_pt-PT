<properties
     pageTitle="Utilizar o Azure CDN | Microsoft Azure"
     description="Este tópico mostra como ativar a rede de entrega de conteúdos (CDN) para Azure. O tutorial explica a criação de um novo perfil CDN e o ponto final."
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
     ms.topic="get-started-article"
     ms.date="07/28/2016" 
     ms.author="casoper"/>

# <a name="using-azure-cdn"></a>Utilizar o Azure CDN  

Este tópico explica ativar Azure CDN ao criar um novo perfil CDN e ponto final.

>[AZURE.IMPORTANT] Para uma introdução à forma como CDN funciona bem como uma lista de funcionalidades, consulte o artigo [Descrição geral de CDN](./cdn-overview.md).

## <a name="create-a-new-cdn-profile"></a>Criar um novo perfil CDN

Um perfil CDN é uma coleção de pontos finais de CDN.  Cada perfil contém um ou mais pontos finais CDN.  Pode optar por utilizar vários perfis para organizar os pontos finais de CDN ao domínio da internet, aplicação web ou por outros critérios algumas.

> [AZURE.NOTE] Por predefinição, uma subscrição do Azure única está limitada aos perfis CDN oito. Cada perfil CDN está limitado a pontos finais de dez CDN.
>
> Preços de CDN são aplicado ao nível de perfil de CDN. Se pretender utilizar uma mistura de camadas comparar Azure CDN, terá de vários perfis CDN.

[AZURE.INCLUDE [cdn-create-profile](../../includes/cdn-create-profile.md)]

## <a name="create-a-new-cdn-endpoint"></a>Criar um novo ponto final CDN

**Para criar um novo ponto final CDN**

1. No [Portal do Azure](https://portal.azure.com), navegue até ao seu perfil CDN.  Pode pode ter afixada-lo para o dashboard no passo anterior.  Se não, pode encontrá-lo clicando em **Procurar**, em seguida, **perfis CDN**e clicar no perfil que pretende adicionar o seu ponto final para.

    É apresentada a pá de perfil CDN.

    ![Perfil CDN][cdn-profile-settings]

2. Clique no botão **Adicionar ponto final** .

    ![Adicionar botão de ponto final][cdn-new-endpoint-button]

    É apresentada a pá de **Adicionar um ponto final** .

    ![Adicionar pá de ponto final][cdn-add-endpoint]

3. Introduza um **nome** para este ponto final CDN.  Este nome será utilizada para aceder aos seus recursos em cache no domínio `<endpointname>.azureedge.net`.

4. Na lista pendente **tipo de origem** , selecione o tipo de origem.  Selecione **armazenamento** para uma conta de armazenamento do Windows Azure, **serviço na nuvem** para um serviço em nuvem Azure, **Web App** para um Azure Web App ou **origem personalizada** para qualquer outra web acessível publicamente servidor origem (hospedada no Azure ou noutro local).

    ![Tipo de origem CDN](./media/cdn-create-new-endpoint/cdn-origin-type.png)
        
5. Na lista pendente **origem hostname** , selecione ou escreva o seu domínio de origem.  No menu pendente irá listar todas as origens disponíveis do tipo que especificou no passo 4.  Se tiver seleccionado *origem personalizada* como a sua **origem escreva**, vou escrever o domínio da sua origem personalizado.

6. Na caixa de texto **caminho de origem** , introduza o caminho para os recursos que pretende colocar em cache, ou deixar em branco para permitir a cache de qualquer recurso no domínio que especificou no passo 5.

7. No **cabeçalho de anfitrião de origem**, introduza o cabeçalho de anfitrião que pretende CDN para enviar com cada pedido ou, deixe a predefinição.

    > [AZURE.WARNING] Alguns tipos de origens diferentes, como o armazenamento do Windows Azure e aplicações Web, requerem o cabeçalho de anfitrião para fazer corresponder o domínio da origem. Se não tiver uma origem de que necessita de um cabeçalho de anfitrião diferente a partir do seu domínio, deverá deixe o valor predefinido.

8. **Protocolo** e **porta de origem**, especifique os protocolos e portas utilizadas para aceder ao seu recursos na origem.  Tem de estar selecionado, pelo menos, um protocolo (HTTP ou HTTPS).
    
    > [AZURE.NOTE] A **porta de origem** afeta apenas o que porta as utilizações de ponto final para obter informações a partir da origem.  O ponto final propriamente dita só estará disponível para os clientes de fim na predefinição HTTP e portas HTTPS (80 e 443), independentemente da **porta de origem**.  
    >
    > Os pontos finais de **CDN Azure a partir do Akamai** não permitir o intervalo de portas TCP completo para origens diferentes.  Para obter uma lista de portas de origem que não são permitidos, consulte o artigo [CDN Azure a partir de portas de origem do Akamai permitidas](https://msdn.microsoft.com/library/mt757337.aspx).  
    >
    > Aceder a CDN conteúdo utilizando HTTPS tem as seguintes restrições:
    > 
    > - Tem de utilizar o certificado SSL fornecido pela CDN. Certificados de terceiros não são suportados.
    > - Tem de utilizar o domínio de CDN fornecido pela (`<endpointname>.azureedge.net`) para aceder a conteúdo HTTPS. Suporte HTTPS não está disponível para nomes de domínio personalizado (CNAMEs), uma vez que a CDN não suporta certificados personalizados neste momento.

9. Clique no botão **Adicionar** para criar o novo ponto final.

10. Quando o ponto final estiver criado, aparece uma lista de pontos finais para o perfil. A vista de lista mostra o URL para o utilizar para aceder a conteúdo em cache, bem como o domínio de origem.

    ![Ponto final CDN][cdn-endpoint-success]

    > [AZURE.IMPORTANT] O ponto final não imediatamente estará disponível para utilização, como demora tempo para o registo a serem propagadas através de CDN.  Perfis de <b>CDN Azure a partir do Akamai</b> , de propagação normalmente irá concluir dentro de um minuto.  Perfis de <b>CDN Azure a partir do Verizon</b> , de propagação normalmente irá concluir no prazo de 90 minutos, mas em alguns casos, pode demorar mais tempo.
    >    
    > Os utilizadores que tentarem para utilizar o nome de domínio CDN antes da configuração de ponto final tem propagado aos POP irão receber HTTP 404 códigos de resposta.  Se tiver sido várias horas uma vez que criou o seu ponto final e ainda estiver a receber 404 respostas, consulte o artigo [resolução de problemas de CDN pontos finais de devolver 404 Estados](cdn-troubleshoot-endpoint.md).


##<a name="see-also"></a>Consulte também
- [Controlar o comportamento de colocação em cache de pedidos de cadeias de consulta](cdn-query-string.md)
- [Como mapear CDN conteúdo para um domínio personalizado](cdn-map-content-to-custom-domain.md)
- [Carregar previamente elementos de um ponto final Azure CDN](cdn-preload-endpoint.md)
- [Limpar um ponto final Azure CDN](cdn-purge-endpoint.md)
- [Resolução de problemas de pontos finais CDN devolver 404 Estados](cdn-troubleshoot-endpoint.md)

[cdn-profile-settings]: ./media/cdn-create-new-endpoint/cdn-profile-settings.png
[cdn-new-endpoint-button]: ./media/cdn-create-new-endpoint/cdn-new-endpoint-button.png
[cdn-add-endpoint]: ./media/cdn-create-new-endpoint/cdn-add-endpoint.png
[cdn-endpoint-success]: ./media/cdn-create-new-endpoint/cdn-endpoint-success.png
