<properties
   pageTitle="Azure AD Connect : comptes et autorisations | Microsoft Azure"
   description="Cette rubrique décrit les comptes utilisés et créés, ainsi que les autorisations nécessaires."
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="stevenpo"
   editor=""/>

<tags
   ms.service="active-directory"  
   ms.workload="identity"
   ms.tgt_pltfrm="na"
   ms.devlang="na"
   ms.topic="article"
   ms.date="03/04/2016"
   ms.author="andkjell;billmath"/>


# Autorisations et comptes Azure AD Connect

L’Assistant d’installation d’Azure AD Connect offre deux chemins d’accès différents :

- Dans Configuration rapide, nous avons besoin de davantage de privilèges pour configurer votre configuration facilement, sans que vous deviez créer des utilisateurs ou configurer des autorisations séparément.

- Dans Configuration personnalisée, nous vous proposons davantage d’options, mais il existe certaines situations dans lesquelles vous devrez disposer vous-même des autorisations appropriées.

## Documentation connexe
Si vous n’avez pas lu la documentation sur l’[Intégration de vos identités locales à Azure Active Directory](active-directory-aadconnect.md), le tableau suivant fournit des liens vers des rubriques connexes.

| Rubrique | |
| --------- | --------- |
| Installation à l’aide de la configuration rapide | [Installation rapide pour Azure AD Connect](active-directory-aadconnect-get-started-express.md) |
| Installation à l’aide des paramètres personnalisés | [Installation personnalisée d’Azure AD Connect](active-directory-aadconnect-get-started-custom.md) |
| Effectuer une mise à niveau à partir de DirSync | [Effectuer une mise à niveau à partir de l’outil de synchronisation Azure AD (DirSync)](active-directory-aadconnect-dirsync-upgrade-get-started.md) |


## Installation à l’aide de la configuration rapide
Dans la configuration rapide, l’Assistant d’installation demande des informations d’identification d’administrateur d’entreprise AD DS pour que votre annuaire Active Directory local puisse être configuré avec les autorisations nécessaires pour Azure AD Connect. Si vous effectuez une mise à niveau à partir de DirSync, les informations d’identification d’administrateur d’entreprise AD DS sont utilisées pour réinitialiser le mot de passe pour le compte utilisé par DirSync. Vous aurez également besoin de vos informations d’identification d’administrateur général Azure AD.

Page de l’Assistant | Informations d’identification collectées | Autorisations requises| Utilisation
------------- | ------------- |------------- |------------- |
N/A|Utilisateur exécutant l’Assistant d’installation| Administrateur du serveur local| <li>Crée le compte local à utiliser comme [compte de service de moteur de synchronisation](#azure-ad-connect-sync-service-account).
Se connecter à Azure AD| Informations d’identification Azure Active Directory | Rôle Administrateur général dans Azure AD | <li>Activation de la synchronisation dans l’annuaire Azure AD.</li> <li>Création du [compte Azure AD](#azure-ad-service-account) à utiliser pour les opérations de synchronisation continue dans Azure AD.</li>
Connexion à AD DS | Informations d’identification Active Directory locales | Membre du groupe Administrateurs de l’entreprise dans Active Directory| <li>Crée un [compte](#active-directory-account) dans Active Directory et lui octroie des autorisations. Ce compte permet de lire et d’écrire les informations d’annuaire pendant la synchronisation.</li>

### Informations d’identification d’administrateur d’entreprise
Ces informations d’identification ne sont utilisées que pendant l’installation. Il s’agit d’informations d’identification d’administrateur d’entreprise, et non d’administrateur de domaine, pour que les autorisations dans Active Directory puissent être définies dans tous les domaines.

### Informations d’identification d’administrateur général
Ces informations d’identification ne sont utilisées que pendant l’installation. Elles permettent de créer le [compte Azure AD](#azure-ad-service-account) utilisé pour synchroniser les modifications apportées à Azure AD. En outre, le compte active la synchronisation en tant que fonctionnalité dans Azure AD.

### Autorisations pour le compte AD DS créé pour la configuration rapide
Le [compte](#active-directory-account) destiné à la lecture et à l’écriture sur les services AD DS disposera des autorisations suivantes s’il a été créé via la configuration rapide :

| Autorisation | Utilisé pour |
| ---- | ---- |
| <li>Répliquer les changements d’annuaire</li> <li>Répliquer les changements d’annuaire Tout | Synchronisation de mot de passe |
| Toutes les propriétés en lecture/écriture Utilisateur | Importation et Exchange hybride |
| Toutes les propriétés en lecture/écriture iNetOrgPerson | Importation et Exchange hybride |
| Toutes les propriétés en lecture/écriture Groupe | Importation et Exchange hybride |
| Toutes les propriétés en lecture/écriture Contact | Importation et Exchange hybride |
| Réinitialiser le mot de passe | Préparation pour l’activation de l’écriture différée du mot de passe |

## Installation à l’aide des paramètres personnalisés
Quand vous recourez à des paramètres personnalisés, le compte utilisé pour la connexion à Active Directory doit être créé avant l’installation. Les autorisations que vous devez accorder à ce compte se trouvent sous [Créer le compte AD DS](#create-the-ad-ds-account).

Page de l’Assistant | Informations d’identification collectées | Autorisations requises| Utilisation
------------- | ------------- |------------- |-------------
N/A|Utilisateur exécutant l’Assistant d’installation|<li>Administrateur du serveur local</li><li>Si vous utilisez un serveur SQL Server complet, l’utilisateur doit être administrateur système dans SQL</li>.| Par défaut, crée le compte local à utiliser comme [compte de service de moteur de synchronisation](#azure-ad-connect-sync-service-account). Le compte n’est créé que si l’administrateur ne spécifie pas de compte particulier.
Installation des services de synchronisation, option Compte de service | Informations d’identification du compte d’utilisateur local ou AD | Utilisateur (les autorisations sont accordées par l’Assistant d’installation)|Si l’administrateur spécifie un compte, ce dernier est utilisé comme compte de service pour le service de synchronisation.
Se connecter à Azure AD|Informations d’identification Azure Active Directory| Rôle Administrateur général dans Azure AD| <li>Activation de la synchronisation dans l’annuaire Azure AD.</li> <li>Création du [compte Azure AD](#azure-ad-service-account) à utiliser pour les opérations de synchronisation continue dans Azure AD.</li>
Connexion de vos annuaires|Informations d’identification Active Directory locales pour chaque forêt connectée à Azure AD | Les autorisations varient selon les fonctionnalités que vous activez et se trouvent dans [Créer le compte AD DS](#create-the-ad-ds-account). |Ce compte permet de lire et d’écrire les informations d’annuaire pendant la synchronisation.
Serveurs AD FS|Pour chaque serveur de la liste, l’Assistant recueille des informations d’identification si celles de l’utilisateur exécutant l’Assistant sont insuffisantes pour se connecter.|Administrateur de domaine|Installation et configuration du rôle de serveur AD FS.
Serveurs proxy d’application web |Pour chaque serveur de la liste, l’Assistant recueille des informations d’identification si celles de l’utilisateur exécutant l’Assistant sont insuffisantes pour se connecter.|Administrateur local sur l’ordinateur cible|Installation et configuration du rôle de serveur WAP
Informations d’identification de confiance du proxy |Informations d’identification de confiance du service de fédération (informations d’identification que le proxy utilise pour obtenir un certificat d’approbation à partir de FS) |Compte de domaine qui est un administrateur local du serveur AD FS|Inscription initiale du certificat d’approbation FS-WAP
Page Compte de service AD FS, option Utilisation d’un compte d’utilisateur de domaine|Informations d’identification du compte d’utilisateur AD|Utilisateur de domaine|Le compte d’utilisateur AD dont les informations d’identification sont fournies sera utilisé comme compte de connexion au service AD FS.

### Créer le compte AD DS
Quand vous installez Azure AD Connect, le compte que vous spécifiez dans la page **Connexion de vos annuaires** doit être présent dans Active Directory et disposer des autorisations nécessaires. L’Assistant d’installation ne vérifie pas les autorisations et les problèmes éventuels ne sont détectés que pendant la synchronisation.

Les autorisations dont vous avez besoin dépendent des fonctionnalités facultatives que vous activez. Si vous avez plusieurs domaines, les autorisations doivent être accordées pour tous les domaines de la forêt. Si vous n’activez pas ces fonctionnalités, les autorisations **Utilisateur de domaine** par défaut sont suffisantes.

| Fonctionnalité | Autorisations |
| ------ | ------ |
| Synchronisation de mot de passe | <li>Répliquer les changements d’annuaire</li> <li>Répliquer les changements d’annuaire Tout |
| Déploiement Exchange hybride | Autorisations d’écriture sur les attributs documentés dans [Écriture différée d’Exchange hybride](active-directory-aadconnectsync-attributes-synchronized.md#exchange-hybrid-writeback) pour les utilisateurs, les groupes et les contacts. |
| Écriture différée du mot de passe | Autorisations d’écriture sur les attributs documentés dans [Prise en main de la gestion de mot de passe](active-directory-passwords-getting-started.md#step-4-set-up-the-appropriate-active-directory-permissions) pour les utilisateurs. |
| Écriture différée des appareils | Autorisations accordées avec un script PowerShell comme décrit dans [Écriture différée des appareils](active-directory-aadconnect-feature-device-writeback.md).|
| Écriture différée de groupe | Lire, créer, mettre à jour et supprimer des objets de groupe dans l’unité d’organisation où les groupes de distributions doivent se trouver.|

## Mise à niveau
Lors de la mise à niveau vers une nouvelle version d'Azure AD Connect, vous devez disposer des autorisations suivantes :

| Principal | Autorisations requises | Utilisé pour |
| ---- | ---- | ---- |
| Utilisateur exécutant l’Assistant d’installation | Administrateur du serveur local | Mettre à jour des fichiers binaires. |
| Utilisateur exécutant l’Assistant d’installation | Membre d'ADSyncAdmins | Modifier les règles de synchronisation et d'autre configurations. |
| Utilisateur exécutant l’Assistant d’installation | Si vous utilisez un serveur SQL complet : propriétaire (DBO, ou rôle similaire) de la base de données du moteur de synchronisation | Apporter des modifications au niveau de la base de données, telles que la mise à jour des tables avec de nouvelles colonnes. |

## Plus d’informations sur les comptes créés

### Compte Active Directory

Si vous utilisez la configuration rapide, un compte à utiliser pour la synchronisation est créé dans Active Directory. Le compte créé se trouve dans le domaine racine de forêt au sein du conteneur Utilisateurs et se voit attribuer le préfixe **MSOL\_**. Le compte est créé avec un mot de passe long et complexe qui n’expire pas. Si vous avez une stratégie de mot de passe dans votre domaine, assurez-vous que les mots de passe longs et complexes sont autorisés pour ce compte.

![Compte AD](./media/active-directory-aadconnect-accounts-permissions/adsyncserviceaccount.png)

### Comptes de service de synchronisation d'Azure AD Connect
Un compte de service local est créé par l’Assistant d’installation (sauf si vous spécifiez le compte à utiliser dans les paramètres personnalisés). Le compte a pour préfixe **AAD\_** et est associé au service de synchronisation à utiliser. Si vous installez Azure AD Connect sur un contrôleur de domaine, le compte est créé dans le domaine. Si vous utilisez un serveur distant exécutant SQL Server ou bien un proxy qui requiert une authentification, le compte de service **AAD\_** doit se trouver dans le domaine.

![Compte de service de synchronisation](./media/active-directory-aadconnect-accounts-permissions/syncserviceaccount.png)

Le compte est créé avec un mot de passe long et complexe qui n’expire pas.

Ce compte étant utilisé par Windows pour stocker les clés de chiffrement, le mot de passe de ce compte ne doit pas être réinitialisé ou modifié.

Si vous utilisez un serveur SQL Server complet, le compte de service est le propriétaire de la base de données créée pour le moteur de synchronisation. Le service ne fonctionne pas comme prévu avec d’autres autorisations. Une connexion SQL est également créée.

En outre, le compte se voit octroyer des autorisations sur les fichiers, les clés de Registre et autres objets liés au moteur de synchronisation.

### Compte de service Azure AD
Un compte dans Azure AD est créé en vue de son utilisation par le service de synchronisation. Ce compte peut être identifié par son nom d’affichage.

![Compte AD](./media/active-directory-aadconnect-accounts-permissions/aadsyncserviceaccount.png)

Le nom du serveur sur lequel le compte est utilisé peut être identifié dans la deuxième partie du nom d’utilisateur. Dans l’image ci-dessus, le nom du serveur est FABRIKAMCON. Si vous disposez de serveurs intermédiaires, chaque serveur a son propre compte. Il existe une limite de 10 comptes de service de synchronisation dans Azure AD.

Le compte de service est créé avec un mot de passe long et complexe qui n’expire pas. Il se voit octroyer le rôle de **Compte de synchronisation de répertoires**. À ce titre, il est uniquement autorisé à effectuer des tâches de synchronisation de répertoires. Ce rôle intégré spécial ne peut pas être accordé en dehors de l'Assistant Azure AD Connect, et le portail Azure n'affiche ce compte qu'avec le rôle **Utilisateur**.

![Rôle de compte AD](./media/active-directory-aadconnect-accounts-permissions/aadsyncserviceaccountrole.png)

## Étapes suivantes

En savoir plus sur l'[Intégration de vos identités locales avec Azure Active Directory](active-directory-aadconnect.md).

<!---------HONumber=AcomDC_0309_2016-->