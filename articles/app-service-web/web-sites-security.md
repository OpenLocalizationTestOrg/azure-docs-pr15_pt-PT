<properties
    pageTitle="Proteger uma aplicação no Azure de aplicação de serviço"
    description="Saiba como seguro num web app, a aplicação móvel do back-end ou a aplicação de API na aplicação de serviço de Azure."
    services="app-service"
    documentationCenter=""
    authors="cephalin"
    manager="wpickett"
    editor=""/>

<tags
    ms.service="app-service"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="multiple"
    ms.topic="article"
    ms.date="01/12/2016"
    ms.author="cephalin"/>


#<a name="secure-an-app-in-azure-app-service"></a>Proteger uma aplicação no Azure de aplicação de serviço

Este artigo ajuda-o a começar a utilizar sobre como proteger o seu web app, aplicação móvel do back-end ou aplicação API na aplicação de serviço de Azure. 

Segurança na aplicação de serviço de Azure tem dois níveis: 

- **Segurança infraestrutura e a plataforma** - confia Azure ter os serviços que precisa para executar realmente coisas segura na nuvem.
- **Segurança da aplicação** - tem de estruturar a própria aplicação em segurança. Isto inclui como integrar com o Azure Active Directory, como gerir certificados e como certificar-se de que forma segura pode falar com diferentes serviços. 

#### <a name="infrastructure-and-platform-security"></a>Infraestrutura e a plataforma de segurança
Uma vez que o serviço de aplicação mantém o VMs Azure, armazenamento, ligações de rede, quadros web, gestão e as funcionalidades de integração e muito mais, ativamente está protegida e temperado e analise conformidade exaustiva e verifica numa base contínua para se certificar de que:

- As aplicações de serviço de aplicação são isoladas de ambos da Internet e de recursos Azure outros clientes.
- Comunicação de segredos (por exemplo, as cadeias de ligação) entre a sua aplicação de aplicação de serviço e outros recursos Azure (por exemplo, base de dados SQL) num grupo de recursos permanece dentro do Azure e não cross qualquer dos limites da rede. Segredos sempre são encriptados.
- Todas as comunicações entre o seu aplicação de serviço de aplicações e recursos externos, como a gestão do PowerShell, interface da linha de comandos, Azure SDK, REST APIs e ligações de híbrido, corretamente são encriptados.
- ameaça de 24 horas protege a gestão de recursos do serviço de aplicação de software maligno, distribuído negação de serviço (DDoS), homem-em-de-meio (MITM) e outras ameaças. 

Para mais informações sobre a segurança infraestrutura e a plataforma no Azure, consulte o artigo [Centro de fidedignidade do Azure](/support/trust-center/security/).

#### <a name="application-security"></a>Segurança de aplicações

Enquanto Azure é responsável por proteger a infraestrutura e a plataforma que a aplicação é executada no, é da sua responsabilidade para proteger a sua própria aplicação. Por outras palavras, precisa de desenvolver, implementar e gerir o seu código da aplicação e o conteúdo de forma segura. Sem isto, o código da aplicação ou conteúdo ainda pode estar vulnerável a ameaças tais como:

- Introdução de SQL
- Utilização indevida de sessões
- Publicação em sites scripting
- Nível de aplicação MITM
- Nível de aplicação DDoS

É um debate completo das considerações de segurança para aplicações baseadas na web para além do âmbito deste documento. Como ponto de partida para obter mais orientações sobre como proteger a sua aplicação, consulte o artigo o, [Aplicação do Abrir Web Project segurança (OWASP)](https://www.owasp.org/index.php/Main_Page), especificamente as [10 principais projecto.](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project), que enumera as atual superior 10 web crítico aplicação falhas de segurança, tal como é determinado por membros do OWASP.

## <a name="perform-penetration-testing-on-your-app"></a>Executar realizados testes na sua aplicação

Uma das formas mais fácil para começou com testes de vulnerabilidades na sua aplicação de serviço de aplicação é utilizar a [integração com segurança Tinfoil](/blog/web-vulnerability-scanning-for-azure-app-service-powered-by-tinfoil-security/) para executar com um clique vulnerabilidade análise na sua aplicação. Pode ver os resultados dos testes num relatório de fácil compreender e saiba como corrigir cada vulnerabilidade com instruções passo a passo.

Se preferir para efetuar a suas própria testes de penetração ou pretende utilizar outro conjunto de aplicações do scanner ou fornecedor, tem de seguir o [processo de aprovação testes de penetração Azure](https://security-forms.azure.com/penetration-testing/terms) e obter aprovação prévia para executar os testes de penetração pretendida.

##<a name="https"></a>Proteger as comunicações com os clientes

Se utilizar o ** \*. azurewebsites.net** nome de domínio criado para a sua aplicação de serviço de aplicação, pode utilizar imediatamente HTTPS, conforme for fornecido um certificado SSL para todas as ** \*. azurewebsites.net** nomes de domínio. Se utiliza o seu site de um [nome de domínio personalizado](web-sites-custom-domain-name.md), pode carregar um certificado SSL para o [Ativar HTTPS](web-sites-configure-ssl-certificate.md) para o domínio personalizado.

Ativar [HTTPS](https://en.wikipedia.org/wiki/HTTPS) pode ajudar a proteger contra ataques MITM sobre a comunicação entre a sua aplicação e os seus utilizadores.

## <a name="secure-data-tier"></a>Camada segura de dados

Aplicação de serviço altamente integra-se com a base de dados SQL, assim que todas as cadeias de ligação são encriptadas ao longo do tabuleiro e apenas são desencriptar na VM que a aplicação é executada no *e* apenas quando a aplicação é executado. Além disso, a base de dados do SQL Azure inclui muitas funcionalidades de segurança para ajudar a proteger os seus dados de aplicação a partir de ameaças cyber, incluindo [encriptação no resto](https://msdn.microsoft.com/library/dn948096.aspx), [Sempre encriptados](https://msdn.microsoft.com/library/mt163865.aspx), [Masking dinâmico de dados](../sql-database/sql-database-dynamic-data-masking-get-started.md)e [Ameaça deteção](../sql-database/sql-database-threat-detection-get-started.md). Se tiver dados sensíveis ou requisitos de conformidade, consulte o artigo [proteger a sua base de dados do SQL](../sql-database/sql-database-security.md) para obter mais informações sobre como proteger os seus dados.

Se utilizar um fornecedor de base de dados de terceiros, como ClearDB, deverá consultar a documentação do fornecedor diretamente no melhores práticas de segurança.  

##<a name="develop"></a>Desenvolvimento seguras e implementação

### <a name="publishing-profiles-and-publish-settings"></a>Publicação de perfis e definições de publicação

Quando desenvolver aplicações, efetuar tarefas de gestão ou automatizar tarefas utilizando os utilitários como o **Visual Studio**, **Matriz Web**, **Azure PowerShell** ou a **Interface de comandos do Azure (Azure CLI)**, pode utilizar um ficheiro de *definições de publicação* ou um *perfil de publicação*. Ambos os tipos de ficheiro autenticar o utilizador com Azure e, devem ser protegidos para evitar acesso não autorizado.

* Contém um ficheiro de **definições de publicação**

    * O ID da subscrição Azure

    * Um certificado de gestão que permite-lhe efetuar tarefas de gestão para a sua subscrição *sem ter de fornecer um nome de conta ou palavra-passe*.

* Contém um ficheiro de **perfil de publicação**

    * Informações para publicação para a sua aplicação

Se utilizar um utilitário que utiliza um ficheiro de definições de publicar ou um ficheiro de perfil de publicar, importe o ficheiro que contém as definições de publicar ou perfil para o utilitário e, em seguida, **elimine** o ficheiro. Se tem de manter o ficheiro, para partilhar com outras pessoas que trabalham no projeto, por exemplo, guarde-o numa localização segura tais como um diretório *encriptados* com permissões restritas.

Para além disso, deve certificar-se de que as credenciais importadas são protegidas. Por exemplo, **PowerShell do Azure** e a **Interface de comandos do Azure (Azure CLI)** armazenam informação importada no seu **diretório** (*~* nos sistemas de Linux ou OS X e */users/yourusername* em sistemas Windows.) Para segurança adicional, pode optar por **encriptar** estas localizações utilizando ferramentas de encriptação disponíveis para o seu sistema operativo.

### <a name="configuration-settings-and-connection-strings"></a>Definições de configuração e cadeias de ligação
É recomendado comuns para armazenar as cadeias de ligação, as credenciais de autenticação e outras informações confidenciais nos ficheiros de configuração. Infelizmente, estes ficheiros podem ser apresentados no seu Web site ou selecionados para um repositório de público, expor estas informações. Uma pesquisa simple no [GitHub](https://github.com), por exemplo, pode descobrir inúmeros ficheiros de configuração com segredos expostos nos repositórios públicos.

É a melhor prática manter estas informações terminar ficheiros de configuração da sua aplicação. Serviço de aplicação permite-lhe armazenar informações sobre a configuração como parte do seu ambiente runtime como **as definições da aplicação** e **cadeias de ligação**. Os valores são expostos à aplicação no runtime através de *variáveis de ambiente* de para a maioria dos linguagens de programação. Para aplicações do .NET, estes valores são inseridos para a configuração do .NET o tempo de execução. Para além das seguintes situações, estas definições de configuração permanecerá encriptadas a menos que visualizar ou configurá-los utilizando o [Portal do Azure](https://portal.azure.com) ou os utilitários como PowerShell ou o clip do Azure. 

Armazenar informações sobre a configuração na aplicação de serviço torna possível administrador a aplicação bloqueá para informações confidenciais para as aplicações de produção de. Os programadores podem utilizar um conjunto diferente de definições de configuração para o desenvolvimento de aplicação e as definições podem ser automaticamente substituídas pelas definições configuradas na aplicação de serviço. Nem mesmo os programadores de precisa de saber os segredos configurados para a aplicação de produção. Para mais informações sobre como configurar as definições da aplicação e cadeias de ligação na aplicação de serviço, consulte [Configurar web apps](web-sites-configure.md).

### <a name="ftps"></a>FTPS://

Aplicação de serviço de Azure fornece acesso FTP seguro para o sistema de ficheiros para a sua aplicação através de **FTPS**. Isto permite-lhe aceder com segurança o código da aplicação no web app, bem como diagnósticos registos. É recomendável que utilize sempre FTPS em vez de FTP. 

A ligação FTPS para a sua aplicação pode ser encontrada com os seguintes passos:

1. Abra o [Azure Portal](https://portal.azure.com).
2. Selecione **Procurar tudo**.
3. Pá **Procurar** , selecione **Os serviços de aplicação**.
4. Pá **Serviços de aplicação** , selecione a aplicação pretendida.
5. Pá a aplicação, selecione **todas as definições**.
6. Pá **Definições** , selecione **Propriedades**.
7. As ligações FTP e FTPS são fornecidas em pá as **Definições** . 

Para mais informações sobre FTPS, consulte o artigo [Protocolo de transferência do ficheiro](http://en.wikipedia.org/wiki/File_Transfer_Protocol).

## <a name="next-steps"></a>Próximos passos

Para mais informações sobre a segurança de plataforma do Azure, informações de elaboração de relatórios um **incidente de segurança ou abuse**, ou para informar Microsoft que irá efectuar **testes de penetração** do seu site, consulte a secção de segurança do [Centro de fidedignidade do Microsoft Azure](https://azure.microsoft.com/support/trust-center/security/)de.

Para mais informações em ficheiros de **Web. config** ou **config** nas aplicações de serviço de aplicação, consulte o artigo [Opções de configuração desbloqueados no Azure aplicação de serviço web apps](https://azure.microsoft.com/blog/2014/01/28/more-to-explore-configuration-options-unlocked-in-windows-azure-web-sites/).

Para obter informações sobre informações de registo para as aplicações de serviço de aplicação, que pode ser útil para detetar ataques, consulte o artigo [Ativar o registo de diagnóstico](web-sites-enable-diagnostic-log.md).

>[AZURE.NOTE] Se pretender começar a aplicação de serviço de Azure antes de inscrever-se para uma conta do Azure, aceda ao [Tentar aplicação de serviço](http://go.microsoft.com/fwlink/?LinkId=523751), onde imediatamente pode criar uma aplicação de starter curto na aplicação de serviço. Sem cartões de crédito necessários; sem compromissos.

## <a name="whats-changed"></a>O que é alterado

* Para um guia para a alteração de Web sites para a aplicação de serviço Consulte o artigo: [aplicação de serviço de Azure e respectivo impacto na existente dos serviços do Azure](http://go.microsoft.com/fwlink/?LinkId=529714)
