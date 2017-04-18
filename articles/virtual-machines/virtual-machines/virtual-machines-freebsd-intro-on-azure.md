<properties
   pageTitle="Introdução ao FreeBSD no Azure | Microsoft Azure"
   description="Saiba como utilizar máquinas virtuais de FreeBSD no Azure"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="KylieLiang"
   manager="timlt"
   editor=""
   tags="azure-service-management"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="08/27/2016"
   ms.author="kyliel"/>

# <a name="introduction-to-freebsd-on-azure"></a>Introdução ao FreeBSD no Azure
Este tópico fornece uma descrição geral de execução de uma máquina de virtual FreeBSD no Azure.

## <a name="overview"></a>Descrição geral
FreeBSD para Microsoft Azure é num sistema operativo do computador avançadas utilizada para os servidores moderna power, ambientes de trabalho e incorporado plataformas. A imagem FreeBSD 10.3 é fornecida pela Microsoft Corporation e está disponível no Azure. Baseia-se a edição de FreeBSD 10.3 e Azure VM convidado agente [2.1.4](https://github.com/Azure/WALinuxAgent/releases/tag/v2.1.4) está instalado. O agente é responsável por comunicação entre a VM FreeBSD e o Azure ferro para operações, tais como aprovisionamento VM na primeira utilização (nome de utilizador, palavra-passe, nome de anfitrião, etc.) e ativar a funcionalidade para as extensões de VM selectivas.
Como para as versões futuras da FreeBSD, a estratégia é para se manter atual e disponibilizar as versões mais recentes pouco depois são lançadas pela equipa de engenharia de lançamento do FreeBSD. Próxima versão é [FreeBSD 11](https://www.freebsd.org/releases/11.0R/schedule.html).

## <a name="deploying-a-freebsd-virtual-machine"></a>Implementar uma máquina de virtual FreeBSD
Implementar uma máquina de virtual FreeBSD é um processo simples utilizando uma imagem a partir [Do Azure Marketplace](https://azure.microsoft.com/marketplace/partners/microsoft/freebsd103/).

## <a name="vm-extensions-for-freebsd"></a>Extensões VM para FreeBSD
Seguem-se extensões VM suportadas no FreeBSD.

### <a name="vmaccess"></a>VMAccess

A extensão [VMAccess](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess) pode:

- Repor a palavra-passe do utilizador sudo original.
- Crie um novo utilizador sudo com a palavra-passe especificada.
- Defina a chave de anfitrião público com a tecla tendo em conta.
- Repor a chave de anfitrião público fornecida durante o aprovisionamento de VM se não for fornecida a chave do anfitrião.
- Abrir a porta SSH (22) e restaurar a sshd_config se reset_ssh estiver definida como verdadeiro.
- Remova o utilizador existente.
- Verificar se os discos.
- Repare um disco adicionado.

### <a name="customscript"></a>CustomScript

A extensão [CustomScript](https://github.com/Azure/azure-linux-extensions/tree/master/CustomScript) pode:

- Se tiver fornecido, transferir os scripts personalizados a partir do armazenamento do Windows Azure ou armazenamento público externo (por exemplo, GitHub).
- Execute o script do ponto de entrada.
- Suporta comandos inline.
- Converta nova linha de estilo do Windows no Alçados e Python scripts automaticamente.
- Remova BOM Alçados e Python scripts automaticamente.
- Proteger dados confidenciais no CommandToExecute.

## <a name="authentication-user-names-passwords-and-ssh-keys"></a>Autenticação: nomes de utilizador, palavras-passe e chaves de SSH
Quando estiver a criar uma máquina de virtual FreeBSD utilizando o portal do Azure, tem de fornecer um nome de utilizador, palavra-passe ou chave pública SSH.
Nomes de utilizador para implementar uma máquina de virtual FreeBSD no Azure não tem de corresponder nomes das contas do sistema (UID < 100) já se encontra presente na máquina virtual ("raiz," por exemplo).
Atualmente, apenas a RSA SSH chave é suportada. Tem de uma chave SSH com várias linhas começam por "-início SSH2 chave pública-" e termina com "-END SSH2 chave pública-".

## <a name="obtaining-superuser-privileges"></a>Obter privilégios super utilizador
A conta de utilizador for especificada durante a implementação de instância máquinas virtuais no Azure é uma conta com privilégios. O pacote de sudo foi instalado na imagem FreeBSD publicada.
Depois de iniciou a sessão através desta conta de utilizador, pode executar comandos como raiz utilizando a sintaxe de comando.

    # sudo <COMMAND>

Opcionalmente, pode obter uma shell de raiz utilizando sudo -s.

## <a name="next-steps"></a>Próximos passos
- Aceda ao [Azure Marketplace](https://azure.microsoft.com/marketplace/partners/microsoft/freebsd103/) para criar uma VM FreeBSD.
- Se pretender colocar o seu próprio FreeBSD Azure, consulte [criar e carregar um VHD FreeBSD para Azure](../virtual-machines-linux-classic-freebsd-create-upload-vhd.md).
