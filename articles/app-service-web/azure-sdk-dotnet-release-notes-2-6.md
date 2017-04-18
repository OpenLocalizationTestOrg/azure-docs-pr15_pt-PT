<properties 
   pageTitle="Notas de lançamento do Azure SDK para .NET 2.6" 
   description="Notas de lançamento do Azure SDK para .NET 2.6" 
   services="app-service/web" 
   documentationCenter=".net" 
   authors="Juliako" 
   manager="erikre" 
   editor=""/>

<tags
   ms.service="app-service"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration" 
   ms.date="10/17/2016"
   ms.author="juliako"/>

 
# <a name="azure-sdk-for-net-26-release-notes"></a>Notas de lançamento do Azure SDK para .NET 2.6

Este documento contém as notas de lançamento para o SDK do Azure para .NET 2.6 lançamento. 

Com o Azure SDK 2.6 podem desenvolver aplicações de serviço de nuvem (PaaS) filtragem .NET 4.5.2 ou .NET 4.6 desde que instalar manualmente o destino .NET Framework sobre o papel de serviço de nuvem. Consulte o artigo [instalar o .NET numa função de serviço na nuvem](http://go.microsoft.com/fwlink/?LinkID=309796).


##<a name="service-bus-updates"></a>Atualizações de serviço Bus

- Concentradores evento: 

    - Agora permite que o controlo de acesso ao enviar eventos por expor o ponto final do publisher adicionais para concentradores do evento.
    - Estabilidade adicional e da melhoria adicionada a funcionalidade de evento concentradores.
    - A adição de suporte do protocolo Amqp sobre WebSocket de mensagens e concentradores do evento.

##<a name="hdinsight-tools-for-visual-studio-updates"></a>Ferramentas de HDInsight para Visual Studio atualizações

- **Melhoramento IntelliSense**: sugestões de metadados remoto

    Ferramentas de HDInsight para Visual Studio suporta agora ao obter metadados remoto quando edita o script ramo. Por exemplo, pode escrever * *SELECIONE* FROM** e todos os nomes de tabela serão apresentados. Além disso, os nomes das colunas serão apresentados depois de especificar uma tabela.

- **Suporte de emulador HDInsight**

    Agora HDInsight ferramentas para o Visual Studio suporta a ligação ao emulador HDInsight, para que poderia desenvolver scripts do ramo localmente sem a introdução de qualquer custo, em seguida, executar esses scripts contra os clusters HDInsight. 

    Para mais informações, consulte [Este manual](http://go.microsoft.com/fwlink/?LinkID=529540&clcid=0x409).

- **Ferramentas de HDInsight para Visual Studio suporte para clusters de Hadoop genéricos** (Pré-visualização)

    Ferramentas de HDInsight para Visual Studio suportam agora genéricos Hadoop clusters, para que possa utilizar ferramentas de HDInsight para Visual Studio para fazer o seguinte:

    - ligar ao seu cluster 
    - escrever ramo de consulta com suporte automática/IntelliSense-conclusão melhorado, 
    - ver todas as tarefas no seu cluster com uma interface de utilizador intuitiva. 

    Para mais informações, consulte [Este manual](http://go.microsoft.com/fwlink/?LinkID=529540&clcid=0x409).

##<a name="in-role-cache-updates"></a>Atualizações de Cache na função

- **Na função Cache** foi atualizado para utilizar o **Microsoft Azure armazenamento SDK** versão 4.3. Até ao momento, a **Cache na função** estava a utilizar Azure armazenamento SDK versão 1.7.

    Clientes utilizando o Azure SDK 2,5 ou abaixo deve atualizar para o Azure SDK 2.6 e deslocar-se para a nova versão do Azure armazenamento SDK. 

    Neste momento o armazenamento do Windows Azure versão 2011-08-18 está agendado para ser removido de 1 de Agosto de 2016. Qualquer migrações de Cache na função a partir do Azure SDK 2,5 ou abaixo para 2.6 tem de ser concluídas por este período de tempo. Para mais informações sobre a reforma do armazenamento do Windows Azure versão 2011-08-18, consulte o artigo [serviço de armazenamento do Microsoft Azure versão remoção Update: extensão para 2016](http://blogs.msdn.com/b/windowsazurestorage/archive/2015/10/19/microsoft-azure-storage-service-version-removal-update-extension-to-2016.aspx).

>[AZURE.IMPORTANT]Estamos a está a anunciar a extinção de 30 de Novembro de 2016, para o serviço de Cache gerido Azure e Azure na função Cache. Recomendamos que migrar para o Azure Redis Cache de preparação para a este reforma. Para mais informações sobre datas e orientações de migração, consulte o artigo [oferta de Cache de Azure que é ideal para mim?](../redis-cache/cache-faq.md#which-azure-cache-offering-is-right-for-me)

##<a name="azure-app-service-tools"></a>Ferramentas do Azure de aplicação de serviço

Os itens seguintes foram atualizados na versão Azure SDK 2.6.

- Publicação Azure avançada para incluir Azure API aplicações como um alvo de implementação.
- API aplicações aprovisionamento funcionalidade para permitir que os utilizadores com a funcionalidade de criação e de aprovisionamento de API aplicação.
- Servidor Explorer alterado para refletir o nó da aplicação de serviço de novo, com as aplicações Web, Mobile e API agrupadas por grupo de recursos.
- Adicione gesto Azure API aplicação cliente adicionado a maioria dos c# projetos que permitirão geração automática de aplicações API com capacidade de Swagger a ser executada em subscrição Azure de um utilizador.
- Ferramentas de aplicações API e nós de aplicação de serviço no Explorador do servidor só estão disponíveis no Visual Studio 2013. 

##<a name="azure-resource-manager-tools-updates"></a>Atualizações de ferramentas do Gestor de recursos do Azure

As ferramentas de Gestor de recursos Azure tenham sido actualizadas para incluir modelos para máquinas virtuais, redes e armazenamento. Foi atualizado para incluir uma nova vista de destaques para modelos e a capacidade de editar modelos utilizando fragmentos JSON JSON experiência de edição. Modelos de implementado a partir do Visual Studio utilize um script PowerShell fornecido com o project, para que as alterações efetuadas para o script serão utilizadas pelo Visual Studio.

##<a name="diagnostics-improvements-for-cloud-services"></a>Melhoramentos de diagnóstico para serviços em nuvem

Azure SDK 2.6 novamente traz suporte para recolher registos de diagnóstico no emulador cluster Azure e transferi-las ao armazenamento de desenvolvimento. Os registos de qualquer diagnósticos (incluindo rastreio da aplicação registos, rastreio para os registos do Windows (ETW), contadores de desempenho, infraestrutura registos de eventos do windows e registos de eventos) geradas quando a aplicação estiver em execução no emulador pode ser transferido para o armazenamento de desenvolvimento para verificar se o registo de diagnóstico está a funcionar no seu computador local. 

Agora pode ser especificada a conta de armazenamento de diagnóstico no ficheiro de configuração (.cscfg) de serviço, tornando mais fácil utilizar contas de armazenamento de diagnóstico diferentes para diferentes ambientes. Existem algumas diferenças entre como a cadeia de ligação trabalhou no Azure SDK 2.4 e Azure SDK 2.6. Para obter mais informações sobre como utilizar a ligação de armazenamento de diagnóstico cadeia e como o seu impacto seus projetos consulte o artigo [Configurar diagnósticos para serviços em nuvem Azure](http://go.microsoft.com/fwlink/?LinkID=532784).

##<a name="breaking-changes"></a>Força de alterações

###<a name="azure-resource-manager-tools"></a>Ferramentas de Gestor de recursos do Azure 

- O tipo de projecto de **Projectos de implementação de nuvem** disponível na 2,5 SDK Azure foi mudado para o **Grupo de recursos do Azure**.
- Tipo de **Projectos de implementação de nuvem** de projectos criados na 2,5 SDK Azure pode ser utilizado na 2.6 mas implementar o modelo a partir do Visual Studio irá falhar. No entanto, implementar com o script do PowerShell continuará a funcionar como estava anteriormente.  Para obter informações sobre como utilizar **Projectos de implementação da nuvem** no 2.6 leia esta [mensagem](http://go.microsoft.com/fwlink/?LinkID=534086).
 
##<a name="known-issues"></a>Problemas conhecidos

- Recolher registos de diagnóstico no emulador requer um sistema operativo de 64 bits. Registos de diagnóstico não serão recolhidos quando a ser executado num sistema operativo de 32 bits. Isto não afeta outras funcionalidades de emulador. 

- Azure SDK 2.6 disponibilizado em 29/4/2015 teve duas questões: 

    - Não foi possível carregar a aplicação universal no Visual Studio 2015 quando Azure SDK 2.6 foi instalado no computador.
    - Depuração de um projeto de serviço em nuvem seria falhar no Visual Studio 2013 e no Visual Studio 2015, onde o Visual Studio deixa de responder e falha enquanto está a apresentar uma caixa de diálogo com a mensagem "Configurar diagnósticos para emulador".
    
    Uma atualização para Azure SDK 2.6 foi publicada em 18/5/2015. A versão actualizada está 2.6.30508.1601; contém correções para problemas de dois descritos acima. É possível identificar a compilação do SDK a partir do painel de controlo -> programas e funcionalidades -> Ferramentas do Microsoft Azure do Microsoft Visual Studio 2013 – v 2.6. A coluna de versão irá apresentar o número de compilação.

    Se ainda estão opostas os problemas acima, instale a versão mais recente do Azure 2.6 SDK para [VS 2012](http://go.microsoft.com/fwlink/p/?linkid=323511&clcid=0x409), [VS 2013](http://go.microsoft.com/fwlink/p/?linkid=323510&clcid=0x409) ou [VS 2015](http://go.microsoft.com/fwlink/?linkid=518003&clcid=0x409).
 
##<a name="see-also"></a>Consulte também

[Suporte e informações de reforma para o SDK do Azure para APIs e .NET](https://msdn.microsoft.com/library/azure/dn479282.aspx/)
