<properties
    pageTitle="Resolução de problemas de compressão de ficheiros no Azure CDN | Microsoft Azure"
    description="Resolva problemas com a compressão de ficheiros de Azure CDN."
    services="cdn"
    documentationCenter=""
    authors="camsoper"
    manager="erikre"
    editor=""/>

<tags
    ms.service="cdn"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/01/2016"
    ms.author="casoper"/>
    
# <a name="troubleshooting-cdn-file-compression"></a>Resolução de problemas de compressão de ficheiros CDN

Este artigo ajuda-o a resolver problemas com a [compressão de ficheiros CDN](cdn-improve-performance.md).

Se precisar de mais ajuda em qualquer ponto neste artigo, pode contactar os Azure especialistas no [Azure o MSDN e os fóruns de pilha de conteúdo adicional](https://azure.microsoft.com/support/forums/). Em alternativa, também pode preencher um incidente de suporte Azure. Aceda ao [site de suporte do Azure](https://azure.microsoft.com/support/options/) e clique em **Obter suporte**.

## <a name="symptom"></a>Sintoma

Compressão para o ponto final está ativado, mas os ficheiros estão a ser devolvidos não comprimidos.

>[AZURE.TIP] Para verificar que se os seus ficheiros estão a ser devolvidos comprimidos, tem de utilizar uma ferramenta como [Fiddler](http://www.telerik.com/fiddler) ou do seu browser [Ferramentas de programador](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/).  Cabeçalhos de resposta de verificação o protocolo HTTP devolvidos com o seu CDN em cache conteúdos.  Se existir um cabeçalho chamado `Content-Encoding` com um valor de **gzip**, **bzip2**ou **Esvaziar**, o seu conteúdo está comprimido.
>
>![Cabeçalho de codificação de conteúdo](./media/cdn-troubleshoot-compression/cdn-content-header.png)

## <a name="cause"></a>Causa

Existem várias causas possíveis, incluindo:

- O conteúdo solicitado não é elegível para compressão.
- Compressão não estiver ativado para o tipo de ficheiro pedido.
- O pedido de HTTP não incluiu um cabeçalho a pedir um tipo de compressão válida.

## <a name="troubleshooting-steps"></a>Passos de resolução de problemas

> [AZURE.TIP] Tal como acontece com a implementar o pontos finais de novos, alterações na configuração CDN demorar algum tempo a serem propagadas através da rede.  Normalmente, as alterações são aplicadas no prazo de 90 minutos.  Se esta for a primeira vez que configurou compressão para o ponto final de CDN, deverá tomar em consideração de aguardar horas 1-2 para se certificar de que a compressão definições tenham propagada aos POP. 

### <a name="verify-the-request"></a>Verifique se o pedido

Em primeiro lugar, podemos deve fazer uma verificação de sanidade rápida sobre o pedido.  Pode utilizar [Ferramentas de programador](https://developer.microsoft.com/microsoft-edge/platform/documentation/f12-devtools-guide/) do seu browser para ver os pedidos a ser apresentados.

- Verifique se o pedido está a ser enviado para o seu URL de ponto final, `<endpointname>.azureedge.net`e não a sua origem.
- Verifique se o pedido contém um cabeçalho de **Codificação de aceitar** e o valor do cabeçalho dessa contiver **gzip**, **Esvaziar**ou **bzip2**.

> [AZURE.NOTE] Os perfis de **CDN Azure a partir do Akamai** apenas suportam a codificação **gzip** .

![Cabeçalhos de pedido CDN](./media/cdn-troubleshoot-compression/cdn-request-headers.png)

### <a name="verify-compression-settings-standard-cdn-profile"></a>Verifique se as definições de compressão (perfil CDN padrão)

> [AZURE.NOTE] Este passo só se aplica se o seu perfil CDN é um perfil do **Azure CDN padrão de Verizon** ou **Azure CDN padrão de Akamai** . 

Navegue para o ponto final no [portal do Azure](https://portal.azure.com) e clique no botão **Configurar** .

- Certifique-se de compressão está ativado.
- Verifique se o tipo de MIME para o conteúdo para ser comprimido está incluído na lista de formatos comprimidos.

![Definições de compressão CDN](./media/cdn-troubleshoot-compression/cdn-compression-settings.png)

### <a name="verify-compression-settings-premium-cdn-profile"></a>Verifique se as definições de compressão (perfil Premium CDN)

> [AZURE.NOTE] Este passo só se aplica se o seu perfil CDN é um perfil do **Azure CDN Premium a partir do Verizon** .

Navegue para o ponto final no [portal do Azure](https://portal.azure.com) e clique no botão **Gerir** .  O portal suplementar será aberto.  Paire sobre o separador **HTTP grandes** , em seguida, coloque o cursor sobre a lista de opções de **Definições da Cache** .  Clique em **compressão**. 

- Certifique-se de compressão está ativado.
- Verifique a lista de **Tipos de ficheiro** contém uma lista separados por vírgulas (sem espaços) de tipos de MIME.
- Verifique se o tipo de MIME para o conteúdo para ser comprimido está incluído na lista de formatos comprimidos.

![Definições de compressão CDN premium](./media/cdn-troubleshoot-compression/cdn-compression-settings-premium.png)

### <a name="verify-the-content-is-cached"></a>Certifique-se de que o conteúdo é colocada em cache

> [AZURE.NOTE] Este passo só se aplica se o seu perfil CDN é um perfil de **CDN Azure a partir do Verizon** (padrão ou Premium).

Utilizar ferramentas de programador do seu browser, verifique os cabeçalhos de resposta para garantir que o ficheiro é colocada em cache na região onde está a ser pedido.

- Verifique se o cabeçalho de resposta do **servidor** .  O cabeçalho deverá ter o formato de **plataforma (POP/servidor ID)**, conforme visto no seguinte exemplo.
- Verifique se o cabeçalho de resposta da **Cache de X** .  O cabeçalho, deverá ler **VISITAS**.  

![Cabeçalhos de resposta de CDN](./media/cdn-troubleshoot-compression/cdn-response-headers.png)

### <a name="verify-the-file-meets-the-size-requirements"></a>Verifique se que o ficheiro cumpre os requisitos de tamanho

> [AZURE.NOTE] Este passo só se aplica se o seu perfil CDN é um perfil de **CDN Azure a partir do Verizon** (padrão ou Premium).

Para ser elegível para compressão, um ficheiro tem de cumprir os seguintes requisitos de tamanho:

- Maior que 128 bytes.
- Menor que 1 MB.

### <a name="check-the-request-at-the-origin-server-for-a-via-header"></a>Verificar o pedido de servidor de origem para abrir um **através do** cabeçalho

O cabeçalho **através de** HTTP indica para o servidor web que o pedido é a ser transmitido por um servidor proxy.  Não é possível servidores de web do Microsoft IIS por predefinição comprimir respostas quando o pedido contém um **através do** cabeçalho.  Para ignorar este comportamento, faça o seguinte:

- **IIS 6**: [definir HcNoCompressionForProxies = "FALSE" nas propriedades Metabase do IIS](https://msdn.microsoft.com/library/ms525390.aspx)
- **IIS 7 e até**: [definir **noCompressionForHttp10** e **noCompressionForProxies** falso na configuração do servidor](http://www.iis.net/configreference/system.webserver/httpcompression)

