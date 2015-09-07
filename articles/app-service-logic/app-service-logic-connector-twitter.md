<properties
   pageTitle="Utilisation du connecteur Twitter dans des applications logiques | Microsoft Azure App Service"
	description="Comment créer et configurer le connecteur Twitter ou une application API et l'utiliser dans une application logique d’Azure App Service"
	services="app-service\logic"
	documentationCenter=".net,nodejs,java"
	authors="anuragdalmia"
	manager="dwrede"
	editor=""/>

<tags
   ms.service="app-service-logic"
	ms.devlang="multiple"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="integration"
	ms.date="08/23/2015"
	ms.author="sameerch"/>


# Prise en main du connecteur Twitter et ajout de celui-ci dans votre application logique
Connectez-vous à votre flux Twitter pour publier des tweets, lire ceux publiés sur votre fil d’actualités et celui de vos amis et découvrir les abonnés à votre compte Twitter. Les connecteurs peuvent être utilisés dans les applications logiques pour extraire, traiter ou placer des données dans le cadre d'un « flux ». En utilisant le connecteur Twitter dans votre flux de travail, vous pouvez obtenir une multitude de scénarios. Vous pouvez, par exemple :

- Lire les nouveaux tweets associés à un mot-clé ou à un texte donné. Lorsqu’un nouveau tweet est récupéré, il déclenche une nouvelle instance de votre flux de travail et transfère les données vers le connecteur suivant dans votre flux de travail. Par exemple, vous créez un connecteur Twitter et utilisez le déclencheur « Nouveau tweet après recherche » pour surveiller #peanutbutterandjelly. Chaque fois qu’un nouveau tweet contenant #peanutbutterandjelly est publié, votre flux de travail (c’est-à-dire l’application logique) démarre automatiquement.
- À l’aide de différentes actions, telles que « Rechercher des tweets », vous récupérez la réponse et l’utilisez dans votre flux de travail. Par exemple, vous pouvez rechercher des tweets contenant le nom de votre société. Si des tweets sont détectés, vous pouvez utiliser une application logique afin d’écrire ces données dans une base de données SQL Server. Utilisez ensuite les données de SQL Server pour déterminer ce qui est tweeté à propos de votre société. 
- Utilisez tous les opérateurs sur [Recherche Twitter](https://twitter.com/search). Sélectionnez le lien **opérateurs**. Le connecteur Twitter prend en charge tous les opérateurs répertoriés.


## Déclencheurs et actions
Les *déclencheurs* sont des événements qui se produisent. Par exemple, un nouveau tweet peut déclencher ou démarrer un flux de travail ou un processus. Une *action* est le résultat d’un élément. Par exemple, vous pouvez rechercher un tweet spécifique et, une fois trouvé, envoyer un courrier électronique ou mettre à jour une base de données.

Le connecteur Twitter peut être utilisé comme déclencheur ou action dans une application logique. Il prend également en charge les données aux formats JSON et XML.

Le connecteur Twitter dispose des déclencheurs et actions suivants :

Déclencheurs | Actions
--- | ---
Nouveau Tweet après recherche | <ul><li>Consulter le fil d’actualités de l’utilisateur</li><li>Rechercher des tweets</li><li>Tweeter</li><li>Consulter le fil d’actualités des mentions</li><li>Consulter votre fil d’actualités</li><li>Obtenir des abonnés</li><li>Obtenir des amis</li><li>Consulter les détails de l’utilisateur</li><li>Tweeter un message à un utilisateur</li><li>Envoyer un message direct</li></ul>

Le déclencheur **Nouveau Tweet** a été archivé. Actuellement, il est toujours disponible en tant qu’opération avancée et peut être utilisé. L’action **Retweeter** est supprimée et n’est plus prise en charge. Si vous utilisez l’action Retweeter, elle échoue lors de l’exécution. Par conséquent, supprimez l’action Retweeter de vos applications logiques.


## Création d’un connecteur Twitter

> [AZURE.IMPORTANT]La création d'un connecteur Twitter nécessite actuellement l'inscription d'une application API avec Twitter. Vous pouvez enregistrer une application gratuitement sur [http://apps.twitter.com](http://apps.twitter.com). Vous aurez besoin de la clé et de la clé secrète d'API Twitter pour créer un connecteur.

Un connecteur peut être créé dans une application logique ou directement à partir d'Azure Marketplace. Pour créer un connecteur à partir de Marketplace :

1. Créez une application Twitter gratuite à l'adresse [http://apps.twitter.com](http://apps.twitter.com)
    * Lors de l'inscription de l'application, vous pouvez saisir n'importe quelle URL pour le site Web et laisser vide l'URL de rappel.
2. Dans le tableau d'accueil Azure, sélectionnez **Marketplace**.
3. Recherchez « Connecteur Twitter », sélectionnez-le et sélectionnez **Créer**.
4. Cliquez sur les « Paramètres du package » et copiez-collez la « Clé du client » de votre application Twitter dans le champ « clientId ». Copiez-collez la « Question secrète du client » de votre application Twitter dans le champ « clientSecret » : ![][10]
5. Entrez les autres paramètres requis : nom du connecteur, App Service et groupe de ressources.
6.	Cliquez sur **Create**.

> [AZURE.NOTE]Si vous souhaitez renforcer la sécurité de votre API Twitter avec l'URL de redirection, vous pouvez consultez la procédure [ici](app-service-logic-oauth-security.md)


## Utilisation du connecteur Twitter dans votre application logique
Une fois votre application API créée, vous pouvez utiliser le connecteur Twitter comme déclencheur/action pour votre application logique. Pour ce faire :

1.	Créez une application logique ou ouvrez une application logique existante : ![][2]
2.	Ouvrez **Déclencheurs et actions** pour ouvrir le concepteur d’applications logiques : ![][3]
3.	Le connecteur Twitter est répertorié sur le côté droit. Sélectionnez-le pour l’ajouter automatiquement à votre application logique : ![][4]
4.	Sélectionnez **Autoriser**, entrez vos informations d’identification Twitter, puis sélectionnez **Autoriser l’application** : ![][5]


Vous pouvez maintenant configurer le connecteur Twitter pour créer votre flux de travail. Vous pouvez utiliser les tweets récupérés par le déclencheur Twitter dans d’autres actions du flux : ![][6]

De la même façon, vous pouvez utiliser les actions Twitter dans votre flux. Sélectionnez une action Twitter et configurez les entrées de cette action : ![][7] ![][8]

## En faire plus avec votre connecteur
Maintenant que le connecteur est créé, vous pouvez l'ajouter à un flux d'entreprise à l'aide d'une application logique. Voir [Que sont les applications logiques ?](app-service-logic-what-are-logic-apps.md).

Affichez la référence d’API REST Swagger sur [Référence de connecteurs et d’applications API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

Vous pouvez également consulter les statistiques de performances et contrôler la sécurité du connecteur. Pour plus d'informations, consultez [Gestion et contrôle de vos connecteurs et applications API intégrés](app-service-logic-monitor-your-connectors.md).

<!--Image references-->
[1]: ./media/app-service-logic-connector-twitter/img1.png
[2]: ./media/app-service-logic-connector-twitter/img2.png
[3]: ./media/app-service-logic-connector-twitter/img3.png
[4]: ./media/app-service-logic-connector-twitter/img4.png
[5]: ./media/app-service-logic-connector-twitter/img5.png
[6]: ./media/app-service-logic-connector-twitter/triggers.png
[7]: ./media/app-service-logic-connector-twitter/img7.png
[8]: ./media/app-service-logic-connector-twitter/actions.png
[9]: ./media/app-service-logic-connector-twitter/settings.PNG
[10]: ./media/app-service-logic-connector-twitter/TwitterAPISettings.png

<!---HONumber=August15_HO9-->