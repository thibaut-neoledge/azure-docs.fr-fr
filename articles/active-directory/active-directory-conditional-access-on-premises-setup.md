<properties
	pageTitle="Configuration d’un accès conditionnel en local à l’aide du service Azure Active Directory Device Registration | Microsoft Azure"
	description="Guide étape par étape permettant d’activer l’accès conditionnel à des applications locales à l’aide des services AD FS (Active Directory Federation Services) dans Windows Server 2012 R2"
	services="active-directory"
	documentationCenter=""
	authors="femila"
	manager="stevenpo"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/19/2015"
	ms.author="femila"/>

# Configuration d’un accès conditionnel en local à l’aide du service d’inscription d’appareils Azure Active Directory

Les appareils dont vos utilisateurs sont personnellement propriétaires peuvent être marqués comme étant connus par votre organisation en demandant aux utilisateurs de joindre leurs appareils à l’espace de travail du service Azure Active Directory Device Registration. Voici un guide étape par étape permettant d’activer l’accès conditionnel à des applications locales à l’aide des services AD FS (Active Directory Federation Services) dans Windows Server 2012 R2.

> [AZURE.NOTE]Une licence Office 365 ou Azure AD Premium est nécessaire pour utiliser des appareils enregistrés dans les stratégies d’accès conditionnel du service Azure Active Directory Device Registration. Ces stratégies incluent les stratégies appliquées par les services AD FS (Active Directory Federation Services) pour les ressources locales.

Pour plus d’informations sur les scénarios d’accès conditionnel en local, consultez [Joindre un espace de travail à partir de n’importe quel appareil en utilisant l’authentification unique et l’authentification de second facteur transparente pour accéder aux applications de l’entreprise](https://technet.microsoft.com/library/dn280945.aspx). Ces fonctionnalités sont disponibles pour les clients qui achètent une licence Azure Active Directory Premium.

Périphériques pris en charge
-------------------------------------------------------------------------
* Appareils Windows 7 joints à un domaine
* Appareils Windows 8.1 personnels et joints à un domaine
* iOS 6 et versions ultérieures
* Android 4.0 ou versions ultérieures, Samsung GS3 ou téléphones de versions supérieures, Samsung Note2 ou tablettes de versions supérieures

Configuration requise pour le scénario
------------------------------------------------------------------------
* Windows Server Active Directory (Windows Server 2008 ou version ultérieure)
* Schéma mis à jour dans Windows Server 2012 R2
* Abonnement à Azure Active Directory Premium
* Windows Server 2012 R2 Federation Services, configuré pour l’authentification unique à Azure AD
* Proxy d’application web Windows Server 2012 R2
* Outil de synchronisation d’annuaires (DirSync) avec réécriture d’objets d’appareil. [Téléchargez la dernière version de dirsync.exe.](http://go.microsoft.com/fwlink/?LinkID=278924)

Problèmes connus dans cette version
-------------------------------------------------------------------------------
* Les stratégies d’accès conditionnel basées sur les appareils nécessitent la réécriture d’objets d’appareil dans Active Directory depuis Azure Active Directory. La réécriture des objets d’appareil dans Active Directory peut prendre jusqu’à 3 heures.
* Les appareils iOS7 demandent systématiquement à l’utilisateur de sélectionner un certificat durant l’authentification des certificats clients. 
* Les appareils bêta iOS8 ne fonctionnent pas à l’heure actuelle.


## Déployer le service Azure Active Directory Device Registration
Utilisez ce guide pour déployer et configurer le service Azure Active Directory Device Registration au sein de votre organisation.

Ce guide part du principe que vous avez configuré Windows Server Active Directory et que vous avez souscrit un abonnement à Microsoft Azure Active Directory. Consultez la configuration requise ci-dessus.

Pour déployer le service Azure Active Directory Device Registration avec votre client Azure Active Directory, effectuez les tâches de la liste de vérification suivante, dans l’ordre. Quand un lien de référence vous mène à une rubrique conceptuelle, passez en revue cette dernière et revenez à cette liste de vérification pour effectuer les tâches restantes. Certaines tâches comprennent une étape de validation de scénario pour vous aider à confirmer que l’étape a été effectuée avec succès.

**Liste de vérification : activation du service Azure Active Directory Device Registration**

Suivez la liste de vérification ci-dessous pour activer et configurer le service Azure Active Directory Device Registration.

| Task | Référence |
|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------|
| Activer le service Device Registration dans votre client Azure Active Directory pour autoriser les appareils à rejoindre l’espace de travail. Par défaut, l’authentification multifacteur n’est pas activée pour le service. Toutefois, l’authentification multifacteur est recommandée au moment de l’inscription d’un appareil. Avant d’activer l’authentification multifacteur dans ADRS, vérifiez que les services AD FS sont configurés pour un fournisseur d’authentification multifacteur. | [Activer le service Azure Active Directory Device Registration](active-directory-conditional-access-device-registration-overview.md) |
| Les appareils détectent votre service Azure Active Directory Device Registration en recherchant des enregistrements DNS connus. Vous devez configurer le DNS de votre entreprise afin que les appareils puissent détecter le service Azure Active Directory Device Registration. | [Configurer la découverte du service Azure Active Directory Device Registration](active-directory-conditional-access-device-registration-overview.md) |

**Liste de vérification : déployer et configurer les services ADFS (Active Directory Federation Services) Windows Server 2012 R2 et configurer une relation de fédération avec Azure Active Directory** Le groupe de tâches suivant vous permet d’intégrer les services AD FS Windows Server 2012 R2 à Azure Active Directory. Ainsi, vous pouvez utiliser les appareils inscrits dans le service Azure Active Directory Device Registration pour les stratégies d’accès conditionnel d’AD FS.


| Task | Référence |
|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------------------------|
| Déployer un domaine Services de domaine Active Directory avec les extensions de schéma Windows Server 2012 R2. (Vous n’avez pas besoin de mettre à niveau vos contrôleurs de domaine vers Windows Server 2012 R2. Seule la mise à niveau des schémas est nécessaire.) | Mettre à niveau le schéma des services de domaine Active Directory |
| Déployer les services de fédération Windows Server 2012 R2 conjointement avec le proxy d’application web | Déployer les services de fédération Windows Server 2012 R2 conjointement avec le proxy d’application web |
| Vous devez configurer votre forêt Services de domaine Active Directory avec les objets et conteneurs nécessaires à la prise en charge des objets d’appareil. Vous devez également activer l’authentification d’appareils dans AD FS. | Préparer votre forêt Active Directory à la prise en charge d’appareils |
| Configurer une relation de fédération avec votre organisation et Azure Active Directory. Cette étape vous guide dans la configuration de votre client Azure Active Directory avec vos services AD FS Windows Server 2012 R2. | Configurer une relation de fédération avec Azure Active Directory et configurer l’outil de synchronisation d’annuaires |
| Configurer Directory Sync (DirSync) pour permettre la réécriture d’objets d’appareil. Les appareils créés dans Azure Active Directory sont écrits dans Active Directory. | Configurer DirSync pour permettre la réécriture d’objets d’appareil |
| Nous vous recommandons vivement de configurer un fournisseur d’authentification multifacteur avec les services AD FS Windows Server 2012 R2. Ainsi, vos utilisateurs peuvent inscrire leurs appareils de manière sécurisée à l’aide de l’authentification multifacteur. | Configurer les services de fédération pour fournir l’authentification multifacteur |


### Mettre à niveau le schéma des services de domaine Active Directory
> [AZURE.NOTE]La mise à niveau de votre schéma Active Directory ne peut pas être annulée. Nous vous conseillons de procéder à cette opération dans un environnement de test.

1. Connectez-vous à votre contrôleur de domaine avec un compte disposant des droits d’administrateur d’entreprise et d’administrateur de schéma.
2. Copiez le répertoire [media]\\support\\adprep et les sous-répertoires vers l’un de vos contrôleurs de domaine Active Directory. 
3. [media] correspond au chemin d’accès du support d’installation de Windows Server 2012 R2.
4. À partir d’une invite de commandes, accédez au répertoire adprep et exécutez la commande suivante : Suivez les instructions à l’écran pour terminer la mise à niveau du schéma.

### Déployer les services de fédération Windows Server 2012 R2 conjointement avec le proxy d’application web
Suivez les deux guides ci-dessous pour déployer les services de fédération Windows Server 2012 R2 avec le proxy d’application web, puis revenez à ce guide. Suivez les instructions applicables au déploiement d’une batterie de serveurs de fédération Windows Server 2012 R2.

> [AZURE.NOTE]Quand vous déployez AD FS, vous devez ignorer l’étape facultative intitulée « Étape facultative : configurer un serveur de fédération avec le service d’inscription pour appareils (DRS) ». Cette étape n’est pas nécessaire si vous utilisez le service Azure Active Directory Device Registration. Suivez les instructions applicables au déploiement d’un serveur proxy de fédération Windows Server 2012 R2.

Installez la dernière mise à jour pour Windows Server 2012 R2 sur tous vos serveurs de fédération et serveurs proxy Windows Server 2012 R2. Cette opération peut être effectuée avant ou après avoir configuré votre batterie de serveurs de fédération. La mise à jour est disponible à cette adresse : Mise à jour Windows Server 2012 R2.


### Préparer votre forêt Active Directory à la prise en charge d’appareils

> [AZURE.NOTE]Il s’agit d’une opération ponctuelle que vous devez effectuer pour préparer votre forêt Active Directory à la prise en charge d’appareils. Pour accomplir cette procédure, vous devez être connecté avec les autorisations d’administrateur d’entreprise et votre forêt Active Directory doit avoir le schéma Windows Server 2012 R2.

1. Sur votre serveur de fédération, ouvrez une fenêtre de commande Windows PowerShell et tapez : *Initialize-ADDeviceRegistration*

2. Quand vous êtes invité à renseigner le ServiceAccountName, entrez le nom du compte de service que vous avez sélectionné pour AD FS. S’il s’agit d’un compte de service administré de groupe, entrez le nom au format domaine\\nom\_compte$. Pour un compte de domaine, utilisez le format domaine\\nom\_compte.


### Activer l’authentification d’appareils dans AD FS

1. Sur votre serveur de fédération, ouvrez la console de gestion AD FS et accédez à **AD FS** > **Stratégies d’authentification**.
2. Sélectionnez **Modifier l’authentification principale globale** dans le volet **Actions**.
3. Cochez **Activer l’authentification des appareils**, puis sélectionnez **OK**.
4. Par défaut, AD FS supprime régulièrement les appareils non utilisés d’Active Directory. Vous devez désactiver cette tâche pendant l’utilisation du service Azure Active Directory Device Registration afin que les appareils puissent être gérés dans Azure.


### Désactiver le nettoyage des appareils non utilisés
1. Sur votre serveur de fédération, ouvrez une fenêtre de commande Windows PowerShell et tapez : `*Set-AdfsDeviceRegistration -MaximumInactiveDays 0*`


###Configurer une relation de fédération avec Azure Active Directory et configurer l’outil de synchronisation d’annuaires
La section suivante vous aide à configurer une relation de fédération entre Azure Active Directory et AD FS. Certaines étapes peuvent vous amener à quitter cette page. Suivez les instructions ci-dessous, puis revenez à ce guide.

Intégrer Azure Active Directory au service Active Directory local et configurer l’authentification unique
------------------------------------------------------------------------------------------------------

1. Connectez-vous au portail Azure en tant qu’**administrateur**.
2. Dans le volet gauche, sélectionnez **Active Directory**.
3. Sous l’onglet **Annuaire**, sélectionnez votre annuaire.
4. Sélectionnez l’onglet **Intégration d’annuaire**.
5. Sous la section concernant l’intégration au répertoire Active Directory local, recherchez l’option Synchronisation d’annuaires et sélectionnez Activé.
6. Suivez les étapes 1 à 4 pour intégrer Azure Active Directory à votre annuaire local.
  1. **Ajoutez des domaines**.
  2. **Configurez l’authentification unique et préparez la synchronisation d’annuaires**. Si vous avez déjà déployé AD FS, vous pouvez suivre ces sous-sections pour configurer et valider une relation d’approbation entre AD FS et Azure AD. 
      *  [Installer Windows PowerShell pour l’authentification unique avec AD FS](https://msdn.microsoft.com/library/azure/jj151814.aspx)
      *  [Configurer une relation d’approbation entre AD FS et Azure AD](https://msdn.microsoft.com/library/azure/jj205461.aspx)
      *  [Vérifier et gérer l’authentification unique avec AD FS](https://msdn.microsoft.com/library/azure/jj151809.aspx)
  	  *  [Références AD FS supplémentaires](https://msdn.microsoft.com/library/azure/dn151321.aspx)

  3. Installez et exécutez l’outil de synchronisation d’annuaires. Si vous avez déjà installé DirSync, vous devez procéder à la mise à niveau vers la version la plus récente. La version minimale requise est 6862.0000.
  4. Vérifiez et gérez la synchronisation d’annuaires. 
  5. Configurez des règles de revendication supplémentaires sur l’objet d’approbation de la partie de confiance pour Azure Active Directory, généralement appelé plateforme d’identité Microsoft Office 365.

###Configurer les règles de revendication sur l’objet d’approbation de la partie de confiance Azure Active Directory

1. Ouvrez la console de gestion d’AD FS et accédez à AD FS > Relations d’approbation > **Approbations de partie de confiance**. Cliquez avec le bouton droit sur l’**objet d’approbation de partie de confiance de la plateforme d’identité Microsoft Office 365** et sélectionnez **Modifier les règles de revendication.
2. Sous l’onglet **Règles de transformation d’émission**, sélectionnez Ajouter une règle.**
3. Sélectionnez **Envoyer les revendications en utilisant une règle personnalisée** dans la zone de liste déroulante **Modèle de règle de revendication**. Sélectionnez **Suivant**.
4. Tapez Règle de revendication de la méthode d’authentification dans la zone de texte **Nom de la règle de revendication**.
5. Tapez la règle de revendication suivante dans la zone de texte Nom de la règle de revendication :

    ` c:[Type == "http://schemas.microsoft.com/claims/authnmethodsreferences"]` `=> issue(claim = c);`

6. Cliquez sur **OK** à deux reprises pour fermer la boîte de dialogue.
7. Configurez des références de classe d’authentification supplémentaires sur l’objet d’approbation de la partie de confiance pour Azure Active Directory, généralement appelé plateforme d’identité Microsoft Office 365.


###Configurer la référence à la classe d’authentification d’approbation de la partie de confiance Azure Active Directory

Sur votre serveur de fédération, ouvrez une fenêtre de commande Windows PowerShell et tapez :

*Set-AdfsRelyingPartyTrust -TargetName <RPObjectName> -AllowedAuthenticationClassReferences wiaormultiauthn*


Où <RPObjectName> est le nom de l’objet de partie de confiance pour votre objet d’approbation de partie de confiance Azure Active Directory. Cet objet est généralement nommé plateforme d’identité Microsoft Office 365.

### Configurer DirSync pour permettre la réécriture d’objets d’appareil
Vous devez configurer Directory Sync (DirSync) pour permettre la réécriture d’objets d’appareil. Les appareils créés dans Azure Active Directory sont écrits dans Active Directory. La réécriture dans Active Directory des appareils créés dans Azure Active Directory peut prendre jusqu’à 3 heures.
>[Azure.Note]Vous devez être connecté avec des autorisations d’administrateur d’entreprise pour effectuer la procédure suivante. Téléchargez la dernière version de dirsync.exe ici.

> Une licence Office 365 ou Azure AD Premium est nécessaire pour autoriser les réécritures des appareils dans Active Directory.

> Si vous venez de terminer l’Assistant Installation de DirSync, déconnectez-vous et reconnectez-vous avant de continuer. Cela permet d’assurer la mise à jour de votre jeton d’accès.

>La réécriture des objets d’appareil dans les services de domaine Active Directory peut prendre jusqu’à 3 heures.

1. Sur votre serveur de synchronisation d’annuaires, ouvrez une fenêtre de commande Windows PowerShell et exécutez les commandes suivantes :

    `Import-Module DirSync`
    
    `$AADCreds = Get-Credential`
    
    `$ADCreds = Get-Credential`

2. Quand vous êtes invité à entrer vos informations d’identification, indiquez celles de votre compte d’administrateur de service cloud et celles de l’administrateur Active Directory.

*Enable-MSOnlineObjectManagement –ObjectTypes Device –TargetCredential $AADCreds -Credential $ADCreds*

### Configurer les services de fédération pour fournir l’authentification multifacteur
Les appareils inscrits auprès de votre organisation peuvent être utilisés en toute transparence en tant que deuxième facteur d’authentification. C’est pourquoi nous vous conseillons de demander une authentification forte au moment de l’inscription d’un appareil. Vous pouvez suivre le guide ci-dessous pour configurer AD FS avec Azure Multi-Factor Authentication, puis revenir à ce guide. Suivez les instructions permettant de déployer Azure Multi-Factor Authentication.

## Joindre des appareils à votre espace de travail à l’aide du service Azure Active Directory Device Registration

### Joindre un appareil iOS à l’aide du service Azure Active Directory Device Registration

Pour les appareils iOS, le service Azure Active Directory Device Registration utilise le processus d’inscription de profil « Over-the-Air ». Ce processus commence par la connexion de l’utilisateur à l’URL d’inscription de profil en utilisant le navigateur web Safari. Le format de l’URL est le suivant :

    https://enterpriseregistration.windows.net/enrollmentserver/otaprofile/"yourdomainname"

Où <yourdomainname> correspond au nom du domaine que vous avez configuré avec Azure Active Directory. Par exemple, si votre nom de domaine est contoso.com, l’URL est :

    https://enterpriseregistration.windows.net/enrollmentserver/otaprofile/contoso.com

Il existe de nombreux moyens de communiquer cette URL à vos utilisateurs. Nous vous conseillons de publier cette URL dans un message d’application personnalisé de refus d’accès dans AD FS. Ce sujet est traité dans la prochaine section : Créer une stratégie d’accès aux applications et un message personnalisé de refus d’accès.

###Joindre un appareil Windows 8.1 à l’aide du service Azure Active Directory Device Registration

1. Sur votre appareil Windows 8.1, accédez à **Paramètres du PC** > **Réseau** > **Lieu de travail**.
2. Entrez votre nom d’utilisateur au format UPN. Par exemple, dan@contoso.com..
3. Sélectionnez **Joindre**.
4. Quand vous y êtes invité, connectez-vous avec vos informations d’identification. L’appareil est à présent joint.

### Joindre un appareil Android à l’aide du service Azure Active Directory Device Registration

La rubrique Azure Authenticator pour Android comporte des instructions sur l’installation de l’application Azure Authenticator sur votre appareil Android et l’ajout d’un compte professionnel. Quand un compte professionnel est créé avec succès sur un appareil Android, ce dernier est joint à l’espace de travail de l’organisation.

### Vérifier la réécriture des appareils inscrits dans Active Directory
Vous pouvez utiliser LDP.exe et l’Éditeur ADSI pour afficher les objets d’appareil et ainsi vérifier qu’ils ont bien été réécrits dans votre service Active Directory. Ces deux outils sont disponibles dans les outils d’administrateur Active Directory.

Par défaut, les objets d’appareil réécrits à partir d’Azure Active Directory sont placés dans le même domaine que votre batterie de serveurs AD FS.

`CN=RegisteredDevices,defaultNamingContext`

###Créer une stratégie d’accès aux applications et un message personnalisé de refus d’accès
Considérez le scénario suivant : vous créez une approbation de partie de confiance d’application dans AD FS et configurez une règle d’autorisation d’émission autorisant uniquement les appareils enregistrés. À présent, seuls les appareils inscrits sont autorisés à accéder à l’application. Afin de faciliter l’accès à l’application à vos utilisateurs, vous configurez un message personnalisé de refus d’accès qui inclut des instructions leur permettant de joindre leur appareil. Vos utilisateurs peuvent à présent enregistrer de manière transparente leurs appareils afin d’accéder à une application.

Les étapes suivantes vous expliquent comment implémenter ce scénario.
>[AZURE.NOTE]Cette section part du principe que vous avez déjà configuré une approbation de partie de confiance pour votre application dans AD FS.

1. Ouvrez l’outil MMC AD FS et accédez à AD FS > Relations d’approbation > Approbations de partie de confiance.
2. Recherchez l’application à laquelle doit s’appliquer la nouvelle règle d’accès. Cliquez avec le bouton droit sur l’application et sélectionnez Modifier les règles de revendication.
3. Sélectionnez l’onglet **Règles d’autorisation d’émission**, puis sélectionnez **Ajouter une règle**.
4. Dans la liste déroulante **Modèle de règle de revendication**, sélectionnez **Autoriser ou refuser l’accès des utilisateurs en fonction d’une revendication entrante**. Sélectionnez **Suivant**.
5. Dans le champ Nom de la règle de revendication, tapez **Autoriser l’accès aux appareils inscrits**.
6. Dans la liste déroulante Type de revendication entrante, sélectionnez **Est un utilisateur inscrit**.
7. Dans le champ Valeur de revendication entrante, tapez **true**.
9. Sélectionnez **Terminer**, puis **Appliquer**.
10. Supprimez les règles moins strictes que celle que vous venez de créer. Par exemple, supprimez la règle par défaut Autoriser l’accès à tous les utilisateurs. 
8. Sélectionnez la case d’option **Autoriser l’accès aux utilisateurs avec cette revendication entrante**.

Votre application est à présent configurée pour autoriser l’accès uniquement quand une personne utilise un appareil qu’elle a inscrit et qu’elle a joint à l’espace de travail. Pour des stratégies d’accès avancées, consultez Gérer les risques avec le contrôle d’accès multifacteur.

Vous allez ensuite configurer un message d’erreur personnalisé pour votre application. Ce message informe les utilisateurs qu’ils doivent joindre leurs appareils à l’espace de travail avant de pouvoir accéder à l’application. Vous pouvez créer un message personnalisé d’application de refus d’accès à l’aide de code HTML personnalisé et de Windows PowerShell.

Sur votre serveur de fédération, ouvrez une fenêtre de commande Windows PowerShell et tapez la commande suivante. Remplacez certaines parties de la commande par des éléments propres à votre système.

`Set-AdfsRelyingPartyWebContent -Name "relying party trust name" -ErrorPageAuthorizationErrorMessage ` Vous devez inscrire votre appareil avant d’accéder à cette application.

**Si vous utilisez un appareil iOS, cliquez sur ce lien pour joindre votre appareil** :

    a href='https://enterpriseregistration.windows.net/enrollmentserver/otaprofile/yourdomain.com

Joindre cet appareil iOS à votre espace de travail.


**Si vous utilisez un appareil Windows 8.1**, vous pouvez joindre votre appareil en accédant à **Paramètres du PC** > **Réseau** > **Lieu de travail**.


Où « **relying party trust name** » correspond au nom de l’objet d’approbation de la partie de confiance de vos applications dans AD FS. Où yourdomain.com correspond au nom du domaine que vous avez configuré avec Azure Active Directory. Par exemple, contoso.com. Assurez-vous de supprimer les sauts de ligne (le cas échéant) dans le contenu HTML que vous transmettez à l’applet de commande **Set-AdfsRelyingPartyWebContent**.

<!---HONumber=August15_HO9-->