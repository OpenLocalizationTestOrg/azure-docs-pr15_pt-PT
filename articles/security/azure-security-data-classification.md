<properties
   pageTitle="Classificação de dados para Azure | Microsoft Azure"
   description="Este artigo fornece uma introdução aos conceitos básicos da classificação de dados e realça o valor correspondente, mais especificamente no contexto da nuvem computação e utilizar o Microsoft Azure"
   services="security"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor="TomSh"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/16/2016"
   ms.author="yurid"/>

# <a name="data-classification-for-azure"></a>Classificação de dados para Azure

Este artigo fornece uma introdução aos conceitos básicos da classificação de dados e realça o valor correspondente, mais especificamente no contexto da nuvem computação e utilizar o Microsoft Azure. 

## <a name="data-classification-fundamentals"></a>Conceitos básicos da classificação de dados

Classificação de dados com êxito numa organização necessita de detecção abrangente necessidades da sua organização e um conhecimento aprofundado sobre onde residem os seus ativos de dados.  
 
Existem dados de uma das três Estados básicos: 

- Em repouso 
- Processo 
- Quando estão em trânsito 
 
Todos os três Estados exigem exclusivas soluções técnicas para classificação de dados, mas os princípios aplicados da classificação de dados deverão ser a mesma para cada um. Precisa de dados que são classificados como confidenciais para se manter confidencial quando na resto, processo e, em trânsito. 
 
Também podem ser dados estruturados ou não estruturados. Processos de classificação típicas para dados estruturados encontrados nas bases de dados e folhas de cálculo são menos complexo e moroso gerir daqueles para os dados não estruturados, como documentos, o código de origem e e-mail. 

> [AZURE.TIP] Para mais informações sobre as capacidades Azure e as melhores práticas para os dados encriptação leia o artigo [Melhores práticas do Azure dados encriptação](azure-security-data-encryption-best-practices.md)

Em geral, organizações terão mais dados não estruturados que dados estruturados. Independentemente de se os dados forem estruturadas ou não estruturados, é importante para si gerir sensibilidade de dados. Quando corretamente implementada, classificação de dados ajuda-o a garantir que os dados confidenciais elementos são geridos com supervisão maior do que os elementos de dados que são considerados público ou gratuito distribuir. 

### <a name="controlling-access-to-data"></a>Controlar o acesso a dados 

Autenticação e autorização frequentemente confusos entre si e respectivas funções mal interpretados. Na realidade, são bastante diferentes, conforme apresentado na figura seguinte.  

![Acesso a dados e controlo](./media/azure-security-data-classification/azure-security-data-classification-fig1.png)

### <a name="authentication"></a>Autenticação 

Autenticação normalmente consiste em, pelo menos, duas partes: um ID de utilizador ou o nome de utilizador para identificar um utilizador e um token, tal como uma palavra-passe para confirmar que a credencial de nome de utilizador é válida. O processo não fornece o utilizador autenticado com acesso a todos os itens ou serviços; verifica se que o utilizador é quem que dizem são.   

> [AZURE.TIP] [Azure Active Directory](../active-directory/active-directory-whatis.md) fornece serviços de identidade baseado na nuvem que permitem-lhe autenticar e autorizar utilizadores. 

### <a name="authorization"></a>Autorização
 
A autorização é o processo de fornecer um utilizador autenticado a capacidade de aceder a uma aplicação, conjunto de dados, ficheiro de dados ou outro objeto algumas. Atribuir utilizadores autenticados direitos de utilização, modificar ou eliminar itens que podem aceder necessita de atenção para classificação de dados. 

Autorização bem sucedida necessita de implementação de uma mecanismos para validar as necessidades os utilizadores individuais para aceder a ficheiros e informações baseada numa combinação de funções, a política de segurança e a política de risco considerações. Por exemplo, dados a partir de aplicações específicas de (LOB) de linha de negócio não poderão ter de ser acedido por todos os funcionários e apenas um subconjunto dos empregados pequenas provavelmente terá acesso aos ficheiros de recursos humanos (HR). Mas, para organizações para controlar quem pode aceder a dados, como assim como quando e como um sistema eficaz para autenticar os utilizadores têm de estar no local. 

> [AZURE.TIP] no Microsoft Azure, certifique-se tirar partido Azure Role-Based acesso controlo RBCA () para conceder apenas a quantidade de acesso que os utilizadores têm de efetuar as suas tarefas. Leia as [atribuições de funções de utilização para gerir o acesso para os recursos do Azure Active Directory](../active-directory/role-based-access-control-configure.md) para obter mais informações. 

### <a name="roles-and-responsibilities-in-cloud-computing"></a>Funções e responsabilidades no informática em nuvem 

Apesar de fornecedores de nuvem podem ajudar a gerir os riscos, clientes necessitam assegurar que gestão de classificação de dados e imposição está corretamente implementada para fornecer o nível adequado de serviços de gestão de dados.  
 
Responsabilidades típicas do classificação dados variam consoante no qual o modelo de serviço na nuvem está no local, conforme apresentado na figura seguinte. Os modelos de serviço de nuvem primária três são infraestrutura como um serviço (IaaS), a plataforma como um serviço (PaaS) e software como um serviço (SaaS). Também irá variar consoante a dependência de e as expectativas do fornecedor de nuvem pós-implementação dos mecanismos de classificação de dados. 

![Funções](./media/azure-security-data-classification/azure-security-data-classification-fig2.png)

Apesar de é responsável por classificação dos seus dados, fornecedores de nuvem devem certificar compromissos escritos sobre como serão seguro e manter a privacidade dos dados de cliente armazenados os respetivos na nuvem.  

- Requisitos de **fornecedores de IaaS** estão limitados a garantir que o ambiente virtual pode acomodar capacidades de classificação de dados e requisitos de conformidade do cliente. IaaS fornecedores tem uma função mais pequena de classificação de dados, uma vez que precisam apenas para se certificar de que os dados do cliente endereços requisitos de conformidade. No entanto, fornecedores ainda tem garantir que os respetivos ambientes virtuais endereço requisitos de classificação de dados para além de proteger os seus centros de dados.
- Responsabilidades típicas do **PaaS fornecedores** podem ser misto, porque a plataforma pode ser utilizada numa segmentação abordagem para fornecer a segurança de uma ferramenta de classificação. Fornecedores de PaaS podem ser responsáveis de autenticação e possivelmente algumas regras de autorização e tem de fornecer segurança e capacidades de classificação de dados na camada de aplicação. Muito como IaaS fornecedores, fornecedores PaaS necessário assegurar que a sua plataforma está em conformidade com os requisitos de classificação quaisquer dados relevantes.
- **Fornecedores de SaaS** frequentemente será considerado como parte de uma cadeia de autorização e irá precisar para se certificar de que os dados armazenados na SaaS aplicação pode ser controlada por tipo de classificação. Aplicações de SaaS podem ser utilizadas para aplicações LOB e por sua necessidade de natureza para fornecer os meios para autenticar e autorizar dados que são utilizados e armazenados. 

## <a name="classification-process"></a>Processo de classificação 

Muitas organizações que compreender a necessidade de classificação de dados e pretendam implementá-lo cara um desafio básico: onde começar?

Uma forma simple e eficaz para implementar a classificação de dados é utilizar a verificação de plano, fazer, AGIR modelo a partir do [MOF](https://technet.microsoft.com/solutionaccelerators/dd320379.aspx). A figura seguinte gráficos as tarefas que são necessários para implementar com êxito classificação de dados neste modelo.  

1. **Planear**. Identifique uma entidade de custódia de dados para implementar o programa de classificação e desenvolver perfis de proteção de elementos de dados. 
2. **DO**. Depois de políticas de classificação de dados são aceitou após, implemente o programa e implementar tecnologias de imposição, conforme necessário para os dados confidenciais.  
3. **Verificar**. Verifique e relatórios para se certificar de que as ferramentas e métodos a ser utilizados são eficazmente endereçar as políticas de classificação de validar. 
4. **ACT**. Rever o estado de acesso a dados e rever ficheiros e dados que requerem revisão utilizando uma metodologia de transferência e revisão aprovar as alterações e para novos riscos de endereço.  

![Planear,, verifique, agir](./media/azure-security-data-classification/azure-security-data-classification-fig3.png)
 
###<a name="select-a-terminology-model-that-addresses-your-needs"></a>Seleccionar um modelo de terminologia endereços às suas necessidades
 
Existem vários tipos de processos para a classificação de dados, incluindo os processos de manuais, processos baseadas na localização que classificar dados com base na localização de um utilizador ou do sistema, processos baseados em aplicações como classificação de base de dados específicas e automatizado processos utilizados por diversas tecnologias, algumas das quais são descritas na secção "Proteger dados confidenciais" mais adiante.  
 
Este artigo apresenta dois modelos de terminologia GRG são baseados em modelos de bem utilizados e respeitadas indústria. Estes modelos de terminologia, que ambos fornecem três níveis de sensibilidade de classificação, são apresentados na seguinte tabela.  

> [AZURE.NOTE] Quando classificar um ficheiro ou recurso que combina dados faria normalmente ser classificados em níveis diferentes, o nível mais alto de classificação apresentar deverá estabelecer a classificação geral. Por exemplo, um ficheiro que contenha dados confidenciais e restritos deve ser classificado como restritos.  

| **Sensibilidade**   | **Modelo de terminologia 1**   | **Modelo de terminologia 2** |
|--------------------|---------------------------|-------------------------|
| Alto               | Confidencial              | Restritos              |
| Médio             | Apenas para utilização interna     | Confidenciais               |
| Baixa                | Público                    | Sem restrições            |

#### <a name="confidential-restricted"></a>Confidencial (restringida) 

Informações que são classificadas como confidencial ou restrito incluem dados que podem ser grave para um ou mais indivíduos e/ou organizações se comprometida ou perdidas. Essas informações frequentemente são fornecidas numa base "precisa de saber" e podem incluir: 

- Dados pessoais, incluindo informação identificativa como Segurança Social ou números de identificação nacionais, números de passport, números de cartão de crédito, do controlador números de licenças, registos médicos e números de ID de política de seguros de saúde.  
- Registos financeiros, incluindo números de conta financeira como verificar ou investimento conta. 
- Material de negócio, como documentos ou de dados que são a propriedade intelectual exclusiva ou específica.  
- Dados legais, incluindo potencial advogado privilegiados material. 
- Dados de autenticação, incluindo chaves de criptografia privado, pares de palavras-passe do nome de utilizador ou outras sequências de identificação como ficheiros de chaves biométricos privados. 

Dados que são classificados como confidenciais frequentemente tem regulamentação e requisitos de conformidade para processamento de dados. 

#### <a name="for-internal-use-only-sensitive"></a>Para utilização interna apenas (sensível)
 
Informações que são classificadas como sendo de média sensibilidade incluem ficheiros e dados que não teria um impacto extremas num indivíduo e/ou a organização se perdidos ou destruídos. Essas informações poderão incluir: 

- Correio eletrónico, a maior parte das quais pode ser eliminado ou distribuído sem a causar uma crise (excluindo o correio eletrónico ou caixas de correio a partir de pessoas que estão identificadas na classificação confidencial).  
- Os documentos e ficheiros que não inclua dados confidenciais.
 
Geralmente, esta classificação inclui tudo o que não sejam confidenciais. Esta classificação pode incluir a maior parte dos dados de negócio, uma vez que a maioria dos ficheiros que são geridos ou utilizados no dia a dia podem ser classificados como sensível. À exceção dos dados que são tornados público ou que seja confidenciais, todos os dados dentro de uma organização empresas podem ser classificados como sensível por predefinição. 

#### <a name="public-unrestricted"></a>Público (sem restrições)
 
Informações que são classificadas como público incluem dados e os ficheiros que não são críticos para as necessidades da empresa ou operações. Esta classificação também pode incluir dados deliberadamente foi lançados ao público para a sua utilização, como material de marketing ou prima anúncios. Além disso, esta classificação pode incluir dados tal como mensagens de e-mail de spam armazenados por um serviço de e-mail. 

### <a name="define-data-ownership"></a>Definir a propriedade dos dados
 
É importante estabelecer uma cadeia de limpar pena de propriedade para todos os elementos de dados. A tabela seguinte identifica funções de que é o proprietário de dados diferentes nos esforços de classificação de dados e os respetivos respectivos direitos.  

| **Função**        | **Criar**    | **Modificar/eliminar**   | **Delegado**  | **Leitura**    | **Arquivo/restauro**   |
|-----------------|---------------|---------------------|---------------|-------------|-----------------------|
| Proprietário           | X             | X                   | X             | X           | X                     |
| Entidade de custódia       |               |                     | X             |             |                       |
| Administrador   |               |                     |               |             | X                     |
| Utilizador\*          |               | X                   |               | X           |                       |
**Os utilizadores podem ser concedidos direitos adicionais, tal como editar e eliminar por uma entidade de custódia* 

> [AZURE.NOTE] Esta tabela não fornece uma lista exaustiva das funções e direitos, mas apenas uma amostra representativa. 

O **proprietário de elementos de dados** é o criador de blocos original dos dados, quem podem delegar a propriedade e atribuir uma entidade de custódia. Quando é criado um ficheiro, o proprietário deverá conseguir atribuir uma classificação, o que significa que tenham uma responsabilidade para compreender o que precisa de ser classificados como confidencial com base em políticas da respetiva organização. Todos os dados de um proprietário de elementos de dados podem ser classificados automática como para utilização interna apenas (sensível), a menos que são responsáveis pelo proprietário ou criar tipos de dados (restritos) confidenciais. Frequentemente, função o proprietário será alterada depois dos dados são classificados. Por exemplo, o proprietário poderá criar uma base de dados das informações classificadas e cedência dos seus direitos para a entidade de custódia de dados.  

> [AZURE.NOTE] proprietários de elementos de dados com frequência utilizam uma mistura de serviços, dispositivos e multimédia, algumas das quais são pessoais e alguns dos quais pertencem à organização. Uma política de limpar organizacional pode ajudar a garantir que a utilização da dispositivos como computadores portáteis e dispositivos inteligentes está em conformidade com diretrizes de classificação de dados.  

A **entidade de custódia de elementos de dados** é atribuído pelo proprietário ativos (ou os respetivos delegado) para gerir elementos de acordo com acordos com o proprietário de elementos ou em conformidade com os requisitos de política aplicável. Idealmente, a função da entidade de custódia pode ser implementada num sistema de automatizada. Uma entidade de custódia elementos assegura que os controlos de acesso necessário são fornecidos e é responsáveis para gerir e proteger elementos delegados aos seus cuidados de. Podem incluir as responsabilidades da entidade de custódia elementos:  

- Proteger o activo em conformidade com a direcção o proprietário de elementos ou de acordo com o proprietário de elementos 
- Garantir que são respeitadas políticas de classificação 
- Informar os proprietários de elementos de quaisquer alterações ao aceitou após controlos e/ou procedimentos de proteção antes dessas alterações tendo efeito 
- Relatório para o proprietário de elementos sobre as alterações ou remoção de responsabilidades da entidade de custódia activo 
- Um **administrador** representa um utilizador que é responsável por garantir que é mantida a integridade, mas não estiverem um proprietário de elementos de dados, a entidade de custódia ou o utilizador. Na verdade, funções de administrador a vários fornecem serviços de gestão do contentor de dados sem ter acesso aos dados. A função de administrador inclui cópia de segurança e restauro dos dados, a manutenção de registos de activos e escolher, ao adquirir e funcionamento os dispositivos e armazenamento de que alojam os ativos. 
- O utilizador ativo inclui qualquer pessoa que é concedido acesso aos dados ou de um ficheiro. Atribuição de acesso é frequentemente delegada pelo proprietário para a entidade de custódia ativo.  

### <a name="implementation"></a>Pós-implementação
  
Considerações sobre gestão aplica a todos os métodos de classificação. As seguintes considerações precisar de incluir detalhes sobre quem, o que, onde, quando e porque é que um recurso de dados seria ser utilizado, pode ser consultado, alterado ou eliminado. Todos os gestão de elementos devem ser feito com uma compreensão do como uma organização vê os seus riscos, mas pode ser aplicada uma metodologia simple, tal como foi definido no processo de classificação de dados. Considerações adicionais para a classificação de dados incluem a introdução das novas aplicações e ferramentas e gerir alterar depois de um método de classificação é implementado.  

### <a name="reclassification"></a>Transferência
 
Reclassificar ou alterar o estado de classificação de um ativo de dados tem de ser concluído quando um utilizador ou sistema determina que foi alterada de perfil de importância ou risco os elementos de dados. Este esforço é importante para assegurar que o estado de classificação continua a ser atual e válida. Pode ser classificado automaticamente ou com base em utilização por uma entidade de custódia de dados ou proprietário de dados a maior parte do conteúdo que não é classificada manualmente. 

### <a name="manual-data-reclassification"></a>Transferência de manual de dados
 
Idealmente, este esforço seria garantir que os detalhes de uma alteração capturados e auditados. O motivo mais provável para transferência manual seria por razões de confidencialidade, ou para registos mantidos no formato de papel ou um requisito para rever os dados que foram originalmente mal classificados. Uma vez que este papel considera classificação de dados e mover dados na nuvem, os esforços de transferência manual seriam requerem atenção no caso a caso e uma revisão de gestão de risco seria ideal para requisitos de classificação de endereço. Em geral, como um esforço classifica política da organização sobre o que precisa de ser classificados, o estado de classificação predefinido (todos os dados e ficheiros a ser sensíveis a maiúsculas e mas não confidenciais) e levá exceções para os dados de alto risco. 

### <a name="automatic-data-reclassification"></a>Transferência de dados automática
 
Transferência de dados automática utiliza a mesma regra geral, como classificação manual. A exceção é que soluções automatizadas podem Certifique-se de que as regras são seguidas e aplicadas conforme necessário. Classificação de dados pode ser feita como parte de uma política de imposição de classificação de dados, que pode ser imposta quando os dados são armazenados, utilizado e, em trânsito com a tecnologia de autorização.

- Baseados em aplicações. Por predefinição, utilizando determinadas aplicações define um nível de classificação. Por exemplo, os dados a partir de software de gestão (CRM) de relação de clientes, HR e ferramentas de gestão de registos do Estado de funcionamento serão confidenciais por predefinição. 
- Baseado na localização. Localização de dados pode ajudar a identificar a sensibilidade de dados. Por exemplo, os dados armazenados por um HR ou departamento financeiro são mais prováveis confidenciais natureza.  
 
### <a name="data-retention-recovery-and-disposal"></a>Retenção de dados, valorização e eliminação 

Recuperação de dados e eliminação, como a transferência de dados, é um aspecto essencial de gerir elementos de dados. Os princípios para a recuperação de dados e eliminação seriam definidos por uma política de retenção de dados e impostos da mesma maneira como a transferência de dados; como um esforço seria ser realizado pelas funções de administrador e entidade de custódia como uma tarefa de colaboração.  

Falha tenham uma política de retenção de dados foi vermelha perda de dados ou falha de cumprir os requisitos de regulamentação e legal deteção. A maioria das organizações que não tenham uma política de retenção de dados claramente definidos tendência para utilizar uma política de retenção "manter tudo" predefinido. No entanto, uma política de retenção tem riscos adicionais em cenários de serviços na nuvem. 

Por exemplo, uma política de retenção de dados para fornecedores de serviço de nuvem pode considerar tal como "a duração da subscrição" (desde que o serviço é paga por, os dados são guardados). Um contrato de pagamento para retenção não dirigir políticas de retenção empresarial ou de regulamentação. Definir uma política para dados confidenciais pode garantir que os dados são armazenados e removidos com baseados no práticas recomendadas. Além disso, uma política de arquivo pode ser criada para formalizar Noções sobre que dados devem ser eliminados e quando. 

Política de retenção de dados deverá abordar obrigatório regulamentação e requisitos de conformidade, bem como os requisitos de retenção legal da empresa. Dados classificados poderão provocar perguntas sobre a duração de retenção e exceções para os dados que tem sido armazenados com um fornecedor; essas questões estão mais predispostos para dados que não foi classificados corretamente. 

> [AZURE.TIP] Saiba mais sobre as políticas de retenção de dados do Azure e mais lendo o [Contrato de subscrição Online da Microsoft](https://azure.microsoft.com/support/legal/subscription-agreement/)

## <a name="protecting-confidential-data"></a>Proteger dados confidenciais
  
Depois de dados são classificados, localizar e implementar a formas de proteger dados confidenciais torna-se uma parte integrante qualquer estratégia de implementação de proteção de dados. Proteger dados confidenciais necessita de atenção adicional para armazenados e dados transmitidos em arquitecturas convencionais, assim como a nuvem. 

Esta secção fornece informações básicas sobre algumas tecnologias de que podem automatizar esforços de imposição para ajudar a proteger os dados que foi classificados como confidenciais. 
 
Como na figura seguinte mostra, estas tecnologias podem ser implementadas como no local ou soluções baseadas na nuvem — ou de uma forma de híbrido, com algumas das-las implementado no local e algumas na nuvem. (Algumas tecnologias, tais como encriptação e gestão de direitos, também alargar a dispositivos de utilizador.)  

![Tecnologias](./media/azure-security-data-classification/azure-security-data-classification-fig4.png)

### <a name="rights-management-software"></a>Software de gestão de direitos  

Uma solução para prevenir a perda de dados é software de gestão de direitos. Ao contrário abordagens que tentam interromper o fluxo de informações em pontos de saída numa organização, o software de gestão de direitos funciona em níveis abrangente dentro de tecnologias de armazenamento de dados. Documentos são encriptados e o controlo sobre quem pode desencriptá-los utiliza os controlos de acesso que são definidos numa solução de controlo de autenticação como um serviço de diretório.  

> [AZURE.TIP] Pode utilizar o Azure Rights Management (Azure RMS) como a solução de proteção de informações de proteger os dados em cenários diferentes. Leitura [o que é o Azure Rights Management?](https://docs.microsoft.com/rights-management/understand-explore/what-is-azure-rms) para mais informações sobre esta solução Azure.

Algumas das vantagens de software de gestão de direitos incluem: 

- Distinta informações confidenciais. Os utilizadores podem proteger os seus dados diretamente utilizando aplicações com capacidade de gestão de direitos. Sem passos adicionais necessários — cocriação de documentos, enviar e-mail e publicar dados oferecem uma experiência de proteção de dados consistentes. 
- Proteção viaja com os dados. Os clientes permanecem no controlo de quem tem acesso aos seus dados, se na nuvem, infraestrutura de TI existente, ou no ambiente de trabalho do utilizador. As organizações podem optar por encriptar os respetivos dados e restringir o acesso de acordo com as suas necessidades de negócio. 
- Políticas de proteção de informações predefinidas. Os administradores e utilizadores que podem utilizar as políticas de padrão para muitos cenários de negócio comuns, tais como "Empresa confidencial – só de leitura" e "Não reencaminhar." Um conjunto avançado de utilização direitos são suportados como ler, copiar, imprimir, guardar, editar e avançar para permitir flexibilidade na definição de direitos de utilização personalizados. 

> [AZURE.TIP] Pode proteger dados armazenamento do Windows Azure utilizando [Azure encriptação do serviço de armazenamento](../storage/storage-service-encryption.md) de dados at Rest. Também pode utilizar a [Encriptação do Azure do disco](azure-security-disk-encryption.md) para ajudar a proteger dados contidos em discos virtuais utilizados para máquinas virtuais do Azure.

### <a name="encryption-gateways"></a>Gateways de encriptação

Trabalhar com os gateways de encriptação nas suas próprias camadas para fornecer serviços de encriptação por reencaminhamento todo o acesso aos dados baseado na nuvem. Esta abordagem não deve ser confundida com que uma rede privada virtual (VPN). Os gateways de encriptação foram concebidos para fornecer uma camada transparente para soluções baseadas na nuvem.   

Os gateways de encriptação podem fornecer um meios para gerir e proteger dados que foi classificados como confidencial através da encriptação os dados estão em trânsito e dados at rest.  
 
Os gateways de encriptação são colocados no fluxo de dados entre dispositivos de utilizador e os dados da aplicação centros de serviços de encriptação/desencriptação. Estas soluções, tais como as VPNs, essencialmente são soluções no local. São concebidos para fornecer de terceiros com controlo sobre chaves de encriptação que ajuda a reduzir o risco de colocação de gestão de dados e uma tecla com um fornecedor. Essas soluções destinam-se, à semelhança encriptação, para trabalhar de forma totalmente integrada e transparente entre os utilizadores e o serviço. 

> [AZURE.TIP] Pode utilizar o Azure ExpressRoute para expandir redes no local para a nuvem da Microsoft através de uma ligação privada dedicada. Leia a [Descrição geral técnica de ExpressRoute](../expressroute/expressroute-introduction.md) para mais informações sobre esta funcionalidade. Outra opções para cruzada local conectividade entre o seu rede no local e o [Azure é um VPN do site para o site](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md).

### <a name="data-loss-prevention"></a>Prevenção de perda de dados 
Perda de dados (por vezes designado por perdas de dados) é uma consideração importante e a prevenção de perda de dados externos através do iniciados maliciosos e acidentais é fundamental para as organizações muitos.  
 
Tecnologias de (DLP) de prevenção de perda de dados podem ajudar a garantir que soluções como serviços de e-mail não transmitirem dados que foi classificados como confidenciais. As organizações podem tirar partido das funcionalidades DLP nos produtos existentes para o ajudar a evitar a perda de dados. Essas funcionalidades utilizam políticas que podem ser facilmente criadas de raiz ou utilizando um modelo fornecido pelo fornecedor de software.  
 
Tecnologias DLP podem executar a análise abrangente de conteúdo através de correspondências de palavra-chave, correspondências dicionário, avaliação da expressão normal e outros conteúdo exame para detetar o conteúdo que viole organizacionais políticas DLP. Por exemplo, DLP pode ajudar a evitar a perda dos seguintes tipos de dados: 

- Segurança social e números de identificação nacionais 
- Informações bancárias 
- Números de cartão de crédito  
- Endereços IP 

Algumas tecnologias DLP também fornecem a capacidade para substituir a configuração de DLP (por exemplo, se necessitar de uma organização transmitir número de Segurança Social informações a um processador de folha de pagamentos). Além disso, é possível configurar DLP para que os utilizadores são notificados antes de tentam enviar informações confidenciais que não devem ser transmitidas mesmo. 

> [AZURE.TIP] Pode utilizar as funcionalidades do Office 365 DLP para proteger os seus documentos. Leitura [controlos de conformidade do Office 365: prevenção de perda](https://blogs.office.com/2013/10/28/office-365-compliance-controls-data-loss-prevention/) para obter mais informações.

## <a name="see-also"></a>Consulte também

- [Práticas recomendadas do Azure dados encriptação](azure-security-data-encryption-best-practices.md)
- [Controlam o acesso e gestão de identidades Azure melhores práticas de segurança](azure-security-identity-management-best-practices.md)
- [Segurança Azure blogue da equipa](http://blogs.msdn.com/b/azuresecurity/)
- [Centro de resposta de segurança do Microsoft](https://technet.microsoft.com/library/dn440717.aspx)

