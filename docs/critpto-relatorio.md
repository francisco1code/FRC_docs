# Introdução
A criptografia é uma das áreas fundamentais da segurança da informação, desempenhando um papel crucial na proteção de dados sensíveis e na garantia da privacidade em ambientes digitais. Nesse contexto, o presente relatório apresenta uma exploração aprofundada dos conceitos e técnicas relacionadas à criptografia simétrica e assimétrica, através da realização de atividades práticas em laboratório.

O objetivo principal desta pesquisa é proporcionar uma oportunidade única de ampliar os conhecimentos na área de criptografia, por meio de experiências práticas envolvendo dois dos mais renomados algoritmos criptográficos: DES (Data Encryption Standard) e RSA (Rivest-Shamir-Adleman).

O relatório descreve de forma breve o funcionamento  do algoritmo abordado em questão e a solução realizada, assim como uma breve descrição das dificuldades enfrentadas.

# Criptografia Simétrica

A criptografia simétrica é uma técnica em que o mesmo segredo ou chave é utilizado tanto para criptografar quanto para descriptografar a mensagem. Nesse processo, tanto o remetente quanto o destinatário precisam compartilhar previamente a chave secreta, tornando a segurança do sistema dependente da proteção dessa chave. O algoritmo de DES (Data Encryption Standard) é um dos exemplos mais notáveis de criptografia simétrica. Desenvolvido na década de 1970, o DES utiliza uma chave de 56 bits para cifrar blocos de dados de 64 bits, aplicando uma série de transformações como permutações e substituições para garantir a confidencialidade dos dados. 

# Uma breve descrição de cada código:

## des.c:
	
O código contém diversas estruturas de dados, constantes e funções relacionadas à criptografia DES, incluindo a permutação inicial da chave e da mensagem, as tabelas de substituição S-Box utilizadas durante o processo de criptografia, a geração das subchaves a partir da chave principal e a aplicação das etapas de criptografia e descriptografia em um bloco de dados de 64 bits.

Para criptografar uma mensagem, o código recebe uma chave e um bloco de dados e, a partir da chave, gera um conjunto de 16 subchaves para serem utilizadas nas rodadas do algoritmo. O bloco de dados passa por um processo de permutação inicial e, em seguida, é dividido em metades esquerda e direita. As rodadas de criptografia (16 no total) são executadas alternando as metades do bloco e aplicando diversas operações de substituição e permutação utilizando as subchaves geradas. O resultado é um bloco de dados criptografado.

## server.c:

O código é um programa C que implementa um servidor de transferência de arquivos BMP criptografados usando o algoritmo de criptografia DES (Data Encryption Standard). Um resumo do seu passo a passo:

1. Bibliotecas e constantes: O código inclui várias bibliotecas necessárias para executar funções de criptografia DES, manipulação de arquivos e sockets de rede. Também define constantes como a porta do servidor e o tamanho da chave DES.


2. Decifração (função "decrypt"): A função "decrypt" é responsável por decifrar o arquivo BMP criptografado usando a chave DES fornecida. O arquivo criptografado é lido, e blocos de dados de 8 bytes são processados usando a chave DES para descriptografá-los. O arquivo descriptografado é então salvo em um novo arquivo chamado "julia_decripted.bmp".


3. Recebendo a chave do cliente (função "recive_key"): Quando o cliente se conecta ao servidor, ele envia a chave de criptografia DES de 8 bytes para o servidor. Essa chave é recebida pela função "recive_key" no servidor.


4. Recebendo e descriptografando o arquivo BMP (função "receive_bmp_over_tcp"): A função "receive_bmp_over_tcp" é responsável por receber os dados do arquivo BMP criptografado do cliente e salvá-los em um arquivo chamado "encrypted_file.ecry". Em seguida, ele chama a função "decrypt" para descriptografar o arquivo salvo.


5. Configurando o servidor: O programa principal começa criando um socket TCP e o associa a uma porta especificada (SERVER_PORT) para aguardar conexões de clientes.


6. Aceitando conexões: O servidor fica em modo de escuta e aguarda a conexão de um cliente. Quando um cliente se conecta, o servidor aceita a conexão e obtém o descritor do socket do cliente (client_fd).


7. Processo de recebimento: O servidor recebe a chave criptográfica do cliente e, em seguida, invoca a função "receive_bmp_over_tcp" para receber o arquivo BMP criptografado, salvar em "encrypted_file.ecry" e, finalmente, descriptografá-lo chamando a função "decrypt".


8. Finalizando: Depois que o arquivo é descriptografado e salvo como "julia_decripted.bmp", o servidor fecha os sockets de cliente e servidor e encerra a execução.


O código permite que um cliente envie um arquivo BMP criptografado usando DES para o servidor, que por sua vez descriptografa o arquivo e o salva no servidor com o nome "julia_decripted.bmp". Note que este código parece ser parte de um sistema maior, possivelmente envolvendo um cliente que criptografa os arquivos antes de enviar ao servidor para descriptografia.

## client.c:

O código é um programa C que implementa um cliente de transferência de arquivos BMP criptografados usando o algoritmo de criptografia DES (Data Encryption Standard). Um resumo do seu passo a passo:

1. Bibliotecas e constantes: O código inclui várias bibliotecas necessárias para executar funções de criptografia DES, manipulação de arquivos e sockets de rede. Também define constantes como o endereço IP e a porta do servidor.
2. Geração da chave (função "gen_key"): A função "gen_key" é responsável por gerar uma chave de criptografia DES de 8 bytes.


3. Criptografia (função "encrypt"): A função "encrypt" é responsável por criptografar um arquivo BMP usando a chave DES gerada pela função "gen_key". O arquivo BMP é lido e dividido em blocos de 8 bytes, que são processados usando a chave DES para criptografá-los. O arquivo criptografado é salvo em um novo arquivo chamado "julia_encrypted".


4. Enviando a chave e o arquivo criptografado para o servidor: O cliente cria um socket TCP e tenta se conectar ao servidor usando o endereço IP e a porta fornecidos. 
Após a conexão bem-sucedida, o cliente envia a chave DES gerada pelo servidor para criptografar o arquivo BMP. Em seguida, ele chama a função "send_over_tcp_encrypted_bmp" para enviar o arquivo criptografado para o servidor.


5. Lendo o arquivo criptografado e enviando para o servidor: A função "send_over_tcp_encrypted_bmp" abre o arquivo criptografado gerado pela função "encrypt" e lê seu conteúdo para um buffer. Em seguida, o cliente envia o conteúdo do arquivo criptografado para o servidor através da conexão TCP.


6. Finalizando: Após o envio bem-sucedido do arquivo criptografado, o cliente fecha o socket e encerra a execução.

# Forma de uso:

1- Compile o fractal:
	gcc fractal.c -o fractal -lm


2- Compile e rode usando:

``` gcc server.c des.c -O3 -o server.bin -lm -lcrypto && ./server.bin ```

``` gcc client.c des.c -O3 -o client.bin -lm -lcrypto && ./client.bin ../fractaljulia.bmp ```


# Criptografia Assimétrica
	
A criptografia assimétrica é um método criptográfico que utiliza um par de chaves relacionadas matematicamente: uma chave pública e uma chave privada. A chave pública é usada para criptografar dados, enquanto a chave privada é usada para descriptografá-los. O importante é que a chave pública pode ser compartilhada amplamente, permitindo que qualquer pessoa criptografe informações para o proprietário da chave privada, mas apenas o proprietário da chave privada é capaz de descriptografar os dados. Isso torna a criptografia assimétrica ideal para garantir a segurança de comunicações na internet, como a troca segura de chaves em protocolos de segurança, autenticação e assinatura digital.

O algoritmo RSA é um dos algoritmos mais populares de criptografia assimétrica. Foi proposto em 1977 por Ron Rivest, Adi Shamir e Leonard Adleman, daí o nome RSA. O algoritmo é baseado em operações matemáticas com números primos grandes. O processo envolve a geração de dois números primos, a criação das chaves pública e privada a partir desses primos e a aplicação de fórmulas matemáticas para criptografar e descriptografar os dados


Vamos explicar as etapas do algoritmo RSA de forma mais detalhada:

1. Seleção de números primos: O algoritmo começa selecionando dois números primos grandes, geralmente com cerca de 1024 bits cada, chamados de p e q. A escolha de números primos é fundamental para a segurança do algoritmo, pois a fatoração de números compostos em seus fatores primos é uma tarefa computacionalmente difícil.


2. Cálculo de n e z: Com os números primos p e q selecionados, calcula-se o produto n = p * q, que será utilizado na construção das chaves. Também é calculado o valor z = (p - 1) * (q - 1), que será utilizado na etapa seguinte.


3. Escolha de e: Em seguida, é escolhido um número e, menor que n, que não tenha fatores em comum com z. Números que não compartilham fatores primos com z são chamados de "primos entre si". O valor de e é utilizado para formar a chave pública.


4. Cálculo de d: Após a escolha de e, é calculado o valor de d de forma que a multiplicação de e por d menos 1 seja divisível por z, ou seja, e * d mod z = 1. A existência de d é garantida pelo Teorema de Euler. O valor de d é utilizado para formar a chave privada.


5. Chaves pública e privada: Finalmente, as chaves são formadas. A chave pública consiste no par (n, e), e é utilizada para criptografar dados. A chave privada é formada pelo par (n, d) e é utilizada para descriptografar os dados criptografados com a chave pública. A distribuição segura das chaves é essencial para garantir a segurança do sistema, já que a chave privada não deve ser compartilhada e apenas o detentor da chave privada pode realizar a descriptografia. Com esse conjunto de chaves, o algoritmo RSA oferece uma criptografia segura e amplamente utilizada em comunicações modernas.

	Vamos explicar as etapas do processo de criptografia e descriptografia usando as chaves geradas pelo algoritmo RSA:

Cifragem (criptografia): Para cifrar uma mensagem m usando a chave pública (n, e), o remetente computa o criptograma c utilizando a fórmula c = (m^e) mod n, ou seja, o resto da divisão da mensagem m elevada à potência de e por n. Neste processo, a mensagem original é convertida em uma sequência de números que representam o criptograma cifrado. Esse criptograma c pode ser transmitido de forma segura, pois somente o detentor da chave privada correspondente será capaz de descriptografar a mensagem.
Decifragem (descriptografia): Para decifrar o criptograma c e obter a mensagem original m, o destinatário utiliza a chave privada (n, d). A descriptografia é realizada computando m = (c^d) mod n, ou seja, o resto da divisão do criptograma c elevado à potência de d por n. Essa operação recupera a mensagem original a partir do criptograma e permite ao destinatário ler a informação original enviada pelo remetente. É importante notar que a descriptografia só é possível com o uso da chave privada correspondente à chave pública usada para cifrar a mensagem.

# Forma de uso:

1- Compile o fractal:
	gcc fractal.c -o fractal -lm

# Conclusão

Em conclusão, este relatório apresentou uma abordagem abrangente sobre dois importantes paradigmas de criptografia: a simétrica e a assimétrica, destacando os algoritmos RSA e DES como exemplos representativos de cada tipo. A criptografia simétrica, com a utilização de uma única chave para cifragem e descriptografia, destaca-se por sua eficiência e velocidade, mas enfrenta o desafio de compartilhar a chave de forma segura entre as partes envolvidas. Por outro lado, a criptografia assimétrica, com seu par de chaves pública e privada, oferece uma solução elegante para a troca segura de informações, mas pode ser mais lenta em operações criptográficas intensivas. A compreensão desses métodos e suas respectivas aplicações é essencial para a construção de sistemas de segurança robustos.

Além disso, este relatório descreveu a implementação de um sistema de envio seguro de fractais através de uma conexão TCP/IP. A combinação das técnicas de criptografia RSA e DES assegurou a confidencialidade dos dados durante a transmissão, garantindo que apenas o destinatário autorizado pudesse decifrar o conteúdo original. Esse sistema demonstrou a importância de empregar medidas de segurança adequadas para proteger informações sensíveis ao longo de uma rede, especialmente em um contexto onde a segurança dos dados é uma prioridade. A utilização eficiente das técnicas de criptografia, aliada a um protocolo de rede seguro como o TCP/IP, reforça a capacidade de proteger a integridade e a privacidade das informações transmitidas, garantindo a confiabilidade e segurança das comunicações digitais.






