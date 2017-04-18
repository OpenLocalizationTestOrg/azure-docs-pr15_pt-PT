<properties 
    pageTitle="Autenticação Multifator Azure - como funciona"
    description="Azure autenticação Multifator ajuda-o a salvaguardar informações o acesso a dados e as aplicações enquanto o utilizador a procura de um processo de início de sessão simples da reunião. -Fornece segurança adicional exigindo uma segunda forma de autenticação e oferece forte autenticação através de um intervalo de opções de verificação fácil."
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="curtland"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/04/2016"
    ms.author="kgremban"/>

#<a name="how-azure-multi-factor-authentication-works"></a>Como funciona a autenticação Multifator de Azure

A segurança de autenticação multifator reside no respetiva abordagem de camadas. Comprometer a vários fatores de autenticação apresenta um desafio significativo para intrusos. Mesmo se gere intruso para saber a palavra-passe de utilizador, é inútil sem ter também possuir do dispositivo fidedigno. Deve o utilizador perder o dispositivo, a pessoa que localiza-não poderá utilizá-lo a menos que também conhecer palavra-passe o utilizador.

![Proofup](./media/multi-factor-authentication-how-it-works/howitworks.png)



Azure autenticação Multifator ajuda-o a salvaguardar informações o acesso a dados e as aplicações enquanto o utilizador a procura de um processo de início de sessão simples da reunião.  -Fornece segurança adicional exigindo uma segunda forma de autenticação e oferece forte autenticação através de um intervalo de opções de verificação fácil:

- chamada telefónica
- mensagem de texto
- notificação de aplicação móvel, permitindo que os utilizadores escolher o método preferem
- código de verificação da aplicação móvel
- 3º tokens de JURAMENTO festa

Para obter informações adicionais oh como funciona consulte o vídeo seguinte.

>[AZURE.VIDEO multi-factor-authentication-deep-dive-securing-access-on-premises]

##<a name="methods-available-for-multi-factor-authentication"></a>Métodos disponíveis para autenticação multifator
Quando um utilizador inicia sessão, é enviada uma verificação adicional ao utilizador.  Seguem-se uma lista dos métodos que podem ser utilizados para esta verificação segunda.

Método de confirmação  | Descrição
------------- | ------------- |
Chamada telefónica | Uma chamada é efetuada para Smartphone de um utilizador solicitando-lhes para verificar que estes estão iniciar sessão ao premir o sinal #.  Isto irá concluir o processo de verificação.  Esta opção é configurável e pode ser alterada para um código que especificar.
Mensagem de texto | Uma mensagem de texto será enviada para Smartphone de um utilizador com um código de 6 dígitos.  Introduza este código para concluir o processo de verificação.
Notificação de aplicação móvel | Será enviado um pedido de verificação para Smartphone de um utilizador pedir que concluída a verificação selecionando verificar a partir da aplicação móvel. Isto ocorrerá se tiver selecionado a notificação de aplicação como o seu método de confirmação principal.  Se recebem este ao que não são iniciar sessão, pode optar por reportar a situação como fraude.
Código de verificação com a aplicação móvel | Um código de verificação será enviado para a aplicação móvel do que está em execução no Smartphone de um utilizador.  Isto ocorrerá se tiver selecionado um código de verificação como o seu método de confirmação principal.


##<a name="available-versions-of-azure-multi-factor-authentication"></a>Versões disponíveis do Azure a autenticação Multifator
Autenticação Multifator Azure está disponível em versões diferentes três.  A tabela abaixo descreve cada um destes mais detalhadamente.

Versão  | Descrição
------------- | ------------- |
Autenticação Multifator para o Office 365 | Esta versão funciona exclusivamente com aplicações do Office 365 e é gerido a partir do portal do Office 365. Para que os administradores agora podem ajudar a proteger dos seus recursos do Office 365 utilizando a autenticação multifator.  Esta versão vem com uma subscrição do Office 365.
Autenticação Multifator para administradores do Azure | O mesmo subconjunto de capacidades de autenticação Multifator para o Office 365 estará disponível sem custos para todos os administradores do Azure. Cada conta administrativa de uma subscrição Azure agora pode aceder a proteção adicional ao ativar esta funcionalidade de autenticação multifator core. Para que um administrador que pretende aceder ao portal Azure para criar uma VM, um web site, gerir armazenamento, serviços móveis ou qualquer outro serviço de Azure pode adicionar autenticação multifator à sua conta de administrador.
Autenticação Multifator Azure | Autenticação Multifator Azure oferece o conjunto richest funcionalidades. <br><br>Fornece opções de configuração adicionais através do portal de gestão do Azure, avançadas elaboração de relatórios e suporte para um intervalo de no local e na nuvem aplicações. Autenticação Multifator Azure pode ser adquirida como uma licença autónoma e está agrupada dentro do Azure Active Directory Premium e o conjunto de aplicações do Enterprise mobilidade. <br><br>Também pode ser adquirido numa base consumo através da criação de um fornecedor de autenticação Multifator Azure numa subscrição do Azure.
##<a name="feature-comparison-of-versions"></a>Funcionalidade de comparação de versões
A seguinte tabela abaixo fornece uma lista das funcionalidades que estão disponíveis em versões diferentes do Azure a autenticação Multifator.


Funcionalidade  | Autenticação Multifator para o Office 365 (incluído no Office 365 SKUs)|Autenticação Multifator para administradores do Azure (incluído com a subscrição do Azure) | Azure autenticação Multifator (incluído no Azure AD Premium e o conjunto de aplicações do Enterprise mobilidade)
------------- | :-------------: |:-------------: |:-------------: |
Os administradores podem proteger contas com MFA| * | * (Disponível apenas para contas de administrador do Azure)|*
Aplicação móvel como um fator de segundo|* | * | *
Chamada como um fator de segundo|* | * | *
SMS como um fator de segundo|* | * | *
Palavras-passe de aplicação para clientes que não suportam MFA|* | * | *
Controlo de administração sobre métodos de autenticação| *| *| *
Modo PIN| | | *
Alerta de fraude| | | *
Relatórios MFA| | | *
Ignorar único| | | *
Saudações personalizadas para chamadas telefónicas| | | *
Personalização de ID do autor da chamada para chamadas telefónicas| | | *
Confirmação de evento| | | *
IPs de confiança| | | *
Suspender MFA para dispositivos passados (público pré-visualização)| | | *
MFA SDK| | | *
MFA para as aplicações no local com o servidor MFA| | | *


##<a name="how-to-get-azure-multi-factor-authentication"></a>Como obter o Azure a autenticação Multifator

Se gostaria a funcionalidade completa disponibilizada pelos Azure a autenticação Multifator em vez de apenas os fornecido para utilizadores do Office 365 e administradores Azure, existem várias opções para obtê-lo:

1.  Comprar licenças de autenticação Multifator de Azure e atribua-lhes aos seus utilizadores.
2.  Comprar licenças que tenham a autenticação Multifator de Azure agrupados nelas como Azure Active Directory Premium ou do conjunto de aplicações do Enterprise mobilidade e atribua-lhes aos seus utilizadores.
3.  Crie um fornecedor de autenticação Multifator Azure dentro de uma subscrição do Azure. Se ainda não tiver uma subscrição do Azure, pode inscrever-se para uma subscrição de avaliação Azure. Subscrições de avaliação terá de ser convertidas em subscrições normais antes de expiração de avaliação.

Ao utilizar um fornecedor de autenticação Multifator Azure existem dois a utilização modelos disponíveis que são faturados através da sua subscrição do Azure:


- **Por utilizador**. Geralmente para as empresas que pretende ativar a autenticação multifator para um número fixo de funcionários que regularmente precisam de autenticação.
- **Por autenticação**. Geralmente para as empresas que pretende ativar a autenticação multifator para um grupo grande de utilizadores externos que com pouca frequência precisam de autenticação.

Para preços detalhes consulte [preços do Azure MFA.](https://azure.microsoft.com/pricing/details/multi-factor-authentication/)

Escolha o por banco ou modelo com base em consumo que funciona melhor para a sua organização.   Em seguida, para obter introdução consulte [Introdução ao](multi-factor-authentication-get-started.md)
