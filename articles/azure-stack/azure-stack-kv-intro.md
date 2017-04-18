<properties
    pageTitle="Introdução de pilha chave cofre Azure | Microsoft Azure"
    description="Saiba como Azure pilha chave cofre faz a gestão das teclas e segredos"
    services="azure-stack"
    documentationCenter=""
    authors="rlfmendes"
    manager="natmack"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="09/26/2016"
    ms.author="ricardom"/>

# <a name="introduction-to-key-vault-in-azure-stack"></a>Introdução à chave cofre Azure empilhados #

## <a name="before-you-start"></a>Antes de começar

Este artigo assume o seguinte procedimento:

- O leitor tem acesso a uma subscrição que tem o Azure chave cofre ativado.
- O SDK do PowerShell Azure é configurados e disponíveis.
- Para o lançamento TP2, todas as operações de inquilino destinado podem ser executadas apenas a partir do PowerShell.

## <a name="key-vault-basics"></a>Noções básicas do Cofre de tecla

Chave cofre Azure empilhados ajuda a salvaguardar chaves de criptografia e utilizam de segredos aplicações e serviços em nuvem. Ao utilizar a chave cofre, pode encriptar teclas e segredos (tal como chaves de autenticação, as teclas de conta de armazenamento, chaves de encriptação de dados, ficheiros. pfx e palavras-passe).

Chave cofre simplifica o processo de gestão de chave e permite-lhe manter o controlo de teclas que aceder e encriptar os seus dados. Os programadores podem criar chaves para desenvolvimento e teste em minutos e, em seguida, a sua forma totalmente integrada migrar para o teclas de produção. Segurança os administradores podem conceder (e revogar) permissão para chaves, conforme necessário.

Qualquer pessoa com uma subscrição do Azure pilha pode criar e utilizar cofres chaves. Apesar de chave cofre vantagens para os programadores e para administradores de segurança, pode ser implementada e gerido por um administrador que gere a outros serviços de pilha de Azure por uma organização. Por exemplo, este administrador pode iniciar sessão com uma subscrição do Azure pilha, crie um cofre para a organização na qual pretende armazenar chaves e, em seguida, será responsável por estas tarefas operacionais avançadas:

- Criar ou importar uma chave ou palavra-passe
- Revogar ou eliminar uma chave ou palavra-passe
- Autorizar utilizadores ou as aplicações acedam Cofre de palavras chave, para que possam, em seguida, gerir ou utilizar o seu teclas e segredos
- Configurar a utilização da chave (por exemplo, inicie sessão ou encriptar)

Este administrador pode, em seguida, fornecer os programadores com URIs para ligar a partir das suas aplicações e um administrador de segurança com informações de utilização de chaves de registo.

Os programadores também podem gerir as teclas diretamente, utilizando APIs. Para mais informações, consulte o artigo Guia do Programador de chave cofre.

## <a name="scenarios"></a>Cenários

A tabela seguinte ilustra algumas situações onde chave cofre podem ajudar a satisfazer as necessidades dos programadores e administradores de segurança:


### <a name="developer-for-an-azure-stack-application"></a>Programador para uma aplicação de pilha de Azure

**Problema**: quiser escrever uma aplicação para pilha de Azure que utiliza as teclas para encriptação e assinatura, mas quero estas definições para ser externo da minha aplicação para que seja adequada para uma aplicação que é distribuída geograficamente a solução.

**Demonstração**: teclas são armazenadas num cofre e chamadas pelo URI quando for necessário.


### <a name="developer-for-software-as-a-service-saas"></a>Programador de software, como um serviço (SaaS)

**Problema:** Não quero a responsabilidade ou passivo potencial para chaves de inquilino dos meus clientes e segredos.

**Declaração:** Os clientes podem importar os seus próprios teclas para pilha Azure e geri-los. Pretendo clientes para o proprietário e gerir as respetivas chaves de modo a que posso pode concentrar-se numa efetuar o que fazer melhores, que está a fornecer as principais funcionalidades do software.


### <a name="chief-security-officer-cso"></a>Segurança director (ASC)

**Problema:** Pretende Certifique-se de que a minha organização está a controlar o chave ciclo de vida e pode monitorizar a utilização da chave.

**Declaração** Chave cofre foi concebido para que o Microsoft não consulte ou extrair suas chaves.  Quando necessita de uma aplicação para efetuar operações de criptografia utilizando as teclas dos clientes, chave cofre faz isto em nome da aplicação. A aplicação não ver teclas dos clientes.  Apesar de utilizamos vários serviços de pilha de Azure e recursos, quiser gerir as teclas a partir de uma única localização na pilha de Azure. O Cofre fornece uma interface única, independentemente de cofres quantos tiver na pilha de Azure, as regiões suporte e quais as aplicações utilizá-los.

## <a name="next-steps"></a>Próximos passos

[Introdução ao Cofre chave](azure-stack-kv-getting-started.md)
