<properties
   pageTitle="Infraestrutura de atualização chapéu vermelho (RHUI) | Microsoft Azure"
   description="Saiba mais sobre vermelho chapéu atualização infraestrutura (RHUI) para instâncias de vermelho chapéu Enterprise Linux a pedido no Microsoft Azure"
   services="virtual-machines-linux"
   documentationCenter=""
   authors="BorisB2015"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="10/14/2016"
   ms.author="borisb"/>

# <a name="red-hat-update-infrastructure-rhui-for-on-demand-red-hat-enterprise-linux-vms-in-azure"></a>Infraestrutura de atualização de chapéu vermelho (RHUI) para a pedido vermelho chapéu Enterprise Linux VMs no Azure

Máquinas virtuais criadas a partir de imagens de vermelho chapéu Enterprise Linux (RHEL) a pedido disponíveis no Azure Marketplace estão registadas para aceder a infraestrutura de atualização de chapéu (RHUI) de vermelho implementado no Azure.  As instâncias RHEL a pedido têm acesso para um repositório de yum regionais e poderão receber actualizações utilizarão.

Lista de repositório de yum, que é gerida pelo RHUI, está configurada na sua instância RHEL durante o aprovisionamento. Não precisa de fazer qualquer configuração adicional - executar `yum update` depois da instância RHEL está pronta para obter as atualizações mais recentes.

> [AZURE.NOTE] Infraestrutura RHUI Azure foi recentemente atualizada (Setembro de 2016) e requer alterações na configuração do seu instâncias RHEL existentes para acesso contínuo a RHUI o Azure. Consulte a secção RHUI Azure infraestrutura atualização para obter detalhes.


## <a name="rhui-azure-infrastructure-update"></a>RHUI infraestrutura Azure actualização
Partir Setembro de 2016, o Azure tem um novo conjunto de servidores de infraestrutura de atualização de chapéu de vermelho (RHUI). Nestes servidores são implementados com o [Gestor de tráfego Azure]( https://azure.microsoft.com/services/traffic-manager/) para que um único ponto final (rhui 1.micrsoft.com) pode ser utilizado pelo qualquer VM, independentemente de região. Estes também utilizar um certificado SSL que está encadeado para uma famoso autoridade de certificação (raiz Baltimore). Tornar esta atualização automática seria perigoso para alguns clientes que têm ACL ou tabelas de encaminhamento personalizadas para os servidores de atualização RHUI, pelo que esta atualização é "optar ativamente por participar no." Passos manuais de ativação para estes novos servidores estão disponíveis nesta página e um script completo para ativação de forma automática (relativamente a verificação dos passos individuais). As imagens RHEL PAYG novas no mercado Azure (versões datadas Setembro de 2016 ou posteriores) serão automaticamente apontar para os servidores de Azure RHUI novos e não requer quaisquer ações adicionais.

### <a name="the-new-azure-rhui-infrastructure-onboarding-timeline"></a>Na linha de tempo de ativação de infraestrutura de Azure RHUI nova

| Data | Nota |
| --- | --- |
|22 de Setembro de 2016 | Os servidores RHUI e instruções de instalação disponíveis para utilização. VMs implementados utilizando o novo (Setembro de 2016 datada) imagens de marketplace RHEL PAYG automaticamente irão utilizar os servidores RHUI novos, mas VMs existentes são "optar ativamente por participar no"
|1 de Novembro de 2016 | Imagens RHEL PAYG VM legadas (que utilizam os servidores de Azure RHUI antigos) serão removidas da Galeria de Azure Marketplace
|16 de Janeiro de 2017 | Os servidores de Azure RHUI antigos vai ser encerrados. Atualizar todos os seus VMs de RHEL PAYG afetado desta vez para manter o acesso ao Azure RHUI

### <a name="the-ips-for-the-new-rhui-content-delivery-servers-are"></a>IPs para os servidores de entrega de conteúdos RHUI novos são

```
# Azure Global
13.91.47.76
40.85.190.91
52.187.75.218
52.174.163.213

# Azure US Government
13.72.186.193
```

### <a name="manual-update-procedure-to-use-the-new-azure-rhui-servers"></a>Procedimento de atualização manual para utilizar os servidores de Azure RHUI novos

Transferir (através do Laço) a assinatura de chave pública

```
curl -o RPM-GPG-KEY-microsoft-azure-release https://download.microsoft.com/download/9/D/9/9d945f05-541d-494f-9977-289b3ce8e774/microsoft-sign-public.asc 
```

Verifique se a chave transferida

```
gpg --list-packets --verbose < RPM-GPG-KEY-microsoft-azure-release
```

Verifique a saída, verifique se `keyid` e `user ID packet`:

```
Version: GnuPG v1.4.7 (GNU/Linux)
:public key packet:
        version 4, algo 1, created 1446074508, expires 0
        pkey[0]: [2048 bits]
        pkey[1]: [17 bits]
        keyid: EB3E94ADBE1229CF
:user ID packet: "Microsoft (Release signing) <gpgsecurity@microsoft.com>"
:signature packet: algo 1, keyid EB3E94ADBE1229CF
        version 4, created 1446074508, md5len 0, sigclass 0x13
        digest algo 2, begin of digest 1a 9b
        hashed subpkt 2 len 4 (sig created 2015-10-28)
        hashed subpkt 27 len 1 (key flags: 03)
        hashed subpkt 11 len 5 (pref-sym-algos: 9 8 7 3 2)
        hashed subpkt 21 len 3 (pref-hash-algos: 2 8 3)
        hashed subpkt 22 len 2 (pref-zip-algos: 2 1)
        hashed subpkt 30 len 1 (features: 01)
        hashed subpkt 23 len 1 (key server preferences: 80)
        subpkt 16 len 8 (issuer key ID EB3E94ADBE1229CF)
        data: [2047 bits]
```

Instalar a chave pública

```
sudo install -o root -g root -m 644 RPM-GPG-KEY-microsoft-azure-release /etc/pki/rpm-gpg
sudo rpm --import /etc/pki/rpm-gpg/RPM-GPG-KEY-microsoft-azure-release
```

Transferir, verificar e instalar o cliente rotações/minuto

Transferência: Para RHEL 6

```
curl -o azureclient.rpm https://rhui-1.microsoft.com/pulp/repos/microsoft-azure-rhel6/rhui-azure-rhel6-2.0-2.noarch.rpm 
```

Para RHEL 7

```
curl -o azureclient.rpm https://rhui-1.microsoft.com/pulp/repos/microsoft-azure-rhel7/rhui-azure-rhel7-2.0-2.noarch.rpm  
```

Verifique se:

```
rpm -Kv azureclient.rpm
```

Verificar no resultado é essa assinatura do pacote

```
azureclient.rpm:
    Header V3 RSA/SHA256 Signature, key ID be1229cf: OK
    Header SHA1 digest: OK (927a3b548146c95a3f6c1a5d5ae52258a8859ab3)
    V3 RSA/SHA256 Signature, key ID be1229cf: OK
    MD5 digest: OK (c04ff605f82f4be8c96020bf5c23b86c)
```

Instalar o rotações/minuto

```
sudo rpm -U azureclient.rpm
```

Após a conclusão, certifique-se de que consegue aceder a formulário Azure RHUI a VM

### <a name="all-in-one-script-for-automating-the-above-task"></a>Tudo-em-um script para automatizar a tarefa acima
Utilize o seguinte script conforme necessário para automatizar a tarefa de atualização VMs afetados aos servidores do Azure RHUI novos.

```
# Download key
curl -o RPM-GPG-KEY-microsoft-azure-release https://download.microsoft.com/download/9/D/9/9d945f05-541d-494f-9977-289b3ce8e774/microsoft-sign-public.asc 

# Validate key
if ! gpg --list-packets --verbose < RPM-GPG-KEY-microsoft-azure-release | grep -q "keyid: EB3E94ADBE1229CF"; then
    echo "Keyfile azure.asc NOT valid. Exiting."
    exit 1
fi

# Install Key
sudo install -o root -g root -m 644 RPM-GPG-KEY-microsoft-azure-release /etc/pki/rpm-gpg
sudo rpm --import /etc/pki/rpm-gpg/RPM-GPG-KEY-microsoft-azure-release

# Download RPM package
if grep -q "release 7" /etc/redhat-release; then
    ver=7
elif  grep -q "release 6" /etc/redhat-release; then
    ver=6
else
    echo "Version not supported, exiting"
    exit 1
fi

url=https://rhui-1.microsoft.com/pulp/repos/microsoft-azure-rhel$ver/rhui-azure-rhel$ver-2.0-2.noarch.rpm
curl -o azureclient.rpm "$url"

# Verify package
if ! rpm -Kv azureclient.rpm | grep -q "key ID be1229cf: OK"; then
    echo "RPM failed validation ($url)"
    exit 1
fi

# Install package
sudo rpm -U azureclient.rpm
```

## <a name="rhui-overview"></a>Descrição geral RHUI
[Infraestrutura de atualização de chapéu vermelho](https://access.redhat.com/products/red-hat-update-infrastructure) oferece uma solução altamente dimensionável para gerir conteúdos de repositório yum de instâncias de nuvem vermelho chapéu Enterprise Linux que são alojados por fornecedores de certificação chapéu vermelho na nuvem. Com base no projeto papel montante, RHUI permite fornecedores de nuvem para localmente aproximadamente alojado vermelho chapéu repositório de conteúdo, criar repositórios personalizados com os seus próprios conteúdo e disponibilizar esses repositórios a um grupo grande de utilizadores finais através de um sistema de balanceamento de carga de entrega de conteúdos.

## <a name="regions-where-rhui-is-available"></a>Regiões onde o RHUI está disponível
RHUI está disponível em todas as regiões onde imagens do RHEL a pedido estão disponíveis. Inclui atualmente públicas todas as regiões listadas na página do [dashboard de estado Azure](https://azure.microsoft.com/status/) e regiões Governo do Azure-nos. Acesso a RHUI VMs aprovisionado a partir do imagens do RHEL a pedido é incluído no seu preço. Disponibilidade de adicionais regionais/nacional nuvem será atualizada como podemos expandir disponibilidade do RHEL a pedido no futuro.

> [AZURE.NOTE] Acesso à alojado no Azure RHUI está limitado a VMs dentro de [intervalos IP de centro de dados do Microsoft Azure](https://www.microsoft.com/download/details.aspx?id=41653).

## <a name="get-updates-from-another-update-repository"></a>Obter atualizações de outro repositório de actualização

Se precisar de obter atualizações a partir de um repositório de actualização diferente (em vez de alojado no Azure RHUI) terá de anular o registo do seu instâncias a partir da RHUI e registe novamente com a infraestrutura de atualização pretendido (como vermelho chapéu satélite ou vermelho chapéu cliente Portal CDN). Terá de subscrições vermelho chapéu apropriadas para estes serviços e registo para [Vermelho chapéu o acesso à nuvem no Azure](https://access.redhat.com/ecosystem/partners/ccsp/microsoft-azure).

Para anular o registo RHUI e registar a sua seguir de infraestrutura de atualizar a seguir os passos.

1.  Editar /etc/yum.repos.d/rh-cloud.repo e alterar todos os `enabled=1` para `enabled=0`. Por exemplo:

        # sed -i 's/enabled=1/enabled=0/g' /etc/yum.repos.d/rh-cloud.repo

2.  Editar /etc/yum/pluginconf.d/rhnplugin.conf e alterar `enabled=0` para `enabled=1`.
3.  Em seguida, registe-se com a infraestrutura pretendida, como o Portal de cliente do chapéu vermelho. Siga guia da solução vermelho chapéu sobre [como registar e subscrever um sistema para o Portal de cliente chapéu vermelho](https://access.redhat.com/solutions/253273).

> [AZURE.NOTE] Acesso à RHUI alojado no Azure é incluído no preço de imagem RHEL repartição (PAYG). Anular o registo de uma VM de RHEL PAYG a partir do RHUI alojado no Azure não converter a máquina virtual trazer o-proprietário-licenças (BYOL) tipo VM, por conseguinte, pode ser assumindo taxas duplas se registar a mesma VM com outra fonte de atualizações. 
> 
> Se for necessário utilizar uma infraestrutura de atualização de forma consistente alojado no Azure RHUI considere criar e implementar a suas próprias imagens (tipo de BYOL) conforme descrito no artigo [criar e carregar chapéu vermelho baseado máquina de virtual para Azure](virtual-machines-linux-redhat-create-upload-vhd.md) .

## <a name="next-steps"></a>Próximos passos
Para criar uma vermelho chapéu Enterprise Linux VM a partir de imagem do Azure Marketplace repartição e tirar partido alojado no Azure RHUI aceda ao [Azure Marketplace](https://azure.microsoft.com/marketplace/partners/redhat/). Que poderá utilizar `yum update` na sua instância RHEL sem qualquer configuração adicionais.