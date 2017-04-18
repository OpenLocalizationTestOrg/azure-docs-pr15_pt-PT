<properties
    pageTitle="Proteção de identidade do Azure Active Directory | Microsoft Azure"
    description="Saiba como a proteção de identidade do Azure AD permite-lhe limitar a capacidade de um ataque para explorar um dispositivo ou comprometida identidade e de proteger uma identidade ou um dispositivo que suspeitos ou conhecido por ser comprometida anteriormente."
    services="active-directory"
    keywords="proteção de identidade do Azure do active directory, deteção de aplicação de nuvem, gerir aplicações, segurança, risco, nível de risco, vulnerabilidade, política de segurança"
    documentationCenter=""
    authors="markusvi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/26/2016"
    ms.author="markvi"/>

#<a name="azure-active-directory-identity-protection"></a>Proteção de identidade do Azure Active Directory 

Azure Active Directory identidade Protection é uma funcionalidade de edição do Azure AD Premium P2 que fornece-lhe uma visão consolidada em eventos de risco e potenciais vulnerabilidades afetar identidades da sua organização. Microsoft tem sido proteger identidades baseado na nuvem para uma década e com proteção de identidade do Azure AD, Microsoft é disponibilização estes mesmos sistemas de proteção para os clientes empresariais. Proteção de identidade tira partido das capacidades de detecção de anomalia do existente Azure AD (disponíveis através de relatórios de atividade discordantes Azure AD) e apresenta os novos tipos de evento de risco podem detetar anomalias em tempo real.



##<a name="getting-started"></a>Introdução

A maioria das violações de segurança executada quando intrusos obtêm acesso a um ambiente por roubar identidade de um utilizador. Intrusos ficaram cada vez mais eficazes a tirar partido da violações de terceiros e utilizar ataques de phishing sofisticadas. Assim que um atacante obtiver acesso ao mesmo uma conta de utilizador privilegiados baixa, é relativamente simples para que possam ter acesso a recursos de empresa importantes através de movimento lateral. Por conseguinte, é essencial para proteger todas as identidades e, quando uma identidade está comprometida, importante impedir a identidade comprometida a ser utilizado abusivamente. 

Descobrir identidades comprometidas não é nenhuma tarefa fácil. Felizmente, pode ajudar a proteção de identidade: proteção de identidade utiliza algoritmos de aprendizagem máquina ajustável e heurística para detetar anomalias e eventos que podem indicar que uma identidade comprometida o risco.
 
Proteção de identidade utilizar estes dados, gera relatórios e alertas permite-lhe investigar estes eventos de risco e tomar remediação adequada ou ação mitigação.
 
Mas protecção de identidade do Azure Active Directory é mais do que uma ferramenta de monitorização e elaboração de relatórios. Proteção de identidade com base em eventos de risco, calcula a um nível de risco de utilizador para cada utilizador, permitindo-lhe configurar com base em risco políticas para proteger automaticamente as identidades da sua organização.  Estas políticas com base em risco, para além de outros controlos de acesso condicional fornecidas pela Azure Active Directory e EMS, podem bloquear ou oferecer ações remediação ajustável que incluam de palavra-passe e imposição de autenticação multifator automaticamente.  

####<a name="explore-identity-protections-capabilities"></a>Explorar as capacidades de protecção de identidade 

**Detetar eventos de risco e arriscadas contas:**  

- Detetar 6 tipos de evento de risco utilizar as regras heurísticas e formação de máquina 

- Calcular níveis de risco de utilizador

- Fornecer recomendações personalizadas para melhorar a geral postura de segurança ao realçá vulnerabilidades



**Investigar eventos de risco:**

- Enviar notificações para eventos de risco

- Investigar eventos de risco utilizando as informações relevantes e contextuais

- Fornecer básicos fluxos de trabalho para controlar a inquéritos

- Fornecer acesso fácil a ações de remediação como reposição de palavra-passe



**Políticas de acesso condicional com base em risco:**

- Política de mitigar o risco de início de sessão-ins pelo bloqueio de início de sessão-ins ou exigir desafios autenticação multifator.

- Política para bloquear ou contas de utilizador arriscado seguro

- Política para exigir que os utilizadores para se registar no autenticação multifator


## <a name="detection-and-risk"></a>Deteção e risco

### <a name="risk-events"></a>Eventos de risco

Eventos de risco são eventos que foram sinalizados como suspeitas pelo proteção de identidade e indicam que uma identidade pode ter sido comprometida. Para uma lista completa dos eventos de risco, consulte o artigo [tipos de eventos de risco detectados pelo protecção de identidade do Azure Active Directory](active-directory-identityprotection-risk-events-types.md). 


### <a name="risk-level"></a>Nível de risco

O nível de risco de um evento de risco é uma indicação (alto, médio ou baixa) da gravidade do evento de risco. O nível de risco ajuda os utilizadores de proteção de identidade atribuir prioridades a ações devem tomar para reduzir o risco à sua organização. A gravidade do evento de risco representa a intensidade do sinal como um predictor de comprometer a identidade, combinado com a quantidade de ruído-normalmente apresenta. 

- **Alto**: alta confiança e evento de risco de gravidade alta. Estes eventos são indicadores fortes que a identidade do utilizador está comprometida e todas as contas de utilizador afectadas devem ser remediated imediatamente.

- **Média**: gravidade alta, mas o evento de risco de confiança inferior, ou vice versa. Estes eventos estão potencialmente perigosos e todas as contas de utilizador afectadas devem ser remediated.

- **Baixa**: fraca confiança e evento de risco de gravidade baixa. Este evento não precisem de uma ação de imediata, mas quando o combinado com outros eventos de risco, poderá fornecer uma indicação forte que a identidade está comprometida. 


![Nível de risco] (./media/active-directory-identityprotection/01.png "Nível de risco")

 

Eventos de risco quer estão identificados **em tempo real**, ou em pós-processamento depois do evento de risco já tiver sido colocar (offline). Atualmente a maioria dos eventos de risco de proteção de identidade são calculados offline e sejam apresentadas na identidade proteção dentro de 2 a 4 horas. Enquanto avaliada em tempo real, os eventos de risco em tempo real irão aparecer na consola de proteção de identidade dentro de 5 a 10 minutos.

Vários clientes legados não suportam atualmente prevenção e deteção de evento de risco em tempo real. Como resultado, inícios de sessão a partir destes clientes não podem ser detetados ou impedidos em tempo real.


## <a name="investigation"></a>Inquérito
Normalmente, a sua viagem através de proteção de identidade começa com o dashboard de proteção de identidade. 

![Remediação] (./media/active-directory-identityprotection/1000.png "Remediação")

O dashboard dá-lhe acesso para:
 
- Relatórios de como **os utilizadores sinalizadas para o risco**, **eventos de risco** e **vulnerabilidades**
- Definições como a configuração do seu **Políticas de segurança**, **notificações** e **registo de autenticação multifator**
 

É normalmente o ponto de partida para inquérito, que é o processo de rever as atividades, registos de início e outras informações relevantes relacionadas com um evento de risco de decidir se remediação ou mitigação passos são necessários, e como a identidade foi comprometida e compreender como a identidade comprometida foi utilizada.

Pode associam suas atividades de inquérito para as [notificações](active-directory-identityprotection-notifications.md) que Azure Active Directory Protection envia por correio eletrónico.

As secções seguintes fornecem-lhe mais detalhes e os passos que estão relacionados com um inquérito.  



## <a name="what-is-a-user-risk-level"></a>O que é um nível de risco de utilizador?

Um nível de risco de utilizador é uma indicação (alto, médio ou baixa) da probabilidade comprometida a identidade do utilizador. É calculado com base nos eventos de risco de utilizador que estão associados a identidade do utilizador. 

O estado de um evento de risco está **activo** ou **fechado**. Apenas os eventos de risco que **ativos** contribuam para o cálculo de risco de utilizador. 

O nível de risco de utilizador é calculado utilizando as seguintes entradas:

- Eventos de risco activo que afetam o utilizador
- Nível de risco destes eventos 
- Se as ações remediação foi reencaminhadas 

![Riscos de utilizador] (./media/active-directory-identityprotection/1001.png "Riscos de utilizador")



Pode utilizar os níveis de risco de utilizador para criar políticas de acesso condicional para impedir que os utilizadores arriscados iniciar sessão ou forçá-los para alterar a respetiva palavra-passe segura. 


## <a name="closing-risk-events-manually"></a>Eventos de risco de fecho manualmente

Na maioria dos casos, irá demorar ações de remediação como uma palavra-passe segura repor para fechar automaticamente eventos de risco. No entanto, esta poderá não sempre ser possível.  
Isto é, por exemplo, as maiúsculas/minúsculas, quando:

- Um utilizador com eventos de risco ativo foi eliminado
- Um inquérito revelar que um evento de risco denunciado tenha sido executar pelo utilizador legítimo

Uma vez que eventos de risco **ativos** contribuam para o cálculo de risco de utilizador, poderá ter de manualmente diminuir um nível de risco fechando eventos de risco manualmente.  
Durante o curso de inquérito, pode optar por efetuar qualquer uma das seguintes ações para alterar o estado de um evento de risco:

![Ações] (./media/active-directory-identityprotection/34.png "Ações")

- **Resolver** - se depois de investigar um evento de risco, que tenha apontado uma ação remediação adequado fora da proteção de identidade e pensa que o evento de risco deve ser considerado fechado, marcar o evento como resolvido. Resolvido eventos irão definir o estado do evento de risco como fechado e o evento de risco já não será contribuir para o risco de utilizador.

- **Marcar como falso positivo** - em alguns casos, poderá investigar um evento de risco e descobrir que incorretamente foi assinalada como um risco. Pode ajudar a reduzir o número dessas ocorrências marcando o evento de risco como falso positivo. Isto irá ajudar a algoritmos para melhorar a classificação dos eventos semelhantes no futuro de aprendizagem automática. O estado dos eventos falso positivo é **fechado** e se já não serão contribuírem para o risco de utilizador.

- **Ignorar** - se de que não tenha tomadas qualquer ação remediação, mas pretende que o evento de risco para ser removido da lista de active, pode marcar um evento de risco ignorar e o estado de evento vai ser fechado. Eventos ignorados não contribuem para o risco de utilizador. Esta opção só deverá ser utilizada em circunstâncias invulgares. 

- **Reative-a** -eventos de risco que foram fechada manualmente (ao selecionar **resolver**, **falso positivo**ou **Ignorar**) pode ser reactivar, definir o estado do evento para **ativo**. Eventos de risco reactivados contribuam para o cálculo de nível de risco de utilizador. Não será possível reactivar eventos de risco fechados através de dirsynchttp (tais como repor uma palavra-passe segura). 




**Para abrir a caixa de diálogo relacionados com a configuração**:

1. No pá **proteção de identidade do Azure AD** , em **investigar efectue a**, clique em **eventos de risco**.

    ![Repor a palavra-passe manual] (./media/active-directory-identityprotection/1002.png "Repor a palavra-passe manual")

2. Na lista **eventos de risco** , clique num risco.

    ![Repor a palavra-passe manual] (./media/active-directory-identityprotection/1003.png "Repor a palavra-passe manual")

2. No pá risco, com o botão direito um utilizador.

    ![Repor a palavra-passe manual] (./media/active-directory-identityprotection/1004.png "Repor a palavra-passe manual")



### <a name="closing-all-risk-events-for-a-user-manually"></a>Fechar todos os eventos de risco para um utilizador manualmente

Em vez de fechar manualmente eventos de risco para um utilizador individualmente, protecção de identidade do Azure Active Directory também fornece um método de fechar todos os eventos para um utilizador com um clique.


![Ações] (./media/active-directory-identityprotection/2222.png "Ações")

Quando clica em **dispensar todos os eventos**, todos os eventos estão fechados e o utilizador afectado já não se encontra conta e risco.



## <a name="remediating-user-risk-events"></a>Eventos de risco de utilizador para baixo

Uma remediação é uma ação para proteger uma identidade ou um dispositivo que suspeitos ou conhecido por ser comprometida anteriormente. Uma ação remediação restaura a identidade ou dispositivo para um Estado de seguro e resolve anterior risco eventos associados a identidade ou dispositivo.

Para solucionar uma eventos de risco de utilizador, pode:

- Executar uma palavra-passe segura repor para solucionar manualmente uma eventos de risco de utilizador 

- Configurar uma política de segurança de risco de utilizador para mitigar ou remediar automaticamente eventos de risco de utilizador

- Imagem novamente o dispositivo infectado  


### <a name="manual-secure-password-reset"></a>Reposição de palavra-passe segura manual

A reposição de palavra-passe segura é uma remediação eficaz para muitos eventos de risco e quando executada, automaticamente fecha estes eventos de risco e volta a calcular o nível de risco de utilizador. Pode utilizar o dashboard de proteção de identidade para iniciar uma palavra-passe repor para um utilizador arriscado. 

A caixa de diálogo relacionada fornece dois métodos diferentes para repor uma palavra-passe:

**Repor palavra-passe** - selecionar **requerer que o utilizador para repor a palavra-passe** para permitir ao utilizador para recuperar personalizada se o utilizador foi registado para autenticação multifator. Durante seguinte iniciar sessão o utilizador, o utilizador será necessário para resolver um desafio autenticação multifator com êxito e, em seguida, forçado para alterar a palavra-passe. Esta opção não está disponível se a conta de utilizador ainda não estiver registada autenticação multifator.

**Palavra-passe temporária** - selecione **gerar uma palavra-passe temporária** para imediatamente invalidar a palavra-passe existente e criar uma palavra-passe temporária nova para o utilizador. Envie a nova palavra-passe temporária para um endereço de e-mail alternativo para o utilizador ou para o Gestor do utilizador. Uma vez que a palavra-passe temporária, será pedido ao utilizador para alterar a palavra-passe ao iniciar sessão.


![Política] (./media/active-directory-identityprotection/1005.png "Política")


**Para abrir a caixa de diálogo relacionados com a configuração**:

1. No pá **Azure AD identidade proteção** , clique em **utilizadores sinalizados para risco**.

    ![Repor a palavra-passe manual] (./media/active-directory-identityprotection/1006.png "Repor a palavra-passe manual")


2. A partir da lista de utilizadores, selecione um utilizador com eventos de pelo menos um risco.

    ![Repor a palavra-passe manual] (./media/active-directory-identityprotection/1007.png "Repor a palavra-passe manual")


2. No pá utilizador, clique em **Repor palavra-passe**.

    ![Repor a palavra-passe manual] (./media/active-directory-identityprotection/1008.png "Repor a palavra-passe manual")





## <a name="user-risk-security-policy"></a>Política de segurança de risco de utilizador

Uma política de segurança de risco de utilizador é uma política de acesso condicional que avalia o nível de risco a um utilizador específico e aplica-se remediação e mitigação ações com base em condições predefinidas e as regras.


![Política de ridk de utilizador] (./media/active-directory-identityprotection/1009.png "Política de ridk de utilizador")


Proteção de identidade AD Azure ajuda-o a gerir as mitigação e remediação de utilizadores sinalizados para risco, permitindo-lhe para:

- Defina os utilizadores e grupos que a política aplica-se ao: 

    ![Política de ridk de utilizador] (./media/active-directory-identityprotection/1010.png "Política de ridk de utilizador")


- Defina o utilizador nível limiar do risco (mínimo, médio ou grande) que a política de accionadores: 

    ![Política de ridk de utilizador] (./media/active-directory-identityprotection/1011.png "Política de ridk de utilizador")


- Defina os controlos para impostas quando a política de accionadores:

    ![Política de ridk de utilizador] (./media/active-directory-identityprotection/1012.png "Política de ridk de utilizador")


- Mude o estado da sua política:

    ![Política de ridk de utilizador] (./media/active-directory-identityprotection/403.png "Registo MFA")


- Reveja e avaliar o impacto de uma alteração antes de ativá-lo:

    ![Política de ridk de utilizador] (./media/active-directory-identityprotection/1013.png "Política de ridk de utilizador")


Escolher um determinado limiar **Alta** reduz o número de vezes que uma política é acionada e minimiza impacto para os utilizadores.
No entanto, excluir a utilizadores **Baixa** e **média** sinalizados para risco da política, poderá não seguro identidades ou dispositivos que anteriormente suspeita ou conhecidos por ser comprometida.

Quando a definição da política

- Excluir os utilizadores que estão predispostos gerar muitas falsos positivos (os programadores, analistas de segurança)

- Excluir utilizadores regiões onde o activar a política não for prático (por exemplo sem acesso ao suporte técnico)

- Utilizar um determinado limiar **Alta** durante a política inicial filmar saída, ou se tem minimizar desafios visualizados pelos utilizadores finais.

- Se a sua organização precisa de maior segurança, utilize um determinado limiar **mínimo** . Selecionar um determinado limiar **mínimo** apresenta utilizador adicionais sessão são os desafios mais, mas mais segurança.

É a predefinição recomendada para a maioria das organizações configurar uma regra para um determinado limiar **médio** obter um equilíbrio entre da eficiência de utilização e segurança.

Para obter uma descrição geral da experiência do utilizador relacionados, consulte:

- [Fluxo de recuperação de conta afectados](active-directory-identityprotection-flows.md#compromised-account-recovery).  

- [Conta afectados bloqueados fluxo](active-directory-identityprotection-flows.md#compromised-account-blocked).  


**Para abrir a caixa de diálogo relacionados com a configuração**:

1. No pá **proteção de identidade do Azure AD** , na secção **Configurar** , clique em **política de risco de utilizador**.

    ![Política de ridk de utilizador] (./media/active-directory-identityprotection/1009.png "Política de ridk de utilizador")






## <a name="mitigating-user-risk-events"></a>Atenuantes eventos de risco de utilizador
Os administradores podem definir uma política de segurança de risco de utilizador para bloquear utilizadores relativamente a iniciar sessão dependendo do nível de risco. 

Bloquear uma sessão:
 
- Impede a geração de novos eventos de risco de utilizador para o utilizador afectado

- Permite aos administradores manualmente solucionar uma os eventos de risco afetar a identidade do utilizador e restaurá-la para um Estado de segurança



## <a name="what-is-a-sign-in-risk-level"></a>O que é um nível de risco de iniciar sessão?

Um nível de início de sessão no risco é uma indicação (alto, médio ou baixa) da probabilidade que para um início de sessão-in específico, alguém está a tentar autenticar com a identidade do utilizador. O nível de início de sessão no risco é avaliado no momento de um início de sessão no e eventos de risco de considerar e indicadores detetado em tempo real para essa sessão específico. 

## <a name="mitigating-sign-in-risk-events"></a>Atenuantes eventos de início de sessão no risco 
Uma mitigação é uma ação para limitar a capacidade de um ataque para explorar uma identidade comprometida ou dispositivo sem restaurar a identidade ou dispositivo para um Estado de seguro. Uma mitigação não resolver o anterior eventos de início de sessão no risco associados à identidade ou dispositivo.

Pode utilizar acesso condicional na proteção de identidade do Azure AD para mitigar automaticamente eventos de início de sessão no risco. Utilizar estas políticas, considere o nível de risco do utilizador ou o iniciar sessão para bloquear o início de sessão arriscados-ins ou requerer que o utilizador para efetuar a autenticação multifator. Estas ações podem impedir um intruso de exploração de uma identidade roubada danificar e poderão dar-lhe algum tempo para proteger a identidade. 


## <a name="sign-in-risk-security-policy"></a>Política de segurança de início de sessão no risco

Uma política de início de sessão no risco é uma política de acesso condicional que avalia o risco de uma sessão específica e aplica-se atenuações com base em condições predefinidas e as regras.

![Início de sessão no política de risco] (./media/active-directory-identityprotection/1014.png "Início de sessão no política de risco")


Proteção de identidade AD Azure ajuda-o a gerir mitigação de início de sessão-ins arriscados, permitindo-lhe:

- Defina os utilizadores e grupos que a política aplica-se ao: 

    ![Início de sessão no política de risco] (./media/active-directory-identityprotection/1015.png "Início de sessão no política de risco")


- Defina o início de sessão no nível limiar do risco (mínimo, médio ou grande) que a política de accionadores: 

    ![Início de sessão no política de risco] (./media/active-directory-identityprotection/1016.png "Início de sessão no política de risco")


- Defina os controlos para impostas quando a política de accionadores:  

    ![Início de sessão no política de risco] (./media/active-directory-identityprotection/1017.png "Início de sessão no política de risco")
    

- Mude o estado da sua política:

    ![Registo MFA] (./media/active-directory-identityprotection/403.png "Registo MFA")

- Reveja e avaliar o impacto de uma alteração antes de ativá-lo: 

    ![Início de sessão no política de risco] (./media/active-directory-identityprotection/1018.png "Início de sessão no política de risco")


### <a name="what-you-need-to-know"></a>O que precisa de saber

Pode configurar uma política de segurança de início de sessão no risco para exigir autenticação multifator:

![Início de sessão no política de risco] (./media/active-directory-identityprotection/1017.png "Início de sessão no política de risco")

No entanto, por motivos de segurança, esta definição só funciona para utilizadores que já foram registados para autenticação multifator. Se a condição para exigir autenticação multifator é cumprida para um utilizador que ainda não está registado para autenticação multifator, o utilizador está bloqueado. 

Como prática recomendada, se pretender exigir autenticação multifator para suplementos de início de sessão arriscados, deverá:

1. Active a [política de registo de autenticação multifator](#multi-factor-authentication-registration-policy) para os utilizadores afetados.
2. Exigir os utilizadores afetados para o início de sessão numa sessão de que não sejam arriscado para executar um registo MFA

Concluir estes passos garante que a autenticação multifator é necessária para um arriscado iniciar sessão. 


### <a name="best-practices"></a>Melhores práticas

 
Escolher um determinado limiar **Alta** reduz o número de vezes que uma política é acionada e minimiza impacto para os utilizadores.  
 
No entanto, exclui **Baixa** e **média** inícios de sessão sinalizados para risco da política, poderá não bloquear intruso de exploração de uma identidade comprometida. 

Quando a definição da política 

- Excluir os utilizadores que não fazem / não podem ter autenticação multifator

- Excluir utilizadores regiões onde o activar a política não for prático (por exemplo sem acesso ao suporte técnico)

- Excluir os utilizadores que estão predispostos gerar muitas falsos positivos (os programadores, analistas de segurança)

- Utilizar um determinado limiar **Alta** durante a política inicial filmar saída, ou se tem minimizar desafios visualizados pelos utilizadores finais.

- Se a sua organização precisa de maior segurança, utilize um determinado limiar **mínimo** . Selecionar um determinado limiar **mínimo** apresenta utilizador adicionais sessão são os desafios mais, mas mais segurança.

É a predefinição recomendada para a maioria das organizações configurar uma regra para um determinado limiar **médio** obter um equilíbrio entre da eficiência de utilização e segurança.

 
A política de início de sessão no risco é:

- Aplicadas a todo o tráfego do browser e inícios de sessão utilizando a autenticação moderna.
- Não é aplicada a aplicações que utilizem mais antigos protocolos de segurança ao desativar o ponto final WS fidedignidade na IDP federado, tal como ADFS.

A página de **Eventos do risco** na consola de proteção de identidade lista todos os eventos:

- Esta política foi aplicada a
- Pode rever a atividade e determinar se a ação foi adequada ou não 

Para obter uma descrição geral da experiência do utilizador relacionados, consulte:

- [Recuperação de início de sessão arriscada](active-directory-identityprotection-flows.md#risky-sign-in-recovery) 

- [Arriscado início de sessão no bloqueados](active-directory-identityprotection-flows.md#risky-sign-in-blocked)  

- [Registo de autenticação multifator durante uma sessão arriscada](active-directory-identityprotection-flows.md#multi-factor-authentication-registration-during-a-risky-sign-in)  





**Para abrir a caixa de diálogo relacionados com a configuração**:

1. No pá **proteção de identidade do Azure AD** , na secção **Configurar** , clique em **Iniciar sessão política de risco**.

    ![Política de ridk de utilizador] (./media/active-directory-identityprotection/1014.png "Política de ridk de utilizador")





## <a name="multi-factor-authentication-registration-policy"></a>Política de registo de autenticação multifator

Autenticação multifator Azure é um método de quem está a verificar que necessita da utilização de mais do que apenas um nome de utilizador e palavra-passe. Fornece uma segunda camada de segurança para suplementos de início de sessão do utilizador e operações.  
Recomendamos que exigir autenticação multifator Azure para suplementos de início de sessão do utilizador, porque-lo:

- Fornece uma autenticação forte com um intervalo de opções de verificação fácil

- É reproduzido um papel preparar-se à sua organização proteger e recuperar da conta compromete-chave

![Política de ridk de utilizador] (./media/active-directory-identityprotection/1019.png "Política de ridk de utilizador")



Para obter mais detalhes, consulte o artigo [o que é o Azure a autenticação Multifator?](../multi-factor-authentication/multi-factor-authentication.md)


Proteção de identidade AD Azure ajuda-o a gerir o fora de imagens do registo de autenticação multifator através da configuração de uma política que permite-lhe: 



- Defina os utilizadores e grupos que a política aplica-se ao: 

    ![Política MFA] (./media/active-directory-identityprotection/1020.png "Política MFA")



- Defina os controlos para impostas quando a política de accionadores:  

    ![Política MFA] (./media/active-directory-identityprotection/1021.png "Política MFA")


- Mude o estado da sua política:

    ![Política MFA] (./media/active-directory-identityprotection/403.png "Política MFA")

- Ver o estado atual de registo: 

    ![Política MFA] (./media/active-directory-identityprotection/1022.png "Política MFA")


Para obter uma descrição geral da experiência do utilizador relacionados, consulte:

- [Fluxo de registo de autenticação multifator](active-directory-identityprotection-flows.md#multi-factor-authentication-registration).  

- [Registo de autenticação multifator durante um arriscado iniciar sessão](active-directory-identityprotection-flows.md#multi-factor-authentication-registration-during-a-risky-sign-in).  





**Para abrir a caixa de diálogo relacionados com a configuração**:

1. No pá **proteção de identidade do Azure AD** , na secção **Configurar** , clique em **registo de autenticação multifator**.

    ![Política MFA] (./media/active-directory-identityprotection/1019.png "Política MFA")





## <a name="next-steps"></a>Próximos passos

 - [Canal 9: Azure AD e a apresentação de identidade: pré-visualização de proteção de identidade](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)
 - [Tipos de eventos de risco detectados pelo protecção de identidade do Azure Active Directory](active-directory-identityprotection-risk-events-types.md)
 - [Vulnerabilidades detectadas pelo protecção de identidade do Azure Active Directory](active-directory-identityprotection-vulnerabilities.md)
 - [Notificações de proteção de identidade do Active Directory Azure](active-directory-identityprotection-notifications.md)
 - [Azure playbook de proteção de identidade do Active Directory](active-directory-identityprotection-playbook.md)
 - [Glossário do Azure Active Directory identidade Protection](active-directory-identityprotection-glossary.md)

 - [Início de sessão no experiências com a proteção de identidade do Azure AD](active-directory-identityprotection-flows.md)

 - [Activar a proteção de identidade do Azure Active Directory](active-directory-identityprotection-enable.md)
 - [Azure Active Directory protecção de identidade - como desbloquear utilizadores](active-directory-identityprotection-unblock-howto.md)

 - [Introdução ao Azure Active Directory identidade Protection e Microsoft Graph](active-directory-identityprotection-graph-getting-started.md)


