<properties
   pageTitle="Gestion de l’image de machine virtuelle sur Azure Marketplace | Microsoft Azure"
   description="Guide détaillé sur la gestion de votre image de machine virtuelle sur Azure Marketplace après la publication initiale."
   services="Azure Marketplace"
   documentationCenter=""
   authors="HannibalSII"
   manager=""
   editor=""/>

<tags
   ms.service="marketplace"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="Azure"
   ms.workload="na"
   ms.date="06/01/2016"
   ms.author="hascipio;"/>

# Guide de post-production pour les offres de machine virtuelle dans Azure Marketplace

Cet article explique comment vous pouvez mettre à jour une offre de machine virtuelle en direct dans Azure Marketplace. Il vous guide également dans le processus d’ajout d’une ou de plusieurs nouvelles références (SKU) à une offre existante, et dans le processus de suppression d’une offre de machine virtuelle en direct ou d’une référence (SKU) dans Azure Marketplace.

**Une fois que la référence (SKU) est en direct dans Azure Marketplace, vous ne pouvez pas mettre à jour les détails ci-dessous :**

- **Identificateur de la référence (SKU)**
- **Identificateur de l’éditeur**
- **Identificateur de l’offre**
- **Modification de la tarification**
- **Modification du modèle de facturation**
- **Suppression des régions de facturation**


## 1\. Comment mettre à jour les détails techniques d’une référence (SKU)

Vous pouvez mettre à jour une image de machine virtuelle et republier votre offre en suivant les étapes ci-dessous :

1. Connectez-vous au [portail de publication](https://publish.windowsazure.com).
2. Accédez à l’onglet **MACHINES VIRTUELLES** et sélectionnez votre offre.
3. Dans le menu de gauche, cliquez sur l’onglet **IMAGES DE MACHINES VIRTUELLES**.
4. À partir de la section **RÉFÉRENCES (SKU)** de l’onglet IMAGES DE MACHINES VIRTUELLES, recherchez la référence (SKU) que vous souhaitez mettre à jour. Ensuite, mettez à jour le numéro de version de la référence (SKU). La nouvelle version doit être au format X.Y.Z, où X, Y et Z sont des entiers. Les modifications de la version doivent uniquement être incrémentielles.
5. Dans la zone **URL de disque dur virtuel de système d’exploitation**, mettez à jour la signature d’accès partagé créée pour le disque dur virtuel du système d’exploitation, puis enregistrez les modifications.
6. Après avoir effectué les modifications, accédez à l’onglet **PUBLIER** et cliquez sur le bouton **DÉPLOYER DANS UN ENVIRONNEMENT INTERMÉDIAIRE**. Pour des instructions détaillées sur le test de votre offre dans l’environnement intermédiaire, reportez-vous à ce [lien](marketplace-publishing-vm-image-test-in-staging.md)
7. Une fois que vous avez testé votre offre dans l’environnement intermédiaire, accédez à l’onglet **PUBLIER** dans le portail de publication et cliquez sur le bouton **DEMANDER UNE APPROBATION POUR LANCER LA PRODUCTION** pour republier votre offre dans Azure Marketplace.

    ![dessin](media/marketplace-publishing-vm-image-post-publishing/img01.png)

## 2\. Comment mettre à jour les détails non techniques d’une offre ou d’une référence (SKU)

Vous pouvez mettre à jour les détails non techniques (marketing, juridique, support, catégories) de votre offre en direct ou de votre référence (SKU) dans Azure Marketplace.

### 2\.1 Mettre à jour la description et les logos de l’offre

Vous pouvez mettre à jour les détails de l’offre et la republier en suivant les étapes ci-dessous :

1. Connectez-vous au [portail de publication](https://publish.windowsazure.com).
2. Accédez à l’onglet **MACHINES VIRTUELLES** et sélectionnez votre offre.
3. Dans le menu de gauche, cliquez sur l’onglet **MARKETING**.
4. Cliquez sur le bouton **FRANÇAIS (FRANCE)**.
5. Dans le menu de gauche, cliquez sur l’onglet **DÉTAILS**. Sous la section *DESCRIPTION* de l’onglet **DÉTAILS**, vous pouvez mettre à jour le titre, le résumé bref et le résumé long de l’offre, puis enregistrer les modifications.

    >[AZURE.NOTE] Tenez compte des éléments suivants lorsque vous mettez à jour les détails de la référence (SKU). **N’entrez pas deux fois le même texte dans la description de l’offre et de la référence (SKU). N’entrez pas deux fois le même texte dans le titre de la référence (SKU) et dans le résumé long de l’offre. N’entrez pas deux fois le même texte dans le titre de la référence (SKU) et dans le résumé de l’offre.**

    ![dessin](media/marketplace-publishing-vm-image-post-publishing/img02.png)

6. Sous la section *LOGOS* de l’onglet **DÉTAILS**, vous pouvez mettre à jour les logos. Toutefois, assurez-vous que les logos respectent les [instructions Azure Marketplace](marketplace-publishing-push-to-staging.md#step-1-provide-marketplace-marketing-content) (reportez-vous à la section Étape 1 : Fournir du contenu marketing à Marketplace -> Détails -> Instructions relatives aux logos Azure Marketplace).

    >[AZURE.NOTE] La bannière est facultative. Vous pouvez choisir de ne pas télécharger de bannière. Cependant, une fois la bannière téléchargée, aucune disposition ne permet de la supprimer du portail de publication. Dans ce cas, vous devez suivre les [instructions relatives à la bannière](marketplace-publishing-push-to-staging.md#step-1-provide-marketplace-marketing-content) (reportez-vous à la section Étape 1 : Fournir du contenu marketing à Marketplace -> Détails -> Instructions supplémentaires relatives aux bannières).

7. Après avoir effectué les modifications, accédez à l’onglet **PUBLIER** et cliquez sur le bouton **DÉPLOYER DANS UN ENVIRONNEMENT INTERMÉDIAIRE**. Pour des instructions détaillées sur le test de votre offre dans l’environnement intermédiaire, reportez-vous à ce [lien](marketplace-publishing-vm-image-test-in-staging.md).
8. Une fois que vous avez testé votre offre dans l’environnement intermédiaire, accédez à l’onglet **PUBLIER** dans le portail de publication et cliquez sur le bouton **DEMANDER UNE APPROBATION POUR LANCER LA PRODUCTION** pour republier votre offre dans Azure Marketplace.

    ![dessin](media/marketplace-publishing-vm-image-post-publishing/img02.1.png)

### 2\.2. Mettre à jour la description de la référence (SKU)

Vous pouvez mettre à jour les détails de la référence (SKU) et republier votre offre en suivant les étapes ci-dessous :

1. Connectez-vous au [portail de publication](https://publish.windowsazure.com).
2. Accédez à l’onglet **MACHINES VIRTUELLES** et sélectionnez votre offre.
3. Dans le menu de gauche, cliquez sur l’onglet **MARKETING**.
4. Cliquez sur le bouton **FRANÇAIS (FRANCE)**.
5. Dans le menu de gauche, cliquez sur l’onglet **PLANS**. Sous la section *RÉFÉRENCES (SKU)* de l’onglet **PLANS**, vous pouvez mettre à jour le titre, le résumé et la description de la référence (SKU), puis enregistrer les modifications.

    >[AZURE.NOTE] Tenez compte des éléments suivants lorsque vous mettez à jour les détails de la référence (SKU). **N’entrez pas deux fois le même texte dans la description de l’offre et de la référence (SKU). N’entrez pas deux fois le même texte dans le titre de la référence (SKU) et dans le résumé long de l’offre. N’entrez pas deux fois le même texte dans le titre de la référence (SKU) et dans le résumé de l’offre.**

6. Après avoir effectué les modifications, accédez à l’onglet **PUBLIER** et cliquez sur le bouton **DÉPLOYER DANS UN ENVIRONNEMENT INTERMÉDIAIRE**. Pour des instructions détaillées sur le test de votre offre dans l’environnement intermédiaire, reportez-vous à ce lien
7. Une fois que vous avez testé votre offre dans l’environnement intermédiaire, accédez à l’onglet **PUBLIER** dans le portail de publication et cliquez sur le bouton **DEMANDER UNE APPROBATION POUR LANCER LA PRODUCTION** pour republier votre offre dans Azure Marketplace.

    ![dessin](media/marketplace-publishing-vm-image-post-publishing/img02.2.png)

### 2\.3 Modifier les liens existants ou ajouter de nouveaux liens

Vous pouvez modifier les liens existants ou ajouter de nouveaux liens, puis republier votre offre en suivant les étapes ci-dessous :

1. Connectez-vous au [portail de publication](https://publish.windowsazure.com).
2. Accédez à l’onglet **MACHINES VIRTUELLES** et sélectionnez votre offre.
3. Dans le menu de gauche, cliquez sur l’onglet **MARKETING**.
4. Cliquez sur le bouton **FRANÇAIS (FRANCE)**.
5. Dans le menu de gauche, cliquez sur l’onglet **LIENS**.
6. Si vous souhaitez ajouter un nouveau lien, sous la section *LIENS*, cliquez sur le bouton **AJOUTER UN LIEN**. La boîte de dialogue *Ajouter un lien* s’ouvre. Dans cette boîte de dialogue, vous pouvez ajouter le titre et les champs URL du lien, puis enregistrer les modifications. Vous pouvez entrer n’importe quel lien contenant des informations utiles aux clients.
7. Si vous souhaitez mettre à jour ou supprimer un lien existant, sélectionnez le lien approprié, puis cliquez sur le bouton Modifier ou sur le bouton Supprimer.

    >[AZURE.NOTE] Assurez-vous que les liens que vous avez saisis dans cette section fonctionnent correctement, car ceux-ci sont validés lors du processus de demande de production.

8. Après avoir effectué les modifications, accédez à l’onglet **PUBLIER** et cliquez sur le bouton **DÉPLOYER DANS UN ENVIRONNEMENT INTERMÉDIAIRE**. Pour des instructions détaillées sur le test de votre offre dans l’environnement intermédiaire, reportez-vous à ce [lien](marketplace-publishing-vm-image-test-in-staging.md).
9. Une fois que vous avez testé votre offre dans l’environnement intermédiaire, accédez à l’onglet **PUBLIER** dans le portail de publication et cliquez sur le bouton **DEMANDER UNE APPROBATION POUR LANCER LA PRODUCTION** pour republier votre offre dans Azure Marketplace.

    ![dessin](media/marketplace-publishing-vm-image-post-publishing/img02.3.png)

    ![dessin](media/marketplace-publishing-vm-image-post-publishing/img02.3-2.png)

### 2\.4 Modifier une image d’exemple existante ou ajouter une nouvelle image d’exemple

Vous pouvez modifier les images d’exemple existantes ou en ajouter de nouvelles, puis republier votre offre en suivant les étapes ci-dessous :

>[AZURE.NOTE] Une seule image d’exemple est affichée sur la page [https://portal.azure.com](https://portal.azure.com).

1. Connectez-vous au [portail de publication](https://publish.windowsazure.com).
2. Accédez à l’onglet **MACHINES VIRTUELLES** et sélectionnez votre offre.
3. Dans le menu de gauche, cliquez sur l’onglet **MARKETING**.
4. Cliquez sur le bouton **FRANÇAIS (FRANCE)**.
5. Dans le menu de gauche, cliquez sur l’onglet **IMAGES D’EXEMPLE**.
6. Si vous souhaitez ajouter une nouvelle image d’exemple, sous la section *Images d’exemple*, cliquez sur le bouton **TÉLÉCHARGER UNE NOUVELLE IMAGE**, puis enregistrez les modifications.

    >[AZURE.NOTE] L’ajout d’une image d’exemple est une étape facultative.

7. Si vous souhaitez mettre à jour ou supprimer une image d’exemple existante, recherchez l’image appropriée, puis cliquez sur le bouton **REMPLACER L’IMAGE** ou sur le Bouton Supprimer.

8. Après avoir effectué les modifications, accédez à l’onglet **PUBLIER** et cliquez sur le bouton **DÉPLOYER DANS UN ENVIRONNEMENT INTERMÉDIAIRE**. Pour des instructions détaillées sur le test de votre offre dans l’environnement intermédiaire, reportez-vous à ce [lien](marketplace-publishing-vm-image-test-in-staging.md).
9. Une fois que vous avez testé votre offre dans l’environnement intermédiaire, accédez à l’onglet **PUBLIER** dans le portail de publication et cliquez sur le bouton **DEMANDER UNE APPROBATION POUR LANCER LA PRODUCTION** pour republier votre offre dans Azure Marketplace.

    ![dessin](media/marketplace-publishing-vm-image-post-publishing/img02.4.png)

### 2\.5 Mettre à jour le contenu juridique

Vous pouvez mettre à jour le contenu juridique et republier votre offre en suivant les étapes ci-dessous :

1. Connectez-vous au [portail de publication](https://publish.windowsazure.com).
2. Accédez à l’onglet **MACHINES VIRTUELLES** et sélectionnez votre offre.
3. Dans le menu de gauche, cliquez sur l’onglet **MARKETING**.
4. Cliquez sur le bouton **FRANÇAIS (FRANCE)**.
5. Dans le menu de gauche, cliquez sur l’onglet **CONTENU JURIDIQUE**. Sous la section *Contenu juridique*, vous pouvez mettre à jour vos stratégies/conditions d’utilisation. Entrez ou collez les stratégies/conditions dans la zone de texte *Conditions d’utilisation*, puis enregistrez les modifications.
6. La limite de caractères pour les conditions d'utilisation est de 1 000 000 caractères.
7. Après avoir effectué les modifications, accédez à l’onglet **PUBLIER** et cliquez sur le bouton **DÉPLOYER DANS UN ENVIRONNEMENT INTERMÉDIAIRE**. Pour des instructions détaillées sur le test de votre offre dans l’environnement intermédiaire, reportez-vous à ce [lien](marketplace-publishing-vm-image-test-in-staging.md)
8. Une fois que vous avez testé votre offre dans l’environnement intermédiaire, accédez à l’onglet **PUBLIER** dans le portail de publication et cliquez sur le bouton **DEMANDER UNE APPROBATION POUR LANCER LA PRODUCTION** pour republier votre offre dans Azure Marketplace.

    ![dessin](media/marketplace-publishing-vm-image-post-publishing/img02.5.png)

### 2\.6 Mettre à jour les informations de support

Vous pouvez mettre à jour les informations de support et republier votre offre en suivant les étapes ci-dessous :

1. Connectez-vous au [portail de publication](https://publish.windowsazure.com).
2. Accédez à l’onglet **MACHINES VIRTUELLES** et sélectionnez votre offre.
3. Dans le menu de gauche, cliquez sur l’onglet **SUPPORT**.
4. Sous la section *Contact Ingénierie* de l’onglet **SUPPORT**, vous pouvez mettre à jour les coordonnées du contact. Ces coordonnées sont uniquement utilisées pour les communications internes entre le partenaire et Microsoft.
5. Sous la section *Service clientèle* de l’onglet **SUPPORT**, vous pouvez mettre à jour les coordonnées, comme **le nom, l’adresse e-mail, le numéro de téléphone** et **l’URL du support technique**. Ces coordonnées sont uniquement utilisées pour les communications internes entre le partenaire et Microsoft.

    >[AZURE.NOTE] Si vous souhaitez assurer un support par courrier électronique, indiquez un numéro de téléphone factice sous la section **Service clientèle**. Dans ce cas, l’adresse e-mail que vous avez fournie est utilisée.

6. Après avoir effectué les modifications, accédez à l’onglet **PUBLIER** et cliquez sur le bouton **DÉPLOYER DANS UN ENVIRONNEMENT INTERMÉDIAIRE**. Pour des instructions détaillées sur le test de votre offre dans l’environnement intermédiaire, reportez-vous à ce [lien](marketplace-publishing-vm-image-test-in-staging.md)
7. Une fois que vous avez testé votre offre dans l’environnement intermédiaire, accédez à l’onglet **PUBLIER** dans le portail de publication et cliquez sur le bouton **DEMANDER UNE APPROBATION POUR LANCER LA PRODUCTION** pour republier votre offre dans Azure Marketplace.

    ![dessin](media/marketplace-publishing-vm-image-post-publishing/img02.7.png)

### 2\.7 Mettre à jour les catégories

Vous pouvez mettre à jour les catégories de votre offre et la republier en suivant les étapes ci-dessous :

1. Connectez-vous au [portail de publication](https://publish.windowsazure.com).
2. Accédez à l’onglet **MACHINES VIRTUELLES** et sélectionnez votre offre.
3. Dans le menu de gauche, cliquez sur l’onglet **CATÉGORIES**.
4. Sous la section *Catégories*, vous pouvez mettre à jour les catégories de votre offre, puis enregistrer les modifications. Vous pouvez sélectionner jusqu’à cinq catégories de la galerie Azure Marketplace.
5. Après avoir effectué les modifications, accédez à l’onglet **PUBLIER** et cliquez sur le bouton **DÉPLOYER DANS UN ENVIRONNEMENT INTERMÉDIAIRE**. Pour des instructions détaillées sur le test de votre offre dans l’environnement intermédiaire, reportez-vous à ce [lien](marketplace-publishing-vm-image-test-in-staging.md)
6. Une fois que vous avez testé votre offre dans l’environnement intermédiaire, accédez à l’onglet **PUBLIER** dans le portail de publication et cliquez sur le bouton **DEMANDER UNE APPROBATION POUR LANCER LA PRODUCTION** pour republier votre offre dans Azure Marketplace.

    ![dessin](media/marketplace-publishing-vm-image-post-publishing/img02.7.png)

## 3\. Comment ajouter une nouvelle référence (SKU) à une offre en direct

Vous pouvez ajouter une nouvelle référence (SKU) à votre offre en direct en suivant les étapes ci-dessous :

1. Connectez-vous au [portail de publication](https://publish.windowsazure.com).
2. Accédez à l’onglet **MACHINES VIRTUELLES** et sélectionnez votre offre.
3. Dans le menu de gauche, cliquez sur l’onglet **RÉFÉRENCES (SKU)**. Cliquez ensuite sur le bouton **AJOUTER UNE RÉFÉRENCE (SKU)**. Une nouvelle boîte de dialogue s’affiche. Entrez un identificateur de référence (SKU) en minuscules. Cochez la case Choisir votre propre modèle de facturation (BYOL) si vous souhaitez publier de nouvelles références (SKU) avec un modèle de facturation BYOL. Sinon, décochez-la. Ensuite, cliquez sur la coche de la boîte de dialogue pour créer une référence (SKU). Si vous n’avez pas opté pour le modèle de facturation BYOL pour la nouvelle référence (SKU), le modèle de facturation sera automatiquement défini sur Toutes les heures pour la nouvelle référence (SKU). Si vous souhaitez activer la version d’évaluation gratuite de 30 jours pour le modèle de facturation horaire, cliquez sur « Un mois » pour l’option « Une version d’évaluation gratuite est-elle disponible ? ». Sinon, sélectionnez AUCUNE VERSION D’ÉVALUATION. [Remarque : l’option « Une version d’évaluation gratuite est-elle disponible ? » s’affiche uniquement si vous n’avez PAS sélectionné BYOL dans la boîte de dialogue lors de la création de la référence (SKU).]

    >[AZURE.IMPORTANT] L’option « Masquer cette référence (SKU) à partir du Marketplace, car elle doit toujours être achetée via un modèle de solution » doit être marquée comme « OUI » UNIQUEMENT si vous êtes autorisé à publier une offre de modèle de solution dans Azure Marketplace. Dans le cas contraire, cette option doit toujours être marquée comme « NON ».

4. Maintenant, dans le menu de gauche, cliquez sur l’onglet **IMAGES DE MACHINE VIRTUELLE** et découvrez la référence (SKU) que vous avez créée.
5. Pour configurer la nouvelle référence (SKU), reportez-vous à l’ÉTAPE 5 de ce [lien](marketplace-publishing-vm-image-creation.md#5-obtain-certification-for-your-vm-image) pour obtenir des instructions.
6. Pour ajouter la documentation marketing d’une nouvelle référence (SKU), reportez-vous à la section Étape 1: Fournir du contenu marketing à Marketplace -> Détails -> Points 2 à 5 de ce [lien](marketplace-publishing-push-to-staging.md#step-1-provide-marketplace-marketing-content).
7. Pour ajouter les informations de tarification de la nouvelle référence (SKU), reportez-vous à la section 2.1. Définissez les prix de machine virtuelle de ce [lien](marketplace-publishing-push-to-staging.md#step-2-set-your-prices)
8. Après avoir effectué les modifications, accédez à l’onglet **PUBLIER** et cliquez sur le bouton **DÉPLOYER DANS UN ENVIRONNEMENT INTERMÉDIAIRE**. Pour des instructions détaillées sur le test de votre offre dans l’environnement intermédiaire, reportez-vous à ce [lien](marketplace-publishing-vm-image-test-in-staging.md)
9. Une fois que vous avez testé votre offre dans l’environnement intermédiaire, accédez à l’onglet **PUBLIER** dans le portail de publication et cliquez sur le bouton **DEMANDER UNE APPROBATION POUR LANCER LA PRODUCTION** pour republier votre offre dans Azure Marketplace.

    ![dessin](media/marketplace-publishing-vm-image-post-publishing/img03.1-1.png)

    ![dessin](media/marketplace-publishing-vm-image-post-publishing/img03.1-2.png)

## 4\. Comment supprimer une offre en direct ou une référence (SKU) d’Azure Marketplace

Différents aspects doivent être pris en compte en cas de demande de suppression d’une offre en direct. Suivez les étapes ci-dessous pour obtenir des instructions de l’équipe de support afin de supprimer une offre en direct/une référence (SKU) d’Azure Marketplace :

1.	Ouvrez un ticket de support en utilisant ce [lien](https://support.microsoft.com/fr-FR/getsupport?wf=0&tenant=ClassicCommercial&oaspworkflow=start_1.0.0.0&locale=fr-FR&supportregion=fr-FR&pesid=15635&ccsid=635993707583706681).
2.	Pour le type de problème, sélectionnez **« Gestion des offres »**, puis pour la catégorie, sélectionnez **« Modification d’une offre et/ou d’une référence (SKU) déjà en production »**.
3.	Envoyez la demande.

L’équipe de support vous guidera tout au long du processus de suppression de l’offre/de la référence (SKU).

>[AZURE.NOTE] Vous pouvez toujours supprimer l’offre lorsqu’elle est à l’état de brouillon (c’est-à-dire, ni à l’état INTERMÉDIAIRE ni EN PRODUCTION) en cliquant sur le bouton **SUPPRIMER LE BROUILLON** sous l’onglet **HISTORIQUE**.

## Voir aussi
- [Mise en route : publication d’une offre dans Azure Marketplace](marketplace-publishing-getting-started.md)
- [Présentation du rapport Informations du vendeur](marketplace-publishing-report-seller-insights.md)
- [Présentation du rapport Revenus](marketplace-publishing-report-payout.md)
- [Modification de « l'offre incitative revendeurs » des fournisseurs de solutions Cloud](marketplace-publishing-csp-incentive.md)
- [Résolution des problèmes de publication courants dans le Marketplace](marketplace-publishing-support-common-issues.md)
- [Obtenir de l’aide en tant qu’éditeur](marketplace-publishing-get-publisher-support.md)
- [Création d’une image de machine virtuelle locale](marketplace-publishing-vm-image-creation-on-premise.md)
- [Création d’une machine virtuelle exécutant Windows dans le portail Azure en version préliminaire](../virtual-machines/virtual-machines-windows-hero-tutorial.md)

<!---HONumber=AcomDC_0608_2016-->