---
title: "Ajouter un certificat SSL à votre application Azure App Service | Microsoft Docs"
description: "Découvrez comment ajouter un certificat SSL à votre application App Service."
services: app-service
documentationcenter: .net
author: ahmedelnably
manager: stefsch
editor: cephalin
tags: buy-ssl-certificates
ms.assetid: cdb9719a-c8eb-47e5-817f-e15eaea1f5f8
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/19/2016
ms.author: apurvajo;aelnably
translationtype: Human Translation
ms.sourcegitcommit: 0b53a5ab59779dc16825887b3c970927f1f30821
ms.openlocfilehash: 00e252e249dbd1a38a4649e435071685860722e4
ms.lasthandoff: 04/07/2017


---

# <a name="add-an-ssl-certificate-to-your-app-service-app"></a>Ajouter un certificat SSL à votre application App Service
> [!div class="op_single_selector"]
> * [Acheter un certificat SSL dans Azure](web-sites-purchase-ssl-web-site.md)
> * [Utiliser un certificat SSL depuis un autre emplacement](web-sites-configure-ssl-certificate.md)
> 
> 

Par défaut, [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) active le protocole HTTPS pour votre application web grâce à un certificat générique pour le domaine \*.azurewebsites.net. Si vous ne souhaitez pas configurer de domaine personnalisé, vous pouvez utiliser le certificat HTTPS par défaut. Comme tous les [domaines génériques](https://casecurity.org/2014/02/26/pros-and-cons-of-single-domain-multi-domain-and-wildcard-certificates), le certificat générique Azure n’est cependant pas aussi sécurisé qu’un domaine personnalisé avec votre propre certificat.

App Service vous offre un moyen simplifié d’acheter et de gérer un certificat SSL dans le portail Azure. 

Cet article explique comment acheter et configurer un certificat SSL pour votre application [App Service](http://go.microsoft.com/fwlink/?LinkId=529714). 

> [!NOTE]
> Vous ne pouvez pas utiliser les certificats SSL pour les noms de domaine personnalisé des applications qui sont hébergées dans un plan App Service gratuit ou partagé. Pour utiliser des certificats SSL, votre application web doit être hébergée dans un plan App Service de base, standard ou Premium. Si vous changez de type d’abonnement, le prix qui vous est facturé pour votre abonnement peut s’en trouver modifié. Pour en savoir plus, consultez [Tarification d’App Service](https://azure.microsoft.com/pricing/details/web-sites/).
> 
> 

> [!WARNING]
> N’essayez pas d’acheter un certificat SSL à l’aide d’un abonnement auquel n’est jointe aucune carte de crédit valide. Cela pourrait désactiver votre abonnement. 
> 
> 

## <a name="prerequisites"></a>Composants requis
Pour activer le protocole HTTPS pour un domaine personnalisé, commencez par [mapper un nom de domaine personnalisé à votre application Azure](web-sites-custom-domain-name.md).

Avant de demander un certificat SSL, déterminez les noms de domaine que vous sécuriserez avec celui-ci, afin de déterminer le type de certificat dont vous avez besoin. Si vous voulez sécuriser un seul nom de domaine comme contoso.com *ou* www.contoso.com, vous pouvez utiliser un certificat Standard (de base). Si vous devez en sécuriser plusieurs, par exemple contoso.com, www.contoso.com *et* mail.contoso.com, vous pouvez obtenir un [certificat générique](http://en.wikipedia.org/wiki/Wildcard_certificate).

## <a name="bkmk_purchasecert"></a>Acheter un certificat SSL

1. Dans le menu du [portail Azure](https://portal.azure.com/), sélectionnez **Parcourir**. Dans la zone de **recherche**, tapez **Certificat App Service**. Dans les résultats de la recherche, sélectionnez **Certificats App Service**. 

   ![Créer à l’aide de la navigation](./media/app-service-web-purchase-ssl-web-site/browse.jpg)
   
2. Sur la page **Certificats App Service**, sélectionnez **Ajouter**. 

   ![Ajouter un certificat](./media/app-service-web-purchase-ssl-web-site/add.jpg)

3. Entez un **nom** pour votre certificat SSL.
4. Entrez le **nom d’hôte**.
   
   > [!WARNING]
   > Il s’agit de l’une des parties les plus critiques du processus d’achat. Assurez-vous de saisir le nom de l’hôte (nom de domaine personnalisé) que ce certificat doit sécuriser. *N’ajoutez pas* « www » au début de votre nom d’hôte. Par exemple, si votre nom de domaine personnalisé est www.contoso.com, entrez **contoso.com** dans la zone **Nom d’hôte**. Le certificat protègera le domaine racine et le domaine www. 
   > 

5. Sélectionnez votre **abonnement**. 
   
   Si vous avez plusieurs abonnements, créez votre certificat SSL dans le même abonnement que celui que vous utilisez pour votre domaine personnalisé ou application web.

6. Sélectionnez ou créez un **groupe de ressources**.
   
   Vous pouvez utiliser des groupes de ressources pour gérer des ressources Azure connexes en tant qu’unité. Les groupes de ressources sont utiles lorsque vous souhaitez établir des règles de contrôle d’accès en fonction du rôle (RBAC) pour vos applications. Pour plus d’informations, consultez Gestion de vos ressources Azure.

7. Sélectionnez la **référence (SKU) du certificat**. 
   
   Sélectionnez la référence SKU qui correspond à vos besoins, puis sélectionnez **Créer**. 
   
   Vous pouvez choisir parmi deux références (SKU) dans App Service :
   * **S1** : certificat standard valide un an et renouvelé automatiquement  
   * **W1** : certificat générique valide un an et renouvelé automatiquement       
  
    ![Référence (SKU) de certificat](./media/app-service-web-purchase-ssl-web-site/SKU.jpg)

    Pour en savoir plus, consultez [Tarification d’App Service](https://azure.microsoft.com/pricing/details/web-sites/).

> [!NOTE]
> La création d’un certificat SSL peut prendre jusqu’à 10 minutes. Le processus implique plusieurs étapes qui sont effectuées en arrière-plan.  
> 
> 

## <a name="bkmk_StoreKeyVault"></a>Stocker le certificat dans Azure Key Vault

1. Une fois que vous avez acheté le certificat SSL, dans le portail Azure, accédez au panneau **Certificats App Service**.

   ![Certificat prêt pour le stockage dans Key Vault](./media/app-service-web-purchase-ssl-web-site/ReadyKV.jpg)
   
   Notez que l’état du certificat est **Pending Issuance** (En attente d’émission). Vous devez effectuer quelques étapes avant de commencer à utiliser ce certificat.

2. Dans le panneau **Propriétés du certificat**, sélectionnez **Configuration de certificat**. Pour stocker ce certificat dans Key Vault, sélectionnez **Étape 1 : Stocker**.
3. Dans le panneau **Statut Key Vault**, pour sélectionner un coffre de clés existant dans lequel stocker ce certificat, sélectionnez **Key Vault Repository** (Référentiel Key Vault).  Pour créer un nouveau coffre de clés dans le même abonnement et dans le même groupe de ressources, sélectionnez **Créer un coffre de clés**.

   ![Créer un coffre de clés](./media/app-service-web-purchase-ssl-web-site/NewKV.jpg)
   
   > [!NOTE]
   > Azure Key Vault engendre très peu de frais pour le stockage du certificat. Pour plus d’informations, consultez [Tarification d’Azure Key Vault](https://azure.microsoft.com/pricing/details/key-vault/).
   > 
   > 

4. Une fois que vous avez sélectionné le référentiel Key Vault dans lequel stocker le certificat, sélectionnez le bouton **Stocker** en haut du panneau **Statut Key Vault**.  
   
Pour vérifier votre sélection, vous pouvez cliquer sur le bouton Actualiser de votre navigateur. Une coche verte indique que cette étape est terminée.

## <a name="bkmk_VerifyOwnership"></a>Vérifier la propriété du domaine

1. Dans le panneau **Configuration de certificat**, sélectionnez **Étape 2 : Vérifier**.
2. Sélectionnez les options de vérification en utilisant les informations suivantes. 

Les certificats App Service prennent en charge trois types de vérification du domaine :

   * Vérification du domaine
   * Vérification par e-mail
   * Vérification manuelle

### <a name="domain-verification"></a>Vérification du domaine 
     
La vérification du domaine constitue le processus le plus pratique *seulement* si vous avez [acheté votre domaine personnalisé à partir d’Azure App Service](custom-dns-web-site-buydomains-web-app.md).

1. Pour terminer cette étape, sélectionnez **Vérifier**.
2. Pour mettre à jour l’état du certificat une fois la vérification terminée, sélectionnez **Actualiser**. Cette vérification peut prendre quelques minutes.

### <a name="mail-verification"></a>Vérification par e-mail
     
Dans le cas d’un domaine personnalisé, un e-mail de vérification est envoyé à l’adresse e-mail associée au domaine. 

1. Pour terminer l’étape de vérification par e-mail, ouvrez l’e-mail, puis cliquez sur le lien de vérification. 
2. Si vous avez besoin de renvoyer l’e-mail de vérification, cliquez sur le bouton **Renvoyer le message**.

### <a name="manual-verification"></a>Vérification manuelle    
     
**Vérification de page web HTML (fonctionne uniquement avec la référence SKU du certificat standard)**

1. Créez un fichier HTML nommé starfield.html. Le contenu de ce fichier doit être exactement identique au nom du jeton de vérification du domaine. (Vous pouvez copier le jeton à partir du panneau d’**état de la vérification du domaine**.)
2. Chargez ce fichier à la racine du serveur web qui héberge votre domaine. Par exemple, /.well-known/pki-validation/starfield.html.
3.  Pour mettre à jour l’état du certificat une fois la vérification terminée, sélectionnez **Actualiser**. Cette vérification peut prendre quelques minutes.

    Par exemple, si vous achetez un certificat standard pour **contosocertdemo.com** avec le jeton de vérification de domaine **tgjgthq8d11ttaeah97s3fr2sh**, une requête web effectuée sur **http://contosocertdemo.com/.well-known/pki-validation/starfield.html** doit retourner **tgjgthq8d11ttaeah97s3fr2sh**.

**Vérification d’enregistrement TXT DNS**
        
1. À l’aide de votre gestionnaire DNS, créez un enregistrement TXT sur le sous-domaine **@** avec une valeur égale au **jeton de vérification du domaine.**
2. Pour mettre à jour l’état du certificat une fois la vérification terminée, sélectionnez **Actualiser**. Cette vérification peut prendre quelques minutes.
 
   Par exemple, pour effectuer la validation d’un certificat générique portant le nom d’hôte **\*.contosocertdemo.com** ou **\*.subdomain.contosocertdemo.com** et le jeton de vérification du domaine **tgjgthq8d11ttaeah97s3fr2sh**, créez un enregistrement TXT sur **contosocertdemo.com** avec la valeur **tgjgthq8d11ttaeah97s3fr2sh**.     

## <a name="bkmk_AssignCertificate"></a>Attribuer le certificat à une application App Service

> [!NOTE]
> Avant d’effectuer les étapes de cette section, vous devez associer un nom de domaine personnalisé à votre application. Pour plus d’informations, consultez la page [Configurer un nom de domaine personnalisé pour une application web](web-sites-custom-domain-name.md).
> 
> 

1. Dans le menu du [portail Azure](https://portal.azure.com/), sélectionnez **App Service**.
2. Sélectionnez le nom de l’application à laquelle vous voulez attribuer ce certificat. 
3. Accédez à **Paramètres** > **Certificats SSL** > **Import App Service Certificate** (Importer un certificat App Service), puis sélectionnez le certificat.

   ![Importation d’un certificat](./media/app-service-web-purchase-ssl-web-site/ImportCertificate.png)

4. Dans la section **Liaisons SSL**, sélectionnez **Ajouter des liaisons**.
5. Dans le panneau **Add SSL Binding** (Ajouter une liaison SSL), sélectionnez le nom du domaine que vous souhaitez sécuriser avec le certificat SSL. Sélectionnez le certificat à utiliser. Vous pouvez également indiquer si vous voulez utiliser [l’indication du nom du serveur (SNI)](http://en.wikipedia.org/wiki/Server_Name_Indication) ou le protocole SSL basé sur IP.

   ![Liaisons SSL](./media/app-service-web-purchase-ssl-web-site/SSLBindings.png)
   
    * Pour associer un certificat à un nom de domaine, le protocole SSL basé sur IP mappe l’adresse IP publique dédiée du serveur au nom de domaine. Lorsque vous utilisez le protocole SSL basé sur IP, chaque nom de domaine (par exemple, contoso.com ou fabricam.com) associé à votre service doit avoir une adresse IP dédiée. Il s’agit de la méthode classique permettant d’associer un certificat SSL à un serveur web.
    * Le protocole SSL basé sur SNI est une extension des protocoles SSL et TLS ([Transport Layer Security](http://en.wikipedia.org/wiki/Transport_Layer_Security)). Lorsque vous utilisez un protocole SSL basé sur SNI, plusieurs domaines peuvent partager la même adresse IP. Chaque domaine possède un certificat de sécurité distinct. La plupart des navigateurs modernes (dont Internet Explorer, Chrome, Firefox et Opera) prennent en charge SNI. Il se peut toutefois que les navigateurs plus anciens ne proposent pas cette prise en charge. Pour plus d’informations sur SNI, consultez l’article [Indication du nom du serveur](http://en.wikipedia.org/wiki/Server_Name_Indication) sur Wikipédia.

6. Pour enregistrer vos modifications et activer SSL, cliquez sur **Ajouter une liaison**.

Si vous avez sélectionné **SSL basé sur IP** et que votre domaine personnalisé est configuré à l’aide d’un enregistrement A, vous devez effectuer les étapes supplémentaires suivantes.

1.  Une fois la liaison SSL basée sur IP configurée, une adresse IP dédiée est attribuée à votre application. Pour rechercher l’adresse IP, accédez à **Paramètres** > **Domaine personnalisé**. Au-dessus de la section **Noms d’hôte**, votre adresse IP est répertoriée en tant qu’**adresse IP externe**.

   ![SSL basé sur IP](./media/app-service-web-purchase-ssl-web-site/virtual-ip-address.png)
    
  Cette adresse IP est différente de l’adresse IP virtuelle utilisée précédemment pour configurer l’enregistrement A de votre domaine. Si votre application est configurée pour utiliser SSL basé sur SNI, ou si elle n’est pas configurée pour utiliser SSL, aucune adresse IP n’est répertoriée ici.

2.  À l’aide des outils fournis par votre bureau d’enregistrement, modifiez l’enregistrement A de votre nom de domaine personnalisé de manière à ce qu’il pointe vers l’adresse IP utilisée lors de l’étape précédente.

3.  Pour vérifier que le certificat a été configuré correctement, accédez à votre application en utilisant HTTPS:// à la place de HTTP://.

## <a name="bkmk_Export"></a>Exporter votre certificat App Service
Vous pouvez créer une copie locale PFX d’un certificat App Service. Lorsque vous disposez d’une copie locale, vous pouvez utiliser le certificat avec d’autres services Azure. Pour plus d’informations, consultez notre billet de blog [Créer une copie locale PFX de votre certificat App Service](https://blogs.msdn.microsoft.com/appserviceteam/2017/02/24/creating-a-local-pfx-copy-of-app-service-certificate/).

## <a name="bkmk_Renew"></a>Renouveler automatiquement votre certificat App Service
Pour configurer les paramètres du renouvellement automatique de votre certificat ou pour renouveler manuellement votre certificat, dans le panneau **Propriétés du certificat**, sélectionnez **Paramètres de renouvellement automatique**. 

![Paramètres de renouvellement automatique](./media/app-service-web-purchase-ssl-web-site/autorenew.png)

Pour renouveler automatiquement votre certificat avant son expiration, **activez** l’option **Renouveler automatiquement**. Il s'agit de l'option par défaut. Si le renouvellement automatique est activé, nous tentons de renouveler votre certificat à partir du 90e jour avant son expiration. Si vous avez créé des liaisons SSL sur vos applications App Service dans le portail Azure, les liaisons sont également mises à jour lorsque le nouveau certificat est prêt (comme dans les scénarios de renouvellement de clé et de synchronisation). 

Si vous souhaitez gérer manuellement les renouvellements, **désactivez** l’option **Renouveler automatiquement**. Vous pouvez renouveler manuellement un certificat App Service uniquement s’il expire dans moins de 90 jours.

## <a name="bkmk_Rekey"></a>Renouveler la clé de votre certificat et le synchroniser

1. Si vous avez besoin de renouveler la clé de votre certificat (pour des raisons de sécurité), dans le panneau **Propriétés du certificat**, sélectionnez **Recréer la clé et synchroniser**. 
2. Sélectionnez **Recréer la clé**. Le processus peut prendre jusqu’à 10 minutes. 

   ![Recréer une clé SSL](./media/app-service-web-purchase-ssl-web-site/Rekey.jpg)

Voici des informations complémentaires sur le renouvellement de la clé :

* Le renouvellement de la clé de votre certificat remplace le certificat par un nouveau certificat. Le nouveau certificat est émis par l’autorité de certification.
* Le renouvellement de la clé ne vous est pas facturé pendant la durée de vie du certificat. 
* Le renouvellement de la clé de votre certificat affecte au certificat l’état **Pending Issuance** (En attente d’émission). 
* Une fois que le certificat est prêt, afin d’éviter toute interruption du service, prenez soin de synchroniser vos ressources à l’aide de ce certificat.
* L’option de synchronisation n’est pas disponible pour les certificats qui ne sont pas encore attribués à une application web. 

## <a name="next-steps"></a>Étapes suivantes

* [Sécuriser le domaine personnalisé de votre application avec le protocole HTTPS](web-sites-configure-ssl-certificate.md)
* [Acheter et configurer un nom de domaine personnalisé dans Azure App Service](custom-dns-web-site-buydomains-web-app.md)
* [Centre de gestion de la confidentialité Microsoft Azure](https://azure.microsoft.com/en-us/support/trust-center/)

> [!NOTE]
> Pour commencer à utiliser Azure App Service avant d’ouvrir un compte Azure, accédez à [Essayer App Service](https://azure.microsoft.com/try/app-service/). Vous pouvez créer une application web temporaire dans App Service. Aucune carte de crédit n’est nécessaire et vous ne prenez aucun engagement.
> 
> 

