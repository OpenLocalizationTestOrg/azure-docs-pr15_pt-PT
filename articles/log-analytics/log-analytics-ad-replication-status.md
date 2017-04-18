<properties
    pageTitle="Solução de estado de replicação do diretório activa no registo de análise | Microsoft Azure"
    description="O pacote de solução do Estado de replicação do Active Directory regularmente monitoriza o seu ambiente do Active Directory para quaisquer falhas de replicação relatórios e os resultados no seu dashboard OMS."
    services="log-analytics"
    documentationCenter=""
    authors="bandersmsft"
    manager="jwhit"
    editor=""/>

<tags
    ms.service="log-analytics"
    ms.workload="na"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/10/2016"
    ms.author="banders"/>

# <a name="active-directory-replication-status-solution-in-log-analytics"></a>Solução de estado de replicação do diretório activa no registo de análise

O Active Directory é um componente da chave de uma empresa de ambiente de TI. Para assegurar que elevada disponibilidade e alto desempenho, cada controlador de domínio tem a sua própria cópia da base de dados do Active Directory. Controladores de domínio criar uma réplica com os outros para poder propagar alterações em toda a empresa. Falhas neste processo de replicação podem causar uma variedade de problemas em toda a empresa.

O pacote de solução do Estado de replicação do AD regularmente monitoriza o seu ambiente do Active Directory para quaisquer falhas de replicação relatórios e os resultados no seu dashboard OMS.

## <a name="installing-and-configuring-the-solution"></a>Instalar e configurar a solução
Utilize as seguintes informações para instalar e configurar a solução.

- Agentes tem de estar instalados no controladores de domínio que são membros do domínio de ser avaliada ou em servidores de membro configurados para enviar dados de replicação do AD para OMS. Para compreender como ligar computadores com o Windows para OMS, consulte o artigo [computadores com o Windows ligar-se para a análise de registo](log-analytics-windows-agents.md). Se o controlador de domínio já fizer parte de um ambiente existente do Gestor de operações do Centro de sistema que gostaria de ligar a OMS, consulte o artigo [Ligar o Gestor de operações para a análise de registo](log-analytics-om-agents.md).
- Adicione a solução de estado de replicação do Active Directory para a área de trabalho OMS utilizando o processo descrito na [soluções de adicionar o registo de análise a partir da Galeria de soluções](log-analytics-add-solutions.md).  Não existe nenhuma configuração mais obrigatória.


## <a name="ad-replication-status-data-collection-details"></a>Detalhes de recolha de dados do AD o estado de replicação

A tabela seguinte mostra os métodos de recolha de dados e outros detalhes sobre como os dados são recolhidos para o estado de replicação AD.

| plataforma | Agente de direta | Agente SCOM | Armazenamento Azure | SCOM necessário? | Dados de agente SCOM enviados por grupo de gestão | frequência de coleções de sites |
|---|---|---|---|---|---|---|
|Windows|![Sim](./media/log-analytics-ad-replication-status/oms-bullet-green.png)|![Sim](./media/log-analytics-ad-replication-status/oms-bullet-green.png)|![N](./media/log-analytics-ad-replication-status/oms-bullet-red.png)|![N](./media/log-analytics-ad-replication-status/oms-bullet-red.png)|![Sim](./media/log-analytics-ad-replication-status/oms-bullet-green.png)| cada 5 dias|


## <a name="optionally-enable-a-non-domain-controller-to-send-ad-data-to-oms"></a>Opcionalmente, ative a um controlador de domínio não enviar dados de AD para OMS
Se não pretender ligar qualquer um dos controladores de domínio diretamente a OMS, pode utilizar qualquer computador ligado OMS no seu domínio para recolher dados para o pacote de solução do Estado de replicação do AD e mantê-la envia os dados.

### <a name="to-enable-a-non-domain-controller-to-send-ad-data-to-oms"></a>Para ativar um controlador de domínio não enviar dados de AD para OMS
1.  Verifique se o computador é um membro do domínio que pretender monitorizar utilizando a solução de estado de replicação do AD.
2.  [Ligar o computador do Windows para OMS](log-analytics-windows-agents.md) ou [ligá-lo a utilizar o seu ambiente do Gestor de operações existente OMS](log-analytics-om-agents.md), se não estiver já ligado.
3.  Nesse computador, defina a seguinte chave de registo:
    - Chave: **HKEY_LOCAL_MACHINE\SYSTEM\CurrentControlSet\Services\HealthService\Parameters\Management grupos\<ManagementGroupName > \Solutions\ADReplication**
    - Valor: **IsTarge**
    - Dados do valor: **Verdadeiro**

    >[AZURE.NOTE]Estas alterações não terão efeito até ao seu reiniciar o serviço do Microsoft Agent monitorização (HealthService.exe).

## <a name="understanding-replication-errors"></a>Noções sobre erros de replicação
Assim que tiver dados de estado de replicação de AD enviados para OMS, verá um mosaico semelhante ao seguinte no dashboard de OMS indicar quantos erros de replicação que possui atualmente.  
![Mosaico do Estado de replicação de AD](./media/log-analytics-ad-replication-status/oms-ad-replication-tile.png)

**Erros de replicação críticos** são aqueles que estão a iguais ou superiores 75% da [duração de desactivar](https://technet.microsoft.com/library/cc784932%28v=ws.10%29.aspx) para floresta do Active Directory.

Quando clica no mosaico, verá a obter mais informações sobre os erros.
![Dashboard do Estado de replicação de AD](./media/log-analytics-ad-replication-status/oms-ad-replication-dash.png)


### <a name="destination-server-status-and-source-server-status"></a>Estado do servidor de destino e o estado de servidor de origem
Estes pás mostram o estado dos servidores de destino e os servidores de origem que ocorrerem erros de replicação. O número após cada nome do controlador de domínio indica o número de erros de replicação no controlador de domínio.

Os erros para os servidores de origem e os servidores de destino são apresentados porque alguns problemas são mais fáceis de resolução de problemas da perspetiva do servidor de origem e de outros da perspetiva do servidor de destino.

Neste exemplo, pode ver que muitos servidores de destino aproximadamente tem o mesmo número de erros, mas existe um servidor de origem (ADDC35) que tem muitas mais erros que todos os outros. É provável que existe algum problema ADDC35 que está a causar-o para a enviar dados para parceiros de replicação falhar. Corrigir os problemas no ADDC35 provavelmente resolverá muitos dos erros que aparecem no pá de servidor de destino.

### <a name="replication-error-types"></a>Tipos de erro de replicação
Este pá dá-lhe informações sobre os tipos de erros de detetado ao longo da sua empresa. Cada erro tem um código numérico exclusivo e uma mensagem que pode ajudar a determinar a causa de raiz do erro.

Anel na parte superior dá-lhe uma ideia de quais os erros de aparecem mais e com menos frequência no seu ambiente.

Isto pode mostrar-lhe quando o mesmo erro de replicação uma experiência de vários controladores de domínio. Neste caso, poderá descobrir identificar uma solução no controlador de um domínio, em seguida, repita-lo noutros controladores de domínio afetadas pelo mesmo erro.

### <a name="tombstone-lifetime"></a>Desactivar durante a vida.
O tempo de vida desactivar determina quanto tempo um objeto eliminado, designado de um desactivar, são guardados na base de dados do Active Directory. Quando um objeto eliminado passa o tempo de vida desactivar, um processo de recolha de lixo remove-a partir da base de dados do Active Directory.

A duração de desactivar predefinida é 180 dias para versões mais recentes do Windows, mas estava 60 dias em versões mais antigas e podem ser alterada explicitamente por um administrador do Active Directory.

É importante saber se está a ter erros de replicação que estão a aproximação ou já passaram o tempo de vida desactivar. Se um erro de replicação persistir anteriores a duração de desactivar uma experiência de dois controladores de domínio, replicação estarão desativada entre os dois controladores de domínio, mesmo se o erro de replicação subjacente é fixo.

O pá desactivar duração ajuda-o a identificar locais onde este é perigo de acontece. Cada erro na **mais de 100% TLS** categoria representa uma partição que não foi replicada entre o seu servidor de origem e de destino para, pelo menos, o tempo de vida desactivar para a floresta.

Esta situação, basta corrigir o erro de replicação não serão suficiente. No mínimo, terá de manualmente investigar para identificar e de limpeza objetos persistentes antes de podem reiniciar a replicação. Ainda poderá ter de encerrar um controlador de domínio.

Para além de identificar erros replicação que tenham persistentes passado o tempo de vida desactivar, que irá também pretende pagar a atenção para quaisquer erros abrangidos pelas categorias **TLS 50 75%** ou **75 100% TLS** .

Estes são os erros que estão claramente persistentes, não breves, por isso que necessitam provavelmente a intervenção para resolver. As boas notícias são que eles ainda não atingiram a duração de desactivar. Se corrigir estes problemas concorda e *antes de* que estes atingirem a duração da desactivar, pode reiniciar o replicação com o mínimo de intervenção manual.

Tal como indicado anteriormente, o mosaico de dashboard para a solução de estado de replicação do AD mostra o número de *crítica* erros de replicação no seu ambiente, que é definido como erros que são mais de 75% de vida de desactivar (incluindo erros que se encontram superior a 100% de TLS). Esforça-se manter este número 0.

>[AZURE.NOTE] Todos os desactivar duração percentagem cálculos são baseados na duração real desactivar para floresta do Active Directory, para que o utilizador pode confiar que essas percentagens estão corretas, mesmo que tenha um valor de duração personalizados desactivar definido.

### <a name="ad-replication-status-details"></a>Detalhes de estado de replicação de AD
Quando clica em qualquer item de uma das listas, irá ver detalhes adicionais sobre a mesma utilizando a pesquisa de registo. Os resultados são filtrados para mostrar apenas os erros relacionados com a esse item. Por exemplo, se clicar no primeiro controlador de domínio listado em **Estado de servidor de destino (ADDC02)**, verá os resultados de pesquisa filtrados para mostrar erros com esse controlador de domínio listado como o servidor de destino:

![Erros de estado de replicação de AD nos resultados da pesquisa](./media/log-analytics-ad-replication-status/oms-ad-replication-search-details.png)

A partir daqui, pode filtrar ainda mais, modificar a consulta de pesquisa e assim sucessivamente. Para obter mais informações sobre como utilizar o registo de pesquisa, consulte [pesquisas de registo](log-analytics-log-searches.md).

O campo **HelpLink** mostra o URL de uma página da TechNet com detalhes adicionais sobre esse erro específico. Pode copiar e colar esta ligação a janela do browser para ver informações sobre resolução de problemas e corrigir o erro.

Também pode clicar em **Exportar** para exportar os resultados para o Excel. Esta opção permite-lhe visualizar dados de erro de replicação de qualquer forma que pretende utilizar.

![erros de estado do AD replicação exportados no Excel](./media/log-analytics-ad-replication-status/oms-ad-replication-export.png)

## <a name="ad-replication-status-faq"></a>Perguntas mais frequentes sobre o estado de replicação AD
**P: com que frequência são atualizados de dados do AD replicação de estado?**
R: a informação é atualizado a cada 5 dias.

**P: existe uma forma para configurar a frequência estes dados são atualizados?**
R: não neste momento.

**P: é necessário para adicionar todos os meus controladores de domínio com o meu workspace OMS para poder ver o estado de replicação?**
R: não, tem de ser adicionado apenas um único controlador de domínio. Se tiver vários controladores de domínio na área de trabalho OMS, os dados a partir de todos os são enviados para OMS.

**P: não quero adicionar quaisquer controladores de domínio com o meu workspace OMS. Posso continuar a utilizar a solução de estado de replicação do AD?**
R: Sim. Pode definir o valor de uma chave de registo para ativar esta. Consulte o artigo [Ativar um controlador de no domínio que não sejam para enviar dados de AD para OMS](#to-enable-a-non-domain-controller-to-send-ad-data-to-oms).

**P: qual é o nome do processo que é que a recolha de dados?**
R: AdvisorAssessment.exe

**P: quanto tempo demora para os dados para serem recolhidos?**
R: tempo de recolha de dados de depende do tamanho do ambiente do Active Directory, mas normalmente demora menos de 15 minutos.

**P: o que tipo de dados é recolhida?**
R: informações de replicação são recolhidas através do LDAP.

**P: existe uma forma de configurar quando os dados recolhidos?**
R: não neste momento.

**P: que permissões preciso de ter recolher dados?**
R: permissões de utilizador normal do Active Directory são normalmente suficientes.

## <a name="troubleshoot-data-collection-problems"></a>Resolução de problemas de recolha de dados
Para recolher dados, o pacote de solução do Estado de replicação do AD requer pelo menos um controlador de domínio de estar ligado à área de trabalho OMS. Até fazer isto, verá uma mensagem a indicar que **ainda estão a ser recolhidos dados**.

Se precisar de assistência ligar-se um dos controladores de domínio, pode ver documentação em [computadores com o Windows ligar-se para a análise de registo](log-analytics-windows-agents.md). Em alternativa, se o controlador de domínio já estiver ligado a um ambiente existente do Gestor de operações do Centro de sistema, pode ver documentação na [Ligar Centro de operações de Gestor de sistema para a análise de registo](log-analytics-om-agents.md).

Se não pretender ligar qualquer um dos controladores de domínio diretamente a OMS ou a SCOM, consulte o artigo [Ativar um controlador de no domínio que não sejam para enviar dados de AD para OMS](#to-enable-a-non-domain-controller-to-send-ad-data-to-oms).


## <a name="next-steps"></a>Próximos passos

- Utilize [pesquisas de registo no registo Analytics](log-analytics-log-searches.md) para ver dados detalhados de estado de replicação do Active Directory.
