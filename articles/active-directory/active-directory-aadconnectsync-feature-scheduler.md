<properties
   pageTitle="Planificateur Azure AD Connect Sync | Microsoft Azure"
   description="Cette rubrique décrit la fonctionnalité de planificateur intégré dans Azure AD Connect Sync."
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
   ms.date="08/04/2016"
   ms.author="billmath"/>


# <a name="azure-ad-connect-sync:-scheduler"></a>Planificateur Azure AD Connect Sync
Cette rubrique décrit le planificateur intégré dans Azure AD Connect Sync (également appelé moteur de synchronisation).

Cette fonctionnalité a été introduite avec la version 1.1.105.0 (publiée en février 2016).

## <a name="overview"></a>Vue d'ensemble
Azure AD Connect Sync synchronise les modifications dans votre répertoire local à l’aide d’un planificateur. Il existe deux processus de planificateur, l’un pour la synchronisation de mot de passe et l’autre pour la synchronisation d’attribut/d’objet, ainsi que des tâches de maintenance. Cette rubrique couvre le planificateur de synchronisation d’attribut/d’objet.

Dans les versions antérieures, le planificateur des objets et attributs était externe au moteur de synchronisation, et le planificateur de tâches de Windows ou un service Windows distinct était utilisé pour déclencher le processus de synchronisation. Le planificateur de la version 1.1 est intégré au moteur de synchronisation et permet de personnaliser certains aspects. La nouvelle valeur de la fréquence de synchronisation par défaut est de 30 minutes.

Le planificateur supervise deux tâches :

- **Cycle de synchronisation**. Processus d’importation, de synchronisation et d’exportation des modifications.
- **Tâches de maintenance**. Renouvelle les clés et les certificats pour la réinitialisation de mot de passe et le service DRS (Device Registration Service). Vide les anciennes entrées dans le journal des opérations.

Le planificateur est toujours en cours d’exécution, mais il peut être configuré pour exécuter uniquement une tâche, ou bien aucune tâche. Par exemple, si vous avez besoin d’avoir votre propre processus de cycle de synchronisation, vous pouvez désactiver cette tâche dans le planificateur, mais toujours exécuter la tâche de maintenance.

## <a name="scheduler-configuration"></a>Configuration du planificateur
Pour afficher vos paramètres de configuration en cours, accédez à PowerShell et exécutez `Get-ADSyncScheduler`. Le résultat suivant doit s’afficher :

![GetSyncScheduler](./media/active-directory-aadconnectsync-feature-scheduler/getsynccyclesettings.png)

Si le message **La commande de synchronisation ou l’applet de commande n’est pas disponible** apparaît lorsque vous exécutez cette applet de commande, le module PowerShell n'est pas chargé. Cela peut se produire si vous exécutez Azure AD Connect sur un contrôleur de domaine ou sur un serveur avec des niveaux de restriction PowerShell plus élevés que les paramètres par défaut. Si vous recevez cette erreur, exécutez `Import-Module ADSync` pour libérer l’applet de commande.

- **AllowedSyncCycleInterval**. Fréquence maximale à laquelle Azure AD autorise les synchronisations. Vous ne pouvez pas synchroniser plus fréquemment tout en maintenant la prise en charge.
- **CurrentlyEffectiveSyncCycleInterval**. Planificateur actuellement en vigueur. La valeur sera identique à celle de CustomizedSyncInterval (si ce paramètre est défini) si la fréquence n’est pas supérieure à AllowedSyncInterval. Si vous modifiez CustomizedSyncCycleInterval, cela prendra effet après le prochain cycle de synchronisation.
- **CustomizedSyncCycleInterval**. Si vous souhaitez que le planificateur s’exécute à une fréquence autre que celle par défaut de 30 minutes, vous devez configurer ce paramètre. Dans l’image ci-dessus, le planificateur a été défini pour s’exécuter toutes les heures. Si vous choisissez une valeur inférieure à AllowedSyncInterval, ce dernier sera utilisé.
- **NextSyncCyclePolicyType**. Delta ou Initial. Indique si la prochaine exécution doit uniquement traiter les modifications delta du processus ou si elle doit effectuer une importation et une synchronisation intégrales, ce qui aura pour effet de retraiter les nouvelles règles ou les règles modifiées.
- **NextSyncCycleStartTimeInUTC**. Heure à laquelle le planificateur démarrera le prochain cycle de synchronisation.
- **PurgeRunHistoryInterval**. Durée pendant laquelle les journaux des opérations doivent être conservés. Ils peuvent être consultés dans Synchronization Service Manager. La valeur de conservation par défaut est de 7 jours.
- **SyncCycleEnabled**. Indique si le planificateur exécute les processus d’importation, de synchronisation et d’exportation dans le cadre de son fonctionnement.
- **MaintenanceEnabled**. Indique si le processus de maintenance est activé. Met à jour les certificats/clés et vide le journal des opérations.
- **IsStagingModeEnabled**. Indique si le [mode intermédiaire](active-directory-aadconnectsync-operations.md#staging-mode) est activé.

Vous pouvez modifier certains de ces paramètres avec `Set-ADSyncScheduler`. Les paramètres suivants peuvent être modifiés :

- CustomizedSyncCycleInterval
- NextSyncCyclePolicyType
- PurgeRunHistoryInterval
- SyncCycleEnabled
- MaintenanceEnabled

La configuration du planificateur est stockée dans Azure AD. Si vous avez un serveur intermédiaire, toute modification sur le serveur principal aura également un effet sur le serveur intermédiaire (à l'exception de IsStagingModeEnabled).

### <a name="customizedsynccycleinterval"></a>CustomizedSyncCycleInterval
Syntaxe : `Set-ADSyncScheduler -CustomizedSyncCycleInterval d.HH:mm:ss`  
 d - jours, HH - heures, mm - minutes, ss - secondes

Exemple : `Set-ADSyncScheduler -CustomizedSyncCycleInterval 03:00:00`  
 modifiera le Planificateur pour qu’il s’exécute toutes les 3 heures.

Exemple : `Set-ADSyncScheduler -CustomizedSyncCycleInterval 1.0:0:0`  
 modifiera le Planificateur pour qu’il s’exécute tous les jours.

## <a name="start-the-scheduler"></a>Démarrer le planificateur
Par défaut, le planificateur s’exécutera toutes les 30 minutes. Dans certains cas, vous souhaiterez peut-être exécuter un cycle de synchronisation entre les cycles planifiés ou vous devrez exécuter un autre type de synchronisation.

**Cycle de synchronisation delta**  
 Un cycle de synchronisation delta comprend les étapes suivantes :

- Importation delta sur tous les connecteurs
- Synchronisation delta sur tous les connecteurs
- Exportation sur tous les connecteurs

Cela peut indiquer une modification urgente à synchroniser immédiatement, nécessitant l’exécution manuelle d’un cycle. Si vous avez besoin d’exécuter un cycle manuellement, exécutez `Start-ADSyncSyncCycle -PolicyType Delta`à partir de PowerShell.

**Cycle de synchronisation complet**  
Si vous avez effectué l’une des modifications de configuration suivantes, vous devez exécuter un cycle de synchronisation complet (également appelé Initial) :

- Ajout de plusieurs objets ou attributs à importer à partir d’un répertoire source
- Modifications apportées aux règles de synchronisation
- Le [filtrage](active-directory-aadconnectsync-configure-filtering.md) étant modifié, un nombre différent d’objets doit être inclus

Si vous avez effectué l’une de ces modifications, vous devez exécuter un cycle de synchronisation complète, afin que le moteur de synchronisation ait la possibilité de reconsolider les espaces du connecteur. Un cycle de synchronisation complète comprend les étapes suivantes :

- Importation complète sur tous les connecteurs
- Synchronisation complète sur tous les connecteurs
- Exportation sur tous les connecteurs

Pour initier un cycle de synchronisation complète, exécutez `Start-ADSyncSyncCycle -PolicyType Initial` à partir d’une invite PowerShell. Ceci démarrera un cycle de synchronisation complète.

## <a name="stop-the-scheduler"></a>Arrêter le planificateur
Si le planificateur exécute un cycle de synchronisation, vous devrez l’arrêter. Par exemple, si vous démarrez l’Assistant d’installation et que vous obtenez l’erreur suivante :

![SyncCycleRunningError](./media/active-directory-aadconnectsync-feature-scheduler/synccyclerunningerror.png)

Lorsqu’un cycle de synchronisation est en cours d’exécution, vous ne pouvez pas modifier la configuration. Vous pouvez attendre jusqu’à ce que le planificateur ait terminé le processus, mais vous pouvez également l’arrêter afin d’implémenter immédiatement vos modifications. L’arrêt du cycle en cours n’est pas dangereux et toutes les modifications non traitées le seront à la prochaine exécution.

1. Commencez par indiquer au planificateur d’arrêter son cycle en cours à l’aide de l’applet de commande PowerShell `Stop-ADSyncSyncCycle`.
2. L’arrêt du planificateur n’empêchera pas le connecteur actuel de terminer sa tâche en cours. Pour forcer le connecteur à s’arrêter, prenez les mesures suivantes : ![StopAConnector](./media/active-directory-aadconnectsync-feature-scheduler/stopaconnector.png)
    - Démarrez le **Service de synchronisation** depuis le menu Démarrer. Accédez à **Connecteurs**, mettez en surbrillance le connecteur avec l’état **En cours d’exécution** et sélectionnez **Arrêter** dans les actions.

Le planificateur est toujours actif et redémarrera à la prochaine occasion.

## <a name="custom-scheduler"></a>Planificateur personnalisé
Les applets de commande décrites dans cette section sont uniquement disponibles dans le build [1.1.130.0](active-directory-aadconnect-version-history.md#111300) et versions ultérieures.

Si le planificateur intégré ne satisfait pas à vos exigences, vous pouvez planifier les connecteurs à l'aide de PowerShell.

### <a name="invoke-adsyncrunprofile"></a>Invoke-ADSyncRunProfile
Vous pouvez démarrer un profil pour un connecteur de cette façon :

```
Invoke-ADSyncRunProfile -ConnectorName "name of connector" -RunProfileName "name of profile"
```

Les noms à utiliser pour [Noms de connecteur](active-directory-aadconnectsync-service-manager-ui-connectors.md) et [Exécuter les noms de profil](active-directory-aadconnectsync-service-manager-ui-connectors.md#configure-run-profiles) se trouvent dans [l’interface utilisateur Synchronization Service Manager](active-directory-aadconnectsync-service-manager-ui.md).

![Appeler le profil d’exécution](./media/active-directory-aadconnectsync-feature-scheduler/invokerunprofile.png)  

L’Applet de commande `Invoke-ADSyncRunProfile` est synchrone, c'est-à-dire qu'il ne retournera pas de contrôle avant que le connecteur ait terminé l'opération, avec succès ou avec une erreur.

Lorsque vous planifiez vos connecteurs, il est recommandé de le faire dans l'ordre suivant :

1. (Intégral/Delta) Importation à partir de répertoires locaux, tels qu'Active Directory
2. (Intégral/Delta) Importation à partir d'Azure AD
3. (Intégral/Delta) Synchronisation à partir de répertoires locaux, tels qu'Active Directory
4. (Intégral/Delta) Synchronisation à partir d'Azure AD
5. Exportation vers Azure AD
6. Exportation vers des répertoires locaux, tels qu'Active Directory

Si vous regardez le planificateur intégré, il s'agit de l'ordre d’exécution des connecteurs.

### <a name="get-adsyncconnectorrunstatus"></a>Get-ADSyncConnectorRunStatus
Vous pouvez également surveiller le moteur de synchronisation pour voir s'il est occupé ou inactif. Cet Applet de commande renvoie un résultat vide si le moteur de synchronisation est inactif et qu'il n'exécute pas un connecteur. Si un connecteur est en cours d'exécution, il retournera le nom du connecteur.

```
Get-ADSyncConnectorRunStatus
```

![État d’exécution du connecteur](./media/active-directory-aadconnectsync-feature-scheduler/getconnectorrunstatus.png)  
 Dans l'illustration ci-dessus, la première ligne est dans un état où le moteur de synchronisation est inactif. La deuxième ligne lorsque le Connecteur Azure AD est en cours d'exécution.

## <a name="scheduler-and-installation-wizard"></a>Planificateur et Assistant d’installation
Si vous démarrez l’Assistant d’installation, le planificateur sera temporairement interrompu. Nous supposons que vous allez apporter des modifications de configuration et celles-ci ne peuvent pas être appliquées si le moteur de synchronisation s’exécute activement. Pour cette raison, ne laissez pas l’Assistant d’installation ouvert, car il empêche le moteur de synchronisation d’effectuer toutes les actions de synchronisation.

## <a name="next-steps"></a>Étapes suivantes
En savoir plus sur la configuration de la [synchronisation Azure AD Connect](active-directory-aadconnectsync-whatis.md) .

En savoir plus sur l’ [intégration de vos identités locales avec Azure Active Directory](active-directory-aadconnect.md).



<!--HONumber=Oct16_HO2-->


