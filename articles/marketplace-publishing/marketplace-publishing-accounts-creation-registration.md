<properties
   pageTitle="Création et enregistrement d'un compte de publication | Microsoft Azure"
   description="Instructions relatives à la création d’un compte de développeur Microsoft, qui vous permet, dès l’approbation du compte, de mettre en vente différents types d’offres sur Azure Marketplace."
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
   ms.date="01/25/2016"
   ms.author="hascipio"/>

# Créer un compte de développeur Microsoft
Cet article décrit la procédure de création de compte et d’inscription que vous devez suivre pour devenir un développeur Microsoft agréé pour Azure Marketplace.

## 1\. Création d'un compte Microsoft
> [AZURE.WARNING] Pour démarrer le processus de publication, vous devez créer un compte Microsoft. Celui-ci vous permettra de vous enregistrer et de vous connecter au **Centre de développement Microsoft** et au **portail de publication Azure**. Vous ne devez utiliser qu'un seul compte Microsoft pour vos offres Azure Marketplace. Celui-ci ne doit pas être propre à des services ou offres spécifiques.

L’adresse correspondant au nom d’utilisateur doit figurer dans votre domaine et être contrôlée par votre équipe informatique (par exemple, publishing@example.com)). Les paiements, informations fiscales et rapports seront envoyés via ce compte.

  > [AZURE.WARNING] Les termes tels qu'« Azure » et « Microsoft » ne sont pas pris en charge pour l'enregistrement d'un compte Microsoft. Évitez de les utiliser si vous souhaitez finaliser le processus de création du compte et d’enregistrement.

### Instructions

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
4. Veillez à utiliser un numéro de téléphone valide lors de l’inscription. Le système vous enverra un code de vérification par SMS ou appel automatique s'il est nécessaire de vérifier votre identité.
5. Vérifiez l'adresse de messagerie envoyée à la liste de distribution.
6. Vous êtes maintenant prêt à utiliser votre nouveau compte Microsoft dans le Centre de développement Microsoft.

> [AZURE.TIP] L'utilisation de la liste de distribution permet à plusieurs personnes de recevoir des notifications par courrier électronique qui sont importantes pour la communication des informations de paiement. Cela garantit également que la possession du compte Microsoft peut être transférée et n'est pas liée à un seul individu.

## 2\. Création de votre compte Centre de développement Microsoft
Le Centre de développement Microsoft est utilisé pour enregistrer les informations de la société en une seule opération. Pour pouvoir vous inscrire, vous devez agir en tant que représentant agréé de l’entreprise ; vous aurez à fournir des informations personnelles afin de valider votre identité. Vous devrez également utiliser un compte Microsoft partagé pour l’ensemble de l’entreprise et utiliser **le même compte dans le portail de publication Azure**. Vérifiez que votre entreprise ne possède pas déjà de compte Centre de développement Microsoft avant d’essayer d’en créer un. Dans le cadre de ce processus, nous recueillons l’adresse de votre entreprise, ainsi que vos informations bancaires et fiscales. Vous pouvez généralement obtenir ces informations auprès du service financier ou commercial.

> [AZURE.IMPORTANT] Vous devez renseigner les éléments de profil de développeur suivants pour avancer dans les différentes phases de création et de déploiement d’offres.


| Profil de développeur | Pour démarrer le projet | Staging | Publier un produit gratuit et un modèle de solution | Publier un produit commercial |
|----|----|----|----|----|
|Enregistrement de l'entreprise | Obligatoire | Obligatoire | Obligatoire | Obligatoire |
|Numéro fiscal | Facultatif | Facultatif | Facultatif | Obligatoire |
|Compte bancaire | Facultatif | Facultatif | Facultatif | Obligatoire |


> [AZURE.NOTE] Le concept BYOL (avec apport de sa propre licence) est pris en charge uniquement pour les machines virtuelles et est considéré comme une offre **gratuite**.


### Enregistrer votre compte de société
1. Ouvrez une nouvelle session de navigation Chrome Incognito ou Internet Explorer InPrivate pour vous assurer que vous n'êtes pas connecté à un compte personnel.

2. Accédez à [http://dev.windows.com/registration?accountprogram=azure](http://dev.windows.com/registration?accountprogram=azure).

3. Connectez-vous avec le compte Microsoft utilisé pour l’enregistrement de votre société (par exemple, marketplace@example.com)).

    ![drawing][img-signin]

4. Suivez les étapes de l’Assistant Aidez-nous à protéger votre compte. Celui-ci vérifie votre identité via un numéro de téléphone ou une adresse de messagerie.

    ![dessin][img-verify]

5. Dans la section « Inscription - Informations sur le compte », sélectionnez l’option **Pays/région du compte** dans le menu déroulant.

    > [AZURE.WARNING] **Pays à partir duquel vous vendez :** avant de pouvoir vendre vos services dans Azure Marketplace, l’entité inscrite doit résider dans l’un des pays autorisés. Cette restriction s’applique pour des raisons de revenus et de taxes. Nous cherchons activement à étendre cette liste de pays dans un avenir proche, donc restez connecté. Pour plus d’informations, consultez [Politiques concernant la participation à Microsoft Azure Marketplace](http://go.microsoft.com/fwlink/?LinkID=526833).

6. Sélectionnez votre « Type de compte » : **Individuel** ou **Entreprise**.

    > [AZURE.IMPORTANT] Pour mieux comprendre les types de comptes et choisir celui qui vous convient le mieux, consultez la page [Types de compte, emplacements et frais](https://msdn.microsoft.com/library/windows/apps/jj863494.aspx).

7. Entrez le **Nom complet de l’éditeur**, en général le nom de votre société.

    > [AZURE.TIP] Actuellement, le portail de publication Azure n’utilise pas le nom complet de l’éditeur. Toutefois, ce champ doit être complété pour terminer le processus d’inscription.

8. Entrez les coordonnées pour le compte.

    > [AZURE.IMPORTANT] Vous devez fournir des coordonnées précises, car elles seront utilisées dans notre processus de vérification de votre société pour l’approbation dans le Centre de développement.

9. Pour un compte d’entreprise, vous devez également fournir les coordonnées de l’**Approbateur de l’entreprise**, la personne qui peut vérifier que vous êtes autorisé à créer le compte au nom de votre organisation. Lorsque vous avez terminé, cliquez sur **Suivant** pour passer à la **« section Paiement »**.

10. Entrez vos informations de paiement pour le paiement de votre compte. Si vous disposez d’un code promotionnel qui couvre le coût de l’inscription, vous pouvez le saisir ici. Sinon, fournissez vos informations de carte de crédit (PayPal est pris en charge pour certains marchés). Lorsque vous avez terminé, cliquez sur **Suivant** pour passer à la **« section Vérification »**.

11. Vérifiez vos informations de compte et confirmez que tout est correct. Ensuite, lisez et acceptez les conditions du [Contrat d’éditeur Microsoft Azure Marketplace](http://go.microsoft.com/fwlink/?LinkID=699560). Cochez la case pour indiquer vous avez lu et que vous acceptez ces conditions.

12. Cliquez sur **Terminer** pour confirmer votre inscription. Nous enverrons un message de confirmation à votre adresse de messagerie de développeur.

13. Si vous envisagez de publier uniquement des offres gratuites, cliquez sur **Accéder au portail de publication Azure Marketplace**, et passez à la section 3 de ce document, [Enregistrer votre compte dans le Portail de publication](#3-register-your-account-in-the-publishing-portal).

14. Si vous envisagez de publier des offres commerciales, cliquez sur **Mettre à jour vos informations de compte**. Vous devrez alors indiquer vos informations bancaires et fiscales dans votre compte Centre de développement.

> [AZURE.IMPORTANT] Pour tester correctement vos offres en environnement intermédiaire et être en mesure de les mettre en production, vous devez impérativement renseigner les informations fiscales et bancaires.

Si vous préférez mettre à jour les informations fiscales et bancaires ultérieurement, vous pouvez accéder à la section 3 [Enregistrer votre compte dans le Portail de publication](#3-register-your-account-in-the-publishing-portal), puis revenir ici ultérieurement en utilisant les liens dans le portail de publication Azure.

### Ajouter des informations fiscales et bancaires
 Si vous souhaitez mettre en vente des offres commerciales, vous devez également ajouter vos informations de paiement et vos données fiscales, puis les soumettre pour validation dans le Centre de développement. Si vous souhaitez publier uniquement des offres gratuites (ou offres BYOL), vous n’aurez pas lieu d’ajouter ces informations. Vous pourrez les ajouter ultérieurement, mais sachez que la validation des informations fiscales peut prendre un certain temps. Si vous savez que vous allez proposer des offres commerciales à l’achat, nous vous recommandons d’ajouter ces informations dès que possible.

**Informations bancaires**

1. Connectez-vous au [Centre de développement Microsoft](http://dev.windows.com/registration?accountprogram=azure) avec votre compte Microsoft.

2. Cliquez sur **Compte de paiement** dans le menu de gauche, puis sous **Choisir le moyen de paiement**, cliquez sur **Compte bancaire** ou sur **PayPal**.

    > [AZURE.IMPORTANT] Si des clients achètent vos offres commerciales sur le Marketplace, il s’agit du compte qui sera crédité du montant de ces achats.

3. Entrez les informations de paiement, puis cliquez sur **Enregistrer** lorsque vous êtes satisfait.

    > [AZURE.IMPORTANT] Si vous devez mettre à jour ou modifier votre compte de paiement, suivez les mêmes étapes ci-dessus, en remplaçant les informations actuelles par les nouvelles informations. La modification de votre compte de paiement peut retarder vos paiements jusqu’à un cycle de paiement. Ce retard se produit parce que nous devons vérifier le changement de compte, comme nous l’avons fait lorsque vous avez configuré le compte de paiement pour la première fois. Vous serez payé pour le montant total une fois que votre compte aura été vérifié. Tout paiement dû dans le cycle de paiement actuel sera ajouté au cycle suivant

4. Cliquez sur **Suivant**.

**Informations fiscales**

1. Connectez-vous au [Centre de développement Microsoft](http://dev.windows.com/registration?accountprogram=azure) avec votre compte Microsoft (si nécessaire).

2. Cliquez sur **Profil fiscal** dans le menu de gauche.

3. Sur la page **Établir votre déclaration d’impôts**, sélectionnez le pays ou la région où vous avez une résidence permanente, puis sélectionnez le pays ou la région dont vous êtes ressortissant. Cliquez sur **Suivant**.

4. Saisissez vos informations fiscales, puis cliquez sur **Suivant**.

> [AZURE.WARNING] Vous ne pourrez pas mettre en production vos offres commerciales si vous n’avez pas renseigné vos informations fiscales et bancaires dans votre compte Centre de développement Microsoft.

## 3\. Enregistrer votre compte dans le Portail de publication
Le Portail de publication Azure permet de publier et gérer vos offres. Il contient des informations utiles qui vous guideront tout au long de la création de votre offre.

> [AZURE.WARNING] Vous devez utiliser ici le même compte société Microsoft que celui qui a servi à vous enregistrer dans le Centre de développement Microsoft. Il est possible d’ajouter des utilisateurs supplémentaires une fois que le compte de l’éditeur principal a été créé.

1.	Ouvrez une nouvelle session de navigation Chrome Incognito ou Internet Explorer InPrivate pour vous assurer que vous n'êtes pas connecté à un compte personnel.

2.	Accédez à [http://publish.windowsazure.com](http://publish.windowsazure.com).

3.	Connectez-vous avec votre compte Microsoft utilisé pour l’enregistrement de la société (c’est-à-dire marketplace@example.com)) et ajoutez des co-administrateurs si vous le souhaitez.

  > [AZURE.TIP] Les politiques concernant la participation sont décrites sur le [site web Azure](https://azure.microsoft.com/support/legal/marketplace/participation-policies/).

  > Si vous rencontrez des problèmes pendant l’inscription au Centre de développement, ouvrez un ticket de support comme indiqué ci-dessous : 1. Contactez le [support technique](https://support.microsoft.com/getsupport?wf=0&tenant=ClassicCommercial&oaspworkflow=start_1.0.0.0&supportregion=fr-FR&pesid=15635&ccsid=635847950577064286). 2. Choisissez **Centre de développement**. 3. Choisissez **Profil**. 4. Choisissez une méthode de contact.






## Étapes suivantes
Maintenant que votre compte est créé et enregistré, cliquez sur le type d'artefact (machine virtuelle, service de développeur, service de données ou modèle de solution) que vous souhaitez publier sur Azure Marketplace. Visitez l’un des articles suivants pour apprendre à publier votre offre :

| Image de machine virtuelle | Service de développement | Service de données | Modèle de solution |
|----|-----|-----|-----|-----|
|**Étape 2 : Créer votre offre** |[Conditions préalables non techniques générales](marketplace-publishing-pre-requisites.md)| [Conditions préalables non techniques générales](marketplace-publishing-pre-requisites.md)| [Conditions préalables non techniques générales](marketplace-publishing-pre-requisites.md)| [Conditions préalables non techniques générales](marketplace-publishing-pre-requisites.md)|
| [Conditions préalables techniques de l’image de machine virtuelle][link-single-vm-prereq] | Conditions préalables techniques des services de développement | [Conditions préalables techniques des services de données](marketplace-publishing-data-service-creation-prerequisites.md) | [Conditions préalables techniques des modèles de solution](marketplace-publishing-solution-template-creation-prerequisites.md) |
| [Guide de publication de l’image de machine virtuelle][link-single-vm] | Guide de publication de service de développeur | [Guide de publication de service de données](marketplace-publishing-data-service-creation.md) | [Guide de publication de modèles de solution](marketplace-publishing-solution-template-creation.md) |
| [Guide de contenu marketing Azure Marketplace][link-pushstaging] | [Guide de contenu marketing Azure Marketplace][link-pushstaging] | [Guide de contenu marketing Azure Marketplace][link-pushstaging] | [Guide de contenu marketing Azure Marketplace][link-pushstaging] |

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

[link-msdndoc]: https://msdn.microsoft.com/library/jj552460.aspx
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

<!---HONumber=AcomDC_0204_2016-->