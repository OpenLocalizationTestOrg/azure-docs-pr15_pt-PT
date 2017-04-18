<properties
    pageTitle="Encriptação de serviço de armazenamento Azure para os dados em repouso | Microsoft Azure"
    description="Utilizar a funcionalidade de encriptação de serviço de armazenamento do Azure para encriptar o seu armazenamento de Blobs do Azure no lado serviço quando armazenar os dados e desencriptá-lo ao obter os dados."
    services="storage"
    documentationCenter=".net"
    authors="robinsh"
    manager="carmonm"
    editor="tysonn"/>

<tags
    ms.service="storage"
    ms.workload="storage"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/16/2016"
    ms.author="robinsh"/>

# <a name="azure-storage-service-encryption-for-data-at-rest"></a>Encriptação de serviço de armazenamento Azure para os dados em repouso

Azure armazenamento serviço encriptação (SSE) para os dados em repouso ajuda-o a proteger e proteger os seus dados para satisfazer as segurança organizacional e compromissos de conformidade. Com esta funcionalidade, o armazenamento do Windows Azure automaticamente encripta os seus dados antes de persistência ao armazenamento e desencripta antes de obtenção. A encriptação, desencriptação e gestão de chave são totalmente transparentes para os utilizadores.

As secções seguintes fornecem informações detalhadas sobre como utilizar as funcionalidades de encriptação de serviço de armazenamento, bem como o cenários suportados e o utilizador experiências.

## <a name="overview"></a>Descrição geral

Armazenamento Azure fornece um conjunto abrangente de capacidades de segurança em conjunto permitir que os programadores criem aplicações seguras. Dados podem ser protegidos em trânsito entre uma aplicação e Azure ao utilizar a [Encriptação do lado do cliente](storage-client-side-encryption.md), HTTPs ou SMB 3.0. Encriptação do serviço de armazenamento fornece encriptação em repouso, processamento de encriptação, desencriptação e gestão de chaves de forma totalmente transparente. Todos os dados são encriptados utilizando 256 bits [encriptação AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), uma das mais fortes bloquear codificações disponíveis.

SSE funciona ao encriptá os dados quando destina-se ao armazenamento do Azure e podem ser utilizado para bloquear blobs, blobs de página e acrescentar blobs. Funciona para as seguintes opções:

-   Contas de armazenamento de objetivo gerais e contas de armazenamento de BLOBs
-   Armazenamento padrão e armazenamento de Premium 
-   Redundância todos os níveis (LRS, ZRS, GRS, GRS RT)
-   Contas do Azure armazenamento de Gestor de recursos (mas não clássico) 
-   Todas as regiões

Para ativar ou desativar a encriptação do serviço de armazenamento para uma conta de armazenamento, inicie sessão no [portal do Azure](https://azure.portal.com) e selecione uma conta de armazenamento. Na pá definições, procure a secção serviço Blob conforme apresentado nesta captura de ecrã e clique em encriptação.

![Portal captura de ecrã que mostra a opção de encriptação](./media/storage-service-encryption/image1.png)

Depois de clicar a definição de encriptação, pode ativar ou desativar a encriptação do serviço de armazenamento.

![Propriedades de encriptação portal captura de ecrã a mostrar a](./media/storage-service-encryption/image2.png)

##<a name="encryption-scenarios"></a>Cenários de encriptação

Encriptação do serviço de armazenamento pode ser ativada num nível de conta de armazenamento. Suporta os seguinte cenários de cliente:

-   Encriptação de blobs do bloco, acrescentar blobs e blobs de página.

-   Encriptação de arquivados VHDs e modelos levados ao Azure a partir no local.

-   Encriptação de discos SO e os dados subjacentes para VMs IaaS criado com o seu VHDs.

SSE tem as seguintes limitações:

-   Encriptação de contas de armazenamento clássica não é suportada.

-   Encriptação de armazenamento clássica contas migradas para contas de armazenamento do Gestor de recursos não é suportada.

-   Os dados existentes - SSE encripta os dados recentemente criados apenas depois da encriptação está ativada. Se, por exemplo criar uma nova conta de armazenamento do Gestor de recursos, mas não ativar encriptação e, em seguida, carregar blobs ou VHDs arquivados para essa conta de armazenamento e, em seguida, ativar SSE, essas blobs não serão encriptados, a menos que são reprogramados ou copiados.

-   Suporte de Marketplace - activar a encriptação de VMs criada a partir Marketplace utilizando o [Azure portal](https://portal.azure.com), PowerShell e clip do Azure. A imagem base VHD permanecerá não encriptada; No entanto, serão encriptadas qualquer escritas concluídas depois da VM tem fiadas para cima.

-   Tabela, filas, e dados de ficheiros não serão encriptados.

##<a name="getting-started"></a>Introdução

###<a name="step-1-create-a-new-storage-accountstorage-create-storage-accountmd"></a>Passo 1: [criar uma nova conta de armazenamento](storage-create-storage-account.md).

###<a name="step-2-enable-encryption"></a>Passo 2: Activar a encriptação.

Pode ativar a encriptação utilizando o [Azure portal](https://portal.azure.com).

> [AZURE.NOTE] Se quiser através de programação ativar ou desativar a encriptação do serviço de armazenamento numa conta de armazenamento, pode utilizar o [Azure armazenamento recurso fornecedor REST API](https://msdn.microsoft.com/library/azure/mt163683.aspx), a [Biblioteca de cliente do armazenamento recurso fornecedor para .NET](https://msdn.microsoft.com/library/azure/mt131037.aspx), [Azure PowerShell](../powershell-install-configure.md)ou o [Clip do Azure](storage-azure-cli.md).

###<a name="step-3-copy-data-to-storage-account"></a>Passo 3: Copiar dados para a conta de armazenamento

Se ativar SSE numa conta de armazenamento e, em seguida, escrever blobs a essa conta de armazenamento que de blobs serão encriptados. Qualquer já localizados nessa conta de armazenamento de blobs não serão encriptados até que são reprogramados. Pode copiar os dados a partir da conta de armazenamento para uma com SSE encriptado, ou até mesmo ativar SSE e copiar os blobs de um contentor para outro para-se de que estão encriptados dados anterior. Pode utilizar qualquer uma das seguintes ferramentas para realizar esta tarefa.

#### <a name="using-azcopy"></a>Utilizar AzCopy

AzCopy é um utilitário da linha de comandos do Windows concebido para copiar dados para e a partir do armazenamento de Blobs do Microsoft Azure, ficheiro e tabela utilizando comandos simples com um desempenho ideal. Pode utilizar este para copiar o seu blobs de uma conta de armazenamento para outra que tem SSE ativado. 

Para saber mais, visite [Transferir dados com o utilitário AzCopy da linha de comandos](storage-use-azcopy.md).

#### <a name="using-the-storage-client-libraries"></a>Utilizar as bibliotecas do cliente de armazenamento

Pode copiar dados blob para e a partir do armazenamento de BLOBs ou entre contas de armazenamento utilizando o nosso conjunto avançado de bibliotecas de armazenamento do cliente incluindo .NET, C++, Java, Android, Node.js, PHP, Python e rubi.

Para saber mais, visite a nossa [Introdução ao armazenamento de Blobs do Azure utilizando o .NET](storage-dotnet-how-to-use-blobs.md).

#### <a name="using-a-storage-explorer"></a>Utilizar o Explorador de armazenamento

Pode utilizar o Explorador de armazenamento para criar contas de armazenamento, carregar e transferir dados, ver conteúdo de blobs e navegar pelos directórios. Pode utilizar um dos seguintes procedimentos para carregar blobs à sua conta de armazenamento com encriptação activada. Com algumas Explorador de armazenamento, também pode copiar dados a partir do armazenamento de Blobs do existente para um contentor diferente da conta de armazenamento ou uma nova conta de armazenamento tem SSE ativado.

Para saber mais, visite o [Explorador de armazenamento do Azure](storage-explorers.md).

###<a name="step-4-query-the-status-of-the-encrypted-data"></a>Passo 4: Consultar o estado dos dados encriptados

Foram implementada uma versão atualizada das bibliotecas do cliente de armazenamento que permite-lhe consultar o estado de um objeto para determinar se é encriptado ou não. Exemplos serão adicionados a este documento no futuro próximo.

Entretanto, pode ligar a [Obter propriedades da conta](https://msdn.microsoft.com/library/azure/mt163553.aspx) para verificar que a conta de armazenamento tem encriptação activada ou ver as propriedades da conta de armazenamento no portal do Azure.

##<a name="encryption-and-decryption-workflow"></a>Encriptação e desencriptação fluxo de trabalho

Eis uma breve descrição do fluxo de trabalho encriptação/desencriptação:

-   O cliente permite encriptação na conta de armazenamento.

-   Quando o cliente escreve novos dados (colocar Blob, colocar bloco, colocar a página, etc.) ao armazenamento Blob do; cada escrita está encriptada utilizando 256 bits [encriptação AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), uma das mais fortes bloquear codificações disponíveis.

-   Quando o cliente tem de aceder a dados (obter Blob, etc.), os dados são automaticamente desencriptados antes de devolver ao utilizador.

-   Se estiver desactivada encriptação, escritas novas já não são encriptadas e dados encriptados existentes permanecem encriptados até reprogramados pelo utilizador. Enquanto encriptação está ativada, escritas ao armazenamento Blob serão encriptadas. Não altera o estado de dados com o utilizador alternar entre Ativar/desativar a encriptação da conta de armazenamento.

-   Todas as chaves de encriptação são armazenadas, encriptadas e geridas pela Microsoft.

##<a name="frequently-asked-questions-about-storage-service-encryption-for-data-at-rest"></a>Perguntas mais frequentes sobre encriptação do serviço de armazenamento para os dados em repouso

**P: Posso ter uma conta existente do armazenamento clássica. Pode ativar SSE no mesmo?**

R: não, SSE só é suportada em contas de armazenamento do Gestor de recursos.

**P: como posso encriptar dados na minha conta de armazenamento clássica?**

R: pode criar uma nova conta de armazenamento do Gestor de recursos e copiar os dados utilizando [AzCopy](storage-use-azcopy.md) da sua conta de armazenamento clássica existente à sua conta de armazenamento do Gestor de recursos recentemente criado. 

Outra opção é migrar a sua conta de armazenamento clássica a uma conta de armazenamento recurso gerir. Para mais informações, consulte o artigo [Plataforma suportadas migração de IaaS recursos a partir do clássico para o Gestor de recursos](https://azure.microsoft.com/blog/iaas-migration-classic-resource-manager/).

**P: Posso ter uma conta de armazenamento do Gestor de recursos existente. Pode ativar SSE no mesmo?**

R: Sim, mas apenas recentemente escrito blobs serão encriptados. Não voltar atrás e encriptar dados já estava a apresentados. 

**P: gostaria de encriptar os dados atuais de uma conta de armazenamento do Gestor de recursos existente?**

R: pode ativar a SSE em qualquer altura numa conta de armazenamento do Gestor de recursos. No entanto, não serão encriptados blobs que já foram apresentados. Para encriptar essas blobs, pode copiá-los para outro nome ou outro contentor e, em seguida, remover as versões não encriptadas.

**P: Estou a utilizar o armazenamento de Premium; Pode utilizar SSE?**

R: Sim, SSE é suportada em armazenamento padrão e armazenamento de Premium.

**P: se posso criar uma nova conta de armazenamento e ativar SSE, em seguida, criar uma nova VM utilizar essa conta de armazenamento, isso significa que está encriptado meu VM?**

R: Sim. Qualquer discos criados que utilizam a nova conta de armazenamento serão encriptados, desde que são criadas após SSE está ativado. Se a VM foi criada utilizando o Azure mercado local, a imagem base VHD permanecerá não encriptada; No entanto, serão encriptadas qualquer escritas concluídas depois da VM tem fiadas para cima.

**P: pode criar novas contas de armazenamento com SSE com permissão para utilizar o PowerShell do Azure e o clip do Azure?**

R: Sim.

**P: como muito mais armazenamento do Windows Azure custo se SSE está ativado?**

R: não existe nenhuma custo adicional.

**P: quem gere as chaves de encriptação?**

R: as teclas são geridas pelo Microsoft.

**P: Posso utilizar o meus próprio chaves de encriptação?**

R: estamos a trabalhar no sentido de fornecer as capacidades de clientes trazer os seus próprios chaves de encriptação.

**P: posso revogar o acesso às chaves de encriptação?**

R: não neste momento; as teclas totalmente são geridas pelo Microsoft.

**P: é SSE ativada por predefinição quando posso criar uma nova conta de armazenamento?**

R: SSE não está ativada por predefinição; Pode utilizar o portal do Azure para ativá-la. Através de programação também pode ativar esta funcionalidade utilizando o fornecedor de REST API do armazenamento recurso.

**P: como é diferente do Azure encriptação de unidade?**

R: esta funcionalidade é utilizada para encriptar dados armazenamento de Blobs do Azure. A encriptação de disco Azure é utilizada para encriptar discos SO e dados no IaaS VMs. Para obter mais detalhes, visite o nosso [Guia de segurança de armazenamento](storage-security-guide.md).

**P: o que acontece se ativar a SSE e, em seguida, aceda e ativar o Azure disco encriptação discos?**

R: Isto irá funcionar de forma totalmente integrada. Os seus dados serão encriptados por ambos os métodos.

**P: a minha conta de armazenamento está configurada para ser replicadas geo-forma redundante. Se ativar SSE, será a minha cópia redundante também encriptada?**

R: Sim, todas as cópias da conta de armazenamento são encriptadas e todas as opções de redundância – localmente redundantes armazenamento (LRS), zona Redundant armazenamento (ZRS), Geo Redundant armazenamento (GRS) e acesso de leitura Geo Redundant armazenamento (GRS RT) – são suportadas.

**P: não é possível ativar o encriptação na minha conta de armazenamento.**

R: é uma conta de armazenamento do Gestor de recursos? Contas de armazenamento clássica não são suportadas. 

**P: SSE só é permitido em regiões específicos?**

R: a SSE está disponível em todas as regiões. 

**P: como posso contactar alguém se que tenha problemas ou pretender fornecer comentários?**

R: contacte [ssediscussions@microsoft.com](mailto:ssediscussions@microsoft.com) quaisquer problemas relacionados com a encriptação do serviço de armazenamento.

##<a name="next-steps"></a>Próximos passos

Armazenamento Azure fornece um conjunto abrangente de capacidades de segurança em conjunto permitir que os programadores criem aplicações seguras. Para obter mais detalhes, visite o [Guia de segurança de armazenamento](storage-security-guide.md).
