
# Activation du protocole HTTPS pour un site Web Azure

Lorsqu'une personne visite votre site Web à l'aide du protocole HTTPS, la communication entre le site Web et le navigateur est sécurisée à l'aide du chiffrement SSL (Secure Socket Layer). Il s'agit de la méthode la plus courante en matière de sécurisation des données envoyées via Internet. Elle permet de garantir aux visiteurs que les transactions qu'ils réalisent sur votre site sont sécurisées. Cet article décrit comment activer le protocole HTTPS pour un site Web Azure.

> [WACOM.NOTE] Pour activer le protocole HTTPS pour les noms de domaines personnalisés, vous devez configurer les sites Web avec le mode standard. Des frais supplémentaires peuvent s'appliquer si vous utilisez actuellement le mode gratuit ou partagé. Pour plus d'informations sur la tarification des modes standard et partagé, consultez la page [Informations sur la tarification][1].

<a href="bkmk_azurewebsites"></a><h2>Domaine \*.azurewebsites.net</h2>

Si vous n'envisagez pas d'utiliser un nom de domaine personnalisé, mais le domaine \*.azurewebsites.net attribué à votre site Web par Azure (par exemple, contoso.azurewebsites.net), votre site est déjà sécurisé par un certificat fourni par Microsoft. Vous pouvez utiliser **https://mywebsite.azurewebsites.net** pour accéder au site de manière
sécurisée.

Le reste de ce document fournit des informations sur l'activation du protocole HTTPS pour les noms de domaines personnalisés, tels que **contoso.com**, **www.contoso.com** et **\*.contoso.com**

<a href="bkmk_domainname"></a><h2>Noms de domaines personnalisés</h2>

Pour activer le protocole HTTPS pour un nom de domaine personnalisé tel que **contoso.com**, vous devez inscrire ce dernier auprès d'un bureau d'enregistrement des noms de domaine. Pour plus d'informations sur la configuration du nom de domaine d'un site Web Azure, consultez la rubrique [Configuration d'un nom de domaine personnalisé pour un site Web Azure](/en-us/develop/net/common-tasks/custom-dns-web-site/). Une fois que vous avez inscrit un nom de domaine personnalisé et configuré votre site Web de manière à ce qu'il réponde au nom personnalisé, vous devez demander un certificat SSL pour le domaine.

L'inscription d'un nom de domaine permet également de créer des sous-domaines tels que **www.contoso.com** ou **mail.contoso.com**. Avant de demander un certificat SSL, vous devez déterminer les noms de domaine qui seront sécurisés par celui-ci, et par conséquent le type de certificat à demander. Si vous voulez sécuriser un seul nom de domaine tel que **contoso.com** ou **www.contoso.com**, un certificat de base est vraisemblablement suffisant. Pour sécuriser plusieurs noms de domaine, tels que **contoso.com**, **www.contoso.com**, et **mail.contoso.com**, un certificat avec caractères génériques ou un certificat subjectAltName (Autre nom de l'objet, SAN) est nécessaire.

> [WACOM.NOTE] La plupart des navigateurs affichent un avertissement si le nom de domaine spécifié dans le certificat ne correspond pas à celui entré dans le navigateur. Par exemple, si le certificat répertorie uniquement www.contoso.com, alors que login.contoso.com est le nom de domaine utilisé pour accéder au site dans Internet Explorer, un avertissement indiquant que le certificat de sécurité présenté par le site Web a été émis pour une autre adresse de site Web s'affiche.

Les **certificats de base** sont des certificats dont le nom commun est défini sur le domaine ou sous-domaine spécifique que le client utilise pour consulter le site. Par exemple, **www.contoso.com**. Ces certificats sécurisent uniquement le nom de domaine spécifié par le nom commun.

Les **certificats avec caractères génériques** sont des certificats dont le nom commun contient le caractère générique << \* >> au niveau du sous-domaine. Le certificat correspond ainsi à un seul niveau de sous-domaines pour le domaine spécifié. Par exemple, un certificat avec caractères génériques pour **\*.contoso.com** est valide pour **www.contoso.com**, **payment.contoso.com** et **login.contoso.com**. Il n'est pas valide pour **test.login.contoso.com**, car un niveau de sous-domaine supplémentaire a été ajouté. Il ne l'est pas non plus pour **contoso.com**, car il s'agit du niveau de domaine racine et non d'un sous-domaine.

Un certificat avec caractères génériques est fourni par Microsoft pour le nom de domaine \*.azurewebsites.net créé automatiquement pour votre site Web.

**subjectAltName** est une extension de certificat qui autorise l'association de plusieurs valeurs (ou autres noms de l'objet) à un certificat. En ce qui concerne les certificats SSL, cela vous permet d'ajouter d'autres noms DNS pour lesquels le certificat est valide. Par exemple, un certificat utilisant un autre nom de l'objet peut avoir le nom commun **contoso.com**, ainsi que d'autres noms comme **www.contoso.com**, **payment.contoso.com**, **test.login.contoso.com** et **fabrikam.com**. Ce type de certificat est valide pour tous les noms de domaine spécifiés dans le nom commun et l'autre nom de l'objet.

Le certificat peut prendre en charge les caractères génériques et autres noms de l'objet.

<a href="bkmk_getcert"></a><h2>Obtention d'un certificat</h2>

Les certificats SSL utilisés avec les sites Web Azure doivent être signés par une autorité de certification, un tiers approuvé qui émet des certificats à cette fin. Si vous n'en possédez pas, vous devez en obtenir un auprès de la société qui vend des certificats SSL. Pour obtenir la liste des autorités de certification, consultez la page [Programme de certificats racine SSL Windows et Windows Phone 8
(autorités de certification membres)][2] dans la section Wiki de Microsoft TechNet.

Le certificat SSL doit répondre aux prérequis suivants dans Azure :

* Le certificat doit contenir une clé privée.

* Le certificat doit être créé pour l'échange de clés et pouvoir faire l'objet d'un export au format Personal Information Exchange (.pfx).

* Le nom d'objet du certificat doit correspondre au domaine servant à accéder au site Web. Si vous voulez gérer plusieurs domaines avec ce certificat, vous devez utiliser un caractère générique ou spécifier un autre nom de l'objet, comme indiqué précédemment.
  
  * Pour plus d'informations sur la configuration d'un nom de domaine personnalisé pour un site Web Azure, consultez la rubrique [Configuration d'un nom de domaine personnalisé pour un site Web Azure](/en-us/develop/net/common-tasks/custom-dns-web-site/).


 
  > [WACOM.NOTE] Ne tentez pas d'obtenir ou de générer un certificat pour le domaine azurewebsites.net.

* Le certificat doit utiliser au minimum un chiffrement à 2 048 bits.

> [WACOM.NOTE] Les certificats émis à partir de serveurs d'autorité de certification privés ne sont pas pris en charge par les sites Web Azure.

Pour obtenir un certificat SSL d'une autorité de certification, vous devez générer une demande de signature de certificat qui est ensuite envoyée à celle-ci. Cette dernière renvoie ensuite un certificat servant à compléter la demande de signature de certificat. Pour générer une demande de signature de certificat, utilisez certmgr.exe ou des applications [OpenSSL][3]. Certmgr.exe est disponible uniquement sous Windows alors qu'OpenSSL est disponible sur la plupart des plateformes. L'utilisation de ces utilitaires est décrite ci-dessous.

Vous devrez peut-être obtenir des **certificats intermédiaires** (également connus sous le nom de certificats de chaîne), si ceux-ci sont utilisés par l'autorité de certification. Les certificats intermédiaires étant plus sécurisés que les certificats sans chaîne, les autorités de certification les utilisent fréquemment. Ils sont souvent fournis sous forme de téléchargement par le site Web de l'autorité de certification. Les étapes décrites dans cet article permettent de s'assurer que les certificats intermédiaires sont fusionnés avec ceux téléchargés sur votre site Web Azure.

> [WACOM.NOTE] Au cours des différentes étapes, vous serez invité à entrer un **nom commun**. Si vous obtenez un certificat avec caractères génériques à utiliser avec plusieurs domaines (www.contoso.com, sales.contoso.com), cette valeur doit être \*.domainname (par exemple, \*.contoso.com). Si vous obtenez un certificat pour un seul nom de domaine, cette valeur doit correspondre à la valeur exacte entrée par les utilisateurs dans le navigateur lorsqu'ils consultent votre site Web. Par exemple, www.contoso.com.
> 
> Pour prendre en charge un nom générique comme \*.contoso.com et un nom de domaine racine comme contoso.com, vous pouvez utiliser un certificat générique avec Autre nom de l'objet. Pour voir un exemple de création de demande de certificat qui utilise les extensions SubjectAltName, consultez [Certificat SubjectAltName](#bkmk_subjectaltname).
> 
> Pour plus d'informations sur la configuration du nom de domaine d'un site Web Azure, consultez la rubrique [Configuration d'un nom de domaine personnalisé pour un site Web Azure](/en-us/develop/net/common-tasks/custom-dns-web-site/).

### Obtention d'un certificat à l'aide de Certreq.exe (Windows uniquement)

Certreq.exe est un utilitaire Windows pour la création de demandes de certificat. Il fait partie de l'installation Windows de base depuis Windows XP/Windows Server 2000 et doit être disponible sur les systèmes Windows récents. Suivez les étapes ci-dessous pour obtenir un certificat SSL à l'aide de certreq.exe.

Pour créer un certificat auto-signé à des fins de test, consultez la section [Certificats auto-signés](#bkmk_selfsigned) de ce document.

Pour créer une demande de certificat à l'aide du gestionnaire des services Internet, consultez la section [Obtention d'un certificat à l'aide du gestionnaire des services Internet](#bkmk_iismgr).

1.  Ouvrez le **Bloc-notes** et créez un document contenant les éléments suivants. Dans la ligne d'objet, remplacez **mysite.com** par le nom de domaine personnalisé de votre site Web. Par exemple, Objet = "CN=www.contoso.com".
    
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
    
    Pour plus d'informations sur les options spécifiées plus haut, ainsi que sur les autres options disponibles, consultez la [documentation de référence Certreq][4].

2.  Enregistrez le fichier texte sous le nom **myrequest.txt**.

3.  Dans l'**écran d'accueil** ou le **menu Démarrer**, exécutez **cmd.exe**.

4.  À l'invite de commandes, utilisez la commande suivante pour créer le fichier de demande de certificat :
    
         certreq -new \path\to\myrequest.txt \path\to\create\myrequest.csr
    
    Spécifiez le chemin d'accès au fichier **myrequest.txt** créé à l'étape 1, ainsi que celui utilisé lors de la création du fichier **myrequest.csr**.

5.  Envoyez le fichier **myrequest.csr** à une autorité de certification pour obtenir un certificat SSL. Pour cela, vous devrez peut-être télécharger le fichier ou l'ouvrir dans le Bloc-notes et coller son contenu directement dans un formulaire Web.
    
    Pour obtenir la liste des autorités de certification, consultez la page [Programme de certificats racine SSL Windows et Windows Phone 8 (autorités de certification membres)][2] dans la section Wiki de Microsoft TechNet.

6.  Une fois que l'autorité de certification vous a fourni un fichier de certificat (.CER); enregistrez-le sur l'ordinateur qui a généré la demande, puis utilisez la commande suivante pour accepter la demande et achever le processus de génération du certificat.
    
         certreq -accept -user mycert.cer
    
    Dans ce cas, le certificat **mycert.cer** reçu de l'autorité de certification servira à obtenir la signature du certificat. Aucun fichier n'est créé. Au lieu de cela, le certificat est stocké dans le magasin de certificats Windows.

7.  Si l'autorité de certification utilise des certificats intermédiaires, vous devez les installer avant d'exporter le certificat lors des prochaines étapes. Ces certificats sont généralement fournis sous forme de téléchargement distinct par l'autorité de certification, dans plusieurs formats adaptés à différents types de serveurs. Sélectionnez la version fournie pour Microsoft IIS.
    
    Une fois le certificat téléchargé, cliquez dessus avec le bouton droit dans l'explorateur, puis sélectionnez **Installer le certificat**. Utilisez les valeurs par défaut de l'**Assistant Importation de certificat**, puis sélectionnez **Suivant** jusqu'à ce que l'importation soit terminée.

8.  Pour exporter le certificat à partir du magasin de certificats, exécutez **certmgr.msc** à partir de l'**écran d'accueil** ou du **menu Démarrer**. Lorsque le **gestionnaire de certificats** apparaît, développez le dossier **Personnel**, puis sélectionnez **Certificats**. Dans le champ **Délivré à**, recherchez l'entrée correspondant au nom de domaine personnalisé pour lequel vous avez demandé un certificat. Le champ **Délivré par** doit répertorier
    l'autorité de certification utilisée pour ce certificat.
    
    ![insérer l’image du gestionnaire de certificat ici](./media/configure-ssl-web-site/waws-certmgr.png)

9.  Cliquez avec le bouton droit sur le certificat, sélectionnez **Toutes les tâches**, puis **Exporter**. Dans l'**Assistant Exportation de certificat**, cliquez sur **Suivant**, puis sélectionnez **Oui, exporter la clé privée**. Cliquez sur **Next**.
    
    ![Exporter la clé privée](./media/configure-ssl-web-site/waws-certwiz1.png)

10. Sélectionnez <strong>Échange d'informations personnelles - PKCS #12 </strong>, **Inclure tous les certificats dans le chemin d'accès de certification** et **Exporter toutes les propriétés étendues**. Cliquez sur **Next**.
    
    ![inclure tous les certificats et les propriétés
    étendues](./media/configure-ssl-web-site/waws-certwiz2.png)

11. Sélectionnez **Mot de passe**, puis entrez et confirmez le mot de passe. Cliquez sur **Next**.
    
    ![spécifier un mot de
    passe](./media/configure-ssl-web-site/waws-certwiz3.png)

12. Indiquez le chemin d'accès et le nom de fichier contenant le certificat exporté. Le nom de fichier doit porter l'extension **.pfx**. Cliquez sur **Suivant** pour terminer le processus.
    
    ![indiquer le chemin d’accès au
    fichier](./media/configure-ssl-web-site/waws-certwiz4.png)

Vous pouvez maintenant télécharger le fichier PFX exporté sur votre site Web Azure.

### Obtention d'un certificat à l'aide d'OpenSSL

1.  Générez une clé privée et une demande de signature de certificat en utilisant la commande suivante dans une ligne de commande, un interpréteur de commandes ou une session terminal :
    
         openssl req -new -nodes -keyout myserver.key -out server.csr -newkey rsa:2048

2.  Lorsque vous y êtes invité, entrez les informations appropriées. Par exemple :

		Nom du pays (code composé de 2 lettres) 
        State or Province Name (full name) []: Washington
        Locality Name (eg, city) []: Redmond
        Organization Name (eg, company) []: Microsoft
        Organizational Unit Name (eg, section) []: Azure
        Common Name (eg, YOUR name) []: www.microsoft.com
        Email Address []:
    
    	Entrez les attributs supplémentaires suivants à envoyer avec votre demande de certificat
    
       	A challenge password []: 
    
    Une fois le processus terminé, vous devez disposer des fichiers **myserver.key** et **server.csr**. Le fichier **server.csr** contient la demande de signature de certificat.

1.  Envoyez votre demande de signature de certificat à une autorité de certification pour obtenir un certificat SSL. Pour obtenir la liste des autorités de certification, consultez la page [Programme de certificats racine SSL Windows et Windows Phone 8 (autorités de certification membres)][2] dans la section Wiki de Microsoft TechNet.

2.  Lorsque vous avez obtenu un certificat d'une autorité de certification, enregistrez-le sous le nom **myserver.crt**. Si l'autorité de certification a fourni le certificat au format texte, collez le texte dans le fichier **myserver.crt**. Le contenu du fichier doit être semblable à ce qui suit lorsque vous l'affichez dans un éditeur de texte :
    
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

3.  Dans une ligne de commande, un interpréteur de commandes ou une session terminal, utilisez la commande suivante pour convertir **myserver.key** et **myserver.crt** en **myserver.pfx** (format requis par les sites Web Azure :
    
         openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt
    
    Lorsque vous y êtes invité, entrez un mot de passe pour sécuriser le
    fichier .pfx.
    
    <div  class="dev-callout" markdown="1">

**Remarque**
Si l'autorité de certification utilise des certificats intermédiaires, vous devez les installer avant d'exporter le certificat lors de la prochaine étape. Ces certificats sont généralement fournis sous forme de téléchargement distinct par l'autorité de certification, dans plusieurs formats adaptés à différents types de serveurs. Sélectionnez la version fournie au format PEM (extension de fichier .pem).


La commande suivante montre comment créer un fichier .pfx incluant des certificats intermédiaires, qui sont contenus dans le fichier **intermediate-cets.pem** :



		openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt -certfile intermediate-cets.pem





    
 Une fois la commande exécutée, vous devez disposer d'un fichier **myserver.pfx** utilisable avec les sites Web Azure.

<a href="bkmk_standardmode"></a><h2>Configuration du mode standard</h2>

L'activation du protocole HTTPS pour un domaine personnalisé est disponible uniquement avec le mode standard des sites Web Azure. Pour passer au mode standard, procédez comme suit.

> [WACOM.NOTE] Avant de basculer un site Web du mode Gratuit au mode Standard, vous devez supprimer les limites de dépense mises en place pour l'abonnement au site Web, sans quoi votre site risque d'être indisponible si vous atteignez la limite avant la fin de la période de facturation. Pour plus d'informations sur la tarification des modes standard et partagé, consultez la page [Informations sur la tarification][1].

1.  Dans votre navigateur, ouvrez le [portail de gestion][5].

2.  Sous l'onglet **Web Sites**, cliquez sur le nom de votre site Web.
    
    ![sélectionner un site
    Web](./media/configure-ssl-web-site/sslwebsite.png)

3.  Cliquez sur l'onglet **SCALE**.
    
    ![Onglet Scale](./media/configure-ssl-web-site/sslscale.png)

4.  Dans la section **general**, définissez le mode du site Web en cliquant sur **STANDARD**.
    
    ![mode standard
    sélectionné](./media/configure-ssl-web-site/sslreserved.png)

5.  Cliquez sur **Save**. Lorsque vous y êtes invité, cliquez sur **Yes**.


   
    > [WACOM.NOTE] Si un message indiquant une erreur de mise à l'échelle pour le site Web choisi s'affiche, vous pouvez cliquer sur le bouton de détails pour obtenir plus d'informations. Une erreur indiquant que le nombre de serveurs d'instances disponibles est insuffisant pour répondre à la demande peut s'afficher. Dans ce cas, contactez le [support technique Azure][6].

<a href="bkmk_configuressl"></a><h2>Configuration du protocole SSL</h2>

Avant de suivre les étapes de cette section, vous devez avoir associé un nom de domaine personnalisé à votre site Web Azure. Pour plus d'informations, consultez la rubrique [Configuration d'un nom de domaine personnalisé pour un site Web Azure](/en-us/develop/net/common-tasks/custom-dns-web-site/).

1.  Dans votre navigateur, ouvrez le [portail de gestion Azure][5].

2.  Dans l'onglet **Web Sites**, cliquez sur le nom de votre site, puis sélectionnez l'onglet **CONFIGURE**.
    
    ![Onglet Configure](./media/configure-ssl-web-site/sslconfig.png)

3.  Dans la section **certificates**, cliquez sur **upload a certificate**
    
    ![télécharger un
    certificat](./media/configure-ssl-web-site/ssluploadcert.png)

4.  Dans la boîte de dialogue **Upload a certificate**, sélectionnez le fichier de certificat .pfx créé plus tôt à l'aide du gestionnaire des services Internet ou d'OpenSSL. Spécifiez le mot de passe utilisé pour sécuriser le fichier .pfx, le cas échéant. Pour terminer, cliquez sur la **coche** pour télécharger le certificat.
    
    ![boîte de dialogue upload
    certificate](./media/configure-ssl-web-site/ssluploaddlg.png)

5.  Dans la section **ssl bindings** de l'onglet **CONFIGURE**, utilisez les listes déroulantes pour sélectionner le nom de domaine à sécuriser à l'aide du protocole SSL, ainsi que le certificat à utiliser. Vous pouvez également indiquer si vous utilisez l'extension [SNI (Indication du nom du serveur)][7] ou le protocole SSL basé sur IP.
    
    ![liaisons SSL](./media/configure-ssl-web-site/sslbindings.png)
    
    * Le protocole SSL basé sur IP associe un certificat à un nom de domaine en mappant l'adresse IP publique dédiée du serveur au nom de domaine. Chaque nom de domaine (contoso.com, fabricam.com, etc.) associé à votre service doit donc posséder une adresse IP dédiée. Il s'agit de la méthode classique permettant d'associer des certificats SSL à un serveur Web.
    
    * Le protocole SSL basé sur SNI une extension des protocoles SSL et [TLS (Transport Layer Security)][8] qui permet à plusieurs domaines de partager la même adresse IP, avec des certificats de sécurité distincts pour chaque domaine. La plupart des navigateurs modernes (dont Internet Explorer, Chrome, Firefox et Opera) prennent en charge SNI. Il se peut toutefois que les navigateurs plus anciens ne proposent pas cette prise en charge. Pour plus
      d'informations sur l'extension SNI, consultez l'[article Wikipédia associé][7].

6.  Cliquez sur **Save** pour enregistrer les modifications et activer SSL.

> [WACOM.NOTE] Si vous avez sélectionné **IP based SSL** et que votre domaine personnalisé est configuré à l'aide d'un enregistrement A, vous devez effectuer les opérations suivantes :
> 
> 1.  Une fois la liaison SSL basée sur IP configurée, une adresse IP dédiée est attribuée à votre site Web. Celle-ci figure dans la page **Dashboard** de votre site Web, dans la section **quick glance**. Elle est répertoriée avec la mention **Adresse IP virtuelle** :
>     
>     ![Adresse IP
>     virtuelle](./media/configure-ssl-web-site/staticip.png)
>     
>     Cette adresse IP est différente de celle utilisée précédemment pour configurer l'enregistrement A de votre domaine. Si vous utilisez le protocole SSL basé sur SNI ou que vous n'utilisez pas SSL, aucune adresse n'est indiquée pour cette entrée.
> 
> 2.  À l'aide des outils fournis par votre bureau d'enregistrement, modifiez l'enregistrement A de votre nom de domaine personnalisé de manière à ce qu'il pointe vers l'adresse IP spécifiée lors de l'étape précédente.

À ce stade, vous devez pouvoir visiter votre site Web à l'aide du protocole HTTPS pour vérifier que le certificat a été correctement configuré.

<a href="bkmk_subjectaltname"></a><h2>Certificats SubjectAltName
(facultatif)</h2>

Bien que permettant de créer une demande de certificat qui utilise l'extension SubjectAltName pour prendre en charge plusieurs noms de domaine avec un seul certificat, OpenSSL nécessite un fichier de configuration. Les étapes suivantes permettent de créer un fichier de configuration, puis de l'utiliser pour demander un certificat.

1.  Créez un fichier nommé **sancert.cnf** et utilisez le contenu suivant :
  
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
    
    Notez la ligne qui commence par << subjectAltName >>. Remplacez les noms de domaine répertoriés par ceux que vous souhaitez prendre en charge en plus du nom commun. Par exemple :
    
         subjectAltName=DNS:sales.contoso.com,DNS:support.contoso.com,DNS:fabrikam.com
    
    Il n'est pas nécessaire de modifier le champ commonName\_default, car vous serez invité à entrer le nom commun au cours de l'une des prochaines étapes.

2.  Enregistrez le fichier **sancert.cnf**.

3.  Générez une clé privée et une demande de signature de certificat à l'aide du fichier de configuration sancert.cnf. Dans un interpréteur de commandes ou une session terminal, utilisez la commande suivante :
    
         openssl req -new -nodes -keyout myserver.key -out server.csr -newkey rsa:2048 -config sancert.cnf

4.  Lorsque vous y êtes invité, entrez les informations appropriées. Par exemple :

 		Nom du pays (code composé de 2 lettres) [] : FR         
		State or Province Name (full name) []: Washington
        Locality Name (eg, city) []: Redmond
        Organizational Unit Name (eg, section) []: Azure
        Your common name (eg, domain name) []: www.microsoft.com
     
    
    Une fois le processus terminé, vous devez disposer des fichiers **myserver.key** et **server.csr**. Le fichier **server.csr** contient la demande de signature de certificat.

1.  Envoyez votre demande de signature de certificat à une autorité de certification pour obtenir un certificat SSL. Pour obtenir la liste des autorités de certification, consultez la page [Programme de certificats racine SSL Windows et Windows Phone 8 (autorités de certification membres)][2] dans la section Wiki de Microsoft TechNet.

2.  Lorsque vous avez obtenu un certificat d'une autorité de certification, enregistrez-le sous le nom **myserver.crt**. Si l'autorité de certification a fourni le certificat au format texte, collez le texte dans le fichier **myserver.crt**. Le contenu du fichier doit être semblable à ce qui suit lorsque vous l'affichez dans un éditeur de texte :
    
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

3.  Dans une ligne de commande, un interpréteur de commandes ou une session terminal, utilisez la commande suivante pour convertir **myserver.key** et **myserver.crt** en **myserver.pfx** (format requis par les sites Web Azure :
    
         openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt
    
    Lorsque vous y êtes invité, entrez un mot de passe pour sécuriser le fichier .pfx.
    
    <div  class="dev-callout" markdown="1">

**Remarque**
Si l'autorité de certification utilise des certificats intermédiaires, vous devez les installer avant d'exporter le certificat lors de la prochaine étape. Ces certificats sont généralement fournis sous forme de téléchargement distinct par l'autorité de certification, dans plusieurs formats adaptés à différents types de serveurs. Sélectionnez la version fournie au format PEM (extension de fichier .pem).


La commande suivante montre comment créer un fichier .pfx incluant des certificats intermédiaires, qui sont contenus dans le fichier **intermediate-cets.pem** :



		openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt -certfile intermediate-cets.pem




    
Une fois la commande exécutée, vous devez disposer d'un fichier **myserver.pfx** utilisable avec les sites Web Azure.

## <a name="bkmk_iismgr"></a>Obtention d'un certificat à l'aide du gestionnaire des services Internet (facultatif)

Si vous savez utiliser le gestionnaire des services Internet, vous pouvez vous en servir pour générer un certificat pouvant être utilisé avec des sites Web Azure.

1.  Générez une demande de signature de certificat à l'aide du gestionnaire des services Internet et envoyez-la à l'autorité de certification. Pour plus d'informations sur la génération d'une demande de signature de certificat, consultez la page [Demander un certificat de serveur Internet (IIS 7)][9].

2.  Envoyez votre demande de signature de certificat à une autorité de certification pour obtenir un certificat SSL. Pour obtenir la liste des autorités de certification, consultez la page [Programme de certificats racine SSL Windows et Windows Phone 8 (autorités de certification membres)][2] dans la section Wiki de Microsoft TechNet.

3.  Complétez la demande de signature de certificat avec le certificat fourni par l'autorité de certification. Pour plus d'informations sur la génération d'une demande de signature de certificat, consultez la page [Installer un certificat de serveur Internet (IIS 7)][10].

4.  Si l'autorité de certification utilise des certificats intermédiaires, vous devez les installer avant d'exporter le certificat lors de la prochaine étape. Ces certificats sont généralement fournis sous forme de téléchargement distinct par l'autorité de certification, dans plusieurs formats adaptés à différents types de serveurs. Sélectionnez la version fournie pour Microsoft IIS.
    
    Une fois le certificat téléchargé, cliquez dessus avec le bouton droit dans l'explorateur, puis sélectionnez **Installer le certificat**. Utilisez les valeurs par défaut de l'**Assistant Importation de certificat**, puis sélectionnez **Suivant** jusqu'à ce que l'importation soit terminée.

5.  Exportez le certificat à partir du gestionnaire des services Internet. Pour plus d'informations sur l'exportation du certificat, consultez la page [Exporter un certificat de serveur (IIS 7)][11]. Le fichier exporté sera téléchargé ultérieurement dans Windows Azure pour l'utiliser avec votre site Web Azure.
    
    <div  class="dev-callout" markdown="1">

**Remarque**

Au cours de l'exportation, veillez à sélectionner l'option **Oui, exporter la clé privée**, qui permet d'inclure la clé privée dans le certificat exporté.



**Remarque**

Lors de l'exportation, veillez à sélectionner les options **Inclure tous les certificats dans le chemin d'accès de certification** et **Exporter toutes les propriétés étendues**. Ainsi, les certificats intermédiaires seront inclus dans le certificat exporté.



<a href="bkmk_selfsigned"></a><h2>Certificats auto-signés (facultatif)</h2>

Dans certains cas, vous pouvez obtenir un certificat à des fins de test et retarder l'achat auprès d'une autorité de certification jusqu'à ce que vous arriviez à la phase de production. Les certificats auto-signés conviennent dans ce cas. Il s'agit de certificats que vous créez et signez comme si vous étiez une autorité de certification. Même si ce certificat peut aider à sécuriser un site Web, la plupart des navigateurs renvoient une erreur lors de l'accès au site, car le certificat n'a pas été signé par une autorité de certification approuvée. Il se peut même que certains navigateurs refusent d'afficher le site.

Plusieurs méthodes permettent de créer un certificat auto-signé. Cet article évoque uniquement celles basées sur **makecert** et **OpenSSL**.

### Création d'un certificat auto-signé à l'aide de makecert

Pour créer un certificat de test à partir du système Windows sur lequel Visual Studio est installé, procédez comme suit :

1.  À partir du **menu Démarrer** ou de l'**écran d'accueil**, recherchez **Invite de commandes développeur**. Pour terminer, cliquez avec le bouton droit sur **Invite de commandes développeur** et sélectionnez **Exécuter en tant qu'administrateur**.
    
    Si la boîte de dialogue du contrôle de compte d'utilisateur s'ouvre, sélectionnez **Oui** pour continuer.

2.  Dans l'invite de commandes développeur, utilisez la commande suivante pour créer un certificat auto-signé. Vous devez remplacer **serverdnsname** par le DNS de votre site Web.
    
         makecert -r -pe -b 01/01/2013 -e 01/01/2014 -eku 1.3.6.1.5.5.7.3.1 -ss My -n CN=serverdnsname -sky exchange -sp "Microsoft RSA SChannel Cryptographic Provider" -sy 12 -len 2048
    
    Cette commande crée un certificat valide entre le 01/01/2013 et le 01/01/2014. Elle stocke l'emplacement du magasin de certificats CurrentUser.

3.  À partir du **menu Démarrer** ou de l'**écran d'accueil**, recherchez **Windows PowerShell** et lancez l'application.

4.  Dans l'invite de commandes Windows PowerShell, utilisez les commandes suivantes pour exporter le certificat créé précédemment :
    
         $mypwd = ConvertTo-SecureString -String "password" -Force -AsPlainText
         get-childitem cert:\currentuser\my -dnsname serverdnsname | export-pfxcertificate -filepath file-to-export-to.pfx -password $mypwd
    
    Cela permet de stocker le mot de passe spécifié en tant que chaîne sécurisée dans $mypwd, de rechercher le certificat à l'aide du nom DNS spécifié par le paramètre **dnsname**, puis d'effectuer un export vers le fichier spécifié par le paramètre **filepath**. La chaîne sécurisée contenant le mot de passe permet de sécuriser le fichier exporté.

### Création d'un certificat auto-signé à l'aide d'OpenSSL

1.  Créez un document nommé **serverauth.cnf** en utilisant le contenu suivant :
    
         [ req ]
         default_bits           = 2048
         default_keyfile        = privkey.pem
         distinguished_name = req_distinguished_name
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
         commonName			= Common Name (eg, your website’s domain name)
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
    
    Ceci permet de spécifier les paramètres de configuration nécessaires à la génération d'un certificat SSL pouvant être utilisé par les sites Web Azure.

2.  Générez un nouveau certificat auto-signé en utilisant la commande suivante dans une ligne de commande, un interpréteur de commandes ou une session terminal :
    
         openssl req -x509 -nodes -days 365 -newkey rsa:2048 -keyout myserver.key -out myserver.crt -config serverauth.cnf
    
    Ceci permet de créer un certificat à l'aide des paramètres de configuration spécifiés dans le fichier **serverauth.cnf**.

3.  Pour exporter le certificat dans un fichier .PFX pouvant être téléchargé sur un site Web Azure, utilisez la commande suivante :
    
         openssl pkcs12 -export -out myserver.pfx -inkey myserver.key -in myserver.crt
    
    Lorsque vous y êtes invité, entrez un mot de passe pour sécuriser le fichier .pfx.
    
    Le fichier **myserver.pfx** généré par cette commande permet de sécuriser votre site Web Azure à des fins de test.



[1]: https://www.windowsazure.com/en-us/pricing/details/
[2]: http://go.microsoft.com/fwlink/?LinkID=269988
[3]: http://www.openssl.org/
[4]: http://technet.microsoft.com/library/cc725793.aspx
[5]: https://manage.windowsazure.com/
[6]: http://www.windowsazure.com/en-us/support/options/
[7]: http://en.wikipedia.org/wiki/Server_Name_Indication
[8]: http://en.wikipedia.org/wiki/Transport_Layer_Security
[9]: http://technet.microsoft.com/en-us/library/cc732906(WS.10).aspx
[10]: http://technet.microsoft.com/en-us/library/cc771816(WS.10).aspx
[11]: http://technet.microsoft.com/en-us/library/cc731386(WS.10).aspx