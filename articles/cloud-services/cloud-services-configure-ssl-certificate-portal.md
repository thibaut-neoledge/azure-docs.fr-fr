<properties 
	pageTitle="Configuration de SSL pour un service cloud | Microsoft Azure" 
	description="Découvrez comment spécifier un point de terminaison HTTPS pour un rôle web et télécharger un certificat SSL pour sécuriser votre application. Ces exemples utilisent le portail Azure." 
	services="cloud-services" 
	documentationCenter=".net" 
	authors="Thraka" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/15/2016"
	ms.author="adegeo"/>




# Configuration de SSL pour une application dans Azure

> [AZURE.SELECTOR]
- [Portail Azure](cloud-services-configure-ssl-certificate-portal.md)
- [Portail Azure Classic](cloud-services-configure-ssl-certificate.md)

Le chiffrement SSL (Secure Socket Layer) est la méthode de sécurisation la plus couramment utilisée pour envoyer des données sécurisées sur Internet. Cette tâche présente la spécification d'un point de terminaison HTTPS pour un rôle Web et le téléchargement d'un certificat SSL pour sécuriser votre application.

> [AZURE.NOTE] Les procédures décrites dans cette tâche s’appliquent à Azure Cloud Services ; pour App Services, consultez [cette page](../app-service-web/web-sites-configure-ssl-certificate.md).

Cette tâche utilise un déploiement de production. Les informations concernant le déploiement intermédiaire sont fournies à la fin de cette rubrique.

Lisez tout d’abord [ceci](cloud-services-how-to-create-deploy-portal.md) si vous n’avez pas encore créé de service cloud.

[AZURE.INCLUDE [websites-cloud-services-css-guided-walkthrough](../../includes/websites-cloud-services-css-guided-walkthrough.md)]

## Étape 1 : obtention d’un certificat SSL

Pour configurer le chiffrement SSL pour une application, vous devez d'abord obtenir un certificat SSL signé par une autorité de certification, un tiers approuvé qui émet des certificats à cet effet. Si vous n'en possédez pas, vous devez en obtenir un auprès de la société qui vend des certificats SSL.

Le certificat SSL doit répondre aux prérequis suivants dans Azure :

-   Le certificat doit contenir une clé privée.
-   Le certificat doit être créé pour l'échange de clés et pouvoir faire l'objet d'un export au format Personal Information Exchange (.pfx).
-   Le nom d'objet du certificat doit correspondre au domaine servant à accéder au service cloud. Vous ne pouvez pas obtenir de certificat SSL d'une autorité de certification pour le domaine cloudapp.net. Vous devez acquérir un nom de domaine personnalisé à utiliser pour accéder à votre service. Lorsque vous demandez un certificat auprès d'une autorité de certification, le nom d'objet du certificat doit correspondre au nom de domaine personnalisé que vous utilisez pour accéder à votre application. Par exemple, si votre nom de domaine personnalisé est **contoso.com**, vous demandez un certificat auprès de votre autorité de certification pour ****.contoso.com** ou **www.contoso.com**.
-   Le certificat doit utiliser au minimum un chiffrement à 2048 bits.

Dans le cadre d’un test, vous pouvez [créer](cloud-services-certs-create.md) et utiliser un certificat auto-signé. Un certificat auto-signé n'est pas authentifié par une autorité de certification et peut utiliser le domaine cloudapp.net comme URL de site Web. Par exemple, la tâche ci-dessous utilise un certificat auto-signé dans lequel le nom commun utilisé dans le certificat est **sslexample.cloudapp.net**.

Ensuite, vous devez ajouter des informations sur le certificat dans votre définition de service et dans les fichiers de configuration de service.

<a name="modify"> </a>
## Étape 2 : modification des fichiers de définition de service et de configuration

Votre application doit être configurée pour utiliser le certificat, et un point de terminaison HTTPS doit être ajouté. Suite à cette opération, les fichiers de définition de service et de configuration de service doivent être mis à jour.

1.  Dans votre environnement de développement, ouvrez le fichier de définition du service (CSDEF), ajoutez une section **Certificates** dans la section **WebRole**, puis ajoutez les informations qui suivent sur le certificat (et les certificats intermédiaires) :

        <WebRole name="CertificateTesting" vmsize="Small">
        ...
            <Certificates>
                <Certificate name="SampleCertificate" 
							 storeLocation="LocalMachine" 
                    		 storeName="CA"
                             permissionLevel="limitedOrElevated" />
                <!-- IMPORTANT! Unless your certificate is either
                self-signed or signed directly by the CA root, you
                must include all the intermediate certificates
                here. You must list them here, even if they are
                not bound to any endpoints. Failing to list any of
                the intermediate certificates may cause hard-to-reproduce
                interoperability problems on some clients.-->
                <Certificate name="CAForSampleCertificate"
                             storeLocation="LocalMachine"
                             storeName="CA"
                             permissionLevel="limitedOrElevated" />
            </Certificates>
        ...
        </WebRole>

    La section **Certificates** définit le nom du certificat, son emplacement et le nom du magasin dans lequel il se trouve.
    
    Les autorisations (attribut `permisionLevel`) peuvent être définies sur les valeurs suivantes :

    | Valeur de l’autorisation | Description |
    | ----------------  | ----------- |
    | limitedOrElevated | **(Par défaut)** Tous les processus de rôle peuvent accéder à la clé privée. |
    | elevated | Seuls les processus élevés peuvent accéder à la clé privée.|

2.  Dans votre fichier de définition du service, ajoutez un élément **InputEndpoint** dans la section **Endpoints** pour activer HTTPS :

        <WebRole name="CertificateTesting" vmsize="Small">
        ...
            <Endpoints>
                <InputEndpoint name="HttpsIn" protocol="https" port="443" 
                    certificate="SampleCertificate" />
            </Endpoints>
        ...
        </WebRole>

3.  Dans votre fichier de définition du service, ajoutez un élément **Binding** dans la section **Sites**. Ceci ajoute une liaison HTTPS pour mapper le point de terminaison à votre site :

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

    Toutes les modifications nécessaires ont été apportées au fichier de définition de service, mais vous devez encore y ajouter les informations de certificat.

4.  Dans votre fichier de configuration de service (CSCFG) ServiceConfiguration.Cloud.cscfg, ajoutez une section **Certificates** dans la section **Role** en remplaçant la valeur d'empreinte numérique ci-dessous par celle de votre certificat :

        <Role name="Deployment">
        ...
            <Certificates>
                <Certificate name="SampleCertificate" 
                    thumbprint="9427befa18ec6865a9ebdc79d4c38de50e6316ff" 
                    thumbprintAlgorithm="sha1" />
                <Certificate name="CAForSampleCertificate"
                    thumbprint="79d4c38de50e6316ff9427befa18ec6865a9ebdc" 
                    thumbprintAlgorithm="sha1" />
            </Certificates>
        ...
        </Role>

(Cet exemple utilise **sha1** comme algorithme d'empreinte numérique. Spécifiez la valeur correspondant à l'algorithme d'empreinte numérique de votre certificat.)

Maintenant que les fichiers de définition du service et de configuration de service ont été mis à jour, créez un package pour votre déploiement afin de le télécharger dans Azure. Si vous utilisez **cspack**, assurez-vous que vous n'utilisez pas l'indicateur **/generateConfigurationFile**, car les informations de certificat que vous venez juste d'entrer seraient écrasées.

## Étape 3 : chargement d’un certificat

Connectez-vous au portail et...

1. Sélectionnez votre service cloud via l’une des méthodes suivantes :
    - Dans le portail, sélectionnez votre **Service cloud**. (Il se trouve dans la **zone Parcourir tout/Récent**.)
    
        ![Publier votre service cloud](media/cloud-services-configure-ssl-certificate-portal/browse.png)
    
        **OR**
        
    - Dans **Parcourir tout**, sélectionnez **Cloud Services**, sous **Filtrer par**, puis sélectionnez l’instance de service cloud de votre choix.

3. Ouvrez les **Paramètres** du service cloud.

    ![Ouvrir les paramètres](media/cloud-services-configure-ssl-certificate-portal/all-settings.png)

4. Cliquez sur **Certificats**.

    ![Cliquer sur l’icône Certificats](media/cloud-services-configure-ssl-certificate-portal/certificate-item.png)

4. Fournissez le **Fichier**, le **Mot de passe**, puis cliquez sur **Télécharger**.

## Étape 4 : connexion à l’instance de rôle à l’aide de HTTPS

Maintenant que votre déploiement est opérationnel dans Azure, vous pouvez vous y connecter via HTTPS.
    
1.  Cliquez sur l’**URL du site** pour ouvrir le navigateur web.

    ![Cliquer sur l’URL du site](media/cloud-services-configure-ssl-certificate-portal/navigate.png)

2.  Dans votre navigateur Web, modifiez le lien pour utiliser **HTTPS** au lieu de **HTTP**, puis accédez à la page.

    >[AZURE.NOTE] si vous utilisez un certificat auto-signé, lorsque vous accédez à un point de terminaison HTTPS qui lui est associé, vous obtenez une erreur de certificat dans le navigateur. Pour remédier à ce problème, utilisez un certificat signé par une autorité de certification approuvée. En attendant, vous pouvez ignorer cette erreur. (Une autre possibilité est d'ajouter le certificat auto-signé au magasin de certificats d'autorité de certification approuvé de l'utilisateur.)

    ![Aperçu du site](media/cloud-services-configure-ssl-certificate-portal/show-site.png)

    >[AZURE.TIP] Si vous voulez utiliser SSL pour un déploiement intermédiaire au lieu d'un déploiement de production, vous devez d'abord déterminer l'URL utilisée pour le déploiement intermédiaire. Une fois le service cloud déployé, l’URL de l’environnement intermédiaire est déterminée par le GUID **ID de déploiement** au format suivant : `https://deployment-id.cloudapp.net/`
      
    >Créez un certificat dont le nom commun (CN) est identique à l’URL basée sur le GUID (par exemple, **328187776e774ceda8fc57609d404462.cloudapp.net**), utilisez le portail pour ajouter le certificat au service cloud intermédiaire, ajoutez les informations de certificat aux fichiers CSDEF et CSCFG, recréez le package de l’application et mettez à jour le déploiement intermédiaire pour utiliser le nouveau package et le nouveau fichier CSCFG.

## Étapes suivantes

* [Configuration générale de votre service cloud](cloud-services-how-to-configure-portal.md).
* Découvrez comment [déployer un service cloud](cloud-services-how-to-create-deploy-portal.md).
* Configurez un [nom de domaine personnalisé](cloud-services-custom-domain-name-portal.md).
* [Gérez votre service cloud](cloud-services-how-to-manage-portal.md).

<!---HONumber=AcomDC_0406_2016-->