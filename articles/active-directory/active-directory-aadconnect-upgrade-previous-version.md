<properties
   pageTitle="Ligação do Azure AD: Atualizar a partir de uma versão anterior | Microsoft Azure"
   description="Explica os diferentes métodos de atualizar para a versão mais recente do Azure Active Directory ligar, incluindo atualização no local e migração de rotação."
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
   ms.workload="Identity"
   ms.date="10/12/2016"
   ms.author="billmath"/>

# <a name="azure-ad-connect-upgrade-from-a-previous-version-to-the-latest"></a>Ligação do Azure AD: Atualização de uma versão anterior para o mais recente
Este tópico descreve os diferentes métodos que pode utilizar para atualizar a instalação de ligação do Azure AD para a versão mais recente. Recomendamos que mantenha-se actualizado com as versões de ligação do Azure AD. Os passos descritos na [entrar migração](#swing-migration) também são utilizados quando fizer uma alteração de configuração substancial.

Se pretende atualizar a partir do DirSync, consulte [atualizar a partir da ferramenta de sincronização do Azure AD (DirSync)](./connect/active-directory-aadconnect-dirsync-upgrade-get-started.md) .

Existem alguns diferentes estratégias de atualizar a ligação do Azure AD.

Método | Descrição
--- | ---
[Atualização automática](active-directory-aadconnect-feature-automatic-upgrade.md) | Para clientes com uma instalação express, este é o método mais fácil.
[Atualização no local](#in-place-upgrade) | Se tiver um único servidor, atualize o instalação no local no mesmo servidor.
[Migração de rotação](#swing-migration) | Com dois servidores, pode preparar um dos servidores com a nova versão ou a configuração e alterar active server quando estiver pronto.

Para as permissões necessárias, consulte o artigo [permissões necessárias para a atualização](./connect/active-directory-aadconnect-accounts-permissions.md#upgrade).

## <a name="in-place-upgrade"></a>Atualização no local
Uma atualização no local funciona para mover a partir do Azure AD Sync ou ligação do Azure AD. Não irá funcionar para DirSync ou para uma solução com FIM + Azure AD conexão.

Este método é preferível quando tem um único servidor e menor que cerca 100.000 objetos. Se existirem quaisquer alterações para as regras de sincronização fora da caixa, uma importação completa e sincronização completa ocorrerem após a atualização. Este procedimento garante que a nova configuração é aplicada a todos os objetos existentes no sistema. Isto pode demorar algumas horas consoante o número de objetos no âmbito do motor de sincronização. Programador de sincronização normal delta, por predefinição a cada 30 minutos está suspensa mas continua a sincronização de palavra-passe. Recomendamos fazer a atualização no local durante um fim de semana. Se não existem alterações na configuração de fora da caixa com a versão de ligação do Azure AD nova, em seguida, uma normal delta para importação/sincronização será iniciado em vez disso.  
![Atualização no local](./media/active-directory-aadconnect-upgrade-previous-version/inplaceupgrade.png)

Se efetuar alterações a regras de sincronização fora da caixa, estes irão ser definir novamente para configuração predefinida no atualização. Para se certificar-se de que a configuração é mantida entre atualiza os, certifique-se das que alterações são feitas conforme descrito no [melhores práticas para alterar a configuração predefinida](active-directory-aadconnectsync-best-practices-changing-default-configuration.md).

## <a name="swing-migration"></a>Migração de rotação
Se tiver uma implementação complexa ou muito muitos objectos, poderá prático para efetuar uma atualização no local no sistema direto. Isto pode para alguns clientes demorar vários dias e durante este período de tempo sem alterações delta serão processadas. Este método também é utilizado quando planeia efetuar alterações consideráveis a sua configuração e pretende experimentá-las antes de estes são enviados para a nuvem.

O método recomendado para estes cenários é utilizar uma migração de rotação. Precisa de (pelo menos) dois servidores, um ativo e um servidor de teste. O servidor activo (linhas contínuas azuis na imagem abaixo) é responsável pela carga de produção ativo. Servidor de teste (linhas tracejadas púrpura na imagem abaixo) é preparado com a nova versão ou a configuração e quando totalmente estiver pronto, este servidor é tornado ativo. O servidor ativo anterior, agora com a versão antiga ou configuração instalado, é feito servidor de teste e atualizado.

Os dois servidores podem utilizar versões diferentes. Por exemplo, o servidor activo que planeia encerrar pode utilizar o Azure AD Sync e o novo servidor transição pode utilizar a ligação do Azure AD. Se utilizar a migração de rotação desenvolver uma nova configuração é uma boa ideia ter as mesmas versões dos dois servidores.  
![Servidor de transição](./media/active-directory-aadconnect-upgrade-previous-version/stagingserver1.png)

Nota: Foi verificou que alguns clientes preferem ter três ou quatro servidores para este cenário. Quando o servidor de transição é atualizado, não tiver um servidor de cópia de segurança em caso de uma [recuperação de falhas](active-directory-aadconnectsync-operations.md#disaster-recovery). Com três ou quatro servidores, pode ser preparado um conjunto de servidores principal/suspensão com a nova versão, garantindo que são sempre um servidor de teste pronto para assumir o controlo.

Também funciona estes passos para mover a partir do Azure AD Sync ou uma solução com FIM + Azure AD conexão. Estes passos não funcionam para o DirSync, mas o método de migração (também designado implementação paralela) de rotação mesmo, com os passos para DirSync pode ser encontrado na [Atualizar Azure Active Directory (DirSync) de sincronizar](./connect/active-directory-aadconnect-dirsync-upgrade-get-started.md).

### <a name="swing-migration-steps"></a>Passos de migração de rotação

1. Se utilizar a ligação do Azure AD em ambos os servidores e planeia utilizar apenas uma alteração da configuração para, certifique-se o servidor ativo e servidor de transição são ambos utilizando a mesma versão. Que torna mais fácil comparar diferenças mais tarde. Se estiver a atualizar a partir do Azure AD Sync, em seguida, nestes servidores tem versões diferentes. Se estiver a atualizar a partir de uma versão mais antiga de ligação do Azure AD, é uma boa ideia começar com os dois servidores utilizando a mesma versão, mas não é necessária.
2. Se que tenha efetuado alguma configuração personalizada e o seu servidor de teste não tivê-lo, siga os passos em [Mover configuração personalizada a partir do active para servidor de teste](#move-custom-configuration-from-active-to-staging-server).
3. Se estiver a atualizar a partir de uma versão anterior de ligação do Azure AD, actualize o servidor de transição para a versão mais recente. Se está a mover a partir do Azure AD Sync, em seguida, instale a ligação do Azure AD no servidor transição.
4. Permitir que o motor de sincronização executar importação completa e sincronização completa no seu servidor de teste.
5. Verificar que a nova configuração não fazer com que quaisquer alterações inesperadas utilizando os passos em **Verificar** confirmar [a configuração de um servidor](active-directory-aadconnectsync-operations.md#verify-the-configuration-of-a-server). Se algo não é como esperado, correto, executar importar e sincronizar e certifique-se até gostar os dados. Podem encontrar no tópico ligado estes passos.
6. Mude o servidor de teste para ser o servidor ativo. Este é o último passo de **Alternar active server** [verificar a configuração de um servidor](active-directory-aadconnectsync-operations.md#verify-the-configuration-of-a-server).
7. Se estiver a atualizar ligação do Azure AD, actualize o servidor agora no modo de para a versão mais recente de transição. Siga os mesmos passos para obter os dados e configuração atualizado. Caso tenha atualizado a partir do Azure AD Sync, agora pode desativar e encerrar o seu servidor antigo.

### <a name="move-custom-configuration-from-active-to-staging-server"></a>Deslocar-se configuração personalizada a partir do active para servidor de teste
Se efetuar alterações à configuração para o servidor activo, é necessário para se certificar de que as mesmas alterações são aplicadas ao servidor de teste.

Podem ser movidas para as regras de sincronização personalizado que criou com o PowerShell. Outras alterações terá de ser aplicadas da mesma forma em ambos os sistemas e não podem ser migradas.

Coisa que deve certificar-se de é configurada da mesma forma em ambos os servidores:

- Ligação para as mesmas florestas.
- Qualquer domínio e or filtragem.
- As mesmo opcionais funcionalidades, como a sincronização de palavra-passe e palavra-passe de escrita não consolidada.

**Deslocar-se de regras de sincronização**  
Para mover uma regra de sincronização personalizadas, faça o seguinte:

1. Abra **O Editor de regras de sincronização** no servidor ativo.
2. Selecione a regra personalizada. Clique em **Exportar**. Isto reúne numa janela do bloco de notas. Guarde o ficheiro temporário com uma extensão de PS1. Isto torna um script PowerShell. Copie o ficheiro ps1 para o servidor de teste.  
![Exportar de regra de sincronização](./media/active-directory-aadconnect-upgrade-previous-version/exportrule.png)
3. O conector GUID é diferente no servidor de transição e têm de ser alterado. Para obter o GUID, inicie o **Editor de regras de sincronização**, selecione uma das regras fora da caixa que representa o mesmo sistema ligado e clique em **Exportar**. Substitua o GUID no seu ficheiro PS1 GUID do servidor de teste.
4. Na linha de comandos do PowerShell, execute o ficheiro PS1. Isto irá criar a regra de sincronização personalizadas no servidor de teste.
5. Se tiver várias regras personalizadas, repita para todas as regras de personalizado.

## <a name="next-steps"></a>Próximos passos
Saiba mais sobre como [integrar a suas identidades no local com o Azure Active Directory](active-directory-aadconnect.md).
