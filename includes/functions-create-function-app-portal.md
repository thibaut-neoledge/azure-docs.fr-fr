1. Cliquez sur le bouton **Nouveau** dans le coin supérieur gauche du portail Azure.

1. Cliquez sur **Calcul** > **Function App**, sélectionnez votre **Abonnement**. Ensuite, utilisez les paramètres de Function App comme indiqué dans la table.

    ![Créer une Function App dans le Portail Azure](./media/functions-create-function-app-portal/function-app-create-flow.png)

    | Paramètre      | Valeur suggérée  | Description                                        |
    | ------------ |  ------- | -------------------------------------------------- |
    | **Nom de l’application** | Nom globalement unique | Nom qui identifie votre nouvelle Function App. | 
    | **[Groupe de ressources](../articles/azure-resource-manager/resource-group-overview.md)** |  myResourceGroup | Nom du nouveau groupe de ressources dans lequel créer votre Function App. | 
    | **[Plan d’hébergement](../articles/azure-functions/functions-scale.md)** |   Plan de consommation | Plan d’hébergement qui définit la façon dont les ressources sont allouées à votre Function App. Dans la valeur par défaut **Plan de consommation**, les ressources sont ajoutées dynamiquement comme requis par vos fonctions. Vous payez uniquement pour la durée d’exécution de vos fonctions.   |
    | **Emplacement** | Europe de l’Ouest | Choisissez un emplacement près de chez vous ou près d’autres services auxquels vos fonctions accéderont. |
    | **[Compte de stockage](../articles/storage/common/storage-create-storage-account.md#create-a-storage-account)** |  Nom globalement unique |  Nom du nouveau compte de stockage utilisé par votre Function App. Les noms des comptes de stockage doivent comporter entre 3 et 24 caractères, uniquement des lettres minuscules et des chiffres. Vous pouvez également utiliser un compte existant. |

1. Cliquez sur **Créer** pour configurer et déployer la nouvelle Function App.