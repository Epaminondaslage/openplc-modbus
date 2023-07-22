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

O Modbus é um protocolo de comunicação simples e eficiente usado para a troca de informações entre dispositivos em um ambiente industrial. Este protocolo  é suportado no OpenPLC, tornando-o uma opção a ser considerada para comunicação com dispositivos e equipamentos industriais que usam esse protocolo. 

No OpenPLC, é possível implementar a comunicação Modbus tanto na versão mestre (cliente) quanto na versão escrava (servidor). Isso permite que o OpenPLC atue como um dispositivo mestre para ler e escrever dados de dispositivos Modbus escravos, ou como um dispositivo escravo para responder a solicitações de leitura ou escrita de outros dispositivos Modbus mestres.

# Endereçamento Modbus

O OpenPLC pode ser configurado como escravo Modbus (servidor). Um escravo Modbus é normalmente controlado por um mestre que realiza medição e controle. O mestre remoto inicia requisições de leitura e escrita para o escravo OpenPLC enviando quadros Modbus pela rede (Modbus/TCP). O micro runtime OpenPLC para placas compatíveis com Arduino também suporta quadros Modbus via serial e USB. O OpenPLC Runtime para hosts Linux e Windows possui um servidor Modbus rodando por padrão na porta TCP 502. Esta configuração pode ser alterada na aba “Settings”.

# Funções Modbus no OpenPLC

O OpenPLC suporta os seguintes códigos de função Modbus:

* Ler bobina de saída discreta (0x01)
* Bobina de saída discreta de gravação (0x05)
* Escreva várias bobinas de saída discreta (0x0F)
* Ler contatos de entrada discreta (0x02)
* Ler registros de entrada analógica (0x04)
* Ler registros de retenção de saída analógica (0x03)
* Gravar registro de retenção de saída analógica (0x06)
* Grava vários registros de saída analógica (0x10)

Os endereços Modbus vinculam-se a endereços PLC com base no valor do endereço hierárquico, ou seja, endereços PLC inferiores são mapeados para endereços Modbus inferiores. Os endereços são mapeados sequencialmente sempre que possível. A tabela a seguir mostra o espaço de endereço Modbus para o tempo de execução OpenPLC Linux/Windows:

<table border="0">
<tbody>
<tr>
<td style="width: 50%;"><img src="https://github.com/Epaminondaslage/OpenPLC/blob/master/img/diagmodbus.png" width="50%" /></td>
</tr>
<tr>
<td style="text-align: center;">Figura 13 - Endereçamento Modbus para Openplc em Windows e Linux.</td>
</tr>
</tbody>
</table>

Devido a limitações de memória, plataformas embarcadas como placas Arduino possuem um espaço de endereçamento Modbus muito mais limitado, conforme tabela abaixo:


<table border="0">
<tbody>
<tr>
<td style="width: 50%;"><img src="https://github.com/Epaminondaslage/OpenPLC/blob/master/img/diagmodbusarduino.png" width="50%" /></td>
</tr>
<tr>
<td style="text-align: center;">Figura 14 - Endereçamento Modbus para Openplc para Arduino.</td>
</tr>
</tbody>
</table>

Plataformas baseadas em ATmega 328P, ATmega 168, ATmega32U4 e ATmega16U4 como o Arduino Uno e similares têm um espaço de endereçamento ainda mais limitado devido à sua disponibilidade de memória RAM ser muito baixa:

<table border="0">
<tbody>
<tr>
<td style="width: 50%;"><img src="https://github.com/Epaminondaslage/OpenPLC/blob/master/img/diagmodbusarduinouno.png" width="50%" /></td>
</tr>
<tr>
<td style="text-align: center;">Figura 15 - Endereçamento Modbus para Openplc para Arduino Nuno, ATmega 328P, ATmega 168 entre outros.</td>
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

    Inicie o OpenPLC Runtime do Raspberry
    Faça login na interface web do OpenPLC Runtime.
    No menu à esquerda, vá para "Dispositivos Escravos" e clique em "Adicionar novo dispositivo".

Depois de adicionar o dispositivo escravo, é possível configurar suas definições de comunicação e integrá-lo ao OpenPLC Runtime para expandir os pontos de E/S e aprimorar a funcionalidade do seu sistema de automação. 

# Adicionando Placas Compatíveis com Arduino como Dispositivos Escravos

O OpenPLC oferece modelos para facilitar a adição de placas Arduino como dispositivos escravos. O único requisito é que a placa Arduino também esteja executando o micro-runtime do OpenPLC e configurada para exportar suas portas de E/S através do protocolo Modbus. A maneira mais simples de instalar o micro-runtime do OpenPLC em suas placas Arduino é utilizando o OpenPLC Editor com um projeto em branco



* https://openplcproject.com/docs/2-6-slave-devices/

# Status do Projeto

![Badge em Desenvolvimento](http://img.shields.io/static/v1?label=STATUS&message=EM%20DESENVOLVIMENTO&color=GREEN&style=for-the-badge)
