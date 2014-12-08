#Activation du protocole HTTPS pour un site web Azure

> [WACOM.NOTE]
> Une prise en main plus rapide grâce à la NOUVELLE [procédure pas à pas](http://support.microsoft.com/kb/2990804) d'Azure !  Grâce à elle, l'association d'un nom de domaine personnalisé ET la sécurisation des communications (SSL) avec Azure Cloud Services ou Sites Web Azure deviennent un jeu d'enfants.

Vous pouvez sécuriser les communications entre le site web et le navigateur avec le protocole HTTPS, qui utilise le chiffrement SSL (Secure Socket Layer). Il s'agit de la méthode la plus courante en matière de sécurisation des données envoyées via Internet. Elle permet de garantir aux visiteurs que les transactions qu'ils réalisent sur votre site sont sécurisées. Cet article décrit comment configurer le protocole HTTPS pour un site web Azure. 

<a href="bkmk_azurewebsites"></a><h2>HTTPS pour le domaine \*.azurewebsites.net</h2>

Si vous n'envisagez pas d'utiliser un nom de domaine personnalisé, mais le domaine *.azurewebsites.net attribué à votre site web par Azure (par exemple, contoso.azurewebsites.net), le protocole HTTPS est déjà activé sur votre site, avec un certificat fourni par Microsoft. Vous pouvez utiliser **https://mywebsite.azurewebsites.net** pour accéder à votre site. Cependant, \*.azurewebsites.net est un domaine générique. Comme [tous les domaines génériques](https://casecurity.org/2014/02/26/pros-and-cons-of-single-domain-multi-domain-and-wildcard-certificates/), il n'est pas aussi sécurisé qu'un domaine personnalisé avec votre propre certificat. 

Le reste de ce document fournit des informations sur l'activation du protocole HTTPS pour les domaines personnalisés, tels que **contoso.com**, **www.contoso.com** ou **\*.contoso.com**

<a href="bkmk_domainname"></a><h2>Activation de SSL pour votre domaine personnalisé</h2>

Pour activer le protocole HTTPS pour un domaine personnalisé tel que **contoso.com**, vous devez inscrire ce dernier auprès d'un bureau d'enregistrement des noms de domaine. Pour plus d'informations sur la configuration du nom de domaine d'un site web Azure, consultez la page [Configuration d'un nom de domaine personnalisé pour un site web Azure](/fr-fr/develop/net/common-tasks/custom-dns-web-site/). Une fois que vous avez inscrit un nom de domaine personnalisé et configuré votre site web de manière à ce qu'il réponde au nom personnalisé, vous devez demander un certificat SSL pour le domaine. 

> [WACOM.NOTE] Pour activer le protocole HTTPS pour les noms de domaine personnalisés, vous devez configurer votre site web pour le mode de plan d'hébergement web **standard**. Des frais supplémentaires peuvent s'appliquer si vous utilisez actuellement le mode gratuit ou partagé. Pour plus d'informations sur la tarification en mode **Standard** ou Partagé, consultez [Détails de la tarification][pricing]. 

Une fois que vous avez un domaine personnalisé valide, vous devez suivre les étapes suivantes pour activer le protocole HTTPS pour votre site web :

1. [Obtention d'un certificat SSL](#bkmk_getcert)
1. [Configuration du mode standard](#bkmk_standardmode)
1. [Configuration du chiffrement SSL](#bkmk_configuressl)
1. [Application du protocole HTTPS sur votre site web Azure](#bkmk_enforce)

<a href="bkmk_getcert"></a><h2>Obtention d'un certificat SSL</h2>

Avant de demander un certificat SSL, vous devez déterminer les noms de domaine qui seront sécurisés par celui-ci, et par conséquent le type de certificat à demander. Si vous voulez sécuriser un seul nom de domaine tel que **contoso.com** ou **www.contoso.com**, un certificat de base est suffisant. Si vous avez besoin de sécuriser plusieurs noms de domaine, par exemple **contoso.com**, **www.contoso.com** et **mail.contoso.com**, vous pouvez obtenir un [certificat générique](http://en.wikipedia.org/wiki/Wildcard_certificate) ou un certificat avec un [autre nom de sujet](http://en.wikipedia.org/wiki/SubjectAltName) (subjectAltName).

Les certificats SSL utilisés avec Sites Web Azure doivent être signés par une [autorité de certification](http://en.wikipedia.org/wiki/Certificate_authority). Si vous n'en possédez pas, vous devez en obtenir un auprès d'une société qui émet des certificats SSL. Pour obtenir la liste des autorités de certification, consultez la page [Programme de certificats racine SSL Windows et Windows Phone 8 (autorités de certification membres)][cas] dans la section Wiki de Microsoft TechNet.

Le certificat SSL doit répondre aux prérequis suivants dans Azure :

* Le certificat doit contenir une clé privée.
* Le certificat doit être créé pour l'échange de clés et pouvoir faire l'objet d'un export au format Personal Information Exchange (.pfx).
* Le nom du sujet du certificat doit correspondre au domaine servant à accéder au site web. Si vous voulez gérer plusieurs domaines avec ce certificat, vous devez utiliser un caractère générique ou spécifier d'autres noms de sujet, comme indiqué précédemment.
* Le certificat doit utiliser au minimum un chiffrement à 2 048 bits.
* Les certificats émis à partir de serveurs d'autorité de certification privés ne sont pas pris en charge par Sites Web Azure.

Pour obtenir un certificat SSL à utiliser avec Sites Web Azure, vous soumettez une demande de signature de certificat à une autorité de certification, puis générez un fichier .pfx à partir du certificat que vous recevez en retour. Vous pouvez effectuer cette opération à l'aide de l'outil de votre choix. Voici quelques méthodes courantes d'obtention d'un certificat :

- [Obtention d'un certificat à l'aide de Certreq.exe](#bkmk_certreq)
- [Obtention d'un certificat à l'aide du Gestionnaire des services Internet](#bkmk_iismgr)
- [Obtention d'un certificat à l'aide d'OpenSSL](#bkmk_openssl)
- [Obtention d'un certificat SubjectAltName à l'aide d'OpenSSL](#bkmk_subjectaltname)
- [Génération de certificats auto-signés (à des fins de test uniquement)](#bkmk_selfsigned) 

> [WACOM.NOTE] Lors des étapes, vous devrez entrer un **nom commun**, tel que " www.contoso.com ". Pour les certificats génériques, cette valeur doit être \*.nomdedomaine (par exemple, \*.contoso.com). Pour prendre en charge un nom générique tel que \*.contoso.com et un nom de domaine racine tel que contoso.com, vous pouvez utiliser un certificat générique subjectAltName.

> [WACOM.NOTE] Les certificats ECC (chiffrement à courbe elliptique) sont pris en charge par Sites Web Azure. Cependant, ils sont relativement nouveaux, et la procédure précise à suivre pour créer la demande de signature de certificat doit être déterminée avec votre autorité de certification.

Vous devrez peut-être obtenir des **[certificats intermédiaires](http://en.wikipedia.org/wiki/Intermediate_certificate_authorities)** (également connus sous le nom de certificats de chaîne), si ceux-ci sont utilisés par l'autorité de certification. Les certificats intermédiaires étant plus sécurisés que les certificats sans chaîne, les autorités de certification les utilisent fréquemment. Ils sont souvent fournis sous forme de téléchargement par le site web de l'autorité de certification. Les étapes décrites dans cet article permettent de s'assurer que les certificats intermédiaires sont fusionnés avec ceux téléchargés sur votre site web Azure. 

<a href="bkmk_certreq"></a>
###Obtention d'un certificat à l'aide de Certreq.exe (Windows uniquement)

Certreq.exe est un utilitaire Windows de création de demandes de certificat. Il fait partie de l'installation Windows de base depuis Windows XP/Windows Server 2000 et doit être disponible sur les systèmes Windows récents. Suivez les étapes ci-dessous pour obtenir un certificat SSL à l'aide de certreq.exe.

1. Ouvrez le **bloc-notes** et créez un document contenant les éléments suivants. Dans la ligne Subject, remplacez **mysite.com** par le nom de domaine personnalisé de votre site web. Par exemple, Subject = "CN=www.contoso.com".

		[NewRequest]
		Subject = "CN=mysite.com"
		Exportable = TRUE
		KeyLength = 2048
		KeySpec = 1
		KeyUsage = 0xA0
		MachineKeySet = True
		ProviderName = "Microsoft RSA SChannel Cryptographic Provider"
		ProviderType = 12
		RequestType = CMC

		[EnhancedKeyUsageExtension]
		OID=1.3.6.1.5.5.7.3.1

	Pour plus d'informations sur les options spécifiées plus haut, ainsi que sur les autres options disponibles, consultez la [documentation de référence Certreq](http://technet.microsoft.com/library/cc725793.aspx).

2. Enregistrez le fichier texte sous le nom **myrequest.txt**.

3. Depuis l'**écran d'accueil** ou le **menu Démarrer**, exécutez **cmd.exe**.

4. À l'invite de commandes, utilisez la commande suivante pour créer le fichier de demande de certificat :

		certreq -new \path\to\myrequest.txt \path\to\create\myrequest.csr

	Spécifiez le chemin d'accès au fichier **myrequest.txt** créé à l'étape 1, ainsi que celui utilisé lors de la création du fichier **myrequest.csr**.

5. Envoyez le fichier **myrequest.csr** à une autorité de certification pour obtenir un certificat SSL. Pour cela, vous devrez peut-être télécharger le fichier ou l'ouvrir dans le bloc-notes et coller son contenu directement dans un formulaire web.

	Pour obtenir la liste des autorités de certification, consultez la page [Programme de certificats racine SSL Windows et Windows Phone 8 (autorités de certification membres) ][cas] dans la section Wiki de Microsoft TechNet.

6. Une fois que l'autorité de certification vous a fourni un fichier de certificat (.CER); enregistrez-le sur l'ordinateur qui a généré la demande, puis utilisez la commande suivante pour accepter la demande et achever le processus de génération du certificat.

		certreq -accept -user mycert.cer

	Dans ce cas, le certificat **mycert.cer** reçu de l'autorité de certification servira à obtenir la signature du certificat. Aucun fichier n'est créé. Au lieu de cela, le certificat est stocké dans le magasin de certificats Windows.

6. Si l'autorité de certification utilise des certificats intermédiaires, vous devez les installer avant d'exporter le certificat lors des prochaines étapes. Ces certificats sont généralement fournis sous forme de téléchargement distinct par l'autorité de certification, dans plusieurs formats adaptés à différents types de serveurs. Sélectionnez la version fournie pour Microsoft IIS.

	Une fois le certificat téléchargé, cliquez dessus avec le bouton droit dans l'explorateur, puis sélectionnez **Installer le certificat**. Utilisez les valeurs par défaut de l'**Assistant Importation de certificat**, puis sélectionnez **Suivant** jusqu'à ce que l'importation soit terminée.

7. Pour exporter le certificat à partir du magasin de certificats, exécutez **certmgr.msc** à partir de l'**écran d'accueil** ou du **menu Démarrer**. Lorsque le **gestionnaire de certificats** apparaît, développez le dossier **Personnel**, puis sélectionnez **Certificats**. Dans le champ **Délivré à**, recherchez l'entrée correspondant au nom de domaine personnalisé pour lequel vous avez demandé un certificat. Le champ **Délivré par** doit répertorier l'autorité de certification utilisée pour ce certificat.

	![insert image of cert manager here][certmgr]

9. Cliquez avec le bouton droit sur le certificat, sélectionnez **Toutes les tâches**, puis **Exporter**. Dans l'**Assistant Exportation de certificat**, cliquez sur **Suivant**, puis sélectionnez **Oui, exporter la clé privée**. Cliquez sur **Suivant**.

	![Export the private key][certwiz1]

10. Sélectionnez **Échange d'informations personnelles - PKCS #12**, **Inclure tous les certificats dans la chaîne de certificats** et **Exporter toutes les propriétés étendues**. Cliquez sur **Suivant**.

	![include all certs and extended properties][certwiz2]

11. Sélectionnez **Mot de passe**, puis entrez et confirmez le mot de passe. Cliquez sur **Suivant**.

	![specify a password][certwiz3]

12. Indiquez le chemin d'accès et le nom de fichier contenant le certificat exporté. Le nom de fichier doit porter l'extension **.pfx**. Cliquez sur **Suivant** pour terminer le processus.

	![provide a file path][certwiz4]

Vous pouvez maintenant télécharger le fichier PFX exporté sur votre site web Azure.

<a href="bkmk_openssl"></a>
###Obtention d'un certificat à l'aide d'OpenSSL

1. Générez une clé privée et une demande de signature de certificat en utilisant la commande suivante dans une ligne de commande, un interpréteur de commandes ou une session terminal :

		openssl req -new -nodes -keyout myserver.key -out server.csr -newkey rsa:2048

2. Lorsque vous y êtes invité, entrez les informations appropriées. Par exemple :

 		Country Name (2 letter code) 
        State or Province Name (full name) []: Washington
        Locality Name (eg, city) []: Redmond
        Organization Name (eg, company) []: Microsoft
        Organizational Unit Name (eg, section) []: Azure
        Common Name (eg, YOUR name) []: www.microsoft.com
        Email Address []:

		Entrez les attributs supplémentaires suivants à envoyer avec votre demande de certificat

       	Un mot de passe de stimulation []: 

	Une fois le processus terminé, vous devez disposer de deux fichiers : **myserver.key** et **server.csr**. Le fichier **server.csr** contient la demande de signature de certificat.

3. Envoyez votre demande de signature de certificat à une autorité de certification pour obtenir un certificat SSL. Pour obtenir la liste des autorités de certification, consultez la page [Programme de certificats racine SSL Windows et Windows Phone 8 (autorités de certification membres)][cas] dans la section Wiki de Microsoft TechNet.

4. Une fois que vous avez obtenu un certificat d'une autorité de certification, enregistrez-le sous le nom **myserver.crt**. Si l'autorité de certification a fourni le certificat au format texte, collez le texte dans le fichier **myserver.crt**. Le contenu du fichier doit être semblable à ce qui suit lorsque vous l'affichez dans un éditeur de texte :

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

	Enregistrez le fichier.

5. À partir de la ligne de commande, un interpréteur de commandes ou une session terminal, utilisez la commande suivante pour convertir **myserver.key** et **myserver.crt** en **myserver.pfx**, le format requis par Sites Web Azure :

		openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt

	Lorsque vous y êtes invité, entrez un mot de passe pour sécuriser le fichier .pfx.

	<div class="dev-callout"> 
	<b>Remarque</b>
	<p>Si l'autorité de certification utilise des certificats intermédiaires, vous devez les installer avant d'exporter le certificat à l'étape suivante. Ces certificats sont généralement fournis sous forme de téléchargement distinct par l'autorité de certification, dans plusieurs formats adaptés à différents types de serveurs. Sélectionnez la version fournie au format PEM (extension de fichier .pem).</p>
	<p>La commande suivante montre comment créer un fichier .pfx incluant des certificats intermédiaires, qui sont contenus dans le fichier <b>intermediate-cets.pem</b> :</p>
	<pre><code>
	openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt -certfile intermediate-cets.pem
	</code></pre>
	</div>

	Une fois la commande exécutée, vous devez disposer d'un fichier **myserver.pfx** utilisable avec Sites Web Azure.

###<a name="bkmk_iismgr"></a>Obtention d'un certificat à l'aide du Gestionnaire des services Internet

Si vous savez utiliser le Gestionnaire des services Internet, vous pouvez vous en servir pour générer un certificat utilisable avec Sites Web Azure.

1. Générez une demande de signature de certificat avec le Gestionnaire des services Internet pour l'envoyer à l'autorité de certification. Pour plus d'informations sur la génération d'une demande de signature de certificat, consultez la page [Demander un certificat de serveur Internet (IIS 7)][iiscsr].

2. Envoyez votre demande de signature de certificat à une autorité de certification pour obtenir un certificat SSL. Pour obtenir la liste des autorités de certification, consultez la page [Programme de certificats racine SSL Windows et Windows Phone 8 (autorités de certification membres)][cas] dans la section Wiki de Microsoft TechNet.

3. Complétez la demande de signature de certificat avec le certificat fourni par l'autorité de certification. Pour plus d'informations sur la génération d'une demande de signature de certificat, consultez la page [Installer un certificat de serveur Internet (IIS 7)][installcertiis].

4. Si l'autorité de certification utilise des certificats intermédiaires, vous devez les installer avant d'exporter le certificat à l'étape suivante. Ces certificats sont généralement fournis sous forme de téléchargement distinct par l'autorité de certification, dans plusieurs formats adaptés à différents types de serveurs. Sélectionnez la version fournie pour Microsoft IIS.

	Une fois le certificat téléchargé, cliquez dessus avec le bouton droit dans l'explorateur, puis sélectionnez **Installer le certificat**. Utilisez les valeurs par défaut de l'**Assistant Importation de certificat**, puis sélectionnez **Suivant** jusqu'à ce que l'importation soit terminée.

4. Exportez le certificat à partir du Gestionnaire des services Internet. Pour plus d'informations sur l'exportation du certificat, consultez la page [Exporter un certificat de serveur (IIS 7)][exportcertiis]. Le fichier exporté sera téléchargé ultérieurement dans Azure pour être utilisé avec votre site web Azure.

	<div class="dev-callout"> 
	<b>Remarque</b>
	<p>Lors de l'exportation, veillez à sélectionner l'option <strong>Oui, exporter la clé privée</strong>, qui permet d'inclure la clé privée dans le certificat exporté.</p>
	</div>

	<div class="dev-callout"> 
	<b>Remarque</b>
	<p>Lors de l'exportation, veillez à sélectionner les options <strong>Inclure tous les certificats dans le chemin d'accès de certification</strong> et <strong>Exporter toutes les propriétés étendues</strong>. Ainsi, les certificats intermédiaires seront inclus dans le certificat exporté.</p>
	</div>


###<a href="bkmk_subjectaltname"></a>Obtention d'un certificat SubjectAltName à l'aide d'OpenSSL

OpenSSL permet de créer une demande de certificat qui utilise l'extension SubjectAltName pour prendre en charge plusieurs noms de domaine avec un seul certificat mais, pour cela, nécessite un fichier de configuration. Les étapes suivantes permettent de créer un fichier de configuration, puis de l'utiliser pour demander un certificat.

1. Créez un fichier nommé __sancert.cnf__ et utilisez le contenu suivant :
 
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

	Notez la ligne qui commence par " subjectAltName ". Remplacez les noms de domaine répertoriés par ceux que vous souhaitez prendre en charge en plus du nom commun. Par exemple :

		subjectAltName=DNS:sales.contoso.com,DNS:support.contoso.com,DNS:fabrikam.com

	Il n'est pas nécessaire de modifier le champ commonName_default, car vous serez invité à entrer le nom commun au cours de l'une des prochaines étapes.

2. Enregistrez le fichier __sancert.cnf__.

1. Générez une clé privée et une demande de signature de certificat à l'aide du fichier de configuration sancert.cnf. Dans un interpréteur de commandes ou une session terminal, utilisez la commande suivante :

		openssl req -new -nodes -keyout myserver.key -out server.csr -newkey rsa:2048 -config sancert.cnf

2. Lorsque vous y êtes invité, entrez les informations appropriées. Par exemple :

 		Country Name (2 letter code) []: US
        State or Province Name (full name) []: Washington
        Locality Name (eg, city) []: Redmond
        Organizational Unit Name (eg, section) []: Azure
        Your common name (eg, domain name) []: www.microsoft.com
 

	Une fois le processus terminé, vous devez disposer de deux fichiers : **myserver.key** et **server.csr**. Le fichier **server.csr** contient la demande de signature de certificat.

3. Envoyez votre demande de signature de certificat à une autorité de certification pour obtenir un certificat SSL. Pour obtenir la liste des autorités de certification, consultez la page [Programme de certificats racine SSL Windows et Windows Phone 8 (autorités de certification membres)][cas] dans la section Wiki de Microsoft TechNet.

4. Une fois que vous avez obtenu un certificat d'une autorité de certification, enregistrez-le sous le nom **myserver.crt**. Si l'autorité de certification a fourni le certificat au format texte, collez le texte dans le fichier **myserver.crt**. Le contenu du fichier doit être semblable à ce qui suit lorsque vous l'affichez dans un éditeur de texte :

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

	Enregistrez le fichier.

5. À partir de la ligne de commande, un interpréteur de commandes ou une session terminal, utilisez la commande suivante pour convertir **myserver.key** et **myserver.crt** en **myserver.pfx**, le format requis par Sites Web Azure :

		openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt

	Lorsque vous y êtes invité, entrez un mot de passe pour sécuriser le fichier .pfx.

	<div class="dev-callout"> 
	<b>Remarque</b>
	<p>Si l'autorité de certification utilise des certificats intermédiaires, vous devez les installer avant d'exporter le certificat à l'étape suivante. Ces certificats sont généralement fournis sous forme de téléchargement distinct par l'autorité de certification, dans plusieurs formats adaptés à différents types de serveurs. Sélectionnez la version fournie au format PEM (extension de fichier .pem).</p>
	<p>La commande suivante montre comment créer un fichier .pfx incluant des certificats intermédiaires, qui sont contenus dans le fichier <b>intermediate-cets.pem</b> :</p>
	<pre><code>
	openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt -certfile intermediate-cets.pem
	</code></pre>
	</div>

	Une fois la commande exécutée, vous devez disposer d'un fichier **myserver.pfx** utilisable avec Sites Web Azure.


###<a href="bkmk_selfsigned"></a>Génération d'un certificat auto-signé (à des fins de test uniquement)

Dans certains cas, vous pouvez obtenir un certificat à des fins de test et retarder l'achat auprès d'une autorité de certification jusqu'à ce que vous arriviez à la phase de production. Les certificats auto-signés conviennent dans ce cas. Il s'agit de certificats que vous créez et signez comme si vous étiez une autorité de certification. Même si ce certificat peut aider à sécuriser un site web, la plupart des navigateurs renvoient une erreur lors de l'accès au site, car le certificat n'a pas été signé par une autorité de certification approuvée. Il se peut même que certains navigateurs refusent d'afficher le site.

- [Génération d'un certificat auto-signé à l'aide de makecert](#bkmk_ssmakecert)
- [Génération d'un certificat auto-signé à l'aide d'OpenSSL](#bkmk_ssopenssl)

<a href="bkmk_ssmakecert"></a>
#### Génération d'un certificat auto-signé à l'aide de makecert ####

Pour créer un certificat de test à partir du système Windows sur lequel Visual Studio est installé, procédez comme suit :

1. À partir du **menu Démarrer** ou de l'**écran d'accueil**, recherchez **Invite de commandes développeur**. Pour terminer, cliquez avec le bouton droit sur **Invite de commandes développeur** et sélectionnez **Exécuter en tant qu'administrateur**.

	Si la boîte de dialogue Contrôle de compte d'utilisateur s'ouvre, sélectionnez **Oui** pour continuer.

2. Dans l'invite de commandes développeur, utilisez la commande suivante pour créer un certificat auto-signé. Vous devez remplacer **serverdnsname** par le DNS de votre site web.

		makecert -r -pe -b 01/01/2013 -e 01/01/2014 -eku 1.3.6.1.5.5.7.3.1 -ss My -n CN=serverdnsname -sky exchange -sp "Microsoft RSA SChannel Cryptographic Provider" -sy 12 -len 2048

	Cette commande crée un certificat valide entre le 01/01/2013 et le 01/01/2014. Elle stocke l'emplacement du magasin de certificats CurrentUser.

3. À partir du **menu Démarrer** ou de l'**écran d'accueil**, recherchez **Windows PowerShell** et lancez l'application.

4. Dans l'invite de commandes Windows PowerShell, utilisez les commandes suivantes pour exporter le certificat créé précédemment :

		$mypwd = ConvertTo-SecureString -String "password" -Force -AsPlainText
		get-childitem cert:\currentuser\my -dnsname serverdnsname | export-pfxcertificate -filepath file-to-export-to.pfx -password $mypwd

	Cela permet de stocker le mot de passe spécifié en tant que chaîne sécurisée dans $mypwd, de rechercher le certificat à l'aide du nom DNS spécifié par le paramètre **dnsname**, puis d'effectuer un export vers le fichier spécifié par le paramètre **filepath**. La chaîne sécurisée contenant le mot de passe permet de sécuriser le fichier exporté.

<a href="bkmk_ssopenssl"></a>
####Génération d'un certificat auto-signé à l'aide d'OpenSSL ####

1. Créez un document nommé **serverauth.cnf** en utilisant le contenu suivant :

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
        commonName			= Common Name (eg, your website's domain name)
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

	Ceci permet de spécifier les paramètres de configuration nécessaires à la génération d'un certificat SSL pouvant être utilisé par Sites Web Azure.

2. Générez un nouveau certificat auto-signé en utilisant la commande suivante dans une ligne de commande, un interpréteur de commandes ou une session terminal :

		openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout myserver.key -out myserver.crt -config serverauth.cnf

	Ceci permet de créer un certificat à l'aide des paramètres de configuration spécifiés dans le fichier **serverauth.cnf**.

3. Pour exporter le certificat dans un fichier .PFX pouvant être téléchargé sur un site web Azure, utilisez la commande suivante :

		openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt

	Lorsque vous y êtes invité, entrez un mot de passe pour sécuriser le fichier .pfx.

	Le fichier **myserver.pfx** généré par cette commande permet de sécuriser votre site web Azure à des fins de test.

<a href="bkmk_standardmode"></a><h2>Configuration du mode standard</h2>

L'activation du protocole HTTPS pour un domaine personnalisé est disponible uniquement avec le mode de plan d'hébergement web **standard** des sites web Azure. Pour passer au mode **standard**, procédez comme suit.

> [WACOM.NOTE] Avant de basculer un site web du mode gratuit au mode **standard**, vous devez supprimer les limites de dépense mises en place pour l'abonnement au site web, sans quoi votre site risque d'être indisponible si vous atteignez la limite avant la fin de la période de facturation. Pour plus d'informations sur la tarification en mode **Standard** et Partagé, consultez [Détails de la tarification][pricing].

1. Dans votre navigateur, ouvrez le [portail de gestion][portal].

2. Sous l'onglet **Sites Web**, cliquez sur le nom de votre site.

	![selecting a web site][website]

3. Cliquez sur l'onglet **METTRE À L'ÉCHELLE**.

	![The scale tab][scale]

4. Dans la section **Général**, définissez le mode de plan d'hébergement web en cliquant sur **STANDARD**.

	![standard mode selected][standard]

5. Cliquez sur **Enregistrer**. Lorsque vous y êtes invité, cliquez sur **Oui**.

	> [WACOM.NOTE] Si un message indiquant une erreur de mise à l'échelle pour le site web choisi s'affiche, vous pouvez cliquer sur le bouton de détails pour obtenir plus d'informations. Une erreur indiquant que le nombre de serveurs d'instances disponibles est insuffisant pour répondre à la demande peut s'afficher. Dans ce cas, contactez le [support technique Azure](http://www.windowsazure.com/fr-fr/support/options/).


##<a href="bkmk_configuressl"></a>Configuration du chiffrement SSL

Avant de suivre les étapes de cette section, vous devez avoir associé un nom de domaine personnalisé à votre site web Azure. Pour plus d'informations, consultez la rubrique [Configuration d'un nom de domaine personnalisé pour un site web Azure][customdomain].

1. Dans votre navigateur, ouvrez le [portail de gestion Azure][portal].

2. Dans l'onglet **Sites Web**, cliquez sur le nom de votre site, puis sélectionnez l'onglet **CONFIGURER**.

	![the configure tab][configure]

3. Dans la section **Certificats**, cliquez sur **Télécharger un certificat**.

	![upload a certificate][uploadcert]

4. Dans la boîte de dialogue **Télécharger un certificat**, sélectionnez le fichier de certificat .pfx créé plus tôt à l'aide du Gestionnaire des services Internet ou d'OpenSSL. Spécifiez le mot de passe utilisé pour sécuriser le fichier .pfx, le cas échéant. Pour terminer, cliquez sur la **coche** pour télécharger le certificat.

	![upload certificate dialog][uploadcertdlg]

5. Dans la section **Liaisons SSL** de l'onglet **CONFIGURER**, utilisez les listes déroulantes pour sélectionner le nom de domaine à sécuriser à l'aide du protocole SSL, ainsi que le certificat à utiliser. Vous pouvez également indiquer si vous utilisez l'extension [SNI (Indication du nom du serveur)][sni] ou le protocole SSL basé sur IP.

	![ssl bindings][sslbindings]
	
	* Le protocole SSL basé sur IP associe un certificat à un nom de domaine en mappant l'adresse IP publique dédiée du serveur au nom de domaine. Chaque nom de domaine (contoso.com, fabricam.com, etc.) associé à votre service doit donc posséder une adresse IP dédiée. Il s'agit de la méthode classique permettant d'associer des certificats SSL à un serveur web.

	* Le protocole SSL basé sur SNI est une extension des protocoles SSL et [TLS (Transport Layer Security)][tls] qui permet à plusieurs domaines de partager la même adresse IP, avec des certificats de sécurité distincts pour chaque domaine. La plupart des navigateurs modernes (dont Internet Explorer, Chrome, Firefox et Opera) prennent en charge SNI. Il se peut toutefois que les navigateurs plus anciens ne proposent pas cette prise en charge. Pour plus d'informations sur l'extension SNI, consultez l'[article Wikipédia associé][].

6. Cliquez sur **Enregistrer** pour enregistrer les modifications et activer SSL.

> [WACOM.NOTE] Si vous avez sélectionné **SSL basé sur IP** et que votre domaine personnalisé est configuré à l'aide d'un enregistrement A, vous devez effectuer les opérations supplémentaires suivantes :
>
> 1. Une fois la liaison SSL basée sur IP configurée, une adresse IP dédiée est attribuée à votre site web. Celle-ci figure dans la page **Tableau de bord** de votre site web, dans la section **aperçu rapide**. Elle est répertoriée avec la mention **Adresse IP virtuelle** :
>    
>     ![Virtual IP address](./media/configure-ssl-web-site/staticip.png)
>    
>     Cette adresse IP est différente de celle utilisée précédemment pour configurer l'enregistrement A de votre domaine. Si vous utilisez le protocole SSL basé sur SNI ou que vous n'utilisez pas SSL, aucune adresse n'est indiquée pour cette entrée.
>
> 2. À l'aide des outils fournis par votre bureau d'enregistrement, modifiez l'enregistrement A de votre nom de domaine personnalisé de manière à ce qu'il pointe vers l'adresse IP spécifiée lors de l'étape précédente.


À ce stade, vous devez pouvoir visiter votre site web à l'aide de HTTPS:// plutôt que de HTTP://, afin de vérifier que le certificat a été correctement configuré.

##<a href="bkmk_enforce"></a>Application du protocole HTTPS sur votre site web Azure

L'application Sites Web Azure n'applique *pas* le protocole HTTPS. Les visiteurs peuvent toujours accéder à votre site à l'aide du protocole HTTP, ce qui peut compromettre la sécurité de votre site web. Pour appliquer le protocole HTTPS pour votre site web, vous pouvez utiliser le **module de réécriture d'URL**. Le module de réécriture d'URL est inclus avec Sites Web Azure et vous permet de définir des règles qui sont appliquées aux requêtes entrantes avant qu'elles ne soient transmises à votre application. **Il peut être utilisé pour des applications écrites dans n'importe quel langage de programmation pris en charge par Sites Web Azure.** 

> [WACOM.NOTE] Les applications .NET MVC doivent utiliser le filtre [RequireHttps](http://msdn.microsoft.com/fr-fr/library/system.web.mvc.requirehttpsattribute.aspx) plutôt que le module de réécriture d'URL. Pour plus d'informations sur l'utilisation de RequireHttps, consultez la page [Déploiement d'une application ASP.NET MVC 5 sécurisée vers un site web Azure](/fr-fr/documentation/articles/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database/).
> 
> Pour plus d'informations sur la redirection par programme des requêtes utilisant d'autres langages et infrastructures de programmation, consultez la documentation de ces technologies.

Les règles de réécriture d'URL sont définies dans un fichier **web.config** stocké à la racine de votre application. L'exemple suivant contient une règle de réécriture d'URL basique qui force tout le trafic entrant à utiliser le protocole HTTPS.

<a name="example"></a>**Exemple de fichier web.config de réécriture d'URL**

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

Cette règle fonctionne en renvoyant le code d'état HTTP 301 (redirection permanente) lorsque l'utilisateur demande une page utilisant le protocole HTTP. Le code 301 redirige la demande vers la même URL que celle requise par le visiteur, mais remplace la partie HTTP de la demande par HTTPS. Par exemple, HTTP://contoso.com serait redirigé vers HTTPS://contoso.com.

> [WACOM.NOTE] Si votre application est écrite en **Node.js**, **PHP**, **Python Django** ou **Java**, elle n'inclut probablement pas de fichier web.config. Toutefois, **Node.js**, **Python Django** et **Java** utilisent tous un fichier web.config lorsqu'ils sont hébergés sur Sites Web Azure. Azure crée le fichier automatiquement durant le déploiement. C'est pour cette raison que vous ne le voyez jamais. Si vous en incluez un comme élément de votre application, il écrasera celui qu'Azure génère automatiquement.

###.NET

Pour les applications .NET, modifiez le fichier web.config de votre application et ajoutez la section **&lt;rewrite>** de l'[exemple](#example) à la section **&lt;system.WebServer>**.

Si votre fichier web.config contient déjà une section **&lt;rewrite>**, ajoutez l'entrée **&lt;rule>** de l'[exemple](#example) comme première entrée dans la section **&lt;rules>**.

###PHP

Pour les applications PHP, enregistrez simplement l'[exemple](#example) comme fichier web.config à la racine de votre application, puis redéployez l'application vers votre site Web Azure.

###Node.js, Python Django et Java

Un fichier web.config est automatiquement créé pour les applications Node.js, Python Django et Java si elles n'en fournissent pas déjà un, mais celui-ci existe uniquement sur le serveur, puisqu'il est créé pendant le déploiement. Le fichier généré automatiquement contient des paramètres qui indiquent à Azure comment héberger votre application.

Pour récupérer et modifier le fichier auto-généré à partir du site web, procédez comme suit :

1. Téléchargez le fichier via FTP (consultez la page [Chargement/téléchargement de fichiers sur FTP et collecte de journaux de diagnostic](http://blogs.msdn.com/b/avkashchauhan/archive/2012/06/19/windows-azure-website-uploading-downloading-files-over-ftp-and-collecting-diagnostics-logs.aspx)).

2. Ajoutez-le à la racine de votre application.

3. Ajoutez les règles de réécriture à l'aide des informations suivantes.

	* **Node.js et Python Django**

		Le fichier web.config généré pour les applications Node.js et Python Django aura déjà une section **&lt;rewrite>** contenant les entrées **&lt;rule>** requises pour le bon fonctionnement du site. Pour forcer le site à utiliser le protocole HTTPS, ajoutez l'entrée **&lt;rule>** de l'exemple comme première entrée de la section **&lt;rules>**. Cette opération force l'utilisation du protocole HTTPS, tout en laissant les autres règles intactes.

	* **Java**
	
		Le fichier web.config des applications Java utilisant Apache Tomcat ne contient pas de section **&lt;rewrite>**. Vous devrez donc ajouter la section **&lt;rewrite>** de l'exemple à la section **&lt;system.webServer>**.

4. Redéployez le projet (en incluant le fichier web.config mis à jour) vers Azure

Une fois que vous avez déployé un fichier web.config avec une règle de réécriture pour forcer HTTPS, il doit prendre effet immédiatement et rediriger toutes les demandes vers HTTPS.

Pour plus d'informations sur le module de réécriture d'URL pour IIS, consultez la documentation sur la [réécriture d'URL](http://www.iis.net/downloads/microsoft/url-rewrite). 

## Plus de ressources ##
- [Microsoft Azure Trust Center](/fr-fr/support/trust-center/security/)
- [Options de configuration déverrouillées dans les sites web Azure](http://azure.microsoft.com/blog/2014/01/28/more-to-explore-configuration-options-unlocked-in-windows-azure-web-sites/)
- [Activation de la journalisation de diagnostic](/fr-fr/documentation/articles/web-sites-enable-diagnostic-log/)
- [Configuration de sites web](/fr-fr/documentation/articles/web-sites-configure/)
- [Portail de gestion Azure](https://manage.windowsazure.com)

[customdomain]: /fr-fr/develop/net/common-tasks/custom-dns-web-site/
[iiscsr]: http://technet.microsoft.com/fr-fr/library/cc732906(WS.10).aspx
[cas]: http://go.microsoft.com/fwlink/?LinkID=269988
[installcertiis]: http://technet.microsoft.com/fr-fr/library/cc771816(WS.10).aspx
[exportcertiis]: http://technet.microsoft.com/fr-fr/library/cc731386(WS.10).aspx
[openssl]: http://www.openssl.org/
[portal]: https://manage.windowsazure.com/
[tls]: http://en.wikipedia.org/wiki/Transport_Layer_Security
[staticip]: ./media/configure-ssl-web-site/staticip.png
[website]: ./media/configure-ssl-web-site/sslwebsite.png
[scale]: ./media/configure-ssl-web-site/sslscale.png
[standard]: ./media/configure-ssl-web-site/sslreserved.png
[pricing]: https://www.windowsazure.com/fr-fr/pricing/details/
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
