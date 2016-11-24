Maintenant que vous avez ajouté un déclencheur, il est temps de faire quelque chose d’intéressant avec les données générées par le déclencheur. Procédez comme suit pour ajouter l’action **SharePoint Online - Créer un fichier** . Cette action crée un fichier dans SharePoint Online chaque fois que le nouveau déclencheur d’élément est activé. 

Pour configurer cette action, vous devez fournir les informations suivantes. Lorsque vous spécifiez ces informations, vous remarquerez qu’il est facile d’utiliser les données générées par le déclencheur en tant qu’entrée pour certaines des propriétés du nouveau fichier de données :

| Propriété Créer un fichier | Description |
| --- | --- |
| URL du site |Il s’agit de l’URL du site SharePoint Online sur lequel vous souhaitez créer le fichier. Sélectionnez le site dans la liste qui s’affiche. |
| Chemin d’accès du dossier |Il s’agit du dossier (situé à l’URL du site sélectionné à l’étape précédente) dans lequel le nouveau fichier sera placé. Recherchez et sélectionnez le dossier. |
| Nom de fichier |Il s’agit du nom du fichier en cours de création. |
| le contenu d’un fichier ; |Le contenu sera écrit dans le fichier. |

1. Sélectionnez **+ Nouvelle étape** pour ajouter l’action.  
   ![Image d’action en ligne SharePoint 1](./media/connectors-create-api-sharepointonline/action-1.png)  
2. Sélectionnez le lien **Ajouter une action** . Ouvre la zone de recherche dans laquelle vous pouvez rechercher l’action que vous souhaitez effectuer. Pour cet exemple, les actions SharePoint nous intéressent.    
   ![Image d’action en ligne SharePoint 2](./media/connectors-create-api-sharepointonline/action-2.png)    
3. Saisissez *sharepoint* pour rechercher des actions associées à SharePoint.
4. Sélectionnez **SharePoint Online - Créer un fichier** en tant qu’action à effectuer.   **Remarque**: vous serez invité à autoriser votre application logique à accéder à votre compte SharePoint si vous n’avez pas précédemment créé une connexion à SharePoint Online.    
   ![Image d’action en ligne SharePoint 3](./media/connectors-create-api-sharepointonline/action-3.png)    
5. Le contrôle **Créer un fichier** s’ouvre.   
   ![Image d’action en ligne SharePoint 4](./media/connectors-create-api-sharepointonline/action-4.png)     
6. Sélectionnez **URL du site** et recherchez le site sur lequel vous souhaitez créer le fichier.     
   ![Image d’action en ligne SharePoint 5](./media/connectors-create-api-sharepointonline/action-5.png)  
7. Sélectionnez **Chemin d’accès du dossier** et recherchez le dossier dans lequel le nouveau fichier sera placé.  
   ![Image d’action en ligne SharePoint 6](./media/connectors-create-api-sharepointonline/action-6.png)  
8. Sélectionnez le contrôle **Nom de fichier** et saisissez le nom du fichier que vous souhaitez créer. Ici, vous pouvez soit entrer le nom de fichier directement, soit utiliser les propriétés du déclencheur que vous avez créé précédemment. Vous pouvez faire cela en sélectionnant les propriétés à partir de la liste des **Sorties lors de la création d’un nouvel élément**. Cette liste s’affiche uniquement après avoir sélectionné le contrôle **Nom de fichier** . Dans cette procédure pas à pas, nous sélectionnons ID (l’ID du nouvel élément de liste) en tant que nom du fichier créé par l’action **SharePoint Online - Créer un fichier** .    
   ![Image d’action en ligne SharePoint 7](./media/connectors-create-api-sharepointonline/action-7.png)  
9. Sélectionnez le contrôle **Contenu du fichier** et saisissez le contenu qui sera écrit dans le fichier créé. Pour le contenu du fichier, notez que vous pouvez utiliser toute propriété du déclencheur que vous avez créé précédemment. Sélectionnez-le simplement dans la liste affichée. Vous pouvez également saisir le texte du **Contenu du fichier** directement dans le contrôle. Dans cet exemple, nous sélectionnons certaines propriétés et ajoutons des espaces et un tiret entre chaque propriété.        
   ![Image d’action en ligne SharePoint 8](./media/connectors-create-api-sharepointonline/action-8.png)  
10. Enregistrez les modifications à votre flux de travail  
11. Félicitations, vous avez maintenant une application logique entièrement fonctionnelle qui est déclenchée lorsqu’un nouvel élément est ajouté à une liste SharePoint Online. L’application crée alors un fichier à l’aide des propriétés du nouvel élément de liste.  Vous pouvez maintenant la tester en créant un nouvel élément dans la liste SharePoint. 



<!--HONumber=Nov16_HO3-->


