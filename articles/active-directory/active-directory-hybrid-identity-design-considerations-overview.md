<properties
    pageTitle="Azure Active Directory híbrido identidade considerações de estrutura - descrição geral | Microsoft Azure"
    description="Descrição geral e um mapa de conteúdos do guia de considerações de estrutura de identidade híbrido"
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

# <a name="azure-active-directory-hybrid-identity-design-considerations"></a>Considerações de estrutura de identidade do Azure Active Directory híbrido

Dispositivos baseados em consumidor são proliferating parte do mundo empresarial e forem de baseado na nuvem software-como-a-(SaaS) aplicações de serviço adotar o. Como resultado, manutenção de controlo do acesso dos utilizadores a aplicação em internas plataformas centros de dados e nuvem é um desafio.  No local e capacidades baseadas na nuvem, criar uma identidade de utilizador único para autenticação e autorização para todos os recursos, independentemente da localização do intervalo de soluções de identidade da Microsoft. Chamamos esta identidade híbrido. Existem estrutura diferente e opções de configuração de identidade híbrido utilizando soluções do Microsoft e, em alguns casos, pode ser difícil determinar o melhor a combinação satisfazem as necessidades da sua organização. Este guia de considerações de estrutura de identidade híbrido irá ajudá-lo a compreender como para estruturar uma solução de identidade híbrido que melhor se adequa o negócio e tecnologia precisa para a sua organização.  Este guia será de detalhe numa série de passos e tarefas que pode seguir para o ajudar a estruturar uma solução de identidade híbrido que cumpre os requisitos exclusivos da sua organização. Ao longo da passos e tarefas, o guia irá apresentar as tecnologias relevantes e as opções de funcionalidade disponíveis para as organizações para reunir funcional e qualidade do serviço (como disponibilidade, escalabilidade, desempenho, capacidade de gestão e segurança) requisitos nível. Especificamente, considerações de estrutura de identidade híbrido guia aos objetivos são atender as seguintes questões: 

- O que perguntas preciso colocar e responder a unidade um híbrido identidade específicas design de um domínio tecnologia ou o problema que melhor satisfaz a minha requisitos?
- O que sequência de atividades deve concluir para estruturar uma solução de identidade híbrido para o domínio de tecnologia ou um problema 
- As opções de tecnologia e configuração de identidade do híbrido estão disponíveis para o ajudar a cumprir requisitos de minha? Quais são os compromissos entre estas opções para que o pode selecionar a melhor opção para a minha empresa?


## <a name="who-is-this-guide-intended-for"></a>Quem este guia destina?
 CIO, CITO, chefe identidade arquitectura, arquitectura empresarial e arquitectura de TI responsável para estruturar uma solução de identidade híbrido para organizações médias ou grandes.

## <a name="how-can-this-guide-help-you"></a>Como pode neste guia ajudá-lo? 
Pode utilizar este guia para compreender como estruturar uma solução de identidade híbrido que consiga integrar um sistema de gestão de identidades baseada na nuvem com a sua solução de identidade atual no local. A seguinte mostra gráfica um exemplo de uma solução de identidade de híbrido permite aos administradores de TI gerir para integrar a sua solução atual do Active Directory do Windows Server localizada no local com o Microsoft Azure Active Directory para permitir que os utilizadores utilizam o único início de sessão (SSO) através de aplicações localizadas na nuvem e no local.

![](./media/hybrid-id-design-considerations/hybridID-example.png)


A ilustração acima é um exemplo de uma solução de identidade de híbrido é tirar partido da serviços em nuvem para integrar com funcionalidades no local para fornecer uma experiência única para o processo de autenticação de utilizador final e facilitar IT gerir esses recursos. Apesar de esta pode ser um cenário muito comum, design de identidade da organização cada híbrido é provável que seja diferente do exemplo ilustrado na figura 1 devido a requisitos diferentes. Este guia fornece uma série de passos e tarefas que pode seguir para estruturar uma solução de identidade híbrido que cumpre os requisitos exclusivos da sua organização. Ao longo dos seguintes passos e tarefas, o Guia apresenta as tecnologias relevantes e opções de funcionalidade disponíveis que cumpra funcional e requisitos de nível de qualidade de serviço para a sua organização.

**Pressupostos**: tiver alguma experiência com o Windows Server, Active Directory Domain Services e Azure Active Directory. Neste documento, podemos partem do princípio de que está à procura como estas soluções podem satisfazer as necessidades da sua empresa sozinho ou numa solução integrada.

## <a name="design-considerations-overview"></a>Descrição geral de considerações de estrutura
Este artigo fornece um conjunto de passos e tarefas que pode seguir para estruturar uma solução de identidade híbrido que melhor corresponde às suas necessidades. Os passos são apresentados numa sequência ordenada. Considerações de estrutura que saiba nos passos posteriores podem pedir-lhe alterar as decisões que efetuou nos passos anteriores, no entanto, devido ao design conflituosas opções. Cada tentativa para alertá-lo para possíveis conflitos de estrutura ao longo do documento. 

Serão entregues na estrutura que melhor corresponde às suas necessidades apenas depois de iteração através dos passos as vezes conforme necessário para incorporar todas as considerações dentro do documento. 

| Fase de identidade híbrido                                             | Lista de tópicos                                                                                                                                                                                       |
|-------------------------------------------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Determinar requisitos de identidade                                   | [Determinar as necessidades da empresa](active-directory-hybrid-identity-design-considerations-business-needs.md)<br> [Determinar requisitos de sincronização de diretório](active-directory-hybrid-identity-design-considerations-directory-sync-requirements.md)<br> [Determinar requisitos de autenticação multifator](active-directory-hybrid-identity-design-considerations-multifactor-auth-requirements.md)<br> [Definir uma estratégia de adoção de identidade híbrido](active-directory-hybrid-identity-design-considerations-identity-adoption-strategy.md)                       |
| Plano para melhorar a segurança de dados através de solução identidades fortes | [Determinar requisitos de proteção de dados](active-directory-hybrid-identity-design-considerations-dataprotection-requirements.md) <br> [Determinar requisitos de gestão de conteúdo](active-directory-hybrid-identity-design-considerations-contentmgt-requirements.md)<br> [Determinar requisitos de controlo de acesso](active-directory-hybrid-identity-design-considerations-accesscontrol-requirements.md)<br> [Determinar requisitos do incidente de resposta](active-directory-hybrid-identity-design-considerations-incident-response-requirements.md) <br> [Definir estratégia de proteção de dados](active-directory-hybrid-identity-design-considerations-data-protection-strategy.md)  |
| Plano para o ciclo de vida de identidade de híbrido                                | [Determinar tarefas de gestão de identidades híbrido](active-directory-hybrid-identity-design-considerations-hybrid-id-management-tasks.md) <br> [Gestão de sincronização](active-directory-hybrid-identity-design-considerations-hybrid-id-management-tasks.md)<br> [Determinar estratégia de adoção de gestão de identidades híbrido](active-directory-hybrid-identity-design-considerations-lifecycle-adoption-strategy.md) |     


##<a name="download-this-guide"></a>Transferir este guia
Pode transferir uma versão pdf do Guia do considerações de estrutura de identidade híbrido a partir da [Galeria do Technet](https://gallery.technet.microsoft.com/Azure-Hybrid-Identity-b06c8288). 

                                                             
