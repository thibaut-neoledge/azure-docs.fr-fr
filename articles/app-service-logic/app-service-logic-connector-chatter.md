<properties
   pageTitle="Application API du connecteur Chatter"
	description="Utilisation du connecteur Chatter"
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
	ms.date="08/19/2015"
	ms.author="sameerch"/>


# Utilisation du connecteur Chatter dans votre application logique

Le connecteur Chatter vous permet de vous connecter à Chatter et d'exécuter des tâches courantes, comme publier un message. Par exemple, vous pouvez rechercher un flux Chatter et lorsque vous trouvez un élément spécifique, vous pouvez publier ce message Chatter vers un groupe de ventes.

Vous pouvez ajouter le connecteur Chatter à flux de travail professionnel et traiter les données dans le cadre de ce flux de travail.

## Déclencheurs et actions

Un déclencheur démarre une nouvelle instance en fonction d'un événement spécifique, comme l'arrivée d'un nouveau message Chatter. Une action est le résultat, comme après la réception d'un nouveau message Chatter, puis la publication du message vers un autre groupe Chatter ou un autre site de réseau social, tel que Facebook ou Twitter.

Le connecteur Chatter peut être utilisé comme déclencheur ou action dans une application logique. Il prend également en charge les données aux formats JSON et XML. Le connecteur Chatter dispose des déclencheurs et actions suivants :

Déclencheurs | Actions
--- | ---
Nouveau message | <ul><li>Publier un message</li><li>Rechercher</li></ul>


## Création du connecteur Chatter pour votre application logique
Un connecteur peut être créé dans une application logique ou directement à partir d'Azure Marketplace. Pour créer un connecteur à partir de Marketplace :

1. Dans le tableau d'accueil Azure, sélectionnez **Marketplace**.
2. Recherchez « Connecteur Chatter », sélectionnez-le et sélectionnez **Créer**.
3. Entrez le nom, le plan App Service et d'autres propriétés :
	![][1]  
	- **Emplacement** : choisissez l’emplacement géographique dans lequel vous voulez déployer le connecteur.
	- **Abonnement** : choisissez un abonnement dans lequel vous souhaitez créer ce connecteur.
	- **Groupe de ressources** : sélectionnez ou créez un groupe de ressources où doit résider le connecteur.
	- **Plan d’hébergement web** : sélectionnez ou créez un plan d’hébergement web.
	- **Niveau de tarification** : choisissez un niveau de tarification pour le connecteur.
	- **Nom** : indiquez le nom de votre connecteur Chatter.

4. Sélectionnez **Créer**.


## Utilisation du connecteur Chatter dans votre application logique
Une fois votre application API créée, vous pouvez utiliser le connecteur Chatter comme déclencheur ou action dans votre application logique. Pour ce faire :

1. Dans votre application logique, ouvrez **Déclencheurs et actions** pour ouvrir le concepteur d'applications logiques et configurer votre flux.

2. Le connecteur Chatter est répertorié dans la galerie :

	![][4]
3. Sélectionnez le connecteur Chatter pour l'ajouter automatiquement dans le concepteur. Sélectionnez **Autoriser**, entrez vos informations d'identification, puis sélectionnez **Autoriser** :
	![][5]
	![][6]
	![][7]

Vous pouvez maintenant utiliser le connecteur Chatter dans le flux. Vous pouvez utiliser le nouveau message récupéré du déclencheur Chatter (« Nouveau message ») dans d’autres actions du flux. Configurez les propriétés d’entrée du déclencheur Chatter comme suit :

**ID de groupe** : entrez l'ID du groupe à partir duquel le nouveau message doit être récupéré. Si l'ID de groupe n'est pas spécifié, le nouveau message est récupéré du flux de l'utilisateur :
	![][8]
	![][9]


De même, vous pouvez utiliser l’action Chatter dans le flux pour publier un message en sélectionnant l’action « Publier le message ». Configurez les propriétés d'entrée pour l'action « Publier le message » comme suit : - **Texte du message** : contenu texte du message à publier - **ID de groupe** : spécifiez l'ID du groupe où le nouveau message doit être publié. Si l'ID de groupe n'est pas indiqué, le message sera publié dans le flux de l'utilisateur. - **Nom de fichier** : nom du fichier à associer à ce message - **Données de contenu** : données de contenu de la pièce jointe - **Type de contenu** : type de contenu de la pièce jointe - **Encodage du transfert de contenu** : encodage du transfert du contenu de la pièce jointe (« none » | « Base64 ») - **Mentions** : tableau des noms d'utilisateur à baliser dans ce message - **Hashtags** : tableau des hashtags à publier avec le message.

![][10]
![][11]

## En faire plus avec votre connecteur
Maintenant que le connecteur est créé, vous pouvez l'ajouter à un flux d'entreprise à l'aide d'une application logique. Voir [Que sont les applications logiques ?](app-service-logic-what-are-logic-apps.md).

Affichez la référence d’API REST Swagger sur [Référence de connecteurs et d’applications API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

Vous pouvez également consulter les statistiques de performances et contrôler la sécurité du connecteur. Pour plus d'informations, consultez [Gestion et contrôle de vos connecteurs et applications API intégrés](app-service-logic-monitor-your-connectors.md).


<!--Image references-->
[1]: ./media/app-service-logic-connector-chatter/img1.PNG
[2]: ./media/app-service-logic-connector-chatter/img2.PNG
[3]: ./media/app-service-logic-connector-chatter/img3.png
[4]: ./media/app-service-logic-connector-chatter/img4.png
[5]: ./media/app-service-logic-connector-chatter/img5.PNG
[6]: ./media/app-service-logic-connector-chatter/img6.PNG
[7]: ./media/app-service-logic-connector-chatter/img7.PNG
[8]: ./media/app-service-logic-connector-chatter/img8.PNG
[9]: ./media/app-service-logic-connector-chatter/img9.PNG
[10]: ./media/app-service-logic-connector-chatter/img10.PNG
[11]: ./media/app-service-logic-connector-chatter/img11.PNG

<!----HONumber=August15_HO8-->