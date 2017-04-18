<properties
   pageTitle="Sincronização do Azure AD Connect: tarefas operacionais e considerações | Microsoft Azure"
   description="Este tópico descreve tarefas operacionais para ligação do Azure AD sync e como preparar para este componente a funcionar."
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

# <a name="azure-ad-connect-sync-operational-tasks-and-consideration"></a>Sincronização do Azure AD Connect: tarefas operacionais e consideração
O objectivo deste tópico é descrever tarefas operacionais para ligação do Azure AD sync.

## <a name="staging-mode"></a>Modo de transição
Modo de transição pode ser utilizado para vários cenários, incluindo:

-   Disponibilidade de alta.
-   Teste e implementar novas alterações de configuração.
-   Introduzir um novo servidor e encerrar antigo.

Com um servidor no modo de teste, pode fazer alterações na configuração e pré-visualizar as alterações antes de fazer o servidor ativo. Também permite executar importação completa e sincronização completa para verificar que todas as alterações são esperadas antes de efetuar estas alterações para o ambiente de produção.

Durante a instalação, pode selecionar o servidor para ser no **modo de transição**. Esta ação faz com que o servidor ativo para importar e a sincronização, mas não são executadas quaisquer exportações. Um servidor no modo de transição não está em execução sincronização de palavra-passe ou escrita não consolidada palavra-passe, mesmo se tiver selecionado estas funcionalidades durante a instalação. Quando desativar o modo de transição, o servidor começa a exportar, permite a sincronização de palavra-passe e permite a palavra-passe de escrita não consolidada.

Ainda pode forçar uma exportação utilizando o Gestor de serviço de sincronização.

Um servidor no modo de transição continua a receber as alterações do Active Directory e Azure AD. Tem sempre uma cópia das alterações mais recentes e tomar muito rápida podem sobre as responsabilidades do outro servidor. Se efetuar alterações à configuração para o servidor principal, é da sua responsabilidade para tornar as mesmas alterações no servidor no modo de transição.

Para aqueles que com dados de conhecimento da mais antigas tecnologias de sincronização, o modo de transição é diferente, uma vez que o servidor tem a sua própria base de dados do SQL. Esta arquitetura permite que o servidor de modo transição estar localizados num centro de dados diferente.

### <a name="verify-the-configuration-of-a-server"></a>Verifique a configuração de um servidor
Para aplicar este método, siga estes passos:

1. [Preparar](#prepare)
2. [Importar e sincronizar](#import-and-synchronize)
3. [Verifique se](#verify)
4. [Servidor activo mudar](#switch-active-server)

#### <a name="prepare"></a>Preparar

1. Instalar a ligação do Azure AD, selecione o **modo de transição**e anular a seleção de **Iniciar a sincronização** na última página no Assistente de instalação. Neste modo permite-lhe executar o motor de sincronização manualmente.
![ReadyToConfigure](./media/active-directory-aadconnectsync-operations/readytoconfigure.png)
2. Início de sessão desativa/início de sessão e a partir do menu Iniciar selecione **O serviço de sincronização**.

#### <a name="import-and-synchronize"></a>Importar e sincronizar

1. Selecione **conectores**e selecione a conexão primeira com o tipo de **Serviços de domínio do Active Directory**. Clique em **Executar**, selecione **importação completa**e em **OK**. Efetue estes passos para todas as conexões deste tipo.
2. Selecione a conexão com o tipo de **Azure Active Directory (Microsoft)**. Clique em **Executar**, selecione **importação completa**e em **OK**.
3. Certifique-se que no separador conectores ainda estiver selecionado. Para cada conector com o tipo de **Active Directory Domain Services**, clique em **Executar**, selecione **A sincronização Delta**e **OK**.
4. Selecione a conexão com o tipo de **Azure Active Directory (Microsoft)**. Clique em **Executar**, selecione **A sincronização Delta**e **OK**.

Agora tem testado exportar muda para Azure AD e no local AD (se estiver a utilizar implementação híbrida do Exchange). Próximos passos permitem-lhe inspecionar o que está prestes a alterar antes de começar a exportação para os directórios realmente.

#### <a name="verify"></a>Verifique se

1. Iniciar um pedido de cmd e vá para`%ProgramFiles%\Microsoft Azure AD Sync\bin`
2. Execute:`csexport "Name of Connector" %temp%\export.xml /f:x`  
Pode encontrar o nome da conexão no serviço de sincronização. Tem um nome semelhante a "contoso.com – AAD" para Azure AD.
3. Execute:`CSExportAnalyzer %temp%\export.xml > %temp%\export.csv`
4. Tem um ficheiro no % temp % denominado export.csv que pode ser analisadas no Microsoft Excel. Este ficheiro contém todas as alterações que estão prestes a ser exportados.
5. Faça as alterações necessárias para os dados ou a configuração e executar estes passos novamente (importar e sincronizar e verificar) até que as alterações que estão prestes a ser exportados são esperadas.

**Noções sobre o ficheiro export.csv**

A maior parte do ficheiro é facilmente compreensíveis. Algumas abreviaturas para compreender o conteúdo:

- OMODT – tipo de alteração do objeto. Indica se o funcionamento no nível do objeto é um adicionar, atualizar ou eliminar.
- AMODT – tipo de alteração de atributo. Indica se a operação a um nível de atributo é uma adicionar, atualizar ou eliminar.

Se o valor do atributo for múltiplos valor, não cada alteração é apresentada. Apenas o número de valores adicionados e removidos está visível.

#### <a name="switch-active-server"></a>Servidor activo mudar

1. No servidor atualmente ativo, desativar o servidor (DirSync/FIM/Azure AD Sync) para que não está a exportar para Azure AD ou defini-lo no modo (ligação do Azure AD) de transição.
2. Execute o Assistente de instalação no servidor no **modo de transição** e desativar o **modo de transição**.
![ReadyToConfigure](./media/active-directory-aadconnectsync-operations/additionaltasks.png)

## <a name="disaster-recovery"></a>Recuperação de falhas
Parte da estrutura de implementação está planear o que fazer se existir uma falhas onde perderá o servidor de sincronização. Existem diferentes modelos para utilização e qual deles pretende utilizar depende diversos fatores, incluindo:

-   O que é o seu tolerância para não a ser capaz de efetuar alterações a objetos no Azure AD durante o tempo de inatividade?
-   Se utilizar a sincronização de palavra-passe, os utilizadores aceitar que tenham que utilizar a palavra-passe antiga no Azure AD caso eles alterá-lo no local?
-   Tem uma dependência de operações em tempo real, por exemplo, a palavra-passe de escrita não consolidada?

Consoante as respostas a estas questões e política da sua organização, uma das seguintes estratégias pode ser implementada:

-   Reconstrua quando for necessário.
-   Ter um servidor de espera reserva, conhecido como **modo de transição**.
-   Utilize máquinas virtuais.

Se não utilizar a base de dados SQL Express incorporado, devem consultar também a secção de [Elevada disponibilidade de SQL](#sql-high-availability) .

### <a name="rebuild-when-needed"></a>Reconstruir quando necessário
Uma estratégia de viável é planear uma recompilação do servidor quando for necessário. Normalmente, a instalar o motor de sincronização e não que podem ser concluídas a importação inicial e sincronização dentro de alguns horas. Se não houver um servidor de reserva disponíveis, é possível utilizar temporariamente um controlador de domínio para alojar o motor de sincronização.

O servidor do motor de sincronização não armazenar qualquer estado sobre os objectos para a base de dados pode ser recriada a partir de dados no Active Directory e Azure AD. O atributo **sourceAnchor** é utilizado para participar os objetos no local e na nuvem. Se reconstruir o servidor com existente objetos no local e na nuvem, em seguida, o motor de sincronização corresponde a esses objetos em conjunto novamente no reinstalação. Coisas que precisa para documentar e guardar são as alterações à configuração efetuadas para o servidor, tais como as regras de filtragem e a sincronização. Tem de ser novamente aplicadas estas configurações personalizadas antes de começar a sincronizar.

### <a name="have-a-spare-standby-server---staging-mode"></a>Ter um servidor de espera reserva - modo de transição
Se tiver um ambiente mais complexo, está a ter um ou mais servidores espera é recomendada. Durante a instalação, pode ativar a um servidor de estar no **modo de transição**.

Para obter mais detalhes, consulte o artigo [modo de transição](#staging-mode).

### <a name="use-virtual-machines"></a>Utilizar máquinas virtuais
É um método comuns e suportado executar o motor de sincronização numa máquina virtual. No caso de anfitrião do tem um problema, a imagem com o servidor do motor de sincronização pode ser migrada para outro servidor.

### <a name="sql-high-availability"></a>Disponibilidade de elevada de SQL
Se não estiver a utilizar o SQL Server Express que vem com ligação do Azure AD, em seguida, elevada disponibilidade para SQL Server serão igualmente considerada. A solução de disponibilidade apenas elevada suportada é clusters de SQL. As soluções não suportadas incluem espelhando as e sempre no.

## <a name="next-steps"></a>Próximos passos

**Tópicos de descrição geral**  

- [Sincronização do Azure AD Connect: compreender e personalizar a sincronização](active-directory-aadconnectsync-whatis.md)  
- [Integrar a suas identidades no local com o Azure Active Directory](active-directory-aadconnect.md)  
