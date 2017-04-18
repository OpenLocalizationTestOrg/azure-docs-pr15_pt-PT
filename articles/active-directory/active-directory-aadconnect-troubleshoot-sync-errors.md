<properties
    pageTitle="Ligação do Azure AD: Resolver problemas de erros durante a sincronização | Microsoft Azure"
    description="Explica como resolver problemas de erros encontrados durante a sincronização com a ligação do Azure AD."
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
    ms.topic="article"
    ms.date="10/18/2016"
    ms.author="vakarand"/>

# <a name="troubleshooting-errors-during-synchronization"></a>Resolução de problemas de erros durante a sincronização
Erros podem ocorrer quando os dados de identidade são sincronizados a partir do Windows Server Active Directory (AD DS) para o Azure Active Directory (Azure AD). Este artigo fornece uma descrição geral dos diferentes tipos de erros de sincronização, algumas situações possíveis que causam esses erros e potenciais formas para corrigir os erros. Este artigo inclui os tipos de erro comuns e não pode abranger todos os possíveis erros.

 Este artigo assume o leitor está familiarizado com subjacente [Estruturar conceitos do Azure AD e Azure AD Connect](active-directory-aadconnect-design-concepts.md).

Com a versão mais recente do ligação do Azure AD \(Agosto 2016 ou superior\), um relatório de erros de sincronização de diretórios está disponível no [Portal do Azure](https://aka.ms/aadconnecthealth) como parte do Azure AD ligar-se do Estado de funcionamento para a sincronização.


Iniciar a 1 de Setembro de 2016 [Azure Active Directory duplicar atributo RDP](active-directory-aadconnectsyncservice-duplicate-attribute-resiliency.md) funcionalidade que irão estar ativada por predefinição para todos os *novos* inquilinos do Azure Active Directory. Esta funcionalidade irão estar ativada automaticamente para a inquilinos existentes nos meses futuras.

Ligação do Azure AD executa 3 tipos de operações de directórios-mantém sincronizado: sincronização, de importação e exportação. Erros podem demorar colocar todas as operações. Este artigo foca principalmente erros durante a exportação para Azure AD.

## <a name="errors-during-export-to-azure-ad"></a>Erros durante a exportar para o Azure AD
Secção a seguir descreve os diferentes tipos de erros de sincronização que podem ocorrer durante a operação de exportação para Azure AD utilizando o conector do Azure AD. Este conector pode ser identificado pelo formato de nome a ser "contoso. *onmicrosoft.com*".
Erros durante a exportar para o Azure AD indicam que a operação \(adicionar, atualizar e eliminar etc\) tentativa de ligação do Azure AD \(motor de sincronização\) no Azure Active Directory falhou.

![Descrição geral de erros de exportação](.\media\active-directory-aadconnect-troubleshoot-sync-errors\Export_Errors_Overview_01.png)

## <a name="data-mismatch-errors"></a>Dados de erros de tipo incompatível
### <a name="invalidsoftmatch"></a>InvalidSoftMatch
#### <a name="description"></a>Descrição
- Quando ligação do Azure AD \(motor de sincronização\) instrui o Azure Active Directory para adicionar ou atualizar objetos, Azure AD corresponde ao objeto recebido utilizando o atributo **sourceAnchor** para o atributo **immutableId** de objetos no Azure AD. Esta correspondência chama um **Corresponder disco rígido**.
- Quando o Azure AD **não localizar** qualquer objeto que corresponde à **immutableId** atributos com o atributo **sourceAnchor** do objeto recebido, antes de aprovisionar um novo objeto reverte para utilizar os atributos ProxyAddresses e UserPrincipalName para localizar uma correspondência. Esta correspondência chama um **Corresponder contornos**. Corresponder contornos foi concebido para que correspondam aos objetos já se encontra presentes no Azure AD (que tenham origem no Azure AD) com os novos objectos a ser adicionado/atualizados durante a sincronização que representam a mesma entidade (utilizadores, grupos) no local.
- **InvalidSoftMatch** erro ocorre quando o disco rígido correspondência não localizar qualquer objeto correspondente **e** contornos corresponder localiza um objecto correspondente, mas esse objeto tem um valor diferente de *immutableId* que *SourceAnchor*, sugerir que o objeto correspondente foi sincronizado com outro objeto a partir no local do Active Directory o objeto recebidas.

Por outras palavras, por ordem para a correspondência contornos trabalhar, o objeto para ser correspondidos contornos com não deve ter qualquer valor da *immutableId*. Se qualquer objeto com *immutableId* definido com um valor é a falhar o disco rígido correspondência mas que satisfaça os critérios de correspondência de contornos, que a operação resultaria um erro de sincronização InvalidSoftMatch.

Esquema do Azure Active Directory não permite duas ou mais objetos de ter o mesmo valor dos seguintes atributos. \(Não se trata de uma lista exaustiva.\)

- ProxyAddresses
- UserPrincipalName
- onPremisesSecurityIdentifier
- ID de objecto

>[AZURE.NOTE] Funcionalidade do [Azure AD atributo duplicado atributo RDP](active-directory-aadconnectsyncservice-duplicate-attribute-resiliency.md) também está a ser lançada como o comportamento predefinido do Azure Active Directory.  Isto irá reduzir o número de erros de sincronização visto por ligação do Azure AD (bem como outros clientes de sincronização) ao efetuar o Azure AD mais flexível na forma como esta trata os duplicados atributos ProxyAddresses e UserPrincipalName presentes no ambientes local AD. Esta funcionalidade não corrigir os erros de duplicação. Por isso, os dados ainda precisa de ser corrigido. Mas permite aprovisionamento de novos objectos bloqueados caso contrário, a partir do Azure AD a ser configurações devido a valores duplicados. Isto também irá reduzir o número de erros de sincronização devolvidas para o cliente de sincronização.
Se esta funcionalidade é ativada para o seu inquilino, não irá ver erros de sincronização InvalidSoftMatch vistos durante o aprovisionamento de objetos de novos.


#### <a name="example-scenarios-for-invalidsoftmatch"></a>Cenários de exemplo para InvalidSoftMatch
1. Objetos de duas ou mais com o mesmo valor do atributo ProxyAddresses existentes no local do Active Directory. Apenas um está introdução aprovisionado no Azure AD.
2. Objetos de duas ou mais com o mesmo valor userPrincipalName existentes no local do Active Directory. Apenas um está introdução aprovisionado no Azure AD.
3. Um objeto foi adicionado nas instalações nas Active Directory com o mesmo valor do atributo ProxyAddresses como que um objeto no Azure Active Directory existente. Não está a obter aprovisionado o objeto adicionado no local no Azure Active Directory.
4. Um objeto foi adicionado no local do Active Directory com o mesmo valor do atributo userPrincipalName como que uma conta no Azure Active Directory. O objeto não está a obter aprovisionado no Azure Active Directory.
5. Uma conta sincronizada foi movida de floresta A para floresta B. Azure AD Connect (motor de sincronização) estava a utilizar o atributo GUID de objecto para calcular o SourceAnchor. Depois de mover floresta, o valor do SourceAnchor é diferente. O novo objecto (a partir do floresta B) está a falhar sincronizar com o objeto existente no Azure AD.
6. Um objeto sincronizado tem acidentalmente eliminado no local do Active Directory e um novo objeto foi criado no Active Directory para a mesma entidade (tal como o utilizador) sem eliminar a conta no Azure Active Directory. A nova conta não consegue sincronizar com o objeto do Azure AD existente.
7. Ligação do Azure AD foi desinstalar e voltar instalado. Durante a instalação RE, um atributo diferentes foi escolhido como o SourceAnchor. Todos os objetos que tinham sincronizadas anteriormente parou sincronizar com o erro InvalidSoftMatch.

#### <a name="example-case"></a>Caso de exemplo:
1. **O Rodrigo Silva** é um utilizador sincronizado no Azure Active Directory no local Active Directory do *contoso.com*
2. O Rodrigo Silva **UserPrincipalName** está definido como **bobs@contoso.com**.
3. **"abcdefghijklmnopqrstuv = ="** é **SourceAnchor** calculada a ligação do Azure AD através de de Rodrigo Silva **GUID de objecto** a partir em instalações do Active Directory, que é o **immutableId** para o Rodrigo Silva no Azure Active Directory.
4. O Rodrigo também tem os seguintes valores para o atributo **proxyAddresses** :
    - smtp:bobs@contoso.com
    - smtp:bob.smith@contoso.com
    - **smtp:bob@contoso.com**
5.  Um novo utilizador, **O Rodrigo Taylor**, é adicionado para o local no Active Directory.
6. De Rodrigo Taylor **UserPrincipalName** está definido como **bobt@contoso.com**.
7. **"abcdefghijkl0123456789 = =" "** é **sourceAnchor** calculada a ligação do Azure AD através de de Rodrigo Taylor **GUID de objecto** a partir em instalações do Active Directory. Objeto de Rodrigo Taylor não foram sincronizadas para o Azure Active Directory ainda.
8. O Rodrigo Taylor tem os seguintes valores para o atributo proxyAddresses
    - smtp:bobt@contoso.com
    - smtp:bob.taylor@contoso.com
    - **smtp:bob@contoso.com**
9. Durante a sincronização, ligação do Azure AD irá reconhecer a adição de Rodrigo Taylor no local do Active Directory e pergunte Azure AD para efetuar a mesma alteração.
10. Azure AD pela primeira vez fará a correspondência de disco rígida. Isto é, irá pesquisar se existir qualquer objeto com o immutableId igual a "abcdefghijkl0123456789 = =". Correspondência de disco rígida falhará como sem outro objeto no Azure AD terão esse immutableId.
11. Azure AD irá, em seguida, tentar correspondência de contornos Rodrigo Taylor. Isto é, irá pesquisar se existir qualquer objeto com proxyAddresses igual a três valores, incluindosmtp:bob@contoso.com
12. Azure AD irá encontrar objeto do Rodrigo Silva para corresponder aos critérios de correspondência de contornos. No entanto, este objeto tem o valor de immutableId = "abcdefghijklmnopqrstuv = =". que indica que este objeto foi sincronizado a partir de outro objeto a partir no local do Active Directory. Portanto, Azure AD não é possível contornos correspondência estes objetos e irá resultar num erro de sincronização **InvalidSoftMatch** .

#### <a name="how-to-fix-invalidsoftmatch-error"></a>Como corrigir o erro InvalidSoftMatch
O motivo mais comuns para o erro InvalidSoftMatch é dois objetos com diferentes SourceAnchor \(immutableId\) têm o mesmo valor para os atributos ProxyAddresses e/ou UserPrincipalName, são utilizados durante o processo de contornos CORRESP no Azure AD. Para corrigir a correspondência de contornos inválido

1.  Identifique o dobro proxyAddresses, userPrincipalName ou outro valor do atributo que está a causar o erro. Também identificar qual dois \(ou mais\) objetos estão, normalmente o conflito. O relatório gerado pelo [Azure AD ligar-se do Estado de funcionamento para a sincronização](https://aka.ms/aadchsyncerrors) pode ajudar a identificar os dois objectos.
2. Identificar que objeto deve continuar a ter o valor de duplicados e que objeto não deve.
3. Remova o valor de duplicados do objeto que não deve ter esse valor. Tenha em atenção que deve fazer a alteração no diretório onde o objeto é com origem. Em alguns casos, poderá precisar de eliminar um dos objetos em conflito.
4. Se a alteração que efetuou nas instalações nas AD, deixe a ligação do Azure AD sincronizar a alteração.

Repare que o relatório de erros de sincronização do Azure AD ligar-se do Estado de funcionamento para a sincronização é atualizado a cada 30 minutos e inclui os erros da tentativa de sincronização mais recente.

>[AZURE.NOTE] ImmutableId, por definição, não deve alterar no tempo de vida do objeto. Se a ligação do Azure AD não foi configurado com alguns dos cenários deve ter em conta a partir da lista acima, poderia terminar para cima numa situação onde a ligação do Azure AD calcula um valor diferente de SourceAnchor para o objeto de AD que representa a mesma entidade (mesmo utilizador/grupo/contacto, etc) que contém um objeto de AD Azure existente que pretende continuar a utilizar.

#### <a name="related-articles"></a>Artigos relacionados
- [Atributos duplicados ou inválidos impedir da sincronização de diretórios no Office 365](https://support.microsoft.com/en-us/kb/2647098)

### <a name="objecttypemismatch"></a>ObjectTypeMismatch
#### <a name="description"></a>Descrição
Quando tenta Azure AD para flexível que correspondam aos dois objetos, é possível que dois objetos de diferentes "tipo de objecto" (como o utilizador, grupo, etc. de contacto) têm os mesmos valores para os atributos utilizados para executar a correspondência de contornos. Tal como a duplicação de seguintes atributos não é permitida no Azure AD, a operação pode resultar num erro de sincronização "ObjectTypeMismatch".

#### <a name="example-scenarios-for-objecttypemismatch-error"></a>Cenários de exemplo para o erro ObjectTypeMismatch
- Um grupo de segurança com capacidade de correio é criado no Office 365. Administração adiciona um novo utilizador ou um contacto no local AD (que não está sincronizado para Azure AD ainda) com o mesmo valor do atributo ProxyAddresses como que o grupo do Office 365.

#### <a name="example-case"></a>Caso de exemplo

1. Administrador cria um novo grupo de segurança com capacidade de correio no Office 365 para o departamento de imposto e fornece um endereço de e-mail como tax@contoso.com. Este procedimento atribui atributo ProxyAddresses para este grupo com o valor de**smtp:tax@contoso.com**
2. Um novo utilizador associa Contoso.com e é criada uma conta de utilizador no local com proxyAddress como**smtp:tax@contoso.com**
3. Quando a ligação do Azure AD irá sincronizar nova conta de utilizador, obterá o erro "ObjectTypeMismatch".

#### <a name="how-to-fix-objecttypemismatch-error"></a>Como corrigir o erro ObjectTypeMismatch
O motivo mais comuns para o erro ObjectTypeMismatch é dois objetos de um tipo diferente (utilizador, grupo, etc. de contacto) têm o mesmo valor do atributo ProxyAddresses. Para corrigir o ObjectTypeMismatch:

1.  Identificar o dobro proxyAddresses (ou outros atributos) valor que está a causar o erro. Também identificar qual dois \(ou mais\) objetos estão, normalmente o conflito. O relatório gerado pelo [Azure AD ligar-se do Estado de funcionamento para a sincronização](https://aka.ms/aadchsyncerrors) pode ajudar a identificar os dois objectos.
2. Identificar que objeto deve continuar a ter o valor de duplicados e que objeto não deve.
3. Remova o valor de duplicados do objeto que não deve ter esse valor. Tenha em atenção que deve fazer a alteração no diretório onde o objeto é com origem. Em alguns casos, poderá precisar de eliminar um dos objetos em conflito.
4. Se a alteração que efetuou nas instalações nas AD, deixe a ligação do Azure AD sincronizar a alteração. Relatório de erros de sincronização do Azure AD ligar-se do Estado de funcionamento para a sincronização é atualizado a cada 30 minutos e inclui os erros da tentativa de sincronização mais recente.


## <a name="duplicate-attributes"></a>Atributos duplicados
### <a name="attributevaluemustbeunique"></a>AttributeValueMustBeUnique
#### <a name="description"></a>Descrição
Esquema do Azure Active Directory não permite duas ou mais objetos de ter o mesmo valor dos seguintes atributos. Que é a que cada objeto no Azure AD for forçado ter um valor exclusivo destes atributos uma determinada instância.

- ProxyAddresses
- UserPrincipalName

Se tentativas de ligação do Azure AD adicionar um novo objeto ou actualizar um objecto existente com um valor para os atributos acima que já esteja atribuído para outro objeto no Azure Active Directory, a operação irá resultar em erros de sincronização "AttributeValueMustBeUnique".
#### <a name="possible-scenarios"></a>Cenários possíveis:
1. Valor duplicado está atribuído a um objeto já sincronizado, que está em conflito com outro objeto sincronizado.

#### <a name="example-case"></a>Caso de exemplo:
1. **O Rodrigo Silva** é um utilizador sincronizado no Azure Active Directory no local Active Directory do contoso.com
2. Do Rodrigo Silva **UserPrincipalName** no local está definido como **bobs@contoso.com**.
3. O Rodrigo também tem os seguintes valores para o atributo **proxyAddresses** :
    - smtp:bobs@contoso.com
    - smtp:bob.smith@contoso.com
    - **smtp:bob@contoso.com**
4. Um novo utilizador, **O Rodrigo Taylor**, é adicionado para o local no Active Directory.
5. De Rodrigo Taylor **UserPrincipalName** está definido como **bobt@contoso.com**.
6. **O Rodrigo Taylor** tem os seguintes valores para o atributo **ProxyAddresses** i. smtp:bobt@contoso.comII. smtp:bob.taylor@contoso.com
7. Objeto de Rodrigo Taylor é sincronizado com o Azure AD com êxito.
8. Administração decidido Atualize o atributo **ProxyAddresses** de Rodrigo Taylor com o seguinte valor: Posso. **smtp:bob@contoso.com**
9. Azure AD tentará atualização de objeto de Rodrigo Taylor no Azure AD com o valor acima, mas que operação falhará como que o valor de ProxyAddresses já está atribuído Rodrigo Casqueiro, que resulta no erro "AttributeValueMustBeUnique".

#### <a name="how-to-fix-attributevaluemustbeunique-error"></a>Como corrigir o erro AttributeValueMustBeUnique
O motivo mais comuns para o erro AttributeValueMustBeUnique é dois objetos com diferentes SourceAnchor \(immutableId\) têm o mesmo valor para os atributos de ProxyAddresses e/ou UserPrincipalName. Para corrigir o erro de AttributeValueMustBeUnique

1.  Identifique o dobro proxyAddresses, userPrincipalName ou outro valor do atributo que está a causar o erro. Também identificar qual dois \(ou mais\) objetos estão, normalmente o conflito. O relatório gerado pelo [Azure AD ligar-se do Estado de funcionamento para a sincronização](https://aka.ms/aadchsyncerrors) pode ajudar a identificar os dois objectos.
2. Identificar que objeto deve continuar a ter o valor de duplicados e que objeto não deve.
3. Remova o valor de duplicados do objeto que não deve ter esse valor. Tenha em atenção que deve fazer a alteração no diretório onde o objeto é com origem. Em alguns casos, poderá precisar de eliminar um dos objetos em conflito.
4. Se a alteração que efetuou nas instalações nas AD, deixe a ligação do Azure AD sincronizar a alteração para o erro para obter fixa.

#### <a name="related-articles"></a>Artigos relacionados
-[Atributos duplicados ou inválidos impedir da sincronização de diretórios no Office 365](https://support.microsoft.com/en-us/kb/2647098)


## <a name="data-validation-failures"></a>Falhas de validação de dados
### <a name="identitydatavalidationfailed"></a>IdentityDataValidationFailed
#### <a name="description"></a>Descrição
Azure Active Directory impõe restrições diversas dados antes de permitir que os dados sejam escritos no diretório. Este é para se certificar de que os utilizadores finais obter as melhores experiências possíveis ao utilizar as aplicações que dependem estes dados.
#### <a name="scenarios"></a>Cenários
um. O valor do atributo UserPrincipalName contém carateres inválidos/não suportadas.
b. O atributo UserPrincipalName não siga formato necessário.
#### <a name="how-to-fix-identitydatavalidationfailed-error"></a>Como corrigir o erro IdentityDataValidationFailed

um. Certifique-se de que o atributo userPrincipalName tem suportado carateres e o formato necessário.

#### <a name="related-articles"></a>Artigos relacionados
- [Preparar o aprovisionamento de utilizadores através da sincronização de diretórios para o Office 365]( https://support.office.com/en-us/article/Prepare-to-provision-users-through-directory-synchronization-to-Office-365-01920974-9e6f-4331-a370-13aea4e82b3e)


### <a name="datavalidationfailed"></a>DataValidationFailed
#### <a name="description"></a>Descrição
Este é um caso muito específico que resulta num erro de sincronização **"DataValidationFailed"** quando o sufixo de UserPrincipalName um utilizador for alterado de um domínio federado para outro domínio federado.

#### <a name="scenarios"></a>Cenários
Para um utilizador sincronizado, o sufixo UserPrincipalName foi alterado a partir de um domínio federado para outro domínio federado no local. Por exemplo, *UserPrincipalName = bob@contoso.com * foi alterada para *UserPrincipalName = bob@fabrikam.com *.

#### <a name="example"></a>Exemplo
1. O Rodrigo Casqueiro, uma conta para Contoso.com, é adicionado como um novo utilizador no Active Directory com o UserPrincipalNamebob@contoso.com
2. O Rodrigo move para uma divisão diferente da Contoso.com denominado Fabrikam.com e dele UserPrincipalName é alterado parabob@fabrikam.com
3. Os domínios contoso.com e fabrikam.com são domínios federados com o Azure Active Directory.
4. UserPrincipalName do Rodrigo não atualizada e resulta num erro de sincronização "DataValidationFailed".

#### <a name="how-to-fix"></a>Como corrigir
Se foi atualizado sufixo UserPrincipalName de um utilizador a partir do bob@ **contoso.com** para bob@ **fabrikam.com**, onde **contoso.com** e **fabrikam.com** estão **domínios federados**, em seguida, siga a seguir passos para corrigir o erro de sincronização

1. Atualização UserPrincipalName do utilizador no Azure AD a partir do bob@contoso.com para bob@contoso.onmicrosoft.com. Pode utilizar o seguinte comando do PowerShell com o módulo Azure AD PowerShell:`Set-MsolUserPrincipalName -UserPrincipalName bob@contoso.com -NewUserPrincipalName bob@contoso.onmicrosoft.com`

2. Permitir que o ciclo de sincronização seguinte a tentativa de sincronização. A sincronização de hora será efetuada com êxito e atualize o UserPrincipalName do Rodrigo para bob@fabrikam.com conforme esperado.


## <a name="largeobject"></a>LargeObject
### <a name="description"></a>Descrição
Quando um atributo excede o limite de tamanho permitido, o limite de comprimento ou o limite de contagem definido pelo esquema do Azure Active Directory, a operação de sincronização irá resultar em erros de sincronização **LargeObject** ou **ExceededAllowedLength** . Normalmente este erro ocorre para os seguintes atributos

- userCertificate
- thumbnailPhoto
- proxyAddresses

### <a name="possible-scenarios"></a>Cenários possíveis
1. Atributo de userCertificate do Rodrigo está a armazenar certificados demasiadas atribuídos para o Rodrigo. Estes podem incluir certificados antigos, expirados.
2. ThmubnailPhoto do Rodrigo definida no Active Directory é demasiado grande para ser sincronizado no Azure AD.
3. Durante a população automática do atributo ProxyAddresses no Active Directory, um objeto tem atribuído > 500 ProxyAddresses.

### <a name="how-to-fix"></a>Como corrigir

1. Certifique-se de que o atributo a causar o erro é no interior a limitação de permitidos.

## <a name="related-links"></a>Ligações relacionadas
- [Localizar objetos do Active Directory no Centro de administração do Active Directory] (https://technet.microsoft.com/library/dd560661.aspx)
- [Como consultar o Azure Active Directory para um objeto com o Azure Active Directory PowerShell](https://msdn.microsoft.com/library/azure/jj151815.aspx)
