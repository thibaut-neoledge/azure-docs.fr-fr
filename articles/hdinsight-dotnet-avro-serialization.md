<properties  linkid="hdinsight-dotnet-avro-serialization" urlDisplayName="HDInsight Avro.NET Serialization" pageTitle="Serialize Data with Avro.NET | Azure" metaKeywords="" description="Learn how Azure HDInsight uses Avro.NET to serialize big data." metaCanonical="" services="hdinsight" documentationCenter="" title="Serialize Data with Avro.NET " authors="bradsev" solutions="" manager="paulettm" editor="cgronlun" />

# Sérialisation des données avec Avro.NET

## Vue d'ensemble

Cette rubrique montre comment utiliser Microsoft Avro Library (Avro.NET) pour sérialiser les objets et les autres structures de données dans des flux d'octets afin de les conserver en mémoire, dans une base de données ou un fichier. Elle implémente le système de sérialisation des données Apache Avro à l'aide de JSON pour définir le schéma sans langage spécifié qui propose un format compact d'échange des données binaires. Ce dernier peut être traité par plusieurs langues (actuellement C, C++, C#, Java, PHP, Python et Ruby) lorsque les objets peuvent être récupérés au moyen de la désérialisation.

Le format de sérialisation Apache Avro est largement utilisé dans les environnements Hadoop, notamment Microsoft HDInsight. Vous pouvez trouver des informations détaillées sur ce format dans la Spécification Apache Avro.

Microsoft HDInsight Avro.NET Library prend en charge deux façons de sérialiser les objets : à l'aide de Reflection ou de Generic Record.

## Conditions préalables

Microsoft .NET Library for Avro est distribué en tant que package NuGet. Installez depuis Visual Studio : sélectionnez l'onglet **Projet** ->
**Gérer les packages NuGet**, recherchez << Avro >> dans la zone
**Rechercher en ligne**, puis cliquez sur le bouton **Installer** en
regard de **Microsoft .NET Library for Avro**. La dépendance Newtonsoft.Json (>= .5.0.5) est également téléchargée automatiquement avec.

### Résumé

Scénarios :

* [Sérialisation à l'aide de Reflection](#Scenario1)

* [Sérialisation à l'aide de conteneurs d'objets Avro](#Scenario2)

* [Sérialisation à l'aide de fichiers conteneurs d'objets et
  sérialisation à l'aide de Reflection](#Scenario3)

* [Sérialisation à l'aide de fichiers conteneurs d'objets et
  sérialisation à l'aide de Generic Record](#Scenario4)

* [Sérialisation à l'aide de fichiers conteneurs d'objets avec codec de
  compression personnalisé](#Scenario5)

<h2> <a name="Scenario1">Sérialisation à l'aide de Reflection </a></h2>


L'exemple ci-dessous montre une classe et une structure sérialisée, désérialisée et enfin comparée aux instances initiales afin de garantir l'identité. Microsoft Avro Library construit automatiquement le schéma JSON des types en tenant compte des attributs de contrat de données. Microsoft Avro Library utilise le [Sérialiseur de contrat de données][1] pour identifier les champs en cours de sérialisation.

    namespace Microsoft.Hadoop.Avro.Sample
    {
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Linq;
    using System.Runtime.Serialization;
    using Microsoft.Hadoop.Avro.Container;
    
    //Exemple de classe utilisée dans les exemples de sérialisation
    [DataContract(Name = "SensorDataValue", Namespace = "Sensors")]
    internal class SensorData
    {
    [DataMember(Name = "Location")]
    public Location Position { get; set; }
    
    [DataMember(Name = "Value")]
    public byte[] Value { get; set; }
    }
    
    //Exemple de structure utilisée dans les exemples de sérialisation
    [DataContract]
    internal struct Location
    {
    [DataMember]
    public int Floor { get; set; }
    
    [DataMember]
    public int Room { get; set; }
    }
    
    //Cette classe contient toutes les méthodes démontrant
    //l'utilisation de Microsoft Avro Library
    public class AvroSample
    {
    
    //Sérialiser et désérialiser l'exemple de jeu de données représenté en tant qu'objet à l'aide de Reflection
    //Aucune définition de schéma explicite n'est requise : le schéma des objets sérialisés est automatiquement construit
    public void SerializeDeserializeObjectUsingReflection()
    {
    
    Console.WriteLine("SERIALIZATION USING REFLECTION\n");
    Console.WriteLine("Serializing Sample Data Set...");
    
    //Créer une instance AvroSerializer et spécifier une stratégie de sérialisation personnalisée AvroDataContractResolver
    //pour sérialiser uniquement les propriétés attribuées avec DataContract/DateMember
    var avroSerializer = AvroSerializer.Create<SensorData>();
    
    //Créer une mémoire tampon de flux de mémoire
    using (var buffer = new MemoryStream())
    {
    //Créer un jeu de données à l'aide des exemples de classe et de structure 
    var expected = new SensorData { Value = new byte[] { 1, 2, 3, 4, 5 }, Position = new Location { Room = 243, Floor = 1 } };
    
    //Sérialiser les données vers le flux spécifié
    avroSerializer.Serialize(buffer, expected);
    
      
    Console.WriteLine("Deserializing Sample Data Set...");
    
    //Préparer le flux pour désérialiser les données
    buffer.Seek(0, SeekOrigin.Begin);
    
    //Désérialiser les données à partir du flux et les convertir dans le même type utilisé pour la sérialisation
    var actual = avroSerializer.Deserialize(buffer);
    
    Console.WriteLine("Comparing Initial and Deserialized Data Sets...");
    
    //Enfin, vérifier que les données désérialisées correspondent aux données d'origine
    bool isEqual = this.Equal(expected, actual);
    
    Console.WriteLine("Result of Data Set Identity Comparison is {0}", isEqual);
    
    }
    }
    
    //
    //Méthodes d'assistance
    //
    
    //Comparaison de deux objets SensorData
    private bool Equal(SensorData left, SensorData right)
    {
    return left.Position.Equals(right.Position) && left.Value.SequenceEqual(right.Value);
    }
    
    
      
    static void Main()
    {
    
    string sectionDivider = "---------------------------------------- ";
    
    //Créer une instance de la classe AvroSample et appeler des méthodes
    //illustrant différentes approches de sérialisation
    AvroSample Sample = new AvroSample();
    
    //Sérialisation en mémoire à l'aide de Reflection
    Sample.SerializeDeserializeObjectUsingReflection();
    
    Console.WriteLine(sectionDivider);
    Console.WriteLine("Press any key to exit.");
    Console.Read();
    }
    }
    }
    // L'exemple est censé afficher la sortie suivante : 
    // SÉRIALISATION À L'AIDE DE REFLECTION
    //
    // Sérialisation de l'exemple de jeu de données...
    // Désérialisation de l'exemple de jeu de données...
    // Comparaison des jeux de données initiaux et désérialisés...
    // Le résultat de la comparaison d'identité des jeux de données est True
    // ----------------------------------------
    // Appuyez sur n'importe quelle touche pour quitter.

<h2> <a name="Scenario2"></a>Sérialisation à l'aide de conteneurs d'objets Avro</h2>


Il n'est pas toujours possible de disposer de classes .NET pour représenter les données. Le schéma de données peut être dynamique, comme, par exemple, lorsque les données sont représentées en tant que fichiers CSV (Comma Separated Values) avec un schéma non connu à l'avance et nécessitent une transformation au format Avro. Dans de tels cas, il convient de créer une instance de la classe spéciale (AvroRecord) avec un schéma JSON explicitement spécifié, de remplir les champs avec les données requises et de sérialiser celles-ci. Cet exemple montre comment définir un schéma, créer l'enregistrement correspondant, remplir ce dernier avec les données, puis sérialiser et désérialiser. Enfin, l'identité des objets initiaux et désérialisés est comparée.

    namespace Microsoft.Hadoop.Avro.Sample
    {
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Linq;
    using System.Runtime.Serialization;
    using Microsoft.Hadoop.Avro.Container;
    using Microsoft.Hadoop.Avro.Schema;
    
    //Cette classe contient toutes les méthodes démontrant
    //l'utilisation de Microsoft Avro Library
    public class AvroSample
    {
    
    //Sérialiser et désérialiser l'exemple de jeu de données représenté à l'aide de Generic Record.
    //Generic Record est une classe spéciale dont le schéma est explicitement défini dans JSON.
    //Toutes les données sérialisées doivent être mappées sur les champs de Generic Record,
    //qui doivent être sérialisés à leur tour.
    public void SerializeDeserializeObjectUsingGenericRecords()
    {
    Console.WriteLine("SERIALIZATION USING GENERIC RECORD\n");
    Console.WriteLine("Defining the Schema and creating Sample Data Set...");
    
    //Définir le schéma dans JSON
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
    
    // Créer un sérialiseur générique fondé sur le schéma
    var serializer = AvroSerializer.CreateGeneric(Schema);
    var rootSchema = serializer.WriterSchema as RecordSchema;
    
    //Créer une mémoire tampon de flux de mémoire
    using (var stream = new MemoryStream())
    {
    //Créer un enregistrement générique pour représenter les données
    dynamic location = new AvroRecord(rootSchema.GetField("Location").TypeSchema);
    location.Floor = 1;
    location.Room = 243;
    
    dynamic expected = new AvroRecord(serializer.WriterSchema);
    expected.Location = location;
    expected.Value = new byte[] { 1, 2, 3, 4, 5 };
    
    Console.WriteLine("Serializing Sample Data Set...");
    
    //Sérialiser les données
    serializer.Serialize(stream, expected);
    
    stream.Seek(0, SeekOrigin.Begin);
    
    Console.WriteLine("Deserializing Sample Data Set...");
    
    //Désérialiser les données dans un enregistrement générique
    dynamic actual = serializer.Deserialize(stream);
    
    Console.WriteLine("Comparing Initial and Deserialized Data Sets...");
    
    //Enfin, vérifier les résultats
    bool isEqual = expected.Location.Floor.Equals(actual.Location.Floor);
    isEqual = isEqual && expected.Location.Room.Equals(actual.Location.Room);
    isEqual = isEqual && ((byte[])expected.Value).SequenceEqual((byte[])actual.Value);
    Console.WriteLine("Result of Data Set Identity Comparison is {0}" , isEqual);
    }
    }
    
    static void Main()
    {
    
    string sectionDivider = "---------------------------------------- ";
    
    //Créer une instance de la classe AvroSample et appeler des méthodes
    //illustrant différentes approches de sérialisation
    AvroSample Sample = new AvroSample();
    
    //Sérialisation en mémoire à l'aide de Generic Record
    Sample.SerializeDeserializeObjectUsingGenericRecords();
    
    Console.WriteLine(sectionDivider);
    Console.WriteLine("Press any key to exit.");
    Console.Read();
    }
    }
    }
    // L'exemple est censé afficher la sortie suivante : 
    // SÉRIALISATION À L'AIDE DE GENERIC RECORD
    //
    // Définition du schéma et création de l'exemple de jeu de données...
    // Sérialisation de l'exemple de jeu de données...
    // Désérialisation de l'exemple de jeu de données...
    // Comparaison des jeux de données initiaux et désérialisés...
    // Le résultat de la comparaison d'identité des jeux de données est True
    // ----------------------------------------
    // Appuyez sur n'importe quelle touche pour quitter.

<h2> <a name="Scenario3"></a>Sérialisation à l'aide de fichiers conteneurs d'objets et sérialisation à l'aide de Reflection</h2>


Cet exemple ressemble à l'exemple 1 : une classe et une structure en cours de sérialisation et stockées dans un fichier conteneur d'objets situé dans le répertoire actuel de l'exemple d'application. Ce fichier est ensuite lu, les données sont désérialisées et enfin comparées aux instances initiales afin de garantir l'identité. Les données du fichier conteneur d'objets sont compressées à l'aide du codec de compression Deflate (Lien ?).

    namespace Microsoft.Hadoop.Avro.Sample
    {
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Linq;
    using System.Runtime.Serialization;
    using Microsoft.Hadoop.Avro.Container;
    
    //Exemple de classe utilisée dans les exemples de sérialisation
    [DataContract(Name = "SensorDataValue", Namespace = "Sensors")]
    internal class SensorData
    {
    [DataMember(Name = "Location")]
    public Location Position { get; set; }
    
    [DataMember(Name = "Value")]
    public byte[] Value { get; set; }
    }
    
    //Exemple de structure utilisée dans les exemples de sérialisation
    [DataContract]
    internal struct Location
    {
    [DataMember]
    public int Floor { get; set; }
    
    [DataMember]
    public int Room { get; set; }
    }
    
    //Cette classe contient toutes les méthodes démontrant
    //l'utilisation de Microsoft Avro Library
    public class AvroSample
    {
    
    //Sérialise et désérialise l'exemple de jeu de données à l'aide de Reflection et des fichiers conteneurs d'objets Avro
    //Les données sérialisées sont compressées avec le codec Deflate
    public void SerializeDeserializeUsingObjectContainersReflection()
    {
    
    Console.WriteLine("SERIALIZATION USING REFLECTION AND AVRO OBJECT CONTAINER FILES\n");
    
    //Chemin d'accès du fichier conteneur d'objets Avro
    string path = "AvroSampleReflectionDeflate.avro";
    
    //Créer un jeu de données à l'aide des exemples de classe et de structure
    var testData = new List<SensorData>
    {
    new SensorData { Value = new byte[] { 1, 2, 3, 4, 5 }, Position = new Location { Room = 243, Floor = 1 } },
    new SensorData { Value = new byte[] { 6, 7, 8, 9 }, Position = new Location { Room = 244, Floor = 1 } }
    };
    
    //Sérialisation et enregistrement des données dans le fichier
    //Création d'une mémoire tampon de flux de mémoire
    using (var buffer = new MemoryStream())
    {
    Console.WriteLine("Serializing Sample Data Set...");
    
    //Créer une instance SequentialWriter pour le type de SensorData qui peut sérialiser une séquence d'objets SensorData à diffuser
    //Les données seront compressées à l'aide du codec Deflate
    using (var w = AvroContainer.CreateWriter<SensorData>(buffer, Codec.Deflate))
    {
    using (var writer = new SequentialWriter<SensorData>(w, 24))
    {
    // Sérialiser les données à diffuser à l'aide de l'enregistreur séquentiel
    testData.ForEach(writer.Write);
    }
    }
    
    //Enregistrer le flux dans le fichier
    Console.WriteLine("Saving serialized data to file...");
    if (!WriteFile(buffer, path))
    {
    Console.WriteLine("Error during file operation. Quitting method");
    return;
    }
    }
    
    //Lecture et désérialisation des données
    //Création d'une mémoire tampon de flux de mémoire
    using (var buffer = new MemoryStream())
    {
    Console.WriteLine("Reading data from file...");
    
    //Lecture de données du fichier conteneur d'objets
    if (!ReadFile(buffer, path))
    {
    Console.WriteLine("Error during file operation. Quitting method");
    return;
    }
    
    Console.WriteLine("Deserializing Sample Data Set...");
    
    //Préparer le flux pour désérialiser les données
    buffer.Seek(0, SeekOrigin.Begin);
    
    //Créer une instance SequentialReader pour le type de SensorData qui désérialise tous les objets sérialisés du flux donné
    //Elle permet l'itération sur les objets désérialisés car elle implémente l'interface IEnumerable<T>
    using (var reader = new SequentialReader<SensorData>(
    AvroContainer.CreateReader<SensorData>(buffer, true)))
    {
    var results = reader.Objects;
    
    //Enfin, vérifier que les données désérialisées correspondent aux données d'origine
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
    
    //Supprimer le fichier
    RemoveFile(path);
    }
    
    //
    //Méthodes d'assistance
    //
    
    //Comparaison de deux objets SensorData
    private bool Equal(SensorData left, SensorData right)
    {
    return left.Position.Equals(right.Position) && left.Value.SequenceEqual(right.Value);
    }
    
    //Enregistrement du flux de mémoire dans un nouveau fichier avec le chemin d'accès donné
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
    
    //Lecture du contenu d'un fichier à l'aide d'un chemin d'accès donné vers un flux de mémoire
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
    
    //Suppression du fichier à l'aide du chemin d'accès donné
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
    
    //Créer une instance de la classe AvroSample et appeler des méthodes
    //illustrant différentes approches de sérialisation
    AvroSample Sample = new AvroSample();
    
    //Sérialisation à l'aide de Reflection vers le fichier conteneur d'objets Avro
    Sample.SerializeDeserializeUsingObjectContainersReflection();
    
    Console.WriteLine(sectionDivider);
    Console.WriteLine("Press any key to exit.");
    Console.Read();
    }
    }
    }
    // L'exemple est censé afficher la sortie suivante :
    // SÉRIALISATION À L'AIDE DE REFLECTION ET DES FICHIERS CONTENEURS D'OBJETS AVRO
    //
    // Sérialisation de l'exemple de jeu de données...
    // Enregistrement des données sérialisées dans le fichier...
    // Lecture des données du fichier...
    // Désérialisation de l'exemple de jeu de données...
    // Comparaison des jeux de données initiaux et désérialisés...
    // Pour la paire 1, le résultat de la comparaison d'identité des jeux de données est True
    // Pour la paire 2, le résultat de la comparaison d'identité des jeux de données est True
    // ----------------------------------------
    // Appuyez sur n'importe quelle touche pour quitter.

<h2> <a name="Scenario4"></a>Sérialisation à l'aide de fichiers conteneurs d'objets et sérialisation à l'aide de Generic Record</h2>


Cet exemple ressemble à l'exemple 2 : un exemple de jeu de données est sérialisé à l'aide d'AvroRecord avec un schéma JSON explicitement défini. Des données sérialisées sont stockées dans un fichier conteneur d'objets situé dans le répertoire actuel de l'exemple d'application. Ce fichier est ensuite lu, les données sont désérialisées et enfin comparées aux instances initiales afin de garantir l'identité.

Les données du fichier conteneur d'objets ne sont pas compressées (un codec sans compression est utilisé).

    namespace Microsoft.Hadoop.Avro.Sample
    {
    using System;
    using System.Collections.Generic;
    using System.IO;
    using System.Linq;
    using System.Runtime.Serialization;
    using Microsoft.Hadoop.Avro.Container;
    using Microsoft.Hadoop.Avro.Schema;
    
    //Cette classe contient toutes les méthodes démontrant
    //l'utilisation de Microsoft Avro Library
    public class AvroSample
    {
    
    //Sérialise et désérialise l'exemple de jeu de données à l'aide de Generic Record et des fichiers conteneurs d'objets Avro
    //Données sérialisées non compressées
    public void SerializeDeserializeUsingObjectContainersGenericRecord()
    {
    Console.WriteLine("SERIALIZATION USING GENERIC RECORD AND AVRO OBJECT CONTAINER FILES\n");
    
    //Chemin d'accès du fichier conteneur d'objets Avro
    string path = "AvroSampleGenericRecordNullCodec.avro";
    
    Console.WriteLine("Defining the Schema and creating Sample Data Set...");
    
    //Définir le schéma dans JSON
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
    
    // Créer un sérialiseur générique fondé sur le schéma
    var serializer = AvroSerializer.CreateGeneric(Schema);
    var rootSchema = serializer.WriterSchema as RecordSchema;
    
    //Créer un enregistrement générique pour représenter les données
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
    
    //Sérialisation et enregistrement des données dans le fichier
    //Créer une mémoire tampon MemoryStream
    using (var buffer = new MemoryStream())
    {
    Console.WriteLine("Serializing Sample Data Set...");
    
    //Créer une instance SequentialWriter pour le type de SensorData qui peut sérialiser une séquence d'objets SensorData à diffuser
    //Les données seront compressées (codec sans compression)
    using (var writer = AvroContainer.CreateGenericWriter(Schema, buffer, Codec.Null))
    {
    using (var streamWriter = new SequentialWriter<object>(writer, 24))
    {
    // Sérialiser les données à diffuser à l'aide de l'enregistreur séquentiel
    testData.ForEach(streamWriter.Write);
    }
    }
    
    Console.WriteLine("Saving serialized data to file...");
    
    //Enregistrer le flux dans le fichier
    if (!WriteFile(buffer, path))
    {
    Console.WriteLine("Error during file operation. Quitting method");
    return;
    }
    }
    
    //Lecture et désérialisation des données
    //Créer une mémoire tampon de flux de mémoire
    using (var buffer = new MemoryStream())
    {
    Console.WriteLine("Reading data from file...");
    
    //Lecture de données du fichier conteneur d'objets
    if (!ReadFile(buffer, path))
    {
    Console.WriteLine("Error during file operation. Quitting method");
    return;
    }
    
    Console.WriteLine("Deserializing Sample Data Set...");
    
    //Préparer le flux pour désérialiser les données
    buffer.Seek(0, SeekOrigin.Begin);
    
    //Créer une instance SequentialReader pour le type de SensorData qui désérialise tous les objets sérialisés du flux donné
    //Elle permet l'itération sur les objets désérialisés car elle implémente l'interface IEnumerable<T>
    using (var reader = AvroContainer.CreateGenericReader(buffer))
    {
    using (var streamReader = new SequentialReader<object>(reader))
    {
    var results = streamReader.Objects;
    
    Console.WriteLine("Comparing Initial and Deserialized Data Sets...");
    
    //Enfin, vérifier les résultats
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
    
    //Supprimer le fichier
    RemoveFile(path);
    }
    
    //
    //Méthodes d'assistance
    //
    
    //Enregistrement du flux de mémoire dans un nouveau fichier avec le chemin d'accès donné
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
    
    //Lecture du contenu d'un fichier à l'aide d'un chemin d'accès donné vers un flux de mémoire
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
    
    //Suppression du fichier à l'aide du chemin d'accès donné
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
    
    //Créer une instance de la classe AvroSample et appeler des méthodes
    //illustrant différentes approches de sérialisation
    AvroSample Sample = new AvroSample();
    
    //Sérialisation à l'aide de Generic Record vers le fichier conteneur d'objets Avro
    Sample.SerializeDeserializeUsingObjectContainersGenericRecord();
    
    Console.WriteLine(sectionDivider);
    Console.WriteLine("Press any key to exit.");
    Console.Read();
    }
    }
    }
    // L'exemple est censé afficher la sortie suivante :
    // SÉRIALISATION À L'AIDE DE GENERIC RECORD ET DES FICHIERS CONTENEURS D'OBJETS AVRO
    //
    // Définition du schéma et création de l'exemple de jeu de données...
    // Sérialisation de l'exemple de jeu de données...
    // Enregistrement des données sérialisées dans le fichier...
    // Lecture des données du fichier...
    // Désérialisation de l'exemple de jeu de données...
    // Comparaison des jeux de données initiaux et désérialisés...
    // Pour la paire 1, le résultat de la comparaison d'identité des jeux de données est True
    // Pour la paire 2, le résultat de la comparaison d'identité des jeux de données est True
    // ----------------------------------------
    // Appuyez sur n'importe quelle touche pour quitter.

<h2> <a name="Scenario5"></a>Sérialisation à l'aide de fichiers conteneurs d'objets avec codec de compression personnalisé</h2>


La [Spécification Avro][2] permet d'utiliser les codecs de compression facultatifs (autres que Null et Deflate). L'exemple ci-dessous montre comment utiliser un codec de compression personnalisé pour les fichiers conteneurs d'objets. Il n'implémente pas des codecs uniformément différents (tels que Snappy, mentionné en tant que codec facultatif pris en charge dans la Spécification Avro), mais utilise l'implémentation
[Deflate][3] du .NET Framework 4.5 (offre un meilleur algorithme de compression fondé sur zlib), qui peut s'avérer utile pour bon nombre de développeurs.

    // Ce code doit être compilé avec le paramètre Framework cible défini en tant que « .NET Framework 4.5 »
    // afin que l'implémentation de l'algorithme de compression Deflate soit utilisée
    // Veillez à ce que votre projet C# soit configuré en conséquence
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

	#region Defining objects for serialization
    //Exemple de classe utilisée dans les exemples de sérialisation
    [DataContract(Name = "SensorDataValue", Namespace = "Sensors")]
    internal class SensorData
    {
    [DataMember(Name = "Location")]
    public Location Position { get; set; }
    
    [DataMember(Name = "Value")]
    public byte[] Value { get; set; }
    }
    
    //Exemple de structure utilisée dans les exemples de sérialisation
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
    //La classe Avro.NET Codec contient deux méthodes, 
    //GetCompressedStreamOver(Flux non compressé) et GetDecompressedStreamOver(Flux compressé)
    //qui sont déterminantes pour la compression de données.
    //Pour activer un codec personnalisé, il convient d'implémenter ces méthodes pour le codec requis

	#region Defining Compression and Decompression Streams
    //DeflateStream (classe de l'espace de noms System.IO.Compression qui implémente l'algorithme Deflate)
    //ne peut pas être utilisée directement pour Avro car elle ne prend pas en charge les opérations vitales telles que Seek.
    //Il convient donc d'implémenter deux classes héritées de Stream
    //(l'une pour le flux compressé et l'autre pour le flux non compressé),
    //qui utilisent la compression Deflate et implémentent toutes les fonctionnalités requises 
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
    //Définir la classe réelle du codec contenant les méthodes requises pour la manipulation des flux :
    //GetCompressedStreamOver(Flux non compressé) et GetDecompressedStreamOver(Flux compressé)
    //La classe Codec utilise des classes pour les flux compressés et décompressés définis ci-dessus
    internal sealed class DeflateCodec45 : Codec
    {
    
    //Nous utilisons simplement une IMPLÉMENTATION différente de Deflate, le CodecName reste ainsi "deflate"
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
    //Définir la fabrique de codec modifié à utiliser dans Reader
    //Elle intercepte la tentative d'utilisation de "deflate" et fournit un codec personnalisé 
    //Dans tous les autres cas, elle repose sur la classe de base (CodecFactory)
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
    //Cette classe contient des méthodes démontrant
    //l'utilisation de Microsoft Avro Library
    public class AvroSample
    {
    
    //Sérialise et désérialise l'exemple de jeu de données à l'aide de Reflection et des fichiers conteneurs d'objets Avro
    //Les données sérialisées sont compressées avec le codec de compression Custom (Deflate de .NET Framework 4.5)
    //
    //Cet exemple utilise Memory Stream pour toutes les opérations liées à la sérialisation, la désérialisation et
    //la manipulation de conteneurs d'objets, même si File Stream peut facilement être utilisé.
    public void SerializeDeserializeUsingObjectContainersReflectionCustomCodec()
    {
    
    Console.WriteLine("SERIALIZATION USING REFLECTION, AVRO OBJECT CONTAINER FILES AND CUSTOM CODEC\n");
    
    //Chemin d'accès du fichier conteneur d'objets Avro
    string path = "AvroSampleReflectionDeflate45.avro";
    
    //Créer un jeu de données à l'aide des exemples de classe et de structure
    var testData = new List<SensorData>
    {
    new SensorData { Value = new byte[] { 1, 2, 3, 4, 5 }, Position = new Location { Room = 243, Floor = 1 } },
    new SensorData { Value = new byte[] { 6, 7, 8, 9 }, Position = new Location { Room = 244, Floor = 1 } }
    };
    
    //Sérialisation et enregistrement des données dans le fichier
    //Création d'une mémoire tampon de flux de mémoire
    using (var buffer = new MemoryStream())
    {
    Console.WriteLine("Serializing Sample Data Set...");
    
    //Créer une instance SequentialWriter pour le type de SensorData qui peut sérialiser une séquence d'objets SensorData à diffuser
    //Le codec personnalisé est présenté ici. Par commodité, la ligne de code commentée suivante montre comment utiliser Deflate intégré.
    //Notez que les codecs intégrés et personnalisés sont interchangeables dans les opérations de lecture-écriture dans la mesure où l'exemple traite de différentes
    //IMPLÉMENTATIONS de Deflate
    //utilisant (var w = AvroContainer.CreateWriter<SensorData>(buffer, Codec.Deflate))
    using (var w = AvroContainer.CreateWriter<SensorData>(buffer, new DeflateCodec45()))
    {
    using (var writer = new SequentialWriter<SensorData>(w, 24))
    {
    // Sérialiser les données à diffuser à l'aide de l'enregistreur séquentiel
    testData.ForEach(writer.Write);
    }
    }
    
    //Enregistrer le flux dans le fichier
    Console.WriteLine("Saving serialized data to file...");
    if (!WriteFile(buffer, path))
    {
    Console.WriteLine("Error during file operation. Quitting method");
    return;
    }
    }
    
    //Lecture et désérialisation des données
    //Création d'une mémoire tampon de flux de mémoire
    using (var buffer = new MemoryStream())
    {
    Console.WriteLine("Reading data from file...");
    
    //Lecture de données du fichier conteneur d'objets
    if (!ReadFile(buffer, path))
    {
    Console.WriteLine("Error during file operation. Quitting method");
    return;
    }
    
    Console.WriteLine("Deserializing Sample Data Set...");
    
    //Préparer le flux pour désérialiser les données
    buffer.Seek(0, SeekOrigin.Begin);
    
    //En raison de la signature du constructeur SequentialReader<T>, une instance AvroSerializerSettings est nécessaire
    //lorsque que Codec Factory est explicitement spécifié
    //Vous pouvez commenter la ligne ci-dessous si vous voulez utiliser Deflate intégré (voir commentaire suivant)
    AvroSerializerSettings settings = new AvroSerializerSettings();
    
    //Créer une instance SequentialReader pour le type de SensorData qui désérialise tous les objets sérialisés du flux donné
    //Elle permet l'itération sur les objets désérialisés car elle implémente l'interface IEnumerable<T>
    //La fabrique de codec personnalisé est présentée ici.
    //Par commodité, la ligne de code commentée suivante montre comment utiliser Deflate intégré.
    //(aucun paramètre Codec Factory explicite n'est requis dans ce cas).
    //Notez que les codecs intégrés et personnalisés sont interchangeables dans les opérations de lecture-écriture dans la mesure où l'exemple traite de différentes
    //IMPLÉMENTATIONS de Deflate
    //utilisant (var reader = new SequentialReader<SensorData>(AvroContainer.CreateReader<SensorData>(buffer, true)))
    using (var reader = new SequentialReader<SensorData>(
    AvroContainer.CreateReader<SensorData>(buffer, true, settings, new CodecFactoryDeflate45())))
    {
    var results = reader.Objects;
    
    //Enfin, vérifier que les données désérialisées correspondent aux données d'origine
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
    
    //Supprimer le fichier
    RemoveFile(path);
    }

	#endregion

	#region Helper Methods
    
    //Comparaison de deux objets SensorData
    private bool Equal(SensorData left, SensorData right)
    {
    return left.Position.Equals(right.Position) && left.Value.SequenceEqual(right.Value);
    }
    
    //Enregistrement du flux de mémoire dans un nouveau fichier avec le chemin d'accès donné
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
    
    //Lecture du contenu d'un fichier à l'aide d'un chemin d'accès donné vers un flux de mémoire
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
    
    //Suppression du fichier à l'aide du chemin d'accès donné
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
    
    //Créer une instance de la classe AvroSample et appeler des méthodes
    //illustrant différentes approches de sérialisation
    AvroSample Sample = new AvroSample();
    
    //Sérialisation à l'aide de Reflection vers le fichier conteneur d'objets Avro à l'aide du codec personnalisé
    Sample.SerializeDeserializeUsingObjectContainersReflectionCustomCodec();
    
    Console.WriteLine(sectionDivider);
    Console.WriteLine("Press any key to exit.");
    Console.Read();
    }
    }
    }
    // L'exemple est censé afficher la sortie suivante :
    // SÉRIALISATION À L'AIDE DE REFLECTION, DES FICHIERS CONTENEURS D'OBJETS AVRO ET D'UN CODEC PERSONNALISÉ
    //
    // Sérialisation de l'exemple de jeu de données...
    // Enregistrement des données sérialisées dans le fichier...
    // Lecture des données du fichier...
    // Désérialisation de l'exemple de jeu de données...
    // Comparaison des jeux de données initiaux et désérialisés...
    // Pour la paire 1, le résultat de la comparaison d'identité des jeux de données est True
    // Pour la paire 2, le résultat de la comparaison d'identité des jeux de données est True
    // ----------------------------------------
    // Appuyez sur n'importe quelle touche pour quitter.



[1]: http://msdn.microsoft.com/en-us/library/ms731072(v=vs.110).aspx
[2]: http://avro.apache.org/docs/current/spec.html#Required+Codecs
[3]: http://msdn.microsoft.com/en-us/library/system.io.compression.deflatestream(v=vs.110).aspx