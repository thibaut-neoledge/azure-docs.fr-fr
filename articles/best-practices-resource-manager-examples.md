<properties
	pageTitle="Exemples contextuels des meilleures pratiques d’implémentation des modèles"
	description="Présente des exemples de modèles Azure Resource Manager qui illustrent les meilleures pratiques."
	services="azure-resource-manager"
	documentationCenter=""
	authors="mmercuri"
	manager="georgem"
	editor="tysonn"/>

<tags
	ms.service="azure-resource-manager"
	ms.workload="multiple"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/17/2015"
	ms.author="mmercuri"/>

# Exemples contextuels des meilleures pratiques d’implémentation des modèles

Cette rubrique fournit 7 exemples en contexte de mise en œuvre de vos modèles Azure Resource Manager. Pour avoir une vue d’ensemble des principes décrits dans ces exemples, consultez la section [Meilleures pratiques pour concevoir les modèles Azure Resource Manager](best-practices-resource-manager-design-templates.md).

Cette rubrique fait partie d’un livre blanc plus volumineux. Pour lire tout le document, téléchargez [World Class ARM Templates Considerations and Proven Practices](http://download.microsoft.com/download/8/E/1/8E1DBEFA-CECE-4DC9-A813-93520A5D7CFE/World Class ARM Templates - Considerations and Proven Practices.pdf).

## Transformation d’un modèle de fonctionnalité étendue en modèle de solution étendue de bout en bout

Le modèle servant à développer un modèle de fonctionnalité étendue a partagé au préalable. La question consiste à savoir s’il y a des éléments divers à prendre en compte au moment d’utiliser le modèle de fonctionnalité étendue seul ou dans le cadre d’un modèle de solution étendue de bout en bout.

Par exemple, dans le cas d’un modèle axé sur une technologie ayant déployé SQL Server en tant que fonctionnalité, quels seraient les éléments à prendre en compte, le cas échéant, selon qu’on l’utilise seul ou dans le cadre d’un modèle de solution étendue de bout en bout plus large faisant appel à SQL Server pour prendre en charge une application web.

Pour évaluer ce scénario, il convient de prendre en compte le nombre de ressources susceptibles d’intervenir. Pour une implémentation robuste, votre modèle de fonctionnalité étendue ne doit pas être un simple compte de stockage dans une machine virtuelle unique avec une installation de SQL Server. Un modèle de fonctionnalité étendue solide doit déployer plusieurs machines virtuelles avec SQL Server, pour une grande disponibilité. Pour certaines fonctionnalités, telles qu’Analysis Services, votre topologie devra également comprendre Active Directory.

Deux points essentiels sont à prendre en compte dans ce scénario : le cycle de vie relatif à l’utilisation de SQL Server et les éléments RBAC à lui appliquer. Plus particulièrement, le SQL Server sera-t-il mis à jour et supprimé avec le reste de la solution ou son cycle de vie changera-t-il à cause de la solution ou de parties de la solution. Si le cycle de vie doit varier varie, vous devez envisager de le placer dans un autre groupe de ressources.

Il faut également se demander comment appliquer les RBAC à votre modèle de solution étendue de fonctionnalité de SQL Server. En fonction de la manière les RBAC seront appliqués au sein de votre topologie, vous pouvez opter pour différents groupes de ressources prévus respectant ces caractéristiques. Vous pouvez appliquer les RBAC au niveau des ressources, mais vu le nombre de ressources nécessaires au modèle de solution étendue de fonctionnalité de SQL Server, mieux vaut envisager l’application d’un groupe de ressources avec RBAC distinct.

On peut aussi envisager d’évaluer le modèle de solution avec étendue de fonctionnalité de SQL Server pour déterminer s’il crée certaines ressources lui-même ou s’il suit le principe « Apportez votre propre ressource » (BYOR). Dans un modèle « Apportez votre propre ressource » (BYOR), le modèle de fonctionnalité étendue permet à votre modèle de réutiliser les ressources existantes, l’exemple type étant un compte de stockage, un réseau virtuel ou un groupe à haute disponibilité. Si votre modèle de fonctionnalité étendue n’utilise pas l’approche BYOR, vous pouvez le modifier et obtenir des modèles de ressources facultatifs en utilisant l’approche décrite plus haut. Dans ce cas, votre modèle de solution étendue de bout disposerait d’un modèle de ressources partagées contenant ces ressources communes, et le modèle de fonctionnalité étendue serait étendu afin de prendre en charge ces ressources optionnelles. Vous obtenez ainsi un meilleur modèle de solution de fonctionnalité étendue car il peut être alors utilisé indépendamment ou comme partie d’un ensemble.

Au moment de décider si le compte de stockage doit être transféré depuis le modèle de solution étendue de bout en bout, le RBAC doit être réévalué. Plus précisément, allez-vous vous assurer que le RBAC doit être appliqué à cette ressource spécifique ? Si c’est le cas, il faut l’appliquer à la ressource au moment du transfert, un niveau de confiance doit être mis en place non seulement dans le bloc de solution, mais également pour tout utilisateur souhaitant éventuellement l’appliquer au modèle de fonctionnalité étendue lorsqu’elle est utilisée de façon indépendante. Si le RBAC est essentiel, vous devez vous demander si vous souhaitez les transformer en un modèle facultatif au sein du modèle de solution de fonctionnalité étendue ou opter pour sa création avec le RBAC requis dans le modèle de solution de fonctionnalité étendue.

Si vous décidez de les placer dans différents groupes de ressources, vous pouvez également utiliser des liens vers les ressources pour définir les relations entre les ressources, y compris lorsque les ressources s’étendent sur les groupes de ressources.

## Création d’un modèle étendu de solution de bout en bout avec plusieurs modèles de fonctionnalité étendue

Il s’agit principalement d’un surensemble de l’exemple précédent. Dans ce scénario, une entreprise possède plusieurs modèles de solutions de fonctionnalité étendue pour un ensemble de technologies de données telles que Kafka, Apache Hadoop, Apache Spark et Apache Storm qu’elle souhaite regrouper dans un bloc de solution unique. La composition obtenue utilisera ces modèles de solution de fonctionnalité étendue, ainsi qu’un stockage partagé et un réseau virtuel avec des affectations de sous-réseau spécifiques.

Outre les modèles de fonctionnalité étendue requis, la solution nécessitera des ressources supplémentaires, même s’il suffit de relier quelques scripts aux modèles de fonctionnalité étendue et de les configurer.

Dans ce cas, on suppose qu’il existe un réseau virtuel partagé et un compte de stockage partagé. Pour ce faire, vous devez les ajouter à un modèle de ressources partagées dans votre modèle étendu de solution de bout en bout et vous assurer qu’une approche de type « Apportez votre propre ressource » est prise en charge dans les modèles de fonctionnalité étendue. Si ce n’est pas le cas, vous pouvez modifier vos modèles de fonctionnalité étendue, comme indiqué dans l’exemple précédent.

Concernant les ressources supplémentaires que vous souhaitez ajouter, vous devez suivre un surensemble du modèle utilisé pour créer un modèle étendu de fonctionnalités individuelles. Dans ce cas, vous devez ajouter un modèle de ressources partagées, des modèles de ressources optionnels, des modèles de nœud membre et la configuration de l’état souhaitée (scripts, Chef, Puppet, Powershell DSC) correspondant aux nouvelles ressources. S’il existe des dépendances, vous devez les optimiser pour utiliser des références implicites par rapport aux dependsOn lorsque cela est possible et ce, afin d’éliminer le risque de dépendances isolées susceptibles d’avoir un impact sur le parallélisme (et la vitesse) de votre déploiement. Vous devez également prendre en compte le cycle de vie de ces ressources, les considérations RBAC et les dépendances pour déterminer s’ils doivent être placés dans différents groupes de ressources.

Lors de l’ajout de ressources partagées telles que le compte de stockage partagé, vous devez également évaluer si un verrou de ressources est requis, car il permet d’éviter les suppressions accidentelles.

Lorsque vous ajoutez de nouvelles ressources, vous devez également voir si toutes les ressources ajoutées au modèle étendu de solution de bout en bout peuvent être isolées en tant que modèles étendus de fonctionnalités. Dans ce cas, il faut réfléchir à deux fois avant de poursuivre la de décomposition et bénéficier des avantages de réutilisation et de test.

Lors de l’intégration dans les blocs de votre solution, vous devez, comme indiqué dans l’exemple précédent, déterminer si le cycle de vie des modèles de solution étendus de fonctionnalité individuelle est différent de la solution la plus large et si toutes les exigences en matière de RBAC nécessitent leur répartition en groupes de ressources distincts.

Enfin, vous devez déterminer si vous souhaitez définir et interroger des liens entre ressources. Si tel est le cas, en utilisant des liens vers les ressources vous pourrez réaliser l’opération grâce au modèle de solution étendu de bout en bout, même si plusieurs groupes de ressources sont concernés.

## Création d’un modèle étendu de solution de bout en bout avec un modèle on/off partiel

Ce scénario est une variante du précédent. Dans ce cas, le client extrait des données d’un système sur site sur à intervalles réguliers tout au long de la journée. Ils disposent d’un pipeline de données pour traiter les données entrantes et d’une banque de données relationnelles dans laquelle les données sont toujours disponibles pour les requêtes. Comme le cloud est un modèle de paiement à l’utilisation, le client souhaite disposer du pipeline de données opérationnelles uniquement durant ces périodes lorsque les données sont présentées au traitement.

Leur pipeline de données contient un serveur SQL Server, qui reçoit les données traitées et les met à disposition pour les requêtes. Le client a indiqué qu’il souhaitait que le serveur SQL reste disponible quand il veut activer et désactiver la réception et le traitement du pipeline suivant un planning fixe.

Dans ce scénario, il apparaît clairement qu’il existe des différences de cycle de vie et qu’il convient d’examiner certains points que le client n’a pas soulevés, mais qui doivent être examinés.

Comme indiqué, le déploiement de SQL Server reste actif alors que les autres ressources sont créées et supprimées. Elles sont déployées ensemble au départ, mais par la suite, les autres membres du modèle sont détruits et créés sur un autre cycle de vie. Ceux-ci peuvent être isolés dans différents groupes de ressources ou laissés dans le même groupe de ressources avec des verrous de ressources appliqués aux ressources SQL Server. Sachant SQL Server est avant tout, comme décrit dans les exemples précédents, un ensemble plus important de ressources, il est recommandé de le diviser en groupes de ressources.

Il faut également noter que le client a demandé que le reste du pipeline de données puisse être activé et désactivé selon un calendrier précis, il ne peut pas envisager le comportement incohérent de systèmes de création de rapports. La livraison planifiée de données provenant de tiers n’est pas toujours précise. La connectivité peut être indisponible pendant une période donnée, les serveurs qui pointent sur l’ordinateur local ou sur le cloud peuvent dériver, les changements d’heure peuvent ne pas s’effectuer comme prévu, etc. Il convient aussi d’évaluer si votre mécanisme de réception doit être utilisé dans un modèle actif/inactif et, le cas échéant, si son cycle de vie est supérieur à celui des composants de traitement.

Si vous réutilisez un service géré comme Azure Data Factory ou Event Hub, celui-ci pose moins de problèmes dans la mesure où ses modèles de fonctionnement et l’approche de facturation qui leur est associée les préparent à absorber vos données et à les placer dans le stockage. Si vous réutilisez une autre technologie, par exemple Kafka, déployée sur une machine virtuelle, vous pouvez examiner son cycle de vie et déterminer comment procéder, ainsi que les comptes de stockage associés requis pour l’ingestion. Ainsi, les ressources de l’ingestion et de traitement peuvent être placées dans un groupe de ressources différent en fonction de leur cycle de vie.

## Prise en charge d’environnements distincts dans un abonnement

Pour fournir efficacement des services, de nombreuses entreprises possèdent un ensemble d’échelle, une facturation d’isolation, isolation de responsabilité et des besoins d’isolation géographique doivent être satisfaits. Lorsque vous concevez des services Azure, ils doivent toujours utiliser des partitions d’abonnement dans leur approche pour répondre à ces besoins.

Le Gestionnaire de ressources assouplit les contraintes sur le nombre de ressources d’un type donné qui peuvent être déployées dans un abonnement et présente également les groupes de ressources, de RBAC et d’audit. La combinaison de ces éléments peut permettre aux entreprises d’utiliser des groupes de ressources pour le partitionnement, ainsi, elles sont à même de répondre à leurs besoins et le cas échéant, de réduire la quantité de répartition d’abonnement à faire.

Cette section passe en revue les exigences liées à ces types d’environnements et donne des conseils sur la façon de fournir des environnements y répondant avec les ARM.

### Considérations relatives à l’isolation

Cette section concerne les pilotes de clients courants servant à l’isolation d’environnements, de facturation et géographique, de façon plus détaillée.

#### Isolation de l’environnement

Les propriétaires de services souhaitent isoler leurs différents environnements. Le fait d’isoler chaque environnement permet aux équipes d’avoir un contrôle plus précis des personnes qui peuvent y accéder. Alors que les environnements de développement peuvent être plus ouverts en termes d’accès, sachant que la portée de l’environnement se rapproche de la production, le nombre d’utilisateurs, qu’il s’agisse d’humains ou de comptes système utilisés pour l’automatisation – est réduit pour faciliter la conformité et minimiser le risque global.

#### Isolation de la facturation – développement et exécution d’un service

Pour refléter précisément le coût des produits vendus (CPV) et les frais d’exploitation (OpEx), les responsables d’entreprise veulent être en mesure de distinguer le coût de recherche et de création du service et celui du fonctionnement des services.

Un surensemble de l’isolation d’environnement comme mentionné précédemment, avec pour objectif de consolider le développement et les tests individuels et/ou agrégés de facturation pour la première tandis que la production reste indépendante de ce dernier.

#### Isolation de facturation – Plus de transparence et de responsabilité pour les coûts de consommation de service

L’isolation de facturation est également utilisée pour gagner en transparence en matière de coûts liés à la consommation de la plateforme par des équipes spécifiques et la présentation des niveaux appropriés de responsabilité.

Le cloud est souple et autorise un modèle de paiement à l’utilisation ; cette solution est moins familière pour certains développeurs issus d’un modèle ne faisant pas appel au cloud, où le matériel est acquis et détenu. Dans le modèle non cloud, il y avait des limites physiques en termes de nombre d’« ordinateurs » susceptibles d’être mis en marche et il y avait peu d’initiatives visant à baisser ou à éteindre les ressources non utilisées. De nombreux cas, l’acquisition de ce matériel dédié n’était pas effectuée par les développeurs qui l’utilisaient.

En isolant les abonnements et en affectant la responsabilité de ces derniers à des équipes spécifiques, les propriétaires de services trouvaient ce type de partitionnement d’abonnement avantageux dans la conduite et l’application des comportements souhaités.

#### Isolation géographique pilotée – déploiements spécifiques régis par des lois géographiques spécifiques

Dans certains contextes, certaines exigences nécessitant des services destinés à une géographie spécifique doivent tenir compte du mode de déploiement pour répondre aux exigences de conformité.

Un service peut être de type global, mais les déploiements résidant dans ou fournissant un service dans des zones géographiques bien délimitées peuvent être régis par des impératifs du personnel d’exploitation. Plus précisément, ce sont des personnes citoyennes d’un pays en particulier ou d’un ensemble de pays et/ou qui transmettent certains processus de filtrage en arrière-plan qui exploitent ces services.

L’isolation géographique fournit également des avantages en termes de nouvelles plateformes de services et fonctionnalités. Certaines zones géographiques, comme en Chine, peuvent juste disposer d’un sous-ensemble de services de plateforme disponible et/ou ont retardé le déploiement de plateforme de services délégués.

L’isolation géographique donne aux équipes la possibilité de faire évoluer leurs services pour tirer parti des fonctionnalités et des services de plateforme ou améliorées à l’endroit où ils sont disponibles.

### Considérations relatives à la conformité

Les services peuvent être fournis sur plusieurs zones géographiques et plusieurs marchés verticaux. Ils portent souvent sur des données ou contenus sensibles dans leurs applications et il existe des réglementations associées destinées à les protéger et les auditer.

#### Séparation des rôles et des droits

La séparation des rôles et des droits est essentielle pour les services en interne pour respecter la conformité avec les stratégies internes. De nombreux services commerciaux doivent également être conformes aux directives réglementaires gouvernementales et industrielles. Les départements doivent limiter l’accès aux services et à leurs ressources sous-jacentes aux rôles autorisés dans des circonstances spécifiques. De nombreux services ont créé une structure pour fournir deux fonctionnalités, la RBAC et l’audit.

#### Résolution des problèmes de contrôle d’accès basé sur les rôles (RBAC)

Dans les scénarios de conformité, il est important de limiter l’accès à certaines ressources.

Par exemple, lorsque vous examinez les données sensibles sur plusieurs scénarios où la discrétion s’impose, les informations médicales, les données financières, les données fiscales, etc., il est essentiel de limiter le nombre de personnes qui peuvent accéder, visualiser ou manipuler les données à celles qui nécessitent ces droits pour des besoins professionnels.

Le RBAC permet de désigner un individu distinct, un système ou un groupe avec accès à des ressources spécifiques sous certaines conditions.

#### Audit

Outre l’accès contraint fourni par le RBAC, les entreprises doivent également auditer l’accès aux ressources et l’interaction avec ces dernières.

### Mettre en œuvre des ressources avec Azure Resource Manager

Auparavant, les entreprises utilisaient le partitionnement d’abonnement pour atteindre ces objectifs. Bien que ce soit possible, ce n’était pas l’idéal. Comme la création d’un abonnement est effectivement une activité commerciale, le service de gestion API n’avait pas présenté de mécanisme pour créer ou supprimer automatiquement les nouveaux abonnements qui autrefois étaient créés manuellement. Le nombre d’abonnements qui en résultait pouvait augmenter considérablement (pour des services très volumineux, par exemple, les services commerciaux de Microsoft), et le nombre obtenu se chiffrait à plus de 1 000 abonnements. Un tel événement se traduirait par la création de structures personnalisées servant à créer et à gérer des abonnements pour une organisation.

Avec le gestionnaire de ressources, le déploiement de plusieurs environnements dans un abonnement est beaucoup plus simple. Il assouplit les CAP fixes précédentes sur les ressources du modèle précédent, ce qui réduit considérablement le besoin de partition en raison de contraintes de ressources.

Les environnements peuvent être placés dans des groupes de ressources, auxquels on peut appliquer un RBAC spécifique, ce qui vous permet de fournir l’isolation de l’environnement. Dans les scénarios où l’isolement géographique est requis, ce peut être aussi fait en utilisant des groupes de ressources. Comme les groupes de ressources peuvent s’étendre sur des zones géographiques, l’isolation spécifique pour un ou plusieurs sites peut se faire.

Vous pouvez appliquer des balises aux ressources et aux groupes de ressources qui peuvent être utilisées dans les Reports de facturation et les résumés des vues pour assurer l’isolation de la facturation. Vous pouvez utiliser des balises pour définir le type d’environnement (recherche, formation, développement, test, production), ou des services ou des individus (« RH », « Finance », « John Smith », « Jane Jones »).

La spécification d’audit est fournie dans le cadre du jeu sous-jacent d’Azure Resource Manager dans la boîte de fonctionnalités et peut être affichée dans un emplacement central.

Les clients finaux ont des comptes inscrits dans Azure Active Directory utilisé pour l’authentification et le contrôle d’accès basé sur les rôles à l’environnement et les ressources.

#### Optimisation de la densité

Bien que les limites de ressource soient assouplies dans Azure Resource Manager, il y aura toujours des limites. Au-delà de la création d’environnements, vous devez également prendre en compte la densité des environnements au sein de l’abonnement. La capacité de fournir un environnement revient à évaluer quel individu ou entreprise concerné doit vraiment en bénéficier. En particulier, il faut identifier les variantes entre les clients petits, moyens, grands et très grands en termes de ressources requises.

Vous pouvez choisir d’utiliser des abonnements différents pour des tailles différentes pour atteindre une densité supérieure. Par exemple, vous pouvez mettre en place 1 000 environnements de petite taille, 500 déploiements de taille moyenne, 100 déploiements de grande taille et 10 de très grande taille dans un abonnement. Comme le coût de plusieurs abonnements n’est pas facturé, vous pouvez isoler les différentes tailles dans différents abonnements pour fournir la densité maximale. Cela est possible tout en conservant un nombre d’abonnements relativement modeste et facile à gérer.

Il faut se demander si vous êtes prêt à autoriser un client à augmenter ou modifier les tailles et, le cas échéant, comment s’y prendre.

Une approche consiste à permettre à un client d’acquérir une fonctionnalité supplémentaire dans les groupes de ressources existant. Cela peut être facilement résolu techniquement, mais avec des conséquences sur la densité. Au lieu de tailles bien définies pour tous les clients, on a affaire à un niveau de variabilité qui ajoute une charge plus importante pour l’optimisation de la densité. Si chaque environnement de petite taille a une taille X, vous pouvez facilement d’abord calculer combien d’environnements de petite taille vous pouvez mettre dans un abonnement pour obtenir la densité optimale. Lorsque vous autorisez les clients à personnaliser l’environnement, le nombre de variantes est imprévisible et les quantités peuvent être de X, X + 1, X + 2, etc. Avec ce niveau de variabilité, vous obtenez moins densité que celle requise pour réserver de la capacité dans un abonnement pour réaliser ces variations.

Bien que possible, cela est loin d’être une solution idéale générale, car elle offre moins densité et nécessite une gestion plus complexe. Pour les environnements de grande taille, cela peut être une solution plus viable. Comme un nombre restreint de ces environnements de grande et très grande taille sont placés dans un abonnement, vous pouvez choisir de placer moins d’éléments dans un abonnement pour faire face à l’augmentation.

Une autre approche consiste à supprimer l’environnement des tailles du client et créer un nouvel environnement de taille différente. Bien que ne convenant pas dans certains cas, cette méthode fonctionne bien pour les environnements qui sont utilisés de façon temporaire comme les environnements de développement et de test.

L’approche suivante la plus simple est de fournir au client la possibilité d’acquérir un environnement de taille supérieure, puis de gérer lui-même la migration de cet environnement. Par exemple, un client qui a un déploiement de SQL Server dans un environnement de petite taille peut acquérir un environnement de taille moyenne et serait lui-même responsable du transfert de données et de l’état personnalisé.

Une autre solution consiste à fournir un service géré où cette transition d’une taille à l’autre est prise en charge. C’est évidemment plus compliqué, mais selon les charges de travail et les clients, c’est peut-être une option que votre entreprise peut réaliser.

## Fournir des environnements avec des contraintes de stratégie client supplémentaires

Certaines entreprises ont des exigences et des stratégies supplémentaires pour les environnements qu’elles déploient. Plus précisément, elles ont des stratégies qui limitent les ports exposés en externe et peuvent avoir des stratégies qui requièrent une surveillance du trafic entrant et sortant de l’environnement. Pour des raisons de coût et de prise en charge, il se peut qu’il y ait également des contraintes sur les ressources que le client final peut créer, mettre à jour ou supprimer. Pour l’entreprise qui fournit l’environnement, elles nécessitent généralement la prise en charge de l’abonnement à l’assistance.

Surensemble du scénario précédent, cela nécessiterait l’ajout de certaines ressources avec des contraintes supplémentaires pour désigner qui pourrait et ne pourrait pas créer des ressources d’un certain type.

La possibilité pour un utilisateur de créer, mettre à jour ou supprimer certaines ressources peut être définie à l’aide de rôles basés sur le contrôle d’accès. On peut citer en exemple une entreprise nécessitant un certain type de réseau virtuel et des sous-réseaux que le client final ne peut pas mettre à jour ou supprimer.

Des verrous de ressources peuvent être installés pour établir que les ressources sont en lecture seule ou ne peuvent pas être supprimées. Le RBAC permet d’autoriser les utilisateurs ou les principaux services à effectuer certaines activités sur une ressource ou un groupe de ressources.

Si l’entreprise demande à ce que certains trafics, par exemple, le trafic entre les couches de l’application, passent d’abord par un intermédiaire tel qu’un matériel de réseau virtuel, il faut alors utiliser des itinéraires définis par l’utilisateur.

Un appareil virtuel n’est rien de plus qu’une machine virtuelle qui exécute une application permettant de gérer le trafic réseau d’une certaine façon, comme un pare-feu ou un périphérique NAT. De nombreux tiers fournissent des équipements de réseau virtuel sur Azure, et les entreprises peuvent également y placer les leurs.

Une approche d’équipement « apportez votre propre » permet à une entreprise de réutiliser le code existant qui peut être utilisé dans leurs environnements locaux. La machine virtuelle d’appliance virtuelle doit être capable de recevoir le trafic entrant qui ne lui est pas adressé. Pour permettre à une machine virtuelle de recevoir le trafic adressé à d’autres destinations, vous devez activer le transfert IP dans la machine virtuelle.

Comme dans les exemples précédents, le cycle de vie des ressources et les contraintes RBAC doivent être examinés et considérés comme faisant partie de votre stratégie de groupe de ressources.

## Sécurisation des ressources vis-à-vis des éléments malveillants

Un des soucis de l’entreprise est de protéger ses ressources et modèles contre les éléments malveillants.

Citons l’exemple d’une banque souhaitant garantir qu’un développeur de logiciels non autorisé ou un membre du personnel informatique ne puisse pas faire des modifications ou extraire des informations cruciales qui se traduisant par des données transférées vers un acteur malveillant à des fins criminelles.

Un scénario d’entreprise classique consiste à offrir à un petit groupe d’opérateurs de confiance l’accès à des secrets cruciaux figurant dans les charges de travail déployées, avec un groupe plus étendu de personnel de développement/opérationnel pouvant créer ou mettre à jour des déploiements de machines virtuelles.

Azure Key Vault s’utilise avec ARM pour orchestrer et stocker des certificats et secrets de machines virtuelles.

La bonne pratique consiste à conserver des modèles ARM distincts pour la création de coffres (qui contiennent le matériel clé) et le déploiement de machines virtuelles (avec les références URI des clés contenues dans les coffres).

Les secrets stockés dans le coffre à clé sont sous le contrôle RBAC total d’un opérateur de confiance. Si l’opérateur de confiance quitte l’entreprise ou est transféré avec la société dans un nouveau groupe, son accès aux clés qu’il a créées dans le coffre est supprimé.

Les modèles ARM pour le déploiement contiennent uniquement les références URI aux secrets. En d’autres termes, cela signifie que les secrets ne sont pas dans le code, la configuration ou les référentiels de code source. Les opportunités de récupération des secrets par hameçonnage et les possibilités pour un acteur malveillant d’apporter des modifications inappropriées sont ainsi réduites.

Comme indiqué plus haut dans le document, il n’existe pas de coffre de clés. Sachant que les coffres de clés sont toujours régionaux, les secrets restent localisés (et indépendants) sur les machines virtuelles.

Un exemple d’installation de cette solution a été fourni dans la section Secrets et certificats plus haut dans ce document.

## Activation d’un modèle « apportez votre propre abonnement »

Les entreprises informatiques, les intégrateurs de systèmes, et les fournisseurs de Services de cloud peuvent faire appel à un modèle de type « Apportez votre propre abonnement » pour leurs clients. Plus précisément, l’entreprise fournit un service au client final et utilise l’abonnement Azure de ce client d’une certaine façon.

Il existe plusieurs variantes de cette approche, chacune ayant des exigences légèrement différentes, comme détaillé ci-dessous.

### Activation d’un accès d’un tiers pour le suivi des ressources dans un compte

Une entreprise avec une application de surveillance peut nécessiter un accès en lecture seule de l’abonnement d’un client et ce, pour récupérer des données à utiliser dans cette application. Cela nécessiterait un accès en lecture seule pour une durée déterminée. L’accès doit être sous contrôle du client, pour qu’il puisse mettre fin à l’accès si la relation avec le fournisseur de service de surveillance est interrompue.

#### Mettre en œuvre des ressources avec Azure Resource Manager

Des informations détaillées sur cette mise en œuvre peuvent être consultées dans « Guide des développeurs sur Auth avec l’API d’Azure Resource Manager » qui se trouve [ici](http://www.dushyantgill.com/blog/2015/05/23/developers-guide-to-auth-with-azure-resource-manager-api/). Ce document fournit des instructions de mise en œuvre pas à pas, ainsi que des exemples de code.

### Activation d’accès pour un tiers pour un déploiement unique du logiciel

Dans un autre exemple, une entreprise peut déployer et configurer une version de son logiciel dans un compte client qui nécessite un accès en écriture pour la durée du déploiement.

#### Mettre en œuvre des ressources avec Azure Resource Manager

Il s’agit d’une approche similaire à l’exemple précédent.

Selon les besoins spécifiques de l’installation, le rôle spécifique affecté au service principal doit autoriser uniquement le niveau minimal d’accès requis pour réaliser l’installation, puis faire en sorte que l’accès soit annulé immédiatement après achèvement de l’installation.

### L’activation de l’accès pour un tiers pour l’utilisation des abonnements du client pour le stockage de données

Dans un autre exemple, une entreprise peut souhaiter exécuter des logiciels dans leur propre environnement, mais utiliser le compte du client pour le stockage. Cela met le client sous contrôle de leurs données en permanence et lui permet de tirer parti d’autres technologies de la plate-forme, par exemple Azure Machine Learning ou HDInsight, à leur convenance tout en n’ajoutant pas de coûts/facturation pour l’entreprise informatique, l’intégrateur de système ou le CSV qui fournit la fonctionnalité. Cela requiert un accès permanent au compte de stockage pour l’entreprise, avec le client sous contrôle et un accès d’audit pour accéder à ces informations.

#### Mettre en œuvre des ressources avec Azure Resource Manager

Mise en œuvre à l’aide du même modèle que les autres exemples. On fournit un accès au stockage de ressources à un service principal. Comme ce scénario requiert le droit d’accès en lecture et en écriture au compte de stockage, le rôle de collaborateur intégré est affecté au service principal pour atteindre ce niveau d’accès.

Comme ce scénario implique une entité et un tiers avec un compte de stockage partagé, il faudra également s’assurer que le compte de stockage n’est pas supprimé par inadvertance. Pour cet aspect du scénario, vous devez appliquer un verrou de ressource pour le compte de stockage.

### Activation de la gestion de service par un tiers

Dans un autre exemple, une entreprise souhaite déployer, suivre et gérer les logiciels dans l’abonnement du client. Il peut y avoir des contraintes sur le client en termes de modifications qu’ils peuvent apporter ou non, à un environnement avec des logiciels déployés.

#### Mettre en œuvre des ressources avec Azure Resource Manager

Cela provient d’un surensemble du modèle identifié au début de cette section. Plus précisément, un service principal utilisé par un tiers reçoit un accès complet aux ressources dans le groupe de ressources.

En outre, étant donné les contraintes sur le client, les utilisateurs ou groupes du client reçoivent des droits appropriés pour utiliser l’environnement. Cela est possible via des modèles identifiés précédemment dans cette section.

Enfin, on peut souhaiter s’assurer que certaines ressources ne sont pas supprimées par inadvertance. Si c’est le cas, il faut envisager également des verrous de ressources pour les ressources qui nécessitent ce type de protection.

## Étapes suivantes

- Pour en savoir plus sur la création de modèles, consultez [Création de modèles](resource-group-authoring-templates.md).
- Pour obtenir des recommandations sur la façon de gérer la sécurité dans Azure Resource Manager, consultez [Considérations de sécurité pour Azure Resource Manager](best-practices-resource-manager-security.md).
- Pour en savoir plus sur le partage d’état vers et depuis des modèles, consultez [Partage d’état dans les modèles Azure Resource Manager](best-practices-resource-manager-state.md).

<!---HONumber=AcomDC_1223_2015-->