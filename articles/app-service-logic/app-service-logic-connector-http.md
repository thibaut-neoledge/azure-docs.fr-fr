<properties
   pageTitle="Utilisation de l’écouteur HTTP et de l’action HTTP dans des applications logiques | Microsoft Azure App Service"
   description="Comment créer et configurer l’écouteur HTTP et l’action HTTP ou une application API et l'utiliser dans une application logique d’Azure App Service"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="anuragdalmia"
   manager="erikre"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="04/19/2016"
   ms.author="prkumar"/>


# Utilisation et ajout de l’écouteur HTTP et de l’action HTTP dans votre application logique

> [AZURE.NOTE] La prise en charge de ce connecteur ne sera plus assurée car sa fonctionnalité est désormais incluse par défaut en tant que **déclencheur manuel** lorsque vous créez des applications logique. Nous vous recommandons de mettre à niveau toutes vos applications logiques qui utilisent ce connecteur. Cette version de l’article s’applique à la version du schéma 2014-12-01-preview des applications logiques.

Connectez-vous directement aux ressources HTTP pour écouter les demandes HTTP et configurer des demandes web HTTP. Il existe certains scénarios où vous devez travailler avec des connexions HTTP directes, notamment :

1.	pour développer une application logique qui prend en charge un frontal interactif d'utilisateur web ou mobile,
2.	pour obtenir et traiter les données d'un service web qui n'a pas de connecteur prêt à l'emploi,
3.	pour effectuer des actions déjà exposées en tant que service web, mais non disponibles sous forme d'application API.

Dans ces cas de figure, il existe deux options :

1. **Écouteur HTTP** : ce connecteur sert de déclencheur et écoute les requêtes HTTP sur un point de terminaison configuré. Quand un appel est reçu sur le point de terminaison configuré, il déclenche une nouvelle instance du flux et transfère les données reçues dans la requête au flux à des fins de traitement. Il peut également être configuré pour répondre automatiquement à la demande entrante quand le flux a démarré ou vous permettre de construire une réponse basée sur l'exécution du flux et d'envoyer une réponse à l'appelant.
2. **Action HTTP** : celle-ci vous permet de configurer une demande web pour n’importe quel point de terminaison disponible sur Internet. Elle obtient une réponse en retour, et la met à disposition des actions supplémentaires du flux pour utilisation.

Les applications logiques peuvent se déclencher selon diverses sources de données et proposent des connecteurs pour obtenir et traiter les données dans le cadre du flux. Vous pouvez ajouter le connecteur HTTP à votre flux d’entreprise et traiter les données dans le cadre de ce flux de travail dans une application logique.

## Création d'un écouteur HTTP pour votre application logique
Un connecteur peut être créé dans une application logique ou directement à partir d'Azure Marketplace. Pour créer un connecteur à partir de Marketplace :

1. Dans le tableau d'accueil Azure, sélectionnez **Marketplace**.
2. Recherchez « HTTP », sélectionnez Écouteur HTTP et sélectionnez **Créer**.
3.	Configurez l'écouteur HTTP comme suit : ![][1]

4.	Quand vous configurez les paramètres du package, vous voyez l'option suivante qui détermine si l'écouteur doit répondre automatiquement ou vous demander d'envoyer une réponse explicite. Affectez à cette option la valeur **False** pour envoyer votre propre réponse : ![][2]

5.	Cliquez sur **OK** pour créer.
6.	Une fois que l'instance de l'application API est créée, ouvrez les paramètres de configuration de la sécurité. L'écouteur HTTP prend actuellement en charge l'authentification de base. Vous pouvez la configurer à l'aide de l'option Sécurité quand vous ouvrez l'écouteur HTTP : ![][3]
  
	**Problème connu** *Les paramètres de sécurité affichent « None » comme valeur par défaut, mais ils ne sont pas définis. Vous devez modifier le paramètre de base et rétablir None avant de l’enregistrer pour vous assurer que l’écouteur HTTP est configuré correctement.*

7. Enfin, définissez les paramètres de sécurité de l’application API en Public (anonyme) pour autoriser les clients externes à accéder au point de terminaison. Ce paramètre est disponible sous « Tous les paramètres > Paramètres de l’application » de l’application API d’écouteur HTTP : ![][10]

Une fois cette opération effectuée, vous pouvez alors créer une application logique pour utiliser l'écouteur HTTP.

## Utilisation de l'écouteur HTTP dans votre application logique
Une fois votre application API créée, vous pouvez utiliser l'écouteur HTTP comme déclencheur pour votre application logique. Pour cela, vous devez procéder comme suit :

4.	Créer une application logique.
5.	Ouvrez « Déclencheurs et actions » pour ouvrir le concepteur d’applications logiques et configurer votre flux. L’écouteur HTTP est répertorié dans la galerie. Sélectionnez-le.
6.	Vous pouvez maintenant définir la méthode HTTP et l’URL relative sur laquelle vous voulez que l’écouteur déclenche le flux : ![][4] ![][5]

7.	Pour obtenir l'URI complet, double-cliquez sur l'écouteur HTTP pour afficher ses paramètres de configuration et copiez l'URL de l'hôte de votre application API : ![][6]
8.	Vous pouvez maintenant utiliser les données reçues dans la demande HTTP dans les autres actions du flux comme suit :![][7] ![][8]
9.	Enfin, pour envoyer une réponse, ajoutez un autre écouteur HTTP et sélectionnez l'action Envoyer une réponse HTTP. Affectez à l’ID de demande la valeur RequestID obtenue de l’écouteur HTTP, puis remplissez le corps de la réponse et l’état HTTP à renvoyer : ![][9]

## Utilisation de l'action HTTP
L'action HTTP est prise en charge de façon native par les applications logiques et ne requiert pas la création préalable d'une application API pour pouvoir être utilisée. Vous pouvez insérer une action HTTP à n'importe quel endroit dans votre application logique et choisir l'URI, les en-têtes et le corps de l'appel. L'action HTTP prend en charge plusieurs options de sécurité côté client. Consultez les [options de sécurité côté client](../scheduler/scheduler-outbound-authentication.md).

La sortie de l'action HTTP correspond aux en-têtes et corps, lesquels peuvent être utilisés plus en aval dans le flux, de la même façon que la sortie des autres actions et connecteurs est consommée.

## En faire plus avec votre connecteur
Maintenant que le connecteur est créé, vous pouvez l'ajouter à un flux d'entreprise à l'aide d'une application logique. Voir [Que sont les applications logiques ?](app-service-logic-what-are-logic-apps.md).

Affichez la référence d’API REST Swagger sur [Référence de connecteurs et d’applications API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

Vous pouvez également consulter les statistiques de performances et contrôler la sécurité du connecteur. Pour plus d'informations, consultez [Gestion et contrôle de vos connecteurs et applications API intégrés](app-service-logic-monitor-your-connectors.md).

> [AZURE.NOTE] Si vous souhaitez commencer à utiliser Azure Logic Apps avant d’ouvrir un compte Azure, accédez à [Try Logic Apps (Essayer Logic Apps)](https://tryappservice.azure.com/?appservice=logic). Vous pouvez créer immédiatement une application logique temporaire dans App Service. Aucune carte de crédit n’est requise ; vous ne prenez aucun engagement.

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

<!---HONumber=AcomDC_0420_2016-->