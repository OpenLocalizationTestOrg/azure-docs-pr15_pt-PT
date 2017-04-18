<properties
    pageTitle="Quais são as novidades no Azure Toolkit para IntelliJ | Microsoft Azure"
    description="Saiba mais sobre as funcionalidades mais recentes no Azure Toolkit para IntelliJ."
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

# <a name="whats-new-in-the-azure-toolkit-for-intellij"></a>Quais são as novidades no Azure Toolkit para IntelliJ

## <a name="azure-toolkit-for-intellij-releases"></a>Toolkit Azure para versões de IntelliJ

Este artigo contém informações sobre os vários lançamentos e atualizações mais recentes para o Toolkit de Azure para IntelliJ.

> [AZURE.NOTE] Também existe uma Toolkit Azure para Eclipse IDE. Para mais informações, consulte o artigo [Toolkit de Azure para Eclipse].

### <a name="august-26-2016"></a>26 de Agosto de 2016

O Toolkit de Azure para IntelliJ - lançamento de Agosto de 2016 inclui as seguintes melhorias:

* **Distribuições JDK personalizado**. O Toolkit de Azure para IntelliJ suporta agora especificando e implementar uma versão JDK arbitrária ao seu Web App do Azure contentor:
  - Para além de JDKs fornecidas pela Azure, também pode escolher a partir de uma vasta seleção de versões Zulu OpenJDK disponibilizados no Azure pelos sistemas blue.
  - Também pode especificar o seu próprio distribuição JDK se carregar como um ficheiro ZIP à sua conta de armazenamento.
* **Melhoramentos para a vista do Explorador de Azure**:
  - Suporte para a gestão de Máquina Virtual com o novo modelo de Gestor de recursos do Azure: da lista, criar e eliminar recursos com base no gestor virtual máquinas sem sair do IDE.
  - Suporte para gestão de contas de armazenamento de BLOBs através do Gestor de recursos do Azure, que completa a funcionalidade existente para gerir contas de armazenamento "clássico".
* **Controlador de JDBC da Microsoft 6.0 para SQL Server**. Esta atualização inclui o controlador JDBC mais recente para o Microsoft SQL Server (v 6.0), que é agora incluída como uma biblioteca que pode adicionar facilmente a sua projetos Java, portanto substituir a versão mais antiga.

### <a name="june-29-2016"></a>29 de Junho de 2016

O Toolkit de Azure para IntelliJ - lançamento de Junho de 2016 inclui as seguintes melhorias:

* **Requisito Java 8**. O Toolkit de Azure para IntelliJ requer agora Java 8, embora este requisito encontra-se apenas para o toolkit - as aplicações podem continuar a utilizar todas as versões do Java que são suportadas pelo Azure.
* **Suporte para o mais recente JDKs Java**. As versões mais recentes da JDKs Java agora são suportadas pelo Toolkit de Azure para IntelliJ.
* **Suporte para v2.9.1 Azure SDK**. A versão mais recente do Azure SDK está agora o mínimo pré necessários para o Toolkit de Azure para IntelliJ.
* **Amostras integradas**. Agora o Toolkit de Azure para IntelliJ funcionalidades de várias aplicações de exemplo para ajudar os programadores começar.
* **Integração de ferramenta HDInsight**. HDInsight ferramentas do Azure estão agrupadas agora com o Toolkit de Azure para IntelliJ. Para mais informações, consulte o artigo [HDInsight Plug-in ferramentas para IntelliJ].
* **Depuração do Java Web Apps remota**. O Toolkit de Azure para IntelliJ suporta agora depuração remota de Java web apps num serviço de aplicação do Azure.

### <a name="april-12-2016"></a>12 de Abril de 2016

O Toolkit de Azure para IntelliJ - lançamento de Abril de 2016 inclui as seguintes melhorias:

* **Suporte para v2.9.0 Azure SDK**. A versão mais recente do Azure SDK está agora o mínimo pré necessários para o Toolkit de Azure para IntelliJ.
* **Diversas da eficiência de utilização, melhorias de desempenho e capacidade de resposta relacionados com o suporte do Azure Web App**. Um número de otimizações de desempenho no modo como o Toolkit comunica com o resultado Azure numa mais da IU.
* **Capacidade para eliminar um contentor de aplicação Web existente no Azure a partir de dentro IntelliJ**. O Toolkit de Azure para IntelliJ agora permite-lhe eliminar um contentor Azure Web existente sem sair do IntelliJ.

## <a name="see-also"></a>Consulte também ##

Para mais informações sobre os conjuntos de ferramentas do Azure para comunicação de IDES: Java, consulte as ligações seguintes:

- [Toolkit Azure para Eclipse]
  - [Instalar o Toolkit Azure para Eclipse]
  - [Criar uma aplicação de Web do mundo Olá para Azure no Eclipse]
  - [Quais são as novidades no Azure Toolkit para Eclipse]
- [Toolkit Azure para IntelliJ]
  - [Instalar o Toolkit Azure para IntelliJ]
  - [Criar uma aplicação de Web do mundo Olá para Azure no IntelliJ]
  - *Quais são as novidades no Azure Toolkit para IntelliJ (Este artigo)*

Para mais informações sobre como utilizar o Azure com Java, consulte o [Centro de programadores do Azure Java].

<!-- URL List -->

[Toolkit Azure para Eclipse]: ./azure-toolkit-for-eclipse.md
[Toolkit Azure para IntelliJ]: ./azure-toolkit-for-intellij.md
[Criar uma aplicação de Web do mundo Olá para Azure no Eclipse]: ./app-service-web/app-service-web-eclipse-create-hello-world-web-app.md
[Criar uma aplicação de Web do mundo Olá para Azure no IntelliJ]: ./app-service-web/app-service-web-intellij-create-hello-world-web-app.md
[Instalar o Toolkit Azure para Eclipse]: ./azure-toolkit-for-eclipse-installation.md
[Instalar o Toolkit Azure para IntelliJ]: ./azure-toolkit-for-intellij-installation.md
[Quais são as novidades no Azure Toolkit para Eclipse]: ./azure-toolkit-for-eclipse-whats-new.md
[What's New in the Azure Toolkit for IntelliJ]: ./azure-toolkit-for-intellij-whats-new.md

[Centro de programadores do Azure Java]: http://go.microsoft.com/fwlink/?LinkID=699547

[Plug-in do HDInsight ferramentas para IntelliJ]: ./hdinsight/hdinsight-apache-spark-intellij-tool-plugin.md
