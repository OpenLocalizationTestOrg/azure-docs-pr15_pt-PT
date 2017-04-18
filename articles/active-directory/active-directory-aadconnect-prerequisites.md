<properties
   pageTitle="Ligação do Azure AD: Pré-requisitos e hardware | Microsoft Azure"
   description="Este tópico descreve pré-requisitos e os requisitos de hardware para ligação do Azure AD"
   services="active-directory"
   documentationCenter=""
   authors="andkjell"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.workload="identity"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="10/12/2016"
   ms.author="billmath"/>

# <a name="prerequisites-for-azure-ad-connect"></a>Ligar a pré-requisitos para Azure AD
Este tópico descreve pré-requisitos e os requisitos de hardware de ligação do Azure AD.

## <a name="before-you-install-azure-ad-connect"></a>Antes de instalar ligação do Azure AD
Antes de instalar ligação do Azure AD, existem algumas coisas que precisa.

### <a name="azure-ad"></a>Azure AD
- Uma subscrição do Azure ou uma [subscrição de avaliação do Azure](https://azure.microsoft.com/pricing/free-trial/). Este procedimento só é necessário para aceder ao portal do Azure e não para utilizar a ligação do Azure AD. Se estiver a utilizar o PowerShell ou do Office 365 não terá uma subscrição do Azure para utilizar a ligação do Azure AD. Se tiver uma licença do Office 365 Também pode utilizar o portal do Office 365. Com uma licença do Office 365 paga também pode obter no portal do Azure a partir do portal do Office 365.
    - Também pode utilizar a funcionalidade de pré-visualização do Azure AD no [portal do Azure](https://portal.azure.com). Este portal não requer uma licença do Azure.
- [Adicionar e verificar o domínio](active-directory-add-domain.md) planeia utilizar no Azure AD. Por exemplo, se planeia utilizar contoso.com para os seus utilizadores, em seguida, certifique-se tiver sido verificado este domínio e não está apenas a utilizar o domínio predefinido de contoso.onmicrosoft.com.
- Um inquilino do Azure AD permite por objetos predefinido 50k. Quando confirmar o seu domínio, o limite é aumentado para objetos de k 300. Se precisar de objetos ainda mais no Azure AD tem de abrir um incidente de suporte para que o limite aumentado ainda mais. Se precisar de mais de 500 objetos de k, em seguida, tem uma licença, como o Office 365, Azure AD básicas, Azure AD Premium ou do conjunto de aplicações do Enterprise mobilidade.

### <a name="prepare-your-on-premises-data"></a>Prepare os dados no local
- Rever [as funcionalidades de sincronização opcional que pode ativar no Azure AD](active-directory-aadconnectsyncservice-features.md) e avaliar quais as funcionalidades que deverá ativar.

### <a name="on-premises-servers-and-environment"></a>Servidores no local e ambiente
- O AD esquema versão e floresta nível funcional tem de ser Windows Server 2003 ou posterior. Os controladores de domínio podem executar qualquer versão, desde que os requisitos de nível do esquema e floresta são preenchidos.
- Se planeia utilizar a funcionalidade de **palavra-passe escrita não consolidada** os controladores de domínio tem de ser no Windows Server 2008 (com SP mais recente) ou posterior. Se encontram a sua CDs 2008 (pré-R2), em seguida, também terá de aplicar [correcção KB2386717](http://support.microsoft.com/kb/2386717).
- O controlador de domínio utilizado pelo Azure AD tem de ser editável. Não é suportada para utilizar um RODC (controlador de domínio só de leitura) e ligação do Azure AD não siga qualquer redirecionamentos escrita.
- Ligação do Azure AD não pode ser instalado em Small Business Server ou o Windows Server Essentials. O servidor tem de utilizar Windows Server padrão ou melhor.
- O servidor de ligação do Azure AD tem de ter uma interface completa gráfica instalada. Não é suportada para instalar o core de servidor.
- Ligação do Azure AD tem de estar instalado no Windows Server 2008 ou posterior. Este servidor pode ser um controlador de domínio ou um servidor membro se utilizando as definições express. Se utilizar definições personalizadas, o servidor também pode ser autónomo e não tem de ser associadas a um domínio.
- Se instalar a ligação do Azure AD no Windows Server 2008, certifique-se aplicar as correcções mais recentes do Windows Update. A instalação não é possível iniciar sessão com um servidor sem patches instalados.
- Se planeia utilizar a funcionalidade de **sincronização de palavra-passe**, o servidor de ligação do Azure AD tem de ser no Windows Server 2008 R2 SP1 ou posterior.
- O servidor de ligação do Azure AD deve ter [.NET Framework 4.5.1](#component-prerequisites) ou posterior e [Microsoft PowerShell 3.0](#component-prerequisites) ou posterior instalado.
- Se está a ser implementado serviços de Federação do Active Directory, os servidores de onde o AD FS ou Proxy de aplicação Web instalados tem de ser Windows Server 2012 R2 ou posterior. [Gestão remota do Windows](#windows-remote-management) deve ser ativado nestes servidores para instalação remota.
- Se está a ser implementado serviços de Federação do Active Directory, terá de [Certificados SSL](#ssl-certificate-requirements).
- Se estiver a ser implementado serviços de Federação do Active Directory, em seguida, tem de configurar [resolução do nome](#name-resolution-for-federation-servers).
- Ligação do Azure AD necessita de uma base de dados do SQL Server para armazenar os dados de identidade. Por predefinição é instalado um SQL Server 2012 Express LocalDB (uma versão simplificada do SQL Server Express) e a conta de serviço para o serviço é criada no computador local. SQL Server Express tem um limite de tamanho de 10GB permite-lhe gerir aproximadamente 100.000 objetos. Se precisar de gerir o volume mais alto de objetos de diretório, terá de aponte o Assistente de instalação para uma instalação diferente do SQL Server.
- Se utilizar um servidor separado de SQL, em seguida, estes requisitos aplicam-se:
    - Ligação do Azure AD suporta todas as versões do Microsoft SQL Server a partir do SQL Server 2008 (com SP4) para SQL Server 2014. Base de dados do Microsoft Azure SQL não é **suportada** como uma base de dados.
    - Tem de utilizar um agrupamento de SQL maiúsculas e minúsculas. Estes estão identificados com um \_CI_ no respetivo nome. É **não suportadas** para utilizar um agrupamento de maiúsculas/minúsculas, identificado por \_CS_ no respetivo nome.
    - Só pode ter um motor de sincronização por instância de base de dados. É **não suportadas** para partilhar a instância de base de dados com a sincronização FIM/MIM, DirSync ou Azure AD Sync.

### <a name="accounts"></a>Contas
- Uma conta de Administrador Global de AD Azure para do diretório do Azure AD que pretende integrar com. Isto deve ser uma **escola ou organização conta** e não pode ser uma **conta Microsoft**.
- Se utilizar definições express ou atualizar a partir do DirSync, em seguida, tem de ter uma conta de administrador da empresa para o seu Active Directory local.
- [Contas no Active Directory](./connect/active-directory-aadconnect-accounts-permissions.md) se utilizar o caminho de instalação definições personalizadas.

### <a name="azure-ad-connect-server-configuration"></a>Configuração do servidor do Azure AD Connect
- Se os seus administradores globais tiverem MFA ativado, o URL **https://secure.aadcdn.microsoftonline-p.com** tem de ser na lista de sites fidedignos. Lhe for pedido para adicionar à lista de sites fidedignos se não for adicionada antes que lhe seja pedido para um desafio MFA. Pode utilizar o Internet Explorer para adicioná-lo aos seus sites fidedignos.

### <a name="connectivity"></a>Conectividade
- O servidor de ligação do Azure AD necessita de resolução de DNS para intranet e internet. O servidor DNS tem de conseguir resolver os nomes tanto o Active Directory no local, bem como os pontos finais Azure AD.
- Se tiver firewalls na Intranet e tem de o abrir portas entre os servidores de ligação do Azure AD e os controladores de domínio, em seguida, consulte o artigo [Azure AD Connect portas](active-directory-aadconnect-ports.md) para obter mais informações.
- Se o limite da sua firewall ou proxy que podem ser acedidas URLs, em seguida, os URLs-se documentados na [intervalos de URLs do Office 365 e o endereço IP](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) têm de ser aberto.
    - Se estiver a utilizar o Microsoft Cloud na Alemanha ou na nuvem da Microsoft Azure administração pública, em seguida, consulte o artigo [Considerações de instâncias de serviço de sincronização de ligação do Azure AD](active-directory-aadconnect-instances.md) para URLs.
- Ligação do Azure AD está por predefinição utilizar TLS 1.0 para comunicar com o Azure AD. Pode alterar para TLS 1.2 ao seguir os passos na [Ativar 1.2 TLS para ligação do Azure AD](#enable-tls-12-for-azure-ad-connect).
- Se estiver a utilizar um proxy de saída para ligar à Internet, a seguinte definição no ficheiro **C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config** deve ser adicionada para o Assistente de instalação e a ligação do Azure AD sync conseguir ligar à Internet e Azure AD. Este texto têm de ser introduzido na parte inferior do ficheiro. Neste código &lt;PROXYADRESS&gt; representa o nome de anfitrião ou endereço IP do proxy real.

```
    <system.net>
        <defaultProxy>
            <proxy
            usesystemdefault="true"
            proxyaddress="http://<PROXYADDRESS>:<PROXYPORT>"
            bypassonlocal="true"
            />
        </defaultProxy>
    </system.net>
```

- Se necessitar de autenticação de servidor proxy, em seguida, a [conta de serviço](./connect/active-directory-aadconnect-accounts-permissions.md#azure-ad-connect-sync-service-accounts) têm de estar localizados no domínio de e tem de utilizar o caminho de instalação as definições personalizadas para especificar uma [conta de serviço personalizado](./connect/active-directory-aadconnect-get-started-custom.md#install-required-components). Também precisa de uma alteração diferente para Machine. config. Com esta alteração no Machine. config o Assistente de instalação e motor de sincronização respondem a pedidos de autenticação de servidor proxy. No Assistente de instalação de todas as páginas, excluindo página **Configurar** , a com a sessão iniciada no credenciais do utilizador são utilizadas. Na página **Configurar** no final do Assistente de instalação, o contexto é ligado à [conta de serviço](./connect/active-directory-aadconnect-accounts-permissions.md#azure-ad-connect-sync-service-accounts) que foi criado por si. A secção Machine. config deve ter este aspeto.

```
    <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
            <proxy
            usesystemdefault="true"
            proxyaddress="http://<PROXYADDRESS>:<PROXYPORT>"
            bypassonlocal="true"
            />
        </defaultProxy>
    </system.net>
```

Consulte MSDN para obter mais informações sobre o [proxy predefinido elemento](https://msdn.microsoft.com/library/kd3cf2ex.aspx).

Se tiver problemas com a conectividade, consulte o artigo [resolução de problemas de conectividade](active-directory-aadconnect-troubleshoot-connectivity.md).

### <a name="other"></a>Outros
- Opcional: Uma teste conta de utilizador para verificar a sincronização.

## <a name="component-prerequisites"></a>Pré-requisitos de componente

### <a name="powershell-and-net-framework"></a>PowerShell e o .net Framework
Ligação do Azure AD depende do Microsoft PowerShell e o .NET Framework 4.5.1. Irá precisar desta versão ou uma versão posterior instalado no seu servidor. Consoante a sua versão do Windows Server, faça o seguinte:

- Windows Server 2012R2
  - Microsoft PowerShell é instalado por predefinição, não é necessária nenhuma ação.
  - .NET framework 4.5.1 e versões posteriores são oferecidas através do Windows Update. Certifique-se de que instalou as atualizações mais recentes para o Windows Server no painel de controlo.
- Windows Server 2008R2 e Windows Server 2012
  - A versão mais recente do Microsoft PowerShell está disponível no **Windows Management Framework 4.0**, disponível no [Centro de transferências da Microsoft](http://www.microsoft.com/downloads).
  - Estão disponíveis no [Centro de transferências da Microsoft](http://www.microsoft.com/downloads).NET framework 4.5.1 e versões posteriores.
- Windows Server 2008
  - A versão mais recente suportada do PowerShell está disponível no **Windows Management Framework 3.0**, disponível no [Centro de transferências da Microsoft](http://www.microsoft.com/downloads).
 - Estão disponíveis no [Centro de transferências da Microsoft](http://www.microsoft.com/downloads).NET framework 4.5.1 e versões posteriores.

### <a name="enable-tls-12-for-azure-ad-connect"></a>Activar TLS 1.2 para ligação do Azure AD
Ligação do Azure AD está a utilizar TLS 1.0 por predefinição para encriptar comunicações entre o servidor de motor de sincronização e Azure AD. Pode alterar ao configurar as aplicações do .net utilizar TLS 1.2 por predefinição no servidor. Podem encontrar mais informações sobre TLS 1.2 na [2960358 de aviso de segurança do Microsoft](https://technet.microsoft.com/security/advisory/2960358).

1. Não pode ser activada TLS 1.2 no Windows Server 2008. É necessário o Windows Server 2008R2 ou posterior. Certifique-se de que a correcção .net 4.5.1 instalada para o seu sistema operativo, consulte o artigo [2960358 de aviso de segurança do Microsoft](https://technet.microsoft.com/security/advisory/2960358). Poderá ter de isto ou uma versão posterior instalado no seu servidor já.
2. Se utiliza o Windows Server 2008R2, em seguida, certifique-se de que TLS 1.2 está ativado. No Windows Server 2012 server e versões posteriores, TLS 1.2 já devem estar ativada.
```
[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2]
[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Client] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
[HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Control\SecurityProviders\SCHANNEL\Protocols\TLS 1.2\Server] "DisabledByDefault"=dword:00000000 "Enabled"=dword:00000001
```
3. Para todos os sistemas operativos, defina esta chave de registo e reinicie o servidor.
```
HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\.NETFramework\v4.0.30319
"SchUseStrongCrypto"=dword:00000001
```
4. Se também pretende ativar TLS 1.2 entre o servidor de motor de sincronização e num servidor SQL remoto, em seguida, certifique-se de que tem as versões necessárias instaladas para [TLS 1.2 suporte para o Microsoft SQL Server](https://support.microsoft.com/kb/3135244).

## <a name="prerequisites-for-federation-installation-and-configuration"></a>Pré-requisitos para Federação de instalação e configuração

### <a name="windows-remote-management"></a>Gestão remota do Windows
Ao utilizar a ligação do Azure AD para implementar os serviços de Federação do Active Directory ou o Proxy de aplicação Web, consulte os requisitos de abaixo para garantir conectividade e configuração será bem sucedida:

- Se o servidor de destino for domínio aderido, certifique-se de que gerido remoto do Windows é activado
    - Numa janela de comando PSH elevada, utilize o comando`Enable-PSRemoting –force`
- Se o servidor de destino for uma máquina WAP associada não domínio, existem algumas requisitos adicionais
    - No computador de destino (máquina WAP):
         - Certifique-se a winrm (gestão remota do Windows / WS Management) serviço está em execução através do snap-in de serviços
         - Numa janela de comando PSH elevada, utilize o comando`Enable-PSRemoting –force`
    - No computador no qual a execução do assistente (se a máquina de destino é que não sejam-associado ou não fidedigno domínio):
        - Numa janela de comando PSH elevada, utilize o comando`Set-Item WSMan:\localhost\Client\TrustedHosts –Value <DMZServerFQDN> -Force –Concatenate`
        - No Gestor de servidor:
             - Adicionar DMZ WAP anfitrião ao agrupamento de máquina (Gestor de servidor -> Gerir -> adicionar servidores... utilizar o separador DNS)
             - Separador Gestor de servidor de todos os servidores: clique com o botão direito do rato em servidor WAP e selecione gerir como..., introduza credenciais locais (não domínio) para o computador WAP
             - Para validar conectividade PSH remota, na caixa de diálogo Gestor de servidor de todos os servidores: clique com o botão direito do rato em servidor WAP e escolha o Windows PowerShell.  Deve abrir uma sessão remota do PSH para garantir que pode ser estabelecido sessões PowerShell remoto.

### <a name="ssl-certificate-requirements"></a>Requisitos de certificado SSL
**Importante:** é vivamente recomendado para utilizar o mesmo certificado SSL ao longo de todos os nós do seu farm do AD FS, bem como todos os servidores proxy de aplicação Web.

- O certificado tem de ser um X509 certificado.
- Pode utilizar um certificado autoassinado em servidores de Federação no ambiente de teste. No entanto, para um ambiente de produção, recomendamos que obtenha o certificado de uma AC pública.
    - Se utilizar um certificado que não é fidedigno publicamente, certifique-se de que o certificado instalado em cada servidor Proxy de aplicação Web é fidedigno no servidor local e em todos os servidores de Federação
- A identidade do certificado tem de corresponder o nome do serviço de Federação (por exemplo, sts.contoso.com).
    - A identidade é um de uma assunto alternativo (SAN) extensão de nome de tipo NomeDNS ou, se não existem entradas SAN, o nome de assunto especificado como um nome comum.  
    - Várias entradas de SAN podem existir no certificado, desde que uma delas corresponde ao nome do serviço de Federação.
    - Se estiver a planear utilizar participar local de trabalho, um SAN adicional é necessária com o valor **enterpriseregistration.** seguido do nome Principal de utilizador (UPN) sufixo da sua organização, por exemplo, **enterpriseregistration.contoso.com**.
- Certificados com base em CryptoAPI seguintes teclas de geração (CNG) e fornecedores de armazenamento de chaves não são suportados. Isto significa que tem de utilizar um certificado baseado num CSP (fornecedor de serviços de criptografia) e não um KSP (fornecedor de armazenamento de chaves).
- Carácter universal certificados são suportados.

### <a name="name-resolution-for-federation-servers"></a>Resolução de nomes para os servidores de Federação
- Configure registos DNS para o AD FS Federação nome do serviço (por exemplo, sts.contoso.com) para a intranet (servidor DNS interno) e o extranet (público DNS através da sua entidade de registo de domínio). Para a intranet DNS record Certifique-se de que utiliza A registos e não os registos CNAME. Isto é necessário para a autenticação do windows a partir do computador associados domínio está a funcionar corretamente.
- Se estiver a implementar o mais do que um servidor do AD FS ou servidor de Proxy de aplicação Web, em seguida, certifique-se de que configurou a sua Balanceador de carga e que os registos DNS para o nome de serviço de Federação do AD FS (por exemplo sts.contoso.com) apontem para o Balanceador de carga.
- Para autenticação integrada do windows trabalhar para aplicações de browser utilizando o Internet Explorer na intranet, certifique-se de que o nome de serviço de Federação do AD FS (por exemplo sts.contoso.com) é adicionado à zona de intranet no IE. Isto pode ser controlado através da política de grupo e implementado todos os computadores de domínio associado.

## <a name="azure-ad-connect-supporting-components"></a>Ligação do Azure AD componentes de suporte
Segue-se uma lista de componentes de que instala o ligação do Azure AD no servidor em que a ligação do Azure AD está instalado. Esta lista é para uma instalação Express básica.  Se optar por utilizar um servidor de SQL diferente na página de serviços de sincronização de instalar, em seguida, SQL Express LocalDB não está instalado localmente.

- Estado de funcionamento de ligação do Azure AD
- Serviços de início de sessão no Assistente de Microsoft Online para profissionais de TI (instalado mas não dependência no mesmo)
- Utilitários de linha de comandos do Microsoft SQL Server 2012
- Microsoft SQL Server 2012 Express LocalDB
- Microsoft SQL Server 2012 Native Client
- Pacote de redistribuição do Microsoft Visual C++ 2013

## <a name="hardware-requirements-for-azure-ad-connect"></a>Requisitos de hardware para ligação do Azure AD
A tabela abaixo mostra os requisitos mínimos para o computador da sincronização de ligação do Azure AD.

| Número de objetos no Active Directory | CPU | Memória | Tamanho de unidade de disco rígido |
| ------------------------------------- | --- | ------ | --------------- |
| Menos de 10.000 | 1.6 GHz | 4 GB | 70 GB |
| 10.000 – 50000 | 1.6 GHz | 4 GB | 70 GB |
| 50000 – 100,000 | 1.6 GHz | 16 GB | 100 GB |
| Para objetos 100.000 ou mais é necessária a versão completa do SQL Server|  |  |  |
| 100,000 – 300.000 | 1.6 GHz | 32 GB | 300 GB |
| 300.000 – 600,000 | 1.6 GHz | 32 GB | 450 GB |
| Mais do que 600,000 | 1.6 GHz | 32 GB | 500 GB |

Os requisitos mínimos para computadores que executam o AD FS ou servidores de aplicações Web é o seguinte:

- CPU: Duplo principais 1.6 GHz ou superior
- MEMÓRIA: 2GB ou superior
- Azure VM: Configuração A2 ou superior

## <a name="next-steps"></a>Próximos passos
Saiba mais sobre como [integrar a suas identidades no local com o Azure Active Directory](active-directory-aadconnect.md).
