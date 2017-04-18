<properties
    pageTitle="Executar Cassandra com Linux no Azure | Microsoft Azure"
    description="Como executar um cluster de Cassandra no Linux em máquinas virtuais do Azure através de uma aplicação de Node.js"
    services="virtual-machines-linux"
    documentationCenter="nodejs"
    authors="hanuk"
    manager="wpickett"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-linux"
    ms.workload="infrastructure-services"
    ms.tgt_pltfrm="vm-linux"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/11/2016"
    ms.author="hanuk;robmcm"/>

# <a name="running-cassandra-with-linux-on-azure-and-accessing-it-from-nodejs"></a>Em execução Cassandra com Linux no Azure e aceder ao mesmo Node.js

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Obter informações sobre como [executar estes passos utilizando o modelo de Gestor de recursos](https://azure.microsoft.com/documentation/templates/datastax-on-ubuntu/).

## <a name="overview"></a>Descrição geral
Microsoft Azure é uma plataforma de abrir na nuvem que é executada ambos os Microsoft como bem como não software da Microsoft que inclui os sistemas operativos, servidores de aplicações, software intermédio mensagens, bem como bases de dados SQL e NoSQL de ambos os modelos de comercial e abrir origem. Criação de serviços e são no nuvens públicos, incluindo Azure requer o planeamento e cuidados arquitetura deliberada para ambos os servidores de aplicações como camadas de armazenamento bem. Arquitetura de armazenamento distribuído do Cassandra naturalmente ajuda na criação de sistemas altamente disponíveis que estão tolerância a falhas de cluster de falhas. Cassandra é uma escala de nuvem da base de dados de NoSQL mantida pela Apache Software Foundation cassandra.apache.org; Cassandra escrito Java e executa, por conseguinte, tanto no Windows, bem como Linux plataformas.

O foco deste artigo é mostrar implementação Cassandra no Ubuntu como um cluster de centro de único e os dados com várias tirar partido da máquinas virtuais do Microsoft Azure e redes virtuais. A implementação de cluster para cargas de trabalho de produção otimizada estiver fora do âmbito deste artigo como necessita de configuração de nó com várias disco, estrutura da topologia simultaneamente adequado e modelação para suportar a replicação conforme seja necessária, consistência dos dados, débito e requisitos de elevada disponibilidade de dados.

Demora neste artigo uma abordagem fundamental para mostrar o que está envolvida na criação de Cassandra cluster comparados com Docker, chefe ou Puppet que pode facilitar a implementação de infraestrutura.  

## <a name="the-deployment-models"></a>Os modelos de implementação
Redes do Microsoft Azure permite a implementação de isoladas clusters privados, o acesso de que pode ser restrito para alcançar a segurança de rede com ajustar.  Uma vez que este artigo é relativo a mostrar a implementação de Cassandra num nível de fundamentais, podemos irá não focar o nível de consistência e a estrutura de armazenamento ideal para débito. Eis a lista de requisitos para os nossos hipotética cluster de rede:

- Sistemas externos não é possível aceder Cassandra da base de dados a partir de dentro ou fora do Azure
- Cassandra cluster tem de estar atrás de um balanceador de carga para o tráfego de outras entidades
- Implementar nós Cassandra em dois grupos cada centro de dados para um disponibilidade do cluster melhorado
- Bloquear o cluster por isso, que só farm de servidores de aplicação tem acesso à base de dados diretamente
- Sem os pontos finais redes públicos que não seja SSH
- Cada nó Cassandra necessita de um endereço IP interno fixo

Pode ser implementada Cassandra a uma única região Azure ou em várias regiões com base em da natureza distribuída da carga de trabalho. Modelo de implementação de região com várias pode ser utilizado para servir aos utilizadores finais mais perto um nomeadamente geografia através de infraestrutura de Cassandra do mesmo. Nó incorporados replicação demora assistência do Cassandra a sincronização de modelo global de várias escreve provenientes de várias centros de dados e apresenta uma vista consistente dos dados para aplicações. Implementação da região com várias também pode ajudar com a mitigação risco das falhas de serviço Azure mais amplas. Consistência ajustável do Cassandra e topologia de replicação irá ajudar em diversificados RPO às necessidades dos pedidos de reunião.

### <a name="single-region-deployment"></a>Implementação única região
Iremos começar com uma implementação única região e recolher learnings na criação de um modelo com várias região. Funcionamento em rede virtual Azure será utilizado para criar sub-redes isoladas para que podem cumprir os requisitos de segurança de rede mencionados acima.  O processo descrito na criação de implementação de única região utiliza Ubuntu 14.04 LTS e Cassandra 2.08; No entanto, o processo de facilmente pode ser aprovado para as outras variantes Linux. Seguem-se algumas das características sistémicas da implementação única região.  

**Elevada disponibilidade:** Os nós de Cassandra apresentados na figura 1 são implementados dois conjuntos de disponibilidade para que os nós são propagados entre vários domínios falhas para elevada disponibilidade. VMs anotados com cada conjunto de disponibilidade é mapeado para 2 domínios de falha.  Utiliza do Microsoft Azure o conceito de domínio falhas para gerir o tempo planeado para baixo (por exemplo, falhas de software ou hardware) enquanto o conceito de atualização domínio (por exemplo, anfitrião ou convidado SO aplicação de patches/atualiza os, actualizações de aplicação) é utilizado para gerir agendada para baixo de tempo. Consulte [recuperação de falhas e elevada disponibilidade para aplicações do Azure para a função de falhas](http://msdn.microsoft.com/library/dn251004.aspx) e domínios no atingir elevada disponibilidade de atualização.

![Implementação única região](./media/virtual-machines-linux-classic-cassandra-nodejs/cassandra-linux1.png)

Figura 1: Implementação de região única

Tenha em atenção que no momento deste escrita, Azure não lhe permitir o mapeamento de um grupo de VMs para um domínio de falhas específica; explícito Por conseguinte, mesmo com o modelo de implementação apresentado na figura 1, é estatisticamente provável que todas as máquinas virtuais podem ser mapeadas para dois domínios falhas em vez de quatro.

**Outras entidades tráfego de balanceamento de carga:** Bibliotecas de cliente de outras entidades dentro do servidor web ligar ao cluster através de um balanceador de carga interno. Isto requer que o processo de adição Balanceador de carga interno à sub-rede "dados" (consulte figura 1) no contexto de serviço em nuvem Cassandra cluster de alojamento. Assim que o Balanceador de carga interno for definido, cada nó requer o ponto final de balanceamento de carga seja adicionada com anotações de um conjunto de balanceamento de carga com o nome do Balanceador de carga definido anteriormente. Para obter mais detalhes, consulte a [Azure interno balanceamento de carga ](../load-balancer/load-balancer-internal-overview.md).

**Cluster sementes:** É importante selecionar a maior parte de nós altamente disponíveis para sementes como novos nós irão comunicar connosco de propagação para descobrir a topologia de cluster. Um nó de cada conjunto de disponibilidade está designado como nós de propagação para evitar ponto único de falha.

**Fator de replicação e consistência nível:** Na compilação alta disponibilidade e dados durabilidade do Cassandra é caracterizada por fator de replicação (RF - número de cópias de cada linha armazenados no cluster) e o nível de consistência (número de réplicas para ser lidos/escritos antes de devolver o resultado para o autor da chamada). Fator de replicação é especificada durante a criação de KEYSPACE (semelhante a uma base de dados relacional), Considerando que o nível de consistência for especificado enquanto emissão a consulta CRUD. Consulte a documentação de Cassandra em [configurar para consistência](http://www.datastax.com/documentation/cassandra/2.0/cassandra/dml/dml_config_consistency_c.html) para obter detalhes de consistência e a fórmula de cálculo quórum.

Cassandra suporta dois tipos de modelos de integridade de dados – consistência e consistência Eventual; a replicação fator e o nível de consistência irão em conjunto determinar se os dados serão consistentes assim que estiver concluída uma operação de escrita ou será eventualmente consistente. Por exemplo, especificar QUÓRUM como o nível de consistência será sempre garante dados consistência enquanto qualquer nível de consistência, abaixo do número de réplicas sejam escritos nos, conforme necessário para atingir QUÓRUM (por exemplo, uma) resulta no dados a ser eventualmente consistentes.

Cluster 8-node apresentado acima, com um fator de replicação de 3 e QUÓRUM (2 nós são lidos ou escritos consistência) leitura/escrita nível de consistência, pode permanecem a perda teórica de mais de 1 nó por grupo de replicação antes do início de aplicação verificando a falha. Este comando assume que todos os espaços chaves têm com bem balanceamento de leitura/escritam pedidos.  Seguem-se os parâmetros que utilizamos para o cluster implementado:

Configuração do cluster de Cassandra única região:

| Parâmetro de cluster | Valor | Observações |
| ----------------- | ----- | ------- |
| Número de nós (N) | 8   | Número total de nós do cluster |
| Fator de replicação (RF) | 3 | Número de réplicas de uma determinada linha |
| Nível de consistência (escrita) | QUORUM[(RF/2) +1) = 2] o resultado da fórmula é arredondado por defeito | Escreve no máximo 2 réplicas antes da resposta é enviada para o autor da chamada; uma forma consistente acaba por ser escrita réplica 3º. |
| Nível de consistência (de leitura) | QUÓRUM [(RF/2),1 = 2] o resultado da fórmula é arredondado por defeito | Lê 2 réplicas antes de enviar a resposta para o autor da chamada. |
| Estratégia de replicação | NetworkTopologyStrategy consulte [Replicação de dados](http://www.datastax.com/documentation/cassandra/2.0/cassandra/architecture/architectureDataDistributeReplication_c.html) na documentação de Cassandra para obter mais informações | Compreenda a topologia de implementação e coloca réplicas em nós para que todas as réplicas não terminam mesmo bastidor |
| Snitch | GossipingPropertyFileSnitch consulte [Snitches](http://www.datastax.com/documentation/cassandra/2.0/cassandra/architecture/architectureSnitchesAbout_c.html) na documentação de Cassandra para obter mais informações | NetworkTopologyStrategy utiliza um conceito de snitch para compreender a topologia. GossipingPropertyFileSnitch dá um melhor controlo no mapeamento de cada nó para o Centro de dados e bastidor. O cluster utiliza, em seguida, mexerico a serem propagadas estas informações. Isto é muito mais simples na definição de IP dinâmica relativamente às PropertyFileSnitch |


**Azure considerações para Cassandra Cluster:** Capacidade de máquinas virtuais do Microsoft Azure utiliza armazenamento de Blobs do Azure para persistente disco; Armazenamento Azure guarda 3 réplicas de cada disco para alta durabilidade. Isso significa que cada linha de dados inseridas uma tabela de Cassandra já está armazenada no 3 réplicas e, consequentemente, consistência dos dados está já a tratar do mesmo se replicação fator (RF) é 1. O problema principal com fator de replicação a ser 1 é que a aplicação de experiência do tempo de inatividade mesmo se a um único nó Cassandra falha. No entanto, se for um nó para baixo para os problemas (por exemplo, hardware, falhas de software do sistema) reconhecidos pelo Azure ferro controlador, aprovisionar um novo nó no seu lugar a utilizar as mesmo unidades de armazenamento. Aprovisionar um novo nó para substituir o antigo poderá demorar alguns minutos.  Da mesma forma para atividades de manutenção planeada como alterações do SO de convidado, Cassandra actualiza e as alterações à aplicação Azure ferro controlador executa gradual atualiza os de nós do cluster.  Gradual atualiza os também poderá demorar para baixo nós alguns uma vez e, consequentemente, cluster poderá breve indisponibilidade para a partições alguns. No entanto, os dados não serão perdidos devido a redundância de armazenamento do Windows Azure incorporada.  

Para sistemas implementados Azure que não necessita de elevada disponibilidade (por exemplo, em redor 99,9 que é equivalente ao 8.76 hrs/ano; consulte [Elevada disponibilidade](http://en.wikipedia.org/wiki/High_availability) para obter detalhes) poderá conseguir executar com RF = 1 e nível de consistência = um.  Para as aplicações com os requisitos de elevada disponibilidade, RF = 3 e consistência nível = QUÓRUM irão tolerar o tempo para baixo de um de nós de uma das réplicas. RF = 1 na tradicional implementações (por exemplo, no local) não podem ser utilizadas devido a possível perda de dados resultante problemas como falhas do disco.   

## <a name="multi-region-deployment"></a>Implementação da região com várias
Ajuda dados que utilizem o Centro replicação e consistência modelo do Cassandra descrito acima com a implementação de região com várias terminar a caixa sem necessidade de qualquer ferramentas externas. Este é bastante diferente a partir de bases de dados relacionais tradicionais onde a configuração de base de dados espelhando as para vários mestres escritas pode ser bastante complexa. Cassandra numa região com várias configurar o pode ajudar com os cenários de utilização, incluindo os seguintes:

**Proximidade com base implementação:** Aplicações do inquilinos com várias, com o mapeamento de limpar de utilizadores do inquilino-para-região, pode ser sido objecto por latências baixa o cluster com várias região. Por exemplo uma gestão de aprendizagem sistemas para estabelecimentos de ensino podem implementar um cluster distribuído em regiões Leste dos Estados Unidos e EUA Ocidental para servir os Campus respetivos para transaccionais assim como a análise. Os dados podem ser localmente consistentes na linha de tempo lê e escritas e podem ser eventualmente consistentes ao longo de ambas as regiões. Existem outros exemplos de como distribuição de multimédia, comércio electrónico e nada e tudo o que serve de base geo concentrado utilizador é um caso de utilização boa para este modelo de implementação.

**Elevada disponibilidade:** Redundância é um fator de chave na realização elevada disponibilidade do software e hardware; consulte o artigo edifício fiável nuvem sistemas no Microsoft Azure para obter detalhes. No Microsoft Azure, a forma fiável apenas de atingir redundância verdadeira é ao implementar um cluster de região com várias. Aplicações podem ser implementadas no modo de activo activo ou activo-passivo e das regiões, se ainda para baixo o Gestor de tráfego Azure pode redirecionar o tráfego à região de ativo.  Com a implementação única região, se a disponibilidade for 99,9, uma implementação duas região possa atingir uma disponibilidade de 99.9999 calculada pela fórmula: (1-(1-0.999) *(0,999 1))*100); consulte a documentação acima para obter detalhes.

**Recuperação de falhas:** Cluster de Cassandra da região com várias, se concebido corretamente, pode suportar Centro de dados grave corrente. Se for uma região para baixo, pode iniciar a aplicação implementada noutras regiões que servem os utilizadores finais. Como quaisquer outras empresas continuidade implementações, a aplicação tem de ser tolerância a falhas algumas perda de dados resultante a partir de dados no pipeline de assíncrono. No entanto, Cassandra faz com que a recuperação ter muito uma que o tempo despendido por processos de recuperação de base de dados tradicional. Figura 2 mostra o modelo de implementação de região com várias típica com oito nós em cada região. Ambas as regiões são imagens de espelho uns dos outros para a mesma de simetria; estruturas reais variam consoante o tipo de carga de trabalho (por exemplo, transaccional ou analytical), RPO, RTO, consistência dos dados e requisitos de disponibilidade.

![Implementação de região multi](./media/virtual-machines-linux-classic-cassandra-nodejs/cassandra-linux2.png)

Figura 2: Implementação de região com várias Cassandra

### <a name="network-integration"></a>Integração com redes
Conjuntos de máquinas virtuais implementados redes privadas localizados no duas regiões comunica com os outros utilizando um túnel VPN. O túnel VPN liga-se dois gateways de software aprovisionados durante o processo de implementação de rede. Ambas as regiões têm arquitetura de rede semelhantes em termos sub-redes "web" e "dados"; Redes Azure permite a criação de sub-redes tantas conforme necessário e aplique ACL, conforme necessário, segurança de rede. Enquanto criava a topologia de cluster, nomeadamente latência de comunicação de centro de dados e o impacto económico a necessidade de tráfego de rede sejam considerados.

### <a name="data-consistency-for-multi-data-center-deployment"></a>Consistência dos dados para a implementação do Centro de dados com várias
Distribuído necessidade de implementações ter em consideração o impacto de topologia de cluster no débito e disponibilidade de alta. O nível de consistência e RF tem de estar selecionada, de modo que o quórum não depende a disponibilidade de todos os centros de dados.
Para um sistema de que necessita de consistência alta, um LOCAL_QUORUM para o nível de consistência (para operações de leitura e escrita) será Certifique-se de que a lê local e as gravações são correspondidas partir de nós locais enquanto dados estão replicados modo assíncrono aos centros de dados remota.  Tabela 2 resume os detalhes de configuração para o cluster de região com várias destacada mais tarde a escrita para cima.

**Configuração do cluster duas região Cassandra**


| Parâmetro de cluster | Valor | Observações |
| ----------------- | ----- | ------- |
| Número de nós (N) | + 8 de 8 | Número total de nós do cluster |
| Fator de replicação (RF) | 3 | Número de réplicas de uma determinada linha |
| Nível de consistência (escrita) | LOCAL_QUORUM [(sum(RF)/2) +1) = 4] o resultado da fórmula é arredondado por defeito | 2 nós serão escritos Centro de dados da primeira forma sincronizada; os nós de 2 adicionais necessários para quórum serão escritos forma assíncrona para o Centro de dados 2.. |
| Nível de consistência (de leitura) | LOCAL_QUORUM ((RF/2),1) = 2, o resultado da fórmula é arredondado por defeito | Pedidos de leitura são correspondidos a partir de apenas uma região; 2 nós são lidos antes da resposta é enviada para o cliente. |
| Estratégia de replicação | NetworkTopologyStrategy consulte [Replicação de dados](http://www.datastax.com/documentation/cassandra/2.0/cassandra/architecture/architectureDataDistributeReplication_c.html) na documentação de Cassandra para obter mais informações | Compreenda a topologia de implementação e coloca réplicas em nós para que todas as réplicas não terminam mesmo bastidor |
| Snitch | GossipingPropertyFileSnitch consulte [Snitches](http://www.datastax.com/documentation/cassandra/2.0/cassandra/architecture/architectureSnitchesAbout_c.html) na documentação de Cassandra para obter mais informações | NetworkTopologyStrategy utiliza um conceito de snitch para compreender a topologia. GossipingPropertyFileSnitch dá um melhor controlo no mapeamento de cada nó para o Centro de dados e bastidor. O cluster utiliza, em seguida, mexerico a serem propagadas estas informações. Isto é muito mais simples na definição de IP dinâmica relativamente às PropertyFileSnitch |


##<a name="the-software-configuration"></a>A CONFIGURAÇÃO DE SOFTWARE
As seguintes versões do software são utilizadas durante a implementação:

<table>
<tr><th>Software</th><th>Origem</th><th>Versão</th></tr>
<tr><td>JRE </td><td>[JRE 8](http://www.oracle.com/technetwork/java/javase/downloads/server-jre8-downloads-2133154.html) </td><td>8U5</td></tr>
<tr><td>JNA </td><td>[JNA](https://github.com/twall/jna) </td><td> 3.2.7</td></tr>
<tr><td>Cassandra</td><td>[Apache Cassandra 2.0.8](http://www.apache.org/dist/cassandra/2.0.8/apache-cassandra-2.0.8-bin.tar.gz)</td><td> 2.0.8</td></tr>
<tr><td>Ubuntu  </td><td>[Microsoft Azure](https://azure.microsoft.com/) </td><td>14.04 LTS</td></tr>
</table>

Uma vez que a transferência de JRE exige aceitação manual de licença Oracle, para simplificar a implementação, transfira todo o software necessário para o ambiente de trabalho para carregar mais tarde para a imagem do modelo Ubuntu que podemos vão criar como precursor de para a implementação de cluster.

Transferir o software acima para um diretório de transferência famoso (por exemplo, %TEMP%/downloads no Windows ou ~/Downloads na maioria dos Linux distribuições ou Mac) no computador local.

### <a name="create-ubuntu-vm"></a>CRIAR UBUNTU VM
Neste passo do processo podemos criará Ubuntu imagem com o software necessário para que a imagem pode ser reutilizada para aprovisionar vários nós Cassandra.  
####<a name="step-1-generate-ssh-key-pair"></a>PASSO 1: Gerar par de chaves de SSH
Azure necessita de um X509 codificado chave pública PEM ou DER ao tempo aprovisionamento. Gera um par de chaves público/privado, utilizando as instruções que se encontra no como utilizar SSH com Linux no Azure. Se planeia utilizar putty.exe como um cliente SSH no Windows ou Linux, tem de converter PEM codificado chave privada RSA para o formato PPK através de puttygen.exe; as instruções para este podem ser encontradas na página web acima.

####<a name="step-2-create-ubuntu-template-vm"></a>PASSO 2: Criar o modelo de Ubuntu VM
Para criar o modelo VM, inicie sessão no portal clássico do Azure e utilize a seguinte sequência: clique em novo, cluster, máquina VIRTUAL, de GALERIA, UBUNTU, Ubuntu Server 14.04 LTS e, em seguida, clique na seta à direita. Para obter um tutorial que descreve como criar uma VM Linux, consulte o artigo criar um Linux de execução de Máquina Virtual.

Introduza as seguintes informações no ecrã "configuração de Máquina Virtual" #1:

<table>
<tr><th>NOME DO CAMPO              </td><td>       VALOR DO CAMPO               </td><td>         OBSERVAÇÕES                </td><tr>
<tr><td>DATA DE LANÇAMENTO DE VERSÃO    </td><td> Selecione uma data a partir do drow para baixo</td><td></td><tr>
<tr><td>NOME DA MÁQUINA VIRTUAL    </td><td> modelo de CASS                </td><td> Este é o nome do anfitrião da VM </td><tr>
<tr><td>CAMADA                     </td><td> PADRÃO                        </td><td> Deixe a predefinição              </td><tr>
<tr><td>TAMANHO                     </td><td> A1                              </td><td>Selecione a VM com base nas necessidades IO; para este efeito deixe o predefinido </td><tr>
<tr><td> NOVO NOME DE UTILIZADOR           </td><td> localadmin                      </td><td> "administrador" é um nome de utilizador reservada Ubuntu 12 xx e depois</td><tr>
<tr><td> AUTENTICAÇÃO      </td><td> Clique em caixa de verificação                 </td><td>Verificar se o que pretende proteger com uma chave SSH </td><tr>
<tr><td> CERTIFICADO             </td><td> nome de ficheiro de certificado de chave pública </td><td> Utilizar a chave pública gerada previamente</td><tr>
<tr><td> Nova palavra-passe   </td><td> palavra-passe segura </td><td> </td><tr>
<tr><td> Confirmar palavra-passe   </td><td> palavra-passe segura </td><td></td><tr>
</table>

Introduza as seguintes informações no ecrã "configuração de Máquina Virtual" #2:

<table>
<tr><th>NOME DO CAMPO             </th><th> VALOR DO CAMPO                       </th><th> OBSERVAÇÕES                                 </th></tr>
<tr><td> SERVIÇO NA NUVEM  </td><td> Criar um novo serviço na nuvem    </td><td>Serviço de nuvem está num contentor cluster a recursos como máquinas virtuais</td></tr>
<tr><td> NOME DE DNS DO SERVIÇO NA NUVEM </td><td>ubuntu template.cloudapp.net   </td><td>Dê um nome de Balanceador de carga desconhecido máquina</td></tr>
<tr><td> REGIÃO/GRUPO AFINIDADE/REDE VIRTUAL </td><td>    Ocidental dos e.u.a. </td><td> Selecione uma região acederem a partir do qual ao cluster Cassandra a suas aplicações web</td></tr>
<tr><td>CONTA DE ARMAZENAMENTO </td><td>   Utilizar predefinido </td><td>Utilizar a conta de armazenamento predefinida ou uma conta de armazenamento previamente criado numa região específico</td></tr>
<tr><td>CONJUNTO DE DISPONIBILIDADE </td><td>  Nenhum </td><td>  Deixá-la em branco</td></tr>
<tr><td>PONTOS FINAIS   </td><td>Utilizar predefinido </td><td>  Utilizar a configuração de SSH predefinida </td></tr>
</table>

Clique na seta direita, mantenha as predefinições no ecrã #3 e clique no botão "verificação" para concluir o processo de aprovisionamento de VM. Depois de alguns minutos, deve ser VM com o nome "ubuntu-modelo" num estado "executar".

###<a name="install-the-necessary-software"></a>INSTALAR O SOFTWARE NECESSÁRIO
####<a name="step-1-upload-tarballs"></a>PASSO 1: Carregar tarballs
Utilizar scp ou pscp, copie o software transferido anteriormente para ~/downloads directório utilizando o seguinte comando formato:

#####<a name="pscp-server-jre-8u5-linux-x64targz-localadminhk-cas-templatecloudappnethomelocaladmindownloadsserver-jre-8u5-linux-x64targz"></a>pscp servidor-jre-8u5-linux-x64.tar.gzlocaladmin@hk-cas-template.cloudapp.net:/home/localadmin/downloads/server-jre-8u5-linux-x64.tar.gz

Repita o comando acima para JRE, bem como para os bits Cassandra.

####<a name="step-2-prepare-the-directory-structure-and-extract-the-archives"></a>PASSO 2: Preparar a estrutura do directório e extrair arquivos
Inicie sessão na VM e criar a estrutura de diretório e extrair software como um utilizador super utilizando o script de festa abaixo:

    #!/bin/bash
    CASS_INSTALL_DIR="/opt/cassandra"
    JRE_INSTALL_DIR="/opt/java"
    CASS_DATA_DIR="/var/lib/cassandra"
    CASS_LOG_DIR="/var/log/cassandra"
    DOWNLOADS_DIR="~/downloads"
    JRE_TARBALL="server-jre-8u5-linux-x64.tar.gz"
    CASS_TARBALL="apache-cassandra-2.0.8-bin.tar.gz"
    SVC_USER="localadmin"

    RESET_ERROR=1
    MKDIR_ERROR=2

    reset_installation ()
    {
       rm -rf $CASS_INSTALL_DIR 2> /dev/null
       rm -rf $JRE_INSTALL_DIR 2> /dev/null
       rm -rf $CASS_DATA_DIR 2> /dev/null
       rm -rf $CASS_LOG_DIR 2> /dev/null
    }
    make_dir ()
    {
       if [ -z "$1" ]
       then
          echo "make_dir: invalid directory name"
          exit $MKDIR_ERROR
       fi

       if [ -d "$1" ]
       then
          echo "make_dir: directory already exists"
          exit $MKDIR_ERROR
       fi

       mkdir $1 2>/dev/null
       if [ $? != 0 ]
       then
          echo "directory creation failed"
          exit $MKDIR_ERROR
       fi
    }

    unzip()
    {
       if [ $# == 2 ]
       then
          tar xzf $1 -C $2
       else
          echo "archive error"
       fi

    }

    if [ -n "$1" ]
    then
       SVC_USER=$1
    fi

    reset_installation
    make_dir $CASS_INSTALL_DIR
    make_dir $JRE_INSTALL_DIR
    make_dir $CASS_DATA_DIR
    make_dir $CASS_LOG_DIR

    #unzip JRE and Cassandra
    unzip $HOME/downloads/$JRE_TARBALL $JRE_INSTALL_DIR
    unzip $HOME/downloads/$CASS_TARBALL $CASS_INSTALL_DIR

    #Change the ownership to the service credentials

    chown -R $SVC_USER:$GROUP $CASS_DATA_DIR
    chown -R $SVC_USER:$GROUP $CASS_LOG_DIR
    echo "edit /etc/profile to add JRE to the PATH"
    echo "installation is complete"


Se colar este script para janela vim, certifique-se remover o símbolo de retorno ('\r ") utilizando o seguinte comando:

    tr -d '\r' <infile.sh >outfile.sh

####<a name="step-3-edit-etcprofile"></a>Passo 3: Editar etc/perfil
Acrescente a seguinte no final:

    JAVA_HOME=/opt/java/jdk1.8.0_05
    CASS_HOME= /opt/cassandra/apache-cassandra-2.0.8
    PATH=$PATH:$HOME/bin:$JAVA_HOME/bin:$CASS_HOME/bin
    export JAVA_HOME
    export CASS_HOME
    export PATH

####<a name="step-4-install-jna-for-production-systems"></a>Passo 4: Instalar JNA para sistemas de produção
Utilize a seguinte comando sequência: O seguinte comando irá instalar jna 3.2.7.jar e jna plataforma 3.2.7.jar para /usr/share.java directório possam sudo-get instalar libjna java

Crie ligações symbolic no directório CASS_HOME/biblioteca $ para que script de arranque do Cassandra pode encontrar estas jarros:

    ln -s /usr/share/java/jna-3.2.7.jar $CASS_HOME/lib/jna.jar

    ln -s /usr/share/java/jna-platform-3.2.7.jar $CASS_HOME/lib/jna-platform.jar

####<a name="step-5-configure-cassandrayaml"></a>Passo 5: Configurar cassandra.yaml
Edite cassandra.yaml no cada VM para refletir necessária por todos os máquinas virtuais [podemos irá aperfeiçoar isto durante o aprovisionamento real] configuração:

<table>
<tr><th>Nome do campo   </th><th> Valor  </th><th> Observações </th></tr>
<tr><td>nome_cluster </td><td>  "CustomerService"   </td><td> Utilize o nome que reflita a sua implementação</td></tr>
<tr><td>listen_address  </td><td>[deixá-la em branco]   </td><td> Eliminar "localhost" </td></tr>
<tr><td>rpc_addres   </td><td>[deixá-la em branco]  </td><td> Eliminar "localhost" </td></tr>
<tr><td>sementes   </td><td>"10.1.2.4, 10.1.2.6, 10.1.2.8" </td><td>Lista de todos os endereços IP que são designados como base.</td></tr>
<tr><td>endpoint_snitch </td><td> org.apache.cassandra.locator.GossipingPropertyFileSnitch </td><td> Isto é utilizado pelo NetworkTopologyStrateg para inferir o Centro de dados e bastidor da VM</td></tr>
</table>

####<a name="step-6-capture-the-vm-image"></a>Passo 6: Capturar a imagem VM
Inicie sessão na máquina virtual utilizando o nome do anfitrião (hk-AC-template.cloudapp.net) e a chave privada de SSH criado anteriormente. Consulte o artigo como utilizar SSH com Linux no Azure para obter detalhes sobre como iniciar sessão utilizando o comando ssh ou putty.exe.

Execute a sequência das ações para capturar a imagem que se segue:
#####<a name="1-deprovision"></a>1. deprovision
Utilize o comando "sudo waagent – deprovision + utilizador" para remover informações específicas de instância de Máquina Virtual. Consulte o artigo [como capturar uma máquina de Virtual Linux](virtual-machines-linux-classic-capture-image.md) a utilizar como um modelo mais detalhes sobre o processo de captura de imagem.

#####<a name="2-shutdown-the-vm"></a>2: encerramento a VM
Certifique-se de que está realçada a máquina virtual e clique na ligação encerramento a partir da barra de comando da parte inferior.

#####<a name="3-capture-the-image"></a>3: capturar a imagem
Certifique-se de que está realçada a máquina virtual e clique na ligação captura a partir da barra de comando da parte inferior. No ecrã seguinte, atribua um nome de imagem (por exemplo, hk-cas-2-08-ub-14-04-2014071), adequado a descrição da imagem e clique na marca de "verificação" para concluir o processo de captura.

Isto vai demorar alguns segundos e a imagem deve ser disponível na secção MY imagens da Galeria de imagem. A origem VM será automaticamente delated depois da imagem é capturada com êxito.

##<a name="single-region-deployment-process"></a>Processo de implementação única região
**Passo 1: criar a rede Virtual** Inicie sessão no portal clássico do Azure e crie uma rede Virtual com a apresentação de atributos na tabela. Consulte o artigo [Configurar uma rede Virtual Cloud-Only no portal do Azure clássico](../virtual-network/virtual-networks-create-vnet-classic-portal.md) para obter passos detalhados do processo.      

<table>
<tr><th>Nome do atributo VM</th><th>Valor</th><th>Observações</th></tr>
<tr><td>Nome</td><td>vnet-cass-oeste--nos</td><td></td></tr>
<tr><td>Região</td><td>Ocidental dos e.u.a.</td><td></td></tr>
<tr><td>Servidores DNS </td><td>Nenhum</td><td>Ignorar esta como podemos não está a utilizar um servidor de DNS</td></tr>
<tr><td>Configurar uma VPN ponto-para-site</td><td>Nenhum</td><td> Ignorar esta</td></tr>
<tr><td>Configurar uma VPN do site para o site</td><td>Nnone</td><td> Ignorar esta</td></tr>
<tr><td>Espaço de endereços</td><td>10.1.0.0/16</td><td></td></tr>
<tr><td>IP inicial</td><td>10.1.0.0</td><td></td></tr>
<tr><td>CIDR </td><td>/ 16 (65531)</td><td></td></tr>
</table>

Adicione as seguintes sub-redes:

<table>
<tr><th>Nome</th><th>IP inicial</th><th>CIDR</th><th>Observações</th></tr>
<tr><td>Web</td><td>10.1.1.0</td><td>/ 24 (251)</td><td>Sub-rede para o farm web</td></tr>
<tr><td>dados</td><td>10.1.2.0</td><td>/ 24 (251)</td><td>Sub-rede para os nós de base de dados</td></tr>
</table>

Dados e sub-redes Web podem ser protegidos através dos grupos de segurança de rede cobertura que estiver fora do âmbito deste artigo.  

**Passo 2: aprovisionar máquinas virtuais** Utilizar a imagem que criou anteriormente, iremos criar as máquinas virtuais seguintes no servidor de nuvem "hk-c-svc-Oeste" e ligá-los para as respetivas sub-redes, conforme apresentado abaixo:

<table>
<tr><th>Nome do computador    </th><th>Sub-rede </th><th>Endereço IP </th><th>Conjunto de disponibilidade</th><th>Cc/bastidor</th><th>Propagar?</th></tr>
<tr><td>HK-c1-oeste--nos   </td><td>dados   </td><td>10.1.2.4   </td><td>HK-c-um conjunto-1    </td><td>Cc = WESTUS bastidor = rack1 </td><td>Sim</td></tr>
<tr><td>HK-c2-oeste--nos   </td><td>dados   </td><td>10.1.2.5   </td><td>HK-c-um conjunto-1    </td><td>Cc = WESTUS bastidor = rack1 </td><td>N </td></tr>
<tr><td>HK-c3-oeste--nos   </td><td>dados   </td><td>10.1.2.6   </td><td>HK-c-um conjunto-1    </td><td>Cc = WESTUS bastidor = rack2 </td><td>Sim</td></tr>
<tr><td>HK-c4-oeste--nos   </td><td>dados   </td><td>10.1.2.7   </td><td>HK-c-um conjunto-1    </td><td>Cc = WESTUS bastidor = rack2 </td><td>N </td></tr>
<tr><td>HK-c5-oeste--nos   </td><td>dados   </td><td>10.1.2.8   </td><td>HK-c-um conjunto-2    </td><td>Cc = WESTUS bastidor = rack3 </td><td>Sim</td></tr>
<tr><td>HK-c6-oeste--nos   </td><td>dados   </td><td>10.1.2.9   </td><td>HK-c-um conjunto-2    </td><td>Cc = WESTUS bastidor = rack3 </td><td>N </td></tr>
<tr><td>HK-c7-oeste--nos   </td><td>dados   </td><td>10.1.2.10  </td><td>HK-c-um conjunto-2    </td><td>Cc = WESTUS bastidor = rack4 </td><td>Sim</td></tr>
<tr><td>HK-c8-oeste--nos   </td><td>dados   </td><td>10.1.2.11  </td><td>HK-c-um conjunto-2    </td><td>Cc = WESTUS bastidor = rack4 </td><td>N </td></tr>
<tr><td>HK-B1-oeste--nos   </td><td>Web    </td><td>10.1.1.4   </td><td>HK-w-um conjunto-1    </td><td>                       </td><td>N/D</td></tr>
<tr><td>HK-w2-oeste--nos   </td><td>Web    </td><td>10.1.1.5   </td><td>HK-w-um conjunto-1    </td><td>                       </td><td>N/D</td></tr>
</table>

Criação da lista acima de VMs requer o seguinte processo:

1.  Criar um serviço em nuvem vazia numa região específico
2.  Criar uma VM a partir da imagem capturada anteriormente e anexá-lo à rede virtual criada anteriormente; Repita este procedimento para todas as VMs
3.  Adicionar um balanceador de carga interna para o serviço de nuvem e anexá-lo à sub-rede "dados"
4.  Para cada VM criada anteriormente, adicione um ponto final de balanceamento de carga para o tráfego de outras entidades através de um conjunto de balanceamento de carga ligado ao balanceador de carga interno criado anteriormente

O processo acima pode ser executado através do portal clássico Azure; utilizar um computador Windows (utilizar uma VM no Azure se não tiver acesso a um computador Windows), utilize o seguinte script do PowerShell para aprovisionar automaticamente todos os 8 VMs.

**Lista 1: Script do PowerShell para aprovisionar máquinas virtuais**

        #Tested with Azure Powershell - November 2014
        #This powershell script deployes a number of VMs from an existing image inside an Azure region
        #Import your Azure subscription into the current Powershell session before proceeding
        #The process: 1. create Azure Storage account, 2. create virtual network, 3.create the VM template, 2. crate a list of VMs from the template

        #fundamental variables - change these to reflect your subscription
        $country="us"; $region="west"; $vnetName = "your_vnet_name";$storageAccount="your_storage_account"
        $numVMs=8;$prefix = "hk-cass";$ilbIP="your_ilb_ip"
        $subscriptionName = "Azure_subscription_name";
        $vmSize="ExtraSmall"; $imageName="your_linux_image_name"
        $ilbName="ThriftInternalLB"; $thriftEndPoint="ThriftEndPoint"

        #generated variables
        $serviceName = "$prefix-svc-$region-$country"; $azureRegion = "$region $country"

        $vmNames = @()
        for ($i=0; $i -lt $numVMs; $i++)
        {
           $vmNames+=("$prefix-vm"+($i+1) + "-$region-$country" );
        }

        #select an Azure subscription already imported into Powershell session
        Select-AzureSubscription -SubscriptionName $subscriptionName -Current
        Set-AzureSubscription -SubscriptionName $subscriptionName -CurrentStorageAccountName $storageAccount

        #create an empty cloud service
        New-AzureService -ServiceName $serviceName -Label "hkcass$region" -Location $azureRegion
        Write-Host "Created $serviceName"

        $VMList= @()   # stores the list of azure vm configuration objects
        #create the list of VMs
        foreach($vmName in $vmNames)
        {
           $VMList += New-AzureVMConfig -Name $vmName -InstanceSize ExtraSmall -ImageName $imageName |
           Add-AzureProvisioningConfig -Linux -LinuxUser "localadmin" -Password "Local123" |
           Set-AzureSubnet "data"
        }

        New-AzureVM -ServiceName $serviceName -VNetName $vnetName -VMs $VMList

        #Create internal load balancer
        Add-AzureInternalLoadBalancer -ServiceName $serviceName -InternalLoadBalancerName $ilbName -SubnetName "data" -StaticVNetIPAddress "$ilbIP"
        Write-Host "Created $ilbName"
        #Add add the thrift endpoint to the internal load balancer for all the VMs
        foreach($vmName in $vmNames)
        {
            Get-AzureVM -ServiceName $serviceName -Name $vmName |
                Add-AzureEndpoint -Name $thriftEndPoint -LBSetName "ThriftLBSet" -Protocol tcp -LocalPort 9160 -PublicPort 9160 -ProbePort 9160 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 -InternalLoadBalancerName $ilbName |
                Update-AzureVM

            Write-Host "created $vmName"     
        }

**Passo 3: Configurar o Cassandra no cada VM**

Inicie sessão na VM e faça o seguinte:

* Edite $CASS_HOME/conf/cassandra-rackdc.properties para especificar as propriedades do centro e bastidor de dados:

       dc =EASTUS, rack =rack1

* Edite cassandra.yaml para configurar nós de propagação como abaixo:

       Seeds: "10.1.2.4,10.1.2.6,10.1.2.8,10.1.2.10"

**Passo 4: Iniciar os VMs e testar o cluster**

Inicie sessão num de nós (por exemplo, hk-c1-oeste-e.u.a.) e execute o seguinte comando para ver o estado do cluster:

       nodetool –h 10.1.2.4 –p 7199 status

Deverá visualizar a apresentação semelhante ao abaixo para um cluster de nó de 8:

<table>
<tr><th>Estado</th><th>Endereço  </th><th>Carregar   </th><th>Tokens </th><th>É o proprietário </th><th>ID de anfitrião  </th><th>Bastidor</th></tr>
<tr><th>ANULAR  </td><td>10.1.2.4   </td><td>87.81 KB   </td><td>256    </td><td>38.0%  </td><td>GUID (removido)</td><td>rack1</td></tr>
<tr><th>ANULAR  </td><td>10.1.2.5   </td><td>41.08 KB   </td><td>256    </td><td>68.9%  </td><td>GUID (removido)</td><td>rack1</td></tr>
<tr><th>ANULAR  </td><td>10.1.2.6   </td><td>55.29 KB   </td><td>256    </td><td>68.8%  </td><td>GUID (removido)</td><td>rack2</td></tr>
<tr><th>ANULAR  </td><td>10.1.2.7   </td><td>55.29 KB   </td><td>256    </td><td>68.8%  </td><td>GUID (removido)</td><td>rack2</td></tr>
<tr><th>ANULAR  </td><td>10.1.2.8   </td><td>55.29 KB   </td><td>256    </td><td>68.8%  </td><td>GUID (removido)</td><td>rack3</td></tr>
<tr><th>ANULAR  </td><td>10.1.2.9   </td><td>55.29 KB   </td><td>256    </td><td>68.8%  </td><td>GUID (removido)</td><td>rack3</td></tr>
<tr><th>ANULAR  </td><td>10.1.2.10  </td><td>55.29 KB   </td><td>256    </td><td>68.8%  </td><td>GUID (removido)</td><td>rack4</td></tr>
<tr><th>ANULAR  </td><td>10.1.2.11  </td><td>55.29 KB   </td><td>256    </td><td>68.8%  </td><td>GUID (removido)</td><td>rack4</td></tr>
</table>

## <a name="test-the-single-region-cluster"></a>Testar o Cluster única região
Utilize os passos seguintes para testar o cluster:

1.    Utilizar o Powershell comando Get-AzureInternalLoadbalancer commandlet, (por exemplo, a obter o endereço IP do Balanceador de carga interno  10.1.2.101). A sintaxe do comando é mostrado abaixo: Get-AzureLoadbalancer – ServiceName "hk-c-svc-oeste--nos" [apresenta os detalhes do Balanceador de carga interno juntamente com o seu endereço IP]
2.  Iniciar sessão no farm web VM (por exemplo, hk-B1-oeste-e.u.a.) utilizando betumes ou ssh
3.  Executar $CASS_HOME/Reciclagem/cqlsh 10.1.2.101 9160
4.  Utilize os seguintes comandos CQL para verificar se o cluster está a funcionar:

        CREATE KEYSPACE customers_ks WITH REPLICATION = { 'class' : 'SimpleStrategy', 'replication_factor' : 3 };
        USE customers_ks;
        CREATE TABLE Customers(customer_id int PRIMARY KEY, firstname text, lastname text);
        INSERT INTO Customers(customer_id, firstname, lastname) VALUES(1, 'John', 'Doe');
        INSERT INTO Customers(customer_id, firstname, lastname) VALUES (2, 'Jane', 'Doe');

        SELECT * FROM Customers;

Deverá visualizar uma apresentação como o apresentado abaixo:

<table>
  <tr><th> id_cliente </th><th> NomePróprio </th><th> Apelido </th></tr>
  <tr><td> 1 </td><td> João </td><td> Andrade </td></tr>
  <tr><td> 2 </td><td> Inês </td><td> Andrade </td></tr>
</table>

Tenha em atenção que keyspace criado no passo 4 utiliza SimpleStrategy com um replication_factor de 3. SimpleStrategy seja recomendado para os dados único centro implementações Considerando que NetworkTopologyStrategy para os dados com várias centrar híbridas. Um replication_factor de 3 irá dar-tolerância a falhas de nós.

##<a id="tworegion"> </a>Processo de implementação de região com várias
Irá tirar partido de implementação de única região concluída e repita o mesmo processo para instalar o segundo região. A chave diferença entre a implementação de uma ou várias região é a configuração de túnel VPN para a comunicação de região dependências entre; Iremos começar com a instalação de rede, aprovisionar os VMs e configurar Cassandra.

###<a name="step-1-create-the-virtual-network-at-the-2nd-region"></a>Passo 1: Criar a rede Virtual na região 2.
Inicie sessão no portal clássico do Azure e crie uma rede Virtual com a apresentação de atributos na tabela. Consulte o artigo [Configurar uma rede Virtual Cloud-Only no portal do Azure clássico](../virtual-network/virtual-networks-create-vnet-classic-pportal.md) para obter passos detalhados do processo.      

<table>
<tr><th>Nome do atributo    </th><th>Valor    </th><th>Observações</th></tr>
<tr><td>Nome    </td><td>vnet-cass-Oriente--nos</td><td></td></tr>
<tr><td>Região  </td><td>Leste dos EUA</td><td></td></tr>
<tr><td>Servidores DNS     </td><td></td><td>Ignorar esta como podemos não está a utilizar um servidor de DNS</td></tr>
<tr><td>Configurar uma VPN ponto-para-site</td><td></td><td>     Ignorar esta</td></tr>
<tr><td>Configurar uma VPN do site para o site</td><td></td><td>      Ignorar esta</td></tr>
<tr><td>Espaço de endereços   </td><td>10.2.0.0/16</td><td></td></tr>
<tr><td>IP inicial </td><td>10.2.0.0   </td><td></td></tr>
<tr><td>CIDR    </td><td>/ 16 (65531)</td><td></td></tr>
</table>

Adicione as seguintes sub-redes:
<table>
<tr><th>Nome    </th><th>IP inicial    </th><th>CIDR   </th><th>Observações</th></tr>
<tr><td>Web </td><td>10.2.1.0   </td><td>/ 24 (251)  </td><td>Sub-rede para o farm web</td></tr>
<tr><td>dados    </td><td>10.2.2.0   </td><td>/ 24 (251)  </td><td>Sub-rede para os nós de base de dados</td></tr>
</table>


###<a name="step-2-create-local-networks"></a>Passo 2: Criar redes locais
Uma rede Local no Azure rede virtual é um espaço de endereços de proxy mapas para um site remoto, incluindo uma nuvem privada ou outro região Azure. Este espaço de endereços proxy está vinculado a um gateway remoto para a rede encaminhamento para os destinos de redes à direita. Consulte o artigo [configurar um VNet VNet ligação](../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md) para as obter instruções sobre como estabelecer ligação VNET para VNET.

Crie duas redes locais pelos seguintes detalhes:

| Nome de rede | Endereço do Gateway VPN | Espaço de endereços | Observações |
| ------------ | ------------------- | ------------- | ------- |
| HK-lnet-Map-to-East-US | 23.1.1.1  | 10.2.0.0/16   | Ao criar a rede Local dar um marcador de posição endereço do gateway. O endereço do real gateway é preenchido assim que o gateway é criado. Certifique-se que o espaço de endereços corresponde exatamente o respetivos VNET remoto; Neste caso o VNET criada na região Leste dos EUA. |
| HK-lnet-Map-to-West-US | 23.2.2.2  | 10.1.0.0/16   | Ao criar a rede Local dar um marcador de posição endereço do gateway. O endereço do real gateway é preenchido assim que o gateway é criado. Certifique-se que o espaço de endereços corresponde exatamente o respetivos VNET remoto; Neste caso o VNET criada na região ocidental dos EUA. |


###<a name="step-3-map-local-network-to-the-respective-vnets"></a>Passo 3: Mapear "" Rede Local os respetivos VNETs
A partir do portal do Azure clássico, selecione cada vnet, clique em "Configurar", selecione "Ligar à rede local" e selecione as redes Local pelos seguintes detalhes:


| Rede virtual | Rede local |
| --------------- | ------------- |
| HK-vnet-oeste--nos | HK-lnet-Map-to-East-US |
| HK-vnet-Oriente--nos | HK-lnet-Map-to-West-US |


###<a name="step-4-create-gateways-on-vnet1-and-vnet2"></a>Passo 4: Criar Gateways no VNET1 e VNET2
O dashboard das redes virtuais, clique em criar GATEWAY que irá acionar o processo de aprovisionamento do gateway VPN. Depois de alguns minutos o dashboard de cada rede virtual deve mostrar o endereço do gateway real.

###<a name="step-5-update-local-networks-with-the-respective-gateway-addresses"></a>Passo 5: Atualização "Local" as redes com os endereços respetivos "Gateway"###
Edite as redes locais para substituir o endereço IP de gateway de marcador de posição com o endereço IP real dos gateways apenas aprovisionados. Utilize o mapeamento do seguinte:

<table>
<tr><th>Rede local    </th><th>Rede virtual Gateway</th></tr>
<tr><td>HK-lnet-Map-to-East-US </td><td>Gateway do hk-vnet-oeste--nos</td></tr>
<tr><td>HK-lnet-Map-to-West-US </td><td>Gateway do hk-vnet-Oriente--nos</td></tr>
</table>

###<a name="step-6-update-the-shared-key"></a>Passo 6: Atualize a chave partilhada
Utilize o seguinte script do Powershell para atualizar a chave de IPSec do gateway cada VPN [Utilize a chave de saké para ambos os gateways]: hk-lnet-map-to-west-us de - LocalNetworkSiteName do conjunto AzureVNetGatewayKey - VNetName hk-vnet-Oriente--nos - SharedKey hk-lnet-map-to-east-us de - LocalNetworkSiteName do conjunto de D9E76BKK-AzureVNetGatewayKey - VNetName hk-vnet-oeste--nos - SharedKey D9E76BKK

###<a name="step-7-establish-the-vnet-to-vnet-connection"></a>Passo 7: Estabelecer a ligação VNET para VNET
A partir do portal do Azure clássico, utilize o menu de "DASHBOARD" das redes virtuais para estabelecer ligação de gateway a gateway. Utilize os itens do menu "Ligar" na barra de ferramentas inferior. Depois de alguns minutos o dashboard deve mostrar os detalhes de ligação graficamente.

###<a name="step-8-create-the-virtual-machines-in-region-2"></a>Passo 8: Criar as máquinas virtuais na região #2
Crie a imagem Ubuntu conforme descrito na implementação de região #1 ao seguir o mesmo passos ou copiar o ficheiro VHD de imagem para a conta de armazenamento Azure localizado na região #2 e crie a imagem. Utilize esta imagem e criar a seguinte lista de máquinas virtuais para um novo serviço de nuvem hk-c-svc-Oriente--nos:


| Nome do computador | Sub-rede | Endereço IP | Conjunto de disponibilidade | Cc/bastidor | Propagar? |
| ------------ | ------ | ---------- | ---------------- | ------- | ----- |
| HK-c1-Oriente--nos | dados  | 10.2.2.4   | HK-c-um conjunto-1      | Cc = EASTUS bastidor = rack1 | Sim |
| HK-c2-Oriente--nos | dados  | 10.2.2.5   | HK-c-um conjunto-1      | Cc = EASTUS bastidor = rack1 | N  |
| HK-c3-Oriente--nos | dados  | 10.2.2.6   | HK-c-um conjunto-1      | Cc = EASTUS bastidor = rack2 | Sim |
| HK-c5-Oriente--nos | dados  | 10.2.2.8   | HK-c-um conjunto-2      | Cc = EASTUS bastidor = rack3 | Sim |
| HK-c6-Oriente--nos | dados  | 10.2.2.9   | HK-c-um conjunto-2      | Cc = EASTUS bastidor = rack3 | N  |
| HK-c7-Oriente--nos | dados  | 10.2.2.10  | HK-c-um conjunto-2      | Cc = EASTUS bastidor = rack4 | Sim |
| HK-c8-Oriente--nos | dados  | 10.2.2.11  | HK-c-um conjunto-2      | Cc = EASTUS bastidor = rack4 | N  |
| HK-B1-Oriente--nos | Web   | 10.2.1.4   | HK-w-um conjunto-1      | N/D                    | N/D |
| HK-w2-Oriente--nos | Web   | 10.2.1.5   | HK-w-um conjunto-1      | N/D                    | N/D |


Siga as instruções do mesmo como região #1, mas utilizar 10.2.xxx.xxx espaço de endereços.

###<a name="step-9-configure-cassandra-on-each-vm"></a>Passo 9: Configurar o Cassandra no cada VM
Inicie sessão na VM e faça o seguinte:

1. Editar $CASS_HOME/conf/cassandra-rackdc.properties para especificar as propriedades de centro e bastidor de dados no formato: CC = EASTUS bastidor = rack1
2. Editar cassandra.yaml para configurar nós de propagação: sementes: "10.1.2.4,10.1.2.6,10.1.2.8,10.1.2.10,10.2.2.4,10.2.2.6,10.2.2.8,10.2.2.10"

###<a name="step-10-start-cassandra"></a>Passo 10: Iniciar o Cassandra
Iniciar sessão para cada VM e iniciar Cassandra em segundo plano, executando o seguinte comando: $CASS_HOME/Reciclagem/cassandra

## <a name="test-the-multi-region-cluster"></a>Testar o Cluster de região com várias
Por agora Cassandra ter sido implementada a 16 nós com 8 nós em cada região Azure. Estes nós estão no mesmo cluster acordo com o nome do cluster comuns e a configuração de nó propagação. Utilize o seguinte processo para testar o cluster:

###<a name="step-1-get-the-internal-load-balancer-ip-for-both-the-regions-using-powershell"></a>Passo 1: Obter o período de inquérito do Balanceador de carga interno para ambas as regiões através do PowerShell
- Get AzureInternalLoadbalancer - ServiceName "hk-c-svc-oeste--nos"
- Get AzureInternalLoadbalancer - ServiceName "hk-c-svc-Oriente--nos"  

    Tenha em atenção os endereços IP (por exemplo, oeste - 10.1.2.101 Leste - 10.2.2.101) apresentados.

###<a name="step-2-execute-the-following-in-the-west-region-after-logging-into-hk-w1-west-us"></a>Passo 2: Execute o seguinte na região Costa depois de iniciar sessão para hk-B1-oeste--nos
1.    Executar $CASS_HOME/Reciclagem/cqlsh 10.1.2.101 9160
2.  Execute os seguintes comandos CQL:

        CREATE KEYSPACE customers_ks
        WITH REPLICATION = { 'class' : 'NetworkToplogyStrategy', 'WESTUS' : 3, 'EASTUS' : 3};
        USE customers_ks;
        CREATE TABLE Customers(customer_id int PRIMARY KEY, firstname text, lastname text);
        INSERT INTO Customers(customer_id, firstname, lastname) VALUES(1, 'John', 'Doe');
        INSERT INTO Customers(customer_id, firstname, lastname) VALUES (2, 'Jane', 'Doe');
        SELECT * FROM Customers;

Deverá visualizar uma apresentação como o apresentado abaixo:

| id_cliente | NomePróprio | Apelido |
| ----------- | --------- | -------- |
| 1           | João      | Andrade      |
| 2           | Inês      | Andrade      |


###<a name="step-3-execute-the-following-in-the-east-region-after-logging-into-hk-w1-east-us"></a>Passo 3: Execute o seguinte na região Leste depois de iniciar sessão para hk-B1-Oriente--nos:
1.    Executar $CASS_HOME/Reciclagem/cqlsh 10.2.2.101 9160
2.  Execute os seguintes comandos CQL:

        USE customers_ks;
        CREATE TABLE Customers(customer_id int PRIMARY KEY, firstname text, lastname text);
        INSERT INTO Customers(customer_id, firstname, lastname) VALUES(1, 'John', 'Doe');
        INSERT INTO Customers(customer_id, firstname, lastname) VALUES (2, 'Jane', 'Doe');
        SELECT * FROM Customers;

Deverá visualizar a apresentação mesmo conforme visto região Costa:


| id_cliente | NomePróprio | Apelido   |
|------------ | --------- | ---------- |
| 1           | João      | Andrade        |
| 2           | Inês      | Andrade        |


Executar alguns insere mais e ver aqueles obtenham replicadas oeste-parte do cluster-nos.

## <a name="test-cassandra-cluster-from-nodejs"></a>Teste Cassandra Cluster de Node.js
Utilizar um do VMs Linux criado no "web" camada anteriormente, podemos executará um script Node.js simples para ler os dados anteriormente inseridos

**Passo 1: Instalar Node.js e Cassandra cliente**

1. Instalar o Node.js e npm
2. Instalar o nó pacote "cassandra-cliente" utilizar npm
3. Execute o seguinte script na linha de comandos da shell que apresenta a cadeia json dos dados obtidos:

        var pooledCon = require('cassandra-client').PooledConnection;
        var ksName = "custsupport_ks";
        var cfName = "customers_cf";
        var hostList = ['internal_loadbalancer_ip:9160'];
        var ksConOptions = { hosts: hostList,
                             keyspace: ksName, use_bigints: false };

        function createKeyspace(callback){
           var cql = 'CREATE KEYSPACE ' + ksName + ' WITH strategy_class=SimpleStrategy AND strategy_options:replication_factor=1';
           var sysConOptions = { hosts: hostList,  
                                 keyspace: 'system', use_bigints: false };
           var con = new pooledCon(sysConOptions);
           con.execute(cql,[],function(err) {
           if (err) {
             console.log("Failed to create Keyspace: " + ksName);
             console.log(err);
           }
           else {
             console.log("Created Keyspace: " + ksName);
             callback(ksConOptions, populateCustomerData);
           }
           });
           con.shutdown();
        }

        function createColumnFamily(ksConOptions, callback){
          var params = ['customers_cf','custid','varint','custname',
                        'text','custaddress','text'];
          var cql = 'CREATE COLUMNFAMILY ? (? ? PRIMARY KEY,? ?, ? ?)';
        var con =  new pooledCon(ksConOptions);
          con.execute(cql,params,function(err) {
              if (err) {
                 console.log("Failed to create column family: " + params[0]);
                 console.log(err);
              }
              else {
                 console.log("Created column family: " + params[0]);
                 callback();
              }
          });
          con.shutdown();
        }

        //populate Data
        function populateCustomerData() {
           var params = ['John','Infinity Dr, TX', 1];
           updateCustomer(ksConOptions,params);

           params = ['Tom','Fermat Ln, WA', 2];
           updateCustomer(ksConOptions,params);
        }

        //update will also insert the record if none exists
        function updateCustomer(ksConOptions,params)
        {
          var cql = 'UPDATE customers_cf SET custname=?,custaddress=? where custid=?';
          var con = new pooledCon(ksConOptions);
          con.execute(cql,params,function(err) {
              if (err) console.log(err);
              else console.log("Inserted customer : " + params[0]);
          });
          con.shutdown();
        }

        //read the two rows inserted above
        function readCustomer(ksConOptions)
        {
          var cql = 'SELECT * FROM customers_cf WHERE custid IN (1,2)';
          var con = new pooledCon(ksConOptions);
          con.execute(cql,[],function(err,rows) {
              if (err)
                 console.log(err);
              else
                 for (var i=0; i<rows.length; i++)
                    console.log(JSON.stringify(rows[i]));
            });
           con.shutdown();
        }

        //exectue the code
        createKeyspace(createColumnFamily);
        readCustomer(ksConOptions)


## <a name="conclusion"></a>Conclusão
Microsoft Azure é uma plataforma flexível que permite a execução de tanto Microsoft, bem como o abrir origem software, tal como demonstrado neste exercício. Podem ser implementados clusters de Cassandra altamente disponíveis num único centro de dados através de propagação de nós do cluster entre vários domínios falhas. Também podem ser implementados Cassandra clusters através de vários regiões Azure geograficamente distantes para sistemas de prova falhas. Azure e Cassandra em conjunto permite a construção de altamente dimensionáveis, altamente disponível e serviços em nuvem recuperáveis falhas necessário pela internet hoje dimensionar serviços.  

##<a name="references"></a>Referências##
- [http://cassandra.apache.org](http://cassandra.apache.org)
- [http://www.datastax.com](http://www.datastax.com)
- [http://www.nodejs.org](http://www.nodejs.org)
