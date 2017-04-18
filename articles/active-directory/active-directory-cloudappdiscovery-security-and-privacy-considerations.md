<properties
    pageTitle="Deteção de aplicação de segurança e privacidade considerações de nuvem | Microsoft Azure"
    description="Este tópico descreve as considerações de segurança e privacidade relacionados com a deteção de aplicação na nuvem."
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
    ms.author="markusvi"/>

# <a name="cloud-app-discovery-security-and-privacy-considerations"></a>Segurança de deteção de aplicação na nuvem e considerações de privacidade

A Microsoft está empenhada em proteger a sua privacidade e proteger os seus dados, enquanto faz software e serviços que ajudam a gerir a segurança da sua organização. <br>
Vamos reconhecer que quando confiar o dos seus dados para outras pessoas, que fidedignidade requer investimentos de engenharia de segurança estritas e conhecimentos cópias de segurança.
Microsoft cumpre as diretrizes de segurança e conformidade estritamente de práticas de ciclo de vida de desenvolvimento segura de software para um serviço de funcionamento. <br>
Proteger e proteger dados são uma prioridade superior na Microsoft.

Este tópico explica como dados são recolhidos, processados e protegidos dentro de deteção de aplicação de nuvem Azure Active Directory




##<a name="overview"></a>Descrição geral

Nuvem aplicação deteção é uma funcionalidade do Azure AD e estão alojada no Microsoft Azure. <br>
O agente de ponto final de deteção de aplicação de nuvem é utilizado para recolher dados de deteção de aplicação de máquinas IT gerido. <br>
Os dados recolhidos são enviados em segurança através de um canal encriptado para o serviço de deteção de aplicação do Azure AD nuvem. <br>
Os dados de deteção de aplicação na nuvem para uma organização, em seguida, estão visíveis no portal do Azure. <br>


<center>![Como funciona a deteção de aplicação de nuvem](./media/active-directory-cloudappdiscovery-security-and-privacy-considerations/cad01.png)</center> <br>


As secções seguintes, siga o fluxo de informação e descrevem como é protegido como esta passa da sua organização para o serviço de nuvem aplicação deteção e finalmente ao portal de deteção de aplicação na nuvem.



## <a name="collecting-data-from-your-organization"></a>Recolha de dados da sua organização

Para poder utilizar a funcionalidade de deteção de aplicação na nuvem do Azure Active Directory para obter informações para as aplicações utilizadas pelos empregados na sua organização, tem de implementar pela primeira vez o agente de ponto final de deteção de aplicação do Azure AD nuvem para máquinas na sua organização.

Os administradores de inquilino Azure Active Directory (ou os respetivos delegado) podem transferir o pacote de instalação do agente do portal do Azure. O agente pode ser manualmente instalado ou instalado através de vários computadores na organização utilizar SCCM ou política de grupo.

Para obter mais instruções sobre as opções de implementação, consulte o artigo [Guia de implementação de política de grupo deteção aplicação na nuvem](http://social.technet.microsoft.com/wiki/contents/articles/30965.cloud-app-discovery-group-policy-deployment-guide.aspx).
<br>

### <a name="data-collected-by-the-agent"></a>Dados recolhidos pelo agente

Serão recolhidas as informações descritas na lista abaixo pelo agente quando é feita uma ligação para uma aplicação Web. Só serão recolhidas as informações para as aplicações que o administrador tiver configurado para deteção. <br>
Pode editar a lista de aplicações na nuvem que o agente monitoriza através de pá de deteção de aplicação da nuvem no Microsoft [Azure portal](https://portal.azure.com/), em **Definições**->**Recolha de dados**->**lista de colecções de aplicação**. Para obter mais detalhes, consulte o artigo [Introdução com nuvem aplicação deteção](http://social.technet.microsoft.com/wiki/contents/articles/30962.getting-started-with-cloud-app-discovery.aspx)
<br>
**Categoria de informações**: informações de utilizador <br>
**Descrição**: <br>
O nome de utilizador do Windows do processo que fez um pedido para a aplicação Web de destino (por exemplo: domínio \ nomedeutilizador) bem como identificador de segurança do Windows (SID) do utilizador.


**Categoria de informações**: informações de processo <br>
**Descrição**: <br>
O nome do processo que fez o pedido para a aplicação Web de destino (por exemplo: "iexplore.exe")

**Categoria de informações**: informações de computador <br>
**Descrição**: <br>
O nome de NetBIOS máquina onde está instalado o agente.

**Categoria de informações**: informações de tráfego de aplicação <br>
**Descrição**: <br>

As seguintes informações de ligação:

- A origem (computador local) e endereços IP de destino e números de porta

- O endereço IP público da organização através do qual o pedido vai saída.

- A hora do pedido de

- O volume de tráfego enviadas e recebidas

- A versão de IP (4 ou 6)

- TLS apenas para ligações: O nome do anfitrião de destino a partir da extensão de nome de servidor de indicação ou o certificado do servidor.

As seguintes informações de HTTP:

- Método (GET, publicar, etc.)

- Protocolo (HTTP/1.1, etc.)

- Cadeia de agente do utilizador

- Nome do anfitrião

- Destino URI (excluindo cadeia de consulta)

- Informações do tipo de conteúdo

- Informações de URL de referência (excluindo cadeia de consulta)



> [AZURE.NOTE] Serão recolhidas as informações de HTTP acima para todas as ligações não encriptado.
Para ligações de TLS, estas informações apenas são capturadas quando a definição de «Controlo abrangente» está ativada no portal. A definição é 'ON' por predefinição.
Para obter mais detalhes, consulte abaixo e [Introdução com nuvem aplicação deteção](http://social.technet.microsoft.com/wiki/contents/articles/30962.getting-started-with-cloud-app-discovery.aspx)


Para além dos dados que o agente recolhe informações sobre a atividade de rede, também recolhe informações anónimas sobre o software e configuração do hardware, relatórios de erro e informações sobre como o agente está a ser utilizado.

<br><br>
### <a name="how-the-agent-works"></a>Como funciona o agente

A instalação do agente inclui dois componentes:

- Um componente de modo de utilizador

- Um componente de controlador do modo de núcleo (controlador de plataforma de filtragem Windows)



Quando o agente pela primeira vez é instalado armazena um certificado fidedigno específicas do computador no computador que utiliza, em seguida, para estabelecer uma ligação segura com o serviço de deteção de aplicação na nuvem. <br>
O agente obtém periodicamente configuração da política de serviço de nuvem aplicação deteção sobre esta ligação segura. <br>
A política inclui informações sobre as aplicações para o monitor do computador e de se atualizar automática deve ser ativado, entre outras coisas que na nuvem.

Como tráfego da Web é enviado e recebido no computador a partir do Internet Explorer e no Chrome, o agente de deteção de aplicação de nuvem analisa o tráfego e extrai os metadados relevante (consulte a secção **os dados recolhidos pelo agente** acima). <br>
Minuto, o agente os carregamentos pendentes os metadados recolhidos para o serviço de deteção de aplicação na nuvem através de um canal encriptado.

O componente de controlador intercepta o tráfego encriptado e insere próprio para a sequência de encriptada. Obter mais detalhes na secção de **dados de Intercepting a partir de ligações encriptadas (inspeção abrangente)** abaixo.


### <a name="respecting-user-privacy"></a>Respeitar privacidade dos utilizadores

Nosso objetivo é proporcionar administradores as ferramentas para definir o equilíbrio entre óptica detalhada em privacidade de utilizador e a utilização da aplicação conforme adequado para a sua organização. Para o efeito, fornecemos os botões seguintes na página de definições no Portal:

- **Recolha de dados**: os administradores podem optar por especificar quais as aplicações ou categorias de aplicação que pretendem obter dados de deteção.

- **Inspeção abrangente**: os administradores pode optar por especificar se o agente recolhe tráfego HTTP para ligações de SSL/TLS (também conhecidos por **' inspeção abrangente '**). Mais informações sobre este na secção seguinte.

- **Opções de consentimento**: os administradores podem utilizar o portal de deteção de aplicação de nuvem escolher se pretende notificar os utilizadores de recolha de dados pelo agente e se pretende requerer que o utilizador consentimento antes do início do agente recolher dados de utilizador.

O agente de ponto final de deteção de aplicação de nuvem apenas recolhe informações descritas na secção **os dados recolhidos pelo agente** acima.


### <a name="intercepting-data-from-encrypted-connections-deep-inspection"></a>Interceptar dados a partir de ligações encriptadas (inspeção abrangente)
Tal como mencionado anteriormente, os administradores podem configurar o agente para monitorizar os dados a partir de ligações encriptadas ('inspeção abrangente'). TLS ([Segurança da camada de transporte](https://msdn.microsoft.com/library/windows/desktop/aa380516%28v=vs.85%29.aspx)) é um dos protocolos mais comuns em utilização na Internet hoje. Ao encriptá comunicação com TLS, um cliente pode estabelecer um canal de comunicação segura e privadas com um servidor web; TLS fornece protecção essencial para transmitir as credenciais de autenticação e impedir a divulgação de informações confidenciais.

Enquanto o ponto a ponto seguro canal encriptado fornecido pela TLS permite segurança importantes e proteção de privacidade, o protocolo é tiram frequentemente proveito para fins maliciosos ou nefarious. Muito, por isso, na verdade, esse TLS é, muitas vezes como "universal firewall-ignorar protocolo de". Na raiz do problema é que a maioria dos firewalls são não é possível inspecionar comunicação TLS porque os dados de camada de aplicação estão encriptados com SSL. Ao saber isto, intrusos frequentemente tirar partido TLS a payloads maliciosos para um utilizador certo de que o mesmo as firewalls camada de aplicação mais inteligentes invisuais completamente a TLS e tem de reencaminhamento simplesmente comunicação TLS entre anfitriões. Os utilizadores finais frequentemente tirar partido de TLS para ignorar aceder a controlos imposta pela sua empresa firewalls e os servidores proxy, utilizá-lo para ligar para o públicos proxies e para protocolos que não sejam TLS através da firewall que caso contrário, poderá estar bloqueado por política de túnel.

Inspeção abrangente permite que o agente de deteção de aplicação na nuvem funcionar como um fidedigno homem no-de-ao centro. Quando é efetuado um pedido de cliente para aceder a um recurso HTTPS protegido, o controlador de agente de ponto final intercepta a ligação e estabelece que uma nova ligação ao servidor de destino para obtém o certificado SSL em nome do cliente. Verifica se o agente de que o certificado pode ser considerado fidedigno (pelo verificar que não foi revogado e efetuar outros controlos de certificado) e, se estes fase, em seguida, o agente de ponto final copia as informações do certificado de servidor e cria o seu próprio certificado de servidor – conhecido como um certificado intercepção – utilizar essas informações. O certificado de intercepção é com a sessão iniciada no instantaneamente pelo agente de ponto final com um certificado de raiz, que é instalado no arquivo de certificados fidedignos Windows. Este certificado autoassinado raiz está marcado como não é possível exportar e é ACL teve administradores. Destina-se para nunca sair do computador no qual foi criada. Quando a aplicação de cliente do utilizador final recebe o certificado de intercepção,-lo será confiar porque-lo com êxito pode validar a cadeia de certificados tudo para o certificado de raiz. Este processo é principalmente transparente a partir perspectiva de um utilizador final com alguns avisos conforme descrito abaixo.

Ao ativar inspeção abrangente, o agente de ponto final de deteção de aplicação de nuvem pode desencriptar e inspecionar comunicações TLS encriptado, permitindo que o serviço para reduzir ruído e fornecer informações sobre a utilização de aplicações codificada na nuvem.

#### <a name="a-word-of-caution"></a>Uma palavra de atenção
Antes de ativar o registo inspeção abrangente, é aconselhável que comunicar a operação aos seus legal e departamentos de recursos humanos e obter o seu consentimento. Da inspeção de comunicações de encriptadas privado do utilizador final, pode ser um assunto sensível, por razões óbvios. Antes de um rollup de produção-out da inspeção abrangente, certifique-se a segurança da empresa e políticas de utilização aceitável tiverem sido atualizadas para indicar que comunicação encriptada será que seja inspeccionada. Notificação de utilizador e isenção de sites consideradas confidenciais (por exemplo, bancários e dos médicos sites) também podem ser necessários caso tenha configurado o Cloud deteção de aplicação para monitorizá-los. Tal como mencionado acima, os administradores podem utilizar o portal de deteção de aplicação na nuvem para escolher a notificar utilizadores da coleção de dados pelo agente e se pretende requerer consentimento utilizador antes do agente inicia a recolha de dados de utilizador.

### <a name="known-issues-and-drawbacks"></a>Problemas conhecidos e desvantagens
Existem alguns casos onde intercepção TLS pode ter um impacto a experiência de utilizador final:

- Os certificados de validação (EV) expandida compõem a barra de endereço do browser verde para funcionar como uma pista visual que está a visitar um web site fidedigno. Inspeção TLS não é possível duplicar EV no certificado de problemas para o cliente, para que a web sites que utilizam os certificados EV irá funcionar normalmente mas barra de endereço não serão apresentados a verde.  

- Afixar chave do público (também conhecido como afixação de certificado) foram concebidos para o ajudar a proteger os utilizadores de ataques de man no meio e sem controlo as autoridades de certificação. Quando o certificado de raiz para um site afixado não corresponder a um da AC boa conhecida, browser rejeita a ligação com um erro. Uma vez que intercepção TLS, na verdade, um homem-em-de-meio, estas ligações irão falhar.

- Se os utilizadores clicam no ícone de cadeado o browser de barra de endereço do browser para inspecionar as informações do site, não verá uma cadeia que termine em autoridade de certificação utilizada para assinar o certificado de Web site, mas em vez disso, uma cadeia de certificados com o Windows fidedignos arquivo de certificados.

Para reduzir as ocorrências destes problemas, recomendamos manter registo dos serviços em nuvem aplicações de cliente conhecidas utilizar validação adicional ou a afixação de chave pública e indicar o agente de ponto final para evitar interceptar ligações afetadas. Mesmo nestes casos, no entanto, continuará a receber relatórios sobre a utilização destas aplicações na nuvem e o volume de dados a serem transferidos, mas uma vez que não são abrangente seja inspecionado, não existem detalhes sobre como as aplicações foram utilizadas estará disponíveis.


## <a name="sending-data-to-cloud-app-discovery"></a>Enviar dados para a nuvem deteção de aplicação

Assim que foi cobrado pelo agente de metadados, que esteja em cache no computador para um minuto ou até que os dados em cache atinge um tamanho de 5MB. Em seguida, são comprimido e enviado através de uma ligação segura para o serviço de deteção de aplicação na nuvem.

Se o agente não conseguir comunicar com o serviço de nuvem aplicação deteção por qualquer motivo, os metadados recolhidos são armazenados na cache ficheiro local que só pode ser acedida por utilizadores privilegiados no computador (como o grupo de administradores). <br>
O agente tenta automaticamente reenviar os metadados em cache até com êxito foi recebida pelo serviço na nuvem deteção de aplicação.



## <a name="receiving-the-data-at-the-service-end"></a>Receber os dados no final de serviço

Os agentes autenticam-se para a deteção de aplicação nuvem serviço utilizando o certificado de autenticação de cliente específico de máquina referenciado acima e encaminha os dados ao longo de um canal encriptado. <br>
Pipeline de análise do serviço de nuvem aplicação deteção processa os metadados de cada cliente em separado por logicamente criar a partições-lo através de todas as fases do pipeline de análise.
Os metadados analisados unidades vários relatórios no portal.

Os metadados não transformados e os metadados analisados são armazenados 180 dias. Além disso, podem escolher clientes capturar os metadados analisados numa conta de armazenamento de Blobs do Azure da sua escolha.
Isto é útil para análise offline de metadados, bem como mais longa retenção dos dados.

## <a name="accessing-the-data-using-the-azure-portal"></a>Aceder aos dados através do portal Azure

Num esforço para manter os metadados recolhidos seguro, por predefinição, apenas os administradores globais do inquilino têm acesso à funcionalidade de deteção de aplicação da nuvem no portal do Azure. <br>
No entanto, os administradores podem optar por esta acesso a outros utilizadores ou grupos delegado.



> [AZURE.NOTE] Para obter mais detalhes, consulte o artigo [Introdução com nuvem aplicação deteção](http://social.technet.microsoft.com/wiki/contents/articles/30962.getting-started-with-cloud-app-discovery.aspx)

<br>
Qualquer utilizador aceder aos dados no portal do, deve estar licenciado com uma licença do Azure AD Premium.



##<a name="additional-resources"></a>Recursos adicionais


* [Como pode descobrir as aplicações de nuvem unsanctioned que são utilizadas na minha organização](active-directory-cloudappdiscovery-whatis.md)
* [Índice de artigo para gestão de aplicações no Azure Active Directory](active-directory-apps-index.md)
