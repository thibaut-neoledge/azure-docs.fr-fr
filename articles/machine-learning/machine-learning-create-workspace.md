<properties 
	pageTitle="Création d'un espace de travail Machine Learning | Microsoft Azure" 
	description="Création d’un espace de travail pour Microsoft Azure Machine Learning Studio." 
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
	ms.date="03/09/2016" 
	ms.author="garye;bradsev"/>


# Création d’un espace de travail Microsoft Azure Machine Learning 

Ce menu pointe vers des rubriques qui décrivent comment configurer les différents environnements de science de données utilisés par le processus d’analyse Cortana (CAP).

[AZURE.INCLUDE [data-science-environment-setup](../../includes/cap-setup-environments.md)]

Pour utiliser Azure Machine Learning Studio, vous devez disposer d’un espace de travail Machine Learning. Cet espace de travail contient les outils dont vous avez besoin pour créer, gérer et publier des expériences.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## Pour créer un espace de travail

1. Connectez-vous au [portail Microsoft Azure Classic](https://manage.windowsazure.com/).
2. Dans le volet des services Microsoft Azure, cliquez sur **MACHINE LEARNING**.

    ![Service Machine Learning][1]

3. Cliquez sur **+NOUVEAU** en bas de la fenêtre.
4. Cliquez sur **SERVICES DE DONNÉES**, puis **APPRENTISSAGE AUTOMATIQUE**, puis **CRÉATION RAPIDE**.

	![Création rapide du nouvel espace de travail][3]

5. Entrez un **NOM DE L’ESPACE DE TRAVAIL** pour votre espace de travail et spécifiez le **PROPRIÉTAIRE DE L’ESPACE DE TRAVAIL**. Le propriétaire de l’espace de travail doit être un compte Microsoft valide (par exemple, name@outlook.com).)

    > [AZURE.NOTE] Ensuite, vous pouvez partager les expériences sur lesquelles vous travaillez en invitant d’autres personnes dans votre espace de travail. Pour cela, dans Machine Learning Studio, ouvrez la page **PARAMÈTRES**. Vous avez simplement besoin du compte Microsoft ou du compte professionnel de chaque utilisateur.

6. Spécifiez l’**EMPLACEMENT** Azure, puis entrez un **COMPTE DE STOCKAGE** Azure existant ou sélectionnez **Créer un compte de stockage** pour en créer un.
7. Cliquez sur **CRÉER UN ESPACE DE TRAVAIL ML**.

Une fois votre espace de travail Machine Learning créé, vous voyez son nom apparaître sur la page **Machine Learning**.

Pour plus d’informations sur la gestion de votre espace de travail, consultez [Gestion d’un espace de travail Azure Machine Learning]. En cas de problème lors de la création de votre espace de travail, consultez [Guide de résolution des problèmes : création et connexion à un espace de travail Machine Learning].

[Gestion d’un espace de travail Azure Machine Learning]: machine-learning-manage-workspace.md
[Guide de résolution des problèmes : création et connexion à un espace de travail Machine Learning]: machine-learning-troubleshooting-creating-ml-workspace.md
 
<!-- ![List of Machine Learning workspaces][2] -->

<!--Anchors-->
[To create a workspace]: #createworkspace

<!--Image references-->
[1]: media/machine-learning-create-workspace/cw1.png
[2]: media/machine-learning-create-workspace/cw2.png
[3]: media/machine-learning-create-workspace/cw3.png



<!--Link references-->
 

<!---HONumber=AcomDC_0316_2016-->