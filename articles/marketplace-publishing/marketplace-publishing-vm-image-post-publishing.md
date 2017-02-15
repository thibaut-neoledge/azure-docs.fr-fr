---
title: "Gestion de l’image de machine virtuelle sur Azure Marketplace | Microsoft Docs"
description: "Guide détaillé sur la gestion de votre image de machine virtuelle sur Azure Marketplace après la publication initiale."
services: Azure Marketplace
documentationcenter: 
author: HannibalSII
manager: hascipio
editor: 
ms.assetid: cc8648d4-59c2-4678-b47d-992300677537
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: Azure
ms.workload: na
ms.date: 08/03/2016
ms.author: hascipio;
translationtype: Human Translation
ms.sourcegitcommit: 5919c477502767a32c535ace4ae4e9dffae4f44b
ms.openlocfilehash: ed2921750f93f344a4c3dbef31d9f523dedc0aae


---
# <a name="post-production-guide-for-virtual-machine-offers-in-the-azure-marketplace"></a>Guide de post-production pour les offres de machine virtuelle dans Azure Marketplace
Cet article explique comment vous pouvez mettre à jour une offre de machine virtuelle en direct dans Azure Marketplace. Il vous guide également dans le processus d’ajout d’une ou de plusieurs nouvelles références (SKU) à une offre existante, et dans le processus de suppression d’une offre de machine virtuelle en direct ou d’une référence (SKU) dans Azure Marketplace.

Une fois qu’une offre/référence est présentée dans le [portail Azure](http://portal.azure.com), vous ne pouvez pas modifier les champs ci-dessous :

* **Identificateur de l’offre :** [Portail de publication -> Machines virtuelles -> Sélectionnez votre offre -> onglet Images de machines virtuelles -> Identificateur de l’offre]
* **Identificateur de référence :** [Portail de publication -> Machines virtuelles -> Sélectionnez votre offre -> Onglet Références -> Ajouter une référence]
* **Espace de noms d’éditeur :** [Portail de publication -> Machines virtuelles -> Onglet Procédure pas à pas (en « Étape 2 : inscrire votre entreprise ») -> Espace de noms d’éditeur -> Espace de noms]

Une fois qu’une offre/référence est présentée dans [Azure Marketplace](http://azure.microsoft.com/marketplace), vous ne pouvez pas modifier les champs ci-dessous :

* **Identificateur de l’offre :** [Portail de publication -> Machines virtuelles -> Sélectionnez votre offre -> onglet Images de machines virtuelles -> Identificateur de l’offre]
* **Identificateur de référence :** [Portail de publication -> Machines virtuelles -> Sélectionnez votre offre -> Onglet Références -> Ajouter une référence]
* **Espace de noms d’éditeur :** [Portail de publication -> Machines virtuelles -> Onglet Procédure pas à pas (en Étape 2 : inscription) -> Espace de noms d’éditeur -> Espace de noms]
* **Ports :** [Portail de publication -> Machines virtuelles -> Sélectionnez votre offre -> Onglet Images de machines virtuelles -> Ports ouverts]
* **Modification de la tarification des références répertoriées**
* **Modification du modèle de facturation des références répertoriées**
* **Suppression des régions de facturation des références répertoriées**
* **Modification du nombre de disques de données des références répertoriées**

## <a name="1-how-to-update-the-technical-details-of-a-sku"></a>1. Comment mettre à jour les détails techniques d’une référence (SKU)
Vous pouvez ajouter une nouvelle version à la référence répertoriée et republier votre offre en suivant les étapes ci-dessous :

1. Connectez-vous au [portail de publication](https://publish.windowsazure.com).
2. Accédez à l’onglet **MACHINES VIRTUELLES** et sélectionnez votre offre.
3. Dans le menu de gauche, cliquez sur l’onglet **IMAGES DE MACHINES VIRTUELLES** .
4. À partir de la section **RÉFÉRENCES (SKU)** de l’onglet **IMAGES DE MACHINES VIRTUELLES** , recherchez la référence (SKU) que vous souhaitez mettre à jour.
5. Ensuite, ajoutez le nouveau numéro de version de la référence et cliquez sur le bouton **« + »** . La nouvelle version doit être au format X.Y.Z, où X, Y et Z sont des entiers. Les modifications de la version doivent uniquement être incrémentielles.
6. Dans la zone **URL de disque dur virtuel de système d’exploitation** , ajoutez l’URI de la signature d’accès partagé créé pour le disque dur virtuel du système d’exploitation, puis enregistrez les modifications.
   
   > [!IMPORTANT]
   > Vous ne pouvez pas incrémenter/décrémenter le nombre de disques de données d’une référence répertoriée. Vous devez dans ce cas créer une nouvelle référence. Reportez-vous à la section [3. Comment ajouter une nouvelle référence (SKU) sous une offre répertoriée](#3-how-to-add-a-new-sku-under-a-live-offer) pour obtenir une aide précise.
   > 
   > 
7. Après avoir effectué les modifications, accédez à l’onglet **PUBLIER** et cliquez sur le bouton **DÉPLOYER DANS UN ENVIRONNEMENT INTERMÉDIAIRE**. Pour des instructions détaillées sur le test de votre offre dans l’environnement intermédiaire, reportez-vous à ce [lien](marketplace-publishing-vm-image-test-in-staging.md)
8. Une fois que vous avez testé votre offre dans l’environnement de préproduction, accédez à l’onglet **PUBLIER** dans le portail de publication et cliquez sur le bouton **DEMANDER UNE APPROBATION POUR LANCER LA PRODUCTION** pour republier votre offre dans Azure Marketplace.
   
    ![dessin](media/marketplace-publishing-vm-image-post-publishing/img01_07.png)

## <a name="2-how-to-update-the-non-technical-details-of-an-offer-or-a-sku"></a>2. Comment mettre à jour les détails non techniques d’une offre ou d’une référence (SKU)
Vous pouvez mettre à jour les détails non techniques (marketing, juridique, support, catégories) de votre offre en direct ou de votre référence (SKU) dans Azure Marketplace.

### <a name="21-update-the-offer-description-and-logos"></a>2.1 Mettre à jour la description et les logos de l’offre
Vous pouvez mettre à jour les détails de l’offre et la republier en suivant les étapes ci-dessous :

1. Connectez-vous au [portail de publication](https://publish.windowsazure.com).
2. Accédez à l’onglet **MACHINES VIRTUELLES** et sélectionnez votre offre.
3. Dans le menu de gauche, cliquez sur l’onglet **MARKETING** .
4. Cliquez sur le bouton **FRANÇAIS (FRANCE)** .
5. Dans le menu de gauche, cliquez sur l’onglet **DÉTAILS** . Sous la section *DESCRIPTION* de l’onglet **DÉTAILS** , vous pouvez mettre à jour le titre, le résumé bref et le résumé long de l’offre, puis enregistrer les modifications.
   
   > [!NOTE]
   > Tenez compte des éléments suivants lorsque vous mettez à jour les détails de la référence (SKU).
   > **N’entrez pas deux fois le même texte dans la description de l’offre et de la référence (SKU). N’entrez pas deux fois le même texte dans le titre de la référence (SKU) et dans le résumé long de l’offre. N’entrez pas deux fois le même texte dans le titre de la référence (SKU) et dans le résumé de l’offre.**
   > 
   > 
   
    ![dessin](media/marketplace-publishing-vm-image-post-publishing/img02.1_05.png)
6. Sous la section *LOGOS* de l’onglet **DÉTAILS** , vous pouvez mettre à jour les logos. Toutefois, assurez-vous que les logos respectent les [instructions Azure Marketplace](marketplace-publishing-push-to-staging.md#step-1-provide-marketplace-marketing-content) (reportez-vous à la section Étape 1 : Fournir du contenu marketing à Marketplace -> Détails -> Instructions relatives aux logos Azure Marketplace).
   
   > [!NOTE]
   > La bannière est facultative. Vous pouvez choisir de ne pas télécharger de bannière. Cependant, une fois la bannière téléchargée, aucune disposition ne permet de la supprimer du portail de publication. Dans ce cas, vous devez suivre les [instructions relatives à la bannière](marketplace-publishing-push-to-staging.md#step-1-provide-marketplace-marketing-content) (reportez-vous à la section Étape 1 : Fournir du contenu marketing à Marketplace -> Détails -> Instructions supplémentaires relatives aux bannières).
   > 
   > 
7. Après avoir effectué les modifications, accédez à l’onglet **PUBLIER** et cliquez sur le bouton **DÉPLOYER DANS UN ENVIRONNEMENT INTERMÉDIAIRE**. Pour des instructions détaillées sur le test de votre offre dans l’environnement intermédiaire, reportez-vous à ce [lien](marketplace-publishing-vm-image-test-in-staging.md).
8. Une fois que vous avez testé votre offre dans l’environnement de préproduction, accédez à l’onglet **PUBLIER** dans le portail de publication et cliquez sur le bouton **DEMANDER UNE APPROBATION POUR LANCER LA PRODUCTION** pour republier votre offre dans Azure Marketplace.
   
    ![dessin](media/marketplace-publishing-vm-image-post-publishing/img02.1_08.png)

### <a name="22-update-the-sku-description"></a>2.2. Mettre à jour la description de la référence (SKU)
Vous pouvez mettre à jour les détails de la référence (SKU) et republier votre offre en suivant les étapes ci-dessous :

1. Connectez-vous au [portail de publication](https://publish.windowsazure.com)
2. Accédez à l’onglet **MACHINES VIRTUELLES** et sélectionnez votre offre.
3. Dans le menu de gauche, cliquez sur l’onglet **MARKETING** .
4. Cliquez sur le bouton **FRANÇAIS (FRANCE)** .
5. Dans le menu de gauche, cliquez sur l’onglet **PLANS** . Sous la section *RÉFÉRENCES (SKU)* de l’onglet **PLANS** , vous pouvez mettre à jour le titre, le résumé et la description de la référence (SKU), puis enregistrer les modifications.
   
   > [!NOTE]
   > Tenez compte des éléments suivants lorsque vous mettez à jour les détails de la référence (SKU). **N’entrez pas deux fois le même texte dans la description de l’offre et de la référence (SKU). N’entrez pas deux fois le même texte dans le titre de la référence (SKU) et dans le résumé long de l’offre. N’entrez pas deux fois le même texte dans le titre de la référence (SKU) et dans le résumé de l’offre.**
   > 
   > 
6. Après avoir effectué les modifications, accédez à l’onglet **PUBLIER** et cliquez sur le bouton **DÉPLOYER DANS UN ENVIRONNEMENT INTERMÉDIAIRE**. Pour des instructions détaillées sur le test de votre offre dans l’environnement intermédiaire, reportez-vous à ce lien
7. Une fois que vous avez testé votre offre dans l’environnement de préproduction, accédez à l’onglet **PUBLIER** dans le portail de publication et cliquez sur le bouton **DEMANDER UNE APPROBATION POUR LANCER LA PRODUCTION** pour republier votre offre dans Azure Marketplace.
   
    ![dessin](media/marketplace-publishing-vm-image-post-publishing/img02.2_07.png)

### <a name="23-change-the-existing-links-or-add-new-links"></a>2.3 Modifier les liens existants ou ajouter de nouveaux liens
Vous pouvez modifier les liens existants ou ajouter de nouveaux liens, puis republier votre offre en suivant les étapes ci-dessous :

1. Connectez-vous au [portail de publication](https://publish.windowsazure.com)
2. Accédez à l’onglet **MACHINES VIRTUELLES** et sélectionnez votre offre.
3. Dans le menu de gauche, cliquez sur l’onglet **MARKETING** .
4. Cliquez sur le bouton **FRANÇAIS (FRANCE)** .
5. Dans le menu de gauche, cliquez sur l’onglet **LIENS** .
6. Si vous souhaitez ajouter un nouveau lien, sous la section *LIENS* , cliquez sur le bouton **AJOUTER UN LIEN** . La boîte de dialogue *Ajouter un lien* s’ouvre. Dans cette boîte de dialogue, vous pouvez ajouter le titre et les champs URL du lien, puis enregistrer les modifications. Vous pouvez entrer n’importe quel lien contenant des informations utiles aux clients.
7. Si vous souhaitez mettre à jour ou supprimer un lien existant, sélectionnez le lien approprié, puis cliquez sur le bouton Modifier ou sur le bouton Supprimer.
   
   > [!NOTE]
   > Assurez-vous que les liens que vous avez saisis dans cette section fonctionnent correctement, car ceux-ci sont validés lors du processus de demande de production.
   > 
   > 
8. Après avoir effectué les modifications, accédez à l’onglet **PUBLIER** et cliquez sur le bouton **DÉPLOYER DANS UN ENVIRONNEMENT INTERMÉDIAIRE**. Pour des instructions détaillées sur le test de votre offre dans l’environnement intermédiaire, reportez-vous à ce [lien](marketplace-publishing-vm-image-test-in-staging.md).
9. Une fois que vous avez testé votre offre dans l’environnement de préproduction, accédez à l’onglet **PUBLIER** dans le portail de publication et cliquez sur le bouton **DEMANDER UNE APPROBATION POUR LANCER LA PRODUCTION** pour republier votre offre dans Azure Marketplace.
   
    ![dessin](media/marketplace-publishing-vm-image-post-publishing/img02.3_09-01.png)
   
    ![dessin](media/marketplace-publishing-vm-image-post-publishing/img02.3-2.png)

### <a name="24-change-an-existing-sample-image-or-add-a-new-sample-image"></a>2.4 Modifier une image d’exemple existante ou ajouter une nouvelle image d’exemple
Vous pouvez modifier les images d’exemple existantes ou en ajouter de nouvelles, puis republier votre offre en suivant les étapes ci-dessous :

> [!NOTE]
> Une seule image d’exemple est affichée sur la page [https://portal.azure.com](https://portal.azure.com).
> 
> 

1. Connectez-vous au [portail de publication](https://publish.windowsazure.com)
2. Accédez à l’onglet **MACHINES VIRTUELLES** et sélectionnez votre offre.
3. Dans le menu de gauche, cliquez sur l’onglet **MARKETING** .
4. Cliquez sur le bouton **FRANÇAIS (FRANCE)** .
5. Dans le menu de gauche, cliquez sur l’onglet **IMAGES D’EXEMPLE** .
6. Si vous souhaitez ajouter une nouvelle image d’exemple, sous la section *Images d’exemple* , cliquez sur le bouton **TÉLÉCHARGER UNE NOUVELLE IMAGE** , puis enregistrez les modifications.
   
   > [!NOTE]
   > L’ajout d’une image d’exemple est une étape facultative.
   > 
   > 
7. Si vous souhaitez mettre à jour ou supprimer une image d’exemple existante, recherchez l’image appropriée, puis cliquez sur le bouton **REMPLACER L’IMAGE** ou sur le bouton Supprimer.
8. Après avoir effectué les modifications, accédez à l’onglet **PUBLIER** et cliquez sur le bouton **DÉPLOYER DANS UN ENVIRONNEMENT INTERMÉDIAIRE**. Pour des instructions détaillées sur le test de votre offre dans l’environnement intermédiaire, reportez-vous à ce [lien](marketplace-publishing-vm-image-test-in-staging.md).
9. Une fois que vous avez testé votre offre dans l’environnement de préproduction, accédez à l’onglet **PUBLIER** dans le portail de publication et cliquez sur le bouton **DEMANDER UNE APPROBATION POUR LANCER LA PRODUCTION** pour republier votre offre dans Azure Marketplace.
   
    ![dessin](media/marketplace-publishing-vm-image-post-publishing/img02.4_09.png)

### <a name="25-update-the-legal-content"></a>2.5 Mettre à jour le contenu juridique
Vous pouvez mettre à jour le contenu juridique et republier votre offre en suivant les étapes ci-dessous :

1. Connectez-vous au [portail de publication](https://publish.windowsazure.com)
2. Accédez à l’onglet **MACHINES VIRTUELLES** et sélectionnez votre offre.
3. Dans le menu de gauche, cliquez sur l’onglet **MARKETING** .
4. Cliquez sur le bouton **FRANÇAIS (FRANCE)** .
5. Dans le menu de gauche, cliquez sur l’onglet **CONTENU JURIDIQUE** . Sous la section *Contenu juridique* , vous pouvez mettre à jour vos stratégies/conditions d’utilisation. Entrez ou collez les stratégies/conditions dans la zone de texte *Conditions d’utilisation* , puis enregistrez les modifications.
6. La limite de caractères pour les conditions d'utilisation est de 1 000 000 caractères.
7. Après avoir effectué les modifications, accédez à l’onglet **PUBLIER** et cliquez sur le bouton **DÉPLOYER DANS UN ENVIRONNEMENT INTERMÉDIAIRE**. Pour des instructions détaillées sur le test de votre offre dans l’environnement intermédiaire, reportez-vous à ce [lien](marketplace-publishing-vm-image-test-in-staging.md)
8. Une fois que vous avez testé votre offre dans l’environnement de préproduction, accédez à l’onglet **PUBLIER** dans le portail de publication et cliquez sur le bouton **DEMANDER UNE APPROBATION POUR LANCER LA PRODUCTION** pour republier votre offre dans Azure Marketplace.
   
    ![dessin](media/marketplace-publishing-vm-image-post-publishing/img02.5_08.png)

### <a name="26-update-the-support-information"></a>2.6 Mettre à jour les informations de support
Vous pouvez mettre à jour les informations de support et republier votre offre en suivant les étapes ci-dessous :

1. Connectez-vous au [portail de publication](https://publish.windowsazure.com)
2. Accédez à l’onglet **MACHINES VIRTUELLES** et sélectionnez votre offre.
3. Dans le menu de gauche, cliquez sur l’onglet **SUPPORT** .
4. Sous la section *Contact Ingénierie* de l’onglet **SUPPORT** , vous pouvez mettre à jour les coordonnées du contact. Ces coordonnées sont uniquement utilisées pour les communications internes entre le partenaire et Microsoft.
5. Dans la section *Service clientèle* de l’onglet **SUPPORT**, vous pouvez mettre à jour les coordonnées, comme **le nom, l’adresse e-mail, le numéro de téléphone** et **l’URL du support technique**. Ces coordonnées sont uniquement utilisées pour les communications internes entre le partenaire et Microsoft.
   
   > [!NOTE]
   > Si vous souhaitez assurer un support par courrier électronique, indiquez un numéro de téléphone factice sous la section **Service clientèle** . Dans ce cas, l’adresse e-mail que vous avez fournie est utilisée.
   > 
   > 
6. Après avoir effectué les modifications, accédez à l’onglet **PUBLIER** et cliquez sur le bouton **DÉPLOYER DANS UN ENVIRONNEMENT INTERMÉDIAIRE**. Pour des instructions détaillées sur le test de votre offre dans l’environnement intermédiaire, reportez-vous à ce [lien](marketplace-publishing-vm-image-test-in-staging.md)
7. Une fois que vous avez testé votre offre dans l’environnement de préproduction, accédez à l’onglet **PUBLIER** dans le portail de publication et cliquez sur le bouton **DEMANDER UNE APPROBATION POUR LANCER LA PRODUCTION** pour republier votre offre dans Azure Marketplace.
   
    ![dessin](media/marketplace-publishing-vm-image-post-publishing/img02.6_07.png)

### <a name="27-update-the-categories"></a>2.7 Mettre à jour les catégories
Vous pouvez mettre à jour les catégories de votre offre et la republier en suivant les étapes ci-dessous :

1. Connectez-vous au [portail de publication](https://publish.windowsazure.com)
2. Accédez à l’onglet **MACHINES VIRTUELLES** et sélectionnez votre offre.
3. Dans le menu de gauche, cliquez sur l’onglet **CATÉGORIES** .
4. Sous la section *Catégories* , vous pouvez mettre à jour les catégories de votre offre, puis enregistrer les modifications. Vous pouvez sélectionner jusqu’à cinq catégories de la galerie Azure Marketplace.
5. Après avoir effectué les modifications, accédez à l’onglet **PUBLIER** et cliquez sur le bouton **DÉPLOYER DANS UN ENVIRONNEMENT INTERMÉDIAIRE**. Pour des instructions détaillées sur le test de votre offre dans l’environnement intermédiaire, reportez-vous à ce [lien](marketplace-publishing-vm-image-test-in-staging.md)
6. Une fois que vous avez testé votre offre dans l’environnement de préproduction, accédez à l’onglet **PUBLIER** dans le portail de publication et cliquez sur le bouton **DEMANDER UNE APPROBATION POUR LANCER LA PRODUCTION** pour republier votre offre dans Azure Marketplace.
   
    ![dessin](media/marketplace-publishing-vm-image-post-publishing/img02.7_06.png)

## <a name="3-how-to-add-a-new-sku-under-a-listed-offer"></a>3. Comment ajouter une nouvelle référence (SKU) à une offre répertoriée
Vous pouvez ajouter une nouvelle référence (SKU) à votre offre en direct en suivant les étapes ci-dessous :

1. Connectez-vous au [portail de publication](https://publish.windowsazure.com).
2. Accédez à l’onglet **MACHINES VIRTUELLES** et sélectionnez votre offre.
3. Dans le menu de gauche, cliquez sur l’onglet **RÉFÉRENCES (SKU)** . Cliquez ensuite sur le bouton **AJOUTER UNE RÉFÉRENCE (SKU)**.  Une nouvelle boîte de dialogue s’affiche. Entrez un identificateur de référence (SKU) en minuscules. Cochez la case Choisir votre propre modèle de facturation (BYOL) si vous souhaitez publier de nouvelles références (SKU) avec un modèle de facturation BYOL. Sinon, décochez-la. Ensuite, cliquez sur la coche de la boîte de dialogue pour créer une référence (SKU). Si vous n’avez pas opté pour le modèle de facturation BYOL pour la nouvelle référence (SKU), le modèle de facturation sera automatiquement défini sur Toutes les heures pour la nouvelle référence (SKU). Si vous souhaitez activer la version d’évaluation gratuite de 30 jours pour le modèle de facturation horaire, cliquez sur « Un mois » pour l’option « Une version d’évaluation gratuite est-elle disponible ? ». Sinon, sélectionnez « AUCUNE VERSION D’ÉVALUATION ». [Remarque : L’option « Une version d’évaluation gratuite est-elle disponible ? » n’apparaît que si vous n’avez PAS sélectionné BYOL dans la boîte de dialogue au moment de la création de la référence (SKU).]
   
   > [!IMPORTANT]
   > L’option « Masquer cette référence (SKU) à partir du Marketplace, car elle doit toujours être achetée via un modèle de solution » doit être marquée comme « OUI » UNIQUEMENT si vous êtes autorisé à publier une offre de modèle de solution dans Azure Marketplace. Dans le cas contraire, cette option doit toujours être marquée comme « NON ».
   > 
   > 
4. Maintenant, dans le menu de gauche, cliquez sur l’onglet **IMAGES DE MACHINES VIRTUELLES** et retrouvez la référence (SKU) que vous avez créée.
5. Pour configurer la nouvelle référence (SKU), reportez-vous à l’ÉTAPE 5 de ce [lien](marketplace-publishing-vm-image-creation.md#5-obtain-certification-for-your-vm-image) pour obtenir des instructions.
6. Pour ajouter la documentation marketing d’une nouvelle référence (SKU), reportez-vous à la section Étape 1 : Fournir du contenu marketing à Marketplace -> Détails -> Points 2 à 5 de ce [lien](marketplace-publishing-push-to-staging.md#step-1-provide-marketplace-marketing-content).
7. Pour ajouter les informations de tarification de la nouvelle référence (SKU), reportez-vous à la section 2.1. Définissez vos prix de machines virtuelles de ce [lien](marketplace-publishing-push-to-staging.md#step-2-set-your-prices)
8. Après avoir effectué les modifications, accédez à l’onglet **PUBLIER** et cliquez sur le bouton **DÉPLOYER DANS UN ENVIRONNEMENT INTERMÉDIAIRE**. Pour des instructions détaillées sur le test de votre offre dans l’environnement intermédiaire, reportez-vous à ce [lien](marketplace-publishing-vm-image-test-in-staging.md)
9. Une fois que vous avez testé votre offre dans l’environnement de préproduction, accédez à l’onglet **PUBLIER** dans le portail de publication et cliquez sur le bouton **DEMANDER UNE APPROBATION POUR LANCER LA PRODUCTION** pour republier votre offre dans Azure Marketplace.
   
    ![dessin](media/marketplace-publishing-vm-image-post-publishing/img03_09-01.png)
   
    ![dessin](media/marketplace-publishing-vm-image-post-publishing/img03_09-02.png)

## <a name="4-how-to-change-the-data-disk-count-for-a-listed-sku"></a>4. Comment modifier le nombre de disques de données pour une référence (SKU) répertoriée
Vous ne pouvez pas incrémenter/décrémenter le nombre de disques de données d’une référence répertoriée. Vous devez dans ce cas créer une nouvelle référence. Reportez-vous à la section [3. Comment ajouter une nouvelle référence (SKU) sous une offre en direct](#3-how-to-add-a-new-sku-under-a-live-offer) pour obtenir une aide précise.

## <a name="5----how-to-delete-a-listed-offer-from-the-azure-marketplace"></a>5.    Comment supprimer une offre répertoriée d’Azure Marketplace
Différents aspects doivent être pris en compte en cas de demande de suppression d’une offre en direct. Suivez les étapes ci-dessous pour obtenir des instructions de l’équipe de support afin de supprimer une offre répertoriée d’Azure Marketplace :

1. Ouvrez un ticket de support en utilisant ce [lien](https://support.microsoft.com/en-us/getsupport?wf=0&tenant=ClassicCommercial&oaspworkflow=start_1.0.0.0&locale=en-us&supportregion=en-us&pesid=15635&ccsid=635993707583706681).
2. Pour le type de problème, sélectionnez **« Gestion des offres »** puis, pour la catégorie, sélectionnez **« Modification d’une offre et/ou d’une référence (SKU) déjà en production »**.
3. Envoyez la demande.

L’équipe de support vous guidera tout au long du processus de suppression de l’offre/de la référence (SKU).

> [!NOTE]
> Vous pouvez toujours supprimer l’offre quand elle est à l’état de brouillon (c’est-à-dire, ni à l’état INTERMÉDIAIRE ni EN PRODUCTION) en cliquant sur le bouton **SUPPRIMER LE BROUILLON** sous l’onglet **HISTORIQUE**.
> 
> 

## <a name="6-how-to-delete-a-listed-sku-from-the-azure-marketplace"></a>6. Comment supprimer une référence (SKU) répertoriée d’Azure Marketplace
Vous pouvez supprimer une référence (SKU) répertoriée dans Azure Marketplace en suivant les étapes ci-dessous :

1. Connectez-vous au [portail de publication](https://publish.windowsazure.com).
2. Accédez à l’onglet **MACHINES VIRTUELLES** et sélectionnez votre offre.
3. Dans le volet de gauche, cliquez sur l’onglet **RÉFÉRENCES (SKU)** .
4. Sélectionnez la référence que vous souhaitez supprimer et cliquez sur le bouton Supprimer à côté.
5. Une fois cette opération effectuée, accédez à l’onglet PUBLIER dans le portail de publication et cliquez sur le bouton **DEMANDER UNE APPROBATION POUR LANCER LA PRODUCTION** pour republier l’offre dans Azure Marketplace.
6. Une fois l’offre republiée dans Azure Marketplace, la référence sera supprimée d’Azure Marketplace et du portail Azure.

## <a name="7-how-to-delete-the-current-version-of-a-listed-sku-from-the-azure-marketplace"></a>7. Comment supprimer la version actuelle d’une référence (SKU) répertoriée d’Azure Marketplace
Vous pouvez supprimer la version actuelle d’une référence (SKU) répertoriée dans Azure Marketplace en suivant les étapes ci-dessous. Une fois le processus terminé, la référence sera restaurée à sa version précédente.

1. Connectez-vous au [portail de publication](https://publish.windowsazure.com).
2. Accédez à l’onglet **MACHINES VIRTUELLES** et sélectionnez votre offre.
3. Dans le volet de gauche, cliquez sur l’onglet **IMAGES DE MACHINES VIRTUELLES** .
4. Sélectionnez la référence dont vous souhaitez supprimer la version actuelle et cliquez sur le bouton Supprimer à côté.
5. Une fois cette opération effectuée, accédez à l’onglet **PUBLIER** dans le portail de publication et cliquez sur le bouton **DEMANDER UNE APPROBATION POUR LANCER LA PRODUCTION** pour republier l’offre dans Azure Marketplace.
6. Une fois l’offre republiée dans Azure Marketplace, la version actuelle de la référence répertoriée sera supprimée d’Azure Marketplace et du portail Azure. La référence sera restaurée à sa version précédente.

## <a name="8-how-to-revert-listing-price-to-production-values"></a>8. Comment restaurer des prix de vente aux valeurs de production
J’ai changé la tarification d’une référence répertoriée (ou j’ai supprimé les régions de facturation d’une référence répertoriée). Dans la mesure où cela n’est pas pris en charge dans Azure Marketplace, je souhaite annuler mes modifications pour revenir aux valeurs de production. Comment faire ?

Veuillez suivre les étapes indiquées ci-dessous :

1. Connectez-vous au [portail de publication](https://publish.windowsazure.com).
2. Accédez à l’onglet **MACHINES VIRTUELLES** et sélectionnez votre offre.
3. Dans le menu de gauche, cliquez sur l’onglet **TARIFICATION** .
4. Sous l’onglet Tarification, sélectionnez la région dont vous souhaitez réinitialiser la tarification.
   
    ![dessin](media/marketplace-publishing-vm-image-post-publishing/img08-04.png)
5. En cas de références avec modèle de facturation à l’heure, réinitialisez le prix de tous les cœurs tels qu’ils sont dans la production de la région sélectionnée. Pour les références avec modèle de facturation BYOL, rendez la référence disponible dans la région en cochant la case à côté de la référence sous la section DISPONIBILITÉ DE LA RÉFÉRENCE AVEC LICENCE EXTERNE (BYOL) (voir la capture d’écran ci-dessous).
   
    ![dessin](media/marketplace-publishing-vm-image-post-publishing/img08-05.png)
6. Cliquez maintenant sur le bouton **TARIFICATION AUTOMATIQUE POUR LES AUTRES MARCHÉS EN FONCTION DES PRIX POUR LES ÉTATS-UNIS**.
   
   > [!NOTE]
   > Le libellé du bouton peut être différent selon la région que vous avez sélectionnée. Comme nous avons sélectionné les États-Unis lors de la création de ce document, le bouton est intitulé « Tarification automatique pour les autres marchés en fonction des prix pour les États-Unis » dans la capture d’écran ci-dessous.
   > 
   > 
   
    ![dessin](media/marketplace-publishing-vm-image-post-publishing/img08-06.png)
7. L’Assistant de tarification automatique s’ouvre. La première page affiche la sélection pour le marché de base. Effectuez votre sélection et passez à la page suivante en cliquant sur le bouton **« -> »**.
   
    ![dessin](media/marketplace-publishing-vm-image-post-publishing/img08-07.png)
8. L’option permettant de sélectionner les cœurs et les plans s’affichera sur la page 2. Sélectionnez les cœurs et les plans voulus, puis cliquez sur le bouton « -> ».
   
    ![dessin](media/marketplace-publishing-vm-image-post-publishing/img08-08.png)
9. La page 3 affiche les marchés et régions. Cliquez sur le bouton Activer/désactiver tout pour sélectionner toutes les régions, ou cochez-les manuellement. Cliquez sur le bouton « -> » pour passer à la page suivante.
   
    ![dessin](media/marketplace-publishing-vm-image-post-publishing/img08-09.png)
10. La page 4 affiche le taux de change. Cliquez sur le bouton Terminer pour terminer la procédure. L’Assistant réinitialisera la tarification en fonction de votre sélection.
11. Maintenant, accédez à l’onglet Tarification et cliquez sur le bouton « AFFICHER LE RÉSUMÉ ET LES MODIFICATIONS ».
    Sélectionnez « Brouillon » dans la section « Afficher la version » et « Production » dans la section « Comparer à » (voir la capture d’écran ci-dessous). Si vous ne constatez aucune différence de prix, cela signifie que la tarification a été rétablie avec succès aux valeurs de production.
    
    ![dessin](media/marketplace-publishing-vm-image-post-publishing/img08-11.png)
12. Après avoir effectué les modifications, accédez à l’onglet PUBLIER et cliquez sur le bouton **DÉPLOYER DANS UN ENVIRONNEMENT INTERMÉDIAIRE**. Pour des instructions détaillées sur le test de votre offre dans l’environnement intermédiaire, reportez-vous à ce [lien](marketplace-publishing-vm-image-test-in-staging.md)
13. Une fois que vous avez testé votre offre dans l’environnement intermédiaire, accédez à l’onglet PUBLIER dans le portail de publication et cliquez sur le bouton **DEMANDER UNE APPROBATION POUR LANCER LA PRODUCTION** pour republier votre offre dans Azure Marketplace.

## <a name="9-how-to-revert-billing-model-to-production-values"></a>9. Comment restaurer un modèle de facturation aux valeurs de production
J’ai modifié le modèle de facturation d’une référence répertoriée. Dans la mesure où cela n’est pas pris en charge dans Azure Marketplace, je souhaite annuler mes modifications pour revenir aux valeurs de production. Comment faire ?

Procédez comme suit :

1. Connectez-vous au [portail de publication](https://publish.windowsazure.com).
2. Accédez à l’onglet **MACHINES VIRTUELLES** et sélectionnez votre offre.
3. Dans le menu de gauche, cliquez sur l’onglet **RÉFÉRENCES (SKU)** .
4. Cliquez sur le bouton MODIFIER pour rétablir le modèle de facturation. Une fenêtre s’affiche. Cochez ou décochez la case **« La facturation et la gestion des licences sont traitées à l’extérieur d’Azure (apportez votre propre licence) »** selon le cas.
   
    ![dessin](media/marketplace-publishing-vm-image-post-publishing/img09-04.png)
5. Une fois cette opération effectuée, consultez la réponse à la question 8 dans ce document pour rétablir la tarification.
6. Ensuite, accédez à l’onglet **PUBLIER** dans le portail de publication et déployez l’offre dans un environnement intermédiaire pour la tester. Une fois que vous avez fini de tester l’offre, cliquez sur le bouton **DEMANDER UNE APPROBATION POUR LANCER LA PRODUCTION** pour republier votre offre dans Azure Marketplace.

## <a name="10-how-to-revert-visibility-setting-of-a-listed-sku-to-the-production-value"></a>10. Comment rétablir le paramètre de visibilité d’une référence (SKU) répertoriée à la valeur de la production
Procédez comme suit :

1. Connectez-vous au [portail de publication](https://publish.windowsazure.com).
2. Accédez à l’onglet **MACHINES VIRTUELLES** et sélectionnez votre offre.
3. Dans le menu de gauche, cliquez sur l’onglet **RÉFÉRENCES (SKU)** .
4. Sélectionnez votre référence et rétablissez le paramètre de visibilité de la référence à la valeur de la production.
   
    ![dessin](media/marketplace-publishing-vm-image-post-publishing/img10-04.png)
5. Une fois que vous avez terminé les modifications, cliquez sur le bouton **DEMANDER UNE APPROBATION POUR LANCER LA PRODUCTION** pour republier votre offre dans Azure Marketplace.

## <a name="see-also"></a>Voir aussi
* [Mise en route : publication d’une offre dans Azure Marketplace](marketplace-publishing-getting-started.md)
* [Présentation du rapport Informations du vendeur](marketplace-publishing-report-seller-insights.md)
* [Présentation du rapport Revenus](marketplace-publishing-report-payout.md)
* [Modification de « l'offre incitative revendeurs » des fournisseurs de solutions Cloud](marketplace-publishing-csp-incentive.md)
* [Résolution des problèmes de publication courants dans le Marketplace](marketplace-publishing-support-common-issues.md)
* [Obtenir de l’aide en tant qu’éditeur](marketplace-publishing-get-publisher-support.md)
* [Création d’une image de machine virtuelle locale](marketplace-publishing-vm-image-creation-on-premise.md)
* [Création d’une machine virtuelle exécutant Windows dans le portail Azure en version préliminaire](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json)




<!--HONumber=Nov16_HO3-->


