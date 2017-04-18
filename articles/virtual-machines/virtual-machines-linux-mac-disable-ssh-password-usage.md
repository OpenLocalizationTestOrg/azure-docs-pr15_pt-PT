<properties
    pageTitle="Desativar SSH palavras-passe a VM Linux configurando SSHD | Microsoft Azure"
    description="Proteger o seu VM Linux no Azure ao desativar inícios de sessão de palavra-passe para SSH."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="vlivech"
    manager="timlt"
    editor=""
    tags="" />

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/26/2016"
    ms.author="v-livech"/>

# <a name="disable-ssh-passwords-on-your-linux-vm-by-configuring-sshd"></a>Desativar SSH palavras-passe a VM Linux configurando SSHD

Este artigo foca-se em como bloquear para baixo a segurança de início de sessão do seu VM Linux.  Assim que a porta SSH 22 é aberta para o início de bots mundo está a tentar iniciar sessão adivinhar palavras-passe.  O que recomendamos irá fazer neste artigo é desativar inícios de sessão de palavra-passe em SSH.  Ao remover completamente a capacidade de utilizar palavras-passe a VM Linux podemos proteger a partir deste tipo de ataques.  O bónus adicionado é que podemos irá configurar Linux SSHD para permitir apenas os inícios de sessão através de chaves públicas e privadas SSH, por extremidade a forma mais segura de início de sessão para Linux.  As combinações possíveis de-seriam necessário adivinhar a chave privada é grande e, consequentemente, desencoraja bots a partir do bothering par para experimentar a força bruta SSH chaves.


## <a name="goals"></a>Objetivos

- Configure SSHD para não permitir:
  - Inícios de sessão de palavra-passe
  - Início de sessão do utilizador raiz
  - Autenticação desafio resposta
- Configure SSHD para permitir que:
  - apenas SSH chaves os inícios de sessão
- Reinicie o SSHD enquanto ainda tem sessão iniciada numa
- Teste a configuração de SSHD nova

## <a name="introduction"></a>Introdução

[SSH definido](https://en.wikipedia.org/wiki/Secure_Shell)

SSHD é o SSH servidor que é executada na VM Linux.  SSH é um cliente que executa a partir de uma shell na estação de trabalho MacBook ou Linux.  SSH também é o protocolo utilizado para proteger e encriptar a comunicação entre a estação de trabalho e a VM Linux.

Este artigo é muito importante manter um início de sessão para sua VM Linux abrir para obter as instruções inteira.  Por este motivo podemos irá abrir duas terminais e SSH a VM Linux a partir de ambos-los.  Vamos irá utilizar o terminal primeiro para efetue as alterações ao ficheiro de configuração de SSHDs e reinicie o serviço SSHD.  Vamos irá utilizar o segundo terminal para testar a essas alterações assim que o serviço for reiniciado.  Uma vez que estamos a desativação SSH as palavras-passe e depender de estritamente SSH chaves, se as suas chaves SSH não estão corretos e feche a ligação para a VM, a VM serão permanentemente bloqueado e ninguém poderão início de sessão para o mesmo que exige o-la para ser eliminada e recriada.

## <a name="prerequisites"></a>Pré-requisitos

- [Criar SSH teclas no Linux e Mac para Linux VMs no Azure](virtual-machines-linux-mac-create-ssh-keys.md)
- Conta Azure
  - [inscrição de avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/)
  - [Portal do Azure](http://portal.azure.com)
- VM Linux em execução no azure
- SSH públicas e privadas chave par em`~/.ssh/`
- SSH chave pública `~/.ssh/authorized_keys` na VM Linux
- Direitos de Sudo na VM
- Porta 22 aberta

## <a name="quick-commands"></a>Comandos rápidos

_Administradores de Linux experiente que apenas pretende instalar a versão TLDR comece aqui.  Para todos os participantes poderão que pretende que a explicação detalhada e obter instruções ignorar esta secção._

```
username@macbook$ sudo vim /etc/ssh/sshd_config

# Change PasswordAuthentication to this:
PasswordAuthentication no

# Change PubkeyAuthentication to this:
PubkeyAuthentication yes

# Change PermitRootLogin to this:
PermitRootLogin no

# Change ChallengeResponseAuthentication to this:
ChallengeResponseAuthentication no

# On the Debian Family
username@macbook$ sudo service ssh restart

# On the RedHat Family
username@macbook$ sudo service sshd restart
```

## <a name="detailed-walk-through"></a>Obter instruções detalhadas

Início de sessão para a VM Linux no terminal 1 (T1).  Início de sessão para a VM Linux no terminal 2 (T2).

No T2 vamos para editar o ficheiro de configuração de SSHD.  

```
username@macbook$ sudo vim /etc/ssh/sshd_config
```

A partir daqui podemos irá editar apenas as definições para desativar as palavras-passe e ativar SSH chaves inícios de sessão.  Existem muitas das definições deste ficheiro que deverá investigação e altere para tornar o Linux & SSH como seguro que precisar.

#### <a name="disable-password-logins"></a>Desativar inícios de sessão de palavra-passe

```
username@macbook$ sudo vim /etc/ssh/sshd_config

# Change PasswordAuthentication to this:
PasswordAuthentication no
```

#### <a name="enable-public-key-authentication"></a>Ativar a autenticação de chave pública

```
username@macbook$ sudo vim /etc/ssh/sshd_config

# Change PubkeyAuthentication to this:
PubkeyAuthentication yes
```

#### <a name="disable-root-login"></a>Desativar o início de sessão de raiz

```
username@macbook$ sudo vim /etc/ssh/sshd_config

# Change PermitRootLogin to this:
PermitRootLogin no
```

#### <a name="disable-challenge-response-authentication"></a>Desativar a autenticação desafio resposta

```
# Change ChallengeResponseAuthentication to this:
ChallengeResponseAuthentication no
```

### <a name="restart-sshd"></a>Reinicie o SSHD

A partir da shell de T1 Certifique-se de que são ainda iniciou sessão.  O que é fundamental para que não ficam bloqueadas fora do seu VM se as suas chaves SSH não estiverem corretas uma vez que as palavras-passe agora são desactivadas.  Se qualquer definição está incorreta no seu VM Linux pode utilizar T1 para corrigir sshd_config à medida que serão ainda registados e SSH manterá a ligação vivo durante o serviço SSHD reinicie.

A partir do T2 executar:

##### <a name="on-the-debian-family"></a>Na família Debian

```
username@macbook$ sudo service ssh restart
```

##### <a name="on-the-redhat-family"></a>Na família do sistema RedHat

```
username@macbook$ sudo service sshd restart
```

As palavras-passe agora estão desactivadas no seu VM-lo a proteger contra tentativas de início de sessão do força bruta palavra-passe.  Com apenas SSH chaves permitida que será conseguir iniciar sessão mais rápido e muito mais segura.
