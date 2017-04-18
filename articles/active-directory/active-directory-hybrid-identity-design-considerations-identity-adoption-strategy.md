<properties
    pageTitle="Azure Active Directory híbrido identidade considerações de estrutura - definir uma estratégia de adoção de identidade híbrido | Microsoft Azure"
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


# <a name="define-a-hybrid-identity-adoption-strategy"></a>Definir uma estratégia de adoção de identidade híbrido

Nesta tarefa, irá definem a estratégia de adoção de identidade híbrido para a sua solução de identidade híbrido cumprir os requisitos de empresas que foram abordados no:

- [Determinar as necessidades da empresa](active-directory-hybrid-identity-design-considerations-business-needs.md)
- [Determinar requisitos de sincronização de diretório](active-directory-hybrid-identity-design-considerations-directory-sync-requirements.md)
- [Determinar requisitos de autenticação multifator](active-directory-hybrid-identity-design-considerations-multifactor-auth-requirements.md)

## <a name="define-business-needs-strategy"></a>Definir estratégia de necessidades de negócio
Os primeiros endereços de tarefa para determinar o negócio organizações necessita.  Isto pode ser muito grande e fluência âmbito pode ocorrer se não estiver cuidado.  No início mantenha-a simples mas lembre-se planear uma estrutura que irá acomodar e facilitar a alteração no futuro.  Independentemente de se tratar de uma estrutura simples ou uma extremamente complexa, o Azure Active Directory é a plataforma de Microsoft Identity que suporta aplicações do Office 365, Microsoft Online Services e nuvem deverá ter em consideração.

## <a name="define-an-integration-strategy"></a>Definir uma estratégia de integração
A Microsoft tem três cenários de integração principal que são identidades na nuvem, identidades sincronizadas e identidades federadas.  Deverá planear no aprovar uma destas estratégias de integração.  A estratégia que selecione podem variar e podem incluir as decisões ao selecionar uma opção, que tipo de experiência do utilizador que quer fornecer, tem algumas da infraestrutura de existente já no local e o que é o mais rentável.  
 
![](./media/hybrid-id-design-considerations/integration-scenarios.png)

Os cenários definidos na figura acima são:

- **Identidades nuvem**: estas são as identidades que existam exclusivamente na nuvem.  No caso do Azure AD, estes residem especificamente no seu diretório do Azure AD.
- **Sincronizado**: estas são as identidades existentes no local e na nuvem.  Utilizar a ligação do Azure AD, estes utilizadores são criados ou associadas com contas do Azure AD existentes.  Hash de palavra-passe do utilizador é sincronizada a partir do ambiente no local para a nuvem no que é designado por um hash de palavra-passe.  Quando utilizar sincronizada um truque é que se um utilizador estiver desativado no ambiente no local, pode demorar até 3 horas para esse Estado da conta ser apresentada nos Azure AD.  Este é devido o intervalo de tempo de sincronização.
- **Federados**: estas identidades existem ambas no local e na nuvem.  Utilizar a ligação do Azure AD, estes utilizadores são criados ou associadas com contas do Azure AD existentes.  
 
>[AZURE.NOTE]
Para obter mais informações sobre a sincronização de opções de leitura [integrar a suas identidades no local com o Azure Active Directory](active-directory-aadconnect.md).

Irá ajudar a tabela seguinte para determinar as vantagens e desvantagens de cada um dos seguintes estratégias:

| Estratégia         | Vantagens                                                                                                                                                                                                                                                  | Desvantagens                                                                                                                                                                                                                                                                                                                                                  |
|------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Nuvem identidades** | Mais fácil de gerir para a organização pequenas. <br> Nada para instalar o hardware adicional no-local-não for necessário<br>Desativado facilmente se o utilizador sai da empresa                                                                                                   | Os utilizadores terão de entrada ao aceder das cargas de trabalho na nuvem <br> As palavras-passe podem ou não podem ser a mesma para identidades na nuvem e no local                                                                                                                                                                                                                     |
| **Sincronizados**     | Palavra-passe de no local será autenticar ambas no local e na nuvem diretórios <br>Mais fácil de gerir para organizações pequenas, médias ou grandes <br>Os utilizadores podem ter sessão único (SSO) para alguns recursos <br> Método de Microsoft preferido para a sincronização <br> Mais fácil de gerir | Alguns clientes poderão estar relutante sincronizar os respetivos directórios com a nuvem para conclusão policiais específicos da empresa                                                                                                                                                                                                                                                  |
| **Federado**        | Os utilizadores podem ter sessão único (SSO) <br>Se um utilizador for terminado ou deixa, a conta pode, desactivada imediatamente e acesso revogado,<br> Suporta avançadas cenários que não podem ser executadas com sincronizados                                           | Obter os passos de configuração e configurar <br> Manutenção mais elevada <br> Pode requerer hardware adicional para a infraestrutura de STS <br> Precisem de hardware adicional para instalar o servidor de Federação. É necessário software adicional se for utilizado o AD FS <br> Exigir extensa configuração para SSO <br> Ponto de crítica de falha se o servidor de Federação for para baixo, os utilizadores não será possível autenticar |

### <a name="client-experience"></a>Experiência do cliente
A estratégia que utilizar irá ditar a experiência de utilizador de início de sessão.  As tabelas seguintes fornecem informações sobre os utilizadores que devem esperar sua experiência de início de sessão para ser.  Note que não em todos os fornecedores de identidade federada suportam SSO em todos os cenários.

**Aplicações de rede façam parte de um domínio e privadas**:
 

|                              | Identidade sincronizada      | Identidade federada                                           |
|------------------------------|----------------------------|--------------------------------------------------------------|
| Browsers                 | Autenticação baseada em formulários | início de sessão único, por vezes necessário fornecer o ID da organização |
| Outlook                      | Pedido para introduzir as credenciais     | Pedido para introduzir as credenciais                                       |
| Skype para empresas (Lync)    | Pedido para introduzir as credenciais     | sessão único para o Lync, lhe for pedido credenciais do Exchange   |
| Do SkyDrive Pro                 | Pedido para introduzir as credenciais     | Single Sign-on                                               |
| Office Pro Plus subscrição | Pedido para introduzir as credenciais     | Single Sign-on                                               |

**Externos ou não fidedignos origens**:

|                              | Identidade sincronizada      | Identidade federada                                           |
|------------------------------|----------------------------|--------------------------------------------------------------|
| Browsers                 | Autenticação baseada em formulários |  Autenticação baseada em formulários |
| Outlook, do Skype para empresas (Lync), Skydrive Pro, subscrição do Office| Pedido para introduzir as credenciais     | Pedido para introduzir as credenciais                                       |
| O Exchange ActiveSync    | Pedido para introduzir as credenciais     | sessão único para o Lync, lhe for pedido credenciais do Exchange   |
| Aplicações móveis                 | Pedido para introduzir as credenciais     | Pedido para introduzir as credenciais                                               |

Se tiver determinado da tarefa que tem um 3 festa aceder IdP ou estão a utilizar uma para fornecer a Federação com Azure AD de 1, é necessário ter em consideração as seguintes capacidades suportadas:
- Qualquer fornecedor de SAML 2.0 que seja compatível com para o perfil SP Lite pode suportar uma autenticação para Azure AD e aplicações associadas
- Suporta a autenticação passiva, que facilita auth para do OWA, SPO, etc.
- Podem ser suportados os clientes do Exchange Online através de SAML 2.0 avançada cliente Perfil (ECP)

Também tem de estar em mente que funcionalidades não estarão disponíveis:

- Sem suporte WS-fidedignidade/Federation, irão interromper todos os outros clientes ativos
 - Isto significa que não cliente do Lync, o cliente OneDrive, subscrição do Office, o Office Mobile anterior ao Office 2016
- A transição do Office para autenticação passiva permitirá-los suportar puro SAML 2.0 IdPs, mas o suporte ainda estará numa base de cliente por cliente


>[AZURE.NOTE]
Para a lista mais actualizada leia o artigo http://aka.ms/ssoproviders.

## <a name="define-synchronization-strategy"></a>Definir estratégia de sincronização
Nesta tarefa, irá definir as ferramentas que serão utilizadas para sincronizar da organização no local dados para a nuvem e o que deve utilizar a topologia.  Uma vez que a maioria das organizações utilizam o Active Directory, informações sobre como utilizar a ligação do Azure AD para abordar as questões acima são fornecidas em alguns detalhes.  Para os ambientes que não têm do Active Directory, existe informações sobre como utilizar FIM 2010 R2 ou MIM 2016 para o ajudar a planear esta estratégia.  No entanto, versões futuras da ligação do Azure AD irão suportar diretórios LDAP, por isso, dependendo na sua linha cronológica, esta informação poderá ser possível dar assistência.

###<a name="synchronization-tools"></a>Ferramentas de sincronização
Ao longo dos anos, várias ferramentas de sincronização tem existia e utilizado para vários cenários.  Atualmente, ligação do Azure AD é ir para a ferramenta de seleção para todos os cenários suportados.  AAD Sync e DirSync também ainda estão à volta e poderão ainda estar presentes no seu ambiente agora. 

>[AZURE.NOTE]
Para informações mais recentes sobre as capacidades suportadas de cada ferramenta, leia o artigo de [comparação de ferramentas de integração de diretório](active-directory-hybrid-identity-design-considerations-tools-comparison.md) .  

### <a name="supported-topologies"></a>Topologias suportadas
Quando definir uma estratégia de sincronização, deve ser determinada a topologia de que é utilizada. Consoante as informações que foram determinadas no passo 2 pode determinar qual topologia é o inicial maiúscula que pretende utilizar. A única floresta única topologia do Azure AD está mais comuns e é constituído por uma única floresta do Active Directory e uma única instância do Azure AD.  Isto vai ser utilizados na maioria dos cenários e é a topologia esperada ao utilizar a instalação do Azure AD ligar Express conforme apresentado na figura seguinte.
 
![](./media/hybrid-id-design-considerations/single-forest.png)Cenário de único floresta é muito frequente grandes e até mesmo pequenas organizações tenha várias florestas, conforme apresentado na figura 5.

>[AZURE.NOTE]
Para obter mais informações sobre os diferentes no local e topologias Azure AD com ligação do Azure AD sync leia o artigo [topologias para ligação do Azure AD](active-directory-aadconnect-topologies.md).


![](./media/hybrid-id-design-considerations/multi-forest.png) 

Cenário de floresta múltipla

Se isto as maiúsculas/minúsculas, em seguida, a topologia de multi-forest-single Azure AD deve ser considerada se os itens seguintes forem verdadeiros:

- Os utilizadores têm apenas 1 identidade em todas as florestas – secção utilizadores identificação exclusivo abaixo descreve isto mais detalhadamente.
- O utilizador autentica à floresta na qual a identidade está localizada
- Origem âncora (id imutáveis) e UPN seja reencaminhado a partir desta floresta
- Todos os florestas são acessíveis a ligação do Azure AD – Isto significa que não necessita de ser domínio associadas e pode ser colocado uma DMZ se Isto facilita isto.
- Os utilizadores têm apenas uma caixa de correio
- Floresta que aloja a caixa de correio de um utilizador tem a melhor qualidade de dados para os atributos visíveis na lista de endereços Global Exchange (GAL)
- Se existir sem caixa de correio no utilizador, pode ser utilizada qualquer floresta, em seguida, para contribuir estes valores
- Se tiver uma caixa de correio ligada, em seguida, também existe outra conta numa floresta diferente utilizada para iniciar sessão.

>[AZURE.NOTE]
Objetos que existirem em ambas no local e na nuvem estão "ligados" através de um identificador exclusivo. No contexto da sincronização de diretórios, este identificador exclusivo é referido como o SourceAnchor. No contexto de Single Sign-On, isto é referido como o ImmutableId. [Os conceitos de estrutura para a ligação do Azure AD](active-directory-aadconnect-design-concepts.md#sourceanchor) para obter mais considerações relativas à utilização do SourceAnchor.

Se o que precede não é true e tiver mais de uma conta active ou mais do que uma caixa de correio, o ligação do Azure AD escolha uma e ignorar o outro.  Se tiver ligado caixas de correio, mas nenhuma outra conta, não serão exportadas nestas contas Azure AD e esse utilizador não vai ser um membro de nenhum dos grupos.  Este é diferente do como foi no passado com DirSync e é intencional para melhor suporte estes cenários floresta múltipla. Um cenário de floresta múltipla é apresentado na figura abaixo.
 
![](./media/hybrid-id-design-considerations/multiforest-multipleAzureAD.png) 
 
**Floresta múltipla cenário com vários Azure AD**

É recomendado ter apenas um único directório no Azure AD para uma organização, mas é suportada-uma relação de 1:1 é mantida entre um servidor de sincronização de ligação do Azure AD e num diretório do Azure AD.  Para cada instância do Azure AD, terá uma instalação de ligação do Azure AD.  Além disso, Azure AD, por predefinição está isolada e os utilizadores numa instância do Azure AD não poderão ver utilizadores noutra instância.

É possível e suportados para ligar uma instância no local do Active Directory para vários directórios Azure AD conforme apresentado na figura abaixo:

![](./media/hybrid-id-design-considerations/single-forest-flitering.png) 
 

**Cenário de filtragem único floresta**

Para fazer o seguinte procedimento tem de ser verdadeiro:

- Os servidores de sincronização do Azure AD Connect tem de ser configurados para filtrar para que cada tenham um conjunto de objetos mutuamente exclusivos.  Isto concluído, por exemplo, por cada servidor para um determinado domínio ou or controlo do âmbito.
- Só pode ser registado um domínio DNS numa única diretório do Azure AD, para que o UPNs os utilizadores no no local AD tem de utilizar espaços de nomes em separado
- Os utilizadores numa instância do Azure AD apenas poderão ver utilizadores a partir da respetiva instância de.  Não conseguirão ver os utilizadores na outras instâncias
- Apenas um dos directórios Azure AD pode ativar a implementação híbrida do Exchange com no local AD
- Exclusividade comum também se aplica para trás de escrita.  Isto faz com algumas funcionalidades de escrita anterior não suportadas com este topologia de uma vez que estes partem do pressuposto de configuração de uma única no local.  Isto inclui:
 - Agrupar anterior escrita com configuração predefinida
 - Dispositivo escrita-anterior


Tenha em atenção que a seguinte não é suportada e não deve ser escolhida como uma implementação:

- Não é suportada para ter várias servidores de sincronização de ligação do Azure AD ligar-se para o mesmo directório do Azure AD mesmo se estão configuradas para sincronizar mutuamente conjunto de objeto
- Os não é suportado para sincronizar o mesmo utilizador a vários directórios Azure AD. 
- Também-não é suportado para efetuar uma alteração para que os utilizadores num Azure AD para que surja como contactos no diretório do Azure AD outra de configuração. 
- Também é suportada para modificar a ligação do Azure AD sync para ligar a vários directórios Azure AD.
- Diretórios do Azure AD são por predefinição, isolada. É suportada para alterar a configuração de ligação do Azure AD sync para ler dados a partir do diretório do Azure AD outra uma tentativa para criar uma GAL comuns e unificada entre os directórios. Também não suportada para exportar utilizadores como contactos para outro local AD utilizando a ligação do Azure AD sync.


>[AZURE.NOTE]
Se a sua organização restringe computadores na sua rede de estabelecer ligação à Internet, este artigo apresenta os pontos finais (FQDNs, IPv4 e intervalos de endereços do IPv6) que deve incluir no seu saída permite a listas e zona de Sites do Internet Explorer fidedignos do cliente de computadores garantir que os computadores com êxito podem utilizar o Office 365. Para obter mais informações, leia [URLs do Office 365 e intervalos de endereços IP](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2?ui=en-US&rs=en-US&ad=US).

## <a name="define-multi-factor-authentication-strategy"></a>Definir estratégia de autenticação multifator
Nesta tarefa definirá a estratégia de autenticação multifator para utilizar.  Autenticação Multifator Azure é recebida duas versão diferente.  Uma é uma conta baseada na nuvem e o outro é no local com base utilizando o servidor de MFA Azure.  Com base numa avaliação indicados acima, pode determinar qual a solução é o correto para a sua estratégia de.  Utilize a tabela abaixo para determinar qual a opção estrutura melhor cumprir requisitos de segurança da sua empresa:

Opções de estruturação de multifator:

| Elementos para proteger                                               | MFA na nuvem | MFA no local |
|---------------------------------------------------------------|------------------|----------------|
| Aplicações do Microsoft                                                | Sim              | Sim            |
| Aplicações de SaaS na Galeria de aplicação                                  | Sim              | Sim            |
| Aplicações do IIS publicadas através de Proxy de aplicação do Azure AD         | Sim              | Sim            |
| Aplicações do IIS não publicadas através do Proxy de aplicação do Azure AD | nenhum               | Sim            |
| Acesso remoto como VPN, RDG                                     | nenhum               | Sim            |

Apesar de podem ter resolvidas numa solução para a sua estratégia de, ainda precisar de utilizar a avaliação dos acima no onde se encontram os seus utilizadores.  Isto pode causar a solução alterar.  Utilize a tabela abaixo para ajudá-lo para determinar isto:

| Localização do utilizador                                                       | Opção de apresentação preferido                 |
|---------------------------------------------------------------------|-----------------------------------------|
| Azure Active Directory                                              | Multi-FactorAuthentication na nuvem |
| Azure AD e no local com a Federação com o AD FS de AD             | Ambos                                    |
| Azure AD e no local AD utilizando Azure AD Connect sem sincronização de palavra-passe | Ambos                                    |
| Azure AD e no local com a ligação do Azure AD com a sincronização de palavra-passe  | Ambos                                    |
| No local AD                                                      | Servidor de autenticação Multifator      |

>[AZURE.NOTE]
Deverá também Certifique-se de que a opção de estrutura de autenticação multifator que selecionou suporta as funcionalidades que são necessárias para a sua estrutura.  Para obter mais informações, leia [Escolher a solução de segurança multifator por si](../multi-factor-authentication-get-started.md#what-am-i-trying-to-secure).

## <a name="multi-factor-auth-provider"></a>Fornecedor de autenticação Multifator
Autenticação multifator por predefinição está disponível para administradores globais que tenham um inquilino do Azure Active Directory. No entanto, se pretender expandir a autenticação multifator para todos os utilizadores e/ou pretende para os administradores globais poder tomar partido funcionalidades como o portal de gestão, personalizadas saudações e relatórios, em seguida, tem de adquirir e configurar o fornecedor de autenticação Multifator.

>[AZURE.NOTE]
Deverá também Certifique-se de que a opção de estrutura de autenticação multifator que selecionou suporta as funcionalidades que são necessárias para a sua estrutura. 

##<a name="next-steps"></a>Próximos passos
[Determinar requisitos de proteção de dados](active-directory-hybrid-identity-design-considerations-dataprotection-requirements.md)

## <a name="see-also"></a>Consulte também
[Descrição geral de considerações de estrutura](active-directory-hybrid-identity-design-considerations-overview.md)
