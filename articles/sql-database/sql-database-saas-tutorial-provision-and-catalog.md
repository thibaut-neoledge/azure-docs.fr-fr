---
title: "Approvisionner de nouveaux locataires dans une application multi-locataire, à l’aide de Azure SQL Database | Microsoft Docs"
description: "Approvisionner et cataloguer de nouveaux locataires dans l’application SaaS Wingtip"
keywords: "didacticiel sur les bases de données SQL"
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: tutorial
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 05/24/2017
ms.author: sstein
ms.translationtype: Human Translation
ms.sourcegitcommit: a30a90682948b657fb31dd14101172282988cbf0
ms.openlocfilehash: cf2fa0950f9f9df833051979b02355236214c4ea
ms.contentlocale: fr-fr
ms.lasthandoff: 05/25/2017


---
# <a name="provision-new-tenants-and-register-them-in-the-catalog"></a>Approvisionner de nouveaux locataires et les inscrire dans le catalogue

Dans ce didacticiel, vous allez apprendre à approvisionner de nouveaux locataires dans l’application SaaS Wingtip. Vous créez des locataires, des bases de données de locataires et inscrivez les locataires dans le catalogue. Le *catalogue* est une base de données qui gère le mappage entre les nombreux locataires des applications SaaS et les données associées. Utilisez ces scripts pour explorer les modèles d’approvisionnement et d’inscription au catalogue utilisés et pour voir la façon dont l’inscription de nouveaux locataires dans le catalogue est implémentée. Le catalogue joue un rôle important, car il dirige les demandes de l’application vers les bases de données appropriées.

Ce tutoriel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]

> * Approvisionner un nouveau locataire unique
> * Approvisionner un lot de locataires supplémentaires
> * Parcourir les étapes de l’approvisionnement de nouveaux locataires et de leur inscription dans le catalogue


Pour suivre ce tutoriel, vérifiez que les conditions préalables suivantes sont bien satisfaites :

* L’application SaaS Wingtip est déployée. Pour procéder à un déploiement en moins de cinq minutes, consultez la page [Déployer et explorer une application SaaS Wingtip](sql-database-saas-tutorial.md)
* Azure PowerShell est installé. Pour plus d’informations, consultez [Bien démarrer avec Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

## <a name="introduction-to-the-saas-catalog-pattern"></a>Présentation du modèle de catalogue SaaS

Dans une application SaaS mutualisée appuyée par une base de données, il est important de savoir où sont stockées les informations de chaque locataire. Dans le modèle de catalogue SaaS, une base de données de catalogue permet de conserver le mappage entre les locataires et l’endroit où sont stockées les données. L’application SaaS Wingtip utilise une architecture de base de données à locataire unique. Toutefois, le modèle de base relatif au stockage du mappage locataire/base de données dans un catalogue s’applique, que la base de données soit mutualisée ou à locataire unique.

Chaque locataire reçoit une clé qui différencie ses données dans le catalogue. Dans le cas de l’application SaaS Wingtip, la clé est générée à partir d’un code de hachage du nom du locataire. Ce modèle permet d’utiliser le nom du locataire dans l’URL de l’application pour construire la clé et récupérer les connexions d’un locataire spécifique. D’autres modèles d’identification peuvent être utilisés sans affecter le modèle global.

Le catalogue de l’application est implémenté à l’aide de la technologie de gestion des partitions dans la [bibliothèque EDCL (Elastic Database Client Library, bibliothèque cliente de bases de données élastiques)](sql-database-elastic-database-client-library.md). La bibliothèque EDCL est responsable de la création et de la gestion d’un _catalogue_ appuyé par une base de données où une _carte de partitions_ est conservée. Le catalogue contient le mappage entre les clés (locataires) et leurs bases de données (partitions).

> [!IMPORTANT]
> Bien que les données de mappage soient accessibles dans la base de données de catalogue, *ne les modifiez pas*. Modifiez les données de mappage des API Elastic Database Client Library uniquement. Manipuler directement les données de mappage risque d’endommager le catalogue et n’est pas pris en charge.

L’application SaaS Wingtip approvisionne de nouveaux locataires en copiant une base de données *finale* (golden).

## <a name="get-the-wingtip-application-scripts"></a>Obtenir les scripts d’application Wingtip

Les scripts et le code source de l’application SaaS Wingtip sont disponibles dans le référentiel GitHub [WingtipSaaS](https://github.com/Microsoft/WingtipSaaS). [Procédure de téléchargement des scripts SaaS Wingtip](sql-database-wtp-overview.md#download-the-wingtip-saas-scripts).

## <a name="provision-a-new-tenant"></a>Approvisionner un nouveau locataire

Si vous avez déjà créé un locataire dans le [premier didacticiel SaaS Wingtip](sql-database-saas-tutorial.md), vous pouvez directement passer à la section suivante : [approvisionner un lot de locataires](#provision-a-batch-of-tenants).

Exécutez le script *Demo-ProvisionAndCatalog* pour créer rapidement un locataire et l’inscrire dans le catalogue :

1. Ouvrez **Demo-ProvisionAndCatalog.ps1** dans l’ISE PowerShell et définissez les valeurs suivantes :
   * **$TenantName** = nom du nouveau lieu (par exemple *Bushwillow Blues*).
   * **$VenueType** = un des types prédéfinis de décor : blues, classicalmusic, dance, jazz, judo, motorracing, multipurpose, opera, rockmusic, soccer.
   * **$DemoScenario** = 1, laissez cette valeur définie sur _1_ pour **approvisionner un seul locataire**.

1. Appuyez sur **F5** pour exécuter le script.

Une fois le script terminé, le nouveau locataire est approvisionné et son application *Events* (Événements) s’ouvre dans le navigateur :

![Nouveau locataire](./media/sql-database-saas-tutorial-provision-and-catalog/new-tenant.png)


## <a name="provision-a-batch-of-tenants"></a>Approvisionner un lot de locataires

Cet exercice permet d’approvisionner un lot de locataires supplémentaires. Il est préférable de suivre ces étapes avant de réaliser tout autre didacticiel SaaS Wingtip.

1. Ouvrez ...\\Learning Modules\\Utilities\\*Demo-ProvisionAndCatalog.ps1* dans l’*ISE PowerShell* et définissez les valeurs suivantes :
   * **$DemoScenario** = **3**. Définissez sur **3** pour **Approvisionner un lot de locataires**.
1. Appuyez sur **F5** pour exécuter le script.

Le script déploie un lot de locataires supplémentaires. Il utilise un [modèle Azure Resource Manager](../azure-resource-manager/resource-manager-template-walkthrough.md) qui contrôle le lot et délègue ensuite la configuration de chaque base de données à un modèle lié. Utiliser des modèles de cette façon permet à Azure Resource Manager de répartir le processus d’approvisionnement pour votre script. Les modèles approvisionnent des bases de données en parallèle dans la mesure du possible, et gèrent les nouvelles tentatives au besoin, en optimisant le processus global. Le script étant idempotent, s’il est interrompu, réexécutez-le.

### <a name="verify-the-batch-of-tenants-successfully-deployed"></a>Vérifier que le lot de locataires a été correctement déployé

* Ouvrez le serveur *tenants1* dans le [portail Azure](https://portal.azure.com) et cliquez sur **Bases de données SQL** :

   ![liste de base de données](media/sql-database-saas-tutorial-provision-and-catalog/database-list.png)


## <a name="provision-and-catalog-detailed-walkthrough"></a>Procédure pas à pas détaillée sur l’approvisionnement et l’inscription dans le catalogue

Pour mieux comprendre la façon dont l’application Wingtip implémente l’approvisionnement de nouveaux locataires, réexécutez le script *Demo-ProvisionAndCatalog* et approvisionnez un autre locataire. Cette fois-ci, ajoutez un point d’arrêt et suivez les étapes du flux de travail :

1. Ouvrez ...\\Learning Modules\Utilities\_Demo-ProvisionAndCatalog.ps1_ et définissez les éléments suivants avec de nouvelles valeurs de locataire qui n’existent pas dans le catalogue actuel :
   * **$TenantName** = définissez sur un nouveau nom (par exemple *Hackberry Hitters*).
   * **$VenueType** = utilisez un des types de lieu prédéfinis (par exemple *judo*).
   * **$DemoScenario** = 1, définissez sur **1** pour **approvisionner un seul locataire**.

1. Ajoutez un point d’arrêt en plaçant votre curseur n’importe où sur la ligne suivante : *New-Tenant `*, puis appuyez sur **F9**.

   ![point d’arrêt](media/sql-database-saas-tutorial-provision-and-catalog/breakpoint.png)

1. Appuyez sur **F5** pour exécuter le script. Lorsque le point d’arrêt est atteint, appuyez sur **F11** pour intervenir. Suivez l’exécution du script à l’aide de **F10** et **F11** pour parcourir les fonctions appelées. [Conseils pour utiliser et déboguer les scripts PowerShell](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise)

### <a name="examine-the-provision-and-catalog-implementation-in-detail-by-stepping-through-the-script"></a>Examiner en détail l’implémentation de l’approvisionnement et du catalogue en parcourant le script

Le script approvisionne et inscrit au catalogue les nouveaux locataires en procédant comme suit :

1. **Importe le module SubscriptionManagement.psm1** qui contient des fonctions pour la connexion à Azure et la sélection de l’abonnement Azure que vous utilisez.
1. **Importe le module CatalogAndDatabaseManagement.psm1** qui fournit un catalogue et l’abstraction au niveau du locataire sur les fonctions de [gestion des partitions](sql-database-elastic-scale-shard-map-management.md). Il s’agit d’un module important qui encapsule une bonne partie du modèle de catalogue et que nous vous conseillons d’explorer.
1. **Accédez aux détails de configuration**. Parcourez _Get-Configuration_ (avec **F11**) et découvrez la façon dont la configuration de l’application est spécifiée. Les noms de ressources et d’autres valeurs propres à l’application sont définis ici, mais ne les modifiez pas tant que vous n’êtes pas familiarisé avec les scripts.
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

Un modèle Resource Manager est utilisé pour **créer une base de données de locataire en copiant une base de données *finale* (golden)** (baseTenantDB) sur le serveur de catalogue.  Une autre approche consiste à créer une base de données vide et à l’initialiser en important un fichier bacpac.

Le modèle Resource Manager est situé dans le dossier ... \\Learning Modules\\Common\\ : *tenantdatabasecopytemplate.json*

Une fois que la base de données de locataire a été créée, elle continue à être initialisée avec le nom du lieu (locataire) et le type de lieu. Une autre initialisation peut également être accomplie ici.

La base de données de locataire est inscrite dans le catalogue avec *Add-TenantDatabaseToCatalog* à l’aide de la clé de locataire. Utilisez **F11** pour parcourir le script en détail :

* La base de données de catalogue est ajoutée à la carte de partitions (liste des bases de données connues).
* Le mappage qui lie la valeur de clé à la partition est créé.
* Des métadonnées supplémentaires (nom du lieu) sur le locataire sont ajoutées.

Une fois que l’approvisionnement est terminé, l’exécution retourne au script d’origine *Demo-ProvisionAndCatalog* et la page **Events** (Événements) du nouveau locataire s’ouvre dans le navigateur :

   ![événements](media/sql-database-saas-tutorial-provision-and-catalog/new-tenant2.png)


## <a name="other-provisioning-patterns"></a>Autres modèles d’approvisionnement

Voici les autres modèles d’approvisionnement non inclus dans ce tutoriel :

**Pré-approvisionnement des bases de données.** Ce modèle exploite le fait que les bases de données d’un pool élastique n’ajoutent aucun coût supplémentaire (la facturation concerne le pool élastique, pas les bases de données) et que les bases de données inactives ne consomment aucune ressource. En pré-approvisionnant les bases de données d’un pool et en les allouant en cas de besoin, le délai d’intégration du locataire peut être réduit considérablement. Le nombre de bases de données pré-approvisionnées peut être ajusté en fonction des besoins pour conserver une mémoire tampon adaptée au taux d’approvisionnement prévu.

**Approvisionnement automatique.** Dans ce modèle, un service d’approvisionnement dédié est utilisé pour approvisionner automatiquement les serveurs, pools et bases de données en fonction des besoins, notamment en pré-approvisionnant les bases de données dans des pools élastiques si besoin. Si les bases de données sont mises hors service et supprimées, les écarts dans les pools élastiques peuvent être remplis par le service d’approvisionnement, en fonction des besoins. Un tel service peut être simple ou complexe (par exemple, la gestion de l’approvisionnement sur plusieurs zones géographiques) et peut configurer la géoréplication automatiquement si cette stratégie est utilisée pour la récupération d’urgence. Avec le modèle d’approvisionnement automatique, une application cliente ou un script soumet une demande d’approvisionnement à une file d’attente pour traitement par le service d’approvisionnement et interroge ensuite le service pour déterminer l’achèvement de l’opération. Si le pré-approvisionnement est utilisé, les demandes sont gérées rapidement grâce au service gérant l’approvisionnement d’une base de données de remplacement exécutée en arrière-plan.


## <a name="stopping-wingtip-saas-application-related-billing"></a>Arrêt de la facturation liée à l’application SaaS Wingtip

Si vous ne prévoyez pas de passer à un autre tutoriel, il est recommandé de supprimer toutes les ressources pour interrompre la facturation. Supprimez le groupe de ressources dans lequel l’application Wingtip a été déployé. Ainsi, toutes ses ressources sont supprimées.

* Accédez au groupe de ressources de l’application dans le portail et supprimez-le afin d’arrêter toute facturation associée à ce déploiement Wingtip.

## <a name="tips"></a>Conseils

* La bibliothèque EDCL fournit également des fonctionnalités importantes qui permettent aux applications clientes de se connecter au catalogue et de l’utiliser. Vous pouvez également utiliser la bibliothèque EDCL pour récupérer une connexion ADO.NET pour une valeur de clé donnée, ce qui permet à l’application de se connecter à la base de données. Le client met en cache ces informations de connexion pour réduire le trafic vers la base de données de catalogue et accélérer l’application.



## <a name="next-steps"></a>Étapes suivantes

Dans ce tutoriel, vous avez appris à effectuer les opérations suivantes :

> [!div class="checklist"]

> * Approvisionner un nouveau locataire unique
> * Approvisionner un lot de locataires supplémentaires
> * Parcourir les étapes de l’approvisionnement de nouveaux locataires et de leur inscription dans le catalogue

[Tutoriel Surveillance des performances](sql-database-saas-tutorial-performance-monitoring.md)

## <a name="additional-resources"></a>Ressources supplémentaires

* Autres [didacticiels reposant sur l’application SaaS Wingtip](sql-database-wtp-overview.md#sql-database-wingtip-saas-tutorials)
* [Bibliothèque cliente de base de données élastique](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-database-client-library)
* [Déboguer les scripts dans l’ISE Windows PowerShell](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise)

