<properties
    pageTitle="Perguntas mais frequentes do Azure autenticação Multifator"
    description="Fornece uma lista de perguntas mais frequentes e respostas relacionados com a autenticação Multifator de Azure. Autenticação Multifator é um método de verificar a identidade de um utilizador que necessite de mais do que um nome de utilizador e palavra-passe. Fornece uma camada adicional de segurança para o início de sessão no utilizador e operações."
    services="multi-factor-authentication"
    documentationCenter=""
    authors="kgremban"
    manager="femila"
    editor="yossib"/>

<tags
    ms.service="multi-factor-authentication"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/13/2016"
    ms.author="kgremban"/>

# <a name="azure-multi-factor-authentication-faq"></a>Perguntas mais frequentes do Azure autenticação Multifator


Estas FAQs respondem a questões recorrentes sobre a autenticação Multifator de Azure e utilizar o serviço de autenticação Multifator, incluindo perguntas sobre o modelo de faturação e da eficiência de utilização.

## <a name="general"></a>Geral

**P: como é que o servidor de autenticação Multifator Azure lidar dados de utilizador?**

Com o servidor de autenticação Multifator, dados de utilizador são armazenados apenas nos servidores no local. Sem dados de utilizador persistentes são armazenados na nuvem. Quando o utilizador executa a verificação de dois passos, o servidor de autenticação Multifator envia dados para o serviço de nuvem Azure a autenticação Multifator para autenticação. Comunicação entre o servidor de autenticação Multifator e o serviço de nuvem autenticação Multifator utiliza Secure Sockets Layer (SSL) ou de segurança de camada de transporte (TLS) através da porta 443 saída.

Quando os pedidos de autenticação são enviadas para o serviço de nuvem, os dados são recolhidos para a autenticação e a utilização de relatórios. Campos de dados incluídos nos registos de verificação de dois passos são os seguintes:

- **ID exclusivo** (o nome ou no local Multifator autenticação servidor ID de utilizador)
- **Nome próprio e apelido** (opcional)
- **Endereço de e-mail** (opcional)
- **Número de telefone** (quando utilizar uma chamada de voz ou a autenticação do SMS)
- **Token de dispositivo** (quando utilizar a aplicação móvel autenticação)
- **Modo de autenticação**
- **Resultado de autenticação**
- **Nome do servidor de autenticação Multifator**
- **Servidor de autenticação Multifator IP**
- **Cliente IP** (se disponível)

Os campos opcionais podem ser configurados de servidor de autenticação Multifator.

O resultado de verificação (sucesso ou negação) e o motivo se negado, é armazenada com os dados de autenticação e está disponíveis em relatórios de autenticação e a utilização.


## <a name="billing"></a>Faturação

A maioria das perguntas de faturaçãohttps podem ser atendidas ao referir-se para a [página de preços de autenticação Multifator](https://azure.microsoft.com/pricing/details/multi-factor-authentication/).

**P: a minha organização cobrada por chamadas telefónicas ou mensagens de texto utilizada para autenticar os meus utilizadores?**

As organizações não são cobradas por individuais chamadas telefónicas colocadas ou texto mensagens enviadas para os utilizadores através de autenticação Multifator de Azure. Poderão ser cobrados proprietários de telefone para chamadas telefónicas ou mensagens de texto que recebam, de acordo com o serviço de telefone pessoal.

**P: faz o encargo de modelo faturação por utilizador com base no número de utilizadores que estão configurados para utilizar a autenticação Multifator ou o número de utilizadores que executar verificações?**

Faturação é baseada no número de utilizadores configurado para utilizar a autenticação Multifator.

**P: como funciona a faturação autenticação Multifator?**

Quando utiliza o "por utilizador" ou "por autenticação" MFA modelo, Azure for um recurso baseado em consumo. Quaisquer encargos são faturados à subscrição do Azure da organização tal como máquinas virtuais, sites públicos, etc.

Quando utiliza o modelo de licença, licenças de autenticação Multifator de Azure são compradas e, em seguida, atribuídas a utilizadores, tal como faria para Office 365 e outros produtos de subscrição.

**P: existe uma versão gratuita do Azure a autenticação Multifator para os administradores?**

Em alguns casos, Sim. Autenticação Multifator para administradores do Azure oferece um subconjunto de funcionalidades do Azure MFA sem custos. Esta oferta aplica-se aos membros do grupo Administradores globais do Azure instâncias do Azure Active Directory que não estão ligadas a um fornecedor de autenticação Multifator de Azure com base em consumo. Utilização de um fornecedor de autenticação Multifator atualiza os todos os administradores e utilizadores no diretório que estão configurados para utilizar a autenticação Multifator para a versão completa do Azure a autenticação Multifator.

**P: existe uma versão gratuita do Azure a autenticação Multifator para utilizadores do Office 365?**

Em alguns casos, Sim. Autenticação Multifator para o Office 365 oferece um subconjunto de funcionalidades do Azure MFA sem custos. Esta oferta aplica-se aos utilizadores que têm uma licença do Office 365 atribuída, quando um fornecedor de autenticação Multifator de Azure com base em consumo não foi ligado para a instância correspondente do Azure Active Directory. Utilizando o fornecedor de autenticação Multifator atualiza os todos os administradores e utilizadores no diretório que estão configurados para utilizar a autenticação Multifator para a versão completa do Azure a autenticação Multifator.

**P: a minha organização alternar entre por utilizador por autenticação consumo faturaçãohttps modelos e em qualquer altura?**

Organização escolhe um modelo de faturação quando cria um recurso. Não é possível alterar um modelo de faturação depois do recurso está aprovisionado. No entanto, pode criar outro autenticação Multifator recurso para substituir o original. Definições de utilizador e as opções de configuração não podem ser transferidas para o novo recurso.

**P: a minha organização alternar entre a faturação consumo e o modelo de licença em qualquer altura?**

Quando as licenças são adicionadas a um diretório que já tem um fornecedor de autenticação Multifator de Azure por utilizador, com base em consumo de faturação for reduzida pelo número de licenças que sejam propriedade. Se todos os utilizadores configurados para utilizar a autenticação Multifator tem licenças atribuídas, o administrador pode eliminar o fornecedor de autenticação Multifator de Azure.

Não é possível misturar faturação consumo de por autenticação com um modelo de licença. Quando um fornecedor de autenticação Multifator por autenticação está ligado a um diretório, a organização é faturada para todos os pedidos de verificação de autenticação Multifator, independentemente de quaisquer licenças de propriedade.

**P: a-se a minha organização tem de utilizar e sincronizar identidades para utilizar Azure a autenticação Multifator?**

Quando uma organização utiliza um modelo com base em consumo de faturação, não é necessário Azure Active Directory. Ligar um fornecedor de autenticação Multifator a um diretório é opcional. Se a sua organização não está ligada a um diretório, pode implementar servidor de autenticação Multifator Azure ou o Azure Multifator autenticação SDK no local.

Azure Active Directory é necessário para o modelo de licença porque licenças são adicionadas ao diretório quando comprar e atribui-las a utilizadores no diretório.


## <a name="usability"></a>Facilidade de utilização

**P: o que é que um utilizador fazer se não recebem uma resposta no seu telemóvel ou se o telefone não está disponível para o utilizador?**

Se o utilizador tiver configurado um telefone de cópia de segurança, deve tente novamente e selecione esse telemóvel quando lhe for pedido na página Iniciar sessão. Se o utilizador não tem outro método configurado, o administrador da empresa pode atualizar o número atribuído ao telefone principal do utilizador.


**P: o que faz o administrador? se o administrador sobre uma conta que o utilizador já não conseguem aceder de contactos de um utilizador**

O administrador pode repor a conta de utilizador, solicitando-lhes para voltar a trabalhar durante o processo de registo. Saiba mais sobre a [Gestão de utilizador e definições de dispositivo com a autenticação Multifator do Azure na nuvem](multi-factor-authentication-manage-users-and-devices.md).

**P: o que faz um administrador? se telemóvel de um utilizador que está a utilizar palavras-passe de aplicação for perdido ou roubado**

O administrador pode eliminar palavras-passe de aplicação do utilizador para evitar acesso não autorizado. Depois do utilizador tem um dispositivo de substituição, o utilizador possa recriar as palavras-passe. Saiba mais sobre a [Gestão de utilizador e definições de dispositivo com a autenticação Multifator do Azure na nuvem](multi-factor-authentication-manage-users-and-devices.md).

**P: o que acontece se o utilizador não é possível iniciar sessão na aplicações não baseadas no browser?**

Um utilizador que está configurado para utilizar a autenticação Multifator requer uma palavra-passe de aplicação para iniciar sessão no algumas aplicações não baseadas no browser. Um utilizador tem de limpar as informações de início de sessão no (eliminar), reinicie a aplicação e inicie sessão utilizando o respetivo nome e a aplicação palavra-passe utilizador.

Obter mais informações sobre como criar palavras-passe de aplicação e outros [ajudar com palavras-passe de aplicação](multi-factor-authentication-end-user-app-passwords.md).


>[AZURE.NOTE] Autenticação moderna para clientes do Office 2013
>
> Novo protocolos de autenticação de suporte de clientes do Office 2013 (incluindo o Outlook). Pode configurar o Office 2013 para suportar uma autenticação Multifator. Depois de configurar o Office 2013, as palavras-passe de aplicação não são necessárias para clientes do Office 2013. Para obter mais informações, consulte o [anúncio de pré-visualização público de autenticação moderna do Office 2013](https://blogs.office.com/2015/03/23/office-2013-modern-authentication-public-preview-announced/).

**P: o que faz um utilizador? se o utilizador recebe uma mensagem de texto, ou se o utilizador responde a uma mensagem de texto bidirecional mas a verificação tempo limite**

Entregar de mensagens de texto e não se garante recibo de respostas numa SMS bidireccional porque existem incontroláveis fatores que podem afetar a fiabilidade do serviço. Estes factores incluem o país de destino, carrier o número de telemóvel e a intensidade do sinal.

Os utilizadores que se sentir dificuldades sujeito a receber mensagens de texto devem seleccionar em vez disso, o método de aplicação ou chamada telefónica móvel. A aplicação móvel pode receber notificações tanto ao longo do telemóvel e ligações de Wi-Fi. Além disso, a aplicação móvel pode gerar códigos de verificação mesmo quando o dispositivo tem sem sinal de todo. A aplicação Microsoft Authenticator está disponível para [Windows Phone](http://go.microsoft.com/fwlink/?Linkid=825071), [Android](http://go.microsoft.com/fwlink/?Linkid=825072)e [IOS](http://go.microsoft.com/fwlink/?Linkid=825073).

Se tem de utilizar mensagens de texto, recomendamos que utilize SMS unidirecional em vez de SMS bidireccional sempre que possível. SMS unidirecional é mais fiável e impede que os utilizadores a partir do sempre globais taxas SMS de responder a uma mensagem de texto que tenha sido enviada noutro país.


**P: pode utilizar tokens de hardware com o servidor de autenticação Multifator Azure?**

Se estiver a utilizar o servidor de autenticação Multifator Azure, pode importar tokens de baseados no tempo, única palavra-passe (TOTP) de autenticação (JURAMENTO abrir) de terceiros e, em seguida, utilizá-los para verificação de dois passos.

Pode utilizar tokens de ActiveIdentity que são tokens JURAMENTO TOTP se colocar o ficheiro de chave secreto num ficheiro CSV e importar para o servidor de autenticação Multifator Azure. Pode utilizar tokens JURAMENTO com Federação do Active Directory serviços (ADFS), autenticação Dial-In RADIUS Remote User Service () quando o sistema de cliente pode processar as respostas de HIP do access e autenticação baseada em formulários do servidor de informação Internet (IIS).

Pode importar tokens de JURAMENTO TOTP de terceiros com os seguintes formatos:  
- Contentor de chaves simétrica (PSKC) Portable  
- CSV se o ficheiro contém um número de série, uma chave secreta no formato de Base 32 e um intervalo de tempo  

**P: pode utilizar servidor de autenticação Multifator Azure para proteger os serviços de Terminal?**

Sim, mas, se estiver a utilizar o Windows Server 2012 R2 ou posterior, apenas utilizando Gateway de ambiente de trabalho remoto (RD Gateway).

Alterações de segurança no Windows Server 2012 R2 alterou a forma como o servidor de autenticação Multifator Azure liga-se para o pacote de segurança de certificação de segurança Local (LSA) no Windows Server 2012 e versões anteriores. Para as versões dos serviços de Terminal no Windows Server 2012 ou anterior, pode [proteger uma aplicação com autenticação do Windows](multi-factor-authentication-get-started-server-windows.md#to-secure-an-application-with-windows-authentication-use-the-following-procedure). Se estiver a utilizar o Windows Server 2012 R2, terá de RD Gateway.

**P: por que motivo seria um utilizador recebe uma chamada de autenticação Multifator a partir de um autor anónimo depois de configurar o ID do autor da?**

Quando a autenticação Multifator chamadas são colocadas através da rede de telefone públicos, por vezes, que são encaminhadas através de um operador de que não suporta o ID do autor da chamada. Por esta razão, ID do autor da não se garante, apesar do sistema de autenticação Multifator sempre envia-lo.


## <a name="errors"></a>Erros

**P: o que devem utilizadores fazer se verão uma mensagem de erro "pedido de autenticação não é para uma conta devoluções" ao utilizar notificações da aplicação móvel?**

Indicar-lhes que siga este procedimento para remover a sua conta a partir da aplicação móvel, em seguida, adicioná-lo novamente:

1. Aceda ao [seu perfil portal Azure](https://account.activedirectory.windowsazure.com/profile/) e inicie sessão com a sua conta institucional.
2. Selecione a **verificação de segurança adicional**.
4. Remova a conta existente a partir da aplicação móvel.
5. Clique em **Configurar**e, em seguida, siga as instruções para reconfigurar a aplicação móvel.


**P: o que devem utilizadores fazer se verão uma mensagem de erro 0x800434D4L ao iniciar sessão para uma aplicação não baseadas no browser?**

Atualmente, um utilizador pode utilizar a verificação de segurança adicional apenas com aplicações e serviços que o utilizador pode aceder através de um browser. Aplicações de browser não (também conhecidas como *aplicações de cliente avançados*) que são instaladas no computador local, como o Windows PowerShell, não funciona com contas Pedir verificação de segurança adicional. Neste caso, o utilizador poderá ver a aplicação gerar um erro de 0x800434D4L.

Solução para este é para que o utilizador em separado contas para relacionados com o administrador e operações de que não sejam administradores. Mais tarde, pode ligar caixas de correio entre o seu conta de administrador e a conta que não sejam administradores para que pode iniciar sessão no Outlook ao utilizar a sua conta de que não sejam administrador. Para obter mais detalhes sobre este procedimento, saiba como [Permitir que um administrador a possibilidade de abrir e ver o conteúdo de caixa de correio de um utilizador](http://help.outlook.com/141/gg709759.aspx?sl=1).

## <a name="next-steps"></a>Próximos passos

Se não responder à sua pergunta aqui, deixe-nos comentários na parte inferior da página. Em alternativa, aqui estão algumas opções adicionais para obter ajuda:


**P: como posso obter ajuda com Azure a autenticação Multifator?**

- Procure na [Base de dados de conhecimento de suporte do Microsoft](https://www.microsoft.com/en-us/Search/result.aspx?form=mssupport&q=phonefactor&form=mssupport) para obter soluções para problemas técnicos comuns.

- Procurar e navegue técnicas perguntas e respostas da Comunidade ou faça a sua própria pergunta nos [fóruns do Azure Active Directory](https://social.msdn.microsoft.com/Forums/azure/newthread?category=windowsazureplatform&forum=WindowsAzureAD&prof=required).

- Se for um cliente PhoneFactor legado e tiver dúvidas ou precisa de ajuda para repor uma palavra-passe, utilize a ligação [Repor a palavra-passe](mailto:phonefactorsupport@microsoft.com) para abrir um incidente de suporte.

- Contacte um profissional de suporte através do [suporte de servidor de autenticação Multifator Azure (PhoneFactor)](https://support.microsoft.com/oas/default.aspx?prid=14947). Quando contactar-nos, é útil se pode incluir quanto informações sobre o problema possível. Pode fornecer as informações incluem a página onde a visualizou o erro, o código de erro específico, o ID da sessão específica e o ID do utilizador ao qual viu o erro.
