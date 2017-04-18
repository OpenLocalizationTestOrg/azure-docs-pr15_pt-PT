<properties
    pageTitle="Implementar o conceito de pilha Azure | Microsoft Azure"
    description="Saiba como preparar o conceito de pilha Azure e executar o script do PowerShell para implementar o conceito de pilha Azure."
    services="azure-stack"
    documentationCenter=""
    authors="ErikjeMS"
    manager="byronr"
    editor=""/>

<tags
    ms.service="azure-stack"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/20/2016"
    ms.author="erikje"/>

# <a name="deploy-azure-stack-poc"></a>Implementar o conceito de pilha Azure
Para implementar o conceito de pilha Azure, tem primeiro de [preparar o computador de implementação](#prepare-the-deployment-machine) e, em seguida, [execute o script de implementação do PowerShell](#run-the-powershell-deployment-script).

## <a name="download-and-extract-microsoft-azure-stack-poc-tp2"></a>Transferir e extrair Microsoft Azure pilha conceito TP2

Antes de começar, certifique-se de que pelo menos 85 GB de espaço.

1. A transferência do Azure pilha conceito TP2 é composto por um ficheiro zip que contém os seguintes 12 ficheiros, ~ 20 GB de somatório:
    - 1 MicrosoftAzureStackPOC.EXE
2. Reveja o ecrã de contrato de licença e informações do assistente auto-extraível e, em seguida, clique em **seguinte**.
3. Reveja o ecrã de declaração de privacidade e informações do assistente auto-extraível e, em seguida, clique em **seguinte**.
4. Selecione o destino para os ficheiros serão extraídas, clique em **seguinte**.
    - A predefinição é: <drive letter>:\<pasta atual > \Microsoft Azure conceito de pilha
5. Reveja o ecrã de localização de destino e informações do assistente auto-extraível e, em seguida, clique em **extrair** para extrair os ficheiros de ThirdPartyLicenses.rtf e CloudBuilder.vhdx (~44.5 GB).

> [AZURE.NOTE] Depois de extrair os ficheiros, pode eliminar o ficheiro zip para recuperar espaço no computador. Em alternativa, pode mover o ficheiro zip para outra localização, pelo que, se que necessita de implementá não é necessário transferir os ficheiros de postal novamente.

## <a name="prepare-the-deployment-machine"></a>Preparar o computador de implementação

1. Certifique-se de que pode física ligar à máquina de implementação, ou ter acesso de consola física (como KVM). Terá acesso após reiniciar o computador de implementação no passo 9.

2. Certifique-se da que máquina de implementação cumpra os [requisitos mínimos](azure-stack-deploy.md). Pode utilizar o [Verificador de implementação do Azure pilha de 2 de pré-visualização técnica](https://gallery.technet.microsoft.com/Deployment-Checker-for-50e0f51b) para confirmar as suas necessidades.

3. Inicie sessão como administrador Local para o seu computador conceito.

4. Copie o ficheiro de CloudBuilder.vhdx para C:\CloudBuilder.vhdx.

    > [AZURE.NOTE] Se optar por não utilizar o script recomendado para preparar o computador anfitrião da conceito (os passos 5 – passo 7), não introduza qualquer tecla de licença na página de ativação. Uma versão de avaliação de imagem do Windows Server 2016 está incluída e ao introduzir uma chave de licença faz com que expiração mensagens de aviso.

5. No computador conceito, execute o seguinte script do PowerShell para transferir os ficheiros de suporte do Azure pilha TP2:

    ```powershell
    # Variables
    $Uri = 'https://raw.githubusercontent.com/Azure/AzureStack-Tools/master/Deployment/'
    $LocalPath = 'c:\AzureStack_TP2_SupportFiles'

    # Create folder
    New-Item $LocalPath -type directory

    # Download files
    ( 'BootMenuNoKVM.ps1', 'PrepareBootFromVHD.ps1', 'Unattend.xml', 'unattend_NoKVM.xml') | foreach { Invoke-WebRequest ($uri + $_) -OutFile ($LocalPath + '\' + $_) } 
    ```

    Este script transfere os ficheiros de suporte do Azure pilha TP2 para a pasta especificada pelo parâmetro $LocalPath.
    
6. Abrir uma consola do PowerShell elevada e altere o directório para onde copiou os ficheiros.
    - 11 MicrosoftAzureStackPOC-N.BIN (onde N é 1-11)
7. Botão direito do rato sobre o MicrosoftAzureStackPOC.EXE > Executar como administrador.

8. Execute o script PrepareBootFromVHD.ps1. Este script e os ficheiros de instalação autónoma estão disponíveis com os scripts de suporte fornecidos juntamente com esta compilação.
    Existem cinco parâmetros para este script do PowerShell:
    - CloudBuilderDiskPath (obrigatório) – o caminho para CloudBuilder.vhdx no anfitrião do.
    - (Opcional) – DriverPath permite-lhe adicionar controladores adicionais para o anfitrião do HD virtual.
    - (Opcional) – ApplyUnattend especifique este parâmetro para automatizar a configuração do sistema operativo. Se especificado, o utilizador tem de fornecer AdminPassword para configurar o SO durante o arranque (requer desde a que o acompanha ficheiro unattend_NoKVM.xml).
    Se não utilizar este parâmetro, é utilizado o ficheiro Unattend genérico sem personalização ainda mais. É necessário KVM personalização concluída depois-ser reiniciado.
    - (Opcional) – AdminPassword utilizado apenas quando o parâmetro ApplyUnattend estiver definido, requer um mínimo de seis carateres.
    - (Opcional) – VHDLanguage, especifica a linguagem VHD, predefinir para "en-US".
    O script é documentado e contém exemplo de utilização, apesar de utilização de mais comuns:
    
        `.\PrepareBootFromVHD.ps1 -CloudBuilderDiskPath C:\CloudBuilder.vhdx -ApplyUnattend`
    
        Se executar este comando exato, tem de introduzir a AdminPassword na linha de comandos.

9. Quando o script estiver concluído, tem de confirmar o reinício. Se existirem outros utilizadores tem sessão iniciados, este comando irá falhar. Se o comando falhar, execute o seguinte comando:`Restart-Computer -force` 

10. ANFITRIÃO do reinicia em SO de CloudBuilder.vhdx, onde continua a implementação.

> [AZURE.IMPORTANT] Pilha Azure requer o acesso à Internet, diretamente ou através de um proxy transparente. A implementação de TP2 conceito suporta exatamente uma NIC para funcionamento em rede. Se tiver várias NIC, certifique-se de que apenas um está ativado (e todas as outras pessoas estão desactivadas) antes de executar o script de implementação na secção seguinte.

## <a name="run-the-powershell-deployment-script"></a>Executar o script de implementação do PowerShell

1. Inicie sessão como administrador Local para o seu computador conceito. Utilize as credenciais especificadas nos passos anteriores.

2. Abra uma elevados consola do PowerShell.

3. No PowerShell, execute este comando:`cd C:\CloudDeployment\Configuration`

4. Execute o comando implementar:`.\InstallAzureStackPOC.ps1`

5. Na linha de comandos **introduzir a palavra-passe** , introduza uma palavra-passe e, em seguida, confirme-lo. Esta é a palavra-passe para todas as máquinas virtuais. Certifique-se de que gravá-lo.

6. Introduza as credenciais para a sua conta do Azure Active Directory. Este utilizador tem de ser o Administrador Global no inquilino de diretório.

7. O processo de implementação pode demorar algumas horas, durante o qual o sistema automaticamente for reiniciado uma vez.

    > [AZURE.IMPORTANT] Se quiser monitorizar o progresso de implementação, inicie sessão no como azurestack\AzureStackAdmin. Se iniciar sessão como um administrador local depois do computador está associado ao domínio, não verá o progresso de implementação. Não execute novamente a implementação, em vez disso, inicie sessão no como azurestack\AzureStackAdmin para validar que está a funcionar.

    Quando a implementação com êxito, consola do PowerShell apresenta: **concluída: ação 'Implementação'**.

    Se a implementação falhar, pode tentar [executá-la novamente](azure-stack-rerun-deploy.md). Ou, pode [implementá](azure-stack-redeploy.md) -lo de raiz.

### <a name="deployment-script-examples"></a>Exemplos de script de implementação

Se a sua identidade AAD só está associado a um AAD directório:

    cd C:\CloudDeployment\Configuration
    $adminpass = ConvertTo-SecureString "<LOCAL ADMIN PASSWORD>" -AsPlainText -Force
    $aadpass = ConvertTo-SecureString "<AAD GLOBAL ADMIN ACCOUNT PASSWORD>" -AsPlainText -Force
    $aadcred = New-Object System.Management.Automation.PSCredential ("<AAD GLOBAL ADMIN ACCOUNT>", $aadpass)
    .\InstallAzureStackPOC.ps1 -AdminPassword $adminpass -AADAdminCredential $aadcred

Se a sua identidade AAD está associada a maior do que um AAD diretório:

    cd C:\CloudDeployment\Configuration
    $adminpass = ConvertTo-SecureString "<LOCAL ADMIN PASSWORD>" -AsPlainText -Force
    $aadpass = ConvertTo-SecureString "<AAD GLOBAL ADMIN ACCOUNT PASSWORD>" -AsPlainText -Force
    $aadcred = New-Object System.Management.Automation.PSCredential ("<AAD GLOBAL ADMIN ACCOUNT> example: user@AADDirName.onmicrosoft.com>", $aadpass)
    .\InstallAzureStackPOC.ps1 -AdminPassword $adminpass -AADAdminCredential $aadcred -AADDirectoryTenantName "<SPECIFIC AAD DIRECTORY example: AADDirName.onmicrosoft.com>"

Se o seu ambiente não tiver DHCP ativado, tem de incluir os seguintes parâmetros adicionais para uma das opções acima (exemplo de utilização fornecidos):

    .\InstallAzureStackPOC.ps1 -AdminPassword $adminpass -AADAdminCredential $aadcred
    -NatIPv4Subnet 10.10.10.0/24 -NatIPv4Address 10.10.10.3 -NatIPv4DefaultGateway 10.10.10.1


### <a name="installazurestackpocps1-optional-parameters"></a>Parâmetros opcionais InstallAzureStackPOC.ps1

| Parâmetro | Obrigatório/opcional | Descrição |
| --------- | ----------------- | ----------- |
| AADAdminCredential | Opcional | Define o nome de utilizador do Azure Active Directory e a palavra-passe. Estas credenciais Azure podem ser um ID de organização ou uma Account Microsoft. Para utilizar as credenciais da Microsoft Account, omita este parâmetro no cmdlet. Omissão este parâmetro solicita-lhe o pop-up autenticação Azure durante a implementação. Esta ação cria os tokens de autenticação e atualização utilizados durante a implementação. |
| AADDirectoryTenantName | Obrigatório | Define o directório de inquilino. Utilize este parâmetro para especificar um diretório específico onde o AAD possui permissões para gerir vários directórios. Total de nome de um inquilino do diretório AAD no formato de <directoryName>. onmicrosoft.com. |
| AdminPassword | Obrigatório | Define a conta de administrador local e todas as outras contas de utilizador em todas as máquinas virtuais criadas como parte de implementação de conceito. Esta palavra-passe tem de corresponder a atual palavra-passe de administrador local no anfitrião do. |
| AzureEnvironment | Opcional | Selecione o ambiente do Azure com o qual pretende registar esta implementação do Azure pilha. Opções de incluem *Azure público*, *Azure - China*, *Azure - Governo dos E.U.A*. |
| EnvironmentDNS | Opcional | Um servidor DNS é criado como parte de implementação do Azure pilha. Para permitir que os computadores dentro da solução para resolver nomes de fora do carimbo de, forneça o seu servidor DNS infraestrutura existente. O servidor DNS no carimbo de data / reencaminha pedidos de resolução do nome desconhecido para este servidor. |
| NatIPv4Address | Necessário para o suporte de DHCP NAT | Define um endereço IP estático para MAS BGPNAT01. Utilize apenas este parâmetro se o DHCP não pode atribuir um endereço IP válido para aceder à Internet. |
| NatIPv4DefaultGateway | Necessário para o suporte de DHCP NAT | Define o gateway predefinido utilizado com o endereço IP estático para MAS BGPNAT01. Utilize apenas este parâmetro se o DHCP não pode atribuir um endereço IP válido para aceder à Internet.  |
| NatIPv4Subnet | Necessário para o suporte de DHCP NAT | Prefixo de sub-rede IP utilizado para DHCP sobre suporte NAT. Utilize apenas este parâmetro se o DHCP não pode atribuir um endereço IP válido para aceder à Internet.  |
| PublicVLan | Opcional | Define o ID da VLAN. Utilize apenas este parâmetro se o anfitrião e MAS BGPNAT01 tem de configurar VLAN ID para aceder à rede física (e Internet). Por exemplo,`.\InstallAzureStackPOC.ps1 –Verbose –PublicVLan 305` |
| Voltar a executar | Opcional | Utilize este sinalizador para execute novamente a implementação.  São utilizada a todas as entradas anterior. Voltar a introduzir dados fornecidos anteriormente não são suportados porque vários valores exclusivos são gerados e utilizados para implementação. |
| Servidor de horas | Opcional | Utilize este parâmetro se precisar de especificar um servidor de tempo específico. |

## <a name="next-steps"></a>Próximos passos

[Ligar a pilha Azure](azure-stack-connect-azure-stack.md)
