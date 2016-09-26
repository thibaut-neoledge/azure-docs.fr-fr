<properties
	pageTitle="Utilisation d’un service web Machine Learning à partir de Microsoft Excel | Microsoft Azure."
	description="Utilisation d’un service web Microsoft Azure Machine Learning à partir de Microsoft Excel."
	services="machine-learning"
	documentationCenter=""
	authors="tedway"
	manager="jhubbard"
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/06/2016"
	ms.author="tedway"/>

# Utilisation d’un service web Microsoft Azure Machine Learning à partir de Microsoft Excel.

 Microsoft Azure Machine Learning Studio permet d’appeler facilement des services web directement à partir de Microsoft Excel sans qu’il soit nécessaire d’écrire du code.

Si vous utilisez Excel 2013 (ou une version ultérieure) ou Excel Online, nous vous recommandons d’utiliser la [macro complémentaire Excel](machine-learning-excel-add-in-for-web-services.md).

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## Étapes

Publiez un service web. [Cette page](machine-learning-walkthrough-5-publish-web-service.md) explique comment procéder. Actuellement, la fonctionnalité de classeur Excel est uniquement prise en charge pour les services de requête/réponse qui produisent une seule sortie (autrement dit, une étiquette de notation unique).

Quand vous disposez d’un service web, cliquez sur la section **WEB SERVICES** située sur la partie gauche de Microsoft Azure Machine Learning Studio, puis sélectionnez le service web à utiliser à partir de Microsoft Excel.

**Service web classique**

1. Sur l’onglet **TABLEAU DE BORD** du service web figure une ligne pour le service **REQUÊTE/RÉPONSE**. Si ce service produit une sortie unique, le lien **Télécharger un classeur Excel** doit apparaître sur cette ligne.

	![][1]

2. Cliquez sur **Télécharger un classeur Excel**.

**Nouveau service web**

1. Dans le portail Service web Azure Machine Learning, sélectionnez **Consommer**.
2. Dans la page Consommer, dans les **options de consommation de service Web** cliquez sur l’icône Excel.

**Utilisation du classeur**

1. Ouvrez le classeur.

2. Un avertissement de sécurité s’affiche. Cliquez sur le bouton **Activer la modification**.

	![][2]

3. Un avertissement de sécurité apparaît. Cliquez sur le bouton **Activer le contenu** pour pouvoir exécuter des macros sur votre feuille de calcul.

	![][3]
4. Une fois les macros activées, une table est générée. Les valeurs des colonnes en bleu sont requises en tant qu’entrées dans le service web RRS (Request/Response Service), ou en tant que **PARAMÈTRES**. Notez la sortie du service RRS, appelée **VALEURS PRÉDITES** et affichée en vert. Lorsque toutes les colonnes d’une ligne donnée sont remplies, le classeur appelle automatiquement l’API de notation et affiche les notes résultantes.

	![][4]

5. Pour noter plusieurs lignes, remplissez la deuxième ligne avec les données ; des valeurs prédites sont produites. Vous pouvez même coller plusieurs lignes à la fois.

Vous pouvez utiliser toutes les fonctionnalités de Microsoft Excel (graphiques, Power Map, mise en forme conditionnelle, etc.) avec les valeurs prédites pour visualiser les données.


## Partage de votre classeur

Pour que les macros fonctionnent, votre Clé API doit faire partie de la feuille de calcul. Cela signifie que vous devez uniquement partager le classeur avec des entités/personnes de confiance.

## Mises à jour automatiques

Un appel RRS est initié dans les deux cas suivants :

1. la première fois que du contenu apparaît dans l’ensemble des **PARAMÈTRES** d’une ligne ;

2. chaque fois que l’un ou l’autre des **PARAMÈTRES** change dans une ligne dont l’ensemble des **PARAMÈTRES** a été entré.

[1]: ./media/machine-learning-consuming-from-excel/excellink.png
[2]: ./media/machine-learning-consuming-from-excel/enableeditting.png
[3]: ./media/machine-learning-consuming-from-excel/enablecontent.png
[4]: ./media/machine-learning-consuming-from-excel/sampletable.png

<!---HONumber=AcomDC_0914_2016-->