---
title: "Activer HTTPS sur un domaine personnalisé Azure CDN | Microsoft Docs"
description: "Découvrez comment activer HTTPS sur votre point de terminaison Azure CDN avec un domaine personnalisé."
services: cdn
documentationcenter: 
author: camsoper
manager: erikre
editor: 
ms.assetid: 10337468-7015-4598-9586-0b66591d939b
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/03/2017
ms.author: casoper
translationtype: Human Translation
ms.sourcegitcommit: 2f03ba60d81e97c7da9a9fe61ecd419096248763
ms.openlocfilehash: b334ba6bbec1d0a7e23a514174bffae01c7fff05
ms.lasthandoff: 03/04/2017


---
# <a name="enable-https-on-an-azure-cdn-custom-domain"></a>Activer HTTPS sur un domaine personnalisé Azure CDN

[!INCLUDE [cdn-verizon-only](../../includes/cdn-verizon-only.md)]

La prise en charge de HTTPS pour les domaines personnalisés Azure CDN vous permet de diffuser du contenu sécurisé via SSL à l’aide de votre propre nom de domaine afin d’améliorer la sécurité des données lors de leur transit. Le workflow de bout en bout visant à activer HTTPS pour votre domaine personnalisé est simplifié via l’activation en un clic et la gestion complète des certificats, le tout sans coût supplémentaire.

Il est essentiel de garantir la confidentialité et l’intégrité de toutes les données sensibles de vos applications web lors de leur transit. L’utilisation du protocole HTTPS garantit que vos données sensibles sont chiffrées lorsqu’elles sont envoyées sur Internet. Il assure la confiance et l’authentification, mais protège également vos applications web contre les attaques. Actuellement, Azure CDN prend en charge HTTPS sur un point de terminaison CDN. Ainsi, si vous créez un point de terminaison CDN à partir d’Azure CDN (par exemple, https://contoso.azureedge.net), HTTPS est activé par défaut. Désormais, avec HTTPS sur votre domaine personnalisé, vous pouvez également activer la fourniture sécurisée pour un domaine personnalisé (par exemple, https://www.contoso.com). 

Voici quelques-uns des attributs clés de la fonctionnalité HTTPS :

- Aucun coût supplémentaire : l’acquisition ou le renouvellement de certificat n’entraîne aucun coût supplémentaire, tout comme le trafic HTTPS. Vous payez uniquement pour l’acheminement des Go du CDN.

- Activation simple : l’approvisionnement en un clic est disponible à partir du [portail Azure](https://portal.azure.com). Vous pouvez également utiliser l’API REST ou d’autres outils de développeur pour activer la fonctionnalité.

- Gestion complète de certificats : l’approvisionnement et la gestion de tous les certificats sont assurés pour vous. Les certificats sont automatiquement approvisionnés et renouvelés avant l’expiration. Cela élimine tout risque d’interruption de service suite à une expiration du certificat.

>[!NOTE] 
>Avant d’activer la prise en charge du protocole HTTPS, vous devez avoir déjà établi un [domaine personnalisé Azure CDN](./cdn-map-content-to-custom-domain.md).

## <a name="step-1-enabling-the-feature"></a>Étape 1 : Activation de la fonctionnalité 

1. Dans le [portail Azure](https://portal.azure.com), accédez à votre profil CDN Verizon Standard ou Premium.

2. Dans la liste des points de terminaison, cliquez sur celui contenant votre domaine personnalisé.

3. Cliquez sur le domaine personnalisé pour lequel vous souhaitez activer le protocole HTTPS.

    ![Panneau Point de terminaison](./media/cdn-custom-ssl/cdn-custom-domain.png)

4. Cliquez sur **Activé** pour activer HTTPS et enregistrez la modification.

    ![Boîte de dialogue HTTPS personnalisé](./media/cdn-custom-ssl/cdn-enable-custom-ssl.png)


## <a name="step-2-domain-validation"></a>Étape 2 : Validation du domaine

>[!IMPORTANT] 
>Vous devez effectuer la validation de domaine avant que le protocole HTTPS soit activé sur votre domaine personnalisé. Vous disposez de 6 jours ouvrables pour approuver le domaine. La demande sera annulée si aucune approbation n’intervient dans ce délai.  

Une fois le protocole HTTPS activé sur votre domaine personnalisé, notre fournisseur de certificat HTTPS DigiCert validera la propriété de votre domaine en contactant l’inscrit pour ce domaine, en fonction des informations sur l’inscrit WHOIS, par e-mail (par défaut) ou par téléphone. DigiCert enverra également l’e-mail de vérification aux adresses suivantes. Si les informations sur l’inscrit whois sont privées, vérifiez que vous pouvez effectuer directement l’approbation à partir de ces adresses.

>admin@<votre-nom-de-domaine.com> administrator@<votre-nom-de-domaine.com>  
>webmaster@<votre-nom-de-domaine.com>  
>hostmaster@<votre-nom-de-domaine.com>  
>postmaster@<votre-nom-de-domaine.com>


Lors de la réception de l’e-mail, vous avez deux options de vérification :

1. Vous pouvez approuver toutes les commandes futures passées via le même compte pour le même domaine racine, par exemple, consoto.com. Il s’agit de l’approche recommandée si vous envisagez d’ajouter d’autres domaines personnalisés à l’avenir pour le même domaine racine.
 
2. Vous pouvez approuver simplement le nom d’hôte spécifique utilisé dans cette demande. L’approbation supplémentaire sera nécessaire pour les demandes suivantes.

    Exemple d’e-mail :
    
    ![Boîte de dialogue HTTPS personnalisé](./media/cdn-custom-ssl/domain-validation-email-example.png)

Après l’approbation, DigiCert ajoutera votre nom de domaine personnalisé au certificat SAN. Le certificat est valide pendant un an et sera automatiquement renouvelé avant son expiration.

## <a name="step-3-wait-for-the-propagation-then-start-using-your-feature"></a>Étape 3 : Attente de la propagation et utilisation de votre fonctionnalité

Une fois que le nom de domaine est validé, cela prendra jusqu’à 6 à 8 heures pour activer la fonctionnalité HTTPS de domaine personnalisé. Une fois le processus terminé, l’état « HTTPS personnalisé » dans le portail Azure sera défini sur « Activé ». Le protocole HTTPS avec votre domaine personnalisé est maintenant prêt à l’emploi.

## <a name="frequently-asked-questions"></a>Forum Aux Questions

1. *Qui est le fournisseur de certificats et quel est le type de certificat utilisé ?*

    Nous utilisons le certificat SAN (Subject Alternative Names) fourni par DigiCert. Un certificat SAN peut sécuriser plusieurs noms de domaine qualifiés complets avec un certificat.

2. *Puis-je utiliser mon certificat dédié ?*
    
    Pas actuellement, mais nous y travaillons.

3. *Que se passe-t-il si je ne reçois pas l’e-mail de vérification de domaine de DigiCert ?*

    Contactez Microsoft si vous ne recevez pas l’e-mail dans les 24 heures.

4. *Un certificat SAN est-il moins sécurisé qu’un certificat dédié ?*
    
    Un certificat SAN suit les mêmes normes de sécurité et de chiffrement qu’un certificat dédié. Tous les certificats SSL émis utilisent la norme SHA-256 pour une sécurité améliorée du serveur.

5. *Puis-je utiliser le protocole HTTPS de domaine personnalisé avec Azure CDN Akamai ?*

    Actuellement, cette fonctionnalité est uniquement disponible avec Azure CDN Verizon. Nous travaillons pour que la prise en charge de cette fonctionnalité par Azure CDN Akamai soit disponible dans les prochains mois.


## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment configurer un [domaine personnalisé sur votre point de terminaison Azure CDN](./cdn-map-content-to-custom-domain.md)



