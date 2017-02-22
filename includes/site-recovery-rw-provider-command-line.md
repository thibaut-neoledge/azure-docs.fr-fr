UnifiedSetup.exe [/ServerMode <CS/PS>] [/InstallDrive <DriveLetter>] [/MySQLCredsFilePath <MySQL credentials file path>] [/VaultCredsFilePath <Vault credentials file path>] [/EnvType <VMWare/NonVMWare>] [/PSIP <adresse IP à utiliser pour le transfert de données] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>]

Paramètres :

* /ServerMode : Obligatoire. Spécifie si les serveurs de configuration et de processus doivent être installés ou si seul le serveur de processus doit être installé. Valeurs d’entrée : CS, PS.
* InstallLocation : Obligatoire. Dossier d’installation des composants.
* /MySQLCredsFilePath. Obligatoire. Chemin d’accès du fichier où sont stockées les informations d’identification du serveur MySQL. Le fichier doit respecter le format suivant :
* [MySQLCredentials]
* MySQLRootPassword = "<Password>"
* MySQLUserPassword = "<Password>"
* /VaultCredsFilePath. Obligatoire. Emplacement du fichier d’informations d’identification du coffre
* / EnvType. Obligatoire. Type d’installation. Valeurs : VMware, NonVMware
* / PSIP et /CSIP. Obligatoire. Adresse IP du serveur de processus et du serveur de configuration.
* /PassphraseFilePath. Obligatoire. Emplacement du fichier de phrase secrète.
* /BypassProxy. facultatif. Indique que le serveur de configuration se connecte à Azure sans proxy.
* /ProxySettingsFilePath. facultatif. Paramètres de proxy (Le proxy par défaut nécessite une authentification, sinon il faut un proxy personnalisé.). Le fichier doit respecter le format suivant :
* [ProxySettings]
* ProxyAuthentication = "Oui/Non"
* Proxy IP = "Adresse IP>"
* ProxyPort = "<Port>"
* ProxyUserName="<User Name>"
* ProxyPassword="<Password>"
* DataTransferSecurePort. facultatif. Numéro de port pour les données de réplication.
* SkipSpaceCheck. facultatif. Vérification de l’omission des espaces pour le cache.
* AcceptThirdpartyEULA. Obligatoire. Accepte le CLUF tiers.
* ShowThirdpartyEULA. Obligatoire. Affiche le CLUF tiers. S’il est fourni en tant qu’entrée, tous les autres paramètres sont ignorés.


<!--HONumber=Feb17_HO2-->


