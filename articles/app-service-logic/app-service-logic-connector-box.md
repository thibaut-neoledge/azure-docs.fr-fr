<properties
   pageTitle="Utilisation du connecteur Box dans votre application logique"
   description="Utilisation du connecteur Box dans votre application logique"
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
   ms.date="07/02/2015"
   ms.author="andalmia"/>

# Utilisation du connecteur Box dans votre application logique



Les applications logiques peuvent se déclencher selon diverses sources de données et proposent des connecteurs pour obtenir et traiter les données dans le cadre du flux. Dans certains scénarios, vous pouvez avoir besoin d'utiliser Box, qui vous permet de partager des données de manière sécurisée avec n'importe quelle personne, même si elle se trouve derrière votre pare-feu.



L'application de la galerie Box vous fournit des actions en tant que mécanismes pour interagir avec Box :



1. **Actions** : les actions vous permettent d’effectuer des actions prédéfinies sur le compte Box configuré avec l’application logique. Voici les actions qui peuvent être effectuées sur le compte Box à l'aide du connecteur Box :

	a. *Liste des fichiers* : cette opération retourne les informations de tous les fichiers inclus dans un dossier. Voici la liste des paramètres obligatoires pour l'action :

	<table>
	  <tr>
	    <td><b>Nom du paramètre</b></td>
	    <td><b>Description</b></td>
	    <td><b>Obligatoire</b></td>
	  </tr>
	  <tr>
	    <td>Chemin d'accès du dossier</td>
	    <td>Chemin du dossier dont les fichiers sont à répertorier.</td>
	    <td>Oui</td>
	  </tr>
	</table>

	>[AZURE.NOTE]Ce paramètre ne permet pas de retourner le contenu d'un fichier.



    b. *Obtenir un fichier* : cette opération récupère un fichier, à savoir son contenu et ses propriétés. Voici la liste des paramètres obligatoires pour l'action :

	<table>
	  <tr>
	    <td><b>Nom du paramètre</b></td>
	    <td><b>Description</b></td>
	    <td><b>Obligatoire</b></td>
	  </tr>
	  <tr>
	    <td>Chemin de fichier</td>
	    <td>Chemin du dossier dans lequel le fichier est présent.</td>
	    <td>Oui</td>
	  </tr>
	  <tr>
	    <td>Type de fichier</td>
	    <td>Spécifie s'il s'agit d'un fichier texte ou binaire.</td>
	    <td>Non</td>
	  </tr>
	</table>
	>[AZURE.NOTE]Cette opération ne supprime pas le fichier une fois qu'il a été lu.



    c. Télécharger un fichier : comme son nom le suggère, cette action télécharge le fichier vers le compte Box. Si le fichier existe déjà, alors il n'est pas remplacé et une erreur est générée. Voici la liste des paramètres obligatoires pour l'action :

	<table>
	  <tr>
	    <td><b>Nom du paramètre</b></td>
	    <td><b>Description</b></td>
	    <td><b>Obligatoire</b></td>
	  </tr>
	  <tr>
	    <td>Chemin de fichier</td>
	    <td>Chemin d'accès au fichier.</td>
	    <td>Oui</td>
	  </tr>
	  <tr>
	    <td>Contenu du fichier</td>
	    <td>Contenu du fichier à télécharger.</td>
	    <td>Oui</td>
	  </tr>
	  <tr>
	    <td>Encodage de transfert de contenu</td>
	    <td>Type d'encodage du contenu, à savoir Base64 ou None.</td>
	    <td> </td>
	  </tr>
	</table>


	d. Supprimer un fichier : l’action supprime un fichier spécifié d’un dossier. Si le fichier/dossier est introuvable, une exception est levée. Voici la liste des paramètres obligatoires pour l'action :

 	<table>
	  <tr>
	    <td><b>Nom du paramètre</b></td>
	    <td><b>Description</b></td>
	    <td><b>Obligatoire</b></td>
	  </tr>
	  <tr>
	    <td>Chemin de fichier</td>
	    <td>Chemin d'accès complet aux fichiers, y compris les dossiers.</td>
	    <td>Oui</td>
	  </tr>
	</table>




## Création d'un connecteur Box pour votre application logique

Pour utiliser le connecteur Box, vous devez d'abord créer une instance de l'application API du connecteur Box. Vous pouvez également effectuer cette opération depuis le concepteur d'application logique ou de l'extérieur. Pour créer le concepteur de l'extérieur, procédez comme suit :

1. Ouvrez Azure Marketplace à partir de la page d'accueil du portail Azure.

2. Sous « Tout », recherchez « Connecteur Box ».

3. Configurez le connecteur Box et cliquez sur Créer :

	![][1]

4. Une fois cette opération effectuée, vous pouvez alors créer une application logique dans le même groupe de ressources pour utiliser le connecteur Box.


## Utilisation du connecteur Box dans votre application logique

Une fois votre application API créée, vous pouvez utiliser le connecteur Box comme action pour votre application logique. Pour cela, vous devez procéder comme suit :


1. Créez une application logique et choisissez le même groupe de ressources qui comporte le connecteur Box.

2. Ouvrez « Déclencheurs et actions » pour ouvrir le concepteur d'applications logiques et configurer votre flux. Le connecteur Box apparaît dans la section des éléments récemment utilisés dans la galerie située sur le côté droit. Sélectionnez-le.

3. Si le connecteur Box est sélectionné au démarrage de l'application logique, il agit comme un déclencheur sinon des actions pourraient être exécutées sur le compte Box à l'aide du connecteur. Notez qu'à l'heure actuelle, le connecteur Box ne comporte pas de déclencheurs.

4. La première étape consiste à authentifier et autoriser des applications logiques pour effectuer des opérations en votre nom. Pour démarrer l'autorisation, cliquez sur Autoriser sur le connecteur Box.

	![][2]

5. Le fait de cliquer sur Autoriser ouvre la boîte de dialogue d'authentification de Box. Fournissez les détails de connexion du compte Box sur lequel vous voulez effectuer les opérations.

	![][3]

6. Octroyez aux applications logiques l'accès à votre compte pour effectuer l'opération en votre nom.

	![][4]

7. Une liste des actions s'affiche dans laquelle vous pouvez choisir l'opération appropriée à exécuter.

	![][5]


<!--Image references-->
[1]: ./media/app-service-logic-connector-box/image_0.jpg
[2]: ./media/app-service-logic-connector-box/image_1.jpg
[3]: ./media/app-service-logic-connector-box/image_2.jpg
[4]: ./media/app-service-logic-connector-box/image_3.jpg
[5]: ./media/app-service-logic-connector-box/image_4.jpg

<!----HONumber=July15_HO4-->