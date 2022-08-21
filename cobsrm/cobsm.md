# COBSM
Is a zero delimted packet framing scheme. 

It encodes any zeros in the source data so that the resulting data can be framed or terminated using a zero byte. This is a common problem and has many existing solutions, including but not limited to SLIP and COBS.
## Purpose
To provide a memory efficient and speed performant method to frame small packets of data (less than 254 bytes).
## Background
COBSM is based on COBS and COBS/R. It has been modified to allow in place encoding and decoding. The purpose of this change is to improve both speed and memory usage. 

This adaptation introduces a packet size limit of 254 bytes. The original implementation encodes arbitrary size packets. 

If the first data byte is numerically greater than the size of the packet then the encoding will not require any extra bytes. Otherwise, one extra byte is required. Upper layers can exploit this feature to reduce the encoding overhead.