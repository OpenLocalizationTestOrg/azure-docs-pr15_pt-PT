## <a name="download-install-and-register-the-azure-backup-agent"></a>Transferir, instalar e registar o agente de cópia de segurança do Azure

Depois de criar o Cofre Azure cópia de segurança, um agente deve ser instalado em cada um dos seus máquinas com o Windows (Windows Server, cliente do Windows, servidor do Gestor de proteção de dados do sistema centro ou máquina de servidor de cópia de segurança do Azure) que permite a cópia de segurança de dados e aplicações para o Azure.

1. Inicie sessão no [Portal de gestão](https://manage.windowsazure.com/)

2. Clique em **Serviços de recuperação**e, em seguida, selecione o Cofre cópia de segurança que pretende registar com um servidor. É apresentada a página de início rápido para esse cofre cópia de segurança.

    ![Guia de introdução](./media/backup-install-agent/quickstart.png)

3. Na página de início rápido, clique na opção **para o Windows Server ou Gestor de proteção de dados do sistema centro ou cliente do Windows** em **Transferir agente**. Clique em **Guardar** para copiá-lo para o computador local.

    ![Guardar agente](./media/backup-install-agent/agent.png)

4. Quando o agente estiver instalado, faça duplo clique MARSAgentInstaller.exe para iniciar a instalação do agente de cópia de segurança do Azure. Selecione a pasta de instalação e a pasta Rascunho necessários para o agente. A localização da cache especificada tem de ter espaço livre que é pelo menos 5% dos dados da cópia de segurança.

5.  Se utilizar um servidor proxy para ligar à internet, no ecrã de **configuração do Proxy** , introduza os detalhes de servidor de proxy. Se utilizar um proxy autenticado, introduza os detalhes de nome e palavra-passe de utilizador neste ecrã.

6.  O agente de cópia de segurança do Azure instala .NET Framework 4,5 e do Windows PowerShell (se ainda não esteja disponível) para concluir a instalação.

7.  Quando o agente estiver instalado, clique no botão de **proceder ao registo** para continuar com o fluxo de trabalho.

    ![Registe-se](./media/backup-install-agent/register.png)

8. No ecrã de credenciais do cofre, procure e selecione o ficheiro de credenciais do cofre que foi transferido anteriormente.

    ![Credenciais do Cofre](./media/backup-install-agent/vc.png)

    O ficheiro de credenciais do Cofre é válido apenas para 48 hrs (após ser transferido a partir do portal). Se deparar com qualquer erro neste ecrã (por exemplo "ficheiro de credenciais do cofre fornecida expirou"), inicie sessão no Azure portal e transferir novamente o ficheiro de credenciais do cofre.

    Certifique-se de que o ficheiro de credenciais do Cofre está disponível numa localização que possa ser acedida pela aplicação de configuração. Se encontrar erros relacionados de acesso, copie o ficheiro de credenciais do cofre para uma localização temporária nesta máquina e repita a operação.

    Se se deparar com um erro de credenciais do Cofre inválido (por exemplo, "credenciais Cofre inválido fornecidas") o ficheiro está danificado ou não tiver as mais recentes credenciais associadas com o serviço de recuperação. Repetir a operação depois de transferir um ficheiro de credenciais do cofre novo a partir do portal. Este erro é normalmente visto se o utilizador clica na opção de **credenciais do Cofre de transferência** no portal do Azure, em rápida sucessão. Neste caso, apenas o segundo cofre credencial ficheiro é válido.

9. No ecrã de **definição de encriptação** , pode gerar uma frase ou fornecer uma frase de acesso (mínimo de 16 caracteres). Lembre-se guardar a frase de acesso numa localização segura.

    ![Encriptação](./media/backup-install-agent/encryption.png)

    > [AZURE.WARNING] Se a frase de acesso for perdida ou esquecida; Não pode ajudar a Microsoft recuperar os dados de cópia de segurança. É o proprietário do utilizador final a frase de acesso de encriptação e a Microsoft não tem visibilidade da frase de acesso utilizado pelo utilizador final. Guarde o ficheiro numa localização segura conforme for necessário durante uma operação de recuperação.

10. Depois de clicar no botão **Terminar** , o computador está registado com êxito ao Cofre de palavras e está agora pronto para começar a cópia de segurança para Microsoft Azure.

11. Ao utilizar a cópia de segurança do Microsoft Azure autónomo pode modificar as definições de especificadas durante o fluxo de trabalho de registo ao clicar na opção de **Alterar as propriedades** no snap in da mmc Azure cópia de segurança.

    ![Alterar as propriedades](./media/backup-install-agent/change.png)

    Em alternativa, ao utilizar o Gestor de proteção de dados, pode modificar as definições de especificadas durante o fluxo de trabalho de registo ao clicar na opção de **Configurar** selecionando **Online** no separador **Gestão** .

    ![Configurar a cópia de segurança do Azure](./media/backup-install-agent/configure.png)
