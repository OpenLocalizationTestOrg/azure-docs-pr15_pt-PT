<properties
    pageTitle="Requisitos de proteção de dados de determinar do Azure Active Directory híbrido identidade considerações de estrutura - | Microsoft Azure"
    description="Quando planear a sua solução de identidade híbrido, identifique os requisitos de proteção de dados para o seu negócio e que opções estão disponíveis para melhor cumprir estes requisitos."
    documentationCenter=""
    services="active-directory"
    authors="billmath"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity" 
    ms.date="08/08/2016"
    ms.author="billmath"/>

#<a name="plan-for-enhancing-data-security-through-strong-identity-solution"></a>Plano para melhorar a segurança de dados através de solução identidades fortes

O primeiro passo para proteger os dados é identificar quem pode aceder a esses dados e como parte deste processo que tem de ter uma identidade solução que pode integra-se com o seu sistema para fornecer capacidades de autenticação e autorização. Autenticação e autorização frequentemente confusos entre si e respectivas funções mal interpretados. Na realidade, são bastante diferentes, tal como apresentado na figura abaixo:

![](./media/hybrid-id-design-considerations/mobile-devicemgt-lifecycle.png)
 
**Fases de ciclo de vida de gestão do dispositivo móvel**

Ao planear a sua solução de identidade híbrido deve compreender os requisitos de proteção de dados para o seu negócio e que opções estão disponíveis para melhor cumprir estes requisitos.
 
>[AZURE.NOTE]
Assim que acabar de planeamento de segurança dos dados, reveja a [determinar os requisitos de autenticação multifator](active-directory-hybrid-identity-design-considerations-multifactor-auth-requirements.md) para se certificar de que as suas seleções relativamente ao requisitos de autenticação multifator não foram afectadas pelas decisões que efetuou nesta secção.

## <a name="determine-data-protection-requirements"></a>Determinar requisitos de proteção de dados
Na idade de mobilidade, a maior parte das empresas tem um objetivo comuns: permitir que os seus utilizadores ser produtivo esteja onde os seus dispositivos móveis enquanto no local ou remotamente a partir de qualquer lugar para aumentar a produtividade. Enquanto Isto pode ser um objetivo comuns, empresas que têm esse requisito também estarão preocupações em relação à quantidade de ameaças que devem ser atenuada para manter os dados da empresa seguros e manter a privacidade do utilizador. Cada empresa poderá têm requisitos diferentes este respeito; regras de conformidade diferentes que variam de acordo com as quais a indústria a empresa está a agir, serão direcionado para decisões de estrutura diferente. 

No entanto, existem alguns aspetos de segurança que devem ser explorou e validados independentemente da indústria, que são explicados na secção seguinte.

## <a name="data-protection-paths"></a>Caminhos de proteção de dados

![](./media/hybrid-id-design-considerations/data-protection-paths.png)
 
**Caminhos de proteção de dados**

No diagrama de acima, o componente de identidade estará a primeira parte para ser validada antes de dados são acedidos. No entanto, estes dados podem ser diferentes Estados dos durante o período de tempo que foi acedido. Cada número num diagrama deste representa um caminho na qual dados podem ser localizados em algumas ponto no tempo. Estes números são explicados abaixo:

1. Proteção de dados ao nível do dispositivo.
2. Proteção de dados enquanto estão em trânsito.
3. Proteção de dados enquanto na resto no local.
4. Proteção de dados enquanto em repouso na nuvem.

Apesar de técnicas controla que ativar IT para proteger os dados propriamente ditos em cada um dessas fases não são diretamente oferecidos pela solução de identidade híbrido, é necessário que a solução de identidade do híbrido é compatível com a tirar partido no local e na nuvem recursos de gestão de identidades para identificar o utilizador antes de conceder acesso aos dados. Quando planear a sua solução de identidade híbrido Certifique-se de que as seguintes questões respondidas acordo com os requisitos da sua organização:

## <a name="data-protection-at-rest"></a>Proteção de dados em repouso
Independentemente de onde estão os dados em repouso (dispositivo, na nuvem ou no local), é importante efetuar uma avaliação para compreender as necessidades da organização este respeito. Para esta área, certifique-se de que são-lhe pedidas as seguintes questões:

- É que a sua empresa necessita proteger os dados no resto?
 - Sim, se a solução de identidade híbrido conseguirá para integrar com a sua infraestrutura no local atual?
 - Sim, se a solução de identidade híbrido conseguirá para integrar com o seu das cargas de trabalho localizadas na nuvem?
- Gestão de identidades da nuvem é possível proteger as credenciais do utilizador e outros dados armazenados na nuvem?

## <a name="data-protection-in-transit"></a>Proteção de dados estão em trânsito
Dados estão em trânsito entre o dispositivo e o Centro de dados ou entre o dispositivo e na nuvem devem ser protegidos. No entanto, a ser em trânsito não significa necessariamente um processo de comunicações com um componente de fora do seu serviço de nuvem; esta passa internamente, para além disso, tal como entre duas redes virtuais. Para esta área, certifique-se de que são-lhe pedidas as seguintes questões:

- É que a sua empresa necessita de proteger os dados estão em trânsito?
 - Sim, se a solução de identidade híbrido conseguirá para integrar com controlos seguros como SSL/TLS?
- A gestão de identidades nuvem manter o tráfego de e para dentro da loja de diretório (no interior e entre centros de dados) assinado?


## <a name="compliance"></a>Conformidade
Regulamentos, leis e requisitos de conformidade de regulamentação variam de acordo com a indústria a que pertence a sua empresa. Empresas de alta ramos regulamentados tem endereço preocupações de gestão de identidades relacionados com problemas de conformidade. Regulamentos como normas Sarbanes-Oxley (SOX), o portabilidade seguros de saúde e responsabilidade Act HIPAA (), a lei Gramm-Leach-Bliley Act (GLBA) e o pagamento cartão indústria dados segurança padrão (PCI DSS) são muito estritamente em relação à identidade e de acesso. A solução de identidade híbrido que a sua empresa irá adotar tem de ter as capacidades de core irão cumprir os requisitos de uma ou mais destes regulamentos. Para esta área, certifique-se de que são-lhe pedidas as seguintes questões:

- A solução de identidade híbrida está em conformidade com os requisitos de regulamentação para a sua empresa?
- A solução de identidade híbrido tem incorporadas nas funcionalidades que permitirão a sua empresa para ser compatível com normas reguladoras? 
 
>[AZURE.NOTE]
Certifique-se tomar notas de cada resposta e compreender a lógica por detrás a resposta. [Definir estratégia de proteção de dados](active-directory-hybrid-identity-design-considerations-data-protection-strategy.md) irá abordar as opções disponíveis e vantagens/desvantagens de cada opção.  Por ter respondido estas perguntas que irá selecionar qual a opção que melhor se adequa a sua empresa necessita.

## <a name="next-steps"></a>Próximos passos
 [Determinar requisitos de gestão de conteúdo](active-directory-hybrid-identity-design-considerations-contentmgt-requirements.md)


## <a name="see-also"></a>Consulte também
[Descrição geral de considerações de estrutura](active-directory-hybrid-identity-design-considerations-overview.md)
