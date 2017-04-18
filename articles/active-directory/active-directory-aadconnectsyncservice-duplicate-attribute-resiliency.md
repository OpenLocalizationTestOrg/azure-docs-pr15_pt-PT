<properties
    pageTitle="Resiliência de identidade sincronização e duplicado atributo | Microsoft Azure"
    description="Novo comportamento de como gerir objetos com UPN ou ProxyAddress está em conflito durante a sincronização de diretórios com ligação do Azure AD."
    services="active-directory"
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
    ms.date="09/29/2016"
    ms.author="markusvi"/>



# <a name="identity-synchronization-and-duplicate-attribute-resiliency"></a>RDP de atributo de sincronização e duplicar identidade
Duplicados atributo RDP é uma funcionalidade no Azure Active Directory irá eliminar fricção causada pela **UserPrincipalName** e **ProxyAddress** conflitos ao executar uma das ferramentas de sincronização de diretórios da Microsoft.

Estes dois atributos são geralmente necessários para ser exclusivos ao longo do **utilizador**, **grupo**ou **contacto** todos os objetos num determinado inquilino do Azure Active Directory.

> [AZURE.NOTE] Apenas os utilizadores podem ter UPNs.

O novo comportamento esta funcionalidade permite aos é na parte nuvem do pipeline de sincronização, por isso é cliente desconhecido e relevante para qualquer produto de sincronização do Microsoft incluindo ligação do Azure AD, DirSync e MIM + conexão. O termo genérico "cliente de sincronização" é utilizado neste documento para representar qualquer um destes produtos.

## <a name="current-behavior"></a>Comportamento actual
Se existir uma tentativa de aprovisionar um novo objeto com um valor (UPN) ou ProxyAddress que viole a restrição de exclusividade, o Azure Active Directory bloqueia a criação de objeto. Da mesma forma, se um objeto é atualizado com uma não exclusivo UPN ou ProxyAddress, a atualização falhará. A tentativa de aprovisionamento ou actualização é repetida pelo cliente de sincronização após cada ciclo de exportação e continua a falhar até que seja resolvido o conflito. Uma mensagem de e-mail do relatório de erro é gerada após cada tentativa de início e um erro é registado pelo cliente de sincronização.

## <a name="behavior-with-duplicate-attribute-resiliency"></a>Comportamento com atributo duplicado RDP
Em vez de completamente a falhar aprovisionar ou atualizar um objeto com um atributo duplicado, Azure Active Directory "coloca em quarentena" o atributo duplicado que viole a restrição de exclusividade. Se este atributo é necessário para aprovisionar, como o UserPrincipalName, o serviço atribui um valor de marcador de posição. O formato destes valores temporários é  
"***<OriginalPrefix>+<4DigitNumber>@<InitialTenantDomain>. onmicrosoft.com***".  
Se o atributo não for necessário, como um **ProxyAddress**, Azure Active Directory é simplesmente coloca em quarentena o atributo conflito e avança com a criação de objeto ou update.

Relativamente a colocar em quarentena o atributo, são enviadas informações sobre o conflito o mesmo erro relatório por correio eletrónico utilizados no comportamento antigo. No entanto, esta informação aparece apenas no relatório de erros uma vez, se a quarentena acontece,-não continuar a ter sessão iniciada no futuras mensagens de correio eletrónico. Além disso, uma vez que a exportação para este objecto com êxito, o cliente de sincronização não regista um erro e não repetir a criar / operação após a sincronização subsequentes ciclos de actualização.

Para suportar este comportamento foi adicionado um novo atributo para as classes de objeto do utilizador, grupo e contacto:  
**DirSyncProvisioningErrors**

Este é um atributo múltiplos valor que é utilizado para armazenar os em conflito atributos que violem a restrição de exclusividade são acrescentadas normalmente. Uma tarefa do temporizador fundo foi ativada na Azure Active Directory é executado cada hora para procurar os conflitos de atributo duplicado que tenham sido resolvidos e remove automaticamente os atributos em questão de quarentena.

### <a name="enabling-duplicate-attribute-resiliency"></a>Ativar o atributo duplicado RDP
Duplicados atributo RDP será o novo comportamento predefinido em todos os inquilinos do Azure Active Directory. Estará em por predefinição para todos os inquilinos que ativadas sincronização pela primeira vez no 22 de Agosto de 2016 ou posterior. Inquilinos ativado sincronização antes deste data terá a funcionalidade de activada em lotes. Este implementação começará no Setembro de 2016 e uma notificação de e-mail será enviada para o contacto Notificação técnica de cada inquilino com a data específica, quando a funcionalidade irão estar ativada.

Assim que foi ativada duplicar RDP atributo não pode ser desativada.

Para verificar se a funcionalidade está ativada para o seu inquilino, pode fazê-lo ao transferir a versão mais recente do módulo Azure Active Directory PowerShell e em execução:

`Get-MsolDirSyncFeatures -Feature DuplicateUPNResiliency`

`Get-MsolDirSyncFeatures -Feature DuplicateProxyAddressResiliency`

Se gostaria de importante para ativar a funcionalidade antes de está ativada do seu inquilino, pode fazê-lo ao transferir a versão mais recente do módulo Azure Active Directory PowerShell e em execução:

`Set-MsolDirSyncFeature -Feature DuplicateUPNResiliency -Enable $true`

`Set-MsolDirSyncFeature -Feature DuplicateProxyAddressResiliency -Enable $true`

## <a name="identifying-objects-with-dirsyncprovisioningerrors"></a>Identificar objetos com DirSyncProvisioningErrors
Existem atualmente dois métodos para identificar os objetos que tenham estes erros devido a propriedade duplicada conflitos, Azure Active Directory PowerShell e Portal de administração do Office 365. Existem planos para alargar a adicionais portal baseada em elaboração de relatórios no futuro.

### <a name="azure-active-directory-powershell"></a>PowerShell do Azure Active Directory
Para os cmdlets do PowerShell neste tópico, o seguinte é verdadeiro:

- Todos os seguintes cmdlets são sensíveis a maiúsculas.
- O **– ErrorCategory PropertyConflict** sempre devem ser incluídos. Não existem actualmente não existem outros tipos de **ErrorCategory**, mas isto pode ser prolongado no futuro.

Em primeiro lugar, comece por a executar o **Ligar MsolService** e introduzir credenciais de administrador inquilino.

Em seguida, utilize os seguintes cmdlets e operadores para ver erros de diferentes formas:

1. [Ver todos os](#see-all)

2. [Por tipo de propriedade](#by-property-type)

3. [Por valor em conflito](#by-conflicting-value)

4. [Utilizar uma cadeia de pesquisa](#using-a-string-search)

5. [Ordenados](#sorted)

6. [Numa quantidade limitada ou todas](#in-a-limited-quantity-or-all)


#### <a name="see-all"></a>Ver todos os
Quando estiver ligado, para ver uma lista geral de aprovisionamento de atributo erros no inquilino de executar:

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict`

Isto produz um resultado semelhante ao seguinte:  
 ![Get-MsolDirSyncProvisioningError](./media/active-directory-aadconnectsyncservice-duplicate-attribute-resiliency/1.png "Get-MsolDirSyncProvisioningError")  


#### <a name="by-property-type"></a>Por tipo de propriedade
Para ver erros por tipo de propriedade, adicione o sinalizador **- PropertyName** com o argumento **UserPrincipalName** ou **ProxyAddresses** :

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -PropertyName UserPrincipalName`

Ou

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -PropertyName ProxyAddresses`

#### <a name="by-conflicting-value"></a>Por valor em conflito
Para ver erros relacionados com uma propriedade específica, adicionar o sinalizador **- PropertyValue** (**- PropertyName** deve ser utilizado, assim ao adicionar este sinalizador):

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -PropertyValue User@domain.com -PropertyName UserPrincipalName`


#### <a name="using-a-string-search"></a>Utilizar uma pesquisa de cadeia
Para efetuar uma pesquisa de cadeia abrangentes utilize o sinalizador **- CadeiaProcura** . Isto pode ser utilizado separadamente a partir de todos os sinalizadores acima, com a exceção das **-ErrorCategory PropertyConflict**, sempre que é necessário:

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -SearchString User`

#### <a name="in-a-limited-quantity-or-all"></a>Numa quantidade limitada ou todas
1. **MaxResults <Int> ** pode ser utilizada para limitar a consulta para um número específico de valores.

2. **Todos** podem ser utilizados para garantir que todos os resultados são obtidos no caso que existe um grande número de erros.

`Get-MsolDirSyncProvisioningError -ErrorCategory PropertyConflict -MaxResults 5`

## <a name="office-365-admin-portal"></a>Portal de administração do Office 365

Pode ver erros de sincronização de diretórios no Centro de administração do Office 365. O relatório no portal do Office 365 apresenta apenas os objetos de **utilizador** que tenham estes erros. Não apresentar informações sobre os conflitos entre **os grupos** e **contactos**.


![Utilizadores ativos] (./media/active-directory-aadconnectsyncservice-duplicate-attribute-resiliency/1234.png "Utilizadores ativos")

Para obter instruções sobre como ver erros de sincronização de diretórios no Centro de administração do Office 365, consulte o artigo [identificar erros de sincronização de diretórios no Office 365](https://support.office.com/en-us/article/Identify-directory-synchronization-errors-in-Office-365-b4fc07a5-97ea-4ca6-9692-108acab74067).


### <a name="identity-synchronization-error-report"></a>Relatório de erros de sincronização de identidade
Quando um objeto com um conflito de atributo duplicado é processado com este novo comportamento uma notificação é incluída no e-mail de relatório de erros de sincronização de identidade padrão que é enviado para a notificação de técnica de contacto para o inquilino. No entanto, existe uma alteração importante neste comportamento. No passado, informações sobre um conflito de atributo duplicado seriam ser incluídas no cada relatório de erros subsequentes até que o conflito foi resolvido. Com este novo comportamento a notificação de erro para um determinado conflito apenas são apresentados uma vez - momento que o atributo em conflito colocada em quarentena.

Eis um exemplo de o aspeto de notificação de e-mail para um conflito de ProxyAddress:  
    ![Utilizadores ativos](./media/active-directory-aadconnectsyncservice-duplicate-attribute-resiliency/6.png "Active Users")  

## <a name="resolving-conflicts"></a>Resolver conflitos
Resolução de problemas táticas estratégia e resolução para estes erros não deve diferir da forma como os erros de atributo duplicado foram efetuados no passado. A única diferença é que a tarefa do temporizador uma que se coloca através do inquilino do lado do serviço para adicionar automaticamente o atributo em questão ao objeto inicial assim que o conflito seja resolvido.

O seguinte artigo destaca estratégias e resolução de vários: [duplicados ou inválidos atributos impedir a sincronização de diretórios no Office 365](https://support.microsoft.com/kb/2647098).

## <a name="known-issues"></a>Problemas conhecidos
Nenhum destes problemas conhecidos faz com que degradação do serviço ou perda de dados. Várias-las são estéticas, outras pessoas causam padrão "*RDP pré*" erros de atributo duplicado ser iniciadas em vez de colocar em quarentena o atributo conflito e outro faz com que determinados erros exigir o fix de segurança extra manual.

**Comportamento principais:**

1. Objetos com configurações de atributo específico continuam a receber erros de exportação por oposição os atributos duplicados a ser colocada em quarentena.  
Por exemplo:

    um. Novo utilizador for criado no AD com um UPN de **Joe@contoso.com** e ProxyAddress**smtp:Joe@contoso.com**

    b. As propriedades deste objeto entram em conflito com um grupo existente, onde está ProxyAddress **SMTP:Joe@contoso.com**.

    c. Após a exportação, é devolvido um erro de **conflito de ProxyAddress** em vez de ter os atributos de conflito colocada em quarentena. A operação é repetida após cada ciclo de sincronização subsequentes, tal como seriam ter sido antes da funcionalidade de RDP foi ativada.

2. Se dois grupos são criados no local com o mesmo endereço SMTP, uma falha a aprovisionar na primeira tentativa com um erro de **ProxyAddress** duplicado padrão. No entanto, o valor duplicado corretamente é colocada em quarentena após o próximo ciclo de sincronização.

**Relatório de Portal do office**:

1. A mensagem de erro detalhada para dois objetos num conjunto de conflito UPN é a mesma. Isto indica que eles tem ambas tiveram respectivo UPN alteradas / colocada em quarentena, quando na verdade apenas deles tinham quaisquer dados alterados.

2. A mensagem de erro detalhada para um conflito UPN mostra o displayName errado para um utilizador que tenha tido respectivo UPN alteradas/em quarentena. Por exemplo:

    um. **O utilizador A** sincroniza o primeiro com **UPN = User@contoso.com **.

    b. **Utilizador B** tentou sincronizados se segue com **UPN = User@contoso.com **.

    c. **Utilizador B** UPN é alterado para **User1234@contoso.onmicrosoft.com** e **User@contoso.com** é adicionado à **DirSyncProvisioningErrors**.

    d. A mensagem de erro para o **Utilizador B** deverá indicar que já tem **um utilizador** **User@contoso.com** como um UPN, mas mostra displayName próprio **Utilizador B** .



**Relatório de erros de sincronização de identidade**:

A ligação para *obter passos sobre como resolver este problema* está incorreta:  
    ![Utilizadores ativos](./media/active-directory-aadconnectsyncservice-duplicate-attribute-resiliency/6.png "Active Users")  

-Deve apontar para [https://aka.ms/duplicateattributeresiliency](https://aka.ms/duplicateattributeresiliency).


## <a name="see-also"></a>Consulte também

- [Sincronização do Azure AD Connect](active-directory-aadconnectsync-whatis.md)

- [Integrar a suas identidades no local com o Azure Active Directory](active-directory-aadconnect.md)

- [Identificar erros de sincronização de diretórios no Office 365](https://support.office.com/en-us/article/Identify-directory-synchronization-errors-in-Office-365-b4fc07a5-97ea-4ca6-9692-108acab74067)
