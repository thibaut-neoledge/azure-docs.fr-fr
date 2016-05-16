<properties
   pageTitle="Gestion des recommandations de sécurité dans le Centre de sécurité Azure | Microsoft Azure"
   description="Ce document vous montre comment les recommandations du Centre de sécurité Azure peuvent vous aider à protéger vos ressources Azure et à rester en conformité avec les stratégies de sécurité."
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

# Gestion des recommandations de sécurité dans le Centre de sécurité Azure

Ce document explique comment les recommandations du Centre de sécurité Azure peuvent vous aider à protéger vos ressources Azure.

> [AZURE.NOTE] Les informations contenues dans ce document s’appliquent à la version préliminaire du Centre de sécurité Azure. Ce document présente le service à l’aide d’un exemple de déploiement. Il ne s’agit pas d’un guide pas à pas.

## Qu’est-ce que le Centre de sécurité Azure ?
Le Centre de sécurité Azure vous aide à prévenir, détecter et résoudre les menaces grâce à une visibilité et un contrôle accrus de la sécurité de vos ressources Azure. Il fournit une surveillance de la sécurité et une gestion des stratégies intégrées pour l’ensemble de vos abonnements, vous aidant ainsi à détecter les menaces qui pourraient passer inaperçues. De plus, il est compatible avec un vaste écosystème de solutions de sécurité.

## Quelles sont les recommandations de sécurité ?
Le Centre de sécurité analyse l’état de sécurité de vos ressources Azure à intervalles réguliers. Lorsqu’il identifie des failles de sécurité potentielles, il crée des recommandations. Ces recommandations vous guident tout au long du processus de configuration des contrôles nécessaires.

## Mise en œuvre des recommandations de sécurité

### Définition de recommandations

Dans la section [Définition des stratégies de sécurité dans le Centre de sécurité Azure](security-center-policies.md), vous apprendrez à :

- configurer des stratégies de sécurité ;
- activer la collecte des données ;
- choisir les recommandations à afficher dans le cadre de votre stratégie de sécurité.

Les recommandations de stratégie actuelles se concentrent sur les mises à jour système, les règles de base, les logiciels anti-programme malveillant, les [listes de contrôle d’accès (ACL) pour les points de terminaison](../virtual-machines/virtual-machines-windows-classic-setup-endpoints.md), les [groupes de sécurité réseau](../virtual-network/virtual-networks-nsg.md) pour les sous-réseaux et les interfaces réseau, l’audit des bases de données SQL, le chiffrement transparent des données de base de données SQL et les pare-feu d’applications web. L’article [Définition de stratégies de sécurité](security-center-policies.md) fournit une description de chacune des recommandations.

### Suivi des recommandations
Après la définition d’une stratégie de sécurité, le Centre de sécurité analyse l’état de sécurité de vos ressources pour identifier les vulnérabilités potentielles. La mosaïque **Recommandations** du panneau **Centre de sécurité** vous permet de connaître le nombre total de recommandations fournies par le Centre de sécurité.

![][2]

Pour afficher les détails de chaque recommandation :

1. Cliquez sur la **mosaïque Recommandations** dans le panneau **Centre de sécurité**. Le panneau **Recommandations** s’ouvre.

Les recommandations sont affichées dans un tableau où chaque ligne correspond à une recommandation. Les colonnes du tableau sont les suivantes :

- **DESCRIPTION** : explication de la recommandation et de la procédure à suivre pour résoudre le problème.
- **RESSOURCES** : ressources auxquelles s’appliquent les recommandations.
- **ÉTAT** : décrit l’état actuel de la recommandation :
    - **Ouverte** : la recommandation n’a pas encore été prise en compte.
    - **En cours** : la recommandation est actuellement appliquée aux ressources ; aucune action de votre part n’est nécessaire.
    - **Résolue** : la recommandation a déjà été appliquée (dans ce cas, la ligne est grisée).
- **GRAVITÉ** : donne le niveau de gravité de chaque recommandation :
    - **Élevée** : existence d’une vulnérabilité sur une ressource importante (application, machine virtuelle ou groupe de sécurité réseau). Le problème doit être analysé.
    - **Moyenne** : il existe une vulnérabilité ; des étapes supplémentaires ou non critiques sont requises pour l’éliminer ou pour terminer un processus.
    - **Faible** :existence d’une vulnérabilité devant être prise en compte, mais qui ne nécessite pas une attention immédiate. Par défaut, les recommandations de niveau Faible ne sont pas affichées, mais vous pouvez filtrer les recommandations pour les faire apparaître.

Utilisez le tableau ci-dessous pour mieux comprendre les recommandations et leurs effets.

> [AZURE.NOTE] Vous devez apprendre à différencier les [modèles de déploiement de type Classique et Resource Manager](../azure-classic-rm.md) utilisés pour les ressources Azure.

|Recommandation|Description|
|-----|-----|
|Activer la collecte des données pour des machines virtuelles ou des abonnements|Recommande l’activation de la collecte des données dans la stratégie de sécurité pour chacun de vos abonnements ou certaines machines virtuelles.|
|Résoudre une non-conformité aux règles de ligne de base|Recommande d’aligner les configurations de système d’exploitation sur les lignes de base recommandées, comme le fait de ne pas permettre l’enregistrement des mots de passe.|
|Appliquer des mises à jour système|Recommande le déploiement des mises à jour de sécurité du système et les mises à jour critiques manquantes sur les machines virtuelles (Windows uniquement).|
|Configurer des listes ACL pour les points de terminaison|Recommande la configuration de listes de contrôle d’accès pour restreindre l’accès entrant aux machines virtuelles (de type Classique uniquement).|
|[Ajouter un pare-feu d’applications web](security-center-add-web-application-firewall.md)|Recommande le déploiement d’un pare-feu d’applications web (WAF) pour les points de terminaison web. Vous pouvez protéger plusieurs applications web dans le centre de sécurité en les ajoutant à vos déploiements WAF existants. Les dispositifs WAF (créés à l’aide du modèle de déploiement de Resource Manager) doivent être déployés sur un réseau virtuel distinct. Les dispositifs WAF (créés à l’aide du modèle de déploiement classique) sont limités à l’utilisation d’un groupe de sécurité réseau. Cette prise en charge sera étendue prochainement à un déploiement entièrement personnalisé d’un dispositif WAF (pour les machines virtuelles de type Classique).|
|Finaliser la configuration du pare-feu d’applications web|Pour terminer la configuration d’un pare-feu d’applications web, le trafic doit être redirigé vers l’appliance de pare-feu d’applications web. L’application de cette recommandation permet d’apporter les modifications nécessaires à la configuration.|
|[Activer le logiciel anti-programme malveillant](security-center-enable-antimalware.md)|Recommande l’approvisionnement de logiciels anti-programme malveillant sur les machines virtuelles (Windows uniquement).|
|Activer des groupes de sécurité réseau sur les sous-réseaux et les interfaces réseau|Recommande l’activation de groupes de sécurité réseau sur les sous-réseaux et les interfaces réseau (machines virtuelles de type Resource Manager uniquement).|
|Restreindre l’accès à l’aide de points de terminaison externes publics|Recommande la configuration de règles de trafic entrant pour les groupes de sécurité réseau.|
|Activer l’audit des serveurs SQL|Recommande l’activation de l’audit pour les serveurs SQL Azure (service Azure SQL uniquement ; ne comprend pas les serveurs SQL exécutés sur des machines virtuelles).|
|Activer l’audit des bases de données SQL|Recommande l’activation de l’audit pour les bases de données SQL Azure (service Azure SQL uniquement ; ne comprend pas les serveurs SQL exécutés sur des machines virtuelles).|
|Activer le chiffrement transparent des données des bases de données SQL|Recommande l’activation du chiffrement pour les bases de données SQL (service Azure SQL uniquement).|
|Déployer l’agent de machine virtuelle|Vous permet de connaître les machines virtuelles qui nécessitent l’agent de machine virtuelle. L’agent de machine virtuelle doit être installé sur les machines virtuelles pour approvisionner l’analyse des correctifs, l’analyse des lignes de base et les logiciels anti-programme malveillant. L’agent de machine virtuelle est installé par défaut sur les machines virtuelles déployées depuis Azure Marketplace. L’article [Installer l’agent de machine virtuelle – Deuxième partie](http://azure.microsoft.com/blog/2014/04/15/vm-agent-and-extensions-part-2/) fournit des informations sur l’installation de l’agent de machine virtuelle.|
| [Apply disk encryption (Appliquer le chiffrement de disque Azure Disk Encryption)](security-center-apply-disk-encryption.md) |Recommande le chiffrement des disques des machines virtuelles à l’aide d’Azure Disk Encryption (Windows et Linux). Le chiffrement est recommandé pour les systèmes d’exploitation et les volumes de données de votre machine virtuelle.|
|Fournir des informations de contact de sécurité | Vous recommande de fournir des informations de contact de sécurité pour chacun de vos abonnements. Les informations de contact correspondent à une adresse électronique et à un numéro de téléphone. Elles seront utilisées pour vous contacter si notre équipe de sécurité détecte que vos ressources sont compromises. |

Vous pouvez filtrer et ignorer les recommandations.

1. Cliquez sur **Filtrer** dans le panneau **Recommandations**. Le panneau **Filtrer** s’ouvre et vous permet d’afficher uniquement certains états ou niveaux de gravité.

    ![][3]

2. Si vous déterminez qu’une recommandation n’est pas applicable, vous pouvez l’ignorer pour la faire disparaître. Il existe deux façons d’ignorer une recommandation. L’une consiste à cliquer avec le bouton droit de la souris sur un élément, puis à sélectionner **Ignorer**. L’autre consiste à pointer sur un élément, à cliquer sur les trois points qui apparaissent à droite, puis à sélectionner **Ignorer**. Vous pouvez afficher les recommandations ignorées en cliquant sur **Filtrer**, puis en sélectionnant **Ignorées**.

    ![][4]

### Appliquer les recommandations
Après avoir examiné toutes les recommandations, vous pouvez décider d’en appliquer une en priorité. Les recommandations à appliquer en priorité sont celles dont le niveau de gravité est le plus élevé. Voyons comment appliquer une recommandation en prenant comme exemple la recommandation **Activer le logiciel anti-programme malveillant**.

1. Dans le panneau **Recommandations** , sélectionnez **Activer le logiciel anti-programme malveillant**. ![][5]

2. Dans le panneau **Installer le logiciel anti-programme malveillant**, sélectionnez les machines virtuelles pour lesquelles aucun logiciel anti-programme malveillant n’est activé, puis cliquez sur **Installer le logiciel anti-programme malveillant**.
3. Le panneau **Nouvelle ressource** s’ouvre et vous permet de sélectionner la solution anti-programme malveillant à utiliser. Sélectionnez **Microsoft Antimalware**.
4. Des informations supplémentaires sur la solution anti-programme malveillant s’affichent. Sélectionnez **Créer**.
5. Saisissez les paramètres de configuration nécessaires dans le panneau **Ajouter une extension**, puis sélectionnez **OK**. ![][6]

[Microsoft Antimalware](../azure-security-antimalware.md) est maintenant activé sur la machine virtuelle sélectionnée.


## Étapes suivantes
Ce document vous a présenté les recommandations de sécurité du Centre de sécurité. Pour plus d’informations sur le Centre de sécurité, consultez les rubriques suivantes :

- [Définition des stratégies de sécurité dans Azure Security Center](security-center-policies.md) -- Découvrez comment configurer des stratégies de sécurité pour vos groupes de ressources et abonnements Azure.
- [Surveillance de l’intégrité de la sécurité dans Azure Security Center](security-center-monitoring.md) -- Découvrez comment surveiller l’intégrité de vos ressources Azure.
- [Gestion et résolution des alertes de sécurité dans Azure Security Center](security-center-managing-and-responding-alerts.md) -- Découvrez comment gérer et résoudre les alertes de sécurité.
- [Surveillance des solutions de partenaires avec Azure Security Center](security-center-partner-solutions.md) -- Découvrez comment surveiller l’état d’intégrité de vos solutions partenaires.
- [FAQ Azure Security Center](security-center-faq.md) -- Consultez la FAQ concernant l’utilisation de ce service.
- [Blog sur la sécurité Azure](http://blogs.msdn.com/b/azuresecurity/) -- Lisez des billets de blog sur la sécurité et la conformité Azure.

<!--Image references-->
[2]: ./media/security-center-recommendations/recommendations-tile.png
[3]: ./media/security-center-recommendations/filter-recommendations.png
[4]: ./media/security-center-recommendations/dismiss-recommendations.png
[5]: ./media/security-center-recommendations/select-enable-antimalware.png
[6]: ./media/security-center-recommendations/install-antimalware.png

<!---HONumber=AcomDC_0504_2016-->