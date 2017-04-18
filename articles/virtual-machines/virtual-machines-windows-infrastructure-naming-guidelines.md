<properties
    pageTitle="Infraestrutura de nomenclatura diretrizes | Microsoft Azure"
    description="Saiba mais sobre as diretrizes de estrutura e implementação chaves para serviços de infraestrutura Azure de atribuição de nomes."
    documentationCenter=""
    services="virtual-machines-windows"
    authors="iainfoulds"
    manager="timlt"
    editor=""
    tags="azure-resource-manager"/>

<tags
    ms.service="virtual-machines-windows"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-windows"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/08/2016"
    ms.author="iainfou"/>

# <a name="infrastructure-naming-guidelines"></a>Diretrizes de nomenclatura de infraestrutura

[AZURE.INCLUDE [virtual-machines-windows-infrastructure-guidelines-intro](../../includes/virtual-machines-windows-infrastructure-guidelines-intro.md)] 

Este artigo foca-se em Noções básicas sobre como aproximar convenções de nomenclatura para todos os recursos Azure vários criar um conjunto de recursos lógico e identificável facilmente ao longo do seu ambiente.

## <a name="implementation-guidelines-for-naming-conventions"></a>Diretrizes de implementação para convenções de nomenclatura

Decisões:

- Quais são as convenções de atribuição de recursos Azure?

Tarefas:

- Defina apõe para utilizar nos seus recursos para manter a consistência.
- Defina nomes de conta de armazenamento tendo em conta o requisito de registo para que possam ser globalmente exclusivos.
- A Convenção de nomenclatura para ser utilizado e distribuir para todas as partes envolvidas assegurar a consistência entre implementações do documento.

## <a name="naming-conventions"></a>Convenções de nomenclatura

Que deve ter uma boa Convenção de nomenclatura num local antes de criar nada no Azure. Convenção de nomenclatura garante que todos os recursos tem um nome previsível, que ajuda a reduzir os encargos administrativos associados à gestão esses recursos.

Pode escolher seguir um conjunto específico de convenções de nomenclatura definido para toda a sua empresa ou para uma conta ou de subscrição Azure específico. Apesar de ser mais fácil para indivíduos no âmbito das organizações estabelecer regras implícitas quando trabalhar com recursos Azure, quando precisa de uma equipa para trabalhar num projeto no Azure, esse modelo não dimensionar bem.

Concordem sobre um conjunto de convenções de nomenclatura adiantado. Existem algumas considerações relativas ao convenções de nomenclatura que cortar ao longo que conjuntos de regras.

## <a name="affixes"></a>Apõe

Como ver para definir uma convenção de nomenclatura, tomar uma decisão vem Control se a apor está em:

- O início do nome (prefixo)
- O fim do nome (sufixo)

Por exemplo, aqui estão dois nomes possíveis para um grupo de recursos utilizando o `rg` afixar:

- RG-Web App (prefixo)
- Web App-Rg (sufixo)

Apõe pode consultar a aspetos diferentes que descrevem os recursos específicos. A tabela seguinte mostra alguns exemplos normalmente utilizados.

| Altura                               | Exemplos                                                               | Notas                                                                                                      |
|:-------------------------------------|:-----------------------------------------------------------------------|:-----------------------------------------------------------------------------------------------------------|
| Ambiente                          | Dev Center, stg, prod                                                         | Consoante o objetivo e o nome de cada ambiente.                                                     |
| Localização                             | usw (Ocidental US), utilize (Leste dos EUA 2)                                         | Dependendo da região do Centro de dados ou a região da organização.                               |
| Componente Azure, produto ou serviço | RG para o grupo de recursos, VNet para a rede virtual                        | Consoante o produto para o qual o recurso fornece suporte.                                          |
| Função                                 | SQL, ora, sp, iis                                                      | Consoante o papel da máquina virtual.                                                              |
| Instância                             | 01, 02, 03, etc.                                                       | Para recursos que tenham mais de uma instância. Por exemplo, carregue os servidores de web desequilibradas num serviço de nuvem. |


Quando estabelece convenções de nomenclatura, certifique-se de que estes indicar claramente quais apõe a utilizar para cada tipo de recurso e, em que posição (sufixo de vs prefixo).

## <a name="dates"></a>Datas

Muitas vezes é importante determinar a data de criação de nomes de um recurso. Recomendamos que o formato de data AAAAMMDD. Este formato garante que não só é registada a data completa, mas também que duas os recursos cujos nomes diferem apenas na data é ordenada por ordem alfabética e por ordem cronológica ao mesmo tempo.

## <a name="naming-resources"></a>Atribuição de nomes de recursos

Definir cada tipo de recurso a Convenção de nomenclatura, que deve ter regras que definem como atribuir nomes a cada recurso que é criado. Estas regras deve ser aplicada a todos os tipos de recursos, por exemplo:

- Subscrições
- Contas
- Contas de armazenamento
- Redes virtuais
- Sub-redes
- Conjuntos de disponibilidade
- Grupos de recursos
- Máquinas virtuais
- Pontos finais
- Grupos de segurança de rede
- Funções

Para se certificar de que o nome fornece informações suficientes para determinar a qual o recurso que se refere, deve utilizar nomes descritivos.

## <a name="computer-names"></a>Nomes de computador

Quando cria uma máquina de virtual (VM), Microsoft Azure requer um VM nome de até 15 carateres que é utilizado para o nome do recurso. Azure utiliza o mesmo nome para o sistema operativo instalado na VM. No entanto, os seguintes nomes poderão não estar sempre o mesmo.

No caso de uma VM é criada a partir de um ficheiro de imagem. vhd que já contenha num sistema operativo, o nome VM no Azure pode ser diferentes do nome do computador a VM sistema operativo. Esta situação pode adicionar um grau de dificuldade a gestão de VM, que, por conseguinte, não recomendamos. Atribua o recurso Azure VM o mesmo nome que o nome do computador que atribui a do sistema operativo que VM.

Recomendamos que o nome do Azure VM é o mesmo que o nome do computador sistema operativo subjacente.

## <a name="storage-account-names"></a>Nomes de conta de armazenamento

As contas do armazenamento têm regras especiais que regem os seus nomes. Só pode utilizar letras em minúsculas e números. Para mais informações, consulte [criar uma conta de armazenamento](../storage/storage-create-storage-account.md#create-a-storage-account) . Para além disso, o nome da conta de armazenamento, juntamente com core.windows.net, deve ser um nome DNS globalmente válido e único. Por exemplo, se a conta de armazenamento é chamada mystorageaccount, os nomes de DNS resultantes seguintes devem ser únicos:

- mystorageaccount.blob.Core.Windows.NET
- mystorageaccount.TABLE.Core.Windows.NET
- mystorageaccount.Queue.Core.Windows.NET


## <a name="next-steps"></a>Próximos passos
[AZURE.INCLUDE [virtual-machines-windows-infrastructure-guidelines-next-steps](../../includes/virtual-machines-windows-infrastructure-guidelines-next-steps.md)] 