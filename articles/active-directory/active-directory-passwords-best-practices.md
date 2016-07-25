<properties
	pageTitle="Meilleures pratiques Gestion des mots de passe Azure AD | Microsoft Azure"
	description="Meilleures pratiques concernant le déploiement et l’utilisation, exemple de documentation pour les utilisateurs finaux et guides de formation pour la gestion des mots de passe dans Azure Active Directory."
	services="active-directory"
	documentationCenter=""
	authors="asteen"
	manager="femila"
	editor="curtand"/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/12/2016"
	ms.author="asteen"/>

# Déploiement de la gestion des mots de passe et formation des utilisateurs

> [AZURE.IMPORTANT] **Rencontrez-vous des problèmes de connexion ?** Dans ce cas, [voici comment vous pouvez modifier et réinitialiser votre mot de passe](active-directory-passwords-update-your-own-password.md).

L’étape qui suit l’activation de la réinitialisation de mot de passe consiste à autoriser les utilisateurs à utiliser le service dans votre organisation. Pour ce faire, vous devez vous assurer que vos utilisateurs sont configurés pour utiliser le service correctement et également qu’ils disposent de la formation nécessaire pour réussir à gérer leurs mots de passe. Cet article vous explique les concepts suivants :

* [**Configuration des utilisateurs pour la gestion des mots de passe**](#how-to-get-users-configured-for-password-reset)
  * [Configuration d’un compte pour la réinitialisation de mot de passe](#what-makes-an-account-configured)
  * [Méthodes permettant de remplir les données d’authentification](#ways-to-populate-authentication-data)
* [**Meilleures pratiques de déploiement de la réinitialisation de mot de passe dans votre organisation**](#what-is-the-best-way-to-roll-out-password-reset-for-users)
  * [Déploiement par courrier électronique et exemples de communications concernant le déploiement par courrier électronique](#email-based-rollout)
  * [Création de votre propre portail de gestion de mot de passe personnalisé pour vos utilisateurs](#creating-your-own-password-portal)
  * [Utilisation de l’inscription imposée pour forcer les utilisateurs à s’inscrire au moment de la connexion](#using-enforced-registration)
  * [Téléchargement des données d’authentification des comptes d’utilisateur](#uploading-data-yourself)
* [**Exemples de supports de formation (bientôt disponibles)**](#sample-training-materials)

## Configuration des utilisateurs pour la réinitialisation de mot de passe
Cette section décrit diverses méthodes qui vous permettent de vous assurer que chaque utilisateur de votre organisation peut utiliser la réinitialisation de mot de passe libre-service efficacement en cas d’oubli de son mot de passe.

### Configuration d’un compte
Avant qu’un utilisateur puisse utiliser la réinitialisation de mot de passe, **toutes les** conditions suivantes doivent être remplies :

1.	La réinitialisation de mot de passe doit être activée dans l’annuaire. Découvrez comment activer la réinitialisation de mot de passe en lisant les sections [Autorisation de la réinitialisation de mot de passe Azure AD par les utilisateurs](active-directory-passwords-getting-started.md#enable-users-to-reset-their-azure-ad-passwords) ou [Autorisation de la réinitialisation ou modification de mot de passe AD par les utilisateurs](active-directory-passwords-getting-started.md#enable-users-to-reset-or-change-their-ad-passwords).
2.	L’utilisateur doit disposer d’une licence.
 - Les utilisateurs du cloud doivent avoir **une licence Office 365 payante**, **AAD Basic** ou **AAD Premium**.
 - Les utilisateurs locaux (fédérés ou avec synchronisation du hachage) **doivent disposer d’une licence AAD Premium**.
3.	L’utilisateur doit avoir l’**ensemble minimal de données d’authentification** conforme à la stratégie de réinitialisation de mot de passe actuelle.
 - Les données d’authentification sont considérées comme définies si le champ correspondant dans l’annuaire contient des données bien formées.
 - Un ensemble minimal de données d’authentification est défini comme correspondant à **au moins une** des options d’authentification activées si une stratégie comportant une seule porte est configurée, ou à **au moins deux** des options d’authentification activées si une stratégie incluant deux portes est configurée.
4.	Si l’utilisateur utilise un compte local, l’[écriture différée de mot de passe](active-directory-passwords-getting-started.md#enable-users-to-reset-or-change-their-ad-passwords) doit être activée.

### Méthodes permettant de remplir les données d’authentification
Vous avez plusieurs options pour spécifier les données que les utilisateurs de votre organisation doivent utiliser pour la réinitialisation du mot de passe.

- Modifiez les utilisateurs dans le [portail de gestion Azure](https://manage.windowsazure.com) ou le [portail d’administration d’Office 365](https://portal.microsoftonline.com).
- Utilisez Azure AD Sync pour synchroniser les propriétés de l’utilisateur dans Azure AD à partir d’un domaine Active Directory local.
- Utilisez Windows PowerShell pour modifier les propriétés de l’utilisateur en [procédant comme indiqué ici](active-directory-passwords-learn-more.md#how-to-access-password-reset-data-for-your-users).
- Autorisez les utilisateurs à inscrire leurs propres données en les guidant vers le portail d’inscription à la page [http://aka.ms/ssprsetup](http://aka.ms/ssprsetup).
- Obligez les utilisateurs à s’inscrire à la réinitialisation de mot de passe lors de leur connexion au compte Azure AD en définissant l’option de configuration [**Demander aux utilisateurs de s’inscrire lorsqu’ils se connectent ?**](active-directory-passwords-customize.md#require-users-to-register-when-signing-in) sur **Oui**.

Les utilisateurs n’ont pas besoin de s’inscrire à la réinitialisation de mot de passe pour que le système fonctionne. Par exemple, si vous avez des numéros de téléphone fixes ou portables existants dans votre annuaire local, vous pouvez les synchroniser dans Azure AD et nous les utiliserons pour réinitialiser automatiquement les mots de passe.

Vous pouvez également en savoir plus sur [comment les données sont utilisées par la réinitialisation de mot de passe](active-directory-passwords-learn-more.md#what-data-is-used-by-password-reset) et [comment vous pouvez remplir les différents champs d’authentification avec PowerShell](active-directory-passwords-learn-more.md#how-to-access-password-reset-data-for-your-users).

## Quelle est la meilleure façon de déployer la réinitialisation de mot de passe pour les utilisateurs ?
Voici les étapes de déploiement générales pour la réinitialisation de mot de passe :

1.	Activez la réinitialisation de mot de passe dans votre annuaire en accédant à l’onglet **Configurer** du [portail de gestion Azure](https://manage.windowsazure.com) et en sélectionnant **Oui** pour l’option **Utilisateurs autorisés à réinitialiser leur mot de passe**.
2.	Attribuez les licences appropriées à chaque utilisateur auquel vous souhaitez proposer la réinitialisation de mot de passe en accédant à l’onglet **Licences** dans le [portail de gestion Azure](https://manage.windowsazure.com).
3.	Vous pouvez éventuellement restreindre la réinitialisation de mot de passe à un groupe d’utilisateurs pour déployer la fonctionnalité lentement au fil du temps en définissant le commutateur **Limiter l’accès à la réinitialisation des mots de passe** sur **Oui** et en sélectionnant un groupe de sécurité pour activer la réinitialisation de mot de passe. Notez que ces utilisateurs doivent tous avoir des licences.
4.	Demandez à vos utilisateurs d’utiliser la réinitialisation de mot de passe soit en leur envoyant un courrier électronique leur indiquant de s’inscrire, soit en activant l’inscription forcée dans le panneau d’accès, soit en chargeant les données d’authentification appropriées pour ces utilisateurs via DirSync, PowerShell ou le [portail de gestion Azure](https://manage.windowsazure.com). Vous trouverez plus d’informations ci-dessous.
5.	Au fil du temps, passez en revue les utilisateurs qui s’inscrivent en accédant à l’onglet Rapports et en affichant le rapport [**Activité d’inscription de réinitialisation de mot de passe**](active-directory-passwords-get-insights.md#view-password-reset-registration-activity).
6.	Une fois qu’un bon nombre d’utilisateurs est inscrit, passez en revue leur utilisation de la réinitialisation de mot de passe en accédant à l’onglet Rapports et en affichant le rapport [**Activité de réinitialisation de mot de passe**](active-directory-passwords-get-insights.md#view-password-reset-activity).

Il existe plusieurs façons d’informer vos utilisateurs qu’ils peuvent s’inscrire à la réinitialisation de mot de passe et l’utiliser dans votre organisation. Elles sont détaillées ci-dessous.

### Déploiement par courrier électronique
L’approche la plus simple pour informer vos utilisateurs qu’ils peuvent s’inscrire à la réinitialisation de mot de passe ou l’utiliser est de leur envoyer un courrier électronique à ce sujet. Voici un modèle que vous pouvez utiliser. N’hésitez pas à remplacer les couleurs/logos par ceux de votre choix afin de le personnaliser selon vos besoins.

  ![][001]

Vous pouvez [télécharger le modèle de courrier électronique ici](http://1drv.ms/1xWFtQM).

### Création de votre propre portail de mot de passe
Une stratégie adaptée aux clients de taille importante qui déploient des fonctionnalités de gestion de mot de passe consiste à créer un « portail de mot de passe » grâce auquel les utilisateurs peuvent gérer, en un emplacement unique, tous les éléments liés à leurs mots de passe.

Nombre de nos clients les plus importants optent pour la création d’une entrée DNS racine comme https://passwords.contoso.com, avec des liens vers le portail de réinitialisation de mot de passe Azure AD, le portail d’inscription pour la réinitialisation du mot de passe et les pages de modification du mot de passe. Ainsi, dans toutes vos communications par messagerie électronique ou prospectus, vous pouvez inclure une URL unique facile à retenir que les utilisateurs peuvent suivre quand ils disposent d’une seconde pour prendre le service en main.

Pour vous aider, nous avons créé une page simple qui utilise les derniers paradigmes de conception d’une interface utilisateur réactive et qui fonctionne sur tous les navigateurs et appareils mobiles.

  ![][007]

Vous pouvez [télécharger le modèle de site web ici](https://github.com/kenhoff/password-reset-page). Nous vous recommandons de personnaliser le logo et les couleurs en fonction des besoins de votre organisation.

### Utilisation de l’inscription forcée
Si vous souhaitez que vos utilisateurs s’inscrivent eux-mêmes à la réinitialisation de mot de passe, vous pouvez également leur imposer de s’inscrire lorsqu’ils se connectent au volet d’accès à la page [http://myapps.microsoft.com](http://myapps.microsoft.com). Pour cela, accédez à l’onglet **Configurer** de votre annuaire et activez l’option **Demander aux utilisateurs de s’inscrire lorsqu’ils se connectent au volet d’accès**.

Vous pouvez également choisir de leur demander de se réinscrire après une période configurable en définissant l’option **Nombre de jours avant que les utilisateurs doivent confirmer leurs données de contact** sur une valeur non nulle. Pour plus d’informations, consultez la page [Personnalisation du comportement de la gestion des mots de passe utilisateur](active-directory-passwords-customize.md#password-management-behavior).

  ![][002]

Après avoir activé cette option, lorsque les utilisateurs se connectent au volet d’accès, une fenêtre contextuelle les informe que leur administrateur leur a demandé de vérifier leurs informations de contact. Ils peuvent les utiliser pour réinitialiser leur mot de passe si jamais ils perdent l’accès à leur compte.

  ![][003]

Lorsqu’ils cliquent sur **Vérifier maintenant**, les utilisateurs sont dirigés vers le **portail d’inscription à la réinitialisation de mot de passe** à la page [http://aka.ms/ssprsetup](http://aka.ms/ssprsetup), dans laquelle ils sont invités à s’inscrire. L’inscription à l’aide de cette méthode peut être désactivée en cliquant sur le bouton **Annuler** ou en fermant la fenêtre, mais les utilisateurs reçoivent un rappel à chaque fois qu’ils se connectent s’ils ne se sont pas inscrits.

  ![][004]

### Téléchargement de données
Si vous souhaitez télécharger vous-même les données d’authentification, les utilisateurs ne sont pas obligés de s’inscrire à la réinitialisation de mot de passe pour pouvoir réinitialiser leur mot de passe. Tant que leurs données d’authentification définies sur le compte sont conformes à la stratégie de réinitialisation de mot de passe que vous avez définie, les utilisateurs sont en mesure de réinitialiser leurs mots de passe.

Pour savoir quelles propriétés peuvent être définies via AAD Connect ou Windows PowerShell, consultez la section [Données utilisées par la réinitialisation de mot de passe](active-directory-passwords-learn-more.md#what-data-is-used-by-password-reset).

Vous pouvez télécharger les données d’authentification via le [portail de gestion Azure](https://manage.windowsazure.com) en suivant les étapes ci-dessous :

1.	Accédez à votre annuaire dans l’**extension Active Directory** du [portail de gestion Azure](https://manage.windowsazure.com).
2.	Cliquez sur l’onglet **Utilisateurs**.
3.	Sélectionnez l’utilisateur qui vous intéresse dans la liste.
4.	Le premier onglet affiche une **Adresse de messagerie de secours**, qui peut être utilisée en tant que propriété pour activer la réinitialisation de mot de passe.

    ![][005]

5.	Cliquez sur l’onglet **Informations professionnelles**.
6.	Vous trouverez sur cette page les options **Téléphone professionnel**, **Téléphone mobile**, **Téléphone d’authentification** et **Adresse électronique d’authentification**. Ces propriétés peuvent également être définies pour permettre à un utilisateur de réinitialiser son mot de passe.

    ![][006]

Pour voir comment chacune de ces propriétés peut être utilisée, consultez la section [Données utilisées par la réinitialisation de mot de passe](active-directory-passwords-learn-more.md#what-data-is-used-by-password-reset).

Consultez la page [Comment accéder aux données de réinitialisation des mots de passe pour vos utilisateurs depuis PowerSell](active-directory-passwords-learn-more.md#how-to-access-password-reset-data-for-your-users) pour apprendre à lire et définir ces données avec PowerShell.

## Exemples de supports de formation
Nous travaillons actuellement sur des exemples de supports de formation que vous pourrez utiliser pour que votre service informatique et vos utilisateurs soient opérationnels rapidement concernant le déploiement et l’utilisation de la réinitialisation de mot de passe. Restez connecté !


<br/> <br/> <br/>

## Liens vers la documentation de réinitialisation du mot de passe
Voici les liens vers toutes les pages de la documentation sur la réinitialisation de mot de passe Azure AD :

* **Rencontrez-vous des problèmes de connexion ?** Dans ce cas, [voici comment vous pouvez modifier et réinitialiser votre mot de passe](active-directory-passwords-update-your-own-password.md).
* [**Fonctionnement**](active-directory-passwords-how-it-works.md) : découvrez les six différents composants du service et la fonction de chacun d’eux.
* [**Prise en main**](active-directory-passwords-getting-started.md) : découvrez comment permettre à vos utilisateurs de réinitialiser et de modifier leurs mots de passe dans le cloud et localement.
* [**Personnalisation**](active-directory-passwords-customize.md) : découvrez comment personnaliser l’apparence et le comportement du service en fonction des besoins de votre organisation.
* [**Obtention d’informations**](active-directory-passwords-get-insights.md) : découvrez nos fonctionnalités intégrées de création de rapports.
* [**FAQ**](active-directory-passwords-faq.md) : obtenez des réponses aux questions fréquemment posées.
* [**Dépannage**](active-directory-passwords-troubleshoot.md) : découvrez comment résoudre rapidement les problèmes liés au service.
* [**En savoir plus**](active-directory-passwords-learn-more.md) : découvrez les détails techniques sur le fonctionnement du service.



[001]: ./media/active-directory-passwords-best-practices/001.jpg "Image_001.jpg"
[002]: ./media/active-directory-passwords-best-practices/002.jpg "Image_002.jpg"
[003]: ./media/active-directory-passwords-best-practices/003.jpg "Image_003.jpg"
[004]: ./media/active-directory-passwords-best-practices/004.jpg "Image_004.jpg"
[005]: ./media/active-directory-passwords-best-practices/005.jpg "Image_005.jpg"
[006]: ./media/active-directory-passwords-best-practices/006.jpg "Image_006.jpg"
[007]: ./media/active-directory-passwords-best-practices/007.jpg "Image_007.jpg"

<!---HONumber=AcomDC_0713_2016-->