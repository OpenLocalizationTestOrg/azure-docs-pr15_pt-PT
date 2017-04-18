<properties
    pageTitle="FAQ dos serviços na nuvem | Microsoft Azure"
    description="Perguntas mais frequentes sobre serviços em nuvem."
    services="cloud-services"
    documentationCenter=""
    authors="Thraka"
    manager="timlt"
    editor=""/>

<tags
    ms.service="cloud-services"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/19/2016"
    ms.author="adegeo"/>

# <a name="cloud-services-faq"></a>FAQ dos serviços na nuvem
Este artigo responde a algumas perguntas mais frequentes sobre os serviços do Microsoft Azure na nuvem. Também pode visite as [FAQ de suporte do Azure](http://go.microsoft.com/fwlink/?LinkID=185083) para Azure preços e suporte obter informações gerais. Também pode consultar a [página de tamanho da memória virtual serviços na nuvem](cloud-services-sizes-specs.md) para informações sobre o tamanho.

## <a name="certificates"></a>Certificados

### <a name="where-should-i-install-my-certificate"></a>Onde posso instalar o meu certificado?

- **Meu**  
Certificado de aplicação com chave privada (\*. pfx, \*. p12).

- **AC**  
Todos os seus certificados intermédios vá neste arquivo (política e Sub AC).

- **RAIZ**  
Armazenar AC de raiz, para que o seu certificado de AC raiz principal deve ir aqui.

### <a name="i-cant-remove-expired-certificate"></a>Não consigo remover certificado expirado

Azure impede que remover um certificado enquanto está a ser utilizado. Tem de eliminar a implementação que utiliza o certificado, ou atualizar a implementação com um certificado renovado ou diferente.

### <a name="delete-an-expired-certificate"></a>Eliminar um certificado expirado

Desde que não estiver a utilizar o certificado, pode utilizar o cmdlet do PowerShell [AzureCertificate remover](https://msdn.microsoft.com/library/azure/mt589145.aspx) para remover um certificado.

### <a name="i-have-expired-certificates-named-windows-azure-service-management-for-extensions"></a>Posso ter expirado denominados gestão de serviço do Windows Azure para as extensões de certificados

Estes certificados são criados sempre que uma extensão é adicionada o serviço na nuvem como a extensão de ambiente de trabalho remoto. Estes certificados só são utilizados para encriptar e desencriptar a configuração privada da extensão. Não importa se estes certificados expirarem. A data de expiração não está selecionada.

### <a name="certificates-i-have-deleted-keep-reappearing"></a>Eliminei certificados que posso ter eliminado

Estes eliminei provavelmente devido a uma ferramenta que estiver a utilizar, tal como o Visual Studio. Sempre que restabelecer a ligação com uma ferramenta que está a utilizar um certificado, novamente a ser carregado para Azure.

### <a name="my-certificates-keep-disappearing"></a>Manterem desaparecer meus certificados

Quando a instância de máquina virtual Reciclagens da, todas as alterações locais são perdidas. Utilize uma [tarefa de arranque](cloud-services-startup-tasks.md) para instalar certificados para a máquina virtual sempre que a função for iniciado.

### <a name="i-cannot-find-my-management-certificates-in-the-portal"></a>Não consigo encontrar meus certificados de gestão no portal

[Certificados de gestão da](..\azure-api-management-certs.md) só estão disponíveis no Portal clássica do Azure. O portal do Azure atual não utilizar certificados de gestão. 

### <a name="how-can-i-disable-a-management-certificate"></a>Como posso desativar um certificado de gestão?

Não pode ser desativada [certificados de gestão](..\azure-api-management-certs.md) . Eliminá-los através do Portal clássica do Azure quando não pretende que já utilizado.

### <a name="how-do-i-create-an-ssl-certificate-for-a-specific-ip-address"></a>Como posso criar um certificado SSL para um endereço IP específico?

Siga as indicações para [criar um tutorial de certificado](cloud-services-certs-create.md). Utilize o endereço IP como o nome de DNS.

## <a name="security"></a>Segurança

### <a name="disable-ssl-30"></a>Desativar SSL 3.0

Para desativar SSL 3.0 e utilizar segurança TLS, criar uma tarefa de arranque que é documentada esta mensagem de blogue: https://azure.microsoft.com/en-us/blog/how-to-disable-ssl-3-0-in-azure-websites-roles-and-virtual-machines/

## <a name="scale-a-cloud-service"></a>Escala de um serviço na nuvem

### <a name="i-cannot-scale-beyond-x-instances"></a>Não consigo dimensionar para além das X instâncias

A sua subscrição do Azure tem um limite no número de núcleos que pode utilizar. Dimensionamento não irá funcionar se tiver utilizado núcleos disponíveis. Por exemplo, se tiver um limite de 100 núcleos, isto significa que pode ter 100 A1 dimensionada máquina virtual instâncias do seu serviço de nuvem, ou 50 A2 dimensionada instâncias de máquina virtual.

## <a name="troubleshooting"></a>Resolução de problemas

### <a name="i-cant-reserve-an-ip-in-a-multi-vip-cloud-service"></a>Posso não é possível reservar um IP num serviço de nuvem multi VIP

Em primeiro lugar, certifique-se de que a instância de máquina virtual que está a tentar reservar IP para está ativada. Em segundo lugar, certifique-se de que estiver a utilizar o IPs reservadas se preocupar em implementações de teste e de produção. **Não** altere as definições enquanto a implementação está a atualizar.

