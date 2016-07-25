<properties
	pageTitle="Comment utiliser des API personnalisées dans les flux logiques et PowerApps | Microsoft Azure"
	description="Présentation des API personnalisées, utilisation de fournisseurs OAuth, et utilisation de Swagger pour ajouter des API personnalisées dans PowerApps et les flux logiques"
	services=""
    suite="powerapps"
	documentationCenter="" 
	authors="sunaysv"
	manager="erikre"
	editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="07/12/2016"
   ms.author="mandia"/>

# Présentation des API personnalisées

Les API personnalisées sont des API RESTful que vous pouvez appeler et utiliser avec PowerApps et vos flux logiques. Elles peuvent être hébergées à n’importe quel endroit, à condition qu’une spécification bien documentée conforme à la norme [OpenAPI][1] existe.

>[AZURE.IMPORTANT] Cette rubrique a été déplacée vers powerapps.microsoft.com dans [Présentation des API personnalisées](https://powerapps.microsoft.com/tutorials/register-custom-api/). Accédez à PowerApps pour la dernière version. Ce lien Azure est archivé.

## Ce dont vous avez besoin pour commencer

- Les API personnalisées peuvent être utilisées par toute personne utilisant PowerApps. PowerApps Enterprise n’est pas nécessaire.
- Vous avez besoin d’un fichier Swagger (.json) et d’une icône pour votre API personnalisée. Cette rubrique décrit plusieurs façons de créer le fichier Swagger. Vous pouvez utiliser l’image de votre choix comme icône.


## Authentification

Vous pouvez choisir l’une des méthodes d’authentification ci-dessous :

- Authentification de base
- OAuth 2.0 : vous pouvez utiliser l’un des fournisseurs OAuth 2.0 pris en charge suivants avec votre API personnalisée (des fournisseurs supplémentaires seront bientôt pris en charge) :

	- Azure Active Directory
	- Box
	- Dropbox
	- Facebook
	- Google
	- Instagram
	- OneDrive
	- SalesForce
	- Slack
	- Yammer

> [AZURE.NOTE] L’authentification par clé API va être prise en charge prochainement.


Pour en savoir plus sur la spécification du type d’authentification dans votre document OpenAPI (Swagger), consultez [Spécification OpenAPI][1].

Si votre point de terminaison d’API autorise l’accès non authentifié, supprimez l’objet ```securityDefintions``` dans le fichier OpenAPI (Swagger). Dans l’exemple ci-dessous, supprimez la totalité de l’objet ```securityDefintions``` suivant :

```
  "securityDefinitions": {
    "AAD": {
      "type": "oauth2",
      "flow": "implicit",
      "authorizationUrl": "https://login.windows.net/common/oauth2/authorize",
      "scopes": []
    }
  },
```

> [AZURE.TIP] Dans les fichiers .json, vous ne pouvez pas ajouter de commentaires. Tout le texte fait partie intégrante des données.

### Exemples d’authentification
* [Azure Resource Manager](powerapps-azure-resource-manager-tutorial.md) avec l’authentification AAD
* [Azure WebApp](powerapps-web-api-tutorial.md) avec l’authentification AAD

## Inscrire une API personnalisée

### Étape 1 : Créer un fichier Swagger

Vous pouvez créer un fichier Swagger à partir de **n’importe** quel point de terminaison d’API, notamment :

- Une API qui est publiée et disponible publiquement. Par exemple, [Spotify][2], [Uber][3], [Slack][4], [Rackspace][5], etc.
- Une API que vous créez et déployez sur un service cloud où vous pouvez déployer des applications web. Par exemple, Amazon Web Services (AWS), Heroku, Azure Web Apps, Google Cloud, etc.
- Une API qui est déployée dans votre réseau ou sur votre ordinateur, à condition que l’API soit disponible publiquement sur Internet.

Quand vous créez le fichier Swagger, un fichier .json est créé. Conservez ce fichier .json à portée de main.

#### Obtenir de l’aide sur la création des fichiers Swagger

- Si vous n’êtes pas familiarisé avec le processus de création d’un fichier Swagger, nous vous conseillons de consulter la rubrique [Prise en main de Swagger][6].
 
- Pour créer votre propre API, déployer cette API dans Azure et créer un fichier Swagger basé sur cette nouvelle API, utilisez le [didacticiel API web](powerapps-web-api-tutorial.md). Ce didacticiel vous permet de créer un fichier Swagger valide. Vous pouvez aussi consulter l’[exemple Hello World][7] fourni sur GitHub.

- Pour valider vos fichiers Swagger, utilisez l’[éditeur de Swagger][8]. Vous collez les données de votre fichier .json, et la validation s’effectue automatiquement.

- Pour personnaliser votre document Swagger pour l’utiliser avec PowerApps et vos flux logiques, consultez la rubrique [Personnaliser votre définition Swagger](powerapps-how-to-swagger.md).

### Étape 2 : Ajouter une connexion à l’API personnalisée
Vous avez créé le fichier Swagger (fichier .json) pour votre API personnalisée. Vous allez maintenant ajouter la connexion à PowerApps. Vous devez également ajouter une icône pour cette API personnalisée.

1. Accédez au [portail web][9] PowerApps et connectez-vous avec votre compte professionnel.

	> [AZURE.NOTE] Actuellement, les API personnalisées peuvent uniquement être utilisées dans le portail web PowerApps. Elles ne sont pas disponibles dans le client PowerApps.

2. Sélectionnez **Connexions**, puis **Ajouter une connexion** : ![](./media/powerapps-register-custom-api/createnewconnection.png "Créer une API personnalisée")

3. Sélectionnez **Ajouter une API personnalisée** : ![](./media/powerapps-register-custom-api/connecttocustomapi.png "Créer une API personnalisée") Ajoutez les propriétés de votre API, notamment les fichiers .json et icône. Ensuite, sélectionnez **Suivant** :

	|Propriété|Description|
|---|---|
|Nom|Entrez le nom de votre API personnalisée. Pour cet exemple d’API web, nommez-la **MySampleWebAPI**.|
|Définition d’API Swagger|Recherchez le fichier .json créé dans Swagger.|
|Charger l’icône d’API|Recherchez le fichier icône.|
|Description|Entrez une description pour votre API personnalisée. Pour cet exemple d’API web, entrez **Sample Web API Tutorial**.|

4. Entrez les propriétés d’authentification. Si le fichier .json utilise l’authentification OAuth2 dans l’objet ```securityDefintions```, vous êtes invité à entrer les valeurs suivantes :

	|Propriété|Description|
|---|---|
|ID client|Un ID client est fourni si vous utilisez l’un des fournisseurs d’identité OAuth pris en charge (voir la liste dans cette rubrique). Entrez cet ID client.
|Clé secrète client|Entrez la clé secrète client provenant du fournisseur d’identité choisi.|  

	Les **exemples d’authentification** présentés dans cette rubrique montrent des exemples de ces propriétés OAuth.

	Si le fichier .json n’utilise pas l’objet ```securityDefintions```, aucune valeur supplémentaire n’est demandée.

5. Sélectionnez **Créer**. Votre API personnalisée s’affiche maintenant dans **Connexions disponibles** :

	![](./media/powerapps-register-custom-api/mycustomapi.png "Connexions disponibles")


> [AZURE.TIP] Si la validation des fichiers Swagger échoue, cela peut être dû à la présence de caractères en trop. Par exemple, quasiment toutes les données doivent être entre guillemets, y compris les sites web. Si vous n’avez pas mis `https://mywebapi.mywebsite.com` entre guillemets, le fichier n’est pas validé.


### Étape 3 : Ajouter l’API personnalisée à PowerApps et à un flux logique
Vous êtes maintenant prêt à utiliser l’API personnalisée avec votre PowerApps ou flux logique. Dans cette section, nous utiliserons une API personnalisée appelée Weather.

#### Ajouter l’API personnalisée à votre flux logique
Dans cette étape, nous allons créer un flux logique très simple pour vous montrer comment ajouter votre API personnalisée. Pour en savoir plus, consultez [Prise en main des flux logiques][10].

1. Dans le [portail web][9] PowerApps, sélectionnez l’onglet **Accueil**.
2. Sous **Make a logic flow** (Créer un flux logique), sélectionnez **Get started** (Démarrer).
3. Cette fenêtre propose plusieurs modèles de flux logiques existants pour des scénarios courants. Vous pouvez utiliser l’un de ces modèles et y ajouter votre propre API personnalisée. Vous pouvez aussi créer entièrement votre flux logique à l’aide de l’option **Create from blank** (Créer à partir de zéro).

	Pour ajouter votre API personnalisée, la méthode la plus rapide est de sélectionner **Create from blank** (Créer à partir de zéro). Cette action ouvre le flux logique suivant : ![](./media/powerapps-register-custom-api/createfromblank.png "Début du flux logique")

4. Sélectionnez **Périodicité** et définissez la fréquence à 1 minute : ![](./media/powerapps-register-custom-api/logicrecurrence.png "Sélectionnez la périodicité")

5. Sélectionnez le signe plus (![](./media/powerapps-register-custom-api/flowplussign.png)), puis sélectionnez **Ajouter une action**. Votre API personnalisée est répertoriée : ![](./media/powerapps-register-custom-api/logicflow.png "Votre API personnalisée")

Les étapes suivantes sont déterminées par les actions réalisables par votre API. S’il s’agit d’une application météorologique comme dans notre exemple, votre API peut obtenir la température actuelle et envoyer ensuite un e-mail avec Office 365 :

![](./media/powerapps-register-custom-api/logicflowexample.png "Exemple « Weather »")



#### Ajouter l’API personnalisée à votre application PowerApps
Dans cette étape, nous allons créer une application PowerApps très simple pour vous montrer comment ajouter votre API personnalisée. Pour en savoir plus, consultez [Créer une application à partir de données][11].

> [AZURE.NOTE] Actuellement, les API personnalisées peuvent uniquement être utilisées dans le portail web PowerApps. Elles ne sont pas disponibles dans le client PowerApps.

1. Dans le [portail web][9] PowerApps, sélectionnez **New PowerApp** (Nouvelle application PowerApps) : ![](./media/powerapps-register-custom-api/newpowerapp.png "Sélectionnez « New PowerApp » (Nouvelle application PowerApps)")
2. Un nouvel onglet s’ouvre dans votre navigateur. Dans cet onglet, une application PowerApps vide est créée automatiquement. Sélectionnez **connect to data** (se connecter aux données) : ![](./media/powerapps-register-custom-api/blankpowerapp.png "Sélectionnez « connect to data » (se connecter aux données)")
3. Dans l’onglet **Contenu**, sélectionnez **Sources de données** : ![](./media/powerapps-register-custom-api/datasources.png "Sélectionnez « connect to data » (se connecter aux données)")
4. Dans le nouvel écran, sous **Mes connexions**, sélectionnez votre API personnalisée : ![](./media/powerapps-register-custom-api/screencustomapi.png "Sélectionnez votre API personnalisée")
5. Sélectionnez **Ajouter une source de données**.

Une fois que vous avez ajouté votre API personnalisée, vous pouvez l’utiliser dans la barre de fonctions, une zone de texte ou d’autres éléments. Par exemple, dans la barre de fonctions, vous pouvez commencer à taper **MySampleWebAPI** pour voir s’afficher les fonctions disponibles. Pour obtenir un exemple d’utilisation de l’API Office 365, consultez [Afficher les données d’Office 365][12].


## Partager une API personnalisée
Les utilisateurs peuvent également partager leurs API personnalisées entre eux. Une fois que vous avez ajouté votre API personnalisée, sélectionnez l’onglet **Connexions**, sélectionnez **API personnalisées**, puis sélectionnez l’icône de partage :

![](./media/powerapps-register-custom-api/sharecustomapi.png "Partager l’API personnalisée")

> [AZURE.NOTE] Vous pouvez partager des API personnalisées uniquement avec d’autres utilisateurs de votre organisation.

## Quota et limitation

- Vous pouvez créer jusqu’à cinq API personnalisées dans un compte PowerApps. Les API personnalisées qui sont partagées avec vous n’entrent pas dans ce quota.
- Pour chaque connexion créée sur une API personnalisée, les utilisateurs peuvent effectuer 500 demandes par minute au maximum.
- Gardez à l’esprit que la suppression d’une API personnalisée supprime également toutes les connexions existantes à cette API.

Envoyez vos questions ou commentaires sur les API personnalisées à l’adresse [customapishelp@microsoft.com](mailto:customapishelp@microsoft.com).

<!--Reference links in article-->
[1]: https://github.com/OAI/OpenAPI-Specification/blob/master/versions/2.0.md#securityDefinitionsObject
[2]: https://developer.spotify.com/
[3]: https://developer.uber.com/
[4]: https://api.slack.com/
[5]: http://docs.rackspace.com/
[6]: http://swagger.io/getting-started/
[7]: https://github.com/OAI/OpenAPI-Specification/wiki/Hello-World-Sample
[8]: http://editor.swagger.io/#/
[9]: https://web.powerapps.com
[10]: https://powerapps.microsoft.com/tutorials/get-started-logic-flow/
[11]: https://powerapps.microsoft.com/tutorials/get-started-create-from-data/
[12]: https://powerapps.microsoft.com/tutorials/show-office-data/

<!---HONumber=AcomDC_0713_2016-->