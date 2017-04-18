<properties
    pageTitle="Ligação do Azure AD: Activar o dispositivo de escrita não consolidada | Microsoft Azure"
    description="Este documento detalhes como ativar o dispositivo de escrita não consolidada utilizando a ligação do Azure AD"
    services="active-directory"
    documentationCenter=""
    authors="billmath"
    manager="femila"
    editor="curtand"/>

<tags
    ms.service="active-directory"  
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/29/2016"
    ms.author="billmath"/>

# <a name="azure-ad-connect-enabling-device-writeback"></a>Ligação do Azure AD: Ativar dispositivo escrita não consolidada

>[AZURE.NOTE] Uma subscrição do Azure AD Premium é necessária para o dispositivo de escrita não consolidada.

A seguinte documentação fornece informações sobre como ativar a funcionalidade de escrita não consolidada dispositivo na ligação do Azure AD. Repetição de escrita de dispositivo é utilizada nos cenários seguintes:

- Ativar o acesso condicional com base em dispositivos para ADFS (2012 R2 ou superior) protegido por aplicações (dependente festa fidedignidades).

Este procedimento fornece segurança adicional e assurance é concedido acesso às aplicações apenas a dispositivos fidedignos. Para mais informações sobre o acesso condicional, consulte o artigo [Gerir o risco com acesso condicional](active-directory-conditional-access.md) e [configurar o acesso a condicional no local com o Azure Active Directory dispositivo registo](https://msdn.microsoft.com/library/azure/dn788908.aspx).

>[AZURE.IMPORTANT]
<li>Dispositivos têm de estar localizados na mesma floresta como os utilizadores. Uma vez que dispositivos tem de ser escritos voltar a uma única floresta, esta funcionalidade não suporta atualmente uma implementação com várias florestas de utilizador.</li>
<li>Objecto de configuração de registo de apenas um dispositivo pode ser adicionado a floresta do Active Directory no local. Esta funcionalidade não é compatível com uma topologia de onde o Active Directory no local está sincronizado com vários directórios Azure AD.</li>

## <a name="part-1-install-azure-ad-connect"></a>Parte 1: Instalar Azure AD ligar
1. Instale a ligação do Azure AD utilizando personalizada ou Express definições. A Microsoft recomenda para começar com todos os utilizadores e grupos sincronizado com êxito antes de ativar o dispositivo de escrita não consolidada.

## <a name="part-2-prepare-active-directory"></a>Parte 2: Preparar o Active Directory
Utilize os seguintes passos para preparar a utilização de dispositivo de escrita não consolidada.

1.  A partir do computador onde está instalado a ligação do Azure AD, inicie o PowerShell no modo de elevada.

2.  Se não estiver instalado o módulo do Active Directory PowerShell, instalá-lo utilizando o seguinte comando:

    `Install-WindowsFeature –Name AD-Domain-Services –IncludeManagementTools`

3. Se não estiver instalado o módulo Azure Active Directory PowerShell, em seguida, transfira e instale-a partir do [Módulo Azure Active Directory para Windows PowerShell (versão de 64 bits)](http://go.microsoft.com/fwlink/p/?linkid=236297). Este componente tem uma dependência de início de sessão no assistente, que é instalado com a ligação do Azure AD.

4.  Com as credenciais de administrador de empresa, execute os seguintes comandos e, em seguida, saia do PowerShell.

    `Import-Module 'C:\Program Files\Microsoft Azure Active Directory Connect\AdPrep\AdSyncPrep.psm1'`

    `Initialize-ADSyncDeviceWriteback {Optional:–DomainName [name] Optional:-AdConnectorAccount [account]}`

São necessárias credenciais de administrador de empresa uma vez que são necessárias alterações para o espaço de nomes de configuração. Um administrador de domínio não terá permissões suficientes.

![PowerShell para ativar a repetição de escrita de dispositivo](./media/active-directory-aadconnect-feature-device-writeback/powershell.png)

Descrição:

- Se estes não existam já, cria e configura novos contentores e objetos em CN = Configuração de registo de dispositivo, CN = Services, CN = Configuration, [floresta-dn].
- Se estes não existam já, cria e configura novos contentores e objetos em CN = RegisteredDevices, [domínio-dn]. Objectos de dispositivo serão criados neste contentor.
- Define as permissões necessárias na conta do Azure AD conexão, a gestão de dispositivos no seu do Active Directory.
- Só precisa para executar o uma floresta, mesmo que a ligação do Azure AD está a ser instalado no várias florestas.

Parâmetros:

- NomeDomínio: Domínio do Active Directory onde objectos de dispositivo serão criados. Nota: Todos os dispositivos de um determinado floresta do Active Directory serão criados num único domínio.
- AdConnectorAccount: Conta do Active Directory que será utilizada por ligação do Azure AD para gerir objetos no diretório. Esta é a conta utilizada pelo ligação do Azure AD sync para ligar ao AD. Se tiver instalado o utilizando as definições express, é a conta com MSOL_ o prefixo.

## <a name="part-3-enable-device-writeback-in-azure-ad-connect"></a>Parte 3: Ativar dispositivo escrita não consolidada na ligação do Azure AD
Utilize o seguinte procedimento para ativar o dispositivo de escrita não consolidada na ligação do Azure AD.

1.  Execute o Assistente de instalação novamente. Selecione **Personalizar as opções de sincronização** a partir da página tarefas adicionais e clique em **seguinte**.
![Instalação personalizada personalizar as opções de sincronização](./media/active-directory-aadconnect-feature-device-writeback/devicewriteback2.png)
2.  Na página funcionalidades opcionais, escrita não consolidada dispositivo será já não estar a cinzento. Fórum nota que se a ligação do Azure AD passos de preparação não estão concluídas dispositivo escrita não consolidada vai ser desativada reduzir na página funcionalidades da opcional. Selecione a caixa do dispositivo de escrita não consolidada e clique em **seguinte**. Se a caixa de verificação ainda estiver desactivada, consulte a [secção de resolução de problemas](#the-writeback-checkbox-is-still-disabled).
![Funcionalidades opcionais do dispositivo de escrita não consolidada de instalação personalizada](./media/active-directory-aadconnect-feature-device-writeback/devicewriteback3.png)
3.  Na página de escrita não consolidada, verá o domínio fornecido como a floresta de repetição de escrita de dispositivo predefinido.
![Personalizado floresta de instalação do dispositivo de escrita não consolidada destino](./media/active-directory-aadconnect-feature-device-writeback/devicewriteback4.png)
4.  Conclua a instalação do assistente com sem alterações adicionais de configuração. Se for necessário, consulte a [instalação personalizada de ligação do Azure AD.](./connect/active-directory-aadconnect-get-started-custom.md)

## <a name="enable-conditional-access"></a>Ativar o acesso condicional
Obter instruções detalhadas para ativar este cenário estão disponíveis no prazo de [configurar o acesso a condicional no local com o Azure Active Directory dispositivo registo](https://msdn.microsoft.com/library/azure/dn788908.aspx).

## <a name="verify-devices-are-synchronized-to-active-directory"></a>Verifique se que dispositivos são sincronizados para o Active Directory
Dispositivo escrita não consolidada deverá agora estar a funcionar corretamente. Tenha em atenção que pode demorar até 3 horas para objetos de dispositivo estar anterior escritos para AD.  Para verificar se os dispositivos estão a ser sincronizados corretamente, faça o seguinte depois de concluir as regras de sincronização:

1.  Inicie o Centro de administração do Active Directory.
2.  Expanda RegisteredDevices, dentro do domínio que está a ser federado.
![Centro de administração do Active Directory registado dispositivos](./media/active-directory-aadconnect-feature-device-writeback/devicewriteback5.png)
3.  Dispositivos registados atuais serão listados aí.
![Centro de administração do Active Directory registado lista de dispositivos](./media/active-directory-aadconnect-feature-device-writeback/devicewriteback6.png)

## <a name="troubleshooting"></a>Resolução de problemas

### <a name="the-writeback-checkbox-is-still-disabled"></a>A caixa de verificação de escrita não consolidada ainda está a ser desactivada
Se a caixa de verificação de escrita não consolidada dispositivo não estiver ativada, apesar de ter seguido os passos acima, os passos seguintes irão guiá-lo através da qual o Assistente de instalação está a verificar antes da caixa está ativada.

Primeira coisas primeiro:

- Certificar-se pelo menos uma floresta 2012R2 do Windows Server. O tipo de objeto do dispositivo tem de ser apresentar.
- Se o Assistente de instalação já está em execução, todas as alterações não ser detetadas. Neste caso, conclua o Assistente de instalação e executá-la novamente.
- Certifique-se a conta que forneceu no script de inicialização realmente o utilizador corretas utilizado pelo conector do Active Directory. Para verificar isto, siga estes passos:
    - A partir do menu Iniciar, abra **O serviço de sincronização**.
    - Abra o separador de **conexões** .
    - Localizar o conector com o tipo Active Directory Domain Services e selecione.
    - Em **ações**, selecione **Propriedades**.
    - Aceda a **ligar a floresta do Active Directory**. Verifique se o nome de utilizador e domínio especificado nesta correspondência de ecrã a conta fornecida para o script.
![Conta de conexão no Gestor de serviços de sincronização](./media/active-directory-aadconnect-feature-device-writeback/connectoraccount.png)

Verificar a configuração no Active Directory:
- Certifique-se de que o serviço de registo de dispositivo está localizado na localização abaixo (CN = DeviceRegistrationService, CN = Serviços de registo de dispositivo, CN = Configuração de registo de dispositivo, CN = Services, CN = Configuration) em contexto de nomenclatura de configuração.

![Resolução de problemas, DeviceRegistrationService no espaço de nomes de configuração](./media/active-directory-aadconnect-feature-device-writeback/troubleshoot1.png)

- Verifique se existe apenas um objeto de configuração ao procurar o espaço de nomes de configuração. Se existir mais do que um, elimine o duplicado.

![Resolução de problemas, procure os objetos duplicados](./media/active-directory-aadconnect-feature-device-writeback/troubleshoot2.png)

- No objeto do serviço de registo de dispositivo, certifique-se o atributo msDS-DeviceLocation estiver presente e tem um valor. Pesquisa esta localização e certifique-se é apresentar com o valor de erro objectType msDS-DeviceContainer.

![Resolução de problemas, msDS DeviceLocation](./media/active-directory-aadconnect-feature-device-writeback/troubleshoot3.png)

![Resolução de problemas, RegisteredDevices classe de objecto](./media/active-directory-aadconnect-feature-device-writeback/troubleshoot4.png)

- Verifique se a conta utilizada pelo conector do Active Directory tiver as permissões necessárias no contentor registado dispositivos encontrado pela verificação passo anterior. Este é as esperado permissões neste contentor:

![Resolução de problemas, verificar permissões no contentor](./media/active-directory-aadconnect-feature-device-writeback/troubleshoot5.png)

- Verifique se a conta do Active Directory tem as permissões no CN = Configuração de registo de dispositivo, CN = Services, CN = objeto de configuração.

![Resolução de problemas, verifique as permissões de configuração do dispositivo de registo](./media/active-directory-aadconnect-feature-device-writeback/troubleshoot6.png)

## <a name="additional-information"></a>Informações adicionais
- [Gestão de riscos com acesso condicional](active-directory-conditional-access.md)
- [Configurar o acesso a condicional no local com o Azure Active Directory dispositivo registo](https://msdn.microsoft.com/library/azure/dn788908.aspx)

## <a name="next-steps"></a>Próximos passos
Saiba mais sobre como [integrar a suas identidades no local com o Azure Active Directory](active-directory-aadconnect.md).
