<properties
    pageTitle="Descrição geral do portal Microsoft Azure"
    description="Saiba como utilizar o portal do Microsoft Azure."
    services=""
    documentationCenter=""
    authors="davidwrede"
    manager="dwrede"
    editor="jimbe"/>

<tags
    ms.service="na"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="12/16/2015"
    ms.author="dwrede"/>

# <a name="microsoft-azure-portal-overview"></a>Descrição geral do portal Microsoft Azure

O portal do Microsoft Azure é um local central onde pode aprovisionar e gerir os seus recursos Azure.  Neste tutorial vai se familiarizar com o portal e mostrar-lhe como utilizar algumas destas funcionalidades principais:
- **Marketplace abrangente** que permite-lhe navegar pelos milhares de itens a partir da Microsoft e outros fornecedores que podem ser comprados e/ou aprovisionados.
- Uma **experiência de procurar unificado e dimensionáveis** que torna mais fácil encontrar os recursos interessam e executar operações de gestão de várias.
- **Páginas de gestão consistentes** (ou pás) que permitem-lhe gerir variedade do Azure dos serviços através de uma forma consistente de expor definições, ações, faturação informações, dados de monitorização e a utilização do Estado de funcionamento e muito mais.
- Uma **experiência pessoal** , que permite-lhe criar um ecrã de início personalizada que mostra as informações que pretende ver sempre que iniciar sessão.  Também pode personalizar qualquer uma das lâminas de gestão que contêm os mosaicos.

 ![Orientação do Azure IU Portal][UIOrientation]

## <a name="before-you-get-started"></a>Antes de começar

Terá de uma subscrição do Azure válida seguir este tutorial.  Se não tiver um, em seguida, [Inscrever-se para uma avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/) hoje.  Assim que tiver uma subscrição, pode aceder ao portal na [https://portal.azure.com].

## <a name="how-to-create-a-resource"></a>Como criar um recurso

Azure tem um marketplace com milhares de itens que pode criar a partir de um só local.  Digamos que pretende criar uma 2012 VM novo do Windows Server.  O + concentrador novo é o seu ponto de entrada para um conjunto curated das categorias em destaque a partir de mercado.  Cada categoria tem um pequeno conjunto de itens em destaque juntamente com uma ligação de mercado completo que mostra todas as categorias e pesquisa. Para criar esse 2012 VM novo do Windows Server, execute as seguintes ações:  

1.  Destaque Windows Server 2012, para que possa seleccionar a partir da categoria de cluster.  
2.  Preencha alguns entradas do tipo básicas num formulário.
3.  Clique em 'Criar' e a VM começará aprovisionar imediatamente.

O concentrador de notificações serão alertam-no quando o seu recurso foi criado e será aberto um pá gestão (pode sempre procurar a recursos mais tarde).

![Categorias de portais][PortalCategories]


## <a name="how-to-find-your-resources"></a>Como encontrar os recursos

Pode sempre afixar recursos acedidos frequentemente seu startboard, mas poderá ter de procurar para algo que não acedem com frequência.  O concentrador de procurar, apresentado abaixo é a forma de obter a todos os recursos.  Pode filtrar por subscrição, selecione/redimensionar colunas e navegue para os pás gestão ao clicar em itens individuais.

![Navegue até concentrador][BrowseHub]

## <a name="how-to-manage-and-delegate-access-to-a-resource"></a>Como gerir e o acesso delegado para um recurso

Este pá que pode ligar-se para a máquina virtual utilizando o ambiente de trabalho remoto, monitorizar métricas de chave de desempenho, controlar o acesso a esta VM utilizar o access baseado em funções (RBCA), configure a VM e executar outras tarefas de gestão importantes.  Delegação de acesso com base numa função é fundamental para gerir no escala.  Clique [aqui](./active-directory/role-based-access-control-configure.md) para saber mais acerca do mesmo. Para acesso a um recurso de delegado, efetue as seguintes ações:

1.  Navegue para o seu recurso.
2.  Clique em definições todos na secção Essentials.
3.  Clique em 'Utilizadores' na lista definições.
4.  Clique em 'Adicionar' na barra de comandos.
5.  Selecione um utilizador e uma função.

![Gerir um recurso][ManageResource]

## <a name="how-to-customize-a-resource-blade"></a>Como personalizar uma pá de recursos

Azure configure previamente pás para os recursos, mas os mosaicos nestes pás são seu para o controlo.  Pode aceder facilmente para personalizar o modo para adicionar, remover, redimensionar ou voltar a dispor os mosaicos das imagens. Para personalizar uma pá, execute as seguintes ações:

1.  Navegue para o seu recurso.
2.  Clique na '...' no topo da pá que pretende personalizar.
3.  Clique em 'Adicionar peças'.
4.  Começar a arrastar e largar no partes.  

![Personalizar pás][CustomizeBlades]

## <a name="how-to-get-help"></a>Como obter ajuda

Se nunca tiver um problema, estamos aqui para si.  O portal tem uma página de ajuda e suporte que pode ajudá-lo a direção correta.  Dependendo do seu [plano de suporte](https://azure.microsoft.com/support/plans/), também pode criar pedidos de suporte diretamente no portal.  Depois de criar um bilhetes de suporte, pode gerir o ciclo de vida da permissão a partir do portal. Pode obter a ajuda e suporte página ao navegar para procurar -> ajuda + suporte.  

![Ajuda e suporte][HelpSupport]

## <a name="summary"></a>Resumo

Analisemos o que aprendeu neste tutorial:
- Que aprendeu como inscrever-se, obtenha uma subscrição e navegue para o portal
- Tem orientado com o portal de IU e aprendeu como criar e navegar recursos
- O que aprendeu criar um recurso e procurar recursos
- Aprendeu sobre as lâminas de estrutura ou gestão e como pode gerir forma consistente diferentes tipos de recursos
- Que aprendeu como personalizar o portal para trazer as informações que lhe interessam para a frente e centro
- O que aprendeu controlar o acesso aos recursos de utilizar o access baseado em funções RBCA)
- O que aprendeu obter ajuda e suporte

Portal do Microsoft Azure simplifica radicalmente criar e gerir as suas aplicações na nuvem.  Veja o [blogue de gestão](https://azure.microsoft.com/blog/topics/management/) para manter atualizado como estamos constantemente [a ouvir comentários](https://feedback.azure.com/forums/223579-azure-preview-portal/) e efetuar melhorias.  [Blogue do ScottGu](http://weblogs.asp.net/scottgu) é outra ótimo local para procurar todas as atualizações do Azure.

[UIOrientation]: ./media/azure-portal-how-to-use/azure_portal_1.png
[PortalCategories]: ./media/azure-portal-how-to-use/azure_portal_2.png
[BrowseHub]: ./media/azure-portal-how-to-use/azure_portal_3.png
[ManageResource]: ./media/azure-portal-how-to-use/azure_portal_4.png
[CustomizeBlades]: ./media/azure-portal-how-to-use/azure_portal_5.png
[HelpSupport]: ./media/azure-portal-how-to-use/azure_portal_6.png
