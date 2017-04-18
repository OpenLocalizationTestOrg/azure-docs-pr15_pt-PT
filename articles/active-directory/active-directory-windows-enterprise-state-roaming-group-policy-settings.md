<properties
    pageTitle="Definições de política e a MDM grupo | Microsoft Azure"
    description="Fornece informações sobre a política de grupo e do dispositivo móvel as definições de gestão (MDM) que devem ser utilizadas em dispositivos empresarial propriedade. Estas políticas são aplicadas para o dispositivo do utilizador inteira."
    services="active-directory"
    keywords="o que são política de grupo e definições da MDM de Roaming do Estado de empresa, Roaming de estado de empresa, nuvem do windows"
    documentationCenter=""
    authors="femila"
    manager="swadhwa"
    editor="curtand"/>

<tags
    ms.service="active-directory"  
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="femila"/>

# <a name="group-policy-and-mdm-settings"></a>Definições de política de grupo e a MDM

Utilize estas política de grupo e as definições de gestão (MDM) do dispositivo móvel apenas em dispositivos empresarial propriedade uma vez que estas políticas são aplicadas para o dispositivo do utilizador inteira. Aplicar uma política de MDM para desativar a sincronização de definições para uma pessoal, propriedade de um utilizador dispositivo terão um impacto negativo a utilização de que o dispositivo. Para além disso, as outras contas de utilizador no dispositivo, também serão afetadas pela política.

Empresas que queira gerir roaming para pessoais dispositivos (que não é geridos) podem utilizar o portal do Azure para ativar ou desativar roaming em vez de utilizar a política de grupo ou a MDM.
As tabelas seguintes descrevem as definições de política disponíveis.

## <a name="mdm-settings"></a>Definições de MDM
Aplicam as definições de políticas da MDM para Windows 10 e Windows 10 Mobile.  Existe suporte do Windows 10 Mobile apenas para a conta Microsoft com base roaming através da conta do OneDrive do utilizador.  Consulte a secção "Dispositivos e os pontos finais" para obter detalhes sobre que dispositivos são suportados para a sincronização do Azure AD com base.

| Nome                               | Descrição                                                          |
|------------------------------------|----------------------------------------------------------------------|
| Permitir a ligação de conta Microsoft | Permite aos utilizadores autenticar com uma conta Microsoft no dispositivo |
| Permitir a sincronizar as minhas definições             | Permite aos utilizadores alternar as definições do Windows e dados de aplicação; Desativar esta política serão desativar a sincronização, bem como cópias de segurança em dispositivos móveis                  |

## <a name="group-policy-settings"></a>Definições de política de grupo
Aplicam as definições de política de grupo para dispositivos Windows 10, que são associados a um domínio do Active Directory. A tabela também inclui legadas as definições que apareceriam gerir as definições de sincronização, mas que não funcionam para Enterprise Estado Roaming para Windows 10, que estão anotadas com 'não utilizar' na descrição.

| Nome                                | Descrição |
|-------------------------------------|-------------|
| Contas: Contas do Microsoft de bloco  |Esta definição de política impede que os utilizadores adicionar novas contas Microsoft neste computador|
| Posso sincronizar não                         |Impede que os utilizadores para alternar as definições do Windows e dados de aplicação|
| Posso sincronizar não personalizar             |Desativa sincronizar do grupo temas|
| Posso sincronizar as definições do browser        |Desativa sincronizar do grupo do Internet Explorer|
| Posso sincronizar as palavras-passe               |Desativa sincronizar do grupo de palavras-passe|
| Posso sincronizar outras definições do Windows  |Desativa sincronização de grupo do Windows outras definições|
| Posso sincronizar personalização de ambiente de trabalho |Não utilize; não tem efeito|
| Não sincronizar com ligações com tráfego limitado  |Desativa roaming com tráfego limitado ligações, tal como 3G móvel|
| Posso sincronizar as aplicações                    |Não utilize; não tem efeito|
|Posso sincronizar as definições da aplicação             |Desativa roaming de dados de aplicação|
|Posso sincronizar as definições de início           |Não utilize; não tem efeito|


## <a name="related-topics"></a>Tópicos relacionados
- [Descrição geral de Roaming do Estado de empresa](active-directory-windows-enterprise-state-roaming-overview.md)
- [Activar o estado da empresa roaming no Azure Active Directory](active-directory-windows-enterprise-state-roaming-enable.md)
- [Definições e dados roaming perguntas mais frequentes](active-directory-windows-enterprise-state-roaming-faqs.md)
- [Referência de definições guardado no servidor do Windows 10](active-directory-windows-enterprise-state-roaming-windows-settings-reference.md)
