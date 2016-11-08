## Création d’un coffre de sauvegarde pour une machine virtuelle
Un coffre de sauvegarde est une entité qui stocke les sauvegardes et les points de récupération créés au fil du temps. Le coffre de sauvegarde contient également les stratégies de sauvegarde qui seront appliquées aux machines virtuelles en cours de sauvegarde.

Cette image illustre les relations entre les différentes entités Azure Backup : ![Entités et relations Azure Backup](./media/backup-create-vault-for-vms/vault-policy-vm.png)

Pour créer un archivage de sauvegarde :

1. Connectez-vous au [portail Azure](http://manage.windowsazure.com/).
2. Dans le portail Azure, cliquez sur **Nouveau** > **Intégration hybride** > **Sauvegarde**. Lorsque vous cliquez sur **Sauvegarde**, vous accédez automatiquement à la version classique du portail (illustrée après la remarque).
   
    ![Portail Ibiza](./media/backup-create-vault-for-vms/Ibiza-portal-backup01.png)
   
   > [!NOTE]
   > Si la dernière utilisation de votre abonnement s’est déroulée dans le portail classique, alors il est possible que votre abonnement s’ouvre dans le portail classique. Dans ce cas, pour créer un coffre de sauvegarde, cliquez sur **Nouveau** > **Data Services** > **Recovery Services** > **Coffre de sauvegarde** > **Création rapide** (voir l’image ci-dessous).
   > 
   > 
   
    ![Créer un archivage de sauvegarde](./media/backup-create-vault-for-vms/backup_vaultcreate.png)
3. Sous **Nom**, entrez un nom convivial permettant d’identifier le coffre. Le nom doit être unique pour l’abonnement Azure. Tapez un nom contenant entre 2 et 50 caractères. Il doit commencer par une lettre, et ne peut contenir que des lettres, des chiffres et des traits d’union.
4. Dans **Region**, sélectionnez la région géographique du coffre. L’archivage doit se trouver dans la même région que les machines virtuelles que vous souhaitez protéger. Si vous avez des machines virtuelles dans plusieurs régions, vous devez créer un coffre de sauvegarde dans chaque région. Il est inutile de spécifier des comptes de stockage pour stocker les données de sauvegarde : l’archivage de sauvegarde et le service Azure Backup s’en chargent automatiquement.
5. Sous **Abonnement**, sélectionnez l’abonnement à associer au coffre de sauvegarde. Vous ne disposez de plusieurs choix que si votre compte professionnel est associé à plusieurs abonnements Azure.
6. Cliquez sur **Create vault**. La création du coffre de sauvegarde peut prendre du temps. Surveillez les notifications d'état en bas du portail.
   
    ![Créer une notification toast l’archivage](./media/backup-create-vault-for-vms/creating-vault.png)
7. Un message confirme que le coffre a été correctement créé. Il est répertorié dans la page **Recovery Services** comme étant **Actif**. Veillez à choisir l’option de redondance de stockage appropriée juste après la création de l’archivage. En savoir plus sur la [définition de l’option de redondance de stockage dans le coffre de sauvegarde](../articles/backup/backup-configure-vault.md#azure-backup---storage-redundancy-options).
   
    ![Liste des archivages de sauvegarde](./media/backup-create-vault-for-vms/backup_vaultslist.png)
8. Cliquez sur l’archivage de sauvegarde pour accéder à la page **Démarrage rapide** où sont affichées les instructions pour la sauvegarde des machines virtuelles Azure.
   
    ![Instructions de sauvegarde de machines virtuelles dans la page Tableau de bord](./media/backup-create-vault-for-vms/vmbackup-instructions.png)

<!---HONumber=AcomDC_0302_2016-->