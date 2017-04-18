<properties
    pageTitle="Publicar aplicações HDInsight | Microsoft Azure"
    description="Saiba como criar e publicar aplicações HDInsight."
    services="hdinsight"
    documentationCenter=""
    authors="mumian"
    manager="jhubbard"
    editor="cgronlun"
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.tgt_pltfrm="na"
    ms.workload="big-data"
    ms.date="10/18/2016"
    ms.author="jgao"/>

# <a name="publish-hdinsight-applications-into-the-azure-marketplace"></a>Publicar HDInsight aplicações para o Azure Marketplace

Uma aplicação do HDInsight é uma aplicação que os utilizadores podem instalar num cluster baseado em Linux HDInsight. Estas aplicações podem ser desenvolvidas pela Microsoft, fornecedores de software independentes (ISV) ou por si. Este artigo vai aprender a publicar uma aplicação de HDInsight para Azure Marketplace.  Para obter informações gerais sobre a publicação para Azure Marketplace, consulte [publicar uma oferta ao Azure Marketplace](../marketplace-publishing/marketplace-publishing-getting-started.md).

Aplicações de HDInsight utilizam o modelo de *Trazer o próprio licença (BYOL)* , onde o fornecedor de aplicação é responsável por licenciamento a aplicação para utilizadores finais e utilizadores finais são apenas cobrados Azure para os recursos que criar, tal como o cluster de HDInsight e respectivos VMs/nós. Neste momento, faturação para a própria aplicação não é feita através do Azure.

Outra aplicação HDInsight relacionadas com o artigo:

- [HDInsight instalar aplicações](hdinsight-apps-install-applications.md): Saiba como instalar uma aplicação do HDInsight a sua clusters.
- [Instalar aplicações personalizadas de HDInsight](hdinsight-apps-install-custom-applications.md): Saiba como instalar e testar aplicações HDInsight personalizadas.

 
## <a name="prerequisites"></a>Pré-requisitos

Para poder submeter a sua aplicação personalizada a mercado, tem de ter criado e testar a sua aplicação personalizada. Consulte os artigos seguintes:

- [Instalar aplicações personalizadas de HDInsight](hdinsight-apps-install-custom-applications.md): Saiba como instalar e testar aplicações HDInsight personalizadas.

Terá também tem de registar a conta de programador. Consulte [publicar uma oferta ao Azure Marketplace](../marketplace-publishing/marketplace-publishing-getting-started.md) e [criar uma conta Microsoft Developer](../marketplace-publishing/marketplace-publishing-accounts-creation-registration.md).

## <a name="define-application"></a>Definir a aplicação

Existem dois passos envolvidos para publicação de aplicações para o Azure Marketplace.  Defina primeiro um ficheiro de **createUiDef.json** para indicar quais clusters a aplicação é compatível com; e, em seguida, publicar o modelo a partir do portal Azure. Segue-se um ficheiro de createUiDef.json de exemplo.

    {
        "handler": "Microsoft.HDInsight",
        "version": "0.0.1-preview",
        "clusterFilters": {
            "types": ["Hadoop", "HBase", "Storm", "Spark"],
            "tiers": ["Standard", "Premium"],
            "versions": ["3.4"]
        }
    }


|Campo  | Descrição   | Valores possíveis|
|-------|---------------|----------------|
|tipos de  | Os tipos de cluster que seja compatível com a aplicação.    |Hadoop, HBase, tempestade, motores (ou qualquer combinação dos seguintes procedimentos)|
|camadas  | As camadas cluster que seja compatível com a aplicação.    |Padrão, Premium, (ou ambas)|
|versões|  Os tipos de cluster HDInsight que seja compatível com a aplicação.    |3.4.|

## <a name="package-application"></a>Aplicação do pacote

Crie um ficheiro zip que contém todos os ficheiros necessários para instalar as aplicações de HDInsight. Terá do ficheiro zip na [aplicação de publicar](#publish-application).

- [createUiDefinition.json](#define-application).
- mainTemplate.json. Ver um exemplo na [instalar aplicações personalizadas de HDInsight](hdinsight-apps-install-custom-applications.md).

    >[AZURE.IMPORTANT] O nome de nomes de script de instalar a aplicação tem de ser exclusivo para um cluster em particular com o formato abaixo. Para além disso qualquer instalar e desinstalar acções de script devem ser idempotent, que significa que os scripts pode ser chamado repeatly enquanto que produza o mesmo resultado.
    
    >   nome":" [concat (' matiz-instalar-v0 ','-', uniquestring('applicationName')] "
        
    >Tenha em atenção existem três partes para o nome de script:
        
    >   1. Um prefixo do nome script, que deve incluir o nome da aplicação ou um nome relevante para a aplicação.
    >   2. A "-" para legibilidade.
    >   3. Função cadeia exclusiva com o nome da aplicação como o parâmetro.

    >   Um exemplo é as extremidades até se tornar acima: matiz-instalar-v0-4wkahss55hlas na lista de ação de script persistentes. Para um nível de JSON exemplo consulte [https://raw.githubusercontent.com/hdinsight/Iaas-Applications/master/Hue/azuredeploy.json](https://raw.githubusercontent.com/hdinsight/Iaas-Applications/master/Hue/azuredeploy.json).

- Todos os necessários scripts.

> [AZURE.NOTE] Os ficheiros da aplicação (incluindo os ficheiros da aplicação web se existir qualquer) pode estar em qualquer ponto final acessível publicamente.

## <a name="publish-application"></a>Publicar aplicação

Siga os passos seguintes para publicar uma aplicação HDInsight:

1. Inicie sessão no [portal de publicação do Azure](https://publish.windowsazure.com/).
2. Clique em **modelos de solução** a partir da esquerda para criar um novo modelo de solução.
3. Introduza um título e, em seguida, clique em **criar um novo modelo de solução**.
3. Clique em **Criar Dev Center de conta e participar no programa de Azure** para registar a sua empresa, se ainda não o tiver feito.  Consulte o artigo [criar uma conta Microsoft Developer](../marketplace-publishing/marketplace-publishing-accounts-creation-registration.md).
4. Clique em **definir algumas topologias para começar a utilizar**. Um modelo de solução é um "principal" a todas as suas topologias. Pode definir várias topologias num modelo oferta/solução. Quando uma oferta é premida para transição, é enviado com todas as suas topologias. 
4. Introduza um nome de topologia e, em seguida, clique no sinal de adição.
5. Introduza uma nova versão e, em seguida, clique no sinal de adição.
6. Carregue o ficheiro zip preparado numa [aplicação do pacote](#package-application).  
7. Clique em **pedido certificação**. A equipa de certificação Microsoft irá rever os ficheiros e certificar a topologia.

## <a name="next-steps"></a>Próximos passos

- [HDInsight instalar aplicações](hdinsight-apps-install-applications.md): Saiba como instalar uma aplicação do HDInsight a sua clusters.
- [Instalar aplicações personalizadas de HDInsight](hdinsight-apps-install-custom-applications.md): Saiba como implementar uma aplicação de HDInsight barra publicada ao HDInsight.
- [Baseado em Personalizar Linux HDInsight clusters utilizando Script acção](hdinsight-hadoop-customize-cluster-linux.md): Saiba como utilizar a ação de Script para instalar as aplicações adicionais.
- [Baseado em criar Linux Hadoop clusters no HDInsight utilizar modelos de Gestor de recursos do Azure](hdinsight-hadoop-create-linux-clusters-arm-templates.md): Saiba como ligar a modelos de Gestor de recursos para criar clusters de HDInsight.
- [Utilizar nós de limite vazia no HDInsight](hdinsight-apps-use-edge-node.md): Saiba como utilizar um nó limite vazia para aceder ao HDInsight cluster, testar HDInsight aplicações e alojamento HDInsight aplicações.

