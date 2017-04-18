
<properties
    pageTitle="Requisitos de controlo de acesso de determinar do Azure Active Directory híbrido identidade considerações de estrutura - | Microsoft Azure"
    description="Abrange os montantes de identidade e identificar os requisitos de acesso para recursos para utilizadores num ambiente híbrido."
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

# <a name="determine-access-control-requirements-for-your-hybrid-identity-solution"></a>Determinar requisitos de controlo de acesso para a sua solução de identidade híbrido
Quando uma organização está a estruturar sua solução de identidade híbrido também podem utilizar este oportunidade para rever os requisitos de acesso para os recursos que está a planear para o tornar disponível para utilizadores. O acesso a dados entre sites quatro todos os montantes de identidade, que são:

- Administração
- Autenticação
- Autorização
- Auditoria

As secções que se segue irão abranger autenticação e autorização em obter mais detalhes, administração e auditoria fazem parte do ciclo de vida de identidade híbrido. Leia as [tarefas de gestão de identidades do determinar híbrido](active-directory-hybrid-identity-design-considerations-hybrid-id-management-tasks.md) para mais informações sobre estas capacidades.

>[AZURE.NOTE]
Leia [O quatro montantes de identidade - gestão de identidades no idade de TI híbrido](http://social.technet.microsoft.com/wiki/contents/articles/15530.the-four-pillars-of-identity-identity-management-in-the-age-of-hybrid-it.aspx) para mais informações sobre cada uma desses montantes.

## <a name="authentication-and-authorization"></a>Autorização e autenticação
Existem cenários diferentes para autenticação e autorização, estes cenários terão requisitos específicos que devem ser preenchidos pela solução de identidade híbrido que a empresa vai-se para adotar. Cenários que envolvam comunicação de (B2B) para as empresas podem adicionar um desafio adicional para administradores de TI, uma vez que precisam de Certifique-se de que o método de autenticação e autorização utilizado pela organização possam comunicar com os respetivos parceiros de negócios. Durante o processo de estruturação para requisitos de autenticação e autorização, certifique-se de que as seguintes questões respondidas:

- Irá autenticar e a sua organização autorizar apenas os utilizadores que se encontra no seu sistema de gestão de identidade?
 - Existem quaisquer planos para B2B cenários?
 - Se Sim, já saber quais os protocolos (SAML, OAuth, Kerberos, Tokens ou certificados) serão utilizados para ligar as duas empresas?
- Faz a solução de identidade de híbrido passar para adotar o suporte esses protocolos?

Outro ponto importante a ter em conta é onde o repositório de autenticação que será utilizado por utilizadores e parceiros estarão localizado e o modelo administrativo para ser utilizado. Considere as seguintes opções de dois principais:
- Centralizada: neste modelo credenciais do utilizador, políticas e administração podem ser centralizado no local ou na nuvem.
- Híbrido: neste modelo credenciais do utilizador, políticas e administração será centralizado no local e um replicada na nuvem.

Qual o modelo será adotar a sua organização irá variar de acordo com os seus requisitos de empresas, que pretende responder às seguintes perguntas para identificar onde o sistema de gestão de identidades residirão e o modo administrativo para:

- A sua organização atualmente tem uma gestão de identidades no local?
 - Se Sim, planear para mantê-lo?
 - Existem quaisquer requisitos de regulamento ou conformidade que sua organização tem de seguir que dita onde deve residem o sistema de gestão de identidade?
- Sua organização utiliza o início de sessão único para aplicações localizadas no local ou na nuvem?
 - Se Sim, a adoção de um modelo de identidade híbrido afeta este processo?

## <a name="access-control"></a>Controlo de acesso
Apesar de autenticação e autorização serem elementos principais para permitir o acesso aos dados da empresa através de validação do utilizador, também é importante controlar o nível de acesso que estes utilizadores terão e o nível de administradores de acesso terão o sobre os recursos que está a gerir. A solução de identidade híbrido têm de fornecer acesso granular recursos, delegação e controlo de acesso a base de função. Certifique-se de que a pergunta seguinte são respondida relativamente ao controlo de acesso:

- A sua empresa tem mais do que um utilizador com privilégios elevados para gerir o seu sistema de identidade?
 - Se Sim, cada utilizador necessitam do mesmo nível de acesso?
- Sua empresa de acesso de delegado para os utilizadores possam gerir recursos específicos?
 - Se Sim, frequência Isto acontece?
- Precisa sua empresa para integrar as capacidades de controlo de acesso entre no local e na nuvem recursos?
- Seria necessário a sua empresa limitar o acesso aos recursos de acordo com algumas condições?
- Sua empresa teria de qualquer aplicação que precisa de controlo personalizado aceder a alguns recursos?
 - Se Sim, onde estão nessas aplicações localizadas (no local ou na nuvem)?
 - Se Sim, onde são aqueles alvo recursos localizados (no local ou na nuvem)?

>[AZURE.NOTE]
Certifique-se tomar notas de cada resposta e compreender a lógica por detrás a resposta. [Definir estratégia de proteção de dados](active-directory-hybrid-identity-design-considerations-data-protection-strategy.md) irá abordar as opções disponíveis e vantagens/desvantagens de cada opção.  Responder a estas perguntas que irá selecionar qual a opção que melhor se adequa às suas necessidades de negócio.

## <a name="next-steps"></a>Próximos passos

[Determinar requisitos do incidente de resposta](active-directory-hybrid-identity-design-considerations-incident-response-requirements.md)

## <a name="see-also"></a>Consulte também
[Descrição geral de considerações de estrutura](active-directory-hybrid-identity-design-considerations-overview.md)
