<properties
    pageTitle="Glossário de proteção de identidade do Azure Active Directory | Microsoft Azure"
    description="Glossário de proteção de identidade do Azure Active Directory"
    services="active-directory"
    keywords="proteção de identidade do Azure do active directory, deteção de aplicação de nuvem, gerir aplicações, segurança, risco, nível de risco, vulnerabilidade, política de segurança, Glossário"
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
    ms.date="08/16/2016"
    ms.author="markvi"/>

# <a name="azure-active-directory-identity-protection-glossary"></a>Glossário de proteção de identidade do Azure Active Directory 


### <a name="at-risk-user"></a>Conta e risco (utilizador)  
Um utilizador com um ou mais eventos de risco ativo. 

### <a name="atypical-sign-in-location"></a>Localização de início de sessão atípica   
Uma sessão a partir de uma localização geográfica que não seja típica para o utilizador específico, os utilizadores semelhantes ou o inquilino.

### <a name="azure-ad-identity-protection"></a>Proteção de identidade do Azure AD    
Um módulo de segurança do Azure Active Directory que fornece uma vista consolidada em eventos de risco e potenciais vulnerabilidades afetar identidades de uma organização.

### <a name="conditional-access"></a>Acesso condicional  
Uma política para proteger o acesso aos recursos. Regras de acesso condicional são armazenadas no Azure Active Directory e são avaliadas pelo Azure AD antes de conceder acesso ao recurso.  Regras de exemplo incluem restringir o acesso com base na localização do utilizador, método de autenticação de utilizador ou de estado de funcionamento do dispositivo.

### <a name="credentials"></a>Credenciais     
Informações que incluem a identificação e prova de identificação é utilizada para obter acesso a locais e recursos de rede. Exemplos de credenciais são nomes de utilizador e palavras-passe, smart cards e certificados.

### <a name="event"></a>Evento   
Um registo de uma atividade no Azure Active Directory.

### <a name="false-positive-risk-event"></a>Falso positivo (evento de risco) 
Estado de evento de risco definir manualmente a um utilizador de proteção de identidade, que indica que o evento de risco foi investigar e incorretamente foi assinalado como um evento de risco.

### <a name="identity"></a>Identidade    
Uma pessoa ou entidade que tem de ser validada através de autenticação, com base em critérios tais como a palavra-passe ou um certificado.

### <a name="identity-risk-event"></a>Evento de risco de identidade     
Evento AAD que foi assinalado como discordantes por proteção de identidade e pode indicar que uma identidade comprometida.

### <a name="ignored-risk-event"></a>Ignorado (evento de risco)    
Estado de evento de risco definir manualmente a um utilizador de proteção de identidade, que indica que o evento de risco está fechado sem uma ação remediação a demorar.

### <a name="impossible-travel-from-atypical-locations"></a>Viagem impossibilita a partir de localizações atípicas   
Um evento de risco acionou quando dois ins início de sessão para o mesmo utilizador são detectados, onde pelo menos um dos mesmos é de uma localização de início de sessão atípica e onde o tempo entre os início de sessão-ins é menor do que o tempo mínimo demorar para física viajar entre estas localizações.  

###<a name="investigation"></a>Inquérito    
Compreender o processo de rever as atividades, registos de início e outras informações relevantes relacionadas com um evento de risco de decidir se remediação ou mitigação passos são necessários, se e como a identidade foi comprometida e compreender como a identidade comprometida foi utilizada.

### <a name="leaked-credentials"></a>Credenciais fuga  

Um evento de risco acionou quando credenciais do utilizador actual (nome de utilizador e palavra-passe) forem encontradas publicado publicamente na web escura por nossos investigadores.

### <a name="mitigation"></a>Mitigação  
Uma ação para limitar a ou eliminar a capacidade de um ataque para explorar uma identidade comprometida ou dispositivo sem restaurar a identidade ou dispositivo para um Estado de seguro. Uma mitigação não resolver o anterior risco eventos associados a identidade ou dispositivo.

### <a name="multi-factor-authentication"></a>Autenticação multifator 
Método de autenticação que requer dois ou mais métodos de autenticação, que podem incluir algo que o utilizador tiver, um certificado; algo o utilizador conhecer, como nomes de utilizador, palavras-passe ou frases fase; atributos físicos, tal como uma impressão digital; atributos e de pessoais, tal como uma assinatura pessoal.

### <a name="offline-detection"></a>Deteção offline   
Deteção de anomalias e avaliação do risco de um evento como tentativa de início de sessão após o facto de, para um evento que já tem aconteceu.

### <a name="policy-condition"></a>Condição da política    
Uma parte de uma política de segurança que define as entidades (grupos, os utilizadores, aplicações, plataformas de dispositivos, Estados de dispositivo, intervalos IP, tipos de clientes) incluídos na política ou excluídos da mesma.

### <a name="policy-rule"></a>Regra de política     
A parte de uma política de segurança que descreve as circunstâncias que pretende acionar a política e as ações realizadas quando a política é acionada.

### <a name="prevention"></a>Prevenção  
Uma ação para prevenir danos a organização através de abuse de um dispositivo ou identidade suspeitos ou saber para ser comprometida. Uma ação de prevenção não seguro do dispositivo ou identidade e não resolver o anterior eventos de risco.

### <a name="privileged-user"></a>Privilegiados (utilizador)
Um utilizador no momento de um evento de risco, tinha permissões de administrador permanente ou temporária a um ou mais recurso no Azure Active Directory, tal como um Administrador Global, administrador de faturação, administrador de serviços, o administrador de utilizador e palavra-passe de administrador. 

###<a name="real-time"></a>Em tempo real    
Consulte o artigo Deteção em tempo real.

###<a name="real-time-detection"></a>Deteção em tempo real  
A deteção de anomalias e avaliação de riscos de um evento como tentativa de início de sessão antes do evento são permitidas para continuar.

### <a name="remediated-risk-event"></a>Remediated (evento de risco)     
Estado de evento de risco configurado automaticamente pelo proteção de identidade, que indica que o evento de risco foi remediated utilizando a ação remediação padrão para este tipo de evento de risco. Por exemplo, quando é repor a palavra-passe de utilizador, muitos eventos de risco que indicam que foi comprometida a palavra-passe anterior são automaticamente remediated.

### <a name="remediation"></a>Remediação     
Uma ação para proteger uma identidade ou um dispositivo que anteriormente suspeita ou conhecidas por ser comprometida. Uma ação remediação restaura a identidade ou dispositivo para um Estado de seguro e resolve anterior risco eventos associados a identidade ou dispositivo.

### <a name="resolved-risk-event"></a>Resolvido (evento de risco)   
Estado de evento de risco definir manualmente por um utilizador de proteção de identidade, que indica que o utilizador tenha apontado uma ação remediação adequado fora da proteção de identidade e que o evento de risco deve ser considerado fechada.

###<a name="risk-event-status"></a>Estado do evento de risco    
Uma propriedade de um evento de risco, que indica se o evento está ativo e, se fechada, a razão para fechá-lo.

###<a name="risk-event-type"></a>Tipo de evento de risco  
Uma categoria para o evento de risco, que indica o tipo de anomalia que causaram o evento sejam considerados arriscado.

###<a name="risk-level-risk-event"></a>Nível de risco (evento de risco)  
Uma indicação (alto, médio ou baixa) da gravidade do evento de risco para ajudar os utilizadores de proteção de identidade atribuir prioridades as ações que o mesmo seguir para reduzir o risco à sua organização. 

###<a name="risk-level-sign-in"></a>Nível de risco (sessão) 
Uma indicação (alto, médio ou baixa) da probabilidade que para um início de sessão-in específico, alguém está a tentar utilizar a identidade do utilizador.

###<a name="risk-level-user-compromise"></a>Nível de risco (compromisso do utilizador) 
Uma indicação (alto, médio ou baixa) da probabilidade comprometida uma identidade.

### <a name="risk-level-vulnerability"></a>Nível de risco (vulnerabilidade)  
Uma indicação (alto, médio ou baixa) da gravidade dos vulnerabilidade para ajudar os utilizadores de proteção de identidade atribuir prioridades às ações que o mesmo seguir para reduzir o risco à sua organização.

### <a name="secure-identity"></a>Seguro (identidade)   
Agir remediação tal como uma alteração de palavra-passe ou máquina reimaging para restaurar uma identidade potencialmente comprometida um sólida e estado.

### <a name="security-policy"></a>Política de segurança
Uma coleção de regras de política e condição. Pode ser aplicada uma política de entidades como os utilizadores, grupos, aplicações, dispositivos, plataformas de dispositivos, Estados de dispositivo, intervalos IP e de tipos de clientes Auth2.0. Quando uma política de está ativada, é avaliado sempre que uma entidade incluída na política é emitida um token de um recurso.

### <a name="sign-in-v"></a>Início de sessão (v) 
Para autenticar para uma identidade no Azure Active Directory.

### <a name="sign-in-n"></a>Iniciar sessão (n) 
O processo ou ação de autenticar uma identidade no Azure Active Directory e o evento que esta operação para capturar.

###<a name="sign-in-from-anonymous-ip-address"></a>Iniciar sessão a partir do endereço IP anónimo    
Um evento de risco acionou depois de um bem sucedida iniciar sessão a partir do endereço IP que identificou como um endereço IP proxy anónima.

###<a name="sign-in-from-infected-device"></a>Iniciar sessão a partir do dispositivo infectado 
Um evento de risco acionou quando um início de sessão no provém de um endereço IP que é conhecido para ser utilizado por uma ou mais dispositivos comprometidos, que são ativamente a tentar comunicar com um servidor de bot.

###<a name="sign-in-from-ip-address-with-suspicious-activity"></a>Iniciar sessão a partir do endereço IP com atividade suspeita 
Um evento de risco acionou depois de um bem sucedida iniciar sessão a partir de um IP de endereços com um elevado número de tentativas falhadas de início de sessão em várias contas de utilizador durante um período curto de tempo.

###<a name="sign-in-from-unfamiliar-location"></a>Iniciar sessão a partir da localização não está familiarizada 
Um evento de risco acionou quando um utilizador com êxito inicia a sessão a partir de uma nova localização (IP, Latitude/Longitude e ASN).

###<a name="sign-in-risk"></a>Início de sessão no risco     
Consulte o artigo risco nível (sessão)

###<a name="sign-in-risk-policy"></a>Política de início de sessão no risco  
Uma política de acesso condicional que avalia o risco de uma sessão específica e aplica-se atenuações com base em condições predefinidas e as regras.

###<a name="user-compromise-risk"></a>Risco de compromisso do utilizador     
Consulte o artigo risco nível (compromisso do utilizador)

###<a name="user-risk"></a>Risco de utilizador    
Consulte o artigo risco nível (compromisso do utilizador).

###<a name="user-risk-policy"></a>Política de risco de utilizador     
Uma política de acesso condicional que considera o iniciar sessão e aplica-se atenuações com base em condições predefinidas e regras.

###<a name="users-flagged-for-risk"></a>Utilizadores sinalizados para o risco   
Utilizadores que têm eventos de risco que são ativa ou remediated

###<a name="vulnerability"></a>Vulnerabilidade    
Uma configuração ou condição no Azure Active Directory que faz com que o diretório sensíveis às ataques ou ameaças.


## <a name="see-also"></a>Consulte também

- [Proteção de identidade do Azure Active Directory](active-directory-identityprotection.md)
