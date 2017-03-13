---
title: "Meilleures pratiques de sécurité pour les charges de travail IaaS dans Azure | Microsoft Docs"
description: " La migration des charges de travail vers Azure IaaS nous offre l’occasion de réévaluer nos conceptionss "
services: security
documentationcenter: na
author: barclayn
manager: MBaldwin
editor: TomSh
ms.assetid: 02c5b7d2-a77f-4e7f-9a1e-40247c57e7e2
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/06/2017
ms.author: barclayn
translationtype: Human Translation
ms.sourcegitcommit: 094729399070a64abc1aa05a9f585a0782142cbf
ms.openlocfilehash: 774feff39bee6f34b1fb292f130d8240ec070c81
ms.lasthandoff: 03/07/2017




---

# <a name="security-best-practices-for-iaas-workloads-in-azure"></a>Meilleures pratiques de sécurité pour les charges de travail IaaS dans Azure

Depuis que vous avez commencé à réfléchir au déplacement des charges de travail vers Azure IaaS, vous vous êtes probablement rendu compte que certaines considérations vous sont familières. Vous disposez peut-être déjà d’une expérience de sécurisation d’environnements virtuels. La migration vers Azure IaaS vous permet d’appliquer votre expertise dans la sécurisation des environnements virtuels et offre également de nouvelles options pour vous aider à sécuriser vos ressources.

Avant de nous lancer, commençons par mentionner qu’il ne faut pas s’attendre à transposer les ressources locales telles quelles dans Azure. Les nouveaux défis et les nouvelles options donnent l’occasion de réévaluer les conceptions, les outils et les processus existants.




![Domaines de responsabilité](./media/azure-security-iaas/sec-cloudstack-new.png)

>[!NOTE]
> Votre responsabilité en matière de sécurité dépend du type de service cloud. Le graphique ci-dessus résume le partage de la responsabilité entre Microsoft et vous.

## <a name="best-practices"></a>Meilleures pratiques
Nous aborderons certaines des options disponibles dans Azure qui pourraient vous aider à répondre aux exigences de sécurité de votre organisation. Ce faisant, nous devons garder à l’esprit les différents types de charges de travail et les variations des exigences de sécurité associées. Aucune de ces meilleures pratiques ne peut à elle seule sécuriser vos systèmes. Comme toujours en matière de sécurité, vous devez choisir les options appropriées et voir comment les solutions peuvent se compléter mutuellement en remplissant les espaces laissés vides par les autres.

### <a name="use-privileged-access-workstations-paw"></a>Utiliser des stations de travail d’accès privilégié (PAW)

Les entreprises sont souvent victimes de cyberattaques dues au fait que les administrateurs effectuent des actions en utilisant des comptes avec des droits élevés. Généralement, leur intention n’est pas de nuire mais, simplement, la configuration et les processus existants les autorisent à le faire. La plupart de ces utilisateurs comprennent les risques d’un point de vue conceptuel, mais choisissent quand même d’effectuer des actions qu’ils considèrent comme risquées.

Des opérations comme consulter sa messagerie électronique et naviguer sur Internet semblent innocentes, mais elles peuvent exposer les comptes disposant de privilèges élevés à des acteurs malveillants susceptibles d’utiliser les activités de navigation, les courriers électroniques spécialement conçus ou d’autres techniques pour accéder à votre entreprise. L’utilisation de stations de gestion sécurisées pour effectuer toutes les tâches d’administration Azure est hautement recommandée pour réduire le risque de compromission accidentelle.

Les stations de travail d’accès privilégié (PAW) fournissent un système d’exploitation dédié aux tâches sensibles et protégé contre les attaques Internet et les vecteurs de menaces. La séparation de ces tâches et comptes sensibles et de ces stations de travail et appareils utilisés au quotidien fournit une protection très élevée contre les attaques par hameçonnage, les vulnérabilités du système d’exploitation et des applications, différentes attaques par emprunt d’identité et les vols d’informations d’identification tels que les enregistreurs de frappe et les attaques de type « Pass the hash » et « Pass the ticket ».

L’approche PAW est une extension de la pratique recommandée bien établie qui consiste à utiliser des comptes d’utilisateur et d’administrateur distincts pour le personnel d’administration. Cette pratique utilise un compte d’administration affecté à titre individuel et distinct du compte standard de l’utilisateur. PAW s’appuie sur cette pratique de séparation des comptes en fournissant une station de travail digne de confiance pour ces comptes sensibles.

Pour plus d’informations sur les stations de travail d’accès privilégié et pour obtenir de l’aide sur l’implémentation de PAW, suivez ce lien :

- [Stations de travail d’accès privilégié](https://technet.microsoft.com/windows-server-docs/security/securing-privileged-access/privileged-access-workstations)

### <a name="use-multifactor-authentication"></a>Utiliser l’authentification multifacteur

Dans le passé, votre périmètre réseau était utilisé pour contrôler l’accès aux données d’entreprise. Dans un monde axé sur le cloud et le mobile, l’identité est le plan de contrôle : elle permet de contrôler l’accès aux services IaaS à partir de n’importe quel appareil, et d’obtenir une visibilité et des recommandations analytiques sur la façon dont vos données sont utilisées et leur emplacement. La protection de l’identité numérique de vos utilisateurs Azure est la pierre angulaire de la protection de vos abonnements contre l’usurpation d’identité et d’autres cybercrimes.

L’une des mesures les plus intéressantes que vous pouvez prendre pour sécuriser un compte consiste à activer l’authentification à deux facteurs. L’authentification à deux facteurs est une méthode d’authentification qui va au-delà du simple mot de passe. Le second facteur vient s’ajouter au mot de passe.  Cela permet d’atténuer le risque d’accès par une personne qui serait parvenue à obtenir le mot de passe de quelqu’un d’autre.

Azure Multi-Factor Authentication contribue à sécuriser l'accès aux données et aux applications tout en répondant à la demande de l'utilisateur d'un processus d'authentification simple. Il assure une authentification forte avec une gamme d’options de vérification simples (appel téléphonique, SMS ou notification d’application mobile) qui permet aux utilisateurs de choisir leur méthode préférée.

Le moyen le plus simple d’utiliser [Azure MFA](../multi-factor-authentication/multi-factor-authentication.md) est l’application mobile Microsoft Authenticator, utilisable sur des appareils mobiles sous Windows, iOS et Android. Avec la dernière version de Windows 10 et votre instance Active Directory locale intégrée à Azure AD, [Microsoft Hello Entreprise](../active-directory/active-directory-azureadjoin-passport-deployment.md) peut être utilisé pour une authentification unique transparente auprès des ressources Azure. Dans ce cas, l’appareil Windows 10 sert de second facteur d’authentification.


Dans le cas d’Azure, le plus simple pour activer l’authentification à deux facteurs consiste à utiliser Azure Multifactor Authentication (MFA). [Azure MFA](../multi-factor-authentication/multi-factor-authentication.md) dispose d’une application qui peut être utilisée sur vos appareils mobiles, au moyen d’appels téléphoniques, de SMS ou d’un code généré dans l’application ; elle peut s’intégrer à votre annuaire local.

Pour les comptes qui gèrent votre abonnement Azure, vous devez utiliser MFA ; pour les comptes qui peuvent se connecter aux Machines Virtuelles, vous devez dans la mesure du possible utiliser MFA. Utiliser MFA pour ces comptes vous assure un niveau de sécurité bien supérieur à celui d’un simple mot de passe. D’autres formes d’authentification à deux facteurs pourraient fonctionner tout aussi bien, mais elles risquent d’être plus complexes à déployer si elles ne sont pas déjà en production.

La capture d’écran ci-dessous montre quelques-unes des options disponibles pour l’authentification Azure MFA.

![Options MFA](./media/azure-security-iaas/mfa-options.png)

### <a name="limit-and-constrain-administrative-access"></a>Limiter et contraindre l’accès administrateur

Il est extrêmement important de sécuriser les comptes qui peuvent gérer votre abonnement Azure. La compromission de ces comptes réduit à néant toutes les autres mesures prises pour garantir la confidentialité et l’intégrité de vos données. Comme l’a récemment illustré la fuite d’informations confidentielles [d’Edward Snowden](https://en.wikipedia.org/wiki/Edward_Snowden), les attaques internes représentent une menace énorme pour la sécurité globale d’une organisation.

Les personnes qui ont des droits d’administration doivent être évaluées selon des critères de ce type :

- Effectue-t-elle des tâches nécessitant des privilèges administratifs ?
- À quelle fréquence ces tâches sont-elles réalisées ?
- Raison pour laquelle ces tâches ne peuvent pas être effectuées par un autre administrateur en son nom.
- Documentez toutes les autres approches connues d’octroi du privilège et les raisons pour lesquelles elles ne sont pas acceptables.

L’utilisation de l’administration juste-à-temps permet d’éviter l’existence inutile de comptes avec des droits élevés pendant les périodes où ces droits ne sont pas nécessaires. Les comptes disposent de droits élevés pour une durée limitée, ce qui permet aux administrateurs d’effectuer leurs travaux ; ces droits sont supprimés à la fin de leur shift ou de la tâche.

[PIM](../active-directory/active-directory-privileged-identity-management-configure.md) vous permet de gérer, d’analyser et de contrôler l’accès au sein de votre organisation. Ce mode de gestion vous aide à rester informé des actions effectuées par des employés de votre organisation et intègre l’administration juste-à-temps à Azure AD en introduisant le concept d’administrateur éligible. Il s’agit des personnes disposant de comptes auxquels des droits d’administrateur peuvent être accordés. Ces types d’utilisateurs peuvent suivre un processus d’activation et se voir accorder des droits d’administrateur pour une durée limitée.


### <a name="use-devtest-labs"></a>Utiliser DevTest Labs

Utiliser Azure pour les environnements de développement et les labos permet aux organisations de gagner en agilité de test et de développement en éliminant les retards dus à l’approvisionnement en matériel. Malheureusement, il existe un risque que l’administrateur soit trop permissif avec l’affectation des droits en raison d’un manque de connaissance d’Azure ou de la volonté d’accélérer son adoption. Cela peut exposer involontairement l’organisation à des attaques internes. Certains utilisateurs pourraient disposer de droits d’accès bien supérieurs à ce qu’ils devraient avoir.

Azure comprend maintenant un service appelé [DevTest Labs](../devtest-lab/devtest-lab-overview.md). DevTest Labs utilise le [contrôle d’accès en fonction du rôle (RBAC) Azure](../active-directory/role-based-access-control-what-is.md). RBAC vous permet de séparer les responsabilités au sein de votre équipe au sein de rôles qui accordent uniquement le niveau d’accès nécessaire pour permettre aux utilisateurs d’effectuer leurs travaux. Il est fourni avec des rôles prédéfinis (propriétaire, utilisateur de labo et collaborateur). Ces rôles peuvent même être utilisés pour affecter des droits à des partenaires externes et simplifier considérablement la collaboration.

Étant donné que DevTest Labs utilise RBAC, il est possible de créer d’autres [rôles personnalisés](../devtest-lab/devtest-lab-grant-user-permissions-to-specific-lab-policies.md). DevTest Labs simplifie non seulement la gestion des autorisations, mais aussi le processus d’approvisionnement des environnements ; il permet également de faire face à d’autres défis courants que rencontrent les équipes qui travaillent sur des environnements de développement et de test. Une certaine préparation s’impose mais, à long terme, cela rendra les choses plus faciles pour votre équipe.

Voici les principales fonctionnalités d’Azure DevTest Labs :

- Contrôle administratif sur les options accessibles aux utilisateurs (tailles de machines virtuelles autorisées, nombre maximal de machines virtuelles, démarrage et arrêt des machines virtuelles, etc.), gérées de façon centralisée par l’administrateur
- Automatisation de la création de l’environnement lab
- Suivi des coûts
- Distribution simplifiée des machines virtuelles pour un travail collaboratif temporaire
- Libre-service permettant aux utilisateurs d’approvisionner leur labo à l’aide de modèles
- Gestion et limitation de la consommation

![Créer un DevTest Lab](./media/azure-security-iaas/devtestlabs.png)

Aucun coût supplémentaire n’est associé à l’utilisation de DevTest Labs. La création de labos, la configuration des stratégies, les modèles et les artefacts sont gratuits. Vous payez uniquement les ressources Azure utilisées dans vos labos, telles que les machines virtuelles, les comptes de stockage et les réseaux virtuels.



### <a name="control-and-limit-endpoint-access"></a>Contrôler et limiter l’accès aux points de terminaison

Pour que vous puissiez héberger des labos ou des systèmes de production dans Azure, vos systèmes doivent être accessibles sur Internet. Pour une nouvelle machine virtuelle Windows, le port RDP est par défaut accessible à partir d’Internet ; pour une machine virtuelle Linux, le port SSH est ouvert. Cela signifie qu’il est nécessaire de prendre des mesures pour « limiter les points de terminaison exposés » afin de réduire le risque d’accès non autorisé.

Certaines technologies dans Azure peuvent vous aider à limiter l’accès à ces points de terminaison d’administration. Vous pouvez notamment utiliser les groupes de sécurité réseau ([NSG](../virtual-network/virtual-networks-nsg.md)) dans Azure. Lorsque vous utilisez Resource Manager pour le déploiement, les NSG sont utilisés dans le but de limiter l’accès provenant de tous les réseaux aux seuls points de terminaison de gestion (RDP ou SSH).  Lorsque vous pensez NSG, pensez listes de contrôle d'accès (ACL) du routeur. Vous pouvez les utiliser pour contrôler étroitement les communications réseau entre les différents segments de vos réseaux Azure. Le processus est similaire à la création de réseaux dans des zones DMZ ou d’autres réseaux isolés. Ils n’inspectent pas le trafic, mais faciliteront la segmentation du réseau.


Dans Azure, vous pouvez configurer un [VPN de site à site](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) à partir de votre réseau local, pour étendre votre réseau local au cloud.  C’est une autre occasion d’utiliser des NSG, car vous pouvez également modifier les NSG de sorte qu’ils n’autorisent aucun accès extérieur au réseau local, puis exiger que l’administration commence par la connexion au réseau Azure via VPN.

L’option de VPN de site à site peut être très intéressante dans les cas où vous hébergez des systèmes de production étroitement intégrés à vos ressources locales dans Azure.

Il existe également l’option [de point à site](../vpn-gateway/vpn-gateway-howto-point-to-site-rm-ps.md), qui peut être utilisée dans les situations où vous souhaitez gérer des systèmes qui n’ont pas besoin d’accéder aux ressources locales. Ces systèmes peuvent être isolés dans leur propre Réseau virtuel Azure ; les administrateurs peuvent alors se connecter par VPN à l’environnement hébergé par Azure à partir de leur station d’administration.

>[!NOTE]
Les deux options de VPN vous permettent de reconfigurer les ACL sur les NSG de façon à interdire l’accès aux points de terminaison de gestion à partir d’Internet.

Une autre option intéressante consiste à envisager un déploiement de type [Passerelle des services Bureau à distance](../multi-factor-authentication/multi-factor-authentication-get-started-server-rdg.md). Il vous permet de vous connecter en toute sécurité via le protocole HTTPS aux serveurs Bureau à distance tout en appliquant des contrôles plus granulaires à ces connexions.

Voici quelques-unes des fonctionnalités auxquelles vous auriez accès :

- Options d’administration pour limiter les connexions aux demandes provenant de systèmes spécifiques
- Authentification par carte à puce ou Azure MFA
- Contrôle des systèmes auxquels chacun peut se connecter via la passerelle
- Contrôle de la redirection des appareils et des disques

### <a name="use-a-key-management-solution"></a>Utiliser une solution de gestion des clés

Une gestion sécurisée des clés est primordiale pour la protection des données du cloud. Avec [Azure Key Vault](../key-vault/key-vault-whatis.md), vous pouvez stocker en toute sécurité des clés de chiffrement et des secrets (tels que les mots de passe) dans des modules de sécurité matériels (HSM). Pour une meilleure garantie, vous pouvez importer ou générer des clés HSM.

Si vous optez pour cette option, Microsoft traite vos clés dans un système de sécurité HSM valide FIPS 140-2 de niveau 2 validé HSM (matériel et microprogramme). Surveillez et auditez l’utilisation des clés avec la journalisation Azure : mettez en place des journaux dans Azure ou votre logiciel SIEM (Security Information and Event Management) pour bénéficier d’une analyse et d’une détection des menaces supplémentaires.

Toute personne disposant d’un abonnement Azure peut créer et utiliser des coffres de clés. Bien que Key Vault procure des avantages aux développeurs et aux administrateurs de sécurité, il peut être implémenté et géré par l’administrateur d’une organisation responsable de la gestion des services Azure.


### <a name="encrypt-virtual-disks-and-disk-storage"></a>Chiffrer les disques virtuels et le stockage des disques

[Azure Disk Encryption](https://gallery.technet.microsoft.com/Azure-Disk-Encryption-for-a0018eb0) élimine le risque de vol de données ou d’exposition à des accès non autorisés par le biais du déplacement d’un disque. Le disque peut être attaché à un autre système dans le but de contourner d’autres contrôles de sécurité. Disk Encryption utilise [BitLocker](https://technet.microsoft.com/library/hh831713 ) sous Windows et DM-Crypt sous Linux pour chiffrer le système d’exploitation et les lecteurs de données. Azure Disk Encryption s’intègre avec Key Vault pour contrôler et gérer les clés de chiffrement ; il est disponible pour les machines virtuelles Standard et les machines virtuelles avec Stockage Premium.

Pour plus d’informations, consultez l’article sur [Azure Disk Encryption dans les machines virtuelles IaaS Windows et Linux](azure-security-disk-encryption.md).

Le [chiffrement des services de stockage Azure](../storage/storage-service-encryption.md) protège vos données au repos. Activé au niveau du compte de stockage, il chiffre les données au fur et à mesure de leur écriture dans nos centres de données ; elles sont déchiffrées automatiquement lorsque vous y accédez. Cette méthode prend en charge les scénarios suivants :

- Chiffrement des objets blob de blocs, des objets blob d’ajout et des objets blob de pages.
- Chiffrement des modèles et des disques durs virtuels archivés dans Azure depuis un emplacement local.
- Chiffrement du système d’exploitation et des disques de données sous-jacents pour les machines virtuelles IaaS créées à l’aide de vos disques durs virtuels.

Avant de passer au chiffrement du Stockage Azure, vous devez avoir connaissance de deux limitations importantes :

- Il n’est pas disponible sur les comptes de stockage Classic.
- Il ne crypte les données écrites qu’une fois le chiffrement activé.

### <a name="use-a-centralized-security-management-system"></a>Utiliser un système de gestion centralisée de la sécurité

Les serveurs doivent être surveillés lors des mises à jour correctives, de la configuration, des événements et des activités qui peuvent être considérées comme des problèmes de sécurité. Pour résoudre ces problèmes, vous pouvez utiliser [Security Center](https://azure.microsoft.com/services/security-center/) et [Sécurité et conformité Operations Management Suite](https://azure.microsoft.com/services/security-center/). Ces deux options vont au-delà de la configuration au sein du système d’exploitation et fournissent également une analyse de la configuration de l’infrastructure sous-jacente, comme la configuration du réseau et l’utilisation des appliances virtuelles.

### <a name="operating-system-management-best-practices"></a>Meilleures pratiques de gestion du système d’exploitation

Dans un déploiement IaaS, vous êtes toujours responsable de la gestion des systèmes que vous déployez, comme tout autre serveur ou station de travail de votre environnement. Cela signifie que la mise à jour corrective, la sécurisation renforcée, l’affectation des droits et les autres activités relatives à la maintenance de votre système restent sous votre responsabilité.  Pour les systèmes étroitement intégrés avec vos ressources locales, il peut être intéressant d’utiliser les même outils et procédures qu’en local pour les antivirus, les logiciels anti-programme malveillant, les mises à jour correctives et les sauvegardes notamment.

**Sécurisation renforcée** Toutes les machines virtuelles dans Azure IaaS doivent faire l’objet d’une sécurisation renforcée afin qu’elles n’exposent que les points de terminaison de service requis pour les applications installées. Pour les machines virtuelles Windows, suivez les recommandations publiées par Microsoft sous forme de lignes de base pour la solution de Security Compliance Manager.

[Security Compliance Manager](https://technet.microsoft.com/solutionaccelerators/cc835245.aspx) : nous avons récemment publié la version 4.0. Il s’agit d’un outil gratuit qui vous permet de configurer et de gérer rapidement vos postes de travail, votre centre de données traditionnel et votre cloud public et privé à l’aide de la stratégie de groupe et de System Center Configuration Manager.

SCM fournit des stratégies prêtes au déploiement et des packs de configuration DCM testés. Ces lignes de base s’appuient sur les recommandations du [guide Microsoft Security](https://technet.microsoft.com/en-us/library/cc184906.aspx) et sur les meilleures pratiques du secteur ; elles vous permettent de gérer les dérives de configuration, de répondre aux exigences de conformité et de réduire les menaces de sécurité.

Vous pouvez tirer parti de SCM pour importer la configuration actuelle de vos ordinateurs suivant deux méthodes différentes : tout d’abord, vous pouvez importer des stratégies de groupe Active Directory ; deuxièmement, vous pouvez importer la configuration d’une machine de référence « golden master » en utilisant [l’outil LocalGPO](https://blogs.technet.microsoft.com/secguide/2016/01/21/lgpo-exe-local-group-policy-object-utility-v1-0/) pour sauvegarder la stratégie de groupe locale que vous pouvez ensuite importer dans SCM.

Comparez vos standards aux meilleures pratiques du secteur, personnalisez-les et créez de nouvelles stratégies et de nouveaux packs de configuration DCM.
Les lignes de base ont été publiées pour tous les systèmes d’exploitation pris en charge, y compris la Mise à jour anniversaire Windows 10 et Windows Server 2016.


**Installer et gérer des logiciels anti-programme malveillant**

Pour les environnements hébergés séparément de votre environnement de production, il existe une extension anti-programme malveillant qui peut servir à protéger vos machines virtuelles et services cloud et qui s’intègre à [Azure Security Center](../security-center/security-center-intro.md).


Le [logiciel anti-programme malveillant Microsoft](azure-security-antimalware.md) inclut des fonctionnalités telles que la protection en temps réel, l’analyse planifiée, la correction des logiciels malveillants, la mise à jour des signatures, la mise à jour des moteurs, les rapports d’exemples, la collecte d’événements d’exclusion et la [prise en charge de PowerShell](https://msdn.microsoft.com/library/dn771715.aspx).

![Logiciel anti-programme malveillant Azure](./media/azure-security-iaas/azantimalware.png)

**Installer les dernières mises à jour de sécurité** Parmi les premières charges de travail que nos clients déplacent vers Azure figurent les labos et les systèmes accessibles de l’extérieur. Si vous hébergez dans Azure des machines virtuelles exécutant des applications ou des services qui doivent être accessibles par Internet, vous devez être vigilant sur les mises à jour correctives. N’oubliez pas que cela va au-delà de la mise à jour corrective du système d’exploitation. Des vulnérabilités non corrigées sur des applications tierces peuvent également provoquer des problèmes qui auraient pu être facilement évités si une gestion efficace des correctifs avait été mise en place.

**Déployer et tester une solution de sauvegarde**

Tout comme les mises à jour de sécurité, la sauvegarde doit être gérée de la même façon que les autres opérations. Cela s’applique aux systèmes qui font partie de votre environnement de production étendu au cloud. Les systèmes de développement et de test doivent suivre des stratégies de sauvegarde capables de fournir des capacités de restauration similaires à ce à quoi les utilisateurs se sont habitués au cours de leur expérience avec des environnements sur site.

Les charges de travail de production déplacées vers Azure doivent s’intégrer avec les solutions de sauvegarde existantes dans la mesure du possible ; sinon, vous pouvez utiliser la [Sauvegarde Azure](../backup/backup-azure-arm-vms.md) pour répondre à vos besoins de sauvegarde.


### <a name="monitor"></a>Surveiller

[Security Center](../security-center/security-center-intro.md) évalue en continu l’état de sécurité de vos ressources Azure pour identifier les vulnérabilités potentielles. Une liste de recommandations vous guide tout au long du processus de configuration des contrôles nécessaires.

Voici quelques exemples :

- Approvisionnement d’un logiciel anti-programme malveillant pour identifier et supprimer les programmes malveillants
- Configuration de groupes de sécurité réseau et de règles associées pour contrôler le trafic vers les machines virtuelles
- Approvisionnement de pare-feu d’applications web pour protéger vos applications web contre les attaques ciblées
- Déploiement de mises à jour système manquantes
- Correction des configurations de système d’exploitation qui ne suivent pas les recommandations

L’image ci-dessous vous montre quelques-unes des options disponibles à l’activation dans Security Center.

![Stratégies Azure Security Center](./media/azure-security-iaas/security-center-policies.png)

 [Operations Management Suite](../operations-management-suite/operations-management-suite-overview.md) est une solution de gestion informatique basée sur le cloud de Microsoft qui vous permet de gérer et de protéger votre infrastructure locale et dans le cloud. La solution OMS étant implémentée sous la forme d’un service cloud, elle peut être déployée rapidement et avec un investissement minimal en ressources d’infrastructure.

Les nouvelles fonctionnalités sont fournies automatiquement, ce qui vous permet d’économiser sur les coûts de mise à niveau et de maintenance. De plus, elle s’intègre avec System Center Operations Manager.  OMS comprend différents composants qui vous aident à mieux gérer vos charges de travail Azure, notamment un module [Sécurité et conformité](../operations-management-suite/oms-security-getting-started.md).

Les fonctionnalités de sécurité et de conformité d’OMS permettent d’afficher des informations sur vos ressources en quatre catégories principales :

- Domaines de sécurité : dans cette section, vous pourrez explorer plus en détail les enregistrements de sécurité au fil du temps, accéder à l’évaluation des programmes malveillants, à l’évaluation des mises à jour, à la sécurité du réseau, aux informations d’identité et d’accès et aux ordinateurs présentant des événements de sécurité, et accéder rapidement au tableau de bord d’Azure Security Center.
- Problèmes importants : cette option vous permet d’identifier rapidement le nombre de problèmes actifs et leur gravité.
- Détections (version préliminaire) : cette option vous permet d’identifier les modèles d’attaque en visualisant les alertes de sécurité au fur et à mesure qu’elles affectent vos ressources.
- Informations sur les menaces : cette option vous permet d’identifier les modèles d’attaques en visualisant le nombre total de serveurs présentant un trafic IP sortant malveillant, le type de menace malveillante et une carte indiquant l’origine de ces adresses IP.
- Requêtes de sécurité courantes : cette option vous fournit une liste des requêtes de sécurité les plus courantes que vous pouvez utiliser pour surveiller votre environnement. Cliquez sur l’une de ces requêtes pour ouvrir le panneau Recherche affichant les résultats de cette requête.

La capture d’écran ci-dessous montre un exemple du type d’informations qui peuvent être affichées par OMS.

![Lignes de base de la sécurité OMS](./media/azure-security-iaas/oms-security-baseline.png)



## <a name="next-steps"></a>Étapes suivantes


* [Blog de l’équipe de sécurité Azure](https://blogs.msdn.microsoft.com/azuresecurity/)
* [Centre de réponse aux problèmes de sécurité Microsoft](https://technet.microsoft.com/library/dn440717.aspx)
* [Meilleures pratiques et tendances Azure relatives à la sécurité](security-best-practices-and-patterns.md)

