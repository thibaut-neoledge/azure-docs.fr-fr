Ensuite, si des serveurs du cluster exécutent Windows Server 2008 R2 ou Windows Server 2012, vous devez vérifier que le correctif logiciel [KB2854082](http://support.microsoft.com/kb/2854082) est installé sur chacun des serveurs locaux ou des machines virtuelles Azure qui font partie du cluster. Ce correctif logiciel doit également être installé sur tout serveur ou une machine virtuelle faisant partie du cluster, mais pas du groupe de disponibilité.

Dans la session Bureau à distance, téléchargez [KB2854082](http://support.microsoft.com/kb/2854082) dans un répertoire local pour chacun des nœuds du cluster. Ensuite, installez le correctif logiciel sur chacun des nœuds du cluster séquentiellement. Si le service de cluster est en cours d'exécution sur le nœud de cluster, le serveur est redémarré à la fin de l'installation du correctif.

> [!WARNING]
> L'arrêt du service de cluster ou le redémarrage du serveur affecte l'intégrité du quorum de votre cluster et le groupe de disponibilité et peut entraîner la mise hors connexion du cluster. Pour assurer la haute disponibilité de votre cluster lors de l'installation, vérifiez que :
> 
> * l'intégrité du quorum du cluster est optimale, 
> * tous les nœuds du cluster sont en ligne avant l'installation du correctif logiciel sur n'importe quel nœud, et
> * autorisez l'installation complète du correctif sur un nœud, notamment avec un redémarrage complet du serveur, avant d'installer le correctif logiciel sur tout autre nœud du cluster.
> 
> 

