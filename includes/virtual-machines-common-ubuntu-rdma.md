1. Installer dapl, rdmacm, ibverbs et mlx4

  ```bash
  sudo apt-get update

  sudo apt-get install libdapl2 libmlx4-1

  ```

2. Dans /etc/waagent.conf, activez RDMA en supprimant les marques de commentaire dans les lignes de configuration suivantes. Un accès racine est requis pour modifier ce fichier.
  
  ```
  OS.EnableRDMA=y

  OS.UpdateRdmaDriver=y
  ```

3. Ajoutez ou modifiez les paramètres de mémoire suivants en Ko dans le fichier /etc/security/limits.conf. Un accès racine est requis pour modifier ce fichier. À des fins de test, vous pouvez définir pour memlock une valeur illimitée. Par exemple : `<User or group name>   hard    memlock   unlimited`.

  ```
  <User or group name> hard    memlock <memory required for your application in KB>

  <User or group name> soft    memlock <memory required for your application in KB>
  ```
  
4. Installez la bibliothèque Intel MPI. [Achetez et téléchargez](https://software.intel.com/intel-mpi-library/) la bibliothèque auprès d’Intel ou téléchargez la [version d’évaluation gratuite](https://registrationcenter.intel.com/en/forms/?productid=1740).

  ```bash
 wget http://registrationcenter-download.intel.com/akdlm/irc_nas/tec/11595/l_mpi_2017.3.196.tgz
   ```
 
 Pour les étapes d’installation, consultez le [Guide d’installation de la bibliothèque Intel MPI](http://registrationcenter-download.intel.com/akdlm/irc_nas/1718/INSTALL.html?lang=en&fileExt=.html).

5. Activez ptrace pour les processus non-racine et non-débogueur (nécessaire pour les versions les plus récentes d’Intel MPI).
 
  ```bash
  echo 0 | sudo tee /proc/sys/kernel/yama/ptrace_scope
  ```
