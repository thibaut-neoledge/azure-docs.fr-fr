<properties 
   pageTitle="Conception de solutions cloud pour la récupération d’urgence à l’aide de la géo-réplication de base de données SQL | Microsoft Azure"
   description="Apprenez à concevoir votre solution cloud pour la récupération d’urgence en choisissant le modèle de basculement approprié."
   services="sql-database"
   documentationCenter="" 
   authors="anosov1960" 
   manager="jhubbard" 
   editor="monicar"/>

<tags
   ms.service="sql-database"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-management" 
   ms.date="04/29/2016"
   ms.author="sashan"/>

# Stratégies de récupération d’urgence pour les applications utilisant le pool élastique de base de données SQL 

Au fil des années, nous avons constaté que les services cloud n’étaient pas infaillibles et que des catastrophes pouvaient, et allaient, se produire. La base de données SQL offre un certain nombre de fonctionnalités pour assurer la continuité des activités de votre application en cas d’incident. Les pools élastiques et les bases de données autonomes prennent en charge le même type de fonctionnalités de récupération d’urgence. Cet article décrit plusieurs stratégies de récupération d’urgence pour les pools élastiques qui tirent parti de ces fonctionnalités de continuité des activités de la base de données SQL.

Dans le cadre de cet article, nous utiliserons le modèle d’application SaaS canonique d’un ISV :

<i>Les nouvelles applications web basées dans le cloud déploient une base de données SQL pour chaque utilisateur final. L’éditeur de logiciels indépendant (ISV) a de nombreux clients et utilise donc de nombreuses bases de données, appelées bases de données client. Comme les bases de données client ont généralement des modèles d’activité non prévisibles, l’ISV utilise un pool élastique afin de pouvoir prévoir à long terme les coûts des bases de données. Le pool élastique simplifie également la gestion des performances lors des pics d’activité de l’utilisateur. Outre les bases de données client, l’application utilise plusieurs bases de données pour gérer les profils utilisateur et la sécurité, recueillir des modèles d’utilisation, etc. La disponibilité des locataires individuels n’affecte pas la disponibilité de l’application dans son ensemble. Toutefois, la disponibilité et les performances des bases de données de gestion sont essentielles au bon fonctionnement de l’application. Ainsi, si les bases de données de gestion sont hors connexion, l’ensemble de l’application le sera également.</i>

Nous allons à présent aborder les stratégies de récupération d’urgence applicables à des scénarios allant des applications de start-ups soucieuses des coûts aux applications présentant des exigences de disponibilité strictes.

## Scénario 1 Start-up soucieuse des coûts

<i>Je suis une jeune entreprise qui fait très attention à ses coûts. Je souhaite simplifier le déploiement et la gestion de l’application et avoir un contrat SLA limité pour chacun de mes clients. Mais je souhaite être sûre que l’application dans son ensemble ne sera jamais hors connexion.</i>

Pour répondre au besoin de simplicité, vous devez déployer toutes les bases de données client dans un pool élastique dans la région Azure de votre choix et déployer les bases de données de gestion en tant que bases de données autonomes géo-répliquées. Pour la récupération d’urgence des locataires, utilisez la fonctionnalité de géo-restauration qui ne vous coûtera pas un centime. Pour garantir la disponibilité des bases de données de gestion, ces dernières doivent être géo-répliquées vers une autre région (étape 1). Dans ce scénario, le coût de la configuration de récupération d’urgence est égal au coût total de la ou des base(s) de données secondaire(s). Cette configuration est illustrée dans le schéma suivant.

![La figure 1](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-1.png)

En cas de panne dans la région primaire, les étapes de récupération à suivre pour remettre votre application en ligne sont illustrées dans le schéma suivant.

- Basculez immédiatement les bases de données de gestion (2) vers la région de récupération d’urgence. 
- Modifiez la chaîne de connexion de l’application pour la diriger vers la région de récupération d’urgence. Tous les nouveaux comptes et bases de données client seront créés dans la région de récupération d’urgence. Les données des clients existants seront temporairement indisponibles.
- Créez le pool élastique en utilisant la même configuration que le pool d’origine (3). 
- Utilisez la géo-restauration pour créer des copies des bases de données client (4). Vous pouvez envisager de déclencher les restaurations individuelles via les connexions de l’utilisateur final ou utiliser d’autres schémas de priorité spécifiques à l’application.

À ce stade, votre application est à nouveau en ligne dans la région de récupération d’urgence, mais certains clients accéderont moins rapidement à leurs données.

![Figure 2](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-2.png)

Si la panne est temporaire, il est possible que la région primaire soit restaurée par Azure avant que toutes les restaurations soient terminées dans la région de récupération d’urgence. Dans ce cas, vous devez orchestrer le déplacement de l’application dans la région primaire. Les étapes du processus sont illustrées dans le schéma suivant.
 
- Annulez toutes les requêtes de géo-restauration en attente.   
- Basculez les bases de données de gestion vers la région primaire (5). Remarque : après la récupération de la région, les anciennes bases de données primaires sont automatiquement devenues bases de données secondaires. Les rôles seront à nouveau permutés. 
- Modifiez la chaîne de connexion de l’application pour la diriger vers la région primaire. Désormais, tous les nouveaux comptes et bases de données client seront créés dans la région primaire. Les données de certains clients existants seront temporairement indisponibles.   
- Définissez toutes les bases de données du pool de récupération d’urgence en lecture seule pour vous assurer qu’elles ne pourront pas être modifiées dans la région de récupération d’urgence (6). 
- Pour chaque base de données du pool de récupération d’urgence qui a été modifiée depuis la récupération, renommez ou supprimez la base de données correspondante dans le pool principal (7). 
- Copiez les bases de données mises à jour du pool de récupération d’urgence vers le pool principal (8). 
- Supprimez le pool de récupération d’urgence (9).

À ce stade, votre application sera en ligne dans la région primaire avec toutes les bases de données client disponibles dans le pool principal.

![Figure 3](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-3.png)

L’**avantage** majeur de cette stratégie est le faible coût de redondance des couches de données. Les sauvegardes sont automatiquement effectuées par le service de base de données SQL avec aucune réécriture de l’application et sans coût supplémentaire. Des frais s’appliquent uniquement lorsque les bases de données sont restaurées. L’**inconvénient** est que la récupération complète de toutes les bases de données client prend beaucoup de temps. Cela dépend du nombre total de restaurations que vous lancez dans la région de récupération d’urgence et de la taille globale des bases de données client. Même si vous donnez la priorité à la restauration de certains clients, vous devrez tout de même composer avec toutes les autres restaurations lancées dans la même région car le service limitera la bande passante pour minimiser l’impact global sur les bases de données des clients existants. En outre, la récupération des bases de données client ne peut pas démarrer tant que le nouveau pool élastique n’a pas été créé dans la région de récupération d’urgence.

## Scénario 2 Application arrivée à maturité avec un service sur plusieurs niveaux 

<i>Je propose une application SaaS mature avec des offres de service sur plusieurs niveaux et des contrats SLA différents pour les clients utilisant une version d’évaluation gratuite et ceux utilisant une version payante. Pour les clients utilisant une version d’évaluation, je dois réduire les coûts autant que possible. Les interruptions de service sont acceptables pour les clients utilisant une version d’évaluation, mais je souhaite les éviter au maximum. Pour les clients utilisant une version payante, les interruptions de service ne sont pas acceptables (risque de perte du client). Je veux donc m’assurer que les clients qui utilisent une version payante puissent accéder à leurs données à tout moment.</i>

Dans ce scénario, vous devez séparer les clients utilisant une version d’évaluation des clients utilisant une version payante en les plaçant dans des pools élastiques distincts. Les clients utilisant une version d’évaluation auront un nombre inférieur d’eDTU par client et un contrat SLA moins élevé avec un temps de récupération plus long. Les clients utilisant une version payante seront dans un pool avec un nombre d’eDTU par client plus élevé et un contrat SLA plus élévé. Pour limiter au maximum le temps de récupération, les bases de données client des clients utilisant une version payante devront être géo-répliquées. Cette configuration est illustrée dans le schéma suivant.

![Figure 4](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-4.png)

Comme dans le premier scénario, les bases de données de gestion seront assez actives, d’où l’importance d’utiliser des bases de données autonomes géo-répliquées (1). Cela garantira des performances prévisibles pour les nouveaux abonnements client, les mises à jour de profil et d’autres opérations de gestion. La région qui héberge les bases de données de gestion primaires sera la région primaire. La région qui héberge les bases de données de gestion secondaires sera la région de récupération d’urgence.

Les bases de données client des clients utilisant une version payante auront des bases de données actives dans le pool « payant » configuré dans la région primaire. Vous devez configurer un pool secondaire portant le même nom dans la région de récupération d’urgence. Chaque base de données client sera géo-répliquée dans le pool secondaire (2). Cela permettra une récupération rapide de toutes les bases de données client à l’aide de la fonctionnalité de basculement.

En cas de panne dans la région primaire, les étapes de récupération à suivre pour remettre votre application en ligne sont illustrées dans le schéma suivant :

![Figure 5](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-5.png)

- Basculez immédiatement les bases de données de gestion vers la région de récupération d’urgence (3).
- Modifiez la chaîne de connexion de l’application pour la diriger vers la région de récupération d’urgence. Tous les nouveaux comptes et bases de données client seront désormais créés dans la région de récupération d’urgence. Les données des clients utilisant une version d’évaluation seront temporairement indisponibles.
- Basculez les bases de données des clients utilisant une version payante vers le pool de la région de récupération d’urgence afin de restaurer immédiatement leur disponibilité (4). Le basculement est un processus de modification rapide au niveau des métadonnées. Vous pouvez envisager d’optimiser le processus en permettant le déclenchement à la demande des basculements individuels via les connexions de l’utilisateur final. 
- Si le nombre d’eDTU de votre pool secondaire était inférieur à celui de votre pool principal car les bases de données secondaires n’avaient besoin que d’une capacité limitée pour traiter les journaux des modifications en tant que bases de données secondaires, vous devez immédiatement augmenter la capacité du pool pour prendre en charge la charge de travail globale de tous les clients (5). 
- Créez le nouveau pool élastique avec le même nom et la même configuration dans la région de récupération d’urgence pour les bases de données des clients utilisant une version d’évaluation (6). 
- Une fois le pool créé pour les clients utilisant une version d’évaluation, utilisez la géo-restauration pour restaurer chaque base de données client en version d’évaluation dans le nouveau pool (7). Vous pouvez envisager de déclencher les restaurations individuelles via les connexions de l’utilisateur final ou utiliser d’autres schémas de priorité spécifiques à l’application.

À ce stade, votre application est à nouveau en ligne dans la région de récupération d’urgence. Tous les clients utilisant une version payante auront accès à leurs données à tout moment, tandis que les clients utilisant une version d’évaluation accéderont moins rapidement à leurs données.

Lorsque la région primaire est restaurée par Azure *après* que vous ayez restauré l’application dans la région de récupération d’urgence, vous pouvez continuer à exécuter l’application dans cette région ou restaurer l’application dans la région primaire. Si la région primaire est restaurée *avant* la fin du processus de basculement, nous vous conseillons de restaurer immédiatement l’application. Les étapes du processus de restauration sont illustrées dans le schéma suivant :
 
![Figure 6](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-6.png)

- Annulez toutes les requêtes de géo-restauration en attente.   
- Basculez les bases de données de gestion (8). Après la récupération de la région, les anciennes bases de données primaires sont automatiquement devenues bases de données secondaires. Celles-ci redeviennent désormais les bases de données primaires.  
- Basculez les bases de données des clients utilisant une version payante (9). Là encore, après la récupération de la région, les anciennes bases de données primaires sont automatiquement devenues bases de données secondaires. Celles-ci redeviennent désormais les bases de données primaires. 
- Définissez les bases de données d’évaluation restaurées qui ont été modifiées dans la région de récupération d’urgence en lecture seule (10).
- Pour chaque base de données du pool de récupération d’urgence des clients utilisant une version d’évaluation qui a été modifiée depuis la restauration, renommez ou supprimez la base de données correspondante dans le pool principal de ces clients (11). 
- Copiez les bases de données mises à jour du pool de récupération d’urgence vers le pool principal (12). 
- Supprimez le pool de récupération d’urgence (13). 

> [AZURE.NOTE] L’opération de basculement est asynchrone. Pour réduire le temps de récupération, il est important que vous exécutiez la commande de basculement des bases de données client pour des lots d’au moins 20 bases de données.

L’**avantage** majeur de cette stratégie est qu’elle fournit le contrat SLA le plus élevé pour les clients utilisant une version payante. Elle garantit également le déblocage des nouvelles bases de données en version d’évaluation dès que le pool de récupération d’urgence des bases de données en version d’évaluation est créé. L’**inconvénient** est que cette configuration augmente le coût total des bases de données client en ajoutant le coût du pool de récupération d’urgence secondaire pour les clients utilisant une version payante. De plus, si le pool secondaire est de taille différente, les clients qui utilisent une version payante constateront une baisse des performances après le basculement, jusqu’à la fin du processus de mise à niveau du pool dans la région de récupération d’urgence.

## Scénario 3 Application dispersée géographiquement avec un service sur plusieurs niveaux

<i>Je propose une application SaaS mature avec des offres de service sur plusieurs niveaux. Je souhaite offrir un contrat SLA très agressif à mes clients qui utilisent la version payante et réduire l’impact d’une éventuelle panne car je sais que même une brève interruption de service peut rendre mes clients mécontents. Il est essentiel que les clients utilisant la version payante puissent accéder à leurs données à tout moment. Les versions d’évaluation sont gratuites et n’incluent aucun contrat SLA. </i>

Dans ce scénario, vous devez configurer trois pools élastiques distincts. Deux pools de taille égale avec un nombre élevé d’eDTU par base de données doivent être configurés dans deux régions différentes pour accueillir les bases de données client des clients utilisant la version payante de l’application. Le troisième pool, qui accueillera les bases de données des clients utilisant la version d’évaluation, aura un nombre moins élevé d’eDTU par base de données et sera configuré dans l’une des deux régions.

Pour limiter au maximum le temps de récupération en cas de panne, les bases de données client des clients utilisant la version payante doivent être répliquées avec 50 % des bases de données primaires dans chacune des deux régions. De même, chaque région aura 50 % des bases de données secondaires. Ainsi, si une région est hors connexion, seule la moitié des bases de données des clients utilisant la version payante sera affectée et devra être basculée. Les autres bases de données ne seront pas affectées. Cette configuration est illustrée dans le schéma suivant :

![Figure 4](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-7.png)

Comme dans les scénarios précédents, les bases de données de gestion seront assez actives. Il est donc essentiel de les configurer en tant que bases de données autonomes géo-répliquées (1). Cela garantira des performances prévisibles pour les nouveaux abonnements client, les mises à jour de profil et d’autres opérations de gestion. La région A sera la région primaire pour les bases de données de gestion et la région B sera utilisée pour la récupération des bases de données de gestion.

Les bases de données client des clients utilisant la version payante seront également géo-répliquées, mais les bases de données primaires et secondaires seront réparties dans les régions A et B (2). Ainsi, les bases de données primaires affectées par la panne pourront basculer vers l’autre région et être à nouveau accessibles. L’autre moitié des bases de données client ne sera pas du tout affectée.

Le schéma suivant illustre les étapes de récupération à suivre en cas de panne dans la région A.

![Figure 5](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-8.png)

- Basculez immédiatement les bases de données de gestion vers la région B (3).
- Modifiez la chaîne de connexion de l’application pour la diriger vers les bases de données de gestion dans la région B. Modifiez les bases de données de gestion pour vous assurer que les nouveaux comptes et bases de données client seront créés dans la région B et que les bases de données client existantes s’y trouveront également. Les données des clients utilisant une version d’évaluation seront temporairement indisponibles.
- Basculez les bases de données des clients utilisant la version payante vers le pool 2 de la région B afin de restaurer immédiatement leur disponibilité (4). Le basculement est un processus de modification rapide au niveau des métadonnées. Vous pouvez envisager d’optimiser le processus en permettant le déclenchement à la demande des basculements individuels via les connexions de l’utilisateur final. 
- Étant donné que le pool 2 contient désormais uniquement les bases de données primaires, la charge de travail totale dans le pool va augmenter. Vous devez donc immédiatement augmenter le nombre d’eDTU du pool (5). 
- Créez le nouveau pool élastique avec le même nom et la même configuration dans la région B pour les bases de données des clients utilisant une version d’évaluation (6). 
- Une fois le pool créé, utilisez la géo-restauration pour restaurer chaque base de données client en version d’évaluation dans le pool (7). Vous pouvez envisager de déclencher les restaurations individuelles via les connexions de l’utilisateur final ou utiliser d’autres schémas de priorité spécifiques à l’application.


> [AZURE.NOTE] L’opération de basculement est asynchrone. Pour réduire le temps de récupération, il est important que vous exécutiez la commande de basculement des bases de données client pour des lots d’au moins 20 bases de données.

À ce stade, votre application est à nouveau en ligne dans la région B. Tous les clients utilisant une version payante auront accès à leurs données à tout moment, tandis que les clients utilisant une version d’évaluation accéderont moins rapidement à leurs données.

Après la récupération de la région A, vous devez décider si vous souhaitez utiliser la région B pour les clients utilisant la version d’évaluation ou restaurer les bases de données dans le pool des clients d’évaluation de la région A. Pour prendre votre décision, vous pouvez par exemple tenir compte du pourcentage de bases de données client en version d’évaluation qui ont été modifiées depuis la récupération. Quelle que soit votre décision, vous devrez rééquilibrer les bases de données des clients utilisant la version payante entre les deux pools. Le schéma suivant illustre le processus de restauration des bases de données client d’évaluation dans la région A.
 
![Figure 6](./media/sql-database-disaster-recovery-strategies-for-applications-with-elastic-pool/diagram-9.png)

- Annulez toutes les requêtes de géo-restauration en attente pour le pool de récupération d’urgence des bases de données en version d’évaluation.   
- Basculez les bases de données de gestion (8). Après la récupération de la région, les anciennes bases de données primaires sont automatiquement devenues bases de données secondaires. Celles-ci redeviennent désormais les bases de données primaires.  
- Choisissez les bases de données des clients utilisant la version payante qui seront restaurées dans le pool 1 et initiez le basculement vers les bases de données secondaires (9). Après la récupération de la région, toutes les bases de données du pool 1 sont automatiquement devenues bases de données secondaires. Désormais, 50 % d’entre elles redeviennent des bases de données primaires. 
- Réduisez la taille du pool 2 en rétablissant le nombre d’eDTU d’origine (10).
- Définissez toutes les bases de données d’évaluation restaurées de la région B en lecture seule (11).
- Pour chaque base de données du pool de récupération d’urgence des versions d’évaluation qui a été modifiée depuis la récupération, renommez ou supprimez la base de données correspondante dans le pool principal des versions d’évaluation (12). 
- Copiez les bases de données mises à jour du pool de récupération d’urgence vers le pool principal (13). 
- Supprimez le pool de récupération d’urgence (14). 

Cette stratégie a plusieurs **avantages** :

- Elle offre le contrat SLA le plus agressif pour les clients utilisant la version payante de l’application, car elle protège au moins 50 % des bases de données client en cas de panne. 
- Elle garantit le déblocage des nouvelles bases de données en version d’évaluation dès que le pool de récupération d’urgence des bases de données en version d’évaluation est créé lors de la récupération. 
- Elle permet une utilisation plus efficace de la capacité du pool car 50 % des bases de données secondaires des pools 1 et 2 sont systématiquement moins actives que les bases de données primaires.

Il y a tout de même des **inconvénients** :

- Les opérations CRUD exécutées sur les bases de données de gestion ont une latence plus faible pour les utilisateurs finaux connectés à la région A que pour ceux connectés à la région B, car elles sont exécutées au niveau des bases de données de gestion primaires.
- Cette stratégie requiert une conception plus complexe des bases de données de gestion. Par exemple, chaque enregistrement de locataire devra disposer d’une balise d’emplacement qui devra être modifiée pendant le basculement et la restauration.  
- Les clients qui utilisent la version payante de l’application peuvent constater une baisse des performances jusqu’à la fin du processus de mise à niveau du pool dans la région B. 

## Résumé

Cet article aborde les différentes stratégies de récupération d’urgence pour la couche de base de données utilisée par une application SaaS à architecture mutualisée. La stratégie choisie doit être basée sur les besoins de l’application, tels que le modèle commercial, le contrat SLA que vous souhaitez offrir à vos clients, les contraintes budgétaires, etc. Nous vous détaillons les avantages et les inconvénients de chaque stratégie afin de vous aider à prendre une décision éclairée. De plus, votre propre application inclura probablement d’autres composants Azure. Vous devez donc vérifier les recommandations associées en termes de continuité des activités et orchestrer la récupération de la couche de base de données avec ces composants. Pour en savoir plus sur la gestion de la récupération des applications de base de données dans Azure, consultez l’article [Conception de solutions cloud pour la récupération d’urgence](./sql-database-designing-cloud-solutions-for-disaster-recovery.md).

Les étapes individuelles requises pour chaque scénario impliquent des opérations sur un grand nombre de bases de données. Vous pouvez utiliser les tâches élastiques de la base de données SQL Azure pour gérer ces opérations à grande échelle. Pour plus d’informations, reportez-vous à l’article [Gestion des bases de données cloud avec montée en charge](./sql-database-elastic-jobs-overview.md). Les pages suivantes contiennent des informations sur les opérations spécifiques requises pour mettre en œuvre chaque scénario décrit dans cet article :

- [Ajouter une base de données secondaire](https://msdn.microsoft.com/library/azure/mt603689.aspx) 
- [Basculer une base de données vers une base de données secondaire](https://msdn.microsoft.com/library/azure/mt619393.aspx)
- [Géo-restaurer une base de données](https://msdn.microsoft.com/library/azure/mt693390.aspx) 
- [Déplacer une base de données](https://msdn.microsoft.com/library/azure/mt619368.aspx)
- [Copier une base de données](https://msdn.microsoft.com/library/azure/mt603644.aspx)

<!---HONumber=AcomDC_0511_2016-->