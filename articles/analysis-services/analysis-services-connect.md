<properties
   pageTitle="Obter dados a partir do Azure Analysis Services | Microsoft Azure"
   description="Saiba como ligar e obter dados a partir de um servidor do Analysis Services no Azure."
   services="analysis-services"
   documentationCenter=""
   authors="minewiskan"
   manager="erikre"
   editor=""
   tags=""/>
<tags
   ms.service="analysis-services"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="na"
   ms.date="10/24/2016"
   ms.author="owend"/>

# <a name="get-data-from-azure-analysis-services"></a>Obter dados a partir do Azure Analysis Services
Depois de ter criado um servidor no Azure e implementado um modelo de tabela, os utilizadores na sua organização estão prontos ligar e iniciar a exploração de dados.

Azure Analysis Services suporta ligações de cliente com [atualizados modelos de objecto](#client-libraries); RUI, AMO, Adomd.Net ou MSOLAP, para ligar via xmla no servidor. Por exemplo, do Power BI, ambiente de trabalho do Power BI, Excel ou qualquer aplicação de cliente de terceiros que suporta os modelos de objeto.

## <a name="server-name"></a>Nome do servidor
Quando cria um servidor do Analysis Services no Azure, especifique um nome exclusivo e a região onde o servidor está a ser criada. Quando especificar o nome do servidor numa ligação, o esquema de nomes de servidor é:
```
<protocol>://<region>/<servername>
```
 Onde protocolo é cadeia **asazure**, a região é o Uri da região em que o servidor foi criado (por exemplo, para US Ocidental, westus.asazure.windows.net) e nomedoservidor é o nome do seu servidor exclusivo dentro de uma região.

## <a name="get-the-server-name"></a>Obter o nome do servidor
Antes de ligar, que precisa para obter o nome do servidor. No **Azure portal** > servidor > **Descrição geral** > **nome do servidor**, copie o nome de servidor completo. Se outros utilizadores na sua organização estiver a ligar a este servidor demasiado, irá querer partilhar este nome de servidor com elas. Quando especificar um nome de servidor, o caminho completo deve ser utilizado.

![Obter o nome do servidor no Azure](./media/analysis-services-deploy/aas-deploy-get-server-name.png)


## <a name="connect-in-power-bi-desktop"></a>Ligar-se no ambiente de trabalho do Power BI

> [AZURE.NOTE] Esta funcionalidade está pré-visualização.

1. No [Power BI Desktop](https://powerbi.microsoft.com/desktop/), clique em **Obter dados** > **bases de dados** > **Azure Analysis Services**.

2. No **servidor**, cole o nome do servidor da área de transferência.

3. Na **base de dados**, se souber o nome da base de dados do modelo de tabela ou perspetiva que pretende ligar, colá-lo aqui. Caso contrário, pode deixar este campo em branco. Pode selecionar uma base de dados ou perspetiva no ecrã seguinte.

4. Deixe a opção de **Ligar live** predefinido selecionado, em seguida, prima **Ligar**. Se lhe for pedido para introduzir uma conta, introduza a sua conta institucional.

5. No **navegador**, expanda o servidor, selecione o modelo ou perspetiva que pretende ligar e, em seguida clique em **Ligar**. Um único clique num modelo ou perspetiva mostra todos os objetos para essa vista.


## <a name="connect-in-power-bi"></a>Ligar-se no Power BI
1. Crie um ficheiro do Power BI Desktop que tem uma ligação dinâmica ao seu modelo no servidor.

2. No [Power BI](https://powerbi.microsoft.com), clique em **Obter dados** > **ficheiros**. Localize e selecione o ficheiro.


## <a name="connect-in-excel"></a>Ligar-se no Excel
Estabelecer ligação ao servidor do Azure Analysis Services no Excel é suportada utilizando obter dados no Excel 2016 ou Power Query em versões anteriores. É necessário o [fornecedor de MSOLAP.7](https://aka.ms/msolap) . Ligar utilizando o Assistente de importação de tabelas no Power Pivot não é suportada.

1. No Excel 2016, no friso **dados** , clique em **Obter dados externos** > **De outras origens** > **Do Analysis Services**.

2. No Assistente de ligação de dados, no **nome do servidor**, cole o nome do servidor da área de transferência. Em seguida, na **credenciais de início de sessão**, selecione **utilizar as seguintes nome de utilizador e palavra-passe**e, em seguida, escreva o nome de utilizador organizacional, por exemplo nancy@adventureworks.com, e palavra-passe.

    ![Ligar no início de sessão do Excel](./media/analysis-services-connect/aas-connect-excel-logon.png)

4. Em, **Selecione a base de dados e tabela**, selecione a base de dados e modelo ou perspetiva e, em seguida, clique em **Concluir**.

    ![Ligar no modelo de select do Excel](./media/analysis-services-connect/aas-connect-excel-select.png)

## <a name="connection-string"></a>Cadeia de ligação
Quando se liga ao Azure Analysis Services utilizando o modelo de objeto em tabela, utilize os seguintes formatos de cadeia de ligação:

###### <a name="integrated-azure-active-directory-authentication"></a>Autenticação integrada do Azure Active Directory
```
"Provider=MSOLAP;Data Source=<Azure AS instance name>;"
```
Autenticação integrada vai selecionar a cache de credenciais do Azure Active Directory, se disponível. Caso contrário, é apresentada a janela de início de sessão Azure.

###### <a name="azure-active-directory-authentication-with-username-and-password"></a>Autenticação do Active Directory Azure com o nome de utilizador e palavra-passe
```
"Provider=MSOLAP;Data Source=<Azure AS instance name>;User ID=<user name>;Password=<password>;Persist Security Info=True; Impersonation Level=Impersonate;";
```

## <a name="client-libraries"></a>Bibliotecas de cliente
Quando ligar para o Azure Analysis Services do Excel ou outras interfaces como RUI, AsCmd, ADOMD.NET, poderá ter de instalar as bibliotecas de cliente mais recentes do fornecedor. Obter as últimas novidades:  

[MSOLAP (amd64)](https://go.microsoft.com/fwlink/?linkid=829576)</br>
[MSOLAP (x86)](https://go.microsoft.com/fwlink/?linkid=829575)</br>
[AMO](https://go.microsoft.com/fwlink/?linkid=829578)</br>
[ADOMD](https://go.microsoft.com/fwlink/?linkid=829577)</br>



## <a name="next-steps"></a>Próximos passos
[Gerir o seu servidor](analysis-services-manage.md)
