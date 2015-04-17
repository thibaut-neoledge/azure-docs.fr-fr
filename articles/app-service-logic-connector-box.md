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
   ms.date="03/20/2015"
   ms.author="vagarw"/>

# **Utilisation du connecteur Box dans votre application logique**

 

Les applications logiques peuvent se déclencher selon diverses sources de données et proposent des connecteurs pour obtenir et traiter les données dans le cadre du flux. Dans certains scénarios, vous pouvez avoir besoin d'utiliser Box, qui vous permet de partager des données de manière sécurisée avec n'importe quelle personne, même si elle se trouve derrière votre pare-feu.

 

L'application de la galerie Box vous fournit un déclencheur et des actions en tant que mécanismes pour interagir avec Box :

 

1. **Déclencheur** **sur fichier disponible** : Utilisez celui-ci si vous voulez lancer une application logique quand un fichier est ajouté à un dossier Box. Le déclencheur vérifie le dossier Box spécifié selon une fréquence configurée et déclenche le flux quand un fichier est disponible dans le dossier spécifié. Il retourne le contenu et les propriétés du fichier et après l'avoir correctement transmis à l'étape suivante de l'application logique, il le supprime.


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
	  <tr>
	    <td>Fréquence</td>
	    <td>Spécifie le type de fréquence, qui doit être sélectionné parmi les types répertoriés. Ceux-ci peuvent être les suivants : Années, Mois, Semaines, Jours, Heures, Minutes ou Secondes</td>
	    <td>Oui</td>
	  </tr>
	  <tr>
	    <td>Intervalle</td>
	    <td>Spécifie l'unité de la fréquence.</td>
	    <td>Oui</td>
	  </tr>
	</table>


 

2. **Actions** : Les actions vous permettent d'effectuer des actions prédéfinies sur le compte Box configuré avec l'application logique. Voici les actions qui peuvent être effectuées sur le compte Box à l'aide du connecteur Box :

	a. *List Files:* Cette opération retourne les informations de tous les fichiers inclus dans un dossier. Voici la liste des paramètres obligatoires pour l'action :

	<table>
	  <tr>
	    <td><b>Nom du paramètre</b></td>
	    <td><b>Description</b></td>
	    <td><b>Obligatoire</b></td>
	  </tr>
	  <tr>
	    <td>Chemin du dossier</td>
	    <td>Chemin du dossier dont les fichiers sont à répertorier.</td>
	    <td>Oui</td>
	  </tr>
	</table>

	*Remarque : Ce paramètre ne permet pas de retourner le contenu d'un fichier.*

 

    b. *Get File:* Cette opération récupère un fichier, à savoir son contenu et ses propriétés. Voici la liste des paramètres obligatoires pour l'action :

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
	*Remarque : cette opération ne supprime pas le fichier une fois qu'il a été lu.*

 

    c. Télécharger un fichier : Comme son nom le suggère, cette action télécharge le fichier vers le compte Box. Si le fichier existe déjà, alors il n'est pas remplacé et une erreur est générée. Voici la liste des paramètres obligatoires pour l'action :

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


    d. Supprimer un fichier : L'action supprime un fichier spécifié d'un dossier. Si le fichier/dossier est introuvable, une exception est levée. Voici la liste des paramètres obligatoires pour l'action :

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
	</table>


 

## **Création d'un connecteur Box pour votre application logique** ##

Pour utiliser le connecteur Box, vous devez d'abord créer une instance de l'application API du connecteur Box. Pour cela, procédez comme suit :

1. Ouvrez Azure Marketplace à l'aide de l'option " + NOUVEAU " en bas à droite du portail Azure.

2. Accédez à " Web et mobile > API Apps " et recherchez " Connecteur Box ".

3. Configurez le connecteur Box et cliquez sur  Créer.

	![][1]

4. Une fois cette opération effectuée, vous pouvez alors créer une application logique dans le même groupe de ressources pour utiliser le connecteur Box.


## **Utilisation du connecteur Box dans votre application logique** ##

Une fois votre application API créée, vous pouvez utiliser le connecteur Box comme déclencheur ou action pour votre application logique. Pour cela, vous devez procéder comme suit :


1. Créez une application logique et choisissez le même groupe de ressources qui comporte le connecteur Box.

2. Ouvrez " Déclencheurs et actions " pour ouvrir le concepteur d'applications logiques et configurer votre flux. Le connecteur Box apparaît dans la section des éléments récemment utilisés dans la galerie située sur le côté droit. Sélectionnez-le.

3. Si le connecteur Box est sélectionné au démarrage de l'application logique, il agit comme un déclencheur sinon des actions pourraient être exécutées sur le compte Box à l'aide du connecteur.

4. La première étape consiste à authentifier et autoriser des applications logiques pour effectuer des opérations en votre nom. Pour démarrer l'autorisation, cliquez sur Autoriser sur le connecteur Box.

	![][2]

5. Le fait de cliquer sur Autoriser ouvre la boîte de dialogue d'authentification de Box. Fournissez les détails de connexion du compte Box sur lequel vous voulez effectuer les opérations.

	![][3]

6. Octroyez aux applications logiques l'accès à votre compte pour effectuer l'opération en votre nom. 

	![][4]

7. Si le connecteur Box est configuré en tant que déclencheur, alors les déclencheurs sont affichés, sinon la liste des actions s'affiche et vous pouvez choisir l'opération appropriée que vous voulez effectuer.  

	![][5]


<!--Image references-->
[1]: ./media/app-service-logic-connector-box/image_0.jpg
[2]: ./media/app-service-logic-connector-box/image_1.jpg
[3]: ./media/app-service-logic-connector-box/image_2.jpg
[4]: ./media/app-service-logic-connector-box/image_3.jpg
[5]: ./media/app-service-logic-connector-box/image_4.jpg


<!--HONumber=49-->