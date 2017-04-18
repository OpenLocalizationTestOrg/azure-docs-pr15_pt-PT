<properties
    pageTitle="Azure B2C diretório ativos: Token, sessão e configuração de início de sessão único | Microsoft Azure"
    description="Token, sessão e configuração do início de sessão única no Azure Active Directory B2C"
    services="active-directory-b2c"
    documentationCenter=""
    authors="swkrish"
    manager="mbaldwin"
    editor="bryanla"/>

<tags
    ms.service="active-directory-b2c"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="07/24/2016"
    ms.author="swkrish"/>

# <a name="azure-active-directory-b2c-token-session-and-single-sign-on-configuration"></a>Azure B2C diretório ativos: Token, sessão e configuração de início de sessão único

Esta funcionalidade dá-lhe controlo extensivamente, uma [política de base](active-directory-b2c-reference-policies.md), de:
 
1. Durações de tokens de segurança emitidos por B2C Azure Active Directory (Azure AD).
2. Durações de sessões de aplicação web geridos por Azure AD B2C.
3. Comportamento sessão único (SSO) em múltiplas aplicações e políticas de no seu inquilino B2C.

Pode utilizar esta funcionalidade no inquilino do B2C da seguinte forma:

1. Siga estes passos para [Navegar para o pá funcionalidades B2C](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade) no portal do Azure.
2. Clique em **Iniciar sessão políticas**. *Nota: pode utilizar esta funcionalidade no qualquer tipo de política, não apenas em* *Políticas de início de sessão no** *.
3. Abra uma política de ao clicar na mesma. Por exemplo, clique no **B2C_1_SiIn**.
4. Clique em **Editar** no topo da pá.
5. Clique em **Token, a sessão e a configuração de início de sessão única**.
6. Faça as alterações pretendidas. Saiba mais sobre as propriedades disponíveis na secções subsequentes.
7. Clique em **OK**.
8. Clique em **Guardar** na parte superior a pá.

![Captura de ecrã do token, a sessão e a configuração de início de sessão única](./media/active-directory-b2c-token-session-sso/token-session-sso.png)

## <a name="token-lifetimes-configuration"></a>Configuração do token durações

Azure AD B2C suporta o [protocolo de autorização de OAuth 2.0](active-directory-b2c-reference-protocols.md) para ativar o acesso seguro a recursos protegidos. Para implementar este suporte, Azure AD B2C emite vários [tokens de segurança](active-directory-b2c-reference-tokens.md). Estas são as propriedades que pode utilizar para gerir as durações de tokens de segurança emitidos por Azure AD B2C:

- **Durações tokens de acesso e ID (minutos)**: A duração da 2.0 token de ligação OAuth utilizada para ter acesso a um recurso protegido. Azure AD B2C problemas de tokens de ID apenas neste momento. Este valor teria se aplicam aos tokens de acesso, assim, vamos adicionar suporte para os mesmos.
   - Predefinição = 60 minutos.
   - Mínimo (inclusive) = 5 minutos.
   - Máximo (inclusive) = 1440 minutos.
- **Duração do token de atualização (dias)**: O período de tempo máximo antes da qual um token de atualização pode ser utilizado para adquirir um novo acesso ou token de ID (e opcionalmente, um novo atualização token, se tivessem sido concedidas as aplicações a `offline_access` âmbito).
   - Predefinição = 14 dias.
   - Mínimo (inclusive) = 1 dia.
   - Máximo (inclusive) = cerca de 90 dias.
- **Token de atualização deslizante duração da janela (dias)**: após este período de tempo decorrido o utilizador for forçada para autenticar novamente, independentemente do período de validade do mais recente atualizar token adquirido pela aplicação. Só pode ser fornecido se o parâmetro estiver definido para **Bounded**. Necessita de ser maior ou igual ao **duração do token de atualização (dias)** valor. Se o parâmetro estiver definido para **Unbounded**, não pode fornecer um valor específico.
   - Predefinição = cerca de 90 dias.
   - Mínimo (inclusive) = 1 dia.
   - Máximo (inclusive) = 365 dias.

Estas são algumas de casos de utilização que pode ativar a utilizar estas propriedades:

- Permitir que um utilizador para se manter com a sessão iniciada numa aplicação móvel indefinidamente, desde que é automática e continuamente ativo relativo à aplicação. Pode fazê-lo ao definir a **atualização deslizante janela duração do token (dias)** mudar **Unbounded** na política de início de sessão.
- Reunir-segurança do seu setor industrial e requisitos de conformidade ao definir as durações token de acesso adequado.

## <a name="session-configuration"></a>Configuração de sessões

Azure AD B2C suporta o [protocolo de autenticação OpenID ligar](active-directory-b2c-reference-oidc.md) para ativar a seguro início de sessão no aplicações web. Estas são as propriedades que pode utilizar para gerir sessões de aplicação web:

- **Aplicação Web duração de sessão (minutos)**: A duração de cookie de sessão do Azure AD B2C armazenado no browser do utilizador relativamente a autenticação com êxito.
   - Predefinição = 1440 minutos.
   - Mínimo (inclusive) = 15 minutos.
   - Máximo (inclusive) = 1440 minutos.
- **Tempo limite de sessão do Web app**: se este parâmetro estiver definido para **absoluta**, o utilizador for forçado para autenticar novamente após o período de tempo especificado pelo decorre da **duração da sessão (minutos) do Web app** . Se este parâmetro estiver definido para **gradual** (a predefinição), o utilizador mantém-se com a sessão iniciada no desde que o utilizador está continuamente ativo na sua aplicação web.

Estas são algumas de casos de utilização que pode ativar a utilizar estas propriedades:

- Cumprir requisitos de segurança e conformidade do seu setor industrial definindo a sessão de aplicação web adequada durações.
- Força uma nova autenticação após um período de tempo definido durante a interação de um utilizador com uma parte de alta segurança da sua aplicação web. 

## <a name="single-sign-on-sso-configuration"></a>Configuração de início de sessão único (SSO)

Se tiver várias aplicações e políticas no inquilino do B2C, pode gerir interações do utilizador através de lhes utilizando a propriedade de **configuração de início de sessão único** . Pode definir a propriedade para um dos seguintes definições:

- **Inquilino**: Esta é a predefinição. Utilizando esta definição permite que múltiplos aplicações e políticas no inquilino do B2C para partilhar a mesma sessão do utilizador. Por exemplo, assim que um utilizador inicia sessão numa aplicação, Contoso compras, pode também totalmente inicie sessão no outro medicina Contoso one, relativamente a aceder ao mesmo.
- **Aplicação**: esta opção permite-lhe manter uma sessão de utilizador exclusivamente para uma aplicação, independentemente de outras aplicações. Por exemplo, se pretender que o utilizador para iniciar sessão no Contoso medicina (com as mesmas credenciais), mesmo se é já iniciou sessão no Contoso compras, noutra aplicação no mesmo B2C do inquilino. 
- **Política**: esta opção permite-lhe manter uma sessão de utilizador exclusivamente para uma política, independentemente das aplicações do utilizá-lo. Por exemplo, se o utilizador já tem sessão iniciada no e concluído um passo de autenticação (MFA) fator multi, podem ser fornecida acesso a partes de uma maior segurança de várias aplicações desde que a sessão associada à política não expira.
- **Desativado**: Esta força o utilizador a executar através da viagem de utilizador inteira em cada execução da política. Por exemplo, isto irá permitir que vários utilizadores inscrever-se à sua aplicação (num cenário de ambiente de trabalho partilhada), mesmo durante a um único utilizador mantém-se com a sessão iniciada no durante o período de tempo inteiro.
