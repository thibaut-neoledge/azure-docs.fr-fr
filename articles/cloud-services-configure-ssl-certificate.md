<properties 
	pageTitle="Configuration de SSL pour un service cloud - Azure" 
	description="Découvrez comment spécifier un point de terminaison HTTPS pour un rôle Web et télécharger un certificat SSL pour sécuriser votre application." 
	services="cloud-services" 
	documentationCenter=".net" 
	authors="Thraka" 
	manager="timlt" 
	editor="mollybos"/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/14/2014" 
	ms.author="adegeo"/>




# Configuration de SSL pour une application dans Azure

[AZURE.INCLUDE [websites-cloud-services-css-guided-walkthrough](../includes/websites-cloud-services-css-guided-walkthrough.md)]

Le chiffrement SSL (Secure Socket Layer) est la méthode de sécurisation la plus couramment utilisée pour envoyer des données sécurisées sur Internet. Cette tâche présente la spécification d'un point de terminaison HTTPS pour un rôle Web et le téléchargement d'un certificat SSL pour sécuriser votre application.

> [AZURE.NOTE] Les procédures de cette tâche s'appliquent à Azure Cloud Services. Pour Sites Web Azure, consultez la page [Configuration d'un certificat SSL pour un site Web Azure](../articles/web-sites-configure-ssl-certificate/).

Cette procédure comprend les étapes suivantes :

-   [Étape 1 : obtention d'un certificat SSL][]
-   [Étape 2 : modification des fichiers de définition de service et de configuration][]
-   [Étape 3 : téléchargement du package de déploiement et du certificat][]
-   [Étape 4 : connexion de l'instance de rôle à l'aide de HTTPS][]

Cette tâche utilise un déploiement de production. Les informations concernant le déploiement intermédiaire sont fournies à la fin de cette rubrique.

<h2><a name="step1"> </a>Étape 1 : obtention d'un certificat SSL</h2>

Pour configurer le chiffrement SSL pour une application, vous devez d'abord obtenir un certificat SSL signé par une autorité de certification, un tiers approuvé qui émet des certificats à cet effet. Si vous n'en possédez pas, vous devez en obtenir un auprès de la société qui vend des certificats SSL.

Le certificat SSL doit répondre aux prérequis suivants dans Azure :

-   Le certificat doit contenir une clé privée.
-   Le certificat doit être créé pour l'échange de clés et pouvoir faire l'objet d'un export au format Personal Information Exchange (.pfx).
-   Le nom d'objet du certificat doit correspondre au domaine servant à accéder au service cloud. Vous ne pouvez pas obtenir de certificat SSL d'une autorité de certification pour le domaine cloudapp.net. Vous devez acquérir un nom de domaine personnalisé à utiliser pour accéder à votre service. Lorsque vous demandez un certificat auprès d'une autorité de certification, le nom d'objet du certificat doit correspondre au nom de domaine personnalisé que vous utilisez pour accéder à votre application. Par exemple, si votre nom de domaine personnalisé est **contoso.com**, vous demandez un certificat auprès de votre autorité de certification pour ***.contoso.com** ou **www.contoso.com**.
-   Le certificat doit utiliser au minimum un chiffrement à 2048 bits.

Dans le cadre d'un test, vous pouvez créer et utiliser un certificat auto-signé. Un certificat auto-signé n'est pas authentifié par une autorité de certification et peut utiliser le domaine cloudapp.net comme URL de site Web. Par exemple, la tâche ci-dessous utilise un certificat auto-signé dans lequel le nom commun utilisé dans le certificat est **sslexample.cloudapp.net**. Pour plus d'informations sur la création d'un certificat auto-signé avec le gestionnaire des services Internet, consultez la page [Création d'un certificat pour un rôle][].

Ensuite, vous devez ajouter des informations sur le certificat dans votre définition de service et dans les fichiers de configuration de service.

<h2><a name="step2"> </a>Étape 2 : modification des fichiers de définition de service et de configuration</h2>

Votre application doit être configurée pour utiliser le certificat, et un point de terminaison HTTPS doit être ajouté. Suite à cette opération, les fichiers de définition de service et de configuration de service doivent être mis à jour.

1.  Dans votre environnement de développement, ouvrez le fichier de définition du service
    (CSDEF), ajoutez une section **Certificats** dans la section **WebRole**, puis
    ajoutez les informations suivantes sur le
    certificat :

        <WebRole name="CertificateTesting" vmsize="Small">
        ...
            <Certificates>
                <Certificate name="SampleCertificate" 
							 storeLocation="LocalMachine" 
                    		 storeName="CA" />
            </Certificates>
        ...
        </WebRole>

    La section **Certificates** définit le nom du certificat, son emplacement et le nom du magasin dans lequel il se trouve. Nous avons choisi de stocker le certificat dans le magasin d'autorité de certification, mais vous pouvez aussi choisir d'autres options. Pour plus d'informations, consultez la page [Association d'un certificat à un service][].

2.  Dans votre fichier de définition du service, ajoutez un élément **InputEndpoint** dans la section
    **Endpoints** pour activer HTTPS :

        <WebRole name="CertificateTesting" vmsize="Small">
        ...
            <Endpoints>
                <InputEndpoint name="HttpsIn" protocol="https" port="443" 
                    certificate="SampleCertificate" />
            </Endpoints>
        ...
        </WebRole>

3.  Dans votre fichier de définition du service, ajoutez un élément **Binding** dans la section
    **Sites**. Ceci ajoute une liaison HTTPS pour mapper le
    point de terminaison à votre site :

        <WebRole name="CertificateTesting" vmsize="Small">
        ...
            <Sites>
                <Site name="Web">
                    <Bindings>
                        <Binding name="HttpsIn" endpointName="HttpsIn" />
                    </Bindings>
                </Site>
            </Sites>
        ...
        </WebRole>

    Toutes les modifications nécessaires ont été
    apportées au fichier de définition de service, mais vous devez encore y ajouter
    les informations de certificat.

4.  Dans votre fichier de configuration de service (CSCFG) ServiceConfiguration.Cloud.cscfg, ajoutez une section **Certificats**
    dans la section **Role** en remplaçant la valeur d'empreinte numérique
    ci-dessous par celle de votre certificat :

        <Role name="Deployment">
        ...
            <Certificates>
                <Certificate name="SampleCertificate" 
                    thumbprint="9427befa18ec6865a9ebdc79d4c38de50e6316ff" 
                    thumbprintAlgorithm="sha1" />
            </Certificates>
        ...
        </Role>

(Cet exemple utilise **sha1** comme algorithme d'empreinte numérique. Spécifiez la valeur correspondant à l'algorithme d'empreinte numérique de votre certificat.)

Maintenant que les fichiers de définition du service et de configuration de service ont
été mis à jour, créez un package pour votre déploiement afin de le télécharger dans Azure. Si
vous utilisez **cspack**, assurez-vous que vous n'utilisez pas
l'indicateur **/ generateconfigurationfile**, car les
informations de certificat que vous venez juste d'entrer seraient écrasées.

<h2><a name="step3"> </a>Étape 3 : téléchargement du package de déploiement et du certificat</h2>

Votre package de déploiement a été mis à jour pour utiliser le certificat, et un
point de terminaison HTTPS a été ajouté. Vous pouvez maintenant télécharger le package et
le certificat dans Azure via le portail de gestion.

1. Connectez-vous au [Portail de gestion Azure][]. 
2. Cliquez sur **Nouveau**, sur **Service cloud**, puis sur **Création personnalisée**.
3. Dans la boîte de dialogue **Créer un service de cloud computing**, entrez les valeurs de l'URL, de la région et du groupe d'affinités et de l'abonnement. Assurez-vous que l'option **Déployez un package de service de cloud computing** est sélectionnée, puis cliquez sur le bouton **Suivant**.
3. Dans la boîte de dialogue **Publier votre service de cloud computing**, entrez les informations requises pour votre service cloud, sélectionnez **Production** comme environnement et assurez-vous que l'option **Ajouter des certificats** est activée. (Si un de vos rôles contient une seule instance, assurez-vous que l'option **Déployez même si un ou plusieurs rôles contiennent une seule instance** est activée.) 

    ![Publish your cloud service][0]

4.  Cliquez sur le bouton **Suivant**.
5.  Dans la boîte de dialogue **Ajouter un certificat**, entrez l'emplacement du
    fichier .pfx du certificat SSL, le mot de passe du certificat, puis cliquez sur
    **Attacher un certificat**.

    ![Add certificate][1]

6.  Assurez-vous que votre certificat figure dans la section **Certificats attachés**.

    ![Attached certificates][4]

7.  Cliquez sur le bouton **Terminer** pour créer votre service cloud. Lorsque le déploiement atteint l'état **Ready**, vous pouvez passer aux étapes suivantes.

<h2><a name="step4"> </a>Étape 4 : connexion de l'instance de rôle à l'aide de HTTPS</h2>

Maintenant que votre déploiement est opérationnel dans Azure, vous pouvez
vous y connecter via HTTPS.

1.  Dans le portail de gestion, sélectionnez le déploiement, puis cliquez sur le lien sous **URL du site**.

    ![Determine site URL][2]

2.  Dans votre navigateur Web, modifiez le lien pour utiliser **https** au lieu de **http**, puis accédez à la page.

    **Remarque :** si vous utilisez un certificat auto-signé, lorsque vous
    accédez à un point de terminaison HTTPS qui lui est associé,
    vous obtenez une erreur de certificat dans le navigateur. Pour remédier
    à ce problème, utilisez un certificat signé par une autorité de certification approuvée. En attendant, vous pouvez ignorer cette erreur. (Vous pouvez aussi ajouter le certificat auto-signé au magasin de certificats d'autorité de certification approuvé de l'utilisateur.)

    ![SSL example web site][3]

Si vous voulez utiliser SSL pour un déploiement intermédiaire au lieu d'un déploiement de production, vous devez d'abord déterminer l'URL utilisée pour le déploiement intermédiaire. Déployez votre service cloud dans l'environnement intermédiaire sans inclure de certificat ou d'informations de certificat. Une fois le déploiement effectué, vous pouvez déterminer l'URL basée sur le GUID, qui se trouve dans le champ **URL du site** du portail de gestion. Créez un certificat dont le nom commun est le même que l'URL basée sur le GUID (par exemple, **32818777-6e77-4ced-a8fc-57609d404462.cloudapp.net**), utilisez le portail de gestion pour ajouter le certificat à votre service de cloud intermédiaire, ajoutez les informations de certificat à vos fichiers CSDEF et CSCFG, recréez le package de votre application et mettez à jour le déploiement intermédiaire pour utiliser le nouveau package et le nouveau fichier CSCFG.

<h2><a name="additional_resources"> </a>Ressources supplémentaires</h2>

* [Association d'un certificat à un service][]

* [Configuration d'un certificat SSL sur un point de terminaison HTTPS][]

  [Étape 1 : obtention d'un certificat SSL]: #step1
  [Étape 2 : modification des fichiers de définition de service et de configuration]: #step2
  [Étape 3 : téléchargement du package de déploiement et du certificat]: #step3
  [Étape 4 : connexion de l'instance de rôle à l'aide de HTTPS]: #step4
  [Création d'un certificat pour un rôle]: http://msdn.microsoft.com/library/windowsazure/gg432987.aspx
  [Association d'un certificat à un service]: http://msdn.microsoft.com/library/windowsazure/gg465718.aspx
  [Portail de gestion Azure]: http://manage.windowsazure.com
  [0]: ./media/cloud-services-dotnet-configure-ssl-certificate/CreateCloudService.png
  [1]: ./media/cloud-services-dotnet-configure-ssl-certificate/AddCertificate.png
  [2]: ./media/cloud-services-dotnet-configure-ssl-certificate/CopyURL.png
  [3]: ./media/cloud-services-dotnet-configure-ssl-certificate/SSLCloudService.png
  [4]: ./media/cloud-services-dotnet-configure-ssl-certificate/AddCertificateComplete.png  
  [Configuration d'un certificat SSL sur un point de terminaison HTTPS]: http://msdn.microsoft.com/library/windowsazure/ff795779.aspx

<!--HONumber=45--> 
