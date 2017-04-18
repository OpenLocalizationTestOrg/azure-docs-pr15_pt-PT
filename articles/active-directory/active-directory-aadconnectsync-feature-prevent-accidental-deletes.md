<properties
   pageTitle="Sincronização do Azure AD Connect: impedir eliminações acidentais | Microsoft Azure"
   description="Este tópico descreve a funcionalidade de eliminações acidentais (evitar eliminações acidentais) prevenir na ligação do Azure AD."
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="09/01/2016"
   ms.author="billmath"/>

# <a name="azure-ad-connect-sync-prevent-accidental-deletes"></a>Sincronização do Azure AD Connect: impedir eliminações acidentais
Este tópico descreve a funcionalidade de eliminações acidentais (evitar eliminações acidentais) impedir na ligação do Azure AD.

Quando instalar a ligação do Azure AD, impedir que acidentais elimina está ativada por predefinição e configurado para não permitir uma exportação com mais de 500 elimina. Esta funcionalidade foi concebida para proteger contra alterações à configuração acidentais e alterações ao seu diretório no local que iria afetam a vários utilizadores e outros objetos.

Cenários comuns quando vir elimina muitos incluem:

- Alterações à [filtragem](active-directory-aadconnectsync-configure-filtering.md) onde um inteiro [or](active-directory-aadconnectsync-configure-filtering.md#organizational-unitbased-filtering) ou [domínio](active-directory-aadconnectsync-configure-filtering.md#domain-based-filtering) está desmarcada.
- Todos os objetos num or são eliminados.
- Um or cujo nome foi mudado é para que todos os objetos são considerados como fora do âmbito para a sincronização.

O valor predefinido de 500 objectos pode ser alterado com PowerShell utilizando `Enable-ADSyncExportDeletionThreshold`. Deve configurar este valor para ajustar o tamanho da sua organização. Visto que o Programador de sincronização executa a cada 30 minutos, o valor é o número de elimina visto dentro de 30 minutos.

Se existirem demasiadas elimina testada a serem exportadas para Azure AD, em seguida, deixa da exportação e recebe uma mensagem de e-mail da seguinte forma:

![Impedir que o e-mail eliminações acidentais](./media/active-directory-aadconnectsync-feature-prevent-accidental-deletes/email.png)

> *Olá (contacto técnico). Ao (tempo), o serviço de sincronização de identidade detetado que o número de eliminações excedido o limiar de eliminação configurado para (nome da organização). Um total de (número) de objetos foram enviados para a eliminação nesta sincronização de identidade executar. Isto cumpridos ou excedido o valor de limiar configurado eliminação de objetos (número). Precisamos para fornecer a confirmação de que estes eliminações devem ser processadas antes de prosseguir será. Consulte o artigo as evitar eliminações acidentais para obter mais informações sobre o erro listado nesta mensagem de correio eletrónico.*

Também pode ver o estado `stopped-deletion-threshold-exceeded` quando procurar no **Gestor de serviço de sincronização** da IU para o perfil de exportação.
![Impedir eliminações acidentais IU Gestor do serviço de sincronização](./media/active-directory-aadconnectsync-feature-prevent-accidental-deletes/syncservicemanager.png)

Se se trata inesperado, investigar e efetuar ações lentes. Para ver quais os objectos estão prestes a ser eliminada, faça o seguinte:

1. Inicie o **serviço de sincronização** a partir do Menu Iniciar.
2. Aceda aos **conectores**.
3. Selecione a conexão com o tipo de **Azure Active Directory**.
4. Em **ações** para a direita, selecione o **Espaço de conector de pesquisa**.
5. No pop-up em **âmbito**, selecione **Perder a ligação uma vez que** e escolha um tempo no passado. Clique em **Procurar**. Esta página disponibiliza uma vista de todos os objetos prestes a ser eliminada. Ao clicar em cada item, pode obter informações adicionais sobre o objeto. Também pode clicar em **Definição da coluna** para adicionar atributos adicionais para que estejam visíveis na grelha de.

![Espaço de conector de pesquisa](./media/active-directory-aadconnectsync-feature-prevent-accidental-deletes/searchcs.png)

Se todas as eliminações são pretendidas, em seguida, faça o seguinte:

1. Para temporariamente desativar esta protecção e deixar essas elimina analisá, execute o cmdlet do PowerShell: `Disable-ADSyncExportDeletionThreshold`. Fornece uma conta de Administrador Global de AD Azure e a palavra-passe.
![Credenciais](./media/active-directory-aadconnectsync-feature-prevent-accidental-deletes/credentials.png)
2. Com o Azure Active Directory Connector ainda selecionado, selecione a ação **Executar** e selecione **Exportar**.
3. Para voltar a ativar a proteção, execute o cmdlet do PowerShell: `Enable-ADSyncExportDeletionThreshold`.

## <a name="next-steps"></a>Próximos passos

**Tópicos de descrição geral**

- [Sincronização do Azure AD Connect: compreender e personalizar a sincronização](active-directory-aadconnectsync-whatis.md)
- [Integrar a suas identidades no local com o Azure Active Directory](active-directory-aadconnect.md)
