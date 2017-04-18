
<properties
    pageTitle="Utilizar o Azure AD ligar-se do Estado de funcionamento com o AD FS | Microsoft Azure"
    description="Esta é a página do Azure AD ligar-se do Estado de funcionamento como monitorizar as suas instalações infraestrutura do AD FS."
    services="active-directory"
    documentationCenter=""
    authors="karavar"
    manager="samueld"
    editor="curtand"/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/18/2016"
    ms.author="vakarand"/>

# <a name="using-azure-ad-connect-health-with-ad-fs"></a>Utilizar o Azure AD ligar-se do Estado de funcionamento com o AD FS
A seguinte documentação é específica a sua infraestrutura de AD FS com Azure AD ligar-se do Estado de funcionamento de monitorização. Para obter informações sobre a ligação do Azure AD (sincronização) com o Azure AD ligar-se do Estado de funcionamento de monitorização, consulte o artigo [Utilizar o Azure AD ligar-se do Estado de funcionamento para a sincronização](active-directory-aadconnect-health-sync.md). Para além disso, para obter informações sobre como monitorizar o Active Directory Domain Services com o Azure AD ligar-se do Estado de funcionamento, consulte o artigo [Utilizar o Azure AD ligar-se do Estado de funcionamento com o AD DS](active-directory-aadconnect-health-adds.md).

## <a name="alerts-for-ad-fs"></a>Alertas do AD FS
A secção de alertas de estado de funcionamento ligar Azure AD fornece a lista de alertas ativas. Cada alerta inclui informações relevantes, os passos de resolução e ligações para documentação relacionada.

Pode fazer duplo clique um alerta ativo ou resolvido, para abrir um novo pá com informações adicionais, passos que pode seguir para resolver o alerta e ligações para a documentação relevante. Também pode ver dados histórico sobre alertas que foram resolvidos no passado.

![Portal de estado de funcionamento de ligação do Azure AD](./media/active-directory-aadconnect-health/alert2.png)



## <a name="usage-analytics-for-ad-fs"></a>Análise de utilização do AD FS
Azure AD ligar-se do Estado de funcionamento a análise de utilização analisa o tráfego de autenticação dos seus servidores de Federação. Pode fazer duplo clique da caixa de análise de utilização, para abrir pá de análise a utilização, que mostra-lhe várias métricas e agrupamentos.

>[AZURE.NOTE] Para utilizar a análise de utilização com o AD FS, tem de garantir que o AD FS auditoria está ativado. Para mais informações, consulte o artigo [Ativar auditoria do AD FS](active-directory-aadconnect-health-agent-install.md#enable-auditing-for-ad-fs).

![Portal de estado de funcionamento de ligação do Azure AD](./media/active-directory-aadconnect-health/report1.png)

Para selecionar métricas adicionais, especifique um intervalo de tempo ou para alterar o agrupamento, o botão direito do rato no gráfico de análise de utilização e selecione editar gráfico. Em seguida, pode especificar o intervalo de tempo, selecione uma métrica diferente e alterar o agrupamento. Pode ver a distribuição do tráfego de autenticação com base nas diferentes "métricas" e agrupar cada métrica utilizando parâmetros relevantes "Agrupar por" descritos na tabela seguinte:

| Métrica | Agrupar por | Significa que o agrupamento e por que motivo é útil? |
| ------ | -------- | -------------------------------------------- |
| Os pedidos de total: O número total de pedidos processados pelo serviço de Federação | Todos os | Apresenta a contagem do número total de pedidos sem agrupamento. |
|  | Aplicação | Agrupa os pedidos de total com base na parte dependente alvo. Este agrupamento é útil para compreender a aplicação que está a receber quanto percentagem do total tráfego. |
|  | Servidor | Agrupa os pedidos de total com base no servidor que processadas o pedido. Este agrupamento é útil para compreender a distribuição de carga do tráfego total. |
|  | Associação do local de trabalho | Grupos o total de pedidos com base em se chegar a partir de dispositivos que estão local de trabalho associada (conhecidas). Este agrupamento é útil para compreender se os recursos são acedidos utilizando os dispositivos que são desconhecidos a infraestrutura de identidade. |
|  | Método de autenticação | Agrupa os pedidos de total com base no método de autenticação utilizado para autenticação. Este agrupamento é útil para compreender o método de autenticação comuns que é utilizado para autenticação. Seguem-se os métodos de autenticação possíveis <ol> <li>Autenticação integrada do Windows (Windows)</li> <li>Formulários baseados autenticação (formulários)</li> <li>SSO (Single Sign-On)</li> <li>(Certificado) a autenticação de certificado X509</li> <br>Se os servidores de Federação recebem o pedido com um Cookie SSO, esse pedido é contado como SSO (Single Sign On). Nestes casos, se o cookie é válido, o utilizador não é pedido para fornecer credenciais e obtém acesso totalmente integrado à aplicação. Este comportamento é comum se tiver várias partes dependente protegidas por servidores de Federação. |
|  | Localização de rede | Agrupa os pedidos de total com base na localização de rede do utilizador. Pode ser qualquer um dos intranet ou extranet. Este agrupamento é útil para saber qual a percentagem do tráfego da intranet ou extranet. |
| Total de pedidos de falhou: O número total Ocorreu uma falha pedidos processados pelo serviço de Federação. <br> (Esta métrica só está disponível no AD FS para o Windows Server 2012 R2)| Tipo de erro | Mostra o número de erros baseados nos tipos de erro predefinida. Este agrupamento é útil para compreender os tipos de erros comuns. <ul><li>Incorretos nome de utilizador ou palavra-passe: erros devido a nome de utilizador incorreto ou palavra-passe.</li> <li>"Bloqueio extranet": falhas devido as pedidos recebidos de um utilizador que foi bloqueado a partir da extranet </li><li> "Palavra-passe expirou": falhas devido a utilizadores que iniciem sessão com uma palavra-passe expirada.</li><li>"Desactivado conta": falhas devido a utilizadores que iniciem sessão com uma conta desativada.</li><li>"Autenticação do dispositivo": falhas devido a utilizadores a falhar autenticar utilizando a autenticação do dispositivo.</li><li>"Autenticação de certificado de utilizador": falhas devido a utilizadores a falhar autenticar devido a um certificado inválido.</li><li>"MFA": falhas devido ao utilizador a falhar autenticar utilizando a autenticação Multifator.</li><li>"Outras credencial": "Autorização de emissão": falhas devido a falhas de autorização.</li><li>"Emissão delegação": falhas devido a erros de delegação de emissão.</li><li>"Token aceitação": falhas devido ADFS rejeitar o token a partir de um fornecedor de identidade de terceiros.</li><li>"Protocolo": falha devido a erros de protocolo.</li><li>"Desconhecida": capturar todas as. Quaisquer outras falhas de que não se ajustam as categorias que definiu.</li> |
|  | Servidor | Grupos de erros com base no servidor. Este agrupamento é útil para compreender a distribuição de erro por servidores. Distribuição desigual poderão ser um indicador em forma de um servidor num Estado cablagem. |
|  | Localização de rede | Grupos de erros com base na localização de rede dos pedidos (vs intranet da extranet). Este agrupamento é útil para compreender o tipo de pedidos de que está a falhar. |
|  | Aplicação | Agrupa falhas com base na aplicação de destino (dependente intervenientes). Este agrupamento é útil para compreender a aplicação de destino está a ver número mais de erros. |
| Contagem de utilizador: o número médio de utilizadores exclusivos activos no sistema | Todos os | Esta métrica fornece uma contagem do número médio de utilizadores através do serviço de Federação no setor tempo selecionado. Os utilizadores não estiverem agrupados. <br>A média depende do setor o tempo selecionado. |
|  | Aplicação | Agrupa o número médio de utilizadores com base na aplicação de destino (dependente intervenientes). Este agrupamento é útil para compreender o número de utilizadores estiver a utilizar a aplicação. |


## <a name="performance-monitoring-for-ad-fs"></a>Monitorizar o desempenho de AD FS
Azure AD ligar-se do Estado de funcionamento monitorização de desempenho fornece informações de monitorização métricas. Selecionar a caixa de monitorização, é aberta uma nova pá com informações detalhadas sobre as métricas.


![Portal de estado de funcionamento de ligação do Azure AD](./media/active-directory-aadconnect-health/perf1.png)


Ao selecionar a opção de filtro no topo da pá, pode filtrar pelo servidor para ver métricas de um servidor individual. Para alterar métricas, com o botão direito no gráfico monitorização em pá a monitorização e selecione editar gráfico. Em seguida, a partir do novo pá disponibilizado, pode selecionar métricas adicionais a partir do menu pendente e especificar um intervalo de tempo para ver os dados de desempenho.

## <a name="reports-for-ad-fs"></a>Relatórios do AD FS
Azure AD ligar o estado de funcionamento do fornece relatórios sobre actividade e desempenho do AD FS. Estes relatórios ajudam os administradores ganhar visão atividades nos seus servidores de AD FS.

### <a name="top-50-users-with-failed-usernamepassword-logins"></a>Início 50 utilizadores com falhados inícios de sessão do nome de utilizador/palavra-passe

Uma das razões comuns para um pedido de autenticação falhadas num servidor ADFS é um pedido de com as credenciais inválidos, ou seja, um nome de utilizador mal ou palavra-passe. Normalmente acontece aos utilizadores devido a palavras-passe complexas, palavras-passe esquecidas ou existem erros ortográficos.

Mas existem outras razões que podem resultar num número inesperado de pedidos de que está a ser processadas pelos seus servidores de AD FS, tais como: uma aplicação que caches credenciais de utilizador e as credenciais expirarem ou por um utilizador malicioso a tentar iniciar sessão para uma conta com uma série de palavras-passe conhecidas. Estes dois exemplos são razões válidas que podem provocar um picos nos pedidos.

Azure AD ligar o estado de funcionamento para ADFS fornece um relatório sobre os principais 50 utilizadores com tentativas de início de sessão falhou devido a nome de utilizador inválido ou palavra-passe. Este relatório é realizado pelos processamento dos eventos de auditoria gerados por todos os servidores de AD FS os farms

![Portal de estado de funcionamento de ligação do Azure AD](./media/active-directory-aadconnect-health-adfs/report1a.png)

Dentro deste relatório pode aceder facilmente para as seguintes peças das informações:

- # Total de pedidos de falhados com o nome de utilizador/palavra-passe incorreta nos últimos 30 dias
- Média # de utilizadores que ocorreu uma falha com um início de sessão do nome de utilizador/palavra-passe incorretas por dia.


Clicar nesta peça leva-o para a pá relatório principal que fornece detalhes adicionais. Este pá inclui um gráfico com informações de tendência para ajudar a estabelecer uma linha base acerca dos pedidos de com palavra-passe ou o nome de utilizador errado. Para além disso, fornece a lista de início 50 utilizadores com o número de tentativas falhadas.

O gráfico fornece as seguintes informações:

- O número total de inícios de sessão falhou devido a um incorretas nome de utilizador/palavra-passe numa base por dia.
- O total # de utilizadores exclusivos que falhou inícios de sessão numa base por dia.
- Endereço IP de cliente para o último pedido

![Portal de estado de funcionamento de ligação do Azure AD](./media/active-directory-aadconnect-health-adfs/report3a.png)

O relatório fornece as seguintes informações:

| Item de relatório | Descrição
| ------ | -------- |
|ID de utilizador| Mostra o ID de utilizador que foi utilizado. Este valor é o que o utilizador escreveu, que em alguns casos é o utilizador mal ID a ser utilizado.|
|Tentativas falhadas| Mostra o total # de tentativas falhadas para esse ID de utilizador específico. A tabela é ordenada com o maior número de tentativas falhadas por ordem descendente.|
|Última falha| Mostra o carimbo de hora quando ocorreu a última falha.
|Última falha de IP | Mostra o endereço IP do cliente a partir do pedido incorretas mais recente.|



>[AZURE.NOTE] Este relatório é automaticamente atualizado depois da cada duas horas com as novas informações recolhidas no prazo de nesse período de tempo. Como resultado, tentativas de início de sessão nas duas últimas horas poderão não estar incluídas no relatório.



## <a name="related-links"></a>Ligações relacionadas

* [Estado de funcionamento de ligação do Azure AD](active-directory-aadconnect-health.md)
* [Ligação do Azure AD instalação do agente de estado de funcionamento](active-directory-aadconnect-health-agent-install.md)
* [Operações de estado de funcionamento de ligação do Azure AD](active-directory-aadconnect-health-operations.md)
* [Utilizar o Azure AD ligar-se do Estado de funcionamento para a sincronização](active-directory-aadconnect-health-sync.md)
* [Utilizar o Azure AD ligar-se do Estado de funcionamento com o AD DS](active-directory-aadconnect-health-adds.md)
* [Perguntas mais frequentes do Estado de funcionamento de ligação do Azure AD](active-directory-aadconnect-health-faq.md)
* [Histórico de versões do Estado de funcionamento de ligação do Azure AD](active-directory-aadconnect-health-version-history.md)
