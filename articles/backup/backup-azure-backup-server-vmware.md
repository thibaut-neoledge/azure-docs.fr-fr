---
title: "Utilisez le serveur de sauvegarde Azure pour protéger le serveur VMware | Microsoft Docs"
description: "Sauvegardez un serveur VMware dans Azure ou sur un disque à l’aide du serveur de sauvegarde Azure. Cet article vous permet de protéger votre charge de travail VMware."
services: backup
documentationcenter: 
author: markgalioto
manager: carmonm
ms.assetid: 6b131caf-de85-4eba-b8e6-d8a04545cd9d
ms.service: backup
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 04/20/2017
ms.author: markgal;
translationtype: Human Translation
ms.sourcegitcommit: abdbb9a43f6f01303844677d900d11d984150df0
ms.openlocfilehash: 34146bd110661c12c4ec1e11d34d7bdfa3cac688
ms.lasthandoff: 04/20/2017


---
# <a name="back-up-vmware-server-to-azure"></a>Sauvegarder un serveur VMware dans Azure

Cet article explique comment configurer un serveur de sauvegarde Azure pour protéger les charges de travail du serveur VMware. Cet article suppose que vous avez déjà installé le serveur de sauvegarde Azure. Si vous n’avez pas de serveur de sauvegarde Azure, consultez l’article [Préparer la sauvegarde de charges de travail à l’aide du serveur de sauvegarde Azure](backup-azure-microsoft-azure-backup.md).

Le serveur de sauvegarde Azure peut sauvegarder, ou protéger, les versions 6.0 et 5.5 du serveur VMware vCenter.


## <a name="create-a-secure-connection-to-the-vcenter-server"></a>Créer une connexion sécurisée au serveur vCenter

Par défaut, le serveur de sauvegarde Azure communique avec les serveurs vCenter via le canal HTTPS. Pour une communication sécurisée, il est recommandé d’installer le certificat de l’autorité de certification VMware sur le serveur de sauvegarde Azure. Si vous n’avez pas besoin de sécuriser la communication et préférez désactiver l’obligation d’utiliser le protocole HTTPS, consultez la section [Désactiver le protocole de communication sécurisée](backup-azure-backup-server-vmware.md#disable-secure-communication-protocol). Pour créer une connexion sécurisée entre le serveur de sauvegarde Azure et le serveur vCenter, importez le certificat approuvé sur le serveur de sauvegarde Azure.

En général, vous utilisez un navigateur sur l’ordinateur du serveur de sauvegarde Azure pour vous connecter au serveur vCenter via le client web vSphere. La première fois que vous utilisez le navigateur du serveur de sauvegarde Azure pour vous connecter au serveur vCenter, la connexion n’est pas sécurisée. L’illustration suivante montre une connexion non sécurisée.

![exemple de connexion non sécurisée à un serveur VMware](./media/backup-azure-backup-server-vmware/unsecure-url.png)

Pour résoudre ce problème, et créer une connexion sécurisée, téléchargez les certificats approuvés de l’autorité de certification racine.

1. Dans le navigateur sur le serveur de sauvegarde Azure, tapez l’URL du client web vSphere.

  La page de connexion du client web vSphere s’affiche.

  ![client web vsphere](./media/backup-azure-backup-server-vmware/vsphere-web-client.png)

  En bas des informations destinées aux administrateurs et aux développeurs se trouve le lien vers **Télécharger les certificats approuvés de l’autorité de certification racine**.

  ![lien de téléchargement des certificats approuvés de l’autorité de certification racine](./media/backup-azure-backup-server-vmware/vmware-download-ca-cert-prompt.png)

  Si la page de connexion au client web vSphere n’apparaît pas, vérifiez les paramètres de proxy de votre navigateur.

2. Cliquez sur **Télécharger les certificats approuvés de l’autorité de certification racine**.

  Le serveur vCenter télécharge un fichier sur votre ordinateur local. Le nom du fichier est **download**. En fonction de votre navigateur, vous recevez un message vous invitant à ouvrir ou enregistrer le fichier.

  ![message de téléchargement lorsque les certificats sont téléchargés](./media/backup-azure-backup-server-vmware/download-certs.png)

3. Enregistrez le fichier dans un emplacement du serveur de sauvegarde Azure. Lorsque vous enregistrez le fichier, ajoutez l’extension de nom de fichier .zip.

  Le fichier est un fichier .zip qui contient des informations sur les certificats. L’ajout de l’extension .zip vous permet d’utiliser des outils d’extraction.

4. Cliquez avec le bouton droit sur **download.zip** et sélectionnez Extraire tout pour extraire le contenu.

  Le fichier compressé extrait son contenu dans un dossier nommé **certs**. Deux types de fichiers figurent dans ce dossier. Le fichier de certificat racine possède l’extension .0, .1 ou .*nombre*. Le fichier CRL possède une extension qui commence par .r0, .r1 et ainsi de suite. Le fichier CRL est associé à un certificat.

  ![fichier de téléchargement extrait localement ](./media/backup-azure-backup-server-vmware/extracted-files-in-certs-folder.png)

5. Dans le dossier **certs**, cliquez avec le bouton droit sur le fichier de certificat racine et sélectionnez **Renommer**.

  ![renommer un certificat racine ](./media/backup-azure-backup-server-vmware/rename-cert.png)

  Remplacez l’extension du certificat racine par .crt. Lorsqu’un message vous demande si vous êtes sûr de vouloir modifier l’extension (car cela pourrait modifier la fonction du fichier), cliquez sur Oui ou OK. L’icône du fichier devient celle d’un certificat racine.

6. Cliquez sur le certificat racine et dans le menu contextuel, sélectionnez **Installer le certificat**.

  L’Assistant Importation de certificat s’ouvre.

7. Dans l’Assistant Importation de certificat, sélectionnez **Ordinateur Local** comme destination du certificat, cliquez sur **Suivant** pour continuer.

  ![boîte de dialogue Certificat avec message d’erreur ](./media/backup-azure-backup-server-vmware/certificate-import-wizard1.png)

  Si un message vous demande si vous voulez autoriser les modifications sur cet ordinateur, cliquez sur Oui ou OK, pour toutes les modifications.

8. Sur l’écran Magasin de certificats, sélectionnez **Placer tous les certificats dans le magasin suivant**, puis cliquez sur **Parcourir** pour choisir un magasin.

  ![boîte de dialogue Certificat avec message d’erreur ](./media/backup-azure-backup-server-vmware/cert-import-wizard-local-store.png)

  La boîte de dialogue Sélectionner un magasin de certificats s’ouvre.

  ![boîte de dialogue Certificat avec message d’erreur ](./media/backup-azure-backup-server-vmware/cert-store.png)

9. Sélectionnez **Autorités de certification racines de confiance** comme dossier de destination pour les certificats, puis cliquez sur **OK**.

  ![boîte de dialogue Certificat avec message d’erreur ](./media/backup-azure-backup-server-vmware/certificate-store-selected.png)

  Le magasin de certificats choisi apparaît dans l’**Assistant Importation de certificat**. Cliquez sur **Suivant**.

  ![boîte de dialogue Certificat avec message d’erreur ](./media/backup-azure-backup-server-vmware/certificate-import-wizard2.png)

10. Sur l’écran de fin de l’Assistant Importation de certificat, vérifiez que le certificat se trouve dans la dossier souhaité, puis cliquez sur **Terminer** pour terminer la procédure.

  ![boîte de dialogue Certificat avec message d’erreur ](./media/backup-azure-backup-server-vmware/cert-wizard-final-screen.png)

  Une boîte de dialogue s’affiche et vous indique si l’importation a réussi.

11. Connectez-vous au serveur vCenter pour vérifier que votre connexion est sécurisée.

  Si vous ne parvenez pas à importer le certificat et à établir une connexion sécurisée, consultez la documentation sur VMware vSphere dans [Obtention de certificats de serveur](http://pubs.vmware.com/vsphere-60/index.jsp#com.vmware.wssdk.dsg.doc/sdk_sg_server_certificate_Appendixes.6.4.html).

  Si vous avez des limites sécurisées au sein de votre organisation et que vous ne souhaitez pas activer le protocole HTTPs, désactivez la communication sécurisée en procédant comme suit.

### <a name="disable-secure-communication-protocol"></a>Désactiver le protocole de communication sécurisée

Si votre organisation n’a pas besoin d’un protocole de communication sécurisée (HTTPS), désactivez-le en procédant comme suit. Pour désactiver le comportement par défaut, créez une clé de Registre qui ignore le comportement par défaut.

1. Copiez et collez le texte suivant dans un fichier .txt.

  ```
Windows Registry Editor Version 5.00
[HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft Data Protection Manager\VMWare]
"IgnoreCertificateValidation"=dword:00000001
```
2. Enregistrez le fichier sous le nom **DisableSecureAuthentication.reg** sur votre serveur de sauvegarde Azure.

3. Double-cliquez sur le fichier pour activer l’entrée de Registre.


## <a name="create-a-role-and-user-account-on-the-vcenter-server"></a>Créer un rôle et un compte d’utilisateur sur le serveur vCenter

Sur le serveur vCenter, un rôle est un ensemble de privilèges prédéfini. Un administrateur du serveur vCenter crée les rôles et associe les comptes d’utilisateur aux rôles pour attribuer des autorisations. Pour définir les informations d’identification d’utilisateur nécessaires pour sauvegarder le serveur vCenter, créez un rôle avec des privilèges spécifiques et associez le compte d’utilisateur au rôle.

Le serveur de sauvegarde Azure utilise un nom d’utilisateur et un mot de passe pour s’authentifier auprès du serveur vCenter. Le serveur de sauvegarde Azure utilise ces informations d’identification comme authentification pour toutes les opérations de sauvegarde.

Pour ajouter un rôle de serveur vCenter et des privilèges pour un administrateur de sauvegarde :

1. Connectez-vous au serveur vCenter et dans le **Navigateur**, cliquez sur **Administration**.

  ![boîte de dialogue Certificat avec message d’erreur ](./media/backup-azure-backup-server-vmware/vmware-navigator-panel.png)

2. Dans la section **Administration**, sélectionnez **Rôles** puis, dans le panneau **Rôles**, cliquez sur l’icône Ajouter un rôle (symbole +).

  ![boîte de dialogue Certificat avec message d’erreur ](./media/backup-azure-backup-server-vmware/vmware-define-new-role.png)

  La boîte de dialogue **Créer un rôle** s’ouvre.

  ![boîte de dialogue Certificat avec message d’erreur ](./media/backup-azure-backup-server-vmware/vmware-define-new-role-priv.png)

3. Dans la boîte de dialogue **Créer un rôle**, dans le champ **Nom du rôle**, entrez *BackupAdminRole*. Vous pouvez choisir n’importe quel nom de rôle, à condition qu’il permettre de reconnaître l’objectif du rôle.

4. Sélectionnez les privilèges pour la version appropriée de vCenter, puis cliquez sur **OK**. Le tableau suivant identifie les privilèges requis pour le serveur vCenter versions 6.0 et 5.5.

  Lorsque vous sélectionnez les privilèges, cliquez sur le chevron de l’étiquette du parent pour développer le parent et afficher les privilèges enfant. La sélection des privilèges VirtualMachine nécessaires requiert de descendre plusieurs niveaux. Vous n’avez pas besoin de sélectionner tous les privilèges enfant au sein d’un privilège parent.

  ![boîte de dialogue Certificat avec message d’erreur ](./media/backup-azure-backup-server-vmware/cert-add-privilege-expand.png)

  Après avoir cliqué sur **OK**, le nouveau rôle s’affiche dans la liste du panneau Rôles.

|Privilèges de vCenter 6.0| Privilèges de vCenter 5.5|
|--------------------------|---------------------------|
|Datastore.AllocateSpace   | Datastore.AllocateSpace|
|Global.ManageCustomFields | Global.ManageCustomerFields|
|Global.SetCustomFields    |   |
|Host.Local.CreateVM       | Network.Assign |
|Network.Assign            |  |
|Resource.AssignVMToPool   |  |
|VirtualMachine.Config.AddNewDisk  | VirtualMachine.Config.AddNewDisk   |
|VirtualMachine.Config.AdvanceConfig| VirtualMachine.Config.AdvancedConfig|
|VirtualMachine.Config.ChangeTracking| VirtualMachine.Config.ChangeTracking |
|VirtualMachine.Config.HostUSBDevice||
|VirtualMachine.Config.QueryUnownedFiles|    |
|VirtualMachine.Config.SwapPlacement| VirtualMachine.Config.SwapPlacement |
|VirtualMachine.Interact.PowerOff| VirtualMachine.Interact.PowerOff |
|VirtualMachine.Inventory.Create| VirtualMachine.Inventory.Create |
|VirtualMachine.Provisioning.DiskRandomAccess| |
|VirtualMachine.Provisioning.DiskRandomRead|VirtualMachine.Provisioning.DiskRandomRead |
|VirtualMachine.State.CreateSnapshot| VirtualMachine.State.CreateSnapshot|
|VirtualMachine.State.RemoveSnapshot|VirtualMachine.State.RemoveSnapshot |
</br>



## <a name="create-vcenter-server-user-account-and-permissions"></a>Créer un compte d’utilisateur et des autorisations de serveur vCenter

Une fois que le rôle avec privilèges existe, créez un compte d’utilisateur. Le compte d’utilisateur possède un nom et un mot de passe, qui représentent les informations d’identification utilisées pour s’authentifier.

1. Pour créer un compte d’utilisateur, dans le navigateur du serveur vCenter, cliquez sur **Utilisateurs et groupes**.

  ![boîte de dialogue Certificat avec message d’erreur ](./media/backup-azure-backup-server-vmware/vmware-userandgroup-panel.png)

  Le panneau Utilisateurs et groupes s’affiche.

  ![boîte de dialogue Certificat avec message d’erreur ](./media/backup-azure-backup-server-vmware/usersandgroups.png)

2. Dans le panneau Utilisateurs et groupes vCenter, sous l’onglet **Utilisateurs**, cliquez sur l’icône Ajouter des utilisateurs (symbole +).

  La boîte de dialogue Nouvel utilisateur s’ouvre.

3. Dans la boîte de dialogue Nouvel utilisateur, remplissez les champs et cliquez sur **OK**. Dans cet exemple, tapez **BackupAdmin** pour le nom d’utilisateur. Créez un mot de passe fort.

  ![boîte de dialogue Certificat avec message d’erreur ](./media/backup-azure-backup-server-vmware/vmware-new-user-account.png)

  Le nouveau compte d’utilisateur s’affiche dans la liste.

4. Pour associer le compte d’utilisateur au rôle, dans le navigateur, cliquez sur **Autorisations globales**. Dans le panneau Autorisations globales, sous l’onglet **Gérer**, cliquez sur l’icône Ajouter (symbole +).

  ![boîte de dialogue Certificat avec message d’erreur ](./media/backup-azure-backup-server-vmware/vmware-add-new-perms.png)

  La boîte de dialogue Global Permissions Root - Add Permission (Racine des autorisations globales - Ajouter une autorisation) s’ouvre.

5. Dans la boîte de dialogue **Global Permissions Root - Add Permission** (Racine des autorisations globales - Ajouter une autorisation), cliquez sur **Ajouter** pour choisir l’utilisateur ou le groupe.

  ![boîte de dialogue Certificat avec message d’erreur ](./media/backup-azure-backup-server-vmware/vmware-add-new-global-perm.png)

  La boîte de dialogue Sélectionner des utilisateurs/groupes s’ouvre.

6. Dans la boîte de dialogue **Sélectionner des utilisateurs/groupes**, sélectionnez **BackupAdmin** et cliquez sur **Ajouter**.

  Le compte d’utilisateur figurant dans le champ Utilisateurs est au format *domaine*`\`*nom d’utilisateur*. Si vous souhaitez utiliser un autre domaine, sélectionnez-le dans la liste des domaines.

  ![boîte de dialogue Certificat avec message d’erreur ](./media/backup-azure-backup-server-vmware/vmware-assign-account-to-role.png)

  Cliquez sur **OK** pour ajouter les utilisateurs sélectionnés dans la boîte de dialogue Ajouter une autorisation.

7. Maintenant que vous avez identifié l’utilisateur, vous devez lui affecter le rôle. Dans la zone Rôle attribué, dans le menu déroulant, sélectionnez le rôle **BackupAdminRole** et cliquez sur **OK**.

  ![boîte de dialogue Certificat avec message d’erreur ](./media/backup-azure-backup-server-vmware/vmware-choose-role.png)

  Sous l’onglet Gérer de la boîte de dialogue des autorisations globales, le nouveau compte d’utilisateur et le rôle associé apparaissent dans la liste.


## <a name="establish-vcenter-server-credentials-on-azure-backup-server"></a>Définir les informations d’identification de serveur vCenter sur le serveur de sauvegarde Azure

Avant d’ajouter le serveur VMware au serveur de sauvegarde Azure, installez la [mise à jour 1 du serveur de sauvegarde Microsoft Azure](https://support.microsoft.com/help/3175529/update-1-for-microsoft-azure-backup-server).

1. Pour ouvrir le serveur de sauvegarde Azure, double-cliquez sur l’icône présente sur le Bureau du serveur de sauvegarde Azure.

  ![Icône du serveur de sauvegarde Azure](./media/backup-azure-backup-server-vmware/mabs-icon.png)

  Si vous ne trouvez pas l’icône sur le Bureau, ouvrez le serveur de sauvegarde Azure dans la liste des applications installées. Le nom de l’application du serveur de sauvegarde Azure est Microsoft Azure Backup.

2. Dans la console du serveur de sauvegarde Azure, cliquez sur **Gestion** et sur **Serveurs de Production**, puis dans la barre d’outils, cliquez sur **Gérer VMware**.

  ![Console MABS](./media/backup-azure-backup-server-vmware/add-vmware-credentials.png)

  La boîte de dialogue Gérer les informations d’identification s’ouvre.

  ![Boîte de dialogue Gérer les informations d’identification de la console MABS](./media/backup-azure-backup-server-vmware/mabs-manage-credentials-dialog.png)

3. Dans la boîte de dialogue Gérer les informations d’identification, cliquez sur **Ajouter** pour ouvrir la boîte de dialogue Ajouter des informations d’identification.

4. Dans la boîte de dialogue Ajouter des informations d’identification, entrez un nom et une description pour les nouvelles informations d’identification. Indiquez ensuite le nom d’utilisateur et le mot de passe. Le nom des informations d’identification, *Contoso Vcenter credential* dans l’exemple, vous permet de les identifier dans la procédure suivante. Utilisez la même combinaison nom d’utilisateur-mot de passe que pour le serveur vCenter. Si le serveur vCenter et le serveur de sauvegarde Azure ne se trouvent pas sur le même domaine, spécifiez le domaine dans le nom d’utilisateur.

  ![Boîte de dialogue Gérer les informations d’identification de la console MABS](./media/backup-azure-backup-server-vmware/mabs-add-credential-dialog2.png)

  Cliquez sur **Ajouter** pour ajouter les nouvelles informations d’identification au serveur de sauvegarde Azure. Les nouvelles informations d’identification s’affichent dans la liste de la boîte de dialogue Gérer les informations d’identification.
  ![Boîte de dialogue Gérer les informations d’identification de la console MABS](./media/backup-azure-backup-server-vmware/new-list-of-mabs-creds.png)

5. Pour fermer la boîte de dialogue Gérer les informations d’identification, cliquez sur le symbole **X** en haut à droite.


## <a name="add-the-vcenter-server-to-azure-backup-server"></a>Ajouter le serveur vCenter au serveur de sauvegarde Azure

Pour ouvrir l’Assistant Ajout d’un serveur de production

1. Dans la console du serveur de sauvegarde Azure, cliquez sur **Gestion** et sur **Serveurs de Production**, puis cliquez sur **Ajouter**.

  ![Assistant Ajout d’un serveur de production](./media/backup-azure-backup-server-vmware/add-vcenter-to-mabs.png)

  L’Assistant Ajout d’un serveur de production s’ouvre.

  ![Assistant Ajout d’un serveur de production](./media/backup-azure-backup-server-vmware/production-server-add-wizard.png)

2. Dans l’écran Sélectionner un type de serveur de protection, sélectionnez Serveurs VMware, puis cliquez sur **Suivant**.

3. Dans Nom du serveur/Adresse IP, spécifiez le nom de domaine complet (FQDN) ou l’adresse IP du serveur VMware. Vous pouvez utiliser le nom vCenter si tous les serveurs ESXi sont gérés par le même serveur vCenter.

  ![Assistant Ajout d’un serveur de production](./media/backup-azure-backup-server-vmware/add-vmware-server-provide-server-name.png)

4. Dans la boîte de dialogue **Port SSL**, entrez le port utilisé pour communiquer avec le serveur VMware. Utilisez le port 443 (le port par défaut), sauf si vous savez que vous avez besoin d’un autre port.

5. Dans la boîte de dialogue **Spécifier des informations d’identification**, sélectionnez les informations d’identification que vous avez créées.

  ![Assistant Ajout d’un serveur de production](./media/backup-azure-backup-server-vmware/identify-creds.png)

6. Cliquez sur **Ajouter** pour ajouter le serveur VMware à la liste des **serveurs VMware ajoutés**, puis cliquez sur **Suivant** pour passer à l’écran suivant de l’Assistant.

  ![Assistant Ajout d’un serveur de production](./media/backup-azure-backup-server-vmware/add-vmware-server-credentials.png)

7. Dans l’écran **Tâches**, cliquez sur **Ajouter** pour ajouter le serveur VMware spécifié au serveur de sauvegarde Azure.

  ![Assistant Ajout d’un serveur de production](./media/backup-azure-backup-server-vmware/tasks-screen.png)

  Étant donné que la sauvegarde du serveur VMware est une sauvegarde sans agent, l’ajout du nouveau serveur s’effectue en quelques secondes. L’écran Terminer affiche le résultat.

  ![Assistant Ajout d’un serveur de production](./media/backup-azure-backup-server-vmware/summary-screen.png)

  Pour ajouter plusieurs serveurs vCenter au serveur de sauvegarde Azure, répétez les étapes précédentes de cette section.

Après avoir ajouté le serveur vCenter au serveur de sauvegarde Azure, l’étape suivante consiste à créer un groupe de protection. Le groupe de protection spécifie les différentes informations de rétention à court ou à long terme, et vous permet de définir et appliquer la stratégie de sauvegarde. La stratégie de sauvegarde permet de planifier les dates d’exécution des sauvegardes ainsi que les éléments à sauvegarder.


## <a name="configure-a-protection-group"></a>Configurer un groupe de protection

Si vous n’avez pas encore utilisé System Center Data Protection Manager ou le serveur de sauvegarde Azure, consultez la rubrique [Planification de sauvegardes sur disque](https://technet.microsoft.com/library/hh758026.aspx) pour préparer votre environnement matériel. Une fois que vous avez vérifié que vous disposez du stockage approprié, utilisez l’Assistant Création d’un nouveau groupe de protection pour ajouter des machines virtuelles VMware.

1. Dans la console du serveur de sauvegarde Azure, cliquez sur **Protection**, puis cliquez sur **Nouveau** dans la barre d’outils pour ouvrir l’assistant Création d’un nouveau groupe de protection.

  ![Assistant Ajout d’un serveur de production](./media/backup-azure-backup-server-vmware/open-protection-wizard.png)

  L’Assistant Création d’un nouveau groupe de protection s’ouvre.

  ![Assistant Ajout d’un serveur de production](./media/backup-azure-backup-server-vmware/protection-wizard.png)

  Cliquez sur **Suivant** pour passer à l’écran **Sélectionner le type de groupe de protection**.

2. Dans l’écran Sélectionner le type de groupe de protection, sélectionnez **Serveurs**, puis cliquez sur **Suivant**.

3. Dans l’écran Sélectionner les membres du groupe, vous pouvez voir les membres disponibles ainsi que les membres qui ont été sélectionnés. Sélectionnez les membres que vous souhaitez protéger, puis cliquez sur **Suivant**.

  ![Assistant Ajout d’un serveur de production](./media/backup-azure-backup-server-vmware/server-add-selected-members.png)

  Lorsque vous sélectionnez un membre, si vous sélectionnez un dossier qui contient d’autres dossiers ou machines virtuelles, ces dossiers et machines virtuelles sont également sélectionnés. On appelle « protection au niveau du dossier » le fait d’inclure des dossiers et des machines virtuelles dans le dossier parent. Vous pouvez exclure n’importe quel dossier ou machine virtuelle en désélectionnant la case à cocher.

  Si une machine virtuelle ou un dossier contenant une machine virtuelle est déjà protégé(e) dans Azure, vous ne pouvez pas sélectionner de nouveau cette machine virtuelle. Autrement dit, une fois qu’une machine virtuelle est protégée dans Azure, elle ne peut pas être de nouveau protégée, afin d’éviter que des points de récupération en double ne soient créés pour une même machine virtuelle. Si vous souhaitez connaître le serveur de sauvegarde Azure qui protège déjà un membre, pointez votre souris sur le membre pour visualiser le nom du serveur de protection.

4. Dans l’écran Sélectionner la méthode de protection des données, entrez le nom du groupe de protection. Les options Protection à court terme (sur disque) et Protection en ligne sont sélectionnées. Si vous souhaitez utiliser la protection en ligne (sur Azure), vous devez utiliser la protection à court terme sur disque. Cliquez sur **Suivant** pour passer à la plage de protection à court terme.

  ![Assistant Ajout d’un serveur de production](./media/backup-azure-backup-server-vmware/name-protection-group.png)

5. Dans l’écran Spécifier les objectifs à court terme, dans le champ **Durée de rétention**, spécifiez le nombre de jours pendant lesquels vous souhaitez conserver les points de récupération *stockés sur le disque*. Si vous souhaitez modifier l’heure et les jours de création des points de récupération, cliquez sur **Modifier**. Les points de récupération à court terme sont des sauvegardes complètes. Ils ne fonctionnent pas comme des sauvegardes incrémentielles. Après avoir défini vos objectifs à court terme, cliquez sur **Suivant**.

  ![Assistant Ajout d’un serveur de production](./media/backup-azure-backup-server-vmware/short-term-goals.png)

6. Dans l’écran Vérifier l’allocation de disque, passez en revue les informations et modifiez l’espace disque pour les machines virtuelles si nécessaire. Les allocations de disques recommandées dépendent de la durée de rétention spécifiée dans l’écran précédent, du type de charge de travail et de la taille des données protégées (identifiées à l’étape 3).  

  - Taille des données : taille des données dans le groupe de protection.
  - Espace disque : quantité d’espace disque recommandée pour le groupe de protection. Si vous souhaitez modifier ce paramètre, vous devez allouer un espace total légèrement supérieur au volume de croissance estimé pour chaque source de données.
  - Colocaliser les données : si vous activez la colocalisation, plusieurs sources de données dans la protection peuvent être mappées à un seul réplica et à un seul volume de points de récupération. La colocalisation n’est pas prise en charge pour toutes les charges de travail.
  - Augmenter automatiquement : si vous activez ce paramètre et si les données contenues dans le groupe protégé dépassent l’allocation initiale, DPM tentera d’augmenter la taille du disque de 25 %.
  - Détails de pool de stockage : affiche l’état actuel du pool de stockage, notamment la taille totale et la taille de disque restante.

  ![Assistant Ajout d’un serveur de production](./media/backup-azure-backup-server-vmware/review-disk-allocation.png)

  Après avoir défini l’allocation d’espace, cliquez sur **Suivant**.

7. Dans l’écran Choisir la méthode de création de réplica, indiquez de quelle manière vous souhaitez générer la copie initiale, ou le réplica, des données protégées sur le serveur de sauvegarde Azure.

  Les valeurs **Automatiquement sur le réseau** et **Maintenant** sont définies par défaut. Si vous utilisez la valeur par défaut, nous vous recommandons de spécifier une heure creuse. Choisissez **Ultérieurement** et spécifiez le jour et l’heure.

  Pour de grandes quantités de données ou des conditions de réseau peu optimales, envisagez de répliquer les données hors connexion à l’aide d’un support amovible.

  Une fois que vous avez effectué vos sélections, cliquez sur **suivant**.

  ![Assistant Création d’un nouveau groupe de protection](./media/backup-azure-backup-server-vmware/replica-creation.png)

8. Dans l’écran **Options de vérification de cohérence**, indiquez comment et quand automatiser les vérifications de cohérence. Vous pouvez exécuter les vérifications de cohérence lorsque les données du réplica deviennent incohérentes, ou selon une planification définie.

  Si vous ne voulez pas configurer la vérification de cohérence automatique, vous pouvez exécuter une vérification manuelle. Dans la zone Protection de la console du serveur de sauvegarde Azure, cliquez avec le bouton droit sur le groupe de protection et sélectionnez **Effectuer une vérification de cohérence**.

  Cliquez sur **Suivant** pour passer à l’écran suivant.

9. Dans l’écran **Indiquer les données de protection en ligne**, sélectionnez la ou les sources de données que vous souhaitez protéger. Vous pouvez sélectionner les membres un à un ou cliquer sur **Sélectionner tout** pour choisir tous les membres. Une fois que vous avez sélectionné les membres, cliquez sur **Suivant**.

  ![Assistant Création d’un nouveau groupe de protection](./media/backup-azure-backup-server-vmware/select-data-to-protect.png)

10. Dans l’écran **Spécifier une planification de sauvegarde en ligne**, définissez la planification qui vous permettra de générer des points de récupération à partir de la sauvegarde sur disque. Une fois le point de récupération généré, il est transféré vers le coffre Recovery Services dans Azure. Lorsque vous avez fini de paramétrer la planification de sauvegarde en ligne, cliquez sur **Suivant**.

  ![Assistant Création d’un nouveau groupe de protection](./media/backup-azure-backup-server-vmware/online-backup-schedule.png)

11. Dans l’écran Spécifier une stratégie de rétention en ligne, indiquez la durée pendant laquelle vous souhaitez conserver les données de sauvegarde dans Azure. Une fois la stratégie définie, cliquez sur **Suivant**.

  ![Assistant Création d’un nouveau groupe de protection](./media/backup-azure-backup-server-vmware/retention-policy.png)

  La durée de conservation des données dans Azure n’est soumise à aucune restriction. Lorsque vous stockez les données de point de récupération dans Azure, la seule limite que vous devez respecter est que vous ne pouvez pas avoir plus de 9 999 points de récupération par instance protégée. Dans cet exemple, l’instance protégée est le serveur VMware.

12. Dans l’écran Résumé, passez en revue les détails de votre groupe de protection. Notez les membres du groupe et les paramètres. Si les paramètres vous conviennent, cliquez sur **Créer un groupe**.

  ![Assistant Création d’un nouveau groupe de protection](./media/backup-azure-backup-server-vmware/protection-group-summary.png)

## <a name="next-steps"></a>Étapes suivantes
Si vous utilisez le serveur de sauvegarde Azure pour protéger vos charges de travail VMware, vous trouverez peut-être utile d’utiliser le serveur de sauvegarde Azure pour protéger un [serveur Microsoft Exchange](./backup-azure-exchange-mabs.md), une [batterie de serveurs Microsoft SharePoint](./backup-azure-backup-sharepoint-mabs.md) ou un [SQL Server](./backup-azure-sql-mabs.md).

Consultez la page [Résoudre les problèmes d’un serveur de sauvegarde Azure](./backup-azure-mabs-troubleshoot.md) pour plus d’informations sur les problèmes d’inscription de l’agent, sur la configuration du groupe de protection et sur les problèmes liés aux travaux de sauvegarde.

