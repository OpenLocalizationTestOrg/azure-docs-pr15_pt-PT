<properties
   pageTitle="Gerir Azure Analysis Services | Microsoft Azure"
   description="Saiba como gerir um servidor do Analysis Services no Azure."
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

# <a name="manage-analysis-services"></a>Gerir do Analysis Services

Depois de criar um servidor do Analysis Services no Azure, poderão existir algumas tarefas de administração e gestão que necessitar de efetuar imediato ou algures poderiam. Por exemplo, executar o processamento para controlar quem pode aceder a modelos no servidor ou monitorizar o estado de funcionamento do seu servidor de atualizar dados. Algumas tarefas de gestão só podem ser executadas no portal Azure, as outras pessoas no SQL Server Management Studio (SSMS), e algumas tarefas podem ser feitas quer.

## <a name="azure-portal"></a>Portal do Azure
O [Azure portal](http://portal.azure.com/) é onde pode criar e eliminar os servidores, monitorizar recursos do servidor, alterar o tamanho e gerir a quem tem acesso aos seus servidores.  Se estiver a ter alguns problemas, também pode submeter um pedido de suporte.

![Obter o nome do servidor no Azure](./media/analysis-services-manage/aas-manage-portal.png)

## <a name="sql-server-management-studio"></a>SQL Server Management Studio
Ligar ao seu servidor no Azure é tal como ligar a uma instância de servidor na sua organização. SSMS, pode efetuar muitas das mesmas tarefas como dados de processo ou criar um script de transformação, gerir funções e utilizar o PowerShell.

![SQL Server Management Studio](./media/analysis-services-manage/aas-manage-ssms.png)

 Uma das diferenças maiores é a autenticação que utilizar para ligar ao servidor. Para ligar ao seu servidor de Azure Analysis Services, é necessário selecionar **A autenticação de palavra-passe do Active Directory**.

### <a name="to-connect-with-ssms"></a>Para se ligar a SSMS
1. Antes de ligar, que precisa para obter o nome do servidor. No **Azure portal** > servidor > **Descrição geral** > **nome do servidor**, copie o nome do servidor.

    ![Obter o nome do servidor no Azure](./media/analysis-services-deploy/aas-deploy-get-server-name.png)

2. No SSMS > **Explorador de objectos**, clique em **Ligar** > **Analysis Services**.

3. Na caixa de diálogo **ligar ao servidor** , cole no nome do servidor, em seguida, em **autenticação**, selecione um dos seguintes procedimentos:

    **Autenticação integrado do active Directory** para utilizar o início de sessão único com o Active Directory para Federação do Active Directory Azure.

    **Autenticação de palavra-passe do active Directory** para utilizar uma conta institucional. Por exemplo, quando ligar a partir de um domínio que não sejam associarem ao computador.

    Nota: Se não vir a autenticação do Active Directory, poderá ter de [Ativar a autenticação do Azure Active Directory](#enable-azure-active-directory-authentication) no SSMS.

    ![Ligar no SSMS](./media/analysis-services-manage/aas-manage-connect-ssms.png)

Uma vez que a gerir o seu servidor no Azure utilizando SSMS é muito semelhante ao gerir um servidor no local, iremos não para iniciar o detalhes aqui. Toda a ajuda é necessário pode encontrar na [Gestão de instância do Analysis dos serviços](https://msdn.microsoft.com/library/hh230806.aspx) no MSDN.

## <a name="server-administrators"></a>Administradores de servidor
Pode utilizar **Os administradores do Analysis Services** no pá controlo do seu servidor no Azure portal ou SSMS para gerir os administradores de servidor. Os administradores do Analysis Services são os administradores de servidor de base de dados com direitos para tarefas comuns de administração de base de dados como adicionar e remover as bases de dados e gerir os utilizadores. Por predefinição, o utilizador que cria o servidor no Azure portal automaticamente é adicionado como um administrador de serviços de análise.

Também deve saber:

-   Windows Live ID não é um tipo de identidade suportados para o Azure Analysis Services.  
-   Os administradores do Analysis Services têm de ser válidos utilizadores do Azure Active Directory.
-   Se criar um servidor do Azure Analysis Services através do Gestor de recursos do Azure modelos, os administradores do Analysis Services leva-o até uma matriz JSON de utilizadores que deve ser adicionado como administradores.

Os administradores do Analysis Services podem ser diferentes dos administradores do Azure recurso, que podem gerir os recursos para subscrições do Azure. Isto mantém a compatibilidade com XMLA e TSML existentes gerir comportamentos no Analysis Services e para lhe permitir segregar direitos entre a gestão de recursos Azure e análise dos serviços de gestão de base de dados.

Para ver todas as funções e aceder aos tipos de para o seu recurso de Azure Analysis Services, utilize o controlo de acesso (IAM) em pá o controlo.

## <a name="database-users"></a>Utilizadores de base de dados
Utilizadores de base de dados de modelo do Azure Analysis Services tem de ser o Azure Active Directory. Liste especificada para a base de dados de modelo tem de ser através do endereço de e-mail organizacional ou UPN. Este é diferente a partir de bases de dados do modelo no local que suportam a utilizadores por liste de domínio do Windows.

Pode adicionar utilizadores ao utilizar as [atribuições de funções no Azure Active Directory](../active-directory/role-based-access-control-configure.md) ou ao utilizar [Tabular modelo linguagem de scripts](https://msdn.microsoft.com/library/mt614797.aspx) (TMSL) no SQL Server Management Studio.

**Exemplo de script TMSL**

```
{
  "createOrReplace": {
    "object": {
      "database": "SalesBI",
      "role": "Users"
    },
    "role": {
      "name": "Users",
      "description": "All allowed users to query the model",
      "modelPermission": "read",
      "members": [
        {
          "memberName": "user1@contoso.com",
          "identityProvider": "AzureAD"
        },
        {
          "memberName": "group1@contoso.com",
          "identityProvider": "AzureAD"
        }
      ]
    }
  }
}
```

## <a name="enable-azure-active-directory-authentication"></a>Ativar a autenticação do Azure Active Directory
Para ativar a funcionalidade de autenticação do Azure Active Directory para SSMS no registo, crie um ficheiro de texto chamado EnableAAD.reg, em seguida, copie e cole o seguinte:


```
Windows Registry Editor Version 5.00
[HKEY_CURRENT_USER\Software\Microsoft\Microsoft SQL Server\Microsoft Analysis Services\Settings]
"AS AAD Enabled"="True"
```

Guardar e, em seguida, execute o ficheiro.



## <a name="next-steps"></a>Próximos passos
Se ainda não o tiver já implementou um modelo de tabela ao seu servidor de novo, é agora uma boa altura. Para saber mais, consulte o artigo [Implementar ao Azure Analysis Services](analysis-services-deploy.md).

Caso tenha implementado um modelo para o seu servidor, está pronto para se ligar à mesma, utilizando um cliente ou no browser. Para saber mais, consulte o artigo [obter dados a partir do servidor do Azure Analysis Services](analysis-services-connect.md).
