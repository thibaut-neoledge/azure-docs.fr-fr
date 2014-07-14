
## <a name="what-is"></a>Présentation de Mobile Services

Mobile Services est une offre de service Azure conçue pour faciliter la création d'applications mobiles hautement fonctionnelles utilisant Azure. Mobile Services réunit un ensemble de services Azure qui dotent vos applications de fonctionnalités principales. Mobile Services offre les fonctionnalités principales suivantes dans Azure pour la prise en charge de vos applications :

* Simplicité de la configuration et de la gestion des tables de stockage
  des données d'application.
* Intégration aux services de notification pour la remise de
  notifications Push à votre application.
* Intégration aux fournisseurs d'identité connus pour
  l'authentification.
* Contrôle granulaire pour l'autorisation de l'accès aux tables.
* Logique métier personnalisée sur le serveur.
* Intégration aux services cloud.
* Prise en charge de l'extensibilité d'une instance de service mobile.
* Analyse et journalisation des services.

Mobile Services fournit une bibliothèque cliente pour chaque plateforme prise en charge, ce qui facilite le développement d'applications sur votre plateforme utilisatrice de Mobile Services.

## <a name="concepts"> </a>Concepts

Voici les fonctionnalités et les concepts importants de Mobile Services :

<!--![1][1]-->

* **Clé de l'application :** valeur unique générée par Mobile Services, distribuée avec votre application et présentée dans les requêtes générées par le client. Bien qu'elle soit utile pour limiter l'accès à votre service mobile à partir de clients aléatoires, cette clé n'est pas sécurisée et elle ne doit pas être utilisée pour authentifier les utilisateurs de votre application.

* **Jeton d'authentification :** jeton d'accès généré par Mobile Services après l'authentification d'un utilisateur.

* **Schéma dynamique :** capacité de Mobile Services à ajouter automatiquement une colonne à une table à partir des champs de l'objet JSON envoyé dans une requête d'insertion ou de mise à jour. Le schéma dynamique doit être désactivé lorsque votre application est en production.

* **Fournisseur d'identité :** service externe, comme un compte Facebook, Twitter, Google ou Microsoft, servant à authentifier les utilisateurs de votre service mobile.

* **Autorisation :** niveau d'authentification minimal nécessaire pour appeler une opération de la table.

* **Notification Push :** message initié par le service qui est envoyé à un appareil ou à un utilisateur inscrit.

* **Mise à l'échelle :** possibilité d'ajouter, moyennant un coût supplémentaire, davantage de puissance de traitement, de performance et de stockage avec le gain de popularité de votre application.

* **Travail planifié :** code de script serveur exécuté en fonction d'une planification prédéfinie ou à la demande.

* **Script serveur :** code JavaScript pour logique métier personnalisée stockée sur le serveur. Ce code est inscrit dans une opération de table (lecture, insertion, mise à jour, suppression) ou dans un travail planifié.

* **Table :** les données utilisateur sont stockées dans les tables d'une base de données SQL. Vous pouvez créer ces tables dans le portail de gestion.

* **Opération de table :** requête HTTP reçue du client pour lire, insérer, mettre à jour ou supprimer des données de la table.

<!-- Images. -->