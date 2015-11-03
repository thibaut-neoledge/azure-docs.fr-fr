<properties
   pageTitle="Processus de création et d’enregistrement d’un compte de publication | Microsoft Azure"
   description="Instructions relatives à la création d’un compte vendeur Microsoft permettant la mise en vente de diverses offres sur Azure Marketplace dès l’approbation du compte."
   services="Azure Marketplace"
   documentationCenter=""
   authors="HannibalSII"
   manager=""
   editor=""/>

<tags
   ms.service="marketplace-publishing"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/09/2015"
   ms.author="hascipio"/>

# Création d’un compte de vendeur Microsoft
Cet article décrit la procédure de création de compte et d’enregistrement que vous devez suivre pour devenir vendeur Microsoft agréé sur Azure Marketplace.

## 1. Créer un compte Microsoft (MSA)
> [AZURE.WARNING] Pour finaliser le processus de publication, vous devez créer un compte Microsoft. Celui-ci vous permettra de vous enregistrer et de vous connecter au Portail de publication et au Tableau de bord du vendeur. Vous ne devez utiliser qu’un compte Microsoft pour vos offres Azure Marketplace. Celui-ci ne doit pas être propre à des services ou offres spécifiques.

L’adresse correspondant au nom d’utilisateur doit figurer dans votre domaine et être contrôlée par votre équipe informatique (par exemple, publishing@yourcompany.com). Les paiements, informations fiscales et rapports seront envoyés via ce compte.

  >[AZURE.WARNING] Les termes tels qu’« Azure » ou « Microsoft » ne sont pas pris en charge pour l’enregistrement d’un compte MSA. Évitez de les utiliser si vous souhaitez finaliser le processus de création du compte et d’enregistrement.

1. Créez une liste de distribution (DL) ou un groupe de sécurité (SG) dans le domaine de votre société.
  - Ajoutez votre équipe d’intégration à la liste de distribution
  - La liste de distribution doit être active pour recevoir le message de confirmation
  - L’opération doit être effectuée dans vos systèmes internes. Par exemple : marketplace@yourcompany.com
2. Ouvrez une nouvelle session de navigation Chrome Incognito ou Internet Explorer InPrivate pour vous assurer que vous n’êtes pas connecté à un compte existant
3. Inscrivez-vous pour créer votre compte Microsoft (MSA) à l’aide de l’adresse de messagerie de la liste de distribution.
 - Vous pouvez vous inscrire pour créer un compte MSA sur la page [https://signup.live.com/signup.aspx](https://signup.live.com/signup.aspx)
 - Utilisez marketplace@yourcompany.com comme adresse de messagerie
 - Votre ID de compte MSA est maintenant marketplace@yourcompany.com

    ![dessin][img-msalive]

4. Veillez à utiliser un numéro de téléphone valide lors de l’inscription. Le système vous enverra un code de vérification par SMS ou appel automatique s’il est nécessaire de vérifier votre identité.
5. Vérifiez l’adresse e-mail envoyée à la liste de distribution

    ![dessin][img-email]

6. Vous êtes maintenant prêt à utiliser votre nouveau compte MSA dans le Tableau de bord du vendeur.

> [AZURE.IMPORTANT] L’utilisation de la liste de distribution permet à plusieurs utilisateurs de recevoir des notifications par e-mail signalant des informations de paiement importantes. Elle permet également d’éviter que la propriété du compte MSA ne soit transférée et qu’elle soit liée à un seul individu.

## 2. Créer votre compte Tableau de bord du vendeur
Le Tableau de bord du vendeur Microsoft est utilisé pour enregistrer les informations de la société en une seule opération. Pour pouvoir vous inscrire, vous devez agir en tant que représentant agréé de l’entreprise ; vous aurez à fournir des informations personnelles afin de valider votre identité. Vous devrez également utiliser un compte Microsoft (MSA) partagé pour l’ensemble de l’entreprise et utiliser le même compte dans le Portail de publication Azure. Vérifiez que votre entreprise ne possède pas déjà de compte Tableau de bord du vendeur avant d’essayer d’en créer un. Dans le cadre de ce processus, nous recueillons vos informations bancaires et fiscales, ainsi que l’adresse de votre entreprise. Vous pouvez généralement obtenir ces informations auprès du service financier ou commercial.

> [AZURE.IMPORTANT] Les éléments de profil vendeur suivants doivent être renseignés afin de progresser dans les différentes phases de création et de déploiement d’offres.


| Profil du vendeur | Pour démarrer le projet | Staging | Publier un produit gratuit et un modèle de solution | Publier un produit commercial |
|----|----|----|----|----|
|Enregistrement de l’entreprise | Obligatoire | Obligatoire | Obligatoire | Obligatoire |
|Numéro fiscal | Facultatif | Facultatif | Facultatif | Obligatoire |
|Compte bancaire | Facultatif | Facultatif | Facultatif | Obligatoire |


> [AZURE.NOTE] Le modèle BYOL est uniquement pris en charge pour les machines virtuelles et est considéré comme une offre **GRATUITE**.


### Enregistrer votre compte de société
1. Ouvrez une nouvelle session de navigation IE InPrivate ou Chrome Incognito pour vous assurer que vous n’êtes pas connecté à un compte personnel

2. Rendez-vous sur [http://sellerdashboard.microsoft.com](http://sellerdashboard.microsoft.com)

    ![dessin][img-sd-url]

3. Connectez-vous avec votre compte Microsoft (MSA) utilisé pour l’enregistrement de la société (par exemple, AzureStore@yourcompany.com)

    ![dessin][img-signin]

4. Suivez les étapes de l’Assistant Aidez-nous à protéger votre compte. Celui-ci vérifie votre identité via un numéro de téléphone ou une adresse de messagerie.

    ![dessin][img-verify]

5. Accédez à **Détails du compte**. Dans cet écran, entrez vos informations personnelles (nom, adresse de messagerie, adresse postale et numéro de téléphone personnel). Celles-ci permettent de vérifier votre identité. Elles ne sont pas communiquées à des personnes extérieures à Microsoft.

    ![dessin][img-sd-top]

    ![dessin][img-sd-info]

6. Inscrivez-vous au nom de votre société en choisissant un type de compte société, et non pas un compte individuel. Cliquez sur **Next**.

    ![dessin][img-sd-type]

7. Renseignez les informations relatives à l’entreprise. Toutes ces informations (siège social, référence) doivent être précises car elles seront vérifiées par une équipe de Microsoft.

    ![dessin][img-sd-mktg1]

8. Le nom de la société renseigné dans les informations ci-dessous est utilisé par le portail de publication et donc être précis

    ![dessin][img-sd-mktg2]

9. Utilisez l’adresse du siège social de votre société

    ![dessin][img-sd-addr]

10. Utilisez une référence qui sera accessible et assimilée à un représentant de la société

    ![dessin][img-sd-legal]

11. Cliquez sur **Soumettre pour approbation**. Vous avez maintenant terminé.

    ![dessin][img-sd-submit]


Si vous prévoyez de publier uniquement des offres gratuites, vous pouvez passer à la section 3, **Enregistrer votre compte dans le Portail de publication.**

Si vous envisagez de publier des offres commerciales, vous devez renseigner les informations fiscales et bancaires de votre compte du Tableau de bord vendeur. Consultez la section 2.2.

> [AZURE.IMPORTANT]Pour tester correctement vos offres en environnement intermédiaire et être en mesure de les mettre en production, vous devez impérativement renseigner les informations fiscales et bancaires.

Si vous préférez mettre à jour vos données bancaires et fiscales ultérieurement, vous pouvez passer directement à l’étape 3. **Enregistrer votre compte dans le Portail de publication** et revenir ultérieurement à cette étape à l’aide des liens disponibles dans le Portail de publication.

### Ajouter des informations fiscales et bancaires
 Si vous souhaitez mettre en vente des offres commerciales, vous devez également ajouter vos informations de paiement et vos données fiscales, puis les soumettre pour validation dans le Tableau de bord vendeur. Si vous souhaitez publier uniquement des offres gratuites (ou offres BYOL), vous n’aurez pas lieu d’ajouter ces informations. Vous pourrez les ajouter ultérieurement, mais sachez que la validation des informations fiscales peut prendre un certain temps. Si vous savez que vous allez proposer des offres commerciales à l’achat, nous vous recommandons d’ajouter ces informations dès que possible.

1. Connectez-vous au [Tableau de bord vendeur](http://sellerdashboard.microsoft.com) avec votre compte Microsoft.

2. Cliquez sur l’onglet **COMPTE**, puis cliquez sur Dividendes et taxes.

3. Cliquez sur **AJOUTER LES INFORMATIONS DE REVENUS ET DE TAXES**.

4. Sur la page **Choisir un moyen de paiement**, sous Nouveau moyen de paiement, cliquez sur **compte bancaire** ou **PayPal**.

> [AZURE.IMPORTANT]Si des clients achètent vos offres commerciales sur le Marketplace, il s’agit du compte qui sera crédité du montant de ces achats.

5. Entrez les coordonnées de votre compte bancaire ou de votre compte PayPal.

6. Cliquez sur **SUIVANT**.

7. Sur la page **Informations de taxes**, sélectionnez le pays ou la région où vous avez une résidence permanente, puis sélectionnez le pays ou la région dont vous êtes ressortissant, puis cliquez sur SUIVANT.

8. Saisissez vos informations fiscales, puis cliquez sur **SUIVANT**.

9. Cliquez sur **Envoyer**. Si vous n’êtes pas prêt à soumettre vos informations fiscales pour validation, vous pouvez cliquer sur **Enregistrer** ou **Enregistrer et quitter**. La validation des informations fiscales prend un certain temps, aussi nous vous recommandons de les soumettre pour validation dès que possible.

> [AZURE.WARNING]Vous ne pourrez pas mettre en production vos offres commerciales si vous n’avez pas renseigné vos informations fiscales et bancaires dans votre compte du tableau de bord vendeur.

## 3. Enregistrer votre compte dans le Portail de publication
Le Portail de publication Azure permet de publier et gérer vos offres. Il contient des informations utiles qui vous guideront tout au long de la création de votre offre.

> [AZURE.WARNING]Vous devez utiliser ici le même compte société Microsoft que celui qui a servi à vous enregistrer dans le tableau de bord vendeur. Il est possible d’ajouter des utilisateurs supplémentaires une fois que le compte de l’éditeur principal a été créé.

1.	Ouvrez une nouvelle session de navigation Chrome Incognito ou IE InPrivate pour vous assurer que vous n’êtes pas connecté à un compte personnel
2.	Accédez à [http://publish.windowsazure.com](http://publish.windowsazure.com)
3.	 Connectez-vous avec votre compte Microsoft (MSA) utilisé pour l’enregistrement de la société (c’est-à-dire AZStore@yourcompany.com) et ajoutez des co-administrateurs si vous le souhaitez.
4.	Lisez et acceptez les conditions du contrat de publication (lors de la première connexion au Portail de publication).

  >[AZURE.TIP] Les règles de participation sont mentionnées [ici](http://azure.microsoft.com/support/legal/marketplace/participation-policies/).

  > Si vous rencontrez des problèmes pendant l’enregistrement du vendeur, ouvrez un ticket de support comme indiqué ci-dessous :
  1. Contactez le [support technique](http://go.microsoft.com/fwlink?LinkId=272975)
  2. Choisissez **Seller Dashboard registration and your account**
  3. Choisissez **Registering for a developer account**
  4. Choisissez une **méthode de contact**

### Pays à partir duquel vous vendez

> [AZURE.WARNING]
Avant de pouvoir vendre vos services sur Microsoft Azure Marketplace, l’entité enregistrée doit résider dans l’un des pays autorisés. Cette restriction s’applique pour des raisons de paiements et de taxes. Nous cherchons activement à étendre cette liste de pays dans un avenir proche, donc restez connecté. Pour obtenir la liste complète, reportez-vous à la section 1 b du document au lien [http://go.microsoft.com/fwlink/?LinkID=526833](http://go.microsoft.com/fwlink/?LinkID=526833).




## Étapes suivantes
Maintenant que votre compte est créé et enregistré, cliquez sur le type d’artefact (machine virtuelle, service de développeur, service de données, modèle de solution) que vous souhaitez publier sur Azure Marketplace. Visitez l’un des articles suivants pour apprendre à publier votre offre :

|| Image de machine virtuelle | Service de développeur | Service de données | Modèle de solution |
|----|-----|-----|-----|-----|
|**Étape 2 : Créer votre offre** |[Conditions générales requises à caractère non technique](marketplace-publishing-pre-requisites.md)| [Conditions générales requises à caractère non technique](marketplace-publishing-pre-requisites.md)| [Conditions générales requises à caractère non technique](marketplace-publishing-pre-requisites.md)| [Conditions générales requises à caractère non technique](marketplace-publishing-pre-requisites.md)|
|| [Conditions techniques requises pour les images de machine virtuelle][link-single-vm-prereq] | Conditions techniques requises pour les services de développeur | Conditions techniques requises pour les services de données | [Conditions techniques requises pour les modèles de solution](marketplace-publishing-solution-template-creation-prerequisites.md) |
|| [Guide de publication d’images de machine virtuelle][link-single-vm] | Guide de publication de services de développeur | Guide de publication de services de données | [Guide de publication de modèles de solution](marketplace-publishing-solution-template-creation.md) |
|| [Guide de contenu marketing Azure Marketplace][link-pushstaging] | [Guide de contenu marketing Azure Marketplace][link-pushstaging] | [Guide de contenu marketing Azure Marketplace][link-pushstaging] | [Guide de contenu marketing Azure Marketplace][link-pushstaging] |

## Voir aussi
- [Mise en route : publication d’une offre sur Azure Marketplace](marketplace-publishing-getting-started.md)

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

<!----HONumber=Oct15_HO3-->