# Tinbus
The main characteristic of tinbus are:

1. Compatible with standard UART signalling (8N1 : 8 data bits, no parity and one stop bit)
1. Media access is CDMA/CS/NDA (Carrier Sense Multiple Access / Collision Detection / Non-Destructive Arbitration)
1. Does not require a bus master or controller
1. Can provide power and data over two wires (Signal and Ground)
1. Clocks can be +/-33% when not implemented with a statndard UART
1. Galvanic isolation can be achieved with minimal cost and complexity
1. Compatible with any physical line drivers that provide a logical OR of the dominant signal (LINBUS, CANBUS, SAE J1708, Open collector)

## Line Encoding
Tinbus is compatible with standard UART encoding. It only encodes one bit per character to ensure:

1. Power is available to devices on the bus for at least 80 % of the time
1. Non destructive arbitration can be implemented with standard UART hardware.

The basic timing characteristics are illustrated below.

![Figure 1](./tinbus/tinbus.svg)

Decoding only requires the leading edge of the dominant pulse for data recovery. This makes the decoding less sensitive to asymetry in the rise and fall times and propogation times of the signal.

### Byte Encoding
Each byte of data is transmitted as 8 characters with the most significant bit being sent first. A valid frame will have a multiple of 8 characters.

### Frame Encoding
Groups of bytes that make up a message are sent as a frame. All the bits in the frame are sent without any break between characters. An idle period of at least 2 characters marks the end of the frame.

## Data Encoding
CBORM is used to serialise data into a simple and compact format. It is based on principles borrowed from CBOR. The encoding is simplifeid to a flat data structure with 3 basic message types:
1. Signed 32 bit integers
2. Base64 (6 bit unsigned values)
3. Byte arrays or strings (up to 64 bytes long)

## Error Detection
An 8 bit CRC is added to the end of all frames to provide error detection. The polynomial used is 0x97 and has optimal performance for messages up to 14 bytes.
