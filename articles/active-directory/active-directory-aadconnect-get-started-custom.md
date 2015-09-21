<properties 
	pageTitle="Installation personnalisée d’Azure AD Connect" 
	description="Ce document présente les options de l’installation personnalisée d’Azure AD Connect." 
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

# Installation personnalisée d’Azure AD Connect


La documentation suivante explique comment utiliser l’option d’installation personnalisée pour Azure AD Connect. Vous pouvez utiliser cette option si vous avez des options de configuration supplémentaires ou si vous avez besoin de fonctionnalités facultatives qui ne sont pas traitées par l’installation rapide.

Pour plus d’informations sur l’installation rapide, consultez la page [Installation rapide](active-directory-aadconnect.md#getting-started-with-azure-ad-connect). Pour plus d’informations sur la mise à niveau depuis DirSync vers Azure AD Connect, consultez [Mise à niveau de DirSync pour Azure Active Directory Connect.](active-directory-aadconnect-dirsync-upgrade-get-started.md)



## Installation des composants requis

Lorsque vous installez les services de synchronisation, vous pouvez laisser la section de configuration facultative de côté. Azure AD Connect configurera tout automatiquement. Cela comprend la configuration d’une instance de SQL Server 2012 Express et la création des groupes appropriés, avec attribution des autorisations. Si vous souhaitez modifier les valeurs par défaut, vous pouvez utiliser le tableau ci-dessous pour comprendre les différentes options de configuration facultatives.

![Composants requis](./media/active-directory-aadconnect-get-started-custom/requiredcomponents.png)


Configuration facultative | Description 
------------- | ------------- |
Nom du serveur SQL Server |Permet de spécifier le nom du serveur SQL et le nom de l’instance. Choisissez cette option si vous avez déjà un serveur de base de données Active Directory que vous souhaitez utiliser.
Compte de service |Par défaut, Azure AD Connect crée le compte de service local qu’utilisent les services de synchronisation. Le problème qui se pose est que le mot de passe est généré automatiquement et n’est pas connu de la personne qui installe Azure AD Connect. Dans la plupart des scénarios, cela ne pose aucun problème, mais si vous souhaitez appliquer certaines configurations avancées, par exemple choisir les unités organisationnelles à synchroniser, vous devez créer un compte et choisir votre mot de passe. Cependant, si vous utilisez un serveur SQL distant, vous devez avoir un compte de service dans le domaine et connaître le mot de passe. Dans ce cas, entrez le compte de service à utiliser. |
Autorisations | Par défaut, Azure AD Connect crée quatre groupes locaux vers le serveur lorsque les services de synchronisation sont installés. Ces groupes sont Administrateurs, Opérateurs, Parcourir et Réinitialisation du mot de passe. Si vous souhaitez spécifier vos propres groupes, vous pouvez le faire ici.


## Connexion de l’utilisateur
Après avoir installé les composants requis, vous devez spécifier la méthode d’authentification unique des utilisateurs. Le tableau ci-dessous fournit une brève description des options disponibles.

![Connexion de l’utilisateur](./media/active-directory-aadconnect-get-started-custom/usersignin.png)



Option d’authentification unique | Description 
------------- | ------------- |
Synchronisation de mot de passe |Les utilisateurs peuvent se connecter aux services cloud Microsoft tels qu’Office 365, Dynamics CRM et Windows InTune à l’aide du mot de passe utilisé pour se connecter à leurs réseaux locaux. Le mot de passe des utilisateurs est synchronisé à Azure via un hachage de mot de passe et l’authentification se produit dans le cloud.
Fédération avec AD FS|Les utilisateurs peuvent se connecter aux services cloud Microsoft tels qu’Office 365, Dynamics CRM et Windows InTune à l’aide du mot de passe utilisé pour se connecter à leurs réseaux locaux. Ils sont redirigés vers leur instance AD FS locale, car la connexion et l’authentification sont effectuées en local.
Ne pas configurer| Aucune fonctionnalité n’est installée ni configurée. Choisissez cette option si vous disposez déjà d’un serveur de fédération tiers ou d’une autre solution.



## Connexion à Azure AD
Sur l’écran Connexion à Azure AD, entrez un compte et un mot de passe d’administrateur général. Assurez-vous que l’authentification multifacteur n’est pas activée pour ce compte. Sinon, l’authentification va échouer. N’oubliez pas que ce compte est uniquement destiné à créer un compte de service dans Azure AD et n’est plus utilisé une fois l’Assistant terminé.

![Connexion de l’utilisateur](./media/active-directory-aadconnect-get-started-custom/connectaad.png)


### Connexion de vos annuaires
Pour vous connecter à votre service de domaine Active Directory, Azure AD Connect a besoin des informations d’identification d’un compte doté d’autorisations suffisantes. Ce compte peut être un compte d’utilisateur normal, car seules des autorisations de lecture par défaut sont nécessaires. Toutefois, selon votre scénario, vous pouvez avoir besoin d’autorisations supplémentaires. Pour plus d’informations, consultez la rubrique [Résumé d’un compte AD Connect](active-directory-aadconnect-account-summary.md).

![Connexion de l’utilisateur](./media/active-directory-aadconnect-get-started-custom/connectdir.png)


### Identification unique de vos utilisateurs

La fonctionnalité Correspondance entre les forêts vous permet de définir la méthode de représentation des utilisateurs de vos forêts AD DS dans Azure AD. Il est possible de représenter seulement une fois chaque utilisateur entre toutes les forêts, ou de présenter une combinaison des comptes activés et désactivés.

![Connexion de l’utilisateur](./media/active-directory-aadconnect-get-started-custom/unique.png)


Paramètre | Description 
------------- | ------------- |
Mes utilisateurs ne sont représentés qu’une seule fois à travers toutes les forêts| Tous les utilisateurs sont créés en tant qu’objets individuels dans Azure AD.<br> Les objets ne sont pas associés dans le métaverse.
Attribut de messagerie | Cette option associe des utilisateurs et des contacts si l’attribut de messagerie a la même valeur dans des forêts différentes. Nous vous recommandons d’utiliser cette option si vos contacts ont été créés avec GALSync.
ObjectSID et msExchangeMasterAccountSID|Cette option associe un utilisateur activé dans une forêt de comptes à un utilisateur désactivé dans une forêt de ressources Exchange. Elle est également nommée Boîte aux lettres liée dans Exchange.
sAMAccountName et MailNickName|Cette option associe des attributs où l’ID de connexion est requis pour rechercher l’utilisateur.
Mon propre attribut|Cette option vous permet de sélectionner votre propre attribut. **Limitation :** assurez-vous de sélectionner un attribut qui existe déjà dans le métaverse. Si vous sélectionnez un attribut personnalisé, l’Assistant va échouer.

- **Point d’ancrage** : l’attribut sourceAnchor ne varie pas pendant la durée de vie d’un objet utilisateur. Il s’agit de la clé primaire liant l’utilisateur local avec l’utilisateur dans Azure AD. Comme l’attribut ne peut pas être modifié, vous devez prévoir l’attribut adéquat à utiliser. Pour cela, nous vous recommandons objectGUID. Cet attribut ne change pas, sauf si le compte de l’utilisateur est déplacé entre les forêts/domaines. Dans un environnement à plusieurs forêts où vous déplacez des comptes entre des forêts, vous devez utiliser un autre attribut, comme un attribut avec l’employeeID. Les attributs à éviter sont ceux susceptibles de changer si une personne se marie ou si son affectation est modifiée. Vous ne pouvez pas utiliser d’attributs avec un signe @, donc les adresses de messagerie et userPrincipalName et ne peuvent pas être utilisées. L’attribut est également sensible à la casse, donc si vous déplacez un objet entre des forêts, veillez à conserver ses minuscules/majuscules. La valeur des attributs binaires est codée en base64, mais pour d’autres types, elle ne sera pas codée. Dans les scénarios de fédération et dans certaines interfaces Azure AD, cet attribut est également appelé immutableID.

- **UserPrincipalName** : l’attribut userPrincipalName est utilisé par les utilisateurs lorsqu’ils se connectent à Azure AD et Office 365. Les domaines utilisés, également nommés « Suffixe UPN » doivent être vérifiés dans Azure AD avant la synchronisation des utilisateurs. Il est fortement recommandé de conserver l’userPrincipalName de l’attribut par défaut. Si cet attribut ne peut pas être acheminé ni vérifié, vous pouvez sélectionner un autre attribut, par exemple une adresse de messagerie électronique, comme attribut contenant l’ID de connexion. Ceci est connu sous le nom d’**ID secondaire**. La valeur de l’attribut ID secondaire doit suivre la norme RFC822. Un ID secondaire peut être utilisé avec l’authentification unique par mot de passe et avec l’authentification unique de fédération comme solution de connexion.

>[AZURE.WARNING]L’utilisation d’un ID secondaire n’est pas compatible avec toutes les charges de travail Office 365. Pour plus d’informations, consultez [Configuration d’un ID secondaire de connexion](https://technet.microsoft.com/library/dn659436.aspx.).







### Filtrage de synchronisation basé sur les groupes
Le filtrage de la fonctionnalité Groupes vous permet d’exécuter un pilote réduit où seul un petit sous-ensemble d’objets doit être créé dans Azure AD et Office 365. Pour utiliser cette fonctionnalité, créez un groupe dans votre version d’Active Directory, puis ajoutez les utilisateurs et groupes à synchroniser avec Azure AD en tant que membres directs. Vous pouvez ajouter et supprimer ultérieurement des utilisateurs à ce groupe pour tenir à jour la liste des objets présents dans Azure AD. Pour utiliser cette fonctionnalité, dans le chemin d’accès personnalisé, vous devez consulter cette page :

![Filtrage de la synchronisation](./media/active-directory-aadconnect-get-started-custom/filter2.png)


### Fonctionnalités facultatives

Cet écran vous permet de sélectionner des fonctionnalités facultatives pour vos scénarios spécifiques. Voici une brève explication de chaque fonctionnalité.

<center>![Installation express](./media/active-directory-aadconnect-get-started-custom/optional.png)</center>


Fonctionnalités facultatives | Description
-------------------    | ------------- | 
Déploiement Exchange hybride |La fonctionnalité de déploiement Exchange hybride permet la coexistence de boîtes aux lettres Exchange locales et dans Azure grâce à la synchronisation d’un jeu d’attributs spécifique d’Azure AD Connect dans votre répertoire local.
Application Azure AD et filtrage des attributs|En activant l’application Azure AD et le filtrage des attributs, l’ensemble des attributs synchronisés peut être adapté à un ensemble spécifique dans une page à venir de l’Assistant. Deux pages de configuration supplémentaires s’ouvrent dans l’Assistant.  
Écriture différée du mot de passe|En activant l’écriture différée du mot de passe, les modifications de mot de passe provenant d’Azure AD Connect sont réécrites dans votre annuaire local.
Synchronisation des attributs des extensions d’annuaire|En activant la synchronisation des attributs des extensions d’annuaire, les attributs spécifiés seront synchronisés avec Azure AD. Une page de configuration supplémentaire s’ouvre dans l’Assistant.  

Pour plus d’options de configuration, comme la modification de la configuration par défaut, l’utilisation de l’éditeur de règles de synchronisation et l’approvisionnement déclaratif, voir [Gestion d’Azure AD Connect](active-directory-aadconnect-whats-next.md).




Ajoutez le nom du groupe contenant les utilisateurs et groupes. Seuls les membres de ce groupe sont synchronisés avec Azure AD.

## Synchronisation des attributs des extensions d’annuaire
Les extensions d’annuaire vous permettent d’étendre le schéma dans Azure AD en utilisant des attributs personnalisés ajoutés par votre organisation ou d’autres attributs dans Active Directory. Pour utiliser cette fonctionnalité, sélectionnez « Synchronisation des attributs des extensions d’annuaire » sur la page « Fonctionnalités facultatives ». Vous allez accéder à cette page et sélectionner vos attributs supplémentaires.

![Filtrage de la synchronisation](./media/active-directory-aadconnect-get-started-custom/extension2.png)


Seuls les attributs à valeur unique sont pris en charge et la valeur ne peut pas comporter plus de 250 caractères. Le schéma Azure AD et le métaverse sont étendus avec les attributs sélectionnés. Une nouvelle application est ajoutée dans Azure AD avec les attributs.

![Filtrage de la synchronisation](./media/active-directory-aadconnect-get-started-custom/extension3.png)


Ces attributs sont désormais disponibles via l’API Graph :

![Filtrage de la synchronisation](./media/active-directory-aadconnect-get-started-custom/extension4.png)




## Écriture différée de groupe (version préliminaire)

> [AZURE.WARNING]Si DirSync ou Azure AD Sync sont actuellement actifs, n’activez aucune des fonctionnalités d’écriture différée dans Azure AD Connect.

L’option pour l’écriture différée de groupe dans les fonctionnalités facultatives permet l’écriture différée de « groupes dans Office 365 » vers une forêt avec Exchange installé. Il s’agit d’un nouveau type de groupe qui est toujours contrôlé dans le cloud. Cette fonctionnalité est disponible dans outlook.office365.com ou myapps.microsoft.com, comme indiqué ici :


![Filtrage de la synchronisation](./media/active-directory-aadconnect-get-started-custom/office365.png)



![Filtrage de la synchronisation](./media/active-directory-aadconnect-get-started-custom/myapps.png)


Ce groupe est représenté comme un groupe de distribution dans les versions locales d’AD DS. Votre serveur Exchange local doit être au niveau de la mise à jour cumulative 8 d’Exchange 2013 (publiée en mars 2015) pour reconnaître ce nouveau type de groupe.

**Remarque :**

- L’attribut de carnet d’adresse n’est pas rempli pour l’instant. Il est plus simple de récupérer la propriété de carnet d’adresse à partir d’un autre groupe de votre organisation, puis de la remplir hors du moteur de synchronisation. **Pour ce faire, nous vous recommandons d’utiliser l’applet de commande PowerShell update-recipient.**
- Seules les forêts dotées du schéma Exchange constituent des cibles valides pour les groupes. Si aucune version d’Exchange n’est détectée, il est impossible d’activer l’écriture différée de groupe.
- La fonctionnalité d’écriture différée de groupe ne prend pas en charge les groupes de sécurité ou les groupes de distribution pour l’instant.

Des informations supplémentaires sont disponibles [ici](http://blogs.office.com/2014/09/25/delivering-first-chapter-groups-office-365/).

## Écriture différée d’appareil (version préliminaire)

> [AZURE.WARNING]Si DirSync ou Azure AD Sync sont actuellement actifs, n’activez aucune des fonctionnalités d’écriture différée dans Azure AD Connect.

La fonctionnalité d'écriture différée d’appareil vous permet de récupérer un appareil inscrit dans le cloud (par exemple, dans Intune) et d’y accéder de façon conditionnelle dans AD DS. Vous devez préparer AD DS avant d’activer la fonctionnalité. Si vous installez AD FS et le service d’inscription pour appareil (DRS), ce dernier fournit des applets de commande PowerShell pour préparer AD à l’écriture différée d’appareil. Si vous n’avez pas encore installé le service d’inscription pour appareil, vous pouvez exécuter C:\\Program Files\\Microsoft Azure Active Directory Connect\\AdPrep\\AdSyncAdPrep.psm1 en tant qu’administrateur d’entreprise.

Pour pouvoir utiliser l’applet PowerShell, vous devez préalablement l’importer.

	Import-Module 'C:\Program Files\Microsoft Azure Active Directory Connect\AdPrep\AdSyncPrep.psm1'

Pour ce faire, vous devez installer localement Active Directory et MSOnline PowerShell.



## Mode intermédiaire
Le mode intermédiaire permet de configurer un nouveau serveur de synchronisation parallèlement à un serveur existant. Le système prend en charge une seule connexion directe de serveur de synchronisation à un seul annuaire dans le cloud. Mais si vous voulez procéder à un déplacement à partir d’un autre serveur (par exemple, un serveur exécutant DirSync), vous pouvez activer Azure AD Connect en mode intermédiaire. Une fois activé, le moteur de synchronisation importe et synchronise les données comme d’habitude, mais il n’exporte rien vers Azure AD tout en désactivant à la fois la synchronisation et l’écriture différée de mot de passe.

![Filtrage de la synchronisation](./media/active-directory-aadconnect-get-started-custom/stagingmode.png)


En mode intermédiaire, vous pouvez modifier le moteur de synchronisation selon vos besoins et examiner ce qui doit être exporté. Lorsque la configuration vous convient, réexécutez l’Assistant Installation et désactivez le mode intermédiaire. Cela permet l’exportation de données vers Azure AD. Veillez à désactiver l’autre serveur en même temps, pour qu’un seul serveur puisse exporter de manière active.



## Configuration de la fédération avec AD FS
La configuration d’AD FS avec Azure AD Connect s’effectue simplement en quelques clics. La configuration suivante est requise avant la configuration.

- Un serveur Windows Server 2012 R2 pour le serveur de fédération avec la gestion distante activée
- Un serveur Windows Server 2012 R2 pour le serveur proxy d’application web avec la gestion distante activée
- Un certificat SSL pour le nom de service de fédération que vous prévoyez d’utiliser (par exemple, adfs.contoso.com)

### Création d’une batterie de serveurs AD FS ou utilisation d’une batterie de serveurs AD FS existante
Vous pouvez utiliser une batterie de serveurs AD FS existante ou d’en créer une. Si vous choisissez de créer une batterie de serveurs, vous devez fournir le certificat SSL. Si le certificat SSL est protégé par un mot de passe, vous devez fournir le mot de passe.

![Batterie de serveurs ADFS](./media/active-directory-aadconnect-get-started-custom/adfs1.png)

**Remarque :** si vous choisissez d’utiliser une batterie de serveurs AD FS existante, vous devez passer quelques pages avant d’être dirigé directement vers l’écran de configuration de la relation d’approbation entre AD FS et Azure AD.

### Spécification des serveurs AD FS

Ici, vous devez entrer les serveurs spécifiques sur lesquels vous souhaitez installer AD FS. Vous pouvez ajouter un ou plusieurs serveurs selon vos besoins de planification de capacité. Ces serveurs doivent tous être associés à un domaine Active Directory avant d’effectuer cette configuration. Nous vous recommandons d’installer un seul serveur AD FS pour tester et superviser les déploiements avant de déployer des serveurs supplémentaires en ouvrant Azure AD Connect et en déployant AD FS sur des serveurs supplémentaires pour répondre à vos besoins de mise à l’échelle.


> [AZURE.NOTE]Avant d’effectuer cette configuration, veuillez vérifier que tous les serveurs sont associés à un domaine Active Directory.

![Serveurs AD FS](./media/active-directory-aadconnect-get-started-custom/adfs2.png)


 
### Spécification des serveurs proxy d’application web
Ici, vous devez entrer les serveurs spécifiques que vous souhaitez utiliser en tant que serveurs proxy d’application web. Les serveurs proxy d’application web sont déployés dans votre DMZ (accès extranet) et prennent en charge les demandes d’authentification provenant de l’extranet. Vous pouvez ajouter un ou plusieurs serveurs selon vos besoins de planification de capacité. Nous vous recommandons d’installer un seul serveur proxy d’application web pour tester et superviser les déploiements avant de déployer des serveurs supplémentaires en ouvrant Azure AD Connect et en déployant d’autres serveurs proxy d’application web. Nous recommandons généralement d’avoir un nombre suffisant de serveurs proxy pour répondre aux demandes d’authentification à partir de l'intranet.

> [AZURE.NOTE]<li>Si le compte que vous utilisez pour installer Azure AD Connect n’est pas un administrateur local sur les serveurs AD FS, vous devez vous connecter avec un compte qui dispose des autorisations suffisantes.</li><li>Avant de procéder à cette configuration, veuillez vérifier la connectivité HTTP/HTTPS entre le serveur Azure AD Connect et le serveur proxy d’application web.</li><li> Assurez-vous également qu’il existe une connectivité HTTP/HTTPS entre le serveur d’applications web et le serveur AD FS pour autoriser les transmissions de demandes d’authentification.</li>


![Application web](./media/active-directory-aadconnect-get-started-custom/adfs3.png)


Vous devez vous connecter pour que le serveur d’application web puisse établir une connexion sécurisée vers le serveur AD FS. Cette connexion doit utiliser des informations d’identification de compte d’administrateur local sur le serveur AD FS.

![Proxy](./media/active-directory-aadconnect-get-started-custom/adfs4.png)

 
### Spécification du compte de service pour le service AD FS
Le service AD FS requiert un compte de service de domaine pour authentifier les utilisateurs et rechercher les informations utilisateur dans Active Directory. Il prend en charge 2 types de compte de service :

- **Comptes de service gérés de groupe** : il s’agit d’un type de compte de service introduit dans le service de domaine Active Directory avec Windows Server 2012. Ce type de compte fournit des services tels qu’AD FS pour utiliser un seul compte sans avoir à mettre à jour son mot de passe régulièrement. Utilisez cette option si vous avez déjà des contrôleurs de domaine Windows Server 2012 dans le domaine auquel appartiennent vos serveurs AD FS.
- **Compte d’utilisateur de domaine** : ce type de compte requiert un mot de passe et des mises à jour régulières, à chaque modification du mot de passe. Utilisez cette option si vous n’avez pas de contrôleur de domaine Windows Server 2012 dans le domaine auquel appartiennent vos serveurs AD FS.

Azure AD Connect crée automatiquement le compte de service géré par un groupe si vous vous connectez en tant qu’administrateur de domaine.
 
![Compte de service AD FS](./media/active-directory-aadconnect-get-started-custom/adfs5.png)


### Sélection du domaine Azure AD à fédérer
Cette opération permet de configurer la relation de fédération entre AD FS et Azure AD. Il s’agit de configurer AD FS pour émettre des jetons de sécurité pour Azure AD et de configurer Azure AD pour approuver les jetons de cette instance d’AD FS spécifique. Dans un premier temps, cette page vous permet de configurer un seul domaine. Par la suite, vous pouvez configurer des domaines supplémentaires à tout moment en rouvrant Azure AD Connect.

 
![Domaine Azure AD](./media/active-directory-aadconnect-get-started-custom/adfs6.png)

 
### Tâches supplémentaires pour terminer la configuration de la fédération
Les tâches supplémentaires suivantes sont requises pour terminer la configuration de la fédération.

- Configurez les enregistrements DNS pour le nom de service de fédération AD FS (par exemple, adfs.contoso.com) pour l’intranet (votre serveur DNS interne) et l’extranet (le DNS public via votre bureau d’enregistrement de domaines). Pour l’enregistrement DNS intranet, vérifiez que vous utilisez des enregistrements A et non des enregistrements CNAME. Ceci est requis pour le bon fonctionnement de l’authentification Windows à partir de l’ordinateur associé à votre domaine.
- Si vous déployez plusieurs serveurs AD FS ou serveurs proxy d’application web, vérifiez que vous avez configuré votre équilibreur de charge et que les enregistrements DNS pour le nom de service de fédération AD FS (par ex. adfs.contoso.com) pointent vers l’équilibreur de charge.
- Pour que l’authentification Windows intégrée fonctionne avec les applications de navigateur utilisant Internet Explorer dans votre intranet, vérifiez que le nom de service de fédération AD FS (par exemple, adfs.contoso.com) est ajouté à la zone Intranet dans Internet Explorer. Vous pouvez vérifier cela via la stratégie de groupe, puis procéder au déploiement vers tous les ordinateurs associés à votre domaine. 

### Vérification de votre configuration de fédération

Lorsque vous cliquez sur le bouton Vérifier, Azure AD Connect vérifie la configuration DNS pour vous.

![Complete](./media/active-directory-aadconnect-get-started-custom/adfs7.png)
 
 
Veuillez également vérifier les points suivants :

- Validez la connexion du navigateur à partir d’un ordinateur associé à votre domaine à partir d’Internet Explorer vers l’intranet : connectez-vous à https://myapps.microsoft.com et vérifiez la connexion avec votre compte connecté.
- Validez la connexion du navigateur à partir de n’importe quel appareil à partir de l’extranet : sur un ordinateur personnel ou un appareil mobile, connectez-vous à https://myapps.microsoft.com et entrez votre ID de connexion et votre mot de passe.
- Validez la connexion à un client complet : connectez-vous à https://testconnectivity.microsoft.com, sélectionnez l’onglet « Office 365 », puis « Test d’authentification unique dans Office 365 ».

### Configuration facultative de votre service AD FS
Vous pouvez personnaliser l’illustration et l’image de logo de vos pages de connexion AD FS en vous connectant à AD FS, puis en utilisant PSH pour effectuer cette configuration.
	
	Set-AdfsWebTheme -TargetName default -Logo @{path="c:\Contoso\logo.png"} –Illustration @{path=”c:\Contoso\illustration.png”}

<!---HONumber=Sept15_HO2-->