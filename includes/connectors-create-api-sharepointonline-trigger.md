Dans cet exemple, nous allons utiliser le déclencheur **SharePoint Online - Création d’un nouvel élément** pour initier un flux de travail d’application logique lorsqu’un nouvel élément est créé dans une liste SharePoint Online.

> [!NOTE]
> Vous serez invité à vous connecter à votre compte SharePoint si vous n’avez pas encore créé une *connexion* à SharePoint Online.  
> 
> 

1. Entrez *sharepoint* dans la zone de recherche sur le Concepteur d’applications logiques, puis sélectionnez le déclencheur **SharePoint Online - Création d’un nouvel élément**.  
   ![Image du déclencheur en ligne SharePoint](./media/connectors-create-api-sharepointonline/trigger-1.png)  
2. Le contrôle **Création d’un nouvel élément** s’affiche.  
   ![Image du déclencheur en ligne SharePoint 2](./media/connectors-create-api-sharepointonline/trigger-2.png)  
3. Sélectionnez une **URL de site**. Il s’agit du site SharePoint Online que vous souhaitez utiliser pour surveiller les nouveaux éléments déclenchant votre flux de travail.  
   ![Image du déclencheur en ligne SharePoint 3](./media/connectors-create-api-sharepointonline/trigger-3.png)  
4. Sélectionnez un **Nom de liste**. Il s’agit de la liste sur le site SharePoint Online que vous souhaitez utiliser pour surveiller les nouveaux éléments déclenchant le flux de travail.  
   ![Image du déclencheur en ligne SharePoint 4](./media/connectors-create-api-sharepointonline/trigger-4.png)  

À ce stade, votre application logique a été configurée avec un déclencheur qui lance une série d’autres déclencheurs et actions dans le flux de travail. Cela aura lieu chaque fois qu’un nouvel élément est créé dans la liste SharePoint Online que vous avez sélectionnée.  

<!---HONumber=AcomDC_0727_2016-->


