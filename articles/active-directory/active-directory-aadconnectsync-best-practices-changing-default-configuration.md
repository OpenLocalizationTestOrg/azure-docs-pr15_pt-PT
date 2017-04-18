<properties
    pageTitle="Sincronização do Azure AD Connect: melhores práticas para alterar a configuração predefinida | Microsoft Azure"
    description="Fornece melhores práticas para alterar a configuração predefinida de ligação do Azure AD sync."
    services="active-directory"
    documentationCenter=""
    authors="andkjell"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/22/2016"
    ms.author="markvi;andkjell"/>


# <a name="azure-ad-connect-sync-best-practices-for-changing-the-default-configuration"></a>Sincronização do Azure AD Connect: melhores práticas para alterar a configuração predefinida
É o objetivo neste tópico descrever suportadas e não suportadas alterações a ligação do Azure AD sync.

A configuração criada por uma ligação do Azure AD funciona "tal como está" para a maioria dos ambientes que são sincronizados no local Active Directory com Azure AD. No entanto, em alguns casos, é necessário aplicar algumas alterações a uma configuração para satisfazer uma necessidade específica ou requisito.

## <a name="changes-to-the-service-account"></a>Alterações à conta de serviço
Sincronização do Azure AD Connect está em execução numa conta de serviço criada pelo Assistente de instalação. Esta conta de serviço mantém as chaves de encriptação à base de dados utilizada pelo sincronização. É criada com uma palavra-passe de tempo de 127 caracteres e a palavra-passe está configurada para não expirar.

- É **não suportadas** para alterar ou repor a palavra-passe da conta de serviço. Fazê-lo destrói as chaves de encriptação e o serviço não é possível aceder à base de dados e não é possível iniciar sessão.

## <a name="changes-to-the-scheduler"></a>Alterações do scheduler
Começar com as versões de compilação 1.1 (Fevereiro de 2016) pode configurar o [Programador de](active-directory-aadconnectsync-feature-scheduler.md) ter um ciclo de sincronização diferente do predefinido 30 minutos.

## <a name="changes-to-synchronization-rules"></a>Alterações a regras de sincronização
O Assistente de instalação fornece uma configuração que é suposto para trabalhar para os cenários mais comuns. No caso de precisar de efetuar alterações na configuração, tem de seguir estas regras para ainda tem uma configuração suportada.

- Pode [alterar o atributo monetários](active-directory-aadconnectsync-change-the-configuration.md#other-common-attribute-flow-changes) se os fluxos de atributo direta predefinido, não são adequados para a sua organização.
- Se pretende [fluxo um atributo](active-directory-aadconnectsync-change-the-configuration.md#do-not-flow-an-attribute) e remova valores atributo existente no Azure AD, em seguida, terá de criar uma regra para este cenário.
- [Desativar uma regra de sincronização indesejados](#disable-an-unwanted-sync-rule) em vez de eliminá-la. Uma regra eliminada é recriada durante uma atualização.
- Para [alterar uma regra de caixa de saída](#change-an-out-of-box-rule), deve fazer uma cópia da regra original e desativar a regra de caixa de saída. O Editor de regras de Sincronização pede e ajuda-o.
- Exporte as suas regras de sincronização personalizado utilizando o Editor de regras de sincronização. O editor de fornece-lhe um script PowerShell, que pode utilizar para facilmente recrie-as num cenário de recuperação falhas.

>[AZURE.WARNING] As regras de sincronização out of box ter uma impressão digital. Se efetuar uma alteração para estas regras, a impressão digital já não é correspondentes. Poderá ter problemas no futuro quando tenta para aplicar uma nova versão do ligação do Azure AD. Apenas altere a forma como é descrito neste artigo.

### <a name="disable-an-unwanted-sync-rule"></a>Desativar uma regra de sincronização indesejados
Não elimine uma regra de sincronização out of box. É recriada a durante a atualização seguinte.

Em alguns casos, o Assistente de instalação tem produzidos uma configuração que não está a funcionar para a sua topologia. Por exemplo, se tiver uma topologia de floresta recurso de conta, mas tiver expandido o esquema na floresta de conta com o esquema do Exchange, em seguida, regras para o Exchange são criadas para floresta de conta e floresta de recursos. Neste caso, é necessário desativar a regra de sincronização no Exchange.

![Regra de sincronização desativado](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/exchangedisabledrule.png)

Na imagem acima, o Assistente de instalação encontrou um esquema de Exchange 2003 antigo na floresta de conta. Esta extensão esquema foi adicionado antes de floresta de recursos foi introduzida num ambiente do Fabrikam. Para assegurar que são sincronizados sem atributos da aplicação antiga do Exchange, a regra de sincronização deve ser desactivada conforme mostrado.

### <a name="change-an-out-of-box-rule"></a>Alterar uma regra de out of box
Se precisar de efetuar alterações a uma regra de caixa de saída, deve fazer uma cópia da regra out of box e desativar a regra original. Em seguida, efetue as alterações à regra clonada. O Editor de regras de sincronização está a ajudar com esses passos. Quando abre uma regra de caixa de saída, são apresentadas com esta caixa de diálogo:  
![Aviso de regra de caixa](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/warningoutofboxrule.png)

Selecione **Sim** para criar uma cópia da regra. A regra clonada, em seguida, é aberta.  
![Regra clonada](./media/active-directory-aadconnectsync-best-practices-changing-default-configuration/clonedrule.png)

Esta regra clonado, faça as alterações necessárias para âmbito, participar e transformações.

## <a name="next-steps"></a>Próximos passos

**Tópicos de descrição geral**

- [Sincronização do Azure AD Connect: compreender e personalizar a sincronização](active-directory-aadconnectsync-whatis.md)
- [Integrar a suas identidades no local com o Azure Active Directory](active-directory-aadconnect.md)
