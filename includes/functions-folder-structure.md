
O código para todas as funções numa aplicação determinada função encontra-se numa pasta raiz que contém um ficheiro de configuração do anfitrião e numa ou mais subpastas, cada um dos quais contêm o código para uma função separada, tal como no exemplo seguinte

```
wwwroot
 | - host.json
 | - mynodefunction
 | | - function.json
 | | - index.js
 | | - node_modules
 | | | - ... packages ...
 | | - package.json
 | - mycsharpfunction
 | | - function.json
 | | - run.csx
```

O ficheiro de *host.json* contém alguma configuração específicas do runtime e encontra-se na pasta raiz da aplicação de função. Para obter informações sobre as definições que estão disponíveis, consulte o artigo [host.json](https://github.com/Azure/azure-webjobs-sdk-script/wiki/host.json) no WebJobs.Script repositório wiki.

Cada função tem uma pasta que contém um ou mais ficheiros de código, a configuração de function.json e outras dependências.