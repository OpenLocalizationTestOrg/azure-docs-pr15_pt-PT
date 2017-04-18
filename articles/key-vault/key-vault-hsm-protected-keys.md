<properties
    pageTitle="Como gerar e transferir protegida HSM teclas para Azure chave Cofre | Microsoft Azure"
    description="Utilize este artigo para o ajudar a planear, gerar e, em seguida, transfira o seus próprio teclas protegida HSM para utilizar com o Azure chave cofre."
    services="key-vault"
    documentationCenter=""
    authors="cabailey"
    manager="mbaldwin"
    tags="azure-resource-manager"/>

<tags
    ms.service="key-vault"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/24/2016"
    ms.author="cabailey"/>
#<a name="how-to-generate-and-transfer-hsm-protected-keys-for-azure-key-vault"></a>Como transferência protegida HSM e gerar chaves para Cofre de chave do Azure

##<a name="introduction"></a>Introdução

Para assurance adicionado, quando utiliza o Azure chave cofre, pode importar ou gerar chaves nos módulos de segurança de hardware (Os HSMs) que nunca deixe o limite da HSM. Este cenário é muitas vezes como *Trazer o seu próprio chave*ou BYOK. Os HSMs são 140-2 nível 2 validados FIPS. Azure chave cofre utiliza família de nShield da Thales dos HSMs para proteger as suas chaves.

Utilize as informações neste tópico para o ajudar a planear, gerar e, em seguida, transfira o seus próprio teclas protegida HSM para utilizar com o Azure chave cofre.

Esta funcionalidade não está disponível para Azure China. 

>[AZURE.NOTE] Para mais informações sobre Azure chave cofre, consulte o artigo [o que é o Azure chave cofre?](key-vault-whatis.md)  
>
>Para obter um tutorial de introdução ao obter, que inclui a criação de uma chave cofre para protegida HSM chaves, consulte o artigo [Introdução ao Azure chave cofre](key-vault-get-started.md).

Mais informações sobre a gerar e transferir uma tecla protegida HSM através da Internet:

- Gerar a chave a partir de uma offline estação de trabalho, o que reduz a superfície de ataque.

- A tecla está encriptada com uma chave de Exchange chave (KEK), que permanece encriptada até serem transferida para o Azure chave cofre HSMs. Apenas a versão da chave encriptada deixa de estação de trabalho original.

- O conjunto de ferramentas configura as propriedades na sua chave de inquilino liga-se a chave para o mundo de segurança do Azure chave cofre. Por isso, depois dos Azure chave cofre HSMs receber e desencriptar a sua chave, apenas estes HSMs utilizá-lo. Não pode ser exportada a sua chave. Este enlace é imposto pelo da Thales HSMs.

- Tecla de intercâmbio de chave (KEK) que é utilizado para encriptar a sua chave é gerado dentro dos Azure chave cofre HSMs e não é exportável. Os HSMs impõem que não podem ser nenhuma versão simples do KEK fora dos HSMs. Além disso, o conjunto de ferramentas inclui certificação a partir da Thales que o KEK não é exportável e foi gerado dentro de um HSM genuine que foi produzido por da Thales.

- O conjunto de ferramentas inclui certificação a partir da Thales que o mundo de segurança do Azure chave cofre também foi gerado num HSM genuine produzido por da Thales. Este certificado prova que a Microsoft utiliza genuine hardware.

- A Microsoft utiliza KEKs separadas e separar mundo de segurança em cada região geográfica. Esta separação garante que a chave pode ser utilizada apenas em centros de dados na região em que lhe encriptados. Por exemplo, uma chave de um cliente Europeu não pode ser utilizada nos centros de dados da América do Norte ou países da Ásia.

##<a name="more-information-about-thales-hsms-and-microsoft-services"></a>Obter mais informações sobre os serviços Microsoft e os da Thales HSMs

Segurança electrónica da Thales é um fornecedor global à esquerda de encriptação e dados cyber soluções de segurança para os serviços financeiros, alta tecnologia, fabrico, administração pública e sectores tecnologia. Com um ano de 40 registar registo das proteger empresarial e informações de administração pública, soluções da Thales são utilizadas pelo quatro dos cinco maior energia e americano empresas. As respetivas soluções também são utilizadas por países a NATO 22 e seguro mais do que 80 cêntimos das transações do pagamento em todo o mundo.

Microsoft tem vindo a colaborar com da Thales para melhorar o estado do ClipArt para os HSMs. Estes melhoramentos permitem-lhe obter as vantagens dos serviços alojados sem ceda o controlo sobre as suas chaves típicas. Especificamente, estes melhoramentos informar Microsoft gerir os HSMs para que não tenha para. Como um serviço na nuvem, Azure chave Cofre é escalas em breve aviso para cumprir picos da utilização da sua organização. Ao mesmo tempo, a sua chave de estiver protegida dentro os-HSMs da Microsoft: manter o controlo sobre o ciclo de vida chave porque gerar a chave e transferi-lo para os HSMs da Microsoft.

##<a name="implementing-bring-your-own-key-byok-for-azure-key-vault"></a>Implementar a trazer o seu próprio chave (BYOK) para Cofre de chave do Azure

Utilize as seguintes informações e procedimentos se irá gerar sua própria chave protegida HSM e, em seguida, transfira-o para Azure chave cofre — a trazer seu próprio cenário de chave (BYOK).


##<a name="prerequisites-for-byok"></a>Pré-requisitos para BYOK

Consulte a tabela seguinte para uma lista de pré-requisitos para trazer o seu próprio chave (BYOK) para Azure chave cofre.

|Requisito de registo|Obter mais informações|
|---|---|
|Uma subscrição do Azure|Para criar um cofre de chave do Azure, tem uma subscrição do Azure: [Inscrever-se para a versão de avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/)|
|A camada de serviço do Azure chave cofre Premium para suportar protegida HSM teclas|Para mais informações sobre as capacidades e camadas de serviços para Azure chave cofre, consulte o Web site [Preços do Azure chave cofre](https://azure.microsoft.com/pricing/details/key-vault/) .|
|Da Thales HSM, smart cards e software de suporte|Tem de ter acesso a um módulo de Hardware de segurança da Thales e conhecimentos operacionais básicos dos da Thales HSMs. Consulte o artigo [Da Thales módulo de segurança de Hardware](https://www.thales-esecurity.com/msrms/buy) para a lista de modelos de compatíveis ou para comprar um HSM se não tiver um.|
|A seguinte hardware e software:<ol><li>Um x64 offline estação de trabalho com uma mínimos Windows operação de sistema do Windows 7 e da Thales nShield software, pelo menos, versão 11.50.<br/><br/>Se esta estação de trabalho for executado o Windows 7, tem de [instalar o Microsoft .NET Framework 4,5](http://download.microsoft.com/download/b/a/4/ba4a7e71-2906-4b2d-a0e1-80cf16844f5f/dotnetfx45_full_x86_x64.exe).</li><li>Estação de trabalho que está ligada à Internet e tem um mínimos Windows operação de sistema do Windows 7.</li><li>Uma unidade USB ou outro dispositivo de armazenamento portátil, que tem, pelo menos, 16 MB de espaço livre.</li></ol>|Por motivos de segurança, recomendamos que a primeira estação de trabalho não está ligada a uma rede. No entanto, esta recomendação não é imposta através de programação.<br/><br/>Tenha em atenção que na instruções que se seguem, esta estação de trabalho é referida como desligada estação de trabalho.</p></blockquote><br/>Além disso, se a chave de inquilino que uma rede de produção, recomendamos que utilize uma estação de trabalho segunda, em separado para transferir o conjunto de ferramentas e carregar a chave de inquilino. Mas, para fins de teste, pode utilizar a mesma estação de trabalho como a primeira parte.<br/><br/>Tenha em atenção que na instruções que se seguem, este segunda estação de trabalho é referida como estação de trabalho ligado à Internet.</p></blockquote><br/>|

##<a name="generate-and-transfer-your-key-to-azure-key-vault-hsm"></a>Gerar e transferir a sua chave para Azure chave cofre HSM

Irá utilizar os seguintes cinco passos para gerar e transferir a sua chave para um HSM do Azure chave cofre:

- [Passo 1: Preparar a estação de trabalho ligado à Internet](#step-1-prepare-your-internet-connected-workstation)
- [Passo 2: Preparar o seu desligada estação de trabalho](#step-2-prepare-your-disconnected-workstation)
- [Passo 3: Gere a sua chave](#step-3-generate-your-key)
- [Passo 4: Preparar a sua chave para transferência](#step-4-prepare-your-key-for-transfer)
- [Passo 5: Transferir a sua chave para Cofre de chave do Azure](#step-5-transfer-your-key-to-azure-key-vault)

## <a name="step-1-prepare-your-internet-connected-workstation"></a>Passo 1: Preparar a estação de trabalho ligado à Internet
Para neste primeiro passo, efetue os seguintes procedimentos na estação de trabalho que está ligado à Internet.


###<a name="step-11-install-azure-powershell"></a>Passo 1.1: Instalar o Azure PowerShell

A partir de estação de trabalho ligado à Internet, transfira e instale o módulo Azure PowerShell, que inclui os cmdlets, para gerir Azure chave cofre. Isto requer que a versão mínima do 0.8.13.

Para obter instruções de instalação, consulte o artigo [como instalar e configurar o Azure PowerShell](../powershell-install-configure.md).

###<a name="step-12-get-your-azure-subscription-id"></a>Passo 1.2: Obtenha o seu ID de subscrição Azure

Iniciar uma sessão de PowerShell do Azure e iniciar sessão na sua conta Azure utilizando o seguinte comando:

        Add-AzureAccount
Na janela do browser de pop-up, introduza o seu nome de utilizador da conta Azure e a palavra-passe. Em seguida, utilize o comando [Get-AzureSubscription](https://msdn.microsoft.com/library/azure/dn790366.aspx) :

        Get-AzureSubscription
A partir de saída, localize o ID da subscrição que irá utilizar para Azure chave cofre. Irá necessitar deste ID da subscrição mais tarde.

Não feche a janela de Azure PowerShell.

###<a name="step-13-download-the-byok-toolset-for-azure-key-vault"></a>Passo 1.3: Transferir o conjunto de ferramentas BYOK para Cofre de chave do Azure

Vá para a Microsoft Download Center e [Transferir o conjunto de ferramentas do Azure chave cofre BYOK](http://www.microsoft.com/download/details.aspx?id=45345) para a sua região geográfica ou instância do Azure. Utilize as seguintes informações para identificar o nome do pacote para transferir e do respectivo hash de pacote SHA 256 correspondente:

---

**América do Norte:**

States.zip United de KeyVault BYOK ferramentas

305F44A78FEB750D1D478F6A0C345B097CD5551003302FA465C73D9497AB4A03

---

**Europa:**

KeyVault BYOK-ferramentas Europe.zip

C73BB0628B91471CA7F9ADFCE247561C6016A5103EF1A315D49C3EA23AFC0B9C

---

**Países da Ásia:**

KeyVault BYOK-ferramentas AsiaPacific.zip

BE9A84B6C76661929F9FDAD627005D892B3B8F9F19F351220BB4F9C356694192

---

**América Latina:**

KeyVault BYOK-ferramentas LatinAmerica.zip
    
9E8EE11972DECE8F05CD898AF64C070C375B387CED716FDCB788544AE27D3D23

---

**Japão:**

KeyVault BYOK-ferramentas Japan.zip

E6B88C111D972A02ABA3325F8969C4E36FD7565C467E9D7107635E3DDA11A8B2

---

**Austrália:**

KeyVault BYOK-ferramentas Australia.zip

7660D7A675506737857B14F527232BE51DC269746590A4E5AB7D50EDD220675D

---

[**Administração pública Azure:**](https://azure.microsoft.com/features/gov/)

KeyVault BYOK-ferramentas USGovCloud.zip

53801A3043B0F8B4A50E8DC01A935C2BFE61F94EE027445B65C52C1ACC2B5E80

---

**Canadá:**

KeyVault BYOK-ferramentas Canada.zip

A42D9407B490E97693F8A5FA6B60DC1B06B1D1516EDAE7C9A71AA13E12CF1345

---

**Alemanha:**

KeyVault BYOK-ferramentas Germany.zip

4795DA855E027B2CA8A2FF1E7AE6F03F772836C7255AFC68E576410BDD28B48E

---
**Índia:**

KeyVault BYOK-ferramentas India.zip

26853511EB767A33CF6CD880E78588E9BBE04E619B17FBC77A6B00A5111E800C

---

Para validar a integridade dos seus transferido BYOK conjunto de ferramentas de, a partir do seu sessão Azure PowerShell, utilize o cmdlet [Get-FileHash](https://technet.microsoft.com/library/dn520872.aspx) .

    Get-FileHash KeyVault-BYOK-Tools-*.zip

O conjunto de ferramentas inclui o seguinte:

- Um pacote de chave Exchange chave (KEK) que tem um nome que começa com **BYOK-KEK - pkg-.**
- Um pacote de segurança mundo que tem um nome que começa com **BYOK-SecurityWorld - pkg-.**
- Um script python denominado v**erifykeypackage.py.**
- Um executável da linha de comandos de ficheiros com nome **KeyTransferRemote.exe** e DLLs associadas.
- Um Visual C++ Redistributable pacote, com o nome **vcredist_x64.exe.**

Copie o pacote para uma unidade USB ou outros armazenamento portátil.

##<a name="step-2-prepare-your-disconnected-workstation"></a>Passo 2: Preparar o seu desligada estação de trabalho

Para este segundo passo, efetue os seguintes procedimentos na estação de trabalho que não está ligado a uma rede (na Internet ou rede interna).


###<a name="step-21-prepare-the-disconnected-workstation-with-thales-hsm"></a>2.1 passo: Preparar a desligada estação de trabalho da Thales HSM

Instale o software de suporte nCipher (da Thales) num computador Windows e, em seguida, anexe uma HSM da Thales nesse computador.

Certifique-se de que as ferramentas da Thales estão no caminho (**%nfast_home%\bin** e **%nfast_home%\python\bin**). Por exemplo, escreva o seguinte:

        set PATH=%PATH%;”%nfast_home%\bin”;”%nfast_home%\python\bin”

Para obter mais informações, consulte o guia de utilizador incluído com o HSM da Thales.

###<a name="step-22-install-the-byok-toolset-on-the-disconnected-workstation"></a>Passo 2.2: Instalar o conjunto de ferramentas BYOK na desligada estação de trabalho

Copie o pacote de conjunto de ferramentas BYOK de unidade USB ou outros armazenamento portátil e, em seguida, efetue o seguinte procedimento:

1. Extrai os ficheiros a partir do pacote transferido para qualquer pasta.
2. A partir dessa pasta, execute vcredist_x64.exe.
3. Siga as instruções para instalar os componentes de runtime Visual C++ para Visual Studio 2013.

##<a name="step-3-generate-your-key"></a>Passo 3: Gere a sua chave

Para este terceiro passo, efetue os seguintes procedimentos na desligada estação de trabalho.

###<a name="step-31-create-a-security-world"></a>Passo 3.1: Criar um mundo de segurança

Iniciar uma linha de comandos e execute o programa de novo mundo da Thales.

    new-world.exe --initialize --cipher-suite=DLf1024s160mRijndael --module=1 --acs-quorum=2/3

Este programa cria um ficheiro de **Segurança mundo** a % NFAST_KMDATA%\local\world, que corresponde à pasta C:\ProgramData\nCipher\Key gestão Settings\User. Pode utilizar diferentes valores para o quórum mas no nosso exemplo, lhe for pedido para introduzir três cartões em branco e PIN para cada um deles. Em seguida, as cartas de qualquer duas conceder acesso total ao mundo de segurança. Estes cartões tornam-se o **Administrador cartão definido** para o novo mundo de segurança.

Em seguida, faça o seguinte:

- Crie uma cópia de segurança o ficheiro do mundo. Seguro proteger o ficheiro do mundo, cartões de administrador e os respetivos PIN e certifique-se de que ninguém única tem acesso ao mais de um cartão.

###<a name="step-32-validate-the-downloaded-package"></a>Passo 3,2: Validar do pacote transferido

Este passo é opcional, mas recomendado para que possa validar o seguinte procedimento:

- A chave de chaves de Exchange que vem incluído do conjunto de ferramentas foi gerado a partir de um HSM da Thales genuine.
- Foi gerado hash do mundo segurança que vem incluído do conjunto de ferramentas num HSM da Thales genuine.
- A chave de Exchange é não é possível exportar.

>[AZURE.NOTE]Para validar o pacote transferido, o HSM tem de estar ligado, powered e tem de ter um mundo de segurança no mesmo (tal como a que acabou de criar).

Para validar o pacote transferido:

1.  Execute o script verifykeypackage.py por associando um dos seguintes procedimentos, dependendo do seu região geográfica ou instância do Azure:
    - Na América do Norte:

            python verifykeypackage.py -k BYOK-KEK-pkg-NA-1 -w BYOK-SecurityWorld-pkg-NA-1
    - Para a Europa:

            python verifykeypackage.py -k BYOK-KEK-pkg-EU-1 -w BYOK-SecurityWorld-pkg-EU-1
    - Para Ásia:

            python verifykeypackage.py -k BYOK-KEK-pkg-AP-1 -w BYOK-SecurityWorld-pkg-AP-1
    - Para América Latina:

            python verifykeypackage.py -k BYOK-KEK-pkg-LATAM-1 -w BYOK-SecurityWorld-pkg-LATAM-1
    - Para o Japão:

            python verifykeypackage.py -k BYOK-KEK-pkg-JPN-1 -w BYOK-SecurityWorld-pkg-JPN-1
    - Para a Austrália:

            python verifykeypackage.py -k BYOK-KEK-pkg-AUS-1 -w BYOK-SecurityWorld-pkg-AUS-1
    - Para [Administração pública do Azure](https://azure.microsoft.com/features/gov/), que utiliza a instância de Governo dos EUA do Azure:

            python verifykeypackage.py -k BYOK-KEK-pkg-USGOV-1 -w BYOK-SecurityWorld-pkg-USGOV-1
    - Para Canadá:

            python verifykeypackage.py -k BYOK-KEK-pkg-CANADA-1 -w BYOK-SecurityWorld-pkg-CANADA-1
    - Para Alemanha:

            python verifykeypackage.py -k BYOK-KEK-pkg-GERMANY-1 -w BYOK-SecurityWorld-pkg-GERMANY-1
    - Para a Índia:

            python verifykeypackage.py -k BYOK-KEK-pkg-INDIA-1 -w BYOK-SecurityWorld-pkg-INDIA-1
    >[AZURE.TIP]O software da Thales inclui python na %NFAST_HOME%\python\bin

2.  Confirmar que está a ver o seguinte, que indica validação com êxito: **resultado: SUCESSO**

Este script valida da cadeia de signatário até a chave de raiz da Thales. O script está incorporado hash desta chave de raiz e o valor deve ser **59178a47 de508c3f 291277ee 184f46c4 f1d9c639**. Também pode confirmar este valor separadamente visitando o [Web site da Thales](http://www.thalesesec.com/).

Agora está pronto para criar uma nova chave.

###<a name="step-33-create-a-new-key"></a>Passo 3.3: Criar uma nova chave

Gere uma chave, utilizando o programa de **generatekey** da Thales.

Execute o seguinte comando para gerar a chave:

    generatekey --generate simple type=RSA size=2048 protect=module ident=contosokey plainname=contosokey nvram=no pubexp=

Quando executa este comando, utilize estas instruções:

- O parâmetro *proteger* tem de estar definida para o valor **módulo**, conforme mostrado. Esta ação cria uma chave protegida módulo. O conjunto de ferramentas BYOK não suporta teclas protegida OCS.

- Substitua o valor de *contosokey* para o **travessão** e **plainname** qualquer valor de cadeia. Para minimizar administrativas transparências e reduzir o risco de erros, recomendamos que utilize o mesmo valor para ambos. O valor de **travessão** tem de conter apenas números, traços e letras minúsculas.

- O pubexp é deixado em branco (predefinição) neste exemplo, mas pode especificar valores específicos. Para mais informações, consulte a documentação da Thales.

Este comando cria um ficheiro de chave com token na sua pasta %NFAST_KMDATA%\local com um nome começando **key_simple_**, seguido de **travessão** que foi especificada no comando. Por exemplo: **key_simple_contosokey**. Este ficheiro contém uma chave encriptada.

Crie uma cópia de segurança este ficheiro de chave com token numa localização segura.

>[AZURE.IMPORTANT] Quando transfere mais tarde a chave para Azure chave cofre, a Microsoft não é possível exportar esta tecla lhe para que se torne extremamente importante que pode fazer cópia de segurança seu mundo chave e segurança em segurança. Contacte da Thales para orientação e práticas recomendadas para cópias de segurança a chave.

Agora está pronto para o qual transferir a sua chave de Azure chave cofre.

##<a name="step-4-prepare-your-key-for-transfer"></a>Passo 4: Preparar a sua chave para transferência

Para este passo quarto, efetue os seguintes procedimentos na desligada estação de trabalho.

###<a name="step-41-create-a-copy-of-your-key-with-reduced-permissions"></a>Passo 4.1: Criar uma cópia da sua chave com permissões reduzidas

Para reduzir as permissões no seu chave, a partir de uma linha de comandos, execute um dos seguintes procedimentos, dependendo do seu região geográfica ou instância do Azure:

- Na América do Norte:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-NA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-NA-1
- Para a Europa:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-EU-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-EU-1
- Para Ásia:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AP-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AP-1
- Para América Latina:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-LATAM-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-LATAM-1
- Para o Japão:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-JPN-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-JPN-1
- Para a Austrália:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AUS-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AUS-1
- Para [Administração pública do Azure](https://azure.microsoft.com/features/gov/), que utiliza a instância de Governo dos EUA do Azure:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USGOV-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USGOV-1
- Para Canadá:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-CANADA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-CANADA-1
- Para Alemanha:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-GERMANY-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-GERMANY-1
- Para a Índia:

        KeyTransferRemote.exe -ModifyAcls -KeyAppName simple -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-INDIA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-INDIA-1


Quando executa este comando, substitua *contosokey* o mesmo valor que especificou no **3.3 passo: criar uma nova chave** do passo [Gerar sua chave](#step-3-generate-your-key) .

Se lhe pedir para plug-in os cartões de administração do mundo de segurança.

Quando o comando for concluída, verá **resultado: SUCESSO** e a cópia da sua chave com permissões reduzidas estão no ficheiro denominado key_xferacId_<contosokey>.

###<a name="step-42-inspect-the-new-copy-of-the-key"></a>Passo 4.2: Inspecionar a nova cópia da chave

Opcionalmente, execute o Thales utilitários para confirmar as permissões mínimas na nova chave:

- aclprint.PY:

        "%nfast_home%\bin\preload.exe" -m 1 -A xferacld -K contosokey "%nfast_home%\python\bin\python" "%nfast_home%\python\examples\aclprint.py"
- kmfile-dump.exe:

        "%nfast_home%\bin\kmfile-dump.exe" "%NFAST_KMDATA%\local\key_xferacld_contosokey"
Quando executar estes comandos, substitua contosokey o mesmo valor que especificou no **3.3 passo: criar uma nova chave** do passo [Gerar sua chave](#step-3-generate-your-key) .

###<a name="step-43-encrypt-your-key-by-using-microsofts-key-exchange-key"></a>Passo 4.3: Encriptar a chave de através de chave de troca de chave da Microsoft

Execute um dos seguintes comandos, dependendo do seu região geográfica ou instância do Azure:

- Na América do Norte:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-NA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-NA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
- Para a Europa:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-EU-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-EU-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
- Para Ásia:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AP-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AP-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
- Para América Latina:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-LATAM-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-LATAM-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
- Para o Japão:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-JPN-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-JPN-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
- Para a Austrália:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-AUS-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-AUS-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
- Para [Administração pública do Azure](https://azure.microsoft.com/features/gov/), que utiliza a instância de Governo dos EUA do Azure:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-USGOV-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-USGOV-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
- Para Canadá:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-CANADA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-CANADA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
- Para Alemanha:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-GERMANY-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-GERMANY-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey
- Para a Índia:

        KeyTransferRemote.exe -Package -KeyIdentifier contosokey -ExchangeKeyPackage BYOK-KEK-pkg-INDIA-1 -NewSecurityWorldPackage BYOK-SecurityWorld-pkg-INDIA-1 -SubscriptionId SubscriptionID -KeyFriendlyName ContosoFirstHSMkey


Quando executa este comando, utilize estas instruções:

- Substitua *contosokey* o identificador que utilizou para gerar a chave no **3.3 passo: criar uma nova chave** do passo [Gerar sua chave](#step-3-generate-your-key) .

- Substitua *SubscriptionID* com o ID da subscrição do Azure que contém a sua chave cofre. Que recuperou este valor anteriormente, no **1.2 passo: Obtenha o seu ID de subscrição Azure** do passo [preparar estação de trabalho ligado à Internet](#step-1-prepare-your-internet-connected-workstation) .

- Substitua *ContosoFirstHSMKey* uma etiqueta que é utilizada para o seu nome de ficheiro de exportação.

Quando esta for concluído com êxito, é-lhe apresentado **resultado: SUCESSO** e não existe um novo ficheiro na pasta atual que tem o seguinte nome: TransferPackage -*ContosoFirstHSMkey*.byok

###<a name="step-44-copy-your-key-transfer-package-to-the-internet-connected-workstation"></a>Passo 4.4: Copiar o seu pacote de transferência chave estação de trabalho ligado à Internet

Utilize uma unidade USB ou outros armazenamento portátil para copiar o ficheiro de exportação do passo anterior (KeyTransferPackage ContosoFirstHSMkey.byok) para sua estação de trabalho ligado à Internet.

##<a name="step-5-transfer-your-key-to-azure-key-vault"></a>Passo 5: Transferir a sua chave para Cofre de chave do Azure

Para este passo final, na estação de trabalho ligado à Internet, utilize o cmdlet [Adicionar AzureKeyVaultKey](https://msdn.microsoft.com/library/azure/dn868048\(v=azure.300\).aspx) para carregar o pacote de transferência chave que copiou do desligada estação de trabalho para o Azure chave cofre HSM:

    Add-AzureKeyVaultKey -VaultName 'ContosoKeyVaultHSM' -Name 'ContosoFirstHSMkey' -KeyFilePath 'c:\TransferPackage-ContosoFirstHSMkey.byok' -Destination 'HSM'

Se o carregamento for bem sucedido, verá apresentadas as propriedades da chave que acabou de adicionar.


##<a name="next-steps"></a>Próximos passos

Agora pode usar esta tecla protegida HSM no cofre chave. Para mais informações, consulte a secção **se pretende utilizar um módulo de segurança do hardware (HSM)** no tutorial [Introdução ao Azure chave cofre](key-vault-get-started.md) .
