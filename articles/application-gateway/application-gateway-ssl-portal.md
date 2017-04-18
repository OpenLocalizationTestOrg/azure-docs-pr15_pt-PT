<properties
   pageTitle="Configurar um gateway de aplicação para a passagem para da SSL utilizando o portal | Microsoft Azure"
   description="Esta página disponibiliza instruções para criar um gateway aplicação com SSL descarregar utilizando o portal"
   documentationCenter="na"
   services="application-gateway"
   authors="georgewallace"
   manager="carmonm"
   editor="tysonn"/>
<tags
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/09/2016"
   ms.author="gwallace"/>

# <a name="configure-an-application-gateway-for-ssl-offload-by-using-the-portal"></a>Configurar um gateway de aplicação para a passagem para da SSL utilizando o portal

> [AZURE.SELECTOR]
-[Azure portal](application-gateway-ssl-portal.md)
-[Azure Gestor de recursos do PowerShell](application-gateway-ssl-arm.md)
-[PowerShell clássica do Azure](application-gateway-ssl.md)

Azure Gateway de aplicação pode ser configurado para terminar a sessão Secure Sockets Layer (SSL) ao gateway para evitar dispendiosas tarefas de desencriptação SSL para ocorrer no web farm. A passagem para da SSL também simplifica a configuração do servidor front-end e gestão da aplicação web.

## <a name="scenario"></a>Cenário

Cenário que se segue analise configurar SSL descarregar num gateway de aplicação existente. O cenário assume que já tenha seguido os passos para [criar um Gateway de aplicação](application-gateway-create-gateway-portal.md).

## <a name="before-you-begin"></a>Antes de começar

Para configurar a passagem para da SSL com um gateway de aplicação, é necessário um certificado. Este certificado é carregado no gateway aplicação e utilizado para encriptar e desencriptar o tráfego enviado através de SSL. O certificado tem de estar no formato de intercâmbio de informações pessoais (pfx). Este formato de ficheiro permite a chave privada a serem exportadas ferramenta necessária ao gateway de aplicação para efetuar a encriptação e desencriptar de tráfego.

## <a name="add-an-https-listener"></a>Adicionar uma escuta HTTPS

A escuta HTTPS procura para o tráfego com base na sua configuração e ajuda-o a encaminha o tráfego para os conjuntos de dados back-end.

### <a name="step-1"></a>Passo 1

Navegue para o portal do Azure e selecione um gateway de aplicação existente

### <a name="step-2"></a>Passo 2

Clique em Listeners e clique no botão Adicionar para adicionar uma escuta.

![Pá de descrição geral de gateway de aplicação][1]

### <a name="step-3"></a>Passo 3

Preencha as informações necessárias para a escuta e carregar o certificado. pfx, quando estiver concluído clique em OK.

**Nome** - este valor é um nome amigável da escuta.

**Configuração do IP de front-end** - este valor é a configuração de IP de front-end que é utilizada a escutar.

**Porta de front-end (nome/porta)** - um nome amigável para a porta utilizada no front-end do gateway aplicação e a porta real utilizada.

**Protocolo** - um parâmetro para determinar se é utilizado para https ou http para o front-end.

**Certificado de (nome/palavra-passe)** - a passagem para da SSL se é utilizado, é necessário para que esta definição um certificado. pfx e um nome amigável e a palavra-passe são necessários.

![Adicionar pá escuta][2]

## <a name="create-a-rule-and-associate-it-to-the-listener"></a>Criar uma regra e associá-lo para a escuta

A escuta agora foi criada. Está na altura de criar uma regra para processar o tráfego da escuta.

### <a name="step-1"></a>Passo 1

Clique as **regras** do gateway aplicação e, em seguida, clique em Adicionar.

![Pá de regras de gateway de aplicação][3]

### <a name="step-2"></a>Passo 2

No pá **Adicionar regra básica** , escreva o nome amigável para a regra e escolha a escuta criada no passo anterior. Escolha o conjunto de back-end adequado e definição de http e clique em **OK**

![janela de definições de HTTPS][4]

As definições agora são guardadas para o gateway de aplicação. Guardar processar para estas definições podem demorar algum tempo até que estão disponíveis para visualização através do portal do ou através do PowerShell. Assim que guardado o gateway aplicação trata a encriptação e desencriptar de tráfego. Todo o tráfego entre o gateway de aplicação e os servidores de web back-end será efetuado através de http. Qualquer comunicação de volta para o cliente se iniciadas por https será devolvida para o cliente encriptado.

## <a name="next-steps"></a>Próximos passos

Para saber como configurar uma sonda de estado de funcionamento personalizado com o Azure Gateway de aplicação, consulte o artigo [criar uma sonda de estado de funcionamento personalizado](application-gateway-create-gateway-portal.md).

[1]: ./media/application-gateway-ssl-portal/figure1.png
[2]: ./media/application-gateway-ssl-portal/figure2.png
[3]: ./media/application-gateway-ssl-portal/figure3.png
[4]: ./media/application-gateway-ssl-portal/figure4.png