<properties
    pageTitle="Resolução de problemas degradado Estado no Gestor de tráfego do Azure"
    description="Como resolver problemas de perfis do Gestor de tráfego quando for apresentada como degradado estado."
    services="traffic-manager"
    documentationCenter=""
    authors="sdwheeler"
    manager="carmonm"
    editor=""
/>
<tags
    ms.service="traffic-manager"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="infrastructure-services"
    ms.date="10/11/2016"
    ms.author="sewhee"
/>

# <a name="troubleshooting-degraded-state-on-azure-traffic-manager"></a>Resolução de problemas degradado Estado no Gestor de tráfego do Azure

Este artigo descreve como resolver problemas de um perfil do Gestor de tráfego Azure que esteja a mostrar um Estado degradado. Para este cenário, considere que tiver configurado um perfil do Gestor de tráfego apontando para alguns dos seus serviços de cloudapp.net alojado. Quando verificar o estado de funcionamento do seu Gestor de tráfego de, verá que o estado é degradado.

![Estado degradado](./media/traffic-manager-troubleshooting-degraded/traffic-manager-degraded.png)

Se aceder no separador pontos finais desse perfil, verá um ou mais dos pontos finais com um Estado Offline:

![offline](./media/traffic-manager-troubleshooting-degraded/traffic-manager-offline.png)

## <a name="understanding-traffic-manager-probes"></a>Noções sobre o Gestor de tráfego sondas

- Gestor de tráfego de considerar um ponto final para ser ONLINE apenas quando a sonda recebe uma resposta de HTTP 200 novamente a partir do caminho de sonda. Qualquer outra resposta não 200 é uma falha.
- Um redirecionamento de 30 x falha, mesmo se o URL redirecionado devolve um 200.
- Para HTTPs sondas, erros de certificados são ignorados.
- O conteúdo em si do caminho sonda é indiferente, desde que é devolvido um 200. A pesquisar um URL algumas em conteúdo estático, como "/ favicon.ico" é uma técnica comuns. Conteúdo dinâmico, como as páginas ASP, poderá não sempre devolver 200, mesmo quando a aplicação está em bom estada.
- É aconselhável definir o caminho de sonda para algo que tem o suficiente lógica para determinar que o site é para cima ou para baixo. No exemplo anterior, definindo o caminho "/ favicon.ico", só estão testes esse w3wp.exe está a responder. Este sonda não pode indicar que a aplicação web está em bom estada. A melhor opção seria definir um caminho para uma algo, tais como "/ Probe.aspx" que tenha lógica para determinar o estado de funcionamento do site. Por exemplo, poderá utilizar contadores de desempenho a utilização da CPU ou medir o número de pedidos de falhados. Ou pode tentar aceder a recursos de base de dados ou o estado da sessão para se certificar de que a aplicação web está a funcionar.
- Se todos os pontos finais de um perfil são degradados, em seguida, Gestor de tráfego tratará todos os pontos finais como saudável e encaminha o tráfego para todos os pontos finais. Este comportamento garante que os problemas com o mecanismo envolvam pesquisa não resultar numa falha de completa do seu serviço.

## <a name="troubleshooting"></a>Resolução de problemas

Para resolver problemas de uma falha de sonda, precisa de uma ferramenta que mostra o código de estado HTTP retorno do URL sonda. Existem várias ferramentas disponíveis que lhe mostram a resposta de HTTP não processada.

* [Fiddler](http://www.telerik.com/fiddler)
* [Laço](https://curl.haxx.se/)
* [wget](http://gnuwin32.sourceforge.net/packages/wget.htm)

Além disso, pode utilizar o separador rede das ferramentas de depuração de F12 no Internet Explorer para ver as respostas de HTTP.

Para este exemplo pretendemos ver a resposta do nosso URL sonda: http://watestsdp2008r2.cloudapp.net:80/sonda. O exemplo seguinte do PowerShell ilustra o problema.

```powershell
    Invoke-WebRequest 'http://watestsdp2008r2.cloudapp.net/Probe' -MaximumRedirection 0 -ErrorAction SilentlyContinue | Select-Object StatusCode,StatusDescription
```

Resultado do exemplo:

```text
    StatusCode StatusDescription
    ---------- -----------------
            301 Moved Permanently
```

Repare que recomendamos recebeu uma resposta de redirecionamento. Tal como mencionado anteriormente, qualquer StatusCode de 200 é considerada uma falha. Gestor de tráfego altera o estado de ponto final para Offline. Para resolver o problema, verifique a configuração de Web site para se certificar de que possa ser devolvido o StatusCode inicial do caminho sonda. Reconfigure a sonda de Gestor de tráfego para apontar para um caminho que devolve uma 200.

Se a sua sonda de utilizar o protocolo HTTPS, poderá ter desativar o certificado de verificação para evitar erros SSL/TLS durante o ensaio. As seguintes instruções PowerShell desativar validação do certificado para a sessão atual do PowerShell:

```powershell
    add-type @"
    using System.Net;
    using System.Security.Cryptography.X509Certificates;
    public class TrustAllCertsPolicy : ICertificatePolicy {
        public bool CheckValidationResult(
        ServicePoint srvPoint, X509Certificate certificate,
        WebRequest request, int certificateProblem) {
        return true;
        }
    }
    "@
    [System.Net.ServicePointManager]::CertificatePolicy = New-Object TrustAllCertsPolicy
```

## <a name="next-steps"></a>Próximos passos

[Acerca do Gestor de tráfego de tráfego de métodos de encaminhamento](traffic-manager-routing-methods.md)

[O que é o Gestor de tráfego](traffic-manager-overview.md)

[Serviços em nuvem](http://go.microsoft.com/fwlink/?LinkId=314074)

[Azure Web Apps](https://azure.microsoft.com/documentation/services/app-service/web/)

[Operações no Gestor de tráfego (REST API referência)](http://go.microsoft.com/fwlink/?LinkId=313584)

[Cmdlets de Gestor de tráfego Azure][1]

[1]: https://msdn.microsoft.com/library/mt125941(v=azure.200).aspx
