<properties
   pageTitle="Proteger um cluster de serviço ferro | Microsoft Azure"
   description="Descreve os cenários de segurança para um cluster de serviço ferro e diferentes tecnologias utilizadas para implementar esses cenários."
   services="service-fabric"
   documentationCenter=".net"
   authors="ChackDan"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/19/2016"
   ms.author="chackdan"/>

# <a name="service-fabric-cluster-security-scenarios"></a>Cenários de segurança do serviço ferro cluster

Um cluster de serviço ferro é um recurso que é o proprietário. Clusters sempre devem ser protegidos para impedir que os utilizadores não autorizados estabelecer ligação ao seu cluster, especialmente quando tiver das cargas de trabalho de produção em execução no mesmo. Apesar de é possível criar um cluster não seguro, ao fazê-lo por isso, permite que qualquer utilizador anónimo ligar para o mesmo se expõe-gestão os pontos finais para a Internet pública. 

Este artigo fornece uma descrição geral dos cenários de segurança para clusters em execução no Azure ou autónomo e diversas tecnologias utilizadas para implementar esses cenários. Os cenários de segurança de cluster são:

- Nó a nó segurança
- Segurança nó de cliente
- Controlo de acesso baseado em funções RBCA)

## <a name="node-to-node-security"></a>Nó a nó segurança
Protege a comunicação entre o VMs ou máquinas no cluster. Este procedimento garante que apenas os computadores que estejam autorizados a aderir ao cluster podem participar em aplicações e serviços no cluster de alojamento.

![Diagrama de comunicação de nó a nó][Node-to-Node]

Clusters em execução no Azure ou autónomo clusters em execução no Windows podem utilizar o [Certificado de segurança](https://msdn.microsoft.com/library/ff649801.aspx) ou [Segurança do Windows](https://msdn.microsoft.com/library/ff649396.aspx) máquinas com o Windows Server.
### <a name="node-to-node-certificate-security"></a>Segurança de certificado de nó a nó
Serviço ferro utiliza os certificados de servidor x. 509 que especificar como parte das configurações de tipo de nó quando cria um cluster de. Uma descrição geral do que são estes certificados e como pode adquirir ou criá-los é fornecida no final deste artigo.

Certificado de segurança é configurada ao criar o cluster através do portal do Azure, modelos de Gestor de recursos do Azure ou um modelo JSON autónomo. Pode especificar um certificado principal e um certificado secundário opcional que é utilizado para rollovers certificado. Os certificados principais e secundários que especificar devem ser diferentes do AnonymousUserName o cliente de administração e certificados de cliente só de leitura que especificar para a [segurança de nó de cliente](#client-to-node-security).

Para Azure leia [configurar um cluster utilizando um modelo de Gestor de recursos do Azure](service-fabric-cluster-creation-via-arm.md) para saber como configurar a segurança do certificado de um cluster de.

Para autónomo Windows Server leia o artigo [seguro um cluster de autónomo num Windows utilizando certificados x. 509](service-fabric-windows-cluster-x509-security.md)

### <a name="node-to-node-windows-security"></a>Segurança do windows nó a nó
Para autónomo Windows Server leia o artigo [seguro um cluster de autónomo no Windows com segurança do Windows](service-fabric-windows-cluster-windows-security.md)

## <a name="client-to-node-security"></a>Segurança nó de cliente
Autentica clientes e protege a comunicação entre um cliente e nós individuais no cluster. Este tipo de segurança autentica e protege as comunicações de cliente, que garantem que apenas os utilizadores autorizados podem aceder ao cluster e as aplicações implementadas no cluster. Os clientes estão identificados de forma exclusiva através das respetivas credenciais de segurança do Windows ou as respetivas credenciais de segurança do certificado.

![Diagrama de comunicação de nó de cliente][Client-to-Node]

Podem utilizar clusters em execução no Azure ou autónomo clusters em execução no Windows [Certificado de segurança](https://msdn.microsoft.com/library/ff649801.aspx) ou [Segurança do Windows](https://msdn.microsoft.com/library/ff649396.aspx).

### <a name="client-to-node-certificate-security"></a>Segurança de certificado de nó de cliente
 Segurança do certificado de cliente-a-nó está configurada ao criar o cluster através do portal do Azure, modelos de Gestor de recursos ou um modelo JSON autónomo ao especificar um certificado de cliente de administração e/ou um certificado de cliente do utilizador.  Os administrador cliente e o utilizador certificados de cliente que especificar devem ser diferentes do AnonymousUserName os certificados principais e secundários que especificar para [nó a nó segurança](#node-to-node-security).

Ligar ao cluster utilizando o certificado de administração de clientes tem acesso completo às capacidades da gestão.  Os clientes ligar ao cluster utilizando o certificado de cliente do utilizador só de leitura ter acesso de leitura para as capacidades da gestão. Por outras palavras estes certificados são utilizados para a função bases de controlo de acesso (RBAC) mais tarde é descrito neste artigo.

Para Azure leia [configurar um cluster utilizando um modelo de Gestor de recursos do Azure](service-fabric-cluster-creation-via-arm.md) para saber como configurar a segurança do certificado de um cluster de.

Para autónomo Windows Server leia o artigo [seguro um cluster de autónomo num Windows utilizando certificados x. 509](service-fabric-windows-cluster-x509-security.md)

### <a name="client-to-node-azure-active-directory-aad-security-on-azure"></a>Segurança do Azure Active Directory (AAD) nó de cliente no Azure
Clusters em execução no Azure podem também acesso seguro aos pontos finais gestão utilizando o Azure Active Directory (AAD). Consulte o artigo [configurar um cluster por através de um modelo de Gestor de recursos do Azure](service-fabric-cluster-creation-via-arm.md) para obter informações sobre como criar erros AAD necessários, como preenchê-las durante a criação de cluster e como ligar a esses clusters posteriormente.

## <a name="security-recommendations"></a>Recomendações de segurança
Para Azure clusters, é recomendado que utilize segurança AAD para autenticar os clientes e certificados para o nó a nó segurança.

Para autónomo Windows Server clusters é recomendável que utilize segurança do Windows com o grupo geridas contas (g) se tiver o Windows Server 2012 R2 e do Active Directory. Caso contrário, ainda utilize segurança do Windows com contas do Windows.

## <a name="role-based-access-control-rbac"></a>Controlo de acesso baseado em funções RBCA)
Controlo de acesso permite ao administrador de cluster limitar o acesso a determinadas operações de cluster para diferentes grupos de utilizadores, tornar o cluster mais segura. Dois tipos de controlo de acesso diferentes são suportados para ligar a um cluster de clientes: função de administrador e a função de utilizador.

Os administradores têm acesso total a capacidades da gestão (incluindo as capacidades de leitura/escrita). Por predefinição, os utilizadores, têm apenas acesso de leitura para as capacidades da gestão (por exemplo, as capacidades de consulta) e a capacidade de resolver aplicações e serviços.

Especificar as funções de administrador e utilizador cliente no momento da criação do cluster, fornecendo identidades separadas (certificados, etc AAD.) para cada. Para mais informações sobre as definições de controlo de acesso predefinida e como alterar as predefinições, consulte o artigo [o controlo de acesso para clientes ferro serviço baseado em funções](service-fabric-cluster-security-roles.md).


## <a name="x509-certificates-and-service-fabric"></a>Certificados x. 509 e ferro de serviço
Certificados digitais x. 509 frequentemente são utilizados para autenticar os clientes e os servidores de e para encriptar e assinar digitalmente mensagens. Para obter mais detalhes sobre estes certificados, vá para [trabalhar com certificados](http://msdn.microsoft.com/library/ms731899.aspx).

Algumas coisas importantes a ter em conta:

- Certificados utilizados em clusters de cargas de trabalho de produção devem ser criados utilizando um serviço de certificado do Windows Server corretamente configurado ou obtidos a partir de uma [Autoridade de certificação (AC)](https://en.wikipedia.org/wiki/Certificate_authority)de aprovado.
- Nunca utilize qualquer temporário ou testar certificados de produção que são criados com ferramentas como MakeCert.exe.
- Pode utilizar um certificado autoassinado, mas deve apenas fazê-lo para clusters de teste e não na produção.

### <a name="server-x509-certificates"></a>Certificados de x. 509 servidor

Os certificados de servidor de ter a tarefa principal de um servidor (nó) para os clientes de autenticação ou autenticar um servidor (nó) para um servidor (nó). Uma das verificações iniciais quando um cliente ou nó autentica um nó é consultar o valor do nome do comuns no campo assunto. Este nome comuns ou um dos nomes de alternativo de assunto dos certificados tem de ser apresentar na lista de permitidos nomes comuns.

O seguinte artigo descreve como gerar certificados com nomes de alternativas assunto (SAN): [como adicionar um nome alternativo de assunto para um certificado LDAP seguro](http://support.microsoft.com/kb/931351).

O campo assunto pode conter vários valores, cada uma com uma inicialização para indicar o tipo de valor o prefixo. Mais frequentemente, a inicialização destina-se "CN" nome comum; Por exemplo, "CN = www.contoso.com". Também é possível para o campo assunto estar em branco. Se o campo opcional do nome alternativo de assunto é preenchido, tem de conter o nome do certificado comum e uma entrada por nome alternativo do assunto. Estes são introduzidos como valores de nome de DNS.

O valor do campo destinados efeitos do certificado deve incluir um valor adequado, tal como "Servidor autenticação" ou "cliente".

### <a name="client-x509-certificates"></a>Certificados de x. 509 de cliente

Os certificados de cliente não são normalmente emitidos por uma autoridade de certificação de terceiros. Em vez disso, o arquivo pessoal da localização do utilizador actual contém normalmente certificados de cliente aí colocados por uma autoridade de raiz, com a finalidade de "Autenticação de cliente". O cliente pode utilizar essa um certificado quando é necessária uma autenticação comum.

>[AZURE.NOTE] Todas as operações de gestão num cluster de serviço ferro requerem certificados de servidor. Os certificados de cliente não podem ser utilizados para a gestão de.

<!--Every topic should have next steps and links to the next logical set of content to keep the customer engaged-->


## <a name="next-steps"></a>Próximos passos

Este artigo fornece informações conceptuais sobre segurança cluster. A seguinte, [crie um cluster no Azure através de um modelo de Gestor de recursos](service-fabric-cluster-creation-via-arm.md) ou através de do [Azure portal](service-fabric-cluster-creation-via-portal.md).

<!--Image references-->
[Node-to-Node]: ./media/service-fabric-cluster-security/node-to-node.png
[Client-to-Node]: ./media/service-fabric-cluster-security/client-to-node.png
