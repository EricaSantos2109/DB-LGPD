# DB-LGPD

## Membros: :memo:
- Arthur Barbero - [Github](https://github.com/arthurbarbero) / [LinkedIn](https://www.linkedin.com/in/arthur-barbero/);
- Érica dos Santos Moreira da Rosa - [Github](https://github.com/EricaSantos2109) / [LinkedIn](https://www.linkedin.com/in/%C3%A9rica-santos-2ab73516b/);
- Felippe Alves - [Github](https://github.com/FelippeAlves) / [LinkedIn](https://www.linkedin.com/in/felippe-alves-de-paula/);
- Gabriel Augusto Landim - [Github](https://github.com/Glandim) / [LinkedIn](https://www.linkedin.com/in/gabriel-landim-2b5bb8181/);
- José Vinicius Ferreira Santana - [Github](https://github.com/JViniciusF) / [LinkedIn](https://www.linkedin.com/in/jose-vinicius-ferreira-santana-903239181/);
- Nayara Lorrane Santos Silveira - [Github](https://github.com/nayaralorrane) / [LinkedIn](https://www.linkedin.com/in/nayara-lorrane-765400157/);
- Stephanie Costa Vale Moura - [Github](https://github.com/Stephanie345) / [LinkedIn](https://www.linkedin.com/in/steph%C3%A1nie-c-349b7b103/);
- Thyago Odorico Garcia - [Github](https://github.com/togarci) / [LinkedIn](https://www.linkedin.com/in/thyago-garcia-10ab8a11a/);

Objetivos: :dart:
----------------

Projeto designado à matéria de Segurança da Informação, lecionado pelo professor Eduardo Sakaue, trouxe consigo o desafio de implementar uma solução para tópicos abordados na Lei Geral de Proteção de Dados (LGPD), sendo assim estipulamos algumas dificuldades e chegamos a um tópico no qual apresentaremos seu conceito.

A Lei Geral de proteção de Dados, de caráter Multidisciplinar e extraterritorial, é a legislação que regula a coleta, armazenamento, tratamento e compartilhamento de dados pessoais, impondo um padrão mais elevado de proteção e penalidades significativas para o não cumprimento da norma. Ela é um marco legal de grande impacto, englobando instituições públicas e privadas.

Essa lei surge com a finalidade de proteger direitos fundamentais, como:

- Privacidade;
- Autodeterminação informativa;
- Liberdade de expressão, de informação, de comunicação e de opinião;
- Inviolabilidade da intimidade da honra e da imagem;
- Desenvolvimento econômico, tecnológico e a inovação;
- A livre iniciativa, a livre concorrência e a defesa do consumidor;
- Direitos humanos: o livre desenvolvimento da personalidade, dignidade e o exercício da cidadania pelas pessoas naturais.

Para mais informações sobre a lei e tópicos deste trabalho, acessar o arquivo [Info.md :book:](/Info.md) 

Resolução
----------------
Dado o contexto acima, desenvolveremos uma aplicação simples com um CRUD (Create, Read, Update, Delete) com acesso a Bancos de dados voltada a resolver problemas de anonimização de uma aplicação de vendas online fictícia.

Para isto contaremos com a seguinte estrutura:

![Esboço](/images/esboço.png)

Segundo a LGPD, a anonimização é atingida quando um dado relativo ao titular não possa ser identificado direta ou indiretamente, considerando a utilização de meios técnicos razoáveis e disponíveis na ocasião de seu tratamento, sendo assim, sabemos que a total anonimização não traz nenhum benefício para a empresa em si, mas enquanto este dado estiver criptografado de maneira na qual os meios técnicos razoáveis forem aplicáveis, este dado é considerado seguro.

Para nossos exemplos abaixo, existem partes importantes do processo na qual o dado é tratado e passam a ser dados pessoais novamente, tanto na captação do dado como em sua validação, trazendo os maiores perigos deste problema em suas etapas de validação e armazenamento. 

Quando o dado é anonimizado, mas é dada a possibilidade de voltar a sua forma original, visto as funcionalidades da aplicação, isto é considerado como pseudonimização, também retratado como válido desde que sejam cumpridos os demais artigos da Lei. 

Técnicas utilizadas
------------
Dada a resolução do problema, decidimos implementar um algoritmo  de criptografia simétrica simples que resolve a questão do armazenamento seguro de informações pessoais de forma anonimizada. Ao se cadastrar no site, para cada usuário é criada uma chave simétrica simples e é armazenada dentro de um banco de dados de chaves criptografadas, separado do banco de dados pessoais.

Para isto utilizaremos a criptografia do padrão AES (Advanced Encryption System), utilizando uma senha com um tamanho de 256 bits e o método de criptografia em bloco no modelo CBC (Cypher Block Chaining). Também utilizamos o serviço [Vault](https://www.vaultproject.io/) para o armazenamento destas chaves para cada usuário.
 
 - AES
 
 
  O algoritmo foi criado por Vincent Rijmen e Joan Daemen, primeiramente com o nome de Rijndael, vencendo o concurso do NIST (National Institute of Standards and Technology) em 1997, que passaria a se chamar AES, o mesmo atendeu a todas as exigências do concurso sendo o melhor em segurança, flexibilidade, bom desempenho em softwares e hardwares.
  
  Dentre as criptografias mais conhecidas, optamos por se utilizar da AES pelo fato de estar entre as maiores criptografias de blocos, podendo-se utilizar de chaves de até 256 bits. Entre os concorrentes mais conhecidos temos o DES que é muito velho e ja está praticamente mapeado, o 3DES que é uma implementação de DES em cascata, criando três instâncias do DES com chaves distintas, mas se prova um algorítmo muito lento em comparação as outras. Blowfish é um algorítmo que concorreu no mesmo concurso em que o AES foi vencedor, se destaca pela sua rapidez porém o tamanho dos blocos são de apenas 64 bits.
  
  
 - Método CBC (Cypher Block Chaining)
  
  Dentre os métodos de criptografia AES, utilzamos o método CBC por ser o mais confiável e aleatório possível. Métodos como o BCE (Eletronic Codebook), utilizam apenas a chave para a realização da criptografia, tornando a criptografia de um mesmo dado, se realizado duas vezes, idênticos entre si. O método CFB (Cipher feedback) utiliza-se de um bloco de inicialização, trazendo uma aleatoriedade ao processo, porém, expõe o conteudo a ser cifrado.
 
  Antes de aprofundarmos no método, primeiramente devemos entender o conceito de bloco IV.
  
  
   - IV - Vetor de Inicialização:
     O vetor de inicialização pode ser entendido como um “bloco falso”, utilizado no início do processo de cifragem, gerando aleatoriedade no sistema.


   - Método:
     O método CBC consiste na criptografia de cada bloco de plaintext junto com o bloco cifrado anterior, antes dele ser criptografado. Com isso os blocos futuros são dependentes dos blocos anteriores. Com IV aleatório em cada cifragem, é possível manter a aleatoriedade do ciphertext mesmo com um plaintext igual.

     ![Método_CBC](https://github.com/arthurbarbero/DB-LGPD/raw/master/images/aes-Algoritmo-CBC.png)
      
A utilização do método CBC foi escolhida entre as demais por ser a melhor em questão de aleatoriedade. Mesmo que passemos o bloco IV no início de sua criptografia, sendo este gerado de maneira aleatória, conseguimos cifras diferentes para um mesmo registro, pelo uso de criptografias passadas conforme na imagem.

- Vault
  
  Aplicação que oferece uma interface via linha de comandos e via requisição http para armazenamento de ``chave=valor`` de forma criptografada. Ela é responsável por vincular ao ID de registro do usuário cadastrado a chave gerada aleatóriamente, utilizada no processo de encriptação do usuário, fazendo a chave=valor ``id=crypt_key``, para que posteriomente quando o back-end necessite visualizar os dados, possa pegar a chave pelo id do usuário.



Backlog
------------
Por tanto, utilizaremos as formas de pseudonimização onde somente manipulamos os dados no Back-end, trazendo formas seguras de conexão ao Banco de dados pessoais e mantendo a criptografia dos dados em todas as etapas de transferência entre aplicações. O Backlog abaixo demonstra os processos que realizaremos para a criação deste projeto:

## Entregas:

Alta prioridade = :red_circle:
Média prioridade = :yellow_circle:
Baixa prioridade = :large_blue_circle:

### Sprint 1 - 07/09 A 20/09:

Com o objetivo de iniciar e nivelar os conhecimentos realizamos algumas tarefas básicas, desde a criação do repositório quanto da consolidação dos objetivos do projeto.

- Start no Repositório. :heavy_check_mark:
- Home Page Mock. :heavy_check_mark:
- Rota de cadastro simples. :heavy_check_mark:

Tag - [Entrega 1](https://github.com/arthurbarbero/DB-LGPD/tree/Entrega-1)

### Sprint 2 - 21/09 A 04/10:

Após a criação de um "boilerplate" para ambas as frentes, front e back-end, iremos focar esta entrega na produção de um fluxo de cadastro completo e na conexão segura com o Banco de Dados, com criptografia nas informações que são enviadas da captação até o Banco de Dados. Utilizaremos criptografia simétrica no padrão AES-256 para o envio das informações do front-end para o back-end, assim como a decriptação, validação dos dados e nova criptografia simétrica no mesmo padrão, com outra chave para a persistência no banco. Sua conexão será via autenticação SSH ao Banco de dados Mongo, assim somente a aplicação com seu conjunto de chaves fará os acessos e modificações necessárias ao Banco de dados.

- Sign-up Page; (Front-end) :large_blue_circle: :heavy_check_mark:
- Criptografia no envio dos dados; (Front-end) :red_circle: :heavy_check_mark:
- Criação de classe de criptografia; (Back-end) :red_circle: :heavy_check_mark:
- Autenticação via SSH com o Banco de dados pessoais; (BD) :red_circle: :heavy_check_mark:
- Persistência dos dados; (BD) :yellow_circle: :heavy_check_mark:

[Entrega 2](https://github.com/arthurbarbero/DB-LGPD/blob/master/Entrega2.md)


### Sprint 3 - 05/10 A 18/10:

Com a criptografia simétrica da aplicação realizada e persistindo no Banco de Dados de forma segura, esta entrega irá focar na finalização das demais páginas e rotas, 

- Validação dos dados; (Back-end) :yellow_circle: :heavy_check_mark:
- Criação das rotas de Sign-in; (Back-end) :yellow_circle: :heavy_check_mark:
- Criação da Sign-in Page; (Front-end) :yellow_circle: :heavy_check_mark:
- Criação de rota para Profile Page (Back-end) :large_blue_circle: :heavy_check_mark:
- Criação da Profile Page (Front-end) :large_blue_circle: :heavy_check_mark:

[Entrega 3](https://github.com/arthurbarbero/DB-LGPD/blob/master/Entrega3.md)

### Sprint 4 - 19/10 A 01/11:

Após as entregas acima, utilizaremos desta sprint para finalizar o restante das tarefas e consertar quaisquer passos anteriores que estejam fora do padrão requisitado pelo cliente.

- Criação de rota para Editar o usuário (Back-end) :large_blue_circle: :heavy_check_mark:
- Criação de rota para Deletar o usuário (Back-end) :large_blue_circle: :heavy_check_mark:
- Ajustes na Profile Page para suportar a edição e exclusão simples (Front-end) :large_blue_circle: :heavy_check_mark:

[Entrega 4](https://github.com/arthurbarbero/DB-LGPD/blob/master/Entrega4.md)

### Sprint 5 - 02/11 A 15/11:

- Implementação de chave de criptografia por usuário (Back-end) :red_circle: :heavy_check_mark:
  
  - Para tornar a criptografia entre o back-end e o banco de dados pessoais mais forte e segura, foi requisitado que fosse implementado algum tipo de chaves simétricas únicas por usuário. Utilizamos do serviço **Vault** para o armazenamento das mesmas.
  
- Finalização da página privacy-page (Front-end) :large_blue_circle: :heavy_check_mark:

[Entrega 5](https://github.com/arthurbarbero/DB-LGPD/blob/master/Entrega5.md)

### Sprint 6 - 16/11 A 29/11:

Reservada para possíveis consertos e criação da apresentação final. :large_blue_circle: :heavy_check_mark:
- Criação do vídeo para a apresentação final. :heavy_check_mark:
- Exemplificação sobre a criptografia utilizada. :heavy_check_mark:

[Entrega 6](https://github.com/arthurbarbero/DB-LGPD/blob/master/Entrega6.md)

Tecnologias empregadas
-------------

- Python + Flask Web Framework

  Utilizamos a framework Flask para construir uma API Restful, um back-end robusto para servir como ponte entre o usuário e o banco de dados de forma criptografada.
  
  Dentro do projeto criamos uma classe em python para manipular todas as interações com métodos de criptografia, [a classe pode ser analisada clicando aqui](https://github.com/arthurbarbero/DB-LGPD/blob/master/back-end/src/model/utils/crypto.py).
  
  A classe crypto.py utiliza a biblioteca [pycrypto](https://github.com/pycrypto/pycrypto), fazendo uso de suas classes 'AES' e 'Random', recebemos uma 'String' com codificação base64 e criptografamos utilizando um bloco IV gerado aleatoriamente, uma chave de tamanho 256 bits que também será gerada aleatóriamente e o método ``AES.MODE_CBC`` que realiza a combinação de cifras anteriores, conforme mencionada no tópico de Técnicas utilizadas.
  
  Esta chave, no momento de sua criação, é salva na aplicação local **Vault**, sua [classe pode ser analisada clicando aqui](https://github.com/arthurbarbero/DB-LGPD/blob/crypt_by_user/back-end/src/model/utils/vault.py), o método recebe as informações do ID do registro do usuário que esta sendo criado e sua chave recém gerada e armazena em seu banco local criptografado após a confirmação de suas três chaves para destravar o acesso a aplicação, este [método pode ser analisado clicando aqui](https://github.com/arthurbarbero/DB-LGPD/blob/crypt_by_user/back-end/src/model/utils/vault.py#L59-L67).
  
  A forma de decriptografia age da mesma maneira, recebe uma 'String' com codificação base64, retiramos o bloco IV, e com a mesma chave, recuperada do banco de chaves **Vault** pelo ID de registro do usuário, e método ``AES.MODE_CBC`` deciframos a mensagem.

- MongoDB

  O banco de dados foi criado no padrão não-relacional, sendo mais rápido e performático para realizar pesquisas, e obtendo o mesmo nível de permissões e autenticações de um banco normal.
  
  Criamos uma máquina Linux para servir de exemplo de um servidor MongoDB onde foram criados usuários com permissão de leitura e escrita para que apenas estes possam realizar acesso ao banco de dados pessoais, estas credenciais não são versionadas e estão disponíveis no arquivo ``.env`` no Back-end, fazendo com que somente a aplicação Flask tenha acesso ao MongoDB.
  
  Não somente isto, como também realizamos as configurações necessárias no firewall para que somente requisições derivadas do IP da máquina onde a aplicação se encontra possam ser aceitas, trazendo mais uma proteção ao Banco de Dados do cliente.
  
  Um exemplo do arquivo de configuração e da criação de usuário se encontra no [ReadMe.md](https://github.com/arthurbarbero/DB-LGPD/blob/master/db/README.md) na pasta 'db'

- HTML+CSS+JavaScript

  Utilizamos a tríade do front-end para construir de forma simples uma interface para simular um cadastro do usuário.
  
  Dentro do projeto de front-end criamos um arquivo JavaScript para manipular as interações com métodos de criptografia, [o arquivo pode ser analisado clicando aqui](https://github.com/arthurbarbero/DB-LGPD/blob/master/front-end/javaScript/utils/cript.js).
  
  O arquivo cript.js utiliza a biblioteca [CryptoJS](https://github.com/brix/crypto-js), fazendo uso de seus métodos ``AES.encrypt`` e ``AES.decrypt``, recebemos uma 'String' e criptografamos utilizando um bloco IV gerado aleatoriamente, uma chave de tamanho 256 bits que está armazenada no mesmo arquivo e o método ``CryptoJS.mode.CBC`` que realiza a combinação de cifras anteriores, conforme mencionada no tópico de Técnicas utilizadas.
  
  A forma de decriptografia age da mesma maneira, recebe uma 'String', retiramos o bloco IV, e com a mesma chave e método ``CryptoJS.mode.CBC`` deciframos a mensagem.

Features
-------------

## [Front-end: :computer:](https://github.com/arthurbarbero/DB-LGPD/tree/master/front-end/)

  Criaremos uma interface básica utilizando as bases do desenvolvimento front-end, HTML, CSS e JavaScript, contendo as  seguintes funcionalidades:
  
###  - Cadastro;
  
  Este cadastro irá simular a interação de um cliente que queria ter acesso ao site para realizar suas compras, para que então, de forma segura e encriptada, estes dados serão enviados para o back-end e assim suas demais funcionalidades de login e perfil possam ser acessadas
  
###  - Login;
  
  Com o cadastro finalizado, o usuário poderá realizar o login de forma segura onde seus dados armazenados em browser estão sendo utilizados apenas para os fins respeitados pelas políticas de privacidade estipuladas pela aplicação e encriptadas, além de poder acessar outras áreas do site.
  
###  - Perfil;
  
  Após o login, a página de perfil pessoal demonstra quais dados estão sendo utilizados pela aplicação de forma transparente.
  
###  - Página de privacidade.
  
  Em toda a aplicação, será disponibilizada parte reservada para as políticas de privacidade contando de forma transparente a finalidade dos dados que persistimos em nossos bancos de dados.
  
  
## [Back-end: :page_with_curl:](https://github.com/arthurbarbero/DB-LGPD/tree/master/back-end)
  
  Com a captação de dados vinda do front-end, a aplicação Rest em Python utilizando a framework Flask nos dará as funcionalidades necessárias para realizar as validações e comunicações com os Bancos de Dados de forma segura e concisa com base nas seguintes rotas:
  
###  - Rota de cadastro;

  Para completarmos o ciclo do cadastro, receberemos os dados da etapa do cadastro do front-end e realizaremos as devidas validações, protegendo contra ataques e informações maliciosas, assim realizaremos a criptografia dos dados e inserimos no respectivo Banco de Dados;
    
###  - Rota de Login;

  Quando do acesso do cliente à aplicação, o back-end realizará a validação da existência do usuário ao Banco de dados, com todas as seguranças necessárias;
  
###  - Rota de Exclusão;

  A pedido do usuário, o back-end realizará a exclusão permanente dos dados em posse referente ao cliente, lembrando que neste quesito não abordaremos todos os riscos que uma empresa necessitaria para realizar as devidas exclusões atendendo todos os tópicos da LGPD que abordam a deleção de dados pessoais, somente uma forma de exclusão simples;
  
  
## [Banco de dados: :books:](https://github.com/arthurbarbero/DB-LGPD/tree/master/db)
  
  Moldado às necessidades acima, realizaremos a criação de um banco de dados exclusivo para o tratamento de dados pessoais. Além de serem armazenados somente dados criptografados, o acesso ao banco também será protegido via chaves SSH, permitindo apenas que a aplicação do Back-end realize leituras e escreva neste banco de dados.
  
###  - Criação do banco:
  
  Utilizaremos de um banco não relacional mongo com as devidas configurações básicas de tunelamento via SSH.
  
  
####  Para mais detalhes de como iniciar cada serviço separadamente, clique nos tópicos acima ou navegue pelas pastas deste repositório.
