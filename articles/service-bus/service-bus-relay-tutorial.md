<properties 
    pageTitle="Didacticiel sur la messagerie par relais Service Bus | Microsoft Azure"
    description="Créer une application et un service client Service Bus à l’aide de la messagerie Service Bus relayée."
    services="service-bus"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="tysonn" />
<tags 
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="05/17/2016"
    ms.author="sethm" />

# Didacticiel sur la messagerie par relais Service Bus

Ce didacticiel vous montre comment créer une application cliente et un service Service Bus simple à l’aide des fonctionnalités « relais » Service Bus. Pour obtenir un didacticiel correspondant utilisant la [messagerie répartie](service-bus-messaging-overview.md#Brokered-messaging) Service Bus, consultez le [didacticiel .NET sur la messagerie répartie Service Bus](service-bus-brokered-tutorial-dotnet.md).

Ce didacticiel vous offre une présentation des étapes requises pour créer une application client Service Bus et une application service. À l’instar de leurs homologues WCF, un service est une construction qui expose un ou plusieurs points de terminaison, chacun d’entre eux exposant une ou plusieurs opérations de service. Le point de terminaison de service spécifie une adresse où le service peut être trouvé, une liaison contenant les informations qu’un client doit communiquer avec le service et le contrat qui définit la fonctionnalité fournie par le service à ses clients. La principale différence entre un service WCF et un service Service Bus est que le point de teminaison est exposé dans le cloud en non localement sur votre ordinateur.

Lorsque vous avez exécuté la suite des rubriques présentes dans ce tutoriel, vous obtenez un service en cours de fonctionnement, et un client peut invoquer les opérations du service. La première rubrique décrit comment configurer un compte. Les étapes suivantes décrivent comment définir un service utilisant un contrat, comment implémenter ledit service et comment le configurer dans le code. Elles décrivent également comment héberger et exécuter le service. Le service créé est auto hébergé et le client et le service s’exécutent sur le même ordinateur. Vous pouvez configurer le service à l’aide d’un code ou d’un fichier de configuration.

Les trois dernières étapes expliquent comment créer une application cliente, la configurer et créer et utiliser un client pouvant accéder à la fonctionnalité de l’hôte.

Toutes les rubriques de cette section supposent que vous utilisez Visual Studio comme environnement de développement.

## Inscrivez-vous pour obtenir un compte

La première étape consiste à créer l’espace de noms de service Service Bus et obtenir une clé de signature d’accès partagé (SAP). Un espace de noms fournit une limite d’application pour chaque application exposée via Service Bus. La combinaison de l’espace de noms et de la clé SAP fournit à Service Bus une information d’identification permettant d’authentifier l’accès à une application.

1. Pour créer un espace de noms, visitez le [portail Azure Classic][]. Cliquez sur **Service Bus** sur le côté gauche, puis sur **Créer**. Entrez un nom pour votre espace de noms, acceptez les valeurs par défaut pour toutes les autres valeurs, puis cliquez sur la coche OK.

	>[AZURE.NOTE] Vous n’avez pas à utiliser le même espace de noms pour les applications client et le service.

	![][4]

1. Dans la fenêtre principale du portail, cliquez sur le nom d'espace de noms que vous avez créé à l'étape précédente.

2. Cliquez sur l’onglet **Configurer** pour afficher les stratégies d’accès partagé par défaut et les clés pour votre espace de noms.

	![][1]

3. Notez la clé primaire de la stratégie **RootManageSharedAccessKey**, ou copiez-la dans le Presse-papiers. Vous aurez besoin de cette valeur plus loin dans ce didacticiel.

## Définition d’un contrat de service WCF à utiliser avec Service Bus

Le contrat de service spécifie les opérations (terminologie du service web pour les fonctions ou méthodes) que le service prend en charge. Les contrats sont créés en définissant une interface C++, C# ou Visual Basic. Chaque méthode dans l'interface correspond à une opération de service spécifique. L’attribut [ServiceContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicecontractattribute.aspx) doit être appliqué à chaque interface et l’attribut [OperationContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.operationcontractattribute.aspx) doit être appliqué à chaque opération. Si une méthode présente dans une interface contenant l’attribut [ServiceContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicecontractattribute.aspx) ne comporte pas l’attribut [OperationContractAttribute](https://msdn.microsoft.com/library/system.servicemodel.operationcontractattribute.aspx), cette méthode n’est pas exposée. Le code servant à effectuer ces tâches est fourni dans l’exemple qui suit la procédure. Pour une description plus approfondie des contrats et des services, consultez [Conception et implémentation de Services](https://msdn.microsoft.com/library/ms729746.aspx) dans la documentation WCF.

### Création d’un contrat de Service Bus avec une interface

1. Ouvrez Visual Studio en tant qu’administrateur en cliquant avec le bouton droit sur le programme dans le menu **Démarrer**, puis sur **Exécuter en tant qu’administrateur**.

2. Créez un projet d’application de console. Cliquez sur le menu **Fichier**, sélectionnez **Nouveau**, puis cliquez sur **Projet**. Dans la boîte de dialogue **Nouveau projet**, cliquez sur **Visual C#** (si **Visual C#** n’apparaît pas, regardez dans les **autres langages**). Cliquez sur le modèle **Application Console** et nommez-le **EchoService**. Cliquez sur **OK** pour créer le projet.

	![][2]

3. Installez le package NuGet Service Bus. Ce package ajoute automatiquement des références aux bibliothèques Service Bus, ainsi qu’au WCF **System.ServiceModel**. [System.ServiceModel](https://msdn.microsoft.com/library/system.servicemodel.aspx) est l’espace de noms qui vous permet d’accéder par programme aux fonctionnalités WCF de base. Service Bus utilise la plupart des objets et attributs de WCF pour définir des contrats de service.

	Dans l’Explorateur de solutions, cliquez avec le bouton droit sur la solution, puis cliquez sur **Gérer les packages NuGet pour la solution**. Cliquez sur l’onglet **Parcourir**, puis recherchez `Microsoft Azure Service Bus`. Vérifiez que le nom du projet est spécifié dans la zone **Version(s)**. Cliquez sur **Installer** et acceptez les conditions d’utilisation.

	![][3]

3. Dans l’Explorateur de solutions, double-cliquez sur le fichier Program.cs pour l’ouvrir dans l’éditeur, si celui-ci n’est pas déjà ouvert.

4. Ajoutez les instructions using suivantes au début du fichier :

	```
	using System.ServiceModel;
	using Microsoft.ServiceBus;
	```

1. Changez le nom de l’espace de noms du nom par défaut **EchoService** en **Microsoft.ServiceBus.Samples**.

	>[AZURE.IMPORTANT] Ce didacticiel utilise l’espace de noms C# **Microsoft.ServiceBus.Samples**, qui est l’espace de noms du contrat géré type utilisé dans le fichier de configuration à l’étape [Configurer le client WCF](#configure-the-wcf-client). Vous pouvez spécifier n’importe quel espace de noms lorsque vous générez cet exemple ; toutefois, le didacticiel ne fonctionnera que si vous modifiez les espaces de noms du contrat et le service en conséquence, dans le fichier de configuration d’application de service. L’espace de noms spécifié dans le fichier App.config doit être identique à l’espace de noms spécifié dans vos fichiers C#.

1. Définissez directement après la déclaration d’espace de noms `Microsoft.ServiceBus.Samples`, mais à l’intérieur de l’espace de noms, une nouvelle interface nommée `IEchoContract` et appliquez l’attribut `ServiceContractAttribute` à cette interface avec une valeur d’espace de noms de **http://samples.microsoft.com/ServiceModel/Relay/**. La valeur de l'espace de noms diffère de l'espace de noms que vous utilisez dans l’ensemble de votre code. En revanche, la valeur de l’espace de noms est utilisée comme identificateur unique pour ce contrat. Spécifier explicitement l'espace de noms empêche l'ajout au nom du contrat de la valeur d'espace de noms par défaut.

	```
	[ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
	public interface IEchoContract
	{
	}
	```

	>[AZURE.NOTE] En règle générale, l’espace de noms de contrat de service contient un schéma d’affectation de noms qui inclut des informations de version. L’inclusion des informations de version dans l’espace de noms de contrat de service permet aux services d’isoler les modifications majeures en définissant un contrat de service avec un nouvel espace de noms et en l’exposant sur un point de terminaison. De cette manière, les clients peuvent continuer à utiliser l’ancien contrat de service sans avoir à procéder à la mise à jour. Les informations de version peuvent se composer d’une date ou un numéro de version. Pour plus d’informations, consultez la rubrique [Contrôle de version des services](http://go.microsoft.com/fwlink/?LinkID=180498). Dans le cadre de ce didacticiel, le schéma d’affectation de noms de l’espace de noms de contrat de service ne contient pas les informations de version.

1. Dans l’interface `IEchoContract`, déclarez une méthode pour une seule opération que le contrat `IEchoContract` expose dans l’interface, puis appliquez l’attribut `OperationContractAttribute` à la méthode que vous souhaitez exposer dans le cadre du contrat Service Bus public.

	```
	[OperationContract]
	string Echo(string text);
	```

1. Directement après la définition de l’interface `IEchoContract`, déclarez un canal qui hérite à la fois de `IEchoContract` et de l’interface `IClientChannel`, comme indiqué ici :

	```
    public interface IEchoChannel : IEchoContract, IClientChannel { }
	```

	Un canal est l’objet WCF par le biais duquel l’hôte et le client se transmettent des informations. Par la suite , vous allez écrire du code par rapport au canal pour reprendre les informations entre les deux applications.

1. Dans le menu **Générer**, cliquez sur **Générer la solution** ou appuyez sur **Ctrl+Maj+B** pour confirmer si votre travail est correct jusqu’à présent.

### Exemple

Le code suivant montre une interface de base qui définit un contrat Service Bus.

```
using System;
using System.ServiceModel;

namespace Microsoft.ServiceBus.Samples
{
    [ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        String Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }

    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

Maintenant que l’interface est créée, vous pouvez implémenter l’interface.

## Implémenter le contrat WCF pour utiliser Service Bus

La création d’un Service Bus Relay nécessite la création au préalable du contrat défini à l’aide d’une interface. Pour plus d’informations sur la création de l’interface, consultez l’étape précédente. L'étape suivante consiste à implémenter l'interface. Cela implique la création d’une classe nommée `EchoService` qui met en œuvre l’interface `IEchoContract` définie par l’utilisateur. Une fois l’interface implémentée, vous configurez l’interface à l’aide d’un fichier de configuration App.config. Le fichier de configuration contient les informations nécessaires à l'application, notamment le nom du service, le nom du contrat et le type de protocole utilisé pour communiquer avec Service Bus. Le code utilisé pour effectuer ces tâches est fourni dans l'exemple suivant la procédure. Pour obtenir une description plus générale de la manière d’implémenter un contrat de service, voir [Implémentation de contrats de service](https://msdn.microsoft.com/library/ms733764.aspx) dans la documentation WCF.

1. Créez une classe nommée `EchoService` directement après la définition de l’interface `IEchoContract`. La classe `EchoService` implémente l’interface `IEchoContract`. 

	```
	class EchoService : IEchoContract
	{
	}
	```
	
	Comme pour d'autres implémentations d'interface, vous pouvez implémenter la définition dans un autre fichier. Toutefois, pour ce didacticiel, l’implémentation se situe dans le même fichier que la définition d’interface et la méthode `Main`.

1. Appliquez l’attribut [ServiceBehaviorAttribute](https://msdn.microsoft.com/library/system.servicemodel.servicebehaviorattribute.aspx) à l’interface `IEchoContract`. L’attribut spécifie le nom du service ainsi que son espace de noms. Ensuite, la classe `EchoService` apparaît comme suit :

	```
	[ServiceBehavior(Name = "EchoService", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
	class EchoService : IEchoContract
	{
	}
	```

1. Implémentez la méthode `Echo` définie dans l’interface `IEchoContract` dans la classe `EchoService`.

	```
	public string Echo(string text)
	{
    	Console.WriteLine("Echoing: {0}", text);
    	return text;
	}
	```

1. Cliquez sur **Générer**, puis sur **Générer la solution** pour confirmer que votre travail est correct.

### Pour définir la configuration de l’hôte de service

1. Le fichier de configuration est très similaire à un fichier de configuration WCF. Il contient le nom du service, le point de terminaison (c’est-à-dire l’emplacement que Service Bus expose aux clients et aux ordinateurs hôtes afin qu’ils communiquent entre eux) et la liaison (type de protocole utilisé pour communiquer). La principale différence réside dans le fait que le point de terminaison de service configuré fait référence à la liaison [NetTcpRelayBinding](https://msdn.microsoft.com/library/azure/microsoft.servicebus.nettcprelaybinding.aspx), qui ne fait pas partie de .NET Framework. [NetTcpRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.nettcprelaybinding.aspx) est l’une des liaisons définies par Service Bus.

1. Dans l’**Explorateur de solutions**, double-cliquez sur le fichier App.config pour l’ouvrir dans l’éditeur de Visual Studio.

2. Dans l’élément `<appSettings>`, remplacez les espaces réservés par le nom de votre espace de noms de service et par la clé SAP que vous avez copiée à l’étape précédente.

1. Dans les balises `<system.serviceModel>`, ajouter un élément `<services>`. Vous pouvez définir plusieurs applications Service Bus dans un même fichier de configuration. Toutefois, ce didacticiel n’en définit qu’un seul.
 
	```
	<?xmlversion="1.0"encoding="utf-8"?>
	<configuration>
	  <system.serviceModel>
	    <services>

	    </services>
	  </system.serviceModel>
	</configuration>
	```

1. Dans l’élément `<services>`, ajoutez un élément `<service>` pour définir le nom du service.

	```
	<service name="Microsoft.ServiceBus.Samples.EchoService">
	</service>
	```

1. Dans l’élément `<service>`, définissez l’emplacement du contrat de point de terminaison, ainsi que le type de liaison de ce dernier.

	```
	<endpoint contract="Microsoft.ServiceBus.Samples.IEchoContract" binding="netTcpRelayBinding"/>
	```

	Le point de terminaison définit l’emplacement où le client recherchera l’application hôte. Plus tard, le didacticiel utilise cette étape pour créer une URI qui expose entièrement l'hôte via Service Bus. La liaison déclare que nous utilisons TCP comme protocole pour communiquer avec Service Bus.

1. Dans le menu **Générer**, cliquez sur **Générer la solution** pour confirmer que votre travail est correct.

### Exemple

Le code suivant illustre l’implémentation du contrat de service.

```
[ServiceBehavior(Name = "EchoService", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]

    class EchoService : IEchoContract
    {
        public string Echo(string text)
        {
            Console.WriteLine("Echoing: {0}", text);
            return text;
        }
    }
```

L’exemple suivant montre le format de case du fichier App.config associé à l’hôte de service.

```
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <system.serviceModel>
    <services>
      <service name="Microsoft.ServiceBus.Samples.EchoService">
        <endpoint contract="Microsoft.ServiceBus.Samples.IEchoContract" binding="netTcpRelayBinding" />
      </service>
    </services>
    <extensions>
      <bindingExtensions>
        <add name="netTcpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetTcpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
      </bindingExtensions>
    </extensions>
  </system.serviceModel>
</configuration>
```

## Héberger et exécuter un service web de base pour s’enregistrer avec Service Bus

Cette étape explique comment exécuter un service Service Bus de base.

### Pour créer les informations d’identification de Service Bus

1. Dans `Main()`, créez deux variables dans lesquelles stocker l’espace de noms et la clé SAS qui sont lues à partir de la fenêtre de console.

	```
	Console.Write("Your Service Namespace: ");
	string serviceNamespace = Console.ReadLine();
	Console.Write("Your SAS key: ");
	string sasKey = Console.ReadLine();
	```

	La clé SAS sera utilisée ultérieurement pour accéder à votre projet Service Bus. L’espace de noms est transmis en tant que paramètre à `CreateServiceUri` pour créer une URI de service.

4. À l’aide d’un objet [TransportClientEndpointBehavior](https://msdn.microsoft.com/library/microsoft.servicebus.transportclientendpointbehavior.aspx), déclarez que vous utiliserez une clé SAS en tant que type d’informations d’identification. Ajoutez le code suivant directement après le code ajouté à l’étape précédente.

	```
	TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
	sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);
	```

### Création d’une adresse de base pour le service

1. Après le code ajouté à l’étape précédente, créez une instance `Uri` pour l’adresse de base du service. Cette URI spécifie le schéma Service Bus, l’espace de noms et le chemin d’accès de l’interface de service.

	```
	Uri address = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");
	```

	« sb » est une abréviation pour le modèle Service Bus et indique que nous utilisons TCP comme protocole. Cette information était précédemment indiquée dans le fichier de configuration, lorsque [NetTcpRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.nettcprelaybinding.aspx) a été spécifié en tant que liaison.
	
	Pour ce didacticiel, l’URI est `sb://putServiceNamespaceHere.windows.net/EchoService`.

### Création et configuration de l’hôte de service

1. Définissez le mode connectivité sur `AutoDetect`.

	```
	ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;
	```

	Le mode de connectivité décrit le protocole que le service utilise pour communiquer avec Service Bus ; HTTP ou TCP. Avec la valeur `AutoDetect` par défaut, le service tente de se connecter à Service Bus via TCP s’il est disponible et HTTP dans le cas contraire. Notez que cela diffère du protocole du service spécifié pour la communication client. Ce protocole est déterminé par la liaison utilisée. Par exemple, un service peut utiliser la liaison [BasicHttpRelayBinding](https://msdn.microsoft.com/library/microsoft.servicebus.basichttprelaybinding.aspx), qui indique que son point de terminaison (exposé dans Service Bus) communique avec les clients via HTTP. Ce même service peut spécifier **ConnectivityMode.AutoDetect** de manière à ce que le service communique avec Service Bus sur TCP.

1. Créez l’hôte de service, en utilisant l’URI créée précédemment dans cette section.

	```
	ServiceHost host = new ServiceHost(typeof(EchoService), address);
	```

	L’hôte de service est l’objet WCF qui instancie le service. Ici, vous transmettez le type de service que vous souhaitez créer (un type `EchoService`), ainsi que l’adresse à laquelle vous souhaitez exposer le service.

1. En haut du fichier Program.cs, ajoutez des références à [System.ServiceModel.Description](https://msdn.microsoft.com/library/system.servicemodel.description.aspx) et [Microsoft.ServiceBus.Description](https://msdn.microsoft.com/library/microsoft.servicebus.description.aspx).

	```
	using System.ServiceModel.Description;
	using Microsoft.ServiceBus.Description;
	```

1. De retour dans `Main()`, configurez le point de terminaison pour activer l’accès public.

	```
	IEndpointBehavior serviceRegistrySettings = new ServiceRegistrySettings(DiscoveryType.Public);
	```

	Cette étape informe Service Bus que votre application peut être trouvée publiquement en examinant le flux ATOM Service Bus pour votre projet. Si vous définissez **DiscoveryType** sur **privé**, un client est toujours en mesure d’accéder au service. Toutefois, le service n’apparaît pas lorsqu’il recherche l’espace de noms Service Bus. Au lieu de ça, le client doit connaître le chemin d’accès du point de terminaison au préalable.

1. Appliquer les informations d’identification de service aux points de terminaison de service définis dans le fichier App.config :

	```
	foreach (ServiceEndpoint endpoint in host.Description.Endpoints)
	{
	    endpoint.Behaviors.Add(serviceRegistrySettings);
	    endpoint.Behaviors.Add(sasCredential);
	}
	```

	Comme indiqué à l’étape précédente, il se peut que vous ayez déclaré plusieurs services et points de terminaison dans le fichier de configuration. Si c’est le cas, ce code traverse le fichier de configuration et recherche chaque point de terminaison auquel vos informations d’identification doivent être appliquées. Toutefois, pour ce didacticiel, le fichier de configuration n’a qu’un seul point de terminaison.

### Pour ouvrir l’hôte de service

1. Ouvrez le service.

	```
	host.Open();
	```

1. Informez l’utilisateur que le service est en cours d’exécution et expliquez comment arrêter le service.

	```
	Console.WriteLine("Service address: " + address);
	Console.WriteLine("Press [Enter] to exit");
	Console.ReadLine();
	```

1. Lorsque vous avez terminé, fermez l'hôte de service.

	```
	host.Close();
	```

1. Appuyez sur **Ctrl+Maj+B** pour générer le projet.

### Exemple

L’exemple suivant inclut le contrat de service et l’implémentation des étapes précédentes du didacticiel et héberge le service dans une application de console. Compilez le code qui suit dans un fichier exécutable nommé EchoService.exe.

```
using System;
using System.ServiceModel;
using System.ServiceModel.Description;
using Microsoft.ServiceBus;
using Microsoft.ServiceBus.Description;

namespace Microsoft.ServiceBus.Samples
{
    [ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        String Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { };

    [ServiceBehavior(Name = "EchoService", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    class EchoService : IEchoContract
    {
        public string Echo(string text)
        {
            Console.WriteLine("Echoing: {0}", text);
            return text;
        }
    }

    class Program
    {
        static void Main(string[] args)
        {

            ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;         
          
            Console.Write("Your Service Namespace: ");
            string serviceNamespace = Console.ReadLine();
            Console.Write("Your SAS key: ");
            string sasKey = Console.ReadLine();

           // Create the credentials object for the endpoint.
            TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
            sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);

            // Create the service URI based on the service namespace.
            Uri address = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");

            // Create the service host reading the configuration.
            ServiceHost host = new ServiceHost(typeof(EchoService), address);

            // Create the ServiceRegistrySettings behavior for the endpoint.
            IEndpointBehavior serviceRegistrySettings = new ServiceRegistrySettings(DiscoveryType.Public);

            // Add the Service Bus credentials to all endpoints specified in configuration.
            foreach (ServiceEndpoint endpoint in host.Description.Endpoints)
            {
                endpoint.Behaviors.Add(serviceRegistrySettings);
                endpoint.Behaviors.Add(sasCredential);
            }
            
            // Open the service.
            host.Open();

            Console.WriteLine("Service address: " + address);
            Console.WriteLine("Press [Enter] to exit");
            Console.ReadLine();

            // Close the service.
            host.Close();
        }
    }
}
```

## Créer un client WCF pour le contrat de service

L’étape suivante consiste à créer une application client Service Bus de base et à définir le contrat de service que vous allez implémenter au cours des étapes ultérieures. Notez que plusieurs de ces étapes ressemblent aux étapes utilisées pour créer un service : définition d’un contrat, modification d’un fichier App.config à l’aide des informations d’identification servant à se connecter à Service Bus et ainsi de suite. Le code utilisé pour effectuer ces tâches est fourni dans l'exemple suivant la procédure.

1. Créer un nouveau projet dans la solution Visual Studio en cours pour le client en procédant comme suit :
	1. Dans l’Explorateur de solutions, dans la solution qui contient le service, cliquez avec le bouton droit de la solution actuelle (et non sur le projet), puis cliquez sur **Ajouter**. Puis cliquez sur **Nouveau projet**.
	2. Dans la boîte de dialogue **Ajouter un nouveau projet**, cliquez sur **Visual C#** (si **Visual C#** n’apparaît pas, regardez sous **Autres langages**), sélectionnez le modèle **Application de console** et nommez-le **EchoClient**.
	3. Cliquez sur **OK**. <br />

1. Dans l’Explorateur de solutions, double-cliquez sur le fichier Program.cs dans le projet **EchoClient** pour l’ouvrir dans l’éditeur, si celui-ci n’est pas déjà ouvert.

1. Remplacez le nom par défaut de l'espace de noms `EchoClient` par `Microsoft.ServiceBus.Samples`.

1. Installez le [package NuGet Service Bus](https://www.nuget.org/packages/WindowsAzure.ServiceBus). Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le projet **EchoClient**, puis cliquez sur **Gérer les packages NuGet**. Cliquez sur l’onglet **Parcourir**, puis recherchez `Microsoft Azure Service Bus`. Cliquez sur **Installer** et acceptez les conditions d’utilisation.

	![][3]

1. Ajoutez une instruction `using` pour l’espace de noms [System.ServiceModel](https://msdn.microsoft.com/library/system.servicemodel.aspx) dans le fichier Program.cs.

	```
	using System.ServiceModel;
	```

1. Ajoutez la définition de contrat de service à l’espace de noms, comme illustré dans l’exemple suivant. Notez que cette définition est identique à celle qui est utilisée dans le projet **Service**. Vous devez ajouter ce code en haut de l’espace de noms `Microsoft.ServiceBus.Samples`.

	```
	[ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
	public interface IEchoContract
	{
	    [OperationContract]
	    string Echo(string text);
	}

	public interface IEchoChannel : IEchoContract, IClientChannel { }
	```

1. Appuyez sur **Ctrl+Maj+B** pour générer le client.

### Exemple

Le code suivant montre l’état actuel du fichier Program.cs dans le projet EchoClient.

```
using System;
using Microsoft.ServiceBus;
using System.ServiceModel;

namespace Microsoft.ServiceBus.Samples
{

	[ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        string Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }


    class Program
    {
        static void Main(string[] args)
        {
        }
    }
}
```

## Configurer le client WCF

Dans cette étape, vous allez créer une application cliente de base qui accède au service que vous avez créé précédemment dans ce didacticiel. Ce fichier App.config définit le contrat, la liaison et le nom du point de terminaison. Le code utilisé pour effectuer ces tâches est fourni dans l'exemple suivant la procédure.

1. Dans l’Explorateur de solutions, dans le projet **EchoClient**, double-cliquez sur **App.config** pour ouvrir le fichier dans l’éditeur de Visual Studio.

2. Dans l’élément `<appSettings>`, remplacez les espaces réservés par le nom de votre espace de noms de service et par la clé SAP que vous avez copiée à l’étape précédente.

1. Dans l’élément system.serviceModel, ajoutez un élément `<client>`.

	```
	<?xmlversion="1.0"encoding="utf-8"?>
	<configuration>
	  <system.serviceModel>
	    <client>
	    </client>
	  </system.serviceModel>
	</configuration>
	```

	Cette étape montre que vous définissez une application client de type WCF.

1. Dans l ’élément `client`, définissez le nom, le contrat et le type de liaison du point de terminaison.

	```
	<endpoint name="RelayEndpoint"
					contract="Microsoft.ServiceBus.Samples.IEchoContract"
					binding="netTcpRelayBinding"/>
	```

	Cette étape définit le nom du point de terminaison, le contrat défini dans le service et le fait que l’application cliente utilise TCP pour communiquer avec Service Bus. Le nom de point de terminaison est utilisé dans l’étape suivante pour associer cette configuration de point de terminaison à l’URI de service.

1. Cliquez sur **fichier**, puis **Tout enregistrer**.

## Exemple

Le code suivant montre le fichier App.config correspondant au client Echo.

```
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <system.serviceModel>
    <client>
      <endpoint name="RelayEndpoint"
                      contract="Microsoft.ServiceBus.Samples.IEchoContract"
                      binding="netTcpRelayBinding"/>
    </client>
    <extensions>
      <bindingExtensions>
        <add name="netTcpRelayBinding"
                    type="Microsoft.ServiceBus.Configuration.NetTcpRelayBindingCollectionElement, Microsoft.ServiceBus, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
      </bindingExtensions>
    </extensions>
  </system.serviceModel>
</configuration>
```

## Implémenter le client WCF pour appeler Service Bus

Dans cette étape, vous allez mettre en oeuvre une application cliente de base qui accède au service que vous avez créé précédemment dans ce didacticiel. Comme le service, le client effectue de nombreuses opérations similaires pour accéder à Service Bus :

1. Définit le mode connectivité sur.
1. Crée l’URI qui localise le service hôte.
1. Définit les informations d’identification de sécurité.
1. Applique les informations d’identification à la connexion.
1. Ouvre la connexion.
1. Effectue les tâches spécifiques à l’application.
1. Ferme la connexion.

Toutefois, une des principales différences réside dans le fait que l’application cliente utilise un canal pour se connecter à Service Bus, tandis que le service utilise un appel à **ServiceHost**. Le code utilisé pour effectuer ces tâches est fourni dans l'exemple suivant la procédure.

### Pour implémenter une application cliente

1. Définissez le mode connectivité sur **AutoDetect**. Ajoutez le code suivant à la méthode `Main()` de l’application **EchoClient**.

	```
	ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;
	```

1. Définissez des variables pour stocker les valeurs de l’espace de noms de service et la clé SAS qui sont lues depuis la console.

	```
	Console.Write("Your Service Namespace: ");
	string serviceNamespace = Console.ReadLine();
	Console.Write("Your SAS Key: ");
	string sasKey = Console.ReadLine();
	```

1. Créez l’URI qui définit l’emplacement de l’hôte dans votre projet de Service Bus.

	```
	Uri serviceUri = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");
	```

1. Créez l’objet d’informations d’identification de votre point de terminaison d’espace de noms du service.

	```
	TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
	sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);
	```

1. Création de la structure de canaux qui charge la configuration décrite dans le fichier App.config.

	```
	ChannelFactory<IEchoChannel> channelFactory = new ChannelFactory<IEchoChannel>("RelayEndpoint", new EndpointAddress(serviceUri));
	```

	Une structure de canaux est un objet WCF qui crée un canal via lequel les services et les applications clients communiquent.

1. Appliquez les informations d’identification Service Bus

	```
	channelFactory.Endpoint.Behaviors.Add(sasCredential);
	```

1. Créer et ouvrir le canal au service.

	```
	IEchoChannel channel = channelFactory.CreateChannel();
	channel.Open();
	```

1. Écrire l’interface utilisateur de base et les fonctionnalités pour l’écho.

	```
	Console.WriteLine("Enter text to echo (or [Enter] to exit):");
	string input = Console.ReadLine();
	while (input != String.Empty)
	{
	    try
	    {
	        Console.WriteLine("Server echoed: {0}", channel.Echo(input));
	    }
	    catch (Exception e)
	    {
	        Console.WriteLine("Error: " + e.Message);
	    }
	    input = Console.ReadLine();
	}
	```

	Notez que le code utilise l’instance de l’objet de canal en tant que proxy pour le service.

1. Fermez le canal ainsi que la structure.

	```
	channel.Close();
	channelFactory.Close();
	```

## Pour exécuter les applications

1. Appuyez sur **Ctrl+Maj+B** pour générer la solution. Ceci génère le projet client et le projet de service que vous avez créés aux étapes précédentes.

2. Avant d’exécuter l’application cliente, assurez-vous que l’application de service est en cours d’exécution. Dans l’Explorateur de solutions dans Visual Studio, cliquez sur la solution **EchoService**, puis sur **Propriétés**.

3. Dans la boîte de dialogue Propriétés de la solution, cliquez sur **Projet de démarrage**, puis sur le bouton **Plusieurs projets de démarrage**. Assurez-vous que **EchoService** apparaît en premier dans la liste.

4. Réglez le champ **Action** sur **Démarrer** pour les projets **EchoService** et **EchoClient**.

	![][5]

5. Cliquez sur **Dépendances du projet**. Dans le champ **Projets**, sélectionnez **EchoClient**. Dans le champ **Dépend de**, assurez-vous que l’option **EchoService** est activée.

	![][6]

6. Cliquez sur **OK** pour fermer la boîte de dialogue **Propriétés**.

7. Appuyez sur **F5** pour exécuter les deux projets.

8. Les deux fenêtres de console s’ouvrent et vous invitent à renseigner le nom de l’espace de noms. Le service doit d’abord s’exécuter. Dans la fenêtre de console **EchoService**, entrez l’espace de noms, puis appuyez sur **Entrée**.

9. Ensuite, vous êtes invité à fournir votre clé SAS. Entrez la clé SAS et appuyez sur ENTRÉE.

	Voici un exemple de sortie de la fenêtre de console. Notez que les valeurs indiquées ici sont fournies à titre d’exemple uniquement.

	`Your Service Namespace: myNamespace` `Your SAS Key: <SAS key value>`

	L’application de service imprime l’adresse sur laquelle il écoute dans la fenêtre de console, comme indiqué dans l’exemple suivant.

    `Service address: sb://mynamespace.servicebus.windows.net/EchoService/` `Press [Enter] to exit`
    
10. Dans la fenêtre de console **EchoClient**, entrez les mêmes informations que vous avez entrées précédemment pour l’application de service. Suivez les étapes précédentes pour saisir des valeurs identiques pour l’espace de noms de service et la clé SAP pour l’application cliente

11. Après avoir saisi ces valeurs, le client ouvre un canal au service et vous invite à saisir du texte, comme indiqué dans l’exemple de sortie de console suivante.

	`Enter text to echo (or [Enter] to exit):`

	Saisissez du texte à envoyer à l’application de service, puis appuyez sur Entrée. Ce texte est envoyé au service via l’opération de service Echo et apparaît dans la fenêtre de console de service comme dans l’exemple de sortie suivant.

	`Echoing: My sample text`

	L’application cliente reçoit la valeur de retour de l’opération `Echo`, qui est le texte d’origine, et l’imprime sur la fenêtre de console. Voici un exemple de sortie de la fenêtre de console du client.

	`Server echoed: My sample text`

12. Vous pouvez continuer d’envoyer des messages texte du client au service de cette manière. Une fois que vous avez terminé, appuyez sur Entrée dans les fenêtres du client et de la console de service pour arrêter les des deux applications.

## Exemple

L’exemple qui suit montre comment créer une application client, comment appeler les opérations du service et comment fermer le client une fois l’appel d’opération terminé.

```
using System;
using Microsoft.ServiceBus;
using System.ServiceModel;

namespace Microsoft.ServiceBus.Samples
{
    [ServiceContract(Name = "IEchoContract", Namespace = "http://samples.microsoft.com/ServiceModel/Relay/")]
    public interface IEchoContract
    {
        [OperationContract]
        String Echo(string text);
    }

    public interface IEchoChannel : IEchoContract, IClientChannel { }

    class Program
    {
        static void Main(string[] args)
        {
            ServiceBusEnvironment.SystemConnectivity.Mode = ConnectivityMode.AutoDetect;

            
            Console.Write("Your Service Namespace: ");
            string serviceNamespace = Console.ReadLine();
            Console.Write("Your SAS Key: ");
            string sasKey = Console.ReadLine();



            Uri serviceUri = ServiceBusEnvironment.CreateServiceUri("sb", serviceNamespace, "EchoService");

            TransportClientEndpointBehavior sasCredential = new TransportClientEndpointBehavior();
            sasCredential.TokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider("RootManageSharedAccessKey", sasKey);

            ChannelFactory<IEchoChannel> channelFactory = new ChannelFactory<IEchoChannel>("RelayEndpoint", new EndpointAddress(serviceUri));

            channelFactory.Endpoint.Behaviors.Add(sasCredential);

            IEchoChannel channel = channelFactory.CreateChannel();
            channel.Open();

            Console.WriteLine("Enter text to echo (or [Enter] to exit):");
            string input = Console.ReadLine();
            while (input != String.Empty)
            {
                try
                {
                    Console.WriteLine("Server echoed: {0}", channel.Echo(input));
                }
                catch (Exception e)
                {
                    Console.WriteLine("Error: " + e.Message);
                }
                input = Console.ReadLine();
            }

            channel.Close();
            channelFactory.Close();

        }
    }
}
```

## Étapes suivantes

Ce didacticiel vous a montré comment créer une application cliente et un service Service Bus à l’aide des fonctionnalités « relais ». Pour obtenir un didacticiel similaire utilisant la [messagerie répartie](service-bus-messaging-overview.md#Brokered-messaging) Service Bus, consultez le [Didacticiel .NET sur la messagerie répartie Service Bus](service-bus-brokered-tutorial-dotnet.md).

Pour en savoir plus sur Service Bus, consultez les rubriques qui suivent.

- [Présentation de la messagerie Service Bus](service-bus-messaging-overview.md)
- [Concepts de base de Service Bus](service-bus-fundamentals-hybrid-solutions.md)
- [Architecture de Service Bus](service-bus-architecture.md)

[portail Azure Classic]: http://manage.windowsazure.com

[1]: ./media/service-bus-relay-tutorial/service-bus-policies.png
[2]: ./media/service-bus-relay-tutorial/create-console-app.png
[3]: ./media/service-bus-relay-tutorial/install-nuget.png
[4]: ./media/service-bus-relay-tutorial/create-ns.png
[5]: ./media/service-bus-relay-tutorial/set-projects.png
[6]: ./media/service-bus-relay-tutorial/set-depend.png

<!---HONumber=AcomDC_0601_2016-->