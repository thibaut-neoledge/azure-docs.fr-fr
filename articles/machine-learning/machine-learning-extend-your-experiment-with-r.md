<properties
	pageTitle="Prolongez votre expérience avec R | Microsoft Azure"
	description="Comment étendre les fonctionnalités d'Azure Machine Learning Studio en utilisant le langage R et le module Exécuter le script R."
	services="machine-learning"
	documentationCenter=""
	authors="garyericson"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/02/2016"
	ms.author="garye" />


#Prolongez votre expérience avec R

Vous pouvez étendre les fonctionnalités de ML Studio grâce au langage R en utilisant le module [Exécuter le script R][execute-r-script].

Ce module accepte plusieurs jeux de données d'entrée et génère un jeu de données unique en sortie. Vous pouvez taper un script R dans le paramètre **Script R** du module [Exécuter le script R][execute-r-script].

Pour accéder à chaque port d'entrée du module, utilisez un code semblable au suivant :

    dataset1 <- maml.mapInputPort(1)

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

##Liste de tous les packages actuellement installés

La liste des packages installés peut changer. Pour obtenir la liste complète et à jour des packages installés, avec une description de chaque package, entrez le code suivant dans le module [Exécuter le Script R][execute-r-script] \:

    out <- data.frame(installed.packages(,,,fields="Description"))
	maml.mapOutputPort("out")

La liste des packages est envoyée au port de sortie du module [Exécuter le Script R][execute-r-script]. Pour afficher la liste des packages, connectez un module de conversion comme [Convertir en CSV][convert-to-csv] à la sortie gauche du module [Exécuter le script R][execute-r-script] puis exécutez le test. Cliquez ensuite sur la sortie du module de conversion et sélectionnez **Télécharger**.

![](./media/machine-learning-extend-your-experiment-with-r/download-package-list.png)


##Importation de packages

Vous pouvez également importer des packages qui ne sont pas déjà installés à partir d'un référentiel ML Studio intermédiaire à l'aide des commandes suivantes dans le module [Exécuter le script R][execute-r-script], ainsi que dans l'archive du package zippé :

    install.packages("src/my_favorite_package.zip", lib = ".", repos = NULL, verbose = TRUE)
    success <- library("my_favorite_package", lib.loc = ".", logical.return = TRUE, verbose = TRUE)

où `my_favorite_package.zip` contient le fichier zip de votre package.


<!-- Module References -->
[execute-r-script]: https://msdn.microsoft.com/library/azure/30806023-392b-42e0-94d6-6b775a6e0fd5/
[convert-to-csv]: https://msdn.microsoft.com/library/azure/faa6ba63-383c-4086-ba58-7abf26b85814/

<!---HONumber=AcomDC_0504_2016-->