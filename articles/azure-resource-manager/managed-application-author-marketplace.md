---
title: "Applications gérées Azure sur la Place de marché | Microsoft Docs"
description: "Décrit les applications gérées Azure disponibles via la Place de marché."
services: azure-resource-manager
author: ravbhatnagar
manager: rjmax
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 07/09/2017
ms.author: gauravbh; tomfitz
ms.translationtype: HT
ms.sourcegitcommit: 54454e98a2c37736407bdac953fdfe74e9e24d37
ms.openlocfilehash: bd7880bdbca8cbf1abcab2cbade050876e26aea1
ms.contentlocale: fr-fr
ms.lasthandoff: 07/13/2017

---

# <a name="azure-managed-applications-in-the-marketplace"></a>Applications gérées Azure sur la Place de marché

Comme indiqué dans l’article [Vue d’ensemble des applications gérées Azure](managed-application-overview.md), les applications gérées de la Place de marché Microsoft Azure permettent aux fournisseurs de services gérés, aux éditeurs de logiciels indépendants et aux intégrateurs système d’offrir leurs solutions à tous les clients Azure. Ces solutions réduisent les opérations de maintenance et de mise à jour côté clients. Les éditeurs peuvent vendre des infrastructures et des logiciels via la Place de marché, et leur attacher des services et des contrats de support opérationnel. 

Cet article explique comment un fournisseur de services gérés, un éditeur de logiciels indépendant ou un intégrateur système peut publier une application sur la Place de marché et la rendre disponible aux clients.  

## <a name="pre-requisites-for-publishing-a-managed-application"></a>Conditions préalables à la publication d’une application gérée

Conditions préalables à la publication sur la Place de marché

* Techniques

    *  Pour en savoir plus sur la structure et la syntaxe de base des modèles Resource Manager, consultez la page [Création de modèles Azure Resource Manager](resource-group-authoring-templates.md).
    *  Pour voir des solutions de modèle complet, consultez la page [Modèles de démarrage rapide Azure](https://azure.microsoft.com/en-us/documentation/templates/) ou le [référentiel GitHub Azure Quickstart Template](https://github.com/azure/azure-quickstart-templates).
    *  Pour plus d’informations sur la création de l’interface destinée aux clients déployant votre application via la Place de marché, consultez la page expliquant comment [créer un fichier de définition d’interface utilisateur](managed-application-createuidefinition-overview.md).

* Non techniques (critères de l’entreprise)

    *   Votre entreprise (ou sa filiale) doit être située dans un pays expéditeur pris en charge par la Place de marché.
    *   Votre produit doit être disponible sous licence d’une manière compatible avec les modèles de facturation pris en charge par la Place de marché.
    *   Il vous incombe de consentir tous les efforts raisonnables pour mettre à la disposition de vos clients un support technique. Ce support peut être gratuit, payant ou accessible via une communauté.
    *   Il vous incombe de gérer les licences de vos logiciels et de toutes les dépendances de logiciels tiers.
    *   Vous devez fournir un contenu conforme aux critères définis pour que votre offre soit répertoriée sur la Place de marché Microsoft Azure et sur le portail Microsoft Azure.
    *   Vous devez accepter les termes de la Stratégie de participation et du Contrat d’éditeur de la Place de marché Microsoft Azure.
    *   Vous devez vous engager à respecter les Conditions d’utilisation, la Déclaration de confidentialité Microsoft et le Contrat relatif au programme Microsoft Azure Certified.

## <a name="how-to-create-a-new-azure-application-offer"></a>Comment créer une offre d’application Azure

Une fois les conditions préalables remplies, vous êtes prêt à démarrer la création de votre offre d’application gérée. Voici une brève présentation de ce que sont une offre et une référence (SKU).

### <a name="offer"></a>Offer

Une offre d’application gérée correspond à une classe d’offre de produit d’un éditeur. Si vous souhaitez rendre un nouveau type de solution/d’application disponible sur la Place de marché, vous pouvez en faire une nouvelle offre. Une offre est une collection de références (SKU). Toutes les offres apparaissent en tant qu’entités distinctes sur la Place de marché.

### <a name="sku"></a>SKU

Une référence (SKU) est la plus petite unité achetable d’une offre. À l’intérieur d’une même classe de produit (offre), les références (SKU) vous permettent de faire la distinction entre les fonctionnalités, la nature gérée ou non gérée de l’offre et les modèles de facturation pris en charge.

Une référence (SKU) s’affiche sous l’offre parent dans la Place de marché. Elle s’affiche comme une entité distincte pouvant être achetée dans le portail Azure.

### <a name="set-up-offer"></a>Configurer l’offre

1.  Connectez-vous au [portail Cloud Partner](https://cloudpartner.azure.com/).
2.  Dans la barre de navigation de gauche, cliquez sur **+ New offer** (+ Nouvelle offre), puis sélectionnez **Azure Applications** (Applications Azure).

    ![nouvelle offre](./media/managed-application-author-marketplace/newOffer.png)

3.  Un **Éditeur** de nouvelle offre s’affiche, et vous voilà prêt à commencer.

    ![paramètres de l’offre](./media/managed-application-author-marketplace/newOffer_OfferSettings.png)

4.  Les formulaires à compléter apparaissent sur la gauche en mode **Editor** (Éditeur). Chaque formulaire se compose d’un ensemble de champs à compléter. Les champs obligatoires sont indiqués par un astérisque rouge (*).

 **Il existe quatre formulaires principaux pour la création d’une application gérée**

    *   Paramètres de l’offre
    *   Références (SKU)
    *   Marketplace
    *   Support

Ces formulaires sont décrits de façon plus détaillée dans les sections suivantes.

## <a name="offer-settings-form"></a>Formulaire des paramètres de l’offre

Le formulaire Paramètres de l’offre est un formulaire de base. Les différents champs sont les suivants :

* Offer ID (ID de l’offre) - Il s’agit d’un identificateur unique pour l’offre au sein d’un profil d’éditeur. Cet ID est visible dans les URL de produit, les modèles Resource Manager et les états de facturation. Il ne peut comprendre que des caractères alphanumériques en minuscules ou des tirets (-). Il ne peut pas se terminer par un tiret et ne peut pas comprendre plus de 50 caractères. Ce champ est verrouillé une fois l’offre publiée.
* Publisher ID (ID de l’éditeur) - Cette liste déroulante vous permet de choisir le profil d’éditeur sous lequel vous voulez publier cette offre. Ce champ est verrouillé une fois l’offre publiée.
* Name (Nom) - Ceci est le nom d’affichage de votre offre. Il s’agit du nom qui s’affiche sur la Place de marché et dans le portail. Il ne peut pas comprendre plus de 50 caractères. Il est conseillé ici d’inclure un nom de marque reconnaissable pour votre produit. N’incluez pas ici le nom de votre entreprise, sauf si c’est le nom sous lequel l’offre est commercialisée. Si vous commercialisez cette offre sur votre propre site web, assurez-vous que le nom présente rigoureusement le même aspect que sur votre site.

Sélectionnez **Save** (Enregistrer) pour enregistrer votre progression. L’étape suivante consiste à ajouter des références (SKU) pour votre offre.

## <a name="skus-form"></a>Formulaire de références (SKU)

Sélectionnez le formulaire **SKUs** (Références). Vous trouverez ici l’option **New SKU** (Nouvelle référence). Cette option vous permet d’entrer un ID de référence dans le champ **SKU ID**.

![sélectionner option de nouvelle référence](./media/managed-application-author-marketplace/newOffer_skus.png)

Le **SKU ID** (ID de référence) est un identificateur unique pour la référence (SKU) au sein d’une offre. Cet ID est visible dans les URL de produit, les modèles Resource Manager et les états de facturation. Il ne peut comprendre que des caractères alphanumériques en minuscules ou des tirets (-). Il ne peut pas se terminer par un tiret et ne peut pas comprendre plus de 50 caractères. Ce champ est verrouillé une fois l’offre publiée. Vous pouvez avoir plusieurs références (SKU) au sein d’une même offre. Vous avez besoin d’une référence (SKU) pour chaque image que vous prévoyez de publier.

Après avoir sélectionné l’option **New SKU** (Nouvelle référence), vous devez remplir le formulaire suivant :

![fournir une nouvelle référence (SKU)](./media/managed-application-author-marketplace/newOffer_newsku.png)

### <a name="how-to-fill-sku-details-section"></a>Comment remplir la section des informations sur la référence (SKU)

* Title (Titre) - Indiquez un titre pour cette référence (SKU). C’est le titre qui s’affiche dans la galerie pour cet élément.
* Summary (Résumé) - Entrez un bref résumé décrivant cette référence (SKU). Ce texte s’affiche juste en dessous du titre.
* Description - Fournissez une description détaillée de la référence (SKU).
* SKU Type (Type de référence) - Les valeurs autorisées sont **Managed Application** (Application gérée) et **Solution Templates** (Modèles de solution). Dans le cas présent, sélectionnez **Managed Application** (Application gérée).

### <a name="how-to-fill-package-details-section"></a>Comment remplir la section des informations sur le package

Dans la section sur le package, les champs suivants doivent être remplis :

![package](./media/managed-application-author-marketplace/newOffer_newsku_package.png)

**Current Version** (Version actuelle) - Indiquez la version du package chargé. Il doit respecter le format suivant - `{number}.{number}.{number}{number}`

**Package file** (Fichier de package) - Les fichiers suivants sont compressés dans un fichier .zip.

* **applianceMainTemplate.json** - Le fichier de modèle de déploiement utilisé pour déployer la solution/l’application. Vous trouverez plus d’informations sur la création de fichiers de modèle de déploiement ici - [Créer votre premier modèle Azure Resource Manager](resource-manager-create-first-template.md)
* **appliancecreateUIDefinition.json** - Ce fichier est utilisé par le portail Azure pour générer l’interface utilisateur pour l’approvisionnement de cette solution/application. Pour plus d’informations, consultez la page [Prise en main de CreateUiDefinition](managed-application-createuidefinition-overview.md).
* **mainTemplate.json** - Le fichier de modèle qui contient uniquement la ressource Microsoft.Solution/appliances. Les propriétés principales de cette ressource sont les suivantes :

Le modèle principal inclut les propriétés suivantes :

*  kind - Utiliser **Marketplace** pour les applications gérées de la Place de marché
*  ManagedResourceGroupId - ID du groupe de ressources de l’abonnement du client dans lequel toutes les ressources définies dans le fichier applianceMainTemplate.json sont déployées.
*  PublisherPackageId - La chaîne qui identifie de façon unique le package. Spécifiez cette valeur au format `{publisherId}.{OfferId}.{SKUID}.{PackageVersion}`.
  Le publisherId et l’OfferId peuvent être obtenus à partir du portail de publication.

  ![ID de l’offre](./media/managed-application-author-marketplace/UniqueString_pubid_offerid.png)
        
  L’ID de référence (SKU) peut être obtenu comme indiqué dans l’image suivante :

  ![Id de référence (SKU)](./media/managed-application-author-marketplace/UniqueString_skuid.png)
        
  La version de package peut être obtenue comme indiqué dans l’image suivante :

  ![version du package](./media/managed-application-author-marketplace/UniqueString_packageversion.png)
    
  Donc d’après les exemples précédents, la valeur de **PublisherPackageId** est `azureappliance-test.ravmanagedapptest.ravpreviewmanagedsku.1.0.0`

  Exemple de fichier mainTemplate.json :

  ```json
  {
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "storageAccountNamePrefix": {
        "type": "string",
        "metadata": {
          "description": "Specify the name of the storage account"
        }
      },
      "storageAccountType": {
        "type": "string"
      }
    },
    "variables": {
      "managedResourceGroup": "[concat(resourceGroup().id,uniquestring(resourceGroup().id))]"
    },
    "resources": [{
      "type": "Microsoft.Solutions/appliances",
      "apiVersion": "2016-09-01-preview",
      "name": "[concat(parameters('storageAccountNamePrefix'), '-', 'managed')]",
      "location": "[resourceGroup().location]",
      "kind": "marketplace",
      "properties": {
        "managedResourceGroupId": "[variables('managedResourceGroup')]",
        "PublisherPackageId":"azureappliancetest.ravmanagedapptest.ravpreviewmanagedsku.1.0.0",
        "parameters": {
          "storageAccountName": {
            "value": "[parameters('storageAccountNamePrefix')]"
          },
          "storageAccountType": {
            "value": "[parameters('storageAccountType')]"
          }
        }
      }
    }],
    "outputs": {

    }
  }
  ```

Ce package doit contenir tous les autres modèles imbriqués ou scripts nécessaires à l’approvisionnement correct de cette application. Les fichiers mainTemplate.json, applianceMainTemplate.json et applianceCreateUIDefinition.json doivent se trouver dans le dossier racine.

**Authorizations** - Cette propriété définit le niveau d’accès des utilisateurs aux ressources des abonnements clients. Elle permet à l’éditeur de gérer l’application pour le compte du client.

* PrincipalId - Cette propriété représente l’identificateur Azure Active Directory d’un utilisateur, d’un groupe d’utilisateurs ou d’une application auquel ou à laquelle certaines autorisations sont accordées (comme indiqué par la définition de rôle) sur les ressources de l’abonnement des clients.
* Role Definition - Cette propriété dresse une liste de tous les rôles RBAC intégrés fournis par Azure AD. Vous pouvez sélectionner le rôle le mieux adapté et gérer les ressources pour le compte du client.

Plusieurs autorisations peuvent être ajoutées. Toutefois, il est recommandé de créer un groupe d’utilisateurs Active Directory et de spécifier son ID au niveau de la propriété **PrincipalId**. Cela permet d’ajouter davantage d’utilisateurs au groupe sans avoir à mettre la référence (SKU) à jour.

Pour plus d’informations sur le sujet, consultez [Bien démarrer avec le contrôle d’accès en fonction du rôle dans le portail Azure](../active-directory/role-based-access-control-what-is.md).

## <a name="marketplace-form"></a>Formulaire Marketplace

Le formulaire Marketplace (Place de marché) permet de définir les champs qui s’affichent dans la [Place de marché Azure](https://azuremarketplace.microsoft.com) et sur le [portail Azure](https://portal.azure.com/).

### <a name="preview-subscription-ids"></a>ID d’abonnement pour version préliminaire

Entrez ici la liste des ID d’abonnement Azure dont vous voulez qu’ils aient accès à l’offre dès sa publication. Ces abonnements autorisés vous permettent de tester l’offre préliminaire avant sa publication. Le portail Microsoft Cloud Partner vous permet d’autoriser jusqu’à 100 abonnements.

### <a name="suggested-categories"></a>Catégories suggérées

Sélectionnez dans la liste fournie jusqu’à cinq catégories auxquelles votre offre peut être associée au mieux. Les catégories sélectionnées servent à mapper votre offre aux catégories de produits disponibles sur la [Place de marché Microsoft Azure](https://azuremarketplace.microsoft.com) et le [portail](https://portal.azure.com/).

#### <a name="azure-marketplace"></a>Azure Marketplace

Les champs suivants s’affichent dans le résumé de votre application gérée :

![résumé sur la place de marché](./media/managed-application-author-marketplace/publishvm10.png)

Les champs suivants s’affichent dans la vue d’ensemble de votre application gérée :

![vue d’ensemble de la place de marché](./media/managed-application-author-marketplace/publishvm11.png)

Les champs suivants s’affichent dans les plans et la tarification de votre application gérée :

![plans sur la place de marché](./media/managed-application-author-marketplace/publishvm15.png)

#### <a name="azure-portal"></a>Portail Azure

Les champs suivants s’affichent dans le résumé de votre application gérée :

![résumé sur le portail](./media/managed-application-author-marketplace/publishvm12.png)

Les champs suivants s’affichent dans la vue d’ensemble de votre application gérée :

![vue d’ensemble sur le portail](./media/managed-application-author-marketplace/publishvm13.png)

#### <a name="logo-guidelines"></a>Instructions concernant le logo

Suivez les instructions suivantes pour tous les logos chargés sur le portail Microsoft Cloud Partner :

*   Le design Azure a une palette de couleurs simple. Limitez le nombre de couleurs primaires et secondaires sur votre logo.
*   Les couleurs de thème du portail sont le blanc et le noir. Par conséquent, évitez d’utiliser ces couleurs comme couleur d’arrière-plan pour vos logos. Utilisez des couleurs qui mettent vos logos en évidence dans le portail. Nous vous recommandons d’utiliser des couleurs primaires simples. **Si vous utilisez un arrière-plan transparent, assurez-vous que le logo/texte n’est pas blanc, noir ou bleu.**
*   N'utilisez pas d’arrière-plan dégradé sur le logo.
*   Évitez de placer du texte, même s’il s’agit du nom votre société ou de votre marque, sur le logo. L'apparence de votre logo doit être « plate » et éviter les dégradés.
*   Assurez-vous que le logo n’est pas étiré.

#### <a name="hero-logo"></a>Bannière

La bannière est facultative. L’éditeur peut choisir de ne pas télécharger de bannière. En revanche, une fois la bannière téléchargée, il n’est plus possible de la supprimer. À ce moment, le partenaire doit suivre les instructions relatives à la Place de marché pour les icônes.

##### <a name="guidelines-for-the-hero-logo-icon"></a>Recommandations concernant l’icône Bannière

*   Le nom d’affichage de l’éditeur, le titre du plan et le résumé long de l’offre s’affichent en couleur de police blanche. Par conséquent, vous devez éviter de conserver toute couleur claire dans l’arrière-plan l’icône. Les arrière-plans noirs, blancs et transparents ne sont pas autorisés pour les icônes.
*   Le nom d’affichage de l’éditeur, le titre du plan, le résumé long de l’offre et le bouton Créer sont incorporés automatiquement à la bannière une fois l’offre publiée. Vous ne devez par conséquent pas saisir de texte lorsque vous concevez la bannière. Laissez un espace vide à droite, car nous incluons le texte (nom d’affichage de l’éditeur, titre du plan, résumé détaillé de l’offre) par programmation à cet emplacement. L’espace vide pour le texte doit être de 415 x 100 sur la droite (décalé de 370 px à partir de la gauche).

![publishvm14](./media/managed-application-author-marketplace/publishvm14.png)

## <a name="support-form"></a>Formulaire de prise en charge

Le formulaire suivant à remplir est celui relatif à la prise en charge. Ce formulaire vous demande de spécifier les contacts de votre entreprise, tels que les contacts du support technique et du support client.

## <a name="how-to-publish-an-offer"></a>Comment publier une offre

Après avoir complété toutes les sections, sélectionnez **Publish** (Publier) pour mettre votre offre à disposition des clients.

## <a name="next-steps"></a>Étapes suivantes

* Pour voir une présentation des applications gérées, consultez [Vue d’ensemble des applications gérées Azure](managed-application-overview.md).
* Pour plus d’informations sur l’utilisation d’applications gérées de la Place de marché, consultez l’article [Utilisation des applications gérées de la Place de marché Azure](managed-application-consume-marketplace.md).
* Pour plus d’informations sur la publication d’une application gérée de catalogue de services, consultez l’article [Créer et publier une application gérée de catalogue de services](managed-application-publishing.md).
* Pour plus d’informations sur l’utilisation d’une application gérée de catalogue de services, consultez l’article [Utiliser une application gérée du catalogue de services](managed-application-consumption.md).

