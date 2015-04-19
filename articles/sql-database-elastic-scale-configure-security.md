<properties 
	title="Elastic Scale Security Configurations" 
	pageTitle="Configuration de la sécurité de l'infrastructure élastique" 
	description="Sécurité des services de fusion et de fractionnement utilisant la mise à l'échelle flexible pour la base de données SQL Azure" 
	metaKeywords="Elastic Scale Security Configurations, Azure SQL Database sharding, elastic scale " 
	services="sql-database" documentationCenter="" 
	manager="jhubbard" 
	authors="sidneyh@microsoft.com"/>

<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" ms.topic="article" ms.date="03/05/2015" 
	ms.author="sidneyh" />

# Configuration de la sécurité de l'infrastructure élastique  

L'infrastructure élastique de la base de données SQL Microsoft Azure inclut un service auto-hébergé. La distribution comprend un fichier de configuration de service qui comporte les paramètres liés à la sécurité devant être configurés.

1. [Configuration des certificats][]
2. [Adresses IP autorisées][]
3. [Prévention du déni de service][]
4. [Autres considérations de sécurité][]

## <a name="configuring-certificates"></a>Configuration des certificats

Les certificats sont configurés de deux manières. 

1. [Pour configurer le certificat SSL][]
2. [Pour configurer des certificats clients][] 

## <a name="obtain-certificates"></a>Pour obtenir des certificats

Les certificats peuvent être obtenus à partir d'autorités de certification publiques ou du [Service de certificats Windows](http://msdn.microsoft.com/library/windows/desktop/aa376539.aspx). Il s'agit des méthodes préférées pour obtenir des certificats.

Si ces options ne sont pas disponibles, vous pouvez générer des **certificats auto-signés**.
 
## <a name="tools"></a>Outils de génération de certificats

* [makecert.exe](http://msdn.microsoft.com/library/bfsktky3.aspx)
* [pvk2pfx.exe](http://msdn.microsoft.com/library/windows/hardware/ff550672.aspx)

### Pour exécuter les outils

* Depuis une invite de commandes développeur pour Visual Studio, consultez la rubrique [Invite de commandes Visual Studio](http://msdn.microsoft.com/library/ms229859.aspx) 

    Si installée, accédez à :

        %ProgramFiles(x86)%\Windows Kits\x.y\bin\x86 

* Obtenez le kit WDK à partir de la page [Windows 8.1 : téléchargement des kits et outils](http://msdn.microsoft.com/windows/hardware/gg454513#drivers)

##    <a name="to-configure-ssl-cert"></a>Pour configurer le certificat SSL
Un certificat SSL est nécessaire pour chiffrer les communications et authentifier le serveur. Choisissez le plus approprié des trois scénarios ci-dessous et exécutez toutes les étapes associées :

### Création d'un certificat auto-signé

1.    [Création d'un certificat auto-signé][]
2.    [Création d'un fichier PFX pour un certificat SSL auto-signé][]
3.    [Téléchargement du certificat SSL vers le service cloud][]
4.    [Mise à jour du certificat SSL dans le fichier de configuration de service][]
5.    [Importation de l'autorité de certification SSL][]

### Pour utiliser un certificat existant du magasin de certificats
1. [Exportation d'un certificat SSL à partir du magasin de certificats][]
2. [Téléchargement du certificat SSL vers le service cloud][]
3. [Mise à jour du certificat SSL dans le fichier de configuration de service][]

### Pour utiliser un certificat existant dans un fichier PFX

1. [Téléchargement du certificat SSL vers le service cloud][]
2. [Mise à jour du certificat SSL dans le fichier de configuration de service][]

## <a name="configuring-client-certs"></a>Pour configurer des certificats clients
Les certificats clients sont requis pour authentifier les demandes au service. Choisissez le plus approprié des trois scénarios ci-dessous et exécutez toutes les étapes associées :

### Désactivation des certificats clients
1.    [Désactivation de l'authentification par certificat client][]

### Émission de nouveaux certificats clients auto-signés
1.    [Création d'une autorité de certification auto-signée][]
2.    [Téléchargement du certificat CA vers le service cloud][]
3.    [Mise à jour du certificat CA dans le fichier de configuration de service][]
4.    [Émission de certificats clients][]
5.    [Création de fichiers PFX pour les certificats clients][]
6.    [Importation d'un certificat client][]
7.    [Copie des empreintes numériques des certificats clients][]
8.    [Configuration des clients autorisés dans le fichier de configuration de service][]

### Utilisation de certificats clients existants
1.    [Recherche de la clé publique de l'autorité de certification][]
2.    [Téléchargement du certificat CA vers le service cloud][]
3.    [Mise à jour du certificat CA dans le fichier de configuration de service][]
4.    [Copie des empreintes numériques des certificats clients][]
5.    [Configuration des clients autorisés dans le fichier de configuration de service][]
6.    [Configuration de la vérification de révocation des certificats clients][]

## <a name="allowed-ip-addresses"></a>Adresses IP autorisées

L'accès aux points de terminaison de service peut être limité à des plages d'adresses IP spécifiques.

## Pour configurer le cryptage pour le magasin

Un certificat est nécessaire pour chiffrer les informations d'identification stockées dans le magasin de métadonnées. Choisissez le plus approprié des trois scénarios ci-dessous et exécutez toutes les étapes associées :

### Utilisation du nouveau certificat auto-signé

1.	 [Création d'un certificat auto-signé][]
2.	 [Création d'un fichier PFX pour un certificat de chiffrement auto-signé][]
3.	 [Téléchargement du certificat de chiffrement vers le service cloud][]
4.	 [Mise à jour du certificat de chiffrement dans le fichier de configuration de service][]

### Utilisation d'un certificat existant du magasin de certificats

1.	 [Exportation d'un certificat de chiffrement à partir du magasin de certificats][]
2.	 [Téléchargement du certificat de chiffrement vers le service cloud][]
3.	 [Mise à jour du certificat de chiffrement dans le fichier de configuration de service][]

### Utilisation d'un certificat existant dans un fichier PFX

1.	 [Téléchargement du certificat de chiffrement vers le service cloud][]
2.	 [Mise à jour du certificat de chiffrement dans le fichier de configuration de service][]

## Configuration par défaut

La configuration par défaut refuse tout accès au point de terminaison HTTP. Il s'agit du paramètre recommandé, puisque les demandes pour ces points de terminaison peuvent comporter des informations sensibles comme les informations d'identification de la base de données.
La configuration par défaut accepte tout accès au point de terminaison HTTPS. Ce paramètre peut être restreint davantage.

### Modification de la configuration

Le groupe de règles de contrôle d'accès qui s'appliquent à un point de terminaison est configuré dans la section **<EndpointAcls>** du **fichier de configuration de service**.

    <EndpointAcls>
      <EndpointAcl role="SplitMergeWeb" endPoint="HttpIn" accessControl="DenyAll" />
      <EndpointAcl role="SplitMergeWeb" endPoint="HttpsIn" accessControl="AllowAll" />
    </EndpointAcls>

Les règles dans un groupe de contrôle d'accès sont configurées dans une section <AccessControl name=""> du fichier de configuration du service. 

Le format est expliqué dans la documentation de listes de contrôle d'accès réseau.
Par exemple, pour autoriser uniquement les adresses IP de la plage 100.100.0.0 à 100.100.255.255 à accéder au point de terminaison HTTPS, les règles ressembleraient à ceci :

    <AccessControl name="Retricted">
      <Rule action="permit" description="Some" order="1" remoteSubnet="100.100.0.0/16"/>
      <Rule action="deny" description="None" order="2" remoteSubnet="0.0.0.0/0" />
    </AccessControl>
    <EndpointAcls>
    <EndpointAcl role="SplitMergeWeb" endPoint="HttpsIn" accessControl="Restricted" />

## <a name = "denial-of-service-prevention"></a>Prévention du déni de service

Il existe deux mécanismes différents pris en charge pour détecter et prévenir les attaques par déni de service :

*    Limiter le nombre de demandes simultanées par hôte distant (désactivé par défaut)
*    Limiter le taux d'accès par hôte distant (activé par défaut)

Ces mécanismes sont basés sur les fonctionnalités plus longuement documentées dans la rubrique Sécurité IP dynamique dans IIS. Lors de la modification de cette configuration, soyez attentif aux facteurs suivants :

* Le comportement des proxys et des appareils de traduction d'adresses réseau sur les informations de l'hôte distant
* Chaque demande à une ressource du rôle web est prise en compte (par exemple, le chargement de scripts, les images, etc.)

## Limitation du nombre d'accès simultanés

Les paramètres qui configurent ce comportement sont les suivants :

    <Setting name="DynamicIpRestrictionDenyByConcurrentRequests" value="false" />
    <Setting name="DynamicIpRestrictionMaxConcurrentRequests" value="20" />

Modifiez DynamicIpRestrictionDenyByConcurrentRequests sur true pour activer cette protection.

## Restriction du taux d'accès

Les paramètres qui configurent ce comportement sont les suivants :

    <Setting name="DynamicIpRestrictionDenyByRequestRate" value="true" />
    <Setting name="DynamicIpRestrictionMaxRequests" value="100" />
    <Setting name="DynamicIpRestrictionRequestIntervalInMilliseconds" value="2000" />

## Configuration de la réponse à une demande refusée

Le paramètre suivant configure la réponse à une demande refusée :

    <Setting name="DynamicIpRestrictionDenyAction" value="AbortRequest" />
Reportez-vous à la documentation relative à la sécurité IP dynamique dans IIS pour les autres valeurs prises en charge.

## Opérations de configuration des certificats de service
Cette rubrique sert de référence uniquement. Suivez les étapes de configuration décrites dans :

* Configuration du certificat SSL
* Configuration des certificats clients

## <a name = "create-self-signed-cert"></a>Création d'un certificat auto-signé
Exécutez :

    makecert ^
      -n "CN=myservice.cloudapp.net" ^
      -e MM/DD/YYYY ^
      -r -cy end -sky exchange -eku "1.3.6.1.5.5.7.3.1" ^
      -a sha1 -len 2048 ^
      -sv MySSL.pvk MySSL.cer

Pour personnaliser :

*    -n avec l'URL du service. Les caractères génériques (CN=*.cloudapp.net) et d'autres noms (CN=myservice1.cloudapp.net, CN=myservice2.cloudapp.net) sont pris en charge.
*    -e avec la date d'expiration du certificat
Créez un mot de passe fort et spécifiez-le lorsque vous y êtes invité.

## <a name="create-pfx-for-self-signed-cert"></a>Création d'un fichier PFX pour un certificat SSL auto-signé

Exécutez :

        pvk2pfx -pvk MySSL.pvk -spc MySSL.cer

Entrez le mot de passe et exportez le certificat avec les options suivantes :
* Oui, exporter la clé privée
* Exporter toutes les propriétés étendues

## <a name="export-ssl-from-store"></a>Exportation d'un certificat SSL à partir du magasin de certificats

* Recherchez le certificat
* Cliquez sur Actions -> Toutes les tâches -> Exporter...
* Exportez le certificat dans un fichier .PFX avec les options suivantes :
    * Oui, exporter la clé privée
    * Inclure tous les certificats dans le chemin d'accès de certification si possible
    *Exporter toutes les propriétés étendues

## <a name="upload-ssl"></a>Téléchargement du certificat SSL vers le service cloud

Téléchargez le certificat avec le fichier .PFX existant ou généré avec la paire de clés SSL :

* Entrez le mot de passe protégeant les informations de clés privées

## <a name="update-ssl-in-csfg"></a>Mise à jour du certificat SSL dans le fichier de configuration de service

Mettez à jour la valeur de l'empreinte numérique du paramètre suivant du fichier de configuration de service avec l'empreinte numérique du certificat téléchargé vers le service cloud :

    <Certificate name="SSL" thumbprint="" thumbprintAlgorithm="sha1" />

## <a name="import-ssl-ca"></a>Importation de l'autorité de certification SSL

Suivez les étapes suivantes pour tous les comptes/ordinateurs qui communiquent avec le service :

* Double-cliquez sur le fichier .CER dans l'Explorateur Windows
* Dans la boîte de dialogue Certificat, cliquez sur Installer le certificat...
* Importez le certificat dans le magasin racine des autorités de certification approuvées

## <a name="turn-off-client-cert"></a>Désactivation de l'authentification par certificat client

Seule l'authentification par certificat client est prise en charge et sa désactivation permet l'accès public aux points de terminaison de service, à moins que d'autres mécanismes soient en place (par exemple, Microsoft Azure Virtual Network).

Définissez les paramètres suivants sur false dans le fichier de configuration de service pour désactiver la fonctionnalité :

    <Setting name="SetupWebAppForClientCertificates" value="false" />
    <Setting name="SetupWebserverForClientCertificates" value="false" />

Ensuite, copiez la même empreinte numérique que celle du certificat SSL dans le paramètre du certificat de l'autorité de certification :

    <Certificate name="CA" thumbprint="" thumbprintAlgorithm="sha1" />

## <a name="create-self-signed-ca"></a>Création d'une autorité de certification auto-signée
Exécutez les étapes suivantes pour créer un certificat auto-signé qui agit comme une autorité de certification :

    makecert ^
    -n "CN=MyCA" ^
    -e MM/DD/YYYY ^
     -r -cy authority -h 1 ^
     -a sha1 -len 2048 ^
      -sr localmachine -ss my ^
      MyCA.cer

Pour le personnaliser

*    -e with the certification expiration date


## <a name="find-ca-public-key"></a>Recherche de la clé publique de l'autorité de certification

Tous les certificats clients doivent avoir été émis par une autorité de certification approuvée par le service. Recherchez la clé publique d'accès à l'autorité de certification qui a émis les certificats clients qui seront utilisés pour l'authentification afin de la télécharger vers le service cloud.

Si le fichier comportant la clé publique n'est pas disponible, exportez-le à partir du magasin de certificats :

* Recherchez le certificat
    * Recherchez un certificat client émis par la même autorité de certification
* Double-cliquez sur le certificat
* Sélectionnez l'onglet Chemin d'accès de certification dans la boîte de dialogue Certificat
* Double-cliquez sur l'entrée de l'autorité de certification dans le chemin d'accès 
* Prenez note des propriétés du certificat
* Fermez la boîte de dialogue Certificat
* Recherchez le certificat
    * Recherchez l'autorité de certification indiquée ci-dessus
* Cliquez sur Actions -> Toutes les tâches -> Exporter...
* Exportez le certificat dans un fichier .CER avec les options suivantes :
    * Non, ne pas exporter la clé privée
    * Inclure tous les certificats dans le chemin d'accès de certification si possible
    * Exporter toutes les propriétés étendues

## <a name="upload-ca-cert"></a>Téléchargement du certificat de l'autorité de certification vers le service cloud

Téléchargez le certificat avec le fichier .CER existant ou généré avec la clé publique de l'autorité de certification.

## <a name="update-ca-in-csft"></a>Mise à jour du certificat de l'autorité de certification dans le fichier de configuration de service

Mettez à jour la valeur de l'empreinte numérique du paramètre suivant du fichier de configuration de service avec l'empreinte numérique du certificat téléchargé vers le service cloud :

    <Certificate name="CA" thumbprint="" thumbprintAlgorithm="sha1" />

Mettez à jour la valeur du paramètre suivant avec la même empreinte numérique :

    <Setting name="AdditionalTrustedRootCertificationAuthorities" value="" />

## <a name="issue-client-certs"></a>Émission de certificats clients

Chaque personne autorisée à accéder au service doit être dotée d'un certificat client émis pour son utilisation exclusive et doit choisir un mot de passe fort pour protéger sa clé privée. 

Les étapes suivantes doivent être exécutées sur l'ordinateur sur lequel le certificat auto-signé de l'autorité de certification a été généré et stocké :

    makecert ^
      -n "CN=My ID" ^
      -e MM/DD/YYYY ^
      -cy end -sky exchange -eku "1.3.6.1.5.5.7.3.2" ^
      -a sha1 -len 2048 ^
      -in "MyCA" -ir localmachine -is my ^
      -sv MyID.pvk MyID.cer

Personnalisation :

* -n avec un identifiant client qui sera authentifié avec ce certificat
* -e avec la date d'expiration du certificat
* MyID.pvk et MyID.cer avec des noms de fichier uniques pour ce certificat client

Cette commande vous demande de créer un mot de passe et de l'utiliser une seule fois. Utilisez un mot de passe fort.

## <a name="create-pfx-files"></a>Création de fichiers PFX pour les certificats clients

Pour chaque certificat client généré, exécutez :

    pvk2pfx -pvk MyID.pvk -spc MyID.cer

Personnalisation :

    MyID.pvk and MyID.cer with the filename for the client certificate

Entrez le mot de passe et exportez le certificat avec les options suivantes :

* Oui, exporter la clé privée
* Exporter toutes les propriétés étendues
* La personne pour laquelle ce certificat a été émis doit choisir le mot de passe d'exportation

## <a name="import-client-cert"></a>Importation d'un certificat client

Chaque personne pour laquelle un certificat client a été émis doit importer la paire de clés dans les ordinateurs qu'elle utilise pour communiquer avec le service :

* Double-cliquez sur le fichier .PFX dans l'Explorateur Windows
* Importez un certificat dans le magasin Personnel avec au moins l'option suivante :
    * Inclure toutes les propriétés étendues activées

## <a name="copy-client-cert"> </a> Copie des empreintes numériques des certificats clients
Chaque personne pour laquelle un certificat client a été émis doit suivre les étapes ci-dessous afin d'obtenir l'empreinte numérique de son certificat qui sera ajoutée au fichier de configuration de service :
* Exécutez certmgr.exe
* Sélectionnez l'onglet Personnel
* Double-cliquez sur le certificat client à utiliser pour l'authentification
* Dans la boîte de dialogue Certificat qui s'ouvre, sélectionnez l'onglet Détails
* Veillez à ce que l'option Afficher indique Tous
* Sélectionnez le champ nommé Empreinte numérique dans la liste
* Copiez la valeur de l'empreinte numérique
** Supprimez les caractères Unicode non visibles avant le premier chiffre
** Supprimez tous les espaces

## <a name="configure-allowed-client"></a>Configuration des clients autorisés dans le fichier de configuration de service

Mettez à jour la valeur du paramètre suivant dans le fichier de configuration de service avec une liste séparée par des virgules des empreintes numériques des certificats clients autorisés à accéder au service :

    <Setting name="AllowedClientCertificateThumbprints" value="" />

## <a name="configure-client-revocation"></a>Configuration de la vérification de révocation des certificats clients

Le paramètre par défaut ne vérifie pas l'état de révocation du certificat client auprès de l'autorité de certification. Pour activer les vérifications, si l'autorité de certification qui a émis les certificats clients prend en charge ces vérifications, définissez le paramètre suivant avec l'une des valeurs définies dans l'énumération X509RevocationMode :

    <Setting name="ClientCertificateRevocationCheck" value="NoCheck" />

## <a name="create-pfx-files-encryption"></a>Création d'un fichier PFX pour un certificat de chiffrement auto-signé

Pour un certificat de chiffrement, exécutez :

    pvk2pfx -pvk MyID.pvk -spc MyID.cer

Personnalisation :

    MyID.pvk and MyID.cer with the filename for the encryption certificate

Entrez le mot de passe et exportez le certificat avec les options suivantes :
*	Oui, exporter la clé privée
*	Exporter toutes les propriétés étendues
*	Vous aurez besoin du mot de passe lors du téléchargement du certificat vers le service cloud.

## <a name="export-encryption-from-store"></a>Exportation d'un certificat de chiffrement à partir du magasin de certificats

*	Recherchez le certificat
*	Cliquez sur Actions -> Toutes les tâches -> Exporter...
*	Exportez le certificat dans un fichier .PFX avec les options suivantes : 
  *	Oui, exporter la clé privée
  *	Inclure tous les certificats dans le chemin d'accès de certification si possible 
*	Exporter toutes les propriétés étendues

## <a name="upload-encryption-cert"></a> Téléchargement du certificat de chiffrement vers le service cloud

Téléchargez le certificat avec le fichier .PFX existant ou généré avec la paire de clés de chiffrement :

* Entrez le mot de passe protégeant les informations de clés privées

## <a name="update-encryption-in-csft"></a>Mise à jour du certificat de chiffrement dans le fichier de configuration de service

Mettez à jour la valeur de l'empreinte numérique des paramètres suivants du fichier de configuration de service avec l'empreinte numérique du certificat téléchargé vers le service cloud :

    <Certificate name="DataEncryptionPrimary" thumbprint="" thumbprintAlgorithm="sha1" />

## Opérations courantes de certificat

* Configuration du certificat SSL
* Configuration des certificats clients

## Recherchez le certificat

Procédez comme suit :

1. Exécutez mmc.exe.
2. Fichier -> Ajouter/supprimer un composant logiciel enfichable...
3. Sélectionnez Certificats
4. Cliquez sur Ajouter.
5. Choisissez l'emplacement du magasin de certificats
6. Cliquez sur Terminer
7. Cliquez sur OK
8. Développez les certificats
9. Développez le magasin de certificats
10. Développez le nœud enfant du certificat
11. Sélectionnez un certificat dans la liste sur le côté droit

## Exportation du certificat
Dans l'Assistant Exportation de certificat :

1. Cliquez sur Suivant
2. Sélectionnez l'option " Oui, exporter la clé privée "
3. Cliquez sur Suivant
4. Sélectionnez le format de fichier de sortie souhaité
5. Vérifiez les options de votre choix
6. Vérifiez le mot de passe
7. Entrez un mot de passe fort et confirmez-le
8. Cliquez sur Suivant
9. Tapez ou sélectionnez un nom de fichier dans lequel stocker le certificat (utilisez une extension .PFX)
10. Cliquez sur Suivant
11. Cliquez sur Terminer
12. Cliquez sur OK 

## Importation d'un certificat

Dans l'Assistant Importation de certificat :

1. Sélectionnez l'emplacement du magasin.
    1.     Utilisateur actuel si seuls les processus s'exécutant sous l'utilisateur actuel accèdent au service
    2.     Ordinateur local si d'autres processus de cet ordinateur accèdent au service
2. Cliquez sur Suivant
3. Si vous importez depuis un fichier, vérifiez le chemin d'accès
4. Si vous importez depuis un fichier .PFX :
    1.     Entrez le mot de passe protégeant la clé privée
    2.     Sélectionnez les options d'importation
5.     Sélectionnez Placer les certificats dans le magasin suivant
6.     Cliquez sur Parcourir
7.     Sélectionnez le magasin de votre choix
8.     Cliquez sur Terminer
    1.     Si le magasin racine des autorités de certification approuvées a été choisi, cliquez sur Oui
9.     Cliquez sur OK dans toutes les fenêtres des boîtes de dialogue

## <a name="upload-certificate"></a>Télécharger le certificat

Dans le [portail de gestion Azure](http://manage.windowsazure.com/)

1. Sélectionnez Cloud Services
2. Sélectionnez le service cloud
3. Dans le menu supérieur, cliquez sur Certificats
4. Cliquez sur Télécharger dans la barre inférieure
5. Sélectionnez le fichier de certificat
6. S'il s'agit d'un fichier .PFX, entrez le mot de passe de la clé privée
7. Lorsque vous avez terminé, copiez l'empreinte de certificat à partir de la nouvelle entrée dans la liste

# <a name="other-security"></a> Autres considérations de sécurité
 
Les paramètres SSL décrits dans ce document chiffrent les communications entre le service et ses clients lorsque le point de terminaison HTTPS est utilisé. Ceci est important car les informations d'identification pour l'accès à la base de données, et éventuellement à d'autres informations sensibles, sont contenues dans les communications. Notez, néanmoins, que le service conserve l'état interne, y compris les informations d'identification, dans ses tables internes de la base de données SQL Azure de Microsoft que vous avez fournies pour le stockage des métadonnées dans votre abonnement Microsoft Azure. Cette base de données a été définie dans le paramètre suivant dans votre fichier de configuration de service (fichier .CSCFG) : 

    <Setting name="ElasticScaleMetadata" value="Server=..." />

Les informations d'identification stockées dans cette base de données sont chiffrées. Toutefois, il est recommandé de s'assurer que les rôles Web et de travail de vos déploiements de service sont mis à jour et sécurisés, car les deux types de rôle ont accès à la base de données de métadonnées et au certificat utilisé pour le chiffrement et le déchiffrement des informations d'identification stockées. 

[AZURE.INCLUDE [elastic-scale-include](../includes/elastic-scale-include.md)]

[Configuration des certificats]:#configuring-certificates
[Adresses IP autorisées]:#allowed-ip-addresses
[Pour configurer des certificats clients]:#configuring-client-certs
[Création d'un certificat auto-signé]:#create-self-signed-cert
[Création d'un fichier PFX pour un certificat SSL auto-signé]:#create-pfx-for-self-signed-cert
[Téléchargement du certificat SSL vers le service cloud]: #upload-ssl
[Mise à jour du certificat SSL dans le fichier de configuration de service]: #update-ssl-in-csfg
[Importation de l'autorité de certification SSL]: "import-ssl-ca"
[Exportation d'un certificat SSL à partir du magasin de certificats]: #export-ssl-from-store
[Désactivation de l'authentification par certificat client]: #turn-off-client-cert
[Création d'une autorité de certification auto-signée]:#create-self-signed-ca
[Copie des empreintes numériques des certificats clients]:#copy-client-cert
[Téléchargement du certificat CA vers le service cloud]:#upload-ca-cert
[Mise à jour du certificat CA dans le fichier de configuration de service]:#update-ca-in-csft
[Émission de certificats clients]:#issue-client-certs
[Création de fichiers PFX pour les certificats clients]:#create-pfx-files
[Importation d'un certificat client]:#import-client-cert
[Configuration des clients autorisés dans le fichier de configuration de service]:#configure-allowed-client
[Recherche de la clé publique de l'autorité de certification]:#find-ca-public-key
[Configuration de la vérification de révocation des certificats clients]:#configure-client-revocation
[Prévention du déni de service]:#denial-of-service-prevention
[Pour obtenir des certificats]:#obtain-certificates
[Outils de génération de certificats]:#tools
[Pour configurer le certificat SSL]:#to-configure-ssl-cert
[Autres considérations de sécurité]:#other-security 
[Télécharger le certificat]:#upload-certificate
[Création d'un fichier PFX pour un certificat de chiffrement auto-signé]:#create-pfx-files-encryption
[Téléchargement du certificat de chiffrement vers le service cloud]:#upload-encryption-cert 
[Mise à jour du certificat de chiffrement dans le fichier de configuration de service]:#update-encryption-in-csft
[Exportation d'un certificat de chiffrement à partir du magasin de certificats]:#export-encryption-from-store

<!--HONumber=47-->
