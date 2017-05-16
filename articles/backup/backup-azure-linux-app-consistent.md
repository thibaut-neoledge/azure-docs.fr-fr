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
translationtype: Human Translation
ms.sourcegitcommit: 2c33e75a7d2cb28f8dc6b314e663a530b7b7fdb4
ms.openlocfilehash: 4529037cb610e31028a35cf4643a2a99e90b2b8f
ms.lasthandoff: 04/21/2017


---
# <a name="application-consistent-backup-of-azure-linux-vms-preview"></a>Sauvegarde cohérente des applications des machines virtuelles Linux Azure (version préliminaire)

Cet article traite de la structure de pré-scripts et post-scripts de Linux, ainsi que de la façon dont elle peut être utilisée pour effectuer des sauvegardes cohérentes d’applications de machines virtuelles Linux Azure.

> [!Note]
> La structure de pré-scripts et post-scripts est prise en charge uniquement pour les machines virtuelles déployées par le Gestionnaire des ressources. Les scripts pour la cohérence d’application ne sont pas pris en charge pour les machines virtuelles déployées par Service Manager ou les machines virtuelles Windows.
>

## <a name="how-the-framework-works"></a>Fonctionnement de l’infrastructure

L’infrastructure fournit une option permettant d’exécuter des pré/post-scripts personnalisés lors de la capture instantanée de machine virtuelle. Le pré-script est exécuté avant la capture instantanée de machine virtuelle et le post-script est exécuté immédiatement après la capture instantanée de machine virtuelle. Cela permet aux utilisateurs de contrôler leur environnement et l’application lors de la sauvegarde de machine virtuelle.

Un scénario important pour cette infrastructure est de garantir la sauvegarde cohérente des applications de la machine virtuelle. Le pré-script peut appeler les API d’applications natives pour suspendre les e/s et le vidage du contenu en mémoire sur le disque. Cela permet de garantir que la capture instantanée est cohérente avec l’application, c’est-à-dire que l’application apparaîtra lorsque la machine virtuelle est démarrée suite à la restauration. Le post-script peut être utilisé pour libérer les e/s à l’aide des API d’applications natives, afin que l’application puisse reprendre son fonctionnement normal après la capture instantanée de la machine virtuelle.

## <a name="steps-to-configure-pre-script-and-post-script"></a>Procédure de configuration du pré-script et du post-script

1. Connectez-vous à la machine virtuelle Linux à sauvegarder en tant qu’utilisateur racine.

2. Téléchargez VMSnapshotScriptPluginConfig.json à partir de [github](https://github.com/MicrosoftAzureBackup/VMSnapshotPluginConfig) et copiez-le dans le dossier /etc/azure sur toutes les machines virtuelles à sauvegarder. Créez le répertoire /etc/azure s’il n’existe pas.

3. Copiez le pré-script et le post-script de votre application sur toutes les machines virtuelles à sauvegarder. Vous pouvez copier les scripts à n’importe quel emplacement de la machine virtuelle. Vous devez mettre à jour le chemin d’accès complet des fichiers de script dans le fichier VMSnapshotScriptPluginConfig.json.

4. Vérifiez les autorisations suivantes pour les fichiers :

   - VMSnapshotScriptPluginConfig.json - Autorisation « 600 », c.-à-d. seul l’utilisateur « racine » doit avoir des autorisations de « lecture » et d’« écriture » pour ce fichier ; aucun utilisateur ne doit disposer des autorisations d’« exécution ».
   - Fichier pré-script - Autorisation « 700 », c.-à-d. seul l’utilisateur « racine » doit avoir les autorisations de « lecture », « d’écriture » et « d’exécution » pour ce fichier.
   - Post-script - Autorisation « 700 », c.-à-d. seul l’utilisateur « racine » doit avoir les autorisations de « lecture », « d’écriture » et « d’exécution » pour ce fichier.

   > [!Note]
   > L’infrastructure fournit beaucoup de puissance aux utilisateurs, il est donc très important qu’elle soit entièrement sécurisée et que seul l’utilisateur « racine » puisse accéder aux fichiers de script et json critiques.
   > Dans le cas où les conditions ci-dessus ne seraient pas respectées, le script ne serait pas exécuté, ce qui crée une sauvegarde cohérente en cas d’incident/cohérente de système de fichiers.
   >

5. Configurez VMSnapshoScriptPluginConfig.json conformément aux informations ci-dessous.
    - **pluginName** : laissez ce champ tel quel, sinon vos scripts ne fonctionneront pas comme prévu.
    - **preScriptLocation** : fournissez le chemin d’accès complet du pré-script sur la machine virtuelle à sauvegarder.
    - **postScriptLocation** : fournissez le chemin d’accès complet du post-script sur la machine virtuelle à sauvegarder.
    - **preScriptParams** : n’importe quel paramètre facultatif devant être transmis au pré-script ; tous les paramètres doivent être entre guillemets et séparés par des virgules en présence de plusieurs paramètres.
    - **ppstScriptParams** : n’importe quel paramètre facultatif devant être transmis au post-script ; tous les paramètres doivent être entre guillemets et séparés par des virgules en présence de plusieurs paramètres.
    - **preScriptNoOfRetries** : nombre de fois où le pré-script doit être traité à nouveau en cas d’erreur avant de terminer. 0 signifie qu’une seule tentative a lieu et qu’aucune nouvelle tentative n’a lieu en cas d’échec.
    - **postScriptNoOfRetries** : nombre de fois où le post-script doit être traité à nouveau en cas d’erreur avant de terminer. 0 signifie qu’une seule tentative a lieu et qu’aucune nouvelle tentative n’a lieu en cas d’échec.
    - **timeoutInSeconds** : délais d’attente individuels pour le pré-script et le post-script.
    - **continueBackupOnFailure** : définissez cette valeur sur true si vous voulez que la sauvegarde Azure effectue une restauration vers une sauvegarde cohérente en cas d’incident/cohérente de système de fichiers en cas d’échec du pré-script ou du post-script. Définir cette valeur sur false fait échouer la sauvegarde en cas d’échec du script (sauf en cas de machine virtuelle à un seul disque où la restauration est effectuée vers une sauvegarde cohérente en cas d’incident, indépendamment de ce paramètre).
    - **fsFreezeEnabled** : spécifie si fsfreeze Linux doit être appelé pendant la capture instantanée de la machine virtuelle pour garantir la cohérence du système de fichiers. Nous vous recommandons de laisser cette valeur définie sur true, sauf si votre application comporte des dépendances sur la désactivation de fsfreeze.

6. L’infrastructure de script est maintenant configurée, si la sauvegarde de machine virtuelle est déjà configurée, la prochaine sauvegarde appellera les scripts et déclenchera une sauvegarde cohérente des applications. Si la sauvegarde de machine virtuelle n’est pas configurée, veuillez le faire à l’aide de [Sauvegarde de machines virtuelles Azure dans des coffres Recovery Services.](https://docs.microsoft.com/azure/backup/backup-azure-vms-first-look-arm)

## <a name="troubleshooting"></a>Résolution des problèmes

Veillez à ajouter un enregistrement approprié lors de l’écriture de votre pré-script et post-script et passez en revue vos journaux de script pour résoudre les problèmes de script. Si vous rencontrez toujours des problèmes pour exécuter des scripts, reportez-vous au tableau suivant pour plus d’informations.

| Erreur | Message d’erreur | Action recommandée |
| ------------------------ | -------------- | ------------------ |
| Pre-ScriptExecutionFailed |Le pré-script a renvoyé une erreur ; la sauvegarde peut ne pas être cohérente avec les applications.    | Examinez les journaux d’erreur de votre script pour résoudre le problème.|  
|    Post-ScriptExecutionFailed |    Le post-script a renvoyé une erreur qui peut affecter l’état de l’application. |    Examinez les journaux d’erreur de votre script pour résoudre le problème et vérifiez l’état de l’application. |
| Pre-ScriptNotFound |    Le pré-script est introuvable à l’emplacement spécifié dans le fichier de configuration VMSnapshotScriptPluginConfig.json. |    Assurez-vous que ce pré-script est présent au niveau du chemin d’accès spécifié dans le fichier de configuration pour garantir la sauvegarde cohérente des applications.|
| Post-ScriptNotFound |    Le post-script est introuvable à l’emplacement spécifié dans le fichier de configuration VMSnapshotScriptPluginConfig.json. |    Assurez-vous que ce post-script est présent au niveau du chemin d’accès spécifié dans le fichier de configuration pour garantir la sauvegarde cohérente des applications.|
| IncorrectPluginhostFile |    Le fichier Pluginhost fourni avec l’extension VmSnapshotLinux est endommagé. Le pré-script et le post-script ne peuvent donc pas être exécutés et la sauvegarde ne sera pas cohérente avec les applications.    | Veuillez désinstaller l’extension VmSnapshotLinux, elle sera automatiquement réinstallée avec la sauvegarde suivante pour résoudre le problème. |
| IncorrectJSONConfigFile | Le fichier VMSnapshotScriptPluginConfig.json est incorrect. Le pré-script et le post-script ne peuvent donc pas être exécutés et la sauvegarde ne sera pas cohérente avec les applications. | Veuillez télécharger la copie à partir de [github](https://github.com/MicrosoftAzureBackup/VMSnapshotPluginConfig) et la configurer à nouveau |
| InsufficientPermissionforPre-Script | Pour l’exécution de scripts, l’utilisateur racine doit être le propriétaire du fichier et le fichier doit avoir des autorisations « 700 », c.-à-d. que seul le propriétaire doit avoir des autorisations de « lecture », « d’écriture » et « d’exécution ». | Assurez-vous que l’utilisateur « racine » est le « propriétaire » du fichier de script et que seul le propriétaire dispose des autorisations de « lecture », « d’écriture » et « d’exécution ». |
| InsufficientPermissionforPost-Script | Pour l’exécution de scripts, l’utilisateur racine doit être le propriétaire du fichier et le fichier doit avoir des autorisations « 700 », c.-à-d. que seul le propriétaire doit avoir des autorisations de « lecture », « d’écriture » et « d’exécution ». | Assurez-vous que l’utilisateur « racine » est le « propriétaire » du fichier de script et que seul le propriétaire dispose des autorisations de « lecture », « d’écriture » et « d’exécution ». |
| Pre-ScriptTimeout | L’exécution du pré-script de sauvegarde cohérente des applications a expiré. | Vérifiez le script et augmentez le délai d’expiration dans le fichier VMSnapshotScriptPluginConfig.json situé à l’emplacement /etc/azure. |
| Post-ScriptTimeout | L’exécution du post-script de sauvegarde cohérente des applications a expiré. | Vérifiez le script et augmentez le délai d’expiration dans le fichier VMSnapshotScriptPluginConfig.json situé à l’emplacement /etc/azure. |

## <a name="next-steps"></a>Étapes suivantes
[Sauvegarder des machines virtuelles Azure dans un coffre Recovery Services](https://docs.microsoft.com/azure/backup/backup-azure-arm-vms)

