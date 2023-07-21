# openplc-modbus
Conexão do  OpenPLC através do Modbus

# Protocolo Modbus

# Endereçamento Modbus

O OpenPLC pode ser configurado como escravo Modbus (servidor). Um escravo Modbus é normalmente controlado por um mestre que realiza medição e controle. O mestre remoto inicia requisições de leitura e escrita para o escravo OpenPLC enviando quadros Modbus pela rede (Modbus/TCP). O micro runtime OpenPLC para placas compatíveis com Arduino também suporta quadros Modbus via serial e USB. O OpenPLC Runtime para hosts Linux e Windows possui um servidor Modbus rodando por padrão na porta TCP 502. Esta configuração pode ser alterada na aba “Settings”.

## OpenPLC suporta os seguintes códigos de função Modbus:

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
<td style="width: 50%;"><img src="https://github.com/Epaminondaslage/OpenPLC/blob/master/img/Figura_1.png/diagmodbus.png" width="50%" /></td>
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

* https://openplcproject.com/docs/2-6-slave-devices/
