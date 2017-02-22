---
title: "Bien démarrer avec les modèles privés | Microsoft Docs"
description: "Ajoutez, gérez et partagez vos modèles privés à l’aide du portail Azure, l’interface de ligne de commande Azure ou PowerShell."
services: marketplace-customer
documentationcenter: 
author: VybavaRamadoss
manager: asimm
editor: 
tags: marketplace, azure-resource-manager
keywords: 
ms.assetid: 6ec20778-b578-4885-acb5-104b0e51ea1a
ms.service: marketplace
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/18/2016
ms.author: vybavar
translationtype: Human Translation
ms.sourcegitcommit: a9b48f149427e5ceb69bcaa97b1bf08519499b6f
ms.openlocfilehash: 01657619cbe579c6818a790cc3ab95a33936a565


---
# <a name="get-started-with-private-templates-on-the-azure-portal"></a>Prise en main des modèles privés sur le portail Azure
Un modèle [Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md) est un modèle déclaratif utilisé pour définir votre déploiement. Vous pouvez définir les ressources à déployer pour une solution et spécifier les paramètres et variables qui permettent d’entrer des valeurs pour les différents environnements. Le modèle se compose de JSON et d'expressions que vous pouvez utiliser pour construire des valeurs pour votre déploiement.

Dans le [portail Azure](https://portal.azure.com), vous pouvez utiliser la nouvelle fonctionnalité **Modèles** ainsi que le fournisseur de ressources **Microsoft.Gallery** comme extension de la [Place de marché Azure](https://azure.microsoft.com/marketplace/) pour permettre aux utilisateurs de créer, gérer et déployer des modèles privés à partir d’une bibliothèque personnelle.

Ce document vous familiarise avec l’ajout, la gestion et le partage d’un **Modèle** privé à l’aide du portail Azure.

## <a name="guidance"></a>Assistance
Les suggestions suivantes vous aideront à tirer le meilleur parti des **Modèles** lorsque vous travaillez avec vos solutions :

* Un **Modèle** est une ressource d’encapsulation qui contient un modèle Resource Manager et des métadonnées supplémentaires. Il fonctionne d’une manière semblable à un élément dans Azure Marketplace. La principale différence est qu’il s’agit d’un élément privé, contrairement aux éléments Marketplace publics.
* La bibliothèque des **Modèles** convient aux utilisateurs qui souhaitent personnaliser leurs déploiements.
* **Modèles** conviennent aux utilisateurs ayant besoin d’un référentiel simple dans Azure.
* Commencez par un modèle Resource Manager existant. Recherchez des modèles dans [github](https://github.com/Azure/azure-quickstart-templates) ou [exportez des modèles](../azure-resource-manager/resource-manager-export-template.md) à partir d’un groupe de ressources existant.
* **Modèles** sont liés à l’utilisateur qui les publie. Le nom de cet éditeur est visible par quiconque disposant d’un accès en lecture à celui-ci.
* **Modèles** sont des ressources Azure Resource Manager et ne peuvent être renommés une fois publiés.

## <a name="add-a-template-resource"></a>Ajouter une ressource de Modèle
Une ressource de **Modèle** peut être créée de deux manières sur le portail Azure.

### <a name="method-1--create-a-new-template-resource-from-a-running-resource-group"></a>Méthode 1 : Créer une ressource de Modèle à partir d’un groupe de ressources en cours d’exécution
1. Accédez à un groupe de ressources existant sur le portail Azure. Sélectionnez **Exporter le modèle** dans **Paramètres**.
2. Une fois le modèle Resource Manager exporté, cliquez sur le bouton **Enregistrer le modèle** pour l’enregistrer dans le dépôt **Modèles**. Cliquez [ici](../azure-resource-manager/resource-manager-export-template.md)pour obtenir des informations complètes sur l’exportation des modèles.
   <br /><br />
   ![Exportation de groupe de ressources](media/rg-export-portal1.PNG)  <br />
3. Cliquez sur le bouton de commande **Enregistrer comme modèle** .
   <br /><br />
4. Entrez les informations suivantes :
   
   * Nom : nom de l’objet du modèle (REMARQUE : il s’agit d’un nom Azure Resource Manager. Toutes les restrictions d’affectation de noms s’appliquent et ces derniers ne peuvent être modifiés une fois créés).
   * Description : résumé des informations du modèle.
     
     ![Enregistrer un Modèle](media/save-template-portal1.PNG)  <br />
5. Cliquez sur **Enregistrer**.
   
   > [!NOTE]
   > Le panneau Exporter le modèle affiche une notification lorsque le modèle Resource Manager exporté comporte des erreurs. Vous serez cependant toujours en mesure d’enregistrer ce modèle Resource Manager dans le référentiel des Modèles. Vérifiez et corrigez tout problème dans le modèle Resource Manager avant de redéployer le modèle Resource Manager exporté.
   > 
   > 

### <a name="method-2--add-a-new-template-resource-from-browse"></a>Méthode 2: Ajouter une nouvelle ressource de Modèle à partir du panneau Parcourir
Vous pouvez également ajouter un nouveau **Modèle** à l’aide du bouton de commande +Ajouter dans **Parcourir > Modèles**. Vous devez fournir un nom, une description ainsi que le JSON du modèle Resource Manager.

![Ajouter un Modèle](media/add-template-portal1.PNG)  <br />

> [!NOTE]
> Microsoft.Gallery est un fournisseur de ressources Azure basé sur le client. La ressource de Modèle est liée à l’utilisateur qui l’a créée. Cette dernière n’est pas liée à un abonnement spécifique. Un abonnement doit être défini uniquement lors du déploiement d’un modèle.
> 
> 

## <a name="view-template-resources"></a>Afficher les ressources de Modèle
Tous les **Modèles** disponibles peuvent être consultés en accédant à **Parcourir > Modèles**. Cela inclut les **Modèles** que vous avez créés, ainsi que ceux ayant été partagés avec vous avec différents niveaux d’autorisation. Pour obtenir plus d’informations, consultez la section [Contrôle d’accès](#access-control-for-a-tenant-resource-provider) ci-dessous.

![Afficher le Modèle](media/view-template-portal1.PNG)  <br />

Vous pouvez afficher les détails d’un **Modèle** en cliquant sur un élément dans la liste.

![Afficher le Modèle](media/view-template-portal2c.png)  <br />

## <a name="edit-a-template-resource"></a>Modifier une ressource de Modèle
Vous pouvez lancer le flux de modification d’un **Modèle** en cliquant avec le bouton droit sur l’élément dans la liste Parcourir ou en cliquant sur le bouton de commande Modifier.

![Modifier un Modèle](media/edit-template-portal1a.PNG)  <br />

Vous pouvez modifier la description ou le texte du modèle Resource Manager. Vous ne pouvez pas modifier le nom car il s’agit d’un nom de ressource Resource Manager. Lorsque vous modifiez le JSON du modèle Resource Manager, nous validerons l’opération pour vérifier la validité du JSON. Cliquez sur **OK**, puis sur **Enregistrer** pour enregistrer votre modèle mis à jour.

![Modifier un Modèle](media/edit-template-portal2a.PNG)  <br />

Une notification de confirmation s’affiche lorsque le **Modèle** est enregistré.

![Modifier un Modèle](media/edit-template-portal3b.png)  <br />

## <a name="deploy-a-template-resource"></a>Déployer une ressource de modèle
Vous pouvez déployer tout **Modèle** pour lequel vous disposez d’autorisations de **lecture**. Le flux de déploiement lance le panneau Déploiement de Modèle Azure standard. Renseignez les valeurs des paramètres du modèle Resource Manager afin de poursuivre le déploiement.

![Déployer un modèle](media/deploy-template-portal1b.png)  <br />

## <a name="share-a-template-resource"></a>Partager une ressource de Modèle
Une ressource de **Modèle** peut être partagée avec vos homologues. Le partage fonctionne d’une manière semblable à l’ [attribution de rôle pour n’importe quelle ressource dans Azure](../active-directory/role-based-access-control-configure.md). Le propriétaire du **Modèle** fournit des autorisations aux autres utilisateurs, qui peuvent interagir avec une ressource de Modèle. La personne ou le groupe de personnes avec lesquelles vous partagez le **Modèle** pourront visualiser le modèle Ressource Manager et ses propriétés de galerie.

### <a name="access-control-for-the-microsoftgallery-resources"></a>Contrôle d’accès pour les ressources Microsoft.Gallery
| Rôle | Autorisations |
| --- | --- |
| Propriétaire |Permet un contrôle total de la ressource de Modèle, y compris de l’option Partager |
| Lecteur |Permet la lecture et l’exécution (Déployer) sur la ressource de Modèle |
| Collaborateur |Rend possibles les permissions de modification et de suppression sur la ressource de Modèle. L’utilisateur ne peut pas partager le Modèle avec d’autres utilisateurs |

Sélectionnez **Partager** dans le volet de navigation d’un élément spécifique ou cliquez avec le bouton droit sur ce dernier pour sélectionner l’option. Cette opération lance une expérience de partage.

![Partager le modèle](media/share-template-portal1a.png)  <br />

 Vous pouvez à présent choisir un rôle et un utilisateur/groupe auquel fournir l’accès à un **Modèle**donné. Les rôles disponibles sont Propriétaire, Lecteur et Collaborateur. Pour plus d’informations, consultez la section [Contrôle d’accès](#access-control-for-a-tenant-resource-provider) ci-dessus.

![Partager le modèle](media/share-template-portal2b.png)  <br />

![Partager le modèle](media/share-template-portal3b.png)  <br />

Cliquez sur **Sélectionner**, puis sur **OK**. Vous pouvez à présent afficher les utilisateurs ou groupes que vous avez ajoutés à la ressource.

![Partager le modèle](media/share-template-portal4b.png)  <br />

> [!NOTE]
> Un Modèle peut uniquement être partagé avec les utilisateurs et les groupes se trouvant dans le même client Azure Active Directory. Si vous partagez un Modèle avec une adresse e-mail absente de votre client, une invitation sera envoyée à l’utilisateur pour joindre le client en tant qu’invité.
> 
> 

## <a name="next-steps"></a>Étapes suivantes
* Pour en savoir plus sur la création de modèles Resource Manager, consultez [Création de modèles](../azure-resource-manager/resource-group-authoring-templates.md)
* Pour mieux comprendre les fonctions que vous pouvez utiliser dans un modèle Resource Manager, voir [Fonctions des modèles](../azure-resource-manager/resource-group-template-functions.md)
* Pour obtenir des instructions sur la conception de vos modèles, consultez [Meilleures pratiques relatives à la conception des modèles Azure Resource Manager](../azure-resource-manager/best-practices-resource-manager-design-templates.md)




<!--HONumber=Feb17_HO3-->


