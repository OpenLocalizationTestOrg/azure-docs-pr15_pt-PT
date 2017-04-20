<properties
   pageTitle="Chave Guia do Cofre de palavras Programador | Microsoft Azure"
   description="Os programadores podem utilizar Azure do Cofre de palavras chave para gerir chaves criptográficas dentro do ambiente de Microsoft Azure. "
   services="key-vault"
   documentationCenter=""
   authors="BrucePerlerMS"
   manager="mbaldwin"
   editor="bruceper" />
<tags
   ms.service="key-vault"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="10/03/2016"
   ms.author="bruceper" />

# <a name="azure-key-vault-developers-guide"></a>Guide Cofre de palavras chave Azure Developers
Utilizar o Cofre de palavras chave, será possível aceder com segurança informações dentro das suas aplicações sensíveis tal que:

- Chaves e segredos serão protegidos sem ter de escrever o código de si próprio e poderá facilmente utilizá-los a partir de aplicações.
- Poderá ter clientes próprios e gerir as próprias chaves de modo que possa concentrar-se no sentido de fornecer as principais funcionalidades de software. Desta forma as aplicações não serão própria a responsabilidade ou do passivo potencial para chaves de Tenants dos seus clientes e os segredos.
- A aplicação pode utilizar chaves de assinatura e encriptação ainda mantém a gestão de chaves externas a partir da aplicação tal que a solução é adequada para uma aplicação que está a ser distribuída geograficamente.

- Com a edição de Setembro de 2016 do Cofre de palavras chave, as aplicações podem efectuar agora a utilização do Cofre de palavras chave certificados. Para mais informações, consulte **sobre chaves, segredos e certificados** artigo em que a [referência do resto](https://msdn.microsoft.com/library/azure/dn903623.aspx).

Para mais informações gerais sobre Azure do Cofre de palavras chave, consulte [o que é o Cofre de palavras chave](key-vault-whatis.md).

## <a name="videos"></a>Vídeos
Este vídeo mostra-lhe como criar seu próprio Cofre de palavras chave e como utilizá-la da aplicação de exemplo 'Hello chave Cofre de palavras'.

[AZURE.VIDEO azure-key-vault-developer-quick-start]

Hiperligações para recursos mencionados no vídeo:
- [Azure PowerShell](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409)
- [Código de exemplo do Cofre de palavras chave Azure](http://go.microsoft.com/fwlink/?LinkId=521527&clcid=0x409)

Para obter informações mais poderá de seguir o [Blogue de Cofre de palavras chave](http://aka.ms/kvblog) e participar no [Fórum de Cofre de palavras chave](http://aka.ms/kvforum).

## <a name="creating-and-managing-key-vaults"></a>Criar e gerir cofres de chaves

Antes de trabalhar com Azure do Cofre de palavras chave no código, pode criar e gerir cofres através de resto, modelos de Gestor de recursos, PowerShell ou CLI, tal como descrito nos seguintes artigos:

- [Criar e gerir chaves cofres com resto](https://msdn.microsoft.com/library/azure/mt620024.aspx)
- [Criar e gerir chaves cofres com o PowerShell](key-vault-get-started.md)
- [Criar e gerir chaves cofres com CLI](key-vault-manage-with-cli.md)
- [Criar um cofre de palavras chave e adicionar uma palavra-passe através de um modelo de Gestor de recursos Azure](../resource-manager-template-keyvault.md)

>[AZURE.NOTE] Operações cofres chaves sejam autenticadas através de AAD e autorizadas através da política de acesso do chave do Cofre de palavras próprio, definida por Cofre de palavras.

## <a name="coding-with-key-vault"></a>Codificação com chave do Cofre de palavras

O sistema de gestão do Cofre de palavras chave para programadores consiste em várias interfaces, com o resto como Fundação, [Chave de referência da API de resto de Cofre de palavras](https://msdn.microsoft.com/library/azure/dn903609.aspx).

É possível, uma autorização com êxito, efectue o seguinte:

- Gerir chaves de criptografia utilizando a [Criar](https://msdn.microsoft.com/library/azure/dn903634.aspx), [Importar](https://msdn.microsoft.com/library/azure/dn903626.aspx), [actualização](https://msdn.microsoft.com/library/azure/dn903616.aspx), [Eliminar](https://msdn.microsoft.com/library/azure/dn903611.aspx) e outras operações

- Gerir segredos utilizando [obter](https://msdn.microsoft.com/library/azure/dn903633.aspx), [actualização](https://msdn.microsoft.com/library/azure/dn986818.aspx), [eliminação](https://msdn.microsoft.com/library/azure/dn903613.aspx) e outras operações

- Utilizar chaves criptográficas com [sinal de](https://msdn.microsoft.com/library/azure/dn878096.aspx)/[Verificar](https://msdn.microsoft.com/library/azure/dn878082.aspx), [WrapKey](https://msdn.microsoft.com/library/azure/dn878066.aspx)/[UnwrapKey](https://msdn.microsoft.com/library/azure/dn878079.aspx) e [encriptar](https://msdn.microsoft.com/library/azure/dn878060.aspx)/[desencriptar](https://msdn.microsoft.com/library/azure/dn878097.aspx) operações

Os SDK seguintes estão disponíveis para trabalhar com o Cofre de palavras chave:

|[![.NET](./media/key-vault-developers-guide/msft.netlogo_purple.png)](https://msdn.microsoft.com/library/mt765854.aspx)|[![NODE.js](./media/key-vault-developers-guide/nodejs.png)](http://azure.github.io/azure-sdk-for-node/azure-arm-keyvault/latest)
|:--:|:--:|
|[Documentação do SDK do .NET](https://msdn.microsoft.com/library/mt765854.aspx)|[Documentação do SDK de node.js](http://azure.github.io/azure-sdk-for-node/azure-arm-keyvault/latest)|
|[Pacote SDK .NET em Nuget](http://www.nuget.org/packages/Microsoft.Azure.KeyVault)|[Pacote node.js SDK](https://www.npmjs.com/package/azure-keyvault)|

Para mais informações sobre a versão 2. x do .NET SDK, consulte as [notas de lançamento](key-vault-dotnet2api-release-notes.md).

## <a name="example-code"></a>Exemplo de código
Para obter exemplos completos utilizando o Cofre de palavras chave com as aplicações, consulte:

- Aplicação de exemplo do .NET *HelloKeyVault* e um exemplo de serviço Azure web. [Exemplos de código de Cofre de palavras chave Azure](http://www.microsoft.com/download/details.aspx?id=45343)
- Iniciação para o ajudar a aprender a utilizar Azure do Cofre de palavras chave a partir de uma aplicação web em Azure. [Utilize o Cofre de palavras chave Azure de uma aplicação Web](key-vault-use-from-web-application.md)

## <a name="how-tos"></a>Procedimentos

Os seguintes artigos e cenários de fornecem uma orientação específica de tarefas para trabalhar com Azure do Cofre de palavras chave:

- [Alterar o Cofre de palavras chave tenant ID depois de mover subscrição](key-vault-subscription-move-fix.md) - quando mover a sua subscrição Azure de Tenants A para tenant B, os cofres de chaves existentes estão inacessíveis por principais (utilizadores e aplicações) em tenant correcção B. utilizar este manual.
- [Aceder ao Cofre de tecla de protegido por firewall](key-vault-access-behind-firewall.md) - aceder a uma chave do Cofre de palavras as necessidades da aplicação cliente Cofre de palavras chave para conseguir aceder a vários pontos finais para várias funcionalidades.

- [Como gerar e chaves de Transfer HSM-Protected para o Cofre de palavras do Azure chave](key-vault-hsm-protected-keys.md) - Isto irá ajudar a planear, gerar e em seguida, transferir as suas próprias chaves protegidas HSM para utilizar com Azure do Cofre de palavras chave.
- [Como passar valores de seguros (por exemplo, palavras-passe) durante a implementação](../resource-manager-keyvault-parameter.md) - quando é necessário transmitir um valor de seguro (por exemplo, uma palavra-passe) como um parâmetro durante a implementação, pode armazenar esse valor como uma palavra-passe numa Azure do Cofre de palavras chave e o valor de outros modelos de Gestor de recursos de referência.
- [Como utilizar o Cofre de palavras chave para gestão de chaves extensível com o SQL Server](https://msdn.microsoft.com/library/dn198405.aspx) - o SQL Server Connector para o Cofre de palavras do Azure chave permite SQL Server e SQL-na-a-VM aproveitar o serviço de Azure do Cofre de palavras chave como um fornecedor de gestão de chave Extensible (EKM) para proteger as chaves de encriptação de ligação de aplicações; Encriptação de dados transparente, a encriptação de cópia de segurança e encriptação de nível de coluna.
- [Como implementar certificados VMs do Cofre de palavras chave](https://blogs.technet.microsoft.com/kv/2015/07/14/deploy-certificates-to-vms-from-customer-managed-key-vault/) - uma aplicação de nuvem em execução numa VM nas necessidades Azure um certificado. Como pode obter este certificado para este VM hoje?
- [Como programa de configuração do Cofre de palavras chave com o ponto a ponto da rotação da chave e a auditoria](key-vault-key-rotation-log-monitoring.md) - este descontraídos através de como configurar a auditoria com Azure do Cofre de palavras chave e a rotação da chave.

Para obter mais informações específicas de tarefa integração e utilização de chave cofres com Azure, consulte [exemplos de modelo de Gestor de recursos Azure dos Ryan Jones de Cofre de palavras chave](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples).

## <a name="integrated-with-key-vault"></a>Integrado com o Cofre de palavras chave

Estes artigos são sobre outros cenários e serviços que efectuar nos ou integram o Cofre de palavras chave.

- [Encriptação de disco Azure](../security/azure-security-disk-encryption.md) tira partido da funcionalidade de [BitLocker](https://technet.microsoft.com/library/cc732774.aspx) padrão da indústria do Windows e a funcionalidade de [Encriptação DM](https://en.wikipedia.org/wiki/Dm-crypt) do Linux para fornecer encriptação do volume do sistema operativo e os discos de dados. A solução é integrada com Azure do Cofre de palavras chave para ajudar a controlar e gerir as chaves de encriptação do disco e os segredos na sua subscrição do Cofre de palavras chave, assegurando simultaneamente que os discos de máquina virtual de todos os dados são encriptados em repouso no seu armazenamento Azure.


## <a name="supporting-libraries"></a>Bibliotecas de suporte

- [Biblioteca de núcleo do Cofre de palavras de chave do Microsoft Azure](http://www.nuget.org/packages/Microsoft.Azure.KeyVault.Core) fornece `IKey` e `IKeyResolver` interfaces para chaves de identificadores de localizar e efectuar operações com chaves.

- [Extensões de Cofre de palavras chave do Microsoft Azure](http://www.nuget.org/packages/Microsoft.Azure.KeyVault.Extensions) fornece capacidades alargadas Azure do Cofre de palavras chave.

## <a name="other-key-vault-resources"></a>Outros recursos de Cofre de palavras chave
- [Blogue do Cofre de palavras chave](http://aka.ms/kvblog)
- [Fórum de Cofre de palavras chave](http://aka.ms/kvforum)
