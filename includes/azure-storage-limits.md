Ressource|Limite par défaut
---|---
Nombre maximal de comptes de stockage par abonnement|100<sup>1</sup>
To par compte de stockage|500 To
Nombre maximal de conteneurs d'objets blob, de partages de fichiers, de tables, de files d'attente, d'entités ou de messages par compte de stockage|La seule limite est celle de 500 To de la capacité du compte de stockage
Taille maximale d'un conteneur d'objets blob, d'une table ou d'une file d'attente|500 To
Nombre maximal de blocs dans un objet blob de blocs|50 000
Taille maximale d'un bloc dans un objet blob de blocs|4 Mo
Taille maximale d'un objet blob de blocs|50 000 X 4 Mo (195 Go environ) 
Taille maximale d'un objet blob de pages |1 To
Taille maximale d'une entité de table|1 Mo
Nombre maximal de propriétés dans une entité de table|252
Taille maximale d'un message dans une file d'attente|64 Ko
Taille maximale d'un partage de fichiers|5 To
Taille maximale d'un fichier dans un partage de fichiers|1 To
Nombre maximal de fichiers dans un partage de fichiers|La seule limite est celle de 5 To de la capacité totale du partage de fichiers
Nombre maximal de conteneurs d'objets blob, de partages de fichiers, de tables, de files d'attente, d'entités ou de messages par compte de stockage|La seule limite est celle de 500 To de la capacité du compte de stockage
Maximum 8 Ko d’opérations E/S par disque persistant (machine virtuelle avec niveau de base)|300<sup>2</sup>
Maximum 8 Ko d’opérations E/S par disque persistant (machine virtuelle avec niveau Standard)|500<sup>2</sup>
Taux de demandes total (objets de 1 Ko) par compte de stockage|Jusqu’à 20 000 opérations E/S, entités par seconde ou messages par seconde
Débit cible pour un objet blob unique|Jusqu'à 60 Mo par seconde ou jusqu'à 500 demandes par seconde
Débit cible pour une file d'attente unique (messages de 1 Ko)|Jusqu'à 2 000 messages par seconde
Débit cible pour une partition de table unique (entités de 1 Ko)|Jusqu'à 2 000 entités par seconde
Débit cible pour un partage de fichier unique (version préliminaire)|Jusqu’à 60 Mo par seconde
Entrée max.<sup>3</sup> par compte de stockage (régions des États-Unis)|10 Gbit/s si GRS/ZRS<sup>4</sup> est activé, 20 Gbit/s pour LRS
Sortie max.<sup>3</sup> par compte de stockage (régions des États-Unis)|20 Gbit/s si GRS/ZRS<sup>4</sup> est activé, 30 Gbit/s pour LRS
Entrée max.<sup>3</sup> par compte de stockage (régions d’Europe et d’Asie)|5 Gbit/s si GRS/ZRS<sup>4</sup> est activé, 10 Gbit/s pour LRS
Sortie max.<sup>3</sup> par compte de stockage (régions d’Europe et d’Asie)|10 Gbit/s si GRS/ZRS<sup>4</sup> est activé, 15 Gbit/s pour LRS

<sup>1</sup>Si vous avez besoin de plus de 100 comptes de stockage, contactez le [Support Azure](http://azure.microsoft.com/support/faq/) pour obtenir une assistance.

<sup>2</sup>La limite de débit d’appels pour un compte de stockage est de 20 000 opérations d’E/S. Pour éviter toute limitation lorsqu’une machine virtuelle utilise le nombre maximal d’E/S par disque, vérifiez que le nombre total d’E/S sur l’ensemble des disques durs virtuels des machines virtuelles ne dépasse pas la limite du compte de stockage (20 000 E/S).

Vous pouvez calculer approximativement le nombre de disques fortement sollicités pris en charge par un seul compte de stockage en vous basant sur la limite de transaction. Par exemple, pour une machine virtuelle de niveau de base, le nombre maximal de disques fortement sollicités est d’environ 66 (20 000/300 E/S par disque) et pour une machine virtuelle de niveau Standard, il est d’environ 40 (20 000/500 E/S par disque). Toutefois, le compte de stockage peut prendre en charge un plus grand nombre de disques s’ils ne sont pas tous fortement sollicités en même temps.

<sup>3</sup>*Entrantes* désigne toutes les données (demandes) envoyées à un compte de stockage. *Sortantes* désigne toutes les données (réponses) reçues d’un compte de stockage.

<sup>4</sup>GRS fait référence au stockage géo-redondant ZRS fait référence à un stockage redondant dans une zone et est disponible uniquement pour des objets Blob de blocs. LRS fait référence à un stockage localement redondant.

<!---HONumber=August15_HO8-->