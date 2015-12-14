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
	ms.date="11/24/2015"
	ms.author="femila"/>


# Configuration d’un accès conditionnel en local à l’aide du service d’inscription d’appareils Azure Active Directory

Les appareils dont vos utilisateurs sont personnellement propriétaires peuvent être marqués comme étant connus par votre organisation en demandant aux utilisateurs de joindre leurs appareils à l’espace de travail du service Azure Active Directory Device Registration. Voici un guide étape par étape permettant d’activer l’accès conditionnel à des applications locales à l’aide des services AD FS (Active Directory Federation Services) dans Windows Server 2012 R2.

> [AZURE.NOTE]Une licence Office 365 ou Azure AD Premium est nécessaire pour utiliser des appareils enregistrés dans les stratégies d’accès conditionnel du service Azure Active Directory Device Registration. Ces stratégies incluent les stratégies appliquées par les services AD FS (Active Directory Federation Services) pour les ressources locales.

Pour plus d’informations sur les scénarios d’accès conditionnel en local, consultez [Joindre un espace de travail à partir de n’importe quel appareil en utilisant l’authentification unique et l’authentification de second facteur transparente pour accéder aux applications de l’entreprise](https://technet.microsoft.com/library/dn280945.aspx).

Ces fonctionnalités sont disponibles pour les clients qui achètent une licence Azure Active Directory Premium.

Appareils pris en charge
-------------------------------------------------------------------------
* Appareils Windows 7 joints à un domaine
* Appareils Windows 8.1 personnels et joints à un domaine
* iOS 6 et versions ultérieures, pour le navigateur Safari
* Android 4.0 ou versions ultérieures, Samsung GS3 ou téléphones de versions supérieures, Samsung Note2 ou tablettes de versions supérieures


Configuration requise pour le scénario
------------------------------------------------------------------------
* Abonnement à Office 365 ou Azure Active Directory Premium
* Locataire Azure Active Directory
* Windows Server Active Directory (Windows Server 2008 ou version ultérieure)
* Schéma mis à jour dans Windows Server 2012 R2
* Abonnement à Azure Active Directory Premium
* Windows Server 2012 R2 Federation Services, configuré pour l’authentification unique à Azure AD
* Windows Server 2012 R2 Proxy Application Web Microsoft Azure Active Directory Connect (Azure AD Connect). [Téléchargez Azure AD Connect ici](http://www.microsoft.com/fr-FR/download/details.aspx?id=47594).
* Domaine vérifié. 

Problèmes connus dans cette version
-------------------------------------------------------------------------------
* Les stratégies d’accès conditionnel basées sur les appareils nécessitent la réécriture d’objets d’appareil dans Active Directory depuis Azure Active Directory. La réécriture des objets d'appareil dans Active Directory peut prendre jusqu'à 3 heures.
* Les appareils iOS 7 demandent systématiquement à l'utilisateur de sélectionner un certificat durant l'authentification des certificats clients. 
* Certaines versions d'iOS8, avant iOS 8.3 ne fonctionnent pas. 

## Hypothèses de scénario
Ce scénario suppose que vous disposez d'un environnement hybride comprenant un locataire Azure AD et un répertoire actif local. Ces locataires doivent être connectés à l'aide d'Azure AD Connect, d'un domaine vérifié et AD FS pour l'authentification unique. La liste de vérification ci-dessous vous aidera à configurer votre environnement jusqu'à l'étape décrite ci-dessus.

Liste de vérification : configuration requise pour le scénario d'accès conditionnel
--------------------------------------------------------------
Connectez votre locataire Azure AD à votre Active Directory local.

## Configuration du service Azure Active Directory Device Registration
Utilisez ce guide pour déployer et configurer le service Azure Active Directory Device Registration au sein de votre organisation.

Ce guide part du principe que vous avez configuré Windows Server Active Directory et que vous avez souscrit un abonnement à Microsoft Azure Active Directory. Consultez la configuration requise ci-dessus.

Pour déployer le service Azure Active Directory Device Registration avec votre client Azure Active Directory, effectuez les tâches de la liste de vérification suivante, dans l’ordre. Quand un lien de référence vous mène à une rubrique conceptuelle, passez en revue cette dernière et revenez à cette liste de vérification pour effectuer les tâches restantes. Certaines tâches comprennent une étape de validation de scénario pour vous aider à confirmer que l’étape a été effectuée avec succès.

## 1ère partie : Activer le service Azure Active Directory Device Registration

Suivez la liste de vérification ci-dessous pour activer et configurer le service Azure Active Directory Device Registration.

| Task | Référence |
|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------|
| Activer le service Device Registration dans votre client Azure Active Directory pour autoriser les appareils à rejoindre l’espace de travail. Par défaut, l’authentification multifacteur n’est pas activée pour le service. Toutefois, l’authentification multifacteur est recommandée au moment de l’inscription d’un appareil. Avant d’activer l’authentification multifacteur dans ADRS, vérifiez que les services AD FS sont configurés pour un fournisseur d’authentification multifacteur. | [Activer le service Azure Active Directory Device Registration](active-directory-conditional-access-device-registration-overview.md) |
| Les appareils détectent votre service Azure Active Directory Device Registration en recherchant des enregistrements DNS connus. Vous devez configurer le DNS de votre entreprise afin que les appareils puissent détecter le service Azure Active Directory Device Registration. | [Configurer la découverte du service Azure Active Directory Device Registration](active-directory-conditional-access-device-registration-overview.md) |

##2ème partie : Déployer et configurer Windows Server 2012 R2 Active Directory Federation Services et configurer une relation de fédération avec Azure AD

| Task | Référence |
|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------|
| Déployer un domaine Services de domaine Active Directory avec les extensions de schéma Windows Server 2012 R2. Vous n'avez pas besoin de mettre à niveau vos contrôleurs de domaine vers Windows Server 2012 R2. Seule la mise à niveau des schémas est nécessaire. | [Mise à niveau de votre schéma Services de domaine Active Directory] (#Upgrade your Active Directory Domain Services Schema) |
| Les appareils détectent votre service Azure Active Directory Device Registration en recherchant des enregistrements DNS connus. Vous devez configurer le DNS de votre entreprise afin que les appareils puissent détecter le service Azure Active Directory Device Registration. | [Préparation de vos appareils de prise en charge Active Directory] (#Prepare your Active Directory to support devices) |


##3ème partie : Activer l'écriture différée des appareils dans Azure AD

| Task | Référence |
|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------|
| Terminez la 2ème partie de l'activation de l'écriture différée des appareils dans Azure AD Connect. Une fois que vous avez terminé, revenez à ce guide. | [Activation de l'écriture différée des appareils dans Azure AD Connect] (#Upgrade your Active Directory Domain Services Schema) |
	 

##[Facultatif] 4ème partie : Activer l'authentification multifacteur

Nous vous recommandons fortement de configurer l'une des différentes options pour l'authentification multifacteur. Si vous voulez avoir recours à l'authentification multifacteur, consultez [Choix de la solution de sécurité multifacteur la plus appropriée pour vous](multi-factor-authentication-get-started.md). Cette rubrique inclut une description de chaque solution et des liens pour vous aider à configurer la solution de votre choix.

## 5ème partie : Vérification

Le déploiement est maintenant terminé. Vous pouvez désormais tester certains scénarios. Suivez les liens ci-dessous pour tester le service et vous familiariser avec les fonctionnalités.


| Task | Référence |
|------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|---------------------------------------------------------------------------------|
| Joindre des appareils à votre espace de travail à l'aide du service Azure Active Directory Device Registration. Vous pouvez joindre des appareils iOS, Windows et Android | [Joindre des appareils à votre espace de travail à l'aide du service Azure Active Directory Device Registration](#Join devices to your workplace using Azure Active Directory Device Registration) |
| Vous pouvez afficher et activer/désactiver les appareils inscrits via le portail d'administration. Dans cette tâche, vous allez afficher des appareils inscrits via le portail d'administration. | [Vue d’ensemble du service d’inscription d’appareil Azure Active Directory](active-directory-conditional-access-device-registration-overview.md) |
| Vérifier que les objets d'appareil sont réécrits depuis Azure Active Directory vers Windows Server Active Directory. | [Vérifier que les appareils inscrits sont réécrits dans Active Directory](#Verify registered devices are written-back to Active Director) |
| Maintenant que les utilisateurs peuvent inscrire leurs appareils, vous pouvez créer des stratégies d'accès à l'application dans AD FS qui autorisent uniquement les appareils inscrits. Dans cette tâche, vous allez créer une règle d'accès à l'application et message personnalisé d'accès refusé. | [Créer une stratégie d'accès à l'application et un message personnalisé d'accès refusé](#Create an application access policy and custom access denied message) |



## Intégrer Azure Active Directory au service Active Directory local
Ceci vous aidera à intégrer votre locataire Azure AD dans votre service Active Directory local à l'aide d'Azure AD Connect. Bien que les étapes soient disponibles dans le portail Azure, notez les instructions spécifiques répertoriées dans cette section.

1.	Ouvrez une session sur le portail Azure en tant qu’administrateur.
2.	Dans le volet gauche, sélectionnez **Active Directory**.
3.	Sous l'onglet **Répertoire**, sélectionnez votre répertoire.
4.	Sélectionnez l'onglet **Intégration de répertoires**.
5.	Dans la section de **déploiement et de gestion**, suivez les étapes 1 à 3 pour intégrer Azure Active Directory à votre répertoire local.
  1.	Ajoutez des domaines.
  2.	Installez et exécutez Azure AD Connect : installez Azure AD Connect à l'aide des instructions suivantes, [Installation personnalisée d'Azure AD Connect](active-directory-aadconnect-get-started-custom.md).
  3. Vérifiez et gérez la synchronisation des répertoires. Les instructions de l'authentification unique sont disponibles dans cette étape. >[AZURE.NOTE]Configurez la fédération avec AD FS comme indiqué dans le document lié ci-dessus. >[AZURE.NOTE]Il est inutile de configurer les fonctionnalités d'aperçu.
  
   


## Mettre à niveau le schéma des services de domaine Active Directory
> [AZURE.NOTE]La mise à niveau de votre schéma Active Directory ne peut pas être annulée. Nous vous conseillons de procéder à cette opération tout d'abord dans un environnement de test.

1. Connectez-vous à votre contrôleur de domaine avec un compte disposant des droits d’administrateur d’entreprise et d’administrateur de schéma.
2. Copiez le répertoire **[media]\\support\\adprep** et les sous-répertoires vers l'un de vos contrôleurs de domaine Active Directory. 
3. [media] correspond au chemin d’accès du support d’installation de Windows Server 2012 R2.
4. À partir d'une invite de commandes, accédez au répertoire adprep et exécutez la commande suivante : **adprep.exe /forestprep**. Suivez les instructions à l’écran pour terminer la mise à niveau du schéma.

## Préparation de votre Active Directory à la prise en charge d'appareils
>[AZURE.NOTE]Il s’agit d’une opération ponctuelle que vous devez effectuer pour préparer votre forêt Active Directory à la prise en charge d’appareils. Pour accomplir cette procédure, vous devez être connecté avec les autorisations d’administrateur d’entreprise et votre forêt Active Directory doit avoir le schéma Windows Server 2012 R2.


##Préparer votre forêt Active Directory à la prise en charge d’appareils

> [AZURE.NOTE]Il s’agit d’une opération ponctuelle que vous devez effectuer pour préparer votre forêt Active Directory à la prise en charge d’appareils. Pour accomplir cette procédure, vous devez être connecté avec les autorisations d’administrateur d’entreprise et votre forêt Active Directory doit avoir le schéma Windows Server 2012 R2.

### Préparation de votre forêt Active Directory

1.	Sur votre serveur de fédération, ouvrez une fenêtre de commande Windows PowerShell et tapez : Initialize-ADDeviceRegistration
2.	Quand vous êtes invité à renseigner le **ServiceAccountName**, entrez le nom du compte de service que vous avez sélectionné pour AD FS. S'il s'agit d'un compte de service administré de groupe, entrez le nom au format **domaine\\nomcompte$**. Pour un compte de domaine, utilisez le format **domaine\\nomcompte**.



### Activer l’authentification d’appareils dans AD FS

1. Sur votre serveur de fédération, ouvrez la console de gestion AD FS et accédez à **AD FS** > **Stratégies d'authentification**.
2. Sélectionnez **Modifier l'authentification principale globale** dans le volet **Actions**.
3. Cochez **Activer l'authentification des appareils**, puis sélectionnez **OK**.
4. Par défaut, AD FS supprime régulièrement les appareils non utilisés d’Active Directory. Vous devez désactiver cette tâche pendant l’utilisation du service Azure Active Directory Device Registration afin que les appareils puissent être gérés dans Azure.


### Désactiver le nettoyage des appareils non utilisés
1. Sur votre serveur de fédération, ouvrez une fenêtre de commande Windows PowerShell et tapez : Set-AdfsDeviceRegistration -MaximumInactiveDays 0

### Préparation d'Azure AD Connect pour l'écriture différée des appareils

1.	Terminez la 1ère partie : préparer AAD Connect. 


## Joindre des appareils à votre espace de travail à l’aide du service Azure Active Directory Device Registration

### Joindre un appareil iOS à l’aide du service Azure Active Directory Device Registration

Pour les appareils iOS, le service Azure Active Directory Device Registration utilise le processus d’inscription de profil « Over-the-Air ». Ce processus commence par la connexion de l’utilisateur à l’URL d’inscription de profil en utilisant le navigateur web Safari. Le format de l’URL est le suivant :

    https://enterpriseregistration.windows.net/enrollmentserver/otaprofile/"yourdomainname"

Où `yourdomainname` correspond au nom du domaine que vous avez configuré avec Azure Active Directory. Par exemple, si votre nom de domaine est contoso.com, l’URL est :

    https://enterpriseregistration.windows.net/enrollmentserver/otaprofile/contoso.com

Il existe de nombreux moyens de communiquer cette URL à vos utilisateurs. Nous vous conseillons de publier cette URL dans un message d’application personnalisé de refus d’accès dans AD FS. Ceci est traité dans la section suivante : [Créer une stratégie d'accès à l'application et un message personnalisé d'accès refusé](#Create an application access policy and custom access denied message).

###Joindre un appareil Windows 8.1 à l’aide du service Azure Active Directory Device Registration

1. Sur votre appareil Windows 8.1, accédez à **Paramètres du PC** > **Réseau** > **Espace de travail**.
2. Entrez votre nom d’utilisateur au format UPN. Par exemple, dan@contoso.com..
3. Sélectionnez **Joindre**.
4. Quand vous y êtes invité, connectez-vous avec vos informations d’identification. L’appareil est à présent joint.

### Joindre un appareil Android à l’aide du service Azure Active Directory Device Registration

La [rubrique Azure Authenticator pour Android](active-directory-conditional-access-azure-authenticator-app.md) comporte des instructions sur l'installation de l'application Azure Authenticator sur votre appareil Android et l'ajout d'un compte professionnel. Quand un compte professionnel est créé avec succès sur un appareil Android, ce dernier est joint à l’espace de travail de l’organisation.

## Vérifier la réécriture des appareils inscrits dans Active Directory
Vous pouvez utiliser LDP.exe et l’Éditeur ADSI pour afficher les objets d’appareil et ainsi vérifier qu’ils ont bien été réécrits dans votre service Active Directory. Ces deux outils sont disponibles dans les outils d’administrateur Active Directory.

Par défaut, les objets d’appareil réécrits à partir d’Azure Active Directory sont placés dans le même domaine que votre batterie de serveurs AD FS.

    CN=RegisteredDevices,defaultNamingContext

## Créer une stratégie d’accès aux applications et un message personnalisé de refus d’accès
Considérez le scénario suivant : vous créez une approbation de partie de confiance d’application dans AD FS et configurez une règle d’autorisation d’émission autorisant uniquement les appareils enregistrés. À présent, seuls les appareils inscrits sont autorisés à accéder à l’application. Afin de faciliter l’accès à l’application à vos utilisateurs, vous configurez un message personnalisé de refus d’accès qui inclut des instructions leur permettant de joindre leur appareil. Vos utilisateurs peuvent à présent enregistrer de manière transparente leurs appareils afin d’accéder à une application.

Les étapes suivantes vous expliquent comment implémenter ce scénario.
>[AZURE.NOTE]Cette section part du principe que vous avez déjà configuré une approbation de partie de confiance pour votre application dans AD FS.

1. Ouvrez l’outil MMC AD FS et accédez à AD FS > Relations d’approbation > Approbations de partie de confiance.
2. Recherchez l’application à laquelle doit s’appliquer la nouvelle règle d’accès. Cliquez avec le bouton droit sur l’application et sélectionnez Modifier les règles de revendication.
3. Sélectionnez l'onglet **Règles d'autorisation d'émission**, puis sélectionnez **Ajouter une règle**.
4. Dans la liste déroulante **Modèle de règle de revendication**, sélectionnez **Autoriser ou refuser l'accès des utilisateurs en fonction d'une revendication entrante**. Sélectionnez **Suivant**.
5. Dans le champ Nom de la règle de revendication, tapez **Autoriser l'accès aux appareils inscrits**.
6. Dans la liste déroulante Type de revendication entrante, sélectionnez **Est un utilisateur inscrit**.
7. Dans le champ Valeur de revendication entrante, tapez **true**.
8. Sélectionnez la case d'option **Autoriser l'accès aux utilisateurs avec cette revendication entrante**.
9. Sélectionnez **Terminer**, puis **Appliquer**.
10. Supprimez les règles moins strictes que celle que vous venez de créer. Par exemple, supprimez la règle par défaut **Autoriser l'accès à tous les utilisateurs**.

Votre application est à présent configurée pour autoriser l’accès uniquement quand une personne utilise un appareil qu’elle a inscrit et qu’elle a joint à l’espace de travail. Pour des stratégies d'accès avancées, consultez [Gérer les risques avec le contrôle d'accès multifacteur](https://technet.microsoft.com/library/dn280949.aspx).

Vous allez ensuite configurer un message d’erreur personnalisé pour votre application. Ce message informe les utilisateurs qu’ils doivent joindre leurs appareils à l’espace de travail avant de pouvoir accéder à l’application. Vous pouvez créer un message personnalisé d’application de refus d’accès à l’aide de code HTML personnalisé et de Windows PowerShell.

Sur votre serveur de fédération, ouvrez une fenêtre de commande Windows PowerShell et tapez la commande suivante. Remplacez certaines parties de la commande par des éléments propres à votre système.

    Set-AdfsRelyingPartyWebContent -Name "relying party trust name" -ErrorPageAuthorizationErrorMessage
Vous devez inscrire votre appareil avant d'accéder à cette application.

**Si vous utilisez un appareil iOS, cliquez sur ce lien pour joindre votre appareil** :

    a href='https://enterpriseregistration.windows.net/enrollmentserver/otaprofile/yourdomain.com

Joindre cet appareil iOS à votre espace de travail.


**Si vous utilisez un appareil Windows 8.1**, vous pouvez joindre votre appareil en accédant à **Paramètres du PC** > **Réseau** > **Espace de travail**.


Où « **relying party trust name** » correspond au nom de l'objet d'approbation de la partie de confiance de vos applications dans AD FS. Où **yourdomain.com** correspond au nom du domaine que vous avez configuré avec Azure Active Directory. Par exemple, contoso.com. Assurez-vous de supprimer les sauts de ligne (le cas échéant) dans le contenu HTML que vous transmettez à l'applet de commande **Set-AdfsRelyingPartyWebContent**.


Désormais, lorsque les utilisateurs accèdent à votre application à partir d'un appareil qui n'est pas enregistré auprès d'Azure Active Directory Device Registration Service, ils verront s'afficher une page qui ressemble à la capture d'écran ci-dessous.

![Capture d'écran d'une erreur lorsque les utilisateurs n'ont pas enregistré leur appareil auprès d'Azure AD](./media/active-directory-conditional-access/error-azureDRS-device-not-registered.gif)

<!---HONumber=AcomDC_1203_2015-->