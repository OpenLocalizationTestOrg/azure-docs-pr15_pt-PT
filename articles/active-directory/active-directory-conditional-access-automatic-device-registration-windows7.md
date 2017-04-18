<properties
    pageTitle="# Configurar registo de dispositivo automático para o Windows 7 domínio aderido dispositivos | Microsoft Azure"
    description="Passos para configurar o seu domínio do Windows 7 associadas dispositivos para registar automaticamente com Azure AD. e os passos para implementar o pacote de software de registo de dispositivo para o seu domínio do Windows 7 associadas dispositivos utilizando um sistema de distribuição de software como o Gestor de configuração do Centro de sistema."
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
    ms.author="MarkVi"/>

# <a name="configure-automatic-device-registration-for-windows-7-domain-joined-devices"></a>Configurar registo de dispositivo automático para dispositivos de domínio associado do Windows 7

Como um administrador de TI pode configurar os seus dispositivos de domínio associado do Windows 7 para registar automaticamente com Azure AD. Para fazê-lo, tem de implementar o pacote de software de registo de dispositivo para o seu domínio do Windows 7 associadas dispositivos utilizando um sistema de distribuição de software como o Gestor de configuração do Centro de sistema. Certifique-se de que leia e concluir os pré-requisitos listados na registo automático de dispositivo com dispositivos associadas Azure para Windows domínio do Active Directory.

>[AZURE.NOTE]
 Para mais recentes instruções sobre como configurar o registo de dispositivo automático Consulte, [como configurar o registo automático de domínio do Windows associadas dispositivos com o Azure Active Directory](active-directory-conditional-access-automatic-device-registration-setup.md).

##<a name="installing-the-device-registration-software-package-on-windows-7-domain-joined-devices"></a>Instalar o pacote de software de registo de dispositivo no Windows 7 domínio associadas dispositivos

Registo de dispositivo para o Windows 7 está disponível como um [pacote MSI transferível](https://connect.microsoft.com/site1164). O pacote tem de estar instalado no Windows 7 máquinas que estejam associadas a um domínio do Active Directory. Deve implementar o pacote utilizando um sistema de distribuição de software como o Gestor de configuração do Centro de sistema. O pacote de MSI suporta as opções de instalação automática padrão, utilizando o /quiet parâmetro.
O pacote de software está disponível para transferência nas do [Web site do Microsoft ligar](https://connect.microsoft.com/site1164). Aqui pode selecionar e, em seguida, transferir participar do local de trabalho para o Windows 7.

![](./media/active-directory-conditional-access/device-registration-process-windows7.gif)

## <a name="workplace-join-with-azure-active-directory"></a>Associação do local de trabalho com o Azure Active Directory
Registo de dispositivo para o Windows 7 dispositivos de domínio associado não exigir ou incluir uma interface de utilizador. Depois de instalado no computador, qualquer utilizador do domínio que inicia sessão no computador será automaticamente e silenciosamente registado com um objeto de dispositivo no Azure AD. Haverá um objeto de dispositivo no Azure AD para cada utilizador registado do dispositivo físico.

O programa de instalação cria uma tarefa agendada no sistema que é executado no contexto do utilizador e é acionado no início de sessão do utilizador. A tarefa em silêncio regista o utilizador e dispositivo com Azure AD depois do utilizador sinais-in está concluído.
Tarefa agendada pode ser encontrada na biblioteca do Programador de tarefas em **Microsoft** > **Participar local de trabalho**.
A tarefa vai executar e registar todos os utilizadores do Active Directory que inicie sessão no computador.
A ilustração seguinte apresenta o processo passo a passo para o registo de dispositivo automático.

![](./media/active-directory-conditional-access/automatic-device-registration-windows7.png)

1. Um utilizador (trabalhador informações) inicia sessão no computador cliente Windows 7 com credenciais de domínio do Active Directory.
1. A tarefa agendada participar local de trabalho é executada.
1. O utilizador é silenciosamente autenticado com o AD FS utilizando a autenticação integrado do Windows.
1. O PC Windows 7 está registado ao utilizador no Azure AD.
1. Um objeto de dispositivo e o certificado é criada no Azure AD. O objecto representa o user@device.
1. O certificado participar local de trabalho está armazenado no computador.

## <a name="unregistering-your-windows-7-domain-joined-devices"></a>Anular o registo de domínio do Windows 7 associadas dispositivos

Pode optar por anular o registo seus dispositivos Windows 7 de domínio associado ao efetuar o seguinte: desinstalar o local de trabalho participar pacote de software a partir do seu domínio do Windows 7 associadas dispositivos utilizando um sistema de distribuição de software como o Gestor de configuração do Centro de sistema.

Em seguida, abra uma linha de comandos no computador Windows 7 e execute o seguinte comando para anular o registo do dispositivo:

    %ProgramFiles%\Microsoft Workplace Join\AutoWorkplace.exe /leave

>[AZURE.NOTE]
>Este comando deve ser executado no contexto de cada utilizador do domínio que tem sessão iniciada no computador.
Visualizador de eventos e erros do Windows 7 domínio associadas dispositivos.

O registo de eventos do Windows no Windows 7 computador irá apresentar mensagens relacionadas com o local de trabalho participar. Pode encontrar mensagens para eventos participar local de trabalho com e sem êxito. O registo de eventos pode ser localizados no Visualizador de eventos em registos de serviços e aplicações > área de trabalho Microsoft participar.

## <a name="additional-topics"></a>Tópicos adicionais

- [Descrição geral do registo de dispositivo do Active Directory Azure](active-directory-conditional-access-device-registration-overview.md)
- [Registo de dispositivo automático com dispositivos Azure Active Directory for Windows Domain-Joined](active-directory-conditional-access-automatic-device-registration.md)
- [Configurar registo de dispositivo automático para dispositivos de domínio associado Windows 8.1](active-directory-conditional-access-automatic-device-registration-windows-8-1.md)
- [Registo de dispositivo automático com dispositivos façam parte de um domínio do Azure Active Directory para o Windows 10](active-directory-azureadjoin-devices-group-policy.md)
