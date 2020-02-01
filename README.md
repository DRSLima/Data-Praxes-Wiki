# Data Praxes Wiki
------------------
## Base de conhecimento

  Aqui vamos falar sobre vários temas relacionados com Inteligência de Dados e suas tecnologias correlatas.
Nossa idéia é contribuir para o compartilhamento de conhecimento. 
Contaremos nossas experiências, casos de sucesso, desafios enfrentados, dificuldades e soluções encontradas, tudo para que de alguma forma nossa wiki possa ajudar quem precisa. Não é mesmo?!!!

## Como redefinir sua senha de ROOT do MySQL ou MariaDB

### Introdução

Esquecer as senhas acontece com os melhores de nós. Se você esquecer ou perder a senha root do seu banco de dados MySQL ou MariaDB, ainda poderá obter acesso e redefinir a senha se tiver acesso ao servidor e a uma sudoconta de usuário habilitada.

Este tutorial mostrará como redefinir a senha root para versões mais antigas e mais recentes do MySQL e MariaDB.
  
#### 1º Identificando a versão do banco de dados:
  
A maioria das distribuições modernas de Linux são fornecidas com o MySQL ou o MariaDB, um popular substituto drop-in que é totalmente compatível com o MySQL. Dependendo do banco de dados usado e de sua versão, você precisará usar comandos diferentes para recuperar a senha root.

Você pode verificar sua versão com o seguinte comando:
  
> $ mysql --version

Você verá uma saída como esta com o MySQL:

> MySQL output
> mysql  Ver 14.14 Distrib 5.7.16, for Linux (x86_64) using  EditLine wrapper

Ou para o MariaDB:

> MariaDB output
> mysql  Ver 15.1 Distrib 5.5.52-MariaDB, for Linux (x86_64) using readline 5.1

Anote qual banco de dados e qual versão você está executando, como você os usará mais tarde. Em seguida, você precisa interromper o banco de dados para acessá-lo manualmente.

#### 2º Parando o servidor de banco de dados

Para alterar a senha root, você deve desligar o servidor de banco de dados antecipadamente.

Você pode fazer isso no MySQL com:

> $ sudo systemctl stop mysql

E para o MariaDB com:

> $ sudo systemctl stop mariadb

Depois que o servidor de banco de dados for parado, você o acessará manualmente para redefinir a senha root.

#### 3º Reiniciando o servidor de banco de dados sem verificação de permissão

Se você executar o MySQL e o MariaDB sem carregar informações sobre os privilégios do usuário, ele permitirá que você acesse a linha de comando do banco de dados com privilégios de root, sem fornecer uma senha. Isso permitirá que você obtenha acesso ao banco de dados sem saber.

Para fazer isso, é necessário parar o banco de dados de carregar as tabelas de concessão , que armazenam informações sobre privilégios do usuário. Como esse é um risco à segurança, você também deve pular a rede também para impedir que outros clientes se conectem.

Inicie o banco de dados sem carregar as tabelas de concessão ou ativar a rede:

> $ sudo mysqld_safe --skip-grant-tables --skip-networking &

***Oe comercial no final deste comando fará com que esse processo seja executado em segundo plano para que você possa continuar usando o seu terminal.***

Agora você pode conectar-se ao banco de dados como usuário root, o que não deve solicitar uma senha.

> $ mysql -u root

Você verá imediatamente um prompt de shell do banco de dados.

> Prompt do MySQL
> Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
> 
> mysql>
> Prompt do MariaDB
> Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
>
> MariaDB [(none)]:
Agora que você tem acesso root, pode alterar a senha root.

#### 4º Alterando a senha root

Uma maneira simples de alterar a senha raiz das versões modernas do MySQL é usando o ALTER USERcomando No entanto, este comando não funcionará no momento porque as tabelas de concessão não estão carregadas.

Vamos dizer ao servidor de banco de dados para recarregar as tabelas de concessão emitindo o FLUSH PRIVILEGEScomando

> FLUSH PRIVILEGES;

Agora podemos alterar a senha do root.

Para o MySQL 5.7.6 e mais recente , bem como o MariaDB 10.1.20 e mais recente , use o seguinte comando.

> ALTER USER 'root'@'localhost' IDENTIFIED BY 'new_password';

Para o MySQL 5.7.5 e mais antigo , bem como o MariaDB 10.1.20 e mais antigo, use:

> SET PASSWORD FOR 'root'@'localhost' = PASSWORD('new_password');

Certifique-se de substituir new_passwordpor sua nova senha de escolha.

Nota : Se o ALTER USERcomando não funcionar, geralmente é indicativo de um problema maior. No entanto, você pode tentar UPDATE ... SETredefinir a senha root.

> UPDATE mysql.user SET authentication_string = PASSWORD('new_password') WHERE User = 'root' AND Host = 'localhost';

Lembre-se de recarregar as tabelas de concessão depois disso.

Em qualquer um dos casos, você deverá ver a confirmação de que o comando foi executado com sucesso.

> Output
> Query OK, 0 rows affected (0.00 sec)

A senha foi alterada; portanto, agora você pode parar a instância manual do servidor de banco de dados e reiniciá-la como antes.

#### 5º Reinicie o servidor de banco de dados normalmente

Primeiro, pare a instância do servidor de banco de dados que você iniciou manualmente na Etapa 3. Este comando procura o PID ou ID do processo MySQL ou MariaDB e envia SIGTERMpara solicitar que ele saia sem problemas após executar operações de limpeza. Você pode aprender mais neste tutorial de gerenciamento de processos do Linux .

Para o MySQL, use:

> $ sudo kill `cat /var/run/mysqld/mysqld.pid`

Para MariaDB, use:

> $ sudo kill `/var/run/mariadb/mariadb.pid`

Em seguida, reinicie o serviço usando systemctl.

Para o MySQL, use:

> $ sudo systemctl start mysql
Para MariaDB, use:

> $ sudo systemctl start mariadb

Agora você pode confirmar que a nova senha foi aplicada corretamente executando:

> $ mysql -u root -p

O comando agora deve solicitar a senha recém-atribuída. Digite-o e você deverá obter acesso ao prompt do banco de dados conforme o esperado.

### Conclusão

Agora você tem acesso administrativo ao servidor MySQL ou MariaDB restaurado. Verifique se a nova senha root que você escolhe é forte e segura e mantenha-a em local seguro.
  
###### Fonte: https://www.digitalocean.com/community/tutorials/how-to-reset-your-mysql-or-mariadb-root-password
###### Colaborador: Mateusz Papiernik
###### Replicador do conhecimento: Danilo Lima


