<properties
   pageTitle="Descrição geral de segurança do armazenamento Azure | Microsoft Azure"
   description=" Armazenamento Azure é a solução de armazenamento na nuvem para aplicações modernas que dependem de durabilidade, disponibilidade e escalabilidade para satisfazer as necessidades dos seus clientes. Este artigo fornece uma descrição geral das principais funcionalidades de segurança Azure que podem ser utilizadas com armazenamento do Windows Azure. "
   services="security"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="MBaldwin"
   editor="TomSh"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/16/2016"
   ms.author="terrylan"/>

# <a name="azure-storage-security-overview"></a>Descrição geral de segurança do armazenamento Azure

Armazenamento Azure é a solução de armazenamento na nuvem para aplicações modernas que dependem de durabilidade, disponibilidade e escalabilidade para satisfazer as necessidades dos seus clientes. Armazenamento Azure fornece um conjunto abrangente de capacidades de segurança:

- A conta de armazenamento pode ser protegida utilizando o controlo de acesso baseado em funções e o Azure Active Directory.
- Dados podem ser protegidos em trânsito entre uma aplicação e Azure ao utilizar a encriptação do lado do cliente, HTTPS ou SMB 3.0.
- Dados podem ser definidos encriptados automaticamente quando escritas armazenamento do Windows Azure utilizando a encriptação do serviço de armazenamento.
- Sistema operativo e dados discos utilizados por máquinas virtuais podem ser definidos para ser encriptado através de encriptação do Azure do disco.
- Pode ser concedido acesso delegado para os objetos de dados no armazenamento Azure utilizar assinaturas de acesso partilhado.
- O método de autenticação utilizado por outra pessoa ao aceder aos armazenamento pode ser controlado utilizando a análise de armazenamento.

Para mais detalhada sobre segurança no armazenamento do Windows Azure, consulte o [Guia de segurança de armazenamento do Windows Azure](../storage/storage-security-guide.md). Este guia fornece um profundidade vôo picado para as funcionalidades de segurança de armazenamento do Windows Azure como chaves de conta de armazenamento, a encriptação em trânsito e restantes e a análise de armazenamento de dados.

Este artigo fornece uma descrição geral das funcionalidades de segurança Azure que podem ser utilizados com armazenamento do Windows Azure. Ligações são fornecidos para artigos que fornecem detalhes de cada funcionalidade-o pode obter mais informações.

Seguem-se as funcionalidades principais para ser abordados neste artigo:

- Controlo de acesso baseado em funções
- Acesso delegado para objetos de armazenamento
- Encriptação em trânsito
- Encriptação no resto/encriptação do serviço de armazenamento
- Encriptação Azure do disco
- Azure cofre chave

## <a name="role-based-access-control-rbac"></a>Controlo de acesso baseado em funções RBCA)

Pode proteger a sua conta de armazenamento com o controlo de acesso baseado em funções RBCA (). Restringir o acesso com base em princípios de segurança [saber](https://en.wikipedia.org/wiki/Need_to_know) e [privilégio](https://en.wikipedia.org/wiki/Principle_of_least_privilege) é obrigatório para as organizações que pretendem impor políticas de segurança para acesso aos dados. São concedidos esses direitos de acesso ao atribuir à função RBAC apropriada ao grupos e aplicações de um determinado âmbito. Pode utilizar [funções RBAC incorporadas](../active-directory/role-based-access-built-in-roles.md), como o armazenamento conta contribuinte, para atribuir privilégios aos utilizadores.

Saiba mais:

- [Controlo de acesso baseado em funções de Azure Active Directory](../active-directory/role-based-access-control-configure.md)

## <a name="delegated-access-to-storage-objects"></a>Acesso delegado para objetos de armazenamento

Uma assinatura de acesso partilhado (SA) oferece um acesso delegado para recursos na sua conta de armazenamento. As associações de segurança significa que o utilizador pode conceder que permissões a objetos na sua conta de armazenamento de limitadas um cliente para um determinado período de tempo e com um conjunto de permissões especificado. Pode conceder estas permissões limitados sem ter de partilhar o seu teclas de acesso de conta. As associações de segurança são um URI que engloba nos respetivos parâmetros de consulta todas as informações necessárias para acesso autenticado a um recurso de armazenamento. Para aceder a recursos de armazenamento com as associações de segurança, o cliente precisa apenas transmitir nas associações de segurança para o método ou construtor adequado.

Saiba mais:

- [Noções sobre o modelo de SA](../storage/storage-dotnet-shared-access-signature-part-1.md)
- [Criar e utilizar uma SA com armazenamento de BLOBs](../storage/storage-dotnet-shared-access-signature-part-2.md)

## <a name="encryption-in-transit"></a>Encriptação em trânsito
Encriptação em trânsito é um mecanismo de proteger dados quando é transmitido através de redes. Com o armazenamento do Windows Azure pode proteger dados utilizando:

- [Encriptação de nível de transporte](../storage/storage-security-guide.md#encryption-in-transit), tal como HTTPS quando transferir dados ou desaparecer armazenamento do Windows Azure.
- [Encriptação de esboço](../storage/storage-security-guide.md#using-encryption-during-transit-with-azure-file-shares), tal como SMB 3.0 encriptação para Azure partilhas de ficheiros.
- [Encriptação do lado do cliente](../storage/storage-security-guide.md#using-client-side-encryption-to-secure-data-that-you-send-to-storage), para encriptar os dados antes de serem transferidas para o armazenamento e para desencriptar os dados após a transferência armazenamento.

Saiba mais sobre encriptação do lado do cliente:

- [Encriptação do lado do cliente para o armazenamento do Microsoft Azure](https://blogs.msdn.microsoft.com/windowsazurestorage/2015/04/28/client-side-encryption-for-microsoft-azure-storage-preview/)
- [Série de controlos de segurança na nuvem: encriptar dados estão em trânsito](http://blogs.microsoft.com/cybertrust/2015/08/10/cloud-security-controls-series-encrypting-data-in-transit/)

## <a name="encryption-at-rest"></a>Encriptação em repouso

Para organizações muitos, [encriptação de dados em repouso](https://blogs.microsoft.com/cybertrust/2015/09/10/cloud-security-controls-series-encrypting-data-at-rest/) é um passo obrigatório no sentido soberania de dados, conformidade e privacidade de dados. Existem três funcionalidades Azure que fornecem encriptação de dados que são "em repouso":

- [Encriptação do serviço de armazenamento](../storage/storage-security-guide.md#encryption-at-rest) permite-lhe pedir que o serviço de armazenamento encriptar dados automaticamente ao escrevê-lo ao armazenamento do Azure.
- [Encriptação do lado do cliente](../storage/storage-security-guide.md#client-side-encryption) também fornece a funcionalidade de encriptação em repouso.
- [Encriptação de disco do Azure](../storage/storage-security-guide.md#using-azure-disk-encryption-to-encrypt-disks-used-by-your-virtual-machines) permite-lhe encriptar o sistema operativo discos e dados utilizados por uma máquina de virtual IaaS.

Saiba mais sobre encriptação do serviço de armazenamento:

- [Encriptação de serviço de armazenamento do Azure](https://azure.microsoft.com/services/storage/) está disponível para o [Armazenamento de Blobs do Azure](https://azure.microsoft.com/services/storage/blobs/). Para obter detalhes sobre outros tipos de armazenamento Azure, consulte o artigo [ficheiro](https://azure.microsoft.com/services/storage/files/), [disco (armazenamento de Premium)](https://azure.microsoft.com/services/storage/premium-storage/), [tabela](https://azure.microsoft.com/services/storage/tables/)e [fila de espera](https://azure.microsoft.com/services/storage/queues/).
- [Encriptação de serviço de armazenamento Azure para os dados em repouso](../storage/storage-service-encryption.md)

## <a name="azure-disk-encryption"></a>Encriptação Azure do disco

Azure encriptação do disco para máquinas virtuais (VMs) ajuda-o segurança organizacional endereço e requisitos de conformidade ao encriptá seus discos VM (incluindo discos arranque e dados) com chaves e políticas de que controlo no [Azure chave cofre](https://azure.microsoft.com/services/key-vault/).

Encriptação do disco para VMs funciona para sistemas operativos Linux e Windows. Também utiliza cofre chave para o ajudar a salvaguardar, gerir e utilização das suas chaves de encriptação do disco de auditoria. Todos os dados nos seus discos VM está encriptado em repouso utilizando a tecnologia de encriptação de norma da indústria nas suas contas de armazenamento do Windows Azure. A solução de encriptação do disco para Windows é baseada no [Microsoft BitLocker unidade encriptação](https://technet.microsoft.com/library/cc732774.aspx)e a solução Linux é baseada no [dm encriptação](https://en.wikipedia.org/wiki/Dm-crypt).

Saiba mais:

- [Encriptação de Azure do disco para Windows e máquinas virtuais de Linux IaaS](https://gallery.technet.microsoft.com/Azure-Disk-Encryption-for-a0018eb0)

## <a name="azure-key-vault"></a>Azure cofre chave

Encriptação do disco Azure utiliza [Azure chave cofre](https://azure.microsoft.com/services/key-vault/) para ajudá-lo a controlar e gerir as chaves de encriptação do disco e segredos na sua subscrição do cofre chave, garantindo que todos os dados no discos máquina virtual são encriptados em repouso no seu armazenamento Azure. Deve utilizar a chave cofre chaves e a utilização de política de auditoria.

Saiba mais:

- [O que é o Azure chave Cofre?](../key-vault/key-vault-whatis.md)
- [Introdução ao Azure chave Cofre](../key-vault/key-vault-get-started.md)
