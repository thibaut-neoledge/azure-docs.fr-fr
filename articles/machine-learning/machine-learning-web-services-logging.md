<properties 
    pageTitle="Journalisation pour les services web Machine Learning | Microsoft Azure" 
    description="Découvrez comment activer la journalisation pour les services Web de Machine Learning. La journalisation fournit des informations supplémentaires pour vous aider à résoudre les problèmes des API." 
    services="machine-learning" 
    documentationCenter="" 
    authors="raymondlaghaeian" 
    manager="jhubbard"
    editor="cgronlun"/>

<tags
    ms.service="machine-learning"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="big-data" 
    ms.date="10/05/2016"
    ms.author="raymondl;garye"/>


# <a name="enable-logging-for-machine-learning-web-services"></a>Activation de la journalisation pour les services Web de Machine Learning  

Ce document fournit des informations sur la fonctionnalité de journalisation des services web classiques. L’activation de la journalisation dans les services web fournit des informations supplémentaires, au-delà d’un numéro et d’un message d’erreur, qui peuvent vous aider à résoudre des problèmes liés à vos appels aux API Machine Learning.  

Pour activer la journalisation dans les Services web du portail Azure Classic :   

1.  Connectez-vous au [portail Azure Classic](https://manage.windowsazure.com/).
2.  Dans la colonne des fonctionnalités à gauche, cliquez sur **MACHINE LEARNING**.
3.  Cliquez votre espace de travail, puis sur **SERVICES WEB**.
4.  Dans la liste de services web, cliquez sur le nom du service web.
5.  Dans la liste des points de terminaison, cliquez sur le nom du point de terminaison.
6.  Cliquez sur **CONFIGURER**.
7.  Définissez **NIVEAU DE SUIVI DES DIAGNOSTICS** sur *Erreur* ou *Tous*, puis cliquez sur **ENREGISTRER**.

Pour activer la journalisation dans le portail des services web Azure Machine Learning

1. Connectez-vous au portail [services web Azure Machine Learning](https://services.azureml.net).
2. Cliquez sur Services web classiques.
3.  Dans la liste de services web, cliquez sur le nom du service web.
4.  Dans la liste des points de terminaison, cliquez sur le nom du point de terminaison.
5.  Cliquez sur **Configurer**.
6.  Définissez **Journalisation** sur *Erreur* ou *Tous*, puis cliquez sur **ENREGISTRER**.

## <a name="the-effects-of-enabling-logging"></a>Effets de l’activation de la journalisation

Quand la journalisation est activée, tous les diagnostics et les erreurs du point de terminaison sélectionné sont enregistrés dans le compte de stockage Azure lié à l’espace de travail de l’utilisateur. Vous pouvez voir ce compte de stockage dans la vue Tableau de bord du portail Azure Classic (en bas de la section Aperçu rapide) de son espace de travail.  

Les journaux peuvent être affichés à l’aide de plusieurs outils servant à « explorer » un compte de stockage Azure. La méthode la plus simple consiste à accéder au compte de stockage dans le portail Azure Classic, puis de cliquer sur l’onglet **CONTENEURS**. Vous verrez alors un conteneur nommé **ml-diagnostic**. Ce conteneur contient toutes les informations de diagnostic pour tous les points de terminaison de service web pour tous les espaces de travail associés à ce compte de stockage. 
 
## <a name="log-blob-detail-information"></a>Journaliser les informations détaillées sur l’objet blob

Chaque objet blob dans le conteneur conserve les informations de diagnostic pour une et une seule des opérations suivantes :

-   une exécution de la méthode de traitement par lot  
-   une exécution de la méthode de requête-réponse  
-   l’initialisation d'un conteneur de requête-réponse
  
Le nom de chaque objet blob a un préfixe sous la forme suivante : 

{Id d’espace de travail}-{Id de service web}-{Id de point de terminaison}/ {type de journal}  

Le type de journal est l’une des valeurs suivantes :  

- batch  
- score/requests  
- score/init  


<!--HONumber=Oct16_HO2-->


