<properties
    pageTitle="O que é o Azure chave Cofre? | Microsoft Azure"
    description="Azure chave cofre ajuda chaves de criptografia salvaguarda e segredos utilizados pelo nuvem aplicações e serviços. Ao utilizar o Azure chave cofre, os clientes podem encriptar teclas e segredos (tal como chaves de autenticação, as teclas de conta de armazenamento, chaves de encriptação de dados. Ficheiros PFX e palavras-passe) utilizando as teclas que estejam protegidas por módulos de segurança do hardware (Os HSMs)."
    services="key-vault"
    documentationCenter=""
    authors="cabailey"
    manager="mbaldwin"
    tags="azure-resource-manager"/>

<tags
    ms.service="key-vault"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/10/2016"
    ms.author="cabailey"/>



# <a name="what-is-azure-key-vault"></a>O que é o Azure chave Cofre?

Azure chave Cofre está disponível na maioria das regiões. Para mais informações, consulte o artigo [chave cofre preços página](https://azure.microsoft.com/pricing/details/key-vault/).

## <a name="introduction"></a>Introdução

Azure chave cofre ajuda chaves de criptografia salvaguarda e segredos utilizados pelo nuvem aplicações e serviços. Ao utilizar a chave cofre, pode encriptar teclas e segredos (tal como chaves de autenticação, as teclas de conta de armazenamento, chaves de encriptação de dados. Ficheiros PFX e palavras-passe) utilizando as teclas que estejam protegidas por módulos de segurança do hardware (Os HSMs). Para assurance adicionada, pode importar ou gerar chaves nos HSMs. Se optar por isso, os processos de Microsoft suas chaves no FIPS 140-2 nivelar os HSMs validados 2 (hardware e firmware).  

Chave cofre simplifica o processo de gestão de chave e permite-lhe manter o controlo de teclas que aceder e encriptar os seus dados. Os programadores podem criar chaves para desenvolvimento e teste em minutos e, em seguida, a sua forma totalmente integrada migrar para o teclas de produção. Segurança os administradores podem conceder (e revogar) permissão para chaves, conforme necessário.

Utilizar a seguinte tabela para melhor compreender como o pode ajudar cofre chave para satisfazer as necessidades dos programadores e administradores de segurança.





| Função        | Declaração de problema           | Resolvido por cofre chave Azure  |
| ------------- |-------------|-----|
| Programador para uma aplicação do Azure      | "Quiser escrever uma aplicação para o Azure que utiliza as teclas para encriptação e assinatura, mas quero estas teclas para ser externo da minha aplicação para que seja adequada para uma aplicação que é distribuída geograficamente a solução. <br/><br/>Também quero estas teclas e segredos para protegido, sem ter de escrever o código pessoalmente. Também quero estas teclas e segredos para ser mais fácil por mim utilizar a partir do meu aplicações, com um desempenho ideal." | Teclas de √ são armazenadas num cofre e chamadas pelo URI quando for necessário.<br/><br/> Teclas de √ são protegidas por Azure, utilizando norma da indústria algoritmos, comprimentos de chave e módulos de segurança do hardware (Os HSMs).<br/><br/> Teclas de √ são processadas nos HSMs que residem os centros de dados Azure mesmo como as aplicações. Este procedimento fornece uma melhor fiabilidade e a latência reduzida que se as teclas armazenadas numa localização diferente, tal como no local.|
| Programador de Software, como um serviço (SaaS)      |"Não quero a responsabilidade ou passivo potencial para chaves de inquilino dos meus clientes e segredos. <br/><br/>Pretendo que os clientes para o proprietário e gerir as respetivas chaves de modo a que posso pode concentrar-se numa efetuar o que fazer melhores, que está a fornecer as principais funcionalidades do software." | Clientes √ podem importar os seus próprios teclas para Azure e geri-los. Quando necessita de uma aplicação de SaaS para efetuar operações de criptografia utilizando as teclas de aos seus clientes, chave cofre faz estas operações em nome da aplicação. A aplicação não ver teclas dos clientes.|
| Chefe de segurança uniformizado (ASC) | "Quiser saber que nossas aplicações está em conformidade com FIPS 140-2 nível 2 Os HSMs, para a gestão de chave seguro. <br/><br/>Pretende Certifique-se de que a minha organização está a controlar o chave ciclo de vida e pode monitorizar a utilização da chave. <br/><br/>E embora utilizamos vários serviços Azure e recursos, quiser gerir as teclas a partir de uma única localização no Azure."     |√ Os HSMs são 140-2 nível 2 validados FIPS.<br/><br/>√ Chave cofre foi concebido para que o Microsoft não consulte ou extrair suas chaves.<br/><br/>√ Perto registo em tempo real de utilização de chaves.<br/><br/>√ cofre fornece uma interface única, independentemente de quantas cofres tiver no Azure, as regiões suporte e quais as aplicações utilizá-los. |


Qualquer pessoa com uma subscrição do Azure pode criar e utilizar cofres chaves. Apesar de chave cofre vantagens para os programadores e para administradores de segurança, poderá ser implementada e gerida pelo administrador de uma organização que gere a outros serviços Azure por uma organização. Por exemplo, este administrador seria inicie sessão com uma subscrição do Azure, crie um cofre para a organização na qual pretende armazenar chaves e, em seguida, ser responsáveis pelas tarefas operacionais avançadas, tal como:

+ Criar ou importar uma chave ou palavra-passe
+ Revogar ou eliminar uma chave ou palavra-passe
+ Autorizar utilizadores ou as aplicações acedam Cofre de palavras chave, para que possam, em seguida, gerir ou utilizar o seu teclas e segredos
+ Configurar a utilização da chave (por exemplo, inicie sessão ou encriptar)
+ Utilização de chaves do monitor

Este administrador seria, em seguida, fornecer os programadores com URIs para ligar a partir das suas aplicações e respetivo administrador de segurança com informações de utilização de chaves de registo. 

   ![Descrição geral do cofre chave Azure][1]

Os programadores também podem gerir as teclas diretamente, utilizando APIs. Para mais informações, consulte o artigo [Guia do Programador de chave do cofre](key-vault-developers-guide.md).

## <a name="next-steps"></a>Próximos passos

Para obter um tutorial introdução ao obter para que um administrador, consulte o artigo [Introdução ao Azure chave cofre](key-vault-get-started.md).

Para mais informações acerca da utilização de registo do cofre chave, consulte o artigo [Azure chave cofre registo](key-vault-logging.md).

Para mais informações sobre como utilizar teclas e segredos com Azure chave cofre, consulte o artigo [sobre chaves, segredos e certificados](https://msdn.microsoft.com/library/azure/dn903623\(v=azure.1\).aspx).


<!--Image references-->
[1]: ./media/key-vault-whatis/AzureKeyVault_overview.png
