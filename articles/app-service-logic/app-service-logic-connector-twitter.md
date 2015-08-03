<properties
   pageTitle="Utilisation du connecteur Twitter dans Microsoft Azure App Service"
   description="Utilisation de l’application API du connecteur Twitter"
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
   ms.date="07/14/2015"
   ms.author="sameerch"/>


# Connecteur Twitter

Connectez-vous à votre flux Twitter pour publier des tweets, lire ceux publiés sur votre fil d’actualités et celui de vos amis et découvrir les abonnés à votre compte Twitter. Les connecteurs peuvent être utilisés dans les applications logiques pour extraire, traiter ou placer des données dans le cadre d'un « flux ». En utilisant le connecteur Twitter dans votre flux de travail, vous pouvez obtenir une multitude de scénarios. Vous pouvez, par exemple :

- Lire les nouveaux tweets associés à un mot-clé ou à un texte donné. Lorsqu’un nouveau tweet est récupéré, il déclenche une nouvelle instance de votre flux de travail et transfère les données vers le connecteur suivant dans votre flux de travail. Par exemple, vous créez un connecteur Twitter et utilisez le déclencheur « Nouveau tweet après recherche » pour surveiller #peanutbutterandjelly. Chaque fois qu’un nouveau tweet contenant #peanutbutterandjelly est publié, votre flux de travail (c’est-à-dire l’application logique) démarre automatiquement.
- À l’aide de différentes actions, telles que « Rechercher des tweets », vous récupérez la réponse et l’utilisez dans votre flux de travail. Par exemple, vous pouvez rechercher des tweets contenant le nom de votre société. Si des tweets sont détectés, vous pouvez utiliser une application logique afin d’écrire ces données dans une base de données SQL Server. Utilisez ensuite les données de SQL Server pour déterminer ce qui est tweeté à propos de votre société. 


## Déclencheurs et actions
Les *déclencheurs* sont des événements qui se produisent. Par exemple, un nouveau tweet peut déclencher ou démarrer un flux de travail ou un processus. Une *action* est le résultat d’un élément. Par exemple, vous pouvez rechercher un tweet spécifique et, une fois trouvé, envoyer un courrier électronique ou mettre à jour une base de données.

Le connecteur Twitter peut être utilisé comme déclencheur ou action dans une application logique. Il prend également en charge les données aux formats JSON et XML.

Le connecteur Twitter dispose des déclencheurs et actions suivants :

Déclencheurs | Actions
--- | ---
Nouveau Tweet après recherche | <ul><li>Consulter le fil d’actualités de l’utilisateur</li><li>Rechercher des tweets</li><li>Tweeter</li><li>Consulter le fil d’actualités des mentions</li><li>Consulter votre fil d’actualités</li><li>Obtenir des abonnés</li><li>Obtenir des amis</li><li>Consulter les détails de l’utilisateur</li><li>Tweeter un message à un utilisateur</li><li>Envoyer un message direct</li></ul>

> [AZURE.IMPORTANT]Le déclencheur **Nouveau Tweet** a été archivé. Actuellement, il est toujours disponible en tant qu’opération avancée et peut être utilisé. L’action **Retweeter** est supprimée et n’est plus prise en charge. Si vous utilisez l’action Retweeter, elle échoue lors de l’exécution. Par conséquent, supprimez l’action Retweeter de vos applications logiques.


## Création d’un connecteur Twitter
Un connecteur peut être créé dans une application logique ou directement à partir d'Azure Marketplace. Pour créer un connecteur à partir de Marketplace :

1. Dans le tableau d'accueil Azure, sélectionnez **Marketplace**.
2. Recherchez « Connecteur Twitter ».
3. Entrez le nom, le plan App Service et d’autres propriétés :

	![][1]
4.	Cliquez sur **Create**.


## Utilisation du connecteur Twitter dans votre application logique
Une fois votre application API créée, vous pouvez utiliser le connecteur Twitter comme déclencheur/action pour votre application logique. Pour ce faire :

1.	Créez une application logique ou ouvrez une application logique existante :

	![][2]
2.	Ouvrez **Déclencheurs et actions** pour ouvrir le concepteur d’applications logiques :

	![][3]
3.	Le connecteur Twitter est répertorié sur le côté droit. Sélectionnez-le pour l’ajouter automatiquement à votre application logique :

	![][4]
4.	Sélectionnez **Autoriser**, entrez vos informations d’identification Twitter, puis sélectionnez **Autoriser l’application** :

	![][5]


Vous pouvez maintenant configurer le connecteur Twitter pour créer votre flux de travail. Vous pouvez utiliser les tweets récupérés par le déclencheur Twitter dans d’autres actions du flux :

![][6]

De la même façon, vous pouvez utiliser les actions Twitter dans votre flux. Sélectionnez une action Twitter et configurez les entrées de cette action :

![][7] ![][8]

## En faire plus avec votre connecteur
Maintenant que le connecteur est créé, vous pouvez l'ajouter à un flux d'entreprise à l'aide d'une application logique. Voir [Que sont les applications logiques ?](app-service-logic-what-are-logic-apps.md).

Créez les applications API à l’aide des API REST. Consultez la page [Référence de connecteurs et d’applications API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

Vous pouvez également consulter les statistiques de performances et contrôler la sécurité du connecteur. Consultez la page [Gestion et contrôle de vos connecteurs et applications API intégrés](app-service-logic-monitor-your-connectors.md).

	<!--Image references-->
[1]: ./media/app-service-logic-connector-twitter/img1.png
[2]: ./media/app-service-logic-connector-twitter/img2.png
[3]: ./media/app-service-logic-connector-twitter/img3.png
[4]: ./media/app-service-logic-connector-twitter/img4.png
[5]: ./media/app-service-logic-connector-twitter/img5.png
[6]: ./media/app-service-logic-connector-twitter/triggers.png
[7]: ./media/app-service-logic-connector-twitter/img7.png
[8]: ./media/app-service-logic-connector-twitter/actions.png

<!---HONumber=July15_HO4-->