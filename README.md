<img src="https://github.com/Epaminondaslage/OpenPLC/blob/master/img/Logo_CEFET-MG.png" width="20%">
Repositório OpenPLC e Modbus</p>
Prof Epaminondas Lage</p>
<a href="http://lattes.cnpq.br/7787341723868111"> Currículo Lattes LAGE, E. S.</a> 

# Índice 

* [Introdução](#Introdução)
* [Endereçamento Modbus](#EndereçamentoModbus)
* [Funções Modbus no OpenPLC](#Funções-Modbus-no-OpenPLC)
* [Dispositivos Escravos](#Dispositivos-Escravos)
* [Adicionando Placas Compatíveis com Arduino como Dispositivos Escravos](#Adicionando-Placas-Compatíveis-com-Arduino-como-Dispositivos-Escravos)

# Introdução

O Modbus é um protocolo de comunicação simples e eficiente usado para a troca de informações entre dispositivos em um ambiente industrial. Este protocolo é suportado no OpenPLC, tornando-o uma opção a ser considerada para comunicação com dispositivos e equipamentos industriais que usam esse protocolo. É um protocolo aberto. Isto significa que ele é livre para que os fabricantes o implementem em seus equipamentos sem que haja a necessidade de pagar royalties.

Em termos simples, é um método usado para transmitir informação dispositivos eletrônicos. O dispositivo que solicita a informação é chamado de Modbus Master (Mestre) e os dispositivos que fornecem informação são os Modbus Slaves (Escravo).

Protocolo de comunicação da camada de aplicação (modelo OSI) e pode utilizar o RS-232, RS-485 ou Ethernet como meios físicos - equivalentes camada de enlace (ou link) e camada física do modelo. Possui comandos para envio de dados discretos (entradas e saídas digitais) ou numéricos (entradas e saídas analógicas).

Ele se tornou um protocolo de comunicação padrão na indústria e atualmente é o meio mais comum para conectar dispositivos eletrônicos industriais. É utilizado amplamente por vários fabricantes em diferentes segmentos industriais, sendo o Modbus tipicamente usado para transmitir sinais de instrumentação e dispositivos de controle para um sistema controlador ou sistema de coleta de dados.

No OpenPLC, é possível implementar a comunicação Modbus tanto na versão mestre (cliente) quanto na versão escrava (servidor). Isso permite que o OpenPLC atue como um dispositivo mestre para ler e escrever dados de dispositivos Modbus escravos, ou como um dispositivo escravo para responder a solicitações de leitura ou escrita de outros dispositivos Modbus mestres.

# Básico do protocolo de comunicação Modbus

As informações do protocolo são encapsuladas em formato binário em frames TCP para uso do meio físico Ethernet (IEEE 802.3). Seu método de controle de acesso é o CSMA-CD (Carrier Sense Multiple Access/Collision Detection – sistema que evita a colisão na emissão de pacotes de dados), com estações do tipo cliente-servidor ou mestre-escravo.

O mestre envia requisições de dados de leitura ou de escrita para o escravo e recebe as respostas.

Dessa forma, para exemplificar, podemos ter um supervisório como o SCADA, enviando uma requisição de leitura para obter algum dado ou estado, de um sensor ou variável, e também de escrita, emitindo algum comando. 

O mestre é quem orientará os pedidos de informação. Já o escravo, que pode ser, por exemplo, um outro  OpenPLC  ou um Arduino, fica aguardando os comandos para enviar respostas.

Entre as funções básicas de um Modbus estão os comandos de alteração de valores em um registro de um dispositivo; controle ou leitura de portas I/O (Input/Output) e a solicitação de envio de valores. 

Pode-se usar o Modbus com um computador para configurar ou coletar dados de um dispositivo de campo. Você pode ter comunicação serial com o Modbus RTU e ASCII ou usando um cabo Ethernet para utilizar o Modbus TCP/IP.

<table border="0">
<tbody>
<tr>
<td style="width: 50%;"><img src="/img/rede_PLC.png" width="150%" /></td>
</tr>
<tr>
<td style="text-align: center;">Figura 1 - Topologia de Rede de Automação.</td>
</tr>
</tbody>
</table>

Existem várias versões do protocolo Modbus, projetadas para diferentes interfaces de comunicação.Podemos citar três mais comuns:

* Modbus RTU: Usado em comunicação serial, o Modbus RTU emprega uma representação compacta e binária dos dados para a comunicação. Ele inclui um checksum de redundância cíclica (CRC) como mecanismo de verificação de erros para garantir a confiabilidade dos dados. O Modbus RTU é a implementação mais comum do Modbus e requer transmissão contínua sem hesitações entre caracteres. As mensagens do Modbus RTU são enquadradas por períodos ociosos (silêncio).
* Modbus ASCII: Também usado em comunicação serial, o Modbus ASCII utiliza caracteres ASCII para a comunicação do protocolo. Ele emprega um checksum de redundância longitudinal (LRC). As mensagens do Modbus ASCII são enquadradas por dois caracteres: dois pontos (":") no início e uma nova linha (CR/LF) no final.
* Modbus TCP/IP ou Modbus TCP: Essa variante foi projetada para comunicação em redes TCP/IP, normalmente conectando-se à porta 502. Ao contrário do Modbus RTU e Modbus ASCII, não requer cálculo de checksum, pois as camadas inferiores do conjunto de protocolos TCP/IP já fornecem proteção de checksum.
  
Essas diferentes versões e variantes do protocolo Modbus atendem a várias necessidades de comunicação e são amplamente utilizadas em sistemas industriais e de automação. O Modbus TCP/IP tem ganhado popularidade nos últimos anos devido ao amplo uso de Ethernet e do conjunto de protocolos da Internet em aplicações de automação industrial.

## O protocolo Modbs RTU

* Utiliza a comunicação serial
* Disposivitos da rede podem ser daisy-chain na rede
* Pode utilizar repetidor na rede
* Comprimento máximo 1200 metros
* 32 escravos na rede sem utilizar repetidor e 247 com repetidor.

## O protocolo Modbus TCP/IP

* Rede Ethernet
* Pode utilizar estrutura Ethernet existente
* Mais rápido e fácil de fazer diagnósticos
* Redução de custo com estrutura

  
# Formatos de frame no Modbus

A estrutura clara e padronizada dos quadros permite uma comunicação eficiente e confiável entre dispositivos mestres e escravos, tornando o Modbus uma escolha popular para comunicação em sistemas de automação industrial. Cada frame ou pacote de dados é enviado com a seguinte estrutura: 

* Endereço do dispositivo escravo;
* código da função ou comando a ser executado;
* os dados e a
* checagem de erros (para garantir que os dados não foram corrompidos durante a transmissão ou não chegaram ao destinatário).Os formatos de quadro no Modbus podem variar dependendo da variante usada (Modbus RTU, Modbus ASCII ou Modbus TCP/IP), mas geralmente seguem uma estrutura semelhante.

Os formatos básicos dos quadros Modbus em suas diferentes variantes.

## Modbus RTU

* Endereço do Dispositivo Escravo (1 byte): Especifica o endereço do dispositivo escravo ao qual o quadro está sendo enviado.
* Código de Função (1 byte): Identifica o tipo de comando que o dispositivo escravo deve executar (por exemplo, leitura de dados, escrita de dados, etc.).
* Dados (variável): Os dados associados à função específica, como endereços de registros, valores a serem escritos ou valores lidos.
* Checksum (2 bytes): Um checksum de redundância cíclica (CRC) calculado com base nos bytes do quadro, usado para detecção de erros durante a transmissão.

## Modbus ASCII

* Delimitadores de Início e Fim (2 caracteres): Um quadro Modbus ASCII é delimitado por caracteres especiais, geralmente ":" no início e "\r\n" (retorno de carro e nova linha) no final.
* Endereço do Dispositivo Escravo (2 caracteres): Especifica o endereço do dispositivo escravo ao qual o quadro está sendo enviado, representado em caracteres ASCII.
* Código de Função (2 caracteres): Identifica o tipo de comando que o dispositivo escravo deve executar, também representado em caracteres ASCII.
* Dados (variável): Os dados associados à função específica, como endereços de registros, valores a serem escritos ou valores lidos, representados em caracteres ASCII.
* Checksum (2 caracteres): Um checksum de redundância longitudinal (LRC) calculado com base nos caracteres ASCII do quadro, usado para detecção de erros.
  
## Modbus TCP/IP

* Identificação do Dispositivo Escravo (6 bytes): O endereço IP e a porta do dispositivo escravo na rede TCP/IP.
* Código de Função (1 byte): Identifica o tipo de comando que o dispositivo escravo deve executar.
* Dados (variável): Os dados associados à função específica, como endereços de registros, valores a serem escritos ou valores lidos.

Nenhum checksum é utilizado no Modbus TCP/IP, pois a camada de transporte TCP já fornece mecanismos de verificação de integridade dos dados.


# Endereçamento Modbus no OpenPLC

O OpenPLC pode ser configurado como escravo Modbus (servidor). Um escravo Modbus é normalmente controlado por um mestre que realiza medição e controle. O mestre remoto inicia requisições de leitura e escrita para o escravo OpenPLC enviando quadros Modbus pela rede (Modbus/TCP). O micro runtime OpenPLC para placas compatíveis com Arduino também suporta quadros Modbus via serial e USB. O OpenPLC Runtime para hosts Linux e Windows possui um servidor Modbus rodando por padrão na porta TCP 502. Esta configuração pode ser alterada na aba “Settings”.

## Funções Modbus no OpenPLC

O OpenPLC suporta os seguintes códigos de função Modbus:

1. Ler bobina de saída discreta (0x01): Permite ao dispositivo mestre ler o estado de uma única bobina de saída discreta (ligada/desligada) em um dispositivo escravo.
2.	Bobina de saída discreta de gravação (0x05): Permite ao dispositivo mestre alterar o estado de uma única bobina de saída discreta (ligada/desligada) em um dispositivo escravo.
3.	Escrever várias bobinas de saída discreta (0x0F): Permite ao dispositivo mestre alterar o estado de várias bobinas de saída discretas (ligadas/desligadas) em um dispositivo escravo.
4.	Ler contatos de entrada discreta (0x02): Permite ao dispositivo mestre ler o estado de uma ou várias entradas discretas (contatos) em um dispositivo escravo.
5.	Ler registros de entrada analógica (0x04): Permite ao dispositivo mestre ler os valores de um ou mais registros de entrada analógica em um dispositivo escravo.
6.	Ler registros de retenção de saída analógica (0x03): Permite ao dispositivo mestre ler os valores de um ou mais registros de saída analógica em um dispositivo escravo.
7.	Gravar registro de retenção de saída analógica (0x06): Permite ao dispositivo mestre alterar o valor de um único registro de saída analógica em um dispositivo escravo.
8.	Gravar vários registros de saída analógica (0x10): Permite ao dispositivo mestre alterar os valores de vários registros de saída analógica em um dispositivo escravo.


Os endereços Modbus vinculam-se a endereços PLC com base no valor do endereço hierárquico, ou seja, endereços PLC inferiores são mapeados para endereços Modbus inferiores. Os endereços são mapeados sequencialmente sempre que possível. A tabela a seguir mostra o espaço de endereço Modbus para o tempo de execução OpenPLC Linux/Windows:

<table border="0">
<tbody>
<tr>
<td style="width: 50%;"><img src="/img/diagmodbus.png" width="50%" /></td>
</tr>
<tr>
<td style="text-align: center;">Figura 2- Endereçamento Modbus para Openplc em Windows e Linux.</td>
</tr>
</tbody>
</table>

Devido a limitações de memória, plataformas embarcadas como placas Arduino possuem um espaço de endereçamento Modbus muito mais limitado, conforme tabela abaixo:


<table border="0">
<tbody>
<tr>
<td style="width: 50%;"><img src="/img/diagmodbusarduino.png" width="50%" /></td>
</tr>
<tr>
<td style="text-align: center;">Figura 3 - Endereçamento Modbus para Openplc para Arduino.</td>
</tr>
</tbody>
</table>

Plataformas baseadas em ATmega 328P, ATmega 168, ATmega32U4 e ATmega16U4 como o Arduino Uno e similares têm um espaço de endereçamento ainda mais limitado devido à sua disponibilidade de memória RAM ser muito baixa:

<table border="0">
<tbody>
<tr>
<td style="width: 50%;"><img src="/img/diagmodbusarduinouno.png" width="50%" /></td>
</tr>
<tr>
<td style="text-align: center;">Figura 4 - Endereçamento Modbus para Openplc para Arduino Nuno, ATmega 328P, ATmega 168 entre outros.</td>
</tr>
</tbody>
</table>

Bobinas de saída discreta e ligação de contatos de entrada discretos são baseadas no endereço PLC de duas partes, sem endereços de dados Modbus não utilizados. A parte menos significativa do endereço do PLC tem um intervalo de 0 a 7, portanto, é necessário um pouco de matemática para traduzir entre os endereços do PLC e os endereços de dados Modbus. Dado o endereço de dados Modbus, o endereço do PLC é determinado desta forma:

    msp := int(modbus_data_address / 8)
    lsp := modbus_data_address mod 8
    final address = msp.lsp

Por exemplo, se o endereço Modbus para uma bobina de saída discreta for 22, a parte mais significativa será 2 (22/8) e a parte menos significativa será 6 (22 mod 8). Portanto, o endereço do PLC é %QX2.6.

# Dispositivos Escravos

Para expandir o número de pontos de E/S no OpenPLC Runtime em dispositivos Windows ou Linux, é possível conectar dispositivos escravos Modbus. Essa funcionalidade é particularmente útil para sistemas que não possuem nenhum ponto de E/S, como o OpenPLC Runtime em computadores desktop ou servidores, por exemplo. O OpenPLC Runtime suporta dispositivos escravos usando tanto a comunicação Modbus/TCP (rede) quanto a Modbus/RTU (serial). Também são suportados dispositivos com capacidade sem fio que podem transmitir pacotes Modbus por TCP/IP.

Para adicionar um dispositivo escravo ao OpenPLC Runtime, siga estas etapas:

* Inicie o OpenPLC Runtime do Raspberry
* Faça login na interface web do OpenPLC Runtime.
* No menu à esquerda, vá para "Dispositivos Escravos" e clique em "Adicionar novo dispositivo".

Depois de adicionar o dispositivo escravo, é possível configurar suas definições de comunicação e integrá-lo ao OpenPLC Runtime para expandir os pontos de E/S e aprimorar a funcionalidade do seu sistema de automação. 

# Adicionando Placas Compatíveis com Arduino como Dispositivos Escravos

O OpenPLC oferece modelos para facilitar a adição de placas Arduino como dispositivos escravos. O único requisito é que a placa Arduino também esteja executando o micro-runtime do OpenPLC e configurada para exportar suas portas de E/S através do protocolo Modbus. A maneira mais simples de instalar o micro-runtime do OpenPLC em suas placas Arduino é utilizando o OpenPLC Editor com um projeto em branco

## Para adicionar um Dispositivo Escravo (Slave Device) no OpenPLC, siga os passos técnicos a seguir:

* Inicie o OpenPLC Runtime na máquina hospedeira. Certifique-se de que o software OpenPLC Runtime esteja instalado e em execução.
* Acesse a interface web do OpenPLC. Abra um navegador e digite o endereço IP ou o nome do host da máquina onde o OpenPLC Runtime está em execução. Isso o levará à interface web do OpenPLC.
* Faça login na interface web usando suas credenciais de administrador.
* No menu lateral esquerdo, clique na opção "Slave Devices" (Dispositivos Escravos) para acessar a página de configuração dos Dispositivos Escravos.
* Na página dos Dispositivos Escravos, clique no botão ou link "Add new device" (Adicionar novo dispositivo) para iniciar o processo de adição de um novo Dispositivo Escravo.
* Especifique as informações do Dispositivo Escravo, incluindo o endereço físico ou lógico, o tipo de protocolo de comunicação (por exemplo, Modbus RTU, Modbus TCP/IP) e outros parâmetros relevantes, conforme exigido pelo protocolo específico.
* Após fornecer as informações necessárias, clique no botão "Save" (Salvar) ou "Add" (Adicionar) para adicionar o Dispositivo Escravo à configuração do OpenPLC.
* Verifique se o Dispositivo Escravo foi adicionado corretamente e se as configurações estão corretas. Em alguns casos, pode ser necessário reiniciar o OpenPLC Runtime para que as alterações tenham efeito.


# Referencias bibliográficas

* OpenPLC Project. Documentação - Slave Devices. Disponível em: https://openplcproject.com/docs/2-6-slave-devices/. Acesso em: 19 de Julho de 2023.
* Modbus Organization. Modbus Protocol Technical Reference. Disponível em: https://modbus.org/tech.php. Acesso em: 19 de julho de 2023.
* CITI Systems. O que é Modbus. Disponível em: https://www.citisystems.com.br/modbus/. Acesso em: 21 de julho de 2023.
* Wikipedia. Modbus. Disponível em: https://pt.wikipedia.org/wiki/Modbus. Acesso em: 20 de julho de 2023.
* Blog Kalatec. Protocolo Modbus: O que é e como funciona. Disponível em: https://blog.kalatec.com.br/protocolo-modbus/. Acesso em: 23 de julho de 2023.

# Status do Projeto

![Badge em Desenvolvimento](http://img.shields.io/static/v1?label=STATUS&message=EM%20DESENVOLVIMENTO&color=GREEN&style=for-the-badge)
