<properties 
   pageTitle="Resolver problemas de um dispositivo StorSimple implementado | Microsoft Azure"
   description="Descreve como diagnosticar e corrigir erros que ocorrem num dispositivo StorSimple que está a ser implementada e operacional."
   services="storsimple"
   documentationCenter="NA"
   authors="SharS"
   manager="carmonm"
   editor="" />
<tags 
   ms.service="storsimple"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="TBD"
   ms.date="05/16/2016"
   ms.author="v-sharos" />

# <a name="troubleshoot-an-operational-storsimple-device"></a>Resolver problemas de um dispositivo de StorSimple operacionais avançado

## <a name="overview"></a>Descrição geral

Este artigo fornece úteis orientações resolução de problemas para resolver problemas de configuração de que se poderá deparar depois do seu dispositivo StorSimple é implementado e operacional. Descreve problemas comuns, causas possíveis e passos recomendados para o ajudar a resolver problemas que se poderá deparar ao executar o Microsoft Azure StorSimple. Esta informação se aplica ao dispositivo físico StorSimple no local e o dispositivo virtual StorSimple.

No final deste artigo que pode encontrar uma lista de códigos de erro que poderá encontrar durante a operação do Microsoft Azure StorSimple, bem como os passos que pode tomar para resolver os erros. 

## <a name="setup-wizard-process-for-operational-devices"></a>Processo do Assistente de configuração para dispositivos operacionais avançados

Utilizar o Assistente de configuração ([Invocar HcsSetupWizard][1]) para verificar se a configuração do dispositivo e efetuar a ação correcção se for necessário.

Quando executa o Assistente de configuração num dispositivo anteriormente configurado e operacional, o fluxo de processo é diferente. Pode alterar as seguintes entradas:

- Endereço IP, máscara de sub-rede e gateway
- Servidor de DNS principal
- Servidor NTP principal
- Configuração do proxy web opcional

O Assistente de configuração não efetua operações relacionadas com registo de recolha e o dispositivo de palavra-passe.

## <a name="errors-that-occur-during-subsequent-runs-of-the-setup-wizard"></a>Erros que ocorrem durante execuções subsequentes do Assistente de configuração

A tabela seguinte descreve os erros que poderá encontrar ao executar o Assistente de configuração num dispositivo operacionais avançado, causas possíveis para os erros e recomendados ações para os resolver. 

| Não. | Mensagem de erro ou condição | Causas possíveis | Acção recomendada |
|:--- |:-------------------------- |:--------------- |:------------------ |
|  1  | Erro 350032: Este dispositivo já foi desativado. | Verá este erro se executar o Assistente de configuração num dispositivo que está desativado. | [Contactar o suporte do Microsoft](storsimple-contact-microsoft-support.md) para os passos seguintes. Um dispositivo desativado não pode ser colocado no serviço. Repor uma fábrica que devem ser seguido antes do dispositivo pode ser activado novamente. |
|  2  | Invocar HcsSetupWizard: ERROR_INVALID_FUNCTION (exceção de HRESULT: 0x80070001) | A actualização do servidor DNS está a falhar. Definições de DNS são definições globais e são aplicadas ao longo de todas as interfaces de rede activadas. | Activar a interface e aplique novamente as definições de DNS. Isto poderá perturbar a rede para outros ativadas interfaces de uma vez que estas definições são globais. |
|  3  | O dispositivo parece estar online no portal de serviço do Gestor de StorSimple, mas quando tenta concluir a configuração mínima e guarde a configuração, a operação irá falhar. | Durante a configuração inicial, o proxy web não foi configurado, apesar de havido um servidor proxy real no local. | Utilize o [cmdlet teste HcsmConnection] [ 2] para localizar o erro. [Contactar o suporte do Microsoft](storsimple-contact-microsoft-support.md) se não conseguir corrigir o problema. |
|  4  | Invocar HcsSetupWizard: Valor não de se situar dentro do intervalo esperado. | Uma máscara de sub-rede incorretos produz este erro. Causas possíveis são: <ul><li> A máscara de sub-rede está em falta ou vazio.</li><li>O formato de prefixo Ipv6 está incorreto.</li><li>A interface está ativado na nuvem, mas o gateway está em falta ou incorretos.</li></ul>Note que dados 0 são automaticamente na nuvem com permissão para se configurado o Assistente de configuração. | Para determinar o problema, utilize sub-rede 0.0.0.0 ou 256.256.256.256 e, em seguida, veja o resultado. Introduza valores corretos para a máscara de sub-rede, gateway e prefixo Ipv6, conforme necessário. |
 
## <a name="error-codes"></a>Códigos de erro

Erros estão listados por ordem numérica.

|Número do erro|Texto do erro ou a descrição|Ação do utilizador recomendadas|
|:---|:---|:---|
|10502|Foi detectado um erro ao aceder à sua conta de armazenamento.|Aguarde alguns minutos e tente novamente. Se o erro persistir, utilize a contactar o suporte do Microsoft para os passos seguintes.|
|40017|A operação de cópia de segurança falhou como um volume especificado na política de cópia de segurança não foi encontrado no dispositivo.|Repetir a cópia de segurança operação, se o erro persistir, contacte o Microsoft Support. para os passos seguintes.|
|40018|A operação de cópia de segurança falhou como dos volumes especificados na política de cópia de segurança não foi encontrado nenhum no dispositivo. |Repetir a cópia de segurança operação, se o erro persistir, contacte o Microsoft Support. para os passos seguintes.|
|390061|O sistema está ocupado ou indisponível.|Aguarde alguns minutos e tente novamente. Se o erro persistir, utilize a contactar o suporte do Microsoft para os passos seguintes.|
|390143|Ocorreu um erro com código de erro 390143. (Erro desconhecido).|Se o erro persistir, contacte o Microsoft Support para os passos seguintes.|

## <a name="next-steps"></a>Próximos passos

Se não conseguir resolver o problema, [contacte o suporte da Microsoft](storsimple-contact-microsoft-support.md) para obter assistência. 


[1]: https://technet.microsoft.com/en-us/%5Clibrary/Dn688135(v=WPS.630).aspx
[2]: https://technet.microsoft.com/en-us/%5Clibrary/Dn715782(v=WPS.630).aspx
