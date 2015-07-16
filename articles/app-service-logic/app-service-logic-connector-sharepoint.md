<properties 
   pageTitle="Créer un connecteur SharePoint à utiliser dans votre application logique" 
   description="Créer un connecteur SharePoint ; Utilisation du connecteur SharePoint dans votre application logique" 
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
   ms.date="06/22/2015"
   ms.author="vagarw"/>

# Utilisation du connecteur SharePoint dans votre application logique

Les applications logiques peuvent se déclencher selon diverses sources de données et proposent des connecteurs pour obtenir et traiter les données dans le cadre du flux. Le connecteur Microsoft SharePoint vous permet de vous connecter à Microsoft SharePoint Server ou SharePoint Online et de gérer des documents et des éléments de liste. Vous pouvez effectuer diverses actions, comme créer, mettre à jour, obtenir et supprimer des documents et éléments de liste. Avec un serveur SharePoint local, vous entrez la chaîne de connexion Service Bus dans le cadre de la configuration du connecteur et installer l'agent détecteur local pour la connexion au serveur.

L'application de la galerie des connecteurs SharePoint Online et SharePoint Server vous propose des déclencheurs et des actions en tant que mécanismes pour interagir avec SharePoint.

## Créer un connecteur SharePoint Online

Un connecteur peut être créé dans une application logique ou directement à partir d'Azure Marketplace. Pour créer un connecteur à partir de Marketplace :

1. Dans le tableau d'accueil Azure, sélectionnez **Marketplace**.
2. Sélectionnez **API Apps** et recherchez « Connecteur SharePoint Online ».
3. Entrez le nom, le plan App Service et d'autres propriétés.
4. Entrez les paramètres de package suivants :

	Nom | Requis | Description
--- | --- | ---
URL du site | Oui | Entrez l'URL complète du site web SharePoint. Par exemple, entrez : *https://microsoft.sharepoint.com/teams/wabstest*.
URL relatives des bibliothèques de documents/listes | Oui | Spécifiez les URL des bibliothèques de documents/listes, relatives à l'URL du site SharePoint, qui sont autorisées à être modifiées par le connecteur. Par exemple, entrez : *Listes/Tâche, Documents partagés*.

5. Lorsque vous avez terminé, les paramètres du package se présentent comme suit :
<br/>
![][1]

Une fois cette opération effectuée, vous pouvez alors créer une application logique dans le même groupe de ressources pour utiliser le connecteur SharePoint Online.

## Créer un connecteur SharePoint Server

Un connecteur peut être créé dans une application logique ou directement à partir d'Azure Marketplace. Pour créer un connecteur à partir de Marketplace :

1. Dans le tableau d'accueil Azure, sélectionnez **Marketplace**.
2. Sélectionnez **API Apps** et recherchez « Connecteur SharePoint Server ».
3. Entrez le nom, le plan App Service et d'autres propriétés.
4. Entrez les paramètres de package suivants :

	Nom | Requis | Description
--- | --- | ---
URL du site | Oui | Entrez l'URL complète du site web SharePoint. Par exemple, entrez : *https://microsoft.sharepoint.com/teams/wabstest*.
Mode d’authentification | Oui | Spécifiez le mode d'authentification pour la connexion à un site SharePoint. Les options incluent :<ul><li>Default</li><li>WindowsAuthentication</li><li>FormBasedAuthentication</li></ul><br/><br/>Si vous choisissez l’option Default, les identifiants qui exécutent le connecteur SharePoint sont utilisés ; Aucun nom d’utilisateur/mot de passe n’est requis. Le nom d'utilisateur et le mot de passe sont requis pour les autres types d'authentification.<br/><br/>**Remarque** L’authentification anonyme n'est pas prise en charge.
Nom d’utilisateur | Non | Entrez un nom d’utilisateur valide pour la connexion au site SharePoint, si le mode d’authentification n’est pas Default.
Mot de passe | Non | Entrez un mot de passe valide pour la connexion au site SharePoint, si le mode d’authentification n’est pas Default.
URL relatives des bibliothèques de documents/listes | Oui | Entrez les URL des bibliothèques de documents/listes, relatives à l'URL du site SharePoint, qui sont autorisées à être modifiées par le connecteur. Par exemple, entrez : *Listes/Tâche, Documents partagés*.
Chaîne de connexion Service Bus | Non | Si vous vous connectez en local, entrez la chaîne de connexion Service Bus Relay.<br/><br/>[Utilisation du Gestionnaire de connexion hybride](app-service-logic-hybrid-connection-manager.md)<br/>[Tarification Service Bus](http://azure.microsoft.com/pricing/details/service-bus/)

5. Lorsque vous avez terminé, les paramètres du package se présentent comme suit :
<br/>
![][2]

Une fois cette opération effectuée, vous pouvez alors créer une application logique dans le même groupe de ressources pour utiliser le connecteur SharePoint Server.


## Utilisation du connecteur SharePoint dans votre application logique

Une fois votre application API créée, vous pouvez utiliser le connecteur SharePoint comme déclencheur ou action pour votre application logique. Pour cela, vous devez procéder comme suit :

1. Créez une application logique et choisissez le même groupe de ressources qui comporte le connecteur SharePoint.

2. Ouvrez **Déclencheurs et actions** pour ouvrir le concepteur d'applications logiques et configurer votre flux. Le connecteur SharePoint apparaît dans la section des éléments récemment utilisés dans la galerie située sur le côté droit. Sélectionnez-le.

3. Si le connecteur SharePoint est sélectionné au début de l'application logique, il agit comme un déclencheur. Dans le cas contraire, les actions pourraient être effectuées sur le compte SharePoint à l'aide du connecteur.

4. Lorsque vous utilisez le connecteur SharePoint Online, vous devez vous authentifier et autoriser les applications logiques pour effectuer des opérations en votre nom. Pour démarrer l'autorisation, cliquez sur **Autoriser** sur le connecteur SharePoint :
<br/>
![][3]

5. Le fait de cliquer sur Autoriser ouvre la boîte de dialogue d'authentification de SharePoint. Entrez les détails de connexion du compte SharePoint sur lequel vous voulez effectuer les opérations :
<br/>
![][4]

6. Octroyez aux applications logiques l'accès à votre compte pour effectuer des opérations en votre nom :
<br/>
![][5]

7. Si le connecteur SharePoint est configuré en tant que déclencheur, les déclencheurs sont affichés. Sinon, une liste des actions s'affiche et vous pouvez choisir l'opération appropriée à effectuer :
<br/>
![][6]
<br/>
**URL relative configurée pour la bibliothèque de documents**
<br/>
![][7]
<br/>
**URL relative configurée pour la liste de documents**
<br/>

> [AZURE.NOTE]Pour les déclencheurs suivants, l'utilisateur est supposé avoir spécifié « Documents partagés, Listes/Tâches » dans les paramètres de package du connecteur, où « Documents partagés » est une bibliothèque de documents et « Listes/Tâches » est une liste.

##  Déclencheurs
Utilisez des déclencheurs pour lancer une application logique.

> [AZURE.NOTE]Les déclencheurs suppriment les fichiers après lecture. Pour conserver ces fichiers, indiquez une valeur pour l'emplacement d'archivage.

### 1. Nouveau document dans les documents partagés (JSON)
Ce déclencheur est déclenché quand un nouveau document est disponible dans Documents partagés.

#### Entrée

Nom | Requis | Description
--- | --- | ---
Nom de la vue | Non | Entrez une vue valide utilisée pour filtrer les documents à sélectionner. Par exemple, entrez : « Commandes approuvées ». Pour traiter tous les documents existants, laissez ce champ vide. 
Emplacement d'archive | Non | Entrez une URL de dossier valide, relative au site SharePoint, où les documents traités sont archivés.
Remplacer dans l'archive | Non | Cochez cette option pour remplacer un fichier dans le chemin d'accès de l'archive s'il existe déjà.
Requête CAML | Non, Avancé | Entrez une requête Caml valide pour filtrer les documents. Par exemple, entrez : `<Where><Geq><FieldRef Name='ID'/><Value Type='Number'>10</Value></Geq></Where>`

#### Sortie

Nom | Description
--- | --- 
Nom | Nom du document.
Contenu | Contenu du document.
ContentTransferEncoding | Encodage de transfert de contenu du message. (« none » ou « base64 »)

**Remarque** : toutes les colonnes de l'élément de document sont affichées dans les propriétés de sortie.


### 2. Nouvel élément dans les tâches (JSON)
Ce déclencheur est déclenché quand un nouvel élément est ajouté à la liste Tâches.

#### Entrée

Nom | Requis | Description
--- | --- | ---
Nom de la vue | Non | Entrez une vue valide utilisée pour filtrer les éléments de la liste. Par exemple, entrez : « Commandes approuvées ». Pour traiter tous les nouveaux éléments, laissez ce champ vide. 
Emplacement d'archive | Non | Entrez une URL de dossier valide, relative au site SharePoint, où les éléments de liste traités sont archivés.
Requête CAML | Non, Avancé | Entrez une requête Caml valide pour filtrer les documents. Par exemple, entrez : `<Where><Geq><FieldRef Name='ID'/><Value Type='Number'>10</Value></Geq></Where>`

#### Sortie

Nom | Description
--- | --- 
Les colonnes de la liste sont renseignées de manière dynamique et présentées dans les paramètres de sortie. | &nbsp;


### 3. Nouveau document dans les documents partagés (XML)

Ce déclencheur est déclenché quand un nouveau document est disponible dans Documents partagés. Le nouveau document est retourné sous forme de message XML.

#### Entrée

Nom | Requis | Description
--- | --- | ---
Nom de la vue | Non | Entrez une vue valide utilisée pour filtrer les documents à sélectionner. Par exemple, entrez : « Commandes approuvées ». Pour traiter tous les documents existants, laissez ce champ vide. 
Emplacement d'archive | Non | Spécifiez une URL de dossier valide, relative au site SharePoint, où les documents traités sont archivés.
Remplacer dans l'archive | Non | Cochez cette option pour remplacer un fichier dans le chemin d'accès de l'archive s'il existe déjà.
Requête CAML | Non, Avancé | Entrez une requête Caml valide pour filtrer les documents. Par exemple, entrez : `<Where><Geq><FieldRef Name='ID'/><Value Type='Number'>10</Value></Geq></Where>`

#### Sortie

Nom | Description
--- | --- 
Contenu | Contenu du document.
ContentTransferEncoding | Encodage de transfert de contenu du message. (« none » ou « base64 »)


### 4. Nouvel élément dans les tâches (XML)

Ce déclencheur est déclenché quand un nouvel élément est ajouté à la liste Tâches. Le nouvel élément de liste est retourné sous forme de message XML.

#### Entrée

Nom | Requis | Description
--- | --- | ---
Nom de la vue | Non | Entrez une vue valide utilisée pour filtrer les éléments de la liste. Exemple : Commandes approuvées. Pour traiter tous les nouveaux éléments, laissez ce champ vide. 
Emplacement d'archive | Non | Entrez une URL de dossier valide, relative au site SharePoint, où les éléments de liste traités sont archivés.
Requête CAML | Non, Avancé | Entrez une requête Caml valide pour filtrer les éléments de liste. Par exemple, entrez : `<Where><Geq><FieldRef Name='ID'/><Value Type='Number'>10</Value></Geq></Where>`

#### Sortie

Nom | Description
--- | --- 
Contenu | Contenu du document.
ContentTransferEncoding | Encodage de transfert de contenu du message. (« none » ou « base64 »)


##  Actions
Pour les actions suivantes, l'utilisateur est supposé avoir spécifié « Documents partagés, Listes/Tâches » dans les paramètres de package du connecteur, où « Documents partagés » est une bibliothèque de documents et « Listes/Tâches » est une liste.

### 1. Télécharger vers les documents partagés (JSON)

Cette action télécharge le nouveau document vers Documents partagés. L'entrée est un objet JSON fortement typé avec tous les champs de colonne de la bibliothèque de documents.

#### Entrée

Nom | Requis | Description
--- | --- | ---
Nom | Oui | Nom du document.
Contenu | Oui | Contenu du document.
ContentTransferEncoding | Oui | Encodage de transfert de contenu du message. (« none » ou « base64 »)
Forcer le remplacement | Oui | Si la valeur est TRUE et qu'un document existe avec le nom donné, il est remplacé.
ReqParam1* | Oui | Il s'agit d'un des paramètres requis pour ajouter un document dans la bibliothèque de documents.
ReqParam2* | Oui | Il s'agit d'un des paramètres requis pour ajouter un document dans la bibliothèque de documents.
OptionalParam1* | Non. Avancé | Il s'agit d'un des paramètres facultatifs pour ajouter un document dans la bibliothèque de documents.
OptionalParam2* | Non. Avancé | Il s'agit d'un des paramètres facultatifs pour ajouter un document dans la bibliothèque de documents.

**Remarque** : tous les paramètres de la bibliothèque de documents sont renseignés de manière dynamique. Les paramètres obligatoires sont visibles et les paramètres facultatifs se trouvent dans la section Avancé.

#### Sortie

Nom | Description
--- | --- 
ItemId | ItemId du document ajouté dans la bibliothèque de documents.
État | Un téléchargement réussi du document retourne le code d'état 200 (OK).


 

### 2. Obtenir à partir des documents partagés (JSON)
Cette action obtient le document à partir de la bibliothèque de documents, étant donné l'URL relative (structure de dossiers) du document.

#### Entrée

Nom | Requis | Description
--- | --- | ---
URI relatif du document | Non | Entrez l'URL du document, relative à « Documents partagés ». Par exemple, entrez : *myspec1,myfolder/orders*.

#### Sortie

Nom | Description
--- | --- 
Contenu | Contenu du document 
ContentTransferEncoding | Encodage de transfert de contenu du message. (« none » ou « base64 »)
Statut | L'exécution réussie d'une action retourne le code d'état 200 (OK).
Param1* | Il s'agit d'un des paramètres d'un document dans la bibliothèque de documents.
Param2* | Il s'agit d'un des paramètres d'un document dans la bibliothèque de documents.

**Remarque** : tous les paramètres de la bibliothèque de documents sont renseignés de manière dynamique. Ils se trouvent aussi dans la section Avancé.

 

### 3. Supprimer des documents partagés

Cette action supprime le document de la bibliothèque de documents, étant donné l'URL relative (structure de dossiers) du document.

#### Entrée

Nom | Requis | Description
--- | --- | ---
URI relatif du document | Non | Entrez l'URL du document, relative à « Documents partagés ». Par exemple, entrez : *myspec1,myfolder/orders*.

#### Sortie

Nom | Description
--- | --- 
Statut | L'exécution réussie d'une action retourne le code d'état 200 (OK).


### 4. Insérer dans des tâches (JSON)

Cette action ajoute un élément dans la liste d'éléments.

#### Entrée

Nom | Requis | Description
--- | --- | ---
ReqParam1* | Oui | Il s'agit d'un des paramètres requis pour ajouter un élément dans la liste.
ReqParam2* | Oui | Il s'agit d'un des paramètres requis pour ajouter un élément dans la liste.
OptionalParam1* | Non. Avancé | Il s'agit d'un des paramètres requis pour ajouter un élément dans la liste.
OptionalParam2* | Non. Avancé | Il s'agit d'un des paramètres requis pour ajouter un élément dans la liste.

**Remarque** : tous les paramètres de la liste sont renseignés de manière dynamique. Les paramètres obligatoires sont visibles et les paramètres facultatifs se trouvent dans la section Avancé.

#### Sortie

Nom | Description
--- | --- 
ItemId | ItemId de l'élément de liste ajouté.
État | Une insertion réussie d'un élément de liste retourne le code d'état 200 (OK).


### 5. Mettre à jour des tâches (JSON)

Cette action met à jour un élément dans la liste d'éléments.

#### Entrée

Nom | Requis | Description
--- | --- | ---
ItemId | Oui | ItemId de l'élément de liste.
ReqParam1* | Non | Il s'agit d'un des paramètres requis pour ajouter un élément dans la liste.
ReqParam2* | Non | Il s'agit d'un des paramètres requis pour ajouter un élément dans la liste.
OptionalParam1* | Non. Avancé | Il s'agit d'un des paramètres requis pour ajouter un élément dans la liste.
OptionalParam2* | Non. Avancé | Il s'agit d'un des paramètres requis pour ajouter un élément dans la liste.

**Remarque** : tous les paramètres de la liste sont renseignés de manière dynamique. Les paramètres obligatoires sont visibles et les paramètres facultatifs se trouvent dans la section Avancé.

#### Sortie

Nom | Description
--- | --- 
Statut | Une mise à jour réussie d'un élément de liste retourne le code d'état 200 (OK).


### 6. Obtenir un élément à partir des tâches (JSON)

Cette action obtient un élément de la liste d'éléments.

#### Entrée

Nom | Requis | Description
--- | --- | ---
ItemId | Oui | ItemId de l'élément de liste.

#### Sortie

Nom | Description
--- | --- 
Column1* | Il s'agit d'un des paramètres dans la liste.
Column2* | Il s'agit d'un des paramètres dans la liste.
État | Une exécution réussie de l'action retourne le code d'état 200 (OK).

**Remarque** : les colonnes de la liste sont renseignées de manière dynamique et présentées dans les paramètres de sortie.


### 7. Supprimer un élément des tâches

Cette action supprime un élément de la liste d'éléments.

#### Entrée

Nom | Requis | Description
--- | --- | ---
ItemId | Oui | ItemId de l'élément de liste.

#### Sortie

Nom | Description
--- | --- 
Statut | Une suppression réussie d'un élément de liste retourne le code d'état 200 (OK).


### 8. Répertorier les documents partagés (JSON)

Cette action répertorie tous les documents dans une bibliothèque de documents. Vous pouvez utiliser une vue ou une requête Caml pour filtrer les documents.

#### Entrée

Nom | Requis | Description
--- | --- | ---
Nom de la vue | Non | Entrez une vue valide utilisée pour filtrer les documents à sélectionner. Par exemple, entrez : « Commandes approuvées ». Pour traiter tous les documents existants, laissez ce champ vide. 
Requête CAML | Non | Entrez une requête Caml valide pour filtrer les documents. Par exemple, entrez : `<Where><Geq><FieldRef Name='ID'/><Value Type='Number'>10</Value></Geq></Where>`

#### Sortie

Nom | Description
--- | --- 
Documents | Tableau de tous les documents. Chaque document comporte les champs suivants : <ul><li>Documents </li><li>Nom</li><li>ID de l'élément</li><li>URL complète de l'élément</li><li>Avancé</li><li>URL de modification de l'élément</li><li>Nom de la liste</li><li>URL complète de la liste</li></ul>
Statut | Une insertion réussie d'un élément de liste retourne le code d'état 200 (OK).


### 9. Télécharger vers les documents partagés (XML)

Cette action télécharge le nouveau document vers Documents partagés. Le document d'entrée doit être une charge utile XML. La réponse de l'action sera une charge utile XML.
 
#### Entrée

Nom | Requis | Description
--- | --- | ---
Nom | Oui | Nom du document.
Contenu | Oui | Contenu du document.
ContentTransferEncoding | Oui | Encodage de transfert de contenu du message. (« none » ou « base64 »)
Forcer le remplacement | Oui | Si la valeur est TRUE et qu'un document existe avec le nom donné, il est remplacé.
 
#### Sortie

Nom | Description
--- | --- 
XML de sortie | Réponse de l'action Télécharger au format XML.
État | Un téléchargement réussi du document retourne le code d'état 200 (OK).

### 10. Obtenir à partir des documents partagés (XML)

Cette action obtient le document à partir de la bibliothèque de documents, étant donné l'URL relative (structure de dossiers) du document.

#### Entrée

Nom | Requis | Description
--- | --- | ---
URI relatif du document | Non | Entrez l'URL du document, relative à « Documents partagés ». Par exemple, entrez : *myspec1,myfolder/orders*.
Type de fichier | Oui | Entrez si le fichier est un fichier binaire ou un fichier texte.

#### Sortie

Nom | Description
--- | --- 
XML de sortie | Contenu du document
ContentTransferEncoding | Encodage de transfert de contenu du message. (« none » ou « base64 »)
Statut | L'exécution réussie d'une action retourne le code d'état 200 (OK).

### 11. Insérer dans des tâches (XML)

Cette action ajoute un élément dans la liste d'éléments. L'entrée doit être une charge utile XML.

#### Entrée

Nom | Requis | Description
--- | --- | ---
XML d'entrée | Oui | Message XML qui contient les valeurs des champs de l'élément de liste à insérer. Vous pouvez utiliser l'application API Transform pour générer le message XML.

**Remarque** : tous les paramètres de la liste sont renseignés de manière dynamique. Les paramètres obligatoires sont visibles et les paramètres facultatifs se trouvent dans la section Avancé.

#### Sortie

Nom | Description
--- | ---
ItemId | ItemId de l'élément de liste ajouté.
État | Une insertion réussie d'un élément de liste retourne le code d'état 200 (OK).


### 12. Mettre à jour des tâches (XML)

Cette action met à jour un élément dans la liste d'éléments. L'entrée doit être une charge utile XML.

#### Entrée

Nom | Requis | Description
--- | --- | ---
ItemID | Oui | ItemId de l'élément de liste.
XML d'entrée | Oui | Message XML qui contient les valeurs des champs de l'élément de liste à insérer. Vous pouvez utiliser l'application API Transform pour générer le message XML.

**Remarque** : tous les paramètres de la liste sont renseignés de manière dynamique. Les paramètres obligatoires sont visibles et les paramètres facultatifs se trouvent dans la section Avancé.

#### Sortie

Nom | Description
--- | ---
Statut | Une mise à jour réussie d'un élément de liste retourne le code d'état 200 (OK).


### 13. Obtenir un élément à partir des tâches (XML)

Cette action obtient un élément de la liste d'éléments.

#### Entrée

Nom | Requis | Description
--- | --- | ---
ItemID | Oui | ItemId de l'élément de liste.

#### Sortie

Nom | Description
--- | ---
XML de sortie | Message XML qui contient les valeurs des champs de l'élément de liste sélectionné.
État | Une exécution réussie de l'action retourne le code d'état 200 (OK).


## Configuration hybride (facultatif)

> [AZURE.NOTE]Cette étape n'est requise que si vous utilisez SharePoint en local, derrière le pare-feu.

App Service utilise le Gestionnaire de configuration hybride pour se connecter en toute sécurité à votre système local. Si votre connecteur utilise un serveur SharePoint en local, le Gestionnaire de connexion hybride est requis.

Consultez la rubrique [Utilisation du Gestionnaire de connexion hybride](app-service-logic-hybrid-connection-manager.md).

## En faire plus avec votre connecteur
Maintenant que le connecteur est créé, vous pouvez l'ajouter à un flux d'entreprise à l'aide d'une application logique. Voir [Que sont les applications logiques ?](app-service-logic-what-are-logic-apps.md).

Vous pouvez également consulter les statistiques de performances et contrôler la sécurité du connecteur. Voir [Gérer et surveiller les applications API et le connecteur](app-service-api-manage-in-portal.md).

<!--Image references-->
[1]: ./media/app-service-logic-connector-sharepoint/image_0.png
[2]: ./media/app-service-logic-connector-sharepoint/image_1.png
[3]: ./media/app-service-logic-connector-sharepoint/image_2.png
[4]: ./media/app-service-logic-connector-sharepoint/image_3.png
[5]: ./media/app-service-logic-connector-sharepoint/image_4.jpg
[6]: ./media/app-service-logic-connector-sharepoint/image_5.png
[7]: ./media/app-service-logic-connector-sharepoint/image_6.png
 

<!-----HONumber=62-->