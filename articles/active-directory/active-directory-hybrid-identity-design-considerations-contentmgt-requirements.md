<properties
    pageTitle="Azure Active Directory híbrido identidade considerações de estrutura - determinar requisitos de gestão de conteúdo | Microsoft Azure"
    description="Fornece visão como determinar os requisitos de gestão de conteúdo da sua empresa. Normalmente, quando um utilizador tem a sua própria dispositivo ele poderá ter credenciais vários também serão alternados de acordo com a aplicação que ele utiliza. É importante diferenciar qual o conteúdo que foi criado utilizando credenciais pessoais versus aqueles criado com as credenciais da empresa. A solução de identidade deverá conseguir interagir com cloud services para fornecer uma experiência totalmente integrada do utilizador final enquanto garantir a sua privacidade e aumentar a proteção contra a perda de dados."
    documentationCenter=""
    services="active-directory"
    authors="billmath"
    manager="femila"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity" 
    ms.date="08/08/2016"
    ms.author="billmath"/>

# <a name="determine-content-management-requirements-for-your-hybrid-identity-solution"></a>Determinar requisitos de gestão de conteúdo para a sua solução de identidade híbrido

Noções sobre os requisitos de gestão de conteúdo para a sua empresa direct poderá afetar a sua decisão no qual híbrido identidade a solução para utilizar. Com a multiplicação de vários dispositivos e a funcionalidade de utilizadores para trazer os seus próprios dispositivos ([BYOD](http://aka.ms/byodcg)), a empresa tem proteger os seus próprios dados mas -lo também tem de manter privacidade do utilizador intacto. Normalmente, quando um utilizador tem a sua própria dispositivo ele poderá ter credenciais vários também serão alternados de acordo com a aplicação que ele utiliza. É importante diferenciar qual o conteúdo que foi criado utilizando credenciais pessoais versus aqueles criado com as credenciais da empresa. A solução de identidade deverá conseguir interagir com cloud services para fornecer uma experiência totalmente integrada do utilizador final enquanto garantir a sua privacidade e aumentar a proteção contra a perda de dados. 

A solução de identidade irão ser utilizada pelo controlos técnicos diferentes para fornecer gestão de conteúdo, tal como apresentado na figura abaixo:
 
![](./media/hybrid-id-design-considerations/securitycontrols.png)

**Controlos de segurança que vai ser tirar partido da sua sistema de gestão de identidades**

Em geral, requisitos de gestão de conteúdo serão tirar partido do seu sistema de gestão de identidades nas seguintes áreas:

- Privacidade: identificar o utilizador ao qual pertence um recurso e aplicar os controlos para manter a integridade dos adequados.
- Classificação de dados: identificar o utilizador ou grupo e nível de acesso a um objeto de acordo com a sua classificação. 
- Proteção contra a perda de dados: controlos de segurança responsáveis pela proteger os dados para evitar a perda terão de interagir com o sistema de identidade para validar a identidade do utilizador. Isto também é importante para fins de auditoria fiável objetivo.

>[AZURE.NOTE]
Leia a [classificação de dados para preparação da nuvem](http://download.microsoft.com/download/0/A/3/0A3BE969-85C5-4DD2-83B6-366AA71D1FE3/Data-Classification-for-Cloud-Readiness.pdf) para obter mais informações sobre procedimentos recomendados e directrizes para a classificação de dados.

Quando planear a sua solução de identidade híbrido Certifique-se de que as seguintes questões respondidas acordo com os requisitos da sua organização:

- A sua empresa tem os controlos de segurança no local para impor a privacidade de dados?
 - Se Sim, os controlos de segurança poderão integrar com a solução de identidade de híbrido passar para adotar?
- De que a sua empresa utiliza classificação de dados?
 - Sim, se a solução actual conseguirá integrar com a solução de identidade de híbrido passar para adotar?
- A sua empresa tem atualmente qualquer solução para perdas de dados? 
 - Sim, se a solução actual conseguirá integrar com a solução de identidade de híbrido passar para adotar?
- É que a sua empresa necessita acesso aos recursos de auditoria?
 - Se Sim, o que escrever dos recursos?
 - Se Sim, o nível de informações é necessário?
 - Se Sim, onde o registo de auditoria tem residem? No local ou na nuvem?
- É que a sua empresa necessita encriptar qualquer e-mails que contêm dados confidenciais (SSNs, números de cartão de crédito, etc)?
- É que a sua empresa necessita encriptar todos os documentos/conteúdo partilhado com parceiros externos?
- Sua empresa necessitam de impor políticas empresariais no determinados tipos de mensagens de correio eletrónico (não sem responder a todos, não reencaminhar)?
 
>[AZURE.NOTE]
Certifique-se tomar notas de cada resposta e compreender a lógica por detrás a resposta. [Definir estratégia de proteção de dados](active-directory-hybrid-identity-design-considerations-data-protection-strategy.md) irá abordar as opções disponíveis e vantagens/desvantagens de cada opção.  Por ter respondido estas perguntas que irá selecionar qual a opção que melhor se adequa a sua empresa necessita.


## <a name="next-steps"></a>Próximos passos
[Determinar requisitos de controlo de acesso](active-directory-hybrid-identity-design-considerations-accesscontrol-requirements.md)

## <a name="see-also"></a>Consulte também
[Descrição geral de considerações de estrutura](active-directory-hybrid-identity-design-considerations-overview.md)
