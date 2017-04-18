<properties
    pageTitle="Azure Active B2C de diretório: Inquilinos escala de produção vs. pré-visualização B2C | Microsoft Azure"
    description="Um tópico nos tipos de inquilinos do Azure Active Directory B2C"
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
    ms.date="08/30/2016"
    ms.author="swkrish"/>

# <a name="azure-active-directory-b2c-production-scale-vs-preview-b2c-tenants"></a>Azure Active B2C de diretório: Inquilinos de escala de produção vs. pré-visualização B2C

Se estiver a planear escrever uma aplicação de produção no Azure Active Directory (Azure AD) B2C, terá de ter a certeza de que tem o inquilino direita "tipo" para ir direto no. Para ver o que tiver, siga estes passos para [Navegar para o pá funcionalidades B2C](active-directory-b2c-app-registration.md#navigate-to-the-b2c-features-blade) no portal do Azure e procure em **tipo de inquilino**.

## <a name="summary"></a>Resumo

Azure AD B2C suporta apenas em **escala de produção** B2C inquilinos na América do Norte de aplicações de produção.

| Tipo de inquilino | Países/regiões | Geralmente-disponíveis? |
| ----------- | -------------- | --------------------- |
| **Inquilino de escala de produção** | Países/regiões da América do Norte | Sim |
| **Inquilino de escala de produção** | Todos os países/regiões exceto América do Norte | N |
| **Inquilino de pré-visualização** | Todos os países/regiões | N |

> [AZURE.NOTE]
Azure AD B2C inquilinos (para os consumidores) não estão atualmente disponíveis em alguns países ou regiões onde inquilinos do Azure AD (para funcionários) estão disponíveis. Leia as secções seguintes para obter mais detalhes.

## <a name="production-scale-b2c-tenant-in-north-america"></a>Inquilino B2C de escala de produção na América do Norte

Se [criou o seu inquilino B2C](active-directory-b2c-get-started.md) na América do Norte, por exemplo, de uma das seguintes países ou regiões: United Unidos, Canadá, Costa Rica, República Dominicana, El Salvador, Guatemala, México, Panamá, Porto Rico e Trindade e Tobago e o **tipo de inquilino** no seu IU para administração B2C diz **escala de produção**, o inquilino pode ser utilizado para as aplicações de produção.

> [AZURE.NOTE]
Inquilinos de escala de produção são capazes de dimensionamento para 100s de milhões de identidades do consumidor por inquilino.

![Captura de ecrã de um inquilino de escala de produção](./media/active-directory-b2c-reference-tenant-type/production-scale-b2c-tenant.png)

## <a name="preview-b2c-tenant-in-any-countryregion"></a>Pré-visualizar inquilino de B2C em qualquer país/região

Se tiver criado um inquilino B2C durante o período de pré-visualização do Azure AD B2C, é provável que seu **inquilino escreva** diz **Pré-visualizar inquilino**. Se este for o caso, tem de utilizar o inquilino apenas para fins de testes de desenvolvimento e e não para aplicações de produção.

> [AZURE.IMPORTANT]
Não existe nenhum caminho de migração a partir de uma pré-visualização B2C inquilino para um inquilino B2C de escala de produção. Tenha em atenção que existem conhecidos problemas quando eliminar um inquilino pré-visualização B2C e voltar a criar um inquilino B2C de escala de produção com o mesmo nome de domínio. Tem de criar um inquilino B2C de escala de produção com um nome de domínio diferente.

![Captura de ecrã de um inquilino pré-visualização](./media/active-directory-b2c-reference-tenant-type/preview-b2c-tenant.png)

## <a name="production-scale-b2c-tenant-outside-of-north-america"></a>Inquilino B2C de escala de produção fora da América do Norte

Azure AD B2C não está neste momento-disponibilizado fora da América do Norte. No entanto pode criar e utilizar inquilinos de escala de produção, para o desenvolvimento e teste fins de uma das seguintes países ou regiões: Argélia, Áustria, Azerbaijão, Barém, Bielorrússia, Bélgica, Bulgária, Croácia, Chipre, República Checa, Dinamarca, Egypt (مصر), Estónia, Finlândia, França, Alemanha, Grécia, Hungria, Islândia, Irlanda, Israel, Itália, Jordânia, Cazaquistão, Quénia, Kuwait, Lativa, Líbano, Listenstaine, Lituania, Luxemburgo, Macedónia Macedónia, Malta, Montenegro, Marrocos, Países Baixos, Nigéria, Noruega , Omã, Paquistão, Polónia, Portugal, Catar, Roménia, Rússia, Arábia Saudita, Sérvia, Eslováquia, Eslovénia, África do Sul, Espanha, Suécia, Suíça, Tunísia, Turquia, Ucrânia, Unidos Emirados Árabes Unidos e Reino Unido.

Assim que Azure AD B2C anuncia disponibilidade geral no acima países ou regiões, pode continuar a utilizar estes inquilinos de escala de produção e ativação com as aplicações de produção sem quaisquer perdas de dados.

## <a name="availability-of-b2c-tenants"></a>Disponibilidade de inquilinos do B2C

B2C inquilinos não estão atualmente disponíveis nas seguintes países ou regiões: Afeganistão, Argentina, Austrália, Brasil, Chile, Colômbia, Equador, RAE de Hong Kong, Índia, Indonésia, Iraque, Japão, Coreia, Malásia, Nova Zelândia, Paraguai, Peru, Filipinas, Singapura, Sri Lanca, Taiwan, Thailand (ไทย), Uruguai e Venezuela. Planeamos incluí-los no futuro.
