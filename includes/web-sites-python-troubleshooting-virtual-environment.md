O script de implementação irá ignorar a criação do ambiente virtual no Azure se detetar que já existe um ambiente virtual compatível.  Isto pode acelerar implementação consideravelmente.  Pacotes que já estejam instalados serão ignorados pela pip.

Em determinadas situações, poderá querer forçar eliminar essa ambiente virtual.  Deverá fazê-lo se optar por incluir um ambiente virtual como parte do seu repositório.  Também poderá fazê-lo se precisar de acidentalmente determinados pacotes ou teste alterações para requirements.txt.

Existem algumas opções para gerir o ambiente virtual existente no Azure:

### <a name="option-1-use-ftp"></a>Opção 1: Utilizar FTP

Com um cliente de FTP, ligar ao servidor e poderá eliminar a pasta de envelope.  Tenha em atenção que alguns clientes FTP (por exemplo, os browsers) podem ser só de leitura e não permitem-lhe eliminar pastas, pelo que pretende Certifique-se utilizar um cliente de FTP com essa funcionalidade.  O nome de anfitrião FTP e o utilizador são apresentados no pá da sua aplicação web no [Portal do Azure](https://portal.azure.com).

### <a name="option-2-toggle-runtime"></a>Opção 2: Botão de alternar runtime

Eis uma alternativa que tira partido do facto de que o script de implementação irá eliminar a pasta de envelope quando-não corresponde à versão pretendida do Python.  Eficazmente Isto irá eliminar o ambiente existente e criar um novo.

1. Mudar para uma versão diferente do Python (através do runtime.txt ou pá as **Definições da aplicação** no Portal do Azure)
1. Git transmitir algumas alterações (ignorar algum erro de instalação pip se existirem)
1. Mude novamente para a versão inicial da Python
1. Git transmitir algumas alterações novamente

### <a name="option-3-customize-deployment-script"></a>Opção 3: Personalizar o script de implementação

Se tiver personalizado o script de implementação, pode alterar o código na deploy.cmd para forçá-la para eliminar a pasta de envelope.
