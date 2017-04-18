<properties
    pageTitle="Perguntas mais frequentes do Azure pilha | Microsoft Azure"
    description="Pilha Azure perguntas mais frequentes."
    services="azure-stack"
    documentationCenter=""
    authors="HeathL17"
    manager="byronr"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/13/2016"
    ms.author="helaw"/>

# <a name="frequently-asked-questions-for-azure-stack"></a>Perguntas mais frequentes do Azure pilha

## <a name="deployment"></a>Implementação

### <a name="do-i-need-to-format-my-data-disks-before-starting-or-restarting-an-installation"></a>É necessário formatar o meu discos de dados antes de iniciar ou reiniciar uma instalação?

Discos devem estar no formato não processado. Se reinstalar o sistema operativo, poderá ter verificar se o agrupamento de armazenamento antigo ainda estiver presente e eliminar através dos seguintes passos:

1. Abra o Gestor de servidor.
2. Selecione agrupamentos de armazenamento.
3. Consulte o artigo se existir um agrupamento de armazenamento.
4. Com o botão direito **agrupamento de armazenamento** se listados e ativar leitura / escrita.
5. Botão direito do rato **disco rígido Virtual** (canto inferior esquerdo) e selecione eliminar.
6. **Agrupamento de armazenamento** com o botão direito e clique em eliminar.
7. Inicie o script Azure pilha novamente e certifique-se de que transmite a verificação de disco.

Opcionalmente, pode ser utilizado o script seguinte:

```PowerShell
$pools = Get-StoragePool -IsPrimordial $False -ErrorVariable Err -ErrorAction SilentlyContinue
if ($pools -ne $null) {
  $pools| Set-StoragePool -IsReadOnly $False -ErrorVariable Err -ErrorAction SilentlyContinue
  $pools = Get-StoragePool -IsPrimordial $False -ErrorVariable Err -ErrorAction SilentlyContinue
  $pools | Get-VirtualDisk | Remove-VirtualDisk -Confirm:$False
  $pools | Remove-StoragePool -Confirm:$False
}
```

### <a name="can-i-use-all-ssd-disks-for-the-storage-pool-in-the-poc-installation"></a>Pode utilizar todos os discos SSD para o agrupamento de armazenamento na instalação conceito?

Esta configuração não é suportada neste lançamento.  Para obter mais informações, consulte o [Guia de requisitos](azure-stack-deploy.md) para obter mais informações.

### <a name="can-i-use-nvme-data-disks-for-the-microsoft-azure-stack-poc"></a>Pode utilizar NVMe discos de dados para o conceito de pilha do Microsoft Azure?

Enquanto armazenamento espaços direta suportar NVMe discos, Azure pilha suporta apenas um subconjunto dos tipos de unidade possíveis e combinações possíveis para armazenamento espaços direta. 

### <a name="how-can-i-reinstall-azure-stack"></a>Como posso reinstalar o Azure pilha?
Pode seguir os passos no [Guia de nova implementação](azure-stack-redeploy.md).  

## <a name="tenant"></a>Inquilino

### <a name="can-i-deploy-my-own-images-as-a-tenant"></a>Pode implementar meus próprios imagens como um inquilino?

Sim, tal como faria no Azure, um inquilino pode carregar imagens na pilha de Azure, além de utilizar as imagens a partir do administrador do serviço. Para obter uma descrição geral, consulte o artigo [Adicionar uma imagem VM](azure-stack-add-vm-image.md). 

## <a name="testing"></a>Testes

### <a name="can-i-use-nested-virtualization-to-test-the-microsoft-azure-stack-poc"></a>Pode utilizar as funções aninhadas virtualização para testar o conceito de pilha do Microsoft Azure?

Virtualização aninhada não é suportada nem testada com 2 de pré-visualização técnica do Azure pilha.

## <a name="virtual-machines"></a>Máquinas virtuais

### <a name="does-azure-stack-support-dynamic-disks-for-virtual-machines"></a>Pilha de Azure suporta discos dinâmicos para máquinas virtuais?

Pilha Azure não suporta discos dinâmicos.

## <a name="next-steps"></a>Próximos passos

[Resolução de problemas](azure-stack-troubleshooting.md)
