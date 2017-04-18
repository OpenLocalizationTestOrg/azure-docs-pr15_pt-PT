## <a name="using-vault-credentials-to-authenticate-with-the-azure-backup-service"></a>Com as credenciais de cofre para autenticar com o serviço de cópia de segurança do Azure

O servidor no local (cliente do Windows ou Windows Server ou do Gestor de proteção de dados de servidor) tem de ser autenticada com uma cópia de segurança cofre antes de-pode agregar dados para Azure. A autenticação é obtida utilizando "credenciais do Cofre de palavras". O conceito de credenciais do Cofre é semelhante ao conceito de um ficheiro "definições de publicação" que é utilizado no Azure PowerShell.

### <a name="what-is-the-vault-credential-file"></a>O que é o ficheiro de credenciais do Cofre?

O ficheiro de credenciais do Cofre é um certificado gerado pelo portal para cada cofre cópia de segurança. O portal, em seguida, os carregamentos pendentes a chave pública para o serviço de controlo de acesso (ACS). A chave privada do certificado é disponibilizada para o utilizador como parte do fluxo de trabalho que é fornecido como uma entrada no fluxo de trabalho de registo de máquina. Isto autentica a máquina para enviar dados de cópia de segurança para um cofre identificado no serviço de cópia de segurança do Azure.

A credencial Cofre é utilizada apenas durante o fluxo de trabalho do registo. Cabe do utilizador para se certificar de que o ficheiro de credenciais Cofre não está comprometido. Se cair mãos de qualquer utilizador não autorizado, o ficheiro de credenciais de cofre pode ser utilizado para registar outras máquinas contra o mesmo cofre. No entanto, como os dados de cópia de segurança são encriptados através de uma frase de acesso que pertence ao cliente, não podem ser comprometidos dados de cópia de segurança existentes. Para mitigar este preocupações, a cofre credenciais estão definidas para expirar no 48hrs. Pode transferir as credenciais do cofre do cofre cópia de segurança qualquer número de vezes – mas que apenas o ficheiro de credenciais de cofre mais recente é aplicável durante o fluxo de trabalho do registo.

### <a name="download-the-vault-credential-file"></a>Transferir o ficheiro de credenciais do Cofre

O ficheiro de credenciais do Cofre é transferido através de um canal seguro a partir do portal Azure. O serviço de cópia de segurança do Azure é ignora da chave privada do certificado e a chave privada não é mantida no portal do ou o serviço. Utilize os passos seguintes para transferir o ficheiro de credenciais do cofre para um computador local.

1.  Inicie sessão no [Portal de gestão](https://manage.windowsazure.com/)
2.  Clique nos **Serviços de recuperação** no painel de navegação do lado esquerdo e selecione o Cofre cópia de segurança que criou. Clique no ícone da nuvem para aceder à vista de guia de introdução a cópia de segurança cofre.

    ![Vista rápida](./media/backup-download-credentials/quickview.png)

3.  Na página de início rápido, clique em **credenciais do Cofre de transferência**. O portal gera o ficheiro de credenciais do cofre, que é feito disponível para transferência.

    ![Transferir](./media/backup-download-credentials/downloadvc.png)

4.  O portal irá gerar uma credencial de cofre utilizando uma combinação do nome de cofre e a data atual. Clique em **Guardar** para transferir as credenciais de cofre para a pasta de transferências da conta local ou, selecione guardar como no menu Guardar para especificar uma localização para as credenciais de cofre.

### <a name="note"></a>Nota
- Certifique-se de que as credenciais de cofre é guardado numa localização que pode ser acedida a partir do seu computador. Se este é armazenado numa partilha de ficheiro/SMB, verifique as permissões de acesso.
- O ficheiro de credenciais Cofre é utilizado apenas durante o fluxo de trabalho do registo.
- O ficheiro de credenciais do cofre expira após 48hrs e pode ser transferido a partir do portal.
- Referem-se para a cópia de segurança do Azure as [Perguntas mais frequentes](../articles/backup/backup-azure-backup-faq.md) para qualquer questões sobre o fluxo de trabalho.
