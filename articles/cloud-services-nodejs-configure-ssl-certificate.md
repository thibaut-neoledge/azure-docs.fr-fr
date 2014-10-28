<properties linkid="dev-nodejs-enablessl" urlDisplayName="Enable SSL" pageTitle="Configure SSL for a cloud service (Node.js) - Azure" metaKeywords="Node.js Azure SSL, Node.js Azure HTTPS" description="Learn how to specify an HTTPS endpoint for a Node.js web role and how to upload an SSL certificate to secure your application." metaCanonical="" services="cloud-services" documentationCenter="nodejs" title="Configuring SSL for a Node.js Application in an Azure Web Role" authors="larryfr" solutions="" manager="" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="09/17/2014" ms.author="larryfr"></tags>

# Configuration de SSL pour une application Node.js dans un rôle web Azure

Le chiffrement SSL (Secure Socket Layer) est la méthode de sécurisation la
plus couramment utilisée pour envoyer des données sécurisées sur Internet. Cette tâche présente la spécification
d'un point de terminaison HTTPS pour une application Node.js hébergée en tant que service cloud Azure dans un rôle
web, ainsi que le téléchargement d'un certificat SSL pour sécuriser votre application.

<div class="dev-callout">Remarque
<p>Les &eacute;tapes de cet article s'appliquent uniquement aux applications Node h&eacute;berg&eacute;es en tant que service cloud Azure dans un r&ocirc;le&nbsp;web. Pour les sites web, consultez la page <a href="../web-sites-configure-ssl-certificate/">Configuration d'un certificat&nbsp;SSL pour un site&nbsp;web Azure</a>.</p>
</div>

Cette procédure comprend les étapes suivantes :

-   [Étape 1 : création d'un service Node.js et publication de ce service dans le cloud][Étape 1 : création d'un service Node.js et publication de ce service dans le cloud]
-   [Étape 2 : obtention d'un certificat SSL][Étape 2 : obtention d'un certificat SSL]
-   [Étape 3 : importation du certificat SSL][Étape 3 : importation du certificat SSL]
-   [Étape 4 : modification des fichiers de définition de service et de configuration][Étape 4 : modification des fichiers de définition de service et de configuration]
-   [Étape 5 : connexion à l'instance de rôle à l'aide de HTTPS][Étape 5 : connexion à l'instance de rôle à l'aide de HTTPS]

## <a name="step1"> </a>Étape 1 : création d'un service Node.js et publication de ce service dans le cloud

Lorsqu'une application Node.js est déployée sur un rôle web Azure,
le certificat de serveur et la connexion SSL sont gérés par Internet
Information Services (IIS) de façon à ce qu'il soit possible
d'écrire sur le service Node.js comme s'il s'agissait d'un service http. Vous pouvez créer un simple service
« hello world » Node.js en utilisant Azure PowerShell, en procédant ainsi :

1.  À partir du menu **Démarrer** ou de l'**écran d'accueil**, recherchez **Azure PowerShell**. Enfin, cliquez avec le bouton droit sur **Azure PowerShell** et sélectionnez **Exécuter en tant qu'administrateur**.

    ![Icône Azure PowerShell][Icône Azure PowerShell]

[WACOM.INCLUDE [install-dev-tools](../includes/install-dev-tools.md)]

1.  Créez un projet de service en utilisant la cmdlet **New-AzureServiceProject**.

    ![][]

2.  Ajoutez un rôle web à votre service en utilisant la cmdlet **Add-AzureNodeWebRole** :

    ![][1]

3.  Publiez vos services sur le cloud en utilisant la cmdlet **Publish-AzureServiceProject** :

    ![][2]

    <div class="dev-callout">
<strong>Remarque</strong>
<p>Si vous n'avez pas d&eacute;j&agrave; import&eacute; les param&egrave;tres de publication pour votre abonnement Azure, vous recevez une erreur lors de la publication. Pour plus d'informations sur le t&eacute;l&eacute;chargement et l'importation des param&egrave;tres de publication pour votre abonnement, consultez la page <a href="https://www.windowsazure.com/fr-fr/develop/nodejs/how-to-guides/powershell-cmdlets/#ImportPubSettings">Utilisation d'Azure PowerShell pour Node.js</a></p>
</div>

La valeur **URL du site web créé** renvoyée par la cmdlet **Publish-AzureServiceProject** contient le nom de domaine complet (FQDN) de votre application hébergée. Vous devrez obtenir un certificat SSL pour ce nom de domaine complet et le déployer sur Azure.

## <a name="step2"> </a>Étape 2 : obtention d'un certificat SSL

Pour configurer le chiffrement SSL pour une application, vous
devez d'abord obtenir un certificat SSL signé par une autorité de
certification, un tiers approuvé qui émet des certificats à cet effet. Si vous
n'en possédez pas, vous devez en obtenir un auprès de la société qui
vend des certificats SSL.

Le certificat SSL doit répondre aux prérequis suivants dans
Azure :

-   Le certificat doit contenir une clé privée.
-   Il doit être créé pour un échange de clés (fichier .pfx).
-   Le nom d'objet du certificat doit correspondre au domaine servant à
    accéder au service cloud. Vous ne pouvez pas acquérir un certificat SSL
    pour le domaine cloudapp.net. Le nom d'objet du certificat doit
    donc correspondre au nom de domaine personnalisé utilisé pour accéder à votre application. Par exemple, **mysecuresite.cloudapp.net**.
-   Le certificat doit utiliser au minimum un chiffrement à 2048 bits.

## <a name="step3"> </a>Étape 3 : importation du certificat SSL

Une fois le certificat obtenu, installez-le dans le magasin de certificats sur votre machine de développement. Ce certificat est ensuite récupéré et téléchargé vers Azure en tant qu'élément de votre package de déploiement d'application, en fonction des modifications de configuration effectuées au cours des étapes suivantes.

<div class="dev-callout">
<strong>Remarque</strong>
<p>Les &eacute;tapes de cette section se basent sur la version Windows&nbsp;8 de l'Assistant Importation de certificat. Si vous utilisez une version ant&eacute;rieure de Windows, l'ordre des &eacute;tapes affich&eacute;es dans l'Assistant peut &ecirc;tre diff&eacute;rent. Si c'est le cas, veillez &agrave; lire l'int&eacute;gralit&eacute; de cette section avant d'utiliser l'Assistant Importation de certificat afin de conna&icirc;tre toutes les actions &agrave; ex&eacute;cuter.</p>
</div>

Pour importer le certificat SSL, procédez comme suit :

1.  Dans l'Explorateur Windows, accédez au répertoire du fichier **.pfx** contenant le certificat. Double-cliquez ensuite sur le certificat. L'Assistant Importation de certificat s'affiche alors.

    ![Assistant Certificat][Assistant Certificat]

2.  Dans la section **Emplacement de stockage**, sélectionnez **Utilisateur actuel**, puis cliquez sur **Suivant**. Le certificat est alors installé dans le magasin de certificats pour votre compte utilisateur.

3.  Poursuivez l'Assistant en acceptant les valeurs par défaut, jusqu'à l'écran **Protection de clé privée**. Entrez alors le mot de passe lié au certificat (le cas échéant). Sélectionnez également **Marquer cette clé comme exportable**. Enfin, cliquez sur **Suivant**.

    ![Protection de clé privée][Protection de clé privée]

4.  Poursuivez dans l'Assistant en acceptant les valeurs par défaut jusqu'à la fin de l'installation du certificat.

Vous devez maintenant modifier votre définition de service pour référencer
le certificat installé.

## <a name="step4"> </a>Étape 4 : modification des fichiers de définition de service et de configuration

Votre application doit être configurée pour référencer le certificat, et un point de
terminaison HTTPS doit être ajouté. Suite à cette opération, les fichiers de définition
de service et de configuration de service doivent être mis à jour.

1.  Dans le répertoire de service, ouvrez le fichier de définition
    de service (ServiceDefinition.csdef), ajoutez une section **Certificates** dans la section **WebRole**, puis ajoutez les
    informations qui suivent sur le certificat :

        <WebRole name="WebRole1" vmsize="ExtraSmall">
        ...
            <Certificates>
                <Certificate name="SampleCertificate" 
                    storeLocation="LocalMachine" storeName="My" />
            </Certificates>
        ...
        </WebRole>

    La section **Certificates** définit le nom du certificat, son
    emplacement et le nom du magasin dans lequel il se trouve. Depuis l'installation du certificat dans le magasin de certificats, la valeur « My » est utilisée. Il est également possible d'utiliser d'autres emplacements de stockage de certificats. Pour plus
    d'informations, consultez la page [Association d'un certificat à un service][Association d'un certificat à un service].

2.  Dans votre fichier de définition de service, mettez à jour l'élément http **InputEndpoint** dans la section **Endpoints** pour activer HTTPS :

        <WebRole name="WebRole1" vmsize="Small">
        ...
            <Endpoints>
                <InputEndpoint name="Endpoint1" protocol="https" 
                    port="443" certificate="SampleCertificate" />
            </Endpoints>
        ...
        </WebRole>

    Toutes les modifications nécessaires ont été apportées au fichier de
    définition de service, mais vous devez encore y ajouter les
    informations de certificat.

3.  Dans vos fichiers de configuration
    de service (**ServiceConfiguration.Cloud.cscfg**
    et **ServiceConfiguration.Local.cscfg**), ajoutez le certificat
    à la section **Certificates** vide au sein de la section **Role**,
    en remplaçant l'exemple de valeur d'empreinte
    numérique par la valeur indiquée sur votre certificat :

        <Role name="WebRole1">
        ...
            <Certificates>
                <Certificate name="SampleCertificate" 
                    thumbprint="9427befa18ec6865a9ebdc79d4c38de50e6316ff" 
                    thumbprintAlgorithm="sha1" />
            </Certificates>
        ...
        </Role>

4.  Republiez votre service dans le cloud pour actualiser votre
    configuration de service. À l'invite Azure PowerShell,
    tapez **Publish-AzureServiceProject** dans le répertoire du service.

    Dans le cadre de ce processus de publication, le certificat référencé est copié depuis le magasin de certificats. Il est également inclus dans le package de déploiement.

## <a name="step5"> </a>Étape 5 : connexion à l'instance de rôle à l'aide de HTTPS

Maintenant que votre déploiement est opérationnel dans Azure,
vous pouvez vous y connecter via HTTPS.

1.  Dans le portail de gestion Azure, sélectionnez votre service cloud, puis cliquez sur **Tableau de bord**.

2.  Faites défiler l'écran vers le bas, puis cliquez sur le lien affiché en tant qu'**URL du site** :

    ![URL du site][URL du site]

    <div class="dev-callout">
<strong>Remarque</strong>
<p>Si l'URL du site affich&eacute;e dans le portail ne commence pas par HTTPS, vous devez alors l'entrer manuellement dans le navigateur en utilisant HTTPS au lieu de HTTP.</p>
</div>

3.  Une nouvelle fenêtre de navigateur s'ouvre et affiche votre site web.

    Votre navigateur affiche une icône de cadenas pour
    indiquer que la connexion HTTPS est active. Ceci indique également que votre
    application est correctement configurée pour SSL.

    ![][3]

## Ressources supplémentaires

[Association d'un certificat à un service][Association d'un certificat à un service]

[Configuration de SSL pour une application Node.js dans un rôle de travail Azure][Configuration de SSL pour une application Node.js dans un rôle de travail Azure]

[Configuration d'un certificat SSL sur un point de terminaison HTTPS][Configuration d'un certificat SSL sur un point de terminaison HTTPS]

  [Configuration d'un certificat SSL pour un site web Azure]: ../web-sites-configure-ssl-certificate/
  [Étape 1 : création d'un service Node.js et publication de ce service dans le cloud]: #step1
  [Étape 2 : obtention d'un certificat SSL]: #step2
  [Étape 3 : importation du certificat SSL]: #step3
  [Étape 4 : modification des fichiers de définition de service et de configuration]: #step4
  [Étape 5 : connexion à l'instance de rôle à l'aide de HTTPS]: #step5
  [Icône Azure PowerShell]: ./media/cloud-services-nodejs-configure-ssl-certificate/azure-powershell-start.png
  [install-dev-tools]: ../includes/install-dev-tools.md
  []: ./media/cloud-services-nodejs-configure-ssl-certificate/enable-ssl-01.png
  [1]: ./media/cloud-services-nodejs-configure-ssl-certificate/enable-ssl-02.png
  [2]: ./media/cloud-services-nodejs-configure-ssl-certificate/enable-ssl-03.png
  [Utilisation d'Azure PowerShell pour Node.js]: https://www.windowsazure.com/fr-fr/develop/nodejs/how-to-guides/powershell-cmdlets/#ImportPubSettings
  [Assistant Certificat]: ./media/cloud-services-nodejs-configure-ssl-certificate/certificateimport.png
  [Protection de clé privée]: ./media/cloud-services-nodejs-configure-ssl-certificate/exportable.png
  [Association d'un certificat à un service]: http://msdn.microsoft.com/fr-fr/library/windowsazure/gg465718.aspx
  [URL du site]: ./media/cloud-services-nodejs-configure-ssl-certificate/site-url.png
  [3]: ./media/cloud-services-nodejs-configure-ssl-certificate/enable-ssl-08.png
  [Configuration de SSL pour une application Node.js dans un rôle de travail Azure]: /fr-fr/develop/nodejs/common-tasks/enable-ssl-worker-role/
  [Configuration d'un certificat SSL sur un point de terminaison HTTPS]: http://msdn.microsoft.com/fr-fr/library/windowsazure/ff795779.aspx
