<properties
   pageTitle="Utilisation du connecteur SAP dans des applications logiques | Microsoft Azure App Service"
   description="Comment créer et configurer le connecteur SAP ou une application API et l’utiliser dans une application logique d’Azure App Service"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="harishkragarwal"
   manager="erikre"
   editor=""/>

<tags
   ms.service="logic-apps"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="05/31/2016"
   ms.author="sameerch"/>


# Prise en main du connecteur SAP et ajout de celui-ci à votre application logique

>[AZURE.NOTE] Nous effectuons une mise à jour de ce connecteur sur la base des remarques des clients. Nous mettrons à jour cette page lorsqu’il sera prêt. Nous déconseillons d’utiliser le connecteur SAP avant que la nouvelle version ne soit disponible. Cette version de l’article s’applique à la version du schéma 2014-12-01-preview des applications logiques.

Se connecter au SAP local pour appeler RFC ou obtenir des métadonnées. Dans certains scénarios, vous pouvez être amené à utiliser SAP, installé localement et derrière le pare-feu. En exploitant le connecteur SAP dans votre flux, vous pouvez couvrir un large éventail de scénarios. Quelques exemples :

1.	Exposer une section des données résidant dans votre SAP via un frontal web ou d'utilisateur mobile.
2.	Publier des données sur votre SAP après leur traitement approprié.
3.	Extraire des données de SAP pour les utiliser dans un processus d'entreprise.

Les connecteurs peuvent être utilisés dans les applications logiques pour extraire, traiter ou placer des données dans le cadre d’un flux. Vous pouvez ajouter le connecteur SAP à votre flux de travail professionnel et traiter les données dans le cadre de ce flux de travail dans une application logique.


Pour ces scénarios, vous devez effectuer ce qui suit :

1. Créez une instance de l’application API du connecteur SAP.
2. Établissez une connectivité hybride pour que l’application API puisse communiquer localement avec SAP.
3. Utilisez l’application API créée dans une application logique pour accomplir le processus d’entreprise souhaité.


## Créez une instance de l’application API du connecteur SAP. ##

Un connecteur peut être créé dans une application logique ou directement à partir d’Azure Marketplace. Pour créer un connecteur à partir de Marketplace :

1. Dans le tableau d'accueil Azure, sélectionnez **Marketplace**.
2. Recherchez « Connecteur SAP », sélectionnez-le et sélectionnez **Créer**.
3. Configurez-le comme suit :
	1. Fournissez les détails génériques comme le nom, le plan de service de l’application, etc. dans le premier panneau.

	2. Dans le cadre des paramètres du package, fournissez les informations d'identification SAP. Fournissez également une chaîne de connexion Azure Service Bus. Celle-ci sert à établir une connectivité hybride avec votre SAP local.

	3. Les RFC, TRFC, BAPI et IDOC doivent être configurés en fonction des besoins du scénario. Si plusieurs valeurs doivent être fournies, alors elles peuvent être séparées par des virgules.

![][1]

## Configurez l’application API du connecteur SAP que vous venez de créer. ##

Accédez à l’application API récemment créée en sélectionnant Parcourir -> API Apps -> Nom de l’application API créée. L’installation n’est pas terminée puisque la connexion hybride n’est pas encore établie : ![][2]

Le connecteur SAP nécessite une connectivité hybride pour se connecter à un point de terminaison SAP, *quel qu’il soit*. Pour l’établir une connectivité hybride, procédez comme suit :

1. Copiez la chaîne de connexion principale.
2. Cliquez sur le lien Télécharger et configurer.
3. Suivez le processus d’installation qui se lance et fournissez la chaîne de connexion principale quand vous y êtes invité.
4. Une fois le processus d’installation terminé, une boîte de dialogue similaire à celle-ci apparaît : ![][3]

En savoir plus sur [l’intégration avec un serveur SAP local](app-service-logic-integrate-with-an-on-premise-sap-server.md).

Maintenant, quand vous accédez de nouveau à l’application API créée, vous pouvez observer que l’état de la connexion hybride est Connecté : ![][4]

Remarque : si vous voulez basculer vers la chaîne de connexion secondaire, il vous suffit de recommencer la configuration et de fournir la chaîne de connexion secondaire à la place de la chaîne de connexion principale.

## Utilisation dans une application logique ##

Le connecteur SAP peut être utilisé en tant qu’action/étape uniquement dans une application logique.

Quand vous créez/modifiez une application logique, choisissez l’application API du connecteur SAP que vous avez créée ci-dessus. Toutes les actions permises que vous pouvez choisir sont alors répertoriées : ![][5]

Dès que vous sélectionnez une action, la liste des paramètres d'entrée de cette action apparaît. Fournissez les valeurs appropriées, puis cliquez sur l'icône représentant une coche : ![][6]

L’étape/l’action s’affiche maintenant comme configurée dans l’application logique. Les sorties de l'opération s'affichent et peuvent être utilisées comme entrées dans une étape ultérieure : ![][7]

Complétez l’application logique pour définir le processus d’entreprise, puis exécutez-la pour atteindre l’objectif voulu.

## En faire plus avec votre connecteur
Maintenant que le connecteur est créé, vous pouvez l’ajouter à un flux d’entreprise à l’aide d’une application logique. Voir [Qu’est-ce qu’une application logique ?](app-service-logic-what-are-logic-apps.md).

>[AZURE.NOTE] Si vous voulez vous familiariser avec Azure Logic Apps avant d’ouvrir un compte Azure, accédez à la page [Essayer Logic App](https://tryappservice.azure.com/?appservice=logic), où vous pourrez créer immédiatement une application logique temporaire dans App Service. Aucune carte de crédit n’est requise ; vous ne prenez aucun engagement.

Affichez la référence d’API REST Swagger sur [Référence de connecteurs et d’applications API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

Vous pouvez également consulter les statistiques de performances et contrôler la sécurité du connecteur. Consultez la page [Gestion et contrôle de vos connecteurs et applications API intégrés](app-service-logic-monitor-your-connectors.md).

<!--Image references-->
[1]: ./media/app-service-logic-connector-sap/Create.jpg
[2]: ./media/app-service-logic-connector-sap/BrowseSetupIncomplete.jpg
[3]: ./media/app-service-logic-connector-sap/HybridSetup.jpg
[4]: ./media/app-service-logic-connector-sap/BrowseSetupComplete.jpg
[5]: ./media/app-service-logic-connector-sap/LogicApp1.jpg
[6]: ./media/app-service-logic-connector-sap/LogicApp2.jpg
[7]: ./media/app-service-logic-connector-sap/LogicApp3.jpg

<!---HONumber=AcomDC_0727_2016-->