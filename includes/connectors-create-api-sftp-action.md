Maintenant que vous avez ajouté un déclencheur, il est temps de faire quelque chose d’intéressant avec les données générées par le déclencheur. Procédez comme suit pour ajouter une action **SFTP - Extraire un dossier**. Cette action extrait le contenu d’un fichier si les conditions définies sont remplies.

Pour configurer cette action, vous devez fournir les informations suivantes. Vous remarquerez qu’il est facile d’utiliser des données générées par le déclencheur en tant qu’entrée pour certaines des propriétés du nouveau fichier :

| SFTP - propriété Extraire un dossier | Description |
| --- | --- |
| Chemin d’accès du fichier d’archive source |Il s’agit du chemin d’accès pour le fichier extrait. Vous pouvez sélectionner un des jetons à partir d’une action antérieure ou parcourir le serveur SFTP pour rechercher le chemin d’accès du fichier. |
| Chemin de destination du dossier |Il s’agit du chemin d’accès auquel seront placés les fichiers extraits. Vous pouvez sélectionner un des jetons à partir d’une action antérieure en tant que le chemin d’accès de destination ou parcourir le serveur SFTP et sélectionner un chemin d’accès. |
| Remplacer ? |Indique si le système doit remplacer ou non un éventuel fichier portant le même nom que le fichier extrait et présent dans le chemin d’accès de destination. |

Commençons par ajouter l’action pour extraire les fichiers si la condition définie précédemment correspond à *True*.

1. Sélectionnez **Ajouter une action**.  
   ![Image de condition d’action SFTP 6](./media/connectors-create-api-sftp/condition-6.png)  
2. Sélectionnez l’action **SFTP - Extraire un dossier**  
   ![Image de condition d’action SFTP 7](./media/connectors-create-api-sftp/condition-7.png)  
3. Sélectionnez **Chemin d’accès du fichier d’archive source**  
   ![Image de condition d’action SFTP 9](./media/connectors-create-api-sftp/condition-9.png)  
4. Sélectionnez le jeton **Chemin d’accès du fichier**. Cela indique que vous allez utiliser le chemin d’accès du fichier que le déclencheur a trouvé en tant que chemin du fichier archive source.  
   ![Image de condition d’action SFTP 10](./media/connectors-create-api-sftp/condition-10.png)  
5. Sélectionnez **Chemin d’accès du dossier**  
   ![Image de condition d’action SFTP 11](./media/connectors-create-api-sftp/condition-11.png)  
6. Sélectionnez le jeton **Chemin d’accès du fichier**. Cela indique que vous allez utiliser le chemin d’accès du fichier trouvé par le déclencheur en tant que le chemin d’accès de destination pour les fichiers extraits.
7. Saisissez*\\ExtractedFile* dans le contrôle **Chemin d’accès du dossier de destination**. Effectuez cela juste après le jeton de chemin d’accès de fichier dans le contrôle Chemin d’accès du dossier de destination.  
   ![Image de condition d’action SFTP 12](./media/connectors-create-api-sftp/condition-12.png)  
8. Saisissez *True* dans le contrôle **Remplacer ?* pour indiquer que les fichiers existants doivent être remplacés s’ils ont le même nom que les fichiers extraits.  
   ![Image de condition d’action SFTP 13](./media/connectors-create-api-sftp/condition-13.png)  
9. Enregistrez les modifications à votre flux de travail

<!---HONumber=AcomDC_0727_2016-->
