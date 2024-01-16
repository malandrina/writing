# Cryptopals for the impatient

_16 January 2024_

[Cryptopals](https://cryptopals.com) is a free problem set comprising 66 challenges that "demonstrates attacks on real-world crypto" and in the process teaches you encryption and decryption techniques. I am an experienced programmer with minimal prior knowledge of cryptography who just wants to 1) solve the problems already and 2) learn the Rust programming language and cryptography along the way.

The former desire keeps me going and the latter keeps me interested. After completing 17 of the challenges, here's how I'm approaching the remaining 49:

## Do
- Be clear about your learning goals for yourself. Do you want to gain a deep knowledge of the history of the AES standard? Learn how to implement hex and base64 encoding/decoding? If not, don't go down those rabbit holes.
- Read the challenge description very carefully, multiple times.
- Pay attention to technical terms you don't understand and look them up instead of guessing at their meaning. A good example: "oracle".
- Read a description of the encryption scheme and/or attack you are being asked to implement in a credible source. Wikipedia counts (most of the time, probably).
- Make decisions about how to separate concerns in your code. How will you organize and encapsulate setup, attack (when applicable), and verification?
- Establish a quick feedback loop. One way to achieve this is by executing the code that implements your solution within the context of an automated test.
- Double-check your assumptions about which buffers are being mutated in memory.
- Substitute your own inputs for the challenge-supplied inputs to reduce complexity and help you build your intuition. Then make your solution work with the Cryptopals inputs.
- Be persistent.

## Don't
- Feel like you're doing it wrong if you seek out and rely on resources other than the challenge description.
- Copy and paste others' code.
- Move on until you feel you have internalized the lesson of the challenge.
- Become obsessed.
