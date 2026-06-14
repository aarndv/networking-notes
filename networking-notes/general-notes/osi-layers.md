# The Seven Layers of the OSI Model

The OSI model is a conceptual reference from ISO (published in 1984) that divides network communication into seven layers.

> Analogy: Think of shipping a package. Each layer has a role (packing, labeling, transport, delivery route), and each role happens in order.

---

## 1. Encapsulation and PDUs

When data moves down the stack, each layer adds control information (**encapsulation**). At the destination, layers remove it (**de-encapsulation**).

| Layer | Name | PDU | Typical Examples |
| :---: | :--- | :--- | :--- |
| 7 | Application | Data | HTTP, DNS, SMTP, SSH |
| 6 | Presentation | Data | TLS, encoding, compression |
| 5 | Session | Data | Session setup/teardown, RPC concepts |
| 4 | Transport | Segment (TCP) / Datagram (UDP) | TCP, UDP, ports |
| 3 | Network | Packet | IP, ICMP, routers |
| 2 | Data Link | Frame | Ethernet, MAC, switches |
| 1 | Physical | Bits | Copper/fiber/radio signals |

---

## 2. Layer-by-Layer Quick View

### Layer 7 — Application
Provides network services to user applications (web, email, name lookups).

### Layer 6 — Presentation
Handles data representation tasks such as encryption/decryption, compression, and format translation.

### Layer 5 — Session
Coordinates session setup, maintenance, and teardown between applications.

### Layer 4 — Transport
Provides end-to-end transport services such as segmentation, reliability (TCP), and multiplexing with port numbers.

### Layer 3 — Network
Provides logical addressing and routing between networks.

### Layer 2 — Data Link
Provides local-link delivery using frames and MAC addresses.

### Layer 1 — Physical
Defines the electrical/optical/radio signaling and media characteristics.
