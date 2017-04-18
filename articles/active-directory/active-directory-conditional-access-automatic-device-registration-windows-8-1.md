<properties
    pageTitle="Configurar registo de dispositivo automático para dispositivos de domínio associado Windows 8.1 | Microsoft Azure"
    description=" Passos para configurar a política de grupo para dispositivos façam parte de um domínio do Windows 8.1 registar automaticamente com Azure AD. "
    services="active-directory"
    documentationCenter=""
    authors="femila"
    manager="swadhwa"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/21/2016"
    ms.author="Markvi"/>

# <a name="configure-automatic-device-registration-for-windows-81-domain-joined-devices"></a>Configurar registo de dispositivo automático para dispositivos de domínio associado Windows 8.1

Pode utilizar uma política de grupo do Active Directory para configurar os dispositivos de domínio associado Windows 8.1 para registar automaticamente com Azure AD. Para configurar a política de grupo, tem de ter pelo menos um domínio associada Windows Server 2012 R2 ou Windows 8.1 máquina com a funcionalidade de gestão de políticas de grupo instalada. Assim que esta política de grupo esteja ativada para o seu domínio, qualquer utilizador do domínio que inicia sessão no computador será automaticamente e em silêncio registado com um objeto de dispositivo no Azure AD. Haverá um objeto de dispositivo no Azure AD para cada utilizador registado do dispositivo físico. Certifique-se de que leia e concluir os pré-requisitos a partir da inscrição do dispositivo automática com Azure Active Directory for Windows Domain-Joined dispositivos.

>[AZURE.NOTE]
 Para mais recentes instruções sobre como configurar o registo de dispositivo automático Consulte, [como configurar o registo automático de domínio do Windows associadas dispositivos com o Azure Active Directory](active-directory-conditional-access-automatic-device-registration-setup.md).



## <a name="configure-the-group-policy-for-your-windows-81-domain-joined-devices"></a>Configurar a política de grupo para os seus dispositivos de domínio associado Windows 8.1

1. Abra o Gestor de servidor e navegue para **Ferramentas** > **Gestão da política de grupo**.
2. A partir de gestão da política de grupo, navegue para o nó do domínio que corresponde ao domínio na qual pretende ativar a **Associação automática do local de trabalho**.
3. **Objectos de política de grupo** com o botão direito e selecione **Novo**. Dê um nome, por exemplo, a **Associação automática do local de trabalho**ao objeto política de grupo. Clique em **OK**.
4. Botão direito do rato no seu novo objeto de política de grupo e, em seguida, selecione **Editar**.
5. Navegue para a **configuração do computador** > **políticas** > **Modelos administrativos** > **componentes do Windows** > **participar local de trabalho**.
6. Botão direito do rato automaticamente computadores de cliente do local de trabalho participar e, em seguida, selecione **Editar**.
7. Selecione o botão de opção ativada e, em seguida, clique em aplicar. Clique em **OK**.
8. Agora pode ligar o objeto de política de grupo a uma localização da sua escolha. Para ativar esta política para todos os dispositivos Windows 8.1 de domínio associado na sua organização, ligação a política de grupo para o domínio.

## <a name="unregistering-your-windows-81-domain-joined-devices"></a>Anular o registo de domínio do Windows 8.1 associadas dispositivos

Pode optar por anular o registo seus dispositivos Windows 8.1 de domínio associado ao efetuar o seguinte: modificar as definições de política de grupo participar local de trabalho criadas na secção anterior. Definir o automaticamente local de trabalho associação computadores política de cliente para desativado. Isto irá impedir novos dispositivos associem automaticamente ao local de trabalho.

Anular o registo de domínio associado Windows 8.1 máquinas existentes por seguinte uma das duas opções abaixo:

* Opção 1: **Unregister um Windows 8.1 domínio associadas dispositivo utilizando as definições do PC**
  1. Num dispositivo Windows 8.1, navegue para **Definições do PC** > **rede** > **local de trabalho**
  2. Selecione **Sair**.
Tem de ser repetido este processo para cada utilizador do domínio que tem sessão iniciada no computador e foi automaticamente local de trabalho associada.

* Opção 2: Anular o registo de um dispositivo associado ao Windows 8.1 domínio utilizar um script
    1. Abra uma linha de comandos no computador Windows 8.1 e execute o seguinte comando:` %SystemRoot%\System32\AutoWorkplace.exe leave`
   
Este comando deve ser executado no contexto de cada utilizador do domínio que tem sessão iniciada no computador.

##<a name="event-viewer--errors-for-windows-81-domain-joined-devices"></a>Dispositivos de domínio associado Visualizador de eventos e erros para o Windows 8.1

O registo de eventos do Windows num computador Windows 8.1 apresenta mensagens relacionadas com o registo de dispositivo. Irá encontrar mensagens para eventos com e sem êxito. 

O registo de eventos pode ser localizados no Visualizador de eventos em aplicações e serviços **registos** > **Microsoft** > **Windows > local de trabalho participar**.

##<a name="additional-details"></a>Detalhes adicionais

A política de grupo permite que uma tarefa agendada no sistema que é executado no contexto do utilizador e é acionado no início de sessão do utilizador. A tarefa irá silenciosamente registe-se o utilizador e o dispositivo com o Azure AD após o início de sessão no está concluída. Tarefa agendada pode ser encontrada em dispositivos Windows 8.1 na biblioteca do Programador de tarefas em **Microsoft** > **Windows** > **Participar local de trabalho**. A tarefa vai executar e registar todos os utilizadores do Active Directory que início de sessão para. 

## <a name="additional-topics"></a>Tópicos adicionais
- [Descrição geral do registo de dispositivo do Active Directory Azure](active-directory-conditional-access-device-registration-overview.md)
- [Registo de dispositivo automático com dispositivos façam parte de um domínio do Azure Active Directory para o Windows 10](active-directory-conditional-access-automatic-device-registration.md)
- [Configurar registo de dispositivo automático para dispositivos de domínio associado do Windows 7](active-directory-conditional-access-automatic-device-registration-windows7.md)

