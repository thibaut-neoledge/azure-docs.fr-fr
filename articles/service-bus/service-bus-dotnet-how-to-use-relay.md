<properties
	pageTitle="Utilisation de Service Bus Relay avec .NET | Microsoft Azure"
	description="Découvrez comment utiliser le service de relais Azure Service Bus pour connecter deux applications hébergées dans des emplacements différents."
	services="service-bus"
	documentationCenter=".net"
	authors="sethmanheim"
	manager="timlt"
	editor=""/>

<tags
	ms.service="service-bus"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="get-started-article"
	ms.date="05/06/2016"
	ms.author="sethm"/>


# Utilisation du service Azure Service Bus Relay

Cet article montre comment utiliser le service Service Bus Relay. Les exemples sont écrits en C# et utilisent l’API WCF (Windows Communication Foundation) avec les extensions contenues dans l’assembly Service Bus. Pour plus d'informations sur Service Bus Relay, consultez la vue d’ensemble [Messagerie par relais Service Bus](service-bus-relay-overview.md).

[AZURE.INCLUDE [create-account-note](../../includes/create-account-note.md)]

## Présentation de Service Bus Relay

Le [service Service Bus *Relay*](service-bus-relay-overview.md) vous permet de créer des applications hybrides qui s’exécutent à la fois dans un centre de données Azure et dans votre propre environnement d’entreprise local. Service Bus Relay facilite ce processus en offrant la possibilité d’exposer les services WCF (Windows Communication Foundation) qui résident dans un réseau d’entreprise sur le cloud public en toute sécurité, sans avoir à ouvrir une connexion de pare-feu ni à exiger des modifications intrusives dans une infrastructure de réseau d’entreprise.

![Concepts de relais](./media/service-bus-dotnet-how-to-use-relay/sb-relay-01.png)

Service Bus Relay vous permet d'héberger des services WCF au sein de votre environnement d'entreprise existant. Vous pouvez ensuite déléguer l'écoute des sessions et des demandes entrantes adressées à ces services WCF au service Service Bus s'exécutant dans Azure. Cela vous permet d'exposer ces services au code d'application s'exécutant dans Azure, à des travailleurs itinérants ou à des environnements extranet de partenaires. Service Bus vous permet de contrôler de manière précise et sécurisée les utilisateurs autorisés à accéder à ces services. Il offre un moyen efficace et sûr d'exposer les fonctionnalités et les données applicatives de vos solutions d'entreprise existantes et d'en tirer profit depuis le cloud.

Cet article montre comment utiliser Service Bus Relay pour créer un service Web WCF, exposé à l’aide d’une liaison de canal TCP, qui implémente une conversation sécurisée entre deux parties.

## Création d'un espace de noms de service

Avant d’utiliser Service Bus Relay dans Azure, vous devez créer un espace de noms. Ce dernier fournit un conteneur d'étendue pour l'adressage des ressources Service Bus au sein de votre application.

Pour créer un espace de noms de service :

[AZURE.INCLUDE [service-bus-create-namespace-portal](../../includes/service-bus-create-namespace-portal.md)]

## Obtention du package NuGet Service Bus

Le [package NuGet Service Bus](https://www.nuget.org/packages/WindowsAzure.ServiceBus) est le moyen le plus simple d’obtenir l’API Service Bus et de configurer votre application avec toutes les dépendances Service Bus. Pour installer le package NuGet dans votre application, procédez comme suit :

1.  Dans l'Explorateur de solutions, cliquez avec le bouton droit sur **References**, puis cliquez sur **Manage NuGet Packages**.
2.  Recherchez « Service Bus » et sélectionnez l’élément **Microsoft Azure Service Bus**. Cliquez sur **Install** pour terminer l’installation, puis fermez la boîte de dialogue suivante.

	![](./media/service-bus-dotnet-how-to-use-relay/getting-started-multi-tier-13.png)


## Utilisation de Service Bus pour exposer et consommer un service web SOAP avec TCP

Pour exposer un service Web SOAP WCF existant pour une consommation externe, vous devez apporter des modifications aux liaisons et aux adresses du service. Vous pouvez ainsi être amené à modifier votre fichier de configuration ou votre code, selon la façon dont vous avez défini et configuré vos services WCF. Notez que WCF vous permet de disposer de plusieurs points de terminaison réseau sur un même service. Vous pouvez donc conserver les points de terminaison internes existants et ajouter en même temps des points de terminaison Service Bus pour l'accès externe.

Dans cette tâche, vous allez créer un service WCF simple et y ajouter un écouteur Service Bus. Cet exercice part du principe que vous utilisez déjà Visual Studio, vous ne serez donc pas guidé pour la création de projet. Il porte essentiellement sur le code.

Avant de commencer les étapes ci-après, exécutez la procédure suivante pour configurer votre environnement :

1.  Dans Visual Studio, créez une application de console contenant deux projets (« Client » et « Service ») dans la solution.
2.  Ajoutez le package Microsoft Azure Service Bus NuGet pour les deux projets. Cela a pour effet d'ajouter toutes les références d'assembly nécessaires à vos projets.

### Création du service

Pour commencer, créez le service proprement dit. Un service WCF se compose d'au moins trois parties distinctes :

-   Définition d'un contrat décrivant la nature des messages échangés et des opérations à appeler.
-   Implémentation dudit contrat.
-   Hôte hébergeant ce service WCF et exposant un certain nombre de points de terminaison.

Les exemples de code présentés dans cette section portent sur chacun de ces composants.

Le contrat définit une seule opération, `AddNumbers`, qui ajoute deux nombres et renvoie le résultat. L'interface `IProblemSolverChannel` permet au client de gérer plus facilement la durée de vie du proxy. Il est recommandé de créer une interface de ce type. De même, vous avez tout intérêt à placer cette définition de contrat dans un fichier distinct de façon à pouvoir y faire référence dans les deux projets (« Client » et « Service »). Vous pouvez également copier le code dans les deux projets.

```
using System.ServiceModel;

[ServiceContract(Namespace = "urn:ps")]
interface IProblemSolver
{
    [OperationContract]
    int AddNumbers(int a, int b);
}

interface IProblemSolverChannel : IProblemSolver, IClientChannel {}
```

Une fois que le contrat est en place, l’implémentation est anecdotique.

```
class ProblemSolver : IProblemSolver
{
    public int AddNumbers(int a, int b)
    {
        return a + b;
    }
}
```

### Configurer un hôte de service par programme

Maintenant que le contrat et l'implémentation sont en place, vous pouvez héberger le service. L'hébergement se situe à l'intérieur d'un objet [System.ServiceModel.ServiceHost](https://msdn.microsoft.com/library/azure/system.servicemodel.servicehost.aspx), qui se charge de gérer les instances du service et héberge les points de terminaison qui écoutent les messages. Le code suivant configure le service avec, d’une part, un point de terminaison local normal et, d’autre part, un point de terminaison Service Bus, pour illustrer la mise en correspondance, côte à côte, des points de terminaison internes et externes. Remplacez la chaîne *namespace* par le nom de votre espace de noms et *yourKey* par la clé SAP que vous avez obtenue à l'étape de configuration précédente.

```
ServiceHost sh = new ServiceHost(typeof(ProblemSolver));

sh.AddServiceEndpoint(
   typeof (IProblemSolver), new NetTcpBinding(),
   "net.tcp://localhost:9358/solver");

sh.AddServiceEndpoint(
   typeof(IProblemSolver), new NetTcpRelayBinding(),
   ServiceBusEnvironment.CreateServiceUri("sb", "namespace", "solver"))
    .Behaviors.Add(new TransportClientEndpointBehavior {
          TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", "yourKey")});

sh.Open();

Console.WriteLine("Press ENTER to close");
Console.ReadLine();

sh.Close();
```

Dans l'exemple, vous créez deux points de terminaison dans le cadre de l'implémentation du même contrat. L’un est local et l’autre projeté via Service Bus. Les principales différences sont les liaisons : [NetTcpBinding](https://msdn.microsoft.com/library/azure/system.servicemodel.nettcpbinding.aspx) pour le point de terminaison local et [NetTcpRelayBinding](https://msdn.microsoft.com/library/azure/microsoft.servicebus.nettcprelaybinding.aspx) pour le point de terminaison et les adresses Service Bus. Le point de terminaison local possède une adresse réseau locale avec un port distinct. Le point de terminaison Service Bus possède une adresse de point de terminaison constituée de la chaîne `sb`, du nom de votre espace de noms et du chemin d'accès « solver ». L'URI obtenu est le suivant : `sb://[serviceNamespace].servicebus.windows.net/solver`, lequel identifie le point de terminaison du service comme un point de terminaison TCP Service Bus associé à un nom DNS externe complet. Si vous insérez le code en remplaçant les espaces réservés comme indiqué plus haut dans la fonction `Main` de l’application **Service**, vous obtiendrez un service fonctionnel. Si vous voulez que votre service écoute exclusivement sur Service Bus, supprimez la déclaration du point de terminaison local.

### Configurer un hôte de service dans le fichier App.config

Vous pouvez également configurer l'hôte à l'aide du fichier App.config. Dans ce cas, le code d’hébergement de service s’affiche dans l’exemple suivant.

```
ServiceHost sh = new ServiceHost(typeof(ProblemSolver));
sh.Open();
Console.WriteLine("Press ENTER to close");
Console.ReadLine();
sh.Close();
```

Les définitions de point de terminaison se déplacent dans le fichier App.config. Notez que le package NuGet a déjà ajouté une plage de définitions au fichier App.config, qui sont des extensions de la configuration requise pour Service Bus. L’exemple suivant, équivalent exact de du code précédent, doit figurer juste en dessous de l’élément **system.serviceModel**. Cet exemple suppose que l’espace de noms C# de votre projet se nomme **Service**. Remplacez les espaces réservés par l’espace de noms et la clé SAS du service Service Bus.

```
<services>
    <service name="Service.ProblemSolver">
        <endpoint contract="Service.IProblemSolver"
                  binding="netTcpBinding"
                  address="net.tcp://localhost:9358/solver"/>
        <endpoint contract="Service.IProblemSolver"
                  binding="netTcpRelayBinding"
                  address="sb://namespace.servicebus.windows.net/solver"
                  behaviorConfiguration="sbTokenProvider"/>
    </service>
</services>
<behaviors>
    <endpointBehaviors>
        <behavior name="sbTokenProvider">
            <transportClientEndpointBehavior>
                <tokenProvider>
                    <sharedAccessSignature keyName="RootManageSharedAccessKey" key="yourKey" />
                </tokenProvider>
            </transportClientEndpointBehavior>
        </behavior>
    </endpointBehaviors>
</behaviors>
```

Une fois ces modifications effectuées, le service démarre comme précédemment, mais avec deux points de terminaison actifs : l'un local et l'autre écoutant dans le cloud.

### Création du client

#### Configuration d’un client par programme

Pour consommer le service, vous pouvez construire un client WCF à l'aide d'un objet [ChannelFactory](https://msdn.microsoft.com/library/system.servicemodel.channelfactory.aspx). Service Bus utilise un modèle de sécurité basée sur un jeton, implémenté à l'aide de SAS. La classe [TokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.tokenprovider.aspx) représente un fournisseur de jetons de sécurité dont les méthodes de fabrique intégrées renvoient des fournisseurs de jetons bien connus. L'exemple ci-dessous utilise la méthode [CreateSharedAccessSignatureTokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.tokenprovider.createsharedaccesssignaturetokenprovider.aspx) pour gérer l'acquisition du jeton SAP approprié. Le nom et la clé sont ceux obtenus sur le portail comme indiqué dans la section précédente.

Tout d'abord, référencez ou copiez le code du contrat `IProblemSolver` du service dans votre projet client.

Ensuite, remplacez le code dans la méthode `Main` du client, en remplaçant de nouveau le texte d’espace réservé par l’espace de noms et la clé SAP de Service Bus.

```
var cf = new ChannelFactory<IProblemSolverChannel>(
    new NetTcpRelayBinding(),
    new EndpointAddress(ServiceBusEnvironment.CreateServiceUri("sb", "namespace", "solver")));

cf.Endpoint.Behaviors.Add(new TransportClientEndpointBehavior
            { TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey","yourKey") });

using (var ch = cf.CreateChannel())
{
    Console.WriteLine(ch.AddNumbers(4, 5));
}
```

Vous pouvez maintenant créer le client et le service, les exécuter (en commençant par le service) ; le client appellera alors le service et imprimera **9**. Vous pouvez exécuter le client et le serveur sur différents ordinateurs, voire sur des réseaux, la communication fonctionnera toujours. Le code client peut également être exécuté dans le cloud ou en local.

#### Configuration d’un client dans le fichier App.config

Vous pouvez également configurer le client à l’aide du fichier App.config.

```
var cf = new ChannelFactory<IProblemSolverChannel>("solver");
using (var ch = cf.CreateChannel())
{
    Console.WriteLine(ch.AddNumbers(4, 5));
}
```

Les définitions de point de terminaison se déplacent dans le fichier App.config. L’extrait suivant, qui est identique au code présenté précédemment, doit figurer juste en dessous de l’élément **system.serviceModel**. Comme précédemment, vous devez remplacer les espaces réservés par l’espace de noms et la clé SAS de Service Bus.

```
<client>
    <endpoint name="solver" contract="Service.IProblemSolver"
              binding="netTcpRelayBinding"
              address="sb://namespace.servicebus.windows.net/solver"
              behaviorConfiguration="sbTokenProvider"/>
</client>
<behaviors>
    <endpointBehaviors>
        <behavior name="sbTokenProvider">
            <transportClientEndpointBehavior>
                <tokenProvider>
                    <sharedAccessSignature keyName="RootManageSharedAccessKey" key="yourKey" />
                </tokenProvider>
            </transportClientEndpointBehavior>
        </behavior>
    </endpointBehaviors>
</behaviors>
```

## Étapes suivantes

Maintenant que vous avez appris les principes de base du service Service Bus Relay, consultez ces liens pour en savoir plus.

- [Aperçu de la messagerie par relais Service Bus](service-bus-relay-overview.md)
- [Présentation de l’architecture d’Azure Service Bus](service-bus-fundamentals-hybrid-solutions.md)
- Téléchargez des exemples Service Bus depuis la page [Exemples Azure][] ou consultez la [vue d’ensemble des exemples Service Bus][].

  [Shared Access Signature Authentication with Service Bus]: service-bus-shared-access-signature-authentication.md
  [Exemples Azure]: https://code.msdn.microsoft.com/site/search?query=service%20bus&f%5B0%5D.Value=service%20bus&f%5B0%5D.Type=SearchText&ac=2
  [vue d’ensemble des exemples Service Bus]: service-bus-samples.md

<!---HONumber=AcomDC_0824_2016-->