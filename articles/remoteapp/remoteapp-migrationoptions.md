
<properties 
    pageTitle="Opções da migração de RemoteApp do Azure | Microsoft Azure" 
    description="Saiba mais sobre as opções da migração de RemoteApp do Azure." 
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
    ms.date="10/06/2016" 
    ms.author="elizapo" />

# <a name="options-for-migrating-out-of-azure-remoteapp"></a>Opções da migração de RemoteApp do Azure

> [AZURE.IMPORTANT]
> Está a ser descontinuada Azure RemoteApp. Leia o [anúncio](https://go.microsoft.com/fwlink/?linkid=821148) para obter detalhes.

Se foram interrompidas é utilizar RemoteApp do Azure devido a [anúncio extinção](https://go.microsoft.com/fwlink/?linkid=821148) ou uma vez que terminar a avaliação, precisar de migrar fora RemoteApp do Azure para outro serviço de aplicação. Existem duas abordagens diferentes para migrar: uma personalizada geridos (frequentemente chamado infraestrutura como um serviço [IaaS]) implementação ou uma totalmente gerida (muitas vezes denominada plataforma como um serviço) ou Software tal como um serviço [PaaS/SaaS] oferta. 

Gestão personalizada IaaS é uma implementação faça mesmo que é gerida, operada e o proprietário dos mesmos, diretamente implementados em máquinas virtuais (VMs) ou sistemas físicos. Na outra extremidade, um SaaS/PaaS totalmente geridas perguntar se é mais como RemoteApp do Azure - um parceiro fornece uma camada de serviço na parte superior de uma solução de comunicação remota processa operacionais e manutenção, enquanto o utilizador, como o cliente, efetue algumas gestão de imagem e de aplicação.

Continue a ler para obter mais informações, incluindo exemplos sobre as diferentes opções de alojamento.    

## <a name="self-managed-iaas-solutions"></a>Soluções de (IaaS) geridas personalizada

### <a name="rds-on-iaas"></a>**RDS no IaaS** 
Pode implementar uma nativa sessão implementação baseada em serviços de ambiente de trabalho remoto (no Windows Server) utilizar RemoteApp ou ambientes de trabalho no local ou num ambiente alojado (como no Azure VMs). RDS no IaaS implementações são mais adequadas para clientes já familiarizados com e que têm existente conhecimentos técnicos com implementações RDS. 

> [AZURE.NOTE]
> Precisa de licenciamento em Volume com Software Assurance (SA) para licenças de acesso de cliente RDS para utilizar esta opção de implementação.

Implementar RDS no Azure VMs é ainda mais fácil se utilizar implementação e a aplicação de patches modelos (obter uma [Descrição geral](https://blogs.technet.microsoft.com/enterprisemobility/2015/07/13/azure-resource-manager-template-for-rds-deployment/) e, em seguida, [aceda a obtê-lo](https://aka.ms/rdautomation)). Pode obter as mesmas capacidades de dimensionamento flexível com recursos de modelo de implementação clássica Azure (não recursos Azure modelo de recurso) dentro RemoteApp do Azure utilizando o [script de escala automática](https://gallery.technet.microsoft.com/scriptcenter/Automatic-Scaling-of-9b4f5e76), apesar de existirem mais personalizações e configurações. Quando implementar RDS no Azure VMs, suporte é fornecido através de [De suporte Azure](https://azure.microsoft.com/support/plans/), o mesmo profissionais de suporte que são suportadas com RemoteApp do Azure. Pode obter custar as previsões com base na sua utilização existente ao contactar o [Suporte do Azure](https://azure.microsoft.com/support/plans/)ou pode efetuar cálculos-se utilizando um breve ser lançadas Calculadora de custo.  Além disso, com o N-série VMs (atualmente em privado pré-visualização) pode adicionar vGPU - ouvir mais sobre a adição de vGPU e sobre como para [combine melhorias RDS no Windows Server 2016](https://myignite.microsoft.com/videos/2794) no nosso sessão Ignite.   

Temos guias de implementação do passo a passo para o [Windows Server 2012 R2](http://aka.ms/rdsonazure) e [Windows Server 2016](http://aka.ms/rdsonazure2016) ajudar com a sua implementação. Consulte o artigo do [blogue de ambiente de trabalho remoto](https://blogs.technet.microsoft.com/enterprisemobility/?product=windows-server-remote-desktop-services) para obter as notícias mais recentes.
 
### <a name="citrix-on-iaas"></a>**Citrix no IaaS** 
Um Citrix nativo implementação do baseada em sessões XenApp ou XenDesktop pode ser implementada no local ou dentro de um ambiente alojado (tal como no Azure VMs). 

Consulte o guia de implementação passo a passo, [Citrix XA 7.6 no Azure](http://www.citrixandmicrosoft.com/Documents/Citrix-Azure Deployment Guide-v.1.0.docx), para obter mais informações. Leia mais sobre [Citrix no Azure](http://www.citrixandmicrosoft.com/Solutions/AzureCloud.aspx), incluindo uma calculadora preço. Também pode encontrar um [contacto Citrix](http://citrix.com/English/contact/index.asp) para discutir as opções de com.

## <a name="fully-managed-paassaas-offerings"></a>Totalmente geridas ofertas de (PaaS/SaaS)

### <a name="citrix-cloud"></a>**Nuvem de Citrix** 
[Solução de nuvem existente Citrix](https://www.citrix.com/products/citrix-cloud/)idêntico ao neutros Citrix XenApp Express. Citrix oferece uma [promoção de desconto de 50%](https://www.citrix.com/blogs/2016/10/03/special-promotion-for-microsoft-azure-remoteapp-customers/) para clientes RemoteApp do Azure existentes. 

### <a name="citrix-xenapp-express-in-tech-preview"></a>**Citrix XenApp Express (na tech pré-visualização)**
[Registe-se para os respetivos Technical preview](http://now.citrix.com/remoteapp)e ver a sua [sessão Ignite](https://myignite.microsoft.com/videos/2792) (começando na minuto 20:30). XenApp Express é neutros idêntico ao Citrix nuvem exceto inclui gestão simplificada IU e outras funcionalidades que são semelhantes às RemoteApp do Azure e funcionalidades. 

Saiba mais sobre [Citrix XenApp Express](http://now.citrix.com/remoteapp).   

### <a name="citrix-service-provider-program"></a>**Programa de fornecedor de serviços de Citrix** 
O programa de fornecedor de serviço Citrix torna mais fácil para fornecedores de serviço para a prestação simplificar de nuvem virtual informática para SMB, os serviços que pretendem num modelo fácil e repartição a oferecê-los. Fornecedores de serviços de Citrix aumentar os seus negócios Microsoft SPLA e expanda aos seus investimentos de plataforma RDS com qualquer dispositivo, aceder em qualquer lugar, o suporte técnico da aplicação mais abrangente, uma experiência avançada, segurança adicional e escalabilidade maior. Por sua vez, fornecedores de serviços de Citrix apelar subscritores mais, aumente a satisfação do cliente e reduzir os seus custos operacionais. [Saiba mais](http://www.citrix.com/products/service-providers.html) ou [encontre um parceiro](https://www.citrix.com/buy/partnerlocator.html).

### <a name="microsoft-hosted-service-provider"></a>**Microsoft alojado fornecedor de serviços** 
Parceiros de alojamento normalmente oferecem totalmente geridas alojado ambiente de trabalho do Windows e serviço de aplicação, que pode incluir a gerir o Azure recursos, sistemas operativos, aplicações e utilizar o parceiro de suporte técnico do licenciamento acordos com a Microsoft e outros fornecedores de software juntamente com a ser um contrato de licença do fornecedor de serviço para permitir que revendem licença de acesso do assinante (SAL). As seguintes informações fornecem detalhes e informações de contacto de alguns dos responsáveis especializados em assistir clientes com a sua migração RemoteApp do Azure. Consulte [a lista atual de fornecedores de serviço alojado](http://aka.ms/rdsonazurecertified) que concluiu RDS no IaaS avaliação e o caminho de formação.  

#### <a name="aspex"></a>**ASPEX**
[ASPEX](http://www.aspex.be/en) especializada em fabricantes independentes de software na transição para a nuvem e ISV' está à procura para optimizar as suas configurações de nuvem atual. ASPEX oferece uma vasta gama de serviços geridos, devops e serviços de consultoria.  

Localização principal: Antuérpia, Bélgica

Região de operação: Europa Ocidental

Estado do parceiro: [prata](https://partnercenter.microsoft.com/pcv/solution-providers/aspex_9397f5dd-ebdd-405b-b926-19a5bda61f7a/cfe00bac-ea36-4591-a60b-ec001c4c3dff)

Fornecedor de serviços da Microsoft Cloud: Sim

Oferecer soluções de RemoteApp e ambiente de trabalho com base na sessão: Sim, ambos

Soluções de migração do Azure RemoteApp: Sim, [Saiba mais](https://www.aspex.be/en/azure-remote-apps)

**Contacto:**

- Telefone: +3232202198
- Mail:[info@aspex.be](mailto:info@aspex.be)
- Web: [http://cloud.aspex.be/contact-ara-0](http://cloud.aspex.be/contact-ara-0)

#### <a name="conexlink-platform-name-mycloudit"></a>**Conexlink (nome de plataforma: MyCloudIT)**
[MyCloudIT](http://www.mycloudit.com) é uma plataforma de automatização para empresas IT simplificar, otimizar e dimensionar a migração e a entrega de ambientes de trabalho remotos, aplicações remotas e infraestrutura na nuvem Microsoft Azure. 

A plataforma MyCloudIT reduz o tempo de implementação de 95%, Azure de custos por 30% e passa infraestrutura de TI inteira do seu cliente para da nuvem numa questão de alguns teclas premidas. Parceiros podem agora gerir clientes a partir de um dashboard global, os utilizadores do serviço de fim em todo o mundo nunca antes em seguida, aumentar receitas sem adicionar sobrecarga adicional ou extensa formação Azure.  

Localização principal: Dallas, TX, EUA

Região de operação: em todo o mundo

Estado do parceiro: [ouro](https://partnercenter.microsoft.com/pcv/solution-providers/conexlink_4298787366/843036_1?k=Conexlink)

Fornecedor de serviços da Microsoft Cloud: Sim

Oferecer soluções de RemoteApp e ambiente de trabalho com base na sessão: Sim, ambos

Soluções de migração do Azure RemoteApp: Sim, [Saiba mais](https://mycloudit.com/remote-app-microsoft/)

**Contacto:**
- Bruno Garoutte, VP de desenvolvimento comercial

   Telefone: 972-218-0741

   Correio eletrónico:[brian.garoutte@conexlink.com](mailto:brian.garoutte@conexlink.com)

#### <a name="acuutech"></a>**Acuutech**
[Acuutech](http://www.acuutech.com) especializada em fornecer alojadas soluções de ambiente de trabalho, fornecer ambiente de trabalho completo e aplicações ISV experiências criada com base em tecnologia Microsoft para um cliente global base a partir do Azure e os seus próprios centros de dados.

Localização principal: Londres, Reino Unido; Singapura; Guarda, TX

Região de operação: em todo o mundo

Estado do parceiro: ouro

Fornecedor de serviços da Microsoft Cloud: Sim

Oferecer soluções de RemoteApp e ambiente de trabalho com base na sessão: Sim, ambos

Soluções de migração do Azure RemoteApp: Sim, [Saiba mais](http://www.acuutech.com/ara-migration/)

**Contacto:**

- Reino Unido:

  5/6 Iorque House, Langston estrada,

  Loughton, Essex IG10 3TQ
  
  Telefone: +44 (0) 20 8502 2155
 
- Singapura:

  Rua 100 Cecil, #09-02, 
  
  O globo, Singapura 069532
  
  Telefone: + 65 6709 4933
 
- América do Norte: 

  3601 S. Sandman St.
  
  200 do conjunto de aplicações, guarda, TX 77098
  
  Telefone: +1 713 691 0800

## <a name="need-more-help"></a>Precisa de mais ajuda?
Ainda precisa de ajuda a escolher ou ainda tem perguntas? Utilize um dos seguintes métodos para obter ajuda. 

1.  Enviar por correio eletrónico-na [arainfo@microsoft.com](mailto:arainfo@microsoft.com).
2.  Contacte o [suporte do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade). Comece por abrir um [incidente de suporte do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
3.  Contacte-nos. [Localizar um número de vendas local](https://azure.microsoft.com/overview/sales-number/).
