1. Copiez le programme d’installation dans un dossier local (par exemple C:\Temp) sur le serveur que vous souhaitez protéger et exécutez les commandes suivantes à partir d’une invite de commandes avec élévation de privilèges.

  ```
  cd C:\Temp
  ren Microsoft-ASR_UA*Windows*release.exe MobilityServiceInstaller.exe
  MobilityServiceInstaller.exe /q /xC:\Temp\Extracted
  cd C:\Temp\Extracted.
  ```
2. Maintenant, nous pouvons installer le service Mobilité à l’aide de la ligne de commande suivante

  ```
  UnifiedAgent.exe /Role "Agent" /CSEndpoint "IP Address of Configuration Server" /PassphraseFilePath <Full path to the passphrase file>``
  ```

#### <a name="mobility-service-installer-command-line-arguments"></a>Arguments de ligne de commande du programme d’installation du service Mobilité

```
Usage :
UnifiedAgent.exe [/Role <Agent/MasterTarget>] [/InstallLocation <Installation Directory>] [/CSIP <IP address>] [/PassphraseFilePath <Passphrase file path>] [/LogFilePath <Log File Path>]<br/>
```

  | Paramètre|Type|Description|Valeurs possibles|
  |-|-|-|-|
  |/Role|Obligatoire|Indique si le service Mobilité doit être installé|Agent </br> MasterTarget|
  |/InstallLocation|Obligatoire|Emplacement où sera installé le service Mobilité|N’importe quel dossier sur l’ordinateur|
  |/CSIP|Obligatoire|Adresse IP du serveur de configuration| Une adresse IP valide|
  |/PassphraseFilePath|Obligatoire|Emplacement de stockage de la phrase secrète |N’importe quel chemin d’accès UNC ou local valide|
  |/LogGilePath|Facultatif|Emplacement du journal d’installation|N’importe quel dossier valide sur l’ordinateur|

#### <a name="sample-usage"></a>Exemple d’utilisation

```
  UnifiedAgent.exe /Role "Agent" /CSEndpoint "I192.168.2.35" /PassphraseFilePath "C:\Temp\MobSvc.passphrase"
```


<!--HONumber=Jan17_HO3-->


