
Cet article décrit comment configurer le protocole HTTPS pour une application web dans Azure App Service. Il ne couvre pas l’authentification du certificat client. Pour plus d’informations à ce sujet, consultez [Comment configurer l’authentification mutuelle TLS pour les applications web](../articles/app-service-web/app-service-web-configure-tls-mutual-auth.md).

Par défaut, Azure active déjà le protocole HTTPS pour votre application grâce à un certificat générique pour le domaine *.azurewebsites.net. Si vous ne souhaitez pas configurer de domaine personnalisé, vous pouvez utiliser le certificat HTTPS par défaut. Comme [tous les domaines génériques](https://casecurity.org/2014/02/26/pros-and-cons-of-single-domain-multi-domain-and-wildcard-certificates/), il n’est cependant pas aussi sécurisé qu’un domaine personnalisé avec votre propre certificat.

Le reste de ce document fournit des détails sur l’activation du protocole HTTPS pour les domaines personnalisés, par exemple **contoso.com**, **www.contoso.com** ou ***.contoso.com**

<a name="bkmk_domainname"></a>
## Activer le chiffrement SSL pour votre domaine personnalisé

Pour activer le protocole HTTPS pour un domaine personnalisé comme **contoso.com**, vous devez d’abord [configurer un nom de domaine personnalisé dans Azure App Service](../articles/app-service-web/web-sites-custom-domain-name.md). Exécutez ensuite les étapes suivantes :

1. [Obtention d'un certificat SSL](#bkmk_getcert)
2. [Configuration d’un niveau tarifaire Standard ou Premium](#bkmk_standardmode)
2. [Configuration de SSL dans votre application](#bkmk_configuressl)
3. [Exécution de SSL sur votre application](#bkmk_enforce) (facultatif)

Si vous avez besoin d’une aide supplémentaire au sujet d’un point quelconque de cet article, contactez les experts Azure sur les [forums MSDN Azure et Stack Overflow](https://azure.microsoft.com/support/forums/). Vous pouvez également signaler un incident au support Azure. Accédez au [site de support Azure](https://azure.microsoft.com/support/options/), puis cliquez sur **Obtenir un support**.

<a name="bkmk_getcert"></a>
## 1\. Obtention d'un certificat SSL

Avant de demander un certificat SSL, vous devez déterminer les noms de domaine qui seront sécurisés par celui-ci, et par conséquent le type de certificat à demander. Si vous devez sécuriser un seul nom de domaine comme **contoso.com** ou **www.contoso.com**, un certificat de base suffit. Si vous devez en sécuriser plusieurs, par exemple **contoso.com**, **www.contoso.com** et **mail.contoso.com**, vous pouvez obtenir un [certificat générique](http://en.wikipedia.org/wiki/Wildcard_certificate) ou un certificat avec [autre nom d’objet](http://en.wikipedia.org/wiki/SubjectAltName) (subjectAltName).

Les certificats SSL utilisés avec App Service doivent être signés par une [autorité de certification](http://en.wikipedia.org/wiki/Certificate_authority). Si vous n’en possédez pas, vous devez en obtenir un auprès d’une société qui émet des certificats SSL. Pour obtenir la liste des autorités de certification, consultez la page [Programme de certificats racine SSL Windows et Windows Phone 8 (autorités de certification membres)][cas] dans la section Wiki de Microsoft TechNet.

Le certificat SSL doit répondre aux prérequis suivants dans Azure :

* Le certificat doit contenir une clé privée.
* Le certificat doit être créé pour l’échange de clés et pouvoir faire l’objet d’un export au format Personal Information Exchange (.pfx).
* Le nom d’objet du certificat doit correspondre au domaine utilisé pour accéder à l’application. Si vous voulez gérer plusieurs domaines avec ce certificat, vous devez utiliser un caractère générique ou spécifier un autre nom de l’objet, comme indiqué précédemment.
* Le certificat doit utiliser au minimum un chiffrement à 2 048 bits.
* Les certificats émis à partir de serveurs d’autorité de certification privés ne sont pas pris en charge par Azure App Service.

Pour obtenir un certificat SSL à utiliser avec Azure App Service, vous soumettez une demande de signature de certificat à une autorité de certification, puis générez un fichier .pfx à partir du certificat que vous recevez en retour. Vous pouvez effectuer cette opération à l’aide de l’outil de votre choix. Voici quelques méthodes courantes d’obtention d’un certificat :

- [Obtention d’un certificat à l’aide de Certreq.exe](#bkmk_certreq)
- [Obtention d’un certificat à l’aide du Gestionnaire des services Internet](#bkmk_iismgr)
- [Obtention d’un certificat à l’aide d’OpenSSL](#bkmk_openssl)
- [Obtention d’un certificat SubjectAltName à l’aide d’OpenSSL](#bkmk_subjectaltname)
- [Génération de certificats auto-signés (à des fins de test uniquement)](#bkmk_selfsigned)

> [AZURE.NOTE] Lors de ces étapes, vous devez entrer un **nom commun**, comme `www.contoso.com`. Pour les certificats génériques, cette valeur doit être *.nomdedomaine (par exemple, *.contoso.com). Pour prendre en charge un nom générique comme *.contoso.com et un nom de domaine racine tel que contoso.com, vous pouvez utiliser un certificat générique subjectAltName.
>
> Les certificats ECC (chiffrement à courbe elliptique) sont pris en charge par Azure App Service. Cependant, ils sont relativement nouveaux, et la procédure précise à suivre pour créer la demande de signature de certificat doit être déterminée avec votre autorité de certification.

Vous devrez peut-être obtenir des **[certificats intermédiaires](http://en.wikipedia.org/wiki/Intermediate_certificate_authorities)** (également appelés certificats de chaîne), si ceux-ci sont utilisés par l’autorité de certification. Les certificats intermédiaires étant plus sécurisés que les certificats sans chaîne, les autorités de certification les utilisent fréquemment. Ils sont souvent fournis sous forme de téléchargement par le site web de l’autorité de certification. Les étapes décrites dans cet article permettent de s’assurer que les certificats intermédiaires sont fusionnés avec ceux qui sont chargés vers vos applications.

<a name="bkmk_certreq"></a>
### Obtention d’un certificat à l’aide de Certreq.exe (Windows uniquement)

Certreq.exe est un utilitaire Windows pour la création de demandes de certificat. Il fait partie de l’installation Windows de base depuis Windows XP/Windows Server 2000 et doit être disponible sur les systèmes Windows récents. Suivez les étapes ci-dessous pour obtenir un certificat SSL à l’aide de certreq.exe.

1. Ouvrez le **Bloc-notes** et créez un document contenant les éléments suivants. Dans la ligne d’objet, remplacez **mysite.com** par le nom de domaine personnalisé de votre application. Par exemple, Objet = "CN=www.contoso.com".

		[NewRequest]
		Subject = "CN=mysite.com"
		Exportable = TRUE
		KeyLength = 2048
		KeySpec = 1
		KeyUsage = 0xA0
		MachineKeySet = True
		ProviderName = "Microsoft RSA SChannel Cryptographic Provider"
		ProviderType = 12

		[EnhancedKeyUsageExtension]
		OID=1.3.6.1.5.5.7.3.1

	Pour plus d’informations sur les options spécifiées plus haut, ainsi que sur les autres options disponibles, consultez la [documentation de référence Certreq](http://technet.microsoft.com/library/cc725793.aspx).

2. Enregistrez le fichier texte sous le nom **myrequest.txt**.

3. Dans l’**écran d’accueil** ou le **menu Démarrer**, exécutez **cmd.exe**.

4. À l’invite de commandes, utilisez la commande suivante pour créer le fichier de demande de certificat :

		certreq -new \path\to\myrequest.txt \path\to\create\myrequest.csr

	Spécifiez le chemin d’accès au fichier **myrequest.txt** créé à l’étape 1, ainsi que celui utilisé lors de la création du fichier **myrequest.csr**.

5. Envoyez le fichier **myrequest.csr** à une autorité de certification pour obtenir un certificat SSL. Pour cela, vous devrez peut-être télécharger le fichier ou l’ouvrir dans le Bloc-notes et coller son contenu directement dans un formulaire Web.

	Pour obtenir la liste des autorités de certification, consultez la page [Programme de certificats racine SSL Windows et Windows Phone 8 (autorités de certification membres)][cas] dans la section Wiki de Microsoft TechNet.

6. Une fois que l’autorité de certification vous a fourni un fichier de certificat (.CER); enregistrez-le sur l’ordinateur qui a généré la demande, puis utilisez la commande suivante pour accepter la demande et achever le processus de génération du certificat.

		certreq -accept -user mycert.cer

	Dans ce cas, le certificat **mycert.cer** reçu de l’autorité de certification servira à obtenir la signature du certificat. Aucun fichier n’est créé. Au lieu de cela, le certificat est stocké dans le magasin de certificats Windows.

6. Si l’autorité de certification utilise des certificats intermédiaires, vous devez les installer avant d’exporter le certificat lors des prochaines étapes. Ces certificats sont généralement fournis sous forme de téléchargement distinct par l’autorité de certification, dans plusieurs formats adaptés à différents types de serveurs. Sélectionnez la version fournie pour Microsoft IIS.

	Une fois le certificat téléchargé, cliquez dessus avec le bouton droit dans l’explorateur, puis sélectionnez **Installer le certificat**. Utilisez les valeurs par défaut de l’**Assistant Importation de certificat**, puis sélectionnez **Suivant** jusqu’à ce que l’importation soit terminée.

7. Pour exporter le certificat à partir du magasin de certificats, exécutez **certmgr.msc** à partir de l’**écran d’accueil** ou du **menu Démarrer**. Lorsque le **gestionnaire de certificats** apparaît, développez le dossier **Personnel**, puis sélectionnez **Certificats**. Dans le champ **Délivré à**, recherchez l’entrée correspondant au nom de domaine personnalisé pour lequel vous avez demandé un certificat. Le champ **Délivré par** doit répertorier l’autorité de certification utilisée pour ce certificat.

	![insérer l’image du gestionnaire de certificat ici][certmgr]

9. Cliquez avec le bouton droit sur le certificat, sélectionnez **Toutes les tâches**, puis **Exporter**. Dans l’**Assistant Exportation de certificat**, cliquez sur **Suivant**, puis sélectionnez **Oui, exporter la clé privée**. Cliquez sur **Suivant**.

	![Exporter la clé privée][certwiz1]

10. Sélectionnez **Échange d’informations personnelles - PKCS #12 **, **Inclure tous les certificats dans le chemin d’accès de certification** et **Exporter toutes les propriétés étendues**. Cliquez sur **Suivant**.

	![inclure tous les certificats et les propriétés étendues][certwiz2]

11. Sélectionnez **Mot de passe**, puis entrez et confirmez le mot de passe. Cliquez sur **Suivant**.

	![spécifier un mot de passe][certwiz3]

12. Indiquez le chemin d’accès et le nom de fichier contenant le certificat exporté. Le nom de fichier doit porter l’extension **.pfx**. Cliquez sur **Suivant** pour terminer le processus.

	![indiquer le chemin d’accès au fichier][certwiz4]

Vous pouvez maintenant charger le fichier PFX exporté vers votre application dans Azure App Service.

<a name="bkmk_openssl"></a>
### Obtention d’un certificat à l’aide d’OpenSSL

1. Générez une clé privée et une demande de signature de certificat en utilisant la commande suivante dans une ligne de commande, un interpréteur de commandes ou une session terminal :

		openssl req -new -nodes -keyout myserver.key -out server.csr -newkey rsa:2048

2. Lorsque vous y êtes invité, entrez les informations appropriées. Par exemple :

 		Country Name (2 letter code)
        State or Province Name (full name) []: Washington
        Locality Name (eg, city) []: Redmond
        Organization Name (eg, company) []: Microsoft
        Organizational Unit Name (eg, section) []: Azure
        Common Name (eg, YOUR name) []: www.microsoft.com
        Email Address []:

		Please enter the following 'extra' attributes to be sent with your certificate request

       	A challenge password []:

	Une fois le processus terminé, vous devez disposer de deux fichiers : **myserver.key** et **server.csr**. Le fichier **server.csr** contient la demande de signature de certificat.

3. Envoyez votre demande de signature de certificat à une autorité de certification pour obtenir un certificat SSL. Pour obtenir la liste des autorités de certification, consultez la page [Programme de certificats racine SSL Windows et Windows Phone 8 (autorités de certification membres)][cas] dans la section Wiki de Microsoft TechNet.

4. Lorsque vous avez obtenu un certificat d’une autorité de certification, enregistrez-le sous le nom **myserver.crt**. Si l’autorité de certification a fourni le certificat au format texte, collez le texte dans le fichier **myserver.crt**. Le contenu du fichier doit être semblable à ce qui suit lorsque vous l’affichez dans un éditeur de texte :

		-----BEGIN CERTIFICATE-----
		MIIDJDCCAgwCCQCpCY4o1LBQuzANBgkqhkiG9w0BAQUFADBUMQswCQYDVQQGEwJV
		UzELMAkGA1UECBMCV0ExEDAOBgNVBAcTB1JlZG1vbmQxEDAOBgNVBAsTB0NvbnRv
		c28xFDASBgNVBAMTC2NvbnRvc28uY29tMB4XDTE0MDExNjE1MzIyM1oXDTE1MDEx
		NjE1MzIyM1owVDELMAkGA1UEBhMCVVMxCzAJBgNVBAgTAldBMRAwDgYDVQQHEwdS
		ZWRtb25kMRAwDgYDVQQLEwdDb250b3NvMRQwEgYDVQQDEwtjb250b3NvLmNvbTCC
		ASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEBAN96hBX5EDgULtWkCRK7DMM3
		enae1LT9fXqGlbA7ScFvFivGvOLEqEPD//eLGsf15OYHFOQHK1hwgyfXa9sEDPMT
		3AsF3iWyF7FiEoR/qV6LdKjeQicJ2cXjGwf3G5vPoIaYifI5r0lhgOUqBxzaBDZ4
		xMgCh2yv7NavI17BHlWyQo90gS2X5glYGRhzY/fGp10BeUEgIs3Se0kQfBQOFUYb
		ktA6802lod5K0OxlQy4Oc8kfxTDf8AF2SPQ6BL7xxWrNl/Q2DuEEemjuMnLNxmeA
		Ik2+6Z6+WdvJoRxqHhleoL8ftOpWR20ToiZXCPo+fcmLod4ejsG5qjBlztVY4qsC
		AwEAATANBgkqhkiG9w0BAQUFAAOCAQEAVcM9AeeNFv2li69qBZLGDuK0NDHD3zhK
		Y0nDkqucgjE2QKUuvVSPodz8qwHnKoPwnSrTn8CRjW1gFq5qWEO50dGWgyLR8Wy1
		F69DYsEzodG+shv/G+vHJZg9QzutsJTB/Q8OoUCSnQS1PSPZP7RbvDV9b7Gx+gtg
		7kQ55j3A5vOrpI8N9CwdPuimtu6X8Ylw9ejWZsnyy0FMeOPpK3WTkDMxwwGxkU3Y
		lCRTzkv6vnHrlYQxyBLOSafCB1RWinN/slcWSLHADB6R+HeMiVKkFpooT+ghtii1
		A9PdUQIhK9bdaFicXPBYZ6AgNVuGtfwyuS5V6ucm7RE6+qf+QjXNFg==
		-----END CERTIFICATE-----

	Enregistrez le fichier .

5. À partir de la ligne de commande, d’un interpréteur de commandes ou d’une session terminal, utilisez la commande suivante pour convertir **myserver.key** et **myserver.crt** en **myserver.pfx**, le format requis par Azure App Service :

		openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt

	Lorsque vous y êtes invité, entrez un mot de passe pour sécuriser le fichier .pfx.

	> [AZURE.NOTE] Si l’autorité de certification utilise des certificats intermédiaires, vous devez les installer avant d’exporter le certificat à l'étape suivante. Ces certificats sont généralement fournis sous forme de téléchargement distinct par l’autorité de certification, dans plusieurs formats adaptés à différents types de serveurs. Sélectionnez la version fournie au format PEM (extension de fichier .pem).
	>
	> La commande suivante montre comment créer un fichier .pfx incluant des certificats intermédiaires, qui sont contenus dans le fichier **intermediate-cets.pem** :
	>
	>
	`````
	openssl pkcs12 -chain -export -out myserver.pfx -inkey myserver.key -in myserver.crt -certfile intermediate-cets.pem
	`````

	Une fois cette commande exécutée, vous devez disposer d’un fichier **myserver.pfx** utilisable avec Azure App Service.

<a name="bkmk_iismgr"></a>
### Obtention d'un certificat à l'aide du Gestionnaire des services Internet

Si vous maîtrisez le Gestionnaire des services Internet, vous pouvez l’utiliser pour générer un certificat pouvant être utilisé avec Azure App Service.

1. Générez une demande de signature de certificat avec le Gestionnaire des services Internet pour l’envoyer à l’autorité de certification. Pour plus d’informations sur la génération d’une demande de signature de certificat, consultez la page [Demander un certificat de serveur Internet (IIS 7)][iiscsr].

2. Envoyez votre demande de signature de certificat à une autorité de certification pour obtenir un certificat SSL. Pour obtenir la liste des autorités de certification, consultez la page [Programme de certificats racine SSL Windows et Windows Phone 8 (autorités de certification membres)][cas] dans la section Wiki de Microsoft TechNet.

3. Complétez la demande de signature de certificat avec le certificat fourni par l’autorité de certification. Pour plus d’informations sur la génération d’une demande de signature de certificat, consultez la page [Installer un certificat de serveur Internet (IIS 7)][installcertiis].

4. Si l’autorité de certification utilise des certificats intermédiaires, vous devez les installer avant d’exporter le certificat lors de la prochaine étape. Ces certificats sont généralement fournis sous forme de téléchargement distinct par l’autorité de certification, dans plusieurs formats adaptés à différents types de serveurs. Sélectionnez la version fournie pour Microsoft IIS.

	Une fois le certificat téléchargé, cliquez dessus avec le bouton droit dans l’explorateur, puis sélectionnez **Installer le certificat**. Utilisez les valeurs par défaut de l’**Assistant Importation de certificat**, puis sélectionnez **Suivant** jusqu’à ce que l’importation soit terminée.

4. Exportez le certificat à partir du gestionnaire des services Internet. Pour plus d’informations sur l’exportation du certificat, consultez la page [Exporter un certificat de serveur (IIS 7)][exportcertiis]. Le fichier exporté sera chargé ultérieurement vers Azure pour être utilisé avec votre application.

	> [AZURE.NOTE] Au cours de l’exportation, veillez à sélectionner l’option <strong>Oui, exporter la clé privée</strong>, qui permet d’inclure la clé privée dans le certificat exporté.

	> [AZURE.NOTE] Lors de l’exportation, veillez à sélectionner les options **Inclure tous les certificats dans le chemin d’accès de certification** et **Exporter toutes les propriétés étendues**. Ainsi, les certificats intermédiaires seront inclus dans le certificat exporté.

<a name="bkmk_subjectaltname"></a>
### Obtention d’un certificat SubjectAltName à l’aide d’OpenSSL

OpenSSL permet de créer une demande de certificat qui utilise l’extension SubjectAltName pour prendre en charge plusieurs noms de domaine avec un seul certificat mais, pour cela, nécessite un fichier de configuration. Les étapes suivantes permettent de créer un fichier de configuration, puis de l’utiliser pour demander un certificat.

1. Créez un fichier nommé __sancert.cnf__ et utilisez le contenu suivant :

		# -------------- BEGIN custom sancert.cnf -----
		HOME = .
		oid_section = new_oids
		[ new_oids ]
		[ req ]
		default_days = 730
		distinguished_name = req_distinguished_name
		encrypt_key = no
		string_mask = nombstr
		req_extensions = v3_req # Extensions to add to certificate request
		[ req_distinguished_name ]
		countryName = Country Name (2 letter code)
		countryName_default =
		stateOrProvinceName = State or Province Name (full name)
		stateOrProvinceName_default =
		localityName = Locality Name (eg, city)
		localityName_default =
		organizationalUnitName  = Organizational Unit Name (eg, section)
		organizationalUnitName_default  =
		commonName              = Your common name (eg, domain name)
		commonName_default      = www.mydomain.com
		commonName_max = 64
		[ v3_req ]
		subjectAltName=DNS:ftp.mydomain.com,DNS:blog.mydomain.com,DNS:*.mydomain.com
		# -------------- END custom sancert.cnf -----

	Notez la ligne qui commence par « subjectAltName ». Remplacez les noms de domaine répertoriés par ceux que vous souhaitez prendre en charge en plus du nom commun. Par exemple :

		subjectAltName=DNS:sales.contoso.com,DNS:support.contoso.com,DNS:fabrikam.com

	Il n’est pas nécessaire de modifier le champ commonName\_default, car vous serez invité à entrer le nom commun au cours de l’une des prochaines étapes.

2. Enregistrez le fichier __sancert.cnf__.

1. Générez une clé privée et une demande de signature de certificat à l’aide du fichier de configuration sancert.cnf. Dans un interpréteur de commandes ou une session terminal, utilisez la commande suivante :

		openssl req -new -nodes -keyout myserver.key -out server.csr -newkey rsa:2048 -config sancert.cnf

2. Lorsque vous y êtes invité, entrez les informations appropriées. Par exemple :

 		Country Name (2 letter code) []: US
        State or Province Name (full name) []: Washington
        Locality Name (eg, city) []: Redmond
        Organizational Unit Name (eg, section) []: Azure
        Your common name (eg, domain name) []: www.microsoft.com


	Une fois le processus terminé, vous devez disposer de deux fichiers : **myserver.key** et **server.csr**. Le fichier **server.csr** contient la demande de signature de certificat.

3. Envoyez votre demande de signature de certificat à une autorité de certification pour obtenir un certificat SSL. Pour obtenir la liste des autorités de certification, consultez la page [Programme de certificats racine SSL Windows et Windows Phone 8 (autorités de certification membres)][cas] dans la section Wiki de Microsoft TechNet.

4. Lorsque vous avez obtenu un certificat d’une autorité de certification, enregistrez-le sous le nom **myserver.crt**. Si l’autorité de certification a fourni le certificat au format texte, collez le texte dans le fichier **myserver.crt**. Le contenu du fichier doit être semblable à ce qui suit lorsque vous l’affichez dans un éditeur de texte :

		-----BEGIN CERTIFICATE-----
		MIIDJDCCAgwCCQCpCY4o1LBQuzANBgkqhkiG9w0BAQUFADBUMQswCQYDVQQGEwJV
		UzELMAkGA1UECBMCV0ExEDAOBgNVBAcTB1JlZG1vbmQxEDAOBgNVBAsTB0NvbnRv
		c28xFDASBgNVBAMTC2NvbnRvc28uY29tMB4XDTE0MDExNjE1MzIyM1oXDTE1MDEx
		NjE1MzIyM1owVDELMAkGA1UEBhMCVVMxCzAJBgNVBAgTAldBMRAwDgYDVQQHEwdS
		ZWRtb25kMRAwDgYDVQQLEwdDb250b3NvMRQwEgYDVQQDEwtjb250b3NvLmNvbTCC
		ASIwDQYJKoZIhvcNAQEBBQADggEPADCCAQoCggEBAN96hBX5EDgULtWkCRK7DMM3
		enae1LT9fXqGlbA7ScFvFivGvOLEqEPD//eLGsf15OYHFOQHK1hwgyfXa9sEDPMT
		3AsF3iWyF7FiEoR/qV6LdKjeQicJ2cXjGwf3G5vPoIaYifI5r0lhgOUqBxzaBDZ4
		xMgCh2yv7NavI17BHlWyQo90gS2X5glYGRhzY/fGp10BeUEgIs3Se0kQfBQOFUYb
		ktA6802lod5K0OxlQy4Oc8kfxTDf8AF2SPQ6BL7xxWrNl/Q2DuEEemjuMnLNxmeA
		Ik2+6Z6+WdvJoRxqHhleoL8ftOpWR20ToiZXCPo+fcmLod4ejsG5qjBlztVY4qsC
		AwEAATANBgkqhkiG9w0BAQUFAAOCAQEAVcM9AeeNFv2li69qBZLGDuK0NDHD3zhK
		Y0nDkqucgjE2QKUuvVSPodz8qwHnKoPwnSrTn8CRjW1gFq5qWEO50dGWgyLR8Wy1
		F69DYsEzodG+shv/G+vHJZg9QzutsJTB/Q8OoUCSnQS1PSPZP7RbvDV9b7Gx+gtg
		7kQ55j3A5vOrpI8N9CwdPuimtu6X8Ylw9ejWZsnyy0FMeOPpK3WTkDMxwwGxkU3Y
		lCRTzkv6vnHrlYQxyBLOSafCB1RWinN/slcWSLHADB6R+HeMiVKkFpooT+ghtii1
		A9PdUQIhK9bdaFicXPBYZ6AgNVuGtfwyuS5V6ucm7RE6+qf+QjXNFg==
		-----END CERTIFICATE-----

	Enregistrez le fichier .

5. À partir de la ligne de commande, d’un interpréteur de commandes ou d’une session terminal, utilisez la commande suivante pour convertir **myserver.key** et **myserver.crt** en **myserver.pfx**, le format requis par Azure App Service :

		openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt

	Lorsque vous y êtes invité, entrez un mot de passe pour sécuriser le fichier .pfx.

	> [AZURE.NOTE] Si l’autorité de certification utilise des certificats intermédiaires, vous devez les installer avant d’exporter le certificat à l'étape suivante. Ces certificats sont généralement fournis sous forme de téléchargement distinct par l’autorité de certification, dans plusieurs formats adaptés à différents types de serveurs. Sélectionnez la version fournie au format PEM (extension de fichier .pem).
	>
	> La commande suivante montre comment créer un fichier .pfx incluant des certificats intermédiaires, qui sont contenus dans le fichier **intermediate-cets.pem** :
	>
	>
	`````
	openssl pkcs12 -chain -export -out myserver.pfx -inkey myserver.key -in myserver.crt -certfile intermediate-cets.pem
	`````

	Une fois cette commande exécutée, vous devez disposer d’un fichier **myserver.pfx** utilisable avec Azure App Service.

<a name="bkmk_selfsigned"></a>
### Génération d'un certificat auto-signé (à des fins de test uniquement)

Dans certains cas, vous pouvez obtenir un certificat à des fins de test et retarder l’achat auprès d’une autorité de certification jusqu’à ce que vous arriviez à la phase de production. Les certificats auto-signés conviennent dans ce cas. Il s’agit de certificats que vous créez et signez comme si vous étiez une autorité de certification. Même si ce certificat permet de sécuriser une application, la plupart des navigateurs renvoient une erreur lors de l’accès à l’application, car le certificat n’a pas été signé par une autorité de certification approuvée. Il se peut même que certains navigateurs refusent d’afficher l’application.

- [Génération d’un certificat auto-signé à l’aide de makecert](#bkmk_ssmakecert)
- [Génération d’un certificat auto-signé à l’aide d’OpenSSL](#bkmk_ssopenssl)

<a name="bkmk_ssmakecert"></a>
#### Génération d’un certificat auto-signé à l’aide de makecert ####

Pour créer un certificat de test à partir du système Windows sur lequel Visual Studio est installé, procédez comme suit :

1. À partir du **menu Démarrer** ou de l’**écran d’accueil**, recherchez **Invite de commandes développeur**. Pour terminer, cliquez avec le bouton droit sur **Invite de commandes développeur** et sélectionnez **Exécuter en tant qu’administrateur**.

	Si la boîte de dialogue du contrôle de compte d’utilisateur s’ouvre, sélectionnez **Oui** pour continuer.

2. Dans l’invite de commandes développeur, utilisez la commande suivante pour créer un certificat auto-signé. Vous devez remplacer **serverdnsname** par le DNS de votre application.

		makecert -r -pe -b 01/01/2013 -e 01/01/2014 -eku 1.3.6.1.5.5.7.3.1 -ss My -n CN=serverdnsname -sky exchange -sp "Microsoft RSA SChannel Cryptographic Provider" -sy 12 -len 2048

	Cette commande crée un certificat valide entre le 01/01/2013 et le 01/01/2014. Elle stocke l’emplacement du magasin de certificats CurrentUser.

3. À partir du **menu Démarrer** ou de l’**écran d’accueil**, recherchez **Windows PowerShell** et lancez l’application.

4. Dans l’invite de commandes Windows PowerShell, utilisez les commandes suivantes pour exporter le certificat créé précédemment :

		$mypwd = ConvertTo-SecureString -String "password" -Force -AsPlainText
		get-childitem cert:\currentuser\my -dnsname serverdnsname | export-pfxcertificate -filepath file-to-export-to.pfx -password $mypwd

	Cela permet de stocker le mot de passe spécifié en tant que chaîne sécurisée dans $mypwd, de rechercher le certificat à l’aide du nom DNS spécifié par le paramètre **dnsname**, puis d’effectuer un export vers le fichier spécifié par le paramètre **filepath**. La chaîne sécurisée contenant le mot de passe permet de sécuriser le fichier exporté.

<a name="bkmk_ssopenssl"></a>
####Génération d’un certificat auto-signé à l’aide d’OpenSSL ####

1. Créez un document nommé **serverauth.cnf** en utilisant le contenu suivant :

        [ req ]
        default_bits           = 2048
        default_keyfile        = privkey.pem
        distinguished_name     = req_distinguished_name
        attributes             = req_attributes
        x509_extensions        = v3_ca

        [ req_distinguished_name ]
        countryName			= Country Name (2 letter code)
        countryName_min			= 2
        countryName_max			= 2
        stateOrProvinceName		= State or Province Name (full name)
        localityName			= Locality Name (eg, city)
        0.organizationName		= Organization Name (eg, company)
        organizationalUnitName		= Organizational Unit Name (eg, section)
        commonName			= Common Name (eg, your app's domain name)
        commonName_max			= 64
        emailAddress			= Email Address
        emailAddress_max		= 40

        [ req_attributes ]
        challengePassword		= A challenge password
        challengePassword_min		= 4
        challengePassword_max		= 20

        [ v3_ca ]
         subjectKeyIdentifier=hash
         authorityKeyIdentifier=keyid:always,issuer:always
         basicConstraints = CA:false
         keyUsage=nonRepudiation, digitalSignature, keyEncipherment
         extendedKeyUsage = serverAuth

	Ceci permet de spécifier les paramètres de configuration nécessaires à la génération d’un certificat SSL pouvant être utilisé par Azure App Service.

2. Générez un nouveau certificat auto-signé en utilisant la commande suivante dans une ligne de commande, un interpréteur de commandes ou une session terminal :

		openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout myserver.key -out myserver.crt -config serverauth.cnf

	Ceci permet de créer un certificat à l’aide des paramètres de configuration spécifiés dans le fichier **serverauth.cnf**.

3. Pour exporter le certificat dans un fichier .PFX pouvant être téléchargé sur une application dans Azure App Service, utilisez la commande suivante :

		openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt

	Lorsque vous y êtes invité, entrez un mot de passe pour sécuriser le fichier .pfx.

	Le fichier **myserver.pfx** généré par cette commande permet de sécuriser votre application à des fins de test.

<a name="bkmk_standardmode"></a>
## 2\. Configuration d’un niveau tarifaire Standard ou Premium

L’activation du protocole HTTPS pour un domaine personnalisé est disponible uniquement avec les niveaux tarifaires **Standard** et **Premium** dans Azure App Service. Procédez comme suit pour basculer votre plan App Service vers le niveau tarifaire **Standard**.

> [AZURE.NOTE] Avant de basculer une application du niveau **Gratuit** au niveau **Standard**, vous devez supprimer les limites de dépense mises en place pour l’abonnement de votre application, sans quoi cette dernière risque d’être indisponible si vous atteignez la limite avant la fin de la période de facturation. Pour plus d’informations sur les niveaux tarifaires Partagé et **Standard**, consultez la page [Tarification][pricing].

1.	Dans votre navigateur, ouvrez le [portail Azure](https://portal.azure.com).
	
2.	Cliquez sur l’option **App Service** sur le côté gauche de la page.

4.	Cliquez sur le nom de votre application.

5.	Dans la page **Essentials**, cliquez sur **Paramètres**.

6.	Cliquez sur **Monter en puissance**.
	
	![L’onglet Mettre à l’échelle][scale]

7.	Dans la section **Monter en puissance**, définissez le mode de plan App Service en cliquant sur **Sélectionner**.

	> [AZURE.NOTE] Si un message indiquant une erreur de mise à l’échelle pour l’application web &lt;nom de l’application&gt; s’affiche, vous pouvez cliquer sur le bouton de détails pour obtenir plus d’informations. Une erreur indiquant que le nombre de serveurs d’instances disponibles est insuffisant pour répondre à la demande peut s’afficher. Dans ce cas, contactez le [support technique Azure](/support/options/).

<a name="bkmk_configuressl"></a>
## 3\. Configuration de SSL dans votre application

Avant de suivre les étapes de cette section, vous devez avoir associé un nom de domaine personnalisé à votre application. Pour plus d’informations, consultez la page [Configuration d’un nom de domaine personnalisé pour une application web][customdomain].

1.	Dans votre navigateur, ouvrez le [portail Azure](https://portal.azure.com).

2.	Cliquez sur l’option **App Service** sur le côté gauche de la page.

4.	Cliquez sur le nom de votre application.

5.	Dans la page **Essentials**, cliquez sur **Paramètres**.

6.	Cliquez sur **Domaines personnalisés et SSL**.

	![Onglet Configurer][configure]

7.	Dans la section **Certificats**, cliquez sur **Charger**.

8.	Dans la boîte de dialogue **Upload a certificate**, sélectionnez le fichier de certificat .pfx créé plus tôt à l’aide du gestionnaire des services Internet ou d’OpenSSL. Spécifiez le mot de passe utilisé pour sécuriser le fichier .pfx, le cas échéant. Pour finir, cliquez sur **Enregistrer** pour charger le certificat.

	![téléchargement SSL][uploadcert]

9. Dans la section **Liaisons SSL** de l’onglet **Paramètres SSL**, utilisez les listes déroulantes pour sélectionner le nom de domaine à sécuriser avec le chiffrement SSL, et le certificat à utiliser. Vous pouvez également indiquer si vous utilisez l’extension [SNI (Indication du nom du serveur)][sni] ou le protocole SSL basé sur IP.

	![liaisons SSL][sslbindings]

	* Le protocole SSL basé sur IP associe un certificat à un nom de domaine en mappant l’adresse IP publique dédiée du serveur au nom de domaine. Chaque nom de domaine (contoso.com, fabricam.com, etc.) associé à votre service doit donc posséder une adresse IP dédiée. Il s’agit de la méthode classique permettant d’associer des certificats SSL à un serveur Web.

	* Le protocole SSL basé sur SNI une extension des protocoles SSL et [TLS (Transport Layer Security)][tls] qui permet à plusieurs domaines de partager la même adresse IP, avec des certificats de sécurité distincts pour chaque domaine. La plupart des navigateurs modernes (dont Internet Explorer, Chrome, Firefox et Opera) prennent en charge SNI. Il se peut toutefois que les navigateurs plus anciens ne proposent pas cette prise en charge. Pour plus d’informations sur l’extension SNI, consultez l’[article Wikipédia associé][sni].

10. Cliquez sur **Save** pour enregistrer les modifications et activer SSL.

> [AZURE.NOTE] Si vous avez sélectionné **SSL basé sur IP** et que votre domaine personnalisé est configuré à l’aide d’un enregistrement A, vous devez effectuer les étapes supplémentaires suivantes :
>
> 1. Une fois la liaison SSL basée sur IP configurée, une adresse IP dédiée est attribuée à votre application. Celle-ci figure dans la page **Tableau de bord** de votre application, dans la section **Aperçu rapide**. Elle est répertoriée avec la mention **Adresse IP virtuelle** :
>    
>     ![Adresse IP virtuelle](./media/configure-ssl-web-site/staticip.png)
>    
>     Cette adresse IP est différente de celle utilisée précédemment pour configurer l’enregistrement A de votre domaine. Si vous utilisez le protocole SSL basé sur SNI ou que vous n’utilisez pas SSL, aucune adresse n’est indiquée pour cette entrée.
>
> 2. À l’aide des outils fournis par votre bureau d’enregistrement, modifiez l’enregistrement A de votre nom de domaine personnalisé de manière à ce qu’il pointe vers l’adresse IP spécifiée lors de l’étape précédente.


À ce stade, vous devez être en mesure de visiter votre application en utilisant `HTTPS://` à la place de `HTTP://` pour vérifier que le certificat a été configuré correctement.

<a name="bkmk_enforce"></a>
## 4\. Exécution de SSL sur votre application

Azure App Service n’applique *pas* le protocole HTTPS. Les visiteurs peuvent toujours accéder à votre application en utilisant le protocole HTTP, ce qui peut compromettre la sécurité de votre application. Si vous souhaitez appliquer le protocole HTTPS pour votre application, vous pouvez utiliser le **module de réécriture d’URL**. Ce module est inclus à Azure App Service et vous permet de définir des règles qui sont appliquées aux requêtes entrantes avant qu’elles ne soient transmises à votre application. **Il peut être utilisé pour les applications écrites dans n’importe quel langage de programmation pris en charge par Azure.**

> [AZURE.NOTE] Les applications .NET MVC doivent utiliser le filtre [RequireHttps](http://msdn.microsoft.com/library/system.web.mvc.requirehttpsattribute.aspx) plutôt que le module de réécriture d’URL. Pour plus d’informations sur l’utilisation du filtre RequireHttps, consultez la page [Déployer une application ASP.NET MVC 5 vers une application web](../articles/app-service-web/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md).
>
> Pour plus d’informations sur la redirection par programme des requêtes utilisant d’autres langages et infrastructures de programmation, consultez la documentation de ces technologies.

Les règles de réécriture d'URL sont définies dans un fichier **web.config** stocké à la racine de votre application. L’exemple suivant contient une règle de réécriture d’URL basique qui force tout le trafic entrant à utiliser HTTPS.

<a name="example"></a>**Exemple de fichier web.config de réécriture d’URL**

	<?xml version="1.0" encoding="UTF-8"?>
	<configuration>
	  <system.webServer>
	    <rewrite>
	      <rules>
	        <rule name="Force HTTPS" enabled="true">
	          <match url="(.*)" ignoreCase="false" />
	          <conditions>
	            <add input="{HTTPS}" pattern="off" />
	          </conditions>
	          <action type="Redirect" url="https://{HTTP_HOST}/{R:1}" appendQueryString="true" redirectType="Permanent" />
	        </rule>
	      </rules>
	    </rewrite>
	  </system.webServer>
	</configuration>

Cette règle fonctionne en renvoyant le code d’état HTTP 301 (redirection permanente) lorsque l’utilisateur demande une page utilisant le protocole HTTP. Le code 301 redirige la demande vers la même URL que celle requise par le visiteur, mais remplace la partie HTTP de la demande par HTTPS. Par exemple, HTTP://contoso.com est redirigé vers HTTPS://contoso.com.

> [AZURE.NOTE] Si votre application est écrite en **Node.js**, **PHP**, **Python Django** ou **Java**, elle n’inclut probablement pas de fichier web.config. Toutefois, **Node.js**, **Python Django** et **Java** utilisent tous un fichier web.config lorsqu’ils sont hébergés dans Azure App Service. Comme Azure crée automatiquement le fichier pendant le déploiement, vous ne le voyez jamais. Si vous en incluez un comme élément de votre application, il écrasera celui qu’Azure génère automatiquement.

###.NET

Pour les applications .NET, modifiez le fichier web.config de votre application et ajoutez la section **&lt;rewrite>** de l’[exemple](#example) de la section **&lt;system.WebServer>**.

Si votre fichier web.config contient déjà une section **&lt;rewrite>**, ajoutez l’entrée **&lt;rule>** de l’[exemple](#example) comme première entrée dans la section **&lt;rules>**.

###PHP

Pour les applications PHP, enregistrez simplement l’[exemple](#example) comme fichier web.config à la racine de votre application, puis redéployez l’application vers votre application.

###Node.js, Python Django et Java

Un fichier web.config est automatiquement créé pour les applications Node.js, Python Django et Java s'ils n'en fournissent pas déjà un, mais il existe uniquement sur le serveur puisqu'il est créé pendant le déploiement. Le fichier généré automatiquement contient des paramètres qui disent à Azure comment héberger votre application.

Pour récupérer et modifier le fichier auto-généré à partir de l’application, procédez comme suit :

1. Téléchargez le fichier via FTP (consultez la page [Chargement/téléchargement de fichiers sur FTP et collecte de journaux de diagnostic](http://blogs.msdn.com/b/avkashchauhan/archive/2012/06/19/windows-azure-website-uploading-downloading-files-over-ftp-and-collecting-diagnostics-logs.aspx)).

2. Ajoutez-le à la racine de votre application.

3. Ajoutez les règles de réécriture à l’aide des informations suivantes.

	* **Node.js et Python Django**

		Le fichier web.config généré pour les applications Node.js et Python Django contient déjà une section **&lt;rewrite>**, contenant les entrées **&lt;rule>** requises pour le bon fonctionnement de l’application. Pour forcer l’application à utiliser HTTPS, ajoutez la règle **&lt;rule>** de l’exemple comme première entrée de la section **&lt;rules>**. Cette opération forcera HTTPS, tout en laissant les autres règles intactes.

	* **Java**

		Comme le fichier web.config des applications Java utilisant Apache Tomcat ne contient pas de section **&lt;rewrite>**, vous devez ajouter la section **&lt;rewrite>** de l’exemple à la section **&lt;system.webServer>**.

4. Redéployez le projet (en incluant le fichier web.config mis à jour) vers Azure

Une fois que vous avez déployé un fichier web.config avec une règle de réécriture pour forcer HTTPS, il doit prendre effet immédiatement et rediriger toutes les demandes vers HTTPS.

Pour plus d'informations sur le module Réécriture d'URL d'IIS, consultez la documentation sur la [Réécriture d'URL](http://www.iis.net/downloads/microsoft/url-rewrite).

## Autres ressources ##
- [Centre de gestion de la confidentialité Microsoft Azure](/support/trust-center/security/)
- [Options de configuration déverrouillées dans Sites Web Azure](/blog/2014/01/28/more-to-explore-configuration-options-unlocked-in-windows-azure-web-sites/)
- [Activer la journalisation des diagnostics](../articles/app-service-web/web-sites-enable-diagnostic-log.md)
- [Configurer des applications web dans Azure App Service](../articles/app-service-web/web-sites-configure.md)
- [Portail de gestion Azure](https://manage.windowsazure.com)

>[AZURE.NOTE] Si vous voulez vous familiariser avec Azure App Service avant d’ouvrir un compte Azure, accédez à la page [Essayer App Service](http://go.microsoft.com/fwlink/?LinkId=523751), où vous pourrez créer immédiatement une application temporaire dans App Service. Aucune carte de crédit n’est requise ; vous ne prenez aucun engagement.

## Changements apportés
* Pour obtenir un guide présentant les modifications apportées dans le cadre de la transition entre Sites Web et App Service, consultez la page [Azure App Service et les services Azure existants](http://go.microsoft.com/fwlink/?LinkId=529714).
* Pour obtenir un guide présentant les modifications apportées dans le cadre de la transition entre l’ancien et le nouveau portail, consultez la page [Références sur la navigation dans le portail Azure](http://go.microsoft.com/fwlink/?LinkId=529715).

[customdomain]: ../articles/app-service-web/web-sites-custom-domain-name.md
[iiscsr]: http://technet.microsoft.com/library/cc732906(WS.10).aspx
[cas]: http://go.microsoft.com/fwlink/?LinkID=269988
[installcertiis]: http://technet.microsoft.com/library/cc771816(WS.10).aspx
[exportcertiis]: http://technet.microsoft.com/library/cc731386(WS.10).aspx
[openssl]: http://www.openssl.org/
[portal]: https://manage.windowsazure.com/
[tls]: http://en.wikipedia.org/wiki/Transport_Layer_Security
[staticip]: ./media/configure-ssl-web-site/staticip.png
[website]: ./media/configure-ssl-web-site/sslwebsite.png
[scale]: ./media/configure-ssl-web-site/sslscale.png
[standard]: ./media/configure-ssl-web-site/sslreserved.png
[pricing]: /pricing/details/
[configure]: ./media/configure-ssl-web-site/sslconfig.png
[uploadcert]: ./media/configure-ssl-web-site/ssluploadcert.png
[uploadcertdlg]: ./media/configure-ssl-web-site/ssluploaddlg.png
[sslbindings]: ./media/configure-ssl-web-site/sslbindings.png
[sni]: http://en.wikipedia.org/wiki/Server_Name_Indication
[certmgr]: ./media/configure-ssl-web-site/waws-certmgr.png
[certwiz1]: ./media/configure-ssl-web-site/waws-certwiz1.png
[certwiz2]: ./media/configure-ssl-web-site/waws-certwiz2.png
[certwiz3]: ./media/configure-ssl-web-site/waws-certwiz3.png
[certwiz4]: ./media/configure-ssl-web-site/waws-certwiz4.png

<!---HONumber=AcomDC_0211_2016-->