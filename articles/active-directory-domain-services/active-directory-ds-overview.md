<properties
	pageTitle="Vue d’ensemble de la version préliminaire des services de domaine Azure Active Directory | Microsoft Azure"
	description="Vue d’ensemble des services de domaine Azure AD"
	services="active-directory-ds"
	documentationCenter=""
	authors="mahesh-unnikrishnan"
	manager="udayh"
	editor="inhenk"/>

<tags
	ms.service="active-directory-ds"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/12/2015"
	ms.author="maheshu"/>

# Services de domaine Azure AD *(version préliminaire)*

## Vue d'ensemble
Azure Infrastructure Services permet de déployer une large gamme de solutions informatiques de manière agile. Azure Virtual Machines permet un déploiement quasi instantané, et un paiement à la minute. Grâce à la prise en charge de Windows, Linux, SQL Server, Oracle, IBM, SAP et BizTalk, vous pouvez déployer n’importe quelle charge de travail et toutes les langues sur quasiment tous les systèmes d’exploitation. Ces avantages permettent de migrer des applications héritées déployées sur site vers Azure, tout en faisant des économies sur les dépenses d’exploitation.

Un aspect clé de la migration d’applications sur site vers Azure consiste à gérer les aspects liés à l’identité de ces applications. Les applications orientées répertoire peuvent s’appuyer sur le protocole LDAP pour obtenir un accès en lecture ou en écriture à l’annuaire d’entreprise, ou sur l’authentification intégrée Windows (authentification Kerberos ou NTLM) pour authentifier les utilisateurs finaux. Les applications métier s’exécutant sous Windows Server sont généralement déployées sur les ordinateurs appartenant au domaine, et peuvent être gérées en toute sécurité grâce à la stratégie de groupe. Si l’on veut « transférer » les applications de site vers le cloud, les dépendances par rapport à l’infrastructure d’identité doivent être résolues.

Pour répondre aux besoins liés à l’identité de leurs applications déployées dans Azure, les administrateurs adoptent souvent l’une des solutions suivantes :

- le déploiement d’une connexion VPN de site à site entre charges de travail en cours d’exécution dans les services d’infrastructure Azure et l’annuaire d’entreprise sur site.
- l’extension de l’infrastructure de domaine/forêt Active Directory d’entreprise par le biais de la configuration de contrôleurs de domaine de réplica à l’aide de machines virtuelles.
- le déploiement d’un domaine autonome dans Azure à l’aide de contrôleurs de domaine déployés en tant que machines virtuelles Azure.

Toutes ces approches pèchent par leur coût élevé et la surcharge administrative. Les administrateurs doivent déployer des contrôleurs de domaine en utilisant des machines virtuelles dans Azure. Ils doivent ensuite gérer, assurer la sécurité, appliquer des correctifs, surveiller, sauvegarder et dépanner ces machines virtuelles. La dépendance vis-à-vis des connexions VPN dans l’annuaire sur site expose les charges de travail déployées dans Azure aux problèmes ou pannes réseau temporaires, ce qui réduit les temps d’activité et affecte quelque peu la fiabilité de ces applications.

Les Services de domaine Active Directory Azure sont conçus pour offrir une alternative plus simple.


## Présentation des services de domaine Azure AD
Les Services de domaine Active Directory Azure fournissent des services de domaine gérés tels que la jonction de domaine, la stratégie de groupe, le protocole LDAP, l’authentification Kerberos/NTLM, etc. totalement compatibles avec Windows Server Active Directory. Les services de domaine Azure AD permettent d’utiliser ces services de domaine sans que vous ayez à déployer, gérer et apporter des correctifs aux contrôleurs de domaine dans le cloud. Les services de domaine Azure AD s’intègrent au locataire Azure AD existant, permettant ainsi aux utilisateurs de se connecter à l’aide de leurs informations d’identification d’entreprise. En outre, vous pouvez utiliser des comptes d’utilisateurs et des groupes existants pour sécuriser l’accès aux ressources, et garantir un « transfert » plus simple des ressources locales vers les services d’infrastructure Azure.

Les services de domaine Azure AD fonctionnent de façon transparente, que votre locataire Azure AD soit situé uniquement dans le cloud ou synchronisé avec votre annuaire Active Directory local.

### Services de domaine Azure AD pour les entreprises exclusivement dans le cloud
Un locataire Azure AD exclusivement dans le cloud (souvent appelé « locataire managé ») n’a aucune empreinte sur l’identité locale. En d’autres termes, les utilisateurs, leurs mots de passe et les appartenances au groupe sont tous natifs du cloud, c’est-à-dire créés et gérés dans Azure AD. Imaginons un instant que Contoso est un locataire Azure AD uniquement dans le cloud. Comme indiqué sur l’illustration ci-dessus, l’administrateur de Contoso a configuré un réseau virtuel dans les Services d’Infrastructure Azure. Les applications et les charges de travail de serveur sont déployées dans ce réseau virtuel sur des machines virtuelles Azure. Contoso étant un locataire exclusivement dans le cloud, toutes les identités des utilisateurs, leurs informations d’identification et les appartenances aux groupes sont créées et gérées dans Azure AD.

![Vue d’ensemble des services de domaine Azure AD](./media/active-directory-domain-services-overview/aadds-overview.png)

L’administrateur informatique de Contoso peut activer les services de domaine Azure AD pour son locataire Azure AD et choisir de proposer des services de domaine dans ce réseau virtuel. Lorsque ce paramètre est configuré, les services de domaine Azure AD configurent un domaine géré et le rendent disponible sur le réseau virtuel. Tous les comptes d’utilisateur, les appartenances au groupe et les informations d’identification utilisateur disponibles dans le locataire Azure AD de Contoso sont également disponibles dans ce domaine nouvellement créé. Cette fonctionnalité permet aux utilisateurs de se connecter au domaine à l’aide de leurs informations d’identification d’entreprise, par exemple, lors de la connexion à distance aux ordinateurs joints au domaine via le Bureau à distance. Les administrateurs peuvent configurer l’accès aux ressources dans le domaine à l’aide des appartenances aux groupes existantes. Les applications déployées sur les machines virtuelles dans le réseau virtuel bénéficient des services de domaine tels que la jonction de domaine, la lecture LDAP, la liaison LDAP, l’authentification NTLM et Kerberos, la stratégie de groupe, etc.

Voici quelques aspects fondamentaux du domaine géré configuré par les services de domaine Azure AD :

- L’administrateur informatique de Contoso n’a pas à gérer, appliquer les correctifs ou surveiller le domaine ou les contrôleurs de domaine pour ce domaine géré.
- Il est inutile de gérer la réplication AD pour ce domaine. Les comptes utilisateur, les appartenances aux groupes et les informations d’identification du locataire Azure AD de Contoso sont automatiquement disponibles dans ce domaine géré.
- Étant donné que le domaine est géré par les services de domaine Azure AD, l’administrateur informatique de Contoso ne dispose pas des privilèges d’administrateur de domaine ou d’administrateur d’entreprise sur ce domaine.


### Services de domaine Azure AD pour les entreprises hybrides
Les entreprises dotées d’une infrastructure informatique hybride consomment à la fois des ressources de cloud et des ressources locales. Ces entreprises synchronisent les informations d’identité de leur annuaire local vers avec le locataire Azure AD. Comme les entreprises hybrides cherchent à migrer davantage d’applications locales vers le cloud, en particulier les applications héritées orientées annuaire, les services de domaine Azure AD peuvent leur être très utiles.

La société Litware Corporation a déployé [Azure AD Connect ](../active-directory/active-directory-aadconnect.md), afin de synchroniser les informations d’identité de l’annuaire local avec le locataire Azure AD. Cela inclut les comptes d’utilisateurs, les hachages d’informations d’identification pour l’authentification (synchronisation de mot de passe) et les appartenances aux groupes. Notez que **la synchronisation de mot de passe est obligatoire pour les entreprises hybrides qui veulent utiliser les services de domaine Azure AD**. Cela est dû au fait que les informations d’identification des utilisateurs sont obligatoires dans le domaine géré fourni par les services de domaine Azure AD afin d’authentifier les utilisateurs via les méthodes d’authentification NTLM ou Kerberos.

![Services de domaine Azure AD pour Litware Corporation](./media/active-directory-domain-services-overview/aadds-overview-synced-tenant.png)

L’illustration ci-dessus montre comment des entreprises dotées d’une infrastructure informatique hybride, comme Litware, peuvent utiliser les services de domaine Azure AD. Les applications et charges de travail de serveurs de Litware nécessitant des services de domaine sont déployées dans un réseau virtuel dans les services d’infrastructure Azure. L’administrateur informatique de Litware peut activer les services de domaine Azure AD pour son locataire Azure AD et rendre un domaine géré disponible dans ce réseau virtuel. Vu que Litware est une entreprise hybride avec une infrastructure informatique hybride, les comptes d’utilisateurs, les groupes et les informations d’identification sont synchronisées avec leur locataire Azure AD à partir de leur annuaire local. Cette fonctionnalité permet aux utilisateurs de se connecter au domaine à l’aide de leurs informations d’identification d’entreprise, par exemple, lors de la connexion à distance aux ordinateurs joints au domaine via le Bureau à distance. Les administrateurs peuvent configurer l’accès aux ressources dans le domaine à l’aide des appartenances aux groupes existantes. Les applications déployées sur les machines virtuelles dans le réseau virtuel bénéficient des services de domaine tels que la jonction de domaine, la lecture LDAP, la liaison LDAP, l’authentification NTLM et Kerberos, la stratégie de groupe, etc.

Voici quelques aspects fondamentaux du domaine géré configuré par les services de domaine Azure AD :

- Il s’agit d’un domaine géré autonome, et non une extension du domaine local de Litware.
- L’administrateur informatique de Litware n’a pas à gérer, appliquer les correctifs ou analyser le domaine ou les contrôleurs de domaine de ce domaine géré.
- Il est inutile de gérer la réplication AD pour ce domaine. Les comptes d’utilisateur, les appartenances aux groupes et les informations d’identification de l’annuaire de Litware local sont synchronisés avec Azure AD via Azure AD Connect. Ceux-ci sont automatiquement disponibles dans ce domaine géré.
- Étant donné que le domaine est géré par les Services de domaine Azure AD, l’administrateur informatique de Litware administrateur ne dispose pas des privilèges d’administrateur ou des privilèges administrateur d’entreprise sur ce domaine.


## Scénarios et cas pratiques
Dans cette section, nous examinons quelques scénarios et cas d’utilisation qui pourraient tirer parti des services de domaine Azure AD.

### Administration sécurisée et simple des machines virtuelles Azure
Les serveurs et les autres infrastructures étant en fin de vie, Contoso transfère de nombreuses applications actuellement hébergées en local vers le cloud. La norme informatique actuelle exige que les serveurs hébergeant les applications d’entreprise soient joints à un domaine et gérés au moyen d’une stratégie de groupe. L’administrateur informatique de Contoso souhaite joindre à un domaine les machines virtuelles déployées dans Azure, afin de faciliter l’administration (c’est-à-dire que les administrateurs peuvent se connecter à l’aide des informations d’identification d’entreprise). Contoso préférerait ne pas avoir à déployer, surveiller et gérer les contrôleurs de domaine dans Azure pour garantir la sécurité des machines virtuelles.

Quelques points importants à noter lorsque l’on envisage ce scénario :

- Les domaines gérés fournis par les services de domaine Azure AD ne prennent en charge qu’une structure d’unité d’organisation plate. Tous les ordinateurs appartenant à un domaine se trouvent dans une seule unité d’organisation plate et des structures d’unités d’organisation hiérarchisées ne sont pas prises en charge.
- Les services de domaine Azure AD prennent en charge une stratégie de groupe simple sous forme d’un objet de stratégie de groupe intégré pour les conteneurs d’utilisateurs et d’ordinateurs par défaut. Vous ne pouvez pas cibler la stratégie de groupe par unité d’organisation/service, effectuer un filtrage WMI ou créer des objets de stratégie de groupe personnalisés.
- Les services de domaine Azure AD prennent en charge le schéma de l’objet de base de l’ordinateur AD. Vous ne pouvez pas étendre le schéma de l’objet de l’ordinateur.


### Le transfert d’une application sur site qui utilise l’authentification de liaison LDAP pour les services d’Infrastructure Azure
Contoso dispose d’une application sur site qui a été achetée auprès d’un ISV il y a de nombreuses années. L’application est actuellement en mode de maintenance par l’ISV et nécessite des modifications hors de prix pour Contoso. Cette application possède un serveur frontal web qui collecte les informations d’identification de l’utilisateur à l’aide d’un formulaire web et authentifie ensuite les utilisateurs en effectuant une liaison LDAP vers l’annuaire Active Directory d’entreprise. Contoso souhaite migrer cette application vers les services d’infrastructure Azure. Il est préférable que l’application fonctionne en l’état, sans modification. En outre, les utilisateurs doivent pouvoir s’authentifier en utilisant leurs informations d’identification d’entreprise existantes, sans avoir à former les utilisateurs à une nouvelle procédure. En d’autres termes, les utilisateurs finaux doivent ignorer l’endroit depuis lequel l’application s’exécute, et la migration doit être transparente pour eux.

Quelques points importants à noter lorsque l’on envisage ce scénario :

- Assurez-vous que l’application n’a pas besoin de modifier/d’écrire dans le répertoire. L’accès en écriture LDAP aux domaines gérés fournis par les services de domaine Azure AD n’est pas pris en charge.
- Les utilisateurs ne peuvent pas modifier leur mot de passe directement sur le domaine géré. Les utilisateurs peuvent modifier leur mot de passe soit à l’aide du mécanisme de modification de mot de passe en libre-service Azure AD, soit depuis le répertoire local. Ces modifications seront automatiquement synchronisées et disponibles dans le domaine géré.


### Transfert d’une application locale qui utilise la lecture LDAP pour accéder à l’annuaire de service d’infrastructure Azure
Contoso possède une application métier locale qui a été développée presque dix ans auparavant. Cette application est orientée répertoire et a été conçue pour fonctionner avec Windows Server AD. L’application utilise le protocole LDAP (Lightweight Directory Access Protocol) pour lire les informations/attributs sur les utilisateurs à partir d’Active Directory. L’application ne modifie pas les attributs ou dans le cas contraire, écrit dans le répertoire. Contoso souhaite migrer cette application vers les services d’infrastructure Azure et mettre hors-service l’ancien matériel local qui héberge actuellement cette application. L’application ne peut pas être réécrite pour utiliser des API Active Directory modernes comme REST Azure AD Graph. Par conséquent, une option de transfert est souhaitée dans laquelle l’application peut être migrée pour s’exécuter dans le cloud, sans modification de code ou réécriture de l’application.

Quelques points importants à noter lorsque l’on envisage ce scénario :

- Assurez-vous que l’application n’a pas besoin de modifier/d’écrire dans le répertoire. L’accès en écriture LDAP aux domaines gérés fournis par les services de domaine Azure AD n’est pas pris en charge.
- Assurez-vous que l’application ne nécessite pas un schéma Active Directory étendu/personnalisé. Les extensions de schéma ne sont pas prises en charge par les services de domaine Azure AD.
- Assurez-vous que l’application n’exige pas d’accès LDAPS. Le protocole LDAPS n’est pas pris en charge par les services de domaine Azure AD.


### Migration d’une application de service ou démon local vers les services d’infrastructure Azure
Contoso est équipé d’une application de coffre de logiciel personnalisée qui inclut un site web frontal, un serveur SQL et un serveur FTP principal. Ils utilisent l’authentification intégrée de Windows qui utilise des comptes de service pour authentifier le site web frontal auprès du serveur FTP. Ils souhaitent déplacer cette application vers les services d’infrastructure Azure et aimeraient ne pas avoir à déployer une machine virtuelle de contrôleur de domaine dans le cloud en vue de prendre en charge les besoins d’information d’identité de cette application. L’administrateur informatique de Contoso peut déployer les serveurs hébergeant le site web frontal, le serveur SQL et le serveur FTP pour les machines virtuelles dans Azure et les joindre à un domaine des services Azure AD. Ensuite, ils pourront utiliser le même compte de service dans leur répertoire pour les besoins d’authentification de l’application.

Quelques points importants à noter lorsque l’on envisage ce scénario :

- Assurez-vous que l’application utilise un ensemble nom d’utilisateur/mot de passe pour l’authentification. L’authentification basée sur un certificat/carte à puce n’est pas prise en charge par les services de domaine Azure AD.


## Avantages
Avec les services de domaine Azure AD, vous pouvez bénéficier des avantages suivants :

-	**Simple** : vous pouvez satisfaire les besoins d’identité des machines virtuelles déployées sur les services d’Infrastructure Azure en quelques clics, sans avoir à déployer et à gérer l’infrastructure d’identité dans Azure ou à configurer la connectivité de retour vers votre infrastructure d’identité locale.

-	**Intégré** : les services de domaine Azure AD s’intègrent parfaitement à votre locataire Azure AD. Désormais, vous pouvez considérer Azure AD comme un annuaire d’entreprise dans le cloud intégré qui répond aux besoins de vos applications modernes ainsi qu’a ceux des applications de répertoires traditionnels.

-	**Compatible** : sachant que les Services de domaine Azure AD reposent sur l’infrastructure de niveau entreprise éprouvée de Windows Server Active Directory, vos applications peuvent s’appuyer sur un degré de compatibilité supérieur, avec les fonctions de Windows Server Active Directory. Notez que les fonctionnalités disponibles dans Windows Server Active Directory ne sont actuellement pas toutes disponibles pour les Services de domaine Azure AD. Toutefois, les fonctionnalités disponibles sont compatibles avec les fonctionnalités correspondantes de Windows Server Active Directory présentes dans votre infrastructure locale. Les fonctionnalités LDAP, Kerberos, NTLM, Stratégie de groupe et de jonction de domaine fournies par les Services de domaine Azure AD constituent une solution ayant fait ses preuves, qui a été testée et encore affinée sur plusieurs versions de Windows Server.

-	**Rentable** : avec les Services de domaine Azure AD, vous pouvez éviter les charges que représentent l’infrastructure et la gestion liées à la gestion de l’infrastructure d’identité associée aux applications orientées répertoire traditionnelles. Vous pouvez transférer ces applications vers les Services d’Infrastructure Azure et réaliser des économies substantielles sur les frais d’exploitation.

<!---HONumber=Oct15_HO3-->