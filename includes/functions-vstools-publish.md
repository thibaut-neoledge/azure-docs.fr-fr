Dans **l’Explorateur de solutions**, cliquez avec le bouton droit sur le projet, puis sélectionnez **Publier**. Choisissez **Créer** puis cliquez sur **Publier**. 

![Publier une nouvelle application de fonction](./media/functions-vstools-publish/functions-vstools-publish-new-function-app.png)

Si vous n’avez pas déjà connecté Visual Studio à votre compte Azure, cliquez sur **Ajouter un compte...**.  

Dans la boîte de dialogue **Créer une App Service**, utilisez les paramètres d’hébergement comme spécifiés dans le tableau. 

![Azure runtime local](./media/functions-vstools-publish/functions-vstools-publish.png)

| Paramètre      | Valeur suggérée  | Description                                |
| ------------ |  ------- | -------------------------------------------------- |
| **Nom de l’application** | Nom globalement unique | Nom qui identifie uniquement votre nouvelle application de fonction. |
| **Abonnement** | Choisissez votre abonnement | Sélectionnez l’abonnement Azure à utiliser. |
| **[Groupe de ressources](../articles/azure-resource-manager/resource-group-overview.md)** | myResourceGroup |  Nom du groupe de ressources où créer votre application de fonction. |
| **[Plan App Service](../articles/azure-functions/functions-scale.md)** | Plan de consommation | Veillez à choisir la **Consommation** sous **Taille** lorsque vous créez un nouveau plan.  |
| **[Compte de stockage](../articles/storage/storage-create-storage-account.md#create-a-storage-account)** | Nom globalement unique | Utilisez un compte de stockage existant ou créez-en un.   |

Cliquez sur **Créer** pour créer une application de fonction dans Azure avec ces paramètres. Une fois l’approvisionnement terminé, notez la valeur **URL du site**, qui est l’adresse de votre application de fonction dans Azure. 

![Azure runtime local](./media/functions-vstools-publish/functions-vstools-publish-profile.png)
