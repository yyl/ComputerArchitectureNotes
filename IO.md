I/O, disk
=====

Measuring I/O performance for different types of computers:

1. desktop, embedded system: response time and diversity
2. server: throughput and expandability

    Elapsed time = CPU Time + I/O time

A bus is a shared communication link

- control lines
- data lines
- low cost, versatility 
- types by architecture
    - processor-memory bus
    - I/O bus
    - backplane bus
- types by protocol
    - synchronous with a clock
    - asynchronous: handshake protocol

Porcessor gives commands to I/O device

- memory-mapped I/O
    - an address space in memory reserver for I/O
    - only processor could access
    - read and write here are considered as commands to I/O devices
- polling
    - I/O devices put their updated status to device status register
    - processor actively check registers for new information
    - inefficient because processor checks too fast while I/O device works much slower
- interrupts
    - I/O devices issue interrupts to processor to inform communication
    - OS only checks for pending interrupts
    - different interrupt priority levels

I/O devices communicate with memory: **DMA**

- Direct Memory Access
- no processor involed
- issue interrupt when it is done
- **DMA controller** becomes bus master
