<properties
    pageTitle="Créer des plans de récupération | Microsoft Azure" 
    description="Créez des plans de récupération à l’aide d’Azure Site Recovery pour basculer et récupérer des groupes de machines virtuelles et de serveurs physiques." 
    services="site-recovery" 
    documentationCenter="" 
    authors="rayne-wiselman" 
    manager="jwhit" 
    editor=""/>

<tags 
    ms.service="site-recovery" 
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="storage-backup-recovery" 
    ms.date="10/05/2016"
    ms.author="raynew"/>


# <a name="create-recovery-plans"></a>Créer des plans de récupération

Le service Azure Site Recovery contribue à mettre en œuvre la stratégie de continuité des activités et de récupération d’urgence de votre entreprise en coordonnant la réplication, le basculement et la récupération de machines virtuelles et de serveurs physiques. Les machines peuvent être répliquées vers Azure ou vers un centre de données local secondaire. Pour obtenir un aperçu rapide, consultez [Qu’est-ce qu’Azure Site Recovery ?](site-recovery-overview.md)


## <a name="overview"></a>Vue d'ensemble

Cet article fournit des informations sur la création et la personnalisation des plans de récupération. 

Les plans de récupération comprennent un ou plusieurs groupes ordonnés présentant des machines virtuelles ou des serveurs physiques qui doivent être basculés en même temps. Les plans de récupération exécutent les actions suivantes :

- Ils définissent les groupes de machines qui basculent, puis démarrent ensemble.
- Ils modélisent les dépendances entre les machines, en les rassemblant au sein d’un groupe de plan de récupération. Par exemple, si vous souhaitez basculer et afficher une application spécifique, il vous faut regrouper les machines virtuelles dédiées dans un même groupe de plan de récupération.
- Ils automatisent et étendent le basculement. Vous pouvez exécuter un basculement test, planifié ou non planifié sur un plan de récupération. Il est possible de personnaliser les plans de récupération avec des scripts, Microsoft Azure Automation ou des actions manuelles.

Les plans de récupération sont affichés sous l’onglet **Plans de récupération** du portail Azure Site Recovery.


Envoyez vos commentaires ou vos questions au bas de cet article ou sur le [Forum Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="before-you-start"></a>Avant de commencer

Notez les points suivants :

- Un plan de récupération ne peut pas comprendre à la fois des machines virtuelles avec une seule carte réseau et des machines virtuelles avec plusieurs cartes réseau. Azure Cloud Service n’autorise pas la récupération simultanée de ces différents types de machines de virtuelles.
- Vous pouvez étendre vos plans de récupération à l’aide de scripts et d’actions manuelles. Notez les points suivants :
    - Écrivez des scripts à l’aide de Windows PowerShell.
    - Veillez à utiliser des blocs try-catch dans vos scripts, ceci pour garantir le traitement approprié des exceptions. Si le script comporte une exception, son exécution s’interrompt et la tâche est mise en échec.  Si une erreur se produit, aucune des éventuelles portions restantes du script ne s’exécute. Si cela se produit lorsque vous exécutez un basculement non planifié, le plan de récupération se poursuit. Si cela se produit lorsque vous exécutez un basculement planifié, le plan de récupération s’arrête. Le cas échéant, corrigez le script, vérifiez sa bonne exécution, puis exécutez de nouveau le plan de récupération.
    - La commande Write-Host, qui ne fonctionne pas dans un script de plan de récupération, entraîne la mise en échec du script. Pour créer une sortie, générez un script de proxy exécutant votre script principal, et assurez-vous que l’ensemble des sorties soient extraites à l’aide de la commande >>.
    - Le script expire si aucune sortie n’est produite dans les 600 secondes.
    - Si des éléments sont écrits sur STDERR, le script est classé comme mis en échec. Ces informations s’affichent dans les détails d’exécution du script.
    - Si vous utilisez VMM dans votre déploiement, notez ce qui suit :

        - Les scripts d’un plan de récupération s’exécutent dans le contexte d’un compte de service VMM. Assurez-vous que ce compte dispose des autorisations en lecture sur le partage distant sur lequel se trouve le script, et testez l’exécution du script au niveau de privilège du compte de service VMM.
        - Les applets de commande VMM sont fournies dans un module Windows PowerShell. Le module VMM Windows PowerShell est installé lors du montage de la console VMM. Pour charger le module VMM dans votre script, exécutez la commande suivante dans le script : Import-Module -Name virtualmachinemanager. [Obtenir des détails](hhttps://technet.microsoft.com/library/hh875013.aspx).
        - Vérifiez que vous disposez d’au moins un serveur de bibliothèque au sein de votre déploiement VMM. Par défaut, le chemin d’accès de partage de bibliothèque d’un serveur VMM est disponible localement sur le serveur VMM, sous le nom de dossier MSCVMMLibrary.
        - Si votre chemin d’accès de partage de bibliothèque est distant (ou local mais non partagé avec MSCVMMLibrary), configurez le partage comme suit (en utilisant \\libserver2.contoso.com\share\ comme exemple) :
            - Ouvrez l’Éditeur du Registre, puis accédez à HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\Azure Site Recovery\Registration.
            -  Remplacez la valeur de ScriptLibraryPath par \\libserver2.contoso.com\share\. Spécifiez le nom de domaine complet. Fournissez des autorisations d’accès à l’emplacement de partage.
            -  Assurez-vous de tester le script avec un compte utilisateur possédant les autorisations du compte de service VMM, ceci pour garantir que les scripts évalués de manière isolée s’exécutent comme dans les plans de récupération. Sur le serveur VMM, définissez le mode de contournement suivant de la stratégie d’exécution :
                -  Ouvrez la console Windows PowerShell 64 bits à l’aide des privilèges élevés.
                -  Entrez : **Set-executionpolicy bypass**. [Obtenir des détails](https://technet.microsoft.com/library/ee176961.aspx).

## <a name="create-a-recovery-plan"></a>Créer un plan de récupération

La méthode de création du plan de récupération dépend de votre déploiement Azure Site Recovery.

- **Réplication des machines virtuelles VMware et des serveurs physiques**: créez un plan, puis ajoutez à un plan de récupération des groupes de réplication contenant des machines virtuelles et des serveurs physiques.
- **Réplication de machines virtuelles Hyper-V (issues d’un cloud VMM)**: créez un plan, puis ajoutez des machines virtuelles Hyper-V protégées issues d’un cloud VMM à un plan de récupération.
- **Réplication de machines virtuelles Hyper-V (non issues d’un cloud VMM)**: créez un plan, puis ajoutez des machines virtuelles Hyper-V protégées issues d’un groupe de protection à un plan de récupération.
- **Réplication SAN**: créez un plan, puis ajoutez un groupe de réplication contenant des machines virtuelles au plan de récupération. Ici, vous sélectionnez un groupe de réplication plutôt que des machines virtuelles, dans la mesure où l’ensemble des machines virtuelles d’un groupe doivent basculer ensemble (la couche de stockage est basculée en premier lieu).


Pour créer un plan de récupération, procédez comme suit :

1. Cliquez sur l’onglet **Plans de récupération** > **Créer un plan de récupération**.
Spécifiez un nom pour le plan de récupération, puis définissez une source et une cible. Le basculement et la récupération doivent être activés sur les machines virtuelles du serveur source.

    - Si vous exécutez une réplication de VMM vers VMM, sélectionnez **Type de source** > **VMM**, puis les serveurs VMM source et cible. Cliquez sur **Hyper-V** afin d’afficher les clouds qui sont configurés pour l’utilisation de Réplica Hyper-V. 
    - Si vous exécutez une réplication de VMM vers VMM à l’aide d’un réseau SAN, sélectionnez **Type de source** > **VMM**, puis les serveurs VMM source et cible. Pour afficher les clouds configurés pour la réplication SAN, cliquez sur **SAN** .
    - Si vous exécutez une réplication de VMM vers Azure, sélectionnez **Type de source** > **VMM**.  Sélectionnez le serveur VMM source et **Azure** en tant que cible.
    - Si vous effectuez une réplication à partir d’un site Hyper-V, sélectionnez **Type de source** > **Site Hyper-V**. Sélectionnez le site en tant que source et **Azure **en tant que cible.
    - Si vous exécutez une réplication de VMware ou d’un serveur physique local vers Microsoft Azure, sélectionnez un serveur de configuration en tant que source et **Azure** en tant que cible.

2. Dans **Sélectionner les machines virtuelles** , sélectionnez les machines virtuelles (ou le groupe de réplication) que vous voulez ajouter au groupe par défaut (Groupe 1) dans le plan de récupération.

## <a name="customize-recovery-plans"></a>Personnaliser des plans de récupération

Une fois que vous avez ajouté les machines virtuelles protégées ou les groupes de réplication au groupe par défaut du plan de récupération et créé le plan, vous pouvez le personnaliser :

- **Ajouter des groupes**: il est possible d’ajouter des groupes supplémentaires au plan de récupération. Les groupes sont numérotés dans l’ordre dans lequel vous les ajouter. Vous pouvez ajouter jusqu’à sept groupes. Une fois que vous les avez ajoutés, vous pouvez y intégrer davantage de machines ou de groupes de réplication. Notez que les machines virtuelles et les groupes de réplication peuvent être incorporés au sein d’un seul plan de récupération.
- **Ajouter un script **: vous pouvez ajouter des scripts avant ou après un groupe de plan de récupération. Lorsque vous ajoutez un script, vous associez un nouvel ensemble d’actions au groupe. Par exemple, un ensemble d’étapes préliminaires au sein du Groupe 1 sera créé avec le nom : Groupe 1 : Étapes préliminaires. L’ensemble des étapes préliminaires seront répertoriées dans cet ensemble. Notez que pour ajouter un script sur le site principal, vous devez disposer d’un serveur VMM déployé.
- **Ajouter une action manuelle**: il est possible d’ajouter des actions manuelles qui s’exécutent avant ou après un groupe de plan de récupération. Lorsque le plan de récupération s’exécute, il est arrêté au point d’insertion de l’action manuelle, et une boîte de dialogue vous invite à confirmer la bonne réalisation de l’action manuelle.

## <a name="extend-recovery-plans-with-scripts"></a>Étendre des plans de récupération avec des scripts

Vous pouvez ajouter un script à votre plan de récupération :

- Si vous possédez un site source VMM, livre à vous de créer un script sur le serveur VMM, et de l’inclure dans votre plan de récupération.
- Si vous exécutez une réplication vers Microsoft Azure, vous pouvez intégrer les Runbooks Azure Automation dans votre plan de récupération.

### <a name="create-a-vmm-script"></a>Créer un script VMM


Créez le script comme suit :

1. Créez un dossier dans le partage de bibliothèque, par exemple \<VMMServerName>\MSSCVMMLibrary\RPScripts. Placez-le dans les serveurs VMM source et cibles.
2. Créez le script (par exemple RPScript), et vérifiez son bon fonctionnement.
3. Placez le script à l’emplacement \<VMMServerName>\MSSCVMMLibrary sur les serveurs VMM source et cible.

### <a name="create-an-azure-automation-runbook"></a>Créer un Runbook Azure Automation

Pour étendre votre plan de récupération, exécutez un Runbook Automation Plan pendant le plan. [En savoir plus](site-recovery-runbook-automation.md).


### <a name="add-custom-settings-to-a-recovery-plan"></a>Ajouter des paramètres personnalisés à un plan de récupération

1. Ouvrez le plan de récupération que vous souhaitez personnaliser.
2. Cliquez pour ajouter une machine virtuelle ou un groupe.
3. Pour ajouter un script ou une action manuelle, cliquez sur un élément de la liste **Étape,** puis cliquez sur **Script** ou sur **Action manuelle**. Indiquez si vous souhaitez ajouter le script ou l’action avant ou après l’élément sélectionné. Utilisez les boutons de commande de **Déplacement vers le haut** et de **Déplacement vers le bas** pour faire monter ou descendre le script.
4. Si vous ajoutez un script VMM, sélectionnez **Basculement vers script VMM**, puis dans **Chemin du script**, entrez le chemin d’accès relatif au partage. Ainsi, dans le cadre de notre exemple où le partage est situé à l’emplacement \\<VMMServerName>\MSSCVMMLibrary\RPScripts, définissez le chemin d’accès : \RPScripts\RPScript.PS1.
5. Si vous ajoutez un Runbook Azure Automation, spécifiez le **Compte Azure Automation** dans lequel se trouve le Runbook, puis sélectionnez le **Script Runbook Azure** approprié.
5. Exécutez un basculement du plan de récupération, afin de vous assurer du bon fonctionnement du script.


## <a name="next-steps"></a>Étapes suivantes

Vous pouvez exécuter différents types de basculements de plan de récupération, notamment des basculements tests pour vérifier votre environnement, des basculements planifiés et des basculements non planifiés. [En savoir plus](site-recovery-failover.md).


 



<!--HONumber=Oct16_HO2-->


