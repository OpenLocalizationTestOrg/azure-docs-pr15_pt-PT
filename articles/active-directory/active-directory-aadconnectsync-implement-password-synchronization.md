<properties
    pageTitle="Implementar a sincronização de palavras-passe com a sincronização de ligação do Azure AD | Microsoft Azure"
    description="Fornece informações sobre como funciona a sincronização de palavra-passe e como ativá-la."
    services="active-directory"
    documentationCenter=""
    authors="markusvi"
    manager="femila"
    editor=""/>
<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/24/2016"
    ms.author="markusvi;andkjell"/>


# <a name="implementing-password-synchronization-with-azure-ad-connect-sync"></a>Implementar a sincronização de palavras-passe com a sincronização de ligação do Azure AD
Este tópico fornece-lhe as informações necessárias sincronizar as palavras-passe de utilizador a partir de no local Active Directory (AD) sessão para um baseado na nuvem Azure Active Directory (Azure AD).

## <a name="what-is-password-synchronization"></a>O que é a sincronização de palavra-passe
A probabilidade que estão bloqueados a partir do seu trabalho fique feito devido a uma palavra-passe esquecida relacionado com o número de palavras-passe diferentes terá de se lembrar. As mais palavras-passe que terá de se lembrar do, maior probabilidade esquecer um. Perguntas e chamadas acerca de palavra-passe e outros problemas relacionados com a palavra-passe de procura da maior parte dos recursos de suporte técnico.

A sincronização de palavra-passe é uma funcionalidade para sincronizar palavras-passe utilizador a partir de um Active Directory no local para uma baseado na nuvem Azure Active Directory (Azure AD).
Esta funcionalidade permite-lhe iniciar sessão para serviços de Azure Active Directory (como o Office 365, Microsoft Intune, CRM Online e serviços de domínio do Azure AD) utilizando a mesma palavra-passe estiver a utilizar para iniciar sessão na sua Active Directory no local.

![O que é a ligação do Azure AD](./media/active-directory-aadconnectsync-implement-password-synchronization/arch1.png)

Reduzir o número de palavras-passe que os seus utilizadores necessitam de manter para apenas uma, a sincronização de palavra-passe ajuda-o a:

- Melhorar a produtividade dos seus utilizadores
- Reduzir os custos de suporte técnico  

Além disso, se selecionar utilizar [**federação com o AD FS**](https://channel9.msdn.com/Series/Azure-Active-Directory-Videos-Demos/Configuring-AD-FS-for-user-sign-in-with-Azure-AD-Connect), pode, opcionalmente, ativar a sincronização de palavras-passe como uma cópia de segurança no caso de falha da sua infraestrutura de AD FS.

A sincronização de palavra-passe é uma extensão para a funcionalidade de sincronização de diretórios implementada através da ligação do Azure AD sync. Para utilizar a sincronização de palavra-passe no seu ambiente, tem de:

- Ligar a instalação do Azure AD  
- Configurar a sincronização de diretórios entre o seu no local AD e o Azure Active Directory
- Ativar a sincronização de palavra-passe

Para obter mais detalhes, consulte o artigo [integrar a suas identidades no local com o Azure Active Directory](active-directory-aadconnect.md)

> [AZURE.NOTE] Para obter mais detalhes sobre o Active Directory Domain Services que estão configuradas para a sincronização FIPS e palavra-passe, consulte o artigo [sincronizar de palavra-passe e FIPS](#password-synchronization-and-fips).

## <a name="how-password-synchronization-works"></a>Como funciona a sincronização de palavra-passe
Serviço de domínio do Active Directory armazena as palavras-passe no formulário de uma representação de valor hash da palavra-passe de utilizador real. Um valor hash é um resultado de uma função matemática unidirecional (a "*o hashing algoritmo*"). Não existe nenhum método para reverter o resultado de uma função unidirecional para a versão de texto simples de uma palavra-passe. Não pode utilizar um hash de palavra-passe para iniciar sessão na sua rede no local.

Para sincronizar a sua palavra-passe, a ligação do Azure AD sync extrai hash sua palavra-passe do Active Directory no local. Processamento de segurança extra é aplicado o hash de palavra-passe antes de-está sincronizada com o serviço de autenticação do Azure Active Directory. As palavras-passe são sincronizadas numa base por utilizador e por ordem cronológica.

O fluxo de dados reais do processo de sincronização de palavra-passe é semelhante a sincronização dos dados de utilizador como DisplayName ou endereços de E-Mail. No entanto, as palavras-passe são sincronizadas mais frequentemente do que a janela de sincronização de diretório padrão para outros atributos. O processo de sincronização de palavra-passe é executado em 2 minutos. Não é possível modificar a frequência deste processo. Quando sincronizar uma palavra-passe, são substituídos a palavra-passe de nuvem existente.

A primeira vez, ativar a funcionalidade de sincronização de palavra-passe, executa uma sincronização inicial as palavras-passe de todos os utilizadores no âmbito. Explicitamente não é possível definir um subconjunto de palavras-passe de utilizador que pretende sincronizar.

Quando altera uma palavra-passe no local, a palavra-passe actualizada é sincronizada, mais frequentemente num assunto de minutos.
A funcionalidade de sincronização de palavra-passe automaticamente repetições de tentativas de sincronização falha. Se ocorre um erro durante uma tentativa de sincronizar uma palavra-passe, um erro tem sessão iniciado no Visualizador de eventos.

A sincronização de uma palavra-passe não tem impacto no utilizador iniciou a sessão.
A sessão de serviço de nuvem actual imediatamente não é afetada por uma alteração de palavra-chave sincronizadas ocorre enquanto tem sessão iniciada num serviço na nuvem. No entanto, quando o serviço de nuvem requer que autenticar novamente, tem de fornecer a sua palavra-passe nova.

> [AZURE.NOTE] Sincronização de palavra-passe só é suportada para o utilizador de tipo de objeto no Active Directory. Não é suportada para o tipo de objeto iNetOrgPerson.

### <a name="how-password-synchronization-works-with-azure-ad-domain-services"></a>Como funciona a sincronização de palavra-passe com os serviços de domínio do Azure AD
Também pode utilizar a funcionalidade de sincronização de palavra-passe para sincronizar seu no local as palavras-passe para os [Serviços de domínio do Azure AD](../active-directory-domain-services/active-directory-ds-overview.md). Este cenário permite que os serviços de domínio do Azure AD autenticar os utilizadores na nuvem com todos os métodos de disponíveis no seu no local AD. A experiência deste cenário é semelhante a utilizar a ferramenta de migração do Active Directory (ADMT) num ambiente no local.

### <a name="security-considerations"></a>Considerações de segurança
Quando sincronizar palavras-passe, a versão de texto simples da sua palavra-passe não é apresentada a funcionalidade de sincronização de palavra-passe, para Azure AD, ou qualquer um dos serviços associados.

Além disso, não existe nenhuma requisito no Active Directory no local para armazenar a palavra-passe num formato reversível. Um resumo do hash de palavra-passe do Active Directory é utilizado para a transmissão de entre no local AD e o Azure Active Directory. O resumo do hash palavra-passe não pode ser utilizado para aceder aos recursos no seu ambiente no local.

### <a name="password-policy-considerations"></a>Considerações de políticas de palavra-passe
Existem dois tipos de políticas de palavra-passe que são afectados ao ativar a sincronização de palavra-passe:

1. Política de complexidade de palavra-passe
2. Política de expiração de palavra-passe

**Política de complexidade de palavra-passe**  
Quando ativar a sincronização de palavra-passe, as políticas de complexidade de palavra-passe no seu no local Active Directory substituem as políticas de complexidade na nuvem para utilizadores sincronizados. Pode utilizar todas as palavras-passe válidas do seu no local Active Directory para aceder aos serviços do Azure AD.

> [AZURE.NOTE] Palavras-passe para os utilizadores que são criados diretamente na nuvem estão ainda sujeitos a políticas de palavra-passe, tal como foi definido na nuvem.

**Política de expiração de palavra-passe**  
Se for um utilizador no âmbito da sincronização de palavra-passe, a palavra-passe da conta de nuvem está definida para "*Nunca expirar*".
Pode continuar a iniciar sessão no seu serviços em nuvem utilizando uma palavra-passe sincronizada que expirou no seu ambiente no local. A palavra-passe de nuvem é atualizada da próxima vez que altera a palavra-passe no ambiente no local.

### <a name="overwriting-synchronized-passwords"></a>Substituir sincronizados palavras-passe
Um administrador manualmente pode repor a palavra-passe através do Windows PowerShell.

Neste caso, a nova palavra-passe substitui a palavra-passe sincronizada e todas as políticas de palavra-passe definidas na nuvem são aplicadas à nova palavra-passe.

Se alterar a palavra-passe no local novamente, a nova palavra-passe é sincronizada na nuvem e substitui a palavra-passe atualizada manualmente.

## <a name="enabling-password-synchronization"></a>Ativar a sincronização de palavra-passe
Sincronização de palavra-passe está ativada automaticamente, quando instala a ligação do Azure AD utilizando as **Definições rápidas**. Para obter mais detalhes, consulte o artigo [introdução com ligação do Azure AD utilizando as definições express](./connect/active-directory-aadconnect-get-started-express.md).

Se utilizar definições personalizadas ao instalar o ligação do Azure AD, ativar a sincronização de palavras-passe na página de início de sessão do utilizador. Para obter mais detalhes, consulte o artigo [instalação personalizada de ligação do Azure AD](./connect/active-directory-aadconnect-get-started-custom.md).

![Ativar a sincronização de palavra-passe](./media/active-directory-aadconnectsync-implement-password-synchronization/usersignin.png)

### <a name="password-synchronization-and-fips"></a>Sincronização de palavra-passe e FIPS
Se o seu servidor foi bloqueado acordo com Federal Information Processing padrão (FIPS), em seguida, MD5 tiver sido desativada.

**Para ativar MD5 para a sincronização de palavra-passe, execute os seguintes passos:**

1. Aceda a **%programfiles%\Azure AD Sync\Bin**.
2. Abra **miiserver.exe.config**.
3. Vá para o nó **configuração/runtime** (no final do ficheiro).
4. Adicione o nó do seguinte:`<enforceFIPSPolicy enabled="false"/>`
5. Guarde as suas alterações.

Para sua referência, deste recorte é como esta deve o seguinte aspeto:

```
    <configuration>
        <runtime>
            <enforceFIPSPolicy enabled="false"/>
        </runtime>
    </configuration>
```

Para obter informações sobre a segurança e FIPS consulte [AAD Sync de palavra-passe, encriptação e FIPS conformidade](https://blogs.technet.microsoft.com/enterprisemobility/2014/06/28/aad-password-sync-encryption-and-fips-compliance/)

## <a name="troubleshooting-password-synchronization"></a>Resolução de problemas de sincronização de palavra-passe
Se as palavras-passe não estão a sincronizar como esperado,-pode ser para um subconjunto dos utilizadores ou para todos os utilizadores.

- Se tiver um problema com individuais objetos, em seguida, consulte [resolver problemas de um objeto que não está a sincronizar as palavras-passe](#troubleshoot-one-object-that-is-not-synchronizing-passwords).
- Se tiver um problema onde é sincronizados sem palavras-passe, consulte o artigo [resolução de problemas onde é sincronizados sem palavras-passe](#troubleshoot-issues-where-no-passwords-are-synchronized).

### <a name="troubleshoot-one-object-that-is-not-synchronizing-passwords"></a>Resolver problemas de um objeto que não está a sincronizar as palavras-passe
Pode facilmente resolver os problemas de sincronização de palavra-passe ao rever o estado de um objeto.

Inicie no **utilizadores do Active Directory e computadores**. Localize o utilizador e confirme que o **que utilizador tem de alterar a palavra-passe no início de sessão seguinte** está desmarcada.
![Active Directory produtivas palavras-passe](./media/active-directory-aadconnectsync-implement-password-synchronization/adprodpassword.png)  
Se estiver selecionado, em seguida, peça ao utilizador para iniciar sessão e alterar a palavra-passe. Palavras-passe temporárias não são sincronizadas com o Azure AD.

Se parecer correta no Active Directory, em seguida, o próximo passo é a seguir o utilizador do motor de sincronização. Ao seguir o utilizador a partir do Active Directory no local para Azure AD, pode ver se existir um erro descritivo no objeto.

1. Inicie o **[Gestor de serviço de sincronização](active-directory-aadconnectsync-service-manager-ui.md)**.
2. Clique em **conexões**.
3. Selecione o **Conector do Active Directory** , o utilizador está localizado no.
4. Selecione o **espaço de conector de pesquisa**.
5. Localize o utilizador que está à procura.
6. Selecione o separador de **linhagem da** e certifique-se de que pelo menos uma regra de sincronização mostra a **Sincronização de palavra-passe** como **True**. Na configuração predefinida, o nome da regra de sincronização é **na partir do AD - utilizador AccountEnabled**.  
    ![Informações de linhagem da sobre um utilizador](./media/active-directory-aadconnectsync-implement-password-synchronization/cspasswordsync.png)  
7. Em seguida, [siga o utilizador](active-directory-aadconnectsync-service-manager-ui-connectors.md#follow-an-object-and-its-data-through-the-system) através de metaverse para o espaço do Azure AD conexão. O objeto de espaço de conexão deve ter uma regra de saída com a **Sincronização de palavra-passe** definida como **Verdadeiro**. Na configuração predefinida, o nome da regra de sincronização é **fora para AAD - utilizador aderir**.  
    ![Propriedades de espaço do conector de um utilizador](./media/active-directory-aadconnectsync-implement-password-synchronization/cspasswordsync2.png)  
8. Para ver os detalhes de sincronização de palavra-passe do objeto para a semana passada, clique em **mais...**.  
    ![Registar os detalhes do objeto](./media/active-directory-aadconnectsync-implement-password-synchronization/csobjectlog.png)  

Coluna Estado pode ter os seguintes valores:

Estado | Descrição
---- | -----
Sucesso | Palavra-passe foram sincronizada com êxito.
FilteredByTarget | Palavra-passe está definido para o **utilizador tem de alterar a palavra-passe no início de sessão seguinte**. Não tenha sido sincronizado a palavra-passe.
NoTargetConnection | Nenhum objeto na metaverse ou no espaço de conexão Azure AD.
SourceConnectorNotPresent | Nenhum objeto que se encontram no espaço de conector do Active Directory no local.
TargetNotExportedToDirectory | O objeto no espaço de conexão Azure AD ainda não foram exportado.
MigratedCheckDetailsForMoreInfo | Entrada de registo foi criada antes de compilação 1.0.9125.0 e é apresentada no seu estado legado.

### <a name="troubleshoot-issues-where-no-passwords-are-synchronized"></a>Resolução de problemas de onde é sincronizados sem palavras-passe
Comece por executar o script na secção [obter o estado das definições de sincronização de palavras-passe](#get-the-status-of-password-sync-settings). Dá-lhe uma descrição geral de configuração de sincronizar a palavra-passe.  
![Resultado de script do PowerShell a partir das definições de sincronização de palavra-passe](./media/active-directory-aadconnectsync-implement-password-synchronization/psverifyconfig.png)  
Se a funcionalidade não está ativada no Azure AD ou se o estado de canal de sincronização não estiver ativado, em seguida, execute o Assistente de instalação de ligar. Selecione **Personalizar as opções de sincronização** e anular a seleção de sincronização de palavra-passe. Esta alteração temporariamente desativa a funcionalidade. Em seguida, execute o assistente novamente e voltar a ativar a sincronização de palavra-passe. Execute o script novamente para confirmar que a configuração está correta.

Se o script mostra que não existe nenhuma heartbeat, em seguida, execute o script na [acionar uma sincronização completa de todas as palavras-passe](#trigger-a-full-sync-of-all-passwords). Este script pode também ser utilizado para outras situações onde a configuração está correta, mas as palavras-passe não estão sincronizadas.

#### <a name="get-the-status-of-password-sync-settings"></a>Obter o estado das definições de sincronização de palavras-passe

```
Import-Module ADSync
$connectors = Get-ADSyncConnector
$aadConnectors = $connectors | Where-Object {$_.SubType -eq "Windows Azure Active Directory (Microsoft)"}
$adConnectors = $connectors | Where-Object {$_.ConnectorTypeName -eq "AD"}
if ($aadConnectors -ne $null -and $adConnectors -ne $null)
{
    if ($aadConnectors.Count -eq 1)
    {
        $features = Get-ADSyncAADCompanyFeature -ConnectorName $aadConnectors[0].Name
        Write-Host
        Write-Host "Password sync feature enabled in your Azure AD directory: "  $features.PasswordHashSync
        foreach ($adConnector in $adConnectors)
        {
            Write-Host
            Write-Host "Password sync channel status BEGIN ------------------------------------------------------- "
            Write-Host
            Get-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector.Name
            Write-Host
            $pingEvents =
                Get-EventLog -LogName "Application" -Source "Directory Synchronization" -InstanceId 654  -After (Get-Date).AddHours(-3) |
                    Where-Object { $_.Message.ToUpperInvariant().Contains($adConnector.Identifier.ToString("D").ToUpperInvariant()) } |
                    Sort-Object { $_.Time } -Descending
            if ($pingEvents -ne $null)
            {
                Write-Host "Latest heart beat event (within last 3 hours). Time " $pingEvents[0].TimeWritten
            }
            else
            {
                Write-Warning "No ping event found within last 3 hours."
            }
            Write-Host
            Write-Host "Password sync channel status END ------------------------------------------------------- "
            Write-Host
        }
    }
    else
    {
        Write-Warning "More than one Azure AD Connectors found. Please update the script to use the appropriate Connector."
    }
}
Write-Host
if ($aadConnectors -eq $null)
{
    Write-Warning "No Azure AD Connector was found."
}
if ($adConnectors -eq $null)
{
    Write-Warning "No AD DS Connector was found."
}
Write-Host
```

#### <a name="trigger-a-full-sync-of-all-passwords"></a>Acionar uma sincronização completa de todas as palavras-passe
Pode acionar uma sincronização completa de todas as palavras-passe utilizando o seguinte script:

```
$adConnector = "<CASE SENSITIVE AD CONNECTOR NAME>"
$aadConnector = "<CASE SENSITIVE AAD CONNECTOR NAME>"
Import-Module adsync
$c = Get-ADSyncConnector -Name $adConnector
$p = New-Object Microsoft.IdentityManagement.PowerShell.ObjectModel.ConfigurationParameter "Microsoft.Synchronize.ForceFullPasswordSync", String, ConnectorGlobal, $null, $null, $null
$p.Value = 1
$c.GlobalParameters.Remove($p.Name)
$c.GlobalParameters.Add($p)
$c = Add-ADSyncConnector -Connector $c
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $aadConnector -Enable $false
Set-ADSyncAADPasswordSyncConfiguration -SourceConnector $adConnector -TargetConnector $aadConnector -Enable $true
```

## <a name="next-steps"></a>Próximos passos

* [Azure AD ligar sincronização: Opções de personalização de sincronização](active-directory-aadconnectsync-whatis.md)
* [Integrar a suas identidades no local com o Azure Active Directory](active-directory-aadconnect.md)
