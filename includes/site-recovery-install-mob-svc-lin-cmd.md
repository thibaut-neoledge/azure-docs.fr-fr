1. Copiez le programme d’installation dans un dossier local (par exemple /tmp) sur le serveur que vous souhaitez protéger et exécutez les commandes suivantes à partir d’une fenêtre de terminal.
  ```
  cd /tmp
  tar -xvzf Microsoft-ASR_UA*release.tar.gz
  ```
2. Maintenant, nous pouvons installer le service Mobilité à l’aide de la ligne de commande suivante

  ```
  sudo ./install -t both -a host -R Agent -d /usr/local/ASR -i <IP address> -p <port> -s y -c https -P MobSvc.passphrase
  ```

#### <a name="mobility-service-installer-command-line-arguments"></a>Arguments de ligne de commande du programme d’installation du service Mobilité

|Paramètre|Type|Description|Valeurs possibles|
|-|-|-|-|
|-t |Obligatoire|Type d’agent<br>(Sera supprimé dans la prochaine version)|*les deux*|
|-a |Obligatoire|Configuration de l’agent<br>(Sera supprimé dans la prochaine version) |*host*|
|-R |Facultatif|Rôle de l’agent|Agent<br>MasterTarget|
|-d |Facultatif|Emplacement où sera installé le service Mobilité|/usr/local/ASR|
|-i |Obligatoire|Adresse IP du serveur de configuration.|Une adresse IP valide|
|-p |Obligatoire|Port sur lequel le serveur de configuration écoute les connexions entrantes|443|
|-s |Obligatoire|Démarrer le service après une installation réussie<br>(Sera supprimé dans la prochaine version)|*y*|
|-c |Obligatoire|Mode de communication entre l’agent et le serveur de traitement<br>(Sera supprimé dans la prochaine version) |*https*|
|-P |Obligatoire|Phrase secrète du serveur de configuration|N’importe quel chemin d’accès UNC ou local valide|


#### <a name="sample-usage"></a>Exemple d’utilisation
```
sudo ./install -t both -a host -R Agent -d /usr/local/ASR -i 192.168.2.53 -p 443 -s y -c https -P /tmp/MobSvc.passphrase
```


<!--HONumber=Jan17_HO3-->


