<properties
   pageTitle="Utilisation du magasin de requêtes dans la base de données SQL Azure"
   description="Découvrez comment utiliser le magasin de requêtes dans la base de données SQL Azure"
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
   ms.tgt_pltfrm="sqldb-performance"
   ms.workload="data-management"
   ms.date="05/25/2016"
   ms.author="carlrab"/>

# Utilisation du magasin de requêtes dans la base de données SQL Azure 

Le magasin de requêtes dans Azure est une fonctionnalité de base de données entièrement gérée qui recueille et présente des informations historiques détaillées sur toutes les requêtes. Le magasin de requêtes peut être comparé à l’enregistreur de données de vol d’un avion, qui simplifie considérablement le dépannage des problèmes de performances des requêtes à la fois pour les clients cloud et pour les clients locaux. Cet article présente certains aspects de l’utilisation du magasin de requêtes dans Azure. À l’aide des données de requête collectées au préalable, les utilisateurs peuvent diagnostiquer et résoudre rapidement les problèmes de performances, ce qui leur permet de consacrer davantage de temps à leurs activités.

Le magasin de requêtes est [disponible dans le monde entier](https://azure.microsoft.com/updates/general-availability-azure-sql-database-query-store/) via la base de données SQL Azure depuis novembre 2015. Le magasin de requêtes constitue la base de l’analyse des performances et de l’ajustement des fonctionnalités, comme [SQL Database Advisor et le tableau de bord des performances](https://azure.microsoft.com/updates/sqldatabaseadvisorga/). Au moment de la publication de cet article, le magasin de requêtes est en cours d’exécution sur plus de 200 000 bases de données utilisateur dans Azure, et recueille les informations liées aux requêtes depuis plusieurs mois sans interruption.

> [AZURE.IMPORTANT] Microsoft met actuellement en place le magasin de requêtes pour toutes les bases de données SQL Azure (existantes et nouvelles). Dans l’immédiat, cette procédure d’activation inclut uniquement les bases de données singleton, mais concernera bientôt également les bases de données de pool élastique. Lorsque vous utilisez des pools élastiques, activez le magasin de requêtes uniquement pour le petit sous-ensemble de bases de données. L’activation du magasin de requêtes pour toutes les bases de données dans un pool élastique peut entraîner l’utilisation excessive des ressources et peut provoquer une absence de réponse de votre système.

## Magasin de requête comme fonction gérée dans la base de données SQL Azure

Le fonctionnement du magasin de requêtes dans la base de données SQL Azure se base sur deux principes fondamentaux :

- Pas d’impact visible sur la charge de travail du client
- Surveillance en continu des requêtes, sauf en cas d’impact sur une charge de travail du client

L’impact sur la charge de travail du client possède deux dimensions :

- ***Disponibilité*** : le [contrat de niveau de service pour la base de données SQL](https://azure.microsoft.com/support/legal/sla/sql-database/v1_0/) n’est pas réduit lorsque le magasin de requêtes est en cours d’exécution.
- ***Performances*** : la surcharge moyenne créée par le magasin de requêtes se situe généralement entre 1 et 2 %.

Dans Azure, le magasin de requêtes fonctionne avec une quantité limitée de ressources (processeur, mémoire, disque d’E/S, volume sur le disque, etc.). Il se conforme à diverses restrictions du système afin d’affecter au minimum la charge de travail régulière :

- ***Restrictions statiques :*** restrictions imposées par la capacité en ressources d’un niveau de service donné (De base, Standard, Premium, pool élastique).
- ***Restrictions dynamiques :*** restrictions imposées par la consommation actuelle de la charge de travail (par exemple, les ressources disponibles).

Pour garantir un fonctionnement continu et fiable, la base de données SQL Azure a créé une infrastructure de surveillance permanente autour du magasin de requêtes pour recueillir les principales données opérationnelles de chaque base de données. Par conséquent, plusieurs KPI techniques sont surveillés en continu afin de garantir la fiabilité :

- Nombre d’exceptions et atténuations automatiques
- Nombre de bases de données à l’état READ\_ONLY et durée de l’état READ\_ONLY
- Les meilleures bases de données avec consommation de mémoire du magasin de requêtes au-delà de la limite.
- Meilleures bases de données en matière de fréquence et de durée du nettoyage automatique
- Meilleures bases de données en matière de durée de chargement des données sur la mémoire (initialisation du magasin de requêtes)
- Meilleures bases de données en matière de durée du vidage des données sur le disque

La base de données SQL Azure utilise les données recueillies pour :

- ***Découvrir des modèles d’utilisation sur un grand nombre de bases de données afin d’améliorer la qualité et la fiabilité de la fonctionnalité :*** le magasin de requêtes s’améliore avec chaque mise à jour de la base de données SQL Azure. 
- ***Résoudre ou limiter les problèmes causés par le magasin de requêtes :*** la base de données SQL Azure peut détecter et limiter les problèmes ayant un impact important sur la charge de travail du client, avec une latence faible (inférieure à une heure). Généralement, les problèmes sont traités en ***désactivant*** temporairement le magasin de requêtes.

De temps à autre, les mises à jour du magasin de requêtes intègrent des modifications des valeurs par défaut appliquées à des configurations internes et, plus rarement, externes (destinées aux clients). Par conséquent, l’expérience client liée au magasin de requêtes sur la base de données SQL Azure peut être différente de celle des utilisateurs dans un environnement local en raison des actions automatiques effectuées par la plateforme Azure :

- Le magasin de requêtes peut être ***désactivé*** pour limiter les problèmes, puis ***réactivé*** une fois le problème résolu.
- La configuration du magasin de requêtes peut être modifiée afin de garantir un travail fiable. Cette action peut être effectuée comme suit :
    - Modifications de chaque base de données individuellement pour résoudre les problèmes d’instabilité ou de manque de fiabilité.
    - Déploiement global des modifications optimales de la configuration pour améliorer toutes les bases de données utilisant le magasin de requêtes.

La ***désactivation*** du magasin de requêtes est une action automatique limitée aux bases de données individuelles. Elle se produit lorsqu’un comportement a un impact négatif sur la ou les bases de données de l’utilisateur pour lesquelles le système de détection a déclenché une alerte. Pour cette base de données spécifique, le magasin de requêtes reste ***désactivé*** jusqu’à ce qu’une nouvelle version améliorant le magasin de requêtes soit disponible. Lorsque le magasin de requêtes passe à l’état ***désactivé***, le client en est informé par e-mail. Il lui est conseillé de ne pas réactiver le magasin de requêtes tant qu’une nouvelle version n’a pas été déployée. Après un nouveau déploiement, l’infrastructure de la base de données SQL Azure active automatiquement le magasin de requêtes pour toutes les bases de données pour lesquelles il avait été ***désactivé***.

Plus rarement, la base de données SQL Azure peut appliquer de nouveaux paramètres de configuration par défaut à toutes les bases de données utilisateur, afin de permettre un travail fiable et la collecte des données en continu.

## Configuration optimale du magasin de requêtes

Cette section décrit les paramètres optimaux de configuration par défaut, conçus pour garantir un fonctionnement fiable du magasin de requêtes, ainsi que des fonctionnalités liées, comme [SQL Database Advisor et le tableau de bord des performances](https://azure.microsoft.com/updates/sqldatabaseadvisorga/). La configuration par défaut est optimisée pour la collecte des données en continu, c’est-à-dire pour passer le moins de temps possible aux états OFF/READ\_ONLY.

| Configuration | Description | Default | Commentaire |
| ------------- | ----------- | ------- | ------- |
| MAX\_STORAGE\_SIZE\_MB | Spécifie la limite d’espace de données que le magasin de requêtes inclura dans la base de données client | 100 | Appliqué aux nouvelles bases de données |
| INTERVAL\_LENGTH\_MINUTES | Définit la durée pendant laquelle les statistiques d’exécution collectées pour les plans de requête sont agrégées et rendues persistantes. Chaque plan de requête actif dispose au maximum d’une ligne pour une période définie avec cette configuration | 60 | Appliqué aux nouvelles bases de données |
| STALE\_QUERY\_THRESHOLD\_DAYS | Stratégie de nettoyage basée sur la durée, et qui contrôle la période de rétention des statistiques d’exécution persistantes et des requêtes inactives | 30 | Appliqué aux nouvelles bases de données et aux bases de données ayant la valeur par défaut précédente (367) |
| SIZE\_BASED\_CLEANUP\_MODE | Indique si un nettoyage automatique des données doit avoir lieu lorsque la taille des données du magasin de requêtes approche de la limite | AUTO | Appliqué à toutes les bases de données |
| QUERY\_CAPTURE\_MODE | Indique si toutes les requêtes sont suivies, ou seulement un sous-ensemble de requêtes | AUTO | Appliqué à toutes les bases de données |
| FLUSH\_INTERVAL\_SECONDS | Indique la durée maximale pendant laquelle les statistiques d’exécution capturées seront conservées dans la mémoire, avant le vidage sur disque | 900 | Appliqué aux nouvelles bases de données |
||||||

> [AZURE.IMPORTANT] Ces paramètres par défaut ci-dessus seront automatiquement appliqués à l’étape finale de l’activation du magasin de requêtes dans toutes les bases de données SQL Azure (voir la remarque importante ci-dessus). Après cela, la base de données SQL Azure ne modifiera pas les valeurs de configuration définies par les clients, à moins qu’elles aient un impact négatif sur les charges de travail principales ou sur la fiabilité des opérations du magasin de requêtes.

Si vous souhaitez conserver vos paramètres personnalisés, utilisez [ALTER DATABASE avec les options du magasin de requêtes](https://msdn.microsoft.com/library/bb522682.aspx) pour rétablir la configuration à l’état précédent. Découvrez les [meilleures pratiques liées au magasin de requêtes](https://msdn.microsoft.com/library/mt604821.aspx) pour savoir comment choisir des paramètres de configuration optimaux.

## Étapes suivantes

[Informations sur les performances des bases de données SQL](sql-database-performance.md)

## En savoir plus

Pour plus d’informations, consultez les articles suivants :

- [Un enregistreur de données pour votre base de données](https://azure.microsoft.com/blog/query-store-a-flight-data-recorder-for-your-database) 

- [Analyse des performances à l’aide du magasin de requêtes](https://msdn.microsoft.com/library/dn817826.aspx)

- [Scénarios d’utilisation du magasin de requêtes](https://msdn.microsoft.com/library/mt614796.aspx)

- [Analyse des performances à l’aide du magasin de requêtes](https://msdn.microsoft.com/library/dn817826.aspx)

<!---HONumber=AcomDC_0601_2016-->