<properties
    pageTitle="Implementar pilha Azure | Microsoft Azure"
    description="Implementar Azure pilha."
    services="azure-stack"
    documentationCenter=""
    authors="ErikjeMS"
    manager="byronr"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/29/2016"
    ms.author="erikje"/>

# <a name="redeploy-azure-stack"></a>Implementar pilha Azure

Para implementar pilha Azure, tem de iniciar ao longo de raiz conforme descrito abaixo.

## <a name="steps-to-redeploy-azure-stack"></a>Passos para implementar o Azure pilha

1. Reiniciar o computador anfitrião do para o sistema operativo original (instalado para metal partes). Esta não é a predefinição no menu de arranque, para que tem de utilizar KVM ou consola local para selecioná-lo durante o reinício (durante a configuração, com nome de "a partir do VHD de arranque" SO para "AzureStack TP2", isto irá ajudar a identificar qual é o sistema operativo).

    Não precisa de remover a entrada de arranque existentes (o novo suporte script "PrepareBootFromVHD.ps1" tratará de que por si.)

2. Se não possui KVM ou gostaria de escolher o SO de arranque antes de reiniciar o computador:
    
    1. Localize o script.\BootMenuNoKVM.ps1. Este ficheiro está disponível com os scripts de suporte fornecidos juntamente com esta compilação.
    
    2. Execute o script com privilégios elevados. Selecione o nome do SO Original do anfitrião. Isto irá arrancar anfitrião do para o anfitrião original SO sem que exijam acesso KVM.
    
    3. Quando o script estiver concluído lhe-á pedido para confirmar o reinício.

    - Se existirem outros utilizadores tem sessão iniciados, este comando irá falhar.

    - Apenas execute o seguinte comando: reiniciar o computador-forçar 
 
3. Elimine o ficheiro de CloudBuilder.vhdx que foi utilizado como parte da implementação anterior.

    Não tem de eliminar o agrupamento de armazenamento existente a partir de implementação de TP2 anterior. O script de implementação Deteta e limpa a existente, em seguida, cria novas.

5. Implementar a partir de copiar uma nova cópia do CloudBuilder.vhdx, arranque-lo, etc.

## <a name="next-steps"></a>Próximos passos

[Ligar a pilha Azure](azure-stack-connect-azure-stack.md)
