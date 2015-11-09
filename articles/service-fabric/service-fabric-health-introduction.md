<properties
   pageTitle="Contrôle d’intégrité de Service Fabric | Microsoft Azure"
   description="Présentation du modèle de contrôle d’intégrité d’Azure Service Fabric, qui permet d’analyser le cluster, ses applications et ses services."
   services="service-fabric"
   documentationCenter=".net"
   authors="oanapl"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/23/2015"
   ms.author="oanapl"/>

# Présentation du contrôle d'intégrité de Service Fabric
Service Fabric introduit un modèle d'intégrité qui fournit une évaluation et des rapports d'intégrité riches, flexibles et extensibles. Il s'agit d'un contrôle quasiment en temps réel de l'état du cluster et des services qu'il exécute. Vous pouvez facilement obtenir les informations d'intégrité et prendre des mesures pour corriger les problèmes potentiels avant qu'ils ne s'enchaînent et provoquent des pannes massives. Le modèle standard implique que les services envoient des rapports en fonction de leur vue locale et les informations sont agrégées pour fournir une vue globale du cluster.

Les composants Service Fabric utilisent ce modèle d'intégrité pour signaler leur état actuel. Vous pouvez utiliser le même mécanisme pour établir des rapports sur l'intégrité de vos applications. La qualité et la richesse des rapports d'intégrité propres à vos conditions personnalisées déterminent la facilité avec laquelle vous pourrez détecter et résoudre les problèmes de votre application en cours d'exécution.

> [AZURE.NOTE]Nous avons créé le sous-système d'intégrité pour répondre au besoin des mises à niveau surveillées. Service Fabric inclut des mises à niveau surveillées qui permettent de mettre à niveau un cluster ou une application sans temps d'arrêt, avec une intervention de l'utilisateur minimale, voire nulle, et une disponibilité complète du cluster et des applications. Pour ce faire, la mise à niveau vérifie l'intégrité en fonction des stratégies de mise à niveau configurées et autorise l'installation uniquement si l'intégrité respecte les seuils souhaités. Sinon, la mise à niveau est automatiquement annulée ou suspendue pour offrir aux administrateurs la possibilité de corriger les problèmes. Pour en savoir plus sur les mises à niveau d'application, consultez [cet article](service-fabric-application-upgrade.md).

## Magasin d'intégrité
Le magasin d'intégrité conserve des informations sur l'intégrité des entités du cluster pour faciliter la récupération et l'évaluation. Il est implémenté en tant que service Service Fabric persistant avec état pour garantir un haut niveau de disponibilité et d'extensibilité. Il fait partie de l'application fabric:/System et est disponible dès que le cluster est opérationnel.

## Entités d'intégrité et hiérarchie
Les entités d'intégrité sont organisées dans une hiérarchie logique qui capture les interactions et les dépendances entre les différentes entités. Les entités et la hiérarchie sont générées automatiquement par le magasin d'intégrité en fonction des rapports provenant des composants Service Fabric.

Les entités d'intégrité reflètent les entités Service Fabric (par ex., l'entité application d'intégrité correspond à une instance d'application déployée dans le cluster, l'entité nœud d'intégrité correspond à un nœud du cluster Service Fabric). La hiérarchie d'intégrité capture les interactions entre les entités système et constitue la base d'une évaluation avancée de l'intégrité. Vous pouvez découvrir les concepts clés de Service Fabric dans la [Vue d'ensemble technique de Service Fabric](service-fabric-technical-overview.md). Pour plus d'informations sur l'application, consultez [Modèle d'application Service Fabric](service-fabric-application-model.md).

Les entités d'intégrité et la hiérarchie permettent un processus efficace de création de rapports, de débogage et d'analyse du cluster et des applications. Le modèle d'intégrité permet une représentation précise et **granulaire** de l'intégrité de nombreux éléments mobiles dans le cluster.

![Entités d'intégrité.][1] Entités d'intégrité, organisées dans une hiérarchie basée sur les relations parent-enfant.

[1]: ./media/service-fabric-health-introduction/servicefabric-health-hierarchy.png

Les entités d'intégrité sont les suivantes :

- **Cluster**. Représente l'intégrité d'un cluster Service Fabric. Les rapports d'intégrité de cluster décrivent les conditions qui affectent l'ensemble du cluster. Ils ne peuvent pas être limités à un ou plusieurs enfants défectueux. Exemple : Split-Brain du cluster en raison de problèmes liés au partitionnement réseau ou à la communication.

- **Nœud**. Représente l'intégrité d'un nœud Service Fabric. Les rapports d'intégrité de nœud décrivent les conditions qui affectent la fonctionnalité de nœud et qui affectent généralement toutes les entités déployées qui s'exécutent sur le nœud. Exemple : le nœud manque d'espace disque (ou d'une autre propriété au niveau de l'ordinateur comme la mémoire, les connexions, etc.) ou le nœud est inactif. L'entité de nœud est identifiée par le nom du nœud (chaîne).

- **Application**. Représente l'intégrité d'une instance d'application s'exécutant dans le cluster. Les rapports d'intégrité d'application décrivent les conditions qui affectent l'intégrité globale de l'application. Ils ne peuvent pas être limités à un enfant en particulier (services ou applications déployées). Exemple : interaction de bout en bout entre les différents services dans l'application. L'entité d'application est identifiée par le nom de l'application (URI).

- **Service**. Représente l'intégrité d'un service s'exécutant dans le cluster. Les rapports d'intégrité de service décrivent les conditions qui affectent l'intégrité globale du service. Ils ne peuvent pas être limités à une partition ou un réplica. Exemple : une configuration de service (par exemple, le partage de fichiers externes ou de ports) à l'origine de problèmes pour toutes les partitions. L'entité de service est identifiée par le nom du service (URI).

- **Partition**. Représente l'intégrité d'une partition de service. Les rapports d'intégrité de partition décrivent les conditions qui affectent le jeu entier de réplicas. Exemple : le nombre de réplicas est inférieur au nombre cible ou la partition est en perte de quorum. L'entité de partition est identifiée par l'ID de la partition (Guid).

- **Réplica**. Représente l'intégrité d'un réplica de service avec état ou d'une instance de service sans état. Il s'agit de la plus petite unité sur laquelle les agents de surveillance et les composants système peuvent établir des rapports pour une application. Exemple : pour les services avec état, le réplica principal peut signaler qu'il ne peut pas répliquer des opérations sur les serveurs secondaires ou que la réplication n'est pas effectuée au rythme attendu. Une instance sans état peut signaler qu'elle manque de ressources ou présente des problèmes de connectivité. L'entité de réplica est identifiée par l'ID de la partition (Guid) et l'ID de l'instance ou du réplica (long).

- **Application déployée**. Représente l'intégrité d'une *application s'exécutant sur un nœud*. Les rapports d'intégrité d'application déployée décrivent les conditions propres à l'application sur le nœud. Ils ne peuvent pas être limités aux packages de service déployés sur le même nœud. Exemple : le package d'application ne peut pas être téléchargé sur le nœud ou un problème est rencontré lors de la configuration des principaux de sécurité de l'application sur le nœud. L'application déployée est identifiée par le nom de l'application (URI) et le nom du nœud (chaîne).

- **Package de service déployé**. Représente l'intégrité d'un package de service d'une application s'exécutant dans un nœud du cluster. Il décrit les conditions propres à un package de service qui n'affectent pas les autres packages de service sur le même nœud pour la même application. Exemple : un package de code dans le package de service ne peut pas être démarré ou le package de configuration ne peut pas être lu. Le package de service déployé est identifié par le nom de l'application (URI), le nom du nœud (chaîne) et le nom du manifeste de service (chaîne).

La granularité du modèle d'intégrité facilite la détection et la correction des problèmes. Par exemple, si un service ne répond pas, il est possible d'indiquer que l'instance d'application est défectueuse. Toutefois, ce n'est pas la solution idéale, car le problème n'affecte peut-être pas tous les services de cette application. Le rapport doit être appliqué sur le service défectueux ou, si plus d'informations pointent vers une partition enfant spécifique, sur cette partition. Les données apparaissent automatiquement dans la hiérarchie : une partition défectueuse est visible au niveau du service et de l'application. Il est donc possible d'identifier et de résoudre la cause principale des problèmes plus rapidement.

La hiérarchie d'intégrité est composée de relations parent-enfant. Le cluster est composé de nœuds et d'applications. Les applications contiennent des services et des applications déployées. Les applications déployées contiennent des packages de service déployés. Les services contiennent des partitions, et chaque partition possède un ou plusieurs réplicas. Il existe une relation spéciale entre les nœuds et les entités déployées. Si un nœud est défectueux, comme indiqué par son composant système d'autorité (service Failover Manager), cela affecte les applications déployées, les packages de service et les réplicas déployés sur celui-ci.

La hiérarchie d'intégrité représente le dernier état du système basé sur les derniers rapports d'intégrité, c'est-à-dire, des informations quasiment en temps réel. Les agents de surveillance internes et externes peuvent établir des rapports sur les mêmes entités en fonction de la logique spécifique de l'application ou des conditions personnalisées surveillées. Les rapports utilisateur coexistent avec les rapports système.

En consacrant du temps pendant la conception du service à la planification des rapports d'intégrité et du mode de réponse en cas de problème, vous facilitez le débogage, la surveillance et, par conséquent, le fonctionnement des services cloud à grande échelle.

## États d'intégrité
Service Fabric utilise trois états d'intégrité pour décrire si une entité est intègre ou non : Ok, Warning et Error. Tous les rapports envoyés au magasin d'intégrité doivent spécifier l'un de ces états. Le résultat de l'évaluation d'intégrité est l'un de ces états.

Les états d'intégrité possibles sont les suivants :

- Ok : l'entité est saine. Aucun problème connu n'est signalé sur elle ou ses enfants (le cas échéant).

- Warning : l'entité rencontre des problèmes, mais elle n'est pas encore défectueuse (par ex., elle subit un délai inattendu qui n'entraîne pas de problème fonctionnel). Dans certains cas, la condition Warning peut se résoudre elle-même sans intervention spéciale, mais il est utile de savoir ce qui se passe. Dans d'autres cas, la condition Warning peut se dégrader en un grave problème si l'utilisateur n'intervient pas.

- Error : l'entité est défectueuse. Une action doit être prise pour résoudre l'état de l'entité, car elle ne peut pas fonctionner correctement.

- Unknown : l'entité n'existe pas dans le magasin d'intégrité. Ce résultat peut être obtenu à partir de requêtes distribuées, comme l'obtention des nœuds ou applications Service Fabric. Cette fusion de requêtes est le résultat de plusieurs composants système. Si un autre composant système a une entité qui n'a pas encore atteint le magasin d'intégrité ou qui a été supprimée de celui-ci, la requête fusionnée remplit les résultats d'intégrité avec un état d'intégrité « Unknown ».

## Stratégies d'intégrité
Le magasin d'intégrité applique des stratégies d'intégrité pour déterminer si une entité est saine en fonction de ses rapports et de ses enfants.

> [AZURE.NOTE]Les stratégies d'intégrité peuvent être spécifiées dans le manifeste de cluster (pour l'évaluation de l'intégrité des clusters et des nœuds) ou le manifeste d'application (pour l'évaluation de l'application et de tous ses enfants). Les demandes d'évaluation d'intégrité peuvent également transmettre des stratégies d'évaluation d'intégrité personnalisées, utilisées uniquement pour cette évaluation.

Par défaut, Service Fabric applique des règles strictes (tous les éléments doivent être sains) pour la relation hiérarchique parent-enfant. Si un enfant contient un événement défectueux, le parent est considéré comme étant défectueux.

### Stratégie d'intégrité de cluster
La stratégie d'intégrité de cluster est utilisée pour évaluer l'état d'intégrité du cluster et des nœuds. Elle peut être définie dans le manifeste de cluster. Sinon, la valeur par défaut est la stratégie par défaut (tolérance zéro sur les défaillances). Elle contient les éléments suivants :

- **ConsiderWarningAsError**. Spécifie s'il faut traiter les rapports d'intégrité Warning comme des erreurs pendant l'évaluation de l'intégrité. Valeur par défaut : false.

- **MaxPercentUnhealthyApplications**. Pourcentage maximum toléré d'applications pouvant être défectueuses avant que l'état d'intégrité du cluster ne soit considéré comme étant Error.

- **MaxPercentUnhealthyNodes**. Pourcentage maximum toléré de nœuds pouvant être défectueux avant que l'état d'intégrité du cluster ne soit considéré comme étant Error. Dans les clusters de grande taille, il y a toujours des nœuds inactifs ou en réparation, ce pourcentage doit donc être configuré pour tolérer cette condition.

Voici un extrait de manifeste de cluster :

```xml
<FabricSettings>
  <Section Name="HealthManager/ClusterHealthPolicy">
    <Parameter Name="ConsiderWarningAsError" Value="False" />
    <Parameter Name="MaxPercentUnhealthyApplications" Value="0" />
    <Parameter Name="MaxPercentUnhealthyNodes" Value="20" />
  </Section>
</FabricSettings>
```

### Stratégie d'intégrité d'application
La stratégie d'intégrité d'application décrit la procédure d'évaluation des événements et une agrégation des états des enfants est effectuée pour l'application et ses enfants. Elle peut être définie dans le manifeste d'application, ApplicationManifest.xml, dans le package d'application. Si aucune stratégie n'est spécifiée, Service Fabric suppose que l'entité est défectueuse si elle (ou un de ses enfants) se trouve dans un état d'intégrité Warning ou Error. Les stratégies configurables sont les suivantes :

- **ConsiderWarningAsError**. Spécifie s'il faut traiter les rapports d'intégrité Warning comme des erreurs pendant l'évaluation de l'intégrité. Valeur par défaut : false.

- **MaxPercentUnhealthyDeployedApplications**. Pourcentage maximum toléré d'applications déployées pouvant être défectueuses avant que l'état d'intégrité de l'application ne soit considéré comme étant Error. Il est calculé en divisant le nombre d'applications déployées défectueuses par le nombre de nœuds sur lesquels les applications sont actuellement déployées dans le cluster. Le calcul est arrondit pour tolérer une défaillance sur un petit nombre de nœuds. La valeur par défaut est 0 %.

- **DefaultServiceTypeHealthPolicy**. Spécifie la stratégie d'intégrité du type de service par défaut, qui remplacera la stratégie d'intégrité par défaut pour tous les types de service dans l'application.

- **ServiceTypeHealthPolicyMap**. Mappage des stratégies d'intégrité de service par type de service, qui remplace la stratégie d'intégrité de type de service par défaut pour les types de service spécifiés. Par exemple, dans une application qui contient un type de service Gateway sans état et un type de service Engine avec état, la stratégie d'intégrité du service avec et sans état peut être configurée différemment. La spécification de la stratégie par type de service permet un contrôle plus précis de l'intégrité du service.

### Stratégie d'intégrité de type de service
La stratégie d'intégrité de type de service spécifie comment évaluer et agréger les enfants d'un service. Elle contient les éléments suivants :

- **MaxPercentUnhealthyPartitionsPerService**. Pourcentage maximum toléré de partitions défectueuses avant qu'un service ne soit considéré comme étant défectueux. La valeur par défaut est 0 %.

- **MaxPercentUnhealthyReplicasPerPartition**. Pourcentage maximum toléré de réplicas défectueux avant qu'une partition ne soit considérée comme étant défectueuse. La valeur par défaut est 0 %.

- **MaxPercentUnhealthyServices**. Pourcentage maximum toléré de services défectueux avant que l'application ne soit considérée comme étant défectueuse. La valeur par défaut est 0 %.

Voici un extrait de manifeste d'application :

```xml
    <Policies>
        <HealthPolicy ConsiderWarningAsError="true" MaxPercentUnhealthyDeployedApplications="20">
            <DefaultServiceTypeHealthPolicy
                   MaxPercentUnhealthyServices="0"
                   MaxPercentUnhealthyPartitionsPerService="10"
                   MaxPercentUnhealthyReplicasPerPartition="0"/>
            <ServiceTypeHealthPolicy ServiceTypeName="FrontEndServiceType"
                   MaxPercentUnhealthyServices="0"
                   MaxPercentUnhealthyPartitionsPerService="20"
                   MaxPercentUnhealthyReplicasPerPartition="0"/>
            <ServiceTypeHealthPolicy ServiceTypeName="BackEndServiceType"
                   MaxPercentUnhealthyServices="20"
                   MaxPercentUnhealthyPartitionsPerService="0"
                   MaxPercentUnhealthyReplicasPerPartition="0">
            </ServiceTypeHealthPolicy>
        </HealthPolicy>
    </Policies>
```

## Évaluation de l'intégrité
Les utilisateurs ou des services automatisés peuvent évaluer l'intégrité de toutes les entités à n'importe quel point dans le temps. Pour évaluer l'intégrité d'une entité, le magasin d'intégrité regroupe tous les rapports d'intégrité sur l'entité et évalue tous ses enfants (le cas échéant). L'algorithme d'agrégation d'intégrité utilise les stratégies d'intégrité qui spécifient comment évaluer les rapports d'intégrité, ainsi que comment agréger les états d'intégrité des enfants (le cas échéant).

### Agrégation de rapports d'intégrité
Une entité peut avoir plusieurs rapports d'intégrité envoyés par différents rapporteurs (composants système ou agents de surveillance) sur différentes propriétés. L'agrégation utilise les stratégies d'intégrité associées, en particulier le membre ConsiderWarningAsError de la stratégie d'intégrité d'application ou de cluster, qui spécifie la façon d'évaluer les avertissements.

L'état d'intégrité agrégé est déclenché par les **pires** rapports d'intégrité sur l'entité. S'il existe au moins un rapport d'intégrité Error, l'état d'intégrité agrégé est Error.

![Agrégation de rapports d'intégrité avec un état Error.][2]

Les rapport d'intégrité Error place l'entité d'intégrité dans un état Error.

[2]: ./media/service-fabric-health-introduction/servicefabric-health-report-eval-error.png

S'il n'existe aucun rapport Error mais un ou plusieurs rapports Warning, l'état d'intégrité agrégé est Warning ou Error, en fonction de l'indicateur de stratégie ConsiderWarningAsError.

![Agrégation de rapports d'intégrité avec un état Warning et valeur de ConsiderWarningAsError égale à false.][3]

Agrégation de rapports d'intégrité avec un état Warning et valeur de ConsiderWarningAsError égale à false (valeur par défaut).

[3]: ./media/service-fabric-health-introduction/servicefabric-health-report-eval-warning.png

### Agrégation d'intégrité des enfants
L'état d'intégrité agrégé d'une entité reflète les états d'intégrité des enfants (le cas échéant). L'algorithme d'agrégation des états d'intégrité des enfants utilise les stratégies d'intégrité applicables selon le type d'entité.

![Agrégation d'intégrité des entités enfants][4]

Agrégation d'enfants basée sur les stratégies d'intégrité.

[4]: ./media/service-fabric-health-introduction/servicefabric-health-hierarchy-eval.png

Après avoir évalué tous les enfants, le magasin d'intégrité regroupe les états d'intégrité selon le pourcentage maximum configuré d'éléments défectueux de la stratégie basée sur le type d'entité et d'enfant.

- Si tous les enfants ont des états Ok, l'état d'intégrité agrégé des enfants est Ok.

- Si des enfants ont des états Ok et des états Warning, l'état d'intégrité agrégé des enfants est Warning.

- S'il existe des enfants avec un état Error dont le nombre dépasse le pourcentage maximum autorisé d'enfants défectueux, l'état d'intégrité agrégé est Error.

- Si le nombre d'enfants ayant un état Error ne dépasse pas le pourcentage maximum autorisé d'enfants défectueux, l'état d'intégrité agrégé est Warning.

## Rapports d'intégrité
Les composants système et les agents de surveillance internes/externes peuvent établir des rapports sur les entités Service Fabric. Les *rapporteurs* déterminent **localement** l'intégrité de l'entité analysée en fonction de certaines conditions. Ils n'ont pas besoin d'examiner les données d'agrégation et l'état global. Cela est d'ailleurs préférable, car les rapporteurs deviendraient des organismes complexes devant examiner de nombreux éléments pour en déduire les informations à envoyer.

Pour envoyer des données d'intégrité au magasin d'intégrité, les rapporteurs doivent identifier l'entité affectée et créer un rapport d'intégrité. Le rapport peut ensuite être envoyé via une API avec FabricClient.HealthManager.ReportHealth, via Powershell ou via REST.

### Rapports d'intégrité
Les rapports d'intégrité de chaque entité du cluster contiennent les informations suivantes :

- ID de la source (SourceId). Chaîne qui identifie de façon unique le rapporteur de l'événement d'intégrité.

- Identificateur de l'entité. Identifie l'entité sur laquelle le rapport est appliqué. Il diffère selon le [type d'entité](service-fabric-health-introduction.md#health-entities-and-hierarchy) :

  - Cluster : aucun

  - Nœud : nom du nœud (chaîne).

  - Application : nom de l'application (URI). Représente le nom de l'instance d'application déployée dans le cluster.

  - Service : nom du service (URI). Représente le nom de l'instance de service déployée dans le cluster.

  - Partition : ID de la partition (GUID). Représente l'identificateur de partition unique.

  - Réplica : ID du réplica du service avec état ou ID de l'instance du service sans état (Int64).

  - Application déployée : nom de l'application (URI) et nom du nœud (string).

  - Package de service déployé : nom de l'application (URI), nom du nœud (chaîne) et nom du manifeste de service (chaîne).

- Propriété (property). *Chaîne* (et non énumération fixe) qui permet au rapporteur de classer l'événement d'intégrité pour une propriété spécifique de l'entité. Par exemple, le rapporteur A peut établir un rapport d'intégrité sur la propriété « stockage » du Nœud01 et le rapporteur B sur la propriété « connectivité » du Nœud01. Ces deux rapports sont traités comme des événements d'intégrité distincts dans le magasin d'intégrité pour l'entité Nœud01.

- Description. Chaîne qui permet au rapporteur de fournir des informations détaillées sur l'événement d'intégrité. L'ID de la source, la propriété et l'état d'intégrité doivent donner une description complète du rapport. La description ajoute des informations explicites sur le rapport pour le rendre plus facile à comprendre pour les administrateurs et les utilisateurs.

- État d'intégrité (HealthState). [Énumération](service-fabric-health-introduction.md#health-states) qui décrit l'état d'intégrité du rapport. Les valeurs acceptées sont OK, Warning et Error.

- Durée de vie (TimeToLive). Intervalle de temps indiquant la durée de validité du rapport d'intégrité. Associé à RemoveWhenExpired, il indique au magasin d'intégrité comment évaluer les événements expirés. Par défaut, la valeur est infinie et le rapport est toujours valide.

- RemoveWhenExpired. Valeur booléenne. Si cette valeur est true, le rapport d'intégrité expiré est automatiquement supprimé du magasin d'état sans impact sur l'évaluation de l'intégrité de l'entité. Ce paramètre est utilisé quand le rapport est valide pour une période donnée uniquement et que le rapporteur n'a pas besoin de l'effacer explicitement. Il est également utilisé pour supprimer des rapports du magasin d'intégrité. Par exemple, un agent de surveillance est modifié et cesse d'envoyer des rapports avec la source et la propriété précédentes. Par conséquent, il peut envoyer un rapport avec une petite durée de vie et avec RemoveWhenExpired pour effacer les états précédents dans le magasin d'intégrité. Si la valeur est false, le rapport expiré est traité comme une erreur pendant l'évaluation de l'intégrité. Ce paramètre indique au magasin d'intégrité que la source doit établir périodiquement un rapport sur cette propriété, sinon, il est probable que l'agent de surveillance présente un problème. L'intégrité de l'agent de surveillance est capturée en tenant compte de l'événement en tant qu'erreur.

- Numéro de séquence (SequenceNumber). Entier positif qui doit être croissant, car il représente l'ordre des rapports. Ce paramètre est utilisé par le magasin d'intégrité pour détecter les rapports obsolètes, reçus en retard en raison de délais sur le réseau ou d'autres problèmes. Les rapports sont rejetés si le numéro de séquence est inférieur ou égal au dernier numéro appliqué à la même entité, source et propriété. Le numéro de séquence est généré automatiquement, s'il n'est pas spécifié. Il n'est nécessaire que lors de la création de rapports sur les transitions d'état : la source doit se rappeler les rapports envoyés et conserver les informations en vue de leur récupération en cas de basculement.

L'ID de la source, l'identificateur d'entité, la propriété et l'état d'intégrité sont requis pour tous les rapports d'intégrité. La chaîne d'ID de la source ne doit pas commencer par le préfixe « System. », réservé aux rapports système. Pour la même entité, il n'existe qu'un seul rapport pour les mêmes source et propriété, si plusieurs rapports sont générés pour la même source et la même propriété, ils se substituent mutuellement, aussi bien sur le client d'intégrité (s'ils sont traités par lot) que dans le magasin d'intégrité. Le remplacement s'effectue en fonction du numéro de séquence : les rapports les plus récents (avec un numéro de séquence supérieur) remplacent les rapports les plus anciens.

### Événements d'intégrité
En interne, le magasin d'intégrité conserve les événements d'intégrité, qui contiennent toutes les informations des rapports ainsi que des métadonnées supplémentaires, par exemple, l'heure à laquelle le rapport a été remis au client d'intégrité et l'heure à laquelle il a été modifié côté serveur. Les événements d'intégrité sont retournés par les [requêtes d'intégrité](service-fabric-view-entities-aggregated-health.md#health-queries).

Les métadonnées ajoutées sont notamment :

- SourceUtcTimestamp : heure à laquelle le rapport a été remis au client d'intégrité (UTC)

- LastModifiedUtcTimestamp : heure de la dernière modification du rapport côté serveur (UTC)

- IsExpired : indique si le rapport avait expiré au moment où la requête a été exécutée par le magasin d'intégrité. Un événement a expiré uniquement si RemoveWhenExpired a la valeur false, sinon, l'événement n'est pas retourné par la requête, il est supprimé du magasin.

- LastOkTransitionAt, LastWarningTransitionAt, LastErrorTransitionAt : heure des dernières transitions des états Ok/Warning/Error. Ces champs fournissent l'historique de la transition des états d'intégrité pour l'événement.

Les champs de transition d'état peuvent être utilisés pour un niveau d'alerte plus intelligent ou pour des informations sur les événements d'intégrité « historiques ». Ils permettent les scénarios suivants :

- Alerte quand une propriété a eu la valeur Error/Warning pendant plus de X minutes. Cela évite les alertes sur les conditions temporaires. Par exemple : une alerte indiquant que l'état d'intégrité a eu la valeur Warning pendant plus de 5 minutes peut être traduite en (HealthState == Warning et Now - LastWarningTransitionTime)
> 5 minutes).

- Alerte uniquement sur les conditions modifiées au cours des X dernières minutes. Si un rapport indiquait déjà l'état Error avant cette période, il peut être ignoré (car il a déjà été signalé précédemment).

- Si une propriété oscille entre Warning et Error, détermine la durée pendant laquelle elle a été défectueuse (donc pas Ok). Par exemple : une alerte indiquant que la propriété a été défectueuse pendant plus de 5 minutes peut être traduite en : (HealthState != Ok et Now - LastOkTransitionTime > 5 minutes).

## Exemple : évaluer et établir un rapport sur l'intégrité de l'application
L'exemple suivant envoie un rapport d'intégrité via Powershell sur l'application nommée fabric:/WordCount à partir de la source MyWatchdog. Le rapport d'intégrité contient des informations sur la propriété d'intégrité Disponibilité dans un état d'intégrité Error avec une durée de vie infinie. Il interroge ensuite l'intégrité de l'application, qui retourne l'état d'intégrité agrégé Error et l'événement d'état signalé dans la liste des événements d'intégrité.

```powershell
PS C:\> Send-ServiceFabricApplicationHealthReport –ApplicationName fabric:/WordCount –SourceId "MyWatchdog" –HealthProperty "Availability" –HealthState Error

PS C:\> Get-ServiceFabricApplicationHealth fabric:/WordCount

ApplicationName                 : fabric:/WordCount
AggregatedHealthState           : Error
UnhealthyEvaluations            :
                                  Error event: SourceId='MyWatchdog', Property='Availability'.

ServiceHealthStates             :
                                  ServiceName           : fabric:/WordCount/WordCount.Service
                                  AggregatedHealthState : Warning

                                  ServiceName           : fabric:/WordCount/WordCount.WebService
                                  AggregatedHealthState : Ok

DeployedApplicationHealthStates :
                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : Node.4
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : Node.1
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : Node.5
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : Node.2
                                  AggregatedHealthState : Ok

                                  ApplicationName       : fabric:/WordCount
                                  NodeName              : Node.3
                                  AggregatedHealthState : Ok

HealthEvents                    :
                                  SourceId              : System.CM
                                  Property              : State
                                  HealthState           : Ok
                                  SequenceNumber        : 5102
                                  SentAt                : 4/15/2015 5:29:15 PM
                                  ReceivedAt            : 4/15/2015 5:29:15 PM
                                  TTL                   : Infinite
                                  Description           : Application has been created.
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : ->Ok = 4/15/2015 5:29:15 PM

                                  SourceId              : MyWatchdog
                                  Property              : Availability
                                  HealthState           : Error
                                  SequenceNumber        : 130736794527105907
                                  SentAt                : 4/16/2015 5:37:32 PM
                                  ReceivedAt            : 4/16/2015 5:37:32 PM
                                  TTL                   : Infinite
                                  Description           :
                                  RemoveWhenExpired     : False
                                  IsExpired             : False
                                  Transitions           : ->Error = 4/16/2015 5:37:32 PM

```

## Utilisation du modèle d'intégrité
Le modèle d'intégrité permet aux services cloud et à la plateforme sous-jacente Service Fabric de monter en charge, car la détermination de la surveillance et de l'intégrité est répartie entre les différents moniteurs au sein du cluster. D'autres systèmes disposent d'un service centralisé unique au niveau du cluster qui analyse toutes les informations *potentiellement* utiles émises par les services. Cela entrave leur extensibilité et ne leur permet pas de collecter des informations très spécifiques pour aider à identifier les problèmes et les problèmes potentiels aussi près de la cause principale que possible.

Le modèle d'intégrité est très utilisé pour la surveillance et le diagnostic, pour évaluer l'intégrité du cluster et des applications, et pour les mises à niveau surveillées. D'autres services utilisent les données d'intégrité pour effectuer des réparations automatiques, générer l'historique d'intégrité du cluster et émettre des alertes sous certaines conditions.

## Étapes suivantes
[Comment afficher les rapports d'intégrité de Service Fabric](service-fabric-view-entities-aggregated-health.md)

[Utilisation des rapports d'intégrité système pour la résolution des problèmes](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

[Ajout de rapports d'intégrité de Service Fabric personnalisés](service-fabric-report-health.md)

[Comment analyser et diagnostiquer des services localement](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Mise à niveau des applications Service Fabric](service-fabric-application-upgrade.md)
 

<!---HONumber=Nov15_HO1-->