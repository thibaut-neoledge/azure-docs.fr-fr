<properties 
    pageTitle="Configuration de SSL pour un service cloud (classique) | Microsoft Azure" 
    description="Découvrez comment spécifier un point de terminaison HTTPS pour un rôle web et télécharger un certificat SSL pour sécuriser votre application." 
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
    ms.date="10/04/2016"
    ms.author="adegeo"/>





# <a name="configuring-ssl-for-an-application-in-azure"></a>Configuration de SSL pour une application dans Azure

> [AZURE.SELECTOR]
- [Portail Azure](cloud-services-configure-ssl-certificate-portal.md)
- [Portail Azure Classic](cloud-services-configure-ssl-certificate.md)

Le chiffrement SSL (Secure Socket Layer) est la méthode de sécurisation la plus couramment utilisée pour envoyer des données sécurisées sur Internet. Cette tâche présente la spécification d'un point de terminaison HTTPS pour un rôle Web et le téléchargement d'un certificat SSL pour sécuriser votre application.

> [AZURE.NOTE] Les procédures décrites dans cette tâche s’appliquent à Azure Cloud Services ; pour App Services, consultez [cet](../app-service-web/web-sites-configure-ssl-certificate.md) article.

Cette tâche utilise un déploiement de production. Vous trouverez des informations sur l’utilisation d’un déploiement intermédiaire à la fin de cette rubrique.

Lisez tout d’abord [cet](cloud-services-how-to-create-deploy.md) article si vous n’avez pas encore créé de service cloud.

[AZURE.INCLUDE [websites-cloud-services-css-guided-walkthrough](../../includes/websites-cloud-services-css-guided-walkthrough.md)]


## <a name="step-1:-get-an-ssl-certificate"></a>Étape 1 : obtention d’un certificat SSL

Pour configurer le chiffrement SSL pour une application, vous devez d’abord obtenir un certificat SSL signé par une autorité de certification, un tiers approuvé qui émet des certificats à cet effet. Si vous n’en possédez pas, vous devez en obtenir un auprès de la société qui vend des certificats SSL.

Le certificat SSL doit répondre aux prérequis suivants dans Azure :

-   Le certificat doit contenir une clé privée.
-   Le certificat doit être créé pour l'échange de clés et pouvoir faire l'objet d'un export au format Personal Information Exchange (.pfx).
-   Le nom d'objet du certificat doit correspondre au domaine servant à accéder au service cloud. Vous ne pouvez pas obtenir de certificat SSL d'une autorité de certification pour le domaine cloudapp.net. Vous devez acquérir un nom de domaine personnalisé à utiliser pour accéder à votre service. Lorsque vous demandez un certificat auprès d’une autorité de certification, le nom d’objet du certificat doit correspondre au nom de domaine personnalisé que vous utilisez pour accéder à votre application. Par exemple, si votre nom de domaine personnalisé est **contoso.com**, vous demandez un certificat auprès de votre autorité de certification pour ***.contoso.com** ou **www.contoso.com**.
-   Le certificat doit utiliser au minimum un chiffrement à 2048 bits.

Dans le cadre d’un test, vous pouvez [créer](cloud-services-certs-create.md) et utiliser un certificat auto-signé. Un certificat auto-signé n'est pas authentifié par une autorité de certification et peut utiliser le domaine cloudapp.net comme URL de site Web. Par exemple, la tâche ci-dessous utilise un certificat auto-signé dans lequel le nom commun utilisé dans le certificat est **sslexample.cloudapp.net**.

Ensuite, vous devez ajouter des informations sur le certificat dans votre définition de service et dans les fichiers de configuration de service.

## <a name="step-2:-modify-the-service-definition-and-configuration-files"></a>Étape 2 : modification des fichiers de définition de service et de configuration

Votre application doit être configurée pour utiliser le certificat, et un point de terminaison HTTPS doit être ajouté. Suite à cette opération, les fichiers de définition de service et de configuration de service doivent être mis à jour.

1.  Dans votre environnement de développement, ouvrez le fichier de définition du service (CSDEF), ajoutez une section **Certificates** dans la section **WebRole**, puis ajoutez les informations qui suivent sur le certificat (et les certificats intermédiaires) :

        <WebRole name="CertificateTesting" vmsize="Small">
        ...
            <Certificates>
                <Certificate name="SampleCertificate" 
                             storeLocation="LocalMachine" 
                             storeName="My"
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

  	| Valeur de l’autorisation  | Description |
  	| ----------------  | ----------- |
  	| limitedOrElevated | **(Par défaut)** Tous les processus de rôle peuvent accéder à la clé privée. |
  	| elevated          | Seuls les processus élevés peuvent accéder à la clé privée.|

2.  Dans votre fichier de définition du service, ajoutez un élément **InputEndpoint** dans la section **Endpoints** pour activer HTTPS :

        <WebRole name="CertificateTesting" vmsize="Small">
        ...
            <Endpoints>
                <InputEndpoint name="HttpsIn" protocol="https" port="443" 
                    certificate="SampleCertificate" />
            </Endpoints>
        ...
        </WebRole>

3.  Dans votre fichier de définition du service, ajoutez un élément **Binding** dans la section **Sites**. Cette section ajoute une liaison HTTPS pour mapper le point de terminaison à votre site :

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

4.  Dans votre fichier de configuration de service (CSCFG) ServiceConfiguration.Cloud.cscfg, ajoutez une section **Certificates** dans la section **Role** en remplaçant la valeur d’empreinte numérique ci-dessous par celle de votre certificat :

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

(L’exemple précédent utilise **sha1** comme algorithme d’empreinte numérique. Spécifiez la valeur correspondant à l'algorithme d'empreinte numérique de votre certificat.)

Maintenant que les fichiers de définition du service et de configuration de service ont été mis à jour, créez un package pour votre déploiement afin de le télécharger dans Azure. Si vous utilisez **cspack**, n’utilisez pas l’indicateur **/generateConfigurationFile**, car les informations de certificat que vous venez d’entrer seraient écrasées.

## <a name="step-3:-upload-a-certificate"></a>Étape 3 : chargement d’un certificat

Votre package de déploiement a été mis à jour pour utiliser le certificat, et un point de terminaison HTTPS a été ajouté. Vous pouvez maintenant télécharger le certificat dans Azure via le portail Azure Classic.

1. Connectez-vous au [portail Azure Classic][]. 
2. Cliquez sur **Cloud Services** dans le volet de navigation de gauche.
3. Cliquez sur le service cloud de votre choix.
4. Cliquer sur l’onglet **Certificats**.

    ![Cliquer sur l’onglet Certificats](./media/cloud-services-configure-ssl-certificate/click-cert.png)

5. Cliquez sur le bouton **Télécharger** .

    ![Télécharger](./media/cloud-services-configure-ssl-certificate/upload-button.png)
    
6. Fournissez le **Fichier**, le **Mot de passe**, puis cliquez sur **Terminé** (la coche).

## <a name="step-4:-connect-to-the-role-instance-by-using-https"></a>Étape 4 : connexion à l’instance de rôle à l’aide de HTTPS

Maintenant que votre déploiement est opérationnel dans Azure, vous pouvez vous y connecter via HTTPS.

1.  Dans le portail Azure Classic, sélectionnez le déploiement, puis cliquez sur le lien sous **Site URL**.

    ![Déterminer l'URL du site][2]

2.  Dans votre navigateur Web, modifiez le lien pour utiliser **HTTPS** au lieu de **HTTP**, puis accédez à la page.

    >[AZURE.NOTE] Si vous utilisez un certificat auto-signé, lorsque vous accédez à un point de terminaison HTTPS qui lui est associé, vous pourriez obtenir une erreur de certificat dans le navigateur. Pour remédier à ce problème, utilisez un certificat signé par une autorité de certification approuvée. En attendant, vous pouvez ignorer cette erreur. (Une autre possibilité est d'ajouter le certificat auto-signé au magasin de certificats d'autorité de certification approuvé de l'utilisateur.)

    ![Exemple de site Web SSL][3]

Si vous voulez utiliser SSL pour un déploiement intermédiaire au lieu d’un déploiement de production, vous devez d’abord déterminer l’URL utilisée pour le déploiement intermédiaire. Déployez votre service cloud dans l'environnement intermédiaire sans inclure de certificat ou d'informations de certificat. Une fois le déploiement effectué, vous pouvez déterminer l'URL basée sur le GUID, qui se trouve dans le champ **Site URL** du portail Azure Classic. Créez un certificat avec le nom commun (CN) similaire à l’URL basée sur GUID (par exemple, **32818777-6e77-4ced-a8fc-57609d404462.cloudapp.net**). Utilisez le portail Azure Classic pour ajouter le certificat à votre service cloud intermédiaire. Ensuite, ajoutez les informations du certificat à vos fichiers CSDEF et CSCFG, recréez le package de votre application et mettez à jour votre déploiement intermédiaire pour utiliser le nouveau package.

## <a name="next-steps"></a>Étapes suivantes

* [Configuration générale de votre service cloud](cloud-services-how-to-configure.md).
* Découvrez comment [déployer un service cloud](cloud-services-how-to-create-deploy.md).
* Configurez un [nom de domaine personnalisé](cloud-services-custom-domain-name.md).
* [Gérez votre service cloud](cloud-services-how-to-manage.md).


  [Portail Azure Classic]: http://manage.windowsazure.com
  [0]: ./media/cloud-services-configure-ssl-certificate/CreateCloudService.png
  [1]: ./media/cloud-services-configure-ssl-certificate/AddCertificate.png
  [2]: ./media/cloud-services-configure-ssl-certificate/CopyURL.png
  [3]: ./media/cloud-services-configure-ssl-certificate/SSLCloudService.png
  [4]: ./media/cloud-services-configure-ssl-certificate/AddCertificateComplete.png  



<!--HONumber=Oct16_HO2-->


