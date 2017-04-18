<properties
    pageTitle="Azure Active Directory híbrido identidade considerações de estrutura - determinar os requisitos de sincronização de diretórios | Microsoft Azure"
    description="Identificar quais os requisitos são necessários para sincronizar todos os utilizadores entre = no local e na nuvem para a empresa."
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

# <a name="determine-directory-synchronization-requirements"></a>Determinar os requisitos de sincronização de diretório
A sincronização é tudo sobre fornecer aos utilizadores uma identidade na nuvem com base na sua identidade no local. Se ou não irão utilizar conta sincronizada para ou a autenticação federados, os utilizadores ainda precisa de ter uma identidade na nuvem.  Esta identidade terá de ser mantido e actualizado periodicamente.  As atualizações podem tomar várias formas, alterações de título a alterações de palavra-passe.  

Começar por avaliar organizações no local identidade solução requisitos e do utilizador. Esta avaliação é importante para definir os requisitos técnicos para como identidades de utilizador serão criadas e mantidas na nuvem.  Para a maioria das organizações, do Active Directory é no local e serão diretório no local que os utilizadores irão por sincronizados a partir de, no entanto em alguns casos isto não será as maiúsculas/minúsculas.  

Certifique-se atender as seguintes questões:


- Tem uma floresta AD, várias ou nenhum?
 - Quantos directórios do Azure AD irá pode estar a sincronizar para?
 
    1. Está a utilizar a filtragem?
    2. Tem vários servidores de ligação do Azure AD planeados?
  
- Possui atualmente uma sincronização ferramenta no local?
  - Se Sim, é que os utilizadores se os utilizadores têm uma diretório/integração virtual de identidades?
- Tem quaisquer outros diretório no local que pretende sincronizar (por exemplo, directório LDAP, HR base de dados, etc)?
  - Vai ser efetuar qualquer GALSync?
  - O que é o estado atual do UPNs na sua organização? 
  - Tem um diretório diferente que os utilizadores autenticados contra?
  - De que a sua empresa utiliza Microsoft Exchange?
    - Estes planear de ter uma versão híbrida do exchange?

Agora que tem uma ideia sobre os requisitos de sincronização, tem de determinar qual a ferramenta está correto de cumprir estes requisitos.  A Microsoft fornece várias ferramentas para realizar integração de diretórios e sincronização.  Consulte a [tabela de comparação de ferramentas de integração de diretório de identidade híbrido](active-directory-hybrid-identity-design-considerations-tools-comparison.md) para obter mais informações. 
   
Agora que tem os requisitos de sincronização e a ferramenta que irá realizar esta tarefa para a sua empresa, tem de avaliar as aplicações que utilizam estes serviços de diretório. Esta avaliação é importante para definir os requisitos técnicos para integrar estas aplicações na nuvem. Certifique-se atender as seguintes questões:

- Estas aplicações de ser movidas para a nuvem e utilizar o directório?
- Existem atributos especiais que necessitam de ser sincronizados com a nuvem para que estas aplicações podem utilizá-los com êxito?
- Estas aplicações terá de ser escritos novamente para tirar partido da nuvem auth?
- Estas aplicações irão continuar a live no local, enquanto utilizadores aceder aos mesmos utilizando a identidade da nuvem?

Também tem de determinar a segurança requisitos e restrições da sincronização de diretórios. Esta avaliação é importante para obter uma lista dos requisitos de serão necessárias para criar e manter identidades do utilizador na nuvem. Certifique-se atender as seguintes questões:

- Onde o servidor da sincronização será localizado?
- Vai estar domínio aderido?
- O servidor de estar localizado numa rede restrita atrás uma firewall, tal como uma DMZ?
  - Será que poderá abrir as portas da firewall necessários para suportar a sincronização?
- Tem um plano de recuperação de falhas para o servidor de sincronização?
- Tem uma conta com as permissões corretas para todos os florestas que pretende sincronizar com?
 - Se a sua empresa não souber a resposta para esta pergunta, reveja a secção "Permissões para a sincronização de palavra-passe" no artigo [instalar o serviço de sincronização do Active Directory Azure](https://msdn.microsoft.com/library/azure/dn757602.aspx#BKMK_CreateAnADAccountForTheSyncService) e determinar se já tiver uma conta com estas permissões ou se for necessário criar uma.
- Se tiver sincronização múltipla floresta é o servidor de sincronização poderão aceder a cada floresta?
 
>[AZURE.NOTE]
Certifique-se tomar notas de cada resposta e compreender a lógica por detrás a resposta. [Requisitos de resposta incidente determinar](active-directory-hybrid-identity-design-considerations-incident-response-requirements.md) irá abordar as opções disponíveis. Por ter respondido estas perguntas que irá selecionar qual a opção que melhor se adequa a sua empresa necessita.

## <a name="next-steps"></a>Próximos passos
[Determinar requisitos de autenticação multifator](active-directory-hybrid-identity-design-considerations-multifactor-auth-requirements.md)

## <a name="see-also"></a>Consulte também
[Descrição geral de considerações de estrutura](active-directory-hybrid-identity-design-considerations-overview.md)
