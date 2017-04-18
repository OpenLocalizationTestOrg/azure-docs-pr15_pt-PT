<properties
    pageTitle="Menção distribuições de Linux | Microsoft Azure"
    description="Saiba como Linux em distribuições menção Azure, incluindo as diretrizes para Ubuntu, OpenLogic, Oracle e SUSE."
    services="virtual-machines-linux"
    documentationCenter=""
    authors="szarkos"
    manager="timlt"
    editor="tysonn"
    tags="azure-service-management,azure-resource-manager"
    />

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/17/2016"
    ms.author="szark"/>



#<a name="linux-on-azure-endorsed-distributions"></a>Linux em distribuições menção Azure

> [AZURE.NOTE] Se tiver alguns minutos, contacte ajude-na melhorar a documentação do Azure Linux VM ao efetuar este [inquérito rápido](https://aka.ms/linuxdocsurvey) das suas experiências. Cada resposta ajuda-nos ajuda realizar o seu trabalho.

As imagens Linux na Galeria de Azure ou Marketplace são fornecidas por um número de parceiros, e estamos a trabalhar com vários Linux Comunidades para adicionar versões ainda mais à lista de distribuição menção. Entretanto, para as distribuições não está disponíveis a partir da galeria pode sempre trazer o-proprietário-Linux seguindo as diretrizes [desta](virtual-machines-linux-classic-create-upload-vhd.md)página.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]


## <a name="supported-distributions--versions"></a>As distribuições suportadas e versões ##

A tabela seguinte lista as distribuições Linux e as versões que são suportadas no Azure. Consulte também referir-se para o [suporte para Linux imagens no Microsoft Azure](https://support.microsoft.com/en-us/kb/2941892) para obter informações mais detalhadas.

Os controladores de lista Linux Integration Services (vertical) para Hyper-V e Azure são módulos kernel que Microsoft contribui diretamente para o montante kernel de Linux.  Os controladores de lista vertical quer são incorporados no kernel a distribuição por predefinição, ou para a mais antiga RHEL/CentOS-based distribuições estão disponíveis como transferência separada [aqui](http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409).  Consulte [Este artigo](virtual-machines-linux-create-upload-generic.md#linux-kernel-requirements) para obter mais informações sobre os controladores de lista vertical.

O agente de Linux Azure previamente já estiver instalado nas imagens da galeria do Azure e está normalmente disponível a partir do repositório de pacote a distribuição.  Código fonte pode ser encontrado no [GitHub](https://github.com/azure/walinuxagent).

Distribuição|Versão|Controladores|Agente de
---|---|---|---
CentOS por OpenLogic | CentOS 6.3 + 7.0 + | CentOS 6.3: [Transferir lista vertical](http://go.microsoft.com/fwlink/?LinkID=403033&clcid=0x409)<p>CentOS 6.4 +: No Kernel | Pacote: No [OpenLogic repo](http://olcentgbl.trafficmanager.net/openlogic/6/openlogic/x86_64/RPMS/) em "WALinuxAgent" <br/>Código fonte: [GitHub](https://github.com/Azure/WALinuxAgent)
[CoreOS](https://coreos.com/docs/running-coreos/cloud-providers/azure/) | 494.4.0+ | No Kernel | Código fonte: [GitHub](https://github.com/coreos/coreos-overlay/tree/master/app-emulation/wa-linux-agent)
Debian | Debian 7.9 +, 8.2 + | No Kernel | Pacote: no repo em "waagent" <br/>Código fonte: [GitHub](https://github.com/Azure/WALinuxAgent)
Oracle Linux | 6.4 +, 7.0 + | No Kernel | Pacote: no repo em "WALinuxAgent" <br/>Código fonte: [GitHub](http://go.microsoft.com/fwlink/p/?LinkID=250998)
Chapéu vermelho Enterprise Linux | RHEL 6,7 + 7.1 + | No Kernel|Pacote: no repo em "WALinuxAgent" <br/>Código fonte: [GitHub](https://github.com/Azure/WALinuxAgent)
SUSE Linux Enterprise | SLES 11 SP4, SLES 12 SP1 + e <p> SLES para SAP 11 SP3 + | No Kernel | Pacote: Na [Nuvem: ferramentas](https://build.opensuse.org/project/show/Cloud:Tools) repo em "python-azure-agent" <br/>Código fonte: [GitHub](http://go.microsoft.com/fwlink/p/?LinkID=250998)
openSUSE | openSUSE 13.2 + | No Kernel | Pacote: Na [Nuvem: ferramentas](https://build.opensuse.org/project/show/Cloud:Tools) repo em "python-azure-agent" <br/>Código fonte: [GitHub](https://github.com/Azure/WALinuxAgent)
Ubuntu|Ubuntu 12.04, 14.04, 16.04, 16.10 | No Kernel | Pacote: no repo em "walinuxagent" <br/>Código fonte: [GitHub](https://github.com/Azure/WALinuxAgent)


## <a name="partners"></a>Parceiros

### <a name="openlogic"></a>OpenLogic
[http://www.openlogic.com/Azure](http://www.openlogic.com/azure)

OpenLogic é um fornecedor à esquerda de soluções de abrir origem enterprise para a nuvem e o Centro de dados. OpenLogic ajuda centenas de levando enterprise através de uma vasta gama de ramos com segurança adquirir, de suporte e controlar o software de abrir origem. OpenLogic oferece suporte técnico de classe comercial e indemnizações para 600 abrir origem pacotes cópias da Comunidade de especialista OpenLogic, incluindo suporte de nível empresarial para CentOS, bem como sendo o parceiro de iniciação para fornecer imagens CentOS baseadas no Azure.

### <a name="coreos"></a>CoreOS
[https://coreos.com/Docs/Running-coreos/cloud-Providers/Azure/](https://coreos.com/docs/running-coreos/cloud-providers/azure/)

A partir do site CoreOS:

*CoreOS destina-se a fiabilidade, segurança e consistência. Em vez de instalar pacotes através do yum ou possam, CoreOS utiliza Linux contentores para gerir os seus serviços num nível mais acima de produção. Código de um serviço único e todas as dependências são fornecidas dentro de um contentor que pode ser executado num ou vários máquinas CoreOS.*


### <a name="credativ"></a>Credativ
[http://www.credativ.co.uk/credativ-blog/debian-Images-Microsoft-Azure](http://www.credativ.co.uk/credativ-blog/debian-images-microsoft-azure)

Credativ é uma consulta independentes e empresa de serviços, especializados no desenvolvimento e implementação de soluções profissionais através da utilização de software gratuito. Como especialistas abrir origem à esquerda, Credative tem reconhecimento internacional com vários departamentos de TI utilizando o seu apoio. Em conjunto com a Microsoft, Credativ está atualmente a preparar imagens Debian correspondentes para 8 Debian (Jessie) e Debian antes de 7 (Wheezy), que são concebido especialmente para executar no Azure e pode ser gerido facilmente através da plataforma. Credativ também irá suportar a manutenção de longo prazo e atualizar das imagens Debian para Azure através do seu centros de suporte de origem abrir.

### <a name="oracle"></a>Oracle
[http://www.Oracle.com/technetwork/topics/cloud/FAQ-1963009.HTML](http://www.oracle.com/technetwork/topics/cloud/faq-1963009.html)

Estratégia do Oracle é a oferecer um amplo portfolio de soluções para nuvens públicas e privadas, dando clientes escolha e flexibilidade na como eles implementarem o software Oracle nuvens Oracle, bem como outras nuvens.  Parceria do Oracle com a Microsoft permite aos clientes software Oracle nuvens recortada de públicas e privadas Microsoft com a confiança de certificação de implementação e suporte do sistema Oracle.  Compromisso do Oracle e investimento nas soluções de nuvem públicas e privadas Oracle é alterado.

### <a name="red-hat"></a>Chapéu vermelho
[http://www.Redhat.com/en/partners/Strategic-Alliance/Microsoft](http://www.redhat.com/en/partners/strategic-alliance/microsoft)

Fornecedor de parte do mundo à esquerda de soluções de abrir origem, chapéu vermelha ajuda-o a mais do que 90% das empresas revista Fortune 500 resolver os desafios da empresa, alinhar os respetivos IT e estratégias de negócio e prepare-se para o futuro da tecnologia. Chapéu vermelho faz isto, fornecendo soluções seguras através de um modelo de negócio abertos e um modelo de subscrição acessível, previsíveis.

### <a name="suse"></a>SUSE
[http://www.SUSE.com/SUSE-Linux-Enterprise-Server-on-Azure](http://www.suse.com/suse-linux-enterprise-server-on-azure)

SUSE Linux Enterprise Server no Azure é uma plataforma provas dadas que fornece fiabilidade superior e segurança para informática em nuvem. Plataforma de Linux versáteis do SUSE integra-se totalmente serviços em nuvem Azure para fornecer um ambiente em nuvem facilmente fácil. E com mais do que 9,200 aplicações certificadas mais de 1 800 fornecedores independentes de software para SUSE Linux Enterprise Server, SUSE garante implementadas confiante das cargas de trabalho em execução suportados no Centro de dados, no Azure.

### <a name="canonical"></a>Canónico
[http://www.ubuntu.com/cloud/Azure](http://www.ubuntu.com/cloud/azure)

Engenharia canónica e governação Comunidade aberta unidade sucesso do Ubuntu no cliente, servidor e nuvem computação, incluindo serviços em nuvem pessoal para consumidores. Visão do canónico de uma plataforma unificada gratuita no Ubuntu, a partir do telefone para o cloud, com uma família de interfaces coerentes para o telemóvel, tablet, TV e ambiente de trabalho, faz com que Ubuntu a primeira opção para instituições diversificadas fornecedores de nuvem público para fabricantes de electrónica consumidor e um favorito entre technologists individuais.

Com os programadores e centros de engenharia em todo o mundo, Canonical exclusivamente é posicionado para o parceiro com fabricantes de hardware, fornecedores de conteúdo e os programadores de software para trazer Ubuntu soluções no mercado, a partir de PCs aos servidores e dispositivos portáteis.

