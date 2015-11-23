<properties
   pageTitle="Utilisation du connecteur Box dans des applications logiques | Microsoft Azure App Service"
   description="Comment créer et configurer le connecteur Box ou une application API et l'utiliser dans une application logique d’Azure App Service"
   services="app-service\logic"
   documentationCenter=".net,nodejs,java"
   authors="rajeshramabathiran"
   manager="dwrede"
   editor=""/>

<tags
   ms.service="app-service-logic"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="integration"
   ms.date="11/11/2015"
   ms.author="rajram"/>

# Utilisation et ajout du connecteur Box dans votre application logique 
Connectez-vous à votre compte Box pour, entre autres, afficher, télécharger, supprimer vos fichiers. Les connecteurs sont utilisés dans les applications logiques dans le cadre d’un « flux de travail ».

Dans certains scénarios, vous pouvez avoir besoin d'utiliser Box, qui vous permet de partager des données de manière sécurisée avec n'importe quelle personne, même si elle se trouve derrière votre pare-feu. Les applications logiques peuvent se déclencher selon diverses sources de données et proposent des connecteurs pour obtenir et traiter les données dans le cadre du flux.


## Déclencheurs et actions
L'application de la galerie Box vous fournit des actions en tant que mécanismes pour interagir avec Box :

**Actions** : les actions vous permettent d’effectuer des actions prédéfinies sur le compte Box configuré avec l’application logique. Voici les actions qui peuvent être effectuées sur le compte Box à l'aide du connecteur Box :

a. *Liste des fichiers* : cette opération retourne les informations de tous les fichiers inclus dans un dossier. Liste des paramètres obligatoires pour l'action :

Nom du paramètre | Description | Requis
--- | --- | ---
Chemin d'accès du dossier | Chemin d'accès du dossier à ajouter à la liste. | Oui

> [AZURE.NOTE]Ce paramètre ne permet pas de retourner le contenu d'un fichier.

b. *Obtenir un fichier* : cette opération récupère un fichier, à savoir son contenu et ses propriétés. Liste des paramètres obligatoires pour l'action :

Nom du paramètre | Description | Requis
--- | --- | ---
Chemin de fichier | Chemin d'accès du dossier où réside le fichier. | Oui
Type de fichier | Spécifie s'il s'agit d'un fichier texte ou binaire. | Non

> [AZURE.NOTE]Cette opération ne supprime pas le fichier une fois qu'il a été lu.


c. *Télécharger un fichier* : comme son nom le suggère, cette action télécharge le fichier vers le compte Box. Si le fichier existe déjà, alors il n'est pas remplacé et une erreur est générée. Liste des paramètres obligatoires pour l'action :

Nom du paramètre | Description | Requis
--- | --- | ---
Chemin de fichier | Chemin d'accès au fichier. | Oui
Contenu du fichier | Contenu du fichier à télécharger. | Oui
Encodage de transfert de contenu | Type d'encodage du contenu, à savoir Base64 ou None. | 

d. *Supprimer un fichier* : l'action supprime un fichier spécifié d'un dossier. Si le fichier/dossier est introuvable, une exception est levée. Liste des paramètres obligatoires pour l'action :

Nom du paramètre | Description | Requis
--- | --- | ---
Chemin de fichier | Chemin d'accès complet aux fichiers, y compris les dossiers. | Oui


## Création du connecteur Box pour votre application logique

Un connecteur peut être créé dans une application logique ou directement à partir d'Azure Marketplace. Pour créer un connecteur à partir de Marketplace :

1. Dans le tableau d'accueil Azure, sélectionnez **Marketplace**.
2. Recherchez « Connecteur Box », sélectionnez-le et sélectionnez **Créer**.
3. Entrez le nom, le plan App Service et d'autres propriétés :
![][1]
4. Sélectionnez **Créer**.


## Utilisation du connecteur Box dans votre application logique

Une fois votre application API créée, vous pouvez utiliser le connecteur Box comme action dans votre application logique. Pour ce faire :

1. Dans votre application logique, ouvrez **Déclencheurs et actions** pour ouvrir le concepteur d'applications logiques et configurer votre flux. Le connecteur Box est répertorié dans la galerie. Sélectionnez-le pour l'ajouter automatiquement à votre concepteur d'application logique.

	> [AZURE.NOTE]Si le connecteur Box est sélectionné au début de l'application logique, il agit comme un déclencheur. Dans le cas contraire, les actions pourraient être effectuées sur le compte Box à l'aide du connecteur. À l'heure actuelle, le connecteur Box ne comporte pas de déclencheurs.

2. Authentifier et autoriser des applications logiques pour effectuer des opérations en votre nom. Sélectionnez **Autoriser** sur le connecteur Box : ![][2]

3. Entrez les détails de connexion du compte Box sur lequel vous voulez effectuer les opérations :
![][3]

4. Octroyez aux applications logiques l'accès à votre compte pour effectuer l'opération en votre nom :
![][4]

5. Une liste des actions s'affiche dans laquelle vous pouvez choisir l'opération appropriée à exécuter :
![][5]

## En faire plus avec votre connecteur
Maintenant que le connecteur est créé, vous pouvez l'ajouter à un flux d'entreprise à l'aide d'une application logique. Voir [Que sont les applications logiques ?](app-service-logic-what-are-logic-apps.md).

>[AZURE.NOTE]Si vous voulez vous familiariser avec Azure Logic Apps avant d'ouvrir un compte Azure, accédez à la page [Essayer Logic App](https://tryappservice.azure.com/?appservice=logic), où vous pourrez créer immédiatement une application logique temporaire dans App Service. Aucune carte de crédit n’est requise ; vous ne prenez aucun engagement.

Affichez la référence d’API REST Swagger sur [Référence de connecteurs et d’applications API](http://go.microsoft.com/fwlink/p/?LinkId=529766).

Vous pouvez également consulter les statistiques de performances et contrôler la sécurité du connecteur. Consultez la page [Gestion et contrôle de vos connecteurs et applications API intégrés](app-service-logic-monitor-your-connectors.md).

<!--Image references-->
[1]: ./media/app-service-logic-connector-box/image_0.jpg
[2]: ./media/app-service-logic-connector-box/image_1.jpg
[3]: ./media/app-service-logic-connector-box/image_2.jpg
[4]: ./media/app-service-logic-connector-box/image_3.jpg
[5]: ./media/app-service-logic-connector-box/image_4.jpg

<!---HONumber=Nov15_HO3-->