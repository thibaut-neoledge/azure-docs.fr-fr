<properties
	pageTitle="Gestion d'un espace de travail Machine Learning | Microsoft Azure"
	description="Gestion de l'accès aux espaces de travail Azure Machine Learning, et déploiement et gestion des services web d'API ML"
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
	ms.date="07/06/2016"
	ms.author="garye"/>


# Gestion d'un espace de travail Azure Machine Learning

>[AZURE.NOTE] Les procédures décrites dans cet article s’appliquent aux services web Azure Machine Learning classiques. Pour plus d’informations sur la gestion de nouveaux services web, consultez [Gestion d’un nouveau service web Machine Learning](machine-learning-manage-new-webservice.md).

À l’aide du portail Azure Classic, vous pouvez gérer vos espaces de travail Machine Learning pour :

- surveiller l’utilisation de l’espace de travail
- configurer l’espace de travail pour autoriser ou refuser l’accès
- gérer les services web créés dans l’espace de travail
- supprimer l’espace de travail

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

De plus, l’onglet du tableau de bord affiche l’utilisation de l’espace de travail, ainsi que des informations sur ce dernier en un clin d’œil.

> [AZURE.TIP] Dans Azure Machine Learning Studio, dans l’onglet **SERVICES WEB**, vous pouvez ajouter, mettre à jour ou supprimer un service Web Machine Learning.

Pour gérer un espace de travail :

1.	Connectez-vous au [portail Azure Classic](https://manage.windowsazure.com/) au moyen de votre compte Microsoft Azure : utilisez le compte associé à l’abonnement Azure.
2.	Dans le volet des services Microsoft Azure, cliquez sur **MACHINE LEARNING**.
3.	Cliquez sur l’espace de travail que vous souhaitez gérer.

La page de l’espace de travail comporte trois onglets :

- **TABLEAU DE BORD** : vous permet d’afficher l’utilisation et les informations de l’espace de travail
- **CONFIGURATION** : vous permet de gérer l’accès à l’espace de travail
- **SERVICES WEB** : vous permet de gérer les services web publiés à partir de cet espace de travail


## Pour surveiller l’utilisation de l’espace de travail

Cliquez sur l’onglet **TABLEAU DE BORD**.

Dans le tableau de bord, vous pouvez afficher l’utilisation globale de votre espace de travail et obtenir un aperçu rapide des informations de l’espace de travail.

- Le graphique **CALCUL** affiche les ressources de calcul utilisées par l’espace de travail. Vous pouvez modifier l’affichage pour afficher les valeurs absolues ou relatives et vous pouvez modifier la plage de temps affichée dans le graphique.
- La **Présentation de l’utilisation** affiche le stockage Azure utilisé par l’espace de travail.
- L’**Aperçu rapide** fournit un résumé des informations de l’espace de travail ainsi que des liens utiles.

> [AZURE.NOTE] Le lien **Se connecter à ML Studio** ouvre Machine Learning Studio via le compte Microsoft auquel vous êtes actuellement connecté. Le compte Microsoft que vous utilisez pour vous connecter au portail Azure Classic afin de créer l’espace de travail n’est pas automatiquement autorisé à ouvrir cet espace de travail. Pour ouvrir un espace de travail, vous devez être connecté au compte Microsoft qui a été défini comme propriétaire de l’espace de travail ou recevoir une invitation du propriétaire à rejoindre l’espace de travail.


## Pour octroyer ou suspendre un accès pour les utilisateurs ##

Cliquez sur l'onglet **Configurer**.

Dans l’onglet Configuration, vous pouvez

- suspendre l’accès à l’espace de travail Machine Learning en cliquant sur REFUSER. Les utilisateurs ne seront plus en mesure d’ouvrir l’espace de travail de Machine Learning Studio. Pour restaurer l’accès, cliquez sur AUTORISER.

Pour gérer des comptes supplémentaires qui ont accès à l’espace de travail dans Machine Learning Studio, cliquez sur **Se connecter à ML Studio** sous l’onglet **TABLEAU DE BORD** (voir la remarque ci-dessus concernant **Se connecter à ML Studio**). Cela ouvre l’espace de travail de Machine Learning Studio. À ce stade, cliquez sur l’onglet **PARAMÈTRES** puis **UTILISATEURS**. Vous pouvez cliquer sur **INVITER PLUS D’UTILISATEURS** pour octroyer à des utilisateurs un accès à l’espace de travail, ou sélectionner un utilisateur et cliquer sur **SUPPRIMER**.


## Pour gérer des services Web dans cet espace de travail

Cliquez sur l’onglet **SERVICES WEB**.

Cela affiche une liste des services Web publiés à partir de cet espace de travail. Pour gérer un service Web, cliquez sur le nom dans la liste pour ouvrir la page du service Web.

Un service Web peut avoir un ou plusieurs points de terminaison.

- Vous pouvez définir des points de terminaison supplémentaires, en plus du point de terminaison « Par défaut ». Pour ajouter le point de terminaison, cliquez sur **AJOUTER UN POINT DE TERMINAISON** en bas de la page.

- Pour supprimer un point de terminaison (vous ne pouvez pas supprimer le point de terminaison « Par défaut »), cliquez n’importe où sur la ligne du point de terminaison, sauf sur le nom, puis cliquez sur **SUPPRIMER LE POINT DE TERMINAISON** en bas de la page. Cela supprime le point de terminaison du service Web.

    > [AZURE.NOTE] Si une application utilise le point de terminaison de service Web lors de la suppression de ce dernier, elle obtiendra une erreur la prochaine fois qu’elle essaiera d’accéder au service.

Cliquez sur le nom d’un point de terminaison de service Web pour l’ouvrir. Le graphique d’utilisation affiche les ressources de calcul et de prédiction utilisées par le point de terminaison de service Web. Vous pouvez modifier l’affichage pour afficher les valeurs absolues ou relatives et vous pouvez modifier la plage de temps affichée dans le graphique.

Cette page vous donne les informations nécessaires pour accéder au point de terminaison à l’aide de l’API REST du service Web. Pour plus d’informations, consultez [Utilisation d’un service Web Microsoft Azure Machine Learning][consume].

Vous pouvez également publier le service web sur le Marketplace de données Azure à partir de cette page. Pour plus d’informations, consultez [Publication du service Web Azure Machine Learning sur Azure Marketplace][marketplace].

Cliquez sur l’onglet **CONFIGURER** pour modifier la description, contrôler le nombre de connexions simultanées autorisées par le service web ou configurer un suivi des diagnostics.

[consume]: machine-learning-consume-web-services.md
[marketplace]: machine-learning-publish-web-service-to-azure-marketplace.md

<!---HONumber=AcomDC_0914_2016-->