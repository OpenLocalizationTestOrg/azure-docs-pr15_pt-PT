<properties
    pageTitle="Utilizar aplicações da App-V com Azure RemoteApp | Microsoft Azure"
    description="Saiba como utilizar aplicações da App-V no Azure RemoteApp."
    services="remoteapp"
    documentationCenter=""
    authors="ericorman"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/15/2016" 
    ms.author="elizapo" />



# <a name="using-app-v-apps-in-azure-remoteapp"></a>Utilizar aplicações do aplicação V Azure RemoteApp

> [AZURE.IMPORTANT]
> Está a ser descontinuada Azure RemoteApp. Leia o [anúncio](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.

Pode utilizar aplicações da App-V numa coleção de híbrido RemoteApp do Azure, solicitar associação de domínio.

Antes de começar, certifique-se instalar o cliente de aplicação V 5.1 com as atualizações mais recentes. Terá de criar uma [imagem personalizada](remoteapp-create-custom-image.md) que inclui o cliente de aplicação V.  

É fácil de utilizar a sua infraestrutura de aplicação V existente com o Azure RemoteApp. Uma vez que é implementada uma coleção de híbrido para um VNET Azure que tenha acesso ao seu controlador de domínio e os VMs são domínio aderido, pode tirar partido seu existentes aplicação v infraestrutura e implementação métodos para aplicação de V aplicação anfitriã easyily no Azure RemoteApp. Aqui estão algumas considerações que deve ter em mente com base no tipo de implementação de aplicação V que possui atualmente:

| Opções de configuração |                       | Positivo                                                               | Negativo                                                                                              |
|-----------------------|-----------------------|------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------|
| Método de entrega       | Transmissão (a pedido) | Aplicação está sempre as mais recentes e doces                                     | Latência da primeira vez                                                                                    |
|                       | Instalados               | Mais rápido; aplicação já se encontra na VM                              | Aumento do tamanho do - demora o espaço de imagem (limite 127 GB)                                                           |
| Armazenamento de localização de aplicação  | Conteúdo partilhado        | Aplicação é executado no memória da instância RemoteApp do Azure                         | Eats memória e boa ligação ao servidor (ficheiro) de streaming onde reside a aplicação                      |
|                       | Disco (em cache)         | Execução rápida. Aplicação não dependente de disponibilidade de origem de conteúdo | Aumento do tamanho do - demora o espaço de imagem (limite 127 GB)                                                           |
| Filtragem             | Utilizador                  | Necessita de infraestrutura de V aplicação completo autónomo                          |                                                                                                       |
|                       | Global (máquina)      |  Publicar previamente ou utilizar a publicação de destino server                         |  Necessita de atualizar a sua imagem Azure se pretende atualizar a aplicação (enorme). Ocupa algum espaço na imagem. |

 Depois de criar a imagem personalizada e a sua coleção de híbrido, publicar a sua aplicação, atribuir utilizadores e desfrutar as aplicações da App-V existentes alojadas no Azure RemoteApp entregue a qualquer dispositivo em qualquer lugar.
