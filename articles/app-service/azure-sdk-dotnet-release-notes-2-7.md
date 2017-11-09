---
title: "Notes de publication du Kit de développement logiciel (SDK) Azure pour .NET 2.7 et .NET 2.7.1"
description: "Notes de publication du Kit de développement logiciel (SDK) Azure pour .NET 2.7 et .NET 2.7.1"
services: app-service\web
documentationcenter: .net
author: chrissfanos
editor: 
ms.assetid: 877d070a-9bd5-49b3-8fac-6bb5f65c3554
ms.service: app-service
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 02/24/2017
ms.author: juliako
ms.openlocfilehash: 9a69253129cdedc4f5d7e736d5bd8d6a68f95a1e
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-sdk-for-net-27-and-net-271-release-notes"></a>Notes de publication du Kit de développement logiciel (SDK) Azure pour .NET 2.7 et .NET 2.7.1
## <a name="overview"></a>Vue d’ensemble
Ce document contient les notes de publication du Kit de développement logiciel (SDK) Azure pour .NET version 2.7. 

Ce document contient également les notes de publication du Kit de développement logiciel (SDK) Azure pour .NET version 2.7.1.

Le SDK Azure 2.7 est uniquement pris en charge dans Visual Studio 2015 et Visual Studio 2013. [Kit de développement logiciel (SDK) Azure 2.6](https://azure.microsoft.com/downloads/) est le dernier Kit de développement logiciel (SDK) pris en charge pour Visual Studio 2012.

Pour plus d’informations sur cette version, consultez le [billet d’annonce du Kit de développement logiciel (SDK) Azure 2.7](https://azure.microsoft.com/blog/2015/07/20/announcing-the-azure-sdk-2-7-for-net/) et le [billet d’annonce du Kit de développement logiciel (SDK) Azure 2.7.1](http://go.microsoft.com/fwlink/?LinkId=623850).

## <a name="azure-sdk-for-net-27"></a>Kit de développement logiciel (SDK) Azure pour .NET 2.7
### <a name="sign-in-improvements-for-visual-studio-2015"></a>Améliorations en matière de connexion pour Visual Studio 2015
Le Kit de développement logiciel Azure 2.7 pour Visual Studio 2015 prend en charge les nouvelles fonctionnalités de gestion des identités de Visual Studio 2015.  Cela inclut la prise en charge des comptes qui accèdent à Azure par le biais du contrôle d’accès en fonction du rôle (RBAC), de fournisseurs de solutions Cloud, de DreamSpark, ainsi que d’autres types de compte et d’abonnement.

Ces améliorations de connexion du le Kit de développement logiciel (SDK) Azure 2.7 sont uniquement disponibles dans Visual Studio 2015. La prise en charge de Visual Studio 2013 est comprise dans le Kit de développement logiciel (SDK) Azure 2.7.1.

### <a name="mobile-sdk"></a>Kit de développement logiciel (SDK) Mobile
Les modèles **Mobile Apps** ont été mis à jour afin de refléter le dernier [package NuGet](https://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/) et processus d’installation.

### <a name="service-bus"></a>Service Bus
Résolutions de bogue et améliorations générales. Pour plus d’informations sur les mises à jour et sur les fonctionnalités, voir les notes de publication du dernier [package NuGet Service Bus](http://www.nuget.org/packages/WindowsAzure.ServiceBus/).

### <a name="hdinsight-tools"></a>Outils HDInsight
Cette version intègre les mises à jour ci-dessous. Ces mises à jour sont disponibles en version préliminaire. Pour plus d’informations, voir [ce blog](http://go.microsoft.com/fwlink/?LinkId=619108).

* Graphiques Hive pour les travaux Hive sur Tez
* Prise en charge complète de la fonctionnalité IntelliSense pour les instructions DML Hive
* Prise en charge des modèles Pig
* Modèles Storm pour les services Azure

#### <a name="breaking-changes"></a>Dernières modifications
* En cas d’utilisation de cette version des outils, l’ancien projet **Storm** doit être mis à niveau. Pour plus d’informations, voir [ce blog](http://go.microsoft.com/fwlink/?LinkId=619108).
* Visual Studio Web Express n’est plus pris en charge. Pour plus d’informations, voir [ce blog](http://go.microsoft.com/fwlink/?LinkId=619108).

### <a name="azure-app-service-tools"></a>Outils Azure App Service
Dans cette version, les mises à jour ci-après ont été apportées à Web Tools Extensions. Pour plus d’informations, consultez [ce](https://azure.microsoft.com/blog/2015/07/20/announcing-the-azure-sdk-2-7-for-net/) blog. 

* Nouvelle prise en charge des comptes DreamSpark
* Modification complète d’Azure Tools pour prendre en charge les nouvelles API de gestion des ressources Azure
* Nouvelle prise en charge d’Azure App Service par [Cloud Explorer](#cloud_explorer)

#### <a name="known-issues"></a>Problèmes connus
Les nœuds d’emplacement de déploiement de Web App n’apparaissent pas sous le nœud Emplacements de l’Explorateur de serveurs, et les nœuds enfants d’emplacement de déploiement de Web App ne se chargent pas sous Cloud Explorer. Ce problème a été résolu et préparé pour la prochaine version du Kit de développement logiciel (SDK). 

### <a name="cloud_explorer"></a>Cloud Explorer pour Visual Studio 2015
Le Kit de développement logiciel (SDK) Azure 2.7 intègre Cloud Explorer pour Visual Studio 2015 qui vous permet de visualiser vos ressources Azure, d’inspecter leurs propriétés et d’exécuter des actions de développeur essentielles à partir de Visual Studio. 

Cloud Explorer prend en charge les fonctionnalités suivantes :

* Vues Groupe de ressources et Type de ressource de vos ressources Azure 
* Recherche de ressources par nom (disponible dans la vue Type de ressource)
* Prise en charge des abonnements et des ressources auxquels s’applique le contrôle d’accès en fonction du rôle (RBAC) 
* Panneau Action intégrée affichant les actions pour développeurs qui sont propres aux ressources sélectionnées (par exemple, Attacher le débogueur distant pour les machines virtuelles créées à l’aide de la pile du Gestionnaire des ressources, Afficher les données de diagnostic pour les machines virtuelles, etc.)
* Panneau Propriétés intégrées affichant les propriétés pour développeurs généralement nécessaires lors des phases de développement ou de test 
* Changement rapide du compte à utiliser lors de l’énumération des ressources (à l’aide de la commande Paramètres de la barre d’outils) 
* Filtrage des abonnements à utiliser lors de l’énumération des ressources (à l’aide de la commande Paramètres de la barre d’outils) 
* Liens ciblés vers le portail Azure pour la gestion des ressources et des groupes de ressources 

### <a name="azure-resource-manager-tools"></a>Outils du gestionnaire de ressources Azure
Les outils d’Azure Resource Manager ont été mis à jour de façon à fonctionner avec le contrôle d’accès en fonction du rôle (RBAC) et les nouveaux types d’abonnement.  Ces modifications intègrent la possibilité d’utiliser les nouveaux comptes de stockage, en plus du stockage classique, pour stocker les artefacts lors du déploiement.  

Si vous utilisez un projet de groupe de ressources Azure issu d’une version précédente du Kit de développement logiciel (SDK) avec le SDK 2.7, un nouveau script de déploiement est nécessaire pour effectuer le déploiement à l’aide d’un nouveau compte de stockage au lieu du stockage classique.  Le système vous demandera confirmation avant de modifier votre projet pour y ajouter le nouveau script.  L’ancien script sera renommé et vous devrez modifier le nouveau script manuellement.

### <a name="storage-explorer-tools"></a>Outils de l’Explorateur de stockage
* Prise en charge de l’affichage des types d’objet blob « Append Blob ». Plus d’informations dans [ce billet de blog](http://blogs.msdn.com/b/windowsazurestorage/archive/2015/04/13/introducing-azure-storage-append-blob.aspx). 
* Prise en charge de l’affichage des comptes de stockage Premium par le biais de l’Explorateur de serveurs. Pour les comptes de stockage Premium, l’Explorateur de serveurs affiche uniquement les objets blob de pages, car il s’agit du seul type pris en charge pour ces comptes.

### <a name="azure-data-factory-tools-for-visual-studio"></a>Outils d’Azure Data Factory pour Visual Studio
Introduction des **outils d’Azure Data Factory** pour Visual Studio. La liste ci-dessous répertorie les fonctionnalités activées. Pour plus d’informations, consultez [ce blog](http://go.microsoft.com/fwlink/?LinkId=617530) .

* **Création basée sur un modèle**: sélectionnez des modèles reposant sur des cas d’utilisation, des modèles de déplacement des données ou des modèles de traitement des données pour déployer une solution d’intégration de données de bout en bout et commencer à utiliser rapidement Data Factory. 
* **Intégration à l’Explorateur de solutions pour la création et le déploiement d’entités Data Factory** : créez et déployez des pipelines et des entités associées sous la forme de projets Visual Studio. 
* **Intégration à la vue Diagramme à des fins d’interaction visuelle pendant la création**: créez visuellement des pipelines et des jeux de données à l’aide de la vue Diagramme. 
* **Intégration à l’Explorateur de serveurs à des fins de navigation et d’interaction avec les entités déjà déployées**: tirez parti de l’Explorateur de serveurs pour parcourir les fabriques de données déjà déployées et les entités correspondantes. Importez une fabrique de données déployée ou n’importe quelle entité (pipeline, service lié, jeux de données) dans votre projet. 
* **Modification du code JSON avec validation de schéma et fonctionnalité IntelliSense enrichie**: configurez et modifiez efficacement les documents JSON des entités Data Factory grâce à la puissante fonctionnalité IntelliSense et à la validation de schéma. 
* **Publication multi-environnement**: publiez des pipelines créés dans un environnement de développement, de test ou de production en créant des fichiers de configuration distincts pour chaque environnement.
* **Prise en charge du traitement de données reposant sur Pig, Hive et .NET**: prenez en charge les scripts Pig et Hive dans vos projets Data Factory. Prenez également en charge le référencement de projets C# pour la gestion de .Net Activity.

## <a name="azure-sdk-for-net-271"></a>Kit de développement logiciel (SDK) Azure pour .NET 2.7.1
La section suivante contient des mises à jour qui ont été présentées avec le Kit de développement logiciel (SDK) pour la version 2.7.1. de .NET.

### <a name="hdinsight-tools"></a>Outils HDInsight
Pour plus d'explications sur les mises à jour des outils HDInsight, consultez [ce blog](http://go.microsoft.com/fwlink/?LinkId=623831).

* Affichage d’opérateur de tâche de Hive (nouvelle fonctionnalité)
  
    Pour vous aider à mieux comprendre votre requête Hive mieux, la fonctionnalité d'affichage d'opérateur Hive a été ajoutée. Pour voir tous les opérateurs à l'intérieur d'un vertex, double-cliquez sur les vertex du graphique de la tâche. Pour afficher plus d'informations sur un opérateur spécifique, pointez sur cet opérateur.
* Marqueur d'erreur Hive (une nouvelle fonctionnalité)
  
    Pour pouvoir consulter les erreurs de grammaire instantanément, la fonctionnalité de marqueur d'erreur Hive a été ajoutée. En outre, les messages d’erreur ont été améliorés et vous pouvez maintenant voir le détail des erreurs de grammaire instantanément (jusqu’à cette version, vous deviez envoyer un script Hive au cluster et attendre un certain temps avant d’obtenir les informations relatives à votre message d’erreur).  
* Graphique de topologie Storm (nouvelle fonctionnalité)
  
    L’affichage est très important lorsque vous souhaitez voir si votre typologie fonctionne comme prévu. Dans cette version, nous avons ajouté l’affichage des graphes Storm. Vous pouvez visualiser les métriques importants pour votre topologie (par exemple, une couleur indique météo une certaine Bolt est « occupé » ou non). Vous pouvez également double-cliquer sur Bolt/Spout pour afficher plus de détails.
* Prise en charge des clusters HDInsight qui ont été créés dans le portail Azure (résolution de bogue)
  
    Vous pouvez maintenant utiliser Visual Studio pour afficher et envoyer des tâches à tous vos clusters HDInsight, peu importe où ils ont été créés.
* Plus de prise en charge IntelliSense et un chargement plus rapide des métadonnées Hive (amélioration)
  
    Nous avons amélioré IntelliSense en ajoutant plus de suggestions conviviales. Par exemple, les alias de table peuvent maintenant être proposés dans IntelliSense pour vous permettre d'écrire plus facilement votre requête. Par ailleurs, nous avons amélioré le chargement des métadonnées Hive de sorte qu’il ne faut que quelques secondes pour répertorier toutes les bases de données, les tables et les colonnes de votre metastore Hive.

Pour plus d'explications sur les mises à jour des outils HDInsight, consultez [ce blog](http://go.microsoft.com/fwlink/?LinkId=623831).

### <a name="improvements-in-visual-studio-2013"></a>Améliorations dans Visual Studio 2013
* Le Kit de développement logiciel (SDK) Azure 2.7.1 permet à Visual Studio 2013 d’accéder aux comptes et aux abonnements via le contrôle d'accès basé sur le rôle, les fournisseurs de solutions de cloud et DreamSpark.
* Avec le Kit de développement logiciel (SDK) Azure 2.7.1, la nouvelle fenêtre d’outil Cloud Explorer est désormais également disponible dans Visual Studio 2013.

### <a name="known-issues"></a>Problèmes connus
L’installation du Kit de développement logiciel Azure 2.6 ou 2.7.1 pour Visual Studio Community 2013 sur un système d'exploitation non anglais affiche un avertissement qui indique que les ressources anglaises et non anglaises de Visual Studio peuvent ne pas correspondre. Cet avertissement peut être ignoré en toute sécurité. Il survient uniquement si l'ordinateur ne contient pas une installation antérieure de Visual Studio Community 2013 et que vous installez le Kit de développement logiciel (SDK) sur un système d'exploitation qui n’est pas en anglais. L'avertissement s'affiche une fois que le module linguistique applique les ressources RTM sur Visual Studio, mais avant l’application de la mise à jour 4. Si vous ignorez l'avertissement, cela permettra au module linguistique de continuer et de terminer l'application de la version 4 de la mise à jour du contenu du module linguistique.

Les projets LightSwitch ne sont pas compatibles avec cette version. Ce problème sera résolu avec la prochaine version du Kit de développement logiciel (SDK).

## <a name="also-see"></a>Voir aussi
[Billet d’annonce du Kit de développement logiciel (SDK) Azure 2.7.1](http://go.microsoft.com/fwlink/?LinkId=623850)

[Billet d’annonce du Kit de développement logiciel (SDK) Azure 2.7](https://azure.microsoft.com/blog/2015/07/20/announcing-the-azure-sdk-2-7-for-net/)

[Informations relatives à la prise en charge et au retrait pour le Kit de développement logiciel Azure SDK pour .NET et les API](https://msdn.microsoft.com/library/azure/dn479282.aspx/)

