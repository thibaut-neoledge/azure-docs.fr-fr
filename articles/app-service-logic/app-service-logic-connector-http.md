<properties
   pageTitle="Écouteur et connecteur HTTP"
   description="Utilisation de l'écouteur HTTP et de l'action HTTP dans votre logique d'application"
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
   ms.date="07/02/2015"
   ms.author="prkumar"/>


#Utilisation de l'écouteur HTTP et de l'action HTTP dans votre logique d'application#

Les applications logiques peuvent se déclencher selon diverses sources de données et proposent des connecteurs pour obtenir et traiter les données dans le cadre du flux. Toutefois, il existe certains scénarios où vous devez travailler avec des connexions HTTP directes, notamment :

1.	pour développer une application logique qui prend en charge un frontal interactif d'utilisateur web ou mobile,
2.	pour obtenir et traiter les données d'un service web qui n'a pas de connecteur prêt à l'emploi,
3.	pour effectuer des actions déjà exposées en tant que service web, mais non disponibles sous forme d'application API.


Pour ces scénarios, la galerie fournit deux options.

1. Écouteur HTTP : ce connecteur sert de déclencheur et écoute les requêtes HTTP sur un point de terminaison configuré. Quand un appel est reçu sur le point de terminaison configuré, il déclenche une nouvelle instance du flux et transfère les données reçues dans la requête au flux à des fins de traitement. Il peut également être configuré pour répondre automatiquement à la demande entrante quand le flux a démarré ou vous permettre de construire une réponse basée sur l'exécution du flux et d'envoyer une réponse à l'appelant.
2.	Action HTTP: celle-ci vous permet de configurer une demande web pour n’importe quel point de terminaison disponible sur Internet. Elle obtient une réponse en retour et la met à disposition des actions du flux pour utilisation.

##Création d'un écouteur HTTP pour votre application logique
Pour utiliser l'écouteur HTTP, vous devez d'abord créer une instance de l'application API de l'écouteur HTTP. Pour cela, procédez comme suit :

1.	Ouvrez Azure Marketplace à l’aide de l’option « + NOUVEAU » en bas à droite du portail Azure.
2.	Accédez à « Web et mobile > API Apps » et recherchez « Écouteur HTTP ».
3.	Configurez l'écouteur HTTP comme suit :

	![][1]

4.	Quand vous configurez les paramètres du package, vous voyez l'option suivante qui détermine si l'écouteur doit répondre automatiquement ou vous demander d'envoyer une réponse explicite. Affectez à cette option la valeur False pour pouvoir envoyer votre propre réponse.

	![][2]

5.	Cliquez sur OK pour créer.
6.	Une fois que l'instance de l'application API est créée, ouvrez les paramètres de configuration de la sécurité. L'écouteur HTTP prend actuellement en charge l'authentification de base. Vous pouvez la configurer à l'aide de l'option Sécurité quand vous ouvrez l'écouteur HTTP.

	![][3] <br> <br> <b><u>Problème connu</u></b><br> *Les paramètres de sécurité affichent « None » comme valeur par défaut, mais ils ne sont pas définis. Vous devez modifier le paramètre de base et rétablir None avant de l’enregistrer pour vous assurer que l’écouteur HTTP est configuré correctement.*

7. Enfin, vous devez définir les paramètres de sécurité de l’application API en Public (anonyme) pour autoriser les clients externes à accéder au point de terminaison. Ce paramètre est disponible sous « Tous les paramètres > Paramètres de l’application » de l’application API d’écouteur HTTP.

	![][10]

Une fois cette opération effectuée, vous pouvez alors créer une application logique dans le même groupe de ressources pour utiliser l'écouteur HTTP.

##Utilisation de l'écouteur HTTP dans votre application logique
Une fois votre application API créée, vous pouvez utiliser l'écouteur HTTP comme déclencheur pour votre application logique. Pour cela, vous devez procéder comme suit :

4.	Créez une application logique et choisissez le même groupe de ressources qui comporte l'écouteur HTTP.
5.	Ouvrez « Déclencheurs et actions » pour ouvrir le concepteur d’applications logiques et configurer votre flux. L'écouteur HTTP apparaît dans la section des éléments récemment utilisés dans la galerie située sur le côté droit. Sélectionnez-le.
6.	Vous pouvez maintenant définir la méthode HTTP et l’URL relative sur laquelle vous voulez que l’écouteur déclenche le flux.<br>

	![][4] ![][5]

7.	Pour obtenir l'URI complet, double-cliquez sur l'écouteur HTTP pour afficher ses paramètres de configuration et copiez l'URL de l'hôte de votre application API.


	![][6]
8.	Vous pouvez maintenant utiliser les données reçues dans la demande HTTP dans les autres actions du flux comme suit :<br> ![][7] ![][8]
9.	Enfin, pour envoyer une réponse, ajoutez un autre écouteur HTTP et sélectionnez l'action Envoyer une réponse HTTP. Affectez à l’ID de demande la valeur RequestID obtenue de l’écouteur HTTP, puis remplissez le corps de la réponse et l’état HTTP à renvoyer.![][9]

##Utilisation de l'action HTTP
L'action HTTP est prise en charge de façon native par les applications logiques et ne requiert pas la création préalable d'une application API pour pouvoir être utilisée. Vous pouvez insérer une action HTTP à n'importe quel endroit dans votre application logique et choisir l'URI, les en-têtes et le corps de l'appel. L'action HTTP prend en charge plusieurs options de sécurité côté client. Pour les utiliser, consultez l’article disponible [ici](http://aka.ms/logicapphttpauth).

La sortie de l'action HTTP correspond aux en-têtes et corps, lesquels peuvent être utilisés plus en aval dans le flux, de la même façon que la sortie des autres actions et connecteurs est consommée.

<!--Image references-->
[1]: ./media/app-service-logic-connector-http/1.png
[2]: ./media/app-service-logic-connector-http/2.png
[3]: ./media/app-service-logic-connector-http/3.png
[4]: ./media/app-service-logic-connector-http/4.png
[5]: ./media/app-service-logic-connector-http/5.png
[6]: ./media/app-service-logic-connector-http/6.png
[7]: ./media/app-service-logic-connector-http/7.png
[8]: ./media/app-service-logic-connector-http/8.png
[9]: ./media/app-service-logic-connector-http/9.png
[10]: ./media/app-service-logic-connector-http/10.png

<!---HONumber=July15_HO4-->