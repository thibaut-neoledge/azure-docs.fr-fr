---
title: Récupération d’urgence et haute disponibilité pour les applications Azure | Microsoft Docs
description: Présentations techniques et informations détaillées sur la conception d’applications pour la haute disponibilité et la récupération d’urgence des applications développées sur Microsoft Azure.
services: ''
documentationcenter: na
author: adamglick
manager: saladki
editor: ''

ms.service: resiliency
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/18/2016
ms.author: aglick

---
# Récupération d’urgence et haute disponibilité des applications développées sur Microsoft Azure
## Introduction
Cet article est axé sur la haute disponibilité pour les applications exécutées dans Azure. Une stratégie globale de haute disponibilité inclut également l’aspect de la récupération d’urgence. La planification des pannes et des sinistres dans le cloud nécessite une identification rapide des dysfonctionnements. Par la suite, vous implémentez une stratégie en fonction de votre tolérance en matière de temps d’arrêt de l’application. En outre, vous devez déterminer l’étendue de perte de données pouvant être tolérée par l’application sans que vous ne déploriez de conséquence commerciale négative au cours de la restauration.

La plupart des entreprises disent être préparées pour les pannes temporaires et de grande envergure. Toutefois, avant que vous ne répondiez vous-même à cette question, demandez-vous : mon entreprise se prépare-t-elle à ces pannes ? Testez-vous la récupération des bases de données afin de vous assurer d’appliquer les processus appropriés ? Probablement pas. Pourquoi ? Une stratégie efficace de récupération d’urgence comprend des phases importantes de planification et de développement dédiées à l’implémentation de ces processus. Tout comme de nombreux secteurs non fonctionnels, comme la sécurité, la récupération d’urgence bénéficie rarement de l’analyse en amont et de l’allocation de temps nécessaires. En outre, la plupart des entreprises ne disposent pas du budget requis pour les régions réparties sur plusieurs emplacements, caractérisées par une capacité redondante. Par conséquent, même les applications critiques sont fréquemment exclues d’une stratégie efficace de récupération d’urgence.

Les plateformes de cloud, comme Azure, servent des régions réparties à divers endroits du monde entier. Ces plateformes fournissent également des fonctionnalités qui prennent en charge la disponibilité et un vaste éventail de scénarios de récupération d’urgence. Désormais, chaque application cloud critique peut être pleinement intégrée dans la stratégie de récupération du système. Azure présente une résilience et une récupération d’urgence intégrées à nombre de ses services. Vous devez soigneusement examiner ces fonctionnalités de plateforme et déterminer des stratégies applicatives.

Cet article décrit les phases architecturales requises pour la définition d’une stratégie appropriée de récupération d’urgence d’un déploiement Azure. Ensuite, vous pouvez implémenter le processus global de continuité d’activité. Un plan de continuité d’activité est une feuille de route dédiée à la continuité des opérations durant des scénarios défavorables. Ces situations peuvent correspondre à une défaillance technologique, telle qu’une panne de service, ou à une catastrophe naturelle, telle qu’une tempête ou une panne de courant. Comme le décrit ce document NIST [Contingency Planning Guide for Information Technology Systems](https://www.fismacenter.com/sp800-34.pdf) (Guide de la planification des impondérables survenant avec les systèmes de technologies de l’information), la résilience des applications en cas de sinistre constitue uniquement un sous-ensemble du processus global de récupération d’urgence.

Les sections suivantes définissent différents niveaux de défaillances, de techniques de résolution et d’architectures prenant en charge ces techniques. Ces informations sont destinées à enrichir vos processus et vos procédures de récupération d’urgence, afin de garantir l’efficacité de votre stratégie.

## Caractéristiques des applications de cloud résilientes
Une application correctement conçue peut résister à des défaillances de fonctionnalités au niveau tactique, et elle peut également tolérer des défaillances stratégiques du système au niveau régional. Les sections suivantes définissent la terminologie référencée dans le document pour décrire les divers aspects des services cloud résilients.

### Haute disponibilité
Une application de cloud hautement disponible implémente des stratégies destinées à l’absorption des pannes des dépendances, comme les services gérés offerts par la plateforme cloud. En dépit des éventuelles défaillances des fonctionnalités de la plateforme cloud, cette approche permet à l’application de continuer à présenter les caractéristiques systémiques fonctionnelles et non fonctionnelles. Cette thématique est abordée en détail dans la série de vidéos de Channel 9 [Failsafe: guidance for resilient cloud architectures](https://channel9.msdn.com/Series/FailSafe) (Sécurité intégrée : recommandations applicables à la résilience des architectures cloud).

Lorsque vous implémentez l’application, vous devez prendre en compte la probabilité d’une panne de fonctionnalité. Par ailleurs, avant de procéder à la définition détaillée des stratégies d’implémentation, anticipez l’incidence commerciale d’une panne sur l’application. Si vous n’intégrez pas l’impact commercial éventuel et le risque potentiel dans votre stratégie, l’implémentation peut s’avérer coûteuse et possiblement inutile.

Établissons une analogie entre la haute disponibilité et le secteur automobile. Un fabricant disposant de pièces de qualité supérieure et d’une meilleure expertise d’ingénierie ne peut garantir l’absence de défaillances occasionnelles. Par exemple, si vous crevez un pneu de votre voiture, cela ne l’empêche pas de rouler : sa fonctionnalité est simplement diminuée. Si vous aviez anticipé l’éventualité d’un incident de ce type, vous auriez disposé d’une roue de dépannage, qui vous aurait permis de rouler jusqu’au garage le plus proche. Bien que cette roue vous interdise toute circulation à vitesse élevée, vous pouvez continuer à rouler jusqu’au remplacement du pneu. De la même manière, l’anticipation d’une perte potentielle de fonctionnalités d’un service cloud peut prévenir tout arrêt de l’application suite à une défaillance relativement mineure. Cela reste valable si le service cloud s’exécute avec une fonctionnalité dégradée.

Voici quelques-unes des caractéristiques principales de services cloud hautement disponibles : disponibilité, extensibilité et tolérance de panne. Bien que ces caractéristiques soient interdépendantes, il est important de comprendre chacune d’entre elles et d’appréhender leur contribution à la disponibilité globale de la solution.

### Availability
Une application disponible tient compte de la disponibilité de son infrastructure sous-jacente et des services dépendants. Les applications disponibles suppriment les points de défaillances uniques, au moyen de la redondance et d’une conception résiliente. Lorsque l’on élargit la portée pour envisager la disponibilité dans Azure, il est important de comprendre le concept de disponibilité effective de la plateforme. La disponibilité effective tient compte des contrats de niveau de service de chaque service dépendant et de leur effet cumulé sur la disponibilité totale du système.

La disponibilité du système est la mesure du pourcentage de la fenêtre de temps pendant laquelle le système pourra fonctionner. Par exemple, le contrat de niveau de service de la disponibilité d’au moins deux instances d’un rôle web ou de travail dans Azure est de 99,95 % (sur 100 %). Il ne mesure pas la performance ou la fonctionnalité des services exécutés sur ces rôles. Toutefois, la disponibilité effective de votre service cloud est également affectée par les différents contrats de niveau de service des autres services dépendants. Plus votre système comporte de pièces mobiles, plus vous devez veiller avec soin à la capacité de l’application à faire preuve de résilience pour satisfaire les exigences de disponibilité de ses utilisateurs finaux.

Considérez les contrats de niveau de service suivants associés à un service Azure utilisant des services Azure : Compute, base de données SQL Azure et Azure Storage.

| Service Azure | Contrat SLA | Temps d’arrêt potentiel (en minutes)/mois (30 jours) |
|:--- |:--- |:---:|
| Calcul |99,95 % |21,6 minutes |
| Base de données SQL |99,99 % |4,3 minutes |
| Storage |99,90 % |43,2 minutes |

Vous devez anticiper l’arrêt potentiel de tous les services à des moments différents. Dans cet exemple simplifié, le nombre total de minutes d’arrêt potentiel de l’application est de 108 par mois. Un mois de 30 jours comporte un total de 43 200 minutes. 108 minutes représentent 0,25 % du nombre total de minutes d’un mois de 30 jours (43 200 minutes). Ainsi, vous disposez d’une disponibilité effective de 99,75 % pour le service cloud.

Malgré tout, vous pouvez améliorer cette statistique en appliquant les techniques de disponibilité décrites dans ce document. Par exemple, si vous concevez votre application pour continuer à s’exécuter quand la base de données SQL n’est plus disponible, vous pouvez retirer cet élément de l’équation. Cela peut signifier que l’application s’exécute avec des fonctionnalités réduites. Dès lors, les exigences de l’entreprise sont également à prendre en compte. Pour connaître la liste complète des contrats de niveau de service Azure, consultez [Contrats de niveau de service](https://azure.microsoft.com/support/legal/sla/).

### Extensibilité
L’extensibilité affecte directement la disponibilité. Une application mise sous échec en raison d’une augmentation de charge n’est plus disponible. Les applications évolutives peuvent satisfaire une demande accrue, tout en proposant des résultats cohérents dans des fenêtres de temps acceptables. Lorsqu’un système est évolutif, il se met à l’échelle horizontalement ou verticalement pour s’adapter aux augmentations de charge, tout en maintenant des performances uniformes. En bref, une mise à l’échelle horizontale correspond à un ajout de machines de la même taille (processeur, mémoire et bande passante), tandis qu’une mise à l’échelle verticale consiste en l’augmentation de la taille des machines existantes. Pour Azure, vous disposez d’options de mise à l’échelle verticale permettant de sélectionner différentes tailles de machines pour le calcul. Toutefois, la modification de la taille des machines nécessite un redéploiement. Par conséquent, les solutions les plus flexibles sont conçues pour une mise à l’échelle horizontale. Cela s’avère particulièrement vrai pour le calcul, dans la mesure où vous pouvez facilement augmenter le nombre d’instances exécutées sur un rôle web ou de travail. Ces instances supplémentaires gèrent l’augmentation de trafic via la portail web Azure, les scripts PowerShell ou le code. Basez cette décision sur les augmentations des métriques surveillées spécifiques. Dans ce scénario, aucune baisse notable des performances des utilisateurs ou des métriques n’est à signaler sous des conditions de charge. En général, les rôles web et de travail stockent en externe tous les états. Cette configuration permet un équilibrage flexible de la charge et une gestion sans heurt des modifications du nombre d’instances. La mise à l’échelle horizontale fonctionne également efficacement avec les services tels qu’Azure Storage, qui ne fournissent pas d’options hiérarchisées pour la mise à l’échelle verticale.

Les déploiements de cloud doivent être considérés comme une collection d’unités à l’échelle. Ainsi, l’application présente une élasticité dans la délivrance des besoins de débit des utilisateurs finaux. La visualisation des unités d’échelle est simplifiée aux niveaux du serveur web et du serveur d’applications. Pourquoi ? En fait, Azure fournit des nœuds de calcul sans état via les rôles web et de travail. L’ajout d’unités d’échelle de calcul au déploiement n’entraîne aucun effet collatéral dans la gestion de l’état des applications, dans la mesure où les unités d’échelle de calcul sont sans état. Une unité d’échelle de stockage prend en charge la gestion d’une partition de données (structurée ou non). Parmi les unités d’échelle de stockage, citons la partition de tables Azure, le conteneur d’objets blob Azure et la base de données SQL Azure. Même l’utilisation de plusieurs comptes Azure Storage à une incidence directe sur l’extensibilité des applications. Pour intégrer plusieurs unités d’échelle de stockage, vous devez concevoir un service cloud hautement évolutif. Par exemple, si une application utilise des données relationnelles, répartissez les données entre plusieurs bases de données SQL. Ce faisant, le stockage est conforme au modèle élastique d’unités d’échelle de calcul. De la même manière, Azure Storage prend en charge les schémas de partitionnement de données qui nécessitent des conceptions dédiées pour répondre aux besoins de débit de la couche de calcul. Pour obtenir la liste des meilleures pratiques de conception de services cloud évolutifs, consultez [Best Practices for the Design of Large-Scale Services on Azure Cloud Services](https://azure.microsoft.com/blog/best-practices-for-designing-large-scale-services-on-windows-azure/) (Meilleures pratiques de conception de services à grande échelle sur Azure Cloud Services).

### Tolérance de panne
Les développeurs de l’application doivent intégrer dans leur stratégie l’éventualité de la panne de l’ensemble des fonctionnalités cloud dépendantes à un moment donné dans le temps. Une application tolérante aux pannes détecte les éléments défaillants et modifie son fonctionnement en fonction afin d’offrir des résultats appropriés au sein de l’intervalle considéré. Dans le cadre des scénarios d’échec temporaire, un système de tolérance de panne applique une stratégie de nouvelle tentative. Pour les problèmes plus sérieux, l’application peut détecter les problèmes et basculer vers une solution matérielle alternative ou un plan d’urgence jusqu’à ce que le problème soit corrigé. Une application fiable peut gérer efficacement la défaillance d’une ou de plusieurs pièces tout en continuant à fonctionner normalement. Les applications tolérantes aux pannes peuvent utiliser une ou plusieurs stratégies de conception, comme la redondance, la réplication, ou une fonctionnalité diminuée.

## Récupération d'urgence
Un déploiement de cloud peut cesser de fonctionner en raison d’une panne systémique des services dépendants ou de l’infrastructure sous-jacente. Dans ces conditions, un plan de continuité d’activité déclenche le processus de récupération d’urgence. Ce processus sollicite généralement le personnel opérationnel et les procédures automatisées pour réactiver l’application dans une région disponible. Cela nécessite le transfert des utilisateurs de l’application, des données et des services vers la nouvelle région. Par ailleurs, l’activité implique l’utilisation d’un support de sauvegarde ou de la réplication en continu.

Penchons-nous de nouveau sur l’analogie analysée plus haut, qui consistait à comparer la haute disponibilité à la capacité à pallier une crevaison à l’aide d’une roue de dépannage. A contrario, la récupération d’urgence correspond aux procédures suivies après un accident, une fois que la voiture n’est plus opérationnelle. Dans ce cas, la meilleure solution consiste à identifier un moyen efficace de remplacer les véhicules en appelant une agence de voyage ou un ami. Ici, vous attendrez probablement un petit moment avant de retrouver la route. La réparation et le rétablissement du véhicule d’origine impliquent des processus bien plus complexes. De la même façon, la récupération d’urgence vers une autre région est une tâche complexe généralement associée à un temps d’arrêt et à une perte potentielle de données. Pour mieux appréhender et évaluer les stratégies de récupération d’urgence, il est important de définir deux termes : l’objectif de délai de récupération et l’objectif de point de récupération.

### Objectif de délai de récupération
L’objectif de délai de récupération correspond à l’intervalle maximal de temps alloué à la restauration de la fonctionnalité de l’application. Cette valeur, basée sur les exigences pour l’entreprise, est liée à l’importance de l’application. Les applications professionnelles critiques nécessitent un objectif de délai de récupération faible.

### Objectif de point de récupération
L’objectif de point de récupération correspond à la fenêtre de temps acceptable de perte de données associée au processus de récupération. Par exemple, si l’objectif de point de récupération est d’une heure, vous devez procéder à une sauvegarde ou à une réplication complètes des données au moins toutes les heures. Une fois que vous déplacez l’application dans une nouvelle région, le volume des données de sauvegarde peut être diminué d’une quantité équivalant à une heure de données, au maximum. Tout comme pour l’objectif de délai de récupération, un objectif de point de récupération bien plus faible sera privilégié pour les applications critiques.

## Liste de contrôle
Résumons les points principaux abordés dans cet article (et les articles associés sur la [haute disponibilité](resiliency-high-availability-azure-applications.md) et la [récupération d’urgence](resiliency-disaster-recovery-azure-applications.md) pour les applications Azure). Cette synthèse fait office de liste de contrôle des éléments à prendre en compte pour votre propre planification de disponibilité et de récupération d’urgence. Il s’agit des meilleures pratiques appliquées par les clients qui ont décidé de franchir le pas, d’implémenter une solution efficace.

1. Effectuez une évaluation des risques pour l’ensemble des applications, dans la mesure où chacune présente ses exigences propres. Certaines applications, plus critiques que d’autres, nécessitent l’allocation d’un budget supplémentaire dédié à la récupération d’urgence.
2. Utilisez ces informations pour définir les objectifs de délai et de point de récupération de chaque application.
3. Concevez en anticipant les défaillances, en commençant par l’architecture de l’application.
4. Mettez en œuvre les meilleures pratiques pour la haute disponibilité, tout en équilibrant les coûts, la complexité et les risques.
5. Implémentez les plans et les processus de récupération d’urgence.
   * Envisagez tous les types de défaillances, des problèmes identifiés au niveau des modules aux pannes complètes du cloud.
   * Établissez des stratégies de sauvegarde pour l’ensemble des données de référence et transactionnelles.
   * Choisissez une architecture de récupération d’urgence multisite.
6. Définissez un propriétaire spécifique pour les processus, l’automatisation et les tests de la récupération d’urgence. Le propriétaire doit gérer et détenir l’intégralité du processus.
7. Documentez les processus, ceci pour qu’ils soient aisément reproductibles. Il n’existe qu’un seul propriétaire, mais plusieurs personnes doivent être en mesure de comprendre et de suivre les processus en cas d’urgence.
8. Formez le personnel à l’implémentation du processus.
9. Recourez à des simulations régulières de récupération dans le cadre de la formation et de la validation du processus.

## Résumé
Lorsque des produits matériels ou des applications tombent en panne dans Azure, les techniques et stratégies appliquées diffèrent de celles qui sont mises en œuvre sur les systèmes en local défaillants. Pourquoi ? En réalité, les solutions cloud présentent généralement plus de dépendances sur une infrastructure distribuée sur une région Azure et gérée sous la forme de services séparés. Pour gérer les défaillances partielles, vous devez appliquer des techniques de haute disponibilité. Pour gérer les défaillances plus graves, par exemple consécutives à une catastrophe, exécutez des stratégies de récupération d’urgence.

Azure détecte et gère de nombreux exemples de défaillances, mais nombre de problèmes nécessitent le développement de stratégiques spécifiques aux applications. Vous devez préparer activement et gérer les défaillances d’applications, de services et de données.

Lorsque vous créez le plan de récupération d’urgence et de disponibilité de votre application, tenez compte des conséquences pour l’entreprise de la défaillance des applications. La définition des processus, des stratégies et des procédures nécessaires à la restauration des systèmes critiques après un événement catastrophique nécessite du temps, de l’engagement et des efforts de planification. Une fois les plans établis, vous devez poursuivre votre initiative. Vous devez régulièrement analyser, tester les plans et les améliorer continuellement en fonction de votre portefeuille d’applications, des besoins de votre entreprise et des technologies dont vous disposez. Azure vous procure de nouvelles fonctionnalités et soulève des obstacles inédits à la création d’applications robustes qui résistent aux défaillances.

## Ressources supplémentaires
[Haute disponibilité pour les applications développées sur Microsoft Azure](resiliency-high-availability-azure-applications.md)

[Récupération d’urgence des applications développées sur Microsoft Azure](resiliency-disaster-recovery-azure-applications.md)

[Guide technique de la résilience Azure](resiliency-technical-guidance.md)

[Vue d’ensemble : continuité des activités cloud et récupération d’urgence d’une base de données avec SQL Database](../sql-database/sql-database-business-continuity.md)

[Haute disponibilité et récupération d’urgence pour SQL Server dans Azure Virtual Machines](../virtual-machines/virtual-machines-windows-sql-high-availability-dr.md)

[Failsafe: guidance for resilient cloud architectures (Sécurité intégrée : recommandations applicables à la résilience des architectures cloud)](https://channel9.msdn.com/Series/FailSafe)

[Best Practices for the design of large-scale services on Azure Cloud Services (Meilleures pratiques de conception de services à grande échelle sur Azure Cloud Services)](https://azure.microsoft.com/blog/best-practices-for-designing-large-scale-services-on-windows-azure/)

## Étapes suivantes
Cet article fait partie d’une série d’articles axés sur la récupération d’urgence et la haute disponibilité pour les applications Azure. L’article suivant de cette série est [Haute disponibilité pour les applications développées sur Microsoft Azure](resiliency-high-availability-azure-applications.md).

<!---HONumber=AcomDC_0824_2016-->