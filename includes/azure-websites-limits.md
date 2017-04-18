Recurso|Gratuito|Partilhado (pré-visualização)|Básicas|Padrão|Premium (pré-visualização)</th>
---|---|---|---|---|---
[Web, telemóvel ou aplicações de API](https://azure.microsoft.com/services/app-service/) por [aplicação de serviço de plano](../articles/app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)<sup>1</sup>|10|100|Ilimitado<sup>2</sup>|Ilimitado<sup>2</sup>|Ilimitado<sup>2</sup>
[Aplicações de lógica](https://azure.microsoft.com/services/app-service/logic/) pelo [plano de serviço de aplicação](../articles/app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)</a><sup>1</sup>|10|10|10|20 por core|20 por core
[Plano de serviço de aplicação](../articles/app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md)|1 por região|10 por grupo de recursos|100 por grupo de recursos|100 por grupo de recursos|100 por grupo de recursos
Calcular o tipo de instância|Partilhado|Partilhado|Dedicada<sup>3</sup>|Dedicada<sup>3</sup>|Dedicada<sup>3</sup></p>
[Escala de saída](../articles/app-service-web/web-sites-scale.md) (máximos instâncias)|1 partilhada|1 partilhada|3 dedicada<sup>3</sup>|10 dedicada<sup>3</sup>|20 dedicado (50 no Auxiliar)<sup>3,4</sup>
Armazenamento<sup>5</sup>|1 GB<sup>5</sup>|1 GB<sup>5</sup>|10 GB<sup>5</sup>|50 GB<sup>5</sup>|500 GB<sup>4,5</sup></p>
CPU hora (Abreviação)-<sup>6</sup>|3 minutos|3 minutos|Ilimitado, pagamento padrão [taxas](https://azure.microsoft.com/pricing/details/app-service/)</a>|Ilimitado, pagamento em taxas fixas|Ilimitado, pagamento em taxas fixas
De hora (dia) CPU<sup>6</sup>|60 minutos|240 minutos|Ilimitado, pagamento padrão [taxas](https://azure.microsoft.com/pricing/details/app-service/)</a>|Ilimitado, pagamento em taxas fixas|Ilimitado, pagamento em taxas fixas
Memória (1 hora)|1024 MB pelo plano de serviço de aplicação|1024 MB por aplicação|N/D|N/D|N/D
Largura de banda|165 MB|Ilimitado, aplicar [taxas de transferência de dados](https://azure.microsoft.com/pricing/details/data-transfers/)|Ilimitado, taxas aplicáveis a transferência de dados|Ilimitado, taxas aplicáveis a transferência de dados|Ilimitado, taxas aplicáveis a transferência de dados
Arquitetura de aplicação|versão de 32 bits|versão de 32 bits|32-bit/64 bits|32-bit/64 bits|32-bit/64 bits
Web Sockets por instância<sup>7</sup>|5|35|350|Ilimitado|Ilimitado
Em simultâneo [ligações depurador](../articles/app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md) por aplicação|1|1|1|5|5
[subdomínio azurewebsites.NET com FTP/S e SSL](../articles/app-service-web/web-sites-configure-ssl-certificate.md)|X|X|X|X|X
Suporte de [domínio personalizado](../articles/app-service-web/web-sites-custom-domain-name.md)||X|X|X|X
[Suporte de SSL](../articles/app-service-web/web-sites-configure-ssl-certificate.md) de domínio personalizado|||Ilimitado|Ilimitado, 5 SNI SSL e 1 as ligações IP SSL incluído|Ilimitado, 5 SNI SSL e 1 as ligações IP SSL incluído
Balanceador de carga integrada||X|X|X|X
[Ativar sempre](../articles/app-service-web/web-sites-configure.md)|||X|X|X
[Cópias de segurança agendadas](../articles/app-service-web/web-sites-backup.md)||||Uma vez por dia|Uma vez por cada de 5 minutos<sup>8</sup>
[Dimensionar automaticamente](../articles/app-service-web/web-sites-scale.md)|||X|X|X
[WebJobs](../articles/app-service-web/web-sites-create-web-jobs.md) <sup>9</sup>|X|X|X|X|X
Suporte de [Programador do Azure](https://azure.microsoft.com/services/scheduler/)||X|X|X|X
[Monitorização de ponto final](../articles/app-service-web/web-sites-monitor.md)|||X|X|X
[Faixas de transição (pré-visualização)](../articles/app-service-web/web-sites-staged-publishing.md)||||5|20
Domínios personalizados por aplicação</a>||500|500|500|500
SLA||<p>|99,9%|99.95%<sup>10</sup>|99.95%<sup>10</sup>

<sup>1</sup> Aplicações e as quotas de armazenamento estão pelo plano de serviço de aplicação salvo indicação em contrário.  
<sup>2</sup> O número real de aplicações que pode alojar nestes máquinas depende a atividade das aplicações, o tamanho das instâncias de máquina e a utilização de recursos correspondente.  
<sup>3</sup> Podem ser dedicadas instâncias de tamanhos diferentes. Para obter mais detalhes, consulte a [Preços de serviço de aplicação](https://azure.microsoft.com/pricing/details/data-transfers/pricing/details/app-service/) .  
<sup>4</sup> Camadas de Premium permite até 50 fórmula calcula instâncias (sujeito a disponibilidade) e 500 GB de espaço em disco ao utilizar a aplicação de serviço ambientes e 20 calcular instâncias e 250 GB de armazenamento, caso contrário.  
<sup>5</sup> O limite de armazenamento é o tamanho total de conteúdo através de todas as aplicações no mesmo plano da aplicação de serviço. Mais opções de armazenamento estão disponíveis no [Ambiente de serviço de aplicação](../articles/app-service-web/app-service-web-configure-an-app-service-environment.md#storage)  
<sup>6</sup> Estes recursos são restringidos pelos recursos físicos nas instâncias dedicados (o tamanho da instância e o número de instâncias).  
<sup>7</sup> Se dimensionar uma aplicação na camada básica para duas instâncias, terá 350 ligações em simultâneo para cada uma das duas instâncias.  
<sup>8</sup> Camadas de Premium permite intervalos de cópia de segurança para baixo até em 5 minutos ao utilizar a aplicação de serviço ambientes e 50 horas por dia, caso contrário.  
<sup>9</sup> Execute executáveis personalizado e/ou scripts a pedido, com base numa agenda ou continuamente como uma tarefa de fundo dentro do seu instância do serviço de aplicação. Sempre no é necessário para contínua WebJobs execução. Azure Scheduler gratuita ou padrão é necessário para WebJobs agendada. Não existe limite predefinido no número de WebJobs que pode executar numa instância da aplicação de serviço, mas existem limites práticos que dependem o que o código da aplicação está a tentar fazer.   
<sup>10</sup> SLA de % 99.95 fornecidos para implementações que utilizam várias instâncias com o Gestor de tráfego Azure configurado para activação pós-falha.  
