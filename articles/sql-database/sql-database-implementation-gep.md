<properties
   pageTitle="Base de dados do Azure SQL Azure caso prático da Microsoft - GEP | Microsoft Azure"
   description="Saiba mais sobre como GEP utiliza a base de dados SQL para alcançar mais globais clientes e alcançar eficiência maior"
   services="sql-database"
   documentationCenter=""
   authors="CarlRabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/08/2016"
   ms.author="carlrab"/>

# <a name="azure-gives-gep-global-reach-and-greater-efficiency"></a>Azure dá alcance global GEP e eficiência maior

![Logótipo GEP](./media/sql-database-implementation-gep/geplogo.png)

GEP fornece software e serviços que permitem líderes de contratos em todo o mundo para maximizar o impacto na operações dos seus negócios, estratégias e espetáculos financeiros. Para além de serviços de consultoria e geridos, empresa oferece smart card por GEP®, uma plataforma baseado na nuvem, abrangente contratos-software. No entanto, GEP tivemos limitações tentar suporta soluções como smart card por GEP com as suas próprias no local centros de dados: investimentos necessários foram acentuados e os requisitos de regulamentação noutros países seriam efetuadas os investimentos necessários mais desencorajador ainda. Ao mover na nuvem, GEP tem disponibilizado recursos de TI, permitindo-lhe se preocupe menor sobre operações IT e concentrar-se mais sobre como desenvolver novas fontes de valor aos seus clientes em todo o mundo.

## <a name="expanding-services-and-growth-by-using-azure"></a>Expandir serviços e crescimento utilizando Azure

Smart Card por clientes GEP adora a plataforma funcionalidades e facilidade de utilização; os clientes podem gerir os seus processos a partir de qualquer lugar, em qualquer altura e em qualquer dispositivo — portátil, tablet ou telemóvel. Ao mover para a Microsoft Azure, GEP foi conseguir acomodar o seu rápido crescimento e o respetivo potencial para expandir para novos mercados. De acordo com VP de tecnologia do GEP de Dhananjay Nagalkar, "Microsoft Azure tem reproduzidos uma função de chave no sucesso do GEP, permitindo-nos Dimensionar rapidamente serviços com agilidade bem como fornecendo centros de dados regionais que ajudam-nos satisfazer as necessidades de regulamentação dos nossos clientes globais."

## <a name="the-limitations-of-a-do-it-yourself-datacenter"></a>As limitações de um centro de dados resolução

No 2013, líderes GEP reconheceram que eles necessários uma forma para se certificar de desempenho e escalabilidade enquanto eles cresceram base os seus clientes. Nagalkar explicado, "para satisfazer essa procura com os nossos centros de dados existentes, recomendamos teria que expandir a nossa infraestrutura e recursos de TI consideravelmente. A investimento e o período de tempo para que teria sido enormes." No local física e máquinas virtuais exigir extensa configuração, gestão, dimensionamento, cópia de segurança e aplicação de patches taxa de juro que teria sido custo proibição para GEP. Soluções de nuvem, outro lado, oferecem simplificar e conveniência ativado GEP focar-se mais no desenvolvimento em vez de gestão de grandes dimensões — e em crescimento — operações IT. Nagalkar Sabia que GEP poderia reduzir o overhead infraestrutura de comprar, configuração e gestão de por migrar para a nuvem.

GEP também necessária uma forma de ultrapassar barreiras regulamentação que mantidos-terminar alguns mercados globais. Muitos dos clientes Europeu de potenciais do GEP, conformidade de regulamentação seria necessário ter dados armazenados no respetivos locais regiões geográficas. Mas -seria não ter sido prático para GEP construir vários centros de dados. "Investimentos amplamente infraestrutura e trabalho IT custos seriam trazer um impacto significativo para margens," de acordo com Nagalkar. "Como resultado, podemos foram realmente forçados para ativar ausente potenciais clientes que os dados armazenados localmente necessários."

Nagalkar Sabia que uma solução de nuvem pode estar a resposta para este dilema do. Se GEP foi possível mover a um fornecedor de nuvem com uma presença global, poderia melhor correspondam aos seus clientes regulamentação e precisa de desempenho ao armazenamento de dados mais perto localizações física dos clientes.

## <a name="finding-smooth-skies-in-the-cloud"></a>Localizar skies suaves na nuvem

Nagalkar e a sua equipa explorou várias opções de nuvem, mas a maior parte foram infraestrutura-como-a-service (IaaS) – com base soluções que iria tem necessários GEP de investir muito em recursos de TI para configurar e gerir o serviço. Ajustar a solução (PaaS) de plataforma-como-a-service Azure ativada para ser uma melhor muito.

"Com Azure, GEP não necessite de mais fácil lidar com gestão de base de dados, configuração virtual máquina, corrigir ou outras tarefas de gestão de infraestrutura," indicada Nagalkar. "Em vez disso, vamos concentrar-nossos recursos o que recomendamos fazer melhores: tirar partido da nossa conhecimentos nos contratos para escrever software verdadeiramente fornece os resultados para dos nossos clientes." 

Na verdade, o comando Mover para Azure ativou GEP encolher o respetiva a equipa de TI de operações permitindo em simultâneo maior funcionalidade para clientes.

Tirando partido da Azure centros de dados ao longo do globo, GEP facilmente pode expandir o seu alcance ao longo da Europa e países da Ásia. Os centros de dados globais ativar GEP para dimensionar com agilidade e para corresponder às necessidades do cliente para dados armazenados localmente que reduz a latência e cumprem os requisitos de regulamentação.

## <a name="smart-by-gep-architecture"></a>INTELIGENTE por arquitectura GEP

GEP criadas smart card por GEP desde o início para cima no Azure. Uma motivação crítica para GEP foi maior escalabilidade, menos tempo de inatividade, e os custos de manutenção reduzida que pode experimentar GEP com base de dados do SQL Azure em comparação com que GEP podem atingir no local. No entanto, uma vez movidos para a nuvem, GEP descobriu novas oportunidades de desenvolvimento na nuvem, como protótipos rápido e engenharia lean para melhor responder às suas necessidades de cliente. Desenvolver no Azure informar GEP ausente fazer com o preocupações com que os programadores podem ocorrer no local de licenciamento de software. O essencial INTELIGENTE por GEP é a base de dados SQL Azure, embora GEP utiliza vários outros serviços Azure para rapidamente e facilmente continuar melhorar a smart card por GEP.

![INTELIGENTE por GEP arquitectura](./media/sql-database-implementation-gep/figure1.png) figura 1. INTELIGENTE por arquitectura GEP

## <a name="structured-data"></a>Dados estruturados

Coração do SMART por aplicação GEP são as instâncias de base de dados do Azure SQL dessa solução power a gestão de aprovisionamento de empresa. Quando GEP engenharia smart card por GEP, viu base de dados do SQL Azure como uma opção perfeita para sua arquitetura, que permitirá a empresa para alcançar o grau mais alto de proteção de dados e cumprir os requisitos de regulamentação. GEP torna a utilização de várias camadas de proteção de dados que ofertas de base de dados SQL Azure, incluindo:

- Encriptação de dados em repouso através de encriptação de dados transparente.
- Proteger a autenticação através da integração de base de dados do SQL Azure com o Azure Active Directory.
- Limitação de acesso a um subconjunto de dados com a segurança de nível de linha adequado.
- Masking dados em tempo real através das políticas de.
- Monitorizar eventos de base de dados através da auditoria de base de dados do Azure SQL.

> "Pode utilizamos todas estas opções sem fazer alterações qualquer código principais e com mínimo impacto no desempenho," disse Nagalkar.

Ao utilizar a base de dados do SQL Azure, GEP tem automaticamente capacidades de recuperação de falhas maiores do que poderia ter económica engenharia no local devido as funcionalidades de tolerância a falhas incorporadas na base de dados do SQL Azure. GEP utiliza a funcionalidade de replicação do Geo Active Azure SQL base de dados, à forma com vários ativas, legíveis e online secundárias réplicas (sempre no disponibilidade grupos) em diferentes regiões geográficas, para formar pares de alta disponibilidade. Replicação smart card por GEP dados entre as regiões significa que, no caso de falhas de toda a região, GEP facilmente pode recuperar dados de cliente com um ponto de recuperação mínimo objectivo (RPO) e o tempo de recuperação objectivo (RTO).

Cada smart card pelo cliente GEP tem duas instâncias de base de dados do Azure SQL: uma para processamento de transações online (OLTP) e outra para uma análise (como o cliente passam e análise de um relatório). Agrupamentos de base de dados flexível de base de dados SQL Azure ativar GEP gerir facilmente milhares de bases de dados globalmente para processar exigências inesperados de recursos de base de dados. Conjuntos de dados flexível fornecem um meio para GEP para se certificar de que bases de dados do cliente podem Dimensionar conforme necessários, sem indevidamente aprovisionamento ou em-Aprovisiona o, permitindo também GEP para controlar custos. Além disso, uma vez que este é um serviço de PaaS, GEP obtém todas as funcionalidades de base de dados do Azure SQL novas com actualizações automáticas.

## <a name="unstructured-and-semi-structured-data"></a>Não estruturados dados estruturados e semiestruturados

No entanto, algumas smart card por dados do cliente GEP tem menos maneira estruturado armazenamento. Para este tipo de dados, GEP utiliza armazenamento de Blobs do Azure, armazenamento de tabela do Azure e Azure Redis Cache. Armazenamento de Blobs do Azure que aloja aos quaisquer anexos que INTELIGENTES por carregamento de utilizadores GEP para a aplicação. Também é onde INTELIGENTE por GEP armazena em conteúdo estático, como folhas de estilo em cascata (CSS) e ficheiros de JavaScript.

GEP armazena dados de que não sejam cliente-destinado, como smart card mediante GEP registo, os dados no armazenamento de tabela do Azure, que lhe dá GEP eficazmente ilimitado de armazenamento eficiente de custo e as horas de obtenção rápida sem ter de se preocupar sobre como configurar um esquema para os dados. GEP utiliza Azure Redis Cache para uma cache principal.

## <a name="authentication-and-routing"></a>Autenticação e de encaminhamento

Serviço de controlo de acesso Azure (ACS) fornece smart card por utilizadores GEP com uma grande variedade de opções para iniciar sessão no software. Azure ACS pode federar com qualquer fornecedor de identidade intercâmbio de dados de autenticação através de segurança afirmação Markup Language (SAML), tal como o Active Directory Domain Services, identidade Ping, OneLogin ou SiteMinder. Isto ajuda a GEP implementar sessão único (SSO) para clientes sem se preocupar armazenar credenciais de utilizador e manter as políticas de palavra-passe do cliente.

Assim que iniciar sessão, clientes têm acesso aos recursos empresas correto no smart card por GEP. GEP utiliza o Gestor de tráfego de Azure para redirecionar e pedidos de balanceamento de carga provenientes de dispositivos móveis dos clientes e sessões do browser.

## <a name="other-azure-services"></a>Outros serviços do Azure

GEP utiliza um número de outros serviços do Azure para tornar smart card por GEP responder ao cliente necessita. GEP utiliza serviços em nuvem Azure (funções web e trabalhador) para a apresentação de aplicação do anfitrião e os serviços de lógica empresarial seguros. Serviços em nuvem tornam para programadores para gerir a infraestrutura como código (IAC) e implementar o novo smart card por aplicações GEP uma fração do momento em que-necessária com centros de dados no local — tudo isto sem qualquer participação a partir do IT. Os programadores GEP podem utilizar os serviços em nuvem teste ambiente para testar novas versões sem que afetam a implementação de produção atual. Assim que a teste, GEP utiliza as funcionalidades de trocar VIP dos serviços em nuvem Azure para mover o código de transição para o ranhura de produção dentro de um minuto que reduz o tempo de inatividade da implementação.

Para baixar latência da aplicação, GEP utiliza a rede de entrega de conteúdos de Azure (CDN) para colocar em conteúdo estático armazenado em armazenamento de Blobs do Azure (como ficheiros CSS e JavaScript) no servidores edge fechar para os utilizadores. Utilizações GEP Azure Service Bus para suportar os padrões de arquitectura de aplicações que se situa entre publiquem-subscrever para parcialmente comando consulta da separação (CQRS) e camadas de arquitectura com lato e comunicação assíncrona. GEP utiliza dos serviços de multimédia do Azure para melhorar o seu serviço de suporte ao cliente. GEP encontrou que-la facilmente poderia registar vídeos de suporte de utilizador dos serviços de multimédia do Azure. Estes vídeos respondam a perguntas comuns do utilizador, que ajuda a melhorar o smart card à satisfação do utilizador GEP enquanto estiver a demorar alguns da carga de suporte fora de docentes do suporte ao cliente do GEP.

Para enviar milhares de e-mails transaccionais gerados diariamente pelo smart card por GEP, a empresa utiliza a API do .NET SendGrid para integrar com o Azure. Para os programadores GEP, isto é fácil fazer — o suplemento de SendGrid para Azure está disponível para a direita no Azure Marketplace. Os programadores GEP poderiam configurar smart card por GEP utilizando à direita de pacote SendGrid NuGet no Microsoft Visual Studio; GEP TI pode monitorizar SendGrid e-mail o tráfego o software diretamente a partir do Azure.

Por fim, smart card por GEP utiliza máquinas virtuais do Azure — o serviço do Azure IaaS — a anfitrião aplicações e serviços não adequadas, no momento da engenharia, para substituir software-como-a-service (SaaS) ou PaaS soluções. Por exemplo, GEP aloja serviços de API integração em máquinas virtuais para integração de (B2B) de negócio para empresas com sistemas dos clientes no local planeamento de recursos de empresa (ERP) como SAP, Oracle, PeopleSoft, JD Edwards, Microsoft Dynamics GP e Lawson e com soluções de SaaS do cliente para documentos de contratos, tal como faturas do exchange eficientemente.

> "Edifício INTELIGENTE pela GEP na nuvem Microsoft Azure tiver removido completamente o a necessidade de no local IT, não só para GEP mas também para operações de contratos os nossos clientes." 

> — Dhananjay Nagalkar, VP das soluções de tecnologia

## <a name="expand-customer-satisfaction-without-expanding-it"></a>Expandir a satisfação do cliente sem expandir IT

Desde a migrar a partir do centros de dados no local para Azure e construir smart card por GEP de raiz a plataforma Azure, GEP tem aumentado escalabilidade e flexibilidade sem ter de expandir a sua infraestrutura ou a equipa de TI. Na verdade, a empresa não adicionada recursos de TI em mais de quatro anos. O modelo de PaaS conveniente do Azure ativou GEP reduzir as suas despesas sobre a gestão de suporte e operações de fornecedor. Como resultado, GEP foi recursos de foco conseguir no desenvolvimento de software; e desenvolver na nuvem permite que os programadores GEP rapidamente testar novas ideias sem ter de passam a hora coordenação com IT ou preocupar com a no local requisitos de licenciamento. Base de dados SQL Azure ajuda GEP melhor garantir que os seus clientes têm sempre serviço excepcional e o desempenho.
 
## <a name="more-information"></a>Obter mais informações

- Home page do GEP: [GEP](http://www.gep.com)
- Inteligente por GEP: [inteligente por GEP](http://www.smartbygep.com)

##<a name="gep-contributors"></a>Contribuintes GEP

- Huzaifa Matawala, associar Director — Architect, GEP
- Sathyan Narasingh, Gestor de engenharia, GEP
- GEP Deepa Velukutty, arquitecto de base de dados,
