<properties 
   pageTitle="Azure Cativação Mobile guia - o serviço de resolução de problemas" 
   description="Resolução de problemas guias para o Azure Cativação móvel" 
   services="mobile-engagement" 
   documentationCenter="" 
   authors="piyushjo" 
   manager="dwrede" 
   editor=""/>

<tags
   ms.service="mobile-engagement"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="mobile-multiple"
   ms.workload="mobile" 
   ms.date="08/19/2016"
   ms.author="piyushjo"/>

# <a name="troubleshooting-guide-for-service-issues"></a>Guia de resolução de problemas do problemas com o serviço

Seguem-se possíveis problemas que poderá encontrar com a forma como o Azure Mobile Cativação é executado.

## <a name="service-outages"></a>Falhas de serviço

### <a name="issue"></a>Problema
- Problemas que aparecem para ser causado por falhas de serviço do Azure Mobile Cativação.

### <a name="causes"></a>Faz com que
- Problemas que aparecem para ser causado por falhas de serviço do Azure Mobile Cativação podem ser causados por vários problemas diferentes:
    - Questões isoladas que originalmente aparecem sistémicas a todas as Azure Mobile Cativação
    - Problemas conhecidos causados pela falhas de servidor (nem sempre é apresentado no estado do servidor):
    - Agendamento atrasos, erros de filtragem, problemas de atualização do distintivo, parar estatísticas recolher, deixa de Push funcionar, de API parar de trabalhar, novas aplicações ou os utilizadores não é possível criar, erros de DNS e erros de tempo limite na IU, API ou aplicações num dispositivo.
    - Nuvem dependência de corrente [Estado do serviço Azure](http://status.azure.com/)
    - Notificações push notificação de corrente de dependência de serviços (PNS)
    - Falhas de App Store

1) Para testar para ver se o problema é sistémico pode testar a mesma função a partir de uma diferente
   
   - Aplicação de Cativação Mobile integrada Azure
   - Dispositivo de teste
   - Versão do SO do dispositivo de teste
   - Campanha
   - Conta de utilizador do administrador
   - Browser (IE, Firefox, Chrome, etc.)
   - Computador

2) Para verificar se o problema afeta apenas a IU ou a API:

   - Teste a mesma função a partir do Azure Mobile Cativação IU e do Azure Mobile Cativação API.

3) Para verificar se o problema for com a sua rede do telemóvel:

   - Teste enquanto estiver ligado à Internet através da conta e enquanto estiver ligado através da rede de telemóvel 3g.
   - Confirme que a firewall não está a bloquear qualquer um dos endereços de IP do Azure Mobile Cativação ou portas.

4) Para verificar se o problema for com o seu dispositivo:

   - Se conseguir ligar ao Azure Mobile Cativação com outra aplicação integrada do Azure Mobile Cativação seu dispositivo de teste.
   - Teste de que pode gerar eventos, tarefas e falhas do seu telefone que pode ser visto no Azure Mobile Cativação IU. 
   - Teste se pode enviar notificações push do Azure Mobile Cativação IU ao seu dispositivo com base no seu dispositivo ID. 

5) Para verificar se o problema for com a sua aplicação:

   - Instalar e testar a aplicação a partir de um emulador em vez da partir de um dispositivo físico:
   
6) Para verificar se o problema for com actualizações SO para dispositivos, que requerem uma atualização SDK para resolver do utilizador final:

   - Teste a sua aplicação em diferentes dispositivos com diferentes versões do SO.
   - Confirme que está a utilizar a versão mais recente do SDK.
 
## <a name="connectivity-and-incorrect-information-issues"></a>Conectividade e de problemas de informações incorretas

### <a name="issue"></a>Problema
- Problemas de registo para o Azure Mobile Cativação IU.
- Erros de ligação do Azure Mobile Cativação API.
- Problemas ao carregar etiquetas de informações da aplicação através da API do dispositivo.
- Problemas durante a transferência de registos ou dados exportados a partir do Azure Mobile Cativação.
- Informações incorretas apresentadas na IU de Cativação do Azure Mobile.
- Informações incorretas apresentadas nos registos do Azure Mobile Cativação.

### <a name="causes"></a>Faz com que
* Confirme a que sua conta de utilizador tem permissões suficientes para executar a tarefa.
* Confirme que o problema não é isolado para um computador ou à sua rede local.
* Confirme que o serviço do Azure Mobile Cativação não tenha comunicado de corrente.
* Confirme que os seus ficheiros de etiqueta de informações da aplicação sigam todas estas regras:
    - Utilize apenas o conjunto de caracteres UTF8 (o conjunto de carateres ANSI não é suportado).
    - Utilize uma vírgula "," como caráter de separação (pode abrir um serviço pedido para pedido para alterar o caráter de separação. csv de um ponto e vírgula "," para outro caráter como um ponto e vírgula ";").
    - Utilize todas minúsculas para valores booleanos "true" e "false".
    - Utilize um ficheiro que for menor que o tamanho máximo do ficheiro de 35MB.
 
