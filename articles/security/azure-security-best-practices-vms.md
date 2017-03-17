---
title: "Meilleures pratiques de sécurité Azure pour les machines virtuelles Azure | Microsoft Docs"
description: "Cet article propose une collection de meilleures pratiques de sécurité à utiliser dans les machines virtuelles situées dans Azure."
services: security
documentationcenter: na
author: YuriDio
manager: mbaldwin
editor: 
ms.assetid: 5e757abe-16f6-41d5-b1be-e647100036d8
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/02/2017
ms.author: yurid
translationtype: Human Translation
ms.sourcegitcommit: cea53acc33347b9e6178645f225770936788f807
ms.openlocfilehash: d6c5ea3e44b6121377d7d51f2bb9c878f9f933c5
ms.lasthandoff: 03/03/2017


---
# <a name="azure-virtual-machine-security-best-practices"></a>Meilleures pratiques de sécurité pour les machines virtuelles Azure

Dans la plupart des scénarios IaaS (Infrastructure en tant que service), les [machines virtuelles](https://docs.microsoft.com/en-us/azure/virtual-machines/) sont la charge de travail principale pour les organisations qui utilisent l’informatique cloud. Cela est prédominant dans les [scénarios hybrides](https://social.technet.microsoft.com/wiki/contents/articles/18120.hybrid-cloud-infrastructure-design-considerations.aspx) où les organisations souhaitent migrer lentement les charges de travail vers le cloud. Vous devez suivre le scénario de [considérations générales de sécurité pour IaaS](https://social.technet.microsoft.com/wiki/contents/articles/3808.security-considerations-for-infrastructure-as-a-service-iaas.aspx) et vous assurer d’avoir appliqué les meilleures pratiques de sécurité à toutes vos machines virtuelles situées dans Azure.

Dans cet article, nous aborderons différentes meilleures pratiques en matière de sécurité de machine virtuelle sur Azure. Ces meilleures pratiques sont issues de notre expérience avec des machines virtuelles Azure, mais également de celle des clients, comme vous. 

Pour chaque meilleure pratique, nous allons détailler les éléments suivants :

- Nature de la meilleure pratique
- Raison pour laquelle activer cette meilleure pratique
- Conséquence possible en cas de non-utilisation de la meilleure pratique
- Alternatives possibles à la meilleure pratique
- Comment apprendre à utiliser la meilleure pratique

Cet article repose sur un consensus, ainsi que sur les fonctionnalités et ensembles de fonctions de la plateforme Azure disponibles lors de l’écriture. Les opinions et avis évoluent au fil du temps ; cet article sera régulièrement mis à jour de manière à tenir compte de ces changements.

Les meilleures pratiques en matière de sécurité des machines virtuelles Azure qui sont abordées dans le cadre de cet article sont les suivantes :

- Authentification de machine virtuelle et contrôle d’accès
- Disponibilité de machine virtuelle et accès au réseau
- Protéger les données au repos sur des machines virtuelles Azure en appliquant le chiffrement
- Gestion des mises à jour de machines virtuelles
- Gérer la posture de sécurité de votre machine virtuelle
- Surveillances performances de machine virtuelle

## <a name="virtual-machine-authentication-and-access-control"></a>Authentification de machine virtuelle et contrôle d’accès

La première étape dans la protection de votre machine virtuelle consiste à vous assurer que seuls les utilisateurs autorisés sont en mesure de configurer de nouvelles machines virtuelles. Vous pouvez utiliser les [stratégies Resource Manager](../azure-resource-manager/resource-manager-policy.md) pour établir des conventions pour les ressources de votre organisation, créer des stratégies personnalisées et appliquer ces stratégies à des ressources, comme un [groupe de ressources](../azure-resource-manager/resource-group-overview.md). Les machines virtuelles qui appartiennent à ce groupe de ressources hériteront de ces stratégies. Bien que cette approche est recommandée pour gérer les ressources (y compris les machines virtuelles) qui ont des besoins différents et sont situées dans différents groupes de ressources, vous pouvez également contrôler l’accès aux machines virtuelles individuellement à l’aide du [contrôle d’accès basé sur les rôles (RBAC)](../active-directory/role-based-access-control-configure.md).

En activant les stratégies Azure Resource Manager et RBAC pour contrôler l’accès aux machines virtuelles, vous améliorez la sécurité globale de votre machine virtuelle. Il est recommandé de placer les machines virtuelles fortement couplées qui partagent le même cycle de vie dans un même groupe de ressources. Les groupes de ressources vous permettent de déployer et de surveiller les ressources en tant que groupe et de répercuter les coûts de facturation par groupe de ressources. Utilisez l’approche de [moindre privilège](https://technet.microsoft.com/en-us/windows-server-docs/identity/ad-ds/plan/security-best-practices/implementing-least-privilege-administrative-models) pour autoriser l’accès aux utilisateurs afin de configurer des machines virtuelles, et envisagez d’utiliser les rôles intégrés suivants dans Azure lors de l’attribution de privilèges aux utilisateurs :

- [Collaborateur de machine virtuelle](../active-directory/role-based-access-built-in-roles.md#virtual-machine-contributor) : peut gérer les machines virtuelles, mais pas le réseau virtuel ou le compte de stockage auquel elles sont connectées.
- [Collaborateur de machine virtuelle classique](../active-directory/role-based-access-built-in-roles.md#classic-virtual-machine-contributor) : peut gérer les machines virtuelles classiques, mais pas le réseau virtuel ou le compte de stockage auquel elles sont connectées.
- [Gestionnaire de sécurité](../active-directory/role-based-access-built-in-roles.md#security-manager) : gérer les composants de sécurité, les stratégies de sécurité et les machines virtuelles.
- [Utilisateur de DevTest Labs](../active-directory/role-based-access-built-in-roles.md#devtest-labs-user) : afficher tout et connecter, démarrer, redémarrer et arrêter les machines virtuelles

Ne partagez pas de comptes ni de mots de passe entre plusieurs administrateurs, et ne réutilisez pas les mots de passe sur plusieurs comptes d’utilisateur ou services, notamment pour les médias sociaux ou d’autres activités non administratives. Dans l’idéal, vous devez utiliser les modèles [Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md) pour approvisionner vos machines virtuelles en toute sécurité. À l’aide de cette approche, vous pouvez renforcer les choix de déploiement et appliquer les paramètres de sécurité tout au long du déploiement.

Les organisations qui n’appliquent aucun contrôle d’accès aux données via des fonctionnalités telles que RBAC risquent d’octroyer plus de privilèges que nécessaire à leurs utilisateurs. Cela risque d’entraîner la compromission des données, car vous pouvez bénéficier d’un accès non justifié à certaines données.
 

## <a name="virtual-machine-availability-and-network-access"></a>Disponibilité de machine virtuelle et accès au réseau

Si votre machine virtuelle exécute des applications critiques qui ont besoin d’une haute disponibilité, il est fortement recommandé d’utiliser plusieurs machines virtuelles.  Pour une meilleure disponibilité, créez au moins deux machines virtuelles dans le [groupe à haute disponibilité](../virtual-machines/virtual-machines-windows-infrastructure-availability-sets-guidelines.md). [L’équilibreur de charge](../load-balancer/load-balancer-overview.md) Azure requiert également que les machines virtuelles avec équilibrage de charge appartiennent au même groupe à haute disponibilité. Si ces machines virtuelles doivent être accessibles à partir d’Internet, vous devez configurer un [équilibrage de charge accessible sur Internet](../load-balancer/load-balancer-internet-overview.md).

Lorsque les machines virtuelles sont exposées à Internet, il est important de vous assurer que vous avez [le contrôle du flux de trafic réseau avec les groupes de sécurité réseau](../virtual-network/virtual-networks-nsg.md).  Comme les groupes de sécurité réseau peuvent être appliqués à des sous-réseaux, vous pouvez réduire le nombre de groupes de sécurité réseau en regroupant vos ressources par sous-réseau et en appliquant des groupes de sécurité réseau aux sous-réseaux. L’objectif est de créer une couche d’isolement réseau, ce qui est possible en configurant correctement les fonctionnalités de [sécurité réseau](../best-practices-network-security.md) dans Azure.  

Vous pouvez également utiliser fonctionnalité d’accès juste à temps aux machines virtuelles dans Azure Security Center pour contrôler qui et la période pendant laquelle un utilisateur peut avoir accès à distance à une machine virtuelle spécifique. Regardez la vidéo ci-dessous pour plus d’informations sur l’utilisation de ces fonctionnalités :


<iframe src="https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Security-Center-Just-in-Time-VM-Access/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

Les organisations qui n’appliquent pas la restriction d’accès réseau aux machines virtuelles accessibles sur Internet sont exposées à des risques de sécurité, comme les attaques par force brute RDP. 

## <a name="protect-data-at-rest-in-azure-vms-by-enforcing-encryption"></a>Protéger les données au repos sur des machines virtuelles Azure en appliquant le chiffrement

De nos jours, le [chiffrement des données au repos](https://blogs.microsoft.com/cybertrust/2015/09/10/cloud-security-controls-series-encrypting-data-at-rest/) est une étape obligatoire du processus de gestion de la confidentialité, de la conformité et de la souveraineté des données. [Azure Disk Encryption](../security/azure-security-disk-encryption.md) permet aux administrateurs informatiques de chiffrer des disques de machines virtuelles Windows et Linux IaaS. Azure Disk Encryption s’appuie sur la fonctionnalité standard BitLocker Windows et la fonctionnalité DM-Crypt de Linux pour assurer le chiffrement de volume du système d’exploitation et des disques de données.

Vous pouvez tirer parti d’Azure Disk Encryption pour protéger vos données, afin de répondre aux exigences de l’entreprise en matière de sécurité et de conformité. Les organisations doivent également envisager d’utiliser le chiffrement pour réduire les risques liés aux accès non autorisés. Avant d’écrire des données sensibles sur les lecteurs, il est également recommandé de chiffrer ces derniers. 

Veillez à chiffrer les volumes de données de vos machines virtuelles afin de protéger les volumes au repos associés à votre compte de stockage Azure. Protégez les clés de chiffrement et les secrets en tirant parti [d’Azure Key Vault](https://azure.microsoft.com/en-us/documentation/articles/key-vault-whatis/). 

Les organisations qui n’appliquent aucun chiffrement des données sont davantage exposées à des problèmes d’intégrité des données (par exemple, des utilisateurs malveillants ou non autorisés s’efforçant de dérober des données ou d’accéder de manière frauduleuse à des données en clair, au moyen de compte compromis). Outre ces risques, les entreprises devant se conformer aux réglementations du secteur doivent prouver leur diligence ; elles utilisent les contrôles de sécurité appropriés pour améliorer la sécurité des données.

Vous pouvez en savoir plus sur Azure Disk Encryption en lisant l’article [Chiffrement de disque Azure pour des machines virtuelles Windows et Linux IaaS](azure-security-disk-encryption.md).


## <a name="manage-virtual-machine-updates"></a>Gérer les mises à jour de machines virtuelles

Azure n’envoie pas les mises à jour Windows sur les machines virtuelles Windows Azure, dans la mesure où ces machines sont destinées à être gérées par l’utilisateur. Le fonctionnement est similaire à n’importe quelle machine locale. Les clients sont cependant invités à laisser la configuration automatique de Windows Update activée. Une autre option consiste à déployer un serveur [Windows Server Update Services (WSUS)](https://technet.microsoft.com/windowsserver/bb332157.aspx) ou un autre produit de gestion des mises à jour approprié sur une autre machine virtuelle Azure ou en local. Les services WSUS et Windows Update maintiennent les machines virtuelles à jour. Il est également d’utiliser un produit d’analyse pour vous assurer que toutes les machines virtuelles IaaS sont à jour.

Les images fournies par Azure sont régulièrement mises à jour pour inclure les mises à jour de Windows les plus récentes. Toutefois, il n’existe aucune garantie que les images seront à jour au moment du déploiement. Il est possible qu’il y ait un léger décalage (pas plus de quelques semaines) par rapport aux versions publiques. Vous devez commencer par effectuer la vérification et l’installation de toutes les mises à jour de Windows lors de chaque déploiement. Cela est particulièrement important lors du déploiement d’images que vous fournissez ou à partir de votre propre bibliothèque. Les images fournies dans le cadre de la galerie Windows Azure disposent toujours des mises à jour automatiques Windows activées par défaut.

Les organisations qui n’appliquent pas de stratégies de mise à jour des logiciels sont plus exposées aux menaces qui exploitent les vulnérabilités connues qui ont été déjà résolues. Outre ces risques, les entreprises devant se conformer aux réglementations du secteur doivent prouver leur diligence ; elles utilisent les contrôles de sécurité appropriés pour améliorer la sécurité de leurs charges de travail situées dans le cloud.
Nous tenons à insister sur le fait qu’il existe de nombreuses similitudes entre les pratiques recommandées de mise à jour de logiciel dans un centre de données traditionnel et Azure IaaS, et il est par conséquent recommandé d’évaluer vos stratégies de mise à jour logicielle en cours pour inclure des machines virtuelles Azure.

## <a name="manage-virtual-machine-secure-posture"></a>Gérer la posture de sécurité de vos machines virtuelles

Les menaces évoluent, et la protection de vos machines virtuelles requiert une fonctionnalité plus riche, en mesure de détecter les menaces, déclencher des alertes et réduire les faux positifs de surveillance. La posture de sécurité pour ce type de charge de travail comprend tous les aspects de sécurité de la machine virtuelle, de la gestion des mises à jour à l’accès réseau sécurisé, tout en surveillant activement les menaces qui tentent d’accéder sans autorisation à vos ressources.

Vous pouvez utiliser [Azure Security Center](../security-center/security-center-intro.md) pour surveiller la posture de sécurité de vos machines virtuelles [Windows](../security-center/security-center-virtual-machine.md) et [Linux](../security-center/security-center-linux-virtual-machine.md). Vous pouvez exploiter les fonctionnalités suivantes dans Azure Security Center pour analyser vos machines virtuelles :

- Paramètres de sécurité du système d’exploitation avec les règles de configuration recommandées
- Sécurité du système et mises à jour critiques manquantes
- Recommandations de protection du point de terminaison (anti logiciels malveillants)
- Validation du chiffrement de disque
- Évaluation et correction des vulnérabilités
- Détection de menaces

Security Center peut surveiller activement les menaces, et ces menaces seront présentées sous Alertes de sécurité. Les menaces qui sont corrélées sont agrégées dans une vue appelée *Incident de sécurité*. Vous pouvez regarder la vidéo ci-dessous pour comprendre comment le centre de sécurité peut vous aider à identifier les menaces potentielles dans vos machines virtuelles situées dans Azure.

<iframe src="https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Security-Center-in-Incident-Response/player" width="960" height="540" allowFullScreen frameBorder="0"></iframe>

Les organisations qui n’appliquent pas une méthode sécurisée pour leurs machines virtuelles ne savent pas que des tentatives potentielles de contournement des contrôles de sécurité sont en place.

## <a name="monitoring-virtual-machine-performance"></a>Surveillances performances de machine virtuelle

L’abus de ressources peut également être un problème lorsque vous avez des processus dans une machine virtuelle qui consomment plus de ressources qu’ils le devraient. Une machine virtuelle avec des problèmes de performances peut entraîner une interruption de service, ce qui va à l’encontre d’un des principaux de sécurité : la disponibilité. Pour cette raison, il est impératif non seulement de surveiller l’accès aux machines virtuelles de façon réactive (lorsqu’un problème se déroule) mais également d’effectuer des vérifications de base pendant les heures de fonctionnement normal.

Les [Journaux de diagnostic Azure](https://azure.microsoft.com/en-us/blog/windows-azure-virtual-machine-monitoring-with-wad-extension/) peuvent vous aider à surveiller les ressources de votre machine virtuelle et identifier les problèmes potentiels qui peuvent compromettre ses performances et sa disponibilité. L’extension Diagnostics Azure fournit des fonctionnalités d’analyse et de diagnostics sur une machine virtuelle Azure basée sur Windows. Vous pouvez activer ces fonctionnalités sur la machine virtuelle en incluant l’extension dans le modèle [Azure Resource Manager](../virtual-machines/virtual-machines-windows-extensions-diagnostics-template.md). Vous pouvez également utiliser [Azure Monitor](../monitoring-and-diagnostics/monitoring-overview-metrics.md) pour obtenir plus de visibilité sur l’intégrité de votre ressource.

Les organisations qui ne surveillent pas les performances des machines virtuelles ne seront pas en mesure de déterminer si certaines modifications dans les modèles de performances font partie de son utilisation normale ou s’il existe un fonctionnement anormal consomme plus de ressources que la normale. L’anomalie peut indiquer une attaque potentielle provenant d’une ressource externe ou d’un processus compromis en cours d’exécution sur cette machine virtuelle. 
