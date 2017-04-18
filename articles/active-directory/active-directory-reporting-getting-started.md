<properties
   pageTitle="Azure Active Directory criação de relatórios: Introdução | Microsoft Azure"
   description="Lista os vários relatórios disponíveis nos mapas do Azure Active Directory"
   services="active-directory"
   documentationCenter=""
   authors="dhanyahk"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="03/07/2016"
   ms.author="dhanyahk"/>

# <a name="getting-started-with-azure-active-directory-reporting"></a>Introdução ao Azure Active Directory elaboração de relatórios

## <a name="what-it-is"></a>O que é

Azure Active Directory (Azure AD) inclui segurança, atividade e relatórios de auditoria para o seu diretório. Aqui está uma lista dos relatórios incluído:

### <a name="security-reports"></a>Relatórios de segurança

- Inícios de sessão a partir de origens desconhecidas
- Inícios de sessão após várias falhas
- Inícios de sessão a partir de várias zonas geográficas
- Início de sessão-ins proveniente de endereços IP com atividade suspeita
- Atividade de início de sessão irregular
- Inícios de sessão a partir de dispositivos possivelmente infectados
- Utilizadores com atividade de início de sessão anómalo

### <a name="activity-reports"></a>Relatórios de atividade

- Utilização da aplicação: Resumo
- Utilização da aplicação: detalhadas
- Dashboard de aplicação
- Erros de aprovisionamento de conta
- Dispositivos de utilizador individual
- Atividade de utilizador individual
- Relatório de atividade de grupos
- Relatório de registo de atividade de reposição de palavra-passe
- Repor a palavra-passe de atividade

### <a name="audit-reports"></a>Relatórios de auditoria

- Relatório de auditoria de diretório

> [AZURE.TIP] Para obter mais documentação no Azure AD relatórios, consulte o artigo [Ver relatórios acesso e utilização](active-directory-view-access-usage-reports.md).



## <a name="how-it-works"></a>Como funciona


### <a name="reporting-pipeline"></a>Pipeline de elaboração de relatórios

Pipeline de elaboração de relatórios é constituído por três passos simples. Sempre que um utilizador inicia sessão ou é feita uma autenticação, acontece o seguinte:

- Em primeiro lugar, o utilizador é autenticado (com ou sem êxito) e o resultado está guardado nas bases de dados de serviço do Azure Active Directory.
- A intervalos regulares, sinal recentes todos os suplementos são processadas. Neste momento, os nossos segurança e algoritmos atividade discordantes estão a procurar início de sessão recentes todos os suplementos da atividade suspeita.
- Após a transformação, os relatórios estão escritos, em cache e servidos no portal do Azure clássico.

### <a name="report-generation-times"></a>Relatório vezes geração

Devido ao grande volume de autenticação e inicie sessão ins processadas pela plataforma do Azure AD, os mais recentes inícios de sessão processados são, em média, um horas de idade. Em casos raros, poderá demorar até 8 horas para os início de sessão-ins mais recentes do processo.

Pode encontrar o mais recente transformado iniciar sessão através da análise o texto de ajuda na parte superior de cada relatório.

![Texto de ajuda na parte superior de cada relatório](./media/active-directory-reporting-getting-started/reportingWatermark.PNG)

> [AZURE.TIP] Para obter mais documentação no Azure AD relatórios, consulte o artigo [Ver relatórios acesso e utilização](active-directory-view-access-usage-reports.md).



## <a name="getting-started"></a>Introdução


### <a name="sign-into-the-azure-classic-portal"></a>Inicie sessão no portal do Azure clássico

Em primeiro lugar, terá de iniciar sessão no [Azure portal clássica](https://manage.windowsazure.com) como administrador global ou administrador de conformidade. Também têm de ser um administrador do serviço de subscrição Azure ou administrador de cocriação ou estar a utilizar o "acesso ao Azure AD" Azure subscrição.

### <a name="navigate-to-reports"></a>Navegue para os relatórios

Para ver os relatórios, navegue até ao separador relatórios na parte superior do seu diretório.

Se esta for a primeira vez que visualização de relatórios, terá de concorda com uma caixa de diálogo antes de poder ver os relatórios. Isto é para garantir que são aceitável para administradores na sua organização para ver estes dados, que podem ser considerados informações privadas em alguns países/regiões.

![Caixa de diálogo](./media/active-directory-reporting-getting-started/dialogBox.png)

### <a name="explore-each-report"></a>Explorar a cada relatório

Navegue para cada relatório para ver os dados recolhidos a ser e os início de sessão-ins processados. Pode encontrar uma [lista de todos os relatórios aqui](active-directory-reporting-guide.md).

![Todos os relatórios](./media/active-directory-reporting-getting-started/reportsMain.png)

### <a name="download-the-reports-as-csv"></a>Transferir os relatórios como CSV

Cada relatório pode ser transferido como ficheiro CSV (valores separados por ponto e vírgula). Pode utilizar estes ficheiros no Excel, obter ou programas adicionais para analisar os seus dados de análise de terceiros.

Para transferir qualquer relatório como um CSV, navegue para o relatório e clique em "Transferir" na parte inferior.

![Botão de transferência](./media/active-directory-reporting-getting-started/downloadButton.png)

> [AZURE.TIP] Para obter mais documentação no Azure AD relatórios, consulte o artigo [Ver relatórios acesso e utilização](active-directory-view-access-usage-reports.md).





## <a name="next-steps"></a>Próximos passos

### <a name="customize-alerts-for-anomalous-sign-in-activity"></a>Personalizar os alertas para o início de sessão discordantes numa atividade

Navegue até ao separador "Configurar" do seu diretório.

Desloque-se para a secção "Notificações".

Ativar ou desativar a secção "Enviar por correio eletrónico notificações de início de sessão-ins discordantes".

![A secção de notificações](./media/active-directory-reporting-getting-started/notificationsSection.png)

### <a name="integrate-with-the-azure-ad-reporting-api"></a>Integrar com o Azure AD API de elaboração de relatórios

Consulte o artigo [introdução com a API elaboração de relatórios](active-directory-reporting-api-getting-started.md).

### <a name="engage-multi-factor-authentication-on-users"></a>Contrate autenticação Multifator utilizadores

Selecione um utilizador num relatório.

Clique no botão "Ativar MFA" na parte inferior do ecrã.

![O botão de autenticação Multifator na parte inferior do ecrã](./media/active-directory-reporting-getting-started/mfaButton.png)

> [AZURE.TIP] Para obter mais documentação no Azure AD relatórios, consulte o artigo [Ver relatórios acesso e utilização](active-directory-view-access-usage-reports.md).




## <a name="learn-more"></a>Saiba mais


### <a name="audit-events"></a>Eventos de auditoria

Saiba mais sobre os eventos que são auditados no diretório no [Azure Active Directory relatórios eventos de auditoria](active-directory-reporting-audit-events.md).

### <a name="api-integration"></a>Integração de API

Consulte o artigo [introdução com a API elaboração de relatórios](active-directory-reporting-api-getting-started.md) e a [documentação da referência API](https://msdn.microsoft.com/library/azure/mt126081.aspx).

### <a name="get-in-touch"></a>Contactar

Mensagem de correio electrónico [aadreportinghelp@microsoft.com](mailto:aadreportinghelp@microsoft.com) para comentários, ajuda ou quaisquer poderá ter de perguntas.

> [AZURE.TIP] Para obter mais documentação no Azure AD relatórios, consulte o artigo [Ver relatórios acesso e utilização](active-directory-view-access-usage-reports.md).
