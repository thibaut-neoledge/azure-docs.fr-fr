<properties linkid="dev-net-how-to-service-bus-relay" urlDisplayName="Service Bus Relay" pageTitle="How to use Service Bus relay (.NET) - Azure" metaKeywords="get started azure Service Bus Relay C# " description="Learn how to use the Azure Service Bus relay service to connect two applications hosted in different locations." metaCanonical="" services="service-bus" documentationCenter=".NET" title="How to Use the Service Bus Relay Service" authors="sethm" solutions="" manager="timlt" editor="mattshel" />

<tags ms.service="service-bus" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="09/24/2014" ms.author="sethm" />

# Utilisation du service Service Bus Relay

Ce guide montre comment utiliser le service de relais Service Bus Relay.
Les exemples sont écrits en C# et utilisent l'API Windows Communication
Foundation avec les extensions contenues dans l'assembly Service Bus,
qui fait partie des bibliothèques .NET pour Azure. Pour plus
d'informations sur Service Bus Relay, consultez la section
[Étapes suivantes][Étapes suivantes].

[WACOM.INCLUDE [create-account-note](../includes/create-account-note.md)]

## <span class="short-header">Présentation de Service Bus Relay</span>Présentation de Service Bus Relay

Le service Service Bus **Relay** vous permet de créer des **applications
hybrides** qui s'exécutent à la fois dans un centre de données Azure et dans votre
propre environnement d'entreprise local. Service Bus Relay facilite
ce processus en offrant la possibilité d'exposer les services WCF (Windows Communication
Foundation) qui résident dans un réseau d'entreprise
sur le cloud public en toute sécurité, sans avoir à ouvrir une
connexion de pare-feu ni à exiger des modifications intrusives dans une
infrastructure de réseau d'entreprise.

![Concepts de relais][Concepts de relais]

Service Bus Relay vous permet d'héberger des services WCF au sein de votre
environnement d'entreprise existant. Vous pouvez ensuite déléguer l'écoute
des sessions et des demandes entrantes adressées à ces services WCF au service Service Bus
s'exécutant dans Azure. Cela vous permet d'exposer ces services au
code d'application s'exécutant dans Azure, à des travailleurs itinérants ou à des environnements extranet
de partenaires. Service Bus vous permet de contrôler de manière sécurisée les personnes autorisées
à accéder à ces services à un niveau très fin. Il offre un moyen efficace et
sûr d'exposer les fonctionnalités et les données applicatives de vos
solutions d'entreprise existantes et d'en tirer profit depuis le cloud.

Ce guide montre comment utiliser Service Bus Relay pour créer un service Web
WCF, exposé à l'aide d'une liaison de canal TCP, qui implémente une
conversation sécurisée entre deux parties.

## <span class="short-header">Création d'un espace de noms de service</span>Création d'un espace de noms de service

Avant d'utiliser Service Bus Relay dans Azure, vous devez
créer un espace de noms de service. Ce dernier fournit un conteneur d'étendue pour l'adressage des ressources Service Bus au sein de votre application.

Pour créer un espace de noms de service :

1.  Connectez-vous au [portail de gestion Azure][portail de gestion Azure].

2.  Dans le volet de navigation gauche du portail de gestion, cliquez sur
    **Service Bus**.

3.  Dans le volet inférieur du portail de gestion, cliquez sur **Créer**.

    ![][0]

4.  Dans la boîte de dialogue **Ajouter un nouvel espace de noms**, entrez un nom d'espace de noms.
    Le système vérifie immédiatement la disponibilité du nom.

    ![][1]

5.  Après vous être assuré de la disponibilité du nom de l'espace de noms,
    choisissez le pays ou la région où votre espace de noms doit être hébergé
    (veillez à utiliser le même pays ou la même région que celui ou celle
    où vous déployez vos ressources de calcul).

    IMPORTANT : choisissez la **même région** que celle que vous prévoyez de sélectionner pour le déploiement de votre application. Vous bénéficiez ainsi des meilleures performances.

6.  Cliquez sur la coche. Le système crée l'espace de noms de service et l'active. Vous devrez peut-être attendre plusieurs minutes afin que le système approvisionne des ressources pour votre compte.

    ![][2]

    L'espace de noms que vous avez créé apparaît alors dans le portail de gestion. Son activation peut prendre un peu de temps. Patientez jusqu’à ce que l’état soit **Actif** avant de continuer.

## <span class="short-header">Obtention des informations d'identification de gestion</span>Obtention des informations d'identification de gestion par défaut pour l'espace de noms

Pour effectuer des opérations de gestion, telles que la création d'une connexion de relais, sur le nouvel espace de noms, vous devez obtenir les informations de gestion associées.

1.  Dans le volet de navigation gauche, cliquez sur le nœud **Service Bus** pour
    afficher la liste des espaces de noms disponibles :

    ![][0]

2.  Sélectionnez l'espace de noms que vous venez de créer dans la liste affichée :

    ![][3]

3.  Cliquez sur **Connection Information**.

    ![][4]

4.  Dans la boîte de dialogue **Access connection information**, recherchez les entrées **Default Issuer** et **Default Key**. Notez ces valeurs, car elles sont utilisées ci-dessous pour effectuer des opérations avec l’espace de noms.

## <span class="short-header">Obtention du package NuGet</span>Obtention du package NuGet Service Bus

Le package **NuGet** Service Bus est le moyen le plus simple de se procurer
l'API Service Bus et de configurer votre application avec toutes les
dépendances Service Bus. L'extension Visual Studio NuGet facilite
l'installation et la mise à jour des bibliothèques et des outils de Visual Studio et
Visual Studio Express 2012 pour le Web. Le package NuGet Service Bus est le moyen le plus simple
de se procurer l'API Service Bus et de configurer votre application avec toutes
les dépendances Service Bus.

Pour installer le package NuGet dans votre application, procédez comme suit :

1.  Dans l'Explorateur de solutions, cliquez avec le bouton droit sur **References**, puis cliquez sur **Manage NuGet Packages**.
2.  Recherchez « Azure » et sélectionnez l'élément **Azure Service Bus**. Cliquez sur **Install** pour terminer l'installation, puis fermez cette boîte de dialogue.

    ![][5]

## <span class="short-header">Exposition et consommation d'un service Web SOAP</span>Utilisation de Service Bus pour exposer et consommer un service Web SOAP avec TCP

Pour exposer un service Web SOAP WCF existant pour une consommation
externe, vous devez apporter des modifications aux liaisons et
aux adresses du service. Vous pouvez ainsi être amené à modifier votre fichier de configuration ou
votre code, selon la façon dont vous avez défini et
configuré vos services WCF. Notez que WCF vous permet de disposer de plusieurs
points de terminaison réseau sur un même service. Vous pouvez donc conserver les
points de terminaison internes existants et ajouter en même temps des points de terminaison Service Bus
pour l'accès externe.

Dans cette tâche, vous allez créer un service WCF simple et
y ajouter un écouteur Service Bus. Sachant que cet exercice s'adresse à des personnes ayant une certaine connaissance de Visual Studio 2012, les étapes nécessaires à la création d'un projet ne sont pas ici détaillées. Il porte essentiellement sur le code.

Avant de commencer les étapes ci-après, exécutez la procédure suivante pour configurer
votre environnement :

1.  Dans Visual Studio, créez une application console contenant deux
    projets (« Client » et « Service ») dans la solution.
2.  Attribuez aux deux projets une infrastructure cible .NET Framework 4.
3.  Ajoutez le package **Azure Service Bus NuGet** aux deux projets.
    Cela a pour effet d'ajouter toutes les références d'assembly nécessaires à vos projets.

### Création du service

Pour commencer, créez le service proprement dit. Un service WCF se compose d'au
moins trois parties distinctes :

-   Définition d'un contrat décrivant la nature
    des messages échangés et des opérations à appeler.
-   Implémentation dudit contrat.
-   Hôte hébergeant ce service et exposant un certain nombre de
    points de terminaison.

Les exemples de code présentés dans cette section portent sur chacun de ces
composants.

Le contrat définit une seule opération, **AddNumbers**, qui ajoute
deux nombres et renvoie le résultat. L'interface **IProblemSolverChannel**
 permet au client de gérer plus facilement la durée de vie du proxy. Il est recommandé de créer une interface de ce type. De même, vous avez tout intérêt à placer cette définition
de contrat dans un fichier distinct de façon à pouvoir y faire référence dans
les deux projets (« Client » et « Service »). Vous pouvez également copier le code
dans les deux projets :

        using System.ServiceModel;
     
        [ServiceContract(Namespace = "urn:ps")]
        interface IProblemSolver
        {
            [OperationContract]
            int AddNumbers(int a, int b);
        }
     
        interface IProblemSolverChannel : IProblemSolver, IClientChannel {}

Une fois que le contrat est en place, l'implémentation est anecdotique :

        class ProblemSolver : IProblemSolver
        {
            public int AddNumbers(int a, int b)
            {
                return a + b;
            }
        }

**Configuration d'un hôte de service par programme**

Maintenant que le contrat et l'implémentation sont en place, vous pouvez héberger
le service. L'hébergement se situe à l'intérieur d'un objet
**System.ServiceModel.ServiceHost**, qui se charge de gérer
les instances du service et héberge les points de terminaison qui écoutent
les messages. Dans le code ci-dessous, le service est configuré avec d'une part
un point de terminaison local normal et d'autre part un point de terminaison Service Bus, ceci afin d'illustrer la configuration côte à côte des points de terminaison internes et externes. Remplacez la
chaîne « \*\*namespace\*\* » par le nom de votre espace de noms et « \*\*key\*\* »
par la clé d'émetteur que vous avez obtenue à l'étape de configuration précédente.

    ServiceHost sh = new ServiceHost(typeof(ProblemSolver));

    sh.AddServiceEndpoint(
       typeof (IProblemSolver), new NetTcpBinding(), 
       "net.tcp://localhost:9358/solver");

    sh.AddServiceEndpoint(
       typeof(IProblemSolver), new NetTcpRelayBinding(), 
       ServiceBusEnvironment.CreateServiceUri("sb", "**namespace**", "solver"))
        .Behaviors.Add(new TransportClientEndpointBehavior {
              TokenProvider = TokenProvider.CreateSharedSecretTokenProvider( "owner", "**key**")});

    sh.Open();

    Console.WriteLine("Press ENTER to close");
    Console.ReadLine();

    sh.Close();

Dans l'exemple, vous créez deux points de terminaison dans le cadre de
l'implémentation du même contrat. L'un est local et l'autre est projeté via Service Bus. Les principales différences entre les deux se trouvent au niveau des liaisons ;
**NetTcpBinding** pour le point de terminaison local et **NetTcpRelayBinding** pour le
point de terminaison et les adresses Service Bus. Le point de terminaison local possède une
adresse réseau locale avec un port distinct. Le point de terminaison Service Bus possède une
adresse de point de terminaison constituée de la chaîne « sb », du nom de votre espace de noms et
du chemin d'accès « solver ». L'URI obtenu est le suivant :
« sb://[serviceNamespace].servicebus.windows.net/solver », lequel identifie le
point de terminaison du service comme un point de terminaison TCP Service Bus associé à un
nom DNS externe complet. Si vous mettez en place le code en remplaçant les espaces réservés comme indiqué
plus haut dans la fonction **Main** de l'application « Service », vous disposerez d'un service fonctionnel. Si vous voulez que votre service écoute exclusivement sur Service Bus, supprimez la déclaration du point de terminaison local.

**Configuration d'un hôte de service dans le fichier App.config**

Vous pouvez également configurer l'hôte à l'aide du fichier App.config. Dans ce cas, le
code d'hébergement du service se présente comme suit :

    ServiceHost sh = new ServiceHost(typeof(ProblemSolver));
    sh.Open();
    Console.WriteLine("Press ENTER to close");
    Console.ReadLine();
    sh.Close();

Les définitions de point de terminaison se déplacent dans le fichier App.config. Notez que le package **NuGet** a déjà ajouté un éventail de définitions au fichier App.config, qui correspondent aux extensions de configuration nécessaires à Service Bus. L'extrait de code suivant, qui est l'équivalent exact du code présenté ci-dessus, doit
figurer juste en dessous de l'élément **system.serviceModel**. Cet extrait suppose que l'espace de noms C# de votre projet se nomme « Service ».
Remplacez les espaces réservés par l'espace de noms et la clé du service Service Bus.

    <services>
        <service name="Service.ProblemSolver">
            <endpoint contract="Service.IProblemSolver"
                      binding="netTcpBinding"
                      address="net.tcp://localhost:9358/solver"/>
            <endpoint contract="Service.IProblemSolver"
                      binding="netTcpRelayBinding"
                      address="sb://**namespace**.servicebus.windows.net/solver"
                      behaviorConfiguration="sbTokenProvider"/>
        </service>
    </services>
    <behaviors>
        <endpointBehaviors>
            <behavior name="sbTokenProvider">
                <transportClientEndpointBehavior>
                    <tokenProvider>
                        <sharedSecret issuerName="owner" issuerSecret="**key**" />
                    </tokenProvider>
                </transportClientEndpointBehavior>
            </behavior>
        </endpointBehaviors>
    </behaviors>

Après avoir apporté ces modifications, le service démarre comme auparavant, sauf qu'il possède deux points de terminaison actifs : un local et un à l'écoute dans le cloud.

### Création du client

**Configuration d'un client par programme**

Pour consommer le service, vous pouvez construire un client WCF à l'aide d'un objet
**ChannelFactory**. La classe
**TokenProvider** représente un fournisseur de jetons de sécurité dont les méthodes de fabrique intégrées renvoient des fournisseurs de jetons bien connus.La classe
**TokenProvider** représente un fournisseur de jetons de sécurité dont
les méthodes de fabrique intégrées renvoient des fournisseurs de jetons bien connus. L'exemple
ci-dessous utilise **SharedSecretTokenProvider** pour conserver
les informations d'identification secrètes partagées et gérer l'acquisition des jetons appropriés
auprès d'ACS. Le nom et la clé sont ceux obtenus
sur le portail comme indiqué dans la section précédente.

Tout d'abord, référencez ou copiez le code du contrat **IProblemSolver** du service
vers votre projet client.

Ensuite, remplacez le code dans la méthode **Main** du client, à nouveau en remplaçant le texte d'espace réservé par l'espace de noms et la clé du service Service Bus :

    var cf = new ChannelFactory<IProblemSolverChannel>(
        new NetTcpRelayBinding(), 
        new EndpointAddress(ServiceBusEnvironment.CreateServiceUri("sb", "**namespace**", "solver")));

    cf.Endpoint.Behaviors.Add(new TransportClientEndpointBehavior
                { TokenProvider = TokenProvider.CreateSharedSecretTokenProvider("owner","**key**") });
     
    using (var ch = cf.CreateChannel())
    {
        Console.WriteLine(ch.AddNumbers(4, 5));
    }

Vous pouvez maintenant compiler le client et le service, les
exécuter (en exécutant le service en premier) ; le client appellera alors le service et
imprimera « 9 ». Vous pouvez exécuter le client et le serveur sur différents ordinateurs, voire sur des réseaux, la communication fonctionnera toujours. Le code client peut également être exécuté dans le cloud ou en local.

**Configuration d'un client dans le fichier App.config**

Vous pouvez également configurer le client à l'aide du fichier App.config. Dans ce cas,
le code client se présente comme suit :

    var cf = new ChannelFactory<IProblemSolverChannel>("solver");
    using (var ch = cf.CreateChannel())
    {
        Console.WriteLine(ch.AddNumbers(4, 5));
    }

Les définitions de point de terminaison se déplacent dans le fichier App.config. L'extrait
suivant, qui est identique au code présenté ci-dessus, doit
figurer juste en dessous de l'élément **system.serviceModel**. Comme précédemment,
vous devez remplacer les espaces réservés par l'espace de noms et
la clé du service Service Bus.

    <client>
        <endpoint name="solver" contract="Service.IProblemSolver"
                  binding="netTcpRelayBinding"
                  address="sb://**namespace**.servicebus.windows.net/solver"
                  behaviorConfiguration="sbTokenProvider"/>
    </client>
    <behaviors>
        <endpointBehaviors>
            <behavior name="sbTokenProvider">
                <transportClientEndpointBehavior>
                    <tokenProvider>
                        <sharedSecret issuerName="owner" issuerSecret="**key**" />
                    </tokenProvider>
                </transportClientEndpointBehavior>
            </behavior>
        </endpointBehaviors>
    </behaviors>

## <span class="short-header">Étapes suivantes</span>Étapes suivantes

Maintenant que vous avez appris les principes de base du service Service Bus **Relay**,
consultez ces liens pour en savoir plus :

-   Création d'un service : [Génération d'un service pour Service Bus][Génération d'un service pour Service Bus].
-   Création du client : [Génération d'une application Service Bus Client][Génération d'une application Service Bus Client].
-   Exemples Service Bus : à télécharger depuis [Exemples Azure][Exemples Azure].

  [Étapes suivantes]: #next_steps
  [Concepts de relais]: ./media/service-bus-dotnet-how-to-use-relay/sb-relay-01.png
  [portail de gestion Azure]: http://manage.windowsazure.com
  [0]: ./media/service-bus-dotnet-how-to-use-relay/sb-queues-13.png
  [1]: ./media/service-bus-dotnet-how-to-use-relay/sb-queues-04.png
  [2]: ./media/service-bus-dotnet-how-to-use-relay/getting-started-multi-tier-27.png
  [3]: ./media/service-bus-dotnet-how-to-use-relay/sb-queues-09.png
  [4]: ./media/service-bus-dotnet-how-to-use-relay/sb-queues-06.png
  [5]: ./media/service-bus-dotnet-how-to-use-relay/getting-started-multi-tier-13.png
  [Génération d'un service pour Service Bus]: http://msdn.microsoft.com/fr-fr/library/windowsazure/ee173564.aspx
  [Génération d'une application Service Bus Client]: http://msdn.microsoft.com/fr-fr/library/windowsazure/ee173543.aspx
  [Exemples Azure]: http://code.msdn.microsoft.com/windowsazure
