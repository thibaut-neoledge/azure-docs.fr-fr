---
title: "Création de clusters Hadoop basés sur Windows dans HDInsight | Microsoft Docs"
description: "Apprenez à créer des clusters Hadoop basés sur Windows dans HDInsight à l’aide du portail Azure."
services: hdinsight
documentationcenter: 
tags: azure-portal
author: mumian
manager: jhubbard
editor: cgronlun
ms.assetid: c8689ef3-f56f-4708-8a3a-cc00abc54e8d
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 01/18/2017
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: ede2e4ec5f3414d1c8a17f4c120011eba0d9a6ca
ms.openlocfilehash: a9d7a4372693ad021a898fae9818f2d037d42547


---
# <a name="create-windows-based-hadoop-clusters-in-hdinsight-using-the-azure-portal"></a>Création de clusters Hadoop basés sur Windows dans HDInsight à l’aide du portail Azure

[!INCLUDE [selector](../../includes/hdinsight-selector-create-clusters.md)]

Apprenez à créer des clusters Hadoop Windows dans HDInsight à l’aide du portail Azure. 

Les informations mentionnées dans cet article s’appliquent uniquement aux clusters HDInsight basés sur Windows. Pour plus d’informations sur la création de clusters Linux, consultez [Création de clusters Hadoop dans HDInsight à l’aide du portail Azure](hdinsight-hadoop-create-linux-clusters-portal.md).

> [!IMPORTANT]
> Linux est le seul système d’exploitation utilisé sur HDInsight version 3.4 ou supérieure. Pour en savoir plus, consultez le paragraphe [Obsolescence de HDInsight sous Windows](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date).

## <a name="prerequisites"></a>Configuration requise :
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Avant de commencer à suivre les instructions de cet article, vous devez disposer des éléments suivants :

* Un abonnement Azure. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

### <a name="access-control-requirements"></a>Exigences de contrôle d’accès
[!INCLUDE [access-control](../../includes/hdinsight-access-control-requirements.md)]

## <a name="create-clusters"></a>Créer des clusters
**Création d'un cluster HDInsight**

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Cliquez sur **NOUVEAU**, **Données + Analyse**, puis sur **HDInsight**.
3. Tapez ou sélectionnez les valeurs suivantes :
   
   * **Nom du cluster** : nom de votre cluster.
   * **Abonnement** : sélectionnez un abonnement Azure utilisé pour ce cluster.
   * **Configuration du cluster** :

     * **Type du cluster** : pour les besoins de ce didacticiel, sélectionnez **Hadoop**.
     * **Système d’exploitation** : sélectionnez **Windows** pour ce didacticiel.
     * **Version** : sélectionnez **Hadoop 2.7.0 (HDI 3.3)**.  Il s’agit de la version la plus récente pour les clusters Hadoop basés sur Windows.
     * **Niveau de cluster** : sélectionnez **STANDARD** pour ce didacticiel.

   * **Informations d’identification** :

     * **Nom d’utilisateur de connexion du cluster**: le nom par défaut est **admin**. 
     * **Mot de passe de connexion du cluster** : saisissez un mot de passe.
     * **Activer le Bureau à distance** : vous n’avez pas besoin du bureau à distance pour ce didacticiel.

   * **Source de données** :

     * **Méthode de sélection** : sélectionnez **À partir de tous les abonnements**.
     * **Sélectionnez un compte de stockage** : cliquez sur **Créer nouveau**, puis entrez un nom pour le compte de stockage par défaut.
     * **Conteneur par défaut** : par défaut, le conteneur par défaut utilise le même nom que celui du cluster.
     * **Emplacement** : choisissez un emplacement proche.  Cet emplacement est utilisé par le cluster et le compte de stockage par défaut.
   * **Taille du cluster** :

     * **Nombre de nœuds Worker** : ce didacticiel ne requiert qu’un nœud.
   * **Configurations avancées** : vous pouvez ignorer cette partie pour ce didacticiel.
   * **Groupe de ressources**  : sélectionnez **Créer un nouveau**, puis entrez un nom.

4. Sélectionnez **Épingler au tableau de bord**, puis cliquez sur **Créer**. En sélectionnant l’option **Épingler au tableau d’accueil** , une vignette est ajoutée pour le cluster dans le tableau d’accueil de votre portail. La création d’un cluster prend 15 à 20 minutes. Utilisez la mosaïque du tableau d’accueil ou l’entrée **Notifications** à gauche de la page pour vérifier le processus d’approvisionnement.
5. Une fois la création terminée, cliquez sur la vignette du cluster dans le tableau d'accueil pour lancer le panneau du cluster. Le panneau de cluster fournit des informations essentielles sur le cluster, notamment son nom, le groupe de ressources auquel il appartient, son emplacement, le système d'exploitation, l'URL vers le tableau de bord de cluster, etc.

## <a name="customize-clusters"></a>Personnalisation des clusters
* Consultez [Personnalisation de clusters HDInsight à l’aide de Bootstrap](hdinsight-hadoop-customize-cluster-bootstrap.md).
* Consultez [Personnalisation de clusters HDInsight basés sur Windows à l’aide d’une action de script](hdinsight-hadoop-customize-cluster.md).

## <a name="next-steps"></a>Étapes suivantes
Cet article vous a présenté différentes méthodes pour créer un cluster HDInsight. Pour en savoir plus, consultez les articles suivants :

* [Prise en main d’Azure HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md) : apprenez à utiliser votre cluster HDInsight
* [Envoi de tâches Hadoop par programme](hdinsight-submit-hadoop-jobs-programmatically.md) : découvrez comment envoyer des tâches par programme à HDInsight
* [Gestion des clusters Hadoop dans HDInsight au moyen du portail Azure](hdinsight-administer-use-management-portal.md)




<!--HONumber=Jan17_HO3-->


