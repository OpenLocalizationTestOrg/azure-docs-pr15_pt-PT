<properties
   pageTitle="Testes de caneta | Microsoft Azure"
   description="O artigo fornece uma descrição geral do realizados testes de processo (pentest) e como executar pentest contra as suas aplicações a ser executada em infraestrutura Azure."
   services="security"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor="TomSh"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/25/2016"
   ms.author="yurid"/>

# <a name="pen-testing"></a>Testes de caneta

Uma das coisas excelentes sobre a utilização do Microsoft Azure para testes de aplicações e implementação é que não precisa de juntar uma infraestrutura no local para desenvolver, testar e implementar a suas aplicações. Infraestrutura do está a tratar pelos serviços da plataforma Microsoft Azure. Não tem de se preocupar com requisitioning, adquirir e "trasfega e empilhar" seu próprio hardware no local.

Esta é ótima – mas ainda precisar para se certificar de que executar a segurança normal diligenciar. Uma das coisas que precisa de fazer é penetração testar as aplicações implementar no Azure.

Poderá já sabe que a Microsoft executa [testes de penetração do nosso ambiente Azure](https://gallery.technet.microsoft.com/Cloud-Red-Teaming-b837392e). Isto ajuda-na melhorar o nossa plataforma e guias de introdução do nossas ações em termos de melhorar a controlos de segurança, introdução aos novos controlos de segurança e melhorar o nossos processos de segurança.

Vamos não da caneta teste a sua aplicação para si, mas compreendemos irá pretendido e precisa de efetuar caneta testes no seus próprio aplicações. Que é uma boa ideia, uma vez quando melhorar a segurança das suas aplicações, ajudam a tornar o ecossistema Azure inteiro mais segura.

Quando caneta testar as suas aplicações,-possível aspeto ataque-nos. Estamos [continuamente monitorizar](http://blogs.msdn.com/b/azuresecurity/archive/2015/07/05/best-practices-to-protect-your-azure-deployment-against-cloud-drive-by-attacks.aspx) para ataque padrões e iniciará um processo de resposta do incidente se precisamos de. Ajudá-lo não e -não ajude-nos se podemos acionar uma resposta a incidentes devido a sua própria vencimento caneta diligência testes.

O que fazer?

Quando estiver pronto para caneta testar as aplicações alojado no Azure, é necessário diga-nos. Quando souber Recomendamos que vai ser efetuar testes específicos, podemos inadvertidamente não encerrar (como bloquear o endereço IP que estiver a testar), desde que os seus testes está em conformidade com o Azure caneta testes termos e condições.
Testes padrão que pode executar incluem:

- Testes no seu os pontos finais para descobrir as [aplicações do Abrir Web Project segurança (OWASP) superior vulnerabilidades de 10](https://www.owasp.org/index.php/Category:OWASP_Top_Ten_Project)
- [Testes de fuzz](https://blogs.microsoft.com/cybertrust/2007/09/20/fuzz-testing-at-microsoft-and-the-triage-process/) da sua os pontos finais
- [Pesquisa de portas](https://en.wikipedia.org/wiki/Port_scanner) da sua os pontos finais

Um tipo de teste que não pode efetuar é qualquer tipo de ataque [Negação de serviço (DoS)](https://en.wikipedia.org/wiki/Denial-of-service_attack) . Isto inclui iniciar um ataque que deve fazer propriamente dito ou efetuar testes relacionados que poderão determinar, demonstram ou simular qualquer tipo de ataque que deve fazer.

Está pronto começar a utilizar com a caneta testar as suas aplicações alojadas no Microsoft Azure? Se, por isso, em seguida, cabeça no sobre para um [Descrição geral de teste de penetração](https://security-forms.azure.com/penetration-testing/terms) da página (e clique em criar um botão Testar pedir na parte inferior da página. Também irá encontrar mais informações sobre a caneta testes termos e condições e hiperligações úteis sobre como pode comunicar falhas de segurança relacionadas com o Azure ou qualquer outro serviço de Microsoft.
