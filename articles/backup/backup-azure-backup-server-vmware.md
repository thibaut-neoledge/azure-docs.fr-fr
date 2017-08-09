---
title: Sauvegarder des serveurs VMware avec le serveur de sauvegarde Azure | Microsoft Docs
description: "Utilisez le serveur de sauvegarde Azure pour sauvegarder des serveurs VMware vCenter et VMware ESXi dans Azure ou sur un disque. Cet article fournit des instructions étape par étape pour sauvegarder (ou protéger) vos charges de travail VMware."
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
ms.date: 07/24/2017
ms.author: markgal;
ms.translationtype: HT
ms.sourcegitcommit: 141270c353d3fe7341dfad890162ed74495d48ac
ms.openlocfilehash: ad331dffb7c31d12290f4223967c568e4535fe3c
ms.contentlocale: fr-fr
ms.lasthandoff: 07/25/2017

---
# <a name="back-up-a-vmware-server-to-azure"></a>Sauvegarder un serveur VMware dans Azure

Cet article explique comment configurer un serveur de sauvegarde Azure pour contribuer à la protection des charges de travail de serveur VMware. Cet article suppose que vous avez déjà installé le serveur de sauvegarde Azure. Si vous n’avez pas installé de serveur de sauvegarde Azure, voir [Préparer la sauvegarde des charges de travail à l’aide du serveur de sauvegarde Azure](backup-azure-microsoft-azure-backup.md).

Un serveur de sauvegarde Azure peut sauvegarder ou aider à protéger les versions 6.5, 6.0 et 5.5 de VMware vCenter Server.


## <a name="create-a-secure-connection-to-the-vcenter-server"></a>Créer une connexion sécurisée au serveur vCenter

Par défaut, le serveur de sauvegarde Azure communique avec chaque serveur vCenter via un canal HTTPS. Pour activer la communication sécurisée, nous vous recommandons d’installer le certificat de l’autorité de certification VMware sur le serveur de sauvegarde Azure. Si vous n’avez pas besoin de communication sécurisée et préférez désactiver l’obligation d’utiliser le protocole HTTPS, voir [Désactiver le protocole de communication sécurisée](backup-azure-backup-server-vmware.md#disable-secure-communication-protocol). Pour créer une connexion sécurisée entre le serveur de sauvegarde Azure et le serveur vCenter, importez le certificat approuvé sur le serveur de sauvegarde Azure.

En général, vous utilisez un navigateur sur le serveur de sauvegarde Azure pour vous connecter au serveur vCenter via le client web vSphere. La première fois que vous utilisez le navigateur du serveur de sauvegarde Azure pour vous connecter au serveur vCenter, la connexion n’est pas sécurisée. L’illustration suivante montre une connexion non sécurisée.

![Exemple de connexion non sécurisée à un serveur VMware](./media/backup-azure-backup-server-vmware/unsecure-url.png)

Pour résoudre ce problème, et créer une connexion sécurisée, téléchargez les certificats approuvés de l’autorité de certification racine.

1. Dans le navigateur sur le serveur de sauvegarde Azure, entrez l’URL du client web vSphere. La page de connexion du client web vSphere s’affiche.

    ![Client web vSphere](./media/backup-azure-backup-server-vmware/vsphere-web-client.png)

    En bas des informations destinées aux administrateurs et aux développeurs se trouve le lien **Télécharger les certificats d’autorités de certification racines de confiance**.

    ![Lien de téléchargement des certificats d’autorités de certification racines de confiance](./media/backup-azure-backup-server-vmware/vmware-download-ca-cert-prompt.png)

  Si la page de connexion au client web vSphere n’apparaît pas, vérifiez les paramètres de proxy de votre navigateur.

2. Cliquez sur **Télécharger les certificats approuvés de l’autorité de certification racine**.

    Le serveur vCenter télécharge un fichier sur votre ordinateur local. Le nom du fichier est **download**. En fonction de votre navigateur, vous recevez un message vous demandant d’ouvrir ou d’enregistrer le fichier.

    ![message de téléchargement lorsque les certificats sont téléchargés](./media/backup-azure-backup-server-vmware/download-certs.png)

3. Enregistrez le fichier à un emplacement du serveur de sauvegarde Azure. Lorsque vous enregistrez le fichier, ajoutez l’extension de nom de fichier .zip.

    Le fichier est un fichier .zip qui contient des informations sur les certificats. Avec l’extension .zip, vous pouvez utiliser les outils d’extraction.

4. Cliquez avec le bouton droit sur **download.zip**, puis sélectionnez **Extraire tout** pour extraire le contenu.

    Le fichier .zip extrait son contenu dans un dossier nommé **certs**. Deux types de fichiers figurent dans ce dossier. Le fichier de certificat racine est doté d’une extension qui commence par une séquence numérotée comme .0 et .1.
    
    Le fichier CRL est doté d’une extension qui commence par une séquence comme .r0 ou .r1. Le fichier CRL est associé à un certificat.

    ![Fichier de téléchargement extrait localement ](./media/backup-azure-backup-server-vmware/extracted-files-in-certs-folder.png)

5. Dans le dossier **certs**, cliquez avec le bouton droit sur le fichier de certificat racine, puis cliquez sur **Renommer**.

    ![Renommer un certificat racine ](./media/backup-azure-backup-server-vmware/rename-cert.png)

    Remplacez l’extension du certificat racine par .crt. Lorsqu’un message s’affiche vous demandant si vous voulez vraiment modifier l’extension, cliquez sur **Oui** ou sur **OK**. Dans le cas contraire, vous modifiez la fonction du fichier. L’icône du fichier est remplacée par une icône représentant un certificat racine.

6. Cliquez sur le certificat racine et dans le menu contextuel, sélectionnez **Installer le certificat**.

    La boîte de dialogue **Assistant Importation de certificat** s’ouvre.

7. Dans la boîte de dialogue **Assistant Importation de certificat**, sélectionnez **Ordinateur Local** comme destination du certificat, puis cliquez sur **Suivant** pour continuer.

    ![Options de destination de stockage du certificat ](./media/backup-azure-backup-server-vmware/certificate-import-wizard1.png)

    Si un message vous demande si vous voulez autoriser les modifications sur cet ordinateur, cliquez sur **Oui** ou sur **OK**, pour toutes les modifications.

8. Dans la page **Magasin de certificats**, sélectionnez **Placer tous les certificats dans le magasin suivant**, puis cliquez sur **Parcourir** pour choisir un magasin.

    ![Placer des certificats dans une zone de stockage spécifique](./media/backup-azure-backup-server-vmware/cert-import-wizard-local-store.png)

    La boîte de dialogue **Sélectionner un magasin de certificats** s’affiche.

    ![Arborescence des dossiers de stockage de certificats](./media/backup-azure-backup-server-vmware/cert-store.png)

9. Sélectionnez **Autorités de certification racines de confiance** comme dossier de destination des certificats, puis cliquez sur **OK**.

    ![Dossier de destination des certificats](./media/backup-azure-backup-server-vmware/certificate-store-selected.png)

    Le dossier **Autorités de certification racines de confiance**  est confirmé comme magasin de certificats. Cliquez sur **Suivant**.

    ![Dossier de magasin de certificats](./media/backup-azure-backup-server-vmware/certificate-import-wizard2.png)

10. Dans la page **Fin de l’Assistant Importation du certificat**, vérifiez que le certificat se trouve dans le dossier souhaité, puis cliquez sur **Terminer**.

    ![Vérifier que le certificat se trouve dans le dossier approprié](./media/backup-azure-backup-server-vmware/cert-wizard-final-screen.png)

    Une boîte de dialogue s’affiche, confirmant l’importation réussie du certificat.

11. Connectez-vous au serveur vCenter pour vérifier que votre connexion est sécurisée.

  Si l’importation du certificat échoue et si vous ne parvenez pas à établir une connexion sécurisée, consultez la documentation sur VMware vSphere dans [Obtaining server certificates (Obtention de certificats de serveur)](http://pubs.vmware.com/vsphere-60/index.jsp#com.vmware.wssdk.dsg.doc/sdk_sg_server_certificate_Appendixes.6.4.html).

  Si votre organisation comprend des limites sécurisées et que vous ne souhaitez pas activer le protocole HTTPS, désactivez la communication sécurisée en procédant comme suit.

### <a name="disable-secure-communication-protocol"></a>Désactiver le protocole de communication sécurisée

Si votre organisation n’a pas besoin d’un protocole HTTPS, désactivez-le en procédant comme suit. Pour désactiver le comportement par défaut, créez une clé de Registre qui ignore le comportement par défaut.

1. Copiez et collez le texte suivant dans un fichier .txt.

  ```
  Windows Registry Editor Version 5.00
  [HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft Data Protection Manager\VMWare]
  "IgnoreCertificateValidation"=dword:00000001
  ```

2. Enregistrez le fichier sur votre ordinateur serveur de sauvegarde Azure. Utilisez DisableSecureAuthentication.reg comme nom de fichier.

3. Double-cliquez sur le fichier pour activer l’entrée de Registre.


## <a name="create-a-role-and-user-account-on-the-vcenter-server"></a>Créer un rôle et un compte d’utilisateur sur le serveur vCenter

Sur le serveur vCenter, un rôle est un ensemble de privilèges prédéfini. Un administrateur de serveur vCenter crée les rôles. Pour attribuer des autorisations, l’administrateur associe des comptes d’utilisateur à un rôle. Pour définir les informations d’identification d’utilisateur nécessaires pour sauvegarder l’ordinateur serveur vCenter, créez un rôle pourvu de privilèges spécifiques, puis associez le compte d’utilisateur au rôle.

Le serveur de sauvegarde Azure utilise un nom d’utilisateur et un mot de passe pour s’authentifier auprès du serveur vCenter. Le serveur de sauvegarde Azure utilise ces informations d’identification comme authentification pour toutes les opérations de sauvegarde.

Pour ajouter un rôle de serveur vCenter et ses privilèges pour un administrateur de sauvegarde :

1. Connectez-vous au serveur vCenter, puis, dans le panneau **Navigateur** du serveur vCenter, cliquez sur **Administration**.

    ![Option d’administration dans le panneau Navigateur du serveur vCenter](./media/backup-azure-backup-server-vmware/vmware-navigator-panel.png)

2. Dans **Administration**, sélectionnez **Rôles**, puis, dans le panneau **Rôles**, cliquez sur l’icône d’ajout de rôle (symbole +).

    ![Ajouter un rôle](./media/backup-azure-backup-server-vmware/vmware-define-new-role.png)

    La boîte de dialogue **Créer un rôle** s’affiche.

    ![Créer un rôle](./media/backup-azure-backup-server-vmware/vmware-define-new-role-priv.png)

3. Dans la boîte de dialogue **Créer un rôle**, dans la zone **Nom du rôle**, entrez *BackupAdminRole*. Vous pouvez choisir n’importe quel nom de rôle, à condition qu’il permettre de reconnaître l’objectif du rôle.

4. Sélectionnez les privilèges correspondant à la version appropriée de vCenter, puis cliquez sur **OK**. Le tableau suivant identifie les privilèges requis pour vCenter versions 6.0 et 5.5.

  Lorsque vous sélectionnez les privilèges, cliquez sur l’icône située à côté de l’étiquette du parent pour développer le parent et afficher les privilèges enfant. Pour sélectionner les privilèges VirtualMachine, vous devez atteindre plusieurs niveaux dans la hiérarchie parent-enfant. Vous n’avez pas besoin de sélectionner tous les privilèges enfant au sein d’un privilège parent.

  ![Hiérarchie des privilèges parent-enfant](./media/backup-azure-backup-server-vmware/cert-add-privilege-expand.png)

  Après que vous avez cliqué sur **OK**, le nouveau rôle s’affiche dans la liste du panneau Rôles.

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



## <a name="create-a-vcenter-server-user-account-and-permissions"></a>Créer un compte d’utilisateur et des autorisations pour un serveur vCenter

Une fois que le rôle est configuré avec des privilèges, créez un compte d’utilisateur. Le compte d’utilisateur possède un nom et un mot de passe, qui représentent les informations d’identification utilisées pour l’authentification.

1. Pour créer un compte d’utilisateur, dans le panneau **Navigateur** du serveur vCenter, cliquez sur **Utilisateurs et groupes**.

    ![Option Utilisateurs et groupes](./media/backup-azure-backup-server-vmware/vmware-userandgroup-panel.png)

    Le panneau **Utilisateurs et groupes vCenter** s’affiche.

    ![Panneau Utilisateurs et groupes vCenter](./media/backup-azure-backup-server-vmware/usersandgroups.png)

2. Dans le panneau **Utilisateurs et groupes vCenter**, dans l’onglet **Utilisateurs**, cliquez sur l’icône d’ajout d’utilisateurs (symbole +).

    La boîte de dialogue **Nouvel utilisateur** s’affiche.

3. Dans la boîte de dialogue **Nouvel utilisateur**, ajoutez les informations de l’utilisateur, puis cliquez sur **OK**. Dans cette procédure, le nom d’utilisateur est BackupAdmin.

    ![Boîte de dialogue Nouvel utilisateur](./media/backup-azure-backup-server-vmware/vmware-new-user-account.png)

    Le nouveau compte d’utilisateur s’affiche dans la liste.

4. Pour associer le compte d’utilisateur au rôle, dans le panneau **Navigateur**, cliquez sur **Autorisations globales**. Dans le panneau **Autorisations globales**, dans l’onglet **Gérer**, cliquez sur l’icône d’ajout (symbole +).

    ![Panneau Autorisations globales](./media/backup-azure-backup-server-vmware/vmware-add-new-perms.png)

    La boîte de dialogue **Global Permissions Root - Add Permission** (Racine des autorisations globales - Ajouter une autorisation) s’ouvre.

5. Dans la boîte de dialogue **Global Permissions Root - Add Permission** (Racine des autorisations globales - Ajouter une autorisation), cliquez sur **Ajouter** pour choisir l’utilisateur ou le groupe.

    ![Choisir un utilisateur ou un groupe](./media/backup-azure-backup-server-vmware/vmware-add-new-global-perm.png)

    La boîte de dialogue **Sélectionner des utilisateurs/groupes** s’ouvre.

6. Dans la boîte de dialogue **Sélectionner des utilisateurs/groupes**, sélectionnez **BackupAdmin**, puis cliquez sur **Ajouter**.

    Dans la zone **Utilisateurs**, le format *domain\username* est utilisé pour le compte d’utilisateur. Si vous souhaitez utiliser un autre domaine, sélectionnez-le dans la liste **Domaine**.

    ![Ajouter un utilisateur BackupAdmin](./media/backup-azure-backup-server-vmware/vmware-assign-account-to-role.png)

    Cliquez sur **OK** pour ajouter les utilisateurs sélectionnés à la boîte de dialogue **Ajouter une autorisation**.

7. Maintenant que vous avez identifié l’utilisateur, vous devez lui affecter le rôle. Dans **Rôle attribué**, dans la liste déroulante, sélectionnez **BackupAdminRole**, puis cliquez sur **OK**.

    ![Affecter un utilisateur à un rôle](./media/backup-azure-backup-server-vmware/vmware-choose-role.png)

  Dans l’onglet **Gérer** du panneau **Autorisations globales**, le nouveau compte d’utilisateur et le rôle associé apparaissent dans la liste.


## <a name="establish-vcenter-server-credentials-on-azure-backup-server"></a>Définir les informations d’identification de serveur vCenter sur le serveur de sauvegarde Azure

Avant d’ajouter le serveur VMware au serveur de sauvegarde Azure, installez la [mise à jour 1 du serveur de sauvegarde Azure](https://support.microsoft.com/help/3175529/update-1-for-microsoft-azure-backup-server).

1. Pour ouvrir le serveur de sauvegarde Azure, double-cliquez sur l’icône présente sur le Bureau du serveur de sauvegarde Azure.

    ![Icône du serveur de sauvegarde Azure](./media/backup-azure-backup-server-vmware/mabs-icon.png)

    Si vous ne trouvez pas l’icône sur le Bureau, ouvrez le serveur de sauvegarde Azure dans la liste des applications installées. Le nom de l’application du serveur de sauvegarde Azure est appelé Sauvegarde Microsoft Azure.

2. Dans la console du serveur de sauvegarde Azure, cliquez sur **Gestion**, **Serveurs de production**, puis, dans la barre d’outils, sur **Gérer VMware**.

    ![Console du serveur de sauvegarde Azure](./media/backup-azure-backup-server-vmware/add-vmware-credentials.png)

    La boîte de dialogue **Gérer les informations d’identification** s’affiche.

    ![Boîte de dialogue Gérer les informations d’identification du serveur de sauvegarde Azure](./media/backup-azure-backup-server-vmware/mabs-manage-credentials-dialog.png)

3. Dans la boîte de dialogue **Gérer les informations d’identification**, cliquez sur **Ajouter** pour ouvrir la boîte de dialogue **Ajouter des informations d’identification**.

4. Dans la boîte de dialogue **Ajouter des informations d’identification**, entrez un nom et une description pour les nouvelles informations d’identification, puis spécifiez le nom d’utilisateur et le mot de passe. Le nom *Contoso Vcenter credential* est utilisé pour identifier les informations d’identification dans la procédure suivante. Utilisez les mêmes nom d’utilisateur et mot de passe que pour le serveur vCenter. Si le serveur vCenter et le serveur de sauvegarde Azure ne se trouvent pas dans le même domaine, spécifiez le domaine dans **Nom d’utilisateur**.

    ![Boîte de dialogue Ajouter des informations d’identification du serveur de sauvegarde Azure](./media/backup-azure-backup-server-vmware/mabs-add-credential-dialog2.png)

    Cliquez sur **Ajouter** pour ajouter les nouvelles informations d’identification au serveur de sauvegarde Azure. Les nouvelles informations d’identification s’affichent dans la liste de la boîte de dialogue **Gérer les informations d’identification**.
    
    ![Boîte de dialogue Gérer les informations d’identification du serveur de sauvegarde Azure](./media/backup-azure-backup-server-vmware/new-list-of-mabs-creds.png)

5. Pour fermer la boîte de dialogue **Gérer les informations d’identification**, cliquez sur le symbole **X** dans l’angle supérieur droit.


## <a name="add-the-vcenter-server-to-azure-backup-server"></a>Ajouter le serveur vCenter au serveur de sauvegarde Azure

L’Assistant Ajout d’un serveur de production permet d’ajouter le serveur vCenter au serveur de sauvegarde Azure.

Pour ouvrir l’Assistant Ajout d’un serveur de production, procédez comme suit :

1. Dans la console du serveur de sauvegarde Azure, cliquez sur **Gestion**, sur **Serveurs de production**, puis sur **Ajouter**.

    ![Ouvrir l’Assistant Ajout d’un serveur de production](./media/backup-azure-backup-server-vmware/add-vcenter-to-mabs.png)

    La boîte de dialogue **Assistant Ajout d’un serveur de production** s’affiche.

    ![Assistant Ajout d’un serveur de production](./media/backup-azure-backup-server-vmware/production-server-add-wizard.png)

2. Dans la page **Sélectionner un type de serveur de production**, sélectionnez **Serveurs VMware**, puis cliquez sur **Suivant**.

3. Dans **Nom du serveur/Adresse IP**, spécifiez le nom de domaine complet (FQDN) ou l’adresse IP du serveur VMware. Vous pouvez utiliser le nom vCenter si tous les serveurs ESXi sont gérés par le même serveur vCenter.

    ![Spécifier l’adresse IP ou le nom de domaine complet du serveur VMware](./media/backup-azure-backup-server-vmware/add-vmware-server-provide-server-name.png)

4. Dans **Port SSL**, entrez le port utilisé pour communiquer avec le serveur VMware. Utilisez le port 443 (le port par défaut), sauf si vous savez que vous avez besoin d’un autre port.

5. Dans **Indiquer les informations d’identification**, sélectionnez les informations d’identification que vous avez créées précédemment.

    ![Indiquer les informations d’identification](./media/backup-azure-backup-server-vmware/identify-creds.png)

6. Cliquez sur **Ajouter** pour ajouter le serveur VMware à la liste des **serveurs VMware ajoutés**, puis cliquez sur **Suivant** pour passer à la page suivante de l’Assistant.

    ![Ajouter un serveur VMware et des informations d’identification](./media/backup-azure-backup-server-vmware/add-vmware-server-credentials.png)

7. Dans la page **Résumé**, cliquez sur **Ajouter** pour ajouter le serveur VMware spécifié au serveur de sauvegarde Azure.

    ![Ajouter le serveur VMware au serveur de sauvegarde Azure](./media/backup-azure-backup-server-vmware/tasks-screen.png)

  La sauvegarde du serveur VMware est une sauvegarde sans agent, et le nouveau serveur est ajouté immédiatement. La page **Terminer** affiche les résultats.

  ![Page Terminer](./media/backup-azure-backup-server-vmware/summary-screen.png)

  Pour ajouter plusieurs instances du serveur vCenter au serveur de sauvegarde Azure, répétez les étapes précédentes de cette section.

Après avoir ajouté le serveur vCenter au serveur de sauvegarde Azure, l’étape suivante consiste à créer un groupe de protection. Le groupe de protection spécifie les différentes informations de rétention à court ou à long terme, et vous permet de définir et appliquer la stratégie de sauvegarde. La stratégie de sauvegarde permet de planifier la date d’exécution des sauvegardes ainsi que leur contenu.


## <a name="configure-a-protection-group"></a>Configurer un groupe de protection

Si vous n’avez pas encore utilisé System Center Data Protection Manager ni le serveur de sauvegarde Azure, voir [Planification de sauvegardes sur disque](https://technet.microsoft.com/library/hh758026.aspx) pour préparer votre environnement matériel. Après avoir vérifié que vous disposez du stockage approprié, utilisez l’Assistant Création d’un nouveau groupe de protection pour ajouter des machines virtuelles VMware.

1. Dans la console du serveur de sauvegarde Azure, cliquez sur **Protection**, puis cliquez sur **Nouveau** dans la barre d’outils pour ouvrir l’assistant Création d’un nouveau groupe de protection.

    ![Ouvrir l’Assistant Création d’un nouveau groupe de protection](./media/backup-azure-backup-server-vmware/open-protection-wizard.png)

    L’Assistant **Création d’un nouveau groupe de protection** s’affiche.

    ![Boîte de dialogue Assistant Création d’un nouveau groupe de protection](./media/backup-azure-backup-server-vmware/protection-wizard.png)

    Cliquez sur **Suivant** pour passer à la page **Sélectionner le type de groupe de protection**.

2. Dans la page **Sélectionner le type de groupe de protection**, sélectionnez **Serveurs**, puis cliquez sur **Suivant**. La page **Sélectionner les membres du groupe** s’affiche.

3. Dans la page **Sélectionner les membres du groupe**, les membres disponibles et les membres sélectionnés s’affichent. Sélectionnez les membres que vous souhaitez protéger, puis cliquez sur **Suivant**.

    ![Sélectionner les membres du groupe](./media/backup-azure-backup-server-vmware/server-add-selected-members.png)

    Lorsque vous sélectionnez un membre, si vous sélectionnez un dossier qui contient d’autres dossiers ou machines virtuelles, ces dossiers et machines virtuelles sont également sélectionnés. On appelle « protection au niveau du dossier » le fait d’inclure des dossiers et des machines virtuelles dans le dossier parent. Pour supprimer un dossier ou une machine virtuelle, décochez la case.

    Si une machine virtuelle ou un dossier contenant une machine virtuelle est déjà protégé(e) dans Azure, vous ne pouvez pas sélectionner de nouveau cette machine virtuelle. Autrement dit, une fois qu’une machine virtuelle est protégée dans Azure, elle ne peut pas être de nouveau protégée, ce qui empêche la création de points de récupération en double pour une même machine virtuelle. Si vous souhaitez connaître l’instance de serveur de sauvegarde Azure qui protège déjà un membre, pointez sur le membre pour visualiser le nom du serveur de protection.

4. Dans la page **Sélectionner la méthode de protection des données**, entrez le nom du groupe de protection. Les options Protection à court terme (sur disque) et Protection en ligne sont sélectionnées. Si vous souhaitez utiliser la protection en ligne (sur Azure), vous devez utiliser la protection à court terme sur disque. Cliquez sur **Suivant** pour passer à la plage de protection à court terme.

    ![Sélectionner la méthode de protection des données](./media/backup-azure-backup-server-vmware/name-protection-group.png)

5. Dans la page **Spécifier les objectifs à court terme**, dans le champ **Durée de rétention**, spécifiez le nombre de jours pendant lesquels vous souhaitez conserver les points de récupération qui sont *stockés sur le disque*. Si vous souhaitez modifier l’heure et les jours de création des points de récupération, cliquez sur **Modifier**. Les points de récupération à court terme sont des sauvegardes complètes. Ils ne fonctionnent pas comme des sauvegardes incrémentielles. Après avoir défini vos objectifs à court terme, cliquez sur **Suivant**.

    ![Spécifier les objectifs à court terme](./media/backup-azure-backup-server-vmware/short-term-goals.png)

6. Dans la page **Vérifier l’allocation de disque**, passez en revue les informations et modifiez l’espace disque des machines virtuelles si nécessaire. Les allocations de disques recommandées dépendent de la durée de rétention spécifiée dans la page **Spécifier les objectifs à court terme**, du type de charge de travail et de la taille des données protégées (identifiées à l’étape 3).  

  - **Taille des données** : taille des données dans le groupe de protection.
  - **Espace disque** : quantité d’espace disque recommandée pour le groupe de protection. Pour modifier ce paramètre, vous devez allouer un espace total légèrement supérieur au volume de croissance estimé pour chaque source de données.
  - **Colocaliser les données** : si vous activez la colocalisation, plusieurs sources de données dans la protection peuvent être mappées à un seul réplica et à un seul volume de points de récupération. La colocalisation n’est pas prise en charge pour toutes les charges de travail.
  - **Augmenter automatiquement :** si vous activez ce paramètre et si les données contenues dans le groupe protégé dépassent l’allocation initiale, System Center Data Protection Manager tente d’augmenter la taille du disque de 25 %.
  - **Détails de pool de stockage** : affiche l’état du pool de stockage, notamment la taille totale et la taille de disque restante.

    ![Vérifier l’allocation de disque](./media/backup-azure-backup-server-vmware/review-disk-allocation.png)

    Après avoir défini l’allocation d’espace, cliquez sur **Suivant**.

7. Dans la page **Choisir la méthode de création de réplica**, indiquez de quelle manière vous souhaitez générer la copie initiale, ou le réplica, des données protégées sur le serveur de sauvegarde Azure.

    Les valeurs **Automatiquement sur le réseau** et **Maintenant** sont définies par défaut. Si vous utilisez la valeur par défaut, nous vous recommandons de spécifier une heure creuse. Choisissez **Ultérieurement** et spécifiez le jour et l’heure.

    Pour de grandes quantités de données ou des conditions réseau peu optimales, pensez à répliquer les données hors connexion à l’aide d’un média amovible.

    Après avoir effectué vos sélections, cliquez sur **Suivant**.

    ![Choisir la méthode de création d’un réplica](./media/backup-azure-backup-server-vmware/replica-creation.png)

8. Dans la page **Options de vérification de cohérence**, indiquez comment et quand automatiser les vérifications de cohérence. Vous pouvez exécuter les vérifications de cohérence lorsque les données du réplica deviennent incohérentes ou en fonction d’une planification définie.

    Si vous ne voulez pas configurer de vérifications de cohérence automatiques, vous pouvez exécuter une vérification manuelle. Dans la zone de protection de la console du serveur de sauvegarde Azure, cliquez avec le bouton droit sur le groupe de protection, puis sélectionnez **Effectuer une vérification de cohérence**.

    Cliquez sur **Suivant** pour passer à la page suivante.

9. Dans la page **Indiquer les données de protection en ligne**, sélectionnez une ou plusieurs sources de données que vous souhaitez protéger. Vous pouvez sélectionner les membres un à un ou cliquer sur **Sélectionner tout** pour choisir tous les membres. Après avoir sélectionné les membres, cliquez sur **Suivant**.

    ![Spécifier les données de protection en ligne](./media/backup-azure-backup-server-vmware/select-data-to-protect.png)

10. Dans la page **Spécifier une planification de sauvegarde en ligne**, indiquez la planification qui vous permettra de générer des points de récupération à partir de la sauvegarde sur disque. Le point de récupération généré est transféré vers le coffre Recovery Services dans Azure. Lorsque vous avez fini de paramétrer la planification de sauvegarde en ligne, cliquez sur **Suivant**.

    ![Spécifier la planification de sauvegarde en ligne](./media/backup-azure-backup-server-vmware/online-backup-schedule.png)

11. Dans la page **Spécifier une stratégie de rétention en ligne**, indiquez la durée de rétention souhaitée des données de sauvegarde dans Azure. Après avoir défini la stratégie, cliquez sur **Suivant**.

    ![Spécifier la stratégie de rétention en ligne](./media/backup-azure-backup-server-vmware/retention-policy.png)

    La durée de conservation des données dans Azure n’est soumise à aucune restriction. Lorsque vous stockez des données de point de récupération dans Azure, la seule limite que vous devez respecter est que vous ne pouvez pas avoir plus de 9 999 points de récupération par instance protégée. Dans cet exemple, l’instance protégée est le serveur VMware.

12. Dans la page **Résumé**, passez en revue les détails des paramètres et des membres du groupe de protection, puis cliquez sur **Créer un groupe**.

    ![Résumé des paramètres et des membres du groupe de protection](./media/backup-azure-backup-server-vmware/protection-group-summary.png)

## <a name="next-steps"></a>Étapes suivantes
Si vous utilisez le serveur de sauvegarde Azure pour protéger vos charges de travail VMware, vous trouverez peut-être utile d’utiliser le serveur de sauvegarde Azure pour contribuer à la protection d’un [serveur Microsoft Exchange](./backup-azure-exchange-mabs.md), d’une [batterie de serveurs Microsoft SharePoint](./backup-azure-backup-sharepoint-mabs.md) ou d’une [base de données SQL Server](./backup-azure-sql-mabs.md).

Pour plus d’informations sur les problèmes d’inscription de l’agent, de configuration du groupe de protection ou de sauvegarde des travaux, voir [Résoudre les problèmes d’un serveur de sauvegarde Azure](./backup-azure-mabs-troubleshoot.md).

