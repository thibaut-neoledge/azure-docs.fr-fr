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
ms.date: 03/28/2017
ms.author: markgal;
translationtype: Human Translation
ms.sourcegitcommit: cc9e81de9bf8a3312da834502fa6ca25e2b5834a
ms.openlocfilehash: 779841483e72b42725354d85a0f593aee7db8278
ms.lasthandoff: 04/11/2017


---
# <a name="back-up-vmware-server-to-azure"></a>Sauvegarder un serveur VMware dans Azure

Cet article explique comment connecter un serveur VMware à un serveur de sauvegarde Azure afin de pouvoir sauvegarder le contenu du serveur VMware dans le cloud. Cet article suppose que vous avez déjà installé le serveur de sauvegarde Azure.

## <a name="create-secure-connection-to-vmware-server"></a>Créer une connexion sécurisée à un serveur VMware

Pour protéger un serveur VMware, le serveur de sauvegarde Azure doit être en mesure de se connecter de façon sécurisée au serveur VMware. Pour activer la connexion sécurisée, installez un certificat valide sur le serveur VMware et sur le serveur de sauvegarde Azure.

Lorsque vous vous connectez au serveur VMware, si l’URL n’est pas sécurisée, vous devez exporter le certificat afin de sécuriser la connexion au site.
![exemple de connexion non sécurisée à un serveur VMware](./media/backup-azure-backup-server-vmware/unsecure-url.png)

1. Cliquez sur https (barré), puis, dans le menu contextuel, cliquez sur le lien Détails.

  En fonction de votre navigateur, vous devrez peut-être cliquer sur **Paramètres** > **Autres outils** > **Outils de développement**, puis sélectionnez l’onglet sécurité.

  ![exemple de message d’erreur lié à une connexion non sécurisée](./media/backup-azure-backup-server-vmware/security-tab.png)

2. Dans les informations détaillées de l’onglet Sécurité, cliquez sur **Afficher le certificat**.

  ![exemple de message d’erreur lié à une connexion non sécurisée](./media/backup-azure-backup-server-vmware/security-tab-view-certificate.png)

  La boîte de dialogue Certificat s’ouvre.

3. Dans la boîte de dialogue Certificat, cliquez sur l’onglet Chemin d’accès de certification.  

  ![boîte de dialogue Certificat avec message d’erreur ](./media/backup-azure-backup-server-vmware/certificate-certification-path.png)

  Le certificat sélectionné n’est pas approuvé car dans ce cas, l’émetteur est introuvable. Le certificat peut ne pas être approuvé pour d’autres raisons.

4. Pour exporter le certificat sur votre ordinateur local, cliquez sur l’onglet Détails, puis cliquez sur Copier dans un fichier.

  ![boîte de dialogue Certificat avec message d’erreur ](./media/backup-azure-backup-server-vmware/certificate-details-tab.png)

  L’Assistant Exportation de certificat s’ouvre.

  ![boîte de dialogue Certificat avec message d’erreur ](./media/backup-azure-backup-server-vmware/certificate-wizard1.png)

  Cliquez sur **Suivant** pour vous déplacer dans l’Assistant.

5. Dans l’écran Format de fichier d’exportation, spécifiez le format de certificat de votre choix. Si vous n’avez aucune préférence, acceptez le format de fichier par défaut pour le certificat et cliquez sur **Suivant**.

  ![boîte de dialogue Certificat avec message d’erreur ](./media/backup-azure-backup-server-vmware/certificate-wizard1b.png)

6. Sur l’écran Fichier à exporter, donnez un nom à votre certificat, puis cliquez sur Parcourir pour choisir l’emplacement de stockage du certificat sur votre ordinateur local. Enregistrez le certificat à un emplacement où vous pourrez facilement le retrouver.

  ![boîte de dialogue Certificat avec message d’erreur ](./media/backup-azure-backup-server-vmware/certificate-wizard2.png)

7. Après avoir exporté le certificat, accédez à l’emplacement où vous l’avez enregistré, cliquez sur le certificat, puis, dans le menu, sélectionnez **Installer le certificat**.

  L’Assistant Importation de certificat s’ouvre.

  ![boîte de dialogue Certificat avec message d’erreur ](./media/backup-azure-backup-server-vmware/cert-import-wizard1.png)

8. Dans l’Assistant Importation de certificat, sélectionnez **Ordinateur Local** comme destination du certificat, cliquez sur **Suivant** pour continuer.

9. Sur l’écran Magasin de certificats, sélectionnez **Placer tous les certificats dans le magasin suivant**, puis cliquez sur **Parcourir**.

  ![boîte de dialogue Certificat avec message d’erreur ](./media/backup-azure-backup-server-vmware/cert-import-wizard-local-store.png)

  La boîte de dialogue Sélectionner un magasin de certificats s’ouvre.

  ![boîte de dialogue Certificat avec message d’erreur ](./media/backup-azure-backup-server-vmware/cert-store.png)

  Choisissez le dossier de destination des certificats, puis cliquez sur **OK**. Si vous ne connaissez pas le dossier à utiliser, choisissez Autorités de certification racines de confiance. Le dossier de destination choisi s’affiche dans la boîte de dialogue Magasin de certificats. Cliquez sur **Suivant** pour importer le certificat.

10. Sur l’écran Fin de l'Assistant Importation de certificat, vérifiez que le certificat se trouve dans le dossier de votre choix et cliquez sur Terminer pour terminer l’Assistant.

  ![boîte de dialogue Certificat avec message d’erreur ](./media/backup-azure-backup-server-vmware/cert-wizard-final-screen.png)

  Une boîte de dialogue s’affiche et vous indique si l’importation a réussi.

11. Connectez-vous à la machine virtuelle VMware pour vérifier que vous disposez d’une connexion sécurisée au serveur VMware. Le serveur de sauvegarde Azure se connecte au serveur VMware via un canal HTTPs sécurisé. Si vous avez des limites sécurisées au sein de votre organisation et que vous ne souhaitez pas activer le protocole HTTPs, désactivez la communication sécurisée via le registre. Il est cependant recommandé d’installer des certificats sur le serveur de sauvegarde Azure et sur le serveur VMware afin de permettre une communication sécurisée.


## <a name="create-role-and-user-account-on-vmware-server"></a>Créer un compte et un compte utilisateur sur le serveur VMware

Le serveur de sauvegarde Azure communique avec un serveur VMware distant en authentifiant les informations d’identification d’un utilisateur VMware spécifié. Le serveur de sauvegarde Azure authentifie les informations d’identification de l’utilisateur de VMware pour toutes les opérations de sauvegarde. Utilisez l’Assistant Ajout d’un serveur de production du serveur de sauvegarde Azure pour permettre au serveur de sauvegarde Azure de communiquer de manière sécurisée avec le serveur VMware. La configuration de la relation entre le serveur de sauvegarde Azure et le serveur VMware se déroule en trois temps. 

- Création d’un rôle d’utilisateur disposant de privilèges
- Création d’un compte d’utilisateur avec des informations d’identification (nom d’utilisateur et mot de passe)
- Ajout du compte d’utilisateur du serveur VMware au serveur de sauvegarde Azure

, lorsque vous passez par l’Assistant Ajout d’un serveur de production. La paire nom d’utilisateur et mot de passe est stockée en tant qu’informations d’identification.


### <a name="create-user-role-and-add-privileges"></a>Créer le rôle d’utilisateur et ajouter des privilèges
Le compte d’utilisateur VMware, qui est spécifié dans les informations d’identification du serveur de sauvegarde Azure, doit être associé à certains privilèges. Les privilèges sont toutefois associés à un rôle d’utilisateur ; autrement dit, nous allons d’abord créer un rôle d’utilisateur, puis lui ajouter des privilèges spécifiques. Les privilèges qui sont associés au rôle d’utilisateur s’appliquent à un administrateur de sauvegarde.

1. Pour créer un nouveau rôle d’utilisateur VMware, connectez-vous à votre serveur VMware, puis, dans le panneau **Navigateur**, cliquez sur **Administration**.

  ![boîte de dialogue Certificat avec message d’erreur ](./media/backup-azure-backup-server-vmware/vmware-navigator-panel.png)

2. Pour créer un nouveau rôle dans la section **Administration**, sélectionnez **Rôles** puis, dans le panneau **Rôles**, cliquez sur l’icône Ajouter un rôle (symbole +).

  ![boîte de dialogue Certificat avec message d’erreur ](./media/backup-azure-backup-server-vmware/vmware-define-new-role.png)

  La boîte de dialogue **Créer un rôle** s’ouvre.

  ![boîte de dialogue Certificat avec message d’erreur ](./media/backup-azure-backup-server-vmware/vmware-define-new-role-priv.png)

3. Dans la boîte de dialogue **Créer un rôle**, dans le champ **Nom du rôle**, saisissez un nom. Dans cet exemple, nous allons utiliser le nom *BackupAdminRole*. Vous pouvez choisir n’importe quel nom de rôle, à condition qu’il soit reconnaissable pour le rôle.

4. Sélectionnez les privilèges à appliquer au rôle d’utilisateur. Dans la liste suivante, sélectionnez les privilèges. Lorsque vous sélectionnez les privilèges, cliquez sur le chevron situé sur l’étiquette du parent pour développer le parent et afficher les privilèges enfant. Vous n’avez pas besoin de sélectionner tous les privilèges enfant au sein d’un privilège parent.

  ![boîte de dialogue Certificat avec message d’erreur ](./media/backup-azure-backup-server-vmware/cert-add-privilege-expand.png)

  - Privilege.Datastore.AllocateSpace.label
  - Privilege.Global.ManageCustomerFields.label
  - privilege.Network.Assign.label
  - Privilege.VirtualMachine.Config.AddNewDisk.label
  - Privilege.VirtualMachine.Config.AdvanceConfig.label
  - Privilege.VirtualMachine.Config.ChangeTracking.label
  - Privilege.VirtualMachine.Config.HostUSBDevice.label
  - Privilege.VirtualMachine.Config.SwapPlacement.label  
  - Privilege.VirtualMachine.Interact.PowerOff.label
  - Privilege.VirtualMachine.Inventory.Create.label
  - Privilege.VirtualMachine.Provisioning.DiskRandomRead.summary
  - Privilege.VirtualMachine.State.CreateSnapshot.label
  - Privilege.VirtualMachine.State.RemoveSnapshot.label
</br>

  Une fois que vous avez sélectionné les privilèges, cliquez sur **OK**. Le nouveau rôle s’affiche dans la liste sur le panneau Rôles.

### <a name="create-a-user-account-and-assign-permissions"></a>Créer un compte d’utilisateur et attribuer des autorisations

Une fois que vous avez défini le rôle d’utilisateur avec des privilèges, créez un compte d’utilisateur. Lorsque vous créez le compte d’utilisateur, vous devez lui affecter un rôle d’utilisateur spécifique, qui accorde au compte les privilèges associés. Le compte d’utilisateur possède un nom et un mot de passe, qui représentent les informations d’identification utilisées pour s’authentifier.

1. Pour créer un nouveau compte d’utilisateur, sur le serveur VMware, dans le volet de navigation, cliquez sur **Utilisateurs et groupes**.

  ![boîte de dialogue Certificat avec message d’erreur ](./media/backup-azure-backup-server-vmware/vmware-userandgroup-panel.png)

  Le panneau Utilisateurs et groupes s’affiche.

  ![boîte de dialogue Certificat avec message d’erreur ](./media/backup-azure-backup-server-vmware/usersandgroups.png)

2. Dans le panneau Utilisateurs et groupes VMware, sous l’onglet Utilisateurs, cliquez sur l’icône Ajouter des utilisateurs (symbole +).

  La boîte de dialogue Nouvel utilisateur s’ouvre.

3. Le compte d’utilisateur que vous créez contient la paire nom et mot de passe d’utilisateur qui est utilisée en tant qu’informations d’identification. Dans la boîte de dialogue Nouvel utilisateur, remplissez les champs et cliquez sur **OK**.

  ![boîte de dialogue Certificat avec message d’erreur ](./media/backup-azure-backup-server-vmware/vmware-new-user-account.png)

  Le nouveau compte d’utilisateur s’affiche dans la liste.

4. Maintenant que vous avez créé le compte d’utilisateur, vous devez l’associer au rôle d’utilisateur (qui possède les autorisations que vous avez choisies). Dans le volet de navigation, cliquez sur **Autorisations globales**. Dans le panneau Autorisations globales, cliquez sur l’onglet **Gérer**, puis cliquez sur l’icône Ajouter (symbole +).

  ![boîte de dialogue Certificat avec message d’erreur ](./media/backup-azure-backup-server-vmware/vmware-add-new-perms.png)

  La boîte de dialogue Global Permissions Root - Add Permission (Racine des autorisations globales - Ajouter une autorisation) s’ouvre.

5. Dans la boîte de dialogue **Global Permissions Root - Add Permission** (Racine des autorisations globales - Ajouter une autorisation), cliquez sur **Ajouter** pour choisir l’utilisateur ou le groupe.

  ![boîte de dialogue Certificat avec message d’erreur ](./media/backup-azure-backup-server-vmware/vmware-add-new-global-perm.png)

  La boîte de dialogue Sélectionner des utilisateurs/groupes s’ouvre.

6. Dans la boîte de dialogue **Sélectionner des utilisateurs/groupes**, sélectionnez le compte d’utilisateur que vous avez créé et cliquez sur **Ajouter**. Le compte d’utilisateur sélectionné s’affiche dans le champ Utilisateurs. Le nom d’utilisateur s’affiche dans le champ Utilisateurs au format *domaine*`\`*nom d’utilisateur*.

  ![boîte de dialogue Certificat avec message d’erreur ](./media/backup-azure-backup-server-vmware/vmware-assign-account-to-role.png)

  Cliquez sur **OK** pour ajouter les utilisateurs sélectionnés dans la boîte de dialogue Ajouter une autorisation.

7. Maintenant que vous avez identifié l’utilisateur, vous devez lui affecter le rôle. Dans la zone Assigned Role (Rôle assigné), dans le menu déroulant, sélectionnez le rôle et cliquez sur **OK**.

  ![boîte de dialogue Certificat avec message d’erreur ](./media/backup-azure-backup-server-vmware/vmware-choose-role.png)

  Sous l’onglet Gérer de la boîte de dialogue des autorisations globales, le nouveau compte d’utilisateur et le rôle associé apparaissent dans la liste.


### <a name="add-the-vmware-user-account-credentials-to-azure-backup-server"></a>Ajouter les informations d’identification du compte d’utilisateur VMware au serveur de sauvegarde Azure

Avant d’ajouter le serveur VMware au serveur de sauvegarde Azure, assurez-vous que vous avez installé la [mise à jour 1 du serveur de sauvegarde Microsoft Azure](https://support.microsoft.com/help/3175529/update-1-for-microsoft-azure-backup-server).

1. Cliquez sur l’icône suivante (sur le Bureau du serveur) pour ouvrir la console du serveur de sauvegarde Azure.

  ![Icône du serveur de sauvegarde Azure](./media/backup-azure-backup-server-vmware/mabs-icon.png)

  Si vous ne trouvez pas l’icône sur le Bureau, vous pouvez ouvrir le serveur de sauvegarde Azure dans la liste des applications installées. Dans la liste des applications installées, l’application du serveur de sauvegarde Azure est nommée Sauvegarde Microsoft Azure.

2. Dans la console du serveur de sauvegarde Azure, cliquez sur **Gestion** et sur **Serveurs de Production**, puis dans la barre d’outils, cliquez sur **Gérer VMware**.

  ![Console MABS](./media/backup-azure-backup-server-vmware/add-vmware-credentials.png)

  La boîte de dialogue Gérer les informations d’identification s’ouvre.

  ![Boîte de dialogue Gérer les informations d’identification de la console MABS](./media/backup-azure-backup-server-vmware/mabs-manage-credentials-dialog.png)

3. Dans la boîte de dialogue Gérer les informations d’identification, cliquez sur **Ajouter** pour ouvrir la boîte de dialogue Ajouter des informations d’identification.

4. Dans la boîte de dialogue Ajouter des informations d’identification, entrez un nom et une description pour les nouvelles informations d’identification. Le nom d’utilisateur et le mot de passe doivent être les mêmes que ceux utilisés lors de la création du compte d’utilisateur dans le serveur VMware.

  ![Boîte de dialogue Gérer les informations d’identification de la console MABS](./media/backup-azure-backup-server-vmware/mabs-add-credential-dialog.png)

  Cliquez sur **Ajouter** pour ajouter les nouvelles informations d’identification au serveur de sauvegarde Azure. Les nouvelles informations d’identification s’affichent dans la liste de la boîte de dialogue Gérer les informations d’identification.
  ![Boîte de dialogue Gérer les informations d’identification de la console MABS](./media/backup-azure-backup-server-vmware/new-list-of-mabs-creds.png)

5. Cliquez sur le symbole **X** en haut à droite pour fermer la boîte de dialogue Gérer les informations d’identification.


## <a name="add-vmware-server-to-azure-backup-server"></a>Ajouter le serveur VMware au serveur de sauvegarde Azure

pour ouvrir l’Assistant Ajout d’un serveur de production

1. Dans la console du serveur de sauvegarde Azure, cliquez sur **Gestion** et sur **Serveurs de Production**, puis cliquez sur **Ajouter**.

  ![Assistant Ajout d’un serveur de production](./media/backup-azure-backup-server-vmware/add-vcenter-to-mabs.png)

  L’Assistant Ajout d’un serveur de production s’ouvre.

  ![Assistant Ajout d’un serveur de production](./media/backup-azure-backup-server-vmware/production-server-add-wizard.png)

2. Dans l’écran Sélectionner un type de serveur de protection, sélectionnez Serveurs VMware, puis cliquez sur **Suivant**.

3. Dans Nom du serveur/Adresse IP, spécifiez le nom de domaine complet (FQDN) ou l’adresse IP du serveur VMware. Vous pouvez entrer le nom VMware si tous les serveurs ESXi sont gérés par le même serveur vCenter.

  ![Assistant Ajout d’un serveur de production](./media/backup-azure-backup-server-vmware/add-vmware-server-provide-server-name.png)

4. Dans la boîte de dialogue **Port SSL**, entrez le port utilisé pour communiquer avec le serveur VMware. Utilisez le port 443 (le port par défaut), sauf si vous savez que vous avez besoin d’un autre port.

5. Dans la boîte de dialogue **Indiquer les informations d’identification**, vous pouvez créer de nouvelles informations d’identification ou sélectionner des informations d’identification existantes. Dans la section précédente, vous avez créé des informations d’identification. Sélectionnez-les.

  Si aucune information d’identification n’est disponible, ou si vous devez créer de nouvelles informations d’identification, cliquez sur **Ajouter de nouvelles informations d’identification**, créez les nouvelles informations d’identification, puis cliquez sur **OK**.

  ![Assistant Ajout d’un serveur de production](./media/backup-azure-backup-server-vmware/specify-new-cred.png)

6. Cliquez sur **Ajouter** pour ajouter le serveur VMware à la liste des **serveurs VMware ajoutés**, puis cliquez sur **Suivant pour passer à l’écran suivant de l’Assistant.

  ![Assistant Ajout d’un serveur de production](./media/backup-azure-backup-server-vmware/add-vmware-server-credentials.png)

7. Dans l’écran **Tâches**, cliquez sur **Ajouter** pour ajouter le serveur VMware spécifié au serveur de sauvegarde Azure.

  ![Assistant Ajout d’un serveur de production](./media/backup-azure-backup-server-vmware/tasks-screen.png)

  Étant donné que la sauvegarde du serveur VMware est une sauvegarde sans agent, l’ajout du nouveau serveur s’effectue en quelques secondes. Vous pouvez ajouter plusieurs serveurs VMware au serveur de sauvegarde Azure en répétant les étapes précédentes de cette section.

Maintenant que vous avez ajouté un serveur VMware au serveur de sauvegarde Azure, l’étape suivante consiste à créer un groupe de protection. Le groupe de protection spécifie les différentes informations de rétention à court ou à long terme, et vous permet de définir et appliquer la stratégie de sauvegarde. La stratégie de sauvegarde permet de planifier les dates d’exécution des sauvegardes ainsi que les éléments à sauvegarder.


## <a name="configure-a-protection-group-to-back-up-vmware-server"></a>Configurer un groupe de protection dans le cadre d’une sauvegarde du serveur VMware

Si vous n’avez pas encore utilisé System Center Data Protection Manager ou le serveur de sauvegarde Azure, consultez la rubrique [Planification de sauvegardes sur disque](https://technet.microsoft.com/library/hh758026.aspx) pour préparer votre environnement matériel. Une fois que vous avez vérifié que vous disposez du stockage approprié, utilisez l’Assistant Création d’un nouveau groupe de protection pour ajouter les machines virtuelles spécifiques.

1. Dans la console du serveur de sauvegarde Azure, cliquez sur **Protection**, puis cliquez sur **Nouveau** dans la barre d’outils pour ouvrir l’assistant Création d’un nouveau groupe de protection.

  ![Assistant Ajout d’un serveur de production](./media/backup-azure-backup-server-vmware/open-protection-wizard.png)

  L’Assistant Création d’un nouveau groupe de protection s’ouvre.

  ![Assistant Ajout d’un serveur de production](./media/backup-azure-backup-server-vmware/protection-wizard.png)

  Cliquez sur **Suivant** pour passer à l’écran **Sélectionner le type de groupe de protection**.

2. Dans l’écran Sélectionner le type de groupe de protection, sélectionnez **Serveurs**, puis cliquez sur **Suivant**.

3. Dans l’écran Sélectionner les membres du groupe, vous pouvez voir les membres disponibles ainsi que les membres qui ont été sélectionnés. Sélectionnez les membres que vous souhaitez protéger, puis cliquez sur **Suivant**.

  ![Assistant Ajout d’un serveur de production](./media/backup-azure-backup-server-vmware/server-add-to-selected-members.png)

  Lorsque vous sélectionnez un membre, si vous sélectionnez un dossier qui contient d’autres dossiers ou machines virtuelles, ces dossiers et machines virtuelles sont également sélectionnés. On appelle « protection au niveau du dossier » le fait d’inclure des dossiers et des machines virtuelles dans le dossier parent. Vous pouvez exclure n’importe quel dossier ou machine virtuelle en désélectionnant la case à cocher.

  Si une machine virtuelle ou un dossier contenant une machine virtuelle est déjà protégé(e) dans Azure, vous ne pouvez pas sélectionner de nouveau cette machine virtuelle. Autrement dit, une fois qu’une machine virtuelle est protégée dans Azure, elle ne peut pas être de nouveau protégée, afin d’éviter que des points de récupération en double ne soient créés pour une même machine virtuelle. Si vous souhaitez connaître le serveur de sauvegarde Azure qui protège déjà un membre, pointez votre souris sur le membre pour visualiser le nom du serveur de protection.

4. Dans l’écran Sélectionner la méthode de protection des données, entrez le nom du groupe de protection. Ensuite, indiquez l’emplacement de sauvegarde de vos données dans le champ **Méthode de protection**. Les données sont sauvegardées sur un disque dans le cadre d’une protection à court terme. Pour une protection à long terme, elles sont sauvegardées dans le cloud (Azure). Cliquez sur **Suivant** pour passer à la plage de protection à court terme.

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

  Une fois que vous avez terminé, cliquez sur **Suivant**.

7. Dans l’écran Choisir la méthode de création de réplica, indiquez de quelle manière vous souhaitez générer la copie initiale, ou le réplica, des données protégées sur le serveur de sauvegarde Azure.

  Les valeurs **Automatiquement sur le réseau** et **Maintenant** sont définies par défaut. Si vous utilisez la valeur par défaut, nous vous recommandons de spécifier une heure creuse. Choisissez **Ultérieurement** et spécifiez le jour et l’heure.

  Pour de grandes quantités de données ou des conditions de réseau peu optimales, envisagez de répliquer les données hors connexion à l’aide d’un support amovible.

  Une fois que vous avez effectué vos sélections, cliquez sur **suivant**.

  ![Assistant Création d’un nouveau groupe de protection](./media/backup-azure-backup-server-vmware/replica-creation.png)

8. Dans l’écran **Options de vérification de cohérence**, indiquez comment et quand automatiser les vérifications de cohérence. Vous pouvez exécuter les vérifications de cohérence lorsque les données du réplica deviennent incohérentes, ou selon une planification définie.

  Si vous ne souhaitez pas configurer la vérification de cohérence automatique, vous pouvez exécuter une vérification manuelle à tout moment en double-cliquant sur le groupe de protection dans la zone Protection de la console du serveur de sauvegarde Azure et en sélectionnant Effectuer une vérification de cohérence.

  Cliquez sur **Suivant** pour passer à l’écran suivant.

9. Dans l’écran **Indiquer les données de protection en ligne**, sélectionnez la ou les sources de données que vous souhaitez protéger. Vous pouvez sélectionner les membres un à un ou cliquer sur **Sélectionner tout** pour choisir tous les membres. Une fois que vous avez sélectionné les membres, cliquez sur **Suivant*.

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

