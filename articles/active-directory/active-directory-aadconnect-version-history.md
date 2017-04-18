<properties
   pageTitle="Ligação do Azure AD: Lançamento histórico de versões | Microsoft Azure"
   description="Este tópico apresenta todas as versões de ligação do Azure AD e Azure AD Sync"
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
   ms.date="08/23/2016"
   ms.author="billmath"/>

# <a name="azure-ad-connect-version-release-history"></a>Ligação do Azure AD: Lançamento histórico de versões

A equipa do Azure Active Directory actualiza regularmente ligação do Azure AD com novas funcionalidades. Nem todas as adições são aplicáveis a todas as audiências.

Este artigo destina-se para o ajudar a manter a par das versões que foram disponibilizadas e de compreender se necessita de atualizar para a versão mais recente ou não.

Esta é a lista de tópicos relacionados:

Tópico |  
--------- | --------- |
Passos para atualizar a partir de ligação do Azure AD | Métodos diferentes para [atualização de uma versão anterior para o mais recente](active-directory-aadconnect-upgrade-previous-version.md) do Azure AD Connect lançamento.
Permissões necessárias | Para permissões necessárias para aplicar uma atualização, consulte o artigo [contas e permissões](./connect/active-directory-aadconnect-accounts-permissions.md#upgrade)
Transferir| [Ligar a transferência do Azure AD](http://go.microsoft.com/fwlink/?LinkId=615771)

## <a name="112810"></a>1.1.281.0
Disponibilizado: Agosto de 2016

**Problemas fixos:**

- Alterações para sincronizar o intervalo não executada até após a conclusão do próximo ciclo de sincronização.
- Assistente do Azure AD Connect não aceitar a conta do Azure AD cujo nome de utilizador começa por um caráter de sublinhado (\_).
- Assistente do Azure AD Connect falha autenticar conta Azure AD fornecida se a palavra-passe conta contiver demasiadas carateres especiais. Mensagem de erro "não é possível validar credenciais. Tem Ocorreu um erro inesperado." é devolvido.
- Desinstalar o servidor de transição desativa a sincronização de palavras-passe no inquilino do Azure AD e faz com que a sincronização de palavras-passe falhar com o active server.
- Sincronização de palavra-passe falhar em casos antigas quando não existe nenhuma hash de palavra-passe armazenado no utilizador.
- Quando o servidor de ligação do Azure AD está ativado para o modo de transição, palavra-passe repetição de escrita não está desativada temporariamente.
- Assistente do Azure AD Connect não mostram a sincronização de palavra-passe real e configuração de repetição de escrita de palavra-passe quando o servidor não está no modo de transição. -Lo sempre apresenta-os como desativada.
- As alterações de configuração de sincronização de palavra-passe e palavra-passe de escrita não consolidada não são permanentes pelo Assistente de ligação do Azure AD quando o servidor não está no modo de transição.

**Melhorias:**

- Cmdlet iniciar ADSyncSyncCycle atualizado para indicar se se trata de conseguir com êxito iniciar um novo ciclo de sincronização ou não.
- Cmdlet parar ADSyncSyncCycle adicionada para terminar o ciclo de sincronização e operação que se encontram atualmente em curso.
- Cmdlet parar ADSyncScheduler atualizado para terminar o ciclo de sincronização e operação que se encontram atualmente em curso.
- Quando configurar [As extensões de diretório](active-directory-aadconnectsync-feature-directory-extensions.md) no Assistente de ligação do Azure AD, atributo de AD do tipo "Teletex cadeia" pode ser selecionado.

## <a name="111890"></a>1.1.189.0
Disponibilizado: 2016 Junho

**Fixos problemas e melhoramentos:**

- Ligação do Azure AD agora pode ser instalado num servidor FIPS em conformidade.
    - Para a sincronização de palavras-passe, consulte o artigo [sincronização de palavra-passe e FIPS](active-directory-aadconnectsync-implement-password-synchronization.md#password-synchronization-and-fips)
- Fixo um problema onde um nome de NetBIOS não pode ser resolvido para o FQDN no conector do Active Directory.

## <a name="111800"></a>1.1.180.0
Disponibilizadas: 2016 Maio

**Novas funcionalidades:**

- Avisa-o e ajuda-o a verificar domínios se não tiver fazê-lo antes de executar a ligação do Azure AD.
- Obter suporte adicionada para [Microsoft Cloud Alemanha](active-directory-aadconnect-instances.md#microsoft-cloud-germany).
- Obter suporte adicionada para a mais recente infraestrutura de [nuvem Microsoft Azure administração pública](active-directory-aadconnect-instances.md#microsoft-azure-government-cloud) com novos requisitos de URL.

**Fixos problemas e melhoramentos:**

- Adicionado ao regra Editor de sincronização para o tornar mais fáceis de localizar regras de sincronização de filtragem.
- Desempenho melhorado ao eliminar um espaço de conexão.
- Fixo uma problemas ao mesmo objeto foi eliminado e adicionado na mesma executar (denominados eliminar/adicionar).
- Uma regra de sincronização desactivada já não serão reativar incluído objetos e atualizar atributos num esquema de atualização ou directório.

## <a name="111300"></a>1.1.130.0
Disponibilizado: 2016 Abril

**Novas funcionalidades:**

- Foi adicionado suporte para múltiplos atributos extensões de [Diretório](active-directory-aadconnectsync-feature-directory-extensions.md).
- Foi adicionado suporte para obter mais variações de configuração para a [atualização automática](active-directory-aadconnect-feature-automatic-upgrade.md) sejam considerados elegível para a atualização.
- Adicionado algumas cmdlets do [scheduler personalizado](active-directory-aadconnectsync-feature-scheduler.md#custom-scheduler).

## <a name="111190"></a>1.1.119.0
Disponibilizadas: Março de 2016

**Problemas fixos:**

- Efetuou-se de que a instalação Express não pode ser utilizada no Windows Server 2008 (pré-R2) desde a sincronização de palavra-passe não é suportada neste sistema operativo.
- Atualização do DirSync com uma configuração de filtro personalizado não funcionarem conforme esperado.
- Ao atualizar para uma versão mais recente e não existem alterações na configuração, não deve ser agendada para importação/sincronização completa.

## <a name="111100"></a>1.1.110.0
Disponibilizadas: Fevereiro de 2016

**Problemas fixos:**

- Atualização do lançamentos anteriores não funciona se instalação não estiver na pasta de **Ficheiros c:\Programas\Microsoft** predefinida.
- Se instalar e anular a seleção de **Iniciar o processo de sincronização …** no final do Assistente de instalação, voltar a executar o Assistente de instalação não permitirá do scheduler.
- O scheduler não irá funcionar como esperado nos servidores onde o formato de data/hora não é pt-pt-pt. Também bloqueará `Get-ADSyncScheduler` para devolver vezes corretos.
- Se instalou uma versão anterior de ligação do Azure AD com o ADFS como a opção de iniciar sessão e a atualização, não pode executar o Assistente de instalação novamente.

## <a name="111050"></a>1.1.105.0
Disponibilizado: Fevereiro de 2016

**Novas funcionalidades:**

- Funcionalidade de [atualização automática](active-directory-aadconnect-feature-automatic-upgrade.md) para os clientes de definições de Express.
- Suporte para o administrador global utilizando MFA e PIM no Assistente de instalação.
    - É necessário permitir que o proxy permitir o tráfego para https://secure.aadcdn.microsoftonline-p.com também se utilizar MFA.
    - Tem de adicionar https://secure.aadcdn.microsoftonline-p.com à sua lista de sites fidedignos para MFA funcionar adequadamente.
- Permitir que alterar o método de sessão do utilizador após a instalação inicial.
- Permitir a [filtragem de domínio e or](./connect/active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering) no Assistente de instalação. Isto também permite ligar-se ao florestas onde não em todos os domínios estão disponíveis.
- [Programador](active-directory-aadconnectsync-feature-scheduler.md) está incorporado para o motor de sincronização.

**Funcionalidades promovidas de pré-visualizar a das versões DG:**

- [Dispositivo escrita não consolidada](active-directory-aadconnect-feature-device-writeback.md).
- [Extensões de diretório](active-directory-aadconnectsync-feature-directory-extensions.md).

**Novas funcionalidades de pré-visualizar:**

- A nova predefinição sincronizar ciclo intervalo é de 30 minutos. Utilizado para ser 3 horas para todas as versões anteriores. Adiciona suporte para alterar o comportamento de [Programador](active-directory-aadconnectsync-feature-scheduler.md) .

**Problemas fixos:**

- Página de domínios DNS verificar sempre não reconhece os domínios.
- Solicita credenciais de administrador do domínio ao configurar ADFS.
- No local contas AD não são reconhecidas pelo Assistente de instalação se localizada num domínio com uma árvore de DNS diferentes que o domínio de raiz.

## <a name="1091310"></a>1.0.9131.0
Disponibilizado: Dezembro de 2015

**Problemas fixos:**

- Sincronização de palavra-passe poderão não funcionar quando altera as palavras-passe no AD DS, mas funciona quando definir palavra-passe.
- Quando tiver um servidor proxy, autenticação do Azure AD poderá falhar durante a instalação ou das Nações Unidas para a atualização na página configuração.
- Atualizar a partir de uma versão anterior da ligação do Azure AD com um total do SQL Server falhará se não estiver SA no SQL.
- Atualizar a partir de uma versão anterior da ligação do Azure AD com um controlo remoto do SQL Server irá mostrar o erro "Não é possível aceder à base de dados ADSync SQL".

## <a name="1091250"></a>1.0.9125.0
Disponibilizado: Novembro de 2015

**Novas funcionalidades:**

- Pode reconfigurar o ADFS a fidedignidade Azure AD.
- Pode atualizar o esquema do Active Directory e gerar regras de sincronização.
- Pode desativar uma regra de sincronização.
- Pode definir "AuthoritativeNull" como um novo literal numa regra de sincronização.

**Novas funcionalidades de pré-visualizar:**

- [Azure AD ligar-se do Estado de funcionamento para a sincronização](active-directory-aadconnect-health-sync.md).
- Suporte para [Serviços de domínio do Azure AD](active-directory-passwords-getting-started.md#enable-users-to-reset-or-change-their-ad-passwords) sincronização de palavras-passe.

**Novo cenário suportado:**

- Suporta vários organizações do Exchange no local. Consulte o artigo [implementações híbridas com vários florestas do Active Directory](https://technet.microsoft.com/library/jj873754.aspx) para obter mais informações.

**Problemas fixos:**

- Problemas de sincronização de palavra-passe:
    - Um objeto movido de fora do âmbito para no âmbito não terá a sua palavra-passe sincronizada. Este incudes or e filtragem de atributo.
    - Selecionar um novo or para incluir sincronizar não requer uma sincronização completa da palavra-passe.
    - Quando um utilizador desativado é activado a palavra-passe não sincronizar.
    - Fila de repetir a palavra-passe é infinita e o limite de 5000 objetos para ser foi removida anterior foi removido.
    - [Resolução de problemas de melhorado](active-directory-aadconnectsync-implement-password-synchronization.md#troubleshoot-password-synchronization).
- Não é possível ligar ao Active Directory com o Windows Server 2016 floresta funcional nível.
- Não é possível alterar o grupo utilizado para filtrar os grupo após a instalação inicial.
- Já não irá criar um novo perfil de utilizador no servidor de ligação do Azure AD para cada utilizador efetuar uma alteração de palavra-passe com palavra-passe de escrita não consolidada ativada.
- Não é possível utilizar o número inteiro longo sincronizados âmbitos de regras de valores.
- A caixa de verificação "dispositivo escrita não consolidada" permanece desativada, se existirem controladores de domínio inacessível.

## <a name="1086670"></a>1.0.8667.0
Disponibilizado: Agosto de 2015

**Novas funcionalidades:**

- O Assistente de instalação de ligação do Azure AD agora está localizado a todos os idiomas do Windows Server.
- Desbloquear suporte adicionada para conta ao utilizar a gestão de palavras-passe do Azure AD.

**Problemas fixos:**

- Assistente de instalação do Azure AD Connect falha se outro utilizador continua a instalação em vez da pessoa que iniciou pela primeira vez a instalação.
- Se uma desinstalação anterior falha de ligação do Azure AD para desinstalar a ligação do Azure AD sync correctamente, não é possível reinstalar o.
- Não é possível instalar uma ligação do Azure AD utilizando Express instalar se o utilizador não está no domínio de raiz da floresta ou se for utilizada uma versão não inglesa do Active Directory.
- Se não pode ser resolvido o FQDN da conta de utilizador do Active Directory, é apresentada uma mensagem de erro "Não foi possível consolidar o esquema" enganadora.
- Se a conta utilizada no Active Directory Connector for alterada fora do assistente, o assistente irá falhar em execuções subsequentes.
- Ligação do Azure AD, por vezes, não consegue instalar num controlador de domínio.
- Não é possível ativar e desativar "Modo de teste", se tiverem sido adicionados atributos da extensão.
- Palavra-passe repetição de escrita na alguma configuração falha devido a uma palavra-passe incorretas no conector do Active Directory.
- Não pode ser actualizado DirSync se dn for utilizado na filtragem de atributo.
- Utilização da CPU excessiva ao utilizar a reposição de palavra-passe.

**Funcionalidades de pré-visualização removido:**

- A funcionalidade de pré-visualização [que utilizador escrita não consolidada](active-directory-aadconnect-feature-preview.md#user-writeback) temporariamente foi removido com base nos comentários dos nossos clientes de pré-visualização. -Lo será adicionado novamente mais tarde quando podemos tenham abordadas o comentários fornecido.

## <a name="1086410"></a>1.0.8641.0
Disponibilizado: Junho de 2015

**Lançamento inicial da ligação do Azure AD.**

Nome alterado a partir do Azure AD Sync a ligação do Azure AD.

**Novas funcionalidades:**

- Instalação [Express definições](./connect/active-directory-aadconnect-get-started-express.md)
- Pode [configurar o ADFS](./connect/active-directory-aadconnect-get-started-custom.md#configuring-federation-with-ad-fs)
- Pode [atualizar a partir do DirSync](./connect/active-directory-aadconnect-dirsync-upgrade-get-started.md)
- [Impedir eliminações acidentais](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md)
- Introduzidos [modo de transição](active-directory-aadconnectsync-operations.md#staging-mode)

**Novas funcionalidades de pré-visualizar:**

- [Repetição de escrita de utilizador](active-directory-aadconnect-feature-preview.md#user-writeback)
- [Grupo repetição de escrita](active-directory-aadconnect-feature-preview.md#group-writeback)
- [Dispositivo repetição de escrita](active-directory-aadconnect-feature-device-writeback.md)
- [Extensões de diretório](active-directory-aadconnect-feature-preview.md#directory-extensions)


## <a name="104940501"></a>1.0.494.0501
Disponibilizadas: Maio de 2015

**Novo requisito:**

- Azure AD Sync requer agora o .net framework versão 4.5.1 ser instalado.

**Problemas fixos:**

- Repetição de escrita de palavra-passe a partir do Azure AD está a falhar com um erro de conectividade servicebus.

## <a name="104910413"></a>1.0.491.0413
Disponibilizado: Abril de 2015

**Fixos problemas e melhoramentos:**

- O Active Directory Connector não processar elimina corretamente se estiver ativada a Reciclagem e existem vários domínios na floresta.
- O desempenho de operações de importação foi melhorado para o conector do Azure Active Directory.
- Quando um grupo tiver excedido o limite de associação (por predefinição, o limite está definido para 50k objetos), o grupo foi eliminado no Azure Active Directory. O novo comportamento é que o grupo permanecerá, é devolvido um erro e não existem novas alterações de associação serão exportadas.
- Não é possível aprovisionar um novo objeto se um eliminar faseado com o mesmo DN já se encontra no espaço de conexão.
- Alguns objectos estão marcados para a ser sincronizada durante uma sincronização delta apesar de não existe nenhuma alteração testada no objeto.
- Forçar uma sincronização de palavra-passe também a irá remover preferida lista Cc.
- CSExportAnalyzer tem problemas com alguns Estados de objetos.

**Novas funcionalidades:**

- Agora pode ligar a uma associação ao tipo de objecto "ANY" no MV.

## <a name="104850222"></a>1.0.485.0222
Disponibilizado: Fevereiro de 2015

**Melhorias:**

- Desempenho melhorado importar.

**Problemas fixos:**

- O atributo cloudFiltered utilizado pelo atributo filtragem respeita a sincronização de palavra-passe. Objetos filtrados deixará de estar no âmbito para a sincronização de palavra-passe.
- Em raras situações em que a topologia tinha muito muitos controladores de domínio, a sincronização de palavra-passe não funciona.
- "Deixou de servidor" ao importar a partir do Azure AD conexão depois de gestão de dispositivos ser ativado no Azure AD/Intune.
- Participar externa principais de segurança (FSP) a partir de vários domínios na mesma floresta provoca um erro de associação ambígua.

## <a name="104751202"></a>1.0.475.1202
Disponibilizado: Dezembro de 2014

**Novas funcionalidades:**

- Agora é suportada para efetuar a sincronização de palavra-passe com o atributo baseado filtragem. Para obter mais detalhes, consulte o artigo [sincronização de palavras-passe com a filtragem](active-directory-aadconnectsync-configure-filtering.md).
- O atributo msDS-ExternalDirectoryObjectID é escrito novamente AD. Suporte para aplicações do Office 365 utilizar oauth2 ainda para aceder à ambas Online ao fazê e caixas de correio numa implementação híbrida do Exchange no local.

**Problemas de atualização fixos:**

- Uma versão mais recente do Assistente de início de sessão do está disponível no servidor.
- Um caminho de instalação personalizada foi utilizado para instalar o Azure AD Sync.
- Um critério de associação personalizado inválida bloqueia a atualização.

**Outras correções:**

- Fixar os modelos para o Office Pro Plus.
- Problemas de instalação fixo causados pela nomes de utilizador que começam com um travessão.
- Fixo perder a definição de sourceAnchor ao executar o Assistente de instalação uma segunda vez.
- Rastreio ETW fixo para a sincronização de palavra-passe

## <a name="104701023"></a>1.0.470.1023
Disponibilizado: Outubro de 2014

**Novas funcionalidades:**

- Sincronização de palavras-passe a partir de múltiplas no local AD a Azure AD.
- Instalação da IU localizada a todos os idiomas do Windows Server.

**Atualizar a partir das versões de AADSync 1.0 DG**

Se já tiver instalado o Azure AD Sync, existe um passo adicional que terá de efetuar caso tenha alterado qualquer uma das regras de sincronização out of box. Depois de ter actualizado para o 1.0.470.1023 Liberte, a sincronização regras que tenha modificado são duplicadas. Para cada regra de sincronização modificados, faça o seguinte:

- Localize a regra de sincronização que tenha modificado e tome nota das alterações.
- Elimine a regra de sincronização.
- Localize a nova regra de sincronização criado por Azure AD Sync e volte a aplicar as alterações.

**Permissões para a conta do AD**

A conta do AD tem de conceder permissões adicionais para que possam ler os hashes de palavra-passe a partir do AD. As permissões para conceder são denominadas "Replicação de alterações do diretório" e "Replicação de diretório todas as alterações". Ambas as permissões necessárias para que possam ler os hashes de palavra-passe

## <a name="104190911"></a>1.0.419.0911
Disponibilizado: Setembro de 2014

**Lançamento inicial do Azure AD Sync.**

## <a name="next-steps"></a>Próximos passos
Saiba mais sobre como [integrar a suas identidades no local com o Azure Active Directory](active-directory-aadconnect.md).
