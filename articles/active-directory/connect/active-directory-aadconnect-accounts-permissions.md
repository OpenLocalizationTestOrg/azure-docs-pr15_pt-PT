<properties
   pageTitle="Ligação do Azure AD: Permissões e contas | Microsoft Azure"
   description="Este tópico descreve as contas utilizado e criado e as permissões necessárias."
   services="active-directory"
   documentationCenter=""
   authors="billmath"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"  
   ms.workload="identity"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="10/04/2016"
   ms.author="billmath"/>


# <a name="azure-ad-connect-accounts-and-permissions"></a>Ligação do Azure AD: Permissões e contas
O Assistente de instalação de ligação do Azure AD oferece duas caminhos diferentes:

- Nas definições Express, o assistente requer privilégios de mais para que-pode configurar a configuração do facilmente, sem que seja necessário criar utilizadores ou configurar permissões separadamente.

- Nas definições personalizadas, o assistente oferece-lhe mais opções e opções, mas existem algumas situações em que precisar para se certificar de que tem as permissões corretas si próprio.

## <a name="related-documentation"></a>Documentação relacionada
Se não lido a documentação sobre [integrar a suas identidades no local com o Azure Active Directory](../active-directory-aadconnect.md), a tabela seguinte fornece ligações para tópicos relacionados.

Tópico |  
--------- | ---------
Instalar o utilizando as definições de Express | [Instalação rápida de ligação do Azure AD](active-directory-aadconnect-get-started-express.md)
Instalar com definições personalizadas | [Instalação personalizada de ligação do Azure AD](active-directory-aadconnect-get-started-custom.md)
Actualizar a partir do DirSync | [Atualizar a partir da ferramenta de sincronização do Azure AD (DirSync)](active-directory-aadconnect-dirsync-upgrade-get-started.md)


## <a name="express-settings-installation"></a>Express instalação de definições
Nas definições de Express, o Assistente de instalação pede credenciais de administrador de empresa do AD DS para que o Active Directory no local podem ser configurado com permissões necessárias para a ligação do Azure AD. Se estiver a atualizar a partir do DirSync, as credenciais de administradores de empresa do AD DS são utilizadas para repor a palavra-passe da conta utilizado pelo DirSync. Também precisa de credenciais de Administrador Global de AD Azure.

Página do Assistente  | Credenciais recolhidas | Permissões necessárias| Utilizado para
------------- | ------------- |------------- |-------------
N/D|Utilizador a executar o Assistente de instalação| Administrador do servidor local| <li>Cria a conta local que é utilizada como a [conta de serviço do motor de sincronização](#azure-ad-connect-sync-service-account).
Ligar ao Azure AD| Credenciais de diretório do Azure AD | Função de administrador global no Azure AD | <li>Ativar a sincronização no diretório Azure AD.</li>  <li>Criação da [conta do Azure AD](#azure-ad-service-account) que é utilizado para operações de sincronização em curso no Azure AD.</li>
Ligar ao AD DS | Credenciais do Active Directory no local | Membro do grupo Administradores Enterprise (EA) no Active Directory| <li>Cria uma [conta](#active-directory-account) no Active Directory e concede permissões para o mesmo. Este criado conta é utilizado para ler e escrever as informações de directório durante a sincronização.</li>

### <a name="enterprise-admin-credentials"></a>Credenciais de administrador de empresa
Estas credenciais são utilizadas apenas durante a instalação e são utilizados após a instalação estiver concluída. É administrador da empresa e não Domain Admin, para se certificar de que as permissões no Active Directory podem ser definidas em todos os domínios.

### <a name="global-admin-credentials"></a>Credenciais de administrador globais
Estas credenciais são utilizadas apenas durante a instalação e não são utilizados após a instalação estiver concluída. É utilizado para criar a [conta do Azure AD](#azure-ad-service-account) utilizada para sincronizadas alterações Azure AD. A conta também lhe permite sincronização como uma funcionalidade no Azure AD.

### <a name="permissions-for-the-created-ad-ds-account-for-express-settings"></a>Permissões para o AD DS criado de conta para definições express
A [conta](#active-directory-account) criada para leitura e escrita para o AD DS ter as seguintes permissões quando criado pelo express definições:

Permissão | Utilizado para
---- | ----
<li>Criar uma réplica alterações de diretório</li><li>Repetições as alterações de directório todos | Sincronização de palavra-passe
Leitura/escrita todas as propriedades de utilizador | Importar e o Exchange híbrido
Leitura/escrita todas as propriedades iNetOrgPerson | Importar e o Exchange híbrido
Grupo de todas as propriedades de leitura/escrita | Importar e o Exchange híbrido
Leitura/escrita que todas as propriedades de contacto | Importar e o Exchange híbrido
Repor palavra-passe | Preparação para ativar a palavra-passe de escrita não consolidada

## <a name="custom-settings-installation"></a>Instalação definições personalizadas
Ao utilizar definições personalizadas, a conta utilizada para ligar ao Active Directory tem de ser criada antes da instalação. As permissões que tem de conceder esta conta podem ser encontradas na [criar a conta de AD DS](#create-the-ad-ds-account).

Página do Assistente  | Credenciais recolhidas | Permissões necessárias| Utilizado para
------------- | ------------- |------------- |-------------
N/D | Utilizador a executar o Assistente de instalação|<li>Administrador do servidor local</li><li>Se utilizar o SQL Server completo, o utilizador tem de ser administrador do sistema (SA) no SQL</li>| Por predefinição, cria a conta local que é utilizada como a [conta de serviço do motor de sincronização](#azure-ad-connect-sync-service-account). A conta apenas é criada quando o administrador não especificar uma determinada conta.
Instale os serviços de sincronização, opção de conta de serviço | AD ou credenciais de conta de utilizador local | Utilizador, são concedidas permissões pelo Assistente de instalação | Se o administrador especifica uma conta, esta conta é utilizada como a conta de serviço para o serviço de sincronização.
Ligar ao Azure AD | Credenciais de diretório do Azure AD| Função de administrador global no Azure AD| <li>Ativar a sincronização no diretório Azure AD.</li>  <li>Criação da [conta do Azure AD](#azure-ad-service-account) que é utilizado para operações de sincronização em curso no Azure AD.</li>
Ligar a sua diretórios | Credenciais do Active Directory no local para cada floresta que está ligado à Azure AD | As permissões dependem quais as funcionalidades que ativa e pode ser encontradas na [conta de criar o AD DS](#create-the-ad-ds-account) |Esta conta é utilizada para ler e escrever as informações de directório durante a sincronização.
Servidores do AD FS | Para cada servidor na lista, o assistente recolhe credenciais quando forem suficientes para ligar as credenciais de início de sessão do utilizador a executar o Assistente | Administrador do domínio | Instalação e configuração da função de servidor do AD FS.
Servidores de proxy de aplicação Web |Para cada servidor na lista, o assistente recolhe credenciais quando forem suficientes para ligar as credenciais de início de sessão do utilizador a executar o Assistente | Administração local no computador de destino | Instalação e configuração WAP da função de servidor.
Credenciais de fidedignidade do proxy |Serviço de Federação confiar credenciais (as credenciais o proxy usa para se inscrever para um certificado de fidedignidade a partir do FS |Conta do domínio que seja um administrador do servidor do AD FS local | Inscrição inicial de certificado de fidedignidade FS WAP.
Página de conta de serviço FS AD, "Utilizar uma opção de conta de utilizador do domínio" | Credenciais de conta de utilizador do AD | Utilizador do domínio | A conta de utilizador do AD são fornecidos cujas credenciais é utilizada como a conta de início de sessão do serviço do AD FS.

### <a name="create-the-ad-ds-account"></a>Criar a conta de AD DS
Quando instalar ligação do Azure AD, a conta que especificar na página **ligar o seu directórios** têm de estar presente no Active Directory e tem necessários permissões concedidas. O Assistente de instalação não verificar que as permissões e quaisquer problemas com apenas forem encontrados durante a sincronização.

Quais as permissões que requerem depende as funcionalidades opcionais ativar. Se tiver múltiplos domínios, tem de conceder as permissões para todos os domínios na floresta. Se não ativar estas funcionalidades, as permissões de **Utilizador de domínio** predefinido forem suficientes.

Funcionalidade | Permissões
------ | ------
Sincronização de palavra-passe | <li>Criar uma réplica alterações de diretório</li>  <li>Repetições as alterações de directório todos
Implementação híbrida do Exchange | Permissões de escrita para os atributos documentados no [Exchange híbrido escrita não consolidada](../active-directory-aadconnectsync-attributes-synchronized.md#exchange-hybrid-writeback) para os utilizadores, grupos e contactos.
Repetição de escrita de palavra-passe | Permissões de escrita para os atributos-se documentados na [Introdução à gestão de palavra-passe](../active-directory-passwords-getting-started.md#step-4-set-up-the-appropriate-active-directory-permissions) para os utilizadores.
Dispositivo repetição de escrita | Permissões concedidas com um script PowerShell, conforme descrito no [dispositivo escrita não consolidada](../active-directory-aadconnect-feature-device-writeback.md).
Grupo repetição de escrita | Ler, criar, atualizar e eliminar objetos de grupo no OU onde os grupos de distribuição devem ser localizados.

## <a name="upgrade"></a>Atualização
Quando atualizar a partir de uma versão de ligação do Azure AD para uma nova versão, tem as seguintes permissões:

Principais | Permissões necessárias | Utilizado para
---- | ---- | ----
Utilizador a executar o Assistente de instalação | Administrador do servidor local | Atualize binários.
Utilizador a executar o Assistente de instalação | Membro da ADSyncAdmins | Efetue alterações aos outros configuração e regras de sincronização.
Utilizador a executar o Assistente de instalação | Se utilizar um completo do SQL server: DBO (ou semelhante) da base de dados de motor de sincronização | Efetue alterações de nível de base de dados, tais como atualizar tabelas com novas colunas.

## <a name="more-about-the-created-accounts"></a>Mais informações sobre contas de criado

### <a name="active-directory-account"></a>Conta do Active Directory
Se utiliza o express definições, em seguida, é criada uma conta no Active Directory é utilizada para a sincronização. A conta criada está localizada no domínio de raiz da floresta no contentor de utilizadores e tem o nome do mesmo com o prefixo **MSOL_**. A conta estiver criada com uma palavra-passe complexa longa que não expire. Se tiver uma política de palavra-passe no seu domínio, certifique tem a certeza longo e palavras-passe complexas seriam permitidas para esta conta.

![Conta do AD](./media/active-directory-aadconnect-accounts-permissions/adsyncserviceaccount.png)

### <a name="azure-ad-connect-sync-service-accounts"></a>Contas de serviço de sincronização do Azure AD Connect
Uma conta de serviço local é criada pelo Assistente de instalação (a menos que especifique a conta a utilizar definições personalizadas). A conta é o prefixo **AAD_** e são utilizadas para o serviço de sincronização real para executar como. Se instalar a ligação do Azure AD num controlador de domínio, a conta estiver criada no domínio. Se utilizar um servidor remoto com o SQL server ou se utilizar um proxy de que precisa de autenticação, a conta de serviço **AAD_** têm de estar localizada no domínio.

![Conta de serviço de sincronização](./media/active-directory-aadconnect-accounts-permissions/syncserviceaccount.png)

A conta estiver criada com uma palavra-passe complexa longa que não expire.

Esta conta é utilizada para armazenar as palavras-passe para as outras contas de forma segura. Estes outras contas palavras-passe é armazenados encriptados na base de dados. Chaves privadas para as chaves de encriptação estão protegidas com a encriptação de chave secreta dos serviços de criptografia através de API de proteção de dados do Windows (DPAPI). Não deve repor a palavra-passe da conta de serviço de uma vez que o Windows, em seguida, irá destroy as chaves de encriptação por motivos de segurança.

Se utiliza o SQL Server completo, em seguida, a conta do serviço é DBO da base de dados criada para o motor de sincronização. O serviço não irá funcionar como pretendido com quaisquer outras permissões. Também é criado um início de sessão SQL.

A conta também é concedida permissões a ficheiros, chaves de registo e outros objetos relacionados com o motor de sincronização.

### <a name="azure-ad-service-account"></a>Conta de serviço do Azure AD
É criada uma conta no Azure AD para utilizar o serviço de sincronização. Esta conta pode ser identificada pelo seu nome de apresentação.

![Conta do AD](./media/active-directory-aadconnect-accounts-permissions/aadsyncserviceaccount.png)

O nome do servidor que de conta é utilizada no pode ser identificado na segunda parte do nome de utilizador. Na imagem, o nome do servidor é FABRIKAMCON. Se tiver transição servidores, cada servidor tem a sua própria conta. Existe um limite de 10 contas de serviço de sincronização no Azure AD.

A conta do serviço é criada com uma palavra-passe complexa longa que não expire. É-lhe concedido um papel especial **Contas de sincronização de diretórios** que tem apenas as permissões para efetuar tarefas de sincronização de diretórios. Esta função incorporada especial não pode ser concedida fora do Assistente de ligação do Azure AD e o portal do Azure mostra esta conta com a função de **utilizador**.

![Conta do AD função](./media/active-directory-aadconnect-accounts-permissions/aadsyncserviceaccountrole.png)

## <a name="next-steps"></a>Próximos passos

Saiba mais sobre como [integrar a suas identidades no local com o Azure Active Directory](../active-directory-aadconnect.md).
