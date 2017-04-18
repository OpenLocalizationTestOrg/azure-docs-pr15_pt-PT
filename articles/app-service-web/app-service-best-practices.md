<properties
    pageTitle="Práticas recomendadas para Azure de aplicação de serviço"
    description="Obter informações sobre procedimentos recomendados e resolução de problemas para a aplicação de serviço de Azure."
    services="app-service"
    documentationCenter=""
    authors="dariagrigoriu"
    manager="wpickett"
    editor="mollybos"/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="06/30/2016"
    ms.author="dariagrigoriu"/>
    
# <a name="best-practices-for-azure-app-service"></a>Práticas recomendadas para Azure de aplicação de serviço

Este artigo resume as práticas recomendadas para utilizar a [Aplicação de serviço de Azure](http://go.microsoft.com/fwlink/?LinkId=529714). 

## <a name="colocation"></a>Colocação
Quando os recursos Azure compor uma solução como uma aplicação web e uma base de dados estão localizados nos diferentes regiões os efeitos podem incluir o seguinte:

*  Latência maior na comunicação entre recursos
*  Encargos monetários para os dados de saída transferir região cruzada conforme indicado na [página comparar Azure](https://azure.microsoft.com/pricing/details/data-transfers).

Colocação na mesma região é melhor para os recursos Azure compor uma solução como uma aplicação web e uma conta de base de dados ou armazenamento utilizada para colocar em espera conteúdo ou dados. Quando criar os recursos que deve certificar-se estiverem na mesma região Azure a menos que tenha específicas da empresa ou razão para que não sejam de estrutura. Pode mover uma aplicação de serviço de aplicação à mesma região como a base de dados ao tirar partido da [aplicação de serviço clonar funcionalidade](app-service-web-app-cloning-portal.md) atualmente disponíveis para aplicações do plano de serviço de aplicação do Premium.   

## <a name="memoryresources"></a>Quando aplicações consumam mais memória que o esperado
Quando que existam uma aplicação consome mais memória que o esperado como indicado através da monitorização ou recomendações de serviço considere a [funcionalidade de reparação automática de serviço de aplicação](https://azure.microsoft.com/blog/auto-healing-windows-azure-web-sites). Uma das opções para a funcionalidade de reparação automática está a demorar ações personalizadas com base em limiar de memória. Ações de calibração o espectro de notificações de correio eletrónico para inquérito através do Estado da memória para mitigação no local ao Reciclagem o processo de trabalho. A reparação automática pode ser configurada via Web. config e uma interface de utilizador amigável conforme descrito nesta mensagem no blogue para a [Extensão de Site de suporte de serviço de aplicação](https://azure.microsoft.com/blog/additional-updates-to-support-site-extension-for-azure-app-service-web-apps).   

## <a name="CPUresources"></a>Quando aplicações consumam CPU mais que o esperado
Quando Repare que uma aplicação consome CPU mais do que esperado ou experiências repetida CPU picos como indicado através da monitorização ou recomendações de serviço considere escalar para cima ou escalar para fora o plano de serviço de aplicação. Se a aplicação estiver statefull, escalar para cima é a única opção, enquanto se a aplicação estiver a saída sem estado, dimensionamento irá dar-lhe mais flexibilidade e superior potencial de escala. 

Para obter mais informações sobre aplicações "sem estado" que "statefull" vs pode ver este vídeo: [Planear uma aplicação de várias camadas dimensionáveis ponto a ponto no Microsoft Azure Web App](https://channel9.msdn.com/Events/TechEd/NorthAmerica/2014/DEV-B414#fbid=?hashlink=fbid). Para obter mais informações sobre as opções de dimensionamento e autoscaling da aplicação de serviço de leitura: [escala de uma aplicação Web na aplicação de serviço de Azure](web-sites-scale.md).  

## <a name="socketresources"></a>Quando os recursos de socket são esgotados
Um motivo comum esgotar TCP para ligações de saída é a utilização das bibliotecas do cliente que não são executadas para reutilizar ligações TCP ou, no caso de um protocolo de nível superior, tal como HTTP - persistentes não a ser utilizadas. Reveja a documentação para cada uma das bibliotecas referenciadas por aplicações no seu plano de serviço de aplicação para se certificar de que estão configuradas ou pode ser consultadas no seu código para reutilização eficiente de ligações de saída. Também, siga as orientações da documentação de biblioteca para inicial maiúscula criação e lançamento ou limpeza para evitar fugas ligações. Os inquéritos de bibliotecas do cliente estiver no impacto de progresso pode ser atenuado por escalar para fora para várias instâncias.  

## <a name="appbackup"></a>Quando a aplicação fazer cópia de segurança é iniciada a falhar
As duas mais comuns razões pelas quais falha de cópia de segurança de aplicação: definições de armazenamento inválido e configuração de base de dados inválidos. Estas falhas normalmente ocorrem quando existem alterações para recursos de armazenamento ou base de dados ou alterações para saber como aceder a estes recursos (por exemplo, credenciais atualizadas para a base de dados selecionada nas definições de cópia de segurança). Cópias de segurança normalmente executar numa agenda e necessitam de acesso ao armazenamento (para exportar uma cópia de segurança dos ficheiros) e as bases de dados (para copiar e conteúdos de ser incluídas na cópia de segurança de leitura). O resultado da falhar aceder a um destes recursos seria consistente falha de cópia de segurança. 

Quando ocorrem falhas de cópia de segurança, reveja os resultados mais recentes para compreender qual o tipo de falha se passa. No caso de falhas de acesso de armazenamento, reveja e atualizar as definições de armazenamento utilizadas na configuração de cópia de segurança. No caso de falhas de acesso de base de dados, reveja e atualizar o seu cadeias de ligações como parte das definições de aplicação; em seguida, avance para atualizar a sua configuração cópia de segurança para incluir corretamente as bases de dados necessários. Para mais informações sobre a cópia de segurança da aplicação, consulte a documentação de [cópia de uma aplicação web na aplicação de serviço de Azure](web-sites-backup.md) .

## <a name="nodejs"></a>Quando novas aplicações Node.js são implementadas ao serviço de aplicação do Azure
Configuração de predefinida do serviço de aplicação Azure para Node.js aplicações destina-se melhor conforme as suas necessidades de aplicações mais comuns. Se configuração para a sua aplicação Node.js seria beneficiar da otimização personalizada para melhorar o desempenho ou otimizar a utilização de recursos para recursos de memória/CPU/rede, pode rever os nossos melhores práticas e os passos de resolução de problemas. Este artigo de documentação descreve as definições de iisnode, poderá ter de configurar para a sua aplicação Node.js descreve os vários cenários ou problemas que a sua aplicação poderá ser opostas e mostra como resolver estes problemas: [melhores práticas e guia de resolução de problemas do nó as aplicações no Azure aplicação de serviço](app-service-web-nodejs-best-practices-and-troubleshoot-guide.md).   


