# Tinbus
Tinbus is not...
1. An efficient protocol for transferring data

Tinbus is...
1. Can be implemented with a standard UART
1. Low speeds
1. Shared bus
1. Half duplex
1. Easy to implement
1. Power and Data over 2 wires (Signal and Ground)

The protocol is intended to provide a simple method to connect devices on a bus.

## Physical Layer
Tinbus may be used with any physical layer that provides a logical OR of the dominant signal sent by devices on the bus. Examples of compatible physical layers are:

1. Tinbus (power and data over 2 wires)
1. Open collector or drain
1. Linbus line driver
1. Canbus line driver
1. SAE J1708 (which uses RS485 line driver)

The physical layer configuration will determine the maximum data rate of the bus. 

The Tinbus physical layer is designed to provide power and data over 2 wires. To achieve this the electrical signal is active for at least 80 % of the time. This allows for power to be provided to the devices on the bus.

The physical layer of Tinbus is designed to be compatible with standard UART signalling and uses 8 data bits, no parity and one stop bit. Each bit in the message is encoded using one UART character (either 0xFF (for 1) or 0xEF (for 0)). Consequently the data rate is reduced to 1/10 of the baud rate. The transmission of a zero bit is dominant.

## Data Link Layer

### Framing

Each frame is encoded using a modified Consistent Overhead Byte Stuffing (COBS) algorithm. Data frames are limited to 254 bytes in length and are delimitedted by a null (0x00) character. The COBSM encoding ensures that there are no nulls (0x00) in the message.

1. Uses Media access is CSMA/NDA (Carrier Sense Multiple Access with Non Destructive Arbitration)
1. Does not require a bus master or controller
1. Can provide power and data over only two wires (Signal and Ground)
1. Clocks can be +/-33% when the timing is not implemented with a standard UART
1. Galvanic isolation can be achieved with minimal cost and complexity

## Line Encoding
Tinbus is compatible with standard UART encoding. It only encodes one bit per character to ensure:

1. Power is available to devices on the bus for at least 80 % of the time
1. Non destructive arbitration can be implemented with standard UART hardware.

The basic timing characteristics are illustrated below, where T is the bit period.

![Figure 1](./tinbus/tinbus.svg)

Decoding only needs to detect the leading edge of the dominant pulse for data recovery. This makes the decoding less sensitive to asymetry in the rise and fall times and propogation times of the signal. It also allows data to be passed through transformers and AC coupled connections.

### Byte Encoding
Each byte of data is transmitted as 8 characters with the most significant bit being sent first. A valid frame will have a multiple of 8 characters.

### Frame Encoding
Bytes that make up a message are sent as a frame. All the bits in the frame are sent without any break between characters. An idle period of at least 2 characters marks the end of the frame.

## Error Detection
An 8 bit CRC is added to the end of all frames to provide error detection. The polynomial used is 0x97 and has optimal performance for messages up to 14 bytes.

## Data Encoding
CBORM is used to serialise data into a simple and compact format. It is based on principles borrowed from CBOR. The encoding is simplifeid to a flat data structure with 3 basic message types:
1. Signed 32 bit integers
2. Base64 (6 bit unsigned values)
3. Byte arrays or strings (up to 64 bytes long)