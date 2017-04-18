<properties
   pageTitle="Genérico SQL conexão passo a passo | Microsoft Azure"
   description="Este artigo é observar um sistema de HR simples passo a passo utilizando o conector de SQL genérico."
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.workload="identity"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="08/30/2016"
   ms.author="billmath"/>

# <a name="generic-sql-connector-step-by-step"></a>Genérico de SQL de conexão, passo a passo
Este tópico é um guia passo a passo. Cria uma base de dados do exemplo simples HR e utilizá-la para importar alguns utilizadores e os respetivos membros do grupo.

## <a name="prepare-the-sample-database"></a>Preparar a base de dados de exemplo
Um servidor que executa o SQL Server, execute o script SQL que se encontram no [anexo A](#appendix-a). Este script cria uma base de dados de exemplo com o nome GSQLDEMO. O modelo de objeto para a base de dados criado aspeto nesta imagem:  
![Modelo de objeto](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\objectmodel.png)

Também crie um utilizador que pretende utilizar para ligar à base de dados. Neste tutorial, o utilizador é chamado FABRIKAM\SQLUser e localizado no domínio.

## <a name="create-the-odbc-connection-file"></a>Criar o ficheiro de ligação ODBC
O conector de SQL genérico está a utilizar para ligar para o servidor remoto ODBC. Em primeiro lugar precisamos de criar um ficheiro com as informações de ligação ODBC.

1. Inicie o utilitário de gestão de ODBC no servidor:  
![ODBC](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc.png)
2. Selecione o separador **DSN de ficheiro**. Clique em **Adicionar....**.
![ODBC1](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc1.png)
3. Works controlador de fora da caixa ajustar, por isso, selecione-o e clique em **Seguinte >**.  
![ODBC2](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc2.png)
4. Dê um nome, tal como **GenericSQL**ao ficheiro.  
![ODBC3](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc3.png)
5. Clique em **Concluir**.  
![ODBC4](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc4.png)
6. Data/hora para configurar a ligação. Atribua a origem de dados uma boa descrição e fornecer o nome do servidor a executar o SQL Server.  
![ODBC5](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc5.png)
7. Selecione como autenticar com SQL. Neste caso, utilizamos a autenticação do Windows.  
![ODBC6](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc6.png)
8. Fornece o nome da base de dados de exemplo, **GSQLDEMO**.  
![ODBC7](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc7.png)
9. Manter tudo predefinido neste ecrã. Clique em **Concluir**.  
![ODBC8](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc8.png)
10. Para verificar se que está tudo a funcionar como esperado, clique em **Testar origem de dados**.  
![ODBC9](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc9.png)
11. Certifique-se de que a teste for bem sucedido.  
![ODBC10](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc10.png)
12. O ficheiro de configuração de ODBC deverá agora estar visível no DSN ficheiro.  
![ODBC11](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\odbc11.png)

Vamos agora ter o ficheiro que precisa e pode começar a criar a conexão.

## <a name="create-the-generic-sql-connector"></a>Criar o conector SQL genérico

1. Na IU do Gestor de serviço de sincronização, selecione **Criar**e **conectores** . Selecione **SQL genérico (Microsoft)** e atribua um nome descritivo.  
![Connector1](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\connector1.png)
2. Localize o ficheiro DSN que criou na secção anterior e carregue-o para o servidor. Forneça as credenciais para ligar à base de dados.  
![Connector2](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\connector2.png)
3. Neste tutorial, é se tornou mais fácil para-nos e dizer que não existem dois tipos de objeto, o **utilizador** e **grupo**.
![Connector3](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\connector3.png)
4. Para localizar os atributos, queremos a conexão para detetar esses atributos verificando na própria tabela. Uma vez que **os utilizadores** é uma palavra reservada no SQL, é necessário fornecê-la entre parênteses Retos [].  
![Connector4](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\connector4.png)
5. Data/hora para definir o atributo âncora e o atributo DN. Para **os utilizadores**, vamos utilizar a combinação dos dois atributos nome de utilizador e o campo IDdeEmpregado. Para o **grupo**, utilizamos nome de grupo (não reais vida real, mas para este tutorial funciona).
![Connector5](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\connector5.png)
6. Nem todos os tipos de atributo podem ser detetados numa base de dados SQL. O tipo de referência do atributo não pode em particular. Para o tipo de objeto do grupo, precisamos de alterar o OwnerID e MemberID a referenciar.  
![Connector6](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\connector6.png)
7. Os atributos que podemos seleccionados, tal como atributos de referência no passo anterior requerem o tipo de objecto estes valores são uma referência a. No nosso caso, o tipo de objeto do utilizador.  
![Connector7](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\connector7.png)
8. Na página parâmetros globais, selecione a **marca d'água** como a estratégia delta. Também escreva o formato de data/hora **AAAA-MM-dd ss**.
![Connector8](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\connector8.png)
9. Na página **Configurar a partições e hierarquias** , selecione ambos os tipos de objeto.
![Connector9](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\connector9.png)
10. Em **Selecione tipos de objeto** e **Selecione atributos**, selecione tipos de objeto e todos os atributos. Na página **Configurar as âncoras** , clique em **Concluir**.

## <a name="create-run-profiles"></a>Criar perfis executar

1. Na IU do Gestor de serviço de sincronização, selecione **Configurar perfis de executar**e **conectores**. Clique em **novo perfil**. Vamos começar com **Importação completa**.  
![Runprofile1](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\runprofile1.png)
2. Selecione o tipo **(Apenas fase) importação completa**.  
![Runprofile2](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\runprofile2.png)
3. Selecione a partições **OBJETO = utilizador**.  
![Runprofile3](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\runprofile3.png)
4. Selecione a **tabela** e escreva **[USERS]**. Desloque para baixo para a secção de tipo de objeto de múltiplos valores e introduzir os dados tal como a seguinte imagem. Selecione **Concluir** para guardar o passo.
![Runprofile4a](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\runprofile4a.png)  
![Runprofile4b](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\runprofile4b.png)  
5. Selecione **novo passo**. Desta vez, selecione **OBJETO = grupo**. Na última página, utilize a configuração, tal como a seguinte imagem. Clique em **Concluir**.  
![Runprofile5a](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\runprofile5a.png)  
![Runprofile5b](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\runprofile5b.png)  
6. Opcional: Se pretender, pode configurar perfis de executar adicionais. Para este tutorial, é utilizada apenas a importação completo.
7. Clique em **OK** para terminar de alterar os perfis de executar.

## <a name="add-some-test-data-and-test-the-import"></a>Adicionar alguns dados de teste e testar a importação
Preencha alguns dados de teste na sua base de dados de exemplo. Quando estiver pronto, selecione **Executar** e **importação completa**.

Eis um utilizador com dois números de telefone e um grupo com alguns dos membros.  
![cs1](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\cs1.png)  
![CS2](.\media\active-directory-aadconnectsync-connector-genericsql-step-by-step\cs2.png)  

## <a name="appendix-a"></a>Anexo A
**Script SQL para criar a base de dados de exemplo**

```SQL
---Creating the Database---------
Create Database GSQLDEMO
Go
-------Using the Database-----------
Use [GSQLDEMO]
Go
-------------------------------------
USE [GSQLDEMO]
GO
/****** Object:  Table [dbo].[GroupMembers]   ******/
SET ANSI_NULLS ON
GO
SET QUOTED_IDENTIFIER ON
GO
CREATE TABLE [dbo].[GroupMembers](
    [MemberID] [int] NOT NULL,
    [Group_ID] [int] NOT NULL
) ON [PRIMARY]

GO
/****** Object:  Table [dbo].[GROUPS]   ******/
SET ANSI_NULLS ON
GO
SET QUOTED_IDENTIFIER ON
GO
CREATE TABLE [dbo].[GROUPS](
    [GroupID] [int] NOT NULL,
    [GROUPNAME] [nvarchar](200) NOT NULL,
    [DESCRIPTION] [nvarchar](200) NULL,
    [WATERMARK] [datetime] NULL,
    [OwnerID] [int] NULL,
PRIMARY KEY CLUSTERED
(
    [GroupID] ASC
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON) ON [PRIMARY]
) ON [PRIMARY]

GO
/****** Object:  Table [dbo].[USERPHONE]   ******/
SET ANSI_NULLS ON
GO
SET QUOTED_IDENTIFIER ON
GO
SET ANSI_PADDING ON
GO
CREATE TABLE [dbo].[USERPHONE](
    [USER_ID] [int] NULL,
    [Phone] [varchar](20) NULL
) ON [PRIMARY]

GO
SET ANSI_PADDING OFF
GO
/****** Object:  Table [dbo].[USERS]   ******/
SET ANSI_NULLS ON
GO
SET QUOTED_IDENTIFIER ON
GO
CREATE TABLE [dbo].[USERS](
    [USERID] [int] NOT NULL,
    [USERNAME] [nvarchar](200) NOT NULL,
    [FirstName] [nvarchar](100) NULL,
    [LastName] [nvarchar](100) NULL,
    [DisplayName] [nvarchar](100) NULL,
    [ACCOUNTDISABLED] [bit] NULL,
    [EMPLOYEEID] [int] NOT NULL,
    [WATERMARK] [datetime] NULL,
PRIMARY KEY CLUSTERED
(
    [USERID] ASC
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, IGNORE_DUP_KEY = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON) ON [PRIMARY]
) ON [PRIMARY]

GO
ALTER TABLE [dbo].[GroupMembers]  WITH CHECK ADD  CONSTRAINT [FK_GroupMembers_GROUPS] FOREIGN KEY([Group_ID])
REFERENCES [dbo].[GROUPS] ([GroupID])
GO
ALTER TABLE [dbo].[GroupMembers] CHECK CONSTRAINT [FK_GroupMembers_GROUPS]
GO
ALTER TABLE [dbo].[GroupMembers]  WITH CHECK ADD  CONSTRAINT [FK_GroupMembers_USERS] FOREIGN KEY([MemberID])
REFERENCES [dbo].[USERS] ([USERID])
GO
ALTER TABLE [dbo].[GroupMembers] CHECK CONSTRAINT [FK_GroupMembers_USERS]
GO
ALTER TABLE [dbo].[GROUPS]  WITH CHECK ADD  CONSTRAINT [FK_GROUPS_USERS] FOREIGN KEY([OwnerID])
REFERENCES [dbo].[USERS] ([USERID])
GO
ALTER TABLE [dbo].[GROUPS] CHECK CONSTRAINT [FK_GROUPS_USERS]
GO
ALTER TABLE [dbo].[USERPHONE]  WITH CHECK ADD  CONSTRAINT [FK_USERPHONE_USER] FOREIGN KEY([USER_ID])
REFERENCES [dbo].[USERS] ([USERID])
GO
ALTER TABLE [dbo].[USERPHONE] CHECK CONSTRAINT [FK_USERPHONE_USER]
GO
```
