<properties
   pageTitle="Présentation de la gestion et surveillance de la sécurité Azure | Microsoft Azure"
   description="Azure propose divers mécanismes de sécurité pour favoriser la gestion et surveillance des services cloud et des machines virtuelles Azure. Cet article fournit une vue d’ensemble de ces fonctionnalités et services clés de sécurité."
   services="security"
   documentationCenter="na"
   authors="TerryLanfear"
   manager="StevenPo"
   editor="TomSh"/>

<tags
   ms.service="security"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="05/19/2016"
   ms.author="terrylan"/>

# Présentation de la gestion et surveillance de la sécurité Azure

Azure propose divers mécanismes de sécurité pour favoriser la gestion et surveillance des services cloud et des machines virtuelles Azure. Cet article fournit une vue d’ensemble de ces fonctionnalités et services clés de sécurité. Les liens renvoient à des articles qui fournissent des informations détaillées complémentaires sur chaque fonctionnalité ou service.

La sécurité de vos services Microsoft Cloud est une collaboration et une responsabilité partagée entre vous et Microsoft. Une responsabilité partagée signifie que Microsoft est responsable de Microsoft Azure et de l’intégrité physique de ses centres de données (grâce à l’utilisation de mesures de protection telles que des portes sécurisées au moyen de badges, des clôtures et des gardes). En outre, Azure fournit des niveaux élevés de sécurité du cloud sur la couche logicielle, répondant aux attentes exigeantes de ses clients en matière de sécurité, de confidentialité et de conformité.

Vos données et identités vous appartiennent, ainsi que la responsabilité de les protéger, la sécurité de vos ressources locales et celle des composants cloud que vous contrôlez. Microsoft vous fournit des fonctionnalités et des contrôles de sécurité pour vous aider à protéger vos données et applications. Votre responsabilité en matière de sécurité dépend du type de service cloud.

Le graphique suivant résume le partage de la responsabilité entre Microsoft et le client.

![Responsabilité partagée][1]

Pour aller plus loin sur la gestion de la sécurité, consultez [Gestion de la sécurité dans Azure](../azure-security-management.md).

Voici les principales fonctionnalités abordées dans cet article :

- Contrôle d’accès en fonction du rôle
- Logiciel anti-programme malveillant
- Authentification multifacteur
- ExpressRoute
- Passerelles de réseau virtuel
- Privileged Identity Management
- Identity Protection
- Centre de sécurité

## Contrôle d’accès en fonction du rôle

Le contrôle d’accès en fonction du rôle (RBAC) Azure offre une gestion précise de l’accès pour les ressources Azure. L’utilisation du RBAC vous permet de n’accorder aux utilisateurs que les droits d’accès dont ils ont besoin pour effectuer leur travail. Le RBAC peut également vous permettre de vous assurer que les utilisateurs perdent l’accès aux ressources dans le cloud lorsqu’ils quittent l’entreprise.

En savoir plus :

- [Blog de l’équipe Active Directory sur le RBAC](http://i1.blogs.technet.com/b/ad/archive/2015/10/12/azure-rbac-is-ga.aspx)
- [Contrôle d’accès en fonction du rôle Azure](../active-directory/role-based-access-control-configure.md)

## Logiciel anti-programme malveillant

Azure met à votre disposition des logiciels anti-programmes malveillants provenant de fournisseurs de sécurité reconnus tels que Microsoft, Symantec, Trend Micro, McAfee et Kaspersky. Ceux-ci permettent de protéger vos machines virtuelles contre les fichiers malveillants, les logiciels de publicité et d’autres menaces.

Microsoft Antimalware vous offre la possibilité d’installer un agent anti-programmes malveillants pour les rôles PaaS et les machines virtuelles. Basée sur System Center Endpoint Protection, cette fonctionnalité offre une technologie de sécurité locale éprouvée sur le cloud.

Nous offrons également une intégration approfondie de produits Trend [Deep Security](http://www.trendmicro.com/us/enterprise/cloud-solutions/deep-security/)™ et [SecureCloud](http://www.trendmicro.com/us/enterprise/cloud-solutions/secure-cloud/)™ dans la plateforme Azure. DeepSecurity est une solution Antivirus et SecureCloud une solution de chiffrement. DeepSecurity sera déployé sur des machines virtuelles à l’aide d’un modèle d’extension. À l’aide de l’interface utilisateur du portail et de PowerShell, vous pouvez choisir d’utiliser DeepSecurity à l’intérieur de nouvelles machines virtuelles en cours de lancement, ou de machines virtuelles existantes déjà déployées.

Symantec Endpoint Protection (SEP) est également pris en charge sur Azure. Grâce à l’intégration du portail, les clients ont la possibilité d’indiquer qu’ils souhaitent utiliser SEP au sein d’une machine virtuelle. SEP peut être installé sur une toute nouvelle machine virtuelle via le portail Azure, ou sur une machine virtuelle existante à l’aide de PowerShell.

En savoir plus :

- [Déploiement de solutions anti-programmes malveillants sur des machines virtuelles Azure (en anglais)](https://azure.microsoft.com/blog/deploying-antimalware-solutions-on-azure-virtual-machines/)
- [Microsoft Antimalware pour Azure Cloud Services et les machines virtuelles](../azure-security-antimalware.md)
- [Installation et configuration de Trend Micro Deep Security comme service sur une machine virtuelle Windows](../virtual-machines/virtual-machines-windows-classic-install-trend.md)
- [Installation et configuration de Symantec Endpoint Protection sur une machine virtuelle Windows](../virtual-machines/virtual-machines-windows-classic-install-symantec.md)
- [Nouvelles options anti-programmes malveillants pour protéger les machines virtuelles – McAfee Endpoint Protection](https://azure.microsoft.com/blog/new-antimalware-options-for-protecting-azure-virtual-machines/)

## Authentification multifacteur

Azure Multi-Factor Authentication (MFA) est une méthode d’authentification qui nécessite l’utilisation de plusieurs méthodes de vérification et ajoute une deuxième couche critique de sécurité aux connexions et transactions des utilisateurs. MFA contribue à sécuriser l’accès aux données et aux applications tout en répondant à la demande des utilisateurs souhaitant un processus d’authentification simple. Elle fournit une authentification forte via diverses options de vérification : appel téléphonique, message texte, notification par application mobile ou code de vérification et jetons OATH tiers.

En savoir plus :

- [Authentification multifacteur](https://azure.microsoft.com/documentation/services/multi-factor-authentication/)
- [Présentation d'Azure Multi-Factor Authentication](../multi-factor-authentication/multi-factor-authentication.md)
- [Azure Multi-Factor Authentication : fonctionnement](../multi-factor-authentication/multi-factor-authentication-how-it-works.md)

## ExpressRoute

Microsoft Azure ExpressRoute vous permet d'étendre vos réseaux locaux au cloud de Microsoft via une connexion privée dédiée assurée par un fournisseur de connectivité. Grâce à ExpressRoute, vous pouvez établir des connexions aux services de cloud computing Microsoft, comme Microsoft Azure, Office 365 et CRM Online. La connectivité peut provenir d'un réseau universel (IP VPN), d’un réseau Ethernet point à point ou d’une interconnexion virtuelle via un fournisseur de connectivité dans un centre de colocalisation. Les connexions ExpressRoute ne sont pas établies par le biais de l'Internet public. Elles offrent ainsi de meilleurs niveaux de fiabilité, de rapidité, de latence et de sécurité que les connexions classiques sur Internet.

En savoir plus :

- [Présentation technique d’ExpressRoute](../expressroute/expressroute-introduction.md)

## Passerelles de réseau virtuel

Les passerelles VPN, aussi appelées passerelles de réseau virtuel Azure, sont conçues pour faire circuler le trafic réseau entre les réseaux virtuels et les emplacements sur site. Elles sont également utilisées pour acheminer le trafic entre plusieurs réseaux virtuels dans Azure (connexion de réseau virtuel à réseau virtuel). Les passerelles VPN garantissent la sécurisation de la connectivité entre les locaux entre Azure et votre infrastructure.

En savoir plus :

- [À propos des passerelles VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md)
- [Vue d’ensemble de la sécurité du réseau Azure](security-network-overview.md)

## Privileged Identity Management

Les utilisateurs doivent parfois effectuer des opérations privilégiées dans des ressources Azure ou dans d’autres applications SaaS. Cela signifie souvent que les entreprises doivent leur donner un accès privilégié permanent à Azure Active Directory (AD). C’est un risque de sécurité croissant pour les ressources hébergées dans le cloud, car les entreprises ne peuvent pas suffisamment surveiller ce que ces utilisateurs font avec leur accès privilégié. En outre, si un compte d’utilisateur disposant d’un accès privilégié est compromis, cette seule faille peut affecter votre sécurité globale sur le cloud. Azure AD Privileged Identity Management permet de résoudre ce risque en réduisant le temps d’exposition des privilèges et en augmentant la visibilité quant à leur utilisation.

Privileged Identity Management introduit le concept d’un administrateur temporaire pour un rôle ou d’un accès administrateur immédiat, qui est un utilisateur devant terminer un processus d’activation pour ce rôle. Le processus d'activation permet d’activer l'affectation de l'utilisateur à un rôle dans Azure AD pendant une période spécifiée, par exemple 8 heures.

En savoir plus :

- [Azure AD Privileged Identity Management](../active-directory/active-directory-privileged-identity-management-configure.md)
- [Prise en main d’Azure AD Privileged Identity Management](../active-directory/active-directory-privileged-identity-management-getting-started.md)

## Identity Protection

Azure Active Directory (AD) Identity Protection fournit une vue consolidée des activités de connexion suspectes et des vulnérabilités potentielles pour vous aider à protéger votre entreprise. Identity Protection détecte les activités de connexion suspectes pour les utilisateurs et les identités privilégiées (administrateurs) en fonction de signaux tels que les attaques par force brute, les fuites d’informations d’identification et les connexions provenant d’emplacements inconnus et d’appareils infectés.

En fournissant des notifications et des mises à jour recommandées, Identity Protection vous permet de limiter les risques en temps réel. Il calcule la gravité des risques utilisateur, et vous pouvez configurer des stratégies basées sur les risques pour automatiquement contribuer à protéger l’accès à l’application contre les menaces futures.

En savoir plus :

- [Azure Active Directory Identity Protection](../active-directory/active-directory-identityprotection.md)
- [Channel 9 : Azure AD and Identity Show: Identity Protection Preview (Émission sur Azure AD et l’identité : présentation d’Identity Protection)](https://channel9.msdn.com/Series/Azure-AD-Identity/Azure-AD-and-Identity-Show-Identity-Protection-Preview)

## Centre de sécurité

Azure Security Center vous aide à prévenir, détecter et résoudre les menaces, en vous apportant une visibilité et un contrôle accrus de la sécurité de vos ressources Azure. Il fournit une surveillance de la sécurité et une gestion des stratégies intégrées pour l’ensemble de vos abonnements Azure, vous aidant ainsi à détecter les menaces qui pourraient passer inaperçues. De plus, il est compatible avec un vaste écosystème de solutions de sécurité.

Security Center vous permet d’optimiser et de surveiller la sécurité de vos ressources Azure grâce aux opérations suivantes :

- Il vous permet de définir des stratégies pour vos abonnements aux ressources Azure en fonction des exigences de sécurité de votre société et du type d’applications ou du niveau de confidentialité des données de chaque abonnement.
- Il analyse l’état de vos machines virtuelles, de votre réseau et de vos applications Azure.
- Il fournit une liste hiérarchisée d’alertes de sécurité, notamment les alertes provenant de solutions de partenaires intégrées, ainsi que les informations nécessaires pour trouver rapidement la cause d’une attaque et des recommandations sur la façon d’y remédier.

En savoir plus :

- [Présentation du Centre de sécurité Azure](../security-center/security-center-intro.md)

<!--Image references-->
[1]: ./media/security-management-and-monitoring-overview/shared-responsibility.png

<!---HONumber=AcomDC_0525_2016-->