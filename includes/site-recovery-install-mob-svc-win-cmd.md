1. Copiez le programme d’installation dans un dossier local (par exemple C:\Temp) sur le serveur que vous souhaitez protéger. Exécutez les commandes suivantes en tant qu’administrateur à l’invite de commandes :

  ```
  cd C:\Temp
  ren Microsoft-ASR_UA*Windows*release.exe MobilityServiceInstaller.exe
  MobilityServiceInstaller.exe /q /x:C:\Temp\Extracted
  cd C:\Temp\Extracted.
  ```
2. Pour installer le service Mobilité, exécutez la commande suivante :

  ```
  UnifiedAgent.exe /Role "Agent" /CSEndpoint "IP address of the configuration server" /PassphraseFilePath <Full path to the passphrase file>``
  ```

#### <a name="mobility-service-installer-command-line-arguments"></a>Arguments de ligne de commande du programme d’installation du service Mobilité

```
Usage :
UnifiedAgent.exe [/Role <Agent/MasterTarget>] [/InstallLocation <Installation directory>] [/CSIP <IP address>] [/PassphraseFilePath <Passphrase file path>] [/LogFilePath <Log file path>]<br/>
```

  | Paramètre|Type|Description|Valeurs possibles|
  |-|-|-|-|
  |/Role|Obligatoire|Indique si le service Mobilité doit être installé|Agent </br> MasterTarget|
  |/InstallLocation|Obligatoire|Emplacement où est installé le service Mobilité|N’importe quel dossier sur l’ordinateur|
  |/CSIP|Obligatoire|Adresse IP du serveur de configuration| Une adresse IP valide|
  |/PassphraseFilePath|Obligatoire|Emplacement de la phrase secrète |N’importe quel chemin d’accès UNC ou local valide|
  |/LogFilePath|Facultatif|Emplacement du journal d’installation|N’importe quel dossier valide sur l’ordinateur|

#### <a name="example"></a>Exemple

```
  UnifiedAgent.exe /Role "Agent" /CSEndpoint "I192.168.2.35" /PassphraseFilePath "C:\Temp\MobSvc.passphrase"
```
