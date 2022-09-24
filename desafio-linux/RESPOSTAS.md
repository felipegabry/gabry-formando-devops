 
# 1 - 
 Durante a inicialização da VM, na janela do grub, pressione a tecla `ESC` para parar a inicialização, e em seguida selecione a opção de inicialização desejada e aperte a tecla `e` para editar.
 No menu de edição, encontre o parâmetro do kernel `ro` e altere para `rw`, adiciona em sequência o parâmetro `init=/sysroot/bin/sh`, dessa forma: antes (ro), depois (rw init=/sysroot/bin/sh).
 Em seguida utilize o CTRL + X para entrar no modo single-user, e rode o comando `chroot /sysroot` para converter o sistema de arquivos root para o modo escrita e leitura.
 Agora poderemos mudar a senha do root, para isso, use o comando `passwd root`, insira uma senha duas vezes, no meu caso (123Gabry).
 Após isso, use o comando `touch /.autorelabel` para iniciar o SELinux relabelling. Após isso, `exit` para salvar as alterações e sair, e `reboot` para reiniciar a VM.
 Ao acessar a VM como root, instalei o nano (editor de texto mais amigável) e editei o arquivo`/etc/sudoers` adicionando no final do arquivo a linha (vagrant  ALL=(ALL) NOPASSWD:ALL) para que o usuário vagrant tivesse permissão de sudo e não precisasse de senha.

# 2.
# 2.1 - 
 Adicionei o grupo getup com o comando `sudo groupadd -g 2222 getup`, Criei o usuário getup com o comando `sudo useradd -g 2222 -G bin -u 1111 getup.
 Editei o arquivo `/etc/sudoers` e adicionei ao final do arquivo a linha (getup  ALL=(ALL) NOPASSWD:ALL).

# 3.
# 3.1 - 
 Usei o comando `sudo nano /etc/ssh/sshd_config` e alterei o PasswordAuthentication e o PermitRootLogin para 'no'.

# 3.2 - 
 Dentro do diretório /home/vagrant/.ssh, criei o par de chaves com o comando `ssh-keygen -t ecdsa -b 256 -C "vagrant@10.0.0.105", e adicionei a chave pública no arquivo authorized_keys `cat vagrant.pub > authorized_keys`.

# 3.3 - 
 Alterei a senha do usuário devel.
 Instalei o git na VM e baixei a chave para a home do usuário vagrant.
 Decodifiquei e descompactei a chave com o comando 'base64 -d id_rsa-desafio-linux-devel.gz.b64 | gzip -d > id_rsa-desafio-linux'.
 Com o comando ssh `devel@10.0.0.105` já foi possível se conectar via ssh com o usuário devel.
 
# 4 - 
 Com o comando `sudo nano /etc/nginx/nginx.conf` editei o arquivo de configuração nginx, nos parâmetros do servidor, ele apontava para a porta 90, alterei para 80, e adicionei um ';' ao final da última linha.
 Após isso tentei reiniciar o serviço nginx, mas não foi possível, ele reclamava de um parâmetro B, no arquivo /lib/systemd/system/nginx.service, removi o parâmetro -BROKEN do comando ExecStart.
 Após isso dei um reload no daemon e subi o nginx.
 Resultado do curl = Duas palavrinhas pra você: para, béns! 
 
 Não consegui terminar o desafio, avancei até aqui no último dia para entrega.
