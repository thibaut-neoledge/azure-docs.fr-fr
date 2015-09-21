<properties
   pageTitle="Tableau des limites de sauvegarde Azure"
   description="Décrit les limites du système pour la sauvegarde Azure."
   services="backup"
   documentationCenter="NA"
   authors="Jim-Parker"
   manager="jwhit"
   editor="" />
<tags  ms.service="backup" ms.devlang="NA" ms.topic="article" ms.tgt_pltfrm="NA" ms.workload="TBD" ms.date="08/26/2015" ms.author="jimpark"; "aashishr" />


Les limites suivantes s’appliquent à la sauvegarde Azure.

| Identificateur de la limite | Limite par défaut |
|---|---|
|Nombre de serveurs/machines pouvant être enregistrés pour chaque coffre|50|
|Taille d’une source de données pour les données stockées dans le stockage de l’archivage Azure|54 400 Go max<sup>1</sup>|
|Nombre de coffres de sauvegarde pouvant être créés dans chaque abonnement Azure|25|
|Nombre de sauvegardes pouvant être planifiées par jour|3 par jour pour Windows Server/Client <br/>2 par jour pour SCDPM|
|Nombre de points de récupération pouvant être créés|366<sup>2</sup>|
|Disques de données attachés à une machine virtuelle Azure pour la sauvegarde|5|

- <sup>1</sup>La limite de 54 400 Go ne s’applique pas à la sauvegarde de la machine virtuelle IaaS.
- <sup>2</sup>Vous pouvez utiliser n’importe quelle permutation pour arriver à un nombre inférieur à 366.

<!---HONumber=Sept15_HO2-->