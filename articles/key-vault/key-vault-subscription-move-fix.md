<properties
    pageTitle="Alterar o ID de inquilino do cofre chave depois de mover uma subscrição | Microsoft Azure"
    description="Saiba como mudar o ID do inquilino para uma chave cofre depois de uma subscrição é movida para um inquilino diferente"
    services="key-vault"
    documentationCenter=""
    authors="amitbapat"
    manager="mbaldwin"
    tags="azure-resource-manager"/>

<tags
    ms.service="key-vault"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="hero-article"
    ms.date="09/13/2016"
    ms.author="ambapat"/>

# <a name="change-a-key-vault-tenant-id-after-a-subscription-move"></a>Alterar um ID de inquilino do cofre chave depois de mover uma subscrição
### <a name="q-my-subscription-was-moved-from-tenant-a-to-tenant-b-how-do-i-change-the-tenant-id-for-my-existing-key-vault-and-set-correct-acls-for-principals-in-tenant-b"></a>P: a minha subscrição foi movida do inquilino A para o inquilino B. Como alterar o ID de inquilino para minha cofre chave existente e defina ACL corretas para os principais no inquilino B?

Quando cria um novo cofre chave numa subscrição, está associada automaticamente para o ID de inquilino predefinido Azure Active Directory para esse subscrição. Todas as entradas de política de acesso também estão associadas a este ID inquilino. Quando mover a sua subscrição Azure a partir de inquilino respostas ao inquilino B, seu cofres teclas existentes são inacessíveis pelos principais (utilizadores e aplicações) no inquilino B. Para corrigir este problema, terá de:

- Alterar o ID do inquilino associados com todos os existentes chaves cofres nesta subscrição ao inquilino B.
- Remova todas as entradas de política de acesso existente.
- Adicionar novas entradas de política de acesso que estão associadas a inquilino B.

Por exemplo, se tiver cofre chave 'myvault' numa subscrição que tenha sido movida a partir do inquilino A para inquilino B, eis como alterar o ID do inquilino deste cofre chave e remover as políticas de acesso antigo.

<pre>
$vaultResourceId = (get-AzureRmKeyVault - VaultName myvault). ResourceId $vault = Get-AzureRmResource – ResourceId $vaultResourceId - ExpandProperties $vault. Properties.TenantId = (Get-AzureRmContext). Tenant.TenantId $vault. Properties.AccessPolicies = @() conjunto AzureRmResource - ResourceId $vaultResourceId-propriedades $vault. Propriedades
</pre>

Uma vez que foi deste cofre no inquilino respostas antes de mover, o valor original do **$vault. Properties.TenantId** é A inquilino, enquanto **(Get-AzureRmContext). Tenant.TenantId** é de inquilinos B.

Agora que Cofre está associado com o ID de inquilino correto e antigo entradas de política de acesso são removidas, defina novo acesso política as entradas com [AzureRmKeyVaultAccessPolicy conjunto](https://msdn.microsoft.com/library/mt603625.aspx).

## <a name="next-steps"></a>Próximos passos

Se tiver questões sobre Azure chave cofre, visite os [Fóruns do Azure chave cofre](https://social.msdn.microsoft.com/forums/azure/home?forum=AzureKeyVault).
