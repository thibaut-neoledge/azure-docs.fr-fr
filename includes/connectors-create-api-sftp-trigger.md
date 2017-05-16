Ajoutons un déclencheur.

1. Entrez *sftp* dans la zone de recherche du Concepteur d’applications logiques, puis sélectionnez le déclencheur **SFTP - Lors de l’ajout ou de la modification d’un fichier**.   
   ![Image du déclencheur SFTP 1](./media/connectors-create-api-sftp/trigger-1.png)  
2. Le contrôle **Lors de l’ajout ou de la modification d’un fichier** s’affiche.  
   ![Image du déclencheur SFTP 2](./media/connectors-create-api-sftp/trigger-2.png)  
3. Sélectionnez le **...** situé à droite du contrôle. Cette opération ouvre le contrôle de sélecteur de dossier   
   ![Image du déclencheur SFTP 3](./media/connectors-create-api-sftp/action-1.png)  
4. Sélectionnez **SFTP** pour sélectionner le dossier racine en tant que dossier à surveiller pour les fichiers nouveaux ou modifiés. Notez que le dossier racine est maintenant affiché dans le contrôle **Dossier** .  
   ![Image du déclencheur SFTP 4](./media/connectors-create-api-sftp/action-2.png)   

À ce stade, votre application logique a été configurée avec un déclencheur qui lance une série d’autres déclencheurs et actions dans le flux de travail lorsqu’un fichier est modifié ou créé dans le dossier SFTP spécifique. 

> [!NOTE]
> Pour qu’une application logique soit fonctionnelle, elle doit contenir au moins un déclencheur et une action. Suivez les étapes décrites dans la section suivante pour ajouter une action.  
> 
> 

