<properties
    pageTitle="Sincronização do Azure AD Connect: configurar a filtragem | Microsoft Azure"
    description="Explica como configurar a filtragem na ligação do Azure AD sync."
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
    ms.date="09/13/2016"
    ms.author="andkjell;markvi"/>


# <a name="azure-ad-connect-sync-configure-filtering"></a>Sincronização do Azure AD Connect: configurar a filtragem
Com a filtragem, pode controlar quais os objectos deverão aparecer no Azure AD a partir do seu diretório no local. A configuração predefinida leva todos os objetos em todos os domínios nas florestas configurados. Em geral, este é a configuração recomendada. Os utilizadores finais utilizando cargas de trabalho do Office 365, como o Exchange Online e o Skype para empresas, beneficiar a partir de uma lista completa de endereços Global para que possam enviar mensagens de e-mail e ligar a todos. Com a configuração de predefinida, obteria a mesma experiência fariam com uma implementação no local do Exchange ou do Lync.

Em alguns casos, é necessário para fazer algumas alterações na configuração predefinido. Aqui estão alguns exemplos:

- Planeia utilizar a [topologia de AD diretório Azure múltiplos](active-directory-aadconnect-topologies.md#each-object-only-once-in-an-azure-ad-directory). Em seguida, necessitar de aplicar um filtro para controlar que objeto deve ser sincronizado para um determinado diretório do Azure AD.
- Executar um piloto para o Office 365 ou do Azure e pretender apenas um subconjunto dos utilizadores no Azure AD. No piloto pequeno, não é importante ter uma lista completa de endereços Global para demonstrar a funcionalidade.
- Dispõe de várias contas de serviço e de outras contas que não sejam pessoal que não pretende no Azure AD.
- Por razões de conformidade não está a eliminar qualquer utilizador contas no local. Desativar apenas-los. Mas Azure AD apenas pretende ativas contas para estar presentes.

Este artigo aborda como configurar os diferentes métodos de filtragem.

> [AZURE.IMPORTANT]A Microsoft não suporta modificação ou uma operação da sincronização de ligação do Azure AD fora essas ações formal documentados. Qualquer uma das seguintes ações poderá resultar num estado inconsistente ou não suportada de ligação do Azure AD sync e como resultado, Microsoft não pode fornecer suporte técnico para essas implementações.

## <a name="basics-and-important-notes"></a>Noções básicas e notas importantes
Na ligação do Azure AD sync, pode ativar a filtragem em qualquer altura. Se começar com uma configuração predefinida da sincronização de diretórios e, em seguida, configurar a filtragem, os objetos que são filtrados já não são sincronizados para Azure AD. Como resultado esta alteração, são eliminados os objetos no Azure AD que foram sincronizados anteriormente, mas que foram filtrados, em seguida, no Azure AD.

Antes de começar a fazer alterações a filtragem, certifique-se [desativar a tarefa agendada](#disable-scheduled-task) para que não acidentalmente exportar as alterações que ainda não verificado parece correta.

Uma vez que a filtragem pode remover vários objetos ao mesmo tempo, que pretende certificar-se de que os novos filtros estão corretos antes de começar a exportar quaisquer alterações para Azure AD. Depois de ter concluído os passos de configuração, recomenda-se vivamente que segue os [passos de verificação](#apply-and-verify-changes) antes de exportar e efetuar alterações ao Azure AD.

Para proteger contra eliminar muitos objectos por acidente, a funcionalidade [impedir eliminações acidentais](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md) está ativada por predefinição. Se eliminar muitos objectos devido a filtragem (500 por predefinição), é necessário seguir os passos neste artigo para permitir que o elimina para percorrer para Azure AD.

Se utilizar uma compilação antes de Novembro de 2015 ([1.0.9125](active-directory-aadconnect-version-history.md#1091250)), efetue uma alteração à configuração do filtro e utiliza a sincronização de palavra-passe, em seguida, tem de acionar uma sincronização completa de todas as palavras-passe depois de concluir a configuração. Para obter passos sobre como acionar uma sincronização completa de palavra-passe Consulte [acionar uma sincronização completa de todas as palavras-passe](active-directory-aadconnectsync-implement-password-synchronization.md#trigger-a-full-sync-of-all-passwords). Se estiver no 1.0.9125 ou posterior, em seguida, a ação regular **sincronização completa** também calcula se as palavras-passe devem ser sincronizadas e este passo extra já não é necessário.

Se objetos de **utilizador** inadvertidamente tiverem sido eliminados no Azure AD devido a um erro de filtragem, pode recriar os objetos de utilizador no Azure AD removendo as configurações de filtragem e, em seguida, volte a sincronizar o seu diretórios. Esta ação restaura os utilizadores a partir da Reciclagem no Azure AD. No entanto, não é possível anular outros tipos de objeto. Por exemplo, se eliminar acidentalmente um grupo de segurança e foi utilizada para ACL um recurso, o grupo e respetivas ACL não não possível recuperar.

Ligação do Azure AD elimina apenas objetos-tem uma vez consideradas no âmbito. Se existirem objetos do Azure AD que foram criados por outro motor de sincronização e estes objectos não estão a ser âmbito, adicionar filtragem não removê-los. Por exemplo, se começar com um servidor do DirSync e ter criada uma cópia completa do diretório da sua inteira no Azure AD e instalar um novo servidor de sincronização de ligação do Azure AD em paralelo com filtragem ativado desde o início, não remove os objectos extra criados por DirSync.

A configuração de filtragem é retida quando instalar ou atualizar para uma versão mais recente da ligação do Azure AD. É sempre prática recomendada para verificar que a configuração não foi inadvertidamente alterada depois de uma atualização para uma versão mais recente antes de executar o ciclo da primeira sincronização.

Se tiver mais do que uma floresta, em seguida, as configurações de filtragem descritas neste tópico terá de ser aplicadas a cada floresta (partindo do princípio de que pretende que a mesma configuração para todos os).

### <a name="disable-scheduled-task"></a>Desativar a tarefa agendada
Para desativar o agendador incorporado que accionadores um ciclo de sincronização a cada 30 minutos, siga estes passos:

1. Aceda a um PowerShell linha de comandos.
2. Executar `Set-ADSyncScheduler -SyncCycleEnabled $False` para desativar o programador.
3. Efetue as alterações como documentado neste tópico.
4. Executar `Set-ADSyncScheduler -SyncCycleEnabled $True` para ativar o agendador novamente.

**Se utilizar uma ligação do Azure AD compilação antes de 1.1.105.0**  
Para desativar a tarefa agendada que accionadores um ciclo de sincronização cada 3 horas, siga estes passos:

1. Inicie **O Programador de tarefas** a partir do menu Iniciar.
2. Diretamente em **Biblioteca do Programador de tarefas**, localize a tarefa com o nome **Scheduler do Azure AD Sync**, o botão direito do rato e selecione **desativar**.  
![Programador de tarefas](./media/active-directory-aadconnectsync-configure-filtering/taskscheduler.png)  
3. Agora pode fazer alterações à configuração e executar o motor de sincronização manualmente a partir da consola do **Gestor de serviço de sincronização** .

Depois de ter concluído todas as suas alterações filtragem, não se esqueça de entrar anterior e **Ativar** a tarefa novamente.

## <a name="filtering-options"></a>Opções de filtragem
Os seguintes tipos de configuração de filtragem podem ser aplicados para a ferramenta de sincronização de diretórios:

- [**Grupo com base**](active-directory-aadconnect-get-started-custom.md#sync-filtering-based-on-groups): filtrar com base num único grupo apenas pode ser configurada numa instalação inicial utilizando o Assistente de instalação. -Não é ainda mais abrangido neste tópico.

- [**Baseado no domínio**](#domain-based-filtering): esta opção permite-lhe selecionar os domínios que são sincronizados para Azure AD. Também permite adicionar e remova os domínios da configuração do motor de sincronização se efetuar alterações à sua infraestrutura no local depois de ter instalado a ligação do Azure AD sync.

- [**Organizacional unidade – com base em**](#organizational-unitbased-filtering): esta opção de filtragem permite-lhe selecionar quais ou sincronizar para o Azure AD. Esta opção está para todos os tipos de objeto ou selecionado.

- [**Com base no atributo**](#attribute-based-filtering): esta opção permite-lhe filtrar objetos com base em valores de atributo sobre os objectos. Também pode ter diferentes filtros para tipos diferentes de objeto.

Pode utilizar várias opções de filtragem ao mesmo tempo. Por exemplo, pode utilizar com base em or filtragem para incluir apenas os objetos no uma or e na mesma tempo com base no atributo filtragem para filtrar os objetos ainda mais. Quando utilizar vários métodos de filtragem, os filtros utilizam um e lógico entre os filtros.

## <a name="domain-based-filtering"></a>Filtragem baseada no domínio
Esta secção fornece os passos para configurar o seu filtro de domínio. Se tiver adicionado ou removido de domínios na sua floresta depois de ter instalado a ligação do Azure AD, também tem de atualizar a configuração de filtragem.

A forma preferida para alterar a filtragem baseada no domínio é ao executar a instalação assistente e alteração de [domínio e ou filtrar](active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering). O Assistente de instalação é automatizar todas as tarefas documentadas neste tópico.

Deve seguir estes passos apenas se por algum motivo não conseguir executar o Assistente de instalação.

Configuração de filtragem baseada no domínio é constituído por estes passos:

- [Selecione os domínios](#select-domains-to-be-synchronized) que devem ser incluídos na sincronização.
- Para cada domínio adicionado e removido, ajuste a [Executar perfis](#update-run-profiles).
- [Aplicar e verifique se as alterações](#apply-and-verify-changes).

### <a name="select-domains-to-be-synchronized"></a>Selecione domínios a ser sincronizada
**Para definir o filtro de domínio, efetue os seguintes passos:**

1. Inicie sessão no servidor que está a ser executada a ligação do Azure AD sync utilizando uma conta que é um membro do grupo de segurança **ADSyncAdmins** .
2. Inicie o **Serviço de sincronização** a partir do menu Iniciar.
3. Selecione **conectores** e na lista de **conectores** , selecione a conexão com o tipo de **Serviços de domínio do Active Directory**. **Ações**, selecione **Propriedades**.  
![Propriedades do conector](./media/active-directory-aadconnectsync-configure-filtering/connectorproperties.png)  
4. Clique em **Configurar a partições diretório**.
5. Na lista **Selecione a partições do directório de** , selecione e anular a seleção de domínios conforme necessário. Certifique-se de que apenas as partições que pretende sincronizar estão seleccionadas.  
![Partições](./media/active-directory-aadconnectsync-configure-filtering/connectorpartitions.png)  
Se tiver alterado no local infraestrutura de AD e domínios adicionados ou removidos da floresta, em seguida, clique no botão **Atualizar** para obter uma lista atualizada. Quando atualizar, são-lhe pedido para introduzir as credenciais. Fornece as credenciais de acesso de leitura para o seu no local Active Directory. -Não tem de ser o utilizador que é povoado previamente na caixa de diálogo.  
![Atualização necessária](./media/active-directory-aadconnectsync-configure-filtering/refreshneeded.png)  
6. Quando tiver terminado, feche a caixa de diálogo de **Propriedades** ao clicar em **OK**. Se tiver removido domínios da floresta, um pop-up de mensagem indicando que foi removido um domínio e que a configuração serão possível limpar.
7. Continue para ajustar a [Executar perfis](#update-run-profiles).

### <a name="update-run-profiles"></a>Atualizar os perfis de executar
Se tiver atualizado o seu filtro de domínio, também terá de atualizar os perfis de executar.

1. Na lista de **conectores** , certifique-se de conexão que alterou no passo anterior está selecionada. **Ações**, selecione **Configurar perfis de executar**.  
![Executar o conector de perfis](./media/active-directory-aadconnectsync-configure-filtering/connectorrunprofiles1.png)  

Precisa de ajustar os perfis seguintes:

- Importação completa
- Sincronização completa
- Importar delta
- Sincronização de variação
- Exportar

Para cada um dos cinco perfis, siga os passos seguintes para cada domínio **adicionados** :

1. Selecione o perfil executar e clique em **Novo passo**.
2. Na página **Configurar passo** , no menu pendente **tipo** , selecione o tipo de passo com o mesmo nome como o perfil que está a configurar. Em seguida, clique em **seguinte**.  
![Executar o conector de perfis](./media/active-directory-aadconnectsync-configure-filtering/runprofilesnewstep1.png)  
3. Na página **Configuração de conexão** , no menu pendente **partição** , selecione o nome do domínio que adicionou ao seu filtro de domínio.  
![Executar o conector de perfis](./media/active-directory-aadconnectsync-configure-filtering/runprofilesnewstep2.png)  
4. Para fechar a caixa de diálogo **Configurar executar perfil** , clique em **Concluir**.

Para cada um dos cinco perfis, siga os passos seguintes para cada domínio **removido** :

1. Selecione o perfil em execução.
2. Se o **valor** do atributo **partição** for um GUID, selecione o passo executar e clique em **Eliminar passo**.  
![Executar o conector de perfis](./media/active-directory-aadconnectsync-configure-filtering/runprofilesdeletestep.png)  

O resultado deve ser que cada domínio que pretende sincronizar deve ser listado como um passo de cada perfil executar.

Para fechar a caixa de diálogo **Configurar perfis de executar** , clique em **OK**.

- Para concluir a configuração, [aplicar e verifique se as alterações](#apply-and-verify-changes).

## <a name="organizational-unitbased-filtering"></a>Filtragem baseada na unidade de organizacional
A forma preferida para alterar a filtragem baseada em or é ao executar a instalação assistente e alteração de [domínio e ou filtrar](active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering). O Assistente de instalação é automatizar todas as tarefas documentadas neste tópico.

Deve seguir estes passos apenas se por algum motivo não conseguir executar o Assistente de instalação.

**Para configurar a filtragem de unidade – baseada na organização, efetue os seguintes passos:**

1. Inicie sessão no servidor que está a ser executada a ligação do Azure AD sync utilizando uma conta que é um membro do grupo de segurança **ADSyncAdmins** .
2. Inicie o **Serviço de sincronização** a partir do menu Iniciar.
3. Selecione **conectores** e na lista de **conectores** , selecione a conexão com o tipo de **Serviços de domínio do Active Directory**. **Ações**, selecione **Propriedades**.  
![Propriedades do conector](./media/active-directory-aadconnectsync-configure-filtering/connectorproperties.png)  
4. Clique em **Configurar a partições do diretório**, selecione o domínio que pretende configurar e, em seguida, clique em **contentores**.
5. Quando lhe for pedido, fornece as credenciais de acesso de leitura ao seu Active Directory no local. -Não tem de ser o utilizador que é povoado previamente na caixa de diálogo.
6. Na caixa de diálogo **Seleccionar Membros em contentores** , desmarque a ou que não pretende sincronizar com o diretório de nuvem e, em seguida, clique em **OK**.  
![OR](./media/active-directory-aadconnectsync-configure-filtering/ou.png)  
  - O contentor de **computadores** deve estar selecionado para os computadores Windows 10 a ser sincronizada com êxito para Azure AD. Se os computadores de domínio associado estiver localizados na outras ou, certifique-se de que aqueles estão selecionados.
  - O contentor **ForeignSecurityPrincipals** deverá ser selecionado se tiver várias florestas com fidedignidades. Neste contentor permite que os membros do grupo de segurança de floresta cruzada ser resolvido.
  - O or **RegisteredDevices** deverá ser selecionada se ativou a funcionalidade de escrita não consolidada dispositivo. Se utilizar outra funcionalidade de repetição de escrita, tal como grupo de escrita não consolidada, certifique-se destas que localizações estão selecionadas.
  - Selecione quaisquer outras or onde estão localizados os utilizadores, iNetOrgPersons, grupos, contactos e computadores. Na imagem, todos estes estão localizados no ManagedObjects OU.
7. Quando tiver terminado, feche a caixa de diálogo de **Propriedades** ao clicar em **OK**.
8. Para concluir a configuração, [aplicar e verifique se as alterações](#apply-and-verify-changes).

## <a name="attribute-based-filtering"></a>Filtrar com base no atributo
Certifique-se de que está incluídos de Novembro de 2015 ([1.0.9125](active-directory-aadconnect-version-history.md#1091250)) ou criar mais tarde para que estes passos trabalhar.

Atributo com base filtragem é a forma mais flexível para objetos de filtro. Pode utilizar potência de [aprovisionamento declarativa](active-directory-aadconnectsync-understanding-declarative-provisioning.md) para controlar quase todas as aspecto do quando um objeto deve ser sincronizados com a Azure AD.

A filtragem pode ser aplicada ambos na [entrada](#inbound-filtering) do Active Directory para o metaverse e de [saída](#outbound-filtering) a partir do metaverse para Azure AD. Recomenda-se para aplicar filtragem em entrada, uma vez que é mais fácil manter. Filtragem de saída só deverá ser utilizado se for necessário para participar objectos a partir de mais do que uma floresta antes da avaliação pode tomar local.

### <a name="inbound-filtering"></a>A filtragem de entrada
Filtragem baseada em entrada está a utilizar a configuração predefinida onde objetos aceder ao Azure AD têm de ter o cloudFiltered de atributo metaverse não definido para um valor a ser sincronizada. Se este atributo está definido como **Verdadeiro**, o objeto não está sincronizado. -Deve não ser definido como **Falso** pelo estrutura. Para se certificar-se de que tem de outras regras a capacidade para contribuir um valor, este atributo só é suposto que contêm os valores **Verdadeiro** ou **Nulo** (em falta).

A filtragem de entrada, utiliza o potência de **âmbito** para determinar quais os objectos devem ou não devem ser sincronizados. Este é onde pode fazer ajustes para a ajustar aos requisitos da sua organização. O módulo do âmbito tem o **grupo** e **cláusula** para determinar se uma regra de sincronização deverá estar no âmbito. Um **grupo** contém um ou vários **cláusula**. Existe um e lógico entre várias cláusulas e uma lógica ou entre vários grupos.

Ver um exemplo diga-nos:  
![Âmbito](./media/active-directory-aadconnectsync-configure-filtering/scope.png) Isto deve ser lido como **(departamento = IT) ou (departamento = Vendas e c = US)**.

Nos passos abaixo e amostras, utilize o objeto do utilizador como exemplo, mas pode utilizar esta opção para todos os tipos de objeto.

Os exemplos abaixo, o valor da precedência iniciar com 500. Este valor assegura que estas regras são avaliadas após as regras de caixa de saída (precedência inferior, superior valor numérico).

#### <a name="negative-filtering-do-not-sync-these"></a>Negativo de filtragem, "não sincronizar estes"
O exemplo seguinte, o utilizador filtra (não sincronizar) todos os utilizadores que **extensionAttribute15** têm o valor **sem sincronização**.

1. Inicie sessão no servidor que está a ser executada a ligação do Azure AD sync utilizando uma conta que é um membro do grupo de segurança **ADSyncAdmins** .
2. Inicie o **Editor de regras de sincronização** a partir do menu Iniciar.
3. Certifique-se de **que entrada** está selecionada e clique em **Adicionar nova regra**.
4. Atribuir a regra um nome descritivo, tais como "*na partir do AD – utilizador DoNotSyncFilter*". Selecione a floresta correta, o **utilizador** como o **tipo de objeto CS**e **pessoa** como o **tipo de objeto MV**. Com o **Tipo de ligação**, selecione **participar** e na precedência, escreva um valor atualmente não utilizado por outro sincronização regra (por exemplo 500) e, em seguida, clique em **seguinte**.  
![1 Descrição de entrada](./media/active-directory-aadconnectsync-configure-filtering/inbound1.png)  
5. No **Scoping filtro**, clique em **Adicionar grupo**, clique em **Adicionar cláusula**e no atributo selecione **ExtensionAttribute15**. Certifique-se que o operador está definido como **igual** e escreva o valor **sem sincronização** na caixa valor. Clique em **seguinte**.  
![Âmbito de 2 de entrada](./media/active-directory-aadconnectsync-configure-filtering/inbound2.png)  
6. Deixe as regras de **participar** em branco e, em seguida, clique em **seguinte**.
7. Clique em **Adicionar transformação**, selecione o **FlowType** para **constante**, selecione o atributo de destino **cloudFiltered** e na caixa de texto de origem, escreva **True**. Clique em **Adicionar** para guardar a regra.  
![Transformação 3 de entrada](./media/active-directory-aadconnectsync-configure-filtering/inbound3.png)
8. Para concluir a configuração, [aplicar e verifique se as alterações](#apply-and-verify-changes).

#### <a name="positive-filtering-only-sync-these"></a>Positivo filtragem, "apenas sincronizar estes"
Expressar filtragem positivo pode ser mais um desafio, uma vez que têm a considerar também objetos que não estão óbvios a ser sincronizada, tal como salas de conferência.

A opção de filtragem positiva requer duas regras de sincronização. Um (ou vários) com o âmbito correcto dos objetos para sincronizar e uma segunda Sincronizar tudo capturas regra esse filtro de todos os objetos que ainda não foram identificadas como um objeto que deve ser sincronizado.

No exemplo seguinte, sincronizar apenas objetos de utilizador onde o atributo departamento tem o valor de **vendas**.

1. Inicie sessão no servidor que está a ser executada a ligação do Azure AD sync utilizando uma conta que é um membro do grupo de segurança **ADSyncAdmins** .
2. Inicie o **Editor de regras de sincronização** a partir do menu Iniciar.
3. Certifique-se de **que entrada** está selecionada e clique em **Adicionar nova regra**.
4. Dê um nome descritivo, tal como "*na partir do AD – utilizador vendas sincronizar*" à regra. Selecione a floresta correta, o **utilizador** como o **tipo de objeto CS**e **pessoa** como o **tipo de objeto MV**. Com o **Tipo de ligação**, selecione **participar** e na precedência, escreva um valor atualmente não utilizado por outro sincronização regra (por exemplo 501) e, em seguida, clique em **seguinte**.  
![Descrição 4 de entrada](./media/active-directory-aadconnectsync-configure-filtering/inbound4.png)  
5. No **Scoping filtro**, clique em **Adicionar grupo**, clique em **Adicionar cláusula**e no atributo selecione **departamento**. Certifique-se que o operador está definido como **igual** e escreva o valor " **vendas** " na caixa valor. Clique em **seguinte**.  
![Âmbito de 5 de entrada](./media/active-directory-aadconnectsync-configure-filtering/inbound5.png)  
6. Deixe as regras de **participar** em branco e, em seguida, clique em **seguinte**.
7. Clique em **Adicionar transformação**, selecione o **FlowType** para **constante**, selecione o atributo de destino **cloudFiltered** e na caixa de texto de origem, escreva **False**. Clique em **Adicionar** para guardar a regra.  
![Transformação 6 de entrada](./media/active-directory-aadconnectsync-configure-filtering/inbound6.png)  
Este é um caso especial onde definir cloudFiltered explicitamente FALSO.

    Temos agora criar a regra de sincronização capturas tudo.

8. Atribuir a regra um nome descritivo, tais como "*na partir do AD – filtro do utilizador capturas tudo*". Selecione a floresta correta, o **utilizador** como o **tipo de objeto CS**e **pessoa** como o **tipo de objeto MV**. Com o **Tipo de ligação**, selecione **participar** e na precedência, escreva um valor atualmente não utilizado por outro sincronização regra (por exemplo 600). Tem selecionada uma precedência valor mais elevada (precedência inferior) à regra de sincronização anterior, mas também deixado algum espaço por isso Pedimos pode adicionar mais regras de filtragem de sincronização mais tarde, quando quiser iniciar sincronização de departamentos adicionais. Clique em **seguinte**.  
![Descrição 7 de entrada](./media/active-directory-aadconnectsync-configure-filtering/inbound7.png)  
9. Deixe o **filtro de Scoping** vazia e clique em **seguinte**. Um filtro vazio indica que a regra deve ser aplicada a todos os objetos.
10. Deixe as regras de **participar** em branco e, em seguida, clique em **seguinte**.
11. Clique em **Adicionar transformação**, selecione o **FlowType** para **constante**, selecione o atributo de destino **cloudFiltered** e na caixa de texto de origem, escreva **True**. Clique em **Adicionar** para guardar a regra.  
![Transformação 3 de entrada](./media/active-directory-aadconnectsync-configure-filtering/inbound3.png)  
12. Para concluir a configuração, [aplicar e verifique se as alterações](#apply-and-verify-changes).

Se precisar, em seguida, pode criar mais regras do primeiro tipo onde incluir mais objectos no nossa sincronização.

### <a name="outbound-filtering"></a>Filtragem de saída
Em alguns casos, é necessário efetuar a filtragem apenas depois dos objetos criaram a metaverse. Por exemplo, poderia, ser necessário para ver o atributo de correio a partir do floresta de recursos e o atributo userPrincipalName do floresta de conta para determinar se um objeto deve ser sincronizado. Nestes casos, pode criar a filtragem da regra de saída.

Neste exemplo, alterar os filtragem por isso, apenas utilizadores onde o correio e userPrincipalName terminam com @contoso.com são sincronizados:

1. Inicie sessão no servidor que está a ser executada a ligação do Azure AD sync utilizando uma conta que é um membro do grupo de segurança **ADSyncAdmins** .
2. Inicie o **Editor de regras de sincronização** a partir do menu Iniciar.
3. Em **Tipo de regras**, clique em **saída**.
4. Localize a regra denominada **fora para AAD – SOAInAD participar do utilizador**. Clique em **Editar**.
5. No pop-up, responda **Sim** para criar uma cópia da regra.
6. Na página **Descrição** , altere a precedência para um valor não utilizado, por exemplo, 50.
7. Clique em **filtro Scoping** na navegação à esquerda. Clique em **Adicionar a cláusula**, no atributo selecionar **correio**, selecione operador **ENDSWITH**e no tipo de valor **@contoso.com**. Clique em **Adicionar cláusula**, selecione atributo **userPrincipalName**, em Selecionar operador **ENDSWITH**e no tipo de valor **@contoso.com**.
8. Clique em **Guardar**.
9. Para concluir a configuração, [aplicar e verifique se as alterações](#apply-and-verify-changes).

## <a name="apply-and-verify-changes"></a>Aplicar e verifique se as alterações
Depois de efetuar as alterações à configuração, estes terá de ser aplicadas para os objetos já se encontra presentes no sistema. Também pode ser que devem ser processados objetos não está no motor de sincronização e o motor de sincronização necessita de ler o sistema de origem novamente para verificar o seu conteúdo.

Caso tenha alterado configuração utilizando o **domínio** ou a filtragem de **unidade organizacional** , em seguida, tem de fazer **Importar completo** seguido de **sincronização Delta**.

Se tiver alterado configuração utilizando **atributo** filtragem, em seguida, tem de fazer **sincronização completa**.

Siga os passos seguintes:

1. Inicie o **Serviço de sincronização** a partir do menu Iniciar.
2. Selecione **conectores** e na lista de **conectores** , selecione a conexão onde que efetuou uma alteração anterior de configuração. **Ações**, selecione **Executar**.  
![Conector executar](./media/active-directory-aadconnectsync-configure-filtering/connectorrun.png)  
3. Em **Executar perfis**, selecione a operação mencionada na secção anterior. Se o que precisa para executar duas ações, execute o segundo depois de concluída a primeira parte (coluna **Estado** é **inactivo** para o conector selecionado).

Após a sincronização, todas as alterações são testadas para ser exportados. Antes de efetuar as alterações no Azure AD realmente, que pretende verificar se todas estas alterações estão corretas.

1. Iniciar um pedido de cmd e vá para`%Program Files%\Microsoft Azure AD Sync\bin`
2. Execute:`csexport "Name of Connector" %temp%\export.xml /f:x`  
Pode encontrar o nome da conexão no serviço de sincronização. Tem um nome semelhante a "contoso.com – AAD" para Azure AD.
3. Execute:`CSExportAnalyzer %temp%\export.xml > %temp%\export.csv`
4. Tem agora um ficheiro no % temp % denominado export.csv que pode ser analisadas no Microsoft Excel. Este ficheiro contém todas as alterações que estão prestes a ser exportados.
5. Faça as alterações necessárias para os dados ou a configuração e executar estes passos novamente (importar, sincronizar e verificar) até que as alterações que estão prestes a ser exportados são esperadas.

Quando estiver satisfeito, exporte as alterações para Azure AD.

1. Selecione **conectores** e na lista de **conectores** , selecione a ligação do Azure AD. **Ações**, selecione **Executar**.
2. Em **Executar perfis**, selecione **Exportar**.
3. Se as alterações à configuração eliminar muitos objectos, em seguida, verá um erro relativo à exportação quando o número for mais do que o limiar configurado (por predefinição 500). Se vir este erro, em seguida, é necessário desligar temporariamente a funcionalidade [impedir eliminações acidentais](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md).

Agora está na altura de ativar o agendador novamente.

1. Inicie **O Programador de tarefas** a partir do menu Iniciar.
2. Diretamente em **Biblioteca do Programador de tarefas**, localize a tarefa com o nome **Scheduler do Azure AD Sync**, o botão direito do rato e selecione **Ativar**.

## <a name="next-steps"></a>Próximos passos
Saiba mais sobre a configuração de [sincronizar a ligação do Azure AD](active-directory-aadconnectsync-whatis.md) .

Saiba mais sobre como [integrar a suas identidades no local com o Azure Active Directory](active-directory-aadconnect.md).
