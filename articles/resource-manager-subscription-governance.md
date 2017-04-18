<properties
   pageTitle="Melhores práticas para empresas mover para o Azure | Microsoft Azure"
   description="Descreve um scaffold que empresas podem utilizar para se certificar de um ambiente seguro e fácil."
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

# <a name="azure-enterprise-scaffold---prescriptive-subscription-governance"></a>Scaffold Azure enterprise - governação à subscrição

Empresas são cada vez que aprova a nuvem pública para a sua agilidade e flexibilidade. Estes são recorrendo forças na nuvem para gerar receita ou otimizar recursos para o negócio. Microsoft Azure fornece inúmeros serviços que podem reunir empresas como blocos modulares para endereçar uma vasta gama de aplicações e das cargas de trabalho. 

No entanto, muitas vezes é difícil saber onde pretende começar. Depois de decidir utilizar Azure, a algumas perguntas surgirem normalmente:

- "Como cumpre os nossos requisitos legais soberania de dados em determinados países/regiões?"
- "Como posso garantir que alguém não inadvertidamente altera um sistema crítico?"
- "Como posso saber o que cada recurso está a suportar, de modo que pode contabilizar- e -o novamente com exatidão de fatura?"

Perspectiva de uma subscrição do vazia com sem guarda-corpos é complicada. Este espaço em branco pode dificultar o seu mover para o Azure.

Este artigo fornece um ponto de partida para profissionais técnicos resolver a necessidade de governação e equilibrá-lo com a necessidade de agilidade. Apresenta-o conceito de um scaffold de empresa guias de introdução do organizações na execução e gerir as suas subscrições Azure. 

## <a name="need-for-governance"></a>Necessidade de governação

Quando mover para Azure, terá de resolver o tópico de governação mais cedo para garantir a utilização da nuvem dentro da empresa com êxito. Infelizmente, a hora e burocracia da criação de um sistema de governação abrangente significa que alguns grupos de empresas ir diretamente para fornecedores, sem que envolvam TI empresariais. Esta abordagem pode deixar a empresa aberta a vulnerabilidades se os recursos não são geridos corretamente. As características da nuvem pública - agilidade, flexibilidade e com base em consumo preços - são importantes para grupos de negócio que precisa para satisfazer rapidamente os pedidos de clientes (internos e externos). Mas, TI empresariais precisa para se certificar de que os dados e sistemas de forma eficaz estão protegidos.

Vida real, andaimes são utilizado para criar a base da estrutura. O scaffold guias de introdução do destaque geral e fornece pontos de âncora para sistemas mais permanentes a ser instalados. Um scaffold enterprise é o mesmo: um conjunto de controlos flexíveis e Azure funcionalidades fornecidas estrutura para o ambiente e as âncoras para serviços criada com base em nuvem pública. Fornece os construtores (IT e grupos de negócio) uma foundation para criar e anexar novos serviços.

O scaffold é baseado práticas que podemos recolheu a partir de muitos compromissos com os clientes de vários tamanhos. Intervalo esses clientes a partir do desenvolvimento de soluções na nuvem, revista Fortune 500 empresas e os fornecedores de software independentes que se encontrem migrar e desenvolver soluções na nuvem para pequenas e médias empresas. O scaffold enterprise é "área propositadamente criada" para ser flexível para suportar tradicionais IT das cargas de trabalho e das cargas de trabalho ágil; tais como os programadores de criação de software-como-a-service (SaaS) aplicações com base no Azure capacidades.

O scaffold enterprise se destina a ser a Fundação de cada nova subscrição no prazo de Azure. -Permite aos administradores garantir que das cargas de trabalho cumprem os requisitos de governação mínimo de uma organização sem a impedi-grupos de negócio e os programadores de rapidamente os seus próprios objetivos da reunião.

> [AZURE.IMPORTANT] Governação é crucial para o sucesso das Azure. Este artigo destina-se da execução técnica do scaffold empresa, mas apenas toca no processo e relações entre os componentes mais amplo. Política governação flua situado na parte superior para baixo e é determinada pela qual o negócio pretenda alcançar. Naturalmente, a criação de um modelo de governação para Azure inclui representantes de IT, mas ainda mais importante deve ter representação forte do coordenadores de grupo de negócio e segurança e gestão de risco. No final, um scaffold enterprise é sobre Mitigam o risco de negócio para facilitar a missão e objetivos de uma organização.

A imagem seguinte descreve os componentes da scaffold. A Fundação baseia-se num plano sólido para os departamentos, contas e subscrições. Os montantes de consistir em políticas de Gestor de recursos e padrões de nomenclatura fortes. Os restantes scaffold provêm de core capacidades Azure e funcionalidades que ativar um ambiente seguro e fácil.

![componentes de scaffold](./media/resource-manager-subscription-governance/components.png)

> [AZURE.NOTE] Azure cresceu rapidamente desde a sua introdução no 2008. Este crescimento necessário Microsoft equipas de engenharia rethink sua abordagem para gerir e implementação dos serviços. O modelo de Gestor de recursos do Azure foi introduzido numa 2014 e substitui o modelo de implementação clássica. Gestor de recursos permitem às organizações implementar mais facilmente, organizar e controlar os recursos Azure. Gestor de recursos inclui parallelization durante a criação de recursos para implementação mais rápida de soluções complexas, interdependentes. Também inclui controlo de acesso granular e a capacidade de recursos de etiqueta com metadados. A Microsoft recomenda que cria todos os recursos através do modelo de Gestor de recursos. O scaffold enterprise explicitamente foi concebido para o modelo de Gestor de recursos.

## <a name="define-your-hierarchy"></a>Definir a hierarquia

A Fundação do scaffold é a inscrição do Azure Enterprise (e o Portal da empresa). A inscrição enterprise define a forma e utilização de serviços Azure dentro de uma empresa e é a estrutura de governação core. Dentro de acordo empresarial, clientes que conseguem subdividir ainda mais o ambiente para os departamentos, contas e, por fim, subscrições. Uma subscrição do Azure é a unidade básica onde todos os recursos estão contidos. Também define os limites várias Azure, como o número de núcleos, recursos, etc.

![hierarquia](./media/resource-manager-subscription-governance/agreement.png)

Cada enterprise é diferente e a hierarquia na imagem anterior permite significativa flexibilidade na como o Azure está organizado dentro da empresa. Antes de implementar as informações contidas neste documento, deve a hierarquia do modelo e compreender o impacto em faturação, acesso a recursos e complexidade.

Os três padrões comuns de inscrição do Azure são:

- O padrão de **funcional**

    ![funcional](./media/resource-manager-subscription-governance/functional.png)

- O padrão de **unidade de negócio** 

    ![empresas](./media/resource-manager-subscription-governance/business.png)

- O padrão de **geográficos**

    ![geográficos](./media/resource-manager-subscription-governance/geographic.png)

Aplicar scaffold ao nível da subscrição para expandir a governação requisitos da empresa para a subscrição.

## <a name="naming-standards"></a>Padrões de nomenclatura

A primeira montantes do scaffold são padrões de nomenclatura. Padrões de nomenclatura bem concebidas permitem-lhe identificar recursos no portal numa fatura e dentro de scripts. Provavelmente, já tem o padrões de nomenclatura para infraestrutura no local. Quando adicionar Azure ambiente do, deverá expandir essas normas de atribuição de nomes para os recursos do Azure. Padrão de nomenclatura facilitar a gestão mais eficaz do ambiente a todos os níveis.

> [AZURE.TIP]
>
> - Rever e adotar onde possível as [orientações padrões e práticas](./guidance/guidance-naming-conventions.md). Estas orientações ajudam-o a decidir sobre um padrão de nomenclatura significativo.
> - Utilize camelCasing para os nomes dos recursos (como myResourceGroup e vnetNetworkName). Nota: Existem certos recursos, tais como contas do armazenamento, onde a única opção é utilizar minúsculas (e outros carateres especiais).
> - Considere utilizar políticas de Gestor de recursos do Azure (descritas na secção seguinte) para impor padrões de nomenclatura.
 
## <a name="policies-and-auditing"></a>Políticas e de auditoria

A segunda montantes do scaffold envolve criar [políticas de Gestor de recursos do Azure](resource-manager-policy.md) e [o registo de atividade de auditoria](resource-group-audit.md). Políticas de Gestor de recursos fornecem-lhe a capacidade para gerir os riscos no Azure. Pode definir políticas que assegurar a soberania de dados através de restrição, impor ou auditoria determinadas ações. 

- A política é um sistema de **Permitir** predefinido. Controlar ações por definir e atribuir políticas para recursos que negar ou ações recursos de auditoria.
- Políticas são descritas pelas definições de política num idioma da definição de política (if-then condições).
- Criar políticas com JSON (Javascript Object Notation) ficheiros formatados. Depois de definir uma política, pode atribuí-lo para um determinado âmbito: de subscrição, de grupo de recursos. ou recurso.

As políticas têm várias ações que permitem uma abordagem extensivamente aos seus cenários. São as ações:

-   **Negar**: bloqueia o pedido de recursos
-   **Auditoria**: permite que o pedido, mas adiciona uma linha para o registo de atividade (que pode ser utilizado para fornecer alertas ou para acionar runbooks)
-   **Acrescentar**: adiciona informações especificadas para o recurso. Por exemplo, se não existir uma etiqueta "CostCenter" um recurso, adicione essa etiqueta com um valor predefinido.

### <a name="common-uses-of-resource-manager-policies"></a>Utilizações comuns das políticas de Gestor de recursos

As políticas de Gestor de recursos Azure são uma poderosa ferramenta no Azure toolkit. Permitem-lhe evitar custos inesperados, para identificar um centro de custos para recursos através de marcação e para garantir que compliancy requisitos são satisfeitos. Quando as políticas são combinadas com as funcionalidades de auditoria incorporadas, pode maneira soluções complexas e flexíveis. As políticas de permitir empresas para fornecer controlos das cargas de trabalho "Tradicional TI" e "Agile" cargas de trabalho; tais como desenvolver aplicações de cliente. Os padrões mais comuns, que vemos para as políticas são:

- **Geo-conformidade/dados soberania** - Azure fornece regiões em todo o mundo. Empresas desejarem, muitas vezes, controlar onde os recursos são criados (quer para garantir a soberania de dados ou apenas para assegurar recursos são criados fechar para os consumidores de fim dos recursos).
- **Gestão de custo** - subscrição An Azure pode conter recursos de muitos tipos e escala. Empresas desejarem, muitas vezes, certifique-se de que subscrições padrão evitem a utilização de recursos desnecessariamente grandes, os quais podem custar centenas de euros um mês ou mais.
- **Predefinido governação pelas etiquetas necessárias** - que exige o etiquetas é uma das funcionalidades mais comuns e altamente pretendidas. Utilizar as políticas de Gestor de recursos do Azure empresas conseguem para se certificar de que um recurso está etiquetado corretamente. As etiquetas mais comuns são: tipo de departamento, o proprietário do recurso e o ambiente (por exemplo - produção, teste, desenvolvimento)

**Exemplos**

"Tradicional IT" subscrição para as aplicações de linha de negócio

-   Impor etiquetas de departamento e o proprietário de todos os recursos
-   Restringir a criação do recurso à região da América do Norte
-   Restringir a capacidade de criar VMs série G e HDInsight Clusters

"Ágil" ambiente para uma unidade de negócio a criação de aplicações na nuvem

- Para cumprir os requisitos de soberania de dados, permitir a criação de recursos apenas numa região específico.
- Impor a etiqueta de ambiente de todos os recursos. Se um recurso é criado sem uma etiqueta, acrescentar a **ambiente: desconhecido** etiqueta para o recurso.
- Auditoria quando recursos são criados fora da América do Norte, mas não impeça a.
- Auditoria quando são criados recursos de custo de alto.

> [AZURE.TIP]
>
> A utilização mais comuns de políticas de Gestor de recursos ao longo organizações é controlo *onde* recursos podem ser criados e *que* tipos de recursos podem ser criados. Além de fornecer controlos no *local onde* e *que*, muitas empresas utilizam políticas para se certificar de recursos tem os metadados adequado para faturar novamente consumo. Recomendamos que a aplicação de políticas de nível de subscrição para:
>
> - Soberania de conformidade-geo/dados
> - Gestão de custo
> - Etiquetas necessárias (determinadas por necessidade de negócio, tal como EndereçoParaCobrança, proprietário de aplicação)
>
> Pode aplicar políticas adicionais níveis inferiores do âmbito.
 
### <a name="audit---what-happened"></a>Auditoria - o que aconteceu?

Para ver como o ambiente está a funcionar, é necessário para auditoria actividade do utilizador. A maioria dos tipos de recursos do Azure criar registos de diagnóstico, que pode analisar através de uma ferramenta de registo ou no conjunto de aplicações do Azure operações gestão. É possível recolher registos de atividade ao longo de múltiplas subscrições para fornecer um departamento ou vista do enterprise. Registos de auditoria são uma ferramenta importante de diagnóstico e um mecanismos cruciais para eventos de accionador no ambiente do Azure.

Registos de atividade a partir do Gestor de recursos implementações permitem-lhe determinar as **operações** que seguiu coloque e quem executou-los. Registos de atividade podem ser recolhidos e agregados utilizando ferramentas de como a análise de registo.

## <a name="resource-tags"></a>Etiquetas de recursos

À medida que os utilizadores na sua organização adicionam recursos para a subscrição, que se torne cada vez mais importante associar recursos o departamento adequados, o cliente e o ambiente. Pode anexar metadados para recursos pelas [etiquetas](resource-group-using-tags.md). Utilizar etiquetas para fornecer informações sobre o recurso ou o proprietário. Etiquetas permitem-lhe não só agregar e agrupar recursos de várias formas, mas utilizar esses dados para fins de encargos. Pode marcar recursos com até 15 pares de chave: valor. 

Etiquetas de recursos são flexíveis e devem ser anexadas a maior parte dos recursos. Exemplos de etiquetas de recurso comuns são:

- CobrarA:
- Departamento (ou unidade de negócio)
- Ambiente (produção, fase, desenvolvimento)
- Camada (Web camada, camada de aplicação)
- Proprietário de aplicação
- Nomeprojeto

![etiquetas](./media/resource-manager-subscription-governance/resource-group-tagging.png)

Para obter mais exemplos de etiquetas, consulte o artigo [recomendado convenções para recursos Azure de nomenclatura](./guidance/guidance-naming-conventions.md).

> [AZURE.TIP]
>
> Crie uma estratégia de etiquetas de rede que identifica nas suas subscrições que metadados são necessário para o negócio, Finanças, segurança, gestão de riscos e gestão global do ambiente de. Considere fazer uma política que exige etiquetagem para:
>
> - Grupos de recursos
> - Armazenamento
> - Máquinas virtuais
> - Servidores de serviço ambientes/web aplicações

## <a name="resource-group"></a>Grupo de recursos 

Gestor de recursos permite-lhe colocados recursos grupos com significado para a afinidade gestão, de faturação ou natural. Como mencionado anteriormente, o Azure tem dois modelos de implementação. No modelo clássico anterior, a unidade básica de gestão do foi a subscrição. Estava de difícil acesso divide recursos dentro de uma subscrição, que por um instrutor para a criação de grandes quantidades de subscrições. Com o modelo de Gestor de recursos, podemos viu a introdução de grupos de recursos. Grupos de recursos são contentores de recursos que tenham um ciclo de vida comuns ou partilham um atributo tal como "todos os servidores de SQL" ou "A aplicação".

Grupos de recursos não podem ser contidos entre si e recursos só podem pertencer a um recurso grupo. Pode aplicar determinadas ações em todos os recursos num grupo de recursos. Por exemplo, a eliminação de um grupo de recursos remove todos os recursos no interior do grupo de recursos. Normalmente, coloque uma aplicação completa ou sistema relacionado no mesmo grupo de recursos. Por exemplo, uma aplicação de três camadas denominada aplicação Web da Contoso iria conter o servidor web, o servidor de aplicações e o SQL server em máquinas mesmo grupo de recursos.

> [AZURE.TIP]
>
> Como pode organizar os seus grupos de recursos pode variar entre das cargas de trabalho "Tradicional TI" e das cargas de trabalho "Ágil TI"
>
> - "Tradicional IT" das cargas de trabalho com mais frequência são agrupadas por itens dentro do ciclo de vida mesmo, tal como uma aplicação. O agrupamento por aplicação permite para gestão de aplicações individuais.
> - "Ágil IT" das cargas de trabalho tendem para focar-se em aplicações externas cliente destinado na nuvem. Os grupos de recursos devem refletir as camadas de implementação (como Web camada, camada de aplicação) e gestão.

## <a name="role-based-access-control"></a>Controlo de acesso baseado em funções

Provavelmente estiver a fazer-se "quem deverá ter acesso aos recursos?" e "como controlar este acesso?" Permitir ou não permitir acesso ao portal do Azure e controlar o acesso aos recursos no portal do são crucial. 

Quando Azure inicialmente foi publicado, controlos de acesso a uma subscrição foram básicos: administrador ou administrador de cocriação. Aceder a uma subscrição do acesso de modelo implicado clássico para todos os recursos no portal. Esta falta de um controlo mais preciso por um instrutor para a multiplicação de subscrições para fornecer um nível de controlo de acesso razoável para uma inscrição Azure.

Já não é necessária, este multiplicação de subscrições. Com o controlo de acesso baseado em funções, pode atribuir utilizadores a funções padrão (tal como "leitor" e "writer" tipos comuns de funções). Também pode definir funções personalizadas.

> [AZURE.TIP]
>  
> - Ligar o arquivo de identidade empresarial (normalmente Active Directory) Azure Active Directory utilizando a ferramenta de AD Connect.
> - Controlar o administrador/Co-administrador de uma subscrição utilizando uma identidade gerida. **Não** atribuir a administração/Co-administrador para um novo proprietário de subscrição. Em alternativa, utilize funções RBAC para fornecer direitos de **proprietário** para um grupo ou individuais.
> - Adicione utilizadores Azure a um grupo (por exemplo, aplicação proprietários X) no Active Directory. Utilize o grupo sincronizado para fornecer os direitos adequados para gerir o grupo de recursos que contém a aplicação de membros do grupo.
> - Siga o princípio do menor **privilégio** necessário para fazer o trabalho esperado de conceder. Por exemplo:
>     - Grupo de implementação: Um grupo de que só é possível implementar recursos.
>     - Gestão de máquina virtual: Um grupo de que consiga reiniciar VMs (para operações de)

## <a name="azure-resource-locks"></a>Bloqueios de recurso Azure

Tal como a sua organização adiciona serviços principais para a subscrição, que se torne cada vez mais importante para se certificar de que desses serviços estão disponíveis para evitar interrupção de empresas. [Os bloqueios de recurso](resource-group-lock-resources.md) permitem-lhe restringir operações em recursos de valor alto onde modificar ou eliminá-los deverá ter um impacto significativo no seu aplicações ou infraestrutura da nuvem. Pode aplicar bloqueios para uma subscrição, grupo de recursos ou recurso. Normalmente, aplicar bloqueios para recursos base destinados como redes virtuais, gateways e contas de armazenamento. 

Os bloqueios de recurso atualmente, dois valores: CanNotDelete e só de leitura. CanNotDelete significa que os utilizadores (com os direitos adequados) podem continuar ler ou modificar um recurso, mas não é possível eliminá-la. Só de leitura significa que os utilizadores autorizados não é possível eliminar ou modificar um recurso.

Para criar ou eliminar bloqueios de gestão, tem de ter acesso a `Microsoft.Authorization/*` ou `Microsoft.Authorization/locks/*` ações.
Das funções incorporadas, apenas o proprietário e o administrador de acesso de utilizador são concedidas essas ações.

> [AZURE.TIP] Opções de rede Core devem ser protegidas com bloqueios. Acidentais de um gateway, VPN do site para o site seria desastrosa numa subscrição do Azure. Azure não lhe permitir eliminar uma rede virtual que está a ser utilizado, mas aplicar restrições mais é uma precauções úteis. Políticas de também são cruciais para a manutenção dos controlos adequados. Recomendamos que se aplicam um cadeado **CanNotDelete** para as políticas que estão em utilização.
>
> - Rede virtual: CanNotDelete
> - Grupo de segurança de rede: CanNotDelete
> - Políticas: CanNotDelete

## <a name="core-networking-resources"></a>Recursos de redes Core

Acesso aos recursos pode ser internos (dentro da rede da empresa) ou externos (através da internet). É fácil para os utilizadores na sua organização inadvertidamente colocar recursos no local errado e potencialmente abri-los para o access malicioso. Tal como acontece com dispositivos no local, empresas tem de adicionar controlos adequados para se certificar de que os utilizadores Azure tomar as decisões à direita. Para governação de subscrição, identificamos recursos de core que fornecem controlo básico do access. Os recursos core é composta por:

- **Redes virtuais** são objectos de contentor para sub-redes. Apesar de não estritamente necessário, que é normalmente utilizado quando ligar as aplicações internas recursos da empresa.
- **Grupos de segurança de rede** são semelhantes a uma firewall e forneça regras para como um recurso pode "falar" através da rede. Fornecem um controlo granular sobre como / se uma sub-rede (ou máquina virtual) pode ligar-se à Internet ou outras sub-redes na mesma rede virtual.

![redes Core](./media/resource-manager-subscription-governance/core-network.png)

> [AZURE.TIP]
>  
> - Crie redes virtuais dedicados à externo destinado cargas de trabalho e direcionados cargas de trabalho. Esta abordagem reduz o risco de colocação inadvertidamente máquinas virtuais que se destinam internas das cargas de trabalho num espaço oposta externo.
> - Grupos de segurança de rede são fundamentais para esta configuração. No mínimo, bloquear o acesso à internet a partir de redes virtuais internas e bloquear o acesso à rede empresarial das redes externas de virtuais.

### <a name="automation"></a>Automatização

Gerir os recursos individualmente é ambas moroso e potencialmente sujeito para determinadas operações a erros. Azure fornece capacidades de automatização diferentes, incluindo Azure automatização, aplicações de lógica e Azure funções. [Azure automatização](./automation/automation-intro.md) permite aos administradores criar e definir runbooks para processar tarefas comuns no gestão de recursos. Criar runbooks utilizando um editor de código do PowerShell ou um editor de gráfico. Pode produzir fluxos de trabalho em várias fases complexos. Automatização Azure é frequentemente utilizada para processar tarefas comuns, como encerrar recursos não utilizados ou criar recursos em resposta a um accionador específico sem necessitar de intervenção humana.

> [AZURE.TIP]
>
> - Criar uma conta de automatização do Azure e reveja o runbooks disponíveis (linha de comando e gráfica) disponíveis na [Galeria de livro execuções](./automation/automation-runbook-gallery.md).
> - Importar e personalizar runbooks chave para utilização própria.
>
> Um cenário comum é a capacidade de máquinas virtuais de início/encerramento numa agenda. Existem runbooks de exemplo que estão disponíveis na Galeria de que tanto processam este cenário e ensina como para expandi-lo.


## <a name="azure-security-center"></a>Centro de segurança do Azure

Talvez um dos bloqueadores maiores para o cloud adoção foi as preocupações sobre segurança. Os gestores de risco de TI e os departamentos de segurança necessitam assegurar que os recursos no Azure são seguros. 

O [Centro de segurança do Azure](./security-center/security-center-intro.md) fornece uma vista central do Estado de segurança dos recursos nas subscrições e recomendações que ajuda a evitar comprometidos recursos. Pode ativar a políticas mais granulares (por exemplo, aplicar políticas de aos grupos de recurso específico que permitem da empresa personalizar os respetivos postura para o risco está a lidar). Por fim, o Centro de segurança do Azure é uma plataforma aberta que permite parceiros da Microsoft e fornecedores independentes de software para criar software que se ligue à Centro de segurança do Azure para melhorar as suas capacidades. 

> [AZURE.TIP]
>
> Centro de segurança do Azure está ativado por predefinição no cada subscrição. No entanto, tem de ativar a recolha de dados a partir do máquinas virtuais para permitir que o Centro de segurança do Azure instalar o seu agente e começar a recolha de dados.
>
> ![recolha de dados](./media/resource-manager-subscription-governance/data-collection.png)

## <a name="next-steps"></a>Próximos passos

- Agora que aprendeu sobre governação de subscrição, é tempo para ver estas recomendações na prática. Veja [exemplos de execução governação Azure subscrição](resource-manager-subscription-examples.md).

*[Karl Kuhnhausen](https://github.com/karlkuhnhausen) contribuiu para neste tópico.*
