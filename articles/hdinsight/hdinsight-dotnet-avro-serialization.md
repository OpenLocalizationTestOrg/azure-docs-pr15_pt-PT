<properties
    pageTitle="Serializar dados com o Microsoft Avro Library | Microsoft Azure"
    description="Saiba como o Azure HDInsight utiliza Avro para serializar dados grandes."
    services="hdinsight"
    documentationCenter=""
    tags="azure-portal"
    authors="mumian" 
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/14/2016"
    ms.author="jgao"/>


# <a name="serialize-data-in-hadoop-with-the-microsoft-avro-library"></a>Serializar dados Hadoop com o Microsoft Avro Library

Este tópico mostra como utilizar a <a href="https://hadoopsdk.codeplex.com/wikipage?title=Avro%20Library" target="_blank">Biblioteca do Microsoft Avro</a> para serializar objetos e outras estruturas de dados para sequências para poder persistirem-los para memória, uma base de dados ou um ficheiro e também como serialização-los para recuperar os objetos originais.

[AZURE.INCLUDE [windows-only](../../includes/hdinsight-windows-only.md)]

##<a name="apache-avro"></a>Apache Avro
A <a href="https://hadoopsdk.codeplex.com/wikipage?title=Avro%20Library" target="_blank">Biblioteca do Microsoft Avro</a> implementa o sistema de serialização Apache Avro dados para o ambiente Microsoft.NET. Apache Avro fornece um formato de intercâmbio de dados binários compacta para serialização. Utiliza <a href="http://www.json.org" target="_blank">JSON</a> para definir um esquema de idioma desconhecido que cubra interoperabilidade de idioma. Dados serializados de um idioma podem ser lidos num outro. Atualmente C, C++, c#, Java, PHP, Python e Rubi são suportadas. Obter informações detalhadas sobre o formato podem encontrar na <a href="http://avro.apache.org/docs/current/spec.html" target="_blank">Especificação de Avro Apache</a>. Tenha em atenção que a versão atual do Microsoft Avro Library não suporta a parte de chamadas (chamadas de procedimento remoto) de procedimento remoto desta especificação.

A representação de série de um objeto no sistema de Avro consiste em duas partes: esquema e o valor real. Esquema de Avro descreve o modelo de dados de idioma independente dos dados da série com JSON. É apresentar lado a lado com uma representação binária de dados. Está a ter o esquema separado da representação binária permite que cada objeto a ser escrito com transparências sem valor-, tornando serialização rápida e a representação pequenas.

##<a name="the-hadoop-scenario"></a>O cenário Hadoop
O formato de serialização Apache Avro amplamente é utilizado em outros ambientes de Apache Hadoop e Azure HDInsight. Avro fornece uma forma conveniente para representar estruturas de dados complexas dentro de uma tarefa de Hadoop MapReduce. O formato de ficheiros de Avro (ficheiro de contentor de objeto Avro) foi concebido para suportar o modelo de programação MapReduce distribuído. A funcionalidade de chave que permite a distribuição é que os ficheiros são "divisíveis" no sentido em que um atingir qualquer ponto num ficheiro e comece a ler a partir de um determinado bloco.

##<a name="serialization-in-avro-library"></a>Serialização numa biblioteca de Avro
A biblioteca de .NET para Avro suporta duas formas de objetos serialização:

- **reflexo** - esquema de JSON para os tipos de é automaticamente criado a partir dos dados contrato atributos dos tipos de .NET para ser serializado.
- **registo genérico** - esquema A JSON for explicitamente especificada num registo de representada pela classe de [**AvroRecord**](http://msdn.microsoft.com/library/microsoft.hadoop.avro.avrorecord.aspx) quando não existem tipos de .NET estão presentes para descrever o esquema para os dados a ser serializado.

Quando o esquema de dados é conhecido ao sénior e leitor da sequência, os dados podem ser enviados sem seu esquema. Em casos quando for utilizado um ficheiro de contentor de objeto Avro, o esquema é armazenado dentro do ficheiro. Outros parâmetros, tal como o codec utilizado para compressão de dados, podem ser especificados. Estes cenários são destacados mais detalhadamente e ilustrados nos exemplos de código abaixo.


## <a name="install-avro-library"></a>Instalar Avro biblioteca

Seguem-se necessários antes de instalar a biblioteca:

- <a href="http://www.microsoft.com/download/details.aspx?id=17851" target="_blank">Microsoft .NET Framework 4</a>
- <a href="http://james.newtonking.com/json" target="_blank">Newtonsoft Json.NET</a> (6.0.4 ou posterior)

Tenha em atenção que a dependência de Newtonsoft.Json.dll é transferida automaticamente com a instalação do Microsoft Avro Library. O procedimento para este é fornecido na secção seguinte.


A Microsoft Avro Library é distribuído como um pacote de NuGet que pode ser instalado a partir do Visual Studio através do seguinte procedimento:

1. Selecione o separador **projeto** -> **Gerir o NuGet pacotes...**
2. Procure "Microsoft.Hadoop.Avro" na caixa de **Pesquisa Online** .
3. Clique no botão **instalar** junto a **Biblioteca do Microsoft Azure HDInsight Avro**.

Tenha em atenção que a Newtonsoft.Json.dll (> = 6.0.4) dependência também é transferida automaticamente com o Microsoft Avro Library.

Pretende visite a <a href="https://hadoopsdk.codeplex.com/wikipage?title=Avro%20Library" target="_blank">home page do Microsoft Avro biblioteca</a> para ler as notas de lançamento atual.


O código de origem do Microsoft Avro Library está disponível na <a href="https://hadoopsdk.codeplex.com/wikipage?title=Avro%20Library" target="_blank">home page do Microsoft Avro biblioteca</a>.

##<a name="compile-schemas-using-avro-library"></a>Compilar esquemas utilizando Avro biblioteca

A Microsoft Avro Library contém um utilitário de geração de código que permite criar tipos c# automaticamente baseados no esquema JSON definido anteriormente. O utilitário de geração código não é distribuído como um executável binário, mas pode ser criado facilmente através do seguinte procedimento:

1. Transfira o ficheiro. zip com a versão mais recente HDInsight SDK do código de origem a partir do <a href="http://hadoopsdk.codeplex.com/SourceControl/latest#" target="_blank">Microsoft .NET SDK para Hadoop</a>. (Clique no ícone **Transferir** e não o separador **transferências** .)

2. Extrai o SDK HDInsight para um diretório no computador com o .NET Framework 4 instalado e ligado à Internet para transferir pacotes de NuGet dependência necessárias. Abaixo estamos irá partem do princípio de que o código de origem for extraído para C:\SDK.

3. Vá para a pasta C:\SDK\src\Microsoft.Hadoop.Avro.Tools e execute build.bat. (O ficheiro ligarem MSBuild a partir da distribuição de 32 bits do .NET Framework. Se gostaria de utilizar a versão de 64 bits, editar build.bat, seguindo os comentários dentro do ficheiro.) Certifique-se de que a compilação é efetuada com êxito. (Em alguns sistemas MSBuild pode produzir avisos. Estes avisos não afetam o utilitário desde que não existem erros compilação.)

4. O utilitário compilado está localizado no C:\SDK\Bin\Unsigned\Release\Microsoft.Hadoop.Avro.Tools.


Para se familiarizar com a sintaxe da linha de comandos, execute o seguinte comando a partir da pasta onde se encontra o utilitário de geração código:`Microsoft.Hadoop.Avro.Tools help /c:codegen`

Para testar o utilitário, pode gerar classes c# a partir de ficheiro de esquema JSON exemplo fornecido com o código de origem. Execute o seguinte comando:

    Microsoft.Hadoop.Avro.Tools codegen /i:C:\SDK\src\Microsoft.Hadoop.Avro.Tools\SampleJSON\SampleJSONSchema.avsc /o:

Isto é suposto para produzir duas c# ficheiros no diretório atual: SensorData.cs e Location.cs.

Para compreender a lógica que está a utilizar o utilitário de geração código ao converter o esquema JSON para tipos c#, consulte o artigo o ficheiro que generationverification.Feature localizado na C:\SDK\src\Microsoft.Hadoop.Avro.Tools\Doc.

Tenha em atenção que são extraídos espaços de nomes do esquema JSON, utilizando a lógica descrita no ficheiro mencionado no parágrafo anterior. Espaços de nomes extraídos de esquema de prevalecem sobre tudo o que é fornecido com o parâmetro de comandos /n na linha de comandos utility. Se pretender substituir os espaços de nomes contidos o esquema, utilize o parâmetro /nf. Por exemplo, para alterar todos os espaços de nomes do SampleJSONSchema.avsc para my.own.nspace, execute o seguinte comando:

    Microsoft.Hadoop.Avro.Tools codegen /i:C:\SDK\src\Microsoft.Hadoop.Avro.Tools\SampleJSON\SampleJSONSchema.avsc /o:. /nf:my.own.nspace

## <a name="samples"></a>Amostras
Seis exemplos fornecidos neste tópico ilustram cenários diferentes suportados pelo Microsoft Avro Library. A Microsoft Avro Library foi concebido para funcionar com qualquer corrente. Nestes exemplos, são manipulação dados através de fluxos de memória em vez de em sequência de ficheiros ou bases de dados para simplificar e consistência. A abordagem tomada num ambiente de produção irá dependem os requisitos de cenário exata, origem de dados e volume, as restrições de desempenho e outros fatores.

Os dois primeiros exemplos mostram como serializar e serialização dados em intervalos de tempo de memória da cadeia com reflexo e registos genéricos. O esquema em ambos os casos, é considerado a ser partilhado entre os leitores e escritores fora de banda.

O terceiro e o quarto exemplos mostram como serializar e serialização dados ao utilizar os ficheiros de contentor de objeto Avro. Quando os dados são armazenados num ficheiro Avro contentor, a esquema é armazenado sempre com o mesmo porque o esquema tem de ser partilhado para desserialização.

O exemplo que contém os primeiros quatro exemplos pode ser transferido a partir do site <a href="http://code.msdn.microsoft.com/windowsazure/Serialize-data-with-the-86055923" target="_blank">exemplos de código Azure</a> .

Mostra o exemplo quinto como como utilizar um codec de compressão personalizado para ficheiros de contentor Avro objeto. Um exemplo que contém o código para este exemplo pode ser transferido a partir do site <a href="http://code.msdn.microsoft.com/windowsazure/Serialize-data-with-the-67159111" target="_blank">exemplos de código Azure</a> .

O exemplo sexto mostra como utilizar Avro serialização para carregar dados para o armazenamento de Blobs do Azure e, em seguida, analisá-los ao utilizar ramo com um cluster de HDInsight (Hadoop). Podem ser transferido a partir do site <a href="https://code.msdn.microsoft.com/windowsazure/Using-Avro-to-upload-data-ae81b1e3" target="_blank">exemplos de código Azure</a> .

Seguem-se ligações para os exemplos de seis abordados no tópico:

 * <a href="#Scenario1">**Serialização com reflexo**</a> - esquema JSON de tipos de ser serializado é automaticamente criado a partir dos dados contrato atributos.
 * <a href="#Scenario2">**Serialização com registo genérico**</a> - esquema JSON o for explicitamente especificada num registo de quando nenhum tipo de .NET está disponível para reflexo.
 * <a href="#Scenario3">**Serialização utilizar ficheiros de contentor de objecto com reflexo**</a> - esquema JSON o é automaticamente criadas e partilhadas com a série de dados através de um ficheiro de contentor de objeto Avro.
 * <a href="#Scenario4">**Serialização utilizar ficheiros de contentor de objecto com registo genérico**</a> - esquema JSON o for explicitamente especificado antes da serialização e partilhada juntamente com os dados através de um ficheiro de contentor de objeto Avro.
 * <a href="#Scenario5">**Serialização utilizar ficheiros de contentor de objecto com um codec de compressão personalizado**</a> - o exemplo que mostra como criar um ficheiro de contentor de objeto Avro com uma implementação .NET personalizada do codec Deflate dados compressão.
 * <a href="#Scenario6">**Utilizar Avro para carregar os dados para o serviço do Microsoft Azure HDInsight**</a> - o exemplo ilustra como Avro serialização interage com o serviço de HDInsight. Uma subscrição ativa do Azure e acesso a um cluster de Azure HDInsight são necessários para executar este exemplo.

###<a name="Scenario1"></a>Exemplo 1: Serialização com reflexo

Esquema de JSON para os tipos de pode ser automaticamente criado pelo Microsoft Avro Library através de reflexo dos dados de contrato atributos de objetos c# para ser serializado. A Microsoft Avro Library cria uma [**IAvroSeralizer<T> **](http://msdn.microsoft.com/library/dn627341.aspx) para identificar os campos a ser serializado.

Neste exemplo, objetos (uma classe **SensorData** com uma estrutura de **localização** de membro) são serializados para uma sequência de memória e esta sequência é serialização sucessivamente. O resultado é, em seguida, comparado com a instância inicial para confirmar que o objeto **SensorData** recuperado é idêntico ao original.

Esquema de neste exemplo é considerado a ser partilhado entre os leitores e escritores, para que o formato de contentor de objeto Avro não é necessário. Para obter um exemplo de como serializar e serialização dados em intervalos de tempo de memória utilizando reflexo com o formato de contentor de objeto quando o esquema tem de ser partilhado com os dados, consulte o artigo <a href="#Scenario3">serialização utilizar ficheiros de contentor de objecto com reflexo</a>.

    namespace Microsoft.Hadoop.Avro.Sample
    {
        using System;
        using System.Collections.Generic;
        using System.IO;
        using System.Linq;
        using System.Runtime.Serialization;
        using Microsoft.Hadoop.Avro.Container;
        using Microsoft.Hadoop.Avro;

        //Sample class used in serialization samples
        [DataContract(Name = "SensorDataValue", Namespace = "Sensors")]
        internal class SensorData
        {
            [DataMember(Name = "Location")]
            public Location Position { get; set; }

            [DataMember(Name = "Value")]
            public byte[] Value { get; set; }
        }

        //Sample struct used in serialization samples
        [DataContract]
        internal struct Location
        {
            [DataMember]
            public int Floor { get; set; }

            [DataMember]
            public int Room { get; set; }
        }

        //This class contains all methods demonstrating
        //the usage of Microsoft Avro Library
        public class AvroSample
        {

            //Serialize and deserialize sample data set represented as an object using reflection.
            //No explicit schema definition is required - schema of serialized objects is automatically built.
            public void SerializeDeserializeObjectUsingReflection()
            {

                Console.WriteLine("SERIALIZATION USING REFLECTION\n");
                Console.WriteLine("Serializing Sample Data Set...");

                //Create a new AvroSerializer instance and specify a custom serialization strategy AvroDataContractResolver
                //for serializing only properties attributed with DataContract/DateMember
                var avroSerializer = AvroSerializer.Create<SensorData>();

                //Create a memory stream buffer
                using (var buffer = new MemoryStream())
                {
                    //Create a data set by using sample class and struct
                    var expected = new SensorData { Value = new byte[] { 1, 2, 3, 4, 5 }, Position = new Location { Room = 243, Floor = 1 } };

                    //Serialize the data to the specified stream
                    avroSerializer.Serialize(buffer, expected);


                    Console.WriteLine("Deserializing Sample Data Set...");

                    //Prepare the stream for deserializing the data
                    buffer.Seek(0, SeekOrigin.Begin);

                    //Deserialize data from the stream and cast it to the same type used for serialization
                    var actual = avroSerializer.Deserialize(buffer);

                    Console.WriteLine("Comparing Initial and Deserialized Data Sets...");

                    //Finally, verify that deserialized data matches the original one
                    bool isEqual = this.Equal(expected, actual);

                    Console.WriteLine("Result of Data Set Identity Comparison is {0}", isEqual);

                }
            }

            //
            //Helper methods
            //

            //Comparing two SensorData objects
            private bool Equal(SensorData left, SensorData right)
            {
                return left.Position.Equals(right.Position) && left.Value.SequenceEqual(right.Value);
            }



            static void Main()
            {

                string sectionDivider = "---------------------------------------- ";

                //Create an instance of AvroSample Class and invoke methods
                //illustrating different serializing approaches
                AvroSample Sample = new AvroSample();

                //Serialization to memory using reflection
                Sample.SerializeDeserializeObjectUsingReflection();

                Console.WriteLine(sectionDivider);
                Console.WriteLine("Press any key to exit.");
                Console.Read();
            }
        }
    }
    // The example is expected to display the following output:
    // SERIALIZATION USING REFLECTION
    //
    // Serializing Sample Data Set...
    // Deserializing Sample Data Set...
    // Comparing Initial and Deserialized Data Sets...
    // Result of Data Set Identity Comparison is True
    // ----------------------------------------
    // Press any key to exit.


###<a name="sample-2-serialization-with-a-generic-record"></a>Exemplo 2: Serialização com um registo genérico

Um esquema JSON pode ser explicitamente especificado num registo de genérico quando reflexo não pode ser utilizado, uma vez que os dados não podem ser representados através de classes .NET com um contrato de dados. Este método é geralmente mais lento que com reflexo. Nestes casos, o esquema para os dados podem também ser dinâmicos, ou seja, não ser conhecida no momento da compilação. Representado como ficheiros de valores separados por vírgulas (CSV) cujo esquema é desconhecida até que está a ser transformado para o formato de Avro em tempo de execução de dados são um exemplo desta ordenação de cenário dinâmico.

Este exemplo mostra como criar e utilizar um [**AvroRecord**](http://msdn.microsoft.com/library/microsoft.hadoop.avro.avrorecord.aspx) para especificar um esquema JSON explicitamente, como preencha-a com os dados e, em seguida, como serializar e serialização-lo. O resultado é, em seguida, comparado com a instância inicial para confirmar que o registo recuperado é idêntico ao original.

Esquema de neste exemplo é considerado a ser partilhado entre os leitores e escritores, para que o formato de contentor de objeto Avro não é necessário. Para obter um exemplo de como serializar e serialização dados em intervalos de tempo de memória utilizando um registo genérico com o formato de contentor de objeto quando o esquema deve ser incluído com os dados da série, consulte o exemplo <a href="#Scenario4">serialização utilizar ficheiros de contentor de objecto com registo genérico</a> .


    namespace Microsoft.Hadoop.Avro.Sample
    {
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Linq;
    using System.Runtime.Serialization;
    using Microsoft.Hadoop.Avro.Container;
    using Microsoft.Hadoop.Avro.Schema;
    using Microsoft.Hadoop.Avro;

    //This class contains all methods demonstrating
    //the usage of Microsoft Avro Library
    public class AvroSample
    {

        //Serialize and deserialize sample data set by using a generic record.
        //A generic record is a special class with the schema explicitly defined in JSON.
        //All serialized data should be mapped to the fields of the generic record,
        //which in turn will be then serialized.
        public void SerializeDeserializeObjectUsingGenericRecords()
        {
            Console.WriteLine("SERIALIZATION USING GENERIC RECORD\n");
            Console.WriteLine("Defining the Schema and creating Sample Data Set...");

            //Define the schema in JSON
            const string Schema = @"{
                                ""type"":""record"",
                                ""name"":""Microsoft.Hadoop.Avro.Specifications.SensorData"",
                                ""fields"":
                                    [
                                        {
                                            ""name"":""Location"",
                                            ""type"":
                                                {
                                                    ""type"":""record"",
                                                    ""name"":""Microsoft.Hadoop.Avro.Specifications.Location"",
                                                    ""fields"":
                                                        [
                                                            { ""name"":""Floor"", ""type"":""int"" },
                                                            { ""name"":""Room"", ""type"":""int"" }
                                                        ]
                                                }
                                        },
                                        { ""name"":""Value"", ""type"":""bytes"" }
                                    ]
                            }";

            //Create a generic serializer based on the schema
            var serializer = AvroSerializer.CreateGeneric(Schema);
            var rootSchema = serializer.WriterSchema as RecordSchema;

            //Create a memory stream buffer
            using (var stream = new MemoryStream())
            {
                //Create a generic record to represent the data
                dynamic location = new AvroRecord(rootSchema.GetField("Location").TypeSchema);
                location.Floor = 1;
                location.Room = 243;

                dynamic expected = new AvroRecord(serializer.WriterSchema);
                expected.Location = location;
                expected.Value = new byte[] { 1, 2, 3, 4, 5 };

                Console.WriteLine("Serializing Sample Data Set...");

                //Serialize the data
                serializer.Serialize(stream, expected);

                stream.Seek(0, SeekOrigin.Begin);

                Console.WriteLine("Deserializing Sample Data Set...");

                //Deserialize the data into a generic record
                dynamic actual = serializer.Deserialize(stream);

                Console.WriteLine("Comparing Initial and Deserialized Data Sets...");

                //Finally, verify the results
                bool isEqual = expected.Location.Floor.Equals(actual.Location.Floor);
                isEqual = isEqual && expected.Location.Room.Equals(actual.Location.Room);
                isEqual = isEqual && ((byte[])expected.Value).SequenceEqual((byte[])actual.Value);
                Console.WriteLine("Result of Data Set Identity Comparison is {0}", isEqual);
            }
        }

        static void Main()
        {

            string sectionDivider = "---------------------------------------- ";

            //Create an instance of AvroSample class and invoke methods
            //illustrating different serializing approaches
            AvroSample Sample = new AvroSample();

            //Serialization to memory using generic record
            Sample.SerializeDeserializeObjectUsingGenericRecords();

            Console.WriteLine(sectionDivider);
            Console.WriteLine("Press any key to exit.");
            Console.Read();
        }
    }
    }
    // The example is expected to display the following output:
    // SERIALIZATION USING GENERIC RECORD
    //
    // Defining the Schema and creating Sample Data Set...
    // Serializing Sample Data Set...
    // Deserializing Sample Data Set...
    // Comparing Initial and Deserialized Data Sets...
    // Result of Data Set Identity Comparison is True
    // ----------------------------------------
    // Press any key to exit.


###<a name="sample-3-serialization-using-object-container-files-and-serialization-with-reflection"></a>Exemplo 3: Serialização utilizando os ficheiros de contentor de objeto e serialização com reflexo

Este exemplo é semelhante ao cenário no <a href="#Scenario1">primeiro exemplo</a>, onde o esquema implicitamente é especificado com reflexo. A diferença é que aqui, o esquema não é assumido como conhecidos para o leitor de deserializes-lo. Os objetos **SensorData** para ser serializado e os respetivos esquema implicitamente especificado são armazenados num ficheiro de contentor de objeto Avro representado pela classe de [**AvroContainer**](http://msdn.microsoft.com/library/microsoft.hadoop.avro.container.avrocontainer.aspx) .

Os dados estão serializados neste exemplo com [**SequentialWriter<SensorData> **](http://msdn.microsoft.com/library/dn627340.aspx) e com [**SequentialReader<SensorData>**](http://msdn.microsoft.com/library/dn627340.aspx). O resultado, em seguida, é comparado com as instâncias iniciais para se certificar de identidade.

Os dados no ficheiro de contentor de objeto estão comprimidos através da predefinição [**Deflate**] [ deflate-100] codec de compressão a partir do .NET Framework 4. Veja o <a href="#Scenario5">exemplo quinto</a> neste tópico para saber como utilizar uma versão mais recente e superior da [**Deflate**] [ deflate-110] codec de compressão disponível no .NET Framework 4,5.

    namespace Microsoft.Hadoop.Avro.Sample
    {
        using System;
        using System.Collections.Generic;
        using System.IO;
        using System.Linq;
        using System.Runtime.Serialization;
        using Microsoft.Hadoop.Avro.Container;
        using Microsoft.Hadoop.Avro;

        //Sample class used in serialization samples
        [DataContract(Name = "SensorDataValue", Namespace = "Sensors")]
        internal class SensorData
        {
            [DataMember(Name = "Location")]
            public Location Position { get; set; }

            [DataMember(Name = "Value")]
            public byte[] Value { get; set; }
        }

        //Sample struct used in serialization samples
        [DataContract]
        internal struct Location
        {
            [DataMember]
            public int Floor { get; set; }

            [DataMember]
            public int Room { get; set; }
        }

        //This class contains all methods demonstrating
        //the usage of Microsoft Avro Library
        public class AvroSample
        {

            //Serializes and deserializes the sample data set by using reflection and Avro object container files.
            //Serialized data is compressed with the Deflate codec.
            public void SerializeDeserializeUsingObjectContainersReflection()
            {

                Console.WriteLine("SERIALIZATION USING REFLECTION AND AVRO OBJECT CONTAINER FILES\n");

                //Path for Avro object container file
                string path = "AvroSampleReflectionDeflate.avro";

                //Create a data set by using sample class and struct
                var testData = new List<SensorData>
                        {
                            new SensorData { Value = new byte[] { 1, 2, 3, 4, 5 }, Position = new Location { Room = 243, Floor = 1 } },
                            new SensorData { Value = new byte[] { 6, 7, 8, 9 }, Position = new Location { Room = 244, Floor = 1 } }
                        };

                //Serializing and saving data to file.
                //Creating a memory stream buffer.
                using (var buffer = new MemoryStream())
                {
                    Console.WriteLine("Serializing Sample Data Set...");

                    //Create a SequentialWriter instance for type SensorData, which can serialize a sequence of SensorData objects to stream.
                    //Data will be compressed using the Deflate codec.
                    using (var w = AvroContainer.CreateWriter<SensorData>(buffer, Codec.Deflate))
                    {
                        using (var writer = new SequentialWriter<SensorData>(w, 24))
                        {
                            // Serialize the data to stream by using the sequential writer
                            testData.ForEach(writer.Write);
                        }
                    }

                    //Save stream to file
                    Console.WriteLine("Saving serialized data to file...");
                    if (!WriteFile(buffer, path))
                    {
                        Console.WriteLine("Error during file operation. Quitting method");
                        return;
                    }
                }

                //Reading and deserializing data.
                //Creating a memory stream buffer.
                using (var buffer = new MemoryStream())
                {
                    Console.WriteLine("Reading data from file...");

                    //Reading data from object container file
                    if (!ReadFile(buffer, path))
                    {
                        Console.WriteLine("Error during file operation. Quitting method");
                        return;
                    }

                    Console.WriteLine("Deserializing Sample Data Set...");

                    //Prepare the stream for deserializing the data
                    buffer.Seek(0, SeekOrigin.Begin);

                    //Create a SequentialReader instance for type SensorData, which will deserialize all serialized objects from the given stream.
                    //It allows iterating over the deserialized objects because it implements the IEnumerable<T> interface.
                    using (var reader = new SequentialReader<SensorData>(
                        AvroContainer.CreateReader<SensorData>(buffer, true)))
                    {
                        var results = reader.Objects;

                        //Finally, verify that deserialized data matches the original one
                        Console.WriteLine("Comparing Initial and Deserialized Data Sets...");
                        int count = 1;
                        var pairs = testData.Zip(results, (serialized, deserialized) => new { expected = serialized, actual = deserialized });
                        foreach (var pair in pairs)
                        {
                            bool isEqual = this.Equal(pair.expected, pair.actual);
                            Console.WriteLine("For Pair {0} result of Data Set Identity Comparison is {1}", count, isEqual);
                            count++;
                        }
                    }
                }

                //Delete the file
                RemoveFile(path);
            }

            //
            //Helper methods
            //

            //Comparing two SensorData objects
            private bool Equal(SensorData left, SensorData right)
            {
                return left.Position.Equals(right.Position) && left.Value.SequenceEqual(right.Value);
            }

            //Saving memory stream to a new file with the given path
            private bool WriteFile(MemoryStream InputStream, string path)
            {
                if (!File.Exists(path))
                {
                    try
                    {
                        using (FileStream fs = File.Create(path))
                        {
                            InputStream.Seek(0, SeekOrigin.Begin);
                            InputStream.CopyTo(fs);
                        }
                        return true;
                    }
                    catch (Exception e)
                    {
                        Console.WriteLine("The following exception was thrown during creation and writing to the file \"{0}\"", path);
                        Console.WriteLine(e.Message);
                        return false;
                    }
                }
                else
                {
                    Console.WriteLine("Can not create file \"{0}\". File already exists", path);
                    return false;

                }
            }

            //Reading a file content by using the given path to a memory stream
            private bool ReadFile(MemoryStream OutputStream, string path)
            {
                try
                {
                    using (FileStream fs = File.Open(path, FileMode.Open))
                    {
                        fs.CopyTo(OutputStream);
                    }
                    return true;
                }
                catch (Exception e)
                {
                    Console.WriteLine("The following exception was thrown during reading from the file \"{0}\"", path);
                    Console.WriteLine(e.Message);
                    return false;
                }
            }

            //Deleting file by using given path
            private void RemoveFile(string path)
            {
                if (File.Exists(path))
                {
                    try
                    {
                        File.Delete(path);
                    }
                    catch (Exception e)
                    {
                        Console.WriteLine("The following exception was thrown during deleting the file \"{0}\"", path);
                        Console.WriteLine(e.Message);
                    }
                }
                else
                {
                    Console.WriteLine("Can not delete file \"{0}\". File does not exist", path);
                }
            }

            static void Main()
            {

                string sectionDivider = "---------------------------------------- ";

                //Create an instance of AvroSample class and invoke methods
                //illustrating different serializing approaches
                AvroSample Sample = new AvroSample();

                //Serialization using reflection to Avro object container file
                Sample.SerializeDeserializeUsingObjectContainersReflection();

                Console.WriteLine(sectionDivider);
                Console.WriteLine("Press any key to exit.");
                Console.Read();
            }
        }
    }
    // The example is expected to display the following output:
    // SERIALIZATION USING REFLECTION AND AVRO OBJECT CONTAINER FILES
    //
    // Serializing Sample Data Set...
    // Saving serialized data to file...
    // Reading data from file...
    // Deserializing Sample Data Set...
    // Comparing Initial and Deserialized Data Sets...
    // For Pair 1 result of Data Set Identity Comparison is True
    // For Pair 2 result of Data Set Identity Comparison is True
    // ----------------------------------------
    // Press any key to exit.


###<a name="sample-4-serialization-using-object-container-files-and-serialization-with-generic-record"></a>Exemplo 4: Serialização utilizando os ficheiros de contentor de objeto e serialização com registo genérico

Este exemplo é semelhante ao cenário no <a href="#Scenario2">segundo exemplo</a>, onde o esquema for explicitamente especificado com JSON. A diferença é que aqui, o esquema não é assumido como conhecidos para o leitor de deserializes-lo.

O conjunto de dados de teste é recolhido para uma lista de objetos [**AvroRecord**](http://msdn.microsoft.com/library/microsoft.hadoop.avro.avrorecord.aspx) através de um esquema JSON definido explicitamente e, em seguida, guardado num ficheiro de contentor de objeto representado pela classe de [**AvroContainer**](http://msdn.microsoft.com/library/microsoft.hadoop.avro.container.avrocontainer.aspx) . Este ficheiro contentor cria um gravador de que é utilizado para serializar os dados, não comprimidos, para uma sequência de memória, em seguida, guardado para um ficheiro. O parâmetro de [**Codec.Null**](http://msdn.microsoft.com/library/microsoft.hadoop.avro.container.codec.null.aspx) utilizado para criar o leitor de Especifica que estes dados não serão comprimidos.

Os dados, em seguida, são ler a partir de ficheiro e anular a serialização para uma coleção de objetos. Nesta coleção de é comparado com a lista de registos de Avro para confirmar que são idênticos inicial.


    namespace Microsoft.Hadoop.Avro.Sample
    {
        using System;
        using System.Collections.Generic;
        using System.IO;
        using System.Linq;
        using System.Runtime.Serialization;
        using Microsoft.Hadoop.Avro.Container;
        using Microsoft.Hadoop.Avro.Schema;
        using Microsoft.Hadoop.Avro;

        //This class contains all methods demonstrating
        //the usage of Microsoft Avro Library
        public class AvroSample
        {

            //Serializes and deserializes a sample data set by using a generic record and Avro object container files.
            //Serialized data is not compressed.
            public void SerializeDeserializeUsingObjectContainersGenericRecord()
            {
                Console.WriteLine("SERIALIZATION USING GENERIC RECORD AND AVRO OBJECT CONTAINER FILES\n");

                //Path for Avro object container file
                string path = "AvroSampleGenericRecordNullCodec.avro";

                Console.WriteLine("Defining the Schema and creating Sample Data Set...");

                //Define the schema in JSON
                const string Schema = @"{
                                ""type"":""record"",
                                ""name"":""Microsoft.Hadoop.Avro.Specifications.SensorData"",
                                ""fields"":
                                    [
                                        {
                                            ""name"":""Location"",
                                            ""type"":
                                                {
                                                    ""type"":""record"",
                                                    ""name"":""Microsoft.Hadoop.Avro.Specifications.Location"",
                                                    ""fields"":
                                                        [
                                                            { ""name"":""Floor"", ""type"":""int"" },
                                                            { ""name"":""Room"", ""type"":""int"" }
                                                        ]
                                                }
                                        },
                                        { ""name"":""Value"", ""type"":""bytes"" }
                                    ]
                            }";

                //Create a generic serializer based on the schema
                var serializer = AvroSerializer.CreateGeneric(Schema);
                var rootSchema = serializer.WriterSchema as RecordSchema;

                //Create a generic record to represent the data
                var testData = new List<AvroRecord>();

                dynamic expected1 = new AvroRecord(rootSchema);
                dynamic location1 = new AvroRecord(rootSchema.GetField("Location").TypeSchema);
                location1.Floor = 1;
                location1.Room = 243;
                expected1.Location = location1;
                expected1.Value = new byte[] { 1, 2, 3, 4, 5 };
                testData.Add(expected1);

                dynamic expected2 = new AvroRecord(rootSchema);
                dynamic location2 = new AvroRecord(rootSchema.GetField("Location").TypeSchema);
                location2.Floor = 1;
                location2.Room = 244;
                expected2.Location = location2;
                expected2.Value = new byte[] { 6, 7, 8, 9 };
                testData.Add(expected2);

                //Serializing and saving data to file.
                //Create a MemoryStream buffer.
                using (var buffer = new MemoryStream())
                {
                    Console.WriteLine("Serializing Sample Data Set...");

                    //Create a SequentialWriter instance for type SensorData, which can serialize a sequence of SensorData objects to stream.
                    //Data will not be compressed (Null compression codec).
                    using (var writer = AvroContainer.CreateGenericWriter(Schema, buffer, Codec.Null))
                    {
                        using (var streamWriter = new SequentialWriter<object>(writer, 24))
                        {
                            // Serialize the data to stream by using the sequential writer
                            testData.ForEach(streamWriter.Write);
                        }
                    }

                    Console.WriteLine("Saving serialized data to file...");

                    //Save stream to file
                    if (!WriteFile(buffer, path))
                    {
                        Console.WriteLine("Error during file operation. Quitting method");
                        return;
                    }
                }

                //Reading and deserializing the data.
                //Create a memory stream buffer.
                using (var buffer = new MemoryStream())
                {
                    Console.WriteLine("Reading data from file...");

                    //Reading data from object container file
                    if (!ReadFile(buffer, path))
                    {
                        Console.WriteLine("Error during file operation. Quitting method");
                        return;
                    }

                    Console.WriteLine("Deserializing Sample Data Set...");

                    //Prepare the stream for deserializing the data
                    buffer.Seek(0, SeekOrigin.Begin);

                    //Create a SequentialReader instance for type SensorData, which will deserialize all serialized objects from the given stream.
                    //It allows iterating over the deserialized objects because it implements the IEnumerable<T> interface.
                    using (var reader = AvroContainer.CreateGenericReader(buffer))
                    {
                        using (var streamReader = new SequentialReader<object>(reader))
                        {
                            var results = streamReader.Objects;

                            Console.WriteLine("Comparing Initial and Deserialized Data Sets...");

                            //Finally, verify the results
                            var pairs = testData.Zip(results, (serialized, deserialized) => new { expected = (dynamic)serialized, actual = (dynamic)deserialized });
                            int count = 1;
                            foreach (var pair in pairs)
                            {
                                bool isEqual = pair.expected.Location.Floor.Equals(pair.actual.Location.Floor);
                                isEqual = isEqual && pair.expected.Location.Room.Equals(pair.actual.Location.Room);
                                isEqual = isEqual && ((byte[])pair.expected.Value).SequenceEqual((byte[])pair.actual.Value);
                                Console.WriteLine("For Pair {0} result of Data Set Identity Comparison is {1}", count, isEqual.ToString());
                                count++;
                            }
                        }
                    }
                }

                //Delete the file
                RemoveFile(path);
            }

            //
            //Helper methods
            //

            //Saving memory stream to a new file with the given path
            private bool WriteFile(MemoryStream InputStream, string path)
            {
                if (!File.Exists(path))
                {
                    try
                    {
                        using (FileStream fs = File.Create(path))
                        {
                            InputStream.Seek(0, SeekOrigin.Begin);
                            InputStream.CopyTo(fs);
                        }
                        return true;
                    }
                    catch (Exception e)
                    {
                        Console.WriteLine("The following exception was thrown during creation and writing to the file \"{0}\"", path);
                        Console.WriteLine(e.Message);
                        return false;
                    }
                }
                else
                {
                    Console.WriteLine("Can not create file \"{0}\". File already exists", path);
                    return false;

                }
            }

            //Reading a file content by using the given path to a memory stream
            private bool ReadFile(MemoryStream OutputStream, string path)
            {
                try
                {
                    using (FileStream fs = File.Open(path, FileMode.Open))
                    {
                        fs.CopyTo(OutputStream);
                    }
                    return true;
                }
                catch (Exception e)
                {
                    Console.WriteLine("The following exception was thrown during reading from the file \"{0}\"", path);
                    Console.WriteLine(e.Message);
                    return false;
                }
            }

            //Deleting file by using the given path
            private void RemoveFile(string path)
            {
                if (File.Exists(path))
                {
                    try
                    {
                        File.Delete(path);
                    }
                    catch (Exception e)
                    {
                        Console.WriteLine("The following exception was thrown during deleting the file \"{0}\"", path);
                        Console.WriteLine(e.Message);
                    }
                }
                else
                {
                    Console.WriteLine("Can not delete file \"{0}\". File does not exist", path);
                }
            }

            static void Main()
            {

                string sectionDivider = "---------------------------------------- ";

                //Create an instance of the AvroSample class and invoke methods
                //illustrating different serializing approaches
                AvroSample Sample = new AvroSample();

                //Serialization using generic record to Avro object container file
                Sample.SerializeDeserializeUsingObjectContainersGenericRecord();

                Console.WriteLine(sectionDivider);
                Console.WriteLine("Press any key to exit.");
                Console.Read();
            }
        }
    }
    // The example is expected to display the following output:
    // SERIALIZATION USING GENERIC RECORD AND AVRO OBJECT CONTAINER FILES
    //
    // Defining the Schema and creating Sample Data Set...
    // Serializing Sample Data Set...
    // Saving serialized data to file...
    // Reading data from file...
    // Deserializing Sample Data Set...
    // Comparing Initial and Deserialized Data Sets...
    // For Pair 1 result of Data Set Identity Comparison is True
    // For Pair 2 result of Data Set Identity Comparison is True
    // ----------------------------------------
    // Press any key to exit.




###<a name="sample-5-serialization-using-object-container-files-with-a-custom-compression-codec"></a>Exemplo 5: Serialização utilizar ficheiros de contentor de objecto com um codec de compressão personalizado

Mostra o exemplo quinto como como utilizar um codec de compressão personalizado para ficheiros de contentor Avro objeto. Um exemplo que contém o código para este exemplo pode ser transferido a partir do site [exemplos de código Azure](http://code.msdn.microsoft.com/windowsazure/Serialize-data-with-the-67159111) .

A [Especificação de Avro](http://avro.apache.org/docs/current/spec.html#Required+Codecs) permite a utilização de um codec de compressão opcional (para além da predefinição é **Nulo** e **Deflate** ). Este exemplo não está a implementar um codec completamente novo como Snappy (mencionado como um codec opcional suportado na [Especificação de Avro](http://avro.apache.org/docs/current/spec.html#snappy)). Mostra como utilizar a aplicação do .NET Framework 4,5 da [**Deflate**] [ deflate-110] codec, que fornece um algoritmo de compressão melhor com base na biblioteca de compressão [zlib](http://zlib.net/) que a versão do .NET Framework 4 predefinido.


    //
    // This code needs to be compiled with the parameter Target Framework set as ".NET Framework 4.5"
    // to ensure the desired implementation of the Deflate compression algorithm is used.
    // Ensure your C# project is set up accordingly.
    //

    namespace Microsoft.Hadoop.Avro.Sample
    {
        using System;
        using System.Collections.Generic;
        using System.Diagnostics;
        using System.IO;
        using System.IO.Compression;
        using System.Linq;
        using System.Runtime.Serialization;
        using Microsoft.Hadoop.Avro.Container;
        using Microsoft.Hadoop.Avro;

        #region Defining objects for serialization
        //Sample class used in serialization samples
        [DataContract(Name = "SensorDataValue", Namespace = "Sensors")]
        internal class SensorData
        {
            [DataMember(Name = "Location")]
            public Location Position { get; set; }

            [DataMember(Name = "Value")]
            public byte[] Value { get; set; }
        }

        //Sample struct used in serialization samples
        [DataContract]
        internal struct Location
        {
            [DataMember]
            public int Floor { get; set; }

            [DataMember]
            public int Room { get; set; }
        }
        #endregion

        #region Defining custom codec based on .NET Framework V.4.5 Deflate
        //Avro.NET codec class contains two methods,
        //GetCompressedStreamOver(Stream uncompressed) and GetDecompressedStreamOver(Stream compressed),
        //which are the key ones for data compression.
        //To enable a custom codec, one needs to implement these methods for the required codec.

        #region Defining Compression and Decompression Streams
        //DeflateStream (class from System.IO.Compression namespace that implements Deflate algorithm)
        //cannot be directly used for Avro because it does not support vital operations like Seek.
        //Thus one needs to implement two classes inherited from stream
        //(one for compressed and one for decompressed stream)
        //that use Deflate compression and implement all required features.
        internal sealed class CompressionStreamDeflate45 : Stream
        {
            private readonly Stream buffer;
            private DeflateStream compressionStream;

            public CompressionStreamDeflate45(Stream buffer)
            {
                Debug.Assert(buffer != null, "Buffer is not allowed to be null.");

                this.compressionStream = new DeflateStream(buffer, CompressionLevel.Fastest, true);
                this.buffer = buffer;
            }

            public override bool CanRead
            {
                get { return this.buffer.CanRead; }
            }

            public override bool CanSeek
            {
                get { return true; }
            }

            public override bool CanWrite
            {
                get { return this.buffer.CanWrite; }
            }

            public override void Flush()
            {
                this.compressionStream.Close();
            }

            public override long Length
            {
                get { return this.buffer.Length; }
            }

            public override long Position
            {
                get
                {
                    return this.buffer.Position;
                }

                set
                {
                    this.buffer.Position = value;
                }
            }

            public override int Read(byte[] buffer, int offset, int count)
            {
                return this.buffer.Read(buffer, offset, count);
            }

            public override long Seek(long offset, SeekOrigin origin)
            {
                return this.buffer.Seek(offset, origin);
            }

            public override void SetLength(long value)
            {
                throw new NotSupportedException();
            }

            public override void Write(byte[] buffer, int offset, int count)
            {
                this.compressionStream.Write(buffer, offset, count);
            }

            protected override void Dispose(bool disposed)
            {
                base.Dispose(disposed);

                if (disposed)
                {
                    this.compressionStream.Dispose();
                    this.compressionStream = null;
                }
            }
        }

        internal sealed class DecompressionStreamDeflate45 : Stream
        {
            private readonly DeflateStream decompressed;

            public DecompressionStreamDeflate45(Stream compressed)
            {
                this.decompressed = new DeflateStream(compressed, CompressionMode.Decompress, true);
            }

            public override bool CanRead
            {
                get { return true; }
            }

            public override bool CanSeek
            {
                get { return true; }
            }

            public override bool CanWrite
            {
                get { return false; }
            }

            public override void Flush()
            {
                this.decompressed.Close();
            }

            public override long Length
            {
                get { return this.decompressed.Length; }
            }

            public override long Position
            {
                get
                {
                    return this.decompressed.Position;
                }

                set
                {
                    throw new NotSupportedException();
                }
            }

            public override int Read(byte[] buffer, int offset, int count)
            {
                return this.decompressed.Read(buffer, offset, count);
            }

            public override long Seek(long offset, SeekOrigin origin)
            {
                throw new NotSupportedException();
            }

            public override void SetLength(long value)
            {
                throw new NotSupportedException();
            }

            public override void Write(byte[] buffer, int offset, int count)
            {
                throw new NotSupportedException();
            }

            protected override void Dispose(bool disposing)
            {
                base.Dispose(disposing);

                if (disposing)
                {
                    this.decompressed.Dispose();
                }
            }
        }
        #endregion

        #region Define Codec
        //Define the actual codec class containing the required methods for manipulating streams:
        //GetCompressedStreamOver(Stream uncompressed) and GetDecompressedStreamOver(Stream compressed).
        //Codec class uses classes for compressed and decompressed streams defined above.
        internal sealed class DeflateCodec45 : Codec
        {

            //We merely use different IMPLEMENTATIONS of Deflate, so CodecName remains "deflate"
            public static readonly string CodecName = "deflate";

            public DeflateCodec45()
                : base(CodecName)
            {
            }

            public override Stream GetCompressedStreamOver(Stream decompressed)
            {
                if (decompressed == null)
                {
                    throw new ArgumentNullException("decompressed");
                }

                return new CompressionStreamDeflate45(decompressed);
            }

            public override Stream GetDecompressedStreamOver(Stream compressed)
            {
                if (compressed == null)
                {
                    throw new ArgumentNullException("compressed");
                }

                return new DecompressionStreamDeflate45(compressed);
            }
        }
        #endregion

        #region Define modified Codec Factory
        //Define modified codec factory to be used in the reader.
        //It will catch the attempt to use "Deflate" and provide  a custom codec.
        //For all other cases, it will rely on the base class (CodecFactory).
        internal sealed class CodecFactoryDeflate45 : CodecFactory
        {

            public override Codec Create(string codecName)
            {
                if (codecName == DeflateCodec45.CodecName)
                    return new DeflateCodec45();
                else
                    return base.Create(codecName);
            }
        }
        #endregion

        #endregion

        #region Sample Class with demonstration methods
        //This class contains methods demonstrating
        //the usage of Microsoft Avro Library
        public class AvroSample
        {

            //Serializes and deserializes sample data set by using reflection and Avro object container files.
            //Serialized data is compressed with the custom compression codec (Deflate of .NET Framework 4.5).
            //
            //This sample uses memory stream for all operations related to serialization, deserialization and
            //object container manipulation, though file stream could be easily used.
            public void SerializeDeserializeUsingObjectContainersReflectionCustomCodec()
            {

                Console.WriteLine("SERIALIZATION USING REFLECTION, AVRO OBJECT CONTAINER FILES AND CUSTOM CODEC\n");

                //Path for Avro object container file
                string path = "AvroSampleReflectionDeflate45.avro";

                //Create a data set by using sample class and struct
                var testData = new List<SensorData>
                        {
                            new SensorData { Value = new byte[] { 1, 2, 3, 4, 5 }, Position = new Location { Room = 243, Floor = 1 } },
                            new SensorData { Value = new byte[] { 6, 7, 8, 9 }, Position = new Location { Room = 244, Floor = 1 } }
                        };

                //Serializing and saving data to file.
                //Creating a memory stream buffer.
                using (var buffer = new MemoryStream())
                {
                    Console.WriteLine("Serializing Sample Data Set...");

                    //Create a SequentialWriter instance for type SensorData, which can serialize a sequence of SensorData objects to stream.
                    //Here the custom codec is introduced. For convenience, the next commented code line shows how to use built-in Deflate.
                    //Note that because the sample deals with different IMPLEMENTATIONS of Deflate, built-in and custom codecs are interchangeable
                    //in read-write operations.
                    //using (var w = AvroContainer.CreateWriter<SensorData>(buffer, Codec.Deflate))
                    using (var w = AvroContainer.CreateWriter<SensorData>(buffer, new DeflateCodec45()))
                    {
                        using (var writer = new SequentialWriter<SensorData>(w, 24))
                        {
                            // Serialize the data to stream using the sequential writer
                            testData.ForEach(writer.Write);
                        }
                    }

                    //Save stream to file
                    Console.WriteLine("Saving serialized data to file...");
                    if (!WriteFile(buffer, path))
                    {
                        Console.WriteLine("Error during file operation. Quitting method");
                        return;
                    }
                }

                //Reading and deserializing data.
                //Creating a memory stream buffer.
                using (var buffer = new MemoryStream())
                {
                    Console.WriteLine("Reading data from file...");

                    //Reading data from object container file
                    if (!ReadFile(buffer, path))
                    {
                        Console.WriteLine("Error during file operation. Quitting method");
                        return;
                    }

                    Console.WriteLine("Deserializing Sample Data Set...");

                    //Prepare the stream for deserializing the data
                    buffer.Seek(0, SeekOrigin.Begin);

                    //Because of SequentialReader<T> constructor signature, an AvroSerializerSettings instance is required
                    //when codec factory is explicitly specified.
                    //You may comment the line below if you want to use built-in Deflate (see next comment).
                    AvroSerializerSettings settings = new AvroSerializerSettings();

                    //Create a SequentialReader instance for type SensorData, which will deserialize all serialized objects from the given stream.
                    //It allows iterating over the deserialized objects because it implements the IEnumerable<T> interface.
                    //Here the custom codec factory is introduced.
                    //For convenience, the next commented code line shows how to use built-in Deflate
                    //(no explicit Codec Factory parameter is required in this case).
                    //Note that because the sample deals with different IMPLEMENTATIONS of Deflate, built-in and custom codecs are interchangeable
                    //in read-write operations.
                    //using (var reader = new SequentialReader<SensorData>(AvroContainer.CreateReader<SensorData>(buffer, true)))
                    using (var reader = new SequentialReader<SensorData>(
                        AvroContainer.CreateReader<SensorData>(buffer, true, settings, new CodecFactoryDeflate45())))
                    {
                        var results = reader.Objects;

                        //Finally, verify that deserialized data matches the original one
                        Console.WriteLine("Comparing Initial and Deserialized Data Sets...");
                        bool isEqual;
                        int count = 1;
                        var pairs = testData.Zip(results, (serialized, deserialized) => new { expected = serialized, actual = deserialized });
                        foreach (var pair in pairs)
                        {
                            isEqual = this.Equal(pair.expected, pair.actual);
                            Console.WriteLine("For Pair {0} result of Data Set Identity Comparison is {1}", count, isEqual.ToString());
                            count++;
                        }
                    }
                }

                //Delete the file
                RemoveFile(path);
            }
        #endregion

            #region Helper Methods

            //Comparing two SensorData objects
            private bool Equal(SensorData left, SensorData right)
            {
                return left.Position.Equals(right.Position) && left.Value.SequenceEqual(right.Value);
            }

            //Saving memory stream to a new file with the given path
            private bool WriteFile(MemoryStream InputStream, string path)
            {
                if (!File.Exists(path))
                {
                    try
                    {
                        using (FileStream fs = File.Create(path))
                        {
                            InputStream.Seek(0, SeekOrigin.Begin);
                            InputStream.CopyTo(fs);
                        }
                        return true;
                    }
                    catch (Exception e)
                    {
                        Console.WriteLine("The following exception was thrown during creation and writing to the file \"{0}\"", path);
                        Console.WriteLine(e.Message);
                        return false;
                    }
                }
                else
                {
                    Console.WriteLine("Can not create file \"{0}\". File already exists", path);
                    return false;

                }
            }

            //Reading file content by using the given path to a memory stream
            private bool ReadFile(MemoryStream OutputStream, string path)
            {
                try
                {
                    using (FileStream fs = File.Open(path, FileMode.Open))
                    {
                        fs.CopyTo(OutputStream);
                    }
                    return true;
                }
                catch (Exception e)
                {
                    Console.WriteLine("The following exception was thrown during reading from the file \"{0}\"", path);
                    Console.WriteLine(e.Message);
                    return false;
                }
            }

            //Deleting file by using given path
            private void RemoveFile(string path)
            {
                if (File.Exists(path))
                {
                    try
                    {
                        File.Delete(path);
                    }
                    catch (Exception e)
                    {
                        Console.WriteLine("The following exception was thrown during deleting the file \"{0}\"", path);
                        Console.WriteLine(e.Message);
                    }
                }
                else
                {
                    Console.WriteLine("Can not delete file \"{0}\". File does not exist", path);
                }
            }
            #endregion

            static void Main()
            {

                string sectionDivider = "---------------------------------------- ";

                //Create an instance of AvroSample Class and invoke methods
                //illustrating different serializing approaches
                AvroSample Sample = new AvroSample();

                //Serialization using reflection to Avro object container file using custom codec
                Sample.SerializeDeserializeUsingObjectContainersReflectionCustomCodec();

                Console.WriteLine(sectionDivider);
                Console.WriteLine("Press any key to exit.");
                Console.Read();
            }
        }
    }
    // The example is expected to display the following output:
    // SERIALIZATION USING REFLECTION, AVRO OBJECT CONTAINER FILES AND CUSTOM CODEC
    //
    // Serializing Sample Data Set...
    // Saving serialized data to file...
    // Reading data from file...
    // Deserializing Sample Data Set...
    // Comparing Initial and Deserialized Data Sets...
    // For Pair 1 result of Data Set Identity Comparison is True
    //For Pair 2 result of Data Set Identity Comparison is True
    // ----------------------------------------
    // Press any key to exit.

###<a name="sample-6-using-avro-to-upload-data-for-the-microsoft-azure-hdinsight-service"></a>Exemplo 6: Utilizar Avro para carregar os dados para o serviço do Microsoft Azure HDInsight

O exemplo sexto ilustra algumas técnicas de programação relacionados com a interagir com o serviço do Azure HDInsight. Um exemplo que contém o código para este exemplo pode ser transferido a partir do site [exemplos de código Azure](https://code.msdn.microsoft.com/windowsazure/Using-Avro-to-upload-data-ae81b1e3) .

O exemplo faz o seguinte:

* Liga a um cluster de serviço existente HDInsight.
* Serializes vários ficheiros CSV e carrega o resultado de armazenamento de Blobs do Azure. (Ficheiros CSV são distribuídos juntamente com o exemplo e representam uma extrair a partir de dados históricos AMEX cotações distribuídos por [Infochimps](http://www.infochimps.com/) para o período de 1970 2010. O exemplo lê dados do ficheiro CSV, converte os registos instâncias a classe de **cotações** de e, em seguida, serializes-las utilizando reflexo. Definição de tipo de cotações é criada a partir de um esquema JSON via o utilitário de geração de código do Microsoft Avro Library.
* Cria uma nova tabela externa denominada **unidades populacionais** na ramo e ligações carregá-lo aos dados no passo anterior.
* Executa uma consulta utilizando ramo sobre a tabela de **unidades populacionais** .

Além disso, o exemplo executa um procedimento de limpeza antes e depois de efectuar operações principais. Durante a limpeza, todos os dados relacionados de Blobs do Azure e pastas são removidos e a tabela Hive é ignorada. Também pode invocar o procedimento de limpeza da linha de comandos de exemplo.

O exemplo tem os pré-requisitos seguintes:

* Uma subscrição ativa do Microsoft Azure e o seu ID da subscrição.
* Um certificado de gestão da subscrição com a chave privada correspondente. O certificado deve ser instalado no atual utilizador privado armazenamento no computador utilizado para executar a amostra.
* Um cluster de HDInsight ativo.
* Uma conta de armazenamento do Windows Azure associada ao HDInsight cluster a partir do pré-requisito anterior, juntamente com a chave primária ou secundária acesso correspondente.

Todas as informações dos pré-requisitos devem ser introduzidas para o ficheiro de configuração de exemplo antes da amostra é executada. Existem duas formas possíveis de fazê-lo:

* Editar o ficheiro App o diretório de raiz de exemplo e, em seguida, crie a amostra
* Em primeiro lugar, crie a amostra e, em seguida, editar AvroHDISample.exe.config no diretório compilação

Em ambos os casos, todas as edições devem ser feitas **<appSettings>** secção definições. Siga os comentários no ficheiro.
A amostra é executada a partir da linha de comandos executando o seguinte comando (caso contrário, onde o ficheiro. zip com a amostra foi considerado extraídos para C:\AvroHDISample; se utilizar o caminho do ficheiro relevantes):

    AvroHDISample run C:\AvroHDISample\Data

Para limpar o cluster, execute o seguinte comando:

    AvroHDISample clean

[deflate-100]: http://msdn.microsoft.com/library/system.io.compression.deflatestream(v=vs.100).aspx
[deflate-110]: http://msdn.microsoft.com/library/system.io.compression.deflatestream(v=vs.110).aspx
