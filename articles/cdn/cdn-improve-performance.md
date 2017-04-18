<properties
    pageTitle="Melhorar o desempenho ao comprimir os ficheiros no Azure CDN | Microsoft Azure"
    description="Saiba como melhorar a velocidade de transferência do ficheiro e aumenta o desempenho de carregamento de página ao comprimir os seus ficheiros no Azure CDN."
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
    ms.date="07/28/2016"
    ms.author="casoper"/>

# <a name="improve-performance-by-compressing-files"></a>Melhorar o desempenho ao comprimir os ficheiros

Compressão é um método simple e eficaz para melhorar a velocidade de transferência do ficheiro e aumentar o desempenho de carregamento de página, reduzindo o tamanho do ficheiro antes de ter sido enviada do servidor. Reduz o custos de largura de banda e fornece uma experiência de responder mais para os seus utilizadores.

Existem duas formas de activar a compressão de:

- Pode ativar a compressão no seu servidor de origem, na qual caso a CDN passar os ficheiros comprimidos e entregar ficheiros comprimidos para clientes que pedem-los.
- Pode ativar a compressão diretamente no servidores edge CDN, na qual caso a CDN comprimir os ficheiros e servi-lo para utilizadores finais, mesmo que não comprimidos pelo servidor de origem.

> [AZURE.IMPORTANT] Alterações à configuração CDN demorar algum tempo a serem propagadas através da rede.  Perfis de <b>CDN Azure a partir do Akamai</b> , de propagação conclui normalmente dentro de um minuto em.  Para os perfis de <b>CDN Azure a partir do Verizon</b> , normalmente, verá as alterações serem aplicadas no prazo de 90 minutos.  Se esta for a primeira vez que configurou compressão para o ponto final de CDN, considere a aguardar horas 1-2 para se certificar de que a compressão definições tenham propagado às POP antes de resolução de problemas

## <a name="enabling-compression"></a>Activar a compressão

> [AZURE.NOTE] As camadas padrão e Premium CDN fornecem a mesma funcionalidade de compressão, mas a interface de utilizador é diferente.  Para mais informações sobre as diferenças entre camadas padrão e Premium CDN, consulte o artigo [Descrição geral de CDN Azure](cdn-overview.md).

### <a name="standard-tier"></a>Camada padrão

> [AZURE.NOTE] Esta secção aplica-se aos perfis **Azure CDN padrão de Verizon** e **Azure CDN padrão de Akamai** .

1. Pá de perfil CDN, clique no ponto final de CDN que pretende gerir.

    ![Pontos finais pá de perfil CDN](./media/cdn-file-compression/cdn-endpoints.png)

    É aberta a pá de ponto final CDN.

2. Clique no botão de **Configurar** .

    ![Botão de gerir pá de perfil CDN](./media/cdn-file-compression/cdn-config-btn.png)

    É aberta a pá CDN configuração.

3. Ative a **compressão**.

    ![Opções de compressão CDN](./media/cdn-file-compression/cdn-compress-standard.png)

4. Utilize os tipos de predefinidos ou modificar a lista ao remover ou adicionar tipos de ficheiro.
    
    > [AZURE.TIP] Enquanto possível, recomenda-não se aplicar compressão a comprimido formatos, como ZIP, MP3, MP4, JPG, etc.
    
5. Depois de efetuar as suas alterações, clique no botão **Guardar** .

### <a name="premium-tier"></a>Camadas de Premium

> [AZURE.NOTE] Esta secção aplica-se aos perfis de **Azure CDN Premium a partir do Verizon** .

1. A partir do pá de perfil CDN, clique no botão **Gerir** .

    ![Botão de gerir pá de perfil CDN](./media/cdn-file-compression/cdn-manage-btn.png)

    O portal de gestão de CDN é aberta.

2. Paire sobre o separador **HTTP grandes** , em seguida, coloque o cursor sobre a lista de opções de **Definições da Cache** .  Clique em **Comprimir**.

    Opções de compressão são apresentadas.

    ![Compressão de ficheiros](./media/cdn-file-compression/cdn-compress-files.png)

3. Active a compressão ao clicar no botão de opção **Compressão activada** .  Introduza os tipos de MIME que pretende comprimir como uma lista delimitado por vírgulas (sem espaços) na caixa de texto **Tipos de ficheiro** .
        
    > [AZURE.TIP] Enquanto possível, recomenda-não se aplicar compressão a comprimido formatos, como ZIP, MP3, MP4, JPG, etc. 

4. Depois de efetuar as suas alterações, clique no botão de **atualização** .


## <a name="compression-rules"></a>Regras de compressão

Estas tabelas descrevem comportamento de compressão Azure CDN para cada cenário.

> [AZURE.IMPORTANT] Para **CDN Azure a partir do Verizon** (padrão e Premium), apenas os ficheiros elegíveis são comprimidos.  Para ser elegível para compressão, tem de um ficheiro:
>
> - Ser maior do que 128 bytes.
> - Ser menor que 1 MB.
> 
> Para **CDN Azure a partir do Akamai**, todos os ficheiros são elegíveis para compressão.
>
> Para todos os produtos de Azure CDN, um ficheiro tem de ser um tipo de MIME que tenha sido [configurado para compressão](#enabling-compression).
>
> Os perfis de **CDN Azure a partir do Verizon** (padrão e Premium) suportam **gzip**, **Esvaziar**ou **bzip2** codificação.  Os perfis de **CDN Azure a partir do Akamai** apenas suportam a codificação **gzip** .
>
> Os pontos finais de **CDN Azure a partir do Akamai** pedir sempre **gzip** codificado ficheiros a partir da origem, independentemente do pedido do cliente.

### <a name="compression-disabled-or-file-is-ineligible-for-compression"></a>Compressão desativada ou ficheiro é elegível para compressão

|Formato de pedido de cliente (através do cabeçalho de codificação de aceitar)|Formato de ficheiro em cache|Resposta CDN ao cliente|Notas|
|----------------|-----------|------------|-----|
|Comprimido|Comprimido|Comprimido|   |
|Comprimido|Não comprimidas|Não comprimidas|    | 
|Comprimido|Não em cache|Comprimido ou não comprimido|Depende da resposta de origem|
|Não comprimidas|Comprimido|Não comprimidas|    |
|Não comprimidas|Não comprimidas|Não comprimidas|    |   
|Não comprimidas|Não em cache|Não comprimidas|     |

### <a name="compression-enabled-and-file-is-eligible-for-compression"></a>Compressão ativada e o ficheiro é elegível para compressão

|Formato de pedido de cliente (através do cabeçalho de codificação de aceitar)|Formato de ficheiro em cache|Resposta CDN ao cliente|Notas|
|----------------|-----------|------------|-----|
|Comprimido|Comprimido|Comprimido|CDN transcodes entre formatos suportados|
|Comprimido|Não comprimidas|Comprimido|CDN executa compressão|
|Comprimido|Não em cache|Comprimido|CDN executa compressão se origem devolve não comprimida.  **Azure CDN a partir do Verizon** passar o ficheiro não comprimido no primeiro pedido e, em seguida, comprimir e o ficheiro para os pedidos subsequentes em cache.  Ficheiros com `Cache-Control: no-cache` cabeçalho nunca será comprimido. 
|Não comprimidas|Comprimido|Não comprimidas|CDN executa descompressão|
|Não comprimidas|Não comprimidas|Não comprimidas|     |  
|Não comprimidas|Não em cache|Não comprimidas|     |

## <a name="media-services-cdn-compression"></a>Serviços de multimédia CDN compressão

Para fornecedores de serviços de multimédia activado pontos finais de transmissão, compressão está ativada por predefinição para os seguintes tipos de conteúdo: aplicação/vnd.ms-sstr + xml, application/dash+xml,application/vnd.apple.mpegurl, aplicação/f4m + xml. Pode não é possível ativar/desativar a compressão para os tipos de mencionadas através do portal Azure.  

## <a name="see-also"></a>Consulte também
- [Resolução de problemas de compressão de ficheiros CDN](cdn-troubleshoot-compression.md)    
