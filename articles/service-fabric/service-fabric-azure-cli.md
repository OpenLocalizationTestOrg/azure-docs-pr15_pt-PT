<properties
   pageTitle="Interagir com clusters de serviço ferro utilizando clip | Microsoft Azure"
   description="Como utilizar o clip do Azure para interagir com um cluster de ferro de serviço"
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/24/2016"
   ms.author="subramar"/>


# <a name="using-the-azure-cli-to-interact-with-a-service-fabric-cluster"></a>Utilizar o clip do Azure para interagir com um Cluster de ferro de serviço

Pode interagir com cluster de serviço ferro a partir do máquinas Linux utilizando o clip do Azure no Linux.

O primeiro passo é obter a versão mais recente do clip a partir do representante de git e configure-o no seu caminho utilizando os seguintes comandos:

```sh
 git clone https://github.com/Azure/azure-xplat-cli.git
 cd azure-xplat-cli
 npm install
 sudo ln -s \$(pwd)/bin/azure /usr/bin/azure
 azure servicefabric
```

Para cada comando, suporta, pode escrever o nome do comando para obter ajuda para esse comando. A conclusão automática é suportada para os comandos. Por exemplo, o comando seguinte dá-lhe ajuda para todos os comandos da aplicação. 

```sh
 azure servicefabric application 
```

Pode filtrar ainda mais a ajuda para um comando específico, como mostra o exemplo seguinte:

```sh
 azure servicefabric application  create
```

Para ativar a conclusão automática no clip, execute os seguintes comandos:

```sh
azure --completion >> ~/azure.completion.sh
echo 'source ~/azure.completion.sh' >> ~/.sh\_profile
source ~/azure.completion.sh
```

Os seguintes comandos ligar ao cluster e mostram os nós no cluster:

```sh
 azure servicefabric cluster connect http://localhost:19080
 azure servicefabric node show
```

Para utilizar parâmetros com nome e para localizar o que são, pode escrever – ajude depois de um comando. Por exemplo:

```sh
 azure servicefabric node show --help
 azure servicefabric application create --help
```

Quando a ligar a um cluster de múltiplos máquina a partir de uma máquina **seja não faz parte do cluster**, utilize o seguinte comando:

```sh
 azure servicefabric cluster connect http://PublicIPorFQDN:19080
```

Substitua a etiqueta PublicIPorFQDN real IP ou FQDN conforme adequado. Quando a ligar a um cluster de múltiplos máquina a partir de uma máquina **que faz parte do cluster**, utilize o seguinte comando:

```sh
 azure servicefabric cluster connect --connection-endpoint http://localhost:19080 --client-connection-endpoint PublicIPorFQDN:19000
```

Pode utilizar o PowerShell ou clip para interagir com o serviço de Linux ferro Cluster criada através do portal do Azure. 

**Atenção:** Estes clusters não estiverem seguros, portanto, poderá estar a abrir a caixa de um ao adicionar o endereço IP público no manifesto cluster.



## <a name="using-the-azure-cli-to-connect-to-a-service-fabric-cluster"></a>Utilizar o clip do Azure para ligar a um Cluster de ferro de serviço

Os seguintes comandos Azure clip descrevem como ligar a um cluster seguro. Os detalhes do certificado tem de corresponder um certificado em nós de cluster.

```
azure servicefabric cluster connect --connection-endpoint http://ip:19080 --client-key-path /tmp/key --client-cert-path /tmp/cert
```
 
Se o seu certificado tiver as autoridades de certificação (AC), terá de adicionar o parâmetro – AC-orientação do diapositivo notas-path semelhante ao exemplo seguinte: 

```
 azure servicefabric cluster connect --connection-endpoint http://ip:19080 --client-key-path /tmp/key --client-cert-path /tmp/cert --ca-cert-path /tmp/ca1,/tmp/ca2 
```
Se tiver várias AC, utilize uma vírgula como o delimitador.
 
Se o seu nome comuns no certificado não corresponder o ponto final de ligação, pode utilizar o parâmetro `--strict-ssl` ignorar a verificação conforme o seguinte comando: 

```
azure servicefabric cluster connect --connection-endpoint http://ip:19080 --client-key-path /tmp/key --client-cert-path /tmp/cert --strict-ssl false 
```
 
Se gostaria ignorar a verificação de AC, é possível adicionar – não autorizado rejeitar parâmetro conforme apresentado no seguinte comando: 

```
azure servicefabric cluster connect --connection-endpoint http://ip:19080 --client-key-path /tmp/key --client-cert-path /tmp/cert --reject-unauthorized false 
```
 
Depois de ligar, deverá conseguir executar outros comandos clip para interagir com o cluster. 

## <a name="deploying-your-service-fabric-application"></a>Implementar a aplicação de serviço ferro

Execute os seguintes comandos para copiar, registe-se e iniciar a aplicação de ferro de serviço:

```
azure servicefabric application package copy [applicationPackagePath] [imageStoreConnectionString] [applicationPathInImageStore]
azure servicefabric application type register [applicationPathinImageStore]
azure servicefabric application create [applicationName] [applicationTypeName] [applicationTypeVersion]
```


## <a name="upgrading-your-application"></a>Atualizar a aplicação

O processo é semelhante ao [processo no Windows](service-fabric-application-upgrade-tutorial-powershell.md)).

Criar, copiar, registe-se e criar uma aplicação a partir do diretório de raiz do projeto. Se a instância da aplicação se chamar ferro: / MySFApp e o tipo é MySFApp, os comandos seria da seguinte forma:

```
 azure servicefabric cluster connect http://localhost:19080
 azure servicefabric application package copy MySFApp fabric:ImageStore
 azure servicefabric application type register MySFApp
 azure servicefabric application create fabric:/MySFApp MySFApp 1.0
```

Efetue as alterações à aplicação e reconstruirá o serviço modificado.  Atualize manifesto seu ficheiro o serviço modificados (ServiceManifest.xml) com as versões atualizadas para o serviço (e código ou Config ou dados conforme adequado). Também modificar manifesto a aplicação (ApplicationManifest.xml) com o número de versão actualizada para a aplicação e o serviço modificado.  Agora, copie e registar a sua aplicação actualizada utilizando os comandos seguintes:

```
 azure servicefabric cluster connect http://localhost:19080>
 azure servicefabric application package copy MySFApp fabric:ImageStore
 azure servicefabric application type register MySFApp
```

Agora, pode começar a atualização da aplicação com o seguinte comando:

```
 azure servicefabric application upgrade start -–application-name fabric:/MySFApp -–target-application-type-version 2.0  --rolling-upgrade-mode UnmonitoredAuto
```

Agora pode monitorizar a atualização da aplicação utilizando SFX. No alguns minutos, a aplicação seria foram atualizada.  Também pode experimentar uma aplicação atualizada com um erro e verifique a funcionalidade de Reversão automática no ferro de serviço.

## <a name="troubleshooting"></a>Resolução de problemas

### <a name="copying-of-the-application-package-does-not-succeed"></a>Copiar do pacote de aplicação não tiver êxito

Verificar se `openssh` está instalado. Por predefinição, Ubuntu ambiente de trabalho não tem instalada. Instalá-lo utilizando o seguinte comando:

```
 sudo apt-get install openssh-server openssh-client**
```

Se o problema persistir, experimente desativar PAM para ssh alterando o ficheiro de **sshd_config** utilizando os seguintes comandos:

```sh
 sudo vi /etc/ssh/sshd_config
#Change the line with UsePAM to the following: UsePAM no
 sudo service sshd reload
```

Se o problema persistir, experimente aumentar o número de ssh sessões executando os seguintes comandos:

```sh
 sudo vi /etc/ssh/sshd\_config
# Add the following to lines:
# MaxSessions 500
# MaxStartups 300:30:500
 sudo service sshd reload
```
Utilizar teclas para ssh autenticação (por oposição a palavras-passe) não é ainda suportada (uma vez que a plataforma utiliza ssh para copiar pacotes), por isso, como alternativa, utilize a autenticação de palavra-passe.


## <a name="next-steps"></a>Próximos passos

Configurar o ambiente de desenvolvimento e implementar uma aplicação de serviço ferro a um cluster de Linux.
