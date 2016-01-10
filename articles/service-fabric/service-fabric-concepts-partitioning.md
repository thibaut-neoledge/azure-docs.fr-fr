<properties
   pageTitle="Partitionnement des services Service Fabric | Microsoft Azure"
   description="Explique comment partitionner les services Service Fabric"
   services="service-fabric"
   documentationCenter=".net"
   authors="bmscholl"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="11/17/2015"
   ms.author="bscholl"/>

# Comment partitionner les services fiables de Service Fabric
Cet article présente les concepts de base du partitionnement des services fiables de Service Fabric. Le code source utilisé dans cet article est également disponible sur [Github](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/master/Services/AlphabetPartitions).

## Qu’est-ce que le partitionnement ?
Le partitionnement n’est pas spécifique à Service Fabric ; il s’agit en fait d’un modèle de base pour la création de services évolutifs. Dans un sens plus large, nous pouvons envisager le partitionnement comme un concept de division d’état (données) et de calcul en plus petites unités accessibles dans un souci d’amélioration de l’évolutivité et des performances. Le [partitionnement de données][wikipartition] représente également une forme bien connue de partitionnement.


### Partitionnement des services sans état Service Fabric
Pour les services sans état, vous pouvez considérer une partition comme une unité logique contenant une ou plusieurs instances d’un service. La figure 1 illustre un service sans état avec 5 instances distribuées sur un cluster à l’aide d’une seule partition.

![Service sans état](./media/service-fabric-concepts-partitioning/statelessservice.png)

Il existe en réalité deux types de solutions de service sans état. Le premier est un service qui conserve son état en externe, par exemple dans la base de données SQL Azure (à la manière d’un site Web qui stocke les informations et données de session) ; le second est un service de calcul uniquement (comme une calculatrice ou un outil de miniaturisation d’images) qui ne gère aucun état persistant. Dans les deux cas, le partitionnement d’un service sans état constitue un scénario extrêmement rare, et l’évolutivité et la disponibilité passent normalement par une augmentation du nombre d’instances. Il n’y a que lorsque vous devez répondre à des demandes de routage spéciales qu’il est judicieux d’envisager plusieurs partitions pour des instances de service sans état. Par exemple, considérez un cas de figure où des utilisateurs dont les identifiants sont compris dans une certaine plage doivent être uniquement pris en charge par une instance de service particulière. Vous pouvez également partitionner un service sans état lorsque vous disposez d’un serveur principal véritablement partitionné, par exemple une base de données SQL partitionnée, et que vous souhaitez contrôler l’instance de service qui doit écrire sur la partition de base de données ou exécuter, au sein du service sans état, d’autres tâches de préparation nécessitant les mêmes informations de partitionnement que celles utilisées dans le serveur principal. Ces types de scénarios peuvent également être résolus de différentes façons et n’impliquent pas nécessairement un partitionnement du service.

La suite de cette procédure pas à pas se concentre sur les services avec état.

### Partitionnement des services avec état Service Fabric
Service Fabric facilite le développement de services avec état évolutifs en offrant une excellente méthode de partitionnement d’état (données). Conceptuellement, vous pouvez considérer une partition d'un service avec état comme une unité d'échelle extrêmement fiable grâce aux [réplicas](service-fabric-availability-services.md) distribués et équilibrés entre les nœuds du cluster. Le partitionnement dans le cadre des services avec état Service Fabric désigne le processus permettant d'assigner à une partition de service donnée (comme indiqué précédemment, une partition est un ensemble de [réplicas](service-fabric-availability-services.md)) la responsabilité d'une partie de l'état complet du service. Service Fabric présente l’avantage de placer les partitions sur différents nœuds, ce qui leur permet d’atteindre la limite de ressources d’un nœud. À mesure que le volume de données augmente, les partitions se développent. Service Fabric rééquilibre alors les partitions sur les nœuds pour garantir une utilisation efficace et constante des ressources matérielles.

Pour vous donner un exemple, imaginons que vous commenciez par un cluster à 5 nœuds et un service configuré avec 10 partitions et une cible de trois réplicas. Dans ce cas, Service Fabric équilibrerait et distribuerait les réplicas sur le cluster, et vous finiriez avec 2 [réplicas](service-fabric-availability-services.md) principaux par nœud. À présent, si vous deviez étendre votre cluster sur 10 nœuds, Service Fabric rééquilibrerait les [réplicas](service-fabric-availability-services.md) principaux sur les 10 nœuds. Même en revenant à 5 nœuds, Service Fabric rééquilibrerait tous les réplicas sur les 5 nœuds.

La figure 2 montre la distribution des 10 partitions avant et après la mise à l’échelle du cluster.

![Service avec état](./media/service-fabric-concepts-partitioning/scaledcluster.png)

Le système est donc mis à l’échelle dans la mesure où les demandes des clients sont réparties entre plusieurs ordinateurs ; les performances globales de l’application sont améliorée et les conflits d’accès aux blocs de données sont réduits.

## Planification du partitionnement
Avant d’implémenter un service, vous devez toujours envisager la stratégie de partitionnement requise pour la mise à l’échelle. Il existe différentes approches, mais toutes se concentrent sur ce que l’application doit effectuer. Dans le contexte de cet article, passons en revue quelques-uns des aspects les plus importants.

Une bonne approche consiste à réfléchir de prime abord à la structure de l’état qui doit être partitionné.

Prenons un exemple simple : si vous deviez créer un service dans le cadre d’un sondage à l’échelle d’un comté, vous pourriez créer une partition pour chaque ville du comté, puis stocker les votes de chaque personne de la ville dans la partition correspondante. La figure 3 présente une liste de personnes associées au nom de la ville dans laquelle elles résident.

![Partition simple](./media/service-fabric-concepts-partitioning/cities.png)

Comme la population des villes varie considérablement, vous pouvez finir par des partitions qui contiennent un grand nombre de données (par exemple, Seattle) et d’autres partitions avec un état très limité (par exemple, Kirkland). Par conséquent, que se passe-t-il si l’on utilise des partitions ayant des volumes d’état irréguliers ?

Si vous réfléchissez de nouveau à cet exemple, vous pouvez facilement voir que la partition qui contient les votes pour la ville de Seattle génère davantage de trafic que celle de Kirkland. Par défaut, Service Fabric fait en sorte qu’il y ait approximativement le même nombre de réplicas primaires et secondaires sur chaque nœud. Autrement dit, vous devriez obtenir des nœuds contenant des réplicas qui gèrent davantage de trafic et d’autres qui traitent une quantité de trafic moindre. Dans un cluster, vous préférerez sans doute éviter une telle alternance de points chauds et de points froids.

Pour éviter ce problème, il y a deux choses à faire du point de vue du partitionnement :

- Essayer de partitionner l’état afin de garantir une répartition équitable entre toutes les partitions.
- [Générer un rapport de mesures pour chacun des réplicas du service](service-fabric-resource-balancer-dynamic-load-reporting.md). Service Fabric permet de générer des rapports de mesures d’un service, notamment sur la quantité de mémoire ou le nombre d’enregistrements. Service Fabric s’appuie sur ces mesures pour détecter si certaines partitions gèrent des charges plus élevées que d’autres et pour rééquilibrer le cluster en déplaçant les réplicas vers des nœuds plus appropriés.

Il se peut que vous ne connaissiez pas la quantité de données qui seront affectées à une partition donnée. Aussi, nous vous recommandons de commencer par adopter une stratégie qui répartit les données uniformément sur les partitions, puis de générer un rapport sur la charge. La première méthode permet d’éviter les situations décrites dans l’exemple du vote, tandis que la seconde lisse dans le temps les différences d’accès ou de charge temporaires.

Lors de la planification d’un partitionnement, vous devez également choisir le nombre de partitions de départ approprié. Du point de vue de Service Fabric, rien ne vous empêche de commencer par un nombre plus élevé de partitions que prévu pour votre scénario. En fait, il est tout à fait acceptable de partir du nombre maximal de partitions.

Il est rare que vous ayez besoin au final d’un nombre de partitions supérieur à celui que vous avez initialement choisi. Comme vous ne pouvez pas modifier le nombre de partitions après coup, vous devez appliquer certaines approches avancées, par exemple créer une nouvelle instance de service du même type de service et implémenter une logique côté client qui achemine les demandes vers l’instance de service appropriée à partir des informations côté client que doit gérer votre code client.

Pour planifier votre partitionnement, vous devez également tenir compte des ressources disponibles de l’ordinateur. Comme l’état doit être accessible et stocké, vous êtes soumis aux contraintes suivantes :

- Limites de bande passante réseau
- Limites de mémoire système
- Limites du stockage sur disque

Par conséquent, que se passe-t-il si vous subissez des contraintes de ressources dans un cluster en cours d’exécution ? La réponse est simple : il vous suffit d’augmenter la taille de votre cluster pour prendre en compte les nouvelles spécifications.

[Le guide de planification de la capacité](service-fabric-capacity-planning.md) propose des conseils qui vous aideront à déterminer le nombre de nœuds dont a besoin votre cluster.

## Procédure de partitionnement
Cette section décrit comment débuter avec le partitionnement de votre service.

Tout d’abord, Service Fabric propose un choix entre trois schémas de partition.

- Partitionnement par plage (également appelé UniformInt64Partition)
- Partitionnement nommé. Les applications qui utilisent ce modèle comportent généralement des données qui utilisent des compartiments au sein d’un ensemble limité. Les régions, les codes postaux, les groupes de clients ou les autres limites de l’entreprise constituent des exemples courants de champs de données utilisés comme clés de partition nommée.
- Partitionnement singleton. Les partitions singleton sont généralement utilisées lorsque le service ne requiert aucun routage supplémentaire. Par exemple, les services sans état utilisent ce schéma de partitionnement par défaut.

Les schémas de partitionnement nommé et singleton sont des formes spéciales de partitions par plage. Par défaut, les modèles Visual Studio pour Service Fabric utilisent le partitionnement par plage car il représente le schéma le plus courant et le plus utile. Le reste de cet article se concentre sur le schéma de partitionnement par plage.

### Schéma de partitionnement par plage
Il permet de spécifier une plage d'entiers (identifiée par une clé basse et une clé haute) et un nombre de partitions (n). Il crée n partitions, chacune responsable d’une sous-plage qui ne chevauche pas la plage globale de clés de partition. Exemple : un schéma de partitionnement par plage doté d’une clé basse de 0, d’une clé haute de 99 et d’un nombre de 4 créerait 4 partitions telles qu’elles sont illustrées ci-dessous.

![Partitionnement par plage](./media/service-fabric-concepts-partitioning/range-partitioning.png)

Une approche courante consiste à créer un hachage basé sur une clé unique dans le dataset. Un numéro d'immatriculation de véhicule, un ID d'employé ou une chaîne unique sont des exemples courants de clés. Vous utilisez cette clé unique pour créer un code de hachage et moduler la plage de clés afin de l’utiliser comme clé. Vous pouvez spécifier les limites supérieure et inférieure de la plage de clé autorisée.


### Sélection d'un algorithme de hachage
Une partie importante du hachage consiste à sélectionner l'algorithme de hachage. Il convient de tenir compte de l’objectif du hachage, à savoir si celui-ci est de regrouper des clés similaires proches les unes des autres (hachage sensible à la localité) ou si l’activité doit être distribuée de manière large entre toutes les partitions (hachage de distribution), ce qui est le cas de figure le plus courant.

Un bon algorithme de hachage de distribution a la particularité d’être facile à calculer, de comporter peu de collisions et de distribuer uniformément les clés. L'algorithme de hachage [FNV-1](https://en.wikipedia.org/wiki/Fowler%E2%80%93Noll%E2%80%93Vo_hash_function) est très efficace, par exemple.


[La page Wikipedia sur les fonctions de hachage](http://en.wikipedia.org/wiki/Hash_function) est une ressource proposant de nombreux algorithmes de code de hachage généraux.

## Création d’un service avec état avec plusieurs partitions
Nous allons créer le premier service fiable avec état à plusieurs partitions. Dans cet exemple, vous allez générer une application très simple dans laquelle vous souhaitez stocker tous les noms qui commencent par la même lettre dans la même partition.

Avant d’écrire de code, vous devez réfléchir aux partitions et aux clés de partition. Vous avez besoin de 26 partitions, soit une partition pour chaque lettre de l’alphabet, mais devez-vous utiliser des clés basses ou hautes ? Comme nous ne voulons littéralement qu’une partition par lettre, nous pouvons utiliser 0 comme clé basse et 25 comme clé haute puisque chaque lettre est sa propre clé.


>[AZURE.NOTE]Il s’agit d’un scénario simplifié car, en réalité, la distribution serait inégale. Les noms commençant par la lettre S ou M sont plus courants que ceux commençant par X ou Y.


1. Ouvrez Visual Studio ->Fichier -> Nouveau -> Projet.
2. Dans la boîte de dialogue Nouveau projet, sélectionnez l’application Service Fabric
3. Appelez le projet AlphabetPartitions
4. Dans la boîte de dialogue Créer un service, choisissez Service avec état et appelez-le Alphabet.Processing comme indiqué dans l'image ci-dessous. ![alphabetstateful](./media/service-fabric-concepts-partitioning/alphabetstatefulnew.png)
5. Définissez le nombre de partitions. Ouvrez le fichier ApplicationManifest.xml dans le projet AlphabetPartitions et mettez à jour le paramètre Processing\_PartitionCount sur 26, comme illustré ci-dessous.

    ```xml
    <Parameter Name="Processing_PartitionCount" DefaultValue="26" />
    ```
    Vous devez également mettre à jour les propriétés LowKey et HighKey de l'élément StatefulService, comme illustré ci-dessous. ```xml
    <Service Name="Processing">
      <StatefulService ServiceTypeName="ProcessingType" TargetReplicaSetSize="[Processing_TargetReplicaSetSize]" MinReplicaSetSize="[Processing_MinReplicaSetSize]">
        <UniformInt64Partition PartitionCount="[Processing_PartitionCount]" LowKey="0" HighKey="25" />
      </StatefulService>
    </Service>
    ```

6. Pour que le service soit accessible, ouvrez un point de terminaison sur un port en ajoutant la mise à jour de l’élément de point de terminaison de ServiceManifest.xml (situé dans le dossier PackageRoot) pour le service Alphabet.Processing, comme indiqué ci-dessous :

    ```xml
    <Endpoint Name="ProcessingServiceEndpoint" Protocol="http" Type="Internal" />
    ```

    Le service est maintenant configuré pour écouter un point de terminaison interne avec 26 partitions.

7. Vous devez ensuite remplacer la méthode `CreateServiceReplicaListeners()` de la classe Processing.

    >[AZURE.NOTE]Pour cet exemple, supposons que vous utilisiez un HttpCommunicationListener simple. Vous trouverez des informations supplémentaires sur la communication des services fiables [ici](service-fabric-reliable-services-communication.md).

8. L'URL sur laquelle écoute un réplica utilise de préférence le format suivant : `{scheme}://{nodeIp}:{port}/{partitionid}/{replicaid}/{guid}`. Vous pouvez donc configurer votre écouteur de communication pour qu'il écoute sur les points de terminaison corrects en utilisant ce modèle. Plusieurs réplicas de ce service peuvent être hébergés sur le même ordinateur, ce qui signifie que cette adresse doit être unique pour le réplica. C’est pourquoi nous utilisons dans l’URL l’ID de partition et l’ID de réplica. HttpListener peut écouter plusieurs adresses sur le même port tant que le préfixe d’URL est unique. Le GUID supplémentaire est fourni dans les cas où des réplicas secondaires écouteraient également les demandes en lecture seule. Dans ce cas de figure, vous voudrez vous assurer qu’une nouvelle adresse unique est utilisée lors de la transition du réplica principal vers le réplica secondaire afin de forcer les clients à résoudre l’adresse. « + » est utilisé comme adresse ici afin que le réplica écoute sur tous les hôtes disponibles (IP, FQDM, localhost, etc.). Le code ci-dessous en montre un exemple.

    ```CSharp
    protected override IEnumerable<ServiceReplicaListener> CreateServiceReplicaListeners()
    {
            return new[] { new ServiceReplicaListener(CreateInternalListener, "Internal", false) };
    }
    private ICommunicationListener CreateInternalListener(StatefulServiceInitializationParameters args)
    {
        EndpointResourceDescription internalEndpoint = args.CodePackageActivationContext.GetEndpoint("ProcessingServiceEndpoint");

        string uriPrefix = String.Format(
                "{0}://+:{1}/{2}/{3}-{4}/",
                internalEndpoint.Protocol,
                internalEndpoint.Port,
                this.ServiceInitializationParameters.PartitionId,
                this.ServiceInitializationParameters.ReplicaId,
                Guid.NewGuid());

        string nodeIP = FabricRuntime.GetNodeContext().IPAddressOrFQDN;
        string uriPublished = uriPrefix.Replace("+", nodeIP);
        return new HttpCommunicationListener(uriPrefix, uriPublished, this.ProcessInternalRequest);
    }
    ```
Il est également important de noter que l’URL publiée diffère légèrement du préfixe de l’URL d’écoute. L’URL d’écoute est donnée à HttpListener. L’URL publiée est l’URL qui est publiée dans le service de nommage Service Fabric, utilisé pour la découverte de service. Les clients demanderont cette adresse via ce service de découverte. L’adresse que les clients obtiennent doit utiliser l’IP réelle ou le FQDN du nœud pour se connecter. Vous devez donc remplacer le symbole « + » par l’IP ou le FQDN du nœud, comme indiqué ci-dessus.
9. La dernière étape consiste à ajouter la logique de traitement au service comme indiqué ci-dessous.

    ```CSharp
    private async Task ProcessInternalRequest(HttpListenerContext context, CancellationToken cancelRequest)
    {
          string output = null;
          string user = context.Request.QueryString["lastname"].ToString();

          try
          {
              output = await this.AddUserAsync(user);
          }
          catch (Exception ex)
          {
              output = ex.Message;
          }

          using (HttpListenerResponse response = context.Response)
          {
              if (output != null)
              {
                  byte[] outBytes = Encoding.UTF8.GetBytes(output);
                  response.OutputStream.Write(outBytes, 0, outBytes.Length);
              }
          }
      }
      private async Task<string> AddUserAsync(string user)
      {
          IReliableDictionary<String, String> dictionary = await this.StateManager.GetOrAddAsync<IReliableDictionary<String, String>>("dictionary");

          using (ITransaction tx = this.StateManager.CreateTransaction())
          {
              bool addResult = await dictionary.TryAddAsync(tx, user.ToUpperInvariant(), user);

              await tx.CommitAsync();

              return String.Format(
                  "User {0} {1}",
                  user,
                  addResult ? "sucessfully added" : "already exists");
          }
      }
    ```

    `ProcessInternalRequest` lit les valeurs du paramètre de la chaîne de requête utilisée pour appeler la partition et appelle `AddUserAsync` pour ajouter le nom au dictionnaire fiable `m_name`.

10. Ajoutons un service sans état au projet pour voir comment appeler une partition particulière. Ce service sert d’interface web simple qui accepte le nom comme paramètre de la chaîne de requête, détermine la clé de partition et l’envoie au service Alphabet.Processing pour traitement.
11. Dans la boîte de dialogue Créer un service, choisissez Service sans état et appelez-le Alphabet.WebAPI comme indiqué ci-dessous. ![alphabetstateless](./media/service-fabric-concepts-partitioning/alphabetstatelessnew.png).
12. Mettez à jour les informations du point de terminaison dans le fichier ServiceManifest.xml du service Alphabet.WebApi pour ouvrir un port comme indiqué ci-dessous.

    ```xml
    <Endpoint Name="WebApiServiceEndpoint" Protocol="http" Port="8090"/>
    ```

13. Vous devez retourner une collection de ServiceInstanceListeners. Là encore, vous pouvez choisir d’implémenter un simple HttpCommunicationListener.

    ```CSharp
    protected override IEnumerable<ServiceInstanceListener> CreateServiceInstanceListeners()
    {
           return new[] {new ServiceInstanceListener(this.CreateInputListener, "Input")};
    }
    private ICommunicationListener CreateInputListener(StatelessServiceInitializationParameters args)
    {
           // Service instance's URL is the node's IP & desired port
           EndpointResourceDescription inputEndpoint = args.CodePackageActivationContext.GetEndpoint("WebApiServiceEndpoint")
           string uriPrefix = String.Format("{0}://+:{1}/alphabetpartitions/", inputEndpoint.Protocol, inputEndpoint.Port);
           var uriPublished = uriPrefix.Replace("+", m_nodeIP);
           return new HttpCommunicationListener(uriPrefix, uriPublished, ProcessInputRequest);
     }
     ```
14. Maintenant, vous devez implémenter la logique de traitement. HttpCommunicationListener appelle `ProcessInputRequest` lorsqu'une demande lui parvient. Par conséquent, vous devez ajouter le code ci-dessous.

    ```CSharp
    private async Task ProcessInputRequest(HttpListenerContext context, CancellationToken cancelRequest)
    {
           String output = null;
           try
           {
               string lastname = context.Request.QueryString["lastname"];
               char firstLetterOfLastName = lastname.First();
               int partitionKey = Char.ToUpper(firstLetterOfLastName) - 'A';

               ResolvedServicePartition partition = await this.servicePartitionResolver.ResolveAsync(alphabetServiceUri, partitionKey, cancelRequest);
               ResolvedServiceEndpoint ep = partition.GetEndpoint();
               JObject addresses = JObject.Parse(ep.Address);
               string primaryReplicaAddress = addresses["Endpoints"].First()["Value"].Value<string>();

               UriBuilder primaryReplicaUriBuilder = new UriBuilder(primaryReplicaAddress);
               primaryReplicaUriBuilder.Query = "lastname=" + lastname;

               string result = await this.httpClient.GetStringAsync(primaryReplicaUriBuilder.Uri);

               output = String.Format(
               "Result: {0}. Partition key: '{1}' generated from the first letter '{2}' of input value '{3}'. Processing service partition ID: {4}. Processing service replica address: {5}",
               result,
               partitionKey,
               firstLetterOfLastName,
               lastname,
               partition.Info.Id,
               primaryReplicaAddress);
    }
    catch (Exception ex) { output = ex.Message; }
    using (var response = context.Response)
    {
               if (output != null)
               {
                   output = output + "added to Partition: " + primaryReplicaAddress;
                   byte[] outBytes = Encoding.UTF8.GetBytes(output);
                   response.OutputStream.Write(outBytes, 0, outBytes.Length);
               }
           }
      }
      ```

    Voici la procédure pas à pas. Le code lit la première lettre du paramètre de chaîne de requête `lastname` sous forme de caractère. Il détermine ensuite la clé de partition pour cette lettre en soustrayant la valeur hexadécimale de `A` de la valeur hexadécimale de la première lettre des derniers noms.

    ```CSharp
    string lastname = context.Request.QueryString["lastname"]; char firstLetterOfLastName = lastname.First(); int partitionKey = Char.ToUpper(firstLetterOfLastName) - 'A';
    ```

    N’oubliez pas que, dans cet exemple, nous utilisons 26 partitions avec une seule clé de partition par partition. Nous obtenons ensuite la partition de service `partition` pour cette clé en utilisant la méthode `ResolveAsync` sur l'objet `servicePartitionResolver`. `servicePartitionResolver` est défini comme

    ```CSharp
    private static readonly ServicePartitionResolver servicePartitionResolver = ServicePartitionResolver.GetDefault();
    ```

    La méthode `ResolveAsync` accepte l'URI de service, la clé de partition et un jeton d'annulation en tant que paramètres. L'URI de service pour le service de traitement est `fabric:/AlphabetPartitions/Processing`. Nous obtenons ensuite le point de terminaison de la partition.

    ```CSharp
    ResolvedServiceEndpoint ep = partition.GetEndpoint()
    ```

    Pour finir, nous allons générer l’URL du point de terminaison ainsi que la chaîne de requête avant d’appeler le service de traitement.

    ```CSharp
    JObject addresses = JObject.Parse(ep.Address);
    string primaryReplicaAddress = addresses["Endpoints"].First()["Value"].Value<string>();

    UriBuilder primaryReplicaUriBuilder = new UriBuilder(primaryReplicaAddress);
    primaryReplicaUriBuilder.Query = "lastname=" + lastname;

    string result = await this.httpClient.GetStringAsync(primaryReplicaUriBuilder.Uri);
    ```

    Une fois le traitement effectué, nous pouvons réécrire la sortie.

15. La dernière étape consiste à tester le service. Visual Studio utilise les paramètres d’application pour un déploiement local et sur le cloud. Pour tester le service avec 26 partitions localement, vous devez mettre à jour le fichier `Local.xml` dans le dossier ApplicationParameters du projet AlphabetPartitions, comme indiqué ci-dessous :

    ```xml
    <Parameters>
      <Parameter Name="Processing_PartitionCount" Value="26" />
      <Parameter Name="WebApi_InstanceCount" Value="1" />
  </Parameters>
  ```

16. Une fois déployé, vous pouvez vérifier le service et toutes ses partitions dans l'Explorateur de Service Fabric. ![Service](./media/service-fabric-concepts-partitioning/alphabetservicerunning.png)
17. Dans un navigateur, vous pouvez tester la logique de partitionnement en entrant `http://localhost:8090/?lastname=somename`. Vous verrez que chaque nom de famille commençant par la même lettre sera stocké dans la même partition. ![Browser](./media/service-fabric-concepts-partitioning/alphabetinbrowser.png)

Le code source complet de cet exemple est disponible sur [Github](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/master/Services/AlphabetPartitions)

## Étapes suivantes

Pour plus d'informations sur les concepts propres à Service Fabric, consultez les articles suivants :

- [Disponibilité des services Service Fabric](service-fabric-availability-services.md)

- [Extensibilité des services Service Fabric](service-fabric-concepts-scalability.md)

[wikipartition]: https://en.wikipedia.org/wiki/Partition_(database)

<!-----HONumber=AcomDC_1125_2015-->