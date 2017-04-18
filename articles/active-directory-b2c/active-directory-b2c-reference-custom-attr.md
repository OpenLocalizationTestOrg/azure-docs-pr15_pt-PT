<properties
    pageTitle="Azure Active B2C de diretório: Atributos personalizados | Microsoft Azure"
    description="Como utilizar os atributos personalizados no Azure Active Directory B2C para recolher informações sobre os utilizadores poderão"
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

#  <a name="azure-active-directory-b2c-use-custom-attributes-to-collect-information-about-your-consumers"></a>Azure B2C diretório ativos: Utilizar os atributos personalizados para recolher informações sobre os utilizadores poderão

Diretório da sua Azure Active Directory (Azure AD) B2C vem com um conjunto de informações (atributos) incorporado: nome próprio, apelido, Localidade, Código Postal e outros atributos. No entanto, todas as aplicações do consumidor destinado possui requisitos exclusivos sobre o que atributos para reunir a partir do consumidores. Com o Azure AD B2C, pode expandir o conjunto de atributos armazenados em cada conta do consumidor. Pode criar atributos personalizados no [portal do Azure](https://portal.azure.com/) e utilizá-lo no seu políticas de inscrição, conforme apresentado abaixo. Também pode ler e escrever estes atributos ao utilizar a [API do Azure AD Graph](active-directory-b2c-devquickstarts-graph-dotnet.md).

> [AZURE.NOTE]
Atributos personalizados utilizam [extensões de esquema do Azure AD Graph API diretório](https://msdn.microsoft.com/library/azure/dn720459.aspx).

## <a name="create-a-custom-attribute"></a>Criar um atributo personalizado

1. [Siga estes passos para navegar para o pá de funcionalidades B2C no portal do Azure](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Clique em **atributos de utilizador**.
3. Clique em **+ Adicionar** no topo da pá.
4. Forneça um **nome** para o atributo personalizado (por exemplo, "ShoeSize") e, opcionalmente, uma **Descrição**. Clique em **Criar**.

    > [AZURE.NOTE]
    Apenas o "Cadeia" **Tipo de dados** está disponível neste momento.

O atributo personalizado está agora disponível na lista de **atributos de utilizador**e para utilização no seu políticas de inscrição.

## <a name="use-a-custom-attribute-in-your-sign-up-policy"></a>Utilizar um atributo personalizado na sua política de inscrição

1. [Siga estes passos para navegar para o pá de funcionalidades B2C no portal do Azure](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade).
2. Clique em **políticas de inscrição**.
3. Clique na política de inscrição (por exemplo, "B2C_1_SiUp") para o abrir. Clique em **Editar** no topo da pá.
4. Clique em **atributos inscrição** e selecione o atributo personalizado (por exemplo, "ShoeSize"). Clique em **OK**.
5. Clique em **afirmações da aplicação** e selecione o atributo personalizado. Clique em **OK**.
6. Clique em **Guardar** no topo da pá.

Pode utilizar a funcionalidade "Executar agora" sobre a política para verificar a experiência do consumidor. Agora deverá ver "ShoeSize" na lista de atributos recolhidas durante a inscrição do consumidor e vê-la no token de enviadas para a sua aplicação.

## <a name="notes"></a>Notas

- Juntamente com as políticas de inscrição, atributos personalizados podem também ser utilizados nas políticas de inscrição ou iniciar sessão e edição de políticas de perfil.
- Existe uma limitação conhecida de atributos personalizados. É criado apenas a primeira vez que é utilizado em qualquer política e não quando adicioná-lo para a lista de **atributos de utilizador**.
