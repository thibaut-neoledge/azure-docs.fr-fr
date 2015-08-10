<properties 
	pageTitle="Prise en main : gestion des mots de passe Azure AD | Microsoft Azure" 
	description="Permettez à vos utilisateurs de réinitialiser eux-même leurs mots de passe, découvrez la configuration requise pour la réinitialisation du mot de passe et activez l’écriture différée pour gérer des mots de passe en local dans Active Directory." 
	services="active-directory" 
	documentationCenter="" 
	authors="asteen" 
	manager="kbrint" 
	editor="billmath"/>

<tags 
	ms.service="active-directory" 
	ms.workload="identity" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/08/2015" 
	ms.author="asteen"/>

# Prise en main de la gestion de mot de passe
Pour permettre à vos utilisateurs de gérer eux-mêmes leurs mots de passe Azure Active Directory pour le cloud ou pour une utilisation en local, il suffit de suivre une procédure simple. Après avoir vérifié que vous disposez de la configuration requise, vous pouvez rapidement activer la modification et la réinitialisation des mots de passe pour toute votre organisation. Cet article présente les concepts suivants :

* [**Comment permettre aux utilisateurs de réinitialiser leurs mots de passe Active Directory Azure pour le cloud**](#enable-users-to-reset-their-azure-ad-passwords)
 - [Configuration requise pour la réinitialisation du mot de passe libre-service](#prerequisites)
 - [Étape 1 : configuration de la stratégie de réinitialisation du mot de passe](#step-1-configure-password-reset-policy)
 - [Étape 2 : ajout de données de contact pour votre utilisateur de test](#step-2-add-contact-data-for-your-test-user)
 - [Étape 3 : réinitialisation de votre mot de passe en tant qu’utilisateur](#step-3-reset-your-azure-ad-password-as-a-user)
* [**Comment permettre aux utilisateurs de réinitialiser leurs mots de passe Active Directory Azure pour une utilisation en local**](#enable-users-to-reset-or-change-their-ad-passwords)
 - [Configuration requise pour l’écriture différée de mot de passe](#writeback-prerequisites)
 - [Étape 1 : téléchargement de la dernière version d’Azure AD Connect](#step-1-download-the-latest-version-of-azure-ad-connect)
 - [Étape 2 : activation et vérification de l’écriture différée des mots de passe dans Azure AD Connect via l’interface utilisateur ou Powershell](#step-2-enable-password-writeback-in-azure-ad-connect)
 - [Étape 3 : configuration de votre pare-feu](#step-3-configure-your-firewall)
 - [Étape 4 : configuration des autorisations appropriées](#step-4-set-up-the-appropriate-active-directory-permissions)
 - [Étape 5 : réinitialisation de votre mot de passe Active Directory en tant qu’utilisateur, puis vérification de son fonctionnement](#step-5-reset-your-ad-password-as-a-user)

## Activation de la réinitialisation du mot de passe pour les utilisateurs Azure AD
Cette section explique comment activer la réinitialisation du mot de passe libre-service pour votre annuaire cloud AAD, en y inscrivant vos utilisateurs, puis comment effectuer un test de réinitialisation du mot de passe libre-service en tant qu’utilisateur.

- [Configuration requise pour la réinitialisation du mot de passe libre-service](#prerequisites)
- [Étape 1 : configuration de la stratégie de réinitialisation du mot de passe](#step-1-configure-password-reset-policy)
- [Étape 2 : ajout de données de contact pour votre utilisateur de test](#step-2-add-contact-data-for-your-test-user)
- [Étape 3 : réinitialisation de votre mot de passe en tant qu’utilisateur](#step-3-reset-your-azure-ad-password-as-a-user)


###  Composants requis
Avant de pouvoir activer et utiliser la réinitialisation du mot de passe libre-service, vous devez effectuer la configuration requise suivante :

- Créez un client AAD. Pour plus d’informations, consultez la page [Prise en main d’Azure AD](https://azure.microsoft.com/trial/get-started-active-directory/).
- Obtenez un abonnement Azure. Pour plus d’informations, consultez la rubrique [Qu’est-ce qu’un client Azure AD ?](active-directory-administer.md#what-is-an-azure-ad-tenant).
- Associez votre client AAD à votre abonnement Azure. Pour plus d’informations, consultez la page [Comment sont associés les abonnements Azure et Azure AD](https://msdn.microsoft.com/library/azure/dn629581.aspx).
- Procédez à une mise à niveau vers Azure AD Premium ou Basic. Pour plus d’informations, consultez la page [Éditions d’Azure Active Directory](http://azure.microsoft.com/pricing/details/active-directory/).

  >[AZURE.NOTE]Pour activer la réinitialisation du mot de passe libre-service, vous devez procéder à une mise à niveau vers Azure AD Premium ou Basic. Pour plus d’informations, consultez la page Éditions d’Azure Active Directory. Ces informations incluent des instructions détaillées sur l'inscription à Azure AD Premium ou Basic, l’activation d’un plan de licence et d’un accès Azure AD, mais aussi l’attribution d’accès aux comptes administrateur et utilisateur.
  
- Créez au moins un compte d’administrateur et un compte d’utilisateur dans votre annuaire AAD.
- Attribuez une licence AAD Premium ou Basic aux comptes d’administrateur et d’utilisateur que vous avez créés.

### Étape 1 : configuration de la stratégie de réinitialisation du mot de passe
Pour configurer la stratégie de réinitialisation du mot de passe utilisateur, procédez comme suit :
 
1.	Ouvrez le navigateur de votre choix et accédez au [portail de gestion Azure](https://manage.windowsazure.com).
2.	Dans la barre de navigation à gauche du [portail de gestion Azure](https://manage.windowsazure.com), recherchez l’**extension Active Directory**.

    ![][001]

3. Sous l’onglet **Annuaire**, cliquez sur l’annuaire dans lequel vous souhaitez configurer la stratégie de réinitialisation du mot de passe utilisateur, par exemple, Wingtip Toys.

    ![][002]

4.	Cliquez sur l’onglet **Configurer**.

    ![][003]

5.	Sous l’onglet **Configurer**, faites défiler vers le bas jusqu'à la section **Stratégie de réinitialisation du mot de passe utilisateur**. Dans cette section, vous pouvez configurer chaque aspect de la stratégie de réinitialisation du mot de passe pour un annuaire donné.

    >[AZURE.NOTE]Cette **stratégie s’applique uniquement aux utilisateurs finaux de votre organisation, pas aux administrateurs**. Pour des raisons de sécurité, Microsoft contrôle la stratégie de réinitialisation du mot de passe pour les administrateurs. Si vous ne voyez pas cette section, assurez-vous que vous êtes inscrit à Azure Active Directory Premium ou Basic et que vous avez **attribué une licence** pour le compte administrateur configurant cette fonctionnalité.

    ![][004]

6.	Pour configurer la stratégie de réinitialisation du mot de passe utilisateur, définissez le paramètre **Utilisateurs autorisés à réinitialiser leur mot de passe** sur **Oui**. Cette action permet d’afficher plusieurs autres contrôles qui vous permettent de configurer le fonctionnement de cette fonctionnalité dans votre annuaire. N’hésitez pas à personnaliser la réinitialisation du mot de passe selon vos besoins. Si vous souhaitez en savoir plus sur le fonctionnement de chaque contrôle de la stratégie de réinitialisation du mot de passe, consultez la page [Personnalisation de la gestion de mot de passe Azure AD](active-directory-passwords-customize).

    ![][005]

7.	Après avoir configuré la stratégie de réinitialisation du mot de passe comme vous le souhaitez pour votre client, cliquez sur le bouton **Enregistrer** situé en bas de l’écran.

  >[AZURE.NOTE]Nous vous recommandons de mettre en place une stratégie de réinitialisation du mot de passe utilisateur à double vérification pour observer le comportement de cette fonctionnalité en situation complexe.

  ![][006]

### Étape 2 : ajout de données de contact pour votre utilisateur de test
Vous avez plusieurs options pour spécifier les données que les utilisateurs de votre organisation doivent utiliser pour la réinitialisation du mot de passe.

-	Modifiez les utilisateurs dans le [portail de gestion Azure](https://manage.windowsazure.com) ou le [portail d’administration d’Office 365](https://portal.microsoftonline.com).
-	Utilisez AAD Connect pour synchroniser les propriétés de l’utilisateur dans Azure AD à partir d’un domaine Active Directory local.
-	Utilisez Windows PowerShell pour modifier les propriétés de l’utilisateur.
-	Autorisez les utilisateurs à inscrire leurs propres données en les guidant vers le portail d’inscription à la page [http://aka.ms/ssprsetup](http://aka.ms/ssprsetup).
-	Obligez les utilisateurs à s’inscrire à la réinitialisation de mot de passe lors de leur connexion au volet d’accès à la page [http://myapps.microsoft.com](http://myapps.microsoft.com) en définissant l’option de configuration **Demander aux utilisateurs de s’inscrire lorsqu’ils se connectent au volet d’accès** sur **Oui**.

Si vous souhaitez en savoir plus sur les données utilisées par la réinitialisation du mot de passe ainsi que sur la configuration requise pour le formatage de ces données, consultez la rubrique [Données utilisées par la réinitialisation du mot de passe](active-directory-passwords-learn-more.md#what-data-is-used-by-password-reset).

#### Pour ajouter des données de contact utilisateur via le portail d’inscription des utilisateurs
1.	Pour utiliser le portail d’inscription à la réinitialisation du mot de passe, vous devez fournir aux utilisateurs de votre organisation un lien vers cette page ([http://aka.ms/ssprsetup](http://aka.ms/ssprsetup)) ou activer l’option pour obliger les utilisateurs à s’inscrire automatiquement. Lorsqu’ils cliquent sur ce lien, ils sont invités à se connecter avec leur compte professionnel. Après cela, la page suivante s’affiche :

    ![][007]

2.	Sur cette page, les utilisateurs peuvent indiquer et vérifier la saisie de leur numéro de téléphone mobile, de leur adresse de messagerie secondaire ou de leurs questions de sécurité. Voici comment se présente l’écran de vérification d’un numéro de téléphone mobile.

    ![][008]

3.	Lorsqu’un utilisateur indique ces informations, la page est mise à jour pour indiquer que les informations sont valides (celles-ci sont masquées ci-dessous). En cliquant sur les boutons **terminer** ou **annuler**, l’utilisateur peut consulter le volet d’accès.

    ![][009]

4.	Une fois qu’un utilisateur a vérifié ces informations, son profil est mis à jour avec les données qu’il a fournies. Dans cet exemple, le numéro **Téléphone professionnel** est indiqué manuellement, l’utilisateur peut alors l’utiliser en tant que méthode de contact pour la réinitialisation de son mot de passe.

    ![][010]

### Étape 3 : réinitialisation de votre mot de passe Azure AD en tant qu’utilisateur
Maintenant que vous avez configuré une stratégie de réinitialisation utilisateur et indiqué les informations de contact de votre utilisateur, celui-ci peut effectuer une réinitialisation de mot de passe libre-service.

#### Pour effectuer une réinitialisation de mot de passe libre-service
1.	Si vous accédez à un site comme [**portal.microsoftonline.com**](http://portal.microsoftonline.com), un écran de connexion semblable à celui ci-dessous s’affiche. Cliquez sur le lien **Votre compte n’est pas accessible ?** pour tester l’interface utilisateur de réinitialisation du mot de passe.

    ![][011]

2.	Après avoir cliqué sur **Votre compte n’est pas accessible ?**, vous êtes dirigé vers une nouvelle page qui vous demande un **ID utilisateur** pour lequel vous souhaitez réinitialiser un mot de passe. Entrez votre **ID utilisateur** de test ici, passez le test captcha, puis cliquez sur **suivant**.

    ![][012]

3.	Comme l’utilisateur a renseigné les champs **téléphone professionnel**, **téléphone mobile** et **Autre adresse de messagerie**, vous pouvez constater qu’il a pu utiliser toutes ces options pour passer la première vérification.

    ![][013]

4.	Dans ce cas, choisissez d’**appeler** le numéro de **téléphone professionnel** en premier. Notez que lorsque vous sélectionnez une méthode basée sur le téléphone, les utilisateurs doivent **vérifier leur numéro de téléphone** avant de pouvoir réinitialiser leur mot de passe. Cela permet d’empêcher les personnes malveillantes de contacter de façon indésirable les numéros de téléphone des utilisateurs de votre organisation.

    ![][014]

5.	Lorsque l’utilisateur a confirmé son numéro de téléphone, le fait de cliquer sur Appeler entraîne l’affichage d’un compteur tandis que son téléphone sonne. Lorsque l’utilisateur décroche son téléphone, il peut entendre un message lui indiquant qu’il **doit appuyer sur la touche dièse (#)** pour vérifier son compte. En appuyant sur cette touche, l’utilisateur valide automatiquement la première vérification, puis le système passe à la deuxième vérification.

    ![][015]

6.	L’interface utilisateur est alors automatiquement mise à jour : le choix de vérification sélectionné est retiré de la liste. Dans ce cas, étant donné que vous avez utilisé le champ **Téléphone professionnel** en premier, les options restantes pour la deuxième vérification sont : **Téléphone mobile** et **Adresse de messagerie de secours**. Cliquez sur l’option **Envoyer un courrier électronique sur mon adresse de messagerie secondaire**. Une fois ce choix effectué, le fait de cliquer sur Courrier électronique déclenche l’envoi d’un courrier électronique sur votre adresse de messagerie secondaire.

    ![][016]

7.	Voici un exemple de courrier électronique affiché pour les utilisateurs (notez la marque du client) :

    ![][017]

8.	Lorsque le courrier électronique arrive, la page est mise à jour, vous permettant de saisir dans la zone d’entrée le code de vérification contenu dans le courrier électronique, comme illustré ci-dessous. Lorsque le code approprié est saisi, le bouton Suivant s’éclaire, permettant de passer la deuxième vérification.

    ![][018]

9.	Une fois la configuration requise par la stratégie organisationnelle atteinte, vous pouvez choisir un nouveau mot de passe. Le mot de passe est validé s’il répond aux exigences de mot de passe « fort » d’AAD (consultez la page [Stratégie de mot de passe dans Azure AD](https://msdn.microsoft.com/library/azure/jj943764.aspx)) : un indicateur de force apparaît pour signaler à l’utilisateur que le mot de passe entré répond à cette stratégie ou non.

    ![][019]

10.	Lorsque vous avez fourni les mots de passe correspondant à la stratégie organisationnelle, votre mot de passe est réinitialisé et vous pouvez vous connecter immédiatement en l’utilisant.

    ![][020]


## Activation de la réinitialisation ou de la modification du mot de passe pour les utilisateurs AD

Cette section présente la configuration de la réinitialisation d’un mot de passe pour l’écriture différée de mot de passe dans une version locale d’Active Directory.

- [Configuration requise pour l’écriture différée de mot de passe](#writeback-prerequisites)
- [Étape 1 : téléchargement de la dernière version d’Azure AD Connect](#step-1-download-the-latest-version-of-azure-ad-connect)
- [Étape 2 : activation et vérification de l’écriture différée des mots de passe dans Azure AD Connect via l’interface utilisateur ou Powershell](#step-2-enable-password-writeback-in-azure-ad-connect)
- [Étape 3 : configuration de votre pare-feu](#step-3-configure-your-firewall)
- [Étape 4 : configuration des autorisations appropriées](#step-4-set-up-the-appropriate-active-directory-permissions)
- [Étape 5 : réinitialisation de votre mot de passe Active Directory en tant qu’utilisateur, puis vérification de son fonctionnement](#step-5-reset-your-ad-password-as-a-user)


### Configuration requise pour l’écriture différée
Avant de pouvoir activer et utiliser l’écriture différée de mot de passe, vous devez respecter la configuration requise suivante :

- Vous avez un client Azure AD avec Azure AD Premium activé. Pour plus d’informations, consultez la page [Éditions d’Azure Active Directory](active-directory-editions.md).
- La réinitialisation du mot de passe a été configurée et activée dans votre client. Pour plus d’informations, consultez la section [Activation de la réinitialisation du mot de passe pour les utilisateurs Azure AD](#enable-users-to-reset-their-azure-ad-passwords)
- Vous avez au moins un compte d’administrateur et un compte d’utilisateur de test avec une licence Azure AD Premium que vous pouvez utiliser pour tester cette fonctionnalité. Pour plus d’informations, consultez la page [Éditions d’Azure Active Directory](active-directory-editions.md).

  >[AZURE.NOTE]Assurez-vous que le compte d’administrateur que vous utilisez pour activer l’écriture différée de mot de passe est un compte d’administrateur cloud (créé dans Azure AD) et non pas un compte fédéré (créé dans un AD local et synchronisé dans Azure AD).
  
- Vous avez un déploiement local d’AD pour une forêt unique ou des forêts multiples exécutant Windows Server 2008, Windows Server 2008 R2, Windows Server 2012 ou Windows Server 2012 R2 dotés des derniers Service Packs.

  >[AZURE.NOTE]Si vous exécutez une version antérieure de Windows Server 2008 ou 2008 R2, vous pouvez toujours utiliser cette fonctionnalité, mais vous devez [télécharger et installer la mise à jour KB 2386717](https://support.microsoft.com/kb/2386717) avant de pouvoir appliquer votre stratégie de mot de passe AD locale dans le cloud.
  
- Vous avez installé l’outil Azure AD Connect et vous avez préparé votre environnement AD pour la synchronisation dans le cloud. Pour plus d’informations, consultez la page [Utilisation de votre infrastructure d’identité locale dans le cloud](active-directory-aadconnect.md).
- Si vous utilisez DirSync, vous devez vérifier que le pare-feu de votre organisation est configuré pour bloquer les connexions sortantes et débloquer **le port TCP 828 ou 818** afin d’activer et d’utiliser l’écriture différée de mot de passe. Si vous utilisez Azure AD Sync ou Azure AD Connect, cette étape est facultative, puisque seul le port sortant **TCP 443** (et dans certains cas les ports **TCP 9350 à 9354**) doit être ouvert.

  >[AZURE.NOTE]Nous recommandons fortement à tous les utilisateurs des outils Azure AD Sync ou DirSync de procéder à une mise à niveau vers la dernière version d’Azure AD Connect pour s’assurer de bénéficier des meilleures conditions d’utilisation possibles et des fonctionnalités les plus récentes dès leur publication.
  

### Étape 1 : téléchargement de la dernière version d’Azure AD Connect
L’écriture différée de mot de passe est intégrée à Azure AD Connect et à l’outil Azure AD Sync à partir de la version **1.0.0419.0911**. L’écriture différée de mot de passe avec déverrouillage de compte automatique est intégrée à Azure AD Connect et à l’outil Azure AD Sync à partir de la version **1.0.0485.0222**. Si vous exécutez une version antérieure, veuillez procéder à la mise à niveau vers cette version ou une version ultérieure avant de continuer. [Cliquez ici pour télécharger la dernière version d’Azure AD Connect](active-directory-aadconnect.md#download-azure-ad-connect).

#### Pour vérifier la version d’Azure AD Sync
1.	Accédez à **%ProgramFiles%\Azure Active Directory Sync**.
2.	Recherchez l’exécutable **ConfigWizard.exe**.
3.	Cliquez avec le bouton droit sur l’exécutable et sélectionnez l’option **Propriétés** dans le menu contextuel.
4.	Cliquez sur l’onglet **Détails**.
5.	Recherchez le champ **Version du fichier**.

    ![][021]

Si ce numéro de version est supérieur ou égal à **1.0.0419.0911**, ou que vous installez Azure AD Connect, vous pouvez passer à l’[étape 2 : activation et vérification de l’écriture différée de mot de passe dans Azure AD Connect via l’interface utilisateur ou Powershell](#step-2-enable-password-writeback-in-azure-ad-connect).

 >[AZURE.NOTE]Si vous installez l’outil Azure AD Connect pour la première fois, vous devez suivre quelques recommandations pour préparer votre environnement à la synchronisation d’annuaires. Avant d'installer l’outil Azure AD Connect, vous devez activer la synchronisation de répertoire dans le [portail d’administration d’Office 365](https://portal.microsoftonline.com) ou le [portail de gestion Azure](https://manage.windowsazure.com). Pour plus d'informations, consultez la rubrique [Gestion d’Azure AD Connect](active-directory-aadconnect-whats-next.md).


### Étape 2 : activation de l'écriture différée de mot de passe dans Azure AD Connect
Lorsque vous avez téléchargé l’outil Azure AD Connect, vous pouvez activer l’écriture différée de mot de passe. Il existe de méthodes pour le faire. Vous pouvez activer l’écriture différée de mot de passe dans l’écran de fonctionnalités facultatives de l’Assistant Installation d’Azure AD Connect ou en utilisant Windows PowerShell.

#### Pour activer l’écriture différée de mot de passe dans l’Assistant Configuration
1.	Sur votre **ordinateur de synchronisation d’annuaires**, ouvrez l’Assistant Configuration d’**Azure AD Connect**.
2.	Cliquez pour passer les étapes jusqu’à atteindre l’écran de configuration **Fonctionnalités facultatives**.
3.	Vérifiez l’option **Écriture différée de mot de passe**.

    ![][022]

4.	Terminez l’Assistant : la dernière page résume les modifications et inclut la modification de configuration pour l’écriture différée de mot de passe.

> [AZURE.NOTE]Pour désactiver l’écriture différée de mot de passe, il vous suffit d’exécuter de nouveau cet Assistant et d’annuler la sélection de cette fonctionnalité, ou de définir le paramètre **Écriture différée de mot de passe sur le répertoire local** sur **Non** dans la section **Stratégie de réinitialisation du mot de passe utilisateur** de l’onglet **Configurer** de votre répertoire dans le [portail de gestion Azure](https://manage.windowsazure.com). Pour plus d'informations sur la personnalisation de votre expérience de réinitialisation de mot de passe, consultez [Personnalisation de la gestion de mot de passe Azure AD](active-directory-passwords-customize.md).

#### Pour activer l’écriture différée de mot de passe en utilisant Windows PowerShell
1.	Sur votre **ordinateur de synchronisation d’annuaires**, ouvrez une nouvelle **fenêtre Windows PowerShell avec élévation de privilèges**.
2.	Si le module n’est pas déjà chargé, entrez la commande `Import-Module ADSync` pour charger les applets de commande Azure AD Connect dans votre session active.
3.	Obtenez la liste des connecteurs AAD dans votre système en exécutant l’applet de commande `Get-ADSyncConnector` et en stockant les résultats dans `$aadConnectorName`
4.	Obtenez l’état actuel de l’écriture différée pour le connecteur actuel en exécutant l’applet de commande suivante : `Get-ADSyncAADPasswordResetConfiguration –Connector $aadConnectorName`
5.	Activez l’écriture différée de mot de passe en exécutant l’applet de commande : `Set-ADSyncAADPasswordResetConfiguration –Connector $aadConnectorName –Enable $true`

> [AZURE.NOTE]Si vous êtes invité à vous identifier, assurez-vous que le compte d’administrateur que vous indiquez pour AzureADCredential est un **compte d’administrateur cloud (créé dans Azure AD)** et non pas un compte fédéré (créé dans un AD local et synchronisé dans Azure AD).
> [AZURE.NOTE]Pour désactiver l’écriture différée de mot de passe via PowerShell, il vous suffit de répéter les instructions précédentes en transmettant `$false` à cette étape ou en définissant le paramètre **Écriture différée de mot de passe sur le répertoire local** sur **Non** dans la section **Stratégie de réinitialisation du mot de passe utilisateur** de l’onglet **Configurer** de votre répertoire dans le [portail de gestion Azure](https://manage.windowsazure.com).

#### Vérification de la réussite de la configuration
Une fois la configuration terminée, un message indiquant la réussite de l’activation de l’écriture différée de mot de passe s’affiche dans la fenêtre Windows PowerShell ou dans l’interface utilisateur de configuration.

Vous pouvez également vérifier que le service a été correctement installé en ouvrant l’Observateur d’événements, en accédant au journal d’événements de l’application et en recherchant l’événement **31005 - OnboardingEventSuccess** à partir de la source **PasswordResetService**.

  ![][023]

### Étape 3 : configuration de votre pare-feu
Une fois que vous avez activé l’écriture différée de mot de passe dans l’outil Azure AD Connect, vous devez vérifier que le service peut se connecter au cloud.

1.	Une fois l’installation terminée, si vous bloquez les connexions sortantes inconnues dans votre environnement, vous devez également ajouter les règles suivantes à votre pare-feu. Assurez-vous de bien redémarrer votre ordinateur AAD Connect après avoir apporté ces modifications :
   - Autorisez les connexions sortantes sur le port TCP 443.
   - Autorisez les connexions sortantes sur https://ssprsbprodncu-sb.accesscontrol.windows.net/. 
   - Lorsque vous utilisez un proxy ou que vous rencontrez des problèmes de connectivités d’ordre général, autorisez les connexions sortantes sur les ports TCP 9350 à 9534.

### Étape 4 : définition des autorisations Active Directory adéquates
Pour chaque forêt contenant des utilisateurs dont les mots de passe doivent être réinitialisés, si X correspond au compte spécifié pour cette forêt dans l’Assistant Configuration (durant l’installation d’origine), X doit avoir les droits étendus **Réinitialiser le mot de passe**, **Modifier le mot de passe** et **Autorisations en écriture** sur `lockoutTime` et **Autorisations en écriture** sur `pwdLastSet` sur l’objet racine de chaque domaine dans cette forêt. Le droit doit être marqué comme hérité par tous les objets utilisateur.

La définition de ces autorisations permet au compte de service de l’agent de gestion de chaque forêt de gérer les mots de passe pour les comptes d’utilisateur de cette forêt. Sans ces autorisations, même si l’écriture différée semblera configurée correctement, les utilisateurs rencontreront des erreurs en essayant de gérer leurs mots de passe locaux à partir du cloud. Voici des instructions détaillées expliquant comment procéder en utilisant le composant logiciel enfichable de gestion **Utilisateurs et ordinateurs Active Directory** :

>[AZURE.NOTE]La réplication de ces autorisations pour tous les objets peut durer jusqu’à une heure.

#### Pour configurer les autorisations appropriées pour l’écriture différée

1.	Ouvrez **Utilisateurs et ordinateurs Active Directory ** avec un compte ayant les autorisations d’administration de domaine appropriées.
2.	Dans **Menu Affichage**, assurez-vous que l’option **Fonctionnalités avancées** est activée.
3.	Dans le volet gauche, cliquez avec le bouton droit sur l’objet représentant la racine du domaine.
4.	Cliquez sur l’onglet **Sécurité**.
5.	Puis cliquez sur **Avancé**.

    ![][024]

6.	Sous l’onglet **Autorisations**, cliquez sur **Ajouter**.

    ![][025]

7.	Sélectionnez le compte auquel vous souhaitez octroyer des autorisations (il s’agit du même compte que celui qui a été spécifié lors de la configuration de la synchronisation de cette forêt).
8.	Dans le menu déroulant situé en haut de la fenêtre, sélectionnez **Objets utilisateur descendants**.
9.	Dans la boîte de dialogue **Entrée d’autorisation** qui s’affiche, cochez les cases **Réinitialiser le mot de passe**, **Modifier le mot de passe** et **Autorisations en écriture** sur `lockoutTime`, et **Autorisations en écriture** sur `pwdLastSet`.

    ![][026] ![][027] ![][028]

10.	Puis, dans chaque boîte de dialogue qui s’affiche, cliquez sur **Appliquer/Ok**.

### Étape 5 : réinitialisation de votre mot de passe AD en tant qu’utilisateur
Maintenant que l’écriture différée de mot de passe a été activée, vous pouvez tester son fonctionnement en réinitialisant le mot de passe d’un utilisateur dont le compte a été synchronisé dans votre client cloud.
 
#### Pour vérifier le bon fonctionnement de l’écriture différée de mot de passe
1.	Accédez à [https://passwordreset.microsoftonline.com](https://passwordreset.microsoftonline.com) ou dans n’importe quel écran de connexion d’ID d’organisation, cliquez sur le lien **Votre compte n’est pas accessible ?**.

    ![][029]

2.	Une page s’affiche, vous demandant un ID utilisateur pour lequel réinitialiser le mot de passe. Entrez votre ID utilisateur de test et suivez les étapes du processus de réinitialisation de mot de passe.
3.	Après avoir réinitialisé votre mot de passe, vous verrez un écran qui ressemble à ceci. Cela signifie que vous avez correctement réinitialisé votre mot de passe vos annuaires locaux/dans le cloud.

    ![][030]

4.	Pour vérifier la réussite de l’opération ou diagnostiquer des erreurs, accédez à votre **ordinateur de synchronisation d’annuaires**, ouvrez l’**Observateur d’événements**, accédez au **journal des événements de l’application** et recherchez l’événement **31002 - PasswordResetSuccess** à partir de la source **PasswordResetService** pour votre utilisateur de test.

    ![][031]


<br/> <br/> <br/>

**Ressources supplémentaires**


* [Définition de la gestion des mots de passe](active-directory-passwords.md)
* [Fonctionnement de la gestion des mots de passe](active-directory-passwords-how-it-works.md)
* [Personnalisation de la gestion des mots de passe](active-directory-passwords-customize.md)
* [Meilleures pratiques de gestion des mots de passe](active-directory-passwords-best-practices.md)
* [Obtention d’informations grâce aux rapports sur la gestion des mots de passe](active-directory-passwords-get-insights.md)
* [FAQ sur la gestion des mots de passe](active-directory-passwords-faq.md)
* [Résolution des problèmes de gestion des mots de passe](active-directory-passwords-troubleshoot.md)
* [En savoir plus](active-directory-passwords-learn-more.md)
* [Gestion des mots de passe sur MSDN](https://msdn.microsoft.com/library/azure/dn510386.aspx)



[001]: ./media/active-directory-passwords-getting-started/001.jpg "Image_001.jpg"
[002]: ./media/active-directory-passwords-getting-started/002.jpg "Image_002.jpg"
[003]: ./media/active-directory-passwords-getting-started/003.jpg "Image_003.jpg"
[004]: ./media/active-directory-passwords-getting-started/004.jpg "Image_004.jpg"
[005]: ./media/active-directory-passwords-getting-started/005.jpg "Image_005.jpg"
[006]: ./media/active-directory-passwords-getting-started/006.jpg "Image_006.jpg"
[007]: ./media/active-directory-passwords-getting-started/007.jpg "Image_007.jpg"
[008]: ./media/active-directory-passwords-getting-started/008.jpg "Image_008.jpg"
[009]: ./media/active-directory-passwords-getting-started/009.jpg "Image_009.jpg"
[010]: ./media/active-directory-passwords-getting-started/010.jpg "Image_010.jpg"
[011]: ./media/active-directory-passwords-getting-started/011.jpg "Image_011.jpg"
[012]: ./media/active-directory-passwords-getting-started/012.jpg "Image_012.jpg"
[013]: ./media/active-directory-passwords-getting-started/013.jpg "Image_013.jpg"
[014]: ./media/active-directory-passwords-getting-started/014.jpg "Image_014.jpg"
[015]: ./media/active-directory-passwords-getting-started/015.jpg "Image_015.jpg"
[016]: ./media/active-directory-passwords-getting-started/016.jpg "Image_016.jpg"
[017]: ./media/active-directory-passwords-getting-started/017.jpg "Image_017.jpg"
[018]: ./media/active-directory-passwords-getting-started/018.jpg "Image_018.jpg"
[019]: ./media/active-directory-passwords-getting-started/019.jpg "Image_019.jpg"
[020]: ./media/active-directory-passwords-getting-started/020.jpg "Image_020.jpg"
[021]: ./media/active-directory-passwords-getting-started/021.jpg "Image_021.jpg"
[022]: ./media/active-directory-passwords-getting-started/022.jpg "Image_022.jpg"
[023]: ./media/active-directory-passwords-getting-started/023.jpg "Image_023.jpg"
[024]: ./media/active-directory-passwords-getting-started/024.jpg "Image_024.jpg"
[025]: ./media/active-directory-passwords-getting-started/025.jpg "Image_025.jpg"
[026]: ./media/active-directory-passwords-getting-started/026.jpg "Image_026.jpg"
[027]: ./media/active-directory-passwords-getting-started/027.jpg "Image_027.jpg"
[028]: ./media/active-directory-passwords-getting-started/028.jpg "Image_028.jpg"
[029]: ./media/active-directory-passwords-getting-started/029.jpg "Image_029.jpg"
[030]: ./media/active-directory-passwords-getting-started/030.jpg "Image_030.jpg"
[031]: ./media/active-directory-passwords-getting-started/031.jpg "Image_031.jpg"

 

<!---HONumber=July15_HO5-->