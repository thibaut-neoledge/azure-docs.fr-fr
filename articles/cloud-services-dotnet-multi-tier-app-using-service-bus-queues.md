<properties linkid="dev-net-e2e-multi-tier" urlDisplayName="Multi-Tier Application" pageTitle=".NET Multi-Tier Application - Azure Tutorial" metaKeywords="Azure Service Bus queue tutorial, Azure queue tutorial, Azure worker role tutorial, Azure .NET queue tutorial, Azure C# queue tutorial, Azure C# worker role tutorial" description="A tutorial that helps you develop a multi-tier app in Azure that uses Service Bus queues to communicate between tiers. Samples in .NET." metaCanonical="" services="cloud-services,service-bus" documentationCenter=".NET" title=".NET Multi-Tier Application Using Service Bus Queues" authors="sethm" solutions="" manager="timlt" editor="mattshel" />

<tags ms.service="service-bus" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="09/15/2014" ms.author="sethm" />

# Application multiniveau .NET avec les files d'attente Service Bus

Le développement pour Azure est simple grâce à Visual Studio 2013 et au
Kit de développement logiciel (SDK) Azure pour .NET gratuit. Si vous n'avez pas déjà installé Visual
Studio 2013, le Kit de développement logiciel (SDK) va automatiquement installer Visual Studio Express 2013 pour que vous
puissiez commencer à développer gratuitement pour Azure. Ce guide part du principe que vous n'avez pas d'expérience en tant
qu'utilisateur de Microsoft Azure. Une fois que vous aurez terminé ce guide, vous disposerez d'une
application qui utilise plusieurs ressources Azure qui s'exécutent
dans votre environnement local et qui illustrent le fonctionnement d'une application multiniveau.

Vous apprendrez à effectuer les opérations suivantes :

-   configuration de votre ordinateur pour le développement Azure
    avec un seul téléchargement et une seule installation ;
-   utilisation de Visual Studio pour développer pour Azure ;
-   création d'une application multiniveau dans Azure avec les
    rôles Web et les rôles de travail ;
-   communication entre niveaux à l'aide des files d'attente Service Bus.

[WACOM.INCLUDE [create-account-note](../includes/create-account-note.md)]

Dans ce didacticiel, vous allez générer et exécuter l'application multiniveau dans Azure Cloud Services. Le composant frontal sera un rôle Web ASP.NET MVC et le composant principal sera un rôle de travail. Vous pourriez créer la même application multiniveau avec le composant frontal comme projet Web à déployer sur un site Web Azure au lieu d'un service cloud. Pour obtenir des instructions sur les différences à appliquer pour le composant frontal d'un site Web Azure, consultez la section [Étapes suivantes][Étapes suivantes].

Voici une capture d'écran de l'application terminée :

![][0]

**Remarque** : Azure offre également la fonction de file d'attente de stockage. Pour plus d'informations sur les files d'attente de stockage Azure et les files d'attente Service Bus, consultez la page [Files d'attente Windows Azure et files d'attente Windows Azure Service Bus - comparaison et différences][Files d'attente Windows Azure et files d'attente Windows Azure Service Bus - comparaison et différences].

## <span class="short-header">Communication entre rôles</span>Présentation du scénario : communication entre rôles

Pour envoyer une commande en traitement, le composant frontal d'interface utilisateur,
qui s'exécute dans le rôle Web, doit pouvoir communiquer avec
la logique de niveau central qui s'exécute dans le rôle de travail. Cet exemple utilise la messagerie répartie Service Bus pour
la communication entre les niveaux.

L'utilisation de la messagerie répartie entre les niveaux Web et central découple les
deux composants. À l'inverse de la messagerie directe (TCP ou HTTP),
le niveau Web ne se connecte pas directement au niveau central.
En effet, il envoie des unités de travail, sous forme de messages, à Service Bus,
qui les retient jusqu'à ce que le niveau central soit prêt à les traiter.

Service Bus contient deux entités pour prendre en charge la messagerie répartie :
les files d'attente et les rubriques. Avec les files d'attente, chaque message envoyé
à la file d'attente est utilisé par un seul destinataire. Les rubriques prennent en charge le modèle
de publication/d'abonnement dans lequel chaque message publié
est mis à disposition de tous les abonnements inscrits auprès de la rubrique. Chaque abonnement gère de façon
logique sa propre file de messages. Les abonnements peuvent également
être configurés avec des règles de filtrage, qui limitent les messages
transmis à la file d'attente d'abonnement à ceux correspondant aux critères du filtre. Cet exemple utilise les files
d'attente Service Bus.

![][1]

Ce mécanisme de communication présente plusieurs avantages par rapport
à la messagerie directe :

-   **Découplage temporel.** Avec le modèle de messagerie asynchrone,
    les producteurs et les consommateurs n'ont pas besoin d'être en ligne en même temps. Service Bus
    stocke de manière fiable les messages jusqu'à ce que le destinataire
    soit prêt à les recevoir. Ceci permet aux composants de l'application distribuée
    d'être déconnectés, soit volontairement, par exemple pour des raisons
    de maintenance, soit en raison de l'échec d'un composant,
    sans que le système dans sa globalité soit affecté. De plus, l'application qui utilise les messages n'a besoin
    d'être en ligne que quelques fois par jour.

-   **Niveau de charge**. Dans de nombreuses applications, la charge système
    varie dans le temps, alors que le temps de traitement nécessaire
    à chaque élément de travail est normalement constant. L'ajout d'une file d'attente entre les producteurs et les consommateurs
    des messages fait que l'application de destination (le rôle de travail) n'a besoin
    d'être configurée que pour une charge de travail moyenne,
    plutôt que pour une charge de travail maximale. La file d'attente s'allonge et se raccourcit en fonction de la charge
    entrante. Ceci permet de faire des économies en termes d'infrastructures
    nécessaires pour faire face à la charge de travail de l'application.

-   **Équilibrage de la charge.** À mesure que la charge augmente, d'autres processus de travail
    peuvent être ajoutés pour lire les éléments de la file d'attente. Chaque message est traité par un seul des
    processus de travail. De plus, cet équilibrage de la charge basé sur
    l'extraction permet d'optimiser l'utilisation des ordinateurs de travail,
    même si ceux-ci diffèrent en termes de puissance de traitement,
    car ils demandent les messages au maximum de leur capacité. Ce modèle est souvent appelé
    modèle consommateur concurrent.

    ![][2]

Les sections qui suivent présentent le code de mise en œuvre
de cette architecture.

## <span class="short-header">Configuration de l’environnement</span>Configuration de l’environnement de développement

Avant de commencer à développer votre application Azure,
vous devez obtenir des outils et configurer votre environnement de développement.

1.  Pour installer le Kit de développement logiciel (SDK) Azure pour .NET, cliquez sur le bouton ci-dessous :

    [Obtenir les outils et le Kit de développement logiciel (SDK)][Obtenir les outils et le Kit de développement logiciel (SDK)]

2.  Cliquez sur **Install the SDK**.

3.  Choisissez le lien correspondant à votre version de Visual Studio. Les étapes de ce didacticiel utilisent Visual Studio 2013 :

    ![][3]

4.  Lorsque vous êtes invité à exécuter ou à enregistrer le fichier d'installation, cliquez sur
    **Exécuter** :

    ![][4]

5.  Dans Web Platform Installer, cliquez sur **Installer**, puis poursuivez l'installation :

    ![][5]

6.  Une fois l'installation terminée, vous disposez de tous les éléments
    nécessaires pour commencer le développement. Le Kit de développement logiciel (SDK)
    comprend des outils qui vous permettent de facilement développer des applications Azure dans Visual Studio. Si Visual Studio
    n'est pas installé, Visual Studio Express pour Web
    est installé gratuitement.

## <span class="short-header">Configuration de l'espace de noms</span>Configuration de l'espace de noms Service Bus

L'étape suivante consiste à créer l'espace de noms de service et à obtenir
une clé secrète partagée. L'espace de noms de service offre une limite à chaque application
exposée via Service Bus. Une clé secrète partagée est automatiquement
générée par le système lors de la création d'un espace de
noms de service. La combinaison de l'espace de noms de service et de la clé secrète
partagée fournit à Service Bus une information d'identification permettant
d'authentifier l'accès à une application.

Notez que vous pouvez également gérer les espaces de noms et les entités de messagerie Service Bus à l'aide de l'explorateur de serveurs Visual Studio, mais vous ne pouvez créer d'espaces de noms que depuis le portail.

### Configuration de l'espace de noms avec le portail de gestion

1.  Connectez-vous au [portail de gestion Azure][portail de gestion Azure].

2.  Dans le volet de navigation gauche du portail de gestion, cliquez sur
    **Service Bus**.

3.  Dans le volet inférieur du portail de gestion, cliquez sur **Créer**.

    ![][6]

4.  Dans la boîte de dialogue **Ajouter un nouvel espace de noms**, entrez un nom d'espace de noms.
    Le système vérifie immédiatement la disponibilité du nom.
   
    ![][7]

5.  Après vous être assuré de la disponibilité du nom de l'espace de noms,
    choisissez le pays ou la région où votre espace de noms doit être hébergé
    (veillez à utiliser le même pays ou la même région que celui ou celle
    où vous déployez vos ressources de calcul).

    IMPORTANT : choisissez la **même région** que celle que vous prévoyez
    de sélectionner pour le déploiement de votre application. Vous bénéficiez ainsi des meilleures performances.

6.  Cliquez sur la coche. Le système crée l'espace de
    noms de service et l'active. Vous devrez peut-être attendre plusieurs minutes
    afin que le système approvisionne des ressources pour votre compte.

    ![][8]

7.  Dans la fenêtre principale, cliquez sur le nom de votre espace de noms de service.

    ![][9]

8.  Cliquez sur **Connection Information**.

    ![][10]

9.  Dans le volet **Access connection information**, recherchez les valeurs **Default Issuer** et **Default Key**.

10. Notez la clé ou copiez-la dans le Presse-papiers.

### Gestion des espaces de noms et des entités de messagerie avec l'explorateur de serveurs Visual Studio

Pour gérer un espace de noms et obtenir des informations de connexion en utilisant Visual Studio plutôt que le portail de gestion, suivez la procédure décrite [ici][ici], dans la section intitulée **Connexion à Azure à partir de Visual Studio**. Lorsque vous vous connectez à Azure, le nœud **Service Bus** sous l’arborescence **Microsoft Azure** dans l’Explorateur de serveurs est automatiquement rempli par les espaces de noms que vous avez déjà créés. Cliquez avec le bouton droit sur un espace de noms, puis cliquez sur **Properties** pour que la chaîne de connexion et les autres métadonnées associées à cet espace de noms s’affichent dans le volet **Properties** de Visual Studio.

Notez la valeur **SharedAccessKey** ou copiez-la dans le Presse-papiers :

![][11]

## <span class="short-header">Création d'un rôle Web</span>Création d'un rôle Web

Dans cette section, vous allez générer le composant frontal de votre application. Vous allez
d'abord créer les différentes pages affichées par votre application.
Ensuite, vous allez ajouter le code permettant d'envoyer les éléments à une file d'attente
Service Bus et d'afficher les informations d'état de la file d'attente.

### Création du projet

1.  Avec les privilèges d'administrateur, démarrez Microsoft Visual Studio 2013
    ou Microsoft Visual Studio Express. Pour démarrer Visual Studio
    avec les privilèges d'administrateur, cliquez avec le bouton droit sur **Microsoft Visual
    Studio 2013 (ou Microsoft Visual Studio Express)**,
    puis cliquez **Exécuter en tant qu'administrateur**. L'émulateur de calcul Azure,
    présenté plus loin dans ce guide, nécessite que Visual Studio
    soit démarré avec les privilèges d'administrateur.

    Dans Visual Studio, dans le menu **Fichier**, cliquez sur **Nouveau**, puis
    sur **Projet**.

    ![][12]

2.  Dans **Modèles installés**, sous **Visual C#**, cliquez sur **Cloud**,
    puis sur **Azure Cloud Service**. Nommez ce projet
    **MultiTierApp**. Cliquez ensuite sur **OK**.

    ![][13]

3.  Dans les rôles **.NET Framework 4.5**, double-cliquez sur **Rôle
    Web ASP.NET**.

    ![][14]

4.  Passez la souris sur **WebRole1** sous **Azure Cloud Service solution**, cliquez
    sur l'icône en forme de crayon et renommez le rôle Web **FrontendWebRole**. Cliquez ensuite sur **OK**. (Entrez bien « Frontend » avec un « e » minuscule, et non « FrontEnd ».)

    ![][15]

5.  Dans la boîte de dialogue **New ASP.NET Project**, dans la liste **Sélectionner un modèle**, cliquez sur **MVC**,
    puis sur **OK**.

    ![][16]

6.  Dans **Explorateur de solutions**, cliquez avec le bouton droit sur **Références**, puis cliquez sur
    **Gérer les packages NuGet...** ou
    **Ajouter une référence au package de bibliothèques**.

7.  Sélectionnez **En ligne** dans la partie gauche de la boîte de dialogue. Recherchez
    « **WindowsAzure** » et sélectionnez l'élément **Azure Service
    Bus**. Ensuite, terminez l'installation et fermez cette boîte de dialogue.

    ![][17]

8.  Notez que les assemblys client nécessaires sont maintenant référencés et que
    certains nouveaux fichiers de code ont été ajoutés.

9.  Dans **Explorateur de solutions**, cliquez avec le bouton droit sur **Modèles**, cliquez sur **Ajouter**,
    puis sur **Classe**. Dans la zone Name, tapez le nom
    **OnlineOrder.cs**. Cliquez ensuite sur **Add**.

### Écriture du code de votre rôle Web

Dans cette section, vous allez créer les différentes pages affichées par
votre application.

1.  Dans le fichier **OnlineOrder.cs** dans Visual Studio, remplacez
    la définition d'espace de noms existante par le code suivant :

        namespace FrontendWebRole.Models
        {
            public class OnlineOrder
            {
                public string Customer { get; set; }
                public string Product { get; set; }
            }
        }

2.  Dans **Explorateur de solutions**, double-cliquez sur
    **Controllers\\HomeController.cs**. Ajoutez les instructions **using**
    suivantes au début du fichier pour inclure les espaces de noms
    au modèle que vous venez de créer, ainsi que Service Bus :

        using FrontendWebRole.Models;
        using Microsoft.ServiceBus.Messaging;
        using Microsoft.ServiceBus;

3.  Également dans le fichier **HomeController.cs** dans Visual Studio,
    remplacez la définition d'espace de noms existante par le code suivant. Ce code
    contient des méthodes pour gérer l'envoi d'éléments dans la file d'attente :

        namespace FrontendWebRole.Controllers
        {
            public class HomeController : Controller
            {
                public ActionResult Index()
                {
                    // Simply redirect to Submit, since Submit will serve as the
                    // front page of this application
                    return RedirectToAction("Submit");
                }

                public ActionResult About()
                {
                    return View();
                }

                // GET: /Home/Submit
                // Controller method for a view you will create for the submission
                // form
                public ActionResult Submit()
                {
                    // Will put code for displaying queue message count here.

                    return View();
                }

                // POST: /Home/Submit
                // Controller method for handling submissions from the submission
                // form 
                [HttpPost]
                // Attribute to help prevent cross-site scripting attacks and 
                // cross-site request forgery  
                [ValidateAntiForgeryToken] 
                public ActionResult Submit(OnlineOrder order)
                {
                    if (ModelState.IsValid)
                    {
                        // Will put code for submitting to queue here.

                        return RedirectToAction("Submit");
                    }
                    else
                    {
                        return View(order);
                    }
                }
            }
        }

4.  Dans le menu **Build**, cliquez sur **Build Solution**.

5.  Maintenant, vous allez créer l'affichage de la méthode **Submit()** que vous
    avez créée plus haut. Cliquez avec le bouton droit sur la méthode Submit() et choisissez
    **Add View**

    ![][18]

6.  La boîte de dialogue qui s'affiche permet de créer l'affichage. Sélectionnez la classe
    **OnlineOrder** dans la liste déroulante **Model class** et 
    **Create** dans la liste déroulante **Template**.

    ![][19]

7.  Cliquez sur **Add**.

8.  Maintenant, vous allez modifier le nom d'affichage de votre application. Dans
    **Explorateur de solutions**, double-cliquez sur le fichier
    **Views\Shared\_Layout.cshtml** pour l'ouvrir dans
    l'éditeur de Visual Studio.

9.  Remplacez toutes les occurrences de **Application ASP.NET MVC** par
    **LITWARE'S Awesome Products**.

10. Remplacez **"your logo here"** par **LITWARE'S Awesome Products** :

    ![][20]

11. Supprimez les liens **Home**, **About** et **Contact**. Supprimez le code en surbrillance :

    ![][21]

12. Enfin, modifiez la page d'envoi pour inclure des informations sur la
    file d'attente. Dans **Explorateur de solutions**, double-cliquez sur le fichier
    **Views\Home\Submit.cshtml** pour l'ouvrir dans
    l'éditeur de Visual Studio. Ajoutez la ligne suivante après **\<h2\>Submit\</h2\>**. Pour le moment,
    **ViewBag.MessageCount** est vide. Vous le remplirez plus tard.

        <p>Current Number of Orders in Queue Waiting to be Processed: @ViewBag.MessageCount</p>

13. Vous avez maintenant implémenté votre interface utilisateur. Vous pouvez appuyer sur **F5** pour exécuter
    votre application et vérifier qu'elle apparaît bien comme vous le souhaitez.

    ![][22]

### Écriture de code pour l'envoi d'éléments dans une file d'attente Service Bus

Maintenant, vous allez ajouter le code pour envoyer des éléments dans une file d'attente. Vous allez d'abord
créer une classe qui contient les informations de connexion
à votre file d'attente Service Bus. Ensuite, vous allez initialiser votre connexion à partir de
**Global.aspx.cs**. Enfin, vous allez mettre à jour le code d'envoi que vous
avez créé précédemment dans **HomeController.cs** pour qu'il envoie réellement les éléments dans
une file d'attente Service Bus.

1.  Dans Explorateur de solutions, cliquez avec le bouton droit sur **FrontendWebRole** (sur le projet, et non sur le rôle). Cliquez sur **Add**, puis sur **Class**.

2.  Donnez le nom **QueueConnector.cs** à la classe. Cliquez sur **Add** pour créer la classe.

3.  Vous allez maintenant coller le code qui encapsule vos informations
    de connexion et qui contient les méthodes permettant d'initialiser
    la connexion à une file d'attente Service Bus. Dans QueueConnector.cs, collez le code suivant et entrez les valeurs
    **Namespace**, **IssuerName** et **IssuerKey**. Vous pouvez les obtenir dans le
    [portail de gestion][portail de gestion Azure] ou dans l'Explorateur de serveurs de Visual Studio, sous le nœud **Service Bus**.

        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Web;
        using Microsoft.ServiceBus.Messaging;
        using Microsoft.ServiceBus;

        namespace FrontendWebRole
        {
            public static class QueueConnector
            {
                // Thread-safe. Recommended that you cache rather than recreating it
                // on every request.
                public static QueueClient OrdersQueueClient;

                // Obtain these values from the Management Portal
                public const string Namespace = "your service bus namespace";
                public const string IssuerName = "issuer name";
                public const string IssuerKey = "issuer key";

                // The name of your queue
                public const string QueueName = "OrdersQueue";

                public static NamespaceManager CreateNamespaceManager()
                {
                    // Create the namespace manager which gives you access to
                    // management operations
                    var uri = ServiceBusEnvironment.CreateServiceUri(
                        "sb", Namespace, String.Empty);
                    var tP = TokenProvider.CreateSharedSecretTokenProvider(
                        IssuerName, IssuerKey);
                    return new NamespaceManager(uri, tP);
                }

                public static void Initialize()
                {
                    // Using Http to be friendly with outbound firewalls
                    ServiceBusEnvironment.SystemConnectivity.Mode = 
                        ConnectivityMode.Http;

                    // Create the namespace manager which gives you access to 
                    // management operations
                    var namespaceManager = CreateNamespaceManager();

                    // Create the queue if it does not exist already
                    if (!namespaceManager.QueueExists(QueueName))
                    {
                        namespaceManager.CreateQueue(QueueName);
                    }

                    // Get a client to the queue
                    var messagingFactory = MessagingFactory.Create(
                        namespaceManager.Address, 
                        namespaceManager.Settings.TokenProvider);
                    OrdersQueueClient = messagingFactory.CreateQueueClient(
                        "OrdersQueue");
                }
            }
        }

4.  Maintenant, vous allez vous assurer que votre méthode **Initialize** est bien appelée. Dans **Explorateur de solutions**, double-cliquez sur **Global.asax\\Global.asax.cs**.

5.  Ajoutez la ligne suivante en bas de la méthode **Application\_Start**
     :

        FrontendWebRole.QueueConnector.Initialize();

6.  Enfin, vous allez mettre à jour le code Web que vous avez créé précédemment,
    pour envoyer des éléments dans la file d'attente. Dans **Explorateur de solutions**,
    double-cliquez sur **Controllers\\HomeController.cs**, que vous avez créé
    précédemment.

7.  Mettez à jour la méthode **Submit()** comme suit pour obtenir le nombre de messages
    de la file d'attente :

        public ActionResult Submit()
        {            
            // Get a NamespaceManager which allows you to perform management and
            // diagnostic operations on your Service Bus Queues.
            var namespaceManager = QueueConnector.CreateNamespaceManager();

            // Get the queue, and obtain the message count.
            var queue = namespaceManager.GetQueue(QueueConnector.QueueName);
            ViewBag.MessageCount = queue.MessageCount;

            return View();
        }

8.  Mettez à jour la méthode **Submit(OnlineOrder order)** comme suit pour envoyer
    des informations de commande à la file d'attente :

        public ActionResult Submit(OnlineOrder order)
        {
            if (ModelState.IsValid)
            {
                // Create a message from the order
                var message = new BrokeredMessage(order);

                // Submit the order
                QueueConnector.OrdersQueueClient.Send(message);
                return RedirectToAction("Submit");
            }
            else
            {
                return View(order);
            }
        }

9.  Vous pouvez maintenant réexécuter votre application. À chaque fois que vous envoyez
    une commande, le nombre de messages augmente.

    ![][23]

## <span class="short-header">Gestionnaire de configuration</span>Gestionnaire de configuration cloud

Azure prend en charge un ensemble d'API managées qui offrent un moyen cohérent de créer des instances de clients du service Azure (comme Service Bus) sur les services cloud Microsoft. Ces API vous permettent d'instancier ces clients (par exemple, **CloudBlobClient**, **QueueClient**, **TopicClient**) quel que soit l'endroit où est hébergée l'application : localement, dans un service cloud Microsoft, sur un site Web ou dans un rôle de machine virtuelle persistant. Vous pouvez également utiliser ces API pour récupérer les informations de configuration nécessaires à l'instanciation de ces clients et pour modifier la configuration sans avoir à redéployer l'application. Ces API se trouvent dans la classe [Microsoft.WindowsAzure.Configuration.CloudConfigurationManager][Microsoft.WindowsAzure.Configuration.CloudConfigurationManager]. Il existe également des API côté client.

### Chaîne de connexion

Pour instancier un client (par exemple, un client Service Bus **QueueClient**), vous pouvez représenter les informations de configuration sous forme de chaîne de connexion. Côté client, une méthode **CreateFromConnectionString()** permet d'instancier ce type de client avec cette chaîne de connexion. Par exemple, dans la section de configuration suivante :

    <ConfigurationSettings>
    ...
        <Setting name="Microsoft.ServiceBus.ConnectionString" value="Endpoint=sb://[yourServiceNamespace].servicebus.windows.net/;SharedSecretIssuer=[issuerName];SharedSecretValue=[yourDefaultKey]" />
    </ConfigurationSettings>

Le code qui suit récupère la chaîne de connexion, crée une file d'attente et initialise la connexion à la file d'attente :

    QueueClient Client; 

    string connectionString =
     CloudConfigurationManager.GetSetting("Microsoft.ServiceBus.ConnectionString");

    var namespaceManager =
     NamespaceManager.CreateFromConnectionString(connectionString); 

    if (!namespaceManager.QueueExists(QueueName))
    {
        namespaceManager.CreateQueue(QueueName);
    }

    // Initialize the connection to Service Bus Queue
    Client = QueueClient.CreateFromConnectionString(connectionString, QueueName);

Le code de la section suivante utilise ces API de gestion de la configuration.

## <span class="short-header">Création du rôle de travail</span>Création du rôle de travail

Vous allez maintenant créer le rôle de travail qui traite les
commandes envoyées. Cet exemple utilise le modèle de projet Visual Studio **Worker Role with Service Bus Queue**. Utilisez d'abord l'Explorateur de serveurs dans Visual Studio pour obtenir les informations de connexion nécessaires.

1.  Si vous avez déjà connecté Visual Studio à votre compte Azure, comme décrit dans la section **Configuration de l'espace de noms avec l'explorateur de serveurs Visual Studio**, passez directement à l'étape 5.

2.  Dans la barre de menus de Visual Studio, sélectionnez **Affichage**, puis cliquez sur **Explorateur de serveurs**. Un nœud **Service Bus** s'affiche sous **Azure** dans la hiérarchie de l'Explorateur de serveurs, comme dans la figure suivante.

    ![][24]

3.  Dans l'Explorateur de serveurs, développez **Azure**, cliquez avec le bouton droit sur **Service Bus**, puis cliquez sur **Ajouter une nouvelle connexion**.

4.  Dans la boîte de dialogue **Ajouter une connexion**, tapez le nom de l'espace de noms de service, le nom de l'émetteur et la clé de l'émetteur. Cliquez ensuite sur **OK** pour vous connecter.

    ![][25]

5.  Dans Visual Studio, dans **Explorateur de solutions**, cliquez avec le bouton droit sur le dossier
    **Rôles** sous le projet **MultiTierApp**.

6.  Cliquez sur **Ajouter**, puis sur **Nouveau projet de rôle de travail**. La boîte de dialogue **Ajouter un nouveau projet de rôle** s'affiche.

    ![][26]

7.  Dans la boîte de dialogue **Ajouter un nouveau projet de rôle**, cliquez sur **Worker Role with Service Bus Queue**, comme dans la figure suivante :

    ![][27]

8.  Dans la boîte de dialogue **Nom**, entrez le nom du projet **OrderProcessingRole**. Cliquez ensuite sur **Add**.

9.  Dans l'Explorateur de serveurs, cliquez avec le bouton droit sur l'espace de noms de votre service, puis cliquez sur **Propriétés**. Dans le volet **Propriétés** de Visual Studio, la première entrée contient une chaîne de connexion qui est remplie avec le point de terminaison de l'espace de noms du service, qui contient les informations d'identification nécessaires. Par exemple, reportez-vous à la figure suivante. Double-cliquez sur **ConnectionString** et appuyez sur **Ctrl+C** pour copier cette chaîne dans le Presse-papiers.

    ![][28]

10. Dans Explorateur de solutions, cliquez avec le bouton droit sur **OrderProcessingRole**, créé à l'étape 7 (assurez-vous de bien cliquer avec le bouton droit sur **OrderProcessingRole** sous **Rôles**, et pas dans la classe). Cliquez ensuite sur **Propriétés**.

11. Sous l'onglet **Paramètres** de la boîte de dialogue **Propriétés**, cliquez dans la zone **Valeur** pour **Microsoft.ServiceBus.ConnectionString**, puis copiez la valeur du point de terminaison que vous avez copiée à l'étape 8.

    ![][29]

12. Créez une classe **OnlineOrder** pour représenter les commandes à mesure que vous les traitez dans la file d'attente. Vous pouvez réutiliser une classe que vous avez déjà créée. Dans Explorateur de solutions, cliquez avec le bouton droit sur **OrderProcessingRole** (sur le projet, et non sur le rôle). Cliquez sur **Ajouter**, puis sur **Élément existant**.

13. Accédez au sous-dossier **FrontendWebRole\\Models**, puis double-cliquez sur **OnlineOrder.cs** pour l'ajouter au projet.

14. Dans WorkerRole.cs, changez la valeur de la variable **QueueName** dans **WorkerRole.cs** de `"ProcessingQueue"` en `"OrdersQueue"`, comme dans le code suivant :

        // The name of your queue
        const string QueueName = "OrdersQueue";

15. Ajoutez l'instruction using suivante au début du fichier WorkerRole.cs :

        using FrontendWebRole.Models;

16. Dans la fonction `Run()`, dans l'appel `OnMessage`, ajoutez le code suivant dans la clause `try` :

        Trace.WriteLine("Processing", receivedMessage.SequenceNumber.ToString());
        // View the message as an OnlineOrder
        OnlineOrder order = receivedMessage.GetBody<OnlineOrder>();
        Trace.WriteLine(order.Customer + ": " + order.Product, "ProcessingMessage");
        receivedMessage.Complete();

17. Vous avez terminé l'application. Vous pouvez tester l'application
    complète comme vous l'avez fait précédemment, en appuyant sur F5. Notez que le nombre de messages n'augmente pas, car le rôle de travail traite les éléments de la file d'attente et les marque comme terminés. Vous pouvez voir le résultat du suivi de
    votre rôle de travail en affichant l'interface utilisateur de l'émulateur de calcul Azure. Pour cela,
    cliquez avec le bouton droit sur l'icône de l'émulateur dans
    la zone de notification de la barre des tâches, puis sélectionnez **Show Compute Emulator UI**.

    ![][30]

    ![][31]

## <a name="nextsteps"></a><span class="short-header">Étapes suivantes</span>Étapes suivantes

Pour en savoir plus sur Service Bus, consultez les ressources suivantes :

-   [Azure Service Bus][Azure Service Bus]
-   [Procédures Service Bus][Procédures Service Bus]
-   [Utilisation des files d'attente Service Bus][Utilisation des files d'attente Service Bus]

Pour plus d'informations sur les scénarios multiniveau ou pour savoir comment déployer une application dans un service cloud, consultez la page :

-   [Application ASP.NET multiniveau avec tables, files d'attente et objets blob de stockage Azure][Application ASP.NET multiniveau avec tables, files d'attente et objets blob de stockage Azure]

Vous pouvez mettre en œuvre le composant frontal d'une application multiniveau dans un site Web Azure au lieu d'un service cloud Azure. Pour plus d'informations sur les différences entre les sites web et les services cloud, consultez la page [Modèles d'exécution Azure][Modèles d'exécution Azure].

Pour mettre en œuvre l'application que vous créez dans ce didacticiel en tant que projet Web standard au lieu d'un rôle Web d'un service cloud, suivez les étapes de ce didacticiel en appliquant les différences suivantes :

1.  Lorsque vous créez le projet, choisissez le modèle de projet **Application Web ASP.NET MVC 4** dans la catégorie **Web**, au lieu du modèle **Service cloud** dans la catégorie **Cloud**. Suivez ensuite les mêmes instructions que celles concernant la création de l'application MVC, jusqu'à ce que vous arriviez à la section **Gestionnaire de configuration cloud**.

2.  Lorsque vous créez le rôle de travail, créez-le dans une nouvelle solution séparée, comme dans les instructions pour le rôle Web. Maintenant, vous créez uniquement le rôle de travail dans le projet de service cloud. Suivez ensuite les mêmes instructions pour créer le rôle de travail.

3.  Vous pouvez tester le composant frontal et le composant principal simultanément, ou vous pouvez les exécuter en même temps dans des instances séparées de Visual Studio.

Pour plus d'informations sur le déploiement du composant frontal sur un site Web Azure, consultez la page [Déploiement d'une application Web ASP.NET sur un site Web Azure][Déploiement d'une application Web ASP.NET sur un site Web Azure]. Pour plus d'informations sur le déploiement du composant principal dans Azure Cloud Services, consultez la page [Application ASP.NET multiniveau avec tables, files d'attente et objets blob de stockage][Application ASP.NET multiniveau avec tables, files d'attente et objets blob de stockage Azure].

  [create-account-note]: ../includes/create-account-note.md
  [Étapes suivantes]: #nextsteps
  [0]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-01.png
  [Files d'attente Windows Azure et files d'attente Windows Azure Service Bus - comparaison et différences]: http://msdn.microsoft.com/fr-fr/library/windowsazure/hh767287.aspx
  [1]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-100.png
  [2]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-101.png
  [3]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-41.png
  [4]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-3.png
  [5]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-4-2-WebPI.png
  [portail de gestion Azure]: http://manage.windowsazure.com
  [6]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/sb-queues-03.png
  [7]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/sb-queues-04.png
  [8]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-27.png
  [9]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/sb-queues-09.png
  [10]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/sb-queues-06.png
  [ici]: http://http://msdn.microsoft.com/fr-fr/library/windowsazure/ff687127.aspx
  [11]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/VSProperties.png
  [12]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-09.png
  [13]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-10.jpg
  [14]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-11.png
  [15]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-02.png
  [16]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-12.png
  [17]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-13.png
  [18]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-33.png
  [19]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-34.png
  [20]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-35.png
  [21]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-40.png
  [22]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-36.png
  [23]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-37.png
  [24]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/SBExplorer.png
  [25]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/SBExplorerAddConnect.png
  [26]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/SBNewWorkerRole.png
  [27]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/SBWorkerRole1.png
  [28]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/SBExplorerProperties.png
  [29]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/SBWorkerRoleProperties.png
  [30]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-38.png
  [31]: ./media/cloud-services-dotnet-multi-tier-app-using-service-bus-queues/getting-started-multi-tier-39.png
  [Azure Service Bus]: http://msdn.microsoft.com/fr-fr/library/windowsazure/ee732537.aspx
  [Procédures Service Bus]: /fr-fr/documentation/services/service-bus/
  [Utilisation des files d'attente Service Bus]: /fr-fr/develop/net/how-to-guides/service-bus-queues/
  [Application ASP.NET multiniveau avec tables, files d'attente et objets blob de stockage Azure]: /fr-fr/develop/net/tutorials/multi-tier-web-site/1-overview/
  [Modèles d'exécution Azure]: http://www.windowsazure.com/fr-fr/develop/net/fundamentals/compute/
  [Déploiement d'une application Web ASP.NET sur un site Web Azure]: http://www.windowsazure.com/fr-fr/develop/net/tutorials/get-started/
