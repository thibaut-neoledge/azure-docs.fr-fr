---
title: "Résolution des problèmes Azure Site Recovery depuis VMware vers Azure | Microsoft Docs"
description: "Résoudre les erreurs rencontrées lors de la réplication de machines virtuelles Azure"
services: site-recovery
documentationcenter: 
author: asgang
manager: srinathv
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 08/24/2017
ms.author: asgang
ms.translationtype: HT
ms.sourcegitcommit: 12c20264b14a477643a4bbc1469a8d1c0941c6e6
ms.openlocfilehash: d4d6d273001642ebb8c960333a1c01977e863dbc
ms.contentlocale: fr-fr
ms.lasthandoff: 09/07/2017

---
# <a name="troubleshoot-mobility-service-push-installation-issues"></a>Résoudre les problèmes d’installation Push du service Mobilité

Cet article décrit les problèmes courants que vous pouvez rencontrer lorsque vous tentez d’installer le service Mobilité d’Azure Site Recovery sur le serveur source en vue d’activer la protection.

## <a name="error-code-95107-protection-could-not-be-enabled"></a>(Code d’erreur 95107) Impossible d’activer la protection
**Code d’erreur** | **Causes possibles** | **Recommandations propres à l’erreur**
--- | --- | ---
95107 </br>**Message** : Échec de l’installation Push du service Mobilité sur la machine source avec le code d’erreur **EP0858**. <br> Soit les informations d’identification fournies pour installer le service Mobilité sont incorrectes, soit le compte d’utilisateur ne dispose pas de privilèges suffisants. | Les informations d’identification de l’utilisateur fournies pour installer le service Mobilité sur la machine source sont incorrectes. | Vérifiez que les informations d’identification fournies pour la machine source sur le serveur de configuration sont correctes. <br> Pour ajouter ou modifier les informations d’identification de l’utilisateur : accédez au serveur de configuration, puis sélectionnez **Cspsconfigtool** > **Gérer le compte**. </br> En outre, vérifiez que les prérequis suivants sont présents pour garantir le bon déroulement de l’installation Push.

## <a name="error-code-95015-protection-could-not-be-enabled"></a>(Code d’erreur 95015) Impossible d’activer la protection
**Code d’erreur** | **Causes possibles** | **Recommandations propres à l’erreur**
--- | --- | ---
95105 </br>**Message** : Échec de l’installation Push du service Mobilité sur la machine source avec le code d’erreur **EP0856**. <br> Soit le **Partage de fichiers et d’imprimantes** n’est pas autorisé sur la machine source, soit il y a des problèmes de connectivité réseau entre le serveur de processus et la machine source.| Le **Partage de fichiers et d’imprimantes** n’est pas activé. | Autorisez le **Partage de fichiers et d’imprimantes** sur la machine source dans le pare-feu Windows. Sur la machine source, sous **Pare-feu Windows** > **Autoriser une application ou une fonctionnalité via le Pare-feu Windows** , sélectionnez **Partage de fichiers et d’imprimantes pour tous les profils**. </br> En outre, vérifiez que les prérequis suivants sont présents pour garantir le bon déroulement de l’installation Push.

## <a name="error-code-95117-protection-could-not-be-enabled"></a>(Code d’erreur 95117) Impossible d’activer la protection
**Code d’erreur** | **Causes possibles** | **Recommandations propres à l’erreur**
--- | --- | ---
95117 </br>**Message** : Échec de l’installation Push du service Mobilité sur la machine source avec le code d’erreur **EP0865**. <br> Soit la machine source n’est pas en cours d’exécution, soit il y a des problèmes de connectivité réseau entre le serveur de processus et la machine source. | Problèmes de connectivité réseau entre le serveur de processus et le serveur source. | Vérifiez la connectivité réseau entre le serveur de processus et le serveur source. </br> En outre, vérifiez que les prérequis suivants sont présents pour garantir le bon déroulement de l’installation Push.

## <a name="error-code-95103-protection-could-not-be-enabled"></a>(Code d’erreur 95103) Impossible d’activer la protection
**Code d’erreur** | **Causes possibles** | **Recommandations propres à l’erreur**
--- | --- | ---
95103 </br>**Message** : Échec de l’installation Push du service Mobilité sur la machine source avec le code d’erreur **EP0854**. <br> Soit WMI (Windows Management Instrumentation) n’est pas autorisé sur la machine source, soit il y a des problèmes de connectivité réseau entre le serveur de processus et la machine source.| WMI est bloqué dans le pare-feu Windows. | Autorisez WMI dans le pare-feu Windows. Sous **Pare-feu Windows** > **Autoriser une application ou une fonctionnalité via le Pare-feu Windows**, sélectionnez **WMI pour tous les profils**. </br> En outre, vérifiez que les prérequis suivants sont présents pour garantir le bon déroulement de l’installation Push.

## <a name="check-push-installation-logs-for-errors"></a>Vérifier la présence d’erreurs dans les journaux de l’installation Push

Sur le serveur de configuration ou le serveur de processus, accédez au fichier PushinstallService situé à l’emplacement <Microsoft Azure Site Recovery Install Location>\home\svsystems\pushinstallsvc\ pour connaître l’origine du problème. Utilisez les étapes de dépannage ci-dessous afin de le résoudre.</br>

![Journaux de l’installation Push](./media/site-recovery-protection-common-errors/pushinstalllogs.png)

## <a name="push-installation-prerequisites-for-windows"></a>Prérequis de l’installation Push pour Windows
### <a name="ensure-that-file-and-printer-sharing-is-enabled"></a>Vérifier que l’option **Partage de fichiers et d’imprimantes** est activée
Autorisez le **Partage de fichiers et d’imprimantes** et **Windows Management Instrumentation** sur la machine source dans le Pare-feu Windows. </br>
#### <a name="if-the-source-machine-is-domain-joined-br"></a>Si la machine source est jointe à un domaine </br>
Configurez les paramètres du Pare-feu à l’aide de la console de gestion des stratégies de groupe.

1. Connectez-vous à la machine de domaine Azure Active Directory en tant qu’administrateur. Ouvrez la console de gestion des stratégies de groupe (GPMC.MSC, à exécuter à partir du menu **Démarrer** > **Exécuter**).</br>

2. Si la console de gestion des stratégies de groupe n’est pas installée, consultez [Installer la console GPMC](https://technet.microsoft.com/library/cc725932.aspx). </br>

3. Dans l’arborescence de la console de gestion des stratégies de groupe, double-cliquez sur **Objets de stratégie de groupe** dans la forêt et le domaine. Accédez à **Stratégie de domaine par défaut**. </br>

    ![Stratégie de domaine par défaut](./media/site-recovery-protection-common-errors/gpmc1.png) </br>
</br>
4. Cliquez avec le bouton droit sur **Stratégie de domaine par défaut** > **Modifier**. La fenêtre **Éditeur de gestion des stratégies de groupe** s’ouvre. </br>

    ![Éditeur de gestion des stratégies de groupe](./media/site-recovery-protection-common-errors/gpmc2.png) </br>
</br>
5. Dans **l’Éditeur de gestion des stratégies de groupe**, accédez à **Configuration ordinateur** > **Stratégies** > **Modèles d’administration** > **Réseau** > **Connexions réseau** > **Pare-feu Windows**. </br>

    ![Pare-feu Windows](./media/site-recovery-protection-common-errors/gpmc3.png) </br>
</br>
6. Activez les paramètres suivants pour le **Profil de domaine** et le **Profil Standard** : </br>

    a. Double-cliquez sur **Pare-feu Windows : autoriser l’exception de partage de fichiers entrants et d’imprimantes**. Sélectionnez **Activé**, puis cliquez sur **OK**. </br>

    b. Double cliquez sur **Pare-feu Windows : autoriser l’exception d’administration à distance entrante**. Sélectionnez **Activé**, puis cliquez sur **OK**. </br>

    ![Profil de domaine](./media/site-recovery-protection-common-errors/gpmc4.png) </br>
</br>

#### <a name="if-the-source-machine-isnt-domain-joined-and-part-of-a-workgroup-br"></a>Si la machine source n’est pas jointe à un domaine et qu’elle fait partie d’un groupe de travail </br>
Configurez les paramètres du Pare-feu sur la machine distante (pour le groupe de travail).

1. Accédez à la machine source.</br>

2. Sous **Pare-feu Windows** > **Autoriser une application ou une fonctionnalité via le Pare-feu Windows**, sélectionnez **Partage de fichiers et d’imprimantes pour tous les profils**. </br>

3. Sous **Pare-feu Windows** > **Autoriser une application ou une fonctionnalité via le Pare-feu Windows**, sélectionnez **WMI pour tous les profils**. </br>

#### <a name="disable-remote-user-account-control"></a>Désactiver le contrôle de compte d’utilisateur distant
Désactivez le contrôle de compte d’utilisateur à l’aide de la clé de Registre pour transmettre (push) le service Mobilité.

1. Sélectionnez **Démarrer** > **Exécuter**, tapez *regedit*, puis sélectionnez **Entrée**.

2. Repérez la sous-clé de Registre suivante et sélectionnez-la : HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System

3. Si l’entrée de Registre LocalAccountTokenFilterPolicy n’existe pas, effectuez les étapes suivantes :

    a. Dans le menu **Modifier**, sélectionnez **Nouveau**, puis sélectionnez **Valeur DWORD**.

    b. Tapez *LocalAccountTokenFilterPolicy*, puis appuyez sur **Entrée**.

    c. Cliquez avec le bouton droit sur **LocalAccountTokenFilterPolicy**, puis cliquez sur **Modifier**.

    d. Dans la zone de données **Valeur**, tapez *1*, puis cliquez sur **OK**.

4. Fermez l’Éditeur du Registre.


## <a name="push-installation-prerequisites-for-linux"></a>Prérequis de l’installation Push pour Linux

1. Créez un utilisateur racine sur le serveur Linux source. (Utilisez ce compte uniquement pour l’installation Push et les mises à jour.)</br>

2. Vérifiez que le fichier /etc/hosts sur le serveur Linux source contient des entrées qui mappent le nom d’hôte local sur les adresses IP associées à toutes les cartes réseau. </br>

3. Vérifiez que les derniers packages openssl, openssh-server et openssh sont installés sur le serveur Linux source. Vérifiez que le port ssh 22 est activé et fonctionne. </br>

4. Vérifiez si des entrées obsolètes d’agents sont déjà présentes sur le serveur source. Si c’est le cas, désinstallez-les, puis redémarrez le serveur. Réinstallez l’agent. </br>

#### <a name="enable-sftp-subsystem-and-password-authentication-in-the-sshdconfig-file"></a>Activer l’authentification par mot de passe et le sous-système SFTP dans le fichier sshd_config
1. Sur le serveur source, connectez-vous en tant qu’utilisateur racine. </br>

2. Dans le fichier /etc/ssh/sshd_config, recherchez la ligne commençant par « PasswordAuthentication ». </br>

3. Supprimez les marques de commentaire de la ligne, puis remplacez la valeur « no » par la valeur « yes ». </br>

    ![PasswordAuthentication](./media/site-recovery-protection-common-errors/linux1.png)

4. Recherchez la ligne commençant par « Subsystem », puis supprimez les marques de commentaire de la ligne. </br>

    ![Subsystem](./media/site-recovery-protection-common-errors/linux2.png)

5. Enregistrez les modifications, puis redémarrez le service sshd. </br>


## <a name="push-installation-checks-on-the-configurationprocess-server"></a>Vérifications de l’installation Push sur le serveur de configuration ou le serveur de processus
#### <a name="validate-credentials-for-discovery-and-installation"></a>Valider les informations d’identification pour la détection et l’installation

1. Depuis le serveur de configuration, démarrez Cspsconfigtool.</br>

    ![WMItestConnect5](./media/site-recovery-protection-common-errors/wmitestconnect5.png) </br>

2. Assurez-vous que le compte utilisé pour la protection dispose des droits d’administrateur sur la machine source. </br>

#### <a name="check-connectivity-between-the-process-server-and-the-source-server"></a>Vérifier la connectivité réseau entre le serveur de processus et le serveur source
1. Vérifiez que le serveur de processus dispose d’une connexion Internet.

2. Vérifiez la connexion WMI à l’aide de wbemtest.exe. </br>

    a. Sur le serveur de processus, sélectionnez **Démarrer** > **Exécuter** > **wbemtest.exe**. La fenêtre **Windows Management Instrumentation Tester** s’ouvre :</br>

      ![Testeur WMI](./media/site-recovery-protection-common-errors/wmitestconnect1.png) </br>
   </br>
    b. Sélectionnez **Se connecter**, puis entrez l’adresse IP du serveur source dans la zone **Espace de noms**.

    c. Saisissez un nom dans le champ **Utilisateur**, puis entrez le **Mot de passe**. (Si la machine source est jointe au domaine, indiquez le nom de domaine, ainsi que le nom d’utilisateur au format *nomDomaine\nomUtilisateur*. Si la machine source fait partie d’un groupe de travail, fournissez uniquement le nom d’utilisateur.)

    d. Sélectionnez **Confidentialité du paquet** comme **niveau d’authentification**. </br>

      ![Confidentialité du paquet](./media/site-recovery-protection-common-errors/wmitestconnect2.png) </br>
   </br>
    e. Sélectionnez **Connecter**. À présent, la connexion WMI affiche les données fournies, et la fenêtre **Testeur WMI** s’affiche comme illustré ci-dessous : </br>

      ![Données du testeur WMI](./media/site-recovery-protection-common-errors/wmitestconnect3.png) </br>
   </br>
      Si la connexion WMI échoue, un message d’erreur s’affiche. La capture d’écran qui suit montre une tentative infructueuse si l’option **WMI/Administration à distance** n’est pas activée dans l’application autorisée par le Pare-feu Windows : </br>

    ![Message d’erreur du testeur WMI](./media/site-recovery-protection-common-errors/wmitestconnect4.png) </br>
   </br>

3. Vérifiez la connectivité et l’état WMI.</br>

    a. Sur le serveur de configuration ou le serveur de processus, sélectionnez **Démarrer** > **Exécuter** > **wmimgmt.msc** > **Actions** > **Autres actions** > **Se connecter à un autre ordinateur (machine source)**. </br>

    b. Entrez les informations d’identification du compte utilisé pour la protection et vérifiez si la connectivité est bonne. </br>

#### <a name="verify-that-network-shared-folders-of-the-source-machine-are-accessible-from-the-process-server-remotely-by-using-specified-credentials"></a>Vérifiez que les dossiers partagés de la machine source sur le réseau sont accessibles depuis le serveur de processus en utilisant à distance les informations d’identification spécifiées.

  1. Connectez-vous à la machine du serveur de processus, puis ouvrez l’Explorateur de fichiers. Dans la barre d’adresses, tapez *"\\\adresse-ip-machine-source\C$"*. Sélectionnez **Enter** (Entrer). </br>

      ![Explorateur de fichiers](./media/site-recovery-protection-common-errors/fileshare1.png) </br>

  2. Dans l’Explorateur de fichiers, vous êtes invité à entrer des informations d’identification. Entrez le nom d’utilisateur et le mot de passe, puis sélectionnez **OK**.</br>

      * Si la machine source est jointe au domaine, entrez le nom de domaine, ainsi que le nom d’utilisateur au format *nomDomaine\nomUtilisateur*.</br>

      * Si la machine source fait partie d’un groupe de travail, entrez uniquement le nom d’utilisateur. </br>

      ![Boîte de dialogue Informations d’identification](./media/site-recovery-protection-common-errors/fileshare2.png) </br>

  3. Si la connexion est établie, vous pouvez afficher les dossiers de la machine source à distance depuis le serveur de processus. </br>

      ![Vue des dossiers](./media/site-recovery-protection-common-errors/fileshare3.png) </br>

> [!NOTE] 
> Si la connexion échoue, vérifiez que tous les prérequis sont bien respectés.
>

Si vous ne souhaitez pas ouvrir **Windows Management Instrumentation**, vous pouvez également installer le service Mobilité manuellement sur la machine source.</br> 

Pour plus d’informations, consultez [Installer le service Mobilité manuellement à l’aide de l’interface utilisateur](site-recovery-vmware-to-azure-install-mob-svc.md#install-mobility-service-manually-by-using-the-gui) et [Installation à l’aide du Gestionnaire de configuration](site-recovery-install-mobility-service-using-sccm.md). </br>

## <a name="next-steps"></a>Étapes suivantes
- [Activer la réplication des machines virtuelles VMware](vmware-walkthrough-enable-replication.md)

