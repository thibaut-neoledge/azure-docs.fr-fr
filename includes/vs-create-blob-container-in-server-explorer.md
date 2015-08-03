Vous pouvez créer des conteneurs d’objets blob à l’aide l’**Explorateur de serveurs** Visual Studio

![Objets blob de l’Explorateur de serveurs][Image1]

1. Dans le menu **Affichage**, cliquez sur **Explorateur de serveurs**.
2. Dans l'Explorateur de serveurs, développez le nœud **Azure** pour votre abonnement, développez le nœud de **stockage** et le nœud pour le compte de stockage que vous avez spécifié dans le service connecté à Azure Storage.
3. Sélectionnez le nœud **Objets blob** et choisissez **Créer un conteneur d’objets blob** dans le menu contextuel.
4. Saisissez un nom pour le conteneur, puis sélectionnez **OK**.   

Le nouveau conteneur est privé par défaut et vous devez indiquer votre clé d’accès de stockage pour télécharger des objets blob depuis ce conteneur. Si vous souhaitez que les fichiers du conteneur soient publics, sélectionnez le conteneur dans l’**Explorateur de serveurs**, puis appuyez sur `F4` pour afficher la fenêtre des **Propriétés**. Configurez la propriété **Accès public en lecture** sur **Objet blob**. Tous les utilisateurs d’Internet peuvent afficher les objets blob d’un conteneur public, mais seuls ceux possédant la clé d’accès adéquate peuvent les modifier ou les supprimer.


[Image1]: ./media/vs-storage-getting-started-blobs-include/vs-storage-create-blob-containers-in-Server-Explorer.png

<!---HONumber=July15_HO4-->