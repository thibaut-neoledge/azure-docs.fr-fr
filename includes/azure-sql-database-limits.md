Ressource|Limite par défaut
---|---
Taille de base de données|Varie selon le niveau de performance <sup>1</sup>
Connexions|Varie selon le niveau de performance <sup>1</sup>
Utilisation de la mémoire|Allocation de mémoire de 16 Mo pendant plus de 20 secondes
Sessions|Varie selon le niveau de performance <sup>1</sup>
Taille de tempdb|5 GO
Durée de la transaction|24 heures<sup>2</sup>
Verrous par transaction|1 million
Taille par transaction|2 Go
Pourcentage d’espace total de journalisation utilisé par transaction|20 %
Nombre maximal de demandes simultanées (threads de travail)|Varie selon le niveau de performance <sup>1</sup>


<sup>1</sup>La base de données SQL a différents niveaux de performances, tels que De base, Standard et Premium. Standard et Premium sont également divisés en niveaux de performance. Pour les limites détaillées par niveau et par niveau de service, consultez la rubrique [Niveaux du service Azure SQL Database et niveaux de performances](https://msdn.microsoft.com/library/azure/dn741336.aspx).

<sup>2</sup>Si la transaction verrouille une ressource requise par une tâche système sous-jacente, la durée maximale est de 20 secondes.

<!---HONumber=August15_HO7-->