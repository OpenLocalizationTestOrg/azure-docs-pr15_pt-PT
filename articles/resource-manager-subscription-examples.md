<properties
   pageTitle="Cenários e exemplos para governação subscrição | Microsoft Azure"
   description="Fornece exemplos de como implementar o governação subscrição Azure para cenários comuns."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="rdendtler"
   manager="timlt"
   editor="tysonn"/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/05/2016"
   ms.author="rodend;karlku;tomfitz"/>

# <a name="examples-of-implementing-azure-enterprise-scaffold"></a>Exemplos de execução scaffold Azure enterprise

Este tópico fornece exemplos de como uma empresa pode implementar as recomendações para um [scaffold Azure enterprise](resource-manager-subscription-governance.md). Utiliza uma empresa fictícia com o nome Contoso para ilustrar melhores práticas para cenários comuns. 

## <a name="background"></a>Fundo

Contoso é uma empresa em todo o mundo que fornece soluções de cadeia de fornecimento de clientes em tudo a partir de um modelo de "Como um serviço de Software" a um modelo de embalado implementado no local.  Estes desenvolvem software em todo o mundo com centros de desenvolvimento significativos na Índia, dos Estados Unidos e Canadá. 

Parte da ISV da empresa é dividida em várias unidades de empresas independentes a gestão de produtos um negócio significativo. Cada unidade de negócio tem as suas próprias programadores, gestores de produtos e arquitectura. 

A unidade de negócio de serviços de tecnologia de empresa (ETS) fornece a capacidade de TI centralizada e gere várias centros de dados onde unidades empresariais alojam o seu pedido. Juntamente com a gerir os centros de dados, a organização ETS fornece e gere centralizada colaboração (tal como o e-mail e Web sites) e serviços de rede/telefonia. Também gerir das cargas de trabalho do cliente destinado mais pequenas para unidades de negócio que não têm docentes operacional. 

As pessoas seguintes são utilizadas neste tópico:

- Dave é o administrador do ETS Azure.
- Alice é Director de desenvolvimento Contoso na unidade de negócio de cadeia de fornecimento. 

Contoso tem de criar uma aplicação de linha de negócio e uma aplicação de cliente destinado. Decidiu-se executar as aplicações no Azure. Dave lê o tópico de [governação à subscrição](resource-manager-subscription-governance.md) e está agora pronto para implementar as recomendações. 

## <a name="scenario-1-line-of-business-application"></a>Cenário 1: aplicação de linha de negócio

Contoso é construir um sistema de gestão de código de origem (BitBucket) para ser utilizado por programadores em todo o mundo.  A aplicação utiliza infraestrutura como um serviço (IaaS) para o alojamento e consiste em servidores web e um servidor de base de dados. Acedem a programadores servidores nos seus ambientes de programação, mas não precisa de acesso aos servidores no Azure. Contoso ETS deseje permitir que o proprietário de aplicação e equipa gerir a aplicação. A aplicação só está disponível na rede da empresa da Contoso. Dave tem de configurar a subscrição para esta aplicação. A subscrição também irá alojar outro software de Programador no futuro.  

### <a name="naming-standards--resource-groups"></a>Padrões de nomenclatura e grupos de recursos

Dave cria uma subscrição do programador ferramentas que são as unidades de negócio comuns de suporte. Ele tem de criar nomes que fazem sentido para os grupos de subscrição e de recursos (para a aplicação e as redes). Ele cria os seguintes grupos de subscrição e recursos:

| Item | Nome | Descrição |
| ---- | ---- | ----------- |
| Subscrição | Produção de DeveloperTools contoso ETS | Suporta ferramentas de programador comuns |
| Grupo de recursos | rgBitBucket | Contém o servidor da aplicação web e o servidor de base de dados |
| Grupo de recursos | rgCoreNetworks | Contém as redes virtuais e ligação de gateway de site para o site |


### <a name="role-based-access-control"></a>Controlo de acesso baseado em funções

Depois de criar a sua subscrição, Dave quer para se certificar de que o equipas adequadas e proprietários de aplicação acede aos seus recursos. Dave reconhece que cada equipa possui requisitos diferentes. Utiliza os grupos que foram sincronizados a partir no local da Contoso do Active Directory (AD) para o Azure Active Directory e fornece o nível de acesso para as equipas de à direita. 

Dave atribui as seguintes funções para a subscrição: 

| Função | Atribuído a | Descrição |
| ---- | ----------- | ----------- |
| [Proprietário](./active-directory/role-based-access-built-in-roles.md#owner) | Gerido ID a partir da Contoso AD | Este ID é controlada com a opção apenas no access de tempo (JIT) através da ferramenta de gestão de identidades da Contoso e assegura que o acesso do proprietário subscrição está totalmente auditado. |
| [Gestor de segurança](./active-directory/role-based-access-built-in-roles.md#security-manager) | Departamento de gestão de segurança e risco | Esta função permite aos utilizadores para o Centro de segurança do Azure e o estado dos recursos. |
| [Rede de contribuinte](./active-directory/role-based-access-built-in-roles.md##network-contributor) | Equipa de rede | Esta função permite a equipa de rede da Contoso gerir a VPN do Site para Site e as redes Virtual. |
| *Função personalizada* | Proprietário de aplicação | Dave cria uma função que concede a capacidade de modificar recursos dentro do grupo de recursos. Para mais informações, consulte [Funções de personalizada no Azure RBAC](./active-directory/role-based-access-control-custom-roles.md) |

### <a name="policies"></a>Políticas

Dave tem os seguintes requisitos para gerir os recursos na subscrição:

- Uma vez que as ferramentas de desenvolvimento suportam os programadores em todo o mundo, ele não pretende impedir que os utilizadores criar os recursos no qualquer região. No entanto, ele precisa de saber onde são criados recursos. 
- Ele é preocupem com custos. Por conseguinte, ele pretende impedir que os proprietários de aplicação crie desnecessariamente dispendiosas máquinas virtuais.  
- Uma vez que esta aplicação serve os programadores de muitos unidades de negócio, ele quer marcar cada recurso com o proprietário de unidade e a aplicação de negócio. Ao utilizar estas marcas, ETS pode bill as equipas de adequado.

Ele cria as seguintes [políticas de Gestor de recursos](resource-manager-policy.md): 

| Campo | Efeito | Descrição |
| ----- | ------ | ----------- |
| localização | auditoria | A criação dos recursos presentes qualquer região de auditoria |
| tipo | Negar | Negar criação de máquinas virtuais de série G |
| etiquetas | Negar | Exigir a etiqueta de proprietário de aplicação |
| etiquetas | Negar | Exigir a etiqueta do Centro de custo |
| etiquetas | Acrescentar | Acrescentar o nome de etiqueta **BusinessUnit** e o valor de etiqueta **ETS** a todos os recursos |


### <a name="resource-tags"></a>Etiquetas de recursos

Dave compreende que precisa de ter informações específicas na fatura para identificar o Centro de custos para a implementação de BitBucket. Para além disso, Dave pretende saber todos os recursos que é o proprietário ETS. 

As seguintes [tags](resource-group-using-tags.md) ele adiciona os grupos de recursos e recursos. 
 
| Nome de etiqueta | Valor de etiqueta |
| -------- | --------- |
| ApplicationOwner | O nome da pessoa que gere a esta aplicação. |
| CostCenter | O Centro de custos do grupo que é paga pelo consumo Azure. |
| BusinessUnit | **ETS** (a unidade de negócio associada à subscrição) |

### <a name="core-network"></a>Rede de Core

A Contoso ETS informações de segurança e risco equipa de gestão revê o plano de proposta do Dave para mover a aplicação para Azure. Que pretendem para se certificar de que a aplicação não é exposta à internet.  Dave também tem as aplicações do programador que no futuro serão movidas para Azure. Estas aplicações requerem interfaces públicas.  Para cumprir estes requisitos, ele fornece internas e externas redes virtuais e um grupo de segurança de rede para restringir o acesso.

Ele cria os seguintes recursos:

| Tipo de recurso | Nome | Descrição |
| ------------- | ---- | ----------- |
| Rede virtual | vnInternal | Utilizada com a aplicação BitBucket e está ligado através de ExpressRoute à rede empresarial da Contoso.  Uma sub-rede (sbBitBucket) fornece a aplicação com um espaço de endereços IP específico. |
| Rede virtual | vnExternal | Disponível para futuras aplicações que requerem pontos finais de público. |
| Grupo de segurança de rede | nsgBitBucket | Garante que a superfície de ataque deste carga de trabalho está minimizada por permitir ligações apenas na porta 443 para a sub-rede onde a aplicação encontra-se (sbBitBucket). |

### <a name="resource-locks"></a>Bloqueios de recursos 

Dave reconhece que deve ser protegida a conectividade da rede da empresa da Contoso à rede interna virtual a partir de qualquer wayward script ou acidentais. 

Ele cria a seguinte [Bloquear recurso](resource-group-lock-resources.md):

| Tipo de bloqueio | Recurso | Descrição |
| --------- | -------- | ----------- |
| **CanNotDelete** | vnInternal | Impede a eliminação de rede virtual ou sub-redes, mas não impede que a adição de novas sub-redes. |

### <a name="azure-automation"></a>Automatização Azure 

Dave não tem nada para automatizar para esta aplicação. Apesar de ele criado uma conta de automatização do Azure, ele inicialmente não utiliza. 

### <a name="azure-security-center"></a>Centro de segurança do Azure 

Necessita de gestão de serviços de TI contoso rapidamente e processar ameaças. Também querer compreender que problemas poderão existir.  

Para cumprir estes requisitos, Dave permite que o [Centro de segurança do Azure](./security-center/security-center-intro.md)e oferece um acesso à função de Gestor de segurança. 

## <a name="scenario-2-customer-facing-app"></a>Cenário 2: aplicação de cliente destinado

A liderança empresas na unidade de negócio de cadeia de fornecimento identificou vários oportunidades para aumentar o empenho com os clientes da Contoso utilizando um cartão de fidelidade. Equipa Alice tem de criar esta aplicação e decida que Azure aumenta a sua capacidade para satisfazer as necessidades de negócio. Alice funciona com Dave a partir do ETS para configurar as duas subscrições para desenvolver e funcionamento esta aplicação.

### <a name="azure-subscriptions"></a>Subscrições do Azure 

Dave inicia sessão Portal da empresa do Azure e vê o departamento de cadeia de fornecimento já existe.  No entanto, como este projeto é o primeiro projecto de desenvolvimento para a equipa de cadeia de fornecimento no Azure, Dave reconhece a necessidade de uma nova conta para a equipa de desenvolvimento da Alice.  Ele cria a conta "R & D" para a sua equipa e atribui acesso a Alice. Alice inicia sessão através do portal de conta e cria duas subscrições: uma para colocar em espera os servidores de desenvolvimento e outra para colocar em espera os servidores de produção.  Posteriormente, segue-se as normas de nomenclatura estabelecidas anteriormente quando criar as seguintes subscrições: 

| Utilização de subscrição | Nome |
| ---------------- | ---- |
| Desenvolvimento | SupplyChain ResearchDevelopment LoyaltyCard desenvolvimento |
| Produção | SupplyChain operações LoyaltyCard produção |

### <a name="policies"></a>Políticas

Dave e Alice discutir a aplicação e identificam que esta aplicação apenas serve clientes na América do Norte região.  Alice e a sua equipa planear a utilização do Azure ambiente do serviço de aplicação e Azure SQL para criar a aplicação. Estes poderão ter de criar máquinas virtuais durante o desenvolvimento.  Alice quiser garantir que os respetivos programadores têm os recursos que necessitam para explorar e analisar os problemas no ETS. 

Para a **subscrição de desenvolvimento**, que criar a política seguinte:

| Campo | Efeito | Descrição |
| ----- | ------ | ----------- |
| localização | auditoria | A criação dos recursos presentes qualquer região de auditoria. |

Não limitar o tipo de um utilizador pode criar no desenvolvimento de sku e não necessitam de etiquetas para qualquer grupos de recursos ou recursos.

Para a **subscrição de produção**, criam as seguintes políticas:

| Campo | Efeito | Descrição |
| ----- | ------ | ----------- |
| localização | Negar | Negar a criação de todos os recursos fora os centros de dados dos EUA. |
| etiquetas | Negar | Exigir a etiqueta de proprietário de aplicação |
| etiquetas | Negar | Exigir a etiqueta de departamento. | 
| etiquetas | Acrescentar | Acrescente a etiqueta para cada grupo de recursos que indica o ambiente de produção. |

Não limitar o tipo de sku que um utilizador pode criar no produção.

### <a name="resource-tags"></a>Etiquetas de recursos 

Dave compreende que precisa de ter informações específicas para identificar os grupos de negócio correta e de faturação que é o proprietário. Define ele tags de recursos para grupos de recursos e recursos. 
 
Nome de etiqueta | Valor de etiqueta |
| -------- | --------- |
| ApplicationOwner | O nome da pessoa que gere a esta aplicação. |
| Departamento | O Centro de custos do grupo que é paga pelo consumo Azure. |
| EnvironmentType | **Produção** (Apesar da subscrição inclui **produção** no nome, incluindo nesta etiqueta permite a identificação mais fácil quando a opção recursos no portal do ou na fatura.) |

### <a name="core-networks"></a>Redes Core

A Contoso ETS informações de segurança e risco equipa de gestão revê o plano de proposta do Dave para mover a aplicação para Azure. Que pretendem para se certificar de que a aplicação de cartão de fidelidade está corretamente, isolada e protegida por uma rede DMZ.  Para cumprir este requisito, Dave e Alice criar uma rede virtual externa e um grupo de segurança de rede para identificar a aplicação de cartão de fidelidade da Contoso da rede da empresa.  

Para a **subscrição de desenvolvimento**, que criar:

| Tipo de recurso | Nome | Descrição |
| ------------- | ---- | ----------- |
| Rede virtual | vnInternal | Funciona o ambiente de desenvolvimento Contoso fidelidade cartão e está ligado através de ExpressRoute à rede empresarial da Contoso. |

Para a **subscrição de produção**, que criar:

| Tipo de recurso | Nome | Descrição |
| ------------- | ---- | ----------- |
| Rede virtual | vnExternal | Aloja a aplicação de fidelidade cartão e não está ligado diretamente ao ExpressRoute da Contoso. Código é enviado através do seu sistema de código fonte diretamente para os serviços de PaaS. |
| Grupo de segurança de rede | nsgBitBucket | Garante que a superfície de ataque deste carga de trabalho está minimizada por permitir apenas no vinculadas a comunicação no TCP 443.  Contoso é também investigar a utilizar uma Firewall de aplicação Web para protecção adicional. |  

### <a name="resource-locks"></a>Bloqueios de recursos 

Dave e Alice conferem e optar por adicionar bloqueios recurso em alguns dos recursos chaves no ambiente, para evitar eliminações acidentais durante push um código de erro. 

Que criar o bloqueio seguinte:

| Tipo de bloqueio | Recurso | Descrição |
| --------- | -------- | ----------- |
| **CanNotDelete** | vnExternal | Para impedir que as pessoas a eliminação de rede virtual ou sub-redes. O bloqueio não impede que a adição de novas sub-redes. |

### <a name="azure-automation"></a>Automatização Azure 

Alice e a sua equipa de desenvolvimento tem runbooks extenso para gerir o ambiente para esta aplicação. Os runbooks permita a adição eliminação de nós para a aplicação e outras tarefas de DevOps. 

Para utilizar estes runbooks, que os mesmos ativam [Automatização](./automation/automation-intro.md).

### <a name="azure-security-center"></a>Centro de segurança do Azure 

Necessita de gestão de serviços de TI contoso rapidamente e processar ameaças. Também querer compreender que problemas poderão existir.  

Para cumprir estes requisitos, Dave permite que o Centro de segurança do Azure. Ele assegura que o Centro de segurança do Azure está a acompanhar os recursos e oferece um acesso às equipas DevOps e segurança. 

## <a name="next-steps"></a>Próximos passos

- Para saber mais sobre a criação de modelos de Gestor de recursos, consulte o artigo [melhores práticas para criar modelos de Gestor de recursos do Azure](resource-manager-template-best-practices.md).

*[Karl Kuhnhausen](https://github.com/karlkuhnhausen) contribuiu para neste tópico.*
