<properties
   pageTitle="Création et enregistrement d'un compte de publication | Microsoft Azure"
   description="Instructions relatives à la création d'un compte vendeur Microsoft permettant la mise en vente de diverses offres sur Azure Marketplace dès l'approbation du compte."
   services="Azure Marketplace"
   documentationCenter=""
   authors="HannibalSII"
   manager=""
   editor=""/>

<tags
   ms.service="marketplace"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/09/2015"
   ms.author="hascipio"/>

# Création d'un compte de vendeur Microsoft
Cet article décrit la procédure de création de compte et d'enregistrement que vous devez suivre pour devenir vendeur Microsoft agréé sur Azure Marketplace.

## 1\. Création d'un compte Microsoft
> [AZURE.WARNING]Pour finaliser le processus de publication, vous devez créer un compte Microsoft. Celui-ci vous permettra de vous enregistrer et de vous connecter au Portail de publication et au Tableau de bord du vendeur. Vous ne devez utiliser qu'un seul compte Microsoft pour vos offres Azure Marketplace. Celui-ci ne doit pas être propre à des services ou offres spécifiques.

L'adresse correspondant au nom d'utilisateur doit figurer dans votre domaine et être contrôlée par votre équipe informatique (par exemple, publishing@example.com)). Les paiements, informations fiscales et rapports seront envoyés via ce compte.

  >[AZURE.WARNING]Les termes tels qu'« Azure » et « Microsoft » ne sont pas pris en charge pour l'enregistrement d'un compte Microsoft. Évitez de les utiliser si vous souhaitez finaliser le processus de création du compte et d’enregistrement.

1. Créez une liste de distribution (DL) ou un groupe de sécurité (SG) dans le domaine de votre société.
  - Ajoutez votre équipe d'intégration à la liste de distribution.
  - La liste de distribution doit être active pour recevoir un message de confirmation.
  - Utilisez marketplace@example.com comme adresse de messagerie pour la liste de distribution.
  - L’opération doit être effectuée dans vos systèmes internes.
2. Ouvrez une nouvelle session de navigation Chrome Incognito ou Internet Explorer InPrivate pour vous assurer que vous n'êtes pas connecté à un compte existant.
3. Inscrivez-vous pour obtenir un compte Microsoft à l'aide de l'adresse de messagerie de la liste de distribution.
 - Vous pouvez vous inscrire pour obtenir un compte Microsoft sur la page [https://signup.live.com/signup.aspx](https://signup.live.com/signup.aspx).
 - Utilisez marketplace@example.com comme adresse de messagerie.
 - Votre ID de compte Microsoft est désormais marketplace@example.com.

    <!--[drawing][img-msalive]-->

4. Veillez à utiliser un numéro de téléphone valide lors de l’inscription. Le système vous enverra un code de vérification par SMS ou appel automatique s'il est nécessaire de vérifier votre identité.
5. Vérifiez l'adresse de messagerie envoyée à la liste de distribution.

    <!--[drawing][img-email]-->

6. Vous êtes maintenant prêt à utiliser votre nouveau compte Microsoft dans le Tableau de bord du vendeur.

> [AZURE.IMPORTANT]L'utilisation de la liste de distribution permet à plusieurs personnes de recevoir des notifications par courrier électronique qui sont importantes pour la communication des informations de paiement. Cela garantit également que la possession du compte Microsoft peut être transférée et n'est pas liée à un seul individu.

## 2\. Créer votre compte Tableau de bord du vendeur
Le Tableau de bord du vendeur Microsoft est utilisé pour enregistrer les informations de la société en une seule opération. Pour pouvoir vous inscrire, vous devez agir en tant que représentant agréé de l’entreprise ; vous aurez à fournir des informations personnelles afin de valider votre identité. Vous devrez également utiliser un compte Microsoft partagé pour l'ensemble de l'entreprise et utiliser le même compte dans le Portail de publication Azure. Vérifiez que votre entreprise ne possède pas déjà de compte Tableau de bord du vendeur avant d'essayer d'en créer un. Dans le cadre de ce processus, nous recueillons vos informations bancaires et fiscales, ainsi que l’adresse de votre entreprise. Vous pouvez généralement obtenir ces informations auprès du service financier ou commercial.

> [AZURE.IMPORTANT]Vous devez renseigner les éléments de profil vendeur suivants afin de progresser dans les différentes phases de création et de déploiement d'offres.


| Profil du vendeur | Pour démarrer le projet | Staging | Publier un produit gratuit et un modèle de solution | Publier un produit commercial |
|----|----|----|----|----|
|Enregistrement de l'entreprise | Obligatoire | Obligatoire | Obligatoire | Obligatoire |
|Numéro fiscal | Facultatif | Facultatif | Facultatif | Obligatoire |
|Compte bancaire | Facultatif | Facultatif | Facultatif | Obligatoire |


> [AZURE.NOTE]Le concept BYOL (avec apport de sa propre licence) est pris en charge uniquement pour les machines virtuelles et est considéré comme une offre **gratuite**.


### Enregistrer votre compte de société
1. Ouvrez une nouvelle session de navigation Chrome Incognito ou Internet Explorer InPrivate pour vous assurer que vous n'êtes pas connecté à un compte personnel.

2. Rendez-vous sur [http://sellerdashboard.microsoft.com](http://sellerdashboard.microsoft.com).

    ![drawing][img-sd-url]

3. Connectez-vous avec le compte Microsoft utilisé pour l'enregistrement de votre société (par exemple, marketplace@example.com)).

    ![dessin][img-signin]

4. Suivez les étapes de l’Assistant Aidez-nous à protéger votre compte. Celui-ci vérifie votre identité via un numéro de téléphone ou une adresse de messagerie.

    ![dessin][img-verify]

5. Accédez à **Détails du compte**. Dans cet écran, entrez vos informations personnelles, qui seront utilisées uniquement pour vérifier votre identité. (nom, adresse de messagerie, adresse postale et numéro de téléphone personnel). Celles-ci permettent de vérifier votre identité. Elles ne sont pas communiquées à des personnes extérieures à Microsoft.

    ![dessin][img-sd-top]

    <!--[drawing][img-sd-info]-->

6. Inscrivez-vous au nom de votre société en choisissant un type de compte **société**, et non pas un compte **individuel**. Cliquez sur **Next**.

    ![dessin][img-sd-type]

7. Renseignez les informations relatives à l'entreprise. Toutes ces informations (siège social, référence) doivent être précises car elles seront vérifiées par une équipe de Microsoft.

    ![dessin][img-sd-mktg1]

8. Le nom de la société est utilisé par le portail de publication, il doit donc être correct.

    <!--[drawing][img-sd-mktg2]-->

9. Utilisez l'adresse du siège social de votre société.

    ![dessin][img-sd-addr]

10. Utilisez une référence qui sera accessible et assimilée à un représentant de la société.

    ![dessin][img-sd-legal]

11. Cliquez sur **Soumettre pour approbation**. Vous avez maintenant terminé.

    ![dessin][img-sd-submit]


Si vous prévoyez de publier uniquement des offres gratuites, vous pouvez passer à la section 3, « Enregistrer votre compte dans le Portail de publication ».

Si vous envisagez de publier des offres commerciales, vous devez renseigner les informations fiscales et bancaires de votre compte du Tableau de bord vendeur.

> [AZURE.IMPORTANT]Pour tester correctement vos offres en environnement intermédiaire et être en mesure de les mettre en production, vous devez impérativement renseigner les informations fiscales et bancaires.

Si vous préférez mettre à jour les informations fiscales et bancaires ultérieurement, vous pouvez accéder à la section 3 « Enregistrer votre compte dans le Portail de publication », puis revenir ici ultérieurement en utilisant les liens dans le portail de publication.

### Ajouter des informations fiscales et bancaires
 Si vous souhaitez mettre en vente des offres commerciales, vous devez également ajouter vos informations de paiement et vos données fiscales, puis les soumettre pour validation dans le Tableau de bord vendeur. Si vous souhaitez publier uniquement des offres gratuites (ou offres BYOL), vous n’aurez pas lieu d’ajouter ces informations. Vous pourrez les ajouter ultérieurement, mais sachez que la validation des informations fiscales peut prendre un certain temps. Si vous savez que vous allez proposer des offres commerciales à l’achat, nous vous recommandons d’ajouter ces informations dès que possible.

1. Connectez-vous au [Tableau de bord du vendeur](http://sellerdashboard.microsoft.com) avec votre compte Microsoft.

2. Cliquez sur l'onglet **COMPTE**, puis cliquez sur **Dividendes et taxes**.

3. Cliquez sur **AJOUTER LES INFORMATIONS DE REVENUS ET DE TAXES**.

4. Sur la page **Choisir un moyen de paiement**, sous **Nouveau moyen de paiement**, cliquez sur **Compte bancaire** ou **PayPal**.

> [AZURE.IMPORTANT]Si des clients achètent vos offres commerciales sur le Marketplace, il s’agit du compte qui sera crédité du montant de ces achats.

5. Entrez les coordonnées de votre compte bancaire ou de votre compte PayPal.

6. Cliquez sur **SUIVANT**.

7. Sur la page **Informations de taxes**, sélectionnez le pays ou la région où vous avez une résidence permanente, puis sélectionnez le pays ou la région dont vous êtes ressortissant. Cliquez sur **SUIVANT**.

8. Saisissez vos informations fiscales, puis cliquez sur **SUIVANT**.

9. Cliquez sur **Envoyer**. Si vous n'êtes pas prêt à soumettre vos informations fiscales pour validation, vous pouvez cliquer sur **Enregistrer** ou **Enregistrer et quitter**. La validation des informations fiscales prend un certain temps, aussi nous vous recommandons de les soumettre pour validation dès que possible.

> [AZURE.WARNING]Vous ne pourrez pas mettre en production vos offres commerciales si vous n’avez pas renseigné vos informations fiscales et bancaires dans votre compte du tableau de bord vendeur.

## 3\. Enregistrer votre compte dans le Portail de publication
Le Portail de publication Azure permet de publier et gérer vos offres. Il contient des informations utiles qui vous guideront tout au long de la création de votre offre.

> [AZURE.WARNING]Vous devez utiliser ici le même compte société Microsoft que celui qui a servi à vous enregistrer dans le Tableau de bord du vendeur. Il est possible d’ajouter des utilisateurs supplémentaires une fois que le compte de l’éditeur principal a été créé.

1.	Ouvrez une nouvelle session de navigation Chrome Incognito ou Internet Explorer InPrivate pour vous assurer que vous n'êtes pas connecté à un compte personnel.
2.	Accédez à [http://publish.windowsazure.com](http://publish.windowsazure.com).
3.	 Connectez-vous avec votre compte Microsoft utilisé pour l'enregistrement de la société (c'est-à-dire marketplace@example.com)) et ajoutez des co-administrateurs si vous le souhaitez.
4.	Lisez et acceptez les conditions du contrat de publication (lors de la première connexion au Portail de publication).

  >[AZURE.TIP]Les politiques concernant la participation sont décrites sur le [site Web Azure](http://azure.microsoft.com/support/legal/marketplace/participation-policies/).

  > Si vous rencontrez des problèmes pendant l'inscription du vendeur, ouvrez un ticket de support comme indiqué ci-dessous : 1. Contactez le [support technique](http://go.microsoft.com/fwlink?LinkId=272975). 2. Choisissez **Inscription au Tableau de bord vendeur et votre compte**. 3. Choisissez **Inscription pour un compte de développeur**. 4. Choisissez une méthode de contact.

### Pays à partir duquel vous vendez

> [AZURE.WARNING]Avant de pouvoir vendre vos services dans Azure Marketplace, l'entité inscrite doit résider dans l'un des pays autorisés. Cette restriction s’applique pour des raisons de revenus et de taxes. Nous cherchons activement à étendre cette liste de pays dans un avenir proche, donc restez connecté. Pour obtenir la liste complète, consultez la Section 1b des [Politiques concernant la participation à Marketplace](http://go.microsoft.com/fwlink/?LinkID=526833).




## Étapes suivantes
Maintenant que votre compte est créé et enregistré, cliquez sur le type d'artefact (machine virtuelle, service de développeur, service de données ou modèle de solution) que vous souhaitez publier sur Azure Marketplace. Visitez l’un des articles suivants pour apprendre à publier votre offre :

|| Image de machine virtuelle | Service de développeur | Service de données | Modèle de solution |
|----|-----|-----|-----|-----|
|**Étape 2 : Créer votre offre** |[Conditions générales requises à caractère non technique](marketplace-publishing-pre-requisites.md)| [Conditions générales requises à caractère non technique](marketplace-publishing-pre-requisites.md)| [Conditions générales requises à caractère non technique](marketplace-publishing-pre-requisites.md)| [Conditions générales requises à caractère non technique](marketplace-publishing-pre-requisites.md)|
|| [Conditions techniques requises pour les images de machine virtuelle][link-single-vm-prereq] | Conditions techniques requises pour les services de développeur | Conditions techniques requises pour les services de données | [Conditions techniques requises pour les modèles de solution](marketplace-publishing-solution-template-creation-prerequisites.md) |
|| [Guide de publication d’images de machine virtuelle][link-single-vm] | Guide de publication de services de développeur | Guide de publication de services de données | [Guide de publication de modèles de solution](marketplace-publishing-solution-template-creation.md) |
|| [Guide de contenu marketing Azure Marketplace][link-pushstaging] | [Guide de contenu marketing Azure Marketplace][link-pushstaging] | [Guide de contenu marketing Azure Marketplace][link-pushstaging] | [Guide de contenu marketing Azure Marketplace][link-pushstaging] |

## Voir aussi
- [Mise en route : publication d'une offre dans Azure Marketplace](marketplace-publishing-getting-started.md)

[img-msalive]: media/marketplace-publishing-accounts-creation-registration/creating-msa-account-msa-live.jpg
[img-email]: media/marketplace-publishing-accounts-creation-registration/creating-msa-account-msa-verifyemail.jpg
[img-sd-url]: media/marketplace-publishing-accounts-creation-registration/seller-dashboard-incognito.jpg
[img-signin]: media/marketplace-publishing-accounts-creation-registration/seller-dashboard-login.jpg
[img-verify]: media/marketplace-publishing-accounts-creation-registration/seller-dashboard-verify.jpg
[img-sd-top]: media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-acc-details.jpg
[img-sd-info]: media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal.jpg
[img-sd-type]: media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-acc-type.jpg
[img-sd-mktg1]: media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-comp-det1.jpg
[img-sd-mktg2]: media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-comp-det2.jpg
[img-sd-addr]: media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-comp-add.jpg
[img-sd-legal]: media/marketplace-publishing-accounts-creation-registration/seller-dashboard-personal-cmp.jpg
[img-sd-submit]: media/marketplace-publishing-accounts-creation-registration/seller-dashboard-approval.jpg

[link-msdndoc]: https://msdn.microsoft.com/fr-FR/library/jj552460.aspx
[link-sellerdashboard]: http://sellerdashboard.microsoft.com/
[link-pubportal]: https://publish.windowsazure.com
[link-single-vm]: marketplace-publishing-vm-image-creation.md
[link-single-vm-prereq]: marketplace-publishing-vm-image-creation-prerequisites.md
[link-multi-vm]: marketplace-publishing-solution-template-creation.md
[link-multi-vm-prereq]: marketplace-publishing-solution-template-creation-prerequisites.md
[link-datasvc]: marketplace-publishing-data-service-creation.md
[link-datasvc-prereq]: marketplace-publishing-data-service-creation-prerequisites.md
[link-devsvc]: marketplace-publishing-dev-service-creation.md
[link-devsvc-prereq]: marketplace-publishing-dev-service-creation-prerequisites.md
[link-pushstaging]: marketplace-publishing-push-to-staging.md

<!---HONumber=Nov15_HO4-->