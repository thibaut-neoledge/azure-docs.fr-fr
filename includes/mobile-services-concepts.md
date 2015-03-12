## <a name="what-is"></a>Présentation de Mobile Services

Mobile Services est une offre de service Azure conçue pour faciliter la création d'applications mobiles hautement fonctionnelles utilisant Azure. Mobile Services réunit un ensemble de services Azure qui dotent vos applications de fonctionnalités principales. Mobile Services offre les fonctionnalités suivantes dans Azure pour la prise en charge de vos applications : 

+ Simplicité de la configuration et de la gestion des tables de stockage des données d'application. 
+ Intégration aux services de notification pour la remise de notifications Push à votre application.
+ Intégration aux fournisseurs d'identité connus pour l'authentification.
+ Contrôle granulaire pour l'autorisation de l'accès aux tables.
+ Logique métier personnalisée sur le serveur.
+ Intégration aux services cloud.
+ Prise en charge de l'extensibilité d'une instance de service mobile.
+ Analyse et journalisation des services.

Mobile Services fournit une bibliothèque cliente pour chaque plateforme prise en charge, ce qui facilite le développement d'applications sur votre plateforme utilisatrice de Mobile Services.

## <a name="concepts"></a>Concepts

Voici les fonctionnalités et les concepts importants de Mobile Services :

<!--![1][1]-->

+ **Service principal .NET :** ce type de service mobile est implémenté comme un projet d'API Web ASP.NET. Le code principal est implémenté en tant que contrôleurs WebAPI.
+ **Clé d'application :** une valeur unique générée par Mobile Services, distribuée avec votre application et présentée dans les requêtes générées par le client. Si elle est utile pour limiter l'accès à votre service mobile à partir de clients aléatoires, cette clé n'est pas sécurisée et ne doit pas être utilisée pour authentifier les utilisateurs de votre application.
+ **Jeton d'authentification :** jeton d'accès généré par Mobile Services après l'authentification d'un utilisateur.
+ **Fournisseur d'identité :** service externe, comme un compte Facebook, Twitter, Google ou Microsoft, ou Azure Active Directory, servant à authentifier les utilisateurs de votre service mobile.
+ **Back-end JavaScript :** ce type de service mobile est implémenté comme un service node.js. Le code principal est écrit en JavaScript.
+ **Autorisation :** niveau d'authentification minimal nécessaire pour appeler une opération de la table ou une API personnalisée.
+ **Notification d'émission :** message initié par le service qui est envoyé à un appareil ou à un utilisateur inscrit via Azure Notification Hubs.
+ **Montée en puissance parallèle :** possibilité d'ajouter, moyennant un coût supplémentaire, davantage de puissance de traitement, de performance et de stockage avec le gain de popularité de votre application.
+ **Tâche planifiée :** code personnalisé exécuté en fonction d'une planification prédéfinie ou à la demande.
+ **Script serveur :** code de logique métier personnalisé dans un service mobile principal JavaScript. Ce code est inscrit dans une opération de table (lire, insérer, mettre à jour, supprimer), une API personnalisée ou une tâche planifiée.
+ **Table :** les données utilisateur sont stockées dans des tables au sein d'une base de données SQL. Vous pouvez créer ces tables dans le portail de gestion.


<!-- Images. -->


  

\<!--HONumber=42-->
