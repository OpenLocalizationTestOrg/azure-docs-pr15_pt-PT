<properties
   pageTitle="SSMS suporte para o Azure AD MFA com base de dados SQL e armazém de dados SQL | Microsoft Azure"
   description="Utilize a autenticação estimadas são aplicadas Multi com SSMS para base de dados SQL e armazém de dados SQL."
   services="sql-database"
   documentationCenter=""
   authors="BYHAM"
   manager="jhubbard"
   editor=""
   tags=""/>

<tags
   ms.service="sql-database"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="data-management"
   ms.date="10/04/2016"
   ms.author="rick.byham@microsoft.com"/>

# <a name="ssms-support-for-azure-ad-mfa-with-sql-database-and-sql-data-warehouse"></a>Suporte SSMS para Azure AD MFA com base de dados SQL e armazém de dados SQL

Base de dados SQL Azure e de armazém de dados do SQL Azure passará a suportar ligações a partir do SQL Server Management Studio (SSMS) utilizando a *Autenticação Universal do Active Directory*. Autenticação Universal do Active Directory é um fluxo de trabalho interativos que suporta *Azure a autenticação Multifator* (MFA). Azure MFA ajuda-o a salvaguardar informações o acesso a dados e as aplicações enquanto o utilizador a procura de um processo de início de sessão simples da reunião. -Oferece uma autenticação forte com um intervalo de opções de verificação fácil — chamada telefónica, mensagem de texto, cartões de smart card com o pin ou notificação de aplicação móvel — permitindo que os utilizadores escolher o método preferirem. Para obter uma descrição de autenticação Multifator, consulte o artigo [Autenticação Multifator](../multi-factor-authentication/multi-factor-authentication.md).

Agora, suporta SSMS:

- Interactivo MFA com Azure AD com potencial para validação de caixa de diálogo de pop-up.
- Não interativo Active Directory palavra-passe e autenticação integrado do Active Directory métodos que podem ser utilizados em muitas aplicações diferentes (ADO.NET, JDBC, ODBC, etc.). Estes dois métodos nunca resultam em caixas de diálogo de pop-up.

Quando a conta de utilizador está configurada para MFA do fluxo de trabalho a autenticação interativos requer interação utilizador adicionais através de caixas de diálogo de pop-up, utilizar cartão inteligente, etc. Quando a conta de utilizador está configurada para MFA, o utilizador tem de selecionar Azure autenticação Universal para ligar. Se a conta de utilizador não requer MFA, o utilizador ainda pode utilizar as outras duas opções de autenticação do Azure Active Directory.

## <a name="universal-authentication-limitations-for-sql-database-and-sql-data-warehouse"></a>Limitações de autenticação universais para base de dados SQL e armazém de dados SQL

- SSMS é a única ferramenta atualmente permissão para MFA através de autenticação Universal do Active Directory.
- Uma única conta Azure Active Directory pode iniciar sessão para uma instância da SSMS utilizando a autenticação do Universal. Para iniciar sessão como outra conta do Azure AD, tem de utilizar outra instância do SSMS. (Esta restrição está limitada a autenticação Universal do Active Directory; que pode iniciar sessão para servidores diferentes utilizando a autenticação de palavra-passe do Active Directory, autenticação integrado do Active Directory ou autenticação do SQL Server).
- SSMS suporta autenticação Universal do Active Directory para visualização Explorador de objectos, Editor de consultas e o arquivo de consulta.
- Nem DacFx nem o estruturador de esquema suportar uma autenticação Universal.
- Contas MSA não são suportadas para autenticação Universal do Active Directory.
- Autenticação Universal do Active Directory não é suportada no SSMS para utilizadores que são importados para o Active Directory atual a partir de outros directórios ativo Azure. Estes utilizadores não são suportados, uma vez que seria necessário um ID de inquilino validar as contas e, sem mecanismos para fornecer que não existe.
- Não existem requisitos de software adicional para autenticação Universal do Active Directory, exceto os tem de utilizar uma versão suportada do SSMS.

## <a name="configuration-steps"></a>Passos de configuração

Implementar a autenticação Multifator requer quatro passos básicos.

1. **Configurar um Azure Active Directory** – para mais informações, consulte o artigo [integrar a suas identidades no local com o Azure Active Directory](../active-directory/active-directory-aadconnect.md), [Adicionar o seu nome de domínio para Azure AD](https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/), [Microsoft Azure agora suporta a Federação com o Active Directory do Windows Server](https://azure.microsoft.com/blog/2012/11/28/windows-azure-now-supports-federation-with-windows-server-active-directory/), [administrar no diretório da sua Azure AD](https://msdn.microsoft.com/library/azure/hh967611.aspx)e [Gerir Azure AD através do Windows PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx).

2. **Configurar MFA** – para instruções passo a passo, consulte o artigo [Configurar a autenticação Multifator Azure](../multi-factor-authentication/multi-factor-authentication-whats-next.md). 

3. **Configurar base de dados do SQL ou armazém de dados do SQL Azure AD autenticação por** – para obter instruções passo a passo, consulte o artigo [ligar a base de dados SQL ou SQL dados armazém ao utilizar o Azure autenticação do Active Directory](sql-database-aad-authentication.md).

4. **Transferir SSMS** – no computador cliente, transfira o mais recente SSMS (pelo menos Agosto de 2016), a partir de [Transferir o SQL Server Management Studio (SSMS)](https://msdn.microsoft.com/library/mt238290.aspx).

## <a name="connecting-by-using-universal-authentication-with-ssms"></a>Ligar utilizando a autenticação Universal com SSMS

Os seguintes passos mostram como se ligar à base de dados SQL ou armazém de dados SQL utilizando o SSMS mais recente.

1. Para ligar utilizando a autenticação do Universal, na caixa de diálogo **ligar ao servidor** , selecione **Autenticação Universal do Active Directory**.
![ligar-1mfa universal][1]

2. Como habitualmente para base de dados SQL e armazém de dados SQL tem clique em **Opções** e especificar a base de dados na caixa de diálogo de **Opções** . Em seguida, clique em **Ligar**.
3. Quando for apresentada a caixa de diálogo **Iniciar sessão na sua conta** , forneça a conta e a palavra-passe da sua identidade do Azure Active Directory.
![2mfa-iniciar sessão][2]

    > [AZURE.NOTE] Para a autenticação Universal com uma conta que não requer MFA, ligue o neste momento. Para utilizadores que necessitem MFA, continue com os seguintes passos.
 
4. Duas caixas de diálogo de configuração MFA poderão aparecer. Desta vez operação depende o administrador MFA definição e, por isso, poderá estar opcional. Para um domínio MFA ativado este passo, por vezes, é predefinido (por exemplo, o domínio, os utilizadores utilizar um smart card e pin).  
![configuração 3mfa][3]

5. Possíveis segundo caixa de diálogo permite-lhe selecionar os detalhes do seu método de autenticação de uma vez. As opções possíveis são configuradas pelo seu administrador.
![4mfa-verificar-1][4]
 
6. O Azure Active Directory envia as informações de confirmação para si. Quando recebe o código de verificação, introduza-a na caixa **Introduza o código de verificação** e clique em **Iniciar sessão**.
![5mfa-verificar-2][5]

Quando verificação estiver concluída, SSMS liga-se normalmente partindo do princípio de credenciais válidas e o acesso de firewall.

##<a name="next-steps"></a>Próximos passos  

Conceder acesso a sua base de dados a outras pessoas: [autenticação de base de dados do SQL e autorização: conceder acesso](sql-database-manage-logins.md)  
Certifique-se de outras pessoas podem ligar-se através da firewall: [Configurar uma regra de firewall de nível do servidor de base de dados do SQL Azure através do portal Azure](sql-database-configure-firewall-settings.md)


[1]: ./media/sql-database-ssms-mfa-auth/1mfa-universal-connect.png
[2]: ./media/sql-database-ssms-mfa-auth/2mfa-sign-in.png
[3]: ./media/sql-database-ssms-mfa-auth/3mfa-setup.png
[4]: ./media/sql-database-ssms-mfa-auth/4mfa-verify-1.png
[5]: ./media/sql-database-ssms-mfa-auth/5mfa-verify-2.png

