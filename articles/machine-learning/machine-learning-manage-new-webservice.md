<properties
	pageTitle="Gérer un nouveau service web Machine Learning | Microsoft Azure"
	description="Gestion de l'accès aux espaces de travail Azure Machine Learning, et déploiement et gestion des services web d'API ML"
	services="machine-learning"
	documentationCenter=""
	authors="vDonGlover"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="machine-learning"
	ms.workload="data-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/06/2016"
	ms.author="v-donglo"/>


# Gérer un nouveau service web Machine Learning

> [AZURE.NOTE] Les procédures décrites dans cet article s’appliquent aux nouveaux services web Azure Machine Learning. Pour obtenir des informations sur la gestion des services web classiques, voir [Gestion d’un espace de travail Azure Machine Learning](machine-learning-manage-workspace.md).

Le portail de services web Microsoft Azure Machine Learning vous permet de gérer vos nouveaux services web Machine Learning. Vous pouvez :

- surveiller l’utilisation du service web
- configurer la description, mettre à jour les clés pour le service web, mettre à jour votre clé de compte de stockage et activer ou désactiver les exemples de données
- supprimer le service web
- créer, supprimer ou mettre à jour des plans de facturation

[AZURE.INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

> [AZURE.TIP] Dans Azure Machine Learning Studio, dans l’onglet **SERVICES WEB**, vous pouvez ajouter, mettre à jour ou supprimer un service Web Machine Learning.

Pour gérer vos services web :

1.	Connectez-vous au [portail de services web Microsoft Azure Machine Learning](https://services.azureml.net/quickstart) à l’aide de votre compte Microsoft Azure : utilisez le compte associé à l’abonnement Azure.
2.	Dans le menu, cliquez sur **Services Web**.

Une liste des services web déployés pour votre abonnement s’affiche. Pour gérer un service Web, cliquez sur le nom dans la liste pour ouvrir la page du service Web.

À partir de la page Services Web, vous pouvez :

- Cliquer sur le service web pour le gérer.
- Cliquer sur le plan de facturation associé au service web pour le mettre à jour.
- Supprimer un service web.
- Copier un service web vers une autre région.
- Copier un service web et le déployer dans une autre région.

Lorsque vous cliquez sur un service web, la page Démarrage rapide du service web s’ouvre. La page Démarrage rapide du service web comprend deux options de menu pour gérer votre service web :

- **TABLEAU DE BORD** : pour afficher l’utilisation du service web.
- **CONFIGURER** : pour ajouter un texte descriptif, activer et désactiver la journalisation des erreurs, mettre à jour la clé du compte de stockage associé au service web, et activer et désactiver des exemples de données.

## Surveiller l’utilisation du service web

Cliquez sur l’onglet **TABLEAU DE BORD**.

Depuis le tableau de bord, vous pouvez afficher l’utilisation globale de votre service web sur une période donnée. Vous pouvez sélectionner la période à afficher dans le menu déroulant Période situé dans le coin supérieur droit des graphiques d’utilisation. Le tableau de bord affiche les informations suivantes :

- **Requests Over Time** (Requêtes sur une période donnée) affiche un graphique détaillé du nombre de requêtes au cours de la période sélectionnée. Ce graphique vous permet d’identifier les pics d’utilisation.
- **Request-Response Requests** (Demandes de requête-réponse) affiche le nombre total d’appels de requête-réponse que le service a reçu au cours de la période sélectionnée et combien d’entre eux ont échoué.
- **Average Request-Response Compute Time** (Temps de calcul moyen d’une requête-réponse) affiche le temps moyen nécessaire pour exécuter les demandes reçues.
- **Batch Requests** (Requêtes de lots) affiche le nombre total de requêtes de traitement par lots que le service a reçu au cours de la période sélectionnée et combien d’entre elles ont échoué.
- **Average Job Latency** (Latence de travail moyenne) affiche le temps moyen nécessaire pour exécuter les requêtes reçues.
- **Errors** (Erreurs) affiche le nombre agrégé d’erreurs qui se sont produites lors des appels vers le service web.
- **Services Costs** (Coûts de service) affiche les frais du plan de facturation associé au service.

## Configuration du service web ##

Cliquez sur l’option de menu **CONFIGURER**.

Vous pouvez mettre à jour les propriétés suivantes :

* Dans **Description**, saisissez une description du service web.
* Dans **Titre**, saisissez le titre du service web.
* Dans **Clés**, configurez la rotation de vos clés API principales et secondaires.
* Dans **Clé du compte de stockage**, mettez à jour la clé du compte de stockage associé aux modifications du service web.
* **Activer l’exemple de données** vous permet de fournir des exemples de données que vous pouvez utiliser pour tester votre service de requête-réponse. Si vous avez créé le service web dans Machine Learning Studio, les exemples de données proviennent des données utilisées dans votre modèle. Si vous avez créé le service par programme, les données proviennent des exemples de données que vous avez fournis dans le cadre du package JSON.

## Gestion des plans de facturation

Cliquez sur l’option de menu **Plans** à partir de la page Démarrage rapide des services web. Vous pouvez également cliquer sur le plan associé au service web spécifique pour gérer ce plan.

* **Nouveau** vous permet de créer un nouveau plan.
* **Add/Remove Plan instance** (Ajouter/supprimer une instance de plan) vous permet d’augmenter la taille du plan existant pour ajouter de la capacité.
* **Upgrade/DownGrade** (Mettre à niveau/rétrograder) vous permet de mettre à l’échelle un plan existant pour ajouter de la capacité.
* **Supprimer** vous permet de supprimer un plan.

Cliquez sur un plan pour afficher son tableau de bord. Le tableau de bord affiche un instantané de l’utilisation du plan sur une période donnée. Cliquez sur le menu déroulant **Période** en haut à droite des informations du tableau de bord pour sélectionner la période souhaitée.

Le tableau de bord du plan affiche les informations suivantes :

* **Description du plan** : informations sur les coûts et la capacité associés au plan.
* **Plan Usage** (Utilisation du plan) : nombre de transactions et d’heures de calcul facturées dans le cadre du plan.
* **Services Web** : nombre de services web qui utilisent ce plan.
* **Top Web Service By Calls** (Principaux services web par appels) : les quatre principaux services web qui effectuent des appels facturés dans le cadre du plan.
* **Top Web Services by Compute Hrs** (Principaux services web par heures de calcul) : les quatre principaux services web qui utilisent des ressources de calcul facturées dans le cadre du plan.

<!---HONumber=AcomDC_0720_2016-->