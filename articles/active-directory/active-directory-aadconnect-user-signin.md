<properties 
	pageTitle="Azure AD Connect - Connexion utilisateur"
	description="Connexion utilisateur Azure AD Connect pour une configuration personnalisée."
	services="active-directory"
	documentationCenter=""
	authors="billmath"
	manager="stevenpo"
	editor="curtand"/>

<tags 
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/24/2015"
	ms.author="billmath"/>



# Options d’authentification de l’utilisateur via Azure AD Connect

Azure AD Connect permet à vos utilisateurs de se connecter aux ressources cloud et locales à l’aide des mêmes mots de passe. Vous pouvez activer cette fonction en procédant de différentes manières.


### Synchronisation du mot de passe
Avec la synchronisation du mot de passe, les hachages des mots de passe utilisateur sont synchronisés de votre annuaire Active Directory local vers Azure AD. Lorsque les mots de passe sont modifiés ou réinitialisés localement, les nouveaux mots de passe sont immédiatement synchronisés avec Azure AD afin que vos utilisateurs puissent toujours utiliser le même mot de passe pour les ressources cloud comme pour les ressources locales. Les mots de passe ne sont jamais envoyés à Azure AD ni stockés dans Azure AD sous forme de texte clair. La synchronisation de mot de passe peut être utilisée avec l’écriture différée de mot de passe pour activer la réinitialisation du mot de passe libre-service dans Azure AD.

<center>![Cloud](./media/active-directory-aadconnect-user-signin/passwordhash.png)</center>

[En savoir plus à propos de la synchronisation de mot de passe](https://msdn.microsoft.com/library/azure/dn246918.aspx)


### Fédération à l’aide des services AD FS nouveaux ou existants dans la batterie de serveurs Windows Server 2012 R2
Avec l’authentification fédérée, vos utilisateurs peuvent se connecter aux services Azure AD avec leurs mots de passe locaux sans avoir à les saisir de nouveau et ce, alors qu’ils sont sur le réseau d’entreprise. L’option de fédération avec les services de fédération d’Azure Directory (AD FS) vous permet de déployer un nouveau service AD FS dans la batterie de serveurs Windows Server 2012 R2 ou d’en spécifier un existant. Si vous choisissez de spécifier une batterie existante, Azure AD Connect configure l’approbation entre votre batterie et Azure AD de manière à ce que vos utilisateurs puissent s’authentifier.

<center>![Cloud](./media/active-directory-aadconnect-user-signin/federatedsignin.png)</center>

#### Éléments requis pour le déploiement de la fédération avec AD FS dans Windows Server 2012 R2
Si vous déployez une nouvelle batterie :

- Un serveur Windows Server 2012 R2 pour le serveur de fédération.
- Un serveur Windows Server 2012 R2 pour le proxy d’application web.
- Un fichier .pfx avec un certificat SSL unique pour le nom de votre service de fédération, par exemple fs.contoso.com.

Si vous déployez une nouvelle batterie ou si vous utilisez une batterie existante :

- Les informations d’identification de l’administrateur local de vos serveurs de fédération.
- Les informations d’identification de l’administrateur local des serveurs des groupes de travail (ne faisant pas partie d’un domaine) sur lesquels vous avez l’intention de déployer le rôle de proxy d’application web.
- L’appareil sur lequel vous exécutez l’Assistant doit être en mesure de se connecter à n’importe quel autre appareil sur lequel vous souhaitez installer AD FS ou un proxy d’application web via Windows Remote Management.

#### Authentification à l’aide d’une version antérieure d’AD FS ou d’une solution tierce
Si vous avez déjà configuré l’authentification cloud à l’aide d’une version antérieure d’AD FS (par exemple, AD FS 2.0) ou à l’aide d’un fournisseur de fédération tiers, vous pouvez choisir d’ignorer la configuration de la connexion utilisateur via Azure AD Connect. Cela vous permet d’obtenir la dernière synchronisation et d’autres fonctionnalités d’Azure AD Connect tout en utilisant toujours votre solution d’authentification existante.

### Choix de la méthode de connexion utilisateur pour votre organisation
Pour la plupart des organisations qui souhaitent juste activer l’authentification utilisateur pour Office 365, les applications SaaS et d’autres ressources basées sur Azure AD, l’option de synchronisation de mot de passe par défaut est recommandée. Certaines organisations ont toutefois des raisons particulières d’utiliser une option d’authentification fédérée comme AD FS. Ces raisons peuvent être les suivantes :

- Votre organisation a déjà déployé AD FS ou un fournisseur de fédération tiers.
- Votre stratégie de sécurité empêche la synchronisation des hachages de mot de passe avec le cloud.
- Vous voulez que les utilisateurs profitent d’une authentification unique transparente (sans demandes supplémentaires du mot de passe) lorsqu’ils accèdent aux ressources cloud à partir d’appareils liés au domaine sur le réseau d’entreprise.
- Vous avez besoin de certaines des capacités spécifiques d’AD FS :
	- Authentification multifacteur locale à l’aide d’un fournisseur tiers ou de cartes à puce (en savoir plus sur les fournisseurs MFA tiers pour AD FS dans Windows Server 2012 R2)
	- Fonctionnalités d’intégration Active Directory telles que le verrouillage souple du compte ou une stratégie d’heures de travail et de mot de passe AD
	- Accès conditionnel aux ressources locales et cloud via l’inscription de l’appareil, une adhésion à Azure AD ou des stratégies Intune MDM
 

<!---HONumber=August15_HO9-->