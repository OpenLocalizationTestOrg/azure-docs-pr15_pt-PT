<properties
    pageTitle="Quais são as novidades no Azure Toolkit para Eclipse"
    description="Saiba mais sobre as funcionalidades mais recentes no Azure Toolkit para Eclipse."
    services=""
    documentationCenter="java"
    authors="rmcmurray"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="multiple"
    ms.workload="na"
    ms.tgt_pltfrm="multiple"
    ms.devlang="Java"
    ms.topic="article"
    ms.date="08/26/2016" 
    ms.author="robmcm;asirveda;martinsawicki"/>

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/hh694270.aspx -->

# <a name="whats-new-in-the-azure-toolkit-for-eclipse"></a>Quais são as novidades no Azure Toolkit para Eclipse

## <a name="azure-toolkit-for-eclipse-releases"></a>Toolkit Azure para Eclipse versões

Este artigo contém informações sobre os vários lançamentos e atualizações mais recentes para o Toolkit de Azure para Eclipse.

> [AZURE.NOTE] Também existe uma Toolkit Azure para IntelliJ IDE. Para mais informações, consulte o artigo [Toolkit de Azure para IntelliJ].

### <a name="august-26-2016"></a>26 de Agosto de 2016

O Toolkit de Azure para Eclipse - lançamento de Agosto de 2016 inclui as seguintes melhorias:

* **Distribuições JDK personalizado**. O Toolkit de Azure para Eclipse suporta agora especificando e implementar uma versão JDK arbitrária ao seu Web App do Azure contentor:
  - Para além de JDKs fornecidas pela Azure, também pode escolher a partir de uma vasta seleção de versões Zulu OpenJDK disponibilizados no Azure pelos sistemas blue.
  - Também pode especificar o seu próprio distribuição JDK se carregar como um ficheiro ZIP à sua conta de armazenamento.
* **Melhoramentos para a vista do Explorador de Azure**:
  - Suporte para a gestão de Máquina Virtual com o novo modelo de Gestor de recursos do Azure: da lista, criar e eliminar recursos com base no gestor virtual máquinas sem sair do IDE.
  - Suporte para gestão de contas de armazenamento de BLOBs através do Gestor de recursos do Azure, que completa a funcionalidade existente para gerir contas de armazenamento "clássico".
* **Controlador de JDBC da Microsoft 6.0 para SQL Server**. Esta atualização inclui o controlador JDBC mais recente para o Microsoft SQL Server (v 6.0), que é agora incluída como uma biblioteca que pode adicionar facilmente a sua projetos Java, portanto substituir a versão mais antiga.

### <a name="june-29-2016"></a>29 de Junho de 2016

O Toolkit de Azure para Eclipse - lançamento de Junho de 2016 inclui as seguintes melhorias:

* **Requisito Java 8**. O Toolkit de Azure para Eclipse requer agora Java 8, embora este requisito encontra-se apenas para o toolkit - as aplicações podem continuar a utilizar todas as versões do Java que são suportadas pelo Azure.
* **Suporte para o mais recente JDKs Java**. As versões mais recentes da JDKs Java agora são suportadas pelo Toolkit de Azure para Eclipse.
* **Suporte para v2.9.1 Azure SDK**. A versão mais recente do Azure SDK está agora o mínimo pré necessários para o Toolkit de Azure para Eclipse.
* **Amostras integradas**. Agora o Toolkit de Azure para Eclipse funcionalidades de várias aplicações de exemplo para ajudar os programadores começar.
* **Integração de ferramenta HDInsight**. HDInsight ferramentas do Azure estão agrupadas agora com o Toolkit de Azure para Eclipse. Para mais informações, consulte o artigo [HDInsight Plug-in ferramentas para Eclipse].
* **Depuração do Java Web Apps remota**. O Toolkit de Azure para Eclipse suporta agora depuração remota de Java web apps num serviço de aplicação do Azure.
* **Suporte para a versão de Eclipse Luna.** A nova versão Eclipse IDE mínima exigida é Luna.

### <a name="april-12-2016"></a>12 de Abril de 2016

O Toolkit de Azure para Eclipse - lançamento de Abril de 2016 inclui as seguintes melhorias:

* **Suporte para v2.9.0 Azure SDK**. A versão mais recente do Azure SDK está agora o mínimo pré necessários para o Toolkit de Azure para Eclipse.
* **Diversas da eficiência de utilização, melhorias de desempenho e capacidade de resposta relacionados com o suporte do Azure Web App**. Um número de otimizações de desempenho no modo como o Toolkit comunica com o resultado Azure numa mais da IU.
* **Capacidade para eliminar um contentor de aplicação Web existente no Azure a partir de dentro Eclipse**. O Toolkit de Azure para Eclipse agora permite-lhe eliminar um contentor Azure Web existente sem sair do Eclipse.

### <a name="march-7-2016"></a>7 de Março de 2016

O Toolkit de Azure para Eclipse - lançamento de Março de 2016 inclui as seguintes melhorias:

* **Suporte para implementação rápida de aplicações Java lightweight**. O Toolkit de Azure para Eclipse suporta agora a rápida implementação de lightweight Java aplicações para o Azure Web App contentores, por isso implementar aplicações Java agora demora segundos em vez dos minutos.
* **Suporte para a gestão de aplicação Web utilizando a vista do Explorador de Azure**. A vista do Explorador de Azure no toolkit suporta agora para listar, iniciar e parar Azure Web Apps.
* **As distribuições Tomcat atualizados, cais e Zulu OpenJDK**. O Toolkit de Azure para Eclipse fornece suporte para versões actualizadas de Tomcat, cais e Zulu OpenJDK para implementações Java para serviços em nuvem Azure.

### <a name="january-4-2016"></a>4 de Janeiro de 2016

O Toolkit de Azure para Eclipse - lançamento de Janeiro de 2016 inclui as seguintes melhorias:

* **Suporte para as atualizações de Zulu OpenJDK**. Para mais informações, consulte a [página web de blue sistemas para o OpenJDK Zulu].
* **Tomcat atualizados e distribuições cais**. As distribuições cais e Tomcat que estão disponíveis no Microsoft Azure para utilização com o Toolkit de Azure para Eclipse tem sido atualizadas.
* **Funcionalidade paridade entre Eclipse e IntelliJ conjuntos de ferramentas para Azure**. O Toolkit de Azure para Eclipse e o [Toolkit de Azure para IntelliJ] passará a suportar o mesmo conjunto de funcionalidades.

### <a name="september-1-2015"></a>1 de Setembro de 2015

O Toolkit de Azure para Eclipse - lançamento de Setembro de 2015 inclui os seguintes melhoramentos:

* **Suporte para as atualizações de Zulu OpenJDK**. Para mais informações, consulte a [página web de blue sistemas para o OpenJDK Zulu].
* **Tomcat atualizados e distribuições cais**. As distribuições cais e Tomcat que estão disponíveis no Microsoft Azure para utilização com o Toolkit de Azure para Eclipse tem sido atualizadas. (Estes distribuições permite que os programadores criar desenvolvimento rápido e testar a projetos com o Toolkit de Azure para Eclipse.
* **Suporte para referências Tomcat e cais automaticamente atualizadas**. Para além das versões específicas do Tomcat e cais que estão disponíveis no Azure, os programadores podem agora fazer referência a uma distribuição designada a "mais recente (actualizado automática)", que irá atualizar automaticamente para a distribuição mais recente de cada versão principal de cais ou Tomcat da próxima vez que as instâncias de função são reciclados. (Ocorre a Reciclagem automaticamente, mas os programadores manualmente podem acionar uma reciclagem através do portal do Azure.) Esta nova funcionalidade significa que os programadores não possui a implementar a sua aplicação possam-se de que tem o seu software de servidor atualizado. (
*  Esta funcionalidade atualmente destina-se apenas para fins de desenvolvimento e teste ou aplicações não críticas e não é recomendada para produção.)
* **Vista do Explorador de azure para blobs, filas e tabelas no Azure armazenamento**. Isto permite que os programadores executar um conjunto de tarefas comuns com os seus artefactos de armazenamento diretamente a partir do Eclipse IDE. Por exemplo: eliminar, carregamento ou transferência blobs.

### <a name="august-1-2015"></a>1 de Agosto de 2015

O Toolkit de Azure para Eclipse - lançamento de Agosto de 2015 inclui os seguintes melhoramentos:

* **Aplicação informações instrumentação Key Management**. Esta atualização permite-lhe adquirir, criar e gerir as suas chaves de instrumentação aplicação informações diretamente a partir do Eclipse IDE.
* **Controlador de JDBC da Microsoft 4.1 para SQL Server**. Esta atualização inclui suporte para o controlador JDBC mais recente para o Microsoft SQL Server.
* **Versão 2.7 do Azure SDK**. Esta atualização mais recente para o SDK do Azure é o novo pré necessários para o Toolkit quando instalado no Windows. (Nota que este não é necessária em sistemas operativos não Windows).
* **Suporte para o v7 Zulu OpenJDK atualizar**. Para mais informações, consulte a [página web de blue sistemas para o OpenJDK Zulu].

### <a name="may-1-2015"></a>1 de Maio de 2015

O Toolkit de Azure para Eclipse - lançamento de Maio de 2015 inclui os seguintes melhoramentos:

* **Melhorada IU de selecção de servidor**. Nesta versão simplifica a utilização do toolkit em sistemas operativos não Windows.
* **Suporte para Maven projetos**. Nesta versão suporta Maven projetos como aplicações, as quais pode implementar o toolkit para Azure e configurar a aplicação de informações.
* **Versão 2.6 do Azure SDK**. Esta atualização mais recente para o SDK do Azure é o novo pré necessários para o Toolkit quando instalado no Windows. (Nota que este não é necessária em sistemas operativos não Windows).
* A **atualização de implementação em vez de voltar a publicar**. Se o é voltar a publicar um projeto de implementação quando a versão anterior já está live, o toolkit utiliza agora a funcionalidade de atualização de implementação do Azure em vez de encerrar a implementação anterior e voltar a publicar de raiz que tinha no passado. Isto permite que o seu serviço de nuvem executar sem interrupções sempre que possível, ajudando a alcançar elevada disponibilidade mesmo durante uma atualização e acelera o processo de publicação novamente.
* **Suporte para o mais recente v8 Zulu OpenJDK - atualizar 40**. Para mais informações, consulte a [página web de blue sistemas para o OpenJDK Zulu].

### <a name="march-9-2015"></a>9 de Março de 2015

O Toolkit de Azure para Eclipse - lançamento de Março de 2015 inclui os seguintes melhoramentos:

* **Suporte para Mac, Ubuntu e existem tipos de Linux adicionais**. Nesta versão do Azure Toolkit para Eclipse adiciona suporte para o sistema operativo Mac e plataformas de Unix várias, para que os programadores podem instalar o toolkit para criar, configurar e publicar projetos Java aos serviços em nuvem Azure (PaaS) a partir do Eclipse em execução em sistemas operativos que não seja o Windows.

>[AZURE.NOTE] Esta funcionalidade é na pré-visualização e não é recomendado para utilização em ambientes de produção. Não existe suporte ao cliente contrato de nível de serviço (SLA), mas todos os comentários estão admiramos e encorajado.

* **Plug-in de novas informações de aplicação**. Os programadores estão agora conseguir configurar telemetria servidor automático com informações de aplicação no Azure.
* **Automatização de implementação de conselhos com base em linha de comandos**. Esta funcionalidade permite que os programadores automatizar a publicação de versões mais recentes dos seus implementações utilizando conselhos fora Eclipse. Um script gerado previamente é automaticamente configurado para um projeto após a primeira vez é implementado a partir de Eclipse e subsequentes implementações podem utilizar o script para automatizar totalmente implementações através da linha de comandos apenas.
* **Disponibilidade tomcat e cais no Azure para implementação mais simples e rápida**. Os programadores agora podem fazer referência a várias versões de Tomcat e cais que estão disponíveis no Azure directamente em vez disso, de ter que carregar um servidor de Java para as respetivas contas (ou através do Toolkit), pelo que não é necessário para carregar um servidor de Java para rápida, introdução ao cenários.
* **Método de atalho para publicação Java web apps aos serviços em nuvem Azure**. Para reduzir a curva de aprendizagem para cenários de desenvolvimento e teste simples, os programadores agora podem publicar aplicações Java mais diretamente para o Azure. Em vez de aceder durante o processo de criar e configurar um projecto de implementação do Azure, aplicações serão implementadas com uma instância predefinida do Tomcat v8 e Zulu JVM (OpenJDK).

### <a name="january-30-2015"></a>30 de Janeiro de 2015

O Toolkit de Azure para Eclipse - lançamento de Janeiro de 2015 inclui os seguintes melhoramentos:

* **Suporte para IBM® WebSphere® aplicação Server liberdade Core**. Nesta versão adiciona IBM WebSphere aplicação Server liberdade Core à lista de suportados de servidores de aplicação a partir do qual o toolkit é possível implementar Azure. Esta adição mais recente expande a lista atual de servidores de aplicações que são suportadas &quot;out of box&quot; pelo Toolkit, que já incluída várias versões de Tomcat, cais, JBoss e GlassFish.
* A **inclusão de informações de aplicação SDK**. Esta biblioteca de cliente recentemente disponibilizadas API (v0.9.0) faz agora parte do pacote do Azure para bibliotecas de para Java.
* **Atualizados Compactar para bibliotecas Azure para Java**. Esta atualização inclui Azure bibliotecas para Java v0.7.0 e v2.0.0 API do cliente de armazenamento, bem como a aplicação informações SDK v0.9.0 recentemente disponibilizadas.

### <a name="november-12-2014"></a>12 de Novembro de 2014

O Toolkit de Azure para Eclipse - lançamento de Novembro de 2014 inclui os seguintes melhoramentos:

* **Suporte para SDK Azure 2,5**. Esta atualização mais recente para o SDK do Azure é o novo pré necessários para o Toolkit.
* **Suporte para uma versão atualizada do v Zulu OpenJDK 1.8, v1.7 e v1.6 pacotes**. Para mais informações, consulte a [página web de blue sistemas para o OpenJDK Zulu].
* **Suporte para os novos tamanhos de D padrão para serviços em nuvem**, a qual oferta aumentado desempenho e recursos de memória adicionais. Para mais informações, consulte o artigo [Máquina Virtual e tamanhos de serviço de nuvem para Azure].

### <a name="october-17-2014"></a>17 de Outubro de 2014

O Toolkit de Azure para Eclipse - lançamento de Outubro de 2014 inclui os seguintes melhoramentos:

* **Melhorias de desempenho na publicar na cenários na nuvem**. O carregamento de informações da subscrição é muito mais rápido quando os utilizadores têm várias contas de armazenamento e de subscrições.
* **Suporte para uma versão atualizada do pacote v 1.8 Zulu OpenJDK**. Para mais informações, consulte a [página web de blue sistemas para o OpenJDK Zulu].
* **Suporte para preterir versões mais antigas do 3º JDKs de terceiros**. Pacotes JDK preteridas já não irão aparecer no menu pendente para novos projectos de implementação. Projetos existentes referência pacotes JDK preteridas irão continuar a ser conseguir fazê-lo para o tempo a ser, mas é recomendado para actualizar um destes projectos para confiar no último.
* **Versão actualizada do pacote do Azure para bibliotecas de para a biblioteca API do cliente Java**. Para obter mais informações, consulte a [API de cliente do Microsoft Azure].
* **Correções de erros.** Nesta versão contém um número diversos correções de erros que foram com base em relatórios de utilizador e testes.

### <a name="august-5-2014"></a>5 de Agosto de 2014

O Toolkit de Azure para Eclipse - lançamento de Agosto de 2014 inclui os seguintes melhoramentos

* **Suporte para o Azure SDK 2.4.** As versões mais antigas do Eclipse Toolkit não irão funcionar com este SDK disponibilizado recentemente.
* **Atualizados versões do v1.6 Zulu OpenJDK, 1.7 e v 1.8 pacotes.** Para mais informações, consulte a [página web de blue sistemas para o OpenJDK Zulu].
* **Versão atualizada do pacote para bibliotecas com o Azure para a biblioteca API do cliente Java.** Para obter mais informações, consulte a [API de cliente do Microsoft Azure].
* **Suporte para mais recente publica formato de ficheiro de definições.** Foi adicionado suporte para a versão 2.0 do formato de ficheiro de definições de publicar.
* **Arquitetura alterações atrás de publicar a funcionalidade de nuvem.** O Toolkit está agora a utilizar o cliente API disponibilizado recentemente do Microsoft Azure para Java para o suporte de publicar a nuvem.
* **Correções de erros.** Nesta versão contém um número utilizador solicitou correções de erros.

### <a name="june-12-2014"></a>12 de Junho de 2014

O Toolkit de Azure para Eclipse - lançamento de Junho de 2014 é uma atualização de manutenção secundária que fornece os seguintes melhoramentos:

* **Suporte para o pacote de Zulu OpenJDK v 1.8.** Para mais informações, consulte a [página web de blue sistemas para o OpenJDK Zulu].
* **Atualizado versões da v1.6 Zulu OpenJDK e 1.7 pacotes.** Para mais informações, consulte a [página web de blue sistemas para o OpenJDK Zulu].
* **Versão atualizada do pacote para bibliotecas com o Azure para a biblioteca API do cliente Java.** Para obter mais informações, consulte a [API de cliente do Microsoft Azure].
* **Correções de erros.** Nesta versão contém um número utilizador solicitou correções de erros.

### <a name="april-4-2014"></a>4 de Abril de 2014

Publicou o plug-in do Azure Eclipse - lançamento de Abril de 2014. Esta é uma atualização que acompanham o lançamento do 2.3 de SDK Azure, que é um pressuposto e serão transferidos automaticamente ao instalar o plug-in. Esta atualização inclui novas funcionalidades, correções de erros e alguns melhoramentos orientadas por comentários textuais uma vez que o de Fevereiro de 2014 pré-visualizar:

* **Suporte para a versão 2.3 do Azure SDK.** O plug-in do Azure Eclipse - lançamento de Abril de 2014 requer Azure SDK 2.3. Ao utilizar o novo plug-in, se ainda não tiver Azure SDK 2.3, vai ser-lhe permitir a sua instalação. Não utilize Azure SDK 2.3 com versões anteriores do plug-in.
* **Atualizar das aplicações sem a implementação do pacote completo.** Quando implementar aplicações Java que fazem parte do seu projeto, o plug-in agora automaticamente os carregamentos pendentes-las para a sua conta de armazenamento selecionada para que possa atualizar e reciclagem as instâncias de função para implementar os bits mais recentes de aplicação sem ter de a reconstruir e implementar o pacote completo.
* **Tomcat 8 é agora um servidor de aplicação reconhecido.** Se selecionar um diretório de instalação Tomcat 8 no seu computador no separador **servidor** da caixa de diálogo de **Projecto de implementação do Azure** , o plug-in será agora automaticamente detetá-lo e poderá implementar Tomcat 8 de forma automática, semelhante de versões mais antigas do Tomcat já na lista.
* **Atualizações de pacote blue Zulu OpenJDK: atualização de v1.7 51 e v1.6 atualizar 47.** Efectiva com esta versão, do sistema blue JDK abrir Zulu v7 pacote 51 está disponível actualização. Além disso, pacotes de v6 JDK abrir Zulu começar a ser disponível, começando atualização 47. Estas atualizações estão além o pacote de v7 JDK abrir Zulu anteriormente disponível actualização 45, 40 e atualizar 25.
* **Suporte para o tamanho de A8 e A9 Microsoft Azure Virtual Machine.** Agora já pode implementar um serviço na nuvem para a memória alta A8 e tamanhos de máquina de Virtual A9. Para mais informações sobre estes tamanhos VM, consulte o artigo [Máquina Virtual e tamanhos de serviço de nuvem para Azure].
* **Redireccionamento automático HTTP por HTTPS para funções com capacidade de SSL.** Quando o seu serviço de nuvem contém apenas HTTPS funções, se o pedido do utilizador Especifica HTTP, irá redirecionar automaticamente para HTTPS. Não é necessário para criar uma função separada para processar os pedidos HTTP.
* **Expressar emulador utilizado para emulação local.** O Azure Express emulador é agora utilizado como emulador quando depurar localmente suas aplicações.
* **Azure nome foi alterado como Microsoft Azure.** Ecrãs da IU agora refletir as que foram nome alterado e já não denominado Azure Azure.

### <a name="february-6-2014"></a>6 de Fevereiro de 2014

O plug-in do Azure para Eclipse - de Fevereiro de 2014 lançado pré-visualização. Esta atualização inclui novas funcionalidades, correções de erros e alguns melhoramentos orientadas por comentários textuais desde a pré-visualização de Outubro de 2013:

* **Suporte para descarregar SSL.** Descarregar segura de Sockets Layer (SSL) foi adicionado como uma funcionalidade, permitindo-lhe facilmente activar o suporte de HTTPS Hypertext Transfer Protocol seguro () na sua implementação Java no Azure, sem que seja necessário configurar SSL no seu servidor de aplicação Java. Isto é especialmente relevante na sessão afinidade e/ou autenticados cenários de comunicação. Por exemplo, ao utilizar o filtro de serviço de controlo de acesso (ACS), que já é suportado pelo toolkit. Para mais informações, consulte o artigo [SSL descarregar] e [como utilizar SSL descarregar].
* **GlassFish 4 é agora um servidor de aplicação reconhecido.** Se selecionar um diretório de instalação GlassFish 4 no seu computador no separador **servidor** da caixa de diálogo de **Projecto de implementação do Azure** , o plug-in será agora automaticamente detetá-lo e poderá implementar GlassFish OSE 4 de forma automática, semelhante à versão GlassFish OSE 3 já na lista.
* **Actualização do pacote blue Zulu OpenJDK 45.** Eficaz com esta versão, do sistema blue Zulu (pacote de v7 JDK abrir) 45 está agora disponível actualização; Esta é além a atualização disponível anteriormente 40 e actualização de 25.
* **Suporte para automática privado portas de ponto final.** Pode definir uma porta privada para automático para pontos finais de entrada e os pontos finais internos para permitir que o Azure atribuir uma porta automaticamente a esse ponto final. Anteriormente só pode atribuir um número de porta específica.
* **Suporte para personalizar o nome do certificado (NC) na criação de certificado autoassinado IU.** Anteriormente, o mesmo nome codificado foi utilizado para todos os certificados novos; Agora pode especificar o seu próprio nome de certificado para ajudar a distinguir entre vários certificados no portal do Azure utilizados para fins diferentes.
* **Azure barra de ferramentas:** A barra de ferramentas Azure tem um atualizado com as seguintes alterações: 
    * ![][ic710876]Este ícone foi adicionado para o **Novo projeto de implementação do Azure**.
    * ![][ic710877]Este ícone foi adicionado como um atalho para a caixa de diálogo do certificado autoassinado criação.
* **Suporte para o tamanho de máquina de Virtual do Azure A5.** Agora já pode implementar um serviço na nuvem para a memória alta A5 Virtual Machine tamanho. Para mais informações sobre este tamanho da memória virtual, consulte o artigo [Máquina Virtual e tamanhos de serviço de nuvem para Azure].
* **Suporte para o Microsoft Windows Server 2012 R2.** Agora pode selecionar Windows Server 2012 R2 como o sistema operativo de nuvem.

### <a name="october-22-2013"></a>22 de Outubro de 2013

O plug-in do Azure para Eclipse - Outubro de 2013 lançado pré-visualização. Esta atualização inclui novas funcionalidades, correções de erros e alguns melhoramentos orientadas por comentários textuais desde a pré-visualização de Setembro de 2013:

* **Suporte para a versão do Azure SDK 2.2.** O plug-in do Azure Eclipse - Outubro de 2013 pré-visualizar suporta Azure SDK 2.2. O plug-in continuam a funcionar com o Azure SDK 2.1 e irá instalar automaticamente o Azure SDK 2.2 se ainda não tiver, pelo menos, Azure SDK 2.1 instalado.
* **Actualização do pacote blue Zulu OpenJDK 40.** Como anunciada para Setembro de 2013 pré-visualizar, permite a agora o plug-in utilizando um JDK desde de terceiros diretamente no Azure, sem que seja necessário carregar o seu próprio JDK. Na versão de Outubro de 2013, do sistema blue Zulu (pacote de v7 JDK abrir) 40 está agora disponível actualização; Este é além a atualização originalmente publicada 25.
* **Ligação de implementação de nuvem no registo de atividade.** Dentro de registo de atividade Azure, quando a sua implementação tem um Estado de **publicado**, pode clicar em **publicada** uma vez que é uma ligação para a sua implementação; a implementação, em seguida, será aberta no seu browser. (O estado de **Published** foi anteriormente assinalado **em execução**.)
* **Selecção de SO destino disponíveis em publicar hora.** A caixa de diálogo **publicar no Azure** contém um novo campo, **SO de destino**, que fornece uma maneira mais detetável onde pode definir o seu sistema operativo de destino.
* **Substitua automática implementação anterior.** A caixa de diálogo **publicar no Azure** contém uma nova caixa de verificação, **Substituir anterior implementação**. Se esta opção estiver selecionada, quando a implementação do nova é publicada automaticamente substituirá a implementação anterior; que se não quiser deparar &quot;conflito 409&quot; problemas ao publicar na mesma localização sem primeiro anular a publicação de implementação anterior.
* **Cais 9 são agora um servidor de aplicação reconhecido.** Se selecionar um diretório de instalação do cais 9 no seu computador no separador **servidor** da caixa de diálogo de **Projecto de implementação do Azure** , o plug-in será agora automaticamente detetá-lo e poderá implementar cais 9 de forma automática, semelhante de versões mais antigas do cais já na lista.
* **Adicione uma função a partir do menu de contexto do projeto.** Menu de contexto do projeto **Azure** contém agora um novo item de menu, **Adicionar função**, que fornece um mais rápido e mais forma detetável de adicionar um novo perfil ao seu projeto Azure.
* **Uma atualização para o pacote para as bibliotecas do Azure para biblioteca Java.** Se baseia-se a versão 0.4.6 da [API de cliente do Microsoft Azure].

### <a name="september-25-2013"></a>25 de Setembro de 2013

O plug-in do Azure para Eclipse - Setembro de 2013 lançado pré-visualização. Esta atualização inclui novas funcionalidades, correções de erros e alguns melhoramentos orientadas por comentários textuais desde o de 2013 Agosto pré-visualizar:

* **Capacidade para implementar o pacote de blue Zulu OpenJDK disponível no Azure.** Uma nova opção foi adicionada ao especificar JDK para utilizar com a sua implementação Azure. Utilizar esta opção, pode implementar um pacote JDK terceiros diretamente no Azure nuvem, sem ser necessário que carregar a sua própria. Sistemas de blue está a fornecer o primeiro tal compactar Zulu denominados, com base em OpenJDK, que agora pode ser implementada ao utilizar esta opção.
* **Uma atualização para o pacote para as bibliotecas do Azure para biblioteca Java.** Se baseia-se a versão 0.4.5 da [API de cliente do Microsoft Azure].

### <a name="august-1-2013"></a>1 de Agosto de 2013

O plug-in do Azure para Eclipse - de 2013 Agosto lançado pré-visualização. Esta é uma atualização que acompanham o lançamento do 2.1 de SDK Azure, que é um pressuposto e serão transferidos automaticamente ao instalar o plug-in. Esta atualização inclui novas funcionalidades, correções de erros e alguns melhoramentos orientadas por comentários textuais desde a pré-visualização de Julho de 2013:

* **Remoção das opções para incluir o local JDK e o servidor de aplicação local como parte do pacote de implementação do.** Transferir o JDK e application server a partir do armazenamento em nuvem durante a implementação é preferível a incorporação estes componentes no pacote, desde a transferir os resultados de itens num tamanho mais pequeno de pacote de implementação, os tempos de implementação mais rápidos e mais fácil manutenção. Como resultado, as opções para incluir a JDK e application server do pacote de implementação do foram removidas. Projetos existentes que tenham sido configurados para incluir o local JDK e o servidor de aplicação local, tal como parte do pacote de implementação serão automaticamente convertidas em automática-carregar o JDK e application server para armazenamento em nuvem.
* **Suporte para a versão do Azure SDK 2.1.** O plug-in do Azure para Eclipse - Agosto de 2013 pré-visualização requer o Azure SDK 2.1. Não utilize a pré-visualização de Agosto de 2013 com versões anteriores do SDK do Azure e não utilize Azure SDK 2.1 com versões anteriores do plug-in do Azure para Eclipse.
* **Suporte para a versão de Eclipse Kepler.** Relacionados com a esta, o novo mínimo exigido Eclipse IDE versão é Índigo. O plug-in do Azure Eclipse oficialmente já não é testado no Helios.

### <a name="july-3-2013"></a>3 de Julho de 2013

O plug-in do Azure para Eclipse - Julho de 2013 lançado pré-visualização. Esta atualização inclui novas funcionalidades, correções de erros e alguns melhoramentos orientadas por comentários textuais desde a pré-visualização de Maio de 2013:

* **Capacidade de criar uma nova conta de armazenamento.** Foi adicionado um botão de **Novo** para a caixa de diálogo **Adicionar conta de armazenamento** . Esta opção permite-lhe criar uma conta de armazenamento dentro o plug-in Eclipse, sem que seja necessário iniciar sessão no Portal de gestão do Azure. (Tem já tiver uma subscrição do Azure para utilizar esta funcionalidade.) Para mais informações sobre como criar uma nova conta de armazenamento, consulte o artigo [criar uma nova conta de armazenamento].
* **Novo &quot;(automático)&quot; opção para a conta de armazenamento utilizada para implementação automática do servidor e JDK e para colocação em cache.** Quando utilizar a opção **carregar automaticamente** para a JDK e servidor de aplicações, pode agora especificar **(automático)** para a conta de URL e armazenamento para utilizar quando carregar o JDK e servidor de aplicações, ou ao utilizar o Azure colocação em cache. Em seguida, estas funcionalidades automaticamente irão utilizar a mesma conta de armazenamento como aquele que selecionar na caixa de diálogo **publicar no Azure** . O tutorial de [criar uma aplicação de Olá mundo para Azure no Eclipse] foi atualizado para utilizar a nova opção de **(automático)** .
* **Capacidade para definir os pontos finais de serviço Azure.** Especifique os pontos finais de serviço que determinam que se a aplicação é implementada e gerida pela plataforma Azure global, Azure operado pela 21Vianet na China, ou um privado a plataforma Azure. Para mais informações, consulte o artigo [Pontos finais de serviço do Azure].
* **Grandes implementações podem especificar um recurso de armazenamento local.** Na eventualidade de sua implementação é demasiado grande para ser contidas na pasta approot predefinida, pode agora especificar um recurso de armazenamento local como o destino de implementação para sua JDK e servidor de aplicações. Para mais informações, consulte o artigo [Implementar grandes implementações].
* **Suporte para os tamanhos A6 e A7 Azure Virtual Machine.** Agora já pode implementar um serviço na nuvem para a memória alta A6 e A7 Virtual Machine tamanhos. Para mais informações sobre estes tamanhos, consulte o artigo [Máquina Virtual e tamanhos de serviço de nuvem para Azure].
* **Uma atualização para o pacote para as bibliotecas do Azure para biblioteca Java.** Se baseia-se a versão 0.4.4 da [API de cliente do Microsoft Azure].

### <a name="may-1-2013"></a>1 de Maio de 2013

O plug-in do Azure Eclipse - poderá 2013 pré-visualização lançado. Esta é uma atualização principal que acompanham o lançamento do Azure SDK 2.0, que é um pressuposto e serão transferidos automaticamente ao instalar o plug-in. Esta versão inclui novas funcionalidades, correções de erros e alguns melhoramentos orientadas por comentários textuais desde a pré-visualização de Fevereiro de 2013:

* **Carregar automática da JDK e application server para e implementação a partir do armazenamento Azure.** Nova opção qual automaticamente os carregamentos pendentes o JDK selecionado e o servidor de aplicação, quando for necessário, para uma conta de armazenamento Azure especificada e implementa estes componentes a partir daí, em vez de geralmente o pacote de implementação ou ter o carregamento de utilizador, em seguida, manualmente. Esta funcionalidade solicitada frequentemente pode melhorar significativamente a facilidade de implementação do componentes JDK e server, especialmente para os utilizadores inexperientes. Para uma guia passo a passo que utiliza estas opções, consulte o artigo [criar uma aplicação de Olá mundo para Azure no Eclipse].
* **Centralizadas controlo de conta de armazenamento e a capacidade de armazenamento contas de referência mais facilmente (através de um controlo de lista pendente).** Aplica-se a várias funcionalidades que dependem de armazenamento, como JDK e implementação do componente de servidor e colocação em cache. Para mais informações, consulte a [Lista de conta de armazenamento do Azure].
* **Simplificar a configuração de acesso remoto na publicar ao Assistente de nuvem** Tudo o que precisa de fazer é escreva um nome de utilizador e palavra-passe para activar o acesso remoto ou deixá-la em branco para manter o acesso remoto desativado.
* **Uma atualização para o pacote para as bibliotecas do Azure para biblioteca Java.** Se baseia-se a versão 0.4.2 da [API de cliente do Microsoft Azure].
* **Suporte para sessões autocolantes no Windows Server 2012.** Anteriormente, sessões autocolantes trabalhou apenas no Windows Server 2008 R2, agora ambos na nuvem afinidade de sessão de suporte do sistema operativo destinos.
* **Melhorias de desempenho de carregamento do pacote.** Mesmo quando o JDK e incorporadas no pacote de implementação do servidor de aplicações, a parte de carregamento do processo de implementação pode ser aproximadamente duas vezes tão rápida em comparação com versões anteriores.

### <a name="february-8-2013"></a>8 de Fevereiro de 2013

O plug-in do Azure para Eclipse - Fevereiro de 2013 lançado pré-visualização. Esta é uma atualização secundária que inclui correções de erros, melhoramentos orientadas por comentários textuais e algumas das novas funcionalidades, uma vez que o de 2012 de Novembro de pré-visualizar:

* Suporte para implementar JDKs, servidores de aplicações e arbitrários outros componentes do Azure público ou privado blob transferências de armazenamento em vez de incluindo-los no pacote de implementação do quando implementá-lo na nuvem.
* Capacidade para alterar a ordem pela qual são processados definidos pelo utilizador componentes de uma função, através da adição dos botões **Mover para cima** e **Mover para baixo** na secção **componentes** das **Propriedades de função Azure**.
* Uma atualização para a biblioteca de **pacote para as bibliotecas do Azure para Java** , com base na versão 0.4.0 da [API de cliente do Microsoft Azure].

### <a name="november-5-2012"></a>5 de Novembro de 2012

O plug-in do Azure para Eclipse - Novembro de 2012 lançado pré-visualização. Esta é uma atualização principal que inclui um número de novas funcionalidades, bem como correções de erros adicionais e melhoramentos orientadas por comentários da eficiência de utilização de uma vez que o de 2012 de Setembro de pré-visualizar:

* Suporte para o Microsoft Windows Server 2012 como o sistema operativo de nuvem.
* Suporte para o suporte de colocação em cache cocriação localizado Azure para memcached clientes.
* Inclusão das bibliotecas do cliente Apache Qpid JMS para tirar partido das baseadas no Azure AMQP mensagens.
* Um Assistente de **Novo projeto** melhorado, com uma nova página no final que fornece aos utilizadores com a capacidade de ativar rapidamente várias funcionalidades chaves comuns no seu projeto: sessões autocolantes, colocação em cache e depuração remota.
* Redução automática do instâncias de função como 1 quando a ser executada em emulador cluster, para evitar conflitos de encadernação de porta entre instâncias do servidor.

### <a name="september-28-2012"></a>28 de Setembro de 2012

O plug-in do Azure para Eclipse - de 2012 de Setembro de pré-visualização lançado. Esta atualização de serviço inclui um número adicionais correções de erros, uma vez que a Agosto de 2012 pré-visualizar, bem como algumas melhorias textuais orientadas por comentários no funcionalidades existentes:

* Suporte para o Microsoft Windows 8 e Microsoft Windows Server 2012 como o sistema operativo de desenvolvimento, resolver problemas que anteriormente impediram o plug-in de funcionar corretamente nesses sistemas operativos.
* Suporte melhorado para especificação de intervalos de porta de ponto final.
* Correções de erros relacionados com caminhos de ficheiros com espaços.
* Melhoramentos de menu de contexto de função para um acesso mais rápido para as definições de configuração específicas da função.
* Secundárias refinamentos no Assistente de **publicação para a cloud** e um número adicionais correções de erros.

### <a name="august-28-2012"></a>28 de Agosto de 2012

O plug-in do Azure para Eclipse - Agosto de 2012 lançado pré-visualização. Esta atualização de serviço inclui correções de erros adicionais, uma vez que de Julho de 2012 pré-visualizar, assim como várias melhorias do esforço de comentários da eficiência de utilização para funcionalidades existentes:

* Dentro da caixa de diálogo Filtro de serviços de controlo de acesso de Azure:
    * **Opção para incorporar o certificado de assinatura** no ficheiro de guerra da sua aplicação, para simplificar a implementação na nuvem.
    * **Opção para criar um certificado autoassinado** dentro do filtro de ACS IU. Para obter informações adicionais sobre o filtro de serviços de controlo de acesso do Azure, consulte o artigo [como utilizadores do Web autenticar com Azure acesso controlo serviço utilizando Eclipse].
* No Assistente de projecto de implementação do Azure (também se aplica à página de propriedades de configuração do servidor a função):
    * **Deteção automática da localização JDK** no seu computador (o que pode substituir se pretender).
    * **Deteção automática do tipo de servidor** quando selecionar o diretório de instalação do servidor de aplicação.

### <a name="july-15-2012"></a>15 de Julho de 2012

O plug-in do Azure para Eclipse - Julho de 2012 pré-visualizar, endereços de um número de erros prioridade mais altos que se encontram e/ou reportados pelos utilizadores após o lançamento de Junho de 2012, lançado. Este é apenas uma atualização de serviço, não existem novas funcionalidades estão contidas.

### <a name="june-7-2012"></a>7 de Junho de 2012

Publicou o Azure Plug-in para Eclipse - CTP de Junho de 2012. Novas funcionalidades incluem:

* **Assistente do novo projeto de implementação do Azure:** Permite-lhe selecionar o seu JDK, Java application server e aplicações Java diretamente no Assistente melhorado IU. Estão incluídas na lista de configurações de servidor de fora da caixa para escolher a partir do Tomcat 6, Tomcat 7, GlassFish OSE 3, cais 7, 8 cais, JBoss 6 e 7 JBoss (versão autónoma). Para além disso, pode personalizar a lista de configurações de servidor. Este melhoramento IU é uma alternativa para arrastar e largar ficheiros comprimidos e copiar através de scripts de arranque, que era anteriormente a abordagem principal. Ainda esse método funciona perfeitamente, mas provavelmente será utilizado apenas para cenários mais avançados.
* **Página de propriedades de função de configuração do servidor:** Permite-lhe alternar facilmente JDKs, servidores de aplicações Java e aplicações associadas com a sua implementação depois de ter criado o projeto. Para mais informações, consulte o artigo [Propriedades do servidor de configuração].
* **&quot;Publicar nuvem&quot; assistente:** fornecem uma forma fácil para implementar o projeto Azure diretamente a partir do Eclipse automatizar anteriormente manual grossa-elevação de obter as credenciais, iniciar sessão no Portal de gestão do Azure, ao carregar um pacote, etc. Para obter um exemplo de como diretamente implementar o projeto para Azure, consulte o artigo [criar uma aplicação de Olá mundo para Azure no Eclipse].
* **Azure barra de ferramentas:** Uma barra de ferramentas Azure está agora disponível no Eclipse que contém botões invocar as seguintes funcionalidades:
    * ![][ic710879]**Executar no Azure emulador**: é executado o seu projeto no emulador.
    * ![][ic710880]**Repor o Azure emulador**: Repõe o emulador.
    * ![][ic710881]**Criar pacote de nuvem para Azure**: compila seu pacote para implementação.
    * ![][ic710876]**Novo projeto de implementação do Azure**: cria um novo projeto de implementação do Azure.
    * ![][ic710882]**Publicar no Azure nuvem**: publica projeto Azure.
    * ![][ic710883]**Anular publicação**: elimina a sua implementação.
    * Muitos destes botões da barra de ferramentas Azure são utilizados na [criação de uma aplicação de Olá mundo para Azure no Eclipse].
* **Azure bibliotecas para Java:** Agora disponível como parte do pacote único para bibliotecas com o Azure de biblioteca de Java no Eclipse, a que o acompanha a instalação do plug-in e que contém todas as dependências de necessárias. Adicionar apenas uma referência para a biblioteca no seu projeto Java e não é necessário transferir algo que separadamente. Para mais informações, consulte o artigo [instalar o Toolkit de Azure para Eclipse].
* **Microsoft JDBC controlador 4.0 para SQL Server disponíveis durante a instalação do plug-in:** Durante a instalação do plug-in de novo, pode ser instalada a versão mais recente do controlador de JDBC da Microsoft para o SQL Server.
* **Azure acesso controlo filtro do serviço disponível durante a instalação do plug-in:** Este novo componente incluído como uma biblioteca de Eclipse o toolkit, permite a aplicação web Java totalmente tirar partido de autenticação de serviço de controlo de acesso do Azure (ACS) utilizando vários fornecedores de identidade, como o Google, Live.com e Yahoo!. Não é necessário escrever lógica de autenticação si, basta configurar algumas opções e permitir que o filtro de fazer a elevatórias grossa da ativação de utilizadores para iniciar sessão a ACS. Apenas pode concentrar-se sobre como escrever o código que permite aos utilizadores acesso aos recursos com base na sua identidade, tal como devolvidos à aplicação pelo filtro dentro do objeto do pedido. Para obter um tutorial sobre como utilizar o filtro de ACS, consulte o artigo [como utilizadores do Web autenticar com Azure acesso controlo serviço utilizando Eclipse].
* **Deteção automática do Azure SDK 1.7 pré-requisito:** Quando cria um novo projeto de implementação do Azure, Azure SDK 1.7 será automaticamente transferido se não estiver instalado.
* **Pontos finais da instância:** Permite o acesso de ponto final de porta direta para a comunicação com balanceamento de carga função instâncias. Os pontos finais de instância podem ser adicionados através os pontos finais da IU, disponível através da página [pontos finais propriedades] . Isto ajuda a Ativar depuração remota e diagnósticos JMX para específico calcular instâncias em execução na nuvem em cenários com multi-implementações da instância. 
* **Componentes IU:** Torna mais fácil para os utilizadores avançados configurar o dependências de projecto entre funções Azure individuais no projeto e outros recursos externos, como projetos de aplicação Java; Também torna mais fácil descrever respectiva lógica de implementação. Para mais informações, consulte o artigo [Propriedades de componentes].
* **Atualização automática das versões anteriores de projetos:** Quando abre uma área de trabalho que tenha projecto Azure criado com uma versão anterior do plug-in, os projectos antigos serão apresentados no Eclipse como fechadas, uma vez que não são compatíveis com a nova versão versões anteriores de projetos. Se tentar abrir um desses projectos antigo, irá iniciar um Assistente de atualização. Se concordar com a atualização, um novo projeto, com **_Upgraded** anexado ao nome, será criado e automaticamente atualizado para trabalhar com a nova versão. Pode mudar o nome novo projeto, conforme necessário. Como parte da atualização, o projeto original não será modificado (e irá permanecer fechado).

### <a name="december-10-2011"></a>10 de Dezembro de 2011

Publicou o Azure Plug-in para Eclipse - CTP de Dezembro de 2011. Novas funcionalidades incluem:

* **Afinidade sessão (&quot;sessões autocolantes&quot;) de suporte:** Ajudar a ativar com estado, agrupado aplicações Java com apenas uma caixa de verificação única. Para mais informações, consulte o artigo [Afinidade sessão].
* **Previamente efetuadas exemplos de scripts de arranque:** Para mais populares Java servidores (Tomcat, cais, JBoss, GlassFish), que lhe pode apenas copiar/colar a partir do diretório de amostras do seu projeto para o script de arranque.
* **Saída de arranque emulador em tempo real:** Agora pode ver a execução de todos os passos a partir do seu script de arranque numa janela de consola dedicada, mostrando-lhe controlar o progresso e falhas no seu script conforme é executada ao Azure.
* **Java.exe automáticas e mais simplificada monitorização:** Que vai forçar uma reciclagem de função quando java.exe deixará de ser executada, utilizando um script simples e previamente criado automaticamente incluído na sua implementação.
* **Aplicação Java remoto depuração de configuração da IU:** Permite-lhe ativar facilmente depurador de remoto do Eclipse aceder à sua aplicação Java a executar o emulador ou na nuvem Azure, para que possa visualizar passo a passo e depurar o seu código Java em tempo real. Para mais informações, consulte o artigo [Depuração de aplicações do Azure no Eclipse].
* **Configuração de recurso de armazenamento local IU:** Por isso, já não tem de configurar recursos locais através da manipulação XML diretamente. Esta funcionalidade também permite-lhe aceder e o caminho do ficheiro eficaz dos seus recursos locais depois de implementado através de uma variável de ambiente que pode referenciar diretamente a partir do seu script de arranque. Para mais informações, consulte o artigo [Propriedades de armazenamento Local].
* **Configuração variável de ambiente IU:** Por isso, já não tem de definir variáveis de ambiente através da edição manual da configuração do XML. Para mais informações, consulte o artigo [Propriedades de variáveis de ambiente].
* **Controlador JDBC para SQL Azure:** Obtém instalado através de plug-in como uma biblioteca de Eclipse totalmente integrada, permitindo-mais fácil de programação contra SQL Azure. 
* **Menu de contexto rápido acesso à configuração da função IU**: apenas o botão direito do rato na pasta de função e clique em **Propriedades**.
* **Ícones de pasta de projecto e função Azure personalizada:** Para melhor visibilidade e navegação mais fácil a área de trabalho e o project.

## <a name="see-also"></a>Consulte também ##

Para mais informações sobre os conjuntos de ferramentas do Azure para comunicação de IDES: Java, consulte as ligações seguintes:

- [Toolkit Azure para Eclipse]
  - [Instalar o Toolkit Azure para Eclipse]
  - [Criar uma aplicação de Web do mundo Olá para Azure no Eclipse]
  - *Quais são as novidades no Azure Toolkit para Eclipse (Este artigo)*
- [Toolkit Azure para IntelliJ]
  - [Instalar o Toolkit Azure para IntelliJ]
  - [Criar uma aplicação de Web do mundo Olá para Azure no IntelliJ]
  - [Quais são as novidades no Azure Toolkit para IntelliJ]

Para mais informações sobre como utilizar o Azure com Java, consulte o [Centro de programadores do Azure Java].

<!-- URL List -->

[Toolkit Azure para Eclipse]: ./azure-toolkit-for-eclipse.md
[Toolkit Azure para IntelliJ]: ./azure-toolkit-for-intellij.md
[Criar uma aplicação de Web do mundo Olá para Azure no Eclipse]: ./app-service-web/app-service-web-eclipse-create-hello-world-web-app.md
[Criar uma aplicação de Web do mundo Olá para Azure no IntelliJ]: ./app-service-web/app-service-web-intellij-create-hello-world-web-app.md
[Instalar o Toolkit Azure para Eclipse]: ./azure-toolkit-for-eclipse-installation.md
[Instalar o Toolkit Azure para IntelliJ]: ./azure-toolkit-for-intellij-installation.md
[What's New in the Azure Toolkit for Eclipse]: ./azure-toolkit-for-eclipse-whats-new.md
[Quais são as novidades no Azure Toolkit para IntelliJ]: ./azure-toolkit-for-intellij-whats-new.md

[Centro de programadores do Azure Java]: http://go.microsoft.com/fwlink/?LinkID=699547

[Página web de blue sistemas para o OpenJDK Zulu]: http://go.microsoft.com/fwlink/?LinkId=402457
[Pontos finais de serviço do Azure]: http://go.microsoft.com/fwlink/?LinkID=699526
[Lista de conta de armazenamento Azure]: http://go.microsoft.com/fwlink/?LinkID=699528
[Propriedades de componentes]: http://go.microsoft.com/fwlink/?LinkID=699525#components_properties
[Criar uma aplicação do mundo Olá Azure no Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699533
[Depuração de aplicações do Azure no Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699535
[Implementar grandes implementações]: http://go.microsoft.com/fwlink/?LinkID=699536
[Propriedades de pontos finais]: http://go.microsoft.com/fwlink/?LinkID=699525#endpoints_properties
[Propriedades de variáveis de ambiente]: http://go.microsoft.com/fwlink/?LinkID=699525#environment_variables_properties
[Plug-in do HDInsight ferramentas para Eclipse]: ./hdinsight/hdinsight-apache-spark-eclipse-tool-plugin.md
[Como autenticar utilizadores do Web com o serviço de controlo de acesso Azure utilizando Eclipse]: http://go.microsoft.com/fwlink/?LinkID=264703
[Como utilizar SSL descarregar]: http://go.microsoft.com/fwlink/?LinkID=699545
[Instalar o Toolkit Azure para Eclipse]: http://go.microsoft.com/fwlink/?LinkId=699546
[Propriedades de armazenamento local]: http://go.microsoft.com/fwlink/?LinkID=699525#local_storage_properties
[API de cliente do Microsoft Azure]: http://go.microsoft.com/fwlink/?LinkId=280397
[Propriedades do servidor de configuração]: http://go.microsoft.com/fwlink/?LinkID=699525#server_configuration_properties
[Afinidade sessão]: http://go.microsoft.com/fwlink/?LinkID=699548
[Descarregar SSL]: http://go.microsoft.com/fwlink/?LinkID=699549
[Para criar uma nova conta de armazenamento]: http://go.microsoft.com/fwlink/?LinkID=699528#create_new
[Máquina virtual e tamanhos de serviço de nuvem para Azure]: http://go.microsoft.com/fwlink/?LinkId=466520

<!-- IMG List -->

[ic710876]: ./media/azure-toolkit-for-eclipse-whats-new/ic710876.png
[ic710877]: ./media/azure-toolkit-for-eclipse-whats-new/ic710877.png
[ic710879]: ./media/azure-toolkit-for-eclipse-whats-new/ic710879.png
[ic710880]: ./media/azure-toolkit-for-eclipse-whats-new/ic710880.png
[ic710881]: ./media/azure-toolkit-for-eclipse-whats-new/ic710881.png
[ic710876]: ./media/azure-toolkit-for-eclipse-whats-new/ic710876.png
[ic710882]: ./media/azure-toolkit-for-eclipse-whats-new/ic710882.png
[ic710883]: ./media/azure-toolkit-for-eclipse-whats-new/ic710883.png
