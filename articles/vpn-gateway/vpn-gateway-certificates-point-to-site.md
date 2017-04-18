<properties 
   pageTitle="Criar certificados autoassinados para rede virtual do ponto-para-Site publicação em várias ligações local utilizando makecert | Microsoft Azure"
   description="Este artigo contém passos para utilizar makecert para criar certificados autoassinados no Windows 10."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>
<tags 
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="08/22/2016"
   ms.author="cherylmc" />

# <a name="working-with-self-signed-certificates-for-point-to-site-connections"></a>Trabalhar com autoassinado certificados para ligações de ponto-para-Site

Este artigo ajuda-o a criar um certificado autoassinado utilizando **makecert**e, em seguida, gerar certificados de cliente do mesmo. Os passos são escritos para makecert no Windows 10. MakeCert foi validado para criar os certificados que são compatíveis com ligações de P2S. 

Para ligações de P2S, o método preferencial para certificados é utilizar a solução de certificado empresarial, certificando-se emitir o cliente de certificados com o formato do valor de nome comum 'name@yourdomain.com', em vez do formato de 'NetBIOS nomededomínio'.

Se não tiver uma solução empresarial, um certificado autoassinado é necessário para permitir que os clientes de P2S ligar a uma rede virtual. Temos que foi preterida makecert, mas ainda é um método para criar os certificados autoassinados que são compatíveis com ligações de P2S válido. Estamos a trabalhar no outra solução para criar os certificados autoassinados mas, neste momento, makecert é o método preferencial.

## <a name="create-a-self-signed-certificate"></a>Criar um certificado autoassinado

MakeCert é uma forma de criação de um certificado autoassinado. Os passos seguintes ajudá-lo através da criação de um certificado autoassinado utilizando makecert. Estes passos não são específicas do modelo de implementação. São válidos para o Gestor de recursos e clássica.

### <a name="to-create-a-self-signed-certificate"></a>Para criar um certificado autoassinado

1. A partir de um computador a executar o Windows 10, transfira e instale o [Windows Software Development Kit (SDK) para Windows 10](https://dev.windows.com/en-us/downloads/windows-10-sdk).

2. Após a instalação, pode encontrar o utilitário makecert.exe neste caminho: c:\Programas\Microsoft ficheiros (x86) \Windows Kits\10\bin\<arco >. 
        
    Exemplo:`C:\Program Files (x86)\Windows Kits\10\bin\x64`

3. Em seguida, crie e instale um certificado no arquivo de certificados pessoais no seu computador. O exemplo seguinte cria um ficheiro *. cer* correspondentes que carregar para o Azure ao configurar P2S. Execute o seguinte comando, como administrador. Substitua o nome que pretende utilizar para o certificado *ARMP2SRootCert* e *ARMP2SRootCert.cer* .<br><br>O certificado irá estar localizado no seu certificados - User\Personal\Certificates atual.

        makecert -sky exchange -r -n "CN=ARMP2SRootCert" -pe -a sha1 -len 2048 -ss My "ARMP2SRootCert.cer"


###  <a name="rootpublickey"></a>Para obter a chave pública

Como parte da configuração do Gateway de VPN para ligações de ponto-para-Site, a chave pública para o certificado de raiz é carregada para o Azure.

1. Para obter um ficheiro. cer a partir do certificado, abra **certmgr**. O certificado autoassinado raiz de contexto, clique em **todas as tarefas**e, em seguida, clique em **Exportar**. Esta ação abre o **Assistente para exportar certificados**.

2. No assistente, clique em **seguinte**, selecione **não, não exportar a chave privada**e, em seguida, clique em **seguinte**.

3. Na página do **Formato de ficheiro de exportação** , selecione **Base-64 codificado x. 509 (. CER).** Em seguida, clique em **seguinte**. 

4. No **ficheiro para exportar**, **procure** a localização para o qual pretende exportar o certificado. Para **nome de ficheiro**, nome do ficheiro de certificado. Em seguida, clique em **seguinte**.

5. Clique em **Concluir** para exportar o certificado.

 
### <a name="export-the-self-signed-certificate-optional"></a>Exporte o certificado autoassinado (opcional)

Poderá querer exporte o certificado autoassinado e armazená-lo em segurança. Caso seja necessário, mais tarde pode instalá-la noutro computador e gerar mais certificados de cliente ou outro ficheiro. cer de exportação. Qualquer computador com um certificado de cliente instalado e que também está configurado com o cliente VPN inicial definições podem ligar-se à sua rede através de P2S virtual. Por que razão, pretende Certifique-se de que os certificados de cliente são gerados e instalados apenas quando for necessário e que o certificado autoassinado está armazenado em segurança.

Para exportar o certificado autoassinado como um PFX, selecione o certificado de raiz e utilizar os mesmos passos conforme descrito no [Exportar um certificado de cliente](#clientkey) para exportar.

## <a name="create-and-install-client-certificates"></a>Criar e instalar o certificados de cliente

Não instale o certificado autoassinado diretamente no computador cliente. Tem de gerar um certificado de cliente a partir do certificado autoassinado. Exportar e instalar o certificado de cliente para o computador cliente. Os passos seguintes não são específicas do modelo de implementação. São válidos para o Gestor de recursos e clássica.

### <a name="part-1---generate-a-client-certificate-from-a-self-signed-certificate"></a>Parte 1 - gerar um certificado de cliente a partir de um certificado autoassinado

Os passos seguintes ajudá-lo através de uma forma para gerar um certificado de cliente a partir de um certificado autoassinado. Pode gerar vários certificados de cliente a partir do mesmo certificado. Cada certificado de cliente, em seguida, pode ser exportado e instalado no computador cliente. 

1. No mesmo computador que utilizou para criar o certificado autoassinado, abra uma linha de comandos como administrador.

2. Neste exemplo, "ARMP2SRootCert" refere-se para o certificado autoassinado que gerou. 
    - Alterar *"ARMP2SRootCert"* para o nome da raiz autoassinado que estão a gerar o certificado de cliente a partir de. 
    - Alterar *ClientCertificateName* para o nome que pretende para gerar um certificado de cliente que seja. 


    Modificar e executar o exemplo para gerar um certificado de cliente. Se executar o exemplo seguinte sem modificá-la, o resultado é um certificado de cliente com o nome ClientCertificateName no arquivo de certificados pessoais que foi criado a partir de certificado de raiz ARMP2SRootCert.

        makecert.exe -n "CN=ClientCertificateName" -pe -sky exchange -m 96 -ss My -in "ARMP2SRootCert" -is my -a sha1

4. Todos os certificados são armazenados no seu 'certificados - User\Personal\Certificates atual' armazenar no seu computador. Pode gerar como vários certificados de cliente, conforme necessário com base neste procedimento.

### <a name="clientkey"></a>Parte 2 - exportar um certificado de cliente

1. Para exportar um certificado de cliente, abra **certmgr**. Botão direito do rato o certificado de cliente que pretende exportar, clique em **todas as tarefas**e, em seguida, clique em **Exportar**. Esta ação abre o **Assistente para exportar certificados**.

2. No assistente, clique em **seguinte**, em seguida, selecione **Sim, exporte a chave privada**e, em seguida, clique em **seguinte**.

3. Na página do **Formato de ficheiro de exportação** , pode deixar as predefinições selecionadas. Em seguida, clique em **seguinte**. 
 
4. Na página de **segurança** , tem de proteger a chave privada. Se selecionar para utilizar uma palavra-passe, certifique-se gravar ou lembre-se a palavra-passe que definiu para este certificado. Em seguida, clique em **seguinte**.

5. No **ficheiro para exportar**, **procure** a localização para o qual pretende exportar o certificado. Para **nome de ficheiro**, nome do ficheiro de certificado. Em seguida, clique em **seguinte**.

6. Clique em **Concluir** para exportar o certificado.  

### <a name="part-3---install-a-client-certificate"></a>Parte 3 - instalar um certificado de cliente

Cada cliente ao qual pretende ligar à sua rede virtual utilizando uma ligação ponto-para-Site tem de ter instalado um certificado de cliente. Este certificado é além o pacote de configuração de VPN necessário. Os passos seguintes orientam o instalar manualmente o certificado de cliente.

1. Localize e copie o ficheiro *. pfx* para o computador cliente. No computador cliente, faça duplo clique no ficheiro *. pfx* para instalar. Deixar a **Localização do arquivo** como **Utilizador atual**e, em seguida, clique em **seguinte**.

2. No **ficheiro** para importar a página, não faça as alterações. Clique em **seguinte**.

3. Na página **protecção por chave privada** , introduza a palavra-passe para o certificado se é utilizado um, ou certifique-se de que o capital de segurança que está a instalar o certificado está correto, em seguida, clique em **seguinte**.

4. Na página **Arquivo de certificados** , deixe a localização predefinida e, em seguida, clique em **seguinte**.

5. Clique em **Concluir**. O **Aviso de segurança** para a instalação do certificado, clique em **Sim**. O certificado agora é importado com êxito.

## <a name="next-steps"></a>Próximos passos

Continue com a configuração do ponto-para-Site. 

- Para obter passos de modelo de implementação do **Gestor de recursos** , consulte o artigo [Configurar uma ligação ponto-para-Site a uma VNet através do PowerShell](vpn-gateway-howto-point-to-site-rm-ps.md). 
- Para obter passos de modelo **clássico** da implementação, consulte o artigo [configurar um ponto-para-Site ligação VPN um VNet através do portal clássico](vpn-gateway-point-to-site-create.md).