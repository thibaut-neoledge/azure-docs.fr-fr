<properties 
   pageTitle="Utilisation du connecteur SharePoint dans votre application logique" 
   description="Utilisation du connecteur SharePoint dans votre application logique" 
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

# Utilisation du connecteur SharePoint dans votre application logique

Les applications logiques peuvent se déclencher selon diverses sources de données et proposent des connecteurs pour obtenir et traiter les données dans le cadre du flux. Le connecteur Microsoft SharePoint vous permet de vous connecter à Microsoft SharePoint Server/SharePoint Online et de gérer des documents et des éléments de liste. Vous pouvez effectuer diverses actions, comme créer, mettre à jour, obtenir et supprimer des documents et éléments de liste. Avec un serveur SharePoint local, vous pouvez fournir la chaîne de connexion Service Bus dans le cadre de la configuration du connecteur et installer l'agent détecteur local pour la connexion au serveur.

L'application de la galerie des connecteurs SharePoint Online et SharePoint Server vous propose des déclencheurs et des actions en tant que mécanismes pour interagir avec SharePoint.

## Création d'un connecteur SharePoint Online pour votre application logique

Pour utiliser le connecteur SharePoint Online, vous devez d'abord créer une instance de l'application API du connecteur SharePoint Online. Pour cela, procédez comme suit :

1. Ouvrez Azure Marketplace à l'aide de l'option " + NOUVEAU " en bas à droite du portail Azure.

2. Accédez à " Web et mobile > API Apps " et recherchez " SharePoint Online Connector ".

3. Configurez le connecteur SharePoint Online et cliquez sur Créer. Voici les paramètres que vous devez fournir pour créer le connecteur :

	<table>
	  <tr>
	    <td><b>Nom</b></td>
	    <td><b>Obligatoire</b></td>
	    <td><b>Description</b></td>
	  </tr>
	  <tr>
	    <td>URL du site</td>
	    <td>Oui</td>
	    <td>Spécifiez l'URL complète du site web SharePoint. Ex: https://microsoft.sharepoint.com/teams/wabstest </td>
	  </tr>
	  <tr>
	    <td>URL relatives des bibliothèques de documents/listes</td>
	    <td>Oui</td>
	    <td>Spécifiez les URL des bibliothèques de documents/listes, relatives à l'URL du site SharePoint, qui sont autorisées à être modifiées par le connecteur. Ex : Listes/tâche, Documents partagés.</td>
	  </tr>
	</table>
	![][1]


4. Une fois cette opération effectuée, vous pouvez alors créer une application logique dans le même groupe de ressources pour utiliser le connecteur SharePoint Online.

## Création d'un connecteur SharePoint Server pour votre application logique

Pour utiliser le connecteur SharePoint Server, vous devez d'abord créer une instance de l'application API du connecteur SharePoint Server. Pour cela, procédez comme suit :

1. Ouvrez Azure Marketplace à l'aide de l'option " + NOUVEAU " en bas à droite du portail Azure.

2. Accédez à " Web et mobile > API Apps " et recherchez " SharePoint Server Connector ".

3. Configurez le connecteur SharePoint Server et cliquez sur Créer. Voici les paramètres que vous devez fournir pour créer le connecteur :

	<table>
	  <tr>
	    <td><b>Nom</b></td>
	    <td><b>Obligatoire</b></td>
	    <td><b>Description</b></td>
	  </tr>
	  <tr>
	    <td>URL du site</td>
	    <td>Oui</td>
	    <td>Spécifiez l'URL complète du site web SharePoint. Ex: https://microsoft.sharepoint.com/teams/wabstest </td>
	  </tr>
	  <tr>
	    <td>Mode d'authentification</td>
	    <td>Oui</td>
	    <td>Spécifiez le mode d'authentification pour la connexion à un site SharePoint. Les valeurs autorisées sont :<br><br>
			Default<br>
			OAuth2<br>
			WindowsAuthentication<br>
			FormBasedAuthentication.<br><br>
	
	Si vous choisissez les informations d'identification par défaut, celles sous lesquelles s'exécute SharePoint Microservice sont utilisées et le nom d'utilisateur/mot de passe ne sont pas nécessaires. Les champs Nom d'utilisateur et Mot de passe sont obligatoires pour les autres types d'authentification. <br><br>Remarque : l'authentification anonyme n'est pas prise en charge.</td>
	  </tr>
	  <tr>
	    <td>User Name</td>
	    <td>Non</td>
	    <td>Spécifiez un nom d'utilisateur valide pour la connexion au site SharePoint, si le mode d'authentification n'est pas Default/OAuth2.</td>
	  </tr>
	  <tr>
	    <td>Mot de passe</td>
	    <td>Non</td>
	    <td>Spécifiez un mot de passe valide pour la connexion au site SharePoint, si le mode d'authentification n'est pas Default/OAuth2.</td>
	  </tr>
	  <tr>
	    <td>URL relatives des bibliothèques de documents/listes</td>
	    <td>Oui</td>
	    <td>Spécifiez les URL des bibliothèques de documents/listes, relatives à l'URL du site SharePoint, qui sont autorisées à être modifiées par le connecteur. Ex : Listes/tâche, Documents partagés.</td>
	  </tr>
	  <tr>
	    <td>Chaîne de connexion Service Bus</td>
	    <td>Non</td>
	    <td>Celle-ci doit être une chaîne de connexion d'espace de noms Service Bus valide.<br><br>
	
	Vous avez besoin d'installer un agent détecteur sur un serveur qui peut accéder à votre serveur SharePoint. <br>Vous pouvez accéder à la page récapitulative de votre application API et cliquer sur  'Hybrid Connection' pour installer l'agent.</td>
	  </tr>
	</table>


	![][2]

4. Une fois cette opération effectuée, vous pouvez alors créer une application logique dans le même groupe de ressources pour utiliser le connecteur SharePoint Server.
5. Vous avez besoin d'installer un agent détecteur sur un serveur qui peut accéder à votre serveur SharePoint Server. Vous pouvez accéder à la page récapitulative de votre application API et cliquer sur  'Hybrid Connection' pour installer l'agent.

## Utilisation du connecteur SharePoint dans votre application logique

Une fois votre application API créée, vous pouvez utiliser le connecteur SharePoint comme déclencheur ou action pour votre application logique. Pour cela, vous devez procéder comme suit :

1. Créez une application logique et choisissez le même groupe de ressources qui comporte le connecteur SharePoint.

2. Ouvrez " Déclencheurs et actions " pour ouvrir le concepteur d'applications logiques et configurer votre flux. Le connecteur SharePoint apparaît dans la section des éléments récemment utilisés dans la galerie située sur le côté droit. Sélectionnez-le.

3. Si le connecteur SharePoint est sélectionné au démarrage de l'application logique, il agit comme un déclencheur sinon des actions pourraient être exécutées sur le compte SharePoint à l'aide du connecteur. 

4. Vous seriez obligé de vous authentifier et d'autoriser des applications logiques à effectuer des opérations en votre nom si le connecteur SharePoint Online était utilisé ou si l'authentification définie était OAuth2 dans le connecteur SharePoint Server. Pour démarrer l'autorisation, cliquez sur Autoriser sur le connecteur SharePoint. 

	![][3]

5. Le fait de cliquer sur Autoriser ouvre la boîte de dialogue d'authentification de SharePoint. Fournissez les détails de connexion du compte SharePoint sur lequel vous voulez effectuer les opérations. 

	![][4]
6. Octroyez aux applications logiques l'accès à votre compte pour effectuer l'opération en votre nom. 

	![][5]

7. Si le connecteur SharePoint est configuré en tant que déclencheur, alors les déclencheurs sont affichés, sinon la liste des actions s'affiche et vous pouvez choisir l'opération appropriée que vous voulez effectuer.  

	![][6]

	<b>URL relative configurée pour la bibliothèque de documents</b><br><br>

	![][7]

	<b>URL relative configurée pour la liste de documents</b>

	<b>Remarque :</b> Pour les déclencheurs ci-dessous, l'utilisateur est supposé avoir spécifié  'Shared Documents, Lists/Task' dans les paramètres de package du connecteur, où  'Shared Documents' est une bibliothèque de documents et  'Lists/Task' est une liste. 

##  Déclencheurs
Utiliser des déclencheurs pour lancer une application logique 

### 1.	Nouveau document dans les documents partagés (JSON)
Ce déclencheur est déclenché quand un nouveau document est disponible dans  'Shared Documents'. 

**Entrées :**

<table>
  <tr>
    <td><b>Nom</b></td>
    <td><b>Obligatoire</b></td>
    <td><b>Description</b></td>
  </tr>
  <tr>
    <td>Nom de la vue</td>
    <td>Non</td>
    <td>Spécifiez une vue valide utilisée pour filtrer les documents à sélectionner. Exemple : 'Approved Orders'. Pour traiter tous les documents existants, laissez ce champ vide. </td>
  </tr>
  <tr>
    <td>Emplacement d'archive</td>
    <td>Non</td>
    <td>Spécifiez une URL de dossier valide, relative au site SharePoint, où les documents traités sont archivés. </td>
  </tr>
  <tr>
    <td>Remplacer dans l'archive</td>
    <td>Non</td>
    <td>Cochez cette option pour remplacer un fichier dans le chemin d'accès de l'archive s'il existe déjà. </td>
  </tr>
  <tr>
    <td>Requête CAML</td>
    <td>Non, Avancé</td>
    <td>Spécifiez une requête Caml valide pour filtrer les documents. Exemple : <Where><Geq><FieldRef Name='ID'/><Value Type='Number'>10</Value></Geq></Where></td>
  </tr>
</table>

**Sorties :**
<table>
  <tr>
    <td><b>Nom</b></td>
    <td><b>Description</b></td>
  </tr>
  <tr>
    <td>Nom  </td>
    <td>Nom du document.</td>
  </tr>
  <tr>
    <td>Contenu</td>
    <td>Contenu du document.</td>
  </tr>
  <tr>
    <td>ContentTransferEncoding</td>
    <td>Encodage de transfert de contenu du message. ("none"|"base64")</td>
  </tr>
</table>


Remarque : Toutes les colonnes de l'élément de document sont affichées dans les propriétés de sortie  'Advanced'.


###2. Nouvel élément dans les tâches (JSON)
Ce déclencheur est déclenché quand un nouvel élément est ajouté à la liste  'Tasks'.

**Entrées :**
<table>
  <tr>
    <td><b>Nom</b></td>
    <td><b>Obligatoire</b></td>
    <td><b>Description</b></td>
 </tr>
  <tr>
    <td>Nom de la vue</td>
    <td>Non</td>
    <td>Spécifiez une vue valide utilisée pour filtrer les éléments de la liste. Exemple : 'Approved Orders'. Pour traiter tous les nouveaux éléments, laissez ce champ vide. </td>
  </tr>
  <tr>
    <td>Emplacement d'archive</td>
    <td>Non</td>
    <td>Spécifiez une URL de dossier valide, relative au site SharePoint, où les éléments de liste traités sont archivés. </td>
  </tr>
  <tr>
    <td>Requête CAML</td>
    <td>Non, Avancé</td>
    <td>Spécifiez une requête Caml valide pour filtrer les éléments de liste. Exemple : <Where><Geq><FieldRef Name='ID'/><Value Type='Number'>10</Value></Geq></Where></td>
  </tr>
</table>


**Sorties :**

<table>
  <tr>
    <td><b>Nom</b></td>
    <td><b>Description</b></td>
  </tr>
  <tr>
    <td>Les colonnes de la liste sont renseignées de manière dynamique et présentées dans les paramètres de sortie.</td>
    <td> </td>
  </tr>

</table>


###3. Nouveau document dans les documents partagés (XML)

Ce déclencheur est déclenché quand un nouveau document est disponible dans  'Shared Documents'. Le nouveau document est retourné sous forme de message XML.

**Entrées :**

<table>
  <tr>
    <td><b>Nom</b></td>
    <td><b>Obligatoire</b></td>
    <td><b>Description</b></td>
  </tr>
  <tr>
    <td>Nom de la vue</td>
    <td>Non</td>
    <td>Spécifiez une vue valide utilisée pour filtrer les documents à sélectionner. Exemple : 'Approved Orders'. Pour traiter tous les documents existants, laissez ce champ vide. </td>
  </tr>
  <tr>
    <td>Emplacement d'archive</td>
    <td>Non</td>
    <td>Spécifiez une URL de dossier valide, relative au site SharePoint, où les documents traités sont archivés. </td>
  </tr>
  <tr>
    <td>Remplacer dans l'archive</td>
    <td>Non</td>
    <td>Cochez cette option pour remplacer un fichier dans le chemin d'accès de l'archive s'il existe déjà. </td>
  </tr>
  <tr>
    <td>Requête CAML</td>
    <td>Non, Avancé</td>
    <td>Spécifiez une requête Caml valide pour filtrer les documents. Exemple : <Where><Geq><FieldRef Name='ID'/><Value Type='Number'>10</Value></Geq></Where></td>
  </tr>
</table>

**Sorties :**

<table>
  <tr>
    <td><b>Nom</b></td>
    <td><b>Description</b></td>
  </tr>
  <tr>
    <td>Contenu</td>
    <td>Contenu du document.</td>
  </tr>
  <tr>
    <td>ContentTransferEncoding</td>
    <td>Encodage de transfert de contenu du message. ("none"|"base64")</td>
  </tr>
</table>


###4. Nouvel élément dans les tâches (XML)

Ce déclencheur est déclenché quand un nouvel élément est ajouté à la liste  'Tasks'. Le nouvel élément de liste est retourné sous forme de message XML.

**Entrées :**

<table>
  <tr>
    <td><b>Nom</b></td>
    <td><b>Obligatoire</b></td>
    <td><b>Description</b></td>
  </tr>
  <tr>
    <td>Nom de la vue</td>
    <td>Non</td>
    <td>Spécifiez une vue valide utilisée pour filtrer les éléments de la liste. Exemple : 'Approved Orders'. Pour traiter tous les nouveaux éléments, laissez ce champ vide. </td>
  </tr>
  <tr>
    <td>Emplacement d'archive</td>
    <td>Non</td>
    <td>Spécifiez une URL de dossier valide, relative au site SharePoint, où les éléments de liste traités sont archivés. </td>
  </tr>
  <tr>
    <td>Requête CAML</td>
    <td>Non, Avancé</td>
    <td>Spécifiez une requête Caml valide pour filtrer les éléments de liste. Exemple : <Where><Geq><FieldRef Name='ID'/><Value Type='Number'>10</Value></Geq></Where></td>
  </tr>
</table>


**Sorties :**

<table>
  <tr>
    <td><b>Nom</b></td>
    <td><b>Description</b></td>
  </tr>
  <tr>
    <td>Contenu</td>
    <td>Contenu du document.</td>
  </tr>
  <tr>
    <td>ContentTransferEncoding</td>
    <td>Encodage de transfert de contenu du message. ("none"|"base64")</td>
  </tr>
</table>


##  Actions
Pour les actions ci-dessous, l'utilisateur est supposé avoir spécifié  'Shared Documents, Lists/Task' dans les paramètres de package du connecteur, où  'Shared Documents' est une bibliothèque de documents et  'Lists/Task' est une liste. 

###1. Télécharger vers les documents partagés (JSON)

Cette action télécharge le nouveau document vers  'Shared Documents'. L'entrée est un objet JSON fortement typé avec tous les champs de colonne de la bibliothèque de documents.

**Entrées :**

<table>
  <tr>
    <td><b>Nom</b></td>
    <td><b>Obligatoire</b></td>
    <td><b>Description</b></td>
 </tr>
  <tr>
    <td>Nom</td>
    <td>Oui</td>
    <td>Nom du document.</td>
  </tr>
  <tr>
    <td>Contenu</td>
    <td>Oui</td>
    <td>Contenu du document.</td>
  </tr>
  <tr>
    <td>ContentTransferEncoding</td>
    <td>Oui</td>
    <td>Encodage de transfert de contenu du message. ("none"|"base64")</td>
  </tr>
  <tr>
    <td>Forcer le remplacement</td>
    <td>Oui</td>
    <td>Si la valeur est TRUE et qu'un document existe avec le nom donné, il est remplacé.</td>
  </tr>
  <tr>
    <td>ReqParam1*</td>
    <td>Oui</td>
    <td>Il s'agit d'un des paramètres requis pour ajouter un document dans la bibliothèque de documents.</td>
  </tr>
  <tr>
    <td>ReqParam2*</td>
    <td>Oui</td>
    <td>Il s'agit d'un des paramètres requis pour ajouter un document dans la bibliothèque de documents.</td>
  </tr>
  <tr>
    <td>OptionalParam1*</td>
    <td>Non. Avancé</td>
    <td>Il s'agit d'un des paramètres facultatifs pour ajouter un document dans la bibliothèque de documents.</td>
  </tr>
  <tr>
    <td>OptionalParam2*</td>
    <td>Non. Avancé</td>
    <td>Il s'agit d'un des paramètres facultatifs pour ajouter un document dans la bibliothèque de documents.</td>
  </tr>
</table>

<b>Remarque :</b> Tous les paramètres de la bibliothèque de documents sont renseignés de manière dynamique. Les paramètres obligatoires sont visibles, alors que les paramètres facultatifs sont dans la section Avancé.


**Sorties :**

<table>
  <tr>
    <td><b>Nom</b></td>
    <td><b>Description</b></td>
  </tr>
  <tr>
    <td>ItemId</td>
    <td>ItemId du document ajouté dans la bibliothèque de documents.</td>
  </tr>
  <tr>
    <td>Statut</td>
    <td>Un téléchargement réussi du document retourne le code d'état 200 (OK).</td>
  </tr>
</table>


 

###2. Obtenir à partir des documents partagés (JSON)
Cette action obtient le document à partir de la bibliothèque de documents, étant donné l'URL relative (structure de dossiers) du document.


**Entrées :**

<table>
  <tr>
    <td><b>Nom</b></td>
    <td><b>Obligatoire</b></td>
    <td><b>Description</b></td>
  </tr>
  <tr>
    <td>URI relatif du document</td>
    <td>Non</td>
    <td>Spécifiez l'URL du document, relative à  'Shared Documents'. Exemple : myspec1,myfolder/orders</td>
  </tr>
</table>


**Sorties :**

<table>
  <tr>
    <td><b>Nom</b></td>
    <td><b>Description</b></td>
  </tr>
  <tr>
    <td>Contenu</td>
    <td>Contenu du document</td>
  </tr>
  <tr>
    <td>ContentTransferEncoding</td>
    <td>Encodage de transfert de contenu du message. ("none"|"base64")</td>
  </tr>
  <tr>
    <td>Statut</td>
    <td>L'exécution réussie d'une action retourne le code d'état 200 (OK).</td>
  </tr>
  <tr>
    <td>Param1*</td>
    <td>Il s'agit d'un des paramètres d'un document dans la bibliothèque de documents.</td>
  </tr>
  <tr>
    <td>Param2*</td>
    <td>Il s'agit d'un des paramètres d'un document dans la bibliothèque de documents.</td>
  </tr>
</table>

<b>Remarque :</b> Tous les paramètres de la bibliothèque de documents sont renseignés de manière dynamique. De plus, ils se trouvent dans la section Avancé.

 

###3. Supprimer des documents partagés

Cette action supprime le document de la bibliothèque de documents, étant donné l'URL relative (structure de dossiers) du document.

**Entrées :**

<table>
  <tr>
    <td><b>Nom</b></td>
    <td><b>Obligatoire</b></td>
    <td><b>Description</b></td>
  </tr>
  <tr>
    <td>URI relatif du document</td>
    <td>Non</td>
    <td>Spécifiez l'URL du document, relative à  'Shared Documents'. Exemple : myspec1,myfolder/orders</td>
  </tr>
</table>


**Sorties :**

<table>
  <tr>
    <td><b>Nom</b></td>
    <td><b>Description</b></td>
  </tr>
  <tr>
    <td>Statut</td>
    <td>L'exécution réussie d'une action retourne le code d'état 200 (OK).</td>
  </tr>
</table>


###4. Insérer dans des tâches (JSON)

Cette action ajoute un élément dans la liste d'éléments.

**Entrées :**

<table>
  <tr>
    <td><b>Nom</b></td>
    <td><b>Obligatoire</b></td>
    <td><b>Description</b></td>
  </tr>
  <tr>
    <td>ReqParam1*</td>
    <td>Oui</td>
    <td>Il s'agit d'un des paramètres requis pour ajouter un élément dans la liste.</td>
  </tr>
  <tr>
    <td>ReqParam2*</td>
    <td>Oui</td>
    <td>Il s'agit d'un des paramètres requis pour ajouter un élément dans la liste.</td>
  </tr>
  <tr>
    <td>OptionalParam1*</td>
    <td>Non. Avancé</td>
    <td>Il s'agit d'un des paramètres requis pour ajouter un élément dans la liste.</td>
  </tr>
  <tr>
    <td>OptionalParam2*</td>
    <td>Non. Avancé</td>
    <td>Il s'agit d'un des paramètres requis pour ajouter un élément dans la liste.</td>
  </tr>
</table>


<b>Remarque :</b> Tous les paramètres de la  'List' sont renseignés de manière dynamique. Les paramètres obligatoires sont visibles, tandis que les paramètres facultatifs sont dans la section Avancé.

 
**Sorties :**

<table>
  <tr>
    <td><b>Nom</b></td>
    <td><b>Description</b></td>
  </tr>
  <tr>
    <td>ItemId</td>
    <td>ItemId de l'élément de liste ajouté.</td>
  </tr>
  <tr>
    <td>Statut</td>
    <td>Une insertion réussie d'un élément de liste retourne le code d'état 200 (OK).</td>
  </tr>
</table>


###5. Mettre à jour des tâches (JSON)

Cette action met à jour un élément dans la liste d'éléments.

**Entrées :**

<table>
  <tr>
    <td><b>Nom</b></td>
    <td><b>Obligatoire</b></td>
    <td><b>Description</b></td>
  </tr>
  <tr>
    <td>ItemId</td>
    <td>Oui</td>
    <td>ItemId de l'élément de liste.</td>
  </tr>
  <tr>
    <td>ReqParam1*</td>
    <td>Non</td>
    <td>Il s'agit d'un des paramètres requis pour ajouter un élément dans la liste.</td>
  </tr>
  <tr>
    <td>ReqParam2*</td>
    <td>Non</td>
    <td>Il s'agit d'un des paramètres requis pour ajouter un élément dans la liste.</td>
  </tr>
  <tr>
    <td>OptionalParam1*</td>
    <td>Non. Avancé</td>
    <td>Il s'agit d'un des paramètres requis pour ajouter un élément dans la liste.</td>
  </tr>
  <tr>
    <td>OptionalParam2*</td>
    <td>Non. Avancé</td>
    <td>Il s'agit d'un des paramètres requis pour ajouter un élément dans la liste.</td>
  </tr>
</table>

<b>Remarque :</b> Tous les paramètres de la  'List' sont renseignés de manière dynamique. Les paramètres obligatoires sont visibles, tandis que les paramètres facultatifs sont dans la section Avancé.


**Sorties :**

<table>
  <tr>
    <td><b>Nom</b></td>
    <td><b>Description</b></td>
  </tr>
  <tr>
    <td>Statut  </td>
    <td>Une mise à jour réussie d'un élément de liste retourne le code d'état 200 (OK).</td>
  </tr>
</table>


###6. Obtenir un élément à partir des tâches (JSON)

Cette action obtient un élément de la liste d'éléments.


**Entrées :**

<table>
  <tr>
    <td><b>Nom</b></td>
    <td><b>Obligatoire</b></td>
    <td><b>Description</b></td>
  </tr>
  <tr>
    <td>ItemId</td>
    <td>Oui</td>
    <td>ItemId de l'élément de liste.</td>
  </tr>
</table>


**Sorties :**

<table>
  <tr>
    <td><b>Nom</b></td>
    <td><b>Description</b></td>
  </tr>
  <tr>
    <td>Column1*</td>
    <td>Il s'agit d'un des paramètres dans la liste.</td>
  </tr>
  <tr>
    <td>Column2*</td>
    <td>Il s'agit d'un des paramètres dans la liste.</td>
  </tr>
  <tr>
    <td>Statut</td>
    <td>Une exécution réussie de l'action retourne le code d'état 200 (OK).</td>
  </tr>
</table>

<b>Remarque :</b> Les colonnes de la liste sont renseignées de manière dynamique et présentées dans les paramètres de sortie.


###7. Supprimer un élément des tâches

Cette action supprime un élément de la liste d'éléments.

 
**Entrées :**

<table>
  <tr>
    <td><b>Nom</b></td>
    <td><b>Obligatoire</b></td>
    <td><b>Description</b></td>
  </tr>
  <tr>
    <td>ItemId</td>
    <td>Oui</td>
    <td>ItemId de l'élément de liste.</td>
  </tr>
</table>


**Sorties :**

<table>
  <tr>
    <td><b>Nom</b></td>
    <td><b>Description</b></td>
  </tr>
  <tr>
    <td>Statut  </td>
    <td>Une suppression réussie d'un élément de liste retourne le code d'état 200 (OK).</td>
  </tr>
</table>


###8. Répertorier les documents partagés (JSON)

Cette action répertorie tous les documents dans une bibliothèque de documents. Vous pouvez utiliser une vue ou une requête Caml pour filtrer les documents.  

 
**Entrées :**

<table>
  <tr>
    <td><b>Nom</b></td>
    <td><b>Obligatoire</b></td>
    <td><b>Description</b></td>
  </tr>
  <tr>
    <td>Nom de la vue</td>
    <td>Non</td>
    <td>Spécifiez une vue valide utilisée pour filtrer les documents à sélectionner. Exemple : 'Approved Orders'. Pour traiter tous les documents existants, laissez ce champ vide. </td>
  </tr>
  <tr>
    <td>Requête CAML</td>
    <td>Non</td>
    <td>Spécifiez une requête Caml valide pour filtrer les documents. Exemple : <Where><Geq><FieldRef Name='ID'/><Value Type='Number'>10</Value></Geq></Where></td>
  </tr>
</table>


**Sorties :**

<table>
  <tr>
    <td><b>Nom</b></td>
    <td><b>Description</b></td>
  </tr>
  <tr>
    <td>Documents</td>
    <td>Tableau de tous les documents. Chaque document comporte les champs ci-dessous. <br><br>
	Documents []<br>
	Nom<br>
	Item Id<br>
	URL complète de l'élément<br>
	Avancé<br>
	URL de modification de l'élément<br>
	Nom de la liste<br>
	URL complète de la liste<br>
	</td>
  </tr>
  <tr>
    <td>Statut  </td>
    <td>Une insertion réussie d'un élément de liste retourne le code d'état 200 (OK).</td>
  </tr>
</table>


###9. Télécharger vers les documents partagés (XML)

Cette action télécharge le nouveau document vers  'Shared Documents'. Le document d'entrée doit être une charge utile XML. La réponse de l'action sera une charge utile XML.
 

**Entrées :**

<table>
  <tr>
    <td><b>Nom</b></td>
    <td><b>Obligatoire</b></td>
    <td><b>Description</b></td>
  </tr>
  <tr>
    <td>Nom</td>
    <td>Oui</td>
    <td>Nom du document.</td>
  </tr>
  <tr>
    <td>Contenu</td>
    <td>Oui</td>
    <td>Contenu du document.</td>
  </tr>
  <tr>
    <td>ContentTransferEncoding</td>
    <td>Oui</td>
    <td>Encodage de transfert de contenu du message. ("none"|"base64")</td>
  </tr>
  <tr>
    <td>Forcer le remplacement</td>
    <td>Oui</td>
    <td>Si la valeur est TRUE et qu'un document existe avec le nom donné, il est remplacé.</td>
  </tr>
</table>
 

**Sorties :**

<table>
  <tr>
    <td><b>Nom</b></td>
    <td><b>Description</b></td>
  </tr>
  <tr>
    <td>XML de sortie</td>
    <td>Réponse de l'action Télécharger au format XML.</td>
  </tr>
  <tr>
    <td>Statut  </td>
    <td>Un téléchargement réussi du document retourne le code d'état 200 (OK).</td>
  </tr>
</table>

###10. Obtenir à partir des documents partagés (XML)

Cette action obtient le document à partir de la bibliothèque de documents, étant donné l'URL relative (structure de dossiers) du document.

 
**Entrées :**

<table>
  <tr>
    <td><b>Nom</b></td>
    <td><b>Obligatoire</b></td>
    <td><b>Description</b></td>
  </tr>
  <tr>
    <td>URI relatif du document</td>
    <td>Non</td>
    <td>Spécifiez l'URL du document, relative à  'Shared Documents'. Exemple : myspec1,myfolder/orders</td>
  </tr>
  <tr>
    <td>Type de fichier</td>
    <td>Oui</td>
    <td>Spécifiez si le fichier est un fichier binaire ou un fichier texte.</td>
  </tr>
</table>


**Sorties :**

<table>
  <tr>
    <td><b>Nom</b></td>
    <td><b>Description</b></td>
  </tr>
  <tr>
    <td>XML de sortie</td>
    <td>Contenu du document</td>
  </tr>
  <tr>
    <td>ContentTransferEncoding</td>
    <td>Encodage de transfert de contenu du message. ("none"|"base64")</td>
  </tr>
  <tr>
    <td>Statut</td>
    <td>L'exécution réussie d'une action retourne le code d'état 200 (OK).</td>
  </tr>
</table>

###11. Insérer dans des tâches (XML)

Cette action ajoute un élément dans la liste d'éléments. L'entrée doit être une charge utile XML.

** Entrées :**

<table>
  <tr>
    <td><b>Nom</b></td>
    <td><b>Obligatoire</b></td>
    <td><b>Description</b></td>
  </tr>
  <tr>
    <td>XML d'entrée</td>
    <td>Oui</td>
    <td>Message XML qui contient les valeurs des champs de l'élément de liste à insérer. Vous pouvez utiliser l'application API Transform pour générer le message XML.</td>
  </tr>
</table>
 
<b>Remarque :</b> Tous les paramètres de la  'List' sont renseignés de manière dynamique. Les paramètres obligatoires sont visibles, tandis que les paramètres facultatifs sont dans la section Avancé.

 
**Sorties :**

<table>
  <tr>
    <td><b>Nom</b></td>
    <td><b>Description</b></td>
  </tr>
  <tr>
    <td>ItemId</td>
    <td>ItemId de l'élément de liste ajouté.</td>
  </tr>
  <tr>
    <td>Statut  </td>
    <td>Une insertion réussie d'un élément de liste retourne le code d'état 200 (OK).</td>
  </tr>
</table>


###12. Mettre à jour des tâches (XML)

Cette action met à jour un élément dans la liste d'éléments. L'entrée doit être une charge utile XML.


**Entrées :**

<table>
  <tr>
    <td><b>Nom</b></td>
    <td><b>Obligatoire</b></td>
    <td><b>Description</b></td>
  </tr>
  <tr>
    <td>ItemId</td>
    <td>Oui</td>
    <td>ItemId de l'élément de liste.</td>
  </tr>
  <tr>
    <td>XML d'entrée</td>
    <td>Oui</td>
    <td>Message XML qui contient les valeurs des champs de l'élément de liste à insérer. Vous pouvez utiliser l'application API Transform pour générer le message XML.</td>
  </tr>
</table>

<b>Remarque :</b> Tous les paramètres de la  'List' sont renseignés de manière dynamique. Les paramètres obligatoires sont visibles, tandis que les paramètres facultatifs sont dans la section Avancé.

 
**Sorties :**

<table>
  <tr>
    <td><b>Nom</b></td>
    <td><b>Description</b></td>
  </tr>
  <tr>
    <td>Statut  </td>
    <td>Une mise à jour réussie d'un élément de liste retourne le code d'état 200 (OK).</td>
  </tr>
</table>


###13. Obtenir un élément à partir des tâches (XML)

Cette action obtient un élément de la liste d'éléments.


**Entrées :**

<table>
  <tr>
    <td><b>Nom</b></td>
    <td><b>Obligatoire</b></td>
    <td><b>Description</b></td>
  </tr>
  <tr>
    <td>ItemId</td>
    <td>Oui</td>
    <td>ItemId de l'élément de liste.</td>
  </tr>
</table>

**Sorties :**

<table>
  <tr>
    <td><b>Nom</b></td>
    <td><b>Description</b></td>
  </tr>
  <tr>
    <td>XML de sortie</td>
    <td>Message XML qui contient les valeurs des champs de l'élément de liste sélectionné. </td>
  </tr>
  <tr>
    <td>Statut  </td>
    <td>Une exécution réussie de l'action retourne le code d'état 200 (OK).</td>
  </tr>
</table>


<!--Image references-->
[1]: ./media/app-service-logic-connector-sharepoint/image_0.png
[2]: ./media/app-service-logic-connector-sharepoint/image_1.png
[3]: ./media/app-service-logic-connector-sharepoint/image_2.png
[4]: ./media/app-service-logic-connector-sharepoint/image_3.png
[5]: ./media/app-service-logic-connector-sharepoint/image_4.jpg
[6]: ./media/app-service-logic-connector-sharepoint/image_5.png
[7]: ./media/app-service-logic-connector-sharepoint/image_6.png

<!--HONumber=49-->