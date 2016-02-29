<properties
   pageTitle="Planificateur Azure AD Connect Sync | Microsoft Azure"
   description="Cette rubrique décrit la fonctionnalité de planificateur intégré dans Azure AD Connect Sync."
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
   ms.date="02/16/2016"
   ms.author="andkjell"/>

# Planificateur Azure AD Connect Sync
Cette rubrique décrit le planificateur intégré dans Azure AD Connect Sync (également appelé moteur de synchronisation).

Cette fonctionnalité a été introduite avec la version 1.1.105.0 (publiée en février 2016).

## Vue d'ensemble
Azure AD Connect Sync synchronise les modifications dans votre répertoire local à l’aide d’un planificateur. Il existe deux processus de planificateur, l’un pour la synchronisation de mot de passe et l’autre pour la synchronisation d’attribut/d’objet, ainsi que des tâches de maintenance. Cette rubrique couvre le planificateur de synchronisation d’attribut/d’objet.

Dans les versions antérieures, le planificateur des objets et attributs était externe au moteur de synchronisation, et le planificateur de tâches de Windows ou un service Windows distinct était utilisé pour déclencher le processus de synchronisation. Le planificateur de la version 1.1 est intégré au moteur de synchronisation et permet de personnaliser certains aspects. La nouvelle valeur de la fréquence de synchronisation par défaut est de 30 minutes.

Le planificateur supervise deux tâches :

- **Cycle de synchronisation**. Processus d’importation, de synchronisation et d’exportation des modifications.
- **Tâches de maintenance**. Renouvelle les clés et les certificats pour la réinitialisation de mot de passe et le service DRS (Device Registration Service). Vide les anciennes entrées dans le journal des opérations.

Le planificateur est toujours en cours d’exécution, mais il peut être configuré pour exécuter uniquement une tâche, ou bien aucune tâche. Par exemple, si vous avez besoin d’avoir votre propre processus de cycle de synchronisation, vous pouvez désactiver cette tâche dans le planificateur, mais toujours exécuter la tâche de maintenance.

## Configuration du planificateur
Pour afficher vos paramètres de configuration en cours, accédez à PowerShell et exécutez `Get-ADSyncScheduler`. Le résultat suivant doit s’afficher :

![GetSyncScheduler](./media/active-directory-aadconnectsync-feature-scheduler/getsynccyclesettings.png)

- **AllowedSyncCycleInterval**. Fréquence maximale à laquelle Azure AD autorise les synchronisations. Vous ne pouvez pas synchroniser plus fréquemment tout en maintenant la prise en charge.
- **CurrentlyEffectiveSyncCycleInterval**. Planificateur actuellement en vigueur. La valeur sera identique à celle de CustomizedSyncInterval (si ce paramètre est défini) si la fréquence n’est pas supérieure à AllowedSyncInterval. Si vous modifiez CustomizedSyncCycleInterval, cela prendra effet après le prochain cycle de synchronisation.
- **CustomizedSyncCycleInterval**. Si vous souhaitez que le planificateur s’exécute à une fréquence autre que celle par défaut de 30 minutes, vous devez configurer ce paramètre. Dans l’image ci-dessus, le planificateur a été défini pour s’exécuter toutes les heures. Si vous choisissez une valeur inférieure à AllowedSyncInterval, ce dernier sera utilisé.
- **NextSyncCyclePolicyType**. Delta ou Initial. Indique si la prochaine exécution doit uniquement traiter les modifications delta du processus ou si elle doit effectuer une importation et une synchronisation intégrales, ce qui aura pour effet de retraiter les nouvelles règles ou les règles modifiées.
- **NextSyncCycleStartTimeInUTC**. Heure à laquelle le planificateur démarrera le prochain cycle de synchronisation.
- **PurgeRunHistoryInterval**. Durée pendant laquelle les journaux des opérations doivent être conservés. Ils peuvent être consultés dans Synchronization Service Manager. La valeur de conservation par défaut est de 7 jours.
- **SyncCycleEnabled**. Indique si le planificateur exécute les processus d’importation, de synchronisation et d’exportation dans le cadre de son fonctionnement.
- **MaintenanceEnabled**. Indique si le processus de maintenance est activé. Met à jour les certificats/clés et vide le journal des opérations.
- **IsStagingModeEnabled**. Indique si le [mode intermédiaire](active-directory-aadconnectsync-operations.md#staging-mode) est activé.

Vous pouvez modifier tous ces paramètres avec `Set-ADSyncScheduler` Le paramètre IsStagingModeEnabled peut uniquement être défini par l’Assistant d’installation.

## Démarrer le planificateur
Par défaut, le planificateur s’exécutera toutes les 30 minutes. Dans certains cas, vous souhaiterez peut-être exécuter un cycle de synchronisation entre les cycles planifiés ou vous devrez exécuter un autre type de synchronisation.

**Cycle de synchronisation delta ** Un cycle de synchronisation delta comprend les étapes suivantes :

- Importation delta sur tous les connecteurs
- Synchronisation delta sur tous les connecteurs
- Exportation sur tous les connecteurs

Cela peut indiquer une modification urgente à synchroniser immédiatement, nécessitant l’exécution manuelle d’un cycle. Si vous avez besoin d’exécuter un cycle manuellement, exécutez `Start-ADSyncSyncCycle -PolicyType Delta` à partir de PowerShell.

**Cycle de synchronisation complète** Si vous avez effectué l’un des changements de configuration suivants, vous devez exécuter un cycle de synchronisation complète (également appelé Initial) :

- Ajout de plusieurs objets ou attributs à importer à partir d’un répertoire source
- Modifications apportées aux règles de synchronisation
- Modification du [filtrage](active-directory-aadconnectsync-configure-filtering.md) pour qu’un nombre différent d’objets soit inclus

Si vous avez effectué l’une de ces modifications, vous devez exécuter un cycle de synchronisation complète, afin que le moteur de synchronisation ait la possibilité de reconsolider les espaces du connecteur. Un cycle de synchronisation complète comprend les étapes suivantes :

- Importation complète sur tous les connecteurs
- Synchronisation complète sur tous les connecteurs
- Exportation sur tous les connecteurs

Pour initier un cycle de synchronisation complète, exécutez `Start-ADSyncSyncCycle -PolicyType Initial` à partir d’une invite PowerShell. Ceci démarrera un cycle de synchronisation complète.

## Arrêter le planificateur
Si le planificateur exécute un cycle de synchronisation, vous devrez l’arrêter. Par exemple, si vous démarrez l’Assistant d’installation et que vous obtenez l’erreur suivante :

![SyncCycleRunningError](./media/active-directory-aadconnectsync-feature-scheduler/synccyclerunningerror.png)

Lorsqu’un cycle de synchronisation est en cours d’exécution, vous ne pouvez pas modifier la configuration. Vous pouvez attendre jusqu’à ce que le planificateur ait terminé le processus, mais vous pouvez également l’arrêter afin d’implémenter immédiatement vos modifications. L’arrêt du cycle en cours n’est pas dangereux et toutes les modifications non traitées le seront à la prochaine exécution.

1. Commencez par indiquer au planificateur d’arrêter son cycle en cours à l’aide de l’applet de commande PowerShell `Stop-ADSyncSyncCycle`.
2. L’arrêt du planificateur n’empêchera pas le connecteur actuel de terminer sa tâche en cours. Pour forcer l’arrêt du connecteur, effectuez les actions suivantes : ![StopAConnector](./media/active-directory-aadconnectsync-feature-scheduler/stopaconnector.png)
    - Démarrez le **Service de synchronisation** depuis le menu Démarrer. Accédez à **Connecteurs**, mettez en surbrillance le connecteur avec l’état **En cours d’exécution** et sélectionnez **Arrêter** dans les actions.

Le planificateur est toujours actif et redémarrera à la prochaine occasion.

## Planificateur et Assistant d’installation
Si vous démarrez l’Assistant d’installation, le planificateur sera temporairement interrompu. Nous supposons que vous allez apporter des modifications de configuration et celles-ci ne peuvent pas être appliquées si le moteur de synchronisation s’exécute activement. Pour cette raison, ne laissez pas l’Assistant d’installation ouvert, car il empêche le moteur de synchronisation d’effectuer toutes les actions de synchronisation.

## Étapes suivantes
En savoir plus sur la configuration de la [synchronisation Azure AD Connect](active-directory-aadconnectsync-whatis.md).

En savoir plus sur l’[intégration de vos identités locales avec Azure Active Directory](active-directory-aadconnect.md).

<!---HONumber=AcomDC_0218_2016-->