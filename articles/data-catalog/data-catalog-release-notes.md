<properties
   pageTitle="Notas de lançamento do catálogo de dados Azure | Microsoft Azure"
   description="Notas de lançamento do catálogo de dados do Azure."
   services="data-catalog"
   documentationCenter=""
   authors="steelanddata"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="09/21/2016"
   ms.author="maroche"/>

# <a name="azure-data-catalog-release-notes"></a>Notas de lançamento do Azure catálogo de dados

## <a name="notes-for-the-november-20-2015-release-of-azure-data-catalog"></a>Notas de 20 de Novembro de 2015 da edição de catálogo de dados do Azure

### <a name="opening-data-sources-in-power-bi-desktop"></a>Origens de dados de abertura no ambiente de trabalho do Power BI

Ao utilizar a opção "Abrir no Power BI Desktop" a partir do portal de **Catálogo de dados do Azure** , poderão encontrar utilizadores um de dois problemas na aplicação de ambiente de trabalho do Power BI:

- É apresentada uma caixa de diálogo com o título "Não é possível para abrir documento"
- A aplicação de ambiente de trabalho do Power BI é aberta, mas o ficheiro parece estar vazio

Para cada situação, o problema pode ser resolvido ao transferir e instalar a versão mais recente do Power BI Desktop a partir de [PowerBI.com](https://powerbi.com).

## <a name="notes-for-the-november-13-2015-release-of-azure-data-catalog"></a>Notas de 13 de Novembro de 2015 da edição de catálogo de dados do Azure

### <a name="registering-and-connecting-to-teradata"></a>Registar e estabelecer ligação ao Teradata

Ao ligar a origens de dados de Teradata utilizadores tem de ter instalado o controlador de Teradata ODBC correcto que correspondem ao número de bits (32 bits ou 64 bits) do software a ser utilizado.

Relativamente a essa data de lançamento ADC, o mais recente [do controlador ODBC do Teradata para windows (versão 15.10)](http://downloads.teradata.com/download/connectivity/odbc-driver/windows) é compatível com o Office 2013, mas não com o Office 2016.

## <a name="notes-for-the-july-13-2015-release-of-azure-data-catalog"></a>Notas de 13 de Julho de 2015 da edição de catálogo de dados do Azure

### <a name="registering-and-connecting-to-oracle-database"></a>Registar e ligar à base de dados Oracle

Ao ligar a origens de dados de base de dados Oracle utilizadores tem de ter instalado os controladores Oracle correctos que correspondem ao número de bits (32 bits ou 64 bits) do software a ser utilizado.

-   Quando registar origens de dados Oracle num computador a executar o Windows de 32 bits, serão utilizados os controladores do Oracle de 32 bits
-   Quando registar origens de dados Oracle num computador a executar o Windows de 64 bits, serão utilizados os controladores do Oracle de 64 bits
-   Ao ligar a origens de dados Oracle utilizando o Excel num computador a executar a versão de 32 bits do Microsoft Office, incluindo no Windows de 64 bits, os controladores do Oracle de 32 bits serão utilizados
-   Ao ligar a origens de dados Oracle utilizando o Excel num computador a executar a versão de 64 bits do Microsoft Office, serão utilizados os controladores do Oracle de 64 bits

### <a name="registering-and-connecting-to-sql-server-reporting-services"></a>Registar e estabelecer ligação ao SQL Server Reporting Services

Suporte de origens de dados do SQL Server Reporting Services (SSRS) estão atualmente limitado aos servidores do modo nativo apenas. Suporte para SSRS no modo do SharePoint será adicionado com uma versão posterior.

### <a name="opening-data-assets-in-excel"></a>Elementos de dados de abrir no Excel

Ao abrir elementos de dados no Microsoft Excel a partir do portal de **Catálogo de dados do Azure** , os utilizadores podem ser-lhe pedidos com uma caixa de diálogo de **Aviso de segurança do Microsoft Excel** . Este é padrão, os utilizadores e comportamento esperado podem selecionar **Ativar** para continuar.

Para obter mais informações, consulte [Ativar ou desativar alertas de segurança sobre ligações e ficheiros de Web sites suspeitos](https://support.office.com/article/Enable-or-disable-security-alerts-about-links-and-files-from-suspicious-websites-A1AC6AE9-5C4A-4EB3-B3F8-143336039BBE).

### <a name="proxy-and-policy-configuration-and-data-source-registration"></a>Configuração do proxy e a política e os dados de origem de registo

Os utilizadores podem encontrar uma situação na qual pode iniciar sessão no portal do catálogo de dados do Azure, mas quando tentarem iniciar sessão para a ferramenta de registo de origem de dados que encontram uma mensagem de erro impede que iniciar sessão.

Existem duas causas possíveis para este comportamento de problema:

**1 causa: a configuração de serviços de Federação do Active Directory** A ferramenta de registo da origem de dados utiliza a autenticação de formulários para validar os inícios de sessão do utilizador contra do Active Directory. Para o início de sessão efetuada com êxito, deve ser ativada autenticação de formulários na política de autenticação Global por um administrador do Active Directory.

Em algumas situações, este comportamento de erro poderá ocorrer apenas quando o utilizador está na rede da empresa, ou apenas quando o utilizador está a ligar a partir de fora da rede da empresa. A política de autenticação Global permite métodos de autenticação para ser activada separadamente para intranet e ligações extranet. Podem ocorrer erros de início de sessão se autenticação de formulários não estiver ativada para a rede a partir do qual o utilizador está a ligar.

Para obter mais informações, consulte [Configurar políticas de autenticação](https://technet.microsoft.com/library/dn486781.aspx).

**Causa 2: configuração do proxy de rede** Se a rede da empresa utiliza um servidor proxy, a ferramenta de registo poderá não conseguir ligar para o Azure Active Directory através de um proxy. Os utilizadores podem garantir que a ferramenta de registo ao editar o ficheiro de configuração da ferramenta, adicionar nesta secção para o ficheiro:


      <system.net>
        <defaultProxy useDefaultCredentials="true" enabled="true">
          <proxy usesystemdefault="True"
                          proxyaddress="http://<your corporate network proxy url>"
                          bypassonlocal="True"/>
        </defaultProxy>
      </system.net>


Para localizar o ficheiro de RegistrationTool.exe.config, iniciação a ferramenta de registo e, em seguida, abra o utilitário de Gestor de tarefas do Windows. No separador Detalhes no Gestor de tarefas, com o botão direito no RegistrationTool.exe e selecione Abrir localização do ficheiro a partir do menu de pop-up.
