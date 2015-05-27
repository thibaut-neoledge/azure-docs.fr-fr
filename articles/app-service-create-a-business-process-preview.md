<properties 
	pageTitle="Créer un processus d'entreprise" 
	description="Créer un processus d'entreprise" 
	authors="stepsic-microsoft-com" 
	manager="dwrede" 
	editor="" 
	services="app-service-logic" 
	documentationCenter=""/>



<tags
	ms.service="app-service-logic"
	ms.workload="web"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/24/2015"
	ms.author="stepsic"/>

# Développement d'un processus d'entreprise à l'aide d'applications de flux et API

Vous avez créé un flux de base, mais vous souhaitez maintenant prendre des mesures concrètes et créer un processus d'entreprise à l'aide d'applications de flux. Je couvrirai plusieurs sujets clés dans cet article :

- Ajout de logique conditionnelle (par exemple, exécuter uniquement une action si une certaine condition est remplie) et de boucles
- Applications API BizTalk
- Utilisation du mode Code pour modifier un flux
- Différents types de déclencheurs

Pour commencer, cliquez ici pour créer un élément dans votre abonnement avec un flux de base déjà rempli. Une fois que vous avez créé ce flux, cliquez sur les déclencheurs et les actions pour le modifier.

## Ajout de logique conditionnelle et de boucles

Bien que le flux de base fonctionne correctement, vous avez peut-être remarqué quelques problèmes. Tout d'abord, il ne vous enverra que le premier tweet, et non tous les tweets sur le mot clé. Afin de répéter une action pour une liste d'éléments, vous pouvez utiliser la propriété « repeat ».

### Répétition

Repeat prend une liste d'éléments et exécute l'action pour chaque élément de la liste. Pour utiliser la répétition, cliquez sur l'icône de flèche circulaire en haut de l'action. Vous ouvrez ainsi une zone de texte et tapez :

    @triggers().outputs.body

Comme avec le flux de base, cela génère une liste de tweets. Ensuite, pour réellement utiliser le tweet à l'intérieur de l'action, remplacez le message par :

    @repeatItem().message

« repeatItem() » est une fonction qui vous donne chaque élément dans la liste. Cliquez sur la coche.

### Logique conditionnelle

Maintenant, ce flux peut toujours être gênant, car il génère un grand nombre de messages. Vous pouvez ajouter une logique supplémentaire afin de ne recevoir qu'un message électronique quand la personne qui crée le tweet a un certain nombre d'abonnés. Pour ce faire, cliquez sur l'icône en forme de losange en haut de l'action.

Dans la zone de texte, tapez :

    @greaterThan(repeatItem().person.followers, 1000)

La fonction repeatItem() s'affiche, ainsi qu'une nouvelle fonction appelée greaterThan(). Cette fonction compare deux valeurs et n'autorise l'exécution de l'action que si la première valeur est supérieure à la seconde. Notez la syntaxe pour obtenir les abonnés : pour chaque propriété qui vous intéresse, ajoutez un point suivi du nom de la propriété. Cliquez sur la coche pour enregistrer la condition.

## Applications API BizTalk

Il existe différentes catégories d'applications API disponibles dans Azure. Twitter et Office 365 sont des connecteurs appropriés à ce scénario, mais BizTalk fournit également d'autres applications API dont vous pouvez tirer parti dans votre flux.

Cliquez sur le service XXXXXX dans le volet de droite. Vous ajoutez ainsi ce service, fourni par BizTalk au concepteur.

### Passage de données entre des actions

Pour utiliser plusieurs actions dans un flux, vous devez passer des données entre les actions. Pour ce faire, utilisez la fonction actions().

Une fois que vous avez ajouté l'action XXXXXX, tapez :

    @actions(‘SendMail’).outputs.YYYYYY

La propriété YYYYYY est ainsi transférée à partir des sorties de l'action SendMail. Vous pouvez toujours voir le nom d'une action en regardant juste en dessous de l'icône de l'action. De même, vous pouvez voir toutes les sorties de l'action en bas de la zone.

## Utilisation du mode Code pour modifier un flux

Outre le concepteur, vous pouvez modifier directement la définition d'un flux à la main. Pour ce faire, cliquez sur le bouton Mode Code dans la barre de commandes. Vous ouvrez ainsi un éditeur complet qui vous montre la définition du flux que vous venez de modifier.

Un éditeur de texte peut faciliter certaines opérations, par exemple vous pouvez copier et coller n'importe quel nombre d'actions au sein du même flux (ou même entre les flux). Vous pouvez aussi ajouter ou supprimer facilement des sections entières dans le flux en même temps.

### Paramètres

Il existe également des fonctions qui sont exposées uniquement en mode Code, par exemple les paramètres. Les paramètres facilitent la réutilisation des valeurs dans un flux. Par exemple, si vous avez une adresse de messagerie que vous souhaitez inclure dans plusieurs actions, vous pouvez en faire un paramètre.

Pour définir les paramètres, accédez à l’objet parameters {} et ajoutez ce qui suit à l’intérieur : « emailAddress »: {« type » : « chaîne », « defaultValue » : « me@example.com »}

Accédez à présent à l'action d'envoi d'un message électronique. Vous y voyez l'entrée de l'adresse de messagerie, que vous remplacez par :

    @parameters(‘emailAddress’)

Les paramètres constituent un bon moyen d'extraire des valeurs que vous êtes susceptible de modifier souvent.

## Différents types de déclencheurs

Il existe différentes façons de démarrer les flux et, comme chacune a un comportement légèrement différent, veillez à sélectionner celle qui est adaptée à vos scénarios.

### Déclencheurs d'interrogation

Certains déclencheurs vérifient une application API selon un intervalle. Vous pouvez définir cet intervalle, mais vous devez également transmettre un champ appelé « triggerState ». L'état du déclencheur est constitué d'informations à transmettre à l'appel du déclencheur pour qu'il sache ce qu'il a retourné lors de la dernière vérification. L'état du déclencheur est généralement la chaîne :

    @triggers().outputs.body.triggerState

### Déclencheurs de rappel

D'autres déclencheurs rappellent le flux quand ils ont une nouvelle demande. Dans ce cas, vous devez attribuer au déclencheur l'URI de rappel de votre flux. Pour l'obtenir, copiez le point de terminaison d'accès du panneau Propriétés à partir de l'écran Paramètres quand vous accédez à un flux.

### Appel manuel

Troisièmement, vous pouvez avoir un flux que vous exécutez manuellement. Dans le portail, il existe un bouton Exécuter sur lequel vous pouvez cliquer pour lancer un flux.

<!--HONumber=54-->