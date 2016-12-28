:Author: jaseg
:Contact: 33c3@jaseg.net
:date: $Date: 2016-12-27 18:15:00 +0100 (Tue, 27 Sep 2016) $
:status: This is work in progress.
:revision: 1
:copyright: CC-By-Sa

=====================================
Regaining Trust in Everyday Computers
=====================================

What's the problem?
===================

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

Alas, the computers we use today to run these systems can barely live up to their job as ultimate safekeepers of private
information. They are /extremely/ complex in hardware and running a very baroque mess of software. The above picture is
the block diagram of a Lenovo x230 laptop. As you can already see, this is an extremely complex system and in order to
secure it one has to properly manage its many components while being aware of the complex interactions that may arise
between them. To make matters worse, some of these components (which are highlighted in red) are running closed-source
firmware. As you can see, one cannot realistically audit this enormous amount of undocumented, sometimes even
deliberately obfuscated code.

All of this complexity and the massive amonut of highly-privileged, network-connected, secret and unauditable firmware
exposes a massive attack surface to any (`well-funded attacker<https://nsa.gov/>`__). Even if a user is running a system
secured according to the state of the art, e.g. running QubesOS in a Secure Boot configuration, massive attack surface
remains. There have been numerous flaws found in both the linux kernel and the Xen Hypervisor. Proprietary firmware has
not fared better, with published hacks of both AMD's SMU firmware and Quallcomm's QSee ARM TrustZone firmware.

About the author
----------------
jaseg is a student of computer science at TU Berlin, electronics and programming hobbyist and is a student employee at
Security Research Labs GmbH.

