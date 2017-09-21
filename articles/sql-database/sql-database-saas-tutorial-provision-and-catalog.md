---
title: "Approvisionner de nouveaux locataires dans une application mutualisée utilisant Azure SQL Database | Microsoft Docs"
description: "Découvrez comment approvisionner et cataloguer de nouveaux locataires dans une application SaaS multilocataire Azure SQL Database."
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
ms.date: 08/11/2017
ms.author: sstein
ms.translationtype: HT
ms.sourcegitcommit: 2c6cf0eff812b12ad852e1434e7adf42c5eb7422
ms.openlocfilehash: febf9d9f8a32a95ae61734cc2d3632bb74f6c8f5
ms.contentlocale: fr-fr
ms.lasthandoff: 09/13/2017

---
# <a name="learn-how-to-provision-and-catalog-new-tenants-and-register-them-in-the-catalog"></a>Découvrez comment approvisionner et cataloguer de nouveaux locataires, et les inscrire dans le catalogue.

Ce didacticiel décrit les modèles SaaS d’approvisionnement et d’inscription au catalogue et comment ils sont implémentés dans l’application SaaS Wingtip. Vous allez créer et initialiser de nouvelles bases de données de clients et les enregistrer dans le catalogue de clients de l’application. Le catalogue est une base de données qui gère le mappage entre les nombreux clients des applications SaaS et les données associées. Le catalogue joue un rôle important, car il dirige les demandes de l’application vers la base de données appropriée.  

Ce didacticiel vous montre comment effectuer les opérations suivantes :

> [!div class="checklist"]

> * Approvisionner un nouveau client unique et découvrir comment il est implémenté
> * Approvisionner un lot de locataires supplémentaires


Pour suivre ce tutoriel, vérifiez que les conditions préalables suivantes sont bien satisfaites :

* L’application SaaS Wingtip est déployée. Pour procéder à un déploiement en moins de cinq minutes, consultez la page [Déployer et explorer une application SaaS Wingtip](sql-database-saas-tutorial.md)
* Azure PowerShell est installé. Pour plus d’informations, consultez [Bien démarrer avec Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

## <a name="introduction-to-the-saas-catalog-pattern"></a>Présentation du modèle de catalogue SaaS

Dans une application SaaS mutualisée appuyée par une base de données, il est important de savoir où sont stockées les informations de chaque locataire. Dans le modèle de catalogue SaaS, une base de données de catalogue permet de conserver le mappage entre les clients et l’endroit où sont stockées les données. L’application SaaS Wingtip utilise une architecture à locataire unique par base de données. Toutefois, le modèle de base relatif au stockage du mappage locataire/base de données dans un catalogue s’applique, que la base de données soit mutualisée ou à locataire unique.

Une clé est attribuée à chaque client afin de l’identifier dans le catalogue. Cette est mappée sur l’emplacement de la base de données correspondante. Dans le cas de l’application SaaS Wingtip, la clé est générée à partir d’un code de hachage du nom du client. Cela permet d’utiliser le nom du client dans l’URL de l’application pour construire la clé. D’autres schémas de clé de client peuvent être utilisés.  

Le catalogue permet de modifier le nom ou l’emplacement de la base de données avec un impact minimal sur l’application.  Dans un modèle de base de données multiclient, il facilite également le « déplacement » d’un client entre les différentes bases de données.  Le catalogue peut également être utilisé pour indiquer si un client ou une base de données est hors connexion pour maintenance ou dans le cadre d’autres opérations. Ce processus est décrit dans le [didacticiel sur la restauration d’un client unique](sql-database-saas-tutorial-restore-single-tenant.md).

En outre, le catalogue, qui est en réalité une base de données de gestion pour une application SaaS, peut stocker des métadonnées supplémentaires relatives au client ou à la base de données, telles que le niveau ou la version d’une base de données, la version du schéma, le plan de service ou les SLA proposés aux clients, ainsi que d’autres informations associées à la gestion des applications, au support technique ou aux processus devops.  

Au-delà de l’application SaaS, le catalogue permet d’accéder à des outils de base de données.  Dans l’exemple de l’application SaaS Wingtip, le catalogue est utilisé pour activer des requêtes entre clients, tel que décrit dans le [didacticiel sur les analyses ad hoc](sql-database-saas-tutorial-adhoc-analytics.md). La gestion des travaux entre différentes bases de données est expliquée dans les didacticiels sur la [gestion des schémas](sql-database-saas-tutorial-schema-management.md) et les [analyses de clients](sql-database-saas-tutorial-tenant-analytics.md). 

Dans l’application SaaS Wingtip, le catalogue est implémenté à l’aide des fonctionnalités de gestion des partitions de la [bibliothèque EDCL (Elastic Database Client Library, bibliothèque cliente de bases de données élastiques)](sql-database-elastic-database-client-library.md). La bibliothèque EDCL permet à une application de créer, gérer et utiliser une carte de partitions reposant sur des bases de données. Une carte de partitions contient une liste de partitions (bases de données) et le mappage entre les clés (clients) et les bases de données.  Les fonctions EDCL peuvent être utilisées à partir des applications ou des scripts PowerShell au cours de l’approvisionnement des clients pour créer les entrées dans la carte de partitions, et à partir des applications pour assurer une connexion fiable à la base de données correspondante. La bibliothèque EDCL met en cache les informations de connexion pour réduire le trafic vers la base de données de catalogue et booster les performances de l’application.  

> [!IMPORTANT]
> Bien que les données de mappage soient accessibles dans la base de données de catalogue, *ne les modifiez pas*. Modifiez les données de mappage des API Elastic Database Client Library uniquement. Manipuler directement les données de mappage risque d’endommager le catalogue et n’est pas pris en charge.


## <a name="introduction-to-the-saas-provisioning-pattern"></a>Présentation du modèle d’approvisionnement SaaS

Lors de l’intégration d’un nouveau client dans une application SaaS qui utilise un modèle de base de données à client unique, une nouvelle base de données client doit être approvisionnée.  Celle-ci doit être créée dans l’emplacement et le niveau de service appropriés, initialisée avec les données de référence et le schéma appropriés, puis enregistrée dans le catalogue sous la clé de client correspondante.  

Il existe différentes approches d’approvisionnement des bases de données, notamment l’exécution de scripts SQL, le déploiement d’un fichier bacpac ou la copie d’un modèle de base de données finale.  

La méthode d’approvisionnement que vous utilisez doit être comprise dans votre stratégie globale de gestion des schémas, qui doit s’assurer que les nouvelles bases de données sont approvisionnées avec le schéma le plus récent.  Cela est explique dans le [didacticiel sur la gestion des schémas](sql-database-saas-tutorial-schema-management.md).  

L’application SaaS Wingtip approvisionne les nouveaux clients en copiant une base de données finale nommée basetenantdb, déployée sur le serveur de catalogue.  L’approvisionnement peut être intégré au processus d’inscription de l’application et/ou pris en charge en mode hors connexion à l’aide de scripts. Ce didacticiel décrit le processus d’approvisionnement à l’aide des scripts PowerShell. Les scripts d’approvisionnement copient l’instance basetenantdb pour créer une nouvelle base de données client dans un pool élastique, avant de l’initialiser avec les informations spécifiques au client et de l’inscrire dans la carte de partitions du catalogue.  Dans l’exemple d’application, les bases de données sont nommées en fonction du nom du client, même s’il ne s’agit pas d’un élément essentiel du modèle. Le catalogue permet d’attribuer n’importe quel nom à la base de données.+ 


## <a name="get-the-wingtip-application-scripts"></a>Obtenir les scripts d’application Wingtip

Les scripts et le code source de l’application SaaS Wingtip sont disponibles dans le référentiel GitHub [WingtipSaaS](https://github.com/Microsoft/WingtipSaaS). [Étapes de téléchargement des scripts SaaS Wingtip](sql-database-wtp-overview.md#download-and-unblock-the-wingtip-saas-scripts).


## <a name="provision-and-catalog-detailed-walkthrough"></a>Procédure pas à pas détaillée sur l’approvisionnement et l’inscription dans le catalogue

Pour comprendre comment l’application Wingtip gère l’approvisionnement du nouveau client, ajoutez un point d’arrêt et suivez les étapes du flux de travail dans le cadre de l’approvisionnement d’un client :

1. Ouvrez ...\\Learning Modules\\ProvisionAndCatalog\\_Demo-ProvisionAndCatalog.ps1_ et définissez les paramètres suivants :
   * **$TenantName** = nom du nouveau lieu (par exemple *Bushwillow Blues*).
   * **$VenueType** = un des types prédéfinis de décor : *blues*, classicalmusic, dance, jazz, judo, motorracing, multipurpose, opera, rockmusic, soccer.
   * **$DemoScenario** = **1**. Définissez sur **1** pour *approvisionner un seul client*.

1. Ajoutez un point d’arrêt en plaçant votre curseur n’importe où sur la ligne 48, celle qui indique *New-Tenant `*, puis appuyez sur **F9**.

   ![point d’arrêt](media/sql-database-saas-tutorial-provision-and-catalog/breakpoint.png)

1. Pour exécuter le script, appuyez sur la touche **F5**.

1. Une fois que l’exécution du script s’arrête au point d’arrêt, appuyez sur **F11** pour effectuer un pas à pas détaillé du code.

   ![point d’arrêt](media/sql-database-saas-tutorial-provision-and-catalog/debug.png)



Suivez l’exécution du script à l’aide des options du menu **Débogage** **F10** et **F11** pour parcourir les fonctions invoquées. Pour plus d’informations sur le débogage des scripts PowerShell, consultez [Conseils sur l’utilisation et le débogage de scripts PowerShell](https://msdn.microsoft.com/powershell/scripting/core-powershell/ise/how-to-debug-scripts-in-windows-powershell-ise).


Les points suivants ne sont pas des étapes à suivre explicitement, mais constituent une explication du flux de travail parcouru pendant le débogage du script :

1. **Importe le module SubscriptionManagement.psm1** qui contient des fonctions pour la connexion à Azure et la sélection de l’abonnement Azure que vous utilisez.
1. **Importe le module CatalogAndDatabaseManagement.psm1** qui fournit un catalogue et l’abstraction au niveau du locataire sur les fonctions de [gestion des partitions](sql-database-elastic-scale-shard-map-management.md). Il s’agit d’un module important qui encapsule une bonne partie du modèle de catalogue et que nous vous conseillons d’explorer.
1. **Accédez aux détails de configuration**. Accédez à Get-Configuration (avec F11) et découvrez la façon dont la configuration de l’application est spécifiée. Les noms de ressources et d’autres valeurs propres à l’application sont définis ici, mais ne les modifiez pas tant que vous n’êtes pas familiarisé avec les scripts.
1. **Obtenez l’objet catalogue**. Accédez à la fonction Get-Catalogue qui compose et retourne un objet de catalogue utilisé dans le script de niveau supérieur.  Cette fonction utilise les fonctions de gestion de partitions qui sont importées à partir de **AzureShardManagement.psm1**. L’objet de catalogue est composé des éléments suivants :
   * $catalogServerFullyQualifiedName est construit à l’aide de la ressource standard et de votre nom d’utilisateur : _catalog-\<utilisateur\>.database.windows.net_.
   * $catalogDatabaseName est extrait de la configuration : *tenantcatalog*.
   * L’objet $shardMapManager est initialisé à partir de la base de données de catalogue.
   * L’objet $shardMap est initialisé à partir de la carte de partitions *tenantcatalog* dans la base de données de catalogue.
   Un objet catalogue est composé, retourné et utilisé dans le script de niveau supérieur.
1. **Calculez la clé du nouveau locataire**. Une fonction de hachage est utilisée pour créer la clé de locataire à partir du nom du locataire.
1. **Vérifiez si la clé de locataire existe déjà**. Le catalogue est passé en revue pour vérifier que la clé est disponible.
1. **La base de données de locataire est approvisionnée avec New-TenantDatabase.** Utilisez **F11** pour parcourir les étapes du script et voir la façon dont la base de données est approvisionnée à l’aide d’un modèle [Azure Resource Manager](../azure-resource-manager/resource-manager-template-walkthrough.md).

Le nom de la base de données est construit à partir du nom de locataire, ce qui permet d’indiquer clairement quelle partition appartient à tel locataire. (D’autres stratégies relatives aux noms de base de données peuvent facilement être utilisées.)+ Un modèle Resource Manager permet de créer une base de données client en copiant une base de données finale (basetenantdb) sur le serveur de catalogue. Une autre approche consiste à créer une base de données vide et à l’initialiser en important un fichier bacpac. Il est également possible d’exécuter un script d’initialisation à partir d’un emplacement connu.  

Le modèle Resource Manager est situé dans le dossier ...\Learning Modules\Common\ : *tenantdatabasecopytemplate.json*

Une fois que la base de données client a été créée, elle continue à être **initialisée avec le nom du lieu (client) et le type de lieu**. Une autre initialisation peut également être accomplie ici.

La **base de données client est inscrite dans le catalogue** avec *Add-TenantDatabaseToCatalog* à l’aide de la clé de client. Utilisez **F11** pour parcourir le script en détail :

* La base de données de catalogue est ajoutée à la carte de partitions (liste des bases de données connues).
* Le mappage qui lie la valeur de clé à la partition est créé.
* Des métadonnées supplémentaires (nom du lieu) sur le client sont ajoutées dans le tableau Clients du catalogue.  Le tableau Clients ne fait pas partie du schéma ShardManagement et n’est pas installé par la bibliothèque EDCL.  Le tableau suivant illustre comment la base de données de catalogue peut être étendue pour prendre en charge des données supplémentaires spécifiques à l’application.   


Une fois l’approvisionnement terminé, l’exécution retourne au script d’origine *Demo-ProvisionAndCatalog* qui ouvre la page **Events** (Événements) du nouveau client dans le navigateur :

   ![événements](media/sql-database-saas-tutorial-provision-and-catalog/new-tenant.png)


## <a name="provision-a-batch-of-tenants"></a>Approvisionner un lot de locataires

Cet exercice permet d’approvisionner un lot de 17 clients. Il est recommandé d’approvisionner ce lot de clients avant de suivre d’autres didacticiels SaaS Wingtip afin de pouvoir utiliser plusieurs bases de données.

1. Ouvrez...\\Learning Modules\\ProvisionAndCatalog\\*Demo-ProvisionAndCatalog.ps1* dans *l’ISE PowerShell* et définissez le paramètre *$DemoScenario* sur 3 :
   * **$DemoScenario** = **3**. Changez sur **3** pour *Approvisionner un lot de clients*.
1. Appuyez sur **F5** pour exécuter le script.

Le script déploie un lot de locataires supplémentaires. Il utilise un [modèle Azure Resource Manager](../azure-resource-manager/resource-manager-template-walkthrough.md) qui contrôle le lot et délègue ensuite la configuration de chaque base de données à un modèle lié. Utiliser des modèles de cette façon permet à Azure Resource Manager de répartir le processus d’approvisionnement pour votre script. Les modèles approvisionnent des bases de données en parallèle dans la mesure du possible, et gèrent les nouvelles tentatives au besoin, en optimisant le processus global. Comme le script est idempotent, en cas d’échec ou d’arrêt pour une raison quelconque, exécutez-le à nouveau.

### <a name="verify-the-batch-of-tenants-successfully-deployed"></a>Vérifier que le lot de locataires a été correctement déployé

* Ouvrez le serveur *tenants1* en accédant à votre liste de serveurs dans le [portail Azure](https://portal.azure.com), cliquez sur **bases de données SQL**et vérifiez que le lot de 17 bases de données supplémentaires est désormais dans la liste :

   ![liste de base de données](media/sql-database-saas-tutorial-provision-and-catalog/database-list.png)



## <a name="other-provisioning-patterns"></a>Autres modèles d’approvisionnement

Voici les autres modèles d’approvisionnement non inclus dans ce tutoriel :

**Pré-approvisionnement des bases de données.** Le modèle de pré-approvisionnement exploite le fait que les bases de données d’un pool élastique n’ajoutent pas de frais supplémentaires. La facturation concerne le pool élastique, pas les bases de données, et les bases de données inactives ne consomment aucune ressource. En pré-approvisionnant les bases de données d’un pool et en les allouant en cas de besoin, le délai d’intégration du client peut être considérablement réduit. Le nombre de bases de données pré-approvisionnées peut être ajusté en fonction des besoins pour conserver une mémoire tampon adaptée au taux d’approvisionnement prévu.

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

