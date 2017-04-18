<properties
   pageTitle="Configuráveis durações Token no Azure Active Directory | Microsoft Azure"
   description="Esta funcionalidade é utilizada por administradores e programadores para especificar as durações das tokens emitidos por Azure AD."
   services="active-directory"
   documentationCenter=""
   authors="billmath"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"  
   ms.workload="identity"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="10/06/2016"
   ms.author="billmath"/>


# <a name="configurable-token-lifetimes-in-azure-active-directory-public-preview"></a>Configuráveis durações Token no Azure Active Directory (público pré-visualização)

>[AZURE.NOTE]
>Esta funcionalidade está atualmente na pré-visualização público.  Deve estar preparado para reverter ou remover quaisquer alterações.  Vamos está a abrir o esta funcionalidade para todos os utilizadores experimentar durante a pré-visualização do público, no entanto, determinados aspectos precisem de uma [subscrição do Azure AD Premium](active-directory-get-started-premium.md) assim que ficará disponível.


## <a name="introduction"></a>Introdução
Esta funcionalidade é utilizada por administradores e programadores para especificar as durações das tokens emitidos por Azure AD. Podem ser configuradas durações tokens de todas as aplicações num inquilino, para uma aplicação do inquilino com várias ou para um determinado Principal de serviço num inquilino.

Azure AD, um objecto de política representa um conjunto de regras aplicadas a aplicações individuais ou todas as aplicações de um inquilino.  Cada tipo de política tem uma estrutura exclusiva com um conjunto de propriedades, em seguida, são aplicadas a objetos aos quais estão atribuídos.

Uma política pode ser designada como o predefinido para um inquilino. Esta política, em seguida, entra em vigor em qualquer aplicação que reside dentro que inquilino desde que não sejam substituída por uma política de com uma prioridade mais alta. Também podem ser atribuídas políticas para aplicações específicas. A ordem de prioridade varia consoante o tipo de política.

Políticas de duração do token podem ser configuradas para tokens de atualização, tokens de acesso, tokens de sessão e tokens de ID.


### <a name="access-tokens"></a>Tokens de acesso
Um token de acesso é utilizado por um cliente para aceder a um recurso protegido. Só pode ser utilizado um token de acesso para uma combinação específica de utilizador, o cliente e recursos. Tokens de acesso não podem ser revogados e são válidos até ao seu termo. Um ator malicioso que obteve um token de acesso pode utilizá-lo para a extensão da sua vida útil.  Ajustar duração do token de acesso é um compromisso entre melhorar o desempenho do sistema e aumentar a quantidade de tempo que o cliente mantém acesso após a conta de utilizador está desactivada.  Desempenho do sistema melhorada é obtido reduzindo o número de vezes que necessita de um cliente para adquirir um token de acesso fresca. 

### <a name="refresh-tokens"></a>Atualizar tokens
Quando um cliente adquire um token de acesso para aceder a um recurso protegido, recebe um token de atualização e um token de acesso. O token de atualização é utilizado para obter acesso/atualização nova pares tokens quando expira token de acesso actual. Atualizar tokens estão vinculados a combinações de utilizador e o cliente. Pode revogados e os respetivos validade está selecionada sempre que são utilizadas.

É importante fazer a distinção entre os clientes confidenciais e públicos. Clientes confidenciais são aplicações que podem armazenar em segurança de uma palavra-passe do cliente, permitindo-lhes provar que pedidos são provenientes da aplicação de cliente e não um ator malicioso. Tal como estes fluxos são mais seguros, as durações predefinidas da atualização tokens emitidos para estes fluxos mais elevados e não podem ser alterados utilizando a política de.

Devido a limitações do ambiente do que as aplicações são executadas no, os clientes públicos conseguem armazena uma palavra-passe de cliente. Políticas de podem ser definidas de recursos para impedir tokens de atualizar a partir dos clientes públicos mais antigos do que um determinado período de obter um novo par de token acesso/atualização (atualizar Token máximo inativa hora).  Para além disso, as políticas podem ser utilizadas para definir um período de tempo para além dos quais a atualização tokens já não são aceites (atualizar Token máximo idade).  Duração do token de atualização de regulação permite-lhe controlar quando e número de vezes que o utilizador é necessário para reintroduzir as credenciais em vez de a ser autenticadas em silêncio voltar ao utilizar uma aplicação de cliente público.


### <a name="id-tokens"></a>Tokens de ID
ID tokens são transmitidos a web sites e clientes nativos e contêm informações de perfil sobre um utilizador. Um token de ID está vinculado a uma combinação específica de utilizador e o cliente. Tokens de ID são consideradas válidas até ao termo.  Normalmente, uma aplicação web corresponde a um utilizador da duração da sessão na aplicação para o tempo de vida do ID token emitido para o utilizador.  Duração do token de regulação ID permite-lhe controlar com que frequência a aplicação web irá expirar a sessão de aplicação e requerer que o utilizador voltar ser autenticada com o Azure AD (silenciosamente ou forma interativa).

### <a name="single-sign-on-session-token"></a>Token de sessão de início de sessão único
Quando um utilizador autentica com Azure AD, uma sessão de início de sessão única é estabelecida com o browser e o Azure AD do utilizador.  O único início de sessão sessão Token, sob a forma de um cookie representa esta sessão. É importante ter em atenção que o token de sessão SSO não está ligado a uma aplicação de cliente/recurso específico. Podem ser revogados tokens de sessão SSO e os respetivos validade está selecionada sempre que são utilizadas.

Existem dois tipos de tokens de sessão SSO. Tokens sessão persistente estão armazenados como cookies persistentes pelo browser e de tokens de sessão não persistente são armazenados como cookies de sessão (estes são destruídos quando estiver fechado o browser).

Tokens de sessão não persistente têm uma duração de 24 horas enquanto tokens persistentes ter um tempo de vida dos 180 dias. Qualquer altura que é utilizado o token de sessão SSO dentro do seu período de validade, o período de validade é expandido outra 24 horas ou dias 180. Se a sessão SSO token não é utilizada dentro do seu período de validade considera-se expirou e já não será aceite. 

Políticas podem ser utilizadas para definir um período de tempo após a primeira sessão token foi emitido para além dos quais os tokens de sessão já não são aceites (sessão Token máximo idade).  Duração do token de regulação sessão permite-lhe controlar quando e número de vezes que o utilizador é necessário para volte a introduzir credenciais em vez de a ser autenticadas em silêncio quando utilizar uma aplicação web.

### <a name="token-lifetime-policy-properties"></a>Propriedades de política de duração do token
Uma política de duração do token é um tipo de objeto de política que contém regras de duração do token.  As propriedades da política são utilizadas para controlar as durações tokens especificadas.  Se nenhuma política estiver definida, o sistema impõe o valor de duração predefinido.


### <a name="configurable-token-lifetime-properties"></a>Propriedades de duração do token configuráveis
Propriedade|Cadeia de propriedade da política|Afeta|Predefinido|Mínimo|Máximo|
----- | ----- | ----- |----- | ----- | ----- |
Duração do Token de acesso|  AccessTokenLifetime|Tokens de acesso, tokens de ID, SAML2 tokens|1 hora|10 minutos|1 dia|
Actualizar máximo Token de tempo Inativas|    MaxInactiveTime |Atualizar tokens |14 dias|10 minutos|    cerca de 90 dias|
Fator único atualização Token máximo idade|    MaxAgeSingleFactor  |Atualizar tokens de (para todos os utilizadores) |cerca de 90 dias|10 minutos |Até revogado *|
Atualização Multifator Token máximo idade| MaxAgeMultiFactor|  Atualizar tokens de (para todos os utilizadores)| cerca de 90 dias|10 minutos|Até revogado *|
Fator único sessão Token máximo idade |MaxAgeSessionSingleFactor * *    |Tokens de sessão (persistentes e que não sejam persistentes)| Até revogado   |10 minutos |Até revogado *|
Multifator sessão Token máximo idade| MaxAgeSessionMultiFactor * * *|    Tokens de sessão (persistentes e que não sejam persistentes)| Até revogado|  10 minutos| Até revogado *



- * 365 dias é o comprimento máximo explícito que pode ser definido para estes atributos.
- * * Se MaxAgeSessionSingleFactor não estiver definida, em seguida, este valor leva-o até o valor de MaxAgeSingleFactor. Se nenhuma parâmetro estiver definido, a propriedade assume o valor predefinido (até-expirou).
- Se não estiver definida MaxAgeSessionMultiFactor este valor leva o valor de MaxAgeMultiFactor. Se nenhuma parâmetro estiver definido, a propriedade assume o valor predefinido (até-expirou).

### <a name="exceptions"></a>Exceções
Propriedade|Afeta|Predefinido|
----- | ----- | ----- |
Actualizar Token máximo de tempo inativa (utilizadores federados com informações de revogação de certificados insuficiente)|Atualizar tokens (emitido para os utilizadores federados com informações de revogação de certificados insuficiente)|12 horas|
Actualizar máximo Token de tempo inativa (clientes confidenciais)| Atualizar tokens (emitido para clientes confidenciais)|cerca de 90 dias|
Atualizar token máximo idade (emitido para clientes confidenciais) |   Atualizar tokens (emitido para clientes confidenciais) |Até revogado

### <a name="priority-and-evaluation-of-policies"></a>Prioridade e avaliação de políticas

Políticas de tempo de vida tokens podem ser criadas e atribuídas aplicações específicas, os inquilinos e principais de serviço. Isto significa que é possível para várias políticas aplicar a uma aplicação específica. A política de duração do Token que entra em vigor segue estas regras:


- Se uma política de explicitamente é atribuída ao capital de serviço, serão imposta. 
- Se nenhuma política explicitamente é atribuída ao capital de serviço, uma política de explicitamente atribuída ao inquilino principal do serviço principal será imposta. 
- Se nenhuma política explicitamente é atribuída ao serviço principal ou o inquilino, a política atribuída à aplicação será imposta. 
- Se tiver sido atribuída sem política para o capital de serviço, o inquilino ou o objeto de aplicação, os valores predefinidos serão impostos (consulte a tabela acima).

Para mais informações sobre a relação entre objectos da aplicação e os objectos principais de serviço no Azure AD, consulte o artigo [aplicação e objectos principais de serviço no Azure Active Directory](active-directory-application-objects.md).

Validade de um token é avaliada ao tempo que é utilizado. A política com a prioridade mais alta sobre a aplicação que está a ser acedida entra em vigor.


>[AZURE.NOTE]
>Exemplo
>
>Um utilizador que pretende aceder a 2 as aplicações web, A e B. 
>
>
>- Ambas as aplicações estão ao mesmo inquilino principal. 
>- Política de duração do token 1 com a uma sessão de Token máximo idade de 8 horas está definida como sendo o predefinido do inquilino principal.
>- Aplicação Web respostas é uma aplicação web do utilização regular e não estiver ligada a quaisquer políticas. 
>- Aplicação Web B é utilizada para processos altamente sensíveis e respetivo principal do serviço está ligada a política de duração do token 2 com a idade de máximo Token uma sessão de 30 minutos.
>
>Às 12:00 PM o utilizador abre-se para cima uma nova sessão do browser e tenta aceder a aplicação web respostas. o utilizador é redirecionado para Azure AD e é pedido para iniciar sessão. Isso remove um cookie com um token de sessão no browser. O utilizador é redirecionado para A aplicação com um token de ID permite-lhes aceder à aplicação da web.
>
>Às 12:15 PM, o utilizador, em seguida, tentar aceder a aplicação web B. Browser redireciona para Azure AD que Deteta cookie da sessão. Principal do serviço de B aplicação Web está associado a uma política de 1, mas também faz parte de inquilino principal com 2 a política predefinida. Política 2 entra em vigor uma vez que políticas de principais de serviço têm uma prioridade mais alta que políticas predefinidas de inquilino. O token de sessão originalmente foi emitido dentro os últimos 30 minutos para que é considerada válida. O utilizador é redirecionado para a aplicação web B com um token de ID-los conceder acesso.
>
>Em 1:00 PM o utilizador tenta navegar para respostas. de aplicação web O utilizador é redirecionado para Azure AD. Aplicação Web respostas não está ligada a quaisquer políticas, mas uma vez que é num inquilino com 1 a política predefinida, esta política entra em vigor. A sessão é detetado cookie que foi originalmente emitida dentro de últimas 8 horas e o utilizador será redirecionada silenciosamente voltar a aplicação web respostas com um novo token de ID sem ser necessário especificá-las autenticar.
>
>O utilizador tenta imediatamente aceder a aplicação web B. O utilizador é redirecionado para Azure AD. Como antes, política 2 entra em vigor. Tal como o token foi emitido mais de 30 minutos há, em seguida, é pedido ao utilizador que volte a introduzir as suas credenciais e, uma sessão de novo e token de ID são enviados. O utilizador, em seguida, pode aceder a aplicação web B.

## <a name="configurable-policy-properties-in-depth"></a>Propriedades da política configuráveis: aprofundada

### <a name="access-token-lifetime"></a>Duração do token de acesso

**Cadeia:** AccessTokenLifetime

**Afeta:** Tokens de acesso, tokens de ID

**Resumo:** Esta política controla quanto tempo o access e tokens de ID para este recurso são consideradas válidas. Reduzir a duração do token de acesso atenua o risco de um access ou o token de ID a ser utilizado por um ator malicioso por um determinado período de tempo (tal como não pode ser revogados), mas também adverso impactos desempenho como os tokens terão de ser substituído com mais frequência.

### <a name="refresh-token-max-inactive-time"></a>Actualizar token de tempo inativa máx.

**Cadeia:** MaxInactiveTime

**Afeta:** Atualizar tokens

**Resumo:** Esta política controla como antigo num token da atualização pode ser antes de um cliente já não utilizá-lo para obter um novo par token de acesso/atualização ao tentar aceder a este recurso. Uma vez que um novo token de atualização, normalmente, é devolvido que um token de atualização é utilizado, o cliente não pode ter atingido a qualquer outro recurso utilizando o token de atualização atual para o período de tempo antes da esta política seria impedir o acesso especificado. 

Esta política vai forçar os utilizadores que não tenham sido ativos no seu cliente para autenticar novamente para obter um novo token de atualização. 

É importante ter em atenção que a atualização Token máximo inativa hora deve ser definida para um valor inferior ou superior a idade máximo Token fator único e a Multifator atualizar Token máximo idade.

### <a name="single-factor-refresh-token-max-age"></a>Idade max token de atualização de fator único

**Cadeia:** MaxAgeSingleFactor

**Afeta:** Atualizar tokens

**Resumo:** Controlos esta política quanto tempo um utilizador pode continuar a utilizar tokens de atualização para obter acesso/atualização nova pares tokens após a última vez que foram autenticados com êxito com apenas um fator único. Assim que um utilizador autentica e recebe um novo token de atualização, poderão utilizar o fluxo de token de atualização (desde que não foi revogado o token de atualização atual e não fica não utilizada durante mais da hora inativa) para o período de tempo especificado. Nesse momento, os utilizadores serão forçados para autenticar novamente para receber um novo token de atualização. 

Reduzir a idade max irá forçar os utilizadores para autenticar com mais frequência. Uma vez que considera menos segura que uma autenticação multifator fator único autenticação, recomenda-se que esta política estiver definida para um valor menor ou igual da Multifator atualizar Token máximo idade política.

### <a name="multi-factor-refresh-token-max-age"></a>Idade max token de atualização de multifator

**Cadeia:** MaxAgeMultiFactor

**Afeta:** Atualizar tokens

**Resumo:** Controlos esta política quanto tempo um utilizador pode continuar a utilizar tokens de atualização para obter acesso/atualização nova pares tokens após a última vez que foram autenticados com êxito com vários fatores. Assim que um utilizador autentica e recebe um novo token de atualização, poderão utilizar o fluxo de token de atualização (desde que não foi revogado o token de atualização atual e não fica não utilizada durante mais da hora inativa) para o período de tempo especificado. Nesse momento, os utilizadores serão forçados para autenticar novamente para receber um novo token de atualização. 

Reduzir a idade max irá forçar os utilizadores para autenticar com mais frequência. Uma vez que considera menos segura que uma autenticação multifator fator único autenticação, recomenda-se que esta política está definida para um valor igual ou maior do que a fator único atualizar Token máximo idade política.

### <a name="single-factor-session-token-max-age"></a>Idade max token de sessão fator único

**Cadeia:** MaxAgeSessionSingleFactor

**Afeta:** Tokens de sessão (persistentes e que não sejam persistentes)

**Resumo:** Controlos esta política quanto tempo um utilizador pode continuar a utilizar tokens de sessão para obter o novos tokens de ID e sessão após a última vez autenticados com êxito com apenas um fator único. Assim que um utilizador autentica e recebe um token de nova sessão, eles poderão utilizar o fluxo de token de sessão (desde o token de sessão atual não for revogado ou expirou) para o período de tempo especificado. Nesse momento, os utilizadores serão forçados para autenticar novamente para receber um token de nova sessão. 

Reduzir a idade max irá forçar os utilizadores para autenticar com mais frequência. Uma vez que considera menos segura que uma autenticação multifator fator único autenticação, recomenda-se que esta política estiver definida para um valor menor ou igual ao Multifator sessão Token máximo idade política.

### <a name="multi-factor-session-token-max-age"></a>Idade max token de multifator sessão

**Cadeia:** MaxAgeSessionMultiFactor

**Afeta:** Tokens de sessão (persistentes e que não sejam persistentes)

**Resumo:** Controlos esta política quanto tempo um utilizador pode continuar a utilizar tokens de sessão para obter o novos tokens de ID e sessão após a última vez autenticados com êxito com vários fatores. Assim que um utilizador autentica e recebe um token de nova sessão, eles poderão utilizar o fluxo de token de sessão (desde o token de sessão atual não for revogado ou expirou) para o período de tempo especificado. Nesse momento, os utilizadores serão forçados para autenticar novamente para receber um token de nova sessão. 

Reduzir a idade max irá forçar os utilizadores para autenticar com mais frequência. Uma vez que considera menos segura que uma autenticação multifator fator único autenticação, recomenda-se que esta política está definida para um valor igual ou maior do que a política de idade Token do máximo fator único sessão.

## <a name="sample-token-lifetime-policies"></a>Exemplos de políticas de duração do token

Ser capaz de criar e gerir as durações tokens para aplicações, principais de serviço e o inquilino geral expõe todos os tipos de novos cenários possíveis no Azure AD.  Iremos guiá-lo através de alguns cenários de política comuns que irão ajudá-lo impor novas regras para:


- Durações tokens
- Máximo token inativa horas
- Token de máximo de idade

Explicaremos através de alguns cenários, incluindo:


- Gerir a política predefinida de um inquilino
- Criar uma política de início de sessão no Web
- Criar uma política para as aplicações nativo chamar uma API Web
- Gerir uma política de avançadas 

### <a name="prerequisites"></a>Pré-requisitos
Nos cenários de exemplo estamos vai ser criar, atualizar, ligação e eliminar políticas no seu inquilino geral, principais de serviço e aplicações.  Se estiver familiarizado com o Azure AD, dar saída [Este artigo](active-directory-howto-tenant.md) para o ajudar a começar a utilizar antes de prosseguir com estes exemplos.  


1. Para começar, transfira a mais recente [pré-visualização Cmdlet do PowerShell do Azure AD](https://www.powershellgallery.com/packages/AzureADPreview). 
2.  Assim que tiver os cmdlets do Azure AD PowerShell, execute o comando de ligar para iniciar sessão na sua conta de administrador do Azure AD. Terá de fazer isto sempre que iniciar uma nova sessão.
        
        Connect-AzureAD -Confirm

3.  Execute o seguinte comando para ver todas as políticas que foram criadas no seu inquilino.  Este comando deve ser utilizado após a maioria das operações nos cenários seguintes.  Também irá ajudá-o a preparar o **ID do objeto** do seu políticas. 
        
        Get-AzureADPolicy

### <a name="sample-managing-a-tenants-default-policy"></a>Exemplo: Gerir a política predefinida de um inquilino

Neste exemplo, criamos uma política que permite que os utilizadores iniciar sessão com menos frequência ao longo de todo o inquilino. 

Para fazer isto, podemos criar uma política de duração do token para fator único atualizar Tokens que é aplicada em seu inquilino. Esta política será aplicada a todas as aplicações no seu inquilino e cada serviço principal que ainda não tiver uma política definida para o mesmo. 

1.  **Crie uma política de duração do Token.** 

Defina o Token fator único de atualizar para "até-revogado" significado-não irá expirar até que o access foi revogado.  A definição de política abaixo é o que recomendamos vão criar:
        
        @("{
          `"TokenLifetimePolicy`":
              {
                 `"Version`":1, 
                 `"MaxAgeSingleFactor`":`"until-revoked`"
              }
        }")

Em seguida, execute o seguinte comando para criar esta política. 

        
    New-AzureADPolicy -Definition @("{`"TokenLifetimePolicy`":{`"Version`":1, `"MaxAgeSingleFactor`":`"until-revoked`"}}") -DisplayName TenantDefaultPolicyScenario -IsTenantDefault $true -Type TokenLifetimePolicy
        
Para ver a nova política e obter o seu ID de objecto, execute o seguinte comando.

    Get-AzureADPolicy
&nbsp;&nbsp;2. **atualizar a política de**

Tiver decidido que a primeira política não é bastante como estrita do seu serviço requer e tiver decidido que pretende que o seu fator único atualizar Tokens de expirar em 2 dias. Execute o seguinte comando. 
        
    Set-AzureADPolicy -ObjectId <ObjectID FROM GET COMMAND> -DisplayName TenantDefaultPolicyUpdatedScenario -Definition @("{`"TokenLifetimePolicy`":{`"Version`":1,`"MaxAgeSingleFactor`":`"2.00:00:00`"}}")
        
&nbsp;&nbsp;3. **já está!** 

### <a name="sample-creating-a-policy-for-web-sign-in"></a>Exemplo: Criar uma política para início de sessão no web

Neste exemplo, criamos uma política que irão necessitar os seus utilizadores autenticar com mais frequência para a sua aplicação Web. Esta política serão definir a duração de Tokens de acesso/Id e a idade de máximo de uma sessão de Multifator Token para o capital de serviço da sua aplicação web.

1.  **Crie uma política de duração do Token.**

Esta política para o início de sessão no Web irá definir a duração do Token de acesso/Id e a idade de Token de sessão do máximo fator único para 2 horas.

    New-AzureADPolicy -Definition @("{`"TokenLifetimePolicy`":{`"Version`":1,`"AccessTokenLifetime`":`"02:00:00`",`"MaxAgeSessionSingleFactor`":`"02:00:00`"}}") -DisplayName WebPolicyScenario -IsTenantDefault $false -Type TokenLifetimePolicy

Para ver a nova política e obter o seu ID de objecto, execute o seguinte comando.

    Get-AzureADPolicy
&nbsp;&nbsp;2. **atribuir a política da sua principal do serviço.**

Iremos associar esta política nova com um principal de serviço.  Também terá de uma forma para aceder a **ID de objecto** do seu serviço principal. Pode efetuar consultas no [Microsoft Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity) ou aceda à nossa [Graph Explorer ferramenta](https://graphexplorer.cloudapp.net/) e inicie sessão na sua conta do Azure AD para ver os principais de serviço do seu inquilino. 

Assim que tiver o **ID de objecto**, execute o seguinte comando.
        
    Add-AzureADServicePrincipalPolicy -ObjectId <ObjectID of the Service Principal> -RefObjectId <ObjectId of the Policy>
&nbsp;&nbsp;3. **já está!** 

 

### <a name="sample-creating-a-policy-for-native-apps-calling-a-web-api"></a>Exemplo: Criar uma política para aplicações nativas chamar uma API Web

>[AZURE.NOTE]
>A ligar as políticas de aplicações atualmente é desativada.  Estamos a trabalhar sobre ativar neste brevemente.  Esta página será atualizada assim que a funcionalidade está disponível.

Neste exemplo, irá criar uma política que requer que os utilizadores inferior autenticar e vai aumentar a quantidade de tempo que pode ser Inativas sem ser necessário que autenticar novamente. A política será aplicada à Web API, dessa forma quando a aplicação nativa pede-lo como um recurso que esta política será aplicada.

1.  **Crie uma política de duração do Token.** 

Este comando irá criar uma política de estritamente para uma API Web. 
        
    New-AzureADPolicy -Definition @("{`"TokenLifetimePolicy`":{`"Version`":1,`"MaxInactiveTime`":`"30.00:00:00`",`"MaxAgeMultiFactor`":`"until-revoked`",`"MaxAgeSingleFactor`":`"180.00:00:00`"}}") -DisplayName WebApiDefaultPolicyScenario -IsTenantDefault $false -Type TokenLifetimePolicy
         
Para ver a nova política e obter o seu ID de objecto, execute o seguinte comando.

    Get-AzureADPolicy

&nbsp;&nbsp;2. **Atribuir política para a API do seu Web**.

Iremos associar esta política nova com uma aplicação.  Também terá de uma forma para aceder a **ID de objecto** da sua aplicação. A melhor forma para localizar a sua aplicação **ID de objecto** é utilizar o [Portal do Azure](https://portal.azure.com/). 

Assim que tiver o **ID de objecto**, execute o seguinte comando.

    Add-AzureADApplicationPolicy -ObjectId <ObjectID of the App> -RefObjectId <ObjectId of the Policy>

&nbsp;&nbsp;3. **já está!** 

### <a name="sample-managing-an-advanced-policy"></a>Exemplo: Gerir uma política de avançadas 

Neste exemplo, criamos algumas políticas para demonstrar como funciona o sistema de prioridade e como pode gerir políticas de vários aplicadas a vários objetos. Atribuirá algumas visão a prioridade das políticas acima explicado e também irá ajudar a gerir mais complicados cenários. 

1.  **Criar uma política de duração do Token**

Até ao momento bastante simples. Já Criámos uma política de predefinida do inquilino que define a duração do Token atualizar fator único para 30 dias. 

    New-AzureADPolicy -Definition @("{`"TokenLifetimePolicy`":{`"Version`":1,`"MaxAgeSingleFactor`":`"30.00:00:00`"}}") -DisplayName ComplexPolicyScenario -IsTenantDefault $true -Type TokenLifetimePolicy
Para ver a nova política e obtê-lo é o ID de objecto, execute o seguinte comando.
 
    Get-AzureADPolicy

&nbsp;&nbsp;2. **Atribuir política de um serviço Principal**

Agora temos uma política no todo o inquilino.  Digamos que queremos preservar esta política de 30 dias para um principal de serviço específico, mas alterar a política predefinida de inquilino para ser o limite superior de "até-revogado". 

Em primeiro lugar, iremos associar esta nova política com os nossos principal do serviço.  Também terá de uma forma para aceder a **ID de objecto** do seu serviço principal. Pode efetuar consultas no [Microsoft Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#serviceprincipal-entity) ou aceda à nossa [Graph Explorer ferramenta](https://graphexplorer.cloudapp.net/) e inicie sessão na sua conta do Azure AD para ver os principais de serviço do seu inquilino. 

Assim que tiver o **ID de objecto**, execute o seguinte comando.

    Add-AzureADServicePrincipalPolicy -ObjectId <ObjectID of the Service Principal> -RefObjectId <ObjectId of the Policy>

&nbsp;&nbsp;3. **definir o sinalizador IsTenantDefault para FALSO utilizando o seguinte comando**. 

    Set-AzureADPolicy -ObjectId <ObjectId of Policy> -DisplayName ComplexPolicyScenario -IsTenantDefault $false
&nbsp;&nbsp;4. **criar uma nova política predefinida do inquilino**

    New-AzureADPolicy -Definition @("{`"TokenLifetimePolicy`":{`"Version`":1,`"MaxAgeSingleFactor`":`"until-revoked`"}}") -DisplayName ComplexPolicyScenarioTwo -IsTenantDefault $true -Type TokenLifetimePolicy

&nbsp;&nbsp;5. **já está!** 

Tem agora a política original ligada a sua principal do serviço e a nova política definida como a política predefinida do inquilino.  É importante lembrar-se de que as políticas de aplicado aos principais de serviço têm prioridade sobre políticas predefinidas de inquilino. 


## <a name="cmdlet-reference"></a>Referência do cmdlet

### <a name="manage-policies"></a>Gerir políticas
Os seguintes cmdlets podem ser utilizados para gerir políticas de.</br></br>



#### <a name="new-azureadpolicy"></a>Novo AzureADPolicy
Cria uma nova política.

    New-AzureADPolicy -Definition <Array of Rules> -DisplayName <Name of Policy> -IsTenantDefault <boolean> -Type <Policy Type> 

Parâmetros|Descrição|Exemplo|
-----| ----- |-----|
-Definição| A matriz de JSON stringified que contém todas as regras da política.|-Definition @("{`"TokenLifetimePolicy`":{`"Version`":1,`"MaxInactiveTime`":`"20:00:00`"}}") 
-DisplayName|Cadeia do nome da política|MyTokenPolicy - DisplayName
-IsTenantDefault|Se verdadeiro define a política como política predefinida do inquilino, se for FALSO não faz nada|-IsTenantDefault $true
-Tipo de|O tipo de política, para tokens durações sempre utilizar "TokenLifetimePolicy"|-Escreva TokenLifetimePolicy
-AlternativeIdentifier [opcional]|Define um id alternativo para a política.|MyAltId - AlternativeIdentifier
</br></br>

#### <a name="get-azureadpolicy"></a>Get-AzureADPolicy         
Obtém todos os AzureAD políticas ou política especificada 
        
    Get-AzureADPolicy 

Parâmetros|Descrição|Exemplo|
-----| ----- |-----|
ID de objecto-[opcional]|Id da política que gostaria de obter o objeto. |ID de objecto - &lt;ID da política de objecto&gt; 
</br></br>

#### <a name="get-azureadpolicyappliedobject"></a>Get-AzureADPolicyAppliedObject         
Obtém a todas as aplicações e principais de serviço ligados a uma política de
        
    Get-AzureADPolicyAppliedObject -ObjectId <object id of policy> 

Parâmetros|Descrição|Exemplo|
-----| ----- |-----|
ID de-objecto|Id da política que gostaria de obter o objeto.|ID de objecto - &lt;ID da política de objecto&gt;
</br></br>

#### <a name="set-azureadpolicy"></a>Definir AzureADPolicy
Atualiza uma política de existente
        
    Set-AzureADPolicy -ObjectId <object id of policy> -DisplayName <string> 
 
Parâmetros|Descrição|Exemplo|
-----| ----- |-----|
ID de-objecto|Id da política que gostaria de obter o objeto.|ID de objecto - &lt;ID da política de objecto&gt;
-DisplayName|Cadeia do nome da política|MyTokenPolicy - DisplayName
-Definição [opcional]|A matriz de JSON stringified que contém todas as regras da política.|-Definition @("{`"TokenLifetimePolicy`":{`"Version`":1,`"MaxInactiveTime`":`"20:00:00`"}}") 
-IsTenantDefault [opcional]|Se verdadeiro define a política como política predefinida do inquilino, se for FALSO não faz nada|-IsTenantDefault $true
-Tipo de [opcional]|O tipo de política, para tokens durações sempre utilizar "TokenLifetimePolicy"|-Escreva TokenLifetimePolicy
-AlternativeIdentifier [opcional]|Define um id alternativo para a política.|MyAltId - AlternativeIdentifier
</br></br>

#### <a name="remove-azureadpolicy"></a>Remover AzureADPolicy         
Elimina a política de especificado

     Remove-AzureADPolicy -ObjectId <object id of policy>

Parâmetros|Descrição|Exemplo|
-----| ----- |-----|
ID de-objecto|Id da política que gostaria de obter o objeto.|ID de objecto - &lt;ID da política de objecto&gt;
</br></br>

### <a name="application-policies"></a>Políticas de aplicação
Os seguintes cmdlets pode ser utilizados para as políticas de aplicação.</br></br>

#### <a name="add-azureadapplicationpolicy"></a>AzureADApplicationPolicy adicionar         
Ligações a política especificada à aplicação

    Add-AzureADApplicationPolicy -ObjectId <object id of application> -RefObjectId <object id of policy>

Parâmetros|Descrição|Exemplo|
-----| ----- |-----|
ID de-objecto|O objeto Id da aplicação.|ID de objecto - &lt;ID da aplicação de objecto&gt; 
-RefObjectId|O Id da política de objeto. |-RefObjectId &lt;ID da política de objecto&gt;
</br></br>

#### <a name="get-azureadapplicationpolicy"></a>Get-AzureADApplicationPolicy        
Obtém a política atribuída à aplicação

    Get-AzureADApplicationPolicy -ObjectId <object id of application>

Parâmetros|Descrição|Exemplo|
-----| ----- |-----|
ID de-objecto|O objeto Id da aplicação.|ID de objecto - &lt;ID da aplicação de objecto&gt; 
</br></br>

#### <a name="remove-azureadapplicationpolicy"></a>Remover AzureADApplicationPolicy        
Remove uma política de uma aplicação

    Remove-AzureADApplicationPolicy -ObjectId <object id of application> -PolicyId <object id of policy>

Parâmetros|Descrição|Exemplo|
-----| ----- |-----|
ID de-objecto|O objeto Id da aplicação.|ID de objecto - &lt;ID da aplicação de objecto&gt; 
-PolicyId| ID de objecto da política.|-PolicyId &lt;ID da política de objecto&gt;
</br></br>

### <a name="service-principal-policies"></a>Políticas de principais de serviço
Os seguintes cmdlets pode ser utilizados para políticas principal do serviço.</br></br>

#### <a name="add-azureadserviceprincipalpolicy"></a>AzureADServicePrincipalPolicy adicionar         
Liga a política especificada a um principal de serviço

    Add-AzureADServicePrincipalPolicy -ObjectId <object id of service principal> -RefObjectId <object id of policy>

Parâmetros|Descrição|Exemplo|
-----| ----- |-----|
ID de-objecto|O objeto Id da aplicação.|ID de objecto - &lt;ID da aplicação de objecto&gt; 
-RefObjectId|O Id da política de objeto. |-RefObjectId &lt;ID da política de objecto&gt;
</br></br>

#### <a name="get-azureadserviceprincipalpolicy"></a>Get-AzureADServicePrincipalPolicy        
Obtém a qualquer política ligada para o capital de serviço especificado

    Get-AzureADServicePrincipalPolicy -ObjectId <object id of service principal>
 
Parâmetros|Descrição|Exemplo|
-----| ----- |-----|
ID de-objecto|O objeto Id da aplicação.|ID de objecto - &lt;ID da aplicação de objecto&gt; 
</br></br>

#### <a name="remove-azureadserviceprincipalpolicy"></a>Remover AzureADServicePrincipalPolicy         
Remove a política de serviço especificado principal

    Remove-AzureADServicePrincipalPolicy -ObjectId <object id of service principal>  -PolicyId <object id of policy>
 
Parâmetros|Descrição|Exemplo|
-----| ----- |-----|
ID de-objecto|O objeto Id da aplicação.|ID de objecto - &lt;ID da aplicação de objecto&gt; 
-PolicyId| ID de objecto da política.|-PolicyId &lt;ID da política de objecto&gt;
