<properties
   pageTitle="Exemplo da aplicação para utilizar com segurança limite ambientes | Microsoft Azure"
   description="Implementar esta aplicação web simples depois de criar uma DMZ para testar cenários de fluxo de tráfego"
   services="virtual-network"
   documentationCenter="na"
   authors="tracsman"
   manager="rossort"
   editor=""/>

<tags
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/01/2016"
   ms.author="jonor"/>

# <a name="sample-application-for-use-with-security-boundary-environments"></a>Aplicação de exemplo para utilizar com segurança limite ambientes

[Regressar à página segurança limite melhor práticas][HOME]

Estes scripts de PowerShell podem ser executados localmente nos servidores IIS01 e AppVM01 para instalar e configurar uma aplicação web muito simples que apresenta uma página html do servidor de IIS01 front-end com o conteúdo do servidor de AppVM01 back-end.

Esta aplicação irá fornece um ambiente de testes simple para muitos dos exemplos DMZ e como alterações pontos finais, NSGs, UDR e Firewall regras podem efeito fluxos de tráfego.

## <a name="firewall-rule-to-allow-icmp"></a>Regra de firewall para permitir ICMP
Declaração simple de PowerShell pode ser executada em qualquer VM do Windows para permitir o tráfego ICMP (Ping). Isto permitirá para testar e resolução de problemas, permitindo que o protocolo ping mais fácil a passar a firewall do windows (para a maioria dos Linux distros que ICMP está ativada por predefinição).

    # Turn On ICMPv4
    New-NetFirewallRule -Name Allow_ICMPv4 -DisplayName "Allow ICMPv4" `
        -Protocol ICMPv4 -Enabled True -Profile Any -Action Allow

**Nota:** Se utilizar o abaixo scripts, esta adição de regra de firewall encontra-se a primeira instrução.

## <a name="iis01---web-application-installation-script"></a>IIS01 - Script de instalação de aplicação Web
Este script irá:

1.  Abrir IMCPv4 (Ping) na firewall do windows local server para testar a ligação mais fácil
2.  Instale o IIS e o .net Framework v4.5
3.  Criar uma página web do ASP.NET e um ficheiro de Web. config
4.  Alterar o conjunto de aplicacional predefinido para facilitar o acesso ao ficheiro
5.  Definir o utilizador anónimo para a sua conta de administrador e a palavra-passe

Este script do PowerShell deve ser executada localmente enquanto tinha RDP para IIS01.

    # IIS Server Post Build Config Script
    # Get Admin Account and Password
        Write-Host "Please enter the admin account information used to create this VM:" -ForegroundColor Cyan
        $theAdmin = Read-Host -Prompt "The Admin Account Name (no domain or machine name)"
        $thePassword = Read-Host -Prompt "The Admin Password"
        
    # Turn On ICMPv4
        Write-Host "Creating ICMP Rule in Windows Firewall" -ForegroundColor Cyan
        New-NetFirewallRule -Name Allow_ICMPv4 -DisplayName "Allow ICMPv4" -Protocol ICMPv4 -Enabled True -Profile Any -Action Allow
        
    # Install IIS
        Write-Host "Installing IIS and .Net 4.5, this can take some time, like 15+ minutes..." -ForegroundColor Cyan
        add-windowsfeature Web-Server, Web-WebServer, Web-Common-Http, Web-Default-Doc, Web-Dir-Browsing, Web-Http-Errors, Web-Static-Content, Web-Health, Web-Http-Logging, Web-Performance, Web-Stat-Compression, Web-Security, Web-Filtering, Web-App-Dev, Web-ISAPI-Ext, Web-ISAPI-Filter, Web-Net-Ext, Web-Net-Ext45, Web-Asp-Net45, Web-Mgmt-Tools, Web-Mgmt-Console
        
    # Create Web App Pages
        Write-Host "Creating Web page and Web.Config file" -ForegroundColor Cyan
        $MainPage = '<%@ Page Language="vb" AutoEventWireup="false" %>
        <%@ Import Namespace="System.IO" %>
        <script language="vb" runat="server">
            Protected Sub Page_Load(ByVal sender As Object, ByVal e As System.EventArgs) Handles Me.Load
                Dim FILENAME As String = "\\10.0.2.5\WebShare\Rand.txt"
                Dim objStreamReader As StreamReader
                objStreamReader = File.OpenText(FILENAME)
                Dim contents As String = objStreamReader.ReadToEnd()
                lblOutput.Text = contents
                objStreamReader.Close()
                lblTime.Text = Now()
            End Sub
        </script>
            
        <!DOCTYPE html>
        <html xmlns="http://www.w3.org/1999/xhtml">
        <head runat="server">
            <title>DMZ Example App</title>
        </head>
        <body style="font-family: Optima,Segoe,Segoe UI,Candara,Calibri,Arial,sans-serif;">
          <form id="frmMain" runat="server">
            <div>
              <h1>Looks like you made it!</h1>
              This is a page from the inside (a web server on a private network),<br />
              and it is making its way to the outside! (If you are viewing this from the internet)<br />
              <br />
              The following sections show:
              <ul style="margin-top: 0px;">
                <li> Local Server Time - Shows if this page is or isnt cached anywhere</li>
                <li> File Output - Shows that the web server is reaching AppVM01 on the backend subnet and successfully returning content</li>
                <li> Image from the Internet - Doesnt really show anything, but it made me happy to see this when the app worked</li>
              </ul>
              <div style="border: 2px solid #8AC007; border-radius: 25px; padding: 20px; margin: 10px; width: 650px;">
                <b>Local Web Server Time</b>: <asp:Label runat="server" ID="lblTime" /></div>
              <div style="border: 2px solid #8AC007; border-radius: 25px; padding: 20px; margin: 10px; width: 650px;">
                <b>File Output from AppVM01</b>: <asp:Label runat="server" ID="lblOutput" /></div>
              <div style="border: 2px solid #8AC007; border-radius: 25px; padding: 20px; margin: 10px; width: 650px;">
                <b>Image File Linked from the Internet</b>:<br />
                <br />
                <img src="http://sd.keepcalm-o-matic.co.uk/i/keep-calm-you-made-it-7.png" alt="You made it!" width="150" length="175"/></div>
            </div>
          </form>
        </body>
        </html>'
        
        $WebConfig ='<?xml version="1.0" encoding="utf-8"?>
        <configuration>
          <system.web>
            <compilation debug="true" strict="false" explicit="true" targetFramework="4.5" />
            <httpRuntime targetFramework="4.5" />
            <identity impersonate="true" />
            <customErrors mode="Off"/>
          </system.web>
          <system.webServer>
            <defaultDocument>
              <files>
                <add value="Home.aspx" />
              </files>
            </defaultDocument>
          </system.webServer>
        </configuration>'
            
        $MainPage | Out-File -FilePath "C:\inetpub\wwwroot\Home.aspx" -Encoding ascii
        $WebConfig | Out-File -FilePath "C:\inetpub\wwwroot\Web.config" -Encoding ascii
    
    # Set App Pool to Clasic Pipeline to remote file access will work easier
        Write-Host "Updaing IIS Settings" -ForegroundColor Cyan
        c:\windows\system32\inetsrv\appcmd.exe set app "Default Web Site/" /applicationPool:".NET v4.5 Classic"
        c:\windows\system32\inetsrv\appcmd.exe set config "Default Web Site/" /section:system.webServer/security/authentication/anonymousAuthentication /userName:$theAdmin /password:$thePassword /commit:apphost
        
    # Make sure the IIS settings take
        Write-Host "Restarting the W3SVC" -ForegroundColor Cyan
        Restart-Service -Name W3SVC
        
        Write-Host
        Write-Host "Web App Creation Successfull!" -ForegroundColor Green
        Write-Host


## <a name="appvm01---file-server-installation-script"></a>AppVM01 - Script de instalação de servidor do ficheiro
Este script configura o back-end para esta aplicação simple. Este script irá:

1.  Abrir IMCPv4 (Ping) na firewall para testar a ligação mais fácil
2.  Criar um novo directório
3.  Criar um ficheiro de texto para aceder a pela página web acima seja remotamente
4.  Definir permissões no directório e ao ficheiro para acesso anónimo para permitir o acesso
5.  Desativar a segurança avançada do IE para permitir que a navegação mais fácil deste servidor 

>[AZURE.IMPORTANT] **Melhores práticas**: desativar nunca segurança avançada do IE num servidor de produção, assim como é geralmente uma ideia incorretas para navegar na web a partir de um servidor de produção. Além disso, abrir o partilhas de ficheiros para o acesso anónimo é uma ideia incorretas, mas concluído aqui para simplificar.

Este script do PowerShell deve ser executada localmente enquanto tinha RDP para AppVM01. PowerShell é necessário para executar como administrador para garantir a execução com êxito.
    
    # AppVM01 Server Post Build Config Script
    # PowerShell must be run as Administrator for Net Share commands to work
    
    # Turn On ICMPv4
        New-NetFirewallRule -Name Allow_ICMPv4 -DisplayName "Allow ICMPv4" -Protocol ICMPv4 -Enabled True -Profile Any -Action Allow
    
    # Create Directory
        New-Item "C:\WebShare" -ItemType Directory
    
    # Write out Rand.txt
        $FileContent = "Hello, I'm the contents of a remote file on AppVM01."
        $FileContent | Out-File -FilePath "C:\WebShare\Rand.txt" -Encoding ascii
    
    # Set Permissions on share
        $Acl = Get-Acl "C:\WebShare"
        $AccessRule = New-Object system.security.accesscontrol.filesystemaccessrule("Everyone","ReadAndExecute, Synchronize","ContainerInherit, ObjectInherit","InheritOnly","Allow")
        $Acl.SetAccessRule($AccessRule)
        Set-Acl "C:\WebShare" $Acl
    
    # Create network share
        Net Share WebShare=C:\WebShare "/grant:Everyone,READ"
    
    # Turn Off IE Enhanced Security Configuration for Admins
        Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Active Setup\Installed Components\{A509B1A7-37EF-4b3f-8CFC-4F3A74704073}" -Name "IsInstalled" -Value 0
    
        Write-Host
        Write-Host "File Server Setup Successfull!" -ForegroundColor Green
        Write-Host
    

## <a name="dns01---dns-server-installation-script"></a>DNS01 - Script de instalação do servidor DNS
Não existe nenhum script incluído nesta aplicação de exemplo para configurar o servidor de DNS. Se a testar das regras de firewall, NSG ou UDR precisa de incluir o tráfego de DNS, o servidor de DNS01 terá de ser configurada manualmente. O ficheiro xml de configuração de rede para ambos os exemplos inclui DNS01 como o servidor DNS principal e o servidor DNS público alojado pelo nível 3, como o servidor DNS cópia de segurança. O servidor de nível 3 DNS seria real servidor DNS utilizado para o tráfego não local e com DNS01 não de configuração, sem DNS locais seria ocorrer.

<!--Link References-->
[HOME]: ../best-practices-network-security.md
