<properties 
   pageTitle="Notas de lançamento do Azure SDK para .NET 2.9" 
   description="Notas de lançamento do Azure SDK para .NET 2.9" 
   services="app-service\web" 
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

# <a name="azure-sdk-for-net-29-release-notes"></a>Notas de lançamento do Azure SDK para .NET 2.9

##<a name="overview"></a>Descrição geral

Este documento contém as notas de lançamento para o SDK do Azure para .NET 2.9 lançamento. 

Para obter informações detalhadas sobre as atualizações nesta versão, consulte o artigo o [Azure SDK 2.9 anúncio publicar](https://azure.microsoft.com/blog/announcing-visual-studio-azure-tools-and-sdk-2-9/).

## <a name="azure-sdk-29-for-visual-studio-2015-update-2-and-visual-studio-15-preview"></a>Azure SDK 2.9 para Visual Studio 2015 Update 2 e Visual Studio "15" pré-visualizar
 
Esta atualização inclui as seguintes correções de erros:

- Ignorado problema relacionado com para o resto API cliente geração na qual a cadeia "Tipo desconhecido" apareceriam como o nome da pasta código-geral e/ou o nome do espaço de nomes para o código gerado.
- Problema relacionado com a WebJobs agendada em que as informações de autenticação não conseguia a transmitir do Scheduler do processo de aprovisionamento.

Esta atualização inclui a nova funcionalidade seguinte:

- Suporte para serviços de aplicação secundários no separador "Serviços" da caixa de diálogo de aprovisionamento de aplicação de serviço. 

##<a name="azure-data-lake-tools-for-visual-studio-2015-update-2"></a>Dados Azure Lake ferramentas para actualização de 2015 do Visual Studio 2
 
Este atualizações inclui o seguinte:

- **Azure dados Lake ferramentas** para Visual Studio é agora fundidos com o SDK do Azure versão .NET. A ferramenta é instalada automaticamente quando instala o SDK do Azure. 

    A ferramenta é atualizada com frequência, aceda [aqui](http://aka.ms/datalaketool) para obter as atualizações.

- Agora o **Server Explorer** permite-lhe ver todos os e criar algumas entidades de metadados U-SQL. Para mais informações, consulte o artigo [neste](https://azure.microsoft.com/documentation/services/data-lake-analytics/) blogue.


##<a name="hdinsight-tools"></a>Ferramentas de HDInsight 

**Ferramentas de HDInsight** para Visual Studio suporta agora HDInsight versão 3.3, incluindo a mostrar Tez gráficos e correções de outro idioma.


##<a name="azure-resource-manager"></a>Gestor de recursos do Azure 

Nesta versão adiciona [KeyVault](../resource-manager-keyvault-parameter.md) suporte para os modelos de processador.

##<a name="see-also"></a>Consulte também

[Mensagem de anúncio SDK 2.9 Azure](https://azure.microsoft.com/blog/announcing-visual-studio-azure-tools-and-sdk-2-9/)
