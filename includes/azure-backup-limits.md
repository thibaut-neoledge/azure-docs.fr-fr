
Les limites suivantes s’appliquent à la sauvegarde Azure.

| Identificateur de la limite | Limite par défaut |
|---|---|
|Nombre de serveurs/machines pouvant être enregistrés pour chaque coffre|50|
|Taille d’une source de données pour les données stockées dans le stockage de l’archivage Azure|1,65 To max<sup>1</sup>|
|Nombre de coffres de sauvegarde pouvant être créés dans chaque abonnement Azure|25|
|Nombre de sauvegardes pouvant être planifiées par jour|3 par jour pour Windows Server/Client <br/>2 par jour pour SCDPM|
|Nombre de points de récupération pouvant être créés|366<sup>2</sup>|
|Disques de données attachés à une machine virtuelle Azure pour la sauvegarde|5|

- <sup>1</sup>La limite de 1,65 To ne s’applique pas à la sauvegarde de la machine virtuelle IaaS.
- <sup>2</sup>Vous pouvez utiliser n’importe quelle permutation pour arriver à un nombre inférieur à 366.

<!---HONumber=July15_HO5-->