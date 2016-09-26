<properties
   pageTitle="Comprendre votre facture | Microsoft Azure"
   description="Apprenez à lire et à comprendre l’utilisation et la facturation de votre abonnement Azure"
   services=""
   documentationCenter=""
   authors="erihur"
   manager="stevenpo"
   editor=""
   tags="billing"/>

<tags
   ms.service="billing"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/13/2016"
   ms.author="erihur;genli"/>


# Comprendre votre facture Microsoft Azure

> [AZURE.NOTE] Si à un moment donné dans cet article, vous avez besoin d’aide, veuillez [contacter le support technique](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pour obtenir une prise en charge rapide de votre problème.

Le coût des abonnements Microsoft Azure varie selon la formule tarifaire. Certaines formules tarifaires, telles que Visual Studio Enterprise (MPN), incluent des crédits mensuels que vous pouvez utiliser avec les services Azure selon vos besoins.

Notez qu’il est possible de reporter jusqu’à 24 heures d’utilisation latente de votre dernière période de facturation sur votre période de facturation en cours.

Pour plus d’informations sur la consommation et les formules tarifaires, consultez la [page Options d’achat de Microsoft Azure](https://azure.microsoft.com/pricing/purchase-options/).

<!-- The below links cover a complete list of all Microsoft Azure services.

<!-- - [Service Details list (csv1)](https://azurepricing.blob.core.windows.net/supplemental/MOSPServices_csv1.xlsx)
<!-- - [Service Details list (csv2)](https://azurepricing.blob.core.windows.net/supplemental/MOSPServices_csv2.xlsx)

<!-- *NOTE: The **csv1** link refers to the column header names for csv version 1 and **csv2** link refers to the new column header names for csv version 2.  These files are updated monthly.*-->

### Afficher ou télécharger une facture Microsoft Azure :

1. Connectez-vous au [Centre des comptes](https://account.windowsazure.com/subscriptions) à l’aide de votre ID de compte Microsoft ou de compte d’entreprise.

2. Cliquez sur l’abonnement dont vous souhaitez afficher les détails et l’utilisation.

3. Cliquez sur **Historique de facturation**.

    ![Résumé - historique de facturation - 1](./media/billing-understand-your-bill/ContentViewaBillforMA1.png)

4. L’**Historique de facturation** affiche les relevés des périodes de facturation précédentes, plus la période non facturée en cours. Le relevé de la période en cours est une estimation de vos frais à la date de génération de l'estimation. Cette information est actualisée tous les jours uniquement et peut ne pas inclure l’ensemble de l’utilisation engagée à ce jour. Votre facture mensuelle peut différer de l'estimation.

    ![Résumé - historique de facturation - 2](./media/billing-understand-your-bill/ContentViewaBillforMA2.png)

5. Cliquez sur **Afficher la déclaration actuelle** pour afficher une estimation de vos frais au moment où l'estimation a été générée. Cette information est actualisée tous les jours uniquement et peut ne pas inclure l’ensemble de l’utilisation engagée à ce jour. Votre facture mensuelle peut différer de l'estimation.

    ![Résumé - historique de facturation - 3](./media/billing-understand-your-bill/ContentViewaBillforMA3.png)

    ![Résumé - historique de facturation - 4](./media/billing-understand-your-bill/ContentViewaBillforMA4.png)

6. Cliquez sur **Télécharger la facture** pour afficher une copie de votre facture précédente.

    ![Résumé - historique de facturation - 5](./media/billing-understand-your-bill/ContentViewaBillforMA5.png)


> [AZURE.NOTE] Les frais répertoriés sur les relevés de facturation pour les clients internationaux sont uniquement fournis à titre d’estimation, les banques appliquant des coûts différents selon les taux de conversion.

Voici quelques exemples de relevés concernant deux offres distinctes disponibles sur Microsoft Azure.

 Type d’offre | Description | Télécharger |
 :--------- |:-------- | :-------|
Pay-As-You-Go | Paiement mensuel des arriérés | [Exemple de fichier](https://azurepricing.blob.core.windows.net/sampleinvoices/Microsoft_Azure_ccinvoice_Sample.pdf)
Offre d’engagement | Dépense déduite de votre engagement prépayé | [Exemple de fichier](https://azurepricing.blob.core.windows.net/sampleinvoices/Microsoft_Azure_invoice_Sample.pdf)

## Informations sur le compte

La section Informations sur le compte identifie les informations pertinentes relatives à votre utilisation et à votre profil.

![en-tête](./media/billing-understand-your-bill/Header.png)

| Terme | Description |
|---------------------|-----------------------------------------------------------------------------------------------------|
| Numéro de facture | Identificateur unique de facture à des fins de suivi |
| Cycle de facturation | Durée pendant laquelle l’utilisation a eu lieu. |
| Date de facture | Date à laquelle la facture a été générée. |
| Mode de paiement | Type de paiement utilisé sur le compte (facture ou carte de crédit). |
| Facturer à | Adresse des paiements Microsoft Azure. |
| Offre d’abonnement | Type d’offre d’abonnement achetée (par exemple, paiement à l’utilisation, BizSpark Plus, Pass Azure, etc.). |
| E-mail du propriétaire du compte | Adresse de messagerie du compte sous lequel le compte Microsoft Azure est enregistré. |

## Comprendre le résumé de la facture

La section **Résumé de la facture** récapitule les transactions intervenues depuis votre dernière facture et vos coûts d’utilisation actuels.

![résumé de la facture](./media/billing-understand-your-bill/InvoiceSummary.png)

La section Solde précédent, paiements et solde restant dû de la facture récapitule les transactions intervenues depuis votre dernière facture.

| Terme | Description |
|---------------------------------------------------|------------------------------------------------------------------------------------------|
| Solde précédent | Montant total dû figurant sur votre dernière facture |
| Paiements | Total des paiements appliqué à votre dernière facture |
| Solde dû (depuis le cycle de facturation précédent) | Tout ajustement de facturation (crédits ou soldes) appliqué à votre compte depuis votre dernière facture |

## Comprendre les frais actuels
La section Frais actuels de la facture contient les détails de vos frais mensuels. Les liens sont organisés selon les sous-sections suivantes.

| Terme | Description |
|---------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Frais d'utilisation | Les frais d’utilisation correspondent au total des frais mensuels d’un abonnement. Vous êtes facturé pour les arriérés de paiement relatifs à votre utilisation du mois passé. |
| Remises | Les remises sur les services appliquées à votre facture actuelle doivent apparaître sur cet élément de ligne. |
| Ajustements | Les ajustements divers correspondent aux crédits divers ou aux frais dus appliqués à votre facture actuelle. Par exemple, si vous disposez de l’offre Visual Studio Enterprise avec MSDN, un crédit mensuel apparaît sur cette ligne. Si vous annulez votre abonnement, les frais d’utilisation mensuelle supérieurs au crédit mensuel inclus dans votre offre à partir du début de la période de facturation actuelle jusqu’à la date d’annulation de votre abonnement s’affichent.|

## Informations de pied de page
![pied de page](./media/billing-understand-your-bill/footerinformation.png)

## Comprendre les informations supplémentaires
La page des informations supplémentaires fournit des références à d’autres ressources pour comprendre votre facture, ainsi que des liens pour afficher votre utilisation et d’autres informations pertinentes relatives à votre facture.

![informations supplémentaires](./media/billing-understand-your-bill/AdditionalInformation.png)

### Utilisation détaillée
Le lien de la description qui figure sous **Utilisation détaillée** vous dirige vers le Centre des comptes, où vous pouvez afficher votre utilisation détaillée pour cet abonnement. Deux versions sont téléchargeables à présent : le fichier **.csv version 1** contient les champs d’utilisation appliquant l’ancienne convention d’affectation de noms, tandis que le fichier **.csv version 2** contient les noms conviviaux de chacune des catégories, ainsi que des champs supplémentaires qui vous aideront à comprendre les services que vous utilisez sur Microsoft Azure. Notez que, dans le fichier .csv version 1, il n’y a aucune information sur Azure Resource Manager. Vous trouverez des informations sur Azure Resource Manager dans le fichier .csv version 2.

### Informations supplémentaires et ressources utiles
Cette section comporte des liens vers des questions simples concernant les tailles des instances de calcul et les frais des bases de données SQ, ainsi que des liens utiles pour vous aider à répondre à d'autres questions.

| Terme | Description |
|----------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| Vendu à | Ce champ est prérempli avec l’adresse du profil du compte. |
| Instructions de paiement | Cette section concerne les instructions de paiement relatives à l’endroit où doivent être envoyés les chèques, aux virements bancaires ou aux contrôles instantanés si votre mode de paiement est la facture. |

## Comprendre les coûts d'utilisation détaillés

Dans le cadre de notre engagement continu visant à faciliter la gestion de l’utilisation d’Azure par les clients, nous avons amélioré le fichier téléchargeable qui présente l’utilisation et les coûts de vos services Azure. Le lien de téléchargement contient deux versions du fichier d’utilisation :

- La **version 1** utilise le format existant

- La **version 2** contient des informations supplémentaires et des noms de colonnes mis à jour dans la section Utilisation quotidienne.

Les frais d’utilisation correspondent au total des frais **mensuels** d’un abonnement, moins les éventuels crédits ou remises. Vous êtes facturé pour les arriérés de paiement relatifs à votre utilisation du mois passé. La section supérieure du fichier présente les détails des services qui vous sont facturés au cours du cycle de facturation du mois précédent. Le tableau ci-dessus répertorie les noms des colonnes pour chacune des versions du fichier .csv.

Version 1 | Version 2 | Description|
:---------------| :---------------- | --------|
Période de facturation | Période de facturation | Période de facturation pendant laquelle la ressource a été consommée.
Nom | Catégorie du compteur | Identifie le service de niveau supérieur dont cette utilisation relève.
Type | Sous-catégorie du compteur | Le service Azure peut aussi être défini par type dans cette colonne, ce qui peut affecter le tarif.
Ressource | Nom du compteur | Identifie l'unité de mesure de la ressource consommée.
Région | Région du compteur | Identifie l’emplacement du centre de données pour certains services dont le prix est basé sur cet emplacement.
SKU | SKU | Identifie l'identificateur système unique de chaque ressource Azure.
Unité | Unité | Identifie l'unité dans laquelle le service est facturé. Par exemple, Go, heures, 10 000 s.
Consommé | Quantité consommée | Contient la quantité de la ressource consommée pendant la période de facturation.
Inclus | Quantité incluse | Contient le montant de la ressource inclus gratuitement dans votre période de facturation actuelle.
Facturable | Quantité de dépassement | Si le montant consommé dépasse le montant inclus, la colonne affiche la différence. Ce montant vous est facturé. Pour les offres de paiement à l’utilisation n’incluant aucun montant, ce total est identique à la quantité consommée.
Au sein de l'engagement | Au sein de l'engagement | Contient les frais de ressource déduits du montant de votre engagement associé à votre offre de 6 ou 12 mois. Vos frais de ressource sont déduits du montant de votre engagement par ordre chronologique.
Devise | Devise | Identifie la devise reflétée dans votre période de facturation actuelle.
Dépassement | Dépassement | Contient les frais de ressource qui excèdent le montant de votre engagement associé à votre offre de 6 ou 12 mois.
Taux d'engagement | Taux d'engagement | Contient le taux d'engagement basé sur le montant total de votre engagement associé à votre offre de 6 ou 12 mois.
Tarif | Tarif | Le tarif affiche le coût qui vous incombe par unité facturable.
Valeur | Valeur | Affiche le résultat de la multiplication de la colonne Facturable par la colonne Tarif. Si la Quantité consommée ne dépasse pas le montant inclus, aucun coût n'apparaît dans cette colonne.

## Analyser les données d'utilisation quotidienne
Selon votre utilisation, il peut y avoir des milliers de lignes de données d'utilisation quotidienne. Si vous souhaitez analyser ces données, cliquez sur **Télécharger l’utilisation**, puis choisissez un fichier de variables séparées par des virgules (.csv) afin de visualiser vos données d’utilisation quotidienne pour la période de facturation appropriée. À titre de référence, vous pouvez télécharger un exemple de fichier .csv pour chacune des versions ci-dessous.

 Name | Télécharger |
 :----------:| :-------: |
 Fichier .csv d’utilisation détaillée version 1| [Exemple de fichier](https://azurepricing.blob.core.windows.net/supplemental/MOSPServices_csv1.xlsx)
 Fichier .csv d’utilisation détaillée version 2 | [Exemple de fichier](https://azurepricing.blob.core.windows.net/supplemental/MOSPServices_csv2.xlsx)



![csv2screenshot](./media/billing-understand-your-bill/csv2screenshot.png)



Dans le fichier .csv, les éléments sont répartis de façon à afficher la liste des quantités consommées pour chaque ressource pendant la période de facturation actuelle.

![csv snapshot](./media/billing-understand-your-bill/csvsnapshotportal.png)

Les colonnes suivantes affichent les détails qui affectent les tarifs au début de la période de facturation :

Version 1 | Version 2 | Description |
:---------------| :----------------| -----|
Date d'utilisation | Date d'utilisation | Date à laquelle la ressource a été émise.
Name | Catégorie du compteur | Identifie le service de niveau supérieur dont cette utilisation relève.
GUID de ressource | ID du compteur | Identificateur du compteur facturé. Cette information sert d’identificateur pour l’établissement du prix de l’utilisation de la facturation.
Type | Sous-catégorie du compteur | Le service Azure peut aussi être défini par type dans cette colonne, ce qui peut affecter le tarif.
Ressource | Nom du compteur | Identifie l'unité de mesure de la ressource consommée.
Région | Région du compteur | Identifie l’emplacement du centre de données pour certains services dont le prix est basé sur cet emplacement.
Unité | Unité | Identifie l'unité dans laquelle le service est facturé. Par exemple, Go, heures, 10 000 s.
Consommé | Quantité consommée | Contient la quantité de la ressource consommée ce jour-là.
Sous-région | Emplacement de la ressource | Identifie le centre de données dans lequel la ressource est en cours d’exécution.
de diffusion en continu | Service consommé | Cette colonne est utilisée pour suivre le service individuel de plateforme Azure qui ne peut pas être spécifiquement identifié dans la colonne Nom. La colonne Service indique à quel service spécifique l’utilisation se rapporte.
N/A | Groupe de ressources | _**Ajout de nouvelle colonne.**_ Groupe de ressources dans lequel la ressource déployée est en cours d’exécution. Voir [Présentation d’Azure Resource Manager](resource-group-overview.md)
Composant | ID de l’instance | Identificateur de la ressource en cours d’exécution. L’identificateur contient le nom que vous avez spécifié pour la ressource lors de sa création.
N/A | Balises | _**Ajout de nouvelle colonne.**_ Nouveaux types de ressource dans Azure qui vous permettent de baliser les ressources. Voir [Organisation des ressources Azure à l’aide de balises](http://azure.microsoft.com/updates/organize-your-azure-resources-with-tags/)
Informations supplémentaires | Informations supplémentaires | Métadonnées supplémentaires associées au service.
Informations sur le service 1 | Informations sur le service 1 | Cette colonne indique le nom du projet auquel le service appartient sur votre abonnement.
Informations sur le service 2 | Informations sur le service 2 | Champ hérité capturant les métadonnées facultatives propres au service.

Outre certains nouveaux champs et changements de nom, la version 2 du fichier .csv inclut une mise en forme standardisée des données dans les champs ci-dessous :

- **ID de l’instance** : ce champ représente l’identificateur spécifié par l’utilisateur pour le service approvisionné. À l’heure actuelle, l’ID de l’instance est représenté sous deux formes : soit le nom de la ressource, soit l’ID complet de la ressource. Les services Microsoft Azure font actuellement l’objet d’une transition en vue de représenter l’ID de l’instance au format standardisé d’ID de ressource complet _**(/subscriptions/<subscription id>/resourcegroups/<resourcegroupname>/providers/<providername>/<resourcename>)**_. Lorsque les services seront passés à ce nouveau format, le champ de données d’ID de l’instance n’indiquera plus le nom de la ressource, mais l’ID de la ressource. L’ID de la ressource correspond au format utilisé par l’[API Azure Resource Manager](https://msdn.microsoft.com/library/azure/dn790567.aspx) pour identifier les ressources dans un abonnement.

![instanceid](./media/billing-understand-your-bill/instanceid.png)

- **Informations supplémentaires** : cette colonne dans le fichier .csv d’utilisation spécifie les métadonnées propres au service. Par exemple, le type d’image d’une machine virtuelle. Actuellement, un service émet des métadonnées propres au service dans plusieurs colonnes : champs Informations supplémentaires, Informations sur le service 1 et Informations sur le service 2. Les services Microsoft Azure vont être standardisés de façon à ne présenter les métadonnées propres au service que dans la colonne Informations supplémentaires. Voir la capture instantanée ci-après du format standardisé :

![additionalinfo\_csv2](./media/billing-understand-your-bill/AdditionaInfo_csv2.png)

- **Balises** : cette colonne contient les balises de ressource spécifiées par l’utilisateur. Les balises peuvent servir à regrouper les enregistrements de facturation. Par exemple, vous pouvez utiliser des balises pour répartir les coûts par département à l’aide du service. Pour plus d’informations, voir l’article décrivant l’[utilisation des balises pour organiser les ressources Azure](./resource-group-using-tags.md). Les services qui prennent en charge l’émission de balises sont les suivants :

    - Machines virtuelles

    - Stockage

    - Services de mise en réseau approvisionnés à l’aide de l’[API Azure Resource Manager](https://msdn.microsoft.com/library/azure/dn790567.aspx)

![tags](./media/billing-understand-your-bill/tags.png)


## Étapes suivantes

- [Définition des alertes de facturation](billing-set-up-alerts.md)

- [Gestion de vos modes de paiement](billing-how-to-change-credit-card.md)

- [Présentation de vos frais Azure Marketplace](billing-understand-your-azure-marketplace-charges.md)

- [FAQ sur la facturation et l’abonnement Azure](billing-subscription-faq.md)

> [AZURE.NOTE] Si vous avez d’autres questions, veuillez [contacter le support technique](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pour obtenir une prise en charge rapide de votre problème.

<!--
OLD MSDN Articles
- [What do I do if my Azure subscription become disabled?](https://msdn.microsoft.com/library/azure/dn736049.aspx)
- [Edit payment information for an existing credit card](https://msdn.microsoft.com/library/azure/dn736053.aspx)
- [Add a new credit card to use as a payment method](https://msdn.microsoft.com/library/azure/dn736057.aspx)
- [Change the credit card on your Microsoft Azure account](https://msdn.microsoft.com/library/azure/dn736050.aspx)
- [Manage your payment method](https://msdn.microsoft.com/library/azure/dn736054.aspx)
-->



<!--Image references-->

<!---HONumber=AcomDC_0914_2016-->