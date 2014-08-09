<properties linkid="manage-linux-common-tasks-manage-certs" urlDisplayName="Manage certificates" pageTitle="Manage certificates for Linux virtual machines in Azure" metaKeywords="Azure management certs, uploading management certs, Azure Service Management API" description="Learn how to create and upload management certificates for Linux in Azure. The certificate is required if you use the Service Management API." metaCanonical="" services="virtual-machines" documentationCenter="" title="Create management certificates for Linux in Azure" authors="kathydav" solutions="" manager="jeffreyg" editor="tysonn" />

Création de certificats de gestion pour Linux dans Azure
========================================================

Un certificat de gestion est requis pour utiliser l'API de gestion de services afin d'interagir avec la plateforme d'images d'Azure.

De la documentation indiquant comment créer et gérer ces certificats est disponible à l'adresse [http://msdn.microsoft.com/fr-fr/library/windowsazure/gg551721.aspx](http://msdn.microsoft.com/fr-fr/library/windowsazure/gg551721.aspx). Vous pouvez également utiliser OpenSSL pour créer le certificat de gestion. Pour plus d’informations, consultez la page [OpenSSL](http://openssl.org/). Néanmoins, cette documentation se concentre principalement sur l'utilisation du portail Silverlight qui peut ne pas être accessible à certains utilisateurs Linux. Elle décrit comment obtenir l'accès à ces certificats, les intégrer dans nos différents outils et chez les partenaires et les utiliser de façon autonome en attendant l'intégration de cette fonctionnalité sur le portail de gestion Azure.

Sommaire
--------

-   [Obtention d'un certificat de gestion à partir du fichier publishsettings](#createcert)
-   [Installation d'un certificat de gestion à l'aide du portail de gestion Azure](#management)

Création et téléchargement d'un certificat de gestion
-----------------------------------------------------

Nous avons créé une méthode simple pour créer un certificat de gestion pour Azure à l'adresse : <https://windows.azure.com/download/publishprofile.aspx>

Une fois connecté avec vos identifiants pour le portail, vous pouvez générer un certificat de gestion qui est conservé avec votre ID d'abonnement, sur un fichier publishsetting que vous êtes invité à télécharger.

![linuxcredentials](./media/linux-create-management-cert/linuxcredentials.png)

Veillez à l'enregistrer en lieu sûr, car il n'est pas possible de le récupérer par la suite. Un nouveau certificat de gestion devra être généré. Le nombre total de certificats qu'il est possible d'utiliser dans le système est limité. Consultez la section adéquate de ce site Web pour confirmer ce nombre. Vous pouvez utiliser le certificat de plusieurs façons :

### Dans Visual Studio

![VSpublish](./media/linux-create-management-cert/VSpublish.png)

### Dans la ligne de commande Azure de Linux

Vous pouvez importer le certificat afin de l'utiliser en exécutant la commande d'importation de compte Azure :

![cmdlinepublish](./media/linux-create-management-cert/cmdlinepublish.png)

Avec d'autres partenaires ou logiciels pour lesquels vous avez besoin de l'outil, vous devez extraire le certificat de gestion du fichier et décoder le contenu en base 64. Certains partenaires, tels que ScaleXtreme et SUSE Studio peuvent utiliser le fichier directement dans son format d'origine.

Procédez comme suit pour extraire le certificat de gestion.

Vous devez extraire de ce fichier le contenu encodé en base 64 situé entre les guillemets, après ManagementCertificate.

      xml version="1.0" encoding="utf-8"
      >
    <PublishData>
      <PublishProfile PublishMethod="AzureServiceManagementAPI"
        Url="https://management.core.windows.net/"
        ManagementCertificate="xxxxx">
        <Subscription Id="8a4a0a51-728e-482e-8daa-c477f03c541d"
          Name="hjereztest" />
      </PublishProfile>
    </PublishData>

Copiez ce code dans un fichier, puis décodez-le à l'aide d'un décodeur base 64. Sous Linux, vous pouvez utiliser :

    Base64 -d [encodedfile] > [decodedfile].pfx

Vous obtenez un fichier .pfx que vous pouvez convertir dans d'autres formats en utilisant openssl pour extraire la clé privée si nécessaire :

openssl.exe pkcs12 -in publicAndprivate.pfx -nocerts -out privateKey.pem

Sur Windows, vous pouvez décoder et extraire le fichier PFX avec PowerShell ou un décodeur base 64 Windows gratuit tel que [http://www.fourmilab.ch/webtools/base64/base64.zip]() en exécutant la commande :

    base64 -d key.txt ->key.pfx

#### Limite de génération de certificats

Le nombre de certificats que vous pouvez créer sur la plateforme avec cet outil est limité à 10. Il n'est pas possible de récupérer les clés une fois que vous les avez générées, car ces clés privées ne sont pas conservées sur le système. Si vous atteignez la limite de certificats dans le système, contactez le support technique via les forums Azure afin que vos certificats soient effacés ou utilisez un navigateur pouvant afficher l'ancien portail Silverlight afin d'effectuer ces tâches.

#### Si votre clé privée est compromise

Si votre clé privée est compromise d'une quelconque façon, vous devez utiliser un navigateur prenant en charge Silverlight afin d'accéder à l'ancien portail et supprimer les certificats de gestion correspondants sur fichier. Vous pouvez également contacter l'assistance technique sur les forums.
 Il ne suffit pas de générer un nouveau certificat, car les 10 certificats demeurent valides et votre clé compromise continue de pouvoir accéder au site Web.

Installation d'un certificat de gestion à l'aide du portail de gestion Azure
----------------------------------------------------------------------------

Il existe différentes façons de créer un certificat de gestion. Pour plus d'informations sur la création de certificats, consultez la page [Création d'un certificat de gestion pour Azure](http://msdn.microsoft.com/fr-fr/library/windowsazure/gg551722.aspx). Une fois le certificat créé, ajoutez-le à votre abonnement dans Azure.

1.  Connectez-vous au portail de gestion Azure.

2.  Dans le volet de navigation, cliquez sur **Paramètres**.

3.  Sous **Certificats de gestion**, cliquez sur **Télécharger un certificat de gestion**.

4.  Dans **Télécharger un certificat de gestion**, accédez au fichier de certificat, puis cliquez sur **OK**.

### Obtention de l'empreinte numérique du certificat et de l'ID d'abonnement

Afin de télécharger le fichier .vhd sur Azure, vous devez disposer de l'empreinte numérique du certificat de gestion que vous avez ajouté ainsi que de votre ID d'abonnement.

1.  Dans le portail de gestion, cliquez sur **Paramètres**.

2.  Sous **Certificats de gestion**, cliquez sur votre certificat, puis enregistrez l'empreinte numérique dans le volet **Propriétés** en effectuant un copier-coller dans un emplacement où vous pourrez le récupérer ultérieurement.

L'ID de votre abonnement est également requis pour télécharger le fichier .vhd.

1.  Dans le portail de gestion, cliquez sur **Tous les éléments**.

2.  Dans le volet central, sous **Abonnement**, copiez l'abonnement et effectuez un copier-coller dans un emplacement où vous pourrez le récupérer ultérieurement.

### Transfert de ces informations vers les outils si vous avez généré votre propre clé

#### Pour CSUPLOAD

1.  Ouvrez une fenêtre d'invite de commandes du Kit de développement logiciel (SDK) Azure en tant qu'administrateur.
2.  Définissez la chaîne de connexion en utilisant la commande suivante, en remplaçant **Subscriptionid** et **CertThumbprint** par les valeurs obtenues précédemment :

        csupload Set-Connection "SubscriptionID=<Subscriptionid>;CertificateThumbprint=<Thumbprint>;ServiceManagementEndpoint=https://management.core.windows.net"

#### Pour les outils en ligne de commande Linux Azure.

Encodez le fichier PFX que vous avez créé en base 64 avec openssl, via la commande :

      Base64 [file] \> [econded file]

Vous devez ensuite fusionner votre ID d'abonnement et le fichier .pfx encodé en base 64 en un seul fichier comportant la structure suivante :

        ?xml version="1.0" encoding="utf-8"
        >
        <PublishData>
          <PublishProfile PublishMethod="AzureServiceManagementAPI"
            Url="https://management.core.windows.net/"
            ManagementCertificate="xxxxx">
            <Subscription Id="8a4a0a51-728e-482e-8daa-c477f03c541d"
              Name="hjereztest" />
          </PublishProfile>
        </PublishData>

Remplacez « xxxxx » par le contenu du [fichier encodé] utilisé pour fournir les détails aux outils en ligne de commande Linux Azure avec les commandes :
 Azure account import (File)

