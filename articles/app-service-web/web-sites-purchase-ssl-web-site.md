<properties
	pageTitle="Acheter et configurer un certificat SSL pour votre service Azure App Service"
	description="Découvrez comment acheter et configurer un certificat SSL pour votre service Azure App Service."
	services="app-service"
	documentationCenter=".net"
	authors="apurvajo"
	manager="stefsch"
	editor="cephalin"
	tags="buy-ssl-certificates"/>

<tags
	ms.service="app-service"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/30/2016"
	ms.author="apurvajo"/>

#Acheter et configurer un certificat SSL pour votre service Azure App Service

> [AZURE.SELECTOR]
- [Acheter un certificat SSL pour Web Apps](web-sites-purchase-ssl-web-site.md)
- [Activer le chiffrement SSL pour votre domaine personnalisé](web-sites-configure-ssl-certificate.md)  

Par défaut, **[Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714)** active déjà le protocole HTTPS pour votre application web grâce à un certificat générique pour le domaine *.azurewebsites.net. Si vous ne souhaitez pas configurer de domaine personnalisé, vous pouvez utiliser le certificat HTTPS par défaut. Cependant, comme tous les* [domaines génériques](https://casecurity.org/2014/02/26/pros-and-cons-of-single-domain-multi-domain-and-wildcard-certificates), il n’est pas aussi sécurisé qu’un domaine personnalisé avec votre propre certificat. Azure App Service vous fournit désormais un moyen simple d’acheter et de gérer un certificat SSL directement à partir du portail Azure sans jamais quitter le portail. Cet article explique comment acheter et configurer un certificat SSL pour votre service **[Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714)** en 3 étapes simples.

> [AZURE.NOTE]
Les certificats SSL pour les noms de domaine personnalisés ne peuvent pas être utilisés avec des applications web gratuites et partagées. Vous devez configurer votre application web pour le mode De base, Standard ou Premium, ce qui peut changer le prix facturé à votre abonnement. Pour plus d’informations, consultez **[Tarification de App Services](https://azure.microsoft.com/pricing/details/web-sites/)**.



##<a name="bkmk_Overview"></a>Vue d’ensemble
> [AZURE.NOTE]
N’essayez pas d’acheter un certificat SSL avec un abonnement qui n’est pas associé à une carte de crédit active. Cela pourrait entraîner la désactivation de votre abonnement.

##<a> Acheter, stocker et attribuer un certificat SSL pour votre domaine personnalisé </a>
Pour activer le protocole HTTPS pour un domaine personnalisé comme contoso.com, vous devez d’abord **[configurer un nom de domaine personnalisé dans Azure App Service.](web-sites-custom-domain-name.md)**

Avant de demander un certificat SSL, vous devez déterminer les noms de domaine qui seront sécurisés par celui-ci, et par conséquent le type de certificat à demander. Si vous devez sécuriser un seul nom de domaine comme contoso.com ou www.contoso.com, un certificat Standard (de base) suffit. Si vous devez en sécuriser plusieurs, par exemple contoso.com, www.contoso.com et mail.contoso.com, vous pouvez obtenir un **[certificat générique](http://en.wikipedia.org/wiki/Wildcard_certificate)**.

##<a name="bkmk_purchasecert"></a>Étape 0 : Passer une commande de certificat SSL

Dans cette étape, vous allez apprendre à passer une commande pour un certificat SSL de votre choix.

1.	Dans le **[portail Azure](https://portal.azure.com/)**, cliquez sur Parcourir et tapez « Certificats App Service » dans la barre de recherche, sélectionnez « Certificats App Service » dans les résultats, puis cliquez sur Ajouter. 

    ![insérer une image de création à l’aide de la navigation](./media/app-service-web-purchase-ssl-web-site/browse.jpg)

    ![insérer une image de création à l’aide de la navigation](./media/app-service-web-purchase-ssl-web-site/add.jpg)

2.	Enter un **nom convivial** pour votre certificat SSL.

3.	Entrez un **nom d’hôte**.
> [AZURE.NOTE]
    Il s’agit de l’une des parties les plus critiques du processus d’achat. Veillez à entrer un nom d’hôte correct (domaine personnalisé) que vous voulez protéger avec ce certificat. N’ajoutez **PAS** le nom d’hôte avec WWW. Par exemple, si votre nom de domaine personnalisé est www.contoso.com, entrez simplement contoso.com dans le champ Nom d’hôte, le certificat en question protégera à la fois le domaine www et le domaine racine.
    
4.	Sélectionnez votre **abonnement**.

    Si vous avez plusieurs abonnements, veillez à créer un certificat SSL dans le même abonnement que celui que vous avez utilisé pour votre domaine personnalisé ou application web en question.
       
5.	Sélectionnez ou créez un **groupe de ressources**.

    Les groupes de ressources vous permettent de gérer les ressources Azure connexes en tant qu’unité et sont utiles lors de l’établissement de règles de contrôle d’accès en fonction du rôle (RBAC) pour vos applications. Pour plus d’informations, consultez Gestion de vos ressources Azure.
     
6.	Sélectionnez la **référence SKU du certificat**.

    Enfin, sélectionnez la référence SKU qui correspond à vos besoins, puis cliquez sur Créer. Aujourd’hui, Azure App Service vous permet d’acheter deux références SKU différentes • S1 : Certificat Standard avec une validité de 1 an et un renouvellement automatique • W1 : Certificat générique avec une validité de 1 an et un renouvellement automatique. Pour plus d’informations, consultez **[Tarification de App Service](https://azure.microsoft.com/pricing/details/web-sites/)**.

![insérer une image de référence SKU de certificat](./media/app-service-web-purchase-ssl-web-site/SKU.jpg)

> [AZURE.NOTE]
La création d’un certificat SSL prend de 1 à 10 minutes environ. Ce processus effectue en arrière-plan plusieurs étapes qui, sans cela, sont très fastidieuses à effectuer manuellement.

##<a name="bkmk_StoreKeyVault"></a>Étape 1 : Stocker le certificat dans Azure Key Vault

Dans cette étape, vous allez apprendre à placer un certificat SSL que vous avez acheté dans le coffre de clés Azure Key Vault de votre choix et à le stocker dans ce dernier.

1.	Une fois l’achat du certificat SSL terminé, vous devrez ouvrir manuellement le panneau des ressources **Certificats App Service** en y accédant à nouveau (voir l’étape 1 ci-dessus).   

    ![insérer une image de prêt à stocker dans KV](./media/app-service-web-purchase-ssl-web-site/ReadyKV.jpg)

    Vous remarquerez que l’état du certificat est **« Émission en attente »**, car vous devez effectuer quelques étapes supplémentaires avant de commencer à utiliser ces certificats.
 
2. Cliquez sur **« Configuration du certificat »** dans le panneau Propriétés du certificat, puis cliquez sur **« Étape 1 : Stocker »** pour stocker ce certificat dans Azure Key Vault.

3.	Dans le panneau **« État du coffre de clés »**, cliquez sur **« Référentiel du coffre de clés »** pour choisir un coffre de clés existant destiné à stocker ce certificat **OU « Créer un coffre de clés »** pour créer un coffre de clés dans le même abonnement et groupe de ressources.
 
    ![insérer une image de création d’un KV](./media/app-service-web-purchase-ssl-web-site/NewKV.jpg)
 
    > [AZURE.NOTE]
    Azure Key Vault engendre très peu de frais pour le stockage de ce certificat. Le coût est d’environ 0,03 $ (USD). Pour plus d’informations, consultez **[Tarification de Key Vault](https://azure.microsoft.com/pricing/details/key-vault/)**.

4. Une fois que vous avez sélectionné le référentiel du coffre de clés dans lequel stocker ce certificat, continuez en cliquant sur le bouton **« Stocker »** en haut du panneau **« État du coffre de clés »**.

    Cette opération doit terminer l’étape de stockage du certificat que vous avez acheté avec le coffre de clés Azure Key Vault de votre choix. Lors de l’actualisation du panneau, une coche verte doit également s’afficher en regard de cette étape.
    
##<a name="bkmk_VerifyOwnership"></a>Étape 2 : Vérifier la propriété du domaine

Dans cette étape, vous allez apprendre à effectuer la vérification de la propriété du domaine pour un certificat SSL que vous venez de commander.

1.	Cliquez sur l’**« Étape 2 : Vérifier »** à partir du panneau **« Configuration du certificat »**. Quatre types de vérification du domaine sont pris en charge par les certificats App Service.

    * **Vérification d’App Service** 
    
        * C’est le processus le plus pratique si **votre domaine personnalisé est déjà attribué aux applications App Service.** Cette méthode répertorie toutes les applications App Service qui répondent à ce critère. Par exemple, dans le cas présent, **contosocertdemo.com** est un domaine personnalisé attribué à une application App Service appelée **« ContosoCertDemo »** et, par conséquent, c’est la seule application App Service répertoriée ici. En cas de déploiement dans plusieurs régions, elle les répertorie toutes dans l’ensemble des régions.
        
           La méthode de vérification est disponible UNIQUEMENT pour les achats de certificats Standard (de base). Pour les certificats génériques, ignorez cette méthode et passez à l’option B, C ou D ci-dessous.
        * Cliquez sur le bouton **Vérifier** pour terminer cette étape.
        * Cliquez sur **Actualiser** pour mettre à jour l’état du certificat une fois la vérification terminée. Cette vérification peut prendre quelques minutes.
        
        ![insérer une image de la vérification d’App Service](./media/app-service-web-purchase-ssl-web-site/AppVerify.jpg)

    * **Vérification du domaine**

        * Il s’agit du processus le plus pratique **SEULEMENT SI** vous avez **[acheté votre domaine personnalisé à partir d’Azure App Service.](custom-dns-web-site-buydomains-web-app.md)**
        
        * Cliquez sur le bouton **Vérifier** pour terminer cette étape.
        
        * Cliquez sur **Actualiser** pour mettre à jour l’état du certificat une fois la vérification terminée. Cette vérification peut prendre quelques minutes.

    * **Vérification par e-mail**
        
        * Un e-mail de vérification a déjà été envoyé aux adresses e-mail associées à ce domaine personnalisé.
         
        * Ouvrez l’e-mail, puis cliquez sur le lien de vérification pour terminer l’étape de vérification par e-mail.
        
        * Si vous avez besoin de renvoyer le courrier électronique de vérification, cliquez sur le bouton **Renvoyer le message**.
         
    * **Vérification manuelle**
                 
        1. **Vérification de page web HTML**
        
            * Créez un fichier HTML nommé **{Jeton de vérification du domaine}**.html. (Vous pouvez copier le jeton à partir du panneau d’état de vérification du domaine.)
            
            * Le contenu de ce fichier doit être exactement identique au nom du **jeton de vérification du domaine**.
            
            * Chargez ce fichier à la racine du serveur web qui héberge votre domaine.
            
            * Cliquez sur **Actualiser** pour mettre à jour l’état du certificat une fois la vérification terminée. Cette vérification peut prendre quelques minutes.
            
            Par exemple, si vous achetez un certificat standard pour contosocertdemo.com avec le jeton de vérification de domaine **cAGgQrKc**, une requête web effectuée sur **http://contosocertdemo.com/cAGgQrKc.html** doit retourner **cAGgQrKc.**
        2. **Vérification d’enregistrement TXT DNS**

            * À l’aide de votre gestionnaire DNS, créez un enregistrement TXT sur le sous-domaine **DZC** avec une valeur égale au **jeton de vérification du domaine**.
            
            * Cliquez sur **Actualiser** pour mettre à jour l’état du certificat une fois la vérification terminée. Cette vérification peut prendre quelques minutes.
                              
            Par exemple, pour effectuer la validation d’un certificat générique portant le nom d’hôte ***.contosocertdemo.com** ou ***.sousdomaine.contosocertdemo.com** et le jeton de vérification du domaine **cAGgQrKc**, vous devez créer un enregistrement TXT sur dzc.contosocertdemo.com avec la valeur **cAGgQrKc.**


##<a name="bkmk_AssignCertificate"></a>Étape 3 : Attribuer un certificat à une application App Service

Dans cette étape, vous allez apprendre à attribuer le certificat que vous venez d’acheter à vos applications App Service.

> [AZURE.NOTE]
Avant de suivre les étapes de cette section, vous devez avoir associé un nom de domaine personnalisé à votre application. Pour plus d’informations, consultez la page **[Configuration d’un nom de domaine personnalisé pour une application web](web-sites-custom-domain-name.md)**.

1.	Dans votre navigateur, ouvrez le **[portail Azure.](https://portal.azure.com/)**
2.	Cliquez sur l’option **App Service** sur le côté gauche de la page.
3.	Cliquez sur le nom de votre application à laquelle vous voulez attribuer ce certificat. 
4.	Dans **Paramètres**, cliquez sur **Domaines personnalisés et SSL**.
5.	Dans la **section Certificats**, cliquez sur **Importer un certificat**, puis sélectionnez le certificat que vous venez d’acheter.

    ![insérer une image d’importation de certificat](./media/app-service-web-purchase-ssl-web-site/ImportCertificate.jpg)

6. Dans la section **Liaisons SSL** de l’onglet **Paramètres SSL**, utilisez les listes déroulantes pour sélectionner le nom de domaine à sécuriser avec le chiffrement SSL, et le certificat à utiliser. Vous pouvez également indiquer si vous voulez utiliser **[l’indication du nom du serveur (SNI)](http://en.wikipedia.org/wiki/Server_Name_Indication)** ou le protocole SSL basé sur IP.

    ![insérer une image de liaisons SSL](./media/app-service-web-purchase-ssl-web-site/SSLBindings.jpg)

       • Le protocole SSL basé sur IP associe un certificat à un nom de domaine en mappant l’adresse IP publique dédiée du serveur au nom de domaine. Chaque nom de domaine (contoso.com, fabricam.com, etc.) associé à votre service doit donc posséder une adresse IP dédiée. Il s’agit de la méthode classique permettant d’associer des certificats SSL à un serveur web. • Le protocole SSL basé sur SNI est une extension des protocoles SSL et **[TLS (Transport Layer Security)](http://en.wikipedia.org/wiki/Transport_Layer_Security)** qui permet à plusieurs domaines de partager la même adresse IP, avec des certificats de sécurité distincts pour chaque domaine. La plupart des navigateurs modernes (dont Internet Explorer, Chrome, Firefox et Opera) prennent en charge SNI. Il se peut toutefois que les navigateurs plus anciens ne proposent pas cette prise en charge. Pour plus d’informations sur SNI, consultez l’article **[Server Name Indication](http://en.wikipedia.org/wiki/Server_Name_Indication)** sur Wikipédia.
       
7. Cliquez sur *Save* pour enregistrer les modifications et activer SSL.



Si vous avez sélectionné **SSL basé sur IP** et que votre domaine personnalisé est configuré à l’aide d’un enregistrement A, vous devez effectuer les étapes supplémentaires suivantes :

* Une fois la liaison SSL basée sur IP configurée, une adresse IP dédiée est attribuée à votre application. Celle-ci figure dans la page **Tableau de bord** de votre application, dans la section **Aperçu rapide**. Elle est répertoriée en tant qu’**Adresse IP virtuelle :**
    
    ![insérer une image d’IP SSL](./media/app-service-web-purchase-ssl-web-site/IPSSL.jpg)

    Cette adresse IP est différente de celle utilisée précédemment pour configurer l’enregistrement A de votre domaine. Si vous utilisez le protocole SSL basé sur SNI ou que vous n’utilisez pas SSL, aucune adresse n’est indiquée pour cette entrée.
    
2. À l’aide des outils fournis par votre bureau d’enregistrement, modifiez l’enregistrement A de votre nom de domaine personnalisé de manière à ce qu’il pointe vers l’adresse IP spécifiée lors de l’étape précédente. À ce stade, vous devez être en mesure de visiter votre application en utilisant HTTPS:// à la place de HTTP:// pour vérifier que le certificat a été configuré correctement.


##<a name="bkmk_Rekey"></a>Renouveler la clé du certificat et le synchroniser

1. Pour des raisons de sécurité, si vous avez besoin de renouveler la clé de votre certificat, sélectionnez simplement l’option **Renouveler la clé et synchroniser** dans le panneau **Propriétés du certificat**. 

2. Cliquez sur le bouton **Renouveler la clé** pour lancer le processus. Ce processus peut prendre de 1 à 10 minutes.

    ![insérer une image de renouvellement de clé SSL](./media/app-service-web-purchase-ssl-web-site/Rekey.jpg)

3. Le renouvellement de la clé de votre certificat remplace le certificat par un nouveau certificat émis par l’autorité de certification.
4. Le renouvellement de la clé ne vous est pas facturé pendant la durée de vie du certificat. 
5. Le renouvellement de la clé de votre certificat passe à l’état Émission en attente. 
6. Une fois que le certificat est prêt, prenez soin de synchroniser vos ressources à l’aide de ce certificat pour empêcher toute interruption du service.
7. L’option Synchroniser n’est pas disponible pour les certificats qui ne sont pas encore attribués à l’application web. 

## Autres ressources ##
- [Activer le protocole HTTPS pour une application dans Azure App Service](web-sites-configure-ssl-certificate.md)
- [Acheter et configurer un nom de domaine personnalisé dans Azure App Service](custom-dns-web-site-buydomains-web-app.md)
- [Centre de gestion de la confidentialité Microsoft Azure](/support/trust-center/security/)
- [Options de configuration déverrouillées dans Azure Web Sites](http://azure.microsoft.com/blog/2014/01/28/more-to-explore-configuration-options-unlocked-in-windows-azure-web-sites/)
- [Portail de gestion Azure](https://manage.windowsazure.com)

>[AZURE.NOTE] Si vous voulez vous familiariser avec Azure App Service avant d’ouvrir un compte Azure, accédez à la page [Essayer App Service](http://go.microsoft.com/fwlink/?LinkId=523751). Vous pourrez créer immédiatement et gratuitement une application de départ temporaire dans App Service. Aucune carte de crédit n’est requise ; vous ne prenez aucun engagement.

<!---HONumber=AcomDC_0413_2016-->