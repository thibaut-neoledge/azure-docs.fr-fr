---
title: "Activer ou désactiver HTTPS sur un domaine personnalisé Microsoft Azure CDN (Content Delivery Network) | Microsoft Docs"
description: "Découvrez comment activer ou désactiver HTTPS sur votre point de terminaison Microsoft Azure CDN avec un domaine personnalisé."
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
ms.openlocfilehash: c92f1e20acf55b8bd791fad43f17e162a5cb3847
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="enable-or-disable-https-on-an-azure-content-delivery-network-custom-domain"></a>Activer ou désactiver HTTPS sur un domaine personnalisé Microsoft Azure CDN (Content Delivery Network)

[!INCLUDE [cdn-verizon-only](../../includes/cdn-verizon-only.md)]

La prise en charge de HTTPS pour les domaines personnalisés Microsoft Azure CDN (Content Delivery Network) vous permet de diffuser du contenu sécurisé via SSL et par l’intermédiaire de votre propre nom de domaine, afin d’améliorer la sécurité des données lors de leur transit. Le workflow de bout en bout visant à activer HTTPS pour votre domaine personnalisé est simplifié via l’activation en un clic et la gestion complète des certificats, le tout sans coût supplémentaire.

Il est essentiel de garantir la confidentialité et l’intégrité de toutes les données sensibles de vos applications web lors de leur transit. L’utilisation du protocole HTTPS garantit que vos données sensibles sont chiffrées lorsqu’elles sont envoyées sur Internet. Il assure la confiance et l’authentification, mais protège également vos applications web contre les attaques. Actuellement, Azure CDN prend en charge HTTPS sur un point de terminaison CDN. Ainsi, si vous créez un point de terminaison CDN à partir d’Azure CDN (par exemple, https://contoso.azureedge.net), HTTPS est activé par défaut. Désormais, avec HTTPS sur un domaine personnalisé, vous pouvez également activer la livraison sécurisée pour un domaine personnalisé (par exemple, https://www.contoso.com). 

Voici quelques-uns des attributs clés de la fonctionnalité HTTPS :

- Aucun coût supplémentaire : l’acquisition ou le renouvellement de certificat n’entraîne aucun coût supplémentaire, tout comme le trafic HTTPS. Vous payez uniquement pour l’acheminement des Go du CDN.

- Activation simple : l’approvisionnement en un clic est disponible à partir du [portail Azure](https://portal.azure.com). Vous pouvez également utiliser l’API REST ou d’autres outils de développeur pour activer la fonctionnalité.

- Gestion complète de certificats : l’approvisionnement et la gestion de tous les certificats sont assurés pour vous. Les certificats sont automatiquement approvisionnés et renouvelés avant l’expiration. Cela élimine tout risque d’interruption de service suite à une expiration du certificat.

>[!NOTE] 
>Avant d’activer la prise en charge du protocole HTTPS, vous devez avoir déjà établi un [domaine personnalisé Azure CDN](./cdn-map-content-to-custom-domain.md).

## <a name="enabling-https"></a>Activation de HTTPS

Pour activer HTTPS, suivez ces étapes :

### <a name="step-1-enable-the-feature"></a>Étape 1 : Activation de la fonctionnalité 

1. Dans le [portail Azure](https://portal.azure.com), accédez à votre profil CDN Verizon Standard ou Premium.

2. Dans la liste des points de terminaison, cliquez sur celui contenant votre domaine personnalisé.

3. Cliquez sur le domaine personnalisé pour lequel vous souhaitez activer le protocole HTTPS.

    ![Panneau Point de terminaison](./media/cdn-custom-ssl/cdn-custom-domain.png)

4. Cliquez sur **Activer** pour activer HTTPS, puis sur **Appliquer**.

    ![Boîte de dialogue HTTPS personnalisé](./media/cdn-custom-ssl/cdn-enable-custom-ssl.png)


### <a name="step-2-validate-domain"></a>Étape 2 : Validation du domaine

>[!IMPORTANT] 
>Vous devez effectuer la validation de domaine avant que le protocole HTTPS soit activé sur votre domaine personnalisé. Vous disposez de six jours ouvrables pour approuver le domaine. Les requêtes qui ne sont pas approuvées dans ce délai de six jours ouvrables sont automatiquement annulées. 

Dès que vous avez activé HTTPS sur votre domaine personnalisé, notre fournisseur de certificats HTTPS, DigiCert, valide la propriété de votre domaine en contactant l’inscrit pour votre domaine, en fonction de ses informations [WHOIS](http://whois.domaintools.com/) du domaine. Une prise de contact est établie par le biais de l’adresse de messagerie (par défaut) ou le numéro de téléphone répertorié dans l’inscription WHOIS. 

![Enregistrement WHOIS](./media/cdn-custom-ssl/whois-record.png)

De plus, DigiCert envoie l’e-mail de vérification aux adresses suivantes. Si les informations sur l’inscrit WHOIS sont privées, vérifiez que vous pouvez effectuer directement l’approbation à partir de l’une de ces adresses :

admin@&lt;votre-nom-de-domaine.com&gt;  
administrateur@&lt;votre-nom-de-domaine.com&gt;  
webmaster@&lt;votre-nom-de-domaine.com&gt;  
hostmaster@&lt;votre-nom-de-domaine.com&gt;  
postmaster@&lt;votre-nom-de-domaine.com&gt;  

Au bout de quelques minutes, vous devriez recevoir un e-mail similaire à celui de l’exemple suivant et vous demandant d’approuver la requête. Si vous utilisez un filtre de courrier indésirable, ajoutez admin@digicert.com à sa liste verte. Si vous ne recevez pas d’e-mail dans les 24 heures, contactez le support Microsoft.
    
![Boîte de dialogue HTTPS personnalisé](./media/cdn-custom-ssl/domain-validation-email.png)

Lorsque vous cliquez sur le lien d’approbation, vous êtes dirigé vers le formulaire d’approbation en ligne suivant : 
    
![Boîte de dialogue HTTPS personnalisé](./media/cdn-custom-ssl/domain-validation-form.png)

Suivez les instructions du formulaire ; deux options de vérification vous sont proposées :

- Vous pouvez approuver toutes les commandes futures passées par l’intermédiaire du même compte pour le même domaine racine, par exemple contoso.com. Il s’agit de l’approche recommandée si vous envisagez à l’avenir d’ajouter d’autres domaines personnalisés pour le même domaine racine.

- Vous pouvez approuver uniquement le nom d’hôte spécifique utilisé dans cette requête. L’approbation supplémentaire sera nécessaire pour les demandes suivantes.

Après l’approbation, DigiCert ajoute votre nom de domaine personnalisé au certificat SAN (Subject Alternative Names). Le certificat est valide pendant un an et est automatiquement renouvelé avant son expiration.

### <a name="step-3-wait-for-propagation"></a>Étape 3 : En attente de la propagation

Une fois le nom de domaine validé, l’activation de la fonctionnalité HTTPS sur un domaine personnalisé peut prendre jusqu’à 6 à 8 heures. Lorsque le processus est terminé, l’état « HTTPS personnalisé » est défini sur « Activé » dans le portail Azure, et les quatre étapes de l’opération sont marquées comme terminées dans le panneau HTTPS sur un domaine personnalisé. Votre domaine personnalisé est désormais prêt à utiliser HTTPS.

![Boîte de dialogue de l’activation de HTTPS](./media/cdn-custom-ssl/cdn-enable-custom-ssl-complete.png)

### <a name="operation-progress"></a>Progression de l’opération

Le tableau suivant présente le déroulement de l’opération qui s’exécute lorsque vous activez HTTPS. Une fois que vous avez activé HTTPS, les quatre étapes de l’opération s’affichent dans le panneau HTTPS sur un domaine personnalisé. À mesure que chaque étape devient active, des détails supplémentaires s’affichent sous l’étape qui est en cours d’exécution. Lorsqu’une étape se termine correctement, une coche verte apparaît en regard de celle-ci. 

| Étape de l’opération | Détails de l’étape | 
| --- | --- |
| 1 Soumission de la requête | Envoi de la requête |
| | Votre requête HTTPS est en cours de soumission. |
| | Votre requête HTTPS a été correctement envoyée. |
| 2 Validation du domaine | Nous vous avons envoyé un e-mail vous demandant de valider la propriété du domaine. En attente de votre confirmation. |
| | La propriété du domaine a été correctement validée. |
| | La requête de validation de la propriété du domaine a expiré ( le client n’a probablement pas répondu dans les 6 jours). Le protocole HTTPS n’est pas activé sur votre domaine. * |
| | La requête de validation de la propriété du domaine a été rejetée par le client. Le protocole HTTPS n’est pas activé sur votre domaine. * |
| 3 Approvisionnement du certificat | L’émission, par l’autorité de certification, du certificat nécessaire à l’activation HTTPS sur votre domaine est en cours. |
| | Le certificat a été émis et son déploiement est en cours sur le réseau CDN. Cette opération peut durer 6 heures. |
| | Le certificat a été correctement déployé sur le réseau CDN. |
| 4 Fin | Le protocole HTTPS a été correctement activé sur votre domaine. |

\* Ce message ne s’affiche pas à moins qu’une erreur se soit produite. 

Si une erreur survient avant la soumission de la requête, vous voyez le message d’erreur suivant :

<code>
We encountered an unexpected error while processing your HTTPS request. Please try again and contact support if the issue persists.
</code>

## <a name="disabling-https"></a>Désactivation de HTTPS

Une fois que vous avez activé le protocole HTTPS, vous pouvez le désactiver ultérieurement. Pour désactiver HTTPS, suivez ces étapes :

### <a name="step-1-disable-the-feature"></a>Étape 1 : Désactivation de la fonctionnalité 

1. Dans le [portail Azure](https://portal.azure.com), accédez à votre profil CDN Verizon Standard ou Premium.

2. Dans la liste des points de terminaison, cliquez sur celui contenant votre domaine personnalisé.

3. Cliquez sur le domaine personnalisé pour lequel vous souhaitez désactiver HTTPS.

    ![Panneau Point de terminaison](./media/cdn-custom-ssl/cdn-custom-domain-HTTPS-enabled.png)

4. Cliquez sur **Désactiver** pour désactiver HTTPS, puis sur **Appliquer**.

    ![Boîte de dialogue HTTPS personnalisé](./media/cdn-custom-ssl/cdn-disable-custom-ssl.png)

### <a name="step-2-wait-for-propagation"></a>Étape 2 : En attente de la propagation

Après la désactivation de la fonctionnalité HTTPS sur un domaine personnalisé, il peut s’écouler jusqu’à 6 à 8 heures avant que cette modification soit effective. Lorsque le processus est terminé, l’état « HTTPS personnalisé » est défini sur « désactivé » dans le portail Azure, et les trois étapes de l’opération sont marquées comme terminées dans le panneau HTTPS sur un domaine personnalisé. Votre domaine personnalisé ne peut plus utiliser HTTPS.

![Boîte de dialogue de la désactivation de HTTPS](./media/cdn-custom-ssl/cdn-disable-custom-ssl-complete.png)

### <a name="operation-progress"></a>Progression de l’opération

Le tableau suivant présente le déroulement de l’opération qui s’exécute lorsque vous désactivez HTTPS. Une fois que vous avez désactivé HTTPS, les trois étapes de l’opération s’affichent dans le panneau HTTPS sur un domaine personnalisé. À mesure que chaque étape devient active, des détails supplémentaires s’affichent sous l’étape en cours d’exécution. Lorsqu’une étape se termine correctement, une coche verte apparaît en regard de celle-ci. 

| Progression de l’opération | Détails de l’opération | 
| --- | --- |
| 1 Soumission de la requête | Envoi de la requête |
| 2 Annulation de l’approvisionnement du certificat | Suppression du certificat |
| 3 Fin | Le certificat est supprimé |

## <a name="frequently-asked-questions"></a>Forum Aux Questions

1. *Qui est le fournisseur de certificats et quel est le type de certificat utilisé ?*

    Nous utilisons le certificat SAN (Subject Alternative Names) fourni par DigiCert. Un certificat SAN peut sécuriser plusieurs noms de domaine qualifiés complets avec un certificat.

2. *Puis-je utiliser mon certificat dédié ?*
    
    Pas actuellement, mais nous y travaillons.

3. *Que se passe-t-il si je ne reçois pas l’e-mail de vérification de domaine de DigiCert ?*

    Contactez le support Microsoft si vous ne recevez pas d’e-mail dans les 24 heures.

4. *Un certificat SAN est-il moins sécurisé qu’un certificat dédié ?*
    
    Un certificat SAN suit les mêmes normes de sécurité et de chiffrement qu’un certificat dédié. Tous les certificats SSL émis utilisent la norme SHA-256 pour une sécurité améliorée du serveur.

5. *Puis-je utiliser le protocole HTTPS de domaine personnalisé avec Azure CDN Akamai ?*

    Actuellement, cette fonctionnalité est uniquement disponible avec Azure CDN Verizon. Nous travaillons pour que la prise en charge de cette fonctionnalité par Azure CDN Akamai soit disponible dans les prochains mois.


## <a name="next-steps"></a>Étapes suivantes

- Découvrez comment configurer un [domaine personnalisé sur votre point de terminaison Azure CDN](./cdn-map-content-to-custom-domain.md)


