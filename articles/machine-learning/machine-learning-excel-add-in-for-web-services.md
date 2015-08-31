<properties
	pageTitle="Complément Excel pour les services web Machine Learning | Microsoft Azure"
	description="Comment accéder aux services web de formation de Machine Azure directement dans Excel sans écrire de code."
	services="machine-learning"
	documentationCenter=""
	authors="tedway"
	manager="paulettm"
	editor="cgronlun"
	tags=""/>

<tags
	ms.service="machine-learning"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="data-services"
	ms.date="08/10/2015"
	ms.author="tedway;garye"/>

# Complément Excel de services web Azure Machine Learning

Excel permet d'appeler facilement des services web directement sans qu'il soit nécessaire d'écrire du code.

## Étapes pour utiliser un fichier de service web existant

1. Ouvrez l'exemple de fichier Excel ici ou dans l'onglet services web **tableau de bord**.
2. Choisissez un service web en cliquant dessus-« Titanic survivant Predictor (exemple de complément Excel) [Score] » dans cet exemple.

    ![Sélectionner un site Web][01]

3. Vous accéderez alors à la section **Prédire**. Sélectionnez une cellule dans Excel, puis cliquez sur **Utiliser les exemples de données**. Vous pouvez également modifier les données dans Excel.

	![Section Prédire][02]

4. Mettez en surbrillance les données et cliquez sur l'icône de plage de données d'entrée.
5. Sous **Sortie**, saisissez le numéro de la cellule dans laquelle vous souhaitez placer le résultat.
6. Cliquez sur **Prédire**.

## Opérations pour ajouter un nouveau Service Web

1. Publier un service web ([cette page](machine-learning-walkthrough-5-publish-web-service.md) explique comment le faire) ou rechercher un service web existant.
2. Lorsque vous disposez d'un service web, cliquez sur la section **WEB SERVICES** située sur le volet de gauche de Machine Learning Studio, puis sélectionnez le service web à utiliser.
3. À partir de l‘onglet **tableau de bord** pour le service web, copiez la clé d'API pour le service web.
4. Dans Excel, accédez à la section **Services web** (si vous vous trouvez dans la section **Prédire**, cliquez sur la flèche Précédent pour accéder à la liste des services web).

	![Section de services web][03]

5. Cliquez sur un **Ajouter un service web**.
6. Collez la clé dans la zone de texte intitulée **clé API**.
7. Sur l'onglet **tableau de bord** pour le service web, cliquez sur le lien **page d'aide de l'API**.
8. Copiez l'URI de requête sous **Demande** et la coller dans la zone de texte intitulée **URL**.
9. Cliquez sur **Add**.

	![URL et clé d'API][04]

10.	Pour utiliser le service web, suivez les instructions ci-dessus, « Étapes pour utiliser un Service web existant ».

## Partage de votre classeur

Si vous enregistrez votre classeur, les clés de l'API des services web que vous avez ajoutés seront également enregistrés. Cela signifie que vous devez uniquement partager le classeur avec des personnes de confiance.


[01]: ./media/machine-learning-excel-add-in-for-web-services/image1.png
[02]: ./media/machine-learning-excel-add-in-for-web-services/image2.png
[03]: ./media/machine-learning-excel-add-in-for-web-services/image3.png
[04]: ./media/machine-learning-excel-add-in-for-web-services/image4.png

<!---HONumber=August15_HO8-->