<properties
   pageTitle="Notas de lançamento do cofre API de 2. x do .NET de chave | Microsoft Azure"
   description="Programadores .NET irão utilizar este API ao código para Cofre de chave do Azure"
   services="key-vault"
   documentationCenter=""
   authors="BrucePerlerMS"
   manager="mbaldwin"
   editor="bruceper" />
<tags
   ms.service="key-vault"
   ms.devlang="CSharp"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="10/07/2016"
   ms.author="bruceper" />

# <a name="azure-key-vault-net-20---release-notes-and-migration-guide"></a>Azure cofre chave .NET 2.0 - Guia de migração e notas de lançamento

As seguintes notes e orientações são para programadores trabalhar com o Azure chave cofre .NET / c# biblioteca. Na mudar da 1.0 versão para a versão 2.0, foram efetuado um número de atualizações que irá necessitam de trabalho de migração no seu código por ordem para que beneficiem das vantagens das melhorias funcionais e funcionalidade adições como o suporte de certificados de chave cofre.

Suporte de certificados de chave cofre fornece para a gestão do seu x509 certificados e os seguintes comportamentos:  

-   Permite que o proprietário de um certificado criar um certificado através de um processo de criação de chave cofre ou através da importação de um certificado existente. Isto inclui ambos personalizada assinada e autoridade de certificação gerado certificados.

- Permite que um proprietário do certificado cofre chave implementar o armazenamento seguro e gestão de X509 certificados sem interação com material da chave privada.  

-   Permite que o proprietário de um certificado criar uma política que o direciona cofre chave para gerir o ciclo de vida de um certificado.  

-   Permite que os proprietários de certificado fornecer informações de contacto notificação sobre eventos de ciclo de vida de expiração e renovação de certificado.  

-   Suporta a renovação automática com emitentes selecionados - chave cofre parceiro X509 fornecedores de certificados / autoridades de certificação.
    - Tenha em atenção - que não sejam uma parceria fornecedores/autoridades também são permitidas, mas, não vai suportar a funcionalidade de renovação automática.


## <a name="net-support"></a>Suporte para o .NET
- **.NET 4.0** não é suportado pela versão 2.0 do .NET Azure chave cofre / c# biblioteca
- **.NET Core** é suportado pela versão 2.0 do .NET Azure chave cofre / c# biblioteca

## <a name="namespaces"></a>Espaços de nomes
- O espaço de nomes para **modelos** de for alterado de **Microsoft.Azure.KeyVault** para **Microsoft.Azure.KeyVault.Models**.
- O espaço de nomes de **Microsoft.Azure.KeyVault.Internal** é ignorado.
- O espaço de nomes do Azure SDK dependências são alteradas a partir do **Hyak.Common** e **Hyak.Common.Internals** para **Microsoft.Rest** e **Microsoft.Rest.Serialization**


## <a name="type-changes"></a>Tipo de alterações
- *Secreta* alterado para *SecretBundle*
- *Dicionário* alterado para *IDictionary*
- *Lista<T>, [] da cadeia* alterado para *IList<T> *
- *NextList* alterado para *NextPageLink*


## <a name="return-types"></a>Tipos de retorno
- Irão devolver **KeyList** e **SecretList** *IPage<T> * em vez de *ListKeysResponseMessage*
- O gerado **BackupKeyAsync** irá devolver *BackupKeyResult* que contém o *valor* (blob de cópia de segurança). Antes do método foi moldado e devolver apenas o valor.

## <a name="exceptions"></a>Exceções
- *KeyVaultClientException* é alterado para *KeyVaultErrorException*
- O erro de serviço for alterado de *exceção. Erro* para *exceção. Body.Error.Message*.
- Removido informações adicionais a mensagem de erro para **[JsonExtensionData]**.

## <a name="constructors"></a>Construtores
- Em vez de aceitar uma *HttpClient* como um argumento de construtor, o construtor só aceita *HttpClientHandler* ou *[DelegatingHandler]*.



## <a name="downloaded-packages"></a>Pacotes transferidos  
Quando um cliente está a processar uma dependência de chave do cofre foram transferido o seguinte
#### <a name="previous-package-list"></a>Lista de pacote anterior
- versão compactar id="Hyak.Common" = "1.0.2" targetFramework = "net45"
- versão compactar id="Microsoft.Azure.Common" = "2.0.4" targetFramework = "net45"
- versão compactar id="Microsoft.Azure.Common.Dependencies" = "1.0.0" targetFramework = "net45"
- versão compactar id="Microsoft.Azure.KeyVault" = "1.0.0" targetFramework = "net45"
- versão compactar id="Microsoft.Bcl" = "1.1.9" targetFramework = "net45"
- versão compactar id="Microsoft.Bcl.Async" = "1.0.168" targetFramework = "net45"
- versão compactar id="Microsoft.Bcl.Build" = "1.0.14" targetFramework = "net45"
- versão compactar id="Microsoft.Net.Http" = "2.2.22" targetFramework = "net45"

#### <a name="current-package-list"></a>Lista de pacote atual
- versão compactar id="Microsoft.Azure.KeyVault" = "2.0.0-preview" targetFramework = "net45"
- versão compactar id="Microsoft.Rest.ClientRuntime" = "2.2.0" targetFramework = "net45"
- versão compactar id="Microsoft.Rest.ClientRuntime.Azure" = "3.2.0" targetFramework = "net45"


## <a name="class-changes"></a>Alterações de classe

- **UnixEpoch** classe foi removido
- Classe **Base64UrlConverter** é o nome alterado para **Base64UrlJsonConverter**

## <a name="other-changes"></a>Outras alterações

- Suporte para a configuração de política de repetir operação KV falhas breves foi adicionado a esta versão da API do.



## <a name="microsoftazuremanagementkeyvault-nuget"></a>Microsoft.Azure.Management.KeyVault NuGet
- Para as operações que devolvido *Cofre*, o tipo devolvido foi uma classe que continha uma propriedade cofre. O tipo devolvido é agora *Cofre*.
- *PermissionsToKeys* e *PermissionsToSecrets* são agora *Permissions.Keys* e *Permissions.Secrets*
- Algumas das alterações de tipos de retorno aplicam-se ao controlo-plano também.

## <a name="microsoftazurekeyvaultextensions-nuget"></a>Microsoft.Azure.KeyVault.Extensions NuGet
- O pacote resultou em erro para cima para **Microsoft.Azure.KeyVault.Extensions** e **Microsoft.Azure.KeyVault.Cryptography** para as operações de criptografia.
