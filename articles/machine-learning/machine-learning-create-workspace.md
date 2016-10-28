<properties
	pageTitle="Création d'un espace de travail Machine Learning | Microsoft Azure"
	description="Création d’un espace de travail pour Microsoft Azure Machine Learning Studio."
	services="machine-learning"
	documentationCenter=""
	authors="garyericson"
	manager="jhubbard"
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/16/2016"
	ms.author="garye;bradsev;ahgyger"/>


# Créer et partager un espace de travail Azure Machine Learning

Ce menu pointe vers des rubriques qui décrivent comment configurer les différents environnements de science de données utilisés par le processus d’analyse Cortana (CAP).

[AZURE.INCLUDE [data-science-environment-setup](../../includes/cap-setup-environments.md)]

Pour utiliser Azure Machine Learning Studio, vous devez disposer d’un espace de travail Machine Learning. Cet espace de travail contient les outils dont vous avez besoin pour créer, gérer et publier des expériences.

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## Pour créer un espace de travail

1. Connectez-vous au [portail Microsoft Azure Classic].

> [AZURE.NOTE] Pour vous connecter, vous devez être un administrateur d’abonnement Azure. Le propriétaire d’un espace de travail Machine Learning ne vous donne pas accès au [portail Microsoft Azure Classic]. Consultez la page [Privileges of Azure subscription administrator and workspace owner (Privilèges de l’administrateur d’abonnement Azure et du propriétaire de l’espace de travail)](#subscriptionvsworkspace) pour plus de détails.

2. Dans le volet des services Microsoft Azure, cliquez sur **MACHINE LEARNING**.

    ![Service Machine Learning][1]

3. Cliquez sur **+NOUVEAU** en bas de la fenêtre.
4. Cliquez sur **SERVICES DE DONNÉES**, puis **APPRENTISSAGE AUTOMATIQUE**, puis **CRÉATION RAPIDE**.

	![Création rapide du nouvel espace de travail][3]

5. Entrez un **NOM D’ESPACE DE TRAVAIL** pour votre espace de travail.
6. Spécifiez l’**EMPLACEMENT** Azure, puis entrez un **COMPTE DE STOCKAGE** Azure existant ou sélectionnez **Créer un compte de stockage** pour en créer un.
7. Cliquez sur **CRÉER UN ESPACE DE TRAVAIL ML**.

Une fois votre espace de travail Machine Learning créé, vous voyez son nom apparaître sur la page **Machine Learning**.

## Partage d’un espace de travail Azure Machine Learning

Une fois l’espace de travail Machine Learning créé, vous pouvez y inviter les utilisateurs et partager son accès et toutes ses expériences. Nous prenons en charge les deux rôles d’utilisateurs suivants :

- **Utilisateur** : un utilisateur de l’espace de travail peut créer, ouvrir, modifier et supprimer des groupes de données, des expériences et des services web dans l’espace de travail.
- **Propriétaire** : un propriétaire peut inviter, supprimer et répertorier les utilisateurs ayant accès à l’espace de travail et les actions qu’ils sont autorisés à effectuer. Il a également accès aux ordinateurs blocs-notes.

### Pour partager un espace de travail
1. Connectez-vous à [Azure Machine Learning Studio]
2. Dans le panneau Machine Learning Studio, cliquez sur **PARAMÈTRES**
3. Cliquez sur **UTILISATEURS**.
4. Cliquez sur **INVITE MORE USERS** (INVITER PLUS D’UTILISATEURS)

    ![Inviter plus d’utilisateurs][4]

5. Entrez une ou plusieurs adresses e-mail. L’utilisateur a seulement besoin d’un compte Microsoft valide (par exemple, name@outlook.com) ou d’un compte professionnel (issu d’Azure Active Directory).
6. Cliquez sur le bouton représentant une coche.

Chaque utilisateur ajouté recevra un e-mail contenant des instructions de connexion à l’espace de travail partagé.

Pour plus d’informations sur la gestion de votre espace de travail, consultez [Gestion d’un espace de travail Azure Machine Learning]. En cas de problème lors de la création de votre espace de travail, consultez [Guide de résolution des problèmes : création et connexion à un espace de travail Machine Learning].

## <a name="subscriptionvsworkspace"></a>Privileges of Azure subscription administrator and workspace owner (Privilèges de l’administrateur d’abonnement Azure et du propriétaire de l’espace de travail)

Voici une table permettant de clarifier la différence entre un administrateur d’abonnement Azure et un propriétaire d’espace de travail.

| Actions | Administrateur d’abonnement Azure | Propriétaire de l'espace de travail |
| --------------			|:------------------------:| :----------------:|
| Accéder au [portail Microsoft Azure Classic]| Oui | Non |
| Créer un espace de travail | Oui | Non |
| Supprimer un espace de travail | Oui | Non |
| Ajouter un point de terminaison à un service web | Oui | Non |
| Supprimer un point de terminaison d’un service web | Oui | Non |
| Modifier l’accès concurrentiel pour un service web | Oui | Non |
| Accéder à [Machine Learning Studio] | Non * | Oui |


> [AZURE.NOTE] * Un administrateur d’abonnement Azure est automatiquement ajouté à l’espace de travail qu’il crée en tant que propriétaire de l’espace de travail. Toutefois, le simple fait d’être administrateur d’abonnement Azure ne lui accorde pas l’accès à tous les espaces de travail sous cet abonnement.

<!-- ![List of Machine Learning workspaces][2] -->

<!--Anchors-->
[To create a workspace]: #createworkspace

<!--Image references-->
[1]: media/machine-learning-create-workspace/cw1.png
[2]: media/machine-learning-create-workspace/cw2.png
[3]: media/machine-learning-create-workspace/cw4.png
[4]: media/machine-learning-create-workspace/cw5.png


<!--Link references-->
[Gestion d’un espace de travail Azure Machine Learning]: machine-learning-manage-workspace.md
[Guide de résolution des problèmes : création et connexion à un espace de travail Machine Learning]: machine-learning-troubleshooting-creating-ml-workspace.md
[Machine Learning Studio]: https://studio.azureml.net/
[Azure Machine Learning Studio]: https://studio.azureml.net/
[portail Microsoft Azure Classic]: https://manage.windowsazure.com/

<!---HONumber=AcomDC_0914_2016-->