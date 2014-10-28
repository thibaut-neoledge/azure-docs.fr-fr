<properties title="Publishing Azure ML Web Services to the Azure Marketplace" pageTitle="Publishing Azure ML Web Services to the Azure Marketplace | Azure" description="Publishing Azure ML Web Services to the Azure Marketplace" metaKeywords="" services="machine-learning" solutions="" documentationCenter="" authors="garye" videoId="" scriptId="" />

<tags ms.service="machine-learning" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="garye"></tags>

# Publication des services web Azure ML sur Azure Marketplace

Dans ce document :

-   [Introduction][Introduction]
-   [Présentation du processus de publication][Présentation du processus de publication]
-   [Consignes pour la publication sur Azure Marketplace][Consignes pour la publication sur Azure Marketplace]
-   [Options spécifiques à l'apprentissage automatique][Options spécifiques à l'apprentissage automatique]

<!--Anchors-->

## Introduction

Azure Marketplace offre la possibilité de publier des services web Azure Machine Learning sous forme de services payants ou gratuits que des clients externes pourront utiliser. Ce document propose une présentation du processus, avec des liens vers les consignes pour vous aider à démarrer. À l'aide de ce processus, vous pouvez mettre vos services web à disposition d'autres développeurs, qui pourront les utiliser dans leurs applications.

## Présentation du processus de publication

Les étapes suivantes illustrent la publication d'un service web Azure ML sur Azure Marketplace :

1.  Créez et publiez un service de requête-réponse (RRS, Request-Response Service) ou un service web d'exécution de lots (BES, Batch Execution Service) Azure ML.
2.  Depuis le portail de gestion Azure, déployez le service en production.
3.  Utilisez l'URL du service web à publier sur Azure Marketplace.
4.  Présentation du processus de publication : <http://msdn.microsoft.com/fr-fr/library/azure/hh580725.aspx>
5.  Une fois soumise, votre offre est évaluée et doit être approuvée avant que vos clients puissent commencer à l'acheter. Le processus de publication peut prendre quelques jours ouvrés. Nous tentons de le réduire au maximum et nous proposerons une mise à jour dans nos prochaines communications.

## Consignes pour la publication sur Azure Marketplace

1.  Vous devez vous enregistrer en tant qu'éditeur. Pour plus d'informations, consultez la page suivante : <http://msdn.microsoft.com/fr-fr/library/azure/hh563872.aspx>
2.  Vous devez indiquer des informations sur votre offre, y compris un plan de tarification. Vous pouvez choisir de proposer un service payant ou gratuit. Pour plus d'informations, consultez la page suivante : <http://msdn.microsoft.com/fr-fr/library/azure/hh563873.aspx>
3.  Pour être payé, vous devez indiquer des informations de paiement, notamment vos renseignements bancaires et fiscaux. Pour plus d'informations, consultez la page suivante : <http://msdn.microsoft.com/fr-fr/library/azure/hh563873.aspx>

## Options spécifiques à l'apprentissage automatique

1.  Lors de la création d'une offre, sélectionnez **Services de données**, puis cliquez sur **Créer un nouveau service de données**.

    ![Azure Marketplace][Azure Marketplace]

2.  Dans l'onglet **Service de données**, cliquez sur **Service web** pour la source de données.

    ![Azure Marketplace][1]

3.  Pour **URL du service**, utilisez l'URL de votre service web :

    -   Dans le menu gauche d'Azure ML Studio, cliquez sur **SERVICES WEB**.
    -   Cliquez sur le service web que vous voulez publier sur Marketplace.
    -   Sur la page **Tableau de bord**, cliquez sur **Page d'aide de l'API** pour le service RSS.
    -   Copiez l'adresse du point de terminaison OData.

4.  Pour l'authentification, choisissez **En-tête** comme **Schéma d'authentification**.

    -   Saisissez « Autorisation » comme **Nom d'en-tête**.
    -   Pour **Valeur d'en-tête** :

        -   Sur la page **Tableau de bord** de votre service web dans ML Studio, copiez la **Clé d'API**.
        -   Dans le champ **Valeur d'en-tête**, saisissez « Titulaire » (sans les guillemets), puis espace, puis collez la clé d'API.
    -   Cochez la case **Ce service est OData**.

5.  Catégories :

    -   Veillez à ce que la case **Machine Learning** soit cochée.

  [Introduction]: #introduction
  [Présentation du processus de publication]: #overview-of-the-publishing-process
  [Consignes pour la publication sur Azure Marketplace]: #guidelines-for-publishing-to-azure-marketplace
  [Options spécifiques à l'apprentissage automatique]: #machine-learning-specific-options
  [Azure Marketplace]: ./media/machine-learning-publish-web-service-to-azure-marketplace/image1.png
  [1]: ./media/machine-learning-publish-web-service-to-azure-marketplace/image2.png
