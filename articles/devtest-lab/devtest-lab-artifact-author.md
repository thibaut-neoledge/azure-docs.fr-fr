<properties 
	pageTitle="Créer des artefacts personnalisés pour votre machine virtuelle DevTest Labs | Microsoft Azure"
	description="Découvrez comment créer vos propres artefacts pour les utiliser avec DevTest Labs"
	services="devtest-lab,virtual-machines"
	documentationCenter="na"
	authors="tomarcher"
	manager="douge"
	editor=""/>

<tags
	ms.service="devtest-lab"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/01/2016"
	ms.author="tarcher"/>

#Créer des artefacts personnalisés pour vos machines virtuelles DevTest Labs

> [AZURE.VIDEO how-to-author-custom-artifacts] 

## Vue d'ensemble
Les **artefacts** sont utilisés pour déployer et configurer votre application après l’approvisionnement d’une machine virtuelle. Un artefact se compose d'un fichier de définition d'artefact et autres fichiers de script qui sont stockés dans un dossier de dépôt git. Les fichiers de définition d'artefact se composent de JSON et d'expressions que vous pouvez utiliser pour spécifier ce que vous voulez installer sur une machine virtuelle. Par exemple, vous pouvez définir le nom de l'artefact, la commande à exécuter et les paramètres qui sont disponibles lorsque la commande est exécutée. Vous pouvez faire référence à d'autres fichiers de script dans le fichier de définition d'artefact par nom.

##Format de fichier de définition d'artefact
L'exemple suivant montre les sections qui composent la structure de base d'un fichier de définition.

	{
	  "$schema": "https://raw.githubusercontent.com/Azure/azure-devtestlab/master/schemas/2015-01-01/dtlArtifacts.json",
	  "title": "",
	  "description": "",
	  "iconUri": "",
	  "targetOsType": "",
	  "parameters": {
	    "<parameterName>": {
	      "type": "",
	      "displayName": "",
	      "description": ""
	    }
	  },
	  "runCommand": {
	    "commandToExecute": ""
	  }
	}

| Nom de l'élément | Requis ? | Description
| ------------ | --------- | -----------
| $schema | Non | Emplacement du fichier de schéma JSON qui permet de tester la validité du fichier de définition.
| title | Oui | Nom de l'artefact affiché dans le laboratoire.
| description | Oui | Description de l'artefact affiché dans le laboratoire.
| iconUri | Non | URI de l'icône affichée dans le laboratoire.
| targetOsType | Oui | Système d'exploitation de la machine virtuelle où l'artefact sera installé. Les options prises en charge sont : Windows et Linux.
| parameters | Non | Les valeurs fournies lorsque la commande d'installation d'artefact est exécutée sur une machine. Cela permet de personnaliser votre artefact.
| runCommand | Oui | Commande d'installation d'artefact qui est exécutée sur une machine virtuelle.

###Paramètres d'artefact

Dans la section des paramètres du fichier de définition, vous spécifiez les valeurs qu'un utilisateur peut entrer lors de l'installation d'un artefact. Vous pouvez faire référence à ces valeurs dans la commande d'installation d'artefact.

Vous définissez des paramètres avec la structure suivante.

	"parameters": {
	    "<parameterName>": {
	      "type": "<type-of-parameter-value>",
	      "displayName": "<display-name-of-parameter>",
	      "description": "<description-of-parameter>"
	    }
	  }

| Nom de l'élément | Requis ? | Description
| ------------ | --------- | -----------
| type | Oui | Type de la valeur du paramètre. Consultez la liste ci-dessous pour découvrir les types autorisés :
| displayName Yes | Nom du paramètre qui est affiché à un utilisateur dans le laboratoire.
| description | Oui | Description du paramètre qui est affiché dans le laboratoire.

Les types autorisés sont :

- string : n'importe quelle chaîne JSON valide
- int : n'importe quel entier JSON valide
- bool : n'importe quel booléen JSON valide
- array : n'importe quel tableau JSON valide

##Expressions et fonctions d'artefact

Vous pouvez utiliser une expression et des fonctions pour construire la commande d'installation d'artefact. Les expressions sont placées entre crochets ([ et ]) et sont évaluées au moment où l'artefact est installé. Les expressions peuvent apparaître n'importe où dans une valeur de chaîne JSON et retournent toujours une autre valeur JSON. Si vous avez besoin d'utiliser une chaîne littérale qui commence par un crochet [, vous devez utiliser deux crochets [[. En général, vous utilisez des expressions avec des fonctions pour construire une valeur. Comme dans JavaScript, les appels de fonctions sont formatés ainsi : functionName(arg1,arg2,arg3)

La liste suivante vous indique les fonctions courantes.

- parameters(parameterName) : renvoie une valeur de paramètre fournie lors de l'exécution de la commande de l'artefact.
- concat(arg1,arg2,arg3,...) : combine plusieurs valeurs de chaîne. Cette fonction peut prendre n'importe quel nombre d'arguments.

L'exemple suivant montre comment utiliser les fonctions et l'expression pour construire une valeur.

	runCommand": {
	     "commandToExecute": "[concat('powershell.exe -File startChocolatey.ps1'
	, ' -RawPackagesList ', parameters('packages')
	, ' -Username ', parameters('installUsername')
	, ' -Password ', parameters('installPassword'))]"
	}

##Création d'un artefact personnalisé

Créez votre artefact personnalisé en suivant les étapes ci-dessous :

1. Installer un éditeur JSON : vous aurez besoin d'un éditeur JSON pour utiliser les fichiers de définition d'artefact. Nous vous recommandons d'utiliser [Visual Studio Code](https://code.visualstudio.com/), qui est disponible pour Windows, Linux et OS X.

1. Obtenir un exemple d'artifactfile.json : découvrez les artefacts créés par l'équipe Azure DevTest Labs dans notre [référentiel GitHub](https://github.com/Azure/azure-devtestlab) où nous avons créé une bibliothèque riche d'artefacts qui vous permettra de créer vos propres artefacts. Téléchargez un fichier de définition d'artefact et modifiez-le pour créer vos propres artefacts.

1. Utiliser IntelliSense : utilisez IntelliSense pour voir des éléments valides qui peuvent être utilisés pour construire un fichier de définition d'artefact. Vous pouvez également voir les différentes options pour les valeurs d'un élément. Par exemple, IntelliSense vous montre les deux options de Windows ou Linux lorsque vous modifiez l'élément **targetOsType**.

1. Stockage de l'artefact dans un dépôt git
	1. Créez un répertoire distinct pour chaque artefact où le nom du répertoire est le même que le nom de l'artefact.
	1. Stockez le fichier de définition d'artefact (artifactfile.json) dans le répertoire que vous avez créé.
	1. Stockez les scripts qui sont référencés à partir de la commande d'installation d'artefact.

	Voici un exemple de dossier d'artefact :

	![Exemple de dépôt git d'artefacts](./media/devtest-lab-artifact-author/git-repo.png)

1. Ajoutez le dépôt d’artefacts au laboratoire : reportez-vous à l’article [Ajouter un dépôt d’artefacts Git à un laboratoire](devtest-lab-add-artifact-repo.md).

## Billets de blog connexes
- [How to troubleshoot failing Artifacts in AzureDevTestLabs](http://www.visualstudiogeeks.com/blog/DevOps/How-to-troubleshoot-failing-artifacts-in-AzureDevTestLabs) (Comment résoudre les problèmes d’échec des artefacts dans AzureDevTestLabs)
- [Join a VM to existing AD Domain using ARM template in Azure Dev Test Lab](http://www.visualstudiogeeks.com/blog/DevOps/Join-a-VM-to-existing-AD-domain-using-ARM-template-AzureDevTestLabs) (Joindre une machine virtuelle à un domaine Active Directory existant à l’aide du modèle ARM dans Azure Dev Test Lab)

## Étapes suivantes

- Découvrez comment [ajouter un dépôt d’artefacts Git à un laboratoire](devtest-lab-add-artifact-repo.md).

<!---HONumber=AcomDC_0803_2016-->