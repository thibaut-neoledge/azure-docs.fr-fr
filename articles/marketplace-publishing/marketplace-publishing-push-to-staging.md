<properties
   pageTitle="Préparer et tester votre offre pour le déploiement sur Azure Marketplace | Microsoft Azure"
   description="Instructions détaillées sur l’indication de contenu marketing, la configuration de plans de tarification et le test de votre offre avant le déploiement sur Azure Marketplace."
   services="marketplace-publishing"
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
   ms.date="04/26/2016"
   ms.author="hascipio"/>

# Compléter la création d’offre avec du contenu marketing
Dans cette étape du processus de publication, vous devez fournir du contenu marketing et des informations sur votre offre et/ou vos références SKU dans Azure Marketplace. Par exemple, vous devez fournir une description de votre produit, le logo de votre entreprise, les offres tarifaires, le détail des offres et d’autres informations nécessaires pour envoyer votre offre et/ou référence SKU dans l’environnement intermédiaire. Ces informations sont utilisées comme contenu marketing dans le portail Azure. Vous allez commencer ce processus dans le [portail de publication][link-pubportal].

## Étape 1 : Fournir du contenu marketing au Marketplace
**L’anglais est la langue par défaut et la seule prise en charge.** Assurez-vous que toutes les informations fournies dans les champs sont en anglais. Toutes les informations peuvent être modifiées à tout moment jusqu’à ce que vous procédiez au déploiement dans un environnement intermédiaire.

  1. Accédez au portail de publication à l’adresse [https://publish.windowsazure.com](https://publish.windowsazure.com)
  2. Dans le menu de gauche, cliquez sur l’onglet **Marketing**.
  3. Dans le volet principal, cliquez sur le bouton **English (US)**.

  > [AZURE.IMPORTANT] Tous les champs doivent avoir des entrées, y compris les images, pour que vous puissiez procéder au déploiement dans un environnement intermédiaire.

### Détails
1. Entrez le titre de l'offre (50 caractères maximum), le résumé de l’offre (100 caractères maximum), le résumé long de l’offre (256 caractères maximum), la description de l’offre (1 300 caractères maximum) et les logos sous l’onglet **Détails**
2. Entrez le titre de la référence (50 caractères maximum), le résumé de la référence (100 caractères maximum), la description de la référence (2 000 caractères maximum) sous l’onglet **Plans**
3. N'entrez pas deux fois le même texte dans la description de l’offre et de la référence.
4. N’entrez pas deux fois le même texte sous le titre de la référence et le résumé long de l’offre.
5. N’entrez pas deux fois le même texte sous le titre de la référence et le résumé de l’offre.
6. Téléchargez des images des spécifications requises (mentionnées sur le portail de publication) au format PNG, une pour chaque taille.
7. Vérifiez que les logos suivent les instructions relatives aux logos Azure Marketplace indiquées ci-dessous.

  ![dessin](media/marketplace-publishing-push-to-staging/pubportal-marketingcontent-details-02.png)

**Instructions relatives aux logos Azure Marketplace**

Tous les logos téléchargés dans le portail de publication doivent respecter les instructions ci-dessous :

- Le design Azure a une palette de couleurs simple. Veuillez limiter le nombre de couleurs primaires et secondaires sur les logos.
- Les logos ne doivent pas être placés sur un arrière-plan blanc. Nous recommandons des couleurs primaires simples ou des arrière-plans transparents.
- N'utilisez pas d’arrière-plan dégradé sur le logo.
- Évitez de placer du texte, même s’il s’agit du nom votre société ou de votre marque, sur le logo.
- L'apparence de votre logo doit être « plate » et éviter les dégradés.
- Le logo ne doit pas être étiré.
- Les petits logos doivent être de la taille 40 x 40 px
- Les logos moyens doivent être de la taille 90 x 90 px
- Les grands logos doivent être de la taille 115 x 115 px
- Les logos larges doivent être de la taille 255 x 115 px
- Les bannières doivent être de la taille 815 x 290 px

  ![dessin](media/marketplace-publishing-push-to-staging/pubportal-marketingcontent-details-03.png)

**Recommandations supplémentaires pour les bannières :**

- La bannière est facultative. Vous pouvez choisir de ne pas télécharger de bannière.
- Le nom d’affichage de l’éditeur, le titre de la référence, le résumé long de l’offre et le bouton Créer sont incorporés automatiquement à la bannière une fois l'offre publiée. Vous n'avez par conséquent pas besoin de les entrer lorsque vous concevez la bannière.
- Étant donné que le nom d'affichage de l'éditeur, le titre de la référence et le résumé de l’offre sont affichés en blanc, il est conseillé d’éviter de conserver un arrière-plan blanc ou de couleur claire pour la bannière.
- Laissez de l'espace pour le texte ci-dessus en haut de la bannière. L'espace pour le texte est de 415 x 100, décalé de 370 px sur la gauche.

  ![dessin](media/marketplace-publishing-push-to-staging/pubportal-herobanner.png)

### Liens
Sous l’onglet **Links** (Liens) de la barre de gauche, entrez des liens utiles ainsi que toute information susceptible d’aider les clients. Entrez un nom et une URL pour chaque lien.

![dessin](media/marketplace-publishing-push-to-staging/pubportal-marketingcontent-link-01.png)

### Images d’exemple (facultatif)
> [AZURE.NOTE] L’ajout d’une image d’exemple est une étape facultative. Le contenu marketing suffit à remplir les conditions pour l’envoi vers l’environnement intermédiaire.

Sous l’onglet **Sample Images** (Images d’exemple) dans le menu de gauche, cliquez sur **Télécharger une nouvelle image** pour télécharger une image. Si vous avez déjà chargé une image que vous voulez remplacer, cliquez sur **Remplacer l’image**.

![dessin](media/marketplace-publishing-push-to-staging/pubportal-marketingcontent-sampleimg-01.png)

### Informations juridiques
Sous l’onglet **Legal** (Informations légales), indiquez un lien vers votre politique ou vos conditions d’utilisation. Entrez ou collez les conditions d’utilisation dans la grande zone **Conditions d’utilisation**. La limite de caractères pour les conditions d'utilisation est de 1 000 000 caractères.

![dessin](media/marketplace-publishing-push-to-staging/pubportal-marketingcontent-legal-01.png)

> [AZURE.NOTE] Pour les machines virtuelles, vous ne pouvez pas modifier ce qui suit une fois qu'une référence SKU est publiée de manière intermédiaire ou en direct : **Identificateur de l'offre**, **Identificateur de l'éditeur** et **SKU de l'identificateur**.

## Étape 2 : Définir vos tarifs
### Modèles de tarification
|Modèle de tarification |Description |
|---------------|------------------------------------------|
|De base| Forfait mensuel payé au moment de l’achat, par exemple 10 $ par mois|
|Consommation (également appelé Utilisation, Compteur) | Paiement à l’utilisation, définie par l’éditeur de l’offre. Le dépassement ne peut pas être exprimé en nombre d’utilisateurs ou de licences, puisqu’il est impossible d’utiliser une fraction d’utilisateur ou de calculer un prorata. L’utilisation est signalée par le partenaire sur une base horaire. Le client paye au terme du cycle de facturation mensuel, et non en avance comme pour les offres mensuelles. |
|Essai gratuit | Le client jouit d’une utilisation gratuite pendant une durée limitée, puis paye un tarif normal. |
|Niveau Gratuit | L’offre est toujours gratuite. |
| Migration (également appelé conversion ou mise à niveau/rétrogradation) de l’offre | L’utilisateur change son offre actuelle pour une autre offre autorisée, définie par le partenaire. |

**Modèles de tarification disponibles par type d’offre**

> [AZURE.IMPORTANT] La disponibilité de certains modèles de tarification varie en fonction du type d’offre. Consultez le tableau ci-dessous.

| | De base uniquement | Consommation uniquement | De base + Consommation |
|---|---|---|---|
| Image de machine virtuelle | Non | Oui | Non|
| Service de développement | Oui | Oui | Oui |
| Service de données | Oui | Non | Non |

### 2\.1. Définir les prix de votre machine virtuelle
> [AZURE.NOTE] BYOL est uniquement pris en charge pour les machines virtuelles.

1.	Les marchés pris en charge apparaissent sous l’onglet **Pricing** (Tarifs). Sélectionnez le marché adapté pour ouvrir les champs de tarification.
2.	Le lien fourni sur le portail de publication indiquera les informations de tarification pour vous aider à déterminer les tarifs de vos références SKU.
3.	Si votre référence SKU est BYOL, cochez la case correspondant à la disponibilité des produits dotés d’une licence externe (« Externally-licensed (BYOL) SKU availability »).
4.	Si votre produit est payable à l’heure, entrez le prix de votre logiciel. Les références SKU sans prix ne pourront pas être achetées ou utilisées.

  > [AZURE.NOTE] Si vous avez des références SKU BYOL et horaires, veillez à ce que les conditions requises soient remplies pour chacune : case à cocher BYOL et valeurs de prix pour Horaire.

5.	Un Assistant de tarification s’ouvre. Complétez-en les étapes pour indiquer vos tarifs, y compris les tarifs pour les autres pays, si vous choisissez d’autoriser les achats en dehors du marché que vous avez spécifié.
6.	Certains pays sont des pays de remise ISV. Pour vendre dans un pays de remise ISV, vous devez facturer et collecter les taxes sur vos références SKU, puis calculer et payer celles-ci auprès de l’administration fiscale du pays. Microsoft n’est pas en position de fournir des conseils juridiques ou fiscaux. Consultez la section « Pays de vente de l’offre » sous l’introduction de ce document pour en savoir plus sur les pays de vente.

  > [AZURE.NOTE] Pour les machines virtuelles, vous ne pouvez pas modifier ce qui suit une fois qu'une référence SKU est publiée en direct car cela a un impact sur la facturation des clients existants : **Changement de tarification**, **Changement du modèle de facturation** et **Suppression de régions de facturation**.

### 2\.2. Définir le prix de vos services de développement
Les offres peuvent constituer une combinaison quelconque de tarifs De base + Consommation, où De base est le tarif mensuel et Dépassement est le tarif à l’utilisation. (Voir détails ci-dessous)

**Exemple :** offre de services de développement Contoso

| Planification | Prix | Inclus | Chemin de migration |
|-------|------|-------|-------|
|Gratuit|0 $/mois|Fonctionnalités de base.|Peut migrer vers une autre offre|
|Bronze|10 $/mois|Fonctionnalités de base et un quota de 1 000 pour la fonctionnalité X.|Possibilité de migration vers les offres Bronze Plus, Silver et Gold|
|Bronze Plus|Période d’essai gratuite : 0 $/mois + 0 $/meter01 |Fonctionnalités de base et quota de 10 000 pour la fonctionnalité X. Une fois le quota alloué pour la fonctionnalité X épuisé, le client peut payer à l’utilisation via meter01.|Possibilité de migration vers les offres Silver Plus et Gold|
|Bronze Plus| Période payée (ou essai gratuit expiré) : 10 $/mois + 0,05 $/meter01|Fonctionnalités de base et quota de 10 000 pour la fonctionnalité X. Une fois le quota alloué pour la fonctionnalité X épuisé, le client peut payer à l’utilisation via meter01.|Possibilité de migration vers les offres Silver Plus et Gold|
|Silver|0,15 $/meter01|Le client peut payer à l’utilisation en fonction de meter01, qui s’applique à la fonctionnalité X.|Possibilité de migration vers les offres Bronze et Gold|
|Silver Plus|20 $/mois + 0,15 $/meter01 + 0,01 $/meter02|Fonctionnalités de base et un quota de 10 000 pour la fonctionnalité X et de 100 pour la fonctionnalité Y. Une fois le quota pour la fonctionnalité X utilisé, le client peut payer en fonction de l’utilisation via meter01. Une fois le quota pour la fonctionnalité Y utilisé, le client peut payer en fonction de l’utilisation via meter02.|Possibilité de migration vers les offres Bronze Plus et Gold|
|Gold|1 000 $ par mois|Quota de 10 000 pour la fonctionnalité X, 1 000 pour la fonctionnalité Y et illimité pour la fonctionnalité Z.|Permet de migrer vers toutes les offres sauf Gratuit|

## Étape 3 : Fournir les informations de support
Les coordonnées sont utilisées uniquement pour les communications internes entre le partenaire et Microsoft. L’URL de support sera disponible pour les clients finaux.

1.	Accédez à l’en-tête **Support** à gauche du portail de publication.
2.	Entrez les informations sous **Engineering Contact** (Contact Ingénieur).
3.	Entrez les informations sous **Customer Support** (Service client). Si vous indiquez uniquement un support électronique, entrez un numéro de téléphone factice, et l’adresse de messagerie fournie sera utilisée.
4.	Entrez l’URL de support.

## Étape 4 : Choisir les catégories Azure Marketplace
L’onglet **Categories** (Catégories) offre diverses options. Votre offre peut appartenir à l’une d’elles et vous pouvez sélectionner jusqu’à cinq catégories.

## Présentation de votre contenu marketing
Voici une vue détaillée de l’utilisation des informations marketing de l’offre sur le [site web Azure Marketplace](https://azure.microsoft.com/marketplace/) et dans le [portail Azure](https://portal.azure.com).

### Site web Azure Marketplace
![dessin](media/marketplace-publishing-push-to-staging/acom-catalog-01.png)

![dessin](media/marketplace-publishing-push-to-staging/acom-catalog-02.png)

*Liste des offres sur le site web Azure Marketplace*

![dessin](media/marketplace-publishing-push-to-staging/acom-listing-details-01.png)

*Détails de la description de l’offre sur le site web Azure Marketplace*

![dessin](media/marketplace-publishing-push-to-staging/acom-listing-details-02.png)

*Détails de tarification de la description de l’offre sur le site web Azure Marketplace*

### Portail Azure
![dessin](media/marketplace-publishing-push-to-staging/azureportal-galleryblade-01.png)

*Liste des offres dans le portail Azure*

![dessin](media/marketplace-publishing-push-to-staging/azureportal-galleryblade-02.png)

*Détails de la description de l’offre sur le portail Azure*

## Étapes suivantes
Votre contenu Marketplace étant chargé, nous allons maintenant tester votre offre dans l’environnement intermédiaire. Vous devez toutefois sélectionner le type d’offre approprié dans la liste ci-dessous, car les étapes diffèrent selon le type d’offre.

||Image de machine virtuelle |Service de développement | Service de données | Modèle de solution |
|----|----|----|----|----|
| **Étape 3. Déployer votre offre dans un environnement intermédiaire** | [Tester votre offre de machine virtuelle dans un environnement intermédiaire](marketplace-publishing-vm-image-test-in-staging.md) | Version préliminaire privée | [Tester votre offre de service de données dans un environnement intermédiaire](marketplace-publishing-data-service-test-in-staging.md) | [Tester votre modèle de solution dans un environnement intermédiaire](marketplace-publishing-solution-template-test-in-staging.md) |

## Voir aussi
- [Mise en route : Publication d’une offre sur Azure Marketplace](marketplace-publishing-getting-started.md)

[img-map-acom]: media/marketplace-publishing-push-to-staging/pubportal-mapping-acom.jpg
[img-map-portal]: media/marketplace-publishing-push-to-staging/pubportal-mapping-azure-portal.jpg
[img-map-link]: media/marketplace-publishing-push-to-staging/marketing-content-guide-links.jpg
[img-map-logo]: media/marketplace-publishing-push-to-staging/marketing-content-guide-logos.jpg
[img-map-title]: media/marketplace-publishing-push-to-staging/marketing-content-guide-publisher-offer.png

[link-pubportal]: https://publish.windowsazure.com
[link-push-to-production]: marketplace-publishing-push-to-production.md

<!---HONumber=AcomDC_0504_2016-->