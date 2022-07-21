# ProjetoLinux

Passo a passo da instalação e configuração do Oracle Linux: 

 

    1 - Ir em “https://yum.oracle.com/oracle-linux-downloads.html?source=:ow:o:p:nav:121620LinuxHero_br&intcmp=:ow:o:p:nav:121620LinuxHero_br” e baixar a ISO do Oracle Linux. 

    2 - Criar uma VM no VirtualBox e configura-la do modo que quiser. 

    3 - Ir em “Configurações” -> “Rede” -> Adaptador 2 -> Botar uma placa no modo bridge em “Conectado a:” 

    4 - Ligar a máquina e rodar a ISO baixada no site da Oracle Linux. 

    5 - Escolher a linguagem para ser utilizada no sistema (Recomendado ser Inglês). 

    6 - Ir em “Time & Date” e modificar o fuso horário na qual o sistema vai utilizar para o seu. 

    7 - Ir em “Software Selection” e escolher “Minimal Install” para instalar o sistema somente com a linha de comando. 

    8 - Ir em “Installation Destination”, selecionar o “Local Standard Disks” e configurar as partições clicando em “custom” no “Storage Configuration” e depois em “Done”, após isso, incluir as repartições: 
       /boot xfs 
       /swap swap 
       /home xfs 
       /var xfs 
       /tmp xfs 
       / xfs 
    Todas eles podem ter o tamanho que quiser. 

    9 - Ir em “Network & Host Name” e ligar o botão de Ethernet. 

    10 - (Opcional) Ir em “Security Policy” e escolher uma das opções. 

    11 - Ir em “Root Password” e insira algo para servir de senha para o Root. 

    12 - Ir em “User Creation” e insira o nome completo e o nome que será usado pelo sistema do usuário, clique em “Make this user administrator” para que ele tenha privilégios de administrador e insira uma senha. 

    13 - Clique em “Begin Installation” para começar a instalação do sistema. 

    14 - Após a instalação terminar, clique em “Reboot System” 

    15 - Quando a máquina reiniciar, aceito os termos de licença da Oracle e clique em “Finish Configuration” 

    16 - Quando aparecer o terminal, escreva o nome do usuário feito no passo XI e a sua senha. 

    17 - Instalar o nano para facilitar o processo de edição de arquivos com “sudo dnf install nano” 

    18 - Instalar o Git com “sudo dnf install git” para que seja possivel mandar repositórios de arquivos para lugares como o GitHub 

    19 - Instalar o bind-utils com “sudo dnf install bind-utils" para que certos comandos em relação a configuração de DNS estejam disponiveis. 

    20 - Mudar o hostname do computador fazendo o comando “sudo nano /etc/hostname” 

    21 - Mudar o nome do DNS usando “sudo nano /etc/hosts” e incluindo uma nova linha com o seguinte escrito: “10.0.2.15 kafkadockerlab” 

    22 - Reiniciar a máquina com “reboot” para que as mudanças entrem em efeito. 

    23 - Estabelecer IP fixo da placa NAT usando “sudo nano /etc/sysconfig/network-scripts/ifcfg-enp0s3” e adicionando as seguintes linhas: 
     
    IPADDR=10.0.2.15 
    NETMASK=255.255.255.0 
    GATEWAY=10.0.2.2 
    DNS1=8.8.8.8 
     
    Não se esquecer de também alterar a linha do BOOTPROTO, substituindo o “dhcp” por “none” ou “static”. 

    24 - Criar uma partição de 12GB para o Docker fazendo o seguinte:	a. Desligar a maquina 
    	       b. Ir em “Armazenamento” no VirtualBox e adicionar um Disco Rígido (HD) de 12GB. 
             c. Ligar a máquina virtual  
             d. Escrever “lsblk” no terminal para verificar se o HD foi criado 
             e. Digite “sudo fdisk /dev/sdb” para iniciar o processo de particionamento 
             f. Digite o seguinte nas proximas opções: 
                           1. n – nova partição 
                           2. p - partição primária 
                           3. 1 - número de partição 
                           4. Aperte Enter 
                           5. Aperte Enter 
                           6. w – salvar 
             g. Após isso, crie um sistema de arquivos com o comando “sudo mkfs –t ext4 /dev/sdb1” 
             h. Crie um diretório onde será montado o SDB1 com o comando “sudo mkdir /var/lib/docker” 
             i. Edite o arquivo fstab com o comando “sudo nano /etc/fstab” e adicione a seguinte linha: 
    “/dev/sdb1 /var/lib/Docker ext4 defaults 0 0” 
    Isso é feito para montar o SDB1 automaticamente dentro do diretório criado. 

    25 - Instale o Docker usando “sudo dnf install docker” 

    26 - Configurar o SSH para permitir a conexão remota com outras maquinas fazendo o seguinte: 
    	a. No computador que irá servir de servidor, verifique se o programa openssh-server está instalado na sua máquina. Caso não esteja, faça a sua instalação usando “sudo dnf install openssh-server”, depois faça o comando “sudo systemctl enable –now sshd” para iniciar o DAEMON do SSH. 
    	     b. Va em “sudo nano /etc/ssh/sshd_config” e mude a linha que diz “PermitRootLogin” de yes para no. 
           c. Reinicie o sistema de SSH com o comando “sudo systemctl restart sshd” para que as mudanças feitas no sshd_config entrem em efeito. 
           d. Adicione o SSH ao Firewall para não ter problema de conexão usando “sudo firewall-cmd –permanent –add-service=ssh” 
           e. Reinicie o Firewall para as mudanças entrarem em efeito com “sudo firewall-cmd –reload" 
    	 f. Verifique se o SSH foi realmente adicionado com o comando “sudo firewall-cmd –list-all" e procure por ele na linha de “services” 
           g. No computador que irá servir de cliente, verifique se o openssh-clients está instalado na máquina. Caso não esteja faça o comando “sudo dnf install openssh-clients" para instala-lo. 
            h. Teste a conexão com o servidor usando o comando “ssh <nomedousuario>@<ipdamaquinaservidor> -p 22” e escreva yes quando perguntar algo. 
            i. Insira a senha de usuário do administrador do computador servidor. Se conseguiu ver o nome do usuário do servidor no terminal, você se conectou com sucesso! 
            j. Escreva exit para sair do computador do servidor. 
            k. Voltando no computador do cliente, escreva “ssh-keygen” para gerar uma chave pública e uma chave privada de conexão, aperte enter quando pedir algo. 
            l. Escreva “ssh-copy-id –i ~/.ssh/id_rsa.pub <nomedousuario@<iddamaquinaservidor>” para mandar a chave pública para a máquina servidor. 
           m. Agora, tente se conectar novamente usando o comando “ssh <nomedousuario>@<ipdamaquinaservidor> -i ~/.ssh/id_rsa”. 
            n. Se der certo, note que não foi necessário botar uma senha dessa vez, já que foi estabelecido uma relação de confiança entre as VMs. 
            o. Volte para o arquivo “sudo nano /etc/ssh/sshd_config” e mude a linha que diz “PasswordAuthentication” de yes para no. 

    27 - Tudo pronto!
