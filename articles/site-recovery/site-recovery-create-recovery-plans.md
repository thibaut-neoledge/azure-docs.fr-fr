---
title: "Création de plans de récupération pour le basculement et la récupération dans Azure Site Recovery | Microsoft Docs"
description: "Explique comment créer et personnaliser des plans de récupération en vue de basculer et récupérer des machines virtuelles et des serveurs physiques dans Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: 72408c62-fcb6-4ee2-8ff5-cab1218773f2
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 02/14/2017
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: 9ab51cb8e11df43ba2157b11e25a1f29b19e4da9
ms.openlocfilehash: e36f19e9c429c0e4b42e96b28b1ba995bd1bf167
ms.lasthandoff: 02/15/2017


---
# <a name="create-recovery-plans"></a>Créer des plans de récupération


Cet article fournit des informations sur la création et la personnalisation des plans de récupération dans [Azure Site Recovery](site-recovery-overview.md).

Publiez des commentaires ou des questions au bas de cet article, ou sur le [Forum Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

 Les plans de récupération exécutent les actions suivantes :

* Ils définissent les groupes de machines qui basculent ensemble, puis démarrent ensemble.
* Ils modélisent les dépendances entre les machines, en les rassemblant au sein d’un groupe de plan de récupération. Par exemple, pour basculer et afficher une application spécifique, vous devez regrouper toutes les machines virtuelles pour cette application dans un même groupe de plan de récupération.
* Basculez. Vous pouvez exécuter un basculement test, planifié ou non planifié sur un plan de récupération.


## <a name="create-a-recovery-plan"></a>Créer un plan de récupération

1. Cliquez sur l’onglet **Plans de récupération** > **Créer un plan de récupération**.
   Spécifiez un nom pour le plan de récupération, puis définissez une source et une cible. Le basculement et la récupération doivent être activés sur les machines virtuelles de l’emplacement source.

    - Pour la réplication de VMM vers VMM, sélectionnez **Type de source** > **VMM**, puis les serveurs VMM source et cible. Cliquez sur **Hyper-V** pour afficher les clouds protégés.
    - Pour une réplication de VMM vers Azure, sélectionnez **Type de source** > **VMM**.  Sélectionnez le serveur VMM source et **Azure** en tant que cible.
    - Pour la réplication d’Hyper-V vers Azure (sans VMM), sélectionnez **Type de source** > **Site Hyper-V**. Sélectionnez le site en tant que source et **Azure** en tant que cible.
    - Pour une réplication de machine virtuelle VMware ou de serveur physique local vers Microsoft Azure, sélectionnez un serveur de configuration en tant que source et **Azure** en tant que cible.
2. Dans **Sélectionner les machines virtuelles**, sélectionnez les machines virtuelles (ou le groupe de réplication) que vous voulez ajouter au groupe par défaut (Groupe 1) dans le plan de récupération.

## <a name="customize-and-extend-recovery-plans"></a>Personnaliser et étendre les plans de récupération

Vous pouvez personnaliser et étendre les plans de récupération :

- **Ajouter de nouveaux groupes** : ajouter des groupes de plan de récupération supplémentaires (jusqu’à sept) au groupe par défaut, puis ajouter des ordinateurs ou groupes de réplication supplémentaires à ces groupes de plan de récupération. Les groupes sont numérotés dans l’ordre dans lequel vous les ajoutez. Vous ne pouvez inclure une machine virtuelle ou un groupe de réplication qu’au sein d’un seul plan de récupération.
- **Ajouter une action manuelle**: il est possible d’ajouter des actions manuelles qui s’exécutent avant ou après un groupe de plan de récupération. Lorsque le plan de récupération s’exécute, il s’arrête au point où vous avez inséré l’action manuelle. Une boîte de dialogue vous invite à spécifier que l’action manuelle est terminée.
- **Ajouter un script** : vous pouvez ajouter des scripts qui s’exécutent avant ou après un groupe de plan de récupération. Lorsque vous ajoutez un script, vous ajoutez un nouvel ensemble d’actions au groupe. Par exemple, un ensemble d’étapes préliminaires au sein du Groupe 1 est créé avec le nom : Groupe 1 : Étapes préliminaires. L’ensemble des étapes préliminaires seront répertoriées dans cet ensemble. Pour ajouter un script sur le site principal, vous devez disposer d’un serveur VMM déployé.
- **Ajouter des runbooks Azure** : vous pouvez étendre des plans de récupération avec des runbooks Azure. Par exemple, pour automatiser des tâches ou pour créer une récupération en une seule étape. [Plus d’informations](site-recovery-runbook-automation.md)

## <a name="add-scripts"></a>Ajouter des scripts

Vous pouvez utiliser des scripts PowerShell dans vos plans de récupération.

 - Veillez à utiliser des blocs try-catch dans vos scripts, ceci pour garantir le traitement approprié des exceptions.
    - Si le script comporte une exception, son exécution s’interrompt et la tâche est mise en échec.
    - Si une erreur se produit, aucune des portions restantes du script ne s’exécute.
    - Si une erreur se produit lorsque vous exécutez un basculement non planifié, le plan de récupération se poursuit.
    - Si une erreur se produit lorsque vous exécutez un basculement planifié, le plan de récupération s’arrête. Vous devez corriger le script, vérifier sa bonne exécution, puis exécuter de nouveau le plan de récupération.
- La commande Write-Host, qui ne fonctionne pas dans un script de plan de récupération, entraîne la mise en échec du script. Pour créer une sortie, générez un script de proxy exécutant votre script principal. Assurez-vous que l’ensemble des sorties sont extraites à l’aide de la commande >>.
  * Le script expire si aucune sortie n’est produite dans les 600 secondes.
  * Si des éléments sont écrits sur STDERR, le script est classé comme mis en échec. Ces informations s’affichent dans les détails d’exécution du script.

Si vous utilisez VMM dans votre déploiement :

* Les scripts d’un plan de récupération s’exécutent dans le contexte d’un compte de service VMM. Assurez-vous que ce compte dispose des autorisations en lecture pour le partage distant sur lequel se trouve le script. Testez l’exécution du script au niveau de privilège du compte de service VMM.
* Les applets de commande VMM sont fournies dans un module Windows PowerShell. Le module est installé lors du montage de la console VMM. Il peut être chargé dans votre script à l’aide de la commande de script suivante : 
   - Import-Module -Name virtualmachinemanager. [Plus d’informations](https://technet.microsoft.com/library/hh875013.aspx)
* Vérifiez que vous disposez d’au moins un serveur de bibliothèque au sein de votre déploiement VMM. Par défaut, le chemin d’accès de partage de bibliothèque d’un serveur VMM est disponible localement sur le serveur VMM, sous le nom de dossier MSCVMMLibrary.
    * Si votre chemin d’accès de partage de bibliothèque est distant (ou local mais non partagé avec MSCVMMLibrary), configurez le partage comme suit (en utilisant \\libserver2.contoso.com\share\ comme exemple) :
      * Ouvrez l’Éditeur du Registre, puis accédez à **HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\Azure Site Recovery\Registration**.
      * Remplacez la valeur de **ScriptLibraryPath** par \\libserver2.contoso.com\share\. Spécifiez le nom de domaine complet. Fournissez des autorisations d’accès à l’emplacement de partage.
      * Veillez à tester le script avec un compte d’utilisateur qui dispose des mêmes autorisations que le compte de service VMM. Cela permet de vérifier que les scripts testés autonomes s’exécuteront de la même manière dans les plans de récupération. Sur le serveur VMM, définissez le mode de contournement suivant de la stratégie d’exécution :
        * Ouvrez la console Windows PowerShell 64 bits à l’aide des privilèges élevés.
        * Entrez : **Set-executionpolicy bypass**. [Plus d’informations](https://technet.microsoft.com/library/ee176961.aspx)

## <a name="add-a-script-or-manual-action-to-a-plan"></a>Ajouter un script ou une action manuelle à un plan

Vous pouvez ajouter un script au groupe de plan de récupération par défaut après y avoir ajouté des machines virtuelles ou des groupes de réplication et après avoir créé le plan.

1. Ouvrez le plan de récupération.
2. Cliquez sur un élément de la liste **Étape,** puis cliquez sur **Script** ou sur **Action manuelle**.
3. Indiquez si vous souhaitez ajouter le script ou l’action avant ou après l’élément sélectionné. Utilisez les boutons de **Déplacement vers le haut** et de **Déplacement vers le bas** pour faire monter ou descendre le script.
4. Si vous ajoutez un script VMM, sélectionnez **Basculement vers script VMM**. Dans **Chemin d’accès du script**, tapez le chemin d’accès relatif au partage. Dans l’exemple VMM ci-dessous, spécifiez le chemin d’accès : **\RPScripts\RPScript.PS1**.
5. Si vous ajoutez un Runbook Azure Automation, spécifiez le compte Azure Automation dans lequel se trouve le runbook, puis sélectionnez le script runbook Azure approprié.
6. Exécutez un basculement du plan de récupération, afin de vous assurer du bon fonctionnement du script.


### <a name="vmm-script"></a>Script VMM

Si vous possédez un site source VMM, libre à vous de créer un script sur le serveur VMM, et de l’inclure dans votre plan de récupération.

1. Créez un dossier dans le partage de bibliothèque. Par exemple, \<VMMServerName>\MSSCVMMLibrary\RPScripts. Placez-le dans les serveurs VMM source et cibles.
2. Créez le script (par exemple RPScript), et vérifiez son bon fonctionnement.
3. Placez le script à l’emplacement \<VMMServerName>\MSSCVMMLibrary sur les serveurs VMM source et cible.


## <a name="next-steps"></a>Étapes suivantes

[En savoir plus](site-recovery-failover.md) sur l’exécution des basculements.

