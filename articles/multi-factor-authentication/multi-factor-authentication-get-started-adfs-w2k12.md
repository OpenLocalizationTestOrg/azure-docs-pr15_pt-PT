<properties
    pageTitle="Servidor MFA com o Windows Server 2012 R2 AD FS | Microsoft Azure"
    description="Este artigo descreve como começar com autenticação Multifator de Azure e o AD FS no Windows Server 2012 R2."
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="yossib"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/14/2016"
    ms.author="kgremban"/>


# <a name="secure-your-cloud-and-on-premises-resources-using-azure-multi-factor-authentication-server-with-ad-fs-in-windows-server-2012-r2"></a>Proteger os recursos na nuvem e no local com o servidor de autenticação Multifator Azure com o AD FS no Windows Server 2012 R2

Se utilizar os serviços de Federação do Active Directory (AD FS) e pretende segura na nuvem ou recursos no local, pode configurar o servidor de autenticação Multifator Azure para trabalhar com o AD FS. Esta configuração accionadores verificação de dois passos para os pontos finais de valor máximo.

Neste artigo, discutimos utilizando o servidor de autenticação Multifator Azure com o AD FS no Windows Server 2012 R2. Para mais informações, leia sobre como [recursos na nuvem e no local seguros utilizando o servidor de autenticação Multifator Azure com o AD FS 2.0](multi-factor-authentication-get-started-adfs-adfs2.md).

## <a name="secure-windows-server-2012-r2-ad-fs-with-azure-multi-factor-authentication-server"></a>Seguro Windows Server 2012 R2 AD FS com o servidor de autenticação Multifator Azure

Quando instala o servidor de autenticação Multifator Azure, tem as seguintes opções:

- Instalar o servidor de autenticação Multifator Azure localmente no mesmo servidor ADFS
- Instalar o adaptador de autenticação Multifator de Azure localmente no servidor ADFS e, em seguida, instale o servidor de autenticação Multifator num computador diferente

Antes de começar, tenha em atenção as seguintes informações:

- Não são necessários para instalar o servidor de autenticação Multifator Azure no servidor ADFS. No entanto, tem de instalar o adaptador de autenticação Multifator do AD FS num Windows Server 2012 R2 que esteja a executar o AD FS. Pode instalar o servidor num computador diferente se é uma versão suportada e instalar o adaptador de AD FS em separado no seu servidor de Federação do AD FS. Consulte os seguintes procedimentos para saber como instalar o adaptador separadamente.

- Quando a placa de servidor de MFA AD FS foi concebida, foi antecipada que o AD FS poderia passar o nome da parte dependente à placa de. Em seguida, o nome de festa dependente pode ser utilizado como nome de uma aplicação. No entanto, esta activadas não deve ser as maiúsculas/minúsculas. Se a sua organização utilizar mensagem de texto ou métodos de verificação da aplicação móvel, as cadeias definidas nas definições de empresa conter um marcador de posição, <$*Nome_aplicação*$>. Este marcador de posição não é substituído automaticamente quando utiliza a placa de AD FS. Recomendamos que remova o marcador de posição das cadeias adequadas quando seguro o AD FS.

- A conta que utiliza para iniciar sessão no tem de ter os direitos de utilizador para criar grupos de segurança no seu serviço de Active Directory.

- O Assistente de instalação de placa de autenticação Multifator AD FS cria um grupo de segurança denominado PhoneFactor administradores na sua instância do Active Directory. Em seguida, adiciona a conta de serviço do AD FS do seu serviço de Federação para este grupo. Recomendamos que verifique no controlador de domínio, o grupo de administradores PhoneFactor facto é criado e o AD FS conta de serviço é um membro deste grupo. Se necessário, adicione manualmente a conta de serviço do AD FS para o grupo de administradores de PhoneFactor no seu controlador de domínio.

- Para obter informações sobre como instalar o SDK do serviço Web com o portal de utilizador, leia sobre [Implementar o portal do utilizador para o servidor de autenticação Multifator Azure.](multi-factor-authentication-get-started-portal.md)


### <a name="install-azure-multi-factor-authentication-server-locally-on-the-ad-fs-server"></a>Instalar o servidor de autenticação Multifator Azure localmente no servidor ADFS

1. Transfira e instale o servidor de autenticação Multifator Azure no servidor ADFS. Para obter informações de instalação, leia sobre como [começar com o servidor de autenticação Multifator Azure](multi-factor-authentication-get-started-server.md).
2. Na consola de gestão de servidor de autenticação Multifator Azure, clique no ícone do **AD FS** e, em seguida, selecione as opções de **inscrição do utilizador de permitir** e **Permitir que os utilizadores para selecionar método**.
3. Selecione outras opções adicionais que gostaria de especificar para a sua organização.
4. Clique em **instalar adaptador do AD FS**.
<center>![Nuvem](./media/multi-factor-authentication-get-started-adfs-w2k12/server.png)</center>
5. Se for apresentada a janela do Active Directory, isso significa que duas coisas. O computador está associado a um domínio e, a configuração do Active Directory para proteger a comunicação entre o adaptador de AD FS e o serviço de autenticação Multifator é incompleta. Clique em **seguinte** para concluir esta configuração automaticamente ou selecione o **Ignorar a configuração automática do Active Directory e configurar as definições manualmente** caixa de verificação e, em seguida, clique em **seguinte**.
6. Se as janelas de Grupo Local for apresentada, isso significa que duas coisas. O computador não está associado a um domínio e, a configuração de grupo local para proteger a comunicação entre o adaptador de AD FS e o serviço de autenticação Multifator é incompleta. Clique em **seguinte** para concluir esta configuração automaticamente ou selecione o **Ignorar a configuração automática do Grupo Local e configurar as definições manualmente** caixa de verificação e, em seguida, clique em **seguinte**.
7. No Assistente de instalação, clique em **seguinte**. Servidor de autenticação Multifator Azure cria o grupo de administradores de PhoneFactor e adiciona a conta de serviço do AD FS ao grupo de administradores de PhoneFactor.
<center>![Nuvem](./media/multi-factor-authentication-get-started-adfs-w2k12/adapter.png)</center>
8. Na página **Iniciar o programa de instalação** , clique em **seguinte**.
9. No programa de instalação da placa de autenticação Multifator AD FS, clique em **seguinte**.
10. Clique em **Fechar** quando a instalação estar concluída.
11. Quando a placa de ter sido instalada, tem de registá-lo com o AD FS. Abra o Windows PowerShell e execute o seguinte comando:<br>
    `C:\Program Files\Multi-Factor Authentication Server\Register-MultiFactorAuthenticationAdfsAdapter.ps1`
   <center>![Nuvem](./media/multi-factor-authentication-get-started-adfs-w2k12/pshell.png)</center>
12. Para utilizar a placa recentemente registada, edite a política de autenticação global no AD FS. Na consola de gestão do AD FS, vá para o nó **Políticas de autenticação** . Na secção **Autenticação multifator** , clique em **Editar** ligação junto a secção **Definições globais** . Na janela **Editar política de autenticação Global** , selecione a **Autenticação Multifator** como um método de autenticação adicionais e, em seguida, clique em **OK**. O adaptador está registado como WindowsAzureMultiFactorAuthentication. Reinicie o serviço do AD FS para o registo entre em vigor.

<center>![Nuvem](./media/multi-factor-authentication-get-started-adfs-w2k12/global.png)</center>

Neste momento, servidor de autenticação Multifator está definido para ser um fornecedor de autenticação adicionais para utilizar com o AD FS.

## <a name="install-a-standalone-instance-of-the-ad-fs-adapter-by-using-the-web-service-sdk"></a>Instalar uma instância de autónomo da placa de AD FS utilizando o SDK do serviço Web
1. Instale o SDK do serviço Web no servidor que está a ser executado o servidor de autenticação Multifator.
2. Copie os seguintes ficheiros a partir do \Program Files\Multi-diretório de servidor de autenticação fator no servidor que planeia instalar o AD FS adaptador:
  - MultiFactorAuthenticationAdfsAdapterSetup64.msi
  - Register-MultiFactorAuthenticationAdfsAdapter.ps1
  - MultiFactorAuthenticationAdfsAdapter.ps1 anular o registo
  - MultiFactorAuthenticationAdfsAdapter.config
3. Execute o ficheiro de instalação MultiFactorAuthenticationAdfsAdapterSetup64.msi.
4. No programa de instalação de placa de autenticação Multifator AD FS, clique em **seguinte** para iniciar a instalação.
5. Clique em **Fechar** quando a instalação estar concluída.

## <a name="edit-the-multifactorauthenticationadfsadapterconfig-file"></a>Editar o ficheiro MultiFactorAuthenticationAdfsAdapter.config

Siga estes passos para editar o ficheiro MultiFactorAuthenticationAdfsAdapter.config:

1. Defina o nó **UseWebServiceSdk** **Verdadeiro**.  
2. Defina o valor para **WebServiceSdkUrl** para o URL do SDK de serviço de Web de autenticação Multifator. Por exemplo: *https://contoso.com/&lt;nome do certificado&gt;/MultiFactorAuthWebServicesSdk/PfWsSdk.asmx*, onde o nome do certificado é o nome do seu certificado.  
3. Editar o script Register MultiFactorAuthenticationAdfsAdapter.ps1 adicionando *- ConfigurationFilePath &lt;caminho&gt; * para o fim da `Register-AdfsAuthenticationProvider` comando, onde * &lt;caminho&gt; * é o caminho completo para o ficheiro MultiFactorAuthenticationAdfsAdapter.config.

### <a name="configure-the-web-service-sdk-with-a-username-and-password"></a>Configurar o SDK do serviço Web com um nome de utilizador e palavra-passe

Existem duas opções para configurar o SDK do serviço Web. O primeiro é com um nome de utilizador e palavra-passe, o segundo é com um certificado de cliente. Siga estes passos para a primeira opção ou avançar para o segundo.  

1. Defina o valor para **WebServiceSdkUsername** numa conta que é um membro do grupo de segurança PhoneFactor administradores. Utilizar o &lt;domínio&gt;& #92; &lt;nome de utilizador&gt; formatar.  
2. Defina o valor para **WebServiceSdkPassword** para a palavra-passe da conta apropriada.

### <a name="configure-the-web-service-sdk-with-a-client-certificate"></a>Configurar o SDK do serviço Web com um certificado de cliente

Se não quiser utilizar um nome de utilizador e palavra-passe, siga estes passos para configurar o SDK do serviço Web com um certificado de cliente.

1. Obter um certificado de cliente a partir de uma autoridade de certificação para o servidor que está a executar o SDK do serviço Web. Saiba como [obter certificados de cliente](https://technet.microsoft.com/library/cc770328.aspx).  
2. Importe o certificado de cliente para o arquivo de certificados pessoais computador local no servidor que está a executar o SDK do serviço Web. Certifique-se de que o certificado pública a autoridade de certificação está no arquivo de certificados de confiança de certificados de raiz.  
3. Exporte as chaves públicas e privadas do certificado do cliente para um ficheiro. pfx.  
4. Exporte a chave pública no formato Base64 para um ficheiro. cer.  
5. No Gestor de servidor, certifique-se de que a funcionalidade de autenticação de mapeamento de certificado de cliente do servidor Web (IIS) \Web Server\Security\IIS está instalada. Se não estiver instalado, selecione **Adicionar funções e funcionalidades** para adicionar esta funcionalidade.  
6. No Gestor de IIS, faça duplo clique em **Editor de configuração** no Web site que contém o directório virtual SDK de serviço Web. É importante para fazê-lo ao nível do Web site e não ao nível de directório virtual.  
7. Aceda à secção **system.webServer/security/authentication/iisClientCertificateMappingAuthentication** .  
8. Conjunto ativado para **true**.  
9. Defina oneToOneCertificateMappingsEnabled como **Verdadeiro**.  
10. Clique no botão **…** junto oneToOneMappings e, em seguida, clique na hiperligação **Adicionar** .  
11. Abra o ficheiro. cer de Base64 que exportou anterior. Remova *-começar certificado-*, *-Terminar certificado-*e todas as quebras de linha. Copie a cadeia resultante.  
12. Certificado de conjunto com a cadeia que copiou no passo anterior.  
13. Conjunto ativado para **true**.  
14. Defina o nome de utilizador para uma conta que é um membro do grupo de segurança PhoneFactor administradores. Utilizar o &lt;domínio&gt;& #92; &lt;nome de utilizador&gt; formatar.  
15. Definir a palavra-passe para a palavra-passe da conta adequada e, em seguida, feche o Editor de configuração.  
16. Clique na ligação de **Aplicar** .  
17. No diretório virtual SDK de serviço Web, faça duplo clique em **autenticação**.  
18. Certifique-se de que a representação do ASP.NET e a autenticação básica estão definidos para **ativado**, e que todos os outros itens estão definidos para **desativado**.  
19. No diretório virtual SDK de serviço Web, faça duplo clique em **Definições de SSL**.  
20. Defina os certificados de cliente para **Aceitar**e, em seguida, clique em **Aplicar**.  
21. Copie o ficheiro. pfx que exportou anterior para o servidor que está a executar o adaptador de AD FS.  
22. Importe o ficheiro. pfx para o arquivo de certificados pessoais computador local.  
23. Botão direito do rato e selecione **Gerir chaves privadas**e, em seguida, conceder acesso de leitura para a conta que utilizou para iniciar sessão para o serviço do AD FS.  
24. Abra o certificado de cliente e copie a impressão digital a partir do separador **Detalhes** .  
25. No ficheiro MultiFactorAuthenticationAdfsAdapter.config, defina **WebServiceSdkCertificateThumbprint** com a cadeia que copiou no passo anterior.  


Por fim, para registar o adaptador, execute o \Program Files\Multi-Factor autenticação Server\Register-MultiFactorAuthenticationAdfsAdapter.ps1 script no PowerShell. O adaptador está registado como WindowsAzureMultiFactorAuthentication. Reinicie o serviço do AD FS para o registo entre em vigor.

## <a name="related-topics"></a>Tópicos relacionados

Para resolver problemas relacionados, consulte as [FAQ de autenticação Multifator de Azure](multi-factor-authentication-faq.md)
