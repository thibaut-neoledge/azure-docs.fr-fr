---
title: Publier des applications HDInsight - Azure | Microsoft Docs
description: "Découvrez comment créer et publier des applications HDInsight."
services: hdinsight
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 14aef891-7a37-4cf1-8f7d-ca923565c783
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/06/2017
ms.author: jgao
ms.translationtype: HT
ms.sourcegitcommit: eeed445631885093a8e1799a8a5e1bcc69214fe6
ms.openlocfilehash: 0ccaee16a19b4338099a93f994553caaa6aa57e3
ms.contentlocale: fr-fr
ms.lasthandoff: 09/07/2017

---
# <a name="publish-hdinsight-applications-into-the-azure-marketplace"></a>Publier des applications HDInsight dans Azure Marketplace
Une application HDInsight est une application que les utilisateurs peuvent installer sur un cluster HDInsight basé sur Linux. Ces applications peuvent être développées par Microsoft, par des éditeurs de logiciels indépendants (ISV) ou par vous-même. Cet article explique comment publier une application HDInsight sur la Place de marché Microsoft Azure.  Pour obtenir des informations générales sur la publication dans Azure Marketplace, consultez la page [Publier une offre dans Azure Marketplace](../marketplace-publishing/marketplace-publishing-getting-started.md).

Les applications HDInsight utilisent le modèle *BYOL (apportez votre propre licence)*, où le fournisseur de l’application doit fournir aux utilisateurs finaux la licence de l’application. Azure facture uniquement les ressources que ces utilisateurs créent, telles que le cluster HDInsight et ses machines virtuelles/nœuds. À ce stade, la facturation de l’application n’est pas effectuée via Azure.

Autre article concernant les applications HDInsight :

* [Installer des applications HDInsight](hdinsight-apps-install-applications.md) : découvrez comment installer une application HDInsight sur vos clusters.
* [Install custom HDInsight applications](hdinsight-apps-install-custom-applications.md)(Installer des applications HDInsight personnalisées) : découvrez comment installer et tester des applications HDInsight personnalisées.

## <a name="prerequisites"></a>Prérequis
Pour envoyer votre application personnalisée à la Place de marché, vous devez l’avoir créée et testée. Consultez les articles suivants :

* [Install custom HDInsight applications](hdinsight-apps-install-custom-applications.md)(Installer des applications HDInsight personnalisées) : découvrez comment installer et tester des applications HDInsight personnalisées.

Vous devez également avoir inscrit votre compte de développeur. Consultez les pages [Publier une offre dans Azure Marketplace](../marketplace-publishing/marketplace-publishing-getting-started.md) et [Créer un compte de développeur Microsoft](../marketplace-publishing/marketplace-publishing-accounts-creation-registration.md).

## <a name="define-application"></a>Définir l’application
La publication d’applications dans la Place de marché Azure se fait en deux étapes.  D’abord, vous définissez un fichier **createUiDef.json** pour indiquer les clusters compatibles avec votre application ; ensuite, vous publiez le modèle à partir du portail Azure. La section suivante présente un exemple de fichier createUiDef.json.

    {
        "handler": "Microsoft.HDInsight",
        "version": "0.0.1-preview",
        "clusterFilters": {
            "types": ["Hadoop", "HBase", "Storm", "Spark"],
            "tiers": ["Standard", "Premium"],
            "versions": ["3.4"]
        }
    }


| Champ | Description | Valeurs possibles |
| --- | --- | --- |
| types |Les types de cluster compatibles avec l’application. |Hadoop, HBase, Storm, Spark (ou toute combinaison de ceux-ci) |
| tiers |Les niveaux de cluster compatibles avec l’application. |Standard, Premium, (ou les deux) |
| versions |Les types de cluster HDInsight compatibles avec l’application. |3.4 |

## <a name="application-install-script"></a>Script d’installation d’application
L’installation d’une application sur un cluster (un cluster existant ou un nouveau) crée un nœud de périmètre sur lequel s’exécute le script d’installation.
  > [!IMPORTANT]
  > Les noms des scripts d’installation d’application doivent être uniques pour un cluster particulier, et leur format doit être le suivant :
  > 
  > name": "[concat('hue-install-v0','-' ,uniquestring(‘applicationName’)]"
  > 
  > Notez que le nom du script se compose de trois parties :
  > 
  > 1. Un préfixe, devant inclure soit le nom de l’application soit un nom en lien avec cette dernière.
  > 2. Un tiret, pour une meilleure lisibilité.
  > 3. Une fonction de chaîne unique utilisant le nom de l’application comme paramètre.
  > 
  > Dans l’exemple précédent, la liste d’actions de script persistantes contient le nom suivant : hue-install-v0-4wkahss55hlas. Pour obtenir un exemple de charge JSON, consultez [https://raw.githubusercontent.com/hdinsight/Iaas-Applications/master/Hue/azuredeploy.json](https://raw.githubusercontent.com/hdinsight/Iaas-Applications/master/Hue/azuredeploy.json).
  > 
Le script d’installation doit avoir les caractéristiques suivantes :
1. Assurez-vous que le script est idempotent. Plusieurs appels au script doivent produire le même résultat.
2. Le script doit être correctement versionné. Utilisez un autre emplacement pour le script lorsque vous effectuez une mise à niveau ou testez des modifications, afin d’éviter toute incidence sur les clients qui tentent d’installer l’application. 
3. Ajoutez aux scripts une journalisation adéquate à chaque étape. Le journaux de script sont généralement la seule façon de déboguer les problème d’installation des applications.
4. Assurez-vous que les appels passés à des services ou ressources externes peuvent faire l’objet de nouvelles tentatives afin que l’installation ne soit pas affectée par des problèmes réseau temporaires.
5. Si votre script démarre des services sur les nœuds, assurez-vous que les services sont analysés et configurés pour démarrer automatiquement en cas de redémarrage du nœud.

## <a name="package-application"></a>Empaqueter une application
Créez un fichier zip qui contient tous les fichiers requis pour l’installation de vos applications HDInsight. Vous aurez besoin du fichier zip à l’étape [Publier une application](#publish-application).

* [createUiDefinition.json](#define-application).
* mainTemplate.json. Consultez un exemple dans l’article [Installer des applications HDInsight personnalisées](hdinsight-apps-install-custom-applications.md).
* Tous les scripts nécessaires.

> [!NOTE]
> Les fichiers d’application (y compris les fichiers d’application web, le cas échéant) peuvent se trouver sur n’importe quel point de terminaison accessible publiquement.
> 

## <a name="publish-application"></a>Publication de l’application
Suivez ces étapes pour publier une application HDInsight :

1. Connectez-vous au [portail de publication Azure](https://publish.windowsazure.com/).
2. Cliquez sur **Modèles de solution** à gauche pour créer un modèle de solution.
3. Entrez un titre, puis cliquez sur **Créer un modèle de solution**.
4. Cliquez sur **Create Dev Center account and join the Azure program** (Créer un compte du Centre de développement et participer au programme Azure) pour enregistrer votre société, si ce n’est pas encore fait.  Consultez les pages [Créer un compte de développeur Microsoft](../marketplace-publishing/marketplace-publishing-accounts-creation-registration.md).
5. Cliquez sur **Définir des Topologies pour démarrer**. Un modèle de solution est « parent » de toutes ses topologies. Vous pouvez définir plusieurs topologies dans une offre/un modèle de solution. Quand une offre est envoyée dans l’environnement intermédiaire, toutes ses topologies l’accompagnent. 
6. Entrez un nom de topologie, puis cliquez sur le signe plus (+).
7. Entrez une nouvelle version, puis cliquez sur le signe plus (+).
8. Charger le fichier zip préparé à l’étape [Empaqueter une application](#package-application).  
9. Cliquez sur **Request Certification**(Demander la certification). L’équipe de certification Microsoft examine les fichiers et certifie la topologie.

## <a name="next-steps"></a>Étapes suivantes
* [Installer des applications HDInsight](hdinsight-apps-install-applications.md) : découvrez comment installer une application HDInsight sur vos clusters.
* [Installer des applications HDInsight personnalisées](hdinsight-apps-install-custom-applications.md): découvrez comment déployer des applications HDInsight inédites vers HDInsight.
* [Personnalisation de clusters HDInsight basés sur Linux à l’aide d’une action de script](hdinsight-hadoop-customize-cluster-linux.md): apprenez à utiliser l’action de script pour installer des applications supplémentaires.
* [Créer des clusters Hadoop sous Linux dans HDInsight à l’aide de modèles Azure Resource Manager](hdinsight-hadoop-create-linux-clusters-arm-templates.md): apprenez à appeler des modèles Resource Manager pour la création de clusters HDInsight.
* [Utiliser des nœuds de périmètre vides dans HDInsight](hdinsight-apps-use-edge-node.md): apprenez à utiliser un nœud de périmètre vide pour accéder au cluster HDInsight, tester des applications HDInsight et héberger des applications HDInsight.


