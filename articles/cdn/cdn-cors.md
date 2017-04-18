<properties
    pageTitle="Utilizar o Azure CDN com CORS | Microsoft Azure"
    description="Saiba como utilizar o Azure conteúdo entrega rede (CDN) para com origem cruzada recurso partilha (CORS)."
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
    ms.date="09/30/2016"
    ms.author="casoper"/>
    
# <a name="using-azure-cdn-with-cors"></a>Utilizar o Azure CDN com CORS     

## <a name="what-is-cors"></a>O que é CORS?

CORS (Cross origem partilha de recursos) é uma funcionalidade HTTP que permite uma aplicação web em execução num domínio para aceder aos recursos no outro domínio. Para reduzir a possibilidade de ataques de scripts de publicação em vários sites, todos os browsers moderna implementam uma restrição de segurança conhecida como [política da mesma origem](http://www.w3.org/Security/wiki/Same_Origin_Policy).  Isto impede que uma página web a partir de chamada APIs num domínio diferente.  CORS fornece uma forma segura para permitir que um domínio (origem) ligar APIs no outro domínio.
 
## <a name="how-it-works"></a>Como funciona
1.  Browser envia o pedido de opções com um cabeçalho de **origem** HTTP. O valor deste cabeçalho é o domínio que servida a página principal. Quando uma página de https://www.contoso.com tentarem aceder aos dados de um utilizador no domínio fabrikam.com, o cabeçalho pedido seguinte seria enviado para fabrikam.com: 
    
    `Origin: https://www.contoso.com`
 
2.  O servidor poderá responder com o seguinte:
    - Um cabeçalho de **Acesso controlo-permitir-origem** na sua resposta que indica os sites de origem são permitidos. Por exemplo:
        
        `Access-Control-Allow-Origin: https://www.contoso.com`
        
    - Uma página de erro se o servidor não permitir o pedido de origem de cruz
    - Um cabeçalho de **Acesso controlo-permitir-origem** com uma universais que permite que todos os domínios:
        
        `Access-Control-Allow-Origin: *`
 
Para pedidos HTTP complexos, existe um pedido de "prévia" concluído primeiro para determinar se tem a permissão antes de enviar o pedido de todo.
 
## <a name="wildcard-or-single-origin-scenarios"></a>Cenários de única origem ou de caracteres universais

CORS no Azure CDN irá funcionar automaticamente com nenhuma configuração adicional quando o cabeçalho do **Access controlo-permitir-origem** está definido como caráter universal (*) ou uma única origem.  A CDN será em cache a primeira resposta e os pedidos subsequentes irão utilizar o mesmo cabeçalho.
 
Se os pedidos de já foram efectuados à CDN antes de CORS a ser configurar na sua origem, é necessário remover conteúdo do seu conteúdo de ponto final para recarregar o conteúdo com o cabeçalho do **Access controlo-permitir-origem** .
 
## <a name="multiple-origin-scenarios"></a>Vários cenários de origem

Se precisar de permitir que uma lista de origens diferentes para ser permitidos CORS específicas, coisas obtém um pouco mais complicadas. O problema ocorre quando a CDN coloca em cache no cabeçalho da **Acesso controlo-permitir-origem** da origem CORS primeiro.  Quando uma origem CORS diferente faz com que um pedido de subsequente, a CDN irá servida o cabeçalho de **Acesso controlo-permitir-origem** em cache, que não correspondem aos.  Existem várias formas para corrigir este problema.
 
### <a name="azure-cdn-premium-from-verizon"></a>Premium Azure CDN a partir do Verizon

A melhor forma de ativar esta é utilizar **Azure CDN Premium a partir do Verizon**, que expõe algumas funcionalidades avançadas. 
 
Terá de para [criar uma regra](cdn-rules-engine.md) verificar o cabeçalho de **origem** no pedido.  Se for uma origem válida, a regra será definir o cabeçalho de **Acesso controlo-permitir-origem** com a origem fornecida no pedido de.  Se a origem especificada no cabeçalho de **origem** não é permitida, a regra deve omita o cabeçalho do **Access controlo-permitir-origem** que irão provocar o browser para rejeitar o pedido. 
 
Existem duas formas de fazer com o motor de regras.  Em ambos os casos, o cabeçalho do **Access controlo-permitir-origem** a partir do servidor de origem do ficheiro é ignorado completamente, motor de regras a CDN completamente gere as origens CORS permitidas.

#### <a name="one-regular-expression-with-all-valid-origins"></a>Uma expressão regular com todas as origens válidas
 
Neste caso, irá criar uma expressão regular que inclui todas as origens que pretende permitir que: 

    https?:\/\/(www\.contoso\.com|contoso\.com|www\.microsoft\.com|microsoft.com\.com)$
 
> [AZURE.TIP] **Azure CDN a partir do Verizon** utiliza [Perl compatível com as expressões regulares](http://pcre.org/) como seu motor de expressões normais.  Pode utilizar uma ferramenta como [101 de expressões regulares](https://regex101.com/) para validar a expressão normal.  Note que o caráter "/" é válido em expressões regulares e não necessita de ser retirado, no entanto, perda esse caráter considera-se prática recomendada e é esperado por alguns validações do Estado de regex.

Se corresponde a expressão regular, a regra irá substituir o cabeçalho do **Access controlo-permitir-origem** (se existir) a partir da origem com a origem que enviou o pedido.  Também pode adicionar cabeçalhos CORS adicionais, tal como **Controlo-permitir-métodos de acesso**.

![Exemplo de regras com expressão regular](./media/cdn-cors/cdn-cors-regex.png)
 
#### <a name="request-header-rule-for-each-origin"></a>Pedido de cabeçalho regra para cada origem.

Em vez de expressões regulares, em vez disso, pode criar uma regra separada para cada origem que deseja permitir ao utilizar a **Pedir universais cabeçalho** [coincidir com condição](https://msdn.microsoft.com/library/mt757336.aspx#Anchor_1). Tal como acontece com o método de expressão regular, o motor de regras sozinho define os cabeçalhos CORS. 
  
![Exemplo de regras sem expressão regular](./media/cdn-cors/cdn-cors-no-regex.png)

> [AZURE.TIP] No exemplo acima, a utilização do carácter universal * indica o motor de regras para que correspondam aos HTTP e HTTPS.
 
### <a name="azure-cdn-standard"></a>Azure CDN padrão

No Azure CDN padrão perfis, o mecanismos apenas para permitir a várias origens diferentes sem a utilização da origem universais são utilizar a [Colocação em cache de cadeia de consulta](cdn-query-string.md).  Tem de ativar a definição de cadeia de consulta para o ponto final de CDN e, em seguida, utilize uma cadeia de consulta exclusivo para pedidos de todos os domínios permitidos. Este procedimento irá resultar em CDN colocação em cache um objeto separado para cada cadeia de consulta exclusivo. Esta abordagem não é ideal, no entanto, à medida que resultará em várias cópias do mesmo ficheiro em cache na CDN.  

