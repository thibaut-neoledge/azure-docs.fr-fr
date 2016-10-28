<properties
   pageTitle="Azure AD Connect : historique de publication des versions | Microsoft Azure"
   description="Cette rubrique répertorie toutes les versions d’Azure AD Connect et d’Azure AD Sync."
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="femila"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="08/23/2016"
   ms.author="andkjell"/>

# Azure AD Connect : historique de publication des versions

L’équipe Azure Active Directory met régulièrement à jour Azure AD Connect avec de nouvelles fonctions et fonctionnalités. Tous les ajouts ne sont pas applicables à toutes les configurations.

Cet article est conçu pour vous aider à conserver la trace des versions qui ont été publiées, et à comprendre si vous devez ou non effectuer la mise jour vers la version la plus récente.

Voici la liste des rubriques connexes :

Rubrique |  
--------- | --------- |
Étapes de mise à niveau à partir d’Azure AD Connect | Différentes méthodes pour [effectuer une mise à niveau à partir d’une version précédente vers la dernière](active-directory-aadconnect-upgrade-previous-version.md) version Azure AD Connect.
Autorisations requises | Pour plus d'informations sur les autorisations requises pour appliquer une mise à jour, consultez [Comptes et autorisations](active-directory-aadconnect-accounts-permissions.md#upgrade)
Télécharger| [Téléchargez Azure AD Connect](http://go.microsoft.com/fwlink/?LinkId=615771)

## 1\.1.281.0
Publiée : août 2016

**Problèmes résolus :**

- Les modifications apportées à l’intervalle de synchronisation n’ont pas lieu avant la fin du prochain cycle de synchronisation.
- L’Assistant Azure AD Connect n’accepte pas de compte Azure AD dont le nom d’utilisateur commence par un trait de soulignement (\_).
- L’Assistant Azure AD Connect ne parvient pas à authentifier le compte Azure AD fourni si le mot de passe du compte contient trop de caractères spéciaux. Un message d’erreur du type « Impossible de valider les informations d’identification. Une erreur inattendue s’est produite. » est renvoyé.
- La désinstallation du serveur intermédiaire désactive la synchronisation de mot de passe dans le client Azure AD et provoque l’échec de la synchronisation de mot de passe avec le serveur actif.
- La synchronisation du mot de passe échoue dans de rares cas lorsqu’aucun hachage de mot de passe n’est stocké sur l’utilisateur.
- Lorsque le serveur Azure AD Connect est activé pour le mode intermédiaire, l’écriture différée de mot de passe n’est pas temporairement désactivée.
- L’assistant Azure AD Connect n’affiche pas la configuration réelle de synchronisation de mot de passe et d’écriture différée de mot de passe lorsque le serveur est en mode intermédiaire. Il les affiche toujours comme étant désactivées.
- Les modifications apportées à la synchronisation de mot de passe et à l’écriture différée de mot de passe ne sont pas conservées par l’Assistant Azure AD lorsque le serveur est en mode intermédiaire.

**Améliorations :**

- Mise à jour de la cmdlet Start-ADSyncSyncCycle pour indiquer si elle est en mesure ou non de démarrer correctement un nouveau cycle de synchronisation.
- Ajout de la cmdlet Stop-ADSyncSyncCycle pour terminer le cycle de synchronisation et l’opération en cours.
- Mise à jour de la cmdlet Stop-ADSyncScheduler pour terminer le cycle de synchronisation et l’opération en cours.
- Lors de la configuration des [extensions d’annuaire](active-directory-aadconnectsync-feature-directory-extensions.md) dans l’Assistant Azure AD Connect, l’attribut AD de type « chaîne Teletex » peut maintenant être sélectionné.

## 1\.1.189.0
Publiée : juin 2016

**Problèmes résolus et améliorations :**

- Vous pouvez maintenant installer Azure AD Connect sur un serveur compatible FIPS.
    - Pour la synchronisation du mot de passe, consultez [Synchronisation de mot de passe et FIPS](active-directory-aadconnectsync-implement-password-synchronization.md#password-synchronization-and-fips)
- Correction d’un problème à cause duquel un nom NetBIOS ne pouvait pas être résolu pour le nom de domaine complet dans le connecteur Active Directory.

## 1\.1.180.0
Publication : mai 2016

**Nouvelles fonctionnalités :**

- Vous avertit et vous permet de vérifier les domaines si vous ne l’avez pas fait avant d’exécuter Azure AD Connect.
- Ajout de la prise en charge de [Microsoft Cloud Allemagne](active-directory-aadconnect-instances.md#microsoft-cloud-germany).
- Ajout de la prise en charge de la toute dernière infrastructure [cloud Microsoft Azure Government](active-directory-aadconnect-instances.md#microsoft-azure-government-cloud) avec de nouvelles exigences d’URL.

**Problèmes résolus et améliorations :**

- Ajout d’un filtrage à l’éditeur de règles de synchronisation pour faciliter la recherche de règles de synchronisation.
- Amélioration des performances lors de la suppression d’un espace connecteur.
- Résolution d’un problème lorsqu’un même objet a été supprimé et ajouté dans la même exécution (appelé supprimer/ajouter).
- Une règle de synchronisation désactivée ne réactivera plus les attributs et objets inclus lors d’une mise à niveau ou d’une actualisation de schéma d’annuaire.

## 1\.1.130.0
Publication : avril 2016

**Nouvelles fonctionnalités :**

- Ajout de la prise en charge des attributs à valeurs multiples dans les [Extensions d’annuaire](active-directory-aadconnectsync-feature-directory-extensions.md).
- Ajout de la prise en charge de variantes de configuration supplémentaires pour que la [mise à niveau automatique](active-directory-aadconnect-feature-automatic-upgrade.md) soit considérée comme éligible à la mise à niveau.
- Ajout d’applets de commande pour le [planificateur personnalisé](active-directory-aadconnectsync-feature-scheduler.md#custom-scheduler).

## 1\.1.119.0
Publié : mars 2016

**Problèmes résolus :**

- Nous nous sommes assurés que l’installation Express ne peut pas être utilisée sur Windows Server 2008 (pré-R2) car la synchronisation du mot de passe n’est pas prise en charge sur ce système d’exploitation.
- La mise à niveau à partir de DirSync avec une configuration de filtre personnalisée n’a pas fonctionné comme prévu.
- Lors de la mise à niveau vers une version plus récente et sans aucune modification de la configuration, une importation/synchronisation complète ne doit pas être planifiée.

## 1\.1.110.0
Publié : février 2016

**Problèmes résolus :**

- La mise à niveau à partir de versions antérieures ne fonctionne pas si l’installation ne se trouve pas dans le dossier **C:\\Program Files** par défaut.
- Si vous installez et désélectionnez **Démarrer le processus de synchronisation** à la fin de l’Assistant Installation, la réexécution de ce dernier n’active pas le planificateur.
- Le planificateur ne fonctionne pas comme prévu sur les serveurs où le format de date et d'heure n'est pas fr-FR. Il empêche également `Get-ADSyncScheduler` de retourner les heures correctes.
- Si vous avez installé une version antérieure d'Azure AD Connect avec ADFS comme option de connexion et mise à niveau, vous ne pouvez pas réexécuter l'assistant d’installation.

## 1\.1.105.0
Publié : février 2016

**Nouvelles fonctionnalités :**

- Fonctionnalité [Mise à niveau automatique](active-directory-aadconnect-feature-automatic-upgrade.md) pour les clients de la configuration rapide.
- Prise en charge pour l’administrateur général à l’aide de l’authentification MFA et de la gestion PIM dans l’Assistant d’installation.
    - Vous devez permettre à votre proxy d’autoriser le trafic vers https://secure.aadcdn.microsoftonline-p.com si vous utilisez l’authentification MFA.
    - Vous devez ajouter https://secure.aadcdn.microsoftonline-p.com à votre liste de sites de confiance pour que l’authentification MFA fonctionne correctement.
- Autorisez la modification de la méthode de connexion de l’utilisateur après l’installation initiale.
- Autorisez le [Filtrage par domaine ou unité d’organisation](active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering) dans l’Assistant Installation. Cela permet également la connexion à des forêts dans lesquelles tous les domaines ne sont pas disponibles.
- Le [planificateur](active-directory-aadconnectsync-feature-scheduler.md) est intégré au moteur de synchronisation.

**Fonctionnalités promues de version préliminaire à disponibilité générale :**

- [Écriture différée des appareils](active-directory-aadconnect-feature-device-writeback.md).
- [Extensions d’annuaire](active-directory-aadconnectsync-feature-directory-extensions.md).

**Nouvelles fonctionnalités préliminaires :**

- Le nouvel intervalle de cycle de synchronisation par défaut est de 30 minutes. (3 heures pour toutes les versions antérieures). Ajoute la prise en charge pour modifier le comportement du [planificateur](active-directory-aadconnectsync-feature-scheduler.md).

**Problèmes résolus :**

- La page de vérification des domaines DNS n’a pas reconnu tous les domaines.
- Demande d’informations d’identification de l’administrateur de domaine lors de la configuration AD FS.
- Les comptes AD locaux ne sont pas reconnus par l’Assistant d’installation lorsqu’ils sont situés dans un domaine avec une autre arborescence DNS que celle du domaine racine.

## 1\.0.9131.0
Publication : décembre 2015

**Problèmes résolus :**

- La synchronisation de mot de passe peut ne pas fonctionner lorsque vous modifiez les mots de passe dans AD DS, mais elle fonctionne lorsque vous définissez un mot de passe.
- Lorsque vous avez un serveur proxy, l’authentification à Azure AD peut échouer durant le processus d’installation ou de mise à niveau sur la page de configuration.
- La mise à jour à partir d’une version précédente d’Azure AD Connect avec un serveur SQL Server complet échoue si vous n’êtes pas administrateur dans SQL.
- La mise à jour à partir d’une version précédente d’Azure AD Connect avec un serveur SQL Server distant affiche l’erreur « Impossible d’accéder à la base de données SQL ADSync ».

## 1\.0.9125.0
Publié : novembre 2015

**Nouvelles fonctionnalités :**

- Reconfiguration des ADFS pour la confiance Azure AD.
- Actualisation du schéma Active Directory et nouvelle génération de règles de synchronisation.
- Désactivation d'une règle de synchronisation.
- Définition d'« AuthoritativeNull » comme nouvelle chaîne littérale dans une règle de synchronisation.

**Nouvelles fonctionnalités préliminaires :**

- [Azure AD Connect Health pour la synchronisation](active-directory-aadconnect-health-sync.md).
- Prise en charge de la synchronisation de mot de passe pour les [services de domaine Azure AD](active-directory-passwords-getting-started.md#enable-users-to-reset-or-change-their-ad-passwords).

**Nouveau scénario pris en charge :**

- Prise en charge de plusieurs organisations Exchange locales Pour plus d’informations, consultez la rubrique [Déploiements hybrides à forêts Active Directory multiples](https://technet.microsoft.com/library/jj873754.aspx).

**Problèmes résolus :**

- Problèmes liés à la synchronisation de mot de passe :
    - Le mot de passe d'un objet inclus dans l'étendue de la synchronisation alors qu'il ne l'était pas ne sera pas synchronisé. Ceci est valable pour l'unité d'organisation ou le filtrage des attributs.
    - La sélection d'une nouvelle unité d'organisation à inclure dans la synchronisation ne requiert pas une synchronisation de mot de passe complète.
    - Lorsqu'un utilisateur désactivé est activé, son mot de passe n'est pas synchronisée.
    - La file d'attente des nouvelles tentatives de mot de passe est infinie et la limite de 5 000 objets supprimés a été supprimée.
    - [Résolution optimisée des problèmes](active-directory-aadconnectsync-implement-password-synchronization.md#troubleshoot-password-synchronization).
- Impossible de se connecter à Active Directory avec le niveau fonctionnel de forêt Windows Server 2016.
- Impossible de modifier le groupe utilisé pour le filtrage de groupes après l'installation initiale.
- Un nouveau profil utilisateur n'est plus créé sur le serveur Connect de Azure AD Connect pour chaque utilisateur effectuant une modification de mot de passe avec l'écriture différée du mot de passe activée.
- Impossible d'utiliser des entiers longs dans des règles de synchronisation.
- La case à cocher « Écriture différée des appareils » reste désactivée s'il existe des contrôleurs de domaine inaccessibles.

## 1\.0.8667.0
Publiée : août 2015

**Nouvelles fonctionnalités :**

- L’Assistant Installation d’Azure AD Connect est maintenant localisé dans toutes les langues de Windows Server.
- Prise en charge du déverrouillage de compte lors de l’utilisation de la gestion des mots de passe d’Azure AD.

**Problèmes résolus :**

- L’Assistant Installation d’Azure AD Connect se bloque si un autre utilisateur continue l’installation à la place de la personne qui a commencé l’installation.
- Si une désinstallation précédente d’Azure AD Connect échoue à désinstaller correctement Azure AD Connect Sync, il n’est pas possible de le réinstaller.
- Impossible d’installer Azure AD Connect en utilisant l’installation rapide si l’utilisateur n’est pas dans le domaine racine de la forêt, ou si une version non anglaise d’Active Directory est utilisée.
- Si le nom de domaine complet du compte d’utilisateur Active Directory ne peut pas être résolu, un message d’erreur trompeur « Échec de la validation du schéma » est affiché.
- Si le compte utilisé sur le connecteur Active Directory est changé en dehors de l’Assistant, l’Assistant échoue lors des exécutions suivantes.
- L’installation d’Azure AD Connect sur un contrôleur de domaine échoue parfois.
- Impossible d’activer et de désactiver le « Mode de préproduction » si des attributs d’extension ont été ajoutés.
- L’écriture différée de mot de passe échoue dans certaines configurations en raison d’un mot de passe incorrect sur le connecteur Active Directory.
- Impossible de mettre à niveau DirSync si dn est utilisé lors du filtrage des attributs.
- Utilisation du processeur excessive lors de la réinitialisation du mot de passe.

**Fonctionnalités préliminaires supprimées :**

- La fonctionnalité préliminaire [Écriture différée d’utilisateur](active-directory-aadconnect-feature-preview.md#user-writeback) a été temporairement supprimée suite aux commentaires des clients de la version préliminaire. Nous la rajouterons une fois que nous aurons traité ces commentaires.

## 1\.0.8641.0
Publiée : juin 2015

**Version initiale d’Azure AD Connect.**

Changement de nom d’Azure AD Sync en Azure AD Connect.

**Nouvelles fonctionnalités :**

- Installation à l’aide de la [configuration rapide](active-directory-aadconnect-get-started-express.md)
- [Configuration d’AD FS](active-directory-aadconnect-get-started-custom.md#configuring-federation-with-ad-fs)
- [Mise à niveau à partir de DirSync](active-directory-aadconnect-dirsync-upgrade-get-started.md)
- [Prévention des suppressions accidentelles](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md)
- Introduction du [mode intermédiaire](active-directory-aadconnectsync-operations.md#staging-mode)

**Nouvelles fonctionnalités préliminaires :**

- [Écriture différée de l’utilisateur](active-directory-aadconnect-feature-preview.md#user-writeback)
- [Écriture différée de groupe](active-directory-aadconnect-feature-preview.md#group-writeback)
- [Écriture différée des appareils](active-directory-aadconnect-feature-device-writeback.md)
- [Extensions d’annuaire](active-directory-aadconnect-feature-preview.md#directory-extensions)


## 1\.0.494.0501
Publiée : mai 2015

**Nouvelle condition requise**

- Azure AD Sync requiert maintenant que le .NET Framework version 4.5.1 soit installé.

**Problèmes résolus :**

- L’écriture différée de mot de passe depuis Azure AD échoue avec une erreur de connectivité Service Bus.

## 1\.0.491.0413
Publiée : avril 2015

**Problèmes résolus et améliorations :**

- Le connecteur Active Directory ne traite pas correctement les suppressions si la Corbeille est activée et qu’il existe plusieurs domaines dans la forêt.
- Les performances des opérations d’importation ont été améliorées pour le connecteur Azure Active Directory.
- Quand un groupe avait dépassé la limite d’appartenance (par défaut, la limite est définie à 50 000 objets), le groupe était supprimé dans Azure Active Directory. Le nouveau comportement est le suivant : le groupe est conservé, une erreur est générée et aucune modification de nouvelle appartenance n’est exportée.
- Un nouvel objet ne peut pas être configuré si une suppression intermédiaire avec le même nom de domaine est déjà présente dans l’espace du connecteur.
- Certains objets sont marqués pour synchronisation lors d’une synchronisation différentielle, bien qu’il n’existe aucune modification intermédiaire sur l’objet.
- Forcer une synchronisation de mot de passe supprime également la liste des contrôleurs de domaine favoris.
- CSExportAnalyzer a des problèmes avec certains états des objets.

**Nouvelles fonctionnalités :**

- Une jonction peut maintenant connecter au type d’objet « ANY » dans le métaverse.

## 1\.0.485.0222
Publié : février 2015

**Améliorations :**

- Performances de l’importation améliorées.

**Problèmes résolus :**

- La synchronisation de mot de passe prend en compte l’attribut cloudFiltered utilisé par le filtrage des attributs. Les objets filtrés ne seront plus dans la portée pour la synchronisation de mot de passe.
- Dans de rares situations où la topologie avait un très grand nombre de contrôleurs de domaine, la synchronisation de mot de passe ne fonctionnait pas.
- « Arrêt serveur » lors de l’importation à partir du connecteur Azure AD après l’activation de la gestion des appareils dans Azure AD/Intune.
- La jonction d’entités de sécurité externes à partir de plusieurs domaines dans la même forêt provoque une erreur de jonction ambiguë.

## 1\.0.475.1202
Publiée : décembre 2014

**Nouvelles fonctionnalités :**

- Il est maintenant possible d’effectuer la synchronisation de mot de passe avec le filtrage basé sur les attributs. Pour plus d’informations, consultez [Synchronisation de mot de passe avec filtrage](active-directory-aadconnectsync-configure-filtering.md).
- L’attribut msDS-ExternalDirectoryObjectID est écrit en différé dans Active Directory. Ceci permet aux applications Office 365 utilisant OAuth2 d’accéder à la fois aux boîtes aux lettres en ligne et locales dans un déploiement hybride d’Exchange.

**Problèmes de mise à niveau résolus :**

- Une version plus récente de l’Assistant Connexion est disponible sur le serveur.
- Un chemin d’installation personnalisé était utilisé pour installer Azure AD Sync.
- Un critère de jonction personnalisée non valide bloque la mise à niveau.

**Autres correctifs :**

- Résolution du problème des modèles pour Office Professionnel Plus.
- Résolution des problèmes d’installation provoqué par des noms d’utilisateurs commençant par un tiret.
- Résolution du problème de perte de la valeur de sourceAnchor lors d’une deuxième exécution de l’Assistant Installation.
- Résolution du problème de suivi d’événements pour Windows pour la synchronisation de mot de passe

## 1\.0.470.1023
Publié : octobre 2014

**Nouvelles fonctionnalités :**

- Synchronisation du mot de passe à partir de plusieurs configurations AD locales vers Azure AD.
- Interface utilisateur du programme d’installation localisée dans toutes les langues de Windows Server.

**Mise à niveau à partir d’Azure Active Directory Sync 1.0 GA**

Si vous avez déjà installé Azure AD Sync, vous devez effectuer une étape supplémentaire au cas où vous avez modifié les règles de synchronisation prédéfinies. Une fois que vous avez mis à niveau vers la version 1.0.470.1023, les règles de synchronisation que vous avez modifiées sont dupliquées. Pour chaque règle de synchronisation modifiée, procédez comme suit :

- Recherchez la règle de synchronisation que vous avez modifiée et notez les modifications.
- Supprimez la règle de synchronisation.
- Recherchez la nouvelle règle de synchronisation créée par Azure AD Sync et réappliquez les modifications.

**Autorisations pour le compte Active Directory**

Le compte Active Directory doit disposer d’autorisations supplémentaires pour pouvoir lire les hachages de mot de passe à partir d’Active Directory. Les autorisations à accorder sont nommées « Réplication des changements de répertoire » et « Réplication de toutes les modifications de l’annuaire ». Les deux autorisations sont nécessaires pour pouvoir lire les hachages de mot de passe.

## 1\.0.419.0911
Publiée : septembre 2014

**Version initiale d’Azure AD Sync.**

## Étapes suivantes
En savoir plus sur l’[intégration de vos identités locales dans Azure Active Directory](active-directory-aadconnect.md).

<!---HONumber=AcomDC_0907_2016-->