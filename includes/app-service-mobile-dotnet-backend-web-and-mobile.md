Cette rubrique vous montre comment créer une application avec un client mobile et web. Vous allez créer une application mobile et une application web et utiliser la même base de données sous-jacente pour les deux.

Vous allez commencer par créer un serveur principal d'applications mobiles et une simple application *To do list* qui stocke les données d'application dans ce nouveau serveur principal d'applications mobiles. Ce serveur principal utilise les langages .NET pris en charge pour la logique métier côté serveur. L’application cliente peut utiliser toutes les plateformes clientes prises en charge par l’application mobile, notamment iOS, Windows, Xamarin iOS et Xamarin Android.

Ensuite, vous allez créer une application web en utilisant la même base de données que votre application mobile. À la fin du didacticiel, vous disposerez d’un client web et d’un client mobile qui fonctionnent avec les mêmes données.

> [!NOTE]
> Si vous voulez vous familiariser avec Azure App Service avant d’ouvrir un compte Azure, accédez à la page [Essayer App Service](http://go.microsoft.com/fwlink/?LinkId=523751). Vous pourrez créer immédiatement et gratuitement une application de départ temporaire dans App Service. Aucune carte de crédit n’est requise ; vous ne prenez aucun engagement.
> 
> 

## Créer un serveur principal d’applications mobiles et un client
* Suivez les étapes du didacticiel [Créer une application mobile] pour créer un serveur principal d'applications mobiles et un client. Vous pouvez utiliser toutes les plateformes clientes prises en charge par les applications mobiles, notamment iOS, Windows, Xamarin iOS et Xamarin Android.
* Vérifiez que vous avez déployé le serveur principal de votre application mobile vers Azure et que vous connectez votre application cliente mobile au serveur principal hébergé. Le projet de code d’application mobile utilise Entity Framework Code First et initialise la base de données après la première requête REST d’une application cliente mobile.

## Publier une API web TodoList à partir de Visual Studio
Dans cette section, vous allez créer une application web à l’aide d’un exemple de solution d’application web. Vous allez modifier cet exemple pour utiliser le nom du même schéma de base de données et la même chaîne de connexion que l’application mobile.

> [!NOTE]
> Avant d’effectuer ces étapes, assurez-vous d’avoir initialisé la base de données du serveur principal de votre application mobile en lui connectant un client, faute de quoi l’application web ne pourra pas se connecter à la même base de données.
> 
> 

1. Dans le [portail Azure](https://portal.azure.com/), créez une application web en utilisant le même groupe de ressources et le même plan d'hébergement que votre application mobile.
2. Téléchargez l'exemple de solution [MultiChannelToDo] et ouvrez-le dans Visual Studio. La solution contient à la fois un projet d’API web et un projet d’application web pour l’interface utilisateur du client web.
3. Dans le projet d’API web, modifiez le fichier MultiChannelToDoContext.cs. Dans `OnModelCreating`, mettez à jour le nom du schéma pour qu'il soit identique à celui de votre application mobile :
   
        modelBuilder.HasDefaultSchema("your_mobile_app"); // your service name, replacing dashes with underscore
4. À présent, vous allez obtenir la chaîne de connexion de l’application mobile à partir du portail Azure :
   
   * Dans le portail, sélectionnez votre application mobile, puis cliquez sur la partie intitulée **Code utilisateur**.
   * Dans le panneau qui s'ouvre, sélectionnez **Tous les paramètres**, puis **Paramètres de l'application**.
   * Dans **Chaînes de connexion**, cliquez sur **Afficher les chaînes de connexion**. Copiez la valeur du paramètre **MS\_TableConnectionString**. Il s’agit de la chaîne de connexion utilisée par votre application mobile pour se connecter à la base de données SQL.
5. Dans Visual Studio, cliquez avec le bouton droit sur le projet d'API web, puis sélectionnez **Publier**. Sélectionnez **Azure Web Apps** comme cible de publication, puis l'application web que vous avez créée ci-dessus. Cliquez sur **Suivant** jusqu'à l'affichage de la section **Paramètres** de l'assistant Publier le site Web.
6. Dans la section **Bases de données**, collez la chaîne de connexion de l'application mobile comme valeur du paramètre **MultiChannelToDoContext**. Cochez uniquement la case **Utiliser cette chaîne de connexion au moment de l'exécution**.
7. Une fois que votre API web a été publiée dans Azure, une page de confirmation s’affiche. Copiez l’URL de votre service publié.

## Publier une interface utilisateur de client web TodoList à partir de Visual Studio
Dans cette section, vous allez utiliser un exemple d’application cliente web implémenté avec AngularJS. Vous allez ensuite utiliser Visual Studio pour publier le projet vers une nouvelle application web App Service hébergée dans Azure.

1. Dans Visual Studio, ouvrez le projet **MultiChannelToDo.Web**. Modifiez le fichier `js/service/ToDoService.js` en ajoutant l'URL à l'API web que vous venez de publier :
   
        var apiPath = "https://your-web-api-site-name.azurewebsites.net";
2. Cliquez avec le bouton droit sur le projet **MultiChannelToDo.Web** et sélectionnez **Publier**.
3. Dans l'assistant **Publier le site Web**, sélectionnez **Azure Web App** comme cible de publication, puis créez une application web sans base de données.
4. Une fois votre projet publié, l’interface utilisateur web s’affiche dans votre navigateur.

## Tester les applications mobiles et web
1. Dans l’interface utilisateur web, ajoutez ou modifiez quelques tâches.
   
    ![Affichage de l'application web dans le navigateur](./media/app-service-mobile-dotnet-backend-web-and-mobile/web-app-in-browser.png)
2. Exécutez l'application mobile que vous avez créée dans le didacticiel [Créer une application mobile]. Les mêmes tâches que celles de l’application web s’affichent.
   
    ![Affichage de l'application mobile Xamarin](./media/app-service-mobile-dotnet-backend-web-and-mobile/xamarin-ios-quickstart-device.png)

## Changements apportés
* Pour obtenir un guide présentant les modifications apportées dans le cadre de la transition entre Sites Web et App Service, consultez la page [Azure App Service et les services Azure existants](http://go.microsoft.com/fwlink/?LinkId=529714).
* Pour obtenir un guide présentant les modifications apportées dans le cadre de la transition entre l’ancien et le nouveau portail, consultez [Références sur la navigation dans le portail Azure](http://go.microsoft.com/fwlink/?LinkId=529715).

<!-- Links -->

[MultiChannelToDo]: https://github.com/Azure/mobile-services-samples/tree/web-mobile/MultiChannelToDo
[Créer une application mobile]: ../article/app-service-mobile/app-service-mobile-xamarin-ios-get-started.md

<!---HONumber=AcomDC_1203_2015-->