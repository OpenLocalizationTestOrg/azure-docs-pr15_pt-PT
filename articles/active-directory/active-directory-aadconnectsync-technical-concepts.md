<properties
    pageTitle="Sincronização do Azure AD Connect: conceitos técnicos | Microsoft Azure"
    description="Explica os conceitos técnicos de ligação do Azure AD sync."
    services="active-directory"
    documentationCenter=""
    authors="markusvi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="markusvi;andkjell"/>


# <a name="azure-ad-connect-sync-technical-concepts"></a>Sincronização do Azure AD Connect: conceitos técnicos
Este artigo é um resumo do tópico [arquitetura de compreender](active-directory-aadconnectsync-technical-concepts.md).

Sincronização do Azure AD Connect baseia-se numa plataforma de sincronização metadirectório sólida.
As secções seguintes apresentam os conceitos para a sincronização metadirectório.
Construir relativamente a MIIS, ILM e FIM, o serviços de sincronização do Active Directory Azure fornece a plataforma seguinte para ligar a origens de dados, sincronizar dados entre as origens de dados, bem como o aprovisionamento e deprovisioning de identidades.

![Conceitos técnicos](./media/active-directory-aadconnectsync-technical-concepts/scenario.png)

As secções seguintes fornecem mais detalhes sobre os seguintes aspectos do serviço de sincronização numa:

- Conexão
- Fluxo de atributo
- Espaço de conexão
- Metaverse
- Aprovisionamento

## <a name="connector"></a>Conexão

Os módulos de código que são utilizados para comunicar com um diretório ligado chamam conectores (anteriormente conhecidos como agentes de gestão (MAs)).

Estes são instalados no computador com ligação do Azure AD sync.
As conexões fornecem a agentless capacidade de comunicar utilizando protocolos sistema remoto em vez de depender de implementação de agentes especializados. Isto significa que diminui o risco e horas de implementação, especialmente recorrentes relacionadas com as aplicações e críticos sistemas.

Na imagem acima, a conexão é sinónima o espaço de conexão mas engloba todas as comunicações com o sistema externo.

A conexão é responsável por todos os importar e exportar funcionalidade ao sistema e liberta os programadores de ser necessário especificá-las compreender como ligar ao sistema de cada vierem ao utilizar o aprovisionamento declarativa para personalizar transformações de dados.

Importação e exportação apenas ocorre quando agendada, permitindo para isolamento outro alterações que ocorram dentro do sistema, uma vez que as alterações não propagar automaticamente à origem de dados ligados. Além disso, os programadores também podem criar os seus próprios conectores para ligar à quase todas as origens de dados.

## <a name="attribute-flow"></a>Fluxo de atributo

O metaverse é a vista consolidada de todas as identidades associadas vizinhas espaços de conexão. Na imagem acima, o fluxo de atributo é descrito por linhas com pontas de seta para o fluxo de entrada e saída. Fluxo de atributo é o processo de copiar ou transformará dados de um sistema para outra e todos os atributos monetários (entrados ou saídos).

Fluxo de atributo ocorre entre o espaço de conexão e o metaverse bi bidireccionalmente quando agendadas para executar operações de sincronização (inteiro ou delta).

Fluxo de atributo só ocorre quando estas sincronizações são executadas. Fluxos de atributo são definidos em regras de sincronização. Estes podem ser (ISR na imagem acima) entrada ou saída (OSR na imagem acima).

## <a name="connected-system"></a>Sistema ligado

Sistema ligado (também conhecidos por ligada directório) é que fazem referência ao sistema remoto ligação do Azure AD sync estiver ligado a e ler e escrever os dados de identidade de e para.

## <a name="connector-space"></a>Espaço de conexão

Cada origem de dados ligada é representada como um subconjunto filtrado os objetos e atributos no espaço de conexão.
Isto permite que o serviço de sincronização trabalhar com localmente sem ter de contactar o sistema remoto quando sincronizar os objetos e restringe interação à importação e exporta apenas.

Quando a origem de dados e o conector têm a capacidade de fornecer uma lista das alterações (uma importar delta), em seguida, a eficiência operacional aumenta significativamente como apenas os variações desde o último ciclo de inquéritos são trocados. O espaço de conexão isola as origens de dados ligados a partir de alterações propagação automaticamente ao exigir que a agenda de conexão importação e exportação. Este seguro adicionado concede paz de ideias enquanto testes, pré-visualizar ou confirmar a próxima atualização.

## <a name="metaverse"></a>Metaverse

O metaverse é a vista consolidada de todas as identidades associadas vizinhas espaços de conexão.

À medida identidades estão ligadas em conjunto e autoridade está atribuída para vários atributos através de mapeamentos de fluxo de importação, o objeto central metaverse começa a agregação informações a partir de vários sistemas. A partir deste fluxo de atributo de objeto, mapeamentos conter informações para sistemas de saída.

Objetos são criados quando um sistema autoritativos projectos-los para o metaverse. Assim que todas as ligações são removidas, o objeto metaverse é eliminado.

Objetos de metaverse não podem ser editados diretamente. Todos os dados no objeto tem de ser contribuiu com através de fluxo de atributo. O metaverse mantém conexões persistentes com cada espaço de conexão. Estes conectores não necessitam de reavaliação para cada execução da sincronização. Isto significa que a ligação do Azure AD sync não tem de localize o objeto remoto correspondente de cada vez. Isto evita a necessidade de agentes dispendiosos impedir alterações a atributos que normalmente seria responsáveis pela correlacionar os objetos.

Quando a descobrir novas origens de dados que possam ter objetos pré-existentes que têm de ser geridos, ligação do Azure AD sync utiliza um processo chama-se uma regra de associação para avaliar potenciais candidatos com a qual pretende estabelecer uma ligação.
Assim que a ligação é estabelecida, esta avaliação não voltar a ocorrer e fluxo de atributo normal pode ocorrer entre a origem de dados ligados remoto e o metaverse.

## <a name="provisioning"></a>Aprovisionamento

Quando uma projectos de origem autoritativas um novo objeto para o metaverse um novo objeto de espaço de conexão pode ser criado no outro conector que representa uma origem de dados ligada descendentes.

Este procedimento implicitamente estabelece uma ligação e fluxo de atributo pode prosseguir bi bidireccionalmente.

Sempre que uma regra determina que necessita ser criado um novo objeto de espaço de conexão, denomina-se de aprovisionamento. No entanto, uma vez que esta operação só ocorre o mais dentro do espaço de conexão,-não utilizava para a origem de dados ligada até que seja realizada uma exportação.

## <a name="additional-resources"></a>Recursos adicionais

* [Azure AD ligar sincronização: Opções de personalização de sincronização](active-directory-aadconnectsync-whatis.md)
* [Integrar a suas identidades no local com o Azure Active Directory](active-directory-aadconnect.md)

<!--Image references-->
[1]: ./media/active-directory-aadsync-technical-concepts/ic750598.png
