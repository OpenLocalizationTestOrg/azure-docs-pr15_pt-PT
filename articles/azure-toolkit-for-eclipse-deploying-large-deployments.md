<properties
    pageTitle="Implementar grandes implementações"
    description="Saiba como implementar grandes implementações utilizando o Toolkit de Azure para Eclipse."
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
    ms.date="08/11/2016" 
    ms.author="robmcm"/>

<!-- Legacy MSDN URL = https://msdn.microsoft.com/library/azure/dn268601.aspx -->

# <a name="deploying-large-deployments"></a>Implementar grandes implementações #

Se a sua implementação é demasiado grande para ser contidas na pasta approot predefinida, pode utilizar um recurso de armazenamento local como a pasta de raiz de implementação para sua JDK e servidor de aplicações.

## <a name="to-use-a-local-storage-resource-as-the-deployment-root-folder-for-large-deployments"></a>Para utilizar um recurso de armazenamento local como a pasta de raiz de implementação para implementações grandes ##

1. Crie um novo recurso de armazenamento local. O nome do recurso não importa. Recursos de armazenamento estão definidos ao nível da função. A forma mais rápida para aceder ao armazenamento local caixa de diálogo Configuração, a partir do qual foi possível criar um novo recurso de armazenamento local, é utilizando os seguintes passos: a função na vista do **Explorador de projeto** de contexto (expanda o nó do seu projeto Azure se não vir a função), clique em **Azure**e, em seguida, clique em **Armazenamento Local**. Na caixa de diálogo **Armazenamento Local** , clique em **Adicionar** para criar um novo recurso de armazenamento local.
1. Defina o tamanho pretendido para, pelo menos, 2048 MB (nada menos pode causar mesmo ficheiro tamanho problemas à medida que pretende encontrar na approot).
1. Certifique-se de que está selecionada **Limpar o conteúdo quando a instância de função está reciclada** ; Isto irá ajudar a impedir a execução para conflitos com os ficheiros pré-existentes o recurso quando está reciclada a instância de funções de lógica de arranque de implementação.
1. Certifique-se de que o valor de **variável de ambiente armazenar o caminho do diretório do recurso após a implementação** está definido para a cadeia **DEPLOYROOT**. Caixa de diálogo do recurso seu armazenamento local terá uma aspeto semelhante ao seguinte.
    ![][ic667943]

Em alternativa, se utilizar **DEPLOYROOT** como o *nome* do seu recurso local e não altere o nome da variável ambiente gerado automaticamente (que será definido como **DEPLOYROOT_PATH** nesse caso), que iria funcionar para a sua aplicação também.

Informações adicionais sobre a criação de um recurso de armazenamento local podem ser encontradas em [Propriedades do armazenamento Local][].

## <a name="see-also"></a>Consulte também ##

[Toolkit Azure para Eclipse][]

[Criar uma aplicação do mundo Olá Azure no Eclipse][]

[Instalar o Toolkit Azure para Eclipse][] 

Para mais informações sobre como utilizar o Azure com Java, consulte o [Centro de programadores do Azure Java][].

<!-- URL List -->

[Centro de programadores do Azure Java]: http://go.microsoft.com/fwlink/?LinkID=699547
[Toolkit Azure para Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699529
[Criar uma aplicação do mundo Olá Azure no Eclipse]: http://go.microsoft.com/fwlink/?LinkID=699533
[Instalar o Toolkit Azure para Eclipse]: http://go.microsoft.com/fwlink/?LinkId=699546
[Propriedades de armazenamento local]: http://go.microsoft.com/fwlink/?LinkID=699525#local_storage_properties

<!-- IMG List -->

[ic667943]: ./media/azure-toolkit-for-eclipse-deploying-large-deployments/ic667943.png
