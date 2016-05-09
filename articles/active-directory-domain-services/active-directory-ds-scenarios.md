<properties
	pageTitle="Version préliminaire des services de domaine Azure Active Directory : scénarios de déploiement | Microsoft Azure"
	description="Scénarios de déploiement pour les Services de domaine Azure AD"
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
	ms.date="04/11/2016"
	ms.author="maheshu"/>


# Scénarios de déploiement et cas pratiques
Dans cette section, nous examinerons quelques scénarios et cas pratiques qui pourraient tirer parti des services de domaine Azure Active Directory (AD).

## Administration sécurisée et simple des machines virtuelles Azure
Les serveurs et les autres infrastructures étant en fin de vie, Contoso transfère de nombreuses applications actuellement hébergées en local vers le cloud. La norme informatique actuelle exige que les serveurs hébergeant les applications d’entreprise soient joints à un domaine et gérés au moyen d’une stratégie de groupe. L’administrateur informatique de Contoso souhaite joindre à un domaine les machines virtuelles déployées dans Azure, afin de faciliter l’administration (c’est-à-dire que les administrateurs peuvent se connecter à l’aide des informations d’identification d’entreprise). Contoso préférerait ne pas avoir à déployer, surveiller et gérer les contrôleurs de domaine dans Azure pour garantir la sécurité des machines virtuelles.

Quelques points importants à noter lorsque l’on envisage ce scénario :

- Les domaines gérés fournis par les services de domaine Azure AD ne prennent en charge qu’une structure d’unité d’organisation plate. Tous les ordinateurs appartenant à un domaine se trouvent dans une seule unité d’organisation plate et des structures d’unités d’organisation hiérarchisées ne sont pas prises en charge.
- Les services de domaine Azure AD prennent en charge une stratégie de groupe simple sous forme d’un objet de stratégie de groupe intégré pour les conteneurs d’utilisateurs et d’ordinateurs par défaut. Vous ne pouvez pas cibler la stratégie de groupe par unité d’organisation/service, effectuer un filtrage WMI ou créer des objets de stratégie de groupe personnalisés.
- Les services de domaine Azure AD prennent en charge le schéma de l’objet de base de l’ordinateur AD. Vous ne pouvez pas étendre le schéma de l’objet de l’ordinateur.


## Le transfert d’une application sur site qui utilise l’authentification de liaison LDAP pour les services d’Infrastructure Azure
Contoso dispose d’une application sur site qui a été achetée auprès d’un ISV il y a de nombreuses années. L’application est actuellement en mode de maintenance par l’ISV et nécessite des modifications hors de prix pour Contoso. Cette application possède un serveur frontal web qui collecte les informations d’identification de l’utilisateur à l’aide d’un formulaire web et authentifie ensuite les utilisateurs en effectuant une liaison LDAP vers l’annuaire Active Directory d’entreprise. Contoso souhaite migrer cette application vers les services d’infrastructure Azure. Il est préférable que l’application fonctionne en l’état, sans modification. En outre, les utilisateurs doivent pouvoir s’authentifier en utilisant leurs informations d’identification d’entreprise existantes, sans avoir à former les utilisateurs à une nouvelle procédure. En d’autres termes, les utilisateurs finaux doivent ignorer l’endroit depuis lequel l’application s’exécute, et la migration doit être transparente pour eux.

Quelques points importants à noter lorsque l’on envisage ce scénario :

- Assurez-vous que l’application n’a pas besoin de modifier/d’écrire dans le répertoire. L’accès en écriture LDAP aux domaines gérés fournis par les services de domaine Azure AD n’est pas pris en charge.
- Les utilisateurs ne peuvent pas modifier leur mot de passe directement sur le domaine géré. Les utilisateurs peuvent modifier leur mot de passe soit à l’aide du mécanisme de modification de mot de passe en libre-service Azure AD, soit depuis le répertoire local. Ces modifications seront automatiquement synchronisées et disponibles dans le domaine géré.


## Transfert d’une application locale qui utilise la lecture LDAP pour accéder à l’annuaire de service d’infrastructure Azure
Contoso possède une application métier locale qui a été développée presque dix ans auparavant. Cette application est orientée répertoire et a été conçue pour fonctionner avec Windows Server AD. L’application utilise le protocole LDAP (Lightweight Directory Access Protocol) pour lire les informations/attributs sur les utilisateurs à partir d’Active Directory. L’application ne modifie pas les attributs ou dans le cas contraire, écrit dans le répertoire. Contoso souhaite migrer cette application vers les services d’infrastructure Azure et mettre hors-service l’ancien matériel local qui héberge actuellement cette application. L’application ne peut pas être réécrite pour utiliser des API Active Directory modernes comme REST Azure AD Graph. Par conséquent, une option de transfert est souhaitée dans laquelle l’application peut être migrée pour s’exécuter dans le cloud, sans modification de code ou réécriture de l’application.

Quelques points importants à noter lorsque l’on envisage ce scénario :

- Assurez-vous que l’application n’a pas besoin de modifier/d’écrire dans le répertoire. L’accès en écriture LDAP aux domaines gérés fournis par les services de domaine Azure AD n’est pas pris en charge.
- Assurez-vous que l’application ne nécessite pas un schéma Active Directory étendu/personnalisé. Les extensions de schéma ne sont pas prises en charge par les services de domaine Azure AD.
- Assurez-vous que l’application n’exige pas d’accès LDAPS. Le protocole LDAPS n’est pas pris en charge par les services de domaine Azure AD.


## Migration d’une application de service ou démon local vers les services d’infrastructure Azure
Contoso est équipé d’une application de coffre de logiciel personnalisée qui inclut un site web frontal, un serveur SQL et un serveur FTP principal. Ils utilisent l’authentification intégrée de Windows qui utilise des comptes de service pour authentifier le site web frontal auprès du serveur FTP. Ils souhaitent déplacer cette application vers les services d’infrastructure Azure et aimeraient ne pas avoir à déployer une machine virtuelle de contrôleur de domaine dans le cloud en vue de prendre en charge les besoins d’information d’identité de cette application. L’administrateur informatique de Contoso peut déployer les serveurs hébergeant le site web frontal, le serveur SQL et le serveur FTP pour les machines virtuelles dans Azure et les joindre à un domaine des services Azure AD. Ensuite, ils pourront utiliser le même compte de service dans leur répertoire pour les besoins d’authentification de l’application.

Quelques points importants à noter lorsque l’on envisage ce scénario :

- Assurez-vous que l’application utilise un ensemble nom d’utilisateur/mot de passe pour l’authentification. L’authentification basée sur un certificat/carte à puce n’est pas prise en charge par les services de domaine Azure AD.


## Azure RemoteApp
Azure RemoteApp permet à l’administrateur de Contoso de créer une collection jointe à un domaine. Cela permet aux applications distantes prises en charge par Azure RemoteApp de s’exécuter sur les ordinateurs joints à un domaine et d’accéder à d’autres ressources à l’aide de l’authentification intégrée de Windows. Contoso peut utiliser les services de domaine Azure AD pour fournir un domaine géré utilisé par les collections jointes à un domaine Azure RemoteApp.

Pour plus d’informations sur ce scénario de déploiement, consultez l’article du blog des services Bureau à distance intitulé [Lift-and-shift your workloads with Azure RemoteApp and Azure AD Domain Services](http://blogs.msdn.com/b/rds/archive/2016/01/19/lift-and-shift-your-workloads-with-azure-remoteapp-and-azure-ad-domain-services.aspx) (en anglais).

<!---HONumber=AcomDC_0427_2016-->