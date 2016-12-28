:Author: jaseg
:Contact: 33c3@jaseg.net
:date: $Date: 2016-12-27 18:15:00 +0100 (Tue, 27 Sep 2016) $
:status: This is work in progress.
:revision: 1
:copyright: CC-By-Sa

=====================================
Regaining Trust in Everyday Computers
=====================================

Introduction
============

What's the Problem?
-------------------

We're using computers in most parts of our lives today. For my personal taste, frictionless communication between people
is one of the most important facilities computers provide us with today. One of the most central properties of many such
communication systems is that they allow /private/ communication. This means that we know who we are communicating with,
and that uninvited parties can at the very least not read the contents of our communications.

Today, there are many systems providing this service to us. GPG can be used to make traditional email a little more
private by hiding message content from bystanders. OTR can be used for robust encryption of chat sessions. Signal
improves on many of OTR's properties and is probably one of the most advanced cryptographically secure communication
systems available to us today.

However, in practical use, all of these systems have one fatal issue: No matter how secure the system implementation and
how good its cryptographical foundations, any system is as suceptible as the underlying machine. In case of GPG, OTR and
Signal, the things we write and that these systems aim to securely transmit is processed inside a programm running on a
commodity operating systems on a commodity hardware platform such as x86-64 or some ARM-based smartphone.

.. image:: slides/slide05.png

The picture above is a block diagram of a Lenovo x230 Laptop, an Intel-based platform. You can already see that this is
a highly complex system. In practice, this comes with lots of interdependencies between components.

To make matters worse, all of the components highlighted in red can be assumed to run proprietary, un-auditable
software.

All of this complexity and the massive amonut of highly-privileged, network-connected, secret firmware exposes a massive
attack surface to any (`well-funded attacker<https://nsa.gov/>`__). Even if a user is running a system secured according
to the state of the art, e.g. running QubesOS in a Secure Boot configuration, massive attack surface remains. There have
been numerous flaws found in both the linux kernel and the Xen Hypervisor. Proprietary firmware has not fared better,
with published hacks of both AMD's SMU firmware and Quallcomm's QSee ARM TrustZone firmware.

Mission Statement
-----------------

In my opinion, we have to solve this issue of trust. Alas, we cannot simply audit the system as-is, since auditing the
enormous amount of binary firmware contained within the system is too much work and and most of this work would have to
be re-done for every new platform. From my point of view, the most realistic way to break this tie is to augment the
system with a simple, auditable coprocessor so that trust can be placed there instead of in the lost rest of the system.

Placing the trust boundary
--------------------------

Looking at the above block diagram, we can see that in order to rule out plaintext data being handled by subsystems
running proprietary firmware, we have to place our trust boundary beyond one very important component: The CPU itself,
for it and the PCH both are running untrusted firmware.

We do have the constraint that at keyboard and display we need to have plaintext data coming through since
"crypto-glasses" are not yet a thing. The only remaining place where we can transform untrusted ciphertext into trusted
plaintext thus is on the display and keyboard matrix buses between PCH and Display, and EC and keyboard, respectively.
In the following discussion, this device will be named "Interceptor".

.. image:: slides/slide08.png

Device Overview
===============

Outside view
------------

The Interceptor will sit between display and PCH on the LVDS bus as well as between keyboard and EC on the keyboard
matrix lines. LVDS is a comparatively simple, high-speed digital display bus. Electrically it consists of 4-10
differential pairs carrying clock and data signals without any framing information beyond a serialized form of vertical
and horizontal sync signals known from the age of VGA. LVDS is used on almost all older laptops (as well as internally
in LCD monitors). More modern laptops tend to use eDP (embedded DisplayPort) instead, which is a somewhat more complex,
packet based bus (though mostly identical to LVDS on the physical layer).

The keyboard itself is implemented as nothing but a simple, passive matrix of switches that is continuously being
scanned by the embedded controller. Key events are then forwarded to the CPU via an LPC bus.

.. image:: slides/slide09.png

Compatibility
-------------

Since the Interceptor is inserted into the system at a /very/ low level, by and in itself it is very agnostic to the
underlying system. That means that apart from minor differences in host system integration, without any adaption of the
hardware or firmware it will work across operating systems and even platforms. The latter means that one could even
insert an Interceptor into a contemporary smartphone (ignoring power and space constraints for now), since modern
smartphones are using a display bus similar to the ones used in desktop systems. The primary input method of most
smartphones is the touchscreen, which generally is connected via I2C, which can also easily be intercepted as a simple
serial protocol.

.. image:: slides/slide10.png

General Implementation
----------------------

LVDS and eDP are very high-bandwidth digital buses. The most obvious device to handle these buses is probably an FPGA.
FPGAs do include serialization and deserialization logic that can easily be adapted to handle all common display buses
and can easily handle simple processing on data at this bandwidth. Furthermore, FPGAs are fundamentally reasonably
simple devices. Compared to e.g. an ARM SoC (such as they are made by Qualcomm, TI or Freescale), its hardware design is
simpler by orders of magnitude. On one hand, this increases the likelihood of verification of one such device ever
happening by keeping the effort required non-astronomical. On the other hand, this configurability allows deep
customization of the used architecture to our needs.

Controlling an Interceptor
--------------------------

In the initial iteration, the Interceptor only has LVDS and keyboard access--no other buses. This greatly reduces design
complexity and attack surface, but it makes controlling the Interceptor from the host system challenging. The initial
approach is to have the area available to it for plaintext rendering used for control data and ciphertext as is shown in
the following image.

.. image:: slides/slide12.png

In almost all systems, the outermost nested display area is the user's desktop environment. Inside, there may be a
number of independent application windows, each of which may wish to display encrypted data to the user. This encrypted
data (henceforth called "payload") is displayed inside the application window by formatting a bitmap to include some
markers and a header with meta-data, followed by the raw ciphtertext interpreted as RGB pixel data (potential correction
for gamma lookup tables etc. may apply here). The Interceptor is continuously scannig the incoming pixel data for this
marker parttern before forwarding it to the display.

About the author
================

jaseg is a student of computer science at TU Berlin, electronics and programming hobbyist and is a student employee at
Security Research Labs GmbH.

