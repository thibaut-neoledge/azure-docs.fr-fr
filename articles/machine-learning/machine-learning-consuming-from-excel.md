<properties 
	pageTitle="Utilisation d'un service web Microsoft Azure Machine Learning à partir de Microsoft Excel | Azure" 
	description="Utilisation d'un service web Microsoft Azure Machine Learning à partir de Microsoft Excel." 
	services="machine-learning" 
	documentationCenter="" 
	authors="LuisCabrer" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/18/2015" 
	ms.author="luisca"/>


# Utilisation d'un service web Microsoft Azure Machine Learning à partir de Microsoft Excel. #

 Microsoft Azure Machine Learning Studio facilite l'utilisation des services web de notation directement à partir de Microsoft Excel, sans qu'il soit nécessaire d'écrire du code. 

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)] 

## Étapes

1. Publiez un service web. [Cette page](machine-learning-walkthrough-5-publish-web-service.md) explique comment procéder. Actuellement, la fonctionnalité de classeur Microsoft Excel est uniquement prise en charge pour les services de requête/réponse qui produisent une seule sortie (autrement dit, une étiquette de notation unique). 
 
2. Lorsque vous disposez d'un service web, cliquez sur la section **SERVICES WEB** située sur la partie gauche de Microsoft Azure Machine Learning Studio, puis sélectionnez le service web à utiliser à partir de Microsoft Excel. 

3. Sur l'onglet **TABLEAU DE BORD** du service web figure une ligne pour le service **REQUÊTE/RÉPONSE**. Si ce service produit une sortie unique, le lien **Télécharger un classeur Excel** doit apparaître sur cette ligne.

	![][1]
 
4. Cliquez sur **Télécharger un classeur Excel**, puis ouvrez l'élément dans Microsoft Excel.

5. Un avertissement de sécurité s'affiche. Cliquez sur le bouton **Activer la modification**.

	![][2]

6. Un avertissement de sécurité apparaît. Cliquez sur le bouton **Activer le contenu** pour pouvoir exécuter des macros sur votre feuille de calcul.

	![][3] 
 
7. Une fois les macros activées, une table est générée. Les valeurs des colonnes en bleu sont requises en tant qu'entrées dans le service web RRS (Request/Response Service), ou en tant que **PARAMÈTRES**. Notez la sortie du service RRS, appelée **VALEURS PRÉDITES** et affichée en vert. Lorsque toutes les colonnes d'une ligne donnée sont remplies, le classeur appelle automatiquement l'API de notation et affiche les notes résultantes. 

	![][4]

7. Pour noter plusieurs lignes, remplissez la deuxième ligne avec les données ; des valeurs prédites sont produites. Vous pouvez même coller plusieurs lignes à la fois.

8. Vous pouvez à présent utiliser toutes les fonctionnalités de Microsoft Excel (graphiques, Power Map, mise en forme conditionnelle, etc.) avec les valeurs prédites !    


## Partage de votre classeur

Pour que les macros fonctionnent, votre CLÉ D'ACCÈS doit faire partie de la feuille de calcul. Cela signifie que vous devez uniquement partager le classeur avec des entités/personnes de confiance.

## Mises à jour automatiques

Un appel RRS est initié dans les deux cas suivants :

1. la première fois que du contenu apparaît dans l'ensemble des **PARAMÈTRES** d'une ligne ;
    
2. chaque fois que l'un ou l'autre des **PARAMÈTRES** change dans une ligne dont l'ensemble des **PARAMÈTRES** a été saisi.

[1]: ./media/machine-learning-consuming-from-excel/excellink.png
[2]: ./media/machine-learning-consuming-from-excel/enableeditting.png
[3]: ./media/machine-learning-consuming-from-excel/enablecontent.png
[4]: ./media/machine-learning-consuming-from-excel/sampletable.png

<!--HONumber=49--> 