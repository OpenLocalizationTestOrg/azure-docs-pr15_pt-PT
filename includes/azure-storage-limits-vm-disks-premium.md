**Máquina virtual discos: por limites da conta**

Recurso|Limite predefinido
---|---
Capacidade total do disco por conta|35 TB
Capacidade total instantâneo por conta|10 TB
Largura de banda máximo por conta (penetração + saída<sup>1</sup>)|< = 50 Gbps

<sup>1</sup> *Penetração* refere-se a todos os dados (pedidos) a ser enviados para uma conta de armazenamento. *Saída* refere-se a todos os dados (respostas) a ser recebidos de uma conta de armazenamento.

**Máquina virtual discos: por limites de disco**

Tipo de armazenamento de disco Premium | P10 | P 20 | P30
---|---|---|---
Tamanho do disco | Beça 128 | 512 beça | 1024 beça (1 TB)
Máximo IOPS por disco | 500 | 2300 | 5000
Débito máximo por disco | 100 MB por segundo | 150 MB por segundo | 200 MB por segundo
Número máximo de discos por conta de armazenamento | 280 | 70 | 35

**Máquina virtual discos: por VM limites**

Recurso|Limite predefinido
---|---
Máximo IOPS por VM|80.000 IOPS com GS5 VM<sup>1</sup>
Débito máximo por VM|2000 MB por s com GS5 VM<sup>1</sup>

<sup>1</sup> Consulte [Tamanho da memória virtual](../articles/virtual-machines/virtual-machines-linux-sizes.md) para limites de outros tamanhos VM. 
