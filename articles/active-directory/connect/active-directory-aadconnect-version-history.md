---
title: 'Azure AD Connect : historique de publication des versions | Microsoft Docs'
description: "Cet article répertorie toutes les versions d’Azure AD Connect et d’Azure AD Sync"
services: active-directory
documentationcenter: 
author: billmath
manager: femila
editor: 
ms.assetid: ef2797d7-d440-4a9a-a648-db32ad137494
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/08/2017
ms.author: billmath
translationtype: Human Translation
ms.sourcegitcommit: d9dad6cff80c1f6ac206e7fa3184ce037900fc6b
ms.openlocfilehash: 7e16fa749389ab876ae413e2ffef7713ed22adac
ms.lasthandoff: 03/06/2017


---
# <a name="azure-ad-connect-version-release-history"></a>Azure AD Connect : historique de publication des versions
L’équipe Azure Active Directory (Azure AD) met régulièrement à jour Azure AD Connect avec de nouvelles fonctions et fonctionnalités. Tous les ajouts ne sont pas applicables à toutes les configurations.

Cet article est conçu pour vous aider à conserver la trace des versions qui ont été publiées, et à comprendre si vous devez ou non effectuer la mise jour vers la version la plus récente.

Voici la liste des rubriques connexes :


Rubrique |  Détails
--------- | --------- |
Étapes de mise à niveau à partir d’Azure AD Connect | Différentes méthodes pour [effectuer une mise à niveau à partir d’une version précédente vers la dernière](active-directory-aadconnect-upgrade-previous-version.md) version Azure AD Connect.
Autorisations requises | Pour plus d’informations sur les autorisations requises afin d’appliquer une mise à jour, consultez [Comptes et autorisations](./active-directory-aadconnect-accounts-permissions.md#upgrade).
Télécharger| [Téléchargez Azure AD Connect](http://go.microsoft.com/fwlink/?LinkId=615771).

## <a name="114430"></a>1.1.443.0
Publication : mars 2017

**Problèmes résolus :**

Synchronisation d’Azure AD Connect
* Résolution d’un problème qui provoquait l’échec de l’assistant Azure AD Connect si le nom complet du connecteur Azure AD ne contenait pas le domaine onmicrosoft.com initialement affecté au client Azure AD.
* Résolution d’un problème qui provoquait l’échec de l’assistant Azure AD Connect lors de la connexion à une base de données SQL lorsque le mot de passe du compte de service de synchronisation contenait des caractères spéciaux tels que l’apostrophe, les deux-points ou l’espace.
* Correction d’un problème qui provoquait l’erreur « la dimage a une ancre qui est différente de celle de l’image » sur un serveur Azure AD Connect en mode intermédiaire après avoir exclu temporairement an objet Active Directory local de la synchronisation et l’avoir inclus à nouveau pour la synchronisation.
* Correction d’un problème qui provoque l’erreur « l’objet se trouve par nom de domaine est un fantôme » sur un serveur Azure AD Connect en mode, intermédiaire une fois que vous avez exclu temporairement sur les sites Active Directory à partir de la synchronisation de l’objet et inclus à nouveau pour la synchronisation.

Gestion AD FS.
* Correction d’un problème qui empêchait l’assistant Azure AD Connect de mettre à jour la configuration AD FS et définir les bonnes revendications sur l’approbation de partie de confiance après configuration de l’ID de connexion secondaire.
* Correction d’un problème qui empêchait l’assistant Azure AD Connect de traiter correctement les serveurs AD FS dont les comptes de service sont configurés à l’aide du format d’userPrincipalName au lieu du format sAMAccountName.

Authentification directe
* Résolution d’un problème qui provoquait l’échec de l’assistant Azure AD Connect si l’authentification directe était activée mais que l’inscription de son connecteur échouait.
* Résolution du problème qui provoquait le contournement des vérifications de validation par l’assistant Azure AD Connect sur la méthode de connexion sélectionnée lorsque la fonctionnalité d’authentification unique de bureau était activée.

**Nouvelles fonctionnalités/améliorations :**

Synchronisation d’Azure AD Connect
* L’applet de commande Get-ADSyncScheduler renvoie désormais une nouvelle propriété booléenne nommée SyncCycleInProgress. Si la valeur renvoyée est true, cela signifie qu’il existe un cycle de synchronisation planifié en cours.
* Le dossier de destination pour les journaux d’installation et de configuration d’Azure Connect AD a été déplacé de %localappdata%\AADConnect à %programdata%\AADConnect pour améliorer l’accessibilité aux fichiers journaux.

Gestion AD FS.
* Prise en charge supplémentaire de la mise à jour du certificat SSL de batterie de serveurs AD FS.
* Prise en charge ajoutée pour la gestion de serveurs AD FS 2016.
* Vous pouvez maintenant spécifier un gMSA (compte de service géré de groupe) existant lors de l’installation d’AD FS.
* Vous pouvez maintenant configurer SHA-256 comme algorithme de hachage de signature pour l’approbation de partie de confiance Azure AD.

## <a name="113800"></a>1.1.380.0
Publication : décembre 2016

**Problème résolu :**

* Résolution du problème d’absence de la règle de revendication issuerid pour Active Directory Federation Services (AD FS) dans la version.

>[!NOTE]
>Cette version n’est pas disponible pour les clients par le biais de la fonction de mise à niveau automatique Azure AD Connect.

## <a name="113710"></a>1.1.371.0
Publication : décembre 2016

**Problème connu :**

* Cette version de contenait pas la règle de revendication issuerid pour AD FS. La règle de revendication issuerid est nécessaire si vous fédérez plusieurs domaines avec Azure Active Directory (Azure AD). Si vous utilisez Azure AD Connect pour gérer votre déploiement AD FS local, la mise à niveau vers cette version supprime la règle de revendication issuerid existant dans votre configuration AD FS. Vous pouvez contourner ce problème en ajoutant la règle de revendication issuerid après l’installation ou la mise à niveau. Pour plus d’informations sur l’ajout de la règle de revendication issuerid, consultez l’article [Prise en charge de plusieurs domaines pour la fédération avec Azure AD](active-directory-aadconnect-multiple-domains.md).

**Problème résolu :**

* Si le port 9090 n’est pas ouvert pour les connexions sortantes, l’installation ou la mise à niveau d’Azure AD Connect échoue.

>[!NOTE]
>Cette version n’est pas disponible pour les clients par le biais de la fonction de mise à niveau automatique Azure AD Connect.

## <a name="113700"></a>1.1.370.0
Publication : décembre 2016

**Problèmes connus :**

* Cette version de contenait pas la règle de revendication issuerid pour AD FS. La règle de revendication issuerid est nécessaire si vous fédérez plusieurs domaines avec Azure AD. Si vous utilisez Azure AD Connect pour gérer votre déploiement AD FS local, la mise à niveau vers cette version supprime la règle de revendication issuerid existant dans votre configuration AD FS. Vous pouvez contourner ce problème en ajoutant la règle de revendication issuerid après l’installation ou la mise à niveau. Pour plus d’informations sur l’ajout de la règle de revendication issuerid, consultez l’article [Prise en charge de plusieurs domaines pour la fédération avec Azure AD](active-directory-aadconnect-multiple-domains.md).
* Le port 9090 doit être ouvert en sortie pour achever l’installation.

**Nouvelles fonctionnalités :**

* Authentification directe (version préliminaire).

>[!NOTE]
>Cette version n’est pas disponible pour les clients par le biais de la fonction de mise à niveau automatique Azure AD Connect.

## <a name="113430"></a>1.1.343.0
Publication : novembre 2016

**Problème connu :**

* Cette version de contenait pas la règle de revendication issuerid pour AD FS. La règle de revendication issuerid est nécessaire si vous fédérez plusieurs domaines avec Azure AD. Si vous utilisez Azure AD Connect pour gérer votre déploiement AD FS local, la mise à niveau vers cette version supprime la règle de revendication issuerid existant dans votre configuration AD FS. Vous pouvez contourner ce problème en ajoutant la règle de revendication issuerid après l’installation ou la mise à niveau. Pour plus d’informations sur l’ajout de la règle de revendication issuerid, consultez l’article [Prise en charge de plusieurs domaines pour la fédération avec Azure AD](active-directory-aadconnect-multiple-domains.md).

**Problèmes résolus :**

* Parfois, l’installation d’Azure AD Connect échoue, car il est impossible de créer un compte de service local dont le mot de passe est conforme au niveau de complexité spécifié par la stratégie de mot de passe de l’organisation.
* Correction d’un problème dans lequel les règles de jointure ne sont pas réévaluées lorsqu’un objet dans l’espace du connecteur devient simultanément hors de portée pour une règle de jointure et dans la portée d’une autre. Cela peut se produire si vous avez deux ou plusieurs règles de jointure dont les conditions de jointure s’excluent mutuellement.
* Correction d’un problème dans lequel les règles de synchronisation entrante (à partir d’Azure AD) qui ne contiennent pas de règles de jointure ne sont pas traitées si elles ont des valeurs de priorité plus faibles que celles contenant des règles de jointure.

**Améliorations :**

* Ajout de la prise en charge de l’installation d’Azure AD Connect sur Windows Server 2016 standard ou version ultérieure.
* Ajout de la prise en charge de l’utilisation de SQL Server 2016 comme base de données distante pour Azure AD Connect.

## <a name="112810"></a>1.1.281.0
Publication : août 2016

**Problèmes résolus :**

* Les modifications apportées à l’intervalle de synchronisation n’ont pas lieu avant la fin du prochain cycle de synchronisation.
* L’Assistant Azure AD Connect n’accepte pas de compte Azure AD dont le nom d’utilisateur commence par un trait de soulignement (\_).
* L’Assistant Azure AD Connect ne parvient pas à authentifier le compte Azure AD fourni si le mot de passe du compte contient un trop grand nombre de caractères spéciaux. Un message d’erreur du type « Impossible de valider les informations d’identification. Une erreur inattendue s’est produite. » est renvoyé.
* La désinstallation du serveur intermédiaire désactive la synchronisation de mot de passe dans le client Azure AD et provoque l’échec de la synchronisation de mot de passe avec le serveur actif.
* La synchronisation du mot de passe échoue dans de rares cas lorsqu’aucun hachage de mot de passe n’est stocké sur l’utilisateur.
* Lorsque le serveur Azure AD Connect est activé pour le mode intermédiaire, l’écriture différée de mot de passe n’est pas temporairement désactivée.
* L’assistant Azure AD Connect n’affiche pas la configuration réelle de synchronisation de mot de passe et d’écriture différée de mot de passe lorsque le serveur est en mode intermédiaire. Il les affiche toujours comme étant désactivées.
* Les modifications apportées à la synchronisation de mot de passe et à l’écriture différée de mot de passe ne sont pas conservées par l’Assistant Azure AD lorsque le serveur est en mode intermédiaire.

**Améliorations :**

* Mise à jour de l’applet de commande Start-ADSyncSyncCycle pour indiquer si elle est en mesure ou non de démarrer correctement un nouveau cycle de synchronisation.
* Ajout de l’applet de commande Stop-ADSyncSyncCycle pour terminer le cycle de synchronisation et l’opération en cours.
* Mise à jour de l’applet de commande Stop-ADSyncScheduler pour terminer le cycle de synchronisation et l’opération en cours.
* Lors de la configuration des [extensions d’annuaire](active-directory-aadconnectsync-feature-directory-extensions.md) dans l’Assistant Azure AD Connect, l’attribut Azure AD de type « chaîne Teletex » peut maintenant être sélectionné.

## <a name="111890"></a>1.1.189.0
Publication : juin 2016

**Problèmes résolus et améliorations :**

* Vous pouvez maintenant installer Azure AD Connect sur un serveur compatible FIPS.
  * Pour la synchronisation du mot de passe, consultez [Synchronisation de mot de passe et FIPS](active-directory-aadconnectsync-implement-password-synchronization.md#password-synchronization-and-fips).
* Correction d’un problème à cause duquel un nom NetBIOS ne pouvait pas être résolu pour le nom de domaine complet dans le connecteur Active Directory.

## <a name="111800"></a>1.1.180.0
Publication : mai 2016

**Nouvelles fonctionnalités :**

* Vous avertit et vous permet de vérifier les domaines si vous ne l’avez pas fait avant d’exécuter Azure AD Connect.
* Ajout de la prise en charge de [Microsoft Cloud Allemagne](active-directory-aadconnect-instances.md#microsoft-cloud-germany).
* Ajout de la prise en charge de la toute dernière infrastructure [cloud Microsoft Azure Government](active-directory-aadconnect-instances.md#microsoft-azure-government-cloud) avec de nouvelles exigences d’URL.

**Problèmes résolus et améliorations :**

* Ajout d’un filtrage à l’éditeur de règles de synchronisation pour faciliter la recherche de règles de synchronisation.
* Amélioration des performances lors de la suppression d’un espace connecteur.
* Résolution d’un problème lorsqu’un même objet a été supprimé et ajouté dans la même exécution (appelé supprimer/ajouter).
* Une règle de synchronisation désactivée ne réactive plus les attributs et objets inclus lors d’une mise à niveau ou d’une actualisation de schéma d’annuaire.

## <a name="111300"></a>1.1.130.0
Publication : avril 2016

**Nouvelles fonctionnalités :**

* Ajout de la prise en charge des attributs à valeurs multiples dans les [extensions d’annuaire](active-directory-aadconnectsync-feature-directory-extensions.md).
* Ajout de la prise en charge de variantes de configuration supplémentaires pour que la [mise à niveau automatique](active-directory-aadconnect-feature-automatic-upgrade.md) soit considérée comme éligible à la mise à niveau.
* Ajout d’applets de commande pour le [planificateur personnalisé](active-directory-aadconnectsync-feature-scheduler.md#custom-scheduler).

## <a name="111190"></a>1.1.119.0
Publication : mars 2016

**Problèmes résolus :**

* Nous nous sommes assurés que l’installation Express ne peut pas être utilisée sur Windows Server 2008 (version antérieure à R2), car la synchronisation du mot de passe n’est pas prise en charge sur ce système d’exploitation.
* La mise à niveau à partir de DirSync avec une configuration de filtre personnalisée n’a pas fonctionné comme prévu.
* Lors de la mise à niveau vers une version plus récente et sans aucune modification de la configuration, une importation/synchronisation complète ne doit pas être planifiée.

## <a name="111100"></a>1.1.110.0
Publication : février 2016

**Problèmes résolus :**

* La mise à niveau à partir de versions antérieures ne fonctionne pas si l’installation ne se trouve pas dans le dossier C:\Program Files par défaut.
* Si vous installez et désélectionnez **Démarrer le processus de synchronisation** à la fin de l’Assistant d’installation, la réexécution de ce dernier n’active pas le planificateur.
* Le planificateur ne fonctionne pas comme prévu sur les serveurs où le format de date et d’heure n’est pas en-US. Il empêche également `Get-ADSyncScheduler` de retourner les heures correctes.
* Si vous avez installé une version antérieure d’Azure AD Connect avec AD FS comme option de connexion et mise à niveau, vous ne pouvez pas réexécuter l’Assistant d’installation.

## <a name="111050"></a>1.1.105.0
Publication : février 2016

**Nouvelles fonctionnalités :**

* [Automatic upgrade](active-directory-aadconnect-feature-automatic-upgrade.md) pour les clients de la configuration rapide.
* Prise en charge pour l’administrateur général par le biais d’Azure Multi-Factor Authentication et de Privileged Identity Management dans l’Assistant d’installation.
  * Vous devez configurer votre proxy pour qu’il autorise également le trafic vers https://secure.aadcdn.microsoftonline-p.com si vous utilisez Multi-Factor Authentication.
  * Vous devez ajouter https://secure.aadcdn.microsoftonline-p.com à votre liste de sites de confiance pour que Multi-Factor Authentication fonctionne correctement.
* Autorisez la modification de la méthode de connexion de l’utilisateur après l’installation initiale.
* Activez l’option [Filtrage par domaine et unité d’organisation](active-directory-aadconnect-get-started-custom.md#domain-and-ou-filtering) dans l’Assistant d’installation. Cela permet également la connexion à des forêts dans lesquelles tous les domaines ne sont pas disponibles.
* [Scheduler](active-directory-aadconnectsync-feature-scheduler.md) est intégré dans le moteur de synchronisation.

**Fonctionnalités promues de version préliminaire à disponibilité générale :**

* [Écriture différée des appareils](active-directory-aadconnect-feature-device-writeback.md).
* [Extensions d’annuaire](active-directory-aadconnectsync-feature-directory-extensions.md).

**Nouvelles fonctionnalités préliminaires :**

* Le nouvel intervalle de cycle de synchronisation par défaut est de 30 minutes. (trois heures pour toutes les versions antérieures). Ajoute la prise en charge pour modifier le comportement du [planificateur](active-directory-aadconnectsync-feature-scheduler.md) .

**Problèmes résolus :**

* La page de vérification des domaines DNS n’a pas reconnu tous les domaines.
* Demande d’informations d’identification de l’administrateur de domaine lors de la configuration AD FS.
* Les comptes AD locaux ne sont pas reconnus par l’Assistant d’installation lorsqu’ils sont situés dans un domaine avec une autre arborescence DNS que celle du domaine racine.

## <a name="1091310"></a>1.0.9131.0
Publication : décembre 2015

**Problèmes résolus :**

* La synchronisation de mot de passe peut ne pas fonctionner lorsque vous modifiez les mots de passe dans Active Directory Domain Services (AD DS), mais elle fonctionne lorsque vous définissez un mot de passe.
* Lorsque vous avez un serveur proxy, l’authentification à Azure AD peut échouer durant le processus d’installation, ou si la mise à niveau est annulée sur la page de configuration.
* La mise à jour à partir d’une version antérieure d’Azure AD Connect avec une instance SQL Server complète échoue si vous n’êtes pas administrateur système SQL Server (SA).
* La mise à jour à partir d’une version antérieure d’Azure AD Connect avec un serveur SQL Server distant affiche l’erreur « Impossible d’accéder à la base de données SQL ADSync ».

## <a name="1091250"></a>1.0.9125.0
Publication : novembre 2015

**Nouvelles fonctionnalités :**

* Reconfiguration d’AD FS pour l’approbation Azure AD.
* Actualisation du schéma Active Directory et nouvelle génération de règles de synchronisation.
* Désactivation d'une règle de synchronisation.
* Définition d’« AuthoritativeNull » comme nouvelle chaîne littérale dans une règle de synchronisation.

**Nouvelles fonctionnalités préliminaires :**

* [Azure AD Connect Health pour la synchronisation](../connect-health/active-directory-aadconnect-health-sync.md).
* Prise en charge de la synchronisation de mot de passe pour les [services de domaine Azure AD](../active-directory-passwords-getting-started.md#enable-users-to-reset-or-change-their-ad-passwords) .

**Nouveau scénario pris en charge :**

* Prise en charge de plusieurs organisations Exchange locales Pour plus d’informations, consultez la rubrique [Déploiements hybrides à forêts Active Directory multiples](https://technet.microsoft.com/library/jj873754.aspx).

**Problèmes résolus :**

* Problèmes liés à la synchronisation de mot de passe :
  * Le mot de passe d'un objet inclus dans l'étendue de la synchronisation alors qu'il ne l'était pas ne sera pas synchronisé. Cela est valable pour l’unité d’organisation et le filtrage des attributs.
  * La sélection d'une nouvelle unité d'organisation à inclure dans la synchronisation ne requiert pas une synchronisation de mot de passe complète.
  * Lorsqu'un utilisateur désactivé est activé, son mot de passe n'est pas synchronisée.
  * La file d'attente des nouvelles tentatives de mot de passe est infinie et la limite de 5 000 objets supprimés a été supprimée.
  * [Résolution optimisée des problèmes](active-directory-aadconnectsync-implement-password-synchronization.md#troubleshooting-password-synchronization).
* Impossible de se connecter à Active Directory avec le niveau fonctionnel de forêt Windows Server 2016.
* Impossible de modifier le groupe utilisé pour le filtrage de groupes après l’installation initiale.
* Aucun profil utilisateur n’est plus créé sur le serveur Azure AD Connect pour chaque utilisateur effectuant une modification de mot de passe avec l’écriture différée du mot de passe activée.
* Impossible d’utiliser des entiers longs dans des règles de synchronisation.
* La case à cocher « Écriture différée des appareils » reste désactivée s’il existe des contrôleurs de domaine inaccessibles.

## <a name="1086670"></a>1.0.8667.0
Publication : août 2015

**Nouvelles fonctionnalités :**

* L’Assistant Installation d’Azure AD Connect est maintenant localisé dans toutes les langues de Windows Server.
* Prise en charge du déverrouillage de compte lors de l’utilisation de la gestion des mots de passe d’Azure AD.

**Problèmes résolus :**

* L’Assistant Installation d’Azure AD Connect se bloque si un autre utilisateur continue l’installation à la place de la personne qui a commencé l’installation.
* Si une désinstallation précédente d’Azure AD Connect ne parvient pas à désinstaller correctement Azure AD Connect Sync, il n’est pas possible de le réinstaller.
* Impossible d’installer Azure AD Connect en utilisant l’installation rapide si l’utilisateur n’est pas dans le domaine racine de la forêt, ou si une version non anglaise d’Active Directory est utilisée.
* Si le nom de domaine complet du compte d’utilisateur Active Directory ne peut pas être résolu, un message d’erreur trompeur « Échec de la validation du schéma » est affiché.
* Si le compte utilisé sur le connecteur Active Directory est modifié en dehors de l’Assistant, l’Assistant échoue lors des exécutions suivantes.
* L’installation d’Azure AD Connect sur un contrôleur de domaine échoue parfois.
* Impossible d’activer et de désactiver le « Mode de préproduction » si des attributs d’extension ont été ajoutés.
* L’écriture différée de mot de passe échoue dans certaines configurations en raison d’un mot de passe incorrect sur le connecteur Active Directory.
* Impossible de mettre à niveau DirSync si un nom unique (DN) est utilisé lors du filtrage des attributs.
* Utilisation du processeur excessive lors de la réinitialisation du mot de passe.

**Fonctionnalités préliminaires supprimées :**

* La fonctionnalité préliminaire [Écriture différée d’utilisateur](active-directory-aadconnect-feature-preview.md#user-writeback) a été temporairement supprimée suite aux commentaires des clients de la version préliminaire. Nous la rajouterons une fois que nous aurons traité ces commentaires.

## <a name="1086410"></a>1.0.8641.0
Publication : juin 2015

**Version initiale d’Azure AD Connect.**

Changement de nom d’Azure AD Sync en Azure AD Connect.

**Nouvelles fonctionnalités :**

* Installation à l’aide de la [configuration rapide](active-directory-aadconnect-get-started-express.md)
* [Configuration d’AD FS](active-directory-aadconnect-get-started-custom.md#configuring-federation-with-ad-fs)
* [Mise à niveau à partir de DirSync](active-directory-aadconnect-dirsync-upgrade-get-started.md)
* [Prévention des suppressions accidentelles](active-directory-aadconnectsync-feature-prevent-accidental-deletes.md)
* Introduction du [mode intermédiaire](active-directory-aadconnectsync-operations.md#staging-mode)

**Nouvelles fonctionnalités préliminaires :**

* [Écriture différée de l’utilisateur](active-directory-aadconnect-feature-preview.md#user-writeback)
* [Écriture différée de groupe](active-directory-aadconnect-feature-preview.md#group-writeback)
* [Écriture différée des appareils](active-directory-aadconnect-feature-device-writeback.md)
* [Extensions d’annuaire](active-directory-aadconnect-feature-preview.md)

## <a name="104940501"></a>1.0.494.0501
Publication : mai 2015

**Nouvelle condition requise**

* Azure AD Sync requiert maintenant que .NET Framework version 4.5.1 soit installé.

**Problèmes résolus :**

* L’écriture différée de mot de passe à partir d’Azure AD échoue en raison d’une erreur de connectivité Azure Service Bus.

## <a name="104910413"></a>1.0.491.0413
Publication : avril 2015

**Problèmes résolus et améliorations :**

* Le connecteur Active Directory ne traite pas correctement les suppressions si la Corbeille est activée et qu’il existe plusieurs domaines dans la forêt.
* Les performances des opérations d’importation ont été améliorées pour le connecteur Azure Active Directory.
* Lorsqu’un groupe dépassait la limite d’appartenance (par défaut, la limite est définie à 50 000 objets), le groupe était supprimé dans Azure Active Directory. Désormais, le groupe n’est pas supprimé. Une erreur est générée et les nouveaux changements d’appartenance ne sont pas exportés.
* Un nouvel objet ne peut pas être configuré si une suppression intermédiaire avec le même nom de domaine est déjà présente dans l’espace du connecteur.
* Certains objets sont marqués pour synchronisation lors d’une synchronisation différentielle même si aucune modification intermédiaire n’est apportée à l’objet.
* Forcer une synchronisation de mot de passe supprime également la liste des contrôleurs de domaine favoris.
* CSExportAnalyzer a des problèmes avec certains états des objets.

**Nouvelles fonctionnalités :**

* Une jonction peut maintenant connecter au type d’objet « ANY » dans le métaverse.

## <a name="104850222"></a>1.0.485.0222
Publication : février 2015

**Améliorations :**

* Performances de l’importation améliorées.

**Problèmes résolus :**

* La synchronisation de mot de passe prend en compte l’attribut cloudFiltered utilisé par le filtrage des attributs. Les objets filtrés ne sont plus dans la portée pour la synchronisation de mot de passe.
* Dans de rares situations où la topologie avait un grand nombre de contrôleurs de domaine, la synchronisation de mot de passe ne fonctionnait pas.
* « Arrêt serveur » lors de l’importation à partir du connecteur Azure AD après l’activation de la gestion des appareils dans Azure AD/Intune.
* La jonction d’entités de sécurité externes à partir de plusieurs domaines dans la même forêt provoque une erreur de jonction ambiguë.

## <a name="104751202"></a>1.0.475.1202
Publication : décembre 2014

**Nouvelles fonctionnalités :**

* Il est maintenant possible d’effectuer la synchronisation de mot de passe avec le filtrage basé sur les attributs. Pour plus d’informations, consultez [Synchronisation de mot de passe avec filtrage](active-directory-aadconnectsync-configure-filtering.md).
* L’attribut msDS-ExternalDirectoryObjectID est écrit en différé dans Active Directory. Cette fonctionnalité prend en charge les applications Office 365. Elle utilise OAuth2 pour accéder aux boîtes aux lettres en ligne et en local dans un déploiement d’Exchange hybride.

**Problèmes de mise à niveau résolus :**

* Une version plus récente de l’Assistant Connexion est disponible sur le serveur.
* Un chemin d’installation personnalisé était utilisé pour installer Azure AD Sync.
* Un critère de jonction personnalisée non valide bloque la mise à niveau.

**Autres correctifs :**

* Résolution du problème des modèles pour Office Professionnel Plus.
* Résolution des problèmes d’installation provoqué par des noms d’utilisateurs commençant par un tiret.
* Résolution du problème de perte de la valeur de sourceAnchor lors d’une deuxième exécution de l’Assistant Installation.
* Résolution du traçage ETW pour la synchronisation de mot de passe.

## <a name="104701023"></a>1.0.470.1023
Publication : octobre 2014

**Nouvelles fonctionnalités :**

* Synchronisation du mot de passe à partir de plusieurs configurations Active Directory locales vers Azure AD.
* Interface utilisateur du programme d’installation localisée dans toutes les langues de Windows Server.

**Mise à niveau à partir d’Azure Active Directory Sync 1.0 GA**

Si vous avez déjà installé Azure AD Sync, vous devez effectuer une étape supplémentaire dans le cas où vous avez modifié les règles de synchronisation prédéfinies. Une fois que vous avez mis à niveau vers la version 1.0.470.1023, les règles de synchronisation que vous avez modifiées sont dupliquées. Pour chaque règle de synchronisation modifiée, procédez comme suit :

1.  Recherchez la règle de synchronisation que vous avez modifiée et notez les modifications.
* Supprimez la règle de synchronisation.
* Recherchez la nouvelle règle de synchronisation créée par Azure AD Sync et réappliquez les modifications.

**Autorisation pour le compte Active Directory**

Le compte Active Directory doit disposer d’autorisations supplémentaires pour permettre la lecture des hachages de mot de passe à partir d’Active Directory. Les autorisations à accorder sont nommées « Réplication des changements de répertoire » et « Réplication de toutes les modifications de l’annuaire ». Les deux autorisations sont nécessaires pour permettre la lecture des hachages de mot de passe.

## <a name="104190911"></a>1.0.419.0911
Publication : septembre 2014

**Version initiale d’Azure AD Sync.**

## <a name="next-steps"></a>Étapes suivantes
En savoir plus sur l’ [intégration de vos identités locales avec Azure Active Directory](active-directory-aadconnect.md).

