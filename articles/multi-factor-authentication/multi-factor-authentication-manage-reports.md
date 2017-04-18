<properties
    pageTitle="Relatórios de autenticação Multifator Azure"
    description="Este exemplo descreve como utilizar a funcionalidade de autenticação Multifator de Azure - relatórios."
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

# <a name="reports-in-azure-multi-factor-authentication"></a>Relatórios no Azure autenticação Multifator

Autenticação Multifator Azure fornece vários relatórios que podem ser utilizados pelo utilizador e a sua organização. Estes relatórios podem ser acedidos através do Portal de gestão de autenticação Multifator, solicitar que tem uma licença do Azure MFA fornecedor, ou uma Azure MFA, Azure AD Premium ou conjunto de mobilidade Enterprise. Segue-se uma lista dos relatórios disponíveis.

Pode aceder a relatórios através do portal de gestão do Azure.

Nome| Descrição
:------------- | :------------- |
Utilização | Utilização de relatórios apresentar informações sobre a utilização geral, o utilizador resumo e detalhes do utilizador.
Estado do servidor|Este relatório apresenta o estado dos servidores de autenticação Multifator associada à sua conta.
Histórico de utilizador bloqueados|Estes relatórios mostram o histórico dos pedidos de bloquear ou desbloquear utilizadores.
Histórico de utilizador ignorada|Mostra o histórico de pedidos de ignorar a autenticação Multifator para o número de telefone de um utilizador.
Alerta de fraude|Mostra um histórico das fraude alertas apresentados durante o intervalo de datas que especificou.
Em fila de espera|Relatórios de listas em fila de espera para processamento e o respetivo estado. Uma ligação para transferir ou visualizar o relatório é fornecida quando o relatório estiver concluído.

## <a name="to-view-reports"></a>Para ver os relatórios

1.  Inicie sessão no http://azure.microsoft.com
2.  No lado esquerdo, selecione do Active Directory.
3.  Selecione uma das seguintes opções:
    - **Opção 1**: clique no separador de fornecedores de autenticação Multifator. Selecione o seu fornecedor MFA e clique no botão Gerir na parte inferior.
    - **Opção 2**: selecione o seu diretório e clique no separador Configurar. Na secção autenticação multifator, selecione gerir definições de serviço. Na parte inferior da página Definições do serviço MFA, clique em Ir para a ligação portal.
4.  No Portal de gestão do Azure Multifator autenticação, verá a vista de uma secção de relatório no painel de navegação esquerdo. A partir daqui pode selecionar os relatórios descritos acima.

<center>![Nuvem](./media/multi-factor-authentication-manage-reports/report.png)</center>


**Recursos adicionais**

* [Para utilizadores](./end-user/multi-factor-authentication-end-user.md)
* [Autenticação Multifator Azure no MSDN](https://msdn.microsoft.com/library/azure/dn249471.aspx)
