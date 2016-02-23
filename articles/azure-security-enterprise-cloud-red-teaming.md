<properties
   pageTitle="Microsoft Enterprise Cloud - Red Teaming"
   description="Cet article aborde la stratégie de Microsoft concernant les tests de Red Teaming et les tests d’intrusion de site menés contre les applications, services et infrastructures cloud gérés par Microsoft. Vous allez apprendre comment Microsoft simule des violations réelles, effectue une surveillance continue de la sécurité et teste ses procédures de réponse aux incidents de sécurité pour valider et améliorer la sécurité de Microsoft Azure et d’Office 365."
   services="virtual-machines, cloud-services, storage"
   documentationCenter="na"
   authors="YuriDio"
   manager="swadhwa"
   editor=""/>

<tags
   ms.service="azure-security"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="02/17/2016"
   ms.author="yuridio"/>


#Microsoft Enterprise Cloud - Red Teaming

Le Red Teaming est 100 % conforme à toutes les exigences du secteur et offre à Microsoft et à nos clients, la garantie supplémentaire que les services de cloud de Microsoft effectuent en permanence une surveillance, des tests et des mises à jour de la sécurité pour s’adapter aux menaces en perpétuelle évolution auxquelles doivent faire face Microsoft et ses clients.

##Introduction

Les entreprises peuvent mieux se préparer à l’impact des menaces actuelles et futures en simulant des attaques réelles et en testant les tactiques, techniques et procédures utilisées par les cybercriminels. Essayer d’éviter les incidents de sécurité ne suffit plus, il est essentiel de considérer qu’un incident de sécurité peut se produire et même qu’il se produira. Les informations obtenues par l’intermédiaire du Red Teaming et des tests d’intrusion de site actif permettent de renforcer considérablement les défenses, d’améliorer les stratégies de réponse, d’entraîner les logiciels de défense et d’augmenter l’efficacité du programme de sécurité dans son ensemble.

Les entreprises ne peuvent pas identifier toutes les failles de détection de violations et de réponse en se concentrant uniquement sur les stratégies de prévention des incidents. Il est aussi important, voire plus, de comprendre comment détecter les violations et y répondre que de prendre des mesures pour les contrecarrer. En planifiant des scénarios catastrophes, à l’aide d’exercices de sécurité (exercices de réflexion et tests d’intrusion) et de Red Teaming (attaques réelles et tests d’intrusion), les entreprises peuvent développer les capacités nécessaires pour détecter les tentatives d’intrusion et améliorer considérablement les réponses associées aux violations de sécurité.

###Microsoft Enterprise Cloud - Red Teaming 

Cet article décrit la manière dont Microsoft utilise le Red Teaming, une approche de test d’intrusion de site actif, sur l’infrastructure, les services et les applications gérés par Microsoft. Vous allez également découvrir comment Microsoft simule des violations réelles, surveille en permanence la sécurité et teste les réponses aux incidents de sécurité pour vérifier et améliorer la sécurité de Microsoft Azure et d’Office 365 (O365). Notez toutefois qu’aucune donnée client n’est délibérément ciblée par le Red Teaming pendant les tests d’intrusion de site actif. Les tests sont effectués sur l’infrastructure de Microsoft Azure et d’O365, ainsi que sur les clients, applications et données de Microsoft uniquement. Les clients, applications et données des clients hébergés sur Microsoft Azure ou O365 ne sont jamais ciblés.

###Au-delà de la prévention 

Les technologies et stratégies de prévention ne peuvent pas garantir la sécurité contre toutes les attaques. Au vu de la variété des menaces existantes, il est vital de considérer que les violations de sécurité représentent un risque bien réel. Il arrive souvent qu’une entreprise soit touchée, sans s’en rendre compte immédiatement. Compte tenu de cette réalité, il est important de repenser les stratégies de détection et de réponse de manière à repousser les limites de l’infrastructure, du personnel, des processus et des technologies des entreprises.

![Citation du NSA](./media/azure-security-enterprise-cloud-red-teaming/azure-security-enterprise-cloud-red-teaming-fig1.png)

Selon le Rapport d’enquête 2014 sur les compromissions de données de Verizon, dans environ 80 % des cas les entreprises victimes d’une violation ne l’ont pas détectée (pages 12, <http://www.verizonenterprise.com/DBIR/2014/>[)](http://www.verizonenterprise.com/DBIR/2014/). La violation leur a été signalée par un tiers, comme un client, un représentant de la loi, un service tiers ou une agence externe de détection des fraudes. Et ces chiffres ne concernent que les cas où le cybercriminel a été détecté ! Ils n’incluent pas les cas où l’attaquant est passé entre les mailles du filet.

Dans les sections qui suivent, nous aborderons le passage à une nouvelle stratégie de sécurité qui est utilisée sur les services de cloud Microsoft, y compris Microsoft Azure et Office 365. Cette stratégie de sécurité, appelée « Assume Breach » (Supposer la compromission), est une méthodologie qui implique de modifier en profondeur la conception, l’ingénierie et l’exploitation en supposant que les pirates ont déjà exploité des vulnérabilités et obtenu des accès privilégiés et qu’ils agissent sur les services de production.

##Méthodologie de sécurité traditionnelle 

Les méthodologies de sécurité traditionnelles se concentrent essentiellement sur la prévention. La prévention est une stratégie visant à éliminer les vulnérabilités et à ainsi réduire le risque de compromission en amont. Dans le portefeuille Microsoft Online Services (qui inclut Microsoft Azure, Office 365, CRM Online, etc.), cela implique une amélioration continue des processus de sécurité à l’aide de nos programmes de [*développement de la sécurité*](http://www.microsoft.com/security/sdl/default.aspx) [*Garantie de sécurité opérationnelle*](http://www.microsoft.com/download/details.aspx?id=40872). La modélisation des menaces, l’analyse statique du code et les tests de sécurité sont utiles dans l’énumération, la réduction et la gestion des surfaces d’attaque, mais ils n’éliminent pas tous les risques liés à la sécurité.

Une des stratégies de prévention de Microsoft consiste à limiter l’accès en tant qu’opérateur/administrateur aux employés qui ont un besoin réel et prouvé de cet accès et qui remplissent les conditions d’éligibilité (vérification des antécédents, exigences de conformité et de sécurité, fonction/poste nécessitant l’accès en question, etc.). En outre, les administrateurs ne reçoivent jamais d’autorisation permanente. Ils se voient attribuer un accès de type « Juste-à-temps » (JIT) [1] et des droits d’administration de type « Juste assez » (JEA) [2]. D’autres exemples seraient la séparation de l’environnement de messagerie des employés de l’environnement de production et l’utilisation de stations de travail renforcées, spécialisées et hautement sécurisées pour les opérations sensibles.

Dans la mesure du possible, les interventions humaines sont remplacées par des processus automatisés, sérieusement audités et basés sur l’outil. Quelques exemples de fonctions de routine incluent le déploiement, le débogage, la collecte de données de diagnostic et l’administration du service. Microsoft Online Services continue à investir dans l’automatisation des opérations et la sécurité des systèmes, afin de réduire les risques potentiels liés à la sécurité.

Au niveau de la technologique d’exploitation, les investissements dans la prévention des violations limitent l’exposition aux risques, mais ne peuvent jamais les éliminer complètement. Par conséquent, même si l’adoption et le développement continu des programmes de sécurité (comme SDL et OSA) et des technologies Microsoft restent un moyen important pour déjouer les violations de sécurité, toutes les entreprises doivent accepter que les failles de sécurité auront un jour ou l’autre un impact sur leurs activités, leurs services et leurs utilisateurs.

###Menaces nouvelles et émergentes 

Au cours de ces cinq dernières années, une catégorie de menace spécifique s’est plus largement répandue. Les APT (Advanced Persistent Threat) désignaient à l’origine les tentatives d’infiltration de bases militaires, de bases industrielles de défense et de réseaux gouvernementaux commanditées par un état ou une nation dans le but spécifique d’exfiltrer des données sensibles. Aujourd’hui, le terme APT est largement utilisé dans les médias et le secteur de la sécurité pour décrire une attaque qui semble cibler spécifiquement une entreprise ou qui semble très technique, que l’attaque soit ou non réellement d’un niveau avancé ou persistante. Les caractéristiques courantes d’une APT incluent :

- Planification sophistiquée

- Ciblage spécifique/séquentiel

- Reconnaissance efficace

- Utilisation d’outils avancés

- Ingénierie sociale

Les pirates d’aujourd’hui disposent de ressources substantielles et d’une bonne orchestration. Quelle que soit la complexité des attaques, les tendances en matière d’incident de sécurité de fin 2009 à aujourd’hui indiquent clairement que la probabilité d’un événement est en hausse et que les risques augmentent. La complexité accrue et la nature ciblée des menaces à la sécurité, qui sont par ailleurs en constante augmentation, suggèrent qu’aucun utilisateur ni entreprise n’est à l’abri d’une compromission de la sécurité.

Au vu de la situation actuelle, il ne suffit plus de mettre l’accent uniquement sur la prévention pour faire face aux pirates déterminés et insistants. En outre, avec les outils de sécurité courants, tels que les antivirus et les systèmes de détection d’intrusion (IDS), il est difficile de détecter et de lutter contre la variété d’attaques existantes. Les contrôles du périmètre du réseau peuvent repousser les amateurs, mais les pirates plus motivés et expérimentés trouveront toujours le moyen de franchir ce périmètre virtuel. Par conséquent, les entreprises sont trop souvent mal préparées lorsqu’elles doivent faire face à toute l’étendue d’une violation.

Avec l’évolution de l’informatique et l’adoption du cloud, les limites de l’entreprise ne peuvent plus être définies par un périmètre de réseau géré physiquement ou virtuellement à travers des pare-feu. Les données d’entreprise, notamment les données sensibles et les applications, sont stockées presque partout : en local, dans des centres de données privés, dans le cloud, chez des partenaires et sur divers appareils appartenant aux utilisateurs. Et tous ces stockages nécessitent des stratégies de sécurité fondamentalement différentes ainsi qu’un changement dans les méthodes de sécurité utilisées par la plupart des entreprises.

La réponse aux violations a toujours présenté de nombreux défis, y compris l’identification de l’étendue de la compromission, la notification en temps voulu aux parties prenantes et clients, la recherche de pertes éventuelles de données et la récupération des ressources compromises. Au vu de l’ingéniosité des pirates modernes et de l’évolution de l’informatique, la réponse aux violations n’a jamais été plus complexe. C’est pour cela qu’une stratégie de sécurité efficace repose non seulement sur la prévention, mais aussi sur l’acceptation du fait qu’un adversaire insistant trouvera toujours le moyen de briser les défenses.

##Méthodologie « Assume Breach » 

Face à la variété des menaces, il est nécessaire d’adopter des stratégies de sécurité efficaces incluant non seulement des solutions de prévention, mais aussi de détection et de réponse aux violations. À l’aide d’une analyse détaillée des tendances de sécurité, Microsoft a commencé à défendre et à mettre en évidence la nécessité d’investir davantage dans les processus et les technologies de sécurité réactifs qui se concentrent non plus uniquement sur la prévention des menaces, mais aussi sur la détection et la réponse aux menaces émergentes. En outre, en raison des changements survenus dans le paysage des menaces et après une analyse approfondie, Microsoft a décidé de pousser sa stratégie de sécurité au-delà de la simple prévention des failles de sécurité afin de pouvoir faire face aux violations lorsqu’elles se produisent. Cette stratégie ne se contente plus d’envisager une faille, elle pose directement la question suivante : quand la violation aura-t-elle lieu ?

Alors que Microsoft applique sa méthodologie « Assume Breach » depuis de nombreuses années déjà, la plupart des clients n’ont pas conscience du travail effectué dans les coulisses pour renforcer le cloud Microsoft. « Assume Breach » est un état d’esprit qui guide les investissements en matière de sécurité, les décisions en matière de conception et les pratiques de sécurité opérationnelle. « Assume Breach » limite la confiance placée dans les applications, les services, les identités et les réseaux en les traitant tous (qu’ils soient internes ou externes) comme s’ils étaient non sécurisés et sans doute déjà compromis. La stratégie « Assume Breach » n’est pas née à la suite d’une violation d’un des services professionnels ou de cloud de Microsoft, mais elle représente une réponse au fait que de nombreuses entreprises, dans tout le secteur, ont subi des violations en dépit de toutes les tentatives pour les contrecarrer.

Même si la prévention des failles reste un élément essentiel de la stratégie de toute entreprise, les pratiques doivent être testées et améliorées en continu pour faire face efficacement aux attaques modernes telles que les APT. Pour se préparer à faire face à une violation, les entreprises doivent tout d’abord créer et maintenir des procédures de réponse de sécurité fiables, reproductibles et testées.

Tandis que les procédures de prévention des violations, telles que la modélisation des menaces, la révision du code et les tests de sécurité, sont très répandues dans les cycles de vie de développement sécurisés, la stratégie « Assume Breach » offre de nombreux avantages qui permettent de garantir une sécurité globale en testant et en mesurant les capacités de réaction en cas de compromission. Chez Microsoft, nous avons décidé d’appliquer cette stratégie par le biais d’exercices de sécurité et de tests d’intrusion de site actif pour mettre à l’épreuve nos plans de réponse et améliorer notre capacité de détection et de réponse.

![Empêcher la violation et « Assume Breach »](./media/azure-security-enterprise-cloud-red-teaming/azure-security-enterprise-cloud-red-teaming-fig2.png)

*Figure 1: modèle de prévention et modèle « Assume Breach »*

Avec le modèle « Assume Breach », l’accent est mis sur l’identification et la résolution des failles dans :

- La détection d’attaque et d’intrusion

- La réponse aux attaques et aux intrusions

- La récupération suite à une fuite de données, à une falsification ou à une compromission

- La prévention des attaques et des intrusions futures

La méthodologie « Assume Breach » vérifie que les mécanismes de protection, de détection et de réponse sont correctement implémentés et qu’ils réduisent même les menaces potentielles provenant de « pirates expérimentés » (qui utilisent des ressources légitimes, comme des comptes ou ordinateurs compromis).

La nature des modèles informatiques hybrides et de cloud augmente l’importance des tests d’intrusion dans le maintien de la sécurité des environnements d’exploitation, y compris dans la préparation à la gestion des violations. Étant donné que de nombreux aspects du cloud Microsoft ne sont pas sous le contrôle direct d’un client, Microsoft travaille pour non seulement éviter, mais aussi détecter les attaques contre l’infrastructure, la plate-forme et les services et y répondre.

Par conséquent, Microsoft organise régulièrement des exercices de sécurité et des tests Red Teaming pour évaluer et améliorer les pratiques « Assume Breach » de Microsoft. Via la modélisation d’attaques réelles et les tests d’intrusion, Microsoft peut tester sa capacité à gérer les attaques et à identifier les failles de détection et de réponse, et concentrer ses ressources pour combler ces lacunes. En pratiquant la réponse aux incidents de sécurité, en effectuant une surveillance continue, ainsi que des analyses et des exercices de récupération, Microsoft s’efforce de développer les capacités critiques nécessaires pour faire face aux violations.

###Exécution de la méthodologie « Assume Breach » 

Le modèle « Assume Breach » dans les services cloud de Microsoft a été initialement implémenté via des exercices de sécurité, puis des exercices de violation effective, appelés tests Red Team, qui simulent des attaques réelles. Les tests Red Team permettent d’évaluer les capacités de Microsoft à répondre à des attaques ciblées et persistantes, dans le but de réduire considérablement le temps moyen de détection (MTTD) [3] et le temps moyen de récupération (MTTR) [4].


![Cycle d’exécution de la méthodologie « Assume Breach »](./media/azure-security-enterprise-cloud-red-teaming/azure-security-enterprise-cloud-red-teaming-fig3.png)

*Figure 2. Cycle d’exécution de la méthodologie « Assume Breach »*

Les exercices de sécurité sont des exercices que l’on peut comparer aux exercices d’incendie. Les exercices Red Team offrent des simulations d’incidents de sécurité de bout en bout aussi réalistes que possible, qui préparent les personnes concernées à réagir en cas de violations réelles.

Les exercices de sécurité et les tests Red Team permettent de tester les réponses aux incidents de sécurité, avant l’apparition d’un problème réel. Une équipe entraînée a toujours plus de chances de bien réagir en cas de violation réelle.


###Exercices de sécurité 

Avant de consacrer des ressources aux tests Red Team, chez Microsoft, nous avons commencé avec des exercices de réflexion sur la sécurité, appelés parfois wargames. Ces exercices de sécurité ressemblent à de la [modélisation de menace SDL](http://msdn.microsoft.com/magazine/dd148644.aspx)[,](http://msdn.microsoft.com/magazine/dd148644.aspx)[ ](http://msdn.microsoft.com/magazine/dd148644.aspx)mais se concentrent sur les processus de réponse et sur le personnel de l’entreprise ou du service en charge de traiter une attaque. L’objectif des exercices de sécurité est d’améliorer les procédures de réponse aux incidents en faisant intervenir le personnel de différents services au sein de Microsoft, de la sécurité à l’ingénierie, en passant par l’exploitation. À mesure que nous effectuions ces tests, nous avons identifié les profils qui nous manquaient et que nous devions impliquer dans notre stratégie de réponse.

Tout individu ou groupe susceptible d’être utile en cas de violation réelle a été invité à participer à nos exercices. La liste des personnes impliquées s’est allongée et n’incluait plus uniquement la sécurité, l’ingénierie et l’exploitation, mais représentait presque tous les groupes au sein de l’entreprise, y compris le service juridique, les ressources humaines, le marketing et même les responsables. En impliquant un large éventail de services et de personnes nous avons pu créer les relations nécessaires entre les différents services et unités commerciales, assurant ainsi le développement de plans de réponse aux violations complets. Dans de nombreux cas, ces exercices ont aussi servi à informer et sensibiliser le personnel de Microsoft sur les risques et les besoins associés aux stratégies de réponse aux violations.

L’objectif principal des exercices de sécurité est de garantir l’exhaustivité des procédures opérationnelles permanentes (POP) et d’améliorer la détection et les réponses. Les exercices de sécurité se déroulent en (3) phases principales :

-   Identification du scénario d’attaque

-   Simulation d’attaque et de réponse

-   Post mortem

La phase d’identification du scénario d’attaque commence par l’élaboration d’une liste des attaques potentielles à modéliser. Ces scénarios d’attaque prennent de nombreuses formes et combinent souvent des scénarios d’attaque différents :

-   Denial of service (déni de service)

-   Apparition d’un programme malveillant

-   Exécution de code à distance

-   Compromission des données client

-   Attaquant interne

-   Compromission de service

Les scénarios sont souvent choisis parmi les plus communs, ou en fonction des incidents de sécurité précédemment signalés (qui ont affecté Microsoft, nos concurrents ou le secteur en général).

Une fois qu’une attaque a été choisie, un ou plusieurs membres du groupe, généralement des membres de l’équipe de sécurité, jouent le rôle de l’adversaire (attaque) et décrivent l’exécution de l’attaque. Il s’agit du début de la phase de simulation d’attaque et de réponse

Les autres personnes participant à l’exercice jouent le rôle de la défense et décrivent comment ils peuvent détecter l’attaque ou y répondre, en se basant sur des procédures opérationnelles permanentes qui existent déjà (ou en identifiant celles qui manquent ou qui sont incomplètes). Lorsque la défense propose des solutions pour détecter et atténuer l’attaque ou remettre le système en service, l’attaque répond par des contre-attaques invalidant souvent les méthodes de défense et forçant les défenseurs à repenser leur stratégie de détection et de réponse.

Par exemple, si défense suggère d’identifier et de bloquer l’adresse IP des personnes malveillantes, l’attaque réplique en montrant qu’il est facile de changer d’adresse IP source ou de tirer parti d’infrastructures d’attaque déjà existantes, comme les botnets, pour lancer l’attaque à partir de plusieurs adresses IP. Ces échanges, au cours des exercices de réflexion, permettent d’augmenter de manière significative les procédures opérationnelles permanentes et l’identification des nombreux besoins en investissements de sécurité. Ce sont des discussions très précieuses.

Même s’ils sont très utiles, ces exercices ont leurs limites concernant ce qui peut être mesuré, en particulier lorsqu’on parle d’attaques ciblées en cours et d’adversaires insistants. Par conséquent, Microsoft s’est donné pour but d’évaluer et de mesurer véritablement le niveau de préparation aux attaques persistantes et sophistiquées lancées par des adversaires déterminés. Pour ce faire, nous avons décidé que rien ne valait un exercice pratique en conditions réelles : un concept appelé Red Teaming. Le terme Red Teaming fait référence à l’utilisation de stratégies de violation réelles pour lancer une attaque ou s’introduire dans un système. Le Red Teaming met en pratique les aspects théoriques des exercices de réflexion.

### Le Red Teaming 

La stratégie de sécurité « Assume Breach » est exécutée par deux (2) groupes principaux : l’équipe Red Team (pirates) et l’équipe Blue Team (défenseurs) [5]. L’approche Red Teaming consiste à tester les systèmes et opérations de Microsoft Azure et d’Office 365 à l’aide des mêmes tactiques, techniques et procédures que celles utilisées par de véritables adversaires. Ces tests sont effectués sur l’infrastructure de l’environnement de production, sans connaissance préalable de l’infrastructure et de la plateforme d’ingénierie ou des équipes d’exploitation.

Ceci permet de tester la détection et les capacités de réponse, et permet d’identifier les vulnérabilités de la production, les erreurs de configuration, les hypothèses non valides ou d’autres problèmes de sécurité, le tout de manière contrôlée. Chaque attaque de l’équipe Red Team est suivie d’une discussion approfondie entre les deux équipes pour identifier les failles, traiter les résultats et améliorer considérablement la stratégie de réponse.

#### L’équipe Red Team 

L’équipe Red Team est un groupe employé à temps plein chez Microsoft qui se concentre sur la compromission de l’infrastructure et de la plate-forme Microsoft, ainsi que sur ses clients et applications. Ils représentent un adversaire (un groupe de hackers éthiques) dédié à l’exécution d’attaques ciblées et persistantes sur les services en ligne (infrastructure, plates-formes et applications Microsoft, sans toucher aux applications et données des clients finaux).

Le rôle d’une équipe Red Team consiste à attaquer les environnements et à s’y introduire en utilisant les procédures d’une chaîne de destruction classique [6] illustrée sur la Figure 3 :

![Étapes de la violation](./media/azure-security-enterprise-cloud-red-teaming/azure-security-enterprise-cloud-red-teaming-fig4.png)

*Figure 3. Étapes de la violation*

C’est pourquoi la recherche et la compréhension des incidents et des tendances en matière de menaces est essentielle aux équipes Red Team pour faire face aux dernières techniques d’attaque et aux outils utilisés par les adversaires. Microsoft Online Services est parmi les entités les plus attaquées et génère une quantité de données significative sur les menaces émergentes. L’équipe Red Team utilise ces informations non seulement pour modéliser, mais aussi pour exécuter des tactiques réelles utilisées dans les chaînes de destruction des adversaires.

L’équipe Red Team ne se contente plus d’étudier et de modéliser les tactiques connues des adversaires, elle développe ses propres techniques pour compromettre Microsoft Azure et Office 365, à l’aide d’outils d’intrusion et de méthodes d’attaque personnalisés. À l’instar d’un adversaire déterminé, l’équipe Red Team utilise des menaces émergentes et des menaces combinées pour compromettre la structure visée et change de tactique lorsqu’elle rencontre un obstacle.

Si les pirates talentueux et motivés parviennent à briser les défenses, l’équipe Red Team doit aussi en être capable. Les contrôles de périmètre peuvent décourager les amateurs, mais les pirates plus motivés trouveront toujours le moyen de franchir le périmètre de sécurité. Une fois à l’intérieur, l’équipe Red Team s’arrange souvent pour acquérir des privilèges lui permettant de pénétrer plus profondément encore dans l’infrastructure. Comme les pirates les plus compétents, l’équipe Red Team établit ensuite une tête de pont à partir de laquelle elle lance une attaque persistante, tout en modifiant continuellement son approche pour échapper à la détection.

Par exemple, l’équipe Red Team peut installer des outils personnalisés (robots, contrôles à distance, etc.) permettant un accès permanent à une ressource compromise et la récupération des informations à tout moment. Ce type d’attaque permet non seulement d’exfiltrer des données sensibles, mais aussi d’exploiter ces données compromises (là encore, seules les données de Microsoft sont visées, pas les données des clients).

En raison de la nature sensible et critique de la tâche, les employés qui travaillent dans les équipes Red Team de Microsoft doivent se conformer à des normes de sécurité et de conformité de très haut niveau. Ils doivent passer des tests de validation supplémentaires, leurs antécédents sont vérifiés et ils sont formés avant de pouvoir s’engager dans des scénarios d’attaque. Bien qu’aucune donnée client ne soit ciblée délibérément par les équipes Red Team, les employés qui les composent se plient aux mêmes exigences en matière d’accès aux données client

que le service d’exploitation qui déploie, gère, entretient et administre Microsoft Azure et Office 365. En outre, les équipes Red Team ne s’attaquent qu’aux plates-formes et infrastructures gérées par Microsoft. Les équipes Red Team se ciblent jamais les données ou applications des clients. Elles n’attaquent que les clients, applications et données dans le cloud détenus par Microsoft.

Mais cela n’empêche pas les équipes Red Team de se conformer à un code de conduite très strict. Les cinq (5) principes clés du code de conduite de l’équipe Red Team sont les suivants :

1.  Ne jamais provoquer intentionnellement un impact sur le contrat de niveau de service d’un client ou des interruptions de service.

2.  Ne jamais accéder intentionnellement aux données client ni les modifier.

3.  Ne jamais détruire intentionnellement.

4.  Ne jamais affaiblir les protections mises en place.

5.  Ne jamais divulguer les vulnérabilités et autres informations critiques en dehors de l’équipe Red Team et ne les partager qu’avec ceux qui ont besoin de les connaître.

En outre, les équipes Red Team travaillant sur Azure et O365 doivent se soumettre à un ensemble documenté des règles d’engagement, qui ont été développées pour s’assurer que le code de conduite est bien suivi. Ces règles d’engagement sont approuvées par la direction de Microsoft.

Un certain nombre de métriques importantes sont mesurées lors des attaques menées par l’équipe Red Team :

-   Le temps moyen de compromission (MTTC)

-   Le temps moyen d’élévation des privilèges ou « Pwnage » (MTTP)

Le temps moyen de compromission mesure le temps entre le début de l’exercice et le moment où l’équipe Red Team a compromis avec succès une ressource. À ce stade, l’équipe Red Team établit généralement son point d’appui dans l’environnement cible. Le temps moyen d’élévation des privilèges, que des adversaires préfèrent appeler « Pwnage », est le temps écoulé entre le début de l’exercice la compromission complète. Par exemple, dans les environnements Active Directory il s’agit du moment où l’équipe Red Team acquiert des privilèges d’administrateur de domaine et/ou compromet le contrôleur de domaine.

On peut comparer ce stade à un « Game Over », car la plupart des entreprises ne sont pas prêtes à traiter ce niveau de violation. Toutefois, il est également important de noter que les scénarios de type « Game Over » ne sont pas les seuls abordés par l’équipe Red Team. Les équipes Red Team peuvent avoir pour autres objectifs le vol de données, la compromission au niveau du client ou par déni de service. Les métriques de MTTC et de MTTP sont calculées via des exercices et peuvent également être calculées par ressource cible ou par type de ressource pour une granularité plus fine.

Le rôle des équipes Red Team de Microsoft est d’identifier les failles dans les contrôles de sécurité de la cible. Le suivi des MTTC et MTTP permet à Microsoft de déterminer une ligne de base à partir de laquelle s’améliorer en permanence. En exploitant les failles de sécurité tout en mettant en évidence les failles de surveillance et de récupération, le Red Teaming améliore les outils et les processus de réponse aux incidents (on en reparlera plus loin). Plus important encore, le Red Teaming produit un impact démontrable, via la compromission et prouve la nécessité de l’approche « Assume Breach ». Le Red Teaming permet par conséquent d’énumérer les risques et de justifier les nouvelles ressources de défense, et d’identifier les priorités et les besoins en matière d’investissement.

#### L’équipe Blue Team 

L’équipe Blue Team se compose soit d’un ensemble dédié de membres de la sécurité, soit de membres des services de réponse aux incidents de sécurité, d’ingénierie et d’exploitation. Quelle que soit la composition de l’équipe, elle est indépendante et fonctionne séparément de l’équipe Red Team. L’équipe Blue Team suit des processus de sécurité établis et utilise les derniers outils et technologies pour détecter les attaques et les intrusions et y répondre.

Comme dans la réalité, l’équipe Blue Team ne sait pas quand et comment l’équipe Red Team va attaquer, ni quelles méthodes seront utilisées. Leur travail, qu’il s’agisse d’une attaque de l’équipe Red Team ou d’une attaque bien réelle, consiste à détecter tous les incidents de sécurité et à y répondre. Pour cette raison, l’équipe Blue Team doit rester disponible 24 heures sur 24 et 7 jours sur 7, toute l’année. Elle doit par ailleurs réagir aux violations de l’équipe Red Team de la même façon qu’elle le ferait face à tout autre adversaire.

Lorsqu’un adversaire, comme une équipe Red Team, s’est infiltré dans un environnement, l’équipe Blue Team doit :

-   Réunir les preuves laissées par l’adversaire

-   Détecter les preuves comme des indicateurs de compromission

-   Alerter les équipes d’ingénierie et d’exploitation concernées

-   Triez les alertes pour déterminer si elles nécessitent une étude approfondie

-   Rassembler le contexte de l’environnement pour mesure l’étendue de la violation

-   Former un plan de correction pour contraindre ou expulser l’adversaire

-   Exécuter le plan de correction et de récupération après la violation

Ces étapes constituent la chaîne de destruction de réponse aux incidents de sécurité qui s’exécute parallèlement à la chaîne de destruction de l’adversaire, comme illustré sur la Figure 4 :

![Étapes de réponse aux violations](./media/azure-security-enterprise-cloud-red-teaming/azure-security-enterprise-cloud-red-teaming-fig5.png)

*Figure 4. Étapes de réponse aux violations*

En bref, les violations de l’équipe Red Team permettent de tester la capacité de l’équipe Blue Team à détecter et à répondre aux attaques réelles, de bout en bout. Plus important encore, elles permettent de mettre en pratique les stratégies de réponse aux incidents de sécurité avant qu’une intrusion authentique ne se produise (de la part d’un adversaire beaucoup moins coopératif). En outre, grâce aux violations de l’équipe Red Team, l’équipe Blue Team améliore sa vigilance, ce qui peut être utile en cas de nouvelles intrusions (que ce soit de l’équipe Red Team ou d’un autre adversaire).

Tout au long du processus de détection et de réponse, l’équipe Blue Team collecte des renseignements utiles et gagne une visibilité des conditions réelles de l’environnement qu’elle essaie de défendre. La plupart du temps l’équipe Blue Team s’améliore en analysant et en étudiant les données collectées lors d’une réponse à une attaque lancée par l’équipe Red Team, puis en établissant des indicateurs de menace, comme les indicateurs de compromission.

Tandis que l’équipe Red Team identifie les failles de sécurité, l’équipe Blue Team identifie les failles dans sa propre capacité à détecter les menaces et à y répondre. En outre, puisque les attaques de l’équipe Red Team imitent des attaques réelles, les capacités de l’équipe Blue Team à faire face à des adversaires déterminés et insistants peut être correctement évaluée.

Enfin, les violations de l’équipe Red Team permettent de mesurer à la fois le niveau de préparation de l’entreprise et l’impact de sa stratégie de réponse aux violations.

Les mesures qui sont couramment évaluées par l’équipe Blue Team sont les suivantes :

- La durée estimée de détection (ETTD)

- La durée estimée de récupération (ETTR)

La raison pour laquelle ce sont des estimations est simple : pendant une infraction, quels que soient le moment et l’endroit où elle est détectée, l’équipe Blue Team ne peut pas être certaine à 100 % de l’instant où l’équipe Red Team a réussi sa compromission. De même, l’équipe Blue Team ne peut pas être entièrement certaine qu’elle a réussi à chasser l’équipe Red Team et qu’elle a entièrement récupéré le système compromis. Une absence de preuve de la présence de l’équipe Red Team n’est pas une preuve que l’adversaire a été chassé et que le système a récupéré de la violation. C’est pour cela que la réponse aux incidents de sécurité est si complexe. C’est comme faire un puzzle en assemblant toutes les pièces une par une, sans savoir à quoi ressemblera l’image finale, ni quelles pièces peuvent éventuellement manquer.

En tenant compte de l’ensemble du paysage des menaces et en bénéficiant d’une meilleure visibilité des conditions réelles de l’environnement dans lequel elle évolue, l’équipe Blue Team peut améliorer ses pratiques et ses procédures de réponse aux incidents de sécurité pour apprendre à faire face au mieux à une violation.


#### Red contre Blue 

Contrairement aux tests d’intrusion traditionnels, les violations de l’équipe Red Team s’efforcent de simuler des menaces réelles, y compris ce que l’on pourrait appeler le « brouillard de guerre » [7]. C’est-à-dire que l’équipe Blue Team ne sait pas ce que fait l’équipe Red Team et inversement. Lorsque l’équipe Red Team compromet une ressource, elle peut déclencher une alerte ou un incident de sécurité sans même le savoir.

Mais elle peut aussi déclencher intentionnellement les alertes afin de vérifier et de valider les systèmes de surveillance ou comme tactique de diversion. Au cours d’un exercice, l’équipe Red Team peut observer des stratégies de réponse comme une réinitialisation des mots de passe, la récupération d’une ressource compromise, la suppression de mécanismes de persistance ou même faire l’expérience d’une éviction complète (même si cela est plutôt rare).

Ainsi, à travers ses violations, l’équipe Red Team peut observer l’impact des stratégies de détection et de récupération. Toutefois, les stratégies de détection et de récupération ne peuvent pas être évaluées correctement par l’équipe Red Team ou Blue Team séparément. Le temps moyen de détection (MTTD) et le temps moyen de réponse (MTTR) ne peuvent être calculés qu’à la fin d’un exercice, une fois le débriefing entre les deux équipes terminé. Pour tester efficacement la détection et les réponses, l’équipe Red Team doit :

- Émuler de nombreuses tactiques, techniques et procédures utilisées par les adversaires auxquels une entreprise a (ou aura) affaire.

- Utiliser ces tactiques, techniques et procédure sur toute la chaîne de destruction pour tester et valider les stratégies de détection et de réponse

- Employer la sophistication technique et opérationnelle

- Répéter les attaques pour s’assurer que les stratégies de détection et de réponse sont suffisantes pour combattre des adversaires déterminés

Afin de garantir que l’exercice fait intervenir l’équipe Blue Team, l’équipe Red Team peut déclencher intentionnellement des mécanismes de détection et de réponse. Cela se produit généralement une fois que l’équipe Red Team a accompli tous ses objectifs principaux, sans avoir observé d’activité du côté de l’équipe Blue Team.

Par exemple, l’équipe Red Team peut provoquer intentionnellement une analyse anti-programme malveillant sur une ressource, comme le contrôleur de domaine, à l’aide d’un fichier de test EICAR [8]. La présence d’une alerte de sécurité inattendue, en particulier sur une ressource aussi critique que le contrôleur de domaine devrait activer les procédures de réponse de l’équipe Blue Team. La création d’un incident de sécurité garantit que la violation se déroule de bout en bout, de la compromission à la réponse. La stratégie de réponse aux violations ne peut être entièrement testée que si l’équipe Blue Team intervient.

Normalement, l’équipe Blue Team ne doit (au début) pas savoir si l’alerte incident a été déclenchée par un attaquant réel ou par l’équipe Red Team. Si possible, l’équipe Blue Team doit traiter tous les incidents de sécurité comme des incidents réels, au moins jusqu’à ce que l’équipe Red Team puisse être clairement identifiée Cette approche de simulation de violation réelle est la meilleure méthode pour bâtir des défenses adaptées aux menaces réelles.


#### Post-mortem de la violation de l’équipe Red Team 

À la fin de chaque violation de l’équipe Red Team, les équipes Red Team et Blue Team se rassemblent pour le post-mortem qui évalue la violation et la réponse. C’est à ce stade que les deux équipes partagent les tactiques utilisées et les leçons tirées de l’exercice tout en expliquant tout ce qui a été fait. L’équipe Red Team explique en détail quand et comment elle s’est introduite dans l’environnement, quelles ressources ont été compromises et contrôlées, si l’équipe Blue Team a entièrement ou partiellement réussi à chasser l’équipe Red Team et si la récupération après la violation de sécurité est complète.

En échange, l’équipe Blue Team fournit toutes les informations sur quand et comment l’équipe Red Team a été détectée (si elle a été détectée), quelles ressources ont été identifiées comme compromises, quels mécanismes de persistance de l’équipe Red Team ont été détectés et quelles stratégies de réponse ont été mises en place afin de chasser l’équipe Red Team et récupérer le système après la violation. Cet échange est ce qui rend le Red Teaming unique : l’équipe Red Team est l’un des seuls adversaires qui peut et qui va fournir des détails sur sa tentative de violation, ainsi que des commentaires sur la réussite (ou non) de la stratégie de détection et de réponse. La comparaison des notes des deux équipes est capitale pour s’assurer que toutes les failles de détection et de réponse ont été identifiées.

Ce n’est que pendant l’analyse post-mortem, lorsque le débriefing complet a lieu, que le temps moyen de détection (MTTD) et le temps moyen de récupération (MTTR) peuvent être calculés. Par exemple, en comparant le temps moyen de compromission relevé par l’équipe Red Team et le temps estimé de détection de l’équipe Blue Team, on peut calculer le temps moyen de détection. Ces mesures de temps de détection et de réponse, pendant et après l’incident, sont utiles pour identifier les risques liés aux failles dans les stratégies de détection et de récupération mises en place dans le cadre de l’exercice.

Pendant le reste de l’analyse post-mortem, les deux équipes collaborent pour identifier les vulnérabilités et les investissements qui pourraient améliorer la protection, la détection et la stratégie de réponse. Après chaque violation, les équipes Red Team et Blue Team identifient les investissements en matière de sécurité nécessaires à renforcer la protection contre les attaques futures, à ralentir les adversaires et à accélérer la détection et la récupération. En d’autres termes, elles cherchent à augmenter le temps moyen de compromission et à diminuer les temps moyens de détection et de récupération.

À la fin de l’analyse post-mortem, les deux équipes doivent fournir un rapport écrit. Ces rapports incluent la chronologie de la violation du point de vue des deux équipes, un résumé de l’impact de l’exercice sur l’entreprise, ainsi qu’une liste détaillée des vulnérabilités, des observations et des investissements nécessaires à améliorer la détection des violations et la stratégie de réponse.

### Principes de sécurité 

L’avantage principal du Red Teaming est le regroupement d’experts de la sécurité et de pirates éthiques. Comme dans n’importe quelle autre profession, les ingénieurs et le personnel d’exploitation ont des domaines de spécialisation, mais ce n’est pas forcément en attaques et en intrusions. Chez Microsoft, les ingénieurs et les opérateurs connaissent bien SDL/OSA et les CEC (Common Engineering Criteria), mais l’équipe Red Team teste la sécurité en dehors des limites de l’environnement de production, auquel il serait trop difficile de s’attaquer.

En investissant dans des équipes Red Team dont l’objectif unique est de comprendre, communiquer et exploiter les dernières menaces pendant les attaques de site, Microsoft s’efforce de tester et d’améliorer en continu ses capacités réactives et de suivre des principes de sécurité clés :

- S’opposer aux stratégies de sécurité fixes en cas de **scénarios d’attaque statiques** ou éviter de supposer que l’adversaire n’attaquera que depuis **une position fixe unique**

- Utiliser des **couches complémentaires de contrôles de sécurité** ; l’effet est cumulatif et les défenses sont améliorées

- Le **nombre et la répartition** des contrôles de sécurité sont plus importants que l’efficacité individuelle de chaque contrôle

- Chercher à **retarder une attaque et à réagir** plutôt qu’à **empêcher** une attaque

## Résumé 

Les entreprises du secteur sont confrontées à une dure réalité : elles ont peut-être vécu dans un état constant de compromission. Le pire, c’est qu’un grand nombre d’entreprises ignorent toujours qu’elles ont été attaquées. Les menaces actuelles nécessitent un effort dans la réduction de l’exposition aux attaques, y compris venant de l’intérieur. Dans l’immédiat, le plus important pour les entreprises est de diminuer les temps moyens de détection et de récupération.

Ce livre blanc met en évidence le fait que les entreprises doivent adopter des stratégies plus flexibles de traitement des failles de sécurité. Grâce à sa stratégie « Assume Breach », Microsoft continue à investir dans la sécurité, de manière plus approfondie. Toutes les entreprises peuvent bénéficier de l’adoption d’une stratégie de sécurité similaire pour combattre les menaces émergentes, en constante évolution.

Microsoft développe ses capacités de gestion des incidents de sécurité par la planification, à l’avance, de sa défense en cas de compromission et en simulant des attaques et des intrusions par le biais d’exercices de réflexion et d’une équipe Red Team active. Le Red Teaming concrétise les scénarios théoriques en exposant des failles en matière de sécurité, en exploitant les vulnérabilités existantes et en fournissant la preuve concrète de la nécessité d’une approche de type « Assume Breach ».

Le Blue Teaming permet de tester les capacités de l’entreprise à détecter les attaques et à réagir. En effectuant des exercices Red Team contre Blue Team, les services de sécurité peuvent se concentrer sur les vecteurs d’attaque clés, développer des contre-mesures et faire évoluer des mécanismes de réponse critiques nécessaires à contrer des adversaires déterminés et insistants.

Le Red Teaming est devenu l’une des phases essentielles du développement et de la sécurisation de l’infrastructure, de la plate-forme et des services Microsoft. Les équipes Red Team de Microsoft Azure et d’Office 365 jouent le rôle d’adversaires sophistiqués et permettent à Microsoft de valider et d’améliorer la sécurité, de renforcer les défenses et d’améliorer l’efficacité de ses programmes de sécurité du cloud d’entreprise.

En attaquant régulièrement des sites actifs, l’équipe Red Team permet à l’entreprise de mettre en pratique ses stratégies de réponse aux incidents et de mesurer son niveau de préparation aux véritables attaques ainsi que l’impact de celles-ci. Les clients de Microsoft peuvent ainsi être sûrs que l’entreprise améliore en permanence ses stratégies de protection, de détection et de réponse afin de fournir des services de cloud plus sécurisés.

## Références et informations supplémentaires 

Les ressources suivantes sont disponibles pour fournir des informations générales sur les services Microsoft Azure et Office 365, ainsi que des éléments spécifiques référencés dans le texte principal :

- [Microsoft Azure Home](http://azure.microsoft.com) – Informations générales et liens sur Microsoft Azure

- [Microsoft Office Home](http://products.office.com) – Informations générales et liens sur Microsoft Office

- [Centre de gestion de la confidentialité Microsoft Azure](https://www.microsoft.com/TrustCenter/CloudServices/Azure)

- [Centre de gestion de la confidentialité Microsoft Office 365](https://www.microsoft.com/TrustCenter/CloudServices/Office-365)

- [Centre de réponse aux problèmes de sécurité Microsoft](http://www.microsoft.com/security/msrc/default.aspx)

- Microsoft Azure ou Office 365, peuvent être signalés] o [**http://www.microsoft.com/security/msrc/default.aspx*](http://www.microsoft.com/security/msrc/default.aspx)[ ] (http://www.microsoft.com/security/msrc/default.aspx) ou par e-mail à **secure@microsoft.com*

- [Interview with Mark Russinovich on Microsoft Azure & Security - Update on Penetration Testing](http://blogs.technet.com/b/matthewms/archive/2013/07/02/an-interview-with-mark-russinovich-on-windows-azure-amp-security-update-on-penetration-testing.aspx) (Entretien avec Mark Russinovich sur Microsoft Azure et la sécurité - Mise à jour sur les tests d’intrusion)

- [Red vs. Blue - Internal security penetration testing of Microsoft Azure and O365](http://azure.microsoft.com/documentation/videos/red-vs-blue-internal-security-penetration-testing-of-microsoft-azure/) (Red contre Blue - Tests d’intrusion de sécurité interne de Microsoft Azure et O365)



## ANNEXE 

### Différence entre le Red Teaming et les tests d’intrusion 

Les techniques de test d’intrusion standard utilisent des outils courants pour lancer leurs attaques, mais exploitent rarement les capacités complètes d’un adversaire compétent.

Les tests d’intrusion traditionnels ne durent qu’un temps défini, puis se terminent sans davantage impacter l’environnement et les mécanismes de réponse aux incidents, comme le ferait un véritable adversaire. Le Red Teaming continue au-delà de la phase de test. Le Red Teaming utilise des méthodes souvent ignorées par les autres méthodologies de test d’intrusion (résumées dans le Tableau 1 ci-dessous) :

**TEST D’INTRUSION TRADITIONNEL RED TEAMING**

| | Test d’intrusion traditionnel | Red Teaming |
|---------------------------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| **Persistance** | L’attaque s’arrête lorsque l’environnement est compromis | Les testeurs établissent un mécanisme de persistance pour conserver l’accès et évaluer la récupération après violation |
| **Outils** | Les testeurs utilisent uniquement les techniques disponibles à ce moment-là, puis attendent jusqu’au prochain cycle de tests | Les testeurs recherchent constamment de nouvelles techniques et vulnérabilités et lancent une autre attaque avec de nouveaux outils dès que l’intrusion est détectée |
| **Activité après la violation** | Les testeurs sont ce qu’on appelle des « white hats », c’est-à-dire qu’ils essaient d’entrer dans le système et s’ils y arrivent, ils ne font rien d’autre | Les testeurs exploitent la violation pour exfiltrer et exploiter des données critiques pour lancer d’autres attaques |
| **Fuite et évasion** | Les tests statiques ne tentent pas d’échapper à la détection et les tests s’arrêtent si la violation est détectée | Tests dynamiques qui tentent d’échapper à la détection |
| **Adversaires compétents** | L’accent est mis sur les systèmes de sécurité connus de l’entreprise (puisque les testeurs ont une parfaite connaissance des systèmes informatiques en place) pour vérifier s’ils sont déployés correctement, et seules des attaques connues sont utilisées | Les testeurs cherchent à savoir ce qui a été *et* n’a pas été déployé pour trouver des vulnérabilités. Ils utilisent des connaissances internes et des outils personnalisés pour attaquer les systèmes, et développent également de nouveaux types d’attaques inconnues et personnalisées |
| **Site actif** | Les attaques ne sont presque jamais dirigées sur des systèmes de production actifs ; elles sont lancées pour tester des systèmes avant déploiement | Les attaques sont dirigées sur des systèmes de production actifs et touchent toutes les couches |
| **Motivation** | Les testeurs font des hypothèses concernant l’environnement, sans tester ce qu’ils n’ont pas reçu l’ordre de tester | Les testeurs ne font aucune hypothèse et tentent de compromettre tout le système en changeant d’orientation et de tactique si besoin |
| **Résultats** | Les testeurs montrent les tests qui ont été effectués et les résultats (réussite/échec) | Les testeurs mesurent et évaluent avec précision les MTTC, MTTP, MTTD et MTTR. Ils peuvent démontrer quand et comment la violation de l’environnement a eu lieu, quelles ressources ont été compromises et si la détection et la stratégie de réponse ont réussi. |


**Tableau 1 : résume les différences entre les tests d’intrusion traditionnels et le Red Teaming**


##Pourquoi attaquer l’environnement de production ? 

Les tests et la pré-production ne correspondent jamais vraiment à la production. Les modifications apportées pendant le déploiement, les opérations de service en cours et les différents environnements de production des utilisateurs contribuent tous à cet écart. Cet écart existe pour une bonne raison : la production est confrontée à des besoins différents en ce qui concerne la charge prévue, les temps d’arrêt acceptables, ainsi que les exigences réglementaires, contractuelles et en matière de sécurité et de conformité.

Le Red Teaming cible de véritables systèmes en s’introduisant dans des sites actifs plutôt que dans des environnements de développement/test aux charges de travail hypothétiques et aux composants bien réels mais contenant des données fictives. C’est principalement en cela que le Red Teaming diffère des stratégies de sécurité plus traditionnelles : de vrais serveurs, des clients réels et des données réelles. Les tests d’intrusion sur sites de production actifs fournissent une mesure beaucoup plus fiable et plus précise de l’impact d’une violation sur le client et l’entreprise.

L’intrusion de sites de production actifs offre aussi d’autres avantages. Elle met à l’épreuve les capacités défensives de détection et de réponse de l’entreprise. Les attaques réelles permettent de tester la fiabilité de la protection déployée sur l’environnement de production, ainsi que les stratégies de détection et de réponse. Elle force l’entreprise à accepter l’idée que la production subit une attaque et peut déjà être compromise. Elle permet aussi de tester en continu les stratégies de réponse aux incidents de sécurité afin de lutter contre les attaques ciblées lancées par des adversaires insistants.

##Fonctionnement d’une équipe Red Team 

Adopter une approche « Assume Breach » n’implique pas forcément d’investir dans votre propre équipe Red Team. De nombreux avantages peuvent être réalisés en modifiant simplement votre approche en matière d’infrastructure, de test de sécurité des applications et de déploiement. Par exemple, pour concevoir une stratégie de protection des informations et des ressources critiques efficace, il est important de comprendre qu’une violation peut se poursuivre bien après la première phase de compromission.

Toutefois, le Red Teaming reste un moyen efficace de tester la fiabilité de vos processus et mesures de sécurité, pour sensibiliser votre personnel aux menaces existantes et informer les ingénieurs, opérateurs, services de sécurité et responsables des menaces émergentes. Pour déterminer les priorités en matière d’investissement, procédez comme suit :

-   **Les approches de prévention et « Assume Breach »** ne s’excluent pas. La prévention implique d’implémenter des stratégies de défense propres à empêcher les attaques critiques. L’approche « Assume Breach » va bien au-delà de ces défenses et prend en compte la détection et les réponses avant, pendant et après une intrusion.

-   **Identifier les aspects critiques** et hiérarchiser les investissements à l’aide de l’approche « Assume Breach ». Quelles sont les données les plus critiques pour l’entreprise ? Où sont stockées les données ? Quelles sont les ressources les plus critiques pour le fonctionnement de l’entreprise ?

-   **Identifier les exercices pertinents** et hiérarchiser les exercices de sécurité en fonction des tendances et des principales tactiques utilisées par les pirates.

-   **Obtenir l’approbation**, en particulier au niveau exécutif, et une validation juridique. En outre, les différents groupes de travail/divisions de votre entreprise doivent connaître et accepter le travail que vous faites. En effet, lancer des attaques contre les environnements d’exploitation qui n’ont jamais été attaqués auparavant pourrait entraîner des problèmes de satisfaction client et des interruptions de service inattendues.


Une fois que vous avez créé un plan de Red Teaming, vous devez protéger vos opérations :

-   **Personnel** – Assurez-vous d’avoir des ressources expertes et adéquates dans l’équipe Red Team comme dans l’équipe Blue Team. L’équipe Blue Team doit traiter tous les incidents de sécurité, y compris les violations de l’équipe Red Team comme des incidents réels. Pour former votre équipe Red Team, envisagez aussi bien des employés à plein temps que des ressources externes, sans oublier de prendre en compte la sécurité des propriétés intellectuelles éventuellement compromises durant la violation. Si vous pensez engager un fournisseur tiers, *vérifiez ses capacités*, sa fiabilité et sa réputation.

-   **Protection** — Les membres de vos équipes doivent non seulement posséder les compétences techniques nécessaires, mais aussi être dignes de confiance. Choisissez avec soin les membres de votre personnel et n’hésitez pas à vérifier leurs antécédents.

-   **Formation** – Si vous embauchez des employés à plein temps, ils doivent recevoir une formation en matière de standards d’éthique, de pratiques de sécurité, de confidentialité et de conformité, et se soumettre à toutes les exigences spécifiques à votre entreprise ou à l’industrie (les agences externes doivent être certifiées pour garantir des formations régulières).

-   **Audit** – Il est impératif que les activités de l’équipe Red Team soient correctement surveillées, consignées et auditées avant et pendant les exercices. Ces informations peuvent servir à l’équipe Red Team à montrer patte blanche au cas où elle serait accusée à tort de dommages ne résultant pas de l’exercice. Ainsi, vous êtes assuré que l’équipe Red Team suit un code de conduite strict et respecte les règles d’engagement. En outre, les enregistrements de journal et d’audit servent souvent à calculer les MTTC, MTTD et MTTR et à fournir un rapport détaillé après l’exercice.

-   **Sécurité opérationnelle** – Il est essentiel que les membres de l’équipe Red Team assurent une sécurité opérationnelle stricte en ce qui concerne ses techniques et procédures, ses outils, ses résultats en matière de sécurité, ses rapports, ses attaques, ses stratégies de fuite et autres ressources. Si un intrus parvenait à accéder aux ressources d’une équipe Red Team il serait en mesure de reproduire rapidement les attaques contre les vulnérabilités connues, avec un impact dévastateur.

##Informations supplémentaires

Les tests d’intrusion à l’échelle de l’entreprise nécessitent la participation de plusieurs parties. Étant donné que ce document traite de procédures, il est destiné aux personnes chargées de l’évaluation les risques en matière de sécurité des environnements, des systèmes et des processus. Notez que les détails fournis dans le présent document sont un aperçu de la manière dont les équipes de sécurité de Microsoft Azure et d’Office 365 ont mené leurs tests Red Teaming et leurs tests d’intrusion, et non pas des instructions sur la façon dont les clients peuvent attaquer l’infrastructure cloud, les plates-formes ou les services de Microsoft.

Microsoft effectue régulièrement des tests d’infiltration pour améliorer les processus et les contrôles de sécurité dans le cloud, et nous sommes conscients que les évaluations de sécurité sont également une partie importante des activités de développement et de déploiement d’applications de nos clients. C’est pour cela que nous avons élaboré une stratégie permettant à nos clients d’effectuer des tests d’intrusion autorisés sur leurs applications, comme celles hébergées dans Microsoft Azure.

Étant donné que ces tests ne peuvent pas être différenciés d’une attaque réelle, les clients doivent impérativement obtenir l’accord préalable du Support client de Microsoft Azure avant d’effectuer un test d’intrusion. Ces tests doivent respecter les termes et conditions de Microsoft. Les demandes de tests d’intrusion doivent être soumises au moins sept jours à l’avance. Pour plus d’informations, voir le [Centre de gestion de la confidentialité de Microsoft]https://www.microsoft.com/TrustCenter/CloudServices/Azure).

## Références

[1] Accès et élévation de privilège de type « Juste-à-temps » (JIT) : élévation de privilège accordée en fonction des besoins et uniquement au moment où ces besoins se font sentir. Cette approche se traduit souvent par la nécessiter d’obtenir une ou plusieurs approbations avant que l’accès ne soit accordé.

[2] Administration de type « Juste assez » (JEA) : une approche conçue pour aider à protéger les systèmes, en permettant à des utilisateurs spécifiques d’effectuer des tâches administratives sans leur donner de droits d’administrateur, puis en auditant toutes les actions effectuées par ces utilisateurs. Pour plus d’informations, consultez l’article [Just Enough Administration dans la Galerie TechNet](https://gallery.technet.microsoft.com/Just-Enough-Administration-6b5ad370).

[3] Dans le contexte d’une faille de sécurité, MTTD fait référence au temps nécessaire pour détecter une violation.

[4] MTTR fait référence au temps nécessaire pour récupérer le système une fois l’intrusion détectée.

[5] Le personnel de Microsoft Azure et d’Office 365 sépare les équipes Red Team et Blue Team à plein temps.

[6] Lire le document [« Intelligence-Driven Computer Network Defense Informed by Analysis of Adversary Campaigns and Intrusion Kill Chains »](http://www.lockheedmartin.com/content/dam/lockheed/data/corporate/documents/LM-White-Paper-Intel-Driven-Defense.pdf) (La défense des réseaux informatiques basée sur l’information informée par l’analyse des campagnes et chaînes de destruction des pirates) proposé par Lockheed Martin pour plus d’informations.

[7] Brouillard de guerre : terme utilisé pour décrire l’incertitude des participants concernant leurs propres capacités, les capacités de l’adversaire et l’intention de l’adversaire.

[8] Fichier test EICAR : ce fichier a été développé par l’EICAR (European Institute for Computer Antivirus Research) et la CARO (Computer Antivirus Research Organization) et permet de tester la stratégie de réponse des logiciels anti-programme malveillant. Contrairement aux véritables logiciels malveillants, qui pourraient causer des dommages réels, ce fichier de test permet de tester les logiciels anti-programme malveillant sans utiliser de véritable virus.


*(c) 2014 Microsoft Corporation. Tous droits réservés. Ce document est fourni « en l’état ». Les informations et les points de vue exprimés dans ce document, y compris les URL et autres références à des sites web, peuvent être modifiés sans préavis. Vous assumez seul les risques liés à leur utilisation. Certains exemples sont fournis à titre indicatif uniquement et sont fictifs. Aucune association réelle n’est prévue ni ne doit être déduite. Ce document ne vous accorde aucun droit légal à la propriété intellectuelle pour un produit Microsoft. Vous pouvez copier et utiliser ce document pour un usage interne, à titre de référence.*

<!---HONumber=AcomDC_0218_2016-->