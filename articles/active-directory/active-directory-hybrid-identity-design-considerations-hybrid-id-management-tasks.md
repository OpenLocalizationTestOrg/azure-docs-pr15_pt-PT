<properties
    pageTitle="Identidade de implementações do Azure Active Directory considerações de estrutura - determinar tarefas de gestão de identidades híbrido | Microsoft Azure"
    description="Com o controlo de acesso condicional, o Azure Active Directory verifica as condições específicas que escolher quando autenticar o utilizador e antes de permissão do acesso para a aplicação. Assim que forem cumpridas destas condições, o utilizador autenticado e permissão de acesso para a aplicação."
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

# <a name="plan-for-hybrid-identity-lifecycle"></a>Plano para o ciclo de vida do híbrido identidade 

Identidade é um dos fundações da sua estratégia de acesso de mobilidade e de aplicação empresarial. Se o que é iniciar sessão seu dispositivo móvel ou aplicação SaaS, a identidade do utilizador é a chave para obter acesso a tudo. No seu nível mais alto, uma solução de gestão de identidades engloba utilização e sincronização entre o seu repositórios de identidade que inclui a automatização e centralizando o processo de aprovisionamento de recursos. A solução de identidade deve ser uma identidade centralizada as no local e na nuvem e também utilizar alguma forma de Federação de identidade para manter a autenticação centralizada e segura para partilhar e colaborar com utilizadores externos e empresas. Recursos o intervalo de sistemas operativos e aplicações para pessoas no livro ou afiliada com uma organização. Estrutura organizacional pode ser alterada para acomodar as políticas de aprovisionamento e procedimentos.

Também é importante ter uma solução de identidade orientada para adquira os seus utilizadores, fornecendo-los com experiências personalizadas para mantê-las mais produtivo. A solução de identidade é mais robusta se permite-serviço single sign-on para os utilizadores em todos os recursos que precisam de aceder a todos os administradores níveis podem utilizar padronizados procedimentos para gerir credenciais de utilizador. Alguns níveis de administração podem ser reduzidos ou eliminados, dependendo da boca da solução de gestão de aprovisionamento. Além disso, pode segura distribuir capacidades de administração, manual ou automaticamente, entre vários organizações. Por exemplo, um administrador de domínio pode servir apenas as pessoas e recursos nesse domínio. Este utilizador pode fazer tarefas administrativas e de aprovisionamento, mas não está autorizado para efetuar tarefas de configuração, tais como criar fluxos de trabalho.


## <a name="determine-hybrid-identity-management-tasks"></a>Determinar tarefas de gestão de identidades híbrido
Distribuir a tarefas administrativas na sua organização melhora a precisão e eficiência da administração e melhora o saldo da carga de trabalho de uma organização. Seguem-se a tabelas dinâmicas que definem um sistema de gestão de identidades robusta.

 ![](./media/hybrid-id-design-considerations/Identity_management_considerations.png)


Para definir o tarefas de gestão de identidades híbrido, tem de compreender algumas características essenciais da organização que vai ser aprovar identidade híbrido. É importante compreender repositórios atuais a ser utilizados para origens de identidade. Ao saber nesses elementos principais, terá os requisitos de base destinados e com base no que terá de fazer perguntas mais granulares que irão guiá-lo para uma melhor decisão de estrutura para a sua solução de identidade.  

Ao definir essas exigências, certifique-se de que, pelo menos, as seguintes questões respondidas

- Opções de aprovisionamento: 
 - A solução de identidade híbrido suporta um sistema de aprovisionamento e gestão da conta robusto de acesso?
 - Como são utilizadores, grupos e aceder a ser gerido de palavras-passe?
 - Está a responder a gestão de ciclo de vida de identidade? 
      - Quanto tempo demora a suspensão de conta de atualizações de palavra-passe?
      
- Gestão de licenças: 
 - Faz a gestão de licença do híbrido identidades solução alças?
     - Se Sim, que funcionalidades estão disponíveis?
- A solução alça de gestão de licenças com base no grupo? 
      -Se Sim, é possível atribuir-lhe um grupo de segurança? 
       -Se Sim, será o diretório de nuvem automaticamente atribuir licenças a todos os membros do grupo? 
        -O que acontece se um utilizador subsequentemente é adicionado ou removido do grupo, será uma licença automaticamente atribuída ou removida conforme adequada? 

- Integração com outros fornecedores de identidade de terceiros:
- Esta solução híbrido pode ser integrada com fornecedores de identidade de terceiros para implementar o início de sessão único?
- É possível uniformizar todos os fornecedores de identidade diferente para um sistema de identidade coesa?
- Se Sim, como e os que são estas e que funcionalidades estão disponíveis?

## <a name="synchronization-management"></a>Gestão de sincronização
Uma aos objetivos de um Gestor de identidade, possam apresentar todos os fornecedores de identidade e mantê-las sincronizada. Que manter os dados sincronizados com base num fornecedor de identidades mestra autoritativas. Num cenário de identidade híbrido, com um modelo de gestão sincronizada, a gestão de identidades de todos os utilizadores e dispositivos num servidor no local e sincronizar as contas e, opcionalmente, palavras-passe para a nuvem. O utilizador introduz o mesma palavra-passe no local, como ele ou ela faz na nuvem e no início de sessão, a palavra-passe é verificada pela solução de identidade. Este modelo utiliza uma ferramenta de sincronização de diretório.
 
![](./media/hybrid-id-design-considerations/Directory_synchronization.png)Para design adequado a sincronização da sua solução de identidade híbrido Certifique-se de que as seguintes questões respondidas: • quais são as soluções de sincronização disponíveis para a solução de identidade híbrido?
• O que são o single sign-on funcionalidades disponíveis?
• Quais são as opções na Federação de identidade entre B2B e B2C?

## <a name="next-steps"></a>Próximos passos
[Determinar estratégia de adoção de gestão de identidades híbrido](active-directory-hybrid-identity-design-considerations-lifecycle-adoption-strategy.md)


## <a name="see-also"></a>Consulte também
[Descrição geral de considerações de estrutura](active-directory-hybrid-identity-design-considerations-overview.md)

