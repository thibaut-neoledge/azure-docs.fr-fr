## <a name="what-is"></a>Présentation de Mobile Services

Azure Mobile Services est une plateforme de développement d'applications mobiles hautement extensible qui vous permet d'ajouter des fonctionnalités avancées à vos applications d'appareil mobile à l'aide d'Azure.

Grâce à Mobile Services, vous pouvez effectuer les opérations suivantes :

+ **Générer des applications natives et interplateformes** : connectez vos applications iOS, Android, Windows, ou bien Xamarin ou Cordova (Phonegap) interplateformes à votre service mobile principal à l'aide de Kits de développement logiciel natifs.  
+ **Envoyer des notifications Push aux utilisateurs** : envoyez des notifications Push aux utilisateurs de votre application.
+ **Authentifier les utilisateurs** : tirez parti des fournisseurs d'identité répandus tels que Facebook et Twitter pour authentifier les utilisateurs de votre application.
+ **Stocker des données sur le cloud** : stockez les données utilisateur dans une base de données SQL (par défaut), dans des tables MongoDB, DocumentDB ou Azure ou dans des objets blob Azure. 
+ **Créer des applications hors connexion compatibles avec la synchronisation** : rendez vos applications opérationnelles en mode hors connexion et utilisez Mobile Services pour synchroniser les données en arrière-plan.
+ **Surveiller et adapter l'échelle de vos applications** : surveillez l'utilisation des applications et adapter l'échelle de votre serveur principal à mesure que la demande augmente. 

## <a name="concepts"> </a>Concepts de Mobile Services

Voici les fonctionnalités et les concepts importants de Mobile Services :

+ **Clé d'application :** valeur unique qui permet de limiter l'accès à votre service mobile à partir de clients aléatoires ; cette « clé » n'est pas un jeton de sécurité et ne permet pas d'authentifier les utilisateurs de votre application.    
+ **Serveur principal :** instance de service mobile qui prend en charge votre application. Un service mobile est implémenté sous la forme d’un projet d’API web ASP.NET (*serveur principal .NET*) ou d’un projet Node.js (*serveur principal JavaScript*).
+ **Fournisseur d'identité :** service externe, approuvé par Mobile Services, qui authentifie les utilisateurs de votre application. Les fournisseurs pris en charge incluent Facebook, Twitter, Google, Microsoft Account et Azure Active Directory. 
+ **Notification Push** : message initié par le service qui est envoyé à un appareil ou à un utilisateur enregistré via Azure Notification Hubs.
+ **Mise à l’échelle** : possibilité d’ajouter, moyennant un coût supplémentaire, davantage de puissance de traitement, de performance et de stockage avec le gain de popularité de votre application.
+ **Tâche planifiée** : code personnalisé exécuté en fonction d’une planification prédéfinie ou à la demande.

<!---HONumber=Oct15_HO3-->