---
title: "Résoudre les problèmes d’un serveur de sauvegarde Azure | Microsoft Docs"
description: "Résolvez les problèmes d’installation et d’enregistrement du serveur de sauvegarde Azure, mais aussi de sauvegarde et de restauration des charges de travail applicatives."
services: backup
documentationcenter: 
author: pvrk
manager: shreeshd
editor: 
ms.assetid: 2d73c349-0fc8-4ca8-afd8-8c9029cb8524
ms.service: backup
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 3/24/2017
ms.author: pullabhk;markgal;
translationtype: Human Translation
ms.sourcegitcommit: 356de369ec5409e8e6e51a286a20af70a9420193
ms.openlocfilehash: a42488d618c58b36fa8105c1b22fd32ca615d1b1
ms.lasthandoff: 03/27/2017


---

# <a name="troubleshoot-azure-backup-server"></a>Résoudre les problèmes d’un serveur de sauvegarde Azure

Vous pouvez résoudre les erreurs rencontrées pendant l’utilisation d’un serveur de sauvegarde Azure à l’aide des informations figurant dans la table suivante.

>
>

## <a name="registration-and-agent-related-issues"></a>Problèmes liés à l’enregistrement et aux agents
| Opération | Détails de l’erreur | Solution de contournement |
| --- | --- | --- |
| Enregistrement dans un coffre | Informations d’identification du coffre fournies non valides. Cela signifie que le fichier est endommagé ou qu’il ne contient pas les dernières informations d’identification associées au service de récupération. | Pour résoudre ce problème : <ol><li> Téléchargez le dernier fichier d’informations d’identification du coffre et réessayez. <br>(OU)</li> <li> Si l’action ci-dessus n’a pas fonctionné, essayez de télécharger les informations d’identification dans un répertoire local différent, ou créez un autre coffre. <br>(OU)</li> <li> Mettez à jour les paramètres de date et l’heure, comme indiqué dans [ce blog](https://azure.microsoft.com/blog/troubleshooting-common-configuration-issues-with-azure-backup/). <br>(OU)</li> <li> Vérifiez si le répertoire c:\windows\temp possède plus de 65 000 fichiers. Déplacez les fichiers obsolètes vers un autre emplacement, ou supprimez les éléments figurant dans le dossier Temp. <br>(OU)</li> <li> Vérifiez l’état des certificats. <br> a. Sélectionnez Gérer les certificats d’ordinateur (dans le Panneau de configuration). <br> b. Développez le nœud Personnel et son nœud enfant appelé Certificats. <br> c.  Supprimez le certificat « Outils Windows Azure ». <br> d. Recommencez l’enregistrement dans le client de sauvegarde Azure. <br> (OU) </li> <li> Vérifiez si une stratégie de groupe est appliquée. </li></ol> |
| Envoi d’un ou de plusieurs agents à des serveurs protégés via une transmission de type push | L’opération de l’agent a échoué en raison d’une erreur de communication avec le service Coordinateur d’agents DPM sur \<nom_serveur > | **Si l’action recommandée dans le produit ne fonctionne pas**, <ol><li> ou si vous joignez un ordinateur à partir d’un domaine non approuvé, suivez [ces étapes](https://technet.microsoft.com/library/hh757801(v=sc.12).aspx). <br> (OU) </li><li> Si vous joignez un ordinateur à partir d’un domaine approuvé, résolvez les problèmes à l’aide de la procédure décrite dans [ce blog](https://blogs.technet.microsoft.com/dpm/2012/02/06/data-protection-manager-agent-network-troubleshooting/). <br>(OU)</li><li> Essayez de désactiver l’antivirus. Si cela résout le problème, modifiez les paramètres de l’antivirus comme suggéré dans [cet article] (https://technet.microsoft.com/library/hh757911.aspx).</li></ol> |
| Envoi d’un ou de plusieurs agents à des serveurs protégés via une transmission de type push | Les informations d’identification spécifiées pour le serveur ne sont pas valides. | **Si l’action recommandée dans le produit ne fonctionne pas**, <br> installez manuellement l’agent de protection sur le serveur de production, comme indiqué dans [cet article](https://technet.microsoft.com/library/hh758186(v=sc.12).aspx#BKMK_Manual).|


## <a name="configuring-protection-group"></a>Configuration du groupe de protection
| Opération | Détails de l’erreur | Solution de contournement |
| --- | --- | --- |
| Configuration des groupes de protection | DPM n’a pas pu énumérer le composant d’application sur l’ordinateur protégé (nom de l’ordinateur protégé). | Cliquez sur l’option Actualiser figurant sur l’écran de configuration du groupe de protection, au niveau du composant/de la source de données pertinents. |
| Configuration des groupes de protection | Configuration de la protection impossible. | Si le serveur protégé est de type SQL Server, vérifiez si les autorisations du rôle sysadmin ont été fournies au compte système (NTAuthority\System) sur l’ordinateur protégé, comme indiqué dans [cet article](https://technet.microsoft.com/library/hh757977(v=sc.12).aspx).
| Configuration des groupes de protection | Le pool de stockage présente un espace disponible insuffisant pour ce groupe de protection. | Les disques ajoutés au pool de stockage [ne doivent pas contenir de partitions](https://technet.microsoft.com/library/hh758075(v=sc.12).aspx). Supprimez les volumes existants sur les disques, puis ajoutez-les à nouveau au pool de stockage.|

## <a name="backup"></a>Sauvegarde
| Opération | Détails de l’erreur | Solution de contournement |
| --- | --- | --- |
| Sauvegarde | Le réplica est incohérent. | Pour en savoir plus sur les causes d’incohérence du réplica et consulter des suggestions pertinentes, cliquez [ici](https://technet.microsoft.com/library/cc161593.aspx). <br> <ol><li> En cas de sauvegarde de l’état système/sauvegarde à des fins de récupération complète, vérifiez si la sauvegarde de Windows Server est installée sur le serveur protégé. </li><li> Recherchez d’éventuels problèmes liés au manque d’espace dans le pool de stockage DPM sur le serveur DPM/de sauvegarde Azure, et allouez le stockage selon les besoins. </li><li> Vérifiez l’état du service Volume Shadow Copy sur le serveur protégé. S’il est à l’état désactivé, définissez-le de sorte qu’il démarre manuellement et lancez le service sur le serveur. Ensuite, revenez à la console DPM/du serveur de sauvegarde Azure et démarrez la tâche de synchronisation avec vérification de la cohérence.</li></ol>|
| Sauvegarde | Une erreur inattendue s’est produite pendant l’exécution de la tâche. Le périphérique n’est pas prêt. | **Si l’action recommandée dans le produit ne fonctionne pas**, <br> <ol><li>définissez l’espace de stockage des clichés instantanés sur Illimité pour les éléments du groupe de protection et exécutez la vérification de la cohérence. <br></li> (OU) <li>Supprimez le groupe de protection existant et créez-en plusieurs, l’un d’eux incluant chaque élément.</li></ol> |
| Sauvegarde | Si vous sauvegardez uniquement l’état du système, vérifiez que l’ordinateur présente un espace disponible suffisant pour stocker cette sauvegarde. | <ol><li>Vérifiez que la sauvegarde Windows Server est installée sur l’ordinateur protégé.</li><li>Assurez-vous que l’ordinateur protégé présente un espace disponible suffisant pour l’état du système. Pour ce faire, la méthode la plus simple consiste à accéder à l’ordinateur protégé, à ouvrir la sauvegarde Windows Server, à cliquer sur les sélections et à sélectionner la récupération complète. L’interface utilisateur vous indique l’espace requis par cette opération. Ouvrez la sauvegarde Windows Server, puis cliquez sur Sauvegarde locale -> Planification de la sauvegarde -> Sélectionner la configuration de sauvegarde -> Serveur complet (la taille est affichée). Utilisez cette taille à des fins de vérification.</li></ol>
| Sauvegarde | Échec de la création de points de récupération en ligne. | Si le message d’erreur indique que l’agent de sauvegarde Azure n’a pas pu créer d’instantané du volume sélectionné, augmentez la taille de l’espace dans le volume du point de récupération et le réplica.
| Sauvegarde | Échec de la création de points de récupération en ligne. | Si le message d’erreur indique que la phrase secrète de chiffrement de ce serveur n’est pas définie et vous invite à la configurer, définissez une phrase secrète de chiffrement. En cas d’échec, <br> <ol><li>vérifiez si l’emplacement temporaire existe ou non. L’emplacement mentionné dans la clé de Registre HKEY_LOCAL_MACHINE\Software\Microsoft\Windows Azure Backup\Config qui porte le nom « ScratchLocation » doit exister.</li><li> Si l’emplacement temporaire existe, enregistrez à nouveau l’ancienne phrase secrète. **Chaque fois que vous configurez une phrase secrète de chiffrement, n’oubliez pas de l’enregistrer à un emplacement sécurisé.**</li><ol>
| Sauvegarde | Échec de la sauvegarde à des fins de récupération complète | Si la taille de la récupération complète est énorme, recommencez l’opération après avoir déplacé certains fichiers vers le lecteur du système d’exploitation. |
| Sauvegarde | Erreur lors de l’accès aux dossiers/fichiers partagés. | Modifiez les paramètres antivirus comme suggéré [ici](https://technet.microsoft.com/library/hh757911.aspx).|

