---
title: Utiliser le portail des services web Azure Machine Learning | Microsoft Docs
description: "Gestion de l&quot;accès aux espaces de travail Azure Machine Learning, et déploiement et gestion des services web d&quot;API ML"
services: machine-learning
documentationcenter: 
author: vDonGlover
manager: jhubbard
editor: cgronlun
ms.assetid: b62cf2ca-dd2a-4a83-bb54-469f948fb026
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/11/2017
ms.author: v-donglo
translationtype: Human Translation
ms.sourcegitcommit: 66fb3dc316ce25aea4dff4add5c25b7f0f56ad7a
ms.openlocfilehash: ef24162006c508134db3f385e791306495ac4614


---
# <a name="manage-a-web-service-using-the-azure-machine-learning-web-services-portal"></a>Gérer un service web à l’aide du portail des services web Azure Machine Learning
Vous pouvez gérer vos services web Machine Learning nouveaux et classiques à l’aide du portail des services web Microsoft Azure Machine Learning. Étant donné que les services web classiques et nouveaux sont basés sur des technologies différentes , les fonctionnalités de gestion diffèrent légèrement pour chacun d’eux.

Le portail des services web Azure Machine Learning vous permet d’effectuer les opérations suivantes :

* surveiller l’utilisation du service web ;
* configurer la description, mettre à jour les clés du service web (nouveau uniquement), mettre à jour votre clé de compte de stockage (nouveau uniquement), activer l’enregistrement (nouveau uniquement) et activer ou désactiver les exemples de données ;
* supprimer le service web ;
* créer, supprimer ou mettre à jour des plans de facturation (nouveau uniquement) ;
* ajouter et supprimer des points de terminaison (classique uniquement).

[!INCLUDE [machine-learning-free-trial](../../includes/machine-learning-free-trial.md)]

## <a name="manage-new-web-services"></a>Gérer de nouveaux services web
Pour gérer vos nouveaux service web :

1. Connectez-vous au portail [Services web Microsoft Azure Machine Learning](https://services.azureml.net/quickstart) à l’aide de votre compte Microsoft Azure (utilisez le compte associé à l’abonnement Azure).
2. Dans le menu, cliquez sur **Services Web**.

La liste des services web déployés pour votre abonnement s’affiche. 

Pour gérer un service web, cliquez sur Services web. À partir de la page Services Web, vous pouvez :

* Cliquer sur le service web pour le gérer.
* Cliquer sur le plan de facturation associé au service web pour le mettre à jour.
* Supprimer un service web.
* Copier un service web et le déployer dans une autre région.

Lorsque vous cliquez sur un service web, la page de démarrage rapide du service web s’ouvre. La page de démarrage rapide du service web comprend deux options de menu pour gérer votre service web :

* **TABLEAU DE BORD** : pour afficher l’utilisation du service web.
* **CONFIGURER** : pour ajouter un texte descriptif, mettre à jour la clé du compte de stockage associé au service web, et activer ou désactiver des exemples de données.

### <a name="monitoring-how-the-web-service-is-being-used"></a>Surveiller l’utilisation du service web
Cliquez sur l’onglet **TABLEAU DE BORD** .

Le tableau de bord vous permet de voir l’utilisation globale de votre service web sur une période donnée. Vous pouvez sélectionner la période à afficher dans le menu déroulant Période situé dans le coin supérieur droit des graphiques d’utilisation. Le tableau de bord affiche les informations suivantes :

* **Requests Over Time** (Requêtes sur une période donnée) affiche un graphique détaillé du nombre de requêtes au cours de la période sélectionnée. Ce graphique vous permet d’identifier les pics d’utilisation.
* **Request-Response Requests** (Demandes de requête-réponse) affiche le nombre total d’appels de requête-réponse que le service a reçu au cours de la période sélectionnée et combien d’entre eux ont échoué.
* **Average Request-Response Compute Time** (Temps de calcul moyen d’une requête-réponse) affiche le temps moyen nécessaire pour exécuter les demandes reçues.
* **Batch Requests** (Requêtes de lots) affiche le nombre total de requêtes de traitement par lots que le service a reçu au cours de la période sélectionnée et combien d’entre elles ont échoué.
* **Average Job Latency** (Latence de travail moyenne) affiche le temps moyen nécessaire pour exécuter les requêtes reçues.
* **Errors** (Erreurs) affiche le nombre agrégé d’erreurs qui se sont produites lors des appels au service web.
* **Services Costs** (Coûts de service) affiche les frais du plan de facturation associé au service.

### <a name="configuring-the-web-service"></a>Configuration du service web
Cliquez sur l’option de menu **CONFIGURER** .

Vous pouvez mettre à jour les propriétés suivantes :

* **Description** pour entrer une description du service web.
* **Titre** pour entrer le titre du service web.
* **Clés** pour configurer la rotation de vos clés API principales et secondaires.
* **Clé du compte de stockage** pour mettre à jour la clé du compte de stockage associé aux modifications du service web. 
* **Activer l’exemple de données** pour fournir des exemples de données que vous pouvez utiliser pour tester votre service de demande-réponse. Si vous avez créé le service web dans Machine Learning Studio, les exemples de données proviennent des données utilisées dans votre modèle. Si vous avez créé le service par programme, les données proviennent des exemples de données que vous avez fournis dans le cadre du package JSON.

### <a name="managing-billing-plans"></a>Gestion des plans de facturation
Cliquez sur l’option de menu **Plans** à partir de la page Démarrage rapide des services web. Vous pouvez également cliquer sur le plan associé au service web spécifique pour gérer ce plan.

* **Nouveau** vous permet de créer un nouveau plan.
* **Add/Remove Plan instance** (Ajouter/supprimer une instance de plan) vous permet d’augmenter la taille du plan existant pour ajouter de la capacité.
* **Upgrade/DownGrade** (Mettre à niveau/rétrograder) vous permet de mettre à l’échelle un plan existant pour ajouter de la capacité.
* **Supprimer** vous permet de supprimer un plan.

Cliquez sur un plan pour afficher son tableau de bord. Le tableau de bord affiche un instantané de l’utilisation du plan sur une période donnée. Pour sélectionner la période à afficher, cliquez sur le menu déroulant **Période** en haut à droite du tableau de bord. 

Le tableau de bord du plan affiche les informations suivantes :

* **Description du plan** : informations sur les coûts et la capacité associés au plan.
* **Plan Usage** (Utilisation du plan) : nombre de transactions et d’heures de calcul facturées dans le cadre du plan.
* **Services web** : nombre de services web qui utilisent ce plan.
* **Top web Service By Calls** (Principaux services web par appels) : quatre principaux services web qui effectuent des appels facturés dans le cadre du plan.
* **Top web Services by Compute Hrs** (Principaux services web par heures de calcul) : les quatre principaux services web qui utilisent des ressources de calcul facturées dans le cadre du plan.

## <a name="manage-classic-web-services"></a>Gérer des services web classiques
> [!NOTE]
> Les procédures décrites dans cette section ont trait à la gestion de services web classiques via le portail des services web Azure Machine Learning. Pour plus d’informations sur la gestion des services web classiques via Machine Learning Studio et le portail Azure Classic, voir [Gestion d’un espace de travail Azure Machine Learning](machine-learning-manage-workspace.md).
> 
> 

Pour gérer vos services web classiques :

1. Connectez-vous au portail [Services web Microsoft Azure Machine Learning](https://services.azureml.net/quickstart) à l’aide de votre compte Microsoft Azure (utilisez le compte associé à l’abonnement Azure).
2. Dans le menu, cliquez sur **Services web classiques**.

Pour gérer un Service web classique, cliquez sur **Services web classiques**. Dans la page Services web classiques, vous pouvez :

* cliquer sur un service web pour afficher les points de terminaison associés ;
* supprimer un service web.

Lorsque vous gérez un service web classique, vous gérer chacun des points de terminaison séparément. Lorsque vous cliquez sur un service web dans la page Services web, la liste des points de terminaison associés au service s’ouvre. 

Dans la page des points de terminaison du web classique, vous pouvez ajouter et supprimer des points de terminaison. Pour plus d’informations sur l’ajout de points de terminaison, consultez [Création de points de terminaison](machine-learning-create-endpoint.md).

Cliquez sur un des points de terminaison pour ouvrir la page de démarrage rapide du service web. Dans la page de démarrage rapide, deux options de menu permettent de gérer le service web :

* **TABLEAU DE BORD** : pour afficher l’utilisation du service web.
* **CONFIGURER** : pour ajouter un texte descriptif, activer et désactiver la journalisation des erreurs, mettre à jour la clé du compte de stockage associé au service web, et activer et désactiver des exemples de données.

### <a name="monitoring-how-the-web-service-is-being-used"></a>Surveiller l’utilisation du service web
Cliquez sur l’onglet **TABLEAU DE BORD** .

Le tableau de bord vous permet de voir l’utilisation globale de votre service web sur une période donnée. Vous pouvez sélectionner la période à afficher dans le menu déroulant Période situé dans le coin supérieur droit des graphiques d’utilisation. Le tableau de bord affiche les informations suivantes :

* **Requests Over Time** (Requêtes sur une période donnée) affiche un graphique détaillé du nombre de requêtes au cours de la période sélectionnée. Ce graphique vous permet d’identifier les pics d’utilisation.
* **Request-Response Requests** (Demandes de requête-réponse) affiche le nombre total d’appels de requête-réponse que le service a reçu au cours de la période sélectionnée et combien d’entre eux ont échoué.
* **Average Request-Response Compute Time** (Temps de calcul moyen d’une requête-réponse) affiche le temps moyen nécessaire pour exécuter les demandes reçues.
* **Batch Requests** (Requêtes de lots) affiche le nombre total de requêtes de traitement par lots que le service a reçu au cours de la période sélectionnée et combien d’entre elles ont échoué.
* **Average Job Latency** (Latence de travail moyenne) affiche le temps moyen nécessaire pour exécuter les requêtes reçues.
* **Errors** (Erreurs) affiche le nombre agrégé d’erreurs qui se sont produites lors des appels au service web.
* **Services Costs** (Coûts de service) affiche les frais du plan de facturation associé au service.

### <a name="configuring-the-web-service"></a>Configuration du service web
Cliquez sur l’option de menu **CONFIGURER** .

Vous pouvez mettre à jour les propriétés suivantes :

* **Description** pour entrer une description du service web. Le champ Description est obligatoire.
* **Journalisation** pour activer ou désactiver la journalisation des erreurs sur le point de terminaison. Pour plus d’informations sur la journalisation, voir [Activation de la journalisation pour les services web Machine Learning](machine-learning-web-services-logging.md).
* **Activer l’exemple de données** pour fournir des exemples de données que vous pouvez utiliser pour tester votre service de demande-réponse. Si vous avez créé le service web dans Machine Learning Studio, les exemples de données proviennent des données utilisées dans votre modèle. Si vous avez créé le service par programme, les données proviennent des exemples de données que vous avez fournis dans le cadre du package JSON.

## <a name="grant-or-suspend-access-to-web-services-for-users-in-the-portal"></a>Octroyer ou suspendre l’accès aux services web pour les utilisateurs dans le portail
Le portail Azure Classic vous permet d’autoriser ou de refuser l’accès à des utilisateurs spécifiques.

### <a name="access-for-users-of-new-web-services"></a>Accès des utilisateurs de nouveaux services web
Pour permettre à d’autres utilisateurs d’utiliser vos services web dans le portail des services web Azure Machine Learning, ajoutez-les en tant que coadministrateurs à votre abonnement Azure.

Connectez-vous au [portail Azure Classic](https://manage.windowsazure.com/) à l’aide de votre compte Microsoft Azure (utilisez le compte associé à l’abonnement Azure).

1. Dans le volet de navigation, cliquez sur **Paramètres**, puis sur **Administrateurs**.
2. En bas de la fenêtre, cliquez sur **Ajouter**. 
3. Dans la boîte de dialogue Ajouter un coadministrateur, entrez l’adresse de messagerie de la personne que vous souhaitez ajouter en tant que coadministrateur, puis sélectionnez l’abonnement auquel le coadministrateur doit avoir accès.
4. Cliquez sur **Save**.

### <a name="access-for-users-of-classic-web-services"></a>Accès des utilisateurs de services web classiques
Pour gérer un espace de travail :

Connectez-vous au [portail Azure Classic](https://manage.windowsazure.com/) à l’aide de votre compte Microsoft Azure (utilisez le compte associé à l’abonnement Azure).

1. Dans le volet des services Microsoft Azure, cliquez sur **MACHINE LEARNING**.
2. Cliquez sur l’espace de travail que vous souhaitez gérer.
3. Cliquez sur l’onglet **Configurer** .

Sous l’onglet Configuration, vous pouvez suspendre l’accès à l’espace de travail Machine Learning en cliquant sur **REFUSER**. Les utilisateurs ne seront plus en mesure d’ouvrir l’espace de travail de Machine Learning Studio. Pour restaurer l’accès, cliquez sur **AUTORISER**.

Utilisateurs spécifiques :

Pour gérer des comptes supplémentaires qui ont accès à l’espace de travail dans Machine Learning Studio, cliquez sur **Se connecter à ML Studio** sous l’onglet **TABLEAU DE BORD**. Cela ouvre l’espace de travail de Machine Learning Studio. À ce stade, cliquez sur l’onglet **PARAMÈTRES**, puis sur **UTILISATEURS**. Vous pouvez cliquer sur **INVITER PLUS D’UTILISATEURS** pour octroyer à des utilisateurs un accès à l’espace de travail, ou sélectionner un utilisateur et cliquer sur **SUPPRIMER**.

> [!NOTE]
> Le lien **Se connecter à ML Studio** ouvre Machine Learning Studio via le compte Microsoft auquel vous êtes actuellement connecté. Le compte Microsoft que vous utilisez pour vous connecter au portail Azure Classic afin de créer l’espace de travail n’est pas automatiquement autorisé à ouvrir cet espace de travail. Pour ouvrir un espace de travail, vous devez être connecté au compte Microsoft qui a été défini comme propriétaire de l’espace de travail ou recevoir une invitation du propriétaire à rejoindre l’espace de travail.
> 
> 




<!--HONumber=Jan17_HO5-->


