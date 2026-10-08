## Introduction  

Online video gaming, particularly competitive gaming and esports, consistently has to confront the issue of cheating. This challenge has intensified with technological advancements in gaming, prompting a response from game developers in the form of sophisticated anti-cheat systems. 

Architecture of a Kernel Anti-Cheat 

Early anti-cheat ran in user space: a process monitored the game process for memory modifications, injected DLLs, or known cheat patterns. This was effective against simplistic cheats but trivially bypassed: any process with sufficient privileges can inject code, manipulate memory, or hide itself from user-space monitoring. 

The fundamental problem with usermode-only anti-cheat is the trust model. Any protection implemented entirely in usermode can be bypassed by anything running at a higher privilege level. 

 

The main difference between the different levels of privilege is the accessibility of memory and instructions. User mode (ring 3) applications are isolated from kernel mode (ring 0) applications, because kernel-mode determines how user-mode behaves, and usermode-mode applications therefore cannot access kernel memory. 
 

Why kernel mode? 

Kernel-mode cheats could directly manipulate game memory without going through any API that a usermode anti-cheat could intercept. 

 

 

Kernel anti-cheat deploys as a Windows kernel driver (.sys file). the driver is loaded at boot (for persistent systems like Vanguard) or at game launch (for on-demand systems). 

Boot-time vs Runtime Driver Loading 

 

The distinction between boot-time and runtime driver loading is more significant than it might appear. 

BattlEye and EAC load their kernel drivers when the game is launched. They are registered as demand-start drivers and loaded via a driver loader from the service when the game starts. They are unloaded when the game exits. 

  

Vanguard loads vgk.sys at system boot. The driver is configured as a boot-start driver (SERVICE_BOOT_START in the registry), meaning the Windows kernel loads it before most of the system has initialized. This gives Vanguard a critical advantage: it can observe every driver that loads after it. Any driver that loads after vgk.sys can be inspected before its code runs in a meaningful way. A cheat driver that loads at the normal driver initialization phase is loading into a system that Vanguard already has eyes on. 

  

The practical implication of boot-time loading is also why Vanguard requires a system reboot to enable: the driver must be in place before the rest of the system initializes, which means it cannot be loaded after the fact without a restart. 

 

Referencias 

s4dbrd - How-kernel-anti-cheats-work 

Why anti-cheat software utilize kernel drivers 

Example 

Hacking Forum - Kernel anticheat analysis 

Wikipedia - Protection rings 

What is a kernel driver 

 

https://s4dbrd.github.io/posts/how-kernel-anti-cheats-work/#8-anti-debug-protections 
 

The three types of detections  

 
Kernel driver: Runs at ring 0. Registers callbacks, intercepts system calls, scans memory, enforces protections. This is the component that actually has the power to do anything meaningful. 

  

Usermode service: Runs as a Windows service, typically with SYSTEM privileges. Communicates with the kernel driver via IOCTLs. Handles network communication with backend servers, manages ban enforcement, collects and transmits telemetry. 

  

Game-injected DLL: Injected into (or loaded by) the game process. Performs usermode-side checks, communicates with the service, and serves as the endpoint for protections applied to the game process specifically. 
 
This 3 things are needed 

Vanguards kernel driver uses a whitelist of future dll loading 

 

When do they load 

BattlEye and EAC Load when the game is launched 

Vanguard is loaded before most of the system has initialized (boot-start driver) 

 

Their detections 

Memory Protection and Scanning 

Anti-Injection Detection 

Hook Detection 

Driver-Level Protections 

Anti-Debug Protections 

DMA Cheats and Detection 

Behavioral Detection and Telemetry (mouse, ML, IA) 

Anti-VM and Environment Checks 

Hardware Fingerprinting and Ban Enforcement 
 

The constant battle between cheats and anticheats 

The escalation we have seen over the past decade follows a clear pattern: 

    Usermode cheats were countered by usermode anti-cheat. 

    Kernel cheats were countered by kernel anti-cheat. 

    Kernel cheats with BYOVD were countered by driver blocklists and stricter DSE enforcement. 

    Hypervisor-based cheats were countered by hypervisor detection. 

    DMA cheats are the current frontier, partially countered by IOMMU, Secure Boot, and TPM attestation. 

    The next level is firmware-based attacks, where the cheat is embedded in the SSD firmware, GPU firmware, or NIC firmware. 
 
 
 
https://x.com/Perpetualmaniac/status/1814376668095754753 

 

Sumario ejecutivo: 

Problema: Los Cheats en videojuegos cada vez se usan más y supone un problema economico y de experiencia de usuario para las empresas y distruidoras de estos. 

A lo largo de los años, se han ido identificando diferentes patrones en los Cheats: 
     

Cheats que se ejecutan a nivel de  Usermode   

Cheats que se ejecutan a nivel de kernel  

Kernel cheats con BYOVD (con un driver vulnerable modificado) 

Cheats basados en Hypervisor. 

Cheats que utilizan DMA (Acceso directo a la memoria) 

    The next level is firmware-based attacks, where the cheat is embedded in the SSD firmware, GPU firmware, or NIC firmware. 

 

Solución: 

Crear un AntiCheat a nivel de kernel 

Fucionamiento: 

Después de que el videojuego se instale junto al anticheat que usa, creará un proceso del sistema (en el caso de riot vanguard es vgk.sys).  este se ejecuta en el anillo 0 del kernel, que es el más profundo del sistema, para que cargue antes de que puedas cargar los cheats. 

 

Luego el programa ejecutable (vgc.exe para Vanguard y Valorant) comprueba si el anticheat está en funcionamiento, haciendo un puente entre el proceso dentro del kernel y el cliente del juego. 

Una vez se arranca el ordenador, ya está activado, y una vez inicias el juego este comprobará si el anti cheat se puede encender, si es que este no está encendido ya. 

Cuando este ya el cliente en funcionamiento, el anti cheat dentro del kernel analizará, bloqueará o interceptará cargas de datos maliciosas, no firmadas o loopholes (vacíos legales) o inyecciones de código.  

Ni en el anillo 3 ni en el anillo 0 se podrá escribir memoria dentro del juego 

El anticheat hará test especiales para herramientas analíticas, y después de detectar comportamientos sospechosos, baneará la máquina mediante HWID (hardware ID) 

Valor: 

 

Conclusion: 

 

 

https://gist.github.com/gmh5225/2b430b6025c8888196dd95c8557bfc6f 

 

	 