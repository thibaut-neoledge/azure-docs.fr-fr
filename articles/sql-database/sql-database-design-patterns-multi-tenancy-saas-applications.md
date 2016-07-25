<properties
   pageTitle="Modèles de conception pour les applications SaaS mutualisées avec une base de données SQL Azure | Microsoft Azure" 
   description="Cet article décrit la configuration requise et les modèles d’architecture de données les plus courants pour les applications de base de données SaaS mutualisées s’exécutant dans un environnement cloud, ainsi que les compromis associés à ces modèles. Il explique également comment le service Base de données SQL Azure, avec ses outils et ses pools de base de données élastique, permet de satisfaire ces exigences sans aucun compromis."
   keywords=""
   services="sql-database"
   documentationCenter=""
   authors="carlrabeler"
   manager="jhubbard"
   editor=""/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="sqldb-design"
   ms.date="06/07/2016"
   ms.author="carlrab"/>

# Modèles de conception pour les applications SaaS mutualisées avec Base de données SQL Azure

Dans cet article, vous découvrirez la configuration requise et les modèles d’architecture de données les plus courants pour les applications de base de données SaaS (software as a service) mutualisées s’exécutant dans un environnement cloud, ainsi que les compromis associés à ces modèles. Il explique également comment le service Base de données SQL Azure, avec ses outils et ses pools de base de données élastique, permet de satisfaire ces exigences sans aucun compromis.

## Introduction

Forts de notre collaboration avec les fournisseurs d’applications SaaS au cours de ces dernières années, nous constatons que les développeurs tablent sur le long terme lors de la conception des modèles d’architecture pour les couches de données de leurs applications mutualisées. Au moins dans un premier temps, la facilité de développement et les faibles coûts des fournisseurs de cloud sont considérés comme plus importants que l’isolation des locataires ou l’évolutivité des applications. Ce qui conduit souvent à des problèmes de satisfaction client et à des corrections ultérieures onéreuses.

Dans cet article, une application mutualisée désigne une application hébergée dans un environnement cloud, qui fournit le même ensemble de services à plusieurs (centaines ou milliers de) locataires qui ne partagent pas ou ne voient pas les données d’autrui. Typiquement, ce service propose une application SaaS fournissant des services à ses locataires dans un environnement cloud hébergé.

## Applications mutualisées

Les applications mutualisées illustrent parfaitement les applications dont les données et les charges de travail peuvent être aisément partitionnées. Par exemple, avec les applications mutualisées, les données et la charge de travail peuvent généralement être partitionnées en fonction des limites du locataire, étant donné que la plupart des demandes sont définies dans ce cadre. Cette propriété est inhérente aux données et à la charge de travail, et elle favorise les modèles d’applications abordés dans la suite de l’article.

Nous avons trouvé des applications de ce type dans toute la gamme d’applications basées sur le cloud, notamment :

- Les applications de base de données ISV effectuant une transition vers le cloud en tant qu’applications SaaS
- Les applications SaaS créées pour le cloud de A à Z
- Les applications orientées consommateur direct/utilisateur final
- Les applications d’entreprise orientées employé

Les applications SaaS du cloud et les applications SaaS incluses dans les applications de base de données ISV aboutissent généralement à des applications mutualisées. Ces applications SaaS offrent une application logicielle spécialisée en tant que service aux locataires. Les locataires ont accès au service d’application et à la pleine propriété des données associées qui sont stockées dans le cadre de l’application. Mais pour tirer parti des avantages d’une application SaaS, ses locataires doivent restituer un niveau de contrôle sur leurs propres données, en se fiant au fournisseur SaaS pour la conservation des données en lieu sûr et à l’écart des données des autres locataires. Exemples : MyOB, SnelStart, Salesforce, etc. Toutes ces applications permettent le partitionnement dans les limites des locataires et prennent donc en charge les modèles d’applications abordés dans les sections suivantes de cet article.

Les applications qui fournissent un service direct aux consommateurs ou aux employés au sein d’une organisation (souvent appelés utilisateurs, plutôt que locataires) forment une autre catégorie de la gamme d’applications mutualisées. Les clients s’abonnent au service et ne possèdent pas les données collectées et stockées par le fournisseur de services. Les fournisseurs de services ont des exigences réduites pour conserver les données de leurs clients à l’écart, qui s’ajoutent néanmoins aux réglementations de confidentialité imposées par les gouvernements. Netflix, Spotify, Xbox live, etc. sont des exemples classiques de fournisseurs de contenus médias. D’autres exemples d’applications facilement configurables en partition sont disponibles avec les applications Internet orientées client ou les applications IoT (Internet des objets), dans lesquelles chaque client ou appareil peut servir de partition et dans lesquelles des limites de partition peuvent être établies entre les différents utilisateurs ou appareils.

Nous reconnaissons toutefois que le partitionnement des applications n’est pas toujours simple pour une propriété unique (locataire, client, utilisateur, appareil, etc.). C’est notamment le cas des applications ERP (Enterprise Resource Planning) complexes impliquant des produits, des commandes et des clients. Ces applications affichent généralement un schéma complexe recensant des milliers de tables hautement interconnectées. Aucune stratégie de partition unique ne s’applique à l’ensemble des tables et ne fonctionne sur toute la charge de travail. Par conséquent, nous n’en parlerons pas dans la suite de cet article.

Les modèles de conception d’architecture mutualisée que nous explorons dans les sections ci-dessous s’appliquent à tous les modèles d’application ci-dessus qui présentent des charges de travail et des données facilement configurables en partition. Cependant, pour plus de simplicité, nous utiliserons le terme « applications SaaS mutualisées ».

## Compromis de conception des applications mutualisées

Les développeurs d’applications cloud mutualisées doivent tenir compte des points généraux suivants :

-	***Isolation des locataires*** : les développeurs doivent s’assurer qu’aucun locataire n’obtient un accès indésirable aux données des autres locataires. Cette exigence d’isolation s’étend à d’autres propriétés, comme la protection vis-à-vis des voisins bruyants, la possibilité de restaurer les données d’un locataire spécifique, les personnalisations du locataire, etc.
-	***Coût des ressources cloud*** : l’application SaaS doit être compétitive. Sur cette base, les applications SaaS des développeurs optimisent les coûts d’utilisation des ressources cloud (calcul, stockage, etc.) au moment de la conception d’applications mutualisées.
-	***Opérations de développement facilitées*** : les fournisseurs d’applications mutualisées ont besoin de créer une isolation protectrice, d’entretenir leurs applications et le schéma des bases de données, d’analyser leur intégrité et de résoudre les problèmes des locataires. La complexité du développement et du fonctionnement des applications se traduit directement par des coûts supplémentaires et une baisse de la satisfaction des locataires.
-	***Évolutivité*** : L’ajout incrémentiel de locataires est primordial pour un bon fonctionnement des applications SaaS, tout comme l’ajout de capacité pour les locataires individuels qui nécessitent plus de ressources.

Des compromis sont possibles. L’offre cloud la plus économique ne propose pas forcément l’expérience de développement la plus pratique. De nombreux développeurs font des choix hasardeux dans l’espace défini par les quatre points ci-dessus.

Concernant la gestion d’applications SaaS mutualisées dans d’autres entreprises, l’isolation des locataires représente souvent une exigence fondamentale. De nombreux développeurs se laissent tenter par les avantages apparents de simplicité et de coût, plutôt que par l’isolation des locataires et l’évolutivité. Ce compromis peut s’avérer complexe et coûteux lorsque le service se développe, et que les exigences d’isolation des locataires s’accentuent et doivent être traitées au niveau de la couche d’application.

Pour les applications mutualisées fournissant un service orienté consommateur direct aux utilisateurs finaux, l’isolation des locataires peut devenir moins prioritaire que l’optimisation du coût des ressources cloud.

Un modèle de développement courant consiste à regrouper plusieurs locataires dans une ou plusieurs bases de données. Les avantages (apparents) de cette approche sont les faibles coûts liés à l’acquisition de quelques bases de données, et la simplification des processus grâce à l’utilisation d’un nombre restreint de bases de données. Pour les applications SaaS mutualisées, ce type de décision apporte progressivement des inconvénients notables en matière d’isolation des locataires et d’évolutivité. Si l’isolation des locataires est requise, des efforts supplémentaires sont nécessaires pour protéger les données des locataires dans l’espace de stockage partagé contre tout accès non autorisé ou voisin bruyant, et ils peuvent améliorer considérablement le travail de développement des applications et les coûts de maintenance de l’isolation. De même, lors de l’ajout de locataires, une mise à l’échelle correcte de la couche de données de ce type d’application nécessite généralement des compétences de développement pour la redistribution des données des locataires entre les bases de données.

## Modèles de données d’architecture mutualisés 

Les pratiques de conception courantes pour placer les données des locataires suivent trois modèles distincts.


  ![modèles de données d’architecture mutualisés](./media/sql-database-design-patterns-multi-tenancy-saas-applications/sql-database-multi-tenant-data-models.png) Figure 1 : Pratiques de conception courantes pour les modèles de données mutualisés
  
1.	***Base de données par locataire*** : cette approche place chaque locataire dans sa propre base de données. Toutes les données spécifiques des locataires sont limitées à leur base de données. Elles sont isolées des autres locataires et de leurs données.
2.	***Base de données partagée - partitionnée*** : cette approche fait appel à plusieurs bases de données et plusieurs locataires qui partagent une base de données. Ainsi, un ensemble distinct de locataires est affecté à chaque base de données au moyen d’une stratégie de partitionnement, comme le hachage, la plage ou le partitionnement de liste. Cette stratégie de distribution des données est souvent appelée partitionnement.
3.	***Base de données partagée - unique*** : cette approche utilise une base de données unique, et parfois volumineuse, qui contient des données permettant d’éviter toute ambiguïté à propos des locataires grâce à une colonne d’ID de locataire.
  
> [AZURE.NOTE] Parfois, plusieurs locataires sont également placés dans différents schémas de base de données, et le nom du schéma permet de lever l’ambiguïté entre les différents locataires. Cette approche n’est pas recommandée, car elle requiert généralement l’utilisation de code SQL dynamique et elle n’optimise pas la mise en cache du plan. Par conséquent, la suite de cet article se concentre sur le concept de table partagée dans cette catégorie.
 
## Caractérisation des modèles de données mutualisés courants

Lors de l’évaluation de l’utilisation de ces modèles de données d’architecture mutualisés, il est important de tenir compte des compromis de conception des applications évoqués dans la section précédente.

-	***Isolation*** : niveau d’isolation entre les locataires permettant de mesurer l’isolation des locataires fournie par un modèle de données
-	***Coût des ressources cloud*** : partage de ressources entre les locataires permettant d’optimiser le coût des ressources cloud. Une ressource peut être définie en fonction du coût de calcul et de stockage.
-	***Coût des opérations de développement*** : la facilité de développement, de déploiement et de gestion des applications réduit le coût de fonctionnement global SaaS.

Nous pouvons ainsi caractériser les modèles de données d’architecture mutualisés décrits précédemment et l’utilisation des bases de données avec l’espace quadrant, comme indiqué dans la Figure 2 ci-dessous. Le degré d’isolation des locataires et le partage des ressources correspondent aux axes X et Y de l’espace. La grande flèche diagonale du milieu indique le coût des opérations de développement.

![modèles courants](./media/sql-database-design-patterns-multi-tenancy-saas-applications/sql-database-popular-application-patterns.png) Figure 2 : Caractérisation des modèles de données mutualisés courants

Le quadrant inférieur droit dans la Figure 2 ci-dessus montre comment utiliser une base de données unique potentiellement volumineuse avec l’approche de table partagée (ou un schéma distinct) pour représenter l’un des modèles d’application. Il est très utile pour le partage des ressources, car tous les locataires utilisent les mêmes ressources de base de données (processeur, mémoire et E/S) sur une base de données unique. Cependant, l’isolation des locataires est limitée. D’autres étapes doivent être ajoutées pour protéger les locataires les uns des autres au niveau de la couche d’application, ce qui peut augmenter considérablement le coût des opérations de développement dans le cadre du développement et de la gestion d’applications. En outre, l’évolutivité est limitée par la mise à l’échelle du matériel utilisé pour héberger la base de données.

La section du milieu illustre plusieurs locataires partitionnés entre plusieurs bases de données (généralement, unités d’échelle matérielles différentes), chaque base de données hébergeant un sous-ensemble de locataires qui résout le problème d’extensibilité du modèle précédent. Si une nouvelle capacité est requise pour d’autres locataires, ces derniers peuvent facilement être placés dans de nouvelles bases de données allouées à de nouvelles unités d’échelle matérielles. Cependant, le partage des ressources est réduit, car seuls les locataires placés sur les mêmes unités d’échelle partagent leurs ressources. En outre, cette approche offre peu d’améliorations en termes d’isolation des locataires, car de nombreux locataires se trouvent toujours dans les mêmes emplacements sans être automatiquement protégés des actions des autres. L’application reste très complexe. Cette approche est souvent appelée « partitionnement ».

Le quadrant supérieur gauche dans la Figure 2 ci-dessus constitue la troisième approche. Il place chaque locataire dans sa propre base de données. Cette approche affiche des propriétés d’isolation des locataires, mais elle permet peu de partage des ressources lorsque chaque base de données fournit ses propres ressources dédiées. Cette approche est très utile quand tous les locataires présentent des charges de travail prévisibles. Toutefois, dès que les charges de travail des locataires deviennent moins prévisibles (comme c’est souvent le cas pour SaaS), le partage des ressources ne peut pas être optimisé et le fournisseur doit surprovisionner les ressources pour répondre à la demande ou réduire le nombre de ressources, ce qui entraîne une hausse du coût des ressources ou une baisse de la satisfaction des locataires. Par conséquent, un plus grand partage des ressources entre les locataires est souhaitable pour que la solution soit plus économique. L’augmentation du nombre de bases de données augmente aussi le coût des opérations de développement dans le cadre du déploiement et de la maintenance des applications. Nous reviendrons à ces problèmes dans la section suivante, mais il est important de se rappeler que cette méthode fournit la meilleure et la plus simple des isolations pour les locataires.

Le choix du modèle de conception par le client est également influencé par ces facteurs supplémentaires :

-	***Propriété des données du locataire*** : si les locataires conservent la propriété de leurs données, cela favorise le modèle de base de données unique par locataire
-	***Mise à l’échelle*** : si l’application cible des centaines de milliers ou des millions de locataires, cela favorise les approches de partage des bases de données, comme le partitionnement, mais les exigences en termes d’isolation peuvent poser problème.
-	***Modèle de valeur et d’entreprise*** : en fonction du chiffre d’affaires des locataires si la valeur est très faible (moins d’un dollar), les exigences d’isolation deviennent moins critiques et il est plus logique de recourir à des bases de données partagées. Si la valeur atteint au moins quelques dollars, il devient possible de créer une base de données par locataire, et les coûts des opérations de développement d’application peuvent être réduits.

Étant donné les compromis cités ci-dessus dans la Figure 2, un modèle d’architecture mutualisé idéal doit incorporer d’excellentes propriétés d’isolation des locataires et un partage des ressources optimal entre les locataires, en s’intégrant par exemple dans le quadrant supérieur droit.

## Prise en charge d’une architecture mutualisée avec Base de données SQL Azure

Base de données SQL Azure prend en charge tous les modèles d’application mutualisés décrits dans la Figure 2. Avec les pools de base de données élastique, il prend aussi en charge un nouveau modèle d’application combinant les avantages du partage des ressources et de l’isolation dans l’approche de base de données par locataire (quadrant supérieur droit en vert dans la Figure 3 ci-dessous). Les outils de base de données élastique et les fonctionnalités de Base de données SQL Azure aident à limiter le coût des opérations de développement et d’exploitation d’une application avec plusieurs bases de données (comme indiqué par la zone ombrée dans la Figure 3 ci-dessous). Ces outils permettent de créer et de gérer des applications en adoptant l’un des modèles de bases de données multiples. Les sous-sections suivantes décrivent les fonctionnalités de Base de données SQL et expliquent l’utilité de Base de données SQL dans ces modèles d’architecture mutualisés.

![modèle Base de données SQL](./media/sql-database-design-patterns-multi-tenancy-saas-applications/sql-database-patterns-sqldb.png) Figure 3 : Modèles d’application mutualisés avec Base de données SQL Azure

## Modèle de base de données par locataire avec outils et pools élastiques

Base de données SQL Azure fournit des « pools de base de données élastique » pour une meilleure prise en charge de l’approche de base de données par locataire, qui combine l’isolation des locataires et le partage de ressources entre les bases de données des locataires. Il s’agit d’une solution de couche de données permettant aux fournisseurs SaaS de créer des applications mutualisées. Lorsqu’elle est combinée avec des offres d’outils de base de données élastique, l’architecture mutualisée est intégrée et la charge du partage des ressources entre les locataires passe de l’application à la couche de service de la base de données. La gestion et l’interrogation à l’échelle entre les bases de données deviennent plus simples avec les tâches, les requêtes et les transactions élastiques, ainsi que la bibliothèque cliente de base de données élastique.

| Exigences de l’application | Fonctionnalités de Base de données SQL |
| ------------------------ | ------------------------- |
| Isolation des locataires et partage des ressources | [Pools de base de données élastique :](sql-database-elastic-pool.md) cette fonctionnalité permet d’allouer un pool de ressources SQLDB et de partager ces ressources sur plusieurs bases de données. En outre, les bases de données individuelles peuvent tirer autant de ressources que nécessaire dans le pool en fonction des pics de besoins de capacité dus aux modifications des charges de travail des locataires, et le pool élastique peut lui-même varier en fonction des besoins. Les pools élastiques offrent aussi une grande facilité de gestion, de surveillance et de résolution des problèmes au niveau du pool. |
| Simplicité des opérations de développement entre bases de données | [Pools de base de données élastique :](sql-database-elastic-pool.md) comme indiqué ci-dessus.|
||[Requête élastique :](sql-database-elastic-query-horizontal-partitioning.md) permet d’interroger des bases de données à propos de la création de rapports ou de l’analyse entre locataires.|
||[Tâches élastiques :](sql-database-elastic-jobs-overview.md) permet de regrouper et de déployer correctement les opérations de maintenance de base de données ou les modifications de schéma de base de données pour plusieurs bases de données.|
||[Transactions élastiques :](sql-database-elastic-transactions-overview.md) permet de traiter les modifications dans plusieurs bases de données de manière atomique et isolée. Cette fonction est nécessaire lorsque les applications ont besoin de garanties « tout ou rien » sur plusieurs opérations de base de données. |
||[Bibliothèque cliente de base de données élastique :](sql-database-elastic-database-client-library.md) cette fonctionnalité permet de gérer les distributions de données et le mappage des locataires vers les bases de données. |
||||

## Modèles partagés

Comme indiqué précédemment, pour la plupart des fournisseurs SaaS de « modèle partagé », ces approches peuvent engendrer des problèmes d’isolation des locataires, mais aussi compliquer le développement et la maintenance des applications. Toutefois, pour que les applications mutualisées fournissent directement un service aux consommateurs finaux, les exigences d’isolation des locataires peuvent ne pas être aussi prioritaires que le désir d’optimiser les coûts. Il est possible de regrouper les locataires dans une ou plusieurs bases de données à une très haute densité afin de réduire les coûts. Les modèles de base de données partagée qui utilisent une base de données unique ou plusieurs bases de données partitionnées peuvent améliorer le partage des ressources et faire baisser les coûts globaux. Base de données SQL Azure fournit des fonctionnalités qui aideront ces clients à générer une isolation pour améliorer la sécurité et la gestion à grande échelle dans la couche de données.

| Exigences de l’application | Fonctionnalités de Base de données SQL |
| ------------------------ | ------------------------- |
| Fonctionnalités d’isolation pour la sécurité | [Sécurité au niveau des lignes](https://msdn.microsoft.com/library/dn765131.aspx) |
|| [Schéma de base de données](https://msdn.microsoft.com/library/dd207005.aspx) |
| Simplicité des opérations de développement entre bases de données | [Requête élastique](sql-database-elastic-query-horizontal-partitioning.md) |
|| [Tâches élastiques](sql-database-elastic-jobs-overview.md) |
|| [Transactions élastiques](sql-database-elastic-transactions-overview.md) |
|| [Bibliothèque cliente de base de données élastique](sql-database-elastic-database-client-library.md) |
|| [Fractionnement/fusion de base de données élastique](sql-database-elastic-scale-overview-split-and-merge.md) |
||||

## Conclusions

Les exigences en matière d’isolation des locataires sont très importantes pour la plupart des applications SaaS mutualisées. La meilleure solution s’appuie sur l’approche de base de données par locataire. Les deux autres approches nécessitent des investissements complexes au niveau de la couche d’application, et donc une équipe de développement spécialisée pour assurer l’isolation. Cela peut augmenter considérablement les coûts et les risques. Si les exigences d’isolation ne sont pas prises en considération suffisamment tôt dans le développement du service, une adaptation a posteriori des deux premiers modèles peut se révéler encore plus onéreuse. Les principaux inconvénients liés au modèle de base de données par locataire concernent l’augmentation du coût des ressources cloud due à la baisse du partage, ainsi qu’à la maintenance et à la gestion d’un grand nombre de bases de données. Les développeurs d’applications SaaS mènent souvent une lutte acharnée pour obtenir de tels compromis.

Si ces inconvénients peuvent constituer des obstacles majeurs pour la plupart des fournisseurs de services de base de données cloud, ce n’est pas le cas du service Base de données SQL Azure, avec son « pool de base de données élastique » et ses « fonctionnalités de base de données élastique ». Les développeurs SaaS peuvent combiner les caractéristiques d’isolation du modèle de base de données par locataire tout en optimisant le partage des ressources et la facilité de gestion d’un grand nombre de bases de données avec les pools élastiques et les outils associés.

Pour les fournisseurs d’applications mutualisées qui n’affichent aucune exigence en termes d’isolation des locataires et peuvent regrouper les locataires dans une base de données à très haute densité afin de réduire les coûts, les modèles de données « partagés » peuvent améliorer le partage des ressources et réduire le coût total. Les outils de base de données élastique de Base de données SQL Azure, les bibliothèques de partitionnement et les fonctionnalités de sécurité aident les fournisseurs SaaS à créer et à gérer ces applications mutualisées.

## Étapes suivantes

Pour obtenir un exemple d’application illustrant la bibliothèque cliente, consultez [Prise en main des outils de base de données élastique](sql-database-elastic-scale-get-started.md).

Pour visualiser un exemple d’application qui apporte une solution pour un scénario Softwware-as-a-Solution (SaaS) s’appuyant sur des pools élastiques afin d’obtenir une base de données principale économique et évolutive d’une application SaaS, consultez [Tableau de bord personnalisé du pool élastique pour SaaS](https://github.com/Microsoft/sql-server-samples/tree/master/samples/manage/azure-sql-db-elastic-pools-custom-dashboard).

Pour convertir des bases de données existantes afin d’utiliser les outils, consultez [Migrer des bases de données existantes pour la montée en charge](sql-database-elastic-convert-to-use-elastic-tools.md).

Pour créer un nouveau pool, consultez le [Didacticiel de création de pool élastique](sql-database-elastic-pool-create-portal.md).

Pour surveiller et gérer un pool élastique, consultez [Surveillance et gestion d’un pool de base de données élastique](sql-database-elastic-pool-manage-portal.md).

## Ressources supplémentaires

- [Qu’est-ce qu’un pool de base de données élastique ?](sql-database-elastic-pool.md)
- [Montée en charge avec Base de données SQL Azure](sql-database-elastic-scale-introduction.md)
- [Applications multi-locataires avec des outils de base de données élastique et la sécurité au niveau des lignes](sql-database-elastic-tools-multi-tenant-row-level-security.md)
- [Authentification sur les applications mutualisées, avec Azure AD et OpenID Connect](../guidance/guidance-multitenant-identity-authenticate.md)
- [Application Tailspin Surveys](../guidance/guidance-multitenant-identity-tailspin.md)
- [Démarrages rapides de solutions](sql-database-solution-quick-starts.md)

## Questions et demandes de fonctionnalités

Pour toute question, contactez-nous sur le [forum Base de données SQL](http://social.msdn.microsoft.com/forums/azure/home?forum=ssdsgetstarted) et formulez vos demandes de fonctionnalités éventuelles sur le [forum de commentaires Base de données SQL](https://feedback.azure.com/forums/217321-sql-database/).









	

<!---HONumber=AcomDC_0713_2016-->