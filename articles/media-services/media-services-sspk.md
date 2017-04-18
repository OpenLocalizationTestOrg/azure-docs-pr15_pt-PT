<properties 
    pageTitle="Licenciamento Microsoft® suaves transmissão cliente migrar Kit" 
    description="Saiba mais sobre como para o licenciamento da Microsoft® suaves transmissão cliente migrar Kit." 
    services="media-services" 
    documentationCenter="" 
    authors="xpouyat,vsood" 
    manager="erikre" 
    editor=""/>

<tags 
    ms.service="media-services" 
    ms.workload="media" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/06/2016"  
    ms.author="xpouyat"/>

#<a name="licensing-microsoft-smooth-streaming-client-porting-kit"></a>Licenciamento Microsoft® suaves transmissão cliente migrar Kit

##<a name="overview"></a>Descrição geral

O Microsoft suaves transmissão cliente migrar Kit (**SSPK** para pequena) é uma implementação do cliente transmissão suave que está optimizada para o ajudar a fabricantes de dispositivos incorporado, cabo e operadores móveis, fornecedores de serviço de conteúdos, fabricantes de auscultador, independentes de software MVPs e fornecedores de soluções para criar produtos e serviços de transmissão ajustável conteúdo transmissão num formato de transmissão suaves. SSPK é uma implementação independente dispositivo e a plataforma do cliente transmissão suave que pode ser convertido pelo licenciado para qualquer dispositivo e a plataforma. 

Incluída abaixo é uma arquitetura de alto nível e caixa IIS suaves transmissão migrar Kit é a aplicação de cliente de transmissão suaves fornecida pela Microsoft e inclui todos os a lógica core para reprodução de transmissão suaves conteúdo. Isto é, em seguida, convertido por parte de parceiros para um dispositivo ou específico plataforma através da implementação interfaces adequadas. 

![SSPK](./media/media-services-sspk/sspk-arch.png)

##<a name="description"></a>Descrição

SSPK é licenciado termos que oferecem valores excelente da empresa. Licença SSPK fornece da indústria com:

- Origem de Kit de migrar transmissão suave no C++ 
  - Implemente a funcionalidade de cliente de transmissão suaves
  - Adiciona o formato de análise, heurística, memória intermédia lógica, etc.
- Aplicação de leitor APIs 
  - interfaces de programação para interação com uma aplicação do leitor de multimédia
- Interface do plataforma produção camada (PAL) 
  - interfaces de programação para interação com o sistema operativo (threads, sockets)
- Interface do hardware produção camada (HAL) 
  - interfaces para interação com hardware respostas de programação / descodificadores V (descodificar, compor)
- Interface de (DRM) de gestão de direitos digitais 
  - interfaces de programação de acordo com DRM através da camada de produção DRM (DAL)
  - Microsoft PlayReady migrar Kit vem incluído separadamente mas integra-se através desta interface. Para obter mais detalhes sobre o licenciamento da Microsoft PlayReady Device, clique [aqui](http://www.microsoft.com/playready/licensing/device_technology.mspx#pddipdl).
- Exemplos de implementação 
  - implementação de PAL de exemplo para Linux
  - implementação de HAL de exemplo para GStreamer

##<a name="licensing-options"></a>Opções de licenciamento

Microsoft suaves transmissão cliente migrar Kit é disponibilizado a licenciados em dois acordos de licença distintas: uma para desenvolver suaves transmissão cliente intercalar os produtos e outro para distribuir suaves transmissão cliente Final produtos para os utilizadores finais.
 
- Para fabricantes de chipset, integradores ou independentes de software MVPs que necessitam de um código de origem migrar kit para desenvolver intercalar produtos, deve ser executada uma Microsoft suaves transmissão cliente migrar Kit **Intercalar licença de produto** .
- Para fabricantes de dispositivo ou fabricantes independentes de software que necessitam de direitos de distribuição para suaves transmissão cliente Final produtos para os utilizadores finais, a Microsoft suaves transmissão cliente migrar Kit **Licença de produto Final** deve ser executada.

###<a name="microsoft-smooth-streaming-client-porting-kit-interim-product-license"></a>Microsoft suave transmissão cliente migrar licença de produto de Kit intercalar

Neste licenças, a Microsoft oferece um bom transmissão cliente migrar Kit e direitos de propriedade intelectual necessárias para desenvolver e distribuir suaves transmissão cliente intercalar os produtos para outras licenciados de dispositivo de suaves transmissão cliente migrar Kit distribuir suaves transmissão cliente produtos finais.

####<a name="fee-structure"></a>Estrutura de taxa

Uma taxa de licença únicos EUA 50.000 oferece um acesso ao suaves transmissão cliente migrar Kit. 

###<a name="microsoft-smooth-streaming-client-porting-kit-final-product-license"></a>Microsoft suave transmissão migrar licença de produto Final de Kit de cliente

Neste licenças, a Microsoft oferece todos os direitos de propriedade intelectual necessárias para receber suaves transmissão cliente intercalar os produtos de outros licenciados suaves transmissão cliente migrar Kit e distribuir empresa adicionado uma imagem corporativa suaves transmissão cliente Final os produtos para os utilizadores finais.

####<a name="fee-structure"></a>Estrutura de taxa

O cliente suaves transmissão produto Final é-lhe fornecido num modelo de royalties como em:

- $0,10 por implementação de dispositivo envio das encomendas
- O royalty está limitado a 50.000 cada ano
- Sem royalty para implementações do dispositivo primeiras 10.000 cada ano 

##<a name="licensing-procedure-and-sspk-access"></a>Procedimento de licenças e SSPK de acesso

Fórum de correio eletrónico [sspkinfo@microsoft.com](mailto:sspkinfo@microsoft.com) para todas as consultas de licenciamento.

O [portal de distribuição SSPK](https://microsoft.sharepoint.com/teams/SSPKDOWNLOAD/) fica acessível a registados licenciados de intercalar.

Intercalar e Final SSPK licenciados podem submeter questões técnicas para [smoothpk@microsoft.com](mailto:smoothpk@microsoft.com).

##<a name="microsoft-smooth-streaming-client-interim-product-agreement-licensees"></a>Microsoft suave transmissão cliente produto provisório contrato licenciados

- Soluções empresariais adroit, Inc.
- SA de difusão Digital avançadas
- AirTies Kablosuz Iletism Sanayive Dis Ticaret S.A.
- Albis Technologies Ltd.
- Alticast Corporation
- Amazon Digital serviços, Inc.
- AVC multimédia Software Co., Ltd.
- Cavium, Inc.
- EchoStar comprar Corporation
- Enseo, Inc.
- Fluendo América do Sul
- HANDAN BroadInfoCom co., Ltd.
- Infomir GMBH
- Irdeto EUA Inc
- Liberdade Global serviços BV
- Inc MediaTek.
- Co MStar, Ltd.
- Nintendo co., Ltd.
- OpenTV, Inc.
- Açafrão Digital limitado
- Sichuan Changhong eléctricos co,. Ltd.
- SoftAtHome
- Sony Corporation
- Tatung Technology Inc.
- TCL Technoly electrónica (Huizhou) Co., Ltd.
- Vestel Elektronik Sanayi vo Ticaret S.A.
- VisualOn, Inc.
- ZTE Corporation

##<a name="microsoft-smooth-streaming-client-final-product-agreement-licensees"></a>Microsoft suave transmissão cliente produto Final contrato licenciados

- SA de difusão Digital avançadas
- AirTies Kablosuz Iletism Sanayive Dis Ticaret S.A.
- Albis Technologies Ltd.
- Amazon Digital serviços, Inc.
- AmTRAN tecnologia co., Ltd.
- Arcadyan Technology Corporation
- ATMACA FORNECER ELEKTRONİK SAN. TİC GUARDAR. A.Ş
- Colúmbia Sky difusão limitado
- CastPal Technology Inc. Shenzhen
- Compal electrónica, Inc.
- Dongguan Digital AV Technology Corp., Ltd.
- EchoStar comprar Corporation
- Enseo, Inc.
- Filmflex filmes limitado
- Fluendo América do Sul
- Gibson ' s inovações limitado
- Haier informações Applicantion S.R.L
- HANDAN BroadInfoCom co., Ltd.
- Co. Homecast, Ltd.
- Hon Hai precisão indústria co., Ltd.
- Infomir GMBH
- Kaonmedia co., Ltd.
- KDDI Corporation
- Nintendo co., Ltd.
- Cor de laranja SA
- Açafrão Digital limitado
- Sagemcom banda larga SA
- Shenzhen Coship electrónica co,. LTD.
- Shenzhen Jiuzhou eléctricos co,. Ltd.
- Shenzhen Skyworth Digital tecnologia co,. Ltd.
- Sichuan Changhong eléctricos co., Ltd.
- Skardin Industrial Corp
- Sky Deutschland Fernsehen GmbH & co. KG
- SmarDTV América do Sul
- SoftAtHome
- Sony Corporation
- Limitado de Marketing ultramarino TCL (ao largo comercial de Macau)
- Tecnologias de entrega de tecnicolor, SA
- Tongfang Global. Ltd.
- Estilo de vida Toshiba produtos e serviços Corporation
- Universal multimédia Corporation /Slovakia/ s.r.o.
- VIZIO, Inc.
- Wistron Corporation
- ZTE Corporation

##<a name="media-services-learning-paths"></a>Caminhos de aprendizagem dos serviços de multimédia

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##<a name="provide-feedback"></a>Fornecer comentários

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]
