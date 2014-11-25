<properties urlDisplayName=".NET Client Library" pageTitle="Creating a Leaderboard App with Azure Mobile Services .NET Backend" metaKeywords="Azure Mobile Services, Mobile Service .NET client, .NET client" description="Learn how to build a Windows Store app using Azure Mobile Services with a .NET backend." documentationCenter="Mobile" title="Creating a Leaderboard App with Azure Mobile Services .NET Backend" authors="mwasson" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="mwasson" />

# Création d'une application de classement avec le serveur principal .NET Azure Mobile Services

Ce didacticiel montre comment générer une application Windows Store à l'aide d'Azure Mobile Services avec un serveur principal .NET. Azure Mobile Services fournit un serveur principal évolutif et sécurisé avec authentification, surveillance, notifications Push et autres fonctions intégrées, ainsi qu'une bibliothèque cliente interplateforme pour générer des applications mobiles. Le serveur principal .NET pour Mobile Services est basé sur l'[API web ASP.NET][API web ASP.NET] et offre aux développeurs .NET un moyen idéal de créer des API REST.

-   [Vue d'ensemble][Vue d'ensemble]
-   [À propos de l'exemple d'application][À propos de l'exemple d'application]
-   [Ajout de modèles de données][Ajout de modèles de données]
-   [Ajout de contrôleurs d'API web][Ajout de contrôleurs d'API web]
-   [Utilisation d'un objet de transfert de données pour renvoyer des entités associées][Utilisation d'un objet de transfert de données pour renvoyer des entités associées]
-   [Définition d'une API personnalisée pour l'envoi de scores][Définition d'une API personnalisée pour l'envoi de scores]
-   [Création de l'application Windows Store][Création de l'application Windows Store]
-   [Ajout de classes de modèle][Ajout de classes de modèle]
-   [Création d'un modèle de vue][Création d'un modèle de vue]
-   [Ajout d'une instance MobileServiceClient][Ajout d'une instance MobileServiceClient]
-   [Création de la page principale][Création de la page principale]
-   [Publication de votre service mobile][Publication de votre service mobile]
-   [Étapes suivantes][Étapes suivantes]

## Vue d'ensemble

L'API web est une infrastructure open source qui offre aux développeurs .NET un moyen idéal de créer des API REST. Vous pouvez héberger une solution d'API web sur Sites Web Azure, sur Azure Mobile Services avec le serveur principal .NET, ou même dans un processus personnalisé. Mobile Services est un environnement d'hébergement spécialement conçu pour les applications mobiles. Lorsque vous hébergez votre service d'API web sur Mobile Services, vous bénéficiez des avantages suivants, en plus du stockage de données :

-   Authentification intégrée avec fournisseurs sociaux et Azure Active Directory (AAD).
-   Notifications Push vers les applications utilisant des services de notification propres à l'appareil.
-   Ensemble complet de bibliothèques clientes qui offrent un accès facile à votre service à partir de toutes les applications.
-   Journalisation et diagnostics intégrés.

Ce didacticiel vous apprendra à effectuer les opérations suivantes :

-   créer une API REST grâce à Azure Mobile Services ;
-   publier le service sur Azure ;
-   créer une application Windows Store qui consomme le service ;
-   utiliser Entity Framework (EF) pour créer des relations de clé étrangère et des objets de transfert de données ;
-   utiliser l'API web ASP.NET pour définir une API personnalisée.

Ce didacticiel requiert [Visual Studio 2013 Update 2][Visual Studio 2013 Update 2].

## À propos de l'exemple d'application

Un *classement* affiche la liste des joueurs pour un jeu, ainsi que leur score et leur rang. Un classement peut faire partie d'un plus grand jeu ou peut être une application séparée. Un classement est une application dans le monde réel, mais elle est assez simple dans le cadre d'un didacticiel. Voici une capture d'écran de l'application :

![][0]

Pour maintenir la simplicité de l'application, il n'existe pas de jeu réel. Vous pouvez en revanche ajouter des joueurs et envoyer un score pour chaque joueur. Lorsque vous envoyez un score, le service mobile calcule les nouveaux rangs. Sur le serveur principal, le service mobile crée une base de données avec deux tables :

-   Player. Contient l'ID et le nom du joueur.
-   PlayerRank. Contient le score et le rang d'un joueur.

PlayerRank possède une clé étrangère pour Player. Chaque joueur possède zéro ou un PlayerRank.

Dans l'application de classement réel, PlayerRank peut également posséder un ID de jeu, de sorte qu'un joueur peut envoyer des scores pour plusieurs jeux.

![][1]

L'application cliente peut exécuter l'ensemble complet d'opérations CRUD sur les joueurs. Elle peut lire ou supprimer les entités PlayerRank existantes, mais ne peut pas les créer ou mettre à jour directement. C'est pour cela que la valeur de rang est calculée par le service. Au lieu de cela, le client envoie un score et le service met à jour les rangs de tous les joueurs.

Téléchargez le projet complet [ici][ici].

## Création du projet

Lancez Visual Studio et créez un projet d'application web ASP.NET. Nommez ce projet Leaderboard.

![][2]

Dans Visual Studio 2013 Update 2, le projet d'application web ASP.NET inclut désormais un modèle pour Windows Azure Mobile Service. Sélectionnez ce modèle et cliquez sur **OK**.

![][3]

Le modèle de projet inclut un exemple de contrôleur et d'objet de données.

![][4]

Ils ne sont pas nécessaires pour le didacticiel, vous pouvez donc les supprimer du projet. Supprimez également les références à TodoItem dans les fichiers WebApiConfig.cs et LeaderboardContext.cs.

## Ajout de modèles de données

Vous utiliserez [EF Code First][EF Code First] pour définir les tables de base de données. Sous le dossier DataObjects, ajoutez une classe nommée `Player`.

    using Microsoft.WindowsAzure.Mobile.Service;

    namespace Leaderboard.DataObjects
    {
        public class Player : EntityData
        {
            public string Name { get; set; }
        }
    }

Ajoutez une autre classe nommée `PlayerRank`.

    using Microsoft.WindowsAzure.Mobile.Service;
    using System.ComponentModel.DataAnnotations.Schema;

    namespace Leaderboard.DataObjects
    {
        public class PlayerRank : EntityData
        {
            public int Score { get; set; }
            public int Rank { get; set; }

            [ForeignKey("Id")]
            public virtual Player Player { get; set; }
        }
    }

Notez que les deux classes héritent de la classe **EntityData**. La dérivation depuis **EntityData** facilite la consommation des données par l'application, grâce à l'utilisation de la bibliothèque cliente interplateforme pour Azure Mobile Services. **EntityData** facilite également la [gestion des conflits d'écriture de base de données][gestion des conflits d'écriture de base de données] pour une application.

La classe `PlayerRank` possède une [propriété de navigation][propriété de navigation] pointant vers l'entité `Player` associée. L'attribut **[ForeignKey]** indique à EF que la propriété `Player` représente une clé étrangère.

# Ajout de contrôleurs d'API web

Ensuite, vous allez ajouter des contrôleurs d'API web pour `Player` et `PlayerRank`. Au lieu de contrôleurs d'API web simples, vous allez ajouter un type spécial de contrôleur nommé *table controller (contrôleur de table)*, spécifiquement conçu pour Azure Mobile Services.

Cliquez avec le bouton droit sur le dossier Contrôleurs, sélectionnez Ajouter, puis sélectionnez Nouvel élément structuré.

![][5]

Dans la boîte de dialogue **Ajouter structure**, agrandissez **Commun** sur la gauche et sélectionnez **Windows Azure Mobile Services**. Sélectionnez ensuite **Contrôleur de table de services Windows Azure Mobile**. Cliquez sur **Add**.

Dans la boîte de dialogue **Ajouter un contrôleur** :

1.  Sous **Classe de modèle**, sélectionnez Player.
2.  Sous **Classe du contexte de données**, sélectionnez LeaderboardContext.
3.  Donnez le nom « PlayerController » au contrôleur.
4.  Cliquez sur **Add**.

![][6]

Cette étape ajoute un fichier nommé PlayerController.cs au projet.

![][7]

Le contrôleur dérive de **TableController<t>**. Cette classe hérite de **ApiController**, mais est spécialisée pour Azure Mobile Services.

-   Routage : l'itinéraire par défaut pour un **TableController** est `/tables/{table_name}/{id}`, où *table\_name* correspond au nom de l'entité. L'itinéraire du contrôleur Player est donc */tables/player/{id}*. Cette convention de routage rend **TableController** cohérent avec l'[API REST][API REST] Mobile Services.
-   Accès aux données : pour les opérations de base de données, la classe **TableController** utilise l'interface **IDomainManager**, qui définit une abstraction pour l'accès aux données. La structure utilise **EntityDomainManager**, qui est une implémentation concrète de **IDomainManager** qui encapsule un contexte EF.

À présent, ajoutez un deuxième contrôleur pour les entités PlayerRank. Suivez les mêmes étapes, mais choisissez PlayerRank pour la classe de modèle. Utilisez la même classe de contexte de données, n'en créez pas une nouvelle. Donnez le nom « PlayerRankController » au contrôleur.

## Utilisation d'un objet de transfert de données pour renvoyer des entités associées

N'oubliez pas que `PlayerRank` possède une entité `Player` associée :

    public class PlayerRank : EntityData
    {
        public int Score { get; set; }
        public int Rank { get; set; }

        [ForeignKey("Id")]
        public virtual Player Player { get; set; }
    }

La bibliothèque cliente Mobile Services ne prend pas en charge les propriétés de navigation, qui ne seront pas sérialisées. Par exemple, voici la réponse HTTP brute pour GET `/tables/PlayerRank` :

    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Pragma: no-cache
    Content-Length: 97
    Content-Type: application/json; charset=utf-8
    Expires: 0
    Server: Microsoft-IIS/8.0
    Date: Mon, 21 Apr 2014 17:58:43 GMT

    [{"id":"1","rank":1,"score":150},{"id":"2","rank":3,"score":100},{"id":"3","rank":1,"score":150}]

Notez que `Player` n'est pas inclus dans l'objet Graph. Pour inclure le joueur, nous pouvons aplatir l'objet Graph en définissant un *objet de transfert de données*.

Un objet de transfert de données est un objet qui définit la façon dont les données sont envoyées sur le réseau. Les objets de transfert de données sont utiles lorsque vous souhaitez que le format câble soit différent de votre modèle de base de données. Pour créer un objet de transfert de données pour `PlayerRank`, ajoutez une nouvelle classe nommée `PlayerRankDto` dans le dossier DataObjects.

    namespace Leaderboard.DataObjects
    {
        public class PlayerRankDto
        {
            public string Id { get; set; }
            public string PlayerName { get; set; }
            public int Score { get; set; }
            public int Rank { get; set; }
        }
    }

Dans la classe `PlayerRankController`, nous utiliserons la méthode LINQ **Select** pour convertir les instances `PlayerRank` en instances `PlayerRankDto`. Mettez à jour les méthodes de contrôleur `GetAllPlayerRank` et `GetPlayerRank` comme suit :

    // GET tables/PlayerRank
    public IQueryable<PlayerRankDto> GetAllPlayerRank()
    {
        return Query().Select(x => new PlayerRankDto()
        {
            Id = x.Id,
            PlayerName = x.Player.Name,
            Score = x.Score,
            Rank = x.Rank
        });
    }

    // GET tables/PlayerRank/48D68C86-6EA6-4C25-AA33-223FC9A27959
    public SingleResult<PlayerRankDto> GetPlayerRank(string id)
    {
        var result = Lookup(id).Queryable.Select(x => new PlayerRankDto()
        {
            Id = x.Id,
            PlayerName = x.Player.Name,
            Score = x.Score,
            Rank = x.Rank
        });

        return SingleResult<PlayerRankDto>.Create(result);
    }

Suite à ces modifications, les deux méthodes GET renvoient des objets `PlayerRankDto` au client. La propriété `PlayerRankDto.PlayerName` est définie sur le nom du joueur. Voici un exemple de réponse après cette modification :

    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Pragma: no-cache
    Content-Length: 160
    Content-Type: application/json; charset=utf-8
    Expires: 0
    Server: Microsoft-IIS/8.0
    Date: Mon, 21 Apr 2014 19:57:08 GMT

    [{"id":"1","playerName":"Alice","score":150,"rank":1},{"id":"2","playerName":"Bob","score":100,"rank":3},{"id":"3","playerName":"Charles","score":150,"rank":1}]

Notez que la charge utile JSON inclut désormais les noms des joueurs.

Une autre option consiste à utiliser AutoMapper au lieu des instructions LINQ Select. Cette option nécessite du code de configuration supplémentaire, mais permet le mappage automatique des entités de domaine aux objets de transfert de données. Pour plus d'informations, consultez le billet de blog [Mappage entre les types de base de données et les types de client dans le serveur principal .NET avec AutoMapper][Mappage entre les types de base de données et les types de client dans le serveur principal .NET avec AutoMapper].

## Définition d'une API personnalisée pour l'envoi de scores

L'entité `PlayerRank` comprend une propriété `Rank`. Cette valeur est calculée par le serveur et les clients ne doivent pas la définir. Au lieu de cela, les clients utiliseront une API personnalisée pour soumettre un score de joueur. Lorsque le serveur obtient un nouveau score, il met à jour tous les rangs des joueurs.

Tout d'abord, ajoutez une classe nommée `PlayerScore` au dossier DataObjects.

    namespace Leaderboard.DataObjects
    {
        public class PlayerScore
        {
            public string PlayerId { get; set; }
            public int Score { get; set; }
        }
    }

Dans la classe `PlayerRankController`, déplacez la variable `LeaderboardContext` du constructeur vers une variable de classe :

    public class PlayerRankController : TableController<PlayerRank>
    {
        // Add this:
        LeaderboardContext context = new LeaderboardContext();

        protected override void Initialize(HttpControllerContext controllerContext)
        {
            base.Initialize(controllerContext);

            // Delete this:
            // LeaderboardContext context = new LeaderboardContext();
            DomainManager = new EntityDomainManager<PlayerRank>(context, Request, Services);
        }

Supprimez les méthodes suivantes de `PlayerRankController` :

-   `PatchPlayerRank`
-   `PostPlayerRank`
-   `DeletePlayerRank`

Ajoutez ensuite le code suivant à `PlayerRankController` :

    [Route("api/score")]
    public async Task<IHttpActionResult> PostPlayerScore(PlayerScore score)
    {
        // Does this player exist?
        var count = context.Players.Where(x => x.Id == score.PlayerId).Count();
        if (count < 1)
        {
            return BadRequest();
        }

        // Try to find the PlayerRank entity for this player. If not found, create a new one.
        PlayerRank rank = await context.PlayerRanks.FindAsync(score.PlayerId);
        if (rank == null)
        {
            rank = new PlayerRank { Id = score.PlayerId };
            rank.Score = score.Score;
            context.PlayerRanks.Add(rank);
        }
        else
        {
            rank.Score = score.Score;
        }

        await context.SaveChangesAsync();

        // Update rankings
        // See http://stackoverflow.com/a/575799
        const string updateCommand =
            "UPDATE r SET Rank = ((SELECT COUNT(*)+1 from {0}.PlayerRanks " +
            "where Score > (select score from {0}.PlayerRanks where Id = r.Id)))" +
            "FROM {0}.PlayerRanks as r";

        string command = String.Format(updateCommand, ServiceSettingsDictionary.GetSchemaName());
        await context.Database.ExecuteSqlCommandAsync(command);

        return Ok();
    }

La méthode `PostPlayerScore` prend une instance `PlayerScore` en entrée (le client enverra le `PlayerScore` dans une requête HTTP POST). La méthode effectue les actions suivantes :

1.  Elle ajoute un nouveau `PlayerRank` pour le joueur, s'il n'en existe pas déjà dans la base de données.
2.  Elle met à jour le score du joueur.
3.  Elle exécute une requête SQL qui met à jour tous les rangs des joueurs par lots.

L'attribut **[Route]** pour définir un itinéraire personnalisé pour cette méthode est le suivant :

    [Route("api/score")]

Vous pouvez également placer la méthode dans un contrôleur séparé. Chaque méthode n'offre pas d'avantage particulier. Tout dépend de la façon dont vous souhaitez organiser votre code.
Pour en savoir plus sur l'attribut **[Route]**, consultez la page [Attribut du routage dans l'API Web][Attribut du routage dans l'API Web].

## Création de l'application Windows Store

Cette section fournit une description de l'application Windows Store qui consomme le service mobile. Cependant, nous ne nous concentrerons pas sur le XAML ou l'interface utilisateur. Au lieu de cela, nous nous pencherons sur la logique d'application. Vous pouvez télécharger le projet complet [ici][ici].

Ajoutez un nouveau projet Windows Store App à la solution. Nous avons utilisé le modèle Application vide (Windows).

![][8]

Utilisez le gestionnaire de package NuGet pour ajouter la bibliothèque cliente Mobile Services. Dans Visual Studio, dans le menu **Outils**, sélectionnez **Gestionnaire de package NuGet**. Sélectionnez ensuite **Console du gestionnaire de package**. Dans la fenêtre Console du gestionnaire de package, entrez la commande suivante.

    Install-Package WindowsAzure.MobileServices -Project LeaderboardApp

Le commutateur -Project indique le projet dans lequel installer le package.

## Ajout de classes de modèle

Créez un dossier nommé Models et ajoutez les classes suivantes :

    namespace LeaderboardApp.Models
    {
        public class Player
        {
            public string Id { get; set; }
            public string Name { get; set; }
        }

        public class PlayerRank
        {
            public string Id { get; set; }
            public string PlayerName { get; set; }
            public int Score { get; set; }
            public int Rank { get; set; }
        }

        public class PlayerScore
        {
            public string PlayerId { get; set; }
            public int Score { get; set; }
        }
    }

Ces classes correspondent directement aux entités de données du service mobile.

## Création d'un modèle de vue

L'architecture Model-View-ViewModel (MVVM) est une variante de Model-View-Controller (MVC). Le modèle MVVM permet de séparer la logique de la présentation d'application.

-   Le modèle représente les données de domaine (joueur, rang et score du joueur).
-   Le modèle de vue est une représentation abstraite de la vue.
-   La vue affiche le modèle de vue et envoie l'entrée utilisateur au modèle de vue. Pour une application Windows Store, la vue est définie en XAML.

![][9]

Ajoutez une classe nommée `LeaderboardViewModel`.

    using LeaderboardApp.Models;
    using Microsoft.WindowsAzure.MobileServices;
    using System.ComponentModel;
    using System.Net.Http;
    using System.Threading.Tasks;

    namespace LeaderboardApp.ViewModel
    {
        class LeaderboardViewModel : INotifyPropertyChanged
        {
            MobileServiceClient _client;

            public LeaderboardViewModel(MobileServiceClient client)
            {
                _client = client;
            }
        }
    }

Implémentez **INotifyPropertyChanged** sur le modèle de vue, de sorte que ce dernier puisse participer à la liaison des données.

    class LeaderboardViewModel : INotifyPropertyChanged
    {
        MobileServiceClient _client;

        public LeaderboardViewModel(MobileServiceClient client)
        {
            _client = client;
        }

        // New code:
        // INotifyPropertyChanged implementation
        public event PropertyChangedEventHandler PropertyChanged;

        public void NotifyPropertyChanged(string propertyName)
        {
            if (PropertyChanged != null)
            {
                PropertyChanged(this,
                    new PropertyChangedEventArgs(propertyName));
            }
        }    
    }

Ensuite, ajoutez les propriétés observables. Le XAML lie les données à ces propriétés.

    class LeaderboardViewModel : INotifyPropertyChanged
    {
        // ...

        // New code:
        // View model properties
        private MobileServiceCollection<Player, Player> _Players;
        public MobileServiceCollection<Player, Player> Players
        {
            get { return _Players; }
            set
            {
                _Players = value;
                NotifyPropertyChanged("Players");
            }
        }

        private MobileServiceCollection<PlayerRank, PlayerRank> _Ranks;
        public MobileServiceCollection<PlayerRank, PlayerRank> Ranks
        {
            get { return _Ranks; }
            set
            {
                _Ranks = value;
                NotifyPropertyChanged("Ranks");
            }
        }

        private bool _IsPending;
        public bool IsPending
        {
            get { return _IsPending; }
            set
            {
                _IsPending = value;
                NotifyPropertyChanged("IsPending");
            }
        }

        private string _ErrorMessage = null;
        public string ErrorMessage
        {
            get { return _ErrorMessage; }
            set
            {
                _ErrorMessage = value;
                NotifyPropertyChanged("ErrorMessage");
            }
        }
    }

La propriété `IsPending` a la valeur true lorsqu'une opération asynchrone est en attente sur le service. La propriété `ErrorMessage` conserve tous les messages d'erreur du service.

Enfin, ajoutez des méthodes effectuant un appel dans la couche de service.

    class LeaderboardViewModel : INotifyPropertyChanged
    {
        // ...

        // New code:
        // Service operations
        public async Task GetAllPlayersAsync()
        {
            IsPending = true;
            ErrorMessage = null;

            try
            {
                IMobileServiceTable<Player> table = _client.GetTable<Player>();
                Players = await table.OrderBy(x => x.Name).ToCollectionAsync();
            }
            catch (MobileServiceInvalidOperationException ex)
            {
                ErrorMessage = ex.Message;
            }
            catch (HttpRequestException ex2)
            {
                ErrorMessage = ex2.Message;
            }
            finally
            {
                IsPending = false;
            }
        }

        public async Task AddPlayerAsync(Player player)
        {
            IsPending = true;
            ErrorMessage = null;

            try
            {
                IMobileServiceTable<Player> table = _client.GetTable<Player>();
                await table.InsertAsync(player);
                Players.Add(player);
            }
            catch (MobileServiceInvalidOperationException ex)
            {
                ErrorMessage = ex.Message;
            }
            catch (HttpRequestException ex2)
            {
                ErrorMessage = ex2.Message;
            }
            finally
            {
                IsPending = false;
            }
        }

        public async Task SubmitScoreAsync(Player player, int score)
        {
            IsPending = true;
            ErrorMessage = null;

            var playerScore = new PlayerScore()
            {
                PlayerId = player.Id,
                Score = score
            }; 
            
            try
            {
                await _client.InvokeApiAsync<PlayerScore, object>("score", playerScore);
                await GetAllRanksAsync();
            }
            catch (MobileServiceInvalidOperationException ex)
            {
                ErrorMessage = ex.Message;
            }
            catch (HttpRequestException ex2)
            {
                ErrorMessage = ex2.Message;
            }
            finally
            {
                IsPending = false;
            }
        }

        public async Task GetAllRanksAsync()
        {
            IsPending = true;
            ErrorMessage = null;

            try
            {
                IMobileServiceTable<PlayerRank> table = _client.GetTable<PlayerRank>();
                Ranks = await table.OrderBy(x => x.Rank).ToCollectionAsync();
            }
            catch (MobileServiceInvalidOperationException ex)
            {
                ErrorMessage = ex.Message;
            }
            catch (HttpRequestException ex2)
            {
                ErrorMessage = ex2.Message;
            }
            finally
            {
                IsPending = false;
            }
         }    
    }

## Ajout d'une instance MobileServiceClient

Ouvrez le fichier App.xaml.cs et ajoutez une instance **MobileServiceClient** à la classe `App`.

    // New code:
    using Microsoft.WindowsAzure.MobileServices;

    namespace LeaderboardApp
    {
        sealed partial class App : Application
        {
            // New code.
            // TODO: Replace 'port' with the actual port number.
            const string serviceUrl = "http://localhost:port/";
            public static MobileServiceClient MobileService = new MobileServiceClient(serviceUrl);


            // ...
        }
    }

Lorsque vous effectuez un débogage localement, le service mobile s'exécute sur IIS Express. Visual Studio attribue un numéro de port aléatoire, de sorte que l'URL locale est http://localhost:*port*, où *port* est le numéro de port. Pour obtenir le numéro de port, démarrez le service dans Visual Studio en appuyant sur F5 pour le débogage. Visual Studio lancera un navigateur et accédera à l'URL de service. Vous pouvez également trouver l'URL locale dans les propriétés du projet, sous **Web**.

## Création de la page principale

Dans la page principale, ajoutez une instance du modèle de vue. Définissez ensuite le modèle de vue en tant que **DataContext** pour la page.

    public sealed partial class MainPage : Page
    {
        // New code:
        LeaderboardViewModel viewModel = new LeaderboardViewModel(App.MobileService);

        public MainPage()
        {
            this.InitializeComponent();
            // New code:
            this.DataContext = viewModel;
        }

       // ...

Comme indiqué précédemment, nous ne verrons pas l'intégralité du XAML pour l'application. L'un des avantages du modèle MVVM est de séparer la présentation de la logique d'application. Il est donc facile de modifier l'interface utilisateur, si l'on ne trouve pas l'exemple d'application à notre goût.

La liste des joueurs s'affiche dans une zone **ListBox** :

    <ListBox Width="200" Height="400" x:Name="PlayerListBox" 
        ItemsSource="{Binding Players}" DisplayMemberPath="Name"/>

Les rangs s'affichent dans une liste **ListView** :

    <ListView x:Name="RankingsListView" ItemsSource="{Binding Ranks}" SelectionMode="None">
        <!-- Header and styles not shown -->
        <ListView.ItemTemplate>
            <DataTemplate>
                <Grid>
                    <Grid.ColumnDefinitions>
                        <ColumnDefinition Width="*"/>
                        <ColumnDefinition Width="2*"/>
                        <ColumnDefinition Width="*"/>
                    </Grid.ColumnDefinitions>
                    <TextBlock Text="{Binding Path=Rank}"/>
                    <TextBlock Text="{Binding Path=PlayerName}" Grid.Column="1"/>
                    <TextBlock Text="{Binding Path=Score}" Grid.Column="2"/>
                </Grid>
            </DataTemplate>
        </ListView.ItemTemplate>
    </ListView>

Toute la liaison des données se produit via le modèle de vue.

## Publication de votre service mobile

Lors de cette étape, vous allez publier votre service mobile sur Microsoft Azure et modifier l'application pour utiliser le service en ligne.

Dans l'Explorateur de solutions, cliquez avec le bouton droit sur le projet Classement, puis sélectionnez **Publier**.

![][10]

Dans la boîte de dialogue **Publier**, cliquez sur **Windows Azure Mobile Services**.

![][11]

Si vous n'êtes pas déjà connecté à votre compte Azure, cliquez sur **Se connecter**.

![][12]

Sélectionnez un service mobile existant ou cliquez sur **Nouveau** pour en créer un. Cliquez ensuite sur **OK** pour publier.

![][13]

Le processus de publication crée automatiquement la base de données. Vous n'avez pas besoin de configurer une chaîne de connexion.

Vous pouvez à présent connecter l'application de classement au service en ligne. Vous avez besoin de deux éléments :

-   L'URL du service
-   La clé de l'application

Vous pouvez obtenir ces éléments via le portail de gestion Azure. Dans le portail de gestion, cliquez sur **Mobile Services**, puis sur le service mobile. L'URL de service est répertoriée sous l'onglet du tableau de bord. Pour obtenir la clé d'application, cliquez sur **Gérer les clés**.

![][14]

Dans la boîte de dialogue **Gérer les clés d'accès**, copiez la valeur de la clé d'application.

![][15]

Transmettez l'URL de service et la clé d'application au constructeur **MobileServiceClient**.

    sealed partial class App : Application
    {
        // TODO: Replace these strings with the real URL and key.
        const string serviceUrl = "https://yourapp.azure-mobile.net/";
        const string appKey = "YOUR ACCESSS KEY";

        public static MobileServiceClient MobileService = new MobileServiceClient(serviceUrl, appKey);

       // ...

À présent, lorsque vous exécutez l'application, elle communique avec le service réel.

## Étapes suivantes

-   [En savoir plus sur Azure Mobile Services][En savoir plus sur Azure Mobile Services]
-   [En savoir plus sur l'API web][API web ASP.NET]
-   [Gestion des conflits d'écriture dans une base de données][Gestion des conflits d'écriture dans une base de données]
-   [Ajout de notifications Push][Ajout de notifications Push] ; par exemple, lorsqu'une personne ajoute un nouveau joueur ou met à jour un score.
-   [Prise en main de l'authentification][Prise en main de l'authentification]





  [API web ASP.NET]: http://asp.net/web-api
  [Vue d'ensemble]: #overview
  [À propos de l'exemple d'application]: #about-the-sample-app
  [Ajout de modèles de données]: #add-data-models
  [Ajout de contrôleurs d'API web]: #add-web-api-controllers
  [Utilisation d'un objet de transfert de données pour renvoyer des entités associées]: #use-a-dto-to-return-related-entities
  [Définition d'une API personnalisée pour l'envoi de scores]: #define-a-custom-api-to-submit-scores
  [Création de l'application Windows Store]: #create-the-windows-store-app
  [Ajout de classes de modèle]: #add-model-classes
  [Création d'un modèle de vue]: #create-a-view-model
  [Ajout d'une instance MobileServiceClient]: #add-a-mobileserviceclient-instance
  [Création de la page principale]: #create-the-main-page
  [Publication de votre service mobile]: #publish-your-mobile-service
  [Étapes suivantes]: #next-steps
  [Visual Studio 2013 Update 2]: http://go.microsoft.com/fwlink/p/?LinkID=390465
  [0]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/01leaderboard.png
  [1]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/02leaderboard.png
  [ici]: http://code.msdn.microsoft.com/Leaderboard-App-with-Azure-9acf63af
  [2]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/03leaderboard.png
  [3]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/04leaderboard.png
  [4]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/05leaderboard.png
  [EF Code First]: http://msdn.microsoft.com/fr-fr/data/ee712907#codefirst
  [gestion des conflits d'écriture de base de données]: http://azure.microsoft.com/fr-fr/documentation/articles/mobile-services-windows-store-dotnet-handle-database-conflicts/
  [propriété de navigation]: http://msdn.microsoft.com/fr-fr/data/jj713564.aspx
  [5]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/06leaderboard.png
  [6]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/07leaderboard.png
  [7]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/08leaderboard.png
  [API REST]: http://msdn.microsoft.com/fr-fr/library/azure/jj710104.aspx
  [Mappage entre les types de base de données et les types de client dans le serveur principal .NET avec AutoMapper]: http://blogs.msdn.com/b/azuremobile/archive/2014/05/19/mapping-between-database-types-and-client-type-in-the-net-backend-using-automapper.aspx
  [Attribut du routage dans l'API Web]: http://www.asp.net/web-api/overview/web-api-routing-and-actions/attribute-routing-in-web-api-2
  [8]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/10leaderboard.png
  [9]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/11leaderboard.png
  [10]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/12leaderboard.png
  [11]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/13leaderboard.png
  [12]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/14leaderboard.png
  [13]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/15leaderboard.png
  [14]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/16leaderboard.png
  [15]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-leaderboard/17leaderboard.png
  [En savoir plus sur Azure Mobile Services]: /fr-fr/develop/mobile/resources/
  [Gestion des conflits d'écriture dans une base de données]: /fr-fr/documentation/articles/mobile-services-windows-store-dotnet-handle-database-conflicts/
  [Ajout de notifications Push]: /fr-fr/documentation/articles/notification-hubs-windows-store-dotnet-get-started/
  [Prise en main de l'authentification]: /fr-fr/develop/mobile/tutorials/get-started-with-users-dotnet
