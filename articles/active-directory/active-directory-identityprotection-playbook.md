<properties
    pageTitle="Playbook do Azure Active Directory identidade Protection | Microsoft Azure"
    description="Saiba como a proteção de identidade do Azure AD permite-lhe limitar a capacidade de um ataque para explorar um dispositivo ou comprometida identidade e de proteger uma identidade ou um dispositivo que suspeitos ou conhecido por ser comprometida anteriormente."
    services="active-directory"
    keywords="proteção de identidade do Azure do active directory, deteção de aplicação de nuvem, gerir aplicações, segurança, risco, nível de risco, vulnerabilidade, política de segurança"
    documentationCenter=""
    authors="markusvi"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="08/22/2016"
    ms.author="markvi"/>

#<a name="azure-active-directory-identity-protection-playbook"></a>Azure playbook de proteção de identidade do Active Directory 

Este playbook ajuda-o a:

- Preencher dados no ambiente de proteção de identidade ao simulação de risco de eventos e vulnerabilidades
- Configurar políticas de acesso condicional com base em risco e testar o impacto destas políticas


## <a name="simulating-risk-events"></a>Simulação de eventos de risco

Esta secção fornece passos para simular os seguintes tipos de evento de risco:

- Inícios de sessão a partir de endereços IP anónimos (fácil)
- Inícios de sessão a partir de localizações não está familiarizados (Moderada)
- Viagem impossibilita atípicas localizações (de difícil acesso)

Não podem ser simulados outros eventos de risco de uma forma segura.


### <a name="sign-ins-from-anonymous-ip-addresses"></a>Inícios de sessão a partir de endereços IP anónimos

Este tipo de evento de risco identifica os utilizadores que iniciar sessão com êxito a partir de um endereço IP que identificou como um endereço IP proxy anónima. Estes proxies são utilizados por pessoas que pretende ocultar o endereço IP do seu dispositivo e podem ser utilizadas para ações mal intencionadas maliciosa.

**Para simular uma sessão a partir de um inquérito anónimo, execute os seguintes passos**:

1.  Transferir o [Browser especificações técnicas](https://www.torproject.org/projects/torbrowser.html.en).
2.  Utilizar o Browser especificações técnicas, navegue até ao [https://myapps.microsoft.com](https://myapps.microsoft.com).   
3.  Introduza as credenciais da conta que pretende que apareça no relatório de **inícios de sessão a partir de endereços IP anónimos** .

O início de sessão no irá aparecer no dashboard de proteção de identidade dentro de 5 minutos. 


###<a name="sign-ins-from-unfamiliar-locations"></a>Inícios de sessão a partir de localizações não está familiarizados

O risco de localizações não está familiarizado é um mecanismo de avaliação de início de sessão em tempo real que considera anteriores localizações de início de sessão (IP, Latitude / Longitude e ASN) para determinar as localizações das novas / familiarizadas. O sistema armazena IPs anterior, Latitude / Longitude e ASNs de um utilizador e considera estas definições para ser familiares localizações. Uma localização de início de sessão é considerada não está familiarizada se a localização de início de sessão não corresponder a qualquer uma das localizações familiares existentes.

Proteção de identidade do Azure Active Directory:  

 - tem um período inicial formação de 14 dias durante os quais não sinalizar qualquer novas localizações como localizações não está familiarizadas.
 - ignora inícios de sessão a partir de dispositivos familiares e localizações que estão geograficamente fechar para uma localização familiar existente.

Para simular localizações não está familiarizadas, tem de iniciar sessão a partir de uma localização e o dispositivo que a conta tem não tiver sessão iniciada de antes. 


**Para simular uma sessão a partir de uma localização não está familiarizada, execute os seguintes passos**:

1.  Selecione uma conta que tenha pelo menos um dia de 14 início de sessão no histórico. 

2.  Efetue um:
    
    um. Ao utilizar uma VPN, navegue para [https://myapps.microsoft.com](https://myapps.microsoft.com) e introduza as credenciais da conta que pretende simular o evento de risco para.

    b. Pedir a um colega numa localização diferente para iniciar sessão com as credenciais da conta (não recomendadas).

O início de sessão no irá aparecer no dashboard de proteção de identidade dentro de 5 minutos.
 
### <a name="impossible-travel-to-atypical-location"></a>Deslocações impossibilita à localização atípica
Simulação a condição de viagem impossibilita é difícil porque o algoritmo de utiliza para infestantes saída falsos positivos como viagem impossibilita a partir de dispositivos familiares ou inícios de sessão a partir do VPNs são utilizadas por outros utilizadores no diretório de aprendizagem automática. Para além disso, o algoritmo requer um histórico de início de sessão de 3 a 14 dias para o utilizador antes de começar a gerar eventos de risco.

**Para simular uma viagem impossibilita atípicas localização, execute os seguintes passos**:

1.  Utilizar o seu browser padrão, navegue até ao [https://myapps.microsoft.com](https://myapps.microsoft.com).  

2.  Introduza as credenciais da conta que pretende gerar um evento de risco de viagem impossibilita para.

3.  Altere o seu agente do utilizador. Pode alterar agente do utilizador no Internet Explorer de ferramentas de programador ou alterar o seu agente do utilizador no Firefox ou Chrome utilizando um suplemento para mudar de agente do utilizador.

4.  Altere o seu endereço IP. Pode alterar o seu endereço IP, utilizando uma VPN, um suplemento do especificações técnicas, ou uma máquina de nova no Azure num centro de dados diferentes em rotação.

5.  Iniciar sessão para [https://myapps.microsoft.com](https://myapps.microsoft.com) utilizando as mesmas credenciais como antes, dentro de alguns minutos após o anterior iniciar sessão.

O início de sessão no irá aparecer no dashboard de proteção de identidade dentro de 2 a 4 horas.<br>
Devido a modelos envolvidos de aprendizagem automática complexa, existe a oportunidade que irá obter não selecionado para cima.<br> Poderá pretender criar uma réplica estes passos para várias contas do Azure AD.


## <a name="simulating-vulnerabilities"></a>Simulação vulnerabilidades 
Vulnerabilidades são fraquezas um ambiente do Azure AD que pode ser tirar partido do ator bad. Atualmente 3 tipos de vulnerabilidades forem apresentados no proteção de identidade do Azure AD que tirar partido outras funcionalidades do Azure AD. Estas vulnerabilidades serão apresentadas no dashboard de proteção de identidade automaticamente depois destas funcionalidades estão configuradas.

-   Azure AD [autenticação Multifator?](../multi-factor-authentication/multi-factor-authentication.md)
-   Azure AD [Deteção de aplicação na nuvem](active-directory-cloudappdiscovery-whatis.md).
-   [Gestão de identidades privilegiados](active-directory-privileged-identity-management-configure.md)do Azure AD. 



##<a name="user-compromise-risk"></a>Risco de compromisso do utilizador

**Para testar o risco de compromisso do utilizador, execute os seguintes passos**:

1.  Inicie sessão no [https://portal.azure.com](https://portal.azure.com) com credenciais de administrador global do seu inquilino.

2.  Navegue para **protecção de identidade**. 

3.  No pá de **proteção de identidade do Azure AD** principal, clique em **Definições**. 

4.  No pá **Definições do Portal** , em **regras de segurança**, clique em **utilizador comprometer risco**. 

5.  No pá **risco de início de sessão** , desativar o **Activar a regra** e, em seguida, clique em **Guardar** definições.

6.  Para uma conta de utilizador determinado, pode simular um familiarizado localizações ou evento de risco IP anónimo. Isto irá elevar o nível de risco de utilizador para esse utilizador para **médio**.

7.  Aguarde alguns minutos e, em seguida, confirme que o nível de utilizador para o utilizador está **médio**.

8.  Vá para o pá de **Definições do Portal** .

9.  No pá **Utilizador comprometer risco** , em **Ativar regra**, selecione **sobre** . 

10. Selecione uma das seguintes opções:

    um. Para bloquear, selecione **médio** em **Bloco de início de sessão**.

    b. Para impor a alteração da palavra-passe segura, selecione **médio** em **exigir autenticação multifator**.

13. Clique em **Guardar**.

14. Agora pode testar o acesso condicional com base em risco ao iniciar sessão com um utilizador com um nível de risco elevado. Se o risco de utilizador é médio, consoante a configuração da sua política, o início de sessão no está ser quer bloqueados ou são forçada para alterar a palavra-passe. 
<br><br>
![Playbook](./media/active-directory-identityprotection-playbook/201.png "Playbook")
<br>

 
##<a name="sign-in-risk"></a>Início de sessão no risco

 
**Para experimentar o início de sessão no risco, execute os seguintes passos:**

1.  Inicie sessão no [https://portal.azure.com](https://portal.azure.com) com credenciais de administrador global do seu inquilino.

2.  Navegue para **protecção de identidade**.

3.  No pá de **proteção de identidade do Azure AD** principal, clique em **Definições**. 

4.  No pá **Definições do Portal** , em **regras de segurança**, clique em **Iniciar sessão no risco**.

5.  No pá **risco de início de sessão **, selecione **** em **Ativar regra**. 

7.  Selecione uma das seguintes opções:

    um. Para bloquear, selecione **médio** em **Bloco de início de sessão**

    b. Para impor a alteração da palavra-passe segura, selecione **médio** em **exigir autenticação multifator**.

8.  Para bloquear, selecione médio em bloco início de sessão.

9.  Para impor a autenticação multifator, selecione **médio** em **exigir autenticação multifator**.

10. Clique em **Guardar**.

11. Agora pode testar o acesso condicional com base em risco através da simulação de localizações não está familiarizadas ou eventos de risco IP anónimos, porque são ambos os eventos de risco **médio** .

<br>
![Playbook](./media/active-directory-identityprotection-playbook/200.png "Playbook")
<br>


## <a name="see-also"></a>Consulte também

 - [Proteção de identidade do Azure Active Directory](active-directory-identityprotection.md)
