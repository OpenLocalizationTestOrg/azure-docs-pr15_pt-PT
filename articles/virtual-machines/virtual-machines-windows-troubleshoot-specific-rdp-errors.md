<properties
    pageTitle="Mensagens de erro RDP específicas para Azure VMs | Microsoft Azure"
    description="Compreender a mensagens de erro específicas que poderá receber ao tentar utilizam a ligação de ambiente de trabalho remoto para uma máquina de virtual do Windows no Azure"
    keywords="Erro de ambiente de trabalho remoto, o erro de ligação de ambiente de trabalho remoto, é possível ligar ao VM, remoto ambiente de trabalho de resolução de problemas"
    services="virtual-machines-windows"
    documentationCenter=""
    authors="iainfoulds"
    manager="timlt"
    editor=""
    tags="top-support-issue,azure-service-management,azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="support-article"
    ms.date="10/14/2016"
    ms.author="iainfou"/>

# <a name="troubleshooting-specific-rdp-error-messages-to-a-windows-vm-in-azure"></a>Resolução de problemas de mensagens de erro RDP específicas para um VM do Windows no Azure
Poderá receber uma mensagem de erro específico quando utilizar a ligação de ambiente de trabalho remoto para uma máquina de virtual do Windows (VM) no Azure. Este artigo fornece detalhes sobre algumas das mensagens de erro mais comuns encontradas, juntamente com os passos para resolvê-los de resolução de problemas. Se estiver a ter problemas estabelecer ligação ao seu VM utilizando o RDP mas não encontrar uma mensagem de erro específico, consulte o [Guia do ambiente de trabalho remoto de resolução de problemas](virtual-machines-windows-troubleshoot-rdp-connection.md).

Para obter informações sobre mensagens de erro específicas, consulte o seguinte:

- [A sessão remota foi desligada porque não existem não existem servidores de licença de ambiente de trabalho remoto disponíveis para fornecer uma licença](#rdplicense).
- [Ambiente de trabalho remoto não é possível localizar o computador "nome"](#rdpname).
- Ocorreu erro [uma autenticação. Autoridade de segurança Local não pode ser contactada](#rdpauth).
- [Erro de segurança do Windows: as suas credenciais não funcionarem](#wincred).
- [Neste computador não é possível ligar para o computador remoto](#rdpconnect).

<a id="rdplicense"></a>
## <a name="the-remote-session-was-disconnected-because-there-are-no-remote-desktop-license-servers-available-to-provide-a-license"></a>A sessão remota foi desligada porque não existem não existem servidores de licença de ambiente de trabalho remoto disponíveis para fornecer uma licença.

Causa: O período de tolerância licenciamento 120 dias para a função de servidor de ambiente de trabalho remoto expirou e tem de instalar licenças.

Como solução, guarde uma cópia local do ficheiro RDP a partir do portal e execute este comando na linha de comandos do PowerShell para ligar. Este passo desativa de licenciamento para apenas essa ligação:

        mstsc <File name>.RDP /admin

Se realmente não precisar de mais de duas ligações de ambiente de trabalho remoto simultâneo para a VM, pode utilizar o Gestor de servidor para remover a função de servidor de ambiente de trabalho remoto.

Para obter mais informações, consulte a mensagem [Azure VM falhar com "Não existem servidores de licenças de ambiente de trabalho remoto disponíveis"](https://blogs.msdn.microsoft.com/mast/2014/01/21/rdp-to-azure-vm-fails-with-no-remote-desktop-license-servers-available/)do blogue.

<a id="rdpname"></a>
## <a name="remote-desktop-cant-find-the-computer-name"></a>Ambiente de trabalho remoto não é possível localizar o computador "nome".

Causa: O cliente de ambiente de trabalho remoto no seu computador não é possível resolver o nome do computador nas definições do ficheiro RDP.

Possíveis soluções:

- Se estiver na intranet de uma organização, certifique-se de que o seu computador tem acesso ao servidor proxy e pode enviar tráfego HTTPS à mesma.

- Se estiver a utilizar um ficheiro RDP armazenado localmente, experimente utilizar que é gerado pelo portal. Este passo garante que tem o nome correto de DNS para máquina virtual, ou o serviço de nuvem e a porta de ponto final da VM. Eis um ficheiro RDP exemplo gerado pelo portal:

        full address:s:tailspin-azdatatier.cloudapp.net:55919
        prompt for credentials:i:1

A parte do endereço deste ficheiro RDP tem:
- O nome de domínio completamente qualificado do serviço em nuvem que contém a VM ("Brinquedos-azdatatier.cloudapp.net" neste exemplo).

- A porta TCP externa do ponto final para o tráfego de ambiente de trabalho remoto (55919).

<a id="rdpauth"></a>
## <a name="an-authentication-error-has-occurred-the-local-security-authority-cannot-be-contacted"></a>Ocorreu um erro de autenticação. Autoridade de segurança Local não pode ser contactada.

Causa: O destino VM não consegue localizar a autoridade de segurança na parte de nome de utilizador das suas credenciais.

Quando o seu nome de utilizador está no formulário de *SecurityAuthority*\\*nome de utilizador* (exemplo: CORP\User1), a parte *SecurityAuthority* é o nome do computador a VM (para a autoridade de segurança local) ou um nome de domínio do Active Directory.

Possíveis soluções:

- Se a conta for local para a VM, certifique-se de que o nome VM está escrito corretamente.

- Se estiver a conta num domínio do Active Directory, verificar a ortografia do nome do domínio.

- Se é uma conta de domínio do Active Directory e o nome de domínio está escrito corretamente, verifique que um controlador de domínio está disponível nesse domínio. É um problema comuns no Azure redes virtuais que contêm os controladores de domínio que não um controlador de domínio está disponível porque não começou. Como solução, pode utilizar uma conta de administrador local em vez de uma conta de domínio.

<a id="wincred"></a>
## <a name="windows-security-error-your-credentials-did-not-work"></a>Erro de segurança do Windows: as suas credenciais não funcionarem.

Causa: O destino VM não é possível validar o nome da conta e a palavra-passe.

Num computador baseado no Windows pode validar as credenciais de uma conta local ou uma conta de domínio.

- Para contas locais, utilize o *nome do computador*\\sintaxe de*nome de utilizador* (exemplo: SQL1\Admin4798).
- Para contas de domínio, utilize o *NomeDomínio*\\sintaxe de*nome de utilizador* (exemplo: CONTOSO\peterodman).

Se tiver promovido a VM controlador de domínio numa nova floresta do Active Directory, é convertida a conta de administrador local que tiver iniciado sessão com uma conta equivalente com a mesma palavra-passe na nova floresta e domínio. A conta local, em seguida, é eliminada.

Por exemplo, se tiver sessão iniciada com a conta local DC1\DCAdmin e, em seguida, promovidos a máquina virtual um controlador de domínio numa nova floresta para o domínio corp.contoso.com, a conta local de DC1\DCAdmin é eliminada e é criada uma nova conta de domínio (CORP\DCAdmin) com a mesma palavra-passe.

Certifique-se de que o nome da conta é um nome que pode verificar a máquina virtual como uma conta válida e se a palavra-passe está correta.

Se precisar de alterar a palavra-passe da conta de administrador local, consulte o artigo [como repor uma palavra-passe ou o serviço de ambiente de trabalho remoto para máquinas de virtuais do Windows](virtual-machines-windows-reset-rdp.md).

<a id="rdpconnect"></a>
## <a name="this-computer-cant-connect-to-the-remote-computer"></a>Neste computador não consegue ligar para o computador remoto.

Causa: A que é utilizada para ligar conta não possui direitos de início de sessão no ambiente de trabalho remoto.

Cada computador com o Windows tem um ambiente de trabalho remoto utilizadores local grupo que contém as contas e grupos que podem iniciar sessão para o mesmo remotamente. Os membros do grupo de administradores locais também têm acesso, apesar das contas não estão listadas no grupo local de utilizadores de ambiente de trabalho remoto. Para máquinas façam parte de um domínio, grupo de administradores locais também contém os administradores de domínio para o domínio.

Certifique-se de que a conta que está a utilizar para se ligar a tem direitos de início de sessão no ambiente de trabalho remoto. Como solução, utilize um domínio ou a conta de administrador local para ligar ao longo do ambiente de trabalho remoto. Para adicionar a conta pretendida para o grupo local de utilizadores de ambiente de trabalho remoto, utilize o snap-in Consola de gestão da Microsoft (**Ferramentas do sistema > Local utilizadores e grupos > grupos > utilizadores de ambiente de trabalho remoto**).


## <a name="next-steps"></a>Próximos passos
Se nenhuma destes erros ocorreu e tiver um problema com ligar utilizando RDP desconhecido, consulte o [Guia do ambiente de trabalho remoto de resolução de problemas](virtual-machines-windows-troubleshoot-rdp-connection.md).

- [Pacote de diagnósticos do Azure IaaS (Windows)](https://home.diagnostics.support.microsoft.com/SelfHelp?knowledgebaseArticleFilter=2976864)
- Para aceder a aplicações em execução numa VM passos de resolução de problemas, consulte o artigo [resolução de problemas acesso à aplicação em execução numa VM Azure](virtual-machines-linux-troubleshoot-app-connection.md).
- Se estiver a ter problemas a utilização da Shell de seguro (SSH) para ligar a uma VM Linux no Azure, consulte o artigo [resolver problemas de SSH ligações para uma VM Linux no Azure](virtual-machines-linux-troubleshoot-ssh-connection.md).