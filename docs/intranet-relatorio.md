# Introdução

No mundo atual, onde a conectividade é uma peça fundamental para o funcionamento eficiente de organizações, a intranet se destaca como uma poderosa ferramenta que possibilita o compartilhamento seguro e rápido de informações e serviços internos. Nesse contexto, o presente trabalho tem como objetivo a construção de uma intranet robusta, baseada no protocolo TCP/IP, visando fornecer aos usuários um ambiente interno eficiente e funcional.

A tarefa primordial a ser empreendida nesta atividade é a configuração dos principais serviços de uma intranet TCP/IP. O aluno, de forma minuciosa, terá a responsabilidade de implementar e otimizar os serviços básicos, incluindo o DNS (Sistema de Nomes de Domínios), o SMTP (Protocolo de Transferência de Emails) juntamente com o cliente de e-mail, e a WWW (World Wide Web), a fim de proporcionar uma experiência integrada e agradável aos usuários da intranet.

#  DNS

	Montagem de rede interconectada para o experimento
	
	1- Certifique-se de ter o Docker e o Docker Compose instalados em seu sistema.
	2- Crie um arquivo chamado docker-compose.yml e adicione o seguinte conteúdo:


version: '3'

services:
  machine1:
    build:
      context: .
    networks:
      mynetwork:
        ipv4_address: 192.168.10.1


  machine2:
    build:
      context: .
    networks:
      mynetwork:
        ipv4_address: 192.168.10.2


networks:
  mynetwork:
    driver: bridge
    ipam:
      config:
        - subnet: 192.168.10.0/24

3- Em paralelo crie um arquivo chamado Dockerfile e adicione o seguinte conteúdo:
FROM alpine
CMD tail -f /dev/null
4- No terminal, navegue até o diretório onde você salvou os arquivos 'docker-compose.yml' e 'Dockerfile'.
5- Execute o comando a seguir para construir as imagens e iniciar os containers:
docker-compose up --build -d
Aguarde até que os containers sejam criados e iniciados. Eles serão nomeados como nomedopdiretório_machine1_1 e nomedopdiretório_machine2_1
6- Para testar a conectividade entre os containers usando o comando ping, execute os seguintes comandos:
docker exec nomedopdiretório_machine1_1 ping 192.168.10.2
docker exec nomedopdiretório_machine2_1 ping 192.168.10.1


Os comandos acima executarão o ping de um container para o outro usando os endereços IP fornecidos. Se a conectividade estiver correta, você receberá respostas dos pings e poderá confirmar que os containers estão se comunicando com sucesso na mesma rede local.
OBS: Lembre-se de substituir nomedopdiretório pelo nome do diretório em que você salvou os arquivos docker-compose.yml e Dockerfile.


# Configuração estática local de endereços/nomes (através arquivo hosts)
1- Identifique os nomes que você deseja atribuir às máquinas da rede montada. Por exemplo, vamos usar os nomes "machine1" e "machine2".
2- Abra um terminal e execute o seguinte comando para acessar o shell de um dos containers:
docker exec -it nomedopdiretório_machine1_1 sh
Substitua nomedopdiretório pelo nome do diretório em que você salvou os arquivos do Docker Compose.
3- Dentro do shell do container, edite o arquivo /etc/hosts usando um editor de texto, como o vi:
vi /etc/hosts
4- No arquivo /etc/hosts, adicione uma linha para cada nome que você deseja atribuir ao endereço IP correspondente. Por exemplo:
machine1
127.0.0.1       localhost
192.168.10.1    machine1
192.168.10.2    machine2 <-- Acrescente esse trecho
~

machine2
127.0.0.1       localhost
192.168.10.2    machine2
192.168.10.1    machine1 <-- Acrescente esse trecho
~

## Varia de caso a caso mas será algo parecido com isso

5- Salve e feche o arquivo.
6- Agora você configurou as associações fixas e estáticas entre os nomes e os endereços IP dentro dos containers.
Para testar a resolução de nomes local usando o arquivo /etc/hosts, execute o comando ping usando os nomes em vez dos endereços IP. Por exemplo:
docker exec -it nomedopdiretório_machine1_1 ping machine2
docker exec -it nomedopdiretório_machine2_1 ping machine1
Você deve ver os pacotes de ping sendo enviados e recebidos entre os containers usando os nomes configurados.
7- Para verificar o nome atual do sistema dentro de um container, use o comando hostname:
docker exec -it nomedopdiretório_machine1_1 hostname
Para alterar o nome do sistema dentro de um container, use o comando hostname seguido pelo novo nome:
docker exec -it nomedopdiretório_machine1_1 hostname novo-nome
Lembre-se de substituir nomedopdiretório pelo nome do diretório em que você salvou os arquivos do Docker Compose.
Caso você receba esse erro:
hostname: sethostname: Operation not permitted
Basta acrescentar o hostname ao arquivo docker-compose.yml

version: '3'

services:
  machine1:
    build:
      context: .
    networks:
      mynetwork:
        ipv4_address: 192.168.10.1
    hostname: machine1

  machine2:
    build:
      context: .
    networks:
      mynetwork:
        ipv4_address: 192.168.10.2
    hostname: machine2
    

networks:
  mynetwork:
    driver: bridge
    ipam:
      config:
        - subnet: 192.168.10.0/24

e recriar os contâiners com:
docker-compose up -d
8- Para verificar o domínio configurado no sistema dentro de um container, use o comando dnsdomainname.
OBS: O comando dnsdomainname não está disponível dentro dos contêineres Docker. No contexto dos contêineres, não existe um domínio configurado da mesma forma que em um sistema operacional completo.
Os contêineres Docker são isolados e têm seu próprio namespace de rede. Eles não estão cientes dos domínios configurados fora do contêiner. Portanto, ao executar o comando dnsdomainname dentro de um contêiner, ele não retornará nada.

Configurando o DNS no docker
9- Altere o arquivo resolv.conf:
Entre no container criado:
docker exec -it dns_machine2_1 /bin/bash/

Digite:
vi /etc/resolv.conf
Dentro do resolv.conf, escreva:
nameserver 127.0.0.11
options edns0 trust-ad ndots:0
domain queropassar.com
nameserver 192.168.10.2
nameserver 192.168.10.3
10- Altere os arquivos named.conf.local
Acesse:
cd /etc/bind/
Inicie alterando o arquivo named.conf.local
//
// Do any local configuration here
//

// Consider adding the 1918 zones here, if they are not used in your
// organization
//include "/etc/bind/zones.rfc1918";
zone "queropassar.com" in {
   type master;
   file "/etc/bind/db.queropassar";
};
/ Zona para dns reverso
zone "10.168.192.in-addr.arpa" in {
    type master;
    file "/etc/bind/db.queroreprovar";
};
Depois crie os arquivos db.dns e db.dnsreverse, com seus respectivos nomes:
Primeiro o db.dns:
/ Cada definição de master deve se iniciar com uma entrada SOA
/ Ela indica o servidor de nomes para o domínio em questão e parâmetros de operação
@ IN SOA flpp.queropassar.com. root.flpp.queropassar.com. (
2022092601 ;numero serial - deve ser incrementado a cada mudança neste arquivo
21600 ;refresh - das informa��ões para slaves
1800 ;retry �~@~S tempo entre as tentativas
604800 ;expire - tempo para se desistir de contactar master
86400 ) ;mí nimo - tempo a manter a informação no cache (TTL)
IN NS flpp.queropassar.com.
queropassar.com. IN MX 10 FERNANDO.queropassar.com. ;entrada MX (mail server)
localhost IN A 127.0.0.1
machine2 IN A 192.168.10.2
machine3 IN A 192.168.10.3
flpp IN A 192.168.10.66
FERNANDO IN A 192.168.10.100
Primeiro o db.dnsreverse:
// Realiza a resolução reversa
// O tipo PTR significa um alias para o endereço IP
@ IN SOA flpp.queropassar.com. root.flpp.queropassar.com. (
2022092601
21600
1800
604800
86400 )
IN NS flpp.queropassar.com.
2 IN PTR machine2.queropassar.com.
3 IN PTR machine3.queropassar.com.
66 IN PTR flpp.queropassar.com.
100 IN PTR FERNANDO.queropassar.com.
11- Rode o comando para fazer as mudanças realizadas:
/etc/init.d/bind9 start
ou
sudo /usr/sbin/named -f -g -d 1
Esse segundo comando serve para rodar mostrando o que está sendo feito durante o processo.
12- Testando o DNS
Rode:
nslookup
Consulte:
server <nome ou IP>

# SMTP

Passo 1: Instalação do Postfix
sudo apt install postfix
Respostas durante a instalação:
Ok
Site da Internet (Internet Site)
pv-notebook
Passo 2: Configuração do Postfix
Durante a configuração, responda as seguintes opções:
Ok
Site da Internet (Internet Site)
pv-notebook NONE
chmod.com.br
chmod.com.br, pv-notebook, pv-notebook, localhost.localdomain, , localhost_
Não
127.0.0.0/8 [::ffff:127.0.0.0]/104 [::1]/128_
Limite de tamanho: 0
Caractere de extensão: +
Todos (all)
Passo 3: Iniciando o Postfix
sudo service postfix start
Passo 4: Adicionando usuários ao grupo
adduser francisco
adduser leonardo
adduser paulo
adduser pedro
Senha para todos os usuários: redes
Passo 5: Instalação do utilitário de email
sudo apt install mailutils
Passo 6: Enviando uma mensagem de email
Use o comando abaixo substituindo "nomeuser" pelo usuário desejado e "chmod.com.br" pelo domínio correto:
mail nomeuser@chmod.com.br
Escreva a mensagem e, quando terminar, utilize o comando CTRL + D para encerrar e enviar.
Passo 7: Ver mensagens enviadas
As mensagens enviadas estarão na pasta:
/var/mail/

Acesse o usuário e veja as mensagens.
POP3 e IMAP com Dovecot
Passo 1: Instalação do Dovecot
sudo apt install dovecot-core dovecot-imapd dovecot-pop3d
Passo 2: Configuração do Dovecot para aceitar POP3 e IMAP
Edite o arquivo de configuração:
sudo nano /etc/dovecot/dovecot.conf
Adicione ou descomente as seguintes linhas para aceitar POP3 e IMAP:
protocols = imap pop3

Realize o restart do Dovecot:
sudo systemctl restart dovecot
Agora o Dovecot está configurado para suportar ambos os protocolos, POP3 e IMAP. O servidor estará pronto para receber conexões dos clientes de email.

SMTP com Docker
Para fazer a instalação e configuração do Postfix usando Docker, você pode seguir os seguintes passos:
Passo 1: Acesse o diretório:
cd SMTP
Passo 2: Crie um arquivo chamado Dockerfile com o seguinte conteúdo:
// Use a imagem base do Ubuntu (ou outra imagem de sua preferência)
FROM ubuntu:latest

// Instale o servidor de e-mail Postfix
RUN apt-get update && apt-get install -y postfix

// Instale o mail
RUN apt install mailutils -y

// Copie o arquivo de configuração pré-configurado para dentro do contêiner
COPY postfix-config.txt /etc/postfix/main.cf

// Inicie o serviço do Postfix quando o contêiner for iniciado
CMD service postfix start && tail -f /dev/null
Passo 3: Crie um arquivo chamado postfix-config.txt com o seguinte conteúdo:
// Configuração do Postfix
myhostname = chmod.com.br
mydestination = chmod.com.br, localhost.localdomain, localhost
mynetworks = 127.0.0.0/8, 192.168.10.0/24
inet_interfaces = all

Substitua "chmod.com.br" pelo nome do seu servidor de e-mail (definido no DNS) e o endereço "192.168.10.0/24" pelo endereço de rede que você definiu para a rede local da sua bancada.
Passo 4: Construa a imagem Docker:
docker build -t smtp .
Passo 5: Execute o container Docker:
docker run -d --name smtp -p 2525:25 smtp
Agora você tem um servidor de e-mail Postfix em execução dentro do contêiner Docker. Você pode adicionar usuários e enviar e-mails da mesma forma como fez anteriormente:
docker exec -it smtp bash

// Agora dentro do contêiner, adicione os usuários
adduser francisco
adduser leonardo
adduser paulo
adduser pedro

// Enviando os e-mails
mail francisco@chmod.com.br
mail leonardo@chmod.com.br
mail paulo@chmod.com.br
mail pedro@chmod.com.br
Escreva a mensagem e, quando terminar, utilize o comando CTRL + D para encerrar e enviar.
Os e-mails enviados estarão disponíveis nos respectivos arquivos dentro do diretório /var/mail dentro do contêiner. Para ver os e-mails recebidos, você pode executar:
docker exec -it smtp /bin/bash
// Agora dentro do contêiner
cat /var/mail/francisco
cat /var/mail/gabriela
cat /var/mail/murilo
cat /var/mail/pedro
Lembrando que esse é um exemplo básico para executar o Postfix dentro do Docker, e em um ambiente real, você precisaria fazer algumas outras configurações para garantir a segurança e autenticação correta dos usuários.

# WWW

	1 - Instalando o apache no servidor DNS:

		sudo apt install apache2
 
2 - Iniciando e testando o apache
	
Foi utilizado o comando sudo service apache2 start para inicializar o programa do apache, depois disso foi criada uma página html de teste contendo apenas o título “Olá Mundo” em seu conteúdo, essa página foi copiada para o arquivo teste.html dentro da pasta /var/www/html. Ao abrir a página www.chmod.com.br/index.html foi possível ver a página descrita. 

Obs: Após o teste, o html foi alterado para conter as questões dos experimentos.

# PGP

O PGP é uma tecnologia de criptografia assimétrica que foi desenvolvida para proteger a privacidade e a autenticidade das comunicações eletrônicas, especialmente e-mails. Foi criado por Phil Zimmermann em 1991 e rapidamente ganhou popularidade por ser uma solução eficaz para garantir a segurança das informações transmitidas pela internet.

A criptografia assimétrica, também conhecida como criptografia de chave pública, utiliza um par de chaves: uma chave pública e uma chave privada. Cada usuário possui seu próprio par de chaves. A chave pública é compartilhada com outras pessoas, enquanto a chave privada é mantida em segredo e conhecida apenas pelo seu proprietário.

Vamos entender como o PGP funciona passo a passo:

Geração das chaves: O usuário gera seu par de chaves, uma pública e uma privada. A chave pública é usada para criptografar as mensagens e é compartilhada livremente com os outros usuários, enquanto a chave privada é protegida e armazenada com segurança pelo proprietário.


Criptografia de Mensagem: Quando um remetente deseja enviar uma mensagem para um destinatário específico, ele utiliza a chave pública do destinatário para criptografar a mensagem. A mensagem criptografada só pode ser descriptografada pelo destinatário, que é o único detentor da chave privada correspondente.


Assinatura Digital: Além da criptografia, o PGP permite que os usuários assinem digitalmente suas mensagens. A assinatura digital é criada usando a chave privada do remetente e garante a autenticidade e integridade da mensagem. O destinatário pode verificar a assinatura usando a chave pública do remetente para garantir que a mensagem não foi adulterada durante a transmissão.


Servidor de Chaves (Chaveiro): Para facilitar o compartilhamento seguro de chaves públicas, existe um serviço chamado servidor de chaves ou chaveiro. Nesse servidor, os usuários podem publicar suas chaves públicas para que outras pessoas possam encontrá-las e usá-las para criptografar mensagens. O servidor de chaves também permite a busca de chaves públicas de outros usuários.


Confiança em Chaves: O PGP também permite que os usuários estabeleçam níveis de confiança nas chaves públicas de outras pessoas. Ao encontrar a chave pública de alguém em um servidor de chaves, o usuário pode validar a autenticidade dessa chave e atribuir-lhe um nível de confiança. Isso é útil para evitar ataques de homens do meio, onde uma chave falsa é apresentada como sendo de outra pessoa.








