## From Physical to Cyber-Keys
At the beginning, there were ignition systems and, in order to start the car, you would need to switch the key to send a signal, that would start the ignition system and ignites the fuel vapor. This mechanism was extremely insecure since you can generate this kind of signal in many ways if you have physical access to the car.

To overcome this problem, the first solution adopted involving a cyber-physical component was the **immobilizer**. The purpose of this component, which used a small chip embedded into the head of the car key, was to send a serial number that could be received by the antenna inside the cylinder once the driver inserted the key into the cylinder. If the code emitted by the key was the same as the one expected by the car, the ignition would start.

As a first solution, it was OK, but since the immobilizer transmitted always the same code, the attacker could brute force it by keeping trying different codes until it found the right one. 

### Digital Signature Transponder
Due to this problems, the immobilizer were quickly upgraded into a more secure system, introducing the **Digital Signature Transponder (DST)**. This is device is small *radio frequency identification (RFID)* chip that is enabled with a cypher F() and a 40-bit secret key. Both the car and the DST share a copy of the private key K.

> [!INFO] Digital Signature Transponder and Car Communication
> ![[KeylessAccess_DST.png]]

In this scenario, an attacker cannot replay the response since the car should be sending a different challenge every round. Still, there is a problem related to the key length, which allows the attacker to try all possible combinations to infer the private keys.
#### DST+
To solve the cloning attack, the DST was eventually upgraded once again introducing the *Mutual Authentication Key (MAK)*. As shown in the picture below, the encryption is more difficult than before and if the challenge is not the one expected, the chip doesn't respond.

> [!INFO] Digital Signature Transponder+
> ![[KeylessAccess_DST+.png]]

To add every more complexity and security, the *Passive Keyless Entry System* was introduced in which there was no interaction needed with the user and the car, in addition to checking whether the code was legitimate and correct, had to check where the key was: by using a low-frequency RFID channel, it checked if key was in remote distance ($\approx 100$m), outside of the car (1 or 2m from the handle) or inside the car. Only in the last case the engine would start.

## Relaying Signals
So the problem with the DST+(and i would add another +) was that is very difficult to identify the shared key or to win the challenge but there was a key factor that should come up to mind: **the communication relies on signals!** 
This is magnificent because attackers don't even need to know what is the challenge or what the car and the immobilizer are saying, they can just propagate the signal. 
This is the basis to define **relay attacks** which, just like man-in-the-middle attacks, where a non legitimate device establishes a communication between two non-proximal legitimate devices.
In this specific type of attack, we only care about the physical layer since we do not need to interpret the signal, modify it, infer keys, and so on but just need to demodulate the signal, amplify it if needed, transmit it as digital information using RF, and modulate it near the victim
### Relay over Cable
In this attack specialisation, two loop antennas are connected via a cable that relays the low frequency signal between them.

![[Screenshot 2026-10-09 at 09.12.14.png]]

### Relay over the Air
Since cables can be suspicious during an attack, cause as attackers we want to be discrete as possible :), we remove them from the equation. From here, we use an *emitter* and a *receiver* where the former captures the low frequency signal and up-converts it to a 2.5 GHz, amplifies it and then transmits it over the air and the latter down-coverts the signal back to the low frequency, amplifies it again and sends it to the loop antenna.

>[!INFO] Relay over the Air
>![[KeylessAccess_Relay_Air.png]]

This attack can also be called **mafia fraud attack** (inspired by the **Chess Grandmaster Problem**) where the attacker sets up 2 devices: one near the verifier and another near the prover. Both of them are honest and they do not expect (or realise) the presence of the attacker. 

## Distance Bounding Protocols
Since space is a factor that can be faked, we could consider timing instead.
**Distance bounding** denotes a class of protocols where the prover measures an upper bound on its distance to another entity; by implementing radio-frequency distance bounding to verify the mutual proximity of the car and the key and the distance bound is obtained by a rapid exchange of messages. The verifier sends a challenge to the prover and, upon receiving a response, the verifier measures the communication **Round Trip Time (RTT)** to obtain an estimate of the distance. In a secure bounding protocol, given $d$ the prover-verifier distance and $T$ the RTT, then $d \leq 0.5 T c$, where $c$ is the speed of light.

### Brands and Chaum Protocol
Consider a Prover $P$ and a verifier $V$ where $V$ wants to verify whether $P$ is close or not. Both parties can generate random numbers and execute cryptographic operations. Let's assume that they can use a public key cryptographic scheme.
- Step 1: $V$ generates $k$ random uniform bits $a_i$ and $P$ generate $k$ random uniform bits $b_i$;
- Step 2: using a low-level distance bounding, repeat $k$ times :
	- $V$ sends bit $a_i$ to $P$;
	- $P$ sends bit $b_i$ to $V$ immediately after receiving $a_i$ and $V$ measures the RTT;
- Step 3: $P$ concatenates the $2k$ bits $a_i$ and $b_i$, signs the resulting message $m$ with its secret key and sends the signature to $V$;
- $V$ accepts only if the RTT is bounded and the received signal is valid on $m = a_1\ |\ b_1\ | ...\ |\ a_k\ |\ b_k$ 
#### BC Protocol V2.0
Let's now add some more security:
- $P$ randomly generates a bit string $m_1, \ ..., \ m_k$ and sends a commit of this value to $V$. The commitment depends on the used algorithm (if we used Pedersen $\rightarrow C(m,\ r) = g^mh^r$, where $g,\ h$ are generators of the public group $G$). Although the bit string is not revealed, $V$ will be able to check whether $P$ really committed to that;
- $V$ generates $a_1,\ ...,\ a_k$ initiating the rapid bit exchange sequence;
- $P$ then needs to respond as quick as possible with $a_i \oplus b_i$;
- From the response times, $V$ will derive a distance upper bound $D = max_i\dfrac{(T_i)c}{2}$ 
This mechanism highlights some key aspects: if used against Mafia Fraud Attacks, the adversary should create a time-gaining relay, by delivering in advance the challenge or response bits but, since bits are unguessable and they cannot travel faster than the speed of light, the probability of guessing a bit is 50% for a bit (but keep in mind that we use $k$ bits) and, in a "standard" security protocol $k = 128$, the probability of success drops to $3\times10^{-39}$.