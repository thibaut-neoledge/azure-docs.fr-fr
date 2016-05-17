<properties
   pageTitle="FAQ du Centre de sécurité Azure | Microsoft Azure"
   description="Ce forum aux questions concerne le Centre de sécurité Azure."
   services="security-center"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="StevenPo"
   editor=""/>

<tags
   ms.service="security-center"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/02/2016"
   ms.author="terrylan"/>

# FAQ du Centre de sécurité Azure

Ce forum aux questions répond aux questions concernant le Centre de sécurité Azure, qui vous aide à prévenir, détecter et résoudre les menaces grâce à une meilleure visibilité et à un meilleur contrôle de la sécurité de vos ressources Microsoft Azure.

> [AZURE.NOTE] Les informations contenues dans ce document s’appliquent à la version préliminaire du Centre de sécurité Azure.

## Questions générales

### Qu’est-ce que le Centre de sécurité Azure ?
Le Centre de sécurité Azure vous aide à prévenir, détecter et résoudre les menaces grâce à une visibilité et un contrôle accrus de la sécurité de vos ressources Azure. Il fournit une surveillance de la sécurité et une gestion des stratégies intégrées pour l’ensemble de vos abonnements, vous aidant ainsi à détecter les menaces qui pourraient passer inaperçues. De plus, il est compatible avec un vaste écosystème de solutions de sécurité.

### Comment obtenir le Centre de sécurité Azure ?
Le Centre de sécurité Azure est disponible avec votre abonnement Microsoft Azure et accessible à partir du [portail Azure](https://azure.microsoft.com/features/azure-portal/). ([Connectez-vous au portail](https://portal.azure.com), sélectionnez **Parcourir**, puis faites défiler jusqu’à **Centre de sécurité**). Des recommandations de sécurité peuvent déjà apparaître dans le tableau de bord. Cela s’explique par le fait que le service peut évaluer l’état de sécurité de certains contrôles en fonction de leur configuration dans Azure. Pour activer l’ensemble des fonctionnalités de sécurité, telles que la surveillance, les recommandations et les alertes, vous devez [activer la collecte des données](#data-collection).

## Facturation

### Comment fonctionne la facturation pour le Centre de sécurité Azure ?
Pour plus d’informations, consultez [Tarification du Centre de sécurité](https://azure.microsoft.com/pricing/details/security-center/).

## Collecte des données

### Comment activer la collecte des données ?<a name=data-collection></a>
Vous pouvez activer la collecte des données pour vos abonnements Azure dans la stratégie de sécurité. Pour activer la collecte des données, [connectez-vous au portail Azure](https://portal.azure.com), sélectionnez **Parcourir**, **Centre de sécurité**, puis **Stratégie de sécurité**. Définissez **Collecte des données** sur **Activé**, puis configurez les comptes de stockage dans lesquels vous voulez stocker les données collectées (voir la question « [Où sont stockées mes données ?](#where-is-my-data-stored) »). Quand **Collecte des données** est activé, les informations liées à la configuration et aux événements de sécurité sont automatiquement collectées sur l’ensemble des machines virtuelles prises en charge par l’abonnement.

> [AZURE.NOTE] Bien que vous puissiez définir les stratégies de sécurité au niveau du groupe de ressources et de l’abonnement Azure, la configuration de la collecte des données intervient uniquement au niveau de l’abonnement.

### Que se passe-t-il quand j’active la collecte des données ?
La collecte des données peut être activée via l’agent de surveillance Azure et via l’extension Surveillance de la sécurité Azure. L’extension Surveillance de la sécurité Azure analyse diverses configurations de sécurité et les envoie sous forme de traces de [suivi d’événements pour Windows](https://msdn.microsoft.com/library/windows/desktop/bb968803.aspx) (ETW). En outre, le système d’exploitation crée des entrées de journal des événements. L’agent de surveillance Azure lit les entrées du journal des événements et les traces ETW, puis les copie dans votre compte de stockage pour les analyser. Le compte de stockage en question est celui que vous avez configuré dans la stratégie de sécurité. Pour plus d’informations sur les comptes de stockage, reportez-vous à la question « [Où sont stockées mes données ?](#where-is-my-data-stored) ».

### L’agent de surveillance et l’extension Surveillance de la sécurité ont-ils un impact sur les performances de mon serveur ?
L’agent et l’extension utilisent une quantité minime de ressources système et n’ont donc qu’un faible impact sur les performances.

### Comment désactiver la collecte des données ?
Vous pouvez désactiver la **collecte des données** pour un abonnement dans la stratégie de sécurité. ([Connectez-vous au portail Azure](https://portal.azure.com), sélectionnez **Parcourir**, sélectionnez **Centre de sécurité**, puis sélectionnez **Stratégie de sécurité**.) Quand vous sélectionnez un abonnement, un nouveau panneau s’ouvre et affiche une option permettant de désactiver la collecte des données. Sélectionnez l’option **Supprimer des agents** dans le ruban supérieur pour supprimer des agents des machines virtuelles existantes.

> [AZURE.NOTE] Vous pouvez définir les stratégies de sécurité au niveau du groupe de ressources et de l’abonnement Azure, mais vous devez sélectionner un abonnement pour désactiver la collecte des données.

### Où sont stockées mes données ?<a name=where-is-my-data-stored></a>
Pour chaque région où s’exécutent des machines virtuelles, vous devez choisir le compte de stockage où doivent être stockées les données collectées à partir de ces machines virtuelles. Ainsi, vous pouvez stocker les données dans une même région pour garantir la confidentialité et la souveraineté des données. Vous devez sélectionner le compte de stockage d’un abonnement dans la stratégie de sécurité. ([Connectez-vous au portail Azure](https://portal.azure.com), sélectionnez **Parcourir**, **Centre de sécurité**, puis **Stratégie de sécurité**.) Quand vous cliquez sur un abonnement, un nouveau panneau s’ouvre. Sélectionnez **Choisir un compte de stockage** pour sélectionner une région. Pour des raisons de sécurité, les données collectées sont isolées logiquement des autres données clients.

> [AZURE.NOTE] Bien que vous puissiez définir les stratégies de sécurité au niveau du groupe de ressources et de l’abonnement Azure, la sélection d’une région pour votre compte de stockage intervient uniquement au niveau de l’abonnement.

Pour plus d’informations sur Azure Storage et les comptes de stockage, consultez [Documentation Azure Storage](https://azure.microsoft.com/documentation/services/storage/) et [À propos des comptes de stockage Azure](../storage/storage-create-storage-account.md).

## Utilisation du Centre de sécurité Azure

### Qu’est-ce qu’une stratégie de sécurité ?
Une stratégie de sécurité définit l’ensemble des contrôles recommandés pour les ressources d’un abonnement ou groupe de ressources spécifique. Dans le Centre de sécurité Azure, vous définissez les stratégies pour vos abonnements et groupes de ressources Azure en fonction des exigences de sécurité de votre société et du type d’applications ou du niveau de confidentialité des données de chaque abonnement.

Par exemple, les ressources utilisées pour le développement ou le test peuvent avoir des exigences de sécurité différentes de celles utilisées pour les applications de production. De même, les applications dont les données sont réglementées (par exemple, les informations d’identification personnelle (PII)) peuvent nécessiter un niveau de sécurité plus élevé. Les stratégies de sécurité activées dans le Centre de sécurité Azure détermineront les recommandations et la surveillance liées à la sécurité. Pour plus d’informations sur les stratégies de sécurité, consultez [Surveillance de l’intégrité de la sécurité dans le Centre de sécurité Azure](security-center-monitoring.md).

> [AZURE.NOTE] En cas de conflit entre la stratégie de niveau abonnement et celle de niveau groupe de ressources, la stratégie de niveau groupe de ressources est prioritaire.

### Qui peut modifier une stratégie de sécurité ?
Vous pouvez configurer des stratégies de sécurité pour chaque abonnement ou groupe de ressources. Pour modifier une stratégie de sécurité au niveau de l’abonnement ou du groupe de ressources, vous devez être propriétaire de l’abonnement ou collaborer à celui-ci.

Pour savoir comment configurer une stratégie de sécurité, consultez [Définition de stratégies de sécurité dans le Centre de sécurité Azure](security-center-policies.md).

### Qu’est-ce qu’une recommandation de sécurité ?
Le Centre de sécurité Azure analyse l’état de sécurité de vos ressources Azure. Quand des failles de sécurité potentielles sont identifiées, des recommandations sont créées. Les recommandations vous guident tout au long du processus de configuration du contrôle. Voici quelques exemples :

- Approvisionnement d’un logiciel anti-programme malveillant pour identifier et supprimer les programmes malveillants
- Configuration de [groupes de sécurité réseau](../virtual-network/virtual-networks-nsg.md) et de règles pour contrôler le trafic vers les machines virtuelles
- Approvisionnement d’un pare-feu d’applications web pour se défendre des attaques ciblant vos applications web
- Déploiement de mises à jour système manquantes
- Correction des configurations de système d’exploitation qui ne suivent pas les recommandations

Seules les recommandations qui sont activées dans les stratégies de sécurité sont affichées ici.

### Comment connaître l’état de sécurité actuel de mes ressources Azure ?
La mosaïque **Intégrité des ressources** du panneau **Centre de sécurité** affiche la posture de sécurité globale de votre environnement, avec le détail de vos machines virtuelles, applications web et autres ressources. Chaque ressource possède un indicateur montrant la présence éventuelle de failles de sécurité. En cliquant sur la mosaïque Intégrité des ressources, vous affichez vos ressources et les problèmes éventuels les concernant.

### Qu’est-ce qui déclenche une alerte de sécurité ?
Le Centre de sécurité Azure collecte, analyse et fusionne automatiquement les données du journal à partir de vos ressources Azure, du réseau et des solutions partenaires telles que les logiciels anti-programme malveillant et les pare-feu. Quand des menaces sont détectées, une alerte de sécurité est créée. Voici quelques exemples de détections :

- Des machines virtuelles compromises qui communiquent avec des adresses IP connues comme étant malveillantes
- Des programmes malveillants avancés qui sont détectés à l’aide du rapport d’erreurs Windows
- Des attaques par force brute contre des machines virtuelles
- Des alertes de sécurité émises par des solutions de sécurité partenaires intégrées, telles que des logiciels anti-programme malveillant ou des pare-feu d’applications web

### Quelle est la différence entre les menaces détectées et faisant l’objet d’une alerte par Microsoft Security Response Center et par Azure Security Center ?
Microsoft Security Response Center (MSRC) effectue certaines analyses de sécurité sur l'infrastructure et le réseau Azure et reçoit des informations sur les menaces et des plaintes pour mauvaise utilisation provenant de tiers. Lorsque MSRC constate que les données client ont été utilisées par un tiers illégal ou non autorisé ou que l'utilisation d’Azure par le client ne respecte pas les conditions de bon usage, un gestionnaire des incidents de sécurité informe le client. La notification correspond généralement à un courrier électronique envoyé au(x) contact(s) de sécurité spécifié(s) dans Azure Security Center ou au propriétaire de l'abonnement Azure si aucun contact de sécurité n'est spécifié.

Security Center est un service Azure qui surveille en continu l'environnement Azure du client et applique des analyses de façon à détecter automatiquement un large éventail d'activités potentiellement malveillantes. Ces détections sont signalées en tant qu'alertes de sécurité dans le tableau de bord du Security Center. À l'avenir, une notification par courrier électronique des alertes de sécurité sera également envoyée au(x) contact(s) de sécurité.

### Comment les autorisations sont-elles gérées dans le Centre de sécurité Azure ?
Le Centre de sécurité Azure prend en charge l’accès en fonction du rôle. Pour plus d’informations sur le contrôle d’accès en fonction du rôle, consultez [Contrôle d’accès en fonction du rôle Azure Active Directory](../active-directory/role-based-access-control-configure.md).

Quand vous ouvrez le Centre de sécurité Azure, seules les recommandations et les alertes se rapportant aux ressources auxquelles vous avez accès sont visibles. Autrement dit, vous voyez uniquement les éléments liés aux ressources pour lesquelles vous avez le rôle de propriétaire, collaborateur ou lecteur, pour l’abonnement ou le groupe de ressources auquel appartiennent les ressources.

Pour modifier une stratégie de sécurité, vous devez avoir le rôle de propriétaire ou de collaborateur pour l’abonnement concerné.

### Quels sont les types de machines virtuelles pris en charge ?
Les machines virtuelles créées à partir des [modèles de déploiement de type classique et Resource Manager](../azure-classic-rm.md) sont prises en charge, y compris celles qui font partie de clusters Azure Service Fabric.

Les recommandations relatives aux listes de contrôle d’accès s’appliquent actuellement aux machines virtuelles de type Classique. Pour l’heure, les groupes de sécurité réseau s’appliquent uniquement aux machines virtuelles de type Resource Manager.

### Les machines virtuelles Linux sont-elles prises en charge ?
Le Centre de sécurité Azure fournit une surveillance de base pour les machines virtuelles Linux (Ubuntu versions 12.04, 14.04, 14.10 et 15.04 uniquement). Une surveillance renforcée de l’intégrité de la sécurité et une analyse/collecte de données améliorée seront prochainement disponibles, tout comme la prise en charge d’autres distributions de Linux.

<!---HONumber=AcomDC_0504_2016-->