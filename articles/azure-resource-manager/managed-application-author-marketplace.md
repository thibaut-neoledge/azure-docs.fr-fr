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
ms.sourcegitcommit: 398efef3efd6b47c76967563251613381ee547e9
ms.openlocfilehash: 58ac7665abf7e75a43bb0b92bdf6f41005c3efe8
ms.contentlocale: fr-fr
ms.lasthandoff: 08/11/2017

---

# <a name="azure-managed-applications-in-the-marketplace"></a>Applications gérées Azure sur la Place de marché

 Les fournisseurs de services gérés, éditeurs de logiciels indépendants et les intégrateurs système (SI) peuvent utiliser les applications gérées Azure pour offrir leurs solutions à tous les clients d’Azure Marketplace. Ces solutions réduisent les opérations de maintenance et de mise à jour côté clients. Les éditeurs peuvent vendre des infrastructures et des logiciels via le Marketplace. Ils peuvent joindre des services et le support opérationnel pour les applications gérées. Pour plus d’informations, consultez [Vue d’ensemble des applications gérées](managed-application-overview.md).

Cet article explique comment un fournisseur de services gérés, un éditeur de logiciels indépendant ou un intégrateur système peut publier une application sur la Place de marché et la rendre disponible aux clients.

## <a name="prerequisites-for-publishing-a-managed-application"></a>Conditions préalables à la publication d’une application gérée

Conditions préalables à la publication sur la Place de marché :

* Techniques

    *  Pour en savoir plus sur la structure et la syntaxe de base des modèles Azure Resource Manager, consultez la page [Modèles Azure Resource Manager](resource-group-authoring-templates.md).
    *  Pour voir des solutions de modèle complet, consultez la page [Modèles de démarrage rapide Azure](https://azure.microsoft.com/en-us/documentation/templates/) ou le [référentiel GitHub Azure Quickstart Template](https://github.com/azure/azure-quickstart-templates).
    *  Pour plus d’informations sur la création de l’interface destinée aux clients déployant votre application via la Place de marché, consultez la page expliquant comment [Créer un fichier de définition d’interface utilisateur](managed-application-createuidefinition-overview.md).

* Non techniques (critères de l’entreprise)

    *   Votre entreprise (ou sa filiale) doit être située dans un pays expéditeur pris en charge par la Place de marché.
    *   Votre produit doit être disponible sous licence d’une manière compatible avec les modèles de facturation pris en charge par la Place de marché.
    *   Il vous incombe de consentir tous les efforts raisonnables pour mettre à la disposition de vos clients un support technique. Ce support peut être gratuit, payant ou accessible via une communauté.
    *   Il vous incombe de gérer les licences de vos logiciels et de toutes les dépendances de logiciels tiers.
    *   Vous devez fournir un contenu conforme aux critères définis pour que votre offre soit répertoriée sur la Place de marché et sur le portail Microsoft Azure.
    *   Vous devez accepter les termes de la Stratégie de participation et du Contrat d’éditeur de la Place de marché Microsoft Azure.
    *   Vous devez vous engager à respecter les Conditions d’utilisation, la Déclaration de confidentialité Microsoft et le Contrat relatif au programme Microsoft Azure Certified.

## <a name="create-a-new-azure-application-offer"></a>Création d’une offre d’application Azure

Une fois que les conditions préalables sont remplies, vous êtes prêt à créer votre offre d’application managée. Voici une brève présentation de ce que sont une offre et une référence (SKU).

### <a name="offer"></a>Offer

Une offre d’application gérée correspond à une classe d’offre de produit d’un éditeur. Si vous souhaitez rendre un nouveau type de solution/d’application disponible sur la Place de marché, vous pouvez en faire une nouvelle offre. Une offre est une collection de références (SKU). Toutes les offres apparaissent en tant qu’entités distinctes sur la Place de marché.

### <a name="sku"></a>SKU

Une référence (SKU) est la plus petite unité achetable d’une offre. Vous pouvez utiliser une référence (SKU) dans la même classe de produit (offre) à des fins de différentiation :

* Les différentes fonctionnalités prises en charge.
* Si l’offre est gérée ou non.
* Les modèles de facturation pris en charge.

Une référence (SKU) s’affiche sous l’offre parent dans la Place de marché. Elle s’affiche comme une entité distincte pouvant être achetée dans le portail Azure.

### <a name="set-up-an-offer"></a>Configuration d’une offre

1. Connectez-vous au [portail Cloud Partner](https://cloudpartner.azure.com/).

2. Dans le volet de navigation de gauche, sélectionnez **+ Nouvelle offre** > **Applications Azure**.

    ![Nouvelle offre](./media/managed-application-author-marketplace/newOffer.png)

3. Remplissez les formulaires qui s’affichent sur la gauche dans la vue **Éditeur**. Les champs obligatoires sont indiqués par un astérisque rouge (*).

    ![Paramètres de l’offre](./media/managed-application-author-marketplace/newOffer_OfferSettings.png)

    Quatre formes principales sont utilisées pour créer une application gérée :

    a. Paramètres de l’offre

    b. Références (SKU)

    c. Marketplace

    d. Support

Ces formulaires sont décrits de façon plus détaillée dans les sections suivantes.

## <a name="offer-settings-form"></a>Formulaire des paramètres de l’offre
Utilisez ce formulaire de base pour spécifier les paramètres de l’offre.

1. Remplissez le formulaire **Paramètres de l’offre**. Les différents champs sont les suivants :

    a. **Offer ID** (ID de l’offre) - Il s’agit d’un identificateur unique qui identifie l’offre au sein d’un profil d’éditeur. Cet ID est visible dans les URL de produit, les modèles Resource Manager et les états de facturation. Il ne peut comprendre que des caractères alphanumériques en minuscules ou des tirets (-). L’ID ne peut pas se terminer par un tiret. Il est limité à un maximum de 50 caractères. Ce champ est verrouillé une fois l’offre publiée.

    b. **Publisher ID** (ID de l’éditeur) : utilisez cette liste déroulante pour choisir le profil d’éditeur sous lequel vous voulez publier cette offre. Ce champ est verrouillé une fois l’offre publiée.

    c. **Nom** : ce nom d’affichage pour votre offre s’affiche dans le Marketplace et dans le portail. Il ne peut pas comprendre plus de 50 caractères. Incluez un nom de marque reconnaissable pour votre produit. N’incluez pas ici le nom de votre entreprise, sauf si c’est le nom sous lequel l’offre est commercialisée. Si vous commercialisez cette offre sur votre propre site web, assurez-vous que le nom présente rigoureusement le même aspect que sur votre site.

2. Sélectionnez **Save** (Enregistrer) pour enregistrer votre progression. 

## <a name="skus-form"></a>Formulaire de références (SKU)
L’étape suivante consiste à ajouter des références (SKU) pour votre offre.

1. Sélectionnez **Références** > **Nouvelle référence**. 

    ![Sélectionner option de nouvelle référence](./media/managed-application-author-marketplace/newOffer_skus.png)

2. Saisissez un **ID de référence**. Un SKU ID (ID de référence) est un identificateur unique pour la référence (SKU) au sein d’une offre. Cet ID est visible dans les URL de produit, les modèles Resource Manager et les états de facturation. Il ne peut comprendre que des caractères alphanumériques en minuscules ou des tirets (-). L’ID ne peut pas se terminer par un tiret et ne peut pas comprendre plus de 50 caractères. Ce champ est verrouillé une fois l’offre publiée. Vous pouvez avoir plusieurs références (SKU) au sein d’une même offre. Vous avez besoin d’une référence (SKU) pour chaque image que vous prévoyez de publier.

3. Remplissez la section **Détails de la référence** sur le formulaire suivant :

    ![Fournir une nouvelle référence (SKU)](./media/managed-application-author-marketplace/newOffer_newsku.png)

    Renseignez les champs suivants :
    
    a. **Titre** : saisissez un titre pour cette référence. Ce titre s’affiche dans la galerie pour cet élément.

    b. **Summary** (Résumé) : saisissez un bref résumé décrivant cette référence (SKU). Ce texte s’affiche en dessous du titre.

    c. **Description** : saisissez une description détaillée de la référence (SKU).

    d. **SKU Type** (Type de référence) - Les valeurs autorisées sont **Managed Application** (Application gérée) et **Solution Templates** (Modèles de solution). Dans le cas présent, sélectionnez **Managed Application** (Application gérée).

4. Remplissez la section **Détails du package** sur le formulaire suivant :

    ![Package](./media/managed-application-author-marketplace/newOffer_newsku_package.png)

    Renseignez les champs suivants :

    a. **Current Version** (Version actuelle) : saisissez la version du package chargé. Il doit respecter le format `{number}.{number}.{number}{number}`.

    b. **Select a package file** (Sélectionner un fichier de package) - Les fichiers suivants sont compressés dans un fichier .zip :
    * **applianceMainTemplate.json** : le fichier de modèle de déploiement utilisé pour déployer la solution/l’application. Pour plus d’informations sur la création de fichiers de modèle de déploiement, consultez [Créer votre premier modèle Azure Resource Manager](resource-manager-create-first-template.md).
    * **appliancecreateUIDefinition.json** : ce fichier est utilisé par le portail Azure pour générer l’interface utilisateur pour l’approvisionnement de cette solution/application. Pour plus d’informations, consultez la page [Prise en main de CreateUiDefinition](managed-application-createuidefinition-overview.md).
    * **mainTemplate.json** : ce fichier de modèle qui contient uniquement la ressource Microsoft.Solution/appliances. Le fichier mainTemplate inclut les propriétés suivantes :

        *  **kind** - Utiliser **Marketplace** pour les applications gérées de la Place de marché.
        *  **ManagedResourceGroupId** : ID du groupe de ressources de l’abonnement du client dans lequel toutes les ressources définies dans le fichier applianceMainTemplate.json sont déployées.
        *  **PublisherPackageId** : cette chaîne identifie de façon unique le package. Spécifiez cette valeur au format `{publisherId}.{OfferId}.{SKUID}.{PackageVersion}`.

Obtenez **l’ID d’offre** et **ID d’éditeur** à partir du portail de publication, comme illustré dans l’image suivante :

![ID de l’offre](./media/managed-application-author-marketplace/UniqueString_pubid_offerid.png)
        
Obtenez **l’ID de référence (SKU)** comme indiqué dans l’image suivante :

![RÉFÉRENCE (SKU)](./media/managed-application-author-marketplace/UniqueString_skuid.png)
        
Obtenez la **version** de package en procédant comme indiqué dans l’image suivante :

![Version du package](./media/managed-application-author-marketplace/UniqueString_packageversion.png)
    
  D’après les exemples précédents, la valeur de **PublisherPackageId** est `azureappliance-test.ravmanagedapptest.ravpreviewmanagedsku.1.0.0`.

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

* **Authorizations** - Cette propriété définit qui obtient l’accès et le niveau d’accès des utilisateurs aux ressources des abonnements clients. L’éditeur peut l’utiliser pour gérer l’application pour le compte du client.
* **PrincipalId** - Cette propriété représente l’identificateur Azure Active Directory (Azure AD) d’un utilisateur, d’un groupe d’utilisateurs ou d’une application auquel ou à laquelle certaines autorisations sont accordées sur les ressources de l’abonnement du client. La définition de rôle décrit les autorisations. 
* **Role Definition** - Cette propriété dresse une liste de tous les rôles RBAC intégrés fournis par Azure AD. Vous pouvez sélectionner le rôle le mieux adapté pour gérer les ressources pour le compte du client.

Vous pouvez ajouter plusieurs autorisations. Nous vous recommandons de créer un groupe d’utilisateurs AD et de spécifier son ID dans **PrincipalId**. De cette manière, vous pouvez ajouter plus d’utilisateurs au groupe d’utilisateurs sans avoir à mettre à jour la référence (SKU).

Pour plus d’informations sur le sujet, consultez [Bien démarrer avec RBAC dans le portail Azure](../active-directory/role-based-access-control-what-is.md).

## <a name="marketplace-form"></a>Formulaire Marketplace

Le formulaire Marketplace (Place de marché) permet de définir les champs qui s’affichent dans la [Place de marché Azure](https://azuremarketplace.microsoft.com) et sur le [portail Azure](https://portal.azure.com/).

### <a name="preview-subscription-ids"></a>ID d’abonnement pour version préliminaire

Entrez une liste d’ID d’abonnement Azure qui peuvent accéder à l’offre après sa publication. Vous pouvez utiliser ces abonnements autorisés pour tester l’offre préliminaire avant sa publication. Vous pouvez compiler une liste blanche de jusqu'à 100 abonnements dans le portail partenaire.

### <a name="suggested-categories"></a>Catégories suggérées

Sélectionnez dans la liste jusqu’à cinq catégories auxquelles votre offre peut être associée au mieux. Les catégories sélectionnées servent à mapper votre offre aux catégories de produits disponibles sur la [Place de marché Microsoft Azure](https://azuremarketplace.microsoft.com) et le [portail Azure](https://portal.azure.com/).

#### <a name="azure-marketplace"></a>Azure Marketplace

Les champs suivants s’affichent dans le résumé de votre application gérée :

![Résumé sur la place de marché](./media/managed-application-author-marketplace/publishvm10.png)

Les champs suivants s’affichent dans l’onglet **Vue d’ensemble** de votre application gérée :

![Présentation de la Place de marché](./media/managed-application-author-marketplace/publishvm11.png)

Les champs suivants s’affichent dans l’onglet **Abonnements + Tarification** de votre application gérée :

![Plans sur la place de marché](./media/managed-application-author-marketplace/publishvm15.png)

#### <a name="azure-portal"></a>Portail Azure

Les champs suivants s’affichent dans le résumé de votre application gérée :

![Résumé sur le portail](./media/managed-application-author-marketplace/publishvm12.png)

Les champs suivants s’affichent dans la vue d’ensemble de votre application gérée :

![Présentation du portail](./media/managed-application-author-marketplace/publishvm13.png)

#### <a name="logo-guidelines"></a>Instructions concernant le logo

Suivez ces instructions pour tout logo que vous téléchargez dans le portail partenaire cloud :

*   Le design Azure a une palette de couleurs simple. Limitez le nombre de couleurs primaires et secondaires sur votre logo.
*   Les couleurs de thème du portail sont le blanc et le noir. Évitez d’utiliser ces couleurs comme couleur d’arrière-plan pour votre logo. Utilisez une couleur qui donne à votre logo plus de visibilité dans le portail. Nous vous recommandons d’utiliser des couleurs primaires simples. *Si vous utilisez un arrière-plan transparent, assurez-vous que le logo et le texte ne sont pas blancs, noirs ou bleus.*
*   N'utilisez pas d’arrière-plan dégradé sur le logo.
*   Ne placez pas de texte sur le logo, pas même le nom de votre marque ou de votre entreprise. L'apparence de votre logo doit être « plate » et éviter les dégradés.
*   Assurez-vous que le logo n’est pas étiré.

#### <a name="hero-logo"></a>Bannière

La bannière est facultative. L’éditeur peut choisir de ne pas télécharger de bannière. Une fois l’icône de bannière téléchargée, elle ne peut pas être supprimée. À ce moment, le partenaire doit suivre les instructions relatives à la Place de marché pour les icônes.

Suivez ces recommandations concernant l’icône de bannière :

*   Le nom d’affichage de l’éditeur, le titre du plan et le résumé long de l’offre s’affichent en blanc. Ainsi, n’utilisez pas de couleur claire pour l’arrière-plan de l’icône de bannière. Les arrière-plans noirs, blancs et transparents ne sont pas autorisés pour les icônes.
*   Le nom d’affichage de l’éditeur, le titre du plan, le résumé long de l’offre et le bouton **Créer** sont incorporés automatiquement à la bannière une fois l’offre publiée. Par conséquent, ne saisissez pas de texte lorsque vous concevez le l’icône de bannière. Laissez un espace vide à droite, car le texte est inclus par programme dans cet espace. L’espace vide pour le texte doit être de 415 x 100 pixels sur la droite. Il est décalé de 370 pixels à partir de la gauche.

    ![Exemple d’icône de bannière](./media/managed-application-author-marketplace/publishvm14.png)

## <a name="support-form"></a>Formulaire de prise en charge

Remplissez le formulaire **Support** avec les contacts de support de votre entreprise. Ces informations peuvent être les contacts de support d’ingénierie et client.

## <a name="publish-an-offer"></a>Publication d’une offre

Après avoir rempli toutes les sections, sélectionnez **Publish** (Publier) pour mettre votre offre à disposition des clients.

## <a name="next-steps"></a>Étapes suivantes

* Pour voir une présentation des applications gérées, consultez [Vue d’ensemble des applications gérées](managed-application-overview.md).
* Pour plus d’informations sur l’utilisation d’applications gérées de la Place de marché, consultez l’article [Utilisation des applications gérées de la Place de marché Azure](managed-application-consume-marketplace.md).
* Pour plus d’informations sur la publication d’une application gérée de catalogue de services, consultez l’article [Créer et publier une application gérée de catalogue de services](managed-application-publishing.md).
* Pour plus d’informations sur l’utilisation d’une application gérée de catalogue de services, consultez l’article [Utiliser une application gérée du catalogue de services](managed-application-consumption.md).

