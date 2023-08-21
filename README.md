# Introduction
Welcome to the overview of [Blackwire](https://www.blackwire.online/), [BrightAI](https://www.brightai.nl/)'s FPGA/RTL implementation of the [WireGuard](https://www.wireguard.com/) VPN protocol released under a permissive BSD license.

# Goal
The goal of this repository is to provide an implementation overview of [Blackwire](https://www.blackwire.online/), as well as the current status and roadmap towards full [WireGuard](https://www.wireguard.com/) compliance.

The top-level architectural documentation lives here.

# Status
Before we released [Blackwire](https://www.blackwire.online/) as open-source,
a small team led by [Leon Woestenberg](https://www.linkedin.com/in/leonwoestenberg/) resolved the initial challenges and provided a proof-of-concept where 100 Gbit/s WireGuard connections could be established and maintained. Our estimate is 75% of the WireGuard protocol is implemented, see the
roadmap below for the large remaining parts. A lot of agile documentation was written in a three year endeavour.

- The documentation needs to be filtered out and converted (TODO).
- During our initial open-sourcing efforts (2023), we might have breaking GIT URI changes while we might re-arrange some of the repositories.
- Selected work-in-progress (branches, software, tests) will be made public once prepared for release.

# Overview

WireGuard's Type 4 messages form the data path. The messages are encapsulated (tunneled) encrypted and authenticated IP packets, as well
as inserted 0-length keep-alive packets. The data path in [Blackwire](https://www.blackwire.online/) is fully implemented by HDL RTL code to achieve 100 Gbit/s and beyond.

- [SpinalCorundum](https://github.com/brightai-nl/SpinalCorundum) is our set of (generic) packet processing components operating in both [Corundum PCIe Ethernet framework from Alex Forencich](https://github.com/corundum/corundum) and [Blackwire](https://www.blackwire.online/) AXI Streaming formats.
- [BlackwireSpinal](https://github.com/brightai-nl/BlackwireSpinal) are our [Blackwire](https://www.blackwire.online/) and [WireGuard](https://www.wireguard.com/) specific components.
- [ChaCha20Poly1305](https://github.com/brightai-nl/ChaCha20Poly1305) is our high-performance ChaCha20 Poly1305 cryptography core.
- [scalable-pipelined-lookup-fpga](https://github.com/brightai-nl/scalable-pipelined-lookup-fpga) is our high-performance dual-issue O(1) Internet address prefix lookup in AllowedIP lists. It builds upon a state-of-the art balanced tree based lookup algorithm.

WireGuard's Types 1-3 messages form the session management, or control path.
These are currently handled by a RISC-V core. The cryptography (mostly x25519) for
session setup and renewal can be optionally accelerated either in software or HDL RTL, depending on the maximum number of required concurrent connections. Our first x25519 core is functional, but we found it resource heavy (over-performing and/but too big) and is currently being redesigned. We may release the high-performance core for other high-end applications.

- [Finka](https://github.com/brightai-nl/Finka) is our top-level SoC with Ethernet AXI Streaming and AXI configuration interfaces (matching [Corundum](https://github.com/corundum/corundum)).
- Our SoC software stack is currently being refactored for open-source release.

- Additional repositories with smartNIC integration work and feature branches are currently subject to review for open-source release.
(We cannot support this work as it requires specific lab setups. We rather would like the open-source community to integrate into a number of NIC stacks, etc. or would like the vendors to sponsor our integration work, as it is a side-effort to the actual [Blackwire](https://www.blackwire.online/) [WireGuard](https://www.wireguard.com/) IP core and requires extra resources on our end.)

# Roadmap

The existing work started with implementing the most risky, uncertain critical parts first. For example, the O(1) fixed-latency real-time lookup in [WireGuard](https://www.wireguard.com/)'s AllowedIP list. This complete existing work now can accept incoming [WireGuard](https://www.wireguard.com/) sessions and renew them, providing 100 Gbit/s total throughput.

We see no risks to achieve 100% [WireGuard](https://www.wireguard.com/)
compliance, most of the hard work has been done, we think (fingers crossed.)

Limitations in the current implementation:
- The remote peer has to start the connection.
- Only IPv4 inner/outer headers are supported.
- No support for roaming yet.
- Only 256 concurrent connections.
- Configuring the VPN (IP addresses, cryptography keys)
- The RX path runs at 128 Gbit/s internally, TX at 64 Gbit/s.
- The replay protection is not yet integrated.
- Overload cookie support is not yet implemented.
- The handshaking crypto is not yet optimally accelerated.
- Not everything is fully configurable, we tested some low-risk items with a fixed configuration.

Remaining items to implement in the open-source project include:
- Open-source (remaining parts of) our [Blackwire](https://www.blackwire.online/) [WireGuard](https://www.wireguard.com/) FPGA project on GitHub and/or GitLab.
- Port developer documentation into an open format (Sphinx, readthedocs).
- Initiate connections from the local FPGA.
- Support for IPv6 inner/outer headers.
- Support for roaming.
- Support 64K concurrent connections.
- Add wireguard-tools (https://github.com/WireGuard/wireguard-tools) backend to configure the smartNIC.
- Double the throughput of the TX path from 64 to 128 Gbit/s, similar to RX.
- Integrate the replay protection block.
- Add overload cookie support.
  ...

For most we already have a proof-of-concept, partial design, or code that has to be enabled.

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
