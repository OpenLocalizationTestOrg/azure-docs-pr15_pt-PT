<properties
   pageTitle="Descrição geral de segurança no arquivo de dados de Lake | Microsoft Azure"
   description="Compreender como Azure dados Lake arquivo é um arquivo de dados grande mais seguro"
   services="data-lake-store"
   documentationCenter=""
   authors="nitinme"
   manager="jhubbard"
   editor="cgronlun"/>

<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="08/02/2016"
   ms.author="nitinme"/>

# <a name="security-in-azure-data-lake-store"></a>Segurança no arquivo de Lake de dados do Azure

Muitas empresas estão a tirar partido de análise de dados grande de informações de negócio para o ajudar a tomar decisões inteligentes. Uma organização poderá ter um ambiente complexo e regulamentado, com um número de utilizadores diversificados crescente. É crucial para uma empresa para se certificar de que os dados de negócio crítico são armazenados mais segura, com o nível de acesso concedido a que os utilizadores individuais correto. Azure dados Lake loja destina-se para o ajudar a cumprir estes requisitos de segurança. Neste artigo, saiba mais sobre as capacidades de segurança Lake de arquivo de dados, incluindo:

* Autenticação
* Autorização
* Isolamento de rede
* Proteção de dados
* Auditoria

## <a name="authentication-and-identity-management"></a>Gestão de autenticação e de identidade

Autenticação é o processo pelo qual identidade de um utilizador é verificada quando o utilizador interage com o arquivo de dados de Lake ou com qualquer serviço que se liga aos dados Lake loja. Para gestão de identidades e autenticação, o arquivo de dados de Lake utiliza o [Azure Active Directory](../active-directory/active-directory-whatis.md), uma identidade abrangente e a solução de nuvem de gestão de acesso que simplifica a gestão de utilizadores e grupos.

Cada subscrição Azure pode ser associada com uma instância do Azure Active Directory. Apenas os utilizadores e identidades de serviço que são definidas no seu serviço de Azure Active Directory podem aceder à sua conta do arquivo de Lake de dados, utilizando o portal do Azure, ferramentas de linha de comandos, ou através de aplicações de cliente a sua organização constrói utilizando o Azure dados Lake loja SDK. Principais vantagens da utilização do Azure Active Directory como um mecanismo de controlo do access centralizada são:

* Gestão de ciclo de vida de identidade simplificadas. A identidade de um utilizador ou um serviço (uma identidade principal de serviço) pode ser criada rapidamente e revogada rapidamente ao simplesmente eliminar ou desativar a conta no diretório.

* Autenticação multifator. [Autenticação multifator](../multi-factor-authentication/multi-factor-authentication.md) fornece uma camada adicional de segurança para suplementos de início de sessão do utilizador e operações.

* Autenticação a partir de qualquer cliente através de um protocolo Abrir padrão, tais como OAuth ou OpenID.

* Federação com os serviços de diretório de empresa e fornecedores de identidade da nuvem.

## <a name="authorization-and-access-control"></a>Autorização e controlo de acesso

Depois do Azure Active Directory autentica um utilizador para que o utilizador pode aceder ao arquivo de Lake Azure dados, controlos de autorização permissões de acesso a dados Lake loja. Arquivo de dados de Lake separa autorização para actividades relacionadas com a conta e relacionada de dados do seguinte modo:

* [Controlo de acesso baseado em funções](../active-directory/role-based-access-control-what-is.md) (RBCA) fornecidas pelo Azure para gestão de contas
* POSIX ACL para aceder aos dados na loja


### <a name="rbac-for-account-management"></a>RBAC para gestão de contas

Funções básicas quatro estão definidas para o arquivo de dados de Lake por predefinição. As funções permitem operações diferentes de uma conta do arquivo de Lake dados através da Azure portal, os cmdlets do PowerShell e REST APIs. As funções proprietário e contribuinte podem executar uma variedade de funções de administração da conta. Pode atribuir a função de leitor aos utilizadores que apenas interagem com os dados.

![Funções RBAC] (./media/data-lake-store-security-overview/rbac-roles.png "Funções RBAC")

Note que apesar das funções são atribuídas para gestão de contas, algumas funções afectam acesso aos dados. Tem de utilizar ACL para controlar o acesso ao operações que pode executar um utilizador no sistema de ficheiros. A tabela seguinte mostra um resumo dos direitos de gestão e direitos de acesso de dados para as funções predefinidas.

| Funções                    | Direitos de gestão               | Direitos de acesso de dados | EXPLICAÇÃO |
| ------------------------ | ------------------------------- | ------------------ | ----------- |
| Função de atribuída         | Nenhum                            | Regida pelos ACL    | O utilizador não é possível utilizar o Azure portal ou os cmdlets do Azure PowerShell para procurar dados Lake loja. O utilizador pode utilizar apenas ferramentas de linha de comandos.
| Proprietário  | Todos os  | Todos os  | A função de proprietário é um super utilizador. Esta função pode gerir tudo e tem acesso completo aos dados.
| Leitor   | Só de leitura  | Regida pelos ACL    | A função de leitor pode ver tudo em relação à gestão de conta, tal como que o utilizador é atribuído a qual a função. A função de leitor não pode efetuar as alterações.   |
| Contribuinte              | Tudo exceto funções adicionar e remover | Regida pelos ACL    | A função Contribuinte pode gerir alguns aspetos de uma conta, tais como implementações e criar e gerir alertas. A função Contribuinte não pode adicionar ou remover funções.
| Administrador de acesso de utilizadores | Adicionar e remover funções            | Regida pelos ACL    | A função de administrador de acesso do utilizador pode gerir o acesso do utilizador para contas. |

Para obter instruções, consulte o artigo [atribuir utilizadores ou grupos de segurança para contas do arquivo de dados de Lake](data-lake-store-secure-data.md#assign-users-or-security-groups-to-azure-data-lake-store-accounts).

### <a name="using-acls-for-operations-on-file-systems"></a>Utilizar o ACL para operações nos sistemas de ficheiros

Arquivo de dados de Lake é um sistema de ficheiros hierárquica como distribuído ficheiro Hadoop sistema (HDFS) e suporta [POSIX ACL](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsPermissionsGuide.html#ACLs_Access_Control_Lists). Controlos de leitura (r), escrita (w) e executar (permissões para os recursos para a função de proprietário, para o grupo de proprietários e para outros utilizadores e grupos x). Na pré-visualização dos dados Lake arquivo público (a versão atual), ACL estão ativadas na pasta raiz, subpastas e em ficheiros individuais. As ACL aplicáveis para a pasta raiz também se aplicam a todas as pastas subordinadas e ficheiros.

Recomendamos que definir ACL para vários utilizadores ao utilizar [grupos de segurança](../active-directory/active-directory-accessmanagement-manage-groups.md). Adicionar utilizadores a um grupo de segurança e, em seguida, atribua-lhe as ACL para um ficheiro ou pasta para esse grupo de segurança. Isto é útil quando quiser fornecer acesso personalizado, porque o utilizador está limitado a adição de um máximo de nove entradas para personalizados acesso. Para mais informações sobre como proteger melhor dados armazenados no arquivo de dados de Lake ao utilizar grupos de segurança do Azure Active Directory, consulte o artigo [atribuir utilizadores ou grupo de segurança como ACL para o sistema de ficheiros de arquivo de Lake Azure dados](data-lake-store-secure-data.md#filepermissions).

![Lista padrão e acesso personalizado] (./media/data-lake-store-security-overview/adl.acl.2.png "Lista padrão e acesso personalizado")

## <a name="network-isolation"></a>Isolamento de rede

Arquivo de Lake de dados de utilização para ajudar a controlar o acesso ao arquivo de dados ao nível de rede. Pode estabelecer firewalls e definir um intervalo de endereços IP para os seus clientes fidedignos. Com um intervalo de endereços IP, só os clientes que têm um endereço IP dentro do intervalo definido podem ligar a dados Lake loja.

![Acesso IP e as definições da firewall] (./media/data-lake-store-security-overview/firewall-ip-access.png "Definições da firewall e o endereço IP")

## <a name="data-protection"></a>Proteção de dados

As organizações querem assegurar-se de que os respetivos dados de negócio crítico são seguros durante o ciclo de vida. Para os dados estão em trânsito arquivo de dados de Lake utiliza o protocolo de transporte segurança (TLS Layer) de norma da indústria para proteger os dados que move o cursor entre um cliente e o arquivo de dados de Lake.

Proteção de dados para os dados em repouso estarão disponível em versões futuras.

## <a name="auditing-and-diagnostic-logs"></a>Registos de diagnóstico e de auditoria

Pode utilizar os registos de auditoria ou de diagnóstico, dependendo se está a procurar registos para atividades relacionados com a gestão ou atividades relacionada de dados.

*  Atividades relacionados com a gestão utilizam APIs de Gestor de recursos do Azure e forem apresentadas no portal do Azure através de registos de auditoria.
*  Relacionada de dados de atividades utilizam WebHDFS REST APIs e forem apresentadas no portal do Azure através de registos de diagnóstico.

### <a name="auditing-logs"></a>Registos de auditoria

Para cumprir os regulamentos, uma organização poderá necessitar de registos de auditoria adequada se precisa de abordar o incidentes específicos. Arquivo de dados de Lake tem auditoria e monitorização incorporados e registará todas as atividades de gestão de conta.

Para registos de auditoria de gestão de conta, visualizar e selecione as colunas que quer iniciar sessão. Também pode exportar registos de auditoria para armazenamento do Windows Azure.

![Registos de auditoria] (./media/data-lake-store-security-overview/audit-logs.png "Registos de auditoria")

### <a name="diagnostic-logs"></a>Registos de diagnóstico

Pode configurar o acesso a dados de registos de auditoria no portal do Azure (nas definições diagnóstico) e crie uma conta de armazenamento de Blobs do Azure onde estão armazenados os registos.

![Registos de diagnóstico] (./media/data-lake-store-security-overview/diagnostic-logs.png "Registos de diagnóstico")

Depois de configurar definições de diagnóstico, pode ver os registos no separador de **Registos de diagnóstico** .

Para mais informações sobre como trabalhar com registos de diagnóstico com Azure dados Lake arquivo, consulte o artigo [registos de diagnóstico do Access para o arquivo de dados de Lake](data-lake-store-diagnostic-logs.md).

## <a name="summary"></a>Resumo

Os clientes empresariais de procura uma plataforma de nuvem de análise de dados que é seguro e fáceis de utilizar. Azure dados Lake loja foi concebida para ajudar o endereço que estes requisitos através de gestão de identidades e autenticação através de integração do Azure Active Directory, autorização com base em ACL, isolamento de rede, encriptação de dados em trânsito e nos coloque (chegar no futuro) e auditorias.

Se pretender ver as novas funcionalidades no arquivo de dados de Lake, envie-nos seus comentários no [Fórum do uservoice de arquivo de Lake de dados](https://feedback.azure.com/forums/327234-data-lake).

## <a name="see-also"></a>Consulte também

- [Descrição geral do Azure Lake de arquivo de dados](data-lake-store-overview.md)
- [Introdução ao arquivo de dados de Lake](data-lake-store-get-started-portal.md)
- [Proteger os dados no arquivo de dados de Lake](data-lake-store-secure-data.md)
