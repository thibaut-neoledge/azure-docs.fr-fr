<properties
   pageTitle="Préparation et test de votre offre pour le déploiement sur Azure Marketplace | Microsoft Azure"
   description="Instructions détaillées sur l’indication de contenu marketing, la configuration de plans de tarification et le test de votre offre avant le déploiement sur Azure Marketplace."
   services="marketplace-publishing"
   documentationCenter=""
   authors="HannibalSII"
   manager=""
   editor=""/>

<tags
   ms.service="marketplace-publishing"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="Azure"
   ms.workload="na"
   ms.date="10/09/2015"
   ms.author="hascipio"/>

# Compléter la création d’offre avec du contenu marketing
Dans cette étape du processus de publication, vous devez fournir un contenu marketing et des informations concernant votre offre et/ou SKU dans Azure Marketplace, telles que la description de votre produit, des logos de société, une tarification, le détail des offres, et toute autre information nécessaire pour envoyer votre offre et/ou SKU dans l’environnement intermédiaire. Ces informations sont utilisées comme contenu marketing dans notre portail Azure. Vous allez commencer ce processus dans le [portail de publication][link-pubportal].

## Étape 1. Fournir du contenu marketing à Marketplace
**L’anglais est la langue définie par défaut et la seule prise en charge.** Vérifiez que les informations fournies dans les champs sont en anglais. Toutes les informations peuvent être modifiées à tout moment jusqu’à ce que vous procédiez au déploiement dans un environnement intermédiaire.

  1. Accédez au portail de publication à l’adresse [https://publish.windowsazure.com](https://publish.windowsazure.com)
  2. Dans le menu de gauche, cliquez sur l’onglet **Marketing**.
  3. Dans le panneau principal, cliquez sur le bouton **English (US)**.

### Détails
1. Entrez un bref résumé, un résumé plus long et une description de votre offre.
2.	Téléchargez des images des spécifications requises (mentionnées sur le portail de publication) au format PNG, une pour chaque taille.

  ![dessin](media/marketplace-publishing-push-to-staging/pubportal-02.png)

  *Titre de l’offre, description et logos*

### Liens
Sous l’onglet Links (Liens) de la barre de gauche, entrez des liens utiles ainsi que toute information susceptible d’aider les clients. Entrez un nom et une URL pour chaque lien.

![dessin](media/marketplace-publishing-push-to-staging/pubportal-02.png)

### Images d’exemple (facultatif)
> [AZURE.NOTE]L’ajout d’une image d’exemple est une étape facultative. Le contenu marketing suffit à remplir les conditions pour l’envoi vers l’environnement intermédiaire.

Sous l’onglet **Sample Images** (Images d’exemple) dans le menu de gauche, cliquez sur **Télécharger une nouvelle image** pour télécharger une image. Si vous avez déjà chargé une image que vous voulez remplacer, cliquez sur **Remplacer l’image**.

![dessin](media/marketplace-publishing-push-to-staging/pubportal-03.png)

### Abonnements
![dessin](media/marketplace-publishing-push-to-staging/pubportal-02.png)

### Informations juridiques
Sous l’onglet Legal (Informations légales), indiquez un lien vers votre politique ou vos conditions d’utilisation. Entrez ou collez les conditions d’utilisation dans la grande zone Conditions d’utilisation.

> [AZURE.IMPORTANT]Tous les champs doivent avoir des entrées, y compris les images, pour que vous puissiez procéder au déploiement dans un environnement intermédiaire.


## Étape 2 : Définir vos tarifs
### Modèles de tarification
|Modèle de tarification |Description |
|---------------|------------------------------------------|
|De base| Forfait mensuel payé au moment de l’achat, par exemple 10 $ par mois|
|Consommation (également appelé Utilisation, Compteur) | Paiement à l’utilisation, définie par l’éditeur de l’offre. Le dépassement ne peut pas être exprimé en nombre d’utilisateurs ou de licences, puisqu’il est impossible d’utiliser une fraction d’utilisateur ou de calculer un prorata. L’utilisation est signalée par le partenaire toutes les heures. Le client paye au terme du cycle de facturation mensuel, et non en avance comme pour les offres mensuelles. |
|Version d’évaluation gratuite | Le client jouit d’une utilisation gratuite pendant une durée limitée, puis paye un tarif normal. |
|Niveau Gratuit | L’offre est toujours gratuite. |
| Migration (également appelé conversion ou mise à niveau/rétrogradation) de l’offre | L’utilisateur change son offre actuelle pour une autre offre autorisée, définie par le partenaire. |

**Modèles de tarification disponibles par type d’offre**

> [AZURE.IMPORTANT]La disponibilité de certains modèles de tarification varie en fonction du type d’offre. Consultez le tableau ci-dessous.

| | De base uniquement | Consommation uniquement | De base + Consommation |
|---|---|---|---|
| Image de machine virtuelle | Non | Oui | Non|
| Service de développement | Oui | Oui | Oui |
| Service de données | Oui | Non | Non |

### 2\.1. Définir les prix de votre machine virtuelle
> [AZURE.NOTE]BYOL est uniquement pris en charge pour les machines virtuelles.

1.	Les marchés pris en charge apparaissent sous l’onglet **Pricing** (Tarifs). Sélectionnez le marché adapté pour ouvrir les champs de tarification.
2.	Le lien fourni sur le portail de publication indiquera les informations de tarification pour vous aider à déterminer les tarifs de vos références SKU.
3.	Si votre référence SKU est BYOL, cochez la case correspondant à « Externally-licensed (BYOL) SKU availability ».
4.	Si votre produit est payable à l’heure, entrez le prix de votre logiciel. Les références SKU sans prix ne pourront pas être achetées ou utilisées.

  >[AZURE.NOTE]Si vous avez des références SKU BYOL et Horaires, veillez à ce que les conditions requises soient remplies pour chacune : case à cocher BYOL et valeurs de prix pour Horaire.

5.	Un Assistant de tarification s’ouvre. Complétez-en les étapes pour indiquer vos tarifs, y compris les tarifs pour les autres pays, si vous choisissez d’autoriser les achats en dehors du marché que vous avez spécifié.
6.	Certains pays sont des pays de remise ISV. Pour vendre dans un pays de remise ISV, vous devez facturer et collecter les taxes sur vos références SKU, puis calculer et payer celles-ci auprès de l’administration fiscale du pays. Microsoft n’est pas en position de fournir des conseils juridiques ou fiscaux. Consultez la section « Sell-to countries of the Offer » sous l’introduction de ce document pour en savoir plus sur les « pays de vente ».

### 2\.2. Définir le prix de vos services de développement
Les offres peuvent constituer une combinaison quelconque de tarifs De base + Consommation, où DE BASE est le tarif mensuel et Dépassement est le tarif à l’utilisation (voir détails ci-dessous)

**Exemple :** offre de services de développement Contoso

| Planification | Prix | Inclus | Chemin de migration |
|-------|------|-------|-------|
|Gratuit|0 $/mois|Fonctionnalités de base|Peut migrer vers une autre offre|
|Bronze|10 $/mois|Fonctionnalités de base et quota de 1 000 pour la fonctionnalité X|Peut migrer vers les offres Bronze Plus, Silver et Gold|
|Bronze Plus|Période d’essai gratuit : 0 $/mois + 0 $/meter01 |Fonctionnalités de base et quota de 10 000 pour la fonctionnalité X. Une fois le quota alloué pour la fonctionnalité X épuisé, le client peut payer à l’utilisation via meter01.|Peut migrer vers les offres Silver Plus et Gold|
|Bronze Plus| Période payée (également appelé Essai gratuit expiré) : 10 $/mois + 0,05 $/meter01|Fonctionnalités de base et quota de 10 000 pour la fonctionnalité X. Une fois le quota alloué pour la fonctionnalité X épuisé, le client peut payer à l’utilisation via meter01.|Peut migrer vers les offres Silver Plus et Gold|
|Silver|0,15 $/meter01|Le client peut payer à l’utilisation via meter01, qui concerne la fonctionnalité X|Peut migrer vers les offres Bronze et Gold|
|Silver Plus|20 $/mois + 0,15 $/meter01 + 0,01 $/meter02|Fonctionnalités de base et quota de 10 000 pour la fonctionnalité X et de 100 pour la fonctionnalité Y. Une fois le quota alloué pour la fonctionnalité X épuisé, le client peut payer à l’utilisation via meter01. Une fois le quota alloué pour la fonctionnalité Y épuisé, le client peut payer à l’utilisation via meter02.|Peut migrer vers les offres Bronze Plus et Gold|
|Gold|1 000 $/mois|Quota de 10 000 pour la fonctionnalité X, 1 000 pour la fonctionnalité Y, et illimité pour la fonctionnalité Z|Permet de migrer vers toutes les offres sauf Gratuit|

## Étape 3 : Fournir les informations de support
Certaines de ces informations auront été complétées pendant l’étape de certification. Vous pouvez ajouter ou modifier des informations comme dans les étapes ci-dessous. Les coordonnées sont utilisées pour les communications internes entre les partenaires et Microsoft uniquement. L’URL du support technique sera disponible pour les clients finaux.

1.	Accédez à la rubrique Support dans la partie gauche du portail de publication.
2.	Entrez des informations sous Engineering Contact (Contact ingénierie).
3.	Entrez des informations sous Customer Support (Support client). Si vous assurez un support par courrier électronique uniquement, entrez un numéro de téléphone factice. L’adresse de messagerie que vous avez fournie sera utilisée à la place.
4.	Entrez l’URL du support technique.

## Étape 4 : Choisir les catégories Azure Marketplace
Dans l’onglet **Categories**, diverses sélections sont proposées. Une ou plusieurs d’entre elles peuvent correspondre à votre offre. Vous pouvez sélectionner cinq (5) catégories au maximum.

## Présentation de votre contenu marketing
Voici une vue détaillée de la présentation du contenu marketing d’une offre du portail de publication sur le [site web Azure Marketplace](http://azure.microsoft.com/marketplace) et la [galerie Marketplace du portail Azure en version préliminaire](https://ms.portal.azure.com).

### Sur le site web Marketplace Azure.com
![dessin](media/marketplace-publishing-push-to-staging/acom-catalog-01.png)

![dessin](media/marketplace-publishing-push-to-staging/acom-catalog-02.png)

*Liste des offres sur le site web Marketplace Azure.com*

![dessin](media/marketplace-publishing-push-to-staging/acom-listing-details-01.png)

*Description d’une offre sur le site web Marketplace Azure.com*

![dessin](media/marketplace-publishing-push-to-staging/acom-listing-details-02.png)

*Tarification d’une offre sur le site web Marketplace Azure.com*

### Dans la galerie Marketplace du portail Azure en version préliminaire
![dessin](media/marketplace-publishing-push-to-staging/portal-catalog-01.png)

*Liste des offres dans la galerie Marketplace du portail Azure en version préliminaire*

![dessin](media/marketplace-publishing-push-to-staging/portal-listing-details-01.png)

![dessin](media/marketplace-publishing-push-to-staging/portal-listing-details-01.png)

*Description d’une offre dans la galerie Marketplace du portail Azure en version préliminaire*

## Étapes suivantes
Une fois votre contenu Marketplace chargé, nous allons passé à l’**étape 3 : Tester votre offre dans l’environnement intermédiaire**. Vous devez toutefois sélectionner le type d’offre approprié dans la liste ci-dessous, car les étapes diffèrent en fonction du type d’offre.

||Image de machine virtuelle |Service de développement | Service de données | Modèle de solution | |----|----|----|----|----| | **Étape 3. Envoyer votre offre dans l’environnement intermédiaire** | [Tester votre offre de machine virtuelle dans l’environnement intermédiaire](marketplace-publishing-vm-image-test-in-staging.md) | Tester votre offre de service de développement dans l’environnement intermédiaire | Tester votre offre de service de données dans l’environnement intermédiaire |[ Tester votre modèle de solution dans l’environnement intermédiaire](marketplace-publishing-solution-template-test-in-staging.md) |

## Voir aussi
- [Mise en route : publication d’une offre sur Azure Marketplace](marketplace-publishing-getting-started.md)

[img-map-acom]: media/marketplace-publishing-push-to-staging/pubportal-mapping-acom.jpg
[img-map-portal]: media/marketplace-publishing-push-to-staging/pubportal-mapping-azure-portal.jpg
[img-map-link]: media/marketplace-publishing-push-to-staging/marketing-content-guide-links.jpg
[img-map-logo]: media/marketplace-publishing-push-to-staging/marketing-content-guide-logos.jpg
[img-map-title]: media/marketplace-publishing-push-to-staging/marketing-content-guide-publisher-offer.png

[link-pubportal]: https://publish.windowsazure.com
[link-push-to-production]: marketplace-publishing-push-to-production.md

<!---HONumber=Oct15_HO3-->