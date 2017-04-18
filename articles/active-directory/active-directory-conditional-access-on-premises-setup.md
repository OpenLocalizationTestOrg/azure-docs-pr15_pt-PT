<properties
    pageTitle="Configurar o acesso a condicional no local com o Azure Active Directory dispositivo registo | Microsoft Azure"
    description="Um guia passo a passo para permitir o acesso condicional às aplicações no local com o serviço de Federação do Active Directory (AD FS) no Windows Server 2012 R2."
    services="active-directory"
    documentationCenter=""
    authors="femila"
    manager="swadhwa"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="femila"/>


# <a name="setting-up-on-premises-conditional-access-using-azure-active-directory-device-registration"></a>Configurar o acesso a condicional no local com o Azure Active Directory dispositivo registo

Pessoalmente dispositivos propriedade, os utilizadores podem ser marcados conhecidos para a sua organização, exigindo que os utilizadores a associação do local de trabalho, os seus dispositivos para o serviço de registo de dispositivo do Azure Active Directory. Abaixo está um guia passo a passo para permitir o acesso condicional às aplicações no local com o serviço de Federação do Active Directory (AD FS) no Windows Server 2012 R2.

> [AZURE.NOTE]
> Licença do Office 365 ou licença do Azure AD Premium é necessária quando utilizar dispositivos registada no políticas de acesso condicional de serviço do Azure Active Directory dispositivo registo. Isto inclui políticas impostas pelos serviços de Federação do Active Directory (AD FS) para recursos no local.

Para mais informações sobre os cenários de acesso condicional para no local, consulte o artigo [participar à área de trabalho a partir de qualquer dispositivo para SSO e totalmente integrada segundo Factor autenticação através de aplicações da empresa](https://technet.microsoft.com/library/dn280945.aspx).

Estas capacidades estão disponíveis para clientes que comprar uma licença do Azure Active Directory Premium.

<a name="supported-devices"></a>Dispositivos suportados
-------------------------------------------------------------------------
* Windows 7 domínio associadas dispositivos.
* Windows 8.1 pessoal e domínio associadas dispositivos.
* iOS 6 e posterior, para o Safari browser
* Android 4.0 ou posterior, Samsung GS3 ou acima telemóveis, Samsung Note2 ou acima tablets.


<a name="scenario-prerequisites"></a>Pré-requisitos de cenário
------------------------------------------------------------------------
* Subscrição do Office 365 ou Premium do Azure Active Directory
* Um inquilino do Azure Active Directory
* Active Directory do Windows Server (Windows Server 2008 ou posterior)
* Esquema actualizado no Windows Server 2012 R2
* Licença para o Azure Active Directory Premium
* Windows Server 2012 R2 serviços de Federação, configurado para o SSO para Azure AD
* Windows Server 2012 R2 Web aplicação Proxy Microsoft Azure Active Directory ligar (ligação do Azure AD). [Transferir o Azure AD ligar-se aqui](http://www.microsoft.com/en-us/download/details.aspx?id=47594).
* Domínio verificado.

<a name="known-issues-in-this-release"></a>Problemas conhecidos nesta versão
-------------------------------------------------------------------------------
* Políticas de acesso condicional do dispositivo baseado necessitam dispositivo escrita-back de objeto do Active Directory a partir do Azure Active Directory. Pode demorar até 3 horas para objetos de dispositivo ser escritos-back ao Active Directory
* dispositivos iOS 7 sempre irão pedir ao utilizador para selecionar um certificado durante a autenticação de certificado de cliente.
* Algumas versões do iOS8, antes de iOS 8.3 não funcionam.

## <a name="scenario-assumptions"></a>Pressupostos do cenário
Este cenário assume que tem um ambiente híbrido que consiste um inquilino do Azure AD e um Active Directory no local. Estes inquilinos devem estar ligados utilizando a ligação do Azure AD e com um domínio verificado e o AD FS para SSO. A lista de verificação abaixo irá ajudá-lo a configurar o seu ambiente para a fase descrita acima.

<a name="checklist-prerequisites-for-conditional-access-scenario"></a>Lista de verificação: Pré-requisitos para o cenário acesso à condicional
--------------------------------------------------------------
Ligue o seu inquilino do Azure AD com o Active Directory no local.

## <a name="configure-azure-active-directory-device-registration-service"></a>Configurar o serviço de registo de dispositivo do Azure Active Directory
Utilize este guia para implementar e configurar o serviço de registo de dispositivo do Azure Active Directory para a sua organização.

Este guia assume que tenha configurado o Windows Server Active Directory e tiver subscrito ao Microsoft Azure Active Directory. Consulte o artigo pré-requisitos acima.

Para implementar o serviço de registo de dispositivo do Azure Active Directory com o seu inquilino do Azure Active Directory, conclua as tarefas na lista de verificação seguinte, ordem. Quando uma ligação de referência leva-o para um tópico conceptual, regresse a esta lista de verificação após rever o tópico conceptual de modo a que pode prosseguir com as tarefas restantes nesta lista de verificação. Algumas tarefas irão incluir um passo de validação de cenário que pode ajudá-lo a confirmar que o passo foi concluído com êxito.

## <a name="part-1-enable-azure-active-directory-device-registration"></a>Parte 1: Ativar Azure Active Directory dispositivo registo

Siga a lista de verificação abaixo para ativar e configurar o serviço registo do Azure Active Directory dispositivo.

| Tarefa                                                                                                                                                                                                                                                                                                                                                                                             | Referência                                                       |
|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------|
| Active o registo de dispositivo no inquilino do Azure Active Directory para permitir que os dispositivos aderir a área de trabalho. Por predefinição, a autenticação multifator não estiver ativada para o serviço. No entanto, autenticação multifator é recomendada quando registar um dispositivo. Antes de ativar a autenticação multifator no ADRS, certifique-se de que o AD FS está configurado para um fornecedor de autenticação multifator. | [Activar o registo de dispositivo do Azure Active Directory](active-directory-conditional-access-device-registration-overview.md)               |
| Dispositivos descobrir o seu serviço registo do Azure Active Directory dispositivo procurando pelo famoso registos DNS. Tem de configurar sua empresa DNS para que dispositivos podem descobrir o seu serviço registo do Azure Active Directory dispositivo.                                                                                                                                                   | [Configure a identificação de registo de dispositivo do Azure Active Directory.](active-directory-conditional-access-device-registration-overview.md) |

##<a name="part-2-deploy-and-configure-windows-server-2012-r2-active-directory-federation-services-and-set-up-a-federation-relationship-with-azure-ad"></a>Parte 2: Implementar e configurar o Windows Server 2012 R2 Federação serviços do Active Directory e configurar uma relação de federação com Azure AD

| Tarefa                                                                                                                                                                                                                                                                                                                                                                                             | Referência                                                       |
|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------|
| Implemente o domínio do Active Directory Domain Services com as extensões de esquema do Windows Server 2012 R2. Não necessita de atualizar qualquer um dos controladores de domínio para o Windows Server 2012 R2. A atualização do esquema é o único requisito. | [Atualizar o esquema de serviços de domínio do Active Directory](#upgrade-your-active-directory-domain-services-schema)               |
| Dispositivos descobrir o seu serviço registo do Azure Active Directory dispositivo procurando pelo famoso registos DNS. Tem de configurar sua empresa DNS para que dispositivos podem descobrir o seu serviço registo do Azure Active Directory dispositivo.                                                                                                                                                   | [Preparar os seus dispositivos de suporte do Active Directory](#prepare-your-active-directory-to-support-devices) |


##<a name="part-3-enable-device-writeback-in-azure-ad"></a>Parte 3: Ativar dispositivo escrita não consolidada no Azure AD

| Tarefa                                                                                                                                                                                                                                                                                                                                                                                             | Referência                                                       |
|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------|
| Conclua a parte 2 de escrita não consolidada na ligação do Azure AD Ativando dispositivo. Após a conclusão, regresse isto neste guia. | [Activar o dispositivo de escrita não consolidada na ligação do Azure AD](#upgrade-your-active-directory-domain-services-schema)               |


##<a name="optional-part-4-enable-multi-factor-authentication"></a>[Opcional] Parte 4: Activar a autenticação multifator

É vivamente recomendado que configure uma das várias opções para autenticação multifator. Se pretender exigir MFA, consulte o artigo [Escolher a solução de segurança multifator por si](../multi-factor-authentication/multi-factor-authentication-get-started.md). Inclui uma descrição de cada solução e ligações para ajudá-lo a configurar a solução da sua escolha.

## <a name="part-5-verification"></a>Parte 5: verificação

A implementação agora está concluída. Agora pode experimentar alguns cenários. Siga as ligações abaixo para experimentar o serviço e familiarizar-se com as funcionalidades


| Tarefa                                                                                                                                                                                                                         | Referência                                                                       |
|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------|
| Participar alguns dispositivos para o seu local de trabalho utilizando Azure Active Directory dispositivo registo. Pode participar, Windows, dispositivos iOS e Android                                                                                         | [Participar em dispositivos para o seu local de trabalho utilizando Azure Active Directory dispositivo registo](#join-devices-to-your-workplace-using-azure-active-directory-device-registration) |
| Pode ver e ativar/desativar registados dispositivos utilizando o Portal do administrador. Nesta tarefa irá ver alguns dispositivos registados utilizando o Portal do administrador.                                                        | [Descrição geral do registo de dispositivo Azure Active Directory](active-directory-conditional-access-device-registration-overview.md)                             |
| Certifique-se de que objectos de dispositivo são escritos novamente a partir do Azure Active Directory para Windows Server Active Directory.                                                                                                                  | [Verifique se dispositivos registados estão escritos-back ao Active Directory](#verify-registered-devices-are-written-back-to-active-directory)                  |
| Agora que os utilizadores podem registar os seus dispositivos, pode criar a aplicação de políticas de acesso no AD FS que permitem apenas registados dispositivos. Nesta tarefa, que irá criar uma regra de acesso de aplicação e um personalizado acesso negada mensagem | [Criar uma política de acesso de aplicação e personalizado acesso negado mensagem](#create-an-application-access-policy-and-custom-access-denied-message)            |



## <a name="integrate-azure-active-directory-with-on-premises-active-directory"></a>Integrar o Azure Active Directory com o Active Directory no local
Isto irá ajudá-lo integrar o seu inquilino do Azure AD com o seu no local active directory, utilizando a ligação do Azure AD. Apesar dos passos estão disponíveis no portal do Azure clássico, tome nota do quaisquer instruções especiais encontram listados nesta secção.

1.  Inicie sessão no portal do Azure clássica ao utilizar uma conta que seja um Administrador Global no Azure AD.
2.  No painel esquerdo, selecione **Do Active Directory**.
3.  No separador do **diretório** , selecione o seu diretório.
4.  Selecione o separador de **Integração de diretórios** .
5.  Na secção **Implementar e gerir** , siga os passos 1 a 3 para integrar o Azure Active Directory com o seu diretório no local.
  1.    Adicione domínios.
  2.    Instalar e executar a ligação do Azure AD: instalar o Azure AD Connect utilizando as seguintes instruções, [instalação personalizada de ligação do Azure AD](./connect/active-directory-aadconnect-get-started-custom.md).
  3. Verificar e gerir a sincronização de diretórios. As instruções de início de sessão únicos estão disponíveis dentro este passo.

  > [AZURE.NOTE]
  > Configure a Federação com o AD FS conforme descrito no documento associada acima. Não tem de configurar qualquer uma das funcionalidades de pré-visualização.


## <a name="upgrade-your-active-directory-domain-services-schema"></a>Actualizar o esquema do Active Directory Domain Services

> [AZURE.NOTE]
> Atualizar o esquema do Active Directory não pode ser revertido. É recomendado que pela primeira vez executar este num ambiente de teste.

1. Inicie sessão na sua controlador de domínio com uma conta que possua direitos de administrador da empresa e o administrador de esquema.
2. Copie o diretório de **\support\adprep [multimédia]** e subpastas diretórios para um dos controladores de domínio do Active Directory.
3. Onde [multimédia] é o caminho para o suporte de instalação do Windows Server 2012 R2.
4. A partir de uma linha de comandos, navegue para o directório adprep e executar: **adprep.exe /forestprep**. Siga as instruções no ecrã para concluir a atualização de esquema.

## <a name="prepare-your-active-directory-to-support-devices"></a>Preparar o seu Active Directory para suportar dispositivos

>[AZURE.NOTE] Esta é uma operação única que tem de executar para preparar o seu floresta do Active Directory para suportar dispositivos. Tem de ter sessão iniciada com permissões de administrador da empresa e floresta do Active Directory tem de ter o esquema do Windows Server 2012 R2 para concluir este procedimento.


##<a name="prepare-your-active-directory-forest-to-support-devices"></a>Preparar floresta do Active Directory para suportar dispositivos

> [AZURE.NOTE]
>Esta é uma operação única que tem de executar para preparar o seu floresta do Active Directory para suportar dispositivos. Tem de ter sessão iniciada com permissões de administrador da empresa e floresta do Active Directory tem de ter o esquema do Windows Server 2012 R2 para concluir este procedimento.

### <a name="prepare-your-active-directory-forest"></a>Preparar a floresta do Active Directory

1.  No seu servidor de Federação, abra uma janela de comando do Windows PowerShell e escrever: inicialização ADDeviceRegistration
2.  Quando lhe for pedido para **ServiceAccountName**, introduza o nome da conta de serviço selecionado como a conta de serviço do AD FS. Se for uma conta de gMSA, introduza a conta no formato de **domain\accountname$** . Para uma conta de domínio, utilize o formato **domain\accountname**.



### <a name="enable-device-authentication-in-ad-fs"></a>Ativar a autenticação do dispositivo no AD FS

1. No seu servidor de Federação, abra a consola de gestão do AD FS e navegue para **o AD FS** > **Políticas de autenticação**.
2. Selecione **Editar Global autenticação primário...** a partir do painel de **ações** .
3. Selecione **Ativar a autenticação do dispositivo** e, em seguida, clique**em OK**.
4. Por predefinição, o AD FS removerá periodicamente dispositivos não utilizados do Active Directory. Tem de desativar esta tarefa ao utilizar o Azure Active Directory dispositivo registo para que os dispositivos podem ser geridos no Azure.


### <a name="disable-unused-device-cleanup"></a>Desativar a limpeza do dispositivo não utilizadas
1. No seu servidor de Federação, abra uma janela de comando do Windows PowerShell e escrever: conjunto AdfsDeviceRegistration - MaximumInactiveDays 0

### <a name="prepare-azure-ad-connect-for-device-writeback"></a>Preparar a ligação do Azure AD para dispositivo repetição de escrita

1.  Concluir a parte 1: Preparar o Azure AD ligar.


## <a name="join-devices-to-your-workplace-using-azure-active-directory-device-registration"></a>Participar em dispositivos para o seu local de trabalho utilizando Azure Active Directory dispositivo registo

### <a name="join-an-ios-device-using-azure-active-directory-device-registration"></a>Participar num dispositivo iOS utilizando Azure Active Directory dispositivo registo

Azure Active Directory dispositivo registo utiliza o processo de inscrição Over-the-Air perfil para dispositivos iOS. Este processo começa com o utilizador ligar-se para o URL de inscrição do perfil através do browser Safari. O formato de URL é da seguinte forma:

    https://enterpriseregistration.windows.net/enrollmentserver/otaprofile/"yourdomainname"

Onde `yourdomainname` é o nome do domínio que configurou com o Azure Active Directory. Por exemplo, se o seu nome de domínio for contoso.com, o URL seria:

    https://enterpriseregistration.windows.net/enrollmentserver/otaprofile/contoso.com

Existem várias maneiras diferentes para comunicar este URL aos seus utilizadores. Uma forma recomendada é publicar este URL de um aplicação personalizados acesso negado mensagem no AD FS. Isto é abrangido na secção futura: [criar uma política de acesso de aplicação e personalizado acesso negado mensagem](#create-an-application-access-policy-and-custom-access-denied-message).

###<a name="join-a-windows-81-device-using-azure-active-directory-device-registration"></a>Aderir a um dispositivo de Windows 8.1 com o Azure Active Directory dispositivo registo

1. No seu dispositivo Windows 8.1, navegue para **Definições do PC** > **rede** > **local de trabalho**.
2. Introduza o seu nome de utilizador no formato UPN. Por exemplo, dan@contoso.com..
3. Selecione **aderir**.
4. Quando lhe for pedido, iniciar sessão com as suas credenciais. O dispositivo está agora associado.

###<a name="join-a-windows-7-device-using-azure-active-directory-device-registration"></a>Aderir a um dispositivo do Windows 7 com o Azure Active Directory dispositivo registo
Para registar o Windows 7 domínio associadas dispositivos que necessitam para implementar o pacote de software de registo do dispositivo. O pacote de software chama-se participar do local de trabalho para o Windows 7 e está disponível para transferência no [Web site do Microsoft ligar](https://connect.microsoft.com/site1164). Instruções de como utilizar o pacote estão disponíveis em [Configurar registo de dispositivo automático para o Windows 7 domínio associadas dispositivos](active-directory-conditional-access-automatic-device-registration-windows7.md).

### <a name="join-an-android-device-using-azure-active-directory-device-registration"></a>Aderir a um dispositivo Android com o Azure Active Directory dispositivo registo

O [Azure autenticador para o tópico Android](active-directory-conditional-access-azure-authenticator-app.md) tem instruções sobre como instalar a aplicação de autenticador Azure no seu dispositivo Android e adicionar uma conta do trabalho. Quando uma conta de trabalho é criada com êxito num dispositivo Android, que o dispositivo é o local de trabalho associada à organização.

## <a name="verify-registered-devices-are-written-back-to-active-directory"></a>Verifique se dispositivos registados estão escritos-back ao Active Directory
Pode ver e certifique-se de que os objetos de dispositivo tem sido escritos novamente para o seu Active Directory utilizando LDP.exe ou ADSI Edit. Ambos estão disponíveis com as ferramentas de administrador do Active Directory.

Por predefinição, os objetos de dispositivo que estão escritos-back a partir do Azure Active Directory serão colocados no mesmo domínio como o seu farm do AD FS.

    CN=RegisteredDevices,defaultNamingContext

## <a name="create-an-application-access-policy-and-custom-access-denied-message"></a>Criar uma política de acesso de aplicação e personalizado acesso negado mensagem
Considere o seguinte cenário: criar uma aplicação Relying festa fidedignidade no AD FS e configurar uma regra de autorização de emissão que permite que apenas os dispositivos registados. Agora, apenas os dispositivos que estão registados são permitidos para aceder à aplicação. Para tornar mais fácil para os seus utilizadores aceder à aplicação, configure uma personalizado mensagem de acesso negado que inclui instruções sobre como participar o seu dispositivo. Agora, os utilizadores ter uma forma totalmente integrada para registar os seus dispositivos para poder aceder a uma aplicação.

Os passos seguintes Mostrar como implementar o este cenário.

>[AZURE.NOTE]
Esta secção assume que já configurou uma festa confiar fidedignidade para a sua aplicação no AD FS.

1. Abrir a ferramenta de MMC do AD FS e navegue para o AD FS > relações de fidedignidade > confiar fidedignidades de terceiros.
2. Localize a aplicação para o qual será aplicada esta nova regra de acesso. A aplicação com o botão direito e selecione editar regras afirmação...
3. Selecione o separador **Regras de autorização de emissão** e, em seguida, selecione **Adicionar regra...**
4. **Regra de afirmação** modelo lista pendente, selecione **Permitir ou negar utilizadores com base uma afirmação de entrada**. Selecione **seguinte**.
5. No nome da regra afirmação: tipo de campo: **Permitir o acesso a partir de dispositivos registados**
6. Tipo de afirmação a partir de receção: lista pendente, selecione **Está registado o utilizador**.
7. De receção de afirmações de valor: de campo, escreva: **Verdadeiro**
8. Selecione o botão de opção **Permitir o acesso aos utilizadores com este afirmação de entrada** .
9. Selecione **Concluir** e, em seguida, selecione **Aplicar**.
10. Remova todas as regras que são mais permissivos que a regra que acabou de criar. Por exemplo, remova a regra de **Permitir o acesso a todos os utilizadores** predefinida.

A aplicação está agora configurada para permitir o acesso apenas quando o utilizador está a chegar a partir de um dispositivo que são registados e associadas à área de trabalho. Para as políticas de acesso mais avançado, consulte o artigo [Gerir o risco com o controlo de acesso Multifator](https://technet.microsoft.com/library/dn280949.aspx).

Em seguida, irá configurar uma mensagem de erro personalizadas para a sua aplicação. A mensagem de erro será informar os utilizadores que eles tem de aderir seu dispositivo à área de trabalho antes de serem autorizados acesso à aplicação. Pode criar um aplicação personalizada de acesso negado mensagem através do Windows PowerShell e HTML personalizados.

No seu servidor de Federação, abra uma janela do comando Windows PowerShell e escreva o seguinte comando. Substitua partes do comando itens que são específicos ao seu sistema:

    Set-AdfsRelyingPartyWebContent -Name "relying party trust name" -ErrorPageAuthorizationErrorMessage
Tem de registar o seu dispositivo antes de poder aceder esta aplicação.

**Se estiver a utilizar um dispositivo iOS, seleccione esta hiperligação para participar no seu dispositivo**:

    a href='https://enterpriseregistration.windows.net/enrollmentserver/otaprofile/yourdomain.com

Aderir a este dispositivo iOS para o seu local de trabalho.


**Se estiver a utilizar um dispositivo Windows 8.1**, pode associar o seu dispositivo ao aceder a **Definições do PC ** >  **rede** > **local de trabalho**.


Onde "**depender de nome de fidedignidade festa**" é o nome do objeto confiar fidedignidade festa aplicações no AD FS.
Onde **yourdomain.com** é o nome do domínio que configurou com o Azure Active Directory. Por exemplo, contoso.com.
Certifique-se de que remove todas as quebras de linha (se existir) do conteúdo html que passar para o cmdlet **Set-AdfsRelyingPartyWebContent** .


Agora quando os utilizadores acedem a aplicação a partir de um dispositivo que não está registado com o Azure Active Directory dispositivo serviço de registo, estes irão receber uma página com um aspeto semelhante ao ecrã captura abaixo.

![Screeshot de um erro quando os utilizadores ainda não registarem o seu dispositivo com Azure AD](./media/active-directory-conditional-access/error-azureDRS-device-not-registered.gif)

##<a name="related-articles"></a>Artigos relacionados

- [Índice de artigo para gestão de aplicações no Azure Active Directory](active-directory-apps-index.md)
