<properties
    pageTitle="Resolver problemas de autoscale com conjuntos de escala de Máquina Virtual | Microsoft Azure"
    description="Resolver problemas de autoscale com conjuntos de escala de Máquina Virtual. Compreenda típicos problemas encontrados e como para os resolver."
    services="virtual-machine-scale-sets"
    documentationCenter=""
    authors="gbowerman"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machine-scale-sets"
    ms.workload="na"
    ms.tgt_pltfrm="windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/28/2016"
    ms.author="guybo"/>

# <a name="troubleshooting-autoscale-with-virtual-machine-scale-sets"></a>Resolução de problemas autoscale com conjuntos de escala de Máquina Virtual

**Problema** – criou uma infraestrutura autoscaling no Azure Gestor de recursos com conjuntos de escala VM – por exemplo por implementar um modelo da seguinte forma: https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale – tiver as suas regras de escala definidas e grandes, funciona, exceto os independentemente da quantidade carga colocar nas VMs, não serão autoscale.

## <a name="troubleshooting-steps"></a>Passos de resolução de problemas

Algumas coisas a considerar incluem:

- Quantos núcleos a cada VM tem e está a ser carregados cada core?
 O modelo de guia de introdução do Azure de exemplo acima tem um script do_work.php, que carrega uma única core. Se estiver a utilizar uma VM maior que um único núcleo tamanho da memória virtual como Standard_A1 ou D1, em seguida, o que precisa para executar esta carga várias vezes. Verificar quantos cores seu VMs ao rever [máquinas virtuais tamanhos para Windows Azure](../virtual-machines/virtual-machines-windows-sizes.md)

- Quantos VMs no VM escala conjunto, estão trabalho em cada um deles?

    Uma escala de evento só será executada quando a média CPU ao longo de **todo** os VMs num conjunto de escala excede o valor do limiar, ao longo do tempo interno definido nas regras de autoscale.

- Perder quaisquer eventos de escala?

    Verifique os registos de auditoria no portal do Azure para eventos de escala. Talvez Ocorreu uma escala de cores para cima e uma escala de cores para baixo que foi perdida. Pode filtrar por "Escala"...

    ![Registos de auditoria][audit]

- Limiares de escala de entrada e saída de escala são suficientemente diferentes?

    Imaginemos que definir uma regra para dimensionar saída quando CPU média for maior de 50% superior 5 minutos e a escala no quando CPU média é inferior a 50%. Isto seria causar um problema "flapping" quando a utilização da CPU se aproxime este limiar com ações de escala constantemente aumentar e diminuir o tamanho do conjunto de. Por esta razão, o serviço de autoscale tentará impedir que "flapping", que pode como dimensionamento não. Por isso, certifique-se de escala de saída e limiares de escala forem suficientemente diferentes para permitir que algum espaço entre dimensionamento.

- Tenha escrito o seu próprio modelo JSON?

    É fácil cometer, por isso, começar com um modelo, como um acima dos quais prova trabalhar e efetuar alterações utilizarão pequenas. 

- Pode manualmente dimensionar ou reduzir?

    Experimente Reimplementar o recurso VM escala definida com uma definição de "capacidade" diferente para alterar o número de VMs manualmente. Um modelo de exemplo para fazer isto é aqui: https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-scale-existing – poderá ter de editar o modelo para se certificar de que tem o mesmo tamanho de máquina como utilizar o seu conjunto de escala. Se com êxito pode alterar o número de VMs manualmente, em seguida, saberá que o problema é isolado para autoscale.

- Verificar o seu Microsoft.Compute/virtualMachineScaleSet e recursos de Microsoft.Insights no [Explorador de recursos do Azure](https://resources.azure.com/)

    Esta é uma ferramenta de resolução de problemas indispensável que lhe mostra o estado dos seus recursos de Gestor de recursos do Azure. Clique na sua subscrição e procure o grupo de recursos que é resolução de problemas. O fornecedor de recurso cluster observe o conjunto de escala de VM que criou e verifique a vista de instância, que mostra-lhe o estado de uma implementação. Além disso, verifique a vista de instância da VMs no conjunto de escala VM. Em seguida, vá para o fornecedor de recurso Microsoft.Insights e verifique as regras de autoscale tenham boas aspeto.

- A extensão de diagnóstico está a trabalhar e emitir dados de desempenho?

    __Atualização:__ Azure autoscale foi melhorada para utilizar uma tubagem de métricas de anfitrião, com base que já não necessita de uma extensão de diagnóstico para ser instalado. Isto significa que a próxima que alguns parágrafos já não se aplicam se criar uma aplicação de autoscaling utilizando pipeline de novo. Exemplos de modelos de Azure que tenham sido convertidos para utilizar a tubagem anfitrião incluem: https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-bottle-autoscale, https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-lapstack-autoscale. 

    Utilizar métricas de anfitrião, com base para autoscale é melhor pelos seguintes motivos:

    - Menos elementos em movimento como sem extensões de diagnóstico precisa de estar instalado.
    - Modelos de mais simples. Basta adicione regras de autoscale de informações a um modelo de conjunto de escala existente.
    - Mais fiável elaboração de relatórios e mais rápido lançamento de novos VMs.

    As razões apenas que poderá pretender continuar a utilizar uma extensão de diagnóstico seria se terá de memória diagnósticos relatórios/dimensionamento. Métricas de anfitrião, com base não comunicar memória.

    Com que deve ter em conta, siga apenas o resto deste artigo se ainda estiver a utilizar extensões de diagnóstico para sua autoscaling.

    Autoscale no Gestor de recursos do Azure pode trabalhar (mas já não tem de) uma VM através de uma extensão de chamada a extensão de diagnóstico. -Emite dados de desempenho a uma conta de armazenamento que definir no modelo. Estes dados, em seguida, são agregados pelo serviço Azure Monitor.

    Se o serviço de informações não consegue ler dados a partir dos VMs, é suposto para enviar um e-mail – por exemplo, se foram os VMs para baixo, por isso, verifique o seu e-mail (aquele que especificou quando criar a conta Azure).

    Também pode aceder e analisar os dados. Observe a conta de armazenamento Azure através do Explorador na nuvem. Por exemplo através do [Explorador de nuvem do Visual Studio](https://visualstudiogallery.msdn.microsoft.com/aaef6e67-4d99-40bc-aacf-662237db85a2), inicie sessão no e selecione a subscrição Azure estiver a utilizar e o nome de conta de armazenamento de diagnóstico referenciados na definição de extensão da diagnósticos no seu modelo de implementação...

    ![Nuvem Explorer][explorer]

    Aqui verá um conjunto de tabelas, onde os dados de cada VM está a ser armazenados. Tendo Linux e a métrica do CPU como exemplo, procure linhas mais recentes. O Explorador de nuvem do Visual Studio suporta um idioma de consulta para que possa executar uma consulta como "carimbo gt datetime'2016-02-02T21:20:00Z'" para se certificar de que obtém os eventos mais recentes (partem do pressuposto de hora está no UTC). É que os dados que vê na aí correspondem às regras de escala que configura o? No exemplo abaixo, a CPU para máquina 20 ao aumentar a 100% sobre os últimos 5 minutos...

    ![Armazenamento de tabelas][tables]

    Se os dados não estiverem lá, em seguida, implica que é o problema com a extensão de diagnóstico em execução nas VMs. Se os dados estiverem lá, implica que existe um problema com as suas regras de escala ou com o serviço de informações. Verificar o [Estado Azure](https://azure.microsoft.com/status/).

    Assim que tiver sido seguir estes passos, se ainda estiver a ter problemas de autoscale poderia experimente os fóruns no [MSDN](https://social.msdn.microsoft.com/forums/azure/home?category=windowsazureplatform%2Cazuremarketplace%2Cwindowsazureplatformctp)ou [empilhar texto em excesso](http://stackoverflow.com/questions/tagged/azure)ou inicie uma chamada de suporte. Estar preparado para partilhar o modelo e uma vista de dados de desempenho.

[audit]: ./media/virtual-machine-scale-sets-troubleshoot/image3.png
[explorer]: ./media/virtual-machine-scale-sets-troubleshoot/image1.png
[tables]: ./media/virtual-machine-scale-sets-troubleshoot/image4.png
