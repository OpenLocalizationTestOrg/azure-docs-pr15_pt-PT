<properties
   pageTitle="As causas mais comuns das funções de serviço em nuvem Reciclagem | Microsoft Azure"
   description="Uma função de serviço de nuvem inesperadamente Reciclagens da pode causar o tempo de inatividade significativo. Aqui estão alguns problemas comuns que causam funções de administrador a ser reciclados, que poderão ajudá-lo a reduzir o tempo de inatividade."
   services="cloud-services"
   documentationCenter=""
   authors="simonxjx"
   manager="felixwu"
   editor=""
   tags="top-support-issue"/>
<tags
   ms.service="cloud-services"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="tbd"
   ms.date="09/02/2016"
   ms.author="v-six" />

# <a name="common-issues-that-cause-roles-to-recycle"></a>Problemas comuns que causam funções de administrador a Reciclagem

Este artigo aborda algumas das causas comuns dos problemas de implementação e fornece sugestões de resolução de problemas para o ajudar a resolver estes problemas. Uma indicação de que existe um problema com uma aplicação é quando a instância da função não inicia ou -ciclos de entre os Estados a inicialização, ocupados e parar.

[AZURE.INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="missing-runtime-dependencies"></a>Dependências de runtime em falta

Se uma função na sua aplicação baseia-se em qualquer assemblagem que não faz parte do .NET Framework ou o Azure gerido biblioteca, tem de incluir explicitamente essa assemblagem no pacote de aplicação. Tenha em atenção que outros quadros Microsoft não estão disponíveis no Azure por predefinição. Se a sua função se baseia nesse quadro, tem de adicionar aos conjuntos para o pacote de aplicação.

Antes de criar e a aplicação do pacote, verifique o seguinte:

- Se utilizar o Visual studio, certifique-se que a propriedade **Cópia Local** está definida como **Verdadeiro** para cada assemblagem referenciada no projeto que não faz parte de SDK do Azure ou o .NET Framework.

- Certifique-se de que o ficheiro da Web. config não faz referência a qualquer assemblagem não utilizadas no elemento de compilação.

- A **Ação Criar** de todos os ficheiros de .cshtml está definido para **conteúdo**. Isto assegura que os ficheiros irão aparecer corretamente no pacote e permite que outros ficheiros referenciados apareça no pacote.

## <a name="assembly-targets-wrong-platform"></a>Plataforma do assemblagem destinos errada

Azure é um ambiente de 64 bits. Por conseguinte, não irá funcionar assemblagem .NET compilada para um alvo de 32 bits no Azure.

## <a name="role-throws-unhandled-exceptions-while-initializing-or-stopping"></a>Função inicia exceções não processadas enquanto a iniciar ou parar a

Quaisquer exceções que são iniciadas pelos métodos da classe de [RoleEntryPoint] , que inclui o [OnStart], [OnStop]e métodos de [Executar] , são exceções não processadas. Caso ocorra uma exceção não processada dos seguintes métodos, a função irá Reciclagem. Se a função é Reciclagem repetidamente, poderá deitar uma exceção não processada cada vez que tenta iniciar.

## <a name="role-returns-from-run-method"></a>Devolve a função partir método Run

O método [Executar] destina-se a ser executada indefinidamente. Se o seu código substitui o método [Executar] ,-deve suspensão indefinidamente. Se o método [Executar] devolve, a função Reciclagens da.

## <a name="incorrect-diagnosticsconnectionstring-setting"></a>Definição de DiagnosticsConnectionString incorrecta

Se a aplicação utiliza diagnósticos do Azure, ficheiro de configuração de serviço tem de especificar o `DiagnosticsConnectionString` definição de configuração. Esta definição deve especificar uma ligação HTTPS à sua conta de armazenamento no Azure.

Para se certificar de que seu `DiagnosticsConnectionString` definição está correcta antes de implementar o pacote de aplicações para o Azure, verifique o seguinte:  

- O `DiagnosticsConnectionString` definição pontos para uma conta de armazenamento válida no Azure.  
  Por predefinição, esta definição aponta para a conta de armazenamento emulada, para que explicitamente tem de alterar esta definição antes de implementar o pacote de aplicações. Se não alterar esta definição, uma exceção é devolvida quando a instância de função tenta iniciar o monitor de diagnóstico. Isto pode causar a instância de função a Reciclagem indefinidamente.

- A cadeia de ligação é especificada no seguinte [formato](../storage/storage-configure-connection-string.md). (O protocolo tem de ser especificado como HTTPS.) Substitua o nome da sua conta de armazenamento e *MyAccountKey* com a sua chave de acesso *MyAccountName* :    

        DefaultEndpointsProtocol=https;AccountName=MyAccountName;AccountKey=MyAccountKey

  Se estiver a desenvolver a aplicação utilizando ferramentas de Azure para o Microsoft Visual Studio, pode utilizar as [páginas de propriedades](https://msdn.microsoft.com/library/ee405486) para definir este valor.

## <a name="exported-certificate-does-not-include-private-key"></a>Certificado exportado não inclui chave privada

Para executar uma função de web em SSL, tem de garantir que o seu certificado de gestão exportado inclui a chave privada. Se utilizar o *Gestor de certificados do Windows* para exportar o certificado, certifique-se de que selecione **Sim** para a opção **exportar a chave privada** . O certificado tem de ser exportado no formato PFX, que é o único formato suportado atualmente.

## <a name="next-steps"></a>Próximos passos

Ver mais [artigos de resolução de problemas](https://azure.microsoft.com/documentation/articles/?tag=top-support-issue&product=cloud-services) para a serviços em nuvem.

Ver mais função Reciclagem cenários na [série de blogues do Kevin Williamson](http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).

[RoleEntryPoint]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.aspx
[OnStart]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.onstart.aspx
[OnStop]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.onstop.aspx
[Executar]: https://msdn.microsoft.com/library/microsoft.windowsazure.serviceruntime.roleentrypoint.run.aspx
