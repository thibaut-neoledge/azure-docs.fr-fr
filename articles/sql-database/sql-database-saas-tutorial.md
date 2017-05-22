---
title: "Déployer et explorer une application SaaS qui utilise Azure SQL Database | Microsoft Docs"
description: "Déployer et explorer l’exemple d’application WTP Azure SQL Database"
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
ms.date: 05/10/2017
ms.author: billgib; sstein
ms.translationtype: Human Translation
ms.sourcegitcommit: fc4172b27b93a49c613eb915252895e845b96892
ms.openlocfilehash: 04859fb8a9f4a8bf04d92e7139d9202885c52503
ms.contentlocale: fr-fr
ms.lasthandoff: 05/12/2017


---
# <a name="deploy-and-explore-a-multi-tenant-saas-application-that-uses-azure-sql-database"></a>Déployer et explorer une application SaaS mutualisée qui utilise Azure SQL Database

Dans ce didacticiel, vous allez déployer et explorer l’application SaaS WTP. L’application utilise une base de données par locataire, un modèle d’application SaaS, pour traiter plusieurs locataires. L’application est conçue pour présenter les fonctionnalités d’Azure SQL Database qui autorisent des scénarios Saas, ainsi que des modèles de gestion et de conception SaaS.

Cinq minutes après avoir cliqué sur le bouton *Déployer sur Azure* ci-dessous, vous disposez d’une application SaaS mutualisée et fonctionnelle dans le cloud, utilisant SQL Database. L’application est déployée avec trois exemples de locataires, chacun avec sa propre base de données et tous déployés dans un pool élastique SQL. L’application est déployée sur votre abonnement Azure, ce qui vous donne un accès complet pour inspecter et utiliser les composants individuels de l’application.

Les scripts et le code source de l’application sont disponibles dans le référentiel GitHub [WingtipSaaS](https://github.com/Microsoft/WingtipSaaS).

Ce didacticiel vous apprend à effectuer les opérations suivantes :

> [!div class="checklist"]

> * Déployer l’application WTP
> * Explorer les serveurs, les pools et les bases de données qui composent l’application
> * Explorer les locataires qui sont mappés à leurs données avec le *catalogue*
> * Approvisionner les nouveaux locataires
> * Afficher l’utilisation du pool pour surveiller l’activité du locataire
> * Supprimer les exemples de ressources pour arrêter la facturation associée

Pour explorer les différents modèles de gestion et de conception SaaS, une [série de didacticiels associés](sql-database-wtp-overview.md) reposant sur ce déploiement initial est disponible. Tout en parcourant les didacticiels, découvrez plus en détail les scripts fournis et examinez la façon dont les différents modèles de SaaS sont implémentés. Parcourez les scripts de chaque didacticiel pour mieux comprendre comment implémenter les nombreuses fonctionnalités SQL Database qui simplifient le développement des applications SaaS.

Pour suivre ce didacticiel, vérifiez que les conditions préalables ci-dessous sont bien satisfaites :

* Azure PowerShell est installé. Pour plus d’informations, consultez [Bien démarrer avec Azure PowerShell](https://docs.microsoft.com/powershell/azure/get-started-azureps).

## <a name="deploy-the-wingtip-tickets-wtp-saas-application"></a>Déployer l’application SaaS WTP

Déployer l’application WTP en moins de cinq minutes :

1. Cliquez pour procéder au déploiement :

   <a href="http://aka.ms/deploywtpapp" target="_blank"><img src="http://azuredeploy.net/deploybutton.png"/></a>

1. Entrez les valeurs de paramètre requises pour le déploiement :

    > [!IMPORTANT]
    > Certaines authentifications et pare-feu de serveur sont volontairement non sécurisés à des fins de démonstration. **Créez un groupe de ressources** plutôt que d’utiliser des groupes de ressources, des serveurs ou des pools existants, et n’utilisez pas cette application, ou les ressources qu’elle crée, pour la production. Supprimez ce groupe de ressources lorsque vous en avez terminé avec l’application pour interrompre la facturation associée.

    * **Groupe de ressources** : sélectionnez **Créer un nouveau** et indiquez un **nom** et un **emplacement**.
    * **Utilisateur** : certaines ressources requièrent des noms uniques parmi tous les abonnements Azure. Pour garantir l’unicité, fournissez une valeur pour différencier les ressources que vous créez des ressources créées par d’autres utilisateurs déployant l’application WTP. Nous vous recommandons d’utiliser un nom **Utilisateur** court, tel que vos initiales plus un chiffre (par exemple, *bg1*) et de l’utiliser ensuite dans le nom du groupe de ressources (par exemple, *wingtip-bg1*). Le paramètre **Utilisateur** peut contenir uniquement des lettres, des chiffres et des traits d’union. Le premier et le dernier caractère doivent être une lettre ou un chiffre (l’utilisation de minuscules est recommandée).

     ![template](./media/sql-database-saas-tutorial/template.png)

1. **Déployez l’application**.

    * Cliquez si vous acceptez les termes et conditions.
    * Sélectionnez **Épingler au tableau de bord**.
    * Cliquez sur **Achat**.

1. Surveiller l’état du déploiement en cliquant sur **Notifications** (l’icône représentant une cloche à droite de la zone de recherche). Le déploiement de l’application WTP prend quatre minutes environ.

   ![déploiement réussi](media/sql-database-saas-tutorial/succeeded.png)

## <a name="explore-the-application"></a>Exploration de l'application

L’application présente des lieux, telles que des salles de concert, des clubs de jazz, des salles de sport, etc. qui accueillent des événements. Les lieux sont inscrits en tant que clients (ou locataires) de l’application WTP pour répertorier facilement les événements et vendre des billets. Chaque lieu obtient une application web personnalisée pour gérer et répertorier ses événements, ainsi que pour vendre des billets indépendamment des autres locataires. En réalité, chaque locataire obtient une base de données SQL qui est déployée dans un pool élastique SQL.

Un **concentrateur d’événements** central fournit une liste d’URL de locataires spécifiques à votre déploiement. Toutes les URL de locataires incluent votre propre valeur *Utilisateur* et respectent le format suivant : http://events.wtp.&lt;UTILISATEUR&gt;.trafficmanager.net/*fabrikamjazzclub*. 

1. Ouvrez le _concentrateur d’événements_ : http://events.wtp.&lt;UTILISATEUR&gt;.trafficmanager.net (remplacez le terme entre chevrons par votre nom Utilisateur) :

    ![concentrateur d’événements](media/sql-database-saas-tutorial/events-hub.png)

1. Cliquez sur **Fabrikam Jazz Club** dans le *concentrateur d’événements*.

   ![Événements](./media/sql-database-saas-tutorial/fabrikam.png)

1. Cliquez sur **Billets** et découvrez comment acheter un billet pour un événement.

L’application WTP utilise [*Azure Traffic Manager*](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-overview) pour contrôler la distribution du trafic entrant. Les pages d’événements, qui sont spécifiques au locataire, requièrent que les noms des locataires soient inclus dans les URL. L’application d’événements analyse le nom du locataire à partir de l’URL et l’utilise pour créer une clé permettant d’accéder à un catalogue utilisant la [*gestion des cartes de partitions*](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-scale-shard-map-management). Le catalogue mappe la clé à l’emplacement de la base de données du locataire. Le **concentrateur d’événements** utilise les métadonnées étendues dans le catalogue pour récupérer le nom du locataire associé à chaque base de données.

Dans un environnement de production, vous créez généralement un enregistrement DNS CNAME pour [*pointer un domaine Internet d’entreprise*](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-point-internet-domain) vers le profil Traffic Manager.

## <a name="get-the-wingtip-application-scripts"></a>Obtenir les scripts de l’application WTP

Les scripts et le code source de l’application Wingtip Tickets sont disponibles dans le référentiel GitHub [WingtipSaaS](https://github.com/Microsoft/WingtipSaaS). Les fichiers de script se trouvent dans le [dossier Learning Modules](https://github.com/Microsoft/WingtipSaaS/tree/master/Learning%20Modules). Téléchargez le dossier **Learning Modules** en local sur votre ordinateur, tout en conservant l’arborescence.

## <a name="provision-a-new-tenant"></a>Approvisionner un nouveau locataire

Le déploiement initial crée trois exemples de locataires, mais nous devons créer davantage de locataires pour obtenir la meilleure expérience de didacticiel. Lors de cette étape, vous allez créer rapidement un locataire. Plus tard, vous allez vous intéresser aux détails de l’approvisionnement des nouveaux locataires dans le [didacticiel Provision new tenants and register them in the catalog](sql-database-saas-tutorial-provision-and-catalog.md) (Approvisionner de nouveaux locataires et les inscrire dans le catalogue), où vous découvrirez la facilité avec laquelle vous pouvez implémenter un composant d’inscription dans l’application et approvisionner automatiquement les locataires à mesure de l’inscription des clients.

### <a name="initialize-the-user-config-file-for-your-deployment"></a>Initialiser le fichier de configuration utilisateur pour votre déploiement

Puisqu’il s’agit du premier script que vous allez exécuter après le déploiement initial de l’application WTP, vous devez mettre à jour le fichier de configuration utilisateur. Mettez à jour les variables en les remplaçant par les valeurs spécifiques de votre déploiement.

   1. Ouvrez ...\\Learning Modules\\*UserConfig.psm1* dans *PowerShell ISE*.
   1. Remplacez _$userConfig.ResourceGroupName_ par le _groupe de ressources_ que vous avez défini lors du déploiement de l’application.
   1. Remplacez _$userConfig.Name_ par le nom _Utilisateur_ que vous avez défini lors du déploiement de l’application.

### <a name="provision-the-new-tenant"></a>Approvisionner le nouveau locataire

1. Ouvrez ...\\Learning Modules\Provision and Catalog\\*Demo-ProvisionAndCatalog.ps1* dans *PowerShell ISE*.
1. Appuyez sur **F5** pour exécuter le script (conservez les valeurs par défaut du script pour l’instant).

Le nouveau locataire est inscrit dans le catalogue. Sa base de données est créée et ajoutée à un pool élastique SQL. Après l’approvisionnement, le site de vente de billets du nouveau locataire apparaît dans votre navigateur :

![Nouveau locataire](./media/sql-database-saas-tutorial/red-maple-racing.png)

Actualisez le concentrateur d’événements et vérifiez que le nouveau locataire est dans la liste.

## <a name="start-generating-load-on-the-tenant-databases"></a>Démarrer la génération de charge sur les bases de données de locataires

Maintenant que nous avons plusieurs bases de données de locataires, utilisons-les ! Un script PowerShell simulant une charge de travail s’exécutant sur toutes les bases de données de locataires est fourni. La charge s’exécute pendant 60 minutes par défaut. WTP est une application SaaS, et dans le monde réel, la charge se trouvant sur une application SaaS est généralement sporadique et imprévisible. Pour simuler cette situation, le générateur de charge produit une charge aléatoire répartie sur tous les locataires. Plusieurs minutes sont nécessaires pour que le modèle émerge, alors laissez le générateur de charge s’exécuter pendant environ 5 à 10 minutes avant d’essayer de surveiller la charge dans les sections suivantes.

1. Ouvrez ...\\Learning Modules\\Utilities\\*Demo-LoadGenerator.psm1* dans **PowerShell ISE**.
1. Appuyez sur **F5** pour exécuter le script et démarrer le générateur de charge (laissez les valeurs de paramètre par défaut pour l’instant).

> [!IMPORTANT]
> Le générateur de charge exécute une série de travaux dans votre session PowerShell locale, alors laissez-la ouverte ! Si vous fermez PowerShell ou l’onglet dans lequel le générateur de charge a été démarré, ou si vous arrêtez votre ordinateur, les travaux s’arrêteront.


## <a name="explore-the-servers-pools-and-tenant-databases"></a>Explorer les serveurs, les pools et les bases de données de locataires

Maintenant que vous avez exploré l’application, approvisionné un nouveau locataire et commencé à exécuter une charge sur la collection de locataires, intéressons-nous à certaines des ressources qui ont été déployées.

1. Dans le [portail Azure](http://portal.azure.com), ouvrez le serveur **catalog-&lt;UTILISATEUR&gt;**. Le serveur de catalogue contient deux bases de données : **tenantcatalog** et **basetenantdb** (une base de données *finale* vide qui est copiée pour créer des locataires).

   ![bases de données](./media/sql-database-saas-tutorial/databases.png)

1. Ouvrez le serveur **tenants1-&lt;UTILISATEUR&gt;** qui contient les bases de données de locataires. Notez que chaque base de données de locataires est une base de données _élastique Standard_ dans un pool Standard de 50 eDTU. Notez également qu’il existe une base de données _Red Maple Racing_, qui correspond au locataire que vous avez approvisionné précédemment.

   ![server](./media/sql-database-saas-tutorial/server.png)

## <a name="monitor-the-pool"></a>Surveiller le pool

Si le générateur de charge s’exécute depuis plusieurs minutes, suffisamment de données doivent être disponibles pour commencer à rechercher certaines des fonctionnalités de surveillance intégrées aux pools et bases de données.

1. Accédez au serveur *tenants1-&lt;UTILISATEUR&gt;*, puis cliquez sur **Pool1** pour afficher l’utilisation des ressources du pool :

   ![surveiller un pool](./media/sql-database-saas-tutorial/monitor-pool.png)

Ces deux graphiques montrent bien que les pools élastiques et SQL Database sont parfaitement adaptés aux charges de travail de l’application SaaS. Quatre bases de données qui migrent chacune jusqu’à 40 eDTU sont facilement prises en charge dans un pool de 50 eDTU. Si chacune d’entre elles a été configurée en tant que base de données autonome, elles auront besoin d’un S2 (50 DTU) pour prendre en charge les migrations, mais le coût de 4 bases de données S2 autonomes serait égal à trois fois le prix du pool. En outre, il reste beaucoup d’espace disponible dans le pool pour accueillir davantage de bases de données. En situation réelle, les locataires exécutent actuellement jusqu’à 500 bases de données dans des pools de 200 eDTU. Pour plus d’informations, consultez le [didacticiel Monitor performance of the WTP sample SaaS application](sql-database-saas-tutorial-performance-monitoring.md) (Surveiller les performances de l’exemple d’application SaaS WTP).


## <a name="deleting-the-resources-created-with-this-tutorial"></a>Suppression des ressources créées lors de ce didacticiel

Lorsque vous avez terminé d’explorer et d’utiliser l’application WTP, accédez au groupe de ressources de l’application dans le portail et supprimez-le pour arrêter toute facturation associée à ce déploiement. Si vous avez suivi l’un des didacticiels associés à celui-ci, toutes les ressources crées seront également supprimées avec l’application.


## <a name="next-steps"></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à effectuer les opérations suivantes :

> [!div class="checklist"]

> * Déployer l’application WTP
> * Explorer les serveurs, les pools et les bases de données qui composent l’application
> * Explorer les locataires qui sont mappés à leurs données avec le *catalogue*
> * Approvisionner les nouveaux locataires
> * Afficher l’utilisation du pool pour surveiller l’activité du locataire
> * Supprimer les exemples de ressources pour arrêter la facturation associée

Essayez maintenant le [didacticiel Provision new tenants and register them in the catalog](sql-database-saas-tutorial-provision-and-catalog.md) (Approvisionner de nouveaux locataires et les inscrire dans le catalogue).



## <a name="additional-resources"></a>Ressources supplémentaires

* [Autres didacticiels reposant sur le déploiement initial de l’application WTP](sql-database-wtp-overview.md#sql-database-wtp-saas-tutorials).
* Pour de plus amples informations sur les pools élastiques, consultez [*Les pools élastiques vous aident à gérer et à mettre à l’échelle plusieurs bases de données SQL*](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-pool).
* Pour de plus amples informations sur les travaux élastiques, consultez [*Gestion des bases de données cloud avec montée en charge*](https://docs.microsoft.com/azure/sql-database/sql-database-elastic-jobs-overview).
* Pour de plus amples informations sur les applications SaaS mutualisées, consultez [*Modèles de conception pour les applications SaaS mutualisées et Base de données SQL Azure*](https://docs.microsoft.com/azure/sql-database/sql-database-design-patterns-multi-tenancy-saas-applications).

