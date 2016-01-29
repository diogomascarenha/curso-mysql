Instale o Virtualbox
Instale o Vagrant

vagrant init ubuntu/trusty64

Altere o arquivo VagrantFile
Descomente as linhas (Remover o #)

config.vm.network "private_network", ip: "192.168.33.10"

config.vm.provider "virtualbox" do |vb|
#   # Display the VirtualBox GUI when booting the machine
#   vb.gui = true
#
#   # Customize the amount of memory on the VM:
vb.memory = "1024"
end

Inicie a maquina virtual
vagrant up

Acesse a maquina virtual
vagrant ssh

Atualizando lista de pacotes
sudo apt-get update

Instalando o MySQL
sudo apt-get install mysql-server-5.6

Coloque a senha de root depois pressione TAB para ir para o OK e clique ENTER

Efetuando configuração de instalação segura (Produção)
sudo mysql_secure_installation

Altere a senha de root se desejar
Remova os usuários anonimos
Remova o acesso como root remotamente
Remova a base "test"
Recarregue os privilégios

Verificando se o MySQL está rodando
netstat -an | grep LISTEN | grep 3306
Deve aparecer uma tela assim

vagrant@vagrant-ubuntu-trusty-64:~$ netstat -an | grep LISTEN | grep 3306
tcp        0      0 127.0.0.1:3306          0.0.0.0:*               LISTEN

O "127.0.0.1:3306" indica que o MySQL está apenas funcionando para acesso local, ou seja, dentro da própria maquina virtual.
Para liberar o acesso é necessário alterar o arquivo "/etc/mysql/my.cnf" e comentar (adicionar um # no início da linha) a linha abaixo
bind-address            = 127.0.0.1

Depois de alterar o arquivo, reiniciar o MySQL
sudo /etc/init.d/mysql restart

Agora o "127.0.0.1" não irá aparecer quando executar o comando abaixo, indicando que o acesso pode ser feito a partir de outras máquinas
vagrant@vagrant-ubuntu-trusty-64:~$ netstat -an | grep LISTEN | grep 3306
tcp6       0      0 :::3306                 :::*                    LISTEN

Conectando no MySQL
mysql -u root -p
Digite a senha e pressione ENTER

Mostrando as bases de dados existentes
show databases;

Mostrando as bases de dados e suas codificações
SELECT SCHEMA_NAME AS 'database', default_character_set_name AS 'charset', DEFAULT_COLLATION_NAME AS  'collation' FROM information_schema.SCHEMATA;

Criando uma base de dados com codificação UTF-8
CREATE DATABASE banco_utf8 CHARACTER SET utf8 COLLATE utf8_general_ci;

Criando uma base de dados com codificação ISO-8859-1 (LATIN1)
CREATE DATABASE banco_latin1 CHARACTER SET latin1 COLLATE latin1_swedish_ci;

Apagando base de dados
DROP DATABASE banco_latin1;

Acessando um banco de dados
use banco_utf8;

Falando um pouco dos principais tipos de dados
https://dev.mysql.com/doc/refman/5.6/en/numeric-type-overview.html
https://dev.mysql.com/doc/refman/5.6/en/integer-types.html

UNSIGNED = Apenas dados maior ou igual a zero
ZEROFILL = Preenchimento de Zeros a Esquerda
M = Utilizado em conjunto com o ZEROFILL para determinar a quantidade preenchida de zeros  a esquerda

TINYINT(M) = Inteiro -128 a 127
		  UNSIGNED 0 a 255

BOOL ou BOOLEAN = TINYINT(1) = Utilizado para valores TRUE/FALSE onde 0=FALSE e 1=TRUE, qualquer coisa diferente de 0 é TRUE, mas tem suas particularidades, recomento usar sempre 0 e 1.

INT(M) = INTEGER(M) = Inteiro -2147483648 a 2147483647
					  UNSIGNED 0 to 4294967295
					  
BIGINT(M) = Inteiro Longo -9223372036854775808 to 9223372036854775807
			UNSIGNED 0 to 18446744073709551615

SERIAL = BIGINT UNSIGNED NOT NULL AUTO_INCREMENT UNIQUE

DECIMAL (M [,D]) = Onde "M" é o numero de digitos (Máximo 65) o padrão é 10 e "D" é o númeor de casas decimais (Máximo 30) e o padrão é 0.
NUMERIC (M [,D]) = DECIMAL (M [,D])
FIXED (M [,D])   = DECIMAL (M [,D])

https://dev.mysql.com/doc/refman/5.6/en/date-and-time-type-overview.html
fsp = FRAÇÃO de segundos, pode ter uma precisão de 0 a 6 digitos. A precisão padrão varia de versão pra versão do MySQL, sempre bom especificar, normalmente "0".

DATE = Data que suporta 'YYYY-MM-DD' entre '1000-01-01' e '9999-12-31'.
DATETIME [(fsp)] = Data e Hora que suporta 'YYYY-MM-DD HH:MM:SS[.fsp]' entre '1000-01-01 00:00:00.000000' e '9999-12-31 23:59:59.999999'. 

TIMESTAMP [(fsp)] = Data e hora que armazena os dados no formato de numeor EPOCH(Segundos desde '1970-01-01 00:00:00' UTC) que é igual a "0". Os datas e horas suportados estão entre '1970-01-01 00:00:01.000000' UTC e '2038-01-19 03:14:07.999999' UTC.

TIME [(fsp)] = Hora que suporta valores entre '-838:59:59.000000' e '838:59:59.000000'. e são mostrados 'HH:MM:SS[.fraction]'.

https://dev.mysql.com/doc/refman/5.6/en/string-type-overview.html

CHAR[(M)] = Caractere onde M pode ser 0 a 255, o valor padrão de M é 1. Uma particularidade de um CHAR é que se a opção "PAD_CHAR_TO_FULL_LENGTH" está habilitada, ele preenche automaticamente o resto do tamanho com espaços. Exemplo CHAR(10) incluindo a paravra 'pato' irá ser armazenado '       pato' ou seja, foi adicionado o caractere espaço.

VARCHAR[(M)] = Caractere onde M pode ser 0 a 65535, caso a codificação seja UTF-8 que pode utilizar 2 bytes por caractere, o máximo de caracteres será 21844.

TEXT, MEDIUMTEXT, LONGTEXT = Caracteres, com textos longos
BLOB, MEDIUMBLOB, LONGBLOB = Caracteres binarios longos
https://dev.mysql.com/doc/refman/5.6/en/blob.html

ENUM ('value1', 'value2') = Utilizado quando quer aceitar apenas um conjunto limitado de valores. Até 65535 elementos.

