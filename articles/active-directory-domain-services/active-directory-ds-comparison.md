<properties
	pageTitle="Services de domaine Azure AD : comparer les services de domaine Azure AD aux contrôleurs de domaine personnalisés | Microsoft Azure"
	description="Comparaison entre les services de domaine Azure AD et les contrôleurs de domaine personnalisés"
	services="active-directory-ds"
	documentationCenter=""
	authors="mahesh-unnikrishnan"
	manager="stevenpo"
	editor="curtand"/>

<tags
	ms.service="active-directory-ds"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/23/2016"
	ms.author="maheshu"/>

# Comment déterminer si les services de domaine Azure AD sont adaptés à votre cas d’utilisation ?
Les services de domaine Azure AD vous permettent de déployer vos charges de travail dans les services d’infrastructure Azure, sans avoir à vous soucier de maintenir votre infrastructure d’identité. Ce service géré est différent d’un déploiement Windows Server Active Directory standard que vous déployez et gérez par vous-même. Le service est conçu pour offrir la facilité de déploiement, le contrôle et la correction automatiques de l’intégrité, ainsi qu’une infrastructure d’identité simple pour le cloud. Le service est en constante évolution pour inclure la prise en charge de scénarios de déploiement courants.

Pour décider si vous pouvez utiliser les services de domaine Azure AD ou créer et gérer votre propre infrastructure AD (personnalisable) dans Azure :

- Consultez la liste des [fonctionnalités offertes par les services de domaine Azure AD](active-directory-ds-features.md).

- Consultez les [scénarios de déploiement courants pour les services de domaine Azure AD](active-directory-ds-scenarios.md).

- Enfin, [comparez les services de domaine Azure AD à une option AD personnalisable](active-directory-ds-comparison.md#compare-azure-ad-domain-services-to-diy-ad-domain-in-azure).


## Comparer les services de domaine Azure AD à un domaine AD personnalisé dans Azure
Le tableau suivant vous permet de choisir entre l’utilisation des services de domaine Azure AD et la gestion de votre propre infrastructure AD dans Azure.

|Fonctionnalité|Services de domaine Azure AD|Infrastructure AD personnalisée sur des machines virtuelles Azure|
|---|:---:|:---:|
|[**Service géré**](active-directory-ds-comparison.md#managed-service)|Oui|Non|
|[**Déploiements sécurisés**](active-directory-ds-comparison.md#secure-deployments)|Oui|L’administrateur doit sécuriser le déploiement.|
|[**Serveur DNS**](active-directory-ds-comparison.md#dns-server)|Oui (service géré)|Oui|
|[**Privilèges d’administrateur de domaine ou d’entreprise**](active-directory-ds-comparison.md#domain-or-enterprise-administrator-privileges)|Non|Oui|
|[**Jonction de domaine**](active-directory-ds-comparison.md#domain-join)|Oui|Oui|
|[**Authentification de domaine à l’aide de NTLM et Kerberos**](active-directory-ds-comparison.md#domain-authentication-using-ntlm-and-kerberos)|Oui|Oui|
|[**Structure d’unité d’organisation personnalisée**](active-directory-ds-comparison.md#custom-ou-structure)|Oui|Oui|
|[**Extensions de schéma**](active-directory-ds-comparison.md#schema-extensions)|Non|Oui|
|[**Approbations de domaine/forêt AD**](active-directory-ds-comparison.md#ad-domain-or-forest-trusts)|Non|Oui|
|[**Lecture LDAP**](active-directory-ds-comparison.md#ldap-read)|Oui|Oui|
|[**LDAP sécurisé (LDAPS)**](active-directory-ds-comparison.md#secure-ldap)|Oui|Oui|
|[**Écriture LDAP**](active-directory-ds-comparison.md#ldap-write)|Non|Oui|
|[**Stratégie de groupe**](active-directory-ds-comparison.md#group-policy)|Simple|Complet|
|[**Déploiements géographiquement dispersés**](active-directory-ds-comparison.md#geo-dispersed-deployments)|Non|Oui|

#### Service géré
Les domaines des services de domaine Azure AD sont gérés par Microsoft. Vous n’avez pas à vous soucier des correctifs, des mises à jour, de la surveillance, des sauvegardes et de la disponibilité de votre domaine. Ces tâches de gestion sont proposées en tant que service par Microsoft Azure pour vos domaines gérés.

#### Déploiements sécurisés
Le domaine géré est verrouillé de façon sécurisée, conformément aux meilleures pratiques de sécurité de Microsoft pour les déploiements AD. Ces meilleures pratiques sont issues des dizaines d’années d’expérience de l’équipe produit AD en matière d’ingénierie et de prise en charge des déploiements AD. Pour les déploiements personnalisés, vous devez prendre des mesures de déploiement spécifiques pour verrouiller/sécuriser votre déploiement.

#### Serveur DNS
Un domaine géré des services de domaine Azure AD inclut des services DNS gérés. Les membres du groupe « AAD DC Administrators » peuvent gérer DNS sur le domaine géré. Les membres de ce groupe bénéficient de privilèges d’administration DNS complets pour le domaine géré. La gestion du service DNS peut être effectuée à l’aide de la « console d’administration DNS » incluse dans le package des outils d’administration du serveur distant (RSAT).

#### Privilèges d’administrateur de domaine ou d’entreprise
Ces privilèges élevés ne sont pas proposés sur un domaine géré AAD-DS. Les applications dont l’installation/l’exécution nécessite ces privilèges élevés ne peuvent pas être exécutées sur des domaines gérés. Un sous-ensemble plus restreint de privilèges d’administration est disponible pour les membres du groupe d’administration déléguée appelé « AAD DC Administrators ». Ces privilèges incluent des autorisations pour configurer DNS, la stratégie de groupe, obtenir des privilèges d’administrateur sur les ordinateurs joints au domaine, etc.

#### Jonction de domaine
Vous pouvez joindre des machines virtuelles au domaine géré de la même manière que pour les ordinateurs joints à un domaine AD.

#### Authentification de domaine à l’aide de NTLM et Kerberos
Avec les services de domaine Azure AD, vous pouvez utiliser vos informations d’identification d’entreprise pour l’authentification sur le domaine géré. Les informations d’identification sont synchronisées avec votre client Azure AD. Pour les clients synchronisés, Azure AD Connect permet de s’assurer que les modifications apportées aux informations d’identification en local sont synchronisées avec Azure AD. Avec une configuration de domaine personnalisé, vous devrez peut-être configurer une relation d’approbation de domaine avec une forêt de comptes locaux pour permettre aux utilisateurs de s’authentifier avec leurs informations d’identification d’entreprise. Vous devrez peut-être également configurer la réplication AD pour vous assurer que les mots de passe des utilisateurs sont synchronisés avec les machines virtuelles du contrôleur de domaine Azure.

#### Structure d’unité d’organisation personnalisée
Les membres du groupe « AAD DC Administrators » peuvent créer des unités d’organisation personnalisées dans le domaine géré.

#### Extensions de schéma
Vous ne pouvez pas étendre le schéma de base d’un domaine géré des services de domaine Azure AD. Par conséquent, les applications qui reposent sur les extensions de schéma AD (par exemple, les nouveaux attributs dans l’objet utilisateur) ne peuvent pas déplacées vers des domaines AAD-DS.

#### Approbations de domaine ou de forêt AD
Les domaines gérés ne peuvent pas être configurés pour définir des relations d’approbation (entrant/sortant) avec d’autres domaines. Par conséquent, pour les scénarios tels que les déploiements de forêt de ressources ou dans les cas où vous préférez ne pas synchroniser les mots de passe avec Azure AD, il est impossible d’utiliser les services de domaine Azure AD.

#### Lecture LDAP
Le domaine géré prend en charge les charges de travail de lecture LDAP. Vous pouvez donc déployer des applications qui effectuent des opérations de lecture LDAP sur le domaine géré.

#### LDAP sécurisé
Vous pouvez configurer les services de domaine Azure AD pour fournir un accès LDAP sécurisé à votre domaine géré, notamment sur Internet.

#### Écriture LDAP
Le domaine géré est en lecture seule pour les objets utilisateur. Par conséquent, les applications qui effectuent des opérations d’écriture LDAP sur les attributs de l’objet utilisateur ne fonctionnent pas dans un domaine géré. En outre, les mots de passe utilisateur ne peuvent pas être modifiés à partir du domaine géré. Un autre exemple : la modification des appartenances aux groupes ou des attributs de groupe dans le domaine géré n’est pas autorisée. Toutefois, les modifications apportées aux mots de passe ou aux attributs utilisateur dans Azure AD (via PowerShell/portail Azure) ou dans Active Directory sur site sont synchronisées avec le domaine géré AAD-DS.

#### Stratégie de groupe
Les constructions de stratégie de groupe complexes ne sont pas prises en charge sur le domaine géré AAD-DS. Par exemple, vous ne pouvez pas créer et déployer des objets de stratégie de groupe distincts pour chaque unité d’organisation personnalisée dans le domaine ou utiliser le filtrage WMI pour le ciblage de la stratégie de groupe. Il existe un objet GPO intégré pour les conteneurs « AADDC Computers » et « AADDC Users », qui peut être personnalisé pour configurer la stratégie de groupe.

#### Déploiements géographiquement dispersés
Les domaines gérés des services de domaine Azure AD sont disponibles dans un seul réseau virtuel dans Azure. Pour les scénarios qui requièrent que des contrôleurs de domaine soient disponibles dans plusieurs régions Azure dans le monde, la configuration de contrôleurs de domaine sur des machines virtuelles IaaS Azure peut constituer la meilleure solution.


## Options de déploiement AD personnalisé
Vous disposez peut-être de cas d’utilisation de déploiement où vous avez besoin de certaines des fonctionnalités offertes par une installation Windows Server AD. Dans ces cas, envisagez l’une des options personnalisées suivantes :

- **Domaine cloud autonome :** vous pouvez configurer un domaine cloud autonome à l’aide de machines virtuelles Azure qui ont été configurées en tant que contrôleurs de domaine. Cette infrastructure ne s’intègre pas à votre environnement AD local. Cette option nécessite un ensemble différent d’informations d’identification cloud pour l’ouverture de session/la gestion des machines virtuelles sur le cloud.

- **Déploiement de forêt de ressources :** vous pouvez configurer un domaine dans la topologie de forêt de ressources à l’aide de machines virtuelles Azure configurées en tant que contrôleurs de domaine. Ensuite, vous pouvez configurer une relation d’approbation AD avec votre environnement AD local. Vous pouvez joindre au domaine des ordinateurs (machines virtuelles Azure) pour cette forêt de ressources dans le cloud. L’authentification de l’utilisateur a lieu sur une connexion VPN/ExpressRoute à votre répertoire local.

- **Étendre votre domaine local à Azure :** vous pouvez connecter un réseau virtuel Azure à votre réseau local à l’aide d’une connexion VPN/ExpressRoute, afin que les machines virtuelles Azure puissent être jointes à votre AD local. Une autre solution consiste à promouvoir des contrôleurs de domaine répliqués de votre domaine local dans Azure en tant que machine virtuelle. Vous pouvez alors configurer celle-ci pour la réplication via une connexion VPN/ExpressRoute à votre répertoire local. Ce mode de déploiement étend efficacement votre domaine local à Azure.

> [AZURE.NOTE] Vous pouvez décider qu’une option personnalisée est mieux adaptée à vos cas d’utilisation de déploiement. Pensez à [partager vos commentaires](active-directory-ds-contact-us.md) pour nous aider à comprendre quelles fonctionnalités vous aideraient à choisir les services de domaine Azure AD à l’avenir. Ces commentaires nous permettent de faire évoluer le service pour mieux répondre à vos besoins et cas d’utilisation de déploiement.

Nous avons publié des [Recommandations en matière de déploiement de Windows Server Active Directory sur des machines virtuelles Microsoft Azure](https://msdn.microsoft.com/library/azure/jj156090.aspx) pour faciliter les installations personnalisées.


## Contenu connexe
- [Fonctionnalités - Services de domaine Azure AD](active-directory-ds-features.md)

- [Scénarios de déploiement - Services de domaine Azure AD](active-directory-ds-scenarios.md)

- [Instructions pour le déploiement de Windows Server Active Directory sur Azure Virtual Machines](https://msdn.microsoft.com/library/azure/jj156090.aspx)

<!---HONumber=AcomDC_0928_2016-->