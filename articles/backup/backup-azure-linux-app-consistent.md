---
title: "Sauvegarde Azure : sauvegarde cohérente des applications des machines virtuelles Linux | Microsoft Docs"
description: "Utilisez des scripts pour garantir des sauvegardes cohérentes des applications sur Azure pour vos machines virtuelles Linux. Les scripts s’appliquent uniquement aux machines virtuelles Linux dans un déploiement de Gestionnaire de ressources. Les scripts ne s’appliquent pas aux machines virtuelles Windows ou aux déploiements de Service Manager. Cet article passe en revue les étapes de configuration des scripts, y compris la résolution des problèmes."
services: backup
documentationcenter: dev-center-name
author: anuragmehrotra
manager: shivamg
keywords: "sauvegarde cohérente des applications ; sauvegarde cohérente des applications de la machine virtuelle Azure ; sauvegarde de la machine virtuelle Linux ; sauvegarde Azure"
ms.assetid: bbb99cf2-d8c7-4b3d-8b29-eadc0fed3bef
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 4/12/2017
ms.author: anuragm;markgal
ms.openlocfilehash: 378c65bec8fd1f880ed459e76f5e4b5d85e49d2a
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2017
---
# <a name="application-consistent-backup-of-azure-linux-vms-preview"></a>Sauvegarde cohérente des applications des machines virtuelles Linux Azure (version préliminaire)

Cet article traite de la structure de pré-scripts et post-scripts de Linux, ainsi que de la façon dont elle peut être utilisée pour effectuer des sauvegardes cohérentes d’applications de machines virtuelles Linux Azure.

> [!Note]
> La structure de pré-scripts et post-scripts est prise en charge uniquement pour les machines virtuelles déployées par Azure Resource Manager. Les scripts pour la cohérence d’application ne sont pas pris en charge pour les machines virtuelles déployées par Service Manager ou les machines virtuelles Windows.
>

## <a name="how-the-framework-works"></a>Fonctionnement de l’infrastructure

L’infrastructure fournit une option permettant d’exécuter des pré/post-scripts personnalisés lors de la capture d’instantanés de machines virtuelles. Les pré-scripts sont exécutés juste avant la capture d’instantané de machine virtuelle, et les post-scripts juste après la capture. Cela vous permet de contrôler votre environnement et l’application lorsque vous prenez des instantanés de machines virtuelles.

Dans ce scénario, il est important d’assurer la sauvegarde cohérente des applications de la machine virtuelle. Le pré-script peut appeler les API natives de l’application pour suspendre les E/S et vider le contenu de la mémoire sur le disque. Cela garantit que l’instantané est cohérent avec l’application (autrement dit, que l’application est lancée lorsque la machine virtuelle est démarrée après la restauration). Le post-script permet de libérer les E/S. Il effectue cela à l’aide des API d’applications natives, afin que l’application puisse reprendre son fonctionnement normal après la capture instantanée de la machine virtuelle.

## <a name="steps-to-configure-pre-script-and-post-script"></a>Procédure de configuration du pré-script et du post-script

1. Connectez-vous en tant qu’utilisateur root de la machine virtuelle Linux que vous souhaitez sauvegarder.

2. Téléchargez **VMSnapshotScriptPluginConfig.json** à partir de [GitHub](https://github.com/MicrosoftAzureBackup/VMSnapshotPluginConfig) et copiez-le dans le dossier **/etc/azure** sur toutes les machines virtuelles que vous prévoyez de sauvegarder. Créez le répertoire **/etc/azure** s’il n’existe pas.

3. Copiez le pré-script et le post-script de votre application sur toutes les machines virtuelles que vous prévoyez de sauvegarder. Vous pouvez copier les scripts vers n’importe quel emplacement sur la machine virtuelle. Veillez à mettre à jour le chemin d’accès complet des fichiers de script dans le fichier **VMSnapshotScriptPluginConfig.json**.

4. Vérifiez les autorisations suivantes pour ces fichiers :

   - **VMSnapshotScriptPluginConfig.json** : autorisation « 600 ». Par exemple, seul l’utilisateur « racine » doit avoir des autorisations de « lecture » et « d’écriture » pour ce fichier, et aucun utilisateur ne doit disposer des autorisations « d’exécution ».

   - **Fichier de pré-script** : autorisation « 700 ».  Par exemple, seul l’utilisateur « racine » doit avoir les autorisations de « lecture », « d’écriture » et « d’exécution » pour ce fichier.
  
   - **Post-script** : autorisation « 700 ». Par exemple, seul l’utilisateur « racine » doit avoir les autorisations de « lecture », « d’écriture » et « d’exécution » pour ce fichier.

   > [!Important]
   > L’infrastructure offre beaucoup de puissance aux utilisateurs. Il est donc important qu’il soit sécurisé et que seul l’utilisateur « root » ait accès aux fichiers critiques de JSON et de script.
   > Si les conditions précédentes ne sont pas remplies, le script ne s’exécute pas. Cela entraîne une sauvegarde du système de fichiers/point cohérent d’incident.
   >

5. Configurez **VMSnapshoScriptPluginConfig.json** comme décrit ici :
    - **pluginName** : laissez ce champ tel quel, sans quoi vos scripts ne fonctionneront pas comme prévu.

    - **preScriptLocation** : fournissez le chemin d’accès complet du pré-script sur la machine virtuelle qui sera sauvegardée.

    - **postScriptLocation** : fournissez le chemin d’accès complet du post-script sur la machine virtuelle qui sera sauvegardée.

    - **preScriptParams** : fournissez les paramètres facultatifs qui doivent être transmis au pré-script. Tous les paramètres doivent être entre guillemets et être séparés par des virgules s’il existe plusieurs paramètres.

    - **postScriptParams** : fournissez les paramètres facultatifs qui doivent être transmis au post-script. Tous les paramètres doivent être entre guillemets et être séparés par des virgules s’il existe plusieurs paramètres.

    - **preScriptNoOfRetries** : définissez le nombre de fois où le pré-script doit être traité à nouveau en cas d’erreur avant de terminer. Zéro signifie qu’une seule tentative a lieu et qu’aucune nouvelle tentative n’a lieu en cas d’échec.

    - **postScriptNoOfRetries** : définissez le nombre de fois où le post-script doit être traité à nouveau en cas d’erreur avant de terminer. Zéro signifie qu’une seule tentative a lieu et qu’aucune nouvelle tentative n’a lieu en cas d’échec.
    
    - **timeoutInSeconds** : spécifiez des délais d’attente individuels pour le pré-script et le post-script.

    - **continueBackupOnFailure** : définissez cette valeur sur **true** si vous voulez que la sauvegarde Azure effectue une restauration vers une sauvegarde cohérente en cas d’incident/cohérente de système de fichiers en cas d’échec du pré-script ou du post-script. Définir cette valeur sur **false** fait échouer la sauvegarde en cas d’échec du script (sauf en cas de machine virtuelle à un seul disque où la restauration est effectuée vers une sauvegarde cohérente en cas d’incident, indépendamment de ce paramètre).

    - **fsFreezeEnabled** : spécifiez si fsfreeze Linux doit être appelé pendant la capture instantanée de la machine virtuelle pour garantir la cohérence du système de fichiers. Nous vous recommandons de laisser cette valeur définie sur **true**, sauf si votre application comporte des dépendances sur la désactivation de fsfreeze.

6. L’infrastructure de script est désormais configurée. Si la sauvegarde de la machine virtuelle est déjà configurée, la sauvegarde suivante appelle les scripts et déclenche la sauvegarde cohérente avec les applications. Si la sauvegarde de machine virtuelle n’est pas configurée, faites-le à l’aide de [Sauvegarde de machines virtuelles Azure dans des coffres Recovery Services.](https://docs.microsoft.com/azure/backup/backup-azure-vms-first-look-arm)

## <a name="troubleshooting"></a>Résolution de problèmes

Veillez à ajouter un enregistrement approprié lors de l’écriture de votre pré-script et post-script et passez en revue vos journaux de script pour résoudre les problèmes de script. Si vous rencontrez toujours des problèmes pour exécuter des scripts, reportez-vous au tableau suivant pour plus d’informations.

| Erreur | Message d’erreur | Action recommandée |
| ------------------------ | -------------- | ------------------ |
| Pre-ScriptExecutionFailed |Le pré-script a renvoyé une erreur ; la sauvegarde peut ne pas être cohérente avec les applications.   | Examinez les journaux d’erreur de votre script pour résoudre le problème.|  
|   Post-ScriptExecutionFailed |    Le post-script a renvoyé une erreur qui peut affecter l’état de l’application. |    Examinez les journaux d’erreur de votre script pour résoudre le problème et vérifiez l’état de l’application. |
| Pre-ScriptNotFound |  Le pré-script est introuvable à l’emplacement spécifié dans le fichier de configuration **VMSnapshotScriptPluginConfig.json**. |   Assurez-vous que ce pré-script est présent au niveau du chemin d’accès spécifié dans le fichier de configuration pour garantir la sauvegarde cohérente des applications.|
| Post-ScriptNotFound | Le post-script est introuvable à l’emplacement spécifié dans le fichier de configuration **VMSnapshotScriptPluginConfig.json**. |   Assurez-vous que ce post-script est présent au niveau du chemin d’accès spécifié dans le fichier de configuration pour garantir la sauvegarde cohérente des applications.|
| IncorrectPluginhostFile | Le fichier **Pluginhost** fourni avec l’extension VmSnapshotLinux est endommagé. Le pré-script et le post-script ne peuvent donc pas être exécutés et la sauvegarde ne sera pas cohérente avec les applications. | Désinstallez l’extension **VmSnapshotLinux**, et elle sera automatiquement réinstallée avec la sauvegarde suivante pour résoudre le problème. |
| IncorrectJSONConfigFile | Le fichier **VMSnapshotScriptPluginConfig.json** est incorrect. Le pré-script et le post-script ne peuvent donc pas être exécutés et la sauvegarde ne sera pas cohérente avec les applications. | Téléchargez la copie à partir de [GitHub](https://github.com/MicrosoftAzureBackup/VMSnapshotPluginConfig) et la configurer à nouveau. |
| InsufficientPermissionforPre-Script | Pour l’exécution de scripts, l’utilisateur racine doit être le propriétaire du fichier et le fichier doit avoir des autorisations « 700 », (seul le propriétaire doit avoir des autorisations de « lecture », « d’écriture » et « d’exécution »). | Assurez-vous que l’utilisateur « racine » est le « propriétaire » du fichier de script et que seul le propriétaire dispose des autorisations de « lecture », « d’écriture » et « d’exécution ». |
| InsufficientPermissionforPost-Script | Pour l’exécution de scripts, l’utilisateur racine doit être le propriétaire du fichier et le fichier doit avoir des autorisations « 700 » (c.-à-d. que seul le propriétaire doit avoir des autorisations de « lecture », « d’écriture » et « d’exécution »). | Assurez-vous que l’utilisateur « racine » est le « propriétaire » du fichier de script et que seul le propriétaire dispose des autorisations de « lecture », « d’écriture » et « d’exécution ». |
| Pre-ScriptTimeout | L’exécution du pré-script de sauvegarde cohérente des applications a expiré. | Vérifiez le script et augmentez le délai d’expiration dans le fichier **VMSnapshotScriptPluginConfig.json** situé à l’emplacement **/etc/azure**. |
| Post-ScriptTimeout | L’exécution du post-script de sauvegarde cohérente des applications a expiré. | Vérifiez le script et augmentez le délai d’expiration dans le fichier **VMSnapshotScriptPluginConfig.json** situé à l’emplacement **/etc/azure**. |

## <a name="next-steps"></a>Étapes suivantes
[Sauvegarder des machines virtuelles Azure dans un coffre Recovery Services](https://docs.microsoft.com/azure/backup/backup-azure-arm-vms)
