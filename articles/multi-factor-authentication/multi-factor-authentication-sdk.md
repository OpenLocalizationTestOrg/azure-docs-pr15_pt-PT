<properties 
    pageTitle="Integrar a suas identidades no local com o Azure Active Directory."
    description="Esta é o ligação do Azure AD que descreve o que é e por que razão que vai utilizar."
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="curtand"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/04/2016"
    ms.author="kgremban"/>

# <a name="building-multi-factor-authentication-into-custom-apps-sdk"></a>Autenticação Multifator de modular para aplicações personalizadas (SDK)

> [AZURE.IMPORTANT]  Se pretender transferir o SDK, terá de criar um fornecedor de autenticação Multifator Azure, mesmo se tiver Azure MFA, AAD Premium ou do licenças EMS.  Se criar um fornecedor de autenticação Multifator Azure para esse efeito e já tem licenças, é necessário para criar o fornecedor com o modelo **Por utilizadores** e ligar o fornecedor do directório que contém as licenças Azure MFA, Azure AD Premium ou do EMS.  Isto irá garantir que não são faturada exceto se tiver mais utilizadores exclusivos utilizando o SDK que o número de licenças que é o proprietário.

O Azure Multifator autenticação Software Development Kit (SDK) permite-lhe criar chamada telefónica e texto mensagem verificação diretamente para os início de sessão no ou da transação processos de aplicações no seu inquilino do Azure AD.

O SDK de autenticação Multifator está disponível para c#, Visual Basic (.NET), Java, Perl, PHP e rubi. O SDK fornece um dispositivo de moldagem fino à volta de autenticação multifator. Inclui tudo o que precisa para escrever o seu código, incluindo ficheiros de código fonte remova, ficheiros de exemplo e um ficheiro Leia-me detalhado. Cada SDK também inclui um certificado e chave privada para encriptar as transações que seja exclusivo para o seu fornecedor de autenticação Multifator. Desde tenha um fornecedor, pode transferir o SDK no tantas idiomas e formatos que precisar.

A estrutura das APIs no SDK autenticação Multifator é bastante simple. Tornar uma função única a chamada para uma API com os parâmetros de opção multifator, como o modo de verificação e dados de utilizador, como o número de telefone para ligar a ou o número PIN para validar. As APIs traduzem a chamada de função para pedidos de serviço web para o serviço baseado na nuvem Azure Multifator autenticação. Todas as chamadas tem de incluir uma referência para o certificado privada que vem incluído no cada SDK.

Uma vez que as APIs não têm acesso aos utilizadores registado no Azure Active Directory, tem de fornecer informações do utilizador, tais como números de telefone e códigos PIN, um ficheiro ou uma base de dados. Além disso, as APIs não fornecem funcionalidades de gestão de inscrição ou utilizador, pelo que necessita para criar estes processos para a aplicação.






## <a name="download-the-azure-multi-factor-authentication-sdk"></a>Transferir a autenticação Multifator Azure SDK

Transferir o SDK do Azure Multifator necessita de um [Fornecedor do Azure Multifator Auth](multi-factor-authentication-get-started-auth-provider.md).  Isto requer uma subscrição Azure completo, mesmo se licenças Azure MFA, Azure AD Premium ou do conjunto de aplicações do Enterprise mobilidade que pertencem.  Para transferir o SDK, terá de navegar até ao Portal de gestão do Multifator gerindo o fornecedor de autenticação Multifator diretamente ou ao clicar na ligação **"Aceda ao portal do"** na página de definições de serviço do MFA.


### <a name="to-download-the-azure-multi-factor-authentication-sdk-from-the-azure-portal"></a>Para transferir o SDK de autenticação Multifator Azure a partir do portal do Azure


1. Inicie sessão Portal do Azure como administrador.
2. No lado esquerdo, selecione do Active Directory.
3. Na página do Active Directory, na parte superior, clique **Fornecedores de autenticação Multifator**
4. Na parte inferior, clique em **Gerir**
5. Isto irá abrir uma nova página.  À esquerda, na parte inferior, clique em SDK.
<center>![Transferir](./media/multi-factor-authentication-sdk/download.png)</center>
6. Selecione o idioma que pretende e clique das ligações de transferência associado.
7. Guarde a transferência.



### <a name="to-download-the-azure-multi-factor-authentication-sdk-via-the-service-settings"></a>Para transferir o SDK de autenticação Multifator Azure através das definições do serviço


1. Inicie sessão Portal do Azure como administrador.
2. No lado esquerdo, selecione do Active Directory.
3. Faça duplo clique na sua instância do Azure AD.
4. Na parte superior, clique em **Configurar**
5. Em autenticação multifator selecione **Gerir definições de serviço**
![transferir](./media/multi-factor-authentication-sdk/download2.png)
6. Na página de definições dos serviços, na parte inferior do ecrã, clique em **Ir para o portal**.
![Transferir](./media/multi-factor-authentication-sdk/download3a.png)
7. Isto irá abrir uma nova página.  À esquerda, na parte inferior, clique em SDK.
8. Selecione o idioma que pretende e clique das ligações de transferência associado.
9. Guarde a transferência.

## <a name="contents-of-the-azure-multi-factor-authentication-sdk"></a>Conteúdos da autenticação Multifator Azure SDK
Dentro do SDK irá encontrar os itens seguintes:

- **Leia-me**. Explica como utilizar a API de autenticação Multifator numa aplicação do nova ou existente.
- **Ficheiro de origem (s)** para autenticação Multifator
- **Certificado de cliente** que utilizar para comunicar com o serviço de autenticação Multifator
- **Chave privada** para o certificado
- **Ligue os resultados.** Uma lista de códigos de resultado de chamada. Para abrir este ficheiro, utilize uma aplicação com formatação de texto, tais como WordPad. Utilize os códigos de resultado de chamada para testar e resolver problemas de implementação de autenticação Multifator na sua aplicação. Não são códigos de autenticação de estado.
- **Exemplos.** Código de exemplo para uma implementação do trabalho básico de autenticação Multifator.


>[AZURE.WARNING]O certificado de cliente é um certificado privado exclusivo que foi gerado especialmente para si. Não partilhar ou perder este ficheiro. É a chave para garantir a segurança das suas comunicações com o serviço de autenticação Multifator.

## <a name="code-sample-standard-mode-phone-verification"></a>Exemplo de código: Verificação de telefone de modo padrão

Este exemplo de código mostra-lhe como utilizar as APIs no Azure Multifator autenticação SDK para adicionar a verificação de chamada de voz do modo padrão para a sua aplicação. Modo padrão é uma chamada telefónica que o utilizador responde ao premir a tecla #.

Este exemplo utiliza o c# .NET 2.0 Multifator autenticação SDK numa aplicação do ASP.NET básica com c#: lógica lado do servidor, mas o processo é muito semelhante para implementações simples noutros idiomas. Uma vez que o SDK inclui ficheiros de origem, ficheiros não executáveis, pode criar os ficheiros e referência-los ou incluí-las diretamente na sua aplicação.

>[AZURE.NOTE]Quando implementar a autenticação Multifator, utilize os fatores adicionais como verificação secundária ou superior para completar o seu método de autenticação principal. Estes métodos não foram concebidos para ser utilizado como métodos de autenticação principal.

### <a name="code-sample-overview"></a>Descrição geral de exemplo de código
Este código de exemplo para uma aplicação de demonstração de web muito simples utiliza uma chamada telefónica com uma resposta chave # para concluir a autenticação do utilizador. Este factor chamada telefónica é conhecido no autenticação Multifator como modo padrão.

O código do lado do cliente não inclui qualquer elementos específicos da autenticação Multifator. Porque os fatores de autenticação adicionais são independentemente da autenticação principal, pode adicioná-los sem alterar a interface de início de sessão existente. APIs no SDK Multifator permitem-lhe personalizar a experiência do utilizador, mas não poderá ter de alterar alguma coisa de todo.

O código do lado do servidor adiciona a autenticação de modo padrão no passo 2. Que cria um objeto de PfAuthParams com os parâmetros que são necessários para verificação modo padrão: nome de utilizador, número e o modo e o caminho para o certificado de cliente (CertFilePath), é necessário em cada chamada por telefone. Para uma demonstração de todos os parâmetros na PfAuthParams, consulte o ficheiro de exemplo no SDK.

Em seguida, o código transmite o objeto PfAuthParams para a função de pf_authenticate(). O valor de retorno indica o sucesso ou o fracasso da autenticação. Os parâmetros de saída, callStatus e errorID, contêm informações de resultado de chamada adicionais. Os códigos de resultado de chamada encontram-se documentados no ficheiro de resultados de chamada no SDK.

Esta implementação mínima pode ser escrita numa apenas alguns linhas. No entanto, no código de produção, teria de incluir mais sofisticado processamento de erros, código de base de dados adicionais e uma experiência de utilizador melhorado.

### <a name="web-client-code"></a>Código de cliente da Web

Segue-se código do cliente de web para uma página de demonstração.


    <%@ Page Language="C#" AutoEventWireup="true" CodeFile="Default.aspx.cs" Inherits="_Default" %>

    <!DOCTYPE html>

    <html xmlns="http://www.w3.org/1999/xhtml">
    <head runat="server">
    <title>Multi-Factor Authentication Demo</title>
    </head>
    <body>
    <h1>Azure Multi-Factor Authentication Demo</h1>
    <form id="form1" runat="server">

    <div style="width:auto; float:left">
    Username:&nbsp;<br />
    Password:&nbsp;<br />
    </div>

    <div">
    <asp:TextBox id="username" runat="server" width="100px"/><br />
    <asp:Textbox id="password" runat="server" width="100px" TextMode="password" /><br />
    </div>

    <asp:Button id="btnSubmit" runat="server" Text="Log in" onClick="btnSubmit_Click"/>

    <p><asp:Label ID="lblResult" runat="server"></asp:Label></p>

    </form>
    </body>
    </html>


### <a name="server-side-code"></a>Código do lado do servidor

O seguinte código do lado do servidor, autenticação Multifator é configurada e executar no passo 2. Modo padrão (MODE_STANDARD) é uma chamada telefónica ao qual o utilizador responde ao premir a tecla #.

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Web;
    using System.Web.UI;
    using System.Web.UI.WebControls;

    public partial class _Default : System.Web.UI.Page
    {
        protected void Page_Load(object sender, EventArgs e)
        {
        }

        protected void btnSubmit_Click(object sender, EventArgs e)
        {
            // Step 1: Validate the username and password
            if (username.Text != "Contoso" || password.Text != "password")
            {
                lblResult.ForeColor = System.Drawing.Color.Red;
                lblResult.Text = "Username or password incorrect.";
            }
            else
            {
                // Step 2: Perform multi-factor authentication

                // Add call details from the user database.
                PfAuthParams pfAuthParams = new PfAuthParams();
                pfAuthParams.Username = username.Text;
                pfAuthParams.Phone = "9134884271";
                pfAuthParams.Mode = pf_auth.MODE_STANDARD;

                // Specify a client certificate
                // NOTE: This file contains the private key for the client
                // certificate. It must be stored with appropriate file
                // permissions.
                pfAuthParams.CertFilePath = "c:\\cert_key.p12";

                // Perform phone-based authentication
                int callStatus;
                int errorId;

                if(pf_auth.pf_authenticate(pfAuthParams, out callStatus, out errorId))
                {
                    lblResult.ForeColor = System.Drawing.Color.Green;
                    lblResult.Text = "Multi-Factor Authentication succeeded.";
                }
                else
                {
                    lblResult.ForeColor = System.Drawing.Color.Red;
                    lblResult.Text = " Multi-Factor Authentication failed.";
                }
            }

        }
    }
