<properties
   pageTitle="Aceder ao Azure máquinas virtuais do Explorador de servidor do | Microsoft Azure"
   description="Obter uma descrição geral de como ver, criar e gerir Azure máquinas virtuais (VMs) no Explorador do servidor no Visual Studio."
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="08/15/2016"
   ms.author="tarcher" />

# <a name="accessing-azure-virtual-machines-from-server-explorer"></a>Aceder a máquinas virtuais Azure a partir do Explorador de servidor

Através do Explorador de servidor no Visual Studio, pode apresentar informações sobre as máquinas virtuais alojadas pelo Azure.

## <a name="accessing-virtual-machines-in-server-explorer"></a>Aceder a máquinas virtuais no Explorador do servidor

Se tiver máquinas virtuais alojadas pelo Azure, pode acedê-los no Explorador do servidor. Tem primeiro de iniciar sessão à sua subscrição do Azure para ver os seus serviços de dispositivos móveis. Para iniciar sessão, abra o menu de atalho para o nó Azure no Explorador do servidor e, selecione **ligar ao Microsoft Azure**.

### <a name="to-get-information-about-your-virtual-machines"></a>Para obter informações sobre as máquinas virtuais

1. No Explorador do servidor, selecione uma máquina virtual e, em seguida, selecione a tecla F4 para mostrar a janela de propriedades.

    A tabela seguinte mostra as propriedades que estão disponíveis, mas são todas as só de leitura. Para alterá-los, utilize o [Azure portal clássica](http://go.microsoft.com/fwlink/?LinkID=213885).

  	|Propriedade|Descrição|
  	|---|---|
  	|Nome de DNS|O URL com o endereço Internet de máquina virtual.|
  	|Ambiente|Para uma máquina virtual, o valor desta propriedade é sempre de produção.|
  	|Nome|O nome da máquina virtual.|
  	|Tamanho|O tamanho da máquina virtual, que reflita a quantidade de memória e espaço no disco que está disponível. Para obter mais informações, consulte como: configurar tamanhos de Máquina Virtual.|
  	|Estado|Valores incluem inicial, introdução, paragem, parado e obtenção de estado. Se for apresentada a obtenção de estado, o estado atual é desconhecido. Os valores para esta propriedade diferem os valores que são utilizados no [Azure portal clássica](http://go.microsoft.com/fwlink/?LinkID=213885).|
  	|SubscriptionID|O ID da subscrição para a sua conta Azure. Pode mostrar esta informação no [portal clássica Azure](http://go.microsoft.com/fwlink/?LinkID=213885) visualizando as propriedades para uma subscrição.|

1. Escolha um nó de ponto final e, em seguida, visualize a janela de **Propriedades** .

1. A tabela seguinte descreve as propriedades disponíveis de pontos finais, mas são só de leitura. Para adicionar ou editar os pontos finais para uma máquina virtual, utilize o [Azure portal clássica](http://go.microsoft.com/fwlink/?LinkID=213885). 

  	|Propriedade|Descrição|
  	|---|---|
  	|Nome|Um identificador para o ponto final.|
  	|Porta de privado|Porta para aceder à rede interna para a sua aplicação.|
  	|Protocolo|O protocolo que utiliza a camada de transporte para este ponto final, TCP ou UDP.|
  	|Porta pública|A porta que é utilizada para acesso do público para a sua aplicação.|

## <a name="next-steps"></a>Próximos passos

Para saber mais sobre como utilizar funções Azure no Visual Studio, consulte o artigo [Utilizar o ambiente de trabalho remoto com o Azure funções](vs-azure-tools-remote-desktop-roles.md).
