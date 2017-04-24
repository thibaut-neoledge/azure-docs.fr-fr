Vous pouvez utiliser la commande **Get-AzureVNetConnection** pour vérifier la connexion à une passerelle de réseau virtuel classique. 

1. Utilisez l’exemple d’applet de commande suivant, en configurant les valeurs sur les vôtres. Le nom du réseau virtuel doit être placé entre guillemets s’il contient des espaces.

  ```powershell
  Get-AzureVNetConnection "Group ClassicRG ClassicVNet"
  ```
2. Une fois l’applet de commande exécutée, affichez les valeurs. Dans l’exemple ci-dessous, la zone État de la connectivité indique « Connecté » et vous pouvez voir les octets d’entrée et de sortie.

        ConnectivityState         : Connected
        EgressBytesTransferred    : 181664
        IngressBytesTransferred   : 182080
        LastConnectionEstablished : 1/7/2016 12:40:54 AM
        LastEventID               : 24401
        LastEventMessage          : The connectivity state for the local network site 'RMVNetLocal' changed from Connecting to
                                    Connected.
        LastEventTimeStamp        : 1/7/2016 12:40:54 AM
        LocalNetworkSiteName      : RMVNetLocal
