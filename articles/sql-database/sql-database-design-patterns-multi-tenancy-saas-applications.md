---
title: "Modèles de conception pour les applications SaaS multilocataires et Azure SQL Database | Microsoft Docs"
description: "Découvrez les exigences et les modèles d’architecture de données les plus courants pour les applications de base de données SaaS (Software as a Service) multilocataires qui s’exécutent dans un environnement cloud."
keywords: 
services: sql-database
documentationcenter: 
author: srinia
manager: jhubbard
editor: 
ms.assetid: 1dd20c6b-ddbb-40ef-ad34-609d398d008a
ms.service: sql-database
ms.custom: scale out apps
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: Active
ms.date: 02/01/2017
ms.author: srinia
ms.openlocfilehash: eef48cfcbc7d6c241b5ece863df0be6ecad78ca7
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/31/2017
---
# <a name="design-patterns-for-multi-tenant-saas-applications-and-azure-sql-database"></a>Modèles de conception pour les applications SaaS multilocataires et Azure SQL Database
Cet article vous fournit des informations sur la configuration requise et les modèles d’architecture de données les plus courants pour les applications de base de données SaaS (Software as a Service) multilocataires s’exécutant dans un environnement cloud. Il explique également les facteurs à prendre en compte et les compromis en fonction de chaque modèle de conception. Les pools élastiques et les outils élastiques dans Azure SQL Database peuvent vous aider à répondre à des besoins spécifiques sans compromettre d’autres objectifs.

Les développeurs qui conçoivent des modèles de location pour les couches de données d’applications multilocataires prennent parfois des décisions qui vont à l’encontre de leurs intérêts à long terme. Il se peut que dans un premier temps le développeur perçoive la facilité de déploiement et le coût réduit du fournisseur de service cloud comme plus important que l’isolation des locataires ou l’évolutivité d’une application. Ce choix peut donner lieu à des problèmes de satisfaction du client doublés d’une correction ultérieure coûteuse.

Une application multilocataire désigne une application hébergée dans un environnement cloud, qui fournit le même ensemble de services à des centaines ou milliers de locataires qui ne partagent pas ou ne voient pas les données d’autrui. Il peut par exemple s’agir d’une application SaaS qui fournit des services aux locataires dans un environnement hébergé dans le cloud.

## <a name="multi-tenant-applications"></a>Applications multilocataires
Dans les applications multilocataires, il est aisé de partitionner les données et la charge de travail. Vous pouvez par exemple le faire en fonction des limites de chaque locataire, dans la mesure où la plupart des requêtes sont exécutées dans les limites d’un seul locataire. Cette propriété est inhérente dans les données et la charge de travail. Elle favorise les modèles d’applications décrits dans cet article.

Les développeurs utilisent ce type d’applications dans toute la gamme d’applications basées sur le cloud, notamment :

* Les applications de base de données partenaires qui sont migrées vers le cloud en tant qu’applications SaaS
* Les applications SaaS créées dès le départ pour le cloud
* Les applications directes orientées client
* Les applications d’entreprise orientées employé

Les applications SaaS conçues pour le cloud et celles avec des racines comme applications de base de données partenaires sont généralement des applications multilocataires. Ces applications SaaS offrent une application logicielle spécialisée en tant que service aux locataires. Les locataires peuvent accéder au service d’applications et ont la pleine propriété des données associées stockées dans le cadre de l’application. Toutefois, pour tirer parti des avantages du SaaS, les locataires doivent renoncer à une partie du contrôle de leurs propres données. Ils font confiance au fournisseur de services SaaS pour protéger leurs données et les isoler des données des autres locataires. MYOB, SnelStart et Salesforce.com sont des exemples de ce type d’applications SaaS multilocataires. Chacune de ces applications peut être partitionnée en respectant les limites de chaque locataire et prendre en charge les modèles de conception d’application présentés dans cet article.

Les applications qui fournissent un service direct aux clients ou aux employés au sein d’une organisation (souvent appelés utilisateurs, plutôt que locataires) forment une autre catégorie de la gamme d’applications multilocataires. Les clients s’abonnent au service et ne possèdent pas les données collectées et stockées par le fournisseur de services. Les fournisseurs de services ont des exigences réduites pour conserver les données de leurs clients à l’écart, en dehors des réglementations de confidentialité imposées par les gouvernements. Les fournisseurs de contenus multimédias comme Netflix, Spotify et Xbox LIVE sont des exemples de ce type d’applications multilocataires orientées client. Il existe d’autres exemples d’applications facilement configurables en partition comme les applications Internet orientées client ou les applications IoT (Internet des objets), dans lesquelles chaque client ou appareil peut servir de partition. Les limites de partition peuvent séparer les utilisateurs et les appareils.

Le partitionnement des applications n’est pas toujours simple pour une propriété unique (locataire, client, utilisateur, appareil, etc.). Une application ERP complexe, par exemple, traite des produits, des commandes et des clients. Elle suit généralement un schéma complexe avec des milliers de tables hautement interconnectées.

Aucune stratégie de partition unique ne s’applique à l’ensemble des tables et ne fonctionne sur une charge de travail complète. Cet article se concentre sur les applications multilocataires comportant des charges de travail et des données facilement configurables en partition.

## <a name="multi-tenant-application-design-trade-offs"></a>Compromis de conception des applications multilocataires
Le modèle de conception choisi par un développeur d’applications multilocataires se base généralement sur la prise en compte des facteurs suivants :

* **Isolation des locataires**. Le développeur doit s’assurer qu’aucun locataire ne dispose d’une accès non autorisé aux données des autres locataires. Cette exigence d’isolation s’étend à d’autres propriétés, comme la fourniture d’une protection vis-à-vis des voisins bruyants, la possibilité de restaurer les données d’un locataire et la mise en œuvre des personnalisations spécifiques à un locataire.
* **Coût des ressources du cloud**. Une application SaaS doit être compétitive. Un développeur d’applications multilocataires peut choisir l’optimisation pour réduire le coût de l’utilisation des ressources cloud, comme les coûts de stockage et de traitement.
* **Convivialité DevOps**. Un développeur d’applications multilocataires doit intégrer la protection par l’isolation, gérer et surveiller l’intégrité de son schéma de base de données et d’application, et résoudre les problèmes des locataires. La complexité du développement et du fonctionnement des applications se traduit directement par des coûts supplémentaires et une difficulté à maintenir la satisfaction des locataires.
* **Extensibilité**. Pour les locataires qui l’exigent, la possibilité d’ajouter des locataires et des capacités supplémentaires est indispensable à un bon fonctionnement du SaaS.

Chacun de ces facteurs implique des compromis par rapport aux autres. L’offre cloud la plus économique ne permet pas forcément de vivre l’expérience de développement la plus pratique. Il est important pour un développeur de prendre des décisions éclairées concernant ces options et leurs compromis pendant le processus de conception d’application.

Un modèle de développement courant consiste à regrouper plusieurs locataires dans une ou plusieurs bases de données. Les avantages de cette approche sont le coût réduit (vous payez pour un petit nombre de bases de données) et la simplicité relative de l’utilisation d’un nombre limité de bases de données. Toutefois, au fil du temps, un développeur d’applications multilocataires SaaS réalisera que ce choix présente des inconvénients substantiels pour l’isolation des locataires et la scalabilité. Si l’isolation des locataires devient importante, il faut faire une effort supplémentaire pour protéger les données des locataires contre les accès non autorisés ou les voisins bruyants sur un stockage partagé. Cet effort supplémentaire peut augmenter considérablement les efforts de développement et les coûts de maintenance de l’isolation. De même, si l’ajout de locataires est requis, ce modèle de conception requiert généralement une certaine expertise pour redistribuer les données des locataires entre les bases de données en vue de mettre correctement à l’échelle la couche données d’une application.  

L’isolation des locataires est généralement une exigence fondamentale pour les applications multilocataires SaaS destinées aux entreprises et aux organisations. Un développeur peut se laisser tenter par les avantages apparents liés à la simplicité et au coût, au lieu de privilégier l’isolation des locataires et l’évolutivité. Ce compromis peut s’avérer complexe et coûteux lorsque le service se développe, et que les exigences d’isolation des locataires s’accentuent et doivent être gérées au niveau de la couche d’application. Toutefois, dans les applications multilocataires fournissant un service direct orienté utilisateur aux clients, l’isolation des locataires peut devenir moins prioritaire que l’optimisation pour réduire le coût des ressources cloud.

## <a name="multi-tenant-data-models"></a>Modèles de données multilocataires
Les pratiques de conception courantes pour placer les données des locataires suivent trois modèles distincts illustrés dans la Figure 1.

![modèles de données d’applications multilocataires](./media/sql-database-design-patterns-multi-tenancy-saas-applications/sql-database-multi-tenant-data-models.png)

Figure 1 : Pratiques de conception courantes pour les modèles de données multilocataires

* **Base de données par locataire**. Chaque locataire a sa propre base de données. Toutes les données spécifiques des locataires sont limitées à leur base de données. Elles sont isolées des autres locataires et de leurs données.
* **Base de données partagée partitionnée**. Plusieurs locataires partagent l’une des différentes bases de données. Un ensemble distinct de clients est attribué à chaque base de données à l’aide d’une stratégie de partitionnement telle que le hachage, la plage ou le partitionnement de liste. Cette stratégie de distribution des données est souvent appelée partitionnement.
* **Base de données partagée unique**. Une base de données unique et parfois volumineuse contient des données permettant d’éviter toute ambiguïté à propos des locataires dans une colonne d’ID de locataire.

> [!NOTE]
> Un développeur d’applications peut choisir de placer plusieurs locataires dans des schémas de base de données différents, puis d’utiliser le nom de schéma pour enlever toute ambiguïté concernant les différents locataires. Nous ne recommandons pas cette approche car elle requiert généralement l’utilisation du SQL dynamique, et elle ne peut pas être efficace pour le caching de plan. Le reste de cet article est consacré à l’approche de la table partagée pour cette catégorie d’application multilocataire.
> 
> 

## <a name="popular-multi-tenant-data-models"></a>Modèles de données multilocataires courants
Il est important d’évaluer les différents types de modèles de données multilocataires en fonction des compromis de conception d’application que nous avons déjà identifiés. Ces facteurs permettent de caractériser les trois modèles de données multilocataires les plus courants décrits précédemment et leur utilisation des bases de données, comme illustré dans la Figure 2.

* **Isolement**. Niveau d’isolation entre les locataires permettant de mesurer l’isolation des locataires fournie par un modèle de données.
* **Coût des ressources du cloud**. La quantité de partage de ressources entre les clients peut optimiser le coût des ressources du cloud. Une ressource peut être définie en fonction du coût de calcul et de stockage.
* **Coût DevOps**. La facilité de développement, de déploiement et de gestion des applications réduit le coût de fonctionnement global du SaaS.  

Dans la Figure 2, l’axe Y indique le niveau d’isolation des locataires. L’axe X indique le niveau de partage des ressources. La flèche diagonale grise au milieu indique l’évolution des coûts DevOps, à la hausse ou à la baisse.

![Modèles courants de conception d’applications multilocataires](./media/sql-database-design-patterns-multi-tenancy-saas-applications/sql-database-popular-application-patterns.png)

Figure 2 : Modèles de données multilocataires courants

Dans la Figure 2, le quadrant inférieur droit montre un modèle d’application qui utilise une base de données unique partagée potentiellement volumineuse et l’approche de la table partagée (ou du schéma distinct). C’est une bonne chose pour le partage de ressources, car tous les locataires utilisent les mêmes ressources de base de données (processeur, mémoire et entrée/sortie) dans une base de données unique. Cependant, l’isolation des locataires est limitée. Vous devrez peut-être prendre des mesures supplémentaires pour protéger les locataires les uns des autres au niveau de la couche application. Ces étapes supplémentaires peuvent augmenter considérablement le coût DevOps de développement et de gestion de l’application. L’évolutivité est limitée par la mise à l’échelle du matériel qui héberge la base de données.

Dans la Figure 2, le quadrant inférieur gauche illustre plusieurs locataires partitionnés entre plusieurs bases de données (en général, différentes unités d’échelle matérielles). Chaque base de données héberge un sous-ensemble de locataires, ce qui résout le problème d’évolutivité d’autres modèles. Si une capacité supplémentaire est requise pour d’autres locataires, vous pouvez placer ces derniers dans de nouvelles bases de données allouées à de nouvelles unités d’échelle matérielles. Toutefois, la quantité de partage des ressources est réduite. Seuls les locataires placés sur des unités d’échelle identiques partagent des ressources. Cette approche offre peu d’améliorations en termes d’isolation des locataires, car de nombreux locataires partagent toujours les mêmes emplacements sans être automatiquement protégés des actions des autres. L’application reste très complexe.

Dans la Figure 2, le quadrant supérieur gauche constitue la troisième approche. Il place les données de chaque locataire dans sa propre base de données. Cette approche présente de bonnes propriétés d’isolation des locataires, mais elle limite le partage des ressources lorsque chaque base de données a ses propres ressources dédiées. Il s’agit d’une bonne approche si tous les locataires ont des charges de travail prévisibles. Si les charges de travail du locataire deviennent moins prévisibles, le fournisseur ne peut pas optimiser le partage des ressources. L’imprévisibilité est courante pour les applications SaaS. Le fournisseur doit surprovisionner pour répondre aux demandes soit diminuer les ressources. Ces actions se traduisent soit par une augmentation des coûts, soit par une diminution de la satisfaction des locataires. Un plus grand partage des ressources entre les locataires est souhaitable pour que la solution soit plus économique. L’augmentation du nombre de bases de données augmente aussi le coût DevOps de déploiement et de maintenance de l’application. Malgré ces problèmes, cette méthode fournit l’isolation la plus performante et la plus simple pour les locataires.

Ces facteurs influencent également le modèle de conception choisi par un client :

* **Propriété des données des locataires**. Une application qui permet aux locataires de conserver la propriété de leurs propres données favorise le modèle d’une base de données unique par locataire.
* **Mettant à l’échelle**. Une application qui cible des centaines de milliers ou des millions de clients favorise les approches de partage des bases de données comme le partitionnement. Les exigences d’isolation peuvent encore poser des problèmes.
* **Modèle de valeur et d’entreprise**. Si le chiffre d’affaires par locataire d’une application est faible (moins d’un dollar), les exigences d’isolation deviennent moins critiques et il est plus logique de recourir à des bases de données partagées. Si le chiffre d’affaires par locataire est de quelques dollars ou plus, il est préférable d’utiliser un modèle de base de données par locataire. Cela peut contribuer à réduire les coûts de développement.

Selon les compromis de conception indiqués dans la Figure 2, un modèle multilocataire idéal doit intégrer de bonnes propriétés d’isolation des locataires avec un partage optimal des ressources entre locataires. Il s’agit d’un modèle qui correspond à la catégorie décrite dans le quadrant supérieur droit de la Figure 2.

## <a name="multi-tenancy-support-in-azure-sql-database"></a>Prise en charge des modèles multilocataires dans Azure SQL Database
Azure SQL Database prend en charge tous les modèles d’applications multilocataires décrits dans la Figure 2. En plus des pools élastiques, il prend aussi en charge un modèle d’application combinant les avantages du partage des ressources et de l’isolation dans l’approche de base de données par locataire (voir le quadrant supérieur droit dans la Figure 3). Dans SQL Database, les outils et les fonctions de base de données élastiques peuvent contribuer à réduire le coût de développement et de fonctionnement d’une application qui comporte de nombreuses bases de données (comme indiqué par la zone ombrée dans la Figure 3). Ces outils peuvent vous aider à créer et gérer des applications qui utilisent des modèles de bases de données multiples.

![Modèles dans Azure SQL Database](./media/sql-database-design-patterns-multi-tenancy-saas-applications/sql-database-patterns-sqldb.png)

Figure 3 : Modèles d’applications multilocataires dans Azure SQL Database

## <a name="database-per-tenant-model-with-elastic-pools-and-tools"></a>Modèle de base de données par locataire avec outils et pools élastiques
Dans SQL Database, les pools de base de données élastiques combinent l’isolation des locataires et le partage de ressources entre les bases de données des locataires pour mieux prendre en charge l’approche de base de données par locataire. SQL Database est une solution de couche de données pour les fournisseurs SaaS qui créent des applications multilocataires. La charge du partage de ressources entre les locataires se déplace de la couche d’application à la couche de service de base de données. La gestion et l’interrogation à l’échelle entre les bases de données deviennent plus simples avec les tâches, les requêtes et les transactions élastiques, ainsi que la bibliothèque cliente de base de données élastique.

| Exigences de l’application | Fonctionnalités de Base de données SQL |
| --- | --- |
| Isolation des locataires et partage des ressources |[Pools élastiques](sql-database-elastic-pool.md): permettent d’allouer un pool de ressources Base de données SQL et de partager les ressources sur plusieurs bases de données. En outre, les bases de données individuelles peuvent tirer autant de ressources que nécessaire dans le pool en fonction des pics de besoins de capacité dus aux modifications des charges de travail des locataires. Le pool élastique peut lui-même varier en fonction des besoins. Les pools élastiques offrent aussi une grande facilité de gestion, de surveillance et de résolution des problèmes au niveau du pool. |
| Simplicité des opérations de développement entre bases de données |[Pools élastiques](sql-database-elastic-pool.md): comme indiqué ci-dessus. |
| | [Requête élastique](sql-database-elastic-query-horizontal-partitioning.md): permet d’interroger des bases de données pour la création de rapports ou l’analyse entre locataires. |
| | [Tâches élastiques](sql-database-elastic-jobs-overview.md): permettent de regrouper et de déployer correctement les opérations de maintenance de base de données ou les modifications de schéma de base de données pour plusieurs bases de données. |
| | [Transactions élastiques](sql-database-elastic-transactions-overview.md): permet de traiter les modifications dans plusieurs bases de données de manière atomique et isolée. Les transactions élastiques sont nécessaire lorsque des applications ont besoin de garanties « tout ou rien » sur plusieurs opérations de base de données. |
| | [Bibliothèque cliente de base de données élastique](sql-database-elastic-database-client-library.md): permet de gérer la distribution de données et de mapper les locataires sur les bases de données. |

## <a name="shared-models"></a>Modèles partagés
Comme indiqué précédemment, pour la plupart des fournisseurs SaaS une approche de modèle partagé peut engendrer des problèmes d’isolation des locataires, mais aussi compliquer le développement et la maintenance des applications. Toutefois, pour les applications multilocataires qui fournissent directement un service aux consommateurs, les exigences d’isolation des locataires peuvent ne pas être aussi prioritaires que le souhait de minimiser les coûts. Il est possible de regrouper les locataires dans une ou plusieurs bases de données haute densité afin de réduire les coûts. Les modèles de base de données partagée qui utilisent une base de données unique ou plusieurs bases de données partitionnées peuvent améliorer le partage des ressources et faire baisser les coûts globaux. Azure SQL Database fournit des fonctionnalités qui aident les clients à générer une isolation pour améliorer la sécurité et la gestion à grande échelle dans la couche de données.

| Exigences de l’application | Fonctionnalités de Base de données SQL |
| --- | --- |
| Fonctionnalités d’isolation pour la sécurité |[Sécurité au niveau des lignes](https://msdn.microsoft.com/library/dn765131.aspx) |
| [Schéma de base de données](https://msdn.microsoft.com/library/dd207005.aspx) | |
| Simplicité des opérations de développement entre bases de données |[Requête élastique](sql-database-elastic-query-horizontal-partitioning.md) |
| | [Tâches élastiques](sql-database-elastic-jobs-overview.md) |
| | [Transactions élastiques](sql-database-elastic-transactions-overview.md) |
| | [Bibliothèque cliente de base de données élastique](sql-database-elastic-database-client-library.md) |
| | [Fractionnement et fusion de base de données élastique](sql-database-elastic-scale-overview-split-and-merge.md) |

## <a name="summary"></a>Résumé
Les exigences en matière d’isolation des locataires sont importantes pour la plupart des applications multilocataires SaaS. Pour assurer l’isolation, la meilleure solution s’appuie sur l’approche de base de données par locataire. Les deux autres approches nécessitent des investissements dans des couches d’application complexes, et donc une équipe de développement spécialisée pour assurer l’isolation, ce qui augmente largement les coûts et les risques. Si les exigences d’isolation ne sont pas prises en considération suffisamment tôt dans le développement du service, une adaptation a posteriori des deux premiers modèles peut se révéler encore plus onéreuse. Les principaux inconvénients liés au modèle de base de données par locataire concernent l’augmentation du coût des ressources cloud due à la baisse du partage, ainsi qu’à la maintenance et à la gestion d’un grand nombre de bases de données. Les développeurs d’applications SaaS sont souvent bien embêtés de devoir faire ces compromis.

Si ces compromis peuvent constituer des obstacles majeurs pour la plupart des fournisseurs de services de base de données cloud, ce n’est pas le cas d’Azure SQL Database, avec son « pool élastique » et ses « fonctionnalités de base de données élastique ». Les développeurs SaaS peuvent combiner les caractéristiques d’isolation du modèle de base de données par locataire tout en optimisant le partage des ressources et la facilité de gestion d’un grand nombre de bases de données à l’aide de pools élastiques et d’outils associés.

Pour les fournisseurs d’applications multilocataires qui n’affichent aucune exigence en termes d’isolation des locataires et peuvent regrouper les locataires dans une base de données à haute densité, les modèles de données partagées offrent la possibilité d’améliorer le partage des ressources et de réduire le coût total. Les outils de bases de données élastiques Azure SQL Database, les bibliothèques de partitionnement et les fonctionnalités de sécurité aident les fournisseurs SaaS à créer et à gérer des applications multilocataires.

## <a name="next-steps"></a>Étapes suivantes
[Prenez en main les outils de base de données élastiques](sql-database-elastic-scale-get-started.md) avec un exemple d’application illustrant la bibliothèque cliente.

Créez un [tableau de bord personnalisé de pool élastique pour SaaS](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-sql-db-elastic-pools-custom-dashboard) avec un exemple d’application qui utilise des pools élastiques pour fournir une solution de base de données économique et évolutive.

Utilisez les outils d’Azure SQL Database pour [migrer les bases de données et augmenter la taille des instances](sql-database-elastic-convert-to-use-elastic-tools.md).

Pour créer un pool élastique avec le portail Microsoft Azure, consultez [Création d’un pool élastique](sql-database-elastic-pool-manage-portal.md).  

Découvrez comment [surveiller et gérer un pool élastique](sql-database-elastic-pool-manage-portal.md).

## <a name="additional-resources"></a>Ressources supplémentaires

* [Déployer et explorer une application multilocataire qui utilise Azure SQL Database - Wingtip SaaS](sql-database-saas-tutorial.md)
* [Qu’est-ce qu’un pool élastique Azure ?](sql-database-elastic-pool.md)
* [Montée en charge avec Azure SQL Database](sql-database-elastic-scale-introduction.md)
* [applications multilocataires avec des outils de bases de données élastiques et la sécurité au niveau des lignes](sql-database-elastic-tools-multi-tenant-row-level-security.md)
* [Authentification dans des applications multilocataires à l’aide d’Azure Active Directory et d’OpenID Connect](../guidance/guidance-multitenant-identity-authenticate.md)
* [Application Tailspin Surveys](../guidance/guidance-multitenant-identity-tailspin.md)


## <a name="questions-and-feature-requests"></a>Questions et demandes de fonctionnalités

Pour toute question, retrouvez-nous sur le [forum de Base de données SQL](http://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted). Ajoutez une demande de fonctionnalité dans le [forum de commentaires de Base de données SQL](https://feedback.azure.com/forums/217321-sql-database/).

