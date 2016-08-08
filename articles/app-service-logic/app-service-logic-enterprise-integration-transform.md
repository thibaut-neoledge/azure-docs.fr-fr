<properties 
	pageTitle="Vue d’ensemble d’Enterprise Integration Pack | Microsoft Azure App Service | Microsoft Azure" 
	description="Utilisez les fonctionnalités d’Enterprise Integration Pack pour activer les processus métier et les scénarios d’intégration à l’aide de Microsoft Azure App Service" 
	services="app-service\logic" 
	documentationCenter=".net,nodejs,java"
	authors="msftman" 
	manager="erikre" 
	editor="cgronlun"/>

<tags 
	ms.service="logic-apps" 
	ms.workload="integration" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/08/2016" 
	ms.author="deonhe"/>

# Intégration d’entreprise avec les transformations XML

## Vue d'ensemble
Le connecteur Enterprise Integration Transform convertit les données d’un format vers un autre format. Vous pouvez, par exemple, avoir un message entrant qui contient la date du jour au format AnnéeMoisJour. Vous pouvez utiliser une transformation pour remettre en forme la date au format MoisJourAnnée.

## Que fait une transformation ?
Une transformation, également appelée mappage, est composée d’un schéma XML source (l’entrée) et d’un schéma XML cible (la sortie). Vous pouvez utiliser différentes fonctions intégrées pour aider à manipuler ou à contrôler les données, y compris les manipulations de chaînes, les affectations conditionnelles, les expressions arithmétiques, les formateurs d'heure et de date et même les constructions de bouclage.

## Création d’une transformation
Vous pouvez créer une transformation/un mappage à l’aide du [Kit de développement logiciel (SDK) Enterprise Integration](https://aka.ms/vsmapsandschemas) de Visual Studio. Lorsque vous avez terminé de créer et de tester la transformation, vous la téléchargez dans votre compte d’intégration.

## Utilisation d’une transformation
Après avoir téléchargé la transformation dans votre compte d’intégration, vous pouvez l’utiliser pour créer une application logique. L’application logique exécute alors vos transformations à chaque fois qu’elle est déclenchée (et qu’il existe du contenu en entrée qui doit être transformé).

**Voici les étapes à suivre pour utiliser une transformation**:

### Configuration requise 
Dans la version préliminaire, vous devez :

-  [Créer un conteneur Azure Functions](https://ms.portal.azure.com/#create/Microsoft.FunctionApp "Création d’un conteneur Azure Functions")
-  [Ajouter une fonction dans le conteneur Azure Functions](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-logic-app-transform-function%2Fazuredeploy.json "Ce modèle crée une fonction Azure en C# basée sur un webhook avec des fonctionnalités de transformation à utiliser dans des scénarios d’intégration d’applications logiques")
-  Créer un compte d’intégration et y ajouter un mappage

>[AZURE.TIP] Notez le nom du conteneur Azure Functions et de la fonction Azure car vous en aurez besoin à l’étape suivante.

Maintenant que vous avez exécuté la configuration requise, il est temps de créer votre application logique :

1. Créez une application logique et [liez-la à votre compte d’intégration](./app-service-logic-enterprise-integration-accounts.md "Découvrez comment lier un compte d’intégration à une application logique") qui contient le mappage.
2. Ajoutez un déclencheur **Demande - Quand une demande HTTP est reçue** à votre application logique ![](./media/app-service-logic-enterprise-integration-transforms/transform-1.png)
3. Ajoutez l’action **Transformer XML** en sélectionnant d’abord **Ajouter une action** ![](./media/app-service-logic-enterprise-integration-transforms/transform-2.png)
4. Entrez le mot *transformation* dans la zone de recherche afin de filtrer toutes les actions et d’obtenir celle que vous souhaitez utiliser ![](./media/app-service-logic-enterprise-integration-transforms/transform-3.png)
5. Sélectionnez l’action **Transformer XML** ![](./media/app-service-logic-enterprise-integration-transforms/transform-4.png)
6. Sélectionnez le **CONTENEUR DE FONCTIONS** qui contient la fonction que vous allez utiliser. Il s’agit du nom du conteneur Azure Functions que vous avez créé précédemment dans cette procédure.
7. Sélectionnez la **FONCTION** à utiliser. Il s’agit du nom de la fonction Azure que vous avez créée précédemment.
8. Ajoutez le **CONTENU** XML à transformer. Notez que vous pouvez utiliser toutes les données XML que vous recevez dans la demande HTTP en tant que le **CONTENU**. Dans cet exemple, sélectionnez le corps de la demande HTTP qui a déclenché l’application logique.
9. Sélectionnez le nom du **MAPPAGE** que vous souhaitez utiliser pour effectuer la transformation. Le mappage doit déjà exister dans votre compte d’intégration. Lors d’une étape précédente, vous avez déjà attribué à votre application logique l’accès à votre compte d’intégration qui contient le mappage.
10. Enregistrez votre travail ![](./media/app-service-logic-enterprise-integration-transforms/transform-5.png)

À ce stade, vous avez terminé de configurer votre mappage. Dans une application réelle, vous souhaiterez peut-être stocker les données transformées dans une application métier, comme SalesForce. Vous pouvez facilement ajouter une action pour envoyer la sortie de la transformation à SalesForce.

Vous pouvez maintenant tester votre transformation en effectuant une demande au point de terminaison HTTP.

## Fonctionnalités et cas d’usage

- La transformation créée dans un mappage peut être simple, par exemple la copie d'un nom et de l'adresse d'un document vers un autre. Vous pouvez aussi créer des transformations plus complexes à l'aide des opérations de mappage prêtes à l'emploi.
- Plusieurs fonctions ou opérations de mappage sont disponibles, y compris des chaînes, des fonctions de date et d'heure, et ainsi de suite.
- Vous pouvez effectuer une copie de données directe entre les schémas. Dans le Mappeur inclus dans le Kit de développement logiciel (SDK), il suffit de dessiner une ligne qui relie les éléments dans le schéma source à leurs équivalents dans le schéma de destination.
- Lors de la création d’un mappage, sa représentation graphique est affichée, notamment toutes les relations et les liens que vous créez.
- Utilisez la fonctionnalité Tester le mappage pour ajouter un exemple de message XML. Avec un simple clic, vous pouvez tester le mappage que vous avez créé et afficher la sortie générée.
- Téléchargement de mappages existants
- Comprend la prise en charge du format XML.


## En savoir plus
- [En savoir plus sur Enterprise Integration Pack](./app-service-logic-enterprise-integration-overview.md "En savoir plus sur Enterprise Integration Pack")
- [En savoir plus sur les mappages](./app-service-logic-enterprise-integration-maps.md "Découvrez les mappages d’intégration d’entreprise")
 

<!---HONumber=AcomDC_0727_2016-->