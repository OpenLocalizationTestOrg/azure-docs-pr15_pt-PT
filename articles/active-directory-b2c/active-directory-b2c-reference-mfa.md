<properties
    pageTitle="B2C Azure Active Directory: Autenticação Multifator | Microsoft Azure"
    description="Como ativar a autenticação Multifator nas aplicações do consumidor destinado protegidas por Azure Active Directory B2C"
    services="active-directory-b2c"
    documentationCenter=""
    authors="swkrish"
    manager="msmbaldwin"
    editor="bryanla"/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/24/2016"
    ms.author="swkrish"/>

# <a name="azure-active-directory-b2c-enable-multi-factor-authentication-in-your-consumer-facing-applications"></a>Azure B2C diretório ativos: Ativar a autenticação Multifator nas suas aplicações do consumidor destinado

Azure Active Directory (Azure AD) B2C integra diretamente [Azure a autenticação Multifator](../multi-factor-authentication/multi-factor-authentication.md) para que possa adicionar uma segunda camada de segurança para experiências inscrição e iniciar sessão nas suas aplicações do consumidor destinado. E pode fazê-lo sem escrever uma única linha de código. Atualmente suportamos verificação de mensagem de chamada telefónica e texto. Se já tiver criado políticas de inscrição e iniciar sessão, ainda pode ativar a autenticação Multifator.

> [AZURE.NOTE]
Também pode ser ativada a autenticação Multifator quando criar políticas de inscrição e iniciar sessão, não apenas ao editar políticas existentes.

Esta funcionalidade ajuda-o a aplicações processar cenários como as seguintes:

- Não necessitar de autenticação Multifator aceder a uma aplicação, mas requerem-lo para aceder à outra. Por exemplo, o consumidor pode iniciar sessão para uma aplicação de seguros automática com uma conta de rede social ou local, mas tem de verificar o número de telefone antes de aceder a aplicação de seguro casa registada no diretório do mesmo.
- Não necessitar de autenticação Multifator aceder a uma aplicação em geral, mas requerem-lo para aceder as partes sensíveis a maiúsculas e dentro da mesma. Por exemplo, o consumidor possam iniciar sessão para uma aplicação bancário com uma conta local ou de rede social e verificação saldo da conta, mas tem de verificar se o número de telefone antes de tentar transferir um esboço.

## <a name="modify-your-sign-up-policy-to-enable-multi-factor-authentication"></a>Modificar a política de inscrição para ativar a autenticação Multifator

1. [Siga estes passos para navegar para o pá de funcionalidades B2C no portal do Azure](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Clique em **políticas de inscrição**.
3. Clique na política de inscrição (por exemplo, "B2C_1_SiUp") para o abrir.
4. Clique em **autenticação multifator** e ativar o **Estado** para **Ativado**. Clique em **OK**.
5. Clique em **Guardar** no topo da pá.

Pode utilizar a funcionalidade "Executar agora" sobre a política para verificar a experiência do consumidor. Confirme o seguinte:

Uma conta do consumidor é criada no seu diretório antes do passo de autenticação Multifator ocorre. Durante o passo, o consumidor-lhe pedido para fornecer dele número de telefone e verificá-la. Se a verificação ser efetuada com êxito, o número de telefone é anexado à conta consumidor para utilizar posteriormente. Mesmo se o consumidor cancela ou desce, pode ser-lhe pedido para verificar se um número de telefone novamente durante o sessão seguinte (com a autenticação Multifator ativado).

## <a name="modify-your-sign-in-policy-to-enable-multi-factor-authentication"></a>Modificar a política de início de sessão para ativar a autenticação Multifator

1. [Siga estes passos para navegar para o pá de funcionalidades B2C no portal do Azure](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Clique em **Iniciar sessão políticas**.
3. Clique na início de sessão no política (por exemplo, "B2C_1_SiIn") para o abrir. Clique em **Editar** no topo da pá.
4. Clique em **autenticação multifator** e ativar o **Estado** para **Ativado**. Clique em **OK**.
5. Clique em **Guardar** no topo da pá.

Pode utilizar a funcionalidade "Executar agora" sobre a política para verificar a experiência do consumidor. Confirme o seguinte:

Quando o consumidor assina (ao utilizar uma conta de rede social ou local), se um número de telefone verificado é anexado à conta consumidor, é-lhe pedido para verificar. Se nenhum número de telefone estiver ligado, o consumidor é-lhe pedido para fornecer um e verificá-la. No Friso minimizado no Word, o número de telefone é anexado à conta consumidor para utilizar posteriormente.

## <a name="multi-factor-authentication-on-other-policies"></a>Autenticação Multifator no outras políticas

Conforme descrito em políticas de inscrição e início de sessão no acima, também é possível ativar a autenticação multifator inscrição no ou políticas de início de sessão e palavra-passe repor políticas. Estarão disponível mais cedo no edição políticas de perfil.
