<properties
    pageTitle="Configurar LDAP seguro (LDAPS) nos serviços de domínio do Azure AD | Microsoft Azure"
    description="Configurar o seguro LDAP (LDAPS) para um domínio gerido de serviços de domínio do Azure AD"
    services="active-directory-ds"
    documentationCenter=""
    authors="mahesh-unnikrishnan"
    manager="stevenpo"
    editor="curtand"/>

<tags
    ms.service="active-directory-ds"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/21/2016"
    ms.author="maheshu"/>

# <a name="configure-secure-ldap-ldaps-for-an-azure-ad-domain-services-managed-domain"></a>Configurar o seguro LDAP (LDAPS) para um domínio gerido de serviços de domínio do Azure AD
Este artigo mostra como pode ativar seguro Lightweight Directory Access Protocol (LDAPS) para o seu domínio gerido de serviços de domínio do Azure AD. LDAP seguro é também conhecida como ' LDAP Lightweight Directory Access Protocol () através de Secure Sockets Layer (SSL) / segurança de camada de transporte (TLS)'.

## <a name="before-you-begin"></a>Antes de começar
Para efetuar as tarefas listadas neste artigo, precisa de:

1. Uma **subscrição do Azure**válida.

2. Um **diretório do Azure AD** - quer sincronizadas com um diretório no local ou apenas na nuvem.

3. **Serviços de domínio do Azure AD** tem de estar ativada para o diretório do Azure AD. Se ainda não o tiver feito, siga todas as tarefas descritas o [Guia de introdução](./active-directory-ds-getting-started.md).

4. Um **certificado para ser utilizados para ativar LDAP seguro**.
    - **Recomendado** - obter um certificado do seu AC empresarial ou de uma autoridade de certificação pública. Esta opção de configuração é mais segura.
    - Em alternativa, pode também optar por [criar um certificado autoassinado](#task-1---obtain-a-certificate-for-secure-ldap) como é mostrado neste artigo.

<br>

### <a name="requirements-for-the-secure-ldap-certificate"></a>Requisitos para o certificado LDAP seguro
Adquira um certificado válido pelas diretrizes seguintes, antes de ativar LDAP seguro. Encontrar falhas se tentar ativar LDAP seguro para o seu domínio gerido com um certificado inválida incorretos.

1. **Fidedignos emissor** - o certificado deve ser emitido por uma autoridade de fidedignos por computadores que precisam de aceder ao domínio utilizando LDAP seguro. Esta autoridade pode ser autoridade de certificação empresarial da sua organização ou uma autoridade de certificação pública fidedignos por estas computadores.

2. **Tempo de vida** - o certificado tem de ser válido, pelo menos, seguinte 3 a 6 meses. Acesso LDAP seguro para o seu domínio gerido for interrompido quando o certificado expirar.

3. **Nome de assunto** - o nome de assunto no certificado tem de ser um caráter universal do seu domínio gerido. Por exemplo, se o seu domínio se chamar 'contoso100.com', nome de assunto o certificado tem de ser ' *. contoso100.com'. Defina o nome DNS (nome alternativo assunto) para este nome universais.

3. **Utilização de chaves** - o certificado tem de ser configurado para o seguinte utiliza - assinaturas digitais e Cifração.

4. **Objectivo de certificado** - o certificado tem de ser válido para autenticação de servidor SSL.

<br>

## <a name="task-1---obtain-a-certificate-for-secure-ldap"></a>Tarefa 1 - obter um certificado para LDAP seguro
A primeira tarefa envolve obter um certificado utilizado para um acesso seguro LDAP para o domínio gerido. Tem duas opções:

- Obter um certificado a partir de uma autoridade de certificação. A autoridade pode ser AC empresarial da sua organização ou uma autoridade de certificação público.

- Crie um certificado autoassinado.


### <a name="option-a-recommended---obtain-a-secure-ldap-certificate-from-a-certification-authority"></a>Opção (recomendado) - obter um certificado LDAP seguro a partir de uma autoridade de certificação
Se a sua organização implementa uma infraestrutura chave pública (PKI) do enterprise, terá de obter um certificado da autoridade de certificação (AC) enterprise para a sua organização. Se a sua organização obtém os seus certificados a partir de uma autoridade de certificação pública, terá de obter o certificado LDAP seguro a partir desse autoridade de certificação pública.

Quando pedir um certificado, certifique-se de que segue os requisitos descritos em [requisito para o certificado LDAP seguro](#requirements-for-the-secure-ldap-certificate).

> [AZURE.NOTE] O emissor do certificado LDAPS tem de confiar em computadores cliente que precisam de aceder ao domínio gerido utilizando LDAP seguro.


### <a name="option-b---create-a-self-signed-certificate-for-secure-ldap"></a>Opção B - criar um certificado autoassinado para LDAP seguro
Pode optar por criar um certificado autoassinado para LDAP seguro, se:

- os certificados na sua organização não são emitidos por uma autoridade de certificação empresarial ou
- não esperar utilizar um certificado a partir de uma autoridade de certificação público.

**Criar um certificado autoassinado através do PowerShell**

No seu computador Windows, abra uma nova janela do PowerShell como **administrador** e escreva os seguintes comandos, para criar um certificado autoassinado novo.

    $lifetime=Get-Date

    New-SelfSignedCertificate -Subject *.contoso100.com -NotAfter $lifetime.AddDays(365) -KeyUsage DigitalSignature, KeyEncipherment -Type SSLServerAuthentication -DnsName *.contoso100.com

No exemplo anterior, substitua 'contoso100.com' com o nome de domínio DNS do seu domínio gerido de serviços de domínio do Azure AD.

![Selecione diretório do Azure AD](./media/active-directory-domain-services-admin-guide/secure-ldap-powershell-create-self-signed-cert.png)

O certificado autoassinado recentemente criado é colocado no arquivo de certificados no computador local.


## <a name="task-2---export-the-secure-ldap-certificate-to-a-pfx-file"></a>Tarefa 2 - exportar o protocolo LDAP seguro certificado para um. Ficheiro PFX
Antes de iniciar esta tarefa, certifique-se de que obteve o certificado LDAP seguro a partir do seu autoridade de certificação empresarial ou de uma autoridade de certificação público ou ter criado um certificado autoassinado.

Execute os seguintes passos, para exportar o certificado LDAPS para um. Ficheiro PFX.

1. Prima o botão **Iniciar** e escreva **R**. Na caixa de diálogo **Executar** , escreva **mmc** e clique em **OK**.

    ![Iniciação consola MMC](./media/active-directory-domain-services-admin-guide/secure-ldap-start-run.png)

2. No pedido de **Controlo de conta de utilizador** , clique em **Sim** para iniciação MMC (Microsoft Management Console) como administrador.

3. A partir do menu **ficheiro** , clique em **Adicionar/Remover Snap-in....**.

    ![Adicionar snap-in consola MMC](./media/active-directory-domain-services-admin-guide/secure-ldap-add-snapin.png)

4. Na caixa de diálogo **Adicionar ou Remover Snap-ins** , selecione o snap-in **certificados** e clique na **Adicionar >** botão.

    ![Adicionar snap-in Certificados a consola MMC](./media/active-directory-domain-services-admin-guide/secure-ldap-add-certificates-snapin.png)

5. No Assistente de **snap-in Certificados** , selecione a **conta de computador** e clique em **seguinte**.

    ![Adicionar snap-in certificados para a conta de computador](./media/active-directory-domain-services-admin-guide/secure-ldap-add-certificates-computer-account.png)

6. Na página **Selecionar computador** , selecione **Computador Local: (no computador em que esta consola está a ser executada)** e clique em **Concluir**.

    ![Adicionar snap-in Certificados - selecionar computador](./media/active-directory-domain-services-admin-guide/secure-ldap-add-certificates-local-computer.png)

7. Na caixa de diálogo **Adicionar ou Remover Snap-ins** , clique em **OK** para adicionar snap-in Certificados MMC.

    ![Adicionar snap-in Certificados MMC - concluída](./media/active-directory-domain-services-admin-guide/secure-ldap-add-certificates-snapin-done.png)

8. Na janela do MMC, clique para expandir a **Raiz da consola**. Deverá visualizar o snap-in Certificados carregado. Clique em **certificados (computador Local)** para expandir. Clique para expandir o nó **pessoal** , seguido pelo nó **certificados** .

    ![Arquivo de certificados pessoais aberta](./media/active-directory-domain-services-admin-guide/secure-ldap-open-personal-store.png)

9. Deverá ver o certificado autoassinado criámos. Pode examinar as propriedades do certificado para garantir que a impressão digital corresponde que comunicado em do windows PowerShell, quando criou o certificado.

10. Selecione o certificado autoassinado e, em seguida, **clique com botão direito**. No menu de contexto, selecione **Todas as tarefas** e selecione **exportar...**.

    ![Certificado de exportação](./media/active-directory-domain-services-admin-guide/secure-ldap-export-cert.png)

11. No **Assistente de exportação do certificado**, clique em **seguinte**.

    ![Assistente de exportação do certificado](./media/active-directory-domain-services-admin-guide/secure-ldap-export-cert-wizard.png)

12. Na página **Exportar chave privada** , selecione **Sim, exporte a chave privada**e clique em **seguinte**.

    ![Exportar chave privada de certificado](./media/active-directory-domain-services-admin-guide/secure-ldap-export-private-key.png)

    > [AZURE.WARNING] TEM de exportar a chave privada juntamente com o certificado. Se fornecer um PFX que não contém a chave privada para o certificado, permitindo-LDAP seguro para o seu domínio gerido irá falhar.

13. Na página do **Formato de ficheiro de exportação** , selecione **intercâmbio de informações pessoais - PKCS #12 (. PFX)** como o formato de ficheiro para o certificado exportado.

    ![Exportar o formato de ficheiro de certificado](./media/active-directory-domain-services-admin-guide/secure-ldap-export-to-pfx.png)

    > [AZURE.NOTE] Apenas a. Formato de ficheiro PFX é suportado. Não exporte o certificado para o. Formato de ficheiro CER.

14. Na página de **segurança** , selecione a opção de **palavra-passe** e escreva uma palavra-passe para proteger a. Ficheiro PFX. Uma vez que vai ser necessária a tarefa seguinte, lembre-se esta palavra-passe. Clique em **seguinte** para avançar.

    ![Palavra-passe para a exportação de certificado ](./media/active-directory-domain-services-admin-guide/secure-ldap-export-select-password.png)

    > [AZURE.NOTE] Anote esta palavra-passe. Precisa delas permitindo LDAP seguro para este domínio gerido na [tarefa 3 - ativar LDAP seguro para o domínio gerido](#task-3---enable-secure-ldap-for-the-managed-domain)

15. Na página **ficheiro a exportar** , especifique o nome do ficheiro e a localização onde pretende exporte o certificado.

    ![Caminho de exportação de certificado](./media/active-directory-domain-services-admin-guide/secure-ldap-export-select-path.png)

16. Na página seguinte, clique em **Concluir** para exportar o certificado para um ficheiro PFX. Deverá ver a caixa de diálogo confirmação quando o certificado foi exportado.

    ![Certificado de exportação concluído](./media/active-directory-domain-services-admin-guide/secure-ldap-exported-as-pfx.png)


## <a name="task-3---enable-secure-ldap-for-the-managed-domain"></a>Tarefa 3 - ativar LDAP seguro para o domínio gerido
Para ativar LDAP seguro, execute os seguintes passos de configuração:

1. Navegue para o **[portal clássica Azure](https://manage.windowsazure.com)**.

2. Selecione o nó **Do Active Directory** no painel da esquerda.

3. Selecione o diretório do Azure AD (também conhecido como 'inquilino'), para o qual ativou dos serviços de domínio do Azure AD.

    ![Selecione diretório do Azure AD](./media/active-directory-domain-services-getting-started/select-aad-directory.png)

4. Clique no separador **Configurar** .

    ![Configurar o separador de diretório](./media/active-directory-domain-services-getting-started/configure-tab.png)

5. Desloque para baixo para a secção intitulada **dos serviços de domínio**. Deverá visualizar uma opção intitulada **Seguro LDAP (LDAPS)** conforme apresentado na captura de ecrã seguinte:

    ![Secção de configuração dos serviços de domínio](./media/active-directory-domain-services-admin-guide/secure-ldap-start.png)

6. Clique no botão de **certificado de configurar...** para ativar a caixa de diálogo **Configurar certificado para LDAP seguro** .

    ![Configurar um certificado para LDAP seguro](./media/active-directory-domain-services-admin-guide/secure-ldap-configure-cert-page.png)

7. Clique no ícone de pasta abaixo **PFX com o certificado do ficheiro** para especificar o ficheiro. PFX, que contém o certificado que pretende utilizar para acesso LDAP seguro para o domínio gerido. Também introduza a palavra-passe que especificou quando exportar o certificado para o ficheiro PFX. Em seguida, clique no botão concluído na parte inferior.

    ![Especificar ficheiro LDAP PFX seguro e palavra-passe](./media/active-directory-domain-services-admin-guide/secure-ldap-specify-pfx.png)

8. A secção **Serviços de domínio** do separador **Configurar** deve obter cinzento e está na **pendente …** estado para alguns minutos. Durante este período, o certificado LDAPS é verificado para precisão e LDAP seguro está configurado para o seu domínio gerido.

    ![Seguro LDAP - pendentes Estado](./media/active-directory-domain-services-admin-guide/secure-ldap-pending-state.png)

    > [AZURE.NOTE] Demora cerca de 10 a 15 minutos para ativar LDAP seguro para o seu domínio gerido. Se o certificado LDAP seguro fornecido não correspondem aos critérios necessários, LDAP seguro não está ativado para o seu diretório e verá uma falha. Por exemplo, o nome de domínio está incorreto, o certificado está expirado ou expira mais cedo, etc.

9. Quando LDAP seguro com êxito está ativado para o seu domínio gerido, o **pendente …** mensagem deverá desaparecer. Deverá visualizar a impressão digital do certificado apresentado.

    ![LDAP seguro activado](./media/active-directory-domain-services-admin-guide/secure-ldap-enabled.png)

<br>


## <a name="task-4---enable-secure-ldap-access-over-the-internet"></a>Tarefa 4 - ativar LDAP um acesso seguro através da internet
**Tarefa opcional** - se não tenciona para aceder ao domínio gerido utilizando LDAPS através da internet, ignorar esta tarefa de configuração.

Antes de começar esta tarefa, certifique-se de que concluiu os passos descritos no [tarefa 3](#task-3---enable-secure-ldap-for-the-managed-domain).

1. Deverá visualizar uma opção para **ATIVAR seguro LDAP ACCESS através da INTERNET** , na secção da página **Configurar** **Serviços de domínio** . Esta opção está definida para **não** por predefinição, uma vez que o acesso à internet para o domínio gerido sobre LDAP seguro está desativado por predefinição.

    ![LDAP seguro activado](./media/active-directory-domain-services-admin-guide/secure-ldap-enabled2.png)

2. Botão de alternar **ATIVAR o acesso seguro LDAP através da INTERNET** como **Sim**. Clique no botão **Guardar** no painel inferior.
    ![LDAP seguro activado](./media/active-directory-domain-services-admin-guide/secure-ldap-enable-internet-access.png)

3. A secção **Serviços de domínio** do separador **Configurar** deve obter cinzento e está na **pendente …** estado para alguns minutos. Após algum tempo, o acesso à internet para o seu domínio gerido sobre LDAP seguro está ativado.

    ![Seguro LDAP - pendentes Estado](./media/active-directory-domain-services-admin-guide/secure-ldap-enable-internet-access-pending-state.png)

    > [AZURE.NOTE] Demora cerca de 10 minutos para ativar o acesso à internet sobre LDAP seguro para o seu domínio gerido.

4. Quando um acesso seguro LDAP para o seu domínio gerido através da internet com êxito está ativado, o **pendente …** mensagem deverá desaparecer. Deverá ver o endereço IP externo que pode ser utilizado para aceder ao seu diretório sobre LDAPS no campo **IP endereço para LDAPS acesso externo**.

    ![LDAP seguro activado](./media/active-directory-domain-services-admin-guide/secure-ldap-internet-access-enabled.png)

<br>

## <a name="task-5---configure-dns-to-access-the-managed-domain-from-the-internet"></a>Tarefa 5 - configurar o DNS para aceder ao domínio gerido a partir da internet
**Tarefa opcional** - se não tenciona para aceder ao domínio gerido utilizando LDAPS através da internet, ignorar esta tarefa de configuração.

Antes de começar esta tarefa, certifique-se de que concluiu os passos descritos no [tarefa 4](#task-4---enable-secure-ldap-access-over-the-internet).

Assim que ativou o acesso LDAP seguro através da internet para o seu domínio gerido, tem de atualizar o DNS para que os computadores cliente podem encontrar este domínio gerido. No final da tarefa 4, um endereço IP externo é apresentado no separador **Configurar** **IP endereço para LDAPS acesso externo**.

Configure fornecedor externo de DNS para que o nome de DNS do domínio gerido (por exemplo, ' contoso100.com') aponta para este endereço IP externo. No nosso exemplo, precisamos de criar a seguinte entrada DNS:

    contoso100.com  -> 52.165.38.113

Já está - está agora pronto para ligar ao domínio gerido utilizando LDAP seguro através da internet.

> [AZURE.WARNING] Lembre-se de que os computadores cliente devem confiar o emissor do certificado LDAPS para poderá ligar-se com êxito ao domínio gerido utilizando LDAPS. Se estiver a utilizar uma autoridade de certificação empresarial ou de uma autoridade de certificação fidedigna publicamente, não terá de fazer nada uma vez que os computadores cliente confiar emitentes estes certificado. Se estiver a utilizar um certificado autoassinado, terá de instalar a peça pública do certificado autoassinado para o arquivo de certificados fidedignos no computador cliente.

<br>

## <a name="related-content"></a>Conteúdo relacionado

- [Administrar um domínio gerido de serviços de domínio do Azure AD](active-directory-ds-admin-guide-administer-domain.md)
