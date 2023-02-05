Nestihl jsem projít. Může tam být:
- Managing a software project with a security as an objective, advantages and disadvantages of waterfall and ellipse model for this use-case, systematic identification of potential vulnerabilities, STRIDE, attack modelling (attack trees), ranking of vulnerabilities (ideal, DREAD).
- Timing and storage covert channels, Side channel attacks, Steganography.
- Discretionary access control(Access control list, Capabilities), Mandatory access control, Multi-level security, Biba model, Multi-lateral security, Role-based access control.
- Privilege escalation, security of operating systems, trusted computer base, reference monitor, complete mediation, needed mechanism for securing current OS, memory management, rings.
- Virtualization, virtual machine monitor, micro-kernels, general-purpose sandboxing, danger, Kernel namespaces, seccomp, Linux kernel capabilities. 
- Access control model of web ecosystem, single-origin policy, preservations of integrity of data and code, sandboxing in web, content security policy.
- Network protocols, TCP, DNS, BGP, security of HTTPs, mechanism of certificates, security of certificate infrastructure.
- Firewalls, network intrusion detection, network intrusion prevention, thin client, intrusion deflection.
- Denial of service attack, reflection attacks, syn-cookies, detection and protection against DOS.

# Guidelines for writing Secure Code

3 assets to protect:
- **Confidentiality**: data is private and it is not supposed to be revealed
    - Protection: *Encryption* VS Performance
- **Integrity**: attacker modifies victims data, code or system
    - Fake news
    - Protection: *Signature*
- **Availability**: victim service is unavailable, no money, no sense

## Development approaches

### Waterfall model

Requirements -> Specification -> Implementation -> Integration/Testing -> Maintenance

- Never go back in the development phases
- Problems: 
    - Difficult in later changes (customers do not know, what they want)

Pros:
- requirements are defined
- risks evaluated in the beginning

Cons:
- difficult to handle changes
- problem implementing new features

### Evolving (Spinal, Iterative)

Making small prototypes, then bigger...
- Microsoft approach VS IBM btw.

Pros:
- software is produces quickly
- flexible
- focus on client approval
- focus on documentation

Cons:
- difficult to implement
- problem with finishing the project - can loop forever
- risks need to be evaluated always again

### How to design secure application
- Implement security since the beginning of the development
- *Policy* -> define security rules for users
    - It should reflect assumptions about the possible attacker user
    - Examples:
        - holding more than 50 % of crypto mining makes control
        - if NSA has more the 50 % of TOR nodes, it can eavesdrop anything
    - Cryptography complexity is breaking over time

#### Steps in threat modeling
1. Decompose the Application
    - Think about each component of the application and its security pillars
2. Determine threats
    - **STRIDE**
        - *Spoofing the identity* -> impersonating identity
        - *Tampering with the data* -> modifying the data or code
        - *Repudiation* -> perform an action and deny doing it
        - *Information disclosure* (confidentiality)
        - *Denial of service*
        - *Elevation of privileges*
            - **Horizontal** -> same privileges on different account
            - **Vertical** -> increase level of privileges
    - Build all possible attack trees
3. Rank threats by decreasing risk
    - Economy harm criterion
    - Attack probability criterion
    - **DREAD** ranking
        - *Damage potential*
        - *Reproducibility of the attack*
        - *Exploitability*
        - *Portion of affected users*
        - *Discoverability*
4. Chose how respond to threats
    - Accept and do nothing
    - Warn the user
    - Remove the buggy feature
    - Fix the bug
5. Chose techniquese to mitigate the threats

## Security Principles
- Use defense in depth
- Use least privileges
- Psycho acceptability
- Learn from mistakes
- Minimize an attack surface
- Separation of privileges
- Assume external systems are insecure
- Backward compatibility always causes a headache
- Plan on failure - fail securely
- Never depend on security through obscurity alone
- Don't mix code and data

# Covert channels & friends

## Covert channels

= a type of attack that creates a capability to transfer information objects between processes that are not supposed to be allowed to communicate by the computer security policy.

[Wikipedia](https://en.wikipedia.org/wiki/Covert_channel)

### Detection
- Difficult to find them
- Estimate bandwidth of chanel
- Identify shared resources

### Storage covert channels
- Uses system variables and e.g. file attributes
- Both entities have access to shared resource
- Entity can force the resource to change
- Receiving entity can detect change
- The mechanism to initiate communication exists (e.g. another covert channel)

### Timing covert channels 
- Influences time to transport message
- Both have access to shared resource
- Both have same reference time
- Sender can change receiver's understanding of time
- The mechanism to initiate communication exists

## Side-channel attacks

= any attack based on information gained from the **implementation** of a computer system, rather than weaknesses in the implemented algorithm itself (e.g. cryptanalysis and software bugs). Timing information, power consumption, electromagnetic leaks or even sound can provide an extra source of information, which can be exploited.

[Wikipedia](https://en.wikipedia.org/wiki/Side-channel_attack)

Examples:
- Timing -> password hashing length processing
- RSA problems:
    - multiplicative: `m_1^e \* m_2^e = (m_1\*m_2)^e mod n`
    - deterministic: it is possible to quess, what message was encrypted by trying to encrypt
- Power monitoring
- EM monitoring
- Acoustic analysis

## Steganography

= art of undetectably communicating message in an innocuous-looking object

**Cryptography = privacy X Steganography = secrecy**

Objects having a high volume of noise inside are best. Eg. photos from smartphones are not the best, because of noise filtering.

### Detection

Least-significant odd bit 

## Steganalysis

- **Visual**: image is preprocessed and can be human-detected.
- **Heuristic**: relies on knowledge of part of key/algorithm/message. (e.g. evaluate pixel values etc.)
- **Blind**: combines both -> usually machine learning

## Watermarking

Typically used to prove ownership of media. Is robust against removal. Can be detected and has low capacity.

# Access control rights

= formalism for security description
- Access control matrix is a method to precisely describe a security state.

**Security model**
- Security model provides a formal representation of the access control security policy and its working.
- The formalization allows the proof of properties on the security provided by the access control system being designed.

**Security policy**
= rules definitions for authorization.
- Very strict policy tends to be bypassed.

**Security state**
= a subset of state of the system that is related to the security.
- The system can traverse through security states (like in graph).
- List of all principles, users, processes, everything with access and with which can be interacted.
- Principle = e.g. `ls -l`

**Secure system**
= a system that starts in an authorized state and cannot enter an unauthorized state.
- DUCK -- Access control matrix
- When users have ownerships between files, they are trusted to act with accordance to the policy -> NP complete

## Discretionary access control and the difficulty of formal proofs
- **Access control list** (unix = file descriptor, win = handles)
    + Easy to implement
    - Hard to know, to which files and resources have some user or app access
    - Cannot grant temporary access (except for manual control)
- **Capabilities** (e.g. access tokens)
    + addresses temporary access problem
    - hard to detect bugs, needs token logging

## Mandatory access control

Users cannot change security policy, only policy administrators can.

- **Multi-level security --- Bell-Lapadula** (e.g. NATO levels: unclassified -> confidental -> secret -> top secret)
    - Every info was stored in one silo, so Chinese hackers revealed it
    - [Bell-Lapadula](https://en.wikipedia.org/wiki/Bell%E2%80%93LaPadula_model)
        1. Simple: *No read up.*
        2. Star property: *No write down.* (Top secret cannot create a confidential level.)
    - When touching higher file, you must be ranked up, but you may not necessary need to write there.
    - **Tranquility property**: security levels do not change during normal operation
        - The **strong tranquility** property says that Subjects and objects do not change labels during the lifetime of the system.
        - The **weak tranquility** property says that Subjects and objects do not change labels in a way that violates "spirit" of the security policy.
    - Problem -- Covert channels: When writing up, write down don't stand, so it cannot be confirmed, that write up was successful (solution: data diode -- copying up nonstop?)

- **Biba**
    - [Biba](https://en.wikipedia.org/wiki/Biba_Model)
        1. Simple: *No read down.*
        2. Star property: *No write up.*
    - Examples: iOS

- **Compartments**

- *SE Linux*

## Role-based access control *RBAC*

Principle
- Needed in large companies -> employees receive roles
- Each user of the system receive one or more roles
- Each role has access to defined assets

Pros:
- Easy to manage
- Simply limits access

# How priviledges are enforced in OS?

## Trusted computer base
- [wikipedia](https://en.wikipedia.org/wiki/Trusted_computing_base)
- Hardware or software that has been designed to be a part of the mechanism that provides its security to the computer system.
- 3 requirements:
    - Complete mediation
    - ???
- Parts of Trusted computer base:
    - Kernel and all its modules (device drivers)
    - Window management systems
    - Systems verifying authenticity (SSH, login).
    - All root processes

## Priviledge escalation
- **Horizontal** -- same privileges on different account
- **Vertical** -- increase level of privileges

![](privescal.png)
Example attack types:
- Attacks on priviledges (blue)

What is needed to prevent privilege escalation?
- Data execution prevention
- Address space layout randomization
- Using least privileges possible
- Encryption of software and firmware

OS requirements:
- scheduling
- process isolation (memory management)
- inter-process communication

Further requirements on OS:
- Complete mediation
- Tamperproof
- Verifiable

### Principle of Complete Mediation

User data is untrusted.

Every call will be verified by **reference monitor**".

Defines a set of design requirements on a reference validation mechanism, which enforces an access control policy over subjects' (e.g., processes and users) ability to perform operations (e.g., read and write) on objects (e.g., files and sockets) on a system.

Requirements:
- Non-bypassable - attacker cannot violate policy
- Evaluable
- Always invoked
- Tamperproof

Problems:
- Policies are sometimes checked only in the beginning.
- It doesn't take the process name in the account. So leaked daemon can run other processes affecting kernel.

### Time of check to Time of use

Occurs, when linux kernel is preemptible.
Example:
```
open(".bashrc", "w")

1) Check access
* But kernel can be interrupted! ln /etc/passwd .bashrc
2) Open file, return file_descriptor
```
How to prevent this?
- Check file inode.


## Mechanisms of contemporary operating systems to isolate processes and prevent compromise of itself (mediation)

### Isolation of processes
The process should feel like running alone.
Therefore it needs to have separate
- registers,
- all kernel structures (file descriptors, network connections etc.),
- memory.


#### Virtual memory

Know from OSY.

Processes shouldn't know about the existence of the page table. 


#### Rings

Kernel (ring 0) \> device drivers (ring 1) \> other drivers \> user processes (ring 3)

Switching between rings is performed with every *system call* through **gates**.

It don't have anything to do with user.
Users are separated from hardware.
If something is run under root, it is not ring 0.


### Anatomy of a system calls

- invoking trap instruction
- arguments passed via registers
    - one arguments says number of instruction to execute
- kernel looks up call in syscall table by instruction number
- actual system call
- return to trap handler
- return values are back in registers

![](syscallimpl.png)


## Types of kernel

**Monolytic**
- Important execution run inside kernel, user application run outside.
- Contains unnecessary services in kernel --> fast

**Modular**
- Attempts to decrease size of kernel --> lower probability of bugs
- Allows administrator to add/remove un/used modules

**Micro**
- Kernel contains only necessary services. Anything other is outside.
- Drivers are outside.
- Allows *formal verification* -- it mathematically ensures security.
    - Haskell implementation, without global variables.

Examples:
- Hybrid - Windows, macOS
- Modular monolithic kernel - Linux


# Running not so trusted code

## Confinement Mechanism
Prevents processes taking disallowed actions.
- virtualization = specific type of sandboxing
- sandboxing - container/app specific
- air gapping
- native client

### Air Gaps
- detach system from the Internet, never connect again
- turn on encryption
- use minimal software required
- disable autoruns
- consider stateless OS (Tails)

Can be attacked by covert channels - acoustic noise, electromagnetic radiation etc.

## Virtualization
To simulate (ring 0) instructions on virtual machine

![](virttypes.png)

- **Hypervisor**
    - communicates with HW directly/through the OS of host
Types:
- **Full virtualization** = type-2 (e.g. QEMU)
    - Guest OS is fully isolated from the hypervisor.
    - Privileged instructions are sent to HW and then trapped and emulated by Hypervisor.
- **Paravirtualization** = type-1 VM (e.g. [XEN](https://en.wikipedia.org/wiki/Xen))
    - Guest OS kernel uses hypercalls to pass priviliged instructions to Hypervisor who handles them to HW.
    - More secure than Full Virtualization
    - Less portable and compatible.
- Native virtualization -- most common

### Mechanisms:
- Separation of memory
    - Guest virtual memory is visible to applications of guest OS.
    - Guest physical memory is managed by guest OS.
    - Host virtual memory is visible to guest OS.
    - Host physical memory is managed by virtual machine monitor
    - **Paging** in VMs: (Complicated)
        - **Nested**
            - hardware assisted virtualization
            - pages are nested
        - **Shadow**
            - sw based virtualization
            - pages are duplicated
            - significant overhead
- Time-sharing of cpu
- Multiplexing IO
- Protection virtual machine monitor

### HW Support
#### Isolation without HW support
- Virtual machine monitor in ring 0, kernel in 3
- Impact - there are no gates to cross between apps and kernel
#### Isolation with HW support
- Virtual machine monitor in **ring -1**, kernel in 0

### Applications & Advatages
- **Enforcing network rules** -> connect workstations to TOR router (anonymization), then firewall, no different path (Isolating dangerous stacks)
- **Road balancing** -> defense against DoS attacks
- **Snapshots**, easy recovery, **migration** -> snapshots can be moved to another VM
- Simplicity of development, integration, deployment
- Data leaking / protection
- Separation of duties
- Allow to set coarse-grained policies

### Disadvantages
- **Attack vectors** -> VMs are sensitive to access attacks
- VMM can be buggy
- **Lack of visibility** -> difficult to see what actually happens in container
- **Virtual machine sprawl** -> the number of VMs exceed network capabilities of the system (Spectre & Meltdown)

## Hardware attacks
- **Ring -2** (System monitor) -> can be infected, more - rings don't help
- the integrity where the VM runs can be verified through **Trusted Platform Module**
    - has cryptographic material and computes BIOS microcode hash
    - **hashes** are stored in special registers and verified by XOR


# Sandboxing

A mechanism that isolates application from critical resources either to protect from them or protect them.

**Software Fault Isolation**
- Allows running untrusted native code by sandboxing all store, read and jump assembly instructions to isolated segments of memory.

## Mechanisms

- **Control groups**:
    - Processes are in groups which are prioritized, so slow and humble processes do not get all memory and cpu, because its recources are limited.
- **Kernel Namespaces**
    - IDs inside namespaces are not shared among different subtrees of processes.
    - kernel’s resources are partitioned such that a set of processes sees some resources and other processes sees something else group
    - hides identity of control group of which the process is a member
    - allows putting quotas on users, memory, cpus
- **Kernel Capabilities**
    - Solve a dichotomy of root and basic user. Ping needs privileged instructions (opening ports). Analogic to Android permissions (Discretionary Access Control).
    - Further limiting together with Namespaces
    - Allows limit access to ports etc.
    - Restrict cron access
- **seccomp-bpf** (secure computing mode)
    - Linux have around 450 syscalls
    - Allows filtering of system calls available - crucial for Docker

## Containers / General-purpose sandboxes

Docker

Pros:
- cheap context switches
- isolate apps
- share system libs

Cons:
- be avare that kernel is shared
- never trust dockers downloaded from public repos


# General defenses to stack / heap / exception overflow

## Buffer overflow

Most probable usage is to jump to address where you *start bash console* --> root access.

The program writes more data that was actually allocated by buffer. The overflow can cause injecting attacker’s code into running process and take over the process.

### Canaries (solution)

Data placed between a **buffer** and a **control data** (return address, locals, ...) on the stack. In case of overflow, first will be corrupt a **canary** and the overflow can be thus propagated further.

Magic value:
- 0\<cr\>\<if\>-1
- Stored in a specific page
- Search for it makes error

It do not require to rewrite source code, only to recompile.

## Heap Overflow

A type of buffer overflow attack located in heap.

Adjacent memory space in heap is exploited.

The attack stands in corrupting the data to damage internal structure of the heap, e.g. linked lists etc.

Example:
- allocate two buffers next to each other
- overwrite first buffer, damage metadata of second one
- free first buffer - the pointer to first buffer will have access to both BK and FD

Solutions:
- Change compiler
- Place Canaries

### Data execution prevention

Place flags, which allows to execute code with allowed flags only.

## Return Oriented Programming Attack

Original idea to jump to lib.c with arguments.

Instructions have different lengths, the oldest have small length.
Example:
```
mov edx,C23BCB50h
Compiles to BA 50 CB 3B C2
50 CB 3B C2
```
Recompiles to 
```
push eax
return
cmp
```

The attacker needs to know source code. Then the attacker gets access to **gadgets** that are in memory and result in return operation. These gadgets allow attacker execute a chosen set of instructions. Programs are usually Turing Complete language to be misused.

### Address space randomization (solution)
In order to prevent an attacker jump directly to an address he expects, address space randomization randomly arranges address space positions of process data and make it more difficult to willingly change code or data.
It complicates prediction of target address.

#### Problems
- lower randomness in 32bit systems
- attacks can be repeated

## Structured Exception Handler Overwrite

Similar to heap attack. Forces program to crash and then overflows linked list of the exception handlers.

Overwriting location of exception handler by malicious code that should be executed when exception happens.

### Structured Exception Handler Overwrite (SEHO) Protection

Checking integrity of exception handler. Using default exception handler. Crashing if integrity is corrupted.
- a hierarchy of exception handlers
- should point to default one

## Write/execute bit for memory pages (NX-bit)
Sections of memory can be marked as non-execute. By doing so, it can prevent attacks such buffer overflow etc. to execute malicious code. The system throws error if such part of memory should be run.


# Security of Browsers

## Why security of Browser is so important
- Browsers got more complicated, complex
    - have a lot of features
    - parsing html, css
        - together are Turing complete language
    - playing video
    - cookies
- Relying a lot on 3rd party code -> potential bugs
    - plugins, extensions
- Attacks on:
    - in-browser resources:
        - credentials
        - credit cards
        - personal information
    - out of browser resources:
        - getting access to computer files, processes, remote execution

## Difference between plugin and extension and impact on security
- **Extension** - inside the browser
    - javascript
- **Plugin** - outside, connected through an API
    - c++, java

## How is this solved
### Sandboxing
- **Broker process** (browser kernel)
    - cookie, history, password databases
    - Window management
    - SSL
- **Rendering engine**
    - What is this doing:
        - html, css parsing, javascript interpreting
        - layout, image decoding, svg
    - When this is compromised, it can attack the broker process
- Windows implementation:
    - similar to object-oriented programming
    - security:
        - each object in windows can have an attached security identifier
        - mandatory integrity control
        - by default access denied, restricted access tokens, job object limitations
        - window station and desktop isolation, since win-vista
    - exports API for sandboxed applications
    - common exploits:
        - event-driven execution => renderer can inject messages, that can be executed with higher privilege
        - renderer can be put into a different desktop (desktop is object)
        - rendered can use all OS resources
    - template for word, adobe player
- Linux implementation:
    - similar to docker
    - security:
        - empty root
        - process, user and network namespaces
            - requires latest kernel 
        - syscall whitelist
        - seccomp-bfl
        - setuid and selinux is old and deprecated

### Site isolation
- similar to OS architecture
- distinct threads communicating with each other via *IPC*
    - **IPC** = inter-process communication, used for sharing memory, like system calls
- browser kernel is the only source of access to OS resources
- operations go through broker process

### Priviledge separation
    - separate process per page

### Plugins
- Has overview only of the current page, not the whole browser. It’s access is limited.

### Extensions
- Firefox came with idea, everything is extension
1. **Content script** (visual part of the page)
    - direct access to page DOM
    - no other privileges
    - communicates with extension core
    - largest attack surface, no direct access to core
    - Javascript has access to everything on the page, quite powerful
2. **Extension core** (tabs, bookmarks)
    - controls extension UI, popups
    - cannot access content script
    - communicates with content script or issues XMLHttpRequest
3. **Native binary** (process, filesystem)
    - can run arbitrary code or access files

### Isolation Mechanism
- Isolated worlds
    - content script has its own version of DOM
    - it shields extensions from other
    - more difficult to manipulate content script data by malicious website
- Manifest
    - json settings
    - contains permittions describing, what can this extension access    
- (Process isolation)
    - extensions run in separate processes
    - attacks on rendering core will not grant access to extension core living in different process


# Security of web applications, Access control of web (Tangled web)

Large companies war agains each other with browsers. First was Microsoft, then Google.

Why is the need for browser usage?
- company knows, what are users visiting => data

## Threat landscape of browsers and web
Common problems:
- inexperienced users
- difficult to verify data origin
- web do not have anything like access rights. Trust state is difficult to achieve.

Most attacks are on:
- injection
- broken authentication
- sensitive data exposure

## Difficulty of maintaining the trust state in asynchronous communication
Necessity to hold session id or similar identifier. Attacker getting to sessionID can exploit, e.g.: session hijacking
- prevention: 
    - unguessable ids -> long identifiers
    - checking of IP (should stay same)
    - same cookie with every request

    - Integrity of code
    - Integrity of data
    - Integrity of session

## Mash-ups
- websites combining multiple data sources
- potential for bugs, security holes

Trusted and untrusted data is mixed ==> trust state is lost. Then we do not know, what is malicious in the page content.

When multiple java-scripts are present, each can communicate with each other, although they are different processes.

## Single-origin policy
Two website should not be able to communicate unless they want to.
- each resource is assigned origin (protocol, domain, port)
    - DOM tree - JS reflection of html in page
    - Cookies 
    - Web storage - associated with origin as well
    - CSS, Images, Javascript
    - Files
    - Plugins - define policy on their own
- iframe has own origin
- if it contains iframe, it has an authority of its origin

Passive content - no authority at all

The CORS is evaluated by browser.

## Problems of permissive parsing
- difficult to parse HTML
    - some people forget quotes around attributes etc.
- opens space for bugs

*Post Message*
- framework, not known much
- provides supervision of process messages?

## Cookies
- provides the way how to maintain asynchronous communication
- the browser includes cookies for a given site into every request
    - cannot be set to more specific sites, eg. only for `foo.example.com`; other site formats must be set explicity in a browser list to be allowed
    - set’s * automatically: `*.foo.example.com`
    - OK: `foo.example.com`, `example.com`
    - Wrong: `bar.foo.example.com` `.com`
- can be set if it belongs to HTTPS or not
- must be protected or Cross-site-request forgery can occur (2007 example of stolen gmail acoount)

## Cross-site-scripting XSS
- malicious code is injected into target website
- in case the website includes user-entered data into DOM
- protection:
    - sanitizing data

### Attacks of XSS

#### Cross-site-request forgery CSRF
- the attacker can guess url form
- malicious website tricks victim into performing an unwanted action on a website the victim is authenticated to
    - browser automatically includes authentication information for given website
- protection:
    - the server generates a token that is hidden on user side
    - the token is then sent with the request and evaluated by the server

#### DNS Rebinding Attack
- goal -> run code of attacker with victims authority
    - register DNS server and attacker.com
    - trick user to visit attacker.com
    - attacker’s DNS issues short TTL DNS response so it cannot be cached
    - the victim’s computer downloads malicious scripts from attacker.com (currently same origin)
    - the downloaded malicious code can download additional resources as being under the same origin

### Protection against XSS

#### Sandboxing with HTML-5
- html5 provides a new tag sandbox for iframe
    - allows script execution, same origin resource fetching, popups, form submission: `sandbox="allow-same-origin allow-scripts allow-popups allow-forms"`
    - [Fullscreen API attack](https://feross.org/html5-fullscreen-api-attack/)

#### Content security policy
- splits object privileges
- specifies content of trusted sources (whitelist)
- permitting depending on where the content can come from, separated to imgs etc.
- keywords:
    - none - no permitted resources, cannot download external scripts
    - self - only current site
    - unsafe-inline - permmits forms
    - unsafe-eval - allowing evaluation of java-script

##### Strict transport security
- prohibits website to load under HTTP


# Insecurity of Internet Protocols (Legacy Protocols)

## The importance of a protocol design
- most of the problems are in the protocol itself, not in implementation
- commonly related to IPv4 - would be solved by IPv6

## Threat model for network security
- Man in the middle
    - an active eavesdropping
- Participation in the protocol
- Eavesdropping
- Brute forcing

## ARP spoofing / poisoning
- [wiki](https://en.wikipedia.org/wiki/ARP_spoofing)
- incorrectly answering ARP packets to change attacker’s identity to e.g. default gateway
- attacker gains access to local network
- attack do not scale, only scales on wifi size
- prevention:
    - static IP and MAC records, but annoying
    - ARP detection software - cross-checking ARP responses
    - router can remember a small ARP history, but it can be flooded

## IP Protocol vulnerabilities
- plain text IP addresses - we can see who communicates
- no encryption potential
- no integrity checks

### IP Spoofing
- ISP can check, if the source address is ok
    - this measure is not implemented everywhere
    - it is possible, someone is google but it is not

## TCP / IP protocol vulnerabilities
### Security guarantees
- assures delivery
    - acknowledgement of packets (sequenceNo)
    - if not match - drop connection
    - relies on “difficulty” to guess sequenceNo
- full duplex, bidirectional
- automatic fragmentation

### Mitnick attack (TCP SYN attack)
- Attacks TCP handshake:  (SYN SEQ*a*)\>  \<(ACK*a+1* SEQ**b**) (Addr ACK**b+1**)\> 
- Vague inicialization (can be exploited with simple implementation)

1. guess sequenceNo **b** (from telnet connections)
2. send a lot of syn requests from spoofed IP address
3. server will keep a lot of open connections that will flood it’s memory - DoS
4. install backdoor
5. unfreeze the server by sending RST packets

## UDP and NAT
- in case of TCP easy to use 
- NAT needs to remember connection is happening for a moment

## UDP and DNS
- send packet, forget the rest
- used by DNS etc.
- DNS server is in chain

## DNS
- For the security the best is to decentralize DNS
- DNS resolution:
    - local cache /etc/resolv.conf
    - router
    - ISP DNS - **recursive nameserver** can give more addresses to redirect the query
    - world DNS
- **Query ID** - unique to every query, if not random, exploit

### Impact on security of web
- DNS is used for most of the protocol thus it needs to be secure

### Cache poisoning
- the attacker tricks DNS server to insert his DNS record into its cache
- the server responds with invalid records unless the cache is flushed

### Changing DNS in a router
- DNS settings can be queried in router from ISP
- changing router’s DNS will have impact on all computer in the network

### Dan Kaminsky attack
- exploit DNS query ID
- using a lot of sibling names to create a lot of domains
- asking for such names to guess *recursive nameserver’s* transaction id
- then it was possible to spoof the reply, if attacker has faster network
- protection:
    - **randomize Query ID**
    - **UDP source port randomization**
        - in case attacker can guess trxId and source port for query, attacker can immediately response

On November 2020 CCS conference paper showed guessing source port.

## BGP protocol
- border gateway protocol
- allows to let routers react to dynamic changes of topology
- favors more specific and shorter routes
- problems:
    - anyone can announce anything
    - based on trust

### Hijacking BGP protocol
- announcing more specific records
- traffic will be routed to my servers
    - can be dropped or forwarded
    - forwarding can include amending of TTL to be undetected

### Defenses?
- paying special services to check consistency of BGP tables
- service detects a “detour” from path

## Evil Twin/Honeypot Attack
- type of wireless network attack
- creating a fake Wi-Fi and tricking users to connect
    - get their credentials

## Security of HTTP protocol
- HTTP protocol itself is plain text protocol
- HTTPS is a secure alternative encrypting the data by TLS
    - relies on certificates and their cert. authorities
    - handshake:
        - Client hello - client sends available ciphersuites + client random
        - Server hello - SSL cert, chosen suite, server random
        - Client verifies server’s authenticity (by cert)
        - Client sends pre-master key secret decrypted by server’s certificate
        - Both derive symmetric keys and can communicate
    - Cipher suite contains:
        - SSL/TLS version
        - exchange algorithm
        - encryption algorithm
        - message authentication algorithm


# Secure Alternatives

Symmetric cryptography

Asymmetric cryptography
- private + public key

## Key exchange with public-private keys
- can be exchanged by Diffie-Helman exchange / ECDH
    - both sides agree on modulus p and base g
    - both compute - computing X = g^a mod p, g^b mod p
    - they transfer the result and compute s = g^X mod p

The problem is with the trust of inputting the password somewhere.

Russians operates Kerberos, so I give credentials to Russians?

## Certificates
- Certificate agency gets hash key, that encrypts a site. So 3rd trusted site provides public key to me, who access.

### The role of certificates in the exchange
- Shift of roles
- are used to verify authenticity of the key by certificate chain

### Problems of certificates
- Certification can be corrupted, CA can be adversary
- Man-in-the-middle can send you fake screen (without https)
- Deleting CA
    - the certificate authority can be manually removed
- **Certificate revocation**
    - in some case, adversary can guess hash collision, so certificate revocation is needed
- OCSP (Online Certificate Status Protocol)
    - privacy is violated, we are sending them info, what we visited
    - **Stapling** (sesivani)
        - [wiki](https://en.wikipedia.org/wiki/OCSP_stapling)
        - querying the certificate authority all the time is an overhead
        - the server can verify validity of the certificate from the vendor and send staple from certificate authority to client
            - CA signed time-stamp - difficult to change
- **Public ledger**
    - It is huge
    - much more transparent than OCSP
    - it all stands on a trust - if we lose trust in CA, we cannot use certificates anymore
- **Bloom filter** = set of hashes
    - ISP-wide? More decentralized than public ledger
    - When checking a site, I ask in negative way, If the site hash is in Bloom filter

### Issuing certificates --- verification of identity
- issued by CA
- CA are cross-verified by other CA

## Attacks
### HTTPS stripping
- downgrading HTTPS to HTTP
- Man-in-the-middle
- victim initiates connection to attacker who is forwarding the requests to target server
- the attacker changes HTTPS to HTTP and let’s victim communicate in HTTP
    - all data are in plaintext
- **Protection**:
    - always use HTTPS after next open window (however the first can be exploited)
    - browsers come with a list of trusted servers

### Protocol downgrading
- Pretend, server do not support higher versions of protocols, lower versions can be exploited

### Company certificates
- after taking control of company certificate, the door is open
- **Protection**:
    - **Certificate pinning**
        - Remembering certificate/has of the server we want to communicate with
        - Ee can directly check it is correct
        - Problem with distribution -> *Key Distribution Center*
        - In case of Let's Encrypt:
            - Lets Encrypt tells you to put a file to site and they compare those files
            - Dependant on DNS

### Cache poisoning
- https://owasp.org/www-community/attacks/Cache_Poisoning 
- can be achieved by:
    - flushing the cache (http headers)
    - use CR and LF in the requests
    - append another response to the request
- **Protection**:
    - prohibit response splitting (sending multiple responses per one request)

## HSTS
- HTTP Strict Transport Security
    - a way how to enforce HTTPS communication for your site
    - sent via header

## DNSSec --- chain of trust
The problem is verification of DNS requests.
Therefore:
- DNSSEC adds *signatures* to DNS records
    - previous problem of certificates
    - records have RRSIG
- Zone Signing Key (ZSK)
    - signs records
- Key-Signing Key (KSK)
    - signs ZSK
    - the zone further has DS (delegation signer) record which is a hash of KSK by parent
- The servers build a hierarchy
- [DNS-Viz](https://dnsviz.net/)
- Problems:
    - large keys => TCP => expensive
    - What if I get query that do not exist in Zone? Attackers can send fake or DoS


# Intrusion Detection and Prevention

## Intrusion Phases
1. Scraping, Searching for vulnerabilities
2. Exploits, Social Engineering
    - Update, patch
    - Typicaly can take 1 year to detect
3. Persistence, Establish C&C channel
4. Searching, where else the attacker can get
5. Data exfitration
6. Hide traces

## How to secure network

### Secure each device individually
- Printers and other devices are difficult to secure

### Secure network

#### Firewalls
- Basic level - reads only MAC address (first value in frame)
- Advanced level
    - IP level, needs to decompose
    - TCP level, ideal to check
- **Stateless**: Treat each packet independently
    - uncomplicated, low memory demands
    - complicated implementation (TCP Handshake - you cannot block all SYN packets)
- **Stateful**:
    - most firewalls
    - complicated, needs to be maintained
    - cannot prevent DDoS
    - issues with *packet reordering* and *packet fragmentation*
- UDP
    - UDP is stateless
    - firewall cannot know, packet no., so it must letting packet to go through
- FTP
    - almost impossible to protect with firewall
- 2 firewalls: Split network to *perimeter* and *internal* network
    - devices inside perimeter network cannot connect to internal, but internal can connect back
    - more routers can make mess to firewalls
- When a firewall falls, it should close all connections, not to expose to world
- **Spare phishing** - bypass firewalls
    - attack moves directly inside
- Can have problems understanding protocols (eg. packet fragmentation)
- a service that is placed between places we want to separate and mediate traffic in between
- represent reference monitor
    - unbypassable
    - tamper resistant - ordinary users should not tamper firewall
    - verifiable
- should always be used
- have to be fast, updated

#### Mail Filters
- good against spams, worse against phishing (tailored to a person)
- persons are using private mails in company

#### Intrusion detection systems
- Systems to match patterns, behavior, invalid signatures and notifies the system of intrusion
- has to have low false positive percentage
- general problem:
    - two systems, two security states and we need to know both to decide
- types:
     rule based
     signature based
     behavior based
     anomaly based

#### Intrusion deflection and honeypots
- decreasing server attactivity
    - different server name (not “production”)
- **Honeypots**
    - server that are used to attract attackers
    - can be used to gain behavioral data
    - difficult to make them attractive

#### Intrusion deterrence
- using warning messages etc. (“your actions are being monitored”)


## Intrusion detection systems

Motivation: Detect the attacker as early as possible and cut of its source

- Deployment points: Host, Server, Network, Distributed
- Detection engines:
    - *Signature-based*
        - Problems: encryption changes attacker identity
    - *Vulnerability-based*
        - Individual vulnerabilities can be checked
    - *Behavior-based*
        - Certain patterns of attack behavior cannot be avoided
    - *Anomaly-based*
        - Least effective, doesn't work in practice by FP rate
        - Can be used as first level detector, anomalies need further investigation 
- Sources of data: Syscalls, Logs, Traffic

If the attacker knows about statistical tools, he pretends to be the normal user.
Then, a wider consequences need to be seen.

ML is not used that much because of model unpredictibility.
Adversarial attacks are toy examples.

For attackers, modus operandi (different instruments) is preccious.


# Denial of Service

- Attack on **availability** asset (from *confidentiality*, *integrity*)
- It is old and recent
- Examples:
    - Cutting wires
    - Calling bomb in school
    - Aluminium stripes air strike against radar
    - Hate and Revenge (attack on Krebs, after criminal gang revelation)
    - Destruction - prevent communication to a parts of a system
    - Economy - shut down rival services
    - Fork Bomb

## Advantages
- Easy to launch
- Low resources required
- No skills required
- IoT devices have poor security
- Cost asymmetry

## Denial of service on all levels of OSI model
- physical - cutting wires, wifi radio jamming
- link - MAC flooding
    - also *Man-in-the-middle*
    - flooding a DHCP bogus packet MACs, so the router MAC table is overfilled and then starts to send packets on all ports (it has no target)
- network - ICMP flooding
- segment - SYN flood, Smurf attack
    - *SYN flooding* -> effective
    - *Smurf attack* -> utilizes packet fragmentation
- session - telnet exploit
- presentation - malformed SSL
- application - HTTP attacks

### ICMP Flooding
- Send a large amount of ICMP packets to the victim - victim needs to reply
- **Spoofing Source IP** - steal echo request and send it multicast
    - Performs more computers, than routers (anything with IP)

### UDP
- Easy for IP spoofing
- UDP spoofing can be exploited for target address substitution in DNS answer
- **Amplification**: turn small requests into big answers
    - DNS and NTP have hundreds in amplification multiplier
    - Scanning for victims is fast
    - Honeypots can fingerprint attackers
    
### SYN Flooding
- Receiver must allocate some memory for initialized communication parameters
- Sufficient number of SYN packets eats victims memory enough to denial new TCP connections to establish
- Keeping the connection open as long as possible
    - exhaust server resources as each connection requiring the state to be held
- Can spoof source IP?

### Cross Fire
- Disabling a router by extreme communication between two attacker machines, which the router connects

## Detection
- Easy, but you are the victim when detected
- Singular behavior

## Defences
- **Data quotas**: restricting volume of disk
- Protect against IP spoofing:
    - ISP has to detect spoofed IPs and block them
    - Around 30 % of devices are not filtered
- **SYN cookies** -> allocate memory after handshake
    - After receiving second ACK with verified cookie, memory is allocated
- Protocol Hacks Papers (doesn't matter too much)
- Small players cannot do much...
- *Large Worldwide Companies* (e.g. CloudFlare)
    - They have Tier 1 ISP level
    - They have low latency between within
    - They have large statistics of normal and DoS traffic
    - When some system is attacked, they switch or shuffle the services (due to their size, not all services can be attacked)
- Virtual Servers
- Honeypots
- Captcha

