---
layout: post
title: NSA Backdoor
subtitle: Crypto
thumbnail-img: /assets/img/pico.jpeg
share-img: /assets/img/pico.jpeg
tags: [CTFs,picoCtf,crypto]
---


NSA Backdoor


In this challenge we are given the public key (n,e=3), the ciphertext and the code used to generate it, there's also a hint in which they mentioned Mr. Wong's whitepaper (https://eprint.iacr.org/2016/644.pdf). by reading the paper and doing some research, the solution is pretty clear:


1.Find the primes:

since p and q are smooth numbers we can use pollard p-1 to get one of them (I used the primefac library for this purpose):


`
import gmpy2

import primefac

n = "8d9424ddbf9fff7636d98abc25af7fde87e719dc3ceee86ca441b079e167cc22ff283f1a8671263c2e5ebd383ca3255e903b37ebca9961fd8a657cb987ef1e709866acc457995bfc7a6d4be7e88b9ee03a9872329e05cb7eb849d61e4bb44a25be8bd42f19f13a9417bfab73ba616b7c05865640682dc685890bbce8c20c65175f322b5b27788fede4f6704c6cb7b2d2d9439fad50f8b79ffab0b790591ae7f43bd0316565b097b9361d3beb88b6ef569d05af75d655b5133dc59a24c86d147a5eb5311344a66791f03a3da797effd600aa61564ce4ffd81f70bfedf12ca7857b9ac781a4823f6c1a08f1e86f8fe0e1eb3eb6ac71b63e4b03ba841c8588f6df1" 

c = "4679331be9883c4518d4870352281710777bcd74e6c9e9abb886254cf42c2f7adf5b58af8c8c00a51a72ee1ffaa8af3e9877a11d8ee8702446f1814a0255013a1e1b50a1c795218130a0dade9a5eb6b2c74a726c689ea9a5fe8391d7963d0a648c7ed79f3571d28252fd109f071a3f4ed6cb1de203c24e1cb5517983a8946a4b69cb39844c9f1c6975ad3f9ff7075b1c3a28a8eb25e28d7ecab781686412ca81f0c646094782c8cbacce9a58609c8041b82f9052ff0afd7c9953fa191ed548cf756e7f713341b434b6cc84ac62ff14740c213c60985fc71a6d23ffec7c2e145af0a4217af5f3263083030bc803c0e591a18760c4ea957f72017dcebe7b130e08"

q = primefac.pollard_pm1(int(n,16))

2.Getting the flag:

If you read the code carefully you can see that this is not a classic RSA challenge since the plaintext is used as an exponent, so we're dealing with a discrete logarithm problem. The section 4 of the paper provided (Mr. Wong's paper) describes clearly the attack we're going to use and luckily sagemath function discrete_log() uses pohlig hellman by default. 3. the final exploit:

from sage import *

n = "8d9424ddbf9fff7636d98abc25af7fde87e719dc3ceee86ca441b079e167cc22ff283f1a8671263c2e5ebd383ca3255e903b37ebca9961fd8a657cb987ef1e709866acc457995bfc7a6d4be7e88b9ee03a9872329e05cb7eb849d61e4bb44a25be8bd42f19f13a9417bfab73ba616b7c05865640682dc685890bbce8c20c65175f322b5b27788fede4f6704c6cb7b2d2d9439fad50f8b79ffab0b790591ae7f43bd0316565b097b9361d3beb88b6ef569d05af75d655b5133dc59a24c86d147a5eb5311344a66791f03a3da797effd600aa61564ce4ffd81f70bfedf12ca7857b9ac781a4823f6c1a08f1e86f8fe0e1eb3eb6ac71b63e4b03ba841c8588f6df1"


c = "4679331be9883c4518d4870352281710777bcd74e6c9e9abb886254cf42c2f7adf5b58af8c8c00a51a72ee1ffaa8af3e9877a11d8ee8702446f1814a0255013a1e1b50a1c795218130a0dade9a5eb6b2c74a726c689ea9a5fe8391d7963d0a648c7ed79f3571d28252fd109f071a3f4ed6cb1de203c24e1cb5517983a8946a4b69cb39844c9f1c6975ad3f9ff7075b1c3a28a8eb25e28d7ecab781686412ca81f0c646094782c8cbacce9a58609c8041b82f9052ff0afd7c9953fa191ed548cf756e7f713341b434b6cc84ac62ff14740c213c60985fc71a6d23ffec7c2e145af0a4217af5f3263083030bc803c0e591a18760c4ea957f72017dcebe7b130e08"

n = int(n,16) 

q = primefac.pollard_pm1(n) 


#q = 120759530765440164788393584815198181785453365216058011221242572474857701934543658420625103273619411412685939059091676362260202826471982447213813057019415223578402663988113530890287786672842686395844059610821738383797601768012556360596829980884906822859047976972535357205380764775443960532258052851032850088083


p = n//q


g = Mod(3,p) 

m = discrete_log(int(c,16),g) 


print(long_to_bytes(m))
`
