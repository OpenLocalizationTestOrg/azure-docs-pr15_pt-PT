<properties
    pageTitle="Azure Active Directory híbrido identidade considerações de estrutura - determinar requisitos de identidade | Microsoft Azure"
    description="Identifica as necessidades da empresa da empresa que irão guiá-lo para definir os requisitos para a estrutura de identidade híbrido."
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

# <a name="determine-identity-requirements-for-your-hybrid-identity-solution"></a>Determinar requisitos de identidade para a sua solução de identidade híbrido
Estruturar uma solução de identidade híbrido o primeiro passo é determinar os requisitos para a organização de empresas que vai ser tirar partido desta solução.  Identidade híbrido inicia como uma função secundária (suporta todas as outras soluções de nuvem, fornecendo autenticação) e vai para fornecer as funcionalidades novas e interessantes que desbloquear novas das cargas de trabalho para os utilizadores.  Das cargas de trabalho ou os serviços que pretende que a tomar para os utilizadores irão ditar os requisitos para a estrutura de identidade híbrido.  Estes serviços e das cargas de trabalho precisam tirar partido identidade híbrido ambas no local e na nuvem.  

Precisa de abordar estes aspectos chaves do negócio para compreender o que é um requisito agora e o que a empresa planos para o futuro. Se não tiver a visibilidade da estratégia longo prazo para a estrutura de identidade híbrido, provavelmente que a solução não será dimensionável medida que as necessidades de negócio aumentar e alterar.   T ele de diagrama abaixo mostra um exemplo de uma arquitetura de identidade híbrido e as cargas de trabalho que estão a ser desbloqueadas para os utilizadores. Este é apenas um exemplo de todas as possibilidades novas que podem ser desbloqueados e entregue com uma estratégia de identidade híbrido sólida. 
 
Alguns componentes que fazem parte da arquitetura de identidade híbrido![](./media/hybrid-id-design-considerations/hybrid-identity-architechture.png)

## <a name="determine-business-needs"></a>Determinar as necessidades da empresa
Cada empresa irá têm requisitos diferentes, mesmo se estas empresas fazem parte da indústria mesmo, o negócio real podem variar de requisitos. Pode ainda tirar partido melhores práticas da indústria, mas, finalmente, é as necessidades da empresa da empresa que irão guiá-lo para definir os requisitos para a estrutura de identidade híbrido. 

Certifique-se atender as seguintes questões para identificar as necessidades da sua empresa:

- É sua empresa está à procura para cortar custo operacional IT?
- É sua empresa está à procura para proteger elementos da nuvem (SaaS aplicações, infraestrutura)?
- Está a sua empresa localizar modernização a da empresa?
  - São os seus utilizadores mais móvel e excessiva IT para criar exceções para o seu DMZ para permitir que o tipo de tráfego para aceder a recursos diferentes diferente?
  - A sua empresa tem aplicações legadas que se necessário, para ser publicado estes utilizadores modernos, mas não são fácil reescrever?
  - Sua empresa necessitam de efetuar as seguintes tarefas e colocá-lo em controlo ao mesmo tempo?
- É sua empresa está à procura para proteger as identidades dos utilizadores e reduzir o risco colocando novas ferramentas tirar partido dos conhecimentos da Microsoft Azure segurança conhecimentos no local?
- Está a sua empresa tentar eliminar as contas "externas" dreaded no local e movê-las na nuvem, onde já não forem uma ameaça Inativos no interior do seu ambiente no local?

## <a name="analyze-on-premises-identity-infrastructure"></a>Analisar infraestrutura de identidade no local
Agora que tem uma ideia em relação à sua requisitos de negócio da empresa, tem de avaliar a sua infraestrutura de identidade no local. Esta avaliação é importante para definir os requisitos técnicos para integrar a sua solução de identidade atual para o sistema de gestão de identidades na nuvem. Certifique-se atender as seguintes questões:

- Solução que autenticação e autorização é que a sua empresa utilizar no local? 
- A sua empresa tem atualmente quaisquer serviços de sincronização no local?
- De que a sua empresa utiliza qualquer fornecedores de identidade de terceiros (IdP)?

Também tem de ter em mente dos serviços na nuvem que pode ter a sua empresa. Efetuar uma avaliação para compreender a integração com os modelos no seu ambiente SaaS, IaaS ou PaaS atual é muito importante. Certifique-se atender as seguintes questões durante este avaliação:
- A sua empresa tem qualquer integração com um fornecedor de serviços na nuvem?
- Se Sim, os serviços que estão a ser utilizados?
- Está a ser esta integração produção ou é um piloto?


>[AZURE.NOTE]
Se não tiver um mapeamento de precisão de todas as suas aplicações e serviços em nuvem, pode utilizar a ferramenta de deteção de aplicação na nuvem. Esta ferramenta pode fornecer o seu departamento de TI com visibilidade do negócio da sua organização e apps de nuvem do consumidor. Que mais fácil descobrir sombra IT, na sua organização, incluindo detalhes sobre como padrões de utilização e quaisquer utilizadores aceder a suas aplicações na nuvem. Para aceder a esta ferramenta, aceda a [https://appdiscovery.azure.com](https://appdiscovery.azure.com/)

## <a name="evaluate-identity-integration-requirements"></a>Avaliar os requisitos de integração de identidade
Em seguida, tem de avaliar os requisitos de integração de identidade. Esta avaliação é importante para definir os requisitos técnicos para como os utilizadores irão autenticar, qual será o aspeto da presença da organização na nuvem, como a organização permitirá autorização e o que a experiência do utilizador vai ser. Certifique-se atender as seguintes questões:

- Será sua organização utilizar Federação, autenticação padrão ou ambas?
- É um requisito de Federação?  Devido às seguintes:
 - Baseada em Kerberos SSO
 - Sua empresa tiver um aplicações no local (quer incorporado sessões ou 3º terceiros) que utiliza SAML ou funcionalidades de Federação semelhantes.
 - MFA através de Smart Cards. RSA SecurID, etc.
 - Regras de acesso de cliente que resolvem questões que se seguem:
     1. Para bloquear todo o acesso externo para o Office 365 com base no endereço IP do cliente?
     1. Para bloquear todo o acesso externo para o Office 365, exceto o Exchange ActiveSync?
     1. Para bloquear o acesso externo todos os para o Office 365, exceto baseada no browser e apps (OWA, SPO)
     1. Pode bloquear todas as acesso externo para o Office 365 para membros de grupos de AD designados
- Preocupações/auditoria de segurança
- Investimento já existente na autenticação federado
- Qual é o nome nossa organização irá utilizar para o nosso domínio na nuvem?
- A organização tem um domínio personalizado?
    1. É esse domínio públicas e facilmente controlado através do DNS?
    1. Se não for, em seguida, possui um domínio de público que pode ser utilizado para registar um UPN alternativo no AD?
- Os identificadores de utilizador são consistentes para representação de nuvem? 
- A organização tem as aplicações que necessitam de integração com serviços em nuvem?
- A organização tiver múltiplos domínios e todos eles utilizará a autenticação de padrão ou federada?

## <a name="evaluate-applications-that-run-in-your-environment"></a>Avaliar as aplicações que são executadas no seu ambiente
Agora que tem uma ideia em relação à sua no local e na nuvem infraestrutura, tem de avaliar as aplicações que são executadas nestes ambientes. Esta avaliação é importante para definir os requisitos técnicos para integrar estas aplicações para o sistema de gestão de identidades na nuvem. Certifique-se atender as seguintes questões:

- Onde irão live nossas aplicações?
- Os utilizadores irão estar a aceder a aplicações no local?  Na nuvem? Ou ambas?
- Existem planos para tirar as cargas de trabalho de aplicação existente e movê-las para a nuvem?
- Existem planos para desenvolver aplicações novas que irão residem quer no local ou na nuvem que irá utilizar na nuvem autenticação?

## <a name="evaluate-user-requirements"></a>Avaliar as necessidades dos utilizadores
Também tem de avaliar os requisitos dos utilizadores. Esta avaliação é importante para definir os passos que serão necessárias para no embarque e prestar assistência aos utilizadores, tal como estes de transição para a nuvem. Certifique-se atender as seguintes questões:

- Os utilizadores irão estar a aceder a aplicações no local?
- Os utilizadores irão estar a aceder a aplicações na nuvem?
- Como é normalmente utilizadores iniciar sessão no seu ambiente no local?
- Como serão os utilizadores iniciar sessão para a nuvem?

>[AZURE.NOTE]
Certifique-se tomar notas de cada resposta e compreender a lógica por detrás a resposta. [Requisitos de resposta incidente determinar](active-directory-hybrid-identity-design-considerations-incident-response-requirements.md) irá abordar as opções disponíveis e profissionais/desvantagens de cada opção.  Por ter respondido estas perguntas que irá selecionar qual a opção que melhor se adequa a sua empresa necessita.

## <a name="next-steps"></a>Próximos passos
[Determinar requisitos de sincronização de diretório](active-directory-hybrid-identity-design-considerations-directory-sync-requirements.md)

## <a name="see-also"></a>Consulte também
[Descrição geral de considerações de estrutura](active-directory-hybrid-identity-design-considerations-overview.md)
