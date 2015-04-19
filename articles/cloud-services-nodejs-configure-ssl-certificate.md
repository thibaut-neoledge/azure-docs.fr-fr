<properties 
	pageTitle="Configuration de SSL pour un service cloud (Node.js) - Azure" 
	description="Découvrez comment spécifier un point de terminaison HTTPS pour un rôle Web Node.js et télécharger un certificat SSL pour sécuriser votre application." 
	services="cloud-services" 
	documentationCenter="nodejs" 
	authors="" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="article" 
	ms.date="09/17/2014" 
	ms.author="wpickett"/>




# Configuration de SSL pour une application Node.js dans un rôle Web Azure

Le chiffrement Secure Socket Layer (SSL) est la méthode la plus couramment utilisée
pour sécuriser des données envoyées via Internet. Cette tâche explique comment
spécifier un point de terminaison HTTPS pour une application Node.js hébergée en tant qu'Azure Cloud Service dans un rôle Web et comment télécharger un
certificat SSL pour sécuriser votre application.

> [AZURE.NOTE] Les étapes de cet article s'appliquent uniquement aux applications Node hébergées en tant qu'Azure Cloud Service dans un rôle Web. Pour les sites Web, consultez la page [Configuration d'un certificat SSL pour un site Web Azure](web-sites-configure-ssl-certificate.md).

Cette procédure comprend les étapes suivantes :

-   [Étape 1 : création d'un service Node.js et publication de ce service dans le cloud]
-   [Étape 2 : obtention d'un certificat SSL]
-   [Étape 3 : importation du certificat SSL]
-   [Étape 4 : modification des fichiers de définition de service et de configuration]
-   [Étape 5 : connexion à l'instance de rôle à l'aide de HTTPS]

## <a name="step1"> </a>Étape 1 : création d'un service Node.js et publication de ce service dans le cloud

Lorsqu'une application Node.js est déployée sur un rôle Web Azure, le
certificat de serveur et la connexion SSL sont gérés par Internet
Information Services (IIS), afin que le service Node.js puisse être écrit
comme s'il s'agissait d'un service http. Vous pouvez créer un simple service Node.js 'hello
world' à l'aide d'Azure PowerShell en procédant comme suit :

1. À partir du menu **Démarrer** ou de l'**écran d'accueil**, recherchez **Azure PowerShell**. Enfin, cliquez avec le bouton droit sur **Azure PowerShell** et sélectionnez **Exécuter en tant qu'administrateur**.

	![Azure PowerShell icon][powershell-menu]

[AZURE.INCLUDE [install-dev-tools](../includes/install-dev-tools.md)]


2.  Créez un nouveau projet de service en utilisant l'applet de commande **New-AzureServiceProject**. 

	![][1]

3.  Ajoutez un rôle Web à votre service en utilisant l'applet de commande **Add-AzureNodeWebRole** :

    ![][2]

4.  Publiez vos services sur le cloud en utilisant l'applet de commande **Publish-AzureServiceProject** :

    ![][3]

	> [AZURE.NOTE] Si vous n'avez pas déjà importé les paramètres de publication pour votre abonnement Azure, vous recevez une erreur lors de la publication. Pour plus d'informations sur le téléchargement et l'importation des paramètres de publication pour votre abonnement, consultez la page [Utilisation d'Azure PowerShell pour Node.js](https://www.windowsazure.com/fr-fr/develop/nodejs/how-to-guides/powershell-cmdlets/#ImportPubSettings)

La valeur **URL du site Web créé** renvoyée par l'applet de commande **Publish-AzureServiceProject** contient le nom de domaine complet (FQDN) de votre application hébergée. Vous devrez obtenir un certificat SSL pour ce nom de domaine complet et le déployer sur Azure.

## <a name="step2"> </a>Étape 2 : obtention d'un certificat SSL

Pour configurer SSL pour une application, vous devez d'abord obtenir un certificat SSL
qui a été signé par une autorité de certification (CA), un
tiers approuvé qui émet des certificats à cet effet. Si vous n'en
possédez pas déjà un, vous devez en obtenir un auprès d'une société qui
vend des certificats SSL.

Le certificat doit répondre aux exigences suivantes relatives aux
certificats SSL dans Azure :

-   Le certificat doit contenir une clé privée.
-   Il doit être créé pour un échange de clés (fichier .pfx).
-   Le nom d'objet du certificat doit correspondre au domaine utilisé pour accéder au
    service cloud. Vous ne pouvez pas acquérir un certificat SSL pour le
    domaine cloudapp.net, donc le nom du sujet du certificat doit correspondre au
    nom de domaine personnalisé utilisé pour accéder à votre application. Par exemple, __mysecuresite.cloudapp.net__.
-   Le certificat doit utiliser au minimum un chiffrement à 2048 bits.

## <a name="step3"> </a>Étape 3 : importation du certificat SSL

Une fois le certificat obtenu, installez-le dans le magasin de certificats sur votre machine de développement. Ce certificat est ensuite récupéré et téléchargé vers Azure en tant qu'élément de votre package de déploiement d'application, en fonction des modifications de configuration effectuées au cours des étapes suivantes.

> [AZURE.NOTE] Les étapes de cette section se basent sur la version Windows 8 de l'Assistant Importation de certificat. Si vous utilisez une version antérieure de Windows, l'ordre des étapes affichées dans l'Assistant peut être différent. Si c'est le cas, veillez à lire l'intégralité de cette section avant d'utiliser l'Assistant Importation de certificat afin de connaître toutes les actions à exécuter.

Pour importer le certificat SSL, procédez comme suit :

1.   Dans l'Explorateur Windows, accédez au répertoire du fichier **.pfx** contenant le certificat. Double-cliquez ensuite sur le certificat. L'Assistant Importation de certificat s'affiche alors.
	
	![certificate wizard][cert-wizard]

2.   Dans la section **Emplacement de stockage**, sélectionnez **Utilisateur actuel**, puis cliquez sur **Suivant**. Le certificat est alors installé dans le magasin de certificats pour votre compte utilisateur.

3.   Suivez les instructions de l'Assistant en acceptant les valeurs par défaut, jusqu'à atteindre l'écran **Protection de clé privée**. Entrez alors le mot de passe lié au certificat (le cas échéant). Vous devez également sélectionner **Marquer cette clé comme exportable**. Enfin, cliquez sur **Suivant**.

	![private key protection][key-protection]

4. Poursuivez dans l'Assistant en acceptant les valeurs par défaut jusqu'à la fin de l'installation du certificat.

Vous devez maintenant modifier votre définition de service pour référencer le certificat
installé.

## <a name="step4"> </a>Étape 4 : modification des fichiers de définition de service et de configuration

Votre application doit être configurée pour faire référence au certificat et un point de terminaison
HTTPS doit être ajouté. Par conséquent, la définition de service et les fichiers de configuration de service
doivent être mis à jour.

1.  Dans le service d'annuaire, ouvrez le fichier de définition de service
    (ServiceDefinition.csdef), ajoutez une section **Certificats** dans la section **WebRole** et ajoutez les informations suivantes relatives au
    certificat :

        <WebRole name="WebRole1" vmsize="ExtraSmall">
        ...
            <Certificates>
                <Certificate name="SampleCertificate" 
                    storeLocation="LocalMachine" storeName="My" />
            </Certificates>
        ...
        </WebRole>

    La section **Certificats** définit le nom du certificat,
    son emplacement et le nom du magasin dans lequel il se trouve. Depuis l'installation du certificat dans le magasin de certificats, la valeur " My " est utilisée. Il est également possible d'utiliser d'autres emplacements de stockage de certificats. Consultez la rubrique [Comment associer un certificat à un Service] pour en savoir plus.

2.  Dans votre fichier de définition de service, mettez à jour l'élément http **InputEndpoint** dans la section **Point de terminaison** pour activer HTTPS :

        <WebRole name="WebRole1" vmsize="Small">
        ...
            <Endpoints>
                <InputEndpoint name="Endpoint1" protocol="https" 
                    port="443" certificate="SampleCertificate" />
            </Endpoints>
        ...
        </WebRole>

    Toutes les modifications nécessaires dans le fichier de définition de service ont été
    effectuées, mais vous devez ajouter les informations de certificat au
    fichier de configuration de service.

3.  Dans vos fichiers de configuration de service
    (**ServiceConfiguration.Cloud.cscfg** et
    **ServiceConfiguration.Local.cscfg**), ajoutez le certificat à la
    section vide **Certificats** dans la section **Rôle**,
    en remplaçant l'exemple de valeur d'empreinte numérique ci-dessous par celle de votre
    certificat :

        <Role name="WebRole1">
        ...
            <Certificates>
                <Certificate name="SampleCertificate" 
                    thumbprint="9427befa18ec6865a9ebdc79d4c38de50e6316ff" 
                    thumbprintAlgorithm="sha1" />
            </Certificates>
        ...
        </Role>

4.  Pour actualiser votre configuration mise à jour dans le cloud, publiez
    à nouveau votre service. Au moment de l'invite Azure PowerShell,
    saisissez **Publish-AzureServiceProject** à partir du répertoire de service.

	Dans le cadre de ce processus de publication, le certificat référencé est copié depuis le magasin de certificats. Il est également inclus dans le package de déploiement.

## <a name="step5"> </a>Étape 5 : connexion à l'instance de rôle à l'aide de HTTPS

Maintenant que votre déploiement est opérationnel dans Azure, vous pouvez
vous y connecter via HTTPS.

1.  Dans le portail de gestion, sélectionnez votre service cloud, puis cliquez sur **Tableau de bord**.

2. Faites défiler l'écran vers le bas, puis cliquez sur le lien affiché en tant qu'**URL du site** :

    ![the site url][site-url]

	> [AZURE.IMPORTANT] Si l'URL du site affichée dans le portail ne commence pas par HTTPS, vous devez alors l'entrer manuellement dans le navigateur en utilisant HTTPS au lieu de HTTP.

3.  Une nouvelle fenêtre de navigateur s'ouvre et affiche votre site Web.

    Votre navigateur affiche une icône de verrou pour indiquer qu'il
    utilise une connexion HTTPS. Cela indique également que votre application
    a été correctement configurée pour SSL.

    ![][8]

## Ressources supplémentaires

[Comment associer un certificat à un Service]

[Configuration de SSL pour une Application Node.js dans un rôle de travail Azure]

[Comment configurer un certificat SSL sur un point de terminaison HTTPS]

  [Étape 1 : création d'un service Node.js et publication de ce service dans le cloud]: #step1
  [Étape 2 : obtention d'un certificat SSL]: #step2
  [Étape 3 : importation du certificat SSL]: #step3
  [Étape 4 : modification des fichiers de définition de service et de configuration]: #step4
  [Étape 5 : connexion à l'instance de rôle à l'aide de HTTPS]: #step5
  [**Azure PowerShell**]: http://go.microsoft.com/?linkid=9790229&clcid=0x409
  
  
  
  
  [1]: ./media/cloud-services-nodejs-configure-ssl-certificate/enable-ssl-01.png
  [2]: ./media/cloud-services-nodejs-configure-ssl-certificate/enable-ssl-02.png
  [3]: ./media/cloud-services-nodejs-configure-ssl-certificate/enable-ssl-03.png
  [Portail de gestion Azure]: http://manage.windowsazure.com
  
  
  [Comment associer un certificat à un Service]: http://msdn.microsoft.com/library/windowsazure/gg465718.aspx
  
  [site-url]: ./media/cloud-services-nodejs-configure-ssl-certificate/site-url.png
  [8]: ./media/cloud-services-nodejs-configure-ssl-certificate/enable-ssl-08.png
  [Comment configurer un certificat SSL sur un point de terminaison HTTPS]: http://msdn.microsoft.com/library/windowsazure/ff795779.aspx
  [powershell-menu]: ./media/cloud-services-nodejs-configure-ssl-certificate/azure-powershell-start.png
  [cert-wizard]: ./media/cloud-services-nodejs-configure-ssl-certificate/certificateimport.png
  [key-protection]: ./media/cloud-services-nodejs-configure-ssl-certificate/exportable.png
  [Configuration de SSL pour une Application Node.js dans un rôle de travail Azure]: /fr-fr/develop/nodejs/common-tasks/enable-ssl-worker-role/

<!--HONumber=45--> 
