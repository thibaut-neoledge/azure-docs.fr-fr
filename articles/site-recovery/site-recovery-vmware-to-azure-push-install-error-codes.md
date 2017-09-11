---
title: "Résolution des problèmes Azure Site Recovery depuis VMware vers Azure | Microsoft Docs"
description: "Résolution des problèmes rencontrés lors de la réplication de machines virtuelles Azure"
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
ms.sourcegitcommit: 7456da29aa07372156f2b9c08ab83626dab7cc45
ms.openlocfilehash: 1e2452ccc6d3f1859a39e1ee09cdde32f53767ba
ms.contentlocale: fr-fr
ms.lasthandoff: 08/28/2017

---
# <a name="troubleshooting-mobility-service-push-install-issues"></a>Résolution des problèmes d’installation Push pour le service Mobilité

Cet article détaille les problèmes courants rencontrés lors d’une tentative d’installation du service Mobilité sur le serveur source en vue d’activer la protection.

## <a name="error-code-95107-protection-could-not-be-enabled"></a>(Code d’erreur 95107) Impossible d’activer la protection.
**Code d’erreur** | **Causes possibles** | **Recommandations propres à l’erreur**
--- | --- | ---
95107 </br>***Message -*** Échec de l’installation Push du service Mobilité sur la machine source avec le code d’erreur ***EP0858***. <br> Soit les informations d’identification fournies pour installer le service Mobilité sont incorrectes, soit le compte d’utilisateur dispose de privilèges insuffisants | Les informations d’identification de l’utilisateur fournies pour installer le service Mobilité sur la machine source sont incorrectes | Vérifiez que les informations d’identification fournies pour la machine source sur le serveur de configuration sont correctes. <br> Pour ajouter ou modifier les informations d’identification de l’utilisateur : accédez au serveur de configuration > icône Cspsconfigtool > Gérer le compte. </br> Vérifiez également que les prérequis ci-dessous sont respectés pour que l’installation Push se déroule correctement.

## <a name="error-code-95015-protection-could-not-be-enabled"></a>(Code d’erreur 95015) Impossible d’activer la protection.
**Code d’erreur** | **Causes possibles** | **Recommandations propres à l’erreur**
--- | --- | ---
95105 </br>***Message -*** Échec de l’installation Push du service Mobilité sur la machine source avec le code d’erreur ***EP0856***. <br> Soit le Partage de fichiers et d’imprimantes n’est pas autorisé sur la machine source, soit il y a des problèmes de connectivité réseau entre le serveur de processus et la machine source| Le Partage de fichiers et d’imprimantes n’est pas activé | Autorisez le Partage de fichiers et d’imprimantes sur la machine source dans le Pare-feu Windows. Pour cela, accédez à la machine source > sous Paramètres du Pare-feu Windows > Autoriser une application ou une fonctionnalité via le Pare-feu > sélectionnez Partage de fichiers et imprimantes pour tous les profils. </br> Vérifiez également que les prérequis ci-dessous sont respectés pour que l’installation Push se déroule correctement.

## <a name="error-code-95117-protection-could-not-be-enabled"></a>(Code d’erreur 95117) Impossible d’activer la protection.
**Code d’erreur** | **Causes possibles** | **Recommandations propres à l’erreur**
--- | --- | ---
95117 </br>***Message -*** Échec de l’installation Push du service Mobilité sur la machine source avec le code d’erreur ***EP0865***. <br> Soit la machine source ne fonctionne pas, soit il y a des problèmes de connectivité réseau entre le serveur de processus et la machine source | Connectivité réseau entre le serveur de processus et le serveur source | Vérifiez la connectivité entre le serveur de processus et le serveur source. </br> Vérifiez également que les prérequis ci-dessous sont respectés pour que l’installation Push se déroule correctement.

## <a name="error-code-95103-protection-could-not-be-enabled"></a>(Code d’erreur 95103) Impossible d’activer la protection.
**Code d’erreur** | **Causes possibles** | **Recommandations propres à l’erreur**
--- | --- | ---
95103 </br>***Message -*** Échec de l’installation Push du service Mobilité sur la machine source avec le code d’erreur ***EP0854***. <br> Soit Windows Management Instrumentation (WMI) n’est pas autorisé sur la machine source, soit il y a des problèmes de connectivité réseau entre le serveur de processus et la machine source| Windows Management Instrumentation (WMI) est bloqué dans le Pare-feu Windows | Autorisez WMI dans le Pare-feu Windows. Sous Paramètres du Pare-feu Windows > Autoriser une application ou une fonctionnalité via le Pare-feu Windows > sélectionnez WMI pour tous les profils. </br> Vérifiez également que les prérequis ci-dessous sont respectés pour que l’installation Push se déroule correctement.

## <a name="check-push-install-logs-for-errors"></a>Vérifier les journaux d’installation Push à la recherche d’erreurs

Sur le serveur de configuration/processus, accédez au fichier PushinstallService situé à l’emplacement <Microsoft Azure Site Recovery Install Location>\home\svsystems\pushinstallsvc\ pour connaître l’origine du problème et utiliser les étapes de dépannage ci-dessous afin de le résoudre.</br>
![JournauxInstallationPush](./media/site-recovery-protection-common-errors/pushinstalllogs.png)

## <a name="push-install-pre-requisites-for-windows"></a>Prérequis de l’installation Push pour Windows
### <a name="ensure-file-and-printer-sharing-is-enabled"></a>S’assurer que l’option Partage de fichiers et d’imprimantes est activée
Autorisez le Partage de fichiers et d’imprimantes et Windows Management Instrumentation sur la machine source dans le Pare-feu Windows. </br>
#### <a name="if-source-machine-is-domain-joined-br"></a>Si la machine source est jointe à un domaine : </br>
Configurez les paramètres du Pare-feu à l’aide de la console de gestion des stratégies de groupe (GPMC).
1. Connectez-vous à la machine de domaine Active directory en tant qu’administrateur et ouvrez la console de gestion des stratégies de groupe (GPMC.MSC, exécution à partir de Démarrer > Exécuter).</br>
3. Si GPMC n’est pas installée, suivez le lien pour [Installer la console de gestion des stratégies de groupe](https://technet.microsoft.com/library/cc725932.aspx). </br>
4. Dans l’arborescence de la console GPMC, double-cliquez sur les objets de stratégie de groupe dans la forêt et le domaine, puis accédez à Stratégie de domaine par défaut. </br>
![gpmc1](./media/site-recovery-protection-common-errors/gpmc1.png) </br>
</br>
5. Cliquez avec le bouton droit sur Stratégie de domaine par défaut > Modifier > une nouvelle fenêtre Éditeur de gestion des stratégies de groupe s’ouvre. </br>
![gpmc2](./media/site-recovery-protection-common-errors/gpmc2.png) </br>
</br>
6. Dans l’Éditeur de gestion des stratégies de groupe, accédez à Configuration ordinateur > Stratégies > Modèles d’administration > Réseau > Connexions réseau > Pare-feu Windows. </br>
![gpmc3](./media/site-recovery-protection-common-errors/gpmc3.png) </br>
</br>
7. Activez les paramètres suivants pour le Profil de domaine et le Profil Standard : </br>
a) Double-cliquez sur l’exception Pare-feu Windows : autoriser l’exception de partage de fichiers entrants et d’imprimantes. Sélectionnez Activé, puis cliquez sur OK. </br>
b) Double cliquez sur l’exception Pare-feu Windows : autoriser l’exception d’administration à distance entrante. Sélectionnez Activé, puis cliquez sur OK. </br>
![gpmc4](./media/site-recovery-protection-common-errors/gpmc4.png) </br>
</br>

###### <a name="if-source-machine-is-not-domain-joined-and-part-of-workgroup-br"></a>Si la machine source n’est pas jointe à un domaine et qu’elle fait partie d’un groupe de travail </br>
Configurez les paramètres du Pare-feu sur la machine distante (pour le groupe de travail) :
1. Accédez à la machine source,</br>
2. Sous Paramètres du Pare-feu Windows > Autoriser une application ou une fonctionnalité via le Pare-feu Windows > sélectionnez Partage de fichiers et d’imprimantes pour tous les profils. </br>
3. Sous Paramètres du Pare-feu Windows > Autoriser une application ou une fonctionnalité via le Pare-feu Windows > sélectionnez WMI pour tous les profils. </br>

#### <a name="disable-remote-user-account-control-uac"></a>Désactiver le contrôle de compte d’utilisateur (UAC)
Désactivez le contrôle de compte d’utilisateur à l’aide de la clé de Registre pour transmettre le service Mobilité.
1. Cliquez sur Démarrer > Exécuter > tapez regedit > Entrée
2. Repérez la sous-clé de Registre suivante et cliquez dessus : HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System
3. Si l’entrée de Registre LocalAccountTokenFilterPolicy n’existe pas, effectuez les étapes suivantes :
4. Dans le menu Edition > Nouveau > cliquez sur Valeur DWORD.
5. Tapez LocalAccountTokenFilterPolicy et appuyez sur Entrée.
6. Cliquez avec le bouton droit sur LocalAccountTokenFilterPolicy, puis cliquez sur Modifier.
7. Dans la zone Données de la valeur, tapez 1 et cliquez sur OK.
8. Quittez l’Éditeur du Registre.


## <a name="push-install-pre-requisites-for-linux"></a>Prérequis de l’installation Push pour Linux :

1. Créez un utilisateur racine sur le serveur Linux source. (Utilisez ce compte uniquement pour l’installation Push et les mises à jour.)</br>
2. Vérifiez que le fichier /etc/hosts sur le serveur Linux source contient des entrées qui mappent le nom d’hôte local sur les adresses IP associées à toutes les cartes réseau. </br>
3. Assurez-vous que les derniers packages openssl, openssh-server et openssh sont installés sur le serveur Linux source. </br>
Vérifiez que le port ssh 22 est activé et fonctionne. </br>
4. Vérifiez si des entrées obsolètes d’agents sont déjà présentes sur le serveur source, désinstallez les agents plus anciens, redémarrez le serveur et réinstallez l’agent. </br>

#### <a name="enable-sftp-subsystem-and-password-authentication-in-the-sshdconfig-file"></a>Activer l’authentification par mot de passe et le sous-système SFTP dans le fichier sshd_config
1. Sur le serveur source, connectez-vous en tant que racine. </br>
2. Dans le fichier /etc/ssh/sshd_config,</br> recherchez la ligne commençant par PasswordAuthentication. </br>
3. d.   Supprimez les marques de commentaire de la ligne et remplacez la valeur « no » par « yes ». </br>
![Linux1](./media/site-recovery-protection-common-errors/linux1.png)
4. Recherchez la ligne commençant par Subsystem et supprimez les marques de commentaire de la ligne. </br>
![Linux2](./media/site-recovery-protection-common-errors/linux2.png)
5. Enregistrez les modifications et redémarrez le service sshd. </br>

## <a name="push-installation-checks-on-configurationprocess-server"></a>Contrôlez l’installation Push sur le serveur de configuration/processus.
#### <a name="validate-credentials-for-discovery-and-installation"></a>Valider les informations d’identification pour la détection et l’installation

1. Depuis le serveur de configuration, lancez Cspsconfigtool.</br>
![VérificationConnexionWMI5](./media/site-recovery-protection-common-errors/wmitestconnect5.png) </br>

2. Assurez-vous que le compte utilisé pour la protection dispose des droits d’administrateur sur la machine source. </br>

#### <a name="check-connectivity-between-process-server-and-source-server"></a>Vérifier la connectivité entre le serveur de processus et le serveur source
1. Vérifiez que le serveur de processus dispose d’une connexion Internet.
2. Vérifiez la connexion WMI à l’aide de wbemtest.exe. </br>
Sur le serveur de processus, cliquez sur Démarrer > Exécuter > wbemtest.exe > la fenêtre Testeur WMI (Windows Management Instrumentation) s’ouvre comme illustré.</br>
   ![VérificationConnexionWMI1](./media/site-recovery-protection-common-errors/wmitestconnect1.png) </br>
   </br>
Cliquez sur Connexion > Entrez l’adresse IP du serveur source dans la zone Espace de noms, renseignez les champs Utilisateur et Mot de passe (si la machine source est jointe au domaine, indiquez le nom de domaine ainsi que le nom d’utilisateur sous la forme nomDomaine\nomUtilisateur. Si la machine source est dans un groupe de travail, fournissez uniquement le nom d’utilisateur.) Sélectionnez Confidentialité du paquet comme Niveau d’authentification. </br>
![VérificationConnexionWMI2](./media/site-recovery-protection-common-errors/wmitestconnect2.png) </br>
   </br>
   Cliquez sur Connexion. À présent, la connexion WMI doit fonctionner correctement avec les données fournies, et la fenêtre Testeur WMI (Windows Management Instrumentation) doit s’afficher comme illustré ci-dessous : </br>
   ![VérificationConnexionWMI3](./media/site-recovery-protection-common-errors/wmitestconnect3.png) </br>
</br>
   Si la connexion WMI ne fonctionne pas correctement, un message d’erreur s’affiche dans une fenêtre indépendante. La capture d’écran ci-dessous montre une tentative infructueuse si l’option WMI/Administration à distance n’est pas activée dans les applications autorisées du Pare-feu Windows. </br>
   ![VérificationConnexionWMIt4](./media/site-recovery-protection-common-errors/wmitestconnect4.png) </br>
</br>

3. Vérifiez la connectivité et l’état WMI.</br>
Sur le serveur de configuration/processus, </br>
Cliquez sur Démarrer > Exécuter > wmimgmt.msc > Actions > Autres actions > Se connecter à un autre ordinateur (machine source). </br>
Entrez les informations d’identification du compte utilisé pour la protection et vérifiez si la connectivité est bonne. </br>

#### <a name="verify-network-shared-folders-of-source-machine-is-accessible-from-process-server-ps-remotely-using-specified-credentials"></a>Vérifiez que les dossiers partagés de la machine source sur le réseau sont accessibles depuis le serveur de processus en utilisant à distance les informations d’identification spécifiées.
  1. Ouvrez une session sur la machine du serveur de processus, puis ouvrez l’Explorateur de fichiers > dans la barre d’adresses, tapez > "\\\source-machine-ip\C$" > et cliquez sur Entrée. </br>
  ![PartageDeFichiers1](./media/site-recovery-protection-common-errors/fileshare1.png) </br>
  2. Dans l’Explorateur de fichiers, vous êtes invité à indiquer des informations d’identification. Entrez le nom d’utilisateur et le mot de passe > cliquez sur OK.</br>
   Si la machine source est jointe au domaine, indiquez le nom de domaine ainsi que le nom d’utilisateur sous la forme nomDomaine\nomUtilisateur.</br>
   Si la machine source se trouve dans un groupe de travail, fournissez uniquement le « nom d’utilisateur ». </br>
  ![PartageDeFichiers2](./media/site-recovery-protection-common-errors/fileshare2.png) </br>
  3. Si la connexion est établie, vous pouvez afficher les dossiers de la machine source à distance depuis le serveur de processus. </br>
  ![PartageDeFichiers3](./media/site-recovery-protection-common-errors/fileshare3.png) </br>

> [!NOTE] 
> Si la connexion échoue, vérifiez que tous les prérequis sont bien respectés.
>

Si vous ne souhaitez pas ouvrir Windows Management Instrumentation, vous pouvez également installer le service Mobilité manuellement sur la machine source.</br> [Installer le service Mobilité manuellement par le biais de l’interface utilisateur](site-recovery-vmware-to-azure-install-mob-svc.md#install-mobility-service-manually-by-using-the-gui) </br>
[Installation à l’aide du Gestionnaire de configuration](site-recovery-install-mobility-service-using-sccm.md) </br>

## <a name="next-steps"></a>Étapes suivantes
- [Activer la réplication des machines virtuelles VMware](vmware-walkthrough-enable-replication.md)

