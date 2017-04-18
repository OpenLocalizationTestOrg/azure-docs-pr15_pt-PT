<properties 
    pageTitle="Segurança práticas recomendadas para utilizar o Azure MFA"
    description="Este artigo fornece práticas recomendadas para utilizar o Azure MFA com contas do Azure"
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="curtland"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/04/2016"
    ms.author="kgremban"/>

# <a name="security-best-practices-for-using-azure-multi-factor-authentication-with-azure-ad-accounts"></a>Segurança práticas recomendadas para utilizar o Azure a autenticação Multifator com contas do Azure AD

Quando chegar para melhorar o processo de autenticação, autenticação multifator é a escolha preferida à maioria das organizações. Autenticação Multifator Azure (MFA) permite às empresas cumprir os respetivos requisitos de segurança e conformidade ao fornecer uma experiência de início de sessão simple para os seus utilizadores. Este artigo aborda algumas das melhores práticas que deverá tomar em consideração ao planear a aprovação do Azure MFA.

## <a name="best-practices-for-azure-multi-factor-authentication-in-the-cloud"></a>Práticas recomendadas para autenticação Multifator de Azure na nuvem
Para fornecer todos os utilizadores com autenticação multifator e tirar vantagens das funcionalidades adicional que oferece a autenticação Multifator de Azure, terá de ativar a autenticação Multifator do Azure em todos os seus utilizadores.  Isto é feito através de um dos seguintes procedimentos:

- Azure AD Premium ou para o conjunto de mobilidade da empresa
- Fornecedor de autenticação Multifator

### <a name="azure-ad-premiumenterprise-mobility-suite"></a>Conjunto de aplicações do Azure AD Premium/Enterprise mobilidade

![EMS](./media/multi-factor-authentication-security-best-practices/ems.png)

O primeiro passo recomendado para adoptar Azure MFA na nuvem através do Azure AD Premium ou para o conjunto de mobilidade da empresa é atribuir licenças aos seus utilizadores.  Autenticação Multifator Azure faz parte destes conjuntos de aplicações e como tal sua organização não necessita de nada adicionais para alargar a funcionalidade de autenticação multifator a todos os utilizadores.

Quando configurar a autenticação Multifator ter em consideração seguintes:

- Não necessita de criar um fornecedor de autenticação Multifator.  Azure AD Premium e o conjunto de mobilidade da empresa é fornecido com autenticação Multifator de Azure.  Se criar um fornecedor de Auth foi possível obter duplo faturada.
- Ligação do Azure AD é apenas um requisito se estiver a sincronizar o seu ambiente do Active Directory no local com um directory Azure AD. Se utilizar apenas um directório do Azure AD que não está sincronizado com uma instância no local do Active Directory, não terá de ligação do Azure AD.


### <a name="multi-factor-auth-provider"></a>Fornecedor de autenticação Multifator

![Fornecedor de autenticação Multifator](./media/multi-factor-authentication-security-best-practices/authprovider.png)

Se não tiver Azure AD Premium ou para o conjunto de mobilidade da empresa, o primeiro passo recomendado para adoptar Azure MFA na nuvem é criar um fornecedor de Auth MFA. Apesar de MFA por predefinição está disponível para administradores globais que tenham o Azure Active Directory, quando estiver a implementar o MFA para a sua organização tem alargar a funcionalidade de autenticação multifator para todos os utilizadores e fazer que precisa de um fornecedor de autenticação Multifator.
Quando seleciona o fornecedor de Auth, terá de selecionar um diretório e considere o seguinte:

- Não necessita de um diretório do Azure AD para criar um fornecedor de autenticação Multifator.
- É necessário associar o fornecedor de autenticação Multifator num diretório do Azure AD Se pretender expandir a autenticação multifator para todos os utilizadores e/ou pretende que os administradores globais possam tirar partido das funcionalidades, como o portal de gestão, saudações personalizadas e relatórios.
- DirSync ou AAD Sync são apenas um requisito se estiver a sincronizar o seu ambiente do Active Directory no local com um directory Azure AD. Se utilizar apenas um directório do Azure AD que não está sincronizado com uma instância no local do Active Directory, não terá DirSync ou AAD Sync.
- Se tiver Azure AD Premium ou para o conjunto de mobilidade da empresa, não terá criar um fornecedor de autenticação Multifator. Só precisa de atribuir uma licença a um utilizador e, em seguida, pode começar já a ativar MFA para os utilizadores.
- Certifique-se escolher o modelo de utilização direita para a sua empresa (por auth ou por utilizador ativado), depois de selecionar o modelo de utilização não é possível alterar.

### <a name="user-account"></a>Conta de utilizador
Quando ativar MFA para os seus utilizadores, contas de utilizador podem ser de uma das três Estados core: desativado, ativado ou impostas.
Utilize as diretrizes abaixo para se certificar de que estiver a utilizar a opção mais adequada para a sua implementação:

- Quando o estado do utilizador está definido para desativado, esse utilizador não está a utilizar a autenticação multifator. Este é o estado predefinido.
- Quando o estado do utilizador está definido ativado, significa que o utilizador está ativado, mas não concluiu o processo de registo. Será pedido para concluir o processo na próxima iniciar sessão. Esta definição não afeta as aplicações de browser não. Todas as aplicações irão continuar a trabalhar até que o processo de registo seja concluído.
- Quando o estado do utilizador está definido como imposto, significa que o utilizador poderá ou poderá não ter sido concluída registo. Se terem concluído o processo de registo, em seguida, estão a utilizar autenticação multifator. Caso contrário, será pedido ao utilizador para concluir o processo de registo na próxima iniciar sessão. Esta definição afeta aplicações não no browser. Estas aplicações não irão funcionar até que as palavras-passe de aplicação são criadas e utilizadas.
- Utilize o modelo de notificação de utilizador disponíveis no artigo [Introdução ao Azure a autenticação Multifator na nuvem](multi-factor-authentication-get-started-cloud.md) para enviar um e-mail aos seus utilizadores relativamente ao adoção MFA.

### <a name="supportability"></a>Compatibilidade

Uma vez que a maioria dos utilizadores estão habituados para utilizar apenas as palavras-passe para autenticar, é importante que a sua empresa trazer detecção para todos os utilizadores sobre este processo. Este detecção pode reduzir a probabilidade dos utilizadores ligarem o suporte técnico para secundárias problemas relacionados com MFA.
No entanto, existem alguns cenários de onde é necessário desativar temporariamente MFA. Utilize as diretrizes abaixo para compreender como lidar com esses cenários:

- Certifique-se de que formação que a equipa de suporte técnico a alça de cenários onde a aplicação móvel ou o telefone não está a receber uma notificação ou chamada telefónica e por este motivo, que o utilizador não consegue iniciar sessão. Estes podem ativar a opção de ignorar únicos permitir que um utilizador autenticar uma única vez ignorando"" autenticação multifator. O fluxo direto é temporário e expira após o número de segundos especificado.
- Se for necessário, pode tirar partido a capacidade de IPs de confiança no Azure MFA. Esta funcionalidade permite aos administradores de um inquilino gerido ou federado a capacidade de ignorar a autenticação multifator para utilizadores que está a iniciar sessão a partir de intranet local da empresa. As funcionalidades estão disponíveis para inquilinos do Azure AD que tenham licenças Azure AD Premium, o conjunto de aplicações do Enterprise mobilidade ou Azure a autenticação Multifator.


## <a name="best-practices-for-azure-multi-factor-authentication-on-premises"></a>Práticas recomendadas para autenticação Multifator Azure no local
Se decidiu sua empresa tirar partido as suas próprias infraestrutura para ativar MFA, será necessário implementar um servidor de autenticação Multifator Azure no local. Os componentes de servidor de MFA são apresentados no diagrama abaixo:

![Fornecedor de autenticação Multifator](./media/multi-factor-authentication-security-best-practices/server.png)
*não instalado por predefinição ** instalado mas não ativado por predefinição


Servidor de autenticação Multifator Azure podem ser utilizado para segura na nuvem recursos e no local que são acedidos pela contas do Azure AD.  No entanto Isto só pode feito usando Federação.  Isto é, tem de ter o AD FS e têm-Federado com o seu inquilino do Azure AD.
Quando configurar o servidor de autenticação Multifator ter em consideração seguintes:

- Se estiver proteger recursos de Azure AD utilizar serviços de Federação do Active Directory, em seguida, fator de 1. º de autenticação é executado no local utilizar os AD FS e fator de 2. é executado no local ao honrar a afirmação.
- Não é um requisito de que o servidor de autenticação Multifator Azure ser instalado no seu servidor de Federação do AD FS, no entanto, a placa de autenticação Multifator do AD FS tem de estar instalada num Windows Server 2012 R2 a executar o AD FS. Pode instalar o servidor num computador diferente, desde que é uma versão suportada e instalar o adaptador de AD FS em separado no seu servidor de Federação do AD FS. Consulte o procedimento abaixo para obter instruções sobre como instalar a placa separadamente.
- O Assistente de instalação de autenticação Multifator AD FS adaptador cria um grupo de segurança denominado PhoneFactor Admins no Active Directory e, em seguida, adiciona a conta de serviço do AD FS do seu serviço de Federação a este grupo. É recomendado que verificar no controlador de domínio que o grupo de administradores PhoneFactor facto é criado e o AD FS conta de serviço é um membro deste grupo. Se necessário, adicione a conta de serviço do AD FS manualmente para o grupo de administradores de PhoneFactor no seu controlador de domínio.

### <a name="user-portal"></a>Portal de utilizador
Este portal é executado num servidor de informação Internet (IIS) web site, que permite capacidades de gestão personalizada e fornece um conjunto completo de funcionalidades de administração do utilizador. Utilize as diretrizes abaixo para configurar este componente:

- É necessário IIS 6 ou superior
- ASP.NET v2.0.507207 tem de estar instalado e registado
- Este servidor pode ser implementada numa rede de perímetro.



### <a name="app-passwords"></a>Palavras-passe de aplicação
Se a sua organização esteja federada a utilizar o SSO com Azure AD e irão estar a utilizar o Azure MFA, em seguida, tenha em atenção o seguinte procedimento quando utiliza palavras-passe de aplicação (não se esqueça de que este apenas se aplica a federada (SSO) é utilizado):

- A palavra-passe de aplicação é verificada por Azure AD e, consequentemente, ignora Federação. Federação só é utilizada quando configurar a palavra-passe de aplicação.
- Para federada (SSO) serão armazenadas no id organizacional palavras-passe de utilizadores. Se o utilizador sai da empresa, essas informações tem de fluxo para id organizacional utilizando DirSync em tempo real. Conta desativar/eliminação poderá demorar até 3 horas para sincronizar, atrasar desativar/eliminação da palavra-passe de aplicação no Azure AD.
- Definições de controlo de acesso de cliente no local não são respeitadas por palavra-passe de aplicação
- No local a autenticação registo / capacidade de auditoria está disponível para a palavra-passe de aplicação
- Mais educação do utilizador final é necessária para o cliente do Microsoft Lync 2013.
- Determinadas estruturas de arquitecturais avançadas poderão necessitar de utilizar uma combinação de organizacional nome de utilizador e palavras-passe e palavras-passe de aplicação ao utilizar a autenticação multifator com os clientes, dependendo de onde autenticar. Para clientes que autenticar uma infraestrutura no local, utilizaria uma organizacional nome de utilizador e palavra-passe. Para clientes que autenticar Azure AD, teria de utilizar a palavra-passe de aplicação.
- Por predefinição, os utilizadores não é possível criar palavras-passe de aplicação, se a sua empresa necessita que ou se precisar de permitir que os utilizadores criar a palavra-passe de aplicação em alguns cenários, certifique-se de que a opção permitir que os utilizadores para criar palavras-passe de aplicação para iniciar sessão no aplicações baseadas no browser que não está selecionada.

## <a name="additional-considerations"></a>Considerações adicionais
Utilize a lista abaixo para compreender algumas considerações adicionais e as melhores práticas para cada componente que será implementada no local:

Método|Descrição
:------------- | :------------- |
[Serviço de Federação do Active Directory](multi-factor-authentication-get-started-adfs.md)|Informações sobre como configurar a autenticação Multifator de Azure com o AD FS.
[RADIUS Authenticaton](multi-factor-authentication-get-started-server-radius.md)|  Informações sobre como configurar e configurar o servidor de MFA Azure com raio.
[Autenticação do IIS](multi-factor-authentication-get-started-server-iis.md)|Informações sobre a instalação e configuração do servidor de MFA do Azure com o IIS.
[Authenticaton do Windows](multi-factor-authentication-get-started-server-windows.md)|  Informações sobre a instalação e configuração do servidor de MFA do Azure com autenticação do Windows.
[Autenticação LDAP](multi-factor-authentication-get-started-server-ldap.md)|Informações sobre como configurar e configurar o servidor de MFA Azure com autenticação LDAP.
[Remoto Gateway de ambiente de trabalho e servidor de autenticação Multifator Azure utilizar RADIUS](multi-factor-authentication-get-started-server-rdg.md)|  Informações sobre o programa de configuração e configurar o servidor de MFA Azure com Gateway de ambiente de trabalho remoto utilizar RADIUS.
[Sincronizar com o Active Directory do Windows Server](multi-factor-authentication-get-started-server-dirint.md)|Informações sobre a instalação e configuração de sincronização entre o Active Directory e o servidor de MFA Azure.
[Implementar o serviço do Azure autenticação Multifator servidor Web da aplicação móvel](multi-factor-authentication-get-started-server-webservice.md)|Informações sobre como configurar e configurar o serviço do Azure MFA servidor web.
[Configuração de VPN avançadas com a autenticação Multifator Azure](multi-factor-authentication-advanced-vpn-configurations.md)|Informações sobre como configurar eletrodomésticos Cisco ASA, Citrix Netscaler e zimbro/Pulse seguro VPN utilizando LDAP ou RADIUS.


## <a name="additional-resources"></a>Recursos adicionais
Enquanto este artigo realça alguns dos procedimentos recomendados para Azure MFA, existem outros recursos que também pode utilizar ao planear a sua implementação MFA. Na lista abaixo tem alguns artigos chaves que podem ajudá-lo durante este processo:

- [Relatórios no Azure autenticação Multifator](multi-factor-authentication-manage-reports.md)
- [Experiência de configuração para autenticação Multifator de Azure](multi-factor-authentication-end-user-first-time.md)
- [Perguntas mais frequentes do Azure autenticação Multifator](multi-factor-authentication-faq.md)
