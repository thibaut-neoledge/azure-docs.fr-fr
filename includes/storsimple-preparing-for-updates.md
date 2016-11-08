<!--author=jgerend last changed: 03/16/16-->

## Préparation des mises à jour
Vous devez effectuer les étapes suivantes avant d’analyser et d’appliquer la mise à jour :

1. Prenez un instantané cloud des données de l’appareil.
2. Assurez-vous que les adresses IP fixes du contrôleur sont routables et peuvent se connecter à Internet. Ces adresses IP fixes seront utilisées pour mettre en service les mises à jour sur votre appareil. Vous pouvez tester cette fonctionnalité en exécutant l’applet de commande suivante sur chaque contrôleur à partir de l’interface Windows PowerShell de l’appareil :
   
     `Test-Connection -Source <Fixed IP of your device controller> -Destination <Any IP or computer name outside of datacenter network> `
   
    **Résultat de l’exemple pour Test-Connection lorsque des adresses IP fixes peuvent se connecter à Internet**

        Controller0>Test-Connection -Source 10.126.173.91 -Destination bing.com

        Source      Destination     IPV4Address      IPV6Address
        ----------------- -----------  -----------
        HCSNODE0  bing.com        204.79.197.200
        HCSNODE0  bing.com        204.79.197.200
        HCSNODE0  bing.com        204.79.197.200
        HCSNODE0  bing.com        204.79.197.200

        Controller0>Test-Connection -Source 10.126.173.91 -Destination  204.79.197.200

        Source      Destination       IPV4Address    IPV6Address
        ----------------- -----------  -----------
        HCSNODE0  204.79.197.200  204.79.197.200
        HCSNODE0  204.79.197.200  204.79.197.200
        HCSNODE0  204.79.197.200  204.79.197.200
        HCSNODE0  204.79.197.200  204.79.197.200

Une fois que vous avez terminé ces vérifications préalables manuelles, vous pouvez passer à l’analyse et à l’installation des mises à jour.

<!---HONumber=AcomDC_0323_2016-->