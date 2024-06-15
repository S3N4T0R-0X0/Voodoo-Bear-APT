# Voodoo Bear APT Adversary Simulation

This is a simulation of attack by (Voodoo Bear) APT group targeting entities in Eastern Europe the attack campaign was active as early as mid-2022,
The attack chain starts with backdoor which is a DLL targets both 32-bit and 64-bit Windows environments, It gathers information and fingerprints the user and the machine then sends the information to the attackers-controlled C2, The backdoor uses a multi-threaded approach, and leverages event objects for data synchronization and signaling across threads. I relied on withsecure tofigure out the details to make this simulation: https://labs.withsecure.com/publications/kapeka

![imageedit_2_8201736021](https://github.com/S3N4T0R-0X0/Voodoo-Bear-APT/assets/121706460/f9ffbe4e-c798-4b8b-86dd-f954d37beb57)

Kapeka, which means “little stork” in Russian,  is a flexible backdoor written in C++. It allows the threat actors to use it as an early stage toolkit, while also providing long term persistence to the victim network. Kapeka’s dropper is a 32-bit Windows executable that drops and launches the backdoor on a victim machine. The dropper also sets up persistence by creating a scheduled task or autorun registry. Finally, the dropper removes itself from the system. 
If you need to know more about Kapeka backdoor for Voodoo Bear APT group: https://blog.polyswarm.io/voodoo-bears-kapeka-backdoor-targets-critical-infrastructure


1. RSA C2-Server: I developed  C2 server script enable to make remote communication by utilizes RSA encryption for secure data transmission between the attacker server and the target.

2. Testing payload : I used payload written by Python only to test C2 (testing payload.py), if there were any problems with the connection (just for test connection) before writing the actual payload.

3. DLL backdoor: I have developed a simulation of the kapeka backdoor that the attackers used in the actual attack.


![Screenshot from 2024-06-11 21-44-39](https://github.com/S3N4T0R-0X0/Voodoo-Bear-APT/assets/121706460/a008a80f-d55c-41a7-b92e-48dd104a4b89)



## The first stage (RSA C2-Server)

This PHP C2 server script enable to make remote communication by utilizes RSA encryption for secure data transmission between the attacker server and the target.

![Screenshot from 2024-06-13 22-11-36](https://github.com/S3N4T0R-0X0/Voodoo-Bear-APT/assets/121706460/f3abb66a-a04e-4238-bc1e-d15626ed0d1a)

`rsa_encrypt($data, $public_key):`

Purpose: Encrypts data using the RSA public key.
Process: The function takes the data and the public key as input, then uses openssl_public_encrypt to encrypt the data with the provided public key.
Output: Returns the encrypted data.

`rsa_decrypt($data, $private_key):`

Purpose: Decrypts data using the RSA private key.
Process: The function takes the encrypted data and the private key as input, then uses openssl_private_decrypt to decrypt the data with the provided private key.
Output: Returns the decrypted data.


![Screenshot from 2024-06-13 22-17-12](https://github.com/S3N4T0R-0X0/Voodoo-Bear-APT/assets/121706460/7d3a2635-dfbd-471c-be2a-f5f7f6e750fe)


## The Second stage (Testing payload)

I used payload written by Python only to test C2 (testing payload.py), if there were any problems with the connection (just for test connection) before writing the actual payload.

![Screenshot from 2024-06-14 17-52-30](https://github.com/S3N4T0R-0X0/Voodoo-Bear-APT/assets/121706460/e93a8ad5-c3b6-4956-814f-018583697283)

RSA and PKCS1_OAEP from pycryptodome: For encryption and decryption using RSA.

rsa_encrypt(data, public_key): Encrypts data using the provided public key.

rsa_decrypt(data, private_key): Decrypts data using the provided private key (not used in this script).


![Screenshot from 2024-06-14 17-45-29](https://github.com/S3N4T0R-0X0/Voodoo-Bear-APT/assets/121706460/8139d107-0766-439e-92c7-7346a0a05a48)

Note: Ensure that the server is correctly sending RSA-encrypted commands and handling the responses appropriately. The script requires the pycryptodome library for RSA encryption and decryption:

    pip install pycryptodome

## The third stage (kapeka backdoor)

The Kapeka backdoor is a Windows DLL containing one function which has been exported by ordinal2 (rather than by name). The backdoor is written in C++ and compiled (linker
14.16) using Visual Studio 2017 (15.9). The backdoor file masquerades as a Microsoft Word Add-In with its extension (.wll), but in reality it is a DLL file.

I have developed a simulation of the kapeka backdoor that the attackers used in the actual attack.



In total, the backdoor launches four main threads:

• First thread: This is the primary thread which performs the initialization and exit routine, as well as C2 polling to receive tasks or an updated C2 configuration.

• Second thread: Monitors for Windows log off events, signaling the primary thread to perform the backdoor’s graceful exit routine upon log off.

![Screenshot from 2024-06-13 17-19-15](https://github.com/S3N4T0R-0X0/Voodoo-Bear-APT/assets/121706460/f0f0868d-ccc7-417f-978e-3dde5beaa662)



• Third thread: Monitors for incoming tasks to be processed. This thread launches subsequent threads to execute each received task.

• Fourth thread: Monitors for completion of tasks to send back the processed task results to the C2.


![Screenshot from 2024-06-13 17-21-20](https://github.com/S3N4T0R-0X0/Voodoo-Bear-APT/assets/121706460/dfc79e98-0363-40e8-aa30-caf00c423bb6)



manual compile:`x86_64-w64-mingw32-g++ -shared -o kapeka_backdoor.dll kapeka_backdoor.cpp -lws2_32`

Run the DLL:`rundll32.exe kapeka_backdoor.dll,ExportedFunction -d`
