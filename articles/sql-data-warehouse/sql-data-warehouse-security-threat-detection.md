<properties
   pageTitle="Introdução ao SQL dados armazém ameaça deteção"
   description="Como começar com a ameaça detecção"
   services="sql-data-warehouse"
   documentationCenter=""
   authors="lodipalm"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="09/24/2016"
   ms.author="lodipalm;sonyama;barbkess"/>


# <a name="get-started-with-threat-detection"></a>Introdução ao deteção ameaça

> [AZURE.SELECTOR]
- [Auditoria](sql-data-warehouse-auditing-overview.md)
- [Deteção de ameaça](sql-data-warehouse-security-threat-detection.md)

## <a name="overview"></a>Descrição geral

Deteção de ameaça Deteta atividades de base de dados discordantes indicando potenciais ameaças de segurança para a base de dados. Deteção de ameaça é na pré-visualização e é suportada para armazém de dados SQL.

Deteção de ameaça fornece uma nova camada de segurança, que permite aos clientes detetar e responder às potenciais ameaças medida que estes ocorrem fornecendo alertas de segurança sobre atividades discordantes. Os utilizadores podem explore os eventos suspeitos utilizando o [Azure SQL dados armazém de auditoria](sql-data-warehouse-auditing-overview.md) para determinar se resultantes uma tentativa de aceder, violar ou explorar dados no armazém de dados.
Deteção de ameaça torna simples para ameaças potenciais do endereço para o armazém de dados sem ter de ser um perito em segurança ou a gestão da segurança avançada sistemas de monitorização.

Por exemplo, deteção ameaça Deteta certas actividades discordantes da base de dados que indica os possíveis tentativas de introdução de SQL. Introdução de SQL é um dos problemas comuns de segurança de aplicação Web na Internet, utilizado para ataque aplicações baseadas em dados. Intrusos tirar partido das vulnerabilidades de aplicação a inserção maliciosas instruções SQL para os campos de entrada de aplicação, para infracção ou modificar os dados na base de dados.


## <a name="set-up-threat-detection-for-your-database"></a>Configurar a deteção de ameaça para a base de dados

1. Inicie o Portal do Azure em [https://portal.azure.com](https://portal.azure.com).

2. Navegue para a pá de configuração do armazém de dados SQL que pretende monitorizar. No pá definições, selecione **auditoria e deteção de ameaça**.

    ![Painel de navegação][1]

3. Na janela **auditoria & ameaça deteção** pá configuração ativar **ON** auditoria, que irá apresentar as definições de deteção de ameaça.

    ![Painel de navegação][2]

4. Ative a deteção de ameaça **Sucessivamente** .

5. Configure a lista de mensagens de correio eletrónico que irão receber alertas de segurança de deteção de dados anómalo de actividades de armazém.

6. Clique em **Guardar** a pá de configuração de **deteção de auditoria & ameaça** para guardar o novo ou actualizado auditoria e ameaças política de detecção.

    ![Painel de navegação][3]


## <a name="explore-anomalous-data-warehouse-activities-upon-detection-of-a-suspicious-event"></a>Explorar actividades de armazém discordantes dados relativamente a deteção de um evento suspeita

1. Receberá uma notificação de correio eletrónico relativamente a deteção de atividades de base de dados discordantes. <br/>
O e-mail irá fornecer informações sobre o evento de segurança suspeitos, incluindo a natureza das atividades anómalo, nome da base de dados, nome de servidor e a hora do evento. Além disso, irá fornecer informações sobre possíveis causas e recomendados ações para investigar e mitigar a ameaça potencial à base de dados.<br/>

    ![Painel de navegação][4]

2. Na mensagem de e-mail, clique na ligação da **Auditoria de registo do SQL Azure** , o que vai iniciação Portal clássica do Azure e mostrar os registos de auditoria relevantes à volta a hora do evento suspeita.

    ![Painel de navegação][5]

3. Clique nos registos de auditoria para ver mais detalhes sobre as atividades de base de dados suspeita como instrução SQL, IP de cliente e motivo falha.

    ![Painel de navegação][6]

4. No pá registos de auditoria, clique em **Abrir no Excel** para abrir um pré-configurada modelo para importar e execute uma análise mais aprofundada do registo de auditoria à volta a hora do evento suspeita do excel.<br/>
**Nota:** No Excel 2010 ou posterior, é necessário Power Query e a definição de **Combinação rápida**

    ![Painel de navegação][7]

5. Para configurar a **Combinação rápida** definição - no separador do friso **POWER QUERY** , selecione **Opções** para apresentar a caixa de diálogo Opções. Selecione a secção de privacidade e escolher a segunda opção - 'Ignorar os níveis de privacidade e melhorar o desempenho potencialmente':

    ![Painel de navegação][8]

6. Para carregar registos de auditoria SQL, certifique-se de que os parâmetros nas definições do separador estão definidos corretamente e, em seguida, selecione o friso 'Dados' e clique no botão 'Atualizar tudo'.

    ![Painel de navegação][9]

7. Os resultados são apresentados na folha de **Registos de auditoria de SQL** que permite-lhe executar uma análise mais aprofundada das atividades discordantes que foram detetadas e mitigar o impacto do evento de segurança na sua aplicação.


<!--Image references-->
[1]: ./media/sql-data-warehouse-security-threat-detection/1_td_click_on_settings.png
[2]: ./media/sql-data-warehouse-security-threat-detection/2_td_turn_on_auditing.png
[3]: ./media/sql-data-warehouse-security-threat-detection/3_td_turn_on_threat_detection.png
[4]: ./media/sql-data-warehouse-security-threat-detection/4_td_email.png
[5]: ./media/sql-data-warehouse-security-threat-detection/5_td_audit_records.png
[6]: ./media/sql-data-warehouse-security-threat-detection/6_td_audit_record_details.png
[7]: ./media/sql-data-warehouse-security-threat-detection/7_td_audit_records_open_excel.png
[8]: ./media/sql-data-warehouse-security-threat-detection/8_td_excel_fast_combine.png
[9]: ./media/sql-data-warehouse-security-threat-detection/9_td_excel_parameters.png
