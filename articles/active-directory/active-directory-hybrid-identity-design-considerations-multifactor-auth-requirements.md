<properties
    pageTitle="Azure Active Directory híbrido identidade considerações de estrutura - determinar requisitos de autenticação multifator"
    description="Com o controlo de acesso condicional, o Azure Active Directory verifica as condições específicas que escolher quando autenticar o utilizador e antes de permissão do acesso para a aplicação. Assim que forem cumpridas destas condições, o utilizador autenticado e permissão de acesso para a aplicação."
    documentationCenter=""
    services="active-directory"
    authors="femila"
    manager="billmath"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity" 
    ms.date="08/08/2016"
    ms.author="billmath"/>

# <a name="determine-multi-factor-authentication-requirements-for-your-hybrid-identity-solution"></a>Determinar requisitos de autenticação multifator para a sua solução de identidade híbrido

Este mundo da mobilidade, com utilizadores aceder aos dados e as aplicações na nuvem e a partir de qualquer dispositivo, proteger esta informação tornou fundamental.  Todos os dias existe um novo título sobre uma violação de segurança.  Embora, há garantias relativamente a essas violações, autenticação multifator, fornece uma camada adicional de segurança para ajudar a evitar estas quebras.
Começar por avaliar os requisitos de organizações para autenticação multifator. Isto é, o que é a organização tentar seguro.  Esta avaliação é importante para definir os requisitos técnicos para configurar e ativar os utilizadores organizações para autenticação multifator.

>[AZURE.NOTE]
Se não estiver familiarizado com MFA e o que faz, recomenda-se vivamente que leia o artigo [o que é o Azure a autenticação Multifator?](../multi-factor-authentication/multi-factor-authentication.md) prévia para continuar a ler esta secção.

Certifique-se atender o seguinte procedimento:

- Está a sua empresa tentar seguro aplicações Microsoft? 
- Como são publicadas estas aplicações?
- Sua empresa fornecida acesso remoto para permitir que os funcionários aceder a aplicações no local?

Se Sim, qual o tipo de acesso remoto? Também tem de avaliar onde os utilizadores que estão a aceder a estas aplicações será localizados. Esta avaliação é outra passo importante para definir a estratégia de autenticação multifator inicial maiúscula. Certifique-se atender as seguintes questões:

- Onde os utilizadores irão ser localizado?
- Podem eles estar localizados em qualquer lugar?
- Sua empresa o pretende estabelecer restrições de acordo com a localização do utilizador?

Assim que a compreender estes requisitos, é importante avaliar também os requisitos do utilizador para autenticação multifator. Esta avaliação é importante porque-definirá os requisitos para gradual saída autenticação multifator. Certifique-se atender as seguintes questões:

- Os utilizadores estiver familiarizado com autenticação multifator?
- Algumas utilizações serão necessárias para fornecer a autenticação adicional?  
 - Se Sim, o tempo, quando chegar a partir de redes externas ou aceder a aplicações específicas ou em outras condições?
- Os utilizadores necessitarão formação sobre como configurar e implementar a autenticação multifator?
- Quais são os cenários chaves que a sua empresa quer para ativar a autenticação multifator para os seus utilizadores?

Depois de responder as perguntas anteriores, será capaz de compreender se existirem autenticação multifator já implementada no local. Esta avaliação é importante para definir os requisitos técnicos para configurar e ativar os utilizadores organizações para autenticação multifator. Certifique-se atender as seguintes questões:

- É que a sua empresa necessita proteger privilegiadas contas com MFA?
- É que a sua empresa necessita ativar MFA para determinados aplicação por razões de conformidade?
- É que a sua empresa necessita ativar MFA para todos os utilizadores elegíveis destes aplicação ou apenas os administradores?
- Precisa de tem MFA ativado sempre ou apenas quando os utilizadores iniciados fora da rede da sua empresa?


## <a name="next-steps"></a>Próximos passos
[Definir uma estratégia de adoção de identidade híbrido](active-directory-hybrid-identity-design-considerations-identity-adoption-strategy.md)


## <a name="see-also"></a>Consulte também
[Descrição geral de considerações de estrutura](active-directory-hybrid-identity-design-considerations-overview.md)
