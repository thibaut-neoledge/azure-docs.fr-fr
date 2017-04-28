---
title: "Publier une machine virtuelle sur la Place de marché Microsoft Azure | Microsoft Docs"
description: "Cet article fournit des détails concernant la publication d’une machine virtuelle via le portail Microsoft Cloud Partner."
services: cloud-partner-portal
documentationcenter: 
author: anuragdalmia
manager: hamidm
ms.service: cloud-partner-portal
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 03/22/2017
ms.author: andalmia
translationtype: Human Translation
ms.sourcegitcommit: db7cb109a0131beee9beae4958232e1ec5a1d730
ms.openlocfilehash: 6e5b1eb51c10bb51762f6815eda72add1341a4ad
ms.lasthandoff: 04/18/2017


---
# <a name="publish-a-virtual-machine-to-azure-marketplace"></a>Publier une machine virtuelle sur la Place de marché Microsoft Azure

Cet article énonce les diverses étapes de la publication d’une machine virtuelle sur la Place de marché Microsoft Azure.

## <a name="what-are-pre-requisites-for-publishing-a-vm"></a>Conditions préalables à la publication d’une machine virtuelle

Conditions préalables à la publication sur la Place de marché Microsoft Azure

1.  Techniques

    -   [Conditions techniques préalables à la création d’une image de machine virtuelle pour la Place de marché](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation-prerequisites)

    -   [Création et chargement d’un disque dur virtuel Linux](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-create-upload-generic?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

    -   [Créer et tester une machine virtuelle Linux à partir d’une image](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-upload-vhd)

    -   [Création et chargement d’un disque dur virtuel Windows](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-prepare-for-upload-vhd-image?toc=/azure/virtual-machines/windows/toc.json)

    -   [Créer et tester une machine virtuelle Windows à partir d’une image](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-create-vm-generalized-managed?toc=/azure/virtual-machines/windows/toc.json)

    -   [Comment résoudre les problèmes courants rencontrés lors de la création d’un disque dur virtuel](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation-troubleshooting )


2.  Non techniques (critères de l’entreprise)

    -   Votre entreprise (ou sa filiale) doit être située dans un pays expéditeur pris en charge par la Place de marché Azure.

    -   Votre produit doit être disponible sous licence d’une manière compatible avec les modèles de facturation pris en charge par la Place de marché Microsoft Azure.

    -   Il vous incombe de consentir tous les efforts raisonnables pour mettre à la disposition de vos clients un support technique gratuit, payant ou accessible via une communauté.

    -   Il vous incombe de gérer les licences de vos logiciels et de toutes les dépendances de logiciels tiers.

    -   Vous devez fournir un contenu conforme aux critères définis pour que votre offre soit répertoriée sur la Place de marché Microsoft Azure et sur le portail de gestion Microsoft Azure.

    -   Vous devez accepter les termes de la Stratégie de participation et du Contrat d’éditeur de la Place de marché Microsoft Azure.

    - Vous devez vous engager à respecter les [Conditions d’utilisation](https://azure.microsoft.com/support/legal/website-terms-of-use/), la [Déclaration de confidentialité Microsoft](http://www.microsoft.com/privacystatement/default.aspx) et le [Contrat relatif au programme Microsoft Azure Certified](https://azure.microsoft.com/support/legal/marketplace/certified-program-agreement/).

## <a name="how-to-create-a-new-vm-offer"></a>Comment créer une offre de machine virtuelle

Une fois toutes les conditions préalables réunies, vous êtes prêt à commencer à créer votre offre de machine virtuelle (VM). Avant de commencer, voici une brève présentation de ce que sont une offre et une référence (SKU)

### <a name="offer"></a>Offre
Une offre de machine virtuelle correspond à une classe d’offre de produit d’un éditeur. Si vous disposez d’un nouveau genre de produit ou de machine virtuelle que vous souhaitez rendre disponibles sur la Place de marché Microsoft Azure, une nouvelle offre constitue la meilleure option. Une offre est une collection de références (SKU). Toutes les offres apparaissent en tant qu’entités distinctes sur la Place de marché Microsoft Azure. 

### <a name="sku"></a>Référence (SKU)
Une référence (SKU) est la plus petite unité achetable d’une offre. À l’intérieur d’une même classe de produit (offre), les références (SKU) vous permettent de faire la distinction entre les fonctionnalités, les types d’images de machine virtuelle et les modèles de facturation pris en charge. 

Ajoutez plusieurs références (SKU) si vous souhaitez prendre en charge différents modèles de facturation, tels que BYOL (apportez votre propre licence), PAYG (payez au fur et à mesure), etc. Ajoutez plusieurs références (SKU) lorsque chacune d’elles prend en charge un ensemble différent de fonctionnalités et est tarifée différemment. Ajoutez également plusieurs références (SKU) si vous avez différentes images de machine virtuelle selon que les clouds sont publics ou souverains. 

Une référence (SKU) apparaît sous l’offre parente sur la Place de marché Microsoft Azure, tandis qu’elle apparaît en tant qu’entité achetable en tant que telle sur le portail Azure. 

1.  Connectez-vous au [portail Microsoft Cloud Partner](http://cloudpartner.azure.com/).

2.  Dans la barre de navigation à gauche, cliquez sur « + Nouvelle offre », puis sélectionnez « Machines Virtuelles ».

    ![publishvm1][1]

3.  Un nouvel affichage d’offre « Éditeur » est à présent ouvert, et nous sommes prêts à créer.

   ![publishvm2][2]

4.  Les « Formulaires » à compléter sont visibles à gauche en mode « Éditeur ». Chaque « formulaire » se compose d’un ensemble de champs à compléter. Les champs obligatoires sont indiqués par un astérisque rouge (\*).

> Il existe 4 formulaires principaux à compléter pour la création d’une machine virtuelle

-   Paramètres de l’offre

-   Références (SKU)

-   Marketplace

-   Support

## <a name="how-to-fill-out-the-offer-settings-form"></a>Comment remplir le formulaire Paramètres de l’offre
Le formulaire Paramètres de l’offre est un formulaire de base. Les différents champs sont décrits ci-dessous.

### <a name="offer-id"></a>ID de l’offre
Il s’agit d’un identificateur unique pour l’offre au sein d’un profil d’éditeur. Cet ID est visible dans les URL de produit, les modèles ARM et les états de facturation. Il ne peut comprendre que des caractères alphanumériques en minuscules ou des tirets (-). Il ne peut pas se terminer par un tiret et ne peut pas comprendre plus de 50 caractères. Notez que ce champ est verrouillé une fois l’offre publiée.

Par exemple, si l’éditeur **contoso** publie une offre dont l’ID est **exemple-vm**, celle-ci s’affiche sur la Place de marché Microsoft Azure en tant que **https://azuremarketplace.microsoft.com/marketplace/apps/contoso.exemple-vm?tab=Overview**

### <a name="publisher-id"></a>ID de l’éditeur
Cette liste déroulante vous permet de choisir le profil d’éditeur sous lequel vous voulez publier cette offre. Notez que ce champ est verrouillé une fois l’offre publiée.

### <a name="name"></a>Nom
Ceci est le nom d’affichage de votre offre. C’est le nom qui apparaît sur la [Place de marché Microsoft Azure](https://azuremarketplace.microsoft.com) et sur le [portail Azure](https://portal.azure.com/). Il peut pas comprendre plus de 50 caractères. Il est conseillé ici d’inclure un nom de marque reconnaissable pour votre produit. N’incluez pas ici le nom de votre entreprise, sauf si c’est le nom sous lequel l’offre est commercialisée. Si vous commercialisez cette offre sur votre propre site web, assurez-vous que le nom présente rigoureusement le même aspect que sur votre site.

Cliquez sur « Enregistrer » pour enregistrer votre progression. Étape suivante consiste à ajouter des références (SKU) pour votre offre.

## <a name="how-to-create-skus"></a>Comment créer des références (SKU)
Cliquez sur le formulaire « références (SKU) ». Vous pouvez voir ici l’option « Ajouter une référence (SKU) » sur laquelle vous pouvez cliquer pour entrer un « ID de SKU ».

![publishvm4][4]

Ceci est un identificateur unique pour la référence (SKU) au sein d’une offre. Cet ID est visible dans les URL de produit, les modèles ARM et les états de facturation. Il ne peut comprendre que des caractères alphanumériques en minuscules ou des tirets (-). Il ne peut pas se terminer par un tiret et ne peut pas comprendre plus de 50 caractères. Notez que ce champ est verrouillé une fois l’offre publiée. Vous pouvez avoir plusieurs références (SKU) au sein d’une même offre. Vous avez besoin d’une référence (SKU) pour chaque image que vous prévoyez de publier.

Après l’ajout d’une référence (SKU), celle-ci apparaît dans la liste des références (SKU) dans le formulaire « Références (SKU) ». Pour obtenir des détails sur une référence (SKU), cliquez sur son nom. Voici quelques détails pour certains champs.

### <a name="hide-this-sku"></a>Masquer cette référence (SKU)
Cet indicateur vous permet de définir si cette référence SKU spécifique est visible aux clients sur la [Place de marché Microsoft Azure](https://azuremarketplace.microsoft.com) et le [portail Azure](https://portal.azure.com/). Vous pouvez choisir de masquer la référence (SKU) si vous souhaitez qu’elle soit disponible via des modèles de solution, mais pas en tant qu’achat individuel.

### <a name="cloud-availability"></a>Disponibilité dans le cloud 
Ce champ vous permet de définir la disponibilité de votre référence (SKU) dans les différents clouds Azure.

#### <a name="public-azure"></a>Cloud public Azure 

Cette référence (SKU) peut être déployée vers des clients dans toutes les régions Azure publiques intégrées à la Place de marché.

#### <a name="azure-government-cloud"></a>Azure Government Cloud 

Cette référence peut être déployée dans l’Azure Government Cloud. Avant de publier sur [Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-marketplace-partners), nous recommandons aux éditeurs de tester et valider que leur solution fonctionne comme prévu. Pour effectuer une copie intermédiaire et tester, demandez un compte d’évaluation [ici](https://azure.microsoft.com/offers/ms-azr-usgov-0044p). Veuillez noter que Microsoft Azure Government est un cloud communautaire pour le secteur public, accessible à des clients d’administrations fédérales, étatiques, locales ou tribales des États-Unis, ainsi qu’à des partenaires éligibles pour servir ces entités.

### <a name="countryregion-availability"></a>Disponibilité par pays/région
Ce champ détermine les régions dans lesquelles votre référence (SKU) sera disponible à l’achat. Vous devez considérer soigneusement celles dans lesquelles vous rendrez vos références disponibles. Certains pays sont classés « Microsoft Tax Remitted Country ».

-   Dans les pays « Microsoft Tax Remitted Country », Microsoft collecte des taxes auprès des clients et les verse au gouvernement.

-   Dans les autres pays, les partenaires sont responsables de la collecte des taxes et de leur versement au gouvernement. Si vous choisissez de vendre dans ces pays, vous devez avoir la capacité de calculer ces taxes et de les y verser.

![publishvm5][5]

### <a name="pricing"></a>Tarification 
Deux modèles de tarification sont actuellement pris en charge.

#### <a name="bring-your-own-license-byol"></a>BYOL (apportez votre propre licence)

Vous gérez la licence du logiciel en cours d’exécution sur la machine virtuelle. Microsoft facture uniquement les coûts d’infrastructure.

#### <a name="usage-based-monthly-billed-sku"></a>Référence (SKU) facturée mensuellement sur la base de l’utilisation

Les clients sont facturés à l’heure sur la base des tarifs définis par les éditeurs en fonction des tailles de machines virtuelles. Dans le cas du modèle de **facturation horaire** des références, le prix total est la somme du coût logiciel facturé par l’éditeur et du coût d’infrastructure facturé par Microsoft. Ce coût total s’affiche au client sous la forme d’un tarif horaire et d’un prix mensuel au moment où il envisage l’achat. Dans ce cas, la facturation est effectuée mensuellement.

Dans le modèle basé sur l’utilisation, vous fixez des paramètres supplémentaires.

##### <a name="free-trial"></a>Version d’évaluation gratuite

Vous pouvez spécifier si vous souhaitez proposer un essai gratuit à vos clients. Ici, le client n’est pas facturé pour le coût du logiciel (gratuit) pendant les 30 premiers jours après le déploiement de la machine virtuelle. À l’issue de ces 30 jours, le client est facturés à l’heure en fonction des tarifs définis par l’éditeur dans le modèle horaire.

##### <a name="per-core-pricing"></a>Tarification par cœur

Vous pouvez définir une tarification par cœur pour votre référence (SKU). Dans ce cas, vous devez simplement entrer un prix de base pour un seul cœur. Ensuite, nous calculons automatiquement le prix pour le reste des cœurs. Entrez Les prix en USD sur le portail. Nous calculons automatiquement les prix pour les autres régions. Vous pouvez vérifier les prix dans les autres régions en **exportant les données de tarification**.

![publishvm6][6]

##### <a name="discrete-pricing"></a>Tarification distincte

Vous pouvez définir la tarification individuellement pour chaque ensemble de cœurs si vous souhaitez tarifer chaque cœur séparément.

![publishvm7][7]

##### <a name="export-import-pricing"></a>Exporter/Importer la tarification

Vous avez la possibilité d’exporter la tarification configurée via le portail pour apporter des modifications dans l’interface Excel. Cela vous permet également de vérifier la tarification par région et la tarification dans les devises locales. L’option d’**exportation de la tarification** vous permet de télécharger un fichier Excel dans lequel les détails de la tarification sont renseignés. Vous pouvez modifier ceux-ci dans Excel, puis utiliser l’option d’**importation de la tarification** pour importer les modifications apportées. La tarification importée s’affiche également sur le portail.

Dans cette tarification Excel, les prix pour les différentes régions sont indiqués dans la devise locale. Le taux de change que nous utilisons est actualisé quotidiennement.

![publishvm8][8]

##### <a name="important-notes-about-pricing"></a>Remarques importantes concernant la tarification

-   Les prix ne sont pas modifiables une fois l’offre publiée. Vous pouvez toujours ajouter ou supprimer des régions desservies.

-   Ce prix est facturé à l’utilisateur en plus de la [tarification de machine virtuelle d’Azure](http://aka.ms/vmpricingdetails).

-   Les prix sont définis pour toutes les régions en devise locale sur la base des taux de change disponibles au moment de leur établissement.

-   Pour définir ou afficher individuellement le prix pour chaque région, exportez la feuille de calcul de tarification, puis importez-la avec une tarification personnalisée.

### <a name="vm-images"></a>Images de machine virtuelle
La section suivante est celles consacrée aux Images de machine virtuelle. Avant de passer à cette section, vous devez avoir préparé le disque dur virtuel que vous souhaitez publier. Voici quelques liens pour vous aider à créer votre disque dur virtuel :

-   [Conditions techniques préalables à la création d’une image de machine virtuelle pour la Place de marché](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation-prerequisites)

-   [Création et chargement d’un disque dur virtuel Linux](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-create-upload-generic?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

-   [Créer et tester une machine virtuelle Linux à partir d’une image](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-linux-upload-vhd)

-   [Création et chargement d’un disque dur virtuel Windows](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-prepare-for-upload-vhd-image?toc=/azure/virtual-machines/windows/toc.json)

-   [Créer et tester une machine virtuelle Windows à partir d’une image](https://docs.microsoft.com/azure/virtual-machines/virtual-machines-windows-create-vm-generalized-managed?toc=/azure/virtual-machines/windows/toc.json)

-   [Comment résoudre les problèmes courants rencontrés lors de la création d’un disque dur virtuel](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation-troubleshooting )


Une fois votre disque dur virtuel prêt, vous pouvez commencer à compléter cette section. Voici quelques détails pour certains champs.

#### <a name="recommended-vm-sizes"></a>Tailles de machines virtuelles recommandées

Sélectionnez jusqu’à six tailles de machine virtuelle recommandées. Ces recommandations sont présentées à l’utilisateur sur la Place de marché Microsoft Azure et dans le panneau Niveau tarifaire sur le portail Azure s’il décide d’acheter et de déployer votre image. **Il s’agit uniquement de recommandations. Le client peut sélectionner n’importe quelle taille de machine virtuelle prenant en charge les disques spécifiés dans votre image.** Vous pouvez voir ci-dessous la manière dont les tailles de machines virtuelles apparaissent au client sur le portail Azure.

![publishvm9][9]

#### <a name="open-ports"></a>Ouvrir des ports

Spécifiez les ports que vous voulez ouvrir et rendre disponibles. Ces ports sont ouverts pendant ce déploiement de machines virtuelles.

#### <a name="adding-vm-images"></a>Ajout d’images de machine virtuelle

L’étape suivante consiste à ajouter une image de machine virtuelle pour votre référence (SKU). Vous pouvez ajouter jusqu’à 8 versions disque par référence (SKU). Seul le numéro de version disque le plus élevé pour une référence (SKU) particulière apparaît sur la Place de marché Microsoft Azure. Les autres sont visibles via des API. 

Commencez par cliquer sur « + Nouvelle version » dans la section **Version disque**. Cela a pour effet d’afficher une série de champs à compléter.

##### <a name="vm-image-version"></a>Version image de machine virtuelle

La version image de machine virtuelle doit suivre le format [version sémantique](http://semver.org/). Les versions doivent être au format X.Y.Z, où X, Y et Z sont des entiers. Les images contenues dans des références SKU différentes peuvent avoir différentes versions majeures et mineures. Les versions au sein d’une même référence SKU ne doivent impliquer que des modifications incrémentielles, qui augmentent la version du correctif (Z de X.Y.Z).

##### <a name="os-vhd-url"></a>URL DU DISQUE DUR VIRTUEL DU SYSTÈME D’EXPLOITATION

Entrez l’[URI de signature d’accès partagé](../../marketplace-publishing/marketplace-publishing-vm-image-creation.md#52-get-the-shared-access-signature-uri-for-your-vm-images) créé pour le disque dur virtuel du système d’exploitation.

Si des disques de données sont associés à cette référence (SKU), vous pouvez les ajouter en cliquant sur le lien « + Nouveau disque de données ». Cela a pour effet d’afficher des champs supplémentaires à compléter.

##### <a name="lun-vhd-url"></a>URL DU DISQUE DUR VIRTUEL DU NUMÉRO D’UNITÉ LOGIQUE (LUN)

Entrez l’[URI de signature d’accès partagé](../../marketplace-publishing/marketplace-publishing-vm-image-creation.md#52-get-the-shared-access-signature-uri-for-your-vm-images) pour votre disque de données.

##### <a name="lun-number"></a>Numéro de LUN

Attribuez un numéro à ce LUN. Ce numéro est réservé pour ce disque de données à l’intérieur de cette référence (SKU).

Notez que, lorsque vous publiez une référence (SKU) avec une version de machine virtuelle spécifique et des disques de données, ces éléments sont verrouillés et ne peuvent plus être modifiés. Pour des versions de machine virtuelle supplémentaires ajoutées à la référence (SKU), le nombre de disques de données à prendre en charge ne peut pas changer.

##### <a name="common-sas-url-issues--fixes"></a>Problèmes communs avec les URL SAS et résolution

| **Problème**                                                           | **Message d’échec**                                                           | **Correctif**                                                   | **Lien vers la documentation**                                                                                    |
|---------------------------------------------------------------------|-------------------------------------------------------------------------------|-----------------------------------------------------------|-----------------------------------------------------------------------------------------------------------|
| Échec lors de la copie d’images : « ? » est introuvable dans l’URL SAS             | Échec : copie d’images. Impossible de télécharger l’objet blob avec l’URI SAS fournie.    | Mettez à jour l’URL SAS avec les outils recommandés                | <https://docs.microsoft.com/fr-fr/azure/storage/storage-dotnet-shared-access-signature-part-1> |
| Échec lors de la copie d’images : les paramètres « st » et « se » ne sont pas présents dans l’URL SAS | Échec : copie d’images. Impossible de télécharger l’objet blob avec l’URI SAS fournie.    | Mettez à jour l’URL SAS en ajoutant les dates de début et de fin         | <https://docs.microsoft.com/fr-fr/azure/storage/storage-dotnet-shared-access-signature-part-1> |
| Échec lors de la copie d’images : « sp=rl » n’est pas présent dans l’URL SAS                  | Échec : copie d’images. Impossible de télécharger l’objet blob avec l’URI SAS fournie     | Mettez à jour l’URL SAS avec les autorisations définies sur « Lecture » et « Liste » | <https://docs.microsoft.com/fr-fr/azure/storage/storage-dotnet-shared-access-signature-part-1> |
| Échec lors de la copie d’images : l’URL SAS contient des espaces blancs dans le nom du disque dur virtuel   | Échec : copie d’images. Impossible de télécharger l’objet blob avec l’URI SAS fournie.    | Mettez à jour l’URL SAS sans espaces blancs                   | <https://docs.microsoft.com/fr-fr/azure/storage/storage-dotnet-shared-access-signature-part-1> |
| Échec lors de la copie d’images : erreur d’autorisation d’URL SAS             | Échec : copie d’images. Impossible de télécharger l’objet blob en raison d’une erreur d’autorisation | Générez à nouveau l’URL SAS                                    | <https://docs.microsoft.com/fr-fr/azure/storage/storage-dotnet-shared-access-signature-part-1> |

## <a name="marketplace-form"></a>Formulaire de la Place de marché
Le formulaire marketplace à l’intérieur d’une offre de machine virtuelle comprend des champs qui doivent apparaître sur la [Place de marché Microsoft Azure](https://azuremarketplace.microsoft.com) et le [portail Azure](https://portal.azure.com/). Voici quelques détails pour certains champs.

#### <a name="preview-subscription-ids"></a>ID d’abonnement pour version préliminaire

Entrez ici la liste des ID d’abonnement Azure dont vous voulez qu’ils aient accès à l’offre dès sa publication. Ces abonnements autorisés vous permettent de tester l’offre préliminaire avant sa publication. Le portail Microsoft Cloud Partner vous permet d’autoriser jusqu’à 100 abonnements.

#### <a name="suggested-categories"></a>Catégories suggérées

Sélectionnez dans la liste fournie jusqu’à 5 catégories auxquelles votre offre peut être associée au mieux. Les catégories sélectionnées servent à mapper votre offre aux catégories de produits disponibles sur la [Place de marché Microsoft Azure](https://azuremarketplace.microsoft.com) et le [portail Azure](https://portal.azure.com/).

Voici certains des emplacements où apparaissent les données que vous entrez dans ce formulaire.

##### <a name="azure-marketplace"></a>Azure Marketplace

![publishvm10][10]

![publishvm11][11]

![publishvm15][15]

##### <a name="azure-portal"></a>Portail Azure

![publishvm12][12]

![publishvm13][13]

##### <a name="logo-guidelines"></a>Instructions concernant le logo

Tous les logos chargés sur le portail Microsoft Cloud Partner doivent respecter les instructions ci-dessous :

-   Le design Azure a une palette de couleurs simple. Limitez le nombre de couleurs primaires et secondaires sur votre logo.

-   Les couleurs de thème du portail Azure sont le blanc et le noir. Par conséquent, évitez d’utiliser ces couleurs comme couleur d’arrière-plan pour vos logos. Utilisez des couleurs qui mettent vos logos en évidence dans le portail Azure. Nous vous recommandons d’utiliser des couleurs primaires simples. **Si vous utilisez un arrière-plan transparent, assurez-vous que le logo/texte n’est pas blanc, noir ou bleu.**

-   N'utilisez pas d’arrière-plan dégradé sur le logo.

-   Évitez de placer du texte, même s’il s’agit du nom votre société ou de votre marque, sur le logo. L'apparence de votre logo doit être « plate » et éviter les dégradés.

-   Le logo ne doit pas être étiré.

##### <a name="hero-logo"></a>Bannière

La bannière est facultative. L’éditeur peut choisir de ne pas télécharger de bannière. En revanche, une fois la bannière téléchargée, il n’est plus possible de la supprimer. À ce moment, le partenaire doit suivre les instructions d’Azure Marketplace pour les icônes.

###### <a name="guidelines-for-the-hero-logo-icon"></a>Recommandations concernant l’icône Bannière

-   Le nom d’affichage de l’éditeur, le titre du plan et le résumé long de l’offre s’affichent en couleur de police blanche. Par conséquent, vous devez éviter de conserver toute couleur claire dans l’arrière-plan l’icône. Les arrière-plans noirs, blancs et transparents ne sont pas autorisés pour les icônes.

-   Le nom d’affichage de l’éditeur, le titre du plan, le résumé long de l’offre et le bouton Créer sont incorporés automatiquement à la bannière une fois l’offre publiée. Vous ne devez par conséquent pas saisir de texte lorsque vous concevez la bannière. Laissez simplement un espace vide sur la droite, car le texte (à savoir le nom d’affichage de l’éditeur, le titre du plan et le résumé long de l’offre) sera inclus automatiquement de notre côté. L’espace vide pour le texte doit être de 415 x 100 sur la droite (décalé de 370px à partir de la gauche).

![publishvm14][14]

#### <a name="lead-management"></a>Gestion des prospects

Pour spécifier les paramètres de gestion des prospects de l’offre, suivez les instructions [ici](./cloud-partner-portal-get-customer-leads.md#how-to-connect-your-crm-system-with-the-cloud-partner-portal).

## <a name="support-form"></a>Formulaire de prise en charge
Le formulaire suivant à remplir est celui relatif à la prise en charge. Ce formulaire vous demande de spécifier les contacts de votre entreprise, tels que les contacts du support technique et du support client. 

## <a name="how-to-publish-an-offer"></a>Comment publier une offre
À présent que votre offre est élaborée, l’étape suivante consiste à la publier sur la Place de marché Microsoft Azure. Suivez les instructions fournies dans [Publier votre offre sur la Place de marché Microsoft Azure](./Cloud-partner-portal-make-offer-live-on-Azure-Marketplace.md). 

[1]: ./media/cloud-partner-portal-publish-virtual-machine/publishvm1.png
[2]: ./media/cloud-partner-portal-publish-virtual-machine/publishvm2.png
[3]: ./media/cloud-partner-portal-publish-virtual-machine/publishvm3.png
[4]: ./media/cloud-partner-portal-publish-virtual-machine/publishvm4.png
[5]: ./media/cloud-partner-portal-publish-virtual-machine/publishvm5.png
[6]: ./media/cloud-partner-portal-publish-virtual-machine/publishvm6.png
[7]: ./media/cloud-partner-portal-publish-virtual-machine/publishvm7.png
[8]: ./media/cloud-partner-portal-publish-virtual-machine/publishvm8.png
[9]: ./media/cloud-partner-portal-publish-virtual-machine/publishvm9.png
[10]: ./media/cloud-partner-portal-publish-virtual-machine/publishvm10.png
[11]: ./media/cloud-partner-portal-publish-virtual-machine/publishvm11.png
[12]: ./media/cloud-partner-portal-publish-virtual-machine/publishvm12.png
[13]: ./media/cloud-partner-portal-publish-virtual-machine/publishvm13.png
[14]: ./media/cloud-partner-portal-publish-virtual-machine/publishvm14.png
[15]: ./media/cloud-partner-portal-publish-virtual-machine/publishvm15.png
