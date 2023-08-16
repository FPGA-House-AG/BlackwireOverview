# Introduction
Welcome to the overview of [Blackwire](https://www.blackwire.online/), [BrightAI](https://www.brightai.nl/)'s FPGA/RTL implementation of the [WireGuard](https://www.wireguard.com/) VPN protocol released under a permissive BSD license.

# Goal
The goal of this repository is to provide an implementation overview of [Blackwire](https://www.blackwire.online/), as well as the current status and roadmap towards full [WireGuard](https://www.wireguard.com/) compliance.

The top-level architectural documentation lives here.

# Status
Before we released [Blackwire](https://www.blackwire.online/) as open-source,
a small team led by [Leon Woestenberg](https://www.linkedin.com/in/leonwoestenberg/) resolved the initial challenges and provided a proof-of-concept where 100 Gbit/s WireGuard connections could be established and maintained. Our estimate is 75% of the WireGuard protocol is implemented. A lot of agile documentation was written in a three year endeavour. This needs to be filtered out and converted (TODO).

# Overview

WireGuard's Type 4 messages form the data path. The messages are encapsulated (tunneled) encrypted and authenticated IP packets, as well
as inserted 0-length keep-alive packets. The data path in [Blackwire](https://www.blackwire.online/) is fully implemented by HDL RTL code to achieve 100 Gbit/s and beyond.

Other WireGuard messages form the session management, or control path.
These are currently handled by a RISC-V core. The cryptography for
session setup and renewal can be optionally accelerated either in software or HDL RTL, depending on the maximum number of required concurrent connections.

# Roadmap

The existing work started with implementing the most risky, uncertain critical parts first. For example, the O(1) fixed-latency real-time lookup in [WireGuard](https://www.wireguard.com/)'s AllowedIP list. This complete existing work now can accept an incoming [WireGuard](https://www.wireguard.com/) session and renew it, providing 100 Gbit/s throughput.

We see no risks to achieve 100% [WireGuard](https://www.wireguard.com/)
compliance, most of the hard work has been done, we think (fingers crossed.)

Limitations in the current implementation:
- The remote peer has to start the connection.
- Only IPv4 inner/outer headers are supported.
- Only 256 concurrent connections.
- Configuring the VPN (IP addresses, cryptography keys)
- The RX path runs at 128 Gbit/s internally, TX at 64 Gbit/s.
- The replay protection is not yet integrated.
- Overload cookie support is not yet implemented.

Remaining items to implement in the open-source project then include research and implementation of the missing items:
- Open-source (remaining parts of) our [Blackwire](https://www.blackwire.online/) [WireGuard](https://www.wireguard.com/) FPGA project on GitHub and/or GitLab.
- Port developer documentation into an open format (Sphinx, readthedocs).
- Initiate connections from the local FPGA.
- Support for IPv6 inner/outer headers.
- Support 64K concurrent connections.
- Add wireguard-tools (https://github.com/WireGuard/wireguard-tools) backend to configure the smartNIC.
- Double the throughput of the TX path from 64 to 128 Gbit/s, similar to RX.
- Integrate the replay protection block.
- Add overload cookie support.
  ...

# Frameworks

We build upon top-notch open-source projects:
- SpinalHDL
- VexRiscv
- Corundum
- ...

# Tools

We build using top-notch open-source projects:
- Verilator
- GHDL
- CocoTB
- Symbiyosys
- ...