<properties 
    pageTitle="Serviço de autenticação Bus e autorização | Microsoft Azure"
    description="Descrição geral de autenticação de assinatura partilhadas do Access (SA)."
    services="service-bus"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" />
<tags 
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="10/03/2016"
    ms.author="sethm" />

# <a name="service-bus-authentication-and-authorization"></a>Autorização e Bus de serviço de autenticação

Aplicações podem autenticar para Azure Service Bus utilizando a autenticação quer assinatura partilhadas do Access (SA) ou através do Azure Active Directory controlo de acesso (também conhecido como serviço de controlo de acesso ou ACS). Partilhada assinatura acesso autenticação permite aplicações para autenticar Bus serviço utilizando uma tecla de acesso configurada no espaço de nomes ou na entidade que estão associados direitos específicos. Em seguida, pode utilizar esta tecla para gerar um token de partilhado assinatura de acesso que os clientes podem utilizar para autenticar Bus de serviço.

>AZURE. SA importantes é recomendada sobre ACS, à medida que fornece um esquema de autenticação simples, flexível e fáceis de utilizar para Bus de serviço. As aplicações podem utilizar SA em cenários na qual não precisam de gerir o conceito de um autorizados "utilizador".

## <a name="shared-access-signature-authentication"></a>Autenticação de assinatura do Access partilhada

[Autenticação SA](service-bus-sas-overview.md) permite-lhe conceder acesso de um utilizador para recursos de serviço Bus com direitos específicos. Autenticação SA no serviço Bus envolve a configuração de uma chave de criptografia com direitos associados num recurso Bus de serviço. Os clientes, em seguida, podem obter acesso a esse recurso por um token de SA que consiste no recurso URI a ser acedido a apresentar e um termo sessão iniciada com a tecla configurada.

Pode configurar teclas para as SA num espaço de nomes de serviço Bus. A tecla aplica-se a todas as entidades mensagens nesse espaço de nomes. Também pode configurar as teclas em filas de serviço Bus e tópicos. SA também são suportada no serviço Bus reencaminha.

Para utilizar SA, pode configurar um objeto de [SharedAccessAuthorizationRule](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx) num espaço de nomes, fila ou tópico que consiste em dos seguintes procedimentos:

- *Nome chave* que identifica a regra.

- *PrimaryKey* é uma chave de criptografia utilizada para o início de sessão/validar os tokens SA.

- *Chave secundária* é uma chave de criptografia utilizada para o início de sessão/validar os tokens SA.

- *Direitos* que representa a coleção de direitos de ouvir, enviar ou gerir atribuídos.

Regras de autorização de configurados ao nível do espaço de nomes podem conceder acesso a todas as entidades num espaço de nomes para clientes com tokens assinados utilizando a chave correspondente. Até 12 dessa autorização regras podem ser configuradas num serviço Bus espaço de nomes, fila de espera ou tópico. Por predefinição, uma [SharedAccessAuthorizationRule](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx) com todos os direitos está configurado para cada espaço de nomes quando-la pela primeira vez está aprovisionado.

Para aceder a uma entidade, o cliente requer um token de SA gerado utilizando um específico [SharedAccessAuthorizationRule](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx). O token de SA é gerado utilizando o SHA256 HMAC de uma cadeia de recursos que consiste o URI de recurso a que o access é pedido e um termo com uma chave de criptografia associada a regra de autorização.

Suporte para autenticação SA para Bus serviço está incluído nas versões Azure .NET SDK 2.0 e versões posteriores. SA incluem suporte para um [SharedAccessAuthorizationRule](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx). Todos os APIs que aceitar uma cadeia de ligação como um parâmetro incluem suporte para as cadeias de ligação SA.

## <a name="acs-authentication"></a>Autenticação ACS

Autenticação do serviço Bus através do ACS é gerida através de um assistente "-sb" ACS espaço de nomes. Se pretender que um espaço de nomes de ACS acompanha seja criado por um espaço de nomes de Bus de serviço, não é possível criar o seu espaço de nomes de serviço Bus utilizando o portal clássico Azure; tem de criar o espaço de nomes utilizando o cmdlet do PowerShell [New-AzureSBNamespace](https://msdn.microsoft.com/library/azure/dn495165.aspx) . Por exemplo:

```
New-AzureSBNamespace <namespaceName> "<Region>” -CreateACSNamespace $true
```

Para evitar a criação de um espaço de nomes de ACS, emita o seguinte comando:

```
New-AzureSBNamespace <namespaceName> "<Region>” -CreateACSNamespace $false
```

Por exemplo, se criar um espaço de nomes de serviço Bus denominado **contoso.servicebus.windows.net**, um espaço de nomes de ACS acompanha denominado **contoso sb.accesscontrol.windows.net** está aprovisionado automaticamente. Para todos os espaços de nomes que foram criados antes de Agosto de 2014, um espaço de nomes de ACS acompanhamento também foi criado.

Uma identidade de serviço predefinida "proprietário," com todos os direitos, está aprovisionada por predefinição no espaço de nomes de ACS assistente. Pode obter extensivamente controlo a qualquer entidade de serviço Bus através de ACS ao configurar as relações de fidedignidade adequado. Pode configurar identidades de serviço adicionais para gerir o acesso ao entidades Bus de serviço.

Para aceder a uma entidade, o cliente pede um token de SWT a partir do ACS com as afirmações adequadas ao apresentar as suas credenciais. O token SWT tem, em seguida, ser enviado como parte do pedido de serviço bus para ativar a autorização do cliente para acesso à entidade.

Suporte para autenticação ACS para Bus serviço está incluído nas versões Azure .NET SDK 2.0 e versões posteriores. Esta autenticação inclui suporte para um [SharedSecretTokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.sharedsecrettokenprovider.aspx). Todos os APIs que aceitar uma cadeia de ligação como um parâmetro incluem suporte para as cadeias de ligação ACS.

## <a name="next-steps"></a>Próximos passos

Continue a ler a [autenticação de assinatura de acesso partilhado com Bus de serviço](service-bus-shared-access-signature-authentication.md) para obter mais detalhes sobre SA.

Para obter uma descrição de alto nível de SA no serviço Bus, consulte o artigo [Partilhado assinaturas de acesso](service-bus-sas-overview.md).

Pode encontrar mais informações sobre tokens ACS em [como: pedir um Token ACS através do protocolo de MOLDAR OAuth](https://msdn.microsoft.com/library/hh674475.aspx).



