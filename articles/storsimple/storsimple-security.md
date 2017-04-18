<properties 
   pageTitle="Segurança StorSimple | Microsoft Azure" 
   description="Descreve as funcionalidades de segurança e privacidade que proteger o seu serviço StorSimple, dispositivo e dados no local e na nuvem." 
   services="storsimple" 
   documentationCenter="NA" 
   authors="SharS" 
   manager="carmonm" 
   editor=""/>

<tags
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD" 
   ms.date="05/03/2016"
   ms.author="v-sharos"/>

# <a name="storsimple-security-and-data-protection"></a>Proteção de segurança e dados StorSimple

## <a name="overview"></a>Descrição geral

Segurança é uma questão principal para qualquer pessoa que está a aprovar uma nova tecnologia, especialmente quando a tecnologia é utilizada com dados confidenciais ou proprietários. Como avaliar tecnologias diferentes, tem de considerar riscos maiores e os custos de proteção de dados. Microsoft Azure StorSimple fornece uma segurança e a solução de privacidade para proteção de dados, ajudando-o a garantir que: 

- **Confidencialidade** – só entidades autorizadas podem ver os seus dados. 
- **Integridade** -autorizados apenas entidades pode modificar ou eliminar os seus dados.

A solução do Microsoft Azure StorSimple é constituído pelos quatro componentes principais que interagem com os outros:

- **Serviço do Gestor de StorSimple hospedado no Microsoft Azure** – o serviço de gestão que utilizar para configurar e aprovisionar o dispositivo StorSimple.
- **Dispositivo StorSimple** – um dispositivo físico instalado no seu centro de dados. Todas as tabelas anfitriões e clientes que geram dados ligar ao dispositivo StorSimple, e o dispositivo gere os dados e move-a para a nuvem Azure conforme adequado.
- **Os clientes/anfitriões ligado ao dispositivo** – clientes na sua infraestrutura que ligam o dispositivo StorSimple e gerar os dados que tem de ser protegidos.
- **Armazenamento em nuvem** – a localização na nuvem Azure onde os dados são armazenados.

As secções seguintes descrevem as funcionalidades de segurança StorSimple que ajudam a proteger cada um dos seguintes componentes e os dados armazenados nos mesmos. Também inclui uma lista de perguntas que pode ter sobre segurança do Microsoft Azure StorSimple e as respostas correspondentes.

## <a name="storsimple-manager-service-protection"></a>Proteção de serviço do Gestor de StorSimple

O serviço do Gestor de StorSimple é um serviço de gestão alojado no Microsoft Azure e são utilizadas para gerir todos os dispositivos de StorSimple tem adquiridos a sua organização. Pode aceder ao serviço do Gestor de StorSimple utilizando as suas credenciais organizacionais para iniciar sessão no portal do Azure clássico através de um browser. 

Acesso ao serviço do Gestor de StorSimple requer que a sua organização tenha uma subscrição do Azure que inclui StorSimple. A sua subscrição controla as funcionalidades que podem aceder no portal do Azure clássico. Se a sua organização não tem uma subscrição do Azure e pretende obter mais informações, consulte o artigo [Inscrever-se no Azure como uma organização](../active-directory/sign-up-organization.md). 

Uma vez que o serviço do Gestor de StorSimple está alojado no Azure, é protegida pelas funcionalidades de segurança Azure. Para mais informações sobre as funcionalidades de segurança fornecida pela Microsoft Azure, vá para o [Centro de fidedignidade do Microsoft Azure](https://azure.microsoft.com/support/trust-center/security/).

## <a name="storsimple-device-protection"></a>Proteção de dispositivo StorSimple

O dispositivo StorSimple é um dispositivo de armazenamento de híbrido no local que contém as unidades de estado sólido (SSDs) e unidades de disco rígido (HDDs), juntamente com os controladores redundantes e capacidades de activação pós-falha automática. Os controladores de gerir o armazenamento de tiering, colocação atualmente dados utilizados (ou quentes) no armazenamento local (nos StorSimple dispositivo ou no local servidores), enquanto move menos dados utilizados com frequência para a nuvem.

Apenas autorizado StorSimple dispositivos são permitidos para aderir ao serviço de Gestor de StorSimple que criou na sua subscrição do Azure. Para autorizar um dispositivo, tem de registá-lo com o serviço do Gestor de StorSimple ao fornecer a chave de registo do serviço. A chave de registo do serviço é uma chave de aleatória de 128-bit gerada no portal do Azure clássico. 

![Chave de registo do serviço](./media/storsimple-security/ServiceRegistrationKey.png)

Para obter informações sobre como obter uma chave de registo do serviço, aceda a [passo 2: obter a chave de registo do serviço](storsimple-deployment-walkthrough.md#step-2-get-the-service-registration-key).

A chave de registo do serviço é uma chave longa que contém 100 + carateres. Pode copiar a chave e guardá-lo num ficheiro de texto numa localização segura, de modo a que pode utilizá-la para autorizar dispositivos adicionais conforme necessário. Se a chave de registo do serviço é perdida após registar o seu primeiro dispositivo, pode gerar uma nova chave de serviço StorSimple gestor. Isto não irá afectar o funcionamento dos dispositivos existentes. 

Depois de um dispositivo está registado, utiliza tokens para comunicar com o Microsoft Azure. A chave de registo do serviço não é utilizada após o registo de dispositivo.

> [AZURE.NOTE] Recomendamos que gerar a chave de registo do serviço após cada utilização.

## <a name="protect-your-storsimple-solution-via-passwords"></a>Proteger a sua solução StorSimple através de palavras-passe

As palavras-passe são um aspecto importante de segurança do computador e são utilizadas extensivamente da solução StorSimple para ajudar a garantir que os seus dados estão acessíveis a apenas os utilizadores autorizados. StorSimple permite-lhe configurar as palavras-passe seguintes:

- Palavra-passe de administrador de dispositivo StorSimple
- Desafio palavras-passe iniciador e destino de autenticação CHAP (Handshake Protocol)
- Palavra-passe StorSimple instantâneo Manager

### <a name="windows-powershell-for-storsimple-and-the-storsimple-device-administrator-password"></a>Windows PowerShell para StorSimple e a palavra-passe de administrador do dispositivo de StorSimple

O Windows PowerShell para StorSimple é uma interface de comandos que pode utilizar para gerir o dispositivo StorSimple. Windows PowerShell para StorSimple tem funcionalidades que permitem-lhe registar o seu dispositivo, configure a interface de rede no seu dispositivo, instalar determinados tipos de atualizações, resolver o seu dispositivo acedendo a sessão de suporte e altere o estado do dispositivo. Pode aceder a Windows PowerShell para StorSimple ao ligar para a série consola no dispositivo ou ao utilizar o Windows PowerShell remoto.

Pode ser feito o PowerShell remoto sobre HTTPS ou HTTP. Se estiver ativada gestão remota através de HTTPS, terá de transferir o certificado de gestão remota a partir do dispositivo de e instalá-lo no cliente remoto. Para mais informações sobre o PowerShell remoto, aceda ao [ligar remotamente ao seu dispositivo StorSimple](storsimple-remote-connect.md).

Depois de utilizar o Windows PowerShell para StorSimple para ligar para o dispositivo, terá de fornecer a palavra-passe de administrador do dispositivo para iniciar sessão no dispositivo.

![Palavra-passe de administrador do dispositivo](./media/storsimple-security/DeviceAdminPW.png)

Mantenha as melhores práticas seguintes em mente:

- Gestão remota está desativada por predefinição. Pode utilizar o serviço do Gestor de StorSimple para ativá-la. Como segurança prática recomendada, acesso remoto deve ser activado apenas durante o período de tempo que realmente é necessária.
- Se alterar a palavra-passe, certifique-se de que notifique todos os utilizadores de acesso remoto, para que não surgirem uma perda de conectividade inesperados.
- O serviço do Gestor de StorSimple não é possível obter palavras-passe existentes: só pode redefini-los. Recomendamos que armazene todas as palavras-passe num local seguro para que não tenha repor uma palavra-passe se é esquecido. Se precisar da repor uma palavra-passe, certifique-se de que notifique todos os utilizadores antes do utilizador repô-la. 

Pode aceder a interface do Windows PowerShell, utilizando uma ligação para o dispositivo série. Também pode aceder-remotamente utilizando HTTP ou HTTPS, que disponibiliza a segurança adicional. HTTPS fornece um nível superior de segurança de uma série ou de uma ligação de HTTP. No entanto, para utilizar HTTPS, tem primeiro de instalar um certificado no computador cliente que irá aceder ao dispositivo. Pode transferir o certificado de acesso remoto a partir da página de configuração de dispositivos no serviço StorSimple gestor. Se o certificado de acesso remoto é perdido, tem de transferir um novo certificado e propagá-la para todos os clientes que estejam autorizados a utilizar a gestão remota.

### <a name="challenge-handshake-authentication-protocol-chap-initiator-and-target-passwords"></a>Desafio palavras-passe iniciador e destino de autenticação CHAP (Handshake Protocol)

CHAP é um esquema de autenticação utilizado pelo dispositivo StorSimple para validar a identidade de clientes remotos. A verificação baseia-se uma palavra-passe partilhada. CHAP pode ser unidirecional (unidireccional) ou participações (bidireccional). Com o CHAP unidirecional, o destino (o dispositivo StorSimple) autentica um iniciador (anfitrião). CHAP comum ou inversa requer que o destino autenticar o iniciador e, em seguida, o iniciador autenticar o destino. O StorSimple pode ser configurado para utilizar qualquer um dos métodos.

Tenha em atenção o seguinte quando configurar CHAP:

- O nome de utilizador CHAP tem de conter menos de 233 carateres.
- A palavra-passe CHAP tem de estar entre 12 e 16 carateres. Tentar utilizar um nome de utilizador mais longo ou palavra-passe irá resultar numa falha de autenticação no anfitrião do Windows.
- Não pode utilizar a mesma palavra-passe para o iniciador CHAP e de destino CHAP.
- Depois de definir a palavra-passe, pode ser alterado, mas não é possível recuperá-lo. Se a palavra-passe for alterada, certifique-se de que notifique todos os utilizadores de acesso remoto, para que estes possam com êxito ligar para o dispositivo StorSimple.

Para mais informações sobre CHAP e como configurá-lo para a sua solução StorSimple, vá para [Configurar CHAP para o seu dispositivo StorSimple](storsimple-configure-chap.md).

### <a name="storsimple-snapshot-manager-password"></a>Palavra-passe StorSimple instantâneo Manager

Gestor de instantâneo StorSimple é um snap-in Consola de gestão da Microsoft (MMC) que utiliza grupos de volume e o serviço de cópia de sombra de Volume do Windows para gerar cópias de segurança aplicação consistente. Além disso, pode utilizar o Gestor de instantâneo StorSimple criar cópia de segurança agendas e clonar ou restaurar volumes.

Quando configurar um dispositivo para utilizar o Gestor de instantâneo StorSimple, será necessário para fornecer a palavra-passe StorSimple instantâneo gestor. Esta palavra-passe pela primeira vez definido no Windows PowerShell para StorSimple durante o registo. Também pode ser definir e mudou do serviço do Gestor de StorSimple a palavra-passe. Esta palavra-passe autentica o dispositivo com o Gestor de instantâneo StorSimple.

![Palavra-passe StorSimple instantâneo Manager](./media/storsimple-security/SnapshotMgrPassword.png)

A palavra-passe StorSimple instantâneo gestor tem de ser 14 a 15 carateres e tem de conter 3 ou mais de uma combinação de carateres em maiúsculas, minúsculas, numéricos e especiais. Depois de definir a palavra-passe StorSimple instantâneo Gestor, pode ser alterado, mas não é possível recuperá-lo. Se alterar a palavra-passe, certifique-se de que notificar todos os utilizadores remotos.

Para obter mais informações sobre o Gestor de instantâneo StorSimple, aceda a [o que é o Gestor de instantâneo StorSimple?](storsimple-what-is-snapshot-manager.md)

### <a name="password-best-practices"></a>Melhores práticas de palavra-passe

Recomendamos que utilize as seguintes diretrizes para o ajudar a garantir que as palavras-passe de StorSimple são forte e bem protegido:

- Altere as palavras-passe em três meses. Alterar as palavras-passe é aplicada todos os anos.
- Utilize palavras-passe seguras. Para obter mais informações, aceda a [criar palavras-passe fortes e protegê-las](http://blogs.microsoft.com/cybertrust/2014/08/25/create-stronger-passwords-and-protect-them/).
- Utilizar sempre as palavras-passe diferentes para mecanismos de acesso diferente; cada um das palavras-passe que especificou deve ser exclusiva.
- Não partilhe a palavras-passe com qualquer pessoa que não está autorizado para aceder ao dispositivo StorSimple.
- Não falar sobre uma palavra-passe à frente das outras pessoas nem aparecer no formato de uma palavra-passe.
- Se suspeitar que uma conta ou palavra-passe comprometida, comunicar o incidente ao seu departamento de segurança de informações.
- Trate todas as palavras-passe como as informações confidenciais, confidenciais. 

## <a name="storsimple-data-protection"></a>Proteção de dados StorSimple

Esta secção descreve as funcionalidades de segurança de StorSimple proteger dados estão em trânsito e dados armazenados.

Conforme descrito em outras secções, palavras-passe são utilizadas para autorizar e autenticar os utilizadores antes que podem aceder à sua solução StorSimple. Considerações de segurança outra está a proteger dados a partir de utilizadores não autorizados enquanto está a ser transferida entre os sistemas de armazenamento e enquanto estiver a ser armazenado. As secções seguintes descrevem as funcionalidades de proteção de dados fornecidas com o StorSimple.

> [AZURE.NOTE] Eliminação de duplicados fornece protecção adicional para os dados armazenados no dispositivo StorSimple e no armazenamento do Microsoft Azure. Quando são deduplicated dados, os objetos de dados são armazenados em separado de metadados utilizado para mapear e aceder aos mesmos: não existe contexto nível de armazenamento disponível para reconstruir dados com base na estrutura de volume, o sistema de ficheiros ou o nome do ficheiro.

## <a name="protect-data-flowing-through-the-service"></a>Proteger a fluir através do serviço de dados

É o objetivo principal do serviço do Gestor de StorSimple gerir e configurar o dispositivo StorSimple. O serviço do Gestor de StorSimple é executado no Microsoft Azure. Utilize o portal clássico do Azure para introduzir dados de configuração do dispositivo e, em seguida, Microsoft Azure utiliza o serviço do Gestor de StorSimple para enviar os dados para o dispositivo. StorSimple utiliza um sistema de assimétricos pares chaves para ajudar a garantir que um compromisso do serviço do Azure não irá resultar num compromisso das informações armazenadas. 

![Encriptação de dados em voo](./media/storsimple-security/DataEncryption.png)

O sistema de chave assimétrico ajuda a proteger os dados que flua através do serviço, da seguinte forma:

1. Certificado de encriptação de dados que utiliza uma chave pública e privada assimétrica par é gerado no dispositivo e é utilizado para proteger os dados. As teclas são geradas quando o primeiro dispositivo está registado. 
2. As teclas de certificado de encriptação de dados são exportadas para um ficheiro de intercâmbio de informações pessoais (. pfx) que é protegido pela chave de encriptação de dados do serviço, que é uma chave de 128-bit forte que aleatoriamente é gerada pelo primeiro dispositivo durante o registo.
3. A chave pública do certificado é segura disponibilizada para o serviço do Gestor de StorSimple e a chave privada mantém-se com o dispositivo.
4. Introduzir o serviço de dados são encriptados utilizando o público chave e desencriptado utilizando a chave privada armazenada no dispositivo, garantindo que o serviço do Azure não é possível desencriptar os dados a fluir para o dispositivo.

A chave de encriptação de dados do serviço é gerada no apenas o primeiro dispositivo registado com o serviço. Todos os dispositivos subsequentes que estão registados com o serviço tem de utilizar a mesma chave de encriptação de dados de serviço. 

> [AZURE.IMPORTANT] 
> 
> É muito importante fazer uma cópia da chave de encriptação de dados de serviço e guardá-lo numa localização segura. Deverá ser guardada uma cópia da chave de encriptação de dados de serviço de forma a que possa ser acedida por uma pessoa autorizada e podem ser facilmente comunicado para o administrador do dispositivo.
>
> Se a chave de encriptação de dados do serviço é perdida, uma pessoa de suporte da Microsoft pode ajudá-lo para obtê-la, desde que tenha pelo menos um dispositivo num estado online. Recomendamos que altere a chave de encriptação de dados do serviço depois de serem recuperado. Para obter instruções, consulte [alterar a chave de encriptação de dados do serviço](storsimple-service-dashboard.md#change-the-service-data-encryption-key).

Pode alterar a chave de encriptação de dados do serviço e o certificado de encriptação de dados correspondente ao selecionar a opção **Alterar chave de encriptação de dados de serviço** no dashboard de serviço. Para se certificar de que a segurança dos dados não está comprometida, tem de utilizar um dispositivo StorSimple físico para alterar a chave de encriptação de dados do serviço. Alterar as chaves de encriptação requer que todos os dispositivos de ser actualizado com a nova chave. Por conseguinte, recomendamos que altere a tecla quando todos os dispositivos estão onlinehttps. Se estiverem offline dispositivos, podem ser alteradas as respetivas chaves de cada vez diferente. Os dispositivos com as teclas Desatualizadas ainda conseguir executar cópias de segurança, mas não conseguirão restaurar os dados até que a tecla é atualizada. Para mais informações, aceda a [utilizar o dashboard de serviço do Gestor de StorSimple](storsimple-service-dashboard.md).

A chave de encriptação de dados do serviço e o certificado de encriptação de dados não expirar. No entanto, recomendamos que altere a chave de encriptação de dados do serviço todos os anos para ajudar a prevenir chave comprometida.

## <a name="protect-data-at-rest"></a>Proteger os dados no resto

O dispositivo StorSimple gere dados guardando-lo em camadas localmente e na nuvem, dependendo da frequência de utilização. Todos os computadores de anfitrião que estão ligados para o dispositivo enviar dados para o dispositivo que avança dados na nuvem, conforme adequado. Dados são transferidos a partir do dispositivo para a nuvem em segurança através da Internet. Cada dispositivo tem um destino de iSCSI superfícies todos os volumes partilhados nesse dispositivo. Todos os dados são encriptados antes de ter sido enviada para o cloud armazenamento. 

![Chave de encriptação de armazenamento na nuvem](./media/storsimple-security/CloudStorageEncryption.png)

Para ajudar a garantir a segurança e a integridade dos dados movidos para a nuvem, StorSimple permite-lhe definir chaves de encriptação de armazenamento na nuvem da seguinte forma:

- Especifique a chave de encriptação de armazenamento na nuvem ao criar um contentor de volume. A tecla não pode ser modificada ou adicionada mais tarde. 
- Todos os volumes num contentor de volume partilham a mesma chave de encriptação. Se pretender que uma forma diferente de encriptação para um volume específico, recomendamos que cria um novo contentor de volume para alojar esse volume.
- Quando introduz a chave de encriptação de armazenamento na nuvem no serviço StorSimple Gestor, a tecla está encriptada utilizando a parte da chave de encriptação de dados de serviço pública e, em seguida, enviada para o dispositivo.
- A chave de encriptação de armazenamento na nuvem não está armazenada em qualquer lugar no serviço e é conhecida apenas para o dispositivo.
- Especificar uma chave de encriptação de armazenamento na nuvem é opcional. Pode enviar dados que tenham sido encriptados no anfitrião para o dispositivo.

### <a name="additional-security-best-practices"></a>Melhores práticas de segurança adicionais

- Dividir o tráfego: isolar seu iSCSI SAN de tráfego de utilizador numa LAN empresarial implementar uma rede totalmente separada e utilizando VLANs onde isolamento físico não é uma opção. Uma rede dedicada iSCSI armazenamento irão garante a segurança e o desempenho dos seus dados críticos. Mistura de tráfego de armazenamento e de utilizadores através de uma LAN empresarial não é recomendado e pode aumentar a latência e causar falhas na rede.

- Para a segurança do lado do anfitrião de rede, utilize as interfaces de rede que suportam TCP/IP descarregar o motor (TOE). TOE reduz carga da CPU ao processamento TCP na placa de rede.

## <a name="protect-data-via-storage-accounts"></a>Proteger dados através de contas de armazenamento

Cada subscrição do Microsoft Azure pode criar um ou mais contas de armazenamento. (Uma conta de armazenamento proporciona um espaço de nomes exclusivo para trabalhar com dados armazenados na nuvem Azure). Acesso a uma conta de armazenamento é controlado pelas teclas de acesso e de subscrição associadas a essa conta de armazenamento. 

Quando cria uma conta de armazenamento, Microsoft Azure gera duas teclas de acesso de armazenamento de 512 bits, um dos quais é utilizado para autenticação, quando o dispositivo StorSimple acede a conta de armazenamento. Tenha em atenção que apenas um destas teclas está a ser utilizado. A chave de outros é mantida na reserva, permitindo-lhe rodar periodicamente as teclas. Para rodar chaves, tornar a chave secundária ativa e, em seguida, elimine a chave primária. Em seguida, pode criar uma nova chave para utilizar durante a rotação seguinte. (Por motivos de segurança, muitos centros de dados necessitam de rotação da chave.) 

Recomendamos que segue estes procedimentos recomendados para a rotação da chave:

- Deve rodar teclas de conta de armazenamento regularmente para ajudar a garantir que a sua conta de armazenamento não é acedida por utilizadores não autorizados.
- O administrador do Azure periodicamente, deverá alterar ou gerar a chave primária ou secundária utilizando a secção de armazenamento do portal clássica Azure para aceder diretamente à conta de armazenamento.


## <a name="protect-data-via-encryption"></a>Proteger dados através de encriptação

StorSimple utiliza os seguintes algoritmos de encriptação para proteger dados armazenados num ou em viagem entre os componentes da sua solução StorSimple.

| Algoritmo de | Comprimento da chave | Protocolos/aplicações/comentários |
| --------- | ---------- | ------------------------------- |
| RSA       | 2048       | 1.5 RSA PKCS 1 é utilizado pelo portal clássico do Azure para encriptar dados de configuração que são enviados para o dispositivo: por exemplo, o armazenamento de credenciais, configuração do dispositivo StorSimple, de conta e chaves de encriptação de armazenamento em nuvem. |
| AES       | 256        | AES com CBC é utilizado para encriptar a parte da chave de encriptação de dados de serviço pública antes de ter sido enviada para o portal clássico Azure dispositivo StorSimple. Também é utilizado pelo dispositivo StorSimple para encriptar dados antes dos dados são enviados para a conta de armazenamento na nuvem. |


## <a name="storsimple-virtual-device-security"></a>Segurança do dispositivo virtual de StorSimple

[AZURE.INCLUDE [storsimple virtual device security](../../includes/storsimple-virtual-device-security.md)]

## <a name="frequently-asked-questions-faq"></a>Perguntas mais frequentes (FAQ)

Seguem-se algumas perguntas e respostas sobre segurança e Microsoft Azure StorSimple.

**Q:** O meu serviço está comprometido. O que devem ser meu passos seguintes?

**A:** Deverá alterar a chave de encriptação de dados do serviço e as teclas de conta de armazenamento da conta de armazenamento que está a ser utilizado para os dados tiering imediatamente. Para obter instruções, vá para: 

- [Alterar a chave de encriptação de dados de serviço](storsimple-service-dashboard.md#change-the-service-data-encryption-key)
- [Rotação da chave de contas de armazenamento](storsimple-manage-storage-accounts.md#key-rotation-of-storage-accounts)

**Q:** Tenho de ter um novo dispositivo StorSimple que está a pedir a chave de registo do serviço. Como posso obtê-lo?

**A:** Esta chave foi criada quando criou o serviço do Gestor de StorSimple pela primeira vez. Quando utiliza o serviço do Gestor de StorSimple para ligar para o dispositivo, pode utilizar a página de guia de introdução do serviço para ver ou gerar a chave de registo do serviço. Gerar uma nova chave de registo de serviço não irão afetar os dispositivos registados existentes. Para obter instruções, vá para:

- [Ver ou gerar a chave de registo do serviço](storsimple-service-dashboard.md#view-or-regenerate-the-service-registration-key)

**Q:** Perdi a minha chave de encriptação de dados de serviço. O que posso fazer?

**A:** Contacte o suporte da Microsoft. Pode iniciar sessão para uma sessão de suporte no seu dispositivo e ajuda a obter a chave (desde que pelo menos um dispositivo estiver online). Imediatamente depois de obter a chave de encriptação de dados do serviço, deverá alterar-a para assegurar que a nova chave é conhecida apenas para si. Para obter instruções, vá para:

- [Alterar a chave de encriptação de dados de serviço](storsimple-service-dashboard.md#change-the-service-data-encryption-key)

**Q:**  Autorizados um dispositivo para que uma alteração de chave de encriptação do serviço dados, mas não iniciou o processo de alteração da chave. O que devo fazer?

**A:** Se o período de tempo limite tiver expirado, terá de autorizar o dispositivo para que a alteração de chave de encriptação do serviço dados e iniciar o processo novamente.

**Q:**  Alterei a chave de encriptação de dados do serviço, mas não foi possível atualizar os outros dispositivos dentro de 4 horas. Tenho de ter que começar novamente?

**A:** O período de tempo de 4 horas é apenas para iniciar a alteração. Depois de iniciar o processo de atualização no dispositivo StorSimple autorizado, a autorização é válida até que todos os dispositivos são atualizados.

**Q:** Os nossos StorSimple administrador tiver saído da empresa. O que devo fazer?

**A:** Alterar e repor palavras-passe que permita o acesso ao dispositivo StorSimple e alterar a chave de encriptação de dados do serviço para se certificar de que as novas informações não são conhecidas a pessoas não autorizadas. Para obter instruções, vá para:

- [Utilizar o serviço do Gestor de StorSimple para alterar as palavras-passe storsimple](storsimple-change-passwords.md)
- [Alterar a chave de encriptação de dados de serviço](storsimple-service-dashboard.md#change-the-service-data-encryption-key)
- [Configurar CHAP para o seu dispositivo StorSimple](storsimple-configure-chap.md)

**Q:** Pretendo fornecer a palavra-passe do Gestor de instantâneo StorSimple para um anfitrião que está a ligar para o dispositivo StorSimple, mas a palavra-passe não está disponível. O que posso fazer?

**A:** Se tiver esquecido a palavra-passe, deve criar um novo. Em seguida, certifique-se de que informar todos os utilizadores existentes que foi alterada a palavra-passe e que atualizam os seus clientes para utilizar a nova palavra-passe. Para obter instruções, vá para:

- [Alterar a palavra-passe StorSimple instantâneo Gestor](storsimple-change-passwords.md#change-the-storsimple-snapshot-manager-password)
- [Autenticar um dispositivo](storsimple-snapshot-manager-manage-devices.md#authenticate-a-device)

**Q:** O certificado para acesso remoto para o Windows PowerShell para StorSimple ter sido alterado no dispositivo. Como atualizo o meu clientes de acesso remoto?

**A:** Pode transferir o novo certificado de serviço StorSimple Manager e, em seguida, envie-o para ser instalado no arquivo de certificados seus clientes de acesso remoto. Para obter instruções, vá para:

- [Cmdlet do certificado de importação](https://technet.microsoft.com/library/hh848630.aspx)

**Q:** É o meu se protegido de dados o Gestor de StorSimple serviço está comprometido?

**A:** Dados de configuração do serviço estão encriptados sempre com a sua chave pública ao visualizar num browser. Uma vez que o serviço não tem acesso à chave privada, o serviço não conseguir ver todos os dados. Se o serviço do Gestor de StorSimple está comprometido, não existe nenhuma impacto, à medida que não existem chaves armazenadas no serviço StorSimple gestor.

**Q:** Se alguém acesso ilegítimo ao certificado de encriptação de dados, irão os meus dados ser comprometidos?

**A:** Microsoft Azure armazena chave de encriptação de dados do cliente (ficheiro. pfx) num formato encriptado. Uma vez que o ficheiro. pfx está encriptado e o serviço de StorSimple não tem a chave de encriptação de dados do serviço para desencriptar o ficheiro. pfx, a introdução simplesmente o acesso ao ficheiro. pfx não expõem qualquer segredos.

**Q:** O que acontece se uma entidade governamental pedir Microsoft os meus dados?

**A:** Uma vez que todos os dados são encriptados o serviço e a chave privada é mantida com o dispositivo, a entidade governamental terá de pedir ao cliente para os dados. 

## <a name="next-steps"></a>Próximos passos

[Implementar o seu dispositivo StorSimple](storsimple-deployment-walkthrough.md).
 
