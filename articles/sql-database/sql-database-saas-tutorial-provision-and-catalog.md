---
title: "Approvisionner de nouveaux locataires dans une application mutualisée utilisant Azure SQL Database | Microsoft Docs"
description: "Découvrez comment approvisionner et cataloguer de nouveaux locataires dans l’application SaaS Wingtip"
keywords: "didacticiel sur les bases de données SQL"
services: sql-database
documentationcenter: 
author: stevestein
manager: craigg
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: scale out apps
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/26/2017
ms.author: sstein
ms.translationtype: HT
ms.sourcegitcommit: 54774252780bd4c7627681d805f498909f171857
ms.openlocfilehash: 658c316d8d9d14ce11dbb92188afbf0e68c00493
ms.contentlocale: fr-fr
ms.lasthandoff: 07/27/2017

---
# <a name="provision-new-tenants-and-register-them-in-the-catalog"></a>Approvisionner de nouveaux locataires et les inscrire dans le catalogue

Dans ce didacticiel, vous allez apprendre à approvisionner de nouveaux locataires dans l’application SaaS Wingtip. Vous créez des locataires en approvisionnant des bases de données clients et en les inscrivant dans le catalogue. Le *catalogue* est une base de données qui gère le mappage entre les locataires d’une application SaaS et les données associées.

Utilisez ces scripts pour explorer les modèles d’approvisionnement et d’inscription au catalogue utilisés et pour voir la façon dont l’inscription de nouveaux locataires dans le catalogue est implémentée. Le catalogue joue un rôle important, car il dirige les demandes de l’application vers la base de données appropriée.

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]

> * Approvisionner un nouveau locataire unique
> * Approvisionner un lot de locataires supplémentaires
> * Parcourir les étapes de l’approvisionnement des locataires et de leur inscription dans le catalogue


Pour suivre ce tutoriel, vérifiez que les conditions préalables suivantes sont bien satisfaites :

* L’application SaaS Wingtip est déployée. Pour procéder à un déploiement en moins de cinq minutes, consultez la page [Déployer et explorer une application SaaS Wingtip](sql-database-saas-tutorial.md)
* Azure PowerShell est installé. Pour plus d’informations, consultez [Bien démarrer avec Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

## <a name="introduction-to-the-saas-catalog-pattern"></a>Présentation du modèle de catalogue SaaS

Dans une application SaaS mutualisée appuyée par une base de données, il est important de savoir où sont stockées les informations de chaque locataire. Dans le modèle de catalogue SaaS, une base de données de catalogue permet de conserver le mappage entre les locataires et l’endroit où sont stockées les données. L’application SaaS Wingtip utilise une architecture à locataire unique par base de données. Toutefois, le modèle de base relatif au stockage du mappage locataire/base de données dans un catalogue s’applique, que la base de données soit mutualisée ou à locataire unique.

Chaque locataire reçoit une clé qui différencie ses données dans le catalogue. Dans le cas de l’application SaaS Wingtip, la clé est générée à partir d’un code de hachage du nom du locataire. Ce modèle permet d’utiliser le nom du locataire dans l’URL de l’application pour construire la clé et récupérer les connexions d’un locataire spécifique. D’autres modèles d’identification peuvent être utilisés sans affecter le modèle global.

Le catalogue de l’application est implémenté à l’aide de la technologie de gestion des partitions dans la [bibliothèque EDCL (Elastic Database Client Library, bibliothèque cliente de bases de données élastiques)](sql-database-elastic-database-client-library.md). La bibliothèque EDCL est responsable de la création et de la gestion d’un *catalogue* appuyé par une base de données où une *carte de partitions* est conservée. Le catalogue contient le mappage entre les clés (locataires) et leurs partitions (bases de données).

> [!IMPORTANT]
> Bien que les données de mappage soient accessibles dans la base de données de catalogue, *ne les modifiez pas*. Modifiez les données de mappage des API Elastic Database Client Library uniquement. Manipuler directement les données de mappage risque d’endommager le catalogue et n’est pas pris en charge.



## <a name="get-the-wingtip-application-scripts"></a>Obtenir les scripts d’application Wingtip

Les scripts et le code source de l’application SaaS Wingtip sont disponibles dans le référentiel GitHub [WingtipSaaS](https://github.com/Microsoft/WingtipSaaS). [Étapes de téléchargement des scripts SaaS Wingtip](sql-database-wtp-overview.md#download-and-unblock-the-wingtip-saas-scripts).

## <a name="provision-one-new-tenant"></a>Approvisionner un nouveau locataire

Si vous avez déjà créé un locataire dans le [premier didacticiel SaaS Wingtip](sql-database-saas-tutorial.md), vous pouvez directement passer à la section suivante où vous allez [approvisionner un lot de locataires](#provision-a-batch-of-tenants).

Exécutez le script *Demo-ProvisionAndCatalog* pour créer rapidement un locataire et l’inscrire dans le catalogue :

1. Ouvrez **Demo-ProvisionAndCatalog.ps1** dans l’ISE PowerShell et définissez les valeurs suivantes :
   * **$TenantName** = nom du nouveau lieu (par exemple *Bushwillow Blues*).
   * **$VenueType** = un des types prédéfinis de décor : blues, classicalmusic, dance, jazz, judo, motorracing, multipurpose, opera, rockmusic, soccer.
   * **$DemoScenario** = 1, laissez cette valeur définie sur _1_ pour *approvisionner un seul locataire*.

1. Appuyez sur **F5** pour exécuter le script.

Une fois le script terminé, le nouveau locataire est approvisionné et son application *Events* (Événements) s’ouvre dans le navigateur :

![Nouveau locataire](./media/sql-database-saas-tutorial-provision-and-catalog/new-tenant.png)


## <a name="provision-a-batch-of-tenants"></a>Approvisionner un lot de locataires

Cet exercice permet d’approvisionner un lot de locataires supplémentaires. Il est recommandé d’approvisionner un lot de locataires avant de réaliser d’autres didacticiels SaaS Wingtip afin que vous puissiez utiliser plusieurs bases de données.

1. Ouvrez...\\Learning Modules\\Utilities\\*Demo-ProvisionAndCatalog.ps1* dans l’*ISE PowerShell* et définissez le paramètre *$DemoScenario* sur 3 :
   * **$DemoScenario** = **3**. Changez sur **3** pour *Approvisionner un lot de clients*.
1. Appuyez sur **F5** pour exécuter le script.

Le script déploie un lot de locataires supplémentaires. Il utilise un [modèle Azure Resource Manager](../azure-resource-manager/resource-manager-template-walkthrough.md) qui contrôle le lot et délègue ensuite la configuration de chaque base de données à un modèle lié. Utiliser des modèles de cette façon permet à Azure Resource Manager de répartir le processus d’approvisionnement pour votre script. Les modèles approvisionnent des bases de données en parallèle dans la mesure du possible, et gèrent les nouvelles tentatives au besoin, en optimisant le processus global. Comme le script est idempotent, en cas d’échec ou d’arrêt pour une raison quelconque, exécutez-le à nouveau.

### <a name="verify-the-batch-of-tenants-successfully-deployed"></a>Vérifier que le lot de locataires a été correctement déployé

* Ouvrez le serveur *tenants1* en accédant à votre liste de serveurs dans le [portail Azure](https://portal.azure.com), cliquez sur **bases de données SQL**et vérifiez que le lot de 17 bases de données supplémentaires est désormais dans la liste :

   ![liste de base de données](media/sql-database-saas-tutorial-provision-and-catalog/database-list.png)


## <a name="provision-and-catalog-details"></a>Approvisionner des détails et les inscrire dans le catalogue

Pour mieux comprendre la façon dont l’application Wingtip implémente l’approvisionnement de nouveaux locataires, réexécutez le script *Demo-ProvisionAndCatalog* et approvisionnez un autre locataire. Cette fois-ci, ajoutez un point d’arrêt et suivez les étapes du flux de travail :

1. Ouvrez ...\\Learning Modules\Utilities\_Demo-ProvisionAndCatalog.ps1_ et définissez les paramètres suivants :
   * **$TenantName** = comme les noms des locataires doivent être uniques, définissez-le sur un nom différent de celui des locataires existants (par exemple, *Hackberry Hitters*).
   * **$VenueType** = utilisez un des types de lieu prédéfinis (par exemple *judo*).
   * **$DemoScenario** = **1**. Définissez sur **1** pour *approvisionner un seul client*.

1. Ajoutez un point d’arrêt en plaçant votre curseur n’importe où sur la ligne suivante : *New-Tenant `*, puis appuyez sur **F9**.

   ![point d’arrêt](media/sql-database-saas-tutorial-provision-and-catalog/breakpoint.png)

1. Pour exécuter le script, appuyez sur la touche **F5**. Lorsque le point d’arrêt est atteint, appuyez sur **F11** pour intervenir. Suivez l’exécution du script à l’aide des options du menu de débogage :**F10** et **F11** pour parcourir les fonctions invoquées. Pour plus d’informations sur le débogage des scripts PowerShell, consultez [Conseils sur l’utilisation et le débogage de scripts PowerShell](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise).

### <a name="examine-the-provision-and-catalog-implementation-in-detail-by-stepping-through-the-script"></a>Examiner en détail l’implémentation de l’approvisionnement et du catalogue en parcourant le script

Le script approvisionne et inscrit au catalogue les nouveaux locataires en procédant comme suit :

1. **Importe le module SubscriptionManagement.psm1** qui contient des fonctions pour la connexion à Azure et la sélection de l’abonnement Azure que vous utilisez.
1. **Importe le module CatalogAndDatabaseManagement.psm1** qui fournit un catalogue et l’abstraction au niveau du locataire sur les fonctions de [gestion des partitions](sql-database-elastic-scale-shard-map-management.md). Il s’agit d’un module important qui encapsule une bonne partie du modèle de catalogue et que nous vous conseillons d’explorer.
1. **Accédez aux détails de configuration**. Parcourez *Get-Configuration* (avec **F11**) et découvrez la façon dont la configuration de l’application est spécifiée. Les noms de ressources et d’autres valeurs propres à l’application sont définis ici, mais ne les modifiez pas tant que vous n’êtes pas familiarisé avec les scripts.
1. **Obtenez l’objet catalogue**. Parcourez *Get-Catalog* pour voir la manière dont le catalogue est initialisé à l’aide des fonctions de gestion des partitions qui sont importées à partir de **AzureShardManagement.psm1**. Le catalogue est composé des objets suivants :
   * $catalogServerFullyQualifiedName est construit à l’aide de la ressource standard et de votre nom d’utilisateur : _catalog-\<utilisateur\>.database.windows.net_.
   * $catalogDatabaseName est extrait de la configuration : *tenantcatalog*.
   * L’objet $shardMapManager est initialisé à partir de la base de données de catalogue.
   * L’objet $shardMap est initialisé à partir de la carte de partitions *tenantcatalog* dans la base de données de catalogue.
   Un objet catalogue est composé, retourné et utilisé dans le script de niveau supérieur.
1. **Calculez la clé du nouveau locataire**. Une fonction de hachage est utilisée pour créer la clé de locataire à partir du nom du locataire.
1. **Vérifiez si la clé de locataire existe déjà**. Le catalogue est passé en revue pour vérifier que la clé est disponible.
1. **La base de données de locataire est approvisionnée avec New-TenantDatabase.** Utilisez **F11** pour parcourir les étapes du script et voir la façon dont la base de données est approvisionnée à l’aide d’un modèle Resource Manager.

Le nom de la base de données est construit à partir du nom de locataire, ce qui permet d’indiquer clairement quelle partition appartient à tel locataire. (D’autres stratégies relatives aux noms de base de données peuvent facilement être utilisées.)

Un modèle Resource Manager permet de créer une base de données client en copiant une base de données *finale* (golden) (basetenantdb) sur le serveur de catalogue.  Une autre approche consiste à créer une base de données vide et à l’initialiser en important un fichier bacpac.

Le modèle Resource Manager est situé dans le dossier ... \\Learning Modules\\Common\\ : *tenantdatabasecopytemplate.json*

Une fois que la base de données de locataire a été créée, elle continue à être initialisée avec le nom du lieu (locataire) et le type de lieu. Une autre initialisation peut également être accomplie ici.

La base de données de locataire est inscrite dans le catalogue avec *Add-TenantDatabaseToCatalog* à l’aide de la clé de locataire. Utilisez **F11** pour parcourir le script en détail :

* La base de données de catalogue est ajoutée à la carte de partitions (liste des bases de données connues).
* Le mappage qui lie la valeur de clé (locataire) à la partition (base de données) est créé.
* Des métadonnées supplémentaires sur le locataire sont ajoutées.

À l’issue de l’approvisionnement, l’exécution retourne au script d’origine *Demo-ProvisionAndCatalog*, et la page **Events** (Événements) du nouveau locataire s’ouvre dans le navigateur :

   ![événements](media/sql-database-saas-tutorial-provision-and-catalog/new-tenant2.png)


## <a name="other-provisioning-patterns"></a>Autres modèles d’approvisionnement

Voici les autres modèles d’approvisionnement non inclus dans ce tutoriel :

**Pré-approvisionnement des bases de données.** Le modèle de pré-approvisionnement exploite le fait que les bases de données d’un pool élastique n’ajoutent pas de frais supplémentaires. La facturation concerne le pool élastique, pas les bases de données, et les bases de données inactives ne consomment aucune ressource. En pré-approvisionnant les bases de données d’un pool et en les allouant en cas de besoin, le délai d’intégration du locataire peut être réduit considérablement. Le nombre de bases de données pré-approvisionnées peut être ajusté en fonction des besoins pour conserver une mémoire tampon adaptée au taux d’approvisionnement prévu.

**Approvisionnement automatique.** Dans le modèle d’approvisionnement automatique, un service d’approvisionnement dédié est utilisé pour approvisionner automatiquement les serveurs, pools et bases de données en fonction des besoins, notamment en pré-approvisionnant les bases de données dans des pools élastiques si besoin. Si les bases de données sont mises hors service et supprimées, les écarts dans les pools élastiques peuvent être remplis par le service d’approvisionnement, en fonction des besoins. Un tel service peut être simple ou complexe (par exemple, la gestion de l’approvisionnement sur plusieurs zones géographiques) et peut configurer la géoréplication automatiquement si cette stratégie est utilisée pour la récupération d’urgence. Avec le modèle d’approvisionnement automatique, une application cliente ou un script soumet une demande d’approvisionnement à une file d’attente pour traitement par le service d’approvisionnement et interroge ensuite le service pour déterminer l’achèvement de l’opération. Si le pré-approvisionnement est utilisé, les demandes sont gérées rapidement grâce au service gérant l’approvisionnement d’une base de données de remplacement exécutée en arrière-plan.



## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à effectuer les opérations suivantes :

> [!div class="checklist"]

> * Approvisionner un nouveau locataire unique
> * Approvisionner un lot de locataires supplémentaires
> * Parcourir les étapes de l’approvisionnement des locataires et de leur inscription dans le catalogue

Essayez le [didacticiel Surveillance des performances](sql-database-saas-tutorial-performance-monitoring.md).

## <a name="additional-resources"></a>Ressources supplémentaires

* Autres [didacticiels reposant sur l’application SaaS Wingtip](sql-database-wtp-overview.md#sql-database-wingtip-saas-tutorials)
* [Bibliothèque cliente de base de données élastique](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-database-client-library)
* [Déboguer les scripts dans l’ISE Windows PowerShell](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise)

