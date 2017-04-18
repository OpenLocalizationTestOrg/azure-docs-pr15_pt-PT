<properties
    pageTitle="Padrão de aplicação Web do inquilino com várias | Microsoft Azure"
    description="Localize descrições gerais de arquitecturais e padrões de estrutura que descrevem como implementar uma aplicação web do inquilino com várias no Azure."
    services=""
    documentationCenter=".net"
    authors="wadepickett" 
    manager="wpickett"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="06/05/2015"
    ms.author="wpickett"/>

# <a name="multitenant-applications-in-azure"></a>Aplicações multi-inquilino no Azure

Uma aplicação multi-inquilino é um recurso partilhado que permite a utilizadores em separado, ou "inquilinos," ver a aplicação, como se fosse a sua própria. Cenário típico que é utilizado pela para uma aplicação multi-inquilino é um em que todos os utilizadores da aplicação podem optar por personalizar a experiência do utilizador mas caso contrário, têm os mesmos requisitos de empresas básica. Exemplos de aplicações multi-inquilino grandes são Office 365, o Outlook.com e o visualstudio.com.

Da perspetiva de um fornecedor de aplicação, os benefícios da multitenancy principalmente referem-se para a eficiência operacional e custo. Uma versão da sua aplicação pode satisfazer as necessidades de muitos inquilinos/clientes, permitindo-consolidação do sistema de tarefas de administração, como monitorização, otimização do desempenho, manutenção de software e cópias de segurança de dados.

A seguinte fornece uma lista de requisitos do perspetiva de um fornecedor e objetivos mais significativo.

- **Aprovisionar**: tem de conseguir aprovisionamento de inquilinos novos para a aplicação.  Para obter aplicações multi-inquilino com um grande número de inquilinos, é normalmente necessário automatizar este processo ao ativar reposição personalizada de aprovisionamento.
- **Manutenção**: tem de conseguir atualizar a aplicação e executar outras tarefas de manutenção enquanto estão a utilizar múltiplos inquilinos do.
- **Monitorização**: tem de conseguir monitorizar a aplicação em qualquer momento para identificar problemas de e para resolvê-los. Isto inclui como cada inquilino está a utilizar a aplicação de monitorização.

Uma aplicação multi-inquilino corretamente implementada fornece as seguintes vantagens para os utilizadores.

- **Isolamento**: as atividades de inquilinos individuais que afetam a utilização da aplicação por outros inquilinos. Inquilinos não consegue aceder uns dos outros dados. Parecerá para o inquilino que tenham exclusivo utilização da aplicação.
- **Disponibilidade**: inquilinos individuais pretende a aplicação esteja constantemente disponível, talvez com garantias definidas num SLA. Novamente, as atividades de outras inquilinos devem não vai afetar a disponibilidade da aplicação.
- **Escalabilidade**: balanças a aplicação para corresponder à procura de inquilinos individuais. O estado de presença e ações de outros inquilinos não devem vai afetar o desempenho da aplicação.
- **Custos**: custos são inferiores a executar uma aplicação dedicada, único inquilino, uma vez multitenancy permite a partilha de recursos.
- **Personalização**. A capacidade de personalizar a aplicação para um inquilino individual de várias formas como adicionar ou remover funcionalidades, alterar cores e logótipos ou até mesmo adicionar os seus próprios código ou script.

Resumindo, enquanto existem muitos que deve ter em conta para fornecer um serviço altamente dimensionável, existem também um número de requisitos do que são comuns a muitas aplicações multi-inquilino e aos objetivos. Alguns podem não ser relevantes em cenários específicos e a importância da objetivos individuais e requisitos serão diferentes em cada cenário. Como um fornecedor da aplicação multi-inquilino, também terá objetivos e requisitos, como, dos inquilinos objetivos e requisitos, rentabilidade, de faturação, vários níveis de serviço, aprovisionamento, manutenção de monitorização e automatização da reunião.

Para mais informações sobre considerações de estrutura adicionais de uma aplicação de multi-inquilino, consulte o artigo [alojamento uma aplicação do inquilino com várias no Azure][]. Para obter informações sobre comuns padrões de arquitectura de dados das aplicações de base de dados do inquilinos com várias software-como-a-service (SaaS), consulte o artigo [Padrões de Design para inquilino com várias SaaS aplicações com base de dados do SQL Azure](./sql-database/sql-database-design-patterns-multi-tenancy-saas-applications.md). 

Azure disponibiliza várias funcionalidades que permitem-lhe resolver os problemas de chaves encontrados ao estruturar um sistema multi-inquilino.

**Isolamento**

- Web site de segmento inquilinos por cabeçalhos de anfitrião, com ou sem comunicação de SSL
- Segmento inquilinos do Web site por parâmetros de consulta
- Serviços Web nas funções de trabalho
    - Funções de trabalho. que normalmente processar dados back-end de uma aplicação.
    - Funções Web que normalmente agir frontend para aplicações.

**Armazenamento**

Gestão de dados como base de dados do SQL Azure ou armazenamento do Windows Azure serviços como o serviço de tabela que fornece serviços de armazenamento de grandes quantidades de dados não estruturados e o serviço de BLOBs que fornece serviços para armazenar grandes quantidades de texto não estruturado ou dados binários como imagens, áudio e vídeo.

- Proteger dados Multitenant adequada da base de dados do SQL inícios de sessão do SQL Server por inquilino.
- Utilizar tabelas Azure para a aplicação de recursos ao especificar uma política de acesso ao nível do contentor, pode a capacidade de ajustar as permissões sem ter de emitir novo URL para os recursos protegidos com assinaturas acesso partilhado.
- Azure filas para filas de aplicação recursos Azure são normalmente utilizadas para processamento de unidade em nome de inquilinos, mas também podem ser utilizadas para distribuir necessário para o aprovisionamento ou de gestão de trabalho.
- Serviço Bus filas para recursos de aplicações que emite trabalhar uma partilhada para um serviço, pode utilizar uma única fila onde cada remetente inquilino só tiver as permissões (tal como derivado de afirmações emitidas a partir de ACS) para transmitir para essa fila, enquanto só destinatários de serviço de ter permissão para recuperar os dados provenientes do múltiplos inquilinos do fila de espera.


**Ligação e serviços de segurança**

- Azure Service Bus, uma infraestrutura mensagens que encontra-se entre as aplicações permitindo-lhes para mensagens de uma forma agregamento para escala melhorada do exchange e RDP.

**Serviços de funcionamento**

Azure fornece várias serviços de rede que suportem a autenticação e melhorar a capacidade de gestão das suas aplicações alojadas. Estes serviços incluem o seguinte:

- Azure rede Virtual permite-lhe que Aprovisiona e gerir redes privadas virtuais (VPNs) no Azure, bem como segura ligá-las no local infraestrutura de TI.
- Gestor de tráfego de rede virtual permite-lhe carregar o tráfego de entrada amortização em vários serviços Azure alojados se que estiver a executar o Centro de dados do mesmo ou através de diferentes centros de dados em todo o mundo.
- Azure Active Directory (Azure AD) é um serviço moderno, com base no resto que fornece capacidades de controlo de acesso e gestão de identidade para as suas aplicações na nuvem. Utilizar o Azure AD para recursos de aplicações Azure AD para fornece uma forma fácil de autenticação e autorização utilizadores para obter acesso ao seu aplicações e serviços web permitindo as funcionalidades de autenticação e autorização para ser estimadas fora do seu código são aplicadas ao mesmo tempo.
- Azure Service Bus fornece um mensagens seguro e capacidade de fluxo de dados para distribuído e aplicações híbridas, tal como a comunicação entre o Azure alojado aplicações e aplicações no local e serviços, sem necessidade de complexas infra-estruturas firewall e segurança. Utilizar o serviço Bus reencaminhamento para recursos de aplicações para os serviços que são expostos como os pontos finais pode pertencer ao inquilino (por exemplo, alojado fora do sistema, tal como no local) ou podem ser serviços aprovisionados especificamente para o inquilino (uma vez que dados confidenciais, específicas do inquilino percorre-los).



**Recursos de aprovisionamento**

Azure fornece várias maneiras inquilinos do novo aprovisionar para a aplicação. Para obter aplicações multi-inquilino com um grande número de inquilinos, é normalmente necessário automatizar este processo ao ativar reposição personalizada de aprovisionamento.

- Funções de trabalho permitem-lhe a disposição e a disposição por inquilino recursos (como quando um novo inquilino sinais de segurança ou cancela), recolher métricas de medição utilizar e gerir a escala de seguir uma agenda de determinadas ou em resposta a passagem dos limiares de indicadores chave de desempenho. Esta função mesmo também pode ser utilizada para transmitir saída atualizações e atualiza os para a solução.
- Podem ser utilizadas para aprovisionar cluster ou previamente inicializados Blobs do Azure recursos de armazenamento para novos inquilinos ao fornecer as políticas de acesso ao nível do contentor proteger a cluster pacotes, imagens VHD e outros recursos de serviço.
- Opções para recursos de base de dados SQL de aprovisionamento para um inquilino incluem:

    -   DDL em scripts ou incorporado como recursos dentro de fluidos
    -   SQL Server 2008 R2 CAD pacotes implementado através de programação.
    -   Copiar a partir de uma base de dados do modelo global de referência
    -   Utilizar a base de dados importar e exportar para aprovisionar novas bases de dados a partir de um ficheiro.



<!--links-->

[Alojar uma aplicação do inquilino com várias no Azure]: http://msdn.microsoft.com/library/hh534480.aspx
[Designing Multitenant Applications on Azure]: http://msdn.microsoft.com/library/windowsazure/hh689716
