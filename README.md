# kafka-para-iniciantes
Exemplo simples - Produzindo e consumindo mensagens em Java

# Kafka CLI - Comandos do Kafka

# CRIAÇÃO DE TÓPICOS

# Lista os tópicos do kafka:
	kafka-topics --bootstrap-server localhost:9092 --list

# Cria um tópico no kafka com apenas uma partição:
	kafka-topics --bootstrap-server localhost:9092 --create --topic teste

# Cria um tópico no kafka com mais de uma partição, neste exemplo estou criando com 12 partições:
	kafka-topics --bootstrap-server localhost:9092 --create --topic teste --partitions 12
	

# ENVIANDO/PRODUZINDO MENSAGENS

Não é a melhor forma mas via linha de comando podemos enviar mensagem para o kafk, devemos enviar a lista de brokers "ip+porta", e o nome do topic do broker que irá receber a mensagem:
	kafka-console-producer .bat --broker-list localhost:9092 --topic teste
	
	em seguida digite as mensagens que deseja enviar:
		mensagem 1
		mensagem 2
		mensagem 3
		mensagem 4

# RECEBENDO/CONSUMINDO MENSAGENS

No exemplo anterior enviamos uma mensagem para o topic teste do brocker localhost:9092, para recebe-las em um novo terminal vamos digitar o seguinte código informando o endereço do broker de origem e o nome do topic:
	kafka-console-consumer --bootstrap-server localhost:9092 --topic  teste
	
	*OBS: Como enviamos primeiro a mensagem para depois "escutar/consumir" o kafka não vai entender que queremos todas as mensagens enviadas anteriormente (1..4),  apenas o que foi enviado após o comando "kafka-console-consumer"... 
	
Para receber todas as mensagens enviadas pelo produtor, mesmo  as que foram enviadas antes de o consumidor está ouvindo, é necessário especificar que quer ouvir desde o início através do seguinte comando:
	kafka-console-consumer --bootstrap-server localhost:9092 --topic  teste --from-beginning
	
	*Obs: para receber a mensagem de um único produtor em vários consumers, basta repetir o comando kafka-console-consumer... em cada um dos serviços que forem consumir.


# GRUPO DE CONSUMIDORES (ENVIANDO MENSAGEM ALEATÓRIA PARA UM CONSUMIDOR DO GRUPO)

Podemos criar grupos de consumidores para onde um deles poderá consumir  mensagem enviada pelo produtor para o grupo, para isso devemos certificar que o tópico utilizado possui as partições necessárias equivalentes a quantidade de consumidores.

O Tópico que nós criamos não foi especificado a quantidade de partições, logo ele irá assumir a quantidade default que é "0", então se tivermos um tópico com zero partições e tentar-mos compartilhar em grupo de consumidores, não teremos êxito pois apenas um irá receber.

Para verificar a descrição do topico e verificar a quantidade de partições vinculada a ele, podemos digitar o seguinte comando:
	kafka-topics --bootstrap-server localhost:9092 --topic teste --describe

Para alterar a quantidade de partições de um tópico, não podemos ter nenhum produtor nem consumidor enviando mensagens para o mesmo, então certifique-se de que o tópico não está recebendo mensagem e seguiremos com a modificação das partições:
	kafka-topics --bootstrap-server localhost:9092 --alter --topic teste --partitions 10

	Obs: O topic teste será alterado e passará a possuir 10 partições.
		Verifique novamente a descrição do tópico "teste": kafka-topics --bootstrap-server localhost:9092 --topic teste --describe

Para enviar mensagens para n consumidores de forma aleatória, faremos o envio sem chaves o que faz com que o kafka escolha de forma aleatória quem será o consumidor, digite o seguinte comando:
	kafka-console-consumer --bootstrap-server localhost:9092 --topic teste --group grupo1
	
	*Obs: Cada mensagem enviada será recebida por apenas um consumidor do grupo de forma aleatória.
	
# GRUPO DE CONSUMIDORES (ENVIANDO MENSAGEM ORDENADA PARA UM CONSUMIDOR DO GRUPO)

Caso o sistema precise que a mensagem seja enviada na ordem para cada consumidor do grupo e não de forma aleatória, podemos enviar uma mensagem com "chave/valor" para o grupo, especificando a ordem de cada consumidor.

Digite o seguinte comando para o producer:
	kafka-console-producer --broker-list 127.0.0.1:9092 --topic teste --property "parse.key=true" --property "key.separator=,"

Digite o seguinte comando para os consumers:
	kafka-console-consumer --bootstrap-server 127.0.0.1:9092 --topic teste --property "print.key=true" --property "key.separator=," --group grupo1
	
Dessa forma, poderemos especificar para qual consumer iremos enviar a mensagem. 
	ex:
	1, mensagem 1
	1, mensagem 2
	1, mensagem 3
	2, teste 1 
	2, teste 2
	2, teste 3
	3, teste 1
	3, teste 2
	3, teste 3

#EXCLUSÃO DE TÓPICOS

Digite o seguinte comando para excluir o tópico desejado:    
	kafka-topics.sh  --bootstrap-server localhost:9092 --delete --topic  teste
	
	Ao listar novamente os tópicos, notará que o topic "teste" foi removido.
	
	Obs: Um dos motivos no qual o kafka não é recomandável para windows, é que ao excluir um tópico no windows 
	
	


		


