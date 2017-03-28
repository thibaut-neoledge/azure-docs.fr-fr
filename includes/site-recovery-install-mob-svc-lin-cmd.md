1. Copiez le programme d’installation dans un dossier local (par exemple /tmp) sur le serveur que vous souhaitez protéger. Dans un terminal, exécutez les commandes suivantes :
  ```
  cd /tmp
  tar -xvzf Microsoft-ASR_UA*release.tar.gz
  ```
2. Pour installer le service Mobilité, exécutez la commande suivante :

  ```
  sudo ./install -t both -a host -R Agent -d /usr/local/ASR -i <IP address> -p <port> -s y -c https -P MobSvc.passphrase
  ```

#### <a name="mobility-service-installer-command-line-arguments"></a>Arguments de ligne de commande du programme d’installation du service Mobilité

|Paramètre|Type|Description|Valeurs possibles|
|-|-|-|-|
|-t |Obligatoire|Type d’agent<br>(déconseillé dans une version à venir)|*les deux*|
|-a |Obligatoire|Configuration de l’agent<br>(déconseillé dans une version à venir) |*host*|
|-R |Facultatif|Rôle de l’agent|Agent<br>MasterTarget|
|-d |Facultatif|Emplacement où sera installé le service Mobilité|/usr/local/ASR|
|-i |Obligatoire|Adresse IP du serveur de configuration|Une adresse IP valide|
|-p |Obligatoire|Port sur lequel le serveur de configuration écoute les connexions entrantes|443|
|-s |Obligatoire|Démarre le service après une installation réussie<br>(déconseillé dans une version à venir)|*y*|
|-c |Obligatoire|Mode de communication entre l’agent et le serveur de traitement<br>(déconseillé dans une version à venir) |*https*|
|-P |Obligatoire|Phrase secrète du serveur de configuration|N’importe quel chemin d’accès UNC ou local valide|


#### <a name="example"></a>Exemple
```
sudo ./install -t both -a host -R Agent -d /usr/local/ASR -i 192.168.2.53 -p 443 -s y -c https -P /tmp/MobSvc.passphrase
```
