## <a name="deploy-template-from-cloud-shell"></a>Déployer le modèle à partir de Cloud Shell

Vous pouvez utiliser [Cloud Shell](../articles/cloud-shell/overview.md) pour déployer votre modèle. Toutefois, vous devez d’abord charger votre modèle dans le partage de fichiers de votre Cloud Shell. Si vous n’avez pas utilisé Cloud Shell, consultez [Vue d’ensemble d’Azure Cloud Shell](../articles/cloud-shell/overview.md) pour obtenir plus d’informations sur sa configuration.

1. Connectez-vous au [portail Azure](https://portal.azure.com).

1. Sélectionnez votre groupe de ressources Cloud Shell. Le modèle de nom est `cloud-shell-storage-<region>`.

   ![Sélection du groupe de ressources](./media/resource-manager-cloud-shell-deploy/select-cs-resource-group.png)

1. Sélectionnez le compte de stockage de votre Cloud Shell.

   ![Sélectionner le compte de stockage](./media/resource-manager-cloud-shell-deploy/select-storage.png)

1. Sélectionnez **Fichiers**.

   ![Sélectionner des fichiers](./media/resource-manager-cloud-shell-deploy/select-files.png)

1. Sélectionnez le partage de fichiers pour Cloud Shell. Le modèle de nom est `cs-<user>-<domain>-com-<uniqueGuid>`.

   ![Sélectionner le partage de fichiers](./media/resource-manager-cloud-shell-deploy/select-file-share.png)

1. Sélectionnez **Ajouter un répertoire**.

   ![Ajouter un répertoire](./media/resource-manager-cloud-shell-deploy/select-add-directory.png)

1. Nommez-le **modèles** puis sélectionnez **Ok**.

   ![Nommer le répertoire](./media/resource-manager-cloud-shell-deploy/name-templates.png)

1. Sélectionnez votre nouveau répertoire.

   ![Sélectionner le répertoire](./media/resource-manager-cloud-shell-deploy/select-templates.png)

1. Sélectionnez **Télécharger**.

   ![Sélectionner Télécharger](./media/resource-manager-cloud-shell-deploy/select-upload.png)

1. Recherchez et chargez votre modèle.

   ![Charger le fichier](./media/resource-manager-cloud-shell-deploy/upload-files.png)

1. Ouvrez l’invite de commandes.

   ![Ouvrir Cloud Shell](./media/resource-manager-cloud-shell-deploy/start-cloud-shell.png)
