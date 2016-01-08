<properties
   pageTitle="Guide de surveillance et de diagnostic | Microsoft Azure"
   description="Meilleures pratiques pour la surveillance des applications distribuées dans le cloud."
   services=""
   documentationCenter="na"
   authors="dragon119"
   manager="masimms"
   editor=""
   tags=""/>

<tags
   ms.service="best-practice"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/28/2015"
   ms.author="masashin"/>

# Guide de surveillance et de diagnostic

![](media/best-practices-monitoring/pnp-logo.png)

## Vue d’ensemble
Les applications et services distribués exécutés dans le cloud sont, par leur nature, les parties complexes des logiciels comprenant de nombreux éléments mobiles. Dans un environnement de production, il est important de pouvoir contrôler la méthode selon laquelle les utilisateurs utilisent votre système, suivre l’utilisation des ressources et généralement surveiller l’intégrité, ainsi que les performances de votre système. Ces informations peuvent être utilisées comme aide au diagnostic pour détecter et corriger les problèmes et également aider à identifier des problèmes potentiels et les empêcher de se produire.

## Scénarios de surveillance et de diagnostic
La surveillance vous permet d’avoir un aperçu de la manière selon laquelle un système fonctionne et est un élément essentiel du maintien des objectifs de qualité de service. Certains scénarios courants pour la collecte des données de surveillance sont les suivants :

- S’assurer que le système reste sain.
- Suivre la disponibilité du système et de ses composants.
- Maintenir les performances pour s’assurer que le débit du système ne se dégrade pas inopinément à mesure que le volume de travail augmente.
- Garantir que le système respecte les SLA conclus avec les clients.
- Protéger la confidentialité et la sécurité du système, des utilisateurs et de leurs données.
- Suivre les opérations qui sont effectuées lors d’audit ou à des fins de réglementation.
- Surveiller l’utilisation quotidienne du système et identifier les tendances qui pourraient entraîner des problèmes si elles ne sont pas traitées.
- Suivre les problèmes qui surviennent, à partir d’un rapport initial via l’analyse des causes possibles, de la rectification, des mises à jour logicielles consécutives et du déploiement.
- Suivre les opérations et les versions de débogage.

> [AZURE.NOTE]Cette liste n’est pas exhaustive. Ce document se concentre sur ces scénarios considérés comme les situations les plus courantes pour effectuer la surveillance, mais il peut exister d’autres scénarios moins courants ou propres à votre environnement.

Les sections suivantes décrivent ces scénarios plus en détail. Les informations pour chaque scénario sont décrites selon le format suivant :

- Une brève vue d’ensemble du scénario.
- Les conditions requises typiques de ce scénario.
- Les données d’instrumentation brutes requises pour prendre en charge le scénario et les sources possibles de ces informations.
- La méthode d’analyse et d’association de ces données brutes pour générer des informations de diagnostic significatives.

## Surveillance de l’intégrité
Un système est sain s’il est en cours d’exécution et capable de traiter les demandes. L’objectif de la surveillance de l’intégrité consiste à générer un instantané de l’intégrité actuelle du système pour vous permettre de vérifier que tous les composants du système fonctionnent comme prévu.

### Conditions requises pour la surveillance de l’intégrité
Un opérateur doit être averti rapidement (en quelques secondes) si une partie du système est considérée comme défectueuse. L’opérateur doit être en mesure de déterminer les parties du système qui fonctionnent normalement et celles qui rencontrent des problèmes. L’intégrité du système peut être mise en surbrillance à l’aide d’un système de feux de circulation ; rouge pour défectueux (le système est arrêté), jaune pour partiellement sain (le système s’exécute avec des fonctionnalités réduites) et vert pour complètement sain.

Un système complet de surveillance de l’intégrité permet à un opérateur d’explorer le système pour afficher l’état d’intégrité des sous-systèmes et des composants. Par exemple, si l’ensemble du système est représenté comme partiellement sain, l’opérateur doit être en mesure de zoomer et de déterminer la fonctionnalité qui est actuellement indisponible.

### Sources de données, instrumentation et conditions de collecte de données
Les données brutes requises pour prendre en charge la surveillance de l’intégrité peuvent être générées après avoir effectué les opérations suivantes :

- Le suivi de l’exécution des demandes utilisateur. Ces informations peuvent être utilisées pour déterminer les demandes qui ont réussi, celles qui ont échoué et le temps de réponse pour chaque demande.
- La surveillance des utilisateurs synthétiques. Ce processus simule les étapes effectuées par un utilisateur et suit une série d’étapes prédéfinie. Les résultats de chaque étape doivent être capturés.
- L’enregistrement des exceptions, erreurs et avertissements. Ces informations peuvent être capturées après avoir suivi les instructions intégrées dans le code d’application, ainsi que récupéré des informations sur les journaux des événements de tout service référencé par le système.
- La surveillance de l’intégrité de tous les services tiers utilisés par le système. Cela peut nécessiter la récupération et l’analyse des données d’intégrité fournies par ces services et ces informations peut avoir une variété de formats.
- La surveillance de point de terminaison. Ce mécanisme est décrit plus en détail dans la section Surveillance de la disponibilité.
- La collecte des informations de performances ambiantes, telles que l’utilisation du processeur d’arrière-plan ou l’activité E/S (y compris le réseau).

### Analyse des données d’intégrité
Le principal objectif de la surveillance d’intégrité est d’indiquer rapidement si le système est en cours d’exécution. Une analyse à chaud des données immédiates peut déclencher une alerte si un composant critique est détecté comme étant défectueux (il ne parvient pas à répondre à une série consécutive de pings, par exemple). L’opérateur peut prendre ensuite l’action corrective appropriée.

Un système plus avancé peut inclure un élément prédictif qui effectue une analyse à froid sur les charges de travail actuelles et récentes pour identifier des tendances et déterminer si le système est susceptible de rester sain ou si des ressources supplémentaires seront requises. Cet élément prédictif doit être basé sur des métriques de performances critiques, telles que le taux de demandes dirigées vers chaque service ou sous-système, les temps de réponse de ces demandes et le volume de données échangées avec chaque service. Si la valeur de toute mesure dépasse un seuil défini, le système peut déclencher une alerte pour permettre à un opérateur ou la mise à l’échelle automatique (si disponible) de prendre les mesures préventives nécessaires pour maintenir l’intégrité du système. Ces actions peuvent impliquer l’ajout de ressources, le redémarrage d’un ou de plusieurs services défectueux, ou l’application d’une limitation aux demandes de faible priorité.

## Surveillance de la disponibilité
Un système véritablement sain nécessite que les composants et les sous-systèmes constituant le système soient disponibles. La surveillance de la disponibilité est étroitement liée à la surveillance de l’intégrité, mais alors que la surveillance de l’intégrité fournit une vue immédiate de l’intégrité actuelle du système, la surveillance de la disponibilité concerne la disponibilité du système et de ses composants afin de générer des statistiques concernant le temps d’activité du système.

Dans de nombreux systèmes, certains composants (par exemple, une base de données) sont configurés avec une redondance intégrée pour permettre un basculement rapide en cas d’erreur grave ou d’une perte de connectivité. Dans l’idéal, les utilisateurs ne doivent pas être conscients que ce type de défaillance s’est produit, mais du point de vue de la surveillance de la disponibilité, il est nécessaire de recueillir autant d’informations que possible sur ces défaillances pour essayer d’en déterminer la cause et de prendre les mesures correctives adéquates pour empêcher qu’elles se reproduisent.

Les données requises pour effectuer le suivi de la disponibilité peuvent dépendre de plusieurs facteurs de niveau inférieur, qui peuvent être propres à l’application, au système et à l’environnement. Un système de surveillance efficace capture les données de disponibilité qui correspondent à ces facteurs de niveau inférieur, puis les agrège afin de donner une vue d’ensemble du système. Par exemple, dans un système de commerce électronique, la fonctionnalité d’entreprise qui permet à un client de passer des commandes peut dépendre du référentiel dans lequel les détails de la commande sont stockés et du système de paiement qui gère les transactions monétaires pour le paiement de ces commandes. La disponibilité de la partie du système liée à la passation des commandes est donc fonction de la disponibilité du référentiel et du sous-système de paiement.

### Conditions requises pour la surveillance de la disponibilité
Un opérateur doit également être en mesure d’afficher la disponibilité historique de chaque système et sous-système et utiliser ces informations pour identifier les tendances susceptibles de provoquer une ou plusieurs défaillances du sous-système (les défaillances des services commencent-elles à un moment précis de la journée qui correspond aux heures de traitement de pointe ?).

En plus de fournir une vue immédiate et historique de la disponibilité ou sinon de chaque sous-système, une solution de surveillance doit également être capable d’alerter rapidement un opérateur en cas de défaillance d’un ou plusieurs services, ou les utilisateurs seront dans l’impossibilité de se connecter aux services. Il ne s’agit pas simplement de surveiller chaque service, mais également d’examiner les actions effectuées par chaque utilisateur en cas d’échec de ces actions lorsqu’ils tentent de communiquer avec un service. Dans une certaine mesure, un degré d’échec de la connectivité est normal et peut être dû à des erreurs temporaires, mais il peut être utile d’autoriser le système à déclencher une alerte concernant le nombre d’échecs de connectivité à un sous-système spécifié se produisant durant une période spécifique.

### Sources de données, instrumentation et conditions de collecte de données
Comme pour la surveillance d’intégrité, les données brutes requises pour prendre en charge la surveillance de la disponibilité peuvent être générées suite à la surveillance d’utilisateurs synthétiques et à l’enregistrement de l’ensemble des exceptions, erreurs et avertissements pouvant se produire. En outre, les données de disponibilité peuvent être obtenues à partir de la surveillance de point de terminaison. L’application peut exposer un ou plusieurs points de terminaison d’intégrité, chacun d’entre eux testant l’accès à une zone fonctionnelle du système. Le système de surveillance peut envoyer une commande ping sur chaque point de terminaison suivant un calendrier défini et collecter les résultats (réussite ou échec).

Tous les délais d’attente et les échecs de connectivité au réseau, ainsi que les nouvelles tentatives de connexion, doivent être enregistrés. Toutes les données doivent être horodatées.

<a name="analyzing-availablity-data"></a>
### Analyse des données de disponibilité
Les données d’instrumentation doivent être agrégées et corrélées pour prendre en charge les types d’analyse suivants :

- La disponibilité immédiate du système et des sous-systèmes.
- Les taux d’échec de la disponibilité du système et des sous-systèmes. Dans l’idéal, un opérateur doit être en mesure de mettre en corrélation les défaillances avec des activités spécifiques ; que s’est-il passé lorsque le système a échoué ?
- Une vue historique des taux de défaillance du système ou des sous-systèmes sur une période spécifiée et le chargement sur le système (nombre de demandes utilisateur par exemple) lorsqu’une erreur s’est produite.
- Les raisons de l’indisponibilité du système ou des sous-systèmes. Par exemple, service n’étant pas en cours d’exécution, perte de connectivité, connecté mais arrivant à expiration et connecté mais renvoyant des erreurs.

Vous pouvez calculer le pourcentage de disponibilité d’un service sur une période de temps à l’aide de la formule suivante :

```
%Availability =  ((Total Time – Total Downtime) / Total Time ) * 100
```

Ce calcul est utile dans le cadre des SLA (La [Surveillance des SLA](#SLA-monitoring) est décrite en détail plus loin dans ce guide). La définition de _temps mort_ dépend du service. Par exemple, Visual Studio Team Services définit les temps morts en tant que période pendant laquelle les tentatives du client pour accéder au service prennent plus de 120 secondes et toutes les opérations de lecture et d’écriture de base échouent une fois la connexion établie dans ce délai.

## Analyse des performances
Comme le système est placé sous contrainte croissante à mesure que le volume d’utilisateurs augmente, ainsi que la taille des jeux de données auxquels ces utilisateurs accèdent, la défaillance d’un ou plusieurs composants devient de plus en plus probable. Fréquemment, la défaillance d’un composant est précédée par une baisse des performances. Si vous êtes en mesure de détecter cette baisse, vous pouvez prendre des mesures proactives pour y remédier.

Les performances du système dépendent de plusieurs facteurs. Chaque facteur est généralement mesuré à l’aide d’indicateurs clés de performance (ICP), tels que le nombre de transactions de base de données par seconde ou le volume de demandes réseau traitées avec succès dans une période donnée. Certains de ces indicateurs peuvent être disponibles en tant que mesures de performances spécifiques, tandis que d’autres peuvent être dérivés d’une combinaison de métriques.

> [AZURE.NOTE]La détermination des bonnes ou mauvaises performances nécessite que vous compreniez le niveau de performance auquel le système doit être capable de fonctionner. Ceci implique d’observer le système pendant qu’il fonctionne sous une charge normale et de capturer des données pour chaque ICP sur une période donnée. Cela peut impliquer de faire fonctionner le système sous une charge simulée dans un environnement de test et de collecter les données appropriées avant de déployer le système dans un environnement de production.

> Vous devez également vous assurer que la surveillance de la performance ne devient pas une charge injustifiée sur le système. Vous pourrez ajuster dynamiquement le niveau de détail concernant les données que le processus de surveillance des performances rassemble.

### Conditions requises pour la surveillance des performances
Pour examiner les performances du système, un opérateur devra généralement consulter un certain nombre d’informations, notamment :

- Les taux de réponse pour les demandes utilisateur.
- Le nombre de demandes utilisateur simultanées.
- Le volume du trafic réseau.
- Les vitesses auxquelles les transactions sont terminées.
- Le temps de traitement moyen des demandes.

Il peut également être utile de fournir des outils qui permettent à un opérateur de repérer des corrélations, par exemple :

- Le nombre d’utilisateurs simultanés par rapport aux temps de latence des demandes (combien de temps faut-il pour démarrer le traitement d’une demande une fois que l’utilisateur l’a envoyée).
- Le nombre d’utilisateurs simultanés par rapport aux temps de réponse moyens (combien de temps faut-il pour effectuer une demande après le début de traitement).
- Le volume de demandes par rapport au nombre d’erreurs de traitement.

En plus d’accéder à ces informations fonctionnelles de haut niveau, un opérateur doit également être capable d’obtenir une vue détaillée des performances pour chaque composant du système. Ces données sont généralement fournies à l’aide de compteurs de performance de niveau inférieur qui suivent les informations comme :

- L’utilisation de la mémoire.
- Le nombre de threads.
- Le temps de traitement du processeur.
- La longueur de la file d’attente de la demande.
- Les taux et erreurs d’E/S disque ou réseau.
- Le nombre d’octets écrits ou lus.
- Les indicateurs d’intergiciel, tels que la longueur de la file d’attente.

Toutes les visualisations doivent permettre à un opérateur de spécifier une période ; les données affichées peuvent être un instantané de la situation actuelle et/ou une vue historique des performances.

Un opérateur doit être en mesure de déclencher une alerte basée sur une mesure des performances pour toute valeur donnée durant n’importe quel intervalle de temps spécifié.

### Sources de données, instrumentation et conditions de collecte de données
Les données de performances de haut niveau (débit, nombre d’utilisateurs simultanés, nombre de transactions commerciales, taux d’erreur, etc.) peuvent être collectées en surveillant la progression des demandes des utilisateurs lorsqu’elles arrivent et passent par le système. Cela implique d’incorporer des instructions de suivi à des points clés dans le code d’application ainsi que des informations de minutage. L’ensemble des erreurs, exceptions et avertissements doit être capturé avec des données suffisantes pour permettre leur mise en corrélation avec les demandes qui les ont générés. Le journal IIS est une autre source utile.

Vous devez également capturer, si possible, les données de performances pour les systèmes externes que l’application utilise. Ces systèmes externes peuvent fournir leurs propres compteurs de performances ou d’autres fonctionnalités pour demander des données de performances. Si ce n’est pas possible, vous devez enregistrer des informations, telles que l’heure de début et de fin de chaque demande adressée à un système externe, ainsi que l’état (réussite, échec ou avertissement) de l’opération. Par exemple, vous pouvez utiliser une approche de type chronomètre pour chronométrer les demandes, démarrer un minuteur au démarrage de la demande, puis arrêter le minuteur lorsque la demande est terminée.

Les données de performance de niveau inférieur pour les composants individuels dans un système peuvent être disponibles via des fonctionnalités telles que les compteurs de performances Windows et les diagnostics Microsoft Azure.

### Analyse des données de performances
L’essentiel du travail d’analyse consiste à agréger des données de performances par type de demande utilisateur (comme ajouter un article dans un panier d’achat ou exécuter le processus de paiement dans un système de commerce électronique) et/ou le sous-système ou le service auquel chaque demande est envoyée.

Une autre exigence commune est de synthétiser les données de performances dans les centiles sélectionnés. Par exemple, déterminer les temps de réponse de 99 % des demandes, 95 % des demandes et 70 % des demandes. Il peut exister des cibles de contrats SLA ou d’autres objectifs définis pour chaque centile. Les résultats en cours doivent être signalés à la fois presque en temps réel pour permettre de détecter les problèmes immédiats, et également en tant que données agrégées sur un temps plus long à des fins statistiques.

Dans le cas de problèmes de latence ayant un impact sur les performances, un opérateur doit être rapidement en mesure d’identifier la cause du goulot d’étranglement en examinant la latence de chaque étape effectuée pour chaque demande. Les données de performances doivent donc fournir un moyen de mettre en corrélation des mesures de performances pour chaque étape afin de les associer à une demande spécifique.

Selon les spécifications de visualisation, il peut être utile de générer et de stocker un cube de données contenant des vues des données brutes pour autoriser les demandes ad hoc complexes et l’analyse des informations de performances.

## Surveillance de la sécurité
Tous les systèmes commerciaux qui contiennent des données sensibles doivent implémenter une structure de sécurité. La complexité du mécanisme de sécurité est généralement fonction de la sensibilité des données. Dans un système qui requiert l’authentification des utilisateurs, vous devez enregistrer toutes les tentatives de connexion, si elles échouent ou réussissent. En outre, toutes les opérations effectuées et les détails de toutes les ressources auxquelles un utilisateur authentifié a eu accès, doivent être consignés. Lorsque l’utilisateur met fin à sa session et se déconnecte, ces informations doivent également être enregistrées.

La surveillance peut permettre de détecter les attaques sur le système. Par exemple, un grand nombre de tentatives de connexion peut indiquer une attaque en force brute, ou un bond inattendu du nombre de demandes peut être le résultat d’une attaque DDoS. Vous devez être prêt à surveiller toutes les demandes pour toutes les ressources indépendamment de la source de ces demandes ; un système avec un problème de connexion peut exposer accidentellement des ressources au monde extérieur sans exiger qu’un utilisateur se connecte.

### Conditions requises pour la surveillance de la sécurité
Les aspects les plus critiques de surveillance de la sécurité doivent rapidement permettre à un opérateur d’effectuer les actions suivantes :

- Détecter les tentatives d’intrusion par une entité non authentifiée.
- Identifier les tentatives des entités d’effectuer des opérations sur les données auxquelles aucun accès ne leur a été accordé.
- Déterminer si le système, ou une partie du système, est en train de subir une attaque interne ou externe (par exemple, un utilisateur authentifié malveillant peut essayer d’arrêter le système).

Pour prendre en charge ces conditions, un opérateur doit être informé des éléments suivants :

- Tout échec répété des tentatives de connexion effectuées par le même compte dans une période spécifiée.
- Si le même compte authentifié tente d’accéder plusieurs fois à une ressource interdite pendant une période spécifiée.
- Si un grand nombre de demandes non authentifiées ou non autorisées sont envoyées pendant une période spécifiée.

Les informations fournies à un opérateur doivent inclure l’adresse hôte de la source pour chaque demande. Si des violations de sécurité se produisent régulièrement à partir d’une plage spécifique d’adresses, ces hôtes peuvent alors être bloqués.

Une partie essentielle du maintien de la sécurité d’un système est de pouvoir détecter rapidement les actions qui s’écartent du modèle habituel. Des informations, telles que le nombre de demandes de connexions échouées et/ou réussies, peuvent être affichées visuellement pour permettre de détecter la présence d’un pic d’activité à un moment inhabituel (par exemple, des utilisateurs se connectant à 3 h et effectuant un grand nombre d’opérations alors que leur journée de travail commence à 9 h). Ces informations peuvent également être utilisées pour permettre de configurer la mise à l’échelle automatique temporelle. Par exemple, si un opérateur observe qu’un grand nombre d’utilisateurs se connectent régulièrement à un moment précis de la journée, l’opérateur peut mettre en place des services d’authentification supplémentaires pour gérer le volume de travail, puis arrêter ces services supplémentaires à la fin du pic.

### Sources de données, instrumentation et conditions de collecte de données
La sécurité est un aspect global de la plupart des systèmes distribués et les données pertinentes sont susceptibles d’être générées en plusieurs points dans un système. Vous devez envisager d’adopter une approche SIEM pour rassembler les informations relatives à la sécurité résultant des événements déclenchés par l’application, l’équipement réseau, les serveurs, les pare-feux, les logiciels antivirus et autres éléments de prévention des intrusions.

La surveillance de la sécurité peut incorporer des données à partir d’outils qui ne font pas partie de votre application, tels que les utilitaires qui identifient les activités d’analyse de port par des agences externes ou des filtres réseau qui détectent les tentatives d’accès non authentifié à votre application et vos données.

Dans tous les cas, les données collectées doivent permettre à un administrateur de déterminer la nature d’une attaque et de prendre les mesures appropriées.

### Analyse des données de sécurité
Une fonctionnalité de la surveillance de la sécurité concerne la variété des sources à partir desquelles les données émanent. Les différents formats et le niveau de détail nécessitent souvent une analyse complexe des données capturées pour les relier à un thread d’informations cohérent. Hormis les cas les plus simples (par exemple, la détection d’un grand nombre d’échecs de connexion ou de tentatives répétées d’accès non autorisé aux ressources critiques), il peut être impossible d’effectuer un traitement automatisé complexe des données de sécurité et, à la place, il peut être préférable d’écrire ces données, horodatées dans leur forme d’origine, dans un référentiel sécurisé pour permettre une analyse manuelle par un expert.

<a name="SLA-monitoring"></a>

## Surveillance des contrats SLA
De nombreux systèmes commerciaux qui prennent en charge les clients payants garantissent les performances du système sous la forme de contrats SLA. Essentiellement, les contrats SLA stipulent que le système peut gérer un volume de travail défini au cours d’une période convenue, sans perdre les informations critiques. La surveillance des contrats SLA vise à garantir que le système peut respecter les contrats SLA mesurables.

> [AZURE.NOTE]La surveillance des contrats SLA est étroitement liée à la surveillance des performances, mais alors que la surveillance des performances s’attache à veiller à ce que le système fonctionne de manière _optimale_, la surveillance des contrats SLA est régie par une obligation contractuelle qui définit ce que le terme _optimal_ signifie réellement.

Les contrats SLA souvent définis en termes de :

- Disponibilité générale du système. Par exemple, une organisation peut garantir que le système sera disponible dans 99,9 % du temps ; cela équivaut à un maximum de 9 heures de temps d’arrêt par an, soit environ 10 minutes par semaine.
- Débit opérationnel. Cet aspect est souvent exprimé en une ou plusieurs bornes hautes clés, telles que la garantie que le système sera en mesure de prendre en charge jusqu’à 100 000 demandes utilisateur simultanées ou gérer 10 000 transactions commerciales simultanées.
- Temps de réponse opérationnel. Le système peut également donner des garanties concernant la vitesse à laquelle les demandes sont traitées, par exemple 99 % de l’ensemble des transactions commerciales seront réalisées dans les 2 secondes et aucune transaction ne prendra plus de 10 secondes.

> [AZURE.NOTE]Certains contrats pour les systèmes commerciaux peuvent également inclure des contrats SLA concernant le service client, par exemple toutes les demandes d’assistance entraîneront une réponse dans les 5 minutes et 99 % de tous les problèmes doivent être entièrement traités dans un délai de 1 jour ouvré. Un [suivi des problèmes](#issue-tracking) efficace (décrit plus loin de cette section) est essentiel au respect des contrats SLA de ce type.

### Conditions requises pour la surveillance des contrats SLA
Au niveau le plus élevé, un opérateur doit être en mesure de déterminer d’un coup de œil si le système respecte le contrat SLA, et dans le cas contraire il doit pouvoir explorer et examiner les facteurs sous-jacents pour déterminer les raisons de ces performances inférieures aux normes.

Les indicateurs de niveau supérieur classiques pouvant être représentés visuellement sont les suivants :

- Le pourcentage de disponibilité du service.
- Le débit de l’application (mesuré en matière de transactions réussies et/ou opérations par seconde).
- Le nombre de demandes d’application réussies/échouées.
- Le nombre d’erreurs, d’exceptions et d’avertissements de l’application et du système.

Tous ces indicateurs doivent pouvoir être filtrés par une période de temps spécifiée.

Une application cloud sera probablement constituée de plusieurs sous-systèmes et composants. Un opérateur doit être en mesure de sélectionner un indicateur de niveau supérieur et de connaître sa composition à partir de l’intégrité des éléments sous-jacents. Par exemple, si la disponibilité de l’ensemble du système tombe en dessous d’une valeur acceptable, un opérateur doit être en mesure de faire un zoom avant et d’identifier les éléments qui contribuent à cette défaillance.

> [AZURE.NOTE]La disponibilité du système doit être définie avec précaution. Dans un système qui utilise la redondance pour garantir une disponibilité maximale, des instances individuelles d’éléments peuvent échouer, mais le système peut rester fonctionnel. La disponibilité du système, telle que présentée par la surveillance de l’intégrité, doit indiquer la disponibilité agrégée de chaque élément et pas nécessairement si le système s’est en réalité arrêté. En outre, les défaillances peuvent être isolées, donc même si un système spécifique n’est pas disponible, le reste du système peut rester disponible, bien qu’avec une fonctionnalité restreinte (dans un système de commerce électronique, une défaillance dans le système peut empêcher un client de passer des commandes mais le client peut rester en mesure de parcourir le catalogue de produits).

À des fins d’alerte, le système doit être en mesure de déclencher un événement si l’un des indicateurs de niveau supérieur dépasse un seuil spécifié. Les détails de niveau inférieur des différents facteurs qui composent l’indicateur de niveau supérieur doivent être disponibles en tant que données contextuelles pour le système d’alerte.

### Sources de données, instrumentation et conditions de collecte de données
Les données brutes requises pour prendre en charge la surveillance des contrats SLA sont semblables à celles requises pour la surveillance des performances, ainsi qu’à certains aspects de la surveillance de l’intégrité et de la disponibilité (consulter ces sections pour plus de détails). Vous pouvez capturer ces données en effectuant :

- La surveillance de point de terminaison.
- L’enregistrement des exceptions, erreurs et avertissements.
- Le suivi de l’exécution des demandes utilisateur.
- La surveillance de la disponibilité des services tiers utilisés par le système.
- L’utilisation des métriques et des compteurs de performances.

Toutes les données doivent être chronométrées et horodatées.

### Analyse des données des contrats SLA
Les données d’instrumentation doivent être regroupées pour générer une image de la performance globale du système et prendre en charge l’exploration pour permettre l’examen des performances des sous-systèmes sous-jacents. Par exemple, vous devez pouvoir :

- Calculer le nombre total de demandes utilisateur pendant une période donnée, et déterminer le taux de réussite et d’échec de ces demandes.
- Combiner les temps de réponse des demandes utilisateur pour générer une vue d’ensemble des temps de réponse.
- Analyser la progression des demandes utilisateur, décomposer le temps de réponse global d’une demande donnée en temps de réponse des éléments de travail individuels de cette demande.  
- Déterminer la disponibilité globale du système comme pourcentage de disponibilité pour une période spécifique.
- Analyser le pourcentage de temps de disponibilité de chacun des composants individuels et des services du système. Cela peut impliquer l’analyse des journaux générés par des services tiers.

De nombreux systèmes commerciaux doivent rapporter les chiffres réels de performances par rapport à ceux convenus dans les contrats SLA pour une période spécifiée, en général un mois. Ces informations peuvent être utilisées pour calculer les crédits ou autres formes de remboursement si les contrats SLA ne sont pas respectés pendant cette période. Vous pouvez calculer la disponibilité d’un service à l’aide de la technique décrite dans la section [Analyse des données de disponibilité](#analyzing-availability-data).

À des fins internes, une organisation peut également suivre le nombre et la nature des incidents ayant provoqué la défaillance des services. Apprendre à résoudre ces problèmes rapidement ou à les éliminer complètement contribue à réduire les temps d’arrêt et à respecter les stipulations des contrats SLA.

## Audit
Selon la nature de l’application, il peut exister des réglementations statutaires ou d’autres réglementations légales qui spécifient les conditions requises pour l’audit des opérations effectuées par les utilisateurs et l’enregistrement de toutes les données d’accès. L’audit peut fournir les preuves reliant des clients à des demandes spécifiques ; la non répudiation est un facteur important dans de nombreux systèmes de commerce électronique pour maintenir la confiance entre le client et l’organisation responsable de l’application ou du service.

### Conditions requises pour l’audit
Un analyste doit pouvoir suivre la séquence des opérations commerciales réalisées par les utilisateurs afin que vous puissiez reconstruire leurs actions. Cela peut être nécessaire simplement à des fins d’enregistrement ou dans le cadre d’une investigation.

Les informations d’audit sont très sensibles et incluront probablement des données qui identifient les utilisateurs du système, ainsi que les tâches qu’ils effectuent. Pour cette raison, les informations d’audit seront, de préférence, affichées sous la forme de rapports uniquement disponibles pour les analystes approuvés plutôt qu’à l’aide d’un système interactif prenant en charge les opérations graphiques d’exploration. Un analyste doit être en mesure de générer une gamme de rapports, par exemple en répertoriant toutes les activités des utilisateurs se produisant pendant une période spécifiée, en détaillant la chronologie de l’activité pour un seul utilisateur ou en répertoriant les opérations de la séquence exécutées sur une ou plusieurs ressources.

### Sources de données, instrumentation et conditions de collecte de données
Les principales sources d’informations d’audit peuvent inclure :

- Le système de sécurité qui gère l’authentification des utilisateurs.
- Les journaux de suivi consignant l’activité des utilisateurs.
- Les journaux de sécurité suivant toutes les demandes réseau identifiables et non identifiables.

Le format des données d’audit et la manière dont elles sont stockées peuvent être dictés par des exigences réglementaires. Par exemple, il peut être impossible de nettoyer les données de quelque manière que ce soit (elles doivent être enregistrées dans leur format d’origine) et l’accès au référentiel dans lesquelles elles sont conservées doit être protégé pour empêcher la falsification.

### Analyse des données d’audit
Un analyste doit être en mesure d’accéder aux données brutes dans leur intégralité et dans leur forme d’origine. En plus de la nécessité de générer des rapports d’audit courants, les outils utilisés pour analyser ces données sont susceptibles d’être spécialisés et maintenus à l’extérieur du système.

## Surveillance de l’utilisation
La surveillance de l’utilisation suit la manière dont les fonctionnalités et les composants d’une application sont utilisés. Les données collectées peuvent être utilisées pour :

- Déterminer les fonctionnalités qui sont massivement utilisées et déterminer les zones réactives potentielles dans le système. Les éléments à trafic élevé peuvent tirer parti du partitionnement fonctionnel ou même de la réplication pour répartir la charge de manière plus uniforme. Ces informations peuvent également être utilisées pour déterminer les fonctionnalités rarement utilisées qui sont des candidats possibles à la suppression ou au remplacement dans une future version du système.
- Obtenir des informations sur les événements opérationnels du système dans le cadre d’un fonctionnement normal. Par exemple, dans un site de commerce électronique, vous pourriez enregistrer les informations statistiques sur le nombre de transactions et le volume des clients qui en sont responsables. Ces informations peuvent être utilisées à des fins de planification de capacité lorsque le nombre de clients augmente.
- Détecter (peut-être indirectement) la satisfaction des utilisateurs grâce aux performances ou à la fonctionnalité du système. Par exemple, dans un système de commerce électronique, l’abandon régulier du panier d’achat par un grand nombre d’utilisateurs pourrait être dû à un problème avec la fonctionnalité de paiement.
- Générer des informations de facturation. Une application commerciale ou un service mutualisé peut facturer les clients pour les ressources qu’ils utilisent.
- Appliquer des quotas. Si un utilisateur dans un système mutualisé dépasse son quota payant de temps de traitement ou d’utilisation de ressources pendant une période spécifiée, leur accès ou le traitement peuvent être limités.

### Conditions requises pour la surveillance de l’utilisation
Pour examiner l’utilisation du système, un opérateur devra généralement consulter des informations, telles que :

- Le nombre de demandes traitées par chaque sous-système et dirigées vers chaque ressource.
- Le travail effectué par chaque utilisateur.
- Le volume de stockage de données occupé par chaque utilisateur.
- Les ressources auxquelles chaque utilisateur a eu accès.

Un opérateur doit également être capable de générer des graphiques, par exemple, affichant les utilisateurs les plus gourmands en ressources ou les ressources les plus fréquemment sollicitées.

### Sources de données, instrumentation et conditions de collecte de données
Le suivi de l’utilisation peut être effectué à un niveau relativement élevé, notant l’heure de début et de fin de chaque demande, ainsi que la nature de la demande (lecture, écriture, etc., en fonction de la ressource en question). Vous pouvez obtenir ces informations en effectuant :

- Le suivi des activités des utilisateurs.
- La capture des compteurs de performances mesurant le taux d’utilisation pour chaque ressource.
- La surveillance de l’utilisation du processeur et des E/S des opérations effectuées par chaque utilisateur.

Pour des raisons de mesure, vous devez également être en mesure de mettre en corrélation les utilisateurs et les opérations qu’ils réalisent, et d’identifier les ressources utilisées par ces opérations. Les informations collectées doivent être suffisamment détaillées pour permettre une facturation précise.

<a name="issue-tracking"></a>
## Suivi des problèmes
Les clients et les autres utilisateurs peuvent signaler des problèmes si des événements ou un comportement inattendus se produisent dans le système. Le suivi des problèmes s’attache à gérer ces problèmes, à les associer à des efforts visant à résoudre tous les problèmes sous-jacents dans le système et à informer les clients des résolutions possibles.

### Conditions requises pour le suivi des problèmes
Le suivi des problèmes est souvent réalisé à l’aide d’un système distinct qui permet aux opérateurs d’enregistrer et de rapporter les détails des problèmes signalés par les utilisateurs. Ces détails peuvent inclure des informations telles que les tâches que l’utilisateur tentait d’effectuer, les symptômes du problème, la séquence des événements et tout message d’erreur ou d’avertissement ayant été émis.

### Sources de données, instrumentation et conditions de collecte de données
L’utilisateur ayant signalé le problème en premier lieu est la source de données initiale pour les données de suivi des problèmes. L’utilisateur peut être en mesure de fournir des données supplémentaires telles qu’un vidage sur incident (si l’application inclut un composant qui s’exécute sur le bureau de l’utilisateur), une capture d’écran, la date et l’heure à laquelle l’erreur s’est produite avec toute autre information environnementale comme leur emplacement.

Ces informations peuvent servir à alimenter les efforts de débogage et permettre d’ouvrir le journal des travaux en souffrance pour les versions futures du logiciel.

### Analyse des données de suivi des problèmes
Différents utilisateurs peuvent signaler le même problème et le système de suivi des problèmes doit associer les rapports courants.

La progression de l’effort de débogage doit être enregistrée sur chaque rapport de problème et lorsque le problème est résolu, le client peut être informé de la solution.

Si un utilisateur signale un problème reconnu avec une solution connue dans le système de suivi de problèmes, l’opérateur doit être en mesure transmettre immédiatement la solution à l’utilisateur.

## Suivi des opérations et versions logicielles de débogage
Lorsqu’un utilisateur signale un problème, il n’est souvent conscient que de l’impact immédiat sur ses propres opérations et ne peut rapporter que le résultat de sa propre expérience à un opérateur responsable de la maintenance du système. Ces expériences sont généralement un symptôme visible d’un ou de plusieurs problèmes fondamentaux. Dans de nombreux cas, un analyste devra examiner en détail la chronologie des opérations sous-jacentes afin d’établir la cause du problème (ce processus est appelé _Analyse de la cause première_).

> [AZURE.NOTE]L’analyse de la cause première peut découvrir des inefficacités dans la conception d’une application. Dans ces situations, il peut être possible de retravailler les éléments affectés et de les déployer dans le cadre d’une version ultérieure. Ce processus nécessite un contrôle prudent et les composants mis à jour doivent être étroitement surveillés.

### Conditions requises pour le suivi et le débogage
Pour le suivi d’événements inattendus et d’autres problèmes, il est essentiel que les données de surveillance fournissent suffisamment d’informations sur les problèmes qui se produisent à un niveau supérieur, mais qu’elles incluent également des informations suffisamment détaillées pour permettre à un analyste de retrouver l’origine de ces problèmes et de reconstruire la séquence des événements qui se sont produits. Ces informations doivent être suffisantes pour permettre à un analyste de diagnostiquer la cause première des problèmes afin qu’un développeur puisse apporter les modifications nécessaires pour empêcher qu’ils se reproduisent.

### Sources de données, instrumentation et conditions de collecte de données
La résolution des problèmes peut entraîner le suivi de toutes les méthodes (et de leurs paramètres) appelées dans le cadre d’une opération de création d’une arborescence illustrant le flux logique à travers le système quand un client effectue une demande spécifique. Les exceptions et les avertissements générés par le système en raison de ce flux doivent être capturés et enregistrés.

Pour prendre en charge le débogage, le système peut fournir des hooks qui permettent à un opérateur de capturer les informations d’état à des moments cruciaux dans le système, ou de fournir des informations étape par étape à mesure que les opérations sélectionnées progressent. La capture des données à ce niveau de détail peut imposer une charge supplémentaire sur le système et doit rester un processus temporaire, principalement utilisé lors d’une série d’événements très rares et difficiles à répliquer, ou lorsqu’une nouvelle version d’un ou de plusieurs éléments d’un système requiert une analyse minutieuse pour s’assurer qu’ils fonctionnent comme prévu.

## Pipeline de surveillance et de diagnostic
La surveillance d’un système distribué à grande échelle pose un défi et chacun des scénarios décrits dans la section précédente ne doit pas nécessairement être considéré de manière isolée. Il existera vraisemblablement un chevauchement significatif des données de surveillance et de diagnostic requises pour chaque situation, bien que ces données puissent devoir être traitées et présentées de différentes manières. Pour ces raisons, vous devez adopter une approche holistique de la surveillance et du diagnostic.

Vous pouvez envisager l’ensemble du processus de surveillance et de diagnostic comme un pipeline qui comprend les étapes présentées à la figure 1.

![](media/best-practices-monitoring/Pipeline.png)

_Figure 1. Les étapes du pipeline de surveillance et de diagnostic_

La figure 1 montre comment les données de surveillance et de diagnostic peuvent provenir de diverses sources de données. L’étape Instrumentation/Collecte concerne l’instrumentation ; déterminer les données à capturer, comment capturer et mettre en forme ces données afin de pouvoir les examiner facilement. La phase Analyse/Diagnostic prend les données brutes et les utilise pour générer des informations significatives qui peuvent être utilisées pour déterminer l’état du système. Ces informations peuvent être utilisées pour prendre des décisions concernant les actions possibles à prendre et les résultats peuvent être réinjectés dans la phase Instrumentation/Collecte. La phase Visualisation/Alerte présente une vue utilisable de l’état du système ; elle peut afficher des informations quasiment en temps réel grâce à une série de tableaux de bord et peut générer des rapports, des diagrammes et des graphiques pour fournir une vue historique des données pouvant permettre d’identifier les tendances à long terme. Si les informations montrent qu’un indicateur clé de performance est susceptible de dépasser les limites acceptables, cette étape peut également déclencher une alerte destinée à un opérateur. Dans certains cas, une alerte peut également servir à déclencher un processus automatisé qui tente de prendre des mesures correctives, telles que la mise à l’échelle automatique.

Notez que ces étapes constituent un processus de flux continu dans lequel les étapes s’effectuent en parallèle. Dans l’idéal, toutes les phases doivent être configurables de manière dynamique. À certains moments, surtout lorsqu’un système a été récemment déployé ou rencontre des problèmes, il peut être nécessaire de rassembler des données étendues plus fréquemment. À d’autres moments, il doit être possible de revenir à la capture d’un niveau de base d’informations essentielles pour vérifier que le système fonctionne correctement.

En outre, l’ensemble du processus de surveillance doit être considéré comme une solution dynamique et continue soumise à des ajustements et améliorations à la suite de commentaires. Par exemple, vous pouvez commencer par mesurer de nombreux facteurs pour déterminer l’intégrité du système, mais au fil du temps, l’analyse peut entraîner un perfectionnement alors que vous ignorez les mesures qui ne sont pas pertinentes, ce qui vous permet de vous consacrer plus précisément aux données dont vous avez besoin tout en réduisant les bruits de fond.

## Sources des données de surveillance et de diagnostic
Les informations utilisées par le processus de surveillance peuvent provenir de plusieurs sources, comme illustré dans la figure 1. Au niveau de l’application, les informations proviennent des journaux de suivi incorporés au code du système. Les développeurs doivent suivre une approche standard pour le suivi du flux de contrôle via leur code (par exemple, à l’entrée d’une méthode, émettre un message de suivi qui spécifie le nom de la méthode, l’heure actuelle, la valeur de chaque paramètre et toute autre information pertinente. Enregistrer l’heure d’entrée et de sortie peut également s’avérer utile). Vous devez consigner l’ensemble des exceptions et avertissements, et veiller à conserver une trace complète des exceptions et avertissements imbriqués. Dans l’idéal, vous devez également capturer les informations qui identifient l’utilisateur exécutant le code avec les informations de corrélation d’activité (pour suivre les demandes lorsqu’elles passent par le système) et consigner les tentatives d’accès à toutes les ressources, telles que les files d’attente de messagerie, les bases de données, les fichiers et les autres services dépendants. Ces informations peuvent être utilisées à des fins d’audit et de mesure.

De nombreuses applications utilisent des bibliothèques et des infrastructures pour effectuer des tâches courantes telles que l’accès à un magasin de données ou la communication sur un réseau. Ces infrastructures peuvent être configurables pour renvoyer leurs propres messages de suivi et les informations de diagnostic brutes, telles que les vitesses de transaction, les réussites et échecs de transmission de données, etc.

> [AZURE.NOTE]De nombreuses infrastructures modernes publient automatiquement les performances et les événements de suivi. La capture de ces informations dépend de la mise à disposition d’un moyen de les récupérer et de les stocker où elles pourront ensuite être traitées et analysées.

Le système d’exploitation sur lequel s’exécute l’application peut être une source d’informations de niveau inférieur à l’échelle du système, telle que les compteurs de performances indiquant les vitesses d’E/S, l’utilisation de la mémoire et du processeur. Les erreurs du système d’exploitation (par exemple, l’impossibilité d’ouvrir un fichier correctement) peuvent également être signalées.

Vous devez également tenir compte de l’infrastructure et des composants sous-jacents sur lesquels s’exécute le système. Les machines virtuelles, les réseaux virtuels et les services de stockage peuvent tous être des sources importantes de données de compteurs de performances au niveau de l’infrastructure et d’autres données de diagnostic.

Si votre application utilise d’autres services externes, tels qu’un serveur web ou un système de gestion de base de données, ces services peuvent publier leurs propres informations de suivi, journaux et compteurs de performance. Les exemples incluent des SQL Server Dynamic Management Views pour le suivi des opérations effectuées sur une base de données SQL Server et les journaux de suivi Internet Information Server pour l’enregistrement des demandes adressées à un serveur web.

Comme les composants d’un système sont modifiés et des nouvelles versions déployées, il est important d’être en mesure d’attribuer des problèmes, des événements et des métriques à chaque version. Ces informations doivent être liées au pipeline de version afin que les problèmes avec une version spécifique d’un composant puissent être suivis rapidement et corrigés.

Des problèmes de sécurité peuvent se produire à tout moment dans le système. Par exemple, un utilisateur peut essayer de se connecter avec un ID utilisateur ou un mot de passe invalide, un utilisateur authentifié peut essayer et obtenir un accès non autorisé à une ressource, ou un utilisateur peut fournir une clé non valide ou obsolète pour accéder à des informations chiffrées. Des informations relatives à la sécurité pour les demandes réussies et échouées doivent toujours être consignées.

La section [Instrumentation d’une application](#instrumenting-an-application) contient des conseils supplémentaires sur les informations que vous devez capturer, mais il existe une variété de stratégies que vous pouvez utiliser pour collecter ces informations en premier lieu :

- **Surveillance de l’application/du système**. Cette stratégie utilise des sources internes au sein de l’application, les infrastructures de l’application, le système d’exploitation et l’infrastructure. Le code de l’application peut générer ses propres données de surveillance à des points notables pendant le cycle de vie d’une demande client. L’application peut inclure des instructions de suivi qui peuvent être activées ou désactivées de manière sélective selon les cas. Il peut également être possible d’injecter des diagnostics dynamiquement à l’aide d’une infrastructure de diagnostics. Ces infrastructures fournissent généralement des plug-in qui peuvent se fixer à divers points d’instrumentation de votre code et capturer les données de suivi à ces points.

En outre, votre code et/ou l’infrastructure sous-jacente peuvent déclencher des événements à des points critiques. Les agents de surveillance configurés pour écouter ces événements peuvent enregistrer les informations sur ces événements.

- **Surveillance des utilisateurs**. Cette approche enregistre les interactions entre un utilisateur et l’application, et observe le flux de chaque demande et réponse. Ces informations peuvent avoir un objectif double : elles peuvent être utilisées pour l’utilisation du contrôle par chaque utilisateur et pour déterminer si les utilisateurs reçoivent une qualité de service satisfaisante (par exemple, temps de réponse rapides, faible latence et fréquence minimales des erreurs). Les données capturées peuvent servir à identifier les domaines de préoccupation où les défaillances se produisent le plus fréquemment et les éléments où le système ralentit, probablement en raison de zones réactives dans l’application ou à une autre forme de goulot d’étranglement. Si cette approche est implémentée avec soin, il peut être possible de reconstruire le flux des utilisateurs via l’application à des fins de débogage et de test.

	> [AZURE.IMPORTANT]Les données capturées par la surveillance des utilisateurs doivent être considérées comme très sensibles car elles peuvent contenir des documents confidentiels. Si les données capturées sont enregistrées, elles doivent être stockées en toute sécurité. Si les données sont utilisées à des fins de surveillance des performances ou de débogage, toutes les informations personnellement identifiables doivent être supprimées en priorité.

- **Surveillance des utilisateurs synthétiques**. Avec cette approche, vous écrivez votre propre client de test qui simule un utilisateur et exécute une série configurable, mais classique, d’opérations. Vous pouvez suivre les performances du client de test afin de déterminer l’état du système. Vous pouvez également utiliser plusieurs instances du client de test dans le cadre d’une opération de test de charge pour définir la réponse du système en situation de contrainte et le type de sortie de surveillance généré dans ces conditions.

	> [AZURE.NOTE]Vous pouvez implémenter une surveillance des utilisateurs réels et synthétiques en incluant du code qui suit et chronomètre l’exécution des appels de méthode et des autres parties critiques d’une application.

- **Profilage**. Cette approche est principalement destinée à la surveillance et l’amélioration des performances de l’application. Au lieu d’opérer au niveau fonctionnel employé par la surveillance des utilisateurs réels et synthétiques, elle capture des informations de niveau inférieur pendant que l’application s’exécute. Le profilage peut être implémenté par échantillonnage périodique de l’état d’exécution d’une application (déterminant la partie du code exécutée par l’application à un moment donné dans le temps) ou à l’aide de l’instrumentation qui insère des sondes dans le code à des points de jonction importants (tels qu’au début et à la fin d’un appel de méthode) et enregistre les méthodes appelées, le moment et la durée de chaque appel. Ces données peuvent ensuite être analysées pour déterminer les parties de l’application qui peuvent causer des problèmes de performances.

- **Surveillance de point de terminaison** Cette technique utilise un ou plusieurs points de diagnostics exposés par l’application spécifiquement pour permettre la surveillance. Un point de terminaison fournit un chemin d’accès au code de l’application et peut renvoyer des informations sur l’intégrité du système. Des points de terminaison différents peuvent se concentrer sur divers aspects de la fonctionnalité. Vous pouvez écrire votre propre client de diagnostic qui envoie des demandes périodiques à ces points de terminaison et assimiler les réponses. Cette approche est décrite plus en détail dans le [Modèle de surveillance de point de terminaison d’intégrité](https://msdn.microsoft.com/library/dn589789.aspx) sur le site web Microsoft.

Pour une couverture maximale, vous devez utiliser ces techniques en association les unes avec les autres.

<a name="instrumenting-an-application"></a>
## Instrumentation d’une application
L’instrumentation est un élément essentiel du processus de surveillance. Vous pouvez prendre des décisions valables sur les performances et l’intégrité d’un système uniquement si vous capturez d’abord les données qui vous permettent de prendre ces décisions. Les informations que vous collectez à l’aide de l’instrumentation doivent être suffisantes pour permettre d’évaluer les performances, diagnostiquer les problèmes et prendre des décisions sans avoir besoin de vous connecter à un serveur de production distant pour effectuer manuellement le suivi (et le débogage).

Les données d’instrumentation comprennent généralement des informations écrites dans les journaux de suivi et des métriques :

- Le contenu d’un journal de suivi peut être le résultat de données textuelles écrites par l’application, de données binaires créées à la suite d’un événement de suivi (si l’application utilise Event Tracing for Windows – ETW), ou il peut être généré à partir des journaux système qui enregistrent les événements provenant des parties de l’infrastructure, comme un serveur web. Les messages textuels du journal sont souvent conçus pour être lisibles, mais ils doivent également être écrits dans un format qui leur permet d’être facilement analysés par un système automatisé. Vous devez également classer les journaux. Ne pas écrire toutes les données de suivi dans un seul journal mais utiliser des journaux distincts pour enregistrer la sortie de suivi à partir de différents aspects opérationnels du système. Cela vous permet de filtrer rapidement les messages du journal par lecture du journal approprié au lieu de traiter un seul fichier long. Ne jamais consigner des informations ayant des exigences de sécurité différentes (telles que les informations d’audit et les données de débogage) dans le même journal.

	> [AZURE.NOTE]Un journal peut être implémenté en tant que fichier sur le système de fichiers ou il peut être conservé dans un autre format comme un objet blob dans le stockage d’objets blob. Les informations du journal peuvent également être conservées dans un stockage structuré, comme des lignes dans un tableau.

- Généralement, les métriques sont une mesure ou un nombre d’un aspect ou d’une ressource dans le système à un moment donné avec une ou plusieurs balises ou dimensions associées (parfois appelées _échantillon_). Une instance unique d’une métrique n’est généralement pas utile isolément. À la place, les métriques doivent être capturées dans le temps. La question clé à examiner concerne le choix des métriques à enregistrer et la fréquence d’enregistrement. Trop souvent, la génération des données pour les métriques peut imposer une importante charge supplémentaire sur le système, alors que la capture des métriques vous fera rarement manquer les circonstances entraînant un événement important. Cet examen varie d’une métrique à l’autre. Par exemple, l’utilisation du processeur sur un serveur peut varier considérablement d’une seconde à l’autre, mais une utilisation élevée ne devient un problème que lorsqu’elle se prolonge sur plusieurs minutes.

<a name="information-for-correlating-data"></a>
### Informations pour la corrélation des données
Vous pouvez facilement surveiller les compteurs de performance au niveau du système individuel, capturer des métriques pour les ressources et obtenir des informations de suivi d’application à partir de divers fichiers journaux, mais certaines formes de surveillance nécessitent une phase d’analyse et de diagnostic dans le pipeline de surveillance pour mettre en corrélation les données extraites à partir de plusieurs sources. Ces données peuvent prendre plusieurs formes dans les données brutes, et le processus d’analyse doit être fourni avec des données d’instrumentation suffisantes pour pouvoir mapper ces différentes formes. Par exemple, au niveau de l’infrastructure de l’application, une tâche peut être identifiée par un ID de thread, mais dans une application, le même travail peut être associé à l’ID utilisateur pour l’utilisateur qui exécute cette tâche. En outre, un mappage 1:1 entre les threads et les demandes utilisateur est peu probable car des opérations asynchrones peuvent réutiliser les mêmes threads pour effectuer des opérations pour le compte de plusieurs utilisateurs. Pour compliquer davantage les choses, une seule demande peut être gérée par plusieurs threads durant le déroulement de l’exécution dans le système. Si possible, il convient d’associer chaque demande à un ID d’activité unique qui est propagé par le système dans le cadre du contexte de la demande (la technique pour générer et inclure les ID d’activité dans les informations de suivi dépendra de la technologie utilisée pour capturer les données de suivi).

Toutes les données de surveillance doivent être horodatées de la même façon. Pour des raisons de cohérence, il est nécessaire d’enregistrer toutes les dates et heures en utilisant le temps universel coordonné. Cela vous permettra de suivre plus facilement des séquences d’événements.

> [AZURE.NOTE]Les ordinateurs qui fonctionnent dans différents fuseaux horaires et les réseaux peuvent ne pas être synchronisés, donc vous ne devez pas dépendre uniquement de l’utilisation de l’horodatage pour corréler les données d’instrumentation qui s’étendent sur plusieurs ordinateurs.

### Quelles informations doivent inclure les données d’instrumentation ?
Lorsque vous décidez des données d’instrumentation à collecter, tenez compte des points suivants :

- Les informations capturées par les événements de suivi doivent être lisibles par l’homme et la machine. Vous devez adopter des schémas clairement définis pour ces informations afin de faciliter le traitement automatisé des données des journaux entre les systèmes, et assurer la cohérence pour le personnel opérationnel et technique qui lisent les journaux. Inclure des informations environnementales, telles que l’environnement de déploiement, l’ordinateur sur lequel le processus s’exécute, les détails du processus et la pile des appels.  
- Le profilage peut imposer une surcharge significative sur le système et doit être activé uniquement en cas de nécessité. Le profilage effectué à l’aide de l’instrumentation enregistre un événement (tel qu’un appel de méthode) chaque fois qu’il se produit, tandis que l’échantillonnage n’enregistre que les événements sélectionnés. La sélection peut être basée sur le temps (une fois toutes les n secondes) ou sur la fréquence (une fois toutes les n demandes). Si les événements se produisent fréquemment, le profilage par instrumentation peut occasionner une charge trop lourde et impacter les performances globales. Dans ce cas, l’approche par échantillonnage peut être préférable. Toutefois, si la fréquence des événements est faible, alors l’échantillonnage peut les manquer et dans ce cas l’instrumentation peut être la meilleure approche.
- Fournir un contexte suffisant pour permettre à un développeur ou un administrateur de déterminer la source de chaque demande. Cela peut inclure une forme d’ID d’activité identifiant une instance spécifique d’une demande et des informations qui peuvent être utilisées pour mettre en corrélation cette activité avec le travail de calcul effectué et les ressources utilisées. Notez que ce travail peut franchir les limites de processus et d’ordinateur. Pour le contrôle, le contexte doit également inclure (directement ou indirectement via d’autres informations mises en corrélation) une référence au client qui a provoqué la formulation de la demande. Ce contexte fournit des informations précieuses sur l’état de l’application au moment où les données de surveillance ont été capturées.
- Enregistrer toutes les demandes, ainsi que les emplacements ou les régions à partir desquels ces demandes sont effectuées. Ces informations peuvent vous aider à déterminer s’il existe des points d’accès propres à l’emplacement et fournissent des données utiles pour déterminer s’il faut repartitionner une application ou les données qu’elle utilise.
- Enregistrer et capturer les détails des exceptions avec soin. Les informations critiques de débogage sont souvent perdues à la suite d’une mauvaise gestion des exceptions. Capturer les informations complètes sur les exceptions lancées par l’application, notamment les exceptions internes et autres informations de contexte, y compris la pile des appels si possible.
- Être cohérent dans les données que les différents éléments de votre application capturent, car cela peut vous permettre d’analyser des événements et de les corréler avec les demandes de l’utilisateur. Envisager de recourir à un package de journalisation complet et configurable pour collecter des informations plutôt que de dépendre de l’implémentation par les développeurs des différentes parties du système adoptant toutes la même approche. Collecter les données à partir des compteurs de performance clés, tels que le volume d’E/S effectué, l’utilisation du réseau, le nombre de demandes, l’utilisation de la mémoire et du processeur. Certains services d’infrastructure peuvent fournir leurs propres compteurs de performance spécifiques, comme le nombre de connexions à une base de données, la vitesse à laquelle les transactions sont effectuées et le nombre de transactions qui réussissent ou échouent. Les applications peuvent également définir leurs propres compteurs de performance spécifiques.
- Consigner tous les appels envoyés à des services externes, tels que les systèmes de base de données, de services web ou d’autres services au niveau du système fournis dans le cadre de l’infrastructure (par exemple la mise en cache d’Azure). Enregistrer les informations sur le temps nécessaire pour réaliser chaque appel et la réussite ou l’échec de l’appel. Si possible, capturer les informations sur toutes les nouvelles tentatives et les échecs pour toutes les erreurs temporaires se produisant.

### Garantir la compatibilité avec les systèmes de télémétrie
Dans de nombreux cas, les informations générées par l’instrumentation sont générées sous la forme d’une série d’événements et transmises à un système de télémétrie distinct pour le traitement et l’analyse. Un système de télémétrie est généralement indépendant de toute application ou technologie spécifique, mais s’attend à ce que les informations respectent un format spécifique, généralement défini par un schéma. Le schéma spécifie en fait un contrat définissant les champs de données et les types que peut recevoir le système de télémétrie. Le schéma doit être généralisé pour autoriser les données reçues à partir de diverses plates-formes et périphériques.

Un schéma commun doit inclure les champs qui sont communs à tous les événements d’instrumentation, tels que le nom de l’événement, l’heure de l’événement, l’adresse IP de l’expéditeur et les détails nécessaires pour la mise en corrélation avec d’autres événements (par exemple un ID d’utilisateur, un ID de périphérique et un ID d’application). N’oubliez pas que les événements peuvent être déclenchés par un nombre illimité de périphériques, donc le schéma ne doit pas être dépendant du type de périphérique. En outre, les événements pour la même application peuvent être déclenchés par un nombre de périphériques différents. L’application peut prendre en charge l’itinérance ou toute autre forme de distribution inter-périphériques. Dans l’idéal, la majorité de ces champs doivent être mappés à la sortie de la bibliothèque de journalisation utilisée pour capturer les événements.

Le schéma peut également inclure des champs de domaine qui sont pertinents pour un scénario particulier commun entre différentes applications. Il peut s’agir d’informations sur les exceptions, d’événements sur le démarrage et la fermeture de l’application et la réussite et/ou l’échec de l’appel de l’API du service web. Toutes les applications qui utilisent le même ensemble de champs de domaine doivent émettre le même jeu d’événements, permettant de créer un ensemble de rapports et d’analyses communs.

Enfin, un schéma peut contenir des champs personnalisés pour capturer les détails des événements propres à l’application.

### Meilleures pratiques pour l’instrumentation d’applications
La liste suivante récapitule les meilleures pratiques pour l’instrumentation d’une application distribuée fonctionnelle dans le cloud.

- Faciliter la lecture et l’analyse des journaux. Utiliser une journalisation structurée dès que possible. Être concis et descriptif dans les messages de journal.
- Dans tous les journaux, identifier la source et fournir le contexte et les informations de minutage à mesure que chaque enregistrement est écrit.
- Utiliser les mêmes fuseau horaire et format pour tous les horodatages. Cela vous aidera à mettre en corrélation des événements pour les opérations qui s’étendent sur le matériel et les services fonctionnant dans différentes régions géographiques.
- Classer les journaux et écrire des messages dans le fichier journal approprié.
- Ne pas divulguer d’informations sensibles sur le système ou les informations personnelles des utilisateurs. Nettoyer ces informations avant qu’elles soient consignées, mais s’assurer que les détails pertinents sont conservés. Par exemple, supprimer l’ID et le mot de passe des chaînes de connexion des base de données, mais écrire les informations restantes dans le journal afin qu’un analyste puisse déterminer que le système accède correctement à la base de données. Enregistrer toutes les exceptions critiques, mais permettre à l’administrateur d’activer et de désactiver la journalisation pour les niveaux inférieurs d’exceptions et d’avertissements. En outre, capturer et enregistrer toutes les informations de logique de nouvelle tentative. Ces données peuvent être utiles pour la surveillance de l’intégrité temporaire du système.
- Suivre les appels hors processus, comme les demandes vers des services web externes ou des bases de données.
- Ne pas mélanger les messages de journal avec des exigences de sécurité différentes dans le même fichier journal. Par exemple, ne pas écrire d’informations de débogage et d’audit dans le même journal.
- À l’exception des événements d’audit, tous les appels de journalisation doivent être des opérations de type fire-and-forget qui ne doivent pas bloquer la progression des opérations de l’entreprise. Les événements d’audit sont exceptionnels, car ils sont essentiels pour l’entreprise et peuvent être classés comme un élément fondamental des opérations de l’entreprise.
- L’enregistrement doit être extensible et n’avoir aucune dépendance directe vis-à-vis de la cible concrète. Par exemple, plutôt que d’écrire des informations à l’aide de _System.Diagnostics.Trace_, définir une interface abstraite (telle que _ILogger_) qui expose des méthodes de journalisation et qui peut être implémentée par tout moyen approprié.
- Toutes les journalisations doivent être effectuées en mode de prévention des défaillances et ne doivent jamais déclencher des erreurs en cascade. La journalisation ne doit lancer aucune exception.
- Traiter l’instrumentation comme un processus itératif continu et examiner régulièrement les journaux, pas uniquement en cas de problème.

## Collecte et stockage des données
La phase de collecte du processus de surveillance vise à récupérer des informations générées par l’instrumentation, à formater ces données afin de les rendre plus faciles à utiliser lors de la phase d’analyse/de calcul et à enregistrer les données transformées dans un stockage fiable et accessible. Les données d’instrumentation que vous collectez à partir des différentes parties d’un système distribué peuvent être conservées dans divers emplacements et avec des formats différents. Par exemple, votre code d’application peut générer des fichiers journaux de suivi et générer des données de journal des événements d’application, tandis que les compteurs de performance qui surveillent les principaux aspects de l’infrastructure utilisés par votre application peuvent être capturés à l’aide d’autres technologies. Les composants et services tiers utilisés par votre application peuvent fournir des informations d’instrumentation dans différents formats, à l’aide de fichiers de suivi distincts, stockage d’objets blob ou même un magasin de données personnalisé.

La collecte de données est souvent effectuée en implémentant un service de collecte qui peut s’exécuter de manière autonome à partir de l’application qui génère les données d’instrumentation. La figure 2 illustre un exemple de cette architecture et montre le sous-système de collecte des données d’ instrumentation.

![](media/best-practices-monitoring/TelemetryService.png)

_Figure 2. Collecte des données d’instrumentation_

Notez qu’il s’agit d’une vue simplifiée. Le service n’est pas nécessairement un processus unique et peut comporter plusieurs parties constituantes s’exécutant sur différents ordinateurs, comme le décrivent les sections suivantes. En outre, si l’analyse de certaines données de télémétrie doit être effectuée rapidement (analyse à chaud, comme décrit dans la section [Prise en charge de l’analyse à chaud, modérée et à froid](#supporting-hot-warm-and-cold-analysis) plus loin dans ce document), des composants locaux fonctionnant en dehors du service de collecte peuvent effectuer les tâches d’analyse immédiatement. La figure 2 illustre cette situation pour des événements sélectionnés. Les résultats du traitement analytique peuvent être envoyés directement au sous-système de visualisation et d’alerte. Les données soumises aux analyses modérée ou à froid sont stockées pendant qu’elles attendent le traitement.

Pour les applications et services Azure, Azure Diagnostics (WAD) fournit une solution possible de capture des données. WAD rassemble des données à partir des sources suivantes pour chaque nœud de calcul, les agrège, puis les télécharge vers le stockage Azure :

- Journaux Azure
- Journaux IIS
- Journaux d’échecs de demande IIS
- Journaux d’événements Windows
- Compteurs de performances
- Vidages sur incident
- Journaux d’infrastructure Azure Diagnostic  
- Journaux d’erreurs personnalisés

Pour plus d’informations, consultez l’article [Azure: Telemetry Basics and Troubleshooting](http://social.technet.microsoft.com/wiki/contents/articles/18146.windows-azure-telemetry-basics-and-troubleshooting.aspx) sur le site web Microsoft.

### Stratégies de collecte des données d’instrumentation
Étant donné la nature évolutive du cloud et pour éviter la nécessité de récupérer manuellement les données de télémétrie de chaque nœud dans le système, vous devez faire en sorte que les données puissent être transférées vers un emplacement central et consolidé. Dans un système qui s’étend sur plusieurs centres de données, il peut être utile de commencer par collecter, consolider et stocker des données sur une base régionale, puis d’agréger les données régionales dans un système central unique.

Pour optimiser l’utilisation de la bande passante, vous pouvez choisir de transférer les données moins urgentes en blocs, en tant que lots. Toutefois, les données de ne doivent pas être retardées indéfiniment, surtout si elles contiennent des informations soumises à une contrainte de temps.

#### _Extraction et transmission des données d’instrumentation_
Le sous-système de collecte des données d’instrumentation peut récupérer activement les données d’instrumentation à partir de divers journaux et d’autres sources pour chaque instance de l’application (le _modèle d’extraction_) ou il peut agir comme un récepteur passif qui attend que les données soient envoyées à partir des composants qui constituent chaque instance de l’application (le _modèle de transmission_).

Une approche de l’implémentation du modèle d’extraction consiste à utiliser des agents de surveillance fonctionnant localement avec chaque instance de l’application. Un agent de surveillance est un processus distinct qui extrait régulièrement les données de télémétrie collectées au niveau du nœud local et écrit ces informations directement dans le stockage centralisé qui est partagé par toutes les instances de l’application. C’est le mécanisme implémenté par Microsoft Azure Diagnostic. Chaque instance d’un rôle web ou d’un rôle de travail Azure peut être configurée pour capturer le diagnostic et d’autres informations de suivi stockées localement. L’agent de surveillance qui s’exécute en même temps copie les données spécifiées dans le stockage Azure. La page [Configuration des diagnostics pour Azure Cloud Services et Azure Virtual Machines](https://msdn.microsoft.com/library/azure/dn186185.aspx) sur le site web de Microsoft fournit plus d’informations sur ce processus. Certains éléments, tels que les journaux IIS, les vidages sur incident et les journaux d’erreurs personnalisés, sont écrits dans le stockage d’objets blob, alors que les données à partir du journal des événements Windows, les événements ETW et les compteurs de performance sont enregistrés dans le stockage de table. La figure 3 illustre ce mécanisme :

![](media/best-practices-monitoring/PullModel.png)

_Figure 3. Utilisation d’un agent de surveillance pour extraire des informations et les écrire dans un stockage partagé_

> [AZURE.NOTE]L’utilisation d’un agent de surveillance est parfaitement adaptée à la capture des données d’instrumentation naturellement extraites d’une source de données, telles que des informations à partir des vues de gestion SQL Server ou la longueur d’une file d’attente de Service Bus Azure.

Pour plus d’informations sur la configuration et l’utilisation des diagnostics Azure, visitez la page [Recueillir des données de journaux à l’aide des diagnostics Azure](https://msdn.microsoft.com/library/azure/gg433048.aspx) du site web Microsoft.

Les données de télémétrie pour une application à petite échelle qui s’exécute sur un nombre limité de nœuds peuvent facilement être stockées dans un emplacement unique à l’aide de l’approche que nous venons de décrire. Toutefois, une application dans le cloud, globale, hautement évolutive et complexe peut générer facilement d’énormes volumes de données provenant de centaines de rôles web et de rôles de travail, de partitions de base de données et d’autres services. Ce flux de données peut très facilement surcharger la bande passante d’E/S disponible avec un emplacement central unique. Par conséquent, votre solution de télémétrie doit être évolutive pour l’empêcher de se comporter comme un goulot d’étranglement à mesure que le système se développe et incorporer, dans l’idéal, un degré de redondance pour réduire les risques de perte des informations de surveillance importantes (telles que les données d’audit ou de facturation) en cas de panne d’une partie du système.

Pour résoudre ces problèmes, vous pouvez implémenter un mécanisme de mise en file d’attente. La figure 4 illustre cette structure. Dans cette architecture, l’agent de surveillance local (s’il peut être configuré de manière appropriée) ou le service de collecte de données personnalisé (dans le cas contraire) envoie les données vers une file d’attente et un processus distinct fonctionnant de façon asynchrone (le Service d’écriture dans le stockage à la figure 4) prend les données dans cette file d’attente et les écrit dans le stockage partagé. Une file d’attente de message est adaptée à ce scénario, car elle fournit au moins une fois la sémantique assurant qu’une fois publiées, les données mises en file d’attente ne seront pas perdues. Le Service d’écriture dans le stockage peut être implémenté en utilisant un rôle de travail distinct.

![](media/best-practices-monitoring/BufferedQueue.png)

_Figure 4. Utilisation d’une file d’attente pour mettre en mémoire tampon les données d’instrumentation_

Le service de collecte de données locales peut ajouter des données à une file d’attente dès leur réception. La file d’attente agit comme une mémoire tampon et le service d’écriture dans le stockage peut récupérer et écrire les données à son propre rythme. Par défaut, une file d’attente fonctionne sur une base « premier entré, premier sorti », mais vous pouvez hiérarchiser les messages pour accélérer leur avancée dans la file d’attente s’ils contiennent des données qui doivent être traitées plus rapidement. Pour plus d’informations, consultez [Priority Queue Pattern](https://msdn.microsoft.com/library/dn589794.aspx). Vous pouvez également utiliser différents canaux (par exemple, les rubriques de Service Bus) pour diriger les données vers différentes destinations en fonction de la forme de traitement analytique nécessaire.

À des fins d’évolutivité, vous pouvez exécuter plusieurs instances du service d’écriture dans le stockage. S’il existe un volume élevé d’événements, vous pouvez utiliser un concentrateur d’événements pour distribuer les données vers des ressources de calcul différentes pour le traitement et le stockage.

<a name="consolidating-instrumentation-data"></a>
#### _Consolidation des données d’instrumentation_
Les données d’instrumentation récupérées par le service de collecte de données à partir d’une seule instance d’une application fournit une vue localisée de l’intégrité et des performances de cette instance. Pour évaluer l’intégrité globale du système, il est nécessaire de consolider certains aspects des données dans les vues locales entre eux. Il est possible d’effectuer cette consolidation une fois que les données ont été stockées, mais dans certains cas, elle peut également être réalisée au moment où les données sont collectées. Au lieu d’être écrites directement dans le stockage partagé, les données d’instrumentation peuvent passer par un service de consolidation de données distinct qui combine les données et agit comme un processus de filtrage et de nettoyage. Par exemple, les données d’instrumentation qui incluent les mêmes informations de corrélation, comme un ID d’activité, peuvent être fusionnées (il est possible qu’un utilisateur démarre une opération sur un nœud, puis soit transféré vers un autre nœud en cas de défaillance du premier ou selon la configuration de l’équilibrage de charge). Ce processus peut également détecter et supprimer les données dupliquées (toujours une possibilité si le service de télémétrie utilise des files d’attente de messages pour envoyer des données d’instrumentation vers le stockage). La figure 5 illustre un exemple de cette structure.

![](media/best-practices-monitoring/Consolidation.png)

_Figure 5. Utilisation d’un service distinct pour consolider et nettoyer les données d’instrumentation_

### Stockage des données d’instrumentation
Les discussions précédentes ont représenté une vue assez simple du mode de stockage des données d’instrumentation. En réalité, il peut être judicieux de stocker les différents types d’informations à l’aide des technologies les plus appropriées au mode d’utilisation de chacun des types. Par exemple, le stockage d’objets blob et de table Azure ont certaines similitudes dans la manière dont ils sont accessibles, mais ont des limitations concernant les opérations que vous pouvez effectuer en les utilisant, et la granularité des données qu’ils conservent est assez différente. Si vous devez effectuer plusieurs opérations analytiques ou avez besoin de fonctions de recherche en texte intégral sur les données, il peut être plus pertinent d’utiliser le stockage de données qui fournit des fonctionnalités optimisées pour des types spécifiques de demandes et d’accès aux données. Par exemple, les données de compteur de performances pourraient être stockées dans une base de données SQL pour permettre une analyse ad hoc. Les journaux de suivi peuvent être mieux stockés dans Azure DocumentDB, les informations de sécurité peuvent être écrites dans HDFS et les informations nécessitant une recherche en texte intégral pourraient être stockées à l’aide de la recherche élastique (qui peut également accélérer les recherches en utilisant l’indexation riche.) Vous pouvez implémenter un service supplémentaire qui extrait régulièrement des données à partir du stockage partagé, partitionne et filtre les données en fonction de leur objectif, puis les écrit dans un ensemble approprié de magasins de données comme illustré à la figure 6. Une autre approche consiste à inclure cette fonctionnalité dans le processus de consolidation et de nettoyage, et à écrire les données directement dans ces magasins à mesure qu’elles sont extraites plutôt que de les enregistrer dans une zone de stockage partagée intermédiaire. Chaque approche a des avantages et des inconvénients. L’implémentation d’un service distinct de partitionnement réduit la charge sur le service de consolidation et de nettoyage et permet au moins que certaines données partitionnées soient régénérées si nécessaire (en fonction de la quantité de données conservées dans le stockage partagé). Toutefois, ce service consomme des ressources supplémentaires et il peut y avoir un délai entre la réception des données d’instrumentation à partir de chaque instance d’application et la conversion des données en informations utilisables.

![](media/best-practices-monitoring/DataStorage.png)

_Figure 6 : Partitionnement des données en fonction des exigences d’analyse et de stockage_

Les mêmes données d’instrumentation peuvent servir à plus d’une fin. Par exemple, les compteurs de performances peuvent être utilisés pour fournir une vue historique des performances du système au fil du temps, mais ces informations peuvent également être combinées avec d’autres données d’utilisation pour générer les informations de facturation client. Dans ces situations, les mêmes données peuvent être envoyées à plusieurs destinations, par exemple une base de données de documents qui peut agir comme un magasin à long terme pour la conservation des informations de facturation et un magasin multidimensionnel pour la gestion d’analyses des performances complexes.

Vous devez également tenir compte du degré d’urgence de ces données. Les données qui fournissent des informations pour les alertes doivent être accessibles rapidement et doivent donc être conservées dans le stockage de données rapide et indexées ou structurées pour optimiser les demandes exécutées par le système d’alerte. Dans certains cas, il peut être nécessaire que le service de télémétrie qui collecte les données sur chaque nœud formate et enregistre les données localement afin qu’une instance locale du système d’alerte puisse rapidement informer de tout problème. Les mêmes données peuvent être transmises au service d’écriture de stockage indiqué dans les diagrammes précédents et stockées de façon centralisée si elles doivent servir à d’autres fins.

Les informations utilisées pour une analyse plus réfléchie, le reporting et l’identification de tendances historiques sont moins urgentes et peuvent être stockées d’une manière qui prend en charge l’exploration de données et les demandes ad hoc. Pour plus d’informations, consultez la section [Prise en charge de l’analyse à chaud, modérée et à froid](#supporting-hot-warm-and-cold-analysis) plus loin dans ce document.

#### _Rotation des journaux et conservation des données_
L’instrumentation peut générer des volumes considérables de données. Ces données peuvent être conservées dans des emplacements différents, des fichiers journaux bruts, fichiers de suivi et autres informations capturées sur chaque nœud à la vue consolidée, nettoyée et partitionnée de ces données conservées dans le stockage partagé. Dans certains cas, une fois que les données ont été traitées et transférées, les données source brutes d’origine peuvent être retirées de chaque nœud. Dans d’autres cas, il peut être nécessaire ou simplement utile d’enregistrer les informations brutes. Par exemple, les données générées à des fins de débogage peuvent être laissées disponibles dans leur forme brute mais peuvent être ensuite ignorées assez rapidement une fois que les bogues ont été corrigés. Les données de performances ont souvent une durée de vie plus longue pour leur permettre d’être utilisées à des fins d’identification des tendances de performances et de planification de la capacité. La vue consolidée de ces données est en général conservée en ligne pour une période limitée afin de permettre un accès rapide, après laquelle elle peut être archivée ou ignorée. Les données collectées pour la mesure et la facturation des clients peuvent devoir être enregistrées indéfiniment. En outre, des exigences réglementaires peuvent imposer l’archivage et la sauvegarde des informations collectées à des fins d’audit et de sécurité. Ces données sont également sensibles et peuvent devoir être chiffrées ou protégées pour empêcher toute falsification. Vous ne devez jamais enregistrer des informations telles que les mots de passe des utilisateurs ou d’autres informations qui peuvent être utilisées pour commettre une usurpation d’identité. De telles informations doivent être supprimées des données avant qu’elles ne soient stockées.

#### _Sous-échantillonnage_
Il est souvent utile de stocker les données historiques pour être en mesure d’identifier les tendances à long terme. Plutôt que d’enregistrer les données anciennes dans leur intégralité, il peut être possible de sous-échantillonner les données pour réduire leur résolution et les coûts de stockage. Par exemple, plutôt que d’enregistrer des indicateurs de performance minute par minute, les données vieilles de plus d’un mois pourraient être consolidées pour former une vue heure par heure.

### Meilleures pratiques pour collecter et stocker des informations de journalisation
La liste suivante récapitule les meilleures pratiques pour la capture et le stockage des informations de journalisation.

- L’agent de surveillance ou le service de collecte de données doit fonctionner comme un service hors processus et doit être simple à déployer.
- Toutes les sorties à partir de l’agent de surveillance ou du service de collecte de données doit avoir un format non spécifique indépendant de l’ordinateur, du système d’exploitation ou du protocole réseau. Par exemple, émettre les informations dans un format autodescriptif tels que JSON, MessagePack ou Protobuf plutôt que ETL/ETW. Utiliser un format standard permet au système de construire des pipelines de traitement ; des composants qui lisent, transforment et sortent des données au format convenu pouvant être facilement intégrées.
- Le processus de collecte de données et de surveillance doit être effectué en mode de prévention des défaillances et ne doit pas déclencher de conditions d’erreurs en cascade.
- En cas de défaillance temporaire lors de l’envoi des informations à un récepteur de données, l’agent de surveillance ou le service de collecte des données doit être préparé à réorganiser les données de télémétrie afin que les informations les plus récentes soient envoyées en premier (l’agent de surveillance/le service de collecte des données peut choisir de supprimer les anciennes données, ou de les enregistrer localement et de les transmettre ultérieurement à des fins de mise à jour, à sa discrétion).

## Analyse des données et diagnostic des problèmes
Une partie importante du processus de surveillance et de diagnostic consiste à analyser les données collectées pour obtenir une image de l’état général du système. Vous devez avoir défini vos propres indicateurs clés de performance et les mesures de performances. Il est important de comprendre comment vous pouvez structurer les données qui ont été collectées pour répondre à vos besoins d’analyse. Il est également important de comprendre comment les données capturées dans les différentes mesures et fichiers journaux sont corrélées, car ces informations peuvent être importantes pour suivre une séquence d’événements et diagnostiquer les problèmes qui surviennent.

Comme décrit dans la section [Consolidation des données d’instrumentation](#consolidating-instrumentation-data), les données pour chaque partie du système sont généralement capturées localement, mais en général doivent être combinées avec des données générées sur d’autres sites participant au système. Ces informations nécessitent d’être corrélées avec attention pour s’assurer que les données sont combinées avec précision. Par exemple, les données d’utilisation pour une opération peuvent s’étendre sur un nœud qui héberge un site web auquel se connecte un utilisateur, un nœud qui exécute un service distinct accessible dans le cadre de cette opération et un stockage de données conservé sur un autre nœud. Ces informations doivent être liées entre elles pour fournir une vue d’ensemble de l’utilisation des ressources et du traitement pour l’opération. Un prétraitement et un filtrage des données peuvent se produire sur le nœud sur lequel les données sont capturées, tandis que l’agrégation et le formatage sont susceptibles de se produire sur un nœud central.

<a name="supporting-hot-warm-and-cold-analysis"></a>
### Prise en charge de l’analyse à chaud, modérée et à froid
L’analyse et le reformatage des données à des fins de visualisation, de reporting et d’alerte peuvent être un processus complexe qui utilise son propre ensemble de ressources. Certaines formes de surveillance sont prioritaires et requièrent que l’analyse immédiate des données soit efficace. C’est ce qu’on appelle _l’analyse à chaud_. Les exemples incluent les analyses nécessaires pour les alertes et certains aspects de la surveillance de la sécurité (tels que la détection d’une attaque sur le système). Les données requises à ces fins doivent être rapidement disponibles et structurées pour permettre un traitement efficace ; dans certains cas il peut être nécessaire de déplacer le traitement de l’analyse vers les nœuds individuels sur lesquels les données sont conservées.

D’autres formes d’analyse sont moins prioritaires et peuvent nécessiter des opérations de calcul et d’agrégation une fois les données brutes reçues. C’est ce qu’on appelle _l’analyse modérée_. L’analyse des performances fait souvent partie de cette catégorie. Dans ce cas, il est peu probable qu’un événement de performances unique et isolé soit statistiquement significatif (il peut être provoqué par un pic ou un problème soudain), alors que les données à partir d’une série d’événements devraient fournir une image plus fiable des performances du système. L’analyse modérée permet également de diagnostiquer les problèmes d’intégrité. Un événement d’intégrité est généralement traité en réalisant une analyse à chaud et peut déclencher une alerte immédiatement. Un opérateur doit être en mesure d’explorer les raisons de l’événement d’intégrité en examinant les données du chemin d’accès encore chaud. Ces données doivent contenir des informations sur les événements entraînant le problème qui a provoqué l’événement d’intégrité.

Certains types de surveillance génèrent des données à long terme et l’analyse peut être effectuée à une date ultérieure, éventuellement selon une planification prédéfinie. Dans certains cas, l’analyse peut devoir effectuer un filtrage complexe d’importants volumes de données capturées sur une période donnée. C’est ce qu’on appelle _l’analyse à froid_. La condition principale est de stocker en toute sécurité les données une fois qu’elles ont été capturées. Par exemple la surveillance et l’audit de l’utilisation nécessitent une image précise de l’état du système à des points réguliers dans le temps, mais ces informations d’état n’ont pas besoin d’être disponibles immédiatement après leur collecte pour être soumises au traitement. L’analyse à froid peut être également utilisée pour fournir des données pour l’analyse prédictive d’intégrité. Les informations historiques collectées sur une période de temps spécifiée peuvent être utilisées conjointement avec les données d’intégrité actuelles (récupérées à partir du chemin d’accès chaud) pour identifier les tendances qui peuvent provoquer rapidement des problèmes d’intégrité. Dans ces cas, il peut être nécessaire de déclencher une alerte pour permettre de prendre des mesures correctives.

### Corrélation des données
Les données capturées par l’instrumentation peuvent fournir un instantané de l’état du système, mais l’objectif de l’analyse est de rendre ces données exploitables. Par exemple, quelle est la cause d’un chargement E/S intense au niveau du système à un moment spécifique ? Est-il le résultat d’un grand nombre d’opérations de base de données ? A-t-il une influence sur les temps de réponse de la base de données, le nombre de transactions par seconde et les temps de réponse de l’application au même point de jonction ? Dans ce cas, une mesure corrective pouvant réduire la charge peut consister à partitionner les données sur plusieurs serveurs. En outre, des exceptions peuvent se produire suite à une erreur à n’importe quel niveau du système et souvent une exception à un niveau déclenche une autre erreur au niveau supérieur. Pour ces raisons, vous devez être en mesure d’établir une corrélation entre les différents types de données de surveillance à chaque niveau afin de produire une vue d’ensemble de l’état du système et des applications qui s’exécutent sur ce système. Vous pouvez ensuite utiliser ces informations pour décider si le système fonctionne de manière satisfaisante ou non, et déterminer ce qui peut être fait pour améliorer la qualité du système.

Comme décrit dans la section [Informations pour la corrélation des données](#information-for-correlating-data), vous devez vous assurer que les données d’instrumentation brutes incluent un contexte et des informations de l’ID d’activité suffisants pour prendre en charge les agrégations requises pour la corrélation des événements. En outre, ces données peuvent être conservées sous différents formats et il peut être nécessaire d’analyser ces informations pour les convertir en un format standardisé à des fins d’analyse.

### Résolution et diagnostic des problèmes
Le diagnostic nécessite de pouvoir déterminer la cause des erreurs ou du comportement inattendu, y compris de réaliser une analyse de la cause première. Les informations requises sont généralement les suivantes :

- Des informations détaillées à partir des journaux des événements et des suivis pour l’ensemble du système ou pour un sous-système spécifié pendant un laps de temps spécifié.
- Des arborescences des appels de procédure résultant des exceptions et des erreurs de tout niveau spécifié qui se produisent au sein du système ou d’un sous-système spécifié pendant une période de temps spécifiée.
- Des vidages sur incident pour tous les processus ayant échoué n’importe où dans le système ou pour un sous-système spécifié pendant un laps de temps spécifié.
- Des journaux d’activité consignant les opérations effectuées par tous les utilisateurs ou pour les utilisateurs sélectionnés pendant une période spécifiée.

L’analyse des données pour résoudre les problèmes requiert souvent une connaissance technique approfondie de l’architecture du système et des différents composants qui constituent la solution. Par conséquent, elle nécessite souvent une importante intervention manuelle pour interpréter les données, déterminer la cause de problèmes et recommander une stratégie appropriée pour les corriger. Il peut être judicieux de simplement stocker une copie de ces informations dans leur format d’origine et de les rendre disponibles pour l’analyse à froid par un expert.

## Visualisation des données et déclenchements des alertes
Un aspect important de tout système de surveillance est la possibilité de présenter les données de sorte qu’un opérateur puisse repérer rapidement les tendances ou les problèmes et d’informer rapidement un opérateur si un événement significatif nécessitant votre attention s’est produit.

La présentation des données peut prendre plusieurs formes, y compris la visualisation à l’aide de tableaux de bord, d’alertes et de rapports.

### Visualisation des tableaux de bord
La façon la plus courante pour visualiser des données consiste à utiliser des tableaux de bord qui affichent des informations sous forme de tableaux, graphiques ou toute autre manière illustrée. Ces éléments peuvent être paramétrés et un analyste doit être en mesure de sélectionner les paramètres importants (par exemple, la période de temps) pour toute situation spécifique. Les tableaux de bord peuvent être organisés hiérarchiquement, avec les tableaux de bord de niveau supérieur donnant une vue d’ensemble de chaque aspect du système, mais avec la possibilité de permettre à un opérateur d’explorer les détails. Par exemple, un tableau de bord qui illustre les E/S de disque pour le système doit permettre à un analyste d’explorer les données et d’afficher les taux d’E/S de chaque disque individuel afin de déterminer si un ou des périphériques plus spécifiques sont responsables d’un volume de trafic disproportionné. Dans l’idéal, le tableau de bord doit également afficher des informations connexes, telles que la source de chaque demande (utilisateur ou activité) qui génère cette E/S. Ces informations peuvent alors servir à déterminer si (et comment) la charge doit être équitablement répartie entre les périphériques et si le système serait plus performant si plusieurs périphériques étaient ajoutés. Un tableau de bord peut également utiliser un code couleur ou d’autres repères visuels pour indiquer les valeurs qui apparaissent anormales ou qui sont en dehors d’une plage attendue. À l’aide de l’exemple précédent, un disque avec un taux d’E/S approchant sa capacité maximale sur une période prolongée (un disque chaud) peut être indiqué en rouge, un disque avec un taux d’E/S qui atteint périodiquement sa limite maximale sur de courtes périodes (un disque tiède) peut être indiqué en jaune et un disque présentant une utilisation normale peut s’afficher en vert.

Notez que pour être efficace, un système de tableau de bord doit incorporer les données brutes avec lesquelles travailler. Si vous créez votre propre système de tableau de bord ou utilisez un tableau de bord développé par une autre organisation, vous devez comprendre quelles données d’instrumentation collecter, à quel niveau de granularité et comment elles doivent être formatées pour être utilisées par le tableau de bord.

Un bon tableau de bord n’affiche pas uniquement les informations, il permet également à un analyste de poser des questions ad hoc sur ces informations. Certains systèmes fournissent des outils de gestion pouvant être utilisés par un opérateur pour effectuer ces tâches et explorer les données sous-jacentes. Vous pouvez également, en fonction du référentiel utilisé pour conserver ces informations, interroger ces données directement, ou les importer dans des outils tels que Microsoft Excel pour approfondir l’analyse et le reporting.

> [AZURE.NOTE]Vous devez restreindre l’accès aux tableaux de bord au personnel autorisé ; ces informations peuvent être sensibles. Vous devez également protéger les données sous-jacentes présentées par le tableau de bord pour empêcher les utilisateurs de les modifier.

### Déclenchement d’alertes
La génération d’alerte consiste à analyser les données de surveillance et d’instrumentation, et à générer une notification si un événement significatif est détecté.

La génération d’alerte est utilisée pour vérifier que le système reste sain, réactif et sécurisé. C’est une partie importante de tout système qui donne des garanties de performances, de disponibilité et de confidentialité aux utilisateurs, et les données peuvent devoir être traitées immédiatement. Un opérateur peut devoir être notifié de l’événement qui a déclenché l’alerte. La génération d’alerte peut également servir à appeler des fonctions système telles que la mise à l’échelle automatique.

La génération d’alerte dépend généralement des données d’instrumentation suivantes :

- Événements de sécurité. Si les journaux des événements indiquent que des échecs d’authentification et/ou d’autorisation répétés se produisent, le système peut être attaqué et un opérateur doit être informé.
- Mesures de performances Le système doit répondre rapidement si une mesure de performance spécifique dépasse un seuil spécifié.
- Informations de disponibilité. Si une erreur est détectée, il peut être nécessaire de redémarrer rapidement un ou plusieurs sous-systèmes ou de basculer vers une ressource de sauvegarde. Des erreurs répétées dans un sous-système peuvent indiquer des problèmes plus sérieux.

Les opérateurs peuvent recevoir les informations d’alerte à l’aide de plusieurs canaux de remise comme un courrier électronique, un périphérique de radiomessagerie ou un SMS. Une alerte peut également inclure une indication de l’importance de la situation survenue. De nombreux systèmes d’alerte prennent en charge les groupes d’abonnés, et le même ensemble d’alertes peut être envoyé à tous les opérateurs qui sont membres du même groupe.

Un système d’alerte doit être personnalisable et les valeurs appropriées peuvent être fournies en tant que paramètres à partir des données d’instrumentation sous-jacentes. Cette approche permet à un opérateur de filtrer les données et de se concentrer sur ces seuils ou combinaisons de valeurs qui présentent un intérêt. Notez que dans certains cas, les données d’instrumentation brutes peuvent être fournies au système d’alerte, tandis que dans d’autres situations, il peut être plus approprié de fournir des données agrégées (par exemple, une alerte peut être déclenchée si l’utilisation du processeur pour un nœud dépasse 90 % au cours des 10 dernières minutes). Les détails fournis au système d’alerte doivent également inclure les résumés appropriés et les informations de contexte. Ces données permettent de réduire le risque d’événements faux positifs déclenchant une alerte.

### Reporting
Le reporting est utilisé pour générer une vue d’ensemble du système et peut inclure des données d’historique, ainsi que des informations actuelles. Les exigences de reporting elles-mêmes se répartissent en deux grandes catégories : reporting opérationnel et reporting de sécurité.

Le reporting opérationnel inclut généralement les aspects suivants :

- Agréger les statistiques qui vous permettent de comprendre l’utilisation des ressources du système global ou des sous-systèmes spécifiés pendant un laps de temps spécifié.
- Identifier les tendances de l’utilisation des ressources pour le système global ou des sous-systèmes spécifiés pendant une période donnée.
- Surveiller les exceptions qui se sont produites dans le système ou dans des sous-systèmes spécifiés pendant une période donnée.
- Déterminer l’efficacité de l’application quant aux ressources déployées et comprendre si le volume des ressources (et leur coût associé) peut être réduit sans avoir d’impact inutile sur les performances.

Le reporting de sécurité vise à suivre l’utilisation du système par le client et peut inclure les points suivants :

- Réaliser un audit des opérations de l’utilisateur. Cela exige d’enregistrer les demandes individuelles effectuées par chaque utilisateur ainsi que les dates et heures. Les données doivent être structurées pour permettre à un administrateur de reconstruire rapidement la séquence d’opérations effectuées par un utilisateur spécifique sur une période spécifiée.
- Suivre l’utilisation des ressources par l’utilisateur. Cela nécessite d’enregistrer la manière dont chaque demande accède aux diverses ressources qui constituent le système, ainsi que de la durée. Un administrateur doit être en mesure d’utiliser ces données pour générer un rapport d’utilisation par l’utilisateur sur une période de temps donnée, éventuellement à des fins de facturation.

Dans de nombreux cas, les rapports peuvent être générés par les processus de traitement par lots selon un calendrier défini (la latence n’est normalement pas un problème), mais ils doivent également être générés sur une base ad hoc, le cas échéant. Par exemple, si vous stockez des données dans une base de données relationnelle telle qu’une base de données SQL Azure, vous pouvez utiliser un outil tel que SQL Server Reporting Services pour extraire et formater les données et les présenter sous la forme d’un ensemble de rapports.

## Conseils et modèles connexes
- Les conseils sur la mise à l’échelle automatique décrivent comment alléger les contraintes de gestion en réduisant le recours à un opérateur dédié à la surveillance continue d’un système, ainsi qu’à l’ajout et au retrait des ressources.
- Le [Health Endpoint Monitoring Pattern](https://msdn.microsoft.com/library/dn589789.aspx) décrit comment implémenter des contrôles fonctionnels au sein d’une application à laquelle accèdent des outils externes via des points de terminaison exposés à intervalles réguliers.
- Le [Priority Queue Pattern](https://msdn.microsoft.com/library/dn589794.aspx) montre comment hiérarchiser les messages en file d’attente afin que les demandes urgentes soient reçues et puissent être traitées avant les messages moins urgents.

## Plus d’informations
- L’article [Monitor, Diagnose, and Troubleshoot Microsoft Azure Storage](storage-monitoring-diagnosing-troubleshooting.md) sur le site web Microsoft.
- L’article [Microsoft Azure: Telemetry Basics and Troubleshooting](http://social.technet.microsoft.com/wiki/contents/articles/18146.windows-azure-telemetry-basics-and-troubleshooting.aspx) sur le site web Microsoft.
- La page [Recueillir des données de journaux à l’aide des diagnostics Azure](https://msdn.microsoft.com/library/azure/gg433048.aspx) sur le site web Microsoft.
- La page [Configuration des diagnostics pour Azure Cloud Services et Azure Virtual Machines](https://msdn.microsoft.com/library/azure/dn186185.aspx) sur le site web Microsoft.
- Les pages [Cache Redis Microsoft Azure](http://azure.microsoft.com/services/cache/), [Azure DocumentDB](http://azure.microsoft.com/services/documentdb/) et [HDInsight](http://azure.microsoft.com/services/hdinsight/) sur le site web Microsoft.
- La page [Utilisation des files d’attente](http://azure.microsoft.com/) sur le site web Microsoft.
- L’article [Business Intelligence de SQL Server dans les machines virtuelles Azure](./virtual-machines/virtual-machines-sql-server-business-intelligence.md) sur le site web Microsoft.
- La page [Présentation de la surveillance des alertes et des notifications dans Azure](https://msdn.microsoft.com/library/azure/dn306639.aspx) sur le site web Microsoft.
- La page [Application Insights](app-insights-get-started/) sur le site web Microsoft.

<!---HONumber=Nov15_HO4-->