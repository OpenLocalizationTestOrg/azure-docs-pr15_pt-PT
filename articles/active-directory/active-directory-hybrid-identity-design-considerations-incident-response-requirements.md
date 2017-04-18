
<properties
    pageTitle="Azure Active Directory híbrido identidade considerações de estrutura - determinar rResponse incidente requisitos | Requisitos do Microsoft Azure "
    description="Determinar as capacidades de monitorização e informação para a solução de identidade híbrido que podem ser utilizadas por IT para efetuar ações para identificar e mitigar um ameaças potenciais"
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

# <a name="determine-incident-response-requirements-for-your-hybrid-identity-solution"></a>Determinar resposta incidente requisitos para a sua solução de identidade híbrido

Grandes ou médias empresas provavelmente terá uma [resposta incidente de segurança](https://technet.microsoft.com/library/cc700825.aspx) no local para o ajudar a IT efetuar ações, consoante adequado para o nível do incidente. O sistema de gestão de identidades é um componente importante no processo de resposta a incidentes porque pode ser utilizada para o ajudar a identificar quem executou uma ação contra de destino específica. A solução de identidade híbrido tem de conseguir fornecem capacidades de monitorização e elaboração de relatórios que podem ser utilizadas por IT para efetuar ações para identificar e mitigar uma ameaça potencial. Um plano de resposta incidente típica terá as seguintes fases como parte do plano de:

1.  Avaliação inicial.
2.  Comunicação incidente.
3.  Controlo de danos e redução de risco.
4.  Identificação que estava comprometer e gravidade.
5.  Preservação de prova.
6.  Notificação para partes adequadas.
7.  Recuperação do sistema.
8.  Documentação.
9.  Avaliação de danos e o custo.
10. Revisão processo e plano.

Durante a identificação do que foi comprometida e gravidade fase, será necessário identificar os sistemas comprometidos, ficheiros que tenham sido acedidos e determinam a sensibilidade desses ficheiros. O sistema de identidade híbrido deverá conseguir cumprir estes requisitos para o ajudar a identificar o utilizador que fez essas alterações. 

## <a name="monitoring-and-reporting"></a>Monitorização e relatórios
Número de vezes o identidade sistema também pode ajudar na fase de avaliação inicial, sobretudo se o sistema tiver criado no auditoria e capacidades de elaboração de relatórios. Durante a avaliação inicial, administrador de TI tem de conseguir identificar uma atividade suspeita ou o sistema deverá conseguir accionador automaticamente com base numa tarefa pré-configurada. Muitas actividades podem indicar um possível ataque, no entanto, noutros casos, um sistema mal configurado pode conduzir a um número de falsos num sistema de deteção intrusos. 

O sistema de gestão de identidades deverá assistir IT administradores para identificar e comunicar essas actividades suspeitas. Normalmente, estes requisitos técnicos podem ser preenchidos por todos os sistemas de monitorização e ter uma capacidade de elaboração de relatórios que pode realçar ameaças potenciais. Utilize as perguntas abaixo para ajudá-lo a sua solução de identidade híbrido enquanto, tendo em requisitos de resposta incidente considerações de estrutura:

- Sua empresa tiver uma resposta do incidente de segurança no local?
 - Se Sim, é o sistema de gestão de identidades atual utilizado como parte do processo de?
- É que a sua empresa necessita identificar tentativas de início de sessão suspeitas de utilizadores em todos os dispositivos diferentes?
- É que a sua empresa necessita detetar potenciais comprometida credenciais do utilizador?
- Sua empresa necessitam de acesso de utilizador e ação de auditoria?
- É que a sua empresa necessita saber quando um utilizador repor a sua palavra-passe?

## <a name="policy-enforcement"></a>Imposição de políticas

Durante o controlo de danos e risco redução de fase, é importante reduzir rapidamente os efeitos reais e potenciais de um ataque. Neste momento, essa ação que vai levar pode tornar a diferença entre uma secundárias e um principal. A resposta exata irá variam consoante a sua organização e da natureza do ataque que lhe cara. Se a avaliação inicial celebração do que uma conta foi comprometida, terá de impor política para bloquear esta conta. Esse é o único exemplo onde o sistema de gestão de identidades irão ser utilizado. Utilize as perguntas abaixo para ajudá-lo a sua solução de identidade híbrido de estrutura, tendo em consideração como serão impostas políticas para responder a um incidente em curso:

- A sua empresa tem políticas num local de bloquear utilizadores a partir do access a rede se for necessário?
 - Se Sim, a solução actual integrar o sistema de gestão de identidades híbrido passar para adotar?
- É que a sua empresa necessita impor acesso condicional para utilizadores que estão em quarentena? 
 
>[AZURE.NOTE]
Certifique-se tomar notas de cada resposta e compreender a lógica por detrás a resposta. [Definir estratégia de proteção de dados](active-directory-hybrid-identity-design-considerations-data-protection-strategy.md) irá abordar as opções disponíveis e vantagens/desvantagens de cada opção.  Por ter respondido estas perguntas que irá selecionar qual a opção que melhor se adequa a sua empresa necessita.

## <a name="next-steps"></a>Próximos passos
[Definir estratégia de proteção de dados](active-directory-hybrid-identity-design-considerations-data-protection-strategy.md)

## <a name="see-also"></a>Consulte também
[Descrição geral de considerações de estrutura](active-directory-hybrid-identity-design-considerations-overview.md)
