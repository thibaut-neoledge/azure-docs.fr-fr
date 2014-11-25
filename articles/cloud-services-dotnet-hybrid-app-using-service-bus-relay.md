<properties linkid="dev-net-tutorials-hybrid-solution" urlDisplayName="Hybrid Application" pageTitle="Hybrid On-Premises/ Cloud Application (.NET) - Azure" metaKeywords="Azure Service Bus tutorial,hybrid .NET" description="Learn how to create a .NET On-Premises/Cloud Hybrid Application Using the Azure Service Bus Relay." metaCanonical="" services="service-bus" documentationCenter=".NET" title=".NET On-Premises/Cloud Hybrid Application Using Service Bus Relay" authors="sethm" solutions="" manager="dwrede" editor="mattshel" />

<tags ms.service="service-bus" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="sethm" />

# Application hybride .NET locale/dans le cloud avec Service Bus Relay

## <span class="short-header">INTRODUCTION</span>INTRODUCTION

Le développement d'applications cloud hybrides avec Azure est simple grâce à Visual Studio 2013 et au Kit de développement logiciel (SDK) Azure gratuit pour .NET. Ce guide part du principe que vous n'avez pas d'expérience en tant qu'utilisateur d'Azure. En moins de 30 minutes, vous disposez d'une application qui utilise plusieurs ressources Azure s'exécutant dans le cloud.

Vous apprendrez à effectuer les opérations suivantes:

-   Créer ou adapter un service web existant qui sera utilisé par une solution web
-   Utiliser Azure Service Bus Relay pour partager des données entre une application Azure et un service web hébergé ailleurs.

[WACOM.INCLUDE [create-account-note](../includes/create-account-note.md)]

### AVANTAGES DE L'UTILISATION DE SERVICE BUS RELAY POUR LES SOLUTIONS HYBRIDES

Les solutions d'entreprise se composent généralement d'une combinaison de code personnalisé écrit pour répondre aux exigences nouvelles et uniques de l'entreprise, et de fonctionnalités existantes offertes par des solutions et des systèmes déjà en place.

Les architectes de solutions commencent à utiliser le cloud, car celui-ci permet de gérer plus facilement les exigences de mise à l'échelle tout en offrant des coûts opérationnels faibles. Ainsi, ils découvrent que les ressources des services existants qu'ils souhaiteraient exploiter comme des blocs de construction pour leurs solutions se situent à l'intérieur du pare-feu d'entreprise et hors de portée pour une solution cloud. Bon nombre de services internes ne sont pas créés ni hébergés pour être facilement exposés dans le périmètre du réseau d'entreprise.

*Service Bus Relay* est conçu pour rendre accessibles les services Web WCF (Windows Communication Foundation) existants, d'une manière sécurisée, aux solutions qui résident à l'extérieur du périmètre de l'entreprise sans exiger de modifications intrusives de l'infrastructure de réseau d'entreprise. Les services de relais Service Bus Relay sont toujours hébergés dans leur environnement existant, mais ils délèguent l'écoute des sessions et des demandes entrantes au Service Bus hébergé dans le cloud. Ce dernier protège également ces services contre l'accès non autorisé à l'aide d'Azure Active Directory Access Control.

### SCÉNARIO DE LA SOLUTION

Dans ce didacticiel, vous allez créer un site web ASP.NET MVC 4 qui vous permettra de voir une liste de produits sur la page d'inventaire des produits.

![][0]

Ce didacticiel part du principe que vous disposez des informations sur les produits dans un système local existant, et utilise Service Bus Relay pour atteindre ce système. La simulation met en scène un service web qui est exécuté dans une simple application console et est appuyé par un ensemble de produits en mémoire. Vous allez pouvoir exécuter cette application console sur votre propre ordinateur et déployer le rôle web dans Azure. Ainsi, vous verrez comment procède le rôle web en cours d'exécution dans le centre de données Azure pour appeler votre ordinateur, même si ce dernier réside certainement derrière au moins un pare-feu et une couche de traduction d'adresses réseau (NAT, network address translation).

La capture d'écran suivante montre la page d'accueil de l'application Web terminée.

![][1]

## <span class="short-header">CONFIGURATION DE L'ENVIRONNEMENT</span>CONFIGURATION DE L'ENVIRONNEMENT DE DÉVELOPPEMENT

Avant de commencer à développer votre application Azure, vous devez obtenir des outils et configurer votre environnement de développement.

1.  Pour installer le Kit de développement logiciel (SDK) Azure pour .NET, cliquez sur le bouton ci-dessous:

    [Obtenir les outils et le Kit de développement logiciel (SDK)][Obtenir les outils et le Kit de développement logiciel (SDK)]

2.  Cliquez sur **Install the SDK**.

3.  Choisissez le lien correspondant à votre version de Visual Studio. Les étapes de ce didacticiel utilisent Visual Studio 2013:

    ![][2]

4.  Lorsque vous êtes invité à exécuter ou à enregistrer **WindowsAzureSDKForNet.exe**, cliquez sur **Exécuter**:

    ![][3]

5.  Dans Web Platform Installer, cliquez sur **Installer**, puis poursuivez l'installation:

    ![][4]

6.  Une fois l'installation terminée, vous disposez de tous les éléments nécessaires pour commencer le développement. Le Kit de développement logiciel (SDK) comprend des outils qui vous permettent de facilement développer des applications Azure dans Visual Studio. Si Visual Studio n'est pas installé, Visual Studio Express est installé gratuitement.

## <span class="short-header">CRÉATION D'UN ESPACE DE NOMS</span>CRÉATION D'UN ESPACE DE NOMS DE SERVICE

Pour commencer à utiliser les fonctionnalités de Service Bus dans Azure, vous devez d'abord créer un espace de noms de service. Ce dernier fournit un conteneur d'étendue pour l'adressage des ressources Service Bus au sein de votre application.

Vous pouvez également gérer les espaces de noms et les entités de messagerie Service Bus à l'aide du [portail de gestion Azure][portail de gestion Azure] ou de l'Explorateur de serveurs Visual Studio, mais vous ne pouvez créer d'espaces de noms que depuis le portail.

### Pour créer un espace de noms de service à l'aide du portail:

1.  Connectez-vous au [portail de gestion Azure][portail de gestion Azure].

2.  Dans le volet de navigation gauche du portail de gestion, cliquez sur **Service Bus**.

3.  Dans le volet inférieur du portail de gestion, cliquez sur **Créer**.

    ![][5]

4.  Dans la boîte de dialogue **Ajouter un nouvel espace de noms**, entrez un nom d'espace de noms. Le système vérifie immédiatement la disponibilité du nom.

    ![][6]

5.  Après vous être assuré de la disponibilité du nom de l'espace de noms, choisissez le pays ou la région où votre espace de noms doit être hébergé (veillez à utiliser le même pays ou la même région que celui ou celle où vous déployez vos ressources de calcul).

    IMPORTANT: choisissez la **même région** que celle que vous prévoyez de sélectionner pour le déploiement de votre application. Vous bénéficiez ainsi des meilleures performances.

6.  Cliquez sur la coche. Le système crée l'espace de noms de service et l'active. Vous devrez peut-être attendre plusieurs minutes afin que le système approvisionne des ressources pour votre compte.

    ![][7]

L'espace de noms que vous avez créé apparaît alors dans le portail de gestion. Son activation peut prendre un peu de temps. Patientez jusqu'à ce que l'état soit **Actif** avant de continuer.

## <span class="short-header">OBTENTION DES INFORMATIONS D'IDENTIFICATION DE GESTION</span>OBTENTION DES INFORMATIONS D'IDENTIFICATION DE GESTION POUR L'ESPACE DE NOMS

Afin d'effectuer des opérations de gestion, comme la création d'une file d’attente, sur le nouvel espace de noms, vous devez obtenir les informations de gestion associées.

1.  Dans la fenêtre principale, cliquez sur le nom de votre espace de noms de service.

    ![][8]

2.  Cliquez sur **Connection Information**.

    ![][9]

3.  Dans le volet **Access connection information**, recherchez les entrées **Default Issuer** et **Default Key**.

4.  Notez la clé ou copiez-la dans le Presse-papiers.

### Gestion d'un espace de noms de service avec l'Explorateur de serveurs Visual Studio:

Pour gérer un espace de noms et obtenir des informations de connexion en utilisant Visual Studio plutôt que le portail de gestion, suivez la procédure décrite [ici][ici], dans la section intitulée **Connexion à Azure à partir de Visual Studio**. Lorsque vous vous connectez à Azure, le nœud **Service Bus** sous l’arborescence **Microsoft Azure** dans l’Explorateur de serveurs est automatiquement rempli par les espaces de noms que vous avez déjà créés. Cliquez avec le bouton droit sur un espace de noms, puis cliquez sur **Properties** pour que la chaîne de connexion et les autres métadonnées associées à cet espace de noms s’affichent dans le volet **Properties** de Visual Studio.

![][10]

Notez la valeur **SharedAccessKey** ou copiez-la dans le Presse-papiers.

## <span class="short-header">CRÉATION D'UN SERVEUR LOCAL</span>CRÉATION D'UN SERVEUR LOCAL

Vous créez d'abord un système local de catalogue de produits (fictif). Cela est assez simple: représentez-vous un système réel de catalogue de produits local avec une surface complète de services que nous essayons d'intégrer.

Ce projet démarre comme une application console Visual Studio. Il utilise le package NuGet Service Bus pour inclure les bibliothèques et les paramètres de configuration Service Bus. L'extension Visual Studio NuGet facilite l'installation et la mise à jour des bibliothèques et des outils de Visual Studio et Visual Studio Express. Le package NuGet Service Bus est le moyen
le plus simple de se procurer l'API Service Bus et de configurer votre application avec toutes les dépendances Service Bus. Pour plus d'informations sur l'utilisation de NuGet et du package Service Bus, consultez la page [Utilisation du package NuGet Service Bus][Utilisation du package NuGet Service Bus].

### CRÉATION DU PROJET

1.  Avec les privilèges d'administrateur, démarrez
    Microsoft Visual Studio 2013 ou Microsoft Visual Studio Express. Pour démarrer Visual Studio avec les privilèges d'administrateur, cliquez avec le bouton droit sur **Microsoft Visual Studio 2013 (ou Microsoft Visual Studio Express)**, puis cliquez sur **Exécuter en tant qu'administrateur**.
2.  Dans Visual Studio, dans le menu **Fichier**, cliquez sur **Nouveau**, puis sur **Projet**.

    ![][11]

3.  Dans **Modèles installés**, sous **Visual C\#**, cliquez sur **Application console**. Dans la zone **Nom**, entrez le nom **ProductsServer**:

    ![][12]

4.  Cliquez sur **OK** pour créer le projet **ProductsServer**.

5.  Dans l'**Explorateur de solutions**, cliquez avec le bouton droit sur **ProductsServer**, puis cliquez sur **Propriétés**.
6.  Cliquez sur l'onglet **Application** situé à gauche, puis assurez-vous que **.NET Framework 4 ** ou **.NET Framework 4.5** apparaît dans la liste déroulante **Version cible de .NET Framework** . Dans le cas contraire, sélectionnez cette entrée dans la liste déroulante, puis cliquez sur **Oui** lorsque vous êtes invité à recharger le projet.

    ![][13]

7.  Si vous avez déjà installé le gestionnaire de package NuGet pour Visual Studio, passez à l'étape suivante. Sinon, accédez à [NuGet][NuGet], puis cliquez sur [Install NuGet][Install NuGet]. Suivez les invites pour installer le gestionnaire de package NuGet, puis redémarrez Visual Studio.

8.  Dans l'**Explorateur de solutions**, cliquez avec le bouton droit sur **References**, puis cliquez sur **Manage NuGet Packages**.
9.  Dans la colonne de gauche de la boîte de dialogue NuGet, cliquez sur **Online**.

10. Dans la colonne de droite, cliquez sur la zone **Rechercher**, tapez « **WindowsAzure** », puis sélectionnez l'élément **Windows Azure Service Bus **. Cliquez sur **Installer** pour terminer l'installation, puis fermez cette boîte de dialogue.

    ![][14]

    Notez que les assemblys client nécessaires sont maintenant référencés.

11. Ajoutez une nouvelle classe pour votre contrat de produit. Dans l'**Explorateur de solutions**, cliquez avec le bouton droit sur le projet **ProductsServer**, cliquez sur **Ajouter**, puis sur **Classe**.

    ![][15]

12. Dans la zone **Nom**, entrez le nom **ProductsContract.cs**. Cliquez ensuite sur **Ajouter**.
13. Dans **ProductsContract.cs**, remplacez la définition d'espace de noms existante par le code suivant, qui définit le contrat du service:

        namespace ProductsServer
        {
            using System.Collections.Generic;
            using System.Runtime.Serialization;
            using System.ServiceModel;

            // Define the data contract for the service
            [DataContract]
            // Declare the serializable properties
            public class ProductData
            {
                [DataMember]
                public string Id { get; set; }
                [DataMember]
                public string Name { get; set; }
                [DataMember]
                public string Quantity { get; set; }
            }

            // Define the service contract.
            [ServiceContract]
            interface IProducts
            {
                [OperationContract]
                IList<ProductData> GetProducts();

            }

            interface IProductsChannel : IProducts, IClientChannel
            {
            }
        }

14. Dans Program.cs, remplacez la définition d'espace de noms existante par le code suivant, qui ajoute le service de profil et l'hôte correspondant:

        namespace ProductsServer
        {
            using System;
            using System.Linq;
            using System.Collections.Generic;
            using System.ServiceModel;

            // Implement the IProducts interface
            class ProductsService : IProducts
            {

                // Populate array of products for display on Website
                ProductData[] products = 
                    new []
                        {
                            new ProductData{ Id = "1", Name = "Rock", 
                                             Quantity = "1"},
                            new ProductData{ Id = "2", Name = "Paper", 
                                             Quantity = "3"},
                            new ProductData{ Id = "3", Name = "Scissors", 
                                             Quantity = "5"},
                            new ProductData{ Id = "4", Name = "Well", 
                                             Quantity = "2500"},
                        };

                // Display a message in the service console application 
                // when the list of products is retrieved
                public IList<ProductData> GetProducts()
                {
                    Console.WriteLine("GetProducts called.");
                    return products;
                }

            }

            class Program
            {
                // Define the Main() function in the service application
                static void Main(string[] args)
                {
                    var sh = new ServiceHost(typeof(ProductsService));
                    sh.Open();

                    Console.WriteLine("Press ENTER to close");
                    Console.ReadLine();

                    sh.Close();
                }
            }
        }

15. Dans l'**Explorateur de solutions**, double-cliquez sur le fichier **app.config** pour l'ouvrir dans l'éditeur **Visual Studio**. Remplacez le contenu de **\<system.ServiceModel\>** par le code XML suivant. Assurez-vous de remplacer *yourServiceNamespace* par le nom de votre espace de noms de service, puis *yourIssuerSecret* par la clé que vous avez récupérée précédemment sur le portail de gestion Azure:

        <system.serviceModel>
          <extensions>
             <behaviorExtensions>
                <add name="transportClientEndpointBehavior" type="Microsoft.ServiceBus.Configuration.TransportClientEndpointBehaviorElement, Microsoft.ServiceBus, Version=2.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
              </behaviorExtensions>
              <bindingExtensions>
                 <add name="netTcpRelayBinding" type="Microsoft.ServiceBus.Configuration.NetTcpRelayBindingCollectionElement, Microsoft.ServiceBus, Version=2.0.0.0, Culture=neutral, PublicKeyToken=31bf3856ad364e35"/>
              </bindingExtensions>
          </extensions>
          <services>
             <service name="ProductsServer.ProductsService">
               <endpoint address="sb://yourServiceNamespace.servicebus.windows.net/products" binding="netTcpRelayBinding" contract="ProductsServer.IProducts"
        behaviorConfiguration="products"/>
             </service>
          </services>
          <behaviors>
             <endpointBehaviors>
               <behavior name="products">
                 <transportClientEndpointBehavior>
                    <tokenProvider>
                       <sharedSecret issuerName="owner" issuerSecret="yourIssuerSecret" />
                    </tokenProvider>
                 </transportClientEndpointBehavior>
               </behavior>
             </endpointBehaviors>
          </behaviors>
        </system.serviceModel>

16. Appuyez sur **F6** ou, dans le menu **Générer**, cliquez sur **Générer la solution** pour générer l'application afin de vérifier si votre travail est correct.

## <span class="short-header">CRÉATION D'UNE APPLICATION ASP.NET MVC</span>CRÉATION D'UNE APPLICATION ASP.NET MVC

Dans cette section, vous allez générer une application ASP.NET simple qui affichera des données récupérées de votre service de produit.

### CRÉATION DU PROJET

1.  Vérifiez que Microsoft Visual Studio 2013 fonctionne avec les privilèges d'administrateur. Si ce n'est pas le cas, pour démarrer Visual Studio avec les privilèges d'administrateur, cliquez avec le bouton droit sur **Microsoft Visual Studio 2013 (ou Microsoft Visual Studio Express)**, puis cliquez sur **Exécuter en tant qu'administrateur**. L'émulateur de calcul Azure, présenté plus loin dans ce guide, nécessite que Visual Studio soit démarré avec les privilèges d'administrateur.

2.  Dans Visual Studio, dans le menu **Fichier**, cliquez sur **Nouveau**, puis sur **Projet**.

3.  Dans **Modèles installés**, sous **Visual C\#**, cliquez sur **Application Web ASP.NET**. Nommez le projet **ProductsPortal**. Cliquez ensuite sur **OK**.

    ![][16]

4.  Dans la liste **Sélectionner un modèle**, cliquez sur **MVC**, puis sur **OK**.

    ![][17]

5.  Dans **Explorateur de solutions**, cliquez avec le bouton droit sur **Modèles**, cliquez sur **Ajouter**, puis sur **Classe**. Dans la zone **Nom**, entrez le nom **Products.cs**. Cliquez ensuite sur **Add**.

    ![][18]

### MODIFICATION DE L'APPLICATION WEB

1.  Dans le fichier Product.cs dans Visual Studio, remplacez la définition d'espace de noms existante par le code suivant:

        // Declare properties for the products inventory
        namespace ProductsWeb.Models
        {
            public class Product
            {
                public string Id { get; set; }
                public string Name { get; set; }
                public string Quantity { get; set; }
            }
        }

2.  Dans le fichier HomeController.cs dans Visual Studio, remplacez la définition d'espace de noms existante par le code suivant:

        namespace ProductsWeb.Controllers
        {
            using System.Collections.Generic;
            using System.Web.Mvc;
            using Models;

            public class HomeController : Controller
            {
                // Return a view of the products inventory
                public ActionResult Index(string Identifier, string ProductName)
                {
                    var products = new List<Product> 
                        {new Product {Id = Identifier, Name = ProductName}};
                    return View(products);
                }

            }
        }

3.  Dans l'**Explorateur de solutions**, développez Views\\Shared:

    ![][19]

4.  Double-cliquez ensuite sur \_Layout.cshtml pour l'ouvrir dans l'éditeur de Visual Studio.

5.  Remplacez toutes les occurrences de **Mon application ASP.NET** par **LITWARE's Products**.

6.  Supprimez les liens **Home**, **About** et **Contact**. Supprimez le code en surbrillance:

    ![][20]

7.  Dans l'**Explorateur de solutions**, développez Views\\Home:

    ![][21]

8.  Double-cliquez sur Index.cshtml pour l'ouvrir dans l'éditeur Visual Studio. Remplacez tout le contenu du fichier par le code suivant:

        @model IEnumerable<ProductsWeb.Models.Product>

        @{
            ViewBag.Title = "Index";
        }

        <h2>Prod Inventory</h2>

        <table>
            <tr>
                <th>
                    @Html.DisplayNameFor(model => model.Name)
                </th>
                <th></th>
                <th>
                    @Html.DisplayNameFor(model => model.Quantity)
                </th>
            </tr>

        @foreach (var item in Model) {
            <tr>
                <td>
                    @Html.DisplayFor(modelItem => item.Name)
                </td>
                <td>
                    @Html.DisplayFor(modelItem => item.Quantity)
                </td>
            </tr>   
        }

        </table>

9.  À ce stade, pour vérifier votre travail, appuyez sur **F6** ou **Ctrl+Maj+B** pour générer le projet.

### EXÉCUTION LOCALE DE L'APPLICATION

Exécutez l'application afin de vérifier qu'elle fonctionne.

1.  Assurez-vous que **ProductsPortal** est le projet actif. Dans l'**Explorateur de solutions**, cliquez avec le bouton droit sur le nom du projet, puis sélectionnez **Définir comme projet de démarrage**.
2.  Dans **Visual Studio**, appuyez sur **F5**.
3.  Votre application doit s'exécuter dans un navigateur:

    ![][22]

    ## <span class="short-header">DÉPLOIEMENT SUR AZURE</span>PRÉPARATION DE VOTRE APPLICATION EN VUE DE SON DÉPLOIEMENT SUR AZURE

    Vous pouvez déployer votre application vers un service cloud Azure ou un site web Azure. Pour plus d'informations sur les différences entre les sites web et les services cloud, consultez la page [Modèles d'exécution Azure][Modèles d'exécution Azure]. Pour plus d'informations sur le déploiement de l'application vers un site web Azure, consultez la page [Déploiement d'une application web ASP.NET vers un site web Azure][Déploiement d'une application web ASP.NET vers un site web Azure]. Cette section contient une procédure pas à pas pour déployer l'application sur un service cloud Azure.

    Pour déployer votre application vers un service cloud, ajoutez un projet de déploiement de projet de service cloud à la solution.
    Le projet de déploiement contient des informations de configuration nécessaires pour exécuter correctement l'application sur le cloud.

    1.  Pour permettre le déploiement de votre application sur le cloud, cliquez avec le bouton droit sur le projet **ProductsPortal** dans l'**Explorateur de solutions**, puis cliquez sur **Convertir** et sur **Convertir en projet service cloud Azure**.

        ![][23]

    2.  Pour tester votre application, appuyez sur **F5**.
    3.  L'émulateur de calcul Azure démarre alors. Il utilise l'ordinateur local pour émuler votre application qui s'exécute dans Azure. Vous savez que l'émulateur a démarré en consultant la zone de notification:

        ![][24]

    4.  Un navigateur continue d'afficher votre application s'exécutant en local. Son apparence et son fonctionnement sont semblables à ceux de l'application ASP.NET MVC 4 classique que vous exécutiez précédemment.

    ## <span class="short-header">ASSEMBLAGE DES ÉLÉMENTS</span>ASSEMBLAGE DES ÉLÉMENTS

    La prochaine étape consiste à raccorder le serveur de produits local et l'application ASP.NET MVC.

    1.  S'il n'est pas déjà ouvert, dans Visual Studio, rouvrez le projet **ProductsPortal** que vous avez créé dans la section « Création d'une application ASP.NET MVC ».

    2.  Comme vous l'avez fait dans la section « Création d'un serveur local », ajoutez le package NuGet au projet Références. Dans l' Explorateur de solutions, cliquez avec le bouton droit sur **Références**, puis cliquez sur **Gérer les packages NuGet**.

    3.  Recherchez « WindowsAzure.ServiceBus », puis sélectionnez l'élément **Windows Azure Service Bus**. Ensuite, terminez l'installation et fermez cette boîte de dialogue.

    4.  Dans l'Explorateur de solutions, cliquez avec le bouton droit sur le projet **ProductsPortal**, cliquez sur **Ajouter**, puis sur **Élément existant**.

    5.  Accédez au fichier **ProductsContract.cs** depuis le projet de console **ProductsServer**. Cliquez sur ProductsContract.cs. afin de le mettre en surbrillance. Cliquez sur la flèche vers le bas en regard d'**Ajouter**, puis cliquez sur **Ajouter en tant que lien**.

        ![][25]

    6.  Ouvrez à présent le fichier **HomeController.cs** dans l'éditeur Visual Studio,  puis remplacez la définition d'espace de noms existante par le code suivant. Veillez à remplacer *yourServiceNamespace* par le nom de votre espace de noms de service, et *yourIssuerSecret* par votre clé. Le client peut alors appeler le service local, en renvoyant le résultat de l'appel.

            namespace ProductsWeb.Controllers
            {
                using System.Linq;
                using System.ServiceModel;
                using System.Web.Mvc;
                using Microsoft.ServiceBus;
                using Models;
                using ProductsServer;

                public class HomeController : Controller
                {
                    // Declare the channel factory
                    static ChannelFactory<IProductsChannel> channelFactory;

                    static HomeController()
                    {
                        // Create shared secret token credentials for authentication 
                        channelFactory = new ChannelFactory<IProductsChannel>(new NetTcpRelayBinding(), 
                            "sb://yourServiceNamespace.servicebus.windows.net/products");
                        channelFactory.Endpoint.Behaviors.Add(new TransportClientEndpointBehavior { 
                            TokenProvider = TokenProvider.CreateSharedSecretTokenProvider(
                                "owner", "yourIssuerSecret") });
                    }

                    public ActionResult Index()
                    {
                        using (IProductsChannel channel = channelFactory.CreateChannel())
                        {
                            // Return a view of the products inventory
                            return this.View(from prod in channel.GetProducts()
                                             select
                                                 new Product { Id = prod.Id, Name = prod.Name, 
                                                     Quantity = prod.Quantity });
                        }
                    }
                }
            }

    7.  Dans l'Explorateur de solutions, cliquez avec le bouton droit sur la solution **ProductsPortal**, cliquez sur **Ajouter**, puis sur **Projet existant**.

    8.  Accédez au projet **ProductsServer**, puis double-cliquez sur le fichier solution **ProductsServer.csproj** pour l'ajouter.

    9.  Dans l'Explorateur de solutions, cliquez avec le bouton droit sur la solution **ProductsPortal**, puis cliquez sur **Propriétés**.

    10. Dans la partie gauche, cliquez sur **Projet de démarrage**. Dans la partie droite, cliquez sur **Plusieurs projets de démarrage**. Assurez-vous que **ProductsServer**, **ProductsPortal.Azure** et **ProductsPortal** apparaissent, dans cet ordre, avec **Démarrer** défini comme action pour **ProductsServer** et **ProductsPortal.Azure**, et **Aucun** défini comme action pour **ProductsPortal**. Par exemple:

        ![][26]

    11. Toujours dans la boîte de dialogue Propriétés, cliquez sur **ProjectDependencies** dans la partie gauche.

    12. Dans le menu déroulant **Projets**, cliquez sur **ProductsServer**. Assurez-vous que **ProductsPortal** est désactivé et que **ProductsPortal.Azure** est activé. Cliquez ensuite sur **OK**:

        ![][27]

    ## <span class="short-header">EXÉCUTION DE L'APPLICATION</span>EXÉCUTION DE L'APPLICATION

    1.  Dans Visual Studio, dans le menu **Fichier**, cliquez sur **Enregistrer tout**.

    2.  Appuyez sur **F5** pour générer et exécuter l'application. Le serveur local (application console **ProductsServer**) doit démarrer en premier, puis l'application **ProductsWeb** doit démarrer dans une fenêtre de navigateur, comme illustré dans la capture d'écran ci-dessous. À présent, vous voyez que l'inventaire de produits répertorie des données récupérées du système local de service de produit.

        ![][1]

    ## <span class="short-header">DÉPLOIEMENT DE L'APPLICATION</span>DÉPLOIEMENT DE L'APPLICATION DANS AZURE

    1.  Dans l'**Explorateur de solutions**, cliquez avec le bouton droit sur le projet **ProductsPortal**, puis cliquez sur **Publier sur Azure**.

    2.  Vous devrez peut-être vous connecter pour voir tous vos abonnements.

        Cliquez sur **Sign in to see more subscriptions**:

        ![][28]

    3.  Connectez-vous à l'aide de votre compte Microsoft.

    4.  Cliquez sur **Suivant**. Si votre abonnement ne contient pas encore de service hébergé, vous êtes invité à en créer un. Le service hébergé se comporte comme un conteneur pour votre application au sein de votre abonnement Azure. Entrez un nom qui identifie votre application et choisissez la région pour laquelle l'application doit être optimisée. Les temps de chargement seront plus rapides pour les utilisateurs y accédant à partir de cette région.

        ![][29]

    5.  Sélectionnez le service hébergé dans lequel vous voulez publier votre application. Conservez les valeurs par défaut comme indiqué ci-dessous pour les autres paramètres. Cliquez sur **Suivant**:

        ![][30]

    6.  Dans la dernière page, cliquez sur **Publier** pour démarrer le processus de déploiement:

        ![][31]

        Cela prend environ 5 à 7 minutes. Comme il s'agit de votre première publication, Azure approvisionne une machine virtuelle, renforce la sécurité, crée un rôle web sur la machine virtuelle pour héberger votre application, déploie votre code sur ce rôle web, et enfin configure l'équilibrage de charge et la mise en réseau afin de rendre votre application accessible au public.

    7.  Lors de la publication, vous serez en mesure de surveiller l'activité dans la fenêtre **Journal des activités Azure**, qui est généralement ancrée au bas de Visual Studio ou de Visual Web Developer:

        ![][32]

    8.  Lorsque le déploiement est terminé, vous pouvez afficher votre site web en cliquant sur le lien **URL du site web** dans la fenêtre de contrôle.

        ![][1]

        Votre site web reposant sur le serveur local, vous devez exécuter l'application **ProductsServer** en local afin que le site web fonctionne correctement. En effectuant des demandes sur le site web du cloud, vous voyez des demandes s'afficher dans votre application console locale, comme indiqué dans la sortie « GetProducts called » visible dans la capture d'écran ci-dessous.

        ![][33]

Pour plus d'informations sur les différences entre les sites web et les services cloud, consultez la page [Modèles d'exécution Azure][Modèles d'exécution Azure].

## <span class="short-header">SUPPRESSION DE L'APPLICATION</span>ARRÊT ET SUPPRESSION DE L'APPLICATION

Après avoir déployé votre application, vous pouvez la désactiver afin de générer et de déployer d'autres applications pendant le temps serveur gratuit de 750 heures/mois (31 jours/mois).

Azure facture les instances de rôle web par heure de serveur consommée. Une fois votre application déployée, elle consomme du temps de serveur, même si les instances ne sont pas exécutées et sont arrêtées. Un compte gratuit comprend 750 heures/mois (31 jours/mois) de temps de serveur pour une machine virtuelle dédiée permettant d'héberger ces instances de rôle web.

La procédure suivante présente l'arrêt et la suppression de l'application.

1.  Connectez-vous au [portail de gestion Azure][portail de gestion Azure], cliquez sur Cloud Services, puis sur le nom du service.

2.  Sélectionnez l'onglet **Tableau de bord**, puis cliquez sur **Arrêter** pour suspendre momentanément l'application. Vous pouvez la redémarrer en cliquant simplement sur Démarrer. Cliquez sur **Supprimer** pour supprimer complètement l'application d'Azure, sans aucune possibilité de la restaurer.

    ![][34]

## <a name="nextsteps"></a><span class="short-header">Étapes suivantes</span>Étapes suivantes

Pour en savoir plus sur Service Bus, consultez les ressources suivantes:

-   [Azure Service Bus][Azure Service Bus]
-   [Procédures Service Bus][Procédures Service Bus]
-   [Utilisation des files d'attente Service Bus][Utilisation des files d'attente Service Bus]

  [0]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hybrid.png
  [1]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/App2.png
  [Obtenir les outils et le Kit de développement logiciel (SDK)]: http://go.microsoft.com/fwlink/?LinkId=271920
  [2]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-41.png
  [3]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-3.png
  [4]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-4-2-WebPI.png
  [portail de gestion Azure]: http://manage.windowsazure.com
  [5]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/sb-queues-03.png
  [6]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/sb-queues-04.png
  [7]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-multi-tier-27.png
  [8]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/sb-queues-09.png
  [9]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/sb-queues-06.png
  [ici]: http://http://msdn.microsoft.com/fr-fr/library/windowsazure/ff687127.aspx
  [10]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/VSProperties.png
  [Utilisation du package NuGet Service Bus]: http://go.microsoft.com/fwlink/?LinkId=234589
  [11]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-web-1.png
  [12]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-con-1.png
  [13]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-con-3.png
  [NuGet]: http://nuget.org
  [Install NuGet]: http://visualstudiogallery.msdn.microsoft.com/27077b70-9dad-4c64-adcf-c7cf6bc9970c
  [14]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-multi-tier-13.png
  [15]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-con-4.png
  [16]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-web-2.png
  [17]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-web-4.png
  [18]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-web-7.jpg
  [19]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-web-10.jpg
  [20]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-multi-tier-40.png
  [21]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-web-11.png
  [22]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/App1.png
  [Modèles d'exécution Azure]: http://www.windowsazure.com/fr-fr/develop/net/fundamentals/compute/
  [Déploiement d'une application web ASP.NET vers un site web Azure]: http://www.windowsazure.com/fr-fr/develop/net/tutorials/get-started/
  [23]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-21.png
  [24]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-22.png
  [25]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-web-12.png
  [26]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-web-13.png
  [27]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-web-14.png
  [28]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-33.png
  [29]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-38.png
  [30]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-39.png
  [31]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-40.png
  [32]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-41.png
  [33]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/hy-service1.png
  [34]: ./media/cloud-services-dotnet-hybrid-app-using-service-bus-relay/getting-started-hybrid-43.png
  [Azure Service Bus]: http://msdn.microsoft.com/fr-fr/library/windowsazure/ee732537.aspx
  [Procédures Service Bus]: /fr-fr/documentation/services/service-bus/
  [Utilisation des files d'attente Service Bus]: /fr-fr/develop/net/how-to-guides/service-bus-queues/
