<properties
   pageTitle="Activar a política de SSL e SSL ponto a ponto no Gateway aplicação | Microsoft Azure"
   description="Esta página fornece uma descrição geral do Gateway aplicação SSL de fim para fim de suporte."
   documentationCenter="na"
   services="application-gateway"
   authors="amsriva"
   manager="rossort"
   editor="amsriva"/>
<tags
   ms.service="application-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/25/2016"
   ms.author="amsriva"/>

# <a name="enabling-ssl-policy-and-end-to-end-ssl-on-application-gateway"></a>Activar a política de SSL e SSL ponto a ponto no Gateway de aplicação

## <a name="overview"></a>Descrição geral

Gateway aplicação suporta taxas de cessação SSL ao gateway, após o qual o tráfego normalmente flua não encriptado para os servidores de back-end. Esta opção permite-servidores da web para ser unburdened a partir de uma sobrecarga dispendioso encriptação/desencriptação. No entanto para alguns clientes não encriptada comunicação para os servidores de back-end não é uma opção aceitável. Isto pode ser devido a requisitos de segurança/conformidade ou a aplicação só pode aceitar uma ligação segura. Para estas aplicações, gateway aplicação suporta agora a encriptação SSL de fim para fim.

Fim para fim SSL permite-lhe para transmitir em segurança dados sensíveis a maiúsculas e a vantagem de escrita de ainda encriptada back-end dos benefícios da camada 7 balanceamento de carga funcionalidades que gateway de aplicação fornece, tal como afinidade cookie, com base no URL encaminhamento, suporte para encaminhamento com base em sites ou capacidade de inserção X-reencaminhadas-* cabeçalhos.

Quando configurado com o modo de comunicação ponto a ponto SSL, o gateway aplicação termina sessões de SSL de utilizador no gateway e desencripta tráfego de utilizador. Em seguida, aplica-se as regras configuradas para selecionar uma instância do conjunto de dados back-end adequado para encaminhar tráfego para. Gateway de aplicação, em seguida, inicia uma nova ligação de SSL para o servidor de back-end e voltar encripta dados utilizando o certificado de chave pública o servidor de back-end antes da transmissão pedido para o back-end. Terminar para terminar SSL está ativada ao definir a definição do protocolo na BackendHTTPSetting por Https, que, em seguida, é aplicado a um conjunto de dados back-end. Cada servidor back-end no conjunto de back-end com ponto a ponto SSL ativado tem de ser configurado com um certificado para permitir a comunicação segura.

![cenário de ssl ponto a ponto][1]

Neste exemplo, pedidos de utilizando TLS1.2 são encaminhados para servidores de back-end no Pool1 através de SSL de fim para fim.

## <a name="end-to-end-ssl-and-whitelisting-of-certificates"></a>Terminar para terminar de SSL e whitelisting de certificados

Gateway aplicação apenas comunica com instâncias de back-end conhecidos que tenham whitelisted respectivo certificado com o gateway de aplicação. Para permitir que whitelisting de certificados, terá de carregar chave pública de certificados de servidor de back-end para o gateway de aplicação (não o certificado de raiz). Apenas ligações a back-ends conhecidos e whitelisted, em seguida, são permitidas. O restante back-ends resulta num erro de gateway. Os certificados autoassinados são para fins de teste apenas e não recomendados para cargas de trabalho de produção. Esses certificados também têm de ser whitelisted com o gateway de aplicação, tal como descrito nos passos anteriores antes de poderem ser utilizados.

## <a name="application-gateway-ssl-policy"></a>Política de SSL de Gateway de aplicação

Gateway aplicação suporta configuráveis SSL negociação políticas de utilizador, que permitem clientes maior controlo sobre ligações SSL no gateway da aplicação.

1. SSL 2.0 e 3.0 desativado por predefinição para todos os Gateways de aplicação. Não são configuráveis de todo.
2. Dá de definição de política SSL opção para desativar a qualquer um dos seguintes 3 protocolos - TLSv1\_0, TLSv1\_1, TLSv1\_2.
3. Se nenhuma política SSL é definida todos os três (TLSv1\_0, TLSv1\_1, TLSv1_2) estão ativados.

## <a name="next-steps"></a>Próximos passos

Depois de formação sobre o fim para fim SSL e a política SSL, aceda a [Ativar SSL ponto a ponto no gateway de aplicação](application-gateway-end-to-end-ssl-powershell.md) criar um gateway aplicação com capacidade para enviar o tráfego para back-ends num formato encriptado.

<!--Image references-->

[1]: ./media/application-gateway-backend-ssl/scenario.png