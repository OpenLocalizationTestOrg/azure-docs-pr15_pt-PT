<properties 
    pageTitle="Utilizar teclas SSH com o Windows para Linux VMs | Microsoft Azure" 
    description="Saiba como gerar e utilizar teclas SSH num computador Windows para ligar a uma máquina de virtual Linux no Azure." 
    services="virtual-machines-linux" 
    documentationCenter="" 
    authors="squillace" 
    manager="timlt" 
    editor=""
    tags="azure-service-management,azure-resource-manager" />

<tags 
    ms.service="virtual-machines-linux" 
    ms.workload="infrastructure-services" 
    ms.tgt_pltfrm="vm-linux" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="10/18/2016" 
    ms.author="rasquill"/>

# <a name="how-to-use-ssh-keys-with-windows-on-azure"></a>Como utilizar SSH teclas com o Windows no Azure

> [AZURE.SELECTOR]
- [Windows](virtual-machines-linux-ssh-from-windows.md)
- [Linux/Mac](virtual-machines-linux-mac-create-ssh-keys.md)

Quando se liga à Linux máquinas virtuais (VMs) Azure, deve utilizar [criptografia de chave pública](https://wikipedia.org/wiki/Public-key_cryptography) para fornecer uma forma mais segura para iniciar sessão na sua VM Linux. Este processo envolve uma troca de chaves pública e privada a utilizar o comando shell seguro (SSH) para autenticar-se em vez de um nome de utilizador e palavra-passe. As palavras-passe estão vulneráveis a força bruta ataques, sobretudo no VMs acesso à Internet, tal como servidores web. Este artigo fornece uma descrição geral de teclas SSH e como gerar das chaves adequadas num computador Windows.


## <a name="overview-of-ssh-and-keys"></a>Descrição geral de SSH e chaves

Pode segura iniciar sessão no seu VM Linux através da utilização de chaves públicas e privadas:

- A **chave pública** é colocado no seu VM Linux ou qualquer outro serviço que pretende utilizar com criptografia de chave pública.
- A **chave privada** é o que fizer a apresentação à sua VM Linux ao iniciar sessão no, verifique a sua identidade. Protege esta chave privada. Não partilhá-lo.

Estes chaves públicas e privadas podem ser utilizados em vários VMs e serviços. Não necessita de um par de teclas para cada VM ou serviço que pretende aceder. Para obter uma descrição mais detalhada, consulte o artigo [criptografia de chave pública](https://wikipedia.org/wiki/Public-key_cryptography).

SSH é um protocolo de ligação encriptada, que permite que os inícios de sessão seguros através de ligações não seguras. É o protocolo de ligação predefinido para VMs Linux alojado no Azure. Embora SSH propriamente dito fornece uma ligação encriptada, utilizar palavras-passe com ligações de SSH ainda vulnerável a VM a força bruta ataques ou adivinhar das palavras-passe. É um método mais seguro e preferido, de ligar a uma VM utilizando SSH ao utilizar estes chaves públicas e privadas, também conhecido como SSH teclas.

Se não pretender utilizar teclas de SSH, pode ainda iniciar sessão sua VMs Linux utilizando uma palavra-passe. Se o seu VM não sejam exposto à Internet, poderão ser suficiente utilizar palavras-passe. No entanto, ainda precisar de gerir as palavras-passe para cada VM Linux e manter as políticas de palavra-passe saudável e práticas, como o comprimento mínimo palavra-passe e actualizar regularmente. A utilização de chaves SSH reduz a complexidade de gerir credenciais individuais através de vários VMs.


## <a name="windows-packages-and-ssh-clients"></a>Pacotes do Windows e clientes do SSH

Ligar a e gerir Linux VMs no Azure utilizando um cliente **ssh** . Computadores com o Windows não normalmente têm um cliente **ssh** instalado. Comuns SSH do Windows clientes que pode instalar estão incluídos nos seguintes pacotes de:

- [Git para Windows](https://git-for-windows.github.io/)
- [Betumes](http://www.chiark.greenend.org.uk/~sgtatham/putty/)
- [MobaXterm](http://mobaxterm.mobatek.net/)
- [Cygwin](https://cygwin.com/)

> [AZURE.NOTE] O aniversário atualização mais recente do Windows 10 inclui festa para Windows. Esta funcionalidade permite-lhe executar o Windows Subsystem para utilitários Linux e acesso, tal como um cliente SSH. Festa para Windows ainda está em desenvolvimento e é considerado como uma versão beta. Para mais informações sobre festa para Windows, consulte o artigo [festa de Ubuntu no Windows](https://msdn.microsoft.com/commandline/wsl/about).


## <a name="which-key-files-do-you-need-to-create"></a>Quais os ficheiros chaves que necessita criar?

Azure necessita de 2048-bit, **ssh rsa** Formatar chaves públicas e privadas. Se está a gerir recursos Azure utilizando o modelo de implementação clássico, também terá de gerar um PEM (`.pem` ficheiro).

Aqui estão os cenários de implementação e os tipos de ficheiros que utilizar em cada:

1. chaves **SSH rsa** são necessárias para qualquer implementação utilizando o [portal do Azure](https://portal.azure.com)e implementações do Gestor de recursos utilizando o [Clip do Azure](../xplat-cli-install.md).
    - Estas teclas são normalmente que precisa de todos os maioria das pessoas.
2. `.pem`ficheiro é necessário para criar VMs utilizando o [portal de clássica](https://manage.windowsazure.com). Estas teclas também são suportadas no implementações clássica que utilizam o [Clip do Azure](../xplat-cli-install.md).
    - Só tem de criar estas teclas adicionais e certificados se está a gerir recursos criados utilizando o modelo de implementação clássica.


## <a name="install-git-for-windows"></a>Instalar o Git para Windows

Secção precedente listado pacotes várias que incluem o `openssl` ferramenta para Windows. Esta ferramenta é necessária para criar chaves públicas e privadas. Os exemplos seguintes de detalhe como instalar e utilizar **Git para Windows**, mas pode escolher independentemente pacote que preferir. **Git para Windows** dá-lhe acesso a algumas ferramentas de software adicional de abrir origem ([OSS](https://en.wikipedia.org/wiki/Open-source_software)) e os utilitários que poderão ser úteis à medida que trabalha com Linux VMs.

1. Transferir e instalar **Git para Windows** a partir da seguinte localização: [https://git-for-windows.github.io/](https://git-for-windows.github.io/).

2. Aceite as opções predefinidas durante o processo de instalação, a menos que precise especificamente para alterá-las.

3. Executar **Git festa** a partir do **Menu Iniciar** > **Git** > **Git festa**. Consola do aspeto semelhante ao exemplo seguinte:

    ![Git para festa de Windows shell](./media/virtual-machines-linux-ssh-from-windows/git-bash-window.png)


## <a name="create-a-private-key"></a>Criar uma chave privada

1. Na janela da **Git festa** , utilizar `openssl.exe` para criar uma chave privada. O exemplo seguinte cria uma chave denominada `myPrivateKey` e com o nome do certificado `myCert.pem`:

    ```bash
    openssl.exe req -x509 -nodes -days 365 -newkey rsa:2048 \
        -keyout myPrivateKey.key -out myCert.pem
    ```

    O resultado é semelhante ao exemplo seguinte:

    ```bash
    Generating a 2048 bit RSA private key
    .......................................+++
    .......................+++
    writing new private key to 'myPrivateKey.key'
    -----
    You are about to be asked to enter information that will be incorporated
    into your certificate request.
    What you are about to enter is what is called a Distinguished Name or a DN.
    There are quite a few fields but you can leave some blank
    For some fields there will be a default value,
    If you enter '.', the field will be left blank.
    -----
    Country Name (2 letter code) [AU]:
    ```

2. Atender os pedidos para o nome do país, localização, nome da organização, etc.

3. Nova chave privada do certificado são criados no seu diretório de trabalho atual. Para melhores práticas de segurança, deverá defina as permissões no seu chave privada para que apenas pode ser acedido:

    ```bash
    chmod 0600 myPrivateKey
    ```

4. Se precisar também gerir clássica recursos, converter o `myCert.pem` para `myCert.cer` (X509 codificado DER X509 certificado). Execute este passo opcional apenas se necessárias para gerir especificamente recursos de clássica mais antigos. 

    Converta o certificado utilizando o seguinte comando:

    ```bash
    openssl.exe  x509 -outform der -in myCert.pem -out myCert.cer
    ```

## <a name="create-a-private-key-for-putty"></a>Criar uma chave privada para betumes

Betumes são um cliente SSH comuns para Windows. Está livre utilizar qualquer cliente SSH que deseja. Para utilizar betumes, necessita de criar um tipo de adicional da chave - uma chave privada betumes (PPK). Se não pretender utilizar betumes, ignorar esta secção.

O exemplo seguinte cria esta chave privada adicional especificamente para betumes utilizar:

1. Utilize **Git festa** para converter a chave privada uma chave privada RSA que PuTTYgen conseguem compreender. O exemplo seguinte cria uma chave denominada `myPrivateKey_rsa` a partir da chave existente com o nome `myPrivateKey`:

    ```bash
    openssl rsa -in ./myPrivateKey.key -out myPrivateKey_rsa
    ```

    Para melhores práticas de segurança, deverá defina as permissões no seu chave privada para que apenas pode ser acedido:

    ```bash
    chmod 0600 myPrivateKey_rsa
    ```

2. Transferir e executar PuTTYgen a partir da seguinte localização: [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)

3. Clique no menu: **ficheiro** > **carga uma chave privada**

4. Localize a chave privada (`myPrivateKey_rsa` no exemplo anterior). O directório predefinido ao iniciar o **Git festa** é `C:\Users\%username%`. Alterar o filtro de ficheiro para mostrar **todos os ficheiros (\*.\*)**:

    ![Carregar a chave privada existente num PuTTYgen](./media/virtual-machines-linux-ssh-from-windows/load-private-key.png)

5. Clique em **Abrir**. Um pedido indica que a tecla tenha sido importada com êxito:

    ![Tecla importada com êxito para PuTTYgen](./media/virtual-machines-linux-ssh-from-windows/successfully-imported-key.png)

6. Clique em **OK** para fechar o pedido.

7. A chave pública é apresentada na parte superior da janela do **PuTTYgen** . Copie e cole esta chave pública no portal do Azure ou modelo de Gestor de recursos do Azure quando cria uma VM Linux. Também pode clicar em **Guardar chave pública** para guardar uma cópia para o seu computador:

    ![Guardar o ficheiro para da chave pública](./media/virtual-machines-linux-ssh-from-windows/save-public-key.png)

    O exemplo seguinte mostra como que copiar e colar esta chave pública no portal do Azure quando cria uma VM Linux. A chave pública, em seguida, normalmente é armazenada na `~/.ssh/authorized_keys` no seu novo VM.

    ![Utilize a chave pública quando criar uma VM no portal do Azure](./media/virtual-machines-linux-ssh-from-windows/use-public-key-azure-portal.png)

7. Novamente na **PuTTYgen**, clique em **Guardar chave privada**:

    ![Guardar o ficheiro de chave privada betumes](./media/virtual-machines-linux-ssh-from-windows/save-ppk-file.png)

    > [AZURE.WARNING] Uma linha de comandos pergunta se pretende continuar sem ter de introduzir uma frase de acesso para a sua chave. Uma frase de acesso é semelhante a uma palavra-passe anexada a sua chave privada. Mesmo se alguém foram obter a chave privada, se ainda não seria possível autenticar utilizando apenas a chave. Também seria necessário a frase de acesso. Sem uma frase de acesso, se alguém obtém a sua chave privada, eles podem iniciar sessão a qualquer VM ou serviço que utiliza a essa tecla. Recomendamos que cria uma frase de acesso. No entanto, caso se esqueça a frase de acesso, não existe nenhuma forma de recuperá-la.

    Se pretender introduzir uma frase de acesso, clique em **não**, introduza uma frase na janela principal do PuTTYgen e, em seguida, clique em **Guardar chave privada** novamente. Caso contrário, clique em **Sim** para continuar sem facultar a frase de acesso opcional.

8. Introduza um nome e uma localização para guardar o ficheiro PPK.


## <a name="use-putty-to-ssh-to-a-linux-machine"></a>Utilizar betumes para SSH para uma máquina de Linux

Novamente, betumes são um cliente SSH comuns para Windows. Está livre utilizar qualquer cliente SSH que deseja. Os passos seguintes detalhadamente como utilizar a chave privada para autenticar com a VM Azure utilizando SSH. Os passos são semelhantes em outros clientes chaves SSH em termos necessário especificá-las carregar a sua chave privada para autenticar a ligação SSH.

1. Transferir e executar betumes a partir da seguinte localização: [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html)

2. Preencha o nome do anfitrião ou o endereço IP do seu VM a partir do Azure portal:

    ![Nova ligação para abrir](./media/virtual-machines-linux-ssh-from-windows/putty-new-connection.png)

3. Antes de selecionar **Abrir**, clique em **ligação** > **SSH** > **Auth** separador. Procure e selecione a chave privada:

    ![Selecione a chave privada betumes para autenticação](./media/virtual-machines-linux-ssh-from-windows/putty-auth-dialog.png)

4. Clique em **Abrir** para ligar a sua máquina virtual
 

## <a name="next-steps"></a>Próximos passos
Também pode gerar o chaves públicas e privadas, [utilizando OS X e Linux](virtual-machines-linux-mac-create-ssh-keys.md).

Para mais informações sobre festa para Windows e os benefícios da tendo ferramentas de OSS facilmente disponíveis no computador Windows, consulte o artigo [festa de Ubuntu no Windows](https://msdn.microsoft.com/commandline/wsl/about).

Se tiver problemas ao utilizar SSH para ligar à sua VMs Linux, consulte o artigo [resolver problemas de SSH ligações para um VM de Linux Azure](virtual-machines-linux-troubleshoot-ssh-connection.md).