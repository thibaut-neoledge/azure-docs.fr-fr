<properties
   pageTitle="Azure AD Connect : Mise à niveau automatique | Microsoft Azure"
   description="Cette rubrique décrit la fonctionnalité de mise à niveau automatique intégrée dans Azure AD Connect Sync."
   services="active-directory"
   documentationCenter=""
   authors="AndKjell"
   manager="StevenPo"
   editor=""/>

<tags
   ms.service="active-directory"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="identity"
   ms.date="04/15/2016"
   ms.author="andkjell"/>

# Azure AD Connect : Mise à niveau automatique
Cette fonctionnalité a été introduite avec la version 1.1.105.0 (publiée en février 2016).

## Vue d'ensemble
Grâce à la fonctionnalité de **mise à niveau automatique**, vous pouvez facilement vous assurer que votre installation Azure AD Connect est à jour. Cette fonctionnalité est activée par défaut pour les installations rapides et garantit que lorsqu’une nouvelle version est publiée, votre installation est automatiquement mise à niveau.

La mise à niveau automatique est activée par défaut pour les éléments suivants :

- Installation à l’aide de la configuration rapide
- SQL Express LocalDB, toujours utilisée par la configuration rapide
- Le compte AD est le compte MSOL\_ par défaut créé par la configuration rapide
- Avoir moins de 100 000 objets dans le métaverse

L’état actuel de la mise à niveau automatique peut être affiché avec l’applet de commande PowerShell `Get-ADSyncAutoUpgrade`. Elle peut avoir les états suivants :

| State | Commentaire |
| ---- | ---- |
| Activé | La mise à niveau automatique est activée. |
| Interrompu | Défini par le système uniquement. Le système n’est plus autorisé à recevoir des mises à niveau automatiques. |
| Désactivé | La mise à niveau automatique est désactivée. |

Vous pouvez passer de **Activé** à **Désactivé** avec `Set-ADSyncAutoUpgrade`. Seul le système doit définir l’état **Interrompu**.

La mise à niveau automatique utilise Azure AD Connect Health comme infrastructure de mise à niveau. Pour qu’une mise à niveau automatique fonctionne correctement, assurez-vous d’avoir ouvert l’URL dans le proxy pour **Azure AD Connect Health** comme indiqué à la rubrique [URL et plages d’adresses IP Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2).

Si l’interface utilisateur du **Synchronization Service Manager** est en cours d’exécution sur le serveur, la mise à niveau sera suspendue jusqu’à la fermeture de l’interface utilisateur.

## Résolution des problèmes
Si votre installation Connect ne se met pas elle-même à niveau comme prévu, procédez comme suit pour savoir d’où vient le problème.

Tout d'abord, ne vous attendez pas à ce qu’il y ait déjà une tentative de mise à niveau automatique le jour du lancement d’une nouvelle version. Toute tentative de mise à niveau a un caractère aléatoire intentionnel. Ne vous inquiétez pas si votre installation n'est pas mise à niveau immédiatement.

Si vous pensez que quelque chose ne convient pas, commencez par exécuter `Get-ADSyncAutoUpgrade` pour garantir l’activation de la mise à niveau automatique.

Démarrez le journal des événements et consultez le journal des événements de l’**Application**. Ajoutez un filtre de journal des événements pour la source **mise à niveau d’Azure AD Connect** et la plage d'id d’événements **300-399**. ![Filtre de journal des événements pour la mise à niveau automatique](./media/active-directory-aadconnect-feature-automatic-upgrade/eventlogfilter.png)

Vous obtiendrez les journaux d'événements associé à l'état de mise à niveau automatique. ![Filtre de journal des événements pour la mise à niveau automatique](./media/active-directory-aadconnect-feature-automatic-upgrade/eventlogresult.png)

La première moitié du résultat vous donnera une vue d'ensemble de l'état.

| Préfixe du résultat | Description |
| --- | --- |
| Succès | La mise à niveau de l’installation a réussi. |
| UpgradeAborted | Un problème temporaire a arrêté la mise à niveau. Elle sera tentée à nouveau et on s’attend à ce qu'elle réussisse. |
| UpgradeNotSupported | La configuration du système entraîne le blocage de sa mise à niveau automatique. Elle sera tentée à nouveau pour voir si l'état est modifié, mais l'on s’attend à ce que le système doive être mis à niveau manuellement. |

Voici une liste de messages les plus courants que vous trouverez. Elle n’est pas exhaustive, mais le message de résultat doit exposer clairement la nature du problème.

| Message de résultat | Description |
| --- | --- |
| **UpgradeAborted** | |
| UpgradeAbortedSyncExeInUse | L’[interface utilisateur du gestionnaire des services de synchronisation](active-directory-aadconnectsync-service-manager-ui.md) est ouverte sur le serveur.
| UpgradeAbortedInsufficientDiskSpace | Il n'y a pas suffisamment d'espace disque pour prendre en charge une mise à niveau. |
| UpgradeAbortedSyncCycleDisabled | L'option SyncCycle dans le [planificateur](active-directory-aadconnectsync-feature-scheduler.md) a été désactivée. |
| UpgradeAbortedSyncOrConfigurationInProgress | L'Assistant Installation est en cours d'exécution ou une synchronisation a été planifiée à l'extérieur du planificateur. |
| **UpgradeNotSupported** | |
| UpgradeNotSupportedCustomizedSyncRules | Vous avez ajouté vos propres règles de personnalisation à la configuration. |
| UpgradeNotSupportedDeviceWritebackEnabled | Vous avez activé la fonctionnalité [Écriture différée des appareils](active-directory-aadconnect-feature-device-writeback.md). |
| UpgradeNotSupportedGroupWritebackEnabled | Vous avez activé la fonctionnalité [Écriture différée de groupe](active-directory-aadconnect-feature-preview.md#group-writeback). |
| UpgradeNotSupportedMetaverseSizeExceeeded | Vous avez plus de 100 000 objets dans le métaverse. |
| UpgradeNotSupportedMultiForestSetup | Vous vous connectez à plusieurs forêts. L’installation rapide se connectera à une seule forêt. |
| UpgradeNotSupportedNonMsolAccount | Le [compte de connecteur AD](active-directory-aadconnect-accounts-permissions.md#active-directory-account) n'est plus le compte\_ MSOL par défaut.
| UpgradeNotSupportedStagingModeEnabled | Le serveur est défini pour être en [mode intermédiaire](active-directory-aadconnectsync-operations.md#staging-mode). |

## Étapes suivantes
En savoir plus sur l’[intégration de vos identités locales avec Azure Active Directory](active-directory-aadconnect.md).

<!---HONumber=AcomDC_0420_2016-->