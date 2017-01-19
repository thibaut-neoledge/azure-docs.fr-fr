Maintenant que vous avez ajouté un déclencheur, il est temps de faire quelque chose d’intéressant avec les données générées par le déclencheur. Procédez comme suit pour ajouter une action **SharePoint Online - Créer un fichier** . Cette action crée un fichier dans SharePoint Online chaque fois que le nouveau déclencheur d’élément est activé. 

Pour configurer cette action, vous devez fournir les informations suivantes. Vous remarquerez qu’il est facile d’utiliser des données générées par le déclencheur en tant qu’entrée pour certaines des propriétés du nouveau fichier :

| Propriété Créer un fichier | Description |
| --- | --- |
| URL du site |Il s’agit de l’URL du site SharePoint Online sur lequel vous souhaitez créer le fichier. Sélectionnez le site dans la liste qui s’affiche. |
| Chemin d’accès du dossier |Il s’agit du dossier (à l’URL du Site) d’emplacement du nouveau fichier. Recherchez et sélectionnez le dossier. |
| Nom de fichier |Il s’agit du nom du fichier en cours de création. |
| le contenu d’un fichier ; |Le contenu sera écrit dans le fichier. |

1. Sélectionnez **+ Nouvelle étape** pour ajouter l’action.  
   ![](./media/connectors-create-api-sharepointonline/action-1.png)  
2. Sélectionnez le lien **Ajouter une action** . Ouvre la zone de recherche dans laquelle vous pouvez rechercher l’action que vous souhaitez effectuer. Pour cet exemple, les actions SharePoint nous intéressent.    
   ![](./media/connectors-create-api-sharepointonline/action-2.png)    
3. Saisissez *sharepoint* pour rechercher des actions associées à SharePoint.
4. Sélectionnez **SharePoint Online - Créer un fichier** en tant qu’action à effectuer.   **Remarque**: vous êtes invité à autoriser votre application logique à accéder à votre compte SharePoint si vous ne l'avez pas fait précédemment.    
   ![](./media/connectors-create-api-sharepointonline/action-3.png)    
5. Le contrôle **Créer un fichier** s’ouvre.   
   ![](./media/connectors-create-api-sharepointonline/action-4.png)     
6. Sélectionnez **URL du site** et recherchez le site sur lequel vous souhaitez créer le fichier.     
   ![](./media/connectors-create-api-sharepointonline/action-5.png)  
7. Sélectionnez **Chemin d’accès du dossier** et recherchez le dossier dans lequel le nouveau fichier sera placé.  
   ![](./media/connectors-create-api-sharepointonline/action-6.png)  
8. Sélectionnez le contrôle **Nom de fichier** et saisissez le nom du fichier que vous souhaitez créer. Pour le nom du fichier, notez que vous pouvez utiliser toute propriété du déclencheur que vous avez créé précédemment en le sélectionnant simplement dans la liste affichée.     
   ![](./media/connectors-create-api-sharepointonline/action-7.png)  
9. Sélectionnez le contrôle **Contenu du fichier** et saisissez le contenu qui sera écrit dans le fichier créé. Pour le contenu du fichier, notez que vous pouvez utiliser toute propriété du déclencheur que vous avez créé précédemment. Sélectionnez-le simplement dans la liste affichée. Vous pouvez également saisir le texte du **Contenu du fichier** directement dans le contrôle. Dans cet exemple, nous sélectionnons certaines propriétés et ajoutons des espaces et un tiret entre chaque propriété.        
   ![](./media/connectors-create-api-sharepointonline/action-8.png)  
10. Enregistrez les modifications à votre flux de travail  



<!--HONumber=Jan17_HO3-->


