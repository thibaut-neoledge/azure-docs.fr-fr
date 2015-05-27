
<properties 
    pageTitle="Configuration d'Active Directory pour Azure RemoteApp" 
    description="Découvrez comment configurer Active Directory pour l'utiliser avec Azure RemoteApp." 
    services="remoteapp" 
    solutions="" documentationCenter="" 
    authors="lizap" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="04/28/2015" 
    ms.author="elizapo" />



# Configuration d'Active Directory pour Azure RemoteApp


Pour une collection hybride de RemoteApp, vous devez configurer une infrastructure de domaine Active Directory locale et un locataire Azure Active Directory avec intégration d'annuaire (et éventuellement l'authentification unique). Par ailleurs, vous devez créer des objets Active Directory dans l'annuaire local. Utilisez les informations suivantes pour configurer des domaines Active Directory et Azure AD locaux, puis intégrer les deux.

## Configuration de votre domaine Active Directory local
Commencez par configurer votre domaine Active Directory local. Vous devez identifier le suffixe de domaine UPN à utiliser, puis créer des objets Active Directory pour RemoteApp.

Vous n'avez pas encore ajouté de services de domaine Active Directory à votre environnement Windows Server 2012 R2 ? Consultez [ces instructions.aspx](https://technet.microsoft.com/library/cc731053.aspx) pour plus d'informations sur la façon de procéder.
### Identification et configuration du suffixe de domaine UPN
Si votre forêt n'est pas configurée avec un suffixe UPN qui correspond au domaine Azure AD que vous utiliserez pour RemoteApp, vous devez l'ajouter. Déterminez si le suffixe requis est configuré :


1. Lancez Utilisateurs et ordinateurs Active Directory.
2.	Développez le nom de domaine, puis cliquez sur **Utilisateurs**.
3.	Cliquez avec le bouton droit sur **Administrateur**, puis cliquez sur **Propriétés**.
4.	Sous l'onglet **Compte**, examinez les noms UPN configurés pour ce domaine dans le champ **Nom d'ouverture de session de l'utilisateur**.
5.	Si vous ne voyez pas de suffixe correspondant au nom de domaine que vous souhaitez utiliser pour votre collection RemoteApp, procédez comme suit :
	1.	Lancez Domaines et approbations Active Directory.
	2.	Cliquez avec le bouton droit sur **Domaines et approbations Active Directory**, puis cliquez sur **Propriétés**.
	3.	Passez en revue la liste des suffixes.
	4.	Tapez le nom de domaine complet dans la zone correspondante, puis cliquez sur **Ajouter**, puis sur **OK**. Par ex. : contoso.com. 

		N'incluez PAS ici le symbole « @ » dans le suffixe.

Quand vous créez de nouveaux utilisateurs, vous pouvez désormais sélectionner le nouveau suffixe dans le champ Nom d'ouverture de session de l'utilisateur. Par ailleurs, vous pouvez modifier le suffixe pour les utilisateurs existants sous l'onglet Compte dans les propriétés des utilisateurs.

Pour plus d'informations, consultez [Ajouter des suffixes UPN](http://technet.microsoft.com/library/cc772007.aspx).

### Création d'objets pour RemoteApp dans Active Directory
RemoteApp a besoin de deux objets dans votre domaine Active Directory local :


- Un compte de service afin de fournir l'accès aux ressources du domaine pour les programmes RemoteApp, en rattachant des points de terminaison RDSH au domaine local.
- Une unité d'organisation pour contenir les objets ordinateur RemoteApp. L'utilisation de l'unité d'organisation est recommandée (mais pas obligatoire) pour isoler les comptes et les stratégies que vous utiliserez avec RemoteApp.

Utilisez les informations suivantes pour créer chacun de ces objets.

#### Création du compte de service :


1. Lancez Utilisateurs et ordinateurs Active Directory.
2.	Cliquez avec le bouton droit sur **Utilisateurs**, puis cliquez sur **Nouveau > Utilisateur**.
3.	Entrez un nom d'utilisateur et un mot de passe pour le compte de service RemoteApp.

	**Remarque :** vous aurez besoin de ces informations de compte pour créer votre collection RemoteApp.

#### Création d'une unité d'organisation


1. Dans Utilisateurs et ordinateurs Active Directory, cliquez avec le bouton droit sur votre domaine. Cliquez sur **Nouveau > Unité d'organisation**.
2. Ajoutez le compte de service que vous avez créé pour RemoteApp à cette nouvelle unité d'organisation.

	Pour cela, recherchez le compte de service que vous avez créé. Cliquez dessus avec le bouton droit, puis cliquez sur **Déplacer**. Sélectionnez la nouvelle unité d'organisation, puis cliquez sur **OK**.


1. Accordez au compte de service RemoteApp l'autorisation d'ajouter et de supprimer des ordinateurs dans cette unité d'organisation :
	1. Dans le composant logiciel enfichable Utilisateurs et ordinateurs Active Directory, cliquez sur **Affichage > Fonctionnalités avancées**. Cette opération ajoute l'onglet **Sécurité** aux informations de propriétés.
	2. Cliquez avec le bouton droit sur l'unité d'organisation du service RemoteApp, puis cliquez sur **Propriétés**.
	3. Sous l'onglet **Sécurité**, cliquez sur **Ajouter**. Sélectionnez l'objet utilisateur du compte de service RemoteApp, puis cliquez sur **OK**.
	4. Cliquez sur **Avancé**.
	5. Sous l'onglet **Autorisations**, sélectionnez le compte de service RemoteApp, puis cliquez sur **Modifier**.
	6. Sélectionnez **Cet objet et tous ceux descendants** dans le champ **S'applique à**.
	7. Dans le champ **Autorisations**, sélectionnez **Autoriser** à côté de Créer des objets ordinateur et Supprimer des objets ordinateur, puis cliquez sur **OK**. 
	8. Cliquez sur **OK** dans les deux fenêtres restantes.


## Configuration d'Azure Active Directory
À présent que le domaine Active Directory local est configuré, passez à Azure AD. Vous devez créer un domaine personnalisé qui correspond au suffixe de votre domaine local et configurer l'intégration d'annuaire. La collection hybride prend en charge uniquement les comptes Azure Active Directory synchronisés (à l'aide d'un outil tel que DirSync) à partir d'un déploiement de Windows Server Active Directory. Plus précisément, ils doivent être synchronisés avec l'option de synchronisation de mot de passe ou la fédération des services ADFS doit être configurée.

Utilisez les informations suivantes pour configurer Azure Active Directory


- [Ajout de votre domaine](http://technet.microsoft.com/library/hh969247.aspx) : utilisez ces informations pour ajouter un domaine correspondant au suffixe de domaine UPN de votre domaine Active Directory local.
- [Intégration d'annuaire](http://technet.microsoft.com/library/jj573653.aspx) : utilisez ces informations pour choisir une option d'intégration d'annuaire, [DirSync avec synchronisation de mot de passe](http://technet.microsoft.com/library/dn441214.aspx) ou [DirSync avec fédération](http://technet.microsoft.com/library/dn441213.aspx).

Vous pouvez également configurer l'[authentification multifacteur](http://technet.microsoft.com/library/dn249466.aspx).

## La configuration de votre synchronisation d'annuaires vous pose problème ?

Dans ce cas, vérifiez les points suivants :

- Vous utilisez la dernière version de l'outil de synchronisation d'annuaires Azure. 
-	Dans le portail de gestion sous **Active Directory -> Annuaire par défaut -> Domaines**, vous avez déjà ajouté votre domaine personnalisé (par exemple, mondomaine.com) et l'avez défini en tant que domaine principal.
-	Sous **Active Directory -> Annuaire par défaut -> Utilisateurs**, vous avez ajouté un nouvel utilisateur sous ce domaine (par exemple, myAzureSyncUser@mydomain.com).
-	Dans votre domaine dans Active Directory, vous avez ajouté un nouvel utilisateur de domaine et l'avez défini en tant que membre des administrateurs de l'entreprise (par exemple, myDomainSyncUser@mydomain.com).

Démarrez maintenant l'outil de synchronisation d'annuaires Azure et utilisez les informations d'identification **myAzureSyncUser@mydomain.com** pour la première invite (Informations d'identification d'administrateur Microsoft Azure Active Directory), puis **myDomainSyncUser@mydomain.com** pour la seconde invite.

<!--HONumber=54-->